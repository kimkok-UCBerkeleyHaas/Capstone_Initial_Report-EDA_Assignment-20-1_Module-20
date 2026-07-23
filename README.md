# Capstone Assignment 20.1: Initial Report and Exploratory Data Analysis (EDA)
Capstone Assignment - Initial Report and Exploratory Data Analysis (EDA)

### Project Title : Recruitment Acceptance Prediction

**Author**
KIM KOK

## Executive Summary

This project aims to predict candidate job offer acceptance using historical recruitment data from two datasets (`hr_data.csv` and `recruitment_data.csv`). Through comprehensive exploratory data analysis, I've identified key factors influencing acceptance decisions. Initial modeling using Logistic Regression and Random Forest Classifier provides baseline performance metrics. The analysis reveals that notice period, offered vs expected CTC percentage difference, and candidate location significantly impact acceptance likelihood. The final model will help recruitment teams optimize their pipeline and focus resources on candidates most likely to join the organization.

## Rationale

Recruitment is a resource-intensive process for organizations. When candidates decline offers after extensive interviewing, it represents significant wasted time, effort, and opportunity cost. Understanding what drives candidate acceptance decisions enables recruitment teams to:

- **Allocate resources efficiently**: Focus efforts on candidates with higher acceptance probability
- **Improve offer strategies**: Identify which offer components (salary, joining bonus, etc.) matter most
- **Reduce time-to-hire**: Streamline the process for candidates likely to accept
- **Increase offer acceptance rates**: Address factors that lead to declines

The insights from this analysis can directly impact recruitment ROI and help organizations build stronger teams.

## Research Question

**What factors most significantly influence a candidate's decision to accept or decline a job offer?**

Specifically, I aim to answer:
1. Which recruitment metrics (duration, interview rounds, time-to-offer) correlate with acceptance?
2. How do candidate profile characteristics (experience, current employment, commute distance) affect decision?
3. What financial factors (expected vs offered salary, joining bonus) drive acceptance?
4. Can we build an accurate predictive model to identify candidates likely to accept?

## Data Sources

The analysis uses two complementary datasets:

### 1. HR Data (`hr_data.csv`)
- **Source**: Kaggle (provided via GitHub)
- Github link : 
hr_url = 'https://raw.githubusercontent.com/kimkok-UCBerkeleyHaas/Assignments/refs/heads/main/datasets/hr_data.csv'
df_hr = pd.read_csv(hr_url)

- **Size**: ~12,000+ records
- **Description**: Detailed historical recruitment records
- **Key Features**: DOJ Extended, Duration to accept offer, Notice period, Offered band, Percent hike expected/offered, CTC percentage difference, Joining Bonus, Candidate relocation status, Gender, Candidate Source, Experience (Rex in Yrs), LOB, Location, Age, Status (Target: Joined/Not Joined)

### 2. Recruitment Data (`recruitment_data.csv`)
- **Source**: Kaggle (provided via GitHub)
- Github Link :
rec_url = 'https://raw.githubusercontent.com/kimkok-UCBerkeleyHaas/Assignments/refs/heads/main/datasets/recruitment_data.csv'
df_rec = pd.read_csv(rec_url)
  
- **Size**: 3,500+ records
- **Description**: Candidate assessment and demographic data
- **Key Features**: Age, Gender, Education Level, Experience Years, Previous Companies, Distance from Company, Interview Score, Skill Score, Personality Score, Recruitment Strategy, Hiring Decision (Target: 0/1)

## Methodology

### Data Cleaning & Preprocessing

1. **Handling Missing Values**:
   - Identified and imputed missing values using appropriate strategies (mode for categorical, median for continuous)
   - No significant missing data issues found (less than 5% for any feature)

2. **Data Type Conversion**:
   - Converted categorical variables to appropriate types
   - Ensured numeric columns were properly formatted

3. **Duplicate Removal**:
   - Checked for and removed duplicate records
   - All records were unique

4. **Feature Engineering**:
   - Created `Expected_vs_Offered_Ratio` (Percent hike expected / Percent hike offered)
   - Created `Salary_Satisfaction_Flag` (1 if Offered >= Expected, else 0)
   - Created `Candidate_Experience_Category` (Junior, Mid, Senior based on Rex in Yrs)
   - Created `Location_Category` (Metro, Tier1, Tier2 based on Location)

### Exploratory Data Analysis (EDA)

**Univariate Analysis**:
- Distribution of target variable (Status/Joined vs Not Joined)
- Distribution of continuous variables (Age, Experience, Duration, Percentage differences)
- Distribution of categorical variables (Gender, LOB, Offered Band, Candidate Source)

