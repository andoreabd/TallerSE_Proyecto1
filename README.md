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
#### Link de referencia
[https://docs.yoctoproject.org/brief-yoctoprojectqs/index.html]
## Comprobación del funcionamiento del modelo 
### Descarga del modelo
1. Desargar el github de los modelos de Open Vino
```
git clone https://github.com/openvinotoolkit/open_model_zoo.git
```
2. Descargar 
```
pip install openvino-dev
```
3. Descargar
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
###  
```
cd ../../demos/human_pose_estimation_demo/python/
omz_downloader --all
omz_converter --all 
```

### Forma 1: Video pre-generado
Se carga el demo de human pose estimation. Se usa "-d" para indicar el dispositivo que se va a usar (CPU o GPU). El "-i" es para indicar la entrada al algoritmo, en este caso se utiliza un video pre-grabado con personas que caminan frente a la cámara. El "-m" indica el modelo que se va a correr. Este de incluir todo el path donde se encuentra. Y por último, el "-at" es para el architecture_type (ae, highernet, openpose).
```
python3 human_pose_estimation_demo.py \
  -d CPU \
  -i  /home/andreabo/open_model_zoo/demos/human_pose_estimation_demo/python/face-demographics-walking.mp4\
  -m /home/andreabo/open_model_zoo/demos/human_pose_estimation_demo/python/intel/human-pose-estimation-0005/FP16/human-pose-estimation-0005.xml \
  -at ae
```
### Forma 2: Video de una cámara de video
En este caso, todas las entradas son las mismas que en el pasado pero el "-i" va a cambiar a la entrada de video de la cámara de la computadora.
```
python3 human_pose_estimation_demo.py \
  -d CPU \
  -i  /dev/video0\
  -m /home/andreabo/open_model_zoo/demos/human_pose_estimation_demo/python/intel/human-pose-estimation-0005/FP16/human-pose-estimation-0005.xml \
  -at ae
```
#### Link de referencia
[https://docs.openvino.ai/2023.1/omz_demos_human_pose_estimation_demo_python.html#doxid-omz-demos-human-pose-estimation-demo-python]
