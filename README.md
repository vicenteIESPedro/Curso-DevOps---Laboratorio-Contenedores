# Curso-DevOps---Laboratorio-Contenedores

1. CREANDO IMAGENES<br>
Creación de un contenedor basado en Ubuntu y ejecución de comandos en el contenedor
<br> <br> 
Paso 1 - Creación mediante comando Docker<br> <br>
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
Paso 2. DOCKER FILE<br>
Un fichero docker permite la creación de imagenes indicando cada uno de los pasos necesarios para obtener la imagen.<br>
Para automatizar los comandos realizados en el paso 1, definiré el fichero: build/Dockerfile.ubuntu<br><br>
a) Crear el fichero dockerfile
Creo el fichero build/Dockerfile.ubuntu. Dentro indico dos líneas: FROM (de que imagen voy a partir) y RUN (comando a ejecutar en la imagen que se crea)<br>
<img width="783" height="237" alt="paso 2- dockerfile" src="https://github.com/user-attachments/assets/73b4828f-a8c3-40dc-b062-102cc760b0f3" />
<br><br>
b) Crear la imagen a partir del fichero Dockerfile con el siguiente comando<br>
docker build -f build/Dockerfile.ubuntu --tag mi-ubuntu:v1 .<br>
Donde:<br>
- Docker build indica que quiero construir una imagen<br>
- -f fichero Indica en dichero docker origen. Si se omite, el comando busca un fichero Dockerfile en la carpeta actual<br>
- --tag nombre_imagen:etiqueta Indica el nombre que tendrá la imagen<br>
- . Indica el contexto, es decir, la carpeta local que tomará de base en la construcción de la imagen.<br><br>
c) Ejecutar un contenedor<br>
Igual que en el paso 1 pero tomando como imagen la creada anteriormente<br><br>
docker run --name ubuntu-1 -it mi-ubuntu:v1<br><br>
e) Comprobación de que curl está instalado<br>
En la terminal, ejecuto curl --version<br><br>
<img width="526" height="90" alt="paso 2- curl" src="https://github.com/user-attachments/assets/a672ab8b-c87f-44b8-bb01-71ea70af44b9" />
<br><br>
Es posible ver las diferentes capas de una imagen concreta usando el comando<br>
docker image history img<br><br><br>
2. Limpiando imágenes<br>
Defino el fichero Dockerfile.ubuntu2 en el que sucesivamente voy indicando las diferentes capas, quedando al final el fichero
<img width="833" height="222" alt="Paso 2 docker 2" src="https://github.com/user-attachments/assets/1292a262-18db-4120-b3d6-73e4830c22e0" />
<br><br>
Tras la ejecución sucesivas de docker build (docker build -f build/Dockerfile.ubuntu-2 --tag ubuntu-2 .) tras añadir cada linea, reviso las imagenes existentes
<img width="616" height="253" alt="paso 2- images" src="https://github.com/user-attachments/assets/38cc6755-f5f0-4592-b454-d35af7b46cf1" />
<br><br>
Como se observa, hay una sola imagen ubuntu-2. Esto pasa porque al ejecutar sucesivamente docker build indicando el mismo nombre de imagen, se sobreescribe el fichero generado quedando por tanto la ultima imagen creada. Para que esto no ocurra, se debe definir un nombre distinto a cada construcción, por ejemplo, indicando una etiqueta (V1, V2, ....)
<br><br>
3. VOLUMENES PERSISTENTES<br>
Un volumen es un espacio que se define en Docker y que puede ser asignado a un contenedor.<br>
Con esta práctica voy a definir un volumen y asignarlo como zona de almacenamiento de PostGresSQL en una máquina. Crearé una base de datos y una tabla. Posteriormente, borraré el contenedor, usando el volumen en otro contenedor PostGresSQL<br><br>

