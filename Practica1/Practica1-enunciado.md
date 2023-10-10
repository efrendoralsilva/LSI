# Práctica 1 : Configuración básica

En este archivo se muestra como solucionar cada apartado de la práctica 1 de la asignatura de Legsilación y Seguridad Informática de la facultad de informática de la universsidad de la Coruña (UDC).

### Apartado A

Configure su máquina virtual de laboratorio con los datos proporcionados por el profesor.
Analice los ficheros básicos de configuración (interfaces, hosts, resolv.conf,
nsswitch.conf, sources.list, etc.)

La configuración de la máquina virtual dependerá de las IPs que sean proporcionadas por el profesor para nosotros y para nuestro compañero. Para configurar la mñaquina tenemos que editar el fichero `/etc/network/interfaces:`

El siguiente ejemplo se correspone con las IPs 10.11.48.207 y 10.11.50.207
```
# This file describes the network interfaces available on your system
# and how to activate them. For more information, see interfaces(5).
# source /etc/network/interfaces.d/*
# The loopback network interface
auto lo ens33 ens34
iface lo inet loopback

iface ens33 inet static
	address 10.11.48.207
	netmask 255.255.254.0
	broadcast 10.11.49.255
	network 10.11.48.0
	gateway 10.11.48.1

iface ens34 inet static
	address 10.11.50.207
	netmask 255.255.254.0
	broadcast 10.11.51.255
	network 10.11.50.0
```

`/etc/hosts`:

En sistemas Debian (y en sistemas basados en Debian, como Ubuntu), el archivo /etc/hosts es un archivo de configuración que se utiliza para mapear nombres de host a direcciones IP antes de consultar servidores DNS. Proporciona una forma de resolver nombres de host localmente en la máquina, sin necesidad de hacer consultas a servidores DNS externos.

```
127.0.0.1	localhost
10.11.48.50	debian

# The following lines are desirable for IPv6 capable hosts
::1     localhost ip6-localhost ip6-loopback
ff02::1 ip6-allnodes
ff02::2 ip6-allrouters
```

`/etc/resolv.conf`:

El archivo /etc/resolv.conf en sistemas Debian (y sistemas basados en Debian, como Ubuntu) es otro archivo de configuración importante. Contiene información sobre la configuración de los servidores DNS que el sistema utilizará para resolver nombres de dominio en direcciones IP. Este archivo determina cómo se resuelven las consultas DNS en tu sistema.
Además de nameserver, puede incluir otras directivas para configurar opciones adicionales, como búsqueda de dominio, dominio predeterminado, etc

```
domain udc.pri
search udc.pri
nameserver 10.8.12.49
nameserver 10.8.12.50
nameserver 10.8.12.47
```

`/etc/nsswitch.conf`:

El archivo /etc/nsswitch.conf en sistemas Debian (y en sistemas basados en Debian, como Ubuntu) es un archivo de configuración que define el orden y las fuentes de búsqueda que se utilizan para resolver diferentes tipos de consultas de nombres en el sistema. NSS significa "Name Service Switch" (Cambio de Servicio de Nombres), y este archivo determina cómo se busca y resuelve la información de nombres como usuarios, grupos, hosts y otros datos en el sistema.

```
# /etc/nsswitch.conf
#
# Example configuration of GNU Name Service Switch functionality.
# If you have the `glibc-doc-reference' and `info' packages installed, try:
# `info libc "Name Service Switch"' for information about this file.

passwd:         files systemd
group:          files systemd
shadow:         files
gshadow:        files

hosts:          files mdns4_minimal [NOTFOUND=return] dns myhostname
networks:       files

protocols:      db files
services:       db files
ethers:         db files
rpc:            db files

netgroup:       nis
```

`/etc/apt/sources.list`:

Este archivo en sistemas Debian y sistemas basados en Debian (como Ubuntu) es un archivo de configuración clave para el sistema de administración de paquetes APT (Advanced Package Tool). A través de este archivo, se especifican los repositorios desde los cuales el sistema puede obtener paquetes y actualizaciones de software.
Cada línea en el archivo sources.list corresponde a un repositorio y proporciona la ubicación de los archivos de índice de paquetes (archivos .deb) para los diferentes componentes del sistema, como "main", "contrib", "non-free", etc.

```
deb http://deb.debian.org/debian/ buster main
deb-src http://deb.debian.org/debian/ buster main

deb https://deb.debian.org/debian-security buster-security main contrib
deb-src https://deb.debian.org/debian-security buster-security main contrib

