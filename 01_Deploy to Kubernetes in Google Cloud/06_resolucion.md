
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
gcloud source repos clone valkyrie-app --project=$PROJECT_ID && cd valkyrie-app
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
docker run -p 8080:8080 valkyrie-app:v0.0.1
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

# fff

sed -i s#IMAGE_HERE#gcr.io/$GOOGLE_CLOUD_PROJECT/valkyrie-app:v0.0.1#g k8s/deployment.yaml

```
gcloud container clusters get-credentials valkyrie-dev
```

```
kubectl create -f k8s/deployment.yaml && \
kubectl create -f k8s/service.yaml
```

>>> chequear load balancer
