# Demand-Forecast-with-Holt-Winters-Methods

The objective of this project is to forecast demand given 3 different parameters through user determined inputs, using past data as supplied under provided file ‘APS1017 Order data for Project’. These 3 parameters are namely: Client designation, material designation and forecast date. The library statsmodels was used to analyse the time series and perform forecasting.

The working program in project was designed to forecast aggregate demand for future planning regardless of client or material specification. Hence if client is not specified, forecast of aggregate demand of a single material will be returned. Similarly, if material designation is not specified, forecast of aggregate demand of a single client will be returned.

Prior to deciding what type of forecasting algorithm to use, a time-series analysis of the past data was carried out using the Augmented Dicky Fuller test and the visualisation of seasonal decomposition to observe suitable forecasting models to use, whether stationary or non-stationary. This was carried out under the 3 different user input scenarios (#1 order quantity per client, #2 order quantity per material and #3 order quantity per client per material).

An example of seasonal decomposition for order quantity per client:

<img src="https://github.com/quincyho/Demand-Forecasting-with-Holt-Winters-Methods/blob/main/Images/Seasonal%20decomposition.PNG" width="600">

Since the data contained both stationary and non-stationary trends, combined with the fact that only 2 different forecasting models should be run for all scenarios each, selection of forecasting algorithm uses Holt-Winters' Additive and Holt-Winters' Multiplicative models.

### Holt-Winters' Multiplicative Model
This method was taught during class and was chosen as the components can be adjusted to represent both stationary and non-stationary datasets with the adjustment of trend and seasonal factors. Ultimately, by automatically fitting trend and seasonal factor components to drop out of the forecasting equation (depending on past data), the only smoothing equation of series represent a stationary forecast which is applicable with our mixed dataset. Winters' multiplicative holds the general equation illustrated below: 

<img src="https://github.com/quincyho/Demand-Forecasting-with-Holt-Winters-Methods/blob/main/Images/Multi%20formulas.PNG" width="400">

Where the three smoothing equations combine multiplicatively with seasonal factor. The smoothing equations of S_t, G_t and c_t represent series, trend and seasonal factor respectively whereas F_{t+1} indicates forecast for the t+1 day. This equation discounts the random component found in the base model equation. The series equation represents deasonalised and weighted (between observation and past data) as represented by alpha. Trend represents linear increase or decrease of the past dataset which uses series equation rather than observed data to decreases fluctuations. Seasonal factor of 7 days is used to predict adjustment depending on previous season observations whereas alpha, beta and gamma are all optimised by the function automatically.

One of the major limitations of this equation is identified when previous datapoints D_t which have no entry (NaN) are set to 0. S_t will also return 0 which produces a numerical error when calculating seasonal factor c_t as the denominator is 0. Moreover, this problem arises regardless of whether a dataset is stationary and is circumnavigated by performing ‘backfill’ of replacing the 0 datapoints with succeeding non-zero datapoint. It is understood that a very small number could also be fitted to approximate a zero value, however, it results in a seasonal factor c_t approaching infinity. The main issue still stands where a Winters' multiplicative model will require additional information handling when 0 quantity is demanded. 

With such limitation on zero values, using another model will be ideal to prevent force fitting certain arbitrary values other than 0.

### Holt-Winters' Additive Model
By using another method with similar components, we are able to compare and avoid previous problems of requiring backfill of values which does not logically reflect the real data. By replacing NaN values with 0, using Winters' Additive formulation bypasses all errors described above. The general formulation is illustrated below:

<img src="https://github.com/quincyho/Demand-Forecasting-with-Holt-Winters-Methods/blob/main/Images/Add%20formulas.PNG" width="400">

There is no denominator in the c_t component which will require further investigation if 0 is inputted hence this presents a better fitting model. The 3 smoothing equations are exactly the same as described in the previous method except for c_t where the first y component is replaced by (D_t-S_t) and the forecast equation F_{t+1} is wholly additive rather than multiplicative of the seasonal factor c_t. 

### Comparison of the Two Models
The two variations of Holt-Winters’ method vary how the seasonal component affects the baseline equation. Generally, the additive method yields better results when the seasonal variations are constant whereas the multiplicative method is better utilised when seasonal variations are altering proportionally to the level of the series. Source: https://kourentzes.com/forecasting/2014/11/09/additive-and-multiplicative-seasonality/

Below shows a better comparison between both methods and their base data entries by taking client c2 and material 11000851. It is observed that the multiplicative method provides a much better forecast (peaks and constant demands) with the backfilled observation data compared to the additive method that fills the NaN values with zeros.

<img src="https://github.com/quincyho/Demand-Forecasting-with-Holt-Winters-Methods/blob/main/Images/Comparison.PNG" width="600">

### Examples of the Working Program for Forecasting

Using Holt-Winters' Additive Model to perform demand forecast per client (original data was used, without backfilling):

<img src="https://github.com/quincyho/Demand-Forecasting-with-Holt-Winters-Methods/blob/main/Images/Client%20forecast%20add.png" width="600">

Using Holt-Winters' Multiplicative Model to perform demand forecast per client (augmented data was used, with backfilling):

<img src="https://github.com/quincyho/Demand-Forecasting-with-Holt-Winters-Methods/blob/main/Images/Client%20forecast%20mul.PNG" width="600">
