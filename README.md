# SVI_with_NLP_Optimization

## 0. Special Thanks
This study was inspired by research conducted based on **'A New Simple Approach for Constructing Implied Volatility Surfaces'** by Peter Carr and Liuren Wu.


## I. Research Background and Objectives

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


## II. Literature Review

### 2.1 Arbitrage-Free SVI Volatility Surfaces

2.1.1. **SVI and Static Arbitrage**:  
   Jim Gatheral and Antoine Jacquier's work on "Arbitrage-Free SVI Volatility Surfaces" demonstrates how SVI can be used to prevent static arbitrage in options pricing. The SVI model ensures no arbitrage opportunities arise by fitting well with market data.

![image](https://github.com/user-attachments/assets/44cef19f-0fb6-4911-a842-13c141f19c90)


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
   
   $$
   0 \leq a \leq \max_{i}[\omega_i],
   $$
   $$
   0 \leq b \leq \frac{4}{\tau(1 + |\rho|)},
   $$
   $$
   -1 \leq \rho \leq 1,
   $$
   $$
   \min_{i}[x_i] \leq m \leq \max_{i}[x_i],
   $$
   $$
   0 < \sigma_{\text{min}} \leq \sigma \leq 10.
   $$

   These constraints help ensure that the SVI model's parameters (a, b, ρ, m, σ) are set within reasonable and realistic ranges, allowing the model to avoid arbitrage while accurately fitting the implied volatility surface.


## III. Stochastic Volatility Inspired (SVI) Model

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

### 3.2 SVI Jump-Wings Parameterization

The **SVI Jump-Wings Parameterization** is inspired by the approach proposed by Tim Klassen, designed to reflect changes in the implied volatility surface more realistically. This parameterization uses implied variance instead of total implied variance and explicitly incorporates time-to-expiration dependency. It is defined using the following raw parameters:

$$
\nu_\tau = \frac{a + b \left( - \rho m + \sqrt{m^2 + \sigma^2} \right)}{\tau},
$$

$$
\phi_\tau = \frac{1}{\sqrt{\omega_\tau}} \frac{b}{2} \left( \rho - \frac{m}{\sqrt{m^2 + \sigma^2}} \right),
$$

$$
p_\tau = \frac{1}{\sqrt{\omega_\tau}} b (1 - \rho),
$$

$$
c_\tau = \frac{1}{\sqrt{\omega_\tau}} b (1 + \rho),
$$

$$
\hat{\nu}_\tau = \frac{1}{\tau} \left( a + b \sigma \sqrt{1 - \rho^2} \right).
$$

### 3.3 SVI Model Optimization via Non-Linear Programming (NLP)

#### 3.3.1 Objective Function in the SVI Model

The objective function, along with constraints, is classified as a non-linear programming (NLP) problem. In the SVI model, the function that represents the volatility smile is defined as follows:

$$
w(k) = a + b \left( \rho (k - m) + \sqrt{(k - m)^2 + \sigma^2} \right)
$$

This function models the implied volatility curve based on several parameters to fit the volatility smile. The term \( \sqrt{(k - m)^2 + \sigma^2} \) indicates how volatility changes with the strike price, exhibiting a non-linear relationship. Therefore, optimizing the SVI model involves solving a non-linear optimization problem to ensure that the volatility smile is properly fitted.


## IV. Problem Solving (Optimization)

### 4.1 Definition of the Primal Problem

The primal objective is to minimize the error between the market-observed total variance \( \hat{w}(k_i) \) and the total variance calculated by the SVI model \( w(k_i) \). The Mean Squared Error (MSE) or Mean Absolute Error (MAE) is typically used to achieve this, and the primal problem is structured as:

$$
\min_{a, b, \rho, m, \sigma} \sum_{i=1}^{N} \left( \hat{w}(k_i) - w(k_i) \right)^2
$$

Where \( N \) represents the number of volatility data points. The constraints ensuring convexity and arbitrage-free conditions are as follows:

$$
b > 0, \quad -1 < \rho < 1, \quad \sigma > 0
$$

### 4.2 Lagrangian Transformation

The primal problem is converted into a Lagrangian to account for the imposed constraints. The Lagrangian is given by:

$$
L(a, b, \rho, m, \sigma, \lambda) = \sum_{i=1}^{N} \left( \hat{w}(k_i) - w(k_i) \right)^2 + \lambda_1 (b - 0) + \lambda_2 (\rho (1 - 1)) + \lambda_3 (\sigma - 0)
$$

Here, \( \lambda_1, \lambda_2, \lambda_3 \) are the Lagrange multipliers that enforce the constraints.


### 4.3 Types of NLP Optimization Algorithms

The following algorithms can be used to solve non-linear optimization problems:

- **Gradient-based Algorithms**:  
  The BFGS (Broyden-Fletcher-Goldfarb-Shanno) algorithm computes gradients and Hessians to achieve fast convergence, making it suitable for large-scale non-linear problems. L-BFGS (Limited-memory BFGS) is a variation of BFGS that reduces memory usage and is more efficient for large datasets.

- **Stochastic Optimization Algorithms**:  
  Simulated Annealing is designed to search for the global optimum of the objective function by exploring multiple directions from an initial solution. The Genetic Algorithm applies biological principles such as crossover and mutation to search the parameter space for the global optimum.

### 4.4 Avoiding and Improving Local Optima

A common issue in non-linear optimization is getting stuck in a **local optimum**, which represents the minimum value of the objective function within a specific parameter range but is not the global minimum across the entire parameter space. The goal of optimization is to find the **global optimum**, which is the minimum value of the objective function over the entire parameter space. Achieving convexity is crucial to ensuring that the global optimum is reached. The following methods can help avoid and improve the handling of local optima:

<img width="440" alt="Screenshot 2024-10-16 at 03 28 05" src="https://github.com/user-attachments/assets/60c78f80-1791-485d-9026-411284d21c40">


- **Improving Initial Values**:  
  Setting appropriate initial values can guide the optimization algorithm toward the global optimum. The rationale for setting initial values will be developed in this study.

- **Multi-start Technique**:  
  This technique involves running the optimization multiple times with different initial values to reduce the likelihood of being trapped in a local optimum and to find the global optimum.

- **Smoothing the Objective Function**:  
  Applying smoothing techniques to ensure the objective function transitions smoothly can improve the optimization path and prevent the algorithm from being trapped in local optima.

### 4.5 Optimization using Convex Duality

#### 4.5.1 Definition of the Dual Problem

By applying convex duality to the objective function and constraints presented in section 3.4.5.2, the primal problem can be transformed into a dual problem. This transformation can reduce the complexity of optimization or provide a new interpretation, allowing for the development of more efficient algorithms. The dual optimization problem for the SVI model is formulated as:

$$
\max_{\lambda} \inf_{a, b, \rho, m, \sigma} L(a, b, \rho, m, \sigma, \lambda)
$$

This equation redefines the Lagrangian function, where the primal variables \(a\), \(b\), \( \rho \), \( m \), \( \sigma \) are minimized, and the Lagrange multiplier \( \lambda \) is maximized. Compared to the primal problem, the dual problem has relaxed constraints, reducing the parameter space, making the optimization more efficient, and facilitating easier access to the global optimum. Furthermore, instead of applying penalties for violating constraints in the primal problem, the dual problem directly handles constraints through Lagrange multipliers, simplifying the optimization process.

#### 4.5.2 Strong Duality and the Dual Gap

**Strong duality** occurs when the convexity condition is satisfied, meaning the optimal solutions of the primal and dual problems coincide. In other words, the optimal value of the objective function for both the primal and dual problems will be equal. When strong duality holds, the **dual gap** is zero. The dual gap is the difference between the optimal objective function values of the primal and dual problems. By verifying the duality gap in the redefined objective function in section 3.4.6.1, the optimization can be confirmed as successful when the primal and dual solutions match.


## V. Result


