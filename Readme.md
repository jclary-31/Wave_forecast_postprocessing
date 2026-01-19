
This will be a long term project aiming at improving determinitic model forecast with post process / 'assimilation' analyses, for marine conditions. Long term goal is to be able to participate into marine oriented projects, such as wave energy production.

For now, the observational dataset is very limited, only 4 Surface Weather Observation Buoy. I hope to find more buoys on NOAA website, but I must be sure they give similar information to the one in given by MSC. Also, a large part of buoys are not available in winter.

Model output is now restricted to 24h and retrieve once a day, meaning overlap between two successives forecast is null. However, models outputs a given every 6 hours, and with a 48h horizon. In a future, I will strongly considers the overlapping forecasts on mutliple variable to, 1) compute model consistency /time accuracy, and 2) see how this can be used to reconstructing missing data (for waves)

I am currently implementing a 1 dimensional kalman filter (state= one variable) but I want to implement a multi state kalman filter. Kalman filtering type is also subject to discussion

I would like to consider AutoRegressive Integrated Moving Average (ARIMA) procedure and Long Short-Term Memory (LSTM) deep learning algorithms to improve the forecasts. I think LSTM architecture has huge potential here

