# Activity: Explore hypothesis testing

## Introduction

You work for an environmental think tank called Repair Our Air (ROA). ROA is formulating policy recommendations to improve the air quality in America, using the Environmental Protection Agency's Air Quality Index (AQI) to guide their decision making. An AQI value close to 0 signals "little to no" public health concern, while higher values are associated with increased risk to public health. 

They've tasked you with leveraging AQI data to help them prioritize their strategy for improving air quality in America.

ROA is considering the following decisions. For each, construct a hypothesis test and an accompanying visualization, using your results of that test to make a recommendation:

1. ROA is considering a metropolitan-focused approach. Within California, they want to know if the mean AQI in Los Angeles County is statistically different from the rest of California.
2. With limited resources, ROA has to choose between New York and Ohio for their next regional office. Does New York have a lower AQI than Ohio?
3. A new policy will affect those states with a mean AQI of 10 or greater. Will Michigan be affected by this new policy?

**Notes:**
1. For your analysis, you'll default to a 5% level of significance.
2. Throughout the lab, for two-sample t-tests, use Welch's t-test (i.e., setting the `equal_var` parameter to `False` in `scipy.stats.ttest_ind()`). This will account for the possibly unequal variances between the two groups in the comparison.

## Step 1: Imports

To proceed with your analysis, import `pandas` and `numpy`. To conduct your hypothesis testing, import `stats` from `scipy`.

#### Import Packages


```python
# Import relevant packages

import pandas as pd
import numpy as np
from scipy import stats

### YOUR CODE HERE ###
```

You are also provided with a dataset with national Air Quality Index (AQI) measurements by state over time for this analysis. `Pandas` was used to import the file `c4_epa_air_quality.csv` as a dataframe named `aqi`. As shown in this cell, the dataset has been automatically loaded in for you. You do not need to download the .csv file, or provide more code, in order to access the dataset and proceed with this lab. Please continue with this activity by completing the following instructions.

**Note:** For purposes of your analysis, you can assume this data is randomly sampled from a larger population.

#### Load Dataset


```python
# RUN THIS CELL TO IMPORT YOUR DATA.

### YOUR CODE HERE ###
aqi = pd.read_csv('c4_epa_air_quality.csv')
```

## Step 2: Data Exploration

### Before proceeding to your deliverables, explore your datasets.

Use the following space to surface descriptive statistics about your data. In particular, explore whether you believe the research questions you were given are readily answerable with this data.


