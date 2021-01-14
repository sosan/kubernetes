## set default region y zone
```
gcloud config set compute/region us-east1
```
```
gcloud config set compute/zone us-east1-b
```

```
export PROJECT_ID=$(gcloud config list --format 'value(core.project)')
export GOOGLE_CLOUD_PROJECT=${PROJECT_ID}

echo $PROJECT_ID
echo $GOOGLE_CLOUD_PROJECT
```


## creacion del vpc

```
gcloud compute networks create griffin-dev-vpc \
  --subnet-mode=custom \
  --mtu=1460 \
  --bgp-routing-mode=regional

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
  --target-tags ssh \
  --source-ranges 192.168.16.0/20,192.168.32.0/20
```
```
gcloud compute firewall-rules create allow-internal-ports-22-3389 \
  --network griffin-dev-vpc \
  --target-tags ssh \
  --allow tcp:22,tcp:3389,icmp
```

============ quitar ===============

gcloud compute firewall-rules create fw-ssh-dev --source-ranges=0.0.0.0/0 --target-tags ssh --allow=tcp:22 --network=griffin-dev-vpc

gcloud compute firewall-rules create fw-ssh-prod --source-ranges=0.0.0.0/0 --target-tags ssh --allow=tcp:22 --network=griffin-prod-vpc

============================

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
gcloud deployment-manager deployments create prod-network --config prod-network.yaml
```

## create bastion host

```
gcloud beta compute instances create bastion \
  --zone=us-east1-b \
  --network-interface=network=griffin-dev-vpc,subnet=griffin-dev-mgmt \
  --network-interface=network=griffin-prod-vpc,subnet=griffin-prod-mgmt \
  --tags=ssh \
  --machine-type=n1-standard-1 \
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

```
gcloud sql instances create griffin-dev-db --root-password='dkja")#("$54545sdfjs)"#$'
```

gcloud services enable servicenetworking.googleapis.com \
  --project=$PROJECT_ID

<!-- gcloud services enable servicenetworking.googleapis.com --project=$PROJECT_ID -->


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

```
gcloud container clusters create "griffin-dev" \
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
```

```
gcloud container clusters get-credentials griffin-dev --zone us-east1-b
```


## desde cloud shell

>copiar codigo:

```
cd .. && \
  gsutil cp -r gs://cloud-training/gsp321/wp-k8s . && \
  cd wp-k8s
```

```
sed -i 's/username_goes_here/wp_user/g' wp-env.yaml && \
sed -i 's/password_goes_here/stormwind_rules/g' wp-env.yaml
```

```
kubectl create -f wp-env.yaml
```

> esperar unos segundos:
```
kubectl get persistentVolume
```

```

gcloud iam service-accounts keys create key.json --iam-account=$PROJECT_ID@$PROJECT_ID.iam.gserviceaccount.com
```

```
kubectl create secret generic cloudsql-instance-credentials \
  --from-file key.json
```

```
INSTANCIA_SQL=$(gcloud sql instances describe griffin-dev-db --format="value(connectionName)")
```

```
sed -i s/YOUR_SQL_INSTANCE/$INSTANCIA_SQL/g wp-deployment.yaml
```

```
kubectl create -f wp-deployment.yaml
```

```
kubectl create -f wp-service.yaml
```
