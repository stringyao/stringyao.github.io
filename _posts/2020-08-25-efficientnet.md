---
layout: post
title:  "EfficientNet: Rethinking Model Scaling for Convolutional Neural Networks"
date:   2020-08-25
excerpt: "Carefully balancing network depth, width, and resolution can lead to better performance."
image: "/images/efficientnet.png"
---

### TLDR

在CV领域，scale up卷积神经网络是提升其表现的一种方法，比如将ResNet18扩展至ResNet200。人们通常是把网络往更深或更宽的方向扩展，也有人尝试提高图片分辨率。Mingxing Tan和Quoc V. Le提出以一个固定比例(compound coefficient)同时扩展这三者来得到最优效果：if we want to use $2^{N}$ times more computational resources, then we can simply increase the network depth by $\alpha^{N}$ , width by $\beta^{N}$ , and image size by $\gamma^{N}$ , where $\alpha$, $\beta$,  $\gamma$ are constant coefficients determined by a small grid search on the original small model。作者通过神经网络结构搜索(neural architecture search)得到一个新的基础网络，并用他们提出的方法将其扩展成了一族网络，称作EfficientNets。

![scaling method](/images/compound_coef.png#center) 

### Why it works?

图片更大->需要更多卷积层来增加感受野，需要更多通道来捕捉细节

Intuitively, the compound scaling method makes sense because if the input image is bigger, then the network needs more layers to increase the receptive field and more channels to capture more fine-grained patterns on the bigger image.

### Tradeoff between depth and width 

Deeper ConvNet can capture richer and more complex features, and generalize well on new tasks. However, deeper networks are also more difficult to train due to the vanishing gradient problem. Also, the accuracy gain of very deep network diminishes. 

Scaling network width is commonly used for small size models. Wider networks tend to be able to capture more fine-grained features and are easier to train. However,
extremely wide but shallow networks tend to have difficulties in capturing higher level features. 

### Oberservations

- Scaling up any dimension of network width, depth, or resolution improves accuracy, but the accuracy gain diminishes for bigger models.
- In order to pursue better accuracy and efficiency, it is critical to balance all dimensions of network width, depth, and resolution during ConvNet scaling.

### Results

The model with compound scaling tends to focus on more relevant regions with more object details, while other models are either lack of object details or unable to capture all objects in the images.