```python
# Explore your dataframe `aqi` here:

print("A glimpse of the  data")
print(aqi.head(10))

print("A summary of the descriptive statistics")
print(aqi.describe(include = "all"))

print("A count of the records for each state")
print(aqi["state_name"].value_counts())
```

    A glimpse of the  data
       Unnamed: 0  date_local    state_name   county_name      city_name  \
    0           0  2018-01-01       Arizona      Maricopa        Buckeye   
    1           1  2018-01-01          Ohio       Belmont      Shadyside   
    2           2  2018-01-01       Wyoming         Teton  Not in a city   
    3           3  2018-01-01  Pennsylvania  Philadelphia   Philadelphia   
    4           4  2018-01-01          Iowa          Polk     Des Moines   
    5           5  2018-01-01        Hawaii      Honolulu  Not in a city   
    6           6  2018-01-01        Hawaii      Honolulu  Not in a city   
    7           7  2018-01-01  Pennsylvania          Erie           Erie   
    8           8  2018-01-01        Hawaii      Honolulu       Honolulu   
    9           9  2018-01-01      Colorado       Larimer   Fort Collins   
    
                                         local_site_name   parameter_name  \
    0                                            BUCKEYE  Carbon monoxide   
    1                                          Shadyside  Carbon monoxide   
    2  Yellowstone National Park - Old Faithful Snow ...  Carbon monoxide   
    3                             North East Waste (NEW)  Carbon monoxide   
    4                                          CARPENTER  Carbon monoxide   
    5                                            Kapolei  Carbon monoxide   
    6                                            Kapolei  Carbon monoxide   
    7                                                NaN  Carbon monoxide   
    8                                           Honolulu  Carbon monoxide   
    9                      Fort Collins - CSU - S. Mason  Carbon monoxide   
    
        units_of_measure  arithmetic_mean  aqi  
    0  Parts per million         0.473684    7  
    1  Parts per million         0.263158    5  
    2  Parts per million         0.111111    2  
    3  Parts per million         0.300000    3  
    4  Parts per million         0.215789    3  
    5  Parts per million         0.994737   14  
    6  Parts per million         0.200000    2  
    7  Parts per million         0.200000    2  
    8  Parts per million         0.400000    5  
    9  Parts per million         0.300000    6  
    A summary of the descriptive statistics
            Unnamed: 0  date_local  state_name  county_name      city_name  \
    count   260.000000         260         260          260            260   
    unique         NaN           1          52          149            190   
    top            NaN  2018-01-01  California  Los Angeles  Not in a city   
    freq           NaN         260          66           14             21   
    mean    129.500000         NaN         NaN          NaN            NaN   
    std      75.199734         NaN         NaN          NaN            NaN   
    min       0.000000         NaN         NaN          NaN            NaN   
    25%      64.750000         NaN         NaN          NaN            NaN   
    50%     129.500000         NaN         NaN          NaN            NaN   
    75%     194.250000         NaN         NaN          NaN            NaN   
    max     259.000000         NaN         NaN          NaN            NaN   
    
           local_site_name   parameter_name   units_of_measure  arithmetic_mean  \
    count              257              260                260       260.000000   
    unique             253                1                  1              NaN   
    top            Kapolei  Carbon monoxide  Parts per million              NaN   
    freq                 2              260                260              NaN   
    mean               NaN              NaN                NaN         0.403169   
    std                NaN              NaN                NaN         0.317902   
    min                NaN              NaN                NaN         0.000000   
    25%                NaN              NaN                NaN         0.200000   
    50%                NaN              NaN                NaN         0.276315   
    75%                NaN              NaN                NaN         0.516009   
    max                NaN              NaN                NaN         1.921053   
    
                   aqi  
    count   260.000000  
    unique         NaN  
    top            NaN  
    freq           NaN  
    mean      6.757692  
    std       7.061707  
    min       0.000000  
    25%       2.000000  
    50%       5.000000  
    75%       9.000000  
    max      50.000000  
    A count of the records for each state
    California              66
    Arizona                 14
    Ohio                    12
    Florida                 12
    Texas                   10
    New York                10
    Pennsylvania            10
    Michigan                 9
    Colorado                 9
    Minnesota                7
    New Jersey               6
    Indiana                  5
    North Carolina           4
    Massachusetts            4
    Maryland                 4
    Oklahoma                 4
    Virginia                 4
    Nevada                   4
    Connecticut              4
    Kentucky                 3
    Missouri                 3
    Wyoming                  3
    Iowa                     3
    Hawaii                   3
    Utah                     3
    Vermont                  3
    Illinois                 3
    New Hampshire            2
    District Of Columbia     2
    New Mexico               2
    Montana                  2
    Oregon                   2
    Alaska                   2
    Georgia                  2
    Washington               2
    Idaho                    2
    Nebraska                 2
    Rhode Island             2
    Tennessee                2
    Maine                    2
    South Carolina           1
    Puerto Rico              1
    Arkansas                 1
    Kansas                   1
    Mississippi              1
    Alabama                  1
    Louisiana                1
    Delaware                 1
    South Dakota             1
    West Virginia            1
    North Dakota             1
    Wisconsin                1
    Name: state_name, dtype: int64


#### **Question 1: From the preceding data exploration, what do you recognize?**

There's enough data for California to test the first hypothesis

There's enough data for New York and Ohio to test the second hypothesis

There's enough Michigan Data to test the third hypothesis

## Step 3. Statistical Tests

Before you proceed, recall the following steps for conducting hypothesis testing:

1. Formulate the null hypothesis and the alternative hypothesis.<br>
2. Set the significance level.<br>
3. Determine the appropriate test procedure.<br>
4. Compute the p-value.<br>
5. Draw your conclusion.

### Hypothesis 1: ROA is considering a metropolitan-focused approach. Within California, they want to know if the mean AQI in Los Angeles County is statistically different from the rest of California.

Before proceeding with your analysis, it will be helpful to subset the data for your comparison.


