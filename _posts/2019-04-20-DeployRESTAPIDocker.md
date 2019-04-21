---
title: "Deploy your Machine Learning Model as a REST API with Docker"
date: 2019-04-20
tags: [Deploy, Machine Learning, Docker, REST API]
#header:
#  image: "/assets/images/activityrecognition.gif"
excerpt: "Deploy, Docker, REST API"
---

We will use the famous iris dataset to build a classifier that we can serve as an API via a Docker container. 

## Train and save a model

```python
from sklearn.externals import joblib
from sklearn import datasets
from sklearn.feature_selection import SelectKBest, chi2
from sklearn.ensemble import RandomForestClassifier
from sklearn.pipeline import Pipeline

# Load the Iris dataset
iris = datasets.load_iris()

# Set up a pipeline
pipeline = Pipeline([
    ('feature_selection', SelectKBest(chi2, k=2)),
    ('classification', RandomForestClassifier(n_estimators=100))
])

pipeline.fit(iris.data, iris.target)

# Export the classifier to a file
joblib.dump(pipeline, 'model.joblib')

```


**Full code**: [Github](https://github.com/hacheemaster/DeployMLRestAPIDocker)

