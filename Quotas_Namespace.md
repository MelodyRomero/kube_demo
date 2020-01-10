# Cuotas por Namespace

Para Limitar el uso de los recursos de Kubernetes se deben implementar cuotas a cada `namespace`. Además de restringir el uso del cluster k8s se busca instruir a los desarrolladores en el uso de *límites* en cada uno de sus despliegues. <br>
Por defecto los `namespaces` dentro de k8s no tienen límites de uso de CPU y Memoria, por lo que una aplicación mal configurada podría usar todos los recursos disponibles de un Nodo provocando el colapso del mismo dentro del cluster.

<br>

# ResourceQuota 

El objeto/recurso `resourcequota` permite asignar límites para el consumo de recursos y a la cantidad de objetos que se pueden crear por namespace. Puede limitar la suma total de recursos (CPU y Memoria) en un namespace.


### Detalle de párametros ResourceQuota (recursos)

Recursos | Descripción |
---|---|
cpu	| Para todos los Pods, la suma de solicitudes de CPU no puede exceder este valor.
limits.cpu | Para todos los Pods, la suma de los límites (limits) de la CPU no puede exceder este valor.
limits.memory | Para todos los Pods, la suma de los límits (limits) de Memoria no puede exceder este valor.
memory | Para todos los Pods, la suma de solicitudes de Memoria no puede exceder este valor.
requests.cpu | Para todos los Pods, la suma de solicitudes (requests) de CPU no puede exceder este valor.
requests.memory | Para todos los Pods, la suma de solicitudes (requests) de Memoria no puede exceder este valor.

<br>

## Ejemplo de Cuotas

Creamos el namespace llamado `prueba`.

```sh
$ kubectl create ns prueba
```
<br>

Creamos un recurso tipo `ResourceQuota` en el archivo quota_prueba.yaml.

```yaml
apiVersion: v1
kind: ResourceQuota
metadata:
  name: limite-recursos
spec:
  hard:
    limits.cpu: "1"
    limits.memory: 2Gi
    requests.cpu: "0.5"
    requests.memory: 512Mi
    pods: "5"
```

<br>

Aplicamos la configuración al namespace creado.

```sh
$ kubectl -n prueba create -f quota_prueba.yaml
```
<br>

Verificamos la cuota asignada al namespace

```sh
$ kubectl describe ns prueba
Name:         prueba
Labels:       <none>
Annotations:  cattle.io/status={"Conditions":[{"Type":"InitialRolesPopulated","Status":"True","Message":"","LastUpdateTime":"2018-07-26T20:24:55Z"},{"Type":"ResourceQuotaInit","Status":"True","Message":"","LastUpda...
              lifecycle.cattle.io/create.namespace-auth=true
Status:       Active

Resource Quotas
 Name:            limite-recursos
 Resource         Used  Hard
 --------         ---   ---
 limits.cpu       0     1
 limits.memory    0     2Gi
 pods             0     5
 requests.cpu     0     500m
 requests.memory  0     512Mi

No resource limits.

```
<br>

En este ejemplo se crea una cuota con las siguientes especificaciones:

Resource |Requests| Limits |
---|---|---|
Memory | 512Mi | 2Gi |
CPU | 500m | 1 |
Pods |X| 5 |

<br>

Esto quiere decir que por ejemplo se podrían crear un máximo de 5 Pods con las siguientes especificaciones:

requests.cpu | requests.memoria | limits.cpu | limits.memoria |
---|---|---|---|
100m | 100Mi| 200m | 256Mi |

Esto sería exitoso, ya que, entre todos los Pods no estarían sobrepasando los `requests` ni `limits` de cpu o memoria asignados al namespace. <br>
Distinto sería el caso si se tratan de deployar 5 Pods con las siguientes especificaciones:

requests.cpu | **requests.memoria** | limits.cpu | limits.memoria |
---|---|---|---|
100m | **128Mi**| 200m | 256Mi |

Esto fallaría, ya que, entre todos estarían superando el `requests.memory` que estaba configurado en **512Mi** (128\*5=640Mi). Sólo se crearían 4 pods con las especificaciones indicadas.

***

<br>

# Creando Deployment

<span style="color:red"> **IMPORTANTE**</span>: Cabe señalar que si especificamos cuotas de CPU y/o Memoria al Namespace, cada despliegue deberá tener implícito sus límites, de lo contrario no se crearán.

<br>

## Ejemplo Deployment Fallido

Crearemos un deploy de nginx sin límites

```sh
$ kubectl run nginx-test --image=nginx -n prueba
```

Se creará el deploy correctamente pero la replica no podrá crear el Pod e indicará que se deben especificar sus correspondientes limits y requests.

```sh
$ kubectl -n prueba get deploy
NAME         DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
nginx-test   1         0         0            0           37s
```

```sh
$ kubectl -n prueba describe rs |grep Error 
Warning  FailedCreate  2m                      replicaset-controller  Error creating: pods "nginx-test-998669899-x9t8w" is forbidden: failed quota: limite-recursos: must specify limits.cpu,limits.memory,requests.cpu,requests.memory
```
<br>

## Ejemplo Deployment Exitoso

Crearemos un deploy de nginx con sus correspondientes límites.

```sh
$ kubectl run nginx-test --image=nginx --requests=cpu=100m,memory=128Mi --limits=cpu=200m,memory=256Mi --replicas=2 -n prueba
```

Este deploy al tener sus límites se creará correctamente al igual que sus Pods.

```sh
$ kubectl -n prueba get deploy,rs,pod
NAME                               DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
deployment.extensions/nginx-test   2         2         2            2           19s

NAME                                          DESIRED   CURRENT   READY     AGE
replicaset.extensions/nginx-test-5bdf799bff   2         2         2         19s

NAME                              READY     STATUS    RESTARTS   AGE
pod/nginx-test-5bdf799bff-hdngl   1/1       Running   0          19s
pod/nginx-test-5bdf799bff-spplf   1/1       Running   0          19s
```

Finalmente podremos ver los recursos utilizados dentro del namespace consultado el recurso `resourcequota`.

```sh
$ kubectl -n prueba describe resourcequotas limite-recursos
Name:            limite-recursos
Namespace:       prueba2
Resource         Used   Hard
--------         ----   ----
limits.cpu       400m   1
limits.memory    512Mi  2Gi
pods             2      5
requests.cpu     200m   500m
requests.memory  256Mi  512Mi

```

Para ayudar a los desarrolladores a la hora de generar sus despliegues cuando no tienen límites, se pueden configurar parámetros `requests` y `limits` de Memoria y CPU por defecto para cada namespace.

<br>

## [Configurar límites y solicitudes de recursos por defecto](/Default_Resources.md)
