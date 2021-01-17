# Build and Secure Networks in Google Cloud: Challenge Lab

1 hour 9 Credits

## GSP322

![Google Cloud Self-Paced Labs](https://cdn.qwiklabs.com/GMOHykaqmlTHiqEeQXTySaMXYPHeIvaqa2qHEzw6Occ%3D)

## Overview

For this Challenge Lab you must complete a series of tasks within a limited time period. Instead of following step-by-step instructions, you'll be given a scenario and task - you figure out how to complete it on your own! An automated scoring system (shown on this page) will provide feedback on whether you have completed your tasks correctly.

To score 100% you must complete all tasks within the time period!

When you take a Challenge Lab, you will not be taught Google Cloud concepts. You'll need to use your advanced Compute Engine and general Google Cloud skills to assess how to build the solution to the challenge presented. This lab is only recommended for students who have advanced Google Cloud and Compute Engine skills. Are you up for the challenge?

#### Topics tested

*   Secure remote ssh access via IAP-enabled bastion
*   Firewall configuration and review

### Prerequisites

*   Familiarity with VPC Networks
*   Firewall rules and network tags
*   IAP

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

## Challenge scenario

You are a security consultant brought in by a Jeff, who owns a small local company, to help him with his very successful website (juiceshop). Jeff is new to Google Cloud and had his neighbour's son set up the initial site. The neighbour's son has since had to leave for college, but before leaving, he made sure the site was running.

You need to help out Jeff and perform appropriate configuration for security. Below is the current situation:

![d1d45b80d0f8dec8.png](https://cdn.qwiklabs.com/qEwFTP7%2FkyF3cRwfT3FGObt7L7VLB60%2Bvp92hZVnogw%3D)

## Your challenge

You need to configure this simple environment securely. Your first challenge is to set up appropriate firewall rules and virtual machine tags. You also need to ensure that SSH is only available to the bastion via IAP.

For the firewall rules, make sure:

*   The bastion host does not have a public IP address.
*   You can only SSH to the bastion and only via IAP.
*   You can only SSH to juice-shop via the bastion.
*   Only HTTP is open to the world for `juice-shop`.

Tips and tricks:

*   Pay close attention to the network tags and the associated VPC firewall rules.
*   Be specific and limit the size of the VPC firewall rule source ranges.
*   Overly permissive permissions will not be marked correct.

![93b315830f8a2a1e.png](https://cdn.qwiklabs.com/BgxgsuLyqMkhxmO3jDlkHE7yGLIR%2B3rrUabKimlgrbo%3D)

Suggested order of actions:

1.  Check the firewall rules. Remove the overly permissive rules.

Remove the overly permissive rules

1.  Navigate to Compute Engine in the Cloud Console and identify the bastion host. The instance should be stopped. Start the instance.

Start the bastion host instance

1.  The bastion host is the one machine authorized to receive external SSH traffic. Create a firewall rule that allows [SSH (tcp/22) from the IAP service](https://cloud.google.com/iap/docs/using-tcp-forwarding). The firewall rule should be enabled on bastion via a network tag.

Create a firewall rule that allows SSH (tcp/22) from the IAP service and add network tag on bastion

1.  The `juice-shop` server serves HTTP traffic. Create a firewall rule that allows traffic on HTTP (tcp/80) to any address. The firewall rule should be enabled on juice-shop via a network tag.

Create a firewall rule that allows traffic on HTTP (tcp/80) to any address and add network tag on juice-shop

1.  You need to connect to `juice-shop` from the bastion using SSH. Create a firewall rule that allows traffic on SSH (tcp/22) from `acme-mgmt-subnet` network address. The firewall rule should be enabled on `juice-shop` via a network tag.

Create a firewall rule that allows traffic on SSH (tcp/22) from acme-mgmt-subnet

1.  In the Compute Engine instances page, click the SSH button for the bastion host. Once connected, SSH to `juice-shop`.

SSH to bastion host via IAP and juice-shop via bastion

## Congratulations!

You've completed the challenge lab and helped Jeff tighten security.

![skills_final_build_and_secure.png](https://cdn.qwiklabs.com/YT4cAR7PzbWucmzITtcowvaBG%2F7m4M%2FUFMoPuZTeL2Y%3D)

### Take your next lab

This lab is also part of a series of labs called Challenge Labs. These labs are designed test your Google Cloud knowledge and skill. Search for "Challenge Lab" in the [lab catalog](http://google.qwiklabs.com/catalog) and challenge yourself!

### Google Cloud Training & Certification

...helps you make the most of Google Cloud technologies. [Our classes](https://cloud.google.com/training/courses) include technical skills and best practices to help you get up to speed quickly and continue your learning journey. We offer fundamental to advanced level training, with on-demand, live, and virtual options to suit your busy schedule. [Certifications](https://cloud.google.com/certification/) help you validate and prove your skill and expertise in Google Cloud technologies.

##### Manual Last Updated October 26, 2020

##### Manual Last Tested October 26, 2020

Copyright 2020 Google LLC All rights reserved. Google and the Google logo are trademarks of Google LLC. All other company and product names may be trademarks of the respective companies with which they are associated.

</div>

</div>