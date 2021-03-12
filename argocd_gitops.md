He hecho un mini tutorial de gitops con argocd sobre gcp.

Consideraciones:
- Se pueden usar otras herramientas(flux, gitkube, jenkinsx, werf...), la instalacion, configuracion, mantenimiento son diferentes.
- La opcion con mas caracteristicas y pesada es jenkinsx con al menos 3 nodos (6vcpus)
- NO tiene configurado firewall ni difrentes profundidades de vpc, ni vault, encriptacion de secretos, passwords, envs, etc...
- Seguridad: https://argoproj.github.io/argo-cd/security_considerations/
- El cluster va a usar 6GiB de memoria

```
gcloud config set compute/region us-east1 && gcloud config set compute/zone us-east1-c
```

```
export PROJECT_ID=$(gcloud config list --format 'value(core.project)')
echo $PROJECT_ID
```

Tardara unos varios minutos y consumira unos 3.4GiB

```
gcloud container clusters create cluster-test \
  --release-channel "regular" \
  --machine-type n1-standard-2 \
  --num-nodes 1 \
  --enable-ip-alias \
  --enable-shielded-nodes \
  --workload-pool=$PROJECT_ID.svc.id.goog \
  --tags cluster-test
```

Saldra mensaje parecido a:
```
WARNING: The Pod address range limits the maximum size of the cluster. Please refer to https://cloud.google.com/kubernetes-engine/docs/how-to/flexible-pod-cidr to learn how to optimize IP address allocation.
WARNING: Starting with version 1.19, newly created clusters and node-pools will have COS_CONTAINERD as the default node image when no image type is specified.
Creating cluster cluster-test in us-east1-c... Cluster is being health-checked (master is healthy)...done.
kubeconfig entry generated for cluster-test.
NAME          LOCATION    MASTER_VERSION    MASTER_IP      MACHINE_TYPE   NODE_VERSION      NUM_NODES  STATUS
cluster-test  us-east1-c  1.18.12-gke.1210  34.73.153.132  n1-standard-2  1.18.12-gke.1210  1          RUNNING
```


```
gcloud container clusters get-credentials cluster-test
```
Creacion de namespace
```
kubectl create namespace n-argocd
```

<!-- ```
kubectl create clusterrolebinding cluster-admin-binding \
    --clusterrole cluster-admin \
    --user $(gcloud config get-value account)
```


añadido un firewall, afecta a la red default, conveninete 3 zonas de red

```
gcloud compute firewall-rules create n-argocd-fire \
    --source-ranges=0.0.0.0/0 \
    --target-tags cluster-test \
    --allow=tcp:80 \
    --direction=INGRESS
``` -->

Antes de lanzar el comando, asegurate que haya sufienciente memoria, ya que los pods no se levantaran si no hay suficiente memoria. Los pods ocuparan unos 2GiB
```
kubectl apply -n n-argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```
Resultado:

```
customresourcedefinition.apiextensions.k8s.io/applications.argoproj.io created
customresourcedefinition.apiextensions.k8s.io/appprojects.argoproj.io created
serviceaccount/argocd-application-controller created
serviceaccount/argocd-dex-server created
serviceaccount/argocd-redis created
serviceaccount/argocd-server created
role.rbac.authorization.k8s.io/argocd-application-controller created
role.rbac.authorization.k8s.io/argocd-dex-server created
...
```


Listado de los pods en namespace n-argocd. Tardara unos minutos en estar todos los pods ready.

```
kubectl get pods -n n-argocd
```

Resultado:
```
NAME                                  READY   STATUS              RESTARTS   AGE
argocd-application-controller-0       0/1     ContainerCreating   0          1s
argocd-dex-server-6dc48777d4-vhfr7    0/1     Init:0/1            0          3s
argocd-redis-66b48966cb-rcf6g         0/1     ContainerCreating   0          2s
argocd-repo-server-7b579957d9-d5k7d   0/1     ContainerCreating   0          2s
argocd-server-7fdb567cd4-wrshw        0/1     ContainerCreating   0          2s
```

Cuando esten todos ready 1/1, podemos listar los servicios con:

```
kubectl get service -n n-argocd
```

Resultado:

```
NAME                    TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)                      AGE
argocd-dex-server       ClusterIP   10.88.0.195    <none>        5556/TCP,5557/TCP,5558/TCP   2m34s
argocd-metrics          ClusterIP   10.88.12.45    <none>        8082/TCP                     2m33s
argocd-redis            ClusterIP   10.88.12.158   <none>        6379/TCP                     2m33s
argocd-repo-server      ClusterIP   10.88.15.142   <none>        8081/TCP,8084/TCP            2m33s
argocd-server           ClusterIP   10.88.2.54     <none>        80/TCP,443/TCP               2m32s
argocd-server-metrics   ClusterIP   10.88.7.15     <none>        8083/TCP                     2m32s
```


convertimos argocd-server de clusterip a loadbalancer:
```
kubectl patch svc argocd-server \
  -n n-argocd \
  -p '{"spec": {"type": "LoadBalancer"}}'
```

Resultado:
```
service/argocd-server patched
```
> NOTA: Es posible que segun el entorno no sea posible obtener una ip externa cambiando a LoadBalancer 

>      apiVersion: extensions/v1beta1
>      kind: Ingress
>      metadata:
>        name: argocd-server-ingress
>        namespace: argocd
>        annotations:
>          kubernetes.io/ingress.class: nginx
>          nginx.ingress.kubernetes.io/force-ssl-redirect: "true"
>          nginx.ingress.kubernetes.io/ssl-passthrough: "true"
>      spec:
>        rules:
>        - host: argocd.example.com
>          http:
>            paths:
>            - backend:
>                serviceName: argocd-server
>                servicePort: https


Ahora concretamente necesitamos la ip del argocd-server, es posible que tengamos que lanzar el comando un par de veces porque tardara un par de segundos aprovisionar una ip externa
IP EXTERNA:
```
kubectl get svc argocd-server -n n-argocd -o jsonpath="{.status.loadBalancer.ingress[0].ip}" && echo
```

tambien podemos mostrar la ip con el awk

```
kubectl get service argocd-server -n n-argocd | awk '{print $4}'
```

Ahora necesitamos la contraseña del servidor, el usuario es admin. 

Bastante recomendable que cambiemos la contraseña desde argocd cli. Instalacion del cliente: https://argoproj.github.io/argo-cd/cli_installation/

Desde argocd cli con un `argocd login <ARGOCD_SERVER_IP_OR_URL> && argocd account update-password` cambiamos la contraseña

CONTRASEÑA DE ADMIN:
```
kubectl get pods \
  -n n-argocd \
  -l app.kubernetes.io/name=argocd-server \
  -o "jsonpath={.items[0].metadata.name}" && echo
```

Vamos a introducir como url la IP EXTERNA en un navegador y nos lanzara la web de argocd server. Introducimos el usuario admin y la contraseña.

