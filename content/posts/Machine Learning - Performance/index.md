---
title: "[Machine Learning] Performance"
date: 2022-07-26
draft: true
author: Youhee
images: []
categories: ["Machine Learning"]

lightgallery: true

toc:
  auto: false
math:
  enable: true
---
## Regression Metrics

In regression, metrics were used **to evaluate how close the predictions were to the expected values and report the performance of a regression model**. The most common regression metrics are `MSE`, `RMSE`, `MAE`. 


* Mean Squared Error (MSE)

* Root Mean Sqaured Error (RMSE)

* Mean Absolute Error (MAE)

As you could notice, all the metrics name have *error* in it. **Error** in regression metrics is important point. Error indicates and summarizes on average exactly how close the predictions were the expected values. 

### Mean Squared Error
$$ MSE = \frac{1}{N}\sum_i^N{(y_i – \hat{y}_i)}^2 $$ 

where $y_i$ is the $i^{th}$ expected value and $\hat{y}_i$ is the $i^{th}$ predicted value in the dataset. Therefore, MSE is the average  of the square of the difference between actual and predicted value in the dataset. 

## Classification Metrics 
- accuracy 


On the other hand, classification metrics are different from regression metrics for Machine Learning. 

## Reference 
* https://machinelearningmastery.com/regression-metrics-for-machine-learning/
* https://scikit-learn.org/stable/modules/classes.html#regression-metrics