deb http://security.debian.org/debian-security buster/updates main
deb-src http://security.debian.org/debian-security buster/updates main
```








## b) ¿Qué distro y versión tiene la máquina inicialmente entregada?. Actualice su máquina a la última versión estable disponible.

Paso 1: Verificar la versión actual de Debian:
Para saber la versión actual de Debian en tu máquina, puedes ejecutar el siguiente comando en la terminal:
```
cat /etc/os-release
```
Paso 2: Actualizar la lista de paquetes:
Antes de actualizar el sistema, es importante actualizar la lista de paquetes para asegurarte de que obtendrás la última versión de los paquetes disponibles. Ejecuta el siguiente comando:
```
sudo apt update
```
Paso 3: Realizar la actualización del sistema:
Una vez que la lista de paquetes esté actualizada, puedes proceder a actualizar el sistema. Utiliza el siguiente comando:
```
sudo apt upgrade
```
Este comando actualizará todos los paquetes instalados a sus últimas versiones disponibles

Paso 4: Actualizar a la última versión estable de Debian:
Para actualizar Debian 11 a la última versión estable disponible, primero debes modificar el archivo /etc/apt/sources.list para apuntar a la nueva versión. Esto se hace reemplazando las referencias a "bullseye" (que es la versión de Debian 11) por "bookworm" (que podría ser la próxima versión en el momento de tu actualización). Ten en cuenta que los nombres pueden cambiar en el futuro, así que asegúrate de verificar la última versión estable de Debian antes de continuar.
Abre el archivo /etc/apt/sources.list con un editor de texto como nano o vim:
```
sudo nano /etc/apt/sources.list
```
Dentro del archivo, busca las líneas que contienen "bullseye" y reemplázalas por "bookworm". Debería verse algo como esto:
```
deb http://deb.debian.org/debian/ bullseye main
deb-src http://deb.debian.org/debian/ bullseye main
```
Cámbialas a:
```
deb http://deb.debian.org/debian/ bookworm main
deb-src http://deb.debian.org/debian/ bookworm main
```
Una vez modificado este archivo realizamos la actualizacion de paquetes y de la maquina:
```
sudo apt update
```
```
sudo apt dist-upgrade
```
Una vez que se complete el proceso de actualización, reinicia la máquina para aplicar los cambios:
```
sudo reboot
```


## c) Identifique la secuencia completa de arranque de una máquina basada en la distribución de referencia (desde la pulsación del botón de arranque hasta la pantalla de login). ¿Qué target por defecto tiene su máquina?. ¿Cómo podría cambiar el target de arranque?. ¿Qué targets tiene su sistema y en qué estado se encuentran?. ¿Y los services?. Obtenga la relación de servicios de su sistema y su estado. ¿Qué otro tipo de unidades existen?.
Paso 1: Encender la Máquina:

Presiona el botón de encendido o la secuencia correspondiente para iniciar la máquina.

Paso 2: BIOS/UEFI:

La máquina realiza una verificación inicial del hardware (POST) y luego se inicia el firmware BIOS o UEFI. Esto es lo primero que ocurre después de encender la máquina.

Paso 3: Carga del gestor de arranque:

El firmware BIOS/UEFI carga el gestor de arranque. En la mayoría de las distribuciones de Debian, el gestor de arranque utilizado es GRUB (Grand Unified Bootloader). GRUB muestra un menú de arranque donde puedes seleccionar el sistema operativo o la configuración que deseas iniciar.

Paso 4: Inicio del Kernel de Linux:

Una vez que seleccionas Debian en el menú de GRUB, el Kernel de Linux se carga en la memoria y comienza su inicialización. El Kernel es el núcleo del sistema operativo y se encarga de interactuar con el hardware.

Paso 5: Proceso de inicio del sistema:

El Kernel de Linux inicia el proceso init o su sucesor, que en sistemas modernos suele ser systemd. systemd es un sistema de inicio y administración de servicios que maneja el proceso de arranque.

Paso 6: Objetivo (Target) de inicio por defecto:

El sistema tiene un "objetivo de inicio" (target) por defecto, que define qué servicios y recursos se deben iniciar. El objetivo de inicio por defecto en Debian suele ser multi-user.target o graphical.target, dependiendo de si estás en un entorno de línea de comandos o entorno gráfico. Puedes verificar el objetivo de inicio actual con el siguiente comando:
```
systemctl get-default
```
El target que viene por defecto en la maquina es el ***graphical.target*** que inicia el sistema con una interfaz grafica ( en nuestro caso no nos hara falta) por lo que lo cambiaremos a ***multi-user.target*** que ser mas adecuado para un servidor debian.

Paso 7: Cambiar el objetivo de inicio:

Puedes cambiar el objetivo de inicio utilizando el siguiente comando:
```
sudo systemctl set-default multi-user.target
```

Paso 8: Listar los objetivos de inicio y su estado:

Puedes listar los targets del sistema y su estado:
```
 systemctl list-unit-files --type=target
```
Esto mostrará una lista de servicios en ejecución y su estado.

Paso 10: Otros tipos de unidades:

Para listar los servicios de nuestro sistem:
```
 systemctl list-unit-files --type=service

