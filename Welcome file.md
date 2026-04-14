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

Vasicek used the Gaussian copula to derive a formula for the **Worst-Case Default Rate ($WCDR(T,\alpha)$)** of a portfolio that will not be exceeded with a certain confidence level ($1 - \alpha$) during time period $T$.

The formula for the **Credit VaR (at confidence level $\alpha$)** is:

$$WCDR(T,\alpha) = N \left[ \frac{N^{-1}(PD) + \sqrt{\rho} \cdot N^{-1}(\alpha)}{\sqrt{1 - \rho}} \right]$$

**Where:**

-   $N$: The cumulative standard normal distribution function.
    
-   $N^{-1}$: The inverse standard normal distribution (the "Z-score").
    
-   $PD$: The individual probability of default for the loans (assumed to be the same for all).
    
-   $\rho$: The **Asset Correlation** (how much the "Economy" factor drives individual defaults).
    
-   $\alpha$: The confidence level (e.g., 0.999 for bank capital).

### **3. How to Interpret the Variables**

-   **If $\rho = 0$ (No Correlation):** The formula simplifies to the $PD$. In a large enough portfolio, the default rate will just be the average $PD$.
    
-   **If $\rho$ is High:** The "Economy" factor dominates. If the economy crashes, everyone defaults at once, making the Credit VaR much higher.
    
-   **The "Tail":** Because this uses the Gaussian Copula, it assumes the correlation structure is "Normal." As we discussed earlier, this is a major criticism because real-world defaults often cluster more aggressively in the tails than a Normal distribution predicts.

### **4. Credit VaR**

To turn this rate into an actual **Credit VaR dollar amount**, you apply it to the entire portfolio:

$$\text{Credit VaR} = \sum (\text{EAD}_i \times \text{LGD}_i \times \text{WCDR}_i)$$

-   **EAD (Exposure at Default):** The total dollar amount owed.
    
-   **LGD (Loss Given Default):** The percentage of that money you _won't_ get back (e.g., if you recover 40%, LGD is 60%).

### **5. Determining Correlation ($\rho$)**

A critical input is the **Asset Correlation ($\rho$)**. Since you can't always see the "value" of a private loan daily, the model suggests using proxies:

-   **ROA/ROE Correlation:** Use the correlation of a company’s Return on Assets or Equity.
    
-   **Public Proxies:** If the borrower is a private company, use the average correlation of similar publicly traded peers.


### **6. Summary of Assumptions**

To make the math work, Vasicek’s model makes three big "Large Portfolio" assumptions:

| Feature | Description |
| :--- | :--- |
| **Large Portfolio Assumption** | Assumes the portfolio is "granular" (many small loans), so individual (idiosyncratic) risks cancel out, leaving only systematic risk. |
| **The "Single Factor"** | Relates everything to one factor: the health of the overall economy. |
| **The "Tail" Weakness** | Because it uses a **Gaussian (Normal)** copula, it often **underestimates** how many companies fail together during a true market crash (lack of "tail correlation"). |

# Credit Risk Plust Model

### **1. Key Concept: The "Insurance" Approach**

CreditRisk+ assumes that defaults are independent events that happen "randomly" over time. Because defaults are rare, it uses the **Poisson distribution** to model the _number_ of defaults.

-   **Input:** The expected number of defaults ($\lambda$).
    
-   **Output:** The probability of having exactly $n$ defaults.

### **2. Core Components**

The model splits the problem into two parts to find the total loss distribution:

1.  **Default Frequency:** How many defaults occur? (Modeled via Poisson).
    
2.  **Loss Severity:** How much money is lost when a default occurs? (Loans are often grouped into "bands" of similar exposure size).
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTE3NTMyMzQ3OTYsLTE1NDQ3Mjk5MiwtNT
IzMzkyMjM2LC0zNzM5MzkyNzUsMTA3NDk3NTY4NiwxMzA5NzE4
NDE0LC0xOTkyNDYwOTAsLTIwODg3NDY2MTIsLTMzMjQ1NTM2M1
19
-->