# Configurar límites y solicitudes de recursos por defecto

Cuando un namespace tiene `requests` y `limits` de recursos asignados, los despliegues a realizar también deben tener estos parámetros declarados de lo contrario, [como mostramos anteriormente](/Quotas_Namespace.md), los Pods no se crearán. <br>
Para esto es posible crear un recurso `LimitRange` que entregará valores por defecto a cualquier deployment de un namespace.<br>


# LimitRange

El recurso `LimitRange` le asigna a un namespace un `límite y/o solicitud de recursos de CPU y Memoria por defecto`, esto quiere decir, que si un deployment no tiene estos parámetros configurados se le entregarán estos valores en base a los que tiene impuestos el namespace.

<br>

# Ejemplo LimitRange

Creamos el namespace llamado `prueba`.

```sh
$ kubectl create ns prueba
```
<br>

Creamos un recurso tipo `LimitRange` en el archivo limite_prueba.yaml.

```yaml
apiVersion: v1
kind: LimitRange
metadata:
  name: default-limits
spec:
  limits:
  - default:
      cpu: 200m
      memory: 128Mi
    defaultRequest:
      cpu: 50m
      memory: 64Mi
    type: Container
```

<br>

Verificamos el límite asignado al namespace prueba

```sh
$ kubectl -n prueba describe limitrange default-limits
Name:       default-limits
Namespace:  prueba
Type        Resource  Min  Max  Default Request  Default Limit  Max Limit/Request Ratio
----        --------  ---  ---  ---------------  -------------  -----------------------
Container   cpu       -    -    50m              200m           -
Container   memory    -    -    64Mi             128Mi          -

```

<br>

En este ejemplo se crea una cuota con las siguientes especificaciones:

Resource |Default Request| Default Limit |
---|---|---|
Memory | 64Mi | 128Mi |
CPU | 50m | 200m |

<br>

Esto quiere decir que si un objeto (deploy, pod, job, cronjob, etc) no tiene especificados sus límites, el namespaces le asignará de forma automática los especificados anteriormente.

***

<br>

# Creando Deployment

Crearemos un deploy de nginx sin límites

```sh
$ kubectl run nginx-test --image=nginx -n prueba
```

<br>

Si consultamos el POD veremos que se autoasignaron límtes sin haberlos especificado

```sh
$ kubectl -n prueba get pod nginx-test-85f7c9cb67-lm8l9 -o yaml
apiVersion: v1
kind: Pod
metadata:
  annotations:
    kubernetes.io/limit-ranger: 'LimitRanger plugin set: cpu, memory request for container
      nginx-test; cpu, memory limit for container nginx-test'
  creationTimestamp: "2018-07-12T11:56:36Z"
  generateName: nginx-test-85f7c9cb67-
  labels:
    pod-template-hash: "4193757623"
    run: nginx-test
  name: nginx-test-85f7c9cb67-lm8l9
  namespace: prueba
  ownerReferences:
  - apiVersion: extensions/v1beta1
    blockOwnerDeletion: true
    controller: true
    kind: ReplicaSet
    name: nginx-test-85f7c9cb67
    uid: 8835fc9b-5607-11e9-801d-0050569e5f2e
  resourceVersion: "39499269"
  selfLink: /api/v1/namespaces/prueba/pods/nginx-test-85f7c9cb67-lm8l9
  uid: 884184e0-5607-11e9-801d-0050569e5f2e
spec:
  containers:
  - image: nginx
    imagePullPolicy: Always
    name: nginx-test
    resources:
      limits:
        cpu: 200m
        memory: 128Mi
      requests:
        cpu: 50m
        memory: 64Mi
    terminationMessagePath: /dev/termination-log
    terminationMessagePolicy: File
    volumeMounts:
    - mountPath: /var/run/secrets/kubernetes.io/serviceaccount
      name: default-token-68stq
      readOnly: true
  dnsPolicy: ClusterFirst
  nodeName: nodo01
  restartPolicy: Always
  schedulerName: default-scheduler
  securityContext: {}
  serviceAccount: default
  serviceAccountName: default
  terminationGracePeriodSeconds: 30
```

Configurar estos límites por defecto obliga al desarrollador a agregar límites razonables para sus aplicaciones
