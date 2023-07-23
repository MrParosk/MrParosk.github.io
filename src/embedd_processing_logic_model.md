# Embed pre & post processing logic in the model

Before we feed the data into our machine learning model we usually have pre-processing steps. For example, we could transform a column of words to integers through tokenization, Z-normalize a numerical column etc. It's very important that these steps gets applied in the same way for training as in serving.

For example, assume we have a feature thats follows a normal distribution with mean 5 and standard deviation of 2. Before feeding this column into our machine learning model (say a decision tree) we Z-normalize the column to mean 0 and standard deviation of 1. Now, during training of our decision tree we have made a split, s.t. if the value is smaller then 0.5 then we take the left branch, otherwise the right. Now if we forget to apply this Z-normalization on the feature almost all of the data points will be larger 0.5, causing the model to behave completely differently.

So how do we make sure that the pre-processing steps gets applied in a similar fashion between training and serving? A common pattern is to embed this step into the model object. Now when we save the model after training in order to be used for serving, this step gets automatically included. An example of this approach is [Sklearn pipelines](https://scikit-learn.org/stable/modules/generated/sklearn.pipeline.Pipeline.html). Below is an example of a Sklearn pipeline where we Z-normalize the feature "revenue" as part of our pipeline:

```python 
from sklearn.preprocessing import StandardScaler
from sklearn.pipeline import Pipeline
from sklearn.compose import ColumnTransformer
from sklearn.linear_model import LinearRegression

num_cols = ["revenue"]

numerical_pipeline = Pipeline(steps=[
    ("Z-normalize", StandardScaler())
])

col_transform = ColumnTransformer(transformers=[
        ("num_pipeline", numerical_pipeline, num_cols),
    ]
)

model = LinearRegression()

model_pipeline = Pipeline(steps=[
    ("col_trans", col_transform),
    ("model", model)
])

# The serialized model will contain the z-normalize step
with open("model.pkl","wb") as fp:
    pickle.dump(model_pipeline, fp)
```

Frameworks such as Tensorflow and PyTorch also have similar support for this pattern. In this case, we will embed the pre-processing steps into the computational graph. Then when we save the model these steps will be included automatically, since it is a part of computational graph.

When we have a large amount of data for training it can be a good idea to do the pre-processing outside of the model (preferably with a distributed computing framework) and later embed the pre-processing into the model object before we save it for serving. This is actually what [Tensorflow transform](https://www.tensorflow.org/tfx/transform/get_started) does. Here the pre-processing step is done with Apache Beam and once we have finished training we embed these steps into the computational graph which gets exported.

This pattern can also be applied to the post-processing step. For example, imagine that we want to make sure our predictions are non-negative. We could add a post-processing step where we take the maximum of our predictions and 0. To make sure that this gets applied in a similar fashion in both training and serving we should embed this step into our model object, as we did for pre-processing.
