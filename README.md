# Electric Vehicles sales analysis

## Table of contents

- [Project Overview](#project-overview)
- [Data Source](#data-source)
- [Tools](#tools)
- [Data Cleaning and Preparation](#data-cleaning-and-preparation)
- [Exploratory Data Analysis](#exploratory-data-analysis)
- [Data Analysis](#data-analysis)
- [Results](#results)
- [Recommendations](#recommendations)
- [Limitations](#limitations)

## Project Overview 

This project shows important insights of the global EV sales data throughout the years, offering data about sales, current number of EVs per country divided by their types (BEV, HEV, PHEV) and charging stations by country and year. The purpose of this is to provide key information about the best EV type investment, best country to invest in EV or charging stations, based on trends and current metrics.

### Data Source

The two datasets ([EV_sales.csv](https://github.com/diegoislasm/Data_projects/blob/main/EV_sales.csv) and [EV_charging_points.csv](https://github.com/diegoislasm/Data_projects/blob/main/EV_charging_points.csv)) used for this analysis were obtained from IEA (2024), Global EV Data Explorer, IEA, Paris https://www.iea.org/data-and-statistics/data-tools/global-ev-data-explorer.

### Tools

- SQL Server - Data cleaning and analysis
- Tableau - Creating dashboard

### Data Cleaning and Preparation

In this phase  the tasks performed were the following:

1. Data loading and data exploration
2. Data cleaning and formatting
3. handling null values

### Exploratory Data Analysis

In the EDA to analyze the sales data the following questions were answered:

- What is the overall trending sales?
- What is the country with more EV sales?
- What is the country with more EVs?
- What is the most popular type of EV?

## Data Analysis

```sql
CREATE VIEW EV_types_distribution AS
SELECT year, 
(FCEV / EV_sales) * 100 AS FCEV_percent, (PHEV / EV_sales) * 100 AS PHEV_percent, (BEV / EV_sales) * 100 AS BEV_percent
FROM (SELECT year, SUM(PHEV_sales) AS  PHEV, SUM(FCEV_sales) AS FCEV, SUM(BEV_sales) AS BEV, (SUM(PHEV_sales) + SUM(FCEV_sales) + SUM(BEV_sales)) AS EV_sales
FROM EV_data_long
GROUP BY year) AS SourceTable
WHERE EV_sales != 0
```

```sql
-- Show number of EVs per charging point by country
SELECT *, 
CASE 
	WHEN Total_EV_stock != 0 AND Total_chargers != 0
	THEN Total_EV_stock / Total_chargers 
	ELSE 0
END
AS EVs_chargers_ratio
FROM
(SELECT region, year, (SUM(FCEV_stock) + SUM(PHEV_stock) + SUM(BEV_stock)) AS Total_EV_stock, (SUM(slow_chargers) + SUM(fast_chargers)) AS Total_chargers
FROM EV_data_long
GROUP BY region, year
) AS cumulated_data
WHERE Total_EV_stock != 0 AND Total_chargers != 0
ORDER BY region, year
```

## Results

The analysis results are summarized as follows:

### Electric Vehicles
- The top 5 countries with highest % of cars being EVs in 2023 were **Norway** (29%), **Iceland** (18%), **Sweden** (11%), **Finland** (8%) and **China** (7%), with a **global average** of 2.97%**
- The top 5 countries with highest percentage of EVs sold of total car sales were **Norway** (93%), **Iceland** (71%), **Sweden** (60%), **Finland** (54%) and **Denmark** (46%), with a **global average** of 18%
- The global EV type sales distribution was 0.1% **FCEV**, 31% **PHEV** and 68.8% **BEV**.
- The countries with the highest EV sales in 2023 are **China** with 8.1 million, **USA** with 1.3 million and **Germany** with 700 thousands.

### Charging stations

- The top 5 countries with more chargers per 100 EVs in 2023 were **South Korea** (36), **Chile** (27), **Netherlands** (21), **Greece** (15) and **China** (12) with the **global average** being 8 chargers.
- Some of the top 10 countries with less chargers per 100 EVs in 2023 include **Norway** (3), **United Kingdom** (3), **Iceland** (4), **United States** (4) and **Germany** (4). 
- The chargers per 100 EV have gone from **23** in 2010 to **10** in 2023.
- The number of EVs and chargers globally have grown at a very similar rate since 2010 through 2023, except between **2019 and 2021** when the **number of EVs increased** at a higher rate than the chargers.

## Recommendations

Based on the analysis, I recommend the following actions:

Although all around the world the EV adoption has shown a clear upward trend, there are some markets and products that could see a bigger adoption therefore these are the recommendations based on that:

- The EV type to focus on to develop would have to be the preferred type of EV which is the **Battery Electric Vehicle (BEV)** by far, having **more than half of the market** for over 10 years, leaving as the runner up the **Plugins Hybrid Electric Vehicles PHEV** with most of the remaining market (**around 30%** in the last years) and the **Fuel Cell Electric Vehicles (FCEV)** with a very small portion of the market with **less that 1%** due to being a new technology.
- The countries where the investments should be thrown at are the ones that have preferred EVs over the **ICE (Internal Combustion Engines or gas cars)** which are **Norway, Iceland, Sweden, Finland, Denmark, Belgium and China.**
- **United States** and **China** are also huge markets to consider, given that despite the USA having **only 10%** of EV adoption in 2023, the States were the **second country** with the highest EV sales in that same year, while **China was first place**. 

Based on the growth in EV sales, the obvious move is to look at the charging stations to recharge this type of vehicles, so these are the recommendations for that:

- Based on the global trend of chargers per 100 EV ratio it's easy to see that EV have been produced at a higher speed compared to the number of chargers to satisfy the demand, considering that after 2010 when the ratio was **20 chargers per 100 EV**, the number of chargers have decreased gradually to only **10 chargers per 100 EV** in 2023, showing unsatisfied demand.
- It is recommended to make investments in the countries with not enough chargers per EV but with a high adoption of EVs, like **New Zealand, Norway, Iceland, USA and Germany** where the chargers per 100 EV ratio is below 5, being 8 the average. So despite the high adoption of EVs in these countries, the chargers developtment is not there yet which creates a big opportunity for success, maybe even better than the EV industry itselt where competition gets harder and harder. 


## Limitations

- The dataset used only included data from 51 countries and covered from 2010 to 2023.
- There were outliers with 0.034 in charging stations that were fixed with zeros.
- The charging stations were divided in slow chargers and fast chargers in the data source but they were joined to simplify the results of this project.
- 

