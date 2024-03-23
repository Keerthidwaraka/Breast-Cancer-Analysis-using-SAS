
# Breast Cancer Data Analysis and Insights
Breast cancer is the second leading cause of death among women globally and one of the most common diagnosable cancers in the United States. With millions of women living with or having a history of breast cancer, understanding the nuances of this disease is crucial for effective management and treatment. This project aims to provide rigorous analysis and insights into breast cancer using data obtained from the National Cancer Institute's Surveillance, Epidemiology, and End Results Program (SEER) spanning from 1992 to 2020.


## Dataset Background
The dataset obtained from the SEER Registry includes individuals diagnosed with breast cancer between 1992 and 2020. It comprises various demographic and clinical factors such as age, sex, main cancer site, year of diagnosis, ICD codes, months from diagnosis to therapy, race, cause of death, course of treatment, and more. SEER collects and disseminates data on cancer incidence and survival from population-based cancer registries, covering approximately 48.0% of the US population. It receives death data from the National Center for Health Statistics and demographic data from the Census Bureau.
## Focus of the Study
The primary focus of this study is to analyze survival length and overall average survival rates of breast cancer patients. The research question guiding this analysis is: What is the impact of delay in treatment on survival months?
## Handling Data
1. Missing data: The term "unknown" is used to remove missing data as "Blanks" from records.

2. Survival months are recoded into four categories as survival code:
- <5 years
- 5-<10 years
- 10-<15 years
- 15-20 years
## Data Analysis

## Demographics

1. Utilized PROC FREQ to generate a table for sex data.
```
/*table for Sex data*/
proc freq data=my_projectBC2a;
tables Sex;
run;
```
Insights: 
- Female: 99.31%
- Male: 0.69%

2. Employed PROC GCHART to create a pie chart illustrating the distribution of races.
```
/* piechart for race*/
proc gchart data=my_projectBC2a;
title'piechart for Race';
pie Race_recode__White__Black__Othe/discrete
percent=inside slice= inside;
where Race_recode__White__Black__Othe ~="Unknown";
run;
```
Insights:
- White: 81.15%
- Black: 10.02%
- Other: 8.82%

3. A pie chart was generated to visualize the percentage distribution of survival codes.
```
data my_projectBC1a;
set my_project;
if Survival_months='Unknow' then delete;
Survival_months1=Survival_months*1;
Rename Survival_months1=Survival_months;
drop Survival_months;
run;

data my_projectBC2;
set my_project1a;
length Survivalcode $10.;
if Survival_months <=59 then Survivalcode='5 years';
if 59< Survival_months<=119 then Survivalcode='10 years';
if 119<Survival_months<=179 then Survivalcode='15 years';
if Survival_months >179 then Survivalcode='20 years';
run; 

proc gchart data=my_projectBC2;
title'Percent and frequency Chart of Survivalcode';
pie Survivalcode/discrete
value=inside percent=inside slice= outside;
run;
```
Insights: 
- <5 years: 37.5%
- 5-<10 years: 24.3%
- 10-<15 years: 16.7%
- 15-20 years: 21.5%


## Descriptive Statistics and Tests of Normality
Descriptive statistics, including median, skewness, kurtosis, and interquartile range, were calculated to summarize the distribution of survival months. Tests of normality were performed to assess the assumption of normality for the survival months variable.
```
Proc univariate data =my_projectBC2a normal plot;
title 'Descriptive statistics';
var Survival_months;
run;
```
Insights: 
- Mean Survival_months was 86.57 with SD(64.19).
- Median: 108.0
- Skewness: 0.76
- Kurtosis: -0.33
- IQR (Interquartile Range): 129
- All tests for normality for the Survival_months variable were <0.05, indicating a non-normal distribution.

Overall, these results suggest that the distribution of survival times is moderately skewed to the right, with a slightly flatter shape than a normal distribution. The variability in survival times is relatively high, as indicated by the large standard deviation, and the data significantly deviate from a normal distribution.

## Scatter Plot
A scatter plot was utilized to visualize the relationship between the months from diagnosis to treatment and the length of survival in breast cancer patients.

Analysis: Identifies trends, impact of treatment delays on survival.

```
proc sgplot data=my_projectBC2a;
title 'Months from Diagnosis to Treatment by Survival Months';
scatter x=Months_from_diagnosis_to_treatm y=Survival_months / group = Race_recode__White__Black__Othe markerattrs=(size=5 
symbol= circlefilled);
where Race_recode__White__Black__Othe ~= "Unknown" and Months_from_diagnosis_to_treatm >=5;
Run;

proc corr data = my_projectBC2a pearson;
title "Coorelation Analysis";
var Months_from_diagnosis_to_treatm Survival_months;
run;
```
Insights:

