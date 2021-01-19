# Securing Applications on Kubernetes Engine - Three Examples

1 hour 9 Credits

## GSP482

![Google Cloud Self-Paced Labs](https://cdn.qwiklabs.com/GMOHykaqmlTHiqEeQXTySaMXYPHeIvaqa2qHEzw6Occ%3D)

## Overview

In this lab you will learn how Kubernetes Engine security features can be used to grant varying levels of privilege to applications based on their particular requirements.

When configuring security, applications should be granted the smallest set of privileges that still allows them to operate correctly. When applications have more privileges than they need, they are more dangerous when compromised. In a Kubernetes cluster, these privileges can be grouped into the following broad levels:

*   **Host access:** describes what permissions an application has on it's host node, outside of its container. This is controlled via Pod and Container security contexts, as well as app armor profiles.
*   **Network access:** describes what other resources or workloads an application can access via the network. This is controlled with NetworkPolicies.
*   **Kubernetes API access:** describes which API calls an application is allowed to make against. API access is controlled using the Role Based Access Control (RBAC) model via Role and RoleBinding definitions.

In the sections below, you will explore three scenarios with distinct security requirements at each of these levels:

<table>
<tbody>
<tr>
<th>Scenario</th>
<th>Host Access</th>
<th>Network Access</th>
<th>Kubernetes API Access</th>
</tr>
<tr>
<td>Hardened Web Server (NGINX)</td>
<td>Minimal</td>
<td>Must be able to serve port 80</td>
<td>None</td>
</tr>
<tr>
<td>Kubernetes Controller (Demo)</td>
<td>Minimal</td>
<td>Must be able to access the API Server</td>
<td>List and Patch Pods</td>
</tr>
<tr>
<td>Privileged Daemonset (AppArmor Loader)</td>
<td>Privileged</td>
<td>None</td>
<td>None</td>
</tr>
</tbody>
</table>

This lab was created by GKE Helmsman engineers to give you a better understanding of GKE security. You can view this demo on Github [here](https://github.com/GoogleCloudPlatform/gke-binary-auth-demo.git). We encourage any and all to contribute to our assets!

## Architecture

The lab uses three applications to illustrate the scenarios described above:

### Hardened Web Server (nginx)

Creates an nginx deployment whose pods have their host-level access restricted by an AppArmor profile and whose network connectivity is restricted by a NetworkPolicy.

### System Daemonset (AppArmor Loader)

Creates a daemonset responsible for loading (installing) the AppArmor profile applied to the nginx pods on each node. Loading profiles requires more privileges than can be provided via Capabilities, so it's containers are given full privileges via their SecurityContexts.

### Simplified Kubernetes Controller (Pod Labeler)

The pod-labeler deployment creates a single pod that watches all other pods in the default namespace and periodically labels them. This requires access to the Kubernetes API server, which is configured via RBAC using a ServiceAccount, Role, and RoleMapping.

## Setup

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

    If you see the **Choose an account** page, click **Use Another Account**. ![Choose an account](https://cdn.qwiklabs.com/eQ6xPnPn13GjiJP3RWlHWwiMjhooHxTNvzfg1AL2WPw%3D)

3.  In the **Sign in** page, paste the username that you copied from the Connection Details panel. Then copy and paste the password.

    **_Important:_** You must use the credentials from the Connection Details panel. Do not use your Qwiklabs credentials. If you have your own Google Cloud account, do not use it for this lab (avoids incurring charges).

4.  Click through the subsequent pages:

    *   Accept the terms and conditions.
    *   Do not add recovery options or two-factor authentication (because this is a temporary account).
    *   Do not sign up for free trials.

After a few moments, the Cloud Console opens in this tab.

**Note:** You can view the menu with a list of Google Cloud Products and Services by clicking the **Navigation menu** at the top-left. ![Cloud Console Menu](https://cdn.qwiklabs.com/9vT7xPlxoNP%2FPsK0J8j0ZPFB4HnnpaIJVCDByaBrSHg%3D)

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

For full documentation of `gcloud` see the [gcloud command-line tool overview](https://cloud.google.com/sdk/gcloud).

### Set your region and zone

Certain Compute Engine resources live in regions and zones. A region is a specific geographical location where you can run your resources. Each region has one or more zones.

Learn more about regions and zones and see a complete list in [Regions & Zones documentation](https://cloud.google.com/compute/docs/regions-zones/).

Run the following to set a region and zone for your lab (you can use the region/zone that's best for you):

    gcloud config set compute/region us-central1
    gcloud config set compute/zone us-central1-a

### Clone demo

    git clone https://github.com/GoogleCloudPlatform/gke-security-scenarios-demo

Go into the directory for this demo:

    cd gke-security-scenarios-demo

## Deployment steps

The steps below will walk you through using terraform to deploy a Kubernetes Engine cluster that you will then use for installing a pod that uses an AppArmor profile and another pod that securely interacts with the Kubernetes API.

### Lab setup

This lab will use the following Google Cloud Service APIs, and have been enabled for you:

*   `compute.googleapis.com`
*   `container.googleapis.com`
*   `cloudbuild.googleapis.com`

In addition, the Terraform configuration takes three parameters to determine where the Kubernetes Engine cluster should be created:

*   `project`
*   `region`
*   `zone`

Patch the Terraform file to use an n1-Standard-1 virtual machine for the Bastion Host

    sed -i 's/f1-micro/n1-standard-1/g' ~/gke-security-scenarios-demo/terraform/variables.tf

For simplicity, these parameters are specified in a file named `terraform.tfvars`, in the `terraform` directory. To ensure the appropriate APIs are enabled and to generate the `terraform/terraform.tfvars` file based on your gcloud defaults, run:

    make setup-project

Type "y" when asked to confirm.

This will enable the necessary Service APIs, and it will also generate a `terraform/terraform.tfvars` file with the following keys. Verify the values themselves will match the output of `gcloud config list` by running:

    cat terraform/terraform.tfvars

### Provisioning the Kubernetes Engine Cluster

Next, from the project root, use `make` apply the terraform configuration and create the environment:

    make create

This command may take longer than 5 minutes to run—please be patient.

Click _Check my progress_ to verify the objective. <ql-activity-tracking step="1">Deployment Steps</ql-activity-tracking>

## Validation

Once complete, terraform will output a message indicating successful creation of the cluster.

    ...snip...
    google_container_cluster.primary: Still creating... (3m20s elapsed)
    google_container_cluster.primary: Still creating... (3m30s elapsed)
    google_container_cluster.primary: Creation complete after 3m33s (ID: gke-security-demo)

    Apply complete! Resources: 7 added, 0 changed, 0 destroyed.

Once the apply completes, ssh to the bastion host:

    gcloud compute ssh gke-tutorial-bastion

Then execute:

    kubectl get pods --all-namespaces

(Output)

    NAMESPACE     NAME                                                          READY     STATUS        RESTARTS   AGE
    kube-system   calico-node-g7mlz                                             2/2       Running       0          56s
    kube-system   calico-node-rzbbc                                             2/2       Running       0          56s
    kube-system   calico-node-vertical-autoscaler-8b959b949-tnwhm               1/1       Running       0          2m
    kube-system   calico-node-vnw26                                             2/2       Running       0          56s
    kube-system   calico-typha-dbf7c7b6d-bpm2r                                  1/1       Running       0          1m
    kube-system   calico-typha-horizontal-autoscaler-5545fbd5d6-4wbgg           1/1       Running       0          2m
    kube-system   calico-typha-vertical-autoscaler-54d8f88b84-2pwdz             1/1       Running       0          2m
    kube-system   event-exporter-v0.2.1-5f5b89fcc8-vb5bc                        2/2       Running       0          2m
    kube-system   fluentd-gcp-scaler-7c5db745fc-hcpr5                           1/1       Running       0          1m
    kube-system   fluentd-gcp-v3.0.0-ks89p                                      2/2       Running       0          1m
    kube-system   fluentd-gcp-v3.0.0-mfbg4                                      2/2       Running       0          1m
    kube-system   heapster-v1.5.3-7ff6c8d4cc-tbf8d                              3/3       Running       0          1m
    kube-system   ip-masq-agent-f8kdp                                           1/1       Running       0          1m
    kube-system   ip-masq-agent-h7fbh                                           1/1       Running       0          1m
    kube-system   ip-masq-agent-nlkl9                                           1/1       Running       0          1m
    kube-system   kube-dns-788979dc8f-c6zv4                                     4/4       Running       0          1m
    kube-system   kube-dns-788979dc8f-xt97h                                     4/4       Running       0          2m
    kube-system   kube-dns-autoscaler-79b4b844b9-cmhns                          1/1       Running       0          1m
    kube-system   kube-proxy-gke-gke-security-demo-default-pool-a55f1c91-6lxr   1/1       Running       0          1m
    kube-system   kube-proxy-gke-gke-security-demo-default-pool-a55f1c91-7smw   1/1       Running       0          1m
    kube-system   kube-proxy-gke-gke-security-demo-default-pool-a55f1c91-9khs   1/1       Running       0          1m
    kube-system   l7-default-backend-5d5b9874d5-57mb5                           1/1       Running       0          2m
    kube-system   metrics-server-v0.2.1-7486f5bd67-f68bq                        2/2       Running       0          1m

## Set up Nginx

Again on the bastion host, execute:

    kubectl apply -f manifests/nginx.yaml

(Output)

    deployment.apps/nginx created
    service/nginx-lb created
    networkpolicy.networking.k8s.io/nginx-from-external created

Then execute:

    kubectl get pods

(Output)

    NAME                     READY     STATUS    RESTARTS   AGE
    nginx-775877bc7b-5hk65   0/1       Blocked   0          13s
    nginx-775877bc7b-jwcv9   0/1       Blocked   0          13s
    nginx-775877bc7b-xc7p2   0/1       Blocked   0          13s

You should see that while the pods have been created, they're in a Blocked state. The nginx pods are blocked because the manifest includes an AppArmor profile that doesn't exist on the nodes:

    ...
        metadata:
          annotations:
            container.apparmor.security.beta.kubernetes.io/nginx: localhost/k8s-nginx
    ...

You can confirm this by executing the following command showing the `Message` field:

    kubectl describe pod -l app=nginx

(Output)

    ...snip...
    Labels:         app=nginx
                    pod-template-hash=3314336736
    Annotations:    container.apparmor.security.beta.kubernetes.io/nginx=localhost/k8s-nginx
                    kubernetes.io/limit-ranger=LimitRanger plugin set: cpu request for container nginx
    Status:         Pending
    Reason:         AppArmor
    Message:        Cannot enforce AppArmor: profile "k8s-nginx" is not loaded
    IP:
    Controlled By:  ReplicaSet/nginx-775877bc7b
    Containers:
      nginx:
        Container ID:
    ...snip...

Click _Check my progress_ to verify the objective. <ql-activity-tracking step="2">Set up Nginx</ql-activity-tracking>

## Set up AppArmor-loader

In order to resolve this, the relevant AppArmor profile must be loaded. Because you don't know on which nodes the nginx pods will be allocated, you must deploy the AppArmor profile to all nodes. The way you'll deploy this, ensuring all nodes are covered, is via a daemonset [https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/#what-is-a-daemonset](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/#what-is-a-daemonset).

The included `apparmor-loader.yaml` is a highly privileged manifest, as it needs to write a file onto each node. The contents of that file are in the `configmap` included in the same.

To deploy it, execute the following on the bastion host:

    kubectl apply -f manifests/apparmor-loader.yaml

(Output)

    namespace/apparmor created
    configmap/apparmor-profiles created
    daemonset.apps/apparmor-loader created
    networkpolicy.networking.k8s.io/deny-apparmor-communication created

The nginx nodes are in Backoff by this point, so the fastest way to resolve it is by deleting the existing nginx pods and allowing kubernetes to re-create them.

On the bastion host, run:

    kubectl delete pods -l app=nginx

(Output)

    pod "nginx-775877bc7b-5hk65" deleted
    pod "nginx-775877bc7b-jwcv9" deleted
    pod "nginx-775877bc7b-xc7p2" deleted

After which, you can confirm that nginx is running successfully with:

    kubectl get pods

(Output)

    NAME                     READY     STATUS    RESTARTS   AGE
    nginx-775877bc7b-n2rxx   1/1       Running   0          28s
    nginx-775877bc7b-q7qfb   1/1       Running   0          28s
    nginx-775877bc7b-zzbdl   1/1       Running   0          28s

Another test that will show the successful deployment of nginx is by executing:

    kubectl get services

(Output)

    NAME         TYPE           CLUSTER-IP      EXTERNAL-IP     PORT(S)        AGE
    kubernetes   ClusterIP      10.137.0.1      <none>          443/TCP        7m
    nginx-lb     LoadBalancer   10.137.15.122   [NGINX_IP]      80:30033/TCP   54s

The external IP listed by the `nginx-lb` is publicly accessible and can be reached with `curl`, `wget`, or a web browser.

If the external IP doesn't appear, rerun the command until it does.

Navigate to the IP displayed for the `nginx-lb`, and you will see:

![nginx browser](https://cdn.qwiklabs.com/IcmI16rZHZ3dN2nQrxU9qhe9ltqkDFB4YRBREantEDc%3D)

Click _Check my progress_ to verify the objective. <ql-activity-tracking step="3">Set up AppArmor-loader</ql-activity-tracking>

## Set up Pod-labeler

In this scenario you'll go through the process of deploying an application that requires access to the Kubernetes API. This pod will update the metadata of other pods.

First, execute:

    kubectl get pods --show-labels

(Output)

    NAME                          READY   STATUS    RESTARTS   AGE         LABELS
    nginx-7d796c869d-bjppj        1/1     Running   0          2m48s   app=nginx,pod-template-hash=7d796c869d
    nginx-7d796c869d-k8g54        1/1     Running   0          2m48s   app=nginx,pod-template-hash=7d796c869d
    nginx-7d796c869d-ng8jg        1/1     Running   0          2m48s   app=nginx,pod-template-hash=7d796c869d

Rerun the command until all pods are in a running state.

Now execute:

    kubectl apply -f manifests/pod-labeler.yaml

(Output)

    role.rbac.authorization.k8s.io/pod-labeler created
    serviceaccount/pod-labeler created
    rolebinding.rbac.authorization.k8s.io/pod-labeler created
    deployment.apps/pod-labeler created

This can take up to two minutes to finish the creation of the new pod, but once this has finished, re-execute:

    kubectl get pods --show-labels

(Output)

    NAME                          READY     STATUS    RESTARTS   AGE       LABELS
    nginx-d99fc9b6c-ff6hw         1/1       Running   0          5m        app=nginx,pod-template-hash=855975627,updated=1530656545.33
    nginx-d99fc9b6c-m7gc2         1/1       Running   0          5m        app=nginx,pod-template-hash=855975627,updated=1530656545.35
    nginx-d99fc9b6c-zbkf9         1/1       Running   0          5m        app=nginx,pod-template-hash=855975627,updated=1530656545.36
    pod-labeler-8845f6488-px9hl   1/1       Running   0          55s       app=pod-labeler,pod-template-hash=440192044,updated=1530656545.38

And you'll see that the pods have an additional "updated=..." label. You may have to run this command a couple of times to see the new label.

Click _Check my progress_ to verify the objective. <ql-activity-tracking step="4">Set up Pod-labeler</ql-activity-tracking>

## Teardown

Qwiklabs will take care of shutting down all the resources used for this lab, but here’s what you would need to do to clean up your own environment to save on cost and to be a good cloud citizen.

Log out of the bastion host:

    exit

Run the following to destroy the environment (type `yes` at the prompt to confirm)

    make teardown

(Output)

    ...snip...
    module.network.google_compute_subnetwork.cluster-subnet: Destruction complete after 26s
    module.network.google_compute_network.gke-network: Destroying... (ID: kube-net)
    module.network.google_compute_network.gke-network: Still destroying... (ID: kube-net, 10s elapsed)
    module.network.google_compute_network.gke-network: Still destroying... (ID: kube-net, 20s elapsed)
    module.network.google_compute_network.gke-network: Destruction complete after 25s

    Destroy complete! Resources: 7 destroyed.

Click _Check my progress_ to verify the objective. <ql-activity-tracking step="5">Teardown</ql-activity-tracking>

## Troubleshooting in your own environment

### The install script fails with a `Permission denied` when running Terraform

The credentials that Terraform is using do not provide the necessary permissions to create resources in the selected projects. Ensure that the account listed in `gcloud config list` has necessary permissions to create resources. If it does, regenerate the application default credentials using `gcloud auth application-default login`.

### Error during scp

Occasionally, the gke-tutorial-bastion host will not be ready for the subsequent `scp` command, in this case there will be an error that looks like: ![terraform scp error](https://cdn.qwiklabs.com/n2eYkw%2B13xo05M23qDRw5YxoHaPLRf%2Fl%2FITlaQvuHlQ%3D) If this happens, simply re-run `make create`.

### Invalid fingerprint error during Terraform operations

Terraform occasionally complains about an invalid fingerprint, when updating certain resources. If you see the error below, simply re-run the command. ![terraform fingerprint error](https://cdn.qwiklabs.com/MovqAAg0Chnh9QY%2BsrRn5WUzsqrVNSRw6sB2lF46U1w%3D)

## Congratulations

### Finish your Quest

![gke_best_security_badge.png](https://cdn.qwiklabs.com/maNNnEGq9cLqRzxQkol4VrPlDzyruxMCXVMlMRHwRhM%3D)

This self-paced lab is part of the Qwiklabs [Google Kubernetes Engine Best Practices: Security](https://google.qwiklabs.com/quests/64) Quest. A Quest is a series of related labs that form a learning path. Completing this Quest earns you the badge above, to recognize your achievement. You can make your badge (or badges) public and link to them in your online resume or social media account. [Enroll in this Quest](https://google.qwiklabs.com/quests/64/enroll) and get immediate completion credit if you've taken this lab. [See other available Qwiklabs Quests](https://google.qwiklabs.com/catalog).

### Take your next lab

Continue your Quest with [Using Role-based Access Control in Kubernetes Engine](https://google.qwiklabs.com/catalog_lab/1720), or check out these suggestions:

*   [Hardening Default GKE Cluster Configurations](https://google.qwiklabs.com/catalog_lab/1722)

## Next steps / learn more

*   [AppArmor](https://kubernetes.io/docs/tutorials/clusters/apparmor/)

*   [Terraform - Kubernetes Engine](https://www.terraform.io/docs/providers/google/r/container_cluster.html)

*   [Bastion Host](https://en.wikipedia.org/wiki/Bastion_host)

*   [RBAC](https://kubernetes.io/docs/reference/access-authn-authz/rbac/)

*   [Securing Kubernetes Engine](https://cloud.google.com/kubernetes-engine/docs/how-to/hardening-your-cluster)

*   [Security Contexts](https://kubernetes.io/docs/tasks/configure-pod-container/security-context/)

##### Manual Last Updated: December 14, 2020

##### Lab Last Tested: December 14, 2020

Copyright 2020 Google LLC. This software is provided as-is, without warranty or representation for any use or purpose. Your use of it is subject to your agreement with Google
