---
title: "Introduction to Azure Personalizer"
date: 2020-05-06T17:35:41+08:00
draft: false
author: "Setu Chokshi"
authorLink: ""
description: "Understanding Reinforcement Learning using Azure Personalizer"

tags: ["Starting up", "Reinforcement Learning", "Azure", "Personalizer"]
categories: ["Reinforcement Learning"]
featuredImage: "/images/posts/introduction_to_azure_personalizer/multi-arm-bandit.jpg"

hiddenFromHomePage: false
hiddenFromSearch: false

toc:
  enable: true
  auto: true
code:
  copy: true
math:
  enable: true
---

## Introduction 
I am absolutely facinated with Reinforcement learning and when Azure came out with the personalizer, I had to try it out. Azure Personalizer makes use of Vowpal Wabbit in the backend and has completely removed the complexity of working with Vowpal Wabbit. They have a sleek interface and also includes the model's feature importances. In my current work at PropertyGuru, we have been making use of Multi Arm Bandits (MAB) for various applications. Exploring the extension to Contextual Bandits, is absolutely fun and challenging. Dont worry if you dont understand the terms, in my presentation below, I have explained the concepts and how they work. 


## What is Azure Personalizer
Azure Personalizer enables the use of reinforcement learning for personalization with few lines of code!. It enables a full learning loop that runs at digital speed and learns from a simple reward score that optimizes your business’s goals. 



## Video:
In this video, I cover the why, what, and how behind the Azure personalization service with some basics for understanding how reinforcement learning works and how we easy it is to set up one for your needs.

{{< youtube A-8OfoWySHQ >}}


 You can get my presentation [from here](https://bit.ly/2YAqpKR)
 
 
## Code
The code that i used in the presentation can be found below. 

```python
from azure.cognitiveservices.personalizer import PersonalizerClient
from azure.cognitiveservices.personalizer.models import RankableAction, RewardRequest, RankRequest
from msrest.authentication import CognitiveServicesCredentials
from random import choice
import datetime, json, os, time, uuid

# Create personalized user experiences
# 50,000 transactions free per month
# Endpoint: https://westus2.api.cognitive.microsoft.com/personalizer/v1.0


# <AuthorizationVariables>
# key_var_name = 'PERSONALIZER_KEY'
# if not key_var_name in os.environ:
# 	raise Exception('Please set/export the environment variable: {}'.format(key_var_name))
# #personalizer_key = os.environ[key_var_name]
personalizer_key = 'Enter Your Key Here'

# For example: https://westus2.api.cognitive.microsoft.com/
personalizer_endpoint = 'https://YourPersonalEndpoint.cognitiveservices.azure.com/'
# </AuthorizationVariables>

# <Client>
# Instantiate a Personalizer client
client = PersonalizerClient(personalizer_endpoint, CognitiveServicesCredentials(personalizer_key))
# </Client>

 # <Load User Preferences>
userChoices = "foodChoices.json"
userpref = None

with open(userChoices) as handle:
    userpref = json.loads(handle.read())


def get_reward(timeofday, tastepreference, action):
    if userpref[timeofday][tastepreference] == action:
        return 1
    else:
        return 0


# <getActions>
def get_actions():
    action1 = RankableAction(id='pasta', features=[{"taste": "salty", "spice_level": "medium"},
                                                   {"nutrition_level": 5, "cuisine": "italian"}])
    action2 = RankableAction(id='ice cream',
                             features=[{"taste": "sweet", "spice_level": "none"}, {"nutritional_level": 2}])
    action3 = RankableAction(id='juice', features=[{"taste": "sweet", 'spice_level': 'none'}, {'nutritional_level': 5},
                                                   {'drink': True}])
    action4 = RankableAction(id='salad', features=[{'taste': 'salty', 'spice_level': 'none'}, {'nutritional_level': 2}])

    return [action1, action2, action3, action4]
# </getActions>

# <createUserFeatureTastePreference>
def get_user_preference(rand=False):
    res = {}
    taste_features = ['salty', 'sweet']

    if not rand:
        pref = input("What type of food would you prefer? Enter number 1.salty 2.sweet\n")

        try:
            ppref = int(pref)
            if (ppref <= 0 or ppref > len(taste_features)):
                raise IndexError
            res['taste_preference'] = taste_features[ppref - 1]
        except (ValueError, IndexError):
            print("Entered value is invalid. Setting feature value to", taste_features[0] + ".")
            res['taste_preference'] = taste_features[0]
    else:
        res['taste_preference'] = choice(taste_features)
        print("Randomly picked ", res['taste_preference'] + ".")
    return res
# </createUserFeatureTastePreference>

# <createUserFeatureTimeOfDay>
def get_user_timeofday(rand=False):
    res = {}
    time_features = ["morning", "afternoon", "evening", "night"]
    if not rand:
        time = input("What time of day is it (enter number)? 1. morning 2. afternoon 3. evening 4. night\n")
        try:
            ptime = int(time)
            if (ptime <= 0 or ptime > len(time_features)):
                raise IndexError
            res['time_of_day'] = time_features[ptime - 1]
        except (ValueError, IndexError):
            print("Entered value is invalid. Setting feature value to", time_features[0] + ".")
            res['time_of_day'] = time_features[0]
    else:
        res['time_of_day'] = choice(time_features)
        print("Randomly picked ", res['time_of_day'] + ".")
    return res
# </createUserFeatureTimeOfDay>


# <mainLoop>
# This is where the code is initalized and run for 500 times. As the reinforcement model learns, the predictions
# will improve. 
num_requests = 500
i = 1

while i <= num_requests:

    eventid = str(uuid.uuid4())

    context = [get_user_preference(rand=True), get_user_timeofday(rand=True)]
    actions = get_actions()

    # <rank>
    rank_request = RankRequest(actions=actions, context_features=context, event_id=eventid)
    response = client.rank(rank_request=rank_request)
    # </rank>

    print("Personalizer service ranked the actions with the probabilities listed below:")

    rankedList = response.ranking
    for ranked in rankedList:
        print(ranked.id, ':', ranked.probability)

    print("Personalizer thinks you would like to have", response.reward_action_id + ".")

    if (i % 50 == 0):
        print("Waiting for 2 minutes to allow for model update")
        time.sleep(120)

    # <reward>
    answer = get_reward(context[1]['time_of_day'], context[0]['taste_preference'], response.reward_action_id)
    print('Correct Choice' if answer == 1 else 'Incorrect Choice')
    client.events.reward(event_id=eventid, value=answer)

    print(" ")

    # </reward>
    i += 1
# </mainLoop>
```

Json File used in the demo code. 

```json
{
 "morning": {"sweet": "juice", "salty": "salad"},
 "afternoon": {"sweet": "juice", "salty": "pasta"},
 "evening": {"sweet": "juice", "salty": "salad"},
 "night": {"sweet": "ice cream", "salty": "salad"}
}
```

As you run this code, you will notice that the intial predictions are very poor. Once the model is able to capture additional data, the predictions improve. What is also not shown here is the percentage of exploration that the model will do. This would mean that even though the model know what is the correct prediction, it will spend about 10 to 20 percent of its predictions to explore additional choices. This helps in exploring if the user choices have changed or there are better rewards available that the model could not get before. 

{{< figure src="/images/posts/introduction_to_azure_personalizer/batch_recommendation_results.jpg" title="Results of the Batch Recommendations" >}}

If you have other ideas or want to discuss futher, look me up on [LinkedIn :(fab fa-linkedin):](https://www.linkedin.com/in/setuchokshi/), would be happy to connect and learn more about your applications. 