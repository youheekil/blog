# [SongPlz-Bot] 1. Slack and Spotify API



# SongPlz Bot - Slack and Spotify API

## What is API 

API stands for Application Programming Interface. The most common example to describe the API is being a waiter in the restaurant. 

{{< figure src="interface_between_human_object.png" title="Interface between human and object">}}
{{< admonition type=note title="Interface" open=false >}}
`Interface` is a program that allows a user to interact computers in person or over a network, which is a software intermediary that allows two applications to talk to each other
{{< /admonition >}}

Imagine you are sitting at the table in a Mexican restaurant, and you are thinking of ordering taco. You call a waiter to order a Taco Plate. Then, waiter takes a order and tells the kitchen that number 5 table customer ordered Taco Plate. Chef in the Kitchen cooks the Taco Plate. When the food is ready, waiter delivers the food to your table !

> In this example, the kitchen is the part of the `system` that will prepare your order (`user's request`), and the waiter is the `API` that takes your request or order and tells the kitchen (`system`) what to do and delivers the food (`response`) to you. 

It can be very dangerous to directly communicate between the `system` and the `user`. APIs provides a layer of security so that your system is not fully exposed. 

The modern API adhere to standards (typically ***HTTP*** and ***REST***), that are developer-friendly, easily accesssible and understood broadly. 

