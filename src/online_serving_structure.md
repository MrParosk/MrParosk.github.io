# Online serving structure

When deploying our machine learning model for online inference we typically divide into two parts, our backend service and our machine learning inference service. Below is a diagram of the high-level overview of this setup:

```mermaid
graph LR
    A[User] <--> B[Backend service]
    B <--> C[Machine learning inference service]
```

Our backend service is responsible authenticate the request, fetching necessary data from the feature store etc. The machine learning inference service will be tasked with doing the actual inference of the machine learning model. It can be broken up into three parts; pre-process, predict and post-process. 

The pre-process step include steps like validating that the required data is available for doing the request, that the data types are correct etc. We try to avoid including model specific pre-processing step here, see [Embed pre & post processing logic in the model](embedd_processing_logic_model.md) for clarification.

The predict part is where we do the actual model inference. We send in the data produces from pre-process step into the model to get the predictions.

Lastly, we have the post-process step. Here we transform the outputted predictions into a format which can be used by the system as a whole. For example, we might transformed predicted class-ids into class names, such as 0 into the name 'Dog'.

Now with these tree steps, how should they be deployed? As one service or spread out into multiple? There is a few different approaches for doing this which we will delve into now with their own advantages and disadvantages.

## Group all steps together

The first approach we will discuss is to group all the steps into one service. Below is an example code structure:

```python
class InferenceHandler:
    def __init__(self, model_path):
        # load the model, e.g. with pickle
        pass

    def pre_process(self, x):
        # define the pre-process logic in this function
        pass

    def predict(self, x):
        # define how the model's predict function should be called in this function
        pass

    def post_process(self, x):
        # define the post-process logic in this function
        pass

    def infer(self, x):
        # in this function we glue all of the above step together
        return self.post_process(self.predict(self.pre_process(x)))
```

The class above would then be included in a web-server which exposes an end-point that allow users to call it through HTTP or gRPC.

The advantages of this approach that it is simple. You only need to deploy one service, making the deployment process a lot smoother. Furthermore, keeping the different steps in synchronisation is a lot easier. For example, imaging we wanted to update our predict function to accept batches of inputs (i.e. from input shape of (num_features) to (num_samples, num_features)). This change would require us to also update the pre & post-processing steps. Now, if these were deployed separately, we would need re-deploy all the services which is more cumbersome.

[VertexAI model serving](https://github.com/googleapis/python-aiplatform/blob/main/google/cloud/aiplatform/prediction/sklearn/predictor.py) and [Ray Serve](https://docs.ray.io/en/latest/serve/index.html) are examples of frameworks that uses this approach.

## Separate steps

```mermaid
graph LR
    A[Pre-process] --> B[Predict]
    B --> C[Post-process]
```
