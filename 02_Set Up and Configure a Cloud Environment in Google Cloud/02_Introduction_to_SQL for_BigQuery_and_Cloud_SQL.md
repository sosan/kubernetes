# Introduction to SQL for BigQuery and Cloud SQL

<div class="lab-preamble__details subtitle-headline-1"><span>1 hour 15 minutes</span> <span>1 Credit</span>

<div class="lab__rating">[](/focuses/2802/reviews?parent=catalog)<a data-target="#lab-review-modal" data-toggle="modal">Rate Lab</a></div>

</div>

</div>

<div class="js-markdown-instructions markdown-lab-instructions" id="markdown-lab-instructions">

## GSP281

![Google Cloud Self-Paced Labs](https://cdn.qwiklabs.com/GMOHykaqmlTHiqEeQXTySaMXYPHeIvaqa2qHEzw6Occ%3D)

## Overview

SQL (Structured Query Language) is a standard language for data operations that allows you to ask questions and get insights from structured datasets. It's commonly used in database management and allows you to perform tasks like transaction record writing into relational databases and petabyte-scale data analysis.

This lab serves as an introduction to SQL and is intended to prepare you for the many labs and quests in Qwiklabs on data science topics. This lab is divided into two parts: in the first half, you will learn fundamental SQL querying keywords, which you will run in the BigQuery console on a public dataset that contains information on London bikeshares.

In the second half, you will learn how to export subsets of the London bikeshare dataset into CSV files, which you will then upload to Cloud SQL. From there you will learn how to use Cloud SQL to create and manage databases and tables. Towards the end, you will get hands-on practice with additional SQL keywords that manipulate and edit data.

### Objectives

In this lab, you will learn how to:

*   Distinguish databases from tables and projects.
*   Use the `SELECT`, `FROM`, and `WHERE` keywords to construct simple queries.
*   Identify the different components and hierarchies within the BigQuery console.
*   Load databases and tables into BigQuery.
*   Execute simple queries on tables.
*   Learn about the `COUNT`, `GROUP BY`, `AS`, and `ORDER BY` keywords.
*   Execute and chain the above commands to pull meaningful data from datasets.
*   Export a subset of data into a CSV file and store that file into a new Cloud Storage bucket.
*   Create a new Cloud SQL instance and load your exported CSV file as a new table.
*   Run `CREATE DATABASE`, `CREATE TABLE`, `DELETE`, `INSERT INTO`, and `UNION` queries in Cloud SQL.

### Prerequisites

**Very Important:** Before starting this lab, log out of your personal or corporate gmail account.

This is a **introductory level** lab. This assumes little to no prior experience with SQL. Familiarity with Cloud Storage and Cloud Shell is recommended, but not required. This lab will teach you the basics of reading and writing queries in SQL, which you will apply by using BigQuery and Cloud SQL.

Before taking this lab, consider your proficiency in SQL. Below are more challenging labs that will let you apply your knowledge to more advanced use cases:

*   [Weather Data in BigQuery](https://google.qwiklabs.com/catalog_lab/476)
*   [Analyzing Natality Data Using Datalab and BigQuery](https://google.qwiklabs.com/catalog_lab/479)

Once you're ready, scroll down and follow the steps below to get your lab environment set up.

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

## The Basics of SQL

### Databases and Tables

As mentioned earlier, SQL allows you to get information from "structured datasets". Structured datasets have clear rules and formatting and often times are organized into tables, or data that's formatted in rows and columns.

An example of _unstructured data_ would be an image file. Unstructured data is inoperable with SQL and cannot be stored in BigQuery datasets or tables (at least natively.) To work with image data (for instance), you would use a service like [Cloud Vision](https://google.qwiklabs.com/catalog_lab/1112), perhaps through its [API](https://google.qwiklabs.com/catalog_lab/1241) directly.

The following is an example of a structured dataset—a simple table:

<table>

<tbody>

<tr>

<td colspan="1" rowspan="1">

**User**

</td>

<td colspan="1" rowspan="1">

**Price**

</td>

<td colspan="1" rowspan="1">

**Shipped**

</td>

</tr>

<tr>

<td colspan="1" rowspan="1">

Sean

</td>

<td colspan="1" rowspan="1">

$35

</td>

<td colspan="1" rowspan="1">

Yes

</td>

</tr>

<tr>

<td colspan="1" rowspan="1">

Rocky

</td>

<td colspan="1" rowspan="1">

$50

</td>

<td colspan="1" rowspan="1">

No

</td>

</tr>

</tbody>

</table>

If you've had experience with Google Sheets, then the above should look quite similar. As we see, the table has columns for User, Price, and Shipped and two rows that are composed of filled in column values.

A Database is essentially a _collection of one or more tables_. SQL is a structured database management tool, but quite often (and in this lab) you will be running queries on one or a few tables joined together—not on whole databases.

### SELECT and FROM

SQL is phonetic by nature and before running a query, it's always helpful to first figure out what question you want to ask your data (unless you're just exploring for fun.)

SQL has predefined _keywords_ which you use to translate your question into the pseudo-english SQL syntax so you can get the database engine to return the answer you want.

The most essential keywords are `SELECT` and `FROM`:

*   Use `SELECT` to specify what fields you want to pull from your dataset.
*   Use `FROM` to specify what table or tables we want to pull our data from.

An example may help understanding. Assume that we have the following table `example_table`, which has columns USER, PRICE, and SHIPPED:

![14422cb7144f3ae.png](https://cdn.qwiklabs.com/XOxiZPGbXJ2GBjG164aXcFmKI35BtpMO0%2FiWcbIYLfQ%3D)

And let's say that we want to just pull the data that's found in the USER column. We can do this by running the following query that uses `SELECT` and `FROM`:

    SELECT USER FROM example_table

If we executed the above command, we would select all the names from the `USER` column that are found in `example_table`.

You can also select multiple columns with the SQL `SELECT` keyword. Say that you want to pull the data that's found in the USER and SHIPPED columns. To do this, modify the previous query by adding another column value to our `SELECT` query (making sure it's separated by a comma!):

    SELECT USER, SHIPPED FROM example_table

Running the above retrieves the `USER` and the `SHIPPED` data from memory:

![a4027fb83edf734.png](https://cdn.qwiklabs.com/u4knQcxxNRrBjThYXtSccEq9%2BroxOz0FL0Jg6zCzIAE%3D)

And just like that you've covered two fundamental SQL keywords! Now to make things a bit more interesting.

### WHERE

The `WHERE` keyword is another SQL command that filters tables for specific column values. Say that you want to pull the names from `example_table` whose packages were shipped. You can supplement the query with a `WHERE`, like the following:

    SELECT USER FROM example_table WHERE SHIPPED='YES'

Running the above returns all USERs whose packages have been SHIPPED to from memory:

![5566150a165277e8.png](https://cdn.qwiklabs.com/wst%2BjF1gd%2FvqJaN8OPxTgoWdUbT%2BcWKMVipwc2wlYWw%3D)

Now that you have a baseline understanding of SQL's core keywords, apply what you've learned by running these types of queries in the BigQuery console.

### Test your understanding

The following are some multiple choice questions to reinforce your understanding of the concepts covered so far. Answer them to the best of your abilities.

## Exploring the BigQuery Console

### The BigQuery paradigm

[BigQuery](https://cloud.google.com/bigquery/) is a fully-managed petabyte-scale data warehouse that runs on the Google Cloud. Data analysts and data scientists can quickly query and filter large datasets, aggregate results, and perform complex operations without having to worry about setting up and managing servers. It comes in the form of a command line tool (preinstalled in cloudshell) or a web console—both ready for managing and querying data housed in Google Cloud projects.

In this lab, you use the web console to run SQL queries.

### Open BigQuery Console

In the Google Cloud Console, select **Navigation menu** > **BigQuery**:

![BigQuery_menu.png](https://cdn.qwiklabs.com/QwUX6algdXPgIThqBlgB2rnqGkoQUUANkl3xicD06uc%3D)

The **Welcome to BigQuery in the Cloud Console** message box opens. This message box provides a link to the quickstart guide and the release notes.

Click **Done**.

The BigQuery console opens.

![bq-console.png](https://cdn.qwiklabs.com/vd3QNHGB4BAyHjAvOHw9qD0iqCPNaHAzY657z%2FGWLtY%3D)

Take a moment to note some important features of the UI. The right-hand side of the console houses the "Query editor". This is where you write and run SQL commands like the examples we covered earlier. Below that is "Query history", which is a list of queries you ran previously.

The left pane of the console is the "Navigation panel". Apart from the self-explanatory query history, saved queries, and job history, there is the _Resources_ tab.

The highest level of resources contain Google Cloud projects, which are just like the temporary Google Cloud projects you sign in to and use with each Qwiklab. As you can see in your console and in the last screenshot, we only have our Qwiklabs project housed in the Resources tab. If you try clicking on the arrow next the project name, nothing will show up.

This is because your project doesn't contain any datasets or tables, you have nothing that can be queried. Earlier you learned datasets contain tables. When you add data to your project, note that in BigQuery, _projects contain datasets, and datasets contain tables_. Now that you better understand the project → dataset → table paradigm and the intricacies of the console, you can load up some queryable data.

### Uploading queryable data

In this section you pull in some public data into your project so you can practice running SQL commands in BigQuery.

Click on the **+ ADD DATA** link then select **Explore public datasets**:

![BQ_adddata.png](https://cdn.qwiklabs.com/F9lhtxyIiwttzF%2FyortRrX8dOAJ60sTN%2BKCNyuXHcfU%3D)

In the search bar, enter "london", then select the **London Bicycle Hires** tile, then **View Dataset**.

A new tab will open, and you will now have a new project called `bigquery-public-data` added to the Resources panel:

![BQ_pubdata.png](https://cdn.qwiklabs.com/f1yJh%2FCYs%2FKZ1D9Ta%2FU1mWAQh3VsOzu0Z%2Becb5CYimY%3D)

It's important to note that you are still working out of your lab project in this new tab. All you did was pull a publicly accessible project that contains datasets and tables into BigQuery for analysis — you didn't _switch over_ to that project. All of your jobs and services are still tied to your Qwiklabs account. You can see this for yourself by inspecting the project field near the top of the console:

![BQ_proj_check.png](https://cdn.qwiklabs.com/eUyizy8IKV89BwSWBtOLM5MhJLemWB%2BCzgSHeaCbn1w%3D)

Click on **bigquery-public-data** > **london_bicycles** > **cycle_hire**. You now have data that follows the BigQuery paradigm:

*   Google Cloud Project → `bigquery-public-data`
*   Dataset → `london_bicycles`
*   Table → `cycle_hire`

Now that you are in the `cycle_hire` table, in the center of the console click the **Preview** tab. Your page should resemble the following:

![cycle_hire.png](https://cdn.qwiklabs.com/foj4wmiY8F%2FcK1BpqVCM%2BP4CgU7Sf5WAk7fNaaeiaZM%3D)

Inspect the columns and values populated in the rows. You are now ready to run some SQL queries on the `cycle_hire` table.

### Running SELECT, FROM, and WHERE in BigQuery

You now have a basic understanding of SQL querying keywords and the BigQuery data paradigm and some data to work with. Run some SQL commands using this service.

If you look at the bottom right corner of the console, you will notice that there are **24,369,201** rows of data, or individual bikeshare trips taken in London between 2015 and 2017 (not a small amount by any means!)

Now take note of the seventh column key: `end_station_name`, which specifies the end destination of bikeshare rides. Before we get too deep, let's first run a simple query to isolate the `end_station_name` column. Copy and paste the following command in to the Query editor:

    SELECT end_station_name FROM `bigquery-public-data.london_bicycles.cycle_hire`;

Then click **Run**.

After ~20 seconds, you should be returned with 24369201 rows that contain the single column you queried for: `end_station_name`.

Why don't you find out how many bike trips were 20 minutes or longer?

Click **COMPOSE NEW QUERY** to clear the Query editor, then run the following query that utilizes the `WHERE` keyword:

    SELECT * FROM `bigquery-public-data.london_bicycles.cycle_hire` WHERE duration>=1200;

This query may take a minute or so to run.

`SELECT *` returns all column values from the table. Duration is measured in seconds, which is why you used the value 1200 (60 * 20).

If you look in the bottom right corner you see that **7,334,890** rows were returned. As a fraction of the total (7334890/24369201), this means that ~30% of London bikeshare rides lasted 20 minutes or longer (they're in it for the long haul!)

### Test your understanding

The following are some multiple choice questions to reinforce your understanding of the concepts we've covered so far. Answer them to the best of your abilities.

## More SQL Keywords: GROUP BY, COUNT, AS, and ORDER BY

### GROUP BY

The `GROUP BY` keyword will aggregate result-set rows that share common criteria (e.g. a column value) and will return all of the unique entries found for such criteria.

This is a useful keyword for figuring out categorical information on tables. To get a better picture of what this keyword does, click **COMPOSE NEW QUERY**, then copy and paste the following command in the Query editor:

    SELECT start_station_name FROM `bigquery-public-data.london_bicycles.cycle_hire` GROUP BY start_station_name;

Click **Run**.

You should receive a similar output (row values may not match the following):

![30b04fc6f21c544c.png](https://cdn.qwiklabs.com/WcheomnmNVxpZflsIhCmoSmxbakCLWCugBjaQ6ARrLI%3D)

Without the `GROUP BY`, the query would have returned the full **24,369,201** rows. `GROUP BY` will output the unique (non-duplicate) column values found in the table. You can see this for yourself by looking in the bottom right corner. You will see **880** rows, meaning there are 880 distinct London bikeshare starting points.

### COUNT

The `COUNT()` function will return the number of rows that share the same criteria (e.g. column value). This can be very useful in tandem with a `GROUP BY`.

Add the `COUNT` function to our previous query to figure out how many rides begin in each starting point. Click **COMPOSE NEW QUERY**, copy and paste the following command in to the Query editor and then click **Run query**:

    SELECT start_station_name, COUNT(*) FROM `bigquery-public-data.london_bicycles.cycle_hire` GROUP BY start_station_name;

You should receive a similar output (row values may not match the following):

![b62aaf26b7a35e35.png](https://cdn.qwiklabs.com/rvBPMCHcoM0etV%2BNXOvBwHnfh6LWgAAzBI5o0%2FKM97Y%3D)

This shows how many bikeshare rides begin at each starting location.

### AS

SQL also has an `AS` keyword, which creates an _alias_ of a table or column. An alias is a new name that's given to the returned column or table—whatever `AS` specifies.

Add an `AS` keyword to the last query we ran to see this in action. Click **COMPOSE NEW QUERY**, then copy and paste the following command in to the Query editor:

    SELECT start_station_name, COUNT(*) AS num_starts FROM `bigquery-public-data.london_bicycles.cycle_hire` GROUP BY start_station_name;

Click **Run**.

You should receive a similar output (be aware that the row values might not be identical):

![a45a8b122dbfea2b.png](https://cdn.qwiklabs.com/tne5HErHLjL7JXpNCIHadDKX1n70jKGnDJcdgwKdHXM%3D)

As you see, the `COUNT(*)` column in the returned table is now set to the alias name `num_starts`. This is a handy keyword to use especially if you are dealing with large sets of data — forgetting that an ambiguous table or column name happens more often than you think!

### ORDER BY

The `ORDER BY` keyword sorts the returned data from a query in ascending or descending order based on a specified criteria or column value. We will add this keyword to our previous query to do the following:

*   Return a table that contains the number of bikeshare rides that begin in each starting station, organized alphabetically by the starting station.
*   Return a table that contains the number of bikeshare rides that begin in each starting station, organized numerically from lowest to highest.
*   Return a table that contains the number of bikeshare rides that begin in each starting station, organized numerically from highest to lowest.

Each of the commands below is a separate query. For each command, clear the Query editor, copy and paste the command in to the Query editor, and then click **Run**. Examine the results.

    SELECT start_station_name, COUNT(*) AS num FROM `bigquery-public-data.london_bicycles.cycle_hire` GROUP BY start_station_name ORDER BY start_station_name;

    SELECT start_station_name, COUNT(*) AS num FROM `bigquery-public-data.london_bicycles.cycle_hire` GROUP BY start_station_name ORDER BY num;

    SELECT start_station_name, COUNT(*) AS num FROM `bigquery-public-data.london_bicycles.cycle_hire` GROUP BY start_station_name ORDER BY num DESC;

The last query should have returned the following:

![368742bde0aa54d6.png](https://cdn.qwiklabs.com/L3uDkzg%2B3uiiBgBwaGSnhVmqVpXP9waOgMIg9zLDG2c%3D)

We see that "Belgrove Street, King's Cross" has the highest number of starts. However, as a fraction of the total (234458/24369201), we see that < 1% of rides start from this station.

### Test your understanding

The following are some multiple choice questions to reinforce your understanding of the concepts we've covered so far. Answer them to the best of your abilities.

## Working with Cloud SQL

### Exporting queries as CSV files

[Cloud SQL](https://cloud.google.com/sql/) is a fully-managed database service that makes it easy to set up, maintain, manage, and administer your relational PostgreSQL and MySQL databases in the cloud. There are two formats of data accepted by Cloud SQL: dump files (.sql) or CSV files (.csv). You will learn how to export subsets of the `cycle_hire` table into CSV files and upload them to Cloud Storage as an intermediate location.

Back in the BigQuery Console, this should have been the last command that you ran:

    SELECT start_station_name, COUNT(*) AS num FROM `bigquery-public-data.london_bicycles.cycle_hire` GROUP BY start_station_name ORDER BY num DESC;

In the Query Results section click **SAVE RESULTS** > **CSV(local file)** > **SAVE**. This initiates a download, which saves this query as a CSV file. Note the location and the name of this downloaded file—you will need it soon.

Click **COMPOSE NEW QUERY**, then copy and run the following in the query editor:

    SELECT end_station_name, COUNT(*) AS num FROM `bigquery-public-data.london_bicycles.cycle_hire` GROUP BY end_station_name ORDER BY num DESC;

This will return a table that contains the number of bikeshare rides that finish in each ending station and is organized numerically from highest to lowest number of rides. You should receive the following output:

![814554dc82c60a74.png](https://cdn.qwiklabs.com/mAesoVBfXplwi1b5bh1nIRILSAi7%2FBbebkAhpzb2CZ4%3D)

In the Query Results section click **SAVE RESULTS** > **CSV(local file)** > **SAVE**. This initiates a download, which saves this query as a CSV file. Note the location and the name of this downloaded file—you will need it in the following section.

### Upload CSV files to Cloud Storage

Go to the Cloud Console where you'll create a storage bucket where you can upload the files you just created.

Select **Navigation menu** > **Storage** > **Browser**, and then click **Create bucket**.

Enter a unique name for your bucket, keep all other settings, and hit **Create**:

![bucket_details.png](https://cdn.qwiklabs.com/MJaLpJcY4bF7yM0I4XC%2BlzCe3F32kXqqayPLGZ5vK4Q%3D)

### Test Completed Task

Click **Check my progress** below to check your lab progress. If you successfully created your bucket, you'll see an assessment score.

<ql-activity-tracking step="1">Create a cloud storage bucket.</ql-activity-tracking>

You should now be in the Cloud Console looking at your newly created Cloud Storage Bucket.

Click **Upload files** and select the CSV that contains `start_station_name` data. Then click **Open**. Repeat this for the `end_station_name` data.

Rename your `start_station_name` file by clicking on the three dots next to on the far side of the file and click **rename**. Rename the file `start_station_data.csv`.

Rename your `end_station_name` file by clicking on the three dots next to on the far side of the file and click **rename**. Rename the file `end_station_data.csv`.

Your bucket should now resemble the following:

![4ca41c9e381d94f.png](https://cdn.qwiklabs.com/O0gGDUAw3%2BKFgvwpeQvYtmRFgfAlChH09mZMXpztL%2FM%3D)

### Test Completed Task

Click **Check my progress** to verify your performed task. If you have successfully upload CSV objects to your bucket, you will see an assessment score.

<ql-activity-tracking step="2">Upload CSV files to Cloud Storage.</ql-activity-tracking>

### Create a Cloud SQL instance

In the console, select **Navigation menu** > **SQL**.

Click **Create Instance**.

From here, you will be prompted to choose a database engine. Select **MySQL**.

Now enter in a name for your instance (like "qwiklabs-demo") and enter in a secure password in the **Root password** field (remember it!), then click **Create**:

![cloud-sql-instance.gif](https://cdn.qwiklabs.com/WUXtEyQAsB3cC2zATXIR1P8kq2BvnrT88egc0y%2F0sFo%3D)

It might take a few minutes for the instance to be created. Once it is, you will see a green checkmark next to the instance name.

Click on the Cloud SQL instance. You should now be on a page that resembles the following:

![overview.png](https://cdn.qwiklabs.com/prYd56hM7k4hZgCBy7WAIxZsvTpgq8E3zbMWEURRn1s%3D)

### Test Completed Task

To check out your lab progress, click **Check my progress** below.If you have successfully set up your Cloud SQL instance, you will see an assessment score.

<ql-activity-tracking step="3">Create a CloudSQL Instance.</ql-activity-tracking>

## New Queries in Cloud SQL

### CREATE keyword (databases and tables)

Now that you have a Cloud SQL instance up and running, create a database inside of it using the Cloud Shell Command Line.

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

Run the following command in Cloud Shell to connect to your SQL instance, replacing `qwiklabs-demo` if you used a different name for your instance:

    gcloud sql connect  qwiklabs-demo --user=root

It may take a minute to connect to your instance.

When prompted, enter the root password you set for the instance.

You should now be on a similar output:

    Welcome to the MariaDB monitor.  Commands end with ; or \g.
    Your MySQL connection id is 494
    Server version: 5.7.14-google-log (Google)

    Copyright (c) 2000, 2017, Oracle, MariaDB Corporation Ab and others.

    Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

    MySQL [(none)]>

A Cloud SQL instance comes with pre-configured databases, but you will create your own to store the London bikeshare data.

Run the following command at the MySQL server prompt to create a database called `bike`:

    CREATE DATABASE bike;

You should receive the following output:

    Query OK, 1 row affected (0.05 sec)

    MySQL [(none)]>

### Test Completed Task

Check your progress by clicking **Check my progress** to verify your performed task. If you have successfully created database in Cloud SQL instance, you will see an assessment score.

<ql-activity-tracking step="4">Create a database.</ql-activity-tracking>

Make a table inside of the bike database by running the following command:

    USE bike;
    CREATE TABLE london1 (start_station_name VARCHAR(255), num INT);

This statement uses the `CREATE` keyword, but this time it uses the `TABLE` clause to specify that it wants to build a table instead of a database. The `USE` keyword specifies a database that you want to connect to. You now have a table named "london1" that contains two columns, "start_station_name" and "num". `VARCHAR(255)` specifies variable length string column that can hold up to 255 characters and `INT` is a column of type integer.

Create another table named "london2" by running the following command:

    USE bike;
    CREATE TABLE london2 (end_station_name VARCHAR(255), num INT);

Now confirm that your empty tables were created. Run the following commands at the MySQL server prompt:

    SELECT * FROM london1;
    SELECT * FROM london2;

You should receive the following output for both commands:

    Empty set (0.04 sec)

It says "empty set" because you haven't loaded in any data yet.

### Upload CSV files to tables

Return to the Cloud SQL console. You will now upload the `start_station_name` and `end_station_name` CSV files into your newly created london1 and london2 tables.

1.  In your Cloud SQL instance page, click **IMPORT**.
2.  In the Cloud Storage file field, click **Browse**, and then click the arrow opposite your bucket name, and then click `start_station_data.csv`. Click **Select**.
3.  Select the `bike` database and type in "london1" as your table.
4.  Click **Import**:

![3aab448fcc8a8a6f.png](https://cdn.qwiklabs.com/AmE%2BAIYHcE48Xt0k7wmRrB%2FJYPoe8dS%2BAmNFP2eZm5g%3D)

Do the same for the other CSV file.

1.  In your Cloud SQL instance page, click **IMPORT**.
2.  In the Cloud Storage file field, click **Browse**, and then click the arrow opposite your bucket name, and then click `end_station_data.csv` Click **Select**.
3.  Select the bike database and type in "london2" as your table.
4.  Click **Import**:

You should now have both CSV files uploaded to tables in the `bike` database.

Return to your Cloud Shell session and run the following command at the MySQL server prompt to inspect the contents of london1:

    SELECT * FROM london1;

You should receive 881 lines of output, one more each unique station name. Your output be formatted like this:

![48c3c74603692827.png](https://cdn.qwiklabs.com/gHiOlNC7sj3nJvU6dgHAVtU4bzDaVWvK6MBEaKRwLH8%3D)

Run the following command to make sure that london2 has been populated:

    SELECT * FROM london2;

You should receive 883 lines of output, one more each unique station name. Your output be formatted like this:

![85a788ec7971f8a0.png](https://cdn.qwiklabs.com/mUE0WbdWa8RP6L4nH%2BJIOBd3M6TBJB3Yi7%2FQje8zcZM%3D)

### DELETE keyword

Here are a couple more SQL keywords that help us with data management. The first is the `DELETE` keyword.

Run the following commands in your MySQL session to delete the first row of the london1 and london2:

    DELETE FROM london1 WHERE num=0;
    DELETE FROM london2 WHERE num=0;

You should receive the following output after running both commands:

    Query OK, 1 row affected (0.04 sec)

The rows deleted were the column headers from the CSV files. The `DELETE` keyword will not remove the first row of the file per se, but all _rows_ of the table where the column name (in this case "num") contains a specified value (in this case "0"). If you run the `SELECT * FROM london1;` and `SELECT * FROM london2;` queries and scroll to the top of the table, you will see that those rows no longer exist.

### INSERT INTO keyword

You can also insert values into tables with the `INSERT INTO` keyword. Run the following command to insert a new row into london1, which sets `start_station_name` to "test destination" and `num` to "1":

    INSERT INTO london1 (start_station_name, num) VALUES ("test destination", 1);

The `INSERT INTO` keyword requires a table (london1) and will create a new row with columns specified by the terms in the first parenthesis (in this case "start_station_name" and "num"). Whatever comes after the "VALUES" clause will be inserted as values in the new row.

You should receive the following output:

    Query OK, 1 row affected (0.05 sec)

If you run the query `SELECT * FROM london1;` you will see an additional row added at the bottom of the "london1" table:

![b067eb36e63b9e68.png](https://cdn.qwiklabs.com/eYhqa3ycQ83rA1PDALG5rffOmKQ5OkLYuduwGhjejK4%3D)

### UNION keyword

The last SQL keyword that you'll learn about is `UNION`. This keyword combines the output of two or more `SELECT` queries into a result-set. You use `UNION` to combine subsets of the "london1" and "london2" tables.

The following chained query pulls specific data from both tables and combine them with the `UNION` operator.

Run the following command at the MySQL server prompt:

    SELECT start_station_name AS top_stations, num FROM london1 WHERE num>100000
    UNION
    SELECT end_station_name, num FROM london2 WHERE num>100000
    ORDER BY top_stations DESC;

The first `SELECT` query selects the two columns from the "london1" table and creates an alias for "start_station_name", which gets set to "top_stations". It uses the `WHERE` keyword to only pull rideshare station names where over 100,000 bikes start their journey.

The second `SELECT` query selects the two columns from the "london2" table and uses the `WHERE` keyword to only pull rideshare station names where over 100,000 bikes end their journey.

The `UNION` keyword in between combines the output of these queries by assimilating the "london2" data with "london1". Since "london1" is being unioned with "london2", the column values that take precedent are "top_stations" and "num".

`ORDER BY` will order the final, unioned table by the "top_stations" column value alphabetically and in descending order.

You should receive the following output:

![aec2cf3f207027a0.png](https://cdn.qwiklabs.com/z9UUN0K%2FyW1UWaCn0M%2BU1b0srEzOO7EO%2BZ1xHk5aNq0%3D)

As you see, 13/14 stations share the top spots for rideshare starting and ending points. With some basic SQL keywords you were able to query a sizable dataset, which returned data points and answers to specific questions.

## Congratulations!

In this lab you learned the fundamentals of SQL and how you can apply keywords and run queries in BigQuery and CloudSQL. You were taught the core concepts behind projects, databases, and tables. You practiced with keywords that manipulated and edited data. You learned how to load datasets into BigQuery and you practiced running queries on tables. You learned how to create instances in Cloud SQL and practiced transferring subsets of data into tables contained in databases. You chained and ran queries in Cloud SQL to arrive at some interesting conclusions about London bikesharing starting and ending stations.

![quest-badge-two.png](https://cdn.qwiklabs.com/YD5HilNuEG%2F0LAxaCSVHvJ065yZtYjD1cPzkCfJbeGg%3D) ![Data_Science_125.png](https://cdn.qwiklabs.com/FNp7RZH%2B7DDSpeAsPcs8J402amNSyvJplHN5Iqrqog4%3D) ![cloudsql-quest-badge.png](https://cdn.qwiklabs.com/8FD2GFrCgTpBETONBnDgeL4v5uMrLJS%2BL%2BJhMQIvLZg%3D) ![BigQueryBasicsforDataAnalysts-125x135.png](https://cdn.qwiklabs.com/4ONtHbbEjhk1Kg0YoTflkUukkw2pn6r3rUVU6ES1TF4%3D) ![MarchMadness02_125.png](https://cdn.qwiklabs.com/jAnA2n029sN72whRZDiRuCPhN%2BJ1HBGQ8ERZBekLCMw%3D) ![CloudEngineeringExaPrep_125.pmg](https://cdn.qwiklabs.com/jVIZEu1uWCkufNtCmqyElRifK5o%2FNRrtt5Vo9iZxHz8%3D) ![Data Catalog Quest Badge.pmg](https://cdn.qwiklabs.com/k6pvbBWPvVh7GY6bkPUN1Y9ay1oDpsQvCzti9wNp5IE%3D)

### Finish Your Quest

This self-paced lab is part of the Qwiklabs Quests [Data Science on Google Cloud](https://google.qwiklabs.com/quests/43), [Scientific Data Processing](https://google.qwiklabs.com/quests/28), [Cloud SQL](https://google.qwiklabs.com/quests/52), [BigQuery Basics for Data Analysts](https://google.qwiklabs.com/quests/69), [NCAA® March Madness®: Bracketology with Google Cloud](https://google.qwiklabs.com/quests/58/), [Cloud Engineering](https://google.qwiklabs.com/quests/66), and [Data Catalog Fundamentals](https://google.qwiklabs.com/quests/134). A Quest is a series of related labs that form a learning path. Completing a Quest earns you one of the badges above, to recognize your achievement. You can make your badge (or badges) public and link to them in your online resume or social media account. Enroll in a quest and get immediate completion credit if you've taken this lab. [See other available Qwiklabs Quests](http://google.qwiklabs.com/catalog).

### Next Steps / Learn More

Continue your learning with and get more practice with Cloud SQL and BigQuery:

*   [Weather Data in BigQuery](https://google.qwiklabs.com/catalog_lab/476)
*   [Exploring NCAA Data with BigQuery](https://google.qwiklabs.com/catalog_lab/815)
*   [Loading Data into Google Cloud SQL](https://google.qwiklabs.com/catalog_lab/996)
*   [Cloud SQL with Terraform](https://google.qwiklabs.com/catalog_lab/1018)

### Google Cloud Training & Certification

...helps you make the most of Google Cloud technologies. [Our classes](https://cloud.google.com/training/courses) include technical skills and best practices to help you get up to speed quickly and continue your learning journey. We offer fundamental to advanced level training, with on-demand, live, and virtual options to suit your busy schedule. [Certifications](https://cloud.google.com/certification/) help you validate and prove your skill and expertise in Google Cloud technologies.

##### Manual Last Updated September 24, 2020

##### Lab Last Tested September 24, 2020

Copyright 2020 Google LLC All rights reserved. Google and the Google logo are trademarks of Google LLC. All other company and product names may be trademarks of the respective companies with which they are associated.
