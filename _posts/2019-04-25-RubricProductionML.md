---
title: "A checklist for ML Production Systems"
date: 2019-04-25
tags: [Machine Learning, Production, Model Monitoring]
#header:
#  image: "/assets/images/activityrecognition.gif"
excerpt: "Model Monitoring, Production"
---

This article summarizes the production pipeline scoring rubric provided by Google Inc. in ["What's your ML Test Score? A rubric for ML production systems"](https://ai.google/research/pubs/pub45742) and places it in a csv file. It can easily be used across projects to make sure they meet the minimum deployment threshold.

Machine Learners at Google specify four categories to test: Data, Model, Infrastructure, and Monitoring. 

# Data

| Test Type | Description | Sample Test |
| --- | --- | --- |
| Data | Distribution of feature matches expectation | PSI, KS |
| Data | Test the relationship between each feature and the target, and the pairwise correlations between individual signals | Correlation coefficient |
| Data | Test the cost of each feature and compare versus percent improvement | Inference latency, RAM usage, upstream data dependencies |
| Data | Model does not contain any features that have been manually determined as unsuitable | Spreadsheet of banned variables |
| Data | Test that your system maintains privacy controls across its entire data pipeline | |
| Data | Test the calendar time needed to develop and add a new feature to the production model| |
| Data | Test all code that creates input features, both in training and serving | Unit-testing using PSI |

# Model

| Test Type | Description | Sample Test |
| --- | --- | --- |
| Model | Test that every model specification undergoes a code review and is checked in to a repository | |
| Model | Test the relationship between model performance metrics (accuracy or AUC) and the actual business impact metrics (click-through rates) | This can be measured in a small scale A/B experiment using an intentionally degraded model to see the impact of a 1% improvement in performance on the business metric |
| Model | Test the impact of each tunable hyperparameter | Grid search or Bayesian Optimization for hyperparameter search |
| Model | Test the effect of model staleness | Compare predictions from last week/month/year |
| Model | Test against a simpler model as a baseline | Cost/benefit analysis against a linear (or equivalent) model with few features |
| Model | Test model quality on important data slices | Important slices might be users by country or by device |
| Model | Test the model for implicit bias |  |

# Infrastructure

| Test Type | Description| Sample Test |
| --- | --- | --- |
| Infrastructure | Test the reproducibility of training | Train two models on the same data, and observe any differences in aggregate metrics, sliced metrics, or example-by-example predictions |
| Infrastructure | Unit test model specification code | Useful assertions include testing that training results in decreased loss and that a model can restore from a checkpoint after a mid-training job crash |
| Infrastructure | Integration test the full ML pipeline | A good integration test runs all the way from original data sources, through feature creation, to training, and to serving. An integration test should run both continuously as well as with new releases of models or servers |
| Infrastructure | Test model quality before attempting to serve it | Useful tests include testing against data with known correct outputs and validating the aggregate quality, as well as comparing predictions to a previous version of the model |
| Infrastructure | Test that a single example or training batch can be sent to the model, and changes to internal state can be observed from training through to prediction | |
| Infrastructure | Test models via a canary process before they enter production serving environments | This includes testing that a model can be loaded into the production serving binaries and perform inference on production input data at all. It also includes a canary process, in which a new version is tested on a small trickle of live data |
| Infrastructure | Test how quickly and safely a model can be rolled back to a previous serving version | |

# Monitoring

| Test Type | Description| Sample Test |
| --- | --- | --- |
| Monitoring | Test for upstream instability in features, both in training and serving | As examples, what alert would fire if one datacenter stops sending data? What if an upstream signal provider did a major version upgrade? | 
| Monitoring | Test that data invariants hold in training and serving inputs | For example, test if Feature A and Feature B should always have the same number of non-zero values in each example, or that Feature C is always in the range (0, 100) or that class distribution is about 10:1 |
| Monitoring | Test that your training and serving features compute the same values | The codepaths that generate input features may differ for training and inference time, due to tradeoffs for flexibility vs. efficiency and other concerns. Make sure there is no “training/serving skew” |
| Monitoring | Test for model staleness | For models that continually update, this means monitoring staleness throughout the training pipeline |
| Monitoring | Test for NaNs or infinities appearing in your model during training or serving | |
| Monitoring | Test for dramatic or slow-leak regressions in training speed, serving latency, throughput, or RAM usage | Dramatic regressions and slow regressions over time may require different kinds of monitoring| 
| Monitoring | Test for regressions in prediction quality on served data | |

For each category above, one point is awarded for executing the test manually. A second point is awarded for automation of the testing. The final ML Test Score is the __minimum__ of the scores aggregated across each of the four categories. 

* 0 points: Research product
* 1-2 points: Serious holes in reliability
* 3-4 points: There’s been first pass at basic productionization, but additional investment may
be needed.
* 5-6 points: Reasonably tested, but it’s possible that more of those tests and procedures may
be automated.
* 7-10 points: Strong levels of automated testing and monitoring, appropriate for mission-
critical systems.
* 12+ points: Exceptional levels of automated testing and monitoring.


**Download CSV**: [Github](https://github.com/hacheemaster/RubricProductionML) 

