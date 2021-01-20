```
gcloud config set compute/region us-central1 && gcloud config set compute/zone us-central1-a
```

```
export PROJECT_ID=$(gcloud config list --format 'value(core.project)')
```



gcloud compute networks create securenetwork \
    --subnet-mode=custom

gcloud compute networks subnets create securenetwork-subnet \
    --network=securenetwork \
    --range=172.16.0.0/24


gcloud compute instances create vm-securehost \
    --network-interface=network=securenetwork,subnet=securenetwork-subnet \
    --network-interface=network=default \
    --tags=rdp \
    --machine-type n1-standard-2 \
    --boot-disk-type pd-ssd \
    --boot-disk-size 50GB \
    --image-family windows-2016 \
    --image-project windows-cloud


gcloud compute instances delete-access-config vm-securehost \
    --access-config-name="external-nat" \
    --network-interface="nic0"

gcloud compute instances delete-access-config vm-securehost \
    --access-config-name="external-nat" \
    --network-interface="nic1"



gcloud compute instances create vm-bastionhost \
    --network-interface=network=securenetwork,subnet=securenetwork-subnet \
    --network-interface=network=default \
    --tags=rdp \
    --machine-type n1-standard-2 \
    --boot-disk-type pd-ssd \
    --boot-disk-size 50GB \
    --image-family windows-2016 \
    --image-project windows-cloud

gcloud compute instances delete-access-config vm-bastionhost \
    --access-config-name="external-nat" \
    --network-interface="nic1"

> esperar unos segundos

gcloud compute reset-windows-password vm-bastionhost \
    --user app_admin \
    --zone us-central1-a \
    --quiet

ip_address: 35.232.159.48
password:   a&dMKUdg~.=t8uN
username:   app_admin

gcloud compute reset-windows-password vm-securehost \
    --user app_admin \
    --zone us-central1-a \
    --quiet


password: m3kXN4@#S;t+R>D
username: app_admin


gcloud compute firewall-rules create allow-rdp --direction=INGRESS \
    --target-tags rdp \
    --allow=tcp:3389 \
    --network=securenetwork \
    --source-ranges=0.0.0.0/0

instalar iis(web server) desde el server manager