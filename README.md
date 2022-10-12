For Milestone 1 you should generate a roughly 1 page writeup (~500 words) listing a partner and one to three datasets that you are considering working with and why. For each dataset you should generate at least one question you hope to answer with that data as well as a small amount of ETL including one interesting stat and one graph. This is just an outline to make sure you are thinking and is not a commitment in any way. This will be published on your GitHub IO page so this also makes sure you’ve figured out how to get it uploaded!

You must also include a short collaboration plan describing how you are working together, what technologies you are using, and when / how often you are meeting to work on this project. Examples include: we setup a private Github repo to coordinate code and we met on Zoom X times…. or even we used LiveShare for CS Code Teletype for Atom or RemoteCollab for Sublime. Failure to turn in a collaboration plan that shows you coordinated will be a loss of professionalism points. The turned in result will need to reflect the understanding of both students

You should load one of these datasets and parse it into shape using the principles of tidy data discussed in class and display the data table in a reasonable format so demonstrating what data you have. This is to show that you have figured out how to get the data into your system and does not need to be a final version, but it should show that you can read in a data source for your project. You should clearly discuss the data and what challenges you had in formatting it.

One page Writeup (500 words)
For each dataset, generate one or more questions you hope as well as some ETL including one interesting stat and one graph.
Short collaboration plan:
How we are working togther, what technologies wer





*   One page Writeup (500 words)
*   For each dataset, generate one or more questions you hope as well as some ETL including one interesting stat and one graph. 


*   Short collaboration plan: 
  *   How we are working togther, what technologies wer







# Where Does Foreign Aid Go?
By Chris Callahan and Mikey Sison

