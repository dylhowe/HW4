
This project was completed as part of George Washington University's Data Analysis and Visualization course. Based on the criteria below, I did some basic analysis of a video game sales dataset using Pandas.

## Heroes of Pymoli

#### Congratulations! After a lot of hard work in the data munging mines, you've landed a job as Lead Analyst for an independent gaming company. You've been assigned the task of analyzing the data for their most recent fantasy game Heroes of Pymoli.

#### Like many others in its genre, the game is free-to-play, but players are encouraged to purchase optional items that enhance their playing experience. As a first task, the company would like you to generate a report that breaks down the game's purchasing data into meaningful insights.


```python
#import dependencies

import numpy as np
import pandas as pd
```


```python
#read in sales data

sales_df = pd.read_json("purchase_data.json")

sales_df.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Age</th>
      <th>Gender</th>
      <th>Item ID</th>
      <th>Item Name</th>
      <th>Price</th>
      <th>SN</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>38</td>
      <td>Male</td>
      <td>165</td>
      <td>Bone Crushing Silver Skewer</td>
      <td>3.37</td>
      <td>Aelalis34</td>
    </tr>
    <tr>
      <th>1</th>
      <td>21</td>
      <td>Male</td>
      <td>119</td>
      <td>Stormbringer, Dark Blade of Ending Misery</td>
      <td>2.32</td>
      <td>Eolo46</td>
    </tr>
    <tr>
      <th>2</th>
      <td>34</td>
      <td>Male</td>
      <td>174</td>
      <td>Primitive Blade</td>
      <td>2.46</td>
      <td>Assastnya25</td>
    </tr>
    <tr>
      <th>3</th>
      <td>21</td>
      <td>Male</td>
      <td>92</td>
      <td>Final Critic</td>
      <td>1.36</td>
      <td>Pheusrical25</td>
    </tr>
    <tr>
      <th>4</th>
      <td>23</td>
      <td>Male</td>
      <td>63</td>
      <td>Stormfury Mace</td>
      <td>1.27</td>
      <td>Aela59</td>
    </tr>
  </tbody>
</table>
</div>



### Total Number of Unique Players


```python
print("Total Players: " + str(len(sales_df["SN"].value_counts())))
```

    Total Players: 573
    

### Purchasing Analysis (Total)


```python
#Count of unique items
print("Number of Unique Items: " + str(len(sales_df["Item ID"].value_counts())))

#average purchase price
avg_price = sales_df["Price"].mean()
print("Average Purchase Price: $" + str(avg_price))

#Total number of purchases
print("Total Purchases: " + str(len(sales_df)))

#Total revenue
print("Total Revenue: $" +str(sales_df["Price"].sum()))
```

    Number of Unique Items: 183
    Average Purchase Price: $2.931192307692303
    Total Purchases: 780
    Total Revenue: $2286.33
    

### Gender Demographics


```python
#create a dataframe to display user gender breakdown

unique_users = sales_df.drop_duplicates(["SN"])

gender_counts = unique_users["Gender"].value_counts()

gendered_df = pd.DataFrame(gender_counts)

#divide gender counts by total users to find percentage of userbase
gendered_df["Percent of Userbase"] = (gendered_df/(len(sales_df["SN"].value_counts())))*100

print("Gender Demographics")
gendered_df.head()
```

    Gender Demographics
    




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Gender</th>
      <th>Percent of Userbase</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Male</th>
      <td>465</td>
      <td>81.151832</td>
    </tr>
    <tr>
      <th>Female</th>
      <td>100</td>
      <td>17.452007</td>
    </tr>
    <tr>
      <th>Other / Non-Disclosed</th>
      <td>8</td>
      <td>1.396161</td>
    </tr>
  </tbody>
</table>
</div>



### Purchasing Analysis by Gender


```python
#group sales data by gender
gendergroup = sales_df.groupby("Gender")

#calculate revenues
gendered_revenues = gendergroup["Price"].sum()

#calculate average purchase price
gendered_avgprice = gendergroup["Price"].mean()

#calculate number of purchases
gendered_counts = sales_df["Gender"].value_counts()

#aggregate into dataframe
gendered_summary = pd.DataFrame({"Total Purchase Value": gendered_revenues,
                                 "Average Purchase Price": gendered_avgprice, 
                                 "Purchase Count": gendered_counts})
print("Purchasing Analysis by Gender")
gendered_summary
```

    Purchasing Analysis by Gender
    




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Average Purchase Price</th>
      <th>Purchase Count</th>
      <th>Total Purchase Value</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Female</th>
      <td>2.815515</td>
      <td>136</td>
      <td>382.91</td>
    </tr>
    <tr>
      <th>Male</th>
      <td>2.950521</td>
      <td>633</td>
      <td>1867.68</td>
    </tr>
    <tr>
      <th>Other / Non-Disclosed</th>
      <td>3.249091</td>
      <td>11</td>
      <td>35.74</td>
    </tr>
  </tbody>
</table>
</div>



### Age Demographics


```python
#create bins for age groups
bins = [0,10,15,20,25,30,35,40,45]
names = ["Under 10", "10 to 15", "15 to 20", "20 to 25", "25 to 30", "30 to 35", "35 to 40", "40 to 45"]

#cut sales data into age groups
sales_df["Age Group"] = pd.cut(sales_df["Age"], bins, labels=names)

#group sales data into age bins
age_groups = sales_df.groupby("Age Group")

#calculate purchase count, purchase price, and total purchase values
age_summary = pd.DataFrame({"Purchase Count": age_groups["Age"].count(),
                            "Average Purchase Price": age_groups["Price"].mean(),
                            "Total Purchase Value": age_groups["Price"].sum()})

