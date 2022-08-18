# [Starbucks Twitter Sentiment Analysis] Instructions and Spark NLP

# Setup with Confluent Kafka, Spark, Delta Lake with Databricks and AWS

{{< figure src="Tiwtter_Sentimnet_Architecture.png" title="Project Final Diagram" >}}


## Instruction


In this post, we will set up environment to perform Starbucks Twitter Sentiment Analysis with Confluent Kafka, Spark, Delta Lake with Databricks and AWS. 



## Step 1. Twitter API Credentials

As we performed in the previous [post](https://youheekil.github.io/running-kafka-docker/), we need to get [Twitter API Credentials](https://developer.twitter.com/en/docs/twitter-api/getting-started/getting-access-to-the-twitter-api). After getting it, we save these credential information in `.env` file. 
Make sure to include `.env` file in `.gitignore` to be ignored in the future. 

```.env
# .env
CONSUMER_KEY = "<api key>"
CONSUMER_SECRET = "<api secret>"
ACCESS_TOKEN_KEY = "<access key>"
ACCESS_TOKEN_SECRET = "<access secret>"
```
## Step 2. Confluent Cloud

> Confluent Cloud is a resilient, scalable streaming data service based on Apache Kafka®, delivered as a fully managed service - [Confluent Cloud](https://docs.confluent.io/cloud/current/get-started/index.html). It offers users to manage cluster resources easily. 

### 2-1. Create a Confluent Cloud account and Kafka cluster
First, create a free Confluent Cloud account and create a kafka cluster in [Confluent Cloud](https://docs.confluent.io/cloud/current/get-started/index.html). I created a basic cluster which supports single zone availability with `aws` cloud provider.

### 2-2. Create a Kafka Topic named `tweet_data` with 2 partitions. 

From the navigation menu, click `Topics`, and in the Topics page, click `Create topic`. I set topic name as `tweet_data` with 2 partitions, the topic created on the Kafka cluster will be available for use by producers and consumers. 

## Step 3. Confluent Cloud API credentials. 

#### API keys
From the navigation menu, click `API keys` under `Data Integration`.  If there is no available `API Keys`, click `add key` to get a new API keys (API_KEY, API_SECRET) and make sure to save it somewhere safe. 


#### HOST: Bootstrap server 

From the navigation menu, click `Cluster settings` under `Cluster Overview`. You can find `Identification` block which contains the information of `Bootstrap server`. Make sure to save it somewhere safe. It should be similar to `pkc-w12qj.ap-southeast-1.aws.confluent.cloud:9092`

HOST = pkc-w12qj.ap-southeast-1.aws.confluent.cloud

#### Save those at `$HOME/.confluent/python.config`

```shell
vi $HOME/.confluent/python.config
```

Press `i` and **copy&paste the file below** !

```
#kafka
bootstrap.servers={HOST}:9092 
security.protocol=SASL_SSL
sasl.mechanisms=PLAIN
sasl.username={API_KEY}
sasl.password={API_SECRET}   
```

Then, replace HOST, API_KEY, API_SECRET with the values from `Step 3`. Press `:wq` to save the file.  

## Step 4. Create a Databricks Cluster 

In this post, we are going to deploy Databricks on the AWS. Instruction to create a Databricks Cluster on AWS is well explained in [HERE](https://www.youtube.com/watch?v=gU1BrfqMCYc). 

Click the `compute` under navigator bar, create a `Create Cluster`, and add some configuration like below in the picture. 

{{< figure src="cluster.png" title="Create a Databricks Cluster" >}}


After creating a Databricks Cluster, it's time to explore the Databricks Workspace. Click the `Workspace` under navigator bar. Click the `users`, `<user-account>, then create a `Notebook`. 


{{< figure src="workspace.png" title="Create a Databricks Workspace" >}}

Once you are done with creating the Databricks Notebook, please check the my [github page](https://github.com/youheekil/twitter-sentiment-analysis) for the source code of twitter data ingestion. 


## Step 4-1. Install Dependencies 

When you are creating a Cluster, you can find the `libraries` tab next next to `Configuration` tab. 

If you need any dependencies needed in the future, you can use this to install. Or you can install dependencies like this, `%pip install delta-spark spark-nlp==3.3.3 wordcloud contractions gensim pyldavis==3.2.0` too.  




## Step 5. Source Code for twitter data ingestion

{{< admonition type=note title="Required files for twitter real-time data ingestion" open=false >}}

**Check the source codes in my [github page](https://github.com/youheekil/twitter-sentiment-analysis)** 

* producer/producer.py
* producer/ccloud_lib.py 
* run.sh
* Dockerfile
* .env
* requirements.txt
{{< /admonition >}}


#### Still some modifications are needed 



```dockerfile
# Dockerfile

FROM python:3.7-slim

COPY requirements.txt /tmp/requirements.txt
RUN pip3 install -U -r /tmp/requirements.txt

COPY producer/ /producer

CMD [ "python3", "producer/producer.py", 
  "-f", "/root/.confluent/librdkafka.config", 
  "-t", "<your-kafka-topic-name>" ]

```

#### Procedure to run the kafka twitter data ingestion 

```Shell 
pip install virtualenv
virtualenv --version # test your installation 
cd <your-project_folder> 
virtualenv ccloud-venv
source ./ccloud-venv/bin/activate
bash run.sh
``` 


## Step 6. Spark Streaming in Databricks - Streaming Data Ingestion 

Add Confluent API Credentials as we used before in `Step 3` and copy and paste the code below for readStreaming Kafka data in the Workspace we created. 


```python
confluentApiKey = "xxx"
confluentSecret = "xxx"
host = "xxx"
```

```python 
streamingInputDF = spark \
  .readStream \
  .format("kafka") \
  .option("kafka.bootstrap.servers", host)  \
  .option("kafka.security.protocol", "SASL_SSL") \
  .option("kafka.sasl.jaas.config", "kafkashaded.org.apache.kafka.common.security.plain.PlainLoginModule required username='{}' password='{}';".format(confluentApiKey, confluentSecret)) \
  .option("kafka.ssl.endpoint.identification.algorithm", "https") \
  .option("kafka.sasl.mechanism", "PLAIN") \
  .option("startingOffsets", "latest") \
  .option("failOnDataLoss", "false") \
  .option("subscribe", "product") \
  .load()

```
**In order to run the Workspace, we need to `attach` the `cluster` we created before.**

## Step 7. Spark Streaming in Databricks - Streaming Data Transformation  


Please check the whole procedure of streaming data tranformation in `notebooks/twitter_ingestion_transformation.ipynb` in my github-pages!



## Step 8. Connect DataBricks and Delta Lake

We are able to build a complete streaming data pipeline to consolidate the data by using Confluent Kafka as an input/source system for Spark Structured Streaming and Delta Lake as a storage layer. 

> `Delta Lake` is an open-source storage layer that brings ACID transactions to Apache Spark and big data workloads. It helps unify streaming and batch data processing. A `Delta Lake` table is both a batch table as well as a streaming source and sink. As data are stored in Parquet files, delta lake is storage agnostic. It could be an Amazon S3 bucket or an Azure Data Lake Storage container - [Michelen Blog](https://blogit.michelin.io/kafka-to-delta-lake-using-apache-spark-streaming-avro/). 

```python
%pip install delta-spark
from delta import *

... # after doing whole data transformation 

# Save the sentiment tweets streaming data into delta Lake under the path, /tmp/delta-tweet-table

sentiment_tweet = sentiment_tweets \
  .writeStream.format("delta") \
  .outputMode("append") \
  .trigger(processingTime='10 seconds') \
  .option("checkpointLocation", "/tmp/checkpoint") \
  .start("/tmp/delta-tweet-table")

# reading data in Delta Lake
DF = (
   spark.read \
      .format("delta") \
      .load("/tmp/delta-tweet-table") \
      .createOrReplaceTempView("table2")
)

display(spark.sql("SELECT * FROM table2 LIMIT 1000"))
```

## Step 9. Spark NLP


``` 
%pip install spark-nlp
 
```
Installing Spark NLP from PyPI is not enough to run Spark NLP in Databricks. Therefore, we still need to install a dependency - `spark-nlp_2.12:3.4.4` (something similar to this one) under the `libraries` tab in the `Cluster`.

Or attch spark-nlp-1.3.0.jar to the cluster. This library can be downloaded from the
spark-packages repository https://spark-packages.org/package/JohnSnowLabs/spark-nlp. 

```python 
from pyspark.ml import Pipeline

# spark-nlp-1.3.0.jar is attached to the cluster. This library was downloaded from the
# spark-packages repository https://spark-packages.org/package/JohnSnowLabs/spark-nlp
from sparknlp.annotator import *
from sparknlp.common import *
from sparknlp.base import *
```

### Create a Preprocessing Stages Pipeline 

```python 
# Create pre-processing stages

# Stage 1: DocumentAssembler as entry point
documentAssembler = DocumentAssembler() \
                    .setInputCol("value") \
                    .setOutputCol("document")
 
# Stage 2: Tokenizer
tokenizer = Tokenizer() \
              .setInputCols(["document"]) \
              .setOutputCol("token")
 
# Stage 3: Normalizer to lower text and to remove html tags, hyperlinks, twitter handles, 
# alphanumeric characters (integers, floats), timestamps in the format hh:mm (e.g. 10:30) and punctuation
cleanUpPatterns = [r"RT", "<[^>]*>", r"www\S+", r"http\S+", "@[^\s]+", "[\d-]", "\d*\.\d+", "\d*\:\d+", "[^\w\d\s]"]
normalizer = Normalizer() \
                .setInputCols("token") \
                .setOutputCol("normalized") \
                .setCleanupPatterns(cleanUpPatterns) \
                .setLowercase(True)
 
# Stage 4: Remove stopwords
stopwords = StopWordsCleaner()\
              .setInputCols("normalized")\
              .setOutputCol("cleanTokens")\
              .setCaseSensitive(False)
 
# Stage 5: Lemmatizer
lemma = LemmatizerModel.pretrained() \
              .setInputCols(["cleanTokens"]) \
              .setOutputCol("lemma")
 
# Stage 6: Stemmer stems tokens to bring it to root form
#.setInputCols(["cleanTokens"]).setOutputCol("stem") \
stemmer = Stemmer() \
            .setInputCols(["lemma"]) \
            .setInputCols(["cleanTokens"]) \
            .setOutputCol("stem")
 
# Stage 7: Finisher to convert custom document structure to array of tokens
finisher = Finisher() \
            .setInputCols(["stem"]) \
            .setOutputCols(["token_features"]) \
            .setOutputAsArray(True) \
            .setCleanAnnotations(False)
```


```python 
from pyspark.ml import Pipeline

# Check pre-processing pipeline
prep_pipeline = Pipeline(stages=[documentAssembler, tokenizer, normalizer, stopwords, lemma, stemmer, finisher])
 
empty_df = spark.createDataFrame([['']]).toDF("value")
prep_pipeline_model = prep_pipeline.fit(empty_df)
result = prep_pipeline_model.transform(tweet_df)
```
The pipeline is followed by the procedure as below. 
{{< figure src="sparknlp.png" title="NLP pipeline in Spark" >}}



## [Option] integrate databricks notebook with Github
You can connect databricks notebook with Github for the revision history. The procedure is described in [here](https://www.youtube.com/watch?v=O-DpRnJsLoQ)


## Reference 
* https://github.com/scoyne2/kafka_spark_streams
* https://blogit.michelin.io/kafka-to-delta-lake-using-apache-spark-streaming-avro/
* https://medium.com/@lorenagongang/sentiment-analysis-on-streaming-twitter-data-using-kafka-spark-structured-streaming-python-part-b27aecca697a
* https://winf-hsos.github.io/databricks-notebooks/big-data-analytics/ss-2020/Word%20Clouds%20with%20Python.html
