# [Kafka] Running Kafka with Docker (python)


{{< figure src="kafka-docker.png" title="Kafka with Docker" >}}

In this post, we would like to go over how to run Kafka with Docker and Python. Before starting the post, make sure you have installed Docker (Docker hub) on your computer.

## Step 1. Docker Image Setup

Okay, first, let's create a directory folder to store docker-compose.yml file.
The docker-compose file does not run your code itself.

```shell
$ mkdir ~/docker-kafka && cd docker-kafka
```
You can pull kafka and zookeeper images by using this docker pull command, more detailed explanation can be found in the following link - [kafka](https://hub.docker.com/r/wurstmeister/kafka) and [zookeeper](https://hub.docker.com/r/wurstmeister/zookeeper) from Docker Hub.

```shell
# Kafka
$ docker pull wurstmeister/kafka

# Zookeeper
$ docker pull wurstmeister/zookeeper
```

## Step 2. Create docker-compose.yml file
### Docker
Instead of pulling images separately, you can write docker-compose.yml file to pull those simultaneously. What is docker-compose.yml file? It is basically a config file for Docker Compose. It allows you to deploy, combine, and configure multiple docker containers at the same time. Is there difference between dockerfile and docker-compose? Yes! "A Dockerfile is a simple text file that contains the commands a user could call to assemble an image whereas Docker Compose is a tool for defining and running multi-container Docker applications" ([dockerlab](https://dockerlabs.collabnix.com/beginners/difference-compose-dockerfile.html))

### Docker-compose.yml file
```yml
# This docker-compose file starts and runs:
# * A 1-node kafka cluster
# * A 1-zookeeper ensemble

version: '2'
services: 
  zookeeper:
    image: wurstmeister/zookeeper
    container_name: zookeeper
    ports:
      - "2181:2181"
  kafka:
    image: wurstmeister/kafka
    container_name: kafka
    ports:
      - "9092:9092"
    environment:
      KAFKA_ADVERTISED_HOST_NAME: 127.0.0.1
      KAFKA_ZOOKEEPER_CONNECT: zookeeper:2181
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
```

## Step 3. Run docker-compose
Make sure you run the following command where docker-compose.yml file is located at. 

```shell
$ docker-compose up -d
```

## Step 4. Run Kafka !
### [Option 1] Execute docker container (bash)
```shell
$ docker container exec -it kafka bash
```
`bash` script will prompt!

```bash
cd opt/kafka_2.13-2.8.1/bin

# topic list
kafka-topics.sh --list --zookeeper zookeeper:2181

# create topic 
kafka-console-producer.sh --bootstrap-server kafka:9092 --topic <topic-name>

# read events
kafka-console-consumer.sh --bootstrap-server kafka:9092 --topic <topic-name> --from-beginning

# delete topic 
kafka-topics.sh --zookeeper zookeeper:2181 --delete --topic <topic-name>
```

### [Option 2] Access Kafka directly through command line
```shell
# Check topic list
$ docker exec -it kafka kafka-topics.sh --bootstrap-server kafka:9092 --list

# Create topic
$ docker exec -it kafka kafka-console-producer.sh --bootstrap-server kafka:9092 --topic <topic-name>

# Read events
$ docker exec -it kafka kafka-console-consumer.sh --bootstrap-server kafka:9092 --topic <topic-name> --from-beginning
```

## Check Your Environment Status
You may run the following command at any time from a separate terminal instance:
```shell
$ docker-compose ps
```

## Stopping & Cleaning Up Docker Compose
When you are ready to stop Docker compose you can run the following command
```shell
$ docker-compose stop
```

And if you'd like to clean up the container to reclaim disk space, as well as the columns containing your data, run the following command: 

```shell
$ docker-compose rm -v
Are you sure? [yN] y
```

## Further More with Python 

So, when you completed connecting kafka and docker, it's time to actually get real-time tweets from twitter through kafka. 

> Imagine, you own a small company which produces a service to users through own online platform. Then, there should be a `source system` like clickstream and a `target system` like own online platform. Data integration between the source system and target system woudln't be that complicated. But, once the size of your company grows, the company would face lots of struggles when the company has more source systems and target systems with all different data sources. That's the when the `Kafka` comes in. `Kafka` is a platform to get produced data from the source systems and the target systems read a streaming data from Kafka. 



{{< figure src="kafka_diagram.png" title="Kafka Diagram" >}}

The image is originally from [a post](https://medium.com/@stephane.maarek/the-kafka-api-battle-producer-vs-consumer-vs-kafka-connect-vs-kafka-streams-vs-ksql-ef584274c1e) explaining about Kakfa. I recommend the post ! 



**In this post, we will create three files under `src` folder.**

### 1. credential.json
- Get Twitter API Credentials thorugh the [link - TwitterAPI for Developer](https://developer.twitter.com/en/docs/twitter-api/getting-started/getting-access-to-the-twitter-api)
### 2. producer.py

```python 
#src/producer.py

BROKER_URL = "localhost:9092"
TOPIC_NAME = "<your topic_name>"

### twitter
import tweepy
from tweepy.auth import OAuthHandler
from tweepy import Stream
from tweepy.streaming import StreamListener
import json
import logging 


### logging 
FORMAT = "%(asctime)s | %(name)s - %(levelname)s - %(message)s"
LOG_FILEPATH = "<path-to-your-log-file>"
logging.basicConfig(
    filename=LOG_FILEPATH,
    level=logging.INFO,
    filemode='w',
    format=FORMAT)

### Authenticate to Twitter
with open('src/credential.json','r') as f:
    credential = json.load(f)

CONSUMER_KEY = credential['twitter_api_key']
CONSUMER_SECRET = credential['twitter_api_secret_key']
ACCESS_TOKEN = credential['twitter_access_token']
ACCESS_TOKEN_SECRET = credential['twitter_access_token_secret']
BEARER_TOKEN = credential['bearer_token']



from tweepy.streaming import StreamListener
from tweepy import OAuthHandler
from tweepy import Stream
from kafka import KafkaProducer
producer = KafkaProducer(bootstrap_servers='localhost:9092',
    value_serializer=lambda v: v.encode('utf-8')) #Same port as your Kafka server


class twitterAuth():
    """SET UP TWITTER AUTHENTICATION"""

    def authenticateTwitterApp(self):
        auth = OAuthHandler(consumer_key=CONSUMER_KEY, consumer_secret=CONSUMER_SECRET)
        auth.set_access_token(ACCESS_TOKEN, ACCESS_TOKEN_SECRET)

        return auth



class TwitterStreamer():

    """SET UP STREAMER"""
    def __init__(self):
        self.twitterAuth = twitterAuth()

    def stream_tweets(self):
        while True:
            listener = ListenerTS() 
            auth = self.twitterAuth.authenticateTwitterApp()
            stream = Stream(auth, listener)
            stream.filter(track=["Starbucks"], stall_warnings=True, languages= ["en"])


class ListenerTS(StreamListener):

    def on_status(self, status):
        tweet = json.dumps({
            'id': status.id, 
            'name': status.user.name, 
            'user_location':status.user.location, 
            'text': status.text, 
            'fav': status.favorite_count, 
            'tweet_date': status.created_at.strftime("%Y-%m-%d %H:%M:%S"), 
            'tweet_location': status.place.full_name if status.place else None
        }, default=str)  

        producer.send(topic_name, tweet)
        return True


if __name__ == "__main__":
    TS = TwitterStreamer()
    TS.stream_tweets()
```

### 3. consumer.py
```python
from kafka import KafkaConsumer
import json

topic_name = "<your_topic_name>"

consumer = KafkaConsumer(
    topic_name,
     bootstrap_servers=['localhost:9092'],
     auto_offset_reset='latest',
     enable_auto_commit=True,
     auto_commit_interval_ms =  5000,
     fetch_max_bytes = 128,
     max_poll_records = 100,

     value_deserializer=lambda x: json.loads(x.decode('utf-8')))

for message in consumer:
 tweets = json.loads(json.dumps(message.value))
 print(tweets)

```

### 4. Run `producer.py` and `consumer.py` files

Open two different terminals. 

#### terminal 1
```shell
python src/consumer.py
```

#### terminal 2
```shell
python src/producer.py
```


**The source code can be checked [here in github](https://github.com/youheekil/Twitter-Streaming-with-Apache-Kafka-Docker-and-Python)**

## Reference 
- image 
https://medium.com/@stephane.maarek/the-kafka-api-battle-producer-vs-consumer-vs-kafka-connect-vs-kafka-streams-vs-ksql-ef584274c1e




