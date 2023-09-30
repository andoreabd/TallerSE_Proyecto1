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
1. Clonar y acceder al repositorio de Poky en su computador
```
git clone git://git.yoctoproject.org/poky
cd poky
```
2. Ingresar al branch de Kirkstone, con el que se va a generar la imagen mínima
```
git checkout -t origin/kirkstone -b my-kirkstone
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
#### Link de referencia
[https://docs.yoctoproject.org/brief-yoctoprojectqs/index.html]
## Comprobación del funcionamiento del modelo 
### Descarga del modelo
1. Desargar el github de los modelos de Open Vino
```
git clone https://github.com/openvinotoolkit/open_model_zoo.git
```
2. Descargar las funciones primordiales para el buen funcionamiento
```
pip install openvino-dev
```
3. Descargar los paquetes de omz
```
cd open_model_zoo/tools/model_tools
pip install --upgrade pip
pip install .
```
4. Descargar Pytorch: 
```
python3 -mpip install --user -r ./requirements-pytorch.in 
```
5. Descargar TensorFlow: 
```
python3 -mpip install --user -r ./requirements-tensorflow.in 
```
###  Descarga y conversión de los modelos necesarios
En este caso se va a ingresar a la carpeta donde está el modelo que se va a usar y se va a descargar y convertir con el **models.lst** para que no dure mucho tiempo ni se descarguen documentos que no se van a necesitar
```
cd ../../demos/human_pose_estimation_demo/python/
omz_downloader --list models.lst
omz_converter --list models.lst
```

### Forma 1: Video pre-generado
Se carga el demo de human pose estimation. Se usa "-d" para indicar el dispositivo que se va a usar (CPU o GPU). El "-i" es para indicar la entrada al algoritmo, en este caso se utiliza un video pre-grabado con personas que caminan frente a la cámara. El "-m" indica el modelo que se va a correr. Este de incluir todo el path donde se encuentra. Y por último, el "-at" es para el architecture_type (ae, highernet, openpose).
```
python3 human_pose_estimation_demo.py \
  -d CPU \
  -i  /home/andoreabd/open_model_zoo/demos/human_pose_estimation_demo/python/face-demographics-walking.mp4 \
  -m /home/andoreabd/open_model_zoo/demos/human_pose_estimation_demo/python/intel/human-pose-estimation-0005/FP16/human-pose-estimation-0005.xml \
  -at ae
```
### Forma 2: Video de una cámara de video
En este caso, todas las entradas son las mismas que en el pasado pero el "-i" va a cambiar a la entrada de video de la cámara de la computadora.
```
python3 human_pose_estimation_demo.py \
  -d CPU \
  -i  /dev/video0\
  -m /home/andoreabd/open_model_zoo/demos/human_pose_estimation_demo/python/intel/human-pose-estimation-0005/FP16/human-pose-estimation-0005.xml \
  -at ae
```
#### Link de referencia
Decarga de omz tools:
[https://docs.openvino.ai/2023.1/omz_tools_downloader.html] 
Busqueda de los demos:
[https://docs.openvino.ai/2023.1/omz_demos.html#doxid-omz-demos]
Informacion del demo escogido:
[https://docs.openvino.ai/2023.1/omz_demos_human_pose_estimation_demo_python.html#doxid-omz-demos-human-pose-estimation-demo-python]

## Generar la imagen personalizada
### Agregar layers a la imagen
Se tienen que clonar varios repositorios dentro del de Poky 
```
git clone -b kirkstone https://github.com/openembedded/meta-openembedded.git
git clone -b kirkstone https://git.yoctoproject.org/meta-intel
git clone -b kirkstone https://github.com/kraj/meta-clang.git
```
Levantar ambiente  
```
source oe-init-build-env 
```
Se deben generar los layes de la imagen con los comandos 
```
bitbake-layers add-layer ../meta-openembedded/meta-oe
bitbake-layers add-layer ../meta-openembedded/meta-python
bitbake-layers add-layer ../meta-openembedded/meta-networking
bitbake-layers add-layer ../meta-openembedded/meta-gnome
bitbake-layers add-layer ../meta-openembedded/meta-multimedia
bitbake-layers add-layer ../meta-openembedded/meta-xfce
bitbake-layers add-layer ../meta-clang
bitbake-layers add-layer ../meta-intel
```
Sin embargo presenté problemas al generarlo así, entonces los añadí dirigiendome a la carpeta **build/conf/bblayers.conf** ya añadiendo manualmente la información necesaria
```
POKY_BBLAYERS_CONF_VERSION = "2"

