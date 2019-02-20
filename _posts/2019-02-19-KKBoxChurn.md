---
title: "Churn Prediction for KKBox"
date: 2019-02-19
tags: [kaggle, churn, logistic regression]
header:
  image: "/assets/images/churn.jpeg"
excerpt: "Kaggle, Churn, Logistic Regression"
---

**Description**: KKBOX offers a subscription based music streaming service. Customers can either auto-renew or choose to renew manually each month. Users can also choose to cancel their membership at anytime. Therefore, the definition of churn is a bit tricky. The competition hosts chose to define a member as having churned if there is no new valid subscription 30 days after the current membership expires, even if the user chooses to cancel. 

**Best Method**: Logistic Regression

**Reflections**: This [competition](https://www.kaggle.com/c/kkbox-churn-prediction-challenge) was interesting because along with membership information, we were provided with user listening behavior in the logs. This was a great opportunity to explore feature engineering and really understand what variables drive user behavior.
