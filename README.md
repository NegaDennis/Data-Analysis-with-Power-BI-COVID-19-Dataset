# Data-Analysis-with-Power-BI-COVID-19-Dataset

## Table of Content
1. Introduction
2. Data preparation
3. Data visualization
4. Project Impact and Limitations

## Introduction
The project aims to utilize Power BI as a tool and demonstrate data analysis skills. The dataset used here is the COVID-19 Dataset from [Kaggle](https://www.kaggle.com/datasets/imdevskp/corona-virus-report/data). The dataset offers data pulled from various reliable sources and straightforward features. In this project, I used the ‘covid_19_clean_complete.csv’ file (hereafter will be referred as the Covid-19 dataset). The data file provides information on Covid-19 cases around the world from 22/1/2020 to 27/7/2020.

The project produced a visual report with interactive components in Power BI. The report includes several data visuals that make use of advanced features in Power BI like DAX measures, Bookmarks, and more. Thanks to these features, the report could quickly give a glimpse into how the pandemic is going around the world on both continent- and county-level.

<img width="1750" height="989" alt="full view" src="https://github.com/user-attachments/assets/0a5af1df-7b94-41e4-987f-b013bc096d60" />

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
The data model first consists of 2 dataset, Covid-19 and Country, which are connected via countries' name. *(Many-to-one relationship between Covid-19: Country/Region and Country: Country)*

(Added image of model)

As best practice, a Date dimension was also added into the model. This dimension was created via **Calculated table** feature in Power BI. Date table was initiated using CALENDARAUTO() which created a list of dates from earliest to latest date in the data model on daily interval. Year, Month, Day columns were then added using time DAX formulas. This Date dimension allows for time intelligence measures and calculations should they become necessary.

The final data model is as below:

(Added image of model)

## Data visualization
The project produced a visual report with interactive components.

(Added gifs)


### DAX measures and calculated columns

The value data in Covid-19 dataset are compounded on daily basis. This means that the number of confirmed, active, recovered cases, and deaths are cummulative with time. It renders basic aggregation of Power BI unusable and customer DAX measures were created as solutions.

DAX measures created for the report include:

(Add table)

Additionally, a calculated column named 'Diff confirmed From Previous Day' was also created. This column calculates the difference in confirmed cases between a day and the previous day of each location (location is defined by a unique combination of country, lat, and long). The formula is as below:
````
Diff confirmed From Previous Day = 
VAR CurrentDate = 'covid_19_clean_complete'[Date]
VAR CurrentCountry = 'covid_19_clean_complete'[Country/Region]
VAR CurrentLat = 'covid_19_clean_complete'[Lat]
VAR CurrentLong = 'covid_19_clean_complete'[Long]
VAR PrevValue =
    CALCULATE(
        MAX('covid_19_clean_complete'[Confirmed]),
        FILTER(
            'covid_19_clean_complete',
            'covid_19_clean_complete'[Country/Region] = CurrentCountry &&
            'covid_19_clean_complete'[Lat] = CurrentLat &&
            'covid_19_clean_complete'[Long] = CurrentLong &&
            'covid_19_clean_complete'[Date] = 
                CALCULATE(
                    MAX('covid_19_clean_complete'[Date]),
                    FILTER(
                        'covid_19_clean_complete',
                        'covid_19_clean_complete'[Country/Region] = CurrentCountry &&
                        'covid_19_clean_complete'[Date] < CurrentDate
                    )
                )
        )
    )
RETURN 
IF(ISBLANK(PrevValue), BLANK(), ABS('covid_19_clean_complete'[Confirmed] - PrevValue))
````

### Components in the report

1. Total Confirmed Cases - Card

<img width="590" height="183" alt="total confirmed cases" src="https://github.com/user-attachments/assets/63eee299-e5fd-40c9-9ed8-f113048d51f8" />

It is a straightforward visual which show the total confirmed cases worldwide. It utilizes the DAX measure named ' '.

2. Current status of Covid-19 Cases - Pie Chart

<img width="601" height="382" alt="current status of covid cases" src="https://github.com/user-attachments/assets/368f3228-fa8b-4dea-b3c7-804885da3ed6" />

This visual shows total number of cases which are still active, have recovered, or resulted in death. These 3 categories make up the total confirmed cases.

3. Daily Growth in Confirmed Cases - Line Chart

<img width="601" height="596" alt="daily growth in total confirmed cases" src="https://github.com/user-attachments/assets/92e96e08-e7cd-4a65-ad8a-e99396da2320" />

This visual shows the growth in spread of Covid-19 around the world. It utilizes value from the calculated column 'Diff confirmed From Previous Day'.

4. Map of Covid-19 Cases - Filled Map

<img width="599" height="559" alt="map (continental)" src="https://github.com/user-attachments/assets/4001d451-2c7c-4fb3-a6e4-561cf47d932e" />

This visual provides high-level geographical picture of the pandemic on worldwide level. The color changes in intensity based on the number of active cases in a continent. That value and others in the tooltip are calculated using DAX measures ' '.

5. Top 5 Countries with Most Confirmed Cases - Stacked Bar Chart

<img width="601" height="556" alt="top 5 confirmed" src="https://github.com/user-attachments/assets/6be93af4-7863-45e9-94a5-5a7d448cd401" />

This visual shows the top 5 countries which have the highest number of confirmed Covid-19 cases at the end of the time period. It uses the DAX measures ' '.

6. Top 5 Countries with Highest Recovery Rate - Stacked Bar Chart

<img width="602" height="558" alt="top 5 recovered" src="https://github.com/user-attachments/assets/94b06852-bbf1-4d9d-b4fa-7b357f540172" />

This visual shows the top 5 countries which have highest recovery rate from Covid-19. It uses the DAX measures ' '.

7. Top 5 Countries with Higest Mortality Rate - Stacked Bar Chart

<img width="734" height="580" alt="top 5 mortality" src="https://github.com/user-attachments/assets/d28fc975-7ab4-49c1-946f-bc7859b82fe0" />

This visual shows the top 5 countries which have highest mortality rate from Covid-19. It uses the DAX measures ' '.

8. Bookmarks

<img width="250" height="598" alt="Bookmarks" src="https://github.com/user-attachments/assets/a9939570-a445-495a-b5bb-586db101d65f" />

The bookmarks in this report function almost like slicers. Besides filtering data in other data visuals by location (continent), it also swap out the map visual to its country-level version when a specific continent is chosen. This allows the map to show details in deeper granularity.

9. Dynamic sign-post

https://github.com/user-attachments/assets/dc35a6cd-655a-4a91-8658-f38e55ab8fbb

The sign-post is actually a Card visual which take in the value of the continent being filtered for. The exception occurs when **Worldwide** bookmark is used, which will swap the visual out for a static block of text.

## Project Impact and Limitation

(loren dimsum)