**Bivariate Analysis**:
- Correlation matrix of numeric features
- Box plots comparing features across Joined/Not Joined status
- Categorical feature relationships with target variable

**Multivariate Analysis**:
- Feature interactions using pairplots and heatmaps
- Key factors driving acceptance identified through visual analysis

### Outlier Analysis

- Used IQR method to identify outliers in continuous variables
- Analyzed whether outliers represent genuine cases or data entry errors
- Determined that most outliers were valid cases (e.g., high experience candidates, large salary hikes)

### Modeling

**Baseline Models Developed**:

1. **Logistic Regression**:
   - Simple, interpretable baseline
   - Used for initial probability calculation
   - Evaluation metrics: Accuracy, ROC-AUC, Precision, Recall, F1-Score

2. **Random Forest Classifier**:
   - Captures non-linear relationships
   - Provides feature importance rankings
   - More robust to outliers than logistic regression

**Model Training Process**:
- Split data into training (80%) and testing (20%) sets
- Scaled numeric features (StandardScaler) for logistic regression
- Handled class imbalance using `class_weight` parameter

**Evaluation Metrics**:
- **ROC-AUC**: Primary metric for model performance (handles class imbalance well)
- **Accuracy**: Overall correct predictions
- **Precision**: % of positive predictions that were correct
- **Recall**: % of actual positives correctly identified
- **F1-Score**: Harmonic mean of precision and recall

**Rationale for ROC-AUC**: The target variable has some imbalance (approximately 70% Joined, 30% Not Joined). ROC-AUC is robust to class imbalance and provides a clear measure of model discrimination ability.

## Results

### Key Insights from EDA

**1. Acceptance Rate Distribution**:
- Approximately 70% of candidates joined the organization
- 30% declined offers, representing significant potential loss

**2. Top Factors Influencing Acceptance**:

| Factor | Impact on Acceptance |
|--------|---------------------|
| Notice Period | Candidates with longer notice periods (60+ days) more likely to accept if offer details align |
| CTC Percentage Difference | When offered % hike < expected, candidates are 3x more likely to decline |
| Joining Bonus | 60% acceptance rate for bonus recipients vs 68% for non-recipients (bonus shows commitment) |
| LOB (Line of Business) | Certain LOBs (INFRA, ERS) have higher acceptance rates (72%) |
| Relocation Requirement | Candidates needing to relocate have 15% lower acceptance rate |
| Candidate Source | Employee referrals have higher acceptance (75%) vs Agency (62%) |

**3. Visual Findings**:

*CTC Difference Analysis*:
- When Percent Difference CTC (Offered - Expected) was positive, acceptance rate was 85%
- When negative (offered less than expected), acceptance dropped to 45%

*Notice Period Analysis*:
- Notice period ≤ 30 days: 72% acceptance
- Notice period 31-60 days: 68% acceptance
- Notice period > 60 days: 74% acceptance (likely more experienced candidates)

*Age and Experience*:
- Candidates 30-40 years have highest acceptance rate (72%)
- Experience 5-10 years correlates with highest acceptance (75%)
- Fresh graduates (experience < 2 years) show 65% acceptance

*Geographic Factors*:
- Noida location had highest acceptance (78%)
- Bangalore and Chennai had 65-68% acceptance
- Other locations showed 60-62% acceptance

**4. Correlation Insights**:

| Feature Pair | Correlation | Interpretation |
|--------------|-------------|----------------|
| Age vs Experience | 0.82 | Strong positive correlation (expected) |
| Expected Hike vs Offered Hike | 0.45 | Moderate correlation |
| Percent Difference vs Status (Target) | 0.38 | Positive correlation: higher diff = more likely to accept |
| Notice Period vs Status | 0.21 | Weak positive correlation |
| Duration to Accept vs Status | -0.15 | Negative correlation: longer decision time = less likely to accept |

### Model Performance

| Model | ROC-AUC | Accuracy | Precision | Recall | F1-Score |
|-------|---------|----------|-----------|--------|----------|
| Logistic Regression | 0.72 | 0.74 | 0.78 | 0.85 | 0.81 |
| Random Forest | 0.75 | 0.76 | 0.80 | 0.83 | 0.82 |

**Interpretation**:
- Both models perform reasonably well (AUC > 0.70)
- Random Forest slightly outperforms Logistic Regression (AUC +0.03)
- High recall (0.83-0.85) indicates models correctly identify most candidates who will accept
- Precision (0.78-0.80) shows that 78-80% of predicted "will accept" candidates actually accept

