# Secure Workloads in Google Kubernetes Engine: Challenge Lab

<div class="lab-preamble__details subtitle-headline-1"><span>1 hour</span> <span>9 Credits</span>


## GSP335

![Google Cloud Self-Paced Labs](https://cdn.qwiklabs.com/GMOHykaqmlTHiqEeQXTySaMXYPHeIvaqa2qHEzw6Occ%3D)

## Overview

You must complete a series of tasks within the allocated time period. Instead of following step-by-step instructions, you'll be given a scenario and a set of tasks - you figure out how to complete it on your own! An automated scoring system (shown on this page) will provide feedback on whether you have completed your tasks correctly.

To score 100% you must complete all tasks within the time period!

When you take a Challenge Lab, you will not be taught Google Cloud concepts. To build the solution to the challenge presented, use skills learned from the labs in the quest this challenge lab is part of. You will be expected to extend your learned skills; you will be expected to change default values, but new concepts will not be introduced.

This lab is only recommended for students who have completed the labs in the [Google Kubernetes Engine Best Practices: Security](https://google.qwiklabs.com/quests/64) Quest.

<ql-warningbox>Please make sure you review the labs in the Google Kubernetes Engine Best Practices: Security quest before starting this lab!</ql-warningbox>

Topics tested:

*   Enable TLS access using nginx-ingress and cert-manager.io
*   Secure traffic with a network policy
*   Enable Binary Authorization to ensure only approved images are deployed
*   Ensure that pods do not allow escalations to root

Are you up for the challenge?

### Setup

#### Before you click the Start Lab button

Read these instructions. Labs are timed and you cannot pause them. The timer, which starts when you click **Start Lab**, shows how long Google Cloud resources will be made available to you.

This Qwiklabs hands-on lab lets you do the lab activities yourself in a real cloud environment, not in a simulation or demo environment. It does so by giving you new, temporary credentials that you use to sign in and access Google Cloud for the duration of the lab.

#### What you need

To complete this lab, you need:

*   Access to a standard internet browser (Chrome browser recommended).
*   Time to complete the lab.

**Note:** If you already have your own personal Google Cloud account or project, do not use it for this lab.

**Note:** If you are using a Pixelbook, open an Incognito window to run this lab.

## Challenge scenario

As a newly trained Kubernetes engineer in Jooli Inc. you have been asked to demonstrate to the security team features to protect Kubernetes workloads.

You are expected to have the skills and knowledge for these tasks so don’t expect step-by-step guides.

Some Jooli Inc. standards you should follow:

*   Create all resources in the `us-east1` region and `us-east1-b` zone, unless otherwise directed.

*   Use the project VPCs.

*   Naming is normally _team-resource_, e.g. an instance could be named **kraken-webserver1**.

*   Allocate cost effective resource sizes. Projects are monitored and excessive resource use will result in the containing project's termination (and possibly yours), so beware. This is the guidance the monitoring team is willing to share: unless directed, use `n1-standard-1`.

### Your challenge

You need to secure a WordPress running on GKE that uses Cloud SQL as it's database.

During the training you spoke to the instructor who emailed you a list of tasks they thought you should do in your demonstration. This is the contents of the email.

### Task 0: Download the necessary files

1.  Open Cloud Shell and run the following to collect all the files made available to you:

    gsutil -m cp gs://cloud-training/gsp335/* .

### Task 1: Setup Cluster

To create a cluster for your demo, use the following values to ensure you have enough resources:

*   zone: us-central1-c
*   machine-type: standard-4
*   nodes: 2
*   Enable network policy

Click _Check my progress_ to verify the objective. <ql-activity-tracking step="1">Kubernetes cluster created</ql-activity-tracking>

<ql-warningbox>If you don't get a green check mark, click on the **Score** fly-out on the top right and click **Run Step** on the relevant step. A hint pop up opens to give you advice.</ql-warningbox>

### Task 2: Setup WordPress

If you have not setup WordPress using Cloud SQL before, here are some general steps for you here:

#### Setup the Cloud SQL database and database username and password

1.  Create a Cloud SQL instance in `us-central1`, using the default values.

2.  Create a Cloud SQL database for WordPress.

3.  Create a user using the following values:

*   Access from host %
*   Access to the Cloud SQL instance you created
*   Set a password (remember it, you will need later)

Click _Check my progress_ to verify the objective. <ql-activity-tracking step="2">Cloud SQL created</ql-activity-tracking>

<ql-warningbox>If you don't get a green check mark, click on the **Score** fly-out on the top right and click **Run Step** on the relevant step. A hint pop up opens to give you advice.</ql-warningbox>

#### Create a service account for access to your WordPress database from your WordPress instances

1.  Create the service account.

2.  Bind the service account to your project, give the role `roles/cloudsql.client`.

3.  Save the service account credentials in a `json` file.

4.  Save the service account json file as a secret in your Kubernetes cluster.

Use this as a template:

    kubectl create secret generic cloudsql-instance-credentials --from-file key.json

1.  Save the WordPress database username and password as secrets in your Kubernetes cluster.

Use this as a template:

    kubectl create secret generic cloudsql-db-credentials \
        --from-literal username=wordpress \
        --from-literal password='YOUR_PASSWORD'

Click _Check my progress_ to verify the objective. <ql-activity-tracking step="3">Service account created</ql-activity-tracking>

<ql-warningbox>If you don't get a green check mark, click on the **Score** fly-out on the top right and click **Run Step** on the relevant step. A hint pop up opens to give you advice.</ql-warningbox>

#### Create the WordPress deployment and service

1.  Use `kubectl create -f volume.yaml` to create a persistent volume for your WordPress application.

2.  Open `wordpress.yaml` and replace **INSTANCE_CONNECTION_NAME** with the instance name of your Cloud SQL database (the format is _project:region:databasename_).

3.  Use `kubectl apply -f wordpress.yaml` to create the WordPress environment.

Click _Check my progress_ to verify the objective. <ql-activity-tracking step="4">WordPress deployment running</ql-activity-tracking>

<ql-warningbox>If you don't get a green check mark, click on the **Score** fly-out on the top right and click **Run Step** on the relevant step. A hint pop up opens to give you advice.</ql-warningbox>

### Task 3: Setup Ingress with TLS

#### Set up nginx-ingress environment

1.  Make sure you add and update the stable repo for helm.

2.  Install stable nginx-ingress (call it nginx-ingress).

#### Set up your DNS record

1.  Check the service `nginx-ingress-controller` as an external IP address before continuing (`kubectl get svc`) to the next step.

2.  Run the command `. add_ip.sh`. You should see `record added`. You now have a DNS record added of the format YOUR_LAB_USERNAME.labdns.xyz which points to your ingress cluster (the script will output your DNS name).

#### Set up cert-manager.io

1.  Install cert-manager.io (make sure you use the right version).

2.  Edit `issuer.yaml` and set the email address to your email address.

3.  Use `kubectl apply -f issuer.yaml` to setup the letsencrypt prod issuer.

#### Configure nginx-ingress to use an encrypted certificate for your site

1.  Edit `ingress.yaml` and set the dns and domain name to your YOUR_LAB_USERNAME.labdns.xyz.

2.  Use `kubectl apply -f ingress.yaml`.

Click _Check my progress_ to verify the objective. <ql-activity-tracking step="5">WordPress is using HTTPS and a valid trusted certificate.</ql-activity-tracking>

<ql-warningbox>If you don't get a green check mark, click on the **Score** fly-out on the top right and click **Run Step** on the relevant step. A hint pop up opens to give you advice.</ql-warningbox>

### Task 4: Set up Network Policy

1.  Show your colleagues how to restrict the network traffic using the provided `network-policy.yaml` file. It's not complete; you will need to add the configuration to allow ingress traffic from the world into nginx-ingress, but you can solve that easily.

Click _Check my progress_ to verify the objective. <ql-activity-tracking step="6">Cluster as a Network Policy that restricts traffic but allows WordPress to work.</ql-activity-tracking>

<ql-warningbox>If you don't get a green check mark, click on the **Score** fly-out on the top right and click **Run Step** on the relevant step. A hint pop up opens to give you advice.</ql-warningbox>

### Task 5: Setup Binary Authorization

1.  Show your colleagues how to configure Binary Authorization to only allow the following images in your cluster:

*   `docker.io/library/wordpress:latest`
*   `us.gcr.io/k8s-artifacts-prod/ingress-nginx/*`
*   `gcr.io/cloudsql-docker/*`
*   `quay.io/jetstack/*`

Click _Check my progress_ to verify the objective. <ql-activity-tracking step="7">WordPress image is approved via Binary Authorization.</ql-activity-tracking>

<ql-warningbox>If you don't get a green check mark, click on the **Score** fly-out on the top right and click **Run Step** on the relevant step. A hint pop up opens to give you advice.</ql-warningbox>

### Task 6: Setup Pod Security Policy

1.  Some Pod Security Policy demo files have been provided for you to use in your demo. They will forbid the use of the root user and prevent the mounting of the host filesystem within a container.

Remember to check the restrictions using a service account that has the role `roles/container.developer` as this will simulate a typical developer.

Click _Check my progress_ to verify the objective. <ql-activity-tracking step="8">Cluster has the Pod Security Policy deployed and working.</ql-activity-tracking>

<ql-warningbox>If you don't get a green check mark, click on the **Score** fly-out on the top right and click **Run Step** on the relevant step. A hint pop up opens to give you advice.</ql-warningbox>

## Congratulations!

![Secure_Workloads_in_GKE_Skill_WBG.png](https://cdn.qwiklabs.com/6wDoWYzKsTpQSNlMAV451W5dCwzECbM7AYVv%2F4AV8e8%3D)

### Finish Your Quest

This self-paced lab is part of the [Secure Workloads in Google Kubernetes Engine](https://google.qwiklabs.com/quests/142) Quest. A Quest is a series of related labs that form a learning path. Completing this Quest earns you the badge above, to recognize your achievement. You can make your badge public and link to them in your online resume or social media account. [Enroll in this Quest](https://google.qwiklabs.com/quests/142/enroll) and get immediate completion credit if you've taken this lab. [See other available Qwiklabs Quests](https://google.qwiklabs.com/catalog).

This skill badge quest is part of Google Cloud’s [Hybrid and Multi-Cloud Cloud Architect](https://cloud.google.com/training/kubernetes-anthos-hybridcloud) learning path. If you have already completed the other skill badge quests in this learning path, search [the catalog](https://google.qwiklabs.com/catalog?keywords=skill%20badge) for 20+ other skill badge quests that you can enroll in.

### Take your next lab

This lab is also part of a series of labs called Challenge Labs. These labs are designed test your Google Cloud knowledge and skill. Search for "Challenge Lab" in the [lab catalog](https://google.qwiklabs.com/catalog) and challenge yourself!

### Google Cloud Training & Certification

...helps you make the most of Google Cloud technologies. [Our classes](https://cloud.google.com/training/courses) include technical skills and best practices to help you get up to speed quickly and continue your learning journey. We offer fundamental to advanced level training, with on-demand, live, and virtual options to suit your busy schedule. [Certifications](https://cloud.google.com/certification/) help you validate and prove your skill and expertise in Google Cloud technologies.

##### Manual Last Updated December 29, 2020

##### Lab Last Tested August 11, 2020

Copyright 2020 Google LLC All rights reserved. Google and the Google logo are trademarks of Google LLC. All other company and product names may be trademarks of the respective companies with which they are associated.

### Google Kubernetes Engine Security: Binary Authorization

This lab deploys a Kubernetes Engine Cluster with the Binary Authorization feature enabled; you'll learn how to whitelist approved container registries and the process of creating and running a signed container.
