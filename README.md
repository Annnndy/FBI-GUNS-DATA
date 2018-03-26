
# Project: A Survey of U.S. Civilian Firearms Transactions

## Table of Contents
<ul>
<li><a href="#intro">Introduction</a></li>
<li><a href="#wrangling">Data Wrangling</a></li>
<li><a href="#eda">Exploratory Data Analysis</a></li>
<li><a href="#conclusions">Conclusions</a></li>
</ul>

<a id='intro'></a>
## Introduction
The U.S. gun control has always been criticized.
In fact, the U.S. government has made a lot of efforts to this end. The most significant one is the NICS system. Since its operation in 1998, NICS has recorded a large number of gun movements Data, it is mainly through the database system to verify the purchaser's permissions. Although NICS has so far failed to fully cover the civilian firearms business, he has also collected a large amount of data and is quite representative of the best data set used to study the state of firearms in the United States.

Now we have the data of the NICS system in the United States over the years. The data cover most of the firearms transactions in the United States. We have recorded in detail the sale and purchase of all kinds of firearms, leasing and pawn.

What will we get from this information? Has he, to some extent, reflected the state of the U.S. gun market? Pistol sales will be better than the spear? In fact, we do not know, but the data will tell us everything, is not it? Let's wait and see!


```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import matplotlib.cm as cm
import seaborn as sns
import itertools
% matplotlib inline
```

<a id='wrangling'></a>
## Data Wrangling

load csv file(`./data/nics-firearm-background-checks.csv`) and have a look!

- have a glance of data columns
- check the data type in every column
- check NAN value 
- check the duplicates


```python
# Load data and print out a few lines.
df = pd.read_csv('./data/nics-firearm-background-checks.csv')
df.head()
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
      <th>month</th>
      <th>state</th>
      <th>permit</th>
      <th>permit_recheck</th>
      <th>handgun</th>
      <th>long_gun</th>
      <th>other</th>
      <th>multiple</th>
      <th>admin</th>
      <th>prepawn_handgun</th>
      <th>...</th>
      <th>returned_other</th>
      <th>rentals_handgun</th>
      <th>rentals_long_gun</th>
      <th>private_sale_handgun</th>
      <th>private_sale_long_gun</th>
      <th>private_sale_other</th>
      <th>return_to_seller_handgun</th>
      <th>return_to_seller_long_gun</th>
      <th>return_to_seller_other</th>
      <th>totals</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2018-01</td>
      <td>Alabama</td>
      <td>23930.0</td>
      <td>0.0</td>
      <td>5692.0</td>
      <td>6199.0</td>
      <td>276.0</td>
      <td>288</td>
      <td>0.0</td>
      <td>12.0</td>
      <td>...</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>22.0</td>
      <td>34.0</td>
      <td>3.0</td>
      <td>2.0</td>
      <td>1.0</td>
      <td>1.0</td>
      <td>38477</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2018-01</td>
      <td>Alaska</td>
      <td>207.0</td>
      <td>6.0</td>
      <td>1844.0</td>
      <td>1621.0</td>
      <td>249.0</td>
      <td>137</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>...</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>13.0</td>
      <td>20.0</td>
      <td>5.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>4429</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2018-01</td>
      <td>Arizona</td>
      <td>5686.0</td>
      <td>413.0</td>
      <td>11659.0</td>
      <td>6992.0</td>
      <td>1074.0</td>
      <td>531</td>
      <td>0.0</td>
      <td>10.0</td>
      <td>...</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>30.0</td>
      <td>14.0</td>
      <td>9.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>28627</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2018-01</td>
      <td>Arkansas</td>
      <td>4162.0</td>
      <td>555.0</td>
      <td>4935.0</td>
      <td>5061.0</td>
      <td>232.0</td>
      <td>323</td>
      <td>37.0</td>
      <td>7.0</td>
      <td>...</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>13.0</td>
      <td>13.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>17483</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2018-01</td>
      <td>California</td>
      <td>55292.0</td>
      <td>0.0</td>
      <td>37335.0</td>
      <td>22919.0</td>
      <td>3495.0</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>...</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>119910</td>
    </tr>
  </tbody>
</table>
<p>5 rows × 27 columns</p>
</div>




```python
df.info()
```

    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 12705 entries, 0 to 12704
    Data columns (total 27 columns):
    month                        12705 non-null object
    state                        12705 non-null object
    permit                       12681 non-null float64
    permit_recheck               1320 non-null float64
    handgun                      12685 non-null float64
    long_gun                     12686 non-null float64
    other                        5720 non-null float64
    multiple                     12705 non-null int64
    admin                        12682 non-null float64
    prepawn_handgun              10762 non-null float64
    prepawn_long_gun             10760 non-null float64
    prepawn_other                5335 non-null float64
    redemption_handgun           10765 non-null float64
    redemption_long_gun          10764 non-null float64
    redemption_other             5335 non-null float64
    returned_handgun             2420 non-null float64
    returned_long_gun            2365 non-null float64
    returned_other               2035 non-null float64
    rentals_handgun              1210 non-null float64
    rentals_long_gun             1045 non-null float64
    private_sale_handgun         2970 non-null float64
    private_sale_long_gun        2970 non-null float64
    private_sale_other           2970 non-null float64
    return_to_seller_handgun     2695 non-null float64
    return_to_seller_long_gun    2970 non-null float64
    return_to_seller_other       2475 non-null float64
    totals                       12705 non-null int64
    dtypes: float64(23), int64(2), object(2)
    memory usage: 2.6+ MB



```python
# how many rows contains nan value
df.isnull().T.any().sum()
```




    11715




```python
# how many duplicates in data set
df.duplicated().sum()
```




    0



### Data Cleaning (Replace this with more specific notes!)

#### Problems and solutions in data set
1. Found the data set has lots of null values, This is not normal. Because some columns are not appended when this plan satrted. so fill them with 0 is no problem. 
2. The dtype of counts of guns is float, I'll trans them to integer.
3. The month contains year and month, Just split it to 2 ineteger number to show year and month.


```python
# fill nan values with 0
df.fillna(0, inplace=True)
```


```python
# change the dtype from float to int
columns_float = df.columns[2:-1]
for c in columns_float:
    df[c] = df[c].apply(lambda x:int(x))
