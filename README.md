# Where Does Foreign Aid Go?
By Chris Callahan and Mikey Sison

Datasets Used for Project: 
[Gapminder](https://www.gapminder.org/data/), 
[US Gov Foreign Assistance](https://foreignassistance.gov/
)
[Link to Website](https://chriscallahan99.github.io/DataScience_Final_Project/)


```python
%pip install geopandas
%pip install seaborn
```


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
from IPython.core.display import display, HTML
display(HTML("<style>.container { width:95% !important; }</style>"))
```

    /var/folders/kl/vj_wh7n95j35fks9h2vf9j0w0000gn/T/ipykernel_25303/617074380.py:22: DeprecationWarning: Importing display from IPython.core.display is deprecated since IPython 7.14, please import from IPython display
      from IPython.core.display import display, HTML



<style>.container { width:95% !important; }</style>



```python
os.chdir('Foreign-Aid-Country-Data')
```

## Foreign Aid by Country data from ForeignAssistance.gov

Our first dataset comes from the foreign assistance webpage from the U.S. government. We will be focusing on the amount of foreign aid the United States gives to other countries, per year, and to which categories of support this money is going, among others. The question we want to answer with this dataset is "Which category, whether it be economic, health-related, or education, does funding go to for each country?" This leads us to the next question we aim to answer, "Does monetary aid in these specific categories lead to significant, measurable improvements in these areas, and what other metrics are secondarrily correlated to the aid disbursements?" 

Our second data set (which is not included in Milestone 1) is the GW13 dataset from the World Health Organization which includes many indicators that correlate to the public health of a country. We want to see how much these different indicators improve with U.S. funding.



The link below describes the data and columns gathered from ForeignAssistance.gov. 

<a href= 'https://s3.amazonaws.com/files.explorer.devtechlab.com/DataDictionary_ForeignAssistancegov.pdf'> https://s3.amazonaws.com/files.explorer.devtechlab.com/DataDictionary_ForeignAssistancegov.pdf </a>

#### Example Query
![FAgovEthiopiaQuery.png](attachment:FAgovEthiopiaQuery.png)
The image above is an example query of the foreign aid given to Ethiopia over from 2018 to 2022


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
foreign_aid_country_summary_data = pd.read_csv('US_foreign_aid_summary.csv')
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
Turkey_foreign_aid_data = pd.read_csv('.' + slash_char + 'Europe' + slash_char + 'Turkey_2022-2002_Econ_Other.csv')
Yemen_foreign_aid_data = pd.read_csv('.' + slash_char + 'Middle-East' + slash_char + 'Yemen_2022-2002_Econ_Other.csv')
Ecuador_foreign_aid_data = pd.read_csv('.' + slash_char + 'South-America' + slash_char + 'Ecuador_2022-2002_Econ_Other.csv')
El_Salvador_foreign_aid_data = pd.read_csv('.' + slash_char + slash_char + 'North-America' + slash_char + 'El_Salvador_2022-2002_Econ_Other.csv')
Haiti_foreign_aid_data = pd.read_csv('.' + slash_char + slash_char + 'North-America' + slash_char +slash_char + 'Haiti_2022-2002_Econ_Other.csv')

countries = [Ethiopia_foreign_aid_data, Moldova_foreign_aid_data, Turkey_foreign_aid_data,
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



The observation for each entity is the Activity that is being funded.

### Exploratory Data Analysis

First, we are going to focus our attention on columns of importance. This means dropping columns that are for internal US gov use and other secondary IDs that are not of use to our analysis.  


```python
columns_to_drop = ['Transaction Type ID',
        'Activity Description',
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
        'International Purpose Name',
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
           'Managing Agency Acronym', 'Managing Agency Name', 'US Sector ID',
           'US Sector Name', 'Funding Account Name', 'Funding Agency Name',
           'Funding Agency Acronym', 'Foreign Assistance Objective Name',
           'Aid Type Group Name', 'Activity Name', 'Activity Start Date',
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



    'Turkey: Upper Middle Income Country'



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
# For scale purposes: y(1e7)
fig1, axes1 = plt.subplots(1, 2, figsize=(9,5) , sharey= True)
fig1.tight_layout(h_pad= 3)
sns.lineplot(Turkey_foreign_aid_data, x= 'Fiscal Year', y= 'Constant Dollar Amount', ax= axes1[0]).set_title('Turkey')
sns.lineplot(Yemen_foreign_aid_data, x= 'Fiscal Year', y= 'Constant Dollar Amount', ax= axes1[1]).set_title('Yemen')

# For scale purposes: y(1e6)
fig2, axes2 = plt.subplots(2, 3, figsize=(12,10) , sharey= True)
fig2.tight_layout(h_pad= 3)

sns.lineplot(Ethiopia_foreign_aid_data, x= 'Fiscal Year', y= 'Constant Dollar Amount', ax= axes2[0,0]).set_title('Ethiopia')
sns.lineplot(Moldova_foreign_aid_data , x= 'Fiscal Year', y= 'Constant Dollar Amount', ax= axes2[0,1]).set_title('Moldova')
sns.lineplot(Haiti_foreign_aid_data , x= 'Fiscal Year', y= 'Constant Dollar Amount', ax= axes2[1,0]).set_title('Haiti')
sns.lineplot(Ecuador_foreign_aid_data , x= 'Fiscal Year', y= 'Constant Dollar Amount', ax= axes2[1,1]).set_title('Ecuador')
sns.lineplot(El_Salvador_foreign_aid_data, x= 'Fiscal Year', y= 'Constant Dollar Amount', ax= axes2[0,2]).set_title('El Salvador')

```




    Text(0.5, 1.0, 'El Salvador')




    
![png](output_23_1.png)
    



    
![png](output_23_2.png)
    


As we can see, Turkey and Yemen receive the most US aid. Next, let's examine which agencies give the most aid. This will allow us to determine which category, health or economic, the country receives the most aid in. 


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
fig, axes = plt.subplots(3, 3, figsize=(12,10))
fig.tight_layout(h_pad= 3)

sns.barplot(data= country_by_funding_agency[0] , x= 'Funding Agency Acronym', y= 'Constant Dollar Amount',  ax= axes[0,0]).set_title('Ethiopia')
sns.barplot(data= country_by_funding_agency[1] , x= 'Funding Agency Acronym', y= 'Constant Dollar Amount',  ax= axes[1,2]).set_title('Moldova')
sns.barplot(data= country_by_funding_agency[2] , x= 'Funding Agency Acronym', y= 'Constant Dollar Amount',  ax= axes[0,2]).set_title('Turkey')
sns.barplot(data= country_by_funding_agency[3] , x= 'Funding Agency Acronym', y= 'Constant Dollar Amount',  ax= axes[1,0]).set_title('Yemen')
sns.barplot(data= country_by_funding_agency[4] , x= 'Funding Agency Acronym', y= 'Constant Dollar Amount',  ax= axes[1,1]).set_title('Ecuador')
sns.barplot(data= country_by_funding_agency[5] , x= 'Funding Agency Acronym', y= 'Constant Dollar Amount',  ax= axes[0,1]).set_title('El Salvador')
sns.barplot(data= country_by_funding_agency[6] , x= 'Funding Agency Acronym', y= 'Constant Dollar Amount',  ax= axes[2,0]).set_title('Haiti')



```




    Text(0.5, 1.0, 'Haiti')




    
![png](output_26_1.png)
    


### Closing thoughts 


#### Collaboration Plan
Our collaboration plan is to meet once or twice a week mostly in-person as needed. We’ve been meeting up at the library and will likely continue to do so. If we have an increased workload to complete, we will decide to either meetup for longer or assign tasks to finish by the next meeting. 

#### Challenges With Data

A key challenge we came across was getting recent data from gapminder as many of their datasets only had data up until 2010, however, our timeframe was up until 2020. So we found the source of the health data from Gapminder, which came from the World Health Organization and found more up-to-date datasets which contained data upto 2020.
