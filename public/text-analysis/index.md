# Sentiment Analysis with NLTK, TextBlob, Spark Streaming


## TextBlob 

The `TextBlob` method produces `polarity` and `subjectivity` score. The `polarity` score which falls between [-1.0, 1.0] indicates a sensentivity from the sentence. If the score is below zero (0.0), sensitivity of the sentence is negativity. While the score is above zero (0.0), then the sensitivity of the sentence is positive. The `subjectivity` score which falls between [0.0, 1.0] identifies whether the sentence is objective or subjectivity. If the score is close to 0.0, the sentence tends to be more objective. On the other hand, if the score is close to 1.0, the sentence tends to be more subjective. 


```python 
# import libraries

import pandas as pd
import seaborn as sns
from textblob import TextBlob 


simple_sentence = "Business Process Management systems (BPMS) are a rich source of events that document the execution of processes and activities within these systems."

# singularize
blob = TextBlob(simple_sentence.lower())

# model 
TextBlob(simple_sentence).sentiment

# words
print("words: {}".format(blob.words))

# sentiment 
print(TextBlob(simple_sentence).polarity)
```
## pyspark.sql
### 1. Create a SparkSession

First, we need to create a SparkSession which serves as an entry point to Spark SQL. 

```python 

from pyspark.sql import SparkSession 

sc = SparkSession.builder.getOrCreate()
sc.SparkContext.setLogLevel("WARN")

print(sc)
```
### 2. Create a spark dataframe by reading a csv file 

```python 
df = sc.read.option("header", "true").csv(<path-to-the-csvfile>)

df.columns
```
### 3. Select 

The `select` function helps to create a subset of the data 
```python 
df.select("column1", "column2").show(5)
```

### 4. filter

```python 
from pyspark.sql import functions as F

df.filter(
    (F.col('column1') == "condition1") &
    (F.col('column2') > 100) 
).count()
```

### 5. withColumn 
The `withColumn` function is very useful, it can be used to manipulate columns or create new columns. 

```python 
df = df.withColumn('new_column', F.col('column2')/100)
df.select('new_column').show(5)
```

### 6. GroupBy 
The `groupby` function is also very useful for data analysis which does grouping data points (rows). 

```python 
df.groupby('column1').agg(
    F.mean('new_column').alias('column3')
).show()
```
The `alias` function does assigning a new name to the aggregated column

### orderby

The `orderby` function is used for sorting the values. 
```python 
df.groupby("column1").agg(
    F.round(F.mean("new_column"), 2).alias("column3")
).orderBy(
    F.col("column3"), descending=False
).show()
```

### lit
The `lit` function is to create a column by assigning a literal or constant value. 

```python 
new_df = df.filter(F.col('Type') == 'h').select(
    'column1', 'column2', 'column3'
    ).withColumn('is_true', F.lit(1)) # assign 'is_true' column to 1(true)
new_df.show(4)
```
### when 
The `when` function evaluates the given conditions and returns values accordingly. 

```python 
df.select(when(df['column1']=='condition1', 1)\
            .otherwise(0)\
            .alias('is_true'))\
            .show()
```

## pyspark.sql.functions 
### get_json_object
The function `get_json_object()` is used to extract the JSON string based on path from the JSON column 
```python
from pyspark.sql.functions import get_json_object

df.select(col("id"),get_json_object(col("value"),"$.ZipCodeType").alias("ZipCodeType")) \
    .show(truncate=False)

//+---+-----------+
//|id |ZipCodeType|
//+---+-----------+
//|1  |STANDARD   |
//+---+-----------+
```
### UDFs - User-Defined Functions 
User-Defined Functions (UDFs) are user-programmable routines that act on one row. 

```python 
from textblob import TextBlob

# define get_sentiment function 
def get_sentiment(text):
    try:
        tweet = TextBlob(text)
        return tweet.sentiment.polarity
    except:
        return None


# define your function 
from pyspark.sql.functions import UserDefinedFunction
getSentiment = UserDefinedFunction(get_sentiment, StringType())

## apply the UDF using withColumn
df.selectExpr("cast(data as string)")\
    .withColumn('tweet', get_json_object(col('data'),"$[0].tweet"))\
    .withColumn('sentiment', getSentiment(col('tweet')).cast(FloatType()))
```




## pyspark.streaming 


* Return a new DStream by applying incremental *reduceByKey* over a sliding window. 

```python 
DStream.reduceByKeyAndWindow(func, invFun, windowDuration, slideDuration=None, numPartitions=None, filterFunc=None)
```

## Reference 
https://towardsdatascience.com/7-must-know-pyspark-functions-d514ca9376b9


