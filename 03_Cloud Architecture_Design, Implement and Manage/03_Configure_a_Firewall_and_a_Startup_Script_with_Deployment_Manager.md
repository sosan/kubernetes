# Configure a Firewall and a Startup Script with Deployment Manager

1 hour 30 minutes 7 Credits

## GSP302

![Google Cloud Self-Paced Labs](https://cdn.qwiklabs.com/GMOHykaqmlTHiqEeQXTySaMXYPHeIvaqa2qHEzw6Occ%3D)

## Overview

For this Challenge Lab you must complete a series of tasks within a limited time period. Instead of following step-by-step instructions, you'll be given a scenario and task - you figure out how to to complete it on your own! An automated scoring system (shown on this page) will provide feedback on whether you have completed your tasks correctly.

To score 100% you must complete all tasks within the time period!

When you take a Challenge Lab, you will not be taught Google Cloud concepts. You'll need to use your advanced Compute Engine skills to assess how to build the solution to the challenge presented. This lab is only recommended for students who have Compute Engine skills. Are you up for the challenge?

### **Topics tested**

*   Configure a deployment template to include a startup script
*   Configure a deployment template to add a firewall rule allowing http traffic
*   Configure a deployment template to add a networking tag to a compute instance
*   Deploy a configuration using Deployment Manager

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

## Challenge scenario

Your company is ready to launch a brand new product and you have been asked to develop a Deployment Manager template to deploy and configure the Google Cloud environment that is required to support this product. To start off, you've been given an existing basic deployment manager template that just deploys a single compute instance.

## Your challenge

Modify the Deployment Manager template to add a firewall rule to:

*   Allow inbound HTTP traffic to tagged instances.
*   Include a startup script for the compute instance that installs the Apache web server application.
*   Add the firewall tag to the compute instance that is created so that it can be accessed from the internet.

## Start your task

### **Download the baseline Deployment Manager template**

A basic Deployment Manager template that just deploys a virtual machine already exists. You can download and unpack the `.jinja`, `.yaml` and `.jinja.schema` files as well as the sample startup script to your Cloud Shell using the following commands:

    mkdir deployment_manager
    cd deployment_manager
    gsutil cp gs://spls/gsp302/* .

### **Tasks**

The key tasks that you need for the challenge are listed below. Good luck!

*   Modify the Deployment Manager template to create a tagged firewall rule that allows inbound HTTP traffic.
*   Modify the deployment manager template to add the startup script to the compute instance.
*   Modify the deployment manager template to add the firewall rule tag that allows inbound HTTP traffic to the instance.
*   Deploy the configuration.

### Testing the deployments

Test the deployment as many times as you need to, but remember that you must clean up test deployments by deleting the named deployment itself before retrying a deployment with a modified template.

### Installing your application

The startup script provided installs the Apache web server software. This serves as a placeholder that allows you to test whether your deployment has successfully completed all tasks.

### Test your server

If you can reach the "Welcome to Apache" default landing page on your compute instance's external IP address, you have successfully completed all the tasks.

## Troubleshooting

*   **Receiving a Connection Refused error:**

    *   Your VM instance might not be publicly accessible because the instance does not have the proper tag that allows Compute Engine to apply the appropriate firewall rules.
    *   Your project does not have the firewall rule that allows traffic to the external IP address of your instance.
    *   If you are trying to access the VM using an HTTPS address, or the incorrect IP address, you will not be able to connect. Check that your URL is `http://[EXTERNAL_IP]` and not `https://[EXTERNAL_IP]` or `http://[INTERNAL_IP]`
*   **Challenge Scoring is not updated:** Your lab score might not be updated even though you can reach the Apache home page on your compute instance from an external address. The lab checks that you have completed the configuration changes using Deployment Manager. You cannot just manually add the startup script, tags, and firewall rules.

## Congratulations!

You have completed the challenge lab.

![final-cloudarch-design.png](https://cdn.qwiklabs.com/N%2FZGiFUWrRCgyZiNS8uD8kjMi0pKdEy5sVo5%2FvycAOE%3D)

### Finish Your Quest

This self-paced lab is part of the Qwiklabs [Cloud Architecture: Design, Implement, and Manage](https://google.qwiklabs.com/quests/124) Quest. A Quest is a series of related labs that form a learning path. Completing this Quest earns you the badge above, to recognize your achievement. You can make your badge (or badges) public and link to them in your online resume or social media account. [Enroll in this Quest](https://google.qwiklabs.com/learning_paths/124/enroll?locale=en) and get immediate completion credit if you've taken this lab. [See other available Qwiklabs Quests](https://google.qwiklabs.com/catalog).

### Take Your Next Lab

Continue your Quest with [Configure Secure RDP Using a Windows Bastion Host](https://google.qwiklabs.com/catalog_lab/1080), or check out these suggestions:

*   [Build and Deploy a Docker Image to a Kubernetes Cluster](https://google.qwiklabs.com/catalog_lab/1081)

*   [Scale Out and Update a Containerized Application on a Kubernetes Cluster](https://google.qwiklabs.com/catalog_lab/1082)

### Next Steps / Learn More

Have you checked out the [Data Science on the Google Cloud Platform](https://google.qwiklabs.com/quests/43) Quest? Students are given the opportunity to practice all aspects of ingestion, preparation, processing, querying, exploring and visualizing data sets using Google Cloud tools and services. The exercises in the quest are taken from book **Data Science on the Google Cloud Platform** by Valliappa Lakshmanan, published by O'Reilly Media, Inc.

### Google Cloud Training & Certification

...helps you make the most of Google Cloud technologies. [Our classes](https://cloud.google.com/training/courses) include technical skills and best practices to help you get up to speed quickly and continue your learning journey. We offer fundamental to advanced level training, with on-demand, live, and virtual options to suit your busy schedule. [Certifications](https://cloud.google.com/certification/) help you validate and prove your skill and expertise in Google Cloud technologies.

##### Manual Last Updated May 27, 2020

Copyright 2020 Google LLC All rights reserved. Google and the Google logo are trademarks of Google LLC. All other company and product names may be trademarks of the respective companies with which they are associated.
