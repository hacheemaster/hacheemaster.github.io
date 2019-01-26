---
title: "Liberty Mutual Group: Property Inspection Challenge (Kaggle)"
date: 2019-01-22
tags: [kaggle, random forest, xgboost]
header:
  image: "/assets/images/kagglelibertychallenge.jpg"
excerpt: "Machine Learning, Activity Recognition, Data Science"
---

**Result**: Top 12% (2,362 competitors)	

**End Date**: August 28, 2015

**Description**: Predict hazard counts for a house given anonymized variables about the roof, foundation, windows, sliding, etc. This helps Liberty determine whether they want to insure the property. This ensures that Liberty Mutual's portolio of home insurance policies aligns with their business goals. 

**Best Method**: Weighted Ensemble (RF + XGBoost)

**Reflections**: This [competition](https://www.kaggle.com/c/liberty-mutual-group-property-inspection-prediction) was particularly challenging because the anonymized features left little room for feature engineering. It was a fun exercise in ensembling and performing proper cross-validation to prevent overfitting on the public leaderboard.