```

Además de los objetivos y los servicios, systemd también gestiona otros tipos de unidades, como sockets, dispositivos, montajes y más. Puedes ver cuales son utilizando el siguiente comando: 
```
systemctl list-units -t help
```
Para ver alguno en concreto se puede utilizar los comandos que utilizamos anteriormente cambiando service por el que nos interese

## d) Determine los tiempos aproximados de botado de su kernel y del userspace. Obtenga la relación de los tiempos de ejecución de los services de su sistema.
Determinar los tiempos aproximados de arranque del kernel y del userspace, así como obtener la relación de los tiempos de ejecución de los servicios en un sistema Debian es un proceso detallado que requiere la herramienta systemd-analyze. A continuación, te mostraré cómo realizar esto paso a paso:

Paso 1: Determinar el tiempo de arranque del kernel y del userspace:
```
systemd-analyze
```
Este comando proporcionará una visión general de los tiempos de arranque del kernel y del userspace, así como el tiempo total de inicio.

Para obtener un desglose más detallado de los tiempos de arranque, puedes ejecutar:
```
systemd-analyze blame
```
Esto mostrará una lista de servicios y el tiempo que cada uno tomó para iniciar. Puedes ver qué servicios están tomando más tiempo durante el proceso de arranque.

Paso 2: Obtener la relación de los tiempos de ejecución de los servicios:

Para obtener una relación más detallada de los tiempos de ejecución de los servicios, sigue estos pasos:
Ejecuta el siguiente comando para obtener una lista de servicios y sus tiempos de inicio:
```
systemd-analyze critical-chain
```
Este comando mostrará una lista de servicios que son críticos para el inicio y sus tiempos de inicio. Te dará una idea de cuáles servicios pueden estar causando retrasos durante el inicio.

Si deseas ver una lista más completa de todos los servicios y sus tiempos de inicio, puedes utilizar:
```
systemctl list-units --type=service --all
```
Esto mostrará una lista completa de servicios con información sobre su estado y tiempo de inicio.

Recuerda que los tiempos de inicio pueden variar según la configuración y el hardware de tu sistema. Presta atención a los servicios que pueden estar causando retrasos y considera si alguno de ellos es necesario para tu uso cotidiano. Puedes desactivar o ajustar la prioridad de los servicios para mejorar el tiempo de inicio si es necesario.

## e) Investigue si alguno de los servicios del sistema falla. Pruebe algunas de las opciones del sistema de registro journald. Obtenga toda la información journald referente al proceso de botado de la máquina. ¿Qué hace el systemd-timesyncd?.

Para investigar si alguno de los servicios del sistema falla y obtener información del registro journald referente al proceso de arranque de la máquina en Debian, así como comprender qué hace el servicio systemd-timesyncd, sigue estos pasos detallados:

Paso 1: Verificar el estado de los servicios:

Para verificar si alguno de los servicios del sistema ha fallado, puedes utilizar el comando systemctl. Abre una terminal en tu sistema Debian y ejecuta lo siguiente:
```
systemctl --failed
```
Este comando mostrará una lista de servicios que han fallado.

Paso 2: Analizar el registro journald:

Systemd utiliza el servicio de registro journald para almacenar registros del sistema. Puedes usar el comando journalctl para acceder a estos registros. Para obtener información sobre el proceso de arranque de la máquina, ejecuta el siguiente comando:
```
journalctl -b
```
Este comando mostrará los registros desde el último inicio del sistema. Puedes utilizar las teclas de flecha o la barra de desplazamiento para navegar por los registros. Presta atención a cualquier mensaje de error o advertencia que puedas encontrar durante el proceso de arranque.

Paso 3: Investigar el systemd-timesyncd:

El servicio systemd-timesyncd es un servicio que se utiliza para sincronizar la hora del sistema con servidores de tiempo en Internet. Puedes obtener más información sobre este servicio ejecutando:
```
systemctl status systemd-timesyncd
```
Esto mostrará información sobre el estado actual del servicio y si ha estado funcionando correctamente.

Si deseas obtener más detalles sobre cómo está configurado systemd-timesyncd, puedes consultar su archivo de configuración:
```
cat /etc/systemd/timesyncd.conf
```
Esto mostrará la configuración actual del servicio de sincronización de tiempo.
Recuerda que systemd-timesyncd es útil para mantener la hora del sistema precisa, lo que es importante para muchas aplicaciones y servicios que dependen de un reloj preciso.


## f) Identifique y cambie los principales parámetros de su segundo interface de red (ens34). Configure un segundo interface lógico. Al terminar, déjelo como estaba.
## g) ¿Qué rutas (routing) están definidas en su sistema?. Incluya una nueva ruta estática a una determinada red.

Para verificar las rutas (routing) definidas en tu sistema Debian y agregar una nueva ruta estática a una red específica, sigue estos pasos detallados:

Paso 1: Verificar las rutas actuales:

Para ver las rutas actuales en tu sistema Debian, puedes utilizar el comando ip o el comando route. Vamos a utilizar ip en este ejemplo:
```
ip route show
```
Este comando mostrará una lista de las rutas actuales en tu sistema, incluyendo las rutas predeterminadas y las rutas específicas.

Paso 2: Agregar una nueva ruta estática:

Puedes hacerlo con el siguiente comando:
```
ip route add 10.11.52.0/24 via 10.11.48.1
```
Este comando agrega una nueva ruta estática que dice que cualquier tráfico destinado a la red 10.11.52.0 ira a tarves de la puerta de enlace que le indiquemos.

Si necesitas que esta ruta sea persistente y se conserve después de reiniciar el sistema, debes agregarla al archivo de configuración correspondiente. En sistemas basados en Debian, puedes hacerlo editando el archivo /etc/network/interfaces o utilizando la herramienta ip de systemd-networkd si estás usando systemd. Aquí hay un ejemplo utilizando systemd-networkd:
```
sudo nano /etc/systemd/network/50-static-route.network
```
Dentro del archivo, puedes agregar las siguientes líneas para definir la ruta estática:
```
[Match]
Name=nombre_de_interfaz

