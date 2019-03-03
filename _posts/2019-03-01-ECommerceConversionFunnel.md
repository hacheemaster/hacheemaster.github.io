---
title: "E-commerce Website Customer Conversion Funnel Analysis"
date: 2019-03-02
tags: [Conversion Funnel, Visualization, Python]
#header:
#  image: "/assets/images/activityrecognition.gif"
excerpt: "Conversion Funnel, Visualization, Plotly"
---

# Agenda 

1. **Problem Statement**: Explore the conversion funnel of an e-commerce website in order to suggest ways of improving the conversion rate.
2. **Data**: E-commerce website visitor history: [Dataset](https://www.kaggle.com/aerodinamicc/ecommerce-website-funnel-analysis)
3. **Exploratory Analysis**: 
* Sankey Diagram Visualization
* Bivariate Exploratory Analysis
4. **Results & Insights**:
* Conversion rate dropped on March 1st, 2015 from the home page to the search page for Mobile users.

# Problem Statement

We are working with data from an e-commerce website. The site is very simple and has just 4 pages. The first page is the home page. When you come to the site for the first time, you can land on the home page. From the home page, the user can perform a search and land on the search page. From the search page, if the user clicks on a product, she will get to the payment page, where she is asked to provide payment information in order to buy that product. If she does decide to buy, she ends up on the confirmation page

The company CEO isn't very happy with the volume of sales and, especially, of sales coming from new users. Therefore, we are tasked with investigating whether there is something wrong in the conversion funnel or, in general, if we can suggest how the conversion rate can be improved.

# Data

We will be using the [E-commerce Website Data Set](https://www.kaggle.com/aerodinamicc/ecommerce-website-funnel-analysis) freely avaiable on the Kaggle website.

For every page, the respective csv contains a list of user_id's that visited that page and a categorical column called page. The user table contains information about the date the user accessed the website as well as the gender and device used.

# Exploratory Analysis

<img src="assets/images/conversion_sankey.png" alt="hi" class="inline"/>

# Results & Insights

From the table below we can see that the LSTM and CNN models are comparable in size. Both sit roughly around 1.5 MB. However, the LSTM model takes 4X longer to run! Moreover, it seems that the LSTM model is slightly overfitting the training data as shown by the drop in accuracy when moving from training to validating. Both models take a slight hit in terms of accuracy when trying to predict the test set, but a slight drop for unseen data is to be expected.

Model | Size (MB) | Runtime (X_train, 10 reps) | Accuracy (X_train) | Accuracy (X_val) | Accuracy (X_test) 
--- | --- | --- | --- | --- | ---
LSTM | 1.5 | 20.4s +/- 1s | 96% | 92% | 86%
CNN | 1.8 | 5.2s +/- .8s | 92% | 90% | 86% 

# Summary

In conclusion, when trying to perform human activity recognition using smart phone data, it's better to use one-dimensional Convolutional Neural Networks over LSTMs due to the longer runtime for LSTMs. 


**Full code**: [Github](https://github.com/hacheemaster/HumanActivityRecognition/blob/master/HAR_LSTMS.ipynb)
