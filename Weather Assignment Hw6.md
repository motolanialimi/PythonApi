
Weather - Working with APIs

Objective: Create a Python script to visualize the weather of 500+ cities across the world of varying distance from the equator.

-Observed Trend 1: When we look at the scatter plot it shows us that the temperature increases as the latitude increases overtime  and then temperature decreases as we move farther along the latitude. The shape and direction of this graph possibly points towards a NON-LINEAR RELATIONSHIP (Reference: Temperature (F) vs. Latitude). 

-Observed Trend 2: When we look at this plot, it doesn't show any significant pattern that shows that there is a relationship, the scatter plots in this plot tells us that there is NO RELATIONSHIP . (Reference: Cloudiness (%) vs. Latitude).

-Observed Trend 3: The wind speeds is higher in regards to the average as we move farther from the equator, see (Scatter Plot: 'Wind Speed (mph)' vs. Latitude).



```python
import pandas as pd
import numpy as np
import requests as req 
import json
import seaborn as sns
import matplotlib.pyplot as plt
from citipy import citipy
from config import API_KEY
import time 
import random
%matplotlib inline
```


```python
#Generating random list for latitudes and longitudes
latitude = []
longitude = []

for lat in np.random.randint(-90,90,1000):
    latitude.append(lat)

for long in np.random.randint(-180,180,800):
    longitude.append(long)
```


```python
latlong = tuple(zip(latitude,longitude))
```


```python
#Locating nearest city
cities = []
country = []

for lat,long in latlong:
    city = citipy.nearest_city(lat,long)
    cityname = city.city_name
    cities.append(cityname)
    country.append(city.country_code)
```


```python
#Different cities generated
print(len(set(cities)))
```

    392
    


```python
#Store all the cities & countries generated with citipy
df_countries = pd.DataFrame({'Cities': cities,
              'Country': country})

#Storing additional column to information from openweathermap api
df_countries['Latitude'] = ''
df_countries['Longitude'] = ''
df_countries['Temperature (F)'] = ''
df_countries['Humidity (%)'] = ''
df_countries['Cloudiness (%)'] = ''
df_countries['Wind Speed (mph)'] = ''
df_countries.head()
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
      <th>Cities</th>
      <th>Country</th>
      <th>Latitude</th>
      <th>Longitude</th>
      <th>Temperature (F)</th>
      <th>Humidity (%)</th>
      <th>Cloudiness (%)</th>
      <th>Wind Speed (mph)</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>mar del plata</td>
      <td>ar</td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>1</th>
      <td>bluff</td>
      <td>nz</td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>2</th>
      <td>biak</td>
      <td>id</td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>3</th>
      <td>myanaung</td>
      <td>mm</td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>4</th>
      <td>pangnirtung</td>
      <td>ca</td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
  </tbody>
</table>
</div>




```python
#Fill in values for blank columns in dataframe and looping through all rows

for index,row in df_countries.iterrows():
    city_name = row['Cities']
    Country_id = row['Country']
    
    #url = "http://api.openweathermap.org/data/2.5/forecast/daily?id=524901&lang=zh_cn"
    
    #api call for all Cities 
    url = "http://api.openweathermap.org/data/2.5/forecast" \
    "?q={},{}&units=IMPERIAL&mode=json&APPID={}".format(city_name,Country_id,API_KEY)
    
    country_info = req.get(url).json()
    
    #print all api url's for each city
    print(url)
    
    
    try:
        df_countries.set_value(index,'Latitude',country_info['city']['coord']['lat'])
        df_countries.set_value(index,'Longitude',country_info['city']['coord']['lon'])
        df_countries.set_value(index,'Temperature (F)',country_info['list'][0]['main']['temp'])
        df_countries.set_value(index,'Humidity (%)',country_info['list'][0]['main']['humidity'])
        df_countries.set_value(index,'Cloudiness (%)',country_info['list'][0]['clouds']['all'])
        df_countries.set_value(index,'Wind Speed (mph)',country_info['list'][0]['wind']['speed'])
        
    except KeyError:
        df_countries.set_value(index,'Latitude',np.nan)
        df_countries.set_value(index,'Longitude',np.nan)
        df_countries.set_value(index,'Temperature (F)',np.nan)
        df_countries.set_value(index,'Humidity (%)',np.nan)
        df_countries.set_value(index,'Cloudiness (%)',np.nan)
        df_countries.set_value(index,'Wind Speed (mph)',np.nan)
        
        print('Missing weather information...skip')
