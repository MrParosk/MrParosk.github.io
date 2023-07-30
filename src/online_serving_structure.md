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
