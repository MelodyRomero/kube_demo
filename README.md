# Kubernetes

- [Kubectl](#kubectl)
  - [Syntaxis](#syntaxis)
- [Instalación Kubectl](#instalacion-kubectl)
  - [Windows (chocolatey)](#windows-chocolatey)
  - [Windows (binario)](#windows-binario)
  - [Linux (Centos/RHEL/Fedora)](#linux-centosrhelfedora)
  - [Linux (Debian)](#linux-debian)
  - [Linux (binario)](#linux-binario)


## Kubectl

Para administrar Kubernetes utilizamos el comando `kubectl`, el cual interactúa con el cluster Kubernetes.
Más información sobre el comando [Kubectl](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands)

### Syntaxis

```sh
$ kubectl [command] [TYPE] [NAME] [flags]
```
<br>

Ejemplo

Listar los nodos del cluster

```sh
$ kubectl get nodes
NAME           STATUS                     ROLES     AGE       VERSION
nodo1          Ready,SchedulingDisabled   master    20d       v1.10.2
nodo2          Ready                      worker    20d       v1.10.2
nodo3          Ready                      worker    20d       v1.10.2
```

<br>

Listar los pods del namespace `default`

```sh
$ kubectl get pods
NAME                           READY   STATUS    RESTARTS   AGE
golang-test-7759c599dc-x4f66   1/1     Running   0          22h
hello-56d89fcd87-rgrs7         1/1     Running   0          22h
```

<br>

---

## Instalación Kubectl

### Windows (chocolatey)

Ejecutar desde PowerShell o CMD

```sh
$ choco install kubernetes-cli
```

<br>

Revisar la versión de kubectl

```sh
$ kubectl version
```
>**Nota:** Aquí información sobre [Instalación chocolatey](https://chocolatey.org/install)

<br>

### Windows (binario)

Descargar binario kubectl.exe

```sh
$ curl -LO https://storage.googleapis.com/kubernetes-release/release/v1.14.0/bin/windows/amd64/kubectl.exe
```

<br>

### Linux (Centos/RHEL/Fedora)

```sh
$ cat <<EOF > /etc/yum.repos.d/kubernetes.repo
[kubernetes]
name=Kubernetes
baseurl=https://packages.cloud.google.com/yum/repos/kubernetes-el7-x86_64
enabled=1
gpgcheck=1
repo_gpgcheck=1
gpgkey=https://packages.cloud.google.com/yum/doc/yum-key.gpg https://packages.cloud.google.com/yum/doc/rpm-package-key.gpg
EOF

$ yum install -y kubectl
```

<br>

### Linux (Debian)

```sh
$ sudo apt-get update && sudo apt-get install -y apt-transport-https
$ curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -
$ echo "deb https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee -a /etc/apt/sources.list.d/kubernetes.list
$ sudo apt-get update
$ sudo apt-get install -y kubectl
```

<br>

### Linux (binario)

Descargar binario kubectl

```sh
$ curl -LO https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/linux/amd64/kubectl
```