```


```python
# split month column to 2 column : year and month
# such as ：'2018-01' to 2018, 1
df['year_month'] = df['month']
df['year'] = df['month'].apply(lambda x: int(x.split("-")[0]))
df['month'] = df['month'].apply(lambda x: int(x.split("-")[1]))
```


```python
df.head()
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
      <th>month</th>
      <th>state</th>
      <th>permit</th>
      <th>permit_recheck</th>
      <th>handgun</th>
      <th>long_gun</th>
      <th>other</th>
      <th>multiple</th>
      <th>admin</th>
      <th>prepawn_handgun</th>
      <th>...</th>
      <th>rentals_long_gun</th>
      <th>private_sale_handgun</th>
      <th>private_sale_long_gun</th>
      <th>private_sale_other</th>
      <th>return_to_seller_handgun</th>
      <th>return_to_seller_long_gun</th>
      <th>return_to_seller_other</th>
      <th>totals</th>
      <th>year_month</th>
      <th>year</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1</td>
      <td>Alabama</td>
      <td>23930</td>
      <td>0</td>
      <td>5692</td>
      <td>6199</td>
      <td>276</td>
      <td>288</td>
      <td>0</td>
      <td>12</td>
      <td>...</td>
      <td>0</td>
      <td>22</td>
      <td>34</td>
      <td>3</td>
      <td>2</td>
      <td>1</td>
      <td>1</td>
      <td>38477</td>
      <td>2018-01</td>
      <td>2018</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1</td>
      <td>Alaska</td>
      <td>207</td>
      <td>6</td>
      <td>1844</td>
      <td>1621</td>
      <td>249</td>
      <td>137</td>
      <td>0</td>
      <td>0</td>
      <td>...</td>
      <td>0</td>
      <td>13</td>
      <td>20</td>
      <td>5</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>4429</td>
      <td>2018-01</td>
      <td>2018</td>
    </tr>
    <tr>
      <th>2</th>
      <td>1</td>
      <td>Arizona</td>
      <td>5686</td>
      <td>413</td>
      <td>11659</td>
      <td>6992</td>
      <td>1074</td>
      <td>531</td>
      <td>0</td>
      <td>10</td>
      <td>...</td>
      <td>0</td>
      <td>30</td>
      <td>14</td>
      <td>9</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>28627</td>
      <td>2018-01</td>
      <td>2018</td>
    </tr>
    <tr>
      <th>3</th>
      <td>1</td>
      <td>Arkansas</td>
      <td>4162</td>
      <td>555</td>
      <td>4935</td>
      <td>5061</td>
      <td>232</td>
      <td>323</td>
      <td>37</td>
      <td>7</td>
      <td>...</td>
      <td>0</td>
      <td>13</td>
      <td>13</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>17483</td>
      <td>2018-01</td>
      <td>2018</td>
    </tr>
    <tr>
      <th>4</th>
      <td>1</td>
      <td>California</td>
      <td>55292</td>
      <td>0</td>
      <td>37335</td>
      <td>22919</td>
      <td>3495</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>...</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>119910</td>
      <td>2018-01</td>
      <td>2018</td>
    </tr>
  </tbody>
