# Práctica 2: Categorías de ataques


**a) Instale el ettercap y pruebe sus opciones básicas en línea de comando**

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

**b) Capture paquetería variada de su compañero de prácticas que incluya varias sesiones HTTP. Sobre esta paquetería (puede utilizar el wireshark para los siguientes subapartados)**

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




