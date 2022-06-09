# Data Engineer Roadmap

## Data Engineer Roadmap 

{{< typeit tag=h4 >}}
This awesome roadmap is provided by [datastacktv](https://github.com/datastacktv/data-engineer-roadmap)!
{{< /typeit >}}


> The roadmap aims to give a complete picture of the modern data engineering landscape and serve as a study guide for aspiring data engineers.

Based on the provided Data Engineer Roadmap, I would like to dig into what we need to know to be a `data engineer`.  
{{< figure src="DE roadmap.png" title="Data Engineer Roadmap" >}}

## Section

### 1. CS fundamentals 
* Basic terminal usage 
* Data structures & algorithms 
* [APIs](https://www.youtube.com/watch?v=GZvSYJDk-us) 
* REST 
* Structured vs unstructured data 
* Linux 
    - CLI 
    - Vim
    - Shell scripting
    - Cronjobs
* OS 
    - How does the computer work?
    - How does the Internet work? 
* Git — Version control 
* Math & statistics basics 

### 2. Programming Language 
The Most popular programming languages are 
* Python :(fa-solid fa-star):
* Java
* Scala
* R

> Especially, `Python`, `Java`, `Scala` are the main languages for Data Engineer since these languages support Spark for big data processing. `Python` and `R` are highly recommended for aspiring Data Scientist,Data Analyst. 

### 3. Testing 
> Understanding `TDD` is very important. 
* Unit Test
* Integration Test
* Functional Test

### 4. Database Fundamentals 
> SQL is very important. Understand the `Entity-Relationship (ER) modelling` and `Normalization`. How to design databases and model data are important as well. Understand `scalling pattern`. 

* CAP theorem 
* OLAP vs OLAP
* Horizontal vs Vertical Scaling
* RDB vs No SQL
* Normalization 
* Dimental Modelling

### 5. Relaitonal Database (RDB) fa-star):


* MySQL 
* PostgreSQL

### 6. Non-relational Database (No SQL) :

> Understand the difference between Document, Wide Column, Graph and key-value. Recommended to master one database from each category. There are different types of DB like Documnet DB, Key-Value style DB 
* Understaning Pro and Cons of No SQL
* Key-Value (DynamoDB, Redis)
* Document (MongoDB, Elasticsearch)
* Wide Column (Cassandra, HBase)

### 7. Data Warehouse 
* Snowflake
* AWS Redshift
* Google BigQuery
* Azure Synapse Analytics

### 8. Object Storage 

* AWS S3
* Azure Blob Storage
* Google Cloud Storage

### 9. Cluster Computing Fundamentals 
> Modern data processing frameworks are based on Apache Hadoop and MapReduce. Understanding these will help you learn modern fraeworks faster.  Big Data, Cluster Computing, Distributed Computing.

* Hadoop
* HDFS
* MapReduce
* Managed Hadoop
* Managed Hadoop - Azure Data Lake / Google Dataproc / Amazon EMR

### 10. Data Processing
* Batch - data build tool 
* Bybrid - Batch + Streaming (Spark, Flink)
* Streaming (Kafka, storm)

### 11. Messaging
* Google PubSub
* Azure Service Bus 
* Rabbit MQ 

### 12. Workflow Scheduling 

* Apache Airflow
* Google Composer

### 13. Monitoring Data Pipelines
* Datadog

### 14. Networking 

* Protocols (HTTP, HTTPS, TCP, SSH, IP, DNS)
* Firewalls
* VPN
* VPC

### 15. Infrastructure as Code
* Docker - container 
* Kubernetes - container orchestration 
* CDK - Infrastrue provisioning 
* Terraform - Infrastrue provisioning 

### 16. CI/CD

* Github Actions
* Jenkinds

### 17. Identity and Access Management

* AWS IAM
* Active Directory
* Azure Active Directory

### 18. Data Security and Privacy 

* Legal Compliance
* Encryption
* Key Management
* Data Governance & Integrity 


## Reference 
* https://github.com/datastacktv/data-engineer-roadmap
