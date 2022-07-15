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
#Purchasing analysis with gender
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

|             | Total Count | Percentage of Players |
| ----------: | ----------: | --------------------: |
| 0-9         | 17          | 2.59%                 |
| 10-14       | 22          | 3.82%                 |
| 15-19       | 107         | 18.58%                |
| 20-24       | 258         | 44.79%                |
| 25-29       | 77          | 13.37%                |
| 30-34       | 52          | 9.03%                 |
| 35-39       | 31          | 5.38%                 |
| 40 and over | 12          | 2.08%                 |

## Purchase and Age Analysis
```python
#Purchasing analysis with Age
purchase_data["Age Ranges"] = pd.cut(purchase_data["Age"], bins, labels = age_groups)
purchase_age_count = purchase_data["Age Ranges"].value_counts()

avg_age_price = purchase_data.groupby("Age Ranges")["Price"].mean()
total_age_purchase = purchase_data.groupby("Age Ranges")["Price"].sum()
avg_age_purchase = total_age_purchase / age_purchase_count

purchase_age_summary = pd.DataFrame({"Purchase Count": purchase_age_count,
                                    "Average Purchase Price": avg_age_price,
                                    "Total Purchase Value": total_age_purchase,
                                    "Avg Total Purchase per Person": avg_age_purchase})

purchase_age_summary[["Average Purchase Price","Total Purchase Value","Avg Total Purchase per Person"]] = purchase_age_summary[["Average Purchase Price","Total Purchase Value","Avg Total Purchase per Person"]].applymap("${:.2f}".format)
purchase_age_summary = purchase_age_summary.rename_axis("Age Ranges")
purchase_age_summary
```

|             | Total Count | Average Purchase Price | Total Purchase Value | Avg Total Purchase per Person |  
| ----------: | ----------: | ---------------------: | -------------------: | ----------------------------: |
| 0-9         | 23          | $3.35                  | $77.13               | $4.54                         |
| 10-14       | 28          | $2.96                  | $82.78               | $3.76                         |
| 15-19       | 136         | $3.04                  | $412.89              | $3.86                         |
| 20-24       | 365         | $3.05                  | $1114.06             | $4.32                         |
| 25-29       | 101         | $2.90                  | $293.00              | $3.81                         |
| 30-34       | 73          | $2.93                  | $214.00              | $4.12                         |
| 35-39       | 41          | $3.60                  | $147.67              | $4.76                         |
| 40 and over | 13          | $2.94                  | $38.24               | $3.19                         |

## Players Purchase Count
```python
#Who Spends the most
user_purchase_count = purchase_data.groupby("SN")["Price"].count()
user_purchase_price = purchase_data.groupby("SN")["Price"].mean()
user_purchase_total = purchase_data.groupby("SN")["Price"].sum()

user_summary = pd.DataFrame({"Purchase Count": user_purchase_count,
                            "Average Purchase Price": user_purchase_price,
                            "Total Purchase per Person": user_purchase_total})

user_summary = user_summary.sort_values(by = "Total Purchase per Person", ascending = False)
user_summary[["Average Purchase Price","Total Purchase per Person"]] = user_summary[["Average Purchase Price","Total Purchase per Person"]].applymap("${:.2f}".format)
user_summary
```

|             | Purchase Count | Average Purchase Price | Total Purchase per Person |
| ----------: | -------------: | ---------------------: | ------------------------: |
| SN          |	               |                        |	                          |
| Lisosia93   |	5              | $3.79                  |	$18.96                    |
| Idastidru52 | 4              | $3.86                  | $15.45                    |
| Chamjask73  |	3              |	$4.61                 |	$13.83                    |
| Iral74      | 4              | $3.40                  | $13.62                    |
| Iskadarya95 | 3              | $4.37                  | $13.10                    |
| ...         | ...            | ...                    | ...                       |
| Ililsasya43 | 1              | $1.02                  | $1.02                     |
| Irilis75    | 1              | $1.02                  | $1.02                     |
| Aidai61     | 1              | $1.01                  | $1.01                     |
| Chanirra79  | 1              | $1.01                  | $1.01                     |
| Alo38       | 1              | $1.00                  | $1.00                     |

## Most Popular Item Choices
```python
#Most popular item purchases
most_purchased = purchase_data.groupby(["Item ID","Item Name"])["Price"].count()
popular_item = purchase_data.groupby(["Item ID","Item Name"])["Price"].sum()

item_price = popular_item / most_purchased

item_summary = pd.DataFrame({"Purchase Count": most_purchased,
                            "Item Price": item_price,
                            "Total Purchase Value": popular_item})

item_summary = item_summary.sort_values(["Purchase Count"], ascending = False)
item_summary[["Item Price", "Total Purchase Value"]] = item_summary[["Item Price", "Total Purchase Value"]].applymap("${:.2f}".format)
item_summary.head()
```

|         |                                              | Purchase Count | Item Price | Total Purchase Value |
| ------: | -------------------------------------------: | -------------: | ---------: | -------------------: |
| Item ID | Item Name                                    |                |            |                      |  
| 92      | Final Critic                                 | 13             | $4.61      | $59.99               |
| 178     |	Oathbreaker, Last Hope of the Breaking Storm | 12             | $4.23      | $50.76               |
| 145     |	Fiery Glass Crusader                         | 9	            | $4.58      | $41.22               |
| 132	    | Persuasion                                   | 9              |	$3.22	     | $28.99               |
| 108	    | Extraction, Quickblade Of Trembling Hands    | 9              | $3.53      | $31.77               |

## Most Profitable items
```python
#Most profitable items
most_profitable = item_summary.sort_values(["Total Purchase Value"], ascending = False)
most_profitable.head(10)
```

|           |                                    | Purchase Count | Item Price | Total Purchase Value |
| --------: | ---------------------------------: | -------------: | ---------: | -------------------: |
| Item ID   | Item Name                          |                |            |                      |  
| 63        | Stormfury Mace                     | 2              | $4.99      | $9.98                |
| 29        |	Chaos, Ender of the End            | 5              | $1.98      | $9.90                |
| 173       |	Stormfury Longsword                | 2	            | $4.93      | $9.86                |
| 38	      | The Void, Vengeance of Dark Magic  | 4              |	$2.37	     | $9.48                |
| 143	      | Frenzied Scimitar                  | 6              | $1.56      | $9.36                |
| 7         | Thorn, Satchel of Dark Souls       | 7              | $1.33      | $9.31                |
| 18        |	Torchlight, Bond of Storms         | 2              | $4.65      | $9.30                |
| 129       |	Fate, Vengeance of Eternal Justice | 6	            | $1.54      | $9.24                |
| 166	      | Thirsty Iron Reaver                | 3              |	$3.07	     | $9.21                |
| 123	      | Twilight's Carver                  | 4              | $2.28      | $9.12                |
