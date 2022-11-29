# Arbitrary style transfer in TensorFlow.js

This repository contains an implementation of arbitrary style transfer running fully
inside the browser using TensorFlow.js.

Demo website: https://reiinakano.github.io/arbitrary-image-stylization-tfjs

Blog post with more details: https://magenta.tensorflow.org/blog/2018/12/20/style-transfer-js/

### Stylize an image

![stylize](readme_img/stylize.jpg)

### Combine styles

![combine](readme_img/combine.jpg)

## FAQ

### What is this?

This is an implementation of an arbitrary style transfer algorithm
running purely in the browser using TensorFlow.js. As with all neural 
style transfer algorithms, a neural network attempts to "draw" one 
picture, the Content (usually a photograph), in the style of another, 
the Style (usually a painting). 

Although [other browser implementations](https://github.com/reiinakano/fast-style-transfer-deeplearnjs)
of style transfer exist,
they are normally limited to a pre-selected handful of styles, due to
the requirement that a separate neural network must be trained for each
style image.

Arbitrary style transfer works around this limitation by using a
separate *style network* that learns to break down *any* image into 
a 100-dimensional vector representing its style. This style vector is 
then fed into another network, the *transformer network*, along
with the content image, to produce the final stylized image.

I have written a [blog post](https://magenta.tensorflow.org/blog/2018/12/20/style-transfer-js/) 
explaining this project in more detail.

### Is my data safe? Can you see my pictures?

Your data and pictures here never leave your computer! In fact,
this is one of the main advantages of running neural networks 
in your browser. Instead of sending us your data, we send *you* 
both the model *and* the code to run the model. These are then 
run by your browser.

### What are all these different models?

The original paper uses an Inception-v3 model 
as the style network, which takes up ~36.3MB 
when ported to the browser as a FrozenModel.

In order to make this model smaller, a MobileNet-v2 was
used to distill the knowledge from the pretrained Inception-v3 
style network. This resulted in a size reduction of just under 4x,
from ~36.3MB to ~9.6MB, at the expense of some quality.

For the transformer network, the original paper uses 
a model using plain convolution layers. When ported to
the browser, this model takes up 7.9MB and is responsible
for the majority of the calculations during stylization.

In order to make the transformer model more efficient, most of the
plain convolution layers were replaced with depthwise separable 
convolutions. This reduced the model size to 2.4MB, while
drastically improving the speed of stylization.

This demo lets you use any combination of the models, defaulting
to the MobileNet-v2 style network and the separable convolution
transformer network.

### How big are the models I'm downloading?

The distilled style network is ~9.6MB, while the separable convolution
transformer network is ~2.4MB, for a total of ~12MB. 
Since these models work for any style, you only 
have to download them once!

### How does style combination work?

Since each style can be mapped to a 100-dimensional 
style vector by the style network,
we simply take a weighted average of the two to get
a new style vector for the transformer network.

This is also how we are able to control the strength
of stylization. We take a weighted average of the style 
vectors of *both* content and style images and use 
it as input to the transformer network.

## Running locally for development

This project uses [Yarn](https://yarnpkg.com/en/) for dependencies.

To run it locally, you must install Yarn and run the following command at the repository's root to get all the dependencies.

```bash
yarn run prep
```

Then, you can run

```bash
yarn run start
```

You can then browse to `localhost:9966` to view the application.


## Credits

This demo could not have been done without the following:

* Authors of the [arbitrary style transfer](https://arxiv.org/abs/1705.06830) paper.
* The Magenta repository for [arbitrary style transfer](https://github.com/tensorflow/magenta/tree/master/magenta/models/arbitrary_image_stylization).
* Authors of [the MobileNet-v2 paper](https://arxiv.org/abs/1801.04381).
* Authors of the paper describing [neural network knowledge distillation](https://arxiv.org/abs/1503.02531).
* The [TensorFlow.js library](https://js.tensorflow.org).
* [Google Colaboratory](https://colab.research.google.com/), with which I was able 
to do all necessary training using a free(!) GPU.

As a final note, I'd love to hear from people interested 
in making a suite of tools for artistically manipulating images, kind of like 
[Magenta Studio](https://magenta.tensorflow.org/studio)
but for images. Please reach out if you're planning to build/are 
building one out!
# 基于TensorFlow.js的画风迁移

本项目是基于[TensorFlow.js](https://js.tensorflow.org/)的纯览器端画风迁移算法实现。

Demo地址：https://reiinakano.github.io/arbitrary-image-stylization-tfjs

### 单风格迁移

![stylize](readme_img/stylize.jpg)

### 多风格迁移

![combine](readme_img/combine.jpg)

## 常见问题

### 概述

这是一个基于TensorFlow.js完全运行在浏览器端的画风迁移算法实现。跟所有基于神经网络的画风迁移算法一样，神经网络试图去“画”一张画，画的内容源自一张图（通常是一张照片），画的风格源自另一张图（通常是一幅画）。

虽然也有其他[浏览器端画风迁移实现](https://github.com/reiinakano/fast-style-transfer-deeplearnjs)，但它们往往受限于少量预置画风，因为每种画风都需要提前训练好一个与之对应的神经网络模型。

本项目通过一个将*任意*画风表示为100维向量的*画风网络*来突破这个限制，该向量跟照片内容一起注入*迁移网络*，产生最终的画风图像。

### 我的数据安全吗？你能看到我的图片吗？

您的数据和照片从未离开您的电脑！事实上，这是在浏览器端运行神经网络的主要优点之一。不是将您的数据发送给我们，而是我们将模型和运行模型的代码发送给您的浏览器，并由它来执行。

### 这些模型间有哪些不同？

原论文采用Inception-v3模型作为画风网络，该模型固化并发送至浏览器时体积达36.3MB。

为减小模型体积，我们将训练好的Inception-v3模型浓缩为MobileNet-v2模型，在模型质量略有牺牲的情况下，模型体积从36.3MB降到了9.6MB，减小了近四倍。

对于迁移网络，原论文采用基于普通卷积层的模型。该模型发送至浏览器时体积为7.9MB，且画风渲染运算中占很大比重。

为使迁移网络更高效，绝大多数的普通卷积层被深度可分离卷积层代替。这使得模型体积降至2.4MB，同时大幅提升了画风渲染速度。

本demo使用组合模型，默认使用的是MobileNet-v2画风网络和可分离卷积迁移网络。

### 下载的模型有多大？

压缩后的画风网络模型是9.6MB，可分离卷积迁移网络是2.4MB，总共是12MB。您只需要一次性下载这两模型，即可用于任意画风渲染。

### 画风组合是怎么实现的？

由于每种画风可通过画风网络转化为一个100维的画风向量，我们简单求两个画风向量的加权平均值，即可得到一个新的画风向量供迁移网络使用。

这也是我们能够控制画风渲染强度的原因，我们求画风图像向量和内容图像向量的加权平均值，并把结果作为迁移网络的输入。

## 本地开发调试

本项目采用[Yarn](https://yarnpkg.com/en/)为依赖管理工具

本地运行，您需要先安装Yarn，并在项目的根目录运行下面的命令来获得所有依赖包。

```bash
yarn run prep
```

然后，接着运行

```bash
yarn run start
```

在浏览器键入`localhost:9966`来访问本应用。

## 鸣谢