# Práctica

- [Buscar y descargar una imagen nginx](#buscar-y-descargar-una-imagen-nginx)
- [Iniciar un contenedor](#iniciar-un-contenedor)
- [Verificar su estado y si es visible](#verificar-su-estado-y-si-es-visible)
- [Eliminar solo el contenedor](#eliminar-solo-el-contenedor)

<br>


## Buscar y descargar una imagen nginx


Buscando imagen nginx

```sh
$ docker search nginx
INDEX       NAME                                                             DESCRIPTION                                     STARS     OFFICIAL   AUTOMATED
docker.io   docker.io/nginx                                                  Official build of Nginx.                        11237     [OK]       
docker.io   docker.io/jwilder/nginx-proxy                                    Automated Nginx reverse proxy for docker c...   1581                 [OK]
docker.io   docker.io/richarvey/nginx-php-fpm                                Container running Nginx + PHP-FPM capable ...   705                  [OK]
docker.io   docker.io/jrcs/letsencrypt-nginx-proxy-companion                 LetsEncrypt container to use with nginx as...   500                  [OK]
docker.io   docker.io/webdevops/php-nginx                                    Nginx with PHP-FPM                              125                  [OK]
docker.io   docker.io/zabbix/zabbix-web-nginx-mysql                          Zabbix frontend based on Nginx web-server ...   95                   [OK]
docker.io   docker.io/bitnami/nginx                                          Bitnami nginx Docker Image                      65                   [OK]
```

<br>

Descargando imagen nginx

```sh
$ docker pull nginx
Using default tag: latest
Trying to pull repository docker.io/library/nginx ... 
latest: Pulling from docker.io/library/nginx
27833a3ba0a5: Pull complete 
eb51733b5bc0: Pull complete 
994d4a01fbe9: Pull complete 
Digest: sha256:50174b19828157e94f8273e3991026dc7854ec7dd2bbb33e7d3bd91f0a4b333d
Status: Downloaded newer image for docker.io/nginx:latest
```

<br>

## Iniciar un contenedor 

Iniciando un contendor y exponiendo su puerto 

```sh
$ docker run -d --name test_web -p 80:80 nginx
7bd0daa29c046f5ade1d7160d4b221511f516948ee964c3508858fbf24a6226e
```

<br>

## Verificar su estado y si es visible (web)

Verificando el estado del contenedor

```sh
$ docker ps -f name=test_web
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                NAMES
7bd0daa29c04        nginx               "nginx -g 'daemon ..."   19 seconds ago      Up 18 seconds       0.0.0.0:80->80/tcp   test_web
```

<br>

Comprobar si es visible

```sh
curl localhost:80
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
    body {
        width: 35em;
        margin: 0 auto;
        font-family: Tahoma, Verdana, Arial, sans-serif;
    }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, the nginx web server is successfully installed and
working. Further configuration is required.</p>

<p>For online documentation and support please refer to
<a href="http://nginx.org/">nginx.org</a>.<br/>
Commercial support is available at
<a href="http://nginx.com/">nginx.com</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>
```

<br>

## Eliminar solo el contenedor

Deteniendo el contenedor

```sh
$ docker stop test_web
test_web

```

<br>

Eliminando el contenedor

```sh
$ docker rm test_web
test_web
```

<br>

---

# Práctica 2

- [Iniciar un contenedor apache](#iniciar-un-contenedor-apache)
- [Editar el contenedor](#editar-el-contenedor)
- [Crear una imagen usando el contenedor](#crear-una-imagen-usando-el-contenedor)
- [Crear un contenedor con la nueva imagen](#crear-un-contenedor-con-la-nueva-imagen)
- [Eliminar el contenedor y la imagen](#eliminar-el-contenedor-y-la-imagen)

<br>

## Iniciar un contenedor apache

Iniciando contenedor httpd

```sh
$ docker run -d --name prueba httpd
Unable to find image 'httpd:latest' locally
Trying to pull repository docker.io/library/httpd ... 
latest: Pulling from docker.io/library/httpd
27833a3ba0a5: Already exists 
7df2f4a2bf95: Pull complete 
bbda6f884d14: Pull complete 
4d3dcf503f89: Pull complete 
b2f11da8a23e: Pull complete 
Digest: sha256:b4096b744d92d1825a36b3ace61ef4caa2ba57d0307b985cace4621139c285f7
Status: Downloaded newer image for docker.io/httpd:latest
5d9fd858c551ca587532fc06dbe6a6664e15c6293388d97eceebfe3c09b30e79
```

<br>

## Editar el contenedor

Creando un archivo index y reemplazar el original del contenedor

```sh
$ echo "PRUEBA PRACTICA 2" > index.html; docker cp index.html prueba:/usr/local/apache2/htdocs/
```

<br>

## Crear una imagen usando el contenedor

```sh
$ docker commit prueba apache_custom
sha256:9f856e4f6dc031c5407e02a5b3717340cedc6708be33a630b62d9a072dd98d2d
```

<br>

Verificar la imagen creada

```sh
$ docker images apache_custom
REPOSITORY          TAG                 IMAGE ID            CREATED              SIZE
apache_custom       latest              9f856e4f6dc0        About a minute ago   132 MB
```

<br>

## Crear un contenedor con la nueva imagen

```sh
$ docker run -d --name nuevo_apache apache_custom
11734c7b4ec874064f9ab548765d2e3665241a879280c53c679f821dbc193b42
```

<br>

Comprobar el archivo index.html

```sh
$ docker exec -it nuevo_apache cat /usr/local/apache2/htdocs/index.html
PRUEBA PRACTICA 2
```

<br>

## Eliminar el contenedor y la imagen

Eliminando el contenedor

```sh
$ docker rm -f nuevo_apache
nuevo_apache
```

<br>

Eliminando la imagen `apache_custom`

```sh
$ docker rmi apache_custom
Untagged: apache_custom:latest
Deleted: sha256:9f856e4f6dc031c5407e02a5b3717340cedc6708be33a630b62d9a072dd98d2d
Deleted: sha256:e1154ce8e72f8fd467cd00399922d7ab3329a340c33a70e9c5f1e7dd04d93dac
```

<br>

---

# Práctica 3

- [Crear un archivo Dockerfile con sus pasos](#crear-un-archivo-Dockerfile-con-sus-pasos)
- [Crear una imagen (Dockerfile)](#crear-una-imagen-dockerfile)
- [Iniciar un contenedor con la nueva imagen](iniciar-un-contenedor-con-la-nueva-imagen)
- [Eliminar el contenedor y la nueva imagen](#eliminar-el-contenedor-y-la-nueva-imagen)

<br>

# Crear un archivo Dockerfile con sus pasos


1.- Crear el archivo `Dockerfile` con la siguiente información

```yaml
FROM nginx:1.11-alpine
COPY index.html /usr/share/nginx/html/index.html
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
```

<br>

2.- Crear un archivo `index.html`

```sh
$ echo "Prueba de Dockerfile" > index.html
```

<br>

## Crear una imagen (Dockerfile)

```sh
$ docker build -t mi_imagen:v1.1 .
Sending build context to Docker daemon  5.12 kB
Step 1/4 : FROM nginx:1.11-alpine
Trying to pull repository docker.io/library/nginx ... 
1.11-alpine: Pulling from docker.io/library/nginx
709515475419: Pull complete 
4b21d71b440a: Pull complete 
c92260fe6357: Pull complete 
ed383a1b82df: Pull complete 
Digest: sha256:5aadb68304a38a8e2719605e4e180413f390cd6647602bee9bdedd59753c3590
Status: Downloaded newer image for docker.io/nginx:1.11-alpine
 ---> bedece1f06cc
Step 2/4 : COPY index.html /usr/share/nginx/html/index.html
 ---> 442b17c25679
Removing intermediate container c6a74a075bef
Step 3/4 : EXPOSE 80
 ---> Running in 64ef6ffc9440
 ---> a0bb0772d0df
Removing intermediate container 64ef6ffc9440
Step 4/4 : CMD nginx -g daemon off;
 ---> Running in 628dd31c5d2f
 ---> 47568ca1c1f2
Removing intermediate container 628dd31c5d2f
Successfully built 47568ca1c1f2

```

<br>

## Iniciar un contenedor con la nueva imagen

```sh
$ docker run -d --name nginx_custom -p 81:80 mi_imagen:v1.1
0ce5ae6db96c1750878142ae8a50636061a8c5e86c7b6c1c8bed96f299c1c622
```

<br>

Comprobar si es visible

```sh
$ curl localhost:81
Prueba de Dockerfile
```

<br>

## Eliminar el contenedor y la nueva imagen

Eliminando contenedor

```sh
$ docker rm -f nginx_custom
nginx_custom
```

<br>

Eliminando imagen

```sh
$ docker rmi mi_imagen:v1.1
Untagged: mi_imagen:v1.1
Deleted: sha256:47568ca1c1f2dea634c7ac75fafb90892dd6ef42acc20be8a5ec2f18e1ab4385
Deleted: sha256:a0bb0772d0dfe32b271798911d7f3fc240d014beaf7832c6620a986c68987b13
Deleted: sha256:442b17c2567979c6f6a3b97bced42672b6fe858ad8a4762221d57ce79b5d9104
Deleted: sha256:dcefd9a45c4fed36d4442e8d4268aa7d7335a0c1bbbad6fb8105a4db2256828d
```


