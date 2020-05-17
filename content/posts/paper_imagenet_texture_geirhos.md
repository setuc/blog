---
title: "[Paper] ImageNet-trained CNNs are biased towards texture"
date: 2020-05-12T16:01:29+08:00
draft: false
author: "Setu Chokshi"
authorLink: ""
description: "Inside a Neural Network"

tags: ["Deep Learning", "Computer Vision", "Visualization", "Technical Paper", "Data Augmentation"]
categories: ["Visualization"]
featuredImage: "/images/posts/paper_imagenet_texture_geirhos/textured-images.jpg"

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
In the previous [post](https://pipinstall.me/inside_a_neural_network/) we briefly reviewed the work done by the Google 
Brain authors [^1], in which they had mentioned the paper from Geirhos et al. [^2]. In this paper the authors discuss
That the object textures play a more prominent role in what CNN can classify. Let us test this, in the image above,can 
you make out what the three images are? If you got them as bear, car, and a cat, you are correct? The human brain is 
able to ignore the textures and still be able to figure out what the image is about? How do you think the neural networks 
would do? A pre-trained InceptionV3 model results the results as toyshop (imagenet class = 865), racer (imagenet class = 751), 
and indian elephant (imagenet class = 385). The author's reason that humans can distinguish the shape, and though theorized 
earlier, the CNN's still rely on the texture.

## The tale of two hypotheses
The authors were trying to resolve the two contradictory hypotheses, 
a.	CNN's are combining low-level features to from increasingly complex shape to be able to classify an object
b.	Object textures are more important than the shapes, mostly starting from the Style Transfer work done by Gatys et al. [^3].

To test the initial hypothesis, they selected 160 images that have been correctly classified by the 4 CNNs (AlexNet, 
GoogLeNet, aka Inception v1, VGG-16, and ResNet-50). They created a grayscale, silhouette, and edges of the images. They 
had also selected about 48 images for textures that consisted of full-width patches of the animal. They also generated 
style transfer images for the selected categories. They found that CNN's could not correctly classify textured filled 
categories, unlike humans from whom the shape was enough.

## Overcoming the bias - the results
They create a new model called Shape-ResNet, which is a vanilla ResNet-50 trained with Stylized ImageNet (SIN), and 
ImageNet (IN) images. They performed the fine-tuning using only the ImageNet images. They found that such stylized 
augmentation results in higher validation accuracy to a regular ResNet. They also found that such training improved the 
performance of the Faster R-CNN object detection model. They even tested their model on ImageNet-C, which is a comprehensive 
benchmark of 15 different image corruptions and found that it reduced the corruption error from 76,7 to 69.3, which is a 
marked improvement over the base. This while making sure that they do not use any of the corrupted images in the network 
training. 

| name           | training               | top-1  | top-5 |
|----------------|------------------------|--------|-------|
| vanilla ResNet | IN                     | 76.13  | 92.86 |
|                | SIN                    | 60.18  | 82.62 |
|                | SIN+IN                 | 74.59  | 92.14 |
| Shape-ResNet   | SIN+IN, IN finetunning | 76.72  | 93.28 |



## Practical Applications
While reading this paper I was trying to find out there were any references to ready to use data augmentation libraries
but couldn't find any. Instead, I stumbled across a very interesting paper "_Style Augmentation: Data Augmentation via Style 
Randomization_" [^4]. Both of the papers seem to be released around the same time. They had shown similar results of 
using style transfer as a way of improving the network performance. The authors had also released the source code [^5]. 
Probably I will cover more about this paper in a later blog post. Using their code, it would be interesting to see the 
impact of a complex augmentation scheme on the training time. Though in terms of practical performance, there in not a
significant improvement, but certainly makes the network more robust. 

## Next Round (maybe)
Texture based augmentation has really perked my interest and one of the papers that I would want to read up more was on 
the "_One-shot Texture Segmentation_"[^6]. 

### Code to reproduce the examples
I have uploaded the example notebook on the Azure Notebooks site. You can find the complete code here:
[https://notebooks.azure.com/setuchokshi/projects/stylized-imagenet-predictions](https://notebooks.azure.com/setuchokshi/projects/stylized-imagenet-predictions)

```python
from torchvision import models, transforms
import torch
from PIL import Image
import pandas as pd

classes = pd.read_csv("imagenet_classes.csv")
# Feel free to play around with the pretrained models in PyTorch
inception = models.inception_v3(pretrained=True)
transform = transforms.Compose([            
     transforms.Resize(256),                    
     transforms.CenterCrop(224),                
     transforms.ToTensor(),                     
     transforms.Normalize(                      
     mean=[0.485, 0.456, 0.406],                
     std=[0.229, 0.224, 0.225]              
 )])
# The images are from the author's repository
# https://github.com/rgeirhos/texture-vs-shape
img = Image.open("car4-clock1.png")
batch = torch.unsqueeze(transform(img), 0)
inception.eval()
result = torch.max(inception(batch),1).indices
print(classes.loc[classes['index'] == result.item()])

```

[^1]: Exploring Neural Networks with Activation Atlases, https://distill.pub/2019/activation-atlas/
[^2]: Geirhos, R., Rubisch, P., Michaelis, C., Bethge, M., Wichmann, F. A., & Brendel, W. (2018). ImageNet-trained CNNs are biased towards texture; increasing shape bias improves accuracy and robustness. Retrieved from http://arxiv.org/abs/1811.12231
[^3]: Gatys, L. A., Ecker, A. S., & Bethge, M. (n.d.). Image Style Transfer Using Convolutional Neural Networks.
[^4]: Jackson, P. T., Atapour-Abarghouei, A., Bonner, S., Breckon, T., & Obara, B. (2018). Style Augmentation: Data Augmentation via Style Randomization. Retrieved from http://arxiv.org/abs/1809.05375
[^5]: https://github.com/philipjackson/style-augmentation
[^6]: Ustyuzhaninov, I., Michaelis, C., Brendel, W., & Bethge, M. (2018). One-shot Texture Segmentation. Retrieved from http://arxiv.org/abs/1807.02654
