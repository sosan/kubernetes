# K3S y K3D = buena combinacion para el desarrollo en local y tests locales

Trabajar con Kubernetes(K8S) en `maquina local` cuando eres desarrollador o un operador de sistemas no es facil. ¿Como crear un cluster de kubernetes local facilmente? Hay muchas soluciones para utilizar kubernetes en el sistema local, `microk8s`, `minikube`, `k3s`. La que veremos aqui es k3s.

- ¿Y por que un cluster local?
Un cluster en local es una buena forma de empezar en kubernetes, ademas, puedes experimentar y realizar todos los errores que quieras.

## En que es diferente k3s a las demas soluciones

- k3s no tiene dependencias externas.
- k3s es un binario de 40MB.
- k3s usa muchisimos menos recursos
- k3s utiliza un intermediario llamado kine que traduce las llamadas de la api a etcd o a una base de datos sql.
- k3s esta construido en base a funcionalidad y la modularidad. Incluye un controlador de helm(puede usar los charts de helm sin tener que usar el cliente de helm), manifiestos para un despliegue automatico, politicas de red.
- k3s al arrancar solo levanta 3 servicios. Pero en caso de necesitar mas servicios se pueden levantar.
- k3s esta optimizado para ARM. Funciona igual de bien que en raspberry pi o en una aws x4large.

## Arquitectura interna de k3s

Aqui tenemos un diagrama de la estructura interna del k3s

