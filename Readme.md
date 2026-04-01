
# Purpose
This will be a long term project aiming at improving deterministic model forecast with post processing analyses, for marine conditions. Long term goal is to be able to participate into marine oriented projects, such as wave energy production.

# Model and data
 ## buoy 
Surface Weather and Marine Observation (SWOB-ML) buoy data consists of XML-formatted meteorological and oceanographic measurements from Canadian moored buoys, accessible in real-time via MSC Datamart. This data includes wind, pressure, temperature, and wave metrics, supporting weather forecasting and marine safety. Most recent data can be found at this [adress](https://dd.weather.gc.ca/today/observations/swob-ml/).

 The special xml format is tricky to read, and practical definitions of wave metric are not exactly the same as for model output. Documentation for markup /xml format is [here](https://dd.weather.gc.ca/today/observations/doc/Met-ML-SchemaDescriptionV2_e.pdf), and a very detailled explanation of surface weather  observation is [here](https://dd.weather.gc.ca/today/observations/doc/SWOB-ML_Product_User_Guide_v8.15_e.pdf)

## Wave model
Description of the regional wave model is [here](https://eccc-msc.github.io/open-data/msc-data/nwp_rdwps/readme_rdwps_en/)
This model is a regional adaptation of the  the third generation spectral wave forecast model WaveWatch III (WW3). It is forced by high resolution wind from HRDPS model, and ice is forecasted from RIOPS model. Ice are rare in measurement location but not impossible. In a nutshell, value of interest are : wind speed, wind direction,


## Locations

<p align="center">
 <img width="600" src=example_locationandwind.jpg>
</p>



# Kalman Filtering
The Kalman Filter (KF) is an algorithm that estimates the evolving state of a dynamical system by optimally combining:
- Model predictions (what the system should do)
- Observations (what we measure, often noisy or incomplete)
It provides, A best linear unbiased estimate of the system state and a quantification of uncertainty through covariance matrice

Kalman filter foundamental hypothesis is that the state evolves according to linear dynamics, and that Errors in the model and observations are Gaussian. Basically, Kalman is a two procedure step with a prediction step (next state and its uncertainty), and an update/assimilation step (assimilate the new observation)

The foundamental main equations are 
-the state equation (forecast step) x_k=Ax_{k-1}+Noise where:  x_k: state vector at time k and A is state transition matrix
-the analysis equation (observation step)  y-H x_k where y is the observation and H is the observation (link y and x) matrix 


         _______________________________________
        │  Prediction x_f(t) = A x_a(t-1)      |
        │  Cov. Error P_ f   = A P_a Aᵀ + Q    |
        |______________________________________|
                          │
                          ▼
         _______________________________________
        │          Observation arrive          |
        |        (buoys, radars, sats,...)     |  
        |______________________________________|
                          │
                          ▼
         _______________________________________
        │          Innovation: y - Hx_f        |
        |          Gain K = P_f Hᵀ/(HP_fHᵀ+R)  | 
        |______________________________________|
                          │
                          ▼
         _______________________________________
        │       Analysis/Correction/Update     |
        |          x_a = x_f + K(y-Hx_f)       |
        |          P_a = (I-KH) P_f            |
        |______________________________________|
                          │
                          ▼
        (Feed back into next forecast cycle)


 Kalman filter also give a balance between model uncertainty (Q) and observation uncertainty (R). This balance determine how reliable is the model vs the observation. However this is an umportant challenge in practices as uncertainty can be measured in several ways, and another problem  might be the distance between observation and model. This distance is not clear in model or obs variance


Kalman filtering type is also subject to discussion. From some papers I found, ensemble kalman filter (ENKF) is way more adapted for this project.  ENKF is EnKF is a Monte Carlo approximation of the Kalman filter.


# Discussion/
## Limitations
For now, the observational dataset is very limited, only 4 Surface Weather Observation Buoy. I hope to find more buoys on NOAA website, but I must be sure they give similar information to the one in given by MSC. Also, a large part of buoys are not available in winter.

Model output is now restricted to 24h and retrieve once a day, meaning overlap between two successives forecast is null. However, models outputs a given every 6 hours, and with a 48h horizon. In a future, I will strongly considers the overlapping forecasts on mutliple variable to, 1) compute model consistency /time accuracy, and 2) see how this can be used to reconstructing missing data (for waves)


## Current situation
I test an order 0 kalman filter (state= one variable), and also an order 1 kalman filter where state=[one variable, time derivative]. I also want to implement a multi state kalman filter, where variable co-dependency are taken into account, which mean a non diagonal state transition matrix.

 I’ve split a time series into two parts: the first part, which is the ‘past’, and the ‘future’, where I assume no observations are available yet (the ones that do exist are the target to reach). So I’m looking to obtain a correction of the forecasts in the past, as well as an adjustment of the forecasts in the future so that they better match the target to reach.


When I apply a filter with a state vector limited to [x], I obtain a satisfactory adjustment in the 'past', but almost no correction propagates into the 'future'.
In contrast, when I use an augmented state vector [x, x_time_derivative], the model adjustment in the 'past' matches the observations almost perfectly. While this outcome appears suspiciously overfitted, this configuration produces a more reasonable modification of the forecasts in the 'future'
The following figure is an analysis using an order 1 Kalman filter


<p align="center">
 <img width="600" src=test_kf_order1.jpg>
</p>

All variables are related in complex ways, but kalman filter is estimated separately for each variable (for now). As figure clearly show, Kalman filter is correctly operate while observations exist but fail to correct forecast when observations are 'missing'. Recall that after 16h (arbitrary) observations are the unseen target to reach.

# Next steps /open considerations
## Concerning Kalman filter
In the current situation x_a is replaced by the regional wave forecast model outputs... for now brutally... which is probably not the way to operate.  to apply  kalman Additionnaly in [this paper](https://idlcc.fc.ul.pt/pdf/Libonati_2008.pdf), kalman filter to correct forecast at x hour is done on all past observations at x hour. What I do here is very different!

## Other
A further challenge arises when a variable is not explicitly modeled—for example, wave parameters that are represented spectrally. In such cases, I would like to reconstruct a time series when model is output is nan, using knowledge from other available forecasts.

I also want to teste AutoRegressive Integrated Moving Average (ARIMA) procedure and Long Short-Term Memory (LSTM) deep learning algorithms to improve the forecasts. I think LSTM architecture has huge potential here.
