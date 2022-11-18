# Where Does Foreign Aid Go?
By Chris Callahan and Mikey Sison

[Website Link](https://chriscallahan99.github.io/DataScience_Final_Project/)

Datasets Used for Project: 
[US Gov Foreign Assistance](https://foreignassistance.gov/
), [WHO GW13 Dataset](https://www.who.int/data/inequality-monitor/data#PageContent_C160_Col00),
[Cereal Dataset](https://ourworldindata.org/agricultural-production#cereals)


```python
%pip install seaborn
```

    Requirement already satisfied: seaborn in /Users/chriscallahan/opt/anaconda3/lib/python3.9/site-packages (0.12.0)
    Requirement already satisfied: pandas>=0.25 in /Users/chriscallahan/opt/anaconda3/lib/python3.9/site-packages (from seaborn) (1.4.1)
    Requirement already satisfied: matplotlib>=3.1 in /Users/chriscallahan/opt/anaconda3/lib/python3.9/site-packages (from seaborn) (3.5.1)
    Requirement already satisfied: numpy>=1.17 in /Users/chriscallahan/opt/anaconda3/lib/python3.9/site-packages (from seaborn) (1.20.3)
    Requirement already satisfied: kiwisolver>=1.0.1 in /Users/chriscallahan/opt/anaconda3/lib/python3.9/site-packages (from matplotlib>=3.1->seaborn) (1.3.2)
    Requirement already satisfied: cycler>=0.10 in /Users/chriscallahan/opt/anaconda3/lib/python3.9/site-packages (from matplotlib>=3.1->seaborn) (0.11.0)
    Requirement already satisfied: python-dateutil>=2.7 in /Users/chriscallahan/opt/anaconda3/lib/python3.9/site-packages (from matplotlib>=3.1->seaborn) (2.8.2)
    Requirement already satisfied: pyparsing>=2.2.1 in /Users/chriscallahan/opt/anaconda3/lib/python3.9/site-packages (from matplotlib>=3.1->seaborn) (3.0.4)
    Requirement already satisfied: pillow>=6.2.0 in /Users/chriscallahan/opt/anaconda3/lib/python3.9/site-packages (from matplotlib>=3.1->seaborn) (9.0.1)
    Requirement already satisfied: packaging>=20.0 in /Users/chriscallahan/opt/anaconda3/lib/python3.9/site-packages (from matplotlib>=3.1->seaborn) (21.3)
    Requirement already satisfied: fonttools>=4.22.0 in /Users/chriscallahan/opt/anaconda3/lib/python3.9/site-packages (from matplotlib>=3.1->seaborn) (4.25.0)
    Requirement already satisfied: pytz>=2020.1 in /Users/chriscallahan/opt/anaconda3/lib/python3.9/site-packages (from pandas>=0.25->seaborn) (2021.3)
    Requirement already satisfied: six>=1.5 in /Users/chriscallahan/opt/anaconda3/lib/python3.9/site-packages (from python-dateutil>=2.7->matplotlib>=3.1->seaborn) (1.16.0)
    Note: you may need to restart the kernel to use updated packages.



```python
## IMPORTS ##

# Load Numpy
import numpy as np

# Load os
import os

# Load Pandas
import pandas as pd

# Load MatPlotLib
import matplotlib
import matplotlib.pyplot as plt

# Load Seaborn
import seaborn as sns; sns.set_theme()



# These two things are for Pandas, it widens the notebook and lets us display data easily.
from IPython.display import display, HTML
display(HTML("<style>.container { width:95% !important; }</style>"))
```


<style>.container { width:95% !important; }</style>



```python
os.chdir("Foreign-Aid-Country-Data")
```

## Foreign Aid by Country data from ForeignAssistance.gov

Our first dataset comes from the foreign assistance webpage from the U.S. government. We will be focusing on the amount of foreign aid the United States gives to other countries, per year, and to which categories of support this money is going, among others. The question we want to answer with this dataset is "Which category, whether it be economic, health-related, or education, does funding go to for each country?" This leads us to the next question we aim to answer, "Does monetary aid in these specific categories lead to significant, measurable improvements in these areas, and what other metrics are secondarrily correlated to the aid disbursements?" 

Our second data set (which is not included in Milestone 1) is the GW13 dataset from the World Health Organization which includes many indicators that correlate to the public health of a country. We want to see how much these different indicators improve with U.S. funding.



The link below describes the data and columns gathered from ForeignAssistance.gov. 

<a href= 'https://s3.amazonaws.com/files.explorer.devtechlab.com/DataDictionary_ForeignAssistancegov.pdf'> https://s3.amazonaws.com/files.explorer.devtechlab.com/DataDictionary_ForeignAssistancegov.pdf </a>

#### Example Query
![FAgovEthiopiaQuery.png](docs/assets/FAgovEthiopiaQuery.png)

Figure 1: The image above is an example query of the foreign aid given to Ethiopia over from 2018 to 2022


### Reading in Country data

Here, we are reading in the country data from our queries on ForeignAssistance.gov. Each of the queries encompass non-military foreign aid from 2002 to 2022 fiscal years. 

We chose countries in low to middle income groups with one to two per continent/region and low military assistance.
* **Europe**: Moldova, Turkey
* **Asia**: Myanmar
* **Middle East**: Yemen
* **Africa**: Ethiopia
* **South America**: Ecuador
* **North/Central America**: El Salvador, Haiti


#### Focus areas
We will be focusing on two datasets from the ForeignAssistance.gov website: the summary data and country-specific data. Each row in the summary describes a country and the fiscal year. Each entity contains data on the transaction type, Transaction Type, Fiscal Year, current amount (in USD), and constant amount (in USD). We are planning on using this data from the summary as a reference to determine how much money is irrelevant to our study (non-military/defense spending).



```python
#foreign_aid_country_summary_data = pd.read_csv('US_foreign_aid_summary.csv')
```


```python
# Due to programming on different operating systems (windows, mac)
# Need to change slash chars based on the system to read dfs correctly

if(os.name == "nt"):
    slash_char = "\\"
else:
    slash_char = "/"

Ethiopia_foreign_aid_data = pd.read_csv('.' + slash_char + 'Africa' + slash_char + 'Ethiopia_2022-2002_Econ_Other.csv')
Moldova_foreign_aid_data = pd.read_csv('.' + slash_char + 'Europe' + slash_char + 'Moldova_2022-2002_Econ_Other.csv')
Yemen_foreign_aid_data = pd.read_csv('.' + slash_char + 'Middle-East' + slash_char + 'Yemen_2022-2002_Econ_Other.csv')
Ecuador_foreign_aid_data = pd.read_csv('.' + slash_char + 'South-America' + slash_char + 'Ecuador_2022-2002_Econ_Other.csv')
El_Salvador_foreign_aid_data = pd.read_csv('.' + slash_char + slash_char + 'North-America' + slash_char + 'El_Salvador_2022-2002_Econ_Other.csv')
Haiti_foreign_aid_data = pd.read_csv('.' + slash_char + slash_char + 'North-America' + slash_char +slash_char + 'Haiti_2022-2002_Econ_Other.csv')

countries = [Ethiopia_foreign_aid_data, Moldova_foreign_aid_data,
             Yemen_foreign_aid_data, Ecuador_foreign_aid_data, El_Salvador_foreign_aid_data,
            Haiti_foreign_aid_data] # For ease of looping

Ethiopia_foreign_aid_data
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
      <th>Country ID</th>
      <th>Country Code</th>
      <th>Country Name</th>
      <th>Region ID</th>
      <th>Region Name</th>
      <th>Income Group ID</th>
      <th>Income Group Name</th>
      <th>Income Group Acronym</th>
      <th>Managing Agency ID</th>
      <th>Managing Agency Acronym</th>
      <th>...</th>
      <th>Activity Name</th>
      <th>Activity Description</th>
      <th>Activity Project Number</th>
      <th>Activity Start Date</th>
      <th>Activity End Date</th>
      <th>Transaction Type ID</th>
      <th>Transaction Type Name</th>
      <th>Fiscal Year</th>
      <th>Current Dollar Amount</th>
      <th>Constant Dollar Amount</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>231</td>
      <td>ETH</td>
      <td>Ethiopia</td>
      <td>5</td>
      <td>Sub-Saharan Africa</td>
      <td>1</td>
      <td>Low Income Country</td>
      <td>LIC</td>
      <td>1</td>
      <td>USAID</td>
      <td>...</td>
      <td>Title II Emergency Program</td>
      <td>Title II Emergency Program through a partner o...</td>
      <td>720BHA22CA00005</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>2</td>
      <td>Obligations</td>
      <td>2022</td>
      <td>113052149</td>
      <td>109155305</td>
    </tr>
    <tr>
      <th>1</th>
      <td>231</td>
      <td>ETH</td>
      <td>Ethiopia</td>
      <td>5</td>
      <td>Sub-Saharan Africa</td>
      <td>1</td>
      <td>Low Income Country</td>
      <td>LIC</td>
      <td>1</td>
      <td>USAID</td>
      <td>...</td>
      <td>Emergency food assistance</td>
      <td>Emergency humanitarian food and nutirition ass...</td>
      <td>720BHA22IO00038</td>
      <td>NaN</td>
      <td>2023-03-09</td>
      <td>2</td>
      <td>Obligations</td>
      <td>2022</td>
      <td>77740000</td>
      <td>75060346</td>
    </tr>
    <tr>
      <th>2</th>
      <td>231</td>
      <td>ETH</td>
      <td>Ethiopia</td>
      <td>5</td>
      <td>Sub-Saharan Africa</td>
      <td>1</td>
      <td>Low Income Country</td>
      <td>LIC</td>
      <td>1</td>
      <td>USAID</td>
      <td>...</td>
      <td>Title II Emergency Program</td>
      <td>Title II Emergency Program through a partner o...</td>
      <td>720BHA22IO00034</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>2</td>
      <td>Obligations</td>
      <td>2022</td>
      <td>57420571</td>
      <td>55441315</td>
    </tr>
    <tr>
      <th>3</th>
      <td>231</td>
      <td>ETH</td>
      <td>Ethiopia</td>
      <td>5</td>
      <td>Sub-Saharan Africa</td>
      <td>1</td>
      <td>Low Income Country</td>
      <td>LIC</td>
      <td>1</td>
      <td>USAID</td>
      <td>...</td>
      <td>Emergency food assistance</td>
      <td>Emergency humanitarian food and nutirition ass...</td>
      <td>720BHA22IO00045</td>
      <td>NaN</td>
      <td>2022-12-31</td>
      <td>2</td>
      <td>Obligations</td>
      <td>2022</td>
      <td>44500000</td>
      <td>42966110</td>
    </tr>
    <tr>
      <th>4</th>
      <td>231</td>
      <td>ETH</td>
      <td>Ethiopia</td>
      <td>5</td>
      <td>Sub-Saharan Africa</td>
      <td>1</td>
      <td>Low Income Country</td>
      <td>LIC</td>
      <td>1</td>
      <td>USAID</td>
      <td>...</td>
      <td>Humanitarian assistance</td>
      <td>Humanitarian assistance funding.</td>
      <td>720BHA22GR00071</td>
      <td>NaN</td>
      <td>2024-09-30</td>
      <td>2</td>
      <td>Obligations</td>
      <td>2022</td>
      <td>39493563</td>
      <td>38132242</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>13582</th>
      <td>231</td>
      <td>ETH</td>
      <td>Ethiopia</td>
      <td>5</td>
      <td>Sub-Saharan Africa</td>
      <td>1</td>
      <td>Low Income Country</td>
      <td>LIC</td>
      <td>1</td>
      <td>USAID</td>
      <td>...</td>
      <td>FFP/CRITICAL FOOD NEEDS OF TARGETED GROUPS MET</td>
      <td>Critical food needs of targeted groups met</td>
      <td>962-001</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>2</td>
      <td>Obligations</td>
      <td>2002</td>
      <td>-69035</td>
      <td>-96892</td>
    </tr>
    <tr>
      <th>13583</th>
      <td>231</td>
      <td>ETH</td>
      <td>Ethiopia</td>
      <td>5</td>
      <td>Sub-Saharan Africa</td>
      <td>1</td>
      <td>Low Income Country</td>
      <td>LIC</td>
      <td>1</td>
      <td>USAID</td>
      <td>...</td>
      <td>Mitigate the effects of disaster</td>
      <td>Mitigate the Effects of Disaster</td>
      <td>663-011</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>2</td>
      <td>Obligations</td>
      <td>2002</td>
      <td>-228000</td>
      <td>-320000</td>
    </tr>
    <tr>
      <th>13584</th>
      <td>231</td>
      <td>ETH</td>
      <td>Ethiopia</td>
      <td>5</td>
      <td>Sub-Saharan Africa</td>
      <td>1</td>
      <td>Low Income Country</td>
      <td>LIC</td>
      <td>1</td>
      <td>USAID</td>
      <td>...</td>
      <td>Mitigate the effects of disaster</td>
      <td>Mitigate the Effects of Disaster</td>
      <td>663-011</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>2</td>
      <td>Obligations</td>
      <td>2002</td>
      <td>-229909</td>
      <td>-322679</td>
    </tr>
    <tr>
      <th>13585</th>
      <td>231</td>
      <td>ETH</td>
      <td>Ethiopia</td>
      <td>5</td>
      <td>Sub-Saharan Africa</td>
      <td>1</td>
      <td>Low Income Country</td>
      <td>LIC</td>
      <td>1</td>
      <td>USAID</td>
      <td>...</td>
      <td>Essential Services for Health</td>
      <td>Essential Services for Health</td>
      <td>663-002</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>2</td>
      <td>Obligations</td>
      <td>2002</td>
      <td>-284530</td>
      <td>-399340</td>
    </tr>
    <tr>
      <th>13586</th>
      <td>231</td>
      <td>ETH</td>
      <td>Ethiopia</td>
      <td>5</td>
      <td>Sub-Saharan Africa</td>
      <td>1</td>
      <td>Low Income Country</td>
      <td>LIC</td>
      <td>1</td>
      <td>USAID</td>
      <td>...</td>
      <td>FFP/CRITICAL FOOD NEEDS OF TARGETED GROUPS MET</td>
      <td>Critical food needs of targeted groups met</td>
      <td>962-001</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>2</td>
      <td>Obligations</td>
      <td>2002</td>
      <td>-579358</td>
      <td>-813134</td>
    </tr>
  </tbody>
</table>
<p>13587 rows × 51 columns</p>
</div>



Figure 2: The observation for each entity is the Activity that is being funded.

### Exploratory Data Analysis

First, we are going to focus our attention on columns of importance. This means dropping columns that are for internal US gov use and other secondary IDs that are not of use to our analysis.  


```python
columns_to_drop = ['Transaction Type ID',
        'Foreign Assistance Objective ID',
        'Implementing Partner Category ID',
        'Implementing Partner Category Name',
        'Implementing Partner Sub-category ID',
        'Implementing Partner Sub-category Name', 
        'Implementing Partner ID',
        'Implementing Partner Name', 
        'International Category ID',
        'International Category Name',
        'International Sector Code',
        'International Sector Name', 
        'International Purpose Code',
        'Managing Agency ID',
        'Managing Sub-agency or Bureau ID',
        'Activity Project Number',
        'Managing Sub-agency or Bureau Name',
        'Aid Type Group ID',
        'Funding Agency ID',
        'Submission ID', 
        'Funding Acount ID',
        'Activity ID',
        'Income Group Acronym',
        'Managing Sub-agency or Bureau Acronym',
        'US Category ID',
        'US Category Name',
    ]


for country in countries:
    for col in columns_to_drop:
        country.drop(labels= col, axis= 'columns', inplace= True)

Ethiopia_foreign_aid_data.columns
```




    Index(['Country ID', 'Country Code', 'Country Name', 'Region ID',
           'Region Name', 'Income Group ID', 'Income Group Name',
           'Managing Agency Acronym', 'Managing Agency Name',
           'International Purpose Name', 'US Sector ID', 'US Sector Name',
           'Funding Account Name', 'Funding Agency Name', 'Funding Agency Acronym',
           'Foreign Assistance Objective Name', 'Aid Type Group Name',
           'Activity Name', 'Activity Description', 'Activity Start Date',
           'Activity End Date', 'Transaction Type Name', 'Fiscal Year',
           'Current Dollar Amount', 'Constant Dollar Amount'],
          dtype='object')



Next, we want to highlight the income of each of the countries to verify that the countries chosen are likely to receive a sizeable amount and diverse range of US foreign aid. 


```python
for country in countries:
    display(country['Country Name'][0] + ': '+ country['Income Group Name'][0])
```


    'Ethiopia: Low Income Country'



    'Moldova: Lower Middle Income Country'



    'Yemen: Low Income Country'



    'Ecuador: Upper Middle Income Country'



    'El Salvador: Lower Middle Income Country'



    'Haiti: Low Income Country'


Furthermore, we want to get rid of entries / aid projects that come from military sources since they would not have impacts on the metrics we are interested in.


```python
# Drop rows unrelated to economic/health development
for i, country in enumerate(countries):
    country.drop(country[country["Managing Agency Name"] == 'Department of Defense'].index, inplace = True)
    country.drop(country[country["Managing Agency Name"] == 'Department of Homeland Security'].index, inplace = True)
    country.drop(country[country["Managing Agency Name"] == 'Department of Justice'].index, inplace = True)
    country.drop(country[country["Managing Agency Name"] == 'Department of the Army'].index, inplace = True)
    country.drop(country[country["Managing Agency Name"] == 'Department of the Interior'].index, inplace = True)
    # Drop row if dollar amount is less than 0 as this is usually a redaction of funds
    country.drop(country[country["Constant Dollar Amount"] < 0 ].index, inplace = True)

```

Fun fact: The U.S. has given over $35 million to ethiopia for election related activities 



```python
Ethiopia_Election_df = Ethiopia_foreign_aid_data[Ethiopia_foreign_aid_data['Activity Name'].str.contains('Election')]

money_sum = Ethiopia_Election_df["Constant Dollar Amount"].sum()

print('${:,.2f}'.format(money_sum))

```

    $37,957,689.00


Let's plot the amount of foreign aid received by country. This exploration will help us determine which of the countries chosen recieve the most aid. Make sure to pay attention to the axis labels! 


```python
fig, axes = plt.subplots(2, 3, figsize=(12,10) , sharey= True)
fig.tight_layout(h_pad= 3)

sns.lineplot(Ethiopia_foreign_aid_data, x= 'Fiscal Year', y= 'Constant Dollar Amount', ax= axes[0,0]).set_title('Ethiopia')
sns.lineplot(Moldova_foreign_aid_data , x= 'Fiscal Year', y= 'Constant Dollar Amount', ax= axes[0,1]).set_title('Moldova')
sns.lineplot(Haiti_foreign_aid_data , x= 'Fiscal Year', y= 'Constant Dollar Amount', ax= axes[1,0]).set_title('Haiti')
sns.lineplot(Ecuador_foreign_aid_data , x= 'Fiscal Year', y= 'Constant Dollar Amount', ax= axes[1,1]).set_title('Ecuador')
sns.lineplot(El_Salvador_foreign_aid_data, x= 'Fiscal Year', y= 'Constant Dollar Amount', ax= axes[0,2]).set_title('El Salvador')
sns.lineplot(Yemen_foreign_aid_data, x= 'Fiscal Year', y= 'Constant Dollar Amount', ax= axes[1,2]).set_title('Yemen')

```




    Text(0.5, 1.0, 'Yemen')




    
![png](output_24_1.png)
    


Graph 1: As we can see, Yemen receives the most US aid in the recent years. Next, let's examine which agencies give the most aid. This will allow us to determine which category, health or economic, the country receives the most aid in. 


```python
# Get the top 5 agencies by money spent 

country_by_funding_agency = []
for i, country  in enumerate(countries):
    # Groupby funding agency to get money spent per agency.
    country_by_funding_agency.append(pd.DataFrame(country.groupby('Funding Agency Acronym')['Constant Dollar Amount'].sum()))
    country_by_funding_agency[i].reset_index(inplace=True) # Reset index for plotting
    # Sort and locate the top 5 agencies by money spent
    country_by_funding_agency[i] = country_by_funding_agency[i].sort_values(ascending= False, by= 'Constant Dollar Amount')
    country_by_funding_agency[i] = country_by_funding_agency[i].iloc[0:5] 
```


```python
# Plotting the top 5 agencies by money spent
fig, axes = plt.subplots(2, 3, figsize=(12,10))
fig.tight_layout(h_pad= 3)

sns.barplot(data= country_by_funding_agency[0] , x= 'Funding Agency Acronym', y= 'Constant Dollar Amount',  ax= axes[0,0]).set_title('Ethiopia')
sns.barplot(data= country_by_funding_agency[1] , x= 'Funding Agency Acronym', y= 'Constant Dollar Amount',  ax= axes[1,2]).set_title('Moldova')
sns.barplot(data= country_by_funding_agency[2] , x= 'Funding Agency Acronym', y= 'Constant Dollar Amount',  ax= axes[1,0]).set_title('Yemen')
sns.barplot(data= country_by_funding_agency[3] , x= 'Funding Agency Acronym', y= 'Constant Dollar Amount',  ax= axes[1,1]).set_title('Ecuador')
sns.barplot(data= country_by_funding_agency[4] , x= 'Funding Agency Acronym', y= 'Constant Dollar Amount',  ax= axes[0,1]).set_title('El Salvador')
sns.barplot(data= country_by_funding_agency[5] , x= 'Funding Agency Acronym', y= 'Constant Dollar Amount',  ax= axes[0,2]).set_title('Haiti')



```




    Text(0.5, 1.0, 'Haiti')




    
![png](output_27_1.png)
    


Graph 3: Bar charts describing the amount of money spent by agency.

### Closing thoughts 


#### Collaboration Plan
Our collaboration plan is to meet once or twice a week mostly in-person as needed. We’ve been meeting up at the library and will likely continue to do so. If we have an increased workload to complete, we will decide to either meetup for longer or assign tasks to finish by the next meeting. 

#### Challenges With Data

A key challenge we came across was getting recent data from gapminder as many of their datasets only had data up until 2010, however, our timeframe was up until 2020. So we found the source of the health data from Gapminder, which came from the World Health Organization and found more up-to-date datasets which contained data up to 2020.

# Milestone 2

### GW13 WHO Dataset

Our second datatset comes from the [GW13 WHO Dataset](https://www.who.int/data/inequality-monitor/data#PageContent_C160_Col00), which contains health markers of interest, stratified by country and year. We did most of the basic filtering of data in Excel as not to clutter the report. We plan on using this data to contextualize and predict the importance of various funding and their quantifible impacts on health outcomes in our countries of interest. 


```python
gw19_df = pd.read_excel(".." + slash_char + "WHO_datasets"+ slash_char + "GW19_2002_2020_filtered.xlsx")
gw19_df.head(5)
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
      <th>setting</th>
      <th>year</th>
      <th>indicator_abbr</th>
      <th>indicator_name</th>
      <th>dimension</th>
      <th>subgroup</th>
      <th>estimate</th>
      <th>population</th>
      <th>indicator_scale</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Ecuador</td>
      <td>2002</td>
      <td>alcohol</td>
      <td>Total alcohol per capita consumption in adults...</td>
      <td>Sex</td>
      <td>Female</td>
      <td>2.0</td>
      <td>NaN</td>
      <td>100</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Ecuador</td>
      <td>2002</td>
      <td>alcohol</td>
      <td>Total alcohol per capita consumption in adults...</td>
      <td>Sex</td>
      <td>Male</td>
      <td>7.0</td>
      <td>NaN</td>
      <td>100</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Ecuador</td>
      <td>2002</td>
      <td>bp</td>
      <td>Prevalence of hypertension among adults aged 3...</td>
      <td>Sex</td>
      <td>Female</td>
      <td>25.0</td>
      <td>NaN</td>
      <td>100</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Ecuador</td>
      <td>2002</td>
      <td>bp</td>
      <td>Prevalence of hypertension among adults aged 3...</td>
      <td>Sex</td>
      <td>Male</td>
      <td>23.0</td>
      <td>NaN</td>
      <td>100</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Ecuador</td>
      <td>2002</td>
      <td>hh_san_sm</td>
      <td>Population using safely managed sanitation ser...</td>
      <td>Place of residence</td>
      <td>Rural</td>
      <td>44.0</td>
      <td>5110442.0</td>
      <td>100</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Here are the different indicators we have kept.
display(gw19_df["indicator_name"].unique())
```


    array(['Total alcohol per capita consumption in adults aged 15+ (litres of pure alcohol)',
           'Prevalence of hypertension among adults aged 30-79 years (age-standardized) (%)',
           'Population using safely managed sanitation services (%)',
           'Population using safely managed drinking water services (%)',
           'Obesity prevalence among adults (%)',
           'Obesity prevalence among children and adolescents (5-19) (%)',
           'Population using at least basic sanitation services (%)',
           'Suicide mortality rate (per 100 000 population)',
           'Under-five mortality rate (deaths per 1000 live births)',
           'Life Expectancy',
           'Antenatal care coverage - at least four visits (in the five years preceding the survey) (%)',
           'Overweight prevalence in children aged < 5 years (%)',
           'Stunting prevalence in children aged < 5 years (%)',
           'DTP3 immunization coverage among one-year-olds (%)',
           'Measles immunization coverage among one-year-olds (%)',
           'Polio immunization coverage among one-year-olds (%)',
           'Healthy life expectancy (HALE) at birth (years)',
           'Population with >10% household expenditures on health (%)',
           'Population with >25% household expenditures on health (%)',
           'Mortality rate attributed to unsafe water, unsafe sanitation and lack of hygiene (per 100 000 population)',
           'TB incidence (new infections per 100 000 population)',
           'Neonatal mortality rate (deaths per 1000 live births)'],
          dtype=object)


###  Water Funding vs Access to Clean Water

After looking through the GW13 dataset we came across an interesting statistic that, in 2002, ~0% of the rural population of Ethiopia had access to clean drinking water. This bewildering statistic made us want to dive deeper into Ethiopians' access to clean water.

Below, we visualize the amount of funding Ethiopia receives for water related purposes from our initial dataset, and compare that to percent of Ethiopians with access to clean water with the GW13 datset.


```python
import matplotlib.ticker as mtick

Ethiopia_water_funding = Ethiopia_foreign_aid_data[Ethiopia_foreign_aid_data['International Purpose Name'].str.contains('water', case=False)]

x = Ethiopia_water_funding["Fiscal Year"]
y = Ethiopia_water_funding["Current Dollar Amount"]

fig, ax = plt.subplots()
fig.suptitle('Funding Amounts Related to Water in Ethiopia')

ax.bar(x, y)
ax.ticklabel_format(useOffset=False, style='plain')


fmt = '${x:,.0f}'
tick = mtick.StrMethodFormatter(fmt)
ax.yaxis.set_major_formatter(tick) 

plt.xlabel('Year', fontsize=14)
plt.ylabel('Current Dollar Amounts', fontsize=14)

plt.show()
```


    
![png](output_38_0.png)
    


As seen above, Ethiopia receives regular funding in regard to water related purposes, but has it helped improve access to clean water?


```python
# Create df of the population using safely managed drinking services.
safe_water_percent = gw19_df[gw19_df['indicator_name'] == "Population using safely managed drinking water services (%)"]
safe_water_percent = safe_water_percent[safe_water_percent["setting"] == "Ethiopia"]

# Divide into separate dfs for rural vs urban
safe_water_percent_rural = safe_water_percent[safe_water_percent["subgroup"] == "Rural"]
safe_water_percent_urban = safe_water_percent[safe_water_percent["subgroup"] == "Urban"]
```


```python
# Creating Rural vs Urban plot

fig, (ax1, ax2) = plt.subplots(1,2 , figsize=(7,5))
fig.suptitle('% of Ethiopian Population With Safe Drinking Water')
fig.tight_layout(pad=3)

x = safe_water_percent_urban["year"]
y = safe_water_percent_urban["estimate"]

z = safe_water_percent_rural["year"]
w = safe_water_percent_rural["estimate"]

# Subplot formatting
ax1.plot(x, y)
ax1.set_title('Urban Population')
ax1.yaxis.set_major_formatter(mtick.PercentFormatter())
ax1.set(xlabel='Year', ylabel='% with Clean Drinking Water')

ax2.plot(z, w)
ax2.set_title('Rural Population')
ax2.yaxis.set_major_formatter(mtick.PercentFormatter())
ax2.set(xlabel='Year', ylabel='% with Clean Drinking Water')

plt.show()
```


    
![png](output_41_0.png)
    


As the graphs show, access to clean drinking services to each population has increased significantly, with the Rural population increasing faster.

### Cereal Production dataset
Cereal production is vital to a country's self-sustainability and ability to feed its own without heavy reliance on foreign imports. Moreover, after careful examination of the dataset (see figure 2 for more details), many activity descriptions discuss improving food security. Many of the studied countries fall into the last places in the [Global Food Security Index](https://impact.economist.com/sustainability/project/food-security-index#rankings-and-trends 'Food Security Inded'). This fact makes domestic food production of prime importance for many of these countries. We look to examine if changes in aid donation affects the amount of cereal produced. We will only examine entries that contain certain indicators of food assistance. 

From looking at the dataset, we have concluded that the following words are indicators of the entity being for food assistance:
* 'Food Assistance'
* 'Emergency food assistance'
* And any other entities containing 'food' in the 'International Purpose Name' column.

![food_security_index.jpg](docs/assets/food_security_index.jpg)
Figure 3: Global Food Security Index 99th-113th ranked countries in food security. Here lies 3 of our countries: Ethiopia at 100th, Yemen at 111th, and Haiti at 112th.

#### Obtaining the dataset


```python
os.chdir(".." + slash_char + "food_datasets")

cereal_df = pd.read_csv('cereal-production.csv')
```


```python
cereal_df.head()
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
      <th>Entity</th>
      <th>Year</th>
      <th>Cereals Production (tonnes)</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Ecuador</td>
      <td>2002</td>
      <td>2057298</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Ecuador</td>
      <td>2003</td>
      <td>2055013</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Ecuador</td>
      <td>2004</td>
      <td>2607223</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Ecuador</td>
      <td>2005</td>
      <td>2303958</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Ecuador</td>
      <td>2006</td>
      <td>2278620</td>
    </tr>
  </tbody>
</table>
</div>



#### Getting the funding amounts related to food


```python
food_aid = pd.DataFrame()


Ethiopia_cereal_data = Ethiopia_foreign_aid_data.loc[Ethiopia_foreign_aid_data['International Purpose Name'].str.contains(
                            'food', case=False)][['Current Dollar Amount', 'Fiscal Year']].groupby('Fiscal Year').sum()

Yemen_cereal_data    = Yemen_foreign_aid_data.loc[Yemen_foreign_aid_data['International Purpose Name'].str.contains(
                            'food', case=False)][['Current Dollar Amount', 'Fiscal Year']].groupby('Fiscal Year').sum()

Haiti_cereal_data    = Haiti_foreign_aid_data.loc[Haiti_foreign_aid_data['International Purpose Name'].str.contains(
                            'food', case=False)][['Current Dollar Amount', 'Fiscal Year']].groupby('Fiscal Year').sum()                     

Moldova_foreign_aid_data = Moldova_foreign_aid_data.loc[Moldova_foreign_aid_data['International Purpose Name'].str.contains(
                            'food', case=False)][['Current Dollar Amount', 'Fiscal Year']].groupby('Fiscal Year').sum() 

El_Salvador_foreign_aid_data = El_Salvador_foreign_aid_data.loc[El_Salvador_foreign_aid_data['International Purpose Name'].str.contains(
                            'food', case=False)][['Current Dollar Amount', 'Fiscal Year']].groupby('Fiscal Year').sum()

Ecuador_foreign_aid_data = Ecuador_foreign_aid_data.loc[Ecuador_foreign_aid_data['International Purpose Name'].str.contains(
                            'food', case=False)][['Current Dollar Amount', 'Fiscal Year']].groupby('Fiscal Year').sum() 




# Add the country codes into the df
Ethiopia_cereal_data['Country Name'] = 'Ethiopia'
Yemen_cereal_data['Country Name'] = 'Yemen'
Haiti_cereal_data['Country Name'] = 'Haiti'
Moldova_foreign_aid_data['Country Name'] = 'Moldova'
El_Salvador_foreign_aid_data['Country Name'] = 'El Salvador'  
Ecuador_foreign_aid_data['Country Name'] = 'Ecuador'


food_aid = pd.concat([Ethiopia_cereal_data,
                        Yemen_cereal_data,
                        Haiti_cereal_data,  
                        Moldova_foreign_aid_data,
                        El_Salvador_foreign_aid_data,
                        Ecuador_foreign_aid_data                     
                     ])

# Lets break this down-- 
# First, we locate the column International Purpose Name and changing it into a string in order to check if it contains the string 'food', not case-senstitive. 
# Second, we slice the 'Current Dollar Amount' and 'Fiscal Year' from the dataframe into its own data frame.
# Next, we group by the 'Fiscal Year' and sum to get all of the aid spend on food in a given year, by country.
# Finally, we add the country names and concatenate into a single dataframe.



food_aid.head()
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
      <th>Current Dollar Amount</th>
      <th>Country Name</th>
    </tr>
    <tr>
      <th>Fiscal Year</th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2002</th>
      <td>77724560</td>
      <td>Ethiopia</td>
    </tr>
    <tr>
      <th>2003</th>
      <td>368507947</td>
      <td>Ethiopia</td>
    </tr>
    <tr>
      <th>2004</th>
      <td>254435836</td>
      <td>Ethiopia</td>
    </tr>
    <tr>
      <th>2005</th>
      <td>467729117</td>
      <td>Ethiopia</td>
    </tr>
    <tr>
      <th>2006</th>
      <td>150265025</td>
      <td>Ethiopia</td>
    </tr>
  </tbody>
</table>
</div>



Now, in order to effectively merge them, we will merge their names and year in order to get a single key that can be used to merge upon. 


```python
food_aid.reset_index(inplace= True)

food_aid['Fiscal Year'] = food_aid['Fiscal Year'].astype('string')
food_aid['Year_Name'] = food_aid['Country Name'] + food_aid['Fiscal Year']

food_aid.head()
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
      <th>Fiscal Year</th>
      <th>Current Dollar Amount</th>
      <th>Country Name</th>
      <th>Year_Name</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2002</td>
      <td>77724560</td>
      <td>Ethiopia</td>
      <td>Ethiopia2002</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2003</td>
      <td>368507947</td>
      <td>Ethiopia</td>
      <td>Ethiopia2003</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2004</td>
      <td>254435836</td>
      <td>Ethiopia</td>
      <td>Ethiopia2004</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2005</td>
      <td>467729117</td>
      <td>Ethiopia</td>
      <td>Ethiopia2005</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2006</td>
      <td>150265025</td>
      <td>Ethiopia</td>
      <td>Ethiopia2006</td>
    </tr>
  </tbody>
</table>
</div>




```python
cereal_df['Year'] = cereal_df['Year'].astype('string')
cereal_df['Year_Name'] = cereal_df['Entity'] + cereal_df['Year']
cereal_df.head()
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
      <th>Entity</th>
      <th>Year</th>
      <th>Cereals Production (tonnes)</th>
      <th>Year_Name</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Ecuador</td>
      <td>2002</td>
      <td>2057298</td>
      <td>Ecuador2002</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Ecuador</td>
      <td>2003</td>
      <td>2055013</td>
      <td>Ecuador2003</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Ecuador</td>
      <td>2004</td>
      <td>2607223</td>
      <td>Ecuador2004</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Ecuador</td>
      <td>2005</td>
      <td>2303958</td>
      <td>Ecuador2005</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Ecuador</td>
      <td>2006</td>
      <td>2278620</td>
      <td>Ecuador2006</td>
    </tr>
  </tbody>
</table>
</div>



Merge the two dataframes on 'Year_Name' with regards to the food_aid frame.


```python
aid_cereal_merge = food_aid.merge(cereal_df, on= 'Year_Name', how= 'left')

aid_cereal_merge.head()
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
      <th>Fiscal Year</th>
      <th>Current Dollar Amount</th>
      <th>Country Name</th>
      <th>Year_Name</th>
      <th>Entity</th>
      <th>Year</th>
      <th>Cereals Production (tonnes)</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2002</td>
      <td>77724560</td>
      <td>Ethiopia</td>
      <td>Ethiopia2002</td>
      <td>Ethiopia</td>
      <td>2002</td>
      <td>9000335.0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2003</td>
      <td>368507947</td>
      <td>Ethiopia</td>
      <td>Ethiopia2003</td>
      <td>Ethiopia</td>
      <td>2003</td>
      <td>9532780.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2004</td>
      <td>254435836</td>
      <td>Ethiopia</td>
      <td>Ethiopia2004</td>
      <td>Ethiopia</td>
      <td>2004</td>
      <td>10140082.0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2005</td>
      <td>467729117</td>
      <td>Ethiopia</td>
      <td>Ethiopia2005</td>
      <td>Ethiopia</td>
      <td>2005</td>
      <td>12749986.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2006</td>
      <td>150265025</td>
      <td>Ethiopia</td>
      <td>Ethiopia2006</td>
      <td>Ethiopia</td>
      <td>2006</td>
      <td>12672350.0</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Delete redundant columns after the merge
del aid_cereal_merge['Year']
del aid_cereal_merge['Entity']
aid_cereal_merge.head()
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
      <th>Fiscal Year</th>
      <th>Current Dollar Amount</th>
      <th>Country Name</th>
      <th>Year_Name</th>
      <th>Cereals Production (tonnes)</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2002</td>
      <td>77724560</td>
      <td>Ethiopia</td>
      <td>Ethiopia2002</td>
      <td>9000335.0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2003</td>
      <td>368507947</td>
      <td>Ethiopia</td>
      <td>Ethiopia2003</td>
      <td>9532780.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2004</td>
      <td>254435836</td>
      <td>Ethiopia</td>
      <td>Ethiopia2004</td>
      <td>10140082.0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2005</td>
      <td>467729117</td>
      <td>Ethiopia</td>
      <td>Ethiopia2005</td>
      <td>12749986.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2006</td>
      <td>150265025</td>
      <td>Ethiopia</td>
      <td>Ethiopia2006</td>
      <td>12672350.0</td>
    </tr>
  </tbody>
</table>
</div>



### Plotting the Cereal Production and Aid Given Relationship


```python
ethiopia_cereal_merged = aid_cereal_merge[aid_cereal_merge['Country Name'] == 'Ethiopia']

y1 = ethiopia_cereal_merged['Current Dollar Amount'].apply(lambda x: x / 10**8)
y2 = ethiopia_cereal_merged['Cereals Production (tonnes)'].apply(lambda x: x / 10**7)
x = "'" + ethiopia_cereal_merged['Fiscal Year'].str.slice(start= 2)

fig, ax = plt.subplots()
ax.set_xlabel('Year')

ax.bar(x, y1, label='Foreign Aid ($10s million)' )
ax.bar(x, y2, label='Cereal Production (millions of tonnes)', alpha= 0.5)
ax.legend()
```




    <matplotlib.legend.Legend at 0x7fc1a35b1460>




    
![png](output_57_1.png)
    



```python
print(f'Average Spending: $%d' %np.average(ethiopia_cereal_merged['Current Dollar Amount']))
```

    Average Spending: $361637510


## Final Notes

For the final part of the project, we plan on predicting the % change in 1. Domestic Cereal Production, 2. Clean Drinking Water Accessibility, and 3. Child Stunting Prevalence given x amount of foreign aid. We plan on using either a support vector regressor or a random forest regressor for our models. One advantage of the random forest is that we could obtain the feature importances to determine if funding from a specific agency or activity is more impactful than others. 

