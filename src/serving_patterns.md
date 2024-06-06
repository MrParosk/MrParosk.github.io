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

One advantage of batch-serving is that we receive all rows at once and therefore it is easier to make efficient use of the hardware.

## Online serving

## Hybrid approach
