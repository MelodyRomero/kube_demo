---?image=images/kubernetes.png&size=auto
@title[Inicio]

---
@title[Introducción Kubernetes]
## ¿Qué es Kubernetes?

También conocido como **k8s**, es una plataforma Open-Source que permite la administración de contenedores (orquestador).
<br>
Se encarga del despliegue, la mantención y la escalabilidad de las aplicaciones.

---
@title[Historia]
<p align="center"><img src="https://raw.githubusercontent.com/coneking/charla_kube/develop/images/googlelogo.png" width="500" /></p>

Desarrollado originalmente por Google en 2014 y liberado en el 2015 para la Cloud Native Computing Foundation es actualmente utilizado por grandes compañías como **Redhat**, **IBM**, **CoreOS**, **SAP** entre otras.

---
@title[Kubectl]
## Comando Kubectl

Para administrar Kubernetes utilizamos el comando **kubectl**, el cual interactúa con el cluster Kubernetes.
<br>

**Sintaxis:**
```
kubectl [command] [TYPE] [NAME] [flags]
```

+++
@title[Ejemplo Kubectl]
## Ejemplo

Listar los nodos del cluster Kubernetes:
```
$ kubectl get nodes
NAME           STATUS                     ROLES     AGE       VERSION
nodo1          Ready,SchedulingDisabled   master    20d       v1.8.0
nodo2          Ready                      <none>    20d       v1.8.0
nodo3          Ready                      <none>    20d       v1.8.0
```

---
@title[Win-Instalación1]
### Instalación Kubectl Windows (chocolatey)

Ejecutar desde PowerShell o CMD:
```
choco install kubernetes-cli
```

<br>

Revisar la versión de `kubectl`:
```
kubectl version
```

([instalación chocolatey](https://chocolatey.org/install))

+++
@title[Win-Instalación2]
### Instalación Kubectl Windows (binario)

Descargar el binario kubectl.exe
```
curl -LO https://storage.googleapis.com/kubernetes-release/release/v1.10.0/bin/windows/amd64/kubectl.exe
```

---
@title[Linux-Instalación1]
### Instalación Kubectl Linux (yum)

Crear repositorio
```
cat <<EOF > /etc/yum.repos.d/kubernetes.repo
[kubernetes]
name=Kubernetes
baseurl=https://packages.cloud.google.com/yum/repos/kubernetes-el7-x86_64
enabled=1
gpgcheck=1
repo_gpgcheck=1
gpgkey=https://packages.cloud.google.com/yum/doc/yum-key.gpg https://packages.cloud.google.com/yum/doc/rpm-package-key.gpg
EOF

yum install -y kubectl
```

+++
@title[Linux-Instalación2]
### Instalación Kubectl Linux (binario)

Descargar el binario kubectl
```
curl -LO https://storage.googleapis.com/kubernetes-release/release/v1.10.0/bin/linux/amd64/kubectl

chmod +x ./kubectl

sudo mv ./kubectl /usr/local/bin/kubectl
```

---
@title[Conceptos]
## Conceptos

- Namespace |
- Pod |
- Service |
- Deployment |
- PV y PVC |
- ReplicaSets |
- Ingress |

---
@title[Namespace]
## Namespace

Son áreas de trabajo que agrupan diferentes objetos como, deployment, services, ingress, etc.
<br>
Estas áreas de trabajo son independientes entre si.

```
$ kubectl get namespaces
NAME          STATUS    AGE
default       Active    43d
kube-public   Active    43d
kube-system   Active    43d
```

---
@title[Pod]
## Pod

Un Pod es la unidad más pequeña que se despliega y puede ser modificada o programada desde Kubernetes.
Puede estar compuesta de uno o más contenedores.

+++
@title[Ejemplo Pod]

Ejemplo Pod

```yaml
apiVersion: apps/v1
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

---
@title[Service]
## Service

Este objeto es encargado de balancear la carga entre los distintos pod que tenga asociado.
Expone sus servicios a través de un proxy.

+++
@title[Ejemplo Service]

Ejemplo Service

```yaml
apiVersion: v1
kind: Service ----> Tipo de objeto o recurso
metadata:
  name: my-service ----> Nombre del servicio
spec:
  selector:
    app: my-app
  ports:
  - protocol: TCP ----> Protocolo a usar
    port: 80 ----> Puerto a exponer
    targetPort: 80 ----> Puerto del pod
```

---
@title[Deployment]
## Deployment

Controla el despliegue de aplicaciones. Este objeto se encarga de las actualizaciones para Pods y ReplicaSets, manteniéndolos en un estado deseado.

+++
@title[Ejemplo Deployment]

Ejemplo Deployment

```yaml
apiVersion: apps/v1
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

---
@title[PV y PVC]
## PV y PVC

PersistentVolume (**PV**): Es una porción de storage que el administrador del cluster Kubernetes otorga al usuario.
<br>

PersistentVolumeClaim (**PVC**): Es el espacio que el usuario solicita para ser asignado a uno o más pods.

+++
@title[Políticas Recuperación]
### Políticas de Recuperación

**RETAIN:** Mantiene el espacio de disco provisionado si el PVC es eliminado por el usuario.

**DELETE:** Se elimina tanto el PV como el espacio en disco previamente provisionado.

+++
@title[Ejemplo PV]

Ejemplo PV

```yaml
apiVersion: v1
kind: PersistentVolume ----> Tipo de objeto o recurso
metadata:
  name: qa-jenkins-home ----> Nombre del PeristentVolume
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

---
@title[ReplicaSets]
## ReplicaSets

Es un controlador de replicación. Se basa en su estado `deseado` para mantener uno o más pod activos.
Es creado en el recurso `Deployment`.
<br>
Ejemplo 2 ReplicaSets
```
$ kubectl get replicasets
NAME                               DESIRED   CURRENT   READY     AGE
nginx-deployment-test-6c54bd5869   2         2         2         4h
```

---
@title[Ingress]
## Ingress

Es el objeto API que gestiona el acceso a los servicios de un cluster mediante reglas.
Negocia la comunicación del usuario con un servicio dentro del cluster Kubernetes.

<p align="center"><img src="https://raw.githubusercontent.com/coneking/charla_kube/develop/images/ingress.png" width="500" /></p>

+++
@title[Ejemplo Ingress]

Ejemplo Ingress

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

---
@title[Info]

Más información sobre [Conceptos Kubernetes](https://kubernetes.io/docs/concepts/)

---
@title[Gracias]

# GRACIAS