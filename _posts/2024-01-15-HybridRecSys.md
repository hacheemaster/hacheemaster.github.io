---
title: "Building a Hybrid Recommendation Engine using Python"
date: 2024-01-15
tags: [Recommendation System, Machine Learning, Python, Content-based filtering, Matrix Factorization]
#header:
#  image: "/assets/images/activityrecognition.gif"
excerpt: "Recommendation System, Python, Matrix Factorization"
---


In this post, we'll explore how to build a hybrid recommendation engine using Python. We'll integrate the strengths of matrix factorization and content-based filtering to create a more robust recommendation system.

## Understanding the Basics
Before diving into the code, let's understand the two core components of our hybrid system:

- Matrix Factorization: This technique helps in uncovering latent features from user-item interactions. It's particularly good at dealing with sparse datasets and providing personalized recommendations.
- Content-Based Filtering: This method uses item features to recommend additional items similar to what the user likes, based on their previous actions.

### Step 1: Creating a Synthetic Dataset
First, we need a dataset to work with. We'll create a simple synthetic dataset representing user interactions with various coupons.

```python
import pandas as pd

# Sample user-item interactions
data = {
    'user_id': [1, 1, 2, 2, 3, 3, 4, 4, 5, 5],
    'coupon_id': [101, 102, 101, 103, 102, 104, 101, 105, 103, 104],
    'clicks': [1, 2, 1, 1, 0, 2, 1, 0, 2, 1],
    'uses': [0, 1, 0, 1, 0, 1, 1, 0, 1, 0]
}
df = pd.DataFrame(data)
```

### Step 2: Implementing Matrix Factorization
We use matrix factorization to uncover the underlying latent features in the user-item interaction data.

```python
from scipy.sparse.linalg import svds
import numpy as np

# Creating a user-item matrix
user_item_matrix = df.pivot(index='user_id', columns='coupon_id', values='interaction').fillna(0)

# Performing matrix factorization
U, sigma, Vt = svds(user_item_matrix, k=2)  # k is the number of latent factors
sigma = np.diag(sigma)
```

### Step 3: Building the Content-Based Component
Next, we add features for each coupon and prepare our dataset for content-based filtering.

```python
# Additional data for content-based filtering
coupon_features_data = {
    'coupon_id': [101, 102, 103, 
104, 105],
'category': ['Medication', 'Wellness', 'Medication', 'Beauty', 'Wellness'],
'discount_rate': [10, 15, 5, 20, 10]
}
coupon_features_df = pd.DataFrame(coupon_features_data)

# One-hot encoding and normalization
coupon_features_df = pd.get_dummies(coupon_features_df, columns=['category'])
from sklearn.preprocessing import MinMaxScaler
scaler = MinMaxScaler()
coupon_features_df['discount_rate'] = scaler.fit_transform(coupon_features_df[['discount_rate']])
```

### Step 4: Combining Both Approaches

We'll now combine predictions from both matrix factorization and content-based filtering.

```python
def hybrid_recommendation(user_id, num_recommendations):
    # Collaborative Filtering Predictions
    cf_predictions = recommend_coupons(user_id, num_recommendations)
    
    # Content-Based Predictions
    cb_predictions = predict_content_based(user_id, coupon_features_df, user_profiles)
    
    # Averaging the Scores
    hybrid_predictions = (cf_predictions + cb_predictions) / 2
    
    # Filtering out already interacted items
    already_interacted = set(df[df['user_id'] == user_id]['coupon_id'])
    hybrid_predictions = hybrid_predictions[~hybrid_predictions.index.isin(already_interacted)]
    
    return hybrid_predictions.sort_values(ascending=False).head(num_recommendations)
```

## Conclusion
This post has walked you through building a basic hybrid recommendation system using Python. We combined matrix factorization and content-based filtering to leverage the strengths of both methods. The hybrid approach ensures that our system can provide personalized recommendations while also handling new items effectively.

