```
gcloud config set compute/region us-central1 && gcloud config set compute/zone us-central1-a
```

```
export PROJECT_ID=$(gcloud config list --format 'value(core.project)')
```

```
gsutil mb gs://$PROJECT_ID
```

subir el fichero `resources-install-web.sh` a storage > browser


```
gsutil cp resources-install-web.sh gs://$PROJECT_ID
```

```
gcloud compute instances create servidorweb \
    --tags http-server \
    --metadata startup-script-url=gs://$PROJECT_ID/resources-install-web.sh
```

```
gcloud compute firewall-rules create allow-http \
    --target-tags http-server \
    --allow tcp:80
```