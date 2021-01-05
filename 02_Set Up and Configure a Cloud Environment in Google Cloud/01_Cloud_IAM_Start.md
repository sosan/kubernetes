# Cloud IAM: Qwik Start

<div class="lab-preamble__details subtitle-headline-1"><span>45 minutes</span> <span>1 Credit</span>

<div class="lab__rating">[](/focuses/551/reviews?parent=catalog)<a data-target="#lab-review-modal" data-toggle="modal">Rate Lab</a></div>

</div>

</div>

<div class="js-markdown-instructions markdown-lab-instructions" id="markdown-lab-instructions">

## GSP064

![Google Cloud Self-Paced Labs](https://cdn.qwiklabs.com/GMOHykaqmlTHiqEeQXTySaMXYPHeIvaqa2qHEzw6Occ%3D)

## Overview

Google Cloud's Identity and Access Management (IAM) service lets you create and manage permissions for Google Cloud resources. Cloud IAM unifies access control for Google Cloud services into a single system and provides a consistent set of operations. In this hands-on lab you learn how to assign a role to a second user and remove assigned roles associated with Cloud IAM. More specifically, you sign in with 2 different sets of credentials to experience how granting and revoking permissions works from Google Cloud Project Owner and Viewer roles.

## Prerequisites

This is an **introductory level** lab. Little to no prior knowledge of Cloud IAM is expected. Experience with Cloud Storage is helpful to complete the tasks in this lab, but is not required. Make sure that you have a file in .txt or .html available. If you are looking for more advanced practice with Cloud IAM, be sure to check out the following lab:

*   [IAM Custom Roles](https://google.qwiklabs.com/catalog_lab/955)

Once you're prepared, scroll down and follow the steps to get your lab environment set up.

## Setup for two users

As mentioned earlier, this lab provides two sets of credentials to illustrate IAM policies and what permissions are available for specific roles.

In the panel on the left-hand side of your lab, you see a list of credentials that resembles the following:

![2_users.png](https://cdn.qwiklabs.com/HvRjPlDrsdpXJmdjfWpV1C%2B6i5wxGswMCs1%2F2bfkhkU%3D)

Notice that there are _two_ usernames: Username 1 and Username 2\. These represent identities in Cloud IAM, each with different access permissions allocated to them. These "roles" set constraints on what you can and cannot do with Google Cloud resources in the project you've been allocated.

### Sign in to Cloud Console as the first user

1.  Click on the **Open Google Console** button. This opens a new browser tab. If you are asked to **Choose an account**, click **Use another account**.
2.  The Google Cloud sign in page opens. A Sign in page opens—copy and paste the **Username 1** credential that resembles `googlexxxxxx_student@qwiklabs.net` into the "Email or phone" field and then click **Next**.
3.  Copy the password from the "Connection Details" panel and paste into the Google Sign in password field.
4.  Click **Next** and then **Accept** the terms of service. The Cloud Console opens. Agree to the terms of service and click **Agree and Continue**:

![sign-in.png](https://cdn.qwiklabs.com/dbhu7riX7d03aQy5lKVhaJXACS4nqZ5SpD5kx5TLqlE%3D)

### Sign in to Cloud Console as the second user

1.  Click on the **Open Google Console** button again. A new browser tab opens, if you are asked to **Choose an account**, click **Use another account**.
2.  The Google Cloud sign in page opens. Copy and paste the **Username 2** credential that resembles `googlexxxxxx_student@qwiklabs.net` into the **Email or phone** field and then click **Next**.
3.  Copy the password from the **Connection Details** panel and paste into the Google Sign in password field.
4.  Click **Next** and then **Accept** the terms of service. The Cloud Console opens. Agree to the terms of service and and click **Agree and Continue**:

![sign-in.png](https://cdn.qwiklabs.com/dbhu7riX7d03aQy5lKVhaJXACS4nqZ5SpD5kx5TLqlE%3D)

You should now have two Cloud Console tabs open in your browser—one signed in with Username 1 and the other with Username 2.

### View or reset the user in a browser tab

Occasionally, a user is overwritten in a browser tab or you may be confused about which user is signed into which browser tab.

To view which user in signed into a browser tab, hover over your Avatar to view your username in that browser tab.

![check-user.png](https://cdn.qwiklabs.com/oVLQ%2F9gR8aFyUYMrI6b9hKqby3vhYtRYslY7giSzpvU%3D)

To reset which user is signed into a browser tab:

1.  Click your Avatar and click **Sign out** to sign out.

2.  In the **Connection Details** panel, click **Open Google Console** and sign in back using the appropriate Username and Password.

## The IAM console and project level roles

1.  Return to the **Username 1** Cloud Console page.
2.  Select **Navigation menu** > **IAM & Admin** > **IAM**. You are now in the "IAM & Admin" console.
3.  Click **+ADD** button at the top of the page and explore the project roles associated with Projects by clicking on the "Select a role" dropdown menu:

![iam-roles.gif](https://cdn.qwiklabs.com/Fj2raSqxxSSaysd18h01Ky0DK7M%2BpUc%2FJsmmSDhvPno%3D)

You should see Browser, Editor, Owner, and Viewer roles. These four are known as _primitive roles_ in Google Cloud. Primitive roles set project-level permissions and unless otherwise specified, they control access and management to all Google Cloud services.

The following table pulls definitions from the [Google Cloud roles documentation](https://cloud.google.com/iam/docs/understanding-roles#primitive_roles), which gives a brief overview of browser, viewer, editor, and owner role permissions:

<table>

<tbody>

<tr>

<td colspan="1" rowspan="1">

**Role Name**

</td>

<td colspan="1" rowspan="1">

**Permissions**

</td>

</tr>

<tr>

<td colspan="1" rowspan="1">

roles/viewer

</td>

<td colspan="1" rowspan="1">

Permissions for read-only actions that do not affect state, such as viewing (but not modifying) existing resources or data.

</td>

</tr>

<tr>

<td colspan="1" rowspan="1">

roles/editor

</td>

<td colspan="1" rowspan="1">

All viewer permissions, plus permissions for actions that modify state, such as changing existing resources.

</td>

</tr>

<tr>

<td colspan="1" rowspan="1">

roles/owner

</td>

<td colspan="1" rowspan="1">

All editor permissions and permissions for the following actions:

*   Manage roles and permissions for a project and all resources within the project.
*   Set up billing for a project.

</td>

</tr>

<tr>

<td colspan="1" rowspan="1">

roles/browser (beta)

</td>

<td colspan="1" rowspan="1">

Read access to browse the hierarchy for a project, including the folder, organization, and Cloud IAM policy. This role doesn't include permission to view resources in the project.

</td>

</tr>

</tbody>

</table>

Since you are able to manage roles and permissions for this project, Username 1 has Project owner permissions.

1.  Click **CANCEL** to exit out of the "Add member" panel.

## Explore editor roles

Now switch to the **Username 2** console.

1.  Navigate to the IAM & Admin console, select **Navigation menu** > **IAM & Admin** > **IAM**.

2.  Search through the table to find Username 1 and Username 2 and examine the roles they are granted. You should see something like this:

![explore_editor.png](https://cdn.qwiklabs.com/MllO3Uv4Rnq5%2FT3Te6XMuRO5dJmy8ZINidMhMPEIvew%3D)

You should see:

*   Username 2 has the "Viewer" role granted to it.
*   The **+ADD** button at the top is grayed out—if you try to click on it you get the following message:

![more-permissions.png](https://cdn.qwiklabs.com/cNeK9TAhskx4dQPXm4lmHqPk9Vb14sS7KFHtmnW150A%3D)

This is one example of how IAM roles affect what you can and cannot do in Google Cloud.

1.  Switch back to the **Username 1** console for the next step.

## Prepare a resource for access testing

Ensure that you are in the **Username 1** Cloud Console.

### Create a bucket

1.  Create a Cloud Storage bucket with a unique name. From the Cloud Console, select **Navigation menu** > **Storage** > **Browser**.

2.  Click **Create bucket**.

<aside>**Note**: If you get a permissions error for bucket creation, sign out and then sign in back in with the Username 1 credentials.</aside>

1.  Update the following fields, leave all others at their default values:

<table>

<tbody>

<tr>

<td colspan="1" rowspan="1">

**Property**

</td>

<td colspan="1" rowspan="1">

**Value**

</td>

</tr>

<tr>

<td colspan="1" rowspan="1">

**Name**:

</td>

<td colspan="1" rowspan="1">

_globally unique name (create it yourself!) and click **Continue**._

</td>

</tr>

<tr>

<td colspan="1" rowspan="1">

**Location Type:**

</td>

<td colspan="1" rowspan="1">

Multi-Region

</td>

</tr>

</tbody>

</table>

Note the bucket name. You will use it in a later step.

1.  Click **Create.**

<aside>**Note**: If you get a permissions error for bucket creation, sign out and then sign in back in with the Username 1 credentials.</aside>

### **Upload a sample file**

1.  On the Bucket Details page click **Upload files** button.

2.  Browse your computer to find a file to use. Any text or html file will do.

3.  Click on the three dots at the end of the line containing the file and click **Rename**.

4.  Rename the file ‘`sample.txt`'.

5.  Click **Rename**.

Click **Check my progress** to verify the objective.

<ql-activity-tracking step="1">Create a bucket and upload a sample file</ql-activity-tracking>

### **Verify project viewer access**

1.  Switch to the **Username 2** console.

2.  From the Console, select **Navigation menu** > **Storage** > **Browser**. Verify that this user can see the bucket.

Username 2 has the "Viewer" role prescribed which allows them read-only actions that do not affect state. This example illustrates this feature—they can view Cloud Storage buckets and files that are hosted in the Google Cloud project that they've been granted access to.

## Remove project access

Switch to the **Username 1** console.

### **Remove Project Viewer for Username 2**

1.  Select **Navigation menu** > **IAM & Admin** > **IAM**. Then click the pencil icon next to **Username 2**.

<aside>You may have to widen the screen to see the pencil icon.</aside>

![remove_project_viewer.png](https://cdn.qwiklabs.com/qGvw8Krg5CsS%2FsfUgPI3swjP3LOiGcoN6N2INSQNF4s%3D)

1.  Remove Project Viewer access for **Username 2** by clicking the trashcan icon next to the role name. Then click **SAVE**.

![IAM_role_trash.png](https://cdn.qwiklabs.com/J3a%2BJeH31q7Sk1IFkOPBWSO13jbUkP3duf5nn%2F9rKp4%3D)

Notice that the user has disappeared from the list! The user has no access now.

<aside>**Note**: It can take up to 80 seconds for such a change to take effect as it propagates. Read more [here](https://cloud.google.com/iam/docs/faq).</aside>

### **Verify that Username 2 has lost access**

1.  Switch to **Username 2** Cloud Console. Ensure that you are still signed in with Username 2's credentials and that you haven't been signed out of the project after permissions were revoked. If signed out, sign in back with the proper credentials.

2.  Navigate back to Cloud Storage by selecting **Navigation menu** > **Storage** > **Browser**.

You should see a permission error.

<aside>**Note**: As mentioned before, it can take up to 80 seconds for permissions to be revoked. If you haven't received a permission error, wait a 2 minutes and then try refreshing the console.</aside>

Click **Check my progress** to verify the objective.

<ql-activity-tracking step="2">Remove project access</ql-activity-tracking>

## Add Storage permissions

1.  Copy **Username 2** name from the Qwiklabs "Connection Details" panel.

![2_users.png](https://cdn.qwiklabs.com/HvRjPlDrsdpXJmdjfWpV1C%2B6i5wxGswMCs1%2F2bfkhkU%3D)

1.  Switch to **Username 1** console. Ensure that you are still signed in with Username 1's credentials. If you are signed out, sign in back with the proper credentials.

2.  In the Console, select **Navigation menu** > **IAM & Admin** > **IAM**.

3.  Click **+ ADD** button and paste the **Username 2** name into the New members field.

4.  In the **Roles** field, select **Cloud Storage** > **Storage Object Viewer** from the drop-down menu.

5.  Click **SAVE**.

## Verify access

1.  Switch to the **Username 2** console. You'll still be on the Storage page.

**Username 2** doesn't have the Project Viewer role, so that user can't see the project or any of its resources in the Console. However, this user has specific access to Cloud Storage, the Storage Object Viewer role - check it out now.

1.  Click the **Activate Cloud Shell** icon to open the Cloud Shell command line.

![start-cloud-shell.png](https://cdn.qwiklabs.com/MwTIT1EPNZG9oo8k53JUFkWo3YG2G0Uk31h9Tfhh4M8%3D)

1.  Open up a Cloud Shell session and then enter in the following command, replace `[YOUR_BUCKET_NAME]` with the name of the bucket you created earlier:

    gsutil ls gs://[YOUR_BUCKET_NAME]

You should receive a similar output:

    gs://[YOUR_BUCKET_NAME]/sample.txt

1.  As you can see, you gave **Username 2** view access to the Cloud Storage bucket.

Click **Check my progress** to verify the objective.

<ql-activity-tracking step="3">Add Storage permissions</ql-activity-tracking>

## Congratulations!

You've completed the lab. In this lab you exercised granting and revoking Cloud IAM roles to a user.

![5edc9b3763bbb596.png](https://cdn.qwiklabs.com/6EiXINl%2FCnCMQyRdF%2Fs34InkRozTg%2FhSEqCQ4CD4nf4%3D) ![SecurityandIdentity_125.png](https://cdn.qwiklabs.com/%2FEzSUI369NAcLdtb5VyfU4a36AGdiWpaU3srdPddAm0%3D) ![CloudEngineeringExaPrep_125.png](https://cdn.qwiklabs.com/jVIZEu1uWCkufNtCmqyElRifK5o%2FNRrtt5Vo9iZxHz8%3D)

### Finish your Quest

This self-paced lab is part of the [Security & Identity Fundamentals](https://google.qwiklabs.com/quests/40), [Baseline: Infrastructure](https://google.qwiklabs.com/quests/33), and [Cloud Engineering](https://google.qwiklabs.com/quests/66) Quests. A Quest is a series of related labs that form a learning path. Completing this Quest earns you the badge above, to recognize your achievement. You can make your badges public and link to them in your online resume or social media account. Enroll in a Quest and get immediate completion credit if you've taken this lab. [See other available Qwiklabs Quests](http://google.qwiklabs.com/catalog).

### Next steps / learn more

This lab is also part of a series of labs called Qwik Starts. These labs are designed to give you a little taste of the many features available with Google Cloud. Search for "Qwik Starts" in the [lab catalog](https://google.qwiklabs.com/catalog) to find the next lab you'd like to take!

### Google Cloud Training & Certification

...helps you make the most of Google Cloud technologies. [Our classes](https://cloud.google.com/training/courses) include technical skills and best practices to help you get up to speed quickly and continue your learning journey. We offer fundamental to advanced level training, with on-demand, live, and virtual options to suit your busy schedule. [Certifications](https://cloud.google.com/certification/) help you validate and prove your skill and expertise in Google Cloud technologies.

##### Manual last updated November 25, 2020

##### Lab last tested November 25, 2020

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

<div class="js-lab-content-outline lab-content__outline">[GSP064](#step1)[Overview](#step2)[Prerequisites](#step3)[Setup for two users](#step4)[The IAM console and project level roles](#step5)[Explore editor roles](#step6)[Prepare a resource for access testing](#step7)[Remove project access](#step8)[Add Storage permissions](#step9)[Verify access](#step10)[Congratulations!](#step11)</div>

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

Google Cloud IAM unifies access control for Cloud Platform services into a single system to present a consistent set of operations. Watch the short video [Manage Access Control with Google Cloud IAM](https://youtu.be/PqMGmRhKsnM).

This lab is included in these quests: [Baseline: Infrastructure](/quests/33), [Perform Foundational Infrastructure Tasks in Google Cloud](/quests/118), [Cloud Architecture](/quests/24), [Cloud Engineering](/quests/66), [Set Up and Configure a Cloud Environment in Google Cloud](/quests/119), [Security & Identity Fundamentals](/quests/40). If you complete this lab you'll receive credit for it when you enroll in one of these quests.

**Duration:** 0m setup · 45m access · 45m completion

<span>**Levels:** introductory</span>

**Permalink:** [https://google.qwiklabs.com/catalog_lab/686](https://google.qwiklabs.com/catalog_lab/686)

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

<div class="controls"><input class="hidden" type="hidden" value="686" name="lab_review[lab_id]" id="lab_review_lab_id"></div>

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

<div class="lab-access-modal"><input type="hidden" name="id" id="id" value="551"> <input type="hidden" name="user_id" id="user_id" value="4061609"> <input type="hidden" name="launch_with_credits" id="launch_with_credits" value="0" class="js-launch-with-credits-input"> <input type="hidden" name="launch_with_subs" id="launch_with_subs" value="0" class="js-launch-with-subscription-input">

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