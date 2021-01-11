## LISTADO COMANDOS
https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands

## INSTALAR MICRO K8S

```
sudo snap install microk8s --classic
```

```
sudo usermod -a -G microk8s $USER
sudo chown -f -R $USER ~/.kube
```


## INFORME MICRO K8S STATUS

```
microk8s status
```

## GENERAR ALIAS

```
alias kubectl='microk8s kubectl'
```

## GENERAR TOKEN Y PORT FORWARD DEL DASHBOARD

```
token=$(microk8s kubectl -n kube-system get secret | grep default-token | cut -d " " -f1)
microk8s kubectl -n kube-system describe secret $token
```

```
microk8s kubectl port-forward -n kube-system service/kubernetes-dashboard 10443:443
```

## ARRANCAR DASHBOARD, DNS, ISTIO



```
microk8s enable dashboard dns istio
```


```
microk8s dashboard-proxy
```
