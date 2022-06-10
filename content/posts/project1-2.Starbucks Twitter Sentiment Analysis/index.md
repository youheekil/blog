---
title: "[Starbucks Twitter Sentiment Analysis] 2. Sentiment Analysis"
date: 2022-06-09
draft: true
author: Youhee
images: []
tags: ["Sentiment-Analysis", "kafka", "spark-streaming" ]
categories: ["Project"]

lightgallery: true

toc:
  auto: false
math:
  enable: true
---
# Sentiment Analysis with SparkNLP & WordCloud

There have been instances where a brand sentiment were shown on account of tweets by consumers. 

Therefore, I wored on a project to create a Starbucks data lake to analyze current trend. 

We leveraged the Databricks Lakehouse Platform to ingest unstructured data from Twitter using the Tweepy libaray and Confluent Kafka. 