```

    http://api.openweathermap.org/data/2.5/forecast?q=mar del plata,ar&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    

    C:\Users\Owner\Anaconda3\envs\PythonData2\lib\site-packages\ipykernel\__main__.py:19: FutureWarning: set_value is deprecated and will be removed in a future release. Please use .at[] or .iat[] accessors instead
    C:\Users\Owner\Anaconda3\envs\PythonData2\lib\site-packages\ipykernel\__main__.py:20: FutureWarning: set_value is deprecated and will be removed in a future release. Please use .at[] or .iat[] accessors instead
    C:\Users\Owner\Anaconda3\envs\PythonData2\lib\site-packages\ipykernel\__main__.py:21: FutureWarning: set_value is deprecated and will be removed in a future release. Please use .at[] or .iat[] accessors instead
    C:\Users\Owner\Anaconda3\envs\PythonData2\lib\site-packages\ipykernel\__main__.py:22: FutureWarning: set_value is deprecated and will be removed in a future release. Please use .at[] or .iat[] accessors instead
    C:\Users\Owner\Anaconda3\envs\PythonData2\lib\site-packages\ipykernel\__main__.py:23: FutureWarning: set_value is deprecated and will be removed in a future release. Please use .at[] or .iat[] accessors instead
    C:\Users\Owner\Anaconda3\envs\PythonData2\lib\site-packages\ipykernel\__main__.py:24: FutureWarning: set_value is deprecated and will be removed in a future release. Please use .at[] or .iat[] accessors instead
    

    http://api.openweathermap.org/data/2.5/forecast?q=bluff,nz&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=biak,id&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=myanaung,mm&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=pangnirtung,ca&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=vaini,to&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=bethel,us&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=yumen,cn&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=upernavik,gl&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=komsomolskiy,ru&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=new norfolk,au&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=barrow,us&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=ushuaia,ar&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=ushuaia,ar&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=kruisfontein,za&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=illoqqortoormiut,gl&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    Missing weather information...skip
    

    C:\Users\Owner\Anaconda3\envs\PythonData2\lib\site-packages\ipykernel\__main__.py:27: FutureWarning: set_value is deprecated and will be removed in a future release. Please use .at[] or .iat[] accessors instead
    C:\Users\Owner\Anaconda3\envs\PythonData2\lib\site-packages\ipykernel\__main__.py:28: FutureWarning: set_value is deprecated and will be removed in a future release. Please use .at[] or .iat[] accessors instead
    C:\Users\Owner\Anaconda3\envs\PythonData2\lib\site-packages\ipykernel\__main__.py:29: FutureWarning: set_value is deprecated and will be removed in a future release. Please use .at[] or .iat[] accessors instead
    C:\Users\Owner\Anaconda3\envs\PythonData2\lib\site-packages\ipykernel\__main__.py:30: FutureWarning: set_value is deprecated and will be removed in a future release. Please use .at[] or .iat[] accessors instead
    C:\Users\Owner\Anaconda3\envs\PythonData2\lib\site-packages\ipykernel\__main__.py:31: FutureWarning: set_value is deprecated and will be removed in a future release. Please use .at[] or .iat[] accessors instead
    C:\Users\Owner\Anaconda3\envs\PythonData2\lib\site-packages\ipykernel\__main__.py:32: FutureWarning: set_value is deprecated and will be removed in a future release. Please use .at[] or .iat[] accessors instead
    

    http://api.openweathermap.org/data/2.5/forecast?q=sao filipe,cv&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=poum,nc&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=havelock,us&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=new norfolk,au&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=cabo san lucas,mx&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=clyde river,ca&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=punta arenas,cl&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=ushuaia,ar&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=khatanga,ru&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=vaini,to&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=suileng,cn&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=thinadhoo,mv&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=hobart,au&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=rikitea,pf&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=chara,ru&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=mataura,pf&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    Missing weather information...skip
    http://api.openweathermap.org/data/2.5/forecast?q=beloha,mg&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=new norfolk,au&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=bredasdorp,za&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=charters towers,au&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=oranjemund,na&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=albany,au&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=hermanus,za&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=saint-philippe,re&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=saint-pierre,re&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=rikitea,pf&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=butaritari,ki&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=cape town,za&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=cherskiy,ru&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=nikolskoye,ru&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=mandurah,au&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=vaini,to&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=qaanaaq,gl&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=rikitea,pf&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=cape town,za&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=illoqqortoormiut,gl&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    Missing weather information...skip
    http://api.openweathermap.org/data/2.5/forecast?q=severo-kurilsk,ru&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=lagunas,pe&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=rikitea,pf&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=esperance,au&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=ushuaia,ar&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=puri,in&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=vostok,ru&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=port elizabeth,za&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=bredasdorp,za&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=tombouctou,ml&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=avarua,ck&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=victoria,sc&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=hirara,jp&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=broken hill,au&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=ugoofaaru,mv&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=busselton,au&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=mar del plata,ar&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=bhadrachalam,in&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=lewisville,us&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=yellowknife,ca&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=mataura,pf&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    Missing weather information...skip
    http://api.openweathermap.org/data/2.5/forecast?q=bluff,nz&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=honiara,sb&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=rikitea,pf&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=east london,za&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=bredasdorp,za&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=tsihombe,mg&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    Missing weather information...skip
    http://api.openweathermap.org/data/2.5/forecast?q=salym,ru&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=dikson,ru&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=mandera,ke&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=victoria,sc&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=saldanha,za&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=taolanaro,mg&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    Missing weather information...skip
    http://api.openweathermap.org/data/2.5/forecast?q=puri,in&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=kagadi,ug&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=rikitea,pf&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=andros town,bs&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=mattru,sl&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=carnarvon,au&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=east london,za&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=avarua,ck&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=rikitea,pf&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=barentsburg,sj&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    Missing weather information...skip
    http://api.openweathermap.org/data/2.5/forecast?q=rikitea,pf&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=castro,cl&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=mount gambier,au&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=naze,jp&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=rikitea,pf&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=amod,in&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=moose factory,ca&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=bengkulu,id&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    Missing weather information...skip
    http://api.openweathermap.org/data/2.5/forecast?q=dehloran,ir&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=bredasdorp,za&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=aksu,cn&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=cape town,za&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=bluff,nz&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=srednekolymsk,ru&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=naze,jp&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=omboue,ga&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=snihurivka,ua&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=luderitz,na&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=yakeshi,cn&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=longyearbyen,sj&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=ust-barguzin,ru&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=aklavik,ca&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=avera,pf&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    Missing weather information...skip
    http://api.openweathermap.org/data/2.5/forecast?q=nykobing,dk&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    Missing weather information...skip
    http://api.openweathermap.org/data/2.5/forecast?q=winslow,us&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=tumannyy,ru&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    Missing weather information...skip
    http://api.openweathermap.org/data/2.5/forecast?q=hualmay,pe&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=tuktoyaktuk,ca&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=broome,au&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=nikolskoye,ru&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=tuktoyaktuk,ca&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=bredasdorp,za&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=butaritari,ki&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=clearwater,us&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=kavaratti,in&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=mahebourg,mu&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=ushuaia,ar&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=tuktoyaktuk,ca&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=ketchikan,us&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=oranjestad,aw&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=ostrovnoy,ru&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=vaini,to&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=souillac,mu&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=yellowknife,ca&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=sitka,us&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=saint-philippe,re&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=constitucion,mx&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=ushuaia,ar&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=pucallpa,pe&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=padang,id&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=hilo,us&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=monrovia,lr&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=cape town,za&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=rikitea,pf&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=wairoa,nz&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=tecoanapa,mx&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=mys shmidta,ru&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    Missing weather information...skip
    http://api.openweathermap.org/data/2.5/forecast?q=asau,tv&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    Missing weather information...skip
    http://api.openweathermap.org/data/2.5/forecast?q=dingle,ie&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=ushuaia,ar&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=teotitlan,mx&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    Missing weather information...skip
    http://api.openweathermap.org/data/2.5/forecast?q=ambon,id&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=jamestown,sh&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=mangrol,in&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=cape town,za&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=longyearbyen,sj&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=mar del plata,ar&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=bluff,nz&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=birjand,ir&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=kuldiga,lv&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=mahebourg,mu&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=puerto ayora,ec&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=avarua,ck&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=cherskiy,ru&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=nikolskoye,ru&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=port elizabeth,za&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=puerto ayora,ec&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=port alfred,za&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=atuona,pf&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=bredasdorp,za&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=yining,cn&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=cape town,za&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=champerico,gt&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=umba,ru&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=itarema,br&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=mulchen,cl&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=jamestown,sh&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=butaritari,ki&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=chuy,uy&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=knoxville,us&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=tsihombe,mg&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    Missing weather information...skip
    http://api.openweathermap.org/data/2.5/forecast?q=hobart,au&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=kodiak,us&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=atuona,pf&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=albany,au&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=mangrol,in&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=troitskoye,ru&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=ulety,ru&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=busselton,au&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=benghazi,ly&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=barentsburg,sj&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    Missing weather information...skip
    http://api.openweathermap.org/data/2.5/forecast?q=clyde river,ca&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=rikitea,pf&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=vanimo,pg&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=yellowknife,ca&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=alotau,pg&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    Missing weather information...skip
    http://api.openweathermap.org/data/2.5/forecast?q=port elizabeth,za&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=zheleznodorozhnyy,ru&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=sokode,tg&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=calama,cl&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=nikolskoye,ru&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=florence,us&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=zalaszentgrot,hu&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=jamestown,sh&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=port alfred,za&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=fatezh,ru&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=codrington,ag&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    Missing weather information...skip
    http://api.openweathermap.org/data/2.5/forecast?q=butaritari,ki&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=ribeira grande,pt&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=coihueco,cl&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=port alfred,za&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=ushuaia,ar&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=pevek,ru&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=yulara,au&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=chuy,uy&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=nanortalik,gl&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=sale,au&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=thinadhoo,mv&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=comodoro rivadavia,ar&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=kyren,ru&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=high level,ca&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=moron,mn&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=raduzhnyy,ru&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=cidreira,br&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=vaitupu,wf&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    Missing weather information...skip
    http://api.openweathermap.org/data/2.5/forecast?q=constitucion,cl&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=ushuaia,ar&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=puerto ayora,ec&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=hay river,ca&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=laguna,br&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    Missing weather information...skip
    http://api.openweathermap.org/data/2.5/forecast?q=tasiilaq,gl&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=auka,hn&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=mataura,pf&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    Missing weather information...skip
    http://api.openweathermap.org/data/2.5/forecast?q=ushuaia,ar&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=kutum,sd&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=tuktoyaktuk,ca&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=smithers,ca&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=kavieng,pg&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=kavali,in&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=hambantota,lk&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=port alfred,za&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=castro,cl&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=lorengau,pg&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=new norfolk,au&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=buala,sb&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=vaitape,pf&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=cartagena,co&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=taolanaro,mg&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    Missing weather information...skip
    http://api.openweathermap.org/data/2.5/forecast?q=rikitea,pf&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=albany,au&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=ushuaia,ar&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=yuzhno-kurilsk,ru&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=taolanaro,mg&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    Missing weather information...skip
    http://api.openweathermap.org/data/2.5/forecast?q=ahipara,nz&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=khatanga,ru&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=rikitea,pf&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=rikitea,pf&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=hithadhoo,mv&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=zdvinsk,ru&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=fort nelson,ca&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=cape town,za&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=pangai,to&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=haines junction,ca&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=bluff,nz&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=mocuba,mz&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=clarence town,bs&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=shingu,jp&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=atuona,pf&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=taolanaro,mg&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    Missing weather information...skip
    http://api.openweathermap.org/data/2.5/forecast?q=hobart,au&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=lebu,cl&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=rikitea,pf&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=illoqqortoormiut,gl&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    Missing weather information...skip
    http://api.openweathermap.org/data/2.5/forecast?q=mataura,pf&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    Missing weather information...skip
    http://api.openweathermap.org/data/2.5/forecast?q=yeppoon,au&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=new norfolk,au&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=goiatuba,br&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=tuktoyaktuk,ca&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=mar del plata,ar&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=touros,br&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=ulaanbaatar,mn&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=hilo,us&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=lebu,cl&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=mataura,pf&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    Missing weather information...skip
    http://api.openweathermap.org/data/2.5/forecast?q=knysna,za&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=whitehorse,ca&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=leningradskiy,ru&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=tuktoyaktuk,ca&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=yellowknife,ca&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=khatanga,ru&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=ranong,th&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=provideniya,ru&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=chuy,uy&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=portland,au&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=mutsamudu,km&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    Missing weather information...skip
    http://api.openweathermap.org/data/2.5/forecast?q=kapaa,us&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=balad,iq&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=port alfred,za&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=norman wells,ca&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=hobart,au&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=punta arenas,cl&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=ushuaia,ar&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=hanmer springs,nz&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=saint-francois,gp&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=albany,au&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=punta arenas,cl&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=somotillo,ni&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=attawapiskat,ca&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    Missing weather information...skip
    http://api.openweathermap.org/data/2.5/forecast?q=bargal,so&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    Missing weather information...skip
    http://api.openweathermap.org/data/2.5/forecast?q=kununurra,au&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=fortuna,us&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=messina,za&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=kalmunai,lk&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=new norfolk,au&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=zeya,ru&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=jamestown,sh&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=grand gaube,mu&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=bonavista,ca&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=thompson,ca&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=sinnamary,gf&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=beloha,mg&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=talnakh,ru&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=bluff,nz&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=norman wells,ca&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=mataura,pf&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    Missing weather information...skip
    http://api.openweathermap.org/data/2.5/forecast?q=busselton,au&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=provideniya,ru&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=tumen,cn&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=cidreira,br&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=atuona,pf&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=ushuaia,ar&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=provideniya,ru&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=vaini,to&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=jamestown,sh&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=sitka,us&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=hasaki,jp&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=biloela,au&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=san ramon,bo&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=new norfolk,au&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=punta arenas,cl&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=pisco,pe&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=tiksi,ru&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=vestmannaeyjar,is&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=busselton,au&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=albany,au&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=clyde river,ca&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=nauta,pe&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=bluff,nz&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=ulverstone,au&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=illoqqortoormiut,gl&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    Missing weather information...skip
    http://api.openweathermap.org/data/2.5/forecast?q=jamestown,sh&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=atuona,pf&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=chicama,pe&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=codrington,ag&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    Missing weather information...skip
    http://api.openweathermap.org/data/2.5/forecast?q=ostrovnoy,ru&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=mamou,gn&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=puksoozero,ru&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=ushuaia,ar&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=hasaki,jp&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=skagastrond,is&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    Missing weather information...skip
    http://api.openweathermap.org/data/2.5/forecast?q=bambous virieux,mu&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=fort nelson,ca&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=coihaique,cl&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=barrow,us&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=lyaskelya,ru&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=alice springs,au&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=jamestown,sh&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=punta arenas,cl&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=ribeira grande,pt&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=pangnirtung,ca&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=kapaa,us&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=malinyi,tz&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=olafsvik,is&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    Missing weather information...skip
    http://api.openweathermap.org/data/2.5/forecast?q=along,in&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=mahebourg,mu&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=oranjemund,na&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=busselton,au&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=punta arenas,cl&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=kirakira,sb&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=van,tr&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=kangaatsiaq,gl&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=mabaruma,gy&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=te anau,nz&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=mackenzie,ca&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=mouzakion,gr&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    Missing weather information...skip
    http://api.openweathermap.org/data/2.5/forecast?q=punta arenas,cl&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=san cristobal,ec&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=ushuaia,ar&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=mahebourg,mu&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=albany,au&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=mys shmidta,ru&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    Missing weather information...skip
    http://api.openweathermap.org/data/2.5/forecast?q=atuona,pf&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=albany,au&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=bambous virieux,mu&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=college,us&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=busselton,au&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=mehamn,no&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=kavieng,pg&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=illoqqortoormiut,gl&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    Missing weather information...skip
    http://api.openweathermap.org/data/2.5/forecast?q=ushuaia,ar&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=bredasdorp,za&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=ushuaia,ar&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=jamestown,sh&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=amderma,ru&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    Missing weather information...skip
    http://api.openweathermap.org/data/2.5/forecast?q=ushuaia,ar&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=sarkand,kz&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=semnan,ir&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=pyay,mm&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=ailigandi,pa&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=hobart,au&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=albany,au&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=tashtyp,ru&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=husavik,is&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=hualmay,pe&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=ushuaia,ar&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=thomasville,us&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=lorengau,pg&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=chuy,uy&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=bredasdorp,za&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=illoqqortoormiut,gl&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    Missing weather information...skip
    http://api.openweathermap.org/data/2.5/forecast?q=ilulissat,gl&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=khatanga,ru&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=tigzirt,dz&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=coquimbo,cl&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=kaitangata,nz&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=maningrida,au&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=algiers,dz&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=lavrentiya,ru&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=hermanus,za&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=arraial do cabo,br&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=cayenne,gf&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=punta arenas,cl&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=ushuaia,ar&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=yerbogachen,ru&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=eydhafushi,mv&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=jamestown,sh&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=mogadishu,so&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=vestmanna,fo&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=bambous virieux,mu&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=sao joao da barra,br&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=chulym,ru&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=jamestown,sh&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=pulaski,us&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=busselton,au&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=puerto ayora,ec&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=jamestown,sh&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=atuona,pf&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=tiksi,ru&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=mataura,pf&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    Missing weather information...skip
    http://api.openweathermap.org/data/2.5/forecast?q=arraial do cabo,br&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=taolanaro,mg&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    Missing weather information...skip
    http://api.openweathermap.org/data/2.5/forecast?q=seoul,kr&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=atar,mr&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=ajdabiya,ly&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=severo-kurilsk,ru&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=albany,au&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=bredasdorp,za&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=san lorenzo,ar&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=lata,sb&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    Missing weather information...skip
    http://api.openweathermap.org/data/2.5/forecast?q=rikitea,pf&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=port blair,in&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=punta arenas,cl&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=rikitea,pf&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=dunedin,nz&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=cape town,za&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=salalah,om&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=kodiak,us&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=dikson,ru&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=clyde river,ca&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=rocha,uy&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=warrnambool,au&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=bluff,nz&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=tambacounda,sn&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=kaitangata,nz&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=carnarvon,au&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=naze,jp&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=tiarei,pf&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=nkhotakota,mw&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=longyearbyen,sj&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=qaanaaq,gl&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=yakima,us&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=dalvik,is&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=hofn,is&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=illoqqortoormiut,gl&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    Missing weather information...skip
    http://api.openweathermap.org/data/2.5/forecast?q=labuhan,id&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=kahului,us&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=port alfred,za&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=port-cartier,ca&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=kapaa,us&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=tromso,no&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=kapaa,us&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=gazanjyk,tm&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=marzuq,ly&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    Missing weather information...skip
    http://api.openweathermap.org/data/2.5/forecast?q=bluff,nz&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=bulolo,pg&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=kapaa,us&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=qaanaaq,gl&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=nokaneng,bw&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=illoqqortoormiut,gl&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    Missing weather information...skip
    http://api.openweathermap.org/data/2.5/forecast?q=fort nelson,ca&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=dikson,ru&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=upernavik,gl&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=avarua,ck&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=ancud,cl&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=cape town,za&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=shenjiamen,cn&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=chokurdakh,ru&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=airai,pw&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    Missing weather information...skip
    http://api.openweathermap.org/data/2.5/forecast?q=union de san antonio,mx&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=ushuaia,ar&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=mullaitivu,lk&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    Missing weather information...skip
    http://api.openweathermap.org/data/2.5/forecast?q=indianola,us&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=rikitea,pf&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=santa marta,co&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=salamiyah,sy&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=ushuaia,ar&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=moose factory,ca&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=hilo,us&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=busselton,au&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=kirakira,sb&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=mitsamiouli,km&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=komsomolskiy,ru&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=rikitea,pf&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=wairoa,nz&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=kodiak,us&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=chitral,pk&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=albany,au&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=lorengau,pg&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=san quintin,mx&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    Missing weather information...skip
    http://api.openweathermap.org/data/2.5/forecast?q=grindavik,is&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=airai,pw&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    Missing weather information...skip
    http://api.openweathermap.org/data/2.5/forecast?q=hermanus,za&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=mar del plata,ar&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=maragogi,br&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=iskateley,ru&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=bredasdorp,za&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=hilo,us&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=longyearbyen,sj&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=rikitea,pf&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=harnai,pk&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=chaohu,cn&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=eydhafushi,mv&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=mataura,pf&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    Missing weather information...skip
    http://api.openweathermap.org/data/2.5/forecast?q=ancud,cl&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=rikitea,pf&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=constitucion,mx&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=jamestown,sh&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=kaitangata,nz&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=kodiak,us&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=tabas,ir&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=punta arenas,cl&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=endicott,us&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=atuona,pf&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=illoqqortoormiut,gl&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    Missing weather information...skip
    http://api.openweathermap.org/data/2.5/forecast?q=longyearbyen,sj&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=bredasdorp,za&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=fortuna,us&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=ilulissat,gl&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=masingbi,sl&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=adrar,dz&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=irara,br&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=umzimvubu,za&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    Missing weather information...skip
    http://api.openweathermap.org/data/2.5/forecast?q=manokwari,id&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=bluff,nz&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=new smyrna beach,us&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=kieta,pg&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=sao filipe,cv&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=cedar city,us&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=florence,us&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=cabo san lucas,mx&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=surgut,ru&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=torbay,ca&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=kodiak,us&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=san ramon de la nueva oran,ar&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=nikolskoye,ru&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=klaksvik,fo&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=ushuaia,ar&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=punta arenas,cl&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=avarua,ck&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=tuatapere,nz&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=port alfred,za&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=ushuaia,ar&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=ushtobe,kz&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=airai,pw&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    Missing weather information...skip
    http://api.openweathermap.org/data/2.5/forecast?q=vaini,to&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=carauari,br&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=saskylakh,ru&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=severo-kurilsk,ru&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=bluff,nz&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=erenhot,cn&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=formoso do araguaia,br&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    Missing weather information...skip
    http://api.openweathermap.org/data/2.5/forecast?q=mys shmidta,ru&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    Missing weather information...skip
    http://api.openweathermap.org/data/2.5/forecast?q=lagoa,pt&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=albany,au&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=lima,pe&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=kushmurun,kz&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    Missing weather information...skip
    http://api.openweathermap.org/data/2.5/forecast?q=ushuaia,ar&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=tiksi,ru&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=tasiilaq,gl&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=sajanan,tn&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=iqaluit,ca&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=yellowknife,ca&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=busselton,au&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=busselton,au&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=bengkulu,id&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    Missing weather information...skip
    http://api.openweathermap.org/data/2.5/forecast?q=half moon bay,us&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=kysyl-syr,ru&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=pevek,ru&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=asyut,eg&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=esperance,au&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=east london,za&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=rock sound,bs&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=hermanus,za&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=east london,za&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=ancud,cl&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=upernavik,gl&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=ust-barguzin,ru&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=samagaltay,ru&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=hermanus,za&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=yellowknife,ca&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=sao sebastiao,br&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=torbay,ca&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=bethel,us&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=hervey bay,au&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=puerto ayora,ec&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=rikitea,pf&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=sao filipe,cv&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=cape town,za&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=carnarvon,au&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=atuona,pf&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=chokurdakh,ru&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=mahebourg,mu&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=durango,us&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=fougamou,ga&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=amderma,ru&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    Missing weather information...skip
    http://api.openweathermap.org/data/2.5/forecast?q=avarua,ck&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=butaritari,ki&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=vao,nc&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=skjervoy,no&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=rikitea,pf&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=cape town,za&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=saint george,bm&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=havelock,us&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=avera,pf&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    Missing weather information...skip
    http://api.openweathermap.org/data/2.5/forecast?q=saint-joseph,re&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=port alfred,za&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=cayenne,gf&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=kidal,ml&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=hilo,us&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=busselton,au&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=punta arenas,cl&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=la ronge,ca&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=vaini,to&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=palabuhanratu,id&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    Missing weather information...skip
    http://api.openweathermap.org/data/2.5/forecast?q=rikitea,pf&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=tasiilaq,gl&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=bredasdorp,za&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=lompoc,us&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=havoysund,no&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=myre,no&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=bud,no&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=mataura,pf&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    Missing weather information...skip
    http://api.openweathermap.org/data/2.5/forecast?q=bathsheba,bb&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=castro,cl&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=ushuaia,ar&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=busselton,au&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=chokurdakh,ru&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=bathsheba,bb&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=upernavik,gl&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=punta arenas,cl&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=punta arenas,cl&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=namatanai,pg&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=rikitea,pf&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=busselton,au&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=norman wells,ca&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=punta arenas,cl&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=vytegra,ru&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=andros town,bs&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=hovd,mn&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=iquique,cl&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=pevek,ru&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=saskylakh,ru&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=kaitangata,nz&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=ushuaia,ar&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=pointe michel,dm&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=albany,au&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=cherskiy,ru&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=varhaug,no&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=hobart,au&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=whakatane,nz&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=aklavik,ca&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=cape town,za&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=yelovo,ru&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=tuktoyaktuk,ca&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=taolanaro,mg&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    Missing weather information...skip
    http://api.openweathermap.org/data/2.5/forecast?q=rikitea,pf&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=pedernales,do&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=henties bay,na&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=butaritari,ki&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=mahajanga,mg&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=tiznit,ma&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=bredasdorp,za&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=kapaa,us&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=tainan,tw&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=sergeyevka,kz&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=wisconsin rapids,us&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=mirina,gr&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    Missing weather information...skip
    http://api.openweathermap.org/data/2.5/forecast?q=lufilufi,ws&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=rocha,uy&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=san cristobal,ec&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=georgetown,sh&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=bur gabo,so&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    Missing weather information...skip
    http://api.openweathermap.org/data/2.5/forecast?q=avarua,ck&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=torbay,ca&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=saryshagan,kz&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    Missing weather information...skip
    http://api.openweathermap.org/data/2.5/forecast?q=ushuaia,ar&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=avarua,ck&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=chuy,uy&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=muros,es&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=bilibino,ru&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=north bend,us&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=taolanaro,mg&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    Missing weather information...skip
    http://api.openweathermap.org/data/2.5/forecast?q=wahran,dz&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    Missing weather information...skip
    http://api.openweathermap.org/data/2.5/forecast?q=pevek,ru&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=nuevitas,cu&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=luderitz,na&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=ribeira grande,pt&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=atuona,pf&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=torbay,ca&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=rikitea,pf&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=bathsheba,bb&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=yellowknife,ca&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=bonavista,ca&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=punta arenas,cl&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=new norfolk,au&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=basco,ph&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=taolanaro,mg&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    Missing weather information...skip
    http://api.openweathermap.org/data/2.5/forecast?q=cehegin,es&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=sentyabrskiy,ru&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    Missing weather information...skip
    http://api.openweathermap.org/data/2.5/forecast?q=havre-saint-pierre,ca&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=saint-louis,re&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=saldanha,za&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=cardoso,br&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=busselton,au&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=punta arenas,cl&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=cortez,us&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=ushuaia,ar&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=nauta,pe&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=tuatapere,nz&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=norman wells,ca&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=bethel,us&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=kapaa,us&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=qaanaaq,gl&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=tsihombe,mg&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    Missing weather information...skip
    http://api.openweathermap.org/data/2.5/forecast?q=poum,nc&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=tual,id&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=punta arenas,cl&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=puerto ayora,ec&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=mandalgovi,mn&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=kodiak,us&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=bilma,ne&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=provideniya,ru&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=vaini,to&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=lagoa,pt&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=hermanus,za&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=mataura,pf&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    Missing weather information...skip
    http://api.openweathermap.org/data/2.5/forecast?q=tiksi,ru&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=kununurra,au&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=hobyo,so&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=iqaluit,ca&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=saryshagan,kz&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    Missing weather information...skip
    http://api.openweathermap.org/data/2.5/forecast?q=taolanaro,mg&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    Missing weather information...skip
    http://api.openweathermap.org/data/2.5/forecast?q=honavar,in&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=kodiak,us&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=albany,au&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=mujiayingzi,cn&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=hualmay,pe&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=virginia beach,us&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=nikolskoye,ru&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=jamestown,sh&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=mataura,pf&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    Missing weather information...skip
    http://api.openweathermap.org/data/2.5/forecast?q=albany,au&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=kenai,us&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=puerto ayora,ec&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=bredasdorp,za&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=barentsburg,sj&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    Missing weather information...skip
    http://api.openweathermap.org/data/2.5/forecast?q=rikitea,pf&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=tuktoyaktuk,ca&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=constantine,dz&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=kodiak,us&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=mbekenyera,tz&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=taolanaro,mg&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    Missing weather information...skip
    http://api.openweathermap.org/data/2.5/forecast?q=biltine,td&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=chuy,uy&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    http://api.openweathermap.org/data/2.5/forecast?q=ossora,ru&units=IMPERIAL&mode=json&APPID=94e61178e945180a21c6abaeb6554ca2
    


```python
#changing data recieved from openweathermap api to numerical data
df_countries['Latitude'] = pd.to_numeric(df_countries['Latitude'])
df_countries['Longitude'] = pd.to_numeric(df_countries['Longitude'])
df_countries['Temperature (F)'] = pd.to_numeric(df_countries['Temperature (F)'])
df_countries['Humidity (%)'] = pd.to_numeric(df_countries['Humidity (%)'])
df_countries['Cloudiness (%)'] = pd.to_numeric(df_countries['Cloudiness (%)'])
df_countries['Wind Speed (mph)'] = pd.to_numeric(df_countries['Wind Speed (mph)'])