### HTTP API
An **HTTP API** is an API that uses `Hypertext Transfer Protocol` as the communication protocol between the two systems ([Educative](https://www.educative.io/blog/what-are-rest-apis))
{{< admonition type=note title="Protocol" open=false >}}
`Protocol` in computer science, a set of rules or procedures for transmitting data between electronic devices, such as computers.
So, for the web, the protocol gives a rule about how a **client** (web browser) and a **server** (web server e.g. udacity.com) talk ([Udacity](https://www.youtube.com/watch?v=0U31-O4oEPc)).
{{< /admonition >}}


### REST API
**REST API** stands for Representational State Transfer and is an *architectural pattern* for creating web services. It focuses on `representational` with **URI** and define `state` with **HTTP** Method. It's essential that REST API's design style strictly has to satisfy CRUD functions only. REST applications use HTTP methods like `GET`, `POST`, `DELETE`, and `PUT` ([Educative](https://www.educative.io/blog/what-are-rest-apis)).

{{< admonition type=note title="CRUD" open=false >}}
CRUD (CREATE, READ, UPDATE, DELETE) is used for anlything related to database and database design. 
| OPERATION | FUNCTION  |
| ------ | ----------- |
| Create | Insert |
| Read   | Select |
| Update | Edit |
| Delete | Delete |

{{< /admonition >}}



## Slack API
Click [here](https://slack.com/help/articles/115005265703-Create-a-bot-for-your-workspace) to create a bot for your slack workspace !

After adding your bot into your slack workspace, you can send a message as a Bot in python. 

There are few options to send messages through Slack API as a Bot !

### Option 1. 
```python
import config
import requests
from datetime import datetime
import logging
from slack_sdk.errors import SlackApiError


logger = logging.getLogger(__name__)

SLACK_BOT_TEXT = (
    "HEY THERE, I FOUND A TOP 10 SONGS YOU MAY BE INTERESTED IN."
    "THEY ARE LISTED BELOW"
)

class SlackFacade(object):

    def __init__(self, token = config.SLACK_BOT_TOKEN, bot_name = config.SLACK_BOT_NAME):
        self.token = token
        self.default_channel = config.DEFAULT_SLACK_CHANNEL
        self.bot_name = bot_name

    def emit(self, channel, text):
        """Sends a message to your channel 

        Args:
          channel ([String]): Specify the name of the slack channel 
        """
        try: 
            text_with_date = datetime.now().strftime('[%m/%d %H:%M:%S] ') + text
            response = requests.post(
              "https://slack.com/api/chat.postMessage",
              headers={"Authorization": "Bearer "+ self.token},
              data={"channel": channel,"text": text_with_date}
              )

        except SlackApiError as e:
            logging.error(f"Slack encountered error: {e.response['error']}")
            raise e

if __name__=="__main__":
    SlackFacade().emit(
      channel=config.DEFAULT_SLACK_CHANNEL, 
      text=SLACK_BOT_TEXT
      )
```
Then, your channel will recieve a message from your app like this :


{{< figure src="slack_message.png" title="Message from a SongPlz bot in Slack (Option 1)" >}}

### Option 2.


#### Code 
```python 
from slack_sdk import WebClient
from slack_sdk.errors import SlackApiError
block = {
			"type": "section",
			"text": {
				"type": "mrkdwn",
				"text": "Hello :heart: this is *SongPlz*, Do you want *Global Top 10 Song*?"
			}
		}

class SlackFacade(object):

    def __init__(self, token=config.SLACK_BOT_TOKEN,
                bot_name=config.SLACK_BOT_NAME):
        self.token = token
        self.default_channel = config.DEFAULT_SLACK_CHANNEL
        self.bot_name = bot_name

        # Internally set properites
        self.client = WebClient(token=self.token)
        self.slack_message_entries = {}

    def emit(self, blocks, channel):
        """Sends a message to your channel.
        Args:
            blocks: Expected to be a json-like array object containing the rich
                text representation of the listings we've found. The methods in
                this object should be used to construct the Rich Message Blocks.
                See the Slack kit builder for more information on block
                construction: https://app.slack.com/block-kit-builder
            channel: string, The channel to send the message to.
        """
        try:
            response = self.client.chat_postMessage(
                channel=channel,
                text="SongPlz Incoming!",
                blocks=blocks,
                # as_user must be false to set bot name
                username=self.bot_name
            )
        except SlackApiError as e:
            logging.error(f"Slack encountered an error: {e.response['error']}")
            raise e

        return response
        
SlackFacade().emit(blocks = [block], channel=config.DEFAULT_SLACK_CHANNEL)
```

{{< figure src="slack_message_2.png" title="Message from a SongPlz bot in Slack (Option 2)" >}}

#### Error (Solved)
At first, I had so much struggles to fix the error encountered while tyring option 2. 
```txt
# error I received
urlopen error [SSL: CERTIFICATE_VERIFY_FAILED]
certificate verify failed: unable to get local issuer certificate

```

I tried following two methods to fix the error. 

* **Method 1**. (-> this one failed for my case)
  ```shell
  pipenv install certifi # or
  pip install certifi 
  ```

* **Method 2**. (if method 1 doesn't work)
  ```python 
  import ssl

  ssl._create_default_https_context = ssl._create_unverified_context
  ```

## Spotify API 

We can use [Spotify's Web API](https://developer.spotify.com/documentation/web-api/reference/#/) to discover music and podcasts. 

Let's try **Search API** for items, you can get Spotify catalog information about albums, aritist, playlists, tracks, shows, or episodes that math a keyword string. 

> **Search API**
{{< admonition type=note title="Request: GET /search" open=false >}}
* **HEADER**: Authorization -> Authorized `Access_token` is requred. 
* **q** [string]: You can narrow down your search using field filters. `album`, `artist`, `track`, `year`, `upc`, `tag`:hipster, `tag`:new, `isrc`, `genre`. e.g. `remaster%20track:Doxy+artist:Miles%20Davis"
  * The `artist` filter can be used while searching albums, artists or tracks.
  * The `album` and `year` filters can be used while searching albums or tracks. You can filter on a single year or a range (e.g. 1955-1960).
  * The genre `filter` can be use while searching tracks and artists.
  * The `isrc` and `track` filters can be used while searching tracks.
  * The `upc`, `tag:new` and `tag:hipster` filters can only be used while searching albums. The `tag:new` filter will return albums released in the past two weeks and tag:hipster can be used to return only albums with the lowest 10% popularity.
* **type** [array]: A comma-separated list of item types to search across e.g. `q=name:abacab&type=album,track` meaning it will return both albums and tracks with "abacab" included in their name
* **limit** [int]: default value is 20 
* **market** [string]: if a country code is specified, only content that is available in that market will be returned. e.g. "ES"
* **offset** [int]: The index of the first result to return.
{{< /admonition >}}
{{< admonition type=note title="Reponse: 200" open=false >}}
* Body - application/**json**
  - `Tracks` 
  - `artist` 
  - `albums`
  - `playlists`
  - `shows`
  - `episodes`

* Each `object` contains 

  - href [string] 
  - items 
  - limit 
  - next 
  - offset
  - previous
  - total : total number of items available to return 

{{< /admonition >}}
#### Example: BTS Song 

We need these to use API !
* **endpoint**: https://api.spotify.com/v1/search
* **method**: GET
* **parameter**: 
  ```json
  {'q': 'BTS', 'type':'album', 'limit': 5}
  ```
* **HEADER**: 

  ```python
  {'Authorization': 'Bearer {}'.format(token)}
  ```
This is just one of options to get a data using API, there are more ways to get a data!  


```python

sp = spotipy.Spotify(auth_manager=SpotifyClientCredentials(client_id=config.SPOTIFY_CLEINT_ID,
client_secret=config.SPOTIFY_CLIENT_SECRET))

results = sp.search(q='BTS', limit=5)
print(results)
for idx, track in enumerate(results['tracks']['items']):
    print(idx, track['name'])
```
## Concern for Better Data Pipeline 

We successfully retrieved information by using Spotify API. 
But, we have to think `HOW` to extract data more effectively. 

Here are still remaining questions to answer to get better data extraction. 
* What if there is API restriction ?
* What if there is an error occurred in middle of the process?
* what if API authroization token is expired ?



## Reference 
https://www.educative.io/blog/what-are-rest-apis
https://restfulapi.net/
https://www.youtube.com/watch?v=0U31-O4oEPc
