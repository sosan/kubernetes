# ETL Processing on Google Cloud Using Dataflow and BigQuery

<div class="lab-preamble__details subtitle-headline-1"><span>1 hour</span> <span>7 Credits</span>

<div class="lab__rating">[](/focuses/3460/reviews?parent=catalog)<a data-target="#lab-review-modal" data-toggle="modal">Rate Lab</a></div>

</div>

</div>

<div class="js-markdown-instructions markdown-lab-instructions" id="markdown-lab-instructions">

## GSP290

![Google Cloud Self-Paced Labs](https://cdn.qwiklabs.com/GMOHykaqmlTHiqEeQXTySaMXYPHeIvaqa2qHEzw6Occ%3D)

## Overview

In this lab you build several Data Pipelines that ingest data from a publicly available dataset into BigQuery, using these Google Cloud services:

*   **Cloud Storage**
*   **Dataflow**
*   **BigQuery**

You will create your own Data Pipeline, including the design considerations, as well as implementation details, to ensure that your prototype meets the requirements. Be sure to open the python files and read the comments when instructed to.

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

### Check project permissions

Before you begin your work on Google Cloud, you need to ensure that your project has the correct permissions within Identity and Access Management (IAM).

1.  In the Google Cloud console, on the **Navigation menu** (![nav-menu.png](https://cdn.qwiklabs.com/ITyhQn00xPCVZIksELAC4wax%2BQdPdrH2DNGLazXJKgw%3D)), click **IAM & Admin** > **IAM**.

2.  Confirm that the default compute Service Account `{project-number}-compute@developer.gserviceaccount.com` is present and has the `editor` role assigned. The account prefix is the project number, which you can find on **Navigation menu** > **Home**.

![check-sa.png](https://cdn.qwiklabs.com/PaSJwLiQ6db41cptCuYECEHZtayQHGZpUJaR0fLIqTw%3D)

If the account is not present in IAM or does not have the `editor` role, follow the steps below to assign the required role.

*   In the Google Cloud console, on the **Navigation menu**, click **Home**.

*   Copy the project number (e.g. `729328892908`).

*   On the **Navigation menu**, click **IAM & Admin** > **IAM**.

*   At the top of the **IAM** page, click **Add**.

*   For **New members**, type:

    {project-number}-compute@developer.gserviceaccount.com

Replace `{project-number}` with your project number.

*   For **Role**, select **Project** (or Basic) > **Editor**. Click **Save**.

![add-sa.png](https://cdn.qwiklabs.com/TJdJBvKxMtTArw6djkzJinwSxZq%2BYa7fwXf54ROeHUM%3D)

## Download the starter code

Open a session in Cloud Shell and run the following command to get Dataflow Python Examples from [Google Cloud's professional services GitHub](https://github.com/GoogleCloudPlatform/professional-services/blob/master/examples/dataflow-python-examples/README.md):

    gsutil -m cp -R gs://spls/gsp290/dataflow-python-examples .

Now set a variable equal to your project id, replacing `<YOUR-PROJECT-ID>` with your lab Project ID:

    export PROJECT=<YOUR-PROJECT-ID>

    gcloud config set project $PROJECT

## Create Cloud Storage Bucket

Use the make bucket command to create a new regional bucket in the `us-central1` region within your project:

    gsutil mb -c regional -l us-central1 gs://$PROJECT

#### Test Completed Task

Click **Check my progress** to verify your performed task.

<ql-activity-tracking step="1">Create a Cloud Storage Bucket</ql-activity-tracking>

## Copy Files to Your Bucket

Use the `gsutil` command to copy files to the Cloud Storage bucket you just created:

    gsutil cp gs://spls/gsp290/data_files/usa_names.csv gs://$PROJECT/data_files/
    gsutil cp gs://spls/gsp290/data_files/head_usa_names.csv gs://$PROJECT/data_files/

#### Test Completed Task

Click **Check my progress** to verify your performed task.

<ql-activity-tracking step="2">Copy Files to Your Bucket</ql-activity-tracking>

## Create the BigQuery Dataset

Create a dataset in BigQuery called `lake`. This is where all of your tables will be loaded in BigQuery:

    bq mk lake

#### Test Completed Task

Click **Check my progress** to verify your performed task.

<ql-activity-tracking step="3">Create the BigQuery Dataset (name: lake)</ql-activity-tracking>

## Build a Dataflow Pipeline

In this section you will create an append-only Dataflow which will ingest data into the BigQuery table. You can use the built-in code editor which will allow you to view and edit the code in the Google Cloud console.

![3b1ddc75045cae6b.png](https://cdn.qwiklabs.com/xu2ZNOCZ0IgdlDNdPrmJN%2FDIpdFiPVPCSstHGOYHeDM%3D)

### Open Code Editor

Navigate to the source code by clicking on the **Open Editor** icon in Cloud Shell:

![editor.png](https://cdn.qwiklabs.com/jBG2dXK1ZILP2ZKKw0EmMovNWjE5JecLn277gF6wy70%3D)

If prompted click on **Open in New Window**. It will open the code editor in new window.

## Data Ingestion

You will now build a Dataflow pipeline with a TextIO source and a BigQueryIO destination to ingest data into BigQuery. More specifically, it will:

*   Ingest the files from Cloud Storage.
*   Filter out the header row in the files.
*   Convert the lines read to dictionary objects.
*   Output the rows to BigQuery.

## Review pipeline python code

In the Code Editor navigate to `dataflow-python-examples` > `dataflow_python_examples` and open the `data_ingestion.py` file. Read through the comments in the file, which explain what the code is doing. This code will populate the data in BigQuery.

![21efa9aaf4e0d11.png](https://cdn.qwiklabs.com/wRFYQrAqdsaCRDuCheahYj6OJeCti0a1%2BWAwuqYtZRE%3D)

## Run the Apache Beam Pipeline

Return to your Cloud Shell session for this step. You will now do a bit of set up for the required python libraries.

Run the following to set up the python environment:

    cd dataflow-python-examples/
    # Here we set up the python environment.
    # Pip is a tool, similar to maven in the java world
    sudo pip install virtualenv

    #Dataflow requires python 3.7
    virtualenv -p python3 venv

    source venv/bin/activate
    pip install apache-beam[gcp]==2.24.0

You will run the Dataflow pipeline in the cloud.

The following will spin up the workers required, and shut them down when complete:

    python dataflow_python_examples/data_ingestion.py --project=$PROJECT --region=us-central1 --runner=DataflowRunner --staging_location=gs://$PROJECT/test --temp_location gs://$PROJECT/test --input gs://$PROJECT/data_files/head_usa_names.csv --save_main_session

Return to the Cloud Console and open the **Navigation menu** > **Dataflow** to view the status of your job.

![df.png](https://cdn.qwiklabs.com/u24oPPlz4rhEkhjeMCa7yjSdXvyg0cP1xZQMKMR0CFA%3D)

Click on the name of your job to watch it's progress. Once your **Job Status** is **Succeeded**.

Navigate to BigQuery (**Navigation menu** > **BigQuery**) see that your data has been populated.

![bq.png](https://cdn.qwiklabs.com/qzX6vFvEIqqkzx%2BFlrwQJy5IB7ACpR4yTxON%2BG9B%2F3M%3D)

Click on your project name to see the **usa_names** table under the `lake` dataset.

![bq_lake_one.png](https://cdn.qwiklabs.com/WLaavXMsSZkeqgq%2B3zHLhFzYjz3Spu5X0tCEkFpjrRo%3D)

Click on the table then navigate to the **Preview** tab to see examples of the `usa_names` data.

<ql-infobox>**Note:** If you don't see the `usa_names` table, try refreshing the page or view the tables using the classic BigQuery UI.</ql-infobox>

### Test Completed Task

Click **Check my progress** to verify your performed task.

<ql-activity-tracking step="4">Build a Data Ingestion Dataflow Pipeline</ql-activity-tracking>

## Data Transformation

You will now build a Dataflow pipeline with a TextIO source and a BigQueryIO destination to ingest data into BigQuery. More specifically, you will:

*   Ingest the files from Cloud Storage.
*   Convert the lines read to dictionary objects.
*   Transform the data which contains the year to a format BigQuery understands as a date.
*   Output the rows to BigQuery.

### Review pipeline python code

Navigate to `data_transformation.py` and open it in Code Editor. Read through the comments in the file which explain what the code is doing.

## Run the Apache Beam Pipeline

You will run the Dataflow pipeline in the cloud. This will spin up the workers required, and shut them down when complete.

Run the following commands to do so:

    python dataflow_python_examples/data_transformation.py --project=$PROJECT --region=us-central1 --runner=DataflowRunner --staging_location=gs://$PROJECT/test --temp_location gs://$PROJECT/test --input gs://$PROJECT/data_files/head_usa_names.csv --save_main_session

Navigate to **Navigation menu** > **Dataflow** and click on the name of this job view the status.

When your **Job Status** is **Succeeded** in the Dataflow Job Status screen, navigate to BigQuery to check to see that your data has been populated.

You should see the **usa_names_transformed** table under the lake dataset.

Click on the table and navigate to the **Preview** tab to see examples of the `usa_names_transformed` data.

<ql-infobox>**Note:** If you don't see the `usa_names_transformed` table, try refreshing the page or view the tables using the classic BigQuery UI.</ql-infobox>

#### Test Completed Task

Click **Check my progress** to verify your performed task.

<ql-activity-tracking step="5">Build a Data Transformation Dataflow Pipeline</ql-activity-tracking>

## Data Enrichment

You will now build a Dataflow pipeline with a TextIO source and a BigQueryIO destination to ingest data into BigQuery. More specifically, you will:

*   Ingest the files from Cloud Storage.
*   Filter out the header row in the files.
*   Convert the lines read to dictionary objects.
*   Output the rows to BigQuery.

## Review pipeline python code

Navigate to `data_enrichment.py` and open it in Code Editor. Check out the comments which explain what the code is doing. This code will populate the data in BigQuery.

Line 83 currently looks like:

    values = [x.decode('utf8') for x in csv_row]

Edit it so it looks like the following:

    values = [x for x in csv_row]

## Run the Apache Beam Pipeline

Here you'll run the Dataflow pipeline in the cloud. Run the following to spin up the workers required, and shut them down when complete:

    python dataflow_python_examples/data_enrichment.py --project=$PROJECT --region=us-central1 --runner=DataflowRunner --staging_location=gs://$PROJECT/test --temp_location gs://$PROJECT/test --input gs://$PROJECT/data_files/head_usa_names.csv --save_main_session

Navigate to **Navigation menu** > **Dataflow** to view the status of your job.

Once your **Job Status** is **Succeed** in the Dataflow Job Status screen, navigate to BigQuery to check to see that your data has been populated.

You should see the **usa_names_enriched** table under the lake dataset.

Click on the table and navigate to the Preview tab to see examples of data for the data.

<ql-infobox>**Note:** If you don't see the `usa_names_enriched` table, try refreshing the page or view the tables using the classic BigQuery UI.</ql-infobox>

#### Test Completed Task

Click **Check my progress** to verify your performed task.

<ql-activity-tracking step="6">Build a Data Enrichment Dataflow Pipeline</ql-activity-tracking>

## Data lake to Mart

Now build a Dataflow pipeline that reads data from 2 BigQuery data sources, and then joins the data sources. Specifically, you:

*   Ingest files from 2 BigQuery sources.
*   Join the 2 data sources.
*   Filter out the header row in the files.
*   Convert the lines read to dictionary objects.
*   Output the rows to BigQuery.

## Review pipeline python code

Navigate to `data_lake_to_mart.py` and open it in Code Editor. Read through the comments in the file which explain what the code is doing. This code will populate the data in BigQuery.

## Run the Apache Beam Pipeline

Now you'll run the Dataflow pipeline in the cloud. Run the following to spin up the workers required, and shut them down when complete:

    python dataflow_python_examples/data_lake_to_mart.py --worker_disk_type="compute.googleapis.com/projects//zones//diskTypes/pd-ssd" --max_num_workers=4 --project=$PROJECT --runner=DataflowRunner --staging_location=gs://$PROJECT/test --temp_location gs://$PROJECT/test --save_main_session --region=us-central1

Navigate to **Navigation menu** > **Dataflow** and click on the name of this new job to view the status.

Once you've **Job Status** is **Succeeded** in the Dataflow Job Status screen, navigate to BigQuery to check to see that your data has been populated.

You should see the **orders_denormalized_sideinput** table under the lake dataset.

Click on the table and navigate to the **Preview** section to see examples of orders_denormalized_sideinput data.

<ql-infobox>**Note:** If you don't see the `orders_denormalized_sideinput` table, try refreshing the page or view the tables using the classic BigQuery UI.</ql-infobox>

#### Test Completed Task

Click **Check my progress** to verify your performed task.

<ql-activity-tracking step="7">Build a Data lake to Mart Dataflow Pipeline</ql-activity-tracking>

## Test your Understanding

Below are a multiple choice questions to reinforce your understanding of this lab's concepts. Answer them to the best of your abilities.

## Congratulations!

You have used python files to ingest data into BigQuery using Dataflow.

### Finish your Quest

![Data_Engineering_badge_125.png](https://cdn.qwiklabs.com/XFebDQwepOTnzaxXtr01EzuRLa43wF9q3%2BaQtsE52GM%3D)

This self-paced lab is part of the Qwiklabs [Data Engineering Quest](https://google.qwiklabs.com/quests/25). A Quest is a series of related labs that form a learning path. Completing this Quest earns you the badge above, to recognize your achievement. You can make your badge (or badges) public and link to them in your online resume or social media account. Enroll in this Quest and get immediate completion credit if you've taken this lab. See other available [Qwiklabs Quests](https://google.qwiklabs.com/catalog).

### Take Your Next Lab

Continue your Quest with [Predict Visitor Purchases with a Classification Model in BQML](https://google.qwiklabs.com/catalog_lab/1101), or check out these suggestions:

*   [Predict Housing Prices with Tensorflow and AI Platform](https://google.qwiklabs.com/catalog_lab/1468)

*   [Cloud Composer: Copy BigQuery Tables Across Different Locations](https://google.qwiklabs.com/catalog_lab/1418)

### Next steps / learn more

Looking for more? Check out official documentation on:

*   [Google Dataflow](https://cloud.google.com/dataflow/)
*   [BigQuery](https://cloud.google.com/bigquery/)
*   Also review the [Apache Beam Programming Guide](https://beam.Apache.org/documentation/programming-guide/) for more advanced concepts.

### Google Cloud Training & Certification

...helps you make the most of Google Cloud technologies. [Our classes](https://cloud.google.com/training/courses) include technical skills and best practices to help you get up to speed quickly and continue your learning journey. We offer fundamental to advanced level training, with on-demand, live, and virtual options to suit your busy schedule. [Certifications](https://cloud.google.com/certification/) help you validate and prove your skill and expertise in Google Cloud technologies.

##### Manual Last Updated October 26, 2020

##### Lab Last Tested October 26, 2020

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

<div class="js-lab-content-outline lab-content__outline">[GSP290](#step1)[Overview](#step2)[Setup](#step3)[Download the starter code](#step4)[Create Cloud Storage Bucket](#step5)[Copy Files to Your Bucket](#step6)[Create the BigQuery Dataset](#step7)[Build a Dataflow Pipeline](#step8)[Data Ingestion](#step9)[Review pipeline python code](#step10)[Run the Apache Beam Pipeline](#step11)[Data Transformation](#step12)[Run the Apache Beam Pipeline](#step13)[Data Enrichment](#step14)[Review pipeline python code](#step15)[Run the Apache Beam Pipeline](#step16)[Data lake to Mart](#step17)[Review pipeline python code](#step18)[Run the Apache Beam Pipeline](#step19)[Test your Understanding](#step20)[Congratulations!](#step21)</div>

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

In this lab you will build several Data Pipelines that will ingest data from a publicly available dataset into BigQuery.

This lab is included in these quests: [Data Engineering](/quests/25), [Engineer Data in Google Cloud](/quests/132). If you complete this lab you'll receive credit for it when you enroll in one of these quests.

**Duration:** 0m setup · 60m access · 60m completion

<span>**Levels:** advanced</span>

**Permalink:** [https://google.qwiklabs.com/catalog_lab/1341](https://google.qwiklabs.com/catalog_lab/1341)

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

<div class="controls"><input class="hidden" type="hidden" value="1341" name="lab_review[lab_id]" id="lab_review_lab_id"></div>

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

<div class="lab-access-modal"><input type="hidden" name="id" id="id" value="3460"> <input type="hidden" name="user_id" id="user_id" value="4061609"> <input type="hidden" name="launch_with_credits" id="launch_with_credits" value="0" class="js-launch-with-credits-input"> <input type="hidden" name="launch_with_subs" id="launch_with_subs" value="0" class="js-launch-with-subscription-input">

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