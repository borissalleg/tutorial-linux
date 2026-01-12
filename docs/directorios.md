# **Comandos Básicos para Directorios en Linux + Práctica para Principiantes**

Esta guía introduce los comandos esenciales del sistema de archivos en Linux y propone una práctica progresiva dentro de un contenedor Alpine —ideal para quienes inician en entornos Unix-like o Docker.

<!-- ![estrucutra](https://www.servidoresadmin.com/wp-content/uploads/2020/06/carpetas-1024x680.jpg) -->

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
            ls` (**L**i**s**t) es un comando fundamental en sistemas Unix y Linux que **enumera los archivos y directorios** contenidos en una ubicación específica del sistema de archivos.

            #### 🔹 ¿Para qué sirve?
            - ✅ **Explorar la estructura de directorios**: ver qué archivos y subdirectorios existen.
            - ✅ **Inspeccionar metadatos**: permisos, propietario, tamaño, fecha de modificación (con `-l`).
            - ✅ **Identificar tipos de archivos**: directorios, ejecutables, enlaces simbólicos, etc.
            - ✅ **Depurar y auditar**: verificar presencia de archivos ocultos (como `.env`, `.git`), permisos incorrectos o cambios recientes.
            - ✅ **Base para scripting**: generar listas de archivos para procesamiento automatizado.

            Por defecto, `ls` opera sobre el **directorio actual** si no se especifica una ruta. Es uno de los primeros comandos que todo usuario de terminal debe dominar.

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




            ???+ info "📋 Opciones esenciales del comando `ls`"

                | Categoría | Opción | Descripción | Ejemplo de uso | Salida esperada (parcial) |
                |----------|--------|-------------|-----------------|----------------------------|
                | **📂 Navegación y visibilidad** | `-a`, `--all` | Muestra todos los archivos, incluidos los ocultos (que comienzan con `.`). | `ls -a` | `.`, `..`, `.bashrc`, `.ssh`, `documento.txt` |
                | | `-A`, `--almost-all` | Como `-a`, pero excluye `.` (directorio actual) y `..` (directorio padre). | `ls -A` | `.bashrc`, `.ssh`, `documento.txt` |
                | | `-d`, `--directory` | Muestra información del directorio mismo, no de su contenido. Útil con `-l`. | `ls -ld /tmp` | `drwxrwxrwt 14 root root 4096 dic 19 15:30 /tmp` |
                | | `-R`, `--recursive` | Lista el contenido recursivamente, incluyendo subdirectorios y sus archivos. | `ls -R ~/proyecto` | `~/proyecto/:<br>docs/  src/<br><br>~/proyecto/docs/:<br>README.md` |
                | **🧾 Formato y presentación** | `-l` | (**long format**) Muestra en columnas detalladas: permisos, número de enlaces, dueño, grupo, tamaño (bytes), fecha y nombre. | `ls -l` | `-rw-r--r-- 1 root root 1024 dic 19 10:30 archivo.txt` |
                | | `-h`, `--human-readable` | Con `-l`, muestra tamaños en unidades legibles: **K**, **M**, **G**. | `ls -lh` | `-rw-r--r-- 1 root root 1.2K dic 19 10:30 log.txt` |
                | | `-S` | Ordena por **tamaño descendente** (archivo más grande primero). | `ls -lS` | `archivo-grande.iso` aparece al inicio |
                | | `-t` | Ordena por **fecha de modificación** (más reciente primero). | `ls -lt` | Archivos modificados hoy aparecen arriba |
                | | `-r`, `--reverse` | Invierte el orden de la lista (útil con `-t`, `-S`). | `ls -ltr` | Archivos **más antiguos primero** |
                | | `-1` (uno) | Una entrada por línea (sin columnas). Ideal para scripts. | `ls -1 *.sh` | `script1.sh`<br>`script2.sh` |
                | **🔐 Metadatos y seguridad** | `-i`, `--inode` | Muestra el **número de inode** (identificador único del archivo en el disco). | `ls -i script.sh` | `123456 script.sh` |
                | | `-n`, `--numeric-uid-gid` | Muestra **UID y GID numéricos** en lugar de nombres. | `ls -ln` | `-rw-r--r-- 1 0 0 1024 dic 19 10:30 archivo` → `0 0` = `root root` |
                | | `--color[=WHEN]` | Colorea la salida según tipo: directorios (azul), ejecutables (verde), enlaces (cian). Valores: `auto`, `always`, `never`. | `ls --color=auto` | Directorios en azul, ejecutables en verde |
                | | `-F`, `--classify` | Añade sufijo para identificar tipos: `/` (directorio), `*` (ejecutable), `@` (enlace), `\|` (FIFO), `=` (socket). | `ls -F` | `bin/`, `script.sh*`, `enlace@` |
                | **🧩 Combinaciones comunes** | `ll` | Alias para `ls -lh` | `ll` | Lista larga con tamaños legibles |
                | | `la` | Alias para `ls -la` | `la` | Todos los archivos en formato largo |
                | | `lt` | Alias para `ls -ltr` | `lt` | Ordenado por fecha (antiguo → reciente) |
                | | `lS` | Alias para `ls -lSh` | `lS` | Ordenado por tamaño (grande → pequeño) |
                | | `ld` | Alias para `ls -ld */` | `ld` | Solo directorios |
                | | `lr` | Alias para `ls -lR` | `lr` | Lista recursiva en formato largo |

            > 💡 **Consejos prácticos**:
            > - Usa `ls -la` como primer comando al explorar un directorio desconocido.
            > - Combina `-l` con `-h`, `-t` o `-S` para análisis eficaz.
            > - En scripts, evita depender de colores; usa `-1` o `-F` para procesamiento seguro.

          

        === "▶  Navegar y crear directorios"

            === "`pwd` — ¿Dónde estoy?"
                pwd (Print Working Directory) es un comando estándar de Unix/Linux que muestra la ruta absoluta del directorio actual (también llamado directorio de trabajo o current working directory).
                🔹 ¿Para qué sirve?

                    ✅ Saber exactamente dónde estás en el árbol de directorios.
                    ✅ Depurar scripts: verificar la ubicación antes de ejecutar operaciones críticas (rm, cp, etc.).
                    ✅ Construir rutas dinámicas en scripts

                ```bash
                pwd
                # → /
                ```

                | Opción | Descripción | Ejemplo |
                |--------|-------------|---------|
                | (sin opciones) | Muestra la ruta lógica (puede incluir enlaces simbólicos). | `pwd` → `/home/ronald/docs` |
                | `-P` | (**physical**) Muestra la ruta física real, resolviendo todos los enlaces simbólicos. | Si `docs` es un enlace a `/datos/documentos`, `pwd -P` muestra `/datos/documentos` |
                | `-L` | (**logical**) Comportamiento por defecto: muestra la ruta tal como se navegó (con enlaces). | Igual que `pwd` solo |

            === "`cd` — Moverse con confianza"
                El comando para navegar entre directorios en la terminal de Linux es cd (change directory). 
                
                Es uno de los comandos más usados y esencial para moverse por el sistema de archivos.

                🔹 Sintaxis básica

                ```bash
                cd [ruta-del-directorio]
                ```

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

                | Comando | Descripción | Ejemplo de uso |
                |---------|-------------|----------------|
                | `cd` | Ir al directorio *home* del usuario actual. | `cd` → `/home/ronald` (o `/root` si eres `root`) |
                | `cd /ruta/absoluta` | Ir a una ruta completa desde la raíz (`/`). | `cd /var/log` |
                | `cd ruta/relativa` | Ir a un subdirectorio desde la ubicación actual. | Si estás en `/home`, `cd ronald` → `/home/ronald` |
                | `cd ..` | Subir un nivel (al directorio padre). | `/home/ronald/docs` → `cd ..` → `/home/ronald` |
                | `cd -` | Volver al directorio anterior (útil para alternar entre dos rutas). | `cd /etc` → `cd /var` → `cd -` → vuelve a `/etc` |
                | `cd ~` | Ir al *home* del usuario actual (equivalente a `cd`). | `cd ~` → `/home/ronald` |
                | `cd ~usuario` | Ir al *home* de otro usuario (si tienes permisos). | `cd ~diana` → `/home/diana` |

                🔹 **Rutas:** absolutas vs. relativas

                **Ruta absoluta:** comienza con / → siempre se refiere a la misma ubicación, sin importar dónde estés.
                Ejemplo
                ```bash
                cd /usr/bin
                ```
                **Ruta relativa:** no comienza con / → se interpreta desde el directorio actual.
                Ejemplo:
                ```bash
                si estás en /home/ronald, cd documentos equivale a cd /home/ronald/documentos.
                ```
                #### ▶️ Rutas relativas en acción
                ```bash
                cd taller/src
                touch main.py
                cd ..        # → taller
                cd docs      # → taller/docs
                pwd          # → /taller/docs
                ```

            === "`mkdir -p` — Construye rutas completas"

                El comando principal para crear directorios en Linux es mkdir (make directory). 
                Permite generar uno o varios directorios, incluso con estructuras anidadas complejas, en una sola instrucción.

                ```bash
                mkdir -p taller/{docs,src,tests}
                ls -F taller
                # → docs/  src/  tests/
                ```
                > 🧠 **En tu mente**: `-p` es como decir: *"construye todos los pisos del edificio, incluso si faltan los cimientos"*.

                El comando mkdir permite crear múltiples directorios en una sola línea. 
                
                Existen tres métodos principales, según la estructura deseada:
                
                1.✅ Directorios independientes (mismo nivel)

            
                ```bash
                mkdir dir1 dir2 dir3
                ```
                → Crea tres directorios hermanos en el directorio actual:  

                ```bash
                ├── dir1
                ├── dir2
                └── dir3
                ```

                2.✅ Estructura anidada (directorios dentro de directorios)

                ```bash
                mkdir -p a/b/c d/e/f
                ```

                La opción -p (parent) crea todos los directorios intermedios necesarios si no existen.
                
                Si omites -p, fallará si a o d no existen.

                ```bash
                .
                ├── a
                │   └── b
                │       └── c
                └── d
                    └── e
                        └── f
                ```

                3.✅ Combinación con expansión de llaves ({}) — muy eficiente

                ```bash
                mkdir -p proyecto/{docs,src,tests,bin}
                ```

                Resultado :

                ```bash
                proyecto/
                ├── docs
                ├── src
                ├── tests
                └── bin
                ```
                

            === "Eliminar con intención "

                En Linux, la eliminación de directorios depende de si están vacíos o contienen archivos/subdirectorios. 
                
                Se usan comandos distintos para garantizar seguridad y evitar borrados accidentales.

                🔹 1. Eliminar un directorio vacío

                Usa el comando rmdir (remove directory), diseñado exclusivamente para directorios sin contenido.

                ```bash
                rmdir nombre-del-directorio

                mkdir temporal
                rmdir temporal    # ✅ Funciona: está vacío
                ```


                ❌ Falla si hay contenido:
                ```bash

                mkdir -p prueba/archivos
                rmdir prueba      # ❌ Error: "Directory not empty"

                ```
                >💡 Ventaja de rmdir: Es seguro por diseño — nunca elimina datos sin querer.

                ```bash
                rmdir docs   # ✅ solo si está vacío
                rm -r src    # ✅ borra src + main.py
                ```
                > ⚠️ **¡Nunca olvides esto!**  
                > `rm -rf ./` (con espacio: `rm -rf . /`) → ¡borra TODO!  
                > ✅ **Buen hábito**: `ls` antes de `rm`, y usa `rm -i` en tu máquina real.


                🔹 2. Eliminar un directorio con contenido (archivos, subdirectorios, etc.)

                Usa rm con la opción recursiva -r (recursive).

                ```bash
                rm -r nombre-del-directorio
                ```

                Ejemplo:
                
                ```bash
                mkdir -p proyecto/{src,docs}
                touch proyecto/src/main.py
                rm -r proyecto    # ✅ Elimina todo: directorio + contenido
                ```
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