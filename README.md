# ReturnGuard-POC
# ReturnGuard: Predicting E-Commerce Returns to Protect Retail Margins

ReturnGuard is a machine learning project that predicts the likelihood that an e-commerce purchase will be returned **using only information available at checkout**.

Online returns create significant costs for retailers through reverse logistics, handling, restocking, and lost product value. Rather than treating every purchase equally, ReturnGuard identifies high-risk purchases so retailers can apply targeted interventions while minimizing unnecessary customer friction.

---

## Project Overview

Using the **ASOS GraphReturns** dataset, containing approximately **2.8 million purchases** from a major online fashion retailer, we build classification models that estimate return probability at the purchase level.

The project focuses on three main goals:

1. Predict whether a purchase is likely to be returned.
2. Identify customer and product characteristics associated with return risk.
3. Translate model predictions into an actionable **Return Risk Score** for business decision-making.

---

## Dataset

### Main Dataset

**ASOS GraphReturns**

- Dataset: [https://osf.io/c793h/](https://osf.io/c793h/)
- Dataset paper: [https://arxiv.org/abs/2302.14096](https://arxiv.org/abs/2302.14096)
- Scale: approximately **2.8 million purchase records**
- Domain: online fashion retail
- Entities include:
  - Customers
  - Products and product variants
  - Purchases
  - Return outcomes

The graph structure of the dataset allows customer, product, and transaction information to be linked across purchases.

---

## Key Modeling Challenge: Data Leakage

During preliminary analysis, we found that some features included in the dataset contain information that would not realistically be available at the time of purchase.

Using these variables directly would introduce **data leakage** and artificially inflate model performance.

To create a realistic prediction setting, ReturnGuard only uses information that could have been known **before or at checkout**.

For example, instead of using a customer's final return statistics, we reconstruct historical features using only their **earlier purchases**.

Example:

```text
Purchase 1 → contributes to history for Purchase 2
Purchase 2 → contributes to history for Purchase 3
Purchase 3 → cannot use information from future purchases
```

This creates a more realistic simulation of how a production return-risk model would operate.

---

## Feature Engineering

Candidate features include information from several groups.

### Customer History

Examples include:

- Number of previous purchases
- Number of previous returns
- Historical return rate
- Customer purchase frequency
- Previous purchasing behaviour

Historical variables are calculated using only transactions occurring before the current purchase.

### Product Information

Potential product-level features include:

- Product category
- Product variant
- Product return history
- Popularity
- Historical return behaviour

### Transaction Information

Where available at checkout, transaction-level variables may also be incorporated into the model.

---

## Modeling Approach

We begin with interpretable baseline models and then compare them with more flexible machine learning approaches.

### Baseline

**Logistic Regression**

Logistic regression provides:

- A strong interpretable benchmark
- Estimated return probabilities
- Insight into the direction and magnitude of important predictors

### Models to Compare

The next stage will evaluate tree-based approaches such as:

- Decision Trees
- Random Forest
- Gradient Boosting
- XGBoost or similar boosting models

Models will be compared not only based on predictive performance, but also on their usefulness for business decision-making.

---

## Preliminary Results

Even a simple logistic regression using leakage-free historical features produces strong separation between low-risk and high-risk purchases.

| Risk Group | Observed Return Rate |
|---|---:|
| Safest 10% of purchases | **24%** |
| Riskiest 10% of purchases | **78%** |

This suggests that meaningful return-risk segmentation is possible using information available before a return occurs.

Rather than simply maximizing classification accuracy, the goal is to determine whether these predictions can support economically useful interventions.

---

## Return Risk Score

The final model will convert predicted probabilities into a **Return Risk Score**.

Conceptually:

```text
Customer + Product + Purchase Information
                    ↓
              ML Model
                    ↓
        Predicted Return Probability
                    ↓
            Return Risk Score
                    ↓
      Business Intervention Decision
```

Higher-risk purchases can then receive targeted interventions while low-risk customers experience the normal checkout process.

---

## Cost-Sensitive Decision Threshold

A standard probability threshold such as `0.50` may not be appropriate for return prediction.

False positives and false negatives have different business consequences.

For example:

| Model Decision | Actual Outcome | Business Impact |
|---|---|---|
| Flag | Return | Potential opportunity to prevent a costly return |
| Flag | No Return | Unnecessary customer friction |
| No Flag | Return | Return cost remains |
| No Flag | No Return | Normal transaction |

We therefore plan to construct a **cost matrix** that captures the economic impact of each outcome.

The optimal threshold can then be selected based on expected business value rather than classification accuracy alone.

---

## Potential Retail Applications

ReturnGuard is designed as a decision-support system rather than a mechanism for blocking customer purchases.

High-risk predictions could trigger interventions such as:

### Sizing Guidance

Provide stronger size recommendations when a purchase has elevated return risk.

### Product-Page Improvements

Identify products associated with unusually high return rates and investigate:

- Incorrect sizing information
- Misleading descriptions
- Product-quality issues
- Missing product information

### Promotion Design

Avoid promotion strategies that encourage purchasing behaviour associated with excessive returns.

### Customer Experience

Apply interventions selectively instead of adding friction to every customer's checkout experience.

---

## Project Workflow

```text
ASOS GraphReturns
        ↓
Data Cleaning
        ↓
Leakage Detection
        ↓
Temporal Feature Engineering
        ↓
Train / Validation / Test Split
        ↓
Logistic Regression Baseline
        ↓
Tree-Based Model Comparison
        ↓
Model Evaluation
        ↓
Cost Matrix
        ↓
Threshold Optimization
        ↓
Return Risk Score
        ↓
Business Recommendations
```

---

## Evaluation

Because return prediction is a classification and ranking problem, evaluation will consider multiple metrics.

Possible metrics include:

- ROC-AUC
- Precision
- Recall
- F1 Score
- Precision-Recall AUC
- Calibration
- Lift by risk decile
- Return rate by predicted-risk group

For the business application, particular attention will be given to **lift and cost-sensitive performance**, since the objective is to identify a relatively small group of purchases where intervention generates the greatest value.

---

## Current Progress

- [x] Dataset identified
- [x] Initial exploratory analysis
- [x] Data leakage identified
- [x] Leakage-free historical return features constructed
- [x] Logistic regression baseline
- [x] Initial risk-decile analysis
- [ ] Tree-based model comparison
- [ ] Model tuning
- [ ] Model calibration
- [ ] Cost matrix design
- [ ] Optimal intervention threshold
- [ ] Return Risk Score
- [ ] Business recommendations
- [ ] Final visualization / dashboard

---

## Expected Outcome

The final output of ReturnGuard will be an interpretable return-risk framework that helps retailers answer:

> **Which purchases are most likely to be returned, and when is it economically worthwhile to intervene?**

By identifying high-risk purchases before a return occurs, retailers can focus interventions where they are most valuable, reduce avoidable return costs, protect margins, and limit unnecessary customer friction.

---

## References

**ASOS GraphReturns Dataset**  
[https://osf.io/c793h/](https://osf.io/c793h/)

**GraphReturns Paper**  
[https://arxiv.org/abs/2302.14096](https://arxiv.org/abs/2302.14096/)