df_countries
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
      <th>Cities</th>
      <th>Country</th>
      <th>Latitude</th>
      <th>Longitude</th>
      <th>Temperature (F)</th>
      <th>Humidity (%)</th>
      <th>Cloudiness (%)</th>
      <th>Wind Speed (mph)</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>mar del plata</td>
      <td>ar</td>
      <td>-46.4276</td>
      <td>-67.5191</td>
      <td>17.82</td>
      <td>100.0</td>
      <td>0.0</td>
      <td>3.71</td>
    </tr>
    <tr>
      <th>1</th>
      <td>bluff</td>
      <td>nz</td>
      <td>-46.6000</td>
      <td>168.3333</td>
      <td>45.36</td>
      <td>100.0</td>
      <td>8.0</td>
      <td>5.95</td>
    </tr>
    <tr>
      <th>2</th>
      <td>biak</td>
      <td>id</td>
      <td>-0.9132</td>
      <td>122.8766</td>
      <td>76.59</td>
      <td>100.0</td>
      <td>92.0</td>
      <td>4.16</td>
    </tr>
    <tr>
      <th>3</th>
      <td>myanaung</td>
      <td>mm</td>
      <td>18.2833</td>
      <td>95.3167</td>
      <td>80.19</td>
      <td>100.0</td>
      <td>92.0</td>
      <td>8.52</td>
    </tr>
    <tr>
      <th>4</th>
      <td>pangnirtung</td>
      <td>ca</td>
      <td>66.1490</td>
      <td>-65.7179</td>
      <td>24.03</td>
      <td>100.0</td>
      <td>68.0</td>
      <td>4.05</td>
    </tr>
    <tr>
      <th>5</th>
      <td>vaini</td>
      <td>to</td>
      <td>-21.2001</td>
      <td>-175.2000</td>
      <td>76.32</td>
      <td>100.0</td>
      <td>88.0</td>
      <td>15.35</td>
    </tr>
    <tr>
      <th>6</th>
      <td>bethel</td>
      <td>us</td>
      <td>60.7922</td>
      <td>-161.7559</td>
      <td>56.57</td>
      <td>47.0</td>
      <td>0.0</td>
      <td>14.67</td>
    </tr>
    <tr>
      <th>7</th>
      <td>yumen</td>
      <td>cn</td>
      <td>40.2917</td>
      <td>97.0449</td>
      <td>76.68</td>
      <td>25.0</td>
      <td>64.0</td>
      <td>2.48</td>
    </tr>
    <tr>
      <th>8</th>
      <td>upernavik</td>
      <td>gl</td>
      <td>72.7868</td>
      <td>-56.1549</td>
      <td>25.56</td>
      <td>95.0</td>
      <td>20.0</td>
      <td>4.16</td>
    </tr>
    <tr>
      <th>9</th>
      <td>komsomolskiy</td>
      <td>ru</td>
      <td>67.5537</td>
      <td>63.7760</td>
      <td>32.04</td>
      <td>100.0</td>
      <td>68.0</td>
      <td>19.26</td>
    </tr>
    <tr>
      <th>10</th>
      <td>new norfolk</td>
      <td>au</td>
      <td>-42.7802</td>
      <td>147.0615</td>
      <td>53.78</td>
      <td>100.0</td>
      <td>48.0</td>
      <td>2.48</td>
    </tr>
    <tr>
      <th>11</th>
      <td>barrow</td>
      <td>us</td>
      <td>39.5062</td>
      <td>-90.4016</td>
      <td>69.87</td>
      <td>91.0</td>
      <td>0.0</td>
      <td>15.57</td>
    </tr>
    <tr>
      <th>12</th>
      <td>ushuaia</td>
      <td>ar</td>
      <td>-54.8070</td>
      <td>-68.3074</td>
      <td>27.09</td>
      <td>100.0</td>
      <td>44.0</td>
      <td>5.28</td>
    </tr>
    <tr>
      <th>13</th>
      <td>ushuaia</td>
      <td>ar</td>
      <td>-54.8070</td>
      <td>-68.3074</td>
      <td>27.09</td>
      <td>100.0</td>
      <td>44.0</td>
      <td>5.28</td>
    </tr>
    <tr>
      <th>14</th>
      <td>kruisfontein</td>
      <td>za</td>
      <td>-34.0034</td>
      <td>24.7314</td>
      <td>57.06</td>
      <td>99.0</td>
      <td>48.0</td>
      <td>19.15</td>
    </tr>
    <tr>
      <th>15</th>
      <td>illoqqortoormiut</td>
      <td>gl</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>16</th>
      <td>sao filipe</td>
      <td>cv</td>
      <td>14.8961</td>
      <td>-24.4956</td>
      <td>71.37</td>
      <td>100.0</td>
      <td>0.0</td>
      <td>13.78</td>
    </tr>
    <tr>
      <th>17</th>
      <td>poum</td>
      <td>nc</td>
      <td>-20.2334</td>
      <td>164.0167</td>
      <td>72.81</td>
      <td>100.0</td>
      <td>0.0</td>
      <td>10.98</td>
    </tr>
    <tr>
      <th>18</th>
      <td>havelock</td>
      <td>us</td>
      <td>34.8785</td>
      <td>-76.9015</td>
      <td>71.69</td>
      <td>91.0</td>
      <td>24.0</td>
      <td>8.75</td>
    </tr>
    <tr>
      <th>19</th>
      <td>new norfolk</td>
      <td>au</td>
      <td>-42.7802</td>
      <td>147.0615</td>
      <td>53.78</td>
      <td>100.0</td>
      <td>48.0</td>
      <td>2.48</td>
    </tr>
    <tr>
      <th>20</th>
      <td>cabo san lucas</td>
      <td>mx</td>
      <td>22.8938</td>
      <td>-109.9112</td>
      <td>79.45</td>
      <td>99.0</td>
      <td>8.0</td>
      <td>7.40</td>
    </tr>
    <tr>
      <th>21</th>
      <td>clyde river</td>
      <td>ca</td>
      <td>70.4692</td>
      <td>-68.5915</td>
      <td>25.59</td>
      <td>90.0</td>
      <td>56.0</td>
      <td>8.52</td>
    </tr>
    <tr>
      <th>22</th>
      <td>punta arenas</td>
      <td>cl</td>
      <td>-53.1627</td>
      <td>-70.9081</td>
      <td>34.25</td>
      <td>100.0</td>
      <td>0.0</td>
      <td>1.92</td>
    </tr>
    <tr>
      <th>23</th>
      <td>ushuaia</td>
      <td>ar</td>
      <td>-54.8070</td>
      <td>-68.3074</td>
      <td>27.09</td>
      <td>100.0</td>
      <td>44.0</td>
      <td>5.28</td>
    </tr>
    <tr>
      <th>24</th>
      <td>khatanga</td>
      <td>ru</td>
      <td>71.9801</td>
      <td>102.4743</td>
      <td>49.95</td>
      <td>85.0</td>
      <td>0.0</td>
      <td>11.65</td>
    </tr>
    <tr>
      <th>25</th>
      <td>vaini</td>
      <td>to</td>
      <td>-21.2001</td>
      <td>-175.2000</td>
      <td>76.32</td>
      <td>100.0</td>
      <td>88.0</td>
      <td>15.35</td>
    </tr>
    <tr>
      <th>26</th>
      <td>suileng</td>
      <td>cn</td>
      <td>47.2500</td>
      <td>127.0833</td>
      <td>84.15</td>
      <td>41.0</td>
      <td>20.0</td>
      <td>22.50</td>
    </tr>
    <tr>
      <th>27</th>
      <td>thinadhoo</td>
      <td>mv</td>
      <td>0.5333</td>
      <td>72.9333</td>
      <td>83.43</td>
      <td>100.0</td>
      <td>48.0</td>
      <td>14.67</td>
    </tr>
    <tr>
      <th>28</th>
      <td>hobart</td>
      <td>au</td>
      <td>-42.8826</td>
      <td>147.3281</td>
      <td>55.22</td>
      <td>94.0</td>
      <td>36.0</td>
      <td>2.37</td>
    </tr>
    <tr>
      <th>29</th>
      <td>rikitea</td>
      <td>pf</td>
      <td>-23.1203</td>
      <td>-134.9692</td>
      <td>71.82</td>
      <td>100.0</td>
      <td>100.0</td>
      <td>17.25</td>
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
    </tr>
    <tr>
      <th>770</th>
      <td>mataura</td>
      <td>pf</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>771</th>
      <td>tiksi</td>
      <td>ru</td>
      <td>71.6366</td>
      <td>128.8685</td>
      <td>37.35</td>
      <td>85.0</td>
      <td>12.0</td>
      <td>8.52</td>
    </tr>
    <tr>
      <th>772</th>
      <td>kununurra</td>
      <td>au</td>
      <td>-15.7731</td>
      <td>128.7388</td>
      <td>86.47</td>
      <td>10.0</td>
      <td>0.0</td>
      <td>15.01</td>
    </tr>
    <tr>
      <th>773</th>
      <td>hobyo</td>
      <td>so</td>
      <td>5.3505</td>
      <td>48.5268</td>
      <td>82.08</td>
      <td>69.0</td>
      <td>12.0</td>
      <td>19.37</td>
    </tr>
    <tr>
      <th>774</th>
      <td>iqaluit</td>
      <td>ca</td>
      <td>63.7494</td>
      <td>-68.5219</td>
      <td>26.83</td>
      <td>93.0</td>
      <td>76.0</td>
      <td>10.09</td>
    </tr>
    <tr>
      <th>775</th>
      <td>saryshagan</td>
      <td>kz</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>776</th>
      <td>taolanaro</td>
      <td>mg</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>777</th>
      <td>honavar</td>
      <td>in</td>
      <td>14.2833</td>
      <td>74.4500</td>
      <td>80.73</td>
      <td>100.0</td>
      <td>92.0</td>
      <td>2.93</td>
    </tr>
    <tr>
      <th>778</th>
      <td>kodiak</td>
      <td>us</td>
      <td>39.9519</td>
      <td>-94.7572</td>
      <td>59.18</td>
      <td>88.0</td>
      <td>0.0</td>
      <td>8.97</td>
    </tr>
    <tr>
      <th>779</th>
      <td>albany</td>
      <td>au</td>
      <td>-35.0248</td>
      <td>117.8836</td>
      <td>66.06</td>
      <td>46.0</td>
      <td>48.0</td>
      <td>6.17</td>
    </tr>
    <tr>
      <th>780</th>
      <td>mujiayingzi</td>
      <td>cn</td>
      <td>42.1167</td>
      <td>118.7833</td>
      <td>65.25</td>
      <td>36.0</td>
      <td>92.0</td>
      <td>9.86</td>
    </tr>
    <tr>
      <th>781</th>
      <td>hualmay</td>
      <td>pe</td>
      <td>-11.0968</td>
      <td>-77.6130</td>
      <td>58.86</td>
      <td>88.0</td>
      <td>80.0</td>
      <td>4.83</td>
    </tr>
    <tr>
      <th>782</th>
      <td>virginia beach</td>
      <td>us</td>
      <td>36.8530</td>
      <td>-75.9775</td>
      <td>71.89</td>
      <td>95.0</td>
      <td>56.0</td>
      <td>3.38</td>
    </tr>
    <tr>
      <th>783</th>
      <td>nikolskoye</td>
      <td>ru</td>
      <td>59.7035</td>
      <td>30.7861</td>
      <td>70.21</td>
      <td>56.0</td>
      <td>44.0</td>
      <td>4.61</td>
    </tr>
    <tr>
      <th>784</th>
      <td>jamestown</td>
      <td>sh</td>
      <td>-15.9388</td>
      <td>-5.7168</td>
      <td>70.65</td>
      <td>100.0</td>
      <td>76.0</td>
      <td>19.48</td>
    </tr>
    <tr>
      <th>785</th>
      <td>mataura</td>
      <td>pf</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>786</th>
      <td>albany</td>
      <td>au</td>
      <td>-35.0248</td>
      <td>117.8836</td>
      <td>66.06</td>
      <td>46.0</td>
      <td>48.0</td>
      <td>6.17</td>
    </tr>
    <tr>
      <th>787</th>
      <td>kenai</td>
      <td>us</td>
      <td>60.5544</td>
      <td>-151.2584</td>
      <td>54.09</td>
      <td>47.0</td>
      <td>44.0</td>
      <td>14.56</td>
    </tr>
    <tr>
      <th>788</th>
      <td>puerto ayora</td>
      <td>ec</td>
      <td>-0.7394</td>
      <td>-90.3518</td>
      <td>72.09</td>
      <td>100.0</td>
      <td>24.0</td>
      <td>8.19</td>
    </tr>
    <tr>
      <th>789</th>
      <td>bredasdorp</td>
      <td>za</td>
      <td>-34.5331</td>
      <td>20.0428</td>
      <td>56.52</td>
      <td>100.0</td>
      <td>100.0</td>
      <td>8.52</td>
    </tr>
    <tr>
      <th>790</th>
      <td>barentsburg</td>
      <td>sj</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>791</th>
      <td>rikitea</td>
      <td>pf</td>
      <td>-23.1203</td>
      <td>-134.9692</td>
      <td>71.82</td>
      <td>100.0</td>
      <td>100.0</td>
      <td>17.25</td>
    </tr>
    <tr>
      <th>792</th>
      <td>tuktoyaktuk</td>
      <td>ca</td>
      <td>69.4440</td>
      <td>-133.0320</td>
      <td>39.45</td>
      <td>91.0</td>
      <td>0.0</td>
      <td>8.97</td>
    </tr>
    <tr>
      <th>793</th>
      <td>constantine</td>
      <td>dz</td>
      <td>36.3645</td>
      <td>6.6083</td>
      <td>64.83</td>
      <td>94.0</td>
      <td>48.0</td>
      <td>3.27</td>
    </tr>
    <tr>
      <th>794</th>
      <td>kodiak</td>
      <td>us</td>
      <td>39.9519</td>
      <td>-94.7572</td>
      <td>59.18</td>
      <td>88.0</td>
      <td>0.0</td>
      <td>8.97</td>
    </tr>
    <tr>
      <th>795</th>
      <td>mbekenyera</td>
      <td>tz</td>
      <td>-10.0000</td>
      <td>38.9833</td>
      <td>71.10</td>
      <td>67.0</td>
      <td>0.0</td>
      <td>3.38</td>
    </tr>
    <tr>
      <th>796</th>
      <td>taolanaro</td>
      <td>mg</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>797</th>
      <td>biltine</td>
      <td>td</td>
      <td>14.5252</td>
      <td>20.9287</td>
      <td>80.73</td>
      <td>31.0</td>
      <td>0.0</td>
      <td>5.50</td>
    </tr>
    <tr>
      <th>798</th>
      <td>chuy</td>
      <td>uy</td>
      <td>-33.6938</td>
      <td>-53.4562</td>
      <td>53.64</td>
      <td>92.0</td>
      <td>24.0</td>
      <td>14.79</td>
    </tr>
    <tr>
      <th>799</th>
      <td>ossora</td>
      <td>ru</td>
      <td>59.2353</td>
      <td>163.0719</td>
      <td>38.61</td>
      <td>91.0</td>
      <td>48.0</td>
      <td>11.99</td>
    </tr>
  </tbody>
