# HeroesofPy

## Summary
A free-to-play fantasy game known as Heroes of Pymoli is run by a small independent gaming company where players are given the option to purchase in-game items to enhance their player experience. The task of this analysis is to analyze and generate a statisical report that breaks down the in-game purchasing data into insightful information.

-----

## Analysis
* Of the 576 active players, the majority of the player base is 84% male while the remainder is 16% female. 
* The age demographic presented that the age peak falls between ages 20--24 (44.79%). Secondary groups that fall behind are ages between 15--19 (18.58%) and 25--29 (13.37%).
* The age group that spends the most money falls between ages 20--24 with $1114.06 as the total purchase value with an average purchase per individual of $4.32. However, the age group that has the highest average purchase per individual is ages 35--39 with $4.76.

-----

```python
#Dependencies and Setup
import pandas as pd

#File name to load
file = "Resources/purchase_data.csv"

#Read file and store into Pandas df
purchase_data = pd.read_csv(file)
purchase_data.head()
```

## Getting the Player Count
```python
#Get total count of players
unique_names = purchase_data["SN"].nunique()
count = pd.DataFrame({"Player Count":[unique_names]})
count
```

|   | Player Count |
| - | -----------: |
| 0 | 576          |


## Purchase Analysis
```python
#Purchasing analysis
item_count = purchase_data["Item ID"].nunique()
avg_price = purchase_data["Price"].mean()
purchase_count = purchase_data["Purchase ID"].count()
total_revenue = purchase_data["Price"].sum()

summary = pd.DataFrame({"Number of Unique Items":[item_count],
                       "Average Price":[avg_price],
                       "Number of Purchases":[purchase_count],
                       "Total Revenue":[total_revenue]})

summary[["Average Price","Total Revenue"]] = summary[["Average Price","Total Revenue"]].applymap("${:,.2f}".format)
summary
```

|   | Number of Unique Items | Average Price| Number of Purchases | Total Revenue |
| - | ---------------------- | ------------ | ------------------- | ------------- |
| 0 | 179                    | $3.05        | 780                 | $2379.77      |


## Gender Analysis
```python
#Gender Demographics
SN = purchase_data.drop_duplicates(subset="SN")
gender_count = SN["Gender"].value_counts()
gender_percentage = gender_count / SN["Gender"].count() * 100
gender_summary_df = pd.DataFrame({"Total Count": gender_count,
                                 "Percentage of Players": gender_percentage})

gender_summary_df["Percentage of Players"] = gender_summary_df["Percentage of Players"].map("{0:.2f}".format)
gender_summary_df
```

|                   | Total Count | Percentage of Players |
| ----------------: | ----------: | --------------------: |
| Male              | 484         | 84.03                 |
| Female            | 81          | 14.06                 |
| Other / Disclosed | 11          | 1.91                  |

## Purchase and Gender Analysis
```python
gender_purchase_count = purchase_data.groupby("Gender")["Item Name"].count()
gender_avg_purchase_price = purchase_data.groupby("Gender")["Price"].mean()
gender_total_purchase_price = purchase_data.groupby("Gender")["Price"].sum()
gender_avg_purchase_total = gender_total_purchase_price / gender_count

gender_purchase_summary_df = pd.DataFrame({"Purchase Count": gender_purchase_count,
                                       "Average Purchase Price": gender_avg_purchase_price,
                                       "Total Purchase Value": gender_total_purchase_price,
                                       "Avg Total Purchase per Person": gender_avg_purchase_total})

gender_purchase_summary_df[["Average Purchase Price","Total Purchase Value","Avg Total Purchase per Person"]] = gender_purchase_summary_df[["Average Purchase Price","Total Purchase Value","Avg Total Purchase per Person"]].applymap("${:.2f}".format)
gender_purchase_summary_df
```

|                   | Purchase Count | Average Purchase Price | Total Purchase Value | Avg Total Purchase per Person |
| ----------------: | -------------: | ---------------------: | -------------------: | ----------------------------: | 
| Female            | 113            | $3.20                  | $361.94              | $4.47                         |
| Male              | 652            | $3.02                  | $1967.64             | $4.07                         |
| Other / Disclosed | 113            | $3.35                  | $50.19               | $4.56                         |

## Age Demographics
```python
#Age Demographics
#Create Bins for age groups
bins = [0,9,14,19,24,29,34,39,100]
age_groups = ["0-9","10-14","15-19","20-24","25-29","30-34","35-39","40 and over"]

SN["Age Range"] = pd.cut(SN["Age"],bins, labels = age_groups)

age_purchase_count = SN["Age Range"].value_counts()
age_percentage_player = (age_purchase_count / SN["SN"].count()) * 100

age_summary = pd.DataFrame({"Total Count": age_purchase_count,
                           "Percentage of Players": age_percentage_player})

age_summary = age_summary.sort_index()
age_summary["Percentage of Players"] = age_summary["Percentage of Players"].map("{:.2f}%".format)
age_summary
```

|            | Total Count | Percentage of Players |
| -------: | ----------: | --------------------: |
| 0-9         | 484         | 84.03                 |
| 10-14       | 81          | 14.06                 |
| 15-19       | 11          | 1.91                  |
| 40 and over | 11          | 1.91                  |
