[<img src="https://github.com/QuantLet/Styleguide-and-Validation-procedure/blob/master/pictures/banner.png" alt="Visit QuantNet">](http://quantlet.de/index.php?p=info)

## [<img src="https://github.com/QuantLet/Styleguide-and-Validation-procedure/blob/master/pictures/qloqo.png" alt="Visit QuantNet">](http://quantlet.de/) **Quiz 10** [<img src="https://github.com/QuantLet/Styleguide-and-Validation-procedure/blob/master/pictures/QN2.png" width="60" alt="Visit QuantNet 2.0">](http://quantlet.de/d3/ia)

```yaml
Name of QuantLet : SFERV4 

Published in : SFE

Description : 'Realized variance analysis and graphical representation of 5 seconds intraday highfrequency
realized variance from the German stock market DAX and comparison with it's lagged subsample' 

Keywords : 'Volatility, graphical representation, time-series, log returns, variance'

Author : Luis Alejandro Sarmiento Abogado

Submitted : Tue, December 28 2015 by Luis Alejandro Sarmiento Abogado

Datafile : Dax Data current.csv
```
![Q_image](https://github.com/saabogal/SFM/blob/master/Dax-5-sec/DAX%201.png?raw=true)
![Q_image](https://github.com/saabogal/SFM/blob/master/Dax-5-sec/DAX2.png?raw=true)
![Q_image](https://github.com/saabogal/SFM/blob/master/Dax-5-sec/DAX3.png?raw=true)
![Q_image](https://github.com/saabogal/SFM/blob/master/Dax-5-sec/DAX4.png?raw=true)


```r
# Dax realized variance, observed and predicted volatility and price
# movements

# Clear enviorenment
graphics.off()
rm(list = ls())

# Load packages
library(highfrequency)
library(PerformanceAnalytics)

# Download file
dax = read.csv(file = "Dax Data current.csv", sep = ",", head = TRUE, na.strings = c("", 
                                                                                     "NA"))

# Download data and transform it to a data frame
dax = as.data.frame(dax)

# Select my working vextors for 5 minutes daily realized variance
daxrv = dax$Realized.Variance..5.minute.

# Convert the date strings to charachter vectors in order to transform
# it to Date format.
dax[, 1] = as.character(dax[, 1])
daxt     = dax$DateID

# Transform the time and date charachter vectors to a valid R format
time = as.POSIXct(paste(daxt), format = "%Y-%m-%d")

# set our xts elements Realized volatility 5 min
daxrv = xts(daxrv, order.by = time)

# Returns 5 min
returns = xts(dax[, 4], order.by = time)

# 5 minutes with one minute subsampling
daxrvs = xts(dax[, 5], order.by = time)

# Eliminate NA's
daxrv  = daxrv[complete.cases(daxrv)]
daxrvs = daxrvs[complete.cases(daxrvs)]

# Apply har model
daxm = harModel(data = daxrv, periods = c(1, 5, 22), RVest = c("rCov"), 
                type = "HARRV", h = 1, transform = NULL)

# Plot observed and forecasted volatility
png(filename = "DAX1.png")

par(mfrow = c(2, 1))

plot(daxm)

# Plot realized variance
cycles.dates = list(c("2000-01-03", "2002-01-03"), c("2008-09-01", "2010-09-01"), 
                    c("2015-06-05", "2015-12-15"))
risk.dates = c("2000-01-04", "2008-09-01", "2015-06-03")
risk.labels = c("Dot-com bubble", "Global Crisis", "Commodities volatility")

chart.TimeSeries(daxrv, type = "l", main = "Dax volatility", ylab = "Return", 
                 col = "black", period.areas = cycles.dates, period.color = "lightgoldenrod3", 
                 event.lines = risk.dates, event.labels = risk.labels, event.color = "red", 
                 lwd = 1)


dev.off()

# plot returns
png(filename = "DAX2.png")

chart.TimeSeries(returns, type = "l", main = "DAX returns", ylab = "Return", 
                 period.areas = cycles.dates, period.color = " lightcoral", event.lines = risk.dates, 
                 event.labels = risk.labels, event.color = "blue", lwd = 1)

dev.off()

# 2008 Volatility
daxrv8 = daxrv["2008"]

# 2008 one minute lagged variance
daxrvs8 = daxrvs["2008"]

# apply har model
daxm8 = harModel(data = daxrv8, periods = c(1, 5, 22), RVest = c("rCov"), 
                 type = "HARRV", h = 1, transform = NULL)

# Plot harModel 2008
png(filename = "DAX3.png")

plot(daxm8, cwd = 0.5, bg = "pch")

dev.off()

# Plot realized variance and a volatility approx.
png(filename = "DAX4.png")

par(mfrow = c(3, 1))

chart.TimeSeries(daxrv8, col = "red", type = "h", main = "5 minutes realized volatility", 
                 ylab = "RV", xlab = "Time")

# Plot realized volatility with subsampling
chart.TimeSeries(daxrvs8, col = "blue", type = "h", main = " 5 minutes realized volatility with subsampling", 
                 ylab = "RV", xlab = "Time")

# set difference between volatilities with and wothout subsampling.
diff = daxrv8 - daxrvs8

# Plot the volatility differences
chart.TimeSeries(diff, col = "purple", type = "h", main = " Difference in lagged and normal volatility", 
                 ylab = "RV", xlab = "Time")

dev.off()
  

```




`