[Network]
Address=192.168.1.0/24
Gateway=192.168.0.1
```
Reemplaza nombre_de_interfaz con el nombre de la interfaz de red a través de la cual deseas que pase el tráfico, guarda el archivo y ciérralo.
Luego, puedes aplicar la configuración utilizando el siguiente comando:
```
sudo systemctl restart systemd-networkd
```
Esto hará que la ruta estática se active y persista incluso después de reiniciar el sistema.
Recuerda que las rutas estáticas son útiles para dirigir el tráfico hacia redes específicas a través de puertas de enlace definidas manualmente cuando no se utiliza un enrutamiento dinámico. Asegúrate de ajustar las direcciones IP y las máscaras de red según tus necesidades específicas.

Si queremos eliminar una ruta el comando que utilizaremos será: 
```
ip route del 10.11.52.0/24 via 10.11.48.1

```

## h) En el apartado d) se ha familiarizado con los services que corren en su sistema. ¿Son necesarios todos ellos?. Si identifica servicios no necesarios, proceda adecuadamente. Una limpieza no le vendrá mal a su equipo, tanto desde el punto de vista de la seguridad, como del rendimiento.

Para listar los servicios del sistema y su estado podemos utilizar el comando:
```
systemctl list-unit-files --type=service
```
Los servicios que nos encontramos ENABLED SON:

**1. accounts-daemon.service**

Su función principal es gestionar las cuentas de usuario y las configuraciones relacionadas con las cuentas.

**2. anacron.service** 

Es un servicio que asegura que las tareas programadas se ejecuten de manera confiable, incluso en sistemas que no están siempre encendidos o que tienen tiempos de inactividad.

**3. apparmor.service**

AppArmor limita los programas de acuerdo con un conjunto de reglas que especifican a qué archivos puede acceder un programa determinado.

**4. avahi-daemon.service**

El objetivo principal de Avahi es facilitar la configuración automática y la descubrimiento de servicios en redes locales sin la necesidad de un servidor de nombres centralizado

Este servicio se utilizar para disponer de bluettoh en nuestra maquina.

Para eliminarlo ya que no lo necesitamos ejecutamos:
```
systemctl stop avahi-daemon.service
```
```
systemctl disable avahi-daemon.service
```

```
systemctl mask avahi-daemon.service
```


**5. bluetooth.service**

Este servicio se utilizar para disponer de bluetooh en nuestra maquina.

Para eliminarlo ya que no lo necesitamos ejecutamos:
```
systemctl stop bluetooth.service
```
```
systemctl disable bluetooth.service
```
```
systemctl mask bluetooth.service
```
**6. console-setup.service**

Se encarga de configurar la disposición del teclado y otras características relacionadas con la consola del sistema, como la configuración de la fuente utilizada en la consola y las teclas de función especiales

**7. cron.service**

Se utiliza para administrar el cron, un programa que permite la programación de tareas automatizadas en un sistema. El cron es una herramienta poderosa que permite a los usuarios y administradores programar la ejecución de comandos, scripts y programas en momentos específicos o en intervalos regulares.

**8.  cups-browsed.service**

Este paquete proporciona cups-browsed, un demonio que navega por las transmisiones Bonjour de las impresoras remotas compartidas por CUPS y hace que las impresoras estén disponibles localmente, reemplazando la navegación/emisión por CUPS que se dejó caer en CUPS 1.6.x

Para eliminarlo ya que no lo necesitamos ejecutamos:
```
systemctl stop cups-browsed.service
```
```
systemctl disable cups-browsed.service
```
```
systemctl mask cups-browsed.service
```
**9. cups.service**

Servicio para la impresion, no será necesario.

Para eliminarlo ya que no lo necesitamos ejecutamos:
```
systemctl stop cups.service
```
```
systemctl disable cups.service
```
```
systemctl mask cups.service
```

**10. e2scrub_reap.service**

 El servicio se utiliza para realizar tareas de limpieza y mantenimiento en sistemas de archivos ext4, especialmente en lo que respecta a la corrección y prevención de errores en el sistema de archivos.

**11. getty.service**

Es un servicio fundamental en sistemas Unix y Linux que se encarga de la gestión de terminales virtuales y conexiones seriales, lo que permite a los usuarios iniciar sesión y trabajar en ellas

**12. keyboard-setup.service**

La función es asegurarse de que el teclado esté configurado correctamente según la configuración regional y de idioma del sistema

**13. ModemManager.service**

Demonio de gestión de módems de banda ancha móvil

Para eliminarlo ya que no lo necesitamos ejecutamos:
```
systemctl stop ModemManager.service
```
```
systemctl disable ModemManager.service
```
```
systemctl mask ModemManager.service
```

**14. NetworkManager.service**
El objetivo de NetworkManager es hacer que la configuración de la red sea lo más sencilla y automática posible. Si usa DHCP, NetworkManager está destinado a reemplazar las rutas predeterminadas, obtener direcciones IP de un servidor DHCP y cambiar los servidores de nombres cuando lo considere oportuno. De hecho, el objetivo de NetworkManager es hacer que la creación de redes simplemente funcione.
Intenta mantener una conexión de red activa disponible en todo momento. No es necesario porque tenemos la confiruación realizada en /etc/network/interfaces 

Para eliminarlo ya que no lo necesitamos ejecutamos:
```
systemctl stop NetworkManager.service
```
```
systemctl disable NetworkManager.service
```
```
systemctl mask NetworkManager.service
```

**15. open-vm-tools.service**

 Es parte de Open VMware Tools, que es una suite de utilidades diseñada para mejorar la integración y el rendimiento de máquinas virtuales en entornos VMware

Para eliminarlo ya que no lo necesitamos ejecutamos:
```
systemctl stop open-vm-tools.service
```
```
systemctl disable open-vm-tools.service
```
```
systemctl mask open-vm-tools.service
```
**16. pppd-dns.service**

PPP es un protocolo de comunicación ampliamente utilizado para establecer conexiones de red a través de líneas telefónicas, módems, conexiones DSL y otros medios

Para eliminarlo ya que no lo necesitamos ejecutamos:
```
systemctl stop pppd-dns.service
```
```
systemctl disable pppd-dns.service
```
```
systemctl mask pppd-dns.service
```
**17. switcheroo-control.service**
Para los sistemas que tienen una GPU integrada y una GPU dedicada, este paquete forzará de forma predeterminada el uso de la GPU integrada para ahorrar energía.

Para eliminarlo ya que no lo necesitamos ejecutamos:
```
systemctl stop switcheroo-control.service
```
```
systemctl disable switcheroo-control.service
```
```
systemctl mask switcheroo-control.service
```

**17. systemd-pstore.service**

Los registros de volcado persistente, a menudo denominados "pstore" o "pstore logs," son registros de eventos y errores que se almacenan en una ubicación persistente incluso a través de reinicios del sistema. Esta funcionalidad es útil para el diagnóstico de problemas y la recuperación de datos de errores en sistemas Linux.

**18. systemd-timesyncd.service**

Se encarga de la sincronización del reloj del sistema con servidores de tiempo en Internet. Es una parte importante de la infraestructura de tiempo en sistemas que utilizan systemd como sistema de inicio.

**19. udisks2.service**

El demonio udisks sirve como interfaz para los dispositivos de bloque del sistema, implementados a través de D-Bus. Maneja operaciones como consultar, montar, desmontar, formatear o extraer dispositivos de almacenamiento como discos duros o unidades USB.

Para eliminarlo ya que no lo necesitamos ejecutamos:
```
systemctl stop udisks2.service
```
```
systemctl disable udisks2.service
```
```
systemctl mask udisks2.service
```

**20. unattended-upgrades.service**

Su principal función es aplicar automáticamente las actualizaciones de seguridad disponibles en el sistema operativo, lo que contribuye a mantener el sistema protegido contra vulnerabilidades conocidas.

**21. wpa_supplicant.service**

 Es un servicio en sistemas Linux que se utiliza para administrar conexiones inalámbricas y autenticación en redes Wi-Fi

Para eliminarlo ya que no lo necesitamos ejecutamos:
```
systemctl stop wpa_supplicant.service
```
```
systemctl disable wpa_supplicant.service
```
```
systemctl mask wpa_supplicant.service
```
**22. vgauth.service**

Tambien relacionado con VmWare
Para eliminarlo ya que no lo necesitamos ejecutamos:
```
systemctl stop vgauth.service
```
```
systemctl disable vgauth.service
```
```
systemctl mask vgauth.service
```

**23. systemd-udevd.service**

Este servicio es esencial para la detección y gestión de dispositivos hardware en el sistema.

**24. systemd-udev-trigger.service**
Este servicio es parte del sistema de detección y administración de hardware.

## i) Diseñe y configure un pequeño “script” y defina la correspondiente unidad de tipo service para que se ejecute en el proceso de botado de su máquina.

El script que creamos meterá en un archivo de txt en la ruta /home/lsi/lastLogin.txt la ultima vez que nos logueamos en la máquina.

Ver servicio creado:
```
nano /etc/systemd/system/last-login.service
```
Para ver el script:
```
nano /usr/local/bin/last-login.sh
```
Para darle los permisos necesarios:
```
chmod ugo+rwx /usr/local/bin/last-login.sh
```
Y por último activamos el servicio:
```
systemctl enable last-login.service
```



## j) Identifique las conexiones de red abiertas a y desde su equipo.

El comando netstat -naut muestra una lista de todas las conexiones activas en un sistema, tanto conexiones TCP como UDP, con las direcciones IP y puertos en formato numérico. Aquí tienes una descripción más detallada de lo que muestra cada opción:

-n: Indica que las direcciones IP y puertos se mostrarán en formato numérico, en lugar de resolverlos a nombres de hosts y servicios. Esto es útil para obtener información más detallada y evitar retrasos en la resolución DNS.

-a: Muestra todas las conexiones, incluidas las conexiones establecidas y las que están esperando ser establecidas o cerradas.

-u: Muestra las conexiones UDP, que son conexiones no orientadas a la conexión que se utilizan para la transmisión de datos sin garantía de entrega ni orden.

-t: Muestra las conexiones TCP, que son conexiones orientadas a la conexión y confiables utilizadas para la transmisión de datos con garantía de entrega y orden.

```
netstat -naut
```
La info que muestra el comando:
Cuando ejecutas el comando netstat -naut, verás una lista de conexiones de red activas en tu sistema, y cada columna muestra información específica sobre estas conexiones. Aquí tienes una descripción de las columnas comunes que aparecen en la salida de netstat:

Proto: Esta columna indica el protocolo utilizado para la conexión. Puede mostrar "tcp" para conexiones TCP o "udp" para conexiones UDP.

**Recv-Q**: Esta columna muestra la cantidad de datos que se han recibido y que están en la cola de espera para ser procesados. Esta columna se aplica principalmente a conexiones TCP.

**Send-Q**: Muestra la cantidad de datos que se han enviado y están en la cola de espera para ser entregados. Al igual que Recv-Q, esta columna se aplica principalmente a conexiones TCP.

**Local Address**: Esta columna muestra la dirección IP y el puerto en el que el servicio local está escuchando. Por ejemplo, 192.168.1.2:80 significa que el servicio local está escuchando en la dirección IP 192.168.1.2 y en el puerto 80.

**Foreign Address**: Muestra la dirección IP y el puerto remoto al que está conectado el servicio local. Por ejemplo, 203.0.113.4:443 indica una conexión con la dirección IP 203.0.113.4 en el puerto 443.

**State**: Esta columna muestra el estado de la conexión TCP. Los estados comunes incluyen ESTABLISHED (conexión establecida), LISTEN (escuchando conexiones entrantes), TIME_WAIT (esperando a cerrar la conexión), entre otros.

**PID/Program name**: Esta columna muestra el identificador del proceso (PID) y, en algunos casos, el nombre del programa o servicio asociado con la conexión. Esto puede ser útil para identificar qué proceso está utilizando una conexión específica.

En resumen, el comando netstat -naut mostrará una lista de todas las conexiones activas en el sistema, incluidas las conexiones TCP y UDP, con direcciones IP y puertos en formato numérico. Esto es útil para diagnosticar y comprender las conexiones de red en un sistema.


## k) Nuestro sistema es el encargado de gestionar la CPU, memoria, red, etc., como soporte a los datos y procesos. Monitorice en “tiempo real” la información relevante de los procesos del sistema y los recursos consumidos. Monitorice en “tiempo real” las conexiones de su sistema.

Mostrar procesos del sistem y rescursos consumidos
```
top
```
Esto mostrará bastante info, el PID l user del proceso, PR (prioridad del proceso donde mas bajo es mayor prioridad), 
NI(Nice" puede variar en un rango de -20 a +19, donde -20 es la prioridad más alta (mayor importancia) y +19 es la prioridad más baja (menor importancia)), VIRT (memoria virtual utilizada en el proceso) , RES (memoria fisica utiliza en el proceso) ,
SHR (memoria compartida utiliza por cada proceso), S (estado del proceso-> R(Running); S(Sleeping, en espera); D(Uninterruptible Sleep, generalmente esperando a que ocurra algo para continuar); Z(Zombie, terminado pero no eliminado al completo); T(Stopped);I(Inactivo);

Mostrar conexiones del sistema:
```
netstat -c –continuous
```
Listar conexiones abiertas por procesos del sistema:
```
lsof -i
```
Este comando mostrara bastane info: nombre del comando, PID, FD(descriptor de archivo asociado al recurso), user, type(UDP/TCP),size/off (tamaño) , NODE y NAME

## l) Un primer nivel de filtrado de servicios los constituyen los tcp-wrappers. Configure el tcpwrapper de su sistema (basado en los ficheros hosts.allow y hosts.deny) para permitir conexiones SSH a un determinado conjunto de IPs y denegar al resto. ¿Qué política general de filtrado ha aplicado?. ¿Es lo mismo el tcp-wrapper que un firewall?. Procure en este proceso no perder conectividad con su máquina. No se olvide que trabaja contra ella en remoto por ssh.

Las políticas o reglas para filtrar el acceso al servidor desde la red se definen de la siguiente
forma:
Demonios o lista de demonios del sistema : Lista de equipos : Acción a realizar
A continuación detallamos cada campo:
– Demonios:
Son servicios que existen en sistemas operativos Unix como por ejemplo sshd (servicio SSH),
slapd (servicio LDAP) o proftpd (servicio FTP). Para crear una regla común para varios demonios
debemos indicar sunombre separados por comas. Existe también el comodín “ALL” que hace
que dicha política afecte a todos los demonios del sistema.
– Lista de equipos:
En este campo indicamos a que equipos aplicamos esta política. Podemos indicar una dirección
IP, un rango de direccionesIP, o un nombre de dominio. También podremos usar el comodín “ALL”
para que esta política afecte a todos los equipos que intenten acceder. También existe el
operador “EXCEPT” que nos permite eliminar de la regla uno o varios equipos.
– Acción a realizar:
Aquí debemos indicar si la política permite el acceso o deniega elacceso a los demonios
indicados anteriormente. Las palabras que se usa denegar el acceso es “deny”. En caso de
dejar este campo vacío, significa que permitimos el acceso a los demonios y equipos indicados.
Opcionalmente, podemos enviar comandos con la directiva “spawn”. Esta directiva suele ser
utilizada para la creación de registros de conexión al propio equipo.
Para configurar el hosts.allow, hosts.deny empezaremos permitiendo las conexiones que nos
interesen en hosts.allow y denegaremos el resto en hosts.deny

Los ficheros de configuración están situados en /etc/hosts.allow, /etc/hosts.deny.

```
cat /etc/hosts.allow
```
```
cat /etc/hosts.deny
```





## m) Existen múltiples paquetes para la gestión de logs (syslog, syslog-ng, rsyslog). Utilizando el rsyslog pruebe su sistema de log local.
## n) Configure IPv6 6to4 y pruebe ping6 y ssh sobre dicho protocolo. ¿Qué hace su tcp-wrapper en las conexiones ssh en IPv6? Modifique su tcp-wapper siguiendo el criterio del apartado h). ¿Necesita IPv6?. ¿Cómo se deshabilita IPv6 en su equipo?

# PARTE 2

## a) En colaboración con otro alumno de prácticas, configure un servidor y un cliente NTP.


> Demostración con `10.11.48.203` como servidor y `10.11.48.207` como cliente.

1. Servidor: El fichero `/etc/ntp.conf` debería quedar así:

```bash
# /etc/ntp.conf, configuration for ntpd; see ntp.conf(5) for help

