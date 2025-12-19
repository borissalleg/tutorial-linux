# 👤 **Usuarios en Linux: Tipos, Estructura y Operaciones**  
*Basado en estándares POSIX y FHS — para administración segura y eficaz*

---

## 👤 ¿Qué es un usuario en Linux?

Un **usuario** es una identidad lógica que permite:

- ✅ **Autenticación** (¿quién eres?)  
- ✅ **Autorización** (¿qué puedes hacer?)  
- ✅ **Aislamiento** (tus archivos/procesos no interfieren con otros)  
- ✅ **Auditoría** (¿quién ejecutó qué, cuándo?)

Cada usuario tiene:

- Un **UID** (*User ID*): número único (ej: `root` → `0`, `ronald` → `1000`)  
- Un **nombre de usuario** (*login*): cadena legible (ej: `ronald`)  
- Un **grupo primario** (GID) y grupos secundarios  
- Un **directorio home** (ej: `/home/ronald`)  
- Una **shell** por defecto (ej: `/bin/bash`)

> 🔐 **Principio clave**:  
> Todo proceso en Linux **siempre se ejecuta bajo un usuario**.  
> Incluso los servicios (`nginx`, `dockerd`) corren como usuarios no-root por seguridad.

---

## 🧾 **Tabla: Usuarios Principales y Especiales en Linux**

| Usuario | UID | Descripción | Directorio home | Shell por defecto | ¿Accesible? | Notas prácticas |
|--------|-----|-------------|-----------------|-------------------|-------------|-----------------|
| **`root`** | `0` | 🛡️ **Superusuario** — acceso total al sistema. | `/root` | `/bin/bash` (o `/bin/sh`) | ✅ Sí (con contraseña o `sudo`) | - Único UID con todos los permisos.<br>- En Docker: **usuario por defecto**.<br>- Evita usarlo directamente en producción. |
| **`daemon`** | `1` o `2` | Servicios heredados (como `syslogd`). | `/usr/sbin` o `/` | `/usr/sbin/nologin` | ❌ No (shell restringida) | Casi obsoleto. Reemplazado por usuarios específicos (ej: `systemd-resolve`). |
| **`bin`**, **`sys`**, **`sync`** | `1`–`99` | Usuarios históricos para binarios y operaciones del sistema. | `/` o `n/a` | `/usr/sbin/nologin` | ❌ No | Solo por compatibilidad. No usados en sistemas modernos. |
| **`nobody`** | `65534` (o `-2`) | 🕵️ Usuario sin privilegios — usado para **procesos aislados**. | `/nonexistent` | `/usr/sbin/nologin` | ❌ No | - Ideal para servicios que no necesitan acceder a nada.<br>- Ej: contenedores sin usuario explícito. |
| **`systemd-*`**, **`_apt`**, **`messagebus`**, **`www-data`**, **`mysql`**, **`postgres`** | `100`–`999` | 👥 **Usuarios de sistema** — creados automáticamente al instalar paquetes. | `/var/lib/<servicio>` o `n/a` | `/usr/sbin/nologin` | ❌ No | - `www-data`: servidor web (Apache/Nginx)<br>- `_apt`: actualizaciones de paquetes<br>- Nunca inician sesión. |
| **`ronald`**, **`diana`**, etc. | ≥ `1000` | 👤 **Usuarios humanos regulares** — creados manualmente o en instalación. | `/home/ronald` | `/bin/bash` | ✅ Sí | - Primero: UID `1000`, luego `1001`, etc.<br>- En WSL: tu usuario predeterminado tiene UID `1000`. |

> 💡 **¿Cómo ver todos los usuarios?**  
> ```

> **cat /etc/passwd** 

>  # lista completa (formato: usuario:x:UID:GID:desc:home:shell)
> 
> **getent passwd**          

>  # más portable (incluye LDAP/AD si aplica)
> 
> **awk -F: '$3 >= 1000 && $3 != 65534 {print $1}' /etc/passwd**  

>  # solo usuarios humanos
> ```

---

## 🔧 **Operaciones con Usuarios: Comandos y Flujos**

