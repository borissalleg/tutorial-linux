# 🐧**Sistema de Archivos y Estructura de Directorios en Linux**

<p align="center">
  <img src="https://www.ticarte.com/sites/su/users/7/image/estrucutura_directorios.jpg" alt="Descripción" width="500"/>

</p>


Linux sigue una jerarquía de directorios unificada y estandarizada, definida por el *Filesystem Hierarchy Standard* (**FHS**). A diferencia de sistemas como Windows (con unidades `C:`, `D:`, etc.), Linux monta todos los dispositivos, particiones y sistemas de archivos bajo un único árbol raíz: `/`.

Esta estructura no es arbitraria: cada directorio tiene un propósito específico, lo que garantiza **coherencia**, **seguridad**, **portabilidad** y **facilidad de mantenimiento** —especialmente relevante en entornos contenerizados, donde entender qué existe (o no) en rutas como `/bin`, `/etc` o `/tmp` es clave para depurar, construir imágenes o escribir scripts robustos.

En distribuciones minimalistas como **Alpine Linux**, algunos directorios pueden contener menos contenido (por ejemplo, ausencia de `/usr/sbin` en imágenes base), pero la estructura general y su semántica se mantienen.

---


=== "🔑 Convenciones clave"

    | Símbolo | Significado |
    |--------|-------------|
    | ✅ | Seguro para lectura/uso común |
    | ⚠️ | Requiere conocimiento o privilegios (`sudo`, `root`) |
    | ⛔ | Evitar modificación manual (riesgo de inestabilidad) |
    | 🏠 | Directorio *home* (personal) |
    | 🛠️ | Configuración del sistema |
    | 🖥️ | Información virtual (no en disco) |
    | 📁 | Datos estructurados |
    | 📈 | Datos que crecen con el tiempo |
    | 📦 | Software/aplicaciones |

    ---

=== " 🗂️ Estructura Estándar de Directorios en Linux (FHS)"

    | Directorio       | Descripción                                                                                          | Contenido típico                                                                 |
    |------------------|------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------|
    | `/`              | **Raíz del sistema de archivos**. Punto de partida de toda la jerarquía.                             | Subdirectorios esenciales (`bin`, `etc`, `home`, `usr`, etc.)                   |
    | `/bin`           | **Binarios esenciales para todos los usuarios** (disponibles en modo de recuperación).              | `sh`, `ls`, `cp`, `mv`, `rm`, `cat`, `echo`, `grep`, `mount`                     |
    | `/sbin`          | **Binarios esenciales para el administrador del sistema** (requieren privilegios).                   | `fdisk`, `ifconfig`, `iptables`, `reboot`, `shutdown`, `init`                   |
    | `/etc`           | **Archivos de configuración del sistema** (no binarios, no variables en tiempo de ejecución).        | `passwd`, `group`, `hosts`, `resolv.conf`, `nginx/`, `ssh/`, `apk/repositories` |
    | `/home`          | Directorios personales de los usuarios (excepto `root`).                                             | `/home/alice`, `/home/bob` → cada uno con `~/.bashrc`, `Documents`, etc.        |
    | `/root`          | Directorio personal del usuario `root`.                                                              | `~/.profile`, scripts de administración                                          |
    | `/tmp`           | Archivos temporales (borrados al reiniciar, en muchas configuraciones).                              | Archivos de procesos efímeros, descargas parciales                              |
    | `/var`           | **Datos variables**: logs, colas, cachés, bases de datos.                                            | `/var/log`, `/var/cache`, `/var/lib`, `/var/spool`                              |
    | `/usr`           | **Recursos de usuario**: binarios no esenciales, bibliotecas, documentación, headers.               | `/usr/bin`, `/usr/lib`, `/usr/share`, `/usr/include`                            |
    | `/usr/bin`       | Binarios de usuario (la mayoría de comandos no críticos).                                            | `git`, `curl`, `python3`, `nano`, `docker`                                      |
    | `/usr/sbin`      | Binarios administrativos no esenciales.                                                              | `httpd`, `sshd`, `crond`                                                         |
    | `/usr/local`     | Software instalado localmente (por el admin, no por gestor de paquetes).                             | `/usr/local/bin`, `/usr/local/etc`                                               |
    | `/opt`           | Paquetes de software adicionales (aplicaciones grandes, autocontenidas).                             | `/opt/google/chrome`, `/opt/jdk`                                                 |
    | `/proc`          | **Sistema de archivos virtual**: interfaz al kernel (procesos, hardware, parámetros en tiempo real).| `/proc/cpuinfo`, `/proc/meminfo`, `/proc/1/` (PID 1)                            |
    | `/sys`           | **Sistema de archivos virtual**: información y control de dispositivos y drivers (sysfs).            | `/sys/class`, `/sys/devices`, `/sys/kernel`                                      |
    | `/dev`           | Archivos de dispositivos (nodos especiales gestionados por el kernel).                               | `stdin`, `stdout`, `sda`, `tty`, `null`, `zero`                                 |
    | `/run`           | Datos volátiles en tiempo de ejecución (PID files, sockets, locks — persisten solo mientras corre). | `/run/docker.sock`, `/run/lock`, `/run/user/1000`                               |
    | `/mnt`           | Punto de montaje temporal para sistemas de archivos (uso manual).                                    | `/mnt/usb`, `/mnt/nfs`                                                           |
    | `/media`         | Puntos de montaje para medios extraíbles (gestionado automáticamente por el sistema).                | `/media/user/USB_DRIVE`, `/media/cdrom`                                          |
    | `/lib` y `/lib64`| **Bibliotecas compartidas esenciales** necesarias para ejecutar binarios en `/bin` y `/sbin`.        | `libc.so`, `ld-musl-x86_64.so.1` (en Alpine), módulos del kernel                 |

    ---



