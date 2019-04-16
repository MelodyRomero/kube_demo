# Práctica

* Buscar y descargar una imagen nginx
* Iniciar un contenedor
* Verificar su estado y si es visible (web)
* Eliminar solo el contenedor

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