The scatter plot revealed a weak/negative correlation between the months from diagnosis to treatment and the length of survival. This suggests that delays in initiating treatment may have a modest adverse effect on survival outcomes in breast cancer patients.


## Box Plot
Box plots were employed to visualize the distribution of survival months across different groups or categories, such as race or age.

Analysis: Highlights distribution disparities, median, quartiles, outliers.
```
proc sgplot data=my_projectBC2a;
title 'Months from Diagnosis to Treatment by Survival Months';
scatter x=Months_from_diagnosis_to_treatm y=Survival_months / group = Race_recode__White__Black__Othe markerattrs=(size=5 
symbol= circlefilled);
where Race_recode__White__Black__Othe ~= "Unknown" and Months_from_diagnosis_to_treatm >=5;
Run;

proc corr data = my_projectBC2a pearson;
title "Coorelation Analysis";
var Months_from_diagnosis_to_treatm Survival_months;
run;
```
Insights:

Variations in survival months were observed among different groups, highlighting potential disparities in survival outcomes based on demographic or clinical characteristics.

## Chi-Square Analysis 
Chi-square tests were performed to examine the association between categorical variables such as race and chemotherapy, as well as survival and chemotherapy.
These tests help understand the relationships between categorical variables and identify any significant associations.

```
/* survival recoded and chi-square analysis 5year survival vs Race */
data my_projectBCX;
set my_project2a;
length Survivalcode $10.;
if Survival_months <=59 then New_Survivalcode="N";
if Survival_months >59 then New_Survivalcode="Y";
run; 

proc freq data=my_projectBCX;
title 'chi-square analysis 5 years survival';
tables New_Survivalcode * Race_recode__White__Black__Othe / expected chisq;
where Race_recode__White__Black__Othe ~="Unknown";
footnote *New_Survivalcode Y= Patient survived for >=5 years;
Run;

/*chi-square analysis- race and chemotherapy */
proc freq data=my_projectX;
title 'chi-square analysis chemotherapy with Race';
tables Chemotherapy_recode__yes__no_un * Race_recode__White__Black__Othe / expected chisq;
where Race_recode__White__Black__Othe ~="Unknown";
footnote *Missing data excluded;
run;

/*chi-square analysis- Survival and chemotherapy */
proc freq data=my_projectX;
title 'chi-square analysis 5 years survival';
tables New_Survivalcode * Chemotherapy_recode__yes__no_un / expected chisq;
where Race_recode__White__Black__Othe ~="Unknown";
footnote *Missing data excluded;
run;

```
Insights:
1. Overall 5-Year Survival Rate:

-Only 57% of breast cancer patients survived for at least 5 years, indicating room for improvement in long-term survival outcomes.
5-Year Survival Rates by Race:

2. Survival rates vary among racial groups:
- Black patients: 50.64%
- White patients: 58.62%
- Other racial groups: 55.24%
-Disparities in survival rates suggest potential differences in access to care or treatment outcomes across racial demographics.

3. Chemotherapy Administration:
-Only 40% of patients initiated chemotherapy as part of their treatment regimen.

-Disparities in chemotherapy initiation across racial groups:
- Black patients: 51.6%
- White patients: 38.08%
- Other racial groups: 44.08%
-Variations in chemotherapy uptake may contribute to differences in survival outcomes among racial groups.

4. Impact of Chemotherapy on 5-Year Survival:

-Patients who received chemotherapy exhibited significantly better 5-year survival rates, with a rate of 58.19%.
Emphasizes the crucial role of chemotherapy in improving long-term survival outcomes for breast cancer patients.


## Analysis of Variance (ANOVA)
ANOVA was conducted to compare the means of survival months across different age groups. The aim was to determine if there is a statistically significant difference in survival months between age categories.

```
proc Anova data=my_projectBC2a;
title 'Analysis of variance in Surval months between Age groups';
class Age_recode_with__1_year_olds;
model Survival_months= Age_recode_with__1_year_olds ;
means Age_recode_with__1_year_olds/ Tukey;
run;

```
Insights: 

The ANOVA test yielded a statistically significant difference in survival months among different age categories (p < 0.05). This indicates that age is associated with variations in survival months, with certain age groups experiencing longer or shorter survival times compared to others.

## Independent T-Test

Independent t-tests were used to compare the means of survival months between genders. The goal was to assess whether there is a significant difference in survival months between males and females.