Remember, the code provided here is a simplified version of what a real-world recommendation system might look like. In practice, you would need to handle larger datasets, refine the model's parameters, and continuously test and validate your system's performance.

I hope this post provides a solid starting point for those interested in diving into the world of recommendation engines. Happy coding!



## Model

We will leverage Google’s Vertex AI PaLM2 model for the chat model and provide this conversationally fine-tuned model with the right prompt to assist our doctor. A couple of things to note about this model are that its output is limited to 1024 tokens ([source](https://console.cloud.google.com/vertex-ai/publishers/google/model-garden/chat-bison?_ga=2.27439484.1757065834.1701892822-45235083.1701744860&project=valiant-memory-307005)) and the [use policy](https://policies.google.com/terms/generative-ai/use-policy) dictates that the end user must not use it to automate decisions in healthcare. Healthcare policy is still an evolving landscape and there is a line that can be navigated between augmenting practitioner intelligence versus fully automating the task.  

## Serve as an API

### Set up Google Cloud Project
#### Set default project
```python
gcloud config set project [PROJECT-ID]
```

#### Enable Vertex AI
```python
gcloud services enable aiplatform.googleapis.com
```

#### Call PaLM LLM from localhost
```python
gcloud auth application-default login
```

### Set up LangChain
#### Install langchain CLI
```python
pip install langchain-cli
```

The command below will create a production-ready API using LangServe. We will borrow the skeleton of the vertexai-chuck-norris package.
```python
langchain app new my-demo --package vertexai-chuck-norris
```

The original chuck-norris package tells you a joke about Chuck Norris and anything else the user enters as a prompt. Instead of a joke, we will update the model to respond with relevant information to assist the doctor in diagnosing.

#### OLD:
```python
_prompt = ChatPromptTemplate.from_template(

   "Tell me a joke about Chuck Norris and : {text}"

)
```

#### NEW:
```python
_prompt = ChatPromptTemplate.from_template(

   "Tell me the DDx, ICD10, and CPT codes for the following chief complaint: {text}"

)
```

### Local Testing

Change into the my-demo directory and deploy the app on localhost:
```python
langchain serve
```

<img src="/assets/images/langserve.gif"/>

### Deployment

Now we go from local deployment to [deploying](https://github.com/langchain-ai/langserve/tree/main?ref=blog.langchain.dev#deploy-to-gcp) our application as an HTTPS endpoint on [Cloud Run](https://cloud.google.com/run/?hl=en), a serverless engine that runs our Fast API endpoint with autoscaling. Follow the on-screen instructions and pick a name for your application. For this instance, we chose the name gptdoc.
```python
gcloud run deploy
```

To test your application in the cloud you can issue the following command to get a similar response to what we received when we did our local testing. Be sure to replace https://gptdoc-fbpcgd5sua-uw.a.run.app/vertexai-ddx-icd10-cpt/invoke with your endpoint and alter the “text” field to anything else the doctor might be curious about.

```python
curl -X 'POST' \
  'https://gptdoc-fbpcgd5sua-uw.a.run.app/vertexai-ddx-icd10-cpt/invoke' \
  -H 'accept: application/json' \
  -H 'Content-Type: application/json' \
  -d '{
  "input": {
    "text": "stomach pain with blood in stool"
  },
  "config": {},
  "kwargs": {}
}'
```

<img src="/assets/images/langserve-cloud.gif"/>

We can also check our container on Cloud Run to see the HTTP 200 status to confirm a successful response was generated from our service.

<img src="/assets/images/langserve-cloudrun.gif"/>

## Next Steps

Create your own [chain](https://github.com/langchain-ai/langserve-launch-example?ref=blog.langchain.dev) using [langserve](https://www.langchain.com/langserve) to easily deploy your use cases - chatbots, retrieval agents, etc. - in a scalable fashion.


The [original article](https://cloud.google.com/blog/products/ai-machine-learning/deploy-langchain-on-cloud-run-with-langserve/) on Google Cloud is co-authored by the Founding Engineer of LangChain, a very popular framework that makes it seamless to build apps with large language models (LLMs). In this post we will explore the quickest way to deploy a doctor’s assistant that can answer questions about possible differential diagnoses, ICD10 and CPT codes that a doctor might need to consider given the patient is coming in with a particular set of chief complaints.

## Model

We will leverage Google’s Vertex AI PaLM2 model for the chat model and provide this conversationally fine-tuned model with the right prompt to assist our doctor. A couple of things to note about this model are that its output is limited to 1024 tokens ([source](https://console.cloud.google.com/vertex-ai/publishers/google/model-garden/chat-bison?_ga=2.27439484.1757065834.1701892822-45235083.1701744860&project=valiant-memory-307005)) and the [use policy](https://policies.google.com/terms/generative-ai/use-policy) dictates that the end user must not use it to automate decisions in healthcare. Healthcare policy is still an evolving landscape and there is a line that can be navigated between augmenting practitioner intelligence versus fully automating the task.  

## Serve as an API

### Set up Google Cloud Project
#### Set default project
```python
gcloud config set project [PROJECT-ID]
```

#### Enable Vertex AI
```python
gcloud services enable aiplatform.googleapis.com
```

#### Call PaLM LLM from localhost
```python
gcloud auth application-default login
```

### Set up LangChain
#### Install langchain CLI
```python
pip install langchain-cli
```

The command below will create a production-ready API using LangServe. We will borrow the skeleton of the vertexai-chuck-norris package.
```python
langchain app new my-demo --package vertexai-chuck-norris
```

The original chuck-norris package tells you a joke about Chuck Norris and anything else the user enters as a prompt. Instead of a joke, we will update the model to respond with relevant information to assist the doctor in diagnosing.

#### OLD:
```python
_prompt = ChatPromptTemplate.from_template(

   "Tell me a joke about Chuck Norris and : {text}"

)
```

#### NEW:
```python
_prompt = ChatPromptTemplate.from_template(

   "Tell me the DDx, ICD10, and CPT codes for the following chief complaint: {text}"

)
```

### Local Testing

Change into the my-demo directory and deploy the app on localhost:
```python
langchain serve
```

<img src="/assets/images/langserve.gif"/>

### Deployment

Now we go from local deployment to [deploying](https://github.com/langchain-ai/langserve/tree/main?ref=blog.langchain.dev#deploy-to-gcp) our application as an HTTPS endpoint on [Cloud Run](https://cloud.google.com/run/?hl=en), a serverless engine that runs our Fast API endpoint with autoscaling. Follow the on-screen instructions and pick a name for your application. For this instance, we chose the name gptdoc.
```python
gcloud run deploy
```

To test your application in the cloud you can issue the following command to get a similar response to what we received when we did our local testing. Be sure to replace https://gptdoc-fbpcgd5sua-uw.a.run.app/vertexai-ddx-icd10-cpt/invoke with your endpoint and alter the “text” field to anything else the doctor might be curious about.

```python
curl -X 'POST' \
  'https://gptdoc-fbpcgd5sua-uw.a.run.app/vertexai-ddx-icd10-cpt/invoke' \
  -H 'accept: application/json' \
  -H 'Content-Type: application/json' \
  -d '{
  "input": {
    "text": "stomach pain with blood in stool"
  },
  "config": {},
  "kwargs": {}
}'
```

<img src="/assets/images/langserve-cloud.gif"/>

We can also check our container on Cloud Run to see the HTTP 200 status to confirm a successful response was generated from our service.

<img src="/assets/images/langserve-cloudrun.gif"/>

## Next Steps

Create your own [chain](https://github.com/langchain-ai/langserve-launch-example?ref=blog.langchain.dev) using [langserve](https://www.langchain.com/langserve) to easily deploy your use cases - chatbots, retrieval agents, etc. - in a scalable fashion.


The [original article](https://cloud.google.com/blog/products/ai-machine-learning/deploy-langchain-on-cloud-run-with-langserve/) on Google Cloud is co-authored by the Founding Engineer of LangChain, a very popular framework that makes it seamless to build apps with large language models (LLMs). In this post we will explore the quickest way to deploy a doctor’s assistant that can answer questions about possible differential diagnoses, ICD10 and CPT codes that a doctor might need to consider given the patient is coming in with a particular set of chief complaints.

## Model

We will leverage Google’s Vertex AI PaLM2 model for the chat model and provide this conversationally fine-tuned model with the right prompt to assist our doctor. A couple of things to note about this model are that its output is limited to 1024 tokens ([source](https://console.cloud.google.com/vertex-ai/publishers/google/model-garden/chat-bison?_ga=2.27439484.1757065834.1701892822-45235083.1701744860&project=valiant-memory-307005)) and the [use policy](https://policies.google.com/terms/generative-ai/use-policy) dictates that the end user must not use it to automate decisions in healthcare. Healthcare policy is still an evolving landscape and there is a line that can be navigated between augmenting practitioner intelligence versus fully automating the task.  

## Serve as an API

### Set up Google Cloud Project
#### Set default project
```python
gcloud config set project [PROJECT-ID]
```

#### Enable Vertex AI
```python
gcloud services enable aiplatform.googleapis.com
```

#### Call PaLM LLM from localhost
```python
gcloud auth application-default login
```

### Set up LangChain
#### Install langchain CLI
```python
pip install langchain-cli
```

The command below will create a production-ready API using LangServe. We will borrow the skeleton of the vertexai-chuck-norris package.
```python
langchain app new my-demo --package vertexai-chuck-norris
```

The original chuck-norris package tells you a joke about Chuck Norris and anything else the user enters as a prompt. Instead of a joke, we will update the model to respond with relevant information to assist the doctor in diagnosing.

#### OLD:
```python
_prompt = ChatPromptTemplate.from_template(

   "Tell me a joke about Chuck Norris and : {text}"

)
```

#### NEW:
```python
_prompt = ChatPromptTemplate.from_template(

   "Tell me the DDx, ICD10, and CPT codes for the following chief complaint: {text}"

)
```

### Local Testing

Change into the my-demo directory and deploy the app on localhost:
```python
langchain serve
```

<img src="/assets/images/langserve.gif"/>

### Deployment

Now we go from local deployment to [deploying](https://github.com/langchain-ai/langserve/tree/main?ref=blog.langchain.dev#deploy-to-gcp) our application as an HTTPS endpoint on [Cloud Run](https://cloud.google.com/run/?hl=en), a serverless engine that runs our Fast API endpoint with autoscaling. Follow the on-screen instructions and pick a name for your application. For this instance, we chose the name gptdoc.
```python
gcloud run deploy
```

To test your application in the cloud you can issue the following command to get a similar response to what we received when we did our local testing. Be sure to replace https://gptdoc-fbpcgd5sua-uw.a.run.app/vertexai-ddx-icd10-cpt/invoke with your endpoint and alter the “text” field to anything else the doctor might be curious about.

```python
curl -X 'POST' \
  'https://gptdoc-fbpcgd5sua-uw.a.run.app/vertexai-ddx-icd10-cpt/invoke' \
  -H 'accept: application/json' \
  -H 'Content-Type: application/json' \
  -d '{
  "input": {
    "text": "stomach pain with blood in stool"
  },
  "config": {},
  "kwargs": {}
}'
```

<img src="/assets/images/langserve-cloud.gif"/>

We can also check our container on Cloud Run to see the HTTP 200 status to confirm a successful response was generated from our service.

<img src="/assets/images/langserve-cloudrun.gif"/>

## Next Steps

Create your own [chain](https://github.com/langchain-ai/langserve-launch-example?ref=blog.langchain.dev) using [langserve](https://www.langchain.com/langserve) to easily deploy your use cases - chatbots, retrieval agents, etc. - in a scalable fashion.


The [original article](https://cloud.google.com/blog/products/ai-machine-learning/deploy-langchain-on-cloud-run-with-langserve/) on Google Cloud is co-authored by the Founding Engineer of LangChain, a very popular framework that makes it seamless to build apps with large language models (LLMs). In this post we will explore the quickest way to deploy a doctor’s assistant that can answer questions about possible differential diagnoses, ICD10 and CPT codes that a doctor might need to consider given the patient is coming in with a particular set of chief complaints.

## Model

We will leverage Google’s Vertex AI PaLM2 model for the chat model and provide this conversationally fine-tuned model with the right prompt to assist our doctor. A couple of things to note about this model are that its output is limited to 1024 tokens ([source](https://console.cloud.google.com/vertex-ai/publishers/google/model-garden/chat-bison?_ga=2.27439484.1757065834.1701892822-45235083.1701744860&project=valiant-memory-307005)) and the [use policy](https://policies.google.com/terms/generative-ai/use-policy) dictates that the end user must not use it to automate decisions in healthcare. Healthcare policy is still an evolving landscape and there is a line that can be navigated between augmenting practitioner intelligence versus fully automating the task.  

## Serve as an API

### Set up Google Cloud Project
#### Set default project
```python
gcloud config set project [PROJECT-ID]
```

#### Enable Vertex AI
```python
gcloud services enable aiplatform.googleapis.com
```

#### Call PaLM LLM from localhost
```python
gcloud auth application-default login
```

### Set up LangChain
#### Install langchain CLI
```python
pip install langchain-cli
```

The command below will create a production-ready API using LangServe. We will borrow the skeleton of the vertexai-chuck-norris package.
```python
langchain app new my-demo --package vertexai-chuck-norris
```

The original chuck-norris package tells you a joke about Chuck Norris and anything else the user enters as a prompt. Instead of a joke, we will update the model to respond with relevant information to assist the doctor in diagnosing.

#### OLD:
```python
_prompt = ChatPromptTemplate.from_template(

   "Tell me a joke about Chuck Norris and : {text}"

)
```

#### NEW:
```python
_prompt = ChatPromptTemplate.from_template(

   "Tell me the DDx, ICD10, and CPT codes for the following chief complaint: {text}"

)
```

### Local Testing

Change into the my-demo directory and deploy the app on localhost:
```python
langchain serve
```

<img src="/assets/images/langserve.gif"/>

### Deployment

Now we go from local deployment to [deploying](https://github.com/langchain-ai/langserve/tree/main?ref=blog.langchain.dev#deploy-to-gcp) our application as an HTTPS endpoint on [Cloud Run](https://cloud.google.com/run/?hl=en), a serverless engine that runs our Fast API endpoint with autoscaling. Follow the on-screen instructions and pick a name for your application. For this instance, we chose the name gptdoc.
```python
gcloud run deploy
```

To test your application in the cloud you can issue the following command to get a similar response to what we received when we did our local testing. Be sure to replace https://gptdoc-fbpcgd5sua-uw.a.run.app/vertexai-ddx-icd10-cpt/invoke with your endpoint and alter the “text” field to anything else the doctor might be curious about.

```python
curl -X 'POST' \
  'https://gptdoc-fbpcgd5sua-uw.a.run.app/vertexai-ddx-icd10-cpt/invoke' \
  -H 'accept: application/json' \
  -H 'Content-Type: application/json' \
  -d '{
  "input": {
    "text": "stomach pain with blood in stool"
  },
  "config": {},
  "kwargs": {}
}'
```

<img src="/assets/images/langserve-cloud.gif"/>

We can also check our container on Cloud Run to see the HTTP 200 status to confirm a successful response was generated from our service.

<img src="/assets/images/langserve-cloudrun.gif"/>

## Next Steps

Create your own [chain](https://github.com/langchain-ai/langserve-launch-example?ref=blog.langchain.dev) using [langserve](https://www.langchain.com/langserve) to easily deploy your use cases - chatbots, retrieval agents, etc. - in a scalable fashion.


The [original article](https://cloud.google.com/blog/products/ai-machine-learning/deploy-langchain-on-cloud-run-with-langserve/) on Google Cloud is co-authored by the Founding Engineer of LangChain, a very popular framework that makes it seamless to build apps with large language models (LLMs). In this post we will explore the quickest way to deploy a doctor’s assistant that can answer questions about possible differential diagnoses, ICD10 and CPT codes that a doctor might need to consider given the patient is coming in with a particular set of chief complaints.

## Model

We will leverage Google’s Vertex AI PaLM2 model for the chat model and provide this conversationally fine-tuned model with the right prompt to assist our doctor. A couple of things to note about this model are that its output is limited to 1024 tokens ([source](https://console.cloud.google.com/vertex-ai/publishers/google/model-garden/chat-bison?_ga=2.27439484.1757065834.1701892822-45235083.1701744860&project=valiant-memory-307005)) and the [use policy](https://policies.google.com/terms/generative-ai/use-policy) dictates that the end user must not use it to automate decisions in healthcare. Healthcare policy is still an evolving landscape and there is a line that can be navigated between augmenting practitioner intelligence versus fully automating the task.  

## Serve as an API

### Set up Google Cloud Project
#### Set default project
```python
gcloud config set project [PROJECT-ID]
```

#### Enable Vertex AI
```python
gcloud services enable aiplatform.googleapis.com
```

#### Call PaLM LLM from localhost
```python
gcloud auth application-default login
```

### Set up LangChain
#### Install langchain CLI
```python
pip install langchain-cli
```

The command below will create a production-ready API using LangServe. We will borrow the skeleton of the vertexai-chuck-norris package.
```python
langchain app new my-demo --package vertexai-chuck-norris
```

The original chuck-norris package tells you a joke about Chuck Norris and anything else the user enters as a prompt. Instead of a joke, we will update the model to respond with relevant information to assist the doctor in diagnosing.

#### OLD:
```python
_prompt = ChatPromptTemplate.from_template(

   "Tell me a joke about Chuck Norris and : {text}"

)
```

#### NEW:
```python
_prompt = ChatPromptTemplate.from_template(

   "Tell me the DDx, ICD10, and CPT codes for the following chief complaint: {text}"

)
```

### Local Testing

Change into the my-demo directory and deploy the app on localhost:
```python
langchain serve
```

<img src="/assets/images/langserve.gif"/>

### Deployment

Now we go from local deployment to [deploying](https://github.com/langchain-ai/langserve/tree/main?ref=blog.langchain.dev#deploy-to-gcp) our application as an HTTPS endpoint on [Cloud Run](https://cloud.google.com/run/?hl=en), a serverless engine that runs our Fast API endpoint with autoscaling. Follow the on-screen instructions and pick a name for your application. For this instance, we chose the name gptdoc.
```python
gcloud run deploy
```

To test your application in the cloud you can issue the following command to get a similar response to what we received when we did our local testing. Be sure to replace https://gptdoc-fbpcgd5sua-uw.a.run.app/vertexai-ddx-icd10-cpt/invoke with your endpoint and alter the “text” field to anything else the doctor might be curious about.

```python
curl -X 'POST' \
  'https://gptdoc-fbpcgd5sua-uw.a.run.app/vertexai-ddx-icd10-cpt/invoke' \
  -H 'accept: application/json' \
  -H 'Content-Type: application/json' \
  -d '{
  "input": {
    "text": "stomach pain with blood in stool"
  },
  "config": {},
  "kwargs": {}
}'
```

<img src="/assets/images/langserve-cloud.gif"/>

We can also check our container on Cloud Run to see the HTTP 200 status to confirm a successful response was generated from our service.

<img src="/assets/images/langserve-cloudrun.gif"/>

## Next Steps

Create your own [chain](https://github.com/langchain-ai/langserve-launch-example?ref=blog.langchain.dev) using [langserve](https://www.langchain.com/langserve) to easily deploy your use cases - chatbots, retrieval agents, etc. - in a scalable fashion.


**Full code**: [Github](https://github.com/hacheemaster/) 

