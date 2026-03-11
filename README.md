# Serverless front-end on Jekyll

A technical blog by [Francesco Polimeni](https://github.com/fpolimen) exploring the intersection of **serverless computing**, **functional programming**, and **machine learning deployment**.

## About

This blog is an experiment in building a simple front-end to aggregate services deployed on serverless providers. The posts trace a coherent narrative: from the original vision of digital service ecosystems (HP e-services, late 90s) to today's API Economy, and forward to a future where deep learning models are deployed as stateless serverless functions governed by a principled versioning layer.

## Topics

- **Serverless & FaaS** — architecture patterns, continuous delivery, governance
- **Functional Programming** — lambda calculus, stateless functions, compositionality
- **Deep Learning** — FaaS deployment strategies, transfer learning, compositional architectures
- **MLOps** — ML model lifecycle, dataset versioning, KPI tracking

## Posts

| Date | Title | Categories |
|------|-------|------------|
| 2020-07-21 | [Welcome to Jekyll!](./_posts/2020-07-21-welcome-to-jekyll.markdown) | jekyll |
| 2020-07-22 | [The next E](./_posts/2020-07-22-the-next-e.md) | digitalization, serverless |
| 2020-08-16 | [Serverless Deep Learning](./_posts/2020-08-16-serverless-deep-learning.md) | ML, FaaS, functional programming |
| 2020-10-06 | [Continuous Delivery of DL services](./_posts/2020-10-06-continuous-ml-delivery.md) | MLOps, versioning |
| 2020-10-12 | [Bye Bye Jekyll](./_posts/2020-10-12-bye-bye-jekyll.md) | generators, Hugo |

## Run locally

**Prerequisites:** Ruby ≥ 2.5, Bundler

```bash
bundle install
bundle exec jekyll serve
```

Open `http://localhost:4000` in your browser.

## Author

Francesco Polimeni — [@fpolimen](https://github.com/fpolimen) — polimeni.francesco@gmail.com
