<div class="lab-preamble">

# Cloud Monitoring: Qwik Start

<div class="lab-preamble__details subtitle-headline-1"><span>50 minutes</span> <span>1 Credit</span>

<div class="lab__rating">[](/focuses/10599/reviews?parent=catalog)<a data-target="#lab-review-modal" data-toggle="modal">Rate Lab</a></div>

</div>

</div>

<div class="js-markdown-instructions markdown-lab-instructions" id="markdown-lab-instructions">

## GSP089

![Google Cloud Self-Paced Labs](https://cdn.qwiklabs.com/GMOHykaqmlTHiqEeQXTySaMXYPHeIvaqa2qHEzw6Occ%3D)

## Overview

Cloud Monitoring provides visibility into the performance, uptime, and overall health of cloud-powered applications. Cloud Monitoring collects metrics, events, and metadata from Google Cloud, Amazon Web Services, hosted uptime probes, application instrumentation, and a variety of common application components including Cassandra, Nginx, Apache Web Server, Elasticsearch, and many others. Cloud Monitoring ingests that data and generates insights via dashboards, charts, and alerts. Cloud Monitoring alerting helps you collaborate by integrating with Slack, PagerDuty, HipChat, Campfire, and more.

This hands-on lab shows you how to monitor a Compute Engine virtual machine (VM) instance with Cloud Monitoring. You'll also install monitoring and logging agents for your VM which collects more information from your instance, which could include metrics and logs from 3rd party apps.

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

## Create a Compute Engine instance

1.  In the Cloud Console dashboard, go to **Navigation menu** > **Compute Engine** > **VM instances**, then click **Create**.

![nav_compute.png](https://cdn.qwiklabs.com/48XM2fQQVCp7ObiNAyXTPs7VFh%2BuW7M2gyc3pPGK62E%3D)

1.  Fill in the fields as follows, leaving all other fields at the default value:

    <table>

    <tbody>

    <tr>

    <th>Field</th>

    <th>Value</th>

    </tr>

    <tr>

    <td>Name</td>

    <td>lamp-1-vm</td>

    </tr>

    <tr>

    <td>Region</td>

    <td>us-central1 (Iowa)</td>

    </tr>

    <tr>

    <td>Zone</td>

    <td>us-central1-a</td>

    </tr>

    <tr>

    <td>Series</td>

    <td>N1</td>

    </tr>

    <tr>

    <td>Machine type</td>

    <td>n1-standard-2</td>

    </tr>

    <tr>

    <td>Firewall</td>

    <td>check Allow HTTP traffic</td>

    </tr>

    </tbody>

    </table>

2.  Click **Create**.

Wait a couple of minutes, you'll see a green check when the instance has launched.

Click **Check my progress** below. A green check confirms you're on track.

<ql-activity-tracking step="1">Create a Compute Engine instance (zone: us-central1-a)</ql-activity-tracking>

## Add Apache2 HTTP Server to your instance

1.  In the Cloud Console, click **SSH** to open a terminal to your instance.

![SSH.png](https://cdn.qwiklabs.com/157XnA6MKqd0jjbTNhOenyTQWI31YJZVWQUn30E8wTY%3D)

1.  Run the following commands in the SSH window to set up Apache2 HTTP Server:

    sudo apt-get update

    sudo apt-get install apache2 php7.0

When asked if you want to continue, enter **Y**.

<aside class="special">

**Note:** If you cannot install php7.0, use php5\.

</aside>

    sudo service apache2 restart

Click **Check my progress** below. A green check confirms you're on track.

<ql-activity-tracking step="2">Add Apache2 HTTP Server to your instance</ql-activity-tracking>

1.  Return to the Cloud Console, on the VM instances page. Click the `External IP` for `lamp-1-vm` instance to see the Apache2 default page for this instance.

![ext_IP_address.png](https://cdn.qwiklabs.com/IZlq%2Fs%2Fjkwv08kKY5OMQouAGmM2e%2BZD83AOMfY1LVqM%3D)

![d1b14dc18bc7a72d.png](https://cdn.qwiklabs.com/UYEHvdQG6fv%2FFFuZk6xiz4oSzaVznBOY%2BrvHOFPnpt8%3D)

Click **Check my progress** below. A green check confirms you're on track.

<ql-activity-tracking step="3">Get a success response over External IP of VM instance</ql-activity-tracking>

### Create a Monitoring workspace

Now set up a Monitoring workspace that's tied to your Google Cloud Project. The following steps create a new account that has a free trial of Monitoring.

1.  In the Cloud Console, click **Navigation menu** > **Monitoring**.

2.  Wait for your workspace to be provisioned.

When the Monitoring dashboard opens, your workspace is ready.

![Overview.png](https://cdn.qwiklabs.com/FfS7W1mNXshxngUuea%2BFUBXoXedgDHt0YWk1aZKHiIk%3D)

### Install the Monitoring and Logging agents

Agents collect data and then send or stream info to Cloud Monitoring in the Cloud Console.

The _Cloud Monitoring agent_ is a collectd-based daemon that gathers system and application metrics from virtual machine instances and sends them to Monitoring. By default, the Monitoring agent collects disk, CPU, network, and process metrics. Configuring the Monitoring agent allows third-party applications to get the full list of agent metrics. See [Cloud Monitoring agent overview](https://cloud.google.com/monitoring/agent) for more information.

In this section, you install the _Cloud Logging agent_ to stream logs from your VM instances to Cloud Logging. Later in this lab, you see what logs are generated when you stop and start your VM.

<ql-infobox>It is best practice to run the Cloud Logging agent on all your VM instances.</ql-infobox>

#### Install agents on the VM:

1.  Run the Monitoring agent install script command in the SSH terminal of your VM instance to install the Cloud Monitoring agent.

    curl -sSO https://dl.google.com/cloudagents/add-monitoring-agent-repo.sh
    sudo bash add-monitoring-agent-repo.sh

    sudo apt-get update

    sudo apt-get install stackdriver-agent

When asked if you want to continue, enter **Y**.

1.  Run the Logging agent install script command in the SSH terminal of your VM instance to install the Cloud Logging agent

    curl -sSO https://dl.google.com/cloudagents/add-logging-agent-repo.sh
    sudo bash add-logging-agent-repo.sh

    sudo apt-get update

    sudo apt-get install google-fluentd

## Create an uptime check

Uptime checks verify that a resource is always accessible. For practice, create an uptime check to verify your VM is up.

1.  In the Cloud Console, in the left menu, click **Uptime checks**, and then click **Create Uptime Check**.

![create_uptime_check.png](https://cdn.qwiklabs.com/mdt35y5ElNzYoCkdrVkodx81TWTuTWAbBYfoj0Ps5r4%3D)

1.  Set the following fields:

**Title**: Lamp Uptime Check, then click **Next**.

**Protocol**: HTTP

**Resource Type**: Instance

**Applies to**: Single, lamp-1-vm

**Path**: leave at default

**Check Frequency**: 1 min

![783246b4ceb3af54.png](https://cdn.qwiklabs.com/HYlXLfWnUXFtXC0A34MfQljuNeFLsUr4XX93uPYfWPY%3D)

1.  Click on **Next** to leave the other details to default and click **Test** to verify that your uptime check can connect to the resource.

2.  When you see a green check mark everything can connect. Click **Create**.

The uptime check you configured takes a while for it to become active. Continue with the lab, you'll check for results later. While you wait, create an alerting policy for a different resource.

## Create an alerting policy

Use Cloud Monitoring to create one or more alerting policies.

1.  In the left menu, click **Alerting**, and then click **Create Policy**.

2.  Click **Add Condition**.

Set the following in the panel that opens, leave all other fields at the default value.

**Target**: Start typing "VM" in the resource type and metric field, and then select:

*   **Resource Type**: VM Instance (gce_instance)

*   **Metric**: Type "network", and then select Network traffic (gce_instance+1). Be sure to choose the Network traffic resource with `agent.googleapis.com/interface/traffic`:

    ![network.png](https://cdn.qwiklabs.com/iaqRe2fY42Td%2B8T8lu0UJUPRp377nHLXBvuXi%2BUGyBk%3D)

**Configuration**

*   **Condition**: is above
*   **Threshold**: 500
*   **For**: 1 minute

Click **ADD**.

1.  Click on **Next**.

2.  Click on drop down arrow next to **Notification Channels**, then click on **Manage Notification Channels**.

![email.png](https://cdn.qwiklabs.com/akOFdDyEuKOmhn6BWOrBP7cGEvEy5jTDg28Q71R6Xms%3D)

A **Notification channels** page will open in new tab.

1.  Scroll down the page and click on **ADD NEW** for **Email**.

![add_email.png](https://cdn.qwiklabs.com/%2B3S74DM%2BjBdvWDyjMwuEzhk76T8GS6jA3aSpeeTsfvU%3D)

1.  In **Create Email Channel** dialog box, enter your personal email address in the **Email Address** field and a **Display name**.

2.  Click on **Save**.

3.  Go back to the previous **Create alerting policy** tab.

4.  Click on **Notification Channels** again, then click on the **Refresh icon** to get the display name you mentioned in the previous step.

![refresh_icon.png](https://cdn.qwiklabs.com/fXnoFzeQzHQdc905vD6QLzfKuwfk3F0BXZZADgsaUaQ%3D)

1.  Now, select your **Display name** and click **OK**.

2.  Click **Next**.

3.  Mention the **Alert name** as `Inbound Traffic Alert`.

4.  Add a message in documentation, which will be included in the emailed alert.

5.  Click on **Save**.

You've created an alert! While you wait for the system to trigger an alert, create a dashboard and chart, and then check out Cloud Logging.

Click **Check my progress** below. A green check confirms you're on track.

<ql-activity-tracking step="4">Create an uptime check and alerting policy</ql-activity-tracking>

## Create a dashboard and chart

You can display the metrics collected by Cloud Monitoring in your own charts and dashboards. In this section you create the charts for the lab metrics and a custom dashboard.

1.  In the left menu select **Dashboards**, and then **Create Dashboard**.

2.  Name the dashboard `Cloud Monitoring LAMP Qwik Start Dashboard`.

### Add the first chart

1.  Click **Line** option in Chart library.

2.  Name the chart title **CPU Load**.

3.  Set the Resource type to **VM Instance**.

4.  Set the Metric **CPU load (1m)**. Refresh the tab to view the graph.

![CPU_load.png](https://cdn.qwiklabs.com/s66pEeItPgh5Xpx5%2FVW9wCUjO%2FFWffJ4%2FEWTLiYqobk%3D)

### Add the second chart

1.  Click **+ Add Chart** and select **Line** option in Chart library.

2.  Name this chart **Received Packets**.

3.  Set the resource type to **VM Instance**.

4.  Set the Metric **Received packets** (gce_instance). Refresh the tab to view the graph.

5.  Leave the other fields at their default values. You see the chart data.

## View your logs

Cloud Monitoring and Cloud Logging are closely integrated. Check out the logs for your lab.

1.  Select **Navigation menu** > **Logging** > **Logs Explorer**.

2.  Select the logs you want to see, in this case, you select the logs for the lamp-1-vm instance you created at the start of this lab:

*   Click on **Resource**.

![resources_1.png](https://cdn.qwiklabs.com/oQG0TLaDLkFzNOAC25twWtuq55mOUPJDw05rZ3hMPXM%3D)

*   Select **VM Instance** > **lamp-1-vm** in the Resource drop-down menu.

![lamp_1_vm.png](https://cdn.qwiklabs.com/At5somXo%2FLgoR0YnOtwttk6q1QZUNTJssq2lxhl5luE%3D)

*   Click **Add**.
*   Leave the other fields with their default values.
*   Click the **Stream logs**.

![stream_logs.png](https://cdn.qwiklabs.com/47cMAwVFt6zz3jGKFihSNTzHxaWmxpdyeXFi0QnUptw%3D)

You see the logs for your VM instance:

![stream_log_view.png](https://cdn.qwiklabs.com/yhfhYnWNatsnKkjPBSrN0gzSS0chG0YZcAF2Y9HSPOg%3D)

### Check out what happens when you start and stop the VM instance.

To best see how Cloud Monitoring and Cloud Logging reflect VM instance changes, make changes to your instance in one browser window and then see what happens in the Cloud Monitoring, and then Cloud Logging windows.

1.  Open the Compute Engine window in a new browser window. Select **Navigation menu** > **Compute Engine**, right-click **VM instances** > **Open link in new window**.
2.  Move the Logs Viewer browser window next to the Compute Engine window. This makes it easier to view how changes to the VM are reflected in the logs.

![both_consoles_1.png](https://cdn.qwiklabs.com/HNEkpniiVJn2WneNYtD9KYwYYpbSEza4A2%2FOYRdgvM4%3D)

1.  In the Compute Engine window, select the lamp-1-vm instance, click **Stop** at the top of the screen, and then confirm to stop the instance.

![stop-vm.png](https://cdn.qwiklabs.com/Cc091icpheV9nzMvh1ZKwdETxIk4UztCuM9COeYfFJo%3D)

It takes a few minutes for the instance to stop.

1.  Watch in the Logs View tab for when the VM is stopped.

![stop_vm_logs.png](https://cdn.qwiklabs.com/Uilb7O2f84GHmmjvLrWsDJEywuBhHIij1jAV24Xkft4%3D)

1.  In the VM instance details window, click **Start** at the top of the screen, and then confirm. It will take a few minutes for the instance to re-start. Watch the log messages to monitor the start up.

![start_vm_logs.png](https://cdn.qwiklabs.com/6R4kvJJg4oJKucjrkvHIJedqvfJ2nemhArgDunrVES0%3D)

## Check the uptime check results and triggered alerts

1.  In the Cloud Logging window, select **Navigation menu** > **Monitoring** > **Uptime checks**. This view provides a list of all active uptime checks, and the status of each in different locations.

You will see Lamp Uptime Check listed. Since you have just restarted your instance, the regions are in a failed status. It may take up to 5 minutes for the regions to become active. Reload your browser window as necessary until the regions are active.

1.  Click the name of the uptime check, Lamp Uptime Check.

Since you have just restarted your instance, it may take some minutes for the regions to become active. Reload your browser window as necessary.

### Check if alerts have been triggered

1.  In the left menu, click **Alerting**.

2.  You see incidents and events listed in the Alerting window.

3.  Check your email account. You should see Cloud Monitoring Alerts.

**Note:** Remove the email notification from your alerting policy. The resources for the lab may be active for a while after the completion, and this may result in a few more email notifications getting sent out.

## Congratulations!

You have successfully set up and monitored a VM with Cloud Monitoring.

![4588136b652766c0.png](https://cdn.qwiklabs.com/v9Q2F9GfjA%2Ff5nwcDjkXPkJaN1L51MLqa%2BhmudOLIaM%3D) ![5edc9b3763bbb596.png](https://cdn.qwiklabs.com/6EiXINl%2FCnCMQyRdF%2Fs34InkRozTg%2FhSEqCQ4CD4nf4%3D) ![CloudEngineeringExaPrep_125.pmg](https://cdn.qwiklabs.com/jVIZEu1uWCkufNtCmqyElRifK5o%2FNRrtt5Vo9iZxHz8%3D) ![CloudDevelopment_125.png](https://cdn.qwiklabs.com/jpM8y0j3jDJyIIgc%2BJitIHzicqeNn%2BgCjyb%2Bir6YE9c%3D)

### Finish your Quest

This self-paced lab is part of the Qwiklabs [Google Cloud's Operations Suite](https://google.qwiklabs.com/quests/35), [Baseline: Infrastructure](https://google.qwiklabs.com/quests/33), [Cloud Engineering](https://google.qwiklabs.com/quests/66), and [Cloud Development](https://google.qwiklabs.com/quests/67) Quests. A Quest is a series of related labs that form a learning path. Completing this Quest earns you the badge above, to recognize your achievement. You can make your badge public and link to them in your online resume or social media account. Enroll in a Quest and get immediate completion credit if you've taken this lab. [See other available Qwiklabs Quests](https://google.qwiklabs.com/catalog).

### Take your next lab

This lab is also part of a series of labs called Qwik Starts. These labs are designed to give you a little taste of the many features available with Google Cloud. Search for "Qwik Starts" in the [lab catalog](https://google.qwiklabs.com/catalog) to find the next lab you'd like to take!

### Google Cloud Training & Certification

...helps you make the most of Google Cloud technologies. [Our classes](https://cloud.google.com/training/courses) include technical skills and best practices to help you get up to speed quickly and continue your learning journey. We offer fundamental to advanced level training, with on-demand, live, and virtual options to suit your busy schedule. [Certifications](https://cloud.google.com/certification/) help you validate and prove your skill and expertise in Google Cloud technologies.

##### Lab last tested December 17, 2020

##### Manual last updated December 17, 2020

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

<div class="js-lab-content-outline lab-content__outline">[GSP089](#step1)[Overview](#step2)[Setup and Requirements](#step3)[Create a Compute Engine instance](#step4)[Add Apache2 HTTP Server to your instance](#step5)[Create an uptime check](#step6)[Create an alerting policy](#step7)[Create a dashboard and chart](#step8)[View your logs](#step9)[Check the uptime check results and triggered alerts](#step10)[Congratulations!](#step11)</div>

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

This lab shows you how to monitor a Google Compute Engine virtual machine (VM) instance with Cloud Monitoring. Watch the short videos [Monitor Health of All Your Cloud Apps with Google Cloud monitoring](https://youtu.be/rGMimov8MPg) and [Monitor a VM Instance with Cloud monitoring, GCP Essentials](https://youtu.be/ZuUZQfCmA7Y).

This lab is included in these quests: [Baseline: Infrastructure](/quests/33), [Perform Foundational Infrastructure Tasks in Google Cloud](/quests/118), [Cloud Engineering](/quests/66), [Set Up and Configure a Cloud Environment in Google Cloud](/quests/119), [Google Cloud's Operations Suite](/quests/35), [Monitor and Log with Google Cloud Operations Suite](/quests/143), [Google Cloud's Operations Suite on GKE](/quests/133), [Optimizing Your Google Cloud Costs](/quests/97), [Cloud Development](/quests/67), [Windows on GCP](/quests/27). If you complete this lab you'll receive credit for it when you enroll in one of these quests.

**Duration:** 0m setup · 50m access · 50m completion

<span>**Levels:** introductory</span>

**Permalink:** [https://google.qwiklabs.com/catalog_lab/2572](https://google.qwiklabs.com/catalog_lab/2572)

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

<div class="controls"><input class="hidden" type="hidden" value="2572" name="lab_review[lab_id]" id="lab_review_lab_id"></div>

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

<div class="lab-access-modal"><input type="hidden" name="id" id="id" value="10599"> <input type="hidden" name="user_id" id="user_id" value="4061609"> <input type="hidden" name="launch_with_credits" id="launch_with_credits" value="0" class="js-launch-with-credits-input"> <input type="hidden" name="launch_with_subs" id="launch_with_subs" value="0" class="js-launch-with-subscription-input">

<div class="lab-access-modal__method">

This lab costs 1 Credit.

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