![Imagen interna de la arquitectura](https://raw.githubusercontent.com/sosan/kubernetes/master/assets/diagrama_arquitectura_rancher.webp)

## Requisitos

Antes de empezar la instalacion de k3d, necesitamos tener instalados:
- [Informacion externa sobre la instalacion de Docker](https://docs.docker.com/engine/install/ubuntu/)
- [Informacion externa sobre la instalacion de Kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/)

### Instalacion de kubectl

- Bajar kubectl
```shell
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
```
- Cambiamos permisos de kubectl y copiamos a /usr/local/bin
```shell
sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl && rm kubectl
```

## ¿Que es k3s? Al turron!!

k3s es una distribucion de Kubernetes muy eficiente y ligera. k3s encaja particularmente bien en un entorno de desarrollo local. Podemos probar la aplicacion con los manifiestos de kubernetes en condiciones reales. Ademas de ligero y eficiente, es bastante modular y debido a esta modularidad podemos intercambiar/cambiar piezas que vienen por defecto. (Desarrollado mas adelante en profundidad)


k3s incluye:

>Flannel: una red superpuesta L2 muy simple que satisface los requisitos de Kubernetes. Este es un complemento CNI (interfaz de red de contenedores), como Calico, Romana, Weave-net
Flannel no es compatible con las políticas de red de Kubernetes, pero se puede reemplazar por Calico.

>CoreDNS: un servidor DNS flexible y extensible que puede servir como el DNS del clúster de Kubernetes.

>Traefik: es un proxy inverso HTTP y un balanceador de carga. Se puede cambiar por traefik2 o nginx.

>Balanceador de carga Klipper: balanceador de carga de servicios que utiliza puertos de host disponibles.

>SQLite3: el almacenamiento utilizado de forma predeterminada. Tambien se puede sustituir por MySQL, Postgres, etcd3.

>Containerd: es un ejecutador de contenedores en tiempo de ejecución como Docker pero sin la parte de construcción de imagen.


## ¿Que es k3d?

k3d es una utilidad diseñada por Rancher para ejecutar fácilmente k3s en Docker, proporciona un cliente para crear, ejecutar y eliminar clusters de Kubernetes de 1 nodo a n nodos.


### Nuevas incorporaciones del K3D version 3

Rancher ha hecho un gran trabajo y ha reescrito por completo k3d en la version 3. Han implementado nuevos conceptos y estructuras para convertir a k3d con caracteristicas muy interesantes y practicas.

- Ahora ya no existe la terminologia `maestro` / `esclavo`, ahora existen `servidor` y `agente` (`server` / `agent`)
- Cada cluster que se cree, generara al menos 2 contenedores: 1 equilibrador de carga y 1 nodo `servidor`. El equilibrador de carga es el punto de acceso a la api de k3s, por lo que para clusters en varios servidores, solo necesitas exponer un unico puerto para la api. Tambien se encarga de enviar las solucitudes al nodo `servidor` correcto (se puede desactivar con `--no-lb`).
- Siguiendo la tendencia de otros clientes nativos de la nube (awscli, gcloud, azurecli, etc...) se ha adoptado la sintaxis `noun verb`.
- Soporte para clusters en multiples servidores (`dqlite`) con configuracion de recargas en caliente cuando se agrega un nuevo nodo `servidor` al cluster.
- Manejos de nodos independientemente de los clusters: k3d create/start/stop/delete node NOMBRE_DEL_NODO
- Soporte basico para plugins.
- Autocompletado. (Desarrollado mas adelante)


### Instalacion K3D

La instalación es muy fácil y está disponible a través de muchos instaladores: wget, curl, brew, ...

>wget:
```shell
wget -q -O - https://raw.githubusercontent.com/rancher/k3d/master/install.sh | bash
```

>curl:
```shell
curl -s https://raw.githubusercontent.com/rancher/k3d/main/install.sh | bash
```

>Homebrew:
```shell
brew install k3d
```

>Choco windows
```shell
choco install k3d
```

una vez instalado, puedes configurar el autocomplete con el shell (bash, zsh, fish, powershell). El Comando completo con sus opcionales:
```shell
k3d completion [bash | zsh | fish | (psh | powershell)]
```

ejemplo en bash, simplemente sustituir bash por el que necesitemos:
```shell
k3d completion bash >> ~/.bashrc && source ~/.bashrc
```


### Interfaz grafica k3d

```shell
https://github.com/inercia/k3x
```



### Creacion del cluster
Creamos un cluster muy simple llamado `hello-world`

```shell
k3d cluster create hello-world
```

En unos segundos saldria el output, lo que realiza el k3d es generando los contenedores:

```shell
INFO[0000] Prep: Network
INFO[0000] Created network 'k3d-hello-world'
INFO[0001] Created volume 'k3d-hello-world-images'
INFO[0002] Creating node 'k3d-hello-world-server-0'
INFO[0002] Creating LoadBalancer 'k3d-hello-world-serverlb'
INFO[0003] Starting cluster 'hello-world'
INFO[0003] Starting Node 'k3d-hello-world-server-0'
INFO[0034] Starting Node 'k3d-hello-world-serverlb'
INFO[0076] (Optional) Trying to get IP of the docker host and inject it into the cluster as 'host.k3d.internal' for easy access
INFO[0146] Successfully added host record to /etc/hosts in 2/2 nodes and to the CoreDNS ConfigMap
INFO[0146] Cluster 'hello-world' created successfully!
```

Este cluster `hello-world` solo tiene un solo nodo, este unico nodo haria la funcion de servidor y agente. No tiene alta disponibilidad. A parte de crear el cluster desde consola tambien se puede crear desde un archivo. Veremos mas adelante.

Creamos otro cluster con 1 balanceador y con 1 nodo (con el rol de servidor y agente) con el nombre `dev` con los puertos abiertos 8080 y 8443 en el host y 80 y 443 puertos locales de la imagen (esto se complica!!!). Tampoco tiene alta disponibilidad:

```shell
k3d cluster create dev \
    --port 8080:80@loadbalancer \
    --port 8443:443@loadbalancer \
    --api-port 6551
```

>ATENCION: Estos dos clusters ocuparan en memoria cerca de 1.5GiB

Ahora podemos listar los clusters que tenemos

```shell
k3d cluster list
```
y obtendremos algo asi:
```
NAME          SERVERS   AGENTS   LOADBALANCER
dev           1/1       0/0      true
hello-world   1/1       0/0      true
```

> Para que podamos entender mejor la estructura que crea k3d, aqui tenemos un diagrama general:

![Diagrama cluster k3d](https://raw.githubusercontent.com/sosan/kubernetes/master/assets/diagrama_cluster_k3d.webp)

Creamos otro cluster con 1 nodo servidor con 3 agentes. Con `--servers` y `--agents` podemos espicificar el numero que queremos de cada tipo. Este cluster tampoco tiene alta disponibilidad. Nos puede vernir muy bien para probar cosas en fase de desarrollo:

```shell
k3d cluster create dev-servidoryagentes \
    --port 8081:81@loadbalancer \
    --port 8444:443@loadbalancer \
    --api-port 6443 \
    --servers 1 \
    --agents 3
```

**NOTA: Si comprobamos la memoria usada por los 2 clusters creados hasta ahora, facilmente sobrepasemos los 2GiB. Este ultimo cluster es solo de prueba y podemos borrarlo con `k3d cluster delete dev-servidoryagentes`**


### Explicacion mapeo de puertos

Ahora vamos a explicar un poco como el balanceador de carga se encarga de repartir el trafico hacia otros contenedores. Con el comando `docker ps` obtenemos que existen 4 contenedores: 2 k3d-proxy(2 balanceadores de carga ... `xxxx-serverlb`) y 2 k3s(2 servidores `xxxx-server-0`):

```shell
[...] IMAGE                      COMMAND                [...]   PORTS                                                                 NAMES
[...] rancher/k3d-proxy:v4.0.0   "/bin/sh -c nginx-pr…" [...]   0.0.0.0:8080->80/tcp, 0.0.0.0:8443->443/tcp, 0.0.0.0:6551->6443/tcp   k3d-dev-serverlb
[...] rancher/k3s:v1.20.0-k3s2   "/bin/k3s server --t…" [...]                                                                         k3d-dev-server-0
[...] rancher/k3d-proxy:v4.0.0   "/bin/sh -c nginx-pr…" [...]   80/tcp, 0.0.0.0:45735->6443/tcp                                       k3d-hello-world-serverlb
[...] rancher/k3s:v1.20.0-k3s2   "/bin/k3s server --t…" [...]                                                                         k3d-hello-world-server-0
```

Aunque el `docker ps` facilita mas informacion, omitiremos informacion y nos centraremos en la parte que nos importa. En **PORTS** obtenemos los puertos abiertos localmente y localmente en el contenedor.

`0.0.0.0:8080->80/tcp, 0.0.0.0:8443->443/tcp, 0.0.0.0:6551->6443/tcp`

Abre 2 puertos en el host 8080 y 8443 y 2 puertos en local del contenedor 80 y 443.

Ahora centremos en el comando de creacion del cluster que hemos utilizado anteriormente:
**NOTA: No es necesario ejecutar el comando de abajo.**

```shell
k3d cluster create dev \
    --port 8080:80@loadbalancer \
    --port 8443:443@loadbalancer \
    --api-port 6551
```

Explicacion del mapeo de puertos:

- **--port 8080:80@loadbalancer** añade un mapa al puerto del host 8080 que vaya al puerto local 80 del balanceador de carga, ademas añade para las peticiones un mapa con el puerto 80 a todos los nodos `agente`.

- **--api-port 6551** : Por defecto, los puertos de la api NO son publicos. API-PORT se usa para forzar el api-servidor de k3s a escuchar en el puerto 6551. De esta forma, el balanceador de carga accedera a la api de kubernetes, incluso para clusters en multi-servidores. Solo es necesario publicar/exponer un solo api puerto. El balanceador de carga se encargara de redireccionar las peticiones al nodo `servidor` apropiado.

- **NOTA: --port "32000-32767:32000-32767@loadbalancer"** : De esta forma, utilizando rangos (`32000-32767`) expones un rango de puertos para los nodos. Se utiliza esta forma (rangos de puertos) cuando quieras evitar el `Ingress Controller`.

**PRO TIP: Durante el proceso de creacion de un cluster es posible que, debido a falta de recursos el cluster este intentando constantemente matandose y reinciandose. Para evitar este comportamiento, es posible añadirle un timeout a la creacion del cluster.**


### ¿Que ocurre cuando tienes multiples clusters? 

Si tenemos planificado tener varios clusters, ¿como podemos separar la configuacion de los multiples clusters? kubernetes tiene una forma de separar la configuracion atrasves de archivos de configuracion. Los usuarios, contextos se suelen definir en uno o varios archivos de configuracion, de esta forma puedes cambiar rapidamente entre configuraciones de clusters.

**NOTA:Normalmente se le suele llamar kubeconfig pero no es fisicamente el nombre de un archivo.**


Un contexto es un elemento del archivo kubeconfig que se usa para agruapar parametros sobre un nombre. Cada contexto tiene tres parametros: cluster, namespace y usuario. Por defecto, kubectl usa los parametros del contexto actual para comunicarse dentro del cluster.


Para ver cual es contexto actual, tenemos este comando:

```shell
kubectl config current-context
```


Para obtener mas informacion de la configuracion:

```shell
kubectl config view
```

el output es el siguiente, como veis enseña los `contexts`:

```shell
apiVersion: v1
clusters:
- cluster:
    certificate-authority-data: DATA+OMITTED
    server: https://0.0.0.0:6551
  name: k3d-dev
- cluster:
    certificate-authority-data: DATA+OMITTED
    server: https://0.0.0.0:34716
  name: k3d-hello-world
contexts:  #####--------AQUI-------------------
- context:
    cluster: k3d-dev
    user: admin@k3d-dev
  name: k3d-dev
- context:
    cluster: k3d-hello-world
    user: admin@k3d-hello-world
  name: k3d-hello-world
current-context: k3d-dev
kind: Config
preferences: {}
users:
- name: admin@k3d-dev
  user:
    client-certificate-data: REDACTED
    client-key-data: REDACTED
- name: admin@k3d-hello-world
  user:
    client-certificate-data: REDACTED
    client-key-data: REDACTED
```
----------------------------
>>------PEQUEÑO INCISO------

>>Hacemos un pequeño inciso y pegamos una pequeña pincelada al manejo de kubectl. Kubectl puede configurar los clusters atraves de archivos, podemos empezar usando la estructura de aqui abajo y modificar la estructura con comandos kubectl. Este archivo describe dos clusters, dos usurios y tres contextos.

>> NO es necesario ejecutar los siguientes comandos, solo es a modo de pequeña explicacion.

```shell
cat <<EOF > configuracion-clusters
apiVersion: v1
kind: Config
preferences: {}

clusters:
- cluster:
  name: development
- cluster:
  name: production

users:
- name: developer
- name: test-user

contexts:
- context:
  name: dev-frontend
- context:
  name: dev-storage
- context:
  name: pd-production
EOF
```


>>Con estos comandos añadimos detalles a los clusters, certificados, direcciones, etc...:
```shell
kubectl config \
    --kubeconfig=configuracion-clusters set-cluster development \
    --server=https://1.2.3.4 \
    --certificate-authority=fake-ca-file
```

```shell
kubectl config \
    --kubeconfig=configuracion-clusters set-cluster production \
    --server=https://5.6.7.8 \
    --insecure-skip-tls-verify
```

>>Con estos comandos añadimos detalles a los usuarios:

```shell
kubectl config \
    --kubeconfig=configuracion-clusters set-credentials developer \
    --client-certificate=certificado_fake_archivo \
    --client-key=key_fake_archivo
```

```shell
kubectl config \
    --kubeconfig=configuracion-clusters set-credentials test-user \
    --username=usuario_test \
    --password=password_test
```

>>Con este comando enlazamos los clusters con el contexto. 
>>- En este caso enlaza el contexto `dev-frontend` y el cluster `development`, el namespace `frontend` y el usuario `developer`.
```shell
kubectl config \
    --kubeconfig=configuracion-clusters set-context dev-frontend \
    --cluster=development \
    --namespace=frontend  \
    --user=developer
```

>>- En este caso enlaza el contexto `dev-storage` y el cluster `development`, el namespace `storage` y el usuario `developer`.
```shell
kubectl config \
    --kubeconfig=configuracion-clusters set-context dev-storage \
    --cluster=development \
    --namespace=storage \
    --user=developer
```
>>- En este caso enlaza el cluster `production` con el contexto `pd-production`.
```shell
kubectl config \
    --kubeconfig=configuracion-clusters set-context pd-production \
    --cluster=production \
    --namespace=production \
    --user=test-user
```

>>para ver los cambios efectuados en el archivo:
```shell
kubectl config --kubeconfig=configuracion-clusters view
```

la salida es la siguiente, fijarse como ha ido cambiando los parametros :

```
apiVersion: v1
clusters:
- cluster:
    certificate-authority: fake-ca-file
    server: https://1.2.3.4
  name: development
- cluster:
    insecure-skip-tls-verify: true
    server: https://5.6.7.8
  name: production
contexts:
- context:
    cluster: development
    namespace: frontend
    user: developer
  name: dev-frontend
- context:
    cluster: development
    namespace: storage
    user: developer
  name: dev-storage
- context:
    cluster: production
    namespace: production
    user: test-user
  name: pd-production
current-context: ""
kind: Config
preferences: {}
users:
- name: developer
  user:
    client-certificate: certificado_fake_archivo
    client-key: key_fake_archivo
- name: test-user
  user:
    password: password_test
    username: usuario_test
```


>>Ahora supongamos que queremos trabajar con el cluster `PRODUCTION` pero estamos en el cluster `DEVELOPMENT`. Tenemos que usar el kubectl y decirle que use el archivo de configuracion `configuracion-clusters` y se pase al pd-production:

```shell
kubectl config --kubeconfig=configuracion-clusters use-context pd-production
```

>>Con este ejemplo pasamos al contexto `dev-storage` y como tiene enlazado el contexto al cluster `DEVELOPMENT` usa el cluster correcto.
```shell
kubectl config --kubeconfig=configuracion-clusters use-context dev-storage
```

**PRO TIP: Existe las herramientas [Kubectx y Kubens](https://github.com/ahmetb/kubectx) que nos permiten cambiar de clusters y namespaces mucho mas facil.**

-------------------------
<br><br><br>
Despues de este pequño inciso, seguimos con k3d. 

Por defecto, al crear un cluster en k3d el kubeconfig se actualizara automaticamente. Por ejemplo, si estamos usando el kubeconfig de un cluster y tenemos que crear un nuevo cluster, al terminar de crear el nuevo cluster nos cambiara automaticamente al nuevo cluster. 

Lo podemos comprobar lanzado el comando `get-contexts`
```
kubectl config get-contexts
```

Nos enseñara algo asi:

```shell
CURRENT   NAME               CLUSTER            AUTHINFO                 NAMESPACE
*         k3d-dev            k3d-dev            admin@k3d-dev
          k3d-hello-world    k3d-hello-world    admin@k3d-hello-world
```

Para cambiar al cluster `k3d-hello-world` tendriamos que realizar:

```
kubectl config use-context k3d-hello-world
```

Saldra mensaje: `Switched to context "k3d-hello-world".`

Por defecto, el formato del contexto es `k3d-NOMBRE_DEL_CLUSTER`. Si quisieramos cambiar de cluster y ademas especificar el archivo de configuracion `kubectl config --kubeconfig=NOMBRE_DEL_ARCHIVO use-context NOMBRE_DEL_CONTEXTO`.


Por el momento cambiamos de contexto a `k3d-dev` con la instruccion:

```
kubectl config use-context k3d-dev
```


## PROFUNDIZANDO. Desactivar el cambio automatico, cambio manual

Si se quiere deshabilitar este comportamiento de creacion/actualizacion automatica que viene por defecto, se puede usar el comando `--kubeconfig-update-default = false`. Quedaria algo asi:

```shell
k3d cluster create sin-auto \
  --port 8085:80@loadbalancer \
  --port 8445:443@loadbalancer \
  --api-port 6552 \
  --kubeconfig-update-default=false
```

Se creara el cluster pero no saltaremos al contexto del nuevo cluster, esto ocurre gracias a la instruccion `kubeconfig-update-default = false`. Por tanto, nos tocara actualizar el kubeconfig manualmente. Pues vamos alla!! Al atake!!

- Para comprobar y consultar el contexto actual:
```shell
kubectl config current-context
```
tendremos un output `k3d-dev`, ya anteriormente hemos cambiado al cluster `k3d-dev`. Nos lo confirmara el comando `kubectl config get-contexts` que nos lanzara una respuesta parecida a:

```
CURRENT   NAME              CLUSTER           AUTHINFO                NAMESPACE
*         k3d-dev           k3d-dev           admin@k3d-dev           
          k3d-hello-world   k3d-hello-world   admin@k3d-hello-world   
```

**ATENCION: con el ultimo cluster creado `sin-auto` llegaremos a los 4GiB de memoria consumidos.**

Y ahora realizamos un:

```shell
kubectl config view
```

Pero tenemos el cluster creado ya que con 

```shell
k3d cluster list
```
aparece el siguente listado:

```
NAME          SERVERS   AGENTS   LOADBALANCER
dev           1/1       0/0      true
hello-world   1/1       0/0      true
sin-auto      1/1       0/0      true
```

Tanto mostrando el `get-contexts` y el `config view` no nos enseña el cluster `sin-auto`. Podriamos pensar que simplemente con este comando se solucionaran los problemas. Pero la realidad es que nos salta un error `error: no context exists with the name: "sin-auto"`

```shell
kubectl config use-context sin-auto
```

Para actualizar el kubeconfig con el nuevo contexto y cambiar al nuevo contexto, necesitamos relaizar estos comandos:

```shell
k3d kubeconfig write sin-auto
```
el output sera parecido a: `/root/.k3d/kubeconfig-sin-auto.yaml`


```shell
k3d kubeconfig merge sin-auto \
  --kubeconfig-merge-default \
  --kubeconfig-switch-context 
```


- Volvemos a consultar el contexto actual:
```shell
kubectl config current-context
```

### Comandos para manipular el kubeconfig desde k3d


k3d proporciona algunos comandos para manipular fácilmente kubeconfig:

>Obtener el kubeconfig del cluster dev.
- `k3d kubeconfig get dev`

>Crear el archivo kubeconfig, por defecto se guardan en `~/.k3d/` y siguen el esquema de nombre `kubeconfig-NOMBRE_DEL_CLUSTER.yaml`
- `k3d kubeconfig write dev`

>obtener kubeconfig del cluster y fusionarlos en un archivo en `$HOME/.k3d/` a otro archivo de configuracion yaml
- `k3d kubeconfig merge dev hello-world`


### SUBCOMANDOS del kubectl config

Los comandos para añadir detalles al kubeconfig del cluster:

```
kubectl config SUBCOMMANDOS
```
```
current-context Muesta el contexto actual
delete-cluster  Borra el cluster especificado del kubeconfig
delete-context  Borra el contexto especificado del kubeconfig
delete-user     Borra el usuario especificado del kubeconfig
get-clusters    Mustra los clusters especificados en el kubeconfig
get-contexts    Muestra los contextos
get-users       Muestra los usuarios dentro del kubeconfig
rename-context  Renombra un contexto del kubeconfig
set             Especifica un valor en el kubeconfig
set-cluster     Añade un cluster en el kubeconfig
set-context     Añade un contexto en el kubeconfig
set-credentials Añade un usuario en el kubeconfig
unset           Desliga un valor individual del kubeconfig
use-context     Fija el contexto actual dentro del kubeconfig
view            Muestra los ajustes del kubeconfig o un archivo kubeconfig especificado
```




### Algunos Comandos k3d


> Paramos el cluster
```shell
k3d cluster stop dev
```
> Arrancamos el cluster y restauramos el estado anterior de la parada
```shell
k3d cluster start dev
```
> Borramos el cluster
```shell
k3d cluster delete dev
```
>Listamos los cluster
```shell
k3d cluster list
```

>[Arbol de comandos k3d](https://k3d.io/usage/commands/)


### Ejemplos de despliegue(workloads):

Antes de hacer un despligue comprobamos que estemos el cluster correcto `dev`.

```
kubectl config get-contexts
```

En caso de no te estemos en el cluster `dev` cambiamos de cluster con `kubectl config use-context k3d-dev`

Luego realizamos ciertos despligues:

```shell
kubectl create deployment nginx --image=nginx
```
Con el comando 

```
kubectl get pod
```
obtendremos si el contenedor esta arriba `Running` o todavia se esta levantado `Pending`. Mientras esperamos podemos lanzar el siguiente comando para crear el servicio con el contenedor nginx:

```shell
kubectl create service clusterip nginx --tcp=80:80
```

Si accedemos a la ip del contenedor con la 8080, nos dara un error 404. El error nos da porque todavia falta configurar los servicios internos para el contenedor nginx. Para ello necesitamos un archivo de configuracion yaml y aplicarle los cambios.

Primero creamos el archivo, luego ejecutamos el comando para aplicar el archivo de configuracion `nginx-config.yaml`. Mas tarde veremos el `cat` redirigido al comando `kubectl apply`

```shell
cat <<EOF > nginx-config.yaml
apiVersion: networking.k8s.io/v1beta1
kind: Ingress
metadata:
  name: nginx
  annotations:
    ingress.kubernetes.io/ssl-redirect: "false"
spec:
  rules:
  - http:
      paths:
      - path: /
        backend:
          serviceName: nginx
          servicePort: 80
EOF
```

Hemos creado el archivo y aplicamos el archivo de config.
```shell
kubectl apply -f nginx-config.yaml
```
el resultado sera un mensaje: `ingress.networking.k8s.io/nginx created`

Para ver como nos ha quedado podemos realizar la consulta `kubectl get pod` y nos quedaria algo asi:

```shell
NAME                     READY   STATUS    RESTARTS   AGE
nginx-6799fc88d8-l97lk   1/1     Running   0          16m
```

Esperamos a que el `status` nos indique `Running` y a partir de ahi podemos realizar un testing a la url `localhost:8080` o `ip_linux:8080` de la maquina que corre el linux deberia salir un texto `Welcome to nginx!`.

### Ejemplo de creacion de 3 nodos servidor y 3 nodos agentes:

Ahora vamos a crear un cluster mas parecido a un entorno un poco mas real. Creamos un cluster `dev-mas-real` con 1 balanceador de carga, 3 servidores con 3 agentes. **En el ejemplo usamos la base de datos interna (por defecto sqlite, pero en este caso usa etcd) de k3s para guardar el estado de los nodos servidor, pero para no complicar mas el ejemplo, se podria utilizar una base de datos externa(Mysql, posgres, etcd) para guardar el estado de los nodos servidor. La base de datos interna de k3s entre los nodos servidor se base en quorum entre los nodos servidor .**

**NOTA: los nodos servidores TAMBIEN estan corriendo cargas de trabajo(workloads)**

**NOTA2: es posible que no arranque por problemas de memoria. Este cluster ocupa 3GiB en memoria con 3 nodos servidor y 3 nodos agente.**

```shell
k3d cluster create dev-mas-real \
    --port 8090:80@loadbalancer \
    --port 8143:443@loadbalancer \
    --servers 3 \
    --agents 3
```

El comando puede tardar varios segundos en completarse. Cuando termine con el comando de abajo listaremos los nodos que estan corriendo.:

```shell
kubectl get nodes
```

```
NAME                        STATUS   ROLES                       AGE     VERSION
k3d-dev-mas-real-agent-0    Ready    <none>                      3m45s   v1.20.0+k3s2
k3d-dev-mas-real-agent-1    Ready    <none>                      3m43s   v1.20.0+k3s2
k3d-dev-mas-real-agent-2    Ready    <none>                      3m43s   v1.20.0+k3s2
k3d-dev-mas-real-server-0   Ready    control-plane,etcd,master   4m11s   v1.20.0+k3s2
k3d-dev-mas-real-server-1   Ready    control-plane,etcd,master   3m57s   v1.20.0+k3s2
k3d-dev-mas-real-server-2   Ready    control-plane,etcd,master   3m47s   v1.20.0+k3s2
```


Si necesitamos conocer las ips externas o internas usaremos este comando:

```
kubectl get nodes -o wide
```


- **RECORDATARIO: si hubieramos creado otro cluster despues de `dev-mas-real`, al realizar el comando `kubectl get nodes` saldrian los nodos del cluster creado depues de `dev-mas-real`, osea, que esta creado despues cluster `nuevo` haciendo `get nodes` saldrian los nodos del cluster `nuevo`.**

- **RECORDATORIO2: para cambiar al cluster `dev-mas-real` tendriamos que realizar `kubectl config use-context k3d-dev-mas-real`. Por defecto, el formato del contexto es `k3d-NOMBRE_DEL_CLUSTER`. Si quisieramos cambiar de cluster y ademas especificar el archivo de configuracion `kubectl config --kubeconfig=NOMBRE_DEL_ARCHIVO use-context NOMBRE_DEL_CONTEXTO`.**

Ahora que tenemos los nodos lanzaremos un despligue de una imagen nginx.

```
kubectl create deployment nginx --image=nginx
```

```
kubectl create service clusterip nginx --tcp=80:80
```

```
cat <<EOF | kubectl apply -f -
apiVersion: networking.k8s.io/v1beta1
kind: Ingress
metadata:
  name: nginx
  annotations:
    ingress.kubernetes.io/ssl-redirect: "false"
spec:
  rules:
  - http:
      paths:
      - path: /
        backend:
          serviceName: nginx
          servicePort: 80
EOF
```

Despues de unos segundos saldra un mensaje: `ingress.networking.k8s.io/nginx created`
```
kubectl get pod
```
el resultado es el de abajo y nos fijamos que solo hay uno.

```
NAME                     READY   STATUS    RESTARTS   AGE
nginx-6799fc88d8-9qdgq   1/1     Running   0          21s
```

Ahora que ya tenemos el despliegue escalaremos a 3 replicas del contenedor nginx, el cluster se encargara de repatirlo entre los nodos:

```
kubectl scale deployment nginx --replicas 3
```

Despues de varios segundos, podremos realizar el siguiente comando:

```
kubectl get pod
```

y veremos como se ha auto escalado a 3

```
NAME                     READY   STATUS    RESTARTS   AGE
nginx-6799fc88d8-6542j   1/1     Running   0          8s
nginx-6799fc88d8-9qdgq   1/1     Running   0          48s
nginx-6799fc88d8-9tr69   1/1     Running   0          8s
```

**NOTA: con este despliegue llegaremos a 3.7GiB facilmente. Si es necesario borramos el cluster dev con `k3d cluster delete dev`**

Explicacion visual de como quedaria el cluster con 3 nodos servidor y 3 nodos agentes:

![Diagrama cluster mas real k3d](https://raw.githubusercontent.com/sosan/kubernetes/master/assets/diagrama_cluster_devmasreal_k3d.webp)

Despues de que el cluster se ha creado, es posible añadirle nodos. Por ejemplo añadimos un nuevo nodo llamado `nuevo-nodo` con un rol de `agente` con:

```shell
k3d node create nuevo-nodo \
    --cluster dev-mas-real \
    --role agent
```

- el resultado despues de listar con `k3d node list` es el siguiente:

```
NAME                        ROLE           CLUSTER        STATUS
k3d-dev-mas-real-agent-0    agent          dev-mas-real   running
k3d-dev-mas-real-agent-1    agent          dev-mas-real   running
k3d-dev-mas-real-agent-2    agent          dev-mas-real   running
k3d-dev-mas-real-server-0   server         dev-mas-real   running
k3d-dev-mas-real-server-1   server         dev-mas-real   running
k3d-dev-mas-real-server-2   server         dev-mas-real   running
k3d-dev-mas-real-serverlb   loadbalancer   dev-mas-real   running
k3d-nuevo-nodo-0            agent          dev-mas-real   running
```

- el resultado despues de listar con `kubectl get nodes` es el siguiente:

```
NAME                        STATUS   ROLES                       AGE     VERSION
k3d-dev-mas-real-agent-0    Ready    <none>                      9m50s   v1.20.0+k3s2
k3d-dev-mas-real-agent-1    Ready    <none>                      9m48s   v1.20.0+k3s2
k3d-dev-mas-real-agent-2    Ready    <none>                      9m48s   v1.20.0+k3s2
k3d-dev-mas-real-server-0   Ready    control-plane,etcd,master   10m     v1.20.0+k3s2
k3d-dev-mas-real-server-1   Ready    control-plane,etcd,master   10m     v1.20.0+k3s2
k3d-dev-mas-real-server-2   Ready    control-plane,etcd,master   9m52s   v1.20.0+k3s2
k3d-nuevo-nodo-0            Ready    <none>                      96s     v1.20.0+k3s
```

El nuevo nodo aparece como `k3d-nuevo-nodo-0`

Ahora fijemos un segundo en `VERSION`. Si queremos tener mayor control de la version de la imagen de k3s debido a incompatibilidades, podemos crear el cluster k3s a una determinada version con:

>- Version v1.17.13-k3s2

```shell
k3d cluster create test-viejo \
    --port 8080:80@loadbalancer \
    --port 8443:443@loadbalancer \
    --image rancher/k3s:v1.17.13-k3s2 # version 1.17...
```
>- Version v1.19.3-k3s3

```shell
k3d cluster create test-nuevo \
    --port 8080:80@loadbalancer \
    --port 8443:443@loadbalancer \
    --image rancher/k3s:v1.19.3-k3s3 # version 1.19...
```

[Listado de las imagenes de k3s en docker hub.](https://hub.docker.com/r/rancher/k3s/tags?page=1&ordering=last_updated)


## Creacion de un cluster atraves de un arhcivo yaml

Lo hemos hablado anteriormente que se puede crear un cluster a partir de un archio yaml.


```
cat <<EOF > k3d-configuracion_cluster_nuevo.yaml
apiVersion: k3d.io/v1alpha1
kind: Simple
name: cluster-desdearchivo
servers: 1
agents: 1
exposeAPI:
  hostIP: "0.0.0.0"
  port: "6443" # puerto kubernetes api 6443:6443
image: rancher/k3s:v1.19.4-k3s1
volumes:
  - volume: /tmp:/tmp/fakepath # volumen en el localhost:contenedor
    nodeFilters:
      - all
ports:
  - port: 9080:80 # puerto http localhost:contenedor 8080:80
    nodeFilters:
      - loadbalancer
  - port: 0.0.0.0:8443:443 # https con 8443:443
    nodeFilters:
      - loadbalancer
env:
  - envVar: secreto=token
    nodeFilters:
      - all
labels:
  - label: mejorcluster=etiquetaforzada
    nodeFilters:
      - server[0] # 
      - loadbalancer

options:
  k3d:
    wait: true
    timeout: "60s" # Cuando no se pueda arrancar, no entre en bucle arriba/abajo
    disableLoadbalancer: false
    disableImageVolume: false
  k3s:
    extraServerArgs:
      - --tls-san=127.0.0.1
    extraAgentArgs: []
  kubeconfig:
    updateDefaultKubeconfig: true # actualiza automatica el kubeconfig
    switchCurrentContext: true # cambia al nuevo contexto
EOF
```


```
k3d cluster create --config k3d-configuracion_cluster_nuevo.yaml
```


## Modularidad de k3s


### Fannel

[Flannel](https://github.com/coreos/flannel) es muy buen CNI plugin pero no soporta las politicas de red de kubernetes (**Aunque la politica se aplica sin errores pero no tendran efectos.**)
La modularidad de k3s nos permite remplazar el CNI por defecto por Calico. Para hacer despliegue de Calico, necesitamos 2 caracterisitcas de: 
  - `--k3s-server-arg "--flannel-backend=none"`: desinstala Flannel del contenedor k3s.
  - Caracteristica de Auto-despliegue del k3s : Cualquier archvio en el directorio `/var/lib/rancher/k3s/server/` automaticamente se despliega en Kubernetes de forma similar al realizar `kubectl apply`.

Sabiendo estas 2 caracteristicas es posible lograr que con un archivo yaml crear un cluster de forma muy automatizada.Por ejemplo, la plantilla de configuracion de calico la podemos descargar con:

<!-- wget -q -O - https://github.com/rancher/k3d/blob/main/docs/usage/guides/calico.yaml -->

- Bajar la configuracion de calico:
```shell
wget -q -O - https://raw.githubusercontent.com/rancher/k3d/main/docs/usage/guides/calico.yaml
```

- Creamos el cluster con calico y la configuracion por defecto del `calico.yaml`:

```shell
k3d cluster create calico-cluster \
    --k3s-server-arg "--flannel-backend=none" \
    --volume "$(pwd)/calico.yaml:/var/lib/rancher/k3s/server/manifests/calico.yaml"
```

**La instruccion `--volume "$(pwd)/calico.yaml:/var/lib/rancher/k3s/server/manifests/calico.yaml"` copia el archivo calico.yaml del directorio actual a `/var/lib/rancher/k3s/server/manifests/`**

Vamos ha realizar un test:

Creamos 2 pods `web-nginx` y `test-alpine`
```shell
kubectl run web-nginx \
    --image nginx \
    --labels app=web-nginx \
    --expose --port 80
```

Pequeña explicacion:
- `--image nginx`: instala la imagen nginx
- `--labels app=web-nginx`: crea una etiqueta donde se puede referenciar dentro del cluster
- `--expose --port 80`: abrir el puerto 80

```shell
kubectl run test-alpine \
    --image alpine \
    -- sleep 3600
```

Pequeña explicacion:
- `--image alpine`: instalamos la imagen alpine
- `-- sleep 3600`: duerme 3600 segundos = 1 hora


Intento de que `test-alpine` se conecte al pod `web-nginx`

```shell
kubectl exec -it test-alpine -- wget -qO- --timeout=2 http://web-nginx
```

NO deberia dar problemas de conexion. Ahora que hemos comprobado que la conexion entre los dos existe. Vamos a generar una nueva configuracion de red donde vamos a denegar todo el trafico. **Aplicamos la politica de red directamente, sin generar el archivo**

```shell
cat <<EOF | kubectl apply -f -
kind: NetworkPolicy
apiVersion: networking.k8s.io/v1
metadata:
  name: web-deny-all
spec:
  podSelector:
    matchLabels:
      app: web
  ingress: []
EOF
```

Ahora comprobamos que no existe conexion entre los dos:

```
kubectl exec -it test-alpine -- wget -qO- --timeout=2 http://web-nginx
```

Aplicada la politica de red, vemos que no es posible acceder a `web-nginx`.


https://www.youtube.com/watch?v=ayV8u-t6e5w&feature=emb_logo

https://www.youtube.com/watch?v=mzuXVuccaqI&feature=emb_logo

https://jaas.8x8.vc/
https://dailysport.store/

https://github.com/k3s-io/k3s/pull/2774
https://doc.traefik.io/traefik/migration/v1-to-v2/
https://github.com/k3s-io/k3s/issues/1141

https://joseluisp.hashnode.dev/

https://k3d.io/#requirements
https://github.com/nlpodyssey/spago/blob/main/Dockerfile
https://devopsdirective.com/posts/2020/03/managed-kubernetes-comparison/
https://github.com/inercia/vscode-k3d

<!-- 
ARN[0520] Node 'k3d-dev-mas-real-server-1' is restarting for more than a minute now. Possibly it will recover soon (e.g. when it's waiting to join). Consider using a creation timeout to avoid waiting forever in a Restart Loop. -->


https://github.com/kelseyhightower/kubernetes-the-hard-way/blob/master/docs/04-certificate-authority.md

https://devopsdirective.com/posts/2020/03/managed-kubernetes-comparison/
https://www.twitch.tv/manningpublications


---- Registros
https://k3d.io/usage/guides/registries/
    <!--  --registry-create -->

---


https://k3d.io/usage/guides/exposing_services/


https://k3d.io/


https://github.com/rancher/k3d/releases





https://inerciatech.com/

https://medium.com/google-cloud/kubernetes-nodeport-vs-loadbalancer-vs-ingress-when-should-i-use-what-922f010849e0




https://turbobit.net/download/free/kozjojbwqkeo
The Secret Life of Programs.rar

https://docs.projectcalico.org/security/tutorials/kubernetes-policy-demo/kubernetes-demo









# INSTALACION DE UN RANCHER SERVER

## Requisitos

### Instalacion de un cert-manager

El cert manager es un plugin que automatiza el manejo de los certificados TLS. Permite una interaccion directa con los certificados, renovacion, etc...

Aplicamos el cert manager con los crds

<!-- ```
kubectl apply -f https://github.com/jetstack/cert-manager/releases/download/v1.0.1/cert-manager.crds.yaml
``` -->

kubectl apply -f https://github.com/jetstack/cert-manager/releases/download/v1.1.0/cert-manager.yaml



```
customresourcedefinition.apiextensions.k8s.io/certificaterequests.cert-manager.io created
customresourcedefinition.apiextensions.k8s.io/certificates.cert-manager.io created
customresourcedefinition.apiextensions.k8s.io/challenges.acme.cert-manager.io created
customresourcedefinition.apiextensions.k8s.io/clusterissuers.cert-manager.io created
customresourcedefinition.apiextensions.k8s.io/issuers.cert-manager.io created
customresourcedefinition.apiextensions.k8s.io/orders.acme.cert-manager.io created
```

Generamos el cert-manager.yaml

```
cat <<EOF > cert-manager.yaml
---
kind: Namespace
apiVersion: v1
metadata:
  name: cert-manager
  labels:
    app: cert-manager
---
apiVersion: helm.cattle.io/v1
kind: HelmChart
metadata:
  name: cert-manager
  namespace: kube-system
spec:
  chart: cert-manager
  repo: https://charts.jetstack.io
  targetNamespace: cert-manager
  version: v1.1.0
EOF
```




Copiamos dentro del nodo servidor el manifiesto del cert-manager. La estructura del comando es:

Veritfcamos que la instalacion sea correcta:



```
docker cp cert-manager.yaml k3d-NOMBRE_SERVIDOR-0:/var/lib/rancher/k3s/server/manifests/
```

para saber el nombre de los nodos hacemos un `k3d node list` en nuestro caso utilizamos el anterior cluster `dev-mas-real`
```
docker cp cert-manager.yaml k3d-dev-server-0:/var/lib/rancher/k3s/server/manifests/
```

```
kubectl -n cert-manager get pods -l app.kubernetes.io/instance=cert-manager
```

```
kubectl get pods --namespace cert-manager
```

```
NAME                                      READY   STATUS    RESTARTS   AGE
cert-manager-5597cff495-zgc47             1/1     Running   0          95s
cert-manager-cainjector-bd5f9c764-tqlm9   1/1     Running   0          95s
cert-manager-webhook-5f57f59fbc-qshwl     1/1     Running   0          95s
```


### Instalacion de rancher server


```
cat <<EOF > rancher-server.yaml
---
kind: Namespace
apiVersion: v1
metadata:
  name: cattle-system
---
apiVersion: helm.cattle.io/v1
kind: HelmChart
metadata:
  name: rancher
  namespace: kube-system
spec:
  chart: rancher
  repo: https://releases.rancher.com/server-charts/latest
  targetNamespace: cattle-system
  version: v2.5.2
  set:
    hostname: rancher.172.17.0.2.xip.io
    replicas: 1
EOF
```



docker cp rancher-server.yaml k3d-dev-server-0:/var/lib/rancher/k3s/server/manifests/

LLeva algo de tiempo arrancar, ves lanzando el comando hasta que veas el STATUS `Running`:

```
kubectl get pods --namespace cattle-system -l app=rancher
```

LA instalacion ocupara cerca de 4GiB



https://www.youtube.com/watch?v=YAeb6WjZ1qw&list=PLQ1vMvN4ZpX9tSYkX0xzP8eV_D3DtB79l&index=7&t=2302s


Herramientas

https://docs.tilt.dev/example_nodejs.html => herramienta donde build en 2segundos

https://stedolan.github.io/jq/download/ => heramienta para modificar los jsons




kubectl top node


https://en.sokube.ch/post/k3s-k3d-k8s-a-new-perfect-match-for-dev-and-test

Realizar un Indice ...
