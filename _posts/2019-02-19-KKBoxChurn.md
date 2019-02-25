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
* Random Forest
* SVM
* Naive Bayes
4. **Results & Insights**:
* For each machine learning technique, make sure to check the assumptions and validate them before interpreting any results.
* Logit transformation is used in logistic regression so we can predict outputs in the range of (-inf,inf) for log odds instead of (0,1) for probabilities.

# Problem Statement

KKBox offers a subscription based music streaming service. Customers can either auto-renew or choose to renew manually each month. Users can also choose to cancel their membership at anytime. Therefore, the definition of churn is a bit tricky. The competition hosts chose to define a member as having churned if there is no new valid subscription 30 days after the current membership expires, even if the user chooses to cancel.

The goal is to perform some exploratory analysis to see what insights we can find about churning customers and build a model to predict the likelihood a given customer will churn. 

# Data

The [Kaggle KKBox Churn Dataset](https://www.kaggle.com/c/kkbox-churn-prediction-challenge/data) presented plenty of opportunity for data cleaning using **pandas**, visualization using **matplotlib**, and prediction using **sklearn**. Below are some of the items addressed:
* Feature engineering: Time-based columns
* Handling missing values: Missing values were imputed using the median/mean/mode when appropriate; a separate **dummy variable** was also created to determine whether the value was previously missing. Certain missing values between inputs were correlated. 
* Outlier detection: Age was the main variable with outliers - with negative values and some in the thousands. Exploratory analysis also revealed that missing values for age were secretly stored as 0. After all the cleaning, age went from 11% missing to 60%!

# Methods

## Logistic Regression

In logistic regression we are predicting the log odds of the target as a linear function of the inputs. Why bother going through all the trouble of going from probabilities to odds to log odds? The simplest reason is because it's harder to predict a restricted range variable such as probability. However, when we make the monotonic transformation from probabilities to logit of the probability, we are free to predict in the range (-inf,inf). 

Logistic regression comes with multiple assumptions. In order to address **multicollinearity**, [**variance inflation factor**](https://en.wikipedia.org/wiki/Variance_inflation_factor) was used to remove correlated inputs (VIF>10). Since our goal is to use the model for interpretability, correlated inputs would lead to biased estimates of our coefficients. 

## Decision Trees

Decision trees use features that produce the greatest reduction in entropy, i.e. the greatest information gain, to split the data. The time complexity for decision trees is O(N*log(N)).   

## Random Forest

Random forests are an extension of decision trees where we only consider a handful of predictors for each tree and build multiple trees (i.e. the forest). The advantage is that weak predictors get a chance to contribute to the overall accuracy and the ensembled forest has a higher accuracy than any one decision tree alone.  

## Support Vector Machines (SVM)

SVM's use kernels to project data into a higher dimension to make it linearly separable and maximize the margin between classes using hyperplanes. Since the time complexity of SVM is O(N^2), they are more appropriate for smaller datasets and would not be appropriate for the current dataset.

## Naive Bayes




# Results & Insights


* Cleaning the data required digging into 
* What is the baseline performance based on the most prevelant class?
* Does a particular city churn more? If so, are there any other variables that might be drivers of the higher churn (age, gender, etc.)?
* Does a particular gender churn more?
* Is there a specific bucketed age group that churns more?
* Does a specific registration lead to more churns? It could be that people that register via similar methods have similar behaviors.

Full Code: [Github]()
