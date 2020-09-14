---
title: "KAGGLE COMPETITION article shares on social media"
author: "Lisa Lang (1902224)"
date: 2020-03-28
tags: [binary classification, random forest, gradient boosting, neural network]
header:
  image: "/images/perceptron/percept.jpg"
excerpt: "Binary Classification, Random Forest, Gradient Boosting, Neural Network"
mathjax: "true"
---



## Description of the Kaggle competition

This is a private binary classification competition for the "Machine Learning" course, part of CEU Business Analytics Master program, spring semester of 2020.

In this competition, the task is to predict which articles are shared the most in social media. The data comes from website mashable.com from the beginning of 2015. The dataset used in the competition can be found at the UCI repository (https://archive.ics.uci.edu/ml/datasets/Online+News+Popularity#).

It is required to submit at least the following 4 types of solutions:

* linear model prediction after parameter tuning
* random forest prediction after parameter tuning
* gradient boosting prediction after parameter tuning
* neural network prediction after parameter tuning.

The best model will need to have at least 0.65 AUC to consider this part of the exam complete.

For extra points, build a stacked model, explain how it works and evaluate its results.

## Setup


```r
test_data <- read.csv("ceuba2020/test.csv/test.csv")
train_data <- read.csv("ceuba2020/train.csv/train.csv")
```

The *training data* table consists of 27752 observations and 60 variables. The *business question* is to predict the number of shares in social networks (popularity), so the *y-variable* in this case is `is_popular`. The *target observations* are the test data in the private leaderboard of the kaggle competition.

## Data exploration and cleaning

First I am going to get an overview of the attributes in this dataset.


```r
skim(train_data)
```


<table style='width: auto;'
        class='table table-condensed'>
<caption>Data summary</caption>
 <thead>
  <tr>
   <th style="text-align:left;">   </th>
   <th style="text-align:left;">   </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:left;"> Name </td>
   <td style="text-align:left;"> train_data </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Number of rows </td>
   <td style="text-align:left;"> 27752 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Number of columns </td>
   <td style="text-align:left;"> 60 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> _______________________ </td>
   <td style="text-align:left;">  </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Column type frequency: </td>
   <td style="text-align:left;">  </td>
  </tr>
  <tr>
   <td style="text-align:left;"> numeric </td>
   <td style="text-align:left;"> 60 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> ________________________ </td>
   <td style="text-align:left;">  </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Group variables </td>
   <td style="text-align:left;"> None </td>
  </tr>
</tbody>
</table>


**Variable type: numeric**

<table>
 <thead>
  <tr>
   <th style="text-align:left;"> skim_variable </th>
   <th style="text-align:right;"> n_missing </th>
   <th style="text-align:right;"> complete_rate </th>
   <th style="text-align:right;"> mean </th>
   <th style="text-align:right;"> sd </th>
   <th style="text-align:right;"> p0 </th>
   <th style="text-align:right;"> p25 </th>
   <th style="text-align:right;"> p50 </th>
   <th style="text-align:right;"> p75 </th>
   <th style="text-align:right;"> p100 </th>
   <th style="text-align:left;"> hist </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:left;"> n_tokens_title </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 1 </td>
   <td style="text-align:right;"> 10.38 </td>
   <td style="text-align:right;"> 2.11 </td>
   <td style="text-align:right;"> 3.00 </td>
   <td style="text-align:right;"> 9.00 </td>
   <td style="text-align:right;"> 10.00 </td>
   <td style="text-align:right;"> 12.00 </td>
   <td style="text-align:right;"> 20.00 </td>
   <td style="text-align:left;"> ▁▅▇▁▁ </td>
  </tr>
  <tr>
   <td style="text-align:left;"> n_tokens_content </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 1 </td>
   <td style="text-align:right;"> 546.56 </td>
   <td style="text-align:right;"> 471.66 </td>
   <td style="text-align:right;"> 0.00 </td>
   <td style="text-align:right;"> 246.00 </td>
   <td style="text-align:right;"> 409.00 </td>
   <td style="text-align:right;"> 717.00 </td>
   <td style="text-align:right;"> 7764.00 </td>
   <td style="text-align:left;"> ▇▁▁▁▁ </td>
  </tr>
  <tr>
   <td style="text-align:left;"> n_unique_tokens </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 1 </td>
   <td style="text-align:right;"> 0.56 </td>
   <td style="text-align:right;"> 4.21 </td>
   <td style="text-align:right;"> 0.00 </td>
   <td style="text-align:right;"> 0.47 </td>
   <td style="text-align:right;"> 0.54 </td>
   <td style="text-align:right;"> 0.61 </td>
   <td style="text-align:right;"> 701.00 </td>
   <td style="text-align:left;"> ▇▁▁▁▁ </td>
  </tr>
  <tr>
   <td style="text-align:left;"> n_non_stop_words </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 1 </td>
   <td style="text-align:right;"> 1.01 </td>
   <td style="text-align:right;"> 6.25 </td>
   <td style="text-align:right;"> 0.00 </td>
   <td style="text-align:right;"> 1.00 </td>
   <td style="text-align:right;"> 1.00 </td>
   <td style="text-align:right;"> 1.00 </td>
   <td style="text-align:right;"> 1042.00 </td>
   <td style="text-align:left;"> ▇▁▁▁▁ </td>
  </tr>
  <tr>
   <td style="text-align:left;"> n_non_stop_unique_tokens </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 1 </td>
   <td style="text-align:right;"> 0.70 </td>
   <td style="text-align:right;"> 3.90 </td>
   <td style="text-align:right;"> 0.00 </td>
   <td style="text-align:right;"> 0.63 </td>
   <td style="text-align:right;"> 0.69 </td>
   <td style="text-align:right;"> 0.75 </td>
   <td style="text-align:right;"> 650.00 </td>
   <td style="text-align:left;"> ▇▁▁▁▁ </td>
  </tr>
  <tr>
   <td style="text-align:left;"> num_hrefs </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 1 </td>
   <td style="text-align:right;"> 10.92 </td>
   <td style="text-align:right;"> 11.40 </td>
   <td style="text-align:right;"> 0.00 </td>
   <td style="text-align:right;"> 4.00 </td>
   <td style="text-align:right;"> 8.00 </td>
   <td style="text-align:right;"> 14.00 </td>
   <td style="text-align:right;"> 304.00 </td>
   <td style="text-align:left;"> ▇▁▁▁▁ </td>
  </tr>
  <tr>
   <td style="text-align:left;"> num_self_hrefs </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 1 </td>
   <td style="text-align:right;"> 3.30 </td>
   <td style="text-align:right;"> 3.85 </td>
   <td style="text-align:right;"> 0.00 </td>
   <td style="text-align:right;"> 1.00 </td>
   <td style="text-align:right;"> 3.00 </td>
   <td style="text-align:right;"> 4.00 </td>
   <td style="text-align:right;"> 116.00 </td>
   <td style="text-align:left;"> ▇▁▁▁▁ </td>
  </tr>
  <tr>
   <td style="text-align:left;"> num_imgs </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 1 </td>
   <td style="text-align:right;"> 4.61 </td>
   <td style="text-align:right;"> 8.36 </td>
   <td style="text-align:right;"> 0.00 </td>
   <td style="text-align:right;"> 1.00 </td>
   <td style="text-align:right;"> 1.00 </td>
   <td style="text-align:right;"> 4.00 </td>
   <td style="text-align:right;"> 111.00 </td>
   <td style="text-align:left;"> ▇▁▁▁▁ </td>
  </tr>
  <tr>
   <td style="text-align:left;"> num_videos </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 1 </td>
   <td style="text-align:right;"> 1.23 </td>
   <td style="text-align:right;"> 4.07 </td>
   <td style="text-align:right;"> 0.00 </td>
   <td style="text-align:right;"> 0.00 </td>
   <td style="text-align:right;"> 0.00 </td>
   <td style="text-align:right;"> 1.00 </td>
   <td style="text-align:right;"> 91.00 </td>
   <td style="text-align:left;"> ▇▁▁▁▁ </td>
  </tr>
  <tr>
   <td style="text-align:left;"> average_token_length </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 1 </td>
   <td style="text-align:right;"> 4.55 </td>
   <td style="text-align:right;"> 0.84 </td>
   <td style="text-align:right;"> 0.00 </td>
   <td style="text-align:right;"> 4.48 </td>
   <td style="text-align:right;"> 4.66 </td>
   <td style="text-align:right;"> 4.86 </td>
   <td style="text-align:right;"> 8.04 </td>
   <td style="text-align:left;"> ▁▁▇▃▁ </td>
  </tr>
  <tr>
   <td style="text-align:left;"> num_keywords </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 1 </td>
   <td style="text-align:right;"> 7.23 </td>
   <td style="text-align:right;"> 1.91 </td>
   <td style="text-align:right;"> 1.00 </td>
   <td style="text-align:right;"> 6.00 </td>
   <td style="text-align:right;"> 7.00 </td>
   <td style="text-align:right;"> 9.00 </td>
   <td style="text-align:right;"> 10.00 </td>
   <td style="text-align:left;"> ▁▂▇▇▇ </td>
  </tr>
  <tr>
   <td style="text-align:left;"> data_channel_is_lifestyle </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 1 </td>
   <td style="text-align:right;"> 0.05 </td>
   <td style="text-align:right;"> 0.23 </td>
   <td style="text-align:right;"> 0.00 </td>
   <td style="text-align:right;"> 0.00 </td>
   <td style="text-align:right;"> 0.00 </td>
   <td style="text-align:right;"> 0.00 </td>
   <td style="text-align:right;"> 1.00 </td>
   <td style="text-align:left;"> ▇▁▁▁▁ </td>
  </tr>
  <tr>
   <td style="text-align:left;"> data_channel_is_entertainment </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 1 </td>
   <td style="text-align:right;"> 0.18 </td>
   <td style="text-align:right;"> 0.38 </td>
   <td style="text-align:right;"> 0.00 </td>
   <td style="text-align:right;"> 0.00 </td>
   <td style="text-align:right;"> 0.00 </td>
   <td style="text-align:right;"> 0.00 </td>
   <td style="text-align:right;"> 1.00 </td>
   <td style="text-align:left;"> ▇▁▁▁▂ </td>
  </tr>
  <tr>
   <td style="text-align:left;"> data_channel_is_bus </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 1 </td>
   <td style="text-align:right;"> 0.16 </td>
   <td style="text-align:right;"> 0.36 </td>
   <td style="text-align:right;"> 0.00 </td>
   <td style="text-align:right;"> 0.00 </td>
   <td style="text-align:right;"> 0.00 </td>
   <td style="text-align:right;"> 0.00 </td>
   <td style="text-align:right;"> 1.00 </td>
   <td style="text-align:left;"> ▇▁▁▁▂ </td>
  </tr>
  <tr>
   <td style="text-align:left;"> data_channel_is_socmed </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 1 </td>
   <td style="text-align:right;"> 0.06 </td>
   <td style="text-align:right;"> 0.23 </td>
   <td style="text-align:right;"> 0.00 </td>
   <td style="text-align:right;"> 0.00 </td>
   <td style="text-align:right;"> 0.00 </td>
   <td style="text-align:right;"> 0.00 </td>
   <td style="text-align:right;"> 1.00 </td>
   <td style="text-align:left;"> ▇▁▁▁▁ </td>
  </tr>
  <tr>
   <td style="text-align:left;"> data_channel_is_tech </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 1 </td>
   <td style="text-align:right;"> 0.19 </td>
   <td style="text-align:right;"> 0.39 </td>
   <td style="text-align:right;"> 0.00 </td>
   <td style="text-align:right;"> 0.00 </td>
   <td style="text-align:right;"> 0.00 </td>
   <td style="text-align:right;"> 0.00 </td>
   <td style="text-align:right;"> 1.00 </td>
   <td style="text-align:left;"> ▇▁▁▁▂ </td>
  </tr>
  <tr>
   <td style="text-align:left;"> data_channel_is_world </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 1 </td>
   <td style="text-align:right;"> 0.21 </td>
   <td style="text-align:right;"> 0.41 </td>
   <td style="text-align:right;"> 0.00 </td>
   <td style="text-align:right;"> 0.00 </td>
   <td style="text-align:right;"> 0.00 </td>
   <td style="text-align:right;"> 0.00 </td>
   <td style="text-align:right;"> 1.00 </td>
   <td style="text-align:left;"> ▇▁▁▁▂ </td>
  </tr>
  <tr>
   <td style="text-align:left;"> kw_min_min </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 1 </td>
   <td style="text-align:right;"> 26.26 </td>
   <td style="text-align:right;"> 69.82 </td>
   <td style="text-align:right;"> -1.00 </td>
   <td style="text-align:right;"> -1.00 </td>
   <td style="text-align:right;"> -1.00 </td>
   <td style="text-align:right;"> 4.00 </td>
   <td style="text-align:right;"> 377.00 </td>
   <td style="text-align:left;"> ▇▁▁▁▁ </td>
  </tr>
  <tr>
   <td style="text-align:left;"> kw_max_min </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 1 </td>
   <td style="text-align:right;"> 1165.99 </td>
   <td style="text-align:right;"> 4147.64 </td>
   <td style="text-align:right;"> 0.00 </td>
   <td style="text-align:right;"> 448.00 </td>
   <td style="text-align:right;"> 662.00 </td>
   <td style="text-align:right;"> 1000.00 </td>
   <td style="text-align:right;"> 298400.00 </td>
   <td style="text-align:left;"> ▇▁▁▁▁ </td>
  </tr>
  <tr>
   <td style="text-align:left;"> kw_avg_min </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 1 </td>
   <td style="text-align:right;"> 314.70 </td>
   <td style="text-align:right;"> 670.78 </td>
   <td style="text-align:right;"> -1.00 </td>
   <td style="text-align:right;"> 142.50 </td>
   <td style="text-align:right;"> 235.94 </td>
   <td style="text-align:right;"> 358.22 </td>
   <td style="text-align:right;"> 42827.86 </td>
   <td style="text-align:left;"> ▇▁▁▁▁ </td>
  </tr>
  <tr>
   <td style="text-align:left;"> kw_min_max </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 1 </td>
   <td style="text-align:right;"> 13529.57 </td>
   <td style="text-align:right;"> 57297.42 </td>
   <td style="text-align:right;"> 0.00 </td>
   <td style="text-align:right;"> 0.00 </td>
   <td style="text-align:right;"> 1400.00 </td>
   <td style="text-align:right;"> 7900.00 </td>
   <td style="text-align:right;"> 843300.00 </td>
   <td style="text-align:left;"> ▇▁▁▁▁ </td>
  </tr>
  <tr>
   <td style="text-align:left;"> kw_max_max </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 1 </td>
   <td style="text-align:right;"> 751996.26 </td>
   <td style="text-align:right;"> 214875.42 </td>
   <td style="text-align:right;"> 0.00 </td>
   <td style="text-align:right;"> 843300.00 </td>
   <td style="text-align:right;"> 843300.00 </td>
   <td style="text-align:right;"> 843300.00 </td>
   <td style="text-align:right;"> 843300.00 </td>
   <td style="text-align:left;"> ▁▁▁▁▇ </td>
  </tr>
  <tr>
   <td style="text-align:left;"> kw_avg_max </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 1 </td>
   <td style="text-align:right;"> 258825.28 </td>
   <td style="text-align:right;"> 134555.01 </td>
   <td style="text-align:right;"> 0.00 </td>
   <td style="text-align:right;"> 172477.50 </td>
   <td style="text-align:right;"> 244297.86 </td>
   <td style="text-align:right;"> 330700.00 </td>
   <td style="text-align:right;"> 843300.00 </td>
   <td style="text-align:left;"> ▃▇▃▁▁ </td>
  </tr>
  <tr>
   <td style="text-align:left;"> kw_min_avg </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 1 </td>
   <td style="text-align:right;"> 1118.68 </td>
   <td style="text-align:right;"> 1138.40 </td>
   <td style="text-align:right;"> -1.00 </td>
   <td style="text-align:right;"> 0.00 </td>
   <td style="text-align:right;"> 1024.77 </td>
   <td style="text-align:right;"> 2060.03 </td>
   <td style="text-align:right;"> 3613.04 </td>
   <td style="text-align:left;"> ▇▃▃▂▂ </td>
  </tr>
  <tr>
   <td style="text-align:left;"> kw_max_avg </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 1 </td>
   <td style="text-align:right;"> 5676.57 </td>
   <td style="text-align:right;"> 6346.89 </td>
   <td style="text-align:right;"> 0.00 </td>
   <td style="text-align:right;"> 3566.30 </td>
   <td style="text-align:right;"> 4359.61 </td>
   <td style="text-align:right;"> 6018.30 </td>
   <td style="text-align:right;"> 298400.00 </td>
   <td style="text-align:left;"> ▇▁▁▁▁ </td>
  </tr>
  <tr>
   <td style="text-align:left;"> kw_avg_avg </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 1 </td>
   <td style="text-align:right;"> 3140.74 </td>
   <td style="text-align:right;"> 1343.01 </td>
   <td style="text-align:right;"> 0.00 </td>
   <td style="text-align:right;"> 2387.63 </td>
   <td style="text-align:right;"> 2870.55 </td>
   <td style="text-align:right;"> 3608.71 </td>
   <td style="text-align:right;"> 43567.66 </td>
   <td style="text-align:left;"> ▇▁▁▁▁ </td>
  </tr>
  <tr>
   <td style="text-align:left;"> self_reference_min_shares </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 1 </td>
   <td style="text-align:right;"> 4123.98 </td>
   <td style="text-align:right;"> 20926.43 </td>
   <td style="text-align:right;"> 0.00 </td>
   <td style="text-align:right;"> 638.00 </td>
   <td style="text-align:right;"> 1200.00 </td>
   <td style="text-align:right;"> 2700.00 </td>
   <td style="text-align:right;"> 843300.00 </td>
   <td style="text-align:left;"> ▇▁▁▁▁ </td>
  </tr>
  <tr>
   <td style="text-align:left;"> self_reference_max_shares </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 1 </td>
   <td style="text-align:right;"> 10401.31 </td>
   <td style="text-align:right;"> 41383.32 </td>
   <td style="text-align:right;"> 0.00 </td>
   <td style="text-align:right;"> 1100.00 </td>
   <td style="text-align:right;"> 2800.00 </td>
   <td style="text-align:right;"> 7900.00 </td>
   <td style="text-align:right;"> 843300.00 </td>
   <td style="text-align:left;"> ▇▁▁▁▁ </td>
  </tr>
  <tr>
   <td style="text-align:left;"> self_reference_avg_sharess </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 1 </td>
   <td style="text-align:right;"> 6479.49 </td>
   <td style="text-align:right;"> 24979.25 </td>
   <td style="text-align:right;"> 0.00 </td>
   <td style="text-align:right;"> 978.79 </td>
   <td style="text-align:right;"> 2200.00 </td>
   <td style="text-align:right;"> 5199.40 </td>
   <td style="text-align:right;"> 843300.00 </td>
   <td style="text-align:left;"> ▇▁▁▁▁ </td>
  </tr>
  <tr>
   <td style="text-align:left;"> weekday_is_monday </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 1 </td>
   <td style="text-align:right;"> 0.17 </td>
   <td style="text-align:right;"> 0.37 </td>
   <td style="text-align:right;"> 0.00 </td>
   <td style="text-align:right;"> 0.00 </td>
   <td style="text-align:right;"> 0.00 </td>
   <td style="text-align:right;"> 0.00 </td>
   <td style="text-align:right;"> 1.00 </td>
   <td style="text-align:left;"> ▇▁▁▁▂ </td>
  </tr>
  <tr>
   <td style="text-align:left;"> weekday_is_tuesday </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 1 </td>
   <td style="text-align:right;"> 0.19 </td>
   <td style="text-align:right;"> 0.39 </td>
   <td style="text-align:right;"> 0.00 </td>
   <td style="text-align:right;"> 0.00 </td>
   <td style="text-align:right;"> 0.00 </td>
   <td style="text-align:right;"> 0.00 </td>
   <td style="text-align:right;"> 1.00 </td>
   <td style="text-align:left;"> ▇▁▁▁▂ </td>
  </tr>
  <tr>
   <td style="text-align:left;"> weekday_is_wednesday </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 1 </td>
   <td style="text-align:right;"> 0.19 </td>
   <td style="text-align:right;"> 0.39 </td>
   <td style="text-align:right;"> 0.00 </td>
   <td style="text-align:right;"> 0.00 </td>
   <td style="text-align:right;"> 0.00 </td>
   <td style="text-align:right;"> 0.00 </td>
   <td style="text-align:right;"> 1.00 </td>
   <td style="text-align:left;"> ▇▁▁▁▂ </td>
  </tr>
  <tr>
   <td style="text-align:left;"> weekday_is_thursday </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 1 </td>
   <td style="text-align:right;"> 0.18 </td>
   <td style="text-align:right;"> 0.39 </td>
   <td style="text-align:right;"> 0.00 </td>
   <td style="text-align:right;"> 0.00 </td>
   <td style="text-align:right;"> 0.00 </td>
   <td style="text-align:right;"> 0.00 </td>
   <td style="text-align:right;"> 1.00 </td>
   <td style="text-align:left;"> ▇▁▁▁▂ </td>
  </tr>
  <tr>
   <td style="text-align:left;"> weekday_is_friday </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 1 </td>
   <td style="text-align:right;"> 0.14 </td>
   <td style="text-align:right;"> 0.35 </td>
   <td style="text-align:right;"> 0.00 </td>
   <td style="text-align:right;"> 0.00 </td>
   <td style="text-align:right;"> 0.00 </td>
   <td style="text-align:right;"> 0.00 </td>
   <td style="text-align:right;"> 1.00 </td>
   <td style="text-align:left;"> ▇▁▁▁▁ </td>
  </tr>
  <tr>
   <td style="text-align:left;"> weekday_is_saturday </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 1 </td>
   <td style="text-align:right;"> 0.06 </td>
   <td style="text-align:right;"> 0.24 </td>
   <td style="text-align:right;"> 0.00 </td>
   <td style="text-align:right;"> 0.00 </td>
   <td style="text-align:right;"> 0.00 </td>
   <td style="text-align:right;"> 0.00 </td>
   <td style="text-align:right;"> 1.00 </td>
   <td style="text-align:left;"> ▇▁▁▁▁ </td>
  </tr>
  <tr>
   <td style="text-align:left;"> weekday_is_sunday </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 1 </td>
   <td style="text-align:right;"> 0.07 </td>
   <td style="text-align:right;"> 0.25 </td>
   <td style="text-align:right;"> 0.00 </td>
   <td style="text-align:right;"> 0.00 </td>
   <td style="text-align:right;"> 0.00 </td>
   <td style="text-align:right;"> 0.00 </td>
   <td style="text-align:right;"> 1.00 </td>
   <td style="text-align:left;"> ▇▁▁▁▁ </td>
  </tr>
  <tr>
   <td style="text-align:left;"> is_weekend </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 1 </td>
   <td style="text-align:right;"> 0.13 </td>
   <td style="text-align:right;"> 0.34 </td>
   <td style="text-align:right;"> 0.00 </td>
   <td style="text-align:right;"> 0.00 </td>
   <td style="text-align:right;"> 0.00 </td>
   <td style="text-align:right;"> 0.00 </td>
   <td style="text-align:right;"> 1.00 </td>
   <td style="text-align:left;"> ▇▁▁▁▁ </td>
  </tr>
  <tr>
   <td style="text-align:left;"> LDA_00 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 1 </td>
   <td style="text-align:right;"> 0.18 </td>
   <td style="text-align:right;"> 0.26 </td>
   <td style="text-align:right;"> 0.00 </td>
   <td style="text-align:right;"> 0.03 </td>
   <td style="text-align:right;"> 0.03 </td>
   <td style="text-align:right;"> 0.24 </td>
   <td style="text-align:right;"> 0.92 </td>
   <td style="text-align:left;"> ▇▁▁▁▁ </td>
  </tr>
  <tr>
   <td style="text-align:left;"> LDA_01 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 1 </td>
   <td style="text-align:right;"> 0.14 </td>
   <td style="text-align:right;"> 0.22 </td>
   <td style="text-align:right;"> 0.00 </td>
   <td style="text-align:right;"> 0.03 </td>
   <td style="text-align:right;"> 0.03 </td>
   <td style="text-align:right;"> 0.15 </td>
   <td style="text-align:right;"> 0.93 </td>
   <td style="text-align:left;"> ▇▁▁▁▁ </td>
  </tr>
  <tr>
   <td style="text-align:left;"> LDA_02 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 1 </td>
   <td style="text-align:right;"> 0.21 </td>
   <td style="text-align:right;"> 0.28 </td>
   <td style="text-align:right;"> 0.00 </td>
   <td style="text-align:right;"> 0.03 </td>
   <td style="text-align:right;"> 0.04 </td>
   <td style="text-align:right;"> 0.33 </td>
   <td style="text-align:right;"> 0.92 </td>
   <td style="text-align:left;"> ▇▁▁▁▁ </td>
  </tr>
  <tr>
   <td style="text-align:left;"> LDA_03 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 1 </td>
   <td style="text-align:right;"> 0.22 </td>
   <td style="text-align:right;"> 0.30 </td>
   <td style="text-align:right;"> 0.00 </td>
   <td style="text-align:right;"> 0.03 </td>
   <td style="text-align:right;"> 0.04 </td>
   <td style="text-align:right;"> 0.38 </td>
   <td style="text-align:right;"> 0.93 </td>
   <td style="text-align:left;"> ▇▁▁▁▂ </td>
  </tr>
  <tr>
   <td style="text-align:left;"> LDA_04 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 1 </td>
   <td style="text-align:right;"> 0.24 </td>
   <td style="text-align:right;"> 0.29 </td>
   <td style="text-align:right;"> 0.00 </td>
   <td style="text-align:right;"> 0.03 </td>
   <td style="text-align:right;"> 0.04 </td>
   <td style="text-align:right;"> 0.40 </td>
   <td style="text-align:right;"> 0.93 </td>
   <td style="text-align:left;"> ▇▂▁▁▂ </td>
  </tr>
  <tr>
   <td style="text-align:left;"> global_subjectivity </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 1 </td>
   <td style="text-align:right;"> 0.44 </td>
   <td style="text-align:right;"> 0.12 </td>
   <td style="text-align:right;"> 0.00 </td>
   <td style="text-align:right;"> 0.40 </td>
   <td style="text-align:right;"> 0.45 </td>
   <td style="text-align:right;"> 0.51 </td>
   <td style="text-align:right;"> 1.00 </td>
   <td style="text-align:left;"> ▁▃▇▁▁ </td>
  </tr>
  <tr>
   <td style="text-align:left;"> global_sentiment_polarity </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 1 </td>
   <td style="text-align:right;"> 0.12 </td>
   <td style="text-align:right;"> 0.10 </td>
   <td style="text-align:right;"> -0.39 </td>
   <td style="text-align:right;"> 0.06 </td>
   <td style="text-align:right;"> 0.12 </td>
   <td style="text-align:right;"> 0.18 </td>
   <td style="text-align:right;"> 0.73 </td>
   <td style="text-align:left;"> ▁▂▇▁▁ </td>
  </tr>
  <tr>
   <td style="text-align:left;"> global_rate_positive_words </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 1 </td>
   <td style="text-align:right;"> 0.04 </td>
   <td style="text-align:right;"> 0.02 </td>
   <td style="text-align:right;"> 0.00 </td>
   <td style="text-align:right;"> 0.03 </td>
   <td style="text-align:right;"> 0.04 </td>
   <td style="text-align:right;"> 0.05 </td>
   <td style="text-align:right;"> 0.16 </td>
   <td style="text-align:left;"> ▅▇▁▁▁ </td>
  </tr>
  <tr>
   <td style="text-align:left;"> global_rate_negative_words </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 1 </td>
   <td style="text-align:right;"> 0.02 </td>
   <td style="text-align:right;"> 0.01 </td>
   <td style="text-align:right;"> 0.00 </td>
   <td style="text-align:right;"> 0.01 </td>
   <td style="text-align:right;"> 0.02 </td>
   <td style="text-align:right;"> 0.02 </td>
   <td style="text-align:right;"> 0.18 </td>
   <td style="text-align:left;"> ▇▁▁▁▁ </td>
  </tr>
  <tr>
   <td style="text-align:left;"> rate_positive_words </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 1 </td>
   <td style="text-align:right;"> 0.68 </td>
   <td style="text-align:right;"> 0.19 </td>
   <td style="text-align:right;"> 0.00 </td>
   <td style="text-align:right;"> 0.60 </td>
   <td style="text-align:right;"> 0.71 </td>
   <td style="text-align:right;"> 0.80 </td>
   <td style="text-align:right;"> 1.00 </td>
   <td style="text-align:left;"> ▁▁▃▇▃ </td>
  </tr>
  <tr>
   <td style="text-align:left;"> rate_negative_words </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 1 </td>
   <td style="text-align:right;"> 0.29 </td>
   <td style="text-align:right;"> 0.16 </td>
   <td style="text-align:right;"> 0.00 </td>
   <td style="text-align:right;"> 0.19 </td>
   <td style="text-align:right;"> 0.28 </td>
   <td style="text-align:right;"> 0.38 </td>
   <td style="text-align:right;"> 1.00 </td>
   <td style="text-align:left;"> ▅▇▃▁▁ </td>
  </tr>
  <tr>
   <td style="text-align:left;"> avg_positive_polarity </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 1 </td>
   <td style="text-align:right;"> 0.35 </td>
   <td style="text-align:right;"> 0.10 </td>
   <td style="text-align:right;"> 0.00 </td>
   <td style="text-align:right;"> 0.31 </td>
   <td style="text-align:right;"> 0.36 </td>
   <td style="text-align:right;"> 0.41 </td>
   <td style="text-align:right;"> 1.00 </td>
   <td style="text-align:left;"> ▁▇▃▁▁ </td>
  </tr>
  <tr>
   <td style="text-align:left;"> min_positive_polarity </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 1 </td>
   <td style="text-align:right;"> 0.10 </td>
   <td style="text-align:right;"> 0.07 </td>
   <td style="text-align:right;"> 0.00 </td>
   <td style="text-align:right;"> 0.05 </td>
   <td style="text-align:right;"> 0.10 </td>
   <td style="text-align:right;"> 0.10 </td>
   <td style="text-align:right;"> 1.00 </td>
   <td style="text-align:left;"> ▇▁▁▁▁ </td>
  </tr>
  <tr>
   <td style="text-align:left;"> max_positive_polarity </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 1 </td>
   <td style="text-align:right;"> 0.76 </td>
   <td style="text-align:right;"> 0.25 </td>
   <td style="text-align:right;"> 0.00 </td>
   <td style="text-align:right;"> 0.60 </td>
   <td style="text-align:right;"> 0.80 </td>
   <td style="text-align:right;"> 1.00 </td>
   <td style="text-align:right;"> 1.00 </td>
   <td style="text-align:left;"> ▁▁▅▅▇ </td>
  </tr>
  <tr>
   <td style="text-align:left;"> avg_negative_polarity </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 1 </td>
   <td style="text-align:right;"> -0.26 </td>
   <td style="text-align:right;"> 0.13 </td>
   <td style="text-align:right;"> -1.00 </td>
   <td style="text-align:right;"> -0.33 </td>
   <td style="text-align:right;"> -0.25 </td>
   <td style="text-align:right;"> -0.19 </td>
   <td style="text-align:right;"> 0.00 </td>
   <td style="text-align:left;"> ▁▁▂▇▃ </td>
  </tr>
  <tr>
   <td style="text-align:left;"> min_negative_polarity </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 1 </td>
   <td style="text-align:right;"> -0.52 </td>
   <td style="text-align:right;"> 0.29 </td>
   <td style="text-align:right;"> -1.00 </td>
   <td style="text-align:right;"> -0.70 </td>
   <td style="text-align:right;"> -0.50 </td>
   <td style="text-align:right;"> -0.30 </td>
   <td style="text-align:right;"> 0.00 </td>
   <td style="text-align:left;"> ▆▆▇▅▅ </td>
  </tr>
  <tr>
   <td style="text-align:left;"> max_negative_polarity </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 1 </td>
   <td style="text-align:right;"> -0.11 </td>
   <td style="text-align:right;"> 0.10 </td>
   <td style="text-align:right;"> -1.00 </td>
   <td style="text-align:right;"> -0.12 </td>
   <td style="text-align:right;"> -0.10 </td>
   <td style="text-align:right;"> -0.05 </td>
   <td style="text-align:right;"> 0.00 </td>
   <td style="text-align:left;"> ▁▁▁▁▇ </td>
  </tr>
  <tr>
   <td style="text-align:left;"> title_subjectivity </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 1 </td>
   <td style="text-align:right;"> 0.28 </td>
   <td style="text-align:right;"> 0.32 </td>
   <td style="text-align:right;"> 0.00 </td>
   <td style="text-align:right;"> 0.00 </td>
   <td style="text-align:right;"> 0.15 </td>
   <td style="text-align:right;"> 0.50 </td>
   <td style="text-align:right;"> 1.00 </td>
   <td style="text-align:left;"> ▇▂▂▁▂ </td>
  </tr>
  <tr>
   <td style="text-align:left;"> title_sentiment_polarity </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 1 </td>
   <td style="text-align:right;"> 0.07 </td>
   <td style="text-align:right;"> 0.27 </td>
   <td style="text-align:right;"> -1.00 </td>
   <td style="text-align:right;"> 0.00 </td>
   <td style="text-align:right;"> 0.00 </td>
   <td style="text-align:right;"> 0.15 </td>
   <td style="text-align:right;"> 1.00 </td>
   <td style="text-align:left;"> ▁▁▇▂▁ </td>
  </tr>
  <tr>
   <td style="text-align:left;"> abs_title_subjectivity </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 1 </td>
   <td style="text-align:right;"> 0.34 </td>
   <td style="text-align:right;"> 0.19 </td>
   <td style="text-align:right;"> 0.00 </td>
   <td style="text-align:right;"> 0.17 </td>
   <td style="text-align:right;"> 0.50 </td>
   <td style="text-align:right;"> 0.50 </td>
   <td style="text-align:right;"> 0.50 </td>
   <td style="text-align:left;"> ▃▂▁▁▇ </td>
  </tr>
  <tr>
   <td style="text-align:left;"> abs_title_sentiment_polarity </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 1 </td>
   <td style="text-align:right;"> 0.16 </td>
   <td style="text-align:right;"> 0.23 </td>
   <td style="text-align:right;"> 0.00 </td>
   <td style="text-align:right;"> 0.00 </td>
   <td style="text-align:right;"> 0.00 </td>
   <td style="text-align:right;"> 0.25 </td>
   <td style="text-align:right;"> 1.00 </td>
   <td style="text-align:left;"> ▇▂▁▁▁ </td>
  </tr>
  <tr>
   <td style="text-align:left;"> is_popular </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 1 </td>
   <td style="text-align:right;"> 0.20 </td>
   <td style="text-align:right;"> 0.40 </td>
   <td style="text-align:right;"> 0.00 </td>
   <td style="text-align:right;"> 0.00 </td>
   <td style="text-align:right;"> 0.00 </td>
   <td style="text-align:right;"> 0.00 </td>
   <td style="text-align:right;"> 1.00 </td>
   <td style="text-align:left;"> ▇▁▁▁▂ </td>
  </tr>
  <tr>
   <td style="text-align:left;"> article_id </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 1 </td>
   <td style="text-align:right;"> 19794.97 </td>
   <td style="text-align:right;"> 11441.80 </td>
   <td style="text-align:right;"> 2.00 </td>
   <td style="text-align:right;"> 9883.50 </td>
   <td style="text-align:right;"> 19765.50 </td>
   <td style="text-align:right;"> 29699.50 </td>
   <td style="text-align:right;"> 39644.00 </td>
   <td style="text-align:left;"> ▇▇▇▇▇ </td>
  </tr>
</tbody>
</table>



*Attribute description:*  
The features fall into various categories such as quantitative information about the article — such things as number of images, number of videos, etc. — and qualitative information about the article — such as which day it was published and which topic the article falls under.

*Missing values:*  
There are no missing values in any of the observations.

*Factorization:*  
For binary prediction with caret, the target variable must be a factor so I am going to coerce it to factors.
I previously tried to factorize also the independent variables, but ran into problems compiling the h2o stacked ensemble model, so I will keep them as integer values.


```r
# turn character variables to factors
# colnames <- names(train_data)[grep("_is_", names(train_data))]
# 
# train_data[colnames] <- lapply(train_data[colnames], as.factor)
# test_data[colnames] <- lapply(test_data[colnames], as.factor)

train_data <- data.table(train_data)
train_data[, is_popular_f := factor(is_popular, levels = c(0,1), 
                                    labels = c("no", "yes"))]
```

Now I look at the outcome variable `is_popular`: 


```r
ggplot(data = train_data, aes(x = is_popular)) + geom_bar()
```

![](CopyOfKaggle_1902224_LisaLang_files/figure-html/plot_y-1.png)<!-- -->

The majority of articles was labeled unpopular.

In addition I created a data profiling report with the 'DataExplorer' package, in order to have a better understanding of the dataset.


```r
library(DataExplorer)
create_report(train_data, y = "is_popular")
```

From the exploratory data analysis functions that were run in this report I learned that 

* most articles were published during the week and not on the weekend (mainly Tue, Wed, Fri).
* the most popular channel is "world", followed by "tech" and "entertainment".
* the number of keywords is mostly between 5 and 10.
* most articles use zero or very few images and videos.

The report is available as a separate html document.

## Model Training

I am going to train five models, and compare their performance using AUC. The AUC statistic is the most commonly used measure for diagnostic accuracy of quantitative tests. It is a discrimination measure which tells us how well we can classify observations in two groups: those with and those without the outcome of interest. 


```r
# separate datasets
set.seed(482)
train_indices <- createDataPartition(train_data$is_popular, 
                                     p = 0.85, 
                                     list = FALSE)
temp_train <- train_data[train_indices, ]
temp_holdout <- train_data[-train_indices, ]

# initialize list
holdout_AUC <- list()

# variables
variables <- paste(" ~", paste(names(train_data[,-c(59,61)]), collapse = " + "))
```


### Linear Model

First I am training a Logit LASSO model as my benchmark model, a type of linear regression that uses shrinkage to reduce model complexity.

I am going to use different lambdas, which defines how much weight we want to add to penalization (the larger lambda the higher the penalty). Cross validation is used to check the best lambda.

The data is pre-processed before training, which includes centering and scaling the data.
I am going to set `metric` to "ROC" to choose models based on AUC.
                          

```r
# tuning parameters
train_control <- trainControl(method = "cv", 
                         number = 5,
                         classProbs = TRUE,
                         summaryFunction = twoClassSummary)

lasso_tg <- expand.grid("alpha" = 1, 
                        "lambda" = 10^seq(-1, -4, length = 10))

set.seed(1102)
logit_lasso_model <- train(
    formula(paste0("is_popular_f", variables)),
    data = temp_train,
    method = "glmnet",
    preProcess = c("center", "scale"),
    family = "binomial",
    trControl = train_control,
    tuneGrid = lasso_tg,
    metric = "ROC"
  )

# print(logit_lasso_model)
```


```r
tuned_logit_lasso_model <- logit_lasso_model$finalModel
best_lambda <- logit_lasso_model$bestTune$lambda
lasso_coeffs <- as.matrix(coef(tuned_logit_lasso_model, best_lambda))
```

The LASSO algorithm picked a regression with 49 predictor variables/interactions and `lambda` = 0.0004641589. 
I will use the trained model to predict the probability of an article being shared or not on the holdout dataset.
The default threshold for prediction is 50%. 
If we were to increase the threshold for predicting something to be positive, we will have fewer cases that we label as positive and therefor decrease the false positives and true positives. 



```r
# Probability prediction
holdout_prediction_probs <- predict.train(logit_lasso_model,
                                          newdata = temp_holdout, 
                                          type = "prob")
```

The ROC curve summarizes how a binary classifier performs "overall", taking into account all possible thresholds. It shows the trade-off between true positive rate (a.k.a sensitivity, # true positives / # all positives) and the false positive rate (a.k.a 1 - specificity, # false positive / # negatives).


```r
# ROC curve
rocr_prediction <- prediction(holdout_prediction_probs$yes,
                              temp_holdout[["is_popular_f"]])

plot(performance(rocr_prediction, "tpr", "fpr"), colorize=TRUE) 
```

![](CopyOfKaggle_1902224_LisaLang_files/figure-html/unnamed-chunk-4-1.png)<!-- -->

AUC is the "area under the (ROC) curve". This is a number between 0 and 1. Higher AUC generally means better classification.


```r
# AUC
holdout_AUC[["logit_lasso"]] <- performance(rocr_prediction, 
                                            measure = "auc")@y.values[[1]]
# print(holdout_AUC[["logit_lasso"]])
```

The bigger the area under the ROC curve is, the better is our prediction. In this case we have an AUC of 0.7111.


```r
# PREDICT on test_data
test_prediction_probs <- predict.train(logit_lasso_model, 
                                       newdata = test_data, 
                                       type = "prob")

# WRITE prediction on test set
filename <- paste0("predictions/",round(holdout_AUC[["logit_lasso"]]*1000),"_lasso.csv")
df <- data.frame(article_id = test_data$article_id,
                   score = test_prediction_probs[,2])
names(df) <- c("article_id", "score")
write.csv(df,file=filename,row.names=FALSE)
```

### Random Forest

Next, I am going to build a Probability Random Forest to predict y. 

Random Forest combines multiple decision trees, each fit to a random sample of the original data. It builds a predictive model without intervention to select variables or functional form. By combining multiple trees it reduces variance with minimal increase in bias.

I am going to use the default option of growing 500 trees, with several different tuning parameters, such as number of variables randomly chosen for any split in the tree or the minimum number of observations in the terminal nodes. To verify the optimal parameters I use 5-fold cross-validation. Here data scaling was not applied during training.


```r
# tuning parameters
train_control <- trainControl(method = "cv", 
                         number = 5,
                         classProbs = TRUE,
                         summaryFunction = twoClassSummary)

tune_grid <- expand.grid(
  .mtry = c(5, 7, 9),
  .splitrule = "gini",
  .min.node.size = c(10, 15)
)
# suggested mtry: square root of the number of predictor variables


# train model "ranger"
set.seed(654)
rf_model_p <- train(
  formula(paste0("is_popular_f", variables)),
  method = "ranger",
  data = temp_train,
  tuneGrid = tune_grid,
  trControl = train_control,
  metric = "ROC",
  verbose = FALSE
)

# rf_model_p
```

The performance of the random forest model changed only marginally with different tuning paramaters. The best results were produced with the following parameters: 5 features to consider for each split and minimum 10 observations in each terminal node.


```r
# Probability prediction
holdout_prediction_probs <- predict.train(rf_model_p, 
                                          newdata = temp_holdout, 
                                          type = "prob")

# AUC
rocr_prediction <- prediction(holdout_prediction_probs$yes,
                              temp_holdout[["is_popular_f"]])
holdout_AUC[["random_forest"]] <- performance(rocr_prediction,
                                              measure = "auc")@y.values[[1]]
# print(holdout_AUC[["random_forest"]])
```
The random forest model produced an AUC of 0.7213, which is an improvement to the previous model (Logit Lasso AUC: 0.7111).


```r
# PREDICT on test_data
test_prediction_probs <- predict.train(rf_model_p, 
                                       newdata = test_data, type = "prob")

# WRITE prediction on test set
filename <- paste0("predictions/",round(holdout_AUC[["random_forest"]]*1000),"_rf.csv")
df <- data.frame(article_id = test_data$article_id,
                 test_prediction_probs[,2])
names(df) <- c("article_id", "score")
write.csv(df,file=filename,row.names=FALSE)
```

### Gradient Boosting

Gradient boosting machines are also an ensemble of trees, however, the method of building the trees is different. The idea is to gradually (step-by-step) improve the trees by using the residuals of the previous tree. For not overfitting the data, a shrinkage parameter is constantly added, as well as a learning rate.

I am going to use different hyperparameters, which include the number of trees (the number of gradient boosting iteration), maximum nodes per tree, the learning rate and the minimum number of observations in trees' terminal nodes.


```r
tune_grid <- expand.grid(n.trees = c(100, 500, 1000), 
                        interaction.depth = c(2, 3, 4, 6), 
                        shrinkage = c(0.005, 0.01, 0.1),
                        n.minobsinnode = c(5,10))

set.seed(1234)
gbm_model <- train(formula(paste0("is_popular_f", variables)),
                   method = "gbm",
                   data = temp_train,
                   trControl = train_control,
                   tuneGrid = tune_grid,
                   preProcess = c("center", "scale", "pca"),
                   metric = "ROC",
                   verbose = FALSE
                   )

# gbm_model
```

The final values used for the model were n.trees = 1000, interaction.depth = 6, shrinkage = 0.01 and n.minobsinnode = 10.


```r
# Probability prediction
holdout_prediction_probs <- predict.train(gbm_model, 
                                          newdata = temp_holdout, 
                                          type = "prob")

# AUC
rocr_prediction <- prediction(holdout_prediction_probs$yes,
                              temp_holdout[["is_popular_f"]])

holdout_AUC[["gradient_boosting"]] <- performance(rocr_prediction, measure = "auc")@y.values[[1]]
# print(holdout_AUC[["gradient_boosting"]])
```

The gradient boosting model produced an AUC of 0.7067, which shows no improvement compared to the previous models (Logit Lasso AUC: 0.7111, Random Forest AUC: 0.7213).

I also tried to remove `pca` variable reduction, which improved the AUC to 0.7338. This might be an indicator that variables are not very much correlated, which I had already assumed from the correlation table in the DataExplorer report.


```r
# PREDICT on test_data
test_prediction_probs <- predict.train(gbm_model, newdata = test_data, type = "prob")

# WRITE prediction on test set
filename <- paste0("predictions/",round(holdout_AUC[["gradient_boosting"]]*1000),"_gbm.csv")
df <- data.frame(article_id = test_data$article_id,
                   score = test_prediction_probs[,2])
names(df) <- c("article_id", "score")
write.csv(df,file=filename, row.names = FALSE)
```

### Neural Network

I am going to train my neural network model with the caret package and tune the hyper-parameters using cross validation. 
The hyperparameters of my neural net model are `size` and `decay`. Size is the number of units in hidden layer (nnet fit a single hidden layer neural network) and decay is the regularization parameter to avoid over-fitting. 
Data is preprocessed, meaning centered, scaled and dimensions are reduced using PCA.


```r
tune_grid <- expand.grid(size = c(3, 5, 7, 10, 15),
                         decay = c(1, 1.5, 2, 2.5, 5, 7, 10))

set.seed(857)
nnet_model <- train(formula(paste0("is_popular_f", variables)),
                   method = "nnet",
                   data = temp_train,
                   trControl = train_control,
                   tuneGrid = tune_grid,
                   preProcess = c("center", "scale", "pca"),
                   metric = "ROC",
                   trace = FALSE) # avoid extensive iteration output

nnet_model
```

The final values used for the model were size = 7 and decay = 5.


```r
# Probability prediction
holdout_prediction_probs <- predict.train(nnet_model,
                                          newdata = temp_holdout, 
                                          type = "prob")

# AUC
rocr_prediction <- prediction(holdout_prediction_probs$yes,
                              temp_holdout[["is_popular_f"]])

holdout_AUC[["nnet_model"]] <- performance(rocr_prediction, 
                                           measure = "auc")@y.values[[1]]
# print(holdout_AUC[["nnet_model"]])
```

The neural network model's AUC of 0.7137 is similar to the lasso model (Logit Lasso AUC: 0.7111, Random Forest AUC: 0.7213, Gradient Boosting AUC: 0.7067).

Again I tried to train the same model without using PCA as the variables, but it only marginally improved the result with an AUC of 0.7177.



```r
# PREDICT on test_data
test_prediction_probs <- predict.train(nnet_model, newdata = test_data, type = "prob")

# WRITE prediction on test set
filename <- paste0("predictions/",round(holdout_AUC[["nnet_model"]]*1000),"_nnet.csv")
df <- data.frame(article_id = test_data$article_id,
                   score = test_prediction_probs[,2])
names(df) <- c("article_id", "score")
write.csv(df,file=filename,row.names=FALSE)
```

For comparison I am going to train a neural network model using H2O. H2O is an open source, in-memory, distributed, fast, and scalable machine learning and predictive analytics platform.

First I am going to connect to a local H2O instance.


```r
# Try to connect to a local H2O instance that is already running.
# If not found, start a local H2O instance from R with 8 gigabytes of memory.
h2o.init(max_mem_size = '8g') 
```

```
##  Connection successful!
## 
## R is connected to the H2O cluster: 
##     H2O cluster uptime:         6 days 22 hours 
##     H2O cluster timezone:       Europe/Vienna 
##     H2O data parsing timezone:  UTC 
##     H2O cluster version:        3.28.0.2 
##     H2O cluster version age:    7 months and 24 days !!! 
##     H2O cluster name:           H2O_started_from_R_lisahlmsch_gap884 
##     H2O cluster total nodes:    1 
##     H2O cluster total memory:   1.69 GB 
##     H2O cluster total cores:    4 
##     H2O cluster allowed cores:  4 
##     H2O cluster healthy:        TRUE 
##     H2O Connection ip:          localhost 
##     H2O Connection port:        54321 
##     H2O Connection proxy:       NA 
##     H2O Internal Security:      FALSE 
##     H2O API Extensions:         Amazon S3, XGBoost, Algos, AutoML, Core V3, TargetEncoder, Core V4 
##     R Version:                  R version 3.6.3 (2020-02-29)
```

For this model I have chosen 3 hidden layers of 32 neurons each. This is a rather small network but it will run faster and hopefully still produces good results, as in the previous example also a small number of neurons was chosen by the algorithm. 
I have chosen 10000 epochs, but expect the misclassification rate to converge earlier, which will stop the training due to the early-stopping rule I will implement.


```r
h2o_nnet_model <- h2o.deeplearning(y = "is_popular_f",
                             x = names(train_data[,-c(59,61)]),
                             training_frame = as.h2o(temp_train),
                             validation_frame = as.h2o(temp_holdout),
                             activation = "Rectifier",
                             hidden=c(32,32,32), 
                             epochs = 10000,
                             stopping_rounds=2,
                             stopping_metric="misclassification",
                             stopping_tolerance=0.01,
                             reproducible = TRUE,
                             nfolds = 5,
                             keep_cross_validation_predictions = TRUE,
                             seed = 123)

holdout_AUC[["h2o_nnet_model"]] <- h2o.auc(h2o_nnet_model, valid = TRUE)
# print(holdout_AUC[["h2o_nnet_model"]])
```

The h2o neural network model was not able to produce a better result than the neural net model trained with caret: 0.7031 (h2o) versus 0.7137 (caret).


```r
test_prediction_probs <- h2o.predict(h2o_nnet_model, 
                                     newdata = as.h2o(test_data))

filename <- paste0("predictions/",round(holdout_AUC[["h2o_nnet_model"]]*1000),"_h2o_nnet.csv")
df <- data.frame("article_id" = test_data$article_id,
                 "score" = as.data.table(test_prediction_probs[,3]))
names(df) <- c("article_id", "score")
write.csv(df,file=filename,row.names=FALSE)
```

### Stacked model

Finally I am going to build a stacked model using h2o. 

I am going to combine four models of different families using cross validation for hyperparameter tuning. These are my **base models**. With these models I am going to approximate the target variable. With the output of each model I am going to use some kind of combining mechanism to combine the results: I am going to use the preditions of my base models as features to train a new model - my **metalearner**. With this combination I am going make my final prediction. The metalearner is supposed to highlight each base learner where it performs better and where it performs worse. In short: Stacking is a model ensembling technique used to combine information from multiple predictive models to produce a new one. 

*Model 1 - Elastic net:*  
In this model I am again using grid search with cross-validation to tune the hyperparamter `alpha` and then extract the best models in terms of cross validated AUC.


```r
hyper_params <- list(alpha = c(0, .25, .5, .75, 1))

glm_grid <- h2o.grid(y = "is_popular_f",
                     x = names(train_data[,-c(59,61)]),
                     training_frame = as.h2o(temp_train),
                     validation_frame = as.h2o(temp_holdout),
                     algorithm = "glm",
                     lambda_search = TRUE,
                     nfolds = 5,
                     seed = 123,
                     family = "binomial",
                     hyper_params = hyper_params,
                     keep_cross_validation_predictions = TRUE,
                     grid_id = "1glm")

# Get the grid results, sorted by AUC
glm_gridperf <- h2o.getGrid("1glm",
                              sort_by = "auc",
                              decreasing = TRUE) 

# print(glm_gridperf)
```


```r
# Grab the model_id for the top GLM model, chosen by best AUC
best_glm_model_id <- glm_gridperf@model_ids[[1]]
best_glm <- h2o.getModel(best_glm_model_id)

# get validation AUC
holdout_AUC[["h2o_glm"]] <- h2o.auc(best_glm, valid = TRUE)
# holdout_AUC[["h2o_glm"]]
```

The best model in terms of AUC was an elastic net model (`alpha` = 0.25) and produced an AUC of 0.7108 on the validation data set.


```r
test_prediction_probs <- h2o.predict(best_glm, newdata = as.h2o(test_data))

filename <- paste0("predictions/",round(holdout_AUC[["h2o_glm"]]*1000),"_h2o_glm.csv")
df <- data.frame("article_id" = test_data$article_id,
                 "score" = as.data.table(test_prediction_probs[,3]))
names(df) <- c("article_id", "score")
write.csv(df,file=filename,row.names=FALSE)
```


*Model 2 - Random Forest:*
As in the previous random forest example I will be growing 500 trees. The default in h2o is 50 but I have incresed it because I will let the early stopping criteria decide when the random forest is sufficiently accurate. 
The algorithm will stop fitting new trees when the 2-tree average is within 0.001 (default) of the prior two 2-tree averages.
As part of hyperparameter tuning I am going to specify different number of columns to randomly select at each level.


```r
rf_params <- list(ntrees = c(500),
                  mtries = c(3, 5, 7, 9))

rf_grid <- h2o.grid(y = "is_popular_f",
                    x = names(train_data[,-c(59,61)]),
                    training_frame = as.h2o(temp_train),
                    validation_frame = as.h2o(temp_holdout), 
                    algorithm = "randomForest", 
                    nfolds = 5,
                    seed = 123,
                    keep_cross_validation_predictions = TRUE,
                    hyper_params = rf_params,
                    stopping_rounds = 2,
                    grid_id = "2rf")


# Get the grid results, sorted by AUC
rf_gridperf <- h2o.getGrid("2rf",
                              sort_by = "auc",
                              decreasing = TRUE)
# print(rf_gridperf)
```



```r
# Grab the model_id for the top RF model, chosen by validation AUC
best_rf_model_id <- rf_gridperf@model_ids[[1]]
best_rf <- h2o.getModel(best_rf_model_id)

# get validation AUC
holdout_AUC[["h2o_rf"]] <- h2o.auc(best_rf, valid = TRUE)
# holdout_AUC[["h2o_rf"]]
```

The final model produced an AUC of 0.7278 on the validation set. It used five randomly selected columns at each level (`mtries` = 5) and 259 trees. 


```r
test_prediction_probs <- h2o.predict(best_rf, newdata = as.h2o(test_data))

filename <- paste0("predictions/",round(holdout_AUC[["h2o_rf"]]*1000),"_h2o_rf.csv")
df <- data.frame("article_id" = test_data$article_id,
                 "score" = as.data.table(test_prediction_probs[,3]))
names(df) <- c("article_id", "score")
write.csv(df,file=filename,row.names=FALSE)
```

*Model 3 - Gradient Boosting:*

For this model I am going to adjust some of the default parameters and use grid search to find the optimal hyperparameters.
I will increase the number of trees from the default of 50 to 300. I will try different learning rates. Increasing the learning rate means the contribution of each tree will be stronger, so the model will move further away from the overall mean.
I will also try different values for depth which adjusts the "weakness" of each learner. Adding depth makes each tree fit the data closer. 


```r
gbm_params <- list(learn_rate = c(0.01, 0.05, 0.1, 0.2),
                    max_depth = c(2, 3, 5, 10),
                    sample_rate = c(0.5), # row sample rate
                    col_sample_rate = c(0.5, 1.0))

# Train and validate a cartesian grid of GBMs
gbm_grid <- h2o.grid(y = "is_popular_f",
                     x = names(train_data[,-c(59,61)]),
                     training_frame = as.h2o(temp_train),
                     validation_frame = as.h2o(temp_holdout), 
                     algorithm = "gbm", 
                     nfolds = 5,
                     seed = 123,
                     ntrees = 300,
                     keep_cross_validation_predictions = TRUE,
                     hyper_params = gbm_params,
                     stopping_rounds = 2,
                     grid_id = "3gbm")

# Get the grid results, sorted by AUC
gbm_gridperf <- h2o.getGrid("3gbm",
                              sort_by = "auc",
                              decreasing = TRUE)
# print(gbm_gridperf)
```



```r
# Grab the model_id for the top GBM model, chosen by validation AUC
best_gbm_model_id <- gbm_gridperf@model_ids[[1]]
best_gbm <- h2o.getModel(best_gbm_model_id)

holdout_AUC[["h2o_gbm"]] <- h2o.auc(best_gbm, valid = TRUE)
# holdout_AUC[["h2o_gbm"]]
```

The best GBM model produced an AUC of 0.7307 on the validation set. It used a learning rate of 0.01, max_depth = 10, col_sample_rate of 0.5 and 295 number of trees.


```r
test_prediction_probs <- h2o.predict(best_gbm, newdata = as.h2o(test_data))

filename <- paste0("predictions/",round(holdout_AUC[["h2o_gbm"]]*1000),"_h2o_gbm.csv")
df <- data.frame("article_id" = test_data$article_id,
                 "score" = as.data.table(test_prediction_probs[,3]))
names(df) <- c("article_id", "score")
write.csv(df,file=filename,row.names=FALSE)
```

*Model 4 - Deep learning:*  
I am going to include the previously trained neural net in my stacked model.

*Validation performances:*  
Here is a summary of performances (evaluated on the validation set) for each of the different models  and its optimal hyperparameters.




```r
kable(data.frame(holdout_AUC)) %>%
  kable_styling()
```
<table class="table" style="margin-left: auto; margin-right: auto;">
 <thead>
  <tr>
   <th style="text-align:right;"> h2o_glm </th>
   <th style="text-align:right;"> h2o_rf </th>
   <th style="text-align:right;"> h2o_gbm </th>
   <th style="text-align:right;"> h2o_nnet </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:right;"> 0.7108 </td>
   <td style="text-align:right;"> 0.728 </td>
   <td style="text-align:right;"> 0.7307 </td>
   <td style="text-align:right;"> 0.7031 </td>
  </tr>
</tbody>
</table>



*Stacked ensemble model:*  
Recall, stacking is a combination of predictive models. The idea is that combined models should produce something more stable and have larger predictive performance (decrease the variance).

To produce a stacked ensemble model I am going to define several independent models and use the same folds for training each model. Then I do an out of fold prediction (produce prediction for these left out folds) and record the value we predicted as new features. With another predictive model I use these new features to estimate the outcome based on these baseline predictive scores. I am stacking the out of fold predictions and produce one single score from those features.

First I start with the baseline meta-learner - a glm model with non-negative weights (coefficients are constraints to be non-negative).


```r
ensemble_model_glm <- h2o.stackedEnsemble(
                     y = "is_popular_f",
                     x = names(train_data[,-c(59,61)]),
                     training_frame = as.h2o(temp_train),
                     validation_frame = as.h2o(temp_holdout), 
                     base_models = list(best_glm,
                                        best_rf,
                                        best_gbm,
                                        h2o_nnet_model))
```

Then I use "gbm" as meta-learner.


```r
ensemble_model_gbm <- h2o.stackedEnsemble(
                     y = "is_popular_f",
                     x = names(train_data[,-c(59,61)]),
                     training_frame = as.h2o(temp_train),
                     validation_frame = as.h2o(temp_holdout), 
                     base_models = list(best_glm,
                                        best_rf,
                                        best_gbm,
                                        h2o_nnet_model),
                     metalearner_algorithm = "gbm")
```

*Validation Performances:*


```r
validation_performances <- list()
validation_performances[["ensemble_glm"]] = h2o.auc(h2o.performance(ensemble_model_glm, valid = TRUE))
validation_performances[["ensemble_gbm"]] = h2o.auc(h2o.performance(ensemble_model_gbm, valid = TRUE))

kable(data.frame(validation_performances)) %>% 
  kable_styling(full_width = FALSE)
  # column_spec(1:2,bold = T, color = "white", background = "darkgrey")
```

<table class="table" style="width: auto !important; margin-left: auto; margin-right: auto;">
 <thead>
  <tr>
   <th style="text-align:right;"> ensemble_glm </th>
   <th style="text-align:right;"> ensemble_gbm </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:right;"> 0.735 </td>
   <td style="text-align:right;"> 0.713 </td>
  </tr>
</tbody>
</table>
The first ensemble, using glm as meta-learner performed better than elastic net, random forest, gbm and deep learning.


```r
# PREDICT on test_data
test_prediction_probs <- h2o.predict(ensemble_model_glm, newdata = as.h2o(test_data))

# WRITE prediction on test set
filename <- paste0("predictions/",round(validation_performances[["ensemble_glm"]]*1000),"_stacking.csv")
df <- data.frame("article_id" = test_data$article_id,
                 "score" = as.data.table(test_prediction_probs[,3]))
names(df) <- c("article_id", "score")
write.csv(df,file=filename,row.names=FALSE)
```

For my final model I therefor choose a stacked ensemble model with glm as meta learner and train it on the entire training dataset to predict my final results:

## FINAL MODEL


```r
# elastic net
# The best model in terms of AUC was an elastic net model (alpha = 0.25)
h2o_glm_x <- h2o.glm(y = "is_popular_f",
                     x = names(train_data[,-c(59,61)]),
                     training_frame = as.h2o(train_data),
                     lambda = 0.0004935,
                     family = "binomial",
                     alpha = c(.25),
                   seed = 700)

# random forest
# The final model used five randomly selected columns at each level (mtries = 5) and 259 trees. 
h2o_rf_x <- h2o.randomForest(y = "is_popular_f",
                    x = names(train_data[,-c(59,61)]),
                    training_frame = as.h2o(train_data),
                    stopping_rounds = 2,
                    ntrees = 300,
                    mtries = 5,
                   seed = 700)

# GBM
# The best GBM model used a learning rate of 0.01, max_depth = 10, col_sample_rate of 0.5 and 295 number of trees.
h2o_gbm_x <- h2o.gbm(y = "is_popular_f",
                   x = names(train_data[,-c(59,61)]),
                   training_frame = as.h2o(train_data),
                   ntrees = 300,
                   learn_rate = 0.01,
                   max_depth = 10,
                   sample_rate = 0.5,
                   col_sample_rate = 0.5,
                   stopping_rounds = 2,
                   seed = 700)

# neural net
h2o_nnet_x <- h2o.deeplearning(y = "is_popular_f",
                             x = names(train_data[,-c(59,61)]),
                             training_frame = as.h2o(train_data),
                             activation = "Rectifier",
                             hidden=c(32,32,32), 
                             epochs = 10000,
                             stopping_rounds=2,
                             stopping_metric="misclassification", ## could be "MSE","logloss","r2"
                             stopping_tolerance=0.01,
                             reproducible = TRUE,
                             seed = 700)

ensemble_model_glm_x <- h2o.stackedEnsemble(
                              y = "is_popular_f",
                              x = names(train_data[,-c(59,61)]),
                              blending_frame = as.h2o(train_data),
                              base_models = list(h2o_nnet_x, 
                                                 h2o_gbm_x, 
                                                 h2o_rf_x, 
                                                 h2o_glm_x))

test_prediction_probs <- h2o.predict(ensemble_model_glm_x, newdata = as.h2o(test_data))

filename <- paste0("predictions/final_stacking_x.csv")
df <- data.frame("article_id" = test_data$article_id,
                 "score" = as.data.table(test_prediction_probs[,3]))
names(df) <- c("article_id", "score")
write.csv(df,file=filename,row.names=FALSE)
```



## Summary

The dataset provided was clean and complete, so no further data cleaning steps were necessary prior to model building. Data was often preprocessed, meaning centered, scaled and dimensions were reduced using PCA, since correlated variables are problematic for gradient-based optimization. PCA however worsened model performance. 

Using the caret package, I trained a LASSO model, a random forest model, a gradient boosting machine model and a neural net. Among these random forest had the best results in terms of AUC on the validation set. 
Using h2o I trained an elastic net model, a random forest, a gradient boosting machine model and a neural net and stacked them using both glm and gbm as a meta learner. The stacked model using glm as a metalearner produced the best results in terms of AUC on the validation set. Among the base models gbm showed the best results, while gbm using caret was rather weak in predicting the target variable.

In the Kaggle competition my gbm model trained with h2o scored the best: the public score was 0.70587.


## Acknowledgments
The dataset used in the competition can be found at the UCI repository. We thank Kelwin Fernandes, Pedro Vinagre, Paulo Cortez and Pedro Sernadela for making it publicly available. Check also their publication below.

K. Fernandes, P. Vinagre and P. Cortez. A Proactive Intelligent Decision Support System for Predicting the Popularity of Online News. Proceedings of the 17th EPIA 2015 - Portuguese Conference on Artificial Intelligence, September, Coimbra, Portugal.







```
