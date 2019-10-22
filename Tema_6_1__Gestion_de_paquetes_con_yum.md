# Tema 6 - Gestión de paquetes software con YUM

Este tema trata sobre cómo gestionar los programas en Linux CentOS 7, usando `YUM`. Pero la teoría y filosofía de trabajo es aplicable a cualquier sistema Linux, aunque se basen otras herramientas, como RPM o APT. Se aprenderá a:

- Instalar, reinstalar y actualizar paquetes usando YUM.
- Obtener información de los paquetes instalados, incluyendo: versión, estado, 
  dependencias e integridad.
- Determinar qué ficheros proporciona un paquete, así como encontrar de qué 
  paquete proviene un determinado fichero.

En los primeros días de Linux, muchos programas se distribuían en código fuente. El usuario tenía que compilar este código fuente para crear los programas correspondientes (binarios), así como la actualización de las páginas *man* y modificaciones en los archivos de configuración. Actualmente las distribuciones Linux (*distros*) proporcionan conjuntos de programas llamados paquetes (*packages*), listos para su instalación.

Este tema se centra en **YUM** (*Yellowdog Updater Modified*), usado por CentOS, aunque también se pueden realizar instalaciones basadas en RPM (RedHat Package Manager). Los Linux basados en Debian usan APT.  

Las instalaciones basadas en**YUM** presentan la ventaja, sobre las instalaciones basadas en RPM, que YUM analiza las dependencias (dependencies)con otros paquetes, y si se necesitan paquetes adicionales, invita a su instalación.

Durante la vida de un sistema Linux, es fácil que el usuario quiera instalar nuevas funcionalidades a su sistema, borrar o actualizar los paquetes originales.  

En las instalaciones con RPM, existe la posibilidad de que falte algo. 

## Instalando paquetes (yum install)

Para saber si tenemos un paquete (xx) instalado tenemos varias opciones:

```bash
# Podemos invocarle directamente
nano
nano --help
# no se encontró la orden

# Podemos ver si existe su página de manual
man nano
# No manual entry for nano

# Podemos buscar si está en el PATH
which nano
# no nano in (/usr/local/bin:/usr/local/sbin:/usr/bin:/usr/sbin:/bin:/sbin:)

# O intentar saber qué tipo de fichero es
type nano
# type: nano: no se encontró
```

Si se obtiene una repuesta de que no se encuentra, es que no está instalado.

El comando para instalar un paquete es `yum install`. Para ejecutar el comando *yum*, y sus diferentes opciones, primero hay que ser superusuario. Si se pide confirmación de instalación, responder *y*. Entonces de descarga los paquetes.

Si aparece una confirmación final, en este caso *¡Listo!*, el paquete ha sido descargado e instalado correctamente  

```bash
su -

yum install nano
Is this ok [y/d/N]: y
¡Listo!

exit
 
```


## Desinstalando paquetes con YUM (yum remove)

Para desinstalar paquetes se usa el comando *`yum remove`* como superusuario. Si el paquete a eliminar se utiliza por otros paquetes, se ofrece la posibilidad de desinstalar los paquetes dependientes.

```bash
su -

yum remove gfortan
# ............  Resolving dependencies
# ............. Dependencies resolved
# Is this ok [y/N]

```

## Actualizando paquetes con YUM (yum update)

Para actualizar paquetes se usa el comando  *`yum update ´nombre de paquete´`*. Si no se especifica ningún paquete, *yum* entiende que se quiere actualizar el sistema entero.

```bash
su -

yum update ´nano´
# ............
# Resolving dependencies
# ................
# Dependencies resolved
# Is this ok [y/N] y
# Complete!

```

Es posible actualizar a una versión determinada con *`yum update_to xx.yy.zz`*:

```bash
su -

yum update_to xx.yy.zz ´nano´

```

## Información de paquetes (yum info)

Para obtener paquetes con yum el comando es *`yum info ´nombre de paquete´`*. 

