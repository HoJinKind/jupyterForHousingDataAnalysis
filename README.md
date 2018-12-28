

```javascript
%%javascript

Jupyter.keyboard_manager.command_shortcuts.add_shortcut('r', {
    help : 'run all cells',
    help_index : 'zz',
    handler : function (event) {
        IPython.notebook.execute_all_cells();
        
    }}
);
```


    <IPython.core.display.Javascript object>


link of data:[URA website](https://www.ura.gov.sg/realEstateIIWeb/transaction/submitSearch.action)

# Threading the property Market in singapore.
## Rent or buy? 
Given a situation where an adult is given the option to buy, assuming he has sufficient for a down payment, should he or she go on to buy a property? Is property truly an asset?

Here, we will consider a specific private apartment, ParkView situated in Singapore, with relatively low variables, (no launch of MRT), to fully capture the depreciation of property as an asset, ceteris paribus. The analysis will span a period of 20 years, since its launch in 1998.


## Defining the problem/ Scoping
To do an anlysis of which is better, it is important that we define the factors we will need, the variables, to make an accurate analysis. Hence, i will be laying them down here.
They consist of:
    1. Transaction Prices
    2. CPI
    3. Interest Rates
    4. Rental


```python
#import files
import pandas as pd
import seaborn as sns
import numpy as np
import sys
import matplotlib.pyplot as plt
import plotly
import plotly.graph_objs as go

plotly.tools.set_credentials_file(username='s9600820a', api_key='5CkH8mTNtfDR2N6LZvmG')
```


```python
CPIdf = pd.read_csv("CPI123.csv")
ax = sns.barplot(x="year", y="value", data=CPIdf)
plt.show()
```


![png](output_5_0.png)


The above graph is the Consumer price index over the years, pegged to 1998. Next, we look at the rental for the past 20 years. Unfortunately, I was only able to get my hands on the average over the span of 20 years, and not individual data.<br/> <img src="files/rent.jpg">


```python
rentPerMonthPSM = 21.7
```

Now we will be looking at the raw Data we have.(Tranactions in ParkView)


```python
RawPVdf = pd.read_csv("RawparkviewTransactions.csv")
print(list(RawPVdf))# headers
RawPVdf=RawPVdf.drop(['Address','No. of Units'],axis =1)
RawPVdf.iloc[np.r_[0:2, -2:0]]
```

    ['Project Name', 'Address', 'No. of Units', 'Area (sqm)', 'Type of Area', 'Transacted Price ($)', 'Nett Price($)', 'Unit Price ($ psm)', 'Unit Price ($ psf)', 'Sale Date', 'Property Type', 'Tenure', 'Completion Date', 'Type of Sale', 'Purchaser Address Indicator', 'Postal District', 'Postal Sector', 'Postal Code', 'Planning Region', 'Planning Area']
    




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Project Name</th>
      <th>Area (sqm)</th>
      <th>Type of Area</th>
      <th>Transacted Price ($)</th>
      <th>Nett Price($)</th>
      <th>Unit Price ($ psm)</th>
      <th>Unit Price ($ psf)</th>
      <th>Sale Date</th>
      <th>Property Type</th>
      <th>Tenure</th>
      <th>Completion Date</th>
      <th>Type of Sale</th>
      <th>Purchaser Address Indicator</th>
      <th>Postal District</th>
      <th>Postal Sector</th>
      <th>Postal Code</th>
      <th>Planning Region</th>
      <th>Planning Area</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>PARKVIEW APARTMENTS</td>
      <td>104</td>
      <td>Strata</td>
      <td>815000</td>
      <td>-</td>
      <td>7837</td>
      <td>728</td>
      <td>26-Sep-18</td>
      <td>Apartment</td>
      <td>99 Yrs From 01/05/1994</td>
      <td>1998</td>
      <td>Resale</td>
      <td>HDB</td>
      <td>23</td>
      <td>65</td>
      <td>658882</td>
      <td>West Region</td>
      <td>Bukit Batok</td>
    </tr>
    <tr>
      <th>1</th>
      <td>PARKVIEW APARTMENTS</td>
      <td>87</td>
      <td>Strata</td>
      <td>728000</td>
      <td>-</td>
      <td>8368</td>
      <td>777</td>
      <td>17-Sep-18</td>
      <td>Apartment</td>
      <td>99 Yrs From 01/05/1994</td>
      <td>1998</td>
      <td>Resale</td>
      <td>HDB</td>
      <td>23</td>
      <td>65</td>
      <td>658882</td>
      <td>West Region</td>
      <td>Bukit Batok</td>
    </tr>
    <tr>
      <th>607</th>
      <td>PARKVIEW APARTMENTS</td>
      <td>104</td>
      <td>Strata</td>
      <td>465000</td>
      <td>-</td>
      <td>4471</td>
      <td>415</td>
      <td>Nov-98</td>
      <td>Apartment</td>
      <td>99 Yrs From 01/05/1994</td>
      <td>1998</td>
      <td>Sub Sale</td>
      <td>Private</td>
      <td>23</td>
      <td>65</td>
      <td>658881</td>
      <td>West Region</td>
      <td>Bukit Batok</td>
    </tr>
    <tr>
      <th>608</th>
      <td>PARKVIEW APARTMENTS</td>
      <td>104</td>
      <td>Strata</td>
      <td>505000</td>
      <td>-</td>
      <td>4856</td>
      <td>451</td>
      <td>6-Oct-98</td>
      <td>Apartment</td>
      <td>99 Yrs From 01/05/1994</td>
      <td>1998</td>
      <td>Sub Sale</td>
      <td>HDB</td>
      <td>23</td>
      <td>65</td>
      <td>658882</td>
      <td>West Region</td>
      <td>Bukit Batok</td>
    </tr>
  </tbody>
</table>
</div>



As you can see, the data contains accurate transactions, but we will be removing majority of the columns, keeping price, getting the average transaction price per year, and adjusting with CPI. 


```python
PVdf=RawPVdf.loc[:, ['Unit Price ($ psm)','Sale Date','Purchaser Address Indicator' ]]
PVdf.iloc[np.r_[0:2, -2:0]]
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Unit Price ($ psm)</th>
      <th>Sale Date</th>
      <th>Purchaser Address Indicator</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>7837</td>
      <td>26-Sep-18</td>
      <td>HDB</td>
    </tr>
    <tr>
      <th>1</th>
      <td>8368</td>
      <td>17-Sep-18</td>
      <td>HDB</td>
    </tr>
    <tr>
      <th>607</th>
      <td>4471</td>
      <td>Nov-98</td>
      <td>Private</td>
    </tr>
    <tr>
      <th>608</th>
      <td>4856</td>
      <td>6-Oct-98</td>
      <td>HDB</td>
    </tr>
  </tbody>
</table>
</div>




```python
#PVdf['year']=PVdf['Sale Date'].dt.strftime('Y')
PVdf['year']=PVdf['Sale Date'].str.slice(-2)
```


```python
#create pivot ttable just for fun
impute_grps = PVdf.pivot_table(index='year', columns='Purchaser Address Indicator', aggfunc='count', fill_value=0)
impute_grps["Sale Date"]
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th>Purchaser Address Indicator</th>
      <th>HDB</th>
      <th>N.A</th>
      <th>Private</th>
    </tr>
    <tr>
      <th>year</th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>00</th>
      <td>7</td>
      <td>0</td>
      <td>5</td>
    </tr>
    <tr>
      <th>01</th>
      <td>10</td>
      <td>0</td>
      <td>7</td>
    </tr>
    <tr>
      <th>02</th>
      <td>8</td>
      <td>0</td>
      <td>7</td>
    </tr>
    <tr>
      <th>03</th>
      <td>7</td>
      <td>0</td>
      <td>7</td>
    </tr>
    <tr>
      <th>04</th>
      <td>12</td>
      <td>0</td>
      <td>10</td>
    </tr>
    <tr>
      <th>05</th>
      <td>13</td>
      <td>0</td>
      <td>9</td>
    </tr>
    <tr>
      <th>06</th>
      <td>17</td>
      <td>0</td>
      <td>14</td>
    </tr>
    <tr>
      <th>07</th>
      <td>26</td>
      <td>0</td>
      <td>34</td>
    </tr>
    <tr>
      <th>08</th>
      <td>12</td>
      <td>0</td>
      <td>17</td>
    </tr>
    <tr>
      <th>09</th>
      <td>29</td>
      <td>0</td>
      <td>29</td>
    </tr>
    <tr>
      <th>10</th>
      <td>41</td>
      <td>0</td>
      <td>25</td>
    </tr>
    <tr>
      <th>11</th>
      <td>35</td>
      <td>0</td>
      <td>19</td>
    </tr>
    <tr>
      <th>12</th>
      <td>23</td>
      <td>0</td>
      <td>17</td>
    </tr>
    <tr>
      <th>13</th>
      <td>12</td>
      <td>0</td>
      <td>13</td>
    </tr>
    <tr>
      <th>14</th>
      <td>4</td>
      <td>0</td>
      <td>8</td>
    </tr>
    <tr>
      <th>15</th>
      <td>8</td>
      <td>0</td>
      <td>8</td>
    </tr>
    <tr>
      <th>16</th>
      <td>16</td>
      <td>0</td>
      <td>7</td>
    </tr>
    <tr>
      <th>17</th>
      <td>16</td>
      <td>0</td>
      <td>9</td>
    </tr>
    <tr>
      <th>18</th>
      <td>12</td>
      <td>0</td>
      <td>11</td>
    </tr>
    <tr>
      <th>98</th>
      <td>2</td>
      <td>0</td>
      <td>2</td>
    </tr>
    <tr>
      <th>99</th>
      <td>26</td>
      <td>5</td>
      <td>10</td>
    </tr>
  </tbody>
</table>
</div>



this is just for my curiosity on the demographics of people who purchase property.<br/> Back to work


```python
def completeDate(year):
    year=int(year)
    if year<50:
        return 2000+year
    else:
        return 1900+year
```


```python
PVdf['year']=PVdf.year.apply(completeDate)
PVdf=PVdf.drop(['Sale Date','Purchaser Address Indicator'],axis =1)
PVdf.iloc[np.r_[0:2, -2:0]]
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Unit Price ($ psm)</th>
      <th>year</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>7837</td>
      <td>2018</td>
    </tr>
    <tr>
      <th>1</th>
      <td>8368</td>
      <td>2018</td>
    </tr>
    <tr>
      <th>607</th>
      <td>4471</td>
      <td>1998</td>
    </tr>
    <tr>
      <th>608</th>
      <td>4856</td>
      <td>1998</td>
    </tr>
  </tbody>
</table>
</div>




```python

PVdf.columns = ['UnitPrice','year']
PVdf=PVdf.groupby(['year']).UnitPrice.agg(['count','mean'])
PVdf=PVdf.reset_index()

```

So here, i faced an issue which took sometime to fix. What happend was that there were 2018 transactions, but not infaltion for 2018. Hence, i had to take out 2018 transactions. Or asssume zero inflation.


```python
var = CPIdf.at[19,'value']
var
CPIdf.append(pd.Series([0,2018,var], index=CPIdf.columns) , ignore_index=True)
CPIdf = CPIdf.drop(['Unnamed: 0'],axis =1)
PVdf =pd.merge(PVdf, CPIdf,on='year')
PVdf
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>year</th>
      <th>count</th>
      <th>mean</th>
      <th>value</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1998</td>
      <td>4</td>
      <td>5103.250000</td>
      <td>1.000000</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1999</td>
      <td>41</td>
      <td>5860.756098</td>
      <td>1.000230</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2000</td>
      <td>12</td>
      <td>6182.583333</td>
      <td>1.013713</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2001</td>
      <td>17</td>
      <td>5050.117647</td>
      <td>1.023998</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2002</td>
      <td>15</td>
      <td>4440.266667</td>
      <td>1.019987</td>
    </tr>
    <tr>
      <th>5</th>
      <td>2003</td>
      <td>14</td>
      <td>4091.571429</td>
      <td>1.024960</td>
    </tr>
    <tr>
      <th>6</th>
      <td>2004</td>
      <td>22</td>
      <td>3862.045455</td>
      <td>1.042088</td>
    </tr>
    <tr>
      <th>7</th>
      <td>2005</td>
      <td>22</td>
      <td>3692.363636</td>
      <td>1.046967</td>
    </tr>
    <tr>
      <th>8</th>
      <td>2006</td>
      <td>31</td>
      <td>3802.645161</td>
      <td>1.057062</td>
    </tr>
    <tr>
      <th>9</th>
      <td>2007</td>
      <td>60</td>
      <td>5036.833333</td>
      <td>1.079312</td>
    </tr>
    <tr>
      <th>10</th>
      <td>2008</td>
      <td>29</td>
      <td>5602.758621</td>
      <td>1.150846</td>
    </tr>
    <tr>
      <th>11</th>
      <td>2009</td>
      <td>58</td>
      <td>5701.086207</td>
      <td>1.157716</td>
    </tr>
    <tr>
      <th>12</th>
      <td>2010</td>
      <td>66</td>
      <td>6891.590909</td>
      <td>1.190401</td>
    </tr>
    <tr>
      <th>13</th>
      <td>2011</td>
      <td>54</td>
      <td>7847.222222</td>
      <td>1.252869</td>
    </tr>
    <tr>
      <th>14</th>
      <td>2012</td>
      <td>40</td>
      <td>8151.825000</td>
      <td>1.310202</td>
    </tr>
    <tr>
      <th>15</th>
      <td>2013</td>
      <td>25</td>
      <td>8941.800000</td>
      <td>1.341098</td>
    </tr>
    <tr>
      <th>16</th>
      <td>2014</td>
      <td>12</td>
      <td>8643.750000</td>
      <td>1.354852</td>
    </tr>
    <tr>
      <th>17</th>
      <td>2015</td>
      <td>16</td>
      <td>8083.937500</td>
      <td>1.347765</td>
    </tr>
    <tr>
      <th>18</th>
      <td>2016</td>
      <td>23</td>
      <td>7809.739130</td>
      <td>1.340597</td>
    </tr>
    <tr>
      <th>19</th>
      <td>2017</td>
      <td>25</td>
      <td>7646.240000</td>
      <td>1.348320</td>
    </tr>
  </tbody>
</table>
</div>



Quick breakdown on the progress, merged the dataframes, and added count of transactions just for extras. We see that there is a spike in transactions during 2007, 2008. Now we will have to adjust the price to inflation pegged to 1998.


```python
PVdf['mean_adjusted'] = PVdf['mean']/PVdf['value']
PVdf.iloc[np.r_[0:2, -2:0]]
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>year</th>
      <th>count</th>
      <th>mean</th>
      <th>value</th>
      <th>mean_adjusted</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1998</td>
      <td>4</td>
      <td>5103.250000</td>
      <td>1.000000</td>
      <td>5103.250000</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1999</td>
      <td>41</td>
      <td>5860.756098</td>
      <td>1.000230</td>
      <td>5859.406315</td>
    </tr>
    <tr>
      <th>18</th>
      <td>2016</td>
      <td>23</td>
      <td>7809.739130</td>
      <td>1.340597</td>
      <td>5825.570277</td>
    </tr>
    <tr>
      <th>19</th>
      <td>2017</td>
      <td>25</td>
      <td>7646.240000</td>
      <td>1.348320</td>
      <td>5670.936999</td>
    </tr>
  </tbody>
</table>
</div>




```python
#ax = sns.lineplot(PVdf.year,PVdf.mean_adjusted)
#plt.show()
# trace = go.Scatter(
#     x = PVdf.mean_adjusted,
#     y = PVdf.year,
#     mode = 'lines+markers',
#     name = 'lines+markers'
# )

# data = [trace]
# #plotly.offline.plot(data, filename='line-mode.html')
# plotly.offline.iplot(data, filename='line-mode')
#iplot(x=[1,2,3],y=[3,4,5])])
N = 100
random_x = np.linspace(0, 1, N)
random_y0 = np.random.randn(N)+5
random_y1 = np.random.randn(N)
random_y2 = np.random.randn(N)-5

trace =  go.Scatter(
    x =  PVdf.year,
    y = PVdf.mean_adjusted,
    mode = 'lines+markers',
    name = 'lines+markers',
  
)
layout = go.Layout(
    title='Property Appreciation',
    xaxis=dict(
        title='Year',
        titlefont=dict(
            family='Courier New, monospace',
            size=18,
            color='#7f7f7f'
        )
    ),
    yaxis=dict(
        title=' Mean PSM price($)',
        titlefont=dict(
            family='Courier New, monospace',
            size=18,
            color='#7f7f7f'
        )
    )
)

data = [trace]
fig = go.Figure(data=data, layout=layout)

py.iplot(fig, filename='propertyApp')
```


    ---------------------------------------------------------------------------

    NameError                                 Traceback (most recent call last)

    <ipython-input-29-72ffc62f1147> in <module>()
         48 fig = go.Figure(data=data, layout=layout)
         49 
    ---> 50 py.iplot(fig, filename='propertyApp')
    

    NameError: name 'py' is not defined


So here we even in the case of a property with little development in the region, price of property is able to maintain its value.<br/><br/> <br/> <img src="calc.png"><br/>



Hence, to calculate the cost between the two. For Renting, we will plot a linear cost of 21.70(PSM) for 20 years, while for owning, will be a linear cost with $5103 / (12 X 20) for simplicity, with a slight offset in the appreciation of the property from 1998 to 2017. We will be assuming 4% interest.


```python
propertyAppreciation =(PVdf.at[19, 'mean_adjusted']-PVdf.at[0, 'mean_adjusted'])/20
interestPeryear=2318/20
salePropertyCostlist=[]
rentCostlist=[]
for i in range(1,21):
    rentCostlist.append(rentPerMonthPSM*i)
    salePropertyCostlist.append((interestPeryear-propertyAppreciation)*i)

traceRent =  go.Scatter(
    x =  PVdf.year,
    y = rentCostlist,
    mode = 'lines+markers',
    name = 'Renting',
  
)

traceSale =  go.Scatter(
    x =  PVdf.year,
    y = salePropertyCostlist,
    mode = 'lines+markers',
    name = 'Owning',
  
)
layout = go.Layout(
    title='Cost of Owning vs Renting',
    xaxis=dict(
        title='Year',
        titlefont=dict(
            family='Courier New, monospace',
            size=18,
            color='#7f7f7f'
        )
    ),
    yaxis=dict(
        title=' cost ($)',
        titlefont=dict(
            family='Courier New, monospace',
            size=18,
            color='#7f7f7f'
        )
    )
)

data1 = [traceRent,traceSale]
fig1 = go.Figure(data=data1, layout=layout)

py.iplot(fig1, filename='OwnVSRent')
```

Remember, this is the cost PSM. Assuming that you bought a property of 100 Square meter, it would be a hundred fold. Leaving you in a deficit of over a $100,000.  