BBPATH = "${TOPDIR}"
BBFILES ?= ""

BBLAYERS ?= " \
  /home/andreabo/poky/meta \
  /home/andreabo/poky/meta-poky \
  /home/andreabo/poky/meta-yocto-bsp \
  /home/andreabo/poky/meta-openembedded/meta-oe \
  /home/andreabo/poky/meta-openembedded/meta-python \
  /home/andreabo/poky/meta-openembedded/meta-networking \
  /home/andreabo/poky/meta-openembedded/meta-gnome \
  /home/andreabo/poky/meta-openembedded/meta-multimedia \
  /home/andreabo/poky/meta-openembedded/meta-xfce \
  /home/andreabo/poky/meta-clang \
  /home/andreabo/poky/meta-intel \
  "
```
#### Link de referencia
[https://docs.openvino.ai/2023.1/openvino_docs_install_guides_installing_openvino_yocto.html]
### Agregar recetas a la imagen
Para añadir las recetas hay que abrir el file en **build/conf/local.conf** y añadir las instalaciones que se van a necesitar en la imagen personalizada
```
IMAGE_INSTALL:append = " \
                 python3-pip \
                 git \
                 vim \
                 openssh \
                 opencv \
                 packagegroup-core-x11 \
                 packagegroup-xfce-base \
                " 
```
Para poder correr la imagen en Virtual Box se le tiene que agregar al mismo archivo
```
IMAGE_FSTYPES += "wic.vmdk"
```
Y también, para tener espacio en el Virtual Box para poder bajar los videos y scripts necesarios se le debe añadir al mismo archivo
```
IMAGE_ROOTFS_SIZE="2048000"
```
Y:
```
# Enable clDNN GPU plugin when needed.
# This requires meta-clang and meta-oe layers to be included in bblayers.conf
# and is not enabled by default.
PACKAGECONFIG:append:pn-openvino-inference-engine = " opencl"

# Enable building OpenVINO Python API.
# This requires meta-python layer to be included in bblayers.conf.
PACKAGECONFIG:append:pn-openvino-inference-engine = " python3"
```
### Correr la imagen mínima con los cambios hechos
```
bitbake core-image-minimal
```
Falló el build system de Yocto Project ya que no podía encontrar ciertos archivos especificados con URL en el error.
La solución fue correr
```
bitbake -c cleansstate fribidi-native
bitbake -c cleansstate dnf-native
bitbake -c cleansstate libice-native
```
Y así pude generar bien la imagen mínima sin problemas.

## Probar la imagen en Virtual Box
### Instalacion de Virtual Box
Cuando ya la imagen esta generada, se va a descargar Virtual Box con
```
sudo apt update
sudo apt install virtualbox
```
### Probar la imagen en el Virtual Box
Ingresar a Virtual Box y darle en New para generar una nueva máquina virtual con las siguientes especificaciones
Name: Proyecto1
Machine Folder: /home/andreabo/VirtualBox VMs
Type: Linux
Version: Other Linux (64-bit) 

Memory size: 4096MB
Hard Disk: Use an existing virtual hard disk file
**/poky/build/tmp/deploy/images/qemux86-64**
core-image-minimal-qemux86-64-20230929031742.rootfs.wic.vmdk

### Actualizar los settings de la Virtual Box para que tenga internet
1. Ir a settings
2. Ir a network y en Attached to seleccionar Bridged Adapter y en Advanced y Promiscous Mode seleccionar Allow All
3. Iniciar la VM
4. En una terminal escribir
```
ifconfig eth0 up
udhcpc -i eth0 
```
### Clonar el repositorio con el modelo a utilizar
```
git clone https://github.com/andoreabd/TallerSE_Proyecto1.git 
```
### Instalar el toolkit de OpenVino de PyPi
Set up en ambiente de Open Vino y activarlo
```
python3 -m venv openvino_env
source openvino_env/bin/activate
```
Set up y actualizar el PIP a la version más alta
```
python -m pip install --upgrade pip
```
Instalar los paquetes
```
python -m pip install openvino
```