driftfile /var/lib/ntp/ntp.drift

# Leap seconds definition provided by tzdata
leapfile /usr/share/zoneinfo/leap-seconds.list

# Enable this if you want statistics to be logged.
#statsdir /var/log/ntpstats/

statistics loopstats peerstats clockstats
filegen loopstats file loopstats type day enable
filegen peerstats file peerstats type day enable
filegen clockstats file clockstats type day enable

# Configuración servidor
server 127.127.1.0 minpoll 4
fudge 127.127.1.0 stratum 0

# You do need to talk to an NTP server or two (or three).

# pool.ntp.org maps to about 1000 low-stratum NTP servers.  Your server will
# pick a different set every time it starts up.  Please consider joining the
# pool: <http://www.pool.ntp.org/join.html>
#pool 0.debian.pool.ntp.org iburst
#pool 1.debian.pool.ntp.org iburst
#pool 2.debian.pool.ntp.org iburst
#pool 3.debian.pool.ntp.org iburst
```

```bash
# By default, exchange time with everybody, but don't allow configuration.
#restrict -4 default kod notrap nomodify nopeer noquery limited
#restrict -6 default kod notrap nomodify nopeer noquery limited
restrict default ignore
restrict 10.11.48.207 nomodify nopeer notrap

# Local users may interrogate the ntp server more closely.
restrict 127.0.0.1
restrict ::1
```

2. Cliente: Dejamos el fichero `/etc/ntp.conf` así:

```bash
# /etc/ntp.conf, configuration for ntpd; see ntp.conf(5) for help

