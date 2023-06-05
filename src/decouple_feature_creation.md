# Decoupling feature creation from training & serving workflow

This design pattern aims to decouple the feature creation from the training and serving workflows. 

It allows for separation of concerns; it let people with expertise in data engineering to focus creating scalable and robust feature creation pipelines while experts in model builders can focus on the modelling parts.

Decrease the risk of training-serving skews. A common cause of bugs in machine learning systems are difference in data processing between training and serving. 

For example, imagine that we are building a machine learning system for recommending products to users. During our modelling phase we have discovered that the price is an important feature. However, accidentally in our training pipeline we have defined the price in American dollars (USD), while in the serving workflow we have defined it in the native currency (i.e. for Swedish users we define it in Swedish Krona). This would not necessarily break our system but our predictions would most be worse then if we had implemented both in the same currency, e.g. USD.

This might seem like a obvious example, but these types of issues crop up all the time. I have seen that simply unifying the feature creation between training and serving workflows substantially increase the accuracy of system.

A common term within machine learning systems are [feature stores](https://www.tecton.ai/blog/what-is-a-feature-store/). Does that mean we always need to include a feature store in our systems? Not necessarily. 

For example, imagine that we have the recommendation problem where we will pre-compute the recommendation before hand, i.e. doing batch inference a head of time and store the predictions in a database. When we want to serve these recommendations, we simply do a lookup in our database. Furthermore, assume that the cardinality of users & products are not too big. Then for our feature creation we could simply store these features in a data warehouse / datalake, where each row contains all the feature for users, articles, the the combination of feature & users.

Below is an example table with this structure:

| article_id | user_id  | article_type | user_previous_purchases | user_article_affinity |
| :------:   | :------: | :------:     | :------:                | :------:              |
| 1582       | 5768     | electronics  | 192.0                   | 0.8                   |
| 1582       | 9922     | electronics  | 29.0                    | 0.3                   |
| 1988       | 9922     | clothes      | 29.0                    | 0.1                   |

Whether we should implement this ourself like above or use an external platform like [Feast](https://docs.feast.dev/) is context dependent. The above approach is good since we don't have to learn a new tool and integrate with it. We can simply use our preferred data warehouse / datalake solution.

However, if we instead would like to do online inference the approach above has most likely too high latency for feature retrieval. To improve the latency of retrieval, we could store the features for inference in a key-value store, like Redis. However, adding these capabilities would require a lot of engineering efforts and simply using a third-party system like Feast is most likely easier.
