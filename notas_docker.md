# Notas Docker

# **Índice**   
1. [Notas acerca de estas notas](#id1)
2. [Sección 5: Trabajar con contenedores](#id2)
3. [Sección 8: REDES](#id2)
4. [Sección 226: Réplicas en los servicios](#id226)
5. [Sección 233: Etiquetar un nodo](#id233)
6. [Sección 234: Restringir nodos en el despliegue de servicios ](#id234)
7. [Sección 235: Nodos Preferentes](#id235)
8. [Sección 236: Quitar nodos del cluster](#id236)
3. [Sección](#id)
4. [Sección](#id)
1. [Sección](#id)
2. [Sección](#id)
3. [Sección](#id)
4. [Sección](#id)


## Notas acerca de estas notas <a name="id1"></a>
Estas son mis notas personales del curso: **_Aprende a usar Docker desde lo más básico hasta llegar a Docker Compose, Docker Swarm y Kubernetes_** de Udemy  con el fin de guardar una referencia documental, técnica y de apoyo personal.

No están incluídos todos los tópicos. Están excluídos los más básicos ( por ejemplo: qué es un contenedor, las diferencias con una máquina virtual, Instalación, Etc.) que están muy documentados. Por esta razón el número de secciones no está toda la secuencia y el índice tine una aparente carencia numérica.

Como son notas que se he hecho al vuelo, es decir al mismo tiempo que he ido tomando el curso, muchas veces no alcanzo a darle el formato adecuado y puede contener ciertos errores.
He estado corrigiendo errores que he notado visuálmente, errores de sintaxis, semánticos, Etc. Conforme pueda las iré revisando y corrigiendo.

Estas notas las pongo para mí mismo. En segundo término  porque me han pedido que comparta mis notas con el equipo de Infraestructura. 

Sirva de advertencia por los posibles errores u omisiones que son completamente involuntarios. Además hay tópicos que necesito desarrollar mas, probar mas casos que los que he podido probar en laboratorio, que por falta de tiempo no he probado, o porque luego me han surgido dudas del tipo: _¿qué pasaría si...?_ y que no habían surgido antes.

Hector Alfredo Alvarez Contreras

## Seccion 1:Introduccion al curso

Notas generales del curso en udemy

## Seccion 2:Introduccion a docker
Qué es docker, diferencias entre máquina virtual, etc.

## Seccion 3 Instalación 
En Gnu/Linux engine

## Seccion 4 Instalación
Docker Desktop on Windows

Nota para mi: En este apartado tal vez valga la pena poner las instrucciones de instalación de docker implementado por los Windows Server, vía Roles y Características y que tengo en otros documentos por separado, aunque esté curso sólo tenga alcance con docker desktop.

## Sección 5: Trabajar con contenedores <a name="id2"></a>

### Para obtener ayuda
    docker --help

### Para obtener ayuda de un comando específico
    docker ps --help
    
    -a (-a ejecutando y no ejecutando)
    -q solo los ids de los contenedores

### Para listar las imagenes 
    docker images

    docker ps -l 

### Listar los últimos contenedores

### Para listar los ids de los 3 últimos contenedores (-a ejecutando y no ejecutando) 
    docker ps -a -n 3

### Para buscar por filtros
    --filter --filter 

### Poner nombres a los contenedores
    --name  
    docker run --name "el nombre del contenedor" ["otro contenedor"] ["otro otro contenedor"]

### Para interactuar con un contenedor con "docker run"
    -i interactivo
    -t abre un shell
    -it interactivo mediante un shell
(En algunos sistemas se tiene que agregar el comando del shell, por ejemplo bash


docker run --name "nombre_contenedor | [id contenedor]" -it "nombre de la imagen a usar"

     Ejemplo: 
     docker run --name mi_contenedor -it suse
     docker run --name mi_contenedor -it suse bash (si la versión la requiere)

### Para salir del modo interactivo con run: exit
Esto va a detener el contenedor

### Para detener un contenedor
docker stop "nombre_contenedor" o id_contenedor (opcional primeras 4 o 5 digitos del id)


### Para interactuar iniciando un conenedor que está detenido:
docker start --name "nombre_contenedor | [id contenedor]" -i "nombre de la imagen a usar"

    Ejemplo: 
    docker run --name mi_contenedor -i suse
la opción **-t** no se usa porque está implícita


### Para comprobar el espacio utilizado por los contenedores
docker ps -a -s

### Para iniciar un contenedor en segundo plano
**-d**

     Ejemplo:
     docker run -d nginx

Si el contenedor no tiene tareas no quedará activo, para forzarlos se usa **-d -it** 

    Ejemplo:
	docker run -d -it fedora

### Interactuar con contenedores iniciados

docker exec "nombre_contenedor | id_contenedor" comando

Se ejecuta el comando y devuelve la salida 

Se pueden ejecutar varios comandos al mismo tiempo:

     docker exec nginx1 date ; uname -n

También se pueden ejecutar scripts de shel como argumento:

    docker exec nginx1 /bin/sh -c 'for i in *; do echo
    "ficheroi -->" $i ; done'

### para interactuar con el contenedor en su shell

   Ejemplo:
   docker exec -it "nombre_contenedor | id_contenedor" bash
   docker exec -it suse bash


### Para borrar un contenedor
docker rm id_contenedor | nombre_contenedor

Nota: Para borrar un contenedor tiene que estar detenido
### Para borrar todos los contenedores
Se pueden sacar la lista de IDs de los contenedors y pasarla como parámetro al **rm**:

    docker rm $(docker ps -qa)
    docker rm `docker ps -aq`

### Para borrar una imagen
NO tiene que estar en uso por algún contenedor, se tiene que borrar primero el contenedor y luego se podrá borrar la imagen

    docker rmi id_imagen

### Para forzar el borrado de una imagen 

Se usa **-f**:

    docker rmi -f fedora


### Para traer a primer plano un contenedor se puede usar atatch

docker attach suse 


### Logs
Muestra lo que se está imprimiendo en pantalla algún contenedor que está corriendo
docker logs "nombre_contenedor | id_contenedor"
docker logs suse

Esto mostrará todo lo que ha impreso la pantalla
docker logs suse -f

### Para matar un contenedor:
kill
docker kill "id_contenedor | nombre_contenedor"
docker kill suse

con la opción -f muestra la pantalla sin parar:
docker logs "nombre_contenedor | id_contenedor"


### Para averiguar qué proceso de un contenedor consume mas recursos
-docker top "nombre_contenedor | id_contenedor"


### Para ver las estadísticas de consumo de un contenedor
docker stats "nombre_contenedor | id_contenedor"


### Para recuperar información y las propiedades de una imagen o un contenedor
docker inspect "id_contenedor | id_imagen"
saldrá todo la información

se sugiera mandarlo a un archivo con >
docker inspect id_contenedor > información_contenedor.txt

### Para obener información del sistema
docker system df | events | info | prune

+df para ver cuantas imagenes contenedores volumentes y cache tiene el sistema actual de docker
+events los eventos del sistema de docker
+prune remueve contenedores, redes, imagenes y caché en el sistema docker

### Para copiar archivos del sistema operativo a un contenedor
 cp
docker cp origen destino 
docker cp "archivo a copiar" "nombre_contenedor | id_contenedor":/ruta/de/destino
docker cp ejemplo.tx ubuntu10:/etc/wireguard

docker cp  ubuntu10:ejemplo.tx /ruta/de/destino/local

### Para borrar un contenedor automáticamente al terminar su función o sus tareas agregar al comando rm
docker run -it -d --rm --name=ubuntu1 

### Para pasar variables y valores 
docker run -it -d ubuntu1 ubuntu -e variable1="valor" -e variable2="valor2"

### Para pasar las variables y valores a docker almacenados en un archivo de texto
--env-file archivo
docker run --name mysql2 --env-file archivo mariadb


### Para crear un contenedor con un puerto específico
docker run -d -name miservidorweb -p 8080:80 nginx
donde_
8080 es el puerto expuesto al exterior
80 es el puerto local interno de docker

### Para generar un puerto expuesto de forma automática
-P
docker 

## Sección 8: REDES <a name="id8"></a>

### Para obtener información acerca de las redes 
docker network inspect [bridge | host | none]

inspecionar una red
docker network ls

docker port "nombre_contenedor | id_contenedor"

ver los puertos expuestos de un contenedor
docker port mi_contenedor

otra forma es con docker image inspect mi_imagen
Revisar el nodo "ExposedPorts"


### Para crear una red
para ver la ayuda  docker network create --help

docker network create nombre_red
docker network create nombre_red --subnet=192.168.0.0/16

### Para iniciar un contenedor con una red específica ya creada
--network "nombre de red"
docker run -d -name "nombre_contenedor | id_contenedor" --network "nombre_red" nombre_imagen
docker run -d --name micontenedor --network red1 nginx

### Para conectar un contenedor a una red existente con docker network
docker network connect "nombre_red" micontenedor
docker network connect red1 fedora10 
Esto  agrega una red adicional al contenedor 

### Para desconectar un contenedor a una red existente con docker network
docker network disconnect "nombre_red" micontenedor
docker network disconnect red1 fedora10 
Esto desconecta la red indicada al contenedor 


docker network create --subnet=172.28.0.0/16 --gateway=172.28.0.1 --ip-range=172.28.10.0/24 net4
--subnet la subred
--gateway la ip del gateway
--ip-range rango apartir del cual se asignarán ips

### Para borrar una red
docker network rm nombre_red
la red no debe de estar utilizada

## Sección 9: VOLUMENES

### Para listar los volumenes 

docker volume COMANDO

Los comandos son:
	* create
	* inspect
	* ls
	* prune
	* rm

### Para crear un volumen
La forma mas sencilla es crear un volumen al crear un contenedor

Se crea con -v
docker run -it -v /datos --name ubuntu1 ubuntu bash

donde /datos es la ruta interna del directorio
en el sistema operativo host el volument por default se creará el volumen en /var/lib/docker/volumes

Otra forma es crear un volumen para luego usarlo
docker volume create "nombre_volumen"
Creará el volumen con el nombre especificado y no con un hash como nombre y lo pondrá en la ruta por defecto:
/var/lib/docker/volumes


### Para asociar un volumen ya creao se usa la opción -v}
ejemplo:
docker run  -it --name "nombre_contenedor" -v "nombre_volumen":/ruta/interna/contenedor nombre_imagen bash


-Se pueden momntar volumenes en varios contenedores al mismo tiempo:
docker run -it --name ubuntuX -v volumen1:/datos

### Para montar un volumen solo lectura:
docker run -it --name ubuntuX -v volumen1:/datos:ro


### Para que un contenedor tenga los mismos volumenes que otro se usa --volumes-from
ejemplo:
docker run -it --name ubuntuX --volumes-from ubuntu4 bash
docker run -it --name ubuntu6 --volumes-from ubuntu4 bash


Bind Mounts

### Para compartir un directorio del host con un contenedor
Es similar a crear un volumen, se usa -v pero se pone el la ruta del directorio local compartido
	ejemplo:
	docker run -it -v /root/dir1:/dir1 --name ubuntu2 ubuntu
Esto NO va a crear un volumen en el direcotorio por defecto

### Para crear almacenamiento temporal mientras que está iniciado el contenedor
se usa la opcción --tmpfs

docker run -d -i --name ejemplo --tmpfs /datos ubuntu
se va a crear un directorio /datos que existirá hasta que se apague o se reinicie el contenedor


## VOLUMENES EN WINDOWS

La versión de DockerDesktop ha ido cambiando con el tiempo, en este momento está asi:

En linux container

Por default en la versión recinte de docker para win a oct 2022 en la carpeta compartida oculta:
\\wsl$\docker-desktop-data\version-pack-data\community\docker

ahí se encuentra el árbol de directorios normal de docker, donde también está el direcotorio volumenes

En modo Windows container
Por lo regular en 
c:\ProgramData\Docker\volumes



## Sección 10: IMÁGENES

### Para ver las modificaciones que ha sufrido una imagen
diff

docker diff "nombre_imagen"  
* A para apend
* C changed
* D delete

### Para generar una imagen a partir de un contenedor modificado
Una forma es la siguiente apartir de una modificación de un contenedor:
commit usa dos parametros: contenedor a partir del cual se hará la nueva imagen
y el nombre de la nueva imagen, mas etiqueta opcional.
Si no se le pone se pone latest.

    Ejemplo:
    docker commit "nombre_contenedor" mi_ubuntu


## DOKERFILE

El nombre del archivo tiene que empezar con mayúscula: Dockerfile

El primer comando también es en mayúscula y siempre es FROM. 
Ejemplo: 

    FROM [ubuntu | scratch | otra ]

Luego se instala lo necesario con RUN por ejemplo: 

    RUN apt-get update
    RUN apt-get install -y python3

Ejemplo completo:

    FROM ubuntu
    RUN apt-get update
    RUN apt-get install -y python3

### Para construir la imagen se usa build
	docker build -t "nombre_imagen:[tag] contexto
	
Donde:
*	-t [:Tag] para nombrar la imagen 
*	Contexto: [. (punto) | ruta | url]
*	El contexto el directorio de trabajo y ahi tienen que estar todos los archivos necesarios
	
### Para ver los cambios que ha sufrido una imagen

docker image history "nombre_imagen"

#### Ejecutar un comando con la ejecución del contenedor RUN 
Ejecuta un comando cuando se está construyendo una imagen.


#### Ejecución de un comando por defecto en el contendor, soporta parámetros CMD
La directiva CMD es para ejecutar un comando por defecto en el contenedor. Sólo debe de existir un solo CMD, en el caso que existan varios, sólamente se ejecutará el último.
Se puede usar un comando en modo shell, es decir que el comando que se ponga se va a ejecutar con /bin/sh -c

    CMD echo "Bienvenido al conenedor"
   
otro ejemplo:

    CMD /bin/bash

En el ejemplo dos, se ejecutará un shell y luego se llamará el comando del otro shell, es decir /bin/bash, que no es lo mejor.
para evitar eso mejor usar corchetes.

Otra forma de ejecutar de con corchetes, y esto tendrá el efecto de ejecutarlo como exec, y no hará uso de shell, y funcionará aunque no tenga instalado ningún shell. 

La sistaxis es ["comando" , "parámetros" ] por ejemplo:

    CMD ["echo", "Bienvenido al contenedor"]
    CMD ["/bin/bash"] 

El segundo ejemplo es una mejor forma de ejecutar el shell que en modo comando.


#### Ejecución de un ejecutable durante el arranque del contenedor ENTRYPOINT

Ejecuta un comando al entrar al contenedor, a diferencia del CMD, el comando siempre se ejecuta con el entrypoint.

Este comando no se puede substituir por un parámetro en el RUN. El efecto que va a tener al encontrar un parámetro, es intentar agregar este como un argumento del comando del entrypoint.

El entrypoint siempre debería ir con corchetes [] en modo exec y también tiene que ir sólo una vez  en el Dockerfile.

    ENTRYPOINT ["/bin/bash"]

Se pueden mezclar entrypoint y cmd en un Dockerfile, tomando en cuenta que el entrypoint siempre se va a ejecutar y el cmd es opcional.

Ejemplo:

     FROM ubuntu
     RUN apt-get update
     RUN apt-get install -y python3
     RUN echo 1.0 >> /etc/version && apt-get install -y git \
     	&& apt-get install -y iputils-ping
     ENTRYPOINT ["ping","c","3"]
     CMD ["localhost"]

Se construye la imagen: 

    docker build -t image:v1

Luego se ejecuta: 

    docker run --rm image:v1 

Se ejecuta el ping con localhost del cmd, pero se puede sustituir con un parámetro:

    docker build -t image:v1 www.google.com

### Sobre escribir un ENTRYPOINT
	

Para sobre escrbir el entrypoint se puede usar --entrypoint:

Ejemplo:

      docker run --rm --entrypoint date image:v1

Esto sustuirá el comando del entrypoint por otro, pero esto se usa en casos extraordinarios, porque la imagen se construyó para usar el comando definido.


### Fijar el directorio de trabajo en el contenedor WORKDIR

Esta directiva permite determinar el directorio de trabajo para otras directivas como RUN, ENTRYPOINT, y otros comandos en ese directorio de trabajo.

Se pueden usar varios WORKDIR en el Dockerfile

Ejemplo:

	FROM ubuntu
	RUN apt-get update
	RUN apt-get install -y python3
	RUN echo 1.0 >> /etc/version && apt-get install -y git \
		&& apt-get install -y iputils-ping

	##WORKDIR
	RUN mkdir /datos
	WORKDIR /datos
	RUN touch f1.txt
	RUN mkdir /datos1
	WORKDIR /datos1
	RUN touch f2.txt
	
	##ENTRYPOINT
	ENTRYPOINT ["/bin/bash"]

### Copiar Contenido al Contenedor COPY - ADD
copian el contenido del host al contenedor origen destino
COPY
el ADD puede copiar directorios, archivos, soporta comodines, copia el contenido de un tar y lo desempaqueta en el destino en el contenedor. También sirve para copiar contenido de url al contenedor.

Ejemplo:

	FROM ubuntu
	RUN apt-get update
	RUN apt-get install -y python3
	RUN echo 1.0 >> /etc/version && apt-get install -y git \
		&& apt-get install -y iputils-ping

	##WORKDIR
	RUN mkdir /datos
	WORKDIR /datos
	RUN touch f1.txt
	RUN mkdir /datos1
	WORKDIR /datos1
	RUN touch f2.txt
	
	##COPY
	COPY index.html .
	COPY app.log /datos
	
	##ADD
	ADD docs docs
	ADD r* /datos/
	ADD f.tar .
	
	##ENTRYPOINT
	ENTRYPOINT ["/bin/bash"]

Construimos la imagen

    docker build -t image:v2

Crear nuevo contenedor

    docker run -it --rm image:v2


### VARIABLES DE ENTORNO ENV

La directiva **ENV** se usa para crear variables cuando se crea el contenedor.
Para crear variables al momento de crear la imagen se usa **ARG**, que sigue mas abajo.
 
 * -env Para ver las variables de entorno en un contenedor
 
 Para crear una variable con RUN
 * --env x=10
 * --env x=`pwd`

### Variables en el dockerfile

variable dir = a /data

Para poner las variables en el Dockerfile se usa ENV en mayúscula:
ENV dir=/data dir1=/data1
RUN mkdir $dir

Ejemplo:

	FROM ubuntu
	RUN apt-get update
	RUN apt-get install -y python3
	RUN echo 1.0 >> /etc/version && apt-get install -y git \
		&& apt-get install -y iputils-ping

	##WORKDIR
	RUN mkdir /datos
	WORKDIR /datos
	RUN touch f1.txt
	RUN mkdir /datos1
	WORKDIR /datos1
	RUN touch f2.txt
	
	##COPY
	COPY index.html .
	COPY app.log /datos
	
	##ADD
	ADD docs docs
	ADD r* /datos/
	ADD f.tar .
	
	##ENV
	ENV dir=/data dir1=/data1
	RUN mkdir $dir && mkdir $dir1
	
	##ENTRYPOINT
	ENTRYPOINT ["/bin/bash"]	
	
### Para Cambiar variables en el arranque. 
En el RUN se poner "-e"  mas el nombre de la variable como fue declarado en el dockerfile = su valor

    ENV TEXTO="Esto es un texto de ejemplo"
    CMD echo $TEXTO

    docker build -t image:v3 
    docker run --rm image:v3

    docker run --rm -e TEXTO="Esto es otro texto" image:v3


### Paso de argumentos ARG
La directiva ARG, es similar a ENV, pero permite pasar argumentos en el momento de la construcción de la imagen. Se usa con el RUN, es decir ARG se usa al momento de crear la imagen. 
A diferencia de ENV, ARG puede declararse sin valor y pasar el valor al construir la imagen. cpm --build-arg

Ejemplo:

  	##ADD
  	ADD docs docs
  	ADD r* /datos/
  	ADD f.tar .
  	
  	##ENV
  	ENV dir=/data dir1=/data1
  	RUN mkdir $dir && mkdir $dir1
  	
  	##ARG
  	ARG dir2
  	RUN mkdir $dir2
  	
  	##ENTRYPOINT
  	ENTRYPOINT ["/bin/bash"]	

El valor de dir2 declarado con ARG se carga al inicio con  
--build-arg | docker build -t image:v4 --build-arg dir2=/data2 .


### EXPOSE
Permite exponer puertos. Es para decir que una imagen esta usando alguno de los puertos que se exponen. 
No los publica y es necesario la opción -p. 

Nota personal: Al parecer es una directiva declarativa que sirve solamente para indicar qué se va a usar ese puerto. Si los puertos son conocidos como el puerto del servicio web, no se tendría necesidad de usar la directiva EXPOSE.


### VOLUME
Sirve para asociar un directorio como volumen en un contenedor. Para crear un volumen de manera automática. 
Similar a la opcción -v, pero aquí primero se agrega un directorio a una ruta en el contenedor y luego se usa la directiva VOLUME.

Por ejemplo:

	RUN apt-get install -y apache2
	EXPOSE 80
	inicia_apache_bash.sh

	ADD paginas /var/www/html
	VOLUME ["var/www/html"]
Dónde: 	
* inicia_apache_bash.sh es un script que inicia el apache y lanza bash (toda ves que la imagen no guarda servicios ni estados iniciados y se tiene que iniciar por el scritp)
* paginas es un directorio local, donde está el html
*/var/www/html es la ruta interna del contenedor y se tiene que iniciar el contenedor similar a esto:


        docker run -it -rm -p 8080:80 --name contweb1 imagen:v1



En el caso de que se tengan varios contenedores que vallan a utilizar los mismos volumenes, se puede usar:

--volumes-from + nombre_contenedor que  ya tiene los volumenes.
	
	Ejemplo:
	
	docker run -it --rm -p 9080:80 --volumes-from contweb1 --name contweb2 imagen:v1
	
	
	
### PARA SUBIR LAS IMAGENES A UN REPOSITORIO

Se deben seguir las reglas de nombre y etiquetado del repositorio.

	docker login + url del repositorio

si se usa docker login sin url, por defecto intentará subir la imagen a dockerhub, pero tiene que estar nombrada la imagen iniciando con la cuenta de usuario docker / nombre:tag

ejemplo: 

    halvarezps/imagen.v1



### docker push 

sirve para enviar una imagen ya creada y subirla a dockerhub. Ejemplo:

	docker push halvarezps/imagen:v1


### Renombrar o reetiquetar una imagen

Se usa la directiva tag  imagen _nvo_nombre_imagen. Ejemplo:

	docker tag imagen halvarezps/imagen:v1
	
	

#### Para guardar una imagen y llevarla a otro equipo se usa saf3

docker image save imagen -o nombre_para_guardar.tar

     Ejemplo:
     docker image save ubuntu -o mi_ubuntu.tar


Para cargar la imagen:

     docker image load -i mi_ubuntu.tar





### Para buscar imagenes en docker hub

#### Search

Para buscar alguna imagen que contenga httpd:

docker search  httpd

El resultado depende del número de estrellas que tiene en docker hub, y se puede de limitar el número de resultados con la opcción --limit


    docker search httpd --limit=5

Esto buscará las primeras 5 imagenes co mas estrellas




#### Para buscar solo imagenes oficiales

docker search httpd --filter is-official=true


#### para filtrar por imagenes automáticas

docker search httpd --filter is-automated=true


##### Para buscar por estrellas

docker search httpd --filter stars=30



## DOCKER FILE EN WINDOWS


    La primer linea debe ser:  
    #Usamos Windows server core
    FROM mcr.microsoft.com/windows/servercore:20H2
    
    #Instalamos el rol IIS
    RUN dism.exe /online/enable-feature /all /featurename:iis-webserver /NoRestart
    
    #Creamos un index.html
    RUN echo "MI primer dockerfile en WINDOWS" > c:\inetpug\wwwroot\index.html
    
    #lanzamos el comando cmd
    CMD ["cmd"]

Nota: no se debe de guardar como txt



## Sección 11: OPCIONES AVANZADAS DE DOCKER

CONFIGURACION DE CONTENEDORES

### ESTADISTICAS DE DOCKER

#### GESTION DE LA MEMORIA



* -m ó --memory
* --memory-swap
* --memory-swappiness   sirve para ajustar cuando se tiene que usar el swap de 0 a 100
* --memory-reservation tiene que ser menor que la cantidad de memria indicada con -m
* --kernel-memory
* --oom-kill-disable (out of memory) para que un servidor no sea borrado cuando el sistema se quede sin memoria


### GESTION DE CPU

* --cpus=<valor>
* --cpu-period=<valor>
* --cpu-quota=<valor>
* --cpu-shares
* -cpuset-cpus=<no de cpus iniciando en  0>  indica la cpu que se va a usar


## 163: Crear un entorno ZFS en Docker

1. Detener docker
sudo systemctl stop docker

2. copiar el directorio de docker como bak

sudo cp -r /var/lib/docker /var/lib/docker.old

3. Crear Zpool  y montarlo como lo necesita docker

   sudo zpool create -f zpool-docker -m /var/lib/docker /dev/sdb

   crea un zpool llamado zpool-docker en la ruta /var/lib/docker usando el dispositivo o disco /dev/sdb

   El directorio indicado en -m tiene que estar vacio.

4. ejecutar un df -h  debe de mostrar el disco montado 

5. iniciar docker 
   sudo systemctl start docker

   El driver de disco se define en /etc/docker/daemon.json; si no existe (entonces tiene configuracion por defecto)  el archivo se necesita crear.

   Edite y ponga lo siguiente:  
   {  
   "storage-driver": "zfs"   
   }

   y reinice docker. 

6. Ya deberia estar usando zfs




## 165: POLITICAS DE REINICIO DE DOCKER


* --restart  
		on-failure   Reinicia el contenedor si se ha terminado con algún código distinto de cero. número de intentos de reinicio, si se supera ya no intenta arrancar el contenedor.
		always       Siempre reiicia el contenedor. sI se reinicia docker el contenedor también arranca por defecto.
		unless-stoped   Como allways pero si se reinicia docker, el contenedor no arranca por defecto.
		no            no se reinicia de forma automática. Es la opción por defecto.
		
Ejemplo: 

    docker run -d --name t1 --restart always imagen

Nota: en docker inspect existe una propiedad llamada "RestartCount" que indica los reinicios del contenedor


## Sección 12: Contextos, Acceder a distintos entornos Docker

* Configuración de docker
  * Archivo de configuración
    * Contextos
	  * Uso de contexto


### Demonio

El demonio que se lanza para docker se llama _dockerd_ (Daemon CLI)

Se puede inciar docker con alguna de las opciones que tiene el demonio, o se puede modificar la configuración por defecto.

Para usar a mano dockerd es:

    dockerd COMMAND

Las opciones del comando están documentadas en la documentación de docker: [https://docs.docker.com/engine/reference/commandline/dockerd](https://docs.docker.com/engine/reference/commandline/dockerd)

Habituálmente estas opciones no se tocan, pero se puede modificar alguna opción a conveniencia.

Para modificar la configuración por defecto se hace en el archivo /etc/docker/daemon.json


### Archivo de configuración daemon.json

El archivo de configuración este archivo se encuentra en: _**/etc/docker/daemon.json**_

En el caso de los Windows este archivo se encuentra en: _**C:\ProgramData\docker\config**_

Es posible que no exista el archivo y eso significa que corre con opciones por defecto.

Para modificar una opción si el archivo no está creado se tiene que escribir en formato json, con las llaves de inicio y ciere, comillas, dos puntos, comas, etc. con par Properties: Valor

En los valores numéricos también llevan comillas según el formato json.

    Ejempo:
	{
	   "log-driver": "json-file",
	   "log-opts": {
	      "max-size": "5m",
		  "max-file": "3"
	}

Realizados los cambios, se guarda el archivo y se reinicia el servicio de docker:

    sudo systemctl restart docker

Nota: Si el archivo _**daemon.json**_ tiene un error de sintaxis o formato mostrará un mensaje de error.

Para verificar el archivo _**daemon.json**_ use el comando:

    dockerd --config-file /etc/docker/daemon.json --print-config

### Crear otro demonio de dockerd

Se puede usar la misma máquina donde corre docker para crear otro demonio. Tiene limitaciones: No se puede crear con el mismo ID, tampoco se puede usar el mismo puerto o socket. 

Las opciones se encuentan documentadas en:  [https://docs.docker.com/engine/reference/commandline/dockerd/#run-multiple-daemons](https://docs.docker.com/engine/reference/commandline/dockerd#run-multiple-daemons)

De igual forma se puede usar el comando **_dockerd_** pero lo normal sería usar un archivo de configuración **_daemon.json_**. mediante la opción **_--config-file=/ruta/daemon.json_**

    Ejemplo de ejecución mediante un script shell crear_docker.sh:
	dockerd \
        -H unix:///var/run/mi_equipo.sock \
        -p /var/run/mi_equipo.pid \
        --iptables=false \
        --ip-masq=false \
        --bridge=mi_red \ # se puede probar con "none"
        --data-root=/var/lib/mi_equipo \
        --exec-root=/var/run/mi_equipo

Para el ejemplo anterior se creará una nueva red

Sugerencia: se puede usar el comando _**nmcli con**_ (con de conecction) para ver las redes que tiene instalado el sistema.

Ejecute el script ya modificado para uso local y con permisos de ejecución con el comando:

./crear_docker.sh

Para conectarse a este nuevo docker con nuevo demonio se usan los contextos.


## Contextos


Se puede usar un docker cli se puede conectar a otros cluster, kubernetes o swarm, u otro servidor docker que estén en otros sitios.

El contexto predefinido se llama default y está marcado con un asterisco.

### Para listar los contextos de docker


docker context ls

### Cómo configurar una conexión 

Se debe de crear un contexto para realizar la conexión a ese otro demonio o docker remoto.

Para obtener la ayuda del contexto:
    **_docker context create --help_**

### Para crear un contexto

Por lo regular se usa la opción **_docker_** para indicarle dónde está la entrada del docker a conectar. 

La sintaxis es la siguiente:

    docker context create nombre_del_contexto --docker host=dirección 
	Dónde dirección puede ser un socket unix o una url

    Ejemplo:



Al presionar enter debería de mostrar un mensaje de éxito si no hay errores.

Crear el contexto no implica que ya se esté trabajando en él. Para eso se requiere cambiar al contexto.

El contexto se crea en el directorio del usuario en el archivo meta.json: .docker/contexts/meta/directorio-con-nombre-largo-alfanumérico/meta.json


### Para cambiar a otro contexto

Para cambiar a otro contexto se usa **_docker context use_**

docker use nombre_contexto

También se puede hacer un cat al archivo meta.json

### Para ver la configuración del contexto

docker context inspect nombre_del_contexto


### Para regresar al contexto inicial

docker context default



### Para conectarse a un servidor docker en otro equipo

Lo primero que se tiene que hacer es que el equipo cliente pueda conectarse al equipo servidor.

Para eso se tiene que crear una par de llaves ssh, y copiar la clave con copy-id ip-servidor-docker.  En el caso de los windows no se tiene disponible este comando, pero se tiene que asegurar que puede ser conectado copiando la llave y revisar los permisos necesarios.

#### Para crear la llave ssh

ssh-keygen

Eso genera las llaves id_rsa y id_rsa.pub en la ruta por defecto .ssh/

Luego se copia la clave pública con:

     ssh-copy-id -p 22  IP_Servidor_remoto



Luego hacer la conexión, por ssh y hacer un nuevo contexto al servidor.

### Crear nuevo contexto al servidor remoto

Para crear un nuevo contexto se usa: **_docker context_**

Sintaxis:  
docker context create docker-serv --docker "host=ssh://root@ip_servidor_docker" 
 
    Ejemplo:
	docker context create docker-serv --docker "host=ssh://root@172.31.17.229:22" El puerto se indica si es distinto al 22.
	
	Successfully created context "docker-srv"
	
Si es satisfactorio, también se puede ver el contexto con docker ls

luego cambiar de contexto usando: **_docker contex use docker-serv_**


### VS Code Contextos

Se pueden usar contextos en VS Code, en el menú del panel izquierdo con ícono de enchufe, y se puede dar clic derecho en la opción **_use_** del menú contextual para conectarse a otro contexto.


# Sección 13: DOCKER COMPOSE

### Trabajar con proyectos

Docker compose trabaja con proyectos, que tienen que tener su propio directorio en la máquina donde está instalado docker.
Docker compose le pone el nombre de los componentes (volumenes, red, etc) con un prefijo igual al nombre del proyecto. (Por lo que es buena idea nombrar el directorio de trabajo del contexto con el nombre del proyecto).
Para poder darle nombre a un proyecto creado con docker compose se usa la opción **-p**.

Ejemplo:  

**_docker compose -p nombre_proyecto up -d_**

Esto va a crear un proyecto llamado **"nombre_proyecto"**, sin embargo si el directorio donde estamos Sí es el contexto del proyecto, pero no se llama igual, docker compose buscará el proyecto en un directorio con el mismo nombre, por lo que al hacer un docker compose ps no mostrará nada.  Entonces se debe de usar la opción **-p**.

Ejemplo:  
 para ver el **"ps"** de **"nombre_proyecto"** en un directorio llamado **"directorio"**:

_**[alvarez@equipo_pc  directorio]$ docker compose -p nombre_proyecto ps**_


Si se está en un directorio que No es el contexto, se puede usar la opción -f para indicar el archivo yaml del proyecto,

Ejemplo:

docker compose -p nombre_proyecto -f ruta/al/yaml/compose.yaml ps

(con compose v2 o posterior, a veces si funciona sin indicar la opción -p)


### PS
Para ver los contenedores corriendo (se tiene que estar en el directorio del proyecto)
docker compose ps

### UP
Para levantar todos los contenedores, (que tienen un compose.yaml)
docker compose up con -d para segundo plano

### STOP
Para detener un contenedor
docker compose stop proyecto

### START
Para iniciar un contenedor específico
docker start sart proyecto

### PAUSE
Para poner en pausa un contenedor especifico
docker  pause proyecto

### UNPAUSE
Para quitar la pausa
docker compose unpause proyecto

### RM
Para borrar un contenedor (tiene que estar detenido)
docker compose rm proyecto

### DOWN
docker compose down (no es lo mismo que stop. Down borra el contedor)

### LOGS
Para ver los logs de los contenedores
docker compose logs contenedor




## COMPOSE.YAML

para construir una imagen con un docker file en el contexto se usa "build . "

Ejemplo:

     version: '3.9'
     services:
        web:
          build: ,
         image: halvarezps/web:v1
         container_name: web_frontal
         ports:
         - "80:80"
	 
	 

Para pasar variables se usa "enviroment"

    services:  
       database:  
          image: mysel  
          restart: always  
          enviroment:  
             MYSQL_DATBASE: db1  
             MYSQL_USER: usu  
             MYSQL _PASSWORD: secreto  
             MYSQL_ROOT_PASSWORD: secreto
		 

Para pasar las variables contenidas en un archivo, se quitan los dos puntos y se pone un signo de igual

Por ejemplo el archivo mysql.properties

     MYSQL_DATBASE=db1
     MYSQL_USER=usu
     MYSQL _PASSWORD=secreto
     MYSQL_ROOT_PASSWORD=secreto
     
     
    services:
       database:
          image: mysql
         restart: always
         env_file: mysql.properties 



### Dependencias

Se utiliza la cláusula depends_on y debajo se pone una lista de los servicios de los que depende el servicio en cuestión.

proyecto wordpress


   services:
   
    wordpress:
      image: wordpress
      restart: always
      ports:
         - 9090:90
      enviroment:
        WORDPRESS_DB_HOST: db-wp
        WORDPRESS_DB_USER: wp-user
        WORDPRESS_DB_PASSWORD: secreto
        WORDPRESS_DB_NAME: wp-db
      depends_on:
        - db-wp
        
    db-wp:
      image: mysql
      restart: always
      enviroment:
        MYSQL_DATABASE: wp-db
        MYSQL_USER: wp-user
        MYSQL_PASSWORD: secreto
        MYSQL_ROOT_PASSWORD: secreto
	  

## VOLUMENES

### Volumes
Volúmenes gestionados por el propio docker, se usa volumes:
(docker le pone un nombre largo a cada contenedor)
Se usa la cláusula **"volumes"** y luego se ponde la ruta que se usará dentro del contenedor.

Ejemplo:

   services:
   
    wordpress:
      image: wordpress
      restart: always
      ports:
         - 9090:90
      enviroment:
         WORDPRESS_DB_HOST: db-wp
         WORDPRESS_DB_USER: wp-user
         WORDPRESS_DB_PASSWORD: secreto
         WORDPRESS_DB_NAME: wp-db
      depends_on:
         - db-wp
      volumes:
         -/var/www/html
   
   
    db-wp:
      image: mysql
      restart: always
      enviroment:
         MYSQL_DATABASE: wp-db
         MYSQL_USER: wp-user
         MYSQL_PASSWORD: secreto
         MYSQL_ROOT_PASSWORD: secreto
      volumes: 
         -/var/lib/mysql
   


### Named Volumes

(nosotros definimos los nombres de los volumenes) Se nombran con la directiva "volumes", pero se pone el nombre del volumen mas dos puntos seguido de la ruta del volumen. Luego al final de nuevo se pone la cláusula volumes con la lista de los volúmenes que se definieron en los servicios.
Ejemplo:

    services:
    
     wordpress:
       image: wordpress
       restart: always
       ports:
          - 9090:90
       enviroment:
          WORDPRESS_DB_HOST: db-wp
          WORDPRESS_DB_USER: wp-user
          WORDPRESS_DB_PASSWORD: secreto
          WORDPRESS_DB_NAME: wp-db
       depends_on:
         - db-wp
       volumes:
         - wp:/var/www/html
    
      
     db-wp:
       image: mysql
       restart: always
       enviroment:
          MYSQL_DATABASE: wp-db
          MYSQL_USER: wp-user
          MYSQL_PASSWORD: secreto
          MYSQL_ROOT_PASSWORD: secreto
       volumes: 
          - db:/var/lib/mysql
    
    volumes:
       wp:
       db:
   


### BinMounts

Igual que el anterior, pero en vez del nombre, lleva la ruta del directorio. Tampoco se tiene que listar al final.
Ejemplo:

      services:
     
      wordpress:
        image: wordpress
        restart: always
        ports:
           - 9090:90
        enviroment:
           WORDPRESS_DB_HOST: db-wp
           WORDPRESS_DB_USER: wp-user
           WORDPRESS_DB_PASSWORD: secreto
           WORDPRESS_DB_NAME: wp-db
        depends_on:
           - db-wp
     
     ##Este es un binmount. No se debe de listar al final  
        volumes:
           -/root/datos/wp:/var/www/html
     
       
      db-wp:
        image: mysql
        restart: always
        enviroment:
           MYSQL_DATABASE: wp-db
           MYSQL_USER: wp-user
           MYSQL_PASSWORD: secreto
           MYSQL_ROOT_PASSWORD: secreto
     
     ##Este es un volumen nombrado, por lo que sí se lista al final
        volumes: 
           - db:/var/lib/mysql
     
     ##Sólo se lista el volumen nombrado
     volumes:
        db:
        
 
 

### Trabajo con redes

Se tiene que declarar en cada servicio cuales son las redes a las que se va a conectar, y al final se tienen que listar las redes.


    version: '3.9'
    services:
       svc-apache:
         image: httpd
       networks:
         - frontal
       ports:
        - "80:80"
       svc-tomcat:
       networks:
          - frontal
          - middleware
         image: tomcat 
        ports:
        - 8080:8080
    
      
    networks:
       frontal:
       middleware:

   
   
### Alias de red

Se usa la directiva **"aliases"** y se usa para nombrar un contenedor en la red  y se pueden referir a  él por el alias (una especie de netbios). Es decir el nombre del servicio puede ser invocado por su nombre (en el ejemplo db-wp) o por su alias de red (db-host).

     services:
      wordpress:
        image: wordpress
        restart: always
        ports:
           - 9090:90
        enviroment:
           WORDPRESS_DB_HOST: db-wp   #aquí se puede invocar por el alias db-host
           WORDPRESS_DB_USER: wp-user
           WORDPRESS_DB_PASSWORD: secreto
           WORDPRESS_DB_NAME: wp-db
         depends_on:
           - db-wp
         networks:
           -wp-network
     
     	  
      db-wp:
        image: mysql
        restart: always
        enviroment:
           MYSQL_DATABASE: wp-db
           MYSQL_USER: wp-user
           MYSQL_PASSWORD: secreto
           MYSQL_ROOT_PASSWORD: secreto
        networks:
           wp-network:
             aliases:
             - db-host
     
     networks:
        wp-network:
   
### PROYECTOS

Se usa la opción **-p** mas el nombre del proyecto. De prerencia el directorio donde está el contexto debe de tener el mismo nombre que el proyecto.  
   
Si se usa la opción **-f** mas la ruta del archivo compose.yaml. 

Con la opción -f el yaml puede tener cualquier nombre.




### PERFILES

Un perfil se puede aplicar a un contenedor para aplicar un servicio u otro dependiendo del perfil

En el ejemplo se va a iniciar una bd dependiendo si es desarrollo o producción. Esto se hace la cláusula _**profiles**_. Si un servicio no tiene esa cláusula significa que el servicio siempre va a arrancar.

En el ejemplo se muestra la sección de wordpress con pertenencia a dos perfiles. Notar que la base de datos que llama la variable _**WORDPRESS_DB_HOST: db-wp**_ utiliza el alias de la base de datos y que este alias es usado por los perfiles: desarrollo y producción.

Para iniciar un perfil se hace con el siguiente comando:

_docker compose --profile nombre_perfil up -d_


Ejemplo:

_docker compose --profile desarrollo up -d_
_docker compose --profile produccion up -d_

para detenerlo o borrarlo
docker compose --profile [desarrollo | produccion] [stop | down ]

    services:
     wordpress:
       image: wordpress
       restart: always
       ports:
          - 9090:90
       enviroment:
          WORDPRESS_DB_HOST: db-wp
          WORDPRESS_DB_USER: wp-user
          WORDPRESS_DB_PASSWORD: secreto
          WORDPRESS_DB_NAME: wp-db
        depends_on:
          - db-wp
        networks:
          -wp-network
        profiles:
           - desarrollo
           - produccion
    
    	##BD desarrollo
     db-wp:
       image: mysql
       restart: always
       container_name: desarrollo-bd
       enviroment:
          MYSQL_DATABASE: wp-db
          MYSQL_USER: wp-user
          MYSQL_PASSWORD: secreto
          MYSQL_ROOT_PASSWORD: secreto
       networks:
          wp-network:
              aliases:
              - db-host
       profiles:
          - desarrollo
    
    
    	##BD producción
     db-wp-prod:
       image: mysql
       restart: always
       container_name: produccion-bd
       enviroment:
          MYSQL_DATABASE: wp-db
          MYSQL_USER: wp-user
          MYSQL_PASSWORD: secreto
          MYSQL_ROOT_PASSWORD: secreto
       networks:
          wp-network:
             aliases:
              - db-host
       profiles:
           - produccion
    
    networks:
       wp-network:
       



### COMMAND
Se usa la cláusula "command" con corchetes [ ]

Este ejemplo usa un dockerfile, un docker compose.yaml, apache.sh y nginx.sh

Dockerfile:
    
    #descargar Ubuntu
    FROM ubuntu
    
    #Actualizar sistema
    RUN apt-get update
    
    #Instalar nginx
    RUN apt-get install -y nginx
    
    #Instalar apache
    RUN apt-get install -y apache2
    
    #Crear un lanzador para apache
    ADD apache.sh /usr/local/bin
    RUN chmod -x /usr/local/bin/apache.sh
    
    #Crear lanzador para nginx
    ADD nginx.sh /usr/local/bin/
    RUN chmod +x /usr/local/bin/nginx.sh
    
    #Este cmd no es importante para el ejemplo, solo envía un mensaje
    CMD ["echo", "prueba de comando"]
    
    ##Exponer puerto 80
    EXPOSE 80
    
    
##### Scripts:
    apache.sh
    
    #!/bin/bash
    apachectl -D FOREGROUND
    
    
    nginx.sh
    #!/bin/bash
    nginx -g "daemon off;"


compose.yaml:

    version: '3.9'
      services:
         web-apache:
           build: .
           image: comando: latest
           container_name: apache
           ports:
           - "8090:80"
           command: ["/usr/local/bin/apache.sh]
           profiles:
            - apache
            
         web-nginx:
           build: .
           image: comando:latest
           cotainer_name: nginx
           ports:
           - "8080:80"
           command: ["/usr/local/bin/nginx.sh"]
           profiles:
            -nginx



### Configurar memoria y CPU

Se usan las cláusulas **cpu_count, cpu_shares y mem_limit** como en docker normal.


Ejemplo:

     services:
	    stress1:
	       image: apache-stress
	       cpu_count:1 
	       cpu_shares: 512
	       mem_limit: 100m
	 
	 services:
	    stress2:
	       image: apache-stress
	       cpu_count:1 
	       cpu_shares: 1024
	       mem_limit: 50m


### Configurar política de reinicio

Al igual que con docker se usa la cláusula **restart** que tiene las siguientes opciones: 
* _always_  
  Siempre se reinicia el contenedor si este para. Si de detiene manualmente, el contenedor se reiniciará sólo si el demonio reinicia o si se inicia el contenedor manualmente.
  
* _unless-stopped_  
   Similar a _always_, excepto que cuando el contenedor se detiene (manualmente o de otra manera), no se reinicia incluso después de que se reinicia el demonio Docker.
   
* _on-failure[:max-retries]_  
  Reinicie el contenedor si sale debido a un error, que se manifiesta como un código de salida distinto de cero. Opcionalmente, limite la cantidad de veces que el demonio Docker intenta reiniciar el contenedor usando la opción :max-retries.
  
* _"no"_  
  No reiniciar el contenedor automáticamente. (el valor por defecto). Esta opción tiene que ir con doble comillas.

Ejemplo:
   
     services:
	    contenedor1:
		   image: imagen-restart
		   restart: unless-stoped
		contedor2:
		   image: imagen-restart
		   restart: always
		contedor3:
		   image: imagen-restart
		   restart: on-failure
		contedor4:
		   image: imagen-restart
		   restart: "no"



## Sección 14: Docker Registry

Los registros son los repositorios de imágenes. Se pueden crear registros propios, subir y bajar imágenes, y gestionar registros.

Por defecto se suben a dockerhub, pero pueden ser registros privados en una nube privada dentro de una organización.

### Crear un registro


Se puede bajar una imagen en dockerhub y descargar una imagen "registro", por defecto los servidores y contenedores que son para registros escuchan por el puerto 5000.

Luego de iniciar el contenedor se puede subir y bajar imágenes.

     Ejemplo:
	 
	 docker run -d -p 5000:5000 --restart always --name registry registry:2

### Subir una imagen al registro


Se tiene que tener una imagen etiquetada. 

La sintaxis es como la siguiente: 
docker tag host:puerto/imagen

    Ejemplo:
	
	docker tag apache-stress localhost:5000 /apache-stress

Luego se hace push

    docker push localhost:5000/apache-stress

Notas:

Se pueden tener varios contenedores para registro cambiando el nombre y el puerto de escucha.

El contenedor _"registry"_ no contiene un bash por lo que no se puede ingresar para interactuar dentro del terminal.


### Bajar una imagen del registro

docker pulll localhost:5000/apache-stress:latest


### Cómo nombrar un registro válido como no seguro


Los ejemplos anteriores se puso el nombre como localhost, pero esa parte es el nombre o IP del servidor donde se encentra.

También se puede bajar con el nombre del equipo. Para eso se tiene que habilitar la seguridad agregando la línea _"insecure-registries": ["mi_srv:5000", "otro_servidor:5001"  ]_ con la lista de repositorios en el archivo _/etc/docker/daemon.json._ Luego del cambio se debe de reiniciar el servicio de docker para que tome el cambio.


    {
	  "log-driver": "json-file",
	  "log-opts": {
	       "max-size": "5m",
		   "max-file": "3"
		`},
	  "insecure-registries": ["mi_srv:5000" ]
	}


### Montar volumen para el contenedor de registro

Al ser un contenedor se utiliza la opción _-v_ igual que con cualquier contenedor para indicar en el _run_ para indicar el volumen que se va a utilizar.


     Ejemplo:
	 
	 docker run --name registro1 -p 5003:5000 --restart always -v /registro_docker:/var/lib/registry -d registro

En este caso se usó un directorio /registro_docker para el mount bind.


### Registry API

Se usa el _API_ para manejar el registro de forma un poco mas amigable, porque el registro solo no tiene una interfáz de trabajo interactiva. Sin embargo, el _API_ tampoco ofrece una interfaz muy elaborada y se usan comandos web que ayudan a administrar y manejar el registro. Estos comandos de escriben en una url y como resultado se obtiene una salida en formato json.

La documentación del _API_ está [aquí](https://docs.docker.com/registry/spec/api/) 

Para referirse al _API_ se usa un navegador web con la notación "v2/comando". 

Por ejemplo: para ver el catálogo de un registro en un servidor local con el puerto 5002 se usa la siguiente url:

    localhost:5002/v2/_catalog

Otro ejemplo útil es para listar las etiquetas de alguna imagen que está contenida en el registro _(con puerto estandar 5000):_

    localhost:5000/v2/nombre_imagen/tag/list


## Instalar un certificado autofirmado para el acceso TLS desde la nube local

El autor generó un script bash para realizar la tarea. 

1. Primero genera el certificado
2. luego se genera un directorio (nodo1:443) copiando el certificado crt creado a este directorio.   
3. Se genera un archivo htpasswd con el contenido de usuario y contraseña. Para hacer esto el script esta ejecutando un contenedor con la imagen httpd y que se sabe que tiene instalado el comando htpasswd y finalmente genera el archivo htpasswd. Ya que termina se auto destruye.    
Pero si se corre en un gnu/linux y se tiene disponible el comando hpasswd no es neceario ejecutar el contenedor.

4. Se genera el contenedor pasando volumenes y variables.



    #!/bin/bash
    
    ##Notas acerca de este archivo
    ##Script de Apasoft training 
    ##registry.key y regostry.crt  es el nombre de la llave autofirmada.
    ##nodo1 es el nombre del equipo de pruebas de Apasoft. Se debería de sustituir por el nombre del servidor local a utilizar.
    
    ##Generamos una clave con certificado autofirmado
    openssl req  -newkey rsa:4096 -nodes -sha256 -keyout registry.key -x509 -days 365 -out registry.crt  -subj "/CN=nodo1/C=ES/ST=MA/L=MAdrid/O=Apasoft/OU=Training" -addext "subjectAltName = DNS:nodo1"
    
    
    ##Creamos un directorio en este PATH que se llame como nuestro nodo:puerto
    mkdir -p /etc/docker/certs.d/nodo1:443
    cp /root/seguridad/registry.crt /etc/docker/certs.d/nodo1:443
    
    #Generamos un fichero de usuario/password de tipo htpasswd
    docker run --rm --entrypoint htpasswd httpd -Bbn admin password > htpasswd   
	#Si se tiene un gnu/linux se puede usar esta línea:   
    #htpasswd -Bbn admin password > htpasswd
    
    
    ##Generamos el contenedor
    docker run -d -p 443:443 --restart=always --name registro_privado \
      -v /imagenes_docker:/var/lib/registry \
      -v /root/seguridad:/auth \
      -e REGISTRY_AUTH=htpasswd \
      -e REGISTRY_AUTH_HTPASSWD_REALM="Mi reino de Seguridad" \
      -e REGISTRY_AUTH_HTPASSWD_PATH=/auth/htpasswd \
       -e REGISTRY_HTTP_ADDR=0.0.0.0:443 \
      -v /root/seguridad:/certs \
      -e REGISTRY_HTTP_TLS_CERTIFICATE=/certs/registry.crt \
      -e REGISTRY_HTTP_TLS_KEY=/certs/registry.key \
      registry:2


Luego ya se puede subir una imagen a ese contenedor por el puerto 443. pero previamente se tiene que hacer login.




    Ejemplo:
	
	Se inicia la sesión:
	
    docker login nodo1:443
    Username: admin
	Password: password #el password no se debería de ver
	
	Se etiqueta la imagen:
	docker tag ubuntu nodo1:443/mi-ubuntu
	
	Se sube la imagen:
	docker push nodo1:443/mi-ubuntu




## Docker Swarm

* Docker Swarm  permite crear cluster de uno o mas nodos.
* Maneja el concepto de **_"servicios"_** o _**"mirco servicios"**_ que se despliegan de forma reolicada a lo largo del cluster. Estos micro servicios pueden ser un sitio web, una base de datos, Etc.

El cluster se componde de _**"nodos Managers"**_, uno o varios que gestionan y coordinan el cluster y se comunican etre sí para determinar el estado del cluster en todo momento.

También se componen de un número N de _**"nodos Workers"**_ para desplegar las tareas y los servicios.

Todos los Workers se comunican con los Manager en todo el cluster.

### Servicios 

Involucran varias tareas dentro de una aplicación mas amplia. Puede involucrar un servidor web, una base de datos, un front end. Etc. Se debe de indicar la imagen para crear los contenedores y qué comandos se van a lanzar.


## Crear un cluster Swarm

#### Crea un primer nodo con init

1. Revisar la ip que se tiene local. Con el fin de ponerla en el cluster

docker swarm inint --advertise-addr IP_local (la ip que revisamos)

Este será el nodo manager

arrojará un mensaje de cómo agregar mas nodos mediante un token


### Para ver los nodos que son parte del cluster

Se puede usar docker info y sale id, cantidad de nodos, si es el manager, etc.

Otra opción es usar:

docker node ls

### Agregar un nodo

Se usa docker swarm join-tocken + [worker] | [manager] 

se copia la salida y se pega en la estación que se va a agregar y se ejecuta

#### Nota los worker no funcionan con docker  node ls porque no es un nodo administrativo


### Mínima cantidad de nodos administrativos

Habrá un lider, y nodos en estado de Reachable, si el lider cae se promociona otro lider. Para que haya alta tolerancia a fallos se debe de tener un número impar de nodos manager. Entonces se requiere por lo menos 3 nodos manager. Este concepto se llama Raft.

Raft: nó impar. y funciona con la fórmula: N manager ((n-1) /2).

    Ejempo:
    Si hay 3 nodos  soporta pérdida de 1 nodos manager
    Si hay 5 nodos soporta pérdida de 2 nodos manager
    si hay 11 nodos soporta perdida de 5 nodos manager

Si hay mas nodos manager incorrectos el cluster falla.

Sin embargo se suele iniciar mínimo con tres manager.

### Promover o denegar nodos

Se puede cambiar de manager a worker o viceversa. Se usa denote o promote. Hay que tener cuidado de no quedarse sin nodos manager ni de tipo worker.

docker node promote ID_node | hostname

docker node demote ID_node | hostname

### docker node update 

Se obtiene la ayuda con docker node update --help

Otra forma de cambiar es usando update

     Ejemplo:
	 docker node update --role manager ID_node
	 docker node update --role worker
	 

### Tareas 
Cuando se crea un servicio en realidad lo que se hace es lanzar una o varias tareas de tipo réplica en los nodos.

Las tareas se ejecutan de manera independiente unas de otras y no tienen ninguna  implicación entre sí.


### Servicios

* Se usan para desplegar aplicaciones
* Normalmente un servicio es en realidad un micro servicio, es decir  un componente individual que pertenece a una aplicación mas grande:
* Puede ser un servidor web, base de datos,  o cualquier componente de la aplicación que ofresca un recurso al resto.
* Cuando se crea un servicio se debe de indicar una serie de características asociadas al mismo:  
  * Numero de réplicas del servicio que se a a ejecutar.
  * Configuración de CPU y memoria.
  * Posible red de tipo "overlay".
  * Puerto de acceso al servicio.


### Crear un servicio

Existen dos tipos de servicios: Replicados y Globales.

Los servicios globales tienen tantos servicios como nodos existan. Si se agrega un nodo adicional, también se agrega una réplica del servicio. Si se elimina uno también se elimina todo.

Los servicios replicados se definen en el número de réplicas, si un nodo se cae, el cluster intentará levantar otra réplica en otro nodo. Si el número de réplicas está completo ya no se crean mas réplicas aunque existan mas nodos, (a menos que se dañe uno, entonces si como se dijo se levantará otro en otro nodo). 



docker service create --name nombre_servicio imagen

    Ejemplo:
	docker service create --name apache httpd
	
#### Listar servicios

Para listar los servicios en el cluster se use la directiva **_service ls_** .

    Ejemplo:
	docker service ls

Se listarán todos los nodos que están en el cluster, su estado y los detalles.


#### Listar procesos

docker service ps nombre_servicio

#### Inspeccionar un servicio 

Simiilar al docker inspect normal, sólo que ahora para un servicio. Es opcional agregar pretty.

docker service inspect apache [pretty] 

#### Acceder a un servicio
Para acceder a un servicio se utilizan las cláususlas --publish y --target. Publish es el puerto por donde se va a publicar el servicio y target es el puerto donde escucha el contenedor internamente.

    Ejemplo:
	docker service create --name apache h --publish published=9000, target=80 nombre_imagen

se puede acceder al servicio con cualquier nombre de máquina que tenta el cluster (con la ip no siempre funciona bien) mas el puerto indicado. Todos los nodos vana a tener publicado el servicio en el mismo puerto y se puede acceder desde cualquier nodo.


### Redes Overlay

Cuando se crea el cluster se crean dos redes, una de tipo Overlay y otra de tipo bridge. Se pueden observar estas redes ejecutando el comando: **_docker network ls_**.
Las redes Overlay se distribuyen por todo el cluster a travez de todos los nodos de docker  y están por encima de otras redes del servidor. Permite que los contenedores estén disponibles.

También se pueden crear nuevas redes personalizadas de tipo overlay.

La red bridge que se crea es necearia para comunicar los demonios, y no se debe de borrar porque si no no va a funcionar.

Se pueden ver los detalles de estas redes haciendo un docker network ls y docker network inspect nombre_de_red


### Réplicas en los servicios name="id226"></a> 

Se utliza la cláusula **_--replicas_** para indicar cuantas réplicas va a tener el servicio:

    Ejemplo:
    docker service create --name apache_web -p 80:80 --replicas=3
	
	
Nota: en este ejemplo se usó la notación de puertos normal de docker, no la cláusula --published nombre_imagen

### Modificar réplicas 

Se utiliza la cláusula **_scale_** mas el número de replicas (mas, o menos para agregar o quitar).

    Ejemplo:
    docker service scale apache_web --replicas=5
	
Atención: NO se debería de replicar bases de datos, porque quedarían bases de datos independientes. Para eso se podría usar **_galera_** que mantiene las bases sincronizadas.

### Seccion 229: Impedir que un nodo reciba tareas

Un nodo puede ser tanto manager como worker. En algunos ambientes sencillos esto es muy común. Sin embargo es posible que solo se quiera que un nodo worker no despliegue tareas y es necesario bloquear para que reciba peticiones de tareas.

Para hacer esto se pone el nodo en estado **_drain_** haciendo **_update_** mas cláusula **_availability_**. Hay otro estado que es el de **_pause_**, esto es para que momentaneamente deje de recibir peticiones de tareas, pero es sólo momentáneo. Con el drain no ocurre eso, drain deja de recibir peticiones de tareas con lo que nunca será worker.

     Ejemplo:
	 docker node update --availability drain nodo_id

### Sección 230 Servicios globales

Para crear un servicio global se usa la directiva **_--mode global_**

    Ejemplo:
	docker service create --name web4 -p 8006:80 --mode global httpd
	
Si algún nodo está en modo **_drain_** este nodo será el único que no estará en modo worker.

Si se añade un nuevo nodo, luego de unirse automáticamente se agregará una tarea en ese nuevo nodo.


### Comportamiento ante fallas en las réplicas

Cuando un nodo falla, se apaga o no está disponible, entonces el cluster lo va a identificar con estatus down y luego intentará levantar otra instancia en otro nodo disponible.


### Sección 232: Actualizar un servicio

Para actualizar un servicio se realiza mediante la cláusula**_update_**. Esta cláususula tiene muchas opciones y está descrito en la página de referencia: [https://docs.docker.com/engine/reference/commandline/service_update](https://docs.docker.com/engine/reference/commandline/service_update)


Cuando se actualiza un servicio, se detiene y luego vuelve a iniciar el servicio con la nueva configuración.

¡Atención!: Es posible que se deba actualizar en un momento que el servicio no afecte mucho cuando se detenga.

    Por ejemplo, limitar el servicio a 10mb de memoria:   
	docker service update --limit-memory 10m web2

¡Advertencia!: Se se hace un _docker service inspect nombre_servicio_, habrá una sección al final **_"PreviousSpect"_** con la configuración anterior. En la parte superior estará el **_"Spect actual"_**. Hay que revisar la sección correcta.


### Sección 233: Etiquetar un nodo <a name="id233"></a>

Esto sirve para que los servicios desplieguen sus tareas en determinados nodos, o no la despliguen. Así evitar que las tareas lleguen a dónde no deberían de ir. Por ejemplo podría seleccionar un nodo en un rack de servidores más rápidos o con determinadas caracteríticas en vez de otros nodos de otro rack.

Esto se hace con la cláusula **_--label-add_**


    Ejemplo:
	docker node update  --label-add rack=1 id_nodo
	
Nota: Para obtener el id del nodo: `docker node ls`

Cuando se etiqueta un nodo, sin embargo, no aparece la etiqueta en un _docker node ls_, y no hay un comando como tal para listar los nodos con las etiquetas de cada nodo, pero se puede hacer un `docker node inspect id_nodo --pretty` y aparecerá una sección _label_. Esto requirá que se haga un inspect nodo a nodo de todos los nodos.

Una opción alternativa es usar un filtro con el comando **_docker node ls_**, opcción **_-f_**, para filtrar todos los que tengan esa etiqueta. Esto implica que se tiene que conocer la etiqueta para ponerlo en el criterio del filtro.  
La salida de ese comando será una lista de los nodos del cluster que contenga una etiqueta con el criterio del filtro, pero no mostrará la etiqueta, sólo listará los nodos que concuerden con el criterio de búsqueda, que tienen esa etiqueta, pero que no se muestra.



    Ejemplo:
	docker node ls -f node.label="alguna etiqueta, o parte de ella"

Para solventar esta carencia de comando, el autor del curso propone un una secuencia de comandos bash (no de docker) directo al sistema operativo:

~~~
docker node ls -q | xargs docker node inspect \
> -f '{{ .ID }} [{{ .Description.Hostname }}]: {{ .Spect.Labels }}'
~~~~

Estos comandos regresarán un listado de todos los nodos, y los que tengan alguna etiqueta cualquiera que sea la mostrará en la columna: **_map[ ]_**


### Sección 234: Restringir nodos en el despliegue de servicios <a name="id234"></a>

Una vez que los nodos han sido etiquetados como se describe en la sección anterior, se puede indicar que en esos nodos con tal etiqueta se debe desplegar una tarea.

Para esto se utiliza la directiva **_constraint node.labels.etiqueta==algún_valor_** 

¡Atención!: Hay que notar el doble igual == que se usa parecido al operador de comparación de 'C'. Para el ejemplo buscará cualquier nodo que contenga: rack=r1.

    Por ejemplo: 
	Desplegar un servicio nginx llamado web10 con éplica en los nodos que tengan una etiqueta rack=r1

    docker service create --name web10 --constraint node.labels.rack==r1 nginx

Si se crea un servicio con mas réplicas que la cantidad de los nodos que se han etiquetado, se lanzarán una o mas réplicas en cada uno de esos nodos indicados por la etiqueta, hasta cumplir con el número de réplicas. Para hacer esto se usan las directivas **_--replicas_** y **_--constraint_**

Por ejemplo: 	En un ambiente dónde se tienen dos nodos etiquetados como "rack=1" se van a desplegar seis réplicas de nginx.  
Docker Swarm levantará automáticamente de forma equitativa la cantidad de réplicas en cada nodo hasta completar 6. Es decir 3 réplicas en cada nodo:

`docker service create --name web11 --replicas=6 --constraint node.labels.rack==r1 nginx`

#### Desplegar un servicio en donde no se tenga una etiqueta

También se pueden utilizar las etiquetas para desplegar un servicio en los nodos que NO tengan esa etiqueta. Para eso se utiliza una sintáxis similar a la anterior pero con el operador de desigualdad: **_!=_** Esto desplegará un servicio en todos los nodos que no contengan la etiqueta del criterio.

Para el ejemplo se plantea el mismo ambiente anterior con nodos sin etiquetar y otros nodos con la etiqueta: rack=1. Se ilustrará en un modo de replicación globál para que sea más evidente el ejemplo.  
Esto desplegará el servicio en todos los nodos exepto los que tengan la etiqueta señalada rack=1 o los nodos que no sean **_"drain"_**.
    
	Ejemplo:
	docker service create --name web12 --mode global --constraint node.labels!=r1 nginx

Si se hace un `docker service ps web12` se podrá comprobar que sólo se deplegó el servicio en los nodos que no tienen etiqueta **_rack=r1_** ni tienen disponibilidad en **_drain_**


Nota Personal:  El autor del curso ha dado ejemplo de una etiqueta que incia con una palabra y termina en números. Queda pendiente de investigar si es posible usar etiquetas sólo numéricas, solo letras y es posible entoces como se debería de filtrar.


### Nodos Preferentes <a name="id235"></a>

Tomando en cuenta los ejemplos anteriores dónde se inician tareas en nodos que tengan alguna etiqueta: 
`docker service create --name web11 --replicas=6 --constraint node.labels.rack==r1 nginx`

Esto presupone que existen nodos que tienen la etiqueta indicada. Pero puede ocurrir que no existan nodos etiquetados y si se intentan iniciar las tareas en esos nodos sin etiqueta, docker swarm no encontrará nodos en dónde desplegarse, y entonces esas tareas quedarán como pendientes. 

Esto se podría comprobar con un `docker service ps nombre_servicio` 
y se observará que el estado actual es en _"Pendiente"_

Para evitar esta posible situación se puede hacer uso de la cláusula de preferencia: **_--placement-pref_** seguido de:  **_'spread=node.labels.etiqueta'_** . Esto hará que se intente realizar el despliegue en los nodos marcados con la etiqueta indicada, pero si no los encuentra, que los inicie en los nodos disponibles.

    Por ejemplo:
	docker service create --name web13 --placement-pref 'spread=mode.labels.desarrollo' --replicas=3 nginx

¡Atención!: El modo "preferente" **NO** puede ser utilizado **en modo Global**. Sólo se puede utilizar en servicios Replicados.

Nota personal: En el ejemplo anterior se observa que se etiquetó con la palabra "desarrollo" sin números como en el caso anterior. 


### Quitar nodos del cluster <a name="id236"></a>

El proceso consta de dos pasos: Quitar del cluster como tal, y luego eliminarlo desde un nodo manager.

Paso 1: Dentro del nodo a eliminar en la consola se usa la cláusula **_leave_**. 

   Por ejemplo:
   docker swarm leave
   
   Y tiene que regresar un mensaje similar a:
   Node left the swarm
   
Hecho esto el nodo habrá salido del cluster y se puede comprobar con el comando: 
   docker info
   
y en la sección **_swarm_** deberá decir: **_inactivo_**
 
Paso 2: En un nodo manager hacer un `docker node ls` el nodo estará en estatus _down_ porque lo acabamos de quitar. 
Para eliminarlo del Manager se usa la cláusula **_rm_** mas el id o nombre del nodo.

   Ejemplo:
   docker swarm rm id_nodo
   
Con eso queda eliminado y se puede comprobar de nuevo haciendo un `docker node ls`


## Sección 16: Kubernetes <a name="id237"></a>

Es un orquestador que tiene mas funcionalidad que docker swarm. Su nombre viene del griego que quiere decir: timón, como timón de barco.

Su arquitectura es la siguiente:
~~~
                     ________________________                   +-------------+ 
                    |         MAESTRO        |----------------->|    NODO     | 
                    |                        |                  |  Container  | 
                    |       SCHEDULER        |                  |  Runtimer   | 
                    |                        |                  |             | 
                    |       CONTROLLER       |                  |  POD   POD  | 
  CLIENTE   ---->   |                        |                  |  POD   POD  | 
                    |       API SERVER       |                  |             | 
                    |                        |                  |   KUBELET   | 
                    |    +---------------+   |                  |   KUBEPROXY | 
                    |    |      ETCD     |   |                  +-------------+ 
                    |    |    ALMACEN    |   | ----------------------> NODO     
                    |____|  CLAVE VALOR  |___|                                  
                         +---------------+     ----------------------> NODO     
~~~

Se tiene un nodo maestro, pero se pueden tener mas para alta disponibilidad.

Los clientes hacen una petición al nodo maestro a través de un API. Y el maestro contiene los siguientes elementos:   

* Sheduller. Es un planificardor para los nodos trabajadores, contiene la información necesaria para saber cuando o cómo mandar a cada nodo. Es decir cotiene la información para determinar que una tarea sea manda a un nodo o a otro y de qué forma.
* Controller. Es un proceso que regula el estado del cluster. Trabaja de forma cíclica para estar verificando el estado de forma regular y si encuentra un comportamiento erroneo inicia las acciones necesarias para intentar corregir el funcionamiento.
* APiServer. Son tareas de administración que se hacen dentro del cluster. Recibe llamadas de los clientes de tipo REST y estas son atendidas por el API Server. Si la petición es válida va intentar realizar los trabajos valiendose de los otros elementos anteriormente descritos.
* ETCD. Es un almacén de tipo clave-valor. El estado del cluster es mantenido en esta base de datos.

Nodos de trabajo o nodeworker. Trabajan colaborativamente con el maestro.

Los nodos tienen distintos componentes:

* Runtime container. Kubernetes soporte distintos tipos de nodos. Algunos son de tipo D como el de docker, rkt, lxd, y varios tipos, y este elemento.
* PODs. Es la unidad mínima de trabajo que puede ejecutarse en un Runtime. Pueden ser uno mas contenedores que trabajan de manera cooperativa dentro de un Runtime y es el elemento básico de trabajo. 
* Kubelet. Es un agente que se ejecuta en cada nodo y que se comunica con el Maestro. Sirve de interprete entre el maestro y esclavo o worker.
* Kubeproxy. Trabaja con los Pods, comunica un servicio como un punto de entrara hacia los pods.


Nota: Para probar plataformas se puede usar **_katacoda.com_**. Por ejemplo se puede probar kubernetes. Presenta escenarios de diversas plataformas para poder probar.


### Trabajar con minikube

Para trabajar con kubernetes se puede hablitar con docker desktop, para windows (o linux, pero habría que confirmar porque está en beta). Sin embargo para Linux es mejor usar Minikube, que tiene todo lo necesario.

Requisistos:

* 2 CPUs o mas
* 2 GB libres de ram
* 20 Gb de espacio en disco
* Conexión a internet
* Un manejador o hypervisor de contedores o maquinas virtuales:  
 *docker, Hyperkit, Hyper-V, KVM, Paralells, Podman, VirtualBox, VMware Fusion/Workstation.
* Kubectl

Cuando se instala se usa lo que se tiene instalado, docker, hyperv, etc y usa lo que encuentra al instalar. Minikube también instala kubectl, pero se recomienda que se insale antes.

#### Instalar kubeclt

Es la herramienta de línea de comandos para cluster que permite desplegar aplicaciones, manejar e inspeccionar recursos de cluster, vistas y bitácoras.

Para instalar kubeclt se debe de ir a la [sección Install tools](https://kubernetes.io/docs/tasks/tools/) de la página de kubernetes. Se encuentran instaladores para Linux, Mac, y Windows.   
Para Linux se encuentran tres formas de instalación: binarios, paquetes de las distribuciones, otros manejadores de paquetes: Snap o Homebrew.

El autor del curso, usó la primer forma de descargar binarios.

Nota: En Windows también está disponible en Chocolatey o Scoop.

Al instalar kubectl no tendrá ningún servidor disponible hasta que se instale.

### Instalar Minikube

El proceso de instalación se encuentra en (La Guía de Instalación)[https://minikube.sigs.k8s.io/docs/start/] para las principales plataformas, para otras plataformas se debe de revisar **_the release page_** de cada version. El modo de binarios, solamente es copiar los comandos y ejecutarlos en el linux local.

Una vez instalado swe tiene que iniciar el minikube, esto se hace con el comando:

    minikube start

Si falla el comando anterior, puede ser que sea necesario visitar la página de drivers. Depende de la herramienta de virtualización que se tiene (docker, kvm, etc.), podría ser necesario inicializar con un driver u otro. 

Un escenario posible es que se encuentre un ambiente con docker instalado y con VirtualBox o KVM instalado, y uno desea que se use uno u otro.

   Por ejemplo con VirtualBox:
   minikube start --drive virtualbox


### Despliegue de una aplicación

Si desea instalar una aplicación a partir de una imagen que ya se tiene creada, se tiene que realizar una implementación o despliegue, que puede de ser de dos formas: Imperativa ó Declarativa. 

Un DEPLOYMENT es la forma habitual de desplegar aplicaciones dentro de kubernetes. Describe el estado deseado de un POD y permite hacer escalamiento, recuperación, updates, replicacion y más. Dentro del DEPLOYMENT hay un objeto que se llama REPLICASET que se encarga de realizar las replicaciones. 

#### Modo Imperativo

Fuciona similar al docker run, ejecutando un comando con las opciones, clausulas como un comnando.

#### Modo Declarativo

Funciona con un archivo YAML donde se describe cómo debe de ser desplegada la implementación. 

Indendientemente de la forma, va a generar un pod con los contenedores y todo lo necesario.

### PODs

Aporta los recursos a los contenedores, uno o varios, pero lo normal es que tenga un solo contenedor. Tienen un conjunto de recursos: socket, volumenes, red, cpu, memoria y tienen una sola IP que comparte todos sus contenedores. Los POD se despliegn en un cluster.
Detro del cluster existe una red virtual por donde se comunican los direrentes PODS.

Los PODs no tienen estado, cuando no se usan desaparecen y luego se reconstruyen cuando de nuevo son necesarios.

Los POD por si solos no se escalan, no se recuperan ante caídas, no se actualizan ni hacen rollback complicados. Para eso es necesario un Deployment.

### Interactuar con kubernetes

Ejecutar `minikube status`
Deberia de salir que están corriendo los elementos. Si esto funciona ya está listo para interactuar con kubernetes. Apartir de ese momento todos los comandos iniciaran con kubectl.

### Crear un deplyment

Se puede obtener ayuda para crear un deployment con el siguiente comando:

`kubectl create deplyment --help`

La sintaxis para crear un deployment es la siguiente:
`kubeclt create deplyment nombre_del_deployment --image=nombre_imagen [--date][--replicas=No_replicas][--port]`

### Obtener los despliegues

Para listar los depliegues que se tienen se usa el comando:  
`kubeclt get deployments`

### Optener los PODs

Para listar los PODs que se tienen en se usa el siguiente comando:  
`kubeclt get pods`

### Eliminar un POD

Se elimina con la cláusula **_delete_**.  
`kubeclt delete nombre_pod`

Nota: Si el despliegue indica que hay algún número de pods, y si se elimina uno, kubernetes automáticamente va a levantar u nuevo pod para que cumpla con los números de pod que indica en el despliegue.


### Escalar un deployment

Se usa la cláusula **_scale_** mas la opción **_--replicas_**.

`kubeclt scatle deploy nombre_deploy --replicas=número_replicas`

   Por ejemplo:
   kubeclt scatle deploy web1 --replicas=8
   
El escalado puede ser aumentando o disminuyendo pods.

### Servicios

Los servicios son por los que se comunican los clientes con los pods.

CLIENTE --> SERVICIO --> PODS

Los servicios tienen IP fija, nombre fijo, puerto fijo

El cliente solicita al servicio y este enruta la solicitud a algún pod regresando el resultado.

#### Tipos de servicios

Existen tres tipos de servicio: ClusterIP, NodePort y LoadBalancer.

##### ClusterIP

Estos servicios están dentro del cluster y no son accesibles desde fuera.  Solamente se pueden acceder desde otros deployments u otros pods. Son útiles para backend como conectar bases de datos, Etc.

##### NodePort
Estos servicios son los que se utilizan para los fronted y es por donde se conecta un cliente.

##### LoadBalancer
Son como un nodeport, se usa para cloud y como su nombre hace balance de cargas.

#### Exponer un servicio

Se utiliza la cláusula **_expose_** y la opción **_type_** mas la cláusula **_--port_**

   Por ejemplo:
   kubeclt expose deploy web1 --type=ClusterIP --port=80 
   ó 
   kubeclt expose deploy web1 --type=NodePort --port=80 
   
¡Atención!: hay que tener cuidado porque es sensible a mayúsculas y minúsculas.

Cuando se expone un servicio ClusterIP permite acceder al recurso invocando el nombre del servicio como si fuera el nombre de la máquina.



### Obtener los ervicios

Para obtener los servicios que están corriendo en el cluster se usa la cláusula **_get service_**

`kubeclt get service`





# Referencia del curso
www.apasoft-training.com



