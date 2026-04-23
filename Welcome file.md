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

# Credit Risk Plus Model

### **1. Key Concept: The "Insurance" Approach**

CreditRisk+ assumes that defaults are independent events that happen "randomly" over time. Because defaults are rare, it uses the **Poisson distribution** to model the _number_ of defaults.

-   **Input:** The expected number of defaults ($\lambda$).
    
-   **Output:** The probability of having exactly $n$ defaults.

### **2. Core Components**

The model splits the problem into two parts to find the total loss distribution:

1.  **Default Frequency:** How many defaults occur? (Modeled via Poisson).
    
2.  **Loss Severity:** How much money is lost when a default occurs? (Loans are often grouped into "bands" of similar exposure size).

### **3. Modeling "Background" Risk (The Gamma Distribution)**

The biggest criticism of a simple Poisson model is that it assumes the default rate is constant. In reality, the economy changes. CreditRisk+ solves this by:

-   Assuming the **mean default rate ($\lambda$)** is itself a random variable.
    
-   It uses a **Gamma distribution** to model the volatility of the default rate.
    
-   Combining Poisson and Gamma results in a **Negative Binomial distribution**, which has "fatter tails" than a standard Poisson distribution, better capturing the risk of a bad economy.

$$Pr(m \space defaults)=p^m(1-p)^\alpha\frac{\Gamma(m+\alpha)}{\Gamma(m+1)\Gamma(\alpha)}$$

**Where:**

-   $\alpha:\frac{\mu^2}{\sigma^2}$
    
-   $p=\frac{\sigma^2}{(\mu+\sigma)^2}$

### 4. The Role of Volatility ($\sigma$)

The model uses a volatility parameter ($\sigma$) to determine the shape of the default distribution.

-   **Low Volatility:** As $\sigma$ decreases, the model converges toward a **Poisson distribution**, implying that defaults are relatively predictable and independent.
    
-   **High Volatility:** As $\sigma$ increases, the distribution shifts toward a **Negative Binomial distribution**. This creates a "fat tail," meaning there is a much higher likelihood of seeing an extreme number of defaults.
    

### 5. Default Correlation and Skewness

The paragraph highlights that default rate uncertainty is inextricably linked to **default correlation**:

-   When uncertainty rises, defaults tend to cluster together (correlation increases).
    
-   This causes the loss probability distribution to exhibit **positive skew**. Instead of a symmetrical "bell curve," the distribution leans heavily toward the right, indicating a higher probability of catastrophic, large-scale losses.
    

### 6. Time Dependency and Monte Carlo Simulation

The text argues that real-world default rates are rarely independent from year to year.

-   **The Flaw:** Simple models assume each year is a "fresh start."
    
-   **The Solution:** Using **Monte Carlo simulations** allows analysts to build more "desirable" models that link current default rates to prior years or lagging economic indicators. This captures the cyclical nature of credit markets.

# Credit Metrics Model
The **CreditMetrics** model, developed by JPMorgan, represents a shift from simple "default-only" models to a more comprehensive **"mark-to-market"** approach. Unlike CreditRisk+, it accounts for the loss in value caused by credit **downgrades** as well as actual defaults.

### 1. The Core Mechanism: Rating Migrations

The model relies on a **rating transition matrix**. This matrix tracks the probability of a counterparty moving from one credit rating (e.g., A) to another (e.g., BBB or Default) over a one-year horizon.

-   **Data Sources:** These transitions are based on either internal bank data or agencies like S&P and Moody's.
    
-   **Credit Spreads:** To value a non-defaulted position at the end of the year, the model uses the **term structure of credit spreads** for the counterparty's new rating category.
    

### 2. Monte Carlo Simulation Process

Because a portfolio contains multiple counterparties with complex correlations, CreditMetrics uses **Monte Carlo simulations** to calculate the one-year **Credit Value at Risk (VaR)**. Each simulation trial follows these steps:

-   **Scenario Generation:** Determine the credit rating of every counterparty at the end of Year 1.
    
-   **Default Case:** If a counterparty defaults, the loss is:
    
    $$\text{Loss} = \text{EAD} \times (1 - RR)$$
    
