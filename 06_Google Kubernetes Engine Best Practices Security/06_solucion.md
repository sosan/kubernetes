# Create a simple GKE cluster

export MY_ZONE=us-central1-a


gcloud container clusters create simplecluster --zone $MY_ZONE --num-nodes 2 --metadata=disable-legacy-endpoints=false


kubectl version


# Run a Google Cloud-SDK pod

kubectl run -it --rm gcloud --image=google/cloud-sdk:latest --restart=Never -- bash

curl -s http://metadata.google.internal/computeMetadata/v1beta1/instance/name


curl -s http://metadata.google.internal/computeMetadata/v1/instance/name


curl -s -H "Metadata-Flavor: Google" http://metadata.google.internal/computeMetadata/v1/instance/name

kubectl run -it --rm gcloud --image=google/cloud-sdk:latest --restart=Never -- bash

## Explore the GKE node bootstrapping credentials

curl -s http://metadata.google.internal/computeMetadata/v1beta1/instance/attributes/


curl -s http://metadata.google.internal/computeMetadata/v1beta1/instance/attributes/kube-env



## Leverage the Permissions Assigned to this Node Pool's Service Account

curl -s -H "Metadata-Flavor: Google" http://metadata.google.internal/computeMetadata/v1/instance/service-accounts/default/scopes


## Deploy a pod that mounts the host filesystem

```
cat <<EOF | kubectl apply -f -
apiVersion: v1
kind: Pod
metadata:
  name: hostpath
spec:
  containers:
  - name: hostpath
    image: google/cloud-sdk:latest
    command: ["/bin/bash"]
    args: ["-c", "tail -f /dev/null"]
    volumeMounts:
    - mountPath: /rootfs
      name: rootfs
  volumes:
  - name: rootfs
    hostPath:
      path: /
EOF
```

kubectl get pod

## Explore and compromise the underlying host


kubectl exec -it hostpath -- bash

chroot /rootfs /bin/bash

exit
exit


kubectl delete pod hostpath

# Deploy a second node pool

gcloud beta container node-pools create second-pool --cluster=simplecluster --zone=$MY_ZONE --num-nodes=1 --metadata=disable-legacy-endpoints=true --workload-metadata-from-node=SECURE


## Run a Google Cloud-SDK pod

kubectl run -it --rm gcloud --image=google/cloud-sdk:latest --restart=Never --overrides='{ "apiVersion": "v1", "spec": { "securityContext": { "runAsUser": 65534, "fsGroup": 65534 }, "nodeSelector": { "cloud.google.com/gke-nodepool": "second-pool" } } }' -- bash



## Explore various blocked endpoints

curl -s http://metadata.google.internal/computeMetadata/v1beta1/instance/name

curl -s -H "Metadata-Flavor: Google" http://metadata.google.internal/computeMetadata/v1/instance/attributes/kube-env

curl -s -H "Metadata-Flavor: Google" http://metadata.google.internal/computeMetadata/v1/instance/name

exit

## Deploy PodSecurityPolicy objects

kubectl create clusterrolebinding clusteradmin --clusterrole=cluster-admin --user="$(gcloud config list account --format 'value(core.account)')"


```
cat <<EOF | kubectl apply -f -
---
apiVersion: policy/v1beta1
kind: PodSecurityPolicy
metadata:
  name: restrictive-psp
  annotations:
    seccomp.security.alpha.kubernetes.io/allowedProfileNames: 'docker/default'
    apparmor.security.beta.kubernetes.io/allowedProfileNames: 'runtime/default'
    seccomp.security.alpha.kubernetes.io/defaultProfileName:  'docker/default'
    apparmor.security.beta.kubernetes.io/defaultProfileName:  'runtime/default'
spec:
  privileged: false
  # Required to prevent escalations to root.
  allowPrivilegeEscalation: false
  # This is redundant with non-root + disallow privilege escalation,
  # but we can provide it for defense in depth.
  requiredDropCapabilities:
    - ALL
  # Allow core volume types.
  volumes:
    - 'configMap'
    - 'emptyDir'
    - 'projected'
    - 'secret'
    - 'downwardAPI'
    # Assume that persistentVolumes set up by the cluster admin are safe to use.
    - 'persistentVolumeClaim'
  hostNetwork: false
  hostIPC: false
  hostPID: false
  runAsUser:
    # Require the container to run without root privileges.
    rule: 'MustRunAsNonRoot'
  seLinux:
    # This policy assumes the nodes are using AppArmor rather than SELinux.
    rule: 'RunAsAny'
  supplementalGroups:
    rule: 'MustRunAs'
    ranges:
      # Forbid adding the root group.
      - min: 1
        max: 65535
  fsGroup:
    rule: 'MustRunAs'
    ranges:
      # Forbid adding the root group.
      - min: 1
        max: 65535
EOF
```

```
cat <<EOF | kubectl apply -f -
---
kind: ClusterRole
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: restrictive-psp
rules:
- apiGroups:
  - extensions
  resources:
  - podsecuritypolicies
  resourceNames:
  - restrictive-psp
  verbs:
  - use
EOF
```


```
cat <<EOF | kubectl apply -f -
---
# All service accounts in kube-system
# can 'use' the 'permissive-psp' PSP
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: restrictive-psp
  namespace: default
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: restrictive-psp
subjects:
- apiGroup: rbac.authorization.k8s.io
  kind: Group
  name: system:authenticated
EOF
```


# Enable PodSecurity policy

gcloud beta container clusters update simplecluster --zone $MY_ZONE --enable-pod-security-policy



# Deploy a blocked pod that mounts the host filesystem

gcloud iam service-accounts create demo-developer

MYPROJECT=$(gcloud config list --format 'value(core.project)')

gcloud projects add-iam-policy-binding "${MYPROJECT}" --role=roles/container.developer --member="serviceAccount:demo-developer@${MYPROJECT}.iam.gserviceaccount.com"

gcloud iam service-accounts keys create key.json --iam-account "demo-developer@${MYPROJECT}.iam.gserviceaccount.com"


gcloud auth activate-service-account --key-file=key.json

gcloud container clusters get-credentials simplecluster --zone $MY_ZONE


```
cat <<EOF | kubectl apply -f -
apiVersion: v1
kind: Pod
metadata:
  name: hostpath
spec:
  containers:
  - name: hostpath
    image: google/cloud-sdk:latest
    command: ["/bin/bash"]
    args: ["-c", "tail -f /dev/null"]
    volumeMounts:
    - mountPath: /rootfs
      name: rootfs
  volumes:
  - name: rootfs
    hostPath:
      path: /
EOF
```

```
cat <<EOF | kubectl apply -f -
apiVersion: v1
kind: Pod
metadata:
  name: hostpath
spec:
  securityContext:
    runAsUser: 1000
    fsGroup: 2000
  containers:
  - name: hostpath
    image: google/cloud-sdk:latest
    command: ["/bin/bash"]
    args: ["-c", "tail -f /dev/null"]
EOF
```

kubectl get pod hostpath -o=jsonpath="{ .metadata.annotations.kubernetes\.io/psp }"










