# HTTP Load Balancer with Cloud Armor

<div class="lab-preamble__details subtitle-headline-1"><span>1 hour</span> <span>7 Credits</span>

</div>

</div>

<div class="js-markdown-instructions markdown-lab-instructions" id="markdown-lab-instructions">

## GSP215

![Google Cloud Self-Paced Labs](https://cdn.qwiklabs.com/GMOHykaqmlTHiqEeQXTySaMXYPHeIvaqa2qHEzw6Occ%3D)

## Overview

Google Cloud HTTP(S) load balancing is implemented at the edge of Google's network in Google's points of presence (POP) around the world. User traffic directed to an HTTP(S) load balancer enters the POP closest to the user and is then load balanced over Google's global network to the closest backend that has sufficient capacity available.

Cloud Armor IP allowlist/denylist enable you to restrict or allow access to your HTTP(S) load balancer at the edge of the Google Cloud, as close as possible to the user and to malicious traffic. This prevents malicious users or traffic from consuming resources or entering your virtual private cloud (VPC) networks.

In this lab, you configure an HTTP Load Balancer with global backends, as shown in the diagram below. Then, you stress test the Load Balancer and denylist the stress test IP with Cloud Armor.

![network_diagram.png](https://cdn.qwiklabs.com/7wJtCqbfTFLwKCpOMzUSyPjVKBjUouWHbduOqMpfRiM%3D)

### Objectives

In this lab, you learn how to perform the following tasks:

*   Create HTTP and health check firewall rules
*   Configure two instance templates
*   Create two managed instance groups
*   Configure an HTTP Load Balancer with IPv4 and IPv6
*   Stress test an HTTP Load Balancer
*   Denylist an IP address to restrict access to an HTTP Load Balancer

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

    <aside>If you see the **Choose an account** page, click **Use Another Account**. ![Choose an account](https://cdn.qwiklabs.com/eQ6xPnPn13GjiJP3RWlHWwiMjhooHxTNvzfg1AL2WPw%3D)</aside>

3.  In the **Sign in** page, paste the username that you copied from the Connection Details panel. Then copy and paste the password.

    **_Important:_** You must use the credentials from the Connection Details panel. Do not use your Qwiklabs credentials. If you have your own Google Cloud account, do not use it for this lab (avoids incurring charges).

4.  Click through the subsequent pages:

    *   Accept the terms and conditions.
    *   Do not add recovery options or two-factor authentication (because this is a temporary account).
    *   Do not sign up for free trials.

After a few moments, the Cloud Console opens in this tab.

<aside>**Note:** You can view the menu with a list of Google Cloud Products and Services by clicking the **Navigation menu** at the top-left. ![Cloud Console Menu](https://cdn.qwiklabs.com/9vT7xPlxoNP%2FPsK0J8j0ZPFB4HnnpaIJVCDByaBrSHg%3D)</aside>

## Configure HTTP and health check firewall rules

Configure firewall rules to allow HTTP traffic to the backends and TCP traffic from the Google Cloud health checker.

### **Create the HTTP firewall rule**

Create a firewall rule to allow HTTP traffic to the backends.

1.  In the Cloud Console, navigate to **Navigation menu** (![mainmenu.png](https://cdn.qwiklabs.com/QRLlvUpflhvp875q7fi7Mf%2FiVCqtMcM10Vfn0vwKZ%2B4%3D)) > **VPC network** > **Firewall**.

![firewall.png](https://cdn.qwiklabs.com/o3ZzeAWb50voTy3ENkYicuiDP9Wen8Sybx83FHz9XhY%3D)

1.  Notice the existing **ICMP**, **internal**, **RDP**, and **SSH** firewall rules.

    Each Google Cloud project starts with the **default** network and these firewall rules.

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
    <td>default-allow-http</td>
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
    <td>http-server</td>
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

<ql-infobox>Make sure to include the **/0** in the **Source IP ranges** to specify all networks.</ql-infobox>

1.  Click **Create**.

### **Create the health check firewall rules**

Health checks determine which instances of a load balancer can receive new connections. For HTTP load balancing, the health check probes to your load balanced instances come from addresses in the ranges `130.211.0.0/22` and `35.191.0.0/16`. Your firewall rules must allow these connections.

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
    <td>default-allow-health-check</td>
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
    <td>http-server</td>
    </tr>
    <tr>
    <td>Source filter</td>
    <td>IP Ranges</td>
    </tr>
    <tr>
    <td>Source IP ranges</td>
    <td>`130.211.0.0/22`, `35.191.0.0/16`</td>
    </tr>
    <tr>
    <td>Protocols and ports</td>
    <td>Specified protocols and ports, and then _check_ tcp</td>
    </tr>
    </tbody>
    </table>

    <ql-infobox>Make sure to enter the two **Source IP ranges** one-by-one and pressing SPACE in between them.</ql-infobox>
3.  Click **Create**.

Click _Check my progress_ to verify the objective. <ql-activity-tracking step="1">Configure HTTP and health check firewall rules</ql-activity-tracking>

## Configure instance templates and create instance groups

A managed instance group uses an instance template to create a group of identical instances. Use these to create the backends of the HTTP Load Balancer.

### **Configure the instance templates**

An instance template is an API resource that you use to create VM instances and managed instance groups. Instance templates define the machine type, boot disk image, subnet, labels, and other instance properties. Create one instance template for **us-east1** and one for **europe-west1**.

1.  In the Cloud Console, navigate to **Navigation menu** (![mainmenu.png](https://cdn.qwiklabs.com/QRLlvUpflhvp875q7fi7Mf%2FiVCqtMcM10Vfn0vwKZ%2B4%3D)) > **Compute Engine** > **Instance templates**, and then click **Create instance template**.
2.  For **Name**, type **us-east1-template**.
3.  For **Series**, select **N1**.
4.  Click **Management, security, disks, networking, sole tenancy**.

![instance_template.png](https://cdn.qwiklabs.com/42bhvUB6JNPt%2BKBTJx4b9dBZlBniQLvUdXbiw8MZKoM%3D)

1.  Click the **Management** tab.

![management_tab.png](https://cdn.qwiklabs.com/iaceYyTDdsPJopiU1w%2Bvz7G7MV%2BORscCE%2FKiycSuAkg%3D)

1.  Under **Metadata**, specify the following:

    <table>
    <tbody>
    <tr>
    <th>Key</th>
    <th>Value</th>
    </tr>
    <tr>
    <td>startup-script-url</td>
    <td>gs://cloud-training/gcpnet/httplb/startup.sh</td>
    </tr>
    </tbody>
    </table>

<ql-infobox>The `startup-script-url` specifies a script that executes when instances are started. This script installs Apache and changes the welcome page to include the client IP and the name, region, and zone of the VM instance. Feel free to explore this script [here](https://storage.googleapis.com/cloud-training/gcpnet/httplb/startup.sh).</ql-infobox>

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
    <td>default</td>
    </tr>
    <tr>
    <td>Subnet</td>
    <td>default (us-east1)</td>
    </tr>
    <tr>
    <td>Network tags</td>
    <td>http-server</td>
    </tr>
    </tbody>
    </table>

<ql-infobox>The network tag **http-server** ensures that the **HTTP** and **Health Check** firewall rules apply to these instances.</ql-infobox>

1.  Click **Create**.
2.  Wait for the instance template to be created.

Now create another instance template for **subnet-b** by copying **us-east1-template**:

1.  Select the **us-east1-template** and click **Copy**.

2.  For **Name**, type **europe-west1-template**.

3.  Click **Management, security, disks, networking, sole tenancy**.

4.  Click **Networking**.

5.  For **Network interfaces**, select **default (europe-west1)** as the **Subnet**.

6.  Click **Create**.

### **Create the managed instance groups**

Create a managed instance group in **us-east1** and one in **europe-west1**.

1.  Still in **Compute Engine**, click **Instance groups** in the left menu.

    ![instance_groups.png](https://cdn.qwiklabs.com/h0wofgXdjiWvhVXUsLAECDnX44ld4rdFVl%2FWO2GfzNw%3D)

2.  Click **Create instance group**.

3.  Set the following values, leave all other values at their defaults:

    <table>
    <tbody>
    <tr>
    <th>Property</th>
    <th>Value (type value or select option as specified)</th>
    </tr>
    <tr>
    <td>Name</td>
    <td>us-east1-mig</td>
    </tr>
    <tr>
    <td>Location</td>
    <td>Multiple zones</td>
    </tr>
    <tr>
    <td>Region</td>
    <td>us-east1</td>
    </tr>
    <tr>
    <td>Instance template</td>
    <td>us-east1-template</td>
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

<ql-infobox>Managed instance groups offer **autoscaling** capabilities that allow you to automatically add or remove instances from a managed instance group based on increases or decreases in load. Autoscaling helps your applications gracefully handle increases in traffic and reduces cost when the need for resources is lower. You just define the autoscaling policy and the autoscaler performs automatic scaling based on the measured load.</ql-infobox>

1.  Click **Done**.

2.  Click **Create**.

Now repeat the same procedure for create a second instance group for **europe-west1-mig** in **europe-west1**:

1.  Click **Create Instance group**.

2.  Set the following values, leave all other values at their defaults:

    <table>
    <tbody>
    <tr>
    <th>Property</th>
    <th>Value (type value or select option as specified)</th>
    </tr>
    <tr>
    <td>Name</td>
    <td>europe-west1-mig</td>
    </tr>
    <tr>
    <td>Location</td>
    <td>Multiple zones</td>
    </tr>
    <tr>
    <td>Region</td>
    <td>europe-west1</td>
    </tr>
    <tr>
    <td>Instance template</td>
    <td>europe-west1-template</td>
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

3.  Click **Done**.

4.  Click **Create**.

Click _Check my progress_ to verify the objective. <ql-activity-tracking step="2">Configure instance templates and instance group</ql-activity-tracking>

### **Verify the backends**

Verify that VM instances are being created in both regions and access their HTTP sites.

1.  Still in **Compute Engine**, click **VM instances** in the left menu.

2.  Notice the instances that start with `us-east1-mig` and `europe-west1-mig`.

    These instances are part of the managed instance groups.

3.  Click on the **External IP** of an instance of `us-east1-mig`.

    You should see the **Client IP** (your IP address), the **Hostname** (starts with `us-east1-mig`) and the **Server Location** (a zone in us-east1).

4.  Click on the **External IP** of an instance of `europe-west1-mig`.

    You should see the **Client IP** (your IP address), the **Hostname** (starts with `europe-west1-mig`) and the **Server Location** (a zone in europe-west1).

    ![backend.png](https://cdn.qwiklabs.com/cB4rkhddQchP1iTAc7xNeF5Bly34SwjtieR406NQM9w%3D)

<ql-infobox>The **Hostname** and **Server Location** identifies where the HTTP Load Balancer sends traffic.</ql-infobox>

## Configure the HTTP Load Balancer

Configure the HTTP Load Balancer to balance traffic between the two backends (**us-east1-mig** in us-east1 and **europe-west1-mig** in europe-west1), as illustrated in the network diagram:

![network_diagram.png](https://cdn.qwiklabs.com/7wJtCqbfTFLwKCpOMzUSyPjVKBjUouWHbduOqMpfRiM%3D)

### **Start the configuration**

1.  In the Cloud Console, click **Navigation menu** (![mainmenu.png](https://cdn.qwiklabs.com/QRLlvUpflhvp875q7fi7Mf%2FiVCqtMcM10Vfn0vwKZ%2B4%3D)) > click **Network Services** > **Load balancing**, and then click **Create load balancer**.

2.  Under **HTTP(S) Load Balancing**, click on **Start configuration**.

    ![start_config.png](https://cdn.qwiklabs.com/b1r6L3DhmSUwRh6HHWN0L%2BMj3SXYZdF8wqWJhdRPlFE%3D)

3.  Select **From Internet to my VMs**, and click **Continue**.

4.  Set the **Name** to `http-lb`.

### **Configure the backend**

Backend services direct incoming traffic to one or more attached backends. Each backend is composed of an instance group and additional serving capacity metadata.

1.  Click on **Backend configuration**.

2.  For **Backend services & backend buckets**, click **Create or select backend services & backend buckets**, then click **Backend services**, and then click **Create a backend service**.

3.  Set the following values, leave all other values at their defaults:

    <table>
    <tbody>
    <tr>
    <th>Property</th>
    <th>Value (select option as specified)</th>
    </tr>
    <tr>
    <td>Name</td>
    <td>http-backend</td>
    </tr>
    <tr>
    <td>Instance group</td>
    <td>us-east1-mig</td>
    </tr>
    <tr>
    <td>Port numbers</td>
    <td>80</td>
    </tr>
    <tr>
    <td>Balancing mode</td>
    <td>Rate</td>
    </tr>
    <tr>
    <td>Maximum RPS</td>
    <td>50</td>
    </tr>
    <tr>
    <td>Capacity</td>
    <td>100</td>
    </tr>
    </tbody>
    </table>

<ql-infobox>This configuration means that the load balancer attempts to keep each instance of **us-east1-mig** at or below 50 requests per second (RPS).</ql-infobox>

1.  Click **Done**.

2.  Click **Add backend**.

3.  Set the following values, leave all other values at their defaults:

    <table>
    <tbody>
    <tr>
    <th>Property</th>
    <th>Value (select option as specified)</th>
    </tr>
    <tr>
    <td>Instance group</td>
    <td>europe-west1-mig</td>
    </tr>
    <tr>
    <td>Port numbers</td>
    <td>80</td>
    </tr>
    <tr>
    <td>Balancing mode</td>
    <td>Utilization</td>
    </tr>
    <tr>
    <td>Maximum backend utilization</td>
    <td>80</td>
    </tr>
    <tr>
    <td>Capacity</td>
    <td>100</td>
    </tr>
    </tbody>
    </table>

<ql-infobox>This configuration means that the load balancer attempts to keep each instance of **europe-west1-mig** at or below 80% CPU utilization.</ql-infobox>

1.  Click **Done**.
2.  For **Health Check**, select **Create a health check**.

![health_check.png](https://cdn.qwiklabs.com/4ak8cQih5SEtXSTrnspw0zooJZm3ZmBoBpEk3KZDz7o%3D)

1.  Set the following values, leave all other values at their defaults:

    <table>
    <tbody>
    <tr>
    <th>Property</th>
    <th>Value (select option as specified)</th>
    </tr>
    <tr>
    <td>Name</td>
    <td>http-health-check</td>
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

<ql-infobox>Health checks determine which instances receive new connections. This HTTP health check polls instances every 5 seconds, waits up to 5 seconds for a response and treats 2 successful or 2 failed attempts as healthy or unhealthy, respectively.</ql-infobox>

1.  Click **Save and Continue**.
2.  Click **Create** to create the backend service.

![create_backend.png](https://cdn.qwiklabs.com/O3aCrf4mUTpnJaZ6XlNAgyTYmfPkrJCw6diNUWvRTd0%3D)

### **Configure the frontend**

The host and path rules determine how your traffic will be directed. For example, you could direct video traffic to one backend and static traffic do another backend. However, you are not configuring the Host and path rules in this lab.

1.  Click on **Frontend configuration**.

2.  Specify the following, leaving all other values at their defaults:

    <table>

    <tbody>

    <tr>

    <th>Property</th>

    <th>Value (type value or select option as specified)</th>

    </tr>

    <tr>

    <td>Protocol</td>

    <td>HTTP</td>

    </tr>

    <tr>

    <td>IP version</td>

    <td>IPv4</td>

    </tr>

    <tr>

    <td>IP address</td>

    <td>Ephemeral</td>

    </tr>

    <tr>

    <td>Port</td>

    <td>80</td>

    </tr>

    </tbody>

    </table>

3.  Click **Done**.

4.  Click **Add Frontend IP and port**.

5.  Specify the following, leaving all other values at their defaults:

    <table>

    <tbody>

    <tr>

    <th>Property</th>

    <th>Value (type value or select option as specified)</th>

    </tr>

    <tr>

    <td>Protocol</td>

    <td>HTTP</td>

    </tr>

    <tr>

    <td>IP version</td>

    <td>IPv6</td>

    </tr>

    <tr>

    <td>IP address</td>

    <td>Ephemeral</td>

    </tr>

    <tr>

    <td>Port</td>

    <td>80</td>

    </tr>

    </tbody>

    </table>

6.  Click **Done**.

<ql-infobox>HTTP(S) load balancing supports both IPv4 and IPv6 addresses for client traffic. Client IPv6 requests are terminated at the global load balancing layer, then proxied over IPv4 to your backends.</ql-infobox>

### **Review and create the HTTP Load Balancer**

1.  Click on **Review and finalize**.

![review.png](https://cdn.qwiklabs.com/HYVSJ9uZjtwq%2BA8XqZTdafRPIQrtKZ2E6sfJuakiRFI%3D)

1.  Review the **Backend services** and **Frontend**.

![review_finalize.png](https://cdn.qwiklabs.com/KmuFz6JLlAKYjQq6XUZqxd0kwSJdj94Q41mO%2FqVY4e4%3D)

1.  Click on **Create**.
2.  Wait for the load balancer to be created.
3.  Click on the name of the load balancer (**http-lb**).
4.  Note the IPv4 and IPv6 addresses of the load balancer for the next task. They will be referred to as `[LB_IP_v4]` and `[LB_IP_v6]`, respectively.

<ql-infobox>The IPv6 address is the one in hexadecimal format.</ql-infobox>

Click _Check my progress_ to verify the objective. <ql-activity-tracking step="3">Configure the HTTP Load Balancer</ql-activity-tracking>

## Test the HTTP Load Balancer

Now that you created the HTTP Load Balancer for your backends, verify that traffic is forwarded to the backend service.

<ql-true-false-probe answer="true" stem="The HTTP load balancer should forward traffic to the region that is closest to you."></ql-true-false-probe>

### **Access the HTTP Load Balancer**

To test IPv4 access to the HTTP Load Balancer, open a new tab in your browser and navigate to `http://[LB_IP_v4]`. Make sure to replace `[LB_IP_v4]` with the IPv4 address of the load balancer.

<ql-infobox>It might take up to 5 minutes to access the HTTP Load Balancer. In the meantime, you might get a 404 or 502 error. Keep trying until you see the page of one of the backends.</ql-infobox> <ql-infobox>Depending on your proximity to **us-east1** and **europe-west1**, you traffic is either forwarded to a **us-east1-mig** or **europe-west1-mig** instance.</ql-infobox>

If you have a local IPv6 address, try the IPv6 address of the HTTP Load Balancer by navigating to `http://[LB_IP_v6]`. Make sure to replace `[LB_IP_v6]` with the IPv6 address of the load balancer.

### **Stress test the HTTP Load Balancer**

Create a new VM to simulate a load on the HTTP Load Balancer using `siege`. Then, determine if traffic is balanced across both backends when the load is high.

1.  In the Console, navigate to **Navigation menu** (![mainmenu.png](https://cdn.qwiklabs.com/QRLlvUpflhvp875q7fi7Mf%2FiVCqtMcM10Vfn0vwKZ%2B4%3D)) > **Compute Engine** > **VM instances**.

2.  Click **Create instance**.

3.  Set the following values, leave all other values at their defaults:

    <table>

    <tbody>

    <tr>

    <th>Property</th>

    <th>Value (type value or select option as specified)</th>

    </tr>

    <tr>

    <td>Name</td>

    <td>siege-vm</td>

    </tr>

    <tr>

    <td>Region</td>

    <td>us-west1</td>

    </tr>

    <tr>

    <td>Zone</td>

    <td>us-west1-c</td>

    </tr>

    <tr>

    <td>Series</td>

    <td>N1</td>

    </tr>

    </tbody>

    </table>

<ql-infobox>Given that **us-west1** is closer to **us-east1** than to **europe-west1**, traffic should be forwarded only to **us-east1-mig** (unless the load is too high).</ql-infobox>

1.  Click **Create**.

2.  Wait for the **siege-vm** instance to be created.

3.  For **siege-vm**, click **SSH** to launch a terminal and connect.

4.  Run the following command, to install siege:

    sudo apt-get -y install siege

1.  To store the IPv4 address of the HTTP Load Balancer in an environment variable, run the following command, replacing `[LB_IP_v4]` with the IPv4 address:

    export LB_IP=[LB_IP_v4]

1.  To simulate a load, run the following command:

    siege -c 250 http://$LB_IP

The output should look like this (**do not copy; this is example output**):

    New configuration template added to /home/cloudcurriculumdeveloper/.siege
    Run siege -C to view the current settings in that file
    [alert] Zip encoding disabled; siege requires zlib support to enable it: No such file or directory
    ** SIEGE 4.0.2
    ** Preparing 250 concurrent users for battle.
    The server is now under siege...

1.  In the Cloud Console, on the **Navigation menu** (![mainmenu.png](https://cdn.qwiklabs.com/QRLlvUpflhvp875q7fi7Mf%2FiVCqtMcM10Vfn0vwKZ%2B4%3D)), click **Network Services** > **Load balancing**.
2.  Click **Backends**.
3.  Click **http-backend**.
4.  Monitor the **Frontend Location (Total inbound traffic)** between North America and the two backends for 2 to 3 minutes.

At first, traffic should just be directed to **us-east1-mig** but as the RPS increases, traffic is also directed to **europe-west1-mig**.

![frontend.png](https://cdn.qwiklabs.com/YsNXQ3Hvf12bu7zmL%2B4cxUeGO01%2B4uchVOnaVW1QcMc%3D)

This demonstrates that by default traffic is forwarded to the closest backend but if the load is very high, traffic can be distributed across the backends.

1.  Return to the **SSH** terminal of **siege-vm**.

2.  Press **CTRL+C** to stop siege.

## Denylist the siege-vm

Use Cloud Armor to denylist the **siege-vm** from accessing the HTTP Load Balancer.

### **Create the security policy**

Create a Cloud Armor security policy with a denylist rule for the **siege-vm**.

1.  In the Console, navigate to **Navigation menu** (![mainmenu.png](https://cdn.qwiklabs.com/QRLlvUpflhvp875q7fi7Mf%2FiVCqtMcM10Vfn0vwKZ%2B4%3D)) > **Compute Engine** > **VM instances**.
2.  Note the **External IP** of the **siege-vm**. This will be referred to as `[SIEGE_IP]`.

<ql-infobox>There are ways to identify the external IP address of a client trying to access your HTTP Load Balancer. For example, you could examine traffic captured by [VPC Flow Logs in BigQuery](https://cloud.google.com/vpc/docs/using-flow-logs#exporting_logs_to_bigquery_name_short_pubsub_name_short_and_custom_targets) to determine a high volume of incoming requests.</ql-infobox>

1.  In the Cloud Console, navigate to **Navigation menu** > **Network Security** > **Cloud Armor**.

2.  Click **Create policy**.

3.  Set the following values, leave all other values at their defaults:

    <table>

    <tbody>

    <tr>

    <th>Property</th>

    <th>Value (type value or select option as specified)</th>

    </tr>

    <tr>

    <td>Name</td>

    <td>denylist-siege</td>

    </tr>

    <tr>

    <td>Default rule action</td>

    <td>Allow</td>

    </tr>

    </tbody>

    </table>

4.  Click **Next step**.

5.  Click **Add rule**.

6.  Set the following values, leave all other values at their defaults:

    <table>

    <tbody>

    <tr>

    <th>Property</th>

    <th>Value (type value or select option as specified)</th>

    </tr>

    <tr>

    <td>Condition</td>

    <td>_Enter the SIEGE_IP_</td>

    </tr>

    <tr>

    <td>Action</td>

    <td>Deny</td>

    </tr>

    <tr>

    <td>Deny status</td>

    <td>403 (Forbidden)</td>

    </tr>

    <tr>

    <td>Priority</td>

    <td>1000</td>

    </tr>

    </tbody>

    </table>

7.  Click **Done**.

    ![armor_rule.png](https://cdn.qwiklabs.com/04QuoE6w8oI6uJ3UhskxiQc%2FsEkQ1rHn4vbWeRrOJys%3D)

8.  Click **Next step**.

9.  Click **Add Target**.

10.  For **Type**, select **Load balancer backend service**.

11.  For **Target**, select **http-backend**.

12.  Click **Done**.

13.  Click **Create policy**.

<ql-infobox>Alternatively, you could set the default rule to **Deny** and only allowlist/allow traffic from authorized users/IP addresses.</ql-infobox>

1.  Wait for the policy to be created before moving to the next step.

Click _Check my progress_ to verify the objective. <ql-activity-tracking step="4">Denylist the siege-vm</ql-activity-tracking>

### **Verify the security policy**

Verify that the **siege-vm** cannot access the HTTP Load Balancer.

1.  Return to the **SSH** terminal of **siege-vm**.

2.  To access the load balancer, run the following:

    curl http://$LB_IP

The output should look like this (**do not copy; this is example output**):

    <!doctype html><meta charset="utf-8"><meta name=viewport content="width=device-width, initial-scale=1"><title>403</
    title>403 Forbidden

<ql-infobox>It might take a couple of minutes for the security policy to take affect. If you are able to access the backends, keep trying until you get the **403 Forbidden error**.</ql-infobox>

1.  Open a new tab in your browser and navigate to `http://[LB_IP_v4]`. Make sure to replace `[LB_IP_v4]` with the IPv4 address of the load balancer.

<ql-infobox>You can access the HTTP Load Balancer from your browser because of the default rule to **allow** traffic; however, you cannot access it from the **siege-vm** because of the **deny** rule that you implemented.</ql-infobox>

1.  Back in the SSH terminal of siege-vm, to simulate a load, run the following command:

    siege -c 250 http://$LB_IP

The output should look like this (**do not copy; this is example output**):

    [alert] Zip encoding disabled; siege requires zlib support to enable it
    ** SIEGE 4.0.2
    ** Preparing 250 concurrent users for battle.
    The server is now under siege...

Explore the security policy logs to determine if this traffic is also blocked.

1.  In the Console, navigate to **Navigation menu** > **Network Security** > **Cloud Armor**.
2.  Click **denylist-siege**.
3.  Click **Logs**.
4.  Click **View policy logs**.
5.  On the Logging page, make sure to clear all the text in the **Query preview**. Select resource to **Cloud HTTP Load Balancer** > **http-lb-forwarding-rule** > **http-lb** then click **Add**.
6.  Now click **Run Query**.
7.  Expand a log entry in **Query results**.
8.  Expand **httpRequest**.

The request should be from the **siege-vm** IP address. If not, expand another log entry.

1.  Expand **jsonPayload**.
2.  Expand **enforcedSecurityPolicy**.
3.  Notice that the **configuredAction** is to `DENY` with the **name** `denylist-siege`.

![query-results.png](https://cdn.qwiklabs.com/6Tg8NeDNW1atHww0aGfo4j90xzYQxpmuPovo11xb9Co%3D)

<ql-infobox>Cloud Armor security policies create logs that can be explored to determine when traffic is denied and when it is allowed, along with the source of the traffic.</ql-infobox>

## Congratulations!

You configured an HTTP Load Balancer with backends in us-east1 and europe-west1\. Then, you stress tested the Load Balancer with a VM and denylisted the IP address of that VM with Cloud Armor. You were able to explore the security policy logs to identify why the traffic was blocked.

![Networking_125.png](https://cdn.qwiklabs.com/3bkldo7J0N94gjkFCX8%2BeN7N%2BSnaVXjJk%2FZRJCySM7U%3D)

### Finish Your Quest

This self-paced lab is part of the Qwiklabs [Networking in the Google Cloud](https://google.qwiklabs.com/quests/31) Quest. A Quest is a series of related labs that form a learning path. Completing this Quest earns you the badge above to recognize your achievement. You can make your badge (or badges) public and link to them in your online resume or social media account. [Enroll in this Quest](https://google.qwiklabs.com/learning_paths/31/enroll) and get immediate completion credit if you've taken this lab. [See other available Qwiklabs Quests](https://google.qwiklabs.com/catalog).

### Take Your Next Lab

Continue your Quest with [Create an Internal Load Balancer](https://google.qwiklabs.com/catalog_lab/1034), or check out these suggestions:

*   [Using a Vault on Compute Engine for Secret Management](https://google.qwiklabs.com/catalog_lab/1013)

*   [Continuous Delivery with Jenkins in Kubernetes Engine](https://google.qwiklabs.com/catalog_lab/984)

### Next Steps / Learn More

For information on the basic concepts of Cloud Armor, see [Cloud Armor Documentation](https://cloud.google.com/armor/docs/).

For more information on Load Balancing, see [Load Balancing](https://cloud.google.com/compute/docs/load-balancing/).

##### Manual Last Updated November 27, 2020

##### Lab Last Tested November 27, 2020

Copyright 2020 Google LLC All rights reserved. Google and the Google logo are trademarks of Google LLC. All other company and product names may be trademarks of the respective companies with which they are associated.

</div>

</div>

<div class="hidden js-end-lab-button-container lab-content__end-lab-button"><ql-lab-control-button class="js-end-lab-button" running=""></ql-lab-control-button></div>

<div class="lab-content__renderable-instructions">

<div class="lab-content__recommendation">

#### Ready for more?

Here's another lab we think you'll like.

<div class="card-content-wrapper js-content-card" data-id="480" data-level="Expert" data-name="Predict Baby Weight with TensorFlow on AI Platform" data-type="Lab">[

<div class="card__body">

<div class="overline card--content__type">Hands-On Lab</div>

### Predict Baby Weight with TensorFlow on AI Platform

In this lab you train, evaluate, and deploy a machine learning model to predict a baby’s weight. You then send requests to the model to make online predictions. This lab is part of a series of labs on processing scientific data.

</div>

<div class="card__footer">

<div class="card__footer__left"><span>Expert</span></div>

</div>

](/focuses/607?parent=catalog)</div>

</div>

</div>

</ql-drawer-content><ql-drawer end="" id="outline-drawer" open="" slot="drawer" width="320">

<div class="js-lab-content-outline lab-content__outline">[GSP215](#step1)[Overview](#step2)[Setup and requirements](#step3)[Configure HTTP and health check firewall rules](#step4)[Configure instance templates and create instance groups](#step5)[Configure the HTTP Load Balancer](#step6)[Test the HTTP Load Balancer](#step7)[Denylist the siege-vm](#step8)[Congratulations!](#step9)</div>

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

In this lab, you configure an HTTP Load Balancer with global backends. Then, you stress test the Load Balancer and blacklist the stress test IP with Cloud Armor.

This lab is included in these quests: [Networking in the Google Cloud](/quests/31) , [Build and Secure Networks in Google Cloud](/quests/128). If you complete this lab you'll receive credit for it when you enroll in one of these quests.

**Duration:** 0m setup · 60m access · 60m completion

<span>**Levels:** advanced</span>

**Permalink:** [https://google.qwiklabs.com/catalog_lab/1016](https://google.qwiklabs.com/catalog_lab/1016)

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

<div class="controls"><input class="hidden" type="hidden" value="1016" name="lab_review[lab_id]" id="lab_review_lab_id"></div>

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

<div class="lab-access-modal"><input type="hidden" name="id" id="id" value="1232"> <input type="hidden" name="user_id" id="user_id" value="4061609"> <input type="hidden" name="launch_with_credits" id="launch_with_credits" value="0" class="js-launch-with-credits-input"> <input type="hidden" name="launch_with_subs" id="launch_with_subs" value="0" class="js-launch-with-subscription-input">

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