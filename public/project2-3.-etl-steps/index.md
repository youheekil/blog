# [SongPlz-Bot] 3. ETL Steps


## Steps
### 1. Set up virtual environment
```bash 
where python # find the location of python 
pip install pipenv # install pipenv which is virtual environment
pip list
mkdir <project folder> && cd <project folder>

# the ending of the path would be `/python.exe` 
pipenv shell --python <the path of python version you want to use> 
pip list # list of packages 
pipenv --venv # path to version environment 

pipenv install pandas 
pip list # check if pandas is installed 

# start the created virtual env
pipenv shell
```

### 2. Set up AWS

#### IAM
save the csv file of aws access key and secret key safely. 
```shell
pipenv install awscli
```


### 3. Understanding the source data using Jupyter Notebook

```shell
pipenv install jupyter
pipenv install boto3
jupyter notebook
```

```python
import boto3
import pandas as pd
from io import StringIO

s3 = boto3.resource('s3')
bucket = s3.bucket('spotify_artists')
bucket 
```
4. Quick and dirty solution using Jupyter Notebook
5. Functional vs. Object Oriented Programming (OOP)
6. Importance of testing
7. Functional approach with the quick and dirty solution
8. OOP Design principles and further requirements – Configuration, Logging, Meta Data
9. OOP Code Design
10. Set up development environment (Github, Python project, VSC)
11. Implement class frame
12. Implement Logging
13. Coding (Clean Code, functionality, linting, unit tests, integration tests)
14. Set up dependency management with pipenv
15. Performance tuning with profiling and timing
16. Create Dockerfile + push docker image to Docker Hub
17. Run application in production using a Minikube and Argo Workflows
