---
title: "Churn Prediction: Music Subscription Service KKBox"
date: 2019-02-19
tags: [kaggle, churn, logistic regression]
header:
  image: "/assets/images/churn.jpg"
excerpt: "Kaggle, Churn, Logistic Regression"
---

**Description**: KKBOX offers a subscription based music streaming service. Customers can either auto-renew or choose to renew manually each month. Users can also choose to cancel their membership at anytime. Therefore, the definition of churn is a bit tricky. The competition hosts chose to define a member as having churned if there is no new valid subscription 30 days after the current membership expires, even if the user chooses to cancel. 

**Best Method**: Logistic Regression

**Reflections**: This [competition](https://www.kaggle.com/c/kkbox-churn-prediction-challenge) was particularly challenging because the anonymized features left little room for feature engineering. It was a fun exercise in ensembling and performing proper cross-validation to prevent overfitting on the public leaderboard, as tempting as that may be.
