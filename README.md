# Data-Analysis-with-Power-BI-COVID-19-Dataset

## Table of Content
1. Introduction
2. Data preparation
3. Data visualization
4. Project Impact and Limitations

## Introduction
The project aims to utilize Power BI as a tool and demonstrate data analysis skills. The dataset used here is the COVID-19 Dataset from [Kaggle](https://www.kaggle.com/datasets/imdevskp/corona-virus-report/data). The dataset offers data pulled from various reliable sources and straightforward features. In this project, I used the ‘covid_19_clean_complete.csv’ file (hereafter will be referred as the Covid-19 dataset). The data file provides information on Covid-19 cases around the world from 22/1/2020 to 27/7/2020.

The project produced a visual report with interactive components in Power BI. The report includes several data visuals that make use of advanced features in Power BI like DAX measures, Bookmarks, and more. Thanks to these features, the report could quickly give a glimpse into how the pandemic is going around the world on both continent- and county-level.

It is worth noting that the project only uses the features available to basic users of Power BI. This limits it to just the Power BI Desktop app. There are features that would have been included if Premium version was available which will be discussed in the Limitation section.

## Data preparation
### Data sources
Data sources used for this project included the Covid-19 Dataset from Kaggle and a clean list of countries (hereafter will be referred as Countries dataset). The latter was later added to faciliate analysis on continental level. The clean list of countries was sourced from [thespreadsheetguru](https://www.thespreadsheetguru.com/list-countries-capitals-abbreviations).

### Data cleaning
The cleaning process started with the covid_19_clean_complete file. A few columns were deemed unnecessary for analysis and were deleted from the model. The remaining columns include: Country/Region, Lat, Long, Date, Confirmed, Deaths, Recovered, Active.

Next, an alignment of countries' name was needed. Using replace value feature and eyeballing, some countries' name in Covid-19 dataset were changed to match those in Countries dataset (for example, Congo (Kinshasa) -> DR Congo; Congo (Brazzaville) -> Congo).

Taiwan, Greenland, and Kosovo were missing from the Countries list and added with data from Google search. 

West Bank & Gaza and Western Sahara were included in the Covid-19 dataset but not the Countries list. Further research showed difficulty in determining legitimacy of these areas as countries and they were removed due to this reason.

To avoid inflation of Covid-19 cases, Russia is considered an European country despite being an intercontinential nation.

Some abbreviations in the Country list were undo as well to match Power BI's setting.

### Data Modelling
The data model first consists of 2 dataset, Covid-19 and Country, which are connected via countries' name. * *(Many-to-one relationship between Covid-19: Country/Region and Country: Country)* *

(Added image of model)

As best practice, a Date dimension was also added into the model. This dimension was created via **Calculated table** feature in Power BI. Date table was initiated using CALENDARAUTO() which created a list of dates from earliest to latest date in the data model on daily interval. Year, Month, Day columns were then added using time DAX formulas. This Date dimension allows for time intelligence measures and calculations should they become necessary.

The final data model is as below:

(Added image of model)