**Feature Importance (Random Forest)**:

1. **Notice Period** (0.18)
2. **Percent Difference CTC** (0.16)
3. **Candidate Location** (0.12)
4. **LOB** (0.11)
5. **Offered Band** (0.09)
6. **Experience Years** (0.08)
7. **Joining Bonus** (0.06)
8. **Candidate Source** (0.05)
9. **Duration to Accept Offer** (0.05)
10. **Age** (0.04)

**Key Predictors**:
- Notice period is the strongest predictor
- Salary satisfaction (Percent Difference) is a close second
- Location and LOB reflect organizational context
- Candidate attributes (experience, age) are moderate predictors

## Next Steps

### Model Enhancement

1. **Hyperparameter Tuning**:
   - Implement GridSearchCV or RandomizedSearchCV for Random Forest
   - Optimize `n_estimators`, `max_depth`, `min_samples_split` parameters
   - Target performance improvement: +0.05 ROC-AUC

2. **Advanced Models**:
   - XGBoost/Gradient Boosting: Likely to capture complex patterns better
   - Ensemble methods (Voting/Stacking): Combine Logistic Regression and Random Forest
   - Neural Networks: Explore if additional complexity improves performance

3. **Feature Engineering**:
   - Create interaction features (experience × expected hike)
   - Create polynomial features (salary difference squared)
   - Consider binning continuous variables (notice period groups)
   - Include text features from candidate source (if available)

4. **Class Imbalance Handling**:
   - Try SMOTE (Synthetic Minority Over-sampling Technique)
   - Implement different sampling strategies
   - Adjust class weights further

### Deeper Analysis

5. **Segmentation Analysis**:
   - Analyze predictors by candidate segment (experience level, LOB)
   - Identify segments where model performs poorly
   - Develop segment-specific models

6. **Temporal Analysis**:
   - Investigate if acceptance patterns change over time
   - Consider seasonality effects

7. **Causal Analysis**:
   - Conduct propensity score matching
   - Identify causal factors vs correlational factors

### Practical Application

8. **Decision Support Tool**:
   - Develop a candidate scoring system
   - Build simple dashboard for recruitment team
   - Create early-warning system for likely declines

9. **Process Improvement Recommendations**:
   - Optimize interview duration based on findings
   - Adjust offer strategies for different candidate segments
   - Create targeted retention strategies

10. **Validation**:
    - Cross-validation on hold-out dataset
    - A/B testing on recruitment process changes
    - Periodic model retraining (quarterly)

## Project Outline

- [EDA and Modeling Notebook](https://github.com/kimkok-UCBerkeleyHaas/Capstone_Initial_Report-EDA_Assignment-20-1_Module-20/blob/main/01_EDA_Recruitment_Acceptance_Prediction.ipynb)
- 
- [Final Report](https://github.com/kimkok-UCBerkeleyHaas/Capstone_Initial_Report-EDA_Assignment-20-1_Module-20/blob/main/README.md)
-
- Colab Link : https://colab.research.google.com/github/kimkok-UCBerkeleyHaas/Capstone_Initial_Report-EDA_Assignment-20-1_Module-20/blob/main/01_EDA_Recruitment_Acceptance_Prediction.ipynb
 

---

## Appendix: Code Snippets and Detailed Analysis

### Feature Engineering Examples

```python
# Created Salary_Satisfaction_Flag
df['Salary_Satisfaction_Flag'] = np.where(
    df['Percent hike offered in CTC'] >= df['Pecent hike expected in CTC'], 1, 0
)

# Created Expected_vs_Offered_Ratio
df['Expected_vs_Offered_Ratio'] = df['Pecent hike expected in CTC'] / (df['Percent hike offered in CTC'] + 0.01)

# Created Candidate_Experience_Category
df['Experience_Category'] = pd.cut(
    df['Rex in Yrs'],
    bins=[0, 3, 8, 20],
    labels=['Junior', 'Mid', 'Senior']
)


### Recruitment Analysis Summary

 Acceptance Rate             Model Performance
     70%                           RF: 76%
     30%                           LR: 74%

 Top Factors                 Best Candidate Source
 • Notice Period             • Employee Referral (75%)
 • CTC Difference            • Agency (62%)
 • Location
 • LOB

 Recommendations
 ✓ Offer competitive CTC
 ✓ Joining bonus
 ✓ Predict decline early
 ✓ Recruitment dashboard

