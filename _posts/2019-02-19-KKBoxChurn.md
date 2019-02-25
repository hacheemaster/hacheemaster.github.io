---
title: "Churn Prediction for KKBox"
date: 2019-02-19
tags: [kaggle, churn, logistic regression]
header:
  image: "/assets/images/churn.jpeg"
excerpt: "Kaggle, Churn, Logistic Regression"
---

# Agenda 

1. **Problem Statement**: Predict churn for a monthly music subscription service (KKBox)
2. **Data**: [Kaggle KKBox Churn Dataset](https://www.kaggle.com/c/kkbox-churn-prediction-challenge/data)
3. **Methods**: 
* Logistic Regression
* Decision Trees
4. **Results & Insights**:
*  

# Problem Statement

KKBox offers a subscription based music streaming service. Customers can either auto-renew or choose to renew manually each month. Users can also choose to cancel their membership at anytime. Therefore, the definition of churn is a bit tricky. The competition hosts chose to define a member as having churned if there is no new valid subscription 30 days after the current membership expires, even if the user chooses to cancel.

The goal is to perform some exploratory analysis to see what insights we can find about churning customers and build a model to predict the likelihood a given customer will churn. 

# Data

The [Kaggle KKBox Churn Dataset](https://www.kaggle.com/c/kkbox-churn-prediction-challenge/data) presented plenty of opportunity for data cleaning using **pandas**, visualization using **matplotlib**, and prediction using **sklearn**. Below are some of the items addressed:
* Feature engineering: Time-based columns
* Handling missing values: Missing values were imputed using the median/mean/mode when appropriate; a separate indicator column for each variable was also created to determine whether the value was previously missing. Certain missing values between inputs were correlated. 
* Outlier detection: Age was the main variable with outliers - with negative values and some in the thousands. Exploratory analysis also revealed that missing values for age were secretly stored as 0. After all the cleaning, age went from 11% missing to 60%!

# Methods

## Logistic Regression

In order to address **multicollinearity**, [**variance inflation factor**](https://en.wikipedia.org/wiki/Variance_inflation_factor) was used to remove correlated inputs (VIF>10) for logistic regression. Correlated inputs lead to biased estimates of our coefficients.  


## Decision Trees



# Results & Insights
* Cleaning the data required digging into 
* What is the baseline performance based on the most prevelant class?
* Does a particular city churn more? If so, are there any other variables that might be drivers of the higher churn (age, gender, etc.)?
* Does a particular gender churn more?
* Is there a specific bucketed age group that churns more?
* Does a specific registration lead to more churns? It could be that people that register via similar methods have similar behaviors.

Full Code: [Github]()
