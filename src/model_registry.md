# Model registry

A central part of any machine learning infrastructure is the model registry. The registry is mainly responsible to keep track of trained models and their versions. There are principally two parts to keep track of:

<ol>
  <li>Model binary</li>
  <li>Dependencies for the model</li>
</ol>

The model binary is a serialized model produced during the training phase. It can be a pickled scikit-learn model, tensorflow model of SavedModel format etc. These can be saved in object storage, like s3 or gcs.

The second part is the dependency needed to load and serve the model. For example, if we trained our scikit-learn model with version 1.3.2 we should use the same version during inference as well. The reason for using the same version for training and inference are twofold. Firstly, to avoid incompatibility between versions. For example, sometimes the internal implementation of the same same model (e.g. LinearRegression) changes between versions which cause our model to not be load-able. Secondly is the potential of updated implementation of certain calculation. For example, imaging that the library maintainer for scikit-learn updated the calculation of sample mean which we use in our model. Now the mean calculation is different between the training and serving, causing train-serving skew.

Now the question arises of how to manage these dependencies. There are two common ways:

<ol>
  <li>Docker container</li>
  <li>Pinned dependencies</li>
</ol>

With the docker container approach, we define a dockerfile which specify certain library versions needed to training and serving our model. This container is then built and uploaded to our docker registry. In our model registry, we add a reference to this specific build, ensuring that the same library versions are installed during training and serving. This approach is used in [VertexAI's model registry](https://cloud.google.com/vertex-ai/docs/training/containers-overview).

Usually you will have certain libraries that should only be installed for training and vise-versa for serving. This can be achieved with a multi-staged build:

```docker
FROM python:3.12-bullseye AS base

# Install dependencies used in both training & serving
pip install scikit-learn==1.3.2

FROM base AS train

# Install dependencies used only in training, like a hyper-parameter tuning library
pip install hyperopt==0.2.7

FROM base AS serve

# Install dependencies used only in serving, like a rest web framework
pip install fastapi==0.109.0
```

The second approach is simply pinning the dependencies in text-format, like YAML. These versions are created during our training process, ensuring that we use the same version during training and serving. This approach is supported in [MLFlow](https://mlflow.org/docs/latest/model-registry.html#concepts):

```yaml
dependencies:
  - scikit-learn==1.3.2
```
