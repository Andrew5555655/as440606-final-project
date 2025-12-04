# Add Health Replication Project: GPA, Ethnicity, and Adolescent Popularity

This repository contains the group outcome for the final project of this course. It presents our econometric replication and extended analysis based on the Add Health dataset.

## 1. Overview

This project uses the public version of the National Longitudinal Study of Adolescent to Adult Health (Add Health) Wave I data and focuses on the following questions:

* How does academic performance (GPA) influence adolescents' popularity within their classrooms?
* Does this relationship vary across different ethnic groups and immigration statuses?
* When indicators of antisocial behavior (alcohol use, drunk driving, marijuana use, serious physical harm to others) are added, does popularity change systematically?

Popularity is measured by the number of friendship nominations (`num_nom`), and academic performance is captured by a GPA constructed from core subject grades.

## 2. Data and Variable Sources

We use three Add Health Wave I public `.rda` files (from ICPSR):

* `21600-0001-Data.rda` – Adolescent questionnaire data
* `21600-0002-Data.rda` – Parent questionnaire data
* `21600-0003-Data.rda` – School and contextual data

The three datasets are merged using the unique respondent identifier `AID`, forming the individual-level master dataset `dataset`. Based on this dataset, we constructed:

* Control variables (`df.control`): age, gender, family structure (living with mother, twin status, household size, number of grades 7–12 students), parental education, parental employment, and broad occupational categories
* GPA (`gpa`): calculated from numeric grades in English, math, history/social science, and science; unreasonable values above 4 were removed, then the average was taken and standardized
* Ethnicity and immigration status (`df.race_cat`, `gen`):

  * Ethnicity is derived from a multi-select race/ethnicity question. Students selecting multiple categories were coded as “Multiracial” and recoded into the variable `Race_category`.
  * Immigration status `Generation` is defined based on the birthplace of the student and parents, distinguishing between “Immigrant” and “Non-immigrant”
  * The two were further combined into the variable `Race and immigration`
* Popularity (`df.independent`): the number of friendship nominations `IDGX2`, renamed as `num_nom`
* Antisocial behaviors (`df.antisocial`): drunk driving, frequency of alcohol use, frequency of marijuana use, and indicators of causing serious harm to others, recoded as ordered categories

The final analytical sample `df.final` is restricted to non-immigrant adolescents with complete information on GPA, ethnicity, immigration status, popularity, and all relevant control variables.


## 3. Replication

In the file `addhealth_replication.Rmd`, our group completed the following replication tasks from the original project:

1. Figure 1: Relationship between GPA, ethnicity, and popularity

2. Table 1: OLS regression (baseline model)

   * Dependent variable: number of friendship nominations `num_nom`
   * Key explanatory variable: centered GPA (`center`) and its interaction with `Race and immigration`
   * Control variables include age, gender, family structure, and parental education, employment, and occupation
   * Results are consistent with the original project: GPA has a positive main effect, meaning high-achieving students receive more nominations on average, while most GPA×ethnicity–immigration interaction terms are not statistically significant, suggesting limited differences in the “social return to academic performance” across ethnic groups

3. Table 2: Extended OLS model including antisocial behavior variables

   * Introduces indicators such as drunk driving, alcohol use frequency, marijuana use frequency, and causing serious physical harm
   * Serves as our group’s econometric extension to test whether antisocial behavior alters popularity
   * Results show generally small and statistically insignificant coefficients. After controlling for GPA and demographic characteristics, antisocial behavior does not systematically increase popularity and may even have slight negative effects, indicating potential social costs within peer groups

## 4. Group Econometric Extension

After completing the replication of the original figures and OLS models, our group designed an additional extension:

### Robustness Check Using Count Models: Poisson and Negative Binomial Regression (Table 3)

This is the core robustness extension in our project, corresponding to “Robustness check: count models for popularity” and Table 3 in the Rmd file.

* Motivation

  * `num_nom` is a non-negative integer count variable. While OLS is a common approximation, Poisson and negative binomial models better match the statistical properties of count data.
* Overdispersion check

  * We computed the mean and variance of `num_nom` and found that the variance substantially exceeds the mean, indicating clear overdispersion. Therefore, the negative binomial model is more appropriate than Poisson (which assumes mean equals variance).
* Model specification

  * Poisson regression: `glm(..., family = poisson)`
  * Negative binomial regression: `MASS::glm.nb(...)`
  * The independent variables match those in the baseline OLS model for comparability
* Main results (from Table 3)

  * The coefficients for GPA (`center`) and its interactions with `Race and immigration` have similar signs and magnitudes to the OLS estimates
  * Most interaction terms remain statistically insignificant
  * The negative binomial model is more suitable under overdispersion, yet it produces the same qualitative conclusions as OLS: academically stronger students are more popular, and ethnic differences in the “social return to academic achievement” remain limited

Overall, the robustness check using count models reinforces the main findings from the OLS models and represents a complete and meaningful econometric extension by our group.

