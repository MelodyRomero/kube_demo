# Kubectl comandos

## Información de configuración

Consultar por los cluster del archivo `config`

```sh
$ kubectl config get-contexts
CURRENT   NAME      CLUSTER   AUTHINFO    NAMESPACE
          cl_prod   cl_prod   admin
*         cl_lab    cl_lab    admin-lab
```
>El `*` indica el cluster en el que estamos actualmente.

<br>

Cambiar de cluster

```sh
$ kubectl config use-context cl_prod
```
>En el ejemplo indicamos que cambiamos al cluster `cl_prod`

<br>

Consultar por el cluster en el que nos encontramos.

```sh
$ kubectl config current-context
cl_prod
```

<br>

Eliminar un cluster del archivo `config`

```sh
$ kubectl config delete-context cl_lab
```

<br>

---

## Información sobre nodos

Consultar por los nodos del cluster

```sh
$ kubectl get nodes
NAME    STATUS                     ROLES     AGE       VERSION
nodo1   Ready,SchedulingDisabled   <none>    20d       v1.10.0
nodo2   Ready,SchedulingDisabled   master    20d       v1.10.0
nodo3   Ready                      <none>    20d       v1.10.0
nodo4   Ready                      <none>    20d       v1.10.0
```
>El parámetro `get` sirve para mostrar información de los recursos del cluster (nodos, pods, services, deployments, etc).

<br>

Mostrar el uso de los nodos

```sh
$ kubectl top nodes
NAME           CPU(cores)   CPU%      MEMORY(bytes)   MEMORY%   
nodo1          152m         7%        923Mi           53%       
nodo2          160m         8%        911Mi           52%       
nodo3          332m         8%        2459Mi          31%       
nodo4          218m         5%        1784Mi          23%             
```

<br>

Desactivar un nodo del cluster (unschedulable)

```sh
$ kubectl cordon nombre_del_nodo
```
>El estado cordon le indicará al servidor `master` que este nodo no prestará servicios para deployar aplicaciones.<br>
Las aplicaciones del nodo seguirán activas hasta que se bajen manualmente. 

<br>

Activar un nodo del cluster (schedulable)

```sh
$ kubectl uncordon nombre_del_nodo
```

<br>

Desactivar un nodo para realizar mantenimiento

```sh
$ kubectl drain nombre_del_nodo
```
>A diferencia de la opción `cordon`, además de dejarlo en dicho estado, moverá todos los `Pods` que tenga activos a otro nodo del cluster.

<br>

Eliminar un nodo del cluster

```sh
$ kubectl delete nombre_del_nodo
```
>El parámetro `delete` sirve para elimianr recursos del cluster (nodos, pods, services, deployments, etc).

<br>

---

## Información sobre recursos

Los siguientes comandos son válidos para todos los recursos del cluster kubernetes, sus salidas pueden ser distintas dependiendo del recursos consultado.


### Recursos Kubernetes

* certificatesigningrequests (aka 'csr')
* clusters (valid only for federation apiservers)
* clusterrolebindings
* clusterroles
* componentstatuses (aka 'cs')
* configmaps (aka 'cm')
* daemonsets (aka 'ds')
* deployments (aka 'deploy')
* endpoints (aka 'ep')
* events (aka 'ev')
* horizontalpodautoscalers (aka 'hpa')
* ingresses (aka 'ing')
* jobs
* limitranges (aka 'limits')
* namespaces (aka 'ns')
* networkpolicies
* nodes (aka 'no')
* persistentvolumeclaims (aka 'pvc')
* persistentvolumes (aka 'pv')
* pods (aka 'po')
* poddisruptionbudgets (aka 'pdb')
* podsecuritypolicies (aka 'psp')
* podtemplates
* replicasets (aka 'rs')
* replicationcontrollers (aka 'rc')
* resourcequotas (aka 'quota')
* rolebindings
* roles
* secrets
* serviceaccounts (aka 'sa')
* services (aka 'svc')
* statefulsets
* storageclasses
* thirdpartyresources

>**Nota:** `aka` indica el alias del comando, ejemplo: `horizontalpodautoscalers = hpa`

<br>

Consultar la documentación de algún recurso del cluster

```sh
$ kubectl explain nombre_del_recurso
```

<br>

Listar uno o varios recursos del cluster

```sh
$ kubectl get nombre_del_recurso
```
>Para mostrar más información se puede agregar el parámetro `-o wide` a la consulta. Ej: `kubectl get pods -o wide`.

<br>

Mostrar información detallada de uno o varios recursos del cluster

```sh
$ kubectl describe nombre_del_recurso
```

<br>

Editar algún recurso del cluster

```sh
$ kubectl edit nombre_del_recurso
```

<br>

Eliminar algún recurso del cluster

```sh
$ kubectl delete nombre_del_recurso
```

<br>

---

# Administración de Deployments


# Administración de Pods

Los siguientes comandos son útiles para el manejo de Pods del cluster kubernetes.

Listar los pods de todos los namespaces del cluster

```sh
$ kubectl get pod --all-namespaces
```
>Para mostrar más información se puede agregar el parámetro `-o wide` a la consulta. Ej: `kubectl get pods -o wide`.

<br>

Listar los pods de un namespaces del cluster

```sh
$ kubectl -n namespace get pod
```

<br>

### Manejo de logs

* Ver logs de un pod: `kubectl logs nombre_del_pod`
* Ver logs en tiempo real: `kubectl logs -f nombre_del_pod`
* Ver las últimas 20 líneas de log: `kubectl logs --tail=20 nombre_del_pod`
* Ver logs de todos los contenedores de un pod: `kubectl logs nombre_del_pod --all-containers=true`


<br>

### Copia de archivos

Copiar un archivo desde /tmp local a /opt de un pod

```sh
$ kubectl cp /tmp/archivo nombre_del_pod:/opt	

```

<br>

Copiar un archivo desde /tmp local a /opt de un contenedor de un pod

```sh
$ kubectl cp /tmp/archivo nombre_del_pod:/tmp -c nombre_del_container 
```

<br>

Copiar un archivo desde /opt de un pod a /opt local

```sh
$ kubectl cp nombre_del_pod:/opt/archivo /tmp	

```

<br>


### Ejecución dentro de un Pod

Ejecutar un comando desde un pod

```sh
$ kubectl exec nombre_del_pod comando	
```

<br>

Ejecutar un comando desde un pod en un contenedor específico

```sh
$ kubectl exec nombre_del_pod -c nombre_del_container comando	
```

<br>

Ingresar a un pod

```sh
$ kubectl exec -i nombre_del_pod sh	
```
>Se debe agregar al final un interprete de comandos como `bash` o `sh`.

<br>


### Exponer un pod


```sh
$ kubectl port-forward nombre_del_pod puerto_local:puerto_pod
```
>Expondrá el pod en el equipo local al puerto especificado para hacer pruebas antes de su implementación final.

<br>




