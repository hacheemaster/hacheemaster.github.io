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
* Bivariate Analysis
4. **Results & Insights**:
* Conversion rate dropped on March 1st, 2015 from the home page to the search page for Mobile users.

# Problem Statement

We are working with data from an e-commerce website. The site is very simple and has just 4 pages. The first page is the home page. When you come to the site for the first time, you can land on the home page. From the home page, the user can perform a search and land on the search page. From the search page, if the user clicks on a product, she will get to the payment page, where she is asked to provide payment information in order to buy that product. If she does decide to buy, she ends up on the confirmation page

The company CEO isn't very happy with the volume of sales and, especially, of sales coming from new users. Therefore, we are tasked with investigating whether there is something wrong in the conversion funnel or, in general, if we can suggest how the conversion rate can be improved.

# Data

We will be using the [E-commerce Website Data Set](https://www.kaggle.com/aerodinamicc/ecommerce-website-funnel-analysis) freely avaiable on the Kaggle website.

For every page, the respective csv contains a list of user_id's that visited that page and a categorical column called page. The user table contains information about the date the user accessed the website as well as the gender and device used.

# Exploratory Analysis

<img src="/assets/images/conversion_sankey.png"/>

# Results & Insights



**Full code**: [Github](https://github.com/hacheemaster/E-commerce_website_conversion_funnel_analysis/blob/master/Conversion%20funnel%20analysis%20for%20e-commerce%20website.ipynb)
