---
layout: post
title:  "Serverless Deep Learning"
date:   2020-08-16 07:18:50 +0200
categories: digitalization serverless
---

Stateless functions are ideal to be deployed on a Function as a Service (FaaS) serverless environment.
Most of the applications we use are stateful — they require a specific layer to preserve state that must be carefully implemented to guarantee scalability. The interesting question is: what happens when we push statelessness as far as it can go?

# Functional Programming

To highlight the value of stateless functions, I take inspiration from lambda calculus.
This is an example of Natural Numbers in Python:

{% highlight python %}
# Functions to generate natural numbers

zero = lambda msg: 'ZERO' if msg == 'who' else succ(zero) if msg == 'succ' else 'WHAT?'

succ = lambda n: lambda msg: 'SUCC(' + n('who') + ')'  if msg == 'who' else n if msg == 'pred' else \
        (lambda msg: succ(succ(n)) (msg)) if msg == 'succ' else 'WHAT?'
{% endhighlight %}

Each number **n** is represented by a function responding to an input message:

- `"who"` — **n** replies with its identity string
- `"pred"` — **n** replies with the function representing **n − 1** (if *n > 0*)
- `"succ"` — **n** replies with the function representing **n + 1**

For example, the identity of number **zero** is:

{% highlight python %}
>>> zero("who")
'ZERO'
{% endhighlight %}

Number **two** obtained from **zero**, and number **one** from **two**:

{% highlight python %}
# Function representing number two
>>> zero("succ")("succ")
<function <lambda>....

# Identity of number two
>>> zero("succ")("succ")("who")
'SUCC(SUCC(ZERO))'

# Identity of number one, obtained as predecessor of two
>>> zero("succ")("succ")("pred")("who")
'SUCC(ZERO)'
{% endhighlight %}

A function to **add** natural numbers:

{% highlight python %}
# Addition of natural numbers
>>> add = lambda m, n: n if m('who') == 'ZERO' else add(m('pred'), n('succ'))

# 2 + 3
>>> add(zero("succ")("succ"), zero("succ")("succ")("succ"))("who")
'SUCC(SUCC(SUCC(SUCC(SUCC(ZERO)))))'
{% endhighlight %}

In this example the `value` is encoded in the representation of the function, not in a variable. The objective of functional programming is to do as much as possible with functions: create, store, and apply them.

[Symbolic functional programming](https://en.wikipedia.org/wiki/Symbolic_programming) was very popular in GOFAI. Today a different kind of functions is driving the progress of AI.

# Deep Learning

Numerical functions are first-order citizens of Deep Learning.

All the sophisticated tasks delivered today by Deep Neural Networks — face identification, language translation, automatic code generation — are based on models that are **embodied into stateless functions** exposed through an API.

The `value` of the function is distilled into the model from the large datasets used for training. The weights are stored in data structures optimised for GPU computation during both training and inference.

Conceptually, a service embodied by a Deep Neural Network benefits naturally from the scalability of a serverless FaaS environment. The main constraint is **memory**: FaaS providers impose limits that non-trivial DNNs can exceed.

## Compositional Architectures and Transfer Learning

A practical solution is to exploit the compositional structure that large DNNs already have.

In Computer Vision, for example, a convolutional neural network trained on ImageNet is naturally decomposable into two sub-networks:

```
┌─────────────────────────────────────────────────┐
│              Full CNN (e.g. ResNet-50)           │
│                                                  │
│  ┌─────────────────────┐  ┌───────────────────┐  │
│  │  Convolutional +    │  │  Fully Connected  │  │
│  │  Pooling Layers     │→ │  Layers (FCNN)    │  │
│  │                     │  │                   │  │
│  │  General features   │  │  Task-specific    │  │
│  │  (ImageNet)         │  │  classifier       │  │
│  └─────────────────────┘  └───────────────────┘  │
└─────────────────────────────────────────────────┘
```

**Transfer Learning** tunes the FCNN on a small, task-specific dataset, leaving the convolutional backbone unchanged.

This decomposition maps naturally to a two-tier serverless deployment:

| Layer | Provider | Characteristics |
|-------|----------|-----------------|
| Core network (conv + pool) | Specialised provider with GPU support | Large, general, rarely updated |
| Task network (FCNN) | Standard FaaS or on-premise | Small, task-specific, updated via retraining |

The core network becomes a shared infrastructure service. The task network is the intellectual property of the specific application, and it can be deployed, versioned, and updated independently.

## Delivery and Governance

The serverless approach simplifies Continuous Delivery through versioning.
The production environment is represented by specific **versions** of the functions composing the application.

For DNN-based functions, and ML models in general, the `SUCC` version is the result of a new training (or transfer learning) on a richer dataset — executed by a generator function that responds to a `generate-new-version` message. This is functional programming applied to the model lifecycle.

The non-production environments are sets of functions at specific versions, some shared with production. What you need to manage:

- Function–version dependency graphs
- Visibility rules (who can invoke a function under test)
- Rollback paths (predecessor versions must remain accessible)
- KPI thresholds that gate promotion to production

![Governance is all you need](/assets/images/Governance_is_all_you_need.png)

In this scenario, **Governance is all you need** to make the next leap towards a dynamic ecosystem of Business Services. The infrastructure is commoditised; the competitive advantage lives in the model, the dataset, and the versioning discipline that keeps them aligned.
