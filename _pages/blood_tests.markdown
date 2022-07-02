---
layout: page
title: Error detection in blood work
permalink: /research/blood_tests.html
---
This page outlines the work that I did for my Master's thesis during my spring semester in 2022.

## The basics
One of the companies that I contacted for a spot to do my Master's thesis at was Werlabs AB. I corresponded with Jens Stjernström, then CTO for Werlabs, who told me about one of the problems that they were facing. As a preventative healthcare company, Werlabs offers inviduals the ability to do private health checkups through blood tests. The company employs its own physicians, but outsources the measuring of blood tests to third party laboratories. Sometimes these laboratories will send back incorrect results. It is often not possible to know definitely whether results are erroneous or not. The role of this study was to examine ways of performing error detection in these blood tests. In practice this was done by comparing different types of neural networks using simulated errors on the blood test data.

![Example diagram][simple_diagram]

## Report
To view my report <ins>[click here][ex_report]</ins>.

### Cite
If you want to cite this report, here is a bibtex entry:
{% highlight none %}
@misc{vinell2022bloodwork,
    title={Error detection in blood work},
    author={Paul Vinell},
    year={2022},
    journal={DiVA}
}
{% endhighlight %}

[simple_diagram]: /assets/img/simple_diagram.png
[ex_report]: /assets/pdf/ex_werlabs.pdf
