```
mkdir deployment_manager && \
cd deployment_manager && \
gsutil cp gs://spls/gsp302/* .
```
```
gcloud deployment-manager deployments create testeo --config=qwiklabs.yaml
```
> saltara error
```
nano qwiklabs.yaml
```
```
nano qwiklabs.jinja
```
```
gcloud deployment-manager deployments delete testeo
```
```
gcloud deployment-manager deployments create testeo --config=qwiklabs.yaml
```
```
gcloud compute instances list
```

comprobar vm-test la ip externa