driftfile /var/lib/ntp/ntp.drift

# Leap seconds definition provided by tzdata
leapfile /usr/share/zoneinfo/leap-seconds.list

# Enable this if you want statistics to be logged.
#statsdir /var/log/ntpstats/

statistics loopstats peerstats clockstats
filegen loopstats file loopstats type day enable
filegen peerstats file peerstats type day enable
filegen clockstats file clockstats type day enable

### Configuración cliente
server 10.11.48.203 minpoll 4
fudge 127.127.1.0 stratum 1
```

```bash
# By default, exchange time with everybody, but don't allow configuration.
#restrict -4 default kod notrap nomodify nopeer noquery limited
#restrict -6 default kod notrap nomodify nopeer noquery limited
#restrict default ignore
#restrict 10.11.48.203 nomodify notrap nopeer

# Local users may interrogate the ntp server more closely.
restrict 127.0.0.1
restrict ::1

# Needed for adding pool entries
restrict source notrap nomodify noquery
```

3. Actualizar cambios en `ntp.conf`: `systemctl restart ntp`.

4. Comprobamos:

```console
root@debian:~# date +%T -s 1
01:00:00
root@debian:~# ntpdate 10.11.49.203
```

> Lo que hice aquí fue poner el reloj local del cliente a las `01:00:00` y hacer `ntpdate` a la IP del servidor NTP para sincronizar la hora.



## b) Cruzando los dos equipos anteriores, configure con rsyslog un servidor y un cliente de logs.

**Servidor RSYSLOG ( IP --> 10.11.48.207) :**

Primero instalamos rsyslog en nuestro sistema (seguramente ya lo tengamos)
```
apt install rsyslog
```
A continuación iremos al archivo de configuración del rsyslog:
```
pico /etc/rsyslog.conf
```
El archivo quedará configurado de la siguiente forma: 
```

