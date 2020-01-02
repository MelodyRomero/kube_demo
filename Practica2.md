# Práctica2

## Deploy Wordpress

- [Crear PersistentVolumeClaim](#crear-persistentvolumeclaim)
- [Crear Deployment](#crear-deployment)
- [Crear Service](#crear-service)
- [Crear Ingress](#crear-ingress)

<br>

# Crear PersistentVolumeClaim

Crear el archivo `pvc-wordpress.yaml` con el siguiente contenido

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: wp-pv-claim
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

Aplicar el archivo `pvc-wordpress.yaml` al namespace creado inicialmente.

```sh
kubectl -n nombre_del_namespace apply -f pvc-wordpress.yaml
```

<br>

# Crear Deployment

Crear el archivo `deploy-wordpress.yaml` con el siguiente contenido

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: wordpress
  labels:
    app: wordpress
spec:
  selector:
    matchLabels:
      app: wordpress
      tier: frontend
  strategy:
    type: Recreate
  template:
    metadata:
      labels:
        app: wordpress
        tier: frontend
    spec:
      containers:
      - image: wordpress:4.8-apache
        name: wordpress
        env:
        - name: WORDPRESS_DB_HOST
          value: wordpress-mysql
        - name: WORDPRESS_DB_PASSWORD
          valueFrom:
            secretKeyRef:
              name: mysql-pass
              key: password
        ports:
        - containerPort: 80
          name: wordpress
        volumeMounts:
        - name: wordpress-persistent-storage
          mountPath: /var/www/html
      volumes:
      - name: wordpress-persistent-storage
        persistentVolumeClaim:
          claimName: wp-pv-claim
```

<br>

Aplicar el archivo `deploy-wordpress.yaml` al namespace creado inicialmente.

```sh
kubectl -n nombre_del_namespace apply -f deploy-wordpress.yaml
```

<br>

# Crear Service

Crear el archivo `service-wordpress.yaml` con el siguiente contenido

```yml
apiVersion: v1
kind: Service
metadata:
  name: wordpress
  labels:
    app: wordpress
spec:
  ports:
    - port: 80
  selector:
    app: wordpress
    tier: frontend
  type: LoadBalancer
```

<br>

Aplicar el archivo `service-wordpress.yaml` al namespace creado inicialmente.

```sh
kubectl -n nombre_del_namespace apply -f service-wordpress.yaml
```

<br>

# Crear Ingress

Crear el archivo `ingress-wordpress.yaml` con el siguiente contenido

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: wordpress
spec:
  rules:
  - host: wordpress.mydomain.cl
    http:
      paths:
      - backend:
          serviceName: wordpress
          servicePort: 80
        path: /
```

<br>

# Verificación

Revisar que todos los recursos estén correctamente desplegados.

```sh
kubectl -n nombre_del_namespace get pvc,service,deploy,ingress
```