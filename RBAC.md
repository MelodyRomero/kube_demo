# RBAC

RBAC (Role-Based Access Control) es un metodo de autenticación de usuarios, utilizado para el acceso al cluster k8s en base a roles previamente definidos. <br>

Sus componentes son:

- Role
- ClusterRole
- RoleBinding
- ClusterRoleBinding


## Role y ClusterRole

Son reglas que definen un grupo de permisos para la interacción con los objetos del cluster k8s. Estas reglas se pueden aplicar a un namespace (Role) y a todo el cluster (ClusterRole). <br>

Ejemplo Role

```yaml
kind: Role
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  namespace: prueba
  name: pod-deploy-reader
rules:
- apiGroups: [""]
  resources: ["pods", "deployments"]
  verbs: ["get", "watch", "list"]
```

En este ejemplo estamos definiendo un role llamado `pod-deploy-reader` para el namespace `prueba`. Este role tiene permisos de lectura para pods y deployments:

Resource|Verbs|
---|---|
pods|"get", "watch", "list"|
deployments|"get", "watch", "list"|

<br>

Esta configuración se puede dar también para un ClusterRole, la única diferencia es que los permisos serán seteados de forma global y no así para un sólo namespace. ClusterRole permite otorgar permisos a objetos superiores a un namespace, como por ejemplo a nodos.<br>

Ejemplo ClusterRole

```yaml
kind: ClusterRole
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: limits-reader
rules:
- apiGroups: [""]
  resources: ["limitranges"]
  verbs: ["get", "watch", "list"]
```
>**Nota:** Se omite el parámetro `namespace` ya que el Role es para todo el cluster.

En el ejemplo se definen permisos de lectura para todos los objetos `limitranges` del cluster.

---

## RoleBinding ClusterRoleBinding

Un RoleBinding otorga permisos que están previamente seteados en un Role a uno o más usuario o grupo de usuarios. Al igual que los Roles y ClusterRoles, se pueden asiganar a namespaces o por completo al cluster. <br>

Ejemplo RoleBinding

```yaml
kind: RoleBinding
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: read-pods-deploy
  namespace: prueba
subjects:
- kind: User
  name: millar
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: Role #this must be Role or ClusterRole
  name: pod-deploy-reader # this must match the name of the Role or ClusterRole you wish to bind to
  apiGroup: rbac.authorization.k8s.io
```

En el ejemplo se crea un `rolebinding` llamado `read-pods-deploy` en el namespace `prueba`. En el apartado `subjects` se asigna el usuario `millar` y en `roleRef` se indica el Role al que está asociado, en este caso `pod-deploy-reader` creado anteriormente.

<br>

Ejemplo ClusterRoleBinding

```yaml
kind: ClusterRoleBinding
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: read-limits-global
subjects:
- kind: Group
  name: lideres
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: ClusterRole
  name: limits-reader
  apiGroup: rbac.authorization.k8s.io
```

Al igual que en el ClusterRole, cuando se crear un ClusterRoleBinding no se especifica el namespace, en este ejemplo se asignó al grupo `lideres` los permisos definidos en el ClusterRole `limits-reader`. <br>



[Autenticación Certificados](Certificados.md)