</table>
<p>800 rows × 8 columns</p>
</div>




```python
len_orig = len(df_countries)
len_dropna = len(df_countries.dropna())
missing_weather = len_orig - len_dropna
```


```python
print(missing_weather)
print('\n')
print(len(df_countries.dropna()))
```

    77
    
    
    723
    


```python
#save citipy data into CSV file and read
df_countries = df_countries.dropna()
df_countries.to_csv(path_or_buf='df_countries.csv', encoding="utf-8", index=False)
df_countries = pd.read_csv("df_countries.csv")
df_countries
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
      <th>Cities</th>
      <th>Country</th>
      <th>Latitude</th>
      <th>Longitude</th>
      <th>Temperature (F)</th>
      <th>Humidity (%)</th>
      <th>Cloudiness (%)</th>
      <th>Wind Speed (mph)</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>mar del plata</td>
      <td>ar</td>
      <td>-46.4276</td>
      <td>-67.5191</td>
      <td>17.82</td>
      <td>100.0</td>
      <td>0.0</td>
      <td>3.71</td>
    </tr>
    <tr>
      <th>1</th>
      <td>bluff</td>
      <td>nz</td>
      <td>-46.6000</td>
      <td>168.3333</td>
      <td>45.36</td>
      <td>100.0</td>
      <td>8.0</td>
      <td>5.95</td>
    </tr>
    <tr>
      <th>2</th>
      <td>biak</td>
      <td>id</td>
      <td>-0.9132</td>
      <td>122.8766</td>
      <td>76.59</td>
      <td>100.0</td>
      <td>92.0</td>
      <td>4.16</td>
    </tr>
    <tr>
      <th>3</th>
      <td>myanaung</td>
      <td>mm</td>
      <td>18.2833</td>
      <td>95.3167</td>
      <td>80.19</td>
      <td>100.0</td>
      <td>92.0</td>
      <td>8.52</td>
    </tr>
    <tr>
      <th>4</th>
      <td>pangnirtung</td>
      <td>ca</td>
      <td>66.1490</td>
      <td>-65.7179</td>
      <td>24.03</td>
      <td>100.0</td>
      <td>68.0</td>
      <td>4.05</td>
    </tr>
    <tr>
      <th>5</th>
      <td>vaini</td>
      <td>to</td>
      <td>-21.2001</td>
      <td>-175.2000</td>
      <td>76.32</td>
      <td>100.0</td>
      <td>88.0</td>
      <td>15.35</td>
    </tr>
    <tr>
      <th>6</th>
      <td>bethel</td>
      <td>us</td>
      <td>60.7922</td>
      <td>-161.7559</td>
      <td>56.57</td>
      <td>47.0</td>
      <td>0.0</td>
      <td>14.67</td>
    </tr>
    <tr>
      <th>7</th>
      <td>yumen</td>
      <td>cn</td>
      <td>40.2917</td>
      <td>97.0449</td>
      <td>76.68</td>
      <td>25.0</td>
      <td>64.0</td>
      <td>2.48</td>
    </tr>
    <tr>
      <th>8</th>
      <td>upernavik</td>
      <td>gl</td>
      <td>72.7868</td>
      <td>-56.1549</td>
      <td>25.56</td>
      <td>95.0</td>
      <td>20.0</td>
      <td>4.16</td>
    </tr>
    <tr>
      <th>9</th>
      <td>komsomolskiy</td>
      <td>ru</td>
      <td>67.5537</td>
      <td>63.7760</td>
      <td>32.04</td>
      <td>100.0</td>
      <td>68.0</td>
      <td>19.26</td>
    </tr>
    <tr>
      <th>10</th>
      <td>new norfolk</td>
      <td>au</td>
      <td>-42.7802</td>
      <td>147.0615</td>
      <td>53.78</td>
      <td>100.0</td>
      <td>48.0</td>
      <td>2.48</td>
    </tr>
    <tr>
      <th>11</th>
      <td>barrow</td>
      <td>us</td>
      <td>39.5062</td>
      <td>-90.4016</td>
      <td>69.87</td>
      <td>91.0</td>
      <td>0.0</td>
      <td>15.57</td>
    </tr>
    <tr>
      <th>12</th>
      <td>ushuaia</td>
      <td>ar</td>
      <td>-54.8070</td>
      <td>-68.3074</td>
      <td>27.09</td>
      <td>100.0</td>
      <td>44.0</td>
      <td>5.28</td>
    </tr>
    <tr>
      <th>13</th>
      <td>ushuaia</td>
      <td>ar</td>
      <td>-54.8070</td>
      <td>-68.3074</td>
      <td>27.09</td>
      <td>100.0</td>
      <td>44.0</td>
      <td>5.28</td>
    </tr>
    <tr>
      <th>14</th>
      <td>kruisfontein</td>
      <td>za</td>
      <td>-34.0034</td>
      <td>24.7314</td>
      <td>57.06</td>
      <td>99.0</td>
      <td>48.0</td>
      <td>19.15</td>
    </tr>
    <tr>
      <th>15</th>
      <td>sao filipe</td>
      <td>cv</td>
      <td>14.8961</td>
      <td>-24.4956</td>
      <td>71.37</td>
      <td>100.0</td>
      <td>0.0</td>
      <td>13.78</td>
    </tr>
    <tr>
      <th>16</th>
      <td>poum</td>
      <td>nc</td>
      <td>-20.2334</td>
      <td>164.0167</td>
      <td>72.81</td>
      <td>100.0</td>
      <td>0.0</td>
      <td>10.98</td>
    </tr>
    <tr>
      <th>17</th>
      <td>havelock</td>
      <td>us</td>
      <td>34.8785</td>
      <td>-76.9015</td>
      <td>71.69</td>
      <td>91.0</td>
      <td>24.0</td>
      <td>8.75</td>
    </tr>
    <tr>
      <th>18</th>
      <td>new norfolk</td>
      <td>au</td>
      <td>-42.7802</td>
      <td>147.0615</td>
      <td>53.78</td>
      <td>100.0</td>
      <td>48.0</td>
      <td>2.48</td>
    </tr>
    <tr>
      <th>19</th>
      <td>cabo san lucas</td>
      <td>mx</td>
      <td>22.8938</td>
      <td>-109.9112</td>
      <td>79.45</td>
      <td>99.0</td>
      <td>8.0</td>
      <td>7.40</td>
    </tr>
    <tr>
      <th>20</th>
      <td>clyde river</td>
      <td>ca</td>
      <td>70.4692</td>
      <td>-68.5915</td>
      <td>25.59</td>
      <td>90.0</td>
      <td>56.0</td>
      <td>8.52</td>
    </tr>
    <tr>
      <th>21</th>
      <td>punta arenas</td>
      <td>cl</td>
      <td>-53.1627</td>
      <td>-70.9081</td>
      <td>34.25</td>
      <td>100.0</td>
      <td>0.0</td>
      <td>1.92</td>
    </tr>
    <tr>
      <th>22</th>
      <td>ushuaia</td>
      <td>ar</td>
      <td>-54.8070</td>
      <td>-68.3074</td>
      <td>27.09</td>
      <td>100.0</td>
      <td>44.0</td>
      <td>5.28</td>
    </tr>
    <tr>
      <th>23</th>
      <td>khatanga</td>
      <td>ru</td>
      <td>71.9801</td>
      <td>102.4743</td>
      <td>49.95</td>
      <td>85.0</td>
      <td>0.0</td>
      <td>11.65</td>
    </tr>
    <tr>
      <th>24</th>
      <td>vaini</td>
      <td>to</td>
      <td>-21.2001</td>
      <td>-175.2000</td>
      <td>76.32</td>
      <td>100.0</td>
      <td>88.0</td>
      <td>15.35</td>
    </tr>
    <tr>
      <th>25</th>
      <td>suileng</td>
      <td>cn</td>
      <td>47.2500</td>
      <td>127.0833</td>
      <td>84.15</td>
      <td>41.0</td>
      <td>20.0</td>
      <td>22.50</td>
    </tr>
    <tr>
      <th>26</th>
      <td>thinadhoo</td>
      <td>mv</td>
      <td>0.5333</td>
      <td>72.9333</td>
      <td>83.43</td>
      <td>100.0</td>
      <td>48.0</td>
      <td>14.67</td>
    </tr>
    <tr>
      <th>27</th>
      <td>hobart</td>
      <td>au</td>
      <td>-42.8826</td>
      <td>147.3281</td>
      <td>55.22</td>
      <td>94.0</td>
      <td>36.0</td>
      <td>2.37</td>
    </tr>
    <tr>
      <th>28</th>
      <td>rikitea</td>
      <td>pf</td>
      <td>-23.1203</td>
      <td>-134.9692</td>
      <td>71.82</td>
      <td>100.0</td>
      <td>100.0</td>
      <td>17.25</td>
    </tr>
    <tr>
      <th>29</th>
      <td>chara</td>
      <td>ru</td>
      <td>56.9029</td>
      <td>118.2610</td>
      <td>39.69</td>
      <td>94.0</td>
      <td>92.0</td>
      <td>3.38</td>
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
    </tr>
    <tr>
      <th>693</th>
      <td>kodiak</td>
      <td>us</td>
      <td>39.9519</td>
      <td>-94.7572</td>
      <td>59.18</td>
      <td>88.0</td>
      <td>0.0</td>
      <td>8.97</td>
    </tr>
    <tr>
      <th>694</th>
      <td>bilma</td>
      <td>ne</td>
      <td>18.6929</td>
      <td>12.9187</td>
      <td>81.00</td>
      <td>21.0</td>
      <td>0.0</td>
      <td>4.27</td>
    </tr>
    <tr>
      <th>695</th>
      <td>provideniya</td>
      <td>ru</td>
      <td>64.4238</td>
      <td>-173.2276</td>
      <td>35.82</td>
      <td>86.0</td>
      <td>24.0</td>
      <td>24.63</td>
    </tr>
    <tr>
      <th>696</th>
      <td>vaini</td>
      <td>to</td>
      <td>-21.2001</td>
      <td>-175.2000</td>
      <td>76.32</td>
      <td>100.0</td>
      <td>88.0</td>
      <td>15.35</td>
    </tr>
    <tr>
      <th>697</th>
      <td>lagoa</td>
      <td>pt</td>
      <td>37.1363</td>
      <td>-8.4541</td>
      <td>56.26</td>
      <td>91.0</td>
      <td>0.0</td>
      <td>4.16</td>
    </tr>
    <tr>
      <th>698</th>
      <td>hermanus</td>
      <td>za</td>
      <td>-34.4176</td>
      <td>19.2361</td>
      <td>46.71</td>
      <td>100.0</td>
      <td>48.0</td>
      <td>1.36</td>
    </tr>
    <tr>
      <th>699</th>
      <td>tiksi</td>
      <td>ru</td>
      <td>71.6366</td>
      <td>128.8685</td>
      <td>37.35</td>
      <td>85.0</td>
      <td>12.0</td>
      <td>8.52</td>
    </tr>
    <tr>
      <th>700</th>
      <td>kununurra</td>
      <td>au</td>
      <td>-15.7731</td>
      <td>128.7388</td>
      <td>86.47</td>
      <td>10.0</td>
      <td>0.0</td>
      <td>15.01</td>
    </tr>
    <tr>
      <th>701</th>
      <td>hobyo</td>
      <td>so</td>
      <td>5.3505</td>
      <td>48.5268</td>
      <td>82.08</td>
      <td>69.0</td>
      <td>12.0</td>
      <td>19.37</td>
    </tr>
    <tr>
      <th>702</th>
      <td>iqaluit</td>
      <td>ca</td>
      <td>63.7494</td>
      <td>-68.5219</td>
      <td>26.83</td>
      <td>93.0</td>
      <td>76.0</td>
      <td>10.09</td>
    </tr>
    <tr>
      <th>703</th>
      <td>honavar</td>
      <td>in</td>
      <td>14.2833</td>
      <td>74.4500</td>
      <td>80.73</td>
      <td>100.0</td>
      <td>92.0</td>
      <td>2.93</td>
    </tr>
    <tr>
      <th>704</th>
      <td>kodiak</td>
      <td>us</td>
      <td>39.9519</td>
      <td>-94.7572</td>
      <td>59.18</td>
      <td>88.0</td>
      <td>0.0</td>
      <td>8.97</td>
    </tr>
    <tr>
      <th>705</th>
      <td>albany</td>
      <td>au</td>
      <td>-35.0248</td>
      <td>117.8836</td>
      <td>66.06</td>
      <td>46.0</td>
      <td>48.0</td>
      <td>6.17</td>
    </tr>
    <tr>
      <th>706</th>
      <td>mujiayingzi</td>
      <td>cn</td>
      <td>42.1167</td>
      <td>118.7833</td>
      <td>65.25</td>
      <td>36.0</td>
      <td>92.0</td>
      <td>9.86</td>
    </tr>
    <tr>
      <th>707</th>
      <td>hualmay</td>
      <td>pe</td>
      <td>-11.0968</td>
      <td>-77.6130</td>
      <td>58.86</td>
      <td>88.0</td>
      <td>80.0</td>
      <td>4.83</td>
    </tr>
    <tr>
      <th>708</th>
      <td>virginia beach</td>
      <td>us</td>
      <td>36.8530</td>
      <td>-75.9775</td>
      <td>71.89</td>
      <td>95.0</td>
      <td>56.0</td>
      <td>3.38</td>
    </tr>
    <tr>
      <th>709</th>
      <td>nikolskoye</td>
      <td>ru</td>
      <td>59.7035</td>
      <td>30.7861</td>
      <td>70.21</td>
      <td>56.0</td>
      <td>44.0</td>
      <td>4.61</td>
    </tr>
    <tr>
      <th>710</th>
      <td>jamestown</td>
      <td>sh</td>
      <td>-15.9388</td>
      <td>-5.7168</td>
      <td>70.65</td>
      <td>100.0</td>
      <td>76.0</td>
      <td>19.48</td>
    </tr>
    <tr>
      <th>711</th>
      <td>albany</td>
      <td>au</td>
      <td>-35.0248</td>
      <td>117.8836</td>
      <td>66.06</td>
      <td>46.0</td>
      <td>48.0</td>
      <td>6.17</td>
    </tr>
    <tr>
      <th>712</th>
      <td>kenai</td>
      <td>us</td>
      <td>60.5544</td>
      <td>-151.2584</td>
      <td>54.09</td>
      <td>47.0</td>
      <td>44.0</td>
      <td>14.56</td>
    </tr>
    <tr>
      <th>713</th>
      <td>puerto ayora</td>
      <td>ec</td>
      <td>-0.7394</td>
      <td>-90.3518</td>
      <td>72.09</td>
      <td>100.0</td>
      <td>24.0</td>
      <td>8.19</td>
    </tr>
    <tr>
      <th>714</th>
      <td>bredasdorp</td>
      <td>za</td>
      <td>-34.5331</td>
      <td>20.0428</td>
      <td>56.52</td>
      <td>100.0</td>
      <td>100.0</td>
      <td>8.52</td>
    </tr>
    <tr>
      <th>715</th>
      <td>rikitea</td>
      <td>pf</td>
      <td>-23.1203</td>
      <td>-134.9692</td>
      <td>71.82</td>
      <td>100.0</td>
      <td>100.0</td>
      <td>17.25</td>
    </tr>
    <tr>
      <th>716</th>
      <td>tuktoyaktuk</td>
      <td>ca</td>
      <td>69.4440</td>
      <td>-133.0320</td>
      <td>39.45</td>
      <td>91.0</td>
      <td>0.0</td>
      <td>8.97</td>
    </tr>
    <tr>
      <th>717</th>
      <td>constantine</td>
      <td>dz</td>
      <td>36.3645</td>
      <td>6.6083</td>
      <td>64.83</td>
      <td>94.0</td>
      <td>48.0</td>
      <td>3.27</td>
    </tr>
    <tr>
      <th>718</th>
      <td>kodiak</td>
      <td>us</td>
      <td>39.9519</td>
      <td>-94.7572</td>
      <td>59.18</td>
      <td>88.0</td>
      <td>0.0</td>
      <td>8.97</td>
    </tr>
    <tr>
      <th>719</th>
      <td>mbekenyera</td>
      <td>tz</td>
      <td>-10.0000</td>
      <td>38.9833</td>
      <td>71.10</td>
      <td>67.0</td>
      <td>0.0</td>
      <td>3.38</td>
    </tr>
    <tr>
      <th>720</th>
      <td>biltine</td>
      <td>td</td>
      <td>14.5252</td>
      <td>20.9287</td>
      <td>80.73</td>
      <td>31.0</td>
      <td>0.0</td>
      <td>5.50</td>
    </tr>
    <tr>
      <th>721</th>
      <td>chuy</td>
      <td>uy</td>
      <td>-33.6938</td>
      <td>-53.4562</td>
      <td>53.64</td>
      <td>92.0</td>
      <td>24.0</td>
      <td>14.79</td>
    </tr>
    <tr>
      <th>722</th>
      <td>ossora</td>
      <td>ru</td>
      <td>59.2353</td>
      <td>163.0719</td>
      <td>38.61</td>
      <td>91.0</td>
      <td>48.0</td>
      <td>11.99</td>
    </tr>
  </tbody>
</table>
<p>723 rows × 8 columns</p>
</div>



TEMPERATURE(F) Vs. LATITUDE


```python
# Scatter Plot for Temperature (F) vs. Latitude

