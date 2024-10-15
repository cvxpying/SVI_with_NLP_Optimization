
# SPX Option Volatility Analysis

## Data Acquisition and Overview

This study utilizes SPX option data. Since options on the S&P 500 index are highly liquid, it allows for a more precise volatility analysis, which is the core of this research. Essential information for implementing volatility smiles (or surfaces) includes option prices, strike prices, expiration dates, and implied volatility. Therefore, the data used in this study contains such information, which is critical not only for simple volatility visualization but also for deriving theoretical option prices, detecting mispricing, and analyzing volatility.

- **Option price** and **strike price** are necessary to assess the current market value of the option.
- **Expiration information** is essential for analyzing the option's validity period.
- **Implied volatility** reflects the market's expectations of volatility, playing a crucial role in option price determination.

### Data Information

The data, including these elements, was obtained from one of the major option exchanges, CBOE, through the [CBOE Datashop](https://datashop.cboe.com/option-quote-intervals). In particular, we secured daily option data from August 5, a date with high liquidity. This data will be used to analyze the volatility landscape throughout the day and to devise dynamic delta hedging strategies. The dataset records both bid and ask prices for SPX options throughout the day, including the following information:

- `underlying_symbol`
- `quote_datetime`
- `root`
- `expiration`
- `strike`
- `option_type`
- `open`, `high`, `low`, `close`
- `trade_volume`
- `bid_size`, `bid`, `ask_size`, `ask`
- `underlying_bid`, `underlying_ask`
- `implied_underlying_price`, `active_underlying_price`
- `implied_volatility`
- Option Greeks: `delta`, `gamma`, `theta`, `vega`, `rho`

![image](https://github.com/user-attachments/assets/6618b3ed-0bfb-4125-b21f-c052e0cecfab)
![image](https://github.com/user-attachments/assets/8c5f36f2-d8e3-403e-b4a2-771651347386)


As indicated, the dataset includes crucial information such as expiration dates, strike prices, trade volume, bid/ask prices for both options and underlying assets, underlying asset prices, implied volatility, and Greeks. These elements are valuable for analyzing the factors influencing option price movements.

In this research, daily data is used for volatility analysis. This is because extreme volatility or volatility jumps can cause significant changes within a single day. The data from August 5, 2024, shows sharp volatility or volatility jumps, making it possible to conduct an in-depth analysis of arbitrage opportunities and market inefficiencies.


## Data Preprocessing

When applying the SVI model, it is common to focus on options where actual trades have occurred. This is because the SVI model explains implied volatility based on the actual market prices of options. To accurately draw the volatility smile curve, the implied volatility derived from the prices of traded options must be used. However, the acquired dataset includes not only traded options but also quoted data. Therefore, we filtered out options with a `trade_volume` of less than 1. (In a later section (3.4.4), we will proceed with the study using data with higher trade volumes than just 1.)

Strike prices were converted to log strike prices to facilitate the analysis of volatility smiles. The volatility smile typically shows asymmetry, with higher volatility for deep ITM or deep OTM options. Scaling through log strike prices clarifies this asymmetric volatility structure.

The expiration date of an option is a critical variable in the SVI model. Since volatility curves vary depending on the expiration date, it is necessary to fit the SVI model for each expiration. After calculating the time remaining until expiration, the SVI model parameters are estimated for each expiration date.

Finally, the data was split into call and put options. This separation is necessary because the price structure, volatility smiles, and market reactions for calls and puts differ. Call and put options have opposite pay-offs concerning the underlying asset's directional movement. Therefore, the pricing methods and the resulting implied volatility may differ. When analyzing this data, the distinct characteristics of calls and puts should be considered. Additionally, the volatility smile and skew generally differ between calls and puts, as do their hedging characteristics and Greeks. By analyzing call and put volatility smiles separately, we can better understand market participants' behavior and develop more accurate risk management strategies.


### Additional Preprocessing (Basics)

1. **Using Traded Option Data (Trade Volume ≥ 1)**  
   SVI model explains implied volatility based on actual market prices of options, so only traded option data is used (where `trade_volume` ≥ 1). This helps focus the analysis on options where actual trades have taken place, ensuring more reliable results.

2. **Strike Price Scaling (Adding log(S/K) Column)**  
   To better analyze the asymmetric nature of volatility smiles, strike prices are converted to log strike prices (`log(S/K)`). This scaling process helps to clearly reveal the asymmetry in volatility structures, especially where strike prices are deep in-the-money (ITM) or out-of-the-money (OTM).

3. **Separating Call and Put Data**  
   Call options and put options have opposite pay-offs and exhibit different price structures and implied volatilities. Therefore, separating call and put data allows for a more precise analysis of the respective volatility smiles and provides clearer insights into the market behavior for each type of option.

4. **Filtering by Expiration Date**  
   Volatility curves vary by expiration date, so it is necessary to fit the SVI model for each expiration. The time to expiration is calculated for each option, and the SVI model parameters are estimated separately for each expiration date.

5. **Volatility Smile Visualization**  
   The volatility smiles are visualized after scaling and preprocessing the data to observe the asymmetric volatility structures. This will guide the subsequent application of the SVI model.



### Additional Preprocessing (IQR & MAD Methods)

- **IQR Method**: 
  The IQR (Inter-Quartile Range) method is applied to remove extreme outliers from the data distribution. Options with extremely low trade volumes relative to the rest are filtered out. 

- **MAD Method**: 
  After applying the IQR method, the MAD (Median Absolute Deviation) method is used to further refine the dataset by adjusting the volatility smile of low-volume data, ensuring that remaining outliers are removed.

---

#### Filtered Volatility Smile (Call Options)
Visualized below are the filtered volatility smiles for call options, following the application of the IQR and MAD methods. Although the filtered smiles exhibit improved shapes compared to the unfiltered data, there are still portions that appear irregular or noisy. These sections will undergo further optimization using the SVI model for better results.



![filtered vol smile_call](https://github.com/user-attachments/assets/3ba6745c-742d-48be-a214-248bf369115e)