-   **Non-Default Case:** If the counterparty is downgraded or upgraded, the "loss" (or gain) is the change in the present value of the remaining cash flows, re-valued using the new credit spreads.
    

### 3. The Valuation Formula

For simulation trials where the counterparty does **not** default in Year 1, the credit loss is calculated by re-valuing the instrument based on the new forward credit spreads. The formula for the loss in a specific simulation trial is:

$$\text{Credit Loss} = \text{Value at Year 0} - \left[ \sum_{i=1}^{n} \frac{\text{CF}_i}{(1 + f_i + s_i)^i} \right]$$

-   **$\text{CF}_i$:** Cash flow at time $i$.
    
-   **$f_i$:** Risk-free forward rate for period $i$.
    
-   **$s_i$:** Credit spread for the counterparty's **new** rating at Year 1.
    

### 4. Outcome: Credit VaR

By running thousands of these trials, the model produces a **probability distribution of total credit losses**.

-   An **upgrade** results in a negative loss (a profit), as the debt becomes more valuable.
    
-   A **downgrade** results in a positive loss.
    
-   The **Credit VaR** is then identified as the percentile of this distribution corresponding to the desired confidence level (e.g., the 99th percentile).
    
   # Correlation Model 
  This passage describes the **CreditMetrics** approach to modeling correlated credit risk. Essentially, it explains how we move from looking at one company’s risk to understanding how two or more companies might fail or be downgraded at the same time.

Here is a summary of the core concepts:

----------

### 1. The Core Assumption: Correlated Risk

The model assumes that credit rating changes are **not independent**. If one company in a specific sector struggles, another related company is likely to struggle as well. To quantify this "relatedness," the model uses **equity returns** (stock price movements) as a proxy for credit health.

### 2. The Gaussian Copula Mechanism

To simulate joint rating changes, the model uses a **Gaussian copula**. This involves:

-   **Standard Normal Distributions:** Mapping the probability of rating changes onto a bell curve.
    
-   **Correlated Sampling:** Randomly drawing values ($x_A$ and $x_B$) that share a specific correlation coefficient (in your example, **0.2**).
    
-   **Thresholds (Z-scores):** Mapping specific ranges on that curve to specific rating outcomes.
    

### 3. Mapping the Transition

The model uses "cutoff points" on the normal distribution to determine the new rating. Based on your data for an **A-rated company**:
| If the sampled value ($x_A$) is... | New Credit Rating | Transition Type |
| :--- | :--- | :--- |
| $x_A < -3.3416$ | **AAA** | Upgrade |
| $-3.3416 \leq x_A < -2.1201$ | **AA** | Upgrade |
| $x_A \geq -2.1201$ | **A or Lower** | No change / Downgrade |


(Other rating transitions)

### 4. Comparison of Models

The text notes a key distinction between the two major credit risk frameworks:

-   **CreditMetrics:** Focuses on the "Value-at-Risk" of a portfolio based on rating migrations (upgrades/downgrades).
    
-   **CreditRisk+:** An actuarial model focusing primarily on default rates.
    
-   **The Bottom Line:** If both models use the same assumptions but produce different results, it is usually because they differ on the **predicted timing** of when losses will occur.
    

----------

> **Key Takeaway:** By using stock correlations and normal distribution thresholds, the model can simulate thousands of scenarios to see how often multiple companies in a portfolio might suffer credit hits simultaneously.

# Credit Spread Risk


<!--stackedit_data:
eyJoaXN0b3J5IjpbMTkxNzg1MjcxNCwxOTA5NDQwMjM0LDE2MD
M0NzA2NTUsLTkyODA0NjY5LC0xMTA2NjAyOTQsLTE0MTkyNDMx
NDIsLTE3NTMyMzQ3OTYsLTE1NDQ3Mjk5MiwtNTIzMzkyMjM2LC
0zNzM5MzkyNzUsMTA3NDk3NTY4NiwxMzA5NzE4NDE0LC0xOTky
NDYwOTAsLTIwODg3NDY2MTIsLTMzMjQ1NTM2M119
-->