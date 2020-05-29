---
layout: page
title: One-shot learning
permalink: /research/one_shot.html
---
This page outlines the work Adam Wiker and I did for our bachelor thesis during our spring semester in 2020.

## The basics
For our examination Adam Wiker and I evaluated a model for one-shot learning I theorized. The basic idea is that by splitting a model into two parts we can task the first part with producing powerful vector representations of the data, and the last part can use that representation to perform classification on the data. In our case, classification is checking the presence of an object category. To train the representational ability of the first part, we create several classifiers and assign each to an object category. Training is done by repeatedly switching which classifier we are currently training. The hope was that this would force the first part to accommodate all classifiers, thus creating a more general representation of the provided images. This first part could then be reused and train a classifier for a novel object category with less data.

Here is a broad overview of the schematics of the model. The input first enters the CNN (tasked with building a representation) and is then passed on to the classifier (can be swapped):
![Model overview][model_img]

Unfortunately, the exact training method did not produce the general adaptations we hoped for. However, we showed that shallow artificial neural networks learn surprisingly well with little data when you introduce image augmentation and regularization.

### Surprises
Just weeks before publication of this report, I found out about a similar study published March 25. This study has uncanny similarity to our own work. It is based on much of the same ideas we have had (and trains the first part with a method described in the section *Future work* in our report) and achieves state-of-the-art performance in few-shot learning. More on this <ins>[in this blog post][one_shot_post]</ins>.

## Report
To view our report <ins>[click here][kex_report]</ins>.

### Cite
If you want to cite this report, here is a bibtex entry:
{% highlight none %}
@misc{vinellwiker2020oneshot,
    title={One-shot learning through generalized representations with neural networks},
    author={Paul Vinell and Adam Wiker},
    year={2020},
    journal={DiVA}
}
{% endhighlight %}

[one_shot_post]: {% post_url 2020-05-28-one-shot-surprise %}
[model_img]: /assets/img/model_broad.png
[kex_report]: /assets/pdf/kex_one_shot_learning.pdf