</table>
<p>5 rows × 29 columns</p>
</div>



Now the data is more suitable for analysis! Save the processed data in stages.Save the processed data in stages.


```python
df.to_csv('./data/nfbc_cleaned.csv', index=False)
```

<a id='eda'></a>
## Exploratory Data Analysis

### Research Question 1 ：Which year did the United States have the largest number of guns trades? How many pistols? How much guns? Specific to the month?


```python
# group by years and show the bar 
# but we should remove 2018 and 1998, because they are not full year.
df_year_totals = df.query("year < 2018 and year > 1998").groupby(['year']).sum()
labels = np.arange(1999,2018)

sns.set()
width = 0.85       # the width of the bars: can also be len(x) sequence
plt.figure(figsize=(20,10))
plt.bar(labels, df_year_totals['totals'], width, alpha=0.75)
plt.ylabel('Guns Total')
plt.title('Guns Total In Each Year')
plt.xticks(labels)
plt.show()
```


![png](output_19_0.png)


The number of system inspections was relatively stable until 2006, but the total number of inspections has been on an upward trend since 2006, with the highest number of inspections of firearms trades in 2016.


```python
# group by month and show the area
df_month_totals = df.groupby(['year_month'], as_index=False).sum()
df_month_totals
ax = df_month_totals['totals'].plot.area(figsize=(20,10), alpha=0.5)
tick_placement = pd.np.arange(1, len(df_month_totals), 12)
ax.set_title("Monthly NICS Background Check Totals Since Nov. 1998", fontsize=24)
ax.set_yticklabels([ "{0:,.0f}".format(y) for y in ax.get_yticks() ], fontsize=12)
ax.set_xticks(tick_placement)
ax.set_xticklabels([ df_month_totals.loc[i,'year_month'] for i in tick_placement ]);
```


![png](output_21_0.png)


The total amount of transactions checked through the system has been increasing year by year. According to the monthly area charts, we find that the annual peak is actually December.
Then we take a look at the highest number of transactions in each of the several months of the year. 


```python
# show the mean of each month in every year by plotting bar.
# we should remove 2018 and 1998, because they are not full year.
df_month_mean = df.query("year < 2018 and year > 1998").groupby(['month']).mean()
labels = np.arange(1999,2018)

ax = df_month_mean['totals'].plot.bar(figsize=(20,10), alpha=0.75)
ax.set_title("Monthly NICS Background Check Totals Since Nov.", fontsize=24);
for p in ax.patches:
    ax.annotate("{:.0f}".format(p.get_height()), (p.get_x() * 1.005, p.get_height() * 1.005), fontsize=16)
```


![png](output_23_0.png)


Through the observation of the monthly data, it was found that the number of guns traded in May, June and July was mediocre, but the number of guns trades was relatively large from September to January. 

### Research Question 2 : Excluding private trading and leasing pawn and other channels, the conventional civilian firearms transactions, the different types of firearms in the transaction accounted for? Which different types of guns in different periods and what is the difference? Pistols in that period of time more popular?


```python
#Just remove the gun trade data from the mainstream channels we care about
df_guns_type = df.loc[:,'handgun':'other'].sum()
df_guns_type.plot.pie(autopct='%1.1f%%', title="Guns Type", label="",
                  fontsize=14, figsize=(12,12));
```


![png](output_26_0.png)


Through the statistics on all the data, it was found that the longs were more popular than the shorts.But the survey is a bit crude and we need to compare it at different times.


```python
# Compare the rifle and rifle data by month, and this 
# time we directly exclude the other types of guns.
df_guns_type = df.groupby(['year_month']).sum().loc[:,'handgun':'long_gun']
ax = df_guns_type.plot.area(figsize=(20,10));
tick_placement = pd.np.arange(1, len(df_guns_type), 12)
ax.set_title("Monthly NICS Background Check Totals Since Nov. 1998", fontsize=24)
ax.set_yticklabels([ "{0:,.0f}".format(y) for y in ax.get_yticks() ], fontsize=12)
ax.set_xticks(tick_placement)
ax.set_xticklabels([ df_month_totals.loc[i,'year_month'] for i in tick_placement ]);
```