=== "📁 Guía práctica por directorio"

    | Directorio | Contenido clave | Comandos útiles | ¿Modificar? |
    |-----------|-----------------|-----------------|-------------|
    | **`/bin`** | Comandos esenciales: `ls`, `cp`, `mv`, `bash` | `ls /bin \| head -5` | ❌ Solo el sistema |
    | **`/sbin`** | Comandos de administración: `iptables`, `fdisk` | `ls /sbin \| grep user` | ❌ Solo root |
    | **`/etc`** | 🔐 **Configuraciones del sistema**<br>• `/etc/passwd` → usuarios<br>• `/etc/hosts` → DNS local<br>• `/etc/apt/` → repositorios | `cat /etc/os-release`<br>`nano /etc/hostname` *(con cuidado)* | ⚠️ Solo si sabes lo que haces |
    | **`/home`** | Carpetas personales de usuarios **no-root**<br>• `/home/ronald/` | `ls /home` | ✅ Seguro (si tienes permisos) |
    | **`/root`** | 🏠 **Directorio *home* del usuario `root`**<br>⚠️ No es lo mismo que `/` | `ls -la /root` | ✅ Solo accesible por `root` |
    | **`/tmp`** | Archivos temporales (se limpian al reiniciar) | `touch /tmp/prueba.txt` | ✅ Totalmente seguro |
    | **`/var`** | Datos variables<br>• `/var/log/` → logs del sistema<br>• `/var/www/` → sitios web (Apache/Nginx) | `tail -f /var/log/syslog`<br>`du -sh /var/log` | ⚠️ Leer: sí. Borrar logs: con criterio. |
    | **`/usr`** | Software instalado por el usuario/sistema<br>• `/usr/bin/` → comandos no esenciales (`curl`, `nano`)<br>• `/usr/local/` → programas compilados manualmente | `which curl` → `/usr/bin/curl` | ❌ Mejor usar `apt` |
    | **`/proc`** | 🖥️ Información *virtual* del kernel y procesos (¡no es disco!) | `cat /proc/cpuinfo`<br>`ls /proc/1` → proceso init | ❌ Solo lectura (casi siempre) |
    | **`/sys`** | Configuración del hardware y drivers | `ls /sys/class/net/` → interfaces de red | ❌ Solo para expertos |

    ---


