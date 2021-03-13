# VPC Networks - Controlling Access

## GSP213

![Google Cloud Self-Paced Labs](https://cdn.qwiklabs.com/GMOHykaqmlTHiqEeQXTySaMXYPHeIvaqa2qHEzw6Occ%3D)

## Overview

In this lab, you create two nginx web servers and control external HTTP access to the web servers using tagged firewall rules. Then, you explore IAM roles and service accounts.

### Objectives

In this lab, you learn how to perform the following tasks:

*   Create an nginx web server

*   Create tagged firewall rules

*   Create a service account with IAM roles

*   Explore permissions for the Network Admin and Security Admin roles

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

## Create the web servers

Create two web servers (**blue** and **green**) in the **default** VPC network. Then, install **nginx** on the webservers and modify the welcome page to distinguish the servers.

### **Create the blue server**

Create the **blue** server with a network tag.

1.  In the Console, navigate to **Navigation menu** (![mainmenu.png](https://cdn.qwiklabs.com/QRLlvUpflhvp875q7fi7Mf%2FiVCqtMcM10Vfn0vwKZ%2B4%3D)) > **Compute Engine** > **VM instances**.

![nav_comengine.png](https://cdn.qwiklabs.com/AowLsoU%2BcN8NvwZpW2Zx5qqf%2BYcijAh9hXf49G3GyvU%3D)

1.  Click **Create**.

2.  Set the following values, leave all other values at their defaults:

    <table>
    <tbody>
    <tr>
    <th>Property</th>
    <th>Value (type value or select option as specified)</th>
    </tr>
    <tr>
    <td>Name</td>
    <td>blue</td>
    </tr>
    <tr>
    <td>Region</td>
    <td>us-central1 (Iowa)</td>
    </tr>
    <tr>
    <td>Zone</td>
    <td>us-central1-a</td>
    </tr>
    </tbody>
    </table>

    For more information on available regions and zones, refer [here](https://cloud.google.com/compute/docs/regions-zones/#available).

3.  Click **Management, disks, networking, sole tenancy**.

4.  Click **Networking**.

    ![networking_tab.png](https://cdn.qwiklabs.com/l%2B7eyXI1V4PV5%2B2xcloNZ3nQX%2FWxF0y1Z8RKICYbiRU%3D)

5.  For **Network tags**, type **web-server**.

**Note:** Networks use network tags to identify which VM instances are subject to certain firewall rules and network routes. Later in this lab, you create a firewall rule to allow HTTP access for VM instances with the **web-server** tag. Alternatively, you could check the **Allow HTTP traffic** checkbox, which would tag this instance as **http-server** and create the tagged firewall rule for tcp:80 for you.



1.  Click **Create**.

#### Test Completed Task

Click **Check my progress** to verify your performed task. If you have completed the task successfully you will granted with an assessment score.

Create the blue server.

### **Create the green server**

Create the **green** server without a network tag.

1.  Still in the Console, in the **VM instances** dialog, click **Create instance**.

2.  Set the following values, leave all other values at their defaults:

    <table>

    <tbody>

    <tr>

    <th>Property</th>

    <th>Value (type value or select option as specified)</th>

    </tr>

    <tr>

    <td>Name</td>

    <td>green</td>

    </tr>

    <tr>

    <td>Region</td>

    <td>us-central1 (Iowa)</td>

    </tr>

    <tr>

    <td>Zone</td>

    <td>us-central1-a</td>

    </tr>

    </tbody>

    </table>

3.  Click **Create**.

#### Test Completed Task

Click **Check my progress** to verify your performed task. If you have completed the task successfully you will granted with an assessment score.

Create the green server.

### **Install nginx and customize the welcome page**

Install nginx on both VM instances and modify the welcome page to distinguish the servers.

1.  Still in the **VM instances** dialog, for **blue**, click **SSH** to launch a terminal and connect.

    ![blue_ssh.png](https://cdn.qwiklabs.com/KYhDQt1QczHDUKVOGHgUZ3Bb%2F2K%2FCmisoBuQSsqboQw%3D)

2.  In the SSH terminal to blue, run the following command to install nginx:

    sudo apt-get install nginx-light -y

1.  Open the welcome page in the nano editor:

    sudo nano /var/www/html/index.nginx-debian.html

1.  Replace the `<h1>Welcome to nginx!</h1>` line with `<h1>Welcome to the blue server!</h1>`.

2.  Press **CTRL+o**, **ENTER**, **CTRL+x**.

3.  Verify the change:

    cat /var/www/html/index.nginx-debian.html

The output should contain the following (**do not copy; this is example output**):

    ...
    <h1>Welcome to the blue server!</h1>
    <p>If you see this page, the nginx web server is successfully installed and
    working. Further configuration is required.</p>
    ...

1.  Close the SSH terminal to **blue**:

    exit

Repeat the same steps for the **green** server:

1.  For **green**, click **SSH** to launch a terminal and connect.

2.  Install nginx:

    sudo apt-get install nginx-light -y

1.  Open the welcome page in the nano editor:

    sudo nano /var/www/html/index.nginx-debian.html

1.  Replace the `<h1>Welcome to nginx!</h1>` line with `<h1>Welcome to the green server!</h1>`.

2.  Press **CTRL+o**, **ENTER**, **CTRL+x**.

3.  Verify the change:

    cat /var/www/html/index.nginx-debian.html

The output should contain the following (**do not copy; this is example output**):

    ...
    <h1>Welcome to the green server!</h1>
    <p>If you see this page, the nginx web server is successfully installed and
    working. Further configuration is required.</p>
    ...

1.  Close the SSH terminal to **green**:

    exit

#### Test Completed Task

Click **Check my progress** to verify your performed task. If you have completed the task successfully you will granted with an assessment score.

Install Nginx and customize the welcome page.

## Create the firewall rule

Create the tagged firewall rule and test HTTP connectivity.

### Create the tagged firewall rule

Create a firewall rule that applies to VM instances with the **web-server** network tag.

1.  In the Console, navigate to **Navigation menu** (![mainmenu.png](https://cdn.qwiklabs.com/QRLlvUpflhvp875q7fi7Mf%2FiVCqtMcM10Vfn0vwKZ%2B4%3D)) > **VPC network** > **Firewall**.
2.  Notice the **default-allow-internal** firewall rule.

<aside class="special">

The **default-allow-internal** firewall rule allows traffic on all protocols/ports within the **default** network. You want to create a firewall rule to allow traffic from outside this network to only the **blue** server, by using the network tag **web-server**.



1.  Click **Create Firewall Rule**.

2.  Set the following values, leave all other values at their defaults and click **Create**:

    <table>

    <tbody>

    <tr>

    <th>Property</th>

    <th>Value (type value or select option as specified)</th>

    </tr>

    <tr>

    <td>Name</td>

    <td>allow-http-web-server</td>

    </tr>

    <tr>

    <td>Network</td>

    <td>default</td>

    </tr>

    <tr>

    <td>Targets</td>

    <td>Specified target tags</td>

    </tr>

    <tr>

    <td>Target tags</td>

    <td>web-server</td>

    </tr>

    <tr>

    <td>Source filter</td>

    <td>IP Ranges</td>

    </tr>

    <tr>

    <td>Source IP ranges</td>

    <td>0.0.0.0/0</td>

    </tr>

    <tr>

    <td>Protocols and ports</td>

    <td>Specified protocols and ports, and then _check_ tcp, _type:_ 80; and _check_ Other protocols, _type:_ icmp.</td>

    </tr>

    </tbody>

    </table>

<aside class="special">

Make sure to include the **/0** in the **Source IP ranges** to specify all networks.



1.  Click **Create**.

#### Test Completed Task

Click **Check my progress** to verify your performed task. If you have completed the task successfully you will granted with an assessment score.

Create the tagged firewall rule.

### Create a test-vm

Create a **test-vm** instance using the Cloud Shell command line.

Create a **test-vm** instance, in the us-central1-a zone:

    gcloud compute instances create test-vm --machine-type=f1-micro --subnet=default --zone=us-central1-a

The output should look like this (**do not copy; this is example output**):

    NAME     ZONE           MACHINE_TYPE  PREEMPTIBLE  INTERNAL_IP  EXTERNAL_IP    STATUS
    test-vm  us-central1-a  f1-micro                   10.142.0.4   35.237.134.68  RUNNING

<aside class="special">

You can easily create VM instances from the Console or the gcloud command line.



#### Test Completed Task

Click **Check my progress** to verify your performed task. If you have completed the task successfully you will granted with an assessment score.

Create a test-vm.

### Test HTTP connectivity

From **test-vm** `curl` the internal and external IP addresses of **blue** and **green**.

1.  In the Console, navigate to **Navigation menu** (![mainmenu.png](https://cdn.qwiklabs.com/QRLlvUpflhvp875q7fi7Mf%2FiVCqtMcM10Vfn0vwKZ%2B4%3D)) > **Compute Engine** > **VM instances**.

2.  Note the internal and external IP addresses of **blue** and **green**.

3.  For **test-vm**, click **SSH** to launch a terminal and connect.

4.  To test HTTP connectivity to **blue**'s internal IP, run the following command, replacing **blue**'s internal IP:

        curl <Enter blue's internal IP here>

    You should see the `Welcome to the blue server!` header.

5.  To test HTTP connectivity to **green**'s internal IP, run the following command, replacing **green**'s internal IP:

    curl -c 3 <Enter green's internal IP here>

You should see the `Welcome to the green server!` header.

You are able to HTTP access both servers using their internal IP addresses. The connection on tcp:80 is allowed by the **default-allow-internal** firewall rule, as **test-vm** is on the same VPC network as the web servers **default** network).

1.  To test HTTP connectivity to **blue**'s external IP, run the following command, replacing **blue**'s external IP:

        curl <Enter blue's external IP here>

    You should see the `Welcome to the blue server!` header.

2.  To test HTTP connectivity to **green**'s external IP, run the following command, replacing **green**'s external IP:

    curl -c 3 <Enter green's external IP here>

This should not work! The request hangs.



1.  Press **CTRL+c** to stop the HTTP request.

As expected, you are only able to HTTP access the external IP address of the **blue** server as the **allow-http-web-server** only applies to VM instances with the **web-server** tag.


You can verify the same behavior from your browser by opening a new tab and navigating to `http://[External IP of server]`.

## Explore the Network and Security Admin roles

Cloud IAM lets you authorize who can take action on specific resources, giving you full control and visibility to manage cloud resources centrally. The following roles are used in conjunction with single-project networking to independently control administrative access to each VPC Network:

*   **Network Admin**: Permissions to create, modify, and delete networking resources, except for firewall rules and SSL certificates.
*   **Security Admin**: Permissions to create, modify, and delete firewall rules and SSL certificates.

Explore these roles by applying them to a service account, which is a special Google account that belongs to your VM instance, instead of to an individual end user. Rather than creating a new user, you will authorize **test-vm** to use the service account to demonstrate the permissions of the **Network Admin** and **Security Admin** roles.

### Verify current permissions

Currently, **test-vm** uses the [Compute Engine default service account](https://cloud.google.com/compute/docs/access/service-accounts#compute_engine_default_service_account), which is enabled on all instances created by Cloud Shell command-line and the Cloud Console.

Try to list or delete the available firewall rules from **test-vm**.

1.  Return to the **SSH** terminal of the **test-vm** instance.

2.  Try to list the available firewall rules:

    gcloud compute firewall-rules list

The output should look like this (**do not copy; this is example output**):

    ERROR: (gcloud.compute.firewall-rules.list) Some requests did not succeed:
     - Insufficient Permission

This should not work!



1.  Try to delete the **allow-http-web-server** firewall rule:

    gcloud compute firewall-rules delete allow-http-web-server

1.  Enter **Y**, if asked to continue.

The output should look like this (**do not copy; this is example output**):

    ERROR: (gcloud.compute.firewall-rules.delete) Could not fetch resource:
     - Insufficient Permission

This should not work!

The **Compute Engine default service account** does not have the right permissions to allow you to list or delete firewall rules. The same applies to other users who do not have the right roles.



### Create a service account

Create a service account and apply the **Network Admin** role.

1.  In the Console, navigate to **Navigation menu** (![mainmenu.png](https://cdn.qwiklabs.com/QRLlvUpflhvp875q7fi7Mf%2FiVCqtMcM10Vfn0vwKZ%2B4%3D)) > **IAM & admin** > **Service Accounts**.

2.  Notice the **Compute Engine default service account**.

3.  Click **Create service account**.

4.  Set the **Service account** name to **Network-admin** and click **CREATE**.

5.  For **Select a role**, select **Compute Engine** > **Compute Network Admin** and click **CONTINUE** then click **DONE**.

6.  After creating service account 'Network-admin', click on three dots at the right corner and click **Create Key** in the dropdown, then **Create** to download your JSON output.

7.  Click **Close**.

    A JSON key file download to your local computer. Find this key file, you will upload it in to the VM in a later step.

8.  Rename the JSON key file on your local machine to **credentials.json**

#### Test Completed Task

Click **Check my progress** to verify your performed task. If you have completed the task successfully you will granted with an assessment score.

Create a Network-admin service account.

### Authorize test-vm and verify permissions

Authorize **test-vm** to use the **Network-admin** service account.

1.  Return to the **SSH** terminal of the **test-vm** instance.

2.  To upload **credentials.json** through the SSH VM terminal, click on the gear icon in the upper-right corner, and then click **Upload file**.

3.  Select **credentials.json** and upload it.

4.  Click **Close** in the File Transfer window.

5.  Authorize the VM with the credentials you just uploaded:

    gcloud auth activate-service-account --key-file credentials.json

The image you are using has the Cloud SDK pre-installed; therefore, you don’t need to initialize the Cloud SDK. If you are attempting this lab in a different environment, make sure you have followed the [procedures regarding installing the Cloud SDK](https://cloud.google.com/sdk/downloads).



1.  Try to list the available firewall rules:

    gcloud compute firewall-rules list

The output should look like this (**do not copy; this is example output**):

    NAME                    NETWORK  DIRECTION  PRIORITY  ALLOW     DENY
    allow-http-web-server   default  INGRESS    1000      tcp:80
    default-allow-icmp      default  INGRESS    65534     icmp
    default-allow-internal  default  INGRESS    65534     all
    default-allow-rdp       default  INGRESS    65534     tcp:3389
    default-allow-ssh       default  INGRESS    65534     tcp:22

This should work!

1.  Try to delete the **allow-http-web-server** firewall rule:

    gcloud compute firewall-rules delete allow-http-web-server

1.  Enter **Y**, if asked to continue.

The output should look like this (**do not copy; this is example output**):

    ERROR: (gcloud.compute.firewall-rules.delete) Could not fetch resource:
     - Required 'compute.firewalls.delete' permission for 'projects/[PROJECT_ID]/global/firewalls/allow-http-web-server'

This should not work!

As expected, the **Network Admin** role has permissions to list but not modify/delete firewall rules.



### Update service account and verify permissions

Update the **Network-admin** service account by providing it the **Security Admin** role.

1.  In the Console, navigate to **Navigation menu** (![mainmenu.png](https://cdn.qwiklabs.com/QRLlvUpflhvp875q7fi7Mf%2FiVCqtMcM10Vfn0vwKZ%2B4%3D)) > **IAM & admin** > **IAM**.

2.  Find the **Network-admin** account. Focus on the **Name** column to identify this account.

3.  Click on the pencil icon for the **Network-admin** account.

4.  Change **Role** to **Compute Engine > Compute Security Admin**.

5.  Click **Save**.

6.  Return to the **SSH** terminal of the **test-vm** instance.

7.  Try to list the available firewall rules:

    gcloud compute firewall-rules list

The output should look like this (**do not copy; this is example output**):

    NAME                    NETWORK  DIRECTION  PRIORITY  ALLOW     DENY
    allow-http-web-server   default  INGRESS    1000      tcp:80
    default-allow-icmp      default  INGRESS    65534     icmp
    default-allow-internal  default  INGRESS    65534     all
    default-allow-rdp       default  INGRESS    65534     tcp:3389
    default-allow-ssh       default  INGRESS    65534     tcp:22

This should work!

1.  Try to delete the **allow-http-web-server** firewall rule:

    gcloud compute firewall-rules delete allow-http-web-server

1.  Enter **Y**, if asked to continue.

The output should look like this (**do not copy; this is example output**):

    Deleted [https://www.googleapis.com/compute/v1/projects/qwiklabs-gcp-00e186e4b1cec086/global/firewalls/allow-http-web-server].

This should work!

As expected, the **Security Admin** role has permissions to list and delete firewall rules.



### Verify the deletion of the firewall rule

Verify that you can no longer HTTP access the external IP of the **blue** server, because you deleted the **allow-http-web-server** firewall rule.

1.  Return to the **SSH** terminal of the **test-vm** instance.

2.  To test HTTP connectivity to **blue**'s external IP, run the following command, replacing **blue**'s external IP:

    curl -c 3 <Enter blue's external IP here>

This should not work!



1.  Press **CTRL+c** to stop the HTTP request.

Provide the **Security Admin** role to the right user or service account to avoid any unwanted changes to your firewall rules!



## Congratulations!

In this lab, you created two nginx web servers and controlled external HTTP access using a tagged firewall rule. Then, you created a service account with first the **Network Admin** role and then the **Security Admin** role to explore the different permissions of these roles.

If your company has a security team that manages firewalls and SSL certificates and a networking team that manages the rest of the networking resources, then grant the security team the **Security Admin** role and the networking team the **Network Admin** role.

![Networking_125.png](https://cdn.qwiklabs.com/3bkldo7J0N94gjkFCX8%2BeN7N%2BSnaVXjJk%2FZRJCySM7U%3D)

### Finish Your Quest

This self-paced lab is part of the Qwiklabs Quest, [Networking in the Google Cloud](https://google.qwiklabs.com/quests/31). A Quest is a series of related labs that form a learning path. Completing this Quest earns you the badge above to recognize your achievement. You can make your badge (or badges) public and link to them in your online resume or social media account. Enroll in this Quest and get immediate completion credit if you've taken this lab. [See other available Qwiklabs Quests](https://google.qwiklabs.com/catalog).

### Take Your Next Lab

*   [Service Accounts and Roles: Fundamentals](https://google.qwiklabs.com/catalog_lab/956)

*   [Cloud Security Scanner: Qwik Start](https://www.qwiklabs.com/catalog_lab/1066)

### Next Steps / Learn More

For information on the basic concepts of Google Cloud Identity and Access Management:

*   [Google Cloud Identity and Access Management Overview](https://cloud.google.com/iam/docs/overview)

### Google Cloud Training & Certification

...helps you make the most of Google Cloud technologies. [Our classes](https://cloud.google.com/training/courses) include technical skills and best practices to help you get up to speed quickly and continue your learning journey. We offer fundamental to advanced level training, with on-demand, live, and virtual options to suit your busy schedule. [Certifications](https://cloud.google.com/certification/) help you validate and prove your skill and expertise in Google Cloud technologies.

##### Manual Last Updated December 2, 2020

##### Lab Last Tested December 2, 2020

Copyright 2020 Google LLC All rights reserved. Google and the Google logo are trademarks of Google LLC. All other company and product names may be trademarks of the respective companies with which they are associated.


#### Ready for more?

Here's another lab we think you'll like.

In this lab you train, evaluate, and deploy a machine learning model to predict a baby’s weight. You then send requests to the model to make online predictions. This lab is part of a series of labs on processing scientific data.

