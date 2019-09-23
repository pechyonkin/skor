# Assignment 2. Machine Learning Solutioning

## Problem Description

​Nulogy would like to build a machine learning product to predict customer demand for SKUs at a retail store. An SKU can be any item such as ketchup bottles for a particular brand, or romaine lettuce.
​
Please describe how you would approach and solve the problem above. Please remember to include:

- What data you will need to collect
- What features you may need to engineer (if applicable)
- How you would assess the performance of your model
- Anything else you deem important to put together a machine learning solution

# Solution

## Data Collection

The problem here is loosely defined so that I will walk through possible scenarios. Imaging we are talking about the ketchup example.

First of all, the problem doesn't state what the granularity of prediction is. If we are prediction demand for the next year, then we could find some market reports about the ketchup market growth and use that data, and that would probably be a pretty good approximation.

The more fine-grained the prediction, the more challenging it would be to collect useful data and build a good predictive model.

Before talking about the ways to get data to let me put a magic hat on and imagine that I could have all the data I could wish for.

In that case, a lot of data could be potentially useful:

- previous sales of the target SKU at a high granularity
- location of the store
- sales of other similar products to the SKU in question
- foot traffic at the store
- location of the SKU inside the store
- foot traffic in the area where SKU is located
- place of the SKU on the shelf, is it low, at eye level, or high?
- weather data at the store
- data about whether other stores are closed or not
- how well is the store being restocked
- percentage of empty spots on the shelves
- data about the supply of the ketchup to the store
- sales of the substitute/complement goods
- data about factory production levels that produces our ketchup
- knowledge about the relative positioning of the SKU related to other goods
- are people looking at our SKU compared to other products? (I have developed an eye-tracking system as a side project, it can be used to collect this data)
- data about changes in consumption preferences of consumers
- features related to date (useful for learning seasonality effects):
    - month
    - week
    - day of the week
    - is it a holiday?
    - is it a day-off?


## Feature Engineering

First of all, let me assume that we will work withing a supervised learning approach. At a bare minimum, we will have the past sales data for the target SKU or another similar SKU that can act as a reasonable estimate for the SKU whose demand we are modeling.

If this data is the only thing we have, we could proceed to develop some time series model; it could be a classical statistical model or something fancier like an LSTM neural net. For a time series, we could use some form of aggregation of the past data as features. We could use moving averages across different windows, various measures of the volatility of time series, self-correlation, etc.

Another good idea would be to decompose time series into the trend, seasonal effects, and residuals. Then our model would only have to work to predict the residuals, which can make it easier to learn essential relationships.

Since we are talking about time-series prediction, seasonality effects must be taken into account and adequately dealt with. Besides, for time series, we need to make sure to carefully perform train-test-validation splits, since traditional random splitting is not going to work.

**Note**: This careful splitting won't be needed if we have a considerable amount of data, and then we can train a neural network in a non-sequential way: use a fully-connected model instead of a sequential model and learn embeddings from input space that map into demand prediction space. In such a case, we can split data randomly, but perform feature engineering in such a way as to preserve information about seasonality. 

For example, for a date variable, we could split it into: 

- month
- week
- day
- hour
- is it a weekend?
- is it a holiday?
- what was the weather like on that day?

Then we could train on specific datapoints non-sequentially since seasonality data is already there. This approach can work well in practice, and the network can pick up those seasonal relationships even though the model is not intrinsically sequential.


## Assessing Performance of the Model

I would train the model on the train data set, use one of the cross-validations schedules to select optimal hyperparameters and then after all is done, use test set to assess expected model performance in real life.

Since it is a regression model, I would go with an MSE loss. It looks suitable since L2 norm penalizes more significant deviations from the target more than smaller deviations.

Based on this, I would decide whether such a model can bring enough economic value if deployed.
