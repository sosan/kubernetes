
```
export DEFAULT_REGION=us-east1 && \
export DEFAULT_ZONE=us-east1-b
```

```
export CUSTOM_US_REGION=us-central1 && \
export CUSTOM_US_ZONE=us-central1-c
```



```
gcloud config set compute/region $DEFAULT_REGION && \
gcloud config set compute/zone $DEFAULT_ZONE
```

```
export PROJECT_ID=$(gcloud config list --format 'value(core.project)') && \
echo $PROJECT_ID
```

## Task 0: Download the necessary files

mkdir solution && cd solution && \
gsutil -m cp gs://cloud-training/gsp335/* .

## Task 1: Setup Cluster

```
gcloud container clusters create blog \
  --zone $CUSTOM_US_ZONE \
  --machine-type n1-standard-4 \
  --num-nodes 2 \
  --enable-network-policy
```

```
gcloud container clusters get-credentials blog --zone $CUSTOM_US_ZONE --project ${PROJECT_ID}
```

## Task 2: Setup WordPress

#### Setup the Cloud SQL database and database username and password

```
gcloud sql instances create blog-db --root-password='dkja")#("$54545sdfjs)"#$'
```

```
gcloud sql connect blog-db --user=root --quiet
```

```
CREATE DATABASE wordpress;
GRANT ALL PRIVILEGES ON wordpress.* TO "wordpress"@"%" IDENTIFIED BY "passwordtest";
FLUSH PRIVILEGES;
```

```
exit
```


#### Create a service account for access to your WordPress database from your WordPress instances

```
gcloud iam service-accounts create wordpress --display-name wordpress
```

```
gcloud projects add-iam-policy-binding "${PROJECT_ID}" \
  --role=roles/cloudsql.client \
  --member="serviceAccount:wordpress@${PROJECT_ID}.iam.gserviceaccount.com"
```

```
gcloud iam service-accounts keys create key.json \
  --iam-account=wordpress@${PROJECT_ID}.iam.gserviceaccount.com
```

```
kubectl create secret generic cloudsql-instance-credentials \
  --from-file key.json
```

```
kubectl create secret generic cloudsql-db-credentials \
  --from-literal username=wordpress \
  --from-literal password='passwordtest'
```

```
export EMAIL_SERVICE_ACCOUNT=wordpress@${PROJECT_ID}.iam.gserviceaccount.com
```
--- conexion a blog 

```
gcloud compute ssh blog --zone=$CUSTOM_US_ZONE
```

```
MYSQL_IP=$(gcloud sql instances describe blog-db --format="value(ipAddresses.ipAddress)")
```
```
mysql --host=$MYSQL_IP --user=root --password
```

#### Create the WordPress deployment and service

```
kubectl create -f volume.yaml
```
```
INSTANCIA_SQL=$(gcloud sql instances describe blog-db --format="value(connectionName)") && \
echo $INSTANCIA_SQL
```

```
sed -i s#INSTANCE_CONNECTION_NAME#${INSTANCIA_SQL}#g wordpress.yaml && \
cat wordpress.yaml | grep ${INSTANCIA_SQL}
```
```
kubectl apply -f wordpress.yaml
```

## Task 3: Setup Ingress with TLS

#### Set up nginx-ingress environment

helm repo add stable https://charts.helm.sh/stable && \
helm repo update

obsoleto pero de todas formas lo instalamos:

helm install nginx-ingress stable/nginx-ingress 

<!--
mas actualizado 
helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx && \
helm install ingress-nginx ingress-nginx/ingress-nginx
helm repo update
-->


#### Set up your DNS record

kubectl get svc

>explorar la ip externa si da un 404

<!-- sed -i s#nginx-ingress-controller#ingress-nginx-controller#g add_ip.sh -->
```
```
```
source add_ip.sh
```

>explorar el dns name si da 404


<!--
#!/bin/bash -xe
#get IP address
IP=$(kubectl describe svc nginx-ingress-controller | grep Ingress | cut -c27-)
if [ "$IP" == "" ]
then
  echo "Can't find nginx-ingress-controller External Ingress IP address!!???"
  exit -1