date = time.strftime("%m/%d/%Y")
# print(date)
plt.scatter(df_countries['Latitude'],df_countries['Temperature (F)'])
plt.title(f"Temperature (F) df_countries vs. Latitude {date}")
plt.xlabel("Latitude")
plt.ylabel("Temperature (F)")
plt.style.use('ggplot')
plt.savefig("Temperature (F).png")
plt.show()
```


![png](output_13_0.png)


HUMIDITY(%) Vs. LATITUDE


```python
# Scatter Plot for Humidity (%) vs. Latitude

plt.scatter(df_countries['Latitude'], df_countries['Humidity (%)'])
plt.title(f"Humidity (%) vs. Latitude {date}")
plt.xlabel("Latitude")
plt.ylabel("Humidity (%)")
plt.style.use('ggplot')
plt.savefig("Humidity (%).png")
plt.show()
```


![png](output_15_0.png)


CLOUDINESS(%) Vs. LATITUDE


```python
#Scatter Plot for Cloudiness (%) vs. Latitude

plt.scatter(df_countries['Latitude'], df_countries['Cloudiness (%)'])
plt.title(f"Cloudiness (%) vs. Latitude {date}")
plt.xlabel("Latitude")
plt.ylabel("Cloudiness (%)")
plt.style.use('ggplot')
plt.savefig("Cloudiness (%).png")
plt.show()
```


![png](output_17_0.png)


WIND SPEED(mph) vs. LATITUDE


```python
# Scatter Plot for Wind Speed (mph) vs. Latitude

plt.scatter(df_countries['Latitude'], df_countries['Wind Speed (mph)'])
plt.title(f"'Wind Speed (mph)' vs. Latitude {date}")
plt.xlabel("Latitude")
plt.ylabel('Wind Speed (mph)')
plt.style.use('ggplot')
plt.savefig('Wind Speed (mph).png')
plt.show()
```


![png](output_19_0.png)



```python
#Average wind speed
print(df_countries['Wind Speed (mph)'].mean())
```

    9.224910096818812
    