print("Age Demographics")
age_summary
```

    Age Demographics
    




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Average Purchase Price</th>
      <th>Purchase Count</th>
      <th>Total Purchase Value</th>
    </tr>
    <tr>
      <th>Age Group</th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Under 10</th>
      <td>3.019375</td>
      <td>32</td>
      <td>96.62</td>
    </tr>
    <tr>
      <th>10 to 15</th>
      <td>2.873718</td>
      <td>78</td>
      <td>224.15</td>
    </tr>
    <tr>
      <th>15 to 20</th>
      <td>2.873587</td>
      <td>184</td>
      <td>528.74</td>
    </tr>
    <tr>
      <th>20 to 25</th>
      <td>2.959377</td>
      <td>305</td>
      <td>902.61</td>
    </tr>
    <tr>
      <th>25 to 30</th>
      <td>2.892368</td>
      <td>76</td>
      <td>219.82</td>
    </tr>
    <tr>
      <th>30 to 35</th>
      <td>3.073448</td>
      <td>58</td>
      <td>178.26</td>
    </tr>
    <tr>
      <th>35 to 40</th>
      <td>2.897500</td>
      <td>44</td>
      <td>127.49</td>
    </tr>
    <tr>
      <th>40 to 45</th>
      <td>2.880000</td>
      <td>3</td>
      <td>8.64</td>
    </tr>
  </tbody>
</table>
</div>



### Top Spenders


```python
#group sales by user
usersums = sales_df.groupby("SN")

#calculate fields
top_summary = pd.DataFrame({"Purchase Count": usersums["Price"].count(),
                            "Average Purchase Price": usersums["Price"].mean(),
                            "Total Purchase Value": usersums["Price"].sum()})

#sort by total purchases
top_summary = top_summary.sort_values(by="Total Purchase Value",ascending=False)

print("Top Spenders")
top_summary.head()
```

    Top Spenders
    




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Average Purchase Price</th>
      <th>Purchase Count</th>
      <th>Total Purchase Value</th>
    </tr>
    <tr>
      <th>SN</th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Undirrala66</th>
      <td>3.412000</td>
      <td>5</td>
      <td>17.06</td>
    </tr>
    <tr>
      <th>Saedue76</th>
      <td>3.390000</td>
      <td>4</td>
      <td>13.56</td>
    </tr>
    <tr>
      <th>Mindimnya67</th>
      <td>3.185000</td>
      <td>4</td>
      <td>12.74</td>
    </tr>
    <tr>
      <th>Haellysu29</th>
      <td>4.243333</td>
      <td>3</td>
      <td>12.73</td>
    </tr>
    <tr>
      <th>Eoda93</th>
      <td>3.860000</td>
      <td>3</td>
      <td>11.58</td>
    </tr>
  </tbody>
</table>
</div>



### Most Popular Items


```python
#group sales data by item
items = sales_df.groupby("Item ID")

#calculate fields
item_summary = pd.DataFrame({"Purchase Count": items["Price"].count(),
                             "Item Name": items["Item Name"].max(),
                             "Item Price": items["Price"].max(),
                             "Total Purchase Value": items["Price"].sum()})

#sort by purchase count
item_summary = item_summary.sort_values(by="Purchase Count", ascending=False)

print("Most Popular Items")
item_summary.head()
```

    Most Popular Items
    




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Item Name</th>
      <th>Item Price</th>
      <th>Purchase Count</th>
      <th>Total Purchase Value</th>
    </tr>
    <tr>
      <th>Item ID</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>39</th>
      <td>Betrayal, Whisper of Grieving Widows</td>
      <td>2.35</td>
      <td>11</td>
      <td>25.85</td>
    </tr>
    <tr>
      <th>84</th>
      <td>Arcane Gem</td>
      <td>2.23</td>
      <td>11</td>
      <td>24.53</td>
    </tr>
    <tr>
      <th>31</th>
      <td>Trickster</td>
      <td>2.07</td>
      <td>9</td>
      <td>18.63</td>
    </tr>
    <tr>
      <th>175</th>
      <td>Woeful Adamantite Claymore</td>
      <td>1.24</td>
      <td>9</td>
      <td>11.16</td>
    </tr>
    <tr>
      <th>13</th>
      <td>Serenity</td>
      <td>1.49</td>
      <td>9</td>
      <td>13.41</td>
    </tr>
  </tbody>
</table>
</div>



### Most Profitable Items


```python
#sort previous summary by total revenue

print("Most Profitable Items")
item_summary.sort_values(by="Total Purchase Value", ascending=False).head()
```

    Most Profitable Items
    




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Item Name</th>
      <th>Item Price</th>
      <th>Purchase Count</th>
      <th>Total Purchase Value</th>
    </tr>
    <tr>
      <th>Item ID</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>34</th>
      <td>Retribution Axe</td>
      <td>4.14</td>
      <td>9</td>
      <td>37.26</td>
    </tr>
    <tr>
      <th>115</th>
      <td>Spectral Diamond Doomblade</td>
      <td>4.25</td>
      <td>7</td>
      <td>29.75</td>
    </tr>
    <tr>
      <th>32</th>
      <td>Orenmir</td>
      <td>4.95</td>
      <td>6</td>
      <td>29.70</td>
    </tr>
    <tr>
      <th>103</th>
      <td>Singed Scalpel</td>
      <td>4.87</td>
      <td>6</td>
      <td>29.22</td>
    </tr>
    <tr>
      <th>107</th>
      <td>Splitter, Foe Of Subtlety</td>
      <td>3.61</td>
      <td>8</td>
      <td>28.88</td>
    </tr>
  </tbody>
</table>
</div>


