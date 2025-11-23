# DaVinci Resolve Setup for Rocky Linux 10 / RHEL 10

Este repositorio contiene un conjunto de scripts automatizados para facilitar la instalación y configuración de **DaVinci Resolve** (versión Studio o Gratuita) en **Rocky Linux 10**, **Red Hat Enterprise Linux 10** y distribuciones compatibles.

El proyecto soluciona automáticamente los problemas comunes de dependencias, conflictos de librerías (GLib/Pango), instalación de drivers NVIDIA y fuentes tipográficas esenciales.

## 🚀 Características

* **Drivers NVIDIA:** Instalación automática de drivers Open Kernel (DKMS) para tarjetas RTX 2000+.
* **Corrección de Dependencias:** Instala automáticamente librerías faltantes como `libXt`, `libGLU`, `xcb-util-cursor`.
* **Bypass de Verificación:** Maneja la compatibilidad con `zlib-ng` saltando el chequeo de paquetes legacy de Blackmagic si es necesario.
* **Patch de Librerías:** Soluciona el conflicto conocido de `GLib/Pango` que impide que Resolve inicie en distros modernas.
* **Fuentes MS Core:** Instala fuentes esenciales (Arial, Times, etc.) para asegurar la correcta visualización de la interfaz.

## 📋 Requisitos Previos

1.  **Sistema Operativo:** Rocky Linux 10 o RHEL 10 (Instalación fresca recomendada).
2.  **Permisos:** Acceso a `root` o usuario con privilegios `sudo`.
3.  **Archivo de Instalación:** Debes descargar el ZIP oficial de DaVinci Resolve desde el sitio de Blackmagic Design.

## 🛠️ Instrucciones de Instalación

Sigue estos pasos en orden para garantizar una instalación exitosa.

### 1. Clonar el repositorio y dar permisos
Descarga los scripts y asegúrate de que sean ejecutables:

```bash
git clone https://github.com/samcalderonaguirre/davinci_resolve_scripts_de_instalacion_para_rhel_rocky_alma_10.git
cd davinci_resolve_scripts_de_instalacion_para_rhel_rocky_alma_10
chmod +x *.sh
```

### 2. Instalar Drivers NVIDIA

Este script habilita los repositorios CRB y EPEL, e instala los drivers necesarios para CUDA.

```bash
sudo ./NVIDIA_rocky.sh
```

⚠️ <b>Importante (Secure Boot):</b> Si tienes Secure Boot habilitado, el script te pedirá configurar una contraseña MOK. Al reiniciar, deberás seleccionar "Enroll MOK" en la pantalla azul e introducir esa contraseña.

### 3. Instalar Fuentes (Opcional pero recomendado)

Resolve utiliza ciertas fuentes del sistema para su interfaz. Este script compila e instala las fuentes Core de Microsoft.

```bash
sudo ./fonts.sh
```

### 4. Instalar DaVinci Resolve

Este es el paso principal. El script espera encontrar el instalador en la carpeta Descargas de tu usuario.

1. Descarga el ZIP de DaVinci Resolve (Linux) desde la web de Blackmagic.
2. Coloca el archivo `.zip` (sin descomprimir) en tu carpeta `~/Descargas` (o `~/Downloads` si modificas el script).
3. Ejecuta el script de instalación:

```bash
sudo ./rocky_resolve.sh
```

#### ¿Qué hace este script?

* Busca el ZIP más reciente en `~/Descargas`.
* Lo descomprime y ejecuta el instalador oficial (`.run`) de forma desatendida o gráfica.
* Aplica un "fix" moviendo librerías conflictivas (`libglib`, `libgio`, `libpango`) a una carpeta de backup para obligar a Resolve a usar las del sistema.
* Crea los enlaces simbólicos necesarios (`libcrypt.so.1`).

### 🏁 Ejecución

Una vez finalizado, no ejecutes DaVinci Resolve como root. Lánzalo desde el menú de aplicaciones o desde la terminal con tu usuario normal:

```bash
/opt/resolve/bin/resolve
```

#### 🐛 Solución de Problemas

* **Error: "No DaVinci_Resolve_*.zip found":** Asegúrate de que el archivo descargado esté en la carpeta `/home/TU_USUARIO/Descargas`. El script está configurado por defecto para buscar en esa ruta en español.
* **Error de librerías al iniciar:** Si ves errores relacionados con `libz.so` o `libcrypt`, el script intenta solucionarlos automáticamente. Si persisten, asegúrate de haber ejecutado el script `rocky_resolve.sh` hasta el final.
* **NVIDIA-SMI falla:** Asegúrate de haber reiniciado después del paso 2 y, si usas Secure Boot, de haber completado el proceso de "Enroll Key".

