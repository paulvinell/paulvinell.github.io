---
layout: post
title:  "Data-less knowledge extraction from models?"
date:   2020-06-01 15:23:00 +0200
categories: research
---
After reading about knowledge distillation in ["Rethinking Few-Shot Image Classification: a Good Embedding Is All You Need?"][few_shot_study] my interests turned towards the method. Knowledge distillation is a technique that allows you to compress knowledge from a larger model into a smaller one. The larger model acts as a teacher and the smaller model acts as a student. When given an input, the student is commonly taught by minimizing the weighted average of two different cost functions. The first cost function is based on how its answer compares to the teacher, and the other is based on a comparison to the correct label.

For me, this research inspired one primary question:  
Will a student that makes all the same mistakes as its teacher also know the same things?

## Training
I try out two techniques:
1. Teaching with random noise. Here I generate 32 images of noise and train based on these.
2. Teaching with adversarial noise. This is noise that is crafted to fool the teacher to classify the image as some object category with high probability. The hope with this was that this would make it easier for the student to learn patterns associated with each object category.

### Steps

1. The teacher model is trained normally on a multi-way classification task.
2. A student model is instantiated.
3. Knowledge extraction loop.
    1. Generate adversarial noise that fools the teacher.
    2. Train the student to answer like the teacher.
    3. (Evaluate progress)

### Model and data
For this project I used ResNet-12 for both my student and teacher models (code taken from [here][resnet_code]). This is because I wanted something fairly standard and powerful, but I still wanted to be able to run it on my computer directly. I trained on CIFAR-10 and MNIST because they are also quite standard (and not too computationally expensive). I used the Adam optimizer with categorical cross entropy loss.

## Experiments
I did not conduct a lot of experiments, and of those experiments I only recorded a few. This means that you should not take the details of the data at face value. The specifics of the results could be due to chance.

For each dataset, a summary table is provided and for each technique the progress of the validation loss and accuracy is shown. In this experiment, an epoch consists of 30 update steps, and each update step is based on 32 novel images of adversarial noise. The best results are bolded.

### CIFAR-10
With its small, natural images CIFAR-10 is not the easiest dataset to master. It is not uncommon for me to not be able to tell which object category is depicted in an image. Here is a summary of how the different models performed:

||starting loss|starting accuracy|best loss|best accuracy|
|---|---|---|---|---|
| **teacher** |--|--|0.9750|0.7352|
| **student (random)** |5.0351|0.0992|2.7919|0.0978|
| **student (adversarial)** |3.6181|0.1034|2.6626|0.1178|

#### Random noise

What's interesting in these results is that this we initially get a decrease in validation loss, but then it increases again. One hypothesis I have is that this could be because most random labels result in an even probability distribution. In turn, this could have the effect of making the network have all its input space map to even probability outputs and therefore overfit on the validation data. But then, why the initial decrease in validation loss? My guess is that we are extracting knowledge, but the force making the output probabilities even "catches up" after a while.

|epoch|loss|accuracy|
|---|---|---|
0|5.0351|0.0992|
1|2.9738|0.0934|
2| **2.7919** |0.0952|
3|2.8365|0.0756|
4|2.9195|0.0956|
5|3.0133| **0.0978** |
6|3.1314|0.0978|
7|3.2071|0.0978|
8|3.3038|0.0978|
9|3.3801|0.0978|
10|3.4699|0.0978|

#### Adversarial noise

These results are also interesting. In general and in these results, adversarial noise tends to achieve a little better loss than the random noise. However, in comparison to the random noise, here it seems that training with adversarial noise makes the validation loss plateau rather than overfit.

|epoch|loss|accuracy|
|---|---|---|
0|3.6181|0.1034|
1|2.7369|0.0964|
2|2.6707|0.0954|
3| **2.6626** |0.1008|
4|2.6656|0.1050|
5|2.6763|0.1086|
6|2.6878|0.1066|
7|2.6783|0.1120|
8|2.6683|0.1142|
9|2.6811| **0.1178** |
10|2.6995|0.1110|

### MNIST
Here I had to comment out the average pooling layer at the end, otherwise MNIST images were too small for the network. My intuition said that MNIST should be easier because its object categories are less complex and its input dimensionality is a factor 4 smaller. This does not seem to be the case, however. Below is a summary of how the models performed:

||starting loss|starting accuracy|best loss|best accuracy|
|---|---|---|---|---|
| **teacher** |--|--|0.1209|0.9839|
| **student (random)** |3.6675|0.1356|3.2609|0.1028|
| **student (adversarial)** |3.8050|0.0780|2.6626|0.2088|

#### Random noise

The results from this run surprised me. The model made little progress in comparison to experiments run on CIFAR-10. We again see the loss first decreasing and then increasing.

|epoch|loss|accuracy|
|---|---|---|
0|3.6675|0.1356|
1|  **3.2609** |0.0762|
2|3.5433|0.0900|
3|3.7826|0.0978|
4|4.0176|0.0990|
5|4.2569|  **0.1028** |
6|4.4911|0.1023|
7|4.7113|0.1012|
8|4.9375|0.1012|
9|5.1158|0.0982|
10|5.3035|0.1005|

#### Adversarial noise

I loved these results. For once, the network actually seems to be learning somewhat significantly. From these results I also noted another trend which is that it seems like peak validation accuracy and peak loss don't have the greatest correlation. What's noteworthy here is that accuracy increased stably from epoch 0 to 39 before quickly dropping. Another noteworthy thing is that the validation loss was also increasing, which seems contradictory.

Note:  
The validation accuracy does not always seem to improve this much, but it consistently improves at least somewhat.

|epoch|loss|accuracy|
|---|---|---|
0|3.8050|0.0780|
1|2.8685|0.1072|
2|2.8731|0.1163|
3|3.0068|0.1123|
4| **2.9638** |0.1448|
5|3.0876|0.1420|
6|3.2152|0.1440|
7|3.4385|0.1445|
8|3.5993|0.1452|
9|3.6124|0.1502|
10|3.7232|0.1538|
...|...|...|
35|8.5899|0.1835|
36|8.6761|0.1910|
37|8.9571|0.1972|
38|8.9700|0.2090|
39|9.1751| **0.2088** |
40|9.2401|0.1775|
41|10.0174|0.1583|
42|10.4065|0.1470|

## Thoughts

It seems that knowledge extraction with adversarial noise can elicit positive changes in validation accuracy. At the same time it also seems to significantly increase the validation loss. The validation accuracy seems to be somewhat divorced from the loss.

Knowledge extraction with random noise does not seem to fare as well. It seems to be able to lower the validation loss somewhat, but has no clear benefits in terms of validation accuracy.

I'm a little surprised that this actually works (kind of).

### Future work
I think the greatest drawback to this method is the lack of structure in the noise. I have a feeling that images from different datasets could elicit greater improvements to the loss than noise, even if they depict entirely different object categories.

## Project code
The code for this project can be found [here][knowledge_extraction_noise].

[few_shot_study]: https://arxiv.org/abs/2003.11539v1
[resnet_code]: https://keras.io/examples/cifar10_resnet/
[knowledge_extraction_noise]: https://github.com/paulvinell/Knowledge_Extraction_Noise