```python
# Create dataframes for each sample being compared in your test

### First Hypothesis ###

print("First Hypothesis Data")

#LA County
LA_county_sample = aqi[aqi["county_name"] == "Los Angeles"]
print("The number of LA county records is ", LA_county_sample.count()["county_name"])
print(LA_county_sample.head())

#California Without LA County
Cali_without_LA_county_sample = aqi[(aqi["state_name"] == "California") 
                                    & (aqi["county_name"] != "Los Angeles")]
print("The number of California state records (excluding LA county) is ", Cali_without_LA_county_sample.count()["state_name"])
print(Cali_without_LA_county_sample.head())
```

    First Hypothesis Data
    The number of LA county records is  14
         Unnamed: 0  date_local  state_name  county_name      city_name  \
    33           33  2018-01-01  California  Los Angeles      Lancaster   
    42           42  2018-01-01  California  Los Angeles  Santa Clarita   
    61           61  2018-01-01  California  Los Angeles       Pasadena   
    76           76  2018-01-01  California  Los Angeles    Los Angeles   
    109         109  2018-01-01  California  Los Angeles    Los Angeles   
    
                       local_site_name   parameter_name   units_of_measure  \
    33       Lancaster-Division Street  Carbon monoxide  Parts per million   
    42                   Santa Clarita  Carbon monoxide  Parts per million   
    61                        Pasadena  Carbon monoxide  Parts per million   
    76                    LAX Hastings  Carbon monoxide  Parts per million   
    109  Los Angeles-North Main Street  Carbon monoxide  Parts per million   
    
         arithmetic_mean  aqi  
    33          0.394737    7  
    42          0.394737    7  
    61          0.789474   16  
    76          0.863158   17  
    109         0.994737   17  
    The number of California state records (excluding LA county) is  52
        Unnamed: 0  date_local  state_name     county_name      city_name  \
    16          16  2018-01-01  California  San Bernardino        Ontario   
    18          18  2018-01-01  California      Sacramento   Arden-Arcade   
    26          26  2018-01-01  California          Orange       La Habra   
    27          27  2018-01-01  California         Alameda  Not in a city   
    34          34  2018-01-01  California          Fresno         Fresno   
    
                     local_site_name   parameter_name   units_of_measure  \
    16  Ontario Near Road (Etiwanda)  Carbon monoxide  Parts per million   
    18     Sacramento-Del Paso Manor  Carbon monoxide  Parts per million   
    26                      La Habra  Carbon monoxide  Parts per million   
    27        Berkeley- Aquatic Park  Carbon monoxide  Parts per million   
    34              Fresno - Garland  Carbon monoxide  Parts per million   
    
        arithmetic_mean  aqi  
    16         0.747368   11  
    18         0.752632   16  
    26         0.673684   13  
    27         1.088889   15  
    34         1.000000   15  


#### Formulate your hypothesis:

**Formulate your null and alternative hypotheses:**

*   $H_0$: There is no difference in the mean AQI between Los Angeles County and the rest of California.
*   $H_A$: There is a difference in the mean AQI between Los Angeles County and the rest of California.


#### Set the significance level:


```python
# For this analysis, the significance level is 5%

sig_lvl = 0.05
sig_lvl
```




    0.05



#### Determine the appropriate test procedure:

Here, you are comparing the sample means between two independent samples. Therefore, you will utilize a **two-sample  𝑡-test**.

#### Compute the P-value


```python
# Compute your p-value here

stats.ttest_ind(a = LA_county_sample["aqi"]
                , b = Cali_without_LA_county_sample["aqi"]
                , equal_var = False)

```




    Ttest_indResult(statistic=2.1107010796372014, pvalue=0.049839056842410995)



#### **Question 2. What is your P-value for hypothesis 1, and what does this indicate for your null hypothesis?**

The p-value is 0.049839056842410995. This indicates that there is a statistical difference between the mean AQI of LA county and the mean AQI of California (excluding LA County).

So we reject the null hypothesis and predict that there is a difference between the mean AI of LA County and California.

### Hypothesis 2: With limited resources, ROA has to choose between New York and Ohio for their next regional office. Does New York have a lower AQI than Ohio?

Before proceeding with your analysis, it will be helpful to subset the data for your comparison.


```python
# Create dataframes for each sample being compared in your test

### Second Hypothesis ###

print("\nSecond Hypothesis Data")

#New York

NY_sample = aqi[aqi["state_name"] == "New York"]
print("The number of New York state records is ", NY_sample.count()["state_name"])

#Ohio

OH_sample = aqi[aqi["state_name"] == "Ohio"]
print("The number of Ohio state records is ", OH_sample.count()["state_name"])

```

    
    Second Hypothesis Data
    The number of New York state records is  10
    The number of Ohio state records is  12


