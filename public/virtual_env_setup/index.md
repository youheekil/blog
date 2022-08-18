# [Python] Three different Virtual Environments Setup (Mac) - Step By Step (conda, pip, venv)


Setting up the virtual environment is especially **crucial** for machine learning projects (data science projects) because of the number of package modules and different versions. 

There are three ways of creating a virtual environment setup in Python. In this post, three different ways of virtual environments will be introduced step by step, `conda`, `pip`, `venv`!

I prefer using `conda` and `venv`.  



{{< figure src="python-virtual-env.png" title="Python Virtual Environment" >}}



## Conda Environment Set up

Download and install `conda` if you don’t have it already.

### 1. Create `get.sh` 
        
```bash
# get.sh
wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh
```
        
### 2. Run `get.sh` file & `Miniconda3-latest-Linux-x86_64.sh`
        
```bash
# got checking contents of get.sh file 
cat get.sh
bash get.sh 
bash Miniconda3-latest-Linux-x86_64.sh
```

**Type ‘yes’**
        
### 3. Then set the workspace path
    
```bash
{pwd}/miniconda3
```
    
> Make sure to set the path of workplace after checking current working directory in another terminal using `pwd` command

    
### 4. Modify `.gitgnore` file
    
```
@ .gitgnore file
     
# conda for gitpod env
anaconda3
miniconda3
```
    
> After pushing it to git, remove two files - (1)`get.sh`, (2)`Miniconda3-latest-Linux-x86_64.sh`

### 5. Create Environment 
**[Option 1]**
    
Then create a new environment using code and activate it: 
    
```bash
conda create -n <environment_name> "python=3.8" scikit-learn dvc pandas numpy pytest jupyter jupyterlab fastapi uvicorn -c conda-forge dvc-s3 sklearn

conda activate <environment_name>
```
    

**[Option 2]**

create a new environment using the `environment.yml`file provided in the root of the repository and activate it:

```yml
name: <environment_name>
channels:
  - conda-forge
  - defaults
dependencies:
  - python=3.8
  - pip
  - pandas
  - matplotlib
  - scikit-learn
  - pip:
    - lightgbm
```

```bash
conda env create -f environment.yml
conda activate <environment_name>
```

If you haven't installed git, please install git through `conda`

```bash
conda install git 
```

### 6. Packaging a conda environment with `conda-pack`
```bash
# install conda-pack 
conda install -c conda-forge conda-pack

# Activate the desired environment
conda activate environment_name

# set the path to desired directory to save the package 
cd path/to/desired/directory.

# pack your environment (it might take few minutes)
conda pack

# environment is packaged as a tar.gz file 
ls
```

### 7. Unpackaging a conda environment with `conda-pack`
```bash
# create a folder for the environment
mkdir <environment_folder>

# Uncompress the environment in the folder
tar xzvf <environment_name>.tar.gz -C <environment_folder>

# check the folder
ls

# Activate the environment
source <environment_name>/bin/activate

# Deactivate the environment 
source <environment_name>/bin/deactivate
```


## pip Environment Set up

### 1. Create a virtual environment 
```bash
python -m <virtual environment name>
```
### 2. Activate this virtual environment 

```bash
source <virtual environment name>/bin/activate
```
after doing this, you may notice the prompt adds the name of the virtual environment 

```bash
(<virtual environment name>)$
```

### 3. Installing list of packages in requirements.txt
```bash
pip install -r requirements.txt
```

### 4. Useful command 
* show a list of all installed python modules
```bash
pip freeze
```

* show a list containing only the outdated modules
```bash
pip list —outdated
```

* provides a list of installed modules in “requirements.format”
```bash
pip list
```
* provide specific information about the pandas module
```bash
pip show pandas
```
* install the pandas module
```bash
pip install pandas
```
* run pip using python
```bash
python -m pip list
```
## venv Environment Set up
### 1. Create a virtual environment
    
```bash
python -m venv <name-of-your-new-virtual-environment>
```
    
### 2. Activate this virtual environment
    
```bash
source <name-of-your-new-virtual-environment>/bin/activate
```
    
After doing this, you may notice the prompt adds the name of the virtual environment
    
```bash
(<name-of-your-new-virtual-environment>)$
```
    
### 3. Installing list of packages in `requirements.txt`
    
```bash
pip install -r requirements.txt
```   
     
### 4. Useful command 

* show a list of all installed python modules and create `requirements.txt`
```bash
pip3 freeze > requirements.txt
```
* show a list containing only the outdated modules

```bash
pip3 list —outdated
```
* provides a list of installed modules in “requirements.format”
```bash
pip3 list
```

* provide specific information about the pandas module

```bash
pip3 show pandas
```

* install the pandas module
```bash
pip3 install pandas
```
* deactivate the environment and delete the envrionment
```bash
deactivate

rm -rf <name-of-your-new-virtual-environment>
```