#################
#### MODULES ####
#################

module(load="imuxsock") # provides support for local system logging
module(load="imklog")   # provides kernel logging support
#module(load="immark")  # provides --MARK-- message capability

# provides UDP syslog reception
#module(load="imudp")
#input(type="imudp" port="514")

# provides TCP syslog reception
module(load="imtcp")
input(type="imtcp" port="514")


###########################
#### GLOBAL DIRECTIVES ####
###########################

#
# Set the default permissions for all log files.
#
$FileOwner root
$FileGroup adm
$FileCreateMode 0640
$DirCreateMode 0755
$Umask 0022

#
# Where to place spool and state files
#
$WorkDirectory /var/spool/rsyslog

#
# Include all config files in /etc/rsyslog.d/
#
$IncludeConfig /etc/rsyslog.d/*.conf


# Servidor solo aceptara mensajes del compañero (IP 10.11.48.203)

$AllowedSender TCP,127.0.0.1, 10.11.48.203
# Template para guardar los registros de log

#$template remote, "/var/log/rsyslog-server/%fromhost-ip%/%programname%.log"
#*.* ?remote
#& stop

# Los registros recibidos se almacenaran en /var/log/remote
$template remote, "/var/log/remote/%HOSTNAME%/%PROGRAMNAME%.log"
*.* ?remote
& stop

```
**Servidor RSYSLOG ( IP --> 10.11.48.203) :**

Añadir en el archivo de configuración lo siguiente:

```
*.* action(
       type="omfwd"
       target="10.11.48.207"
       port="514"
       protocol="tcp"
       action.resumeRetryCount="-1"
       queue.type="linkedlist"
       queue.filename="/var/log/rsyslog-queue"
       queue.saveOnShutdown="on"
)

```
Despues de configurar ambas maquina para que los cambios sean efectivos reiniciamos el servicio:
```
systemctl restart rsyslog.service
```
Si queremos ver el estado actual del servicio:
```
systemctl status rsyslog.service
```
A continuación para probar que funciona correctamente:
- Cliente:
Lanzamos lo siguiente desde la cmd:
```
logger "ESTO ES UNA PRUEBA"
```

- Servidor:
Si desde el cliente ha ejecutado lo anterior desde el usuario root (el log se guardara en root.log)
```
cat /var/log/rsyslog-server/10.11.48.203/root.log

```
Si lo ejecuto desde el usuario LSI solo habria que cambiar root.log por lsi.log

En este punto vemos que funciona correctamente, a cotinuacion vamos a probar la cola:

1. Primero "comentamos" para que deje de escuchar:
```
pico /etc/rsyslog.conf
```
Comentamos la linea y luego reiciamos el servicio
```
# provides TCP syslog reception
module(load="imtcp")
# input(type="imtcp" port="514")
```
2. A continuación desde el cliente enviamos un log como el de antes :
3. Ejecutamos el siguiente comando para ver que los log no estan llegando:

```
tail /var/log/rsyslog-server/10.11.48.203/root.log
```
4. Volvemos a poner al servidor escuchando ( descomentamos la línea) y volvemos a lanzar desde el cliente un logger
5. Tras unos segundos ejecutamos el siguiente comando para ver que los mensajes de han procesado y almacenado correctamente:
```
tail /var/log/rsyslog-server/10.11.48.203/root.log
```


## c) Haga todo tipo de propuestas sobre los siguientes aspectos.: ¿Qué problemas de seguridad identifica en los dos apartados anteriores?. ¿Cómo podría solucionar los problemas identificados?
## d) En la plataforma de virtualización corren, entre otros equipos, más de 200 máquinas virtuales para LSI. Como los recursos son limitados, y el disco duro también, identifique todas aquellas acciones que pueda hacer para reducir el espacio de disco ocupado.

