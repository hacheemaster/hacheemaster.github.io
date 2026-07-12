---
title: "Hazard Count Prediction for Liberty Mutual's Properties"
date: 2019-01-22
redirect_from:
  - /KaggleLibertyMutual/
  - /kagglelibertymutual/
tags: [kaggle, random forest, xgboost]
categories:
  - Machine Learning
header:
  image: "/assets/images/propertyinspection.jpg"
excerpt: "A top-12% Kaggle finish predicting property hazard counts for Liberty Mutual, where ensembling and disciplined cross-validation beat feature engineering."
---

**Kaggle Ranking**: Top 12% (248/2,362 competitors)	

**End Date**: August 28, 2015

**Description**: Liberty Mutual needs to decide which homes are worth insuring, and this competition frames that underwriting decision as a supervised learning problem: predict the hazard count for a property from anonymized attributes such as its roof, foundation, windows, and siding. Getting these predictions right lets the insurer align its portfolio of home policies with its business goals.

**Best Method**: Weighted Ensemble (RF + XGBoost)

**Reflections**: This [competition](https://www.kaggle.com/c/liberty-mutual-group-property-inspection-prediction) was particularly challenging because the anonymized features left little room for feature engineering. It was a fun exercise in ensembling and performing proper cross-validation to prevent overfitting on the public leaderboard, as tempting as that may be.
