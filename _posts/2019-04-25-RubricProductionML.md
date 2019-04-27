---
title: "A checklist for ML Production Systems"
date: 2019-04-25
tags: [Machine Learning, Production, Model Monitoring]
#header:
#  image: "/assets/images/activityrecognition.gif"
excerpt: "Model Monitoring, Production"
---

This article summarizies the production pipeline scoring rubric provided by Google Inc. in ["What's your ML Test Score? A rubric for ML production systems"](https://ai.google/research/pubs/pub45742) and places it in a csv file.

Machine Learners at Google specify four categories to test: Data, Model, Infrastructure, and Monitoring. For each category, one point is awarded for executing the test manually. A second point is awarded for automation of the testing. The final ML Test Score is the __minimum__ of the scores aggregated across each of the four categories. 

• 0 points: Research product
• 1-2 points: Serious holes in reliability
• 3-4 points: There’s been first pass at basic productionization, but additional investment may
be needed.
• 5-6 points: Reasonably tested, but it’s possible that more of those tests and procedures may
be automated.
• 7-10 points: Strong levels of automated testing and monitoring, appropriate for mission-
critical systems.
• 12+ points: Exceptional levels of automated testing and monitoring.

# Data

| Test Type | Description | Sample Test |
| --- | --- | --- |
| Data | Distribution of feature matches expectation | PSI, KS |
| Data | Test the relationship between each feature and the target, and the pariwise correlations between individual signals | Correlation coefficient |

# Model

# Infrastructure

# Monitoring

  

**Full code**: [Github](https://github.com/hacheemaster/RubricProductionML) 

