
## Algorithmic Trading Strategy

## Introduction

In this assignment, you will develop an algorithmic trading strategy by incorporating financial metrics to evaluate its profitability. This exercise simulates a real-world scenario where you, as part of a financial technology team, need to present an improved version of a trading algorithm that not only executes trades but also calculates and reports on the financial performance of those trades.

## Background

Following a successful presentation to the Board of Directors, you have been tasked by the Trading Strategies Team to modify your trading algorithm. This modification should include tracking the costs and proceeds of trades to facilitate a deeper evaluation of the algorithm’s profitability, including calculating the Return on Investment (ROI).

After meeting with the Trading Strategies Team, you were asked to include costs, proceeds, and return on investments metrics to assess the profitability of your trading algorithm.

## Objectives

1. **Load and Prepare Data:** Open and run the starter code to create a DataFrame with stock closing data.

2. **Implement Trading Algorithm:** Create a simple trading algorithm based on daily price changes.

3. **Customize Trading Period:** Choose your entry and exit dates.

4. **Report Financial Performance:** Analyze and report the total profit or loss (P/L) and the ROI of the trading strategy.

5. **Implement a Trading Strategy:** Implement a trading strategy and analyze the total updated P/L and ROI. 

6. **Discussion:** Summarise your finding.


## Instructions

### Step 1: Data Loading

Start by running the provided code cells in the "Data Loading" section to generate a DataFrame containing AMD stock closing data. This will serve as the basis for your trading decisions. First, create a data frame named `amd_df` with the given closing prices and corresponding dates. 

```r
# Load data from CSV file
amd_df <- read.csv("AMD.csv")
# Convert the date column to Date type and Adjusted Close as numeric
amd_df$date <- as.Date(amd_df$Date)
amd_df$close <- as.numeric(amd_df$Adj.Close)
amd_df <- amd_df[, c("date", "close")]
```

#### Plotting the Data
Plot the closing prices over time to visualize the price movement.
```r
plot(amd_df$date, amd_df$close,'l')
```

### Step 2: Trading Algorithm
Implement the trading algorithm as per the instructions. You should initialize necessary variables, and loop through the dataframe to execute trades based on the set conditions.

- Initialize Columns: Start by ensuring dataframe has columns 'trade_type', 'costs_proceeds' and 'accumulated_shares'.
- Change the algorithm by modifying the loop to include the cost and proceeds metrics for buys of 100 shares. Make sure that the algorithm checks the following conditions and executes the strategy for each one:
  - If the previous price = 0, set 'trade_type' to 'buy', and set the 'costs_proceeds' column to the current share price multiplied by a `share_size` value of 100. Make sure to take the negative value of the expression so that the cost reflects money leaving an account. Finally, make sure to add the bought shares to an `accumulated_shares` variable.
  - Otherwise, if the price of the current day is less than that of the previous day, set the 'trade_type' to 'buy'. Set the 'costs_proceeds' to the current share price multiplied by a `share_size` value of 100.
  - You will not modify the algorithm for instances where the current day’s price is greater than the previous day’s price or when it is equal to the previous day’s price.
  - If this is the last day of trading, set the 'trade_type' to 'sell'. In this case, also set the 'costs_proceeds' column to the total number in the `accumulated_shares` variable multiplied by the price of the last day.



