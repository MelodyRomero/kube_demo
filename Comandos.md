# Comandos Docker

## Imágenes

Ver las imágenes que se han descargado:

```sh
$ docker image ls
```
o

```sh
$ docker images
```

<br>

Buscar imágenes de docker:

```sh	
$ docker search nombre_de_la_imagen
```
>Por defecto las imágenes son buscadas en Docker Hub.

<br>

Descargar una imagen:
	
```sh
$ docker pull nombre_de_la_imagen
```

***

<br>

## Inicio de contenedores.

Iniciar un contenedor:
	
```sh
$ docker run nombre_de_la_imagen
```
>Se le asignará un ID al contenedor y un nombre, por defecto si la imagen no la tenemos localmente la descargará.

<br>

Iniciar un contenedor y dejarlo activo en background:
	
```sh
$ docker run -d nombre_de_la_imagen
```
>La opción `-d` o `--detach` mantiene el contenedor ejecutándose en background.

<br>

Iniciar un contenedor, dejarlo activo y darle un nombre:
	
```sh
docker run -d --name "nombre" nombre_de_la_imagen
```

<br>

Iniciar un contenedor y pasarle variables de entorno:

```sh
$ docker run -d --name BD -e MYSQL_RANDOM_ROOT_PASSWORD=true mysql
```
>En este ejemplo se creó el contenedor `BD` y con el parámetro `-e` se le asignó la variable **MYSQL_RANDOM_ROOT_PASSWORD=true**, la cual crea una contraseña random para el usuario root de la bd mysql.

<br>

Inspeccionar un contenedor (Fecha de creación, Ip, su estado, volúmenes, puntos de montaje, etc):
	
```sh
$docker inspect nombre_del_contendor
```

***
<br>

Si por algun motivo, queremos acceder a la consola de un contenedor que ya esta corriendo, podemos ejecutar lo siguiente:

```sh
$ docker exec -it id_del_contenedor sh
```
>En este ejemplo, al final se indica el interprete de comandos para el contenedor (`sh`), dependiendo del contenedor también podría aceptar `bash`.

***

## Logs y estado del contenedor

Ver el log de un contenedor:
	
```sh
$ docker logs nombre_del_contenedor
```
>Es posible ver el log en tiempo real `logs -f` o entre otras cosas `logs --tail 10` para las últimas 10 líneas

<br>

Ver el estado de un contenedor

```sh
$ docker top nombre_del_contenedor
```

<br>

Mostrar el uso de un contenedor

```sh
$ docker stats nombre_del_contenedor
```

>Mostrará datos como el uso de CPU, Memoria, IO, etc.
Si no se especifica el nombre del contenedor, mostrará el estado de todos los contenedores.

***

## Mapear puertos

Iniciar un contenedor con un puerto específico, ejemplo conociendo que la imagen "redis" usa el puerto 6379, usarlo a través del puerto 6323

```sh	
$ docker run -d --name "nombre" -p 6323:6379 redis
```

<br>

Iniciar un contenedor y mapear un puerto aleatorio.

```sh	
docker run -d --name "nombre" -p 6379 nombre_de_la_imagen
```
<br>

Ver el puerto que tomó el contenedor:
	
```sh
$ docker ps
```
o

```sh
docker port "nombre" 6379
```

***

<br>

## Volúmenes

Al crear un contenedor la información por defecto se almacena dentro del mismo contenedor.
Por ende, si eliminamos el contenedor, la data guardada se eliminará y al tratar de levantar un nuevo contenedor, no tendrá la información anterior.
Para esto se puede `mapear` un volumen o directorio.
Por ejemplo, si en un contendor que guarda la información en el directorio `/opt/info`, lo podemos mapear a un directorio local por ejemplo `/var/container/info`.
Con esto si eliminamos el contenedor, la data seguirá existiendo en el S.O en la ruta local `/var/container/info` y al crear un nuevo contenedor y mapearlo, seguirá ocupando la información de antes.

```sh
$ docker run -d --name "nombre" -v "/var/container/info:/opt/info" nombre_de_la_imagen
```

<br>
 
Los contenedores se ejecutan en background con la opción -d, si a un contenedor se le da un comando para ejecutar, ejecutará la acción y dejará de correr.

**Ejemplo:**

```sh
$ docker run -it --name "nombre" ubuntu ps
PID TTY        TIME      CMD
  1   ?        00:00:00  ps
```
>En este ejemplo se creó un contenedor a partir de la imagen `ubuntu` y se le ordenó ejecutar el comando `ps`, luego el contendor dejó de correr.

<br>

Para crear un contenedor e ingresar a él, se puede crear de forma interactiva con la opción "-i -t" o "-it". Ejemplo:
	
```sh
$ docker run -it --name "nombre" ubuntu bash
```
>En este caso el contenedor se creará y podremos ingresar a él, pero al desloguearnos el contendor se detendrá. Para salir de este contenedor sin detenerlo, ya que se inició interactivamente (`-it`), pulsamos las teclas `ctrl + p + q `.

<br>

Crear un contenedor y eliminarlo automáticamente

```sh
$ docker run --rm -it --name test nginx ps
PID   USER     TIME   COMMAND
  1   root     0:00   ps
```
>En el ejemplo se crea un contenedor llamado `test`, se le solicita ejecutar `ps` y posteriormente se eliminará.

***

<br>


## Dockerfile

Dockerfile nos permite crear una imagen personalizada, siempre usando una imagen base.
Dockerfile es un achivo de texto plano donde se definen varios pasos que se requieren para la creación de una imagen la cual será posteriormente usada para crear un contenedor. 

**Ejemplo archivo Dockerfile:**

```yaml
FROM nginx:1.11-alpine
COPY index.html /usr/share/nginx/html/index.html
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
```

