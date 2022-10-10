---
title: "[Machine Learning] Supervised Learning"
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

**Unsupervised Learning** refers an approach within maching learning that takes in **unlabeled** examples and produces patterns from the provided data. Typically, the goal is to discover something previously unknown about the unlabled examples. 

* Classification and regression are examples of supervised learning 

# Regression Algorithm 
predicts a number
- we tried to make the line as close to the data points as possible

Popular Regression Algorithms are

  1. Linear Regression
  2. Decision Tree
  3. Support Vector Regression
  4. Lasso Regression
  5. Random Forest Regression

# Classification Algorithm
predicts a category/label
- we use the line to separate data points of different color (classes)

Popular Classification Algorithms are

  1. Logistic Regression
  2. Naive Bayes
  3. K-Nearest Neighbors
  4. Decision Tree
  5. Support Vector 

## Naive Bayes
## KNN  
## Decision Tree
## Linear Regression
## Logistic Regression 


`Logistic Regression` (= Sigmoid function) classifies the input as a binary value of 0 or 1. It takes linear regression model ($y = \beta_1 x_1 +\beta_0$) and put it in the exponent in the following formula:

$$p(y|x) = \frac{1}{1 + e^{-(\beta_1 x_1 +\beta_0)}}$$

### Loss 
**Loss** represents how far off the model's prediction was from the true label. 

$Loss(\hat{y}, y)$ defines the difference in $\hat{y}$ (the model predicted) and y. 

$$Loss(\hat{y}, y) = [y\log{\hat{y}} + (1-y)\log{(1-y)}] $$

* cross-entropy loss = negative log loss 
then we minimzing loss function. 

## Support Vector Machine 

# Machine Learning Script 
1. Load in the data 
- a) X, Y
- b) Typically use `pandas` unless data is too complex
2. Split into train/test sets
Sometimes test and validation are used interchangeably, and the "true test set" is seomthing else
3. Build a model 
- a) OOP
- b) Tensorflow 2.0 standard is Keras API, very similar to Scikit-Learn
4. Fit the model (gradient descent)
- a) model.fit(X,Y)
5. Evaluate the model 
6. Make predictions 
- a) model.predict(X)


