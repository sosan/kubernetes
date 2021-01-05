# Deployment Manager - Full Production

<div class="lab-preamble__details subtitle-headline-1"><span>1 hour 30 minutes</span> <span>9 Credits</span>

<div class="lab__rating">[](/focuses/15839/reviews?parent=catalog)<a data-target="#lab-review-modal" data-toggle="modal">Rate Lab</a></div>

</div>

</div>

<div class="js-markdown-instructions markdown-lab-instructions" id="markdown-lab-instructions">

## GSP060

![Google Cloud Self-Paced Labs](https://cdn.qwiklabs.com/GMOHykaqmlTHiqEeQXTySaMXYPHeIvaqa2qHEzw6Occ%3D)

## Overview

In this lab, you launch a service using an infrastructure orchestration tool called Deployment Manager and monitor the service using Cloud Monitoring. In Cloud Monitoring, you set up basic black box monitoring with a Cloud Monitoring dashboard and establish an Uptime Check (alert notification) to trigger incident response.

More specifically, you:

1.  Install and configure an advanced deployment using Deployment Manager sample templates.
2.  Enable Cloud Monitoring.
3.  Configure Cloud Monitoring Uptime Checks and notifications.
4.  Configure a Cloud Monitoring dashboard with two charts, one showing CPU usage and the other ingress traffic.
5.  Perform a load test and simulate a service outage.

Cloud Monitoring provides visibility into the performance, uptime, and overall health of cloud-powered applications.It collects metrics, events, and metadata from Google Cloud, Amazon Web Services, hosted uptime probes, application instrumentation, and a variety of common application components including Cassandra, Nginx, Apache Web Server, Elasticsearch, and many others. Cloud Monitoring ingests that data and generates insights via dashboards, charts, and alerts. Cloud Monitoring alerting helps you collaborate by integrating with Slack, PagerDuty, HipChat, Campfire, and more.

## Objectives

In this lab, you learn to:

*   Launch a cloud service from a collection of templates.

*   Configure basic black box monitoring of an application.

*   Create an uptime check to recognize a loss of service.

*   Establish an alerting policy to trigger incident response procedures.

*   Create and configure a dashboard with dynamically updated charts.

*   Test the monitoring and alerting regimen by applying a load to the service.

*   Test the monitoring and alerting regimen by simulating a service outage.

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

## Create a virtual environment

Execute the following command to download and update the packages list.

    sudo apt-get update

Python virtual environments are used to isolate package installation from the system.

    sudo apt-get install virtualenv

<ql-infobox>If prompted [Y/n], press `Y` and then `Enter`.</ql-infobox>

    virtualenv -p python3 venv

Activate the virtual environment.

    source venv/bin/activate

## Clone the Deployment Manager Sample Templates

Google provides a robust set of sample Deployment Manager templates that you can learn from and build upon.

To clone the repository, enter the following commands in Cloud Shell Command Line to create a directory to hold the Deployment Manager sample templates.

    mkdir ~/dmsamples

Go to that directory.

    cd ~/dmsamples

Clone the repository to the directory you just made.

    git clone https://github.com/GoogleCloudPlatform/deploymentmanager-samples.git

**Example Output:**

    remote: Counting objects: 1917, done.
    remote: Compressing objects: 100% (31/31), done.
    remote: Total 1917 (delta 11), reused 29 (delta 7), pack-reused 1874
    Receiving objects: 100% (1917/1917), 426.86 KiB | 0 bytes/s, done.
    Resolving deltas: 100% (1060/1060), done.

## Explore the Sample Files

We just downloaded a collection of sample templates to our directory, let's dive in and explore some of them.

### List the example templates

Run the following commands to navigate to and list the version2 examples:

    cd ~/dmsamples/deploymentmanager-samples/examples/v2

    ls

You should see something like this:

    access_context_manager  cloud_functions  common               folder_creation  iam_custom_role  internal_lb_haproxy  quick_start   ssl                 vm_with_disks
    access_control          cloudkms         container_igm        gke              igm-updater      metadata_from_file   regional_igm  step_by_step_guide  vpn_auto_subnet
    bigtable                cloud_router     container_vm         ha-service       image_based_igm  nodejs               saltstack     template_modules    waiter
    build_configuration     cloudsql         custom_machine_type  htcondor         instance_pool    nodejs_l7            single_vm     vlan_attachment
    cloudbuild              cloudsql_import  dataproc             iam              internal_lb      project_creation     sqladmin      vm_startup_script

Not all of the subdirectories are independent projects. For example, the directory named **common** contains templates that are used by several of the other projects. If you are studying independently later, use the `README` files as a guide.

The `nodejs` directory contains everything you need to build this lab. Note that there is a `nodejs` directory and a `nodejs_l7`directory. Use `nodejs`.

### List and examine the Nodejs deployment

Navigate to and list the version2 examples:

    cd nodejs/python

    ls

**Example Output:**

    frontend.py  frontend.py.schema  nodejs.py  nodejs.py.schema  nodejs.yaml

The main deployment manager configuration file is `nodejs.yaml`. It makes use of templates to generate infrastructure. The rest of the files are templates. Templates use variables defined in the `nodejs`.`yaml` configuration file to produce customized results.

![bb0ba9fa2750ba43.png](https://cdn.qwiklabs.com/40UqXM4XpfPlZ%2BOQWaVadL5q1cNctNoC2OHvAS3pxsw%3D)

### frontend.py

`frontend.py` includes `frontend.py.schema`, which creates an instance template based on `container_instance_template.py`. This template is used to create a managed instance group and an autoscaler. The template also creates a network load balancer that has a forwarding rule with a single public IP address. It also creates:

*   A target pool that refers to the managed instance group.

*   A health check attached to the target pool.

### nodejs.py

`nodejs.py` includes `nodejs.py.schema`, which brings the frontend and backend templates together.

*   Note that the frontend is `frontend.py`.

*   The backend is `/common/python/container_vm.py`.

*   This is a VM running a Docker container with MySQL, so it doesn't require a custom template.

### Other files

*   `/common/python/container_instance_template.py`

*   `/common/python/container_vm.py`

*   `/common/python/container_helper.py`

## Customize the Deployment

Now that you've downloaded and reviewed the `nodejs` Deployment Manager template, let's start customizing the deployment.

### Specify the zone

The `nodejs.yaml` file requires a zone, and you'll now add one to the file.

1.  Enter the following command to open the list of zones:

    gcloud compute zones list

Copy the name of a zone for the configuration file to use.

1.  Open `nodejs.yaml` in `nano` so you can edit the `zone` value:

    nano nodejs.yaml

The `nodejs.yaml` file contents:

    resources:
    - name: nodejs
      type: nodejs.py
      properties:
        zone: ZONE_TO_RUN

1.  Replace `ZONE_TO_RUN` with a zone name that's near you, then exit nano and save the file.

This example shows `ZONE_TO_RUN` set to `us-east1-d`

    resources:
    - name: nodejs
      type: nodejs.py
      properties:
        zone: us-east1-d

### Modify the maximum number of instances in the instance group

Edit the `nodejs.py` file.

1.  Enter this command to open `nodejs.py` in `nano`:

    nano nodejs.py

1.  Verify current scaling limit for frontend in `nodejs.py`

    {
         'name': frontend,
         'type': 'frontend.py',
         'properties': {
             'zone': context.properties['zone'],
             'dockerImage': 'gcr.io/deployment-manager-examples/nodejsservice',
             'port': application_port,
             # Define the variables that are exposed to container as env variables.
             'dockerEnv': {
                 'SEVEN_SERVICE_MYSQL_PORT': mysql_port,
                 'SEVEN_SERVICE_PROXY_HOST': '$(ref.' + backend
                                             + '.networkInterfaces[0].networkIP)'
             },
             # If left out will default to 1
             'size': 2,
             # If left out will default to 1
             'maxSize': 20
         }
     },

Current scaling limit is 20 (refer maxSize).

1.  Modify the `maxSize` and set it to 4:

    {
         'name': frontend,
         'type': 'frontend.py',
         'properties': {
             'zone': context.properties['zone'],
             'dockerImage': 'gcr.io/deployment-manager-examples/nodejsservice',
             'port': application_port,
             # Define the variables that are exposed to container as env variables.
             'dockerEnv': {
                 'SEVEN_SERVICE_MYSQL_PORT': mysql_port,
                 'SEVEN_SERVICE_PROXY_HOST': '$(ref.' + backend
                                             + '.networkInterfaces[0].networkIP)'
             },
             # If left out will default to 1
             'size': 2,
             # If left out will default to 1
             'maxSize': 4
         }
     },

Save the file and exit nano when you're done.

## Run the Application

Now you'll use Deployment Manager to deploy the application and make it operational. This builds the infrastructure, but it won't allow traffic. After Deployment Manager sets up the infrastructure, you can apply service labels.

### Deploy the application

Enter this command to name the application `advanced-configuration` and pass Deployment Manager the configuration file (`nodejs.yaml`).

    gcloud deployment-manager deployments create advanced-configuration --config nodejs.yaml

Output:

    The fingerprint of the deployment is PiYc6OsIFkWzQpCDklHvaA==
    Waiting for create [operation-1529913842103-56f72d31872d9-90070017-aec5761d]...done.
    Create operation operation-1529913842103-56f72d31872d9-90070017-aec5761d completed successfully.
    NAME                                   TYPE                             STATE      ERRORS  INTENT
    advanced-configuration-application-fw  compute.v1.firewall              COMPLETED  []
    advanced-configuration-backend         compute.v1.instance              COMPLETED  []
    advanced-configuration-frontend-as     compute.v1.autoscaler            COMPLETED  []
    advanced-configuration-frontend-hc     compute.v1.httpHealthCheck       COMPLETED  []
    advanced-configuration-frontend-igm    compute.v1.instanceGroupManager  COMPLETED  []
    advanced-configuration-frontend-it     compute.v1.instanceTemplate      COMPLETED  []
    advanced-configuration-frontend-lb     compute.v1.forwardingRule        COMPLETED  []
    advanced-configuration-frontend-tp     compute.v1.targetPool            COMPLETED  []

Click **Check my progress** below to check your lab progress.

<ql-activity-tracking step="1">Deploy the application</ql-activity-tracking>

Confirm the limit for the maximum number of instances:

1.  Go to **Compute Engine** > **Instance groups**.

    ![ComputeEngine_InstanceGroups.png](https://cdn.qwiklabs.com/VSe6QJCURyuBRtdwI1VmPbmtVO6lr%2BMBOVqSxBQ%2FR1o%3D)

2.  Click on **advanced-configuration-frontend-igm**. ![11990217fdad313e.png](https://cdn.qwiklabs.com/%2Bp3hZFvK7pLEz8wDzim4v9wdElPNp53neYOUXYA0OV0%3D)

3.  Click on the Details tab, then verify the maximum number of instances. ![max-num.png](https://cdn.qwiklabs.com/P%2BEn0rirUUkLCkoMCWp2QJtHtGjaVqydNJNAphyJNZI%3D)

You'll see it has been set to 4.

## Verify that the application is operational

The application takes a few minutes to start. You can view it in the Deployment Manager part of Cloud Console (**Navigation menu** > **Deployment Manager**), or you can see the instances in the Compute Engine part of Console (**Navigation menu** > **Compute Engine** > **VM**).

To verify that the application is running, open a browser to access `port 8080` and view the service. Since the IP address was established dynamically when the Deployment Manager implemented the global forwarding rule (specified in the template), you'll need to find that address to test the application.

### Find the global load balancer forwarding rule IP address

1.  Enter the following command in the Cloud Command Line to find your forwarding IP address.

    gcloud compute forwarding-rules list

**Example output:**

![369c66095dd87ce8.png](https://cdn.qwiklabs.com/iTFyoWP0LBGcSOSQLoSTyfXuGX7UQdYCQNJLoXu415Q%3D) Your forwarding IP address is the IP_ADDRESS listed in your output. Be sure to record it; you'll use it a few times in this lab.

1.  Open a new window in your browser. In the address field, enter the following URL, replacing `<your IP address>` with your forwarding IP address:

    http://<your IP address>:8080

You see a blank page, similar to below, with your own IP address:

![b0313bc2bc83fd85.png](https://cdn.qwiklabs.com/STpmChPe39xBddGAasxfBm%2BTsT5A425F6HG%2FMKQeBz0%3D)

It may take up to 10 minutes for the service to become operational. If you get an error, such as a 404, or a time out, wait about two minutes and try again.

1.  Now enter log information by entering a log message in your browser address line.

    http://<your forwarding IP address>:8080/?msg=<enter_a_message>

Replace `enter_a_message` with any kind of message.

These are log message examples - you have a different IP address:

    http://35.196.56.153:8080/?msg=my dog has spots

![dcbc4d400e96e650.png](https://cdn.qwiklabs.com/BaDXAVrIgQau%2F%2F1ym99jFfrWBd8xwAn7qZnOYGEgkLY%3D)

After you enter the log, the browser returns `added`.

![d816761e8572a251.png](https://cdn.qwiklabs.com/9iHvic1MnaKJUnI%2Bf1pwgTnIdhHnYEcWLiGexXu5IJA%3D)

1.  View the log by going to `http://<your IP address>:8080`. For example:

![7ce7917b8519c96d.png](https://cdn.qwiklabs.com/m7fbmMH6Z5acpT9%2F2ueDX5hOVMtdP7tlqAs0Axmj34I%3D)

Go ahead and create more logs and see them at `http://<your IP address>:8080`.

## Configure an uptime check and alert policy in Cloud Monitoring

### Create a Monitoring workspace

Now set up a Monitoring workspace that's tied to your Google Cloud Project. The following steps create a new account that has a free trial of Monitoring.

1.  In the Cloud Console, click **Navigation menu** > **Monitoring**.

2.  Wait for your workspace to be provisioned.

When the Monitoring dashboard opens, your workspace is ready.

![Overview.png](https://cdn.qwiklabs.com/FfS7W1mNXshxngUuea%2BFUBXoXedgDHt0YWk1aZKHiIk%3D)

### Configure an uptime check

The Cloud Monitoring window opens.

1.  In the Cloud Monitoring tab, click on **Uptime Checks**
2.  Click **Create Uptime Check**.
3.  In the **New uptime check** page Specify the following:

<table>

<tbody>

<tr>

<th>Property</th>

<th>Value</th>

</tr>

<tr>

<td>Title</td>

<td>Check1</td>

</tr>

<tr>

<td>Check Type</td>

<td>TCP</td>

</tr>

<tr>

<td>Resource Type</td>

<td>URL</td>

</tr>

<tr>

<td>Hostname</td>

<td>`<your forwarding address>` (you previously recorded)</td>

</tr>

<tr>

<td>Port</td>

<td>8080</td>

</tr>

<tr>

<td>Response content contains the text</td>

<td>`<leave blank>`</td>

</tr>

<tr>

<td>Check every</td>

<td>1 minute</td>

</tr>

</tbody>

</table>

1.  Click **Test** to test the check:

![7f60fe39e946f680.png](https://cdn.qwiklabs.com/eay9YeJ5k9vIdLIv62KrdE3aOgp3kCbtwGZE3zwAnZI%3D)

<aside class="special">

If the test fails, make sure that the service is still working. Also check to see that the firewall rule exists and is correct.

</aside>

1.  After the test succeeds, click **Save**.

After the Uptime Check is saved, Cloud Monitoring offers to create an alerting policy. Click **No, Thanks**.

![bfe4607c7305436d.png](https://cdn.qwiklabs.com/5P8EvAIM64KkuQrUlbLCndLuh85pVz%2BUrIX6MpBRhT0%3D)

## Configure an alerting policy and notification

1.  Click on **Alerting** > **Create Policy**.

2.  Name the policy **Test**.

3.  Click **Add Condition**.

4.  For **Find resource type and metric**, select **Compute Engine VM Instance**.

5.  For **Metrics**, select a metric you are interested in evaluating, such as **CPU usage** or **CPU utilization**.

6.  For **Condition**, select **is above**.

7.  Specify the threshold value and for how long the metric must cross this set value before the alert is triggered. For example, for **THRESHOLD**, type **20** and set **FOR** to **1 minute**.

8.  Click **Add**.

9.  In **Notifications**, click on **Add Notification Channel** select Notification Channel type as "Email" and add your personal email. Then Click **Add**.

    In a later step, you trigger an event that notify you via email.

10.  Click **Save**.

Click **Check my progress** below to check your lab progress.

<ql-activity-tracking step="2">Configure an Uptime Check and Alert Policy in Cloud Monitoring</ql-activity-tracking>

## Configure a Dashboard with a Couple of Useful Charts

### Configure a dashboard

1.  In the left menu, click **Dashboards** > **Create Dashboard**

2.  Name the New Dashboard **DMDash**. and then Click **Confirm**.

3.  Click **Add Chart** in the top right.

    ![ef98bfe7497869ed.png](https://cdn.qwiklabs.com/7mqY6WhK4LAtBMDffF1udM%2FkvqUHeJJPO1IL1dV3iok%3D)

4.  Set the following properties:

<table>

<tbody>

<tr>

<th>Property</th>

<th>Value</th>

</tr>

<tr>

<td>Title</td>

<td>Sample</td>

</tr>

<tr>

<td>Resource type</td>

<td>Compute Engine VM Instance</td>

</tr>

<tr>

<td>Metric Type</td>

<td>CPU Usage</td>

</tr>

</tbody>

</table>

1.  Click **Save**.
2.  Click on **Add Chart** to add another chart to the dashboard with the following properties:

<table>

<tbody>

<tr>

<th>Property</th>

<th>Value</th>

</tr>

<tr>

<td>Title</td>

<td>Sample 2</td>

</tr>

<tr>

<td>Resource type</td>

<td>Compute Engine VM Instance</td>

</tr>

<tr>

<td>Metric Type</td>

<td>Sent bytes</td>

</tr>

</tbody>

</table>

1.  Click **Save** DMDash should look like this:

    ![8b18deae69fb8132.png](https://cdn.qwiklabs.com/NO8Ga0SKlB8dId7XxLbrRrVrA3DDYObh8xGsfoPPdOM%3D)

## Create a test VM with ApacheBench

Now that you've configured monitoring for traffic in a specified region, see if it works. You'll install and use ApacheBench to apply 3 levels of load to the service and then view the Cloud Monitoring Dashboard you've set up.

### Create a VM

1.  In the Cloud Console, click **Compute Engine** > **VM instances**.

    ![cf68a77143674661.png](https://cdn.qwiklabs.com/vbSlqad%2BVrl178fWsGAaH%2BmPyCI%2Fdjo0hGtwvn793mg%3D)

2.  Click **Create instance**, use all the default settings in the **Create an instance** dialog.

    ![Create_Instance.png](https://cdn.qwiklabs.com/J%2BpSMLd%2FmiylLCHiefiff22NU0a9k1S1SuI8Du16%2F50%3D)

3.  Click **Create**.

### Install ApacheBench

1.  Still in the VM Instances window, click the instance-1 **SSH** button to SSH into the VM you just created.

2.  Enter the following commands to install the latest ApacheBench:

    sudo apt-get update

    sudo apt-get -y install apache2-utils

Click **Check my progress** below to check your lab progress.

<ql-activity-tracking step="3">Create a Test VM with ApacheBench.</ql-activity-tracking>

### Apply and monitor load

Now you'll use ApacheBench to apply load to the service. Watch the DMDash dashboard in Cloud Monitoring to monitor the CPU usage and the Network Inbound Traffic. You'll also be able to track the number of instances in Cloud Monitoring by mousing over the lines, or by viewing the instances in the Cloud Console (**Navigation menu** > **Compute Engine** > **VM**).

1.  In the SSH window, enter this command for ApacheBench to apply load to the service. Replace your forwarding IP for `Your_IP` (you previously recorded). Run the following command two or three times to create traffic.

    ab -n 1000 -c 100 http://<Your_IP>:8080/

**Sample output:**

    This is ApacheBench, Version 2.3 <$Revision: 1757674 $>
    Copyright 1996 Adam Twiss, Zeus Technology Ltd, http://www.zeustech.net/
    Licensed to The Apache Software Foundation, http://www.apache.org/

    Benchmarking 35.196.195.26 (be patient)
    Completed 100 requests
    Completed 200 requests
    Completed 300 requests
    Completed 400 requests
    Completed 500 requests
    Completed 600 requests
    Completed 700 requests
    Completed 800 requests
    Completed 900 requests
    Completed 1000 requests
    Finished 1000 requests

    Server Software:
    Server Hostname:        35.196.195.26
    Server Port:            8080

    Document Path:          /
    Document Length:        40 bytes

    Concurrency Level:      100
    Time taken for tests:   0.824 seconds
    Complete requests:      1000
    Failed requests:        0
    Total transferred:      140000 bytes
    HTML transferred:       40000 bytes
    Requests per second:    1213.57 [#/sec] (mean)
    Time per request:       82.402 [ms] (mean)
    Time per request:       0.824 [ms] (mean, across all concurrent requests)
    Transfer rate:          165.92 [Kbytes/sec] received

    Connection Times (ms)
                  min  mean[+/-sd] median   max
    Connect:       36   37   0.5     37      40
    Processing:    36   43   8.7     38      73
    Waiting:       36   43   8.7     38      73
    Total:         73   80   9.1     75     112

    Percentage of the requests served within a certain time (ms)
      50%     75
      66%     78
      75%     81
      80%     84
      90%     95
      95%    101
      98%    106
      99%    110
     100%    112 (longest request)

Wait a few minutes, then increase the load to 5000.

1.  Run this command two or three times to create traffic:

    ab -n 5000 -c 100 http://<Your_IP>:8080/

Wait a few minutes, then increase the load to 10000.

1.  Run this command two or three times to create traffic:

    ab -n 10000 -c 100 http://<Your_IP>:8080/

Now see what happens when you lower the CPU usage per instance.

1.  In the cloud console, click **Navigation menu** > **Compute Engine** > **Instance groups**.
2.  Click on the name of your instance group, click on the Details tab, and then click **Edit Group**.
3.  Change the CPU utilization. Click **CPU utilization**, change **Target CPU utilization** to **20**, and then click **Done**.
4.  Click **Save**.

<aside class="special">

The target CPU usage is the total value of the CPU usage for all VMs in the instance group. This controls when autoscaling occurs. In production you would usually have this set to at least 60%. For this exercise you set it temporarily to 20% to make it quicker to examine autoscaling.

</aside>

Run this command two or three times to create traffic:

    ab -n 10000 -c 100 http://<Your_IP>:8080/

**Expected behavior:** The load consumed more than 20% of the cumulative CPU in the group, triggering autoscaling. A new instance was started.

Now see what happens when you turn autoscaling off.

1.  Go to **Compute Engine** > **Instance groups**.
2.  Click the name of your instance group, then **Edit Group**.
3.  Change **Autoscaling mode** to **Don't autoscale**.
4.  Click **Save**.

Wait a few minutes, then run this command two or three times to create traffic:

    ab -n 10000 -c 100 http://<Your_IP>:8080/

**Expected behavior:** With autoscaling off, no new instances are created, cumulative CPU usage increases.

### Results

There is a lag time of around 5 minutes before you see changes in the Cloud Monitoring Dashboard.

## Simulate a Service Outage

To simulate an outage, remove the firewall.

1.  Click **Navigation menu** > **VPC Network** > **Firewall**.

2.  Check the box next to the firewall rule which contains **tcp:8080**, then click **Delete** at the top of the page. Click **Delete** again to confirm.

3.  You will receive a notification email in 15 to 30 minutes.

## Test your knowledge

Test your knowledge about Google cloud Platform by taking our quiz.

<ql-true-false-probe answer="true" stem="Cloud Monitoring collects metrics, events, and metadata from Google Cloud, Amazon Web Services, hosted uptime probes, application instrumentation, and a variety of common application components including Cassandra, Nginx, Apache Web Server, Elasticsearch, and many others. "></ql-true-false-probe>

## Congratulations!

![c19694752c475f16.png](https://cdn.qwiklabs.com/Dr5EtfhvT7F7Asg6VvWZwpwPe4cgxXVkXN7%2BEmmumqI%3D) ![Cloud_Architecture.png](https://cdn.qwiklabs.com/QNBqfg1D%2FzCw3KdiioKbnB1YWX9jpAs21holGhwny%2FY%3D) ![CloudEngineeringExaPrep_125.pmg](https://cdn.qwiklabs.com/jVIZEu1uWCkufNtCmqyElRifK5o%2FNRrtt5Vo9iZxHz8%3D)

## Finish your Quest

This self-paced lab is part of the [Deployment Manager](https://google.qwiklabs.com/quests/30), [Cloud Architecture](https://google.qwiklabs.com/quests/24), and [Cloud Engineering](https://google.qwiklabs.com/quests/66) Quests. A Quest is a series of related labs that form a learning path. Completing this Quest earns you the badge above, to recognize your achievement. You can make your badge public and link to them in your online resume or social media account. Enroll in a Quest and get immediate completion credit if you've taken this lab. [See other available Qwiklabs Quests](https://google.qwiklabs.com/catalog).

### Take your next lab

Continue your Quest with [Create a Custom Network Using Deployment Manager](https://google.qwiklabs.com/catalog_lab/1148), or check out these suggestions:

*   [Create a Network Load-Balanced Logbook Application](https://google.qwiklabs.com/catalog_lab/1149)

*   [Deployment Manager for Appserver](https://qwiklabs.com/catalog_lab/1121)

### Next steps / learn more

*   Check out how Cloud Monitoring can be used, see [Cloud Monitoring Documentation](https://cloud.google.com/stackdriver/docs/).
*   Learn more about Deployment Manager, see [Deployment Manager Fundamentals](https://cloud.google.com/deployment-manager/docs/fundamentals).
*   How about our tutorials? There are [several step-by-step tutorials](https://cloud.google.com/deployment-manager/docs/tutorials) in the documentation. This lab is based on one of those tutorials, but includes additional content. The Deployment Manager template used in this lab generates an advanced HTTP(S) Load Balanced deployment for a Logbook sample application. [Sample templates](https://cloud.google.com/deployment-manager/docs/create-advanced-http-load-balanced-deployment) are available in both Jinja and Python. This lab used the Python version.

### Google Cloud Training & Certification

...helps you make the most of Google Cloud technologies. [Our classes](https://cloud.google.com/training/courses) include technical skills and best practices to help you get up to speed quickly and continue your learning journey. We offer fundamental to advanced level training, with on-demand, live, and virtual options to suit your busy schedule. [Certifications](https://cloud.google.com/certification/) help you validate and prove your skill and expertise in Google Cloud technologies.

##### Manual last updated June 19, 2020

##### Lab last tested June 19, 2020

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

</div>

<div class="card__footer">

<div class="card__footer__left"><span>Introductory</span></div>

</div>

](/focuses/2802?parent=catalog)</div>

</div>

</section>

</div>

</div>

</ql-drawer-content><ql-drawer end="" id="outline-drawer" open="" slot="drawer" width="320">

<div class="js-lab-content-outline lab-content__outline">[GSP060](#step1)[Overview](#step2)[Objectives](#step3)[Setup and Requirements](#step4)[Create a virtual environment](#step5)[Clone the Deployment Manager Sample Templates](#step6)[Explore the Sample Files](#step7)[Customize the Deployment](#step8)[Run the Application](#step9)[Verify that the application is operational](#step10)[Configure an uptime check and alert policy in Cloud Monitoring](#step11)[Configure an alerting policy and notification](#step12)[Configure a Dashboard with a Couple of Useful Charts](#step13)[Create a test VM with ApacheBench](#step14)[Simulate a Service Outage](#step15)[Test your knowledge](#step16)[Congratulations!](#step17)[Finish your Quest](#step18)</div>

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

In this lab you will launch a service using Deployment Manager, and monitor it using Cloud Monitoring. You will set up basic black box monitoring with Cloud Monitoring Dashboard and establish uptime check alert notification to trigger incident response.

This lab is included in these quests: [Cloud Architecture](/quests/24), [Deploy and Manage Cloud Environments with Google Cloud](/quests/121), [Cloud Engineering](/quests/66), [Set Up and Configure a Cloud Environment in Google Cloud](/quests/119), [Deployment Manager](/quests/30). If you complete this lab you'll receive credit for it when you enroll in one of these quests.

**Duration:** 0m setup · 90m access · 90m completion

<span>**Levels:** expert</span>

**Permalink:** [https://google.qwiklabs.com/catalog_lab/940](https://google.qwiklabs.com/catalog_lab/940)

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

<div class="controls"><input class="hidden" type="hidden" value="940" name="lab_review[lab_id]" id="lab_review_lab_id"></div>

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

<div class="lab-access-modal"><input type="hidden" name="id" id="id" value="15839"> <input type="hidden" name="user_id" id="user_id" value="4061609"> <input type="hidden" name="launch_with_credits" id="launch_with_credits" value="0" class="js-launch-with-credits-input"> <input type="hidden" name="launch_with_subs" id="launch_with_subs" value="0" class="js-launch-with-subscription-input">

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