# Credit Risk Calculation under Basel III Framework

## 1. Introduction
As of January 2024, Basel III regulations will enforce updated risk management practices, including the calculation of credit risk **Risk-Weighted Assets (RWA)** for banking portfolios. This report evaluates the credit risk of a mortgage loan portfolio using two key approaches:
1. **Standardized Approach**: Based on fixed risk weights.
2. **Internal Ratings-Based (IRB) Approach**: Employing internal models to calculate Probability of Default (PD), Loss Given Default (LGD), and Exposure at Default (EAD).

The goal is to compute RWA under both approaches, compare the capital requirements, and identify the key risk factors influencing the results. The data used is derived from the provided mortgage loan dataset, where all loans are categorized as defaulted (bad) or non-defaulted.

## 2. Data Overview and Preparation

### 2.1 Dataset Description
The dataset includes 5,960 loan records with the following features:
- **BAD**: Indicator of loan default (1 = defaulted, 0 = non-defaulted)
- **MORTDUE**: Mortgage amount due
- **VALUE**: Property value
- **JOB**: Borrower's occupation
- **YOJ**: Years at current job
- **DEROG**: Number of derogatory reports
- **DELINQ**: Number of delinquent credit lines
- **CLAGE**: Age of oldest credit line (in months)
- **NINQ**: Number of recent credit inquiries
- **CLNO**: Number of credit lines
- **DEBTINC**: Debt-to-income ratio

### 2.2 Data Cleaning and Imputation
To handle missing data:
- **Property Values**: Missing values in `VALUE` were imputed using the loan-to-value (LTV) ratio of 0.45, based on mortgage amounts.
- **Numeric Variables**: Columns with missing numerical data (e.g., `YOJ`, `DEBTINC`, `DEROG`) were filled using their median values.
- **Job Data**: Missing job information was imputed with "Unknown" to ensure consistency.

The cleaned dataset was then used for analysis, ensuring that all loans were represented with complete data.


## 3. Exploratory Data Analysis (EDA)

