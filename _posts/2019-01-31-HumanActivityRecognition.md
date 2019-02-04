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
3. **Methods**: 
* LSTM
* 1D CNN
4. **Results & Insights**:
* Due to the stochastic nature of neural networks, repeat the experiment multiple times in order to get a stable estimate of a particular architecture's accuracy.
* 1D CNNs are faster to train and test. They would serve better in production.

# Problem Statement

Human Action Recogntion aims to determine human activities given monitoring data from systems such as body sensors or videos. The process usually involves noise filtering the data and extracting pre-defined time series features. The problem with this approach is it requires domain expertise.

An alternative approach would be to use neural network architectures that can serve as feature extractors, thus eliminating the need for feature engineering. In this tutorial, you will learn LSTMs and how they stack up against 1-D CNNs.

Human Action Recognition has multiple applications from research into fall risk patients to surveillance systems. The main goal of this post is to classify six human actions (walking, walking upstairs, walking downstairs, sitting, standing, laying) based on time series data provided by a smartphone. 

# Data

We will be wrangling with the [Human Activity Recognition Using Smartphones Data Set](https://archive.ics.uci.edu/ml/machine-learning-databases/00240/UCI%20HAR%20Dataset.zip) freely available in the UCI Machine Learning Repository.

The experiment was carried out by 30 participants. Each person performed six activities (WALKING, WALKING_UPSTAIRS, WALKING_DOWNSTAIRS, SITTING, STANDING, LAYING) wearing a smartphone (Samsung Galaxy S II) on the waist. Using its embedded accelerometer and gyroscope, the authors captured 3-axial linear acceleration and 3-axial angular velocity at a constant rate of 50Hz (50 samples/sec). 

The sensor signals were pre-processed by applying noise filters and then sampled in fixed-width sliding windows of 2.56 sec and 50% overlap (128 readings/window). From each window, a vector of features was obtained (jerk, magnitude, etc.). However, we won't rely on this 561-feature vector that requires domain expertise to create. Instead, we'll let our neural network calculate the necessary features from the 6 original inertial signals:
* 3-axial signals for total acceleration (x,y,z)
* 3-axial signals for angular velocity (x,y,z)

Every row in the final dataset contains a 128 element vector with 6 signals. Therefore, each row contains 128*6, or 768 elements. The dataset has been randomly partitioned into two sets, where 70% of the volunteers were selected for generating the training data and 30% the test data. 

# Methods

# Results & Insights