=== " 📁 Notas practicas sobre la estructura"

    | Directorio | Descripción | Contenido típico | ¿Lectura/Escritura? | Notas prácticas |
    |-----------|-------------|------------------|---------------------|-----------------|
    | **`/`** | **Directorio raíz** — punto de partida de todo el sistema de archivos. | Subdirectorios esenciales (`bin`, `etc`, `home`, etc.). | ✅ Lectura<br>⛔ Escritura directa | Nunca almacenes archivos aquí. Solo contiene subdirectorios. |
    | **`/bin`** | **Binarios esenciales** necesarios en modo monousuario y para todos los usuarios. | `bash`, `ls`, `cp`, `mv`, `rm`, `cat`, `echo`, `ps`, `kill` | ✅ Lectura<br>⛔ Escritura | Parte crítica del sistema. No se modifica manualmente. |
    | **`/boot`** | Archivos estáticos requeridos para **arrancar el sistema** (bootloader y kernel). | `vmlinuz-*` (kernel), `initrd.img-*`, `grub/` | ✅ Lectura<br>⚠️ Escritura (solo con conocimiento) | Daño aquí puede impedir el arranque. En Docker: generalmente vacío o minimal. |
    | **`/dev`** | **Dispositivos** representados como archivos (interfaz con el kernel). | `sda`, `tty`, `null`, `zero`, `stderr`, `stdin`, `stdout` | ✅ Lectura/Escritura (según dispositivo) | Ej: `echo "hola" > /dev/null` descarta salida. Clave para I/O en scripts. |
    | **`/etc`** | 🛠️ **Archivos de configuración del sistema y aplicaciones** (texto plano). | `passwd`, `shadow`, `hosts`, `fstab`, `nginx/`, `ssh/`, `systemd/` | ✅ Lectura<br>⚠️ Escritura (con respaldo) | Directorio más modificado por administradores. Usa `sudo` y haz copias (`*.bak`). |
    | **`/home`** | 🏠 **Directorios personales de usuarios no-root**. | `/home/ronald/`, `/home/diana/`, con `Documents/`, `.bashrc`, `.ssh/`, etc. | ✅ Lectura/Escritura (por el dueño) | Zona segura para trabajo personal. En Docker: no existe por defecto (se crea si se añade usuario). |
    | **`/lib`**, **`/lib64`** | **Bibliotecas compartidas** necesarias para ejecutar binarios en `/bin` y `/sbin`. | `libc.so`, `ld-linux-x86-64.so`, módulos del kernel | ✅ Lectura<br>⛔ Escritura | Requeridas al inicio. No se modifican manualmente. `/lib64` es para sistemas de 64 bits. |
    | **`/media`** | Punto de montaje para **dispositivos extraíbles** (USB, CD, etc.). | `/media/usb-stick/`, `/media/cdrom/` | ✅ Lectura/Escritura (según permisos) | Usado por entornos gráficos (GNOME, KDE) para montaje automático. |
    | **`/mnt`** | Punto de montaje **temporal/manual** para filesystems. | `/mnt/data/`, `/mnt/backup/` | ✅ Lectura/Escritura | Recomendado para montajes administrativos (ej: `mount /dev/sdb1 /mnt/backup`). |
    | **`/opt`** | 📦 **Software de terceros** (paquetes grandes, autónomos, no gestionados por `apt`). | `/opt/slack/`, `/opt/google/chrome/`, `/opt/jdk/` | ✅ Lectura/Escritura (por admin) | Ideal para apps comerciales o .tar.gz autocontenidos. |
    | **`/proc`** | 🖥️ **Sistema de archivos virtual** — interfaz en tiempo real con el **kernel y procesos**. | Directorios numéricos por PID (`/proc/1/`), `cpuinfo`, `meminfo`, `version` | ✅ Lectura (casi todo)<br>⚠️ Escritura (solo algunos archivos) | `cat /proc/cpuinfo` → info de CPU. No ocupa espacio en disco. |
    | **`/root`** | 🏠 **Directorio *home* del usuario `root`**. | `.bashrc`, `.profile`, scripts de admin | ✅ Lectura/Escritura (solo por `root`) | No es lo mismo que `/`. Permisos: `drwx------` (solo root). |
    | **`/run`** | 📡 Datos volátiles de **procesos en ejecución** (creado al arrancar). | `utmp`, `docker.pid`, `systemd/`, sockets, lock files | ✅ Lectura/Escritura (por procesos) | Reemplaza a `/var/run`. Se limpia al reiniciar. |
    | **`/sbin`** | **Binarios de sistema** — comandos esenciales para administración (solo root). | `fdisk`, `iptables`, `reboot`, `shutdown`, `useradd`, `mkfs` | ✅ Lectura<br>⚠️ Ejecución (con privilegios) | Muchos comandos ahora están en `/usr/sbin` (ej: en Ubuntu). |
    | **`/srv`** | 🌐 **Datos servidos por el sistema** (contenido específico de servicios). | `/srv/www/`, `/srv/ftp/`, `/srv/git/` | ✅ Lectura/Escritura (según servicio) | Estándar para contenido de apps (mejor que `/var/www` en entornos personalizados). |
    | **`/sys`** | ⚙️ **Sistema de archivos virtual** — exposición de **dispositivos, drivers y parámetros del kernel**. | `class/`, `devices/`, `kernel/`, `firmware/` | ✅ Lectura<br>⚠️ Escritura (muy limitada) | Usado por `udev` y herramientas de bajo nivel. No para usuarios comunes. |
    | **`/tmp`** | 🗑️ **Archivos temporales** — accesibles por todos los usuarios. | Archivos efímeros, descargas, caches de sesión | ✅ Lectura/Escritura | Se limpia al reiniciar (en sistemas reales). En Docker: persiste mientras el contenedor viva. |
    | **`/usr`** | 📦 **Software no esencial, pero importante** — recursos compartidos del sistema. | `bin/`, `sbin/`, `lib/`, `share/`, `local/`, `include/` | ✅ Lectura<br>⛔ Escritura directa | Contiene la mayoría de comandos (`/usr/bin/curl`). **No es "usuario"** (viene de *Unix System Resources*). |
    | **`/var`** | 📈 **Datos variables** — archivos que cambian con frecuencia durante la operación. | `log/`, `lib/`, `www/`, `spool/`, `cache/`, `mail/` | ✅ Lectura/Escritura (según subdirectorio) | `/var/log/` es clave para monitoreo. En Docker: logs suelen ir a `stdout`, no aquí. |

