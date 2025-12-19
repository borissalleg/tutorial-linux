
# 🐧 Guía Visual: El Directorio Raíz (`/`) y el Usuario `root` en Linux  
*Para uso práctico en Docker y entornos de terminal — por Ronald Berna López*

---

## 🔑 ¿Estoy en `/` o soy `root`?  
⚠️ ¡Son dos cosas distintas! No las confundas.

| Concepto | Cómo verificarlo | Prompt típico | ¿Es peligroso? |
|---------|------------------|---------------|----------------|
| **Directorio raíz (`/`)** | `pwd` → `/` | `user@host:/#` | ❌ No por sí solo — navegar es seguro |
| **Usuario `root`** | `whoami` → `root` | `root@host:~#` | ⚠️ Sí, si ejecutas comandos destructivos sin cuidado |

> ✅ En Docker: **por defecto eres `root` y empiezas en `/`** → entorno controlado y seguro para aprender.

---

## 🗺️ Estructura del Directorio Raíz (`/`)  
Ejecuta `ls -l /` para ver esto:



### 📁 Guía práctica por directorio

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

## 🛡️ Buenas Prácticas de Seguridad

### ✅ Lo que **SÍ** puedes hacer (y debes practicar)
```bash
# Explorar sin miedo
ls -l /
tree -L 1 /               # vista en árbol (instala `tree` primero)
cat /etc/os-release       # ver versión de Linux
du -sh /var/log           # ver tamaño de logs

# Trabajar en zonas seguras
mkdir /tmp/practica
cd /tmp/practica
echo "Hola Ronald" > prueba.txt