Si no se especifica ningún paquete, *yum* entiende que se quiere información sobre el sistema entero. Para obtener información de paquetes no es necesario ser superusuario.
 
```bash
yum info nano
 
```

## Comprobar actualizaciones (yum check-update)

Es posible consultar si un paquete, o el sistema entero, tiene todas las actualizaciones instaladas, o bien si hay paquetes con actualizaciones. Los valores posibles que devuelve *yum check-update* son:

- **100**: Significa que hay paquetes disponibles para una actualización. También se presenta una lista de paquetes con actualizaciones pendientes. 
- **0**: No hay actualizaciones disponibles.
- **1**: Si hay errores. 

```
yum check-update nano
 
yum check-update

```

## Búsqueda de paquetes (yum search)

Se puede buscar un término entre el nombre o la descripción de todos los paquetes disponibles en los repositorios. Para ello se usa el comando *`yum search`*:

```bash
yum search pyqt

```

## Haciendo limpieza (yum clean)

Los gestores de paquetes suelen utilizar cachés para almacenar información sobre los paquetes. Estas cachés ocupan espacio en disco, y pueden ser vaciadas para liberar espacio. El comando para limpiar todas estas cachés y metadatos es *`yum clean all`*.

```bash
yum clean [expired-cache] [packages][headers][metadata][plugins]....
yum clean all

```


## Reinstalar (yum reinstall)

Hay veces que un comando falla en  una instalación de linux. Antes de reinstalar el linux completo se puede intentar reinstalar unicamente el paquete que no funciona  
```bash
yum reinstall nano
 

```
## Repositorios 

YUM, al igual que APT, trabaja con **repositorios**, que son conjuntos de paquetes a los que, típicamente, se accede mediante una conexión de red.

**Localización de los paquetes**

Tecleando un *ls -la /etc* podemos ver los directorios *`/etc/yum.repos.d/`*, *`/etc/yum/`* y el fichero informativo *`yum.conf/`*. Todos estos ficheros y directorios guardan información y configuración de yum.

La información de los paquetes instalados se guarda, por defecto, en el directorio *`/etc/yum.repos.d/`* que contiene varios ficheros que son repositorios (*.repo*). La información de los *repos* se dividen en tres secciones: paquetes normales (CentOS-Base.repo), paquetes de desarrollo (CentOS-Debuginfo.repo) y paquetes en código fuente (CentOS-Sources.repo). El *.repo* dice a CentOS en que *mirrors* (o réplicas) están las últimas versiones de los paquetes.


```bash
ls -l /etc
ls -l /etc/yum.repos.d
```

## Añadir nuevos repositorios

Para añadir un repositorio debemos crear un fichero con extension .repo. 
Después mediante un editor como vim o nano, se crea un fichero con nombre *nombre_repositorio.repo* se incluyen los siguientes campos:

```bash


[nombre_repositorio]
name=Nombre del repositorio
baseurl=http://servidor/repositorio
enabled=1
gpkey=http:/servidor/repositorio/gpgkey
gpgcheck=1
```
name indica el nombre. baseurl es la ruta hasta el repositorio. Enabled habilita (1) o deshabilita el repositorio. gpgkey es la ruta de la llave de seguridad. pqpcheck(=1) comprueba la integridad con la llave.

Una vez guardado el fichero .repo listamos los repositorios.


```bash
yum repolits
```
Se debe obtener  un listado similar al siguiente:

```bash
Complementos cargados:fastestmirror
Loading mirror speeds from cached hostfile
* base: mirror.librelabucm.org
* extras: mirror.librelabucm.org
* update. mirror.librelabucm.org
*id del repositorio		nombre del repositorio
!base/7/x86_64			CentOS-7 - Base
!extras/7/x86_64		CentOS-7 - Extras
!updates/7/x86_64		CentOS-7 - Updates
!n_repositorio/7/x86_64	CentOS-7 - n_repositorio
 
```
A continuación se instalaría el paquete


```bash
yum install nombre_repositorio
```

## Paquetes adicionales para Linux Empresarial (EPEL)

