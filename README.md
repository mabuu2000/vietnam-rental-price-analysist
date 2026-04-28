# Vietnam Residential Rental Market Analysis: End-to-End ETL & Power BI

### Summary:
- This is a E2E Data Analyst project that create a pipeline extracting raw, dirty real estate data from Kaggle. I processed it using python ETL script to delete the critical outliers and visualize the result in an interactive Power BI dashboard.
- The goal here is to give some business intelligence for real estate agencies and investors in the Vietnamese market, especially about market supply, regional pricing and the scaling cost of the properties.

### The script:
The python script "rental_price_etl.ipynb" automates the extraction and also the cleaning of the data. The data in this case has many things that needed to be cleaned, like fake 1 VND listing or 99 billion VND pricing. So this script will some these problems and load them into csv file.

#### Extract:
- I used the kagglehub API to automate the downloading process from the "Vietnam Real Estate Datasets Catalyst" dataset.
- After that, I the zipfile library to unzip the downloaded file and loads it into a pandas dataframe.
#### Transform:
- First, there are rows of missing data in area_m2 and price_million_vnd that I dropped immidiately, since these are critical data that cannot be filled or be missing. Next, there are also missing values in bedrooms, bathrooms, and floors. Because these consist of a huge number of rows, I cannot drop all of them like the prior, so I wrote the pipeline using ".fillna(1).astype(int)" to preserve most of the rows.
- Secondly, the raw location is concatinated from the provinces and the cities, so I wanted to seperate them. But the problem is that in Viet Nam, some big cites are not in provinces and they have their own districts, so I used "str.split" to create 2 hierarchical columns: location_level_1 and location_level_2.
- I also wanted a KPI metric showing price per m2, so I created a standard KPI metric by calculating price_per_m2_vnd, also filtered them beforehand to prevent any divide by 0 error.
- Most importantly, after looking at the scatter plot of this data, it is heavily right-skewed and have some really big outliers, like pricing with 99 billions. So, I calculated the IQR to make a upper bound (Q3 + 1.5 * IQR), this will remove most of those fake outliers. However, since the data is right-skewed, IQR created a negative lowerbound. So I created a lowerbound of 30,000. I also put an upperbound for the "area" too to remove industrial listings.
- Next, brokers frequently spam the same listing many times. So, the pipeline drops duplicates by looking for exact matches across a specific subset of columns: 'location_level_2', 'area_m2', 'bedrooms', 'price_million_vnd'.
#### Load:
- After these are finished, the pipeline export the dataset to a .csv file to be loaded to Power BI.
