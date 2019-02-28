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
* Logistic regression with model monitoring implemented is the best solution for the given data and business use case.

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

SVM's use kernels to project data into a higher dimension to make it linearly separable and maximize the margin between classes using a N-1 dimensional hyperplane. Since the time complexity of SVM is O(N^2), they are more appropriate for smaller datasets and will not be used for the current dataset.

## Naive Bayes

Naive Bayes classifier is a probabilistic classifier that uses Bayes' theorem along with the assumption of independent covariates. It is very fast with a time complexity of O(N). For a new customer - using Bayes rule - we can find P(churn=1/age=a1,...,gender=a28) by calculating P(age=a1,...,gender=a28/churn=1)*P(churn=1)/P(age=a1,...,gender=a28). However, since we assume indepencence of our features, this simplifies to P(age=a1/churn=1)x...xP(gender=a28/churn=1)xP(churn=1)/P(age=a1,...,gender=a28). We can create frequency tables from our training data to calculate the posterior probability using the prior probabilities. If we ever run into the case where a particular attribute value-class combination has a frequency of zero, we can add 1 to the count (**Laplace Smoothing**). 

# Results & Insights

We can evaluate each of the classifiers using an ROC curve and computing the Area Under the Curve (AUC). AUC represents the probability of a classifier ranking a randomly chosen positive observation higher than a randomly chosen negative observation. If the classifier does a perfect job of separating the classes, then this value should be equal to 1.

<img src="/assets/images/KKBOX_ROC_testing.png"> 

| Machine Learning Technique | AUC (TRAIN) | AUC (TESTING)| Runtime Complexity |
| --- | --- | --- | --- |
| Naive Bayes | 0.66 | 0.66 | O(N) |
| Logistic Regression | 0.68 | 0.68 | O(N) |
| Random Forest | 0.71 | 0.71 | O(N x log(N)) |
| Decision Tree | 0.73 | 0.72 | O(N x log(N)) |
| Support Vector Machines | - | - | O(N^2) |

Based on **interpretability** and **accuracy vs. runtime** considerations, it makes the most sense to deploy the logistic regression model into production to periodically predict the likelihood of a customer churning (churn next 30 days, churn next 3 months, churn next 6 months, etc.). We can rank order the customers based on highest probability of churning and use a separate A/B tested marketing promotions model - only targeting those customers with a high probability of churning that will also accept the marketing promotion. That way, depending on the cost of each promotion, we save money and only target the customers most likely to renew their subscription while also enhancing the customer experience by not annoying a larger population of users that might churn now but come back later. 

Finally, we will have to put some sort of **model monitoring** into effect to check whether the distribution of our scores or input parameters to the logistic model have started deviating too much. This indicates the model might need to be trained on a new dataset as a result of changing customer behaviors. Metrics such as [**Population Stability Index**](https://www.listendata.com/2015/05/population-stability-index.html) can be used to rank order the scores and compare the distribution across deciles to see if has shifted.




Full Code: [Github](https://github.com/hacheemaster/KKBox-Churn-Prediction/blob/master/KKBOX%20Churn%20Prediction.ipynb)
