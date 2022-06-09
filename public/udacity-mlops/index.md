# [Udacity] MLOps


# Udacity Program
{{< figure src="mlops.png" title="MLOps" >}}

I participated [Machine Learning DevOps Engineer program in Udacity](https://www.udacity.com/course/machine-learning-dev-ops-engineer-nanodegree--nd0821) ([Certification](https://confirm.udacity.com/K6YJFENE)). 

The program was extraordinary great, providing handful hands-on projects with the detailed and accurate human feedback. MLOps was whole different world and opened my eyes to the world of DevOps and machine learning in production level. 

The program is consisted of four projects, and each project covered different technology skills and tools. The most important thing the program emphasized was clean code principes, such as refactoring, handling errors, unit-testing, logging, and addressing. 


---

## Projects
1. Predicting Customer Churn
2. Airbnb Rent prices in NYC
3. Income prediction based on Census data
4. Dynamic Risk Assessment System
---


### 1. Predicting Customer Churn [(link)](https://github.com/youheekil/predict_customer_churn)

We identified and predicted credit card customers that are most likely to churn, the percentage of service subscribers who discontinue their subscriptions within a given time period. The main objective of the project was to build a customer churn prediction model and implement all the clean code principles. 

* `Process`
    * EDA
    * Feature Engineering (including encoding of categorical variables)
    * Model Training 
    * Prediction 
    * Model Validation

* `Technology Skills/Tools`
    * Pytest
    * Pylint
    * AutoPEP8
    * Logging and Error Handling 

### 2. Airbnb Rent Prices in NYC [(link)](https://github.com/youheekil/nyc_airbnb_prediction)

In this project, we built machine learning pipeline by using `MLflow` and `Wandb`. We estimated the typical prices for a given property based on the price of similar properties. The company receives new data in bulk every week. The model needed to be retrained with the same cadence, necessitating an end-to-end pipeline that can be reused.  

* `Process`
    * Create Conda Environment
    * Logging into Weights &amp; Biases (API key, authorization)
    * Use Cookiecutter Tool for the creation of steps required by the ML pipeline 
    * EDA
    * Data Cleaning
    * Data Testing 
    * Data Splitting
    * Train Model (Random Forest)
    * Hyperparameters Tuning
    * Best Model Selection 
    * Testing 
    * Visualize the pipeline
    * Release the pipeline
    * Train the model on a new data sample
    * Release on Github

* `Technology Skills/Tools`
    * Weights &amp; Biases
    * MLflow
    * Cookiecutter


### 3. Income prediction based on Census data [(link)](https://github.com/youheekil/predict_income)

We created and developed income prediction classification model on Census Bureau data by monitoring the model performance. Lastly, we deployed the model using the FastAPI on Heroku and created API tests for it. This whole procedure was incorporated into a CI/CD framework using Github Actions. 

There were many takeaways in this project, (1) model performance testing, (2) Data Version Control (DVC) as a remote storage and pipeline functionality performance (creating and running pipelines on dvc), (3) storing model in AWS S3 Bucket, (4) FastAPI, (5) deploying machine learning model on Heroku, (6) Github Actions. 

This project was the most difficult but the most enoyable one. 

* `Process`
    * Initialize Git and DVC and Connect Git repository to Github
    * Set up AWS S3 Bucket from the AWS CLI ( create IAM user with the appropriate permissions) [here](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_users_create.html#id_users_create_console)
    * Set up Github Actions - pytest, flaske8, python version, AWS credentials, DVC
    * Commiting raw and transformed data on DVC remote storage pointing to the S3 Bucket. 
    * Train Model 
    * Unit Testing the trainig model
    * Creating a RESTful API using FastAPI
    * API Deployment on Heroku

* `Technology Skills/Tools`
    * CI/CD
    * DVC
    * AWS S3 Bucket
    * FastAPI
    * Github Action
    * Heroku



### 4. Dynamic Risk Assessment System [(link)](https://github.com/youheekil/dynamic_risk_assessment_system)

We created, deployed, and monitored a risk assessment ML model that will estimate the attrition risk of each of the company's 10,000 clients. The created API will return the model predictions, f1 score, summary statistics, and model diagnostics data (model metrics). Based on the model we created and deployed, the client managers will contact the clients with the highest risk and avoid losing clients and revenue. This entire process is automated and 

* `Process`
    * Data Ingestion
    * Training, Scoring and Deploying an ML Model 
    * Diagnostics - model predictions, summary statistics, check missing data, performance timing, update dependencies.
    * Create Model Reporting
    * Process Automation - the model re-deployment 
    * Croning job for the full pipeline - run the `fullprocess.py` script one time every 10 min.

* `Technology Skills/Tools`
    * ML Pipeline Automation
    * Cronjob
    * Flask 
