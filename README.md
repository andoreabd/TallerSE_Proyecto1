# Bitácora: Proyecto 1 - Taller de Sistemas Embebidos
## Creación de una imagen en Yocto Project
### Instalación de Python
1. Revisar la versión de Python
```
python3 --version
```
2. Instalar la última versión de Python3
```
sudo apt update
sudo apt install python3
```
### Instalar paquetes necesarios para el correcto funcionamiento
```
sudo apt install gawk wget git diffstat unzip texinfo gcc build-essential chrpath socat cpio python3 python3-pip python3-pexpect xz-utils debianutils iputils-ping python3-git python3-jinja2 libegl1-mesa libsdl1.2-dev python3-subunit mesa-common-dev zstd liblz4-tool file locales
sudo locale-gen en_US.UTF-8
```
### Clonar el repositorio de Poky
Clonar y acceder al repositorio de Poky en su computador
```
git clone git://git.yoctoproject.org/poky
cd poky
```
Ingresar al branch de Kirkstone, con el que se va a generar la imagen mínima
```
git checkout -t origin/mickledore -b my-mickledore
git pull
```
### Creación de la imagen mínima con Yocto Project
1. Inicializar el ambiente
```
source oe-init-build-env
```
2. Para mejorar el tiempo de generación ingresar a **build/conf/local.conf** y se descomentan las siguientes líneas
```
BB_SIGNATURE_HANDLER = "OEEquivHash"
BB_HASHSERVE = "auto"
BB_HASHSERVE_UPSTREAM = "hashserv.yocto.io:8687"
SSTATE_MIRRORS ?= "file://.* https://sstate.yoctoproject.org/all/PATH;downloadfilename=PATH"
```
3. Generar la imagen
```
bitbake core-image-minimal
```
