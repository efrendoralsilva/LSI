# Práctica 1 : Configuración básica

En este archivo se muestra como solucionar cada apartado de la práctica 1 de la asignatura de Legsilación y Seguridad Informática de la facultad de informática de la universsidad de la Coruña (UDC).

### Apartado A

Configure su máquina virtual de laboratorio con los datos proporcionados por el profesor.
Analice los ficheros básicos de configuración (interfaces, hosts, resolv.conf,
nsswitch.conf, sources.list, etc.)

La configuración de la máquina virtual dependerá de las IPs que sean proporcionadas por el profesor para nosotros y para nuestro compañero. Para configurar la mñaquina tenemos que editar el fichero `/etc/network/interfaces:`

El siguiente ejemplo se correspone con las IPs 10.11.48.25 y 10.11.50.25
```
# This file describes the network interfaces available on your system
# and how to activate them. For more information, see interfaces(5).
# source /etc/network/interfaces.d/*
# The loopback network interface
auto lo ens33 ens34
iface lo inet loopback

iface ens33 inet static
	address 10.11.48.25
	netmask 255.255.254.0
	broadcast 10.11.49.255
	network 10.11.48.0
	gateway 10.11.48.1

iface ens34 inet static
	address 10.11.50.25
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
## c) Identifique la secuencia completa de arranque de una máquina basada en la distribución de referencia (desde la pulsación del botón de arranque hasta la pantalla de login). ¿Qué target por defecto tiene su máquina?. ¿Cómo podría cambiar el target de arranque?. ¿Qué targets tiene su sistema y en qué estado se encuentran?. ¿Y los services?. Obtenga la relación de servicios de su sistema y su estado. ¿Qué otro tipo de unidades existen?.
## d) Determine los tiempos aproximados de botado de su kernel y del userspace. Obtenga la relación de los tiempos de ejecución de los services de su sistema.
## e) Investigue si alguno de los servicios del sistema falla. Pruebe algunas de las opciones del sistema de registro journald. Obtenga toda la información journald referente al proceso de botado de la máquina. ¿Qué hace el systemd-timesyncd?.
## f) Identifique y cambie los principales parámetros de su segundo interface de red (ens34).Configure un segundo interface lógico. Al terminar, déjelo como estaba.
## g) ¿Qué rutas (routing) están definidas en su sistema?. Incluya una nueva ruta estática a una determinada red.
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