<br>

- **FROM:** Imagen base que se usará.
- **COPY:** Copia elementos "origen", "destino"... Si se trata de un archivo, se debe especificar el nombre del archivo destino.
- **EXPOSE:** Indica el puerto o puertos o rango de puertos que se expondrán en la imagen.
- **CMD:** Describe una secuencia de comandos que se ejecutarán al lanzar el contenedor que se cree con esta imagen.
- **RUN:** Pasos que se ejecutarán en la imagen antes de crearse.
- **WORKDIR:** Directorio en el que se ejecutarán los comandos.

<br>

Si el archivo es llamado `Dockerfile`, la creación de la imagen se ejecuta con el comando:

```sh
$ docker build -t "NOMBRE_DE_LA_IMAGEN" "PATH_DEL_ARCHIVO"
Sending build context to Docker daemon  5.12 kB
Step 1/4 : FROM nginx:1.11-alpine
 ---> bedece1f06cc
Step 2/4 : COPY index.html /usr/share/nginx/html/index.html
 ---> Using cache
 ---> c5de2891a602
Step 3/4 : EXPOSE 80
 ---> Using cache
 ---> b41f915da8da
Step 4/4 : CMD nginx -g daemon off;
 ---> Using cache
 ---> 489a0f2e27c0
Successfully built 489a0f2e27c0
```
>La opción `-t` es para poder darle un nombre y `tag` a la imagen, por ejemplo para especificar su versión; `mi_imagen:v1`. Si no se especifica el tag, se creará una imagen con su más reciente versión `latest`. 

<br>

Si el archivo tiene un nombre distinto a Dockerfile se debe espeficiar el nombre del archivo y su ruta com el parámetro `-f`.

```sh
$ docker build -t MI_IMAGEN:v1 -f opt/Mi_Dockerfile opt/
```


<br>

***

## Dockerignore

Para ignorar archivos o directorios que no se quieran incluir en la imagen a crear, se deben agregar al archivo `.dockerignore` (similar a .gitignore).

***
<br>

## Docker Network

Crear una red docker, una red sirve para generar un puente del cual varios otros contenedores se conectarán y obtendrán ip, además de resolver por el nombre con el que se crearon los contenedores.

**Ejemplo:**

Crear una red docker.

```sh 	
$ docker network create backend-network
```

<br>

Ahora crearemos dos contenedores asociándolos a la red `backend-network` a partir de una imagen `redis`.

```sh	
$ docker run -d --name=redis --net=backend-network redis
$ docker run -d --name=test --net=backend-network redis
```

<br>

Ambos contenedores están corriendo en background y si creamos un nuevo contenedor que esté dentro del mismo puente y genere un ping a uno de los contenedores "redis" o "test", este deberá responder.

```sh
$ docker run --net=backend-network alpine ping -c1 test
PING test (172.19.0.3): 56 data bytes
64 bytes from 172.19.0.3: seq=0 ttl=64 time=0.069 ms

--- test ping statistics ---
1 packets transmitted, 1 packets received, 0% packet loss
round-trip min/avg/max = 0.069/0.069/0.069 ms
```

Esto sucede porque al crear un contenedor y asociarlo a un puente, se guardará en el `resolv.conf` de los contenedores un servidor DNS 127.0.0.11. Hacer un ping a `redis` es lo mismo que a `redis.backend-network`.
Se pueden crear multiples redes e interconectar contenedores de distintas redes entre si.

<br>

**Ejemplo:**

Crearemos una red nueva:

```sh
$ docker network create frontend-network
```

<br>

Ahora usaremos el comando `connect` para adjuntar un contenedor ya existente a la nueva red.

```sh
$ docker network connect frontend-network redis
```

<br>

Si creamos un nuevo contenedor, web en este caso, y lo adjuntamos a la misma red se podrá comunicar con la instancia `redis` que se creó anteriormente.

```sh
$ docker run -d -p 3000:3000 --net=frontend-network katacoda/redis-node-docker-example
```

<br>

Consultamos con curl:

```sh	
$ curl docker:3000
```

<br>

Las redes se pueden listar y al igual que un contenedor se puede inspeccionar con los comandos:

```sh
$ docker network list
```

```sh	
$ docker network inspect "nombre_de_la_red"
```

<br>

Con docker network se puede proporcionar un alias a los contenedores, para tener una entrada DNS extra.
Por ejemplo crearemos una nueva red y le conectaremos nuestro contenedor llamado `redis` con el alias `db`.

```sh
$ docker network create frontend-network2
$ docker network connect --alias db frontend-network2 redis
```

<br>

Si inspeccionamos el contenedor redis veremos que aparte del id que le entrega docker, también tiene el alias "db" que le proporcionamos con docker network.

```sh
$ docker inspect redis
--> Salida
"frontend-network2": {
                   "IPAMConfig": {},
                   "Links": null,
                   "Aliases": [
                       "db",
                       "b2e0316bee85"
                   ],
```

<br>

Ahora si creamos un contenedor alpine con la nueva red y hacemos un ping al alias responderá correctamente.

```sh
$ docker run --net=frontend-network2 alpine ping -c1 db
PING db (172.21.0.2): 56 data bytes
64 bytes from 172.21.0.2: seq=0 ttl=64 time=0.084 ms

--- db ping statistics ---
1 packets transmitted, 1 packets received, 0% packet loss
round-trip min/avg/max = 0.084/0.084/0.084 ms	
```
<br>

De la misma forma en que se conectan contenedores a una red, también se pueden desconectar.
El siguiente ejemplo muestra como desconectar el contenedor `redis` de la red `fronend-network`.

```sh
$ docker network disconnect frontend-network redis
```
<br>