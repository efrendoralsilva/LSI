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


APARTADO 1B FUNCIONANDO PERFECTAMENTE:Haga una copia remota de un fichero utilizando un algoritmo de cifrado determinado.
Analice el proceso que se realiza.

Ciframos el archivo que queremos enviar (nos pedira una passwd, ponemos la que queramos, en este caso ciframos con aes-256-cbs):

--> openssl aes-256-cbc -e -in archivo_que_queremos_cifrar.txt -out archivo_cifrado.txt

Le pasamos el archivo al compañero mediante una comunicacion cifrada(en este caso cifrados con aes128-ctr)

--> scp -c aes128-ctr archivo_cifrado.txt lsi@10.11.48.203:/home/lsi/archivo_cifrado_destino.txt

Hacemos un cat desde el compañero para ver que efectivamente esta cifrado:

--> cat /home/lsi/archivo_cifrado_destino

Desciframos el archivo ( nos pedira la passwd que metimos al cifrar)

--> openssl aes-256-cbc -d -in archivo_cifrado_destino.txt -out archivo_descifrado.txt

Hacemos cat y vemos que realmente tiene el mensaje que pusimos en la maquina origen:

--> cat /home/lsi/archivo_descifrado




***c. Configure su cliente y servidor para permitir conexiones basadas en un esquema de autenticación de usuario de clave pública***

Como usuario lsi:

```
ssh-keygen -t rsa
```

```
ssh-keygen -t ed25519
```

```
ssh-keygen -t ecdsa
```

Compañerp desde ~/.ssh (Alex)

```
cd /home/lsi/.ssh
```

```
scp lsi@10.11.48.203:/home/lsi/.ssh/*.pub ../keys
```

```
touch authorized_keys
```

```
cat ../keys/id_rsa.pub >> authorized_keys
```

```
cat ../keys/id_ed25519.pub >> authorized_keys
```

```
cat ../keys/id_ecdsa.pub >> authorized_keys
```


***d. Mediante túneles SSH securice algún servicio no seguro***

***e. “Exporte” un directorio y “móntelo” de forma remota sobre un túnel SSH***

***f. PARA PLANTEAR DE FORMA TEÓRICA.: Securice su sevidor considerando que únicamente dará servicio ssh para sesiones de usuario desde determinadas IPs.***


## 2. Tomando como base de trabajo el servidor Apache2

***a. Configure una Autoridad Certificadora en su equipo.***

***b. Cree su propio certificado para ser firmado por la Autoridad Certificadora. Bueno, y fírmelo.***

***c. Configure su Apache para que únicamente proporcione acceso a un determinado directorio del árbol web bajo la condición del uso de SSL. Considere que si su la clave privada está cifrada en el proceso de arranque su máquina le solicitará la correspondiente frase de paso, pudiendo dejarla inalcanzable para su sesión ssh de trabajo.***


## 3. Tomando como base de trabajo el openVPN deberá configurar una VPN entre dos equipos virtuales del laboratorio que garanticen la confidencialidad entre sus comunicaciones.


## 6. En este punto, cada máquina virtual será servidor y cliente de diversos servicios (NTP, syslog, ssh, web, etc.). Configure un “firewall stateful” de máquina adecuado a la situación actual de su máquina.


## 7. Ejecute la utilidad de auditoría de seguridad lynis en su sistema y trate de identificar las acciones de securización detectadas así como los consejos sobre las que se deberían contemplar.
