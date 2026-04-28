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
- I also wanted a KPI metric showing price per m², so I created a standard KPI metric by calculating price_per_m2_vnd, also filtered them beforehand to prevent any divide by 0 error.
- Most importantly, after looking at the scatter plot of this data, it is heavily right-skewed and have some really big outliers, like pricing with 99 billions. So, I calculated the IQR to make a upper bound (Q3 + 1.5 * IQR), this will remove most of those fake outliers. However, since the data is right-skewed, IQR created a negative lowerbound. So I created a lowerbound of 30,000. I also put an upperbound for the "area" too to remove industrial listings.
- Next, brokers frequently spam the same listing many times. So, the pipeline drops duplicates by looking for exact matches across a specific subset of columns: 'location_level_2', 'area_m2', 'bedrooms', 'price_million_vnd'.
#### Load:
- After these are finished, the pipeline exports the dataset to a .csv file to be loaded to Power BI.

### Business Questions:
<img width="2232" height="1265" alt="image" src="https://github.com/user-attachments/assets/a6b83f80-9ced-4d69-99df-40bb1c8e680d" />

There are some business questions or problems I wanted to solve. They are:
- Which districts command the highest rental premiums?
- What is the financial impact of street-facing frontage versus alleyway locations?
- What property configurations dominate market supply and how does bedroom count influence pricing?
- How does the price per square meter behave as total property area increases?

### Analysis, Strategic Solutions:
1. Which districts command the highest rental premiums?
<img width="1256" height="713" alt="image" src="https://github.com/user-attachments/assets/7f113667-3d7c-40af-99f6-93ea22d8128f" />

- Could be obvious, but the central and historical urban districts in HCM and Hanoi have the highest valuations, this could be from the proximity to major commercial hubs, which drives the value up. This also explains the Hoi An situation. Quận 5 leads the market at 0.51M VND/m², followed closely by Hoàn Kiếm (Hà Nội) at 0.45M VND/m² and Quận 10 (HCM) at 0.42M VND/m².
- For high capital investors, they can develop smaller luxury apartments in District 5 or Hoan Kiem to maximize the return on those expensive square meters. For value investors, the nearby districts or cities in those provinces where the price is close to those premium zones can be targeted. This can help them secure a lower acquisition cost while still benefiting from the central hubs.

2. What is the financial impact of street-facing frontage versus alleyway locations?
<img width="1260" height="690" alt="image" src="https://github.com/user-attachments/assets/1ff47fe8-35b5-48f9-bfa1-e550740429a5" />

- The value of the street-facing frontage is much higher than the alleyway, this trend is pretty much consistent. In big cities such as Hanoi, the cost of a rental unit facing the street is 0.31M VND/m² and the cost of a rental unit in an alley is 0.25M VND/m². Similarly, Ho Chi Minh's streetfacing frontage costs 0.27M VND/m² vs 0.18M VND/m². So for commercial rentals, the pricing has to be justified here for the availability.
- But for residentials, the alleyway is much more affordable. This means that investors can rent these properties and renovate them, which can attract customers to rent them at a higher price than the initial rental price.

3. What property configurations dominate market supply, and how does bedroom count influence pricing?
- The market is heavily leaning towards the smaller units, with 1 bedroom properties making up the majority of listings (1,191 units). Also, the pricing trend is pretty obvious for standard residential units (1-4 bedrooms).
- If investors want fast liquidity, they should prioritize 1-2 bedroom units, since the data shows that this is where the market demand is.

4. How does the price per square meter behave as total property area increases?
<img width="1269" height="690" alt="image" src="https://github.com/user-attachments/assets/e4178684-e813-4c2e-b780-170e9dd39f6b" />

- From this chart, we can see that most properties under 200 m² have a higher cost per m². When the area gets bigger, especially above 300 m², the price drops to below 300,000 VND/m². This is also clearly seen in the trend line.
- To maximize revenue, instead of renting out big properties like 400 m² to a single tenant for a lower rate, the investors should divide them into smaller, modern apartments. From this chart, the optimal seems to be around the average line of 110-120 m² to raise the price per m² up.
