<div class="lab-preamble">

# Multiple VPC Networks

<div class="lab-preamble__details subtitle-headline-1"><span>1 hour 10 minutes</span> <span>7 Credits</span>

<div class="lab__rating">[](/focuses/1230/reviews?parent=catalog)<a data-target="#lab-review-modal" data-toggle="modal">Rate Lab</a></div>

</div>

</div>

<div class="js-markdown-instructions markdown-lab-instructions" id="markdown-lab-instructions">

## GSP211

![Google Cloud Self-Paced Labs](https://cdn.qwiklabs.com/GMOHykaqmlTHiqEeQXTySaMXYPHeIvaqa2qHEzw6Occ%3D)

## Overview

In this lab you create several VPC networks and VM instances and test connectivity across networks. Specifically, you create two custom mode networks (**managementnet** and **privatenet**) with firewall rules and VM instances as shown in this network diagram:

![211-diagram.png](https://cdn.qwiklabs.com/OBtRY37ZCmWiHi%2FHsG8XCSGDBfsuKk3IMJVgQscsg2E%3D)

The **mynetwork** network with its firewall rules and two VM instances (**mynet-eu-vm** and **mynet-us-vm**) have already been created for you in this Qwiklabs project.

### Objectives

In this lab, you will learn how to perform the following tasks:

*   Create custom mode VPC networks with firewall rules
*   Create VM instances using Compute Engine
*   Explore the connectivity for VM instances across VPC networks
*   Create a VM instance with multiple network interfaces

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

## Create custom mode VPC networks with firewall rules

Create two custom networks **managementnet** and **privatenet**, along with firewall rules to allow **SSH**, **ICMP**, and **RDP** ingress traffic.

### Create the managementnet network

Create the **managementnet** network using the Cloud Console.

1.  In the Cloud Console, navigate to **Navigation menu** (![mainmenu.png](https://cdn.qwiklabs.com/QRLlvUpflhvp875q7fi7Mf%2FiVCqtMcM10Vfn0vwKZ%2B4%3D)) > **VPC network** > **VPC networks**.

![navmenu_vpcnetworks.png](https://cdn.qwiklabs.com/kHFA7kqMeawU3GQ0qfj29u%2B1hGieMSEZWl6Su0S1Ry4%3D)

1.  Notice the **default** and **mynetwork** networks with their subnets.

    Each Google Cloud project starts with the **default** network. In addition, the **mynetwork** network has been premade as part of your network diagram.

2.  Click **Create VPC Network**.

3.  Set the **Name** to `managementnet`.

4.  For **Subnet creation mode**, click **Custom**.

5.  Set the following values, leave all other values at their defaults:

    <table>

    <tbody>

    <tr>

    <th>Property</th>

    <th>Value (type value or select option as specified)</th>

    </tr>

    <tr>

    <td>Name</td>

    <td>managementsubnet-us</td>

    </tr>

    <tr>

    <td>Region</td>

    <td>us-central1</td>

    </tr>

    <tr>

    <td>IP address range</td>

    <td>10.130.0.0/20</td>

    </tr>

    </tbody>

    </table>

6.  Click **Done**.

7.  Click **command line**.

    ![create_subnet.png](https://cdn.qwiklabs.com/WEGrd5zpLB1JkgbtDzxadKeRAO%2FWkYpH5RKEfF%2Bxp%2FY%3D)

    These commands illustrate that networks and subnets can be created using the Cloud Shell command line. You will create the **privatenet** network using these commands with similar parameters.

8.  Click **Close**.

9.  Click **Create**.

#### Test Completed Task

Click **Check my progress** to verify your performed task. If you have successfully created a managementnet network, you will see an assessment score.

<ql-activity-tracking step="1">Create the managementnet network</ql-activity-tracking>

### **Create the privatenet network**

Create the **privatenet** network using the Cloud Shell command line.

1.  Run the following command to create the **privatenet** network:

    gcloud compute networks create privatenet --subnet-mode=custom

1.  Run the following command to create the **privatesubnet-us** subnet:

    gcloud compute networks subnets create privatesubnet-us --network=privatenet --region=us-central1 --range=172.16.0.0/24

1.  Run the following command to create the **privatesubnet-eu** subnet:

    gcloud compute networks subnets create privatesubnet-eu --network=privatenet --region=europe-west1 --range=172.20.0.0/20

#### Test Completed Task

Click **Check my progress** to verify your performed task. If you have successfully created a privatenet network, you will see an assessment score.

<ql-activity-tracking step="2">Create the privatenet network</ql-activity-tracking>

1.  Run the following command to list the available VPC networks:

    gcloud compute networks list

The output should look like this (**do not copy; this is example output**):

    NAME           SUBNET_MODE  BGP_ROUTING_MODE  IPV4_RANGE  GATEWAY_IPV4
    default        AUTO         REGIONAL
    managementnet  CUSTOM       REGIONAL
    mynetwork      AUTO         REGIONAL
    privatenet     CUSTOM       REGIONAL

<ql-infobox>**default** and **mynetwork** are auto mode networks, whereas, **managementnet** and **privatenet** are custom mode networks. Auto mode networks create subnets in each region automatically, while custom mode networks start with no subnets, giving you full control over subnet creation</ql-infobox>

1.  Run the following command to list the available VPC subnets (sorted by VPC network):

    gcloud compute networks subnets list --sort-by=NETWORK

The output should look like this (**do not copy; this is example output**):

    NAME                REGION                   NETWORK        RANGE
    default             asia-northeast1          default        10.146.0.0/20
    default             us-west1                 default        10.138.0.0/20
    default             southamerica-east1       default        10.158.0.0/20
    default             europe-west4             default        10.164.0.0/20
    default             asia-east1               default        10.140.0.0/20
    default             europe-north1            default        10.166.0.0/20
    default             asia-southeast1          default        10.148.0.0/20
    default             us-east4                 default        10.150.0.0/20
    default             europe-west1             default        10.132.0.0/20
    default             europe-west2             default        10.154.0.0/20
    default             europe-west3             default        10.156.0.0/20
    default             australia-southeast1     default        10.152.0.0/20
    default             asia-south1              default        10.160.0.0/20
    default             us-east1                 default        10.142.0.0/20
    default             us-central1              default        10.128.0.0/20
    default             northamerica-northeast1  default        10.162.0.0/20
    managementsubnet-us us-central1              managementnet  10.130.0.0/20
    mynetwork           asia-northeast1          mynetwork      10.146.0.0/20
    mynetwork           us-west1                 mynetwork      10.138.0.0/20
    mynetwork           southamerica-east1       mynetwork      10.158.0.0/20
    mynetwork           europe-west4             mynetwork      10.164.0.0/20
    mynetwork           asia-east1               mynetwork      10.140.0.0/20
    mynetwork           europe-north1            mynetwork      10.166.0.0/20
    mynetwork           asia-southeast1          mynetwork      10.148.0.0/20
    mynetwork           us-east4                 mynetwork      10.150.0.0/20
    mynetwork           europe-west1             mynetwork      10.132.0.0/20
    mynetwork           europe-west2             mynetwork      10.154.0.0/20
    mynetwork           europe-west3             mynetwork      10.156.0.0/20
    mynetwork           australia-southeast1     mynetwork      10.152.0.0/20
    mynetwork           asia-south1              mynetwork      10.160.0.0/20
    mynetwork           us-east1                 mynetwork      10.142.0.0/20
    mynetwork           us-central1              mynetwork      10.128.0.0/20
    mynetwork           northamerica-northeast1  mynetwork      10.162.0.0/20
    privatesubnet-eu    europe-west1             privatenet     172.20.0.0/20
    privatesubnet-us    us-central1              privatenet     172.16.0.0/24

<ql-infobox>As expected, the **default** and **mynetwork** networks have subnets in [each region](https://cloud.google.com/compute/docs/regions-zones/#available) as they are auto mode networks. The **managementnet** and **privatenet** networks only have the subnets that you created as they are custom mode networks.</ql-infobox>

1.  In the Cloud Console, navigate to **Navigation menu** > **VPC network** > **VPC networks**.

2.  You see that the same networks and subnets are listed in the Cloud Console.

### Create the firewall rules for managementnet

Create firewall rules to allow **SSH**, **ICMP**, and **RDP** ingress traffic to VM instances on the **managementnet** network.

1.  In the Cloud Console, navigate to **Navigation menu** (![mainmenu.png](https://cdn.qwiklabs.com/QRLlvUpflhvp875q7fi7Mf%2FiVCqtMcM10Vfn0vwKZ%2B4%3D)) > **VPC network** > **Firewall**.

2.  Click **+ Create Firewall Rule**.

3.  Set the following values, leave all other values at their defaults:

    <table>

    <tbody>

    <tr>

    <th>Property</th>

    <th>Value (type value or select option as specified)</th>

    </tr>

    <tr>

    <td>Name</td>

    <td>managementnet-allow-icmp-ssh-rdp</td>

    </tr>

    <tr>

    <td>Network</td>

    <td>managementnet</td>

    </tr>

    <tr>

    <td>Targets</td>

    <td>All instances in the network</td>

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

    <td>Specified protocols and ports, and then _check_ tcp, _type:_ 22, 3389; and _check_ Other protocols, _type:_ icmp.</td>

    </tr>

    </tbody>

    </table>

<ql-infobox>Make sure to include the **/0** in the **Source IP ranges** to specify all networks.</ql-infobox>

1.  Click **command line**.

    These commands illustrate that firewall rules can also be created using the Cloud Shell command line. You will create the **privatenet**'s firewall rules using these commands with similar parameters.

2.  Click **Close**.

3.  Click **Create**.

#### Test Completed Task

Click **Check my progress** to verify your performed task. If you have successfully created firewall rules for managementnet network, you will see an assessment score.

<ql-activity-tracking step="3">Create the firewall rules for managementnet</ql-activity-tracking>

### Create the firewall rules for privatenet

Create the firewall rules for **privatenet** network using the Cloud Shell command line.

1.  In Cloud Shell, run the following command to create the **privatenet-allow-icmp-ssh-rdp** firewall rule:

    gcloud compute firewall-rules create privatenet-allow-icmp-ssh-rdp --direction=INGRESS --priority=1000 --network=privatenet --action=ALLOW --rules=icmp,tcp:22,tcp:3389 --source-ranges=0.0.0.0/0

The output should look like this (**do not copy; this is example output**):

    NAME                           NETWORK     DIRECTION  PRIORITY  ALLOW                 DENY
    privatenet-allow-icmp-ssh-rdp  privatenet  INGRESS    1000      icmp,tcp:22,tcp:3389

#### Test Completed Task

Click **Check my progress** to verify your performed task. If you have successfully created firewall rules for privatenet network, you will see an assessment score.

<ql-activity-tracking step="4">Create the firewall rules for privatenet</ql-activity-tracking>

1.  Run the following command to list all the firewall rules (sorted by VPC network):

    gcloud compute firewall-rules list --sort-by=NETWORK

The output should look like this (**do not copy; this is example output**):

    NAME                              NETWORK        DIRECTION  PRIORITY  ALLOW                         DENY
    default-allow-icmp                default        INGRESS    65534     icmp
    default-allow-internal            default        INGRESS    65534     tcp:0-65535,udp:0-65535,icmp
    default-allow-rdp                 default        INGRESS    65534     tcp:3389
    default-allow-ssh                 default        INGRESS    65534     tcp:22
    managementnet-allow-icmp-ssh-rdp  managementnet  INGRESS    1000      icmp,tcp:22,tcp:3389
    mynetwork-allow-icmp              mynetwork      INGRESS    1000      icmp
    mynetwork-allow-rdp               mynetwork      INGRESS    1000      tcp:3389
    mynetwork-allow-ssh               mynetwork      INGRESS    1000      tcp:22
    privatenet-allow-icmp-ssh-rdp     privatenet     INGRESS    1000      icmp,tcp:22,tcp:3389

The firewall rules for **mynetwork** network have been created for you. You can define multiple protocols and ports in one firewall rule (**privatenet** and **managementnet**), or spread them across multiple rules (**default** and **mynetwork**).

1.  In the Cloud Console, navigate to **Navigation menu** > **VPC network** > **Firewall**.

2.  You see that the same firewall rules are listed in the Cloud Console.

## Create VM instances

Create two VM instances:

*   **managementnet-us-vm** in **managementsubnet-us**

*   **privatenet-us-vm** in **privatesubnet-us**

### Create the managementnet-us-vm instance

Create the **managementnet-us-vm** instance using the Cloud Console.

1.  In the Cloud Console, navigate to **Navigation menu** > **Compute Engine** > **VM instances**.

    The **mynet-eu-vm** and **mynet-us-vm** has been created for you, as part of your network diagram.

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

    <td>managementnet-us-vm</td>

    </tr>

    <tr>

    <td>Region</td>

    <td>us-central1</td>

    </tr>

    <tr>

    <td>Zone</td>

    <td>us-central1-c</td>

    </tr>

    <tr>

    <td>Series</td>

    <td>N1</td>

    </tr>

    <tr>

    <td>Machine type</td>

    <td>1 vCPU (f1-micro)</td>

    </tr>

    </tbody>

    </table>

4.  Click **Management, security, disks, networking, sole tenancy**.

5.  Click **Networking**.

6.  For **Network interfaces**, click the pencil icon to edit.

7.  Set the following values, leave all other values at their defaults:

    <table>

    <tbody>

    <tr>

    <th>Property</th>

    <th>Value (type value or select option as specified)</th>

    </tr>

    <tr>

    <td>Network</td>

    <td>managementnet</td>

    </tr>

    <tr>

    <td>Subnetwork</td>

    <td>managementsubnet-us</td>

    </tr>

    </tbody>

    </table>

8.  Click **Done**.

9.  Click **command line**.

    This illustrate that VM instances can also be created using the Cloud Shell command line. You will create the **privatenet-us-vm** instance using these commands with similar parameters.

10.  Click **Close**.

11.  Click **Create**.

#### Test Completed Task

Click **Check my progress** to verify your performed task. If you have successfully created VM instance in managementnet network, you will see an assessment score.

<ql-activity-tracking step="5">Create the managementnet-us-vm instance</ql-activity-tracking>

### Create the privatenet-us-vm instance

Create the **privatenet-us-vm** instance using the Cloud Shell command line.

1.  In Cloud Shell, run the following command to create the **privatenet-us-vm** instance:

    gcloud compute instances create privatenet-us-vm --zone=us-central1-c --machine-type=n1-standard-1 --subnet=privatesubnet-us

The output should look like this (**do not copy; this is example output**):

    NAME              ZONE           MACHINE_TYPE   PREEMPTIBLE  INTERNAL_IP  EXTERNAL_IP    STATUS
    privatenet-us-vm  us-central1-c  n1-standard-1               172.16.0.2   35.184.221.40  RUNNING

#### Test Completed Task

Click **Check my progress** to verify your performed task. If you have successfully created VM instance in privatenet network, you will see an assessment score.

<ql-activity-tracking step="6">Create the privatenet-us-vm instance</ql-activity-tracking>

1.  Run the following command to list all the VM instances (sorted by zone):

    gcloud compute instances list --sort-by=ZONE

The output should look like this (**do not copy; this is example output**):

    NAME                 ZONE            MACHINE_TYPE   PREEMPTIBLE  INTERNAL_IP  EXTERNAL_IP     STATUS
    mynet-eu-vm          europe-west1-c  n1-standard-1               10.132.0.2   35.205.124.164  RUNNING
    managementnet-us-vm  us-central1-c   n1-standard-1               10.130.0.2   35.226.20.87    RUNNING
    mynet-us-vm          us-central1-c   n1-standard-1               10.128.0.2   35.232.252.86   RUNNING
    privatenet-us-vm     us-central1-c   n1-standard-1               172.16.0.2   35.184.221.40   RUNNING

1.  In the Cloud Console, navigate to **Navigation menu** (![mainmenu.png](https://cdn.qwiklabs.com/QRLlvUpflhvp875q7fi7Mf%2FiVCqtMcM10Vfn0vwKZ%2B4%3D)) > **Compute Engine** > **VM instances**.

2.  You see that the VM instances are listed in the Cloud Console.

3.  Click on **Columns**, then select **Network**.

    There are three instances in **us-central1-c** and one instance in **europe-west1-c**. However, these instances are spread across three VPC networks (**managementnet**, **mynetwork** and **privatenet**), with no instance in the same zone and network as another. In the next section, you explore the effect this has on internal connectivity.

## Explore the connectivity between VM instances

Explore the connectivity between the VM instances. Specifically, determine the effect of having VM instances in the same zone versus having instances in the same VPC network.

### Ping the external IP addresses

Ping the external IP addresses of the VM instances to determine if you can reach the instances from the public internet.

1.  In the Cloud Console, navigate to **Navigation menu** > **Compute Engine** > **VM instances**.

2.  Note the external IP addressees for **mynet-eu-vm**, **managementnet-us-vm**, and **privatenet-us-vm**.

3.  For **mynet-us-vm**, click **SSH** to launch a terminal and connect.

4.  To test connectivity to **mynet-eu-vm**'s external IP, run the following command, replacing **mynet-eu-vm**'s external IP:

    ping -c 3 <Enter mynet-eu-vm's external IP here>

This should work!

1.  To test connectivity to **managementnet-us-vm**'s external IP, run the following command, replacing **managementnet-us-vm**'s external IP:

    ping -c 3 <Enter managementnet-us-vm's external IP here>

This should work!

1.  To test connectivity to **privatenet-us-vm**'s external IP, run the following command, replacing **privatenet-us-vm**'s external IP:

    ping -c 3 <Enter privatenet-us-vm's external IP here>

This should work!

<ql-infobox>You are able to ping the external IP address of all VM instances, even though they are either in a different zone or VPC network. This confirms public access to those instances is only controlled by the **ICMP** firewall rules that you established earlier.</ql-infobox>

### Ping the internal IP addresses

Ping the internal IP addresses of the VM instances to determine if you can reach the instances from within a VPC network.

1.  In the Cloud Console, navigate to **Navigation menu** > **Compute Engine** > **VM instances**.

2.  Note the internal IP addressees for **mynet-eu-vm**, **managementnet-us-vm**, and **privatenet-us-vm**.

3.  Return to the **SSH** terminal for **mynet-us-vm**.

4.  To test connectivity to **mynet-eu-vm**'s internal IP, run the following command, replacing **mynet-eu-vm**'s internal IP:

    ping -c 3 <Enter mynet-eu-vm's internal IP here>

<ql-infobox>You are able to ping the internal IP address of **mynet-eu-vm** because it is on the same VPC network as the source of the ping (**mynet-us-vm**), even though both VM instances are in separate zones, regions and continents!</ql-infobox>

1.  To test connectivity to **managementnet-us-vm**'s internal IP, run the following command, replacing **managementnet-us-vm**'s internal IP:

    ping -c 3 <Enter managementnet-us-vm's internal IP here>

<ql-warningbox>This should not work as indicated by a 100% packet loss!</ql-warningbox>

1.  To test connectivity to **privatenet-us-vm**'s internal IP, run the following command, replacing **privatenet-us-vm**'s internal IP:

    ping -c 3 <Enter privatenet-us-vm's internal IP here>

<ql-warningbox>This should not work either as indicated by a 100% packet loss! You are unable to ping the internal IP address of **managementnet-us-vm** and **privatenet-us-vm** because they are in separate VPC networks from the source of the ping (**mynet-us-vm**), even though they are all in the same zone **us-central1**.</ql-warningbox>

VPC networks are by default isolated private networking domains. However, no internal IP address communication is allowed between networks, unless you set up mechanisms such as VPC peering or VPN.

## Create a VM instance with multiple network interfaces

Every instance in a VPC network has a default network interface. You can create additional network interfaces attached to your VMs. Multiple network interfaces enable you to create configurations in which an instance connects directly to several VPC networks (up to 8 interfaces, depending on the instance's type).

### Create the VM instance with multiple network interfaces

Create the **vm-appliance** instance with network interfaces in **privatesubnet-us**, **managementsubnet-us** and **mynetwork**. The CIDR ranges of these subnets do not overlap, which is a requirement for creating a VM with multiple network interface controllers (NICs).

1.  In the Cloud Console, navigate to **Navigation menu** > **Compute Engine** > **VM instances**.

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

    <td>vm-appliance</td>

    </tr>

    <tr>

    <td>Region</td>

    <td>us-central1</td>

    </tr>

    <tr>

    <td>Zone</td>

    <td>us-central1-c</td>

    </tr>

    <tr>

    <td>Series</td>

    <td>N1</td>

    </tr>

    <tr>

    <td>Machine type</td>

    <td>4 vCPUs (n1-standard-4)</td>

    </tr>

    </tbody>

    </table>

<ql-infobox>The number of interfaces allowed in an instance is dependent on the instance's machine type and the number of vCPUs. The n1-standard-4 allows up to 4 network interfaces. Refer [here](https://cloud.google.com/vpc/docs/create-use-multiple-interfaces#max-interfaces) for more information.</ql-infobox>

1.  Click **Management, security, disks, networking, sole tenancy**.

2.  Click **Networking**.

3.  For **Network interfaces**, click the pencil icon to edit.

4.  Set the following values, leave all other values at their defaults:

    <table>

    <tbody>

    <tr>

    <th>Property</th>

    <th>Value (type value or select option as specified)</th>

    </tr>

    <tr>

    <td>Network</td>

    <td>privatenet</td>

    </tr>

    <tr>

    <td>Subnetwork</td>

    <td>privatesubnet-us</td>

    </tr>

    </tbody>

    </table>

5.  Click **Done**.

6.  Click **Add network interface**.

7.  Set the following values, leave all other values at their defaults:

    <table>

    <tbody>

    <tr>

    <th>Property</th>

    <th>Value (type value or select option as specified)</th>

    </tr>

    <tr>

    <td>Network</td>

    <td>managementnet</td>

    </tr>

    <tr>

    <td>Subnetwork</td>

    <td>managementsubnet-us</td>

    </tr>

    </tbody>

    </table>

8.  Click **Done**.

9.  Click **Add network interface**.

10.  Set the following values, leave all other values at their defaults:

    <table>

    <tbody>

    <tr>

    <th>Property</th>

    <th>Value (type value or select option as specified)</th>

    </tr>

    <tr>

    <td>Network</td>

    <td>mynetwork</td>

    </tr>

    <tr>

    <td>Subnetwork</td>

    <td>mynetwork</td>

    </tr>

    </tbody>

    </table>

11.  Click **Done**.

12.  Click **Create**.

#### Test Completed Task

Click **Check my progress** to verify your performed task. If you have successfully created VM instance with multiple network interfaces, you will see an assessment score.

<ql-activity-tracking step="7">Create a VM instance with multiple network interfaces</ql-activity-tracking>

### Explore the network interface details

Explore the network interface details of **vm-appliance** within the Cloud Console and within the VM's terminal.

1.  In the Cloud Console, navigate to **Navigation menu** (![mainmenu.png](https://cdn.qwiklabs.com/QRLlvUpflhvp875q7fi7Mf%2FiVCqtMcM10Vfn0vwKZ%2B4%3D)) > **Compute Engine** > **VM instances**.
2.  Click **nic0** within the **Internal IP** address of **vm-appliance** to open the **Network interface details** page.
3.  Verify that **nic0** is attached to **privatesubnet-us**, is assigned an internal IP address within that subnet (172.16.0.0/24), and has applicable firewall rules.
4.  Click **nic0** and select **nic1**.
5.  Verify that **nic1** is attached to **managementsubnet-us**, is assigned an internal IP address within that subnet (10.130.0.0/20), and has applicable firewall rules.
6.  Click **nic1** and select **nic2**.
7.  Verify that **nic2** is attached to **mynetwork**, is assigned an internal IP address within that subnet (10.128.0.0/20), and has applicable firewall rules.

<ql-infobox>Each network interface has its own internal IP address so that the VM instance can communicate with those networks.</ql-infobox>

1.  In the Cloud Console, navigate to **Navigation menu** > **Compute Engine** > **VM instances**.

2.  For **vm-appliance**, click **SSH** to launch a terminal and connect.

3.  Run the following, to list the network interfaces within the VM instance:

    sudo ifconfig

The output should look like this (**do not copy; this is example output**):

    eth0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1460
            inet 172.16.0.3  netmask 255.255.255.255  broadcast 172.16.0.3
            inet6 fe80::4001:acff:fe10:3  prefixlen 64  scopeid 0x20<link>
            ether 42:01:ac:10:00:03  txqueuelen 1000  (Ethernet)
            RX packets 626  bytes 171556 (167.5 KiB)
            RX errors 0  dropped 0  overruns 0  frame 0
            TX packets 568  bytes 62294 (60.8 KiB)
            TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
    eth1: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1460
            inet 10.130.0.3  netmask 255.255.255.255  broadcast 10.130.0.3
            inet6 fe80::4001:aff:fe82:3  prefixlen 64  scopeid 0x20<link>
            ether 42:01:0a:82:00:03  txqueuelen 1000  (Ethernet)
            RX packets 7  bytes 1222 (1.1 KiB)
            RX errors 0  dropped 0  overruns 0  frame 0
            TX packets 17  bytes 1842 (1.7 KiB)
            TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
    eth2: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1460
            inet 10.128.0.3  netmask 255.255.255.255  broadcast 10.128.0.3
            inet6 fe80::4001:aff:fe80:3  prefixlen 64  scopeid 0x20<link>
            ether 42:01:0a:80:00:03  txqueuelen 1000  (Ethernet)
            RX packets 17  bytes 2014 (1.9 KiB)
            RX errors 0  dropped 0  overruns 0  frame 0
            TX packets 17  bytes 1862 (1.8 KiB)
            TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

<ql-infobox>The **sudo ifconfig** command lists a Linux VM's network interfaces along with the internal IP addresses for each interface.</ql-infobox>

### Explore the network interface connectivity

Demonstrate that the **vm-appliance** instance is connected to **privatesubnet-us**, **managementsubnet-us** and **mynetwork** by pinging VM instances on those subnets.

1.  In the Cloud Console, navigate to **Navigation menu** > **Compute Engine** > **VM instances**.

2.  Note the internal IP addressees for **privatenet-us-vm**, **managementnet-us-vm**, **mynet-us-vm**, and **mynet-eu-vm**.

3.  Return to the **SSH** terminal for **vm-appliance**.

4.  To test connectivity to **privatenet-us-vm**'s internal IP, run the following command, replacing **privatenet-us-vm**'s internal IP:

    ping -c 3 <Enter privatenet-us-vm's internal IP here>

This works!

1.  Repeat the same test by running the following:

    ping -c 3 privatenet-us-vm

<ql-infobox>You are able to ping **privatenet-us-vm** by its name because VPC networks have an internal DNS service that allows you to address instances by their DNS names rather than their internal IP addresses. When an internal DNS query is made with the instance hostname, it resolves to the primary interface (nic0) of the instance. Therefore, this only works for **privatenet-us-vm** in this case.</ql-infobox>

1.  To test connectivity to **managementnet-us-vm**'s internal IP, run the following command, replacing **managementnet-us-vm**'s internal IP:

    ping -c 3 <Enter managementnet-us-vm's internal IP here>

This works!

1.  To test connectivity to **mynet-us-vm**'s internal IP, run the following command, replacing **mynet-us-vm**'s internal IP:

    ping -c 3 <Enter mynet-us-vm's internal IP here>

This works!

1.  To test connectivity to **mynet-eu-vm**'s internal IP, run the following command, replacing **mynet-eu-vm**'s internal IP:

    ping -c 3 <Enter mynet-eu-vm's internal IP here>

<ql-warningbox>This does not work! In a multiple interface instance, every interface gets a route for the subnet that it is in. In addition, the instance gets a single default route that is associated with the primary interface eth0\. Unless manually configured otherwise, any traffic leaving an instance for any destination other than a directly connected subnet will leave the instance via the default route on eth0.</ql-warningbox>

1.  To list the routes for **vm-appliance** instance, run the following command:

    ip route

The output should look like this (**do not copy; this is example output**):

    default via 172.16.0.1 dev eth0
    10.128.0.0/20 via 10.128.0.1 dev eth2
    10.128.0.1 dev eth2 scope link
    10.130.0.0/20 via 10.130.0.1 dev eth1
    10.130.0.1 dev eth1 scope link
    172.16.0.0/24 via 172.16.0.1 dev eth0
    172.16.0.1 dev eth0 scope link

<ql-infobox>The primary interface eth0 gets the default route (default via 172.16.0.1 dev eth0), and all three interfaces eth0, eth1 and eth2 get routes for their respective subnets. Since, the subnet of **mynet-eu-vm** (**10.132.0.0/20**) is not included in this routing table, the ping to that instance leaves **vm-appliance** on eth0 (which is on a different VPC network). You could change this behavior by configuring policy routing as documented [here](https://cloud.google.com/vpc/docs/create-use-multiple-interfaces#configuring_policy_routing).</ql-infobox>

## Congratulations!

In this lab you created several custom mode VPC networks, firewall rules and VM instances using the Cloud Console and the Cloud Shell command line. Then, you tested the connectivity across VPC networks which worked when pinging external IP addresses but not when pinging internal IP addresses. Thus, you created a VM instance with three network interfaces and verified internal connectivity for VM instances that are on the subnets that are attached to the multiple interface VM.

You also explored the default network along with its subnets, routes, and firewall rules. You deleted the default network and determined that you cannot create any VM instances without a VPC network. Thus, you created a new auto mode VPC network with subnets, routes, firewall rules and two VM instances. Then, you tested the connectivity for the VM instances and explored the effects of the firewall rules on connectivity.

![Networking_125.png](https://cdn.qwiklabs.com/3bkldo7J0N94gjkFCX8%2BeN7N%2BSnaVXjJk%2FZRJCySM7U%3D) ![Cloud_Architecture.png](https://cdn.qwiklabs.com/QNBqfg1D%2FzCw3KdiioKbnB1YWX9jpAs21holGhwny%2FY%3D) ![CloudEngineeringExaPrep_125.png](https://cdn.qwiklabs.com/jVIZEu1uWCkufNtCmqyElRifK5o%2FNRrtt5Vo9iZxHz8%3D)

### Finish your Quest

This self-paced lab is part of the Qwiklabs [Networking in the Google Cloud](https://google.qwiklabs.com/quests/31), [Cloud Architecture](https://google.qwiklabs.com/quests/24) and [Cloud Engineering](https://google.qwiklabs.com/quests/66) Quests. A Quest is a series of related labs that form a learning path. Completing this Quest earns you the badge above to recognize your achievement. You can make your badge (or badges) public and link to them in your online resume or social media account. [Enroll in this Quest](https://google.qwiklabs.com/learning_paths/31/enroll) and get immediate completion credit if you've taken this lab. [See other available Qwiklabs Quests](https://google.qwiklabs.com/catalog).

### Take your next lab

Continue your Quest with [VPC Networks - Controlling Access](https://google.qwiklabs.com/catalog_lab/1032), or check out these suggestions:

*   [Customize Network Topology with Subnetworks](https://google.qwiklabs.com/catalog_lab/483)

*   [Creating Cross-region Load Balancing](https://google.qwiklabs.com/catalog_lab/805)

### Next Steps / Learn More

Learn more about VPC networking:

*   [Using VPC Networks](https://cloud.google.com/vpc/docs/using-vpc).

### Google Cloud Training & Certification

...helps you make the most of Google Cloud technologies. [Our classes](https://cloud.google.com/training/courses) include technical skills and best practices to help you get up to speed quickly and continue your learning journey. We offer fundamental to advanced level training, with on-demand, live, and virtual options to suit your busy schedule. [Certifications](https://cloud.google.com/certification/) help you validate and prove your skill and expertise in Google Cloud technologies.

##### Manual last updated September 10, 2020

##### Lab last tested September 10, 2020

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

<div class="js-lab-content-outline lab-content__outline">[GSP211](#step1)[Overview](#step2)[Setup and Requirements](#step3)[Create custom mode VPC networks with firewall rules](#step4)[Create VM instances](#step5)[Explore the connectivity between VM instances](#step6)[Create a VM instance with multiple network interfaces](#step7)[Congratulations!](#step8)</div>

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

In this lab, you create several VPC networks and VM instances and test connectivity across networks.

This lab is included in these quests: [Cloud Architecture](/quests/24), [Deploy and Manage Cloud Environments with Google Cloud](/quests/121), [Cloud Engineering](/quests/66), [Set Up and Configure a Cloud Environment in Google Cloud](/quests/119), [Networking in the Google Cloud](/quests/31) , [Build and Secure Networks in Google Cloud](/quests/128). If you complete this lab you'll receive credit for it when you enroll in one of these quests.

**Duration:** 1m setup · 70m access · 70m completion

<span>**Levels:** advanced</span>

**Permalink:** [https://google.qwiklabs.com/catalog_lab/1031](https://google.qwiklabs.com/catalog_lab/1031)

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

<div class="controls"><input class="hidden" type="hidden" value="1031" name="lab_review[lab_id]" id="lab_review_lab_id"></div>

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

<div class="lab-access-modal"><input type="hidden" name="id" id="id" value="1230"> <input type="hidden" name="user_id" id="user_id" value="4061609"> <input type="hidden" name="launch_with_credits" id="launch_with_credits" value="0" class="js-launch-with-credits-input"> <input type="hidden" name="launch_with_subs" id="launch_with_subs" value="0" class="js-launch-with-subscription-input">

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