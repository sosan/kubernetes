
# Task 1: Create a Docker image and store the Dockerfile

```
gcloud config set compute/region us-east1 && gcloud config set compute/zone us-east1-b
```

```
export PROJECT_ID=$(gcloud config list --format 'value(core.project)') && export GOOGLE_CLOUD_PROJECT=${PROJECT_ID}
```

```
source <(gsutil cat gs://cloud-training/gsp318/marking/setup_marking.sh)
```

> el codigo es:

```
#!/bin/bash
mkdir ~/marking
cd ~/marking
gsutil cp gs://cloud-training/gsp318/marking/step*.sh .
chmod +x *.sh
echo "export PATH=$PATH:$HOME/marking" >> ~/.bashrc
export PATH=$PATH:$HOME/marking
```

```
cd .. && gcloud source repos clone valkyrie-app --project=$PROJECT_ID && cd valkyrie-app
```

```
cat <<EOF > Dockerfile
FROM golang:1.10
WORKDIR /go/src/app
COPY source .
RUN go install -v
ENTRYPOINT ["app","-single=true","-port=8080"]
EOF
```

`
docker build -t valkyrie-app:v0.0.1 .
`
```
cd .. && \
cd marking && \
source step1.sh && \
cd .. && \
cd valkyrie-app
```

```
docker run -d -p 8080:8080 valkyrie-app:v0.0.1
```

```
cd .. && \
cd marking && \
source step2.sh && \
cd .. && \
cd valkyrie-app
```

# Task 3: Push the Docker image in the Container Repository

```
docker tag valkyrie-app:v0.0.1 gcr.io/${PROJECT_ID}/valkyrie-app:v0.0.1
```

```
docker push gcr.io/$GOOGLE_CLOUD_PROJECT/valkyrie-app:v0.0.1
```

# Task 4: Create and expose a deployment in Kubernetes

sed -i s#IMAGE_HERE#gcr.io/$GOOGLE_CLOUD_PROJECT/valkyrie-app:v0.0.1#g k8s/deployment.yaml

```
gcloud container clusters get-credentials valkyrie-dev --region us-east1-d
```

```
kubectl create -f k8s/deployment.yaml && \
kubectl create -f k8s/service.yaml
```

>>> chequear load balancer


# Task 5: Update the deployment with a new version of valkyrie-app


kubectl scale deployment.apps/valkyrie-dev --replicas=3

git merge origin/kurt-dev

docker build -t valkyrie-app:v0.0.2 .
docker tag valkyrie-app:v0.0.2 gcr.io/${PROJECT_ID}/valkyrie-app:v0.0.2
docker images
docker push gcr.io/${PROJECT_ID}/valkyrie-app:v0.0.2


```
kubectl edit deployment valkyrie-dev
```
cambiar imagen de `valkyrie-app:v0.0.1` a `valkyrie-app:v0.0.2`

# Task 6: Create a pipeline in Jenkins to deploy your app

comprobar que no hay ningun contenedor valkyrie corriendo:

```
docker ps
```

```
docker rm -f $(docker ps -q)
```

>conseguir la password para mas adelante:
```
printf $(kubectl get secret cd-jenkins -o jsonpath="{.data.jenkins-admin-password}" | base64 --decode);echo
```

```
export POD_NAME=$(kubectl get pods --namespace default -l "app.kubernetes.io/component=jenkins-master" -l "app.kubernetes.io/instance=cd" -o jsonpath="{.items[0].metadata.name}")
kubectl port-forward $POD_NAME 8080:8080 >> /dev/null &
```


desde la consola pulsa webpreview 
user: admin
password = PASSWORD ANTERIOR | o4EAT3qUw0

> PROJECT REPOSITORY: (COPY & PASTE)

```
echo https://source.developers.google.com/p/${PROJECT_ID}/r/valkyrie-app
```

sed -i s#YOUR_PROJECT#${PROJECT_ID}#g Jenkinsfile
sed -i s#green#orange#g source/html.go

git config --global user.email ${PROJECT_ID}
git config --global user.name ${PROJECT_ID}

git add .
git commit -m "cambio de verde a naranja"
git push origin master



