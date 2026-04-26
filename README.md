# Curso-DevOps---Laboratorio-Contenedores

1. CREANDO IMAGENES
Creación de un contenedor basado en Ubuntu y ejecución de comandos en el contenedor
<br> <br> 
Paso 1 - Creación mediante comando Docker<br> 
a) Creación de imagen<br><br>  
docker run -it ubuntu bash<br> <br> 
- Docker run Comando docker para iniciar un contenedor<br> 
- modificador -it Le indico que quiero una sesión interactiva con el contenedor<br> 
- imagen: ubuntu. Por regla general, debe ser lo último que se indica en el comando docker run<br> 
- interprete de comando: bash Ya que voy a abrir una sesión interactiva, le indico que interprete 
<br><br>  
b) Ejecución de comandos
Si todo ha ido bien, nos abrirá un promt para ejecutar comandos sobre el contenedor docker.
En este caso, voy a instalar curl con los siguientes comandos:
<br><br>
apt-get update
<br><br>
apt-get install curl
<br><br>
Por último, verifico que esta instalada.
curl --version
<img width="277" height="34" alt="Paso 1-curl version" src="https://github.com/user-attachments/assets/e2c9795e-bb42-4bfe-8e91-7a4a4d0a1a3f" />
<br> <br>
Y cierro el promt con  el comando exit.
<br> <br> 
c) Como convertir el contenedor con el estado actual en una imagen.<br> 
Para ello se utiliza el comando docker commit.<br>
Por ejemplo, voy a crear la imagen mi-ubuntu a partir del contenedor<br> 
Obtengo su id con docker ps -a <br> 
Docker commit id mi-ubuntu
<br> <br>
2. DOCKER FILE<br>
Un fichero docker permite la creación de imagenes indicando cada uno de los pasos necesarios para obtener la imagen.<br>
Para automatizar los comandos realizados en el paso 1, definiré el fichero: build/Dockerfile.ubuntu<br><br>
a) Crear el fichero dockerfile
Creo el fichero build/Dockerfile.ubuntu. Dentro indico dos líneas: FROM (de que imagen voy a partir) y RUN (comando a ejecutar en la imagen que se crea)<br>
<img width="783" height="237" alt="paso 2- dockerfile" src="https://github.com/user-attachments/assets/73b4828f-a8c3-40dc-b062-102cc760b0f3" />
<br><br>
b) Creo la imagen a partir del fichero Dockerfile con el siguiente comando<br>
docker build -f build/Dockerfile.ubuntu --tag mi-ubuntu:v1 .<br>
Donde:<br>
- Docker build indica que quiero construir una imagen<br>
- -f fichero Indica en dichero docker origen. Si se omite, el comando busca un fichero Dockerfile en la carpeta actual<br>
- --tag nombre_imagen:etiqueta Indica el nombre que tendrá la imagen<br>
- . Indica el contexto, es decir, la carpeta local que tomará de base en la construcción de la imagen.<br><br>
c) Ejecutar un contenedor<br>
Igual que en el paso 1 pero tomando como imagen la creada anteriormente<br><br>

d) Creación de un contenedor a partir de la imagen<br>
docker run --name ubuntu-1 -it mi-ubuntu:v1<br><br>
e) Comprobación de que curl está instalado<br>
En la terminal creada, ejecuto curl --version
<img width="526" height="90" alt="paso 2- curl" src="https://github.com/user-attachments/assets/a672ab8b-c87f-44b8-bb01-71ea70af44b9" />
<br><br>


  
  

 





