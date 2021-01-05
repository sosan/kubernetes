# Cloud Composer: Copying BigQuery Tables Across Different Locations

<div class="lab-preamble__details subtitle-headline-1"><span>1 hour</span> <span>7 Credits</span>

<div class="lab__rating">[](/focuses/3528/reviews?parent=catalog)<a data-target="#lab-review-modal" data-toggle="modal">Rate Lab</a></div>

</div>

</div>

<div class="js-markdown-instructions markdown-lab-instructions" id="markdown-lab-instructions">

## GSP283

![Google Cloud Self-Paced Labs](https://cdn.qwiklabs.com/GMOHykaqmlTHiqEeQXTySaMXYPHeIvaqa2qHEzw6Occ%3D)

## Overview

In this advanced lab, you will learn how to create and run an [Apache Airflow](http://airflow.Apache.org/) workflow in Cloud Composer that completes the following tasks:

*   Reads from a config file the list of tables to copy
*   Exports the list of tables from a [BigQuery](https://cloud.google.com/bigquery/docs/) dataset located in US to [Cloud Storage](https://cloud.google.com/storage/docs/)
*   Copies the exported tables from US to EU [Cloud Storage](https://cloud.google.com/storage/docs/) buckets
*   Imports the list of tables into the target BigQuery Dataset in EU

![cce6bf21555543ce.png](https://cdn.qwiklabs.com/fQx5wXnJbI%2B5%2FM3gspKNfi2%2BcXo0QdvB%2FAGChdzxVsI%3D)

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

## Create Cloud Composer environment

First, create a Cloud Composer environment by clicking on **Composer** in the **Navigation menu**:

![composer.png](https://cdn.qwiklabs.com/kGzLJFkogYCg1fxdIWpZan0LlJcycLFf2IA0kJQ1fMM%3D)

Then click **Create environment**.

Set the following parameters for your environment:

*   **Name:** composer-advanced-lab
*   **Location:** us-central1
*   **Zone:** us-central1-a

Leave all other settings as default. Click **Create**.

The environment creation process is completed when the green checkmark displays to the left of the environment name on the Environments page in the Cloud Console.

It can take up to 20 minutes for the environment to complete the setup process. Move on to the next section - Create Cloud Storage buckets and BigQuery dataset.

Click **Check my progress** to verify the objective.

<ql-activity-tracking step="1">Create Cloud Composer environment.</ql-activity-tracking>

## Create Cloud Storage buckets

Create two Cloud Storage Multi-Regional buckets. Give your two buckets a universally unique name including the location as a suffix:

*   one located in the US as _source_ (e.g. 6552634-us)
*   the other located in EU as _destination_ (e.g. 6552634-eu)

These buckets will be used to copy the exported tables across locations, i.e., US to EU.

Click **Check my progress** to verify the objective.

<ql-activity-tracking step="2">Create two Cloud Storage buckets.</ql-activity-tracking>

## BigQuery destination dataset

Create the destination BigQuery Dataset in EU from the BigQuery new web UI.

Go to **Navigation menu** > **BigQuery**.

The **Welcome to BigQuery in the Cloud Console** message box opens. This message box provides a link to the quickstart guide and lists UI updates.

Click **Done**.

Then click on your Qwiklabs project ID:

![7d223d13071ebcbf.png](https://cdn.qwiklabs.com/nvj6A2Awnd8mTnhY2PnBRW9Ts%2FQcozXvjcMRqfWP9f0%3D)

Click **Create Dataset**. Use the name **nyc_tlc_EU** and Data location **EU**.

![9d361344dc671755.png](https://cdn.qwiklabs.com/D82z664n6VpX%2FczJyj0AbosvfJXpUwaF1trSV51ERq0%3D)

![6b9e57fc93c734f7.png](https://cdn.qwiklabs.com/kurGeclloMQHxRJyhaXsrf7VnrB4gFbMjM2oRX3YkkE%3D)

Click **Create dataset**.

Click **Check my progress** to verify the objective.

<ql-activity-tracking step="3">Create a dataset.</ql-activity-tracking>

## Airflow and core concepts, a brief introduction

While your environment is building, read about the sample file you'll be using in this lab.

[Airflow](https://airflow.Apache.org/) is a platform to programmatically author, schedule and monitor workflows.

Use airflow to author workflows as directed acyclic graphs (DAGs) of tasks. The airflow scheduler executes your tasks on an array of workers while following the specified dependencies.

### Core concepts

[DAG](https://airflow.Apache.org/concepts.html#dags) - A Directed Acyclic Graph is a collection of tasks, organised to reflect their relationships and dependencies.

[Operator](https://airflow.Apache.org/concepts.html#operators) - The description of a single task, it is usually atomic. For example, the _BashOperator_ is used to execute bash command.

[Task](https://airflow.Apache.org/concepts.html#tasks) - A parameterised instance of an Operator; a node in the DAG.

[Task Instance](https://airflow.Apache.org/concepts.html#task-instances) - A specific run of a task; characterised as: a DAG, a Task, and a point in time. It has an indicative state: _running_, _success_, _failed_, _skipped_, ...

The rest of the Airflow concepts can be found [here](https://airflow.Apache.org/concepts.html#).

## Defining the workflow

Cloud Composer workflows are comprised of [DAGs (Directed Acyclic Graphs)](https://airflow.incubator.Apache.org/concepts.html#dags). The code shown in [bq_copy_across_locations.py](https://github.com/GoogleCloudPlatform/python-docs-samples/blob/master/composer/workflows/bq_copy_across_locations.py) is the workflow code, also referred to as the DAG. Open the file now to see how it is built. Next will be a detailed look at some of the key components of the file.

To orchestrate all the workflow tasks, the DAG imports the following operators:

1.  `DummyOperator`: Creates Start and End dummy tasks for better visual representation of the DAG.
2.  `BigQueryToCloudStorageOperator`: Exports BigQuery tables to Cloud Storage buckets using Avro format.
3.  `GoogleCloudStorageToGoogleCloudStorageOperator`: Copies files across Cloud Storage buckets.
4.  `GoogleCloudStorageToBigQueryOperator`: Imports tables from Avro files in Cloud Storage bucket.

In this example, the function `read_master_file()` is defined to read the config file and build the list of tables to copy.

    # --------------------------------------------------------------------------------
    # Functions
    # --------------------------------------------------------------------------------

    def read_table_list(table_list_file):
        """
        Reads the master CSV file that will help in creating Airflow tasks in
        the DAG dynamically.
        :param table_list_file: (String) The file location of the master file,
        e.g. '/home/airflow/framework/master.csv'
        :return master_record_all: (List) List of Python dictionaries containing
        the information for a single row in master CSV file.
        """
        master_record_all = []
        logger.info('Reading table_list_file from : %s' % str(table_list_file))
        try:
            with open(table_list_file, 'rb') as csv_file:
                csv_reader = csv.reader(csv_file)
                next(csv_reader)  # skip the headers
                for row in csv_reader:
                    logger.info(row)
                    master_record = {
                        'table_source': row[0],
                        'table_dest': row[1]
                    }
                    master_record_all.append(master_record)
                return master_record_all
        except IOError as e:
            logger.error('Error opening table_list_file %s: ' % str(
                table_list_file), e)

The name of the DAG is `bq_copy_us_to_eu_01`, and the DAG is not scheduled by default so needs to be triggered manually.

    default_args = {
        'owner': 'airflow',
        'start_date': datetime.today(),
        'depends_on_past': False,
        'email': [''],
        'email_on_failure': False,
        'email_on_retry': False,
        'retries': 1,
        'retry_delay': timedelta(minutes=5),
    }
    # DAG object.
    with models.DAG('bq_copy_us_to_eu_01',
                    default_args=default_args,
                    schedule_interval=None) as dag:

To define the Cloud Storage plugin, the class `Cloud StoragePlugin(AirflowPlugin)` is defined, mapping the hook and operator downloaded from the Airflow 1.10-stable branch.

    """
        GCS Plugin
        This plugin provides an interface to GCS operator from Airflow Master.
    """

    from airflow.plugins_manager import AirflowPlugin

    from gcs_plugin.hooks.gcs_hook import GoogleCloudStorageHook
    from gcs_plugin.operators.gcs_to_gcs import \
        GoogleCloudStorageToGoogleCloudStorageOperator

    class GCSPlugin(AirflowPlugin):
        name = "gcs_plugin"
        operators = [GoogleCloudStorageToGoogleCloudStorageOperator]
        hooks = [GoogleCloudStorageHook]

## Viewing environment information

Go back to **Composer** to check on the status of your environment.

Once your environment has been created, click the name of the environment to see its details.

The **Environment details** page provides information, such as the Airflow web UI URL, Google Kubernetes Engine cluster ID, name of the Cloud Storage bucket connected to the DAGs folder.

![instance-creation.png](https://cdn.qwiklabs.com/fNCV7l%2F4Wv3TjzAq%2BROexIcxzALu3cyvCvKFxbnNpBg%3D)

<aside class="special">

**Note:** Cloud Composer uses [Cloud Storage](https://cloud.google.com/storage) to store Apache Airflow DAGs, also known as _workflows_. Each environment has an associated Cloud Storage bucket. Cloud Composer schedules only the DAGs in the Cloud Storage bucket.

</aside>

### Creating a virtual environment

Execute the following command to download and update the packages list.

    sudo apt-get update

Python virtual environments are used to isolate package installation from the system.

    sudo apt-get install virtualenv

<ql-infobox>If prompted [Y/n], press `Y` and then `Enter`.</ql-infobox>

    virtualenv -p python3 venv

Activate the virtual environment.

    source venv/bin/activate

## Setting DAGs Cloud Storage bucket

In Cloud Shell, run the following to copy the name of the DAGs bucket from your Environment Details page and set a variable to refer to it in Cloud Shell:

<ql-infobox>Make sure to replace your DAGs bucket name in the following command. Navigate to **Navigation menu** > **Storage**, it will be similar to `us-central1-composer-advanc-YOURDAGSBUCKET-bucket`.</ql-infobox>

    DAGS_BUCKET=us-central1-composer-advanc-YOURDAGSBUCKET-bucket

You will be using this variable a few times during the lab.

## Setting Airflow variables

Airflow variables are an Airflow-specific concept that is distinct from [environment variables](https://cloud.google.com/composer/docs/how-to/managing/environment-variables). In this step, you'll set the following three [Airflow variables](https://airflow.Apache.org/concepts.html#variables) used by the DAG we will deploy: `table_list_file_path`, `gcs_source_bucket`, and `gcs_dest_bucket`.

<table>

<tbody>

<tr>

<td colspan="1" rowspan="1">

**KEY**

</td>

<td colspan="1" rowspan="1">

**VALUE**

</td>

<td colspan="1" rowspan="1">

**Details**

</td>

</tr>

<tr>

<td colspan="1" rowspan="1">

`table_list_file_path`

</td>

<td colspan="1" rowspan="1">

/home/airflow/gcs/dags/bq_copy_eu_to_us_sample.csv

</td>

<td colspan="1" rowspan="1">

CSV file listing source and target tables, including dataset

</td>

</tr>

<tr>

<td colspan="1" rowspan="1">

`gcs_source_bucket`

</td>

<td colspan="1" rowspan="1">

{UNIQUE ID}-us

</td>

<td colspan="1" rowspan="1">

Cloud Storage bucket to use for exporting BigQuery tabledest_bbucks from source

</td>

</tr>

<tr>

<td colspan="1" rowspan="1">

`gcs_dest_bucket`

</td>

<td colspan="1" rowspan="1">

{UNIQUE ID}-eu

</td>

<td colspan="1" rowspan="1">

Cloud Storage bucket to use for importing BigQuery tables at destination

</td>

</tr>

</tbody>

</table>

The next `gcloud composer` command executes the Airflow CLI sub-command [variables](https://airflow.apache.org/docs/apache-airflow/stable/cli-and-env-variables-ref.html#variables). The sub-command passes the arguments to the `gcloud` command line tool.

To set the three variables, you will run the `composer command` once for each row from the above table. The form of the command is this:

    gcloud composer environments run ENVIRONMENT_NAME \
    --location LOCATION variables -- \
    --set KEY VALUE

*   `ENVIRONMENT_NAME` is the name of the environment.
*   `LOCATION` is the Compute Engine region where the environment is located. The gcloud composer command requires including the `--location` flag or [setting the default location](https://cloud.google.com/composer/docs/gcloud-installation) before running the gcloud command.
*   `KEY` and `VALUE` specify the variable and its value to set. Include a space two dashes space ( `--` ) between the left-side `gcloud` command with gcloud-related arguments and the right-side Airflow sub-command-related arguments. Also include a space between the `KEY` and `VALUE` arguments. using the `gcloud composer environments run` command with the variables sub-command in

For example, the `gcs_source_bucket` variable would be set like this:

    gcloud composer environments run composer-advanced-lab \
    --location us-central1 variables -- \
    --set gcs_source_bucket My_Bucket-us

To see the value of a variable, run the Airflow CLI sub-command [variables](https://airflow.apache.org/docs/apache-airflow/stable/cli-and-env-variables-ref.html#variables) with the `get` argument or use the [Airflow UI](https://cloud.google.com/composer/docs/quickstart#variables-ui).

For example, run the following:

    gcloud composer environments run composer-advanced-lab \
        --location us-central1 variables -- \
        --get gcs_source_bucket

## Uploading the DAG and dependencies to Cloud Storage

1.  Copy the Google Cloud Python docs samples files into your Cloud shell:

    cd ~
    gsutil -m cp -r gs://spls/gsp283/python-docs-samples .

1.  Upload a copy of the third party hook and operator to the plugins folder of your Composer DAGs Cloud Storage bucket:

    gsutil cp -r python-docs-samples/third_party/apache-airflow/plugins/* gs://$DAGS_BUCKET/plugins

1.  Next, upload the DAG and config file to the DAGs Cloud Storage bucket of your environment:

    gsutil cp python-docs-samples/composer/workflows/bq_copy_across_locations.py gs://$DAGS_BUCKET/dags
    gsutil cp python-docs-samples/composer/workflows/bq_copy_eu_to_us_sample.csv gs://$DAGS_BUCKET/dags

Cloud Composer registers the DAG in your Airflow environment automatically, and DAG changes occur within 3-5 minutes. You can see task status in the Airflow web interface and confirm the DAG is not scheduled as per the settings.

## Using the Airflow UI

To access the Airflow web interface using the Cloud Console:

1.  Go back to the Composer **Environments** page.
2.  In the **Airflow webserver** column for the environment, click the new window icon.

![395f17840778e0dd.png](https://cdn.qwiklabs.com/VBJcMpbTSgi2FoGwYgZRT%2BcXoCrmm2ABvnrX3Th6H2w%3D)

1.  Click on your lab credentials.

2.  The Airflow web UI opens in a new browser window. Data will still be loading when you get here. You can continue with the lab while this is happening.

### Viewing Variables

The variables you set earlier are persisted in your environment. You can view the variables by selecting **Admin** >**Variables** from the Airflow menu bar.

![38b43061012ab930.png](https://cdn.qwiklabs.com/%2FLrVMg8Yq8azTAsxrQ08VI5gw%2F%2Fs3P8gmzfLG9nc97k%3D)

### Trigger the DAG to run manually

Click on the **DAGs** tab and wait for the links to finish loading.

To trigger the DAG manually, click the play button for `composer_sample_bq_copy_across_locations` :

![1a05036b9227748.png](https://cdn.qwiklabs.com/rdWz7HDaC2nm8byAie8mkTWxm2ky9TZzy8ubzWmrwvc%3D)

Click **Trigger** to confirm this action.

Click **Check my progress** to verify the objective.

<ql-activity-tracking step="4">Uploading the DAG and dependencies to Cloud Storage</ql-activity-tracking>

### Exploring DAG runs

When you upload your DAG file to the DAGs folder in Cloud Storage, Cloud Composer parses the file. If no errors are found, the name of the workflow appears in the DAG listing, and the workflow is queued to run immediately if the schedule conditions are met, in this case, None as per the settings.

The **DAG Runs** status turns green once the play button is pressed:

![8965437cacab8604.png](https://cdn.qwiklabs.com/j7lOD5q0Ci73O8QijS6vzIKNXcDUQf%2BbURBVzPHkKEM%3D)

Click the name of the DAG to open the DAG details page. This page includes a graphical representation of workflow tasks and dependencies.

![4a7bdf344674e2e8.png](https://cdn.qwiklabs.com/kzVVEhgReRAc9AWUA%2F5sPZ8cz8XOHAeqgbMQGFa7Y7E%3D)

Now, in the toolbar, click **Graph View**, then mouseover the graphic for each task to see its status. Note that the border around each task also indicates the status (green border = running; red = failed, etc.).

![c8c7c51f9f0cac63.png](https://cdn.qwiklabs.com/ggJCzDiC8%2Bpfrk4NqCjPR6itLSRuxZn1S6UWZDJg%2BJY%3D)

To run the workflow again from the **Graph View**:

1.  In the Airflow UI Graph View, click the **start** graphic.
2.  Click **Clear** to reset all the tasks and then click **OK** to confirm.

![b977538c5a7fa192.png](https://cdn.qwiklabs.com/83twxCLtRZG%2BwwrEBW0Fm25rCZBui3j5hj1ZSNAHGiY%3D)

Refresh your browser while the process is running to see the most recent information.

## Validate the results

Now check the status and results of the workflow by going to these Cloud Console pages:

*   The exported tables were copied from the US bucket to the EU Cloud Storage bucket. Click on **Storage** to see the intermediate Avro files in the source (US) and destination (EU) buckets.
*   The list of tables were imported into the target BigQuery Dataset. Click on **BigQuery**, then click on your project name and the **nyc_tlc_EU** dataset to validate the tables are accessible from the dataset you created.

![2e3c9292a44f5968.png](https://cdn.qwiklabs.com/i6U17N3BTYfpcNwPQl7kbqU5ia570bs8YmqqF07voLk%3D)

## Congratulations!

You've have completed this advanced lab and copied 2 tables programmatically from US to EU! This lab is based on this [blog post](https://cloud.google.com/blog/products/data-analytics/how-to-transfer-bigquery-tables-between-locations-with-cloud-composer) by David Sabater Dinter.

### Take Your Next Lab

Continue your Quest with [Predict Visitor Purchases with a Classification Model in BQML](https://google.qwiklabs.com/catalog_lab/1101), or check out these suggestions:

*   [Predict Housing Prices with Tensorflow and AI Platform](https://google.qwiklabs.com/catalog_lab/1468)

## Next steps

*   Learn more about using Airflow at the [Airflow website](https://airflow.Apache.org/) or the Airflow [Github project](https://github.com/Apache/incubator-airflow).
*   There are lots of other resources available for Airflow, including a [discussion group](https://groups.google.com/a/tensorflow.org/forum/#%21forum/discuss).
*   Sign up for the Apache dev and commits mailing lists (send emails to dev-subscribe@airflow.incubator.Apache.org and commits-subscribe@airflow.incubator.Apache.org to subscribe to each)
*   Sign up for an Apache JIRA account and re-open any issues that you care about in the [Apache Airflow JIRA project](https://issues.Apache.org/jira/browse/AIRFLOW)
*   For information about the Airflow UI, see [Accessing the web interface](https://cloud.google.com/composer/docs/how-to/accessing/airflow-web-interface#accessing_the_web_interface).

### Google Cloud Training & Certification

...helps you make the most of Google Cloud technologies. [Our classes](https://cloud.google.com/training/courses) include technical skills and best practices to help you get up to speed quickly and continue your learning journey. We offer fundamental to advanced level training, with on-demand, live, and virtual options to suit your busy schedule. [Certifications](https://cloud.google.com/certification/) help you validate and prove your skill and expertise in Google Cloud technologies.

##### Manual Last Updated December 30, 2020

##### Lab Last Tested December 30, 2020

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

<div class="js-lab-content-outline lab-content__outline">[GSP283](#step1)[Overview](#step2)[Setup](#step3)[Create Cloud Composer environment](#step4)[Create Cloud Storage buckets](#step5)[BigQuery destination dataset](#step6)[Airflow and core concepts, a brief introduction](#step7)[Defining the workflow](#step8)[Viewing environment information](#step9)[Setting DAGs Cloud Storage bucket](#step10)[Setting Airflow variables](#step11)[Uploading the DAG and dependencies to Cloud Storage](#step12)[Using the Airflow UI](#step13)[Validate the results](#step14)[Congratulations!](#step15)[Next steps](#step16)</div>

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

In this advanced lab you will create and run an Apache Airflow workflow in Cloud Composer that exports tables from a BigQuery dataset located in Cloud Storage bucktes in the US to buckets in Europe, then import th0se tables to a BigQuery dataset in Europe.

This lab is included in these quests: [Data Engineering](/quests/25), [Engineer Data in Google Cloud](/quests/132). If you complete this lab you'll receive credit for it when you enroll in one of these quests.

**Duration:** 0m setup · 60m access · 60m completion

<span>**Levels:** advanced</span>

**Permalink:** [https://google.qwiklabs.com/catalog_lab/1418](https://google.qwiklabs.com/catalog_lab/1418)

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

<div class="controls"><input class="hidden" type="hidden" value="1418" name="lab_review[lab_id]" id="lab_review_lab_id"></div>

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

<div class="lab-access-modal"><input type="hidden" name="id" id="id" value="3528"> <input type="hidden" name="user_id" id="user_id" value="4061609"> <input type="hidden" name="launch_with_credits" id="launch_with_credits" value="0" class="js-launch-with-credits-input"> <input type="hidden" name="launch_with_subs" id="launch_with_subs" value="0" class="js-launch-with-subscription-input">

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