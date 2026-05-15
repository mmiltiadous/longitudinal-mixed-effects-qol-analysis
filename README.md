# Longitudinal Analysis of Adjuvant Chemotherapy Effects on Quality of Life
This project analyses a simulated longitudinal dataset from a clinical trial investigating whether adjuvant chemotherapy improves quality of life (QoL) in rectal cancer patients after neoadjuvant chemoradiotherapy and curative resection.
226 patients with histologically proven stage II or III rectal cancer were assigned to either adjuvant chemotherapy or observation. QoL was assessed via the EORTC-C30 questionnaire at 1, 3, 6, and 12 months post-surgery. All analyses were conducted in R.

## Research Questions

### Continuous outcome (QoL Score):
 - Does adjuvant chemotherapy affect mean QoL progression over time?
 - Is there a significant difference in QoL at 6 months for patients who completed chemotherapy?
 - Is there a difference between treatment groups at 12 months post-surgery?

### Binary outcome (Presence of Complaints):

- Does the probability of having more than 2 complaints change over time, and does this depend on treatment assignment?


## Dataset
File: `QOL-3.csv`
Description of Variables:
- `ID`: Patient identifier 
- `Time`: Visit number (1–4) 
- `Months`: Month of visit post-surgery (1, 3, 6, 12) 
- `Score`: EORTC-C30 QoL score (continuous) 
- `Treat`: Treatment group (0 = observation, 1 = adjuvant chemotherapy) 
- `Treatn`: Treatment label ("observed" / "chemotherapy") 
- `Age`: Age at study inclusion (baseline) 
- `Complaints`: Presence of >2 complaints (0 = no, 1 = yes) 

**Note:** `Months` is used as a continuous time variable rather than the visit index `Time`, as the intervals between visits are unequal (1, 3, 6, 12 months).

## Methods
 
### Missing Data
 
179 of 226 patients have complete measurements across all 4 time points. Missingness arises from loss to follow-up, relocation, and side effects. Results are assumed valid under **Missing At Random (MAR)**.
 
### Part 1: QoL Score (Continuous Outcome)
 
**1. Multivariate Linear Regression**
- Unstructured covariance matrix, REML estimation
- Model selection via AIC across mean and covariance structures
- Final model: `Score ~ Months * Treat`
**2. Linear Mixed-Effects Model (LMM)**
- Random intercept per patient, unstructured residual correlation
- Random slope comparison via likelihood ratio test
- Final model: `Score ~ Months * Treat + (1 | ID)`

### Part 2 — Complaints (Binary Outcome)
 
**3. Generalized Estimating Equations (GEE)**
- Marginal logistic regression, unstructured working correlation matrix
- Robust sandwich variance estimator
- Model: `Complaints ~ Months * Treat`
**4. Mixed-Effects Logistic Regression (GLMM)**
- Subject-specific logistic regression with random intercept
- Adaptive Gauss-Hermite Quadrature (Q=16) vs. Laplace approximation
- Final model: `Complaints ~ Months * Treat + (1 | ID)`

## Key Findings
 
- **Time (Months)** is the only statistically significant predictor of QoL score, treatment group alone does not significantly affect mean QoL progression.
- QoL score improves significantly between month 1 and month 6 for chemotherapy patients (p < 0.0001), but **no significant difference** between groups is found at 12 months.
- For complaints, the **interaction of treatment × time** is significant (p = 0.038), suggesting the trajectory of complaints differs between groups over time.
- The **multivariate model** (AIC = 6171.6) outperforms the LMM (AIC = 6206.7) on this dataset.
- The **mixed-effects logistic regression** is preferred over GEE for the binary outcome, as it is valid under MAR and supports subject-specific inference.


 
## Repository Structure
 
- `data/` — raw data file (`QOL-3.csv`)
- `analysis/` — R Markdown source (`Mixed_Model_Group_Assignment.Rmd`)
- `report/` — compiled PDF report (`Mixed_Model_Group_Assignment.pdf`)
- `docs/` — assignment instructions (`QualityLife.pdf`)

## Requirements
 
- **R** (≥ 4.0)
- Packages: `nlme`, `lme4`, `geepack`, `JMbayes2` or `GLMMadaptive`, `aod`, `multcomp`, `ggplot2`, `corrplot`
Install all dependencies:
```r
install.packages(c("nlme", "lme4", "geepack", "GLMMadaptive",
                   "aod", "multcomp", "ggplot2", "corrplot"))
```
