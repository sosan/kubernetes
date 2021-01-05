# How to Use a Network Policy on Google Kubernetes Engine

<div class="lab-preamble__details subtitle-headline-1"><span>1 hour</span> <span>5 Credits</span>

<div class="lab__rating">[](/focuses/5572/reviews?parent=catalog)<a data-target="#lab-review-modal" data-toggle="modal">Rate Lab</a></div>

</div>

</div>

<div class="js-markdown-instructions markdown-lab-instructions" id="markdown-lab-instructions">

## GSP480

![Google Cloud Self-Paced Labs](https://cdn.qwiklabs.com/GMOHykaqmlTHiqEeQXTySaMXYPHeIvaqa2qHEzw6Occ%3D)

## Overview

This lab will show you how to improve the security of your Kubernetes Engine by applying fine-grained restrictions to network communication.

The [Principle of Least Privilege](https://en.wikipedia.org/wiki/Principle_of_least_privilege) is widely recognized as an important design consideration in enhancing the protection of critical systems from faults and malicious behavior. It suggests that every component must be able to access **only** the information and resources that are necessary for its legitimate purpose. This document demonstrates how the Principle of Least Privilege can be implemented within the Kubernetes Engine network layer.

Network connections can be restricted at two tiers of your Kubernetes Engine infrastructure. The first, and coarser grained, mechanism is the application of Firewall Rules at the Network, Subnetwork, and Host levels. These rules are applied outside of the Kubernetes Engine at the VPC level.

While Firewall Rules are a powerful security measure, and Kubernetes enables you to define even finer grained rules via Network Policies. [Network Policies](https://kubernetes.io/docs/concepts/services-networking/network-policies/) are used to limit intra-cluster communication. Network policies do not apply to pods attached to the host's network namespace.

For this lab you will provision a private Kubernetes Engine cluster and a bastion host with which to access it. A bastion host provides a single host that has access to the cluster, which, when combined with a private Kubernetes network, ensures that the cluster isn't exposed to malicious behavior from the internet at large. Bastions are particularly useful when you do not have VPN access to the cloud network.

Within the cluster, a simple HTTP server and two client pods will be provisioned. You will learn how to use a Network Policy and labeling to only allow connections from one of the client pods.

This lab was created by GKE Helmsman engineers to give you a better understanding of GKE Binary Authorization. You can view this demo on Github [here](https://github.com/GoogleCloudPlatform/gke-binary-auth-demo.git)

*   We encourage any and all to contribute to our assets!

## Architecture

You will define a private Kubernetes cluster. Since the cluster is private, neither the API nor the worker nodes will be accessible from the internet. Instead, you will define a bastion host and use a firewall rule to enable access to it. The bastion's IP address is defined as an authorized network for the cluster, which grants it access to the API.

Within the cluster, provision three workloads:

1.  hello-server: this is a simple HTTP server with an internally-accessible endpoint
2.  hello-client-allowed: this is a single pod that repeatedly attempts to access hello-server. The pod is labeled such that the Network Policy will allow it to connect to hello-server.
3.  hello-client-blocked: this runs the same code as hello-client-allowed but the pod is labeled such that the Network Policy will **not** allow it to connect to hello-server.

![architecture](https://cdn.qwiklabs.com/hk%2FxJLR97fdUVv6wUeXrNnsWxUr7OaAbdS8uCBFOUX4%3D)

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

### Clone demo

Clone the resources needed for this lab by running:

    git clone https://github.com/GoogleCloudPlatform/gke-network-policy-demo.git

Go into the directory for the demo:

    cd gke-network-policy-demo

### Set your region and zone

Certain Compute Engine resources live in regions and zones. A region is a specific geographical location where you can run your resources. Each region has one or more zones.

<aside>Learn more about regions and zones and see a complete list in [Regions & Zones documentation](https://cloud.google.com/compute/docs/regions-zones/).</aside>

Run the following to set a region and zone for your lab (you can use the region/zone that's best for you):

    gcloud config set compute/region us-central1
    gcloud config set compute/zone us-central1-a

## Lab setup

This lab will use the following Google Cloud Service APIs, and have been enabled for you:

*   `compute.googleapis.com`
*   `container.googleapis.com`
*   `cloudbuild.googleapis.com`

In addition, the Terraform configuration takes three parameters to determine where the Kubernetes Engine cluster should be created:

*   `project ID`
*   `region`
*   `zone`

For simplicity, these parameters are specified in a file named `terraform.tfvars`, in the `terraform` directory. To ensure the appropriate APIs are enabled and to generate the `terraform/terraform.tfvars` file based on your gcloud defaults, run:

    make setup-project

Type `y` when asked to confirm.

This will enable the necessary Service APIs, and it will also generate a `terraform/terraform.tfvars` file with the following keys. Verify the values themselves will match the output of `gcloud config list` by running:

    cat terraform/terraform.tfvars

Check the latest version for [Terraform Google Provider](https://github.com/terraform-providers/terraform-provider-google/releases)

Set the required terraform provider version with the following command:

    sed -i 's/~> 2.17.0/~> 2.17.0/g' terraform/provider.tf

### Provisioning the Kubernetes Engine Cluster

Next, apply the Terraform configuration within the project root:

    make tf-apply

When prompted, review the generated plan and enter `yes` to deploy the environment.

This will take several minutes to deploy.

## Validation

Once complete, Terraform will output a message indicating successful creation of the cluster.

    ...snip...
    google_container_cluster.primary: Still creating... (3m0s elapsed)
    google_container_cluster.primary: Still creating... (3m10s elapsed)
    google_container_cluster.primary: Still creating... (3m20s elapsed)
    google_container_cluster.primary: Still creating... (3m30s elapsed)
    google_container_cluster.primary: Creation complete after 3m34s (ID: gke-demo-cluster)

    Apply complete! Resources: 5 added, 0 changed, 0 destroyed.

You can also confirm the cluster was created successfully by ensuring that Network Policies are enabled for the new cluster.

Verify that `networkPolicy.enabled` is `true` and `networkPolicy.provider` is `CALICO` by running:

    gcloud container clusters describe gke-demo-cluster | grep  -A2 networkPolicy

(Output)

      networkPolicyConfig: {}
    clusterIpv4Cidr: 10.0.92.0/22
    createTime: '2019-04-18T19:41:27+00:00'
    --
    networkPolicy:
      enabled: true
      provider: CALICO

#### Test Completed Task

Click **Check my progress** to verify your performed task. If you have successfully deployed necessary infrastructure with Terraform, you will see an assessment score.

<ql-activity-tracking step="1">Use Terraform to set up the necessary infrastructure (Lab setup)</ql-activity-tracking>

Now ssh into the bastion for the remaining steps:

    gcloud compute ssh gke-demo-bastion

The newly-created cluster will now be available for the standard `kubectl` commands on the bastion.

## Installing the hello server

The test application consists of one simple HTTP server, deployed as `hello-server`, and two clients, one of which will be labeled `app=hello` and the other `app=not-hello`.

All three services can be deployed by applying the hello-app manifests. On the bastion, run:

    kubectl apply -f ./manifests/hello-app/

(Output)

    deployment.apps/hello-client-allowed created
    deployment.apps/hello-client-blocked created
    service/hello-server created
    deployment.apps/hello-server created

Verify all three pods have been successfully deployed:

    kubectl get pods

You will see one running pod for each of _hello-client-allowed_, _hello-client-blocked_, and _hello-server_ deployments.

    NAME                                      READY     STATUS    RESTARTS   AGE
    hello-client-allowed-7d95fcd5d9-t8fsk   |  1/1      Running   0          14m
    hello-client-blocked-6497db465d-ckbn8   |  1/1      Running   0          14m
    hello-server-7df58f7fb5-nvcvd           |  1/1      Running   0          14m

#### Test Completed Task

Click **Check my progress** to verify your performed task. If you have successfully deployed simple HTTP hello server, you will see an assessment score.

<ql-activity-tracking step="2">Installing the hello server</ql-activity-tracking>

## Confirming default access to the hello server

First, tail the "allowed" client:

    kubectl logs --tail 10 -f $(kubectl get pods -oname -l app=hello)

Press **Ctrl** + **c** to exit.

Second, tail the logs of the "blocked" client:

    kubectl logs --tail 10 -f $(kubectl get pods -oname -l app=not-hello)

Press **Ctrl** + **c** to exit.

You will notice that both pods are successfully able to connect to the `hello-server` service. This is because you have not yet defined a Network Policy to restrict access. In each of these windows you should see successful responses from the server.

    Hostname: hello-server-7df58f7fb5-nvcvd
    Hello, world!
    Version: 1.0.0
    Hostname: hello-server-7df58f7fb5-nvcvd
    Hello, world!
    Version: 1.0.0
    Hostname: hello-server-7df58f7fb5-nvcvd
    ...

## Restricting access with a Network Policy

Now you will block access to the `hello-server` pod from all pods that are not labeled with `app=hello`.

The policy definition you'll use is contained in `manifests/network-policy.yaml`

Apply the policy with the following command:

    kubectl apply -f ./manifests/network-policy.yaml

(Output)

    networkpolicy.networking.k8s.io/hello-server-allow-from-hello-client created

Tail the logs of the "blocked" client again:

    kubectl logs --tail 10 -f $(kubectl get pods -oname -l app=not-hello)

You'll now see that the output looks like this in the window tailing the "blocked" client:

    wget: download timed out
    wget: download timed out
    wget: download timed out
    wget: download timed out
    wget: download timed out
    wget: download timed out
    wget: download timed out
    wget: download timed out
    wget: download timed out
    ...

The network policy has now prevented communication to the `hello-server` from the unlabeled pod.

Press **Ctrl** + **c** to exit.

## Restricting namespaces with Network Policies

In the previous example, you defined a network policy that restricts connections based on pod labels. It is often useful to instead label entire namespaces, particularly when teams or applications are granted their own namespaces.

You'll now modify the network policy to only allow traffic from a designated namespace, then you'll move the `hello-allowed` pod into that new namespace.

First, delete the existing network policy:

    kubectl delete -f ./manifests/network-policy.yaml

(Output)

    networkpolicy.networking.k8s.io "hello-server-allow-from-hello-client" deleted

Create the namespaced version:

    kubectl create -f ./manifests/network-policy-namespaced.yaml

(Output)

    networkpolicy.networking.k8s.io/hello-server-allow-from-hello-client created

Now observe the logs of the `hello-allowed-client` pod in the default namespace:

    kubectl logs --tail 10 -f $(kubectl get pods -oname -l app=hello)

You will notice it is no longer able to connect to the `hello-server`.

Press **Ctrl** + **c** to exit.

Finally, deploy a second copy of the hello-clients app into the new namespace.

    kubectl -n hello-apps apply -f ./manifests/hello-app/hello-client.yaml

(Output)

    deployment.apps/hello-client-allowed created
    deployment.apps/hello-client-blocked created

#### Test Completed Task

Click **Check my progress** to verify your performed task. If you have successfully deployed a second copy of the hello-clients app into the new namespace, you will see an assessment score.

<ql-activity-tracking step="3">Deploy a second copy of the hello-clients app into the new namespace</ql-activity-tracking>

## Validation

Next, check the logs for the two new `hello-app` clients.

View the logs for the "hello"-labeled app in the app in the `hello-apps` namespace by running:

    kubectl logs --tail 10 -f -n hello-apps $(kubectl get pods -oname -l app=hello -n hello-apps)

(Output)

    Hostname: hello-server-6c6fd59cc9-7fvgp
    Hello, world!
    Version: 1.0.0
    Hostname: hello-server-6c6fd59cc9-7fvgp
    Hello, world!
    Version: 1.0.0
    Hostname: hello-server-6c6fd59cc9-7fvgp
    Hello, world!
    Version: 1.0.0
    Hostname: hello-server-6c6fd59cc9-7fvgp
    Hello, world!
    Version: 1.0.0
    Hostname: hello-server-6c6fd59cc9-7fvgp

Both clients are able to connect successfully because _as of Kubernetes 1.10.x NetworkPolicies do not support restricting access to pods within a given namespace_. You can whitelist by pod label, namespace label, or whitelist the union (i.e. OR) of both. But you cannot yet whitelist the intersection (i.e. AND) of pod labels and namespace labels.

Press **Ctrl** + **c** to exit.

## Teardown

Qwiklabs will take care of shutting down all the resources used for this lab, but here’s what you would need to do to clean up your own environment to save on cost and to be a good cloud citizen:

Log out of the bastion host:

    exit

Run the following to destroy the environment:

    make teardown

(Output)

    ...snip...
    google_compute_subnetwork.cluster-subnet: Still destroying... (ID: us-east1/kube-net-subnet, 20s elapsed)
    google_compute_subnetwork.cluster-subnet: Destruction complete after 25s
    google_compute_network.gke-network: Destroying... (ID: kube-net)
    google_compute_network.gke-network: Still destroying... (ID: kube-net, 10s elapsed)
    google_compute_network.gke-network: Still destroying... (ID: kube-net, 20s elapsed)
    google_compute_network.gke-network: Destruction complete after 26s

    Destroy complete! Resources: 5 destroyed.

## Troubleshooting in your own environment

### The install script fails with a `Permission denied` when running Terraform

The credentials that Terraform is using do not provide the necessary permissions to create resources in the selected projects. Ensure that the account listed in `gcloud config list` has necessary permissions to create resources. If it does, regenerate the application default credentials using `gcloud auth application-default login`.

### Invalid fingerprint error during Terraform operations

Terraform occasionally complains about an invalid fingerprint, when updating certain resources. If you see the error below, simply re-run the command. ![terraform fingerprint error](https://cdn.qwiklabs.com/MovqAAg0Chnh9QY%2BsrRn5WUzsqrVNSRw6sB2lF46U1w%3D)

## Congratulations

![gke_best_security_badge.png](https://cdn.qwiklabs.com/maNNnEGq9cLqRzxQkol4VrPlDzyruxMCXVMlMRHwRhM%3D)

### Finish Your Quest

This self-paced lab is part of the Qwiklabs [Google Kubernetes Engine Best Practices: Security](https://google.qwiklabs.com/quests/64) Quest. A Quest is a series of related labs that form a learning path. Completing this Quest earns you the badge above, to recognize your achievement. You can make your badge (or badges) public and link to them in your online resume or social media account. [Enroll in this Quest](https://google.qwiklabs.com/quests/64/enroll) and get immediate completion credit if you've taken this lab. [See other available Qwiklabs Quests](https://google.qwiklabs.com/catalog).

### Take your next lab

Continue your Quest with [Securing Applications in Kubernetes Engine - Three Examples](https://google.qwiklabs.com/catalog_lab/1737), or check out these suggestions:

*   [Google Kubernetes Engine Security: Binary Authorization](https://google.qwiklabs.com/catalog_lab/1718)

*   [Using Role-based Access Control in Kubernetes Engine](https://google.qwiklabs.com/catalog_lab/1720)

### Next steps / Learn more

*   [Terraform Google Provider](https://www.terraform.io/docs/providers/google/)

*   [Kubernetes Network Policies](https://kubernetes.io/docs/concepts/services-networking/network-policies/)

*   [Kubernetes Engine - Creating a Cluster Network Policy](https://cloud.google.com/kubernetes-engine/docs/how-to/network-policy)

*   [Kubernetes Engine - Network Policy Tutorial](https://cloud.google.com/kubernetes-engine/docs/tutorials/network-policy)

*   [Kubernetes Engine - Hardening your cluster's security](https://cloud.google.com/kubernetes-engine/docs/how-to/hardening-your-cluster)

##### Manual Last Updated: December 28, 2020

##### Lab Last Tested: December 28, 2020

Copyright 2020 Google LLC. This software is provided as-is, without warranty or representation for any use or purpose. Your use of it is subject to your agreement with Google

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

<div class="js-lab-content-outline lab-content__outline">[GSP480](#step1)[Overview](#step2)[Architecture](#step3)[Setup](#step4)[Lab setup](#step5)[Validation](#step6)[Installing the hello server](#step7)[Confirming default access to the hello server](#step8)[Restricting access with a Network Policy](#step9)[Restricting namespaces with Network Policies](#step10)[Validation](#step11)[Teardown](#step12)[Troubleshooting in your own environment](#step13)[Congratulations](#step14)</div>

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

In this lab you learn how to improve the security of your Kubernetes Engine by applying fine-grained restrictions to limit intra-cluster network communication.

This lab is included in these quests: [Google Kubernetes Engine Best Practices: Security](/quests/64), [Secure Workloads in Google Kubernetes Engine](/quests/142). If you complete this lab you'll receive credit for it when you enroll in one of these quests.

**Duration:** 0m setup · 60m access · 60m completion

<span>**Levels:** fundamental</span>

**Permalink:** [https://google.qwiklabs.com/catalog_lab/1764](https://google.qwiklabs.com/catalog_lab/1764)

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

<div class="controls"><input class="hidden" type="hidden" value="1764" name="lab_review[lab_id]" id="lab_review_lab_id"></div>

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

<div class="lab-access-modal"><input type="hidden" name="id" id="id" value="5572"> <input type="hidden" name="user_id" id="user_id" value="4061609"> <input type="hidden" name="launch_with_credits" id="launch_with_credits" value="0" class="js-launch-with-credits-input"> <input type="hidden" name="launch_with_subs" id="launch_with_subs" value="0" class="js-launch-with-subscription-input">

<div class="lab-access-modal__method">

This lab costs 5 Credits.

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