# Create an Internal Load Balancer

## GSP216

![Google Cloud Self-Paced Labs](https://cdn.qwiklabs.com/GMOHykaqmlTHiqEeQXTySaMXYPHeIvaqa2qHEzw6Occ%3D)

## Overview

Google Cloud offers Internal Load Balancing for your TCP/UDP-based traffic. Internal Load Balancing enables you to run and scale your services behind a private load balancing IP address that is accessible only to your internal virtual machine instances.

In this lab, you create two managed instance groups in the same region. Then, you configure and test an Internal Load Balancer with the instances groups as the backends, as shown in this network diagram:

![Network_Diagram.png](https://cdn.qwiklabs.com/k3u04mphJhk%2F2yM84NjgPiZHrbCuzbdwAQ98vnaoHQo%3D)

### Objectives

In this lab you learn how to perform the following tasks:

*   Create HTTP and health check firewall rules

*   Configure two instance templates

*   Create two managed instance groups

*   Configure and test an internal load balancer

## Setup and requirements

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

## Configure HTTP and health check firewall rules

Configure firewall rules to allow HTTP traffic to the backends and TCP traffic from the Google Cloud health checker.

### **Explore the my-internal-app network**

The network `my-internal-app` with subnet-a and subnet-b along with firewall rules for RDP, SSH, and ICMP traffic have been configured for you.

1.  In the Console, navigate to **Navigation menu** > **VPC network** > **VPC networks**.

![nav_vpc.png](https://cdn.qwiklabs.com/NwwYCb1%2BkDLl4tlW9V7FqPN%2FFJrQcBZhJXkvvhIha0E%3D)

1.  Notice the **my-internal-app** network with its subnets: **subnet-a** and **subnet-b**

    Each Google Cloud project starts with the **default** network. In addition, the **my-internal-app** network has been created for you, as part of your network diagram.

    You will create the managed instance groups in **subnet-a** and **subnet-b**. Both subnets are in the **us-central1** region because an Internal Load Balancer is a regional service. The managed instance groups will be in different zones, making your service immune to zonal failures.

### **Create the HTTP firewall rule**

Create a firewall rule to allow HTTP traffic to the backends from the Load Balancer and the internet (to install Apache on the backends).

1.  Still in **VPC network**, in the left pane click **Firewall**.

![left-pane.png](https://cdn.qwiklabs.com/wxuL1ePKCRMYwhdpXDuYllwcalpvD9eLvwqxCMycn%2B4%3D)

1.  Notice the **app-allow-icmp** and **app-allow-ssh-rdp** firewall rules.

    These firewall rules have been created for you.

2.  Click **Create Firewall Rule**.

3.  Set the following values, leave all other values at their defaults:

    <table>
    <tbody>
    <tr>
    <th>Property</th>
    <th>Value (type value or select option as specified)</th>
    </tr>
    <tr>
    <td>Name</td>
    <td>app-allow-http</td>
    </tr>
    <tr>
    <td>Network</td>
    <td>my-internal-app</td>
    </tr>
    <tr>
    <td>Targets</td>
    <td>Specified target tags</td>
    </tr>
    <tr>
    <td>Target tags</td>
    <td>lb-backend</td>
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
    <td>Specified protocols and ports, and then _check_ tcp, _type:_ 80</td>
    </tr>
    </tbody>
    </table>

Make sure to include the **/0** in the **Source IP ranges** to specify all networks.

1.  Click **Create**.

### **Create the health check firewall rules**

Health checks determine which instances of a Load Balancer can receive new connections. For Internal load balancing, the health check probes to your load balanced instances come from addresses in the ranges `130.211.0.0/22` and `35.191.0.0/16`. Your firewall rules must allow these connections.

1.  Still in the **Firewall rules** page, click **Create Firewall Rule**.

2.  Set the following values, leave all other values at their defaults:

    <table>
    <tbody>
    <tr>
    <th>Property</th>
    <th>Value (type value or select option as specified)</th>
    </tr>
    <tr>
    <td>Name</td>
    <td>app-allow-health-check</td>
    </tr>
    <tr>
    <td>Targets</td>
    <td>Specified target tags</td>
    </tr>
    <tr>
    <td>Target tags</td>
    <td>lb-backend</td>
    </tr>
    <tr>
    <td>Source filter</td>
    <td>IP Ranges</td>
    </tr>
    <tr>
    <td>Source IP ranges</td>
    <td>130.211.0.0/22 35.191.0.0/16</td>
    </tr>
    <tr>
    <td>Protocols and ports</td>
    <td>Specified protocols and ports, and then _check_ tcp</td>
    </tr>
    </tbody>
    </table>

Make sure to enter the two **Source IP ranges** one-by-one and pressing SPACE in between them.

1.  Click **Create**.

Click Check my progress to verify the objective.

Configure HTTP and health check firewall rules

## Configure instance templates and create instance groups

A managed instance group uses an instance template to create a group of identical instances. Use these to create the backends of the Internal Load Balancer.

### **Configure the instance templates**

An instance template is an API resource that you can use to create VM instances and managed instance groups. Instance templates define the machine type, boot disk image, subnet, labels, and other instance properties. Create an instance template for both subnets of the **my-internal-app** network.

1.  In the Console, navigate to **Navigation menu** > **Compute Engine** > **Instance templates**.
2.  Click **Create instance template**.
3.  For **Name**, type **instance-template-1**.
4.  For **Series**, select **N1**.
5.  Click **Management, security, disks, networking, sole tenancy**.

![management-security.png](https://cdn.qwiklabs.com/ip3uH4PrKs1%2BAlGm23bLaP9zwWua2AiuABosu2PRwUg%3D)

1.  Click **Management**.

2.  Under **Metadata**, specify the following:

    <table>
    <tbody>
    <tr>
    <th>Key</th>
    <th>Value</th>
    </tr>
    <tr>
    <td>startup-script-url</td>
    <td>gs://cloud-training/gcpnet/ilb/startup.sh</td>
    </tr>
    </tbody>
    </table>

The **startup-script-url** specifies a script that will be executed when instances are started. This script installs Apache and changes the welcome page to include the client IP and the name, region and zone of the VM instance. Feel free to explore this script [here](https://storage.googleapis.com/cloud-training/gcpnet/ilb/startup.sh).

1.  Click **Networking**.

2.  For **Network interfaces**, set the following values, leave all other values at their defaults:

    <table>
    <tbody>
    <tr>
    <th>Property</th>
    <th>Value (type value or select option as specified)</th>
    </tr>
    <tr>
    <td>Network</td>
    <td>my-internal-app</td>
    </tr>
    <tr>
    <td>Subnetwork</td>
    <td>subnet-a</td>
    </tr>
    <tr>
    <td>Network tags</td>
    <td>lb-backend</td>
    </tr>
    </tbody>
    </table>

The network tag **lb-backend** ensures that the **HTTP** and **Health Check** firewall rules apply to these instances.

1.  Click **Create**.
2.  Wait for the instance template to be created.

### Configure the next instance template

Create another instance template for **subnet-b** by copying **instance-template-1**:

1.  Still in **Instance templates**, select **instance-template-1** and click **Copy**.
2.  Click **Management, security, disks, networking, sole tenancy**.
3.  Click **Networking**.
4.  For **Network interfaces**, select **subnet-b** as the **Subnetwork**.
5.  Click **Create**.

### **Create the managed instance groups**

Create a managed instance group in **subnet-a** (us-central1-a) and one **subnet-b** (us-central1-b).

1.  Still in **Compute Engine**, in the left pane click **Instance groups**, and then click **Create Instance group**.
2.  Set the following values, leave all other values at their defaults:

    <table>
    <tbody>
    <tr>
    <th>Property</th>
    <th>Value (type value or select option as specified)</th>
    </tr>
    <tr>
    <td>Name</td>
    <td>instance-group-1</td>
    </tr>
    <tr>
    <td>Location</td>
    <td>Single-zone</td>
    </tr>
    <tr>
    <td>Region</td>
    <td>us-central1</td>
    </tr>
    <tr>
    <td>Zone</td>
    <td>us-central1-a</td>
    </tr>
    <tr>
    <td>Instance template</td>
    <td>instance-template-1</td>
    </tr>
    <tr>
    <td>Autoscaling > Autoscaling metrics > Click Pencil icon > Metric type</td>
    <td>CPU utilization</td>
    </tr>
    <tr>
    <td>Target CPU utilization</td>
    <td>80</td>
    </tr>
    <tr>
    <td>Minimum number of instances</td>
    <td>1</td>
    </tr>
    <tr>
    <td>Maximum number of instances</td>
    <td>5</td>
    </tr>
    <tr>
    <td>Cool-down period</td>
    <td>45</td>
    </tr>
    </tbody>
    </table>

Managed instance groups offer **autoscaling** capabilities that allow you to automatically add or remove instances from a managed instance group based on increases or decreases in load. Autoscaling helps your applications gracefully handle increases in traffic and reduces cost when the need for resources is lower. You just define the autoscaling policy and the autoscaler performs automatic scaling based on the measured load.

1.  Click **Create**.

    Repeat the same procedure for **instance-group-2** in **us-central1-b**:

2.  Click **Create Instance group**.

3.  Set the following values, leave all other values at their defaults:

    <table>
    <tbody>
    <tr>
    <th>Property</th>
    <th>Value (type value or select option as specified)</th>
    </tr>
    <tr>
    <td>Name</td>
    <td>instance-group-2</td>
    </tr>
    <tr>
    <td>Location</td>
    <td>Single-zone</td>
    </tr>
    <tr>
    <td>Region</td>
    <td>us-central1</td>
    </tr>
    <tr>
    <td>Zone</td>
    <td>us-central1-b</td>
    </tr>
    <tr>
    <td>Instance template</td>
    <td>instance-template-2</td>
    </tr>
    <tr>
    <td>Autoscaling > Autoscaling metrics > Click Pencil icon > Metric type</td>
    <td>CPU utilization</td>
    </tr>
    <tr>
    <td>Target CPU utilization</td>
    <td>80</td>
    </tr>
    <tr>
    <td>Minimum number of instances</td>
    <td>1</td>
    </tr>
    <tr>
    <td>Maximum number of instances</td>
    <td>5</td>
    </tr>
    <tr>
    <td>Cool-down period</td>
    <td>45</td>
    </tr>
    </tbody>
    </table>

4.  Click **Create**.

### **Verify the backends**

Verify that VM instances are being created in both subnets and create a utility VM to access the backends' HTTP sites.

1.  Still in **Compute Engine**, click **VM instances**.

2.  Notice two instances that start with `instance-group-1` and `instance-group-2`.

    These instances are in separate zones and their internal IP addresses are part of the **subnet-a** and **subnet-b** CIDR blocks.

3.  Click **Create an instance**.

4.  Set the following values, leave all other values at their defaults:

    <table>
    <tbody>
    <tr>
    <th>Property</th>
    <th>Value (type value or select option as specified)</th>
    </tr>
    <tr>
    <td>Name</td>
    <td>utility-vm</td>
    </tr>
    <tr>
    <td>Region</td>
    <td>us-central1</td>
    </tr>
    <tr>
    <td>Zone</td>
    <td>us-central1-f</td>
    </tr>
    <tr>
    <td>Series</td>
    <td>N1</td>
    </tr>
    <tr>
    <td>Machine type</td>
    <td>f1-micro (1 shared vCPU)</td>
    </tr>
    </tbody>
    </table>

5.  Click **Management, security, disks, networking, sole tenancy**.

6.  Click **Networking**.

7.  For **Network interfaces**, click the pencil icon to edit.

8.  Set the following values, leave all other values at their defaults:

    <table>
    <tbody>
    <tr>
    <th>Property</th>
    <th>Value (type value or select option as specified)</th>
    </tr>
    <tr>
    <td>Network</td>
    <td>my-internal-app</td>
    </tr>
    <tr>
    <td>Subnetwork</td>
    <td>subnet-a</td>
    </tr>
    <tr>
    <td>Primary internal IP</td>
    <td>Ephemeral (Custom)</td>
    </tr>
    <tr>
    <td>Custom ephemeral IP address</td>
    <td>10.10.20.50</td>
    </tr>
    </tbody>
    </table>

9.  Click **Create**.

![network_interface.png](https://cdn.qwiklabs.com/E0C6OR6xKOXnz3EAsMb8oP4sbv0FpmgciXDTpIhoGXc%3D)

Click Check my progress to verify the objective.

Configure instance templates and create instance groups

1.  Note that the internal IP addresses for the backends are `10.10.20.2` and `10.10.30.2`.
If these IP addresses are different, replace them in the two **curl** commands below.

1.  For **utility-vm**, click **SSH** to launch a terminal and connect.

2.  To verify the welcome page for `instance-group-1-xxxx`, run the following command:

    curl 10.10.20.2

The output should look like this (**do not copy; this is example output**):

    <h1>Internal Load Balancing Lab</h1><h2>Client IP</h2>Your IP address : 10.10.20.50<h2>Hostname</h2>Server Hostname:
     instance-group-1-1zn8<h2>Server Location</h2>Region and Zone: us-central1-a

1.  To verify the welcome page for `instance-group-2-xxxx`, run the following command:

    curl 10.10.30.2

The output should look like this (**example output**):

    <h1>Internal Load Balancing Lab</h1><h2>Client IP</h2>Your IP address : 10.10.20.50<h2>Hostname</h2>Server Hostname:
     instance-group-2-q5wp<h2>Server Location</h2>Region and Zone: us-central1-b

The **curl** commands demonstrate that each VM instance lists the Client IP and its own name and location. This will be useful when verifying that the Internal Load Balancer sends traffic to both backends.

1.  Close the SSH terminal to **utility-vm**:

    exit

## Configure the Internal Load Balancer

Configure the Internal Load Balancer to balance traffic between the two backends (**instance-group-1** in us-central1-a and **instance-group-2** in us-central1-b), as illustrated in this diagram:

![Network_Diagram.png](https://cdn.qwiklabs.com/k3u04mphJhk%2F2yM84NjgPiZHrbCuzbdwAQ98vnaoHQo%3D)

### **Start the configuration**

1.  In the Cloud Console, navigate to **Navigation menu** (![mainmenu.png](https://cdn.qwiklabs.com/QRLlvUpflhvp875q7fi7Mf%2FiVCqtMcM10Vfn0vwKZ%2B4%3D)) > **Network Services** > **Load balancing**, and then click **Create load balancer**.
2.  Under **TCP Load Balancing**, click on **Start configuration**.
3.  For **Internet facing or internal only**, select **Only between my VMs**.

Choosing **Only between my VMs** makes this Load Balancer internal. This choice requires the backends to be in a single region (us-central1) and does not allow offloading TCP processing to the Load Balancer.



1.  Click **Continue**.

2.  For **Name**, type `my-ilb`.

### **Configure the regional backend service**

The backend service monitors instance groups and prevents them from exceeding configured usage.

1.  Click on **Backend configuration**.

2.  Set the following values, leave all other values at their defaults:

    <table>
    <tbody>
    <tr>
    <th>Property</th>
    <th>Value (select option as specified)</th>
    </tr>
    <tr>
    <td>Region</td>
    <td>us-central1</td>
    </tr>
    <tr>
    <td>Network</td>
    <td>my-internal-app</td>
    </tr>
    <tr>
    <td>Instance group</td>
    <td>instance-group-1 (us-central1-a)</td>
    </tr>
    </tbody>
    </table>

3.  Click **Add backend**.

4.  For **Instance group**, select **instance-group-2 (us-central1-b)**.

5.  For **Health Check**, select **Create a health check**.

6.  Set the following values, leave all other values at their defaults:

    <table>
    <tbody>
    <tr>
    <th>Property</th>
    <th>Value (select option as specified)</th>
    </tr>
    <tr>
    <td>Name</td>
    <td>my-ilb-health-check</td>
    </tr>
    <tr>
    <td>Protocol</td>
    <td>TCP</td>
    </tr>
    <tr>
    <td>Port</td>
    <td>80</td>
    </tr>
    </tbody>
    </table>

Health checks determine which instances can receive new connections. This HTTP health check polls instances every 5 seconds, waits up to 5 seconds for a response and treats 2 successful or 2 failed attempts as healthy or unhealthy, respectively.

1.  Click **Save and Continue**.

2.  Verify that there is a blue check mark next to **Backend configuration** in the Cloud Console. If not, double-check that you have completed all the steps above.

### **Configure the frontend**

The frontend forwards traffic to the backend.

1.  Click on **Frontend configuration**.

2.  Specify the following, leaving all other values with their defaults:

    <table>
    <tbody>
    <tr>
    <th>Property</th>
    <th>Value (type value or select option as specified)</th>
    </tr>
    <tr>
    <td>Subnetwork</td>
    <td>subnet-b</td>
    </tr>
    <tr>
    <td>Internal IP</td>
    <td>Reserve a static internal IP address</td>
    </tr>
    </tbody>
    </table>

3.  Specify the following, leaving all other values with their defaults:

    <table>
    <tbody>
    <tr>
    <th>Property</th>
    <th>Value (type value or select option as specified)</th>
    </tr>
    <tr>
    <td>Name</td>
    <td>my-ilb-ip</td>
    </tr>
    <tr>
    <td>Static IP address</td>
    <td>Let me choose</td>
    </tr>
    <tr>
    <td>Custom IP address</td>
    <td>10.10.30.5</td>
    </tr>
    </tbody>

    </table>

4.  Click **Reserve**.

5.  For **Ports**, type `80`.

6.  Click **Done**.

### **Review and create the Internal Load Balancer**

1.  Click on **Review and finalize**.
2.  Review the **Backend** and **Frontend**.

![review_and_finalize.png](https://cdn.qwiklabs.com/xhvBHyYfU4G4Od6k2TYUnPPPhAcRJewkbOR%2FT%2BAUXhI%3D)

1.  Click on **Create**. Wait for the Load Balancer to be created, before moving to the next task.

Click Check my progress to verify the objective.

<ql-activity-tracking step="3">Configure the Internal Load Balancer</ql-activity-tracking>

## Test the Internal Load Balancer

Verify that the `my-ilb` IP address forwards traffic to **instance-group-1** in us-central1-a and **instance-group-2** in us-central1-b.

### **Access the Internal Load Balancer**

1.  In the Console, navigate to **Navigation menu** > **Compute Engine** > **VM instances**.

2.  For **utility-vm**, click **SSH** to launch a terminal and connect.

3.  To verify that the Internal Load Balancer forwards traffic, run the following command:

    curl 10.10.30.5

The output should look like this (**example output**):

    <h1>Internal Load Balancing Lab</h1><h2>Client IP</h2>Your IP address : 10.10.20.50<h2>Hostname</h2>Server Hostname:
     instance-group-1-1zn8<h2>Server Location</h2>Region and Zone: us-central1-a

As expected, traffic is forwarded from the Internal Load Balancer (10.10.30.5) to the backend.



1.  Run the same command a couple more times.

You should be able to see responses from **instance-group-1** in us-central1-a and **instance-group-2** in us-central1-b.

![2_groups.png](https://cdn.qwiklabs.com/oJQI21BKI%2BJEe3t0FBKnmBQ5vBNBdvl9BmiksgjG57w%3D)

## Congratulations!

In this lab you created two managed instance groups in the us-central1 region, along with firewall rules to allow HTTP traffic to those instances and TCP traffic from the Google Cloud health checker. Then, you configured and tested an Internal Load Balancer for those instance groups.

![Networking_125.png](https://cdn.qwiklabs.com/3bkldo7J0N94gjkFCX8%2BeN7N%2BSnaVXjJk%2FZRJCySM7U%3D)

### Finish Your Quest

This self-paced lab is part of the Qwiklabs Quest, [Networking in the Google Cloud](https://google.qwiklabs.com/quests/31). A Quest is a series of related labs that form a learning path. Completing this Quest earns you the badge above to recognize your achievement. You can make your badge (or badges) public and link to them in your online resume or social media account. [Enroll in this Quest](https://google.qwiklabs.com/learning_paths/31/enroll) and get immediate completion credit if you've taken this lab. [See other available Qwiklabs Quests](https://google.qwiklabs.com/catalog).

### Take Your Next Lab

Continue your Quest with [Dynamic VPN Gateways - Cloud Routers](https://google.qwiklabs.com/catalog_lab/1036), or check out these suggestions:

*   [Deployment Manager - Adding Load Balancing](https://google.qwiklabs.com/catalog_lab/1235)

*   [Create a Network Load-Balanced Logbook Application](https://google.qwiklabs.com/catalog_lab/1149)

### Next Steps / Learn More

For information on the basic concepts of Load Balancing, see [Google Cloud Load Balancing Documentation](https://cloud.google.com/load-balancing/docs/).

##### Manual Last Updated November 27, 2020

##### Lab Last Tested November 27, 2020

Copyright 2020 Google LLC All rights reserved. Google and the Google logo are trademarks of Google LLC. All other company and product names may be trademarks of the respective companies with which they are associated.
