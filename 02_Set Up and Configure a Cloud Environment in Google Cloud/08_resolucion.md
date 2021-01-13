## set default region y zone
```
gcloud config set compute/region us-east1
```
```
gcloud config set compute/zone us-east1-b
```



```
export PROJECT_ID=ID_DEL_PROYECTO
```


## creacion del vpc

```
gcloud compute networks create griffin-dev-vpc --subnet-mode=custom --mtu=1460 --bgp-routing-mode=regional

```

## creacion de los subnets

```
gcloud compute networks subnets create griffin-dev-wp --range=192.168.16.0/20 --network=griffin-dev-vpc --region=us-east1
```

```
gcloud compute networks subnets create griffin-dev-mgmt --range=192.168.32.0/20 --network=griffin-dev-vpc --region=us-east1
```


## creacion de las reglas firewall

```
gcloud compute firewall-rules create allow-griffin-dev-vpc \
  --network griffin-dev-vpc \
  --allow tcp,udp,icmp \
  --source-ranges 192.168.16.0/20,192.168.32.0/20
```
```
gcloud compute firewall-rules create allow-internal-ports-22-3389 \
  --network griffin-dev-vpc \
  --allow tcp:22,tcp:3389,icmp
```

## compobar si se han creado los networks
```
gcloud compute networks list
```


## usando el depploy manager

**copiar el codigo:**
```
gsutil cp -r gs://cloud-training/gsp321/dm . && cd dm
```

**cambiamos el SET_REGION por la region:**
```
sed -i 's/SET_REGION/us-east1/g' prod-network.yaml
```

```
cat prod-network.yaml
```
**deberia quedar asi:**

```
imports:
- path: prod-network.jinja

resources:
- name: prod-network
  type: prod-network.jinja
  properties:
    region: us-east1
```

## Deployment Manager

```
gcloud deployment-manager deployments create advanced-configuration --config prod-network.yaml
```

## create bastion host

```
gcloud beta compute instances create bastion-host \
  --zone=us-east1-b \
  --machine-type=n1-standard-1 \
  --subnet=griffin-dev-mgmt \
  --network-tier=STANDARD \
  --maintenance-policy=MIGRATE \
  --scopes=https://www.googleapis.com/auth/devstorage.read_only,https://www.googleapis.com/auth/logging.write,https://www.googleapis.com/auth/monitoring.write,https://www.googleapis.com/auth/servicecontrol,https://www.googleapis.com/auth/service.management.readonly,https://www.googleapis.com/auth/trace.append \
  --image=debian-10-buster-v20201216 \
  --image-project=debian-cloud \
  --boot-disk-size=10GB \
  --boot-disk-type=pd-standard \
  --boot-disk-device-name=bastion-host \
  --no-shielded-secure-boot \
  --shielded-vtpm \
  --shielded-integrity-monitoring \
  --reservation-affinity=any
```


## crear sql instancia

> databases > sql
> mysql 5.7

nombre: `griffin-dev-db`
contraseña: `dkja")#("$54545sdfjs)"#$`
zona: `us-east1`

```
gcloud sql connect griffin-dev-db --user=root --quiet
```

```
CREATE DATABASE wordpress;
GRANT ALL PRIVILEGES ON wordpress.* TO "wp_user"@"%" IDENTIFIED BY "stormwind_rules";
FLUSH PRIVILEGES;
```


## crear clusters

gcloud beta container clusters create "griffin-dev" \
  --zone "us-east1-b" \
  --no-enable-basic-auth \
  --cluster-version "1.16.15-gke.4901" \
  --release-channel "None" \
  --machine-type "n1-standard-4" \
  --image-type "COS_CONTAINERD" \
  --disk-type "pd-standard" \
  --disk-size "100" \
  --metadata disable-legacy-endpoints=true \
  --scopes "https://www.googleapis.com/auth/devstorage.read_only","https://www.googleapis.com/auth/logging.write","https://www.googleapis.com/auth/monitoring","https://www.googleapis.com/auth/servicecontrol","https://www.googleapis.com/auth/service.management.readonly","https://www.googleapis.com/auth/trace.append" \
  --num-nodes "2" \
  --enable-stackdriver-kubernetes \
  --enable-ip-alias \
  --network "projects/${PROJECT_ID}/global/networks/griffin-dev-vpc" \
  --subnetwork "projects/${PROJECT_ID}/regions/us-east1/subnetworks/griffin-dev-wp" \
  --default-max-pods-per-node "110" \
  --no-enable-master-authorized-networks \
  --addons HorizontalPodAutoscaling,HttpLoadBalancing \
  --enable-autoupgrade \
  --enable-autorepair \
  --max-surge-upgrade 1 \
  --max-unavailable-upgrade 0



## desde cloud shell

>copiar codigo:

```
cd .. && gsutil cp -r gs://cloud-training/gsp321/wp-k8s .
```

```
sed -i 's/username_goes_here/wp_user/g' wp-env.yaml && \
sed -i 's/password_goes_here/stormwind_rules/g' wp-env.yaml && \
sed -i 's/database/griffin-dev-db/g' wp-env.yaml
```

```
kubectl apply -f wp-env.yaml && \
kubectl get persistentVolume
```

```
gcloud iam service-accounts keys create key.json \
        --iam-account=cloud-sql-proxy@$GOOGLE_CLOUD_PROJECT.iam.gserviceaccount.com && \
    kubectl create secret generic cloudsql-instance-credentials \
        --from-file key.json
```