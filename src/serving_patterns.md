# Serving patterns

Once a model is trained we want to make it available for serving. There are typically three common ways of doing it:

<ol>
  <li>Batch serving</li>
  <li>Online serving</li>
  <li>Hybrid serving</li>
</ol>

Each of them have their own advantages and disadvantages.

## Batch serving

During batch serving we typically received a large number of rows and want to do inference on them. One example where this arises is that we want to make forecast of sales for different products on a weekly basis so that we can allocate appropriate volumes to different stores. The prediction job is executed every Saturday at midnight. Here we make make daily predictions for seven days, for each product and store. Image we have 100 stores, each with 1000 products. Therefore we would need to make 7,000,000 predictions. Here the batch-serving patterns fits well, since the number of rows are quite large and there is no tight latency requirements (it probably doesn't matter if the job takes 60 minutes or 90 minutes).

Now for actually serving the requests to our users we typically save these prediction in a key-value cache (e.g. Redis). And when a user request the forecasted sale for a given store, product and date we simply look it up in the cache and return the value, as depicted below:

```mermaid
graph LR
    A[Batch job] --> B[Key-Value cache]
    B --> C[User request]
```

One advantage of batch-serving is that we receive all rows at once and therefore it is easier to make efficient use of the hardware. Also, since we pre-compute these the speed of receiving predictions when a user request them will be very quick since fetching items from a key-value store is fast (typically ~1 ms).

One disadvantage is that we need to pre-compute the prediction for all possible combinations of feature, which grows exponentially. For example, imagine we instead wanted to forecast per hour. That would now require 16,800,000 predictions. And if we wanted more predictions columns, such as country, the number of rows we need to pre-compute grows quickly.

Note that pre-computing the predictions is not always be possible. For example, imagine above that one of the features to our model would be the product description, which would be free-flowing text of type string. We could simply not pre-compute all possible descriptions a head of time since the number of possible combination is enormous. So we would need to drop this feature from the model in this case in order to pre-compute the predictions, which might be an acceptable solution, depending on how important this feature is for the model performance.

## Online serving

## Hybrid approach
