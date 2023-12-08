# Práctica 3 : Protocolos Seguros y Auditorías de seguridad

## 1. Tomando como base de trabajo el SSH pruebe sus diversas utilidades:

***a. Abra un shell remoto sobre SSH y analice el proceso que se realiza. Configure su fichero ssh_known_hosts para dar soporte a la clave pública del servidor***

1. Creamos el archivo ssh_known_hosts y metemos en él el output del ssh-keyscan a la IP de nuestro compañero:

```
touch /etc/ssh/ssh_known_hosts
```

```
ssh-keyscan 10.11.48.203 >> /etc/ssh/ssh_known_hosts
```

2. Vaciamos el archivo known_hosts de nuestro .ssh y probamos a hacer ssh:

```
echo "" > /home/lsi/.ssh/known_hosts
```
```
ssh lsi@10.11.48.203
```
Vemos que no nos pone el mensaje de add new fingerprint, por lo que está bien configurado.


***b. Haga una copia remota de un fichero utilizando un algoritmo de cifrado determinado. Analice el proceso que se realiza***


Creamos un archivo de texto que utilizaremos para la copia remota:

```
nano archivo_a_cifrar
```

A continuacion vamos a cifrar ese archivo:

```
openssl aes-256-cbc -e -in archivo_a_cifrart -out archivo_cifrado
```

Podemos hacer un cat de archivo_cifrado para ver que efectivamente esta cifrado:

```
cat archivo_cifrado
```

Le pasamos el archivo al compañero mediante una comunicacion cifrada(en este caso cifrados con aes128-ctr)

```
scp -c aes128-ctr archivo_cifrado lsi@10.11.48.203:/home/lsi/archivo_cifrado_destino
```

El compañero tiene que comprobar que efectivamente el archivo le ha llegado:

```
cat /home/lsi/archivo_cifrado_destino
```

Desciframos el archivo cifrado que nos ha enviado el compañero ( nos pedira la passwd que puismos al cifrar, en nuestro caso 1234):

```
openssl aes-256-cbc -d -in archivo_cifrado_destino -out archivo_descifrado
```

Comprobamos que efectivamente el archibo que hemos recibido es igual al que enviamos desde la maquina origen una vez descifrado:

```
cat /home/lsi/archivo_descifrado
```


***c. Configure su cliente y servidor para permitir conexiones basadas en un esquema de autenticación de usuario de clave pública***

Genero las claves desde mi máquina con el usuario LSI:

```
ssh-keygen -t rsa
```

```
ssh-keygen -t ed25519
```

```
ssh-keygen -t ecdsa
```

Mi compañero pilla mis claves desde root@debian:/home/lsi/.ssh : ( como root)

```
cd /home/lsi/.ssh
```

```
scp lsi@10.11.48.207:/home/lsi/.ssh/*.pub ../keys
```

```
touch authorized_keys
```

Metemos las claves en authorized_keys:

```
cat ../keys/id_rsa.pub >> authorized_keys
```

```
cat ../keys/id_ed25519.pub >> authorized_keys
```

```
cat ../keys/id_ecdsa.pub >> authorized_keys
```

Comprobamos que no nos pide password conectandonos por ssh a la maquina del compañero desde el usuario LSI:

```
ssh lsi@10.11.48.203
```


***d. Mediante túneles SSH securice algún servicio no seguro***

Creamos el tunel SSH para securizar apache ( puerto 80)
```
ssh -P -L 10.11.48.207:10080:10.11.48.203:80 lsi@10.11.48.203
```

Para probarlo realizamos una peticion a traves de ese puerto ( Alex hace una peticion contra mi apache)
```
w3m http://10.11.48.207:10080/
```

Si hacemos exit es decir cerramos la conexion que abrimos para el túnel y volvemos a hacer la peticion sobre el puerto 10080 no nos dara respuesta


Nota: Tenemos que tener configurado en el firewall ese puerto, sino cuando establezcamos las reglas no nos dara respuesta el apache con el puerto 10080

***e. “Exporte” un directorio y “móntelo” de forma remota sobre un túnel SSH***


Creamos un directorio en nuestra maquina donde montaremos el remoto:
```
mkdir testsshfs_remota_alex
```

Exportaremos el directorio de alex /home/lsi/Escritorio/sshfs2 en el directorio que hemos creado:

```
sshfs lsi@10.11.48.203:/home/lsi/Escritorio/sshfs2 /home/lsi/Escritorio/testsshfs_remota_alex/
```

Para desmontar el directorio que habiamos montado:

