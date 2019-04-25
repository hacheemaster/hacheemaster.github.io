---
title: "Deploy a Containerized Machine Learning Model as a REST API with Docker"
date: 2019-04-20
tags: [Deploy, Machine Learning, Docker, REST API]
#header:
#  image: "/assets/images/activityrecognition.gif"
excerpt: "Deploy, Docker, REST API"
---

Let's use the famous iris dataset to build a classifier that we can serve as an API via a Docker container. The sklearn pipeline for preprocessing as well as predicting is saved as a joblib file. 

## Train and export prediction pipeline

To efficiently retrain the model, let's create a file named __model.py__

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

To run Docker containers you need the Docker daemon installed. Once it's installed, we need a directory with the following files:
1. Dockerfile
2. __ init __.py
3. app.py
4. requirements.txt
5. model.joblib

The __app.py__ is a basic Flask App for serving our model pipeline.

```python
import numpy as np
from flask import Flask
from flask import request
from flask import jsonify
from sklearn.externals import joblib
 
app = Flask(__name__)

model = 'model.joblib'
loaded_model = joblib.load(model)
 
@app.route('/predict''GET',, methods=['POST'])
def predict():
    data = request.get_json()
    X = data['X']
    X = np.array(list(map(float,X.split(',')))) #str to float
    preds = loaded_model.predict(X.reshape(1,-1))
    return jsonify({'classes': preds.tolist()})
  
if __name__ == '__main__':
    app.run(port=5000,host='0.0.0.0')
```

### Test flask server

Before deploying our flask server in a Docker container, let's check to make sure it's working as expected. In a terminal we can run the server by running the following command:
```powershell
python app.py
```
The output should look similar to:
```powershell
 * Serving Flask app "app" (lazy loading)
 * Environment: production
   WARNING: Do not use the development server in a production environment.
   Use a production WSGI server instead.
 * Debug mode: off
 * Running on http://0.0.0.0:5000/ (Press CTRL+C to quit)
```

Open up another terminal and test the server works by running:
```powershell
curl -v -H "Content-Type: application/json" -d '{"X":"3,3,3,3"}' http://127.0.0.1:5000/predict
```

The result of the POST request will be:
```powershell
*   Trying 127.0.0.1...
* TCP_NODELAY set
* Connected to 127.0.0.1 (127.0.0.1) port 5000 (#0)
> POST /predict HTTP/1.1
> Host: 127.0.0.1:5000
> User-Agent: curl/7.52.1
> Accept: */*
> Content-Type: application/json
> Content-Length: 15
> 
* upload completely sent off: 15 out of 15 bytes
* HTTP 1.0, assume close after body
< HTTP/1.0 200 OK
< Content-Type: application/json
< Content-Length: 29
< Server: Werkzeug/0.14.1 Python/3.6.7
< Date: Thu, 25 Apr 2019 04:55:30 GMT
< 
{
  "classes": [
    2
  ]
}
```

Based on the input array of [3,3,3,3] the Random Forest classifier predicted class 2.

## Setup the Dockerfile

To build our Docker container, we need to provide a __Dockerfile__. 

```python
FROM python:3.5.3
 
WORKDIR /app/
 
COPY requirements.txt /app/
RUN pip install -r ./requirements.txt
 
COPY app.py __ init__.py /app/
COPY model.joblib /app/
 
EXPOSE 5000
 
ENTRYPOINT python ./app.py
```

After installing the necessary dependencies for python, the file installs everything from the __requirements.txt__ file.

```python
Flask==1.0.2
scikit_learn==0.20.1
numpy==1.15.4
```

The dockerfile then copies the necessary files to the app working directory. We can build the container with:

```python
docker build . -t docker_flask:v1
```

The -t flag indicates the name:version of our newly created docker image and . indicates that the Dockerfile is in the current directory.

After it has finished building, you can run it with:
```python
sudo docker run --name test-api -p 5000:5000 -d docker_flask:v1
```

We have mapped port 500 from the Docker container to port 5000 on our host machine (localhost). Check that the container is running using:
```python
docker ps
```

Test the exposed API endpoint using the same curl command:
```powershell
curl -v -H "Content-Type: application/json" -d '{"X":"3,3,3,3"}' http://127.0.0.1:5000/predict
```

We get the same output:
```python
{"classes":[2]}
```

Stop the container and remove it with the following commands:
```python
docker stop test-api
docker rm test-api
```

**Full code**: [Github](https://github.com/hacheemaster/DeployMLRestAPIDocker)