![png](output_28_0.png)


We found that the trend of guns and pistols are basically the same, but by the year 2015, the area of guns in the plot seems to outnumber the rifles. Let's make a detailed comparison.


```python
df_guns_type = df.groupby(['year_month']).sum().loc[:,'handgun':'long_gun']
ax = df_guns_type.plot.bar(figsize=(20,10), alpha=0.45, width=2, color=['green', 'red']);
tick_placement = pd.np.arange(1, len(df_guns_type), 6)
ax.set_title("Monthly NICS Background Check Totals Since Nov. 1998", fontsize=24)
ax.set_yticklabels([ "{0:,.0f}".format(y) for y in ax.get_yticks() ], fontsize=12)
ax.set_xticks(tick_placement)
ax.set_xticklabels([ df_month_totals.loc[i,'year_month'] for i in tick_placement ]);
```


![png](output_30_0.png)


It is clear that since 2014, the number of pistol transactions has overtaken the rifle and continues until now, which may be related to a government policy.

**Long gun and handgun in different times.**

To study this issue requires a bit of additional information, that is, from 1998 to the present time of several presidents. Based on these times, grouping was conducted to compare the U.S. gun trade data for different presidential periods. In order to be fair, we still discard the channels of leasing and pawnshops which appeared only in the latter part of the period, and only keep the mainstream trading channels.

> **Presidential ruling schedule**：
- Bill Clinton ： 1993 - 2001
- George Walker Bush： 2001 - 2009
- Barack Hussein Obama II： 2009 - 2017
- Donald John Trump：2017 -


```python
# create edges by Presidential ruling schedule
bin_edges = [ 1998, 2001, 2009, 2017, 2018] 
bin_names = ['Clinton', 'Bush', 'Obama', 'Trump']
#cut dataframe by presidential ruling schedule
df['president'] = pd.cut(df['year'], bins=bin_edges, labels=bin_names)
```


```python
df_pre = df.groupby('president').mean()
datas = df_pre.loc[:,'handgun':'other']
ax = datas.plot.area(figsize=(20,10), alpha=0.75);
tick_placement = np.arange(0, len(datas), 1)
ax.set_title("hangun and long gun in different periods of change(MEAN)", fontsize=24)
ax.set_yticklabels([ "{0:,.0f}".format(y) for y in ax.get_yticks() ], fontsize=12)
ax.set_xticks(tick_placement)
ax.set_xticklabels(bin_names);
```


![png](output_36_0.png)



```python
ax = datas.plot.bar(figsize=(20,10))
datas.plot.line(ax=ax)
ax.set_title('hangun and long gun in different periods of change(MEAN)',fontsize=24)
for p in ax.patches:
    ax.annotate("{:.0f}".format(p.get_height()), (p.get_x() * 1.005, p.get_height() * 1.005), fontsize=16);
```


![png](output_37_0.png)


It can be found that the regular volume of transactions that were imposed during the Obama administration has seen an explosive growth. Although Trump has only been in office for one year now, the average number of traded guns is still lower than that of Obama.

Combined with the monthly chart above, we found a curious phenomenon: the number of firearms transactions reached a peak during Obama's tenure and reelection.

### Research Question 3: How are the firearms transactions in different regions? What is the relationship between mandatory quantity and population?


```python
#group data set by state and get sum
df_region = df.groupby(['state'], as_index=False).mean()
```

Let's take a look at those cities where authentication systems operate more frequently. In other words, NICS systems are the best performing in which cities.


```python
x = np.arange(len(df_region))
y = df_region['totals']
s = df_region['totals'] / 20
c = cm.rainbow(np.linspace(0, 1, len(y)))
plt.figure(figsize=(20,10))
ax = plt.subplot()
plt.xticks(x, df_region['state'], rotation='vertical')
ax.scatter(x=x, y=y, s=s, color=c)
for i, txt in zip(x,df_region['state']):
    ax.annotate(txt, (x[i]-1, y[i]))
```


![png](output_42_0.png)


Kentucky's total gun trade looks to be far ahead.But, Why? Is it because the state has a large population?

To solve this problem, we need to use census data from the United States.