| Operación | Comando(s) | Requiere `root`? | Ejemplo práctico | Notas |
|----------|------------|------------------|------------------|-------|
| **Listar usuarios** | `cat /etc/passwd`, `getent passwd`, `compgen -u` | ❌ No | `getent passwd \| cut -d: -f1` | `/etc/passwd` es legible por todos (¡pero sin contraseñas!). |
| **Crear usuario** | `useradd`, `adduser` | ✅ Sí | `useradd -m -s /bin/bash ronald`<br>`adduser ronald` (interactivo, más amigable) | `-m`: crea `/home/ronald`<br>`-s`: especifica shell<br>`adduser` configura más cosas (grupos, contraseña, etc.). |
| **Establecer/actualizar contraseña** | `passwd` | ✅ Sí (para otros)<br>❌ No (para ti mismo) | `passwd ronald` | Contraseñas se almacenan en `/etc/shadow` (solo root puede leer). |
| **Modificar usuario** | `usermod` | ✅ Sí | `usermod -aG sudo ronald` → añadir a grupo `sudo`<br>`usermod -d /new/home ronald` → cambiar home | `-aG`: **añadir** a grupo (sin `-a`, *reemplaza* grupos). |
| **Bloquear/desbloquear cuenta** | `passwd -l`, `passwd -u` | ✅ Sí | `passwd -l ronald` → bloquea (`!` en `/etc/shadow`)<br>`passwd -u ronald` → desbloquea | Útil para desactivar temporalmente. |
| **Eliminar usuario** | `userdel` | ✅ Sí | `userdel -r ronald` → borra usuario **y** su `/home` | ⚠️ `-r` es crítico: sin él, `/home/ronald` queda huérfano. |
| **Cambiar identidad temporalmente** | `su`, `sudo`, `sudo -u` | ✅ `su` y `sudo` requieren permisos | `su - ronald` → cambia a `ronald`<br>`sudo -u www-data whoami` → ejecuta como `www-data` | `su -` (con guion) carga el entorno completo del usuario. |
| **Ver usuario actual** | `whoami`, `id`, `echo $USER` | ❌ No | `id ronald` → muestra UID, GID y grupos | `id` es el más completo. |
| **Ver quién está conectado** | `who`, `w`, `users` | ❌ No | `w` → muestra usuarios + procesos en ejecución | Útil en servidores compartidos. |

---

## 🔐 **Grupos: El otro pilar de permisos**

Cada usuario pertenece a:
- **1 grupo primario** (por defecto, un grupo con su mismo nombre)  
- **0 o más grupos secundarios**

| Comando | Descripción |
|--------|-------------|
| `groups ronald` | Muestra grupos de `ronald` |
| `id ronald` | Muestra UID, GID y todos los grupos |
| `groupadd devops` | Crea grupo `devops` |
| `usermod -aG devops ronald` | Añade a `ronald` al grupo `devops` |
| `newgrp devops` | Cambia temporalmente al grupo `devops` (para crear archivos con ese GID) |

> 🌟 **Ejemplo clave**:  
> En Ubuntu, los usuarios en el grupo `sudo` pueden ejecutar `sudo comando`.  
> En Debian, es el grupo `sudo` o `adm`.

---

## 🐳** Usuarios en Docker: Buenas prácticas**

| Escenario | Recomendación | Ejemplo en `Dockerfile` |
|----------|---------------|--------------------------|
| **Desarrollo (tú)** | Está bien usar `root` (por comodidad) | `FROM ubuntu:22.04` → ya eres `root` |
| **Producción (equipo)** | ⚠️ **Evita `root`** → usa usuario no-root | ```Dockerfile<br>RUN useradd -m appuser<br>USER appuser<br>WORKDIR /home/appuser<br>CMD ["./app"]``` |
| **Compartir archivos con Windows** | Asegura que UID/GID coincidan con tu WSL/host | ```Dockerfile<br>ARG UID=1000<br>ARG GID=1000<br>RUN groupadd -g $GID appgroup && \<br>    useradd -u $UID -g $GID -m appuser``` |

> ✅ Ventaja de usuario no-root en Docker:  
> - Si hay una vulnerabilidad, el atacante **no tiene acceso total** al contenedor.  
> - Evita crear archivos en volúmenes como `root` (problemas de permisos en Windows/WSL).

---

## ⚠️ **Riesgos comunes y cómo evitarlos**

| Riesgo | Causa | Solución |
|-------|-------|----------|
| **Archivos creados como `root` en volúmenes** | Ejecutar contenedor como `root` y escribir en `-v C:\datos:/app` | Usa `USER no-root` en `Dockerfile` o `--user 1000:1000` en `docker run` |
| **Contraseñas débiles o expuestas** | `passwd` con claves simples | Usa `pwgen` o políticas de contraseñas (`/etc/pam.d/common-password`) |
| **Permisos excesivos en `/home`** | `chmod 777 /home/ronald` | Usa `chmod 750` o `700` (solo dueño) |
| **Usuario `root` en producción** | Comodidad inicial | Automatiza con `Dockerfile` y CI/CD |

---

## 🧪 Ejercicio práctico en Docker (5 minutos)

```bash
# 1. Inicia contenedor como root
docker run -it --name usuarios ubuntu:22.04 /bin/bash

# 2. Dentro del contenedor:
grep "1000" /etc/passwd      # → ¿hay usuarios humanos? (en Ubuntu: no, hasta que los crees)
useradd -m -s /bin/bash ronald
passwd ronald                # pon una contraseña sencilla, ej: "1234"
su - ronald                  # cambia a ronald
whoami                       # → ronald
pwd                          # → /home/ronald
exit                         # vuelve a root
id ronald                    # → UID=1000, GID=1000, grupos=1000(ronald)

# 3. Sal y limpia
exit
docker rm -f usuarios

```


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