Datasets Used for Project: 
[Gapminder](https://www.gapminder.org/data/), 
[US Gov Foreign Assistance](https://foreignassistance.gov/
)


```python
%pip install geopandas
%pip install seaborn
```

    Collecting geopandas
      Downloading geopandas-0.11.1-py3-none-any.whl (1.0 MB)
    [K     |████████████████████████████████| 1.0 MB 3.4 MB/s eta 0:00:01
    [?25hRequirement already satisfied: shapely<2,>=1.7 in /Users/chriscallahan/opt/anaconda3/lib/python3.9/site-packages (from geopandas) (1.7.1)
    Requirement already satisfied: pyproj>=2.6.1.post1 in /Users/chriscallahan/opt/anaconda3/lib/python3.9/site-packages (from geopandas) (3.3.0)
    Requirement already satisfied: packaging in /Users/chriscallahan/opt/anaconda3/lib/python3.9/site-packages (from geopandas) (21.3)
    Requirement already satisfied: pandas>=1.0.0 in /Users/chriscallahan/opt/anaconda3/lib/python3.9/site-packages (from geopandas) (1.4.1)
    Requirement already satisfied: fiona>=1.8 in /Users/chriscallahan/opt/anaconda3/lib/python3.9/site-packages (from geopandas) (1.8.21)
    Requirement already satisfied: six>=1.7 in /Users/chriscallahan/opt/anaconda3/lib/python3.9/site-packages (from fiona>=1.8->geopandas) (1.16.0)
    Requirement already satisfied: cligj>=0.5 in /Users/chriscallahan/opt/anaconda3/lib/python3.9/site-packages (from fiona>=1.8->geopandas) (0.7.2)
    Requirement already satisfied: click>=4.0 in /Users/chriscallahan/opt/anaconda3/lib/python3.9/site-packages (from fiona>=1.8->geopandas) (8.0.4)
    Requirement already satisfied: setuptools in /Users/chriscallahan/opt/anaconda3/lib/python3.9/site-packages (from fiona>=1.8->geopandas) (58.0.4)
    Requirement already satisfied: click-plugins>=1.0 in /Users/chriscallahan/opt/anaconda3/lib/python3.9/site-packages (from fiona>=1.8->geopandas) (1.1.1)
    Requirement already satisfied: certifi in /Users/chriscallahan/opt/anaconda3/lib/python3.9/site-packages (from fiona>=1.8->geopandas) (2022.6.15)
    Requirement already satisfied: attrs>=17 in /Users/chriscallahan/opt/anaconda3/lib/python3.9/site-packages (from fiona>=1.8->geopandas) (21.4.0)
    Requirement already satisfied: munch in /Users/chriscallahan/opt/anaconda3/lib/python3.9/site-packages (from fiona>=1.8->geopandas) (2.5.0)
    Requirement already satisfied: python-dateutil>=2.8.1 in /Users/chriscallahan/opt/anaconda3/lib/python3.9/site-packages (from pandas>=1.0.0->geopandas) (2.8.2)
    Requirement already satisfied: pytz>=2020.1 in /Users/chriscallahan/opt/anaconda3/lib/python3.9/site-packages (from pandas>=1.0.0->geopandas) (2021.3)
    Requirement already satisfied: numpy>=1.18.5 in /Users/chriscallahan/opt/anaconda3/lib/python3.9/site-packages (from pandas>=1.0.0->geopandas) (1.20.3)
    Requirement already satisfied: pyparsing!=3.0.5,>=2.0.2 in /Users/chriscallahan/opt/anaconda3/lib/python3.9/site-packages (from packaging->geopandas) (3.0.4)
    Installing collected packages: geopandas
    Successfully installed geopandas-0.11.1
    Note: you may need to restart the kernel to use updated packages.
    Requirement already satisfied: seaborn in /Users/chriscallahan/opt/anaconda3/lib/python3.9/site-packages (0.11.2)
    Requirement already satisfied: numpy>=1.15 in /Users/chriscallahan/opt/anaconda3/lib/python3.9/site-packages (from seaborn) (1.20.3)
    Requirement already satisfied: scipy>=1.0 in /Users/chriscallahan/opt/anaconda3/lib/python3.9/site-packages (from seaborn) (1.7.3)
    Requirement already satisfied: pandas>=0.23 in /Users/chriscallahan/opt/anaconda3/lib/python3.9/site-packages (from seaborn) (1.4.1)
    Requirement already satisfied: matplotlib>=2.2 in /Users/chriscallahan/opt/anaconda3/lib/python3.9/site-packages (from seaborn) (3.5.1)
    Requirement already satisfied: cycler>=0.10 in /Users/chriscallahan/opt/anaconda3/lib/python3.9/site-packages (from matplotlib>=2.2->seaborn) (0.11.0)
    Requirement already satisfied: python-dateutil>=2.7 in /Users/chriscallahan/opt/anaconda3/lib/python3.9/site-packages (from matplotlib>=2.2->seaborn) (2.8.2)
    Requirement already satisfied: kiwisolver>=1.0.1 in /Users/chriscallahan/opt/anaconda3/lib/python3.9/site-packages (from matplotlib>=2.2->seaborn) (1.3.2)
    Requirement already satisfied: packaging>=20.0 in /Users/chriscallahan/opt/anaconda3/lib/python3.9/site-packages (from matplotlib>=2.2->seaborn) (21.3)
    Requirement already satisfied: pillow>=6.2.0 in /Users/chriscallahan/opt/anaconda3/lib/python3.9/site-packages (from matplotlib>=2.2->seaborn) (9.0.1)
    Requirement already satisfied: fonttools>=4.22.0 in /Users/chriscallahan/opt/anaconda3/lib/python3.9/site-packages (from matplotlib>=2.2->seaborn) (4.25.0)
    Requirement already satisfied: pyparsing>=2.2.1 in /Users/chriscallahan/opt/anaconda3/lib/python3.9/site-packages (from matplotlib>=2.2->seaborn) (3.0.4)
    Requirement already satisfied: pytz>=2020.1 in /Users/chriscallahan/opt/anaconda3/lib/python3.9/site-packages (from pandas>=0.23->seaborn) (2021.3)
    Requirement already satisfied: six>=1.5 in /Users/chriscallahan/opt/anaconda3/lib/python3.9/site-packages (from python-dateutil>=2.7->matplotlib>=2.2->seaborn) (1.16.0)
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
#import seaborn as sns; sns.set_theme()


```

Europe: Moldova, Turkey
Asia: Myanmar
Middle East: Yemen
Africa: Ethiopia
South America: Ecuador
North/Central America: El Salvador, Haiti



```python
os.chdir('Foreign-Aid-Country-Data')
```

## Foreign Aid by Country data from ForeignAssistance.gov

The link below describes the data and columns gathered from ForeignAssistance.gov. 

<a href= 'https://s3.amazonaws.com/files.explorer.devtechlab.com/DataDictionary_ForeignAssistancegov.pdf'> https://s3.amazonaws.com/files.explorer.devtechlab.com/DataDictionary_ForeignAssistancegov.pdf </a>

#### Example Query
![FAgovEthiopiaQuery.png](attachment:FAgovEthiopiaQuery.png)
The image above is an example query of the foreign aid given to Ethiopia over from 2018 to 2022


### Reading in Country data

Here, we are reading in the country data from our queries on ForeignAssistance.gov. Each of the queries encompass non-military foreign aid from 2002 to 2022 fiscal years. We deliberately chose countries in low to middle income groups:


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



```python
foreign_aid_country_summary_data = pd.read_csv('US_foreign_aid_summary.csv')
```


```python
Ethiopia_foreign_aid_data = pd.read_csv('.\Africa' + '\Ethiopia_2022-2002_Econ_Other.csv')
Moldova_foreign_aid_data = pd.read_csv('.\Europe' + '/Moldova_2022-2002_Econ_Other.csv')
Turkey_foreign_aid_data = pd.read_csv('.\Europe' + '\Turkey_2022-2002_Econ_Other.csv')
Yemen_foreign_aid_data = pd.read_csv('.\Middle-East' + '\Yemen_2022-2002_Econ_Other.csv')
Ecuador_foreign_aid_data = pd.read_csv('.\South-America' + '\Ecuador_2022-2002_Econ_Other.csv')
El_Salvador_foreign_aid_data = pd.read_csv('.\\North-America' + '\El_Salvador_2022-2002_Econ_Other.csv')
Haiti_foreign_aid_data = pd.read_csv('.\\North-America' + '\\Haiti_2022-2002_Econ_Other.csv')

countries = [Ethiopia_foreign_aid_data, Moldova_foreign_aid_data, Turkey_foreign_aid_data,
             Yemen_foreign_aid_data, Ecuador_foreign_aid_data, El_Salvador_foreign_aid_data,
            Haiti_foreign_aid_data] # For ease of looping

Ethiopia_foreign_aid_data.columns
```




    Index(['Country ID', 'Country Code', 'Country Name', 'Region ID',
           'Region Name', 'Income Group ID', 'Income Group Name',
           'Income Group Acronym', 'Managing Agency ID', 'Managing Agency Acronym',
           'Managing Agency Name', 'Managing Sub-agency or Bureau ID',
           'Managing Sub-agency or Bureau Acronym',
           'Managing Sub-agency or Bureau Name',
           'Implementing Partner Category ID',
           'Implementing Partner Category Name',
           'Implementing Partner Sub-category ID',
           'Implementing Partner Sub-category Name', 'Implementing Partner ID',
           'Implementing Partner Name', 'International Category ID',
           'International Category Name', 'International Sector Code',
           'International Sector Name', 'International Purpose Code',
           'International Purpose Name', 'US Category ID', 'US Category Name',
           'US Sector ID', 'US Sector Name', 'Submission ID', 'Funding Acount ID',
           'Funding Account Name', 'Funding Agency ID', 'Funding Agency Name',
           'Funding Agency Acronym', 'Foreign Assistance Objective ID',
           'Foreign Assistance Objective Name', 'Aid Type Group ID',
           'Aid Type Group Name', 'Activity ID', 'Activity Name',
           'Activity Description', 'Activity Project Number',
           'Activity Start Date', 'Activity End Date', 'Transaction Type ID',
           'Transaction Type Name', 'Fiscal Year', 'Current Dollar Amount',
           'Constant Dollar Amount'],
          dtype='object')



### Exploratory Data Analysis


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
        'Funding Agency Acronym',
        'US Category ID',
        'US Category Name',
                   
    ]


for country in countries:
    for col in columns_to_drop:
        country.drop(labels= col, axis= 'columns', inplace= True)



```


```python

```


    'Low Income Country'



    'Lower Middle Income Country'



    'Upper Middle Income Country'



    'Low Income Country'



    'Upper Middle Income Country'



    'Lower Middle Income Country'



    'Low Income Country'



```python
Ethiopia_foreign_aid_data.columns


```




    Index(['Country ID', 'Country Code', 'Country Name', 'Region ID',
           'Region Name', 'Income Group ID', 'Income Group Name',
           'Managing Agency Acronym', 'Managing Agency Name', 'US Sector ID',
           'US Sector Name', 'Funding Account Name', 'Funding Agency Name',
           'Foreign Assistance Objective Name', 'Aid Type Group Name',
           'Activity Name', 'Activity Start Date', 'Activity End Date',
           'Transaction Type Name', 'Fiscal Year', 'Current Dollar Amount',
           'Constant Dollar Amount'],
          dtype='object')




```python

```
