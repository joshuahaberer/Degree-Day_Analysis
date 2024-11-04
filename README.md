# Degree-Day Analysis of Melt and Runoff for the Athabasca Glacier

This project uses a degree-day (temperature-index) model to estimate seasonal melt from the Athabasca Glacier and compare it to observed discharge data. The analysis includes data preparation, degree-day melt calculations, and visualizations to interpret seasonal trends and correlations between melt and runoff.

## Project Overview

This project applies the degree-day model to estimate glacier melt. The approach is based on the assumption that daily melt can be estimated from air temperature data using a Degree-Day Factor (DDF) that represents the rate of snow/ice melt per degree above 0°C (Hock, 2003).

The analysis includes:
(1) Climate and discharge data cleaning.
(2) Melt calculations using the degree-day model.
(3) Monthly aggregation of melt and discharge values.
(4) Plotting seasonal trends and assessing correlations.

## Equations

The degree-day model used for this project calculates melt as follows:

Melt=DDF×max(Mean Temp,0)

Where:
   - Melt = Estimated daily melt (in mm of water equivalent).
   - DDF = Degree-Day Factor, typically in mm/°C/day. Here, we use a value of 5 mm/°C/day.
   - Mean Temp = Mean daily air temperature (°C).
   - max(Mean Temp, 0) = A threshold function that sets melt to zero when temperatures are below 0°C, assuming no melt occurs.




