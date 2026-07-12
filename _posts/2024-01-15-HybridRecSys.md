---
title: "Building a Hybrid Recommendation Engine using Python"
date: 2024-01-15
redirect_from:
  - /hybridrecsys/
tags: [Recommendation System, Machine Learning, Python, Content-based filtering, Matrix Factorization]
categories:
  - Machine Learning
#header:
#  image: "/assets/images/activityrecognition.gif"
excerpt: "Building a hybrid recommendation engine in Python that blends matrix factorization with content-based filtering for personalized, cold-start-resilient recs."
---


Neither collaborative filtering nor content-based filtering is sufficient on its own: one struggles with brand-new items, the other ignores the signal in collective user behavior. In this post we build a hybrid recommendation engine in Python that integrates the strengths of matrix factorization and content-based filtering, so the system stays personalized while still handling items it has never seen interacted with.

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
