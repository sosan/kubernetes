# Building an IoT Analytics Pipeline on Google Cloud

<div class="lab-preamble__details subtitle-headline-1"><span>1 hour 10 minutes</span> <span>7 Credits</span>

<div class="lab__rating">[](/focuses/605/reviews?parent=catalog)<a data-target="#lab-review-modal" data-toggle="modal">Rate Lab</a></div>

</div>

</div>

<div class="js-markdown-instructions markdown-lab-instructions" id="markdown-lab-instructions">

## GSP088

![Google Cloud Self-Paced Labs](https://cdn.qwiklabs.com/GMOHykaqmlTHiqEeQXTySaMXYPHeIvaqa2qHEzw6Occ%3D)

## Overview

The term **Internet of Things (IoT)** refers to the interconnection of physical devices with the global Internet. These devices are equipped with sensors and networking hardware, and each is globally identifiable. Taken together, these capabilities afford rich data about items in the physical world.

[Cloud IoT Core](https://cloud.google.com/iot-core) is a fully managed service that allows you to easily and securely connect, manage, and ingest data from millions of globally dispersed devices. The service connects IoT devices that use the standard `Message Queue Telemetry Transport (MQTT)` protocol to other Google Cloud data services.

Cloud IoT Core has two main components:

*   A **device manager** for registering devices with the service, so you can then monitor and configure them.
*   A **protocol bridge** that supports MQTT, which devices can use to connect to Google Cloud.

### What You'll Learn

In this lab, you will dive into Google Cloud's IoT services and complete the following tasks:

*   Connect and manage MQTT-based devices using Cloud IoT Core (using simulated devices)

*   Ingest a stream of information from Cloud IoT Core using Cloud Pub/Sub.

*   Process the IoT data using Cloud Dataflow.

*   Analyze the IoT data using BigQuery.

### Prerequisites

This is an **advanced level** lab and having a baseline level of knowledge on the following services will help you better understand the steps and commands that you'll be writing:

*   **BigQuery**
*   **Cloud Pub/Sub**
*   **Dataflow**
*   **IoT**

If you want to get up to speed in any of the above services, check out the following Qwiklabs:

*   [BigQuery: Qwik Start - Console](https://qwiklabs.com/catalog_lab/685)

*   [Weather Data in BigQuery](https://qwiklabs.com/catalog_lab/476)

*   [Dataflow: Qwik Start - Templates](https://qwiklabs.com/catalog_lab/934)

*   [Internet of Things: Qwik Start](https://qwiklabs.com/catalog_lab/1092)

## Setup and Requirements

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

### Check project permissions

Before you begin your work on Google Cloud, you need to ensure that your project has the correct permissions within Identity and Access Management (IAM).

1.  In the Google Cloud console, on the **Navigation menu** (![nav-menu.png](https://cdn.qwiklabs.com/ITyhQn00xPCVZIksELAC4wax%2BQdPdrH2DNGLazXJKgw%3D)), click **IAM & Admin** > **IAM**.

2.  Confirm that the default compute Service Account `{project-number}-compute@developer.gserviceaccount.com` is present and has the `editor` role assigned. The account prefix is the project number, which you can find on **Navigation menu** > **Home**.

![check-sa.png](https://cdn.qwiklabs.com/PaSJwLiQ6db41cptCuYECEHZtayQHGZpUJaR0fLIqTw%3D)

If the account is not present in IAM or does not have the `editor` role, follow the steps below to assign the required role.

*   In the Google Cloud console, on the **Navigation menu**, click **Home**.

*   Copy the project number (e.g. `729328892908`).

*   On the **Navigation menu**, click **IAM & Admin** > **IAM**.

*   At the top of the **IAM** page, click **Add**.

*   For **New members**, type:

    {project-number}-compute@developer.gserviceaccount.com

Replace `{project-number}` with your project number.

*   For **Role**, select **Project** (or Basic) > **Editor**. Click **Save**.

![add-sa.png](https://cdn.qwiklabs.com/TJdJBvKxMtTArw6djkzJinwSxZq%2BYa7fwXf54ROeHUM%3D)

Throughout this lab you'll be using your Project ID. You may want to copy and save it now for use later.

1.  In the Cloud Console, click **Navigation menu** > **APIs & Services**.

![Console_APIs.png](https://cdn.qwiklabs.com/Bk8sebENMKcXgE3kHURIvL%2B6W7yXbsjNMARW1mmnYSM%3D)

1.  Scroll down in the list of enabled APIs, and confirm that these APIs are enabled:

    *   **Cloud IoT API**
    *   **Cloud Pub/Sub API**
    *   **Dataflow API**
2.  If one or more API is not enabled, click the **ENABLE APIS AND SERVICES** button at the top. Search for the APIs by name and enable each API for your current project.

## Create a Cloud Pub/Sub topic

[Cloud Pub/Sub](https://cloud.google.com/pubsub) is an asynchronous global messaging service. By decoupling senders and receivers, it allows for secure and highly available communication between independently written applications. Cloud Pub/Sub delivers low-latency, durable messaging.

In Cloud Pub/Sub, publisher applications and subscriber applications connect with one another through the use of a shared string called a **topic**. A publisher application creates and sends messages to a topic. Subscriber applications create a subscription to a topic to receive messages from it.

In an IoT solution built with Cloud IoT Core, device telemetry data is forwarded to a Cloud Pub/Sub topic.

To define a new Cloud Pub/Sub topic:

1.  In the Cloud Console, go to **Navigation menu** > **Pub/Sub** > **Topics**.
2.  Click **+ CREATE TOPIC**. The **Create a topic** dialog shows you a partial URL path.

<ql-infobox>**Note:** If you see `qwiklabs-resources` as your project name, cancel the dialog and return to the Cloud Console. Use the menu to the right of the Google Cloud logo to select the correct project. Then return to this step.</ql-infobox>

1.  Add this string as your topic name:

    iotlab

1.  Click **CREATE TOPIC**.

### Test Completed Task

Click **Check my progress** to verify your performed task. If you have successfully created a Cloud Pub/Sub topic, you will see an assessment score.

<ql-activity-tracking step="1">Create a Cloud Pub/Sub Topic</ql-activity-tracking>

1.  In the list of topics, you will see a new topic whose partial URL ends in `iotlab`. Click the three-dot icon at the right edge of its row to open the context menu. Choose **View permissions**.

    ![view-permission.png](https://cdn.qwiklabs.com/SvC3j4rElD%2B2RjEnX4ElE%2FF39rp%2B0gLhYmQgFDRqLXA%3D)

2.  In the **Permissions** dialogue, click **ADD MEMBER** and copy the below member as **New members**:

    cloud-iot@system.gserviceaccount.com

1.  From the `Select a role` menu, give the new member the **Pub/Sub** > **Pub/Sub Publisher** role.

2.  Click **Save**.

### Test Completed Task

Click **Check my progress** to verify your performed task. If you have successfully added IAM binding policy to Pub/Sub topic, you will see an assessment score.

<ql-activity-tracking step="2">Add IAM binding policy to Pub/Sub topic</ql-activity-tracking>

## Create a BigQuery dataset

[BigQuery](https://cloud.google.com/bigquery) is a serverless data warehouse. Tables in BigQuery are organized into datasets. In this lab, messages published into Pub/Sub will be aggregated and stored in BigQuery.

To create a new BigQuery dataset:

1.  In the Cloud Console, go to **Navigation menu** > **BigQuery**.

2.  Click **Done**.

3.  Click on your Project ID from the left-hand menu:

![bq-project.png](https://cdn.qwiklabs.com/XWLjYo2wn3IowpCj2ADweRFAYiUJ9ixBblnZg4zj6yU%3D)

1.  On the right-hand side of the console underneath the query editor click **CREATE DATASET**.

2.  Name the dataset **iotlabdataset**, leave all the other fields the way they are, and click **Create dataset**.

3.  You should see your newly created dataset under your project:

    ![BQ_iot_dataset.png](https://cdn.qwiklabs.com/4hBJ4zjzwcbLlLU1Dp8t3BgU3JJa2HwLLj6ISV39b0s%3D)

### Test Completed Task

Click **Check my progress** to verify your performed task. If you have successfully created a BigQuery dataset, you will see an assessment score.

<ql-activity-tracking step="3">Create a BigQuery dataset</ql-activity-tracking>

1.  Click on your dataset, then on the right-hand side of the console click **+ CREATE TABLE**.

2.  Ensure that the source field is set to **Empty table**.

3.  In the **Destination** section's **Table name** field, enter `sensordata`.

4.  In the **Schema** section, click the **+ Add field** button and add the following fields:

    *   **timestamp**, set the field's **Type** to **TIMESTAMP**.
    *   **device**, set the field's **Type** to **STRING**.
    *   **temperature**, set the field's **Type** to **FLOAT**.
5.  Leave the other defaults unmodified. Click **Create table**.

![bq-table.png](https://cdn.qwiklabs.com/sUAeu9y60u1fLtFPv8jDc4MvbsRtA2ihVXwgzlnGuTI%3D)

### Test Completed Task

Click **Check my progress** to verify your performed task. If you have successfully created an empty table in BigQuery Dataset, you will see an assessment score.

<ql-activity-tracking step="4">Create an empty table in BigQuery Dataset</ql-activity-tracking>

## Create a cloud storage bucket

[Cloud Storage](https://cloud.google.com/storage) allows world-wide storage and retrieval of any amount of data at any time. You can use Cloud Storage for a range of scenarios including serving website content, storing data for archival and disaster recovery, or distributing large data objects to users via direct download.

For this lab Cloud Storage will provide working space for your Cloud Dataflow pipeline.

1.  In the Cloud Console, go to **Navigation menu** > **Storage**.

2.  Click **CREATE BUCKET**.

3.  For **Name**, use your Project ID then add `-bucket`.

4.  For **Default storage class**, click **Multi-regional** if it is not already selected.

5.  For **Location**, choose the selection closest to you.

6.  Click **CREATE**.

### Test Completed Task

Click **Check my progress** to verify your performed task. If you have successfully created a Cloud Storage bucket, you will see an assessment score.

<ql-activity-tracking step="5">Create a Cloud Storage Bucket</ql-activity-tracking>

## Set up a Cloud Dataflow Pipeline

[Cloud Dataflow](https://cloud.google.com/dataflow) is a serverless way to carry out data analysis. In this lab, you will set up a streaming data pipeline to read sensor data from Pub/Sub, compute the maximum temperature within a time window, and write this out to BigQuery.

1.  In the Cloud Console, go to **Navigation menu** > **Dataflow**.

2.  In the top menu bar, click **+ CREATE JOB FROM TEMPLATE**.

3.  In the job-creation dialog, for **Job name**, enter `iotlabflow`.

4.  For **Regional Endpoint**, choose the region as **us-central1**.

5.  For **Dataflow template**, choose **Pub/Sub Topic to BigQuery**. When you choose this template, the form updates to review new fields below.

6.  For **Input Pub/Sub topic**, enter `projects/` followed by your Project ID then add `/topics/iotlab`. The resulting string will look like this: `projects/qwiklabs-gcp-d2e509fed105b3ed/topics/iotlab`

7.  The **BigQuery output table** takes the form of Project ID:dataset.table (`:iotlabdataset.sensordata`). The resulting string will look like this: `qwiklabs-gcp-d2e509fed105b3ed:iotlabdataset.sensordata`

8.  For **Temporary location**, enter `gs://` followed by your Cloud Storage bucket name (should be your Project ID if you followed the instructions) then `/tmp/`. The resulting string will look like this: `gs://qwiklabs-gcp-d2e509fed105b3ed-bucket/tmp/`

9.  Click **SHOW OPTIONAL PARAMETERS**.

10.  For Max workers, enter **2**.

11.  For Machine type, enter **n1-standard-1**.

12.  Click **RUN JOB**.

A new streaming job is started. You can now see a visual representation of the data pipeline.

### Test Completed Task

Click **Check my progress** to verify your performed task. If you have successfully set up a Cloud Dataflow Pipeline, you will see an assessment score.

<ql-activity-tracking step="6">Set up a Cloud Dataflow Pipeline (region: us-central1)</ql-activity-tracking>

## Prepare your compute engine VM

In your project, a pre-provisioned VM instance named `iot-device-simulator` will let you run instances of a Python script that emulate an MQTT-connected IoT device. Before you emulate the devices, you will also use this VM instance to populate your Cloud IoT Core device registry.

To connect to the `iot-device-simulator` VM instance:

1.  In the Cloud Console, go to **Navigation menu** > **Compute Engine** > **VM Instances**. You'll see your VM instance listed as `iot-device-simulator`.

2.  Click the **SSH** drop-down arrow and select **Open in browser window**.

3.  In your SSH session, enter following commands to create a virtual environment.

    sudo pip install virtualenv
    virtualenv -p python3 venv
    source venv/bin/activate

1.  In your SSH session on the **iot-device-simulator** VM instance, enter this command to remove the default Cloud SDK installation. (In subsequent steps, you will install the latest version, including the beta component.)

    sudo apt-get remove google-cloud-sdk -y

1.  Now install the latest version of the Cloud SDK and accept all defaults:

    curl https://sdk.cloud.google.com | bash

1.  End your ssh session on the **iot-device-simulator** VM instance:

    exit

1.  Start another SSH session on the `iot-device-simulator` VM instance and execute the following command to activate the virtual environment.

    source venv/bin/activate

1.  Initialize the **gcloud** SDK.

    gcloud init

<ql-warningbox>

If you get the error message "Command not found," you might have forgotten to exit your previous SSH session and start a new one.

</ql-warningbox><ql-warningbox>

If you are asked whether to authenticate with an @developer.gserviceaccount.com account or to log in with a new account, choose **log in with a new account**.

</ql-warningbox><ql-warningbox>

If you are asked "Are you sure you want to authenticate with your personal account? Do you want to continue (Y/n)?" enter Y.

</ql-warningbox>

1.  Click on the URL shown to open a new browser window that displays a verification code.

2.  Copy the verification code and paste it in response to the "Enter verification code:" prompt, then press **Enter**.

3.  In response to "Pick cloud project to use", pick your lab project.

4.  Enter this command to make sure that the components of the SDK are up to date:

    gcloud components update

1.  Enter this command to install the beta components:

    gcloud components install beta

If you are asked "Do you want to continue (Y/n)?" enter "Y".

1.  Enter this command to update the system's information about Debian Linux package repositories:

    sudo apt-get update

1.  Enter this command to make sure that various required software packages are installed:

    sudo apt-get install python-pip openssl git -y

1.  Use **pip** to add needed Python components:

    pip install pyjwt paho-mqtt cryptography

1.  Enter this command to add data to analyze during this lab:

    git clone http://github.com/GoogleCloudPlatform/training-data-analyst

## Create a registry for IoT devices

To register devices, you must create a registry for the devices. The registry is a point of control for devices.

To create the registry:

1.  In your SSH session on the **iot-device-simulator** VM instance, run the following, adding your Project ID as the value for **PROJECT_ID**:

    export PROJECT_ID=

Your completed command will look like this: `export PROJECT_ID=qwiklabs-gcp-d2e509fed105b3ed`

1.  You must choose a region for your IoT registry. At this time, these regions are supported:

*   `us-central1`
*   `europe-west1`
*   `asia-east1`

Choose the region that is closest to you. To set an environment variable containing your preferred region, enter this command followed by the region name:

    export MY_REGION=

Your completed command will look like this: `export MY_REGION=us-central1`.

1.  Enter this command to create the device registry:

    gcloud beta iot registries create iotlab-registry \
       --project=$PROJECT_ID \
       --region=$MY_REGION \
       --event-notification-config=topic=projects/$PROJECT_ID/topics/iotlab

### Test Completed Task

Click **Check my progress** to verify your performed task. If you have successfully created a Registry for IoT Devices, you will see an assessment score.

<ql-activity-tracking step="7">Create a Registry for IoT Devices</ql-activity-tracking>

## Create a Cryptographic Keypair

To allow IoT devices to connect securely to Cloud IoT Core, you must create a cryptographic keypair.

In your SSH session on the **iot-device-simulator** VM instance, enter these commands to create the keypair in the appropriate directory:

    cd $HOME/training-data-analyst/quests/iotlab/
    openssl req -x509 -newkey rsa:2048 -keyout rsa_private.pem \
        -nodes -out rsa_cert.pem -subj "/CN=unused"

This `openssl` command creates an RSA cryptographic keypair and writes it to a file called `rsa_private.pem`.

## Add simulated devices to the registry

For a device to be able to connect to Cloud IoT Core, it must first be added to the registry.

1.  In your SSH session on the **iot-device-simulator** VM instance, enter this command to create a device called `temp-sensor-buenos-aires`:

    gcloud beta iot devices create temp-sensor-buenos-aires \
      --project=$PROJECT_ID \
      --region=$MY_REGION \
      --registry=iotlab-registry \
      --public-key path=rsa_cert.pem,type=rs256

1.  Enter this command to create a device called `temp-sensor-istanbul`:

    gcloud beta iot devices create temp-sensor-istanbul \
      --project=$PROJECT_ID \
      --region=$MY_REGION \
      --registry=iotlab-registry \
      --public-key path=rsa_cert.pem,type=rs256

### Test Completed Task

Click **Check my progress** to verify your performed task. If you have successfully added Simulated Devices to the Registry, you will see an assessment score.

<ql-activity-tracking step="8">Add Simulated Devices to the Registry</ql-activity-tracking>

## Run simulated devices

1.  In your SSH session on the **iot-device-simulator** VM instance, enter these commands to download the CA root certificates from pki.google.com to the appropriate directory:

    cd $HOME/training-data-analyst/quests/iotlab/
    wget https://pki.google.com/roots.pem

1.  Enter this command to run the first simulated device:

    python cloudiot_mqtt_example_json.py \
       --project_id=$PROJECT_ID \
       --cloud_region=$MY_REGION \
       --registry_id=iotlab-registry \
       --device_id=temp-sensor-buenos-aires \
       --private_key_file=rsa_private.pem \
       --message_type=event \
       --algorithm=RS256 > buenos-aires-log.txt 2>&1 &

It will continue to run in the background.

1.  Enter this command to run the second simulated device:

    python cloudiot_mqtt_example_json.py \
       --project_id=$PROJECT_ID \
       --cloud_region=$MY_REGION \
       --registry_id=iotlab-registry \
       --device_id=temp-sensor-istanbul \
       --private_key_file=rsa_private.pem \
       --message_type=event \
       --algorithm=RS256

Telemetry data will flow from the simulated devices through Cloud IoT Core to your Cloud Pub/Sub topic. In turn, your Dataflow job will read messages from your Pub/Sub topic and write their contents to your BigQuery table.

## Analyze the Sensor Data Using BigQuery

To analyze the data as it is streaming:

1.  In the Cloud Console, open the **Navigation menu** and select **BigQuery**.

2.  Enter the following query in the Query editor and click **RUN**:

    SELECT timestamp, device, temperature from iotlabdataset.sensordata
    ORDER BY timestamp DESC
    LIMIT 100

1.  You should receive a similar output:

![bq-results.png](https://cdn.qwiklabs.com/AqrtZkywR2zkkri3S6jn%2BC9yf5vWEk3XlEL6H8Ym8ec%3D)

1.  Browse the Results. What is the temperature trend at each of the locations?

## Test your Understanding

Below are multiple-choice questions to reinforce your understanding of this lab's concepts. Answer them to the best of your abilities.

<ql-true-false-probe answer="true" stem="When you create a device within a registry, you define the device as a Cloud IoT Core resource."></ql-true-false-probe>

## Congratulations!

In this lab, you used Cloud IoT Core and other services on Google Cloud to collect, process, and analyze IoT data.

![IOT-Badge-125.png](https://cdn.qwiklabs.com/4rk6ETLiTiIIuhzW5wHyOLxRLI%2FXvcy5mYm47F3C3O8%3D) ![Data_Engineering_badge_125.png](https://cdn.qwiklabs.com/XFebDQwepOTnzaxXtr01EzuRLa43wF9q3%2BaQtsE52GM%3D)

### Finish your Quest

This self-paced lab is part of the Qwiklabs [IoT in the Google Cloud](https://google.qwiklabs.com/quests/49) and [Data Engineering](https://google.qwiklabs.com/quests/25) Quests. A Quest is a series of related labs that form a learning path. Completing a Quest earns you a badge to recognize your achievement. You can make your badge (or badges) public and link to them in your online resume or social media account. Enroll in a Quest and get immediate completion credit if you've taken this lab. [See other available Qwiklabs Quests](http://google.qwiklabs.com/catalog).

### Take Your Next Lab

Continue your Quest with [ETL Processing on Google Cloud Using Dataflow and BigQuery](https://google.qwiklabs.com/catalog_lab/1341), or check out these suggestions:

*   [Predict Visitor Purchases with a Classification Model in BQML](https://google.qwiklabs.com/catalog_lab/1101)

*   [Predict Housing Prices with Tensorflow and AI Platform](https://google.qwiklabs.com/catalog_lab/1468)

#### Next steps

*   Read the [Getting Started guide for Cloud IoT Core](https://cloud.google.com/iot/docs/how-tos/getting-started).
*   For more information, see [Overview of Internet of Things](https://cloud.google.com/solutions/iot-overview)

### Google Cloud Training & Certification

...helps you make the most of Google Cloud technologies. [Our classes](https://cloud.google.com/training/courses) include technical skills and best practices to help you get up to speed quickly and continue your learning journey. We offer fundamental to advanced level training, with on-demand, live, and virtual options to suit your busy schedule. [Certifications](https://cloud.google.com/certification/) help you validate and prove your skill and expertise in Google Cloud technologies.

##### Manual Last Updated October 21, 2020

##### Lab Last Tested October 21, 2020

Copyright 2020 Google LLC All rights reserved. Google and the Google logo are trademarks of Google LLC. All other company and product names may be trademarks of the respective companies with which they are associated.

</div>

</div>

<div class="hidden js-end-lab-button-container lab-content__end-lab-button"><ql-lab-control-button class="js-end-lab-button" running=""></ql-lab-control-button></div>

<div class="lab-content__renderable-instructions">

<div class="lab-content__recommendation">

<section class="upcoming-cards">

## Continue questing

<div class="content-card-grid">

<div class="card-content-wrapper js-content-card" data-id="1062" data-level="Advanced" data-name="Creating a Data Transformation Pipeline with Cloud Dataprep" data-type="Lab">[

<div class="card__body">

<div class="overline card--content__type">Hands-On Lab</div>

### Creating a Data Transformation Pipeline with Cloud Dataprep

Cloud Dataprep by Trifacta is an intelligent data service for visually exploring, cleaning, and preparing structured and unstructured data for analysis. In this lab you will explore the Cloud Dataprep UI to build a data transformation pipeline.

</div>

<div class="card__footer">

<div class="card__footer__left"><span>Advanced</span></div>

</div>

](/focuses/4415?parent=catalog)</div>

</div>

</section>

</div>

</div>

</ql-drawer-content><ql-drawer end="" id="outline-drawer" open="" slot="drawer" width="320">

<div class="js-lab-content-outline lab-content__outline">[GSP088](#step1)[Overview](#step2)[Setup and Requirements](#step3)[Create a Cloud Pub/Sub topic](#step4)[Create a BigQuery dataset](#step5)[Create a cloud storage bucket](#step6)[Set up a Cloud Dataflow Pipeline](#step7)[Prepare your compute engine VM](#step8)[Create a registry for IoT devices](#step9)[Create a Cryptographic Keypair](#step10)[Add simulated devices to the registry](#step11)[Run simulated devices](#step12)[Analyze the Sensor Data Using BigQuery](#step13)[Test your Understanding](#step14)[Congratulations!](#step15)</div>

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

This lab shows you how to connect and manage devices using Cloud IoT Core; ingest the stream of information using Cloud Pub/Sub; process the IoT data using Cloud Dataflow; use BigQuery to analyze the IoT data. Watch this short video, [Easily Build an IoT Analytics Pipeline](https://youtu.be/r_4BVf2x2Yo).

This lab is included in these quests: [Data Engineering](/quests/25), [Engineer Data in Google Cloud](/quests/132), [IoT in the Google Cloud](/quests/49). If you complete this lab you'll receive credit for it when you enroll in one of these quests.

**Duration:** 0m setup · 70m access · 50m completion

<span>**Levels:** advanced</span>

**Permalink:** [https://google.qwiklabs.com/catalog_lab/694](https://google.qwiklabs.com/catalog_lab/694)

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

<div class="controls"><input class="hidden" type="hidden" value="694" name="lab_review[lab_id]" id="lab_review_lab_id"></div>

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

<div class="lab-access-modal"><input type="hidden" name="id" id="id" value="605"> <input type="hidden" name="user_id" id="user_id" value="4061609"> <input type="hidden" name="launch_with_credits" id="launch_with_credits" value="0" class="js-launch-with-credits-input"> <input type="hidden" name="launch_with_subs" id="launch_with_subs" value="0" class="js-launch-with-subscription-input">

<div class="lab-access-modal__method">

This lab costs 7 Credits.

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