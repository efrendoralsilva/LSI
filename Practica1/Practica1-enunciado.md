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
Paso 7: Cambiar el objetivo de inicio:

Puedes cambiar el objetivo de inicio utilizando el siguiente comando:
```
sudo systemctl set-default nombre_del_target
```
Reemplaza nombre_del_target con el nombre del objetivo que desees establecer, como multi-user.target o graphical.target.

Paso 8: Listar los objetivos de inicio y su estado:

Puedes listar los objetivos de inicio disponibles y su estado con el siguiente comando:
```
systemctl list-units --type=target
```
Esto mostrará una lista de servicios en ejecución y su estado.

Paso 10: Otros tipos de unidades:

Además de los objetivos y los servicios, systemd también gestiona otros tipos de unidades, como sockets, dispositivos, montajes y más. Puedes listarlos utilizando comandos similares, reemplazando --type=service con el tipo de unidad que deseas consultar (por ejemplo, --type=socket, --type=device, etc.).

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

Supongamos que deseas agregar una nueva ruta estática a la red 192.168.1.0/24 a través de una puerta de enlace (gateway) con la dirección IP 192.168.0.1. Puedes hacerlo con el siguiente comando:
```
ip route add 192.168.1.0/24 via 192.168.0.1
```
Este comando agrega una nueva ruta estática que dice que cualquier tráfico destinado a la red 192.168.1.0/24 debe ser enviado a través de la puerta de enlace con la dirección IP 192.168.0.1.

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


## h) En el apartado d) se ha familiarizado con los services que corren en su sistema. ¿Son necesarios todos ellos?. Si identifica servicios no necesarios, proceda adecuadamente. Una limpieza no le vendrá mal a su equipo, tanto desde el punto de vista de la seguridad, como del rendimiento.
## i) Diseñe y configure un pequeño “script” y defina la correspondiente unidad de tipo service para que se ejecute en el proceso de botado de su máquina.
## j) Identifique las conexiones de red abiertas a y desde su equipo.
## k) Nuestro sistema es el encargado de gestionar la CPU, memoria, red, etc., como soporte a los datos y procesos. Monitorice en “tiempo real” la información relevante de los procesos del sistema y los recursos consumidos. Monitorice en “tiempo real” las conexiones de su sistema.
## l) Un primer nivel de filtrado de servicios los constituyen los tcp-wrappers. Configure el tcpwrapper de su sistema (basado en los ficheros hosts.allow y hosts.deny) para permitir conexiones SSH a un determinado conjunto de IPs y denegar al resto. ¿Qué política general de filtrado ha aplicado?. ¿Es lo mismo el tcp-wrapper que un firewall?. Procure en este proceso no perder conectividad con su máquina. No se olvide que trabaja contra ella en remoto por ssh.
## m) Existen múltiples paquetes para la gestión de logs (syslog, syslog-ng, rsyslog). Utilizando el rsyslog pruebe su sistema de log local.
## n) Configure IPv6 6to4 y pruebe ping6 y ssh sobre dicho protocolo. ¿Qué hace su tcp-wrapper en las conexiones ssh en IPv6? Modifique su tcp-wapper siguiendo el criterio del apartado h). ¿Necesita IPv6?. ¿Cómo se deshabilita IPv6 en su equipo?



# PARTE 2

## a) En colaboración con otro alumno de prácticas, configure un servidor y un cliente NTP.
## b) Cruzando los dos equipos anteriores, configure con rsyslog un servidor y un cliente de logs.
## c) Haga todo tipo de propuestas sobre los siguientes aspectos.: ¿Qué problemas de seguridad identifica en los dos apartados anteriores?. ¿Cómo podría solucionar los problemas identificados?
## d) En la plataforma de virtualización corren, entre otros equipos, más de 200 máquinas virtuales para LSI. Como los recursos son limitados, y el disco duro también, identifique todas aquellas acciones que pueda hacer para reducir el espacio de disco ocupado.

