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

Calculating Credit VaR is significantly more complex than Market VaR because it has to account for the "interconnectedness" of the economy and the specific lifecycle of a credit rating.

Here is a summary of the key factors and the use of transition matrices:

----------

## **1. Core Drivers of Credit VaR**

Unlike simple price movements, Credit VaR relies on two primary "hidden" factors:

-   **Credit Correlation:** This recognizes that defaults are **not** independent. In a recession, multiple companies tend to fail at once. As correlation increases during downturns, the risk to financial institutions spikes significantly.
    
-   **Economic Environment:** A strong economy acts as a "rising tide" that lowers default risk, while a poor economy creates a systematic increase in risk across the board.
   


----------

## **1. Core Drivers of Credit VaR**

Unlike simple price movements, Credit VaR relies on two primary "hidden" factors:

-   **Credit Correlation:** This recognizes that defaults are **not** independent. In a recession, multiple companies tend to fail at once. As correlation increases during downturns, the risk to financial institutions spikes significantly.
    
-   **Economic Environment:** A strong economy acts as a "rising tide" that lowers default risk, while a poor economy creates a systematic increase in risk across the board.
    

----------

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

Does the math for the time horizon adjustments make sense, or would you like to see another example of how the matrix "powers up"?

<!--stackedit_data:
eyJoaXN0b3J5IjpbLTMwNjUzOTIyNywtMTk5MjQ2MDkwLC0yMD
g4NzQ2NjEyLC0zMzI0NTUzNjNdfQ==
-->