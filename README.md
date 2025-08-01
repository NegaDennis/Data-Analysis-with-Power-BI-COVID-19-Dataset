# Data-Analytics-with-Power-BI-COVID-19-Dataset

## Table of Content

[1. Introduction](https://github.com/NegaDennis/Data-Analysis-with-Power-BI-COVID-19-Dataset?tab=readme-ov-file#introduction)

[2. Data preparation](https://github.com/NegaDennis/Data-Analysis-with-Power-BI-COVID-19-Dataset?tab=readme-ov-file#data-preparation)

[3. Report Development](https://github.com/NegaDennis/Data-Analysis-with-Power-BI-COVID-19-Dataset?tab=readme-ov-file#data-visualization)

[4. Project Impact and Limitations](https://github.com/NegaDennis/Data-Analysis-with-Power-BI-COVID-19-Dataset?tab=readme-ov-file#project-impact-and-limitation)

## 1. Introduction
The project aims to utilize Power BI as a tool and demonstrate data analysis skills. The dataset used here is the COVID-19 Dataset from [Kaggle](https://www.kaggle.com/datasets/imdevskp/corona-virus-report/data). The dataset offers data pulled from various reliable sources and straightforward features. In this project, I used the ‘covid_19_clean_complete.csv’ file (hereafter will be referred as the Covid-19 dataset). The data file provides information on Covid-19 cases around the world from 22/1/2020 to 27/7/2020.

The project produced a visual report with interactive components in Power BI. The report includes several data visuals that make use of advanced features in Power BI like DAX measures, Bookmarks, and more. Thanks to these features, the report could quickly give a glimpse into how the pandemic is going around the world on both continent- and county-level.


https://github.com/user-attachments/assets/b9f9abd8-6ce9-4096-a5c0-dbc319a2394f

## 2. Data preparation
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

<img width="611" height="325" alt="model 1" src="https://github.com/user-attachments/assets/9321e244-796c-4ff6-9e12-93118d51e319" />



As best practice, a Date dimension was also added into the model. This dimension was created via **Calculated table** feature in Power BI. Date table was initiated using CALENDARAUTO() which created a list of dates from earliest to latest date in the data model on daily interval. Year, Month, Day columns were then added using time DAX formulas. This Date dimension allows for time intelligence measures and calculations should they become necessary.

The final data model is as below:

<img width="982" height="388" alt="model 2" src="https://github.com/user-attachments/assets/e0654883-c5bb-4090-b6ad-42186f96c8eb" />



## 3. Report Development

### DAX measures and calculated columns

The value data in Covid-19 dataset are compounded on daily basis. This means that the number of confirmed, active, recovered cases, and deaths are cummulative with time. It renders basic aggregation of Power BI unusable and customer DAX measures were created as solutions.

DAX measures created for the report include:

|DAX measure|Usage|Formula components|
|---|---|---|
|Latest Date per Location|Returns the last date of a location, defined by value of latitude and longtitude|Functions: CALCULATE, MAX, ALLEXCEPT|
|Latest Date per Country|Returns the last date of a country|Functions: CALCULATE, MAX, ALLEXCEPT|
|Confirmed/Active/Recovered/Death cases|Returns total cases with confirmed/active/recovered/death status on country level|Functions: CALCULATE, SUM, FILTER; Reference: Latest Date per Location|
|(All) Confirmed/Active/Recovered/Death cases|Returns total cases with confirmed/active/recovered/death status on continent level|Functions: CALCULATE, SUM, FILTER; Reference: Latest Date per Country|
|Recovery Rate|Returns the recovery rate of a location|Functions: DIVIDE; Reference: Recovered cases, Confirmed cases|
|Mortality Rate|Returns the mortality rate of a location|Functions: DIVIDE; Reference: Death cases, Confirmed cases|


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

It is a straightforward visual which show the total confirmed cases worldwide. It utilizes the DAX measure ``(All) Confirmed cases``.

2. Current status of Covid-19 Cases - Pie Chart

<img width="601" height="382" alt="current status of covid cases" src="https://github.com/user-attachments/assets/368f3228-fa8b-4dea-b3c7-804885da3ed6" />

This visual shows total number of cases which are still active, have recovered, or resulted in death. These 3 categories make up the total confirmed cases. This visual uses the following DAX measures ``(All) Active/Recovered/Death cases``.

3. Daily Growth in Confirmed Cases - Line Chart

<img width="601" height="596" alt="daily growth in total confirmed cases" src="https://github.com/user-attachments/assets/92e96e08-e7cd-4a65-ad8a-e99396da2320" />

This visual shows the growth in spread of Covid-19 around the world. It utilizes value from the calculated column `Diff confirmed From Previous Day` and basic aggregation `SUM`.

4. Map of Covid-19 Cases - Filled Map

<img width="599" height="559" alt="map (continental)" src="https://github.com/user-attachments/assets/4001d451-2c7c-4fb3-a6e4-561cf47d932e" />

This visual provides high-level geographical picture of the pandemic on worldwide level. That visual uses the following DAX measures ``(All) Confirmed/Active/Recovered/Death cases``. The map gradient coloring is based on value calculated from ``(All) Active cases``.

*(Note: On its country-level version, the calculations will make use of location-level DAX measures instead)*

5. Top 5 Countries with Most Confirmed Cases - Stacked Bar Chart

<img width="602" height="558" alt="top 5 confirmed" src="https://github.com/user-attachments/assets/6be93af4-7863-45e9-94a5-5a7d448cd401" />

This visual shows the top 5 countries which have the highest number of confirmed Covid-19 cases at the end of the time period. It uses the DAX measures `Confirmed cases` and Top N advanced filtering.

6. Top 5 Countries with Highest Recovery Rate - Stacked Bar Chart

<img width="602" height="558" alt="top 5 recovered" src="https://github.com/user-attachments/assets/94b06852-bbf1-4d9d-b4fa-7b357f540172" />

This visual shows the top 5 countries which have highest recovery rate from Covid-19. It uses the DAX measures `Recovery rate` and Top N advanced filtering.

7. Top 5 Countries with Higest Mortality Rate - Stacked Bar Chart

<img width="510" height="477" alt="top 5 mortality" src="https://github.com/user-attachments/assets/0be6c97f-1481-4772-b6df-2de908724833" />

This visual shows the top 5 countries which have highest mortality rate from Covid-19. It uses the DAX measures `Mortality rate` and Top N advanced filtering.

8. Bookmarks

<img width="250" height="598" alt="Bookmarks" src="https://github.com/user-attachments/assets/a9939570-a445-495a-b5bb-586db101d65f" />

The bookmarks in this report function almost like slicers. Besides filtering data in other data visuals by location (continent), it also swap out the map visual to its country-level version when a specific continent is chosen. This allows the map to show details in deeper granularity.

9. Dynamic sign-post

https://github.com/user-attachments/assets/dc35a6cd-655a-4a91-8658-f38e55ab8fbb

The sign-post is actually a Card visual which take in the value of the continent being filtered for. The exception occurs when **Worldwide** bookmark is used, which will swap the visual out for a static block of text. The sign-post's sole purpose is to inform viewer which continental filter is being applied on the visual report.

## 4. Project Impact and Limitation

The project succeeded in prodiving a visual report regarding the Covid-19 pandemic around the world from 22/1/2020 to 27/7/2020. The report provides a quick glimpse into the intensity of the pandemic as well as how well different parts of the world is handling it. It also provides interactive components which drill down data into country-level.

As one can observed previously, there is paginated report 'button' but no paginated report was shown.

<img width="232" height="106" alt="paginated report" src="https://github.com/user-attachments/assets/dfa90d59-824e-40af-9487-5aee3091e9ab" />

This is only the limitation of basic subscription to Power BI. With Premium version, a paginated report, which allow users to filter data by location and time period, would be added. Such feature would enable users to quickly isolate the chunk of data they want and export them into a data file with type of choice.

Another limitation comes from data granularity of the original dataset. As it uses latitude and longtitude data as the lower level to country-level, it was difficult to display and manipulate data in a meaningful way. With better annotation (e.g., city name instead of coordinates), even more detailed analysis and visualization could be available.
