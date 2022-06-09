# [Starbucks Twitter Sentiment Analysis] 0. Architecture Planning


# Architecture Planning


{{< figure src="head_image.png" title="From Kafka to Delta Lake using Apache Spark Structured Streaming" >}}

Image Source: [From Kafka to Delta Lake using Apache Spark Structured Streaming](https://blogit.michelin.io/kafka-to-delta-lake-using-apache-spark-streaming-avro/)

## 1. Aim

The aim of the Starbucks Twitter Sentimental Analysis project is to build end-to-end twitter data streaming pipeline to analyze brand sentiment analysis. 


> `Brand sentiment analysis` is, to put it simply, a way of determining the general attitude toward your brand, product, or service. Nowadays, the easiest way to analyze brand sentiment is through media monitoring tools. Customers often express their feeling or option toward the brand on social media channels. These opinions will give insight on the brand's position. The sentimental is determined mostly just as positive, negative, or neutral ([MediaToolKit](https://www.mediatoolkit.com/blog/brand-sentiment-analysis/#:~:text=Brand%20sentiment%20analysis%20is%2C%20to,is%20through%20media%20monitoring%20tools.))

{{< figure src="project_overview.png" title="Project Overview" >}}


## 2. Happy Path
* connect with big data streams to ingest twtiter data. Azure event hub -> azure stream analytics and dump into datalake gen2.  
then azure synapse analytics and connect to azure machine learning for sentimental analysis 

{{< admonition type=note title="Script Planning" open=false >}}
* producer.py
* consumer.py
* config (.env)
* Dockerfile 
* run.sh
{{< /admonition >}}
## 3. Tech Stack 
* Kafka, Spark Streaming, Confluent Cloud, Databricks, Delta Lake, Spark NLP

## 4. Procedure
There were three options to choose for this project. The first Option I considered was the combination of Kafka and Spark streaming. The second option was the combination of socket and Spark streaming, and third is the combination of Confluent Cloud and Databricks.  

### Option 1. 
- Docker Platform 

Twitter - Kafka - Spark Streaming 


### Option 2 
- local environment
Twitter - Socket - Spark Streaming 

### Option 3.
Combination of Confluent Cloud and Databricks  
Twitter - Kafka - Spark Streaming 
 


After the trials and concerns, I decided to go for Option 3. There are three reasons, first, I faced some struggles while submiting python file for spark streaming. My local environment kept giving me errors whenever I tried to submit the python file with `spark-submit` code. Second, I wanted to try different cloud platform, and found out that Confluent Cloud perform reall well. For the Spark Streaming, Databricks was used since it is well organized and easy setup for Spark Streaming and have Delta Lake. 


## 5. What is Spark Streaming?
- Data is continuously flowing in so that it's a bit different than working with batch data. 
- Processing engine to process data in real-time from sources and output data to external storage systems. 

> STREAMING API is diffrent than API calls. For Streaming API, we just make one request to API that opens up portal where all of the event/data are going to be streaming back to client. 


## Spark Streaming vs Spark Structured Streaming 

### Identify Input sources
* File Source: reads files written in a directory as a stream of data. Supported file formats are text, CSV, JSON, ORC, Parquet
* kafka Source: Reads data from Kafka. 
* Socket Source(for testing): Reads UTF8 text data from a socket connection. The listening server socket is at the driver. 

### Identify Output Modes

* Append Mode: Spark will output only newly processed row since the last trigger
* Update Mode: Spark will output only updated rows since the last trigger
* Complete mode: Spark will output all the rows it has processed so far.  

### Output Sinks
* File sink: Store the output to a directory
* Kafka sink: Store the output to one or more topics in Kafka  
* Console sink (for debugging): The output is printed to the console/stdout every time there is a trigger
* Memory sink (for debugging): The output is sotred in memory as an in-memory table


### Testing locally with sockets

Testing with sockets helps you to design the overall data extraction, transformation and storage place in your local environment. 

Once you scale up, other services such as Kafka can replace the socket. 

> Socket is like an electric socket. Data is coming into the socket like electricity and then Spark is the plug that accepts that electricity and sends it to your data stroage place. 

### Real Examples of data streaming 
* Chat bots
* Message apps 
* Real-time ML prediction like Netflix, Amazon
* E-commerce apps 

