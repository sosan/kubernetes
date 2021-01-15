# Continuous Delivery with Jenkins in Kubernetes Engine

<div class="lab-preamble__details subtitle-headline-1"><span>1 hour 15 minutes</span> <span>7 Credits</span>

<div class="lab__rating">[](/focuses/1104/reviews?parent=catalog)<a data-target="#lab-review-modal" data-toggle="modal">Rate Lab</a></div>

</div>

</div>

<div class="js-markdown-instructions markdown-lab-instructions" id="markdown-lab-instructions">

## GSP051

![Google Cloud Self-Paced Labs](https://cdn.qwiklabs.com/GMOHykaqmlTHiqEeQXTySaMXYPHeIvaqa2qHEzw6Occ%3D)

## Overview

In this lab, you will learn how to set up a continuous delivery pipeline with `Jenkins` on Kubernetes engine. Jenkins is the go-to automation server used by developers who frequently integrate their code in a shared repository. The solution you'll build in this lab will be similar to the following diagram:

![overview.png](https://cdn.qwiklabs.com/1b%2B9D20QnfRjAF8c6xlXmexot7TDcOsYzsRwp%2FH4ErE%3D)

You can find more details about running Jenkins on Kubernetes [here](https://cloud.google.com/solutions/jenkins-on-container-engine).

### **What you'll do**

In this lab, you will complete the following tasks:

*   Provision a Jenkins application into a Kubernetes Engine Cluster
*   Set up your Jenkins application using Helm Package Manager
*   Explore the features of a Jenkins application
*   Create and exercise a Jenkins pipeline

### Prerequisites

This is an **advanced level** lab. Before taking it, you should be comfortable with at least the basics of shell programming, Kubernetes, and Jenkins. Here are some Qwiklabs that can get you up to speed:

*   [Introduction to Docker](https://google.qwiklabs.com/catalog_lab/944)
*   [Hello Node Kubernetes](https://google.qwiklabs.com/catalog_lab/468)
*   [Managing Deployments Using Kubernetes Engine](https://google.qwiklabs.com/catalog_lab/572)
*   [Setting up Jenkins on Kubernetes Engine](https://google.qwiklabs.com/catalog_lab/1093)

Once you are prepared, scroll down to learn more about Kubernetes, Jenkins, and Continuous Delivery.

### What is Kubernetes Engine?

Kubernetes Engine is Google Cloud's hosted version of `Kubernetes` - a powerful cluster manager and orchestration system for containers. Kubernetes is an open source project that can run on many different environments—from laptops to high-availability multi-node clusters; from virtual machines to bare metal. As mentioned before, Kubernetes apps are built on `containers` - these are lightweight applications bundled with all the necessary dependencies and libraries to run them. This underlying structure makes Kubernetes applications highly available, secure, and quick to deploy—an ideal framework for cloud developers.

### What is Jenkins?

[Jenkins](https://jenkins.io/) is an open-source automation server that lets you flexibly orchestrate your build, test, and deployment pipelines. Jenkins allows developers to iterate quickly on projects without worrying about overhead issues that can stem from continuous delivery.

### What is Continuous Delivery / Continuous Deployment?

When you need to set up a continuous delivery (CD) pipeline, deploying Jenkins on Kubernetes Engine provides important benefits over a standard VM-based deployment.

When your build process uses containers, one virtual host can run jobs on multiple operating systems. Kubernetes Engine provides `ephemeral build executors`—these are only utilized when builds are actively running, which leaves resources for other cluster tasks such as batch processing jobs. Another benefit of ephemeral build executors is _speed_—they launch in a matter of seconds.

Kubernetes Engine also comes pre-equipped with Google's global load balancer, which you can use to automate web traffic routing to your instance(s). The load balancer handles SSL termination and utilizes a global IP address that's configured with Google's backbone network—coupled with your web front, this load balancer will always set your users on the fastest possible path to an application instance.

Now that you've learned a little bit about Kubernetes, Jenkins, and how the two interact in a CD pipeline, it's time to go build one.

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

## Clone the repository

To get set up, open a new session in Cloud Shell and run the following command to set your zone `us-east1-d`:

    gcloud config set compute/zone us-east1-d

Then clone the lab's sample code:

    git clone https://github.com/GoogleCloudPlatform/continuous-deployment-on-kubernetes.git

Now change to the correct directory:

    cd continuous-deployment-on-kubernetes

## Provisioning Jenkins

### **Creating a Kubernetes cluster**

Now, run the following command to provision a Kubernetes cluster:

    gcloud container clusters create jenkins-cd \
    --num-nodes 2 \
    --machine-type n1-standard-2 \
    --scopes "https://www.googleapis.com/auth/source.read_write,cloud-platform"

This step can take up to several minutes to complete. The extra scopes enable Jenkins to access Cloud Source Repositories and Google Container Registry.

### Test Completed Task

Click **Check my progress** to verify your performed task. If you have successfully created Kubernetes cluster, you'll see an assessment score.

<ql-activity-tracking step="1">Create a Kubernetes cluster (zone: us-east1-d)</ql-activity-tracking>

Before continuing, confirm that your cluster is running by executing the following command:

    gcloud container clusters list

Now, get the credentials for your cluster:

    gcloud container clusters get-credentials jenkins-cd

Kubernetes Engine uses these credentials to access your newly provisioned cluster—confirm that you can connect to it by running the following command:

    kubectl cluster-info

## Setup Helm

In this lab, you will use Helm to install Jenkins from the Charts repository. Helm is a package manager that makes it easy to configure and deploy Kubernetes applications. Once you have Jenkins installed, you'll be able to set up your CI/CD pipeline.

1.  Add Helm's stable chart repo:

    helm repo add jenkins https://charts.jenkins.io

1.  Ensure the repo is up to date:

    helm repo update

## Configure and Install Jenkins

You will use a custom `values` file to add the Google Cloud specific plugin necessary to use service account credentials to reach your Cloud Source Repository.

1.  Use the Helm CLI to deploy the chart with your configuration settings.

    helm install cd jenkins/jenkins -f jenkins/values.yaml --version 1.2.2 --wait

This command may take a couple minutes to complete.

### Test Completed Task

Click **Check my progress** to verify your performed task. If you have successfully configure Jenkins chart you will see an assessment score.

<ql-activity-tracking step="2">Configure and Install Jenkins</ql-activity-tracking>

1.  Once that command completes ensure the Jenkins pod goes to the `Running` state and the container is in the READY state:

    kubectl get pods

**Example Output:**

    NAME                          READY     STATUS    RESTARTS   AGE
    cd-jenkins-7c786475dd-vbhg4   1/1       Running   0          1m

1.  Configure the Jenkins service account to be able to deploy to the cluster.

    kubectl create clusterrolebinding jenkins-deploy --clusterrole=cluster-admin --serviceaccount=default:cd-jenkins

You should receive the following output:

    clusterrolebinding.rbac.authorization.k8s.io/jenkins-deploy created

1.  Run the following command to setup port forwarding to the Jenkins UI from the Cloud Shell

    export POD_NAME=$(kubectl get pods --namespace default -l "app.kubernetes.io/component=jenkins-master" -l "app.kubernetes.io/instance=cd" -o jsonpath="{.items[0].metadata.name}")
    kubectl port-forward $POD_NAME 8080:8080 >> /dev/null &

1.  Now, check that the Jenkins Service was created properly:

    kubectl get svc

**Example Output:**

    NAME               CLUSTER-IP     EXTERNAL-IP   PORT(S)     AGE
    cd-jenkins         10.35.249.67   <none>        8080/TCP    3h
    cd-jenkins-agent   10.35.248.1    <none>        50000/TCP   3h
    kubernetes         10.35.240.1    <none>        443/TCP     9h

You are using the [Kubernetes Plugin](https://wiki.jenkins-ci.org/display/JENKINS/Kubernetes+Plugin) so that our builder nodes will be automatically launched as necessary when the Jenkins master requests them. Upon completion of their work, they will automatically be turned down and their resources added back to the clusters resource pool.

Notice that this service exposes ports `8080` and `50000` for any pods that match the `selector`. This will expose the Jenkins web UI and builder/agent registration ports within the Kubernetes cluster. Additionally, the `jenkins-ui` services is exposed using a ClusterIP so that it is not accessible from outside the cluster.

## Connect to Jenkins

1.  The Jenkins chart will automatically create an admin password for you. To retrieve it, run:

    printf $(kubectl get secret cd-jenkins -o jsonpath="{.data.jenkins-admin-password}" | base64 --decode);echo

1.  To get to the Jenkins user interface, click on the **Web Preview** button in cloud shell, then click **Preview on port 8080**:

![web_prev.png](https://cdn.qwiklabs.com/wy13PEPdV6ZbYMJR2tmk3iKe%2FEyVDXVWtrWFVJeZUXk%3D)

1.  You should now be able to log in with username `admin` and your auto-generated password.

You now have Jenkins set up in your Kubernetes cluster! Jenkins will drive your automated CI/CD pipelines in the next sections.

## Understanding the Application

You'll deploy the sample application, `gceme`, in your continuous deployment pipeline. The application is written in the Go language and is located in the repo's sample-app directory. When you run the gceme binary on a Compute Engine instance, the app displays the instance's metadata in an info card.

![90fb779a65809b9e.png](https://cdn.qwiklabs.com/ceqFX6Vwtd12NSBtnNhrkemKfRSLHbCmFZiLn8WmC98%3D)

The application mimics a microservice by supporting two operation modes.

*   In **backend mode**: gceme listens on port 8080 and returns Compute Engine instance metadata in JSON format.
*   In **frontend mode**: gceme queries the backend gceme service and renders the resulting JSON in the user interface.

![fc22b68ab20dfe0e.png](https://cdn.qwiklabs.com/P1T5JBWWprA4iLf%2FB5%2BO6as7otLE25YBde57gzZwSz4%3D)

## Deploying the Application

You will deploy the application into two different environments:

*   **Production**: The live site that your users access.
*   **Canary**: A smaller-capacity site that receives only a percentage of your user traffic. Use this environment to validate your software with live traffic before it's released to all of your users.

In Google Cloud Shell, navigate to the sample application directory:

    cd sample-app

Create the Kubernetes namespace to logically isolate the deployment:

    kubectl create ns production

Create the production and canary deployments, and the services using the `kubectl apply` commands:

    kubectl apply -f k8s/production -n production

    kubectl apply -f k8s/canary -n production

    kubectl apply -f k8s/services -n production

### Test Completed Task

Click **Check my progress** to verify your performed task. If you have successfully created deployments you will see an assessment score.

<ql-activity-tracking step="3">Create the production and canary deployments</ql-activity-tracking>

By default, only one replica of the frontend is deployed. Use the `kubectl scale` command to ensure that there are at least 4 replicas running at all times.

Scale up the production environment frontends by running the following command:

    kubectl scale deployment gceme-frontend-production -n production --replicas 4

Now confirm that you have 5 pods running for the frontend, 4 for production traffic and 1 for canary releases (changes to the canary release will only affect 1 out of 5 (20%) of users):

    kubectl get pods -n production -l app=gceme -l role=frontend

Also confirm that you have 2 pods for the backend, 1 for production and 1 for canary:

    kubectl get pods -n production -l app=gceme -l role=backend

Retrieve the external IP for the production services:

    kubectl get service gceme-frontend -n production

<aside class="warning">

**Note:** It can take several minutes before you see the load balancer external IP address.

</aside>

**Example Output:**

    NAME            TYPE          CLUSTER-IP     EXTERNAL-IP     PORT(S)  AGE
    gceme-frontend  LoadBalancer  10.79.241.131  104.196.110.46  80/TCP   5h

Paste **External IP** into a browser to see the info card displayed on a card—you should get a similar page:

![backend_card.png](https://cdn.qwiklabs.com/dfXfZfxRk0UMSTZm8FyIaLFsHla7AlUmxHO70UTOFjk%3D)

Now, store the _frontend service_ load balancer IP in an environment variable for use later:

    export FRONTEND_SERVICE_IP=$(kubectl get -o jsonpath="{.status.loadBalancer.ingress[0].ip}" --namespace=production services gceme-frontend)

Confirm that both services are working by opening the frontend external IP address in your browser. Check the version output of the service by running the following command (it should read 1.0.0):

    curl http://$FRONTEND_SERVICE_IP/version

You have successfully deployed the sample application! Next, you will set up a pipeline for deploying your changes continuously and reliably.

## Creating the Jenkins Pipeline

### **Creating a repository to host the sample app source code**

Create a copy of the `gceme` sample app and push it to a [Cloud Source Repository](https://cloud.google.com/source-repositories/docs/):

    gcloud source repos create default

You can ignore the warning, you will not be billed for this repository.

### Test Completed Task

Click **Check my progress** to verify your performed task. If you have successfully created source repository you will see an assessment score.

<ql-activity-tracking step="4">Create a repository</ql-activity-tracking>

    git init

Initialize the sample-app directory as its own Git repository:

    git config credential.helper gcloud.sh

Run the following command:

    git remote add origin https://source.developers.google.com/p/$DEVSHELL_PROJECT_ID/r/default

Set the username and email address for your Git commits. Replace `[EMAIL_ADDRESS]` with your Git email address and `[USERNAME]` with your Git username:

    git config --global user.email "[EMAIL_ADDRESS]"

    git config --global user.name "[USERNAME]"

Add, commit, and push the files:

    git add .

    git commit -m "Initial commit"

    git push origin master

### **Adding your service account credentials**

Configure your credentials to allow Jenkins to access the code repository. Jenkins will use your cluster's service account credentials in order to download code from the Cloud Source Repositories.

**Step 1**: In the Jenkins user interface, click **Manage Jenkins** in the left navigation then click **Manage Credentials**.

**Step 2**: Click **Jenkins** ![stored_scoped_cred.png](https://cdn.qwiklabs.com/SiqLluBt531RSaLWnUZm28LRRVJCVBbINUZz7vaSyfo%3D)

**Step 3**: Click **Global credentials (unrestricted)**.

**Step 4**: Click **Add Credentials** in the left navigation.

**Step 5**: Select **Google Service Account from metadata** from the **Kind** drop-down and click **OK**.

The global credentials has been added. The name of the credential is the `Project ID` found in the `CONNECTION DETAILS` section of the lab.

![global_cred.png](https://cdn.qwiklabs.com/AADN9HYavaPKH3MPQMY8P9qhACY47HgPjAOuRLZz97M%3D)

**Creating the Jenkins job**

Navigate to your Jenkins user interface and follow these steps to configure a Pipeline job.

**Step 1**: Click **New Item** in the left navigation:

![86e92964a4599231.png](https://cdn.qwiklabs.com/KXB9gx8B77F27XAgu6bgXle0HvnoERwlPf9f2WYVqQM%3D)

**Step 2**: Name the project **sample-app**, then choose the **Multibranch Pipeline** option and click **OK**.

**Step 3**: On the next page, in the **Branch Sources** section, click **Add Source** and select **git**.

**Step 4**: Paste the **HTTPS clone URL** of your sample-app repo in Cloud Source Repositories into the **Project Repository** field. Replace `[PROJECT_ID]` with your **Project ID**:

    https://source.developers.google.com/p/[PROJECT_ID]/r/default

**Step 5**: From the **Credentials** drop-down, select the name of the credentials you created when adding your service account in the previous steps.

**Step 6**: Under **Scan Multibranch Pipeline Triggers** section, check the **Periodically if not otherwise run** box and set the **Interval** value to 1 minute.

**Step 7**: Your job configuration should look like this:

![general_tab_jenkins.png](https://cdn.qwiklabs.com/Eke%2BxaE%2BRpitZRG%2FFifw9jmNwAgCLMmnU21mdq8KDco%3D)

![branch_source_jenkins.png](https://cdn.qwiklabs.com/Kp4KwWsjF4YOP1PHIs7i4OPV19GvBQMGqWkqG7UTpOg%3D)

![build_conf_jenkins.png](https://cdn.qwiklabs.com/MmwLl5ZgWpBlrD%2B9%2Bfr97UhBrnVuqlpmMvmBofd49YM%3D)

**Step 8**: Click **Save** leaving all other options with their defaults

After you complete these steps, a job named `Branch indexing` runs. This meta-job identifies the branches in your repository and ensures changes haven't occurred in existing branches. If you click sample-app in the top left, the `master` job should be seen.

<aside class="warning">

**Note:** The first run of the master job might fail until you make a few code changes in the next step.

</aside>

You have successfully created a Jenkins pipeline! Next, you'll create the development environment for continuous integration.

## Creating the Development Environment

Development branches are a set of environments your developers use to test their code changes before submitting them for integration into the live site. These environments are scaled-down versions of your application, but need to be deployed using the same mechanisms as the live environment.

### Creating a development branch

To create a development environment from a feature branch, you can push the branch to the Git server and let Jenkins deploy your environment.

Create a development branch and push it to the Git server:

    git checkout -b new-feature

### Modifying the pipeline definition

The `Jenkinsfile` that defines that pipeline is written using the [Jenkins Pipeline Groovy syntax](https://jenkins.io/doc/book/pipeline/syntax/). Using a `Jenkinsfile` allows an entire build pipeline to be expressed in a single file that lives alongside your source code. Pipelines support powerful features like parallelization and require manual user approval.

In order for the pipeline to work as expected, you need to modify the `Jenkinsfile` to set your project ID.

Open the Jenkinsfile in your terminal editor, for example `vi`:

    vi Jenkinsfile

Start the editor:

    i

Add your `PROJECT_ID` to the `REPLACE_WITH_YOUR_PROJECT_ID` value. (Your `PROJECT_ID` is your Project ID found in the `CONNECTION DETAILS` section of the lab. You can also run `gcloud config get-value project` to find it.

    PROJECT = "REPLACE_WITH_YOUR_PROJECT_ID"
    APP_NAME = "gceme"
    FE_SVC_NAME = "${APP_NAME}-frontend"
    CLUSTER = "jenkins-cd"
    CLUSTER_ZONE = "us-east1-d"
    IMAGE_TAG = "gcr.io/${PROJECT}/${APP_NAME}:${env.BRANCH_NAME}.${env.BUILD_NUMBER}"
    JENKINS_CRED = "${PROJECT}"

Save the `Jenkinsfile` file: hit **Esc** then (for `vi` users):

    :wq

### Modify the site

To demonstrate changing the application, you will change the gceme cards from **blue** to **orange**.

Open `html.go:`

    vi html.go

Start the editor:

    i

Change the two instances of `<div class="card blue">` with following:

    <div class="card orange">

Save the `html.go` file: press **Esc** then:

    :wq

Open `main.go:`

    vi main.go

Start the editor:

    i

The version is defined in this line:

    const version string = "1.0.0"

Update it to the following:

    const version string = "2.0.0"

Save the main.go file one more time: **Esc** then:

    :wq

## Kick off Deployment

Commit and push your changes:

    git add Jenkinsfile html.go main.go

    git commit -m "Version 2.0.0"

    git push origin new-feature

This will kick off a build of your development environment.

After the change is pushed to the Git repository, navigate to the Jenkins user interface where you can see that your build started for the `new-feature` branch. It can take up to a minute for the changes to be picked up.

After the build is running, click the down arrow next to the **build** in the left navigation and select **Console output**:

![6ea3b2ed776e3b29.png](https://cdn.qwiklabs.com/3SL2TROBOgHPAAXRLhLCMEmyJ4h0LJGZAH2aNrLsToQ%3D)

Track the output of the build for a few minutes and watch for the `kubectl --namespace=new-feature apply...` messages to begin. Your new-feature branch will now be deployed to your cluster.

<aside class="special">

**Note:** In a development scenario, you wouldn't use a public-facing load balancer. To help secure your application, you can use [kubectl proxy](https://kubernetes.io/docs/tasks/extend-kubernetes/http-proxy-access-api/). The proxy authenticates itself with the Kubernetes API and proxies requests from your local machine to the service in the cluster without exposing your service to the Internet.

</aside>

If you didn't see anything in `Build Executor`, not to worry. Just go to the Jenkins homepage --> sample app. Verify that the `new-feature` pipeline has been created.

Once that's all taken care of, start the proxy in the background:

    kubectl proxy &

If it stalls, press **Ctrl + C** to exit out. Verify that your application is accessible by sending a request to `localhost` and letting `kubectl` proxy forward it to your service:

    curl \
    http://localhost:8001/api/v1/namespaces/new-feature/services/gceme-frontend:80/proxy/version

You should see it respond with 2.0.0, which is the version that is now running.

If you receive a similar error:

    {
      "kind": "Status",
      "apiVersion": "v1",
      "metadata": {
      },
      "status": "Failure",
      "message": "no endpoints available for service \"gceme-frontend:80\"",
      "reason": "ServiceUnavailable",
      "code": 503

It means your frontend endpoint hasn't propagated yet—wait a little bit and try the `curl` command again. Move on when you get the following output:

    2.0.0

You have set up the development environment! Next, you will build on what you learned in the previous module by deploying a canary release to test out a new feature.

## Deploying a Canary Release

You have verified that your app is running the latest code in the development environment, so now deploy that code to the canary environment.

Create a canary branch and push it to the Git server:

    git checkout -b canary

    git push origin canary

In Jenkins, you should see the **canary** pipeline has kicked off. Once complete, you can check the service URL to ensure that some of the traffic is being served by your new version. You should see about 1 in 5 requests (in no particular order) returning version `2.0.0`.

    export FRONTEND_SERVICE_IP=$(kubectl get -o \
    jsonpath="{.status.loadBalancer.ingress[0].ip}" --namespace=production services gceme-frontend)

    while true; do curl http://$FRONTEND_SERVICE_IP/version; sleep 1; done

If you keep seeing 1.0.0, try running the above commands again. Once you've verified that the above works, end the command with **Ctrl + C**.

That's it! You have deployed a canary release. Next you will deploy the new version to production.

## Deploying to production

Now that our canary release was successful and we haven't heard any customer complaints, deploy to the rest of your production fleet.

Create a canary branch and push it to the Git server:

    git checkout master

    git merge canary

    git push origin master

In Jenkins, you should see the master pipeline has kicked off. _Once complete_ (which may take a few minutes), you can check the service URL to ensure that all of the traffic is being served by your new version, 2.0.0.

    export FRONTEND_SERVICE_IP=$(kubectl get -o \
    jsonpath="{.status.loadBalancer.ingress[0].ip}" --namespace=production services gceme-frontend)

    while true; do curl http://$FRONTEND_SERVICE_IP/version; sleep 1; done

Once again, if you see instances of `1.0.0` try running the above commands again. You can stop this command by pressing **Ctrl + C**.

**Example Output:**

    gcpstaging9854_student@qwiklabs-gcp-df93aba9e6ea114a:~/continuous-deployment-on-kubernetes/sample-app$ while true; do curl http://$FRONTEND_SERVICE_IP/version; sleep 1; done
    2.0.0
    2.0.0
    2.0.0
    2.0.0
    2.0.0
    2.0.0
    ^C

You can also navigate to site on which the gceme application displays the info cards. The card color changed from blue to orange. Here's the command again to get the external IP address so you can check it out:

    kubectl get service gceme-frontend -n production

**Example Output:**

![orange_card.png](https://cdn.qwiklabs.com/nFJlHtZPI%2F8HvtCNSGRmCyqRkMDylftq%2F200xRXtUKc%3D)

## Test your Understanding

Below are multiple-choice questions to reinforce your understanding of this lab's concepts. Answer them to the best of your abilities.

<ql-true-false-probe answer="true" stem="The Helm chart is a collection of files that describe a related set of Kubernetes resources."></ql-true-false-probe>

**You're done!**

Awesome job, you have successfully deployed your application to production!

## Congratulations!

This concludes this hands-on lab deploying and working with Jenkins in Kubernetes Engine to enable a Continuous Delivery / Continuous Deployment pipeline. You've had the opportunity to deploy a **significant** DevOps tool in Kubernetes Engine and configure it for production use. You've worked with the kubectl command-line tool and deployment configurations in YAML files, and have learned a bit about setting up Jenkins pipelines for a development / deployment process. With this practical hands-on experience you should feel comfortable applying these tools in your own DevOps shop.

### Finish Your Quest

![6d0798e24a18671b.png](https://cdn.qwiklabs.com/2LSNdbBU3lu0UqtX1hUrMEip%2BbKsc9ecpQe%2BXtIL9qY%3D) ![Cloud_Architecture.png](https://cdn.qwiklabs.com/QNBqfg1D%2FzCw3KdiioKbnB1YWX9jpAs21holGhwny%2FY%3D)

This self-paced lab is part of the Qwiklabs [Kubernetes in the Google Cloud](https://google.qwiklabs.com/quests/29), [Cloud Architecture](https://google.qwiklabs.com/quests/24), and [DevOps Essentials](https://google.qwiklabs.com/quests/96) Quests. A Quest is a series of related labs that form a learning path. Completing this Quest earns you the badge above, to recognize your achievement. You can make your badge (or badges) public and link to them in your online resume or social media account. Enroll in a Quest and get immediate completion credit if you've taken this lab. [See other available Qwiklabs Quests](https://google.qwiklabs.com/catalog).

### Take Your Next Lab

Continue your Quest with [Hello Node Kubernetes](https://google.qwiklabs.com/catalog_lab/468), or check out these suggestions:

*   [Orchestrating the Cloud with Kubernetes](https://google.qwiklabs.com/catalog_lab/486)

*   [Managing Deployments Using Kubernetes Engine](https://google.qwiklabs.com/catalog_lab/572)

### Next Steps / Learn More

*   Read further on the [Jenkins in Kubernetes Engine](https://cloud.google.com/solutions/jenkins-on-container-engine) Solution.
*   Learn how to [use Jenkins to enable Continuous Delivery to Kubernetes Engine](https://cloud.google.com/solutions/continuous-delivery-jenkins-container-engine)
*   Read further on [DevOps Solutions and DevOps Guides](https://cloud.google.com/solutions/devops/) in the Google Cloud documentation
*   Connect with the [Jenkins Community](https://jenkins.io/)!

### Google Cloud Training & Certification

...helps you make the most of Google Cloud technologies. [Our classes](https://cloud.google.com/training/courses) include technical skills and best practices to help you get up to speed quickly and continue your learning journey. We offer fundamental to advanced level training, with on-demand, live, and virtual options to suit your busy schedule. [Certifications](https://cloud.google.com/certification/) help you validate and prove your skill and expertise in Google Cloud technologies.

##### Manual Last Updated December 28, 2020

##### Lab Last Tested December 28, 2020

Copyright 2020 Google LLC All rights reserved. Google and the Google logo are trademarks of Google LLC. All other company and product names may be trademarks of the respective companies with which they are associated.

## Continue questing
[Hands-On Lab]()

### Deploy to Kubernetes in Google Cloud: Challenge Lab

This challenge lab tests your skills and knowledge from the labs in the Kubernetes in Google Cloud quest. You should be familiar with the content of the labs before attempting this lab.

[Expert]()
