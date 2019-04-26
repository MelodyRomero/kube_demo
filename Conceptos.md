![Kubernetes](/images/kubernetes.png)

- [Namespace](#namespace)
- [Pod](#pod)
- [Service](#service)
- [Deployment](#deployment)
- [ReplicaSet](#replicaset)
- [Persistent Volume](#persistent-volume)
- [Persistent Volume Claim](#persistent-volume-claim)
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

## Pod

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

```yaml
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

## Deployment

Controla el despliegue de aplicaciones. Este objeto se encarga de las actualizaciones para Pods y ReplicaSets, manteniéndolos en un estado deseado. Al crear un `deployment` este creará automáticamente un objeto `replicaset` quien se encargará de desplegar los pods solicitados y su configuración. Más información sobre [Deployments](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/)

<br>

#### Estructura de un Deployment

```yaml
apiVersion: extensions/v1beta1
kind: Deployment ----> Tipo de objeto o recurso
metadata:
  name: nginx-deployment ----> Nombre del despliegue
  labels:
    app: nginx
spec:
  replicas: 1 ----> Cantidad de replicas
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
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

## Replicaset

Es un controlador de replicación. Se basa en su estado deseado para mantener uno o más pod activos. Es creado en el recurso Deployment pero también es posible . Más información sobre [Replicasets](https://kubernetes.io/docs/concepts/workloads/controllers/replicaset/)

<br>

### Estructura de un ReplicaSets

```yaml
apiVersion: extensions/v1beta1
kind: ReplicaSet ----> Tipo de objeto o recurso
metadata:
  name: test-frontend ----> Nombre del replicaset
  labels:
    app: guestbook
    tier: frontend
spec:
  replicas: 3 ----> Cantidad de pods a crear
  selector:
    matchLabels:
      tier: frontend  
  template:
    metadata:
      labels:
        tier: frontend
    spec:
      containers: ----> El/los contenedores del pod
      - name: php-redis ----> Nombre del contenedor
        image: gcr.io/google_samples/gb-frontend:v3 ----> Imagen del contenedor
```

<br>

---

## Persistent Volume

PV es una porción de storage que el administrador del cluster Kubernetes otorga al usuario. Es un volumen que se puede utilizar en los despliegues y tiene un ciclo de vida definido. Más información sobre [PersistenVolumes y PersistenVolumesClaim](https://kubernetes.io/docs/concepts/storage/persistent-volumes/)

<br>

### Políticas de Recuperación

Política | Definición
---|---
RETAIN | Mantiene el espacio de disco provisionado si el PVC es eliminado por el usuario
DELETE| Se elimina tanto el PV como el espacio en disco previamente provisionado

<br>

### Método de Acceso

Tipo | Acrónimo | Definición
---|---|---
ReadWriteOnce | RWO | El volumen puede ser montado en modo `lectura-escritura` sólo por un nodo
ReadOnlyMany | ROX | El volumen puede ser montado en modo `sólo lectura` por varios nodos
ReadWriteMany | RWX | El volumen puede ser montado en modo `lectura-escritura` por varios nodos

<br>

### Estructura de un Persistent Volume

```yaml
apiVersion: v1
kind: PersistentVolume ----> Tipo de objeto o recurso
metadata:
  name: pv-test ----> Nombre del PeristentVolume
spec:
  accessModes:
  - ReadWriteOnce ----> Tipo de acceso
  capacity:
    storage: 2Gi ----> Tamaño asignado
  persistentVolumeReclaimPolicy: Retain ----> Política de reclamación
  mountOptions: ----> Opciones de monataje
    - hard
    - nfsvers=3
  nfs: 
    path: /mnt ----> Punto de montaje de storage
    server: 172.17.0.2 ----> Servidor Storage
```

<br>

---

## Persistent Volume Claim

PVC es el espacio que el usuario solicita para ser asignado a uno o más pods. Está unido a un `persistentvolume` por su `selector` o indicando el nombre del `PV`. Más información sobre [PersistenVolumes y PersistenVolumesClaim](https://kubernetes.io/docs/concepts/storage/persistent-volumes/)

<br>

### Estructura de un Persistent Volume Claim

```yaml
apiVersion: v1
kind: PersistentVolumeClaim ----> Tipo de objeto o recurso
metadata:
  name: pvc-test ----> Nombre del PeristentVolumeClaim
spec:
  accessModes:
  - ReadWriteOnce ----> Tipo de acceso
  resources:
    requests:
      storage: 2Gi ----> Tamaño requerido
  volumeName: pv-test ----> Nombre del PV a asociar
```

<br>

---

## Ingress

Es el objeto API que gestiona el acceso a los servicios de un cluster mediante reglas. Negocia la comunicación del usuario con un servicio dentro del cluster Kubernetes. Más información sobre [Ingress](https://kubernetes.io/docs/concepts/services-networking/ingress/)

<br>

![Ingress](/images/ingress.png)

<br>

### Estructura de un Ingress

```yaml
apiVersion: extensions/v1beta1
kind: Ingress ----> Tipo de objeto o recurso
metadata:
  name: my-ingress ----> Nombre del ingress
spec:
  rules:
  - host: my-dns-url.cencosud.corp ----> DNS de acceso usuario
    http:
      paths:
      - backend:
          serviceName: my-service ----> Nombre del servicio a acceder
          servicePort: 80 ----> Puerto a exponer 
        path: /
```