```
/* Independent T-Test for Survival Months by Gender */
proc ttest data=my_projectBC2a;
   title 'Independent T-Test: Survival Months by Gender';
   class Sex;
   var Survival_months1; /* Use the transformed variable */
run;
```
Insights:

The independent t-test showed a statistically significant difference in survival months between males and females (p < 0.05).Females exhibited significantly higher survival rates compared to males, suggesting that gender may influence survival outcomes in breast cancer patients.

## Box Plot : Survival Months by Gender
The box plot illustrates the distribution of survival months among male and female breast cancer patients.
```
/* Box Plot for Survival Months by Gender */
proc sgplot data=my_projectBC2a;
  vbox Survival_months1 / category=Sex;
  title 'Box Plot: Survival Months by Gender';
run;
```
Insights:

- Females tend to have a higher median survival time compared to males, as evidenced by the box plot.
- There is greater variability in survival months among male patients, indicated by the length of the whiskers in the box plot.

## Contingency Table: Gender vs. Survival Months:

The contingency table is used to examine the association between gender (male/female) and survival months (< 5 years or >= 5 years).
It helps assess whether there is a relationship between gender and survival outcomes in breast cancer patients.
```
proc freq data=my_projectBC2a;
    title 'Contingency Table: Sex vs. Survival';
    tables Sex * Survival_months1 / chisq relrisk;
run;
```
Insights:

- The contingency table reveals that a higher proportion of female patients (70%) survived for at least 5 years compared to male patients (57%).
- There appears to be an association between gender and long-term survival rates, with females demonstrating a higher likelihood of surviving beyond 5 years compared to males.
- The differences in survival outcomes between genders may have implications for treatment strategies, healthcare delivery, and patient support services.


## Linear Regression Analysis

Linear regression analysis was performed to model the relationship between survival months (dependent variable) and other relevant variables (independent variables).

```
/* Linear Regression Analysis */
proc reg data=my_projectBC2a;
   title 'Linear Regression Analysis: Survival Months';
   model Survival_months = Age_recode_with__1_year_olds Sex Chemotherapy_recode__yes__no_un / r;
run;
```
Insights:

The linear regression model demonstrated that all variables included were significantly associated with survival months (p < 0.05), with an R-squared value of approximately 0.26. The model explained around 26% of the variability in survival months and provided valuable insights into the factors affecting survival outcomes in breast cancer patients.


## Discussion

The research question guiding this analysis focused on exploring the impact of delay in treatment on survival months in breast cancer patients. Through a comprehensive examination of various factors, including chemotherapy administration, survival rates among different demographic groups, and the time from diagnosis to treatment, valuable insights were gained into the complex interplay of treatment timing and survival outcomes.

The analysis revealed several key findings:

1. Chemotherapy Administration and Survival Rates: Significant differences were observed in chemotherapy administration among breast cancer patients, with disparities noted across racial groups. Importantly, patients who received chemotherapy demonstrated better 5-year survival rates compared to those who did not. This underscores the critical role of timely chemotherapy in improving survival outcomes and highlights the importance of ensuring equitable access to this vital treatment modality.

2. Time from Diagnosis to Treatment: While not directly measuring treatment delay, scatter plots and box plots provided visual representations of the relationship between the time from diagnosis to treatment and subsequent survival months. Although correlations were weak or negative, these analyses suggested potential trends indicating that longer intervals between diagnosis and treatment may be associated with shorter survival times. Further investigation into the factors contributing to treatment delays and their impact on survival outcomes is warranted to better understand and address this phenomenon.

3. Gender Disparities in Survival: Contingency table analysis revealed gender disparities in survival outcomes, with a higher proportion of female patients surviving for at least 5 years compared to male patients. While not directly related to treatment delay, these findings underscore the need for gender-sensitive approaches in breast cancer care and highlight potential areas for targeted interventions to improve outcomes among male patients.
## Conclusion

In conclusion, while this analysis did not directly measure the impact of treatment delay on survival months, it provided valuable insights into related factors that may influence survival outcomes in breast cancer patients. Timely administration of chemotherapy emerged as a critical determinant of long-term survival, underscoring the importance of ensuring prompt access to evidence-based treatments. Additionally, gender disparities in survival rates underscore the need for tailored interventions to address the unique needs of male patients in breast cancer care.

Moving forward, further research is needed to delve deeper into the factors contributing to treatment delays and their implications for survival outcomes. By addressing these gaps in knowledge and implementing targeted strategies to minimize delays and disparities in care, we can strive to improve overall survival rates and enhance the quality of life for individuals affected by breast cancer.