a) Creación del contenedor con el volumen <br><br>
docker run --name mi-postgres -e POSTGRES_USER=vicente -e POSTGRES_PASSWORD=hola -d --mount source=vol-pg,target=/var/lib/postgresql/18/docker postgres<br><br>
- --name mi-postgres. Definición del nombre del contenedor
- -e POSTGRES_USER=vicente -e POSTGRES_PASSWORD=hola Parametros de Postgres para definir el usuario superadministrador
- --mount source=vol-pg,target=/var/lib/postgresql/18/docker Definición del volumen. El volumen se llama vol-pg. Lo enlazo a la carpeta de data del contenedor
<br><br>
b) Apertura de shell en el contenedor <br><br>
docker exec -it mi-postgres bash
<br><br>
c) Una vez en el shell, iniciar el cliente de PostgresSQL psql con creación de la base de datos dbdevops<br><br>
psql -h 127.0.0.1 -U vicente -d dbdevops<br><br>
d) En psql, crear la tabla, insertar registros, mostrar el contenido de la tabla y cerrar psql<br><br>
CREATE TABLE items (<br>
 id SERIAL PRIMARY KEY,<br>
 name TEXT<br>
);<br><br>
INSERT INTO items(name) VALUES ('item1');<br>
INSERT INTO items(name) VALUES ('item2');<br><br>
select * from items;<br><br>
exit<br><br>
e) Me aseguro que se han guardado los datos en disco parando el servicio y borro el contenedor
<br><br>
service postgresql stop<br><br>
exit<br><br>
docker rm -f mi-postgres <br><br>
f) Creo el nuevo contenedor enlazando el mismo volumen
docker run --name mi-postgres1 -e POSTGRES_USER=vicente -e POSTGRES_PASSWORD=hola -d --mount source=vol-pg,target=/var/lib/postgresql/18/docker postgres<br><br>
g) Verifico que existe la base de datos dbdevops y la tabla items que creé en la máquina virtual anterior mostrando sus datos.<br><br>
docker exec -it mi-postgres1 bash<br><br>
psql -h 127.0.0.1 -U vicente<br><br>
\c dbdevops<br><br>
\dt<br><br>
select * from items<br><br>
4. BIND MOUNTS<br>
Un montaje de tipo bind permite enlazar una carpeta local a un espacio de almacenamiento en el contenedor<br>
Parto de la carpeta web y dentro el archivo index.html con contenido &lt;h1>Hola Docker&lt;/h1>. Creo el nuevo contenedor enlazando la carpeta web a la carpeta html del contenedor<br><br>
docker run -d -p 8080:80 --mount type=bind,source=./web,target=/usr/share/nginx/html --name nginx-bind nginx<br><br>
Al acceder en el navegador a localhost:8080 compruebo que se muestra la página local<br><br>
Como se ve, la ventaja de BIND MOUNTS es trabajar en un contenedor con una carpeta local. Si cambiamos los archivos locales, automáticamente se ven los resultados en el contenedor al estar directamente conectado a la carpeta<br><br>
5. AUDITANDO VOLUMENES<br><br>
Para obtener información de los volúmenes se utiliza el comando docker volume con diversas variantes.<br><br>
Ver los volúmenes existentes.<br>
docker image list<br><br>
Consultar información de un volumen, incluyendo punto de montaje<br>
docker image inspect volumen<br><br>
6. CREANDO REDES PRIVADAS<br>
Los contenedores se conectan por defecto a la red bridge. Es posible crear nuestra propia red y que los contenedores que definamos con esta red se puedan comunicar entre ellos. <br>
Los pasos para comprobar esto son:<br><br>
a) Crear mi propia red<br>
docker network create my-net<br><br>
b) Añadir un contenedor ubuntu a la red mostrado el prompt para que quede a la espera<br>
docker run  --name ubuntu-red-1 --network my-net -it ubuntu bash<br><br>
c) En otro terminal, comprobar la dirección asignada al contenedor anterior<br>
docker network inspect my-net<br><br>
d) Iniciar un nuevo contenedor en modo interactivo<br>
docker run  --name ubuntu-red-2 --network my-net -it ubuntu bash<br><br>
e) Instalar en el contenedor la utilidad ping<br>
apt-get update && apt-get install -y iputils-ping <br><br>
f) Hacer ping al otro contenedor (tengo su ip del apartado c, 172.18.0.2)<br>
ping 172.18.0.2<br><br>
Y de esta forma compruebo que los contenedores se comunican entre sí<br><br>
7. RED NONE<br>
Esta red permite que un contenedor esté completamente aislado del resto. Esto nos va a permitir usar este contenedor para la realización de operaciones sensibles sin peligro de que sea interferido por otros contenedores.



  
  

 





