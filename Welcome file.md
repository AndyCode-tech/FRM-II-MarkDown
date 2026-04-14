# Ch26. Credit Value at Risk
## Defining Credit VaR
### Market VaR vs. Credit VaR
| Feature | Market Risk VaR | Credit Risk VaR |
| :--- | :--- | :--- |
| **Definition** | The maximum loss from price/rate fluctuations (interest rates, equities, FX) over a set period. | The maximum loss from credit events (defaults, credit rating downgrades, or spread changes). |
| **Time Horizon** | **Short-term** (typically 1 day to 10 days). Markets move fast and trade frequently. | **Long-term** (typically 1 year). Credit events occur over longer cycles and assets are less liquid. |
| **Primary Tools** | **Historical Simulation** (using past price data) or Parametric (Variance-Covariance) models. | **Advanced Modeling** (e.g., CreditMetrics, Monte Carlo) to account for the "fat-tailed" nature of defaults. |
| **Primary Use** | Daily risk management and monitoring of trading desks. | Determining **Economic Capital** and Regulatory Capital requirements for banks. |

## Factors for Calculating Credit VaR
   
## **1. Core Drivers of Credit VaR**

Unlike simple price movements, Credit VaR relies on two primary "hidden" factors:

-   **Credit Correlation:** This recognizes that defaults are **not** independent. In a recession, multiple companies tend to fail at once. As correlation increases during downturns, the risk to financial institutions spikes significantly.
    
-   **Economic Environment:** A strong economy acts as a "rising tide" that lowers default risk, while a poor economy creates a systematic increase in risk across the board.
    

## **2. Rating Transition Matrices**

Financial institutions use these tables to track the "migration" of a company’s creditworthiness.

-   **The Diagonal Rule:** Typically, the highest probability is that a company **stays in its current rating** (the diagonal line of the matrix).
    
-   **Upgrades vs. Downgrades:** The matrix shows the historical probability of moving to a higher or lower tier (e.g., an $AA$ rating moving to $A$).
    
-   **Default State:** The final column usually represents "Default"—once a company hits this state, it generally stays there (an "absorbing state").
    

----------

## **3. Time Horizon Adjustments**

The matrix allows you to calculate risk over different periods using basic powers and roots, assuming rating changes are independent across periods:

-   **Longer Horizons (Multiplication):** To find the probability over $n$ years, you raise the one-year probability to the $n^{th}$ power.
    
    -   _Example:_ If a $AAA$ rating has an $89.85\%$ chance of staying $AAA$ for 1 year, the 3-year probability is $0.8985^3 = 72.54\%$.
        
-   **Shorter Horizons (Roots):** To find the probability for a fraction of a year, you take the corresponding root.
    
    -   _Example:_ For a 3-month (1/4 year) period, you take the fourth root: $0.8985^{1/4} = 97.36\%$.
        

> **The Trend:** As time increases, the probability of staying in the same rating **decreases** and the probability of default **increases**.

----------

## **4. The "Ratings Momentum" Reality Check**

While the math assumes independence, the text highlights a real-world flaw: **Ratings Momentum**. In reality, if a company was just downgraded, it is statistically _more_ likely to be downgraded again soon, rather than the probability reset to a "blank slate."

## Vasicek's Model
### **1. The Core Intuition**

Vasicek’s model assumes that a company defaults if the value of its assets falls below a certain threshold. It links these defaults together using a **single common factor** (usually interpreted as "The Economy").

-   **Individual Risk:** Each company has its own probability of default ($PD$).
    
-   **Systemic Risk:** Every company is exposed to the same economic cycle.
    
-   **Idiosyncratic Risk:** Every company has its own unique "bad luck" that isn't related to the economy.

### **2. The Mathematical Formula**

Vasicek used the Gaussian copula to derive a formula for the **Default Rate ($DR$)** of a portfolio that will not be exceeded with a certain confidence level ($1 - \alpha$).

The formula for the **Credit VaR (at confidence level $\alpha$)** is:

$$DR(\alpha) = N \left[ \frac{N^{-1}(PD) + \sqrt{\rho} \cdot N^{-1}(\alpha)}{\sqrt{1 - \rho}} \right]$$

**Where:**

-   $N$: The cumulative standard normal distribution function.
    
-   $N^{-1}$: The inverse standard normal distribution (the "Z-score").
    
-   $PD$: The individual probability of default for the loans (assumed to be the same for all).
    
-   $\rho$: The **Asset Correlation** (how much the "Economy" factor drives individual defaults).
    
-   $\alpha$: The confidence level (e.g., 0.999 for bank capital).
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTM3MzkzOTI3NSwxMDc0OTc1Njg2LDEzMD
k3MTg0MTQsLTE5OTI0NjA5MCwtMjA4ODc0NjYxMiwtMzMyNDU1
MzYzXX0=
-->