```
gcloud config set compute/region us-central1 && gcloud config set compute/zone us-central1-a
```

## creacion de la instancia apache

```
gcloud compute instances create apache \
    --machine-type=n1-standard-1
```


## creacion de las reglas firewall

gcloud compute firewall-rules create allow-acces \
      --allow=tcp:80

gcloud compute firewall-rules create allow-ssh \
    --allow=tcp:22


gcloud compute ssh Apache

```
sudo apt-get update -y && \
sudo apt-get install apache2 -y && \
sudo service apache2 restart
```


