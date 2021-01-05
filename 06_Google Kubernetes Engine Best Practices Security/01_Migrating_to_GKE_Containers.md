# Migrating to GKE Containers

<div class="lab-preamble__details subtitle-headline-1"><span>1 hour 15 minutes</span> <span>7 Credits</span>

<div class="lab__rating">[](/focuses/12768/reviews?parent=catalog)<a data-target="#lab-review-modal" data-toggle="modal">Rate Lab</a></div>

</div>

</div>

<div class="js-markdown-instructions markdown-lab-instructions" id="markdown-lab-instructions">

![GKE-Engine.png](https://cdn.qwiklabs.com/QKV092kALeUu8E6Qu3qVrHWJ%2BjkMkSlR%2BwZpsbfX73w%3D)

## GSP475

![Google Cloud Self-Paced Labs](https://cdn.qwiklabs.com/GMOHykaqmlTHiqEeQXTySaMXYPHeIvaqa2qHEzw6Occ%3D)

Containers are quickly becoming an industry standard for deployment of software applications. The business and technological advantages of containerizing workloads are driving many teams towards moving their applications to containers. This lab provides a basic walkthrough of migrating a stateless application from running on a VM to running on [Kubernetes Engine (GKE)](https://cloud.google.com/kubernetes-engine/). It demonstrates the lifecycle of an application transitioning from a typical VM/OS-based deployment to a specialized os for containers to a platform for containers better known as [GKE](https://cloud.google.com/kubernetes-engine/).

## Overview

There are numerous advantages to using containers to deploy applications. Among these are:

1.  _Isolated_ - Applications have their own libraries; no conflicts will arise from different libraries in other applications.

2.  _Limited (limits on CPU/memory)_ - Applications may not hog resources from other applications.

3.  _Portable_ - The container contains everything it needs and is not tied to an OS or Cloud provider.

4.  _Lightweight_ - The kernel is shared, making it much smaller and faster than a full OS image.

## What you'll learn

This project demonstrates migrating a simple Python application named [Prime-flask](https://github.com/GoogleCloudPlatform/gke-migration-to-containers/blob/master/container/prime-flask-server.py) to:

1.  A [virtual machine (Debian VM)](https://cloud.google.com/compute/docs/instances/) where `Prime-flask` is deployed as the only application, much like a traditional application is run in an on-premises datacenter

2.  A containerized version of `Prime-flask` is deployed on [Container-Optimized OS (COS)](https://cloud.google.com/container-optimized-os/)

3.  A [Kubernetes](https://kubernetes.io/) deployment where `Prime-flask` is exposed via a load balancer and deployed in Kubernetes Engine

After the deployment you'll run a load test against the final deployment and scale it to accommodate the load.

If during the lab you get an error, it probably makes sense to re-execute the failing script. Occasionally there are network connectivity issues, and retrying will likely work the subsequent time.

This lab was created by GKE Helmsman engineers to help you grasp a better understanding of migrating to containers. You can view this demo on on Github [here](https://github.com/GoogleCloudPlatform/gke-migration-to-containers.git). We encourage any and all to contribute to our assets!

## Architecture

**Configuration 1:** a virtual machine running Debian, app deployed directly to the host OS, no containers

![screenshot](https://cdn.qwiklabs.com/aBo1TdWIv1Z6t5%2BIM5T%2B3v5popLO9DB0grD9cPjIjYQ%3D)

**Configuration 2:** a virtual machine running Container-Optimized OS, app deployed into a container

![screenshot](https://cdn.qwiklabs.com/M8wZrtea2pP8VuBZBFoICAeiuSXkf8PmNaDHMwBNJaA%3D)

**Configuration 3:** Kubernetes Engine (GKE) platform, many machines running many containers

![screenshot](https://cdn.qwiklabs.com/6JpcPcEzTVArtgUNwQfbkWgrd4zh%2BGiENwIoJQ1h92w%3D)

A simple Python [Flask](http://flask.pocoo.org/) web application (`Prime-flask`) was created for this demonstration which contains two endpoints:

`http://<ip>:8080/factorial/` and

`http://<ip>:8080/prime/`

Examples of use would look like:

    curl http://35.227.149.80:8080/prime/10
    The sum of all primes less than 10 is 17

    curl http://35.227.149.80:8080/factorial/10
    The factorial of 10 is 3628800

Also included is a utility to validate a successful deployment.

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

### Install ApacheBench

Install ApacheBench with the following command:

    sudo apt-get install apache2-utils

Type "y" when asked to confirm you want to continue.

Verify that the installation was successful:

    ab -V

Your output should look like this:

    This is ApacheBench, Version 2.3 <$Revision: 1757674 $>
    Copyright 1996 Adam Twiss, Zeus Technology Ltd, http://www.zeustech.net/
    Licensed to The Apache Software Foundation,

### Clone the repo

Open a new session in Cloud Shell and run the following command to clone the sample code repository:

    git clone https://github.com/GoogleCloudPlatform/gke-migration-to-containers.git

Go into the directory of the demo:

    cd gke-migration-to-containers

### Set your region and zone

Certain Compute Engine resources live in regions and zones. A region is a specific geographical location where you can run your resources. Each region has one or more zones.

<aside>Learn more about regions and zones and see a complete list in [Regions & Zones documentation](https://cloud.google.com/compute/docs/regions-zones/).</aside>

Run the following to set a region and zone for your lab (you can use the region/zone that's best for you):

    gcloud config set compute/region us-central1
    gcloud config set compute/zone us-central1-a

### Change machine Type

In the top-right corner of your Cloud Shell session, click **Open Editor** and then click **Open in new window**:

![cloudshelleditor](https://cdn.qwiklabs.com/hZYBOz6ymTpCcrjxYiyPK2gVdwvdXxHVm77O5rBeFfs%3D)

From the left-hand menu, open the **gke-migration-to-containers** > **terraform** > **variables.tf**.

Then scroll down to the "machine_type" configuration (line 25) and change the machine type from `f1-micro` to `n1-standard-1`:

![machine-type](https://cdn.qwiklabs.com/viOKnHl8C7GukzbwfVeOd3M9oIwcPboO9fMhZITlH1g%3D)

## Deployment

The infrastructure required by this project can be deployed by executing:

    make create

The setup of this demo can take up to **15 minutes** to provision. If there is no error the best thing to do is keep waiting. The execution of `make create` should **not** be interrupted. While the resources are building, you can check on the progress in the Console by going to **Compute Engine** > **VM instances**.

While you're waiting, read ahead to learn what the `create.sh` script does and what is getting created for you.

You can view `gke-migration-to-containers/scripts/create.sh` in the code editor.

The `make create` command calls the create.sh script which performs following tasks:

1.  Packages the deployable `Prime-flask` application, making it ready to be copied to [Google Cloud Storage](https://cloud.google.com/storage/).
2.  Creates the container image via [Google Cloud Build](https://cloud.google.com/cloud-build/) and pushes it to the private [Container Registry (GCR)](https://cloud.google.com/container-registry/) for your project.
3.  Generates an appropriate configuration for [Terraform](https://www.terraform.io).
4.  Executes Terraform which creates the scenarios outlined above.

The following are examples of:

Terraform creating single VM, COS VM, and GKE cluster:

![screenshot](https://cdn.qwiklabs.com/nLTbyOA6DIJNh7Y9CocWWFBqjfJm91kpIj7C9EUkIz0%3D)

Terraform outputs showing prime and factorial endpoints for Debian VM and COS system:

![screenshot](https://cdn.qwiklabs.com/piLnE93Lo5OnLcF8g2CSW1YV7sGWtxJF3j5RYPtVSRI%3D)

Kubernetes Cluster and `Prime-flask` service are up:

![screenshot](https://cdn.qwiklabs.com/qHWRZGl5mNmm3qDrdFFBubW%2BVadz0AvinAF%2BWtMTrBE%3D)

### Test Completed Task

Click **Check my progress** below to check your lab progress. If you successfully created infrastructure deployment, you'll see an assessment score.

<ql-activity-tracking step="1">Create Deployment</ql-activity-tracking>

## Exploring Prime-Flask Environments

Three different environments have been set up that your Prime-flask app could traverse as it is making its way to becoming a container app living on a single virtual machine to a [pod](https://kubernetes.io/docs/concepts/workloads/pods/pod/) running on a container orchestration platform like Kubernetes.

Once your resources are ready, it would benefit you to explore the systems.

Run the following to go into the `vm-webserver` machine that has the application running on host OS:

    gcloud compute ssh vm-webserver --zone us-central1-a

Type "Y" when asked if you want to continue. Press **Enter** twice to not use a passphrase.

In this environment there is no isolation, and portability is less efficient. In a sense, the app has access to the whole system, and, depending on other factors, may not have automatic recovery of application if it fails. Scaling up this application may require more virtual machines to spin up and most likely will not be the best use of resources.

List all processes:

    ps aux

(Output)

    root       882  0.0  1.1  92824  6716 ?        Ss   18:41   0:00 sshd: user [priv]
    user       888  0.0  0.6  92824  4052 ?        S    18:41   0:00 sshd: user@pts/0
    user       889  0.0  0.6  19916  3880 pts/0    Ss   18:41   0:00 -bash
    user       895  0.0  0.5  38304  3176 pts/0    R+   18:41   0:00 ps aux
    apprunn+  7938  0.0  3.3  48840 20328 ?        Ss   Mar19   1:06 /usr/bin/python /usr/local/bin/gunicorn --bind 0.0.0.0:8080 prime-flask-server
    apprunn+ 21662  0.0  3.9  69868 24032 ?        S    Mar20   0:05 /usr/bin/python /usr/local/bin/gunicorn --bind 0.0.0.0:8080 prime-flask-server

Then exit:

    exit

Run the following to go into the `cos-vm` machine, where you have docker running the container.

    gcloud compute ssh cos-vm --zone us-central1-a

Type "Y" when asked if you want to continue. Press **Enter** twice to not use a passphrase.

COS is an optimized operating system with small OS footprint, which is part of what makes it secure to run container workloads. It has cloud-init and has Docker runtime preinstalled. This system on its own could be great to run several containers that did not need to be run on a platform that provided higher levels of reliability.

Clone the build files:

    git clone https://github.com/GoogleCloudPlatform/gke-migration-to-containers.git
    cd gke-migration-to-containers/container

Build the docker image locally with the following command:

    sudo docker build -t gcr.io/migration-to-containers/prime-flask:1.0.2 .

Run the following command to get a list of processes running on port 8080:

    ps aux | grep 8080

You should receive a similar output:

![processes](https://cdn.qwiklabs.com/KF2xdNT2JR6ncwPRe%2BUnwv73LHcNzFMsJEJZvivCdRU%3D)

Find the first `chronos` port number, which will look like the following:

![cron-port](https://cdn.qwiklabs.com/RRvlu3k%2FJ%2BWQ8lFEhLzXhZP3VhoSpZZmuNxtvBQ5EmU%3D)

Then run the following command to kill the process on the port number, replacing `<YOUR_PORT_NUMBER>` with the cron port number you found (in the above example, you would use 763):

    sudo kill -9 <YOUR_PORT_NUMBER>

Now run the following docker command to retrieve the local image and run it on the vm:

    sudo docker run --rm -d --name=appuser -p 8080:8080 gcr.io/migration-to-containers/prime-flask:1.0.2

You can also run `ps aux` on the host and see the `prime-flask` running:

    ps aux

Notice the docker and container references:

    root         626  0.0  5.7 496812 34824 ?        Ssl  Mar19   0:14 /usr/bin/docker run --rm --name=flaskservice -p 8080:8080 gcr.io/migration-to-containers/prime-flask:1.0.2
    root         719  0.0  0.5 305016  3276 ?        Sl   Mar19   0:00 /usr/bin/docker-proxy -proto tcp -host-ip 0.0.0.0 -host-port 8080 -container-ip 172.17.0.2 -container-port 8080
    root         724  0.0  0.8 614804  5104 ?        Sl   Mar19   0:09 docker-containerd-shim -namespace moby -workdir /var/lib/docker/containerd/daemon/io.containerd.runtime.v1.linux/mo
    chronos      741  0.0  0.0    204     4 ?        Ss   Mar19   0:00 /usr/bin/dumb-init /usr/local/bin/gunicorn --bind 0.0.0.0:8080 prime-flask-server
    chronos      774  0.0  3.2  21324 19480 ?        Ss   Mar19   1:25 /usr/local/bin/python /usr/local/bin/gunicorn --bind 0.0.0.0:8080 prime-flask-server
    chronos    14376  0.0  4.0  29700 24452 ?        S    Mar20   0:05 /usr/local/bin/python /usr/local/bin/gunicorn --bind 0.0.0.0:8080 prime-flask-server

Also notice, if you try to list the python path it does not exist:

    ls /usr/local/bin/python

(Output)

    ls: cannot access '/usr/local/bin/python': No such file or directory

Docker list containers:

    sudo docker ps

(Output)

    CONTAINER ID        IMAGE                                                  COMMAND                  CREATED             STATUS              PORTS                    NAMES
    d147963ec3ca        gcr.io/migration-to-containers/prime-flask:1.0.2   "/usr/bin/dumb-init …"   39 hours ago        Up 39 hours         0.0.0.0:8080->8080/tcp   flaskservice

Now you can execute a command to see running process on the container:

    sudo docker exec -it $(sudo docker ps |awk '/prime-flask/ {print $1}') ps aux

(Output)

    PID   USER     TIME  COMMAND
        1 apprunne  0:00 /usr/bin/dumb-init /usr/local/bin/gunicorn --bind 0.0.0.0:
        6 apprunne  1:25 {gunicorn} /usr/local/bin/python /usr/local/bin/gunicorn -
       17 apprunne  0:05 {gunicorn} /usr/local/bin/python /usr/local/bin/gunicorn -
       29 apprunne  0:00 ps aux

Type in "exit":

    exit

Next, go into the Kubernetes environment. Here you can run hundreds or thousands of [pods](https://kubernetes.io/docs/concepts/workloads/pods/pod/) that are groupings of containers. Kubernetes is the defacto standard for deploying containers these days. It offers high productivity, reliability, and scalability. Kubernetes makes sure your containers have a home, and if a container happens to fail, it will respawn it again. You can have many machines making up the cluster and in so doing you can spread it across different zones ensuring availability, and resilience to potential issues.

Get cluster configuration:

    gcloud container clusters get-credentials prime-server-cluster

Get pods running in the default namespace:

    kubectl get pods

(Output)

    NAME                            READY   STATUS    RESTARTS   AGE
    prime-server-6b94cdfc8b-dfckf   1/1     Running   0          2d5h

See what is running on the pod:

    kubectl exec $(kubectl get pods -lapp=prime-server -ojsonpath='{.items[].metadata.name}')  -- ps aux

(Output)

    PID   USER     TIME  COMMAND
        1 apprunne  0:00 /usr/bin/dumb-init /usr/local/bin/gunicorn --bind 0.0.0.0:8080 prime-flask-server
        6 apprunne  1:16 {gunicorn} /usr/local/bin/python /usr/local/bin/gunicorn --bind 0.0.0.0:8080 prime-flask-server
        8 apprunne  2:52 {gunicorn} /usr/local/bin/python /usr/local/bin/gunicorn --bind 0.0.0.0:8080 prime-flask-server
       19 apprunne  0:00 ps aux

As you can see, the python application is now running in a container. The application can't access anything on the host. The container is isolated. It runs in a linux namespace and can't (by default) access files, the network, or other resources running on the VM, in containers or otherwise.

## Validation

Now that the application is deployed, validate these three deployments by executing:

    make validate

Occasionally the APIs take a few moments to complete. Running `make validate` immediately could potentially appear to fail, but in fact the instances haven't finished initializing. Waiting for a minute or two and retrying the command should resolve any issues.

A successful output will look like this:

    Validating Debian VM Webapp...
    Testing endpoint http://35.227.149.80:8080
    Endpoint http://35.227.149.80:8080 is responding.
    **** http://35.227.149.80:8080/prime/10
    The sum of all primes less than 10 is 17
    The factorial of 10 is 3628800

    Validating Container OS Webapp...
    Testing endpoint http://35.230.123.231:8080
    Endpoint http://35.230.123.231:8080 is responding.
    **** http://35.230.123.231:8080/prime/10
    The sum of all primes less than 10 is 17
    The factorial of 10 is 3628800

    Validating Kubernetes Webapp...
    Testing endpoint http://35.190.89.136
    Endpoint http://35.190.89.136 is responding.
    **** http://35.190.89.136/prime/10
    The sum of all primes less than 10 is 17
    The factorial of 10 is 3628800

Of course, the IP addresses will likely differ for your deployment.

## Load Testing

In Cloud Shell click the **+** to open a new Cloud Shell tab.

Execute the following, for each resource, replacing `[IP_ADDRESS]` with the **IP address and port** from your validation output in the previous step. Note that the Kubernetes deployment runs on port `80`, while the other two deployments run on port `8080`:

    ab -c 120 -t 60  http://<IP_ADDRESS>/prime/10000

ApacheBench (`ab`) will execute 120 concurrent requests against the provided endpoint for 1 minute. The demo application's replica is insufficiently sized to handle this volume of requests.

This can be confirmed by reviewing the output from the `ab` command. If you see a `Failed requests` value of greater than 0, this means that the server couldn't respond successfully to this load:

![screenshot](https://cdn.qwiklabs.com/zRrDWLQ7U4IfJlnoD2ZtVfvwo%2Bx8S0GI1H4zZDUBOBM%3D)

**Note:** You may not see failures on all three of your resources.

### Test Completed Task

Click **Check my progress** below to check your lab progress. If you successfully performed load testing, you'll see an assessment score.

<ql-activity-tracking step="2">Load Testing</ql-activity-tracking>

One way to ensure that your system has the capacity to handle this type of traffic is by scaling up. In this case, scale the service horizontally.

In the Debian and COS architectures, horizontal scaling would include:

1.  Creating a load balancer.
2.  Spinning up additional instances.
3.  Registering them with the load balancer.

This is an involved process and is out of scope for this demonstration.

For the third (Kubernetes) deployment, the process is far easier.

In your 1st Cloud Shell tab, run the following:

    kubectl scale --replicas 3 deployment/prime-server

### Test Completed Task

Click **Check my progress** below to check your lab progress. If you successfully scaled deployment, you'll see an assessment score.

<ql-activity-tracking step="3">Scale prime-server deployment</ql-activity-tracking>

After allowing 30 seconds for the replicas to initialize, re-run the load test:

    ab -c 120 -t 60  http://<IP_ADDRESS>/prime/10000

Notice how the `Failed requests` is now 0\. This means that all of the 10,000+ requests were successfully answered by the server:

![screenshot](https://cdn.qwiklabs.com/xN8Ara7Oz6%2BUbDrxvfhCwfawr%2FgXoMD4zcbzwUFpMWE%3D)

## Tear Down

Although Qwiklabs will clean up all resources provided to you for this lab, it's good to know how to clean up your own environment.

Run the following in your 1st Cloud Shell tab:

    make teardown

It will run `terraform destroy` which will destroy all of the resources created for this demonstration.

![screenshot](https://cdn.qwiklabs.com/FWthwHGVx6G6KkmTg3wVA6lJuXmG%2BZqA24RDwyfiMVo%3D)

## Congratulations!

### Finish Your Quest

![Enterprise-Customers.png](https://cdn.qwiklabs.com/XArdOxOtBmZg8V4GpuBLqcLsnklweSkkEkmbmTtqBHE%3D)

This self-paced lab is part of the Qwiklabs [Google Kubernetes Engine Best Practices: Security](https://google.qwiklabs.com/quests/64) Quest. A Quest is a series of related labs that form a learning path. Completing this Quest earns you the badge above, to recognize your achievement. You can make your badge (or badges) public and link to them in your online resume or social media account. [Enroll in this Quest](https://google.qwiklabs.com/quests/64/enroll) and get immediate completion credit if you've taken this lab. [See other available Qwiklabs Quests](https://google.qwiklabs.com/catalog).

### Take your next lab

Continue your Quest with [How to Use a Network Policy on Kubernetes Engine](https://google.qwiklabs.com/catalog_lab/1764), or check out these suggestions:

*   [Google Kubernetes Engine Security: Binary Authorization](https://google.qwiklabs.com/catalog_lab/1718)

*   [Accessing Role-based Access Control in Kubernetes Engine](https://google.qwiklabs.com/catalog_lab/1720)

### Next Steps / Learn More

*   For additional information see: [Embarking on a Journey Towards Containerizing Your Workloads](https://www.youtube.com/watch?v=_aFA-p87Eec&index=22&list=PLBgogxgQVM9uSqzLOc66kNIZMUOvnGbU4).
*   More information on [virtual machines](https://cloud.google.com/compute/docs/instances/).

### Google Cloud Training & Certification

...helps you make the most of Google Cloud technologies. [Our classes](https://cloud.google.com/training/courses) include technical skills and best practices to help you get up to speed quickly and continue your learning journey. We offer fundamental to advanced level training, with on-demand, live, and virtual options to suit your busy schedule. [Certifications](https://cloud.google.com/certification/) help you validate and prove your skill and expertise in Google Cloud technologies.

##### Manual Last Updated December 10, 2020

##### Lab Last Tested December 10, 2020

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

<div class="js-lab-content-outline lab-content__outline">[GSP475](#step1)[Overview](#step2)[What you'll learn](#step3)[Architecture](#step4)[Setup](#step5)[Deployment](#step6)[Exploring Prime-Flask Environments](#step7)[Validation](#step8)[Load Testing](#step9)[Tear Down](#step10)[Congratulations!](#step11)</div>

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

This lab teaches you how to migrate a stateless application from running on a VM to running on Kubernetes Engine (GKE). You will learn about the lifecycle of an application transitioning from a typical VM/OS-based deployment to three different containerized cloud infrastructure platforms.

This lab is included in these quests: [Google Kubernetes Engine Best Practices: Security](/quests/64), [Secure Workloads in Google Kubernetes Engine](/quests/142). If you complete this lab you'll receive credit for it when you enroll in one of these quests.

**Duration:** 0m setup · 75m access · 75m completion

<span>**Levels:** advanced</span>

**Permalink:** [https://google.qwiklabs.com/catalog_lab/2762](https://google.qwiklabs.com/catalog_lab/2762)

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

<div class="controls"><input class="hidden" type="hidden" value="2762" name="lab_review[lab_id]" id="lab_review_lab_id"></div>

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

<div class="lab-access-modal"><input type="hidden" name="id" id="id" value="12768"> <input type="hidden" name="user_id" id="user_id" value="4061609"> <input type="hidden" name="launch_with_credits" id="launch_with_credits" value="0" class="js-launch-with-credits-input"> <input type="hidden" name="launch_with_subs" id="launch_with_subs" value="0" class="js-launch-with-subscription-input">

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