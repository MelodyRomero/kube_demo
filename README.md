# Límites 

Cuando un Pod es creado en un cluster k8s, utiliza una cierta cantidad de recursos (dependiendo de la aplicación desplegada). Los recursos en kubernetes son determinados por CPU y Memoria de cada nodo worker, por lo que, un Pod que especifique una cierta cantidad de recursos a utilizar hará que el cluster tome mejores decisiones al momento de desplegar la aplicación.

<br>

## Límites de un deploy

En el apartado `resources`, dentro de `containers`, es donde se deben configurar los requerimientos y límites de un contenedor.
En k8s los requerimientos y límites que se pueden configurar son de CPU y Memoria.

### Ejemplo

```yaml
  resources:
    limits:
      cpu: 1
      memory: 512Mi
    requests:
      cpu: 500m
      memory: 256Mi
```
<br>

### Medición

- **CPU:** Se mide en milicores, donde 1000 milicores (1000m) es una CPU de un nodo. También se puede especificar como `1` para indicar que se usará una CPU completa o `"0.5"` para indicar que se usará la mitad de una CPU.

- **Memoria:** Se mide en Kibibit (Ki), Mebibyte (Mi) o Gibibyte (Gi), etc.
>Más información [International System of Units](http://physics.nist.gov/cuu/Units/binary.html)

<br>

## Ejemplo de límite de un deploy

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deploy
  namespace: prueba
spec:
  replicas: 1
  selector:
    matchLabels:
      app: nginx
      environment: test
  template:
    metadata:
      labels:
        app: nginx
        environment: test
    spec:
      containers:
      - image: nginx:1.7.9
        name: web-app
        ports:
        - containerPort: 80
          protocol: TCP
        resources: --> Aquí se debe aplicar el límite del contenedor
          limits: --> Máximo a utiliza por el contendor
            cpu: 200m --> Máximo uso de CPU
            memory: 128Mi --> Máximo uso de Memoria
          requests:
            cpu: 100m --> CPU requerida para iniciar el contendor
            memory: 64Mi --> Memoria requerida para iniciar el contendor
```

<br>

### Requests

En el parámetro `requests` se especifica cuanto es lo que necesita un contenedor para poder deployarse en un nodo. <br>
En el ejemplo anterior se indica que para deployar la aplicación *nginx* se necesita que el Nodo donde se ejecute el POD tenga por lo menos un `10% de CPU (100m)` y `64 MB de Memoria (64Mi)` libres. <br>

>**Nota:** Si el Nodo no tiene disponible lo solicitado en el apartado `requests`, este no se podrá crear.

<br>

### Limits

En el parámetro `limits` se especifica hasta que punto puede llegar a crecer un contenedor, indicando un máximo de uso de CPU y/o Memoria.<br>
En el ejemplo anterior se indica que el límite de uso de del contenedor *nginx* será de un 20% de CPU (200m) y 128 Mb de Memoria (128Mi).

>**Nota:** Se debe tener en cuenta que si no se especifica un límite, la aplicación puede llegar a consumir la totalidad de recursos de un nodo.

<br>

## [Cuotas por Namespace](/Quotas_Namespace.md)