```python
#import the census data
df_pop = pd.read_csv('data/u.s.-census-data.csv')
df_pop = df_pop.T
# we only use the estimates in 2016 and append it to 
df_2016_est = df_pop.iloc[2:,1].reset_index()
df_2016_est.columns=['state', '2016_est']
#change the 2016_est from string to number
df_2016_est['2016_est'] = df_2016_est['2016_est'].str.replace(',','').astype(int)
# get the 2016 regioned data
df_2016_region = df.groupby(['year','state'], as_index=False).sum().query("year==2016")
# merge dataframe
df_2016 = pd.merge(df_2016_region, df_2016_est)
df_2016.head()
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
      <th>year</th>
      <th>state</th>
      <th>month</th>
      <th>permit</th>
      <th>permit_recheck</th>
      <th>handgun</th>
      <th>long_gun</th>
      <th>other</th>
      <th>multiple</th>
      <th>admin</th>
      <th>...</th>
      <th>rentals_handgun</th>
      <th>rentals_long_gun</th>
      <th>private_sale_handgun</th>
      <th>private_sale_long_gun</th>
      <th>private_sale_other</th>
      <th>return_to_seller_handgun</th>
      <th>return_to_seller_long_gun</th>
      <th>return_to_seller_other</th>
      <th>totals</th>
      <th>2016_est</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2016</td>
      <td>Alabama</td>
      <td>78</td>
      <td>291039</td>
      <td>0</td>
      <td>153123</td>
      <td>121963</td>
      <td>6104</td>
      <td>6545</td>
      <td>5</td>
      <td>...</td>
      <td>0</td>
      <td>0</td>
      <td>72</td>
      <td>65</td>
      <td>4</td>
      <td>3</td>
      <td>2</td>
      <td>0</td>
      <td>616947</td>
      <td>4780131</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2016</td>
      <td>Alaska</td>
      <td>78</td>
      <td>3121</td>
      <td>0</td>
      <td>37491</td>
      <td>36887</td>
      <td>2889</td>
      <td>2316</td>
      <td>0</td>
      <td>...</td>
      <td>0</td>
      <td>0</td>
      <td>39</td>
      <td>66</td>
      <td>1</td>
      <td>2</td>
      <td>3</td>
      <td>0</td>
      <td>87647</td>
      <td>710249</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2016</td>
      <td>Arizona</td>
      <td>78</td>
      <td>87771</td>
      <td>5109</td>
      <td>166784</td>
      <td>108988</td>
      <td>13122</td>
      <td>7908</td>
      <td>0</td>
      <td>...</td>
      <td>0</td>
      <td>0</td>
      <td>76</td>
      <td>56</td>
      <td>11</td>
      <td>11</td>
      <td>11</td>
      <td>1</td>
      <td>416279</td>
      <td>6392301</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2016</td>
      <td>Arkansas</td>
      <td>78</td>
      <td>55456</td>
      <td>7036</td>
      <td>80244</td>
      <td>82120</td>
      <td>3059</td>
      <td>4400</td>
      <td>143</td>
      <td>...</td>
      <td>0</td>
      <td>0</td>
      <td>72</td>
      <td>64</td>
      <td>6</td>
      <td>7</td>
      <td>4</td>
      <td>1</td>
      <td>266014</td>
      <td>2916025</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2016</td>
      <td>California</td>
      <td>78</td>
      <td>1036981</td>
      <td>0</td>
      <td>560355</td>
      <td>554550</td>
      <td>211707</td>
      <td>0</td>
      <td>0</td>
      <td>...</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>2377167</td>
      <td>37254522</td>
    </tr>
  </tbody>
</table>
<p>5 rows × 29 columns</p>
</div>



Now we have the dataframe witch contains populations data anda guns data in 2016, then we can find the relationship between them.


```python
y = df_2016['2016_est']
x = df_2016['totals']
s =  df_2016['totals'] / df_2016['2016_est']  * 4000
c = cm.rainbow(np.linspace(0, 1, len(y)))
plt.figure(figsize=(20,10))
ax = plt.subplot()
ax.scatter(x=x, y=y, s=s, color=c)
plt.xlabel('totals in guns', fontsize=18)
plt.ylabel('Population', fontsize=18)
plt.title('relationship between population and guns total in 2016', fontsize=28);
```


![png](output_46_0.png)


We can find that there is a positive correlation between total population and total firearms.

<a id='conclusions'></a>
## Conclusions

In general, the number of guns in the United States has been rising year by year. In the Obama and Trump era, the number of firearms began to increase rapidly.And the amount of short shots gradually exceeds that of long shots.Finally, the number of guns is also related to the size of the city. The more people in the city, the more guns there are, but this is not always the case.
Perhaps, people are increasingly insecure, so most people will buy guns. People tend to use short shots for self-defense rather than long shots, perhaps to avoid accidents.
Anyway, I hope everyone will not launch bullets and hope that world peace.

