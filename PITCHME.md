---
@title[Capacitación]
# Capacitación Kubernetes

Pero antes...

---
@title[Docker]
## ¿Que es Docker?

<br>

Docker es un proyecto Opensource que permite desplegar aplicaciones en contenedores. Gestiona la creación y administración de contenedores.

+++
@title[Estructura Docker]
#### Estructura Docker
<p align="center"><img src="https://raw.githubusercontent.com/coneking/charla_kube/dia1/images/docker.png" width="600" /></p>

---
@title[Contenedores]
## Contenedores

<br>

Es una tecnología de virtualización que se basa en la ejecución de instancias de sistemas operativos desde un servidor. Los contenedores utilizan binarios, dependencias y recursos de este servidor. Para su creación usan una imagen base.

+++
@title[Ejemplo Contenedor]
#### Ejemplo de contenedor
<p align="center"><img src="https://raw.githubusercontent.com/coneking/charla_kube/dia1/images/container.png" width="500" /></p>
[DockerHub](https://hub.docker.com/)


+++
@title[Contenedor vs Virtual Machine]
#### Contenedor vs Máquinas Virtuales
<p align="center"><img src="https://raw.githubusercontent.com/coneking/charla_kube/dia1/images/vm-vs-container.png" width="800" /></p>

---
@title[Componentes]
## Componentes

- Docker Image |
- Docker Hub |
- Dockerfile |
- Docker Container |
- Docker Registry |
- Docker Swarm |

+++
@title[Información]

Más información sobre [Componentes Docker](https://github.com/coneking/docker#componentes-de-docker)

---
@title[Ejemplo]

## Ejemplo práctico

Creación de Contenedor

Note:
Esto es un ejemplo solamente.

<br>

Contenedor Nginx: <br>
docker container run --publish 81:80 nginx
<br>
Para salir de un contenedor: <br> Ctrl + P + Q.