```r
# Initialize columns to ensure the dataframe has columns 'trade_type', 'cost_proceed s', and 'accumulated_shares' in amd_df
amd_df$trade_type <- NA
amd_df$costs_proceeds <- NA
amd_df$accumulated_shares <- 0
# Initialize variables
previous_price <- 0
share_size <- 100
accumulated_shares <- 0
# Loop through the dataframe to apply the trading algorithm
for (i in 1:nrow(amd_df)) { current_price <- amd_df$close[i]
# For the first day of trading, set the trade type to 'buy'
if (previous_price == 0) {
# Initial buy amd_df$trade_type[i] <- "buy"
# Calculate cost_proceeds and accumulated_shares
    amd_df$costs_proceeds[i] <- -current_price * share_size
    accumulated_shares <- accumulated_shares + share_size
# Check if the current_price is less than the previous_price, and if it is, set the t
rade type to 'buy'
} else if (current_price < previous_price) {
# Buy when the current day's price is less than the previous day's price amd_df$trade_type[i] <- "buy"
amd_df$costs_proceeds[i] <- -current_price * share_size accumulated_shares <- accumulated_shares + share_size
}
# Update the accumulated shares in the dataframe
  amd_df$accumulated_shares[i] <- accumulated_shares
# Check if this is the last day of trading, and if it is, set the trade type to 'sel
l'
if (i == nrow(amd_df)) {
amd_df$trade_type[i] <- "sell"
amd_df$costs_proceeds[i] <- current_price * accumulated_shares
}
# Hold when the current day's price is greater than or equal to the previous day's pr
ice
else if (current_price >= previous_price) { amd_df$trade_type[i] <- "hold" amd_df$costs_proceeds[i] <- 0
}
# Update the previous price for the next iteration
  previous_price <- current_price
}
```


### Step 3: Customize Trading Period
- Define a trading period you wanted in the past five years 
```r
# Fill your code here
# Define the trading period (between "2023-01-01" and "2023-12-31")
start_date <- as.Date("2023-01-01")
end_date <- as.Date("2023-12-31")
# Filter the dataframe to include only the rows where the date column is between "202 3-01-01" and "2023-12-31"
amd_df <- amd_df[amd_df$date >= start_date & amd_df$date <= end_date, ]
# Reinitialize necessary columns and variables after adjusting the trading period
amd_df$trade_type <- NA
amd_df$costs_proceeds <- NA
amd_df$accumulated_shares <- 0
previous_price <- 0
accumulated_shares <- 0
# Loop through the filtered dataframe
for (i in 1:nrow(amd_df)) { current_price <- amd_df$close[i]
# For the first day of the trading period, set the trade type to 'buy'
if (previous_price == 0) {
# First day buy
amd_df$trade_type[i] <- 'buy'
amd_df$costs_proceeds[i] <- -current_price * share_size accumulated_shares <- accumulated_shares + share_size
# # Check if the current_price is less than the previous_price, and if it is, set the
trade type to 'buy'
} else if (current_price < previous_price) {
# Buy if current price is less than previous price amd_df$trade_type[i] <- 'buy'
amd_df$costs_proceeds[i] <- -current_price * share_size accumulated_shares <- accumulated_shares + share_size
}
# Check if this is the last day of trading, and if it is, set the trade type to 'sel
l'
if (i == nrow(amd_df)) {
# Sell on the last day
amd_df$trade_type[i] <- 'sell'
amd_df$costs_proceeds[i] <- current_price * accumulated_shares
}
# Hold when the current day's price is greater than or equal to the previous day's pr
ice
else if (current_price >= previous_price) { amd_df$trade_type[i] <- "hold" amd_df$costs_proceeds[i] <- 0
}
# Update accumulated shares
  amd_df$accumulated_shares[i] <- accumulated_shares
# Update previous price
  previous_price <- current_price
}
```


### Step 4: Run Your Algorithm and Analyze Results
After running your algorithm, check if the trades were executed as expected. Calculate the total profit or loss and ROI from the trades.

- Total Profit/Loss Calculation: Calculate the total profit or loss from your trades. This should be the sum of all entries in the 'costs_proceeds' column of your dataframe. This column records the financial impact of each trade, reflecting money spent on buys as negative values and money gained from sells as positive values.
- Invested Capital: Calculate the total capital invested. This is equal to the sum of the 'costs_proceeds' values for all 'buy' transactions. Since these entries are negative (representing money spent), you should take the negative sum of these values to reflect the total amount invested.
- ROI Formula: $$\text{ROI} = \left( \frac{\text{Total Profit or Loss}}{\text{Total Capital Invested}} \right) \times 100$$

```r
# Fill your code here
# Total profit or loss
total_profit_loss <- sum(amd_df$costs_proceeds, na.rm = TRUE)
# Invested capital
total_invested_capital <- -sum(amd_df$costs_proceeds[amd_df$trade_type == "buy"], na.
rm = TRUE)
# ROI calculation
roi <- (total_profit_loss / total_invested_capital) * 100
# Print the calculations to the console
cat("Total Profit or Loss: ", total_profit_loss, "\n")
```

