# [SongPlz-Bot] 0. Slack Serverless Song Recommendataion Chatbot (SongPlz) - Architecture Planning



# SongPlz Bot - Architecture Planning

## 1. Aim

The aim of this `SongPlz` Bot project (Slack Song Recommendation Bot) is to build a data pipeline to create a chatting bot for song recommendation with Serverless architecture which is a way to develop and run applications and services without managing infrastructure ([AWS](https://aws.amazon.com/lambda/serverless-architectures-learn-more/)). If you are still unsure about serverless architecture, please click here. 

There are many cloud services available providing FaaS such as AWS Lambda, Azure Functions, and Google Cloud Function. In this project, AWS Serverless Architecture, aws Lambda, and api gateaway, will be used. Since it's a project to perform a data pipeline, I would try to ingest data with different types of databases and data sources. 

A messenger is Slack. Mainly, AWS Database service will be used for the whole process. 

Expected Architecture planning is going to be like this:

{{< figure src="songplz-bot-planning.png" title="Architecture Planning" >}}

## 2. Happy Path
* Use the config file to get a set of queries we want to use for our artist search.
* Iterate over query objects and call Spotify API to get the songs of the artist. And recommend similar songs from different artists (son recommendation modeling is required at this stage)
* Format Slack messages according to preferred style/layout.
* Send Slack messages to the specified channel.

{{< admonition type=note title="Script Planning" open=false >}}
Create two folders - `SongPlz`, `tests`
Under `SongPlz` folder
1. config.py 
2. spotify_api_helper.py
3. query_helper.py
4. slack.py
5. runner.py

{{< /admonition >}}
## 3. Tech Stack 
* Slack API
* Spotify API
* AWS account 


