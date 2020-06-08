---
title: "NLP的Precision(查准率,精确率),Recall(查全率,召回率),Accuracy(准确率)以及综合评价指标(F1-Measure)"
date: 2020-06-05T11:47:25+08:00
draft: false
tags: ["nlp"]
categories: ["人工智能"]
---

一个很简单的例子：
我们训练了一个识猫模型，送一张图片给模型，模型就能告诉你该图片是否有猫。目标是找出所有有猫图片。

下面送100张有猫的图片，和100张无猫的图片给模型。假设预测的结果如下：

| 例子|图片有猫|图片没有猫 |
| -- | -- | -- |
|模型识别有猫（Positive）|90|5|
|模型识别没有猫（Negative）|10|95|

下面定义四个参数：

TP(True Positives): 模型识别有猫，实际图片有猫，在例子中是90

FP(False Positives): 模型识别有猫，实际图片没猫，在例子中是5

TN(True Negatives): 模型识别没猫，实际图片没猫，在例子中是95

FN(False Negatives): 模型识别没猫，实际图片有猫，在例子中是10


1、$$Precision=\frac{TP}{TP+FP}$$ ：表示 $$\color{red}{正确被识别到有猫的图片}$$ 占$$\color{red}{所有被识别到有猫的图片}$$的比例。该例为：$$\frac{90}{90+5}$$

2、$$Recall=\frac{TP}{TP+FN}$$：表示$$\color{red}{正确被识别到有猫的图片}$$ 占$$\color{red}{所有含有猫的图片}$$的比例。该例为：$$\frac{90}{90+10}$$

3、$$Accuracy=\frac{TP+TN}{TP+FP+TN+FN}$$：表示$$\color{red}{模型预测正确}$$的比例。该例为：$$\frac{90+95}{90+5+95+10}$$

4、$$F1=\frac{2\*Pre*Rec}{Pre+Rec}=\frac{2\*TP}{2\*TP+FP+FN}$$：表示精确值和召回率的调和均值