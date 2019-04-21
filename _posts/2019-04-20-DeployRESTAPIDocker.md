---
title: "Deploy your Machine Learning Model as a REST API with Docker"
date: 2019-04-20
tags: [Deploy, Machine Learning, Docker, REST API]
#header:
#  image: "/assets/images/activityrecognition.gif"
excerpt: "Deploy, Docker, REST API"
---

We will use the famous iris dataset to build a classifier that we can serve as an API via a Docker container. The sklearn pipeline for preprocessing as well as predicting is saved as a joblib file. 

## Train and export prediction pipeline

In order to efficiently retrain the model, we will create a file named __model.py__

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
## Deploy the model with Docker and Flask

In order to run Docker containers, you need the Docker daemon installed. Once it's installed, we need a directory with the following files:
1. Dockerfile
2. __init__.py
3. app.py
4. requirements.txt
5. model.joblib

## Setup the Dockerfile

To build our Docker container, we need to provide a __Dockerfile__. 

```python
FROM python:3.5.3
 
WORKDIR /app/
 
COPY requirements.txt /app/
RUN pip install -r ./requirements.txt
 
COPY app.py __init__.py /app/
COPY model.joblib /app/
 
EXPOSE 5000
 
ENTRYPOINT python ./app.py
```

After installing the necessary dependencies for python, the file installs everything from the __requirements.txt__ file.

```python
Flask==1.0.2
scikit_learn==0.20.1
```

The dockerfile then copies the necessary files to the app working directory. The __app.py__ is a basic Flask App for serving our model pipeline.

```python

```


**Full code**: [Github](https://github.com/hacheemaster/DeployMLRestAPIDocker)

