# Degree-Day Analysis of Melt and Runoff for the Athabasca Glacier
Created By: Joshua Haberer

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

## Code
#### Data Setup
This section loads the required libraries for this analysis.

```
# Load in libraries
library(dplyr)
library(ggplot2)
library(lubridate)
library(corrplot)
library(kableExtra)
```
Set the working directory to ensure the code accesses the necessary data files.

```
# Set working directory
dir <- "~/Desktop/Degree_Day_Analysis"
setwd(dir)
```
This section defines file paths, then loads and cleans climate data for each year. It selects relevant columns, converts dates, and filters for specified months.
```
# Define file paths for each year's data
files <- list("./2005_PR.csv", "./2006_PR.csv", "./2007_PR.csv")

# Function to load, clean, and select necessary columns for each climate file
load_and_clean <- function(file) {
  data <- read.csv(file)
  
  # Select necessary columns
  data <- data %>%
    select(Longitude = `Longitude..x.`, Latitude = `Latitude..y.`, Station_Name = `Station.Name`, Date = `Date.Time`, Mean_Temp = `Mean.Temp...C.`)
  
  # Convert Date column to Date format
  data$Date <- as.Date(data$Date)
  
  # Extract Year and Month for filtering
  data <- data %>%
    mutate(Year = year(Date), Month = month(Date))
  
  return(data)
}

# Load all climate files and bind them into one dataframe
climate_data <- do.call(rbind, lapply(files, load_and_clean))

# Filter data based on specified months
climate_data <- climate_data %>%
  filter((Year == 2005 & Month >= 6 & Month <= 9) |
           (Year == 2006 & Month >= 5 & Month <= 10) |
           (Year == 2007 & Month >= 5 & Month <= 10))

# View cleaned and filtered data
head(climate_data)
```
This code reads the discharge data, selects relevant columns, converts the date format, and filters for specified months.
```
# Read and clean discharge data
hydro_data <- read.csv("./Hydrology.csv")
hydro_data <- hydro_data %>%
  select(ID, Date, Value) %>%
  mutate(Date = as.Date(Date, format = "%Y/%m/%d"),
         Year = year(Date),
         Month = month(Date)) %>%
  filter((Year == 2005 & Month >= 6 & Month <= 9) |
           (Year == 2006 & Month >= 5 & Month <= 10) |
           (Year == 2007 & Month >= 5 & Month <= 10))

# View cleaned discharge data
head(hydro_data)
```
#### Degree-Day Melt Calculation
In this section, the degree-day model is applied to calculate daily melt. The DDF is set to 5 based on Hock (2003).
```
# Set degree-day factor
degree_day_factor <- 5  # mm/°C/day

# Calculate daily melt and add to dataframe
climate_data <- climate_data %>%
  mutate(Daily_Melt = pmax(Mean_Temp, 0) * degree_day_factor)
```
#### Aggregating Data
This code aggregates daily melt and discharge data to monthly totals for analysis.
```
# Aggregate melt and discharge data to monthly totals
monthly_melt <- climate_data %>%
  group_by(Year, Month) %>%
  summarize(Total_Melt = sum(Daily_Melt))

monthly_discharge <- hydro_data %>%
  group_by(Year, Month) %>%
  summarise(Total_Discharge = sum(Value))

# Combine melt and discharge data
combined_data <- full_join(monthly_melt, monthly_discharge, by = c("Year", "Month"))
```
#### Plotting Melt and Discharge
This plot compares seasonal melt and discharge trends.
```
# Plot melt and discharge
ggplot(combined_data, aes(x = Month)) +
  geom_line(aes(y = Total_Melt, color = "Melt (mm)")) +
  geom_line(aes(y = Total_Discharge, color = "Discharge (m³/s)")) +
  facet_wrap(~ Year) +
  labs(x = "Month", y = "Value", title = "Melt and Discharge Comparison")
```
![image](https://github.com/user-attachments/assets/39433773-3ea1-4844-a6a3-adcdc118fe68)
#### Correlation Analysis
Calculate and visualize the correlation between monthly melt and discharge values.
```
# Calculate correlation
cor(combined_data$Total_Melt, combined_data$Total_Discharge)
> [1] 0.9603841

# Visualize correlation
ggplot(combined_data, aes(x = Total_Melt, y = Total_Discharge)) +
  geom_point() +
  labs(x = "Total Melt (mm)", y = "Total Discharge (m³/s)", title = "Melt vs. Discharge Correlation")
```
![image](https://github.com/user-attachments/assets/3b814fc5-a12f-46d6-8286-16737e0110c8)

#### Summary Table of Results
```
# Summarize data by year for reporting
summary_data <- combined_data %>%
  group_by(Year) %>%
  summarize(Mean_Melt = mean(Total_Melt),
            SD_Melt = sd(Total_Melt),
            Mean_Discharge = mean(Total_Discharge),
            SD_Discharge = sd(Total_Discharge))

# Display summary table
table_data <- data.frame(
  Year = summary_data$Year,
  "Mean Melt (mm)" = summary_data$Mean_Melt,
  "SD Melt (mm)" = summary_data$SD_Melt,
  "Mean Discharge (m³/s)" = summary_data$Mean_Discharge,
  "SD Discharge (m³/s)" = summary_data$SD_Discharge
)

print(kable(table_data, booktabs = TRUE))
```
![image](https://github.com/user-attachments/assets/a376dae2-472c-4a2d-8d83-8afac59e7e8e)

## Results
The code provided calculates and visualizes seasonal melt patterns and their relationship with discharge. It demonstrates how well the degree-day model estimates seasonal trends but also highlights the limitations for more detailed or spatially specific analysis.


