# Predict Visitor Purchases with a Classification Model in BQML

<div class="lab-preamble__details subtitle-headline-1"><span>1 hour 15 minutes</span> <span>7 Credits</span>

<div class="lab__rating">[](/focuses/1794/reviews?parent=catalog)<a data-target="#lab-review-modal" data-toggle="modal">Rate Lab</a></div>

</div>

</div>

<div class="js-markdown-instructions markdown-lab-instructions" id="markdown-lab-instructions">

## GSP229

![Google Cloud Self-Paced Labs](https://cdn.qwiklabs.com/GMOHykaqmlTHiqEeQXTySaMXYPHeIvaqa2qHEzw6Occ%3D)

## Overview

BigQuery is Google's fully managed, NoOps, low cost analytics database. With BigQuery you can query terabytes and terabytes of data without having any infrastructure to manage or needing a database administrator. BigQuery uses SQL and can take advantage of the pay-as-you-go model. BigQuery allows you to focus on analyzing data to find meaningful insights.

[BigQuery Machine Learning](https://cloud.google.com/bigquery/docs/bigqueryml-analyst-start) (BQML, product in beta) is a new feature in BigQuery where data analysts can create, train, evaluate, and predict with machine learning models with minimal coding.

There is a newly available [ecommerce dataset](https://www.en.advertisercommunity.com/t5/Articles/Introducing-the-Google-Analytics-Sample-Dataset-for-BigQuery/ba-p/1676331#) that has millions of Google Analytics records for the [Google Merchandise Store](https://shop.googlemerchandisestore.com/) loaded into BigQuery. In this lab you will use this data to run some typical queries that businesses would want to know about their customers' purchasing habits.

### Objectives

In this lab, you learn to perform the following tasks:

*   Use BigQuery to find public datasets

*   Query and explore the ecommerce dataset

*   Create a training and evaluation dataset to be used for batch prediction

*   Create a classification (logistic regression) model in BQML

*   Evaluate the performance of your machine learning model

*   Predict and rank the probability that a visitor will make a purchase

## Set up your environment

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

### Open BigQuery Console

In the Google Cloud Console, select **Navigation menu** > **BigQuery**:

![BigQuery_menu.png](https://cdn.qwiklabs.com/QwUX6algdXPgIThqBlgB2rnqGkoQUUANkl3xicD06uc%3D)

The **Welcome to BigQuery in the Cloud Console** message box opens. This message box provides a link to the quickstart guide and the release notes.

Click **Done**.

The BigQuery console opens.

![bq-console.png](https://cdn.qwiklabs.com/vd3QNHGB4BAyHjAvOHw9qD0iqCPNaHAzY657z%2FGWLtY%3D)

### Access the course dataset

Once BigQuery is open, open on the below direct link in a new browser tab to bring the public **data-to-insights** project into your BigQuery projects panel:

*   [https://console.cloud.google.com/bigquery?p=data-to-insights&d=ecommerce&t=web_analytics&page=table](https://console.cloud.google.com/bigquery?p=data-to-insights&d=ecommerce&t=web_analytics&page=table)

![dataset-added.png](https://cdn.qwiklabs.com/wAOpgsMVrpanjH%2F8OqKMFHWaIUfTMjG6C1O8HBYRlzM%3D)

The field definitions for the **data-to-insights** ecommerce dataset are [here](https://support.google.com/analytics/answer/3437719?hl=en). Keep the link open in a new tab for reference.

To avoid confusion, close one of the BigQuery browser tabs.

## Explore ecommerce data

**Scenario:** Your data analyst team exported the Google Analytics logs for an ecommerce website into BigQuery and created a new table of all the raw ecommerce visitor session data for you to explore. Using this data, you'll try to answer a few questions.

**Question:** Out of the total visitors who visited our website, what % made a purchase?

Copy and paste the following query into the BigQuery **Query Editor**:

    #standardSQL
    WITH visitors AS(
    SELECT
    COUNT(DISTINCT fullVisitorId) AS total_visitors
    FROM `data-to-insights.ecommerce.web_analytics`
    ),

    purchasers AS(
    SELECT
    COUNT(DISTINCT fullVisitorId) AS total_purchasers
    FROM `data-to-insights.ecommerce.web_analytics`
    WHERE totals.transactions IS NOT NULL
    )

    SELECT
      total_visitors,
      total_purchasers,
      total_purchasers / total_visitors AS conversion_rate
    FROM visitors, purchasers

Click **Run**.

The result: 2.69%

**Question:** What are the top 5 selling products?

Click **Compose New Query** to clear the previous query, and then add the following query in the **Query editor**:

    SELECT
      p.v2ProductName,
      p.v2ProductCategory,
      SUM(p.productQuantity) AS units_sold,
      ROUND(SUM(p.localProductRevenue/1000000),2) AS revenue
    FROM `data-to-insights.ecommerce.web_analytics`,
    UNNEST(hits) AS h,
    UNNEST(h.product) AS p
    GROUP BY 1, 2
    ORDER BY revenue DESC
    LIMIT 5;

Click **Run**

The result:

<table>

<tbody>

<tr>

<td colspan="1" rowspan="1">

**Row**

</td>

<td colspan="1" rowspan="1">

**v2ProductName**

</td>

<td colspan="1" rowspan="1">

**v2ProductCategory**

</td>

<td colspan="1" rowspan="1">

**units_sold**

</td>

<td colspan="1" rowspan="1">

**revenue**

</td>

</tr>

<tr>

<td colspan="1" rowspan="1">

1

</td>

<td colspan="1" rowspan="1">

Nest® Learning Thermostat 3rd Gen-USA - Stainless Steel

</td>

<td colspan="1" rowspan="1">

Nest-USA

</td>

<td colspan="1" rowspan="1">

17651

</td>

<td colspan="1" rowspan="1">

870976.95

</td>

</tr>

<tr>

<td colspan="1" rowspan="1">

2

</td>

<td colspan="1" rowspan="1">

Nest® Cam Outdoor Security Camera - USA

</td>

<td colspan="1" rowspan="1">

Nest-USA

</td>

<td colspan="1" rowspan="1">

16930

</td>

<td colspan="1" rowspan="1">

684034.55

</td>

</tr>

<tr>

<td colspan="1" rowspan="1">

3

</td>

<td colspan="1" rowspan="1">

Nest® Cam Indoor Security Camera - USA

</td>

<td colspan="1" rowspan="1">

Nest-USA

</td>

<td colspan="1" rowspan="1">

14155

</td>

<td colspan="1" rowspan="1">

548104.47

</td>

</tr>

<tr>

<td colspan="1" rowspan="1">

4

</td>

<td colspan="1" rowspan="1">

Nest® Protect Smoke + CO White Wired Alarm-USA

</td>

<td colspan="1" rowspan="1">

Nest-USA

</td>

<td colspan="1" rowspan="1">

6394

</td>

<td colspan="1" rowspan="1">

178937.6

</td>

</tr>

<tr>

<td colspan="1" rowspan="1">

5

</td>

<td colspan="1" rowspan="1">

Nest® Protect Smoke + CO White Battery Alarm-USA

</td>

<td colspan="1" rowspan="1">

Nest-USA

</td>

<td colspan="1" rowspan="1">

6340

</td>

<td colspan="1" rowspan="1">

178572.4

</td>

</tr>

</tbody>

</table>

**Question:** How many visitors bought on subsequent visits to the website?

Run the following query to find out:

    # visitors who bought on a return visit (could have bought on first as well
    WITH all_visitor_stats AS (
    SELECT
      fullvisitorid, # 741,721 unique visitors
      IF(COUNTIF(totals.transactions > 0 AND totals.newVisits IS NULL) > 0, 1, 0) AS will_buy_on_return_visit
      FROM `data-to-insights.ecommerce.web_analytics`
      GROUP BY fullvisitorid
    )

    SELECT
      COUNT(DISTINCT fullvisitorid) AS total_visitors,
      will_buy_on_return_visit
    FROM all_visitor_stats
    GROUP BY will_buy_on_return_visit

The results:

<table>

<tbody>

<tr>

<td colspan="1" rowspan="1">

**Row**

</td>

<td colspan="1" rowspan="1">

**total_visitors**

</td>

<td colspan="1" rowspan="1">

**will_buy_on_return_visit**

</td>

</tr>

<tr>

<td colspan="1" rowspan="1">

1

</td>

<td colspan="1" rowspan="1">

729848

</td>

<td colspan="1" rowspan="1">

0

</td>

</tr>

<tr>

<td colspan="1" rowspan="1">

2

</td>

<td colspan="1" rowspan="1">

11873

</td>

<td colspan="1" rowspan="1">

1

</td>

</tr>

</tbody>

</table>

Analyzing the results, you can see that (11873 / 741721) = 1.6% of total visitors will return and purchase from the website. This includes the subset of visitors who bought on their very first session and then came back and bought again.

**Question:** What are some of the reasons a typical ecommerce customer will browse but not buy until a later visit?

**Answer:** Although there is no one right answer, one popular reason is comparison shopping between different ecommerce sites before ultimately making a purchase decision. This is very common for luxury goods where significant up-front research and comparison is required by the customer before deciding (think car purchases) but also true to a lesser extent for the merchandise on this site (t-shirts, accessories, etc).

In the world of online marketing, identifying and marketing to these future customers based on the characteristics of their first visit will increase conversion rates and reduce the outflow to competitor sites.

## Identify an objective

Now you will create a Machine Learning model in BigQuery to predict whether or not a new user is likely to purchase in the future. Identifying these high-value users can help your marketing team target them with special promotions and ad campaigns to ensure a conversion while they comparison shop between visits to your ecommerce site.

## Select features and create your training dataset

Google Analytics captures a wide variety of dimensions and measures about a user's visit on this ecommerce website. Browse the complete list of fields [here](https://support.google.com/analytics/answer/3437719?hl=en) and then [preview the demo dataset](https://console.cloud.google.com/bigquery?project=&p=data-to-insights&d=ecommerce&t=web_analytics&page=table) to find useful features that will help a machine learning model understand the relationship between data about a visitor's first time on your website and whether they will return and make a purchase.

Your team decides to test whether these two fields are good inputs for your classification model:

*   `totals.bounces` (whether the visitor left the website immediately)
*   `totals.timeOnSite` (how long the visitor was on our website)

**Question:** What are the risks of only using the above two fields?

**Answer:** Machine learning is only as good as the training data that is fed into it. If there isn't enough information for the model to determine and learn the relationship between your input features and your label (in this case, whether the visitor bought in the future) then you will not have an accurate model. While training a model on just these two fields is a start, you will see if they're good enough to produce an accurate model.

In the **Query editor**, run the following query:

    SELECT
      * EXCEPT(fullVisitorId)
    FROM

      # features
      (SELECT
        fullVisitorId,
        IFNULL(totals.bounces, 0) AS bounces,
        IFNULL(totals.timeOnSite, 0) AS time_on_site
      FROM
        `data-to-insights.ecommerce.web_analytics`
      WHERE
        totals.newVisits = 1)
      JOIN
      (SELECT
        fullvisitorid,
        IF(COUNTIF(totals.transactions > 0 AND totals.newVisits IS NULL) > 0, 1, 0) AS will_buy_on_return_visit
      FROM
          `data-to-insights.ecommerce.web_analytics`
      GROUP BY fullvisitorid)
      USING (fullVisitorId)
    ORDER BY time_on_site DESC
    LIMIT 10;

Results:

<table>

<tbody>

<tr>

<td colspan="1" rowspan="1">

**Row**

</td>

<td colspan="1" rowspan="1">

**bounces**

</td>

<td colspan="1" rowspan="1">

**time_on_site**

</td>

<td colspan="1" rowspan="1">

**will_buy_on_return_visit**

</td>

</tr>

<tr>

<td colspan="1" rowspan="1">

1

</td>

<td colspan="1" rowspan="1">

0

</td>

<td colspan="1" rowspan="1">

15047

</td>

<td colspan="1" rowspan="1">

0

</td>

</tr>

<tr>

<td colspan="1" rowspan="1">

2

</td>

<td colspan="1" rowspan="1">

0

</td>

<td colspan="1" rowspan="1">

12136

</td>

<td colspan="1" rowspan="1">

0

</td>

</tr>

<tr>

<td colspan="1" rowspan="1">

3

</td>

<td colspan="1" rowspan="1">

0

</td>

<td colspan="1" rowspan="1">

11201

</td>

<td colspan="1" rowspan="1">

0

</td>

</tr>

<tr>

<td colspan="1" rowspan="1">

4

</td>

<td colspan="1" rowspan="1">

0

</td>

<td colspan="1" rowspan="1">

10046

</td>

<td colspan="1" rowspan="1">

0

</td>

</tr>

<tr>

<td colspan="1" rowspan="1">

5

</td>

<td colspan="1" rowspan="1">

0

</td>

<td colspan="1" rowspan="1">

9974

</td>

<td colspan="1" rowspan="1">

0

</td>

</tr>

<tr>

<td colspan="1" rowspan="1">

6

</td>

<td colspan="1" rowspan="1">

0

</td>

<td colspan="1" rowspan="1">

9564

</td>

<td colspan="1" rowspan="1">

0

</td>

</tr>

<tr>

<td colspan="1" rowspan="1">

7

</td>

<td colspan="1" rowspan="1">

0

</td>

<td colspan="1" rowspan="1">

9520

</td>

<td colspan="1" rowspan="1">

0

</td>

</tr>

<tr>

<td colspan="1" rowspan="1">

8

</td>

<td colspan="1" rowspan="1">

0

</td>

<td colspan="1" rowspan="1">

9275

</td>

<td colspan="1" rowspan="1">

1

</td>

</tr>

<tr>

<td colspan="1" rowspan="1">

9

</td>

<td colspan="1" rowspan="1">

0

</td>

<td colspan="1" rowspan="1">

9138

</td>

<td colspan="1" rowspan="1">

0

</td>

</tr>

<tr>

<td colspan="1" rowspan="1">

10

</td>

<td colspan="1" rowspan="1">

0

</td>

<td colspan="1" rowspan="1">

8872

</td>

<td colspan="1" rowspan="1">

0

</td>

</tr>

</tbody>

</table>

**Question:** Which fields are the input features and the label?

**Answer** The inputs are **bounces** and **time_on_site**. The label is **will_buy_on_return_visit**.

**Question:** Which two fields are known after a visitor's first session?

**Answer:** **bounces** and **time_on_site** are known after a visitor's first session.

**Question:** Which field isn't known until later in the future?

**Answer:** **will_buy_on_return_visit** is not known after the first visit. Again, you're predicting for a subset of users who returned to your website and purchased. Since you don't know the future at prediction time, you cannot say with certainty whether a new visitor come back and purchase. The value of building an ML model is to get the probability of future purchase based on the data gleaned about their first session.

**Question:** Looking at the initial data results, do you think **time_on_site** and **bounces** will be a good indicator of whether the user will return and purchase or not?

**Answer:** It's often too early to tell before training and evaluating the model, but at first glance out of the top 10 `time_on_site`, only 1 customer returned to buy, which isn't very promising. Let's see how well the model does.

## Create a BigQuery dataset to store models

Next, create a new BigQuery dataset which will also store your ML models.

1.  In the left pane, click on your project name (starts with `qwiklabs-gcp-...`), and then click **Create Dataset**.

![BQ_CreateDataset.png](https://cdn.qwiklabs.com/QZ8JT8gSgMzUxN%2B7ZxxLmlnMDDAc8WARu5H8m2JhSbQ%3D)

1.  In the **Create Dataset** dialog:

*   For **Dataset ID**, type "ecommerce".
*   Leave the other values at their defaults.

![dataset_name.png](https://cdn.qwiklabs.com/8gj1ZaX%2Bw1qHo38EtfkJh0249QgMlU8gJwk%2B%2FNskn18%3D)

1.  Click **Create dataset**.

Click **Check my progress** to verify the objective.

<ql-activity-tracking step="1">Create a new dataset</ql-activity-tracking>

## Select a BQML model type and specify options

Now that you have your initial features selected, you are now ready to create your first ML model in BigQuery.

There are the two model types to choose from:

<table>

<tbody>

<tr>

<td colspan="1" rowspan="1">

**Model**

</td>

<td colspan="1" rowspan="1">

**Model Type**

</td>

<td colspan="1" rowspan="1">

**Label Data type**

</td>

<td colspan="1" rowspan="1">

**Example**

</td>

</tr>

<tr>

<td colspan="1" rowspan="1">

Forecasting

</td>

<td colspan="1" rowspan="1">

linear_reg

</td>

<td colspan="1" rowspan="1">

Numeric value (typically an integer or floating point)

</td>

<td colspan="1" rowspan="1">

Forecast sales figures for next year given historical sales data.

</td>

</tr>

<tr>

<td colspan="1" rowspan="1">

Classification

</td>

<td colspan="1" rowspan="1">

logistic_reg

</td>

<td colspan="1" rowspan="1">

0 or 1 for binary classification

</td>

<td colspan="1" rowspan="1">

Classify an email as spam or not spam given the context.

</td>

</tr>

</tbody>

</table>

**Note:** There are many additional model types used in Machine Learning (like Neural Networks and decision trees) and available using libraries like [TensorFlow](https://www.tensorflow.org/tutorials/). At time of writing, BQML supports the two listed above.

**Which model type should you choose?**

Since you are bucketing visitors into "will buy in future" or "won't buy in future", use `logistic_reg` in a classification model.

The following query creates a model and specifies model options. Run this query to train your model:

    CREATE OR REPLACE MODEL `ecommerce.classification_model`
    OPTIONS
    (
    model_type='logistic_reg',
    labels = ['will_buy_on_return_visit']
    )
    AS

    #standardSQL
    SELECT
      * EXCEPT(fullVisitorId)
    FROM

      # features
      (SELECT
        fullVisitorId,
        IFNULL(totals.bounces, 0) AS bounces,
        IFNULL(totals.timeOnSite, 0) AS time_on_site
      FROM
        `data-to-insights.ecommerce.web_analytics`
      WHERE
        totals.newVisits = 1
        AND date BETWEEN '20160801' AND '20170430') # train on first 9 months
      JOIN
      (SELECT
        fullvisitorid,
        IF(COUNTIF(totals.transactions > 0 AND totals.newVisits IS NULL) > 0, 1, 0) AS will_buy_on_return_visit
      FROM
          `data-to-insights.ecommerce.web_analytics`
      GROUP BY fullvisitorid)
      USING (fullVisitorId)
    ;

Wait for the model to train (5 - 10 minutes).

<aside>**Note:** You cannot feed all of your available data to the model during training since you need to save some unseen data points for model evaluation and testing. To accomplish this, add a WHERE clause condition is being used to filter and train on only the first 9 months of session data in your 12 month dataset.</aside>

Click **Check my progress** to verify the objective.

<ql-activity-tracking step="2">Create a model and specify model options</ql-activity-tracking>

After your model is trained, you will see the message "This statement created a new model named qwiklabs-gcp-xxxxxxxxx:ecommerce.classification_model".

Click **Go to model**.

Look inside the ecommerce dataset and confirm **classification_model** now appears.

![classification-model.png](https://cdn.qwiklabs.com/4J4Zcu1NA70NBc07evKZsQ4xw9skVdQt90DiTgpviHk%3D)

Next, you evaluate the performance of the model against new unseen evaluation data.

## Evaluate classification model performance

### Select your performance criteria

For classification problems in ML, you want to minimize the False Positive Rate (predict that the user will return and purchase and they don't) and maximize the True Positive Rate (predict that the user will return and purchase and they do).

This relationship is visualized with a ROC (Receiver Operating Characteristic) curve like the one shown here, where you try to maximize the area under the curve or AUC:

![42fc12b6077e4784.png](https://cdn.qwiklabs.com/GNW5Bw%2B8bviep9OK201QGPzaAEnKKyoIkDChUHeVdFw%3D)

In BQML, **roc_auc** is simply a queryable field when evaluating your trained ML model.

Now that training is complete, run this query to evaluate how well the model performs using `ML.EVALUATE`:

    SELECT
      roc_auc,
      CASE
        WHEN roc_auc > .9 THEN 'good'
        WHEN roc_auc > .8 THEN 'fair'
        WHEN roc_auc > .7 THEN 'decent'
        WHEN roc_auc > .6 THEN 'not great'
      ELSE 'poor' END AS model_quality
    FROM
      ML.EVALUATE(MODEL ecommerce.classification_model,  (

    SELECT
      * EXCEPT(fullVisitorId)
    FROM

      # features
      (SELECT
        fullVisitorId,
        IFNULL(totals.bounces, 0) AS bounces,
        IFNULL(totals.timeOnSite, 0) AS time_on_site
      FROM
        `data-to-insights.ecommerce.web_analytics`
      WHERE
        totals.newVisits = 1
        AND date BETWEEN '20170501' AND '20170630') # eval on 2 months
      JOIN
      (SELECT
        fullvisitorid,
        IF(COUNTIF(totals.transactions > 0 AND totals.newVisits IS NULL) > 0, 1, 0) AS will_buy_on_return_visit
      FROM
          `data-to-insights.ecommerce.web_analytics`
      GROUP BY fullvisitorid)
      USING (fullVisitorId)

    ));

You should see the following result:

<table>

<tbody>

<tr>

<td colspan="1" rowspan="1">

Row

</td>

<td colspan="1" rowspan="1">

roc_auc

</td>

<td colspan="1" rowspan="1">

model_quality

</td>

</tr>

<tr>

<td colspan="1" rowspan="1">

1

</td>

<td colspan="1" rowspan="1">

0.724588

</td>

<td colspan="1" rowspan="1">

decent

</td>

</tr>

</tbody>

</table>

After evaluating your model you get a **roc_auc** of 0.72, which shows the model has decent, but not great, predictive power. Since the goal is to get the area under the curve as close to 1.0 as possible, there is room for improvement.

Click **Check my progress** to verify the objective.

<ql-activity-tracking step="3">Evaluate classification model performance</ql-activity-tracking>

## Improve model performance with Feature Engineering

As was hinted at earlier, there are many more features in the dataset that may help the model better understand the relationship between a visitor's first session and the likelihood that they will purchase on a subsequent visit.

Add some new features and create a second machine learning model called `classification_model_2`:

*   How far the visitor got in the checkout process on their first visit
*   Where the visitor came from (traffic source: organic search, referring site etc..)
*   Device category (mobile, tablet, desktop)
*   Geographic information (country)

Create this second model by running the below query:

    CREATE OR REPLACE MODEL `ecommerce.classification_model_2`
    OPTIONS
      (model_type='logistic_reg', labels = ['will_buy_on_return_visit']) AS

    WITH all_visitor_stats AS (
    SELECT
      fullvisitorid,
      IF(COUNTIF(totals.transactions > 0 AND totals.newVisits IS NULL) > 0, 1, 0) AS will_buy_on_return_visit
      FROM `data-to-insights.ecommerce.web_analytics`
      GROUP BY fullvisitorid
    )

    # add in new features
    SELECT * EXCEPT(unique_session_id) FROM (

      SELECT
          CONCAT(fullvisitorid, CAST(visitId AS STRING)) AS unique_session_id,

          # labels
          will_buy_on_return_visit,

          MAX(CAST(h.eCommerceAction.action_type AS INT64)) AS latest_ecommerce_progress,

          # behavior on the site
          IFNULL(totals.bounces, 0) AS bounces,
          IFNULL(totals.timeOnSite, 0) AS time_on_site,
          IFNULL(totals.pageviews, 0) AS pageviews,

          # where the visitor came from
          trafficSource.source,
          trafficSource.medium,
          channelGrouping,

          # mobile or desktop
          device.deviceCategory,

          # geographic
          IFNULL(geoNetwork.country, "") AS country

      FROM `data-to-insights.ecommerce.web_analytics`,
         UNNEST(hits) AS h

        JOIN all_visitor_stats USING(fullvisitorid)

      WHERE 1=1
        # only predict for new visits
        AND totals.newVisits = 1
        AND date BETWEEN '20160801' AND '20170430' # train 9 months

      GROUP BY
      unique_session_id,
      will_buy_on_return_visit,
      bounces,
      time_on_site,
      totals.pageviews,
      trafficSource.source,
      trafficSource.medium,
      channelGrouping,
      device.deviceCategory,
      country
    );

<aside>**Note:** You are still training on the same first 9 months of data, even with this new model. It's important to have the same training dataset so you can be certain a better model output is attributable to better input features and not new or different training data.</aside>

A key new feature that was added to the training dataset query is the maximum checkout progress each visitor reached in their session, which is recorded in the field `hits.eCommerceAction.action_type`. If you search for that field in the [field definitions](https://support.google.com/analytics/answer/3437719?hl=en) you will see the field mapping of 6 = Completed Purchase.

<aside>The web analytics dataset has nested and repeated fields like [ARRAYS](https://cloud.google.com/bigquery/docs/reference/standard-sql/arrays) which need to broken apart into separate rows in your dataset. This is accomplished by using the UNNEST() function, which you can see in the above query.</aside>

Wait for the new model to finish training (5-10 minutes).

Click **Check my progress** to verify the objective.

<ql-activity-tracking step="4">Improve model performance with Feature Engineering(Create second model)</ql-activity-tracking>

Evaluate this new model to see if there is better predictive power:

    #standardSQL
    SELECT
      roc_auc,
      CASE
        WHEN roc_auc > .9 THEN 'good'
        WHEN roc_auc > .8 THEN 'fair'
        WHEN roc_auc > .7 THEN 'decent'
        WHEN roc_auc > .6 THEN 'not great'
      ELSE 'poor' END AS model_quality
    FROM
      ML.EVALUATE(MODEL ecommerce.classification_model_2,  (

    WITH all_visitor_stats AS (
    SELECT
      fullvisitorid,
      IF(COUNTIF(totals.transactions > 0 AND totals.newVisits IS NULL) > 0, 1, 0) AS will_buy_on_return_visit
      FROM `data-to-insights.ecommerce.web_analytics`
      GROUP BY fullvisitorid
    )

    # add in new features
    SELECT * EXCEPT(unique_session_id) FROM (

      SELECT
          CONCAT(fullvisitorid, CAST(visitId AS STRING)) AS unique_session_id,

          # labels
          will_buy_on_return_visit,

          MAX(CAST(h.eCommerceAction.action_type AS INT64)) AS latest_ecommerce_progress,

          # behavior on the site
          IFNULL(totals.bounces, 0) AS bounces,
          IFNULL(totals.timeOnSite, 0) AS time_on_site,
          totals.pageviews,

          # where the visitor came from
          trafficSource.source,
          trafficSource.medium,
          channelGrouping,

          # mobile or desktop
          device.deviceCategory,

          # geographic
          IFNULL(geoNetwork.country, "") AS country

      FROM `data-to-insights.ecommerce.web_analytics`,
         UNNEST(hits) AS h

        JOIN all_visitor_stats USING(fullvisitorid)

      WHERE 1=1
        # only predict for new visits
        AND totals.newVisits = 1
        AND date BETWEEN '20170501' AND '20170630' # eval 2 months

      GROUP BY
      unique_session_id,
      will_buy_on_return_visit,
      bounces,
      time_on_site,
      totals.pageviews,
      trafficSource.source,
      trafficSource.medium,
      channelGrouping,
      device.deviceCategory,
      country
    )
    ));

(Output)

<table>

<tbody>

<tr>

<td colspan="1" rowspan="1">

Row

</td>

<td colspan="1" rowspan="1">

roc_auc

</td>

<td colspan="1" rowspan="1">

model_quality

</td>

</tr>

<tr>

<td colspan="1" rowspan="1">

1

</td>

<td colspan="1" rowspan="1">

0.910382

</td>

<td colspan="1" rowspan="1">

good

</td>

</tr>

</tbody>

</table>

With this new model you now get a **roc_auc** of 0.91 which is significantly better than the first model.

Now that you have a trained model, time to make some predictions.

Click **Check my progress** to verify the objective.

<ql-activity-tracking step="5">Improve model performance with Feature Engineering(Better predictive power)</ql-activity-tracking>

## Predict which new visitors will come back and purchase

Next you will write a query to predict which new visitors will come back and make a purchase.

The prediction query below uses the improved classification model to predict the probability that a first-time visitor to the Google Merchandise Store will make a purchase in a later visit:

    SELECT
    *
    FROM
      ml.PREDICT(MODEL `ecommerce.classification_model_2`,
       (

    WITH all_visitor_stats AS (
    SELECT
      fullvisitorid,
      IF(COUNTIF(totals.transactions > 0 AND totals.newVisits IS NULL) > 0, 1, 0) AS will_buy_on_return_visit
      FROM `data-to-insights.ecommerce.web_analytics`
      GROUP BY fullvisitorid
    )

      SELECT
          CONCAT(fullvisitorid, '-',CAST(visitId AS STRING)) AS unique_session_id,

          # labels
          will_buy_on_return_visit,

          MAX(CAST(h.eCommerceAction.action_type AS INT64)) AS latest_ecommerce_progress,

          # behavior on the site
          IFNULL(totals.bounces, 0) AS bounces,
          IFNULL(totals.timeOnSite, 0) AS time_on_site,
          totals.pageviews,

          # where the visitor came from
          trafficSource.source,
          trafficSource.medium,
          channelGrouping,

          # mobile or desktop
          device.deviceCategory,

          # geographic
          IFNULL(geoNetwork.country, "") AS country

      FROM `data-to-insights.ecommerce.web_analytics`,
         UNNEST(hits) AS h

        JOIN all_visitor_stats USING(fullvisitorid)

      WHERE
        # only predict for new visits
        totals.newVisits = 1
        AND date BETWEEN '20170701' AND '20170801' # test 1 month

      GROUP BY
      unique_session_id,
      will_buy_on_return_visit,
      bounces,
      time_on_site,
      totals.pageviews,
      trafficSource.source,
      trafficSource.medium,
      channelGrouping,
      device.deviceCategory,
      country
    )

    )

    ORDER BY
      predicted_will_buy_on_return_visit DESC;

The predictions are made on the last 1 month (out of 12 months) of the dataset.

Click **Check my progress** to verify the objective.

<ql-activity-tracking step="6">Predict which new visitors will come back and purchase</ql-activity-tracking>

Your model now outputs its predictions for those July 2017 ecommerce sessions. You can see three newly added fields:

*   predicted_will_buy_on_return_visit: whether the model thinks the visitor will buy later (1 = yes)
*   predicted_will_buy_on_return_visit_probs.label: the binary classifier for yes / no
*   predicted_will_buy_on_return_visit.prob: the confidence the model has in it's prediction (1 = 100%)

![710c0c0b9abf827b.png](https://cdn.qwiklabs.com/fEttKDSQAqoD3sZWAo04MVkXGfM%2ByRDnOQST%2FaCRZwg%3D)

## Results

*   Of the top 6% of first-time visitors (sorted in decreasing order of predicted probability), more than 6% make a purchase in a later visit.

*   These users represent nearly 50% of all first-time visitors who make a purchase in a later visit.

*   Overall, only 0.7% of first-time visitors make a purchase in a later visit.

*   Targeting the top 6% of first-time increases marketing ROI by 9x vs targeting them all!

## Additional information

**Tip:** add `warm_start = true` to your model options if you are retraining new data on an existing model for faster training times. Note that you cannot change the feature columns (this would necessitate a new model).

**roc_auc** is just one of the performance metrics available during model evaluation. Also available are [accuracy, precision, and recall](https://en.wikipedia.org/wiki/Precision_and_recall). Knowing which performance metric to rely on is highly dependent on what your overall objective or goal is.

## Other datasets to explore

You can use this below link to bring in the **bigquery-public-data** project if you want to explore modeling on other datasets like forecasting fares for taxi trips:

*   [https://console.cloud.google.com/bigquery?p=bigquery-public-data&d=chicago_taxi_trips&t=taxi_trips&page=table](https://console.cloud.google.com/bigquery?p=bigquery-public-data&d=chicago_taxi_trips&t=taxi_trips&page=table)

## Test your knowledge

Test your knowledge about Google cloud Platform by taking our quiz.

<ql-true-false-probe answer="true" stem="With BigQuery you can query terabytes and terabytes of data without having any infrastructure to manage or needing a database administrator."></ql-true-false-probe>

## Congratulations!

You've successfully built an ML model in BigQuery to classify ecommerce visitors.

![data_engg_quest_icon.png](https://cdn.qwiklabs.com/XFebDQwepOTnzaxXtr01EzuRLa43wF9q3%2BaQtsE52GM%3D) ![BigQueryBasicsforMachineLearning-125x135.png](https://cdn.qwiklabs.com/ZUmKiX4CjNc4Qk7HtL6%2FVujHeG1GCP177Pgw9bYucM8%3D) ![bqdataalaysis.png](https://cdn.qwiklabs.com/jOzMLx8xazskc0UcLYJnZ4cDpE13GVQWi0FTbVtg8OE%3D)

### Finish your Quest

This self-paced lab is part of the Qwiklabs [Data Engineering](https://google.qwiklabs.com/quests/25) and [BigQuery for Machine Learning](https://google.qwiklabs.com/quests/71) Quests. A Quest is a series of related labs that form a learning path. Completing a Quest earns you badge to recognize your achievement. You can make your badge (or badges) public and link to them in your online resume or social media account. Enroll in either Quest and get immediate completion credit if you've taken the lab. [See other available Qwiklabs Quests](https://google.qwiklabs.com/catalog).

### Take Your Next Lab

Continue your Quest with [Predict Housing Prices with Tensorflow and AI Platform](https://google.qwiklabs.com/catalog_lab/1468), or check out these suggestions:

*   [Cloud Composer: Copy BigQuery Tables Across Different Locations](https://google.qwiklabs.com/catalog_lab/1418)

*   [Build an IoT Analytics Pipeline on Google Cloud](https://google.qwiklabs.com/catalog_lab/694)

### Next Steps / Learn More

*   Already have a Google Analytics account and want to query your own datasets in BigQuery? Follow this [export guide](https://support.google.com/analytics/answer/3416092).
*   The complete BigQuery SQL reference guide is here as an additional resource: [https://cloud.google.com/bigquery/docs/reference/standard-sql/query-syntax](https://cloud.google.com/bigquery/docs/reference/standard-sql/query-syntax)

### Google Cloud Training & Certification

...helps you make the most of Google Cloud technologies. [Our classes](https://cloud.google.com/training/courses) include technical skills and best practices to help you get up to speed quickly and continue your learning journey. We offer fundamental to advanced level training, with on-demand, live, and virtual options to suit your busy schedule. [Certifications](https://cloud.google.com/certification/) help you validate and prove your skill and expertise in Google Cloud technologies.

##### Manual Last Updated December 25, 2020

##### Lab Last Tested December 25, 2020

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

<div class="js-lab-content-outline lab-content__outline">[GSP229](#step1)[Overview](#step2)[Set up your environment](#step3)[Explore ecommerce data](#step4)[Identify an objective](#step5)[Select features and create your training dataset](#step6)[Create a BigQuery dataset to store models](#step7)[Select a BQML model type and specify options](#step8)[Evaluate classification model performance](#step9)[Improve model performance with Feature Engineering](#step10)[Predict which new visitors will come back and purchase](#step11)[Results](#step12)[Additional information](#step13)[Other datasets to explore](#step14)[Test your knowledge](#step15)[Congratulations!](#step16)</div>

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

In this lab you will use a newly available ecommerce dataset to run some typical queries that businesses would want to know about their customers’ purchasing habits.

This lab is included in these quests: [Data Engineering](/quests/25), [Engineer Data in Google Cloud](/quests/132), [BigQuery for Machine Learning](/quests/71), [Create ML Models with BigQuery ML](/quests/146), [DEPRICATED BigQuery for Data Analysis](/quests/55). If you complete this lab you'll receive credit for it when you enroll in one of these quests.

**Duration:** 0m setup · 75m access · 60m completion

<span>**Levels:** advanced</span>

**Permalink:** [https://google.qwiklabs.com/catalog_lab/1101](https://google.qwiklabs.com/catalog_lab/1101)

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

<div class="controls"><input class="hidden" type="hidden" value="1101" name="lab_review[lab_id]" id="lab_review_lab_id"></div>

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

<div class="lab-access-modal"><input type="hidden" name="id" id="id" value="1794"> <input type="hidden" name="user_id" id="user_id" value="4061609"> <input type="hidden" name="launch_with_credits" id="launch_with_credits" value="0" class="js-launch-with-credits-input"> <input type="hidden" name="launch_with_subs" id="launch_with_subs" value="0" class="js-launch-with-subscription-input">

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