fi
# check user name
if [ "$USER" == "" ]
then
  echo "Can't find user name!!???"
  exit -1
fi
# send it
USER_NAME=$(echo $USER | tr -d '_')
REQUEST="{\"ip\":\"${IP}\",\"user_name\":\"${USER_NAME}\"}"
curl -H "Content-Type: application/json" -H "Authorization: bearer $(gcloud auth  print-identity-token)" https://us-central1-qwiklabs-resources.cloudfunctions.net/add-dns-record -d "${REQUEST}"
if [ $? -eq 0 ]
then
  echo Your DNS record is ${USER}.labdns.xyz
fi
-->



#### Set up cert-manager.io


kubectl apply -f https://github.com/jetstack/cert-manager/releases/download/v0.16.0/cert-manager.yaml

<!-- kubectl apply -f https://github.com/jetstack/cert-manager/releases/download/v1.1.0/cert-manager.yaml -->

en caso de encontrarnos algun problema tipo "denegado", elevamos privilegios con:

```
kubectl create clusterrolebinding cluster-admin-binding \
  --clusterrole=cluster-admin \
  --user=$(gcloud config get-value core/account)
```

sed -i s#LAB_EMAIL_ADDRESS#${EMAIL_SERVICE_ACCOUNT}#g issuer.yaml

kubectl apply -f issuer.yaml

> puede lanzar error por no encontrar el endpoint, tarda en crearse



#### Configure nginx-ingress to use an encrypted certificate for your site

no funca

sed -i s#HOST_NAME#${USER}.labdns.xyz#g ingress.yaml
kubectl apply -f ingress.yaml



## Task 4: Set up Network Policy


cat network-policy.yaml

```
cat <<EOF > network-policy.yaml
kind: NetworkPolicy
apiVersion: networking.k8s.io/v1
metadata:
 name: default-deny-all-ingress
 namespace: default
spec:
 podSelector:
   matchLabels: {}
policyTypes:
 - Ingress
---
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-nginx-access-to-wordpress
  namespace: default
spec:
  podSelector:
    matchLabels:
      app: wordpress
  policyTypes:
  - Ingress
  ingress:
  - from:
    - podSelector:
        matchLabels:
          app: nginx-ingress
---
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-nginx-access-to-internet
spec:
  podSelector:
    matchLabels:
      app: nginx-ingress
  policyTypes:
  - Ingress
  ingress:
  - {}
EOF
```

kubectl apply --validate=false -f network-policy.yaml


## Task 5: Setup Binary Authorization

gcloud services enable \
  binaryauthorization.googleapis.com


gcloud container clusters update blog \
  --enable-binauthz \
   --zone $CUSTOM_US_ZONE

cat <<EOF > /tmp/policy.yaml
admissionWhitelistPatterns:
- namePattern: gcr.io/google_containers/*
- namePattern: gcr.io/google-containers/*
- namePattern: k8s.gcr.io/*
- namePattern: gke.gcr.io/*
- namePattern: gcr.io/stackdriver-agents/*
- namePattern: docker.io/library/wordpress:latest
- namePattern: us.gcr.io/k8s-artifacts-prod/ingress-nginx/*
- namePattern: gcr.io/cloudsql-docker/*
- namePattern: quay.io/jetstack/*
defaultAdmissionRule:
  enforcementMode: ENFORCED_BLOCK_AND_AUDIT_LOG
  evaluationMode: ALWAYS_DENY
globalPolicyEvaluationMode: ENABLE
name: projects/${PROJECT_ID}/policy
EOF


gcloud container binauthz policy import /tmp/policy.yaml


## Task 6: Setup Pod Security Policy

kubectl apply -f psp-role.yaml && \
kubectl apply -f psp-use.yaml


sed -i s#extensions/v1beta1#policy/v1beta1#g psp-restrictive.yaml

kubectl apply -f psp-restrictive.yaml


