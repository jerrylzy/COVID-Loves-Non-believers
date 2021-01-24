# CS 145: Data Management and Data Systems
## Project 3 | COVID Loves Non-believers

### Link to Jupyter Notebook
You can check out the detailed work from [project3.ipynb](https://github.com/jerrylzy/COVID-Loves-Non-believers/blob/main/project3.ipynb)
#### Collaborators:

* Jerry Liu, [@jerrylzy](https://github.com/jerrylzy/)
* Yihan Lin, [@linyh97](https://github.com/linyh97)

### Project Overview
#### Background
The new coronavirus (SARS-Cov-2) has significantly changed our lives. Right now, in the US, new infections are occuring at an all time high, and hospitals are on the brink of another overflow, possibly at a worse level than before. However, some states are doing much better than others.

#### Central Question of This Project
This project aims to unearth some key indicators to how well a state does during this pandemic. It compares each state’s COVID-19 situations in the United State to explore the likely culprits of higher infection rate and

#### Important Aspects to Examine
This project will examine whether lower population density, higher average temperatures, higher government stringency indices (stricter lockdown policies) and a decrease in mobility trends contribute to lower new cases per capita and lower death per capita. This project will also examine whether each state's performance varies significantly. The answers to these questions will help us understand the central question - what factors correlelate to how well a state does during the COVID-19 pandemic.


### Dataset Exploration

#### Introduction

We use four tables to get our data for US COVID-19 trending analysis: COVID Open Data, US Population Density, Government Response Stringency Index, and US 2020 Presidential Election Winner.


#### COVID Open Data
We get the COVID Open Data from big query: `bigquery-public-data.covid19_open_data.covid19_open_data`. 

COVID Open Data contains country-level datasets of daily time-series data related to COVID-19 globally.  It merges multiple sources at a fine spatial resolution, using a consistent set of region keys. The dataset contains a total size of 1.31GB data. We use this dataset to get new confirmed cases, new deceased cases, temperatures, and mobility data based on state and date.


#### US Population Density

We get the US Population Density Data from https://worldpopulationreview.com/state-rankings/state-densities. 

US Population density dataset contains the population density of all US states and DC in 2020. The density shows number of people per square mile, and is calculated by state population in 2020 dividing by state land area(square mile).


#### Government Response Stringency Index

We get the Government Response Stringency Index Data from https://data.humdata.org/dataset/oxford-covid-19-government-response-tracker.

We used this to cleansing data from the Oxford government stringency index csv file. Here, we added 28 days because usually it will take weeks for government's easing or increasing restrictions to show up in cases. We also removed incomplete data where the stringency indices are not available.
```sql
SELECT
  RegionName state,
  DATE_ADD(PARSE_DATE("%Y%m%d", CAST(Date AS STRING)), INTERVAL 28 DAY) date,
  StringencyIndex,
  StringencyLegacyIndex,
  GovernmentResponseIndex,
  ContainmentHealthIndex,
  EconomicSupportIndex
FROM `cs145-fa20-290805.Project3.gov_stringency`  
WHERE CountryCode = 'USA' AND StringencyIndex IS NOT NULL AND RegionName IS NOT NULL
ORDER BY RegionName, date
```

#### US 2020 Presidential Election Winner
This dataset contains the state name, its abbreviation and the winner party of the 2020 presidential election. We just use the winner party for coloring and illustration purposes during the data exploration section to demonstrate the disparity between red states and blue states.
This table is created by ourselves based on the [current official call from AP](https://apps.npr.org/elections20-interactive/#/president).

### Conclusion
In conclusion, this project found that colder temperatures, higher government stringency index and a state less supportive of mask usage and social distancing indicate lower daily infections per capita as they share a negative correlation, just like we initially expected. However, we also found that a higher population density actually correlates fewer new cases per capita and that a decrease in mobility trend correlates with more new cases per capita, the opposite of what we expected. 

This, however, can be easily explained because:
1. a state with higher population density tends to be more Democratic and liberal and thus is more likely to have a higher mask usage and adherence to social distancing. 
2. when cases spike up, governments tend to enact policies to restrict unnecessary activities, and people often decide to stay home on their own. When cases go down, governments tend to lessen restrictions, and people usually go out more often.

Overall, the features we chose seem indicative of whether a state has a low or high number of new cases per capita on a certain date because our data analysis and visualization showed correlations, and our simple logistic regression model performed relatively well.
