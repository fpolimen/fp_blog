---
layout: post
title:  "Serverless Deep Learning"
date:   2020-08-16 07:18:50 +0200
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
 
Numerical functions are first order citizens of Deep Learning.

All the sophisticated tasks delivered today by Deep Neural Networks, like for example: face identification, language translation, up to automatic code generation, are based on models that are embodied into stateless functions exposed through an API.

The `value` of the function is distilled into the model from the large datasets used for the training of the network. The weights of the model are represented in data structures like tensors, optimized to be managed by GPUs.

Conceptually a service embodied by a Deep Neural Network would benefit of the scalability provided by a serverless FaaS environment. The main issue that could affect this kind of deployment is the memory size available for a service, supported by FaaS providers, that couldn't satisfy the requirements of non-trivial DNNs.

A solution to this problem could be the adoption of compositional architecures for DNNs and fine tuning for specific Tasks by transfer learning.

For example in Computer Vision are available Convolutional Neural Networks, trained on large datasets like Imagenet, that can be seen as composition of 2 Newral Networks. The fisrt is formed by the Convolutional and Pooling layers that generate the inner features used by the second Fully Connected Neural Network.

The Transfer learning is used to specialize those large CNNs to Tasks using a small dataset to that task and acting on the FCNN parameters.

The 2 networks could be deployed on 2 providers, the first dedicated to the large general purpose inner layers and the much smaller second layer dedicated to the specific task.

So there will be a provider dedicated to host large core networks, supporting the transfer learning activity that generate smaller network segments representing the intellectual property of the specific task, that could be deployed on a third party FaaS provider or on premise.

## Delivery and Governance

The adoption of Serverless approach simplifies the Continuous Delivery processes by versioning.
The production environment will be represented by specific versions of the functions composing your applications. 

For the DNNs based function, and ML models in general, the `SUCC` version will be the result of a new training, i.e. transfer learning, on a richer dataset, executed by a generator function that will respond to `generate-new-version` message.

The non-production environments will be represented by set of functions with specific versions, some of them eventually shared with the production. You will need to manage function-version dependencies, visibility rules (who can access a function under test), etc..

![Governance is all you need](/assets/images/Governance_is_all_you_need.png)

In this scenario `Governance is all you need` to make the next leap towards a dynamic ecosystem of Business Services.

Check out the [Jekyll docs][jekyll-docs] for more info on how to get the most out of Jekyll. File all bugs/feature requests at [Jekyll’s GitHub repo][jekyll-gh]. If you have questions, you can ask them on [Jekyll Talk][jekyll-talk].

[jekyll-docs]: https://jekyllrb.com/docs/home
[jekyll-gh]:   https://github.com/jekyll/jekyll
[jekyll-talk]: https://talk.jekyllrb.com/
