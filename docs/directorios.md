# 📁 Comandos Básicos para Directorios en Linux + Práctica para Principiantes

Esta guía introduce los comandos esenciales del sistema de archivos en Linux y propone una práctica progresiva dentro de un contenedor Alpine —ideal para quienes inician en entornos Unix-like o Docker.

> ✅ **Ventaja del entorno contenerizado**: Puedes equivocarte sin riesgo. Todo se descarta al final.

---
???+ info "Trabajando con Directorios"
    === "Parte 1: Comandos Básicos para Directorios" 

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

        ---


    === "Parte 2: Práctica — Explorando Directorios en Alpine"

        > **Duración estimada**: 10 minutos  
        > **Nivel**: Principiante absoluto  
        > **Entorno seguro**: Todo ocurre dentro de un contenedor efímero.

        ---

        === "▶️ Paso 0: Iniciar el entorno de práctica"

            Ejecuta en tu terminal (host):

            ```bash
            docker run -it --rm --name lab-dir alpine:3.20 /bin/sh
            ```