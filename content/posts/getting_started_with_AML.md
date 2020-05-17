---
title: "Getting started with Azure Machine Learning"
date: 2020-05-06T14:33:02+08:00
draft: false
author: "Setu Chokshi"
authorLink: ""
description: ""

tags: ["Starting up", "Machine Learning", "Azure"]
categories: ["Azure Machine Learning"]
featuredImage: "/images/posts/getting_started_with_AML/decisiontree.jpg"

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

## Introduction and Motivation 
If you are reading this then you probably understand the need and the importance of machine learning. You probably are itching to get started with machine learning. But everywhere you look, job boards, tutorials, etc. you find that there ask is always for a PhD or having expertise in statistics, linear algebra or programming or any combination of the three. Even when you want to just get started, the courses start off with a deep dive into Math. There is no doubt that the math is important, but when you are starting out, trying to understand and build intuition about the machine learning algorithms, simple explanations of the models help.

I recently attended the awesome Deep Learning Tutorials by Jeremy Howard and Rachel Thomas of [fast.ai](http://www.fast.ai/) . In the introduction they gave an analogy that has stuck in my mind. It goes this way:

Lets say when you were a kid and wanted to get started playing a sport, like football or even hit the ball. You went and told your parent that “Hey, can we get started with football”. Your parent did not go “Hey, before you hit your first ball, let me explain you the rules” Or “Let me teach you the physics of the ball movement” Or “Lets start to understand how this ball is made”. You, most likely, took a ball, went to the field and got started. No physics, no rules. You just got started. And once you started to love the sport, you learnt the rules. You learnt the physics. You learnt all the nitty gritty details that was to learn about the sport. But the first step was to build an interest in the sport.

I am hoping that these set of tutorials will help you develop an interest in machine learning and help you kick start you love and passion for the area. In the course of this blog series, we will build not only the intuition for the models, but take some time to understand the math, hopefully in a fun way. I will try to build most of the models in Excel to explain the math and how and why it works (where its possible).

And that is also the reason and my motivation to pick Azure Machine Learning (Azure ML) as the base to help you get started. I do not want to overwhelm you with the programming details, where most likely you will copy paste the code. But hopefully with Azure ML you will develop and gain an intuitive understanding to help you build your super awesome chat bot or a machine learning model.

Azure ML also allows you to take your model into production with a few clicks of a button. So in case you plan to continue towards using Azure ML, you can be assured that your learning and models won’t go to waste.

So lets get stared.


## 5 questions data science can answer for us
Before we get going, lets spend sometime to understand what machine learning can do for us. Though the number of applications are large, all of them can be grouped into 5 questions that data science can answer for us.

### 1. Is this A or B? or C or D…?
You would have seen a lot of examples of these types of algorithms in daily life. For example, when you click a picture, how does the app know that you are a male or female? Have you see the results from [how-old.net](https://how-old.net/)? With a fairly decent accuracy its able to predict whether the person is male or female and their age as well.

{{< figure src="/images/posts/getting_started_with_AML/howoldnet.jpg" title="Output of How-Old.Net" >}}

But if the person, as you see in the image above, changes their expressions, suddenly the tool goes from identifying the same person as a young female to an aged man. There are limitations to what classifications can do and we will cover them as we get deeper into the classification models. When classification is done between two classes, Is this A or B, its called binary classification or two class classification. When you have more than two classes its called multi-class classification (Is this A or B or C or D or…).


### 2. Is this weird?
This question is usually answered by a family of algorithms called Anomaly detection algorithms. In this you would want to (quickly) identify which are the weird data points that are not only rare and hard to find but they could have a material impact on the process. Lets take a couple of examples:
    * Am I being hacked? Out of the total network traffic, the intrusion traffic is going to much less. But if not detected, can wreck your critical infrastructure.
    * Detecting fraudulent transactions on your credit card to prevent abuse. Of the total transactions, the fraudulent transactions amount to a very small number.
    * Medical diagnoses to identify if the pixels represent a cancerous growth. Again, of the total pixels in an image, you will be looking at a very small number of pixels to identify cancerous cells.

Unlike classification algorithms, a metric of accuracy would not be a correct way to evaluate the model. Why? Lets take another example. Lets say you are trying to diagnose a disease that occurs only 1 in a million people. If you were using accuracy as your metric, then your best algorithm just says there is no disease as it will have the accuracy or 99.9999%. But that is incorrect. Instead we end up using a metric called Detection Rate (Recall). Recall can be defined as the ration between the correctly identified ‘anomalous’ events to the total number of ‘anomalous’ events. So in our example, the Recall would be 0 as we failed to identify the diseases in that one person.


### 3. How much? or How many?
When you are looking to predict a number to answer the questions of how much or how many, you will refer to the regression algorithm family. If you have heard about Linear regression or Ordinary Least Square (OLS) regression, then you have already know quite a bit about them. These have often been used for example:
    * What effect of price and promotion on the sales of a product?
    * How many parts of an engine will fail in next two years?
{{< figure src="/images/posts/getting_started_with_AML/weibull.jpg" title="Weibull curve [Source](http://www.weibull.com/hotwire/issue14/relbasics14.htm)" >}}



### 4. How is this organized?
When you want to understand how is the underlying data organized and you don’t have examples that you have know outcome for. Clustering algorithms help you gather a bunch of data and separate them into groups with similar properties or characteristics. You can then explore these groups to understand what makes them similar (or different). By exploring these groups you can then clearly extract the insights that will help you make better decisions.

These are useful when you are trying to recommending movies based on their viewing habits or even grouping your financial investments to ensure a diversified portfolio.

Clustering algorithms fall into a family of Unsupervised Learning. This means that the data is not labeled and does not have a known result. For example, in your clustering algorithm you can determine the number of groups your data will be clustered into but cannot dictate what the groups will be clustered into.


### 5. What should I do now?
When you are trying to determine what should I do now, you will want to explore the family of algorithms called “Reinforcement Learning Algorithms”. You would have (un)knowingly used these algorithms while training a pets. When they do a good thing, you give them a small treat. Or in a your day to day life where good behaviors are encouraged either verbally or monetarily.

{{< figure src="/images/posts/getting_started_with_AML/nutonomy.jpg" title="nuTonomy self driving car" >}}

Reinforcement algorithms learn as they go. They often learn based on the examples and training that is provided. For example, the famous AlphaGo played a lot of games against itself to help it determine the best possible move(s). In case of self driving car, it needs to make decision whether to stop or continue when it sees an object on the road.

How you ask a question is a clue to get you to the right family of algorithms that will help you answer your question. And yes make sure that you have the data to answer your question.

I am planning to the following algorithms in detail with the basics worked out in excel. We will also explore these algorithms in Azure Machine Learning to firm up your grasp on them.

 1. Classification Algorithms:
    * Decision Trees which will include decision forest, decision jungles and boosted decision tree.
    * Support Vector Machines
    * Logistic Regression.

2. Recommendation system algorithms: We will cover the data sets where we have explicit and implicit ratings data like movie ratings and come up with near state of the art results with the algorithm available on the Azure Machine Learning.

3. Regression algorithms.
    * Linear Regression
    * Decision Forest and Gradient Boosted Decision Tree (GBDT).

4. Anomaly detection
    * Support vector machines
    * K-Means

5. Reinforcement learning
    * Multi arm bandit algorithms. 
     
These cover 13 algorithms out of the 24 that are available in Azure Machine Learning platform. I have also included the 
reinforcement learning algorithm that is available as a service called [Azure Personalizer](https://azure.microsoft.com/en-us/services/cognitive-services/personalizer/). 

One more thing, a sharp eyed reader would have noticed that some regression algorithms are used for classifications and 
some of them have been used in various other families. Don’t worry, we will cover them as we go along and will explain 
the how’s and the why’s as well.