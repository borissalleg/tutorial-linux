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


Cada usuario tiene:
    - **UID** (*User ID*): número único (`root` = `0`, tú = `1000+`)  
    - **Nombre** (`ronald`)  
    - **Grupo primario** + grupos secundarios  
    - **Directorio home** (`/home/ronald`)  
    - **Shell** (`/bin/bash`)

> 🧠 **En tu mente**:  
    > Piensa en el usuario como una **credencial de trabajo**:  
    > - El carné (UID) abre puertas específicas.  
    > - La tarjeta magnética (grupo) da acceso a áreas compartidas.  
    > - Sin credencial, ni siquiera entras al edificio.
---


=== "▶ Usuarios en Linux: Tipos, Estructura y Operaciones"

    > 📁 *Basado en estándares POSIX y FHS — para administración segura y eficaz*

    ---

    === "👥 Tipos de usuarios"

        | Tipo | UID | Ejemplos | ¿Puede iniciar sesión? | Propósito |
        |------|-----|----------|------------------------|-----------|
        | **Superusuario** | `0` | `root` | ✅ Sí | Control total del sistema |
        | **Sistema** | `1`–`999` | `www-data`, `mysql`, `systemd-resolve` | ❌ No | Ejecutar servicios (seguros y aislados) |
        | **Humano** | `≥1000` | `ronald`, `diana` | ✅ Sí | Usuarios reales |

        > 💡 **¿Cómo listarlos?**
        > ```bash
        > # Todos los usuarios
        > getent passwd
        > 
        > # Solo humanos (UID ≥ 1000, excluye nobody)
        > awk -F: '$3 >= 1000 && $3 != 65534 {print $1}' /etc/passwd
        > ```

        > 🗺️ **Archivo clave**: `/etc/passwd`  
        > Formato: `usuario:x:UID:GID:descripción:home:shell`  
        > > 🔒 Las contraseñas **no están aquí** → están en `/etc/shadow` (solo `root` puede leerlo).

        ## 🧾 **Tabla: Usuarios Principales y Especiales en Linux**

        | Usuario | UID | Descripción | Directorio home | Shell por defecto | ¿Accesible? | Notas prácticas |
        |--------|-----|-------------|-----------------|-------------------|-------------|-----------------|
        | **`root`** | `0` | 🛡️ **Superusuario** — acceso total al sistema. | `/root` | `/bin/bash` (o `/bin/sh`) | ✅ Sí (con contraseña o `sudo`) | - Único UID con todos los permisos.<br>- En Docker: **usuario por defecto**.<br>- Evita usarlo directamente en producción. |
        | **`daemon`** | `1` o `2` | Servicios heredados (como `syslogd`). | `/usr/sbin` o `/` | `/usr/sbin/nologin` | ❌ No (shell restringida) | Casi obsoleto. Reemplazado por usuarios específicos (ej: `systemd-resolve`). |
        | **`bin`**, **`sys`**, **`sync`** | `1`–`99` | Usuarios históricos para binarios y operaciones del sistema. | `/` o `n/a` | `/usr/sbin/nologin` | ❌ No | Solo por compatibilidad. No usados en sistemas modernos. |
        | **`nobody`** | `65534` (o `-2`) | 🕵️ Usuario sin privilegios — usado para **procesos aislados**. | `/nonexistent` | `/usr/sbin/nologin` | ❌ No | - Ideal para servicios que no necesitan acceder a nada.<br>- Ej: contenedores sin usuario explícito. |
        | **`systemd-*`**, **`_apt`**, **`messagebus`**, **`www-data`**, **`mysql`**, **`postgres`** | `100`–`999` | 👥 **Usuarios de sistema** — creados automáticamente al instalar paquetes. | `/var/lib/<servicio>` o `n/a` | `/usr/sbin/nologin` | ❌ No | - `www-data`: servidor web (Apache/Nginx)<br>- `_apt`: actualizaciones de paquetes<br>- Nunca inician sesión. |
        | **`ronald`**, **`diana`**, etc. | ≥ `1000` | 👤 **Usuarios humanos regulares** — creados manualmente o en instalación. | `/home/ronald` | `/bin/bash` | ✅ Sí | - Primero: UID `1000`, luego `1001`, etc.<br>- En WSL: tu usuario predeterminado tiene UID `1000`. |

    === "🔧 Operaciones esenciales (con práctica segura)"

        > 🐳 **Inicia tu laboratorio seguro**:
        > ```bash
        > docker run -it --rm --name lab-users ubuntu:22.04 /bin/bash
        > ```

        #### ▶ Crear un usuario
        ```bash
        useradd -m -s /bin/bash ronald   # -m: crea /home/ronald
        passwd ronald                     # asigna contraseña (ej: "1234")
        su - ronald                       # cambia a ronald
        whoami                            # → ronald
        pwd                               # → /home/ronald
        exit                              # vuelve a root
        ```

        > ❌ **Error típico**: `useradd ronald` (sin `-m`) → no hay `/home/ronald` → muchos programas fallan.  
        > ✅ **Solución**: Usa siempre `-m` para usuarios humanos.

        #### ▶ Añadir a un grupo (¡crucial para sudo!)
        ```bash
        usermod -aG sudo ronald    # -aG = "añadir a grupo" (sin -a, lo reemplaza)
        groups ronald               # → ronald sudo
        ```

        > ⚠️ Si omites `-a`, `ronald` **pierde su grupo primario** → caos de permisos.

        #### ▶ Ver identidad actual
        ```bash
        id ronald   # → uid=1000(ronald) gid=1000(ronald) groups=1000(ronald),27(sudo)
        ```

        > 🧠 **¿Por qué `gid=1000`?**  
        > Por defecto, se crea un grupo con el mismo nombre y UID/GID.

        #### ▶ Eliminar limpiamente
        ```bash
        userdel -r ronald   # -r: borra /home/ronald y correo (si existe)
        ```

        > 🐳 *Docker Tip*: Al salir (`exit`), todo se borra. ¡No hay riesgo!

    === "🔐 Grupos: El poder de la colaboración"

        Los grupos permiten dar permisos a **conjuntos de usuarios**.

        | Comando | Uso |
        |--------|-----|
        | `groups ronald` | ¿En qué grupos está `ronald`? |
        | `newgrp devops` | Cambia temporalmente al grupo `devops` (útil para crear archivos con GID correcto) |
        | `chgrp devops script.sh` | Cambia el grupo del archivo |

        > 🌟 **Ejemplo real**:  
        > - En Ubuntu, el grupo `sudo` permite usar `sudo`.  
        > - En servidores web, `www-data` y tus archivos deben compartir grupo (ej: `chgrp -R www-data /var/www` + `chmod g+w`).

    === "🐳 Usuarios en Docker: Buenas prácticas"

        | Escenario | Recomendación | Ejemplo en `Dockerfile` |
        |----------|---------------|--------------------------|
        | **Desarrollo** | OK usar `root` | `FROM alpine` → ya eres `root` |
        | **Producción** | ⚠️ **Evita `root`** | ```Dockerfile<br>RUN adduser -D appuser<br>USER appuser<br>WORKDIR /home/appuser``` |
        | **Volúmenes en Windows/WSL** | Usa `--user 1000:1000` para evitar permisos como `root` | `docker run -u 1000:1000 -v ./data:/app ...` |

        > ✅ **Ventaja de no-root**:  
        > Si hay una vulnerabilidad, el atacante **no tiene control total** del contenedor.

    === "⚠️ ¿Estoy en `/` o soy `root`? ¡No es lo mismo!"

        | Concepto | Verifica con | Prompt típico | ¿Peligroso? |
        |---------|--------------|----------------|-------------|
        | Estar en `/` | `pwd` → `/` | `user@host:/#` | ❌ No — navegar es seguro |
        | Ser `root` | `whoami` → `root` | `root@host:~#` | ⚠️ Sí — comandos como `rm -rf /` son letales |

        > 🔐 En Docker: **por defecto eres `root` y empiezas en `/`** → ideal para aprender, pero **nunca en producción**.

    === "🎯 Reto final: Diagnóstico rápido"
        Sin salir del contenedor, resuelve:
        > 1. Crea un usuario `test` con home y bash.  
        > 2. Añádelo al grupo `sudo`.  
        > 3. Verifica que tiene GID `1001` y está en dos grupos.  
        > 4. Bloquea su cuenta.  
        > 5. Confirma que `passwd` muestra `!` en `/etc/shadow`.

        **Solución sugerida**:
        ```bash
        useradd -m -s /bin/bash test
        usermod -aG sudo test
        id test                         # → gid=1001(test), groups=1001(test),27(sudo)
        passwd -l test
        grep test /etc/shadow           # → test:!...
        ```

    === "🚪 Salir y continuar"
        ```bash
        exit
        docker rm -f lab-users   # por si acaso; --rm ya lo hace, pero doble verificación
        ```
        > ✅ Todo desaparece.  
        > ➡️ **Siguiente paso**:  
        > - Combina esto con **permisos (`chmod`)** → ¿cómo afecta el usuario a lo que puede hacer?  
        > - O avanza a: **"Grupos y políticas de seguridad"** → para entornos multiusuario.