---


???+ info "Notas clave para entornos Docker y Alpine"
    - En contenedores, muchos directorios (`/home`, `/mnt`, `/media`) suelen estar **vacíos o ausentes**, ya que no hay usuarios interactivos ni dispositivos físicos.
    - Alpine usa `musl` en lugar de `glibc`, por lo que las bibliotecas en `/lib` difieren (ej: `ld-musl-*.so.1`).
    - El directorio `/etc` es **crítico**: es donde se configuran servicios, repositorios (`/etc/apk/repositories`) y permisos.
    - `/tmp` y `/run` son **efímeros**: su contenido se pierde al detener el contenedor (a menos que se monte un volumen).
    - No existe `/usr/lib/systemd/` en Alpine base: no incluye `systemd` (usa `openrc` o procesos ligeros como `s6`/`runit` en variantes).

    > ✅ Comando útil para explorar:  
    > ```bash
    > docker run --rm -it alpine:3.20 ls -l /
    > ```



???+ warning "Consejos entorno (Docker/WSL)"
    - En **Docker**, los directorios como `/proc`, `/sys`, `/dev` están virtualizados (pero limitados por seguridad).
    - `/etc` y `/var/log` son los más útiles para **configurar y depurar** contenedores.
    - Para persistir datos: usa **volúmenes** (`-v`) en lugar de escribir en `/` del contenedor.
    - Evita trabajar como `root` en producción: crea un usuario no-root y usa `/home/usuario`.

