# Predict Housing Prices with Tensorflow and AI Platform

<div class="lab-preamble__details subtitle-headline-1"><span>1 hour 30 minutes</span> <span>7 Credits</span>

<div class="lab__rating">[](/focuses/3644/reviews?parent=catalog)<a data-target="#lab-review-modal" data-toggle="modal">Rate Lab</a></div>

</div>

</div>

<div class="js-markdown-instructions markdown-lab-instructions" id="markdown-lab-instructions">

## GSP418

![Google Cloud Self-Paced Labs](https://cdn.qwiklabs.com/GMOHykaqmlTHiqEeQXTySaMXYPHeIvaqa2qHEzw6Occ%3D)

## Overview

In this lab, you will build an end to end machine learning solution using Tensorflow 1.x and AI Platform and leverage the cloud for distributed training and online prediction.

[tf.estimator](https://www.tensorflow.org/api_docs/python/tf/estimator) — a high-level TensorFlow API that greatly simplifies machine learning programming. Estimators encapsulate the following actions:

*   training
*   evaluation
*   prediction
*   export for serving

This lab is focused on interacting with Jupyter and AI Platform. Non-relevant concepts and code blocks are glossed over and are provided for you to execute in your Jupyter notebook.

## Set up

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

## Create Storage Bucket

Create a bucket using the Cloud Console:

1.  Click on the **Navigation menu**, and select **Storage**.

2.  Click on **Create bucket**.

3.  Set a unique name (use your project ID because it is unique) and then choose a regional bucket, setting the region to `us-central1`. Then, click **Create**.

Click **Check my progress** to verify your performed task. If you have completed the task successfully you will be granted with an assessment score.

<ql-activity-tracking step="1">Create Storage Bucket</ql-activity-tracking>

## Launch AI Platform Notebooks

To launch AI Platform Notebooks:

**Step 1**

Click on the **Navigation Menu**. Navigate to **AI Platform**, then to **Notebooks**.

![Open new notebook](https://cdn.qwiklabs.com/jH5%2FIspr88moxsjUKc2TQdpRvqUfNvj098DG3ZRQe%2B4%3D)

**Step 2**

On the Notebook instances page, click ![NEW INSTANCE](https://cdn.qwiklabs.com/YI0InqyQhTRNsEIGzrufyXjMtsdrwKwspeNXtPlPPeY%3D). Select a `1.XX` version of TensorFlow (not 2.0) _without GPUs_. In the following example, you would select **Tensorflow Enterprise 1.15** > **Without GPUs**:

![New instance, TensorFlow 1.x](https://cdn.qwiklabs.com/5%2BwKu1qmMIZOnkr3dVugTSjyWPWKLEESHJOPKM%2BSbos%3D)

Tensorflow 1.XX versions change semi-frequently, so the version you pick may be different.

In the pop-up, confirm the name of the deep learning VM and click **Create**.

![Create new VM](https://cdn.qwiklabs.com/KVOV3Q70ahsDN9accf6NIsZTtGNKpi5ThwrRSmRBod8%3D)

The new VM will take 2-3 minutes to start.

**Step 3**

Click **Open JupyterLab**. A JupyterLab window will open in a new tab.

![JupyterLab](https://cdn.qwiklabs.com/pwhtoT2OOAq8DamQHYjk1TYcoAuOQTwkoYM5qglV1AQ%3D)

Click **Check my progress** to verify your performed task. If you have completed the task successfully you will be granted with an assessment score.

<ql-activity-tracking step="2">Create the AI Platform notebook instance</ql-activity-tracking>

## Download lab notebook

To clone the `training-data-analyst` notebook in your JupyterLab instance:

1.  In JupyterLab, click the **Terminal** icon to open a new terminal.

![Open Terminal](https://cdn.qwiklabs.com/rSJUVtqbDlE28I3g1GyCXTkPI2nFhq1oA%2FXXMISaSdQ%3D)

1.  At the command-line prompt, type in the following command and press Enter.

    git clone https://github.com/GoogleCloudPlatform/training-data-analyst

1.  Confirm that you have cloned the repository by double clicking on the `training-data-analyst` directory and ensuring that you can see its contents. The files for all the Jupyter notebook-based labs throughout this course are available in this directory.

![Training data analyst repository](https://cdn.qwiklabs.com/viCi2TfN6FSS9PR2xkTx4S59n2yPDDWf4lHgn79Chy4%3D)

Click **Check my progress** to verify your performed task. If you have completed the task successfully you will be granted with an assessment score.

<ql-activity-tracking step="3">Download lab notebook</ql-activity-tracking>

## Open and execute the housing prices notebook

From within the Jupyter console, select **training-data-analyst > blogs > housing_prices > cloud-ml-housing-prices.ipynb** to begin the lab. Now you're ready to start!

In the top ribbon, click **Edit** > **Clear All Outputs**.

From the top right corner, select **Python 2** and change it to **Python 3**.

From here, read the instructions in the notebook to complete the lab.

Execute the cells one by one and observe the results. A convenient way to progress through the cells is by clicking in a cell, then click `Shift + Enter`, waiting for each cell to complete before progressing.

Read the instructions and the comments in the code blocks carefully. You will be asked to edit some of the code blocks before running them. For example, you will be setting environment variables in the notebook, so add your bucket name and project ID before running the cell.

Click **Check my progress** to verify your performed task. If you have completed the task successfully you will be granted with an assessment score.

<ql-activity-tracking step="4">Train and deploy the Model for Predictions</ql-activity-tracking>

## Congratulations!

You have used Tensorflow's high level Estimator API to deploy Tensorflow 1.x code for distributed training in the cloud and evaluated the results, then deployed the model to the cloud for online prediction.

![Data_Engineering_badge_125.png](https://cdn.qwiklabs.com/XFebDQwepOTnzaxXtr01EzuRLa43wF9q3%2BaQtsE52GM%3D) ![ML-TensorFlow-on-GCP-badge.png](https://cdn.qwiklabs.com/dlaeVPgDRCgDl3xR2zclXT%2Fldb6%2BzZn%2BvRdxjSrUPhg%3D)

### Finish Your Quest

This self-paced lab is part of the Qwiklabs [Data Engineering](https://google.qwiklabs.com/quests/25) and [Intermediate ML: TensorFlow on Google Cloud](https://google.qwiklabs.com/quests/83) Quests. A Quest is a series of related labs that form a learning path. Completing a Quest earns you a badge to recognize your achievement. You can make your badge (or badges) public and link to them in your online resume or social media account. Enroll in one of the above Quests and get immediate completion credit if you've taken this lab. [See other available Qwiklabs Quests](https://google.qwiklabs.com/catalog).

### Take Your Next Lab

Continue your Quest with [Cloud Composer: Copy BigQuery Tables Across Different Locations](https://google.qwiklabs.com/catalog_lab/1418), or check out these suggestions:

*   [Build an IoT Analytics Pipeline on Google Cloud](https://google.qwiklabs.com/catalog_lab/694)

*   [ETL Processing on Google Cloud using dataflow and BigQuery](https://google.qwiklabs.com/catalog_lab/1341)

### Next steps / learn more

Check out official documentation on:

*   If you're interested more in-depth training, try the [Cloud ML coursera labs](https://www.coursera.org/specializations/machine-learning-tensorflow-gcp).
*   [Jupyter Notebook Basics](http://jupyter-notebook.readthedocs.io/en/latest/examples/Notebook/Notebook%20Basics.html) (Datalab is based on Jupyter)
*   [tf.estimator](https://www.tensorflow.org/guide/estimators)

Check out this talk from Google Cloud Next '17 on [advanced data science on Google Cloud](https://www.youtube.com/watch?v=Jp-qJFF9jww&list=PLIivdWyY5sqLq-eM4W2bIgbrpAsP5aLtZ) (43:15).

### Google Cloud Training & Certification

...helps you make the most of Google Cloud technologies. [Our classes](https://cloud.google.com/training/courses) include technical skills and best practices to help you get up to speed quickly and continue your learning journey. We offer fundamental to advanced level training, with on-demand, live, and virtual options to suit your busy schedule. [Certifications](https://cloud.google.com/certification/) help you validate and prove your skill and expertise in Google Cloud technologies.

##### Manual Last Updated July 20, 2020

##### Lab Last Tested July 20, 2020

Copyright 2020 Google LLC All rights reserved. Google and the Google logo are trademarks of Google LLC. All other company and product names may be trademarks of the respective companies with which they are associated.

</div>

</div>

<div class="hidden js-end-lab-button-container lab-content__end-lab-button"><ql-lab-control-button class="js-end-lab-button" running=""></ql-lab-control-button></div>

<div class="lab-content__renderable-instructions">

<div class="lab-content__recommendation">

<section class="upcoming-cards">

## Continue questing

<div class="content-card-grid">

<div class="card-content-wrapper js-content-card" data-id="1062" data-level="Advanced" data-name="Creating a Data Transformation Pipeline with Cloud Dataprep" data-type="Lab">[

<div class="card__body">

<div class="overline card--content__type">Hands-On Lab</div>

### Creating a Data Transformation Pipeline with Cloud Dataprep

Cloud Dataprep by Trifacta is an intelligent data service for visually exploring, cleaning, and preparing structured and unstructured data for analysis. In this lab you will explore the Cloud Dataprep UI to build a data transformation pipeline.

</div>

<div class="card__footer">

<div class="card__footer__left"><span>Advanced</span></div>

</div>

](/focuses/4415?parent=catalog)</div>

</div>

</section>

</div>

</div>

</ql-drawer-content><ql-drawer end="" id="outline-drawer" open="" slot="drawer" width="320">

<div class="js-lab-content-outline lab-content__outline">[GSP418](#step1)[Overview](#step2)[Set up](#step3)[Create Storage Bucket](#step4)[Launch AI Platform Notebooks](#step5)[Download lab notebook](#step6)[Open and execute the housing prices notebook](#step7)[Congratulations!](#step8)</div>

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

In this lab you will build an end to end machine learning solution using Tensorflow + AI Platform and leverage the cloud for distributed training and online prediction.

This lab is included in these quests: [Data Engineering](/quests/25), [Engineer Data in Google Cloud](/quests/132), [Intermediate ML: TensorFlow on GCP](/quests/83). If you complete this lab you'll receive credit for it when you enroll in one of these quests.

**Duration:** 0m setup · 90m access · 90m completion

<span>**Levels:** advanced</span>

**Permalink:** [https://google.qwiklabs.com/catalog_lab/1468](https://google.qwiklabs.com/catalog_lab/1468)

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

<div class="controls"><input class="hidden" type="hidden" value="1468" name="lab_review[lab_id]" id="lab_review_lab_id"></div>

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

<div class="lab-access-modal"><input type="hidden" name="id" id="id" value="3644"> <input type="hidden" name="user_id" id="user_id" value="4061609"> <input type="hidden" name="launch_with_credits" id="launch_with_credits" value="0" class="js-launch-with-credits-input"> <input type="hidden" name="launch_with_subs" id="launch_with_subs" value="0" class="js-launch-with-subscription-input">

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