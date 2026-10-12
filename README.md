# Employee Attrition: Flight-Risk Model in R

Logistic regression on HR data to estimate each employee's probability of leaving and group employees into three risk tiers.

## Objective
Voluntary turnover is costly (lost productivity, lost know-how, hiring costs). This project asks which factors are associated with attrition and builds an interpretable model that gives each employee a flight-risk score, so HR can prioritise retention conversations.

## Data
- `Employee.csv`: 1,470 employees, 35 variables, 16.1% attrition (237 leavers).
- Source: [IBM HR Analytics Employee Attrition & Performance](https://www.kaggle.com/datasets/pavansubhasht/ibm-hr-analytics-attrition-dataset) (public dataset on Kaggle).
- No missing values. Constant columns (`EmployeeCount`, `Over18`, `StandardHours`) and the ID column (`EmployeeNumber`) are dropped.

## Method
1. **Cleaning:** drop constant/ID columns, encode `Attrition` and `OverTime` as 0/1.
2. **Features:**
   - `IncomeRatio`: monthly income divided by the mean income of the employee's job role (1.0 = at role average).
   - `AvgYearsPerCompany`: total working years / (number of previous companies + 1).
3. **Model:** binomial GLM (`glm`, logit link) with Age, OverTime, IncomeRatio, AvgYearsPerCompany, JobSatisfaction, WorkLifeBalance and YearsSinceLastPromotion.
4. **Risk tiers:** predicted probability → Low (< 30%), Medium (30–60%), High (≥ 60%).

## Results
| Variable | Odds ratio | p-value |
|---|---|---|
| OverTime (yes vs no) | 4.50 | < 0.001 |
| Age (per year) | 0.95 | < 0.001 |
| AvgYearsPerCompany (per year) | 0.83 | < 0.001 |
| JobSatisfaction (per point) | 0.74 | < 0.001 |
| WorkLifeBalance (per point) | 0.74 | 0.004 |
| YearsSinceLastPromotion (per year) | 1.08 | 0.009 |
| IncomeRatio | 0.97 | 0.89 (not significant) |

- **Overtime is the strongest signal:** 30.5% of employees working overtime left, against 10.4% of those who did not.
- **Pay relative to role average was not significant** once the other variables were included.
- **Discrimination:** AUC 0.78, computed on the same data the model was fitted on (in-sample).
- **Risk tiers (full sample):** 1,252 Low, 202 Medium, 16 High.

| Tier | Stayed | Left |
|---|---|---|
| Low | 1,119 | 133 |
| Medium | 110 | 92 |
| High | 4 | 12 |

The High tier is small but precise (12 of 16 left); most leavers sit in the Low tier, so the 30%/60% cut-offs are conservative and could be tuned.

## Limitations
- Associations, not causes: the model shows what is linked to leaving, not what makes people leave.
- Scores and AUC are in-sample; there is no train/test split yet, so performance on new data is likely lower.
- Public dataset (simulated HR data), so results are illustrative.

## How to run
```r
install.packages("tidyverse")
# open "analysis people attrition.Rmd" in RStudio and knit
```
Requires R 4.x and `Employee.csv` in the same folder.
