# Creating a Data Transformation Pipeline with Cloud Dataprep

<div class="lab-preamble__details subtitle-headline-1"><span>1 hour 15 minutes</span> <span>7 Credits</span>

<div class="lab__rating">[](/focuses/4415/reviews?parent=catalog)<a data-target="#lab-review-modal" data-toggle="modal">Rate Lab</a></div>

</div>

</div>

<div class="js-markdown-instructions markdown-lab-instructions" id="markdown-lab-instructions">

## GSP430

![Google Cloud Self-Paced Labs](https://cdn.qwiklabs.com/GMOHykaqmlTHiqEeQXTySaMXYPHeIvaqa2qHEzw6Occ%3D)

## Overview

[Cloud Dataprep](https://cloud.google.com/dataprep/) by Trifacta is an intelligent data service for visually exploring, cleaning, and preparing structured and unstructured data for analysis. In this lab you explore the Cloud Dataprep UI to build a data transformation pipeline that runs at a scheduled interval and outputs results into BigQuery.

The dataset you'll use is an [ecommerce dataset](https://www.en.advertisercommunity.com/t5/Articles/Introducing-the-Google-Analytics-Sample-Dataset-for-BigQuery/ba-p/1676331#) that has millions of Google Analytics session records for the [Google Merchandise Store](https://shop.googlemerchandisestore.com/) loaded into BigQuery. You have a copy of that dataset for this lab and will explore the available fields and row for insights.

#### **Objectives**

In this lab, you will learn how to perform these tasks:

*   Connect BigQuery datasets to Cloud Dataprep.
*   Explore dataset quality with Cloud Dataprep.
*   Create a data transformation pipeline with Cloud Dataprep.
*   Schedule transformation jobs outputs to BigQuery.

#### **What you'll need**

*   The [Google Chrome browser](https://www.google.com/chrome/browser/desktop/). Other browsers are currently not supported by Cloud Dataprep.

## Task 1\. Setting up your development environment

### Qwiklabs setup

#### Before you click the Start Lab button

Read these instructions. Labs are timed and you cannot pause them. The timer, which starts when you click **Start Lab**, shows how long Google Cloud resources will be made available to you.

This Qwiklabs hands-on lab lets you do the lab activities yourself in a real cloud environment, not in a simulation or demo environment. It does so by giving you new, temporary credentials that you use to sign in and access Google Cloud for the duration of the lab.

#### What you need

To complete this lab, you need:

*   Access to a standard internet browser (Chrome browser recommended).
*   Time to complete the lab.

**Note:** If you already have your own personal Google Cloud account or project, do not use it for this lab.

**Note:** If you are using a Pixelbook, open an Incognito window to run this lab.

### Google Cloud Platform Console

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

## Task 2\. Creating a BigQuery Dataset

Although this lab is largely focused on Cloud Dataprep, you need BigQuery as an endpoint for dataset ingestion to the pipeline and as a destination for the output when the pipeline is completed.

![a1394a21c40722a9.png](https://cdn.qwiklabs.com/O2n0exH9RUENSsK99EJIUKFgk%2BX8HlTC95mpNxwqZcM%3D)

1.  In the Cloud Console, select **Navigation menu** > **BigQuery**:

2.  The **Welcome to BigQuery in the Cloud Console** message box opens. This message box provides a link to the quickstart guide and lists UI updates.

3.  Click **Done**.

4.  In the left pane, select your project name:

![project.png](https://cdn.qwiklabs.com/esw2SaacyT37O73Yl9AXNNnlfgwdd2YzbCs5W1m8yZU%3D)

1.  Then from the right-hand side of the Console, click **CREATE DATASET**:

*   For **Dataset ID**, type `ecommerce`.

*   Leave the other values at their defaults.

1.  Click **Create Dataset**. You will now see your dataset under your project in the left-hand menu:

![dataset.png](https://cdn.qwiklabs.com/%2FWb9tCY5obRnzxLWeG2CKCoALTFsWJEjbHk8iXa6Hjc%3D)

1.  Navigate to the Query editor and copy and paste the following SQL query into it:

    #standardSQL
     CREATE OR REPLACE TABLE ecommerce.all_sessions_raw_dataprep
     OPTIONS(
       description="Raw data from analyst team to ingest into Cloud Dataprep"
     ) AS
     SELECT * FROM `data-to-insights.ecommerce.all_sessions_raw`
     WHERE date = '20170801'; # limiting to one day of data 56k rows for this lab

1.  Click **Run**. This query copies over a subset of the public raw ecommerce dataset (one day's worth of session data, or about 56 thousand records) into a new table named `all_sessions_raw_dataprep`, which has been added to your ecommerce dataset for you to explore and clean in Cloud Dataprep.

2.  Confirm that the new table exists in your `ecommerce` dataset:

![table.png](https://cdn.qwiklabs.com/l4rCFkdBfxvQTSQTABjeUBMZVqzknJPhaFHL9NyTsK4%3D)

## Task 3\. Opening Cloud Dataprep

In this task, you will open Cloud Dataprep and go through some initialization steps.

1.  Using a Chrome browser (Reminder: Cloud Dataprep works **ONLY** in Chrome), open the Navigation menu and under **Big Data**, select **Dataprep**.

2.  Accept the terms of service.

3.  In the **Share account information with Trifacta** dialog, select the checkbox, and then click **Agree and Continue**.

![aafe44979674e62.png](https://cdn.qwiklabs.com/ANZC4%2Fu1p4kkwTCgTYjvK%2BtmjEd%2Bq%2BhbujlWQm%2FKXHM%3D)

1.  To allow Trifacta to access your project data, click **Allow**.

<ql-infobox>**Note:** This authorization process might take a few minutes.</ql-infobox>

1.  In the **Sign in with Google** window appears, select your Qwiklabs account and then click **Allow**. Accept the Trifacta Terms of Service if prompted.

2.  If prompted to use the default location for the storage bucket, click **Continue**.

Soon after you should be on the Cloud Dataprep homepage:

![welcome.png](https://cdn.qwiklabs.com/uveoLQouqfa7CLryUNBSV1lMcMhm6o%2Fk9Q529usmDGU%3D)

## Task 4\. Connecting BigQuery data to Cloud Dataprep

In this task, you will connect Cloud Dataprep to your BigQuery data source. On the Cloud Dataprep page:

1.  Click **Create Flow** in the top-right corner.

2.  In the **Create Flow** dialog, specify these details:

*   For **Flow Name**, type `Ecommerce Analytics Pipeline`
*   For **Flow Description**, type `Revenue reporting table`

![d8b3251cd8c2250b.png](https://cdn.qwiklabs.com/M6bE5rIGjhcgBRo%2FQGGSk7q9prlaKdUn%2F9KyMyiJEcI%3D)

1.  Click **Create**.

2.  If prompted with a `What's a flow?` popup, select **Don't show me any helpers**.

3.  Click the **Add Icon** in the Dataset box.

![add_icon.png](https://cdn.qwiklabs.com/k%2BTy2cx5xnl0DwdmQngGjEGU5ZU1mnb7vsm7PMkQJwE%3D)

1.  In the **Add Datasets to Flow** dialog box, select **Import Datasets**.

![import_datasets.png](https://cdn.qwiklabs.com/wFwRWqZUroCDoofOWEPo3wwP5986NYWUEcCibwbaYDI%3D)

1.  In the left pane, click **BigQuery**.

2.  When your **ecommerce** dataset is loaded, click on it.

![2daac2ca3e01ee9d.png](https://cdn.qwiklabs.com/A1PnS5h0nvcB11tTyzjMNvKuMXNxIUT42EIEYTJi1b0%3D)

1.  Click on the **Create dataset** icon (+ sign) on the left of the `all_sessions_raw_dataprep` table.

2.  Click **Import & Add to Flow** in the bottom right corner.

The data source automatically updates. You are ready to go to the next task.

## Task 5\. Exploring ecommerce data fields with a UI

In this task, you will load and explore a sample of the dataset within Cloud Dataprep.

1.  Click on the **Recipe icon** and then select **Edit Recipe**.

![recipe1.png](https://cdn.qwiklabs.com/G3PDFXsoKjxJQTIpLgT%2BLYEO742qE8OqE%2BR3q2PLPyM%3D)

Cloud Dataprep loads a sample of your dataset into the Transformer view. This process might take a few seconds. You are now ready to start exploring the data!

Answer the following questions:

*   How many columns are there in the dataset?

![dataset-columns.png](https://cdn.qwiklabs.com/7vmXj5JVvo2Mt7izyYOfhj9FbKcLRVLt3nHiLHzBGtY%3D)

**Answer**: 32 columns.

*   How many rows does the sample contain?

![dataset-rows.png](https://cdn.qwiklabs.com/6WjYgczvWvFywfhDdXtxvS4UeXIsKJqItdBWIAL0vdM%3D)

**Answer**: About 12 thousand rows.

*   What is the most common value in the `channelGrouping` column?

<ql-infobox>**Hint**: Find out by hovering your mouse cursor over the histogram under the `channelGrouping` column title.</ql-infobox>

![a5c9e2bed2fe81a9.png](https://cdn.qwiklabs.com/ZqwIsyvjq2WfBNh1XlLmlht60PfwBnKK%2B8maGuBtOPI%3D)

**Answer**: Referral. A [referring site](https://support.google.com/analytics/answer/1011811?hl=en) is typically any other website that has a link to your content. An example here is a different website reviewed a product on our ecommerce website and linked to it. This is considered a different acquisition channel than if the visitor came from a search engine.

<ql-infobox>**Tip**: When looking for a specific column, click the **Find column** icon (![33a3808286737935.png](https://cdn.qwiklabs.com/Ui5aE9D%2FnzHJzNjz1IX48nZPBtuIhbd8YNvv9JZ4r3M%3D)) in the top right corner, then start typing the column's name in the **Find column** textfield, then click on the column's name. This will automatically scroll the grid to bring the column on the screen.</ql-infobox>

*   What are the top three countries from which sessions are originated?

![ee4f0348196c0ae2.png](https://cdn.qwiklabs.com/AozLCVR6k2yuhGP6RBEmPJmASKtb3zSKh%2BYIzWO6t3U%3D)

**Answer**: United States, India, United Kingdom

*   What does the grey bar under **totalTransactionRevenue** represent?

![8135e223d2fb3aea.png](https://cdn.qwiklabs.com/Na%2B8%2FJzA5c5J%2Bw23pE6qnA1Z5GyvMLih1MeZs8IQOXE%3D)

**Answer**: Missing values for the `totalTransactionRevenue` field. This means that a lot of sessions in this sample did not generate revenue. Later, we will filter out these values so our final table only has customer transactions and associated revenue.

*   What is the maximum `timeOnSite` in seconds, maximum `pageviews`, and maximum `sessionQualityDim` for the data sample? (Hint: Open the menu to the right of the `timeOnSite` column by clicking ![93c14cbf1f70a561.png](https://cdn.qwiklabs.com/4RIUGfHjcTkc3C8%2F8cuxxnFWjyVXCgyNf0afoBdidWM%3D)the **Column Details** menu)

![e5d3d6810ccffe1f.png](https://cdn.qwiklabs.com/HX6AKSqxJL4U1oX5Xs7%2FjlBSq1YoCdtXuJv0HvLDFvc%3D)

![37ce662aab85f02b.png](https://cdn.qwiklabs.com/Hp2p2%2FjlETLLD0Uz6Nurf8HhWCY3BhUlU4t7n357ymM%3D)

To close the details window, click the **Close Column Details** button in the top right corner. Then repeat the process to view details for the `pageviews` and `sessionQualityDim` columns.

![9bd53860d075ec1a.png](https://cdn.qwiklabs.com/pnBT9jpB%2F%2F1fF5UgnwKlR960bjuSjmPxRGU9oWTkT8o%3D)

**Answers**:

*   **Maximum Time On Site:** 5,561 seconds (or 92 minutes)
*   **Maximum Pageviews:** 155 pages
*   **Maximum Session Quality Dimension:** 97

<ql-infobox>**Note**: Your answers for maximums may vary slightly due to the data sample used by Cloud Dataprep</ql-infobox> <ql-infobox>**Note on averages**: Use extra caution when performing aggregations like averages over a column of data. We need to first ensure fields like `timeOnSite` are only counted once per session. We'll explore the uniqueness of visitor and session data in a later lab.</ql-infobox>

*   Looking at the histogram for `sessionQualityDim`, are the data values evenly distributed?

![147a90f6d73025f8.png](https://cdn.qwiklabs.com/%2BrVfeEfi6%2FzqkUWYRt7qoHSkydmPrXI1gW6s7JDePiQ%3D)

**Answer**: No, they are skewed to lower values (low quality sessions), which is expected.

*   What is the **date** range for the dataset? Hint: Look at **date** field

**Answer**: 8/1/2017 (one day of data)

*   You might see a red bar under the `productSKU` column. If so, what might that mean?

![9fb9608323fdc50.png](https://cdn.qwiklabs.com/eVIJPpwJeLxBZ4KPm1fEyqVhIZ4RvZ9egZiAfxG9KnM%3D)

**Answer**: A red bar indicates mismatched values. While sampling data, Cloud Dataprep attempts to automatically identify the type of each column. If you do not see a red bar for the `productSKU` column, then this means that Cloud Dataprep correctly identified the type for the column (i.e. the String type). If you do see a red bar, then this means that Cloud Dataprep found enough number values in its sampling to determine (incorrectly) that the type should be Integer. Cloud Dataprep also detected some non-integer values and therefore flagged those values as mismatched. In fact, the `productSKU` is not always an integer (for example, a correct value might be "GGOEGOCD078399"). So in this case, Cloud Dataprep incorrectly identified the column type: it should be a string, not an integer. You will fix that later in this lab.

*   Looking at the `v2ProductName` column, what are the most popular products?

![v2-product-name.png](https://cdn.qwiklabs.com/8%2BjK8dAs1yOyjglL%2FSL7KKNJxUA%2BvLa6dTnEHd4Mnd8%3D)

**Answer**: Nest products

*   Looking at the `v2ProductCategory` column, what are some of the most popular product categories?

![v2-product-category.png](https://cdn.qwiklabs.com/fC%2FFGE8m1w4h9Rlq3uke6W6M8Q6i8480z8hownoW%2BbM%3D)

**Answers**:

The most popular product categories are:

*   **Nest**

*   **Bags**

*   (**not set**) (which means that some sessions are not associated with a category)

*   True or False? The most common `productVariant` is `COLOR`.

**Answer**: False. It's **(not set)** because most products do not have variants (80%+)

*   What are the two values in the **type** column?

**Answer**: `PAGE` and `EVENT`

A user can have many different interaction types when browsing your website. Types include recording session data when viewing a PAGE or a special EVENT (like "clicking on a product") and other types. Multiple hit types can be triggered at the exact same time so you will often filter on type to avoid double counting. We'll explore this more in a later analytics lab.

*   What is the maximum `productQuantity`?

**Answer**: 100 (your answer may vary)

`productQuantity` indicates how many units of that product were added to cart. 100 means 100 units of a single product was added.

*   What is the dominant `currencyCode` for transactions?

**Answer**: **USD** (United States Dollar)

*   Are there valid values for `itemQuantity` or `itemRevenue`?

**Answer**: No, they are all `NULL` (or missing) values.

Note: After exploration, in some datasets you may find duplicative or deprecated columns. We will be using `productQuantity` and `productRevenue` fields instead and dropping the `itemQuantity` and `itemRevenue` fields later in this lab to prevent confusion for our report users.

*   What percentage of `transactionId` values are valid? What does this represent for our `ecommerce` dataset?

![efb7c106725f543b.png](https://cdn.qwiklabs.com/GC2hSwg3Od5mn30oPVKO3ntObn4hz78nk5d40yvzw6E%3D)

*   Answer: About 4.6% of transaction IDs have a valid value, which represents the average conversion rate of the website (4.6% of visitors transact).
*   How many `eCommerceAction_type` values are there, and what is the most common value?

<ql-infobox>**Hint**: count the distinct number of histogram columns.</ql-infobox>

![4bd75c218ede9078.png](https://cdn.qwiklabs.com/xEi9Nhxr8C%2B%2FVpQ1UPlkEZwRE1onNpy1MzBv2dd%2BfVk%3D)

**Answers**: There are seven values found in our sample. The most common value is zero `0` which indicates that the type is unknown. This makes sense as the majority of the web sessions on our website will not perform any ecommerce actions as they are just browsing.

*   Using the [schema](https://support.google.com/analytics/answer/3437719?hl=en), what does `eCommerceAction_type = 6` represent?

<ql-infobox>**Hint:** Search for `eCommerceAction` type and read the description for the mapping</ql-infobox>

**Answer**: 6 maps to "Completed purchase". Later in this lab we will ingest this mapping as part of our data pipeline.

![b88fc201e7f4ed2b.png](https://cdn.qwiklabs.com/mo%2BDN33yq0CAs0XYW23ziOq2ci1hF7EI3iz1WghQLuQ%3D)

## Task 6\. Cleaning the data

In this task, you will clean the data by deleting unused columns, eliminating duplicates, creating calculated fields, and filtering out unwanted rows.

### Converting the productSKU column data type

To ensure that the **productSKU** column type is a string data type, open the menu to the right of the **productSKU** column by clicking ![93c14cbf1f70a561.png](https://cdn.qwiklabs.com/4RIUGfHjcTkc3C8%2F8cuxxnFWjyVXCgyNf0afoBdidWM%3D), then click **Change type > String**.

![3ddc0e2c789a1a8b.png](https://cdn.qwiklabs.com/h9XRQZfWpeWFemjzgS90OJIJyC68beZ0TXHPNun6nqM%3D)

Verify that the first step in your data transformation pipeline was created by clicking on the **Recipe** icon:

![2eb040515089d6dc.png](https://cdn.qwiklabs.com/QqCACDwXBSL6Va99bq%2FsnytTzLgd3%2BuHbPqxbDsTr68%3D)

### Deleting unused columns

As we mentioned earlier, we will be deleting the **itemQuantity** and **itemRevenue** columns as they only contain NULL values are not useful for the purpose of this lab.

*   Open the menu for the **itemQuantity** column, and then click **Delete.**

![825f52479020b034.png](https://cdn.qwiklabs.com/D7Pv7GOsFWtLIVQK6TwOINOxqNTdx17IWNlhDO94XL0%3D)

*   Repeat the process to delete the **itemRevenue** column.

### Deduplicating rows

Your team has informed you there may be duplicate session values included in the source dataset. Let's remove these with a new deduplicate step.

1.  Click the **Filter rows** icon in the toolbar, then click **Remove duplicate rows**.

![653b887591f3d0a.png](https://cdn.qwiklabs.com/M7zceE5c8t7br68Fn1RvH6fg3%2FKtNVJwcCVsa6Zuflo%3D)

1.  Click **Add** in the right-hand panel.

2.  Review the recipe that you created so far, it should resemble the following:

![a3493bcad8ecd7b1.png](https://cdn.qwiklabs.com/n%2FrJQzp7UnN%2B13Y1m6KaZoaWNx5q431mEfqZSV4apqs%3D)

### Filtering out sessions without revenue

Your team has asked you to create a table of all user sessions that bought at least one item from the website. Filter out user sessions with NULL revenue.

1.  Under the **totalTransactionRevenue** column, click the grey **Missing values** bar. All rows with a missing value for **totalTransactionRevenue** are now highlighted in red.
2.  In the **Suggestions** panel, in **Delete rows** , click **Add**.

![delete-row.png](https://cdn.qwiklabs.com/AtSqaRx%2Bungs9s7v9ZYAWfapqjbHItEto9kmzDb6oi0%3D)

This step filters your dataset to only include transactions with revenue (where **totalTransactionRevenue** is not NULL).

### Filtering sessions for PAGE views

The dataset contains sessions of different types, for example **PAGE** (for page views) or **EVENT** (for triggered events like "viewed product categories" or "added to cart"). To avoid double counting session pageviews, add a filter to only include page view related hits.

1.  In the histogram below the **type** column, click the bar for **PAGE**. All rows with the type **PAGE** are now highlighted in green.

![935fa1db619751d6.png](https://cdn.qwiklabs.com/judkPO8YaepxEeswZSNuTe%2BuNj41CoaR%2F8zBIIVUO9g%3D)

1.  In the **Suggestions** panel, in **Keep rows**, and click **Add**.

![keep-row.png](https://cdn.qwiklabs.com/%2FXqtI8a4TRqhcFHBu2IjwuNKH8Rt3i8amIMyvPpihgw%3D)

## Task 7\. Enriching the data

Search your [schema documentation](https://support.google.com/analytics/answer/3437719?hl=en/) for **visitId** and read the description to determine if it is unique across all user sessions or just the user.

*   `visitId`: an identifier for this session. This is part of the value usually stored as the `utmb` cookie. This is only unique to the user. For a completely unique ID, you should use a combination of fullVisitorId and visitId.*

As we see, `visitId` is not unique across all users. We will need to create a unique identifier.

### Creating a new column for a unique session ID

As you discovered, the dataset has no single column for a unique visitor session. Create a unique ID for each session by concatenating the **fullVisitorID** and **visitId** fields.

1.  Click on the **Merge columns** icon in the toolbar.

![400992977a254f5d.png](https://cdn.qwiklabs.com/4p8pt0WP6h%2BMERLwCgIWDp%2B5WDIwN8XRyo45nbcw07w%3D)

1.  For **Columns**, select `fullVisitorId` and `visitId`.

2.  For **Separator** type a single hyphen character: `-`.

3.  For the **New column name**, type `unique_session_id`.

![merge.png](https://cdn.qwiklabs.com/Xc1sUxfFNb48M%2FH1G61NgHc8kaSJidHOYyg0yv6WJeg%3D)

1.  Click **Add**.

The `unique_session_id` is now a combination of the `fullVisitorId` and `visitId`. We will explore in a later lab whether each row in this dataset is at the unique session level (one row per user session) or something even more granular.

### Creating a case statement for the ecommerce action type

As you saw earlier, values in the `eCommerceAction_type` column are integers that map to actual ecommerce actions performed in that session. For example, 3 = "Add to Cart" or 5 = "Check out." This mapping will not be immediately apparent to our end users so let's create a calculated field that brings in the value name.

1.  Click on **Conditions** in the toolbar, then click **Case on single column**.

![condition.png](https://cdn.qwiklabs.com/CuaxX3SVElvxsAij2wJThUdkT%2BB%2B2MemzepIijLrQzU%3D)

1.  For **Column to evaluate**, specify `eCommerceAction_type`.

2.  Next to **Cases (1)**, click **Add** 8 times for a total of 9 cases.

![8a688cb82ca9e6c7.png](https://cdn.qwiklabs.com/Ezmb5cxJ05XFNlNYCPAEB%2FccaZDj9jL6rwXTlbaIHso%3D)

1.  For each **Case**, specify the following mapping values (including the single quote characters):

<table>

<tbody>

<tr>

<td colspan="1" rowspan="1">

**Value to compare**

</td>

<td colspan="1" rowspan="1">

**New value**

</td>

</tr>

<tr>

<td colspan="1" rowspan="1">

`0`

</td>

<td colspan="1" rowspan="1">

`'Unknown'`

</td>

</tr>

<tr>

<td colspan="1" rowspan="1">

`1`

</td>

<td colspan="1" rowspan="1">

`'Click through of product lists'`

</td>

</tr>

<tr>

<td colspan="1" rowspan="1">

`2`

</td>

<td colspan="1" rowspan="1">

`'Product detail views'`

</td>

</tr>

<tr>

<td colspan="1" rowspan="1">

`3`

</td>

<td colspan="1" rowspan="1">

`'Add product(s) to cart'`

</td>

</tr>

<tr>

<td colspan="1" rowspan="1">

`4`

</td>

<td colspan="1" rowspan="1">

`'Remove product(s) from cart'`

</td>

</tr>

<tr>

<td colspan="1" rowspan="1">

`5`

</td>

<td colspan="1" rowspan="1">

`'Check out'`

</td>

</tr>

<tr>

<td colspan="1" rowspan="1">

`6`

</td>

<td colspan="1" rowspan="1">

`'Completed purchase'`

</td>

</tr>

<tr>

<td colspan="1" rowspan="1">

`7`

</td>

<td colspan="1" rowspan="1">

`'Refund of purchase'`

</td>

</tr>

<tr>

<td colspan="1" rowspan="1">

`8`

</td>

<td colspan="1" rowspan="1">

`'Checkout options'`

</td>

</tr>

</tbody>

</table>

![3fa23ce8592b0dd6.png](https://cdn.qwiklabs.com/InTnnbRm5%2FKrtkO3cDA70xD77CHwG5iBGGJfo9S2Tqg%3D)

1.  For **New column name**, type `eCommerceAction_label`. Leave the other fields at their default values.

2.  Click **Add**.

### Adjusting values in the totalTransactionRevenue column

As mentioned in the [schema](https://support.google.com/analytics/answer/3437719?hl=en), the **totalTransactionRevenue** column contains values passed to Analytics multiplied by 10^6 (e.g., 2.40 would be given as 2400000). You now divide contents of that column by 10^6 to get the original values.

1.  Open the menu to the right of the **totalTransactionRevenue** column by clicking ![93c14cbf1f70a561.png](https://cdn.qwiklabs.com/4RIUGfHjcTkc3C8%2F8cuxxnFWjyVXCgyNf0afoBdidWM%3D), then select **Calculate** > **Custom formula**.

![54fb00ede7ed8004.png](https://cdn.qwiklabs.com/LAs4nQx3xR5AOiXmVbCnX1amcUbzsBn%2FiQizgdfJ%2F%2B8%3D)

1.  For **Formula**, type: `DIVIDE(totalTransactionRevenue,1000000)` and for **New column name**, type: `totalTransactionRevenue1`. Notice the preview for the transformation:

![custom_formula.png](https://cdn.qwiklabs.com/mtcOJdCsgTzBjkRHChesabCKcEpBGx8EW8A8alwSa70%3D)

1.  Click **Add**.

2.  To convert the new `totalTransactionRevenue1` column's type to a decimal data type, open the menu to the right of the `totalTransactionRevenue1` column by clicking ![93c14cbf1f70a561.png](https://cdn.qwiklabs.com/4RIUGfHjcTkc3C8%2F8cuxxnFWjyVXCgyNf0afoBdidWM%3D), then click **Change type > Decimal**.

![type_decimal.png](https://cdn.qwiklabs.com/sRgQKuW%2FuYevo%2FCc%2ByyE%2B0HxQGqRkesxkAmpHGt2xjM%3D)

1.  Review the full list of steps in your recipe:

![recipee-update.png](https://cdn.qwiklabs.com/SEoWpS%2BEF81apBjMygCHEN%2BYZBtprEnhLn7U%2Bml7maI%3D)

1.  You can now click **Run Job**.

## Task 8\. Running and scheduling Cloud Dataprep jobs to BigQuery

<ql-infobox>**Challenge**: Now that you are satisfied with the flow, it's time to execute the transformation recipe against your source dataset. The challenge for you is to load the output of the job into the BigQuery dataset that you created earlier. Make sure you load the output into a separate table and name it `revenue_reporting`.</ql-infobox>

Once your Cloud Dataprep job is completed, refresh your BigQuery page and confirm that the output table **revenue_reporting** exists.

Click _Check my progress_ to verify the objective. <ql-activity-tracking step="1">Verify if the Cloud Dataprep jobs output the data to BigQuery</ql-activity-tracking>

While it's running, you can also schedule the execution of pipeline in the next step so the job can be re-run automatically on a regular basis to account for newer data. Note: You can navigate and perform other operations while jobs are running.

1.  You will now schedule a recurrent job execution. Click the **Flows** icon on the left of the screen.

![1bed08c0d2bbbc92.png](https://cdn.qwiklabs.com/PRlXYizjAcRcqU45KvYIEmO6tsHiFwJXvU%2B1Apn2lJ0%3D)

1.  On the right of your **Ecommerce Analytics Pipeline** flow click the **More** icon ( ![992c7da113be1fab.png](https://cdn.qwiklabs.com/guJ5AJajJyZe8C8oAOoeL3bwL24x7k2ty3U19pZa0sE%3D)), then click **Schedule**.

![schedule.png](https://cdn.qwiklabs.com/eprFNr50u5c7VJiTKRBBqllYHVKx8MZhf8PdbCiiXe8%3D)

1.  In the **Add schedule** dialog:

*   For **Frequency**, select **Weekly**.

*   For day of week, select **Saturday** and unselect **Sunday**.

*   For time, enter **3:00** and select **AM**.

1.  Click **Save**.

![1afb29a7249b36e0.png](https://cdn.qwiklabs.com/pUCkMW2fIzrD6fSBxzrDce2jCfZoPLDeQP68OEEE3YQ%3D)

The job is now scheduled to trigger every Saturday at 3AM. You can see the schedule on the top panel.

You will see the following notification at the top of your flow:

![no-scheduled-dest.png](https://cdn.qwiklabs.com/XazfxzTCmNIuexKqH3eb8MwtrEWte%2FUy85QmtCe%2F8js%3D)

Cloud Dataprep allows you to set different publishing destinations for manual job runs versus scheduled jobs.

1.  Click on the **Output** node to see the Details. (**Right click** and **View Details** if the panel is not open)

![output-node.png](https://cdn.qwiklabs.com/v3Qusc%2BE2Tit3cP8B3nnNbP2%2FbPxkpZve7Mt%2FitytDY%3D)

1.  In the **Destinations** section, click on **Add** next to Scheduled Destinations to add a new publishing destination for scheduled jobs.

![scheduled-destinations.png](https://cdn.qwiklabs.com/8P9BdnOs34J8T3Je7lQGohLk3zatkBh7o8TVQPu%2FbVs%3D)

<ql-infobox>**Challenge:** Create another table to load the scheduled jobs into. Make sure you load the output into a separate table than the manual job and name it `revenue_reporting_recurring`. Since this table would be run weekly with the full data, make sure that the results overwrite the full table with each run.</ql-infobox>

1.  Click the **Jobs** icon on the left of the screen.

![19afbf69228b5735.png](https://cdn.qwiklabs.com/B30EPBxqQori4%2BC9hUlWQBt2XB9iAVIpAGaHfKVaMtU%3D)

1.  You see the list of jobs, and wait until your job is marked as **Completed**.

![job1.png](https://cdn.qwiklabs.com/wfRPABiApiO4C%2BwLKbTgiLE%2BMfNC2BFAGFoYSD2o5lM%3D)

## Congratulations!

You've successfully explored your ecommerce dataset and created a recurring data transformation pipeline with Cloud Dataprep.

![Data_Engineering_badge_125.png](https://cdn.qwiklabs.com/XFebDQwepOTnzaxXtr01EzuRLa43wF9q3%2BaQtsE52GM%3D)

### Finish Your Quest

This self-paced lab is part of the Qwiklabs [Data Engineering](https://google.qwiklabs.com/quests/25) Quest. A Quest is a series of related labs that form a learning path. Completing this Quest earns you the badge above, to recognize your achievement. You can make your badge (or badges) public and link to them in your online resume or social media account. Enroll in this Quest and get immediate completion credit if you've taken this lab. [See other available Qwiklabs Quests](https://google.qwiklabs.com/catalog).

### Take Your Next Lab

Continue your Quest with [Building an IoT Analytics Pipeline on Google Cloud](https://google.qwiklabs.com/catalog_lab/694), or check out these suggestions:

*   [ETL Processing on Google Cloud Using Dataflow and BigQuery](https://google.qwiklabs.com/catalog_lab/1341)

*   [Predict Visitor Purchases with a Classification Model in BQML](https://google.qwiklabs.com/catalog_lab/1101)

### Next steps / learn more

Do you already have a Google Analytics account and want to query your own datasets in BigQuery? Follow this [export guide](https://support.google.com/analytics/answer/3416092).

### Google Cloud Training & Certification

...helps you make the most of Google Cloud technologies. [Our classes](https://cloud.google.com/training/courses) include technical skills and best practices to help you get up to speed quickly and continue your learning journey. We offer fundamental to advanced level training, with on-demand, live, and virtual options to suit your busy schedule. [Certifications](https://cloud.google.com/certification/) help you validate and prove your skill and expertise in Google Cloud technologies.

##### Last Tested Date: November 30, 2020

##### Last Updated Date: November 30, 2020

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

<div class="js-lab-content-outline lab-content__outline">[GSP430](#step1)[Overview](#step2)[Task 1\. Setting up your development environment](#step3)[Task 2\. Creating a BigQuery Dataset](#step4)[Task 3\. Opening Cloud Dataprep](#step5)[Task 4\. Connecting BigQuery data to Cloud Dataprep](#step6)[Task 5\. Exploring ecommerce data fields with a UI](#step7)[Task 6\. Cleaning the data](#step8)[Task 7\. Enriching the data](#step9)[Task 8\. Running and scheduling Cloud Dataprep jobs to BigQuery](#step10)[Congratulations!](#step11)</div>

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

Cloud Dataprep by Trifacta is an intelligent data service for visually exploring, cleaning, and preparing structured and unstructured data for analysis. In this lab you will explore the Cloud Dataprep UI to build a data transformation pipeline.

This lab is included in these quests: [Data Engineering](/quests/25), [Engineer Data in Google Cloud](/quests/132). If you complete this lab you'll receive credit for it when you enroll in one of these quests.

**Duration:** 0m setup · 75m access · 60m completion

<span>**Levels:** advanced</span>

**Permalink:** [https://google.qwiklabs.com/catalog_lab/1062](https://google.qwiklabs.com/catalog_lab/1062)

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

<div class="controls"><input class="hidden" type="hidden" value="1062" name="lab_review[lab_id]" id="lab_review_lab_id"></div>

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

<div class="lab-access-modal"><input type="hidden" name="id" id="id" value="4415"> <input type="hidden" name="user_id" id="user_id" value="4061609"> <input type="hidden" name="launch_with_credits" id="launch_with_credits" value="0" class="js-launch-with-credits-input"> <input type="hidden" name="launch_with_subs" id="launch_with_subs" value="0" class="js-launch-with-subscription-input">

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