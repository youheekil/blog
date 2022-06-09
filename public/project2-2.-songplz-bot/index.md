# [SongPlz-Bot] 2. Severless & Data Ingestion & Recommender System


There are two basic recommender systems: **(1) Collaborative Filtering, (2) Content-Based Filtering**. It differs by what kinds of data that you are working with. ***Collaborative Filtering approach*** works with the user-item interactions types of data, such as ratings or buying behavior. On the other hand, ***Content-Based Filtering approach*** is for the attribute information about the users and items, such as textual profiles or relevant keywords. 


In this post, I am going to perform an effective song recommendataion system with the combination of two user's informations - mood and favorite artist. 

## Recommender system architecture 
First, each user will get questions like below asking user's mood by color and favorite singer. 


{{< figure src="questions_slack.png" title="Question Messages from a SongPlz bot in Slack" >}}



* **PHASE 1** : React to commands in Slack channel, do some basic operations like retrieving global top 10 songs. 
* **PHASE 2** : 


### Mood 

For the song recommendataion algorithm based on user's mood, I took some references from [researchgate](researchgate.net). 



{{< figure src="mood-by-color.png" title="Defination of Mood by color" >}}


{{< figure src="mood.png" title="2D Arousal-Valence 2D plane" >}}

So, few colors will be listed to let users choose depending on their mood (red 🔴, yellow 🟡, navy blue 🔵, purple 🟣, white ⚪). Also, Spotify provides `audio feature` for each song which contains value of Danceability, Energy, Instrumentalness, Liveness, Loudness, Speechiness, Tempo, Valence from 0.0 to 1.0. In this post, two audio features - `energy` and `valence` - will be used mainly.  


> `Energy` is a measure from 0.0 to 1.0 and represents a perceptual measure of intensity and activity. Typically, energetic tracks feel fast, loud, and noisy. Tracks with high `valence` sound more positive (e.g. happy, cheerful, euphoric), while tracks with low valence sound more negative (e.g. sad, depressed, angry) [(Bo Plantinga)](https://medium.com/@boplantinga/what-do-spotifys-audio-features-tell-us-about-this-year-s-eurovision-song-contest-66ad188e112a#:~:text=Typically%2C%20energetic%20tracks%20feel%20fast,an%20audience%20in%20the%20recording.). 

After we collected the information related to `mood` & `color`, and researched available information we can get from Spotify, we came up with the final recommendation approach to recommend songs based on user's information. 


{{< figure src="mood-color-2d-plane.png" title="Final song recommendation approach planning based on user's mood" >}}
### Singer
> Artists and Track Data ETL pipeline is required. 

We calculate average of audio features of each artists's top 10 songs that user would like to listen by using either Euclidean Distance or Cosine Distance. Save top 3 most similar artists into postgreSQL or MySQL.



## Data Modelling 

There are many more Response status code as you can check [HERE](https://developer.spotify.com/documentation/web-api/).

* Artist

| Column | Data Type |
| ------ | ----------- |
| Artist Id | VARCHAR(256) |
| Artist Name | VARCHAR(256)|
| Artist Genre | VARCHAR(256) |
| Followers | INT(11) |
| Popularity | INT(11) |
| Artist Uri   | VARCHAR(256) |
| Artist Info | VARCHAR(256) |

* Artist Genre


* TRACK 

| Column | Data Type |
| ------ | ----------- |
| URI   | VARCHAR(256) |
| Track Name | VARCHAR(256) |
| Artist Uri    | extension to be used for dest files. |

* GENRE 

* MOOD 
