# Práctica

## Deploy MySql

- [Crear Secret](#crear-secret)
- [Crear PersistentVolumeClaim](#crear-persistentvolumeclaim)
- [Crear Deployment](#crear-deployment)
- [Crear Service](#crear-service)

<br>

# Primero

Crear un namespace para desplegar Wordpress y MySQL

```sh
kubectl create namespace my-namespace
```

<br>

# Crear Secret

Crear un recurso secret con la contraseña de conexión mysql. 

<br>

## Definir una contraseña

En kubernetes se pueden crear secrets, que son datos encriptados. Esto se puede hacer mediante un archivo o encriptando el texto antes.
Ejemplo.<br>

```sh
echo -n 'MyNewPassword' |base64
```

Esto devolverá el texto `MyNewPassword` encriptado en `base64` como sigue.

```sh
TXlOZXdQYXNzd29yZA==
```

<br>

Crear el archivo `secret.yaml` con el siguiente contenido

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: mysql-pass
data:
  password: TXlOZXdQYXNzd29yZA==
type: Opaque
```

<br>

Aplicar el archivo `secret.yaml` al namespace creado inicialmente.

```sh
kubectl -n nombre_del_namespace apply -f secret.yaml
```

<br>

# Crear PersistentVolumeClaim

Crear el archivo `pvc-mysql.yaml` con el siguiente contenido

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: mysql-pv-claim
  labels:
    app: wordpress
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 10Gi
```

<br>

Aplicar el archivo `pvc-mysql.yaml` al namespace creado inicialmente.

```sh
kubectl -n nombre_del_namespace apply -f pvc-mysql.yaml
```

<br>

# Crear Deployment

Crear el archivo `deploy-mysql.yaml` con el siguiente contenido

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: wordpress-mysql
  labels:
    app: wordpress
spec:
  selector:
    matchLabels:
      app: wordpress
      tier: mysql
  strategy:
    type: Recreate
  template:
    metadata:
      labels:
        app: wordpress
        tier: mysql
    spec:
      containers:
      - image: mysql:5.6
        name: mysql
        env:
        - name: MYSQL_ROOT_PASSWORD
          valueFrom:
            secretKeyRef:
              name: mysql-pass
              key: password
        ports:
        - containerPort: 3306
          name: mysql
        volumeMounts:
        - name: mysql-persistent-storage
          mountPath: /var/lib/mysql
      volumes:
      - name: mysql-persistent-storage
        persistentVolumeClaim:
          claimName: mysql-pv-claim
```

<br>

Aplicar el archivo `deploy-mysql.yaml` al namespace creado inicialmente.

```sh
kubectl -n nombre_del_namespace apply -f deploy-mysql.yaml
```

<br>

# Crear Service

Crear el archivo `service-mysql.yaml` con el siguiente contenido

```yml
apiVersion: v1
kind: Service
metadata:
  name: wordpress-mysql
  labels:
    app: wordpress
spec:
  ports:
    - port: 3306
  selector:
    app: wordpress
    tier: mysql
  clusterIP: None
```

<br>

Aplicar el archivo `service-mysql.yaml` al namespace creado inicialmente.

```sh
kubectl -n nombre_del_namespace apply -f service-mysql.yaml
```

<br>

# Verificación

Revisar que todos los recursos estén correctamente desplegados.

```sh
kubectl -n nombre_del_namespace get secret,pvc,service,deploy
```

[Deploy Wordpress](/Practica2.md)