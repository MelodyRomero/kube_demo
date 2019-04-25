![Kubernetes](/images/kubernetes.png)

- [Namespace](#namespace)
- [Pod](#pod)
- [Service](#service)
- [Deployment](#deployment)
- [Persistent Volume](#persistent-volume)
- [Persistent Volume Claim](#persistent-volume-claim)
- [ReplicaSet](#replicaset)
- [Ingress](#ingress)

<br>

## Namespace

Son áreas de trabajo que agrupan diferentes objetos como `deployment`, `services`, `ingress`, etc. Estas áreas de trabajo son independientes entre si, por lo que no hay conflicto entre aplicaciones. <br>
En el caso de que se elimine un `namespace`, todos sus objetos se borrarán. Más información sobre [Namespaces](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/). <br>
Por defecto el cluster de kubernetes crea tres namespaces:

<br>

Nombre | Definición
---|---
default | Namespace por defecto para objetos que no especifiquen un namespace
kube-public | Namespace con permisos de lectura para cualquier usuario (incluyendo no autenticados)
kube-system | Namespace contiene objetos necesarios para el cluster Kubernetes

<br>

#### Ejemplo listar namespaces

```sh
$ kubectl get namespaces
NAME          STATUS    AGE
default       Active    43d
kube-public   Active    43d
kube-system   Active    43d
```

<br>

---

## PODs

Un Pod es la unidad mínima que se despliega y puede ser modificada o programada desde Kubernetes. Puede estar compuesta de uno o más contenedores que comparten storage y red. Más información sonbre [Pods](https://kubernetes.io/docs/concepts/workloads/pods/pod/).

<br>

#### Estructura de un Pod

```yaml
apiVersion: v1
kind: Pod ----> Tipo de objeto o recurso
metadata:
  name: my-app ----> Nombre del pod
  labels:
    app: nginx
spec:
  containers: ----> El/los contenedores del pod
  - name: nginx ----> Nombre del contenedor
    image: nginx:1.10.2 ----> Imagen del contenedor
    ports:
     - containerPort: 80 ----> Puerto del contenedor
```

<br>

---

## Service

Este objeto es el encargado de balancear la carga entre los distintos `pods` que tenga asociado. Expone sus servicios a través de un proxy para que las aplicaciones sean reconocidas en el cluster. <br>
Los `pods` objetivos de un `service` se determinan por el `label selector` configurado en este objeto. Más información sobre [Services](https://kubernetes.io/docs/concepts/services-networking/service/).

<br>

#### Estructura de un Service

```sh
apiVersion: v1
kind: Service ----> Tipo de objeto o recurso
metadata:
  name: my-service ----> Nombre del servicio
spec:
  selector:
    app: my-app ----> label del pod objetivo
  ports:
  - protocol: TCP ----> Protocolo a usar
    port: 80 ----> Puerto a exponer
    targetPort: 80 ----> Puerto del pod objetivo
```

<br>

---

