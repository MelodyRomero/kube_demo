---?image=images/kubernetes.png&size=auto 40%
@title[Inicio]

---
@title[Infraestructura]
## Infraestructura

La infraestructura base del cluster de Kubernetes se compone de un servidor principal Master y sus Nodos (Workers).

![master](images/master-nodes.png)

---
@title[Master]
## Master

Sus componentes principales son `etcd`, `APIServer`, `Scheduler`, `Controller-Manager`.
Estos componentes toman decisiones sobre el cluster 

+++
@title[etcd]
## Etcd

Etcd se utiliza como almacén de respaldo de Kubernetes para todos los datos de cluster.
Su formato llave/valor permite que los componentes estén al tanto de cualquier cambio en el cluster.

+++
@title[APIServer]
## APIServer

Valida y configura datos para Pods, Services, ReplicaSets, entre otros.

+++
@title[Scheduler]
## Scheduler

Determina en qué nodo alojar un pod y sincroniza su información con la configuración del servicio.
Estas desiciones son tomadas en base a:

- Recursos del nodo |
- Restricciones de Hardware |
- Restricciones de software |
- Interferencia entre carga de trabajo |

+++
@title[Controller-Manager]
## Controller-Manager

El administrador de controlador de Kubernetes es un daemon (Servicio) que integra los bucles (secuencia repetida) de control central enviados con Kubernetes.

---
@title[Nodes]
## Nodes (Workers)

Son servidores que alojan los despliegues de aplicación. Son administrados por el servidor Master.
Se componen de los servicios `kubelet`, `container runtime` y `proxy`.


+++
@title[Kubelet]
## Kubelet 

Verifica que los contendores que forman parte del POD estén en ejecución.
Solo administra contenedores creados por Kubernetes.

+++
@title[Container runtime]
## Container Runtime

Su función es ejecutar contenedores (Docker) y administrar sus imágenes.

+++
@title[Proxy]
## Proxy

Se encarga de realizar `forwarding` de conexiones TCP o UDP para exponer Services o PODs.

---
@title[Info]

Más información sobre [Conceptos Kubernetes](https://kubernetes.io/docs/concepts/)

---?image=images/kubespray-logo.png&size=auto 40%
@title[Inicio]

---
@title[Introducción Kubespray]
## ¿Qué es Kubespray?

Es un proyecto Open Source que permite implementar un cluster de kubernetes en Bare Metal, AWS, GCE y OS, el cual es una composición de PlayBook's de Ansible, inventario, herramientas de aprovisionamiento y conocimiento de tareas de administración de configuración de clústeres OS/Kubernetes genéricos.

---
@title[Soporte Kubespray]

**Kubespray** tiene soporte para las siguientes versiones de sistema operativo:

- Container Linux by CoreOS |
- Debian Jessie, Stretch, Wheezy |
- Ubuntu 16.04, 18.04 |
- CentOS/RHEL 7 |
- Fedora/CentOS Atomic |
- openSUSE Leap 42.3/Tumbleweed |

---
@title[Infraestructura]
## Infraestructura Escalable

La plataforma de Cluster Kubernetes se implementara sobre infraestructura **VMWare** y se deplegara de la siguiente forma:

- 3 Nodos Etcd |
- 4 Nodos Master |
- N+1 Nodos Worker |
- 1 Nodo de Deployment (Administración) |

+++
@title[Kuberntes - VMWare]

![infra](images/infra.png)


---
@title[Pruebas]
---
@title[Gracias]

# GRACIAS