```
fusermount -u /home/lsi/Escritorio/testsshfs_remota_alex/
```

***f. PARA PLANTEAR DE FORMA TEÓRICA.: Securice su sevidor considerando que únicamente dará servicio ssh para sesiones de usuario desde determinadas IPs.***


## 2. Tomando como base de trabajo el servidor Apache2


***a. Configure una Autoridad Certificadora en su equipo.***

```
cd /usr/lib/ssl/misc
```

```
./CA.pl -newca
```

***b. Cree su propio certificado para ser firmado por la Autoridad Certificadora. Bueno, y fírmelo.***

Generamos el certificado:
```
./CA.pl -newreq-nodes
```

Firmamos el certificado:

```
./CA.pl -sign
```

***c. Configure su Apache para que únicamente proporcione acceso a un determinado directorio del árbol web bajo la condición del uso de SSL. Considere que si su la clave privada está cifrada en el proceso de arranque su máquina le solicitará la correspondiente frase de paso, pudiendo dejarla inalcanzable para su sesión ssh de trabajo.***


Habilitamos SSL:
```
cd /etc/apache2
```

```
a2enmod ssl
```

```
 systemctl restart apache2
```

```
cp newcert.pem /etc/ssl/certs/cert.crt
```

```
cp newkey.pem /etc/ssl/private/keysandro.key

```

Nos colocamos en el directorio /usr/lib/ssl/misc/demoCA y ejecutamos:

```
cp cacert.pem /usr/local/share/ca-certificates/cacert.crt
```
Hacemos update de los certificados:

```
update-ca-certificates
```

```
cp -p /usr/lib/ssl/misc/demoCA/cacert.pem /etc/ssl/certs/cacert.pem
```

Obtenemos el hash (lo anotamos para despues comprobar el certificado) (d939aaf8)

```
openssl x509 -in cacert.pem -noout -hash
```

Creamos un enlace simbolico a ese hash:
```
ln -s cacert.pem d939aaf8.0
```
Verifacmos si esta correcto ( si es asi nos devolvera OK):
```
openssl verify cacert.pem
```

Una vez en este punto meteremos el certificado en nuestro servidor apache:

Abrimos el ardchivo de configuracion de SSL:
```
nano /etc/apache2/sites-available/default-ssl.conf
```
y lo configuramos de la siguiente manera , indicandole las rutas donde se encuentra el certificado firmado, la CPriv del certificado y la CPriv de la CA, y la ruta del directorio que se abrira cuando la conexion sea segura a traves del puerto https (443):

```
<VirtualHost *:443>

     SSLEngine On
     SSLCertificateFile /etc/ssl/certs/cert.crt
     SSLCertificateKeyFile /etc/ssl/private/key.key
     SSLCACertificateFile /etc/ssl/certs/cacert.pem

     ServerAdmin webmaster@localhost
     DocumentRoot /var/www/html/prueba
     
     ErrorLog ${APACHE_LOG_DIR}/error.log
     CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>

```

Luego me cree un directorio prueba en /var/www/


## 3. Tomando como base de trabajo el openVPN deberá configurar una VPN entre dos equipos virtuales del laboratorio que garanticen la confidencialidad entre sus comunicaciones.


Alex hace de servidor, la clave la genera el y me la manda (clave.key) 

Esa clave yo la muevo a /etc/openvpn

Creamos los dos el archivo tunel.conf, la diferencia de el de alex al mio es simplemente la ip local/remote que estan intercambiadas y en el ifconfig que estan tambien intercambiadas. Importante meter el cipher AES-256-CBC

Para levantar el túnel:

Si al ejecutar el openvpn aparece un error que ponga "IOCTL busy resource" o algo parecido, significa que la vpn ya está levantada (vamos, que ya está funcionando)

```
openvpn --verb 5 --config/etc/openvpn/tunel.conf
```


Para comprobar el tunel hacer ping al otro extremo en mi caso a la ip de alex:

```
 ping 172.160.0.1
```

Si paramos el VPV dejara de ser efectivo el tuneo y no hara el ping:

```
systemctl stop openvpn
```

## 6. En este punto, cada máquina virtual será servidor y cliente de diversos servicios (NTP, syslog, ssh, web, etc.). Configure un “firewall stateful” de máquina adecuado a la situación actual de su máquina.


## 7. Ejecute la utilidad de auditoría de seguridad lynis en su sistema y trate de identificar las acciones de securización detectadas así como los consejos sobre las que se deberían contemplar.

```
 apt-get install lynis
```

```
lynis audit system
```



