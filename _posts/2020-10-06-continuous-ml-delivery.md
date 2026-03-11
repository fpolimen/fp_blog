---
layout: post
title:  "Continuous Delivery of services embodied by Deep Learning models"
date:   2020-10-06 07:18:50 +0200
categories: digitalization serverless
---

Many digital services will be embodied by Machine Learning and Deep Learning models trained on large datasets. Delivering these services continuously — the way we deliver software — requires tracking assets that simply do not exist in traditional software projects.

# The three pillars of an ML service

A software service has one primary versioned artefact: the code. An ML-based service has three:

| Asset | What it is | Why it matters |
|-------|-----------|----------------|
| **Dataset** | The data used for training and evaluation | Determines what the model can learn; must be reproducible |
| **Model** | The trained artefact (weights + architecture) | The production deliverable; maps inputs to outputs |
| **KPIs** | Accuracy, precision, recall, latency, drift | The acceptance criteria for promoting a model to production |

These three assets are tightly coupled. A new dataset produces a new model. A new model must be evaluated against the KPIs before it can replace the current production version. The KPIs themselves may evolve as the service matures and user expectations change.

# Versioning the three pillars

## Dataset versioning

A dataset is not a static file. It grows, it is cleaned, it is augmented, it is split into training, validation and test sets. Each of these transformations must be tracked.

Tools like [DVC](https://dvc.org/) treat datasets the way Git treats code: every version is addressable, reproducible, and linked to the model it produced. The dataset version becomes a first-class citizen of the delivery pipeline.

```
dataset-v1.2.3  →  model-v1.2.3  →  KPI: accuracy=0.94
dataset-v1.3.0  →  model-v1.3.0  →  KPI: accuracy=0.96  ✓ promoted
```

## Model versioning

The trained model is a binary artefact — typically hundreds of megabytes — that cannot live in a Git repository. It must be stored in an artefact registry (MLflow, Weights & Biases, a cloud blob store) with the same discipline applied to software packages.

A model version carries metadata linking it to the exact dataset version, training configuration, framework version, and KPIs achieved. Without this traceability, reproducing a production model is impossible.

## KPI versioning and promotion gates

KPIs are the contract between the ML team and the business. They must be:

- **Defined** before training begins (not chosen post hoc to flatter the model)
- **Measured** on a held-out test set that the model has never seen
- **Versioned** alongside the model, so that historical performance is comparable
- **Used as gates** in the delivery pipeline: a model that does not meet the KPI threshold does not reach production

# The delivery pipeline

Combining these three pillars with a serverless deployment target gives a pipeline that looks like this:

```
┌──────────────┐    ┌──────────────┐    ┌──────────────┐    ┌──────────────┐
│   New data   │───▶│   Training   │───▶│  Evaluation  │───▶│  Deployment  │
│   arrives    │    │  (retrain or │    │  against     │    │  (FaaS,      │
│  (dataset    │    │  transfer    │    │  KPI gates)  │    │  versioned   │
│   v+1)       │    │  learning)   │    │              │    │  function)   │
└──────────────┘    └──────────────┘    └──────────────┘    └──────────────┘
       │                  │                    │                    │
       ▼                  ▼                    ▼                    ▼
  DVC / S3           MLflow /           Pass → next         API Gateway
  (versioned)        artefact           Fail → alert        (route traffic
                     registry           + retrain           to new version)
```

Each stage is itself a function. The training step is a function that takes a dataset version and produces a model version. The evaluation step is a function that takes a model version and returns a pass/fail decision. The deployment step is a function that updates the routing rules in the API gateway.

# Serverless as the target

A serverless FaaS environment is a natural fit for the deployment layer because:

1. **Versioning is native.** FaaS platforms expose function versions as first-class concepts. Routing a percentage of traffic to a new version (canary deployment) is a configuration change, not a deployment operation.

2. **Rollback is cheap.** Rolling back means re-routing traffic to the previous function version. The old version was never removed; it was simply not the primary target.

3. **Idle cost is zero.** A model that handles sporadic inference requests costs nothing when idle. This makes it practical to keep multiple model versions live simultaneously for A/B testing.

4. **The governance model fits.** As described in the previous post, what you need to manage a fleet of ML functions is a versioning discipline and visibility rules — not a different infrastructure stack.

# What changes compared to traditional CD

In traditional software delivery, the unit of delivery is a code artefact (a JAR, a Docker image, a Lambda zip). In ML delivery, the unit is a **(dataset, model, KPI)** triple. The pipeline is the same in structure; what changes is the nature of the artefacts and the cost of producing them.

Training is expensive. This means the feedback loop is slower, and the discipline around dataset quality and KPI definition must be proportionally more rigorous. You cannot afford to discover that your test set was contaminated after training a large model for 48 hours on a GPU cluster.

The investment in tooling — dataset versioning, artefact registries, automated evaluation — pays off precisely because it makes that feedback loop shorter and more reliable.
