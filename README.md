# SVI_with_NLP_Optimization

# I. Research Background and Objectives

### 1.1 Background
1.1.1. **1987 Black Monday**: Highlighted the risks of extreme volatility, which can cause sudden market shocks.
1.1.2. **Recent Volatility (August 5, 2024)**: Global markets, especially tech stocks, experienced sharp declines, with the VIX surging over 65%.
1.1.3. **Limitations of Black-Scholes Model**: Assumes constant volatility, failing to account for the asymmetric and extreme volatility patterns observed in real markets, leading to option mispricing and arbitrage opportunities.
1.1.4. **Introduction of SVI Model**: The Stochastic Volatility Inspired (SVI) model addresses some of these limitations by dynamically modeling implied volatility and explaining the volatility smile in extreme market conditions.
1.1.5. **Need for Improvement**: Even the SVI model has limitations in fully capturing volatility asymmetry. Hence, this study proposes a Post-SVI model incorporating 'Convex Duality' to improve option pricing and dynamic hedging in extreme volatility scenarios.

### 1.2 Research Objectives
1.2.1. **Improve Option Pricing Accuracy**: The goal is to enhance option pricing during extreme volatility conditions using the Post-SVI model, incorporating convexity duality to better estimate implied volatility.
1.2.2. **Eliminate Arbitrage Opportunities**: By improving the modeling of volatility smiles and addressing mispricing, the study aims to create an arbitrage-free environment.
1.2.3. **Enhance Dynamic Delta Hedging**: Utilize real-time dynamic delta hedging strategies based on the refined Post-SVI model to improve risk management and market stability during volatile periods.


# II. Literature Review

### 2.1 Arbitrage-Free SVI Volatility Surfaces

2.1.1. **SVI and Static Arbitrage**:  
   Jim Gatheral and Antoine Jacquier's work on "Arbitrage-Free SVI Volatility Surfaces" demonstrates how SVI can be used to prevent static arbitrage in options pricing. The SVI model ensures no arbitrage opportunities arise by fitting well with market data.

2.1.2. **Types of Static Arbitrage**:  
   - **Calendar Spread Arbitrage**: This occurs when options with different expirations on the same underlying asset violate price symmetry. The model ensures that volatility surfaces increase monotonically over time to avoid arbitrage.
   - **Butterfly Arbitrage**: This happens due to irregular curvature in implied volatility across strike prices. The SVI model controls the curvature, preventing extreme volatility spikes or drops that could lead to risk-free profits.

2.1.3. **Lee's Formula and Volatility Asymmetry**:  
   Lee's formula limits the growth of implied volatility at extreme strike prices to avoid unrealistic volatility smiles. The SVI model incorporates this through its parameters (a, b, ρ, m, σ), ensuring a balanced and realistic volatility surface.

### 2.2 SVI Parameter Calibration

2.2.1. **Parameter Importance**:  
   The initial parameter setup in the SVI model significantly influences its ability to fit the volatility surface and prevent arbitrage. The parameters a, b, ρ, m, and σ must be properly constrained to ensure realistic and stable volatility surface formation.

2.2.2. **Parameter Range**:  
   To achieve effective model calibration, the following parameter constraints are used:  
   
   \[
   0 \leq a \leq \max_{i}[\omega_i],
   \]
   \[
   0 \leq b \leq \frac{4}{\tau(1 + |\rho|)},
   \]
   \[
   -1 \leq \rho \leq 1,
   \]
   \[
   \min_{i}[x_i] \leq m \leq \max_{i}[x_i],
   \]
   \[
   0 < \sigma_{\text{min}} \leq \sigma \leq 10.
   \]

   These constraints help ensure that the SVI model's parameters (a, b, ρ, m, σ) are set within reasonable and realistic ranges, allowing the model to avoid arbitrage while accurately fitting the implied volatility surface.


# III. Stochastic Volatility Inspired (SVI) Model

### 3.1 SVI Model

$$
w(k) = a + b \left( \rho (k - m) + \sqrt{(k - m)^2 + \sigma^2} \right)
$$


The Stochastic Volatility Inspired (SVI) model was introduced by Jim Gatheral of Merrill Lynch in 1999. It is a simple parametric model used to explain the volatility smile observed in the options market. The SVI model expresses implied volatility as a function of the option’s time to maturity and the difference between the strike price and the underlying asset price. 

1. **Key Concept**:  
   The primary purpose of the SVI model is to fit the volatility smile with a smooth and convex function, allowing it to accurately capture the implied volatility of options across different strike prices and maturities. A convex volatility curve ensures that implied volatility behaves consistently and avoids theoretical mispricing, leading to more accurate option pricing.

2. **Convexity and Arbitrage**:  
   Convexity plays a crucial role in ensuring that the volatility smile is stable and rational across the market. A non-convex or irregular volatility smile could indicate arbitrage opportunities where option prices deviate from theoretical values. By using the SVI model to fit a convex curve, such arbitrage opportunities are minimized, improving market efficiency.

3. **Risk Management**:  
   The convexity of the volatility smile is also vital for managing risk and developing hedging strategies. When volatility is expressed smoothly and consistently, key Greeks like delta, gamma, and vega can be calculated more accurately, allowing for more effective portfolio risk management and more precise hedging strategies, such as delta or gamma hedging.

### SVI Jump-Wings Parameterization

The **SVI Jump-Wings Parameterization** is inspired by the approach proposed by Tim Klassen, designed to reflect changes in the implied volatility surface more realistically. This parameterization uses implied variance instead of total implied variance and explicitly incorporates time-to-expiration dependency. It is defined using the following raw parameters:

\[
\nu_\tau = \frac{a + b \left( - \rho m + \sqrt{m^2 + \sigma^2} \right)}{\tau},
\]

\[
\phi_\tau = \frac{1}{\sqrt{\omega_\tau}} \frac{b}{2} \left( \rho - \frac{m}{\sqrt{m^2 + \sigma^2}} \right),
\]

\[
p_\tau = \frac{1}{\sqrt{\omega_\tau}} b (1 - \rho),
\]

\[
c_\tau = \frac{1}{\sqrt{\omega_\tau}} b (1 + \rho),
\]

\[
\hat{\nu}_\tau = \frac{1}{\tau} \left( a + b \sigma \sqrt{1 - \rho^2} \right).
\]

