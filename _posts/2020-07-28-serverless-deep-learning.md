---
layout: post
title:  "Serverless Deep Learning"
date:   2020-07-28 07:18:50 +0200
categories: digitalization serverless
---
Stateless functions are ideal to be deployed on a Function as a Service (FaaS) serverless environment, 
but most of the applications that we use are stateful, they require a specific layer to preserve the state that has to be properly implemented to guarantee the scalability of the service.

# Functional Programming

To highlight the value of stateless functions, I take inspiration by lambda calculus. 
This is an example of Natural Numbers in Python:

{% highlight Python %}
# Functions to generate natural numbers

zero = lambda msg: 'ZERO' if msg == 'who' else succ(zero) if msg == 'succ' else 'WHAT?'

succ = lambda n: lambda msg: 'SUCC(' + n('who') + ')'  if msg == 'who' else n if msg == 'pred' else \
        (lambda msg: succ(succ(n)) (msg)) if msg == 'succ' else 'WHAT?'
{% endhighlight %}

Each number **n** is represented by a function responding to an input message:

- "who" : **n** replies with its identity string
- "pred" : **n** replies with the function representing **n - 1** (if *n > 0*)
- "succ" : **n** replies with the function representing **n + 1**

For example the identity of number **zero** is:

{% highlight Python %}
>>> zero("who")
'ZERO'
{% endhighlight %}

Example, number **two** obtained from **zero**, and number **one** from **two**:

{% highlight Python %}
# Function representing number two
>>> zero("succ")("succ")
<function <lambda>....

# Identity of number two
>>> zero("succ")("succ")("who")
'SUCC(SUCC(ZERO))'

# Identity of number one, obtained as preceding of two
>>> zero("succ")("succ")("pred")("who")
'SUCC(ZERO)'
{% endhighlight %}

A function to **add** natural numbers:

{% highlight Python %}
# addition of natural numbers
>>> add = lambda m, n: n if m('who') == 'ZERO' else add(m('pred'), n('succ'))

# 2 + 3
>>> add(zero("succ")("succ"), zero("succ")("succ")("succ"))("who")
'SUCC(SUCC(SUCC(SUCC(SUCC(ZERO)))))'
{% endhighlight %}

In this example the `value` is encoded in the representation of the function, not in a variable. The objective of functional programming is to do as much as possible with functions: create, store and apply functions.

[Symbolic functional programming](https://en.wikipedia.org/wiki/Symbolic_programming) was very popular in GOFAI, but today another kind of functions are driving the progress of AI.

# Deep Learning
 
Continuous functions are first order citizens of Deep Learning.

All the sophisticated tasks delivered today by Deep Neural Networks, like for example: face identification, language translation, up to automatic code generation, are based on models that are embodied into stateless functions exposed through an API.

The `value` of the function is distilled into the model from the large datasets used for the training of the network. The learnt weights of the model are represented in data structures optimized to be managed by GPUs.

Conceptually they are iedal to be deployed in a serverless FaaS environment, getting the benefit of scalability of the infrastructure managed by the provider.

Continuous Delivery, smooth lifecycle, versioning of the service related to versioning of the model (new version like succ operations in the above example).

The usage of the deployed service with feedback collection can generate new dataset for the evolution of the model

Main constrain is the size limitation of current FaaS environment, can be addressed using:
Compositional architecture for Neural Network and Transfer learning. For example GPT-3, small portion of the network is retrained

I see a bright future for Serverless DL





Example of `YEAR-MONTH-DAY-title.MARKUP`

Check out the [Jekyll docs][jekyll-docs] for more info on how to get the most out of Jekyll. File all bugs/feature requests at [Jekyll’s GitHub repo][jekyll-gh]. If you have questions, you can ask them on [Jekyll Talk][jekyll-talk].

[jekyll-docs]: https://jekyllrb.com/docs/home
[jekyll-gh]:   https://github.com/jekyll/jekyll
[jekyll-talk]: https://talk.jekyllrb.com/
