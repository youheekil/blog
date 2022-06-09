---
title: "SQL Interview Preparation"
date: 2022-05-23
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

## Database 
* `Database` is a **container** that holds all your tables. 
* A `table` is a container for a subset of your **data**. 
    - A table holds data organized in ***rows*** and ***columns*** 

```sql
CREATE DATABASE MovieIndustry;

CREATE DATABASE IF NOT EXISTS MovieIndustry;

SHOW DATABASES; /* The output lists all the databases in the system */

DROP DATABASE MovieIndustry; 
```

## Data Type 
* **Numeric**: INT, BIGINT, TINYINT, DECIMAL
* **Date and Time**: DATE, TIME, TIMESTAMP, YEAR
* **String**: VARCHAR, CHAR, ENUM, SET, BLOB
* **JSON**: JSON
* **Spatial Data**: GEOMETRY, POINT