### 3.1 Distribution of Defaults
![output_13_1](https://github.com/user-attachments/assets/3f4a3860-79d5-4302-b7f3-9e7c7444b00f)

Approximately **20%** of the loans in the dataset were classified as defaulted (`BAD = 1`), reflecting the typical distribution of credit risk in mortgage portfolios. This segmentation provided a basis for analyzing the underlying risk factors.

### 3.2 Correlation Analysis
Key correlations in the dataset include:
![output_21_0](https://github.com/user-attachments/assets/1d3390de-da83-4546-8d62-2e57dc627c04)

- **MORTDUE** and **VALUE**: Strong positive correlation (0.80), indicating that higher property values are associated with larger mortgage amounts.
- **DEROG**, **DELINQ**, and **CLAGE**: Moderate correlations with default status, suggesting that these variables significantly influence a borrower’s likelihood of default.

### 3.3 Occupation and Default Rates
Certain occupational categories were found to have higher default rates:
![output_16_0](https://github.com/user-attachments/assets/08e01449-89f5-4401-a48a-6f92aa2d4dfc)

- **Sales** and **Self-employed** borrowers exhibited default rates exceeding **30%**, making them the riskiest categories.
- **Office workers** and **Managers** had the lowest default rates, averaging around **13%-16%**.

## 4. Methodology: Credit Risk Calculation

### 4.1 Standardized Approach
The standardized approach applies fixed risk weights based on the Loan-to-Value (LTV) ratio of each loan. The calculation follows these risk weight thresholds:
- **LTV ≤ 0.60**: 35% risk weight
- **0.60 < LTV ≤ 0.80**: 50% risk weight
- **0.80 < LTV ≤ 0.90**: 75% risk weight
- **LTV > 0.90**: 100% risk weight

For non-defaulted loans, the RWA is calculated as:
RWA_Standardized = MORTDUE × Risk Weight

This approach uses a "one-size-fits-all" method, assigning risk based solely on LTV, without considering borrower-specific factors such as job stability or debt-to-income ratio.

### 4.2 Internal Ratings-Based (IRB) Approach
The IRB approach allows banks to calculate RWA using their own models to estimate Probability of Default (PD), Loss Given Default (LGD), and Exposure at Default (EAD). This method requires a deeper analysis of borrower data to model the risk.

The IRB RWA is calculated as:

RWA_IRB = PD × LGD × EAD × 12.5

Where:
- **PD**: Probability of Default, estimated using a **Logistic Regression** model based on borrower characteristics such as **YOJ**, **DEROG**, **DELINQ**,**NINQ**, **CLNO**, **DEBTINC**, **encoded jobcolumns** and **CLAGE**.
- **LGD**: Assumed to be 45%, as per Basel guidelines.
- **EAD**: Exposure at Default, represented by the mortgage amount (MORTDUE).

### 4.3 Basel Advanced IRB Approach
This advanced approach incorporates the maturity of the loans and uses more complex risk formulas to adjust capital requirements. The RWA for non-defaulted loans under this approach is adjusted for maturity, using the following formula:

RWA_Advanced = LGD × K × EAD × 12.5

Where **K** is a function of PD, and a correlation factor based on the maturity of the loan.

## 5. Results

### 5.1 RWA Calculation Results
- **Standardized Approach**:
  - The total RWA for non-defaulted loans under the standardized approach was calculated to be **$786,321,459.46**.
  
- **IRB Approach**:
  - The IRB method resulted in a total RWA of **$627,963,132.11**, significantly lower than the standardized approach due to its more individualized risk assessment.

- **Basel Advanced Approach**:
  - This approach produced the highest capital requirement, with a total RWA of **$1,924,706,295.03**. The higher capital requirement reflects more conservative risk assumptions and adjustments for loan maturity.

### 5.2 Capital Requirements
![output_61_0](https://github.com/user-attachments/assets/3ce6b802-5d83-4635-84e2-58eeab6c8704)

Capital requirements are calculated by applying an **8% capital charge** to the RWAs:
- **Standardized Capital Requirement**: $786,321,459.46 × 0.08 = **$62,905,716.76**
- **IRB Capital Requirement**: $627,963,132.11 × 0.08 = **$50,237,050.57**
- **Basel Advanced Capital Requirement**: $1,924,706,295.03 × 0.08 = **$153,976,503.60**

### 5.3 Comparison of Results
![output_72_0](https://github.com/user-attachments/assets/ebc40e51-e3b5-4c48-96b5-1a501e032c0a)

- The **IRB approach** yielded the lowest capital requirement, demonstrating that when borrower-specific factors are considered, the overall portfolio is less risky than what the standardized approach suggests.
- The **Basel Advanced Approach** resulted in the highest capital requirement, reflecting a more conservative risk profile that considers the loan maturity and the correlation between risk factors.

## 6. Discussion and Recommendations

### 6.1 Interpretation of Results
- **Why IRB is Preferred**: The IRB approach reduces capital requirements by considering borrower-specific risk factors, allowing for a more accurate risk assessment of the portfolio. This leads to more efficient capital allocation compared to the standardized method.
  
- **How to Decrease RWA**: Reducing the number of high-risk loans (such as those with high LTV or borrowers in risky occupations) and improving loan quality will help reduce RWA. Encouraging borrowers to maintain stable employment and reduce their debt-to-income ratios can also lower PD.

- **Most and Least Risky Segments**:
  - The riskiest segment of the portfolio includes **Sales** and **Self-employed** borrowers, who exhibit high default rates and are likely driving up the RWA.
  - The least risky loans are those associated with **Office workers** and **Managers**, who have more stable job histories and lower default rates.

### 6.2 Recommendations for the Bank
- **Adopt the IRB Approach**: Given that the IRB approach results in lower capital requirements, it is recommended that the bank adopt this model, provided it has the infrastructure to maintain and validate internal risk models.
  
- **Focus on High-Risk Borrowers**: The bank should implement stricter underwriting criteria for high-risk occupational categories like Sales and Self-employed individuals. Consider offering more tailored financial products to mitigate default risks for these borrowers.
  
- **Enhance Data Quality and Risk Models**: To fully realize the benefits of the IRB approach, the bank must ensure high-quality borrower data and continuously update its risk models to reflect changes in economic conditions and borrower behavior.

## 7. Conclusion
This report demonstrates the differences in credit risk evaluation between the standardized and IRB approaches under Basel III. The IRB method proves to be more efficient, resulting in lower capital requirements while maintaining a more granular risk assessment. By adopting the IRB approach, the bank can allocate capital more effectively while ensuring regulatory compliance and managing credit risk more accurately. The Basel Advanced Approach, while more conservative, can be leveraged for more risk-averse portfolios.

