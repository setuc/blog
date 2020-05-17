---
title: "Inside a neural network: What is it thinking?"
date: 2020-05-08T23:04:39+08:00
draft: false
author: "Setu Chokshi"
authorLink: ""
description: "Inside a Neural Network"

tags: ["Deep Learning", "Computer Vision", "Visualization"]
categories: ["Visualization"]
featuredImage: "/images/posts/inside_a_neural_network/building-dreams.jpg"

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
Deep convolution neural networks are often thought about as black boxes and often limited understanding of their performance. 
We often train them by showing them thousands of images of a class, hoping that just like humans, they will learn distinctive
features from the photos that will help it classify the image back to its class. So how do you know that the convolution 
neural network (CNN) did its tasks? You do that by visualizing the layers of the network weights. There have been a lot 
of papers that have been written on the topic. 

Since 2013, from one of the earliest paper I still remember reading, was from  Zeiler and Fergus [^1] where the authors 
had discussed a simple visualization technique to understand what the networks are learning. In their paper, they had 
shown what the deep neural network, AlexNet in the paper, was learning. They found that the initial layers were learning 
simple features, which increase in the complexity as one navigates down to subsequent layers. They also found that model 
results are dependant on the structure of the images (e.g., orientation of the face, etc.), and the accuracy of the model 
can be improved by data augmentation, which they demonstrate in their paper. I guess zooming in on the inner workings of 
the neural network enabled us to understand what the network is learning and how we can use that to improve the final 
accuracy of the model. 

{{< figure src="/images/posts/inside_a_neural_network/visualization-of-neural-network.jpg" title="Visualization of features in a fully trained model" >}}

Though this makes sense after the fact, for example in the ICLR 2019 paper of Geirhos et al. [^2], they mentioned that 
Imagenet trained networks like ResNet are prone to classify the images based on the local textures rather than shapes as 
the earlier papers theorized. See the example of the paper. In the figure below, they took an example of a ring-tailed lemur 
to which they transformed into various images using style transfer. With this, they maintained the global structure of 
the image while removing its textures. None of the style transferred Lemur got classified back to its class. I wrote about
the paper in [this post](https://pipinstall.me/paper_imagenet_texture_geirhos)
 
{{< figure src="/images/posts/inside_a_neural_network/style-transfer-lemur.jpg" title="Stylized Lemur, no longer predictive of its class" >}}

They make a strong point, and we will explore the papers and their findings in the series of posts. 

## Methods
Since then, a lot of research efforts have guided their efforts to visualizing the neural networks. In the research so far
there have been two main methods that have come up:
* Images that maximize neural activation
* Using backpropagation to generate saliency image features

There have been others where they have use dimensionality reduction techniques to determine its location using the weights
from the AlexNet network[^3].
 
I am sure there are other techniques as well, but once I start going through the papers, I will keep updating them here. 
I will continue to write about these techniques in detail. 

_Update_: There was a fascinating discussion by the authors[^7] discussed that the backpropagation based methods are not 
doing the explanation. Instead, they are reconstructing the images, which is nothing to do with how the network arrived 
at the classification decision. 

## Activation Atlas: A More Recent Work
In all the methods until recent times, focus on a single input image, and there was no way to understand a global picture.
The authors from Google and OpenAI recently published their work that can be found here [^4]. In this, they took one random
activation from one million images. Each activation vector was 512 dimensions, to which they perform feature visualization, 
a technique pioneered by Chris Olah [^5] to obtain the [Activation Atlas](https://distill.pub/2019/activation-atlas/app.html).

{{< figure src="/images/posts/inside_a_neural_network/activation-atlas.jpg" title="Activation atlas of the InceptionV1" >}}

If you start to explore the Atlas, you will notice that there are individual activations that can be noticed from a given 
class, which later on combine to form more complex activations. Similar to the 2013 paper of Zeiler and Fergus. 

Later in work, they also create a class activation atlas, in which they remove all activations that are not contributing
to a specific class. They then try to compare two related classes and find that some detectors are playing a dual role,
one for identifying the baseball and one for the great white shark's teeth. Upon adding the image, in this case of a baseball, 
the classification of the grey whale turns that to a great white shark. 

{{< figure src="/images/posts/inside_a_neural_network/mixed5b-InceptionV1.jpg" title="Baseball or great white shark's teeth" >}}

These are pretty exciting findings that enable you to peer into the workings of the neural network. One question that came 
to my mind as I went through the paper is now what I can do to address it? The baseball and white shark is an extreme example, 
but what can we do to make the models robust. One thought that comes to mind is the use of adverserial networks. The other 
is to use various augmentation techniques like Cut-Mix Augmentation[^6]. I haven't tried them yet, but will do so in the 
coming weeks.  

## Generating your activation atlas
I was able to reproduce some code from the authors and played around with the layers and create activation atlas. The
results are pretty interesting. As you as see that the top left is outline of the person while at the bottom left shows 
the dogs face. Very interesting. Time to play with the models and various augmentation techniques to see how these structure
changes.  

{{< figure src="/images/posts/inside_a_neural_network/activation-atlas-inception.jpg" title="Generated Activation Atlas for Inception v1" alt="activation atlas for Inception">}}

[^1]:Zeiler, M. D., & Fergus, R. (2013). Visualizing and Understanding Convolutional Networks. Retrieved from https://arxiv.org/abs/1311.2901
[^2]: Geirhos, R., Rubisch, P., Michaelis, C., Bethge, M., Wichmann, F. A., & Brendel, W. (2018). ImageNet-trained CNNs are biased towards texture; increasing shape bias improves accuracy and robustness. Retrieved from http://arxiv.org/abs/1811.12231
[^3]: t-SNE visualization of CNN codes, https://cs.stanford.edu/people/karpathy/cnnembed/
[^4]: Exploring Neural Networks with Activation Atlases, https://distill.pub/2019/activation-atlas/
[^5]: Feature Visualization, https://doi.org/10.23915/distill.00007
[^6]: Yun, S., Han, D., Oh, S. J., Chun, S., Choe, J., & Yoo, Y. (2019). CutMix: Regularization Strategy to Train Strong Classifiers with Localizable Features. Retrieved from http://arxiv.org/abs/1905.04899
[^7]: Nie, W., Zhang, Y., & Patel, A. B. (2018). A theoretical explanation for perplexing behaviors of backpropagation-based visualizations. 35th International Conference on Machine Learning, ICML 2018, 9, 6105–6114. Retrieved from https://arxiv.org/abs/1805.07039