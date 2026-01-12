# 📄 **Archivos en Linux**
### *Fundamentos, Creación y Práctica*

<!-- ![sistema_archivo](https://es.easeus.com/images/en/screenshot/partition-manager/file-system-work-flow.png) -->

                
*Enfoque 100 % práctico utilizando Docker*

---


???+ info "Archivos en Linux"
    === " 🔍 ¿**Qué es un archivo en Linux?**"

        En Linux, **todo es un archivo** — una abstracción poderosa del sistema operativo:

        Un archivo es la unidad básica de almacenamiento en Linux. A diferencia de otros sistemas, no se distingue por extensión (.txt, .jpg), sino por su contenido y metadatos (tipo, permisos, dueño).

        > Al igual que en Windows, en Linux, los usuarios disponen de ciertos permisos o privilegios que limitan su control sobre el sistema.

        >Para saber los permisos que un usuario tiene sobre determinados directorios, no tenemos más que observar el primer atributo que aparece en cada caso al ejecutar la orden ls -l.

        >Si, además añadimos -d, y el nombre del directorio que queremos, veremos exclusivamente los permisos que tenemos sobre ese directorio. Así, si ejecutamos $ ls -ld Fotos, veremos qué permisos tenemos sobre él.

        Los tipos de permisos sobre archivos en Linux son los siguientes:

        **Lectura:** Permite fundamentalmente visualizar el contenido del archivo con órdenes como ls, cat, etc. También permite el uso de órdenes como cp.

        **Escritura:** Permite modificar el contenido del archivo. El archivo se puede editar, por ejemplo, con gedit y modificar su contenido sin ningún problema.

        **Ejecución:** Permite ejecutar el archivo como si de un programa ejecutable se tratase. Estos permisos se suelen asignar a archivos Shell, es decir, archivos que realizan funciones propias del sistema operativo, como copias de seguridad, análisis de la integridad del sistema, etc.

        ![carchivo](https://inspiretic.wordpress.com/wp-content/uploads/2016/09/imagenprincipal.png)

        | Tipo | Ejemplo | Descripción |
        |------|---------|-------------|
        | **Archivo regular** | `documento.txt`, `script.sh`, `imagen.jpg` | Datos almacenados en disco (texto, binario, etc.) |
        | **Directorio** | `/home`, `./proyecto/` | Archivo especial que contiene *enlaces a otros archivos* |
        | **Archivo de dispositivo** | `/dev/sda`, `/dev/tty` | Interfaz con hardware (bloque o carácter) |
        | **Enlace simbólico** | `mi-enlace -> /ruta/real` | "Atajo" a otro archivo (como un acceso directo) |
        | **Socket** | `/run/docker.sock` | Punto de comunicación entre procesos |
        | **Tubería (pipe) con nombre** | `/tmp/mipipe` | Comunicación unidireccional entre procesos |

        > 💡 **Concepto clave**:  
        > Un archivo no es solo "contenido", sino un **inode** (estructura de metadatos en disco) + **datos**.  
        > El nombre del archivo es solo un *enlace* al inode (por eso se permiten enlaces duros/simbólicos).

        ---
    === " 🧰 Crear archivos en Linux: 7 métodos prácticos"

        | Método | Comando | Cuándo usarlo | Ejemplo |
        |--------|---------|---------------|---------|
        | **1. `touch`** | `touch archivo.txt` | ✅ Crear archivo vacío o actualizar marca de tiempo | `touch notas.md` |
        | **2. Redirección simple** | `> archivo.txt` | ✅ Vaciar o crear archivo (¡cuidado: sobrescribe!) | `> log.txt` |
        | **3. Redirección con contenido** | `echo "hola" > archivo.txt` | ✅ Crear archivo con una línea | `echo "# Proyecto X" > README.md` |
        | **4. Editor de texto** | `nano archivo.txt` | ✅ Crear/modificar con interfaz | `nano script.sh` → escribe → `Ctrl+O` → `Ctrl+X` |
        | **5. `cat` con here-document** | `cat > archivo <<EOF`<br>`contenido`<br>`EOF` | ✅ Crear archivos multilínea sin editor | Ver práctica abajo |
        | **6. Copiar de `/dev/null`** | `cp /dev/null archivo` | ✅ Crear vacío (menos común) | `cp /dev/null temp.log` |
        | **7. `printf`** | `printf "User=%s\nPass=%s\n" ronald 123 > conf.ini` | ✅ Formato controlado (mejor que `echo` para scripts) | ✅ Ideal para configuraciones |

        > ⚠️ **Diferencia crítica**:  
        > - `>` → **sobrescribe** el archivo  
        > - `>>` → **añade** al final (append)

        ---
    === "Parte 2: Práctica — Gestión de Archivos en Alpine"

        > **Duración estimada**: 20 minutos  
        > **Nivel**: Principiante (recomendado haber hecho la lección de directorios)  
        > **Objetivo**: Crear, inspeccionar, copiar, mover y eliminar archivos con confianza.

        ---

        === "▶ Iniciar el entorno limpio"

            ```bash
            docker run -it --rm --name lab-files alpine:3.20 /bin/sh
            ```

            > 💡 **¿Qué ves?**  
            > Prompt `/ #` → raíz del sistema, como `root`.  
            > > 🐳 *Docker Tip*: Este contenedor no tiene `nano` ni `vim` completo → usaremos `vi` (mínimo) o `cat >` para crear contenido.

        === "▶ Crear y ver archivos con `touch`, `cat`, `head`, `tail`"

            #### ▶️ `touch`—Crear un archivo vacío
            ```bash
            touch notas.txt
            ls -l notas.txt
            # → -rw-r--r--    1 root     root             0 May 21 15:00 notas.txt
            ```
            > 🧠 **En tu mente**: Es como crear una hoja en blanco en una carpeta.

            #### ▶️ `cat` — Ver contenido completo
            ```bash
            cat notas.txt   # → nada (está vacío)
            ```

            #### ▶️ Crear contenido sin editor
            ```bash
            cat > saludo.txt <<EOF
            Hola, soy Ronald.
            Estoy aprendiendo Linux.
            EOF
            ```
            > 🔍 `<<EOF` = *here document*: todo hasta `EOF` se escribe en el archivo.

            ```bash
            cat saludo.txt
            # → Hola, soy Ronald.
            # → Estoy aprendiendo Linux.
            ```

            #### ▶️ `head` y `tail` — Inspección rápida
            ```bash
            head -n 1 saludo.txt   # → Hola, soy Ronald.
            tail -n 1 saludo.txt   # → Estoy aprendiendo Linux.
            ```

            > ❌ **Error típico**: `head saludo.txt` en un archivo de 2 líneas → muestra todo.  
            > ✅ **Solución**: Usa `-n N` para ser explícito.

        === "▶ Copiar, mover y renombrar con `cp` y `mv`"

            #### ▶️ `cp` — Copia segura
            ```bash
            cp saludo.txt copia_saludo.txt
            ls -l *.txt
            # → dos archivos con mismo tamaño y timestamp (pero INODE distinto)
            ```

            #### ▶️ `mv` — Renombrar o mover
            ```bash
            mv copia_saludo.txt docs/      # si docs/ existe → mueve
            mv saludo.txt bienvenida.txt   # mismo directorio → renombra
            ```

            > 🐳 *Docker Tip*: En Alpine, `mv` y `cp` son parte de `busybox` → son ligeros pero completos.

            === "📌 ¿Sabías que…?"
                - `mv` dentro del **mismo sistema de archivos** es solo un cambio de nombre (rápido, no copia datos).  
                - `mv` entre sistemas de archivos (ej. disco → USB) **sí copia y borra** (más lento).

        === "▶ Eliminar con `rm` y verificar con `file`/`stat`"

            #### ▶️ `rm` — Eliminar 
            ```bash
            rm bienvenida.txt
            ls bienvenida.txt   # → ls: bienvenida.txt: No such file or directory
            ```

            #### ▶️ `file` — ¿Qué tipo *real* es?
            ```bash
            file /bin/sh
            # → /bin/sh: symbolic link to busybox   ✅
            file /etc/passwd
            # → /etc/passwd: ASCII text              ✅
            ```

            #### ▶️ `stat` — Metadatos en profundidad
            ```bash
            stat /etc/passwd
            ```
            **Salida clave**:
            ```
            Size: 1234       Blocks: 8          IO Block: 4096   regular file
            Access: (0644/-rw-r--r--)  Uid: (    0/    root)   Gid: (    0/    root)
            Access: 2025-12-21 15:05:01.000000000
            Modify: 2025-12-21 15:00:00.000000000
            Change: 2025-12-21 15:00:00.000000000
            ```
            > 🔍 `Modify` = última edición del contenido  
            > 🔍 `Change` = último cambio de metadatos (permisos, dueño)  

        === "🎯 Reto integrador"
            > Sin salir del contenedor:  
            > 1. Crea dos archivos: `comandos.md` y `ejemplos.txt`  
            > 2. Copia `comandos.md` a `/tmp/`  
            > 3. Mueve `ejemplos.txt` a `/tmp/` y renómbralo a `ejemplos-linux.txt`  
            > 4. Verifica que ambos están en `/tmp/` con `ls -l /tmp`  
            > 5. Elimina el original `comandos.md` (no el de `/tmp/`)

            **Solución sugerida**:
            ```bash
            touch comandos.md ejemplos.txt
            cp comandos.md /tmp/
            mv ejemplos.txt /tmp/ejemplos-linux.txt
            ls -l /tmp/
            rm comandos.md
            ```

        === "⚠️ ¡Cuidado realista!"
            > - `rm *` en el directorio equivocado → desastre.  
            > - `rm -rf /` → **nunca lo ejecutes** (aunque en Docker no rompe tu PC, es un mal hábito).  
            > ✅ **Mejor práctica**:  
            > ```bash
            > alias rm='rm -i'   # en tu ~/.bashrc (no en Alpine, pero sí en tu máquina)
            > ```

        === "🚪 Salir y reflexionar"
            ```bash
            exit
            ```
            > ✅ Todo desaparece. ¿Qué aprendiste?  
            > ➡️ **Siguiente paso**: Combina directorios + archivos → **permisos y dueños** (`chmod`, `chown`), o pasa a **búsqueda con `grep` y `find`**.
        
    === "▶ Parte 3: Práctica- Permisos y Atributos en Linux"

    > 📁 **¿Por qué importan los permisos?**  
    > En Linux, **todo es un archivo** — y cada archivo tiene reglas que definen quién puede *leerlo*, *modificarlo* o *ejecutarlo*.  
    > Esto protege el sistema y tus datos, incluso cuando múltiples usuarios comparten la misma máquina.

    ![Permisos rwx](https://mural.uv.es/oshuso/p1.jpg)

    > 🔍 **Interpretación del esquema**:  
    > `rwx rwx rwx` → tres bloques de 3 caracteres:  
    > - **Primer bloque** (`rwx`): permisos del **propietario**  
    > - **Segundo bloque** (`rwx`): permisos del **grupo**  
    > - **Tercer bloque** (`rwx`): permisos de **otros** (todos los demás)

    Cada letra representa:
    
    - `r` = **lectura** → ver contenido (`cat`, `cp`, `ls`)  
    - `w` = **escritura** → modificar o borrar (`echo >`, `rm`, `mv`)  
    - `x` = **ejecución** → lanzar como programa (`./script.sh`)

    > 🧠 **En tu mente**:  
    > Piensa en los permisos como las **llaves de una caja fuerte**:  
    > - El **propietario** tiene todas las llaves.  
    > - El **grupo** tiene algunas.  
    > - Los **otros** quizás solo ven el exterior.

    ---

    === "📌 Comandos esenciales"

        | Comando | Descripción | Ejemplo |
        |--------|-------------|---------|
        | `ls -l` | Muestra permisos, dueño y grupo | `ls -l script.sh` → `-rwxr-xr-- 1 root root 0 May 21 15:00 script.sh` |
        | `ls -ld dir/` | Muestra permisos **del directorio** (no de su contenido) | `ls -ld /tmp` |
        | `chmod` | **Ch**ange **mod**e: modifica permisos | `chmod 755 script.sh`, `chmod u+x script.sh` |
        | `chown` | **Ch**ange **own**er: cambia propietario | `chown alumno archivo.txt` |
        | `chgrp` | **Ch**ange **gr**ou**p**: cambia grupo | `chgrp developers script.sh` |
        | `umask` | Define permisos **por defecto** al crear archivos | `umask 0022` → archivos nuevos: `644`, directorios: `755` |

        > 💡 **Modos de `chmod`**:
        > - **Numérico (octal)**: `chmod 755 archivo`  
        >   - `7` = `r+w+x` = 4+2+1  
        >   - `5` = `r+ x` = 4+0+1  
        > - **Simbólico**: `chmod u+x,g-w,o=r archivo`  
        >   - `u` = user (propietario), `g` = group, `o` = others, `a` = all  
        >   - `+` = añadir, `-` = quitar, `=` = asignar exactamente

    === "▶ Práctica segura en Alpine"

        > **Objetivo**: Entender cómo se asignan y modifican permisos en un entorno sin riesgo.

        #### ▶ Iniciar entorno
        ```bash
        docker run -it --rm --name lab-perms alpine:3.20 /bin/sh
        ```

        > ⚠️ Alpine no incluye `gedit` ni entorno gráfico → usaremos comandos en terminal.

        #### ▶ Crear archivos y observar permisos por defecto
        ```bash
        touch archivo.txt
        mkdir carpeta
        ls -l archivo.txt
        ls -ld carpeta
        ```
        **Resultados típicos**:
        ```
        -rw-r--r--    1 root     root             0 May 21 15:30 archivo.txt
        drwxr-xr-x    2 root     root          4096 May 21 15:30 carpeta
        ```

        > 🧠 **¿Por qué la diferencia?**  
        > - Archivos nuevos: **sin `x` por seguridad** (evita ejecutar basura accidentalmente).  
        > - Directorios nuevos: **con `x` obligatorio** → sin `x`, no puedes entrar (`cd`) ni listar (`ls`) su contenido.

        #### ▶ Modificar permisos con `chmod`
        ```bash
        # Añadir ejecución al propietario
        chmod u+x archivo.txt
        ls -l archivo.txt
        # → -rwxr--r--

        # Usar modo octal: propietario=rwx, grupo=r-x, otros=r--
        chmod 754 archivo.txt
        ls -l archivo.txt
        # → -rwxr-xr--
        ```

        > ❌ **Error típico**: `chmod 777 archivo` "para que funcione" → **mala práctica** (riesgo de seguridad).  
        > ✅ **Mejor**: Otorga solo lo necesario (`644` para texto, `755` para scripts).

        #### ▶ Cambiar propietario y grupo
        ```bash
        # En Alpine, crea un usuario de prueba
        adduser -D alumno
        chown alumno archivo.txt
        chgrp users archivo.txt    # si el grupo existe (puedes crearlo con addgroup)
        ls -l archivo.txt
        # → -rwxr-xr--    1 alumno   users            0 May 21 15:35 archivo.txt
        ```

        > 🐳 *Docker Tip*: Los cambios no persisten, pero la práctica es 100% realista.

    === "🎯 Reto: Resolver la tabla de actividades (adaptado)"

        Basado en las [actividades de la UV](https://mural.uv.es/oshuso/8339_permisos_y_atributos.html), aquí va una versión ejecutable en tu contenedor:

        ```bash
        # Actividad 4: Permitir ejecución al propietario y grupo
        touch texto.txt
        chmod ug+x texto.txt
        ls -l texto.txt   # → debe mostrar -rwxr-xr--

        # Actividad 5: Quitar ejecución y escritura a todos
        chmod a-wx texto.txt
        ls -l texto.txt   # → -r--r--r--

        # Actividad 7: 764 = rwx (7) para dueño, rw- (6) para grupo, r-- (4) para otros
        chmod 764 texto.txt
        ls -l texto.txt   # → -rwxrw-r--
        ```

        > ✅ Verifica cada paso con `ls -l`.

    === "📋 Tabla resumen: comandos y efectos"

        | Comando | Efecto |
        |--------|--------|
        | `chmod g+x doc1` | Añade permiso de **ejecución** al **grupo** |
        | `chmod a=rwx doc1` | Asigna `rwx` a **todos** (equivalente a `chmod 777`) |
        | `chmod go-wx doc1` | Quita **escritura y ejecución** a **grupo y otros** |
        | `chmod a+x doc1` | Añade **ejecución** a **todos** |
        | `chmod ugo+x doc1` | Igual que arriba (`ugo` = `a`) |
        | `chmod a= doc1` | Quita **todos los permisos** a todos |
        | `chmod 764 doc1` | Dueño: `rwx`, Grupo: `rw-`, Otros: `r--` |

        > 💡 Recuerda: `ugo` = `a`, pero `a` es más corto y estándar.

    === "⚠️ ¡Atención crítica!"

        - 🔒 **Directorios requieren `x` para ser utilizados**:  
          Sin `x`, no puedes hacer `cd dir/`, ni `ls dir/`, ¡aunque tengas `r` y `w`!  
        - 📂 `chmod -R` es poderoso… y peligroso:  
          ```bash
          chmod -R 777 /home   # ❌ Nunca en producción
          ```
        - 👤 El superusuario (`root`) **ignora permisos** → puede leer/eliminar cualquier archivo.

        > ✅ **Buenas prácticas**:  
        > - Usa `chmod 644` para archivos de texto/configuración  
        > - Usa `chmod 755` para scripts y directorios  
        > - Usa `chmod 600` para claves privadas (`~/.ssh/id_rsa`)

    === "🚪 Salir y continuar"
        ```bash
        exit
        ```
        > ✅ Todo desaparece.  
        > ➡️ **Siguiente paso**:  
        > - Prueba estos comandos en tu máquina (¡con precaución!)  
        > - O avanza a: **"Búsqueda con `grep` y `find`"** → para localizar archivos *por contenido o nombre*.