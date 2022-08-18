---
title: "[Data Engineer] Data Quality"
date: 2022-08-02
draft: true
author: Youhee
images: []
categories: ["Data Engineer"]

lightgallery: true

toc:
  auto: false
math:
  enable: true
---

https://www.youtube.com/watch?v=N9olq42z-AE

## What is Data Quality?

In computing, `data` is information that has been translated into a form efficient for movement or processing. People use data for operation, customer management, marketing, analysis, and decision-making. 


According to [Profisee](https://profisee.com/data-quality-what-why-how-who/), data quality can be defined as the following:

* Data is of high quality if it is fit for its intended use or purpose.
* Data is of high quality if it correctly represents the real-world construct it describes. 


In short, data is high quality if there are no data issues. On the other hand, low-quality data can lead to a low-quality decision. 


## Different types of data quality issues

1. Incompletness ex) NULL 
2. Inaccuracy
3. Inconsistency
4. Invalidity 
5. Redundancy 
6. Non-standard format

## What cuases data quality issues?


## Data Quality assurance process
* Data profiling, first explore the data and analyze it. Then identify the quality issues and summarize the data. 


# how do you write a good data check?
write-audit-publish 

when we write a dataset for a day instead of writing it to production, we write it to a staging tables. 

After we are writing it to a staging table, we write out audit queries mainly do thiing like check for 





# how can you implement the write-audit-publish pattern in your pipelines?



