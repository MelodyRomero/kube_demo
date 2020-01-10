# RBAC

## Creación de certificados

### Crear certificados por usuario

Creamos la KEY para el nuevo usuario.

```sh
$ openssl genrsa -out newuser.key 2048
```

<br>

Creamos el certificado CSR (Certificate Signing Request) usando la llave creada enteriormente.

```sh
$ openssl req -new -key newuser.key -out newuser.csr -subj "/CN=newuser/O=newgroup"
```
>**Nota:** En el apartado `CN` debe ir el usuario que se utilizará en el cluster kubernetes.
 
<br>


### Crear el certificado CRT 


Creamos el certificado CRT utilizando el archivo `.csr` creado anteriormente junto con el certificado CA proporcionado por kubernetes `ca.crt` y su llave `ca.key`. 

```sh
$ openssl x509 -req -in newuser.csr -CA ca.crt -CAkey ca.key -CAcreateserial -out newuser.crt -days 365
```

Debería responder que fue firmado correctamente.

```sh
Signature ok
subject=/CN=newuser/O=newgroup
Getting CA Private Key
```

>**Nota1:** Si los archivos `ca.crt` y `ca.key` del cluster k8s están una ruta distinta al del certificado creado en el primer paso, se debe ingresar la ruta completa en los parámetros `-CA` y `-CAkey` correspondientemente.

>**Nota2:** Se debe especificar la cantidad de días de vigencia para el certificado firmado `-days`.


<br>

Para saber el periodo de vigencia de un certificado podemos ejecutar:

```sh
$ openssl x509  -enddate -noout -in certificado.crt
```

<br>

### Creación del archivo config

Con el certificado creado y firmado podemos crear el archivo config que hará la conexión con el cluster Kubernetes. <br>

Configuramos el usuario:

```sh
$ kubectl config set-credentials newuser --client-certificate=newuser.crt --client-key=newuser.key
```
>**Nota:** En su defecto se puede codificar en *base64* los archivo `.crt` y `.key` y agregarlos al archivo config.

<br>

Configuramos el contexto para el usuario:

```sh
$ kubectl config set-context newuser-context --cluster=cluster.local --user=newuser
```

<br>

Con esto tenemos un archivo `config` listo para usar con el usuario `newuser` pero no tendrá acceso a ningún namespace, ya que, aún no se han creado los roles para el usuario.

<br>

---

## Creación de Role

Para crear una política de permisos sobre los recursos de un namespace es necesario declarar un recurso tipo `role`.  <br>

Ejemplo:

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  name: role-newuser
  namespace: default
rules:
- apiGroups:
  - '*'
  resources:
  - deployments
  - pods
  verbs:
  - get
  - edit
```

En el ejemplo se muestra un archivo el cual creará un Role llamado `role-newuser` perteneciente al namespace `default`. Este Role indica que sólo podrá usar `get` y `edit` sobre `deployments` y `pods` para este namespace. <br>

Creamos el Role:


```sh
$ kubectl apply -f role.yaml
```

<br>

## Creación de Rolebindig

Para vincular un usuario al role creado anteriormente se debe crear un recurso tipo `rolebinding` con la siguiente información. <br>

Ejemplo:

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: rolebinding-newuser
  namespace: default
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: Role
  name: role-newuser
subjects:
- apiGroup: rbac.authorization.k8s.io
  kind: User
  name: newuser
```

En el ejemplo se crea un Rolebinding llamado `rolebinding-newuser` perteneciente al namespace `default`. Este Rolebinding está asociado al Role `role-newuser` y otorga al usuario `newuser` los privilegios de dicho Role.<br>

Creamos el Rolebinding:


```sh
$ kubectl apply -f rolebinding.yaml
```

<br>

Con esto tenemos configuradas las políticas del usuario `newuser` quien debería sólo poder editar y ver pods y deployments del namespace default.

<br>

---

## Verificación de permisos

Como administrador del cluster kubernetes podemos consultar si un usuario tiene ciertos privilegios sobre los recursos de un namespace.<br>

Ejemplo1:

```sh
$ kubectl auth can-i edit deployment --namespace=default --as=newuser
yes
```

En el ejemplo se consulta si el usuario `newuser` puede `editar` (edit) recursos de tipo `deployment` en el namespaces `default`. En este caso responderá `yes` dada las políticas que fueron configuradas anteriormente. <br>

Ejemplo2:

```sh
$ kubectl auth can-i get pod --namespace=kube-system --as=newuser
no
```

En el ejemplo se consulta si el usuario `newuser` puede `obtener` (get) recursos de tipo `pod` del namespace `kube-system`. En este caso responderá `no` puesto que las políticas asiganadas al usuario sólo abarcan `get` y `edit` de recursos `pods` y `deployment` del namespace `default`.