EPEL (Extra Packages for Enterprise Linux) es un Grupo de Interés Especial (SIG) de la distribución Fedora, que crea, mantiene y gestiona un conjunto de paquetes para sistemas operativos Linux derivados de Red Hut  

 
La lista de los paquetes EPEL disponibles para Centos 7, para micros x86_64 se pueden encontrar en:
https://dl.fedoraproject.org/pub/epel/7/x86_64/Packages/a/


Desde el siguiente enlace se descarga el fichero .rpm para CentOS 7 "epel-release-latest-7"
https://fedoraproject.org/wiki/EPEL?rd=EPEL/en

Para instalarlos:
```yum install https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
```
el paquete se incluye en el repositorio CentOS Extras, y se habilita por defecto 

Para CentOS 7 se recomienda habilitar los repositorios opcional, extra y HA para solucionar las dependencias con estos repositorios:
```subscription-manager repos --enable "rhel-*-optional-rpms" --enable "rhel-*-extras-rpms"  --enable "rhel-ha-for-rhel-*-server-rpms```

Como usar estos paquetes. EPEL tiene un paquetel "epel-release" que incluye las llaves gpg para el firmado de paquetes e información del repositorio. Una vez instalado se pueden usar herramientas de linux, como yum para instalar paquetes y sus dependencias.




## Paquetes adicionales para Linux Empresarial (Wandisco Git repositry)
Git MultiSite aplica la tecnología WANdisco para permitir la colaboracion entre equipos distribuidos, usando Git, permitiendo trabajar conjuntamente, como si estuvieran en el mismo lugar físico.
Git Multisite elimina la necesidad de un repositorio central.

Un ejemplo, en este momento, los repositorios de CentOS 7 proporcionan una versión muy antigua de Git, la 1.8.3, cuando la versión actual es la 2.18

La forma más facil de instalar Git v2.18 es instalarlo usando la herramienta de instalación de paquetes yum desde los repositorios Wandisco.

Otra opción sería es compilar el codigo fuente de Git, pero en este caso no podriamos actualizarlo através del gestor de paquetes yum

En primer lugar cambiariamos a super-usario "su"

En primer lugar activariamos el repositorio Wandisco Git. Para esto crearimos un archivo *wandisco-git.repo* en el directory */etc/yum.repos.d*

```sudo nano /etc/yum.repos.d/wandisco-git.repo```
El contenido de este fichero será
```
[wandisco-git]
name=Wandisco GIT Repository
baseurl=http://opensource.wandisco.com/centos/7/git/$basearch/
enabled=1
gpgcheck=1
gpgkey=http://opensource.wandisco.com/RPM-GPG-KEY-WANdisco
````

Importariamos las llaves gpg:
````sudo rpm --import http://opensource.wandisco.com/RPM-GPG-KEY-WANdisco
````

Una vez que el repositori se ha añadido, instalariamos la última versión de Git
````sudo yum install git
````

Para verificar la instalación, podemos preguntarle a Git su versión:
````git --version
````

Podemos asumir que es correcta, si responde algo similar a:
```
Output
git version 2.18.0
````
Ahora sería el momento de añadir tu información personal, que será usada cuando se realicen cambios en tu código:

```
git config --global user.name "Tu Nombre"
git config --global user.email "tucorreo@tudominio.com"
````

Para comprobar los datos introducidos:

```
git config --list"

Output
user.name=Tu Nombre
user.email=Tucorreo@tudominio.com
````

La información anterior se guarda en el fichero home/nombre de usuario/.gitconfig



## Algunos recursos útiles

Se recomienda leer la página de manual de *yum*, donde se puede encontrar información detallada de todas sus opciones:

```bash
man yum
info yum

```
https://fedoraproject.org/wiki/EPEL?rd=EPEL/en

https://dl.fedoraproject.org/pub/epel/7/x86_64/Packages/a/

https://docs.wandisco.com/git/ms/1.0/index.html#whatisgitmultisite

