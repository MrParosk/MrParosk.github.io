# Decoupling feature creation from training & serving workflow

This design pattern aims to decouple the feature creation from the training and serving workflows. 

It allows for separation of concerns; it allows our data engineers to focus creating scalable and robust feature creation pipelines and while model builders can focus on the modelling parts.

Decrease the risk of training-serving skews. A common cause of bugs in machine learning systems are difference in data processing between training and serving. For example, imagine that we are building a machine learning system for recommending products to users. During our modelling phase we have discovered that the price is an important feature. However, accidentally in our training pipeline we have defined the price in American dollars (USD), while in the serving workflow we have defined it in the native currency (i.e. for Swedish users we define it in Swedish Krona). This would not necessarily break our system but our predictions would most be worse then if we had implemented both in the same currency, e.g. USD.