### Step 5: Profit-Taking Strategy or Stop-Loss Mechanisum (Choose 1)
- Option 1: Implement a profit-taking strategy that you sell half of your holdings if the price has increased by a certain percentage (e.g., 20%) from the average purchase price.
- Option 2: Implement a stop-loss mechanism in the trading strategy that you sell half of your holdings if the stock falls by a certain percentage (e.g., 20%) from the average purchase price. You don't need to buy 100 stocks on the days that the stop-loss mechanism is triggered.


```r
# Option 1:
# Fill your code here
# Initialize variables for tracking purchases and sales previous_price <- NA
share_size <- 100
accumulated_shares <- 0
total_cost <- 0
total_shares_bought <- 0
# Define the profit-taking threshold
profit_threshold <- 1.20
# Loop through the dataframe to apply the trading algorithm and profit-taking strateg y
for (i in 1:nrow(amd_df)) {
  current_price <- amd_df$close[i]
if (is.na(previous_price)) {
# Set rade type to buy on the first day
    amd_df$trade_type[i] <- "buy"
    amd_df$costs_proceeds[i] <- -current_price * share_size
    accumulated_shares <- accumulated_shares + share_size
    total_cost <- total_cost + (current_price * share_size)
    total_shares_bought <- total_shares_bought + share_size
} else if (current_price < previous_price) {
# Buy when the current day's price is less than the previous day's price
    amd_df$trade_type[i] <- "buy"
    amd_df$costs_proceeds[i] <- -current_price * share_size
    accumulated_shares <- accumulated_shares + share_size
    total_cost <- total_cost + (current_price * share_size)
    total_shares_bought <- total_shares_bought + share_size
} else if (total_shares_bought > 0 && current_price >= (total_cost / total_shares_b ought) * profit_threshold) {
# Profit-taking: sell half of the holdings if the price has increased by the profit t hreshold
    shares_to_sell <- accumulated_shares / 2
    amd_df$trade_type[i] <- "sell"
    amd_df$costs_proceeds[i] <- shares_to_sell * current_price
    accumulated_shares <- accumulated_shares - shares_to_sell
} else if (i == nrow(amd_df)) {
# Sell all remaining shares on the last day
    amd_df$trade_type[i] <- "sell"
amd_df$costs_proceeds[i] <- accumulated_shares * current_price } else {
# Hold when no buy or sell condition is met
    amd_df$trade_type[i] <- "hold"
    amd_df$costs_proceeds[i] <- 0
  }
# Update the accumulated shares in the dataframe
  amd_df$accumulated_shares[i] <- accumulated_shares
# Update the previous price for the next iteration
  previous_price <- current_price
  }
```


### Step 6: Summarize Your Findings
- Did your P/L and ROI improve over your chosen period?
- Relate your results to a relevant market event and explain why these outcomes may have occurred.


```r
# Fill your code here and Disucss
# Calculate total profit or loss, ignoring NA values total_profit_loss <- sum(amd_df$costs_proceeds, na.rm = TRUE)
# Calculate invested capital (total amount invested), ignoring NA values
total_invested <- -sum(amd_df$costs_proceeds[amd_df$trade_type == "buy"], na.rm = TRU
E)
# Calculate ROI
roi <- (total_profit_loss / total_invested) * 100
# Print the results
cat("Total Profit/Loss: ", total_profit_loss, "\n")
```

Discussion: On May 2, 2023, AMD reported its Q1 2023 financial results. These results showed a slight, whilst showing a slight decrease in revenue, also showed robust growth over the first qurter of 2023. Hence this increased investor confidence and they likely beleived that the company had upwards potential. Thus, following this, on the 3rd of May 2023, there was an increase in AMD shares. Additionally, my first strategy earned $437602.6 more dollars than my second strategy implemented in step 5, thus showing a clear difference in profitability, and thus caused the significantly lower ROI in my second strategy compared to my first.




