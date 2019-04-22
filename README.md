# Kubernetes

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

Descargar el binario kubectl.exe

```sh
$ curl -LO https://storage.googleapis.com/kubernetes-release/release/v1.14.0/bin/windows/amd64/kubectl.exe
```

