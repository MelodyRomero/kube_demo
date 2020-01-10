# Infraestructura

La infraestructura base del cluster de Kubernetes se compone de un servidor principal Master y sus Nodos (Workers).

![master](images/master-nodes.png)

<br>

## Master

Sus componentes principales son:

- [ETCD](#ETCD)
- [APIServer](#apiserver)
- [Scheduler](#scheduler)
- [Controller-Manager](#controller-manager)

Estos componentes toman las decisiones sobre el cluster.

<br>

## ETCD

ETCD se utiliza como almacén de respaldo de Kubernetes para todos los datos de cluster.
Su formato llave/valor permite que los componentes estén al tanto de cualquier cambio en el cluster.
Se recomienda que en la implementación del del cluster, los servidores ETCD estén separados de los servidores Master y se genere un sistema de respaldo para la data.
Más información en la [Documentación Oficial ETCD](https://etcd.io/docs/v3.4.0/)

<br>

## APIServer

Valida y configura datos para Pods, Services, ReplicaSets, entre otros.

<br>

## Scheduler

Determina en qué nodo alojar un pod y sincroniza su información con la configuración del servicio.
Estas desiciones son tomadas en base a:

- Recursos del nodo
- Restricciones de Hardware
- Restricciones de software
- Interferencia entre carga de trabajo
- Afinidad con nodos (taint)

<br>

## Controller-Manager

El administrador de controlador de Kubernetes es un demonio (servicio) que integra los bucles (secuencia repetida) de control central enviados con Kubernetes.
Cada uno de estos _loops_ corresponden al estado deseado de algún recurso de kubernetes, por ejemplo; un recurso _Service_ que balancea todos los Pods que cumplan con una _label_ en concreto.


<br>

---

# Nodes (Workers)

Son servidores que alojan los despliegues de aplicación. Son administrados por el servidor Master.
Los Workers son servidores que deben tener gran cantidad de recursos para alojar aplicaciones (máximo 110 pods). Estos nodos pueden ser facilmente reemplazados en el cluster Kubernetes y también pueden ser asignados para aplicaciones específicas.
Se componen de los siguientes servicios:

- [Kubelet](#kubelet)
- [Container runtime](#container-runtime)
- [Proxy](#proxy)

<br>

## Kubelet

Verifica que los contendores que forman parte del POD estén en ejecución.
Solo administra contenedores creados por Kubernetes y no contenedores creados manualmente por el servicio Docker.

<br>

## Container Runtime

Su función es ejecutar contenedores (Docker) y administrar sus imágenes mediante el demonio `containerd`.

<br>

## Proxy

Se encarga de realizar `forwarding` de conexiones TCP o UDP para exponer servicios
Mantiene las reglas de red de los nodos del cluster.

<br>

Más información sobre [Conceptos Kubernetes](https://kubernetes.io/docs/concepts/)