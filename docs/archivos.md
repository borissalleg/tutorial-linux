# 📄 **Archivos en Linux**
### *Fundamentos, Creación y Práctica*
* Enfoque 100 % práctico utilizando Docker*

---

## 🔍 ¿**Qué es un archivo en Linux?**

En Linux, **todo es un archivo** — una abstracción poderosa del sistema operativo:

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

## 🧰 Cómo crear archivos en Linux: 7 métodos prácticos

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

## 🛠️ Práctica guiada: "Laboratorio de Creación de Archivos"  
*Ejecútala en un contenedor Docker — segura y efímera*

### ✅ Paso 0: Preparar el entorno
Ejecuta en PowerShell/CMD:
```powershell
docker run -it --name lab-archivos ubuntu:22.04 /bin/bash
apt update && apt install -y nano
```

### 1. Archivo vacío con touch
    touch vacio.txt
    ls -l vacio.txt          # → tamaño: 0 bytes

### 2. Archivo con una línea
    echo "Primera línea" > linea.txt
    cat linea.txt

### 3. Archivo multilínea con here-document
    cat > presentacion.md <<EOF

#### Mi Proyecto
- Autor: Ronald
- Fecha: $(date +%Y-%m-%d)
- Estado: ✅ En desarrollo
EOF
cat presentacion.md

### 4. Archivo de configuración con printf (más robusto)
    printf "HOST=%s\nPORT=%d\nDEBUG=%s\n" "localhost" 8080 "true" > app.conf
    cat app.conf

### 5. Script ejecutable
    cat > saludar.sh <<'EOF'
    #!/bin/bash
    echo "¡Hola, soy $(whoami)!"
    echo "Fecha: $(date)"
    EOF

    chmod +x saludar.sh      # hacerlo ejecutable
    ./saludar.sh             # ejecutar