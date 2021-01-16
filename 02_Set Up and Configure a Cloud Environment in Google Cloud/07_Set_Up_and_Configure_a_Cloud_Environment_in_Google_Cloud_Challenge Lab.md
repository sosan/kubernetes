<div class="lab-preamble">

# Set Up and Configure a Cloud Environment in Google Cloud: Challenge Lab

<div class="lab-preamble__details subtitle-headline-1"><span>1 hour</span> <span>9 Credits</span>

</div>

</div>

<div class="js-markdown-instructions markdown-lab-instructions" id="markdown-lab-instructions">

## GSP321

![Google Cloud Self-Paced Labs](https://cdn.qwiklabs.com/GMOHykaqmlTHiqEeQXTySaMXYPHeIvaqa2qHEzw6Occ%3D)

## Overview

You must complete a series of tasks within the allocated time period. Instead of following step-by-step instructions, you'll be given a scenario and a set of tasks - you figure out how to complete it on your own! An automated scoring system (shown on this page) will provide feedback on whether you have completed your tasks correctly.

To score 100% you must complete all tasks within the time period!

When you take a Challenge Lab, you will not be taught Google Cloud concepts. To build the solution to the challenge presented, use skills learned from the labs in the quest this challenge lab is part of. You will be expected to extend your learned skills; you will be expected to change default values, but new concepts will not be introduced.

This lab is only recommended for students who have completed the labs in the [Set up and Configure a Cloud Environment in Google Cloud](https://google.qwiklabs.com/quests/119) Quest.

<ql-warningbox>Please make sure you review the labs in the Cloud Engineering quest before starting this lab!</ql-warningbox>

Topics tested:

*   Creating and using VPCs and subnets
*   Configuring and launching a Deployment Manager configuration
*   Creating a Kubernetes cluster
*   Configuring and launching a Kubernetes deployment and service
*   Setting up stackdriver monitoring
*   Configuring an IAM role for an account

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

As a cloud engineer in Jooli Inc. and recently trained with Google Cloud and Kubernetes you have been asked to help a new team (Griffin) set up their environment. The team has asked for your help and has done some work, but needs you to complete the work.

You are expected to have the skills and knowledge for these tasks so don’t expect step-by-step guides.

You need to complete the following tasks:

*   Create a development VPC with three subnets manually
*   Create a production VPC with three subnets using a provided Deployment Manager configuration
*   Create a bastion that is connected to both VPCs
*   Create a development Cloud SQL Instance and connect and prepare the WordPress environment
*   Create a Kubernetes cluster in the development VPC for WordPress
*   Prepare the Kubernetes cluster for the WordPress environment
*   Create a WordPress deployment using the supplied configuration
*   Enable monitoring of the cluster via stackdriver
*   Provide access for an additional engineer

Some Jooli Inc. standards you should follow:

*   Create all resources in the `us-east1` region and `us-east1-b` zone, unless otherwise directed.
*   Use the project VPCs.
*   Naming is normally _team-resource_, e.g. an instance could be named **kraken-webserver1**.
*   Allocate cost effective resource sizes. Projects are monitored and excessive resource use will result in the containing project's termination (and possibly yours), so beware. This is the guidance the monitoring team is willing to share: unless directed, use `n1-standard-1`.

### Your challenge

You need to help the team with some of their initial work on a new project. They plan to use WordPress and need you to set up a development environment. Some of the work was already done for you, but other parts require your expert skills.

As soon as you sit down at your desk and open your new laptop you receive the following request to complete these tasks. Good luck!

#### Environment

![gsp321.png](https://cdn.qwiklabs.com/UE5MydlafU0QvN7zdaOLo%2BVxvETvmuPJh%2B9kZxQnOzE%3D)

### Task 1: Create development VPC manually

Create a VPC called `griffin-dev-vpc` with the following subnets only:

*   `griffin-dev-wp`
    *   IP address block: `192.168.16.0/20`
*   `griffin-dev-mgmt`
    *   IP address block: `192.168.32.0/20`

Click _Check my progress_ to verify the objective. <ql-activity-tracking step="1">Create development VPC manually</ql-activity-tracking>

If you don't get a green check mark, click on the **Score** fly-out on the top right and click **Run Step** on the relevant step. A hint pop up opens to give you advice.</ql-warningbox>

### Task 2: Create production VPC using Deployment Manager

Use Cloud Shell and copy all files from `gs://cloud-training/gsp321/dm`.

Check the Deployment Manager configuration and make any adjustments you need, then use the template to create the production VPC with the 2 subnets.

Click _Check my progress_ to verify the objective. <ql-activity-tracking step="2">Create production VPC using Deployment Manager</ql-activity-tracking>

<ql-warningbox>If you don't get a green check mark, click on the **Score** fly-out on the top right and click **Run Step** on the relevant step. A hint pop up opens to give you advice.</ql-warningbox>

### Task 3: Create bastion host

Create a bastion host with two network interfaces, one connected to `griffin-dev-mgmt` and the other connected to `griffin-prod-mgmt`. Make sure you can SSH to the host.

Click _Check my progress_ to verify the objective. <ql-activity-tracking step="3">Create bastion host</ql-activity-tracking>

<ql-warningbox>If you don't get a green check mark, click on the **Score** fly-out on the top right and click **Run Step** on the relevant step. A hint pop up opens to give you advice.</ql-warningbox>

### Task 4: Create and configure Cloud SQL Instance

Create a **MySQL Cloud SQL Instance** called `griffin-dev-db` in `us-east1`. Connect to the instance and run the following SQL commands to prepare the **WordPress** environment:

    CREATE DATABASE wordpress;
    GRANT ALL PRIVILEGES ON wordpress.* TO "wp_user"@"%" IDENTIFIED BY "stormwind_rules";
    FLUSH PRIVILEGES;

These SQL statements create the worpdress database and create a user with access to the wordpress dataase.

You will use the username and password in task 6.

Click _Check my progress_ to verify the objective. <ql-activity-tracking step="4">Create and configure Cloud SQL Instance</ql-activity-tracking>

<ql-warningbox>If you don't get a green check mark, click on the **Score** fly-out on the top right and click **Run Step** on the relevant step. A hint pop up opens to give you advice.</ql-warningbox>

### Task 5: Create Kubernetes cluster

Create a 2 node cluster (n1-standard-4) called `griffin-dev`, in the `griffin-dev-wp` subnet, and in zone `us-east1-b`.

Click _Check my progress_ to verify the objective. <ql-activity-tracking step="5">Create Kubernetes cluster</ql-activity-tracking>

<ql-warningbox>If you don't get a green check mark, click on the **Score** fly-out on the top right and click **Run Step** on the relevant step. A hint pop up opens to give you advice.</ql-warningbox>

### Task 6: Prepare the Kubernetes cluster

Use Cloud Shell and copy all files from `gs://cloud-training/gsp321/wp-k8s`.

The **WordPress** server needs to access the MySQL database using the _username_ and _password_ you created in task 4\. You do this by setting the values as secrets. **WordPress** also needs to store its working files outside the container, so you need to create a volume.

Add the following secrets and volume to the cluster using `wp-env.yaml`. Make sure you configure the _username_ to `wp_user` and _password_ to `stormwind_rules` before creating the configuration.

You also need to provide a key for a service account that was already set up. This service account provides access to the database for a sidecar container. Use the command below to create the key, and then add the key to the Kubernetes environment.

    gcloud iam service-accounts keys create key.json \
        --iam-account=cloud-sql-proxy@$GOOGLE_CLOUD_PROJECT.iam.gserviceaccount.com && \
    kubectl create secret generic cloudsql-instance-credentials \
        --from-file key.json

Click _Check my progress_ to verify the objective. <ql-activity-tracking step="6">Prepare the Kubernetes cluster</ql-activity-tracking>

<ql-warningbox>If you don't get a green check mark, click on the **Score** fly-out on the top right and click **Run Step** on the relevant step. A hint pop up opens to give you advice.</ql-warningbox>

### Task 7: Create a WordPress deployment

Now you have provisioned the MySQL database, and set up the secrets and volume, you can create the deployment using `wp-deployment.yaml`. Before you create the deployment you need to edit `wp-deployment.yaml` and replace **YOUR_SQL_INSTANCE** with griffin-dev-db's **Instance connection name**. Get the **Instance connection name** from your Cloud SQL instance.

After you create your WordPress deployment, create the service with `wp-service.yaml`.

Once the Load Balancer is created, you can visit the site and ensure you see the **WordPress** site installer. At this point the dev team will take over and complete the install and you move on to the next task.

![wp-installer](https://cdn.qwiklabs.com/u9QONUelkiErVu8MR%2BeuhxVDI0QdUxWqK%2BmlEdVgpao%3D)

Click _Check my progress_ to verify the objective. <ql-activity-tracking step="7">Create a WordPress deployment</ql-activity-tracking>

<ql-warningbox>If you don't get a green check mark, click on the **Score** fly-out on the top right and click **Run Step** on the relevant step. A hint pop up opens to give you advice.</ql-warningbox>

### Task 8: Enable monitoring

Create an uptime check for your WordPress development site.

Click _Check my progress_ to verify the objective. <ql-activity-tracking step="8">Enable monitoring</ql-activity-tracking>

<ql-warningbox>If you don't get a green check mark, click on the **Score** fly-out on the top right and click **Run Step** on the relevant step. A hint pop up opens to give you advice.</ql-warningbox>

### Task 9: Provide access for an additional engineer

You have an additional engineer starting and you want to ensure they have access to the project, so please go ahead and grant them the editor role to the project.

The second user account for the lab represents the additional engineer.

Click _Check my progress_ to verify the objective. <ql-activity-tracking step="9">Provide access for an additional engineer</ql-activity-tracking>

<ql-warningbox>If you don't get a green check mark, click on the **Score** fly-out on the top right and click **Run Step** on the relevant step. A hint pop up opens to give you advice.</ql-warningbox>

## Congratulations!

![Set_Up_and_Config_Cloud_Env_Skill_WBG.png](https://cdn.qwiklabs.com/yuwz8Q8HZu8wplHB0wBms7NV1drJGU8lcnFMXvRnPrQ%3D)

### Finish Your Quest

This self-paced lab is part of the Qwiklabs [Set Up and Configure a Cloud Environment in Google Cloud](https://google.qwiklabs.com/quests/119) Quest. A Quest is a series of related labs that form a learning path. Completing this Quest earns you the badge above, to recognize your achievement. You can make your badge public and link to them in your online resume or social media account. [Enroll in this Quest](https://google.qwiklabs.com/quests/119/enroll) and get immediate completion credit if you've taken this lab. [See other available Qwiklabs Quests](https://google.qwiklabs.com/catalog).

This quest is part of the Associate Cloud Engineer learning path and is a great resource for understanding topics that will appear on the [Google Cloud Associate Cloud Engineer certification exam](https://cloud.google.com/certification/cloud-engineer). If you are interested in pursuing the certification, it is recommended that you review the other preparation resources listed on the exam page.

### Take your next lab

This lab is also part of a series of labs called Challenge Labs. These labs are designed test your Google Cloud knowledge and skill. Search for "Challenge Lab" in the [lab catalog](https://google.qwiklabs.com/catalog) and challenge yourself!

### Google Cloud Training & Certification

...helps you make the most of Google Cloud technologies. [Our classes](https://cloud.google.com/training/courses) include technical skills and best practices to help you get up to speed quickly and continue your learning journey. We offer fundamental to advanced level training, with on-demand, live, and virtual options to suit your busy schedule. [Certifications](https://cloud.google.com/certification/) help you validate and prove your skill and expertise in Google Cloud technologies.

##### Manual Last Updated December 28, 2020

##### Lab Last Tested April 06, 2020

Copyright 2020 Google LLC All rights reserved. Google and the Google logo are trademarks of Google LLC. All other company and product names may be trademarks of the respective companies with which they are associated.

</div>

</div>

<div class="hidden js-end-lab-button-container lab-content__end-lab-button"><ql-lab-control-button class="js-end-lab-button" running=""></ql-lab-control-button></div>

<div class="lab-content__renderable-instructions">

<div class="lab-content__recommendation">

<section class="upcoming-cards">

## Continue questing

<div class="content-card-grid">

<div class="card-content-wrapper js-content-card" data-id="1282" data-level="Introductory" data-name="Introduction to SQL for BigQuery and Cloud SQL" data-type="Lab">[

<div class="card__body">

<div class="overline card--content__type">Hands-On Lab</div>

### Introduction to SQL for BigQuery and Cloud SQL

In this lab you will learn fundamental SQL clauses and will get hands on practice running structured queries on BigQuery and Cloud SQL.

