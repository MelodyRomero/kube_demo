---
@title[Inicio]
# Capacitación Kubernetes

---
@title[Docker Hub]
## Docker Hub

Es el repositorio por defecto de imágenes para contenedores Docker https://hub.docker.com.

Se pueden encontrar imágenes tanto oficiales como no oficiales y a través de un registro gratuito podemos aportar a la comunidad.

---
@title[Buscar imágenes]
## ¿Cómo buscar imágenes?

Mediante línea de comando se ejecuta lo siguiente:

```
$ docker search nginx
NAME                      DESCRIPTION                                     STARS     OFFICIAL   AUTOMATED
nginx                     Official build of Nginx.                        8341      [OK]
jwilder/nginx-proxy       Automated Nginx reverse proxy for docker c...   1314                 [OK]
richarvey/nginx-php-fpm   Container running Nginx + PHP-FPM capable ...   544                  [OK]
```


+++
@title[Descargar imagen]
## Descargar una imagen

Para descargar una imagen ejecutamos lo siguiente:

```
$ docker image pull httpd
Using default tag: latest
Trying to pull repository docker.io/library/httpd ...
latest: Pulling from docker.io/library/httpd
f2b6b4884fc8: Pull complete
Digest: sha256:b54c05d62f0af6759c0a9b53a9f124ea2ca7a631dd7b5730bca96a2245a34f9d
Status: Downloaded newer image for docker.io/httpd:latest
```
>**Nota:** Si no se especifica la versión de la imagen, se descargará la última versión (latest).

+++
@title[Iniciar contenedor]
## Iniciar un contendor

Para iniciar un contendor y mantenerlo en ejecución ejecutamos lo siguiente:

```
$ docker container run -d httpd
87595b724f012b29857a55283383c4214e07188dfe6a06553cc329282c336f0e
```

+++
@title[Eliminar contenedor]
## Eliminar un contenedor

Para eliminar un contenedor, debe estar detenido.
Ejecutamos lo siguiente:

```
$ docker container stop 87595b7
87595b7

$ docker container rm 87595b7
87595b7
```
>**Nota:** Para iniciar, detener, eliminar, etc. Se usa el id del contenedor o el nombre que se le asigna automáticamente. 

---
@title[Práctica]
# Práctica

- Buscar una imagen |
- Descargar e iniciar un contenedor con parámetros |
- Verificar su estado y si es visible (web) |
- Eliminar solo el contenedor |

---
@title[Crear imagen]
## Crear una imagen

Para crear una imagen podemos hacerlo basándonos en un contenedor que hayamos iniciado, ajustándolo a nuestra necesidad.
También podemos crear una imagen en base a un archivo (Dockerfile).

+++
@title[Creando imagen1]
## Creando imagen desde contenedor

Debemos tener un contenedor iniciado y modificarlo.
Posteriormente ejecutamos lo siguiente:

```
$ docker container ls
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                  NAMES
836d31ee29e2        httpd               "httpd-foreground"       8 minutes ago       Up 8 minutes        0.0.0.0:82->80/tcp     mi_apache

$ docker commit mi_apache mi_imagen
sha256:9f622d201933a6197310aa0995e746b3bbd736e04f186c39b1adb4b28494f316
```
---
@title[Práctica2]
# Práctica

- Iniciar un contenedor apache |
- Editar el contenedor |
- Crear una imagen |
- Iniciar un contenedor con la nueva imagen |
- Eliminar el contenedor y la imagen |
---

@title[Dockerfile]
## Dockerfile

Dockerfile define varios pasos que se requieren para la creación de una imagen.
Estos pasos se añaden a un archivo de texto, llamado de preferencia, Dockerfile.

+++
@title[Ejemplo Dockerfile]

### Ejemplo de archivo Dockerfile

```
# FROM Indica qué imagen base se usará
FROM ubuntu:16.04

# Se dejan los datos del creador de la imagen
MAINTAINER Nombre Apellido version: 1 correo@dominio.com

# Ejecuta tareas antes de crear la imagen
RUN apt-get update && apt-get install -y apache2 && apt-get clean && rm -rf /var/lib/apt/lists/*

# Asignación de variables
ENV APACHE_RUN_USER www-data
ENV APACHE_RUN_GROUP www-data
ENV APACHE_LOG_DIR /var/log/apache2

# Puerto o rango de puertos que se expondrán en la imágen
EXPOSE 80

# Comandos que se ejecutarán una vez se creé el contenedor
CMD ["/usr/sbin/apache2", "-D", "FOREGROUND"]
```

+++
@title[Creando imagen2]

## Crear imagen desde Dockerfile

Se deben definir los pasos para la imagen en un archivo llamado `Dockerfile`. Posteriormente se crea la imagen de la siguiente manera:

```
$ docker build -t nombre_de_la_imagen:version .
```

---
@title[Práctica3]
# Práctica Dockerfile

- Crear un archivo Dockerfile con sus pasos |
- Crear una imagen (Dockerfile) |
- Iniciar un contenedor con la nueva imagen |
- Eliminar el contenedor y la imagen |

---
@title[Gracias]

# GRACIAS