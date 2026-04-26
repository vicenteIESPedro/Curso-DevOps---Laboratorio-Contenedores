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
c) Como convertir el contenedor con el estado actual en una imagen.
Para ello se utiliza el comando docker commit. Por ejemplo, voy a crear la imagen mi-ubuntu a partir del contenedor (obtengo su id con docker ps -a)
Docker commit id mi-ubuntu
<br> <br> 



