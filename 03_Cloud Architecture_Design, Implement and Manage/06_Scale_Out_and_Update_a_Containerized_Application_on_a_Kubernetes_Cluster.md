# Scale Out and Update a Containerized Application on a Kubernetes Cluster

<div class="lab-preamble__details subtitle-headline-1"><span>1 hour</span> <span>7 Credits</span>

<div class="lab__rating">[](/focuses/1739/reviews?parent=catalog)<a data-target="#lab-review-modal" data-toggle="modal">Rate Lab</a></div>

</div>

</div>

<div class="js-markdown-instructions markdown-lab-instructions" id="markdown-lab-instructions">

## GSP305

![Google Cloud Self-Paced Labs](https://cdn.qwiklabs.com/GMOHykaqmlTHiqEeQXTySaMXYPHeIvaqa2qHEzw6Occ%3D)

## Overview

For this Challenge Lab you must complete a series of tasks within a limited time period. Instead of following step-by-step instructions, you'll be given a scenario and task - you figure out how to to complete it on your own! An automated scoring system (shown on this page) will provide feedback on whether you have completed your tasks correctly.

To score 100% you must complete all tasks within the time period!

When you take a Challenge Lab, you will not be taught Google Cloud concepts. You'll need to use your advanced Compute Engine skills to assess how to build the solution to the challenge presented. This lab is only recommended for students who have Compute Engine skills. Are you up for the challenge?

**Topics tested**

*   Update a docker application and push a new version to a container repository.

*   Deploy the updated application version to a Kubernetes cluster.

*   Scale out the application so that it is running 2 replicas.

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

## Challenge scenario

You are taking over ownership of a test environment and have been given an updated version of a containerized test application to deploy. Your systems' architecture team has started adopting a containerized microservice architecture. You are responsible for managing the containerized test web applications. You will first deploy the initial version of a test application, called `echo-app` to a Kubernetes cluster called `echo-cluster` in a deployment called `echo-web`.

Before you get started, open the navigation menu and select **Storage**. The last steps in the Deployment Manager script used to set up your environment creates a bucket.

Refresh the Storage browser until you see your bucket. You can move on once your Console resembles the following:

![buckets.png](https://cdn.qwiklabs.com/6LFiu9lfhzr7qtTo4e1BifM0q0cRiNDzEHnvYmfvrjc%3D)

Check to make sure your GKE cluster has been created before continuing. Open the navigation menu and select **Kuberntes Engine** > **Clusters**.

Continue when you see a green checkmark next to `echo-cluster`:

![cluster-complete.png](https://cdn.qwiklabs.com/QouWWaKBDJ2Dug%2B1QP3Zw4jqG5NTXpXmRhrfTXvdF08%3D)

To deploy your first version of the application, run the following commands in Cloud Shell to get up and running:

    gcloud container clusters get-credentials echo-cluster --zone=us-central1-a

    kubectl create deployment echo-web --image=gcr.io/qwiklabs-resources/echo-app:v1

    kubectl expose deployment echo-web --type=LoadBalancer --port 80 --target-port 8000

## Your challenge

You need to update the running `echo-app` application in the `echo-web` deployment from the v1 to the v2 code you have been provided. You must also scale out the application to 2 instances and confirm that they are all running.

### Build and deploy the updated application with a new tag

The updated sample application, including the Dockerfile and the application context files, are contained in an archive called `echo-web-v2.tar.gz`. The archive has been copied to a Cloud Storage bucket in your lab project called `gs://[PROJECT_ID]`. V2 of the application adds a version number to the output of the application.

### Push the image to the Container Registry

Your organization uses the Container Registry to host Docker images for deployments, and uses the `gcr.io` Container Registry hostname for all projects. You must push the updated image to the Container Registry before deploying it.

## Troubleshooting

**Receiving a 504, Gateway timeout error:** This might just indicate that the application hasn't quite initialized yet, but it could also be caused by a mismatch between the default port that is set in the Dockerfile (TCP port 8000) and:

*   The choice of application port you configured when deploying the application image, or

*   When you configured external access.

## Congratulations!

You have completed the challenge lab.

![final-cloudarch-design.png](https://cdn.qwiklabs.com/N%2FZGiFUWrRCgyZiNS8uD8kjMi0pKdEy5sVo5%2FvycAOE%3D)

### Finish Your Quest

This self-paced lab is part of the Qwiklabs [Cloud Architecture: Design, Implement, and Manage](https://google.qwiklabs.com/quests/124) Quest. A Quest is a series of related labs that form a learning path. Completing this Quest earns you the badge above, to recognize your achievement. You can make your badge (or badges) public and link to them in your online resume or social media account. [Enroll in this Quest](https://google.qwiklabs.com/learning_paths/124/enroll?locale=en) and get immediate completion credit if you've taken this lab. [See other available Qwiklabs Quests](https://google.qwiklabs.com/catalog).

### Take Your Next Lab

Continue your Quest with [Migrate a MySQL Database to Google Cloud SQL](https://google.qwiklabs.com/catalog_lab/1083), or check out these suggestions:

*   [Deploy a Compute Instance with a Remote Startup Script(https://google.qwiklabs.com/catalog_lab/1078)

*   [Configure a Firewall and a Startup Script with Deployment Manager](https://google.qwiklabs.com/catalog_lab/1079)

### Next Steps / Learn More

Have you checked out the [Data Science on the Google Cloud Platform](https://google.qwiklabs.com/quests/43) Quest? Students are given the opportunity to practice all aspects of ingestion, preparation, processing, querying, exploring and visualizing data sets using Google Cloud tools and services. The exercises in the quest are taken from book **Data Science on the Google Cloud Platform** by Valliappa Lakshmanan, published by O'Reilly Media, Inc.

### Google Cloud Training & Certification

...helps you make the most of Google Cloud technologies. [Our classes](https://cloud.google.com/training/courses) include technical skills and best practices to help you get up to speed quickly and continue your learning journey. We offer fundamental to advanced level training, with on-demand, live, and virtual options to suit your busy schedule. [Certifications](https://cloud.google.com/certification/) help you validate and prove your skill and expertise in Google Cloud technologies.

##### Manual Last Updated April 24, 2020

Copyright 2020 Google LLC All rights reserved. Google and the Google logo are trademarks of Google LLC. All other company and product names may be trademarks of the respective companies with which they are associated.
