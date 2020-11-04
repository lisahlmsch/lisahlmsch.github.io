---
title: "Data cleaning"
date: 2020-11-04
tags: [extreme_values, missing_values]
excerpt: "Exreme values, missing values"
mathjax: "true"
---

## Introduction

As the majority of time of a data scientist is used for data preparation and cleaning, I wanted to dedicate a section of my github repository to this task.
The goal is to discuss and summarize the most important aspects of data cleaning and what impact different approaches have for model training.


## Process

The data cleaning process starts with the raw data and results in clean and tidy data. 

Choices in the process need to be the results of conscious decisions and have to be properly **documented**. 
**Flag** the observation with an indicator variable of changes you made.
When it affects many observations, it may have effects on the results of our analysis. 
In such a case it is good practice to try different solutions to the problem and see whether and how that affects the results of the analysis. 
This process is sometimes called **robustness checking**.

### Variable tormat
All important variables should be stored in an appropriate format.

* Binary variables are best stored as 0 or 1. 
* Qualitative variables with several values may be stored as text or number 
* Convert to dummy variables if appropriate

### Variable cleaning
Often numbers are stored in weir formats such as "12 min" or are wrongly interpreted upon import because of the separators used (point versus comma)
* Extracting numerical information where necessary or 
* Transforming text into numbers

### Missing values
It is crucial to identify missing values and to make appropriate decisions on them.

If the fraction of missing values is not too high,  we can remove all and work with observations that have non-missing values for all variables used in the analysis. (selection bias!)

As an alternative keep these observations, but apply imputation: Fill in some value for the missing values, such as the average value, or a value randomly selected value from the other observations

We may opt to leave them as missing, then missing values need to be stored in a way that the statistical software recognizes. 
For a qualitative variable, we may want to add missing as a new value. 

Missing values cause problems when ….
* we have too few observations in the data with valid information so that we cannot confidently generalize our results from the particular dataset to the situation we truly care about.  The variable that best captures the information we need may have many missing values and removing them might reduce the sample size too much. Solution: focus on more fully filled variables. A less perfect variable is available with few missing variables.
Removing those observations causes selection bias.
One version of this approach is to work with observations that make up a well-defined subsample, in which the missing data problem is a lot less severe. For example, if missing values are a lot more prevalent among small firms in administrative data on firms, we may exclude all small firms from the data. The advantage of such a choice is transparency: the results of the analysis will refer to medium and large firms.
* they are stored in a way that the statistical software does no recognize it. 

Sometimes, missing values are recorded with number values, outside the range (e.g., 0 for no, 1 for yes, 9 for missing). Such values need to be replaced.


### Checking range
Values of each variable should be within their admissible range.
* Values outside the range are best replaced as missing unless it is obvious what the value was meant to be (e.g., an obvious misplacement of digits)
* Change units of measurement (currency, large numbers with measures in thousands, 
Check duplicates

### Check distribution / extreme values
Extreme values can cause problems with certain types of models. 
For example, linear regression models are less robust to extreme values than decision tree models. 
In general, if you have a legitimate reason to remove an extreme value, it will help your model’s performance. 
You must have a good reason for removing an extreme value, such as suspicious measurements that are unlikely to be real data.
