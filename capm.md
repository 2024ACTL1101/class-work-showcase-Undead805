
# CAPM Analysis

## Introduction

In this assignment, you will explore the foundational concepts of the Capital Asset Pricing Model (CAPM) using historical data for AMD and the S&P 500 index. This exercise is designed to provide a hands-on approach to understanding how these models are used in financial analysis to assess investment risks and returns.

## Background

The CAPM provides a framework to understand the relationship between systematic risk and expected return, especially for stocks. This model is critical for determining the theoretically appropriate required rate of return of an asset, assisting in decisions about adding assets to a diversified portfolio.

## Objectives

1. **Load and Prepare Data:** Import and prepare historical price data for AMD and the S&P 500 to ensure it is ready for detailed analysis.
2. **CAPM Implementation:** Focus will be placed on applying the CAPM to examine the relationship between AMD's stock performance and the overall market as represented by the S&P 500.
3. **Beta Estimation and Analysis:** Calculate the beta of AMD, which measures its volatility relative to the market, providing insights into its systematic risk.
4. **Results Interpretation:** Analyze the outcomes of the CAPM application, discussing the implications of AMD's beta in terms of investment risk and potential returns.

## Instructions

### Step 1: Data Loading

- We are using the `quantmod` package to directly load financial data from Yahoo Finance without the need to manually download and read from a CSV file.
- `quantmod` stands for "Quantitative Financial Modelling Framework". It was developed to aid the quantitative trader in the development, testing, and deployment of statistically based trading models.
- Make sure to install the `quantmod` package by running `install.packages("quantmod")` in the R console before proceeding.

```r
# Set start and end dates
start_date <- as.Date("2019-05-20")
end_date <- as.Date("2024-05-20")

# Load data for AMD, S&P 500, and the 1-month T-Bill (DTB4WK)
amd_data <- getSymbols("AMD", src = "yahoo", from = start_date, to = end_date, auto.assign = FALSE)
gspc_data <- getSymbols("^GSPC", src = "yahoo", from = start_date, to = end_date, auto.assign = FALSE)
rf_data <- getSymbols("DTB4WK", src = "FRED", from = start_date, to = end_date, auto.assign = FALSE)

# Convert Adjusted Closing Prices and DTB4WK to data frames
amd_df <- data.frame(Date = index(amd_data), AMD = as.numeric(Cl(amd_data)))
gspc_df <- data.frame(Date = index(gspc_data), GSPC = as.numeric(Cl(gspc_data)))
rf_df <- data.frame(Date = index(rf_data), RF = as.numeric(rf_data[,1]))  # Accessing the first column of rf_data

# Merge the AMD, GSPC, and RF data frames on the Date column
df <- merge(amd_df, gspc_df, by = "Date")
df <- merge(df, rf_df, by = "Date")
```

#### Data Processing 
```r
colSums(is.na(df))
# Fill N/A RF data
df <- df %>%
  fill(RF, .direction = "down") 
```

### Step 2: CAPM Analysis

The Capital Asset Pricing Model (CAPM) is a financial model that describes the relationship between systematic risk and expected return for assets, particularly stocks. It is widely used to determine a theoretically appropriate required rate of return of an asset, to make decisions about adding assets to a well-diversified portfolio.

#### The CAPM Formula
The formula for CAPM is given by:

$$
E(R_i) = R_f + \beta_i (E(R_m) - R_f)
$$

Where:

- $E(R_i)$ is the expected return on the capital asset,
- $R_f$ is the risk-free rate,
- $\beta_i$ is the beta of the security, which represents the systematic risk of the security,
- $E(R_m)$ is the expected return of the market.



#### CAPM Model Daily Estimation

- **Calculate Returns**: First, we calculate the daily returns for AMD and the S&P 500 from their adjusted closing prices. This should be done by dividing the difference in prices between two consecutive days by the price at the beginning of the period.
  
$$
\text{Daily Return} = \frac{\text{Today's Price} - \text{Previous Trading Day's Price}}{\text{Previous Trading Day's Price}}
$$

```r
#fill the code
# Calculate daily returns df <- df %>%
  mutate(
    AMD_return = (AMD - lag(AMD)) / lag(AMD),
    GSPC_return = (GSPC - lag(GSPC)) / lag(GSPC)
)
# Remove NA values
df <- na.omit(df)
```

- **Calculate Risk-Free Rate**: Calculate the daily risk-free rate by conversion of annual risk-free Rate. This conversion accounts for the compounding effect over the days of the year and is calculated using the formula:
  
$$
\text{Daily Risk-Free Rate} = \left(1 + \frac{\text{Annual Rate}}{100}\right)^{\frac{1}{360}} - 1
$$

```r
#fill the code
# Convert the annual risk-free rate to a daily rate df <- df %>%
  mutate(RF_daily = (1 + RF/100)^(1/360) - 1)
```


- **Calculate Excess Returns**: Compute the excess returns for AMD and the S&P 500 by subtracting the daily risk-free rate from their respective returns.

```r
#fill the code
# Calculate excess returns df <- df %>%
  mutate(
    AMD_excess_return = AMD_return - RF_daily,
    GSPC_excess_return = GSPC_return - RF_daily
)
```


- **Perform Regression Analysis**: Using linear regression, we estimate the beta (\(\beta\)) of AMD relative to the S&P 500. Here, the dependent variable is the excess return of AMD, and the independent variable is the excess return of the S&P 500. Beta measures the sensitivity of the stock's returns to fluctuations in the market.

```r
#fill the code
# Perform linear regression and thus estimate beta
capm_model <- lm(AMD_excess_return ~ GSPC_excess_return, data = df) summary(capm_model)
```


#### Interpretation

What is your \(\beta\)? Is AMD more volatile or less volatile than the market?

**Answer:**
My beta of AMD is 1.5699987 which is approximately 1.57. This indicates that AMD is more volatile than the market. Specifically, AMD’s returns are expected to be 57% more volatile than the market’s returns. This implies that if the market goes up by 10%, AMD’s stock is expected to go up by 15.7%. Conversely, if the market goes down by 10%, AMD’s stock is expected to go down by 15.7%.

#### Plotting the CAPM Line
Plot the scatter plot of AMD vs. S&P 500 excess returns and add the CAPM regression line.

```r
#fill the code
# Plot scatter plot and CAPM regression line plot(df$GSPC_excess_return, df$AMD_excess_return,
     main = "CAPM: AMD Excess Return vs. S&P 500 Excess Return",
     xlab = "S&P 500 Excess Return", ylab = "AMD Excess Return", pch = 16)
abline(capm_model, col = "blue")
```

### Step 3: Predictions Interval
Suppose the current risk-free rate is 5.0%, and the annual expected return for the S&P 500 is 13.3%. Determine a 90% prediction interval for AMD's annual expected return.



**Answer:**

```r
#fill the code
# Calculate daily standard error of the forecast sf <- summary(capm_model)$sigma
# Calculate the annual standard error for prediction
annual_sf <- sf * sqrt(252)
# Set the risk-free rate and expected market return
current_rf <- 0.05
expected_market_return <- 0.133
# Calculate the expected return of AMD using CAPM
expected_amd_return <- current_rf + beta_amd * (expected_market_return - current_rf)
# Calculate the 90% prediction interval
alpha <- 0.10
z_value <- qnorm(1 - alpha/2)
lower_bound <- expected_amd_return - z_value * annual_sf
upper_bound <- expected_amd_return + z_value * annual_sf
# Output the prediction interval
cat("90% Prediction Interval for AMD's Annual Expected Return: [", lower_bound, ", ",
upper_bound, "]\n")
```
