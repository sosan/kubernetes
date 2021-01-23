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

## Task 0: Download the necessary files

gsutil -m cp gs://cloud-training/gsp335/* .

## Task 1: Setup Cluster


```
gcloud container clusters create "demo" \
  --zone "us-central1-c" \
  --machine-type "n1-standard-4" \
  --num-nodes "2"
 
```

>>>>>>>>>> falta el network policy----
kubectl apply -f ./manifests/network-policy.yaml

```
gcloud container clusters get-credentials demo --zone us-central1-c
```



## Task 2: Setup WordPress



gcloud sql instances create demo-db --root-password='dkja")#("$54545sdfjs)"#$'

INSTANCIA_SQL=$(gcloud sql instances describe demo-db --format="value(connectionName)")

us-central1



## Task 3: Setup Ingress with TLS











## Task 4: Set up Network Policy




## Task 5: Setup Binary Authorization


## Task 6: Setup Pod Security Policy






