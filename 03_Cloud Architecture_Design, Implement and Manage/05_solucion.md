```
gcloud config set compute/region us-central
```
```
gcloud config set compute/zone us-central1-a
```

```
export PROJECT_ID=$(gcloud config list --format 'value(core.project)')
```

```
gsutil cp -r gs://${PROJECT_ID}/echo-web.tar.gz .
```
```
tar -xvf echo-web.tar.gz && cd echo-web
```

```
docker build -t echo-app:v1 .
```

```
docker tag echo-app:v1 gcr.io/${PROJECT_ID}/echo-app:v1
```

```
docker push gcr.io/${PROJECT_ID}/echo-app:v1
```

```
gcloud container clusters create echo-cluster2 --num-nodes "2" --zone us-central1-a --machine-type n1-standard-2
```

```
kubectl create deployment echo-web --image=gcr.io/${PROJECT_ID}/echo-app:v1
```
```
kubectl expose deployment echo-web --type=LoadBalancer --port=80 --target-port=8000
```

```
kubectl get svc
```

**visitar ip externa**

