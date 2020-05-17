---
title: "Critical Learning Periods for Deep Learning"
date: 2020-05-17T21:03:14+08:00
draft: false
author: "Setu Chokshi"
authorLink: ""
description: ""

tags: ["Deep Learning", "Computer Vision", "Optimization"]
categories: ["Optimization"]
featuredImage: "/images/posts/critical_learning_deep_learning/cat_with_cataract_and_normal_eyes.jpg"

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
While researching the paper on End-to-End Incrementatl Learning [^1]I somehow ended on this wonderful video by Stefano 
Soatto on the "Dynamics and Control of Differential Learning".  The video is linked below:

{{< youtube WdjJEfUifJs>}}

## Key Themes
In this video, he started with the topic about cats and cataracts.  In the study done by the folks at Harvard, they found 
that depending on when you fix the cats cataract, the visual acuity of the cat is different.  If there is a delay in 
fixing the vision for say  10 days, 20 days, the cat will never be able to restore a healthy and normal vision. Hence 
these are called critical learning periods. They try to perform a similar experiment or a neural network using CIFAR 
images.  To simulate the effect of cataract deplored the images at the beginning of the training of the neural network, 
and then they restored the sharp images and resumed the training.  They found similar results as that for the cat.  
Depending upon the epoch, at which the sharp images were restored, the test accuracy of the model decreased from over 
90% to about 82% at 140 epochs.

{{< figure src="/images/posts/critical_learning_deep_learning/effect_of_blurred_images_in_cnn_training.jpg" 
    title="Effect of blurred images on test accuracy of the final model" >}}  

They also perform another experiment starting with sharp images.  They introduced blurred images for about 40 epochs and 
then continue the training again with sharp images.  They found similar results of degrees in the test accuracy of the 
model.  The authors concluded that a shot deficit epoch at 40 is enough to damage the network permanently.  
Pretty powerful. Probably over the next couple of days, I will try to reproduce the methodology of the authors and see 
if I get similar results.

## Conclusion
This was undoubtedly a unique video.  One of the response by Soatto towards the end of the video to the audience question 
draws the conclusion very effectively:
{{< admonition type=quote title="Stefano Soatto" open=True >}}
we do pre training and then we do fine tuning this is the way which most deep learning works today and you would think 
then training on classifying slightly blurring CIFAR images would help you to then classify sharp see five images but 
in fact not only doesn't help you it hurts you you can never learn you would be better off learning from scratch.
{{< /admonition >}}




[^1]: Castro, F. M., Marín-Jiménez, M. J., Guil, N., Schmid, C., & Alahari, K. (2018). End-to-End Incremental Learning. Retrieved from http://arxiv.org/abs/1807.09536