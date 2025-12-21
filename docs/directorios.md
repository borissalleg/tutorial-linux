# Comandos Básicos para Directorios en Linux + Práctica para Principiantes

Esta guía introduce los comandos esenciales del sistema de archivos en Linux y propone una práctica progresiva dentro de un contenedor Alpine —ideal para quienes inician en entornos Unix-like o Docker.

![estrucutra](https://hugorc.es/wp-content/uploads/linux.png)

 📁**¿Qué es un directorio en Linux?** 
> Un **directorio** es una *estructura lógica* que organiza archivos y otros directorios, similar a una **carpeta** en otros sistemas. Pero en Linux, **todo es un archivo o un directorio**, incluso dispositivos (`/dev/sda`), procesos (`/proc/123`) y configuraciones (`/etc/passwd`).  
> La raíz de esta jerarquía es `/` — el único directorio que no tiene padre.

La estructura de directorios en Linux es una organización jerárquica en forma de árbol que comienza en el directorio raíz (
/), de donde se ramifican todos los demás archivos y carpetas, facilitando la localización y gestión de datos, donde incluso dispositivos y programas se tratan como archivos, con directorios clave como /bin (comandos básicos), /etc (configuración), /home (usuarios) y /var (datos variables). 



**Características principales**

**1. Jerarquía de árbol:** Todo emana del directorio raíz (/), creando una estructura lógica y  rdenada.

**2. Todo es un archivo:** Incluye archivos normales, directorios, dispositivos (como discos en /dev), y hasta procesos del sistema.

**3. Rutas:** Se usan rutas absolutas (desde /) o relativas para navegar; .. es el padre, y . es el directorio actual.

**4. Estándar FHS (Filesystem Hierarchy Standard):** Define la ubicación y propósito de los directorios principales para asegurar la portabilidad.


> ✅ **Ventaja del entorno contenerizado**: Puedes equivocarte sin riesgo. Todo se descarta al final.

---

???+ info "Trabajando con Directorios"
    === "Parte 1: Comandos Básicos" 

        | Comando | Descripción | Ejemplo de uso |
        |--------|-------------|----------------|
        | `pwd` | **P**rint **W**orking **D**irectory: muestra la ruta actual. | `pwd` → `/home/user` |
        | `ls` | **L**i**s**t: lista contenido de un directorio. | `ls`, `ls -l` (detallado), `ls -a` (incluye ocultos) |
        | `cd` | **C**hange **D**irectory: navega entre directorios. | `cd /etc`, `cd ..` (subir), `cd ~` (a home), `cd -` (volver al anterior) |
        | `mkdir` | **M**a**k**e **dir**ectory: crea un directorio. | `mkdir documentos`, `mkdir -p a/b/c` (crea rutas anidadas) |
        | `rmdir` | **R**e**m**ove **dir**ectory: elimina directorios **vacíos**. | `rmdir vacio` |
        | `rm -r` | Elimina directorios **con contenido** (¡cuidado!). | `rm -r carpeta/` |
        | `cp -r` | **C**o**p**y: copia directorios recursivamente. | `cp -r origen/ destino/` |
        | `mv` | **M**o**v**e / renombra archivos y directorios. | `mv viejo/ nuevo/`, `mv archivo.txt docs/` |
        | `tree` | Muestra estructura de directorios en árbol *(no incluido en Alpine base)*. | `apk add tree && tree` |

        > ⚠️ **Notas clave**:  
        > - En Linux, las rutas usan `/`, no `\`.  
        > - Los nombres son **sensibles a mayúsculas**: `Documentos ≠ documentos`.  
        > - `.` = directorio actual, `..` = directorio padre.

    === "Parte 2: Práctica — Exploración y Navegación en Alpine"

        > **Duración estimada**: 20 minutos  
        > **Nivel**: Principiante absoluto  
        > **Objetivo**: Dominar `ls`, `pwd`, `cd`, `mkdir`, `rmdir` en un entorno 100% seguro.

        ---

        === "▶ Iniciar el entorno común"

            ```bash
            docker run -it --rm --name lab-linux alpine:3.20 /bin/sh
            ```

            > 💡 **¿Qué ves?**  
            > Un prompt como `/ #` → estás en la **raíz del sistema**, como superusuario.  
            > > 🐳 *Docker Tip*: Si cometes un error, `exit` lo borra todo. ¡Sin daños colaterales!*

        === "▶ Listar directorios con `ls`"

            #### ▶️ `ls` — Vistazo al sistema
            ```bash
            ls
            ```
            **¿Qué verás?**
            ```
            bin  dev  etc  home  lib  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var
            ```
            > 🧠 **En tu mente**: Piensa en `/` como el *lobby* de un edificio. Cada nombre es una puerta a una zona diferente.

            #### ▶️ `ls -F` — Clasifica lo que ves
            ```bash
            ls -F
            ```
            **¿Qué cambia?**  
            Ahora verás `/` al final de cada nombre → ¡todos son *directorios*!  
            > ✅ `archivo.txt` → sin símbolo  
            > ✅ `script.sh*` → ejecutable  
            > ❌ No hay `*` ni `@` aquí → Alpine es minimalista.

            #### ▶️ `ls -a` — Revela lo oculto
            ```bash
            ls -a
            ```
            **Nuevo contenido**: `.`, `..`  
            > 🧠 **En tu mente**:  
            > - `.` = "esta habitación"  
            > - `..` = "el pasillo que me trajo aquí"  

            #### ▶️ `ls -l` — Los detalles importan
            ```bash
            ls -l
            ```
            **Ejemplo de línea**:
            ```
            drwxr-xr-x    2 root     root          4096 May 10 12:34 bin
            ```
            > 🔍 **Desglose**:  
            > `d` = directorio | `rwxr-xr-x` = permisos | `root root` = dueño/grupo | `4096` = tamaño | `bin` = nombre  
            > ❌ **Error típico**: Leer `rwxr-xr-x` como "todos pueden escribir" → ¡no! Solo el dueño (`root`) tiene `w`.

            #### ▶️ Explorar subdirectorios
            ```bash
            ls /bin
            ls /usr
            ```
            > 🐳 *Docker Tip*: `/bin` es pequeño porque Alpine usa `busybox` (1 binario → 100 comandos).

            === "📌 Reflexión guiada"
                - ¿Por qué `/proc` muestra tamaño `0`? → Es una "ventana" al kernel, no al disco.  
                - ¿Por qué no hay usuarios en `/home`? → No se ha creado ninguno; este contenedor es efímero.

        === "▶  Navegar y crear directorios"

            #### ▶️ `pwd` — ¿Dónde estoy?
            ```bash
            pwd
            # → /
            ```

            #### ▶️ `cd` — Moverse con confianza
            ```bash
            cd /home
            pwd          # → /home
            cd /etc
            cd -         # ¡salto mágico!
            pwd          # → /home
            cd ..        # sube al padre (/)
            ```

            > ❌ **Error típico**: `cd home` (sin `/`) → falla si no estás en `/`.  
            > ✅ **Solución**: Usa rutas absolutas (`/home`) o relativas (`./home`) según tu ubicación.

            #### ▶️ `mkdir -p` — Construye rutas completas
            ```bash
            mkdir -p taller/{docs,src,tests}
            ls -F taller
            # → docs/  src/  tests/
            ```
            > 🧠 **En tu mente**: `-p` es como decir: *"construye todos los pisos del edificio, incluso si faltan los cimientos"*.

            #### ▶️ Rutas relativas en acción
            ```bash
            cd taller/src
            touch main.py
            cd ..        # → taller
            cd docs      # → taller/docs
            pwd          # → /taller/docs
            ```

            #### ▶️ Eliminar con intención
            ```bash
            rmdir docs   # ✅ solo si está vacío
            rm -r src    # ✅ borra src + main.py
            ```
            > ⚠️ **¡Nunca olvides esto!**  
            > `rm -rf ./` (con espacio: `rm -rf . /`) → ¡borra TODO!  
            > ✅ **Buen hábito**: `ls` antes de `rm`, y usa `rm -i` en tu máquina real.

            === "🎯 Reto integrador"
                > Crea `/sandbox/linux/basics`, entra, crea `ejemplo.md`, lista con ruta absoluta — en ≤ 5 comandos.

                **Solución (con explicación)**:
                ```bash
                mkdir -p /sandbox/linux/basics   # ▶️ construye toda la ruta
                cd $_                             # ▶️ $_ = último argumento → /sandbox/linux/basics
                touch ejemplo.md                 # ▶️ crea archivo vacío
                ls -l "$(pwd)"                   # ▶️ lista el directorio actual (explícito y seguro)
                ```

                > 💡 Bonus: `$_` y `"$(pwd)"` son trucos profesionales. ¡Guárdalos para más adelante!

        === "🚪 Salir y repetir"
            ```bash
            exit
            ```
            > ✅ El contenedor desaparece. ¿Quieres repetir?  
            > ➡️ **Siguiente paso**: Prueba los mismos comandos en tu máquina (¡con precaución!) o pasa a la lección de **gestión de archivos** (`touch`, `cp`, `mv`, `cat`).    