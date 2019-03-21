---?image=images/kubernetes.png&size=auto 40%
@title[Inicio]

---
@title[Infraestructura]
## Infraestructura

La infraestructura base del cluster de Kubernetes se compone de un servidor principal Master y sus Nodos (Workers).

![master](images/master-nodes.png)

---
@title[Master]
## Master

Sus componentes principales son `etcd`, `APIServer`, `Scheduler`, `Controller-Manager`.
Estos componentes toman decisiones sobre el cluster 

+++
@title[etcd]
## Etcd

Etcd se utiliza como almacén de respaldo de Kubernetes para todos los datos de cluster.
Su formato llave/valor permite que los componentes estén al tanto de cualquier cambio en el cluster.

+++
@title[APIServer]
## APIServer

Valida y configura datos para Pods, Services, ReplicaSets, entre otros.

+++
@title[Scheduler]
## Scheduler

Determina en qué nodo alojar un pod y sincroniza su información con la configuración del servicio.
Estas desiciones son tomadas en base a:

- Recursos del nodo |
- Restricciones de Hardware |
- Restricciones de software |
- Interferencia entre carga de trabajo |

+++
@title[Controller-Manager]
## Controller-Manager

El administrador de controlador de Kubernetes es un daemon (Servicio) que integra los bucles (secuencia repetida) de control central enviados con Kubernetes.

---
@title[Nodes]
## Nodes (Workers)

Son servidores que alojan los despliegues de aplicación. Son administrados por el servidor Master.
Se componen de los servicios `kubelet`, `container runtime` y `proxy`.


+++
@title[Kubelet]
## Kubelet

Verifica que los contendores que forman parte del POD estén en ejecución.
Solo administra contenedores creados por Kubernetes.

+++
@title[Container runtime]
## Container Runtime

Su función es ejecutar contenedores (Docker) y administrar sus imágenes.

+++
@title[Proxy]
## Proxy

Se encarga de realizar `forwarding` de conexiones TCP o UDP para exponer Services o PODs.

---
@title[Info]

Más información sobre [Conceptos Kubernetes](https://kubernetes.io/docs/concepts/)

---?image=images/kubespray-logo.png&size=auto 40%
@title[Inicio]

---
@title[Introducción Kubespray]
## ¿Qué es Kubespray?

Es un proyecto Open Source que permite implementar un cluster de kubernetes en Bare Metal, AWS, GCE y OS, el cual es una composición de PlayBook's de Ansible, inventario, herramientas de aprovisionamiento y conocimiento de tareas de administración de configuración de clústeres OS/Kubernetes genéricos.

---
@title[Soporte Kubespray]

**Kubespray** tiene soporte para las siguientes versiones de sistema operativo:

- Container Linux by CoreOS |
- Debian Jessie, Stretch, Wheezy |
- Ubuntu 16.04, 18.04 |
- CentOS/RHEL 7 |
- Fedora/CentOS Atomic |
- openSUSE Leap 42.3/Tumbleweed |

---
@title[Infraestructura]
### Infraestructura On-Premise

La plataforma de Cluster Kubernetes de Cencosud se implementara sobre infraestructura **VMWare** y se deplegara de la siguiente forma:

- 3 Nodos Etcd |
- 4 Nodos Master |
- N+1 Nodos Worker |
- 1 Nodo de Deployment (Administración) |

+++
@title[Kuberntes - VMWare]

![infra](images/infra.png)


---
@title[Descarga]
### Descarga de Kubespray

GitHub:
https://github.com/kubernetes-sigs/kubespray

Releases:
https://github.com/kubernetes-sigs/kubespray/releases

+++
@title[Accion]
```
$ mkdir /opt/kubespray
$ cd /opt/kubespray
$ wget https://github.com/kubernetes-sigs/kubespray/archive/v2.8.3.tar.gz -O - | tar -xz
$ cd kubespray-2.8.3
$ ls
ansible.cfg         CONTRIBUTING.md  inventory  mitogen.yaml    RELEASE.md        roles              setup.cfg            Vagrantfile
cluster.yml         Dockerfile       library    OWNERS          remove-node.yml   scale.yml          setup.py
code-of-conduct.md  docs             LICENSE    OWNERS_ALIASES  requirements.txt  scripts            tests
contrib             extra_playbooks  Makefile   README.md       reset.yml         SECURITY_CONTACTS  upgrade-cluster.yml

```
---
@title[Inventario]
### Configuración de Inventario

Para la instalación del cluster es necesario declarar un inventario donde se definan los servidores que cumplirán con los roles de **ETCD**, **MASTER** y **WORKER**.<br>
Por defecto Kubespray tiene un ejemplo de este inventario en el directorio`inventory/sample`. <br>
Copiaremos todo el contenido del directorio para crear una configuración personalizada.

```sh
$ cp -r inventory/sample inventory/mycluster
```


+++
@title[Inventario-Ejemplo]

Modificaremos el archivo `inventory/sample/hosts.ini` con la información de los servidores del cluster.

```yaml
[all]
nodo-etcd01 ip=10.10.1.2 etcd_member_name=etcd1
nodo-etcd02 ip=10.10.1.3 etcd_member_name=etcd2
nodo-etcd03 ip=10.10.1.4 etcd_member_name=etcd3
nodo-master01 ip=10.10.1.5
nodo-master02 ip=10.10.1.6
nodo-worker01 ip=10.10.1.7

# Todos los nodos de role MASTER
[kube-master]
nodo-master01
nodo-master02

# Todos los nodos de role ETCD
[etcd]
nodo-etcd01
nodo-etcd02
nodo-etcd03

# Todos los nodos de role WORKER
[kube-node]
nodo-worker01

[k8s-cluster:children]
kube-master
kube-node

[all:vars]
ansible_user=root # Usuario de conexión
ansible_password=S3cuRe_P4sS # Contraseña de conexión
# ansible_ssh_private_key_file=/roo/.ssh/id_rsa #Llave de confianza
```


---
@title[Configuracion]
### Configuración de cluster

De la configuración por defecto que trae Kubespray realizaremos los siguientes cambios para una instalación más personalizada de acuerdo a nuestra infraestructura y para la integración con VMWare para cloud provider.

+++
@title[Configuracion_1]

En el archivo `inventory/mycluster/group_vars/k8s-cluster/k8s-cluster.yml` realizaremos los siguientes cambios

- **Kube-proxy proxyMode configuration.**
```yaml
kube_proxy_mode: iptables
```

+++
@title[Configuracion_2]

En el archivo `inventory/mycluster/group_vars/k8s-cluster/addons.yml` realizamos los siguientes cambios en los componentes:

- Helm |
- Metrics Server |
- Nginx ingress controller |

+++
@title[Configuracion_3]

### Helm deployment

```yaml
helm_enabled: true
```

+++
@title[Configuracion_4]

### Metrics Server deployment

```yaml
metrics_server_enabled: true
metrics_server_kubelet_insecure_tls: true
metrics_server_metric_resolution: 60s
metrics_server_kubelet_preferred_address_types: "InternalIP"
```

+++
@title[Configuracion_5]

### Nginx ingress controller deployment

```yaml
ingress_nginx_enabled: true                   
ingress_nginx_host_network: false             
ingress_nginx_nodeselector:                   
  node-role.kubernetes.io/master: ""          
ingress_nginx_tolerations:                    
  - key: "node-role.kubernetes.io/master"     
    effect: "NoSchedule"                      
ingress_nginx_namespace: "kube-ingress"       
ingress_nginx_insecure_port: 80               
ingress_nginx_secure_port: 443                
ingress_nginx_configmap:                      
  map-hash-bucket-size: "128"                 
  ssl-protocols: "SSLv2"                      
  access-log-path: /dev/stdout                
  enable-vts-status: "true"                   
  error-log-level: warn                       
  error-log-path: /dev/stdout                 
  proxy-connect-timeout: "240"                
  proxy-read-timeout: "240"                   
  proxy-send-timeout: "240"                   
  worker-shutdown-timeout: 3600s              
```
+++
@title[Configuracion_6]

En el archivo `inventory/mycluster/group_vars/all/all.yml` realizamos los siguientes cambios para habilitar nuestro Loadbalancer y cloud provider con vsphere.

+++
@title[Configuracion_7]

### External LB example config
```yaml
apiserver_loadbalancer_domain_name: "k8s.mydomain.com"      
loadbalancer_apiserver:                                         
  address: 10.10.1.130                                         
  port: 6443                                                     
```
`apiserver_loadbalancer_domain_name` es el nombre otorgado para el balanceador HAProxy.

`address` es la dirección IP apartada para el balanceador HAProxy.

### Internal loadbalancers for apiservers
```yaml
loadbalancer_apiserver_localhost: false 
```

+++
@title[Cloud]

### Cloud_provider

```yaml
cloud_provider: vsphere                             
vsphere_vcenter_ip: "DIRECCION IP O HOST_VCENTER"    
vsphere_vcenter_port: 443                           
vsphere_insecure: 1                                 
vsphere_user: "USUARIO VCENTER"           
vsphere_password: "PASSWORD VICENTER"                        
vsphere_datacenter: "NOMBRE DATACENTER"                  
vsphere_datastore: "NOMBRE_DATASTORE"      
vsphere_working_dir: "DIRECTORIO DE TRABAJO DATASTORE"                    
vsphere_scsi_controller_type: "TIPO CONTROLADORA"              
vsphere_public_network: "NOMBRE VLAN CONEXION"              
```
[Referencia Vsphere](https://github.com/kubernetes-sigs/kubespray/blob/master/docs/vsphere.md)

---
@title[PlayBook]

### Instalación

Una vez el inventario con los nodos está configurado y los archivos necesarios para la instalación fueron ajustados de acuerdo a la infraestructura del cluster, ya es posible ejecutar la instalación de kubernetes. 

+++
@title[Ansible]

### Ejemplo

```sh
$ ansible-playbook -i inventory/mycluster/hosts.ini cluster.yml -b -v --private-key=~/.ssh/private_key
```
**Nota:** Se pasa el parámetro `--private-key` en el caso de que no se haya especificado la llave de confianza en el inventario.

+++
@title[Salida]

#### Salida

```sh
Using /opt/kubespray/k8sopcl_kubespray/kubespray-2.8.3/ansible.cfg as config file

PLAY [localhost] *************************************************************************************************************************************

TASK [Check ansible version !=2.7.0] *****************************************************************************************************************
Friday 01 March 2019  16:54:29 -0300 (0:00:00.091)       0:00:00.091 ******* 
ok: [localhost] => {
    "changed": false, 
    "msg": "All assertions passed"
}

PLAY [localhost] *************************************************************************************************************************************

TASK [deploy warning for non kubeadm] ****************************************************************************************************************
Friday 01 March 2019  16:54:29 -0300 (0:00:00.075)       0:00:00.166 ******* 

TASK [deploy cluster for non kubeadm] ****************************************************************************************************************
Friday 01 March 2019  16:54:29 -0300 (0:00:00.032)       0:00:00.199 ******* 
 [WARNING]: Could not match supplied host pattern, ignoring: bastion


PLAY [bastion[0]] ************************************************************************************************************************************
skipping: no hosts matched
 [WARNING]: Could not match supplied host pattern, ignoring: calico-rr


PLAY [k8s-cluster:etcd:calico-rr] ********************************************************************************************************************

...
....
.....

TASK [kubernetes-apps/cloud_controller/oci : OCI Cloud Controller | Apply Controller Manifest] *******************************************************
Friday 01 March 2019  16:45:06 -0300 (0:00:00.255)       0:23:50.234 ******* 

PLAY RECAP *******************************************************************************************************************************************
nodo-etcd01              : ok=214  changed=51   unreachable=0    failed=0   
nodo-etcd02             : ok=208  changed=48   unreachable=0    failed=0   
nodo-etcd03         	: ok=207  changed=48   unreachable=0    failed=0   
nodo-master01             : ok=383  changed=108  unreachable=0    failed=0   
nodo-master02              : ok=311  changed=86   unreachable=0    failed=0   
nodo-worker01             : ok=273  changed=71   unreachable=0    failed=0   
localhost                  : ok=1    changed=0    unreachable=0    failed=0   

Friday 01 March 2019  16:45:07 -0300 (0:00:00.134)       0:23:50.369 ******* 
=============================================================================== 
kubernetes/master : kubeadm | Initialize first master --------------------------------------------------------------------------------------- 104.68s
container-engine/docker : ensure docker packages are installed ------------------------------------------------------------------------------- 88.59s
kubernetes/master : kubeadm | Init other uninitialized masters ------------------------------------------------------------------------------- 77.32s
bootstrap-os : Install pip for bootstrap ----------------------------------------------------------------------------------------------------- 57.93s
kubernetes/preinstall : Update package management cache (YUM) -------------------------------------------------------------------------------- 42.22s
download : file_download | Download item ----------------------------------------------------------------------------------------------------- 37.44s
download : container_download | Download containers if pull is required or told to always pull (all nodes) ----------------------------------- 36.00s
kubernetes/preinstall : Install packages requirements ---------------------------------------------------------------------------------------- 35.18s
kubernetes/master : Master | wait for the apiserver to be running ---------------------------------------------------------------------------- 18.05s
download : container_download | Download containers if pull is required or told to always pull (all nodes) ----------------------------------- 17.83s
etcd : Gen_certs | Write etcd master certs --------------------------------------------------------------------------------------------------- 17.30s
download : container_download | Download containers if pull is required or told to always pull (all nodes) ----------------------------------- 14.65s
bootstrap-os : Install packages requirements for bootstrap ----------------------------------------------------------------------------------- 14.17s
container-engine/docker : Ensure old versions of Docker are not installed. | RedHat ---------------------------------------------------------- 13.58s
kubernetes/master : kubeadm | write out kubeadm certs ---------------------------------------------------------------------------------------- 11.07s
etcd : reload etcd --------------------------------------------------------------------------------------------------------------------------- 11.03s
kubernetes-apps/ansible : Kubernetes Apps | Lay Down CoreDNS Template ------------------------------------------------------------------------ 10.99s
gather facts from all instances -------------------------------------------------------------------------------------------------------------- 10.35s
container-engine/docker : Docker | pause while Docker restarts ------------------------------------------------------------------------------- 10.28s
kubernetes-apps/ingress_controller/ingress_nginx : NGINX Ingress Controller | Create manifests ------------------------------------------------ 9.96s

```





---
@title[Gracias]

# GRACIAS