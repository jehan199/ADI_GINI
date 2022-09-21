# The Relationship Between GINI and ADI

## Background on GINI and ADI

The goal of this project is to establish whether or not there is a potential link between inequality and overall societal conditions. To analyze this, I used two variables from publicly available datasets. 

The GINI coefficient is a variable that is used the track the distribution of either wealth or income in a given population. A GINI value of 1.0 means that the wealth or income of a given population is concentrated exclusively in the hands of one individual or household. Conversely, a value of O.O implies that the distribution of resources is allocated in a way in which each member of the population has the exact same amount. Data on the GINI values of US counties was sourced from the Census Bureau's 2020 dataset. 

URL: https://data.census.gov/cedsci/map?q=GINI%20by%20county&g=0100000US&tid=ACSDT5Y2020.B19083&layer=VT_2020_040_00_PP_D1&mode=thematic&loc=38.8800,-98.0000,z3.0000

ADI, short for the Area Deprivation Index, is a multidimensional evaluation of a region's socioeconomic condition that takes into account measurable metrics of social wellbeing including income, education, employment, and housing conditions. Data on the ADI Values where compiled by the company Broadstreet and the data was sourced from the list of public available datasets in Google Cloud. 

URL: https://console.cloud.google.com/marketplace/product/broadstreet-public-data/adi?project=adi-gini-case-study


## Load the Data Into Bigquery and Format for Analysis in R
```
WITH ADI_table as (SELECT 
  county_name, state_name, year, area_deprivation_index_percent, CONCAT(county_name,', ',state_name) AS Full_Name
FROM 
  `bigquery-public-data.broadstreet_adi.area_deprivation_index_by_county`
WHERE
  year = 2020)
SELECT 
  county_name, state_name, year, area_deprivation_index_percent, CONCAT(county_name,', ',state_name) AS Full_Name, EstimateGiniIndex_2020, 
FROM 
  ADI_table as ADI
LEFT JOIN 
  `adi-gini-case-study.GINI_INFO.GINI_2019-2020` as GINI
  ON ADI.FULL_NAME = GINI.Geographic_Area_Name_2020
```
## Export Output from Bigquery and Import Into R Studio

```
case_study_data <-read_csv("GINI_ADI_INFO.csv")
View(case_study_data)
install.packages('Tmisc')
library(Tmisc)
cor.test(case_study_data$EstimateGiniIndex_2020,case_study_data$area_deprivation_index_percent, method = "pearson")
ggplot(case_study_data,mapping = aes(area_deprivation_index_percent,EstimateGiniIndex_2020)) +
  geom_jitter()+geom_smooth()
```
## Link to R Markdown Output and Conclusions

Link to HTML output for visualizations: file:///Users/jeremyhandy/GINI_ADI-R-Markdown.html

The results of our correlation test for GINI and ADI indicate of statistically significant relationship between the two variables with a 95% confidence interval. With a corrilation coefficient of positive 0.401604, this data shows that counties with higher GINI coefficients tend to have higher ADI values.

While these values have a statistically significant relationship, it is import to note that correlation does not equate to causation and further research would be necessary to draw a definitive conclusions on how economic inequality impacts overall societal wellbeing.
  
