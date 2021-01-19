```
gcloud config set compute/region us-central1 && gcloud config set compute/zone us-central1-a
```

```
export PROJECT_ID=$(gcloud config list --format 'value(core.project)')
```

***realizar las tareas antes de empezar***
```
gcloud container clusters get-credentials echo-cluster --zone=us-central1-a
kubectl create deployment echo-web --image=gcr.io/qwiklabs-resources/echo-app:v1
kubectl expose deployment echo-web --type=LoadBalancer --port 80 --target-port 8000
```

```
gsutil cp gs://sureskills-ql/challenge-labs/ch05-k8s-scale-and-update/echo-web-v2.tar.gz .
```

```
tar xvzf echo-web-v2.tar.gz && rm echo-web-v2.tar.gz
```
```
docker build -t echo-app:v2 .
docker tag echo-app:v2 gcr.io/$PROJECT_ID/echo-app:v2
docker push gcr.io/$PROJECT_ID/echo-app:v2
```
```
kubectl edit deploy echo-web
```

cambiar a - image: (...)/echo-app:v2

```
kubectl scale deploy echo-web --replicas=2
```
