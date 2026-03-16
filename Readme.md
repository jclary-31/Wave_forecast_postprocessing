
# Purpose
This will be a long term project aiming at improving deterministic model forecast with post processing analyses, for marine conditions. Long term goal is to be able to participate into marine oriented projects, such as wave energy production.

# Model and data
 ## buoy 
 data are here https://dd.weather.gc.ca/today/observations/swob-ml/
 documentation for markup /xml format is here https://dd.weather.gc.ca/today/observations/doc/Met-ML-SchemaDescriptionV2_e.pdf, and a very detailled explanation of surface weather  observation is here https://dd.weather.gc.ca/today/observations/doc/SWOB-ML_Product_User_Guide_v8.15_e.pdf.  Note that some definitions are tricky.

## Wave model
Description of the regional wave model is here https://eccc-msc.github.io/open-data/msc-data/nwp_rdwps/readme_rdwps_en/
This model is a regional adaptation of the  the third generation spectral wave forecast model WaveWatch III (WW3). It is forced by high resolution wind from HRDPS model, and ice is forecasted from RIOPS model. Ice are rare in measurement location but not impossible. 

# Current Limitations
For now, the observational dataset is very limited, only 4 Surface Weather Observation Buoy. I hope to find more buoys on NOAA website, but I must be sure they give similar information to the one in given by MSC. Also, a large part of buoys are not available in winter.

Model output is now restricted to 24h and retrieve once a day, meaning overlap between two successives forecast is null. However, models outputs a given every 6 hours, and with a 48h horizon. In a future, I will strongly considers the overlapping forecasts on mutliple variable to, 1) compute model consistency /time accuracy, and 2) see how this can be used to reconstructing missing data (for waves)


# Current situation
I test a 1 dimensional kalman filter (state= one variable), and also a 2 dimensional kalman filter where state=[one variable, time derivative]. I also want to implement a multi state kalman filter, where variable co-dependency are tanken into account. 

 I’ve split a time series into two parts: the first part, which is the ‘past’, and the ‘future’, where I assume no observations are available yet (the ones that do exist are the target to reach). So I’m looking to obtain a correction of the forecasts in the past, as well as an adjustment of the forecasts in the future so that they better match the target to reach.


When I apply a filter with a state vector limited to [x], I obtain a satisfactory adjustment in the 'past', but almost no correction propagates into the 'future'.
In contrast, when I use an augmented state vector [x, x_time_derivative], the model adjustment in the 'past' matches the observations almost perfectly. While this outcome appears suspiciously overfitted, this configuration produces a more reasonable modification of the forecasts in the 'future'



# Kalman Filtering
Kalman filtering type is also subject to discussion. From some papers I found, ensemble kalman filter (ENKF) is way more adapted for this project.  ENKF is EnKF is a Monte Carlo approximation of the Kalman filter.


# Next steps /open considerations
A further challenge arises when a variable is not explicitly modeled—for example, wave parameters that are represented spectrally. In such cases, I would like to reconstruct a time series for the unmodeled variable using other available forecasts.

I also want to teste AutoRegressive Integrated Moving Average (ARIMA) procedure and Long Short-Term Memory (LSTM) deep learning algorithms to improve the forecasts. I think LSTM architecture has huge potential here.
