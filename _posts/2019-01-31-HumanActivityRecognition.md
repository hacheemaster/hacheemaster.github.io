---
title: "Human Action Recognition using Deep Learning"
date: 2019-01-31
tags: [Deep Learning, smartphones, action recognition]
#header:
#  image: "/assets/images/activityrecognition.jpg"
excerpt: "Deep Learning, Human Action Recognition, Smartphones"
---

# Summary 

1. **Problem Statement**: Use smartphone data to classify six human actions (walking, walking upstairs, walking downstairs, sitting, standing, laying).
2. **Data**: Human Activity Recognition smartphone dataset: [UCI HAR Dataset](https://archive.ics.uci.edu/ml/machine-learning-databases/00240/UCI%20HAR%20Dataset.zip)
3. **Approaches**: 
* LSTM
* 1D CNN

4. **Key Insights**:
* Due to the stochastic nature of neural networks, repeat the experiment multiple times in order to get a stable estimate of a particular architecture's accuracy.
* 1D CNNs are faster to train and test. Hence, they would serve better in production

# Problem Statement: Human Action Recognition Using Smartphone Data

Human Action Recogntion aims to determine human activities given monitoring data from systems such as body sensors or videos. The process usually involves noise filtering the data and extracting pre-defined time series features. The problem with this approach is it requires domain expertise.

An alternative approach would be to use neural network architectures that can serve as feature extractors, thus eliminating the need for feature engineering. In this tutorial, you will learn LSTMs and how they  

It has multiple applications from research into fall risk patients to surveillance systems.



The main goal of this post is to analyze monitoring data provided by a smartphone.

# Data: Smartphone Dataset

The data we'll be wrangling with today is the ['Human Activity Recognition Using Smartphones Data Set'](https://archive.ics.uci.edu/ml/machine-learning-databases/00240/UCI%20HAR%20Dataset.zip) made freely available on the UCI Machine Learning Repository.