#### Formulate your hypothesis:

**Formulate your null and alternative hypotheses:**

*   $H_0$: The mean AQI of New York is greater than or equal to that of Ohio.
*   $H_A$: The mean AQI of New York is **below** that of Ohio.


#### Significance Level (remains at 5%)

#### Determine the appropriate test procedure:

Here, you are comparing the sample means between two independent samples in one direction. Therefore, you will utilize a **two-sample  𝑡-test**.

#### Compute the P-value


```python
# Compute your p-value here

stats.ttest_ind(a = NY_sample["aqi"]
                , b = OH_sample["aqi"]
                ,alternative = "less"
                , equal_var = False)

```




    Ttest_indResult(statistic=-2.025951038880333, pvalue=0.030446502691934697)



#### **Question 3. What is your P-value for hypothesis 2, and what does this indicate for your null hypothesis?**

With a p-value (0.030) of less than 0.05 (as your significance level is 5%) and a t-statistic < 0 (-2.036), **reject the null hypothesis in favor of the alternative hypothesis**.

Therefore, you can conclude at the 5% significance level that New York has a lower mean AQI than Ohio.

###  Hypothesis 3: A new policy will affect those states with a mean AQI of 10 or greater. Will Michigan be affected by this new policy?

Before proceeding with your analysis, it will be helpful to subset the data for your comparison.


```python
# Create dataframes for each sample being compared in your test

### Third Hypothesis ###

print("Third Hypothesis Data")

#Michigan
MI_sample = aqi[aqi["state_name"] == "Michigan"]
print("The number of Michigan state records is ", MI_sample.count()["state_name"])
```

    Third Hypothesis Data
    The number of Michigan state records is  9


#### Formulate your hypothesis:

**Formulate your null and alternative hypotheses here:**

*   $H_0$: The mean AQI of Michigan is less than or equal to 10.
*   $H_A$: The mean AQI of Michigan is greater than 10.


#### Significance Level (remains at 5%)

#### Determine the appropriate test procedure:

Here, you are comparing one sample mean relative to a particular value in one direction. Therefore, you will utilize a **one-sample  𝑡-test**. 

#### Compute the P-value


```python
# Compute your p-value here

stats.ttest_1samp(MI_sample['aqi'], 10, alternative='greater')
```




    Ttest_1sampResult(statistic=-1.7395913343286131, pvalue=0.9399405193140109)



#### **Question 4. What is your P-value for hypothesis 3, and what does this indicate for your null hypothesis?**

With a p-value (0.940) being greater than 0.05 (as your significance level is 5%) and a t-statistic < 0 (-1.74), fail to reject the null hypothesis.

Therefore, you cannot conclude at the 5% significance level that Michigan's mean AQI is greater than 10. This implies that Michigan would most likely not be affected by the new policy.

## Step 4. Results and Evaluation

Now that you've completed your statistical tests, you can consider your hypotheses and the results you gathered.

#### **Question 5. Did your results show that the AQI in Los Angeles County was statistically different from the rest of California?**

Yes

#### **Question 6. Did New York or Ohio have a lower AQI?**

Using a 5% significance level, you can conclude that New York has a lower AQI than Ohio based on the results.

#### **Question 7: Will Michigan be affected by the new policy impacting states with a mean AQI of 10 or greater?**



Based on the tests, you would fail to reject the null hypothesis, meaning you can't conclude that the mean AQI is greater than 10. Thus, it is unlikely that Michigan would be affected by the new policy.

# Conclusion

**What would you consider presenting to your manager as part of your findings?**

For each test, you would present the null and alternative hypothesis, then describe your conclusion and the resulting p-value that drove that conclusion. As the setup of t-test's have a few key configurations that dictate how you interpret the result, you would specify the type of test you chose, whether that tail was one-tail or two-tailed, and how you performed the t-test from `stats`.

**What would you convey to external stakeholders?**

In answer to the research questions posed, you would convey the level of significance (5%) and your conclusion. Additionally, providing the sample statistics being compared in each case will likely provide important context for stakeholders to quickly understand the difference between your results.

**Congratulations!** You've completed this lab. However, you may not notice a green check mark next to this item on Coursera's platform. Please continue your progress regardless of the check mark. Just click on the "save" icon at the top of this notebook to ensure your work has been logged.
