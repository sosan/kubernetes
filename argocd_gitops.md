He hecho un mini tutorial de gitops con argocd sobre gcp.

He elegido argocd porque tiene una interfaz web, se pueden usar otras herramientas(flux, gitkube, jenkinsx, werf...), cada una tiene un enfoque tirando a mas desarrollador o a mas ingeniero.

```
gcloud config set compute/region us-east1 && gcloud config set compute/zone us-east1-c
```


```
export PROJECT_ID=$(gcloud config list --format 'value(core.project)')
echo $PROJECT_ID
```

NOTA: NO tiene configurado firewall ni difrentes profundidades de vpc, ni secretos, etc...


Tardara unos varios minutos

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
kubectl create namespace dev
```

```
kubectl create clusterrolebinding cluster-admin-binding \
    --clusterrole cluster-admin \
    --user $(gcloud config get-value account)
```


añadido un firewall, afecta a la red default, conveninete 3 zonas de red

```
gcloud compute firewall-rules create devfire \
    --source-ranges=0.0.0.0/0 \
    --target-tags cluster-test \
    --allow=tcp:80 \
    --direction=INGRESS
```

```
kubectl apply -n dev -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```
Mensajes:

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


Listado de los pods en namespace dev. Tardara unos minutos en estar todos los pods ready.

```
kubectl get pods -n dev
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
kubectl get service -n dev
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
  -n dev \
  -p '{"spec": {"type": "LoadBalancer"}}'
```

Resultado:
```
service/argocd-server patched
```

Ahora concretamente necesitamos la ip del argocd-server, es posible que tengamos que lanzar el comando un par de veces porque tardara un par de segundos aprovisionar una ip externa
IP EXTERNA:
```
kubectl get svc argocd-server -n dev -o jsonpath="{.status.loadBalancer.ingress[0].ip}" && echo
```

tambien podemos sacar la ip con el awk

```
kubectl get service argocd-server -n dev | awk '{print $4}'
```

Ahora necesitamos la contraseña del servidor, el usuario es admin

PASSWORD:
```
kubectl get pods \
  -n dev \
  -l app.kubernetes.io/name=argocd-server \
  -o "jsonpath={.items[0].metadata.name}" && echo
```

Vamos a introducir como url la IP EXTERNA en un navegador y nos lanzara la web de argocd server. Introducimos el usuario admin y la contraseña.

