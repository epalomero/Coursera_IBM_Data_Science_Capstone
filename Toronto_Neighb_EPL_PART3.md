# Toronto Neighborhood Analysis
## Clustering Coursera Capstone Project - Week 3 

### Eduardo Palomero-López

### PART 1: Converting Wikipedia Table Data into DataFrame
### PART 2: Add Latitude and Longitude Data
### PART 3: Explore and cluster the Neighborhoods in Toronto


```python
pip install beautifulsoup4
```

    Collecting beautifulsoup4
    [?25l  Downloading https://files.pythonhosted.org/packages/66/25/ff030e2437265616a1e9b25ccc864e0371a0bc3adb7c5a404fd661c6f4f6/beautifulsoup4-4.9.1-py3-none-any.whl (115kB)
    [K     |████████████████████████████████| 122kB 5.3MB/s eta 0:00:01
    [?25hCollecting soupsieve>1.2 (from beautifulsoup4)
      Downloading https://files.pythonhosted.org/packages/6f/8f/457f4a5390eeae1cc3aeab89deb7724c965be841ffca6cfca9197482e470/soupsieve-2.0.1-py3-none-any.whl
    Installing collected packages: soupsieve, beautifulsoup4
    Successfully installed beautifulsoup4-4.9.1 soupsieve-2.0.1
    Note: you may need to restart the kernel to use updated packages.



```python
pip install lxml
```

    Collecting lxml
    [?25l  Downloading https://files.pythonhosted.org/packages/55/6f/c87dffdd88a54dd26a3a9fef1d14b6384a9933c455c54ce3ca7d64a84c88/lxml-4.5.1-cp36-cp36m-manylinux1_x86_64.whl (5.5MB)
    [K     |████████████████████████████████| 5.5MB 24.0MB/s eta 0:00:01     |██████████████████████████████▌ | 5.3MB 24.0MB/s eta 0:00:01
    [?25hInstalling collected packages: lxml
    Successfully installed lxml-4.5.1
    Note: you may need to restart the kernel to use updated packages.



```python
from bs4 import BeautifulSoup as bsoup
from urllib.request import urlopen as uReq
import requests
import lxml
import pandas as pd
from pandas import DataFrame
import numpy as np
```


```python
URL='https://en.wikipedia.org/wiki/List_of_postal_codes_of_Canada:_M' 
```


```python
r=requests.get(URL)
```

### Use BeautifulSoup to parse the data in Wikipedia


```python
parsed_web=bsoup(r.text,"html.parser")
# uncomment next line to check parsing results
#parsed_web
```


```python
# get table from parsed data
Table=parsed_web.table
# uncomment next line to check results
#Table
```


```python
results=Table.find_all('tr')
number_rows=len(results)
print('Total rows=',number_rows,'; so total number of data rows (removing header row)=', number_rows-1)
```

    Total rows= 181 ; so total number of data rows (removing header row)= 180



```python
header=results[0].text.split()
header
```




    ['Postal', 'Code', 'Borough', 'Neighborhood']



__Check that 'Postal' and 'Code' have been split and need to merge__


```python
header=[header[0]+header[1],header[2],header[3]]
header
```




    ['PostalCode', 'Borough', 'Neighborhood']



__Check data__


```python
results[7].text
```




    "\nM7A\n\nDowntown Toronto\n\nQueen's Park, Ontario Provincial Government\n"




```python
results[7].text.split('\n')
```




    ['',
     'M7A',
     '',
     'Downtown Toronto',
     '',
     "Queen's Park, Ontario Provincial Government",
     '']




```python
PostalCode=results[7].text.split('\n')[1]
PostalCode
```




    'M7A'




```python
Borough=results[7].text.split('\n')[3]
Borough
```




    'Downtown Toronto'




```python
Neighborhood=results[7].text.split('\n')[5]
Neighborhood
```




    "Queen's Park, Ontario Provincial Government"




```python
# Loop to extract data

Data =[]
n=1
while n < number_rows :
    Postcode=results[n].text.split('\n')[1]
    Borough=results[n].text.split('\n')[3]
    Neighborhood=results[n].text.split('\n')[5]
    Data.append((Postcode, Borough,Neighborhood))
    n=n+1

df=pd.DataFrame(Data, columns=['PostalCode', 'Borough', 'Neighbourhood'])
df.head(5)


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
      <th>PostalCode</th>
      <th>Borough</th>
      <th>Neighbourhood</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>M1A</td>
      <td>Not assigned</td>
      <td></td>
    </tr>
    <tr>
      <th>1</th>
      <td>M2A</td>
      <td>Not assigned</td>
      <td></td>
    </tr>
    <tr>
      <th>2</th>
      <td>M3A</td>
      <td>North York</td>
      <td>Parkwoods</td>
    </tr>
    <tr>
      <th>3</th>
      <td>M4A</td>
      <td>North York</td>
      <td>Victoria Village</td>
    </tr>
    <tr>
      <th>4</th>
      <td>M5A</td>
      <td>Downtown Toronto</td>
      <td>Regent Park, Harbourfront</td>
    </tr>
  </tbody>
</table>
</div>




```python
df.shape
```




    (180, 3)




```python
# Remove rows Borough='Not assigned'
df1=df[~df.Borough.str.contains("Not assigned")]
df1=df1.reset_index(drop=True)
print(df1.shape)
print(df1.head())
```

    (103, 3)
      PostalCode           Borough                                Neighbourhood
    0        M3A        North York                                    Parkwoods
    1        M4A        North York                             Victoria Village
    2        M5A  Downtown Toronto                    Regent Park, Harbourfront
    3        M6A        North York             Lawrence Manor, Lawrence Heights
    4        M7A  Downtown Toronto  Queen's Park, Ontario Provincial Government



```python
distinct_PostalCode = df1['PostalCode'].nunique()
distinct_borough = df1['Borough'].nunique()
distinct_neighbourhood= df1['Neighbourhood'].nunique()
print('Different Postal Codes : ' + str(distinct_PostalCode))
print('Different Boroughs  : '+ str(distinct_borough))
print('Different Neighbourhoods  :' + str(distinct_neighbourhood))
```

    Different Postal Codes : 103
    Different Boroughs  : 10
    Different Neighbourhoods  :99



```python
df1.head(10)
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
      <th>PostalCode</th>
      <th>Borough</th>
      <th>Neighbourhood</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>M3A</td>
      <td>North York</td>
      <td>Parkwoods</td>
    </tr>
    <tr>
      <th>1</th>
      <td>M4A</td>
      <td>North York</td>
      <td>Victoria Village</td>
    </tr>
    <tr>
      <th>2</th>
      <td>M5A</td>
      <td>Downtown Toronto</td>
      <td>Regent Park, Harbourfront</td>
    </tr>
    <tr>
      <th>3</th>
      <td>M6A</td>
      <td>North York</td>
      <td>Lawrence Manor, Lawrence Heights</td>
    </tr>
    <tr>
      <th>4</th>
      <td>M7A</td>
      <td>Downtown Toronto</td>
      <td>Queen's Park, Ontario Provincial Government</td>
    </tr>
    <tr>
      <th>5</th>
      <td>M9A</td>
      <td>Etobicoke</td>
      <td>Islington Avenue, Humber Valley Village</td>
    </tr>
    <tr>
      <th>6</th>
      <td>M1B</td>
      <td>Scarborough</td>
      <td>Malvern, Rouge</td>
    </tr>
    <tr>
      <th>7</th>
      <td>M3B</td>
      <td>North York</td>
      <td>Don Mills</td>
    </tr>
    <tr>
      <th>8</th>
      <td>M4B</td>
      <td>East York</td>
      <td>Parkview Hill, Woodbine Gardens</td>
    </tr>
    <tr>
      <th>9</th>
      <td>M5B</td>
      <td>Downtown Toronto</td>
      <td>Garden District, Ryerson</td>
    </tr>
  </tbody>
</table>
</div>



__Check if any Neighborhood='Not assigned'__


```python
((df1['Neighbourhood'] == 'Not assigned').groupby)
```




    <bound method Series.groupby of 0      False
    1      False
    2      False
    3      False
    4      False
           ...  
    98     False
    99     False
    100    False
    101    False
    102    False
    Name: Neighbourhood, Length: 103, dtype: bool>



__There is no data with Neighborhood='Not assigned'__


```python
df1.shape
```




    (103, 3)



## --------------------END OF PART 1----------------------------------

### PART 2: Add Latitude and Longitude Data


```python
# Get coordinates from csv file provided
df_codes=pd.read_csv('http://cocl.us/Geospatial_data')
df_codes.head()
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
      <th>Postal Code</th>
      <th>Latitude</th>
      <th>Longitude</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>M1B</td>
      <td>43.806686</td>
      <td>-79.194353</td>
    </tr>
    <tr>
      <th>1</th>
      <td>M1C</td>
      <td>43.784535</td>
      <td>-79.160497</td>
    </tr>
    <tr>
      <th>2</th>
      <td>M1E</td>
      <td>43.763573</td>
      <td>-79.188711</td>
    </tr>
    <tr>
      <th>3</th>
      <td>M1G</td>
      <td>43.770992</td>
      <td>-79.216917</td>
    </tr>
    <tr>
      <th>4</th>
      <td>M1H</td>
      <td>43.773136</td>
      <td>-79.239476</td>
    </tr>
  </tbody>
</table>
</div>




```python
df_codes.shape
```




    (103, 3)




```python
df_codes.columns = ['PostalCode', 'Latitude', 'Longitude']
df_codes.head()
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
      <th>PostalCode</th>
      <th>Latitude</th>
      <th>Longitude</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>M1B</td>
      <td>43.806686</td>
      <td>-79.194353</td>
    </tr>
    <tr>
      <th>1</th>
      <td>M1C</td>
      <td>43.784535</td>
      <td>-79.160497</td>
    </tr>
    <tr>
      <th>2</th>
      <td>M1E</td>
      <td>43.763573</td>
      <td>-79.188711</td>
    </tr>
    <tr>
      <th>3</th>
      <td>M1G</td>
      <td>43.770992</td>
      <td>-79.216917</td>
    </tr>
    <tr>
      <th>4</th>
      <td>M1H</td>
      <td>43.773136</td>
      <td>-79.239476</td>
    </tr>
  </tbody>
</table>
</div>




```python
df1sorted=df1.sort_values('PostalCode')
df1sorted.head()
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
      <th>PostalCode</th>
      <th>Borough</th>
      <th>Neighbourhood</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>6</th>
      <td>M1B</td>
      <td>Scarborough</td>
      <td>Malvern, Rouge</td>
    </tr>
    <tr>
      <th>12</th>
      <td>M1C</td>
      <td>Scarborough</td>
      <td>Rouge Hill, Port Union, Highland Creek</td>
    </tr>
    <tr>
      <th>18</th>
      <td>M1E</td>
      <td>Scarborough</td>
      <td>Guildwood, Morningside, West Hill</td>
    </tr>
    <tr>
      <th>22</th>
      <td>M1G</td>
      <td>Scarborough</td>
      <td>Woburn</td>
    </tr>
    <tr>
      <th>26</th>
      <td>M1H</td>
      <td>Scarborough</td>
      <td>Cedarbrae</td>
    </tr>
  </tbody>
</table>
</div>




```python
NBRHs=pd.merge(df1sorted,df_codes, how='right', on = 'PostalCode')
NBRHs.head(12)
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
      <th>PostalCode</th>
      <th>Borough</th>
      <th>Neighbourhood</th>
      <th>Latitude</th>
      <th>Longitude</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>M1B</td>
      <td>Scarborough</td>
      <td>Malvern, Rouge</td>
      <td>43.806686</td>
      <td>-79.194353</td>
    </tr>
    <tr>
      <th>1</th>
      <td>M1C</td>
      <td>Scarborough</td>
      <td>Rouge Hill, Port Union, Highland Creek</td>
      <td>43.784535</td>
      <td>-79.160497</td>
    </tr>
    <tr>
      <th>2</th>
      <td>M1E</td>
      <td>Scarborough</td>
      <td>Guildwood, Morningside, West Hill</td>
      <td>43.763573</td>
      <td>-79.188711</td>
    </tr>
    <tr>
      <th>3</th>
      <td>M1G</td>
      <td>Scarborough</td>
      <td>Woburn</td>
      <td>43.770992</td>
      <td>-79.216917</td>
    </tr>
    <tr>
      <th>4</th>
      <td>M1H</td>
      <td>Scarborough</td>
      <td>Cedarbrae</td>
      <td>43.773136</td>
      <td>-79.239476</td>
    </tr>
    <tr>
      <th>5</th>
      <td>M1J</td>
      <td>Scarborough</td>
      <td>Scarborough Village</td>
      <td>43.744734</td>
      <td>-79.239476</td>
    </tr>
    <tr>
      <th>6</th>
      <td>M1K</td>
      <td>Scarborough</td>
      <td>Kennedy Park, Ionview, East Birchmount Park</td>
      <td>43.727929</td>
      <td>-79.262029</td>
    </tr>
    <tr>
      <th>7</th>
      <td>M1L</td>
      <td>Scarborough</td>
      <td>Golden Mile, Clairlea, Oakridge</td>
      <td>43.711112</td>
      <td>-79.284577</td>
    </tr>
    <tr>
      <th>8</th>
      <td>M1M</td>
      <td>Scarborough</td>
      <td>Cliffside, Cliffcrest, Scarborough Village West</td>
      <td>43.716316</td>
      <td>-79.239476</td>
    </tr>
    <tr>
      <th>9</th>
      <td>M1N</td>
      <td>Scarborough</td>
      <td>Birch Cliff, Cliffside West</td>
      <td>43.692657</td>
      <td>-79.264848</td>
    </tr>
    <tr>
      <th>10</th>
      <td>M1P</td>
      <td>Scarborough</td>
      <td>Dorset Park, Wexford Heights, Scarborough Town...</td>
      <td>43.757410</td>
      <td>-79.273304</td>
    </tr>
    <tr>
      <th>11</th>
      <td>M1R</td>
      <td>Scarborough</td>
      <td>Wexford, Maryvale</td>
      <td>43.750072</td>
      <td>-79.295849</td>
    </tr>
  </tbody>
</table>
</div>




```python
NBRHs.shape
```




    (103, 5)



## --------------------END OF PART 2----------------------------------

### PART 3: Explore and Cluster the Neighborhoods in Toronto

__Import needed libraries__


```python
import json

#comment or uncomment next row as needed
#!conda install -c conda-forge geopy --yes 
from geopy.geocoders import Nominatim # convert an address into latitude and longitude values

from pandas.io.json import json_normalize # tranform JSON file into a pandas dataframe

# Matplotlib and associated plotting modules
import matplotlib.cm as cm
import matplotlib.colors as colors

# import k-means from clustering stage
from sklearn.cluster import KMeans

#comment or uncomment next row as needed
#!conda install -c conda-forge folium=0.5.0 --yes
import folium # map render

```

__Filter data with Boroughs containing 'Toronto'__


```python
Toronto_filter= NBRHs[NBRHs['Borough'].str.contains('Toronto', na = False)].reset_index(drop=True)
Toronto_filter.head()
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
      <th>PostalCode</th>
      <th>Borough</th>
      <th>Neighbourhood</th>
      <th>Latitude</th>
      <th>Longitude</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>M4E</td>
      <td>East Toronto</td>
      <td>The Beaches</td>
      <td>43.676357</td>
      <td>-79.293031</td>
    </tr>
    <tr>
      <th>1</th>
      <td>M4K</td>
      <td>East Toronto</td>
      <td>The Danforth West, Riverdale</td>
      <td>43.679557</td>
      <td>-79.352188</td>
    </tr>
    <tr>
      <th>2</th>
      <td>M4L</td>
      <td>East Toronto</td>
      <td>India Bazaar, The Beaches West</td>
      <td>43.668999</td>
      <td>-79.315572</td>
    </tr>
    <tr>
      <th>3</th>
      <td>M4M</td>
      <td>East Toronto</td>
      <td>Studio District</td>
      <td>43.659526</td>
      <td>-79.340923</td>
    </tr>
    <tr>
      <th>4</th>
      <td>M4N</td>
      <td>Central Toronto</td>
      <td>Lawrence Park</td>
      <td>43.728020</td>
      <td>-79.388790</td>
    </tr>
  </tbody>
</table>
</div>




```python
Toronto_filter.shape
```




    (39, 5)



__39 Boroughs containing 'Toronto'__

__Get Toronto Coordinates from Google__


```python
lat = 43.6532
long = -79.3832
```

__Create map of Toronto with Boroughs containing 'Toronto'__


```python

Toronto = folium.Map(location=[lat, long], zoom_start=12)

# Markers
for latit, longit, label in zip(Toronto_filter['Latitude'], Toronto_filter['Longitude'], Toronto_filter['Neighbourhood']):
    label = folium.Popup(label, parse_html=True)
    folium.CircleMarker(
        [latit, longit],
        radius=5,
        popup=label,
        color='blue',
        fill=True,
        fill_color='blue',
        fill_opacity=0.3,
        parse_html=False).add_to(Toronto)  
    
Toronto
```




<div style="width:100%;"><div style="position:relative;width:100%;height:0;padding-bottom:60%;"><span style="color:#565656">Make this Notebook Trusted to load map: File -> Trust Notebook</span><iframe src="about:blank" style="position:absolute;width:100%;height:100%;left:0;top:0;border:none !important;" data-html=PCFET0NUWVBFIGh0bWw+CjxoZWFkPiAgICAKICAgIDxtZXRhIGh0dHAtZXF1aXY9ImNvbnRlbnQtdHlwZSIgY29udGVudD0idGV4dC9odG1sOyBjaGFyc2V0PVVURi04IiAvPgogICAgPHNjcmlwdD5MX1BSRUZFUl9DQU5WQVMgPSBmYWxzZTsgTF9OT19UT1VDSCA9IGZhbHNlOyBMX0RJU0FCTEVfM0QgPSBmYWxzZTs8L3NjcmlwdD4KICAgIDxzY3JpcHQgc3JjPSJodHRwczovL2Nkbi5qc2RlbGl2ci5uZXQvbnBtL2xlYWZsZXRAMS4yLjAvZGlzdC9sZWFmbGV0LmpzIj48L3NjcmlwdD4KICAgIDxzY3JpcHQgc3JjPSJodHRwczovL2FqYXguZ29vZ2xlYXBpcy5jb20vYWpheC9saWJzL2pxdWVyeS8xLjExLjEvanF1ZXJ5Lm1pbi5qcyI+PC9zY3JpcHQ+CiAgICA8c2NyaXB0IHNyYz0iaHR0cHM6Ly9tYXhjZG4uYm9vdHN0cmFwY2RuLmNvbS9ib290c3RyYXAvMy4yLjAvanMvYm9vdHN0cmFwLm1pbi5qcyI+PC9zY3JpcHQ+CiAgICA8c2NyaXB0IHNyYz0iaHR0cHM6Ly9jZG5qcy5jbG91ZGZsYXJlLmNvbS9hamF4L2xpYnMvTGVhZmxldC5hd2Vzb21lLW1hcmtlcnMvMi4wLjIvbGVhZmxldC5hd2Vzb21lLW1hcmtlcnMuanMiPjwvc2NyaXB0PgogICAgPGxpbmsgcmVsPSJzdHlsZXNoZWV0IiBocmVmPSJodHRwczovL2Nkbi5qc2RlbGl2ci5uZXQvbnBtL2xlYWZsZXRAMS4yLjAvZGlzdC9sZWFmbGV0LmNzcyIvPgogICAgPGxpbmsgcmVsPSJzdHlsZXNoZWV0IiBocmVmPSJodHRwczovL21heGNkbi5ib290c3RyYXBjZG4uY29tL2Jvb3RzdHJhcC8zLjIuMC9jc3MvYm9vdHN0cmFwLm1pbi5jc3MiLz4KICAgIDxsaW5rIHJlbD0ic3R5bGVzaGVldCIgaHJlZj0iaHR0cHM6Ly9tYXhjZG4uYm9vdHN0cmFwY2RuLmNvbS9ib290c3RyYXAvMy4yLjAvY3NzL2Jvb3RzdHJhcC10aGVtZS5taW4uY3NzIi8+CiAgICA8bGluayByZWw9InN0eWxlc2hlZXQiIGhyZWY9Imh0dHBzOi8vbWF4Y2RuLmJvb3RzdHJhcGNkbi5jb20vZm9udC1hd2Vzb21lLzQuNi4zL2Nzcy9mb250LWF3ZXNvbWUubWluLmNzcyIvPgogICAgPGxpbmsgcmVsPSJzdHlsZXNoZWV0IiBocmVmPSJodHRwczovL2NkbmpzLmNsb3VkZmxhcmUuY29tL2FqYXgvbGlicy9MZWFmbGV0LmF3ZXNvbWUtbWFya2Vycy8yLjAuMi9sZWFmbGV0LmF3ZXNvbWUtbWFya2Vycy5jc3MiLz4KICAgIDxsaW5rIHJlbD0ic3R5bGVzaGVldCIgaHJlZj0iaHR0cHM6Ly9yYXdnaXQuY29tL3B5dGhvbi12aXN1YWxpemF0aW9uL2ZvbGl1bS9tYXN0ZXIvZm9saXVtL3RlbXBsYXRlcy9sZWFmbGV0LmF3ZXNvbWUucm90YXRlLmNzcyIvPgogICAgPHN0eWxlPmh0bWwsIGJvZHkge3dpZHRoOiAxMDAlO2hlaWdodDogMTAwJTttYXJnaW46IDA7cGFkZGluZzogMDt9PC9zdHlsZT4KICAgIDxzdHlsZT4jbWFwIHtwb3NpdGlvbjphYnNvbHV0ZTt0b3A6MDtib3R0b206MDtyaWdodDowO2xlZnQ6MDt9PC9zdHlsZT4KICAgIAogICAgICAgICAgICA8c3R5bGU+ICNtYXBfMTZlNDUzMDQ4Yzc2NGRjZDgwZjBjNjU3YTRhZTQxMWUgewogICAgICAgICAgICAgICAgcG9zaXRpb24gOiByZWxhdGl2ZTsKICAgICAgICAgICAgICAgIHdpZHRoIDogMTAwLjAlOwogICAgICAgICAgICAgICAgaGVpZ2h0OiAxMDAuMCU7CiAgICAgICAgICAgICAgICBsZWZ0OiAwLjAlOwogICAgICAgICAgICAgICAgdG9wOiAwLjAlOwogICAgICAgICAgICAgICAgfQogICAgICAgICAgICA8L3N0eWxlPgogICAgICAgIAo8L2hlYWQ+Cjxib2R5PiAgICAKICAgIAogICAgICAgICAgICA8ZGl2IGNsYXNzPSJmb2xpdW0tbWFwIiBpZD0ibWFwXzE2ZTQ1MzA0OGM3NjRkY2Q4MGYwYzY1N2E0YWU0MTFlIiA+PC9kaXY+CiAgICAgICAgCjwvYm9keT4KPHNjcmlwdD4gICAgCiAgICAKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGJvdW5kcyA9IG51bGw7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgdmFyIG1hcF8xNmU0NTMwNDhjNzY0ZGNkODBmMGM2NTdhNGFlNDExZSA9IEwubWFwKAogICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgJ21hcF8xNmU0NTMwNDhjNzY0ZGNkODBmMGM2NTdhNGFlNDExZScsCiAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICB7Y2VudGVyOiBbNDMuNjUzMiwtNzkuMzgzMl0sCiAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICB6b29tOiAxMiwKICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgIG1heEJvdW5kczogYm91bmRzLAogICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgbGF5ZXJzOiBbXSwKICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgIHdvcmxkQ29weUp1bXA6IGZhbHNlLAogICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgY3JzOiBMLkNSUy5FUFNHMzg1NwogICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICB9KTsKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHRpbGVfbGF5ZXJfNDNmYTFjYWJlYzE2NGMzYmFhZmZmMGM2ZDEyYjVhOTMgPSBMLnRpbGVMYXllcigKICAgICAgICAgICAgICAgICdodHRwczovL3tzfS50aWxlLm9wZW5zdHJlZXRtYXAub3JnL3t6fS97eH0ve3l9LnBuZycsCiAgICAgICAgICAgICAgICB7CiAgImF0dHJpYnV0aW9uIjogbnVsbCwKICAiZGV0ZWN0UmV0aW5hIjogZmFsc2UsCiAgIm1heFpvb20iOiAxOCwKICAibWluWm9vbSI6IDEsCiAgIm5vV3JhcCI6IGZhbHNlLAogICJzdWJkb21haW5zIjogImFiYyIKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfMTZlNDUzMDQ4Yzc2NGRjZDgwZjBjNjU3YTRhZTQxMWUpOwogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyX2E5NDg2N2YwZDMwZTRkYWZhODhhZjY4NDBmZTA1NmNhID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNjc2MzU3Mzk5OTk5OTksLTc5LjI5MzAzMTJdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogImJsdWUiLAogICJmaWxsT3BhY2l0eSI6IDAuMywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfMTZlNDUzMDQ4Yzc2NGRjZDgwZjBjNjU3YTRhZTQxMWUpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfMjExODQwZTYxNWZiNDM2Mjk4ZWY2OTEzZDFlZTQxMTggPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfZmM4MTU1MDk5ZTJlNDM4MDk5OTI4OTZiNDJlMGExODEgPSAkKCc8ZGl2IGlkPSJodG1sX2ZjODE1NTA5OWUyZTQzODA5OTkyODk2YjQyZTBhMTgxIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5UaGUgQmVhY2hlczwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfMjExODQwZTYxNWZiNDM2Mjk4ZWY2OTEzZDFlZTQxMTguc2V0Q29udGVudChodG1sX2ZjODE1NTA5OWUyZTQzODA5OTkyODk2YjQyZTBhMTgxKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyX2E5NDg2N2YwZDMwZTRkYWZhODhhZjY4NDBmZTA1NmNhLmJpbmRQb3B1cChwb3B1cF8yMTE4NDBlNjE1ZmI0MzYyOThlZjY5MTNkMWVlNDExOCk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl85YWU5YTJiZmU3ZmQ0ZjhhODc2MGM4NjE5ZTAyZDQ5YiA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjY3OTU1NzEsLTc5LjM1MjE4OF0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiYmx1ZSIsCiAgImZpbGxPcGFjaXR5IjogMC4zLAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF8xNmU0NTMwNDhjNzY0ZGNkODBmMGM2NTdhNGFlNDExZSk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF85NjhlNzM3NDZjOTQ0Mjk4OGYwZGFmM2IzZmEyZmU1MiA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF8wMWVmNTlhOGU3NjQ0YTE2YmJkODY1YzZjM2IwNTFkZiA9ICQoJzxkaXYgaWQ9Imh0bWxfMDFlZjU5YThlNzY0NGExNmJiZDg2NWM2YzNiMDUxZGYiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPlRoZSBEYW5mb3J0aCBXZXN0LCBSaXZlcmRhbGU8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzk2OGU3Mzc0NmM5NDQyOTg4ZjBkYWYzYjNmYTJmZTUyLnNldENvbnRlbnQoaHRtbF8wMWVmNTlhOGU3NjQ0YTE2YmJkODY1YzZjM2IwNTFkZik7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl85YWU5YTJiZmU3ZmQ0ZjhhODc2MGM4NjE5ZTAyZDQ5Yi5iaW5kUG9wdXAocG9wdXBfOTY4ZTczNzQ2Yzk0NDI5ODhmMGRhZjNiM2ZhMmZlNTIpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfNzgzOGRmMDM1OTJmNDc1MTk5MGJhNWNhYmQyOTFhMmEgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42Njg5OTg1LC03OS4zMTU1NzE1OTk5OTk5OF0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiYmx1ZSIsCiAgImZpbGxPcGFjaXR5IjogMC4zLAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF8xNmU0NTMwNDhjNzY0ZGNkODBmMGM2NTdhNGFlNDExZSk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF80M2MyMzljOTM3NzU0MTU1YjE0ZDcwMGQxMTQ1MjVkMyA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF9iZThjNzg3NWY5ZjQ0ZDBiYmMxOTFjMmUwOGYyYWVkNyA9ICQoJzxkaXYgaWQ9Imh0bWxfYmU4Yzc4NzVmOWY0NGQwYmJjMTkxYzJlMDhmMmFlZDciIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkluZGlhIEJhemFhciwgVGhlIEJlYWNoZXMgV2VzdDwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfNDNjMjM5YzkzNzc1NDE1NWIxNGQ3MDBkMTE0NTI1ZDMuc2V0Q29udGVudChodG1sX2JlOGM3ODc1ZjlmNDRkMGJiYzE5MWMyZTA4ZjJhZWQ3KTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzc4MzhkZjAzNTkyZjQ3NTE5OTBiYTVjYWJkMjkxYTJhLmJpbmRQb3B1cChwb3B1cF80M2MyMzljOTM3NzU0MTU1YjE0ZDcwMGQxMTQ1MjVkMyk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl9iNjhmMjY2MWZjMzU0NmYzYjQyNjk1N2EzNDk1ZjQzYiA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjY1OTUyNTUsLTc5LjM0MDkyM10sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiYmx1ZSIsCiAgImZpbGxPcGFjaXR5IjogMC4zLAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF8xNmU0NTMwNDhjNzY0ZGNkODBmMGM2NTdhNGFlNDExZSk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF9jMjM4N2ZhMTU0NDY0ODdmYWFkNTI0YmNlMzcyZWUyNiA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF8zMzNjZjJjYTg2NmE0ZjBlOGY1YjNlOWNmZTZhYTVmOCA9ICQoJzxkaXYgaWQ9Imh0bWxfMzMzY2YyY2E4NjZhNGYwZThmNWIzZTljZmU2YWE1ZjgiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPlN0dWRpbyBEaXN0cmljdDwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfYzIzODdmYTE1NDQ2NDg3ZmFhZDUyNGJjZTM3MmVlMjYuc2V0Q29udGVudChodG1sXzMzM2NmMmNhODY2YTRmMGU4ZjViM2U5Y2ZlNmFhNWY4KTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyX2I2OGYyNjYxZmMzNTQ2ZjNiNDI2OTU3YTM0OTVmNDNiLmJpbmRQb3B1cChwb3B1cF9jMjM4N2ZhMTU0NDY0ODdmYWFkNTI0YmNlMzcyZWUyNik7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl81N2Y3OWIzMzMwYzQ0ZWJmOTA4NDk1NzM0MzMwZGI0MCA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjcyODAyMDUsLTc5LjM4ODc5MDFdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogImJsdWUiLAogICJmaWxsT3BhY2l0eSI6IDAuMywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfMTZlNDUzMDQ4Yzc2NGRjZDgwZjBjNjU3YTRhZTQxMWUpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfZjUzMTI5NzY0ODQwNDA1ZmIwNGM5MmIyNDE0OWM2MTMgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfYmU5MWVmZDUwN2Q3NGMyNTlkNGNlODZjNzYzNjgzN2YgPSAkKCc8ZGl2IGlkPSJodG1sX2JlOTFlZmQ1MDdkNzRjMjU5ZDRjZTg2Yzc2MzY4MzdmIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5MYXdyZW5jZSBQYXJrPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF9mNTMxMjk3NjQ4NDA0MDVmYjA0YzkyYjI0MTQ5YzYxMy5zZXRDb250ZW50KGh0bWxfYmU5MWVmZDUwN2Q3NGMyNTlkNGNlODZjNzYzNjgzN2YpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfNTdmNzliMzMzMGM0NGViZjkwODQ5NTczNDMzMGRiNDAuYmluZFBvcHVwKHBvcHVwX2Y1MzEyOTc2NDg0MDQwNWZiMDRjOTJiMjQxNDljNjEzKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzcxZWIyMWUwNmZmZDQ5Y2Q4YjM1OWJmYzI4NWZhMjczID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNzEyNzUxMSwtNzkuMzkwMTk3NV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiYmx1ZSIsCiAgImZpbGxPcGFjaXR5IjogMC4zLAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF8xNmU0NTMwNDhjNzY0ZGNkODBmMGM2NTdhNGFlNDExZSk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF8xOWZjZGVjYTcwY2M0YjM0ODMyMTkyYTc4MWQyZmE1YiA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF9jNThmNDM0Y2Y2ZjA0MzRiYTUxYmU4YjdjZDAwNjU3NCA9ICQoJzxkaXYgaWQ9Imh0bWxfYzU4ZjQzNGNmNmYwNDM0YmE1MWJlOGI3Y2QwMDY1NzQiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkRhdmlzdmlsbGUgTm9ydGg8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzE5ZmNkZWNhNzBjYzRiMzQ4MzIxOTJhNzgxZDJmYTViLnNldENvbnRlbnQoaHRtbF9jNThmNDM0Y2Y2ZjA0MzRiYTUxYmU4YjdjZDAwNjU3NCk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl83MWViMjFlMDZmZmQ0OWNkOGIzNTliZmMyODVmYTI3My5iaW5kUG9wdXAocG9wdXBfMTlmY2RlY2E3MGNjNGIzNDgzMjE5MmE3ODFkMmZhNWIpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfMjA0YjUwZjQ1YTBlNDU0NWE3ODQyMzczOTJhY2UwMzEgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My43MTUzODM0LC03OS40MDU2Nzg0MDAwMDAwMV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiYmx1ZSIsCiAgImZpbGxPcGFjaXR5IjogMC4zLAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF8xNmU0NTMwNDhjNzY0ZGNkODBmMGM2NTdhNGFlNDExZSk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF9jYWU2YWNlZWRjODY0M2JlOThlZjg1Zjg4ZDMwYjUwNyA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF85NTg0Zjc0MjgxMWU0M2JhYTgyOWZkNDZlZTUxZDkxZCA9ICQoJzxkaXYgaWQ9Imh0bWxfOTU4NGY3NDI4MTFlNDNiYWE4MjlmZDQ2ZWU1MWQ5MWQiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPk5vcnRoIFRvcm9udG8gV2VzdCwgIExhd3JlbmNlIFBhcms8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwX2NhZTZhY2VlZGM4NjQzYmU5OGVmODVmODhkMzBiNTA3LnNldENvbnRlbnQoaHRtbF85NTg0Zjc0MjgxMWU0M2JhYTgyOWZkNDZlZTUxZDkxZCk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl8yMDRiNTBmNDVhMGU0NTQ1YTc4NDIzNzM5MmFjZTAzMS5iaW5kUG9wdXAocG9wdXBfY2FlNmFjZWVkYzg2NDNiZTk4ZWY4NWY4OGQzMGI1MDcpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfMDU1NWUwOWQwMzdkNDMzNWEyZWM3MjNkODM3MjJiMTEgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My43MDQzMjQ0LC03OS4zODg3OTAxXSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImJsdWUiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICJibHVlIiwKICAiZmlsbE9wYWNpdHkiOiAwLjMsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzE2ZTQ1MzA0OGM3NjRkY2Q4MGYwYzY1N2E0YWU0MTFlKTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwX2E3MzgyMmExMGU4MzQ1MjY5NWI1YzcxOTIzNWMxZTc3ID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzVmMmEwOGYxNGRlMTRiODRhMmMzNTI4YjNkMjc1N2E4ID0gJCgnPGRpdiBpZD0iaHRtbF81ZjJhMDhmMTRkZTE0Yjg0YTJjMzUyOGIzZDI3NTdhOCIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+RGF2aXN2aWxsZTwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfYTczODIyYTEwZTgzNDUyNjk1YjVjNzE5MjM1YzFlNzcuc2V0Q29udGVudChodG1sXzVmMmEwOGYxNGRlMTRiODRhMmMzNTI4YjNkMjc1N2E4KTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzA1NTVlMDlkMDM3ZDQzMzVhMmVjNzIzZDgzNzIyYjExLmJpbmRQb3B1cChwb3B1cF9hNzM4MjJhMTBlODM0NTI2OTViNWM3MTkyMzVjMWU3Nyk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl9mNTM1ZWE2ZWIzZjk0ZmYwYjg0ZmVkZDUyNjdlYTYzOSA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjY4OTU3NDMsLTc5LjM4MzE1OTkwMDAwMDAxXSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImJsdWUiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICJibHVlIiwKICAiZmlsbE9wYWNpdHkiOiAwLjMsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzE2ZTQ1MzA0OGM3NjRkY2Q4MGYwYzY1N2E0YWU0MTFlKTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzI5YjFlMThkMmE4NjRhZjJiYjI2Y2FjZGQ4NDkwYmNmID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzUxMGJiZmZjMTJmMjQ1MjBiZTM1OTUxMTNhNjY1YWI4ID0gJCgnPGRpdiBpZD0iaHRtbF81MTBiYmZmYzEyZjI0NTIwYmUzNTk1MTEzYTY2NWFiOCIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+TW9vcmUgUGFyaywgU3VtbWVyaGlsbCBFYXN0PC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF8yOWIxZTE4ZDJhODY0YWYyYmIyNmNhY2RkODQ5MGJjZi5zZXRDb250ZW50KGh0bWxfNTEwYmJmZmMxMmYyNDUyMGJlMzU5NTExM2E2NjVhYjgpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfZjUzNWVhNmViM2Y5NGZmMGI4NGZlZGQ1MjY3ZWE2MzkuYmluZFBvcHVwKHBvcHVwXzI5YjFlMThkMmE4NjRhZjJiYjI2Y2FjZGQ4NDkwYmNmKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzQ3NTdhNGY0MGU2MzRmYzBhYjJhM2I2NjZmYjBlMmJkID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNjg2NDEyMjk5OTk5OTksLTc5LjQwMDA0OTNdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogImJsdWUiLAogICJmaWxsT3BhY2l0eSI6IDAuMywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfMTZlNDUzMDQ4Yzc2NGRjZDgwZjBjNjU3YTRhZTQxMWUpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfYmIwMzk3NTQ4YzYzNDIxZmE2NDU2ZmIxNDM2MjZiZjggPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfMTIwZjdkMGUxNzZkNDczZDgyMjVmYTc4NzIxZDg5MmMgPSAkKCc8ZGl2IGlkPSJodG1sXzEyMGY3ZDBlMTc2ZDQ3M2Q4MjI1ZmE3ODcyMWQ4OTJjIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5TdW1tZXJoaWxsIFdlc3QsIFJhdGhuZWxseSwgU291dGggSGlsbCwgRm9yZXN0IEhpbGwgU0UsIERlZXIgUGFyazwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfYmIwMzk3NTQ4YzYzNDIxZmE2NDU2ZmIxNDM2MjZiZjguc2V0Q29udGVudChodG1sXzEyMGY3ZDBlMTc2ZDQ3M2Q4MjI1ZmE3ODcyMWQ4OTJjKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzQ3NTdhNGY0MGU2MzRmYzBhYjJhM2I2NjZmYjBlMmJkLmJpbmRQb3B1cChwb3B1cF9iYjAzOTc1NDhjNjM0MjFmYTY0NTZmYjE0MzYyNmJmOCk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl9kMDk2ZjdlZjMzMjE0ZGM4YmY4NTA0NWQ0MTVjZTQyZCA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjY3OTU2MjYsLTc5LjM3NzUyOTQwMDAwMDAxXSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImJsdWUiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICJibHVlIiwKICAiZmlsbE9wYWNpdHkiOiAwLjMsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzE2ZTQ1MzA0OGM3NjRkY2Q4MGYwYzY1N2E0YWU0MTFlKTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzQ5MzAyNjUxNjU4MDQyYTViYjZjMGFhZWZlMWMzOTkxID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzgyZmQxZmFjYzkxMTQ4NWZhYTBiODQ1MDFkMjFiZGRhID0gJCgnPGRpdiBpZD0iaHRtbF84MmZkMWZhY2M5MTE0ODVmYWEwYjg0NTAxZDIxYmRkYSIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+Um9zZWRhbGU8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzQ5MzAyNjUxNjU4MDQyYTViYjZjMGFhZWZlMWMzOTkxLnNldENvbnRlbnQoaHRtbF84MmZkMWZhY2M5MTE0ODVmYWEwYjg0NTAxZDIxYmRkYSk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl9kMDk2ZjdlZjMzMjE0ZGM4YmY4NTA0NWQ0MTVjZTQyZC5iaW5kUG9wdXAocG9wdXBfNDkzMDI2NTE2NTgwNDJhNWJiNmMwYWFlZmUxYzM5OTEpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfOGVlNmNmOWQyYzc1NDY5OGI1Njg0MzA2Yzk4NDA4YTMgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42Njc5NjcsLTc5LjM2NzY3NTNdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogImJsdWUiLAogICJmaWxsT3BhY2l0eSI6IDAuMywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfMTZlNDUzMDQ4Yzc2NGRjZDgwZjBjNjU3YTRhZTQxMWUpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfNGM1MWNkOGE0ZmY5NDkwMjkwZDVjN2M1M2Q5Zjg1MTEgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfZDI1Mzg2NGE0NTA5NGIyNzk1YjhmYmQ3M2YzZTQ1NDIgPSAkKCc8ZGl2IGlkPSJodG1sX2QyNTM4NjRhNDUwOTRiMjc5NWI4ZmJkNzNmM2U0NTQyIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5TdC4gSmFtZXMgVG93biwgQ2FiYmFnZXRvd248L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzRjNTFjZDhhNGZmOTQ5MDI5MGQ1YzdjNTNkOWY4NTExLnNldENvbnRlbnQoaHRtbF9kMjUzODY0YTQ1MDk0YjI3OTViOGZiZDczZjNlNDU0Mik7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl84ZWU2Y2Y5ZDJjNzU0Njk4YjU2ODQzMDZjOTg0MDhhMy5iaW5kUG9wdXAocG9wdXBfNGM1MWNkOGE0ZmY5NDkwMjkwZDVjN2M1M2Q5Zjg1MTEpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfYzM2Y2JlYjNhMTJjNGExYzg1MmM2YWUwZjExYTc1NmUgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42NjU4NTk5LC03OS4zODMxNTk5MDAwMDAwMV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiYmx1ZSIsCiAgImZpbGxPcGFjaXR5IjogMC4zLAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF8xNmU0NTMwNDhjNzY0ZGNkODBmMGM2NTdhNGFlNDExZSk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF9lZTJmN2E5Mjk4NjI0N2Q0ODUwYTU3YWMxZTZkZTJkMCA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF8wODU4ODA2ODljYWI0MmU2OWE3ZTRhMGMzYmQ5YTViZCA9ICQoJzxkaXYgaWQ9Imh0bWxfMDg1ODgwNjg5Y2FiNDJlNjlhN2U0YTBjM2JkOWE1YmQiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkNodXJjaCBhbmQgV2VsbGVzbGV5PC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF9lZTJmN2E5Mjk4NjI0N2Q0ODUwYTU3YWMxZTZkZTJkMC5zZXRDb250ZW50KGh0bWxfMDg1ODgwNjg5Y2FiNDJlNjlhN2U0YTBjM2JkOWE1YmQpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfYzM2Y2JlYjNhMTJjNGExYzg1MmM2YWUwZjExYTc1NmUuYmluZFBvcHVwKHBvcHVwX2VlMmY3YTkyOTg2MjQ3ZDQ4NTBhNTdhYzFlNmRlMmQwKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzc4NTkxNzk4ODU4ZjRkZTJhZTY4M2I0MDhlOTU4MDFkID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNjU0MjU5OSwtNzkuMzYwNjM1OV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiYmx1ZSIsCiAgImZpbGxPcGFjaXR5IjogMC4zLAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF8xNmU0NTMwNDhjNzY0ZGNkODBmMGM2NTdhNGFlNDExZSk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF8xOTczMjIyMWQ0NTQ0NWMwYmE4ZGU2NzFkNGU1NzIwNSA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF84NzYwMTAzZjU4YmI0MThlODhkMDBhMmU4Y2YyMDFhNSA9ICQoJzxkaXYgaWQ9Imh0bWxfODc2MDEwM2Y1OGJiNDE4ZTg4ZDAwYTJlOGNmMjAxYTUiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPlJlZ2VudCBQYXJrLCBIYXJib3VyZnJvbnQ8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzE5NzMyMjIxZDQ1NDQ1YzBiYThkZTY3MWQ0ZTU3MjA1LnNldENvbnRlbnQoaHRtbF84NzYwMTAzZjU4YmI0MThlODhkMDBhMmU4Y2YyMDFhNSk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl83ODU5MTc5ODg1OGY0ZGUyYWU2ODNiNDA4ZTk1ODAxZC5iaW5kUG9wdXAocG9wdXBfMTk3MzIyMjFkNDU0NDVjMGJhOGRlNjcxZDRlNTcyMDUpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfMjJlZTM4ZGNhN2RhNDcyZDk3YTIwNTdkZDE4N2NhY2UgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42NTcxNjE4LC03OS4zNzg5MzcwOTk5OTk5OV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiYmx1ZSIsCiAgImZpbGxPcGFjaXR5IjogMC4zLAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF8xNmU0NTMwNDhjNzY0ZGNkODBmMGM2NTdhNGFlNDExZSk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF9kNjRiNDNhZGM5NTU0YmEzOGQwMDBlNWVhZDM0MjQ0YiA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF80NTIwNzA4NDIwNDc0NjMwYmFmZDJlYTgyODI5MWViNyA9ICQoJzxkaXYgaWQ9Imh0bWxfNDUyMDcwODQyMDQ3NDYzMGJhZmQyZWE4MjgyOTFlYjciIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkdhcmRlbiBEaXN0cmljdCwgUnllcnNvbjwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfZDY0YjQzYWRjOTU1NGJhMzhkMDAwZTVlYWQzNDI0NGIuc2V0Q29udGVudChodG1sXzQ1MjA3MDg0MjA0NzQ2MzBiYWZkMmVhODI4MjkxZWI3KTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzIyZWUzOGRjYTdkYTQ3MmQ5N2EyMDU3ZGQxODdjYWNlLmJpbmRQb3B1cChwb3B1cF9kNjRiNDNhZGM5NTU0YmEzOGQwMDBlNWVhZDM0MjQ0Yik7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl9jNWM5M2YwZjdkMDg0OWVmYjQyYTY1YzZiMjBmNTg0ZiA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjY1MTQ5MzksLTc5LjM3NTQxNzldLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogImJsdWUiLAogICJmaWxsT3BhY2l0eSI6IDAuMywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfMTZlNDUzMDQ4Yzc2NGRjZDgwZjBjNjU3YTRhZTQxMWUpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfZTY3NGE3NWI5OWFiNDdjNzkxMGQxNzNjZGNiZDZkZTIgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfMzI1Y2NjYWRlZDQ5NGNkM2I0ZDJjYWZkMzkxNGUyYTIgPSAkKCc8ZGl2IGlkPSJodG1sXzMyNWNjY2FkZWQ0OTRjZDNiNGQyY2FmZDM5MTRlMmEyIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5TdC4gSmFtZXMgVG93bjwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfZTY3NGE3NWI5OWFiNDdjNzkxMGQxNzNjZGNiZDZkZTIuc2V0Q29udGVudChodG1sXzMyNWNjY2FkZWQ0OTRjZDNiNGQyY2FmZDM5MTRlMmEyKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyX2M1YzkzZjBmN2QwODQ5ZWZiNDJhNjVjNmIyMGY1ODRmLmJpbmRQb3B1cChwb3B1cF9lNjc0YTc1Yjk5YWI0N2M3OTEwZDE3M2NkY2JkNmRlMik7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl9kNjBkNzE0NDRhZjE0MTg0YTE4YTZhODlmZThmMTMxYSA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjY0NDc3MDc5OTk5OTk5NiwtNzkuMzczMzA2NF0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiYmx1ZSIsCiAgImZpbGxPcGFjaXR5IjogMC4zLAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF8xNmU0NTMwNDhjNzY0ZGNkODBmMGM2NTdhNGFlNDExZSk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF85YmY1YWFjOGQ1OTU0NGNlYWRiYzZlMzI5ZjU0YzU5ZiA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF9lZGJiZjNiMjkzYTA0MGRjYTkzYjZhODA3YmQ1YTJmZiA9ICQoJzxkaXYgaWQ9Imh0bWxfZWRiYmYzYjI5M2EwNDBkY2E5M2I2YTgwN2JkNWEyZmYiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkJlcmN6eSBQYXJrPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF85YmY1YWFjOGQ1OTU0NGNlYWRiYzZlMzI5ZjU0YzU5Zi5zZXRDb250ZW50KGh0bWxfZWRiYmYzYjI5M2EwNDBkY2E5M2I2YTgwN2JkNWEyZmYpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfZDYwZDcxNDQ0YWYxNDE4NGExOGE2YTg5ZmU4ZjEzMWEuYmluZFBvcHVwKHBvcHVwXzliZjVhYWM4ZDU5NTQ0Y2VhZGJjNmUzMjlmNTRjNTlmKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzk5ODFkYTFjN2UyODQ0ZTdiMTRkZjk2ZWEzMzgwNjRjID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNjU3OTUyNCwtNzkuMzg3MzgyNl0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiYmx1ZSIsCiAgImZpbGxPcGFjaXR5IjogMC4zLAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF8xNmU0NTMwNDhjNzY0ZGNkODBmMGM2NTdhNGFlNDExZSk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF82NGVkY2E0YWRhNWM0MThlOGY0NDNiOGM3ZGIxZjA5YiA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF8wZDQxYjZhOTBjZmI0OWIwYjFiMzc2M2EyYTMzYjg2ZSA9ICQoJzxkaXYgaWQ9Imh0bWxfMGQ0MWI2YTkwY2ZiNDliMGIxYjM3NjNhMmEzM2I4NmUiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkNlbnRyYWwgQmF5IFN0cmVldDwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfNjRlZGNhNGFkYTVjNDE4ZThmNDQzYjhjN2RiMWYwOWIuc2V0Q29udGVudChodG1sXzBkNDFiNmE5MGNmYjQ5YjBiMWIzNzYzYTJhMzNiODZlKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzk5ODFkYTFjN2UyODQ0ZTdiMTRkZjk2ZWEzMzgwNjRjLmJpbmRQb3B1cChwb3B1cF82NGVkY2E0YWRhNWM0MThlOGY0NDNiOGM3ZGIxZjA5Yik7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl85MTQ4NDExY2EyNDQ0ZTAwYjY0N2I2Nzk1OWEyOTJlZiA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjY1MDU3MTIwMDAwMDAxLC03OS4zODQ1Njc1XSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImJsdWUiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICJibHVlIiwKICAiZmlsbE9wYWNpdHkiOiAwLjMsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzE2ZTQ1MzA0OGM3NjRkY2Q4MGYwYzY1N2E0YWU0MTFlKTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzM3MGJlYjI5MWQ1NzQ2OWRiMTVkMTQzMjZjYmIzMDEwID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sX2NkMWZlZDE5NDJmMjRiNWZhZWFiMWUyNDZkNTUxODEzID0gJCgnPGRpdiBpZD0iaHRtbF9jZDFmZWQxOTQyZjI0YjVmYWVhYjFlMjQ2ZDU1MTgxMyIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+UmljaG1vbmQsIEFkZWxhaWRlLCBLaW5nPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF8zNzBiZWIyOTFkNTc0NjlkYjE1ZDE0MzI2Y2JiMzAxMC5zZXRDb250ZW50KGh0bWxfY2QxZmVkMTk0MmYyNGI1ZmFlYWIxZTI0NmQ1NTE4MTMpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfOTE0ODQxMWNhMjQ0NGUwMGI2NDdiNjc5NTlhMjkyZWYuYmluZFBvcHVwKHBvcHVwXzM3MGJlYjI5MWQ1NzQ2OWRiMTVkMTQzMjZjYmIzMDEwKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzA3MWRjZGJjNjAxYzQ3MWI5ZDIxZWMxOTQ3MDgwMzM5ID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNjQwODE1NywtNzkuMzgxNzUyMjk5OTk5OTldLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogImJsdWUiLAogICJmaWxsT3BhY2l0eSI6IDAuMywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfMTZlNDUzMDQ4Yzc2NGRjZDgwZjBjNjU3YTRhZTQxMWUpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfNmVjNTAxNzc5YzYwNGM2YmFiNzIxZTc2NThiZDIwMWMgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfMjUxMWZmMzI1ODAyNDI3YjgzZDA2MjIxZjYwNGExNTkgPSAkKCc8ZGl2IGlkPSJodG1sXzI1MTFmZjMyNTgwMjQyN2I4M2QwNjIyMWY2MDRhMTU5IiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5IYXJib3VyZnJvbnQgRWFzdCwgVW5pb24gU3RhdGlvbiwgVG9yb250byBJc2xhbmRzPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF82ZWM1MDE3NzljNjA0YzZiYWI3MjFlNzY1OGJkMjAxYy5zZXRDb250ZW50KGh0bWxfMjUxMWZmMzI1ODAyNDI3YjgzZDA2MjIxZjYwNGExNTkpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfMDcxZGNkYmM2MDFjNDcxYjlkMjFlYzE5NDcwODAzMzkuYmluZFBvcHVwKHBvcHVwXzZlYzUwMTc3OWM2MDRjNmJhYjcyMWU3NjU4YmQyMDFjKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzRhMmM0NmFiMTViMTQ5ZjBhYTBjODgxNjgyMzllNGMyID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNjQ3MTc2OCwtNzkuMzgxNTc2NDAwMDAwMDFdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogImJsdWUiLAogICJmaWxsT3BhY2l0eSI6IDAuMywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfMTZlNDUzMDQ4Yzc2NGRjZDgwZjBjNjU3YTRhZTQxMWUpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfMTNkMzAxNTc0MjNiNDgwOWE1ODVjMWE5MzExZTVlMDUgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfNWNmNDczNWEwMDA3NGMyOWJmOTRhNzkxOWQ2NWI3Y2UgPSAkKCc8ZGl2IGlkPSJodG1sXzVjZjQ3MzVhMDAwNzRjMjliZjk0YTc5MTlkNjViN2NlIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5Ub3JvbnRvIERvbWluaW9uIENlbnRyZSwgRGVzaWduIEV4Y2hhbmdlPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF8xM2QzMDE1NzQyM2I0ODA5YTU4NWMxYTkzMTFlNWUwNS5zZXRDb250ZW50KGh0bWxfNWNmNDczNWEwMDA3NGMyOWJmOTRhNzkxOWQ2NWI3Y2UpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfNGEyYzQ2YWIxNWIxNDlmMGFhMGM4ODE2ODIzOWU0YzIuYmluZFBvcHVwKHBvcHVwXzEzZDMwMTU3NDIzYjQ4MDlhNTg1YzFhOTMxMWU1ZTA1KTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzBkY2QyMmE4M2E0MjQ5YjRiZDIxZDA0ZGQ2YWM2YzgwID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNjQ4MTk4NSwtNzkuMzc5ODE2OTAwMDAwMDFdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogImJsdWUiLAogICJmaWxsT3BhY2l0eSI6IDAuMywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfMTZlNDUzMDQ4Yzc2NGRjZDgwZjBjNjU3YTRhZTQxMWUpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfMGM2NzlkYTAzNTUyNDU2ZTgyNjgxZmQwNzBhYTI2MWYgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfNmQxNTc3NmNiZGNlNDM0OWI5MTRiMDE2YmMxYTg3NWEgPSAkKCc8ZGl2IGlkPSJodG1sXzZkMTU3NzZjYmRjZTQzNDliOTE0YjAxNmJjMWE4NzVhIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5Db21tZXJjZSBDb3VydCwgVmljdG9yaWEgSG90ZWw8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzBjNjc5ZGEwMzU1MjQ1NmU4MjY4MWZkMDcwYWEyNjFmLnNldENvbnRlbnQoaHRtbF82ZDE1Nzc2Y2JkY2U0MzQ5YjkxNGIwMTZiYzFhODc1YSk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl8wZGNkMjJhODNhNDI0OWI0YmQyMWQwNGRkNmFjNmM4MC5iaW5kUG9wdXAocG9wdXBfMGM2NzlkYTAzNTUyNDU2ZTgyNjgxZmQwNzBhYTI2MWYpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfNDUwNmFiYjhkZGJmNDA5N2IzMWQ5ZGZhMTMzM2VmMmUgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My43MTE2OTQ4LC03OS40MTY5MzU1OTk5OTk5OV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiYmx1ZSIsCiAgImZpbGxPcGFjaXR5IjogMC4zLAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF8xNmU0NTMwNDhjNzY0ZGNkODBmMGM2NTdhNGFlNDExZSk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF8zYmNmNTQ2YTBkMjY0OWU0YmQwMDAwNGRhMWI4M2M3YyA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF9jOTBhNmFhNTk0MTg0MTViYWQyMTMzMTk1NWFhNjEwYiA9ICQoJzxkaXYgaWQ9Imh0bWxfYzkwYTZhYTU5NDE4NDE1YmFkMjEzMzE5NTVhYTYxMGIiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPlJvc2VsYXduPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF8zYmNmNTQ2YTBkMjY0OWU0YmQwMDAwNGRhMWI4M2M3Yy5zZXRDb250ZW50KGh0bWxfYzkwYTZhYTU5NDE4NDE1YmFkMjEzMzE5NTVhYTYxMGIpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfNDUwNmFiYjhkZGJmNDA5N2IzMWQ5ZGZhMTMzM2VmMmUuYmluZFBvcHVwKHBvcHVwXzNiY2Y1NDZhMGQyNjQ5ZTRiZDAwMDA0ZGExYjgzYzdjKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyX2JhMWQyMTE4OTJmYTRjNjk5ZTJjYWVjYTk1MDVkMGE2ID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNjk2OTQ3NiwtNzkuNDExMzA3MjAwMDAwMDFdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogImJsdWUiLAogICJmaWxsT3BhY2l0eSI6IDAuMywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfMTZlNDUzMDQ4Yzc2NGRjZDgwZjBjNjU3YTRhZTQxMWUpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfMjQyN2Q2NGFhNjMxNDlhY2FlYWMxNjYzMGFhYmJkNmEgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfMzc4NmIzMjYzMmE3NGE3MmE2NGNhNjhkYjA0MGU2NDQgPSAkKCc8ZGl2IGlkPSJodG1sXzM3ODZiMzI2MzJhNzRhNzJhNjRjYTY4ZGIwNDBlNjQ0IiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5Gb3Jlc3QgSGlsbCBOb3J0aCAmYW1wOyBXZXN0LCBGb3Jlc3QgSGlsbCBSb2FkIFBhcms8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzI0MjdkNjRhYTYzMTQ5YWNhZWFjMTY2MzBhYWJiZDZhLnNldENvbnRlbnQoaHRtbF8zNzg2YjMyNjMyYTc0YTcyYTY0Y2E2OGRiMDQwZTY0NCk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl9iYTFkMjExODkyZmE0YzY5OWUyY2FlY2E5NTA1ZDBhNi5iaW5kUG9wdXAocG9wdXBfMjQyN2Q2NGFhNjMxNDlhY2FlYWMxNjYzMGFhYmJkNmEpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfNTA0MGExZDYxYTFlNDAxZTg0ZDk3NzI0YjM2NDFmNjUgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42NzI3MDk3LC03OS40MDU2Nzg0MDAwMDAwMV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiYmx1ZSIsCiAgImZpbGxPcGFjaXR5IjogMC4zLAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF8xNmU0NTMwNDhjNzY0ZGNkODBmMGM2NTdhNGFlNDExZSk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF9iNzVkMzFkZmQ5NDc0Y2I3ODQ3OWQ2ODdlZDFiZWJmMSA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF80NDhiYjY3NDQxYzM0YTQ4Yjg1YTg4Mzc1YzdmOTY2ZSA9ICQoJzxkaXYgaWQ9Imh0bWxfNDQ4YmI2NzQ0MWMzNGE0OGI4NWE4ODM3NWM3Zjk2NmUiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPlRoZSBBbm5leCwgTm9ydGggTWlkdG93biwgWW9ya3ZpbGxlPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF9iNzVkMzFkZmQ5NDc0Y2I3ODQ3OWQ2ODdlZDFiZWJmMS5zZXRDb250ZW50KGh0bWxfNDQ4YmI2NzQ0MWMzNGE0OGI4NWE4ODM3NWM3Zjk2NmUpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfNTA0MGExZDYxYTFlNDAxZTg0ZDk3NzI0YjM2NDFmNjUuYmluZFBvcHVwKHBvcHVwX2I3NWQzMWRmZDk0NzRjYjc4NDc5ZDY4N2VkMWJlYmYxKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyX2I4ZDZmODQwNjVkNTQ4ZTBhNTY4ZWM3N2UzODExNzZhID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNjYyNjk1NiwtNzkuNDAwMDQ5M10sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiYmx1ZSIsCiAgImZpbGxPcGFjaXR5IjogMC4zLAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF8xNmU0NTMwNDhjNzY0ZGNkODBmMGM2NTdhNGFlNDExZSk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF83MTZmZjFkZWNlMGE0NjY1YWU1MDI1NWVhNTI1ZWM4YSA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF80YmNkY2FmYTFkMzY0YThiYTQzZjZmYWI1YzM2MzhjNCA9ICQoJzxkaXYgaWQ9Imh0bWxfNGJjZGNhZmExZDM2NGE4YmE0M2Y2ZmFiNWMzNjM4YzQiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPlVuaXZlcnNpdHkgb2YgVG9yb250bywgSGFyYm9yZDwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfNzE2ZmYxZGVjZTBhNDY2NWFlNTAyNTVlYTUyNWVjOGEuc2V0Q29udGVudChodG1sXzRiY2RjYWZhMWQzNjRhOGJhNDNmNmZhYjVjMzYzOGM0KTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyX2I4ZDZmODQwNjVkNTQ4ZTBhNTY4ZWM3N2UzODExNzZhLmJpbmRQb3B1cChwb3B1cF83MTZmZjFkZWNlMGE0NjY1YWU1MDI1NWVhNTI1ZWM4YSk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl8yNmY1Y2Y3ODAyOWE0YjgzYTI5NWQ5ZGYyMTcxYTMwOSA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjY1MzIwNTcsLTc5LjQwMDA0OTNdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogImJsdWUiLAogICJmaWxsT3BhY2l0eSI6IDAuMywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfMTZlNDUzMDQ4Yzc2NGRjZDgwZjBjNjU3YTRhZTQxMWUpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfMzM5YWIxMDQ4OGE4NGYwMmEyYmUwZGIwMDU0MDg0MjAgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfMTU3YmE1ZWQzNzU3NGU4ZGJhNmRiNTliOTdjYTMwOWYgPSAkKCc8ZGl2IGlkPSJodG1sXzE1N2JhNWVkMzc1NzRlOGRiYTZkYjU5Yjk3Y2EzMDlmIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5LZW5zaW5ndG9uIE1hcmtldCwgQ2hpbmF0b3duLCBHcmFuZ2UgUGFyazwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfMzM5YWIxMDQ4OGE4NGYwMmEyYmUwZGIwMDU0MDg0MjAuc2V0Q29udGVudChodG1sXzE1N2JhNWVkMzc1NzRlOGRiYTZkYjU5Yjk3Y2EzMDlmKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzI2ZjVjZjc4MDI5YTRiODNhMjk1ZDlkZjIxNzFhMzA5LmJpbmRQb3B1cChwb3B1cF8zMzlhYjEwNDg4YTg0ZjAyYTJiZTBkYjAwNTQwODQyMCk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl85M2I4NWJkMmM0ZTQ0Y2ZiOTQ1MDZmODc0YzU4YjE2MiA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjYyODk0NjcsLTc5LjM5NDQxOTldLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogImJsdWUiLAogICJmaWxsT3BhY2l0eSI6IDAuMywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfMTZlNDUzMDQ4Yzc2NGRjZDgwZjBjNjU3YTRhZTQxMWUpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfNTQ2YTIzYTA3NzBhNGY0NzkwOTViZDBhOTZiZjVhNGQgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfNGJmYmYwZjkwOGJlNGRkZGJkNzliMTkxMmRjMGI0NjcgPSAkKCc8ZGl2IGlkPSJodG1sXzRiZmJmMGY5MDhiZTRkZGRiZDc5YjE5MTJkYzBiNDY3IiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5DTiBUb3dlciwgS2luZyBhbmQgU3BhZGluYSwgUmFpbHdheSBMYW5kcywgSGFyYm91cmZyb250IFdlc3QsIEJhdGh1cnN0IFF1YXksIFNvdXRoIE5pYWdhcmEsIElzbGFuZCBhaXJwb3J0PC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF81NDZhMjNhMDc3MGE0ZjQ3OTA5NWJkMGE5NmJmNWE0ZC5zZXRDb250ZW50KGh0bWxfNGJmYmYwZjkwOGJlNGRkZGJkNzliMTkxMmRjMGI0NjcpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfOTNiODViZDJjNGU0NGNmYjk0NTA2Zjg3NGM1OGIxNjIuYmluZFBvcHVwKHBvcHVwXzU0NmEyM2EwNzcwYTRmNDc5MDk1YmQwYTk2YmY1YTRkKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyX2Q1ODRhMDc3NTRjNTRiOTBhYTJmNzFhMDRlMjlhMDg2ID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNjQ2NDM1MiwtNzkuMzc0ODQ1OTk5OTk5OTldLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogImJsdWUiLAogICJmaWxsT3BhY2l0eSI6IDAuMywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfMTZlNDUzMDQ4Yzc2NGRjZDgwZjBjNjU3YTRhZTQxMWUpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfZDJmZThhZDc2MGRmNDVmMWEwYTllYzEyYjhmZDViY2MgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfNjU0ZjMxODJhZWQ3NGY3NDkxNzVmNzIzYTlhOTBiOWUgPSAkKCc8ZGl2IGlkPSJodG1sXzY1NGYzMTgyYWVkNzRmNzQ5MTc1ZjcyM2E5YTkwYjllIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5TdG4gQSBQTyBCb3hlczwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfZDJmZThhZDc2MGRmNDVmMWEwYTllYzEyYjhmZDViY2Muc2V0Q29udGVudChodG1sXzY1NGYzMTgyYWVkNzRmNzQ5MTc1ZjcyM2E5YTkwYjllKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyX2Q1ODRhMDc3NTRjNTRiOTBhYTJmNzFhMDRlMjlhMDg2LmJpbmRQb3B1cChwb3B1cF9kMmZlOGFkNzYwZGY0NWYxYTBhOWVjMTJiOGZkNWJjYyk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl8zOGJiZGU0MmY1ZDk0MjJmYTYwY2JmMmFiMDg1YTZmOCA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjY0ODQyOTIsLTc5LjM4MjI4MDJdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogImJsdWUiLAogICJmaWxsT3BhY2l0eSI6IDAuMywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfMTZlNDUzMDQ4Yzc2NGRjZDgwZjBjNjU3YTRhZTQxMWUpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfMzZjNmQxMTE3MTNhNDkwYzkyYzE1NGE3MmRhZmQ3NDQgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfMjFiNGZkZmVjODIyNDk2YThlM2YwYzAzMTAwMzE3YzUgPSAkKCc8ZGl2IGlkPSJodG1sXzIxYjRmZGZlYzgyMjQ5NmE4ZTNmMGMwMzEwMDMxN2M1IiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5GaXJzdCBDYW5hZGlhbiBQbGFjZSwgVW5kZXJncm91bmQgY2l0eTwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfMzZjNmQxMTE3MTNhNDkwYzkyYzE1NGE3MmRhZmQ3NDQuc2V0Q29udGVudChodG1sXzIxYjRmZGZlYzgyMjQ5NmE4ZTNmMGMwMzEwMDMxN2M1KTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzM4YmJkZTQyZjVkOTQyMmZhNjBjYmYyYWIwODVhNmY4LmJpbmRQb3B1cChwb3B1cF8zNmM2ZDExMTcxM2E0OTBjOTJjMTU0YTcyZGFmZDc0NCk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl8yY2RmOTAwOWE4NjU0ZDA5OGI1YzBjZDVlNzI5NDM0YyA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjY2OTU0MiwtNzkuNDIyNTYzN10sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiYmx1ZSIsCiAgImZpbGxPcGFjaXR5IjogMC4zLAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF8xNmU0NTMwNDhjNzY0ZGNkODBmMGM2NTdhNGFlNDExZSk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF8yYmU5MmYzMmE2ODI0ZDc2YjY2MjkzNTVmODFmODA0MSA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF81YWFhMWMzZDgyOGQ0OTk2YTY1NTcwNWY3MWFjYzFiMSA9ICQoJzxkaXYgaWQ9Imh0bWxfNWFhYTFjM2Q4MjhkNDk5NmE2NTU3MDVmNzFhY2MxYjEiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkNocmlzdGllPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF8yYmU5MmYzMmE2ODI0ZDc2YjY2MjkzNTVmODFmODA0MS5zZXRDb250ZW50KGh0bWxfNWFhYTFjM2Q4MjhkNDk5NmE2NTU3MDVmNzFhY2MxYjEpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfMmNkZjkwMDlhODY1NGQwOThiNWMwY2Q1ZTcyOTQzNGMuYmluZFBvcHVwKHBvcHVwXzJiZTkyZjMyYTY4MjRkNzZiNjYyOTM1NWY4MWY4MDQxKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzQ1NmMzNjc5NzY3OTRmMzdiOTE1MGQ2MTVmYjY0NzhlID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNjY5MDA1MTAwMDAwMDEsLTc5LjQ0MjI1OTNdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogImJsdWUiLAogICJmaWxsT3BhY2l0eSI6IDAuMywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfMTZlNDUzMDQ4Yzc2NGRjZDgwZjBjNjU3YTRhZTQxMWUpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfZTVmODg4NWI3ZWZmNDI3MGFiNmM1ZDk5YmVmMTYyM2UgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfNmFkNTcwMzc1NmU2NGYzMGJlYzQ3MjZmMDI0MjAwY2YgPSAkKCc8ZGl2IGlkPSJodG1sXzZhZDU3MDM3NTZlNjRmMzBiZWM0NzI2ZjAyNDIwMGNmIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5EdWZmZXJpbiwgRG92ZXJjb3VydCBWaWxsYWdlPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF9lNWY4ODg1YjdlZmY0MjcwYWI2YzVkOTliZWYxNjIzZS5zZXRDb250ZW50KGh0bWxfNmFkNTcwMzc1NmU2NGYzMGJlYzQ3MjZmMDI0MjAwY2YpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfNDU2YzM2Nzk3Njc5NGYzN2I5MTUwZDYxNWZiNjQ3OGUuYmluZFBvcHVwKHBvcHVwX2U1Zjg4ODViN2VmZjQyNzBhYjZjNWQ5OWJlZjE2MjNlKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyX2I4MWMwNzk4MTEwMTQ5NTI4NmRmNzFhMjZkMjZhMzQ0ID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNjQ3OTI2NzAwMDAwMDA2LC03OS40MTk3NDk3XSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImJsdWUiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICJibHVlIiwKICAiZmlsbE9wYWNpdHkiOiAwLjMsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzE2ZTQ1MzA0OGM3NjRkY2Q4MGYwYzY1N2E0YWU0MTFlKTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzE1YTZmMWMyZjk0MjRjNDlhZWY4MDU5MGNmZmUzMWY5ID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzI0Y2YzZTNlY2U2ODRjNjBhMTEzOGQ1MTRkZDA4ZmY1ID0gJCgnPGRpdiBpZD0iaHRtbF8yNGNmM2UzZWNlNjg0YzYwYTExMzhkNTE0ZGQwOGZmNSIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+TGl0dGxlIFBvcnR1Z2FsLCBUcmluaXR5PC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF8xNWE2ZjFjMmY5NDI0YzQ5YWVmODA1OTBjZmZlMzFmOS5zZXRDb250ZW50KGh0bWxfMjRjZjNlM2VjZTY4NGM2MGExMTM4ZDUxNGRkMDhmZjUpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfYjgxYzA3OTgxMTAxNDk1Mjg2ZGY3MWEyNmQyNmEzNDQuYmluZFBvcHVwKHBvcHVwXzE1YTZmMWMyZjk0MjRjNDlhZWY4MDU5MGNmZmUzMWY5KTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzlhYzE1NGRiNzA2NzQ1MWU5NjdmNjY3OGQ5OWEwMjNkID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNjM2ODQ3MiwtNzkuNDI4MTkxNDAwMDAwMDJdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogImJsdWUiLAogICJmaWxsT3BhY2l0eSI6IDAuMywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfMTZlNDUzMDQ4Yzc2NGRjZDgwZjBjNjU3YTRhZTQxMWUpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfMDdlOWIyZGYzNjk2NGI4Y2IzYTI0MDZhZjJlNzY5MmIgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfYTE0NDY4ZTE0Zjg3NDY5Yzk5NGQ2MGVjMjdiNjBhYzkgPSAkKCc8ZGl2IGlkPSJodG1sX2ExNDQ2OGUxNGY4NzQ2OWM5OTRkNjBlYzI3YjYwYWM5IiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5Ccm9ja3RvbiwgUGFya2RhbGUgVmlsbGFnZSwgRXhoaWJpdGlvbiBQbGFjZTwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfMDdlOWIyZGYzNjk2NGI4Y2IzYTI0MDZhZjJlNzY5MmIuc2V0Q29udGVudChodG1sX2ExNDQ2OGUxNGY4NzQ2OWM5OTRkNjBlYzI3YjYwYWM5KTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzlhYzE1NGRiNzA2NzQ1MWU5NjdmNjY3OGQ5OWEwMjNkLmJpbmRQb3B1cChwb3B1cF8wN2U5YjJkZjM2OTY0YjhjYjNhMjQwNmFmMmU3NjkyYik7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl8wYTg5ODNjYTRhOTA0MWY5OTliMWM2YWIzMTYyN2IwNSA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjY2MTYwODMsLTc5LjQ2NDc2MzI5OTk5OTk5XSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogImJsdWUiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICJibHVlIiwKICAiZmlsbE9wYWNpdHkiOiAwLjMsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzE2ZTQ1MzA0OGM3NjRkY2Q4MGYwYzY1N2E0YWU0MTFlKTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzJhNzc3YmE2NWM0NjRlZjViN2JjYTQxYjY2NjQ2MzNjID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzYwNGRkN2Q2NmYzYjQ4MDViYTRmNzU2M2QzNDllOThjID0gJCgnPGRpdiBpZD0iaHRtbF82MDRkZDdkNjZmM2I0ODA1YmE0Zjc1NjNkMzQ5ZTk4YyIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+SGlnaCBQYXJrLCBUaGUgSnVuY3Rpb24gU291dGg8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzJhNzc3YmE2NWM0NjRlZjViN2JjYTQxYjY2NjQ2MzNjLnNldENvbnRlbnQoaHRtbF82MDRkZDdkNjZmM2I0ODA1YmE0Zjc1NjNkMzQ5ZTk4Yyk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl8wYTg5ODNjYTRhOTA0MWY5OTliMWM2YWIzMTYyN2IwNS5iaW5kUG9wdXAocG9wdXBfMmE3NzdiYTY1YzQ2NGVmNWI3YmNhNDFiNjY2NDYzM2MpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfNzMzNGZjZDkxMDE2NDExYmE4NTM0M2QyMzAzNDE4YWMgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42NDg5NTk3LC03OS40NTYzMjVdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogImJsdWUiLAogICJmaWxsT3BhY2l0eSI6IDAuMywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfMTZlNDUzMDQ4Yzc2NGRjZDgwZjBjNjU3YTRhZTQxMWUpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfYTcxOTE4OGZkMzkzNDJhMGI3MzBhYmU0Y2M0ZDY1ZTMgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfZGM1MTQ2NDcyZWRhNDZlNDg0YTg4Njc3MzA3NzQ3YmQgPSAkKCc8ZGl2IGlkPSJodG1sX2RjNTE0NjQ3MmVkYTQ2ZTQ4NGE4ODY3NzMwNzc0N2JkIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5QYXJrZGFsZSwgUm9uY2VzdmFsbGVzPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF9hNzE5MTg4ZmQzOTM0MmEwYjczMGFiZTRjYzRkNjVlMy5zZXRDb250ZW50KGh0bWxfZGM1MTQ2NDcyZWRhNDZlNDg0YTg4Njc3MzA3NzQ3YmQpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfNzMzNGZjZDkxMDE2NDExYmE4NTM0M2QyMzAzNDE4YWMuYmluZFBvcHVwKHBvcHVwX2E3MTkxODhmZDM5MzQyYTBiNzMwYWJlNGNjNGQ2NWUzKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzFlM2UxZGI5ODQyODQ0MTM5MmI2MGJhOTQyNTE3YzI5ID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNjUxNTcwNiwtNzkuNDg0NDQ5OV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiYmx1ZSIsCiAgImZpbGxPcGFjaXR5IjogMC4zLAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF8xNmU0NTMwNDhjNzY0ZGNkODBmMGM2NTdhNGFlNDExZSk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF80NWZhYWM0YWVkMDM0MWVhYTU1NDNkOTkyMzU5ZDBjNSA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF81ZWY5ODQ4ZDAwNjI0N2FhOGUyMTM2NWI0YmIxYmEzMCA9ICQoJzxkaXYgaWQ9Imh0bWxfNWVmOTg0OGQwMDYyNDdhYThlMjEzNjViNGJiMWJhMzAiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPlJ1bm55bWVkZSwgU3dhbnNlYTwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfNDVmYWFjNGFlZDAzNDFlYWE1NTQzZDk5MjM1OWQwYzUuc2V0Q29udGVudChodG1sXzVlZjk4NDhkMDA2MjQ3YWE4ZTIxMzY1YjRiYjFiYTMwKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzFlM2UxZGI5ODQyODQ0MTM5MmI2MGJhOTQyNTE3YzI5LmJpbmRQb3B1cChwb3B1cF80NWZhYWM0YWVkMDM0MWVhYTU1NDNkOTkyMzU5ZDBjNSk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl82MmYxYzNjNDhkNDE0NmI3Yjc5MDZlMzg2NDNjZjgzOCA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjY2MjMwMTUsLTc5LjM4OTQ5MzhdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiYmx1ZSIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogImJsdWUiLAogICJmaWxsT3BhY2l0eSI6IDAuMywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfMTZlNDUzMDQ4Yzc2NGRjZDgwZjBjNjU3YTRhZTQxMWUpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfNWUzY2Q2YmFhMGU1NGFjNjlhY2FhMjIzZjE0Y2RmMTggPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfZDFjMTk4NWRlOWMzNDllZTk2NDU0ZjgzNWNkMjA4MTAgPSAkKCc8ZGl2IGlkPSJodG1sX2QxYzE5ODVkZTljMzQ5ZWU5NjQ1NGY4MzVjZDIwODEwIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5RdWVlbiYjMzk7cyBQYXJrLCBPbnRhcmlvIFByb3ZpbmNpYWwgR292ZXJubWVudDwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfNWUzY2Q2YmFhMGU1NGFjNjlhY2FhMjIzZjE0Y2RmMTguc2V0Q29udGVudChodG1sX2QxYzE5ODVkZTljMzQ5ZWU5NjQ1NGY4MzVjZDIwODEwKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzYyZjFjM2M0OGQ0MTQ2YjdiNzkwNmUzODY0M2NmODM4LmJpbmRQb3B1cChwb3B1cF81ZTNjZDZiYWEwZTU0YWM2OWFjYWEyMjNmMTRjZGYxOCk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl9iZTM3MmVhNjY0M2I0NjcyOWY0OTk0ZDdiYjRhZDdlYiA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjY2Mjc0MzksLTc5LjMyMTU1OF0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICJibHVlIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiYmx1ZSIsCiAgImZpbGxPcGFjaXR5IjogMC4zLAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF8xNmU0NTMwNDhjNzY0ZGNkODBmMGM2NTdhNGFlNDExZSk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF8xOWQyMmNkZDc1MzM0YTM4OTc3M2ExNzAxNzdmMzBkMyA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF9lZDRhZDcwZDBmZDA0MGQ2YTc2Zjc0NDRjOGEwMzA3OCA9ICQoJzxkaXYgaWQ9Imh0bWxfZWQ0YWQ3MGQwZmQwNDBkNmE3NmY3NDQ0YzhhMDMwNzgiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkJ1c2luZXNzIHJlcGx5IG1haWwgUHJvY2Vzc2luZyBDZW50cmUsIFNvdXRoIENlbnRyYWwgTGV0dGVyIFByb2Nlc3NpbmcgUGxhbnQgVG9yb250bzwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfMTlkMjJjZGQ3NTMzNGEzODk3NzNhMTcwMTc3ZjMwZDMuc2V0Q29udGVudChodG1sX2VkNGFkNzBkMGZkMDQwZDZhNzZmNzQ0NGM4YTAzMDc4KTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyX2JlMzcyZWE2NjQzYjQ2NzI5ZjQ5OTRkN2JiNGFkN2ViLmJpbmRQb3B1cChwb3B1cF8xOWQyMmNkZDc1MzM0YTM4OTc3M2ExNzAxNzdmMzBkMyk7CgogICAgICAgICAgICAKICAgICAgICAKPC9zY3JpcHQ+ onload="this.contentDocument.open();this.contentDocument.write(atob(this.getAttribute('data-html')));this.contentDocument.close();" allowfullscreen webkitallowfullscreen mozallowfullscreen></iframe></div></div>



__Prepare Foursquare credentials__


```python
CLIENT_ID = 'YPQO0R5GDUG1VMEFVS2DECJC12PN0MG5S4LIGHFKWW0ZX1KY' # Foursquare ID
CLIENT_SECRET = 'YYWVUBTIE0UXQW5AF25EWAL0L3XX2QZDTRJ1CTRG4TPD5OT1' # Foursquare Secret
VERSION = '20180605' # Foursquare API version

```

__Function to repeat same analysis done for Manhattan__


```python
limit = 100 # limit of number of venues returned by Foursquare API
radius = 500 # define radius
```


```python
def getNearbyVenues(names, latitudes, longitudes, radius=500):
    
    venues_list=[]
    for name, lat, lng in zip(names, latitudes, longitudes):
        print(name)
            
        # create the API request URL
        url = 'https://api.foursquare.com/v2/venues/explore?&client_id={}&client_secret={}&v={}&ll={},{}&radius={}&limit={}'.format(
            CLIENT_ID, 
            CLIENT_SECRET, 
            VERSION, 
            lat, 
            lng, 
            radius, 
            limit)
            
        # make the GET request
        results = requests.get(url).json()["response"]['groups'][0]['items']
        
        # return only relevant information for each nearby venue
        venues_list.append([(
            name, 
            lat, 
            lng, 
            v['venue']['name'], 
            v['venue']['location']['lat'], 
            v['venue']['location']['lng'],  
            v['venue']['categories'][0]['name']) for v in results])

    nearby_venues = pd.DataFrame([item for venue_list in venues_list for item in venue_list])
    nearby_venues.columns = ['Neighborhood', 
                  'Neighborhood Latitude', 
                  'Neighborhood Longitude', 
                  'Venue', 
                  'Venue Latitude', 
                  'Venue Longitude', 
                  'Venue Category']
    
    return(nearby_venues)
```

__Code for running the function to get venues and put them in a dataframe__


```python
Toronto_venues = getNearbyVenues(names=Toronto_filter['Neighbourhood'],latitudes=Toronto_filter['Latitude'],longitudes=Toronto_filter['Longitude'])
```

    The Beaches
    The Danforth West, Riverdale
    India Bazaar, The Beaches West
    Studio District
    Lawrence Park
    Davisville North
    North Toronto West,  Lawrence Park
    Davisville
    Moore Park, Summerhill East
    Summerhill West, Rathnelly, South Hill, Forest Hill SE, Deer Park
    Rosedale
    St. James Town, Cabbagetown
    Church and Wellesley
    Regent Park, Harbourfront
    Garden District, Ryerson
    St. James Town
    Berczy Park
    Central Bay Street
    Richmond, Adelaide, King
    Harbourfront East, Union Station, Toronto Islands
    Toronto Dominion Centre, Design Exchange
    Commerce Court, Victoria Hotel
    Roselawn
    Forest Hill North & West, Forest Hill Road Park
    The Annex, North Midtown, Yorkville
    University of Toronto, Harbord
    Kensington Market, Chinatown, Grange Park
    CN Tower, King and Spadina, Railway Lands, Harbourfront West, Bathurst Quay, South Niagara, Island airport
    Stn A PO Boxes
    First Canadian Place, Underground city
    Christie
    Dufferin, Dovercourt Village
    Little Portugal, Trinity
    Brockton, Parkdale Village, Exhibition Place
    High Park, The Junction South
    Parkdale, Roncesvalles
    Runnymede, Swansea
    Queen's Park, Ontario Provincial Government
    Business reply mail Processing Centre, South Central Letter Processing Plant Toronto



```python
print(Toronto_venues.shape)
Toronto_venues.head(10)
```

    (1613, 7)





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
      <th>Neighborhood</th>
      <th>Neighborhood Latitude</th>
      <th>Neighborhood Longitude</th>
      <th>Venue</th>
      <th>Venue Latitude</th>
      <th>Venue Longitude</th>
      <th>Venue Category</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>The Beaches</td>
      <td>43.676357</td>
      <td>-79.293031</td>
      <td>Glen Manor Ravine</td>
      <td>43.676821</td>
      <td>-79.293942</td>
      <td>Trail</td>
    </tr>
    <tr>
      <th>1</th>
      <td>The Beaches</td>
      <td>43.676357</td>
      <td>-79.293031</td>
      <td>The Big Carrot Natural Food Market</td>
      <td>43.678879</td>
      <td>-79.297734</td>
      <td>Health Food Store</td>
    </tr>
    <tr>
      <th>2</th>
      <td>The Beaches</td>
      <td>43.676357</td>
      <td>-79.293031</td>
      <td>Grover Pub and Grub</td>
      <td>43.679181</td>
      <td>-79.297215</td>
      <td>Pub</td>
    </tr>
    <tr>
      <th>3</th>
      <td>The Beaches</td>
      <td>43.676357</td>
      <td>-79.293031</td>
      <td>Upper Beaches</td>
      <td>43.680563</td>
      <td>-79.292869</td>
      <td>Neighborhood</td>
    </tr>
    <tr>
      <th>4</th>
      <td>The Beaches</td>
      <td>43.676357</td>
      <td>-79.293031</td>
      <td>Dip 'n Sip</td>
      <td>43.678897</td>
      <td>-79.297745</td>
      <td>Coffee Shop</td>
    </tr>
    <tr>
      <th>5</th>
      <td>The Danforth West, Riverdale</td>
      <td>43.679557</td>
      <td>-79.352188</td>
      <td>MenEssentials</td>
      <td>43.677820</td>
      <td>-79.351265</td>
      <td>Cosmetics Shop</td>
    </tr>
    <tr>
      <th>6</th>
      <td>The Danforth West, Riverdale</td>
      <td>43.679557</td>
      <td>-79.352188</td>
      <td>Pantheon</td>
      <td>43.677621</td>
      <td>-79.351434</td>
      <td>Greek Restaurant</td>
    </tr>
    <tr>
      <th>7</th>
      <td>The Danforth West, Riverdale</td>
      <td>43.679557</td>
      <td>-79.352188</td>
      <td>Cafe Fiorentina</td>
      <td>43.677743</td>
      <td>-79.350115</td>
      <td>Italian Restaurant</td>
    </tr>
    <tr>
      <th>8</th>
      <td>The Danforth West, Riverdale</td>
      <td>43.679557</td>
      <td>-79.352188</td>
      <td>Dolce Gelato</td>
      <td>43.677773</td>
      <td>-79.351187</td>
      <td>Ice Cream Shop</td>
    </tr>
    <tr>
      <th>9</th>
      <td>The Danforth West, Riverdale</td>
      <td>43.679557</td>
      <td>-79.352188</td>
      <td>La Diperie</td>
      <td>43.677530</td>
      <td>-79.352295</td>
      <td>Ice Cream Shop</td>
    </tr>
  </tbody>
</table>
</div>



__Summary of venues for each Neighborhood__


```python
Toronto_venues.groupby('Neighborhood').count()
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
      <th>Neighborhood Latitude</th>
      <th>Neighborhood Longitude</th>
      <th>Venue</th>
      <th>Venue Latitude</th>
      <th>Venue Longitude</th>
      <th>Venue Category</th>
    </tr>
    <tr>
      <th>Neighborhood</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Berczy Park</th>
      <td>56</td>
      <td>56</td>
      <td>56</td>
      <td>56</td>
      <td>56</td>
      <td>56</td>
    </tr>
    <tr>
      <th>Brockton, Parkdale Village, Exhibition Place</th>
      <td>22</td>
      <td>22</td>
      <td>22</td>
      <td>22</td>
      <td>22</td>
      <td>22</td>
    </tr>
    <tr>
      <th>Business reply mail Processing Centre, South Central Letter Processing Plant Toronto</th>
      <td>18</td>
      <td>18</td>
      <td>18</td>
      <td>18</td>
      <td>18</td>
      <td>18</td>
    </tr>
    <tr>
      <th>CN Tower, King and Spadina, Railway Lands, Harbourfront West, Bathurst Quay, South Niagara, Island airport</th>
      <td>15</td>
      <td>15</td>
      <td>15</td>
      <td>15</td>
      <td>15</td>
      <td>15</td>
    </tr>
    <tr>
      <th>Central Bay Street</th>
      <td>63</td>
      <td>63</td>
      <td>63</td>
      <td>63</td>
      <td>63</td>
      <td>63</td>
    </tr>
    <tr>
      <th>Christie</th>
      <td>16</td>
      <td>16</td>
      <td>16</td>
      <td>16</td>
      <td>16</td>
      <td>16</td>
    </tr>
    <tr>
      <th>Church and Wellesley</th>
      <td>79</td>
      <td>79</td>
      <td>79</td>
      <td>79</td>
      <td>79</td>
      <td>79</td>
    </tr>
    <tr>
      <th>Commerce Court, Victoria Hotel</th>
      <td>100</td>
      <td>100</td>
      <td>100</td>
      <td>100</td>
      <td>100</td>
      <td>100</td>
    </tr>
    <tr>
      <th>Davisville</th>
      <td>36</td>
      <td>36</td>
      <td>36</td>
      <td>36</td>
      <td>36</td>
      <td>36</td>
    </tr>
    <tr>
      <th>Davisville North</th>
      <td>9</td>
      <td>9</td>
      <td>9</td>
      <td>9</td>
      <td>9</td>
      <td>9</td>
    </tr>
    <tr>
      <th>Dufferin, Dovercourt Village</th>
      <td>14</td>
      <td>14</td>
      <td>14</td>
      <td>14</td>
      <td>14</td>
      <td>14</td>
    </tr>
    <tr>
      <th>First Canadian Place, Underground city</th>
      <td>100</td>
      <td>100</td>
      <td>100</td>
      <td>100</td>
      <td>100</td>
      <td>100</td>
    </tr>
    <tr>
      <th>Forest Hill North &amp; West, Forest Hill Road Park</th>
      <td>4</td>
      <td>4</td>
      <td>4</td>
      <td>4</td>
      <td>4</td>
      <td>4</td>
    </tr>
    <tr>
      <th>Garden District, Ryerson</th>
      <td>100</td>
      <td>100</td>
      <td>100</td>
      <td>100</td>
      <td>100</td>
      <td>100</td>
    </tr>
    <tr>
      <th>Harbourfront East, Union Station, Toronto Islands</th>
      <td>100</td>
      <td>100</td>
      <td>100</td>
      <td>100</td>
      <td>100</td>
      <td>100</td>
    </tr>
    <tr>
      <th>High Park, The Junction South</th>
      <td>23</td>
      <td>23</td>
      <td>23</td>
      <td>23</td>
      <td>23</td>
      <td>23</td>
    </tr>
    <tr>
      <th>India Bazaar, The Beaches West</th>
      <td>22</td>
      <td>22</td>
      <td>22</td>
      <td>22</td>
      <td>22</td>
      <td>22</td>
    </tr>
    <tr>
      <th>Kensington Market, Chinatown, Grange Park</th>
      <td>58</td>
      <td>58</td>
      <td>58</td>
      <td>58</td>
      <td>58</td>
      <td>58</td>
    </tr>
    <tr>
      <th>Lawrence Park</th>
      <td>3</td>
      <td>3</td>
      <td>3</td>
      <td>3</td>
      <td>3</td>
      <td>3</td>
    </tr>
    <tr>
      <th>Little Portugal, Trinity</th>
      <td>44</td>
      <td>44</td>
      <td>44</td>
      <td>44</td>
      <td>44</td>
      <td>44</td>
    </tr>
    <tr>
      <th>Moore Park, Summerhill East</th>
      <td>3</td>
      <td>3</td>
      <td>3</td>
      <td>3</td>
      <td>3</td>
      <td>3</td>
    </tr>
    <tr>
      <th>North Toronto West,  Lawrence Park</th>
      <td>21</td>
      <td>21</td>
      <td>21</td>
      <td>21</td>
      <td>21</td>
      <td>21</td>
    </tr>
    <tr>
      <th>Parkdale, Roncesvalles</th>
      <td>14</td>
      <td>14</td>
      <td>14</td>
      <td>14</td>
      <td>14</td>
      <td>14</td>
    </tr>
    <tr>
      <th>Queen's Park, Ontario Provincial Government</th>
      <td>35</td>
      <td>35</td>
      <td>35</td>
      <td>35</td>
      <td>35</td>
      <td>35</td>
    </tr>
    <tr>
      <th>Regent Park, Harbourfront</th>
      <td>45</td>
      <td>45</td>
      <td>45</td>
      <td>45</td>
      <td>45</td>
      <td>45</td>
    </tr>
    <tr>
      <th>Richmond, Adelaide, King</th>
      <td>93</td>
      <td>93</td>
      <td>93</td>
      <td>93</td>
      <td>93</td>
      <td>93</td>
    </tr>
    <tr>
      <th>Rosedale</th>
      <td>4</td>
      <td>4</td>
      <td>4</td>
      <td>4</td>
      <td>4</td>
      <td>4</td>
    </tr>
    <tr>
      <th>Roselawn</th>
      <td>3</td>
      <td>3</td>
      <td>3</td>
      <td>3</td>
      <td>3</td>
      <td>3</td>
    </tr>
    <tr>
      <th>Runnymede, Swansea</th>
      <td>38</td>
      <td>38</td>
      <td>38</td>
      <td>38</td>
      <td>38</td>
      <td>38</td>
    </tr>
    <tr>
      <th>St. James Town</th>
      <td>79</td>
      <td>79</td>
      <td>79</td>
      <td>79</td>
      <td>79</td>
      <td>79</td>
    </tr>
    <tr>
      <th>St. James Town, Cabbagetown</th>
      <td>44</td>
      <td>44</td>
      <td>44</td>
      <td>44</td>
      <td>44</td>
      <td>44</td>
    </tr>
    <tr>
      <th>Stn A PO Boxes</th>
      <td>93</td>
      <td>93</td>
      <td>93</td>
      <td>93</td>
      <td>93</td>
      <td>93</td>
    </tr>
    <tr>
      <th>Studio District</th>
      <td>40</td>
      <td>40</td>
      <td>40</td>
      <td>40</td>
      <td>40</td>
      <td>40</td>
    </tr>
    <tr>
      <th>Summerhill West, Rathnelly, South Hill, Forest Hill SE, Deer Park</th>
      <td>16</td>
      <td>16</td>
      <td>16</td>
      <td>16</td>
      <td>16</td>
      <td>16</td>
    </tr>
    <tr>
      <th>The Annex, North Midtown, Yorkville</th>
      <td>21</td>
      <td>21</td>
      <td>21</td>
      <td>21</td>
      <td>21</td>
      <td>21</td>
    </tr>
    <tr>
      <th>The Beaches</th>
      <td>5</td>
      <td>5</td>
      <td>5</td>
      <td>5</td>
      <td>5</td>
      <td>5</td>
    </tr>
    <tr>
      <th>The Danforth West, Riverdale</th>
      <td>42</td>
      <td>42</td>
      <td>42</td>
      <td>42</td>
      <td>42</td>
      <td>42</td>
    </tr>
    <tr>
      <th>Toronto Dominion Centre, Design Exchange</th>
      <td>100</td>
      <td>100</td>
      <td>100</td>
      <td>100</td>
      <td>100</td>
      <td>100</td>
    </tr>
    <tr>
      <th>University of Toronto, Harbord</th>
      <td>35</td>
      <td>35</td>
      <td>35</td>
      <td>35</td>
      <td>35</td>
      <td>35</td>
    </tr>
  </tbody>
</table>
</div>



__Check how many Venue Categories__


```python
print('There are ', len(Toronto_venues['Venue Category'].unique()), ' unique Venue Categories')
```

    There are  239  unique Venue Categories


__For Neighbour Analysis, we start with a table that pivots individual venues per neighbourhood__


```python
# Create table with Venues
Toronto_venuexNBRH = pd.get_dummies(Toronto_venues[['Venue Category']], prefix="", prefix_sep="")

# add neighborhood column
Toronto_venuexNBRH['Neighborhood'] = Toronto_venues['Neighborhood'] 

# move neighborhood column to the first column
cols=list(Toronto_venuexNBRH.columns.values)
cols.pop(cols.index('Neighborhood'))
Toronto_venuexNBRH=Toronto_venuexNBRH[['Neighborhood']+cols]

print(Toronto_venuexNBRH.shape)
Toronto_venuexNBRH.head(10)
```

    (1613, 239)





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
      <th>Neighborhood</th>
      <th>Afghan Restaurant</th>
      <th>Airport</th>
      <th>Airport Food Court</th>
      <th>Airport Gate</th>
      <th>Airport Lounge</th>
      <th>Airport Service</th>
      <th>Airport Terminal</th>
      <th>American Restaurant</th>
      <th>Antique Shop</th>
      <th>...</th>
      <th>Trail</th>
      <th>Train Station</th>
      <th>Vegetarian / Vegan Restaurant</th>
      <th>Video Game Store</th>
      <th>Video Store</th>
      <th>Vietnamese Restaurant</th>
      <th>Wine Bar</th>
      <th>Wings Joint</th>
      <th>Women's Store</th>
      <th>Yoga Studio</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>The Beaches</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>...</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>The Beaches</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
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
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>The Beaches</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
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
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>The Beaches</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
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
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>The Beaches</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
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
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>5</th>
      <td>The Danforth West, Riverdale</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
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
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>6</th>
      <td>The Danforth West, Riverdale</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
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
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>7</th>
      <td>The Danforth West, Riverdale</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
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
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>8</th>
      <td>The Danforth West, Riverdale</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
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
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>9</th>
      <td>The Danforth West, Riverdale</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
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
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
  </tbody>
</table>
<p>10 rows × 239 columns</p>
</div>



__Group rows by neighborhood and by taking the mean of the frequency of occurrence of each category__


```python
Toronto_groupBy = Toronto_venuexNBRH.groupby('Neighborhood').mean().reset_index()
Toronto_groupBy
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
      <th>Neighborhood</th>
      <th>Afghan Restaurant</th>
      <th>Airport</th>
      <th>Airport Food Court</th>
      <th>Airport Gate</th>
      <th>Airport Lounge</th>
      <th>Airport Service</th>
      <th>Airport Terminal</th>
      <th>American Restaurant</th>
      <th>Antique Shop</th>
      <th>...</th>
      <th>Trail</th>
      <th>Train Station</th>
      <th>Vegetarian / Vegan Restaurant</th>
      <th>Video Game Store</th>
      <th>Video Store</th>
      <th>Vietnamese Restaurant</th>
      <th>Wine Bar</th>
      <th>Wings Joint</th>
      <th>Women's Store</th>
      <th>Yoga Studio</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Berczy Park</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>...</td>
      <td>0.000000</td>
      <td>0.00</td>
      <td>0.017857</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Brockton, Parkdale Village, Exhibition Place</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>...</td>
      <td>0.000000</td>
      <td>0.00</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Business reply mail Processing Centre, South C...</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>...</td>
      <td>0.000000</td>
      <td>0.00</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>3</th>
      <td>CN Tower, King and Spadina, Railway Lands, Har...</td>
      <td>0.000000</td>
      <td>0.066667</td>
      <td>0.066667</td>
      <td>0.066667</td>
      <td>0.066667</td>
      <td>0.133333</td>
      <td>0.066667</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>...</td>
      <td>0.000000</td>
      <td>0.00</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Central Bay Street</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>...</td>
      <td>0.000000</td>
      <td>0.00</td>
      <td>0.015873</td>
      <td>0.000000</td>
      <td>0.015873</td>
      <td>0.000000</td>
      <td>0.015873</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.015873</td>
    </tr>
    <tr>
      <th>5</th>
      <td>Christie</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>...</td>
      <td>0.000000</td>
      <td>0.00</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>6</th>
      <td>Church and Wellesley</td>
      <td>0.012658</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.012658</td>
      <td>0.000000</td>
      <td>...</td>
      <td>0.000000</td>
      <td>0.00</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.012658</td>
      <td>0.000000</td>
      <td>0.025316</td>
    </tr>
    <tr>
      <th>7</th>
      <td>Commerce Court, Victoria Hotel</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.040000</td>
      <td>0.000000</td>
      <td>...</td>
      <td>0.000000</td>
      <td>0.00</td>
      <td>0.020000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.010000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>8</th>
      <td>Davisville</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>...</td>
      <td>0.000000</td>
      <td>0.00</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>9</th>
      <td>Davisville North</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>...</td>
      <td>0.000000</td>
      <td>0.00</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>10</th>
      <td>Dufferin, Dovercourt Village</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>...</td>
      <td>0.000000</td>
      <td>0.00</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>11</th>
      <td>First Canadian Place, Underground city</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.030000</td>
      <td>0.000000</td>
      <td>...</td>
      <td>0.000000</td>
      <td>0.01</td>
      <td>0.010000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.010000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>12</th>
      <td>Forest Hill North &amp; West, Forest Hill Road Park</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>...</td>
      <td>0.250000</td>
      <td>0.00</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>13</th>
      <td>Garden District, Ryerson</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>...</td>
      <td>0.000000</td>
      <td>0.00</td>
      <td>0.000000</td>
      <td>0.010000</td>
      <td>0.000000</td>
      <td>0.010000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>14</th>
      <td>Harbourfront East, Union Station, Toronto Islands</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>...</td>
      <td>0.000000</td>
      <td>0.01</td>
      <td>0.010000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.010000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>15</th>
      <td>High Park, The Junction South</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>...</td>
      <td>0.000000</td>
      <td>0.00</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>16</th>
      <td>India Bazaar, The Beaches West</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>...</td>
      <td>0.000000</td>
      <td>0.00</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>17</th>
      <td>Kensington Market, Chinatown, Grange Park</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>...</td>
      <td>0.000000</td>
      <td>0.00</td>
      <td>0.034483</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.051724</td>
      <td>0.017241</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>18</th>
      <td>Lawrence Park</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>...</td>
      <td>0.000000</td>
      <td>0.00</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>19</th>
      <td>Little Portugal, Trinity</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>...</td>
      <td>0.000000</td>
      <td>0.00</td>
      <td>0.045455</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.022727</td>
      <td>0.022727</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.022727</td>
    </tr>
    <tr>
      <th>20</th>
      <td>Moore Park, Summerhill East</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>...</td>
      <td>0.333333</td>
      <td>0.00</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>21</th>
      <td>North Toronto West,  Lawrence Park</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>...</td>
      <td>0.000000</td>
      <td>0.00</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.047619</td>
    </tr>
    <tr>
      <th>22</th>
      <td>Parkdale, Roncesvalles</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>...</td>
      <td>0.000000</td>
      <td>0.00</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>23</th>
      <td>Queen's Park, Ontario Provincial Government</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>...</td>
      <td>0.000000</td>
      <td>0.00</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.028571</td>
      <td>0.000000</td>
      <td>0.028571</td>
    </tr>
    <tr>
      <th>24</th>
      <td>Regent Park, Harbourfront</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.022222</td>
      <td>...</td>
      <td>0.000000</td>
      <td>0.00</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.022222</td>
    </tr>
    <tr>
      <th>25</th>
      <td>Richmond, Adelaide, King</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.021505</td>
      <td>0.000000</td>
      <td>...</td>
      <td>0.000000</td>
      <td>0.00</td>
      <td>0.010753</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.010753</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>26</th>
      <td>Rosedale</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>...</td>
      <td>0.250000</td>
      <td>0.00</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>27</th>
      <td>Roselawn</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>...</td>
      <td>0.000000</td>
      <td>0.00</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>28</th>
      <td>Runnymede, Swansea</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>...</td>
      <td>0.000000</td>
      <td>0.00</td>
      <td>0.026316</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.026316</td>
    </tr>
    <tr>
      <th>29</th>
      <td>St. James Town</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.037975</td>
      <td>0.000000</td>
      <td>...</td>
      <td>0.000000</td>
      <td>0.00</td>
      <td>0.012658</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.012658</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>30</th>
      <td>St. James Town, Cabbagetown</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>...</td>
      <td>0.000000</td>
      <td>0.00</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>31</th>
      <td>Stn A PO Boxes</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.010753</td>
      <td>0.010753</td>
      <td>...</td>
      <td>0.000000</td>
      <td>0.00</td>
      <td>0.010753</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.010753</td>
    </tr>
    <tr>
      <th>32</th>
      <td>Studio District</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.050000</td>
      <td>0.000000</td>
      <td>...</td>
      <td>0.000000</td>
      <td>0.00</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.025000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.025000</td>
    </tr>
    <tr>
      <th>33</th>
      <td>Summerhill West, Rathnelly, South Hill, Forest...</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.062500</td>
      <td>0.000000</td>
      <td>...</td>
      <td>0.000000</td>
      <td>0.00</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.062500</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>34</th>
      <td>The Annex, North Midtown, Yorkville</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>...</td>
      <td>0.000000</td>
      <td>0.00</td>
      <td>0.047619</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>35</th>
      <td>The Beaches</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>...</td>
      <td>0.200000</td>
      <td>0.00</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>36</th>
      <td>The Danforth West, Riverdale</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.023810</td>
      <td>0.000000</td>
      <td>...</td>
      <td>0.023810</td>
      <td>0.00</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.023810</td>
    </tr>
    <tr>
      <th>37</th>
      <td>Toronto Dominion Centre, Design Exchange</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.030000</td>
      <td>0.000000</td>
      <td>...</td>
      <td>0.000000</td>
      <td>0.01</td>
      <td>0.010000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.010000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>38</th>
      <td>University of Toronto, Harbord</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>...</td>
      <td>0.000000</td>
      <td>0.00</td>
      <td>0.000000</td>
      <td>0.028571</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.028571</td>
    </tr>
  </tbody>
</table>
<p>39 rows × 239 columns</p>
</div>




```python
Toronto_groupBy.shape
```




    (39, 239)



__Top 5 most common venues per neighborhood__


```python
#Toronto_venuexNBRH

top_venues = 5

for hood in Toronto_groupBy['Neighborhood']:
    print("----"+hood+"----")
    temp = Toronto_groupBy[Toronto_groupBy['Neighborhood'] == hood].T.reset_index()
    temp.columns = ['venue','freq']
    temp = temp.iloc[1:]
    temp['freq'] = temp['freq'].astype(float)
    temp = temp.round({'freq': 2})
    print(temp.sort_values('freq', ascending=False).reset_index(drop=True).head(top_venues))
    print('\n')
```

    ----Berczy Park----
                    venue  freq
    0         Coffee Shop  0.07
    1        Cocktail Bar  0.05
    2         Cheese Shop  0.04
    3  Seafood Restaurant  0.04
    4                 Pub  0.04
    
    
    ----Brockton, Parkdale Village, Exhibition Place----
                venue  freq
    0            Café  0.14
    1  Breakfast Spot  0.09
    2     Coffee Shop  0.09
    3   Burrito Place  0.05
    4    Intersection  0.05
    
    
    ----Business reply mail Processing Centre, South Central Letter Processing Plant Toronto----
                    venue  freq
    0  Light Rail Station  0.11
    1         Pizza Place  0.06
    2             Butcher  0.06
    3          Restaurant  0.06
    4       Auto Workshop  0.06
    
    
    ----CN Tower, King and Spadina, Railway Lands, Harbourfront West, Bathurst Quay, South Niagara, Island airport----
                 venue  freq
    0  Airport Service  0.13
    1            Plane  0.07
    2          Airport  0.07
    3      Coffee Shop  0.07
    4              Bar  0.07
    
    
    ----Central Bay Street----
                    venue  freq
    0         Coffee Shop  0.17
    1  Italian Restaurant  0.06
    2                Café  0.06
    3      Sandwich Place  0.05
    4     Bubble Tea Shop  0.03
    
    
    ----Christie----
               venue  freq
    0  Grocery Store  0.25
    1           Café  0.19
    2           Park  0.12
    3     Baby Store  0.06
    4     Restaurant  0.06
    
    
    ----Church and Wellesley----
                     venue  freq
    0          Coffee Shop  0.08
    1  Japanese Restaurant  0.06
    2     Sushi Restaurant  0.06
    3           Restaurant  0.04
    4              Gay Bar  0.04
    
    
    ----Commerce Court, Victoria Hotel----
             venue  freq
    0  Coffee Shop  0.11
    1         Café  0.07
    2   Restaurant  0.07
    3        Hotel  0.05
    4          Gym  0.04
    
    
    ----Davisville----
                venue  freq
    0     Pizza Place  0.14
    1  Sandwich Place  0.08
    2    Dessert Shop  0.08
    3            Café  0.06
    4     Coffee Shop  0.06
    
    
    ----Davisville North----
                  venue  freq
    0    Breakfast Spot  0.11
    1              Park  0.11
    2  Department Store  0.11
    3    Sandwich Place  0.11
    4             Hotel  0.11
    
    
    ----Dufferin, Dovercourt Village----
                           venue  freq
    0                   Pharmacy  0.14
    1                     Bakery  0.14
    2                       Pool  0.07
    3  Middle Eastern Restaurant  0.07
    4                       Bank  0.07
    
    
    ----First Canadian Place, Underground city----
             venue  freq
    0  Coffee Shop  0.10
    1         Café  0.08
    2        Hotel  0.04
    3   Restaurant  0.04
    4          Gym  0.04
    
    
    ----Forest Hill North & West, Forest Hill Road Park----
                    venue  freq
    0  Mexican Restaurant  0.25
    1       Jewelry Store  0.25
    2    Sushi Restaurant  0.25
    3               Trail  0.25
    4   Afghan Restaurant  0.00
    
    
    ----Garden District, Ryerson----
                           venue  freq
    0             Clothing Store  0.09
    1                Coffee Shop  0.08
    2  Middle Eastern Restaurant  0.03
    3                       Café  0.03
    4         Italian Restaurant  0.03
    
    
    ----Harbourfront East, Union Station, Toronto Islands----
             venue  freq
    0  Coffee Shop  0.13
    1     Aquarium  0.05
    2         Café  0.04
    3        Hotel  0.04
    4      Brewery  0.03
    
    
    ----High Park, The Junction South----
                    venue  freq
    0                Café  0.09
    1  Mexican Restaurant  0.09
    2     Thai Restaurant  0.09
    3         Flea Market  0.04
    4  Italian Restaurant  0.04
    
    
    ----India Bazaar, The Beaches West----
                      venue  freq
    0                  Park  0.09
    1        Sandwich Place  0.09
    2  Fast Food Restaurant  0.09
    3     Food & Drink Shop  0.05
    4      Sushi Restaurant  0.05
    
    
    ----Kensington Market, Chinatown, Grange Park----
                       venue  freq
    0                   Café  0.09
    1            Coffee Shop  0.07
    2     Mexican Restaurant  0.05
    3  Vietnamese Restaurant  0.05
    4                 Bakery  0.05
    
    
    ----Lawrence Park----
                   venue  freq
    0               Park  0.33
    1        Swim School  0.33
    2           Bus Line  0.33
    3  Afghan Restaurant  0.00
    4      Movie Theater  0.00
    
    
    ----Little Portugal, Trinity----
                               venue  freq
    0                            Bar  0.11
    1               Asian Restaurant  0.07
    2                     Restaurant  0.07
    3  Vegetarian / Vegan Restaurant  0.05
    4                    Coffee Shop  0.05
    
    
    ----Moore Park, Summerhill East----
                   venue  freq
    0       Tennis Court  0.33
    1                Gym  0.33
    2              Trail  0.33
    3             Museum  0.00
    4  Martial Arts Dojo  0.00
    
    
    ----North Toronto West,  Lawrence Park----
                      venue  freq
    0        Clothing Store  0.10
    1           Coffee Shop  0.10
    2         Metro Station  0.05
    3  Fast Food Restaurant  0.05
    4                   Spa  0.05
    
    
    ----Parkdale, Roncesvalles----
                             venue  freq
    0               Breakfast Spot  0.14
    1                    Gift Shop  0.14
    2                      Dog Run  0.07
    3                 Dessert Shop  0.07
    4  Eastern European Restaurant  0.07
    
    
    ----Queen's Park, Ontario Provincial Government----
                    venue  freq
    0         Coffee Shop  0.23
    1    Sushi Restaurant  0.06
    2         Yoga Studio  0.03
    3             Theater  0.03
    4  College Auditorium  0.03
    
    
    ----Regent Park, Harbourfront----
             venue  freq
    0  Coffee Shop  0.18
    1          Pub  0.07
    2       Bakery  0.07
    3         Park  0.07
    4      Theater  0.04
    
    
    ----Richmond, Adelaide, King----
               venue  freq
    0    Coffee Shop  0.11
    1           Café  0.05
    2     Restaurant  0.04
    3          Hotel  0.03
    4  Deli / Bodega  0.03
    
    
    ----Rosedale----
                   venue  freq
    0               Park  0.50
    1         Playground  0.25
    2              Trail  0.25
    3             Museum  0.00
    4  Martial Arts Dojo  0.00
    
    
    ----Roselawn----
                          venue  freq
    0               Music Venue  0.33
    1                    Garden  0.33
    2              Home Service  0.33
    3  Mediterranean Restaurant  0.00
    4               Men's Store  0.00
    
    
    ----Runnymede, Swansea----
                  venue  freq
    0              Café  0.08
    1       Coffee Shop  0.08
    2  Sushi Restaurant  0.05
    3       Pizza Place  0.05
    4               Pub  0.05
    
    
    ----St. James Town----
                     venue  freq
    0          Coffee Shop  0.06
    1                 Café  0.06
    2         Cocktail Bar  0.05
    3            Gastropub  0.04
    4  American Restaurant  0.04
    
    
    ----St. James Town, Cabbagetown----
                    venue  freq
    0         Coffee Shop  0.09
    1         Pizza Place  0.05
    2                 Pub  0.05
    3  Italian Restaurant  0.05
    4          Restaurant  0.05
    
    
    ----Stn A PO Boxes----
                     venue  freq
    0          Coffee Shop  0.10
    1                 Café  0.04
    2             Beer Bar  0.03
    3   Seafood Restaurant  0.03
    4  Japanese Restaurant  0.03
    
    
    ----Studio District----
             venue  freq
    0         Café  0.10
    1  Coffee Shop  0.08
    2    Gastropub  0.05
    3       Bakery  0.05
    4      Brewery  0.05
    
    
    ----Summerhill West, Rathnelly, South Hill, Forest Hill SE, Deer Park----
              venue  freq
    0           Pub  0.12
    1   Coffee Shop  0.12
    2  Liquor Store  0.06
    3    Bagel Shop  0.06
    4    Restaurant  0.06
    
    
    ----The Annex, North Midtown, Yorkville----
                   venue  freq
    0               Café  0.14
    1     Sandwich Place  0.14
    2        Coffee Shop  0.10
    3           Pharmacy  0.05
    4  Indian Restaurant  0.05
    
    
    ----The Beaches----
                   venue  freq
    0              Trail   0.2
    1  Health Food Store   0.2
    2                Pub   0.2
    3        Coffee Shop   0.2
    4  Afghan Restaurant   0.0
    
    
    ----The Danforth West, Riverdale----
                        venue  freq
    0        Greek Restaurant  0.21
    1      Italian Restaurant  0.07
    2             Coffee Shop  0.07
    3  Furniture / Home Store  0.05
    4          Ice Cream Shop  0.05
    
    
    ----Toronto Dominion Centre, Design Exchange----
               venue  freq
    0    Coffee Shop  0.10
    1          Hotel  0.06
    2           Café  0.06
    3     Restaurant  0.04
    4  Deli / Bodega  0.03
    
    
    ----University of Toronto, Harbord----
                     venue  freq
    0                 Café  0.17
    1                  Bar  0.06
    2           Restaurant  0.06
    3            Bookstore  0.06
    4  Japanese Restaurant  0.06
    
    


__Function to sort the venues in descending order__


```python
def return_most_common_venues(row, num_top_venues):
    row_categories = row.iloc[1:]
    row_categories_sorted = row_categories.sort_values(ascending=False)
    
    return row_categories_sorted.index.values[0:num_top_venues]
```

__New dataframe and display the top 10 venues for each neighborhood__


```python
num_top_venues = 10

indicators = ['st', 'nd', 'rd']

# create columns according to number of top venues
columns = ['Neighborhood']
for ind in np.arange(num_top_venues):
    try:
        columns.append('{}{} Most Common Venue'.format(ind+1, indicators[ind]))
    except:
        columns.append('{}th Most Common Venue'.format(ind+1))

# create a new dataframe
neighborhoods_venues_sorted = pd.DataFrame(columns=columns)
neighborhoods_venues_sorted['Neighborhood'] = Toronto_groupBy['Neighborhood']
for ind in np.arange(Toronto_groupBy.shape[0]):
    neighborhoods_venues_sorted.iloc[ind, 1:] = return_most_common_venues(Toronto_groupBy.iloc[ind, :], num_top_venues)

neighborhoods_venues_sorted.head()
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
      <th>Neighborhood</th>
      <th>1st Most Common Venue</th>
      <th>2nd Most Common Venue</th>
      <th>3rd Most Common Venue</th>
      <th>4th Most Common Venue</th>
      <th>5th Most Common Venue</th>
      <th>6th Most Common Venue</th>
      <th>7th Most Common Venue</th>
      <th>8th Most Common Venue</th>
      <th>9th Most Common Venue</th>
      <th>10th Most Common Venue</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Berczy Park</td>
      <td>Coffee Shop</td>
      <td>Cocktail Bar</td>
      <td>Pub</td>
      <td>Café</td>
      <td>Bakery</td>
      <td>Restaurant</td>
      <td>Cheese Shop</td>
      <td>Beer Bar</td>
      <td>Seafood Restaurant</td>
      <td>Bistro</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Brockton, Parkdale Village, Exhibition Place</td>
      <td>Café</td>
      <td>Coffee Shop</td>
      <td>Breakfast Spot</td>
      <td>Grocery Store</td>
      <td>Bakery</td>
      <td>Performing Arts Venue</td>
      <td>Pet Store</td>
      <td>Nightclub</td>
      <td>Climbing Gym</td>
      <td>Restaurant</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Business reply mail Processing Centre, South C...</td>
      <td>Light Rail Station</td>
      <td>Auto Workshop</td>
      <td>Park</td>
      <td>Pizza Place</td>
      <td>Recording Studio</td>
      <td>Restaurant</td>
      <td>Butcher</td>
      <td>Burrito Place</td>
      <td>Brewery</td>
      <td>Skate Park</td>
    </tr>
    <tr>
      <th>3</th>
      <td>CN Tower, King and Spadina, Railway Lands, Har...</td>
      <td>Airport Service</td>
      <td>Harbor / Marina</td>
      <td>Bar</td>
      <td>Plane</td>
      <td>Coffee Shop</td>
      <td>Rental Car Location</td>
      <td>Sculpture Garden</td>
      <td>Boat or Ferry</td>
      <td>Boutique</td>
      <td>Airport Lounge</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Central Bay Street</td>
      <td>Coffee Shop</td>
      <td>Italian Restaurant</td>
      <td>Café</td>
      <td>Sandwich Place</td>
      <td>Burger Joint</td>
      <td>Japanese Restaurant</td>
      <td>Department Store</td>
      <td>Salad Place</td>
      <td>Bubble Tea Shop</td>
      <td>Ice Cream Shop</td>
    </tr>
  </tbody>
</table>
</div>



## Cluster Neighbourhoods

__Use k-means to cluster the neighborhood into 5 clusters__


```python
# set number of clusters
kclusters = 5
Toronto_groupBy_cluster = Toronto_groupBy.drop('Neighborhood', 1)

# run k-means clustering
kmeans = KMeans(n_clusters=kclusters, random_state=0).fit(Toronto_groupBy_cluster)

# check cluster labels generated for each row in the dataframe
kmeans.labels_[0:10]
```




    array([0, 0, 0, 0, 0, 0, 0, 0, 0, 0], dtype=int32)



__New dataframe that includes the cluster as well as the top 10 venues for each neighborhood__


```python
# add clustering labels
neighborhoods_venues_sorted.insert(0, 'Cluster Labels', kmeans.labels_)

Toronto_merged = Toronto_filter
#Toronto_merged.rename(columns = {'Neighbourhood': 'Neighborhood'}, inplace = True)

# merge Toronto_groupBy with Toronto_filter to add latitude/longitude for each neighborhood
Toronto_merged = Toronto_merged.join(neighborhoods_venues_sorted.set_index('Neighborhood'), on='Neighborhood')
Toronto_merged.head() # check the last columns!
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
      <th>PostalCode</th>
      <th>Borough</th>
      <th>Neighborhood</th>
      <th>Latitude</th>
      <th>Longitude</th>
      <th>Cluster Labels</th>
      <th>1st Most Common Venue</th>
      <th>2nd Most Common Venue</th>
      <th>3rd Most Common Venue</th>
      <th>4th Most Common Venue</th>
      <th>5th Most Common Venue</th>
      <th>6th Most Common Venue</th>
      <th>7th Most Common Venue</th>
      <th>8th Most Common Venue</th>
      <th>9th Most Common Venue</th>
      <th>10th Most Common Venue</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>M4E</td>
      <td>East Toronto</td>
      <td>The Beaches</td>
      <td>43.676357</td>
      <td>-79.293031</td>
      <td>0</td>
      <td>Pub</td>
      <td>Health Food Store</td>
      <td>Coffee Shop</td>
      <td>Trail</td>
      <td>Yoga Studio</td>
      <td>Discount Store</td>
      <td>Distribution Center</td>
      <td>Dog Run</td>
      <td>Doner Restaurant</td>
      <td>Donut Shop</td>
    </tr>
    <tr>
      <th>1</th>
      <td>M4K</td>
      <td>East Toronto</td>
      <td>The Danforth West, Riverdale</td>
      <td>43.679557</td>
      <td>-79.352188</td>
      <td>0</td>
      <td>Greek Restaurant</td>
      <td>Italian Restaurant</td>
      <td>Coffee Shop</td>
      <td>Furniture / Home Store</td>
      <td>Restaurant</td>
      <td>Ice Cream Shop</td>
      <td>Yoga Studio</td>
      <td>Liquor Store</td>
      <td>Spa</td>
      <td>Juice Bar</td>
    </tr>
    <tr>
      <th>2</th>
      <td>M4L</td>
      <td>East Toronto</td>
      <td>India Bazaar, The Beaches West</td>
      <td>43.668999</td>
      <td>-79.315572</td>
      <td>0</td>
      <td>Park</td>
      <td>Sandwich Place</td>
      <td>Fast Food Restaurant</td>
      <td>Pet Store</td>
      <td>Pub</td>
      <td>Burrito Place</td>
      <td>Board Shop</td>
      <td>Italian Restaurant</td>
      <td>Fish &amp; Chips Shop</td>
      <td>Steakhouse</td>
    </tr>
    <tr>
      <th>3</th>
      <td>M4M</td>
      <td>East Toronto</td>
      <td>Studio District</td>
      <td>43.659526</td>
      <td>-79.340923</td>
      <td>0</td>
      <td>Café</td>
      <td>Coffee Shop</td>
      <td>Brewery</td>
      <td>Gastropub</td>
      <td>Bakery</td>
      <td>American Restaurant</td>
      <td>Yoga Studio</td>
      <td>Comfort Food Restaurant</td>
      <td>Seafood Restaurant</td>
      <td>Sandwich Place</td>
    </tr>
    <tr>
      <th>4</th>
      <td>M4N</td>
      <td>Central Toronto</td>
      <td>Lawrence Park</td>
      <td>43.728020</td>
      <td>-79.388790</td>
      <td>4</td>
      <td>Park</td>
      <td>Swim School</td>
      <td>Bus Line</td>
      <td>Yoga Studio</td>
      <td>Diner</td>
      <td>Falafel Restaurant</td>
      <td>Event Space</td>
      <td>Ethiopian Restaurant</td>
      <td>Electronics Store</td>
      <td>Eastern European Restaurant</td>
    </tr>
  </tbody>
</table>
</div>




```python
# create map
map_clusters = folium.Map(location=[lat, long], zoom_start=12)

# set color scheme for the clusters
x = np.arange(kclusters)
ys = [i + x + (i*x)**2 for i in range(kclusters)]
colors_array = cm.rainbow(np.linspace(0, 1, len(ys)))
rainbow = [colors.rgb2hex(i) for i in colors_array]


# add markers to the map
markers_colors = []
for lat, lon, poi, cluster in zip(Toronto_merged['Latitude'], Toronto_merged['Longitude'], Toronto_merged['Neighborhood'], 
                                  Toronto_merged['Cluster Labels']):
    label = folium.Popup(str(poi) + ' Cluster ' + str(cluster), parse_html=True)
    folium.CircleMarker(
        [lat, lon],
        radius=5,
        popup=label,
        color=rainbow[cluster-1],
        fill=True,
        fill_color=rainbow[cluster-1],
        fill_opacity=0.7).add_to(map_clusters)

    
map_clusters
```




<div style="width:100%;"><div style="position:relative;width:100%;height:0;padding-bottom:60%;"><span style="color:#565656">Make this Notebook Trusted to load map: File -> Trust Notebook</span><iframe src="about:blank" style="position:absolute;width:100%;height:100%;left:0;top:0;border:none !important;" data-html=PCFET0NUWVBFIGh0bWw+CjxoZWFkPiAgICAKICAgIDxtZXRhIGh0dHAtZXF1aXY9ImNvbnRlbnQtdHlwZSIgY29udGVudD0idGV4dC9odG1sOyBjaGFyc2V0PVVURi04IiAvPgogICAgPHNjcmlwdD5MX1BSRUZFUl9DQU5WQVMgPSBmYWxzZTsgTF9OT19UT1VDSCA9IGZhbHNlOyBMX0RJU0FCTEVfM0QgPSBmYWxzZTs8L3NjcmlwdD4KICAgIDxzY3JpcHQgc3JjPSJodHRwczovL2Nkbi5qc2RlbGl2ci5uZXQvbnBtL2xlYWZsZXRAMS4yLjAvZGlzdC9sZWFmbGV0LmpzIj48L3NjcmlwdD4KICAgIDxzY3JpcHQgc3JjPSJodHRwczovL2FqYXguZ29vZ2xlYXBpcy5jb20vYWpheC9saWJzL2pxdWVyeS8xLjExLjEvanF1ZXJ5Lm1pbi5qcyI+PC9zY3JpcHQ+CiAgICA8c2NyaXB0IHNyYz0iaHR0cHM6Ly9tYXhjZG4uYm9vdHN0cmFwY2RuLmNvbS9ib290c3RyYXAvMy4yLjAvanMvYm9vdHN0cmFwLm1pbi5qcyI+PC9zY3JpcHQ+CiAgICA8c2NyaXB0IHNyYz0iaHR0cHM6Ly9jZG5qcy5jbG91ZGZsYXJlLmNvbS9hamF4L2xpYnMvTGVhZmxldC5hd2Vzb21lLW1hcmtlcnMvMi4wLjIvbGVhZmxldC5hd2Vzb21lLW1hcmtlcnMuanMiPjwvc2NyaXB0PgogICAgPGxpbmsgcmVsPSJzdHlsZXNoZWV0IiBocmVmPSJodHRwczovL2Nkbi5qc2RlbGl2ci5uZXQvbnBtL2xlYWZsZXRAMS4yLjAvZGlzdC9sZWFmbGV0LmNzcyIvPgogICAgPGxpbmsgcmVsPSJzdHlsZXNoZWV0IiBocmVmPSJodHRwczovL21heGNkbi5ib290c3RyYXBjZG4uY29tL2Jvb3RzdHJhcC8zLjIuMC9jc3MvYm9vdHN0cmFwLm1pbi5jc3MiLz4KICAgIDxsaW5rIHJlbD0ic3R5bGVzaGVldCIgaHJlZj0iaHR0cHM6Ly9tYXhjZG4uYm9vdHN0cmFwY2RuLmNvbS9ib290c3RyYXAvMy4yLjAvY3NzL2Jvb3RzdHJhcC10aGVtZS5taW4uY3NzIi8+CiAgICA8bGluayByZWw9InN0eWxlc2hlZXQiIGhyZWY9Imh0dHBzOi8vbWF4Y2RuLmJvb3RzdHJhcGNkbi5jb20vZm9udC1hd2Vzb21lLzQuNi4zL2Nzcy9mb250LWF3ZXNvbWUubWluLmNzcyIvPgogICAgPGxpbmsgcmVsPSJzdHlsZXNoZWV0IiBocmVmPSJodHRwczovL2NkbmpzLmNsb3VkZmxhcmUuY29tL2FqYXgvbGlicy9MZWFmbGV0LmF3ZXNvbWUtbWFya2Vycy8yLjAuMi9sZWFmbGV0LmF3ZXNvbWUtbWFya2Vycy5jc3MiLz4KICAgIDxsaW5rIHJlbD0ic3R5bGVzaGVldCIgaHJlZj0iaHR0cHM6Ly9yYXdnaXQuY29tL3B5dGhvbi12aXN1YWxpemF0aW9uL2ZvbGl1bS9tYXN0ZXIvZm9saXVtL3RlbXBsYXRlcy9sZWFmbGV0LmF3ZXNvbWUucm90YXRlLmNzcyIvPgogICAgPHN0eWxlPmh0bWwsIGJvZHkge3dpZHRoOiAxMDAlO2hlaWdodDogMTAwJTttYXJnaW46IDA7cGFkZGluZzogMDt9PC9zdHlsZT4KICAgIDxzdHlsZT4jbWFwIHtwb3NpdGlvbjphYnNvbHV0ZTt0b3A6MDtib3R0b206MDtyaWdodDowO2xlZnQ6MDt9PC9zdHlsZT4KICAgIAogICAgICAgICAgICA8c3R5bGU+ICNtYXBfZWMwYjIzNDJkMzg1NDU4MTkxNDQzM2VhOTFhMWNiOTggewogICAgICAgICAgICAgICAgcG9zaXRpb24gOiByZWxhdGl2ZTsKICAgICAgICAgICAgICAgIHdpZHRoIDogMTAwLjAlOwogICAgICAgICAgICAgICAgaGVpZ2h0OiAxMDAuMCU7CiAgICAgICAgICAgICAgICBsZWZ0OiAwLjAlOwogICAgICAgICAgICAgICAgdG9wOiAwLjAlOwogICAgICAgICAgICAgICAgfQogICAgICAgICAgICA8L3N0eWxlPgogICAgICAgIAo8L2hlYWQ+Cjxib2R5PiAgICAKICAgIAogICAgICAgICAgICA8ZGl2IGNsYXNzPSJmb2xpdW0tbWFwIiBpZD0ibWFwX2VjMGIyMzQyZDM4NTQ1ODE5MTQ0MzNlYTkxYTFjYjk4IiA+PC9kaXY+CiAgICAgICAgCjwvYm9keT4KPHNjcmlwdD4gICAgCiAgICAKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGJvdW5kcyA9IG51bGw7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgdmFyIG1hcF9lYzBiMjM0MmQzODU0NTgxOTE0NDMzZWE5MWExY2I5OCA9IEwubWFwKAogICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgJ21hcF9lYzBiMjM0MmQzODU0NTgxOTE0NDMzZWE5MWExY2I5OCcsCiAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICB7Y2VudGVyOiBbNDMuNjUzMiwtNzkuMzgzMl0sCiAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICB6b29tOiAxMiwKICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgIG1heEJvdW5kczogYm91bmRzLAogICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgbGF5ZXJzOiBbXSwKICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgIHdvcmxkQ29weUp1bXA6IGZhbHNlLAogICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgY3JzOiBMLkNSUy5FUFNHMzg1NwogICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICB9KTsKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHRpbGVfbGF5ZXJfN2E4NjAyOGVlY2E5NDBmMTllY2Y2MzU5MTg1NDAzYTAgPSBMLnRpbGVMYXllcigKICAgICAgICAgICAgICAgICdodHRwczovL3tzfS50aWxlLm9wZW5zdHJlZXRtYXAub3JnL3t6fS97eH0ve3l9LnBuZycsCiAgICAgICAgICAgICAgICB7CiAgImF0dHJpYnV0aW9uIjogbnVsbCwKICAiZGV0ZWN0UmV0aW5hIjogZmFsc2UsCiAgIm1heFpvb20iOiAxOCwKICAibWluWm9vbSI6IDEsCiAgIm5vV3JhcCI6IGZhbHNlLAogICJzdWJkb21haW5zIjogImFiYyIKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfZWMwYjIzNDJkMzg1NDU4MTkxNDQzM2VhOTFhMWNiOTgpOwogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyX2U1ZGQzNjYyNjAzZDQ1MDI4Zjg3ZTcwYzFmYTM2ZmY2ID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNjc2MzU3Mzk5OTk5OTksLTc5LjI5MzAzMTJdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiI2ZmMDAwMCIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiNmZjAwMDAiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfZWMwYjIzNDJkMzg1NDU4MTkxNDQzM2VhOTFhMWNiOTgpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfODEyMGFkOTY2Y2M3NDFmYTk2YTg3Zjk3OGI5NmUxNzggPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfNDQyZWRhMjhkMjZmNDQ4Mjg2YTBmYjk1NDQyZGNkYWQgPSAkKCc8ZGl2IGlkPSJodG1sXzQ0MmVkYTI4ZDI2ZjQ0ODI4NmEwZmI5NTQ0MmRjZGFkIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5UaGUgQmVhY2hlcyBDbHVzdGVyIDA8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzgxMjBhZDk2NmNjNzQxZmE5NmE4N2Y5NzhiOTZlMTc4LnNldENvbnRlbnQoaHRtbF80NDJlZGEyOGQyNmY0NDgyODZhMGZiOTU0NDJkY2RhZCk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl9lNWRkMzY2MjYwM2Q0NTAyOGY4N2U3MGMxZmEzNmZmNi5iaW5kUG9wdXAocG9wdXBfODEyMGFkOTY2Y2M3NDFmYTk2YTg3Zjk3OGI5NmUxNzgpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfNWRmYjBlY2Y2ZGZlNDE4NWIxZDAwZGNhNDU4Y2I3NzIgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42Nzk1NTcxLC03OS4zNTIxODhdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiI2ZmMDAwMCIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiNmZjAwMDAiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfZWMwYjIzNDJkMzg1NDU4MTkxNDQzM2VhOTFhMWNiOTgpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfMGQ4OWYwMmVkMTUxNGMwNzhhMDk1YmRkMGEzOWU4NmEgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfZmUxYTJlMTlmZGZkNDBlZGIzNzZiZTEwNjNmZWVlMWMgPSAkKCc8ZGl2IGlkPSJodG1sX2ZlMWEyZTE5ZmRmZDQwZWRiMzc2YmUxMDYzZmVlZTFjIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5UaGUgRGFuZm9ydGggV2VzdCwgUml2ZXJkYWxlIENsdXN0ZXIgMDwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfMGQ4OWYwMmVkMTUxNGMwNzhhMDk1YmRkMGEzOWU4NmEuc2V0Q29udGVudChodG1sX2ZlMWEyZTE5ZmRmZDQwZWRiMzc2YmUxMDYzZmVlZTFjKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzVkZmIwZWNmNmRmZTQxODViMWQwMGRjYTQ1OGNiNzcyLmJpbmRQb3B1cChwb3B1cF8wZDg5ZjAyZWQxNTE0YzA3OGEwOTViZGQwYTM5ZTg2YSk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl83NjI4ZDgyMzM2NzE0MDAwYTQ3NDAxNzFlNWIxZGU2YyA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjY2ODk5ODUsLTc5LjMxNTU3MTU5OTk5OTk4XSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogIiNmZjAwMDAiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjZmYwMDAwIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwX2VjMGIyMzQyZDM4NTQ1ODE5MTQ0MzNlYTkxYTFjYjk4KTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzc5NDgyYTM4ZGY2OTQwZTU5MDJmNTA1N2I4OGMwOTU2ID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sX2I3ODM1YWI4MDEzNDQ0MjBiZWI4ZGIwYTIwNDZjYTkzID0gJCgnPGRpdiBpZD0iaHRtbF9iNzgzNWFiODAxMzQ0NDIwYmViOGRiMGEyMDQ2Y2E5MyIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+SW5kaWEgQmF6YWFyLCBUaGUgQmVhY2hlcyBXZXN0IENsdXN0ZXIgMDwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfNzk0ODJhMzhkZjY5NDBlNTkwMmY1MDU3Yjg4YzA5NTYuc2V0Q29udGVudChodG1sX2I3ODM1YWI4MDEzNDQ0MjBiZWI4ZGIwYTIwNDZjYTkzKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzc2MjhkODIzMzY3MTQwMDBhNDc0MDE3MWU1YjFkZTZjLmJpbmRQb3B1cChwb3B1cF83OTQ4MmEzOGRmNjk0MGU1OTAyZjUwNTdiODhjMDk1Nik7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl82YmRiMDRlMWZhYzc0OGFmYjNjNDUwYjMyMTE5MGI1YiA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjY1OTUyNTUsLTc5LjM0MDkyM10sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICIjZmYwMDAwIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiI2ZmMDAwMCIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF9lYzBiMjM0MmQzODU0NTgxOTE0NDMzZWE5MWExY2I5OCk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF84OTAzNjA0ZjE2YzI0MGQ1OGJlMDgzZjYzMzI0NWYzZiA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF9jNDQ4ZTJkZGIyZGY0NjU5YTVhNTU5NzE0MzljNWZlMyA9ICQoJzxkaXYgaWQ9Imh0bWxfYzQ0OGUyZGRiMmRmNDY1OWE1YTU1OTcxNDM5YzVmZTMiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPlN0dWRpbyBEaXN0cmljdCBDbHVzdGVyIDA8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzg5MDM2MDRmMTZjMjQwZDU4YmUwODNmNjMzMjQ1ZjNmLnNldENvbnRlbnQoaHRtbF9jNDQ4ZTJkZGIyZGY0NjU5YTVhNTU5NzE0MzljNWZlMyk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl82YmRiMDRlMWZhYzc0OGFmYjNjNDUwYjMyMTE5MGI1Yi5iaW5kUG9wdXAocG9wdXBfODkwMzYwNGYxNmMyNDBkNThiZTA4M2Y2MzMyNDVmM2YpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfYzYyMTRjZGUwOTQwNGU1NThjMzdlNjkyZGVjOTdjYTIgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My43MjgwMjA1LC03OS4zODg3OTAxXSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogIiNmZmIzNjAiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjZmZiMzYwIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwX2VjMGIyMzQyZDM4NTQ1ODE5MTQ0MzNlYTkxYTFjYjk4KTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwX2M3ZTAwNzVkNjJjZjQwNzA4ZjkxNzc0YjA5ZGQyYjg2ID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzIxYThjMjUwNDg2ZjRlYmE4ZDUxYTllNWNhMWQ3ZGU2ID0gJCgnPGRpdiBpZD0iaHRtbF8yMWE4YzI1MDQ4NmY0ZWJhOGQ1MWE5ZTVjYTFkN2RlNiIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+TGF3cmVuY2UgUGFyayBDbHVzdGVyIDQ8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwX2M3ZTAwNzVkNjJjZjQwNzA4ZjkxNzc0YjA5ZGQyYjg2LnNldENvbnRlbnQoaHRtbF8yMWE4YzI1MDQ4NmY0ZWJhOGQ1MWE5ZTVjYTFkN2RlNik7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl9jNjIxNGNkZTA5NDA0ZTU1OGMzN2U2OTJkZWM5N2NhMi5iaW5kUG9wdXAocG9wdXBfYzdlMDA3NWQ2MmNmNDA3MDhmOTE3NzRiMDlkZDJiODYpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfMzg5ODI0ODIzZmU4NGQ1OGFlYThhMGJlZWU0YTQ3ODMgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My43MTI3NTExLC03OS4zOTAxOTc1XSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogIiNmZjAwMDAiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjZmYwMDAwIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwX2VjMGIyMzQyZDM4NTQ1ODE5MTQ0MzNlYTkxYTFjYjk4KTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwX2I4MzUyODVlZjVjZjRjNGRhZjJlYzA5M2Y3NzdlMjkzID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sX2I5NGY0NDE2YmRmODRjNGE5NTk1NjJlMTUxZmQ3MTUwID0gJCgnPGRpdiBpZD0iaHRtbF9iOTRmNDQxNmJkZjg0YzRhOTU5NTYyZTE1MWZkNzE1MCIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+RGF2aXN2aWxsZSBOb3J0aCBDbHVzdGVyIDA8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwX2I4MzUyODVlZjVjZjRjNGRhZjJlYzA5M2Y3NzdlMjkzLnNldENvbnRlbnQoaHRtbF9iOTRmNDQxNmJkZjg0YzRhOTU5NTYyZTE1MWZkNzE1MCk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl8zODk4MjQ4MjNmZTg0ZDU4YWVhOGEwYmVlZTRhNDc4My5iaW5kUG9wdXAocG9wdXBfYjgzNTI4NWVmNWNmNGM0ZGFmMmVjMDkzZjc3N2UyOTMpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfZDY4MWFkZjEwZDg1NDZhZGFlNTM2MzFkNTYxYjU4YmQgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My43MTUzODM0LC03OS40MDU2Nzg0MDAwMDAwMV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICIjZmYwMDAwIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiI2ZmMDAwMCIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF9lYzBiMjM0MmQzODU0NTgxOTE0NDMzZWE5MWExY2I5OCk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF8zNmMwOTViYTg0YzA0MDVkYTUyNGY3MDhmYTY5ZThhZCA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF9jZjdjMmU4NGE4OWU0NzdiYjUwNzA1ZTBlZWI5MTg3MyA9ICQoJzxkaXYgaWQ9Imh0bWxfY2Y3YzJlODRhODllNDc3YmI1MDcwNWUwZWViOTE4NzMiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPk5vcnRoIFRvcm9udG8gV2VzdCwgIExhd3JlbmNlIFBhcmsgQ2x1c3RlciAwPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF8zNmMwOTViYTg0YzA0MDVkYTUyNGY3MDhmYTY5ZThhZC5zZXRDb250ZW50KGh0bWxfY2Y3YzJlODRhODllNDc3YmI1MDcwNWUwZWViOTE4NzMpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfZDY4MWFkZjEwZDg1NDZhZGFlNTM2MzFkNTYxYjU4YmQuYmluZFBvcHVwKHBvcHVwXzM2YzA5NWJhODRjMDQwNWRhNTI0ZjcwOGZhNjllOGFkKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyX2VjNGZkZGY2MWE0YjRjZjI4YjIzNjJkNzNhMDQ2Y2U1ID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNzA0MzI0NCwtNzkuMzg4NzkwMV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICIjZmYwMDAwIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiI2ZmMDAwMCIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF9lYzBiMjM0MmQzODU0NTgxOTE0NDMzZWE5MWExY2I5OCk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF80MDQ2ZGFlOTg3MGY0YzljOGE0MjBlODg2MDA5NTg4NSA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF85YWE3ZjZlMzA0NTM0ODM0YjJhMTAzMGZlZDFkOWUzNiA9ICQoJzxkaXYgaWQ9Imh0bWxfOWFhN2Y2ZTMwNDUzNDgzNGIyYTEwMzBmZWQxZDllMzYiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkRhdmlzdmlsbGUgQ2x1c3RlciAwPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF80MDQ2ZGFlOTg3MGY0YzljOGE0MjBlODg2MDA5NTg4NS5zZXRDb250ZW50KGh0bWxfOWFhN2Y2ZTMwNDUzNDgzNGIyYTEwMzBmZWQxZDllMzYpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfZWM0ZmRkZjYxYTRiNGNmMjhiMjM2MmQ3M2EwNDZjZTUuYmluZFBvcHVwKHBvcHVwXzQwNDZkYWU5ODcwZjRjOWM4YTQyMGU4ODYwMDk1ODg1KTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyX2YyN2I1ZjRhOWY4ODQxZWViNzNlYTdjYmZhY2I4OWE3ID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNjg5NTc0MywtNzkuMzgzMTU5OTAwMDAwMDFdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiIzgwMDBmZiIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiM4MDAwZmYiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfZWMwYjIzNDJkMzg1NDU4MTkxNDQzM2VhOTFhMWNiOTgpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfMmZmYzkyMGIyOWRhNGU2Njk5MTFiODdkNjAzNTJhYmYgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfYTY5YTBiNzY3Y2I0NDc2NDg1MDc3OTdmY2MyNjg0YzggPSAkKCc8ZGl2IGlkPSJodG1sX2E2OWEwYjc2N2NiNDQ3NjQ4NTA3Nzk3ZmNjMjY4NGM4IiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5Nb29yZSBQYXJrLCBTdW1tZXJoaWxsIEVhc3QgQ2x1c3RlciAxPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF8yZmZjOTIwYjI5ZGE0ZTY2OTkxMWI4N2Q2MDM1MmFiZi5zZXRDb250ZW50KGh0bWxfYTY5YTBiNzY3Y2I0NDc2NDg1MDc3OTdmY2MyNjg0YzgpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfZjI3YjVmNGE5Zjg4NDFlZWI3M2VhN2NiZmFjYjg5YTcuYmluZFBvcHVwKHBvcHVwXzJmZmM5MjBiMjlkYTRlNjY5OTExYjg3ZDYwMzUyYWJmKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzFiOWZiZGJkOTA4ZDRhYWFhYzQ2M2UzOWU5ZjE5MzJkID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNjg2NDEyMjk5OTk5OTksLTc5LjQwMDA0OTNdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiI2ZmMDAwMCIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiNmZjAwMDAiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfZWMwYjIzNDJkMzg1NDU4MTkxNDQzM2VhOTFhMWNiOTgpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfMmQwZTY5OTQ1ZmY5NDRkYmJkYjlmMTk2MmVlM2Y3ZWEgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfODFmN2IyYzIzNmJhNGU5MGIyNGRjOTUyNjliYTBmMWUgPSAkKCc8ZGl2IGlkPSJodG1sXzgxZjdiMmMyMzZiYTRlOTBiMjRkYzk1MjY5YmEwZjFlIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5TdW1tZXJoaWxsIFdlc3QsIFJhdGhuZWxseSwgU291dGggSGlsbCwgRm9yZXN0IEhpbGwgU0UsIERlZXIgUGFyayBDbHVzdGVyIDA8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzJkMGU2OTk0NWZmOTQ0ZGJiZGI5ZjE5NjJlZTNmN2VhLnNldENvbnRlbnQoaHRtbF84MWY3YjJjMjM2YmE0ZTkwYjI0ZGM5NTI2OWJhMGYxZSk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl8xYjlmYmRiZDkwOGQ0YWFhYWM0NjNlMzllOWYxOTMyZC5iaW5kUG9wdXAocG9wdXBfMmQwZTY5OTQ1ZmY5NDRkYmJkYjlmMTk2MmVlM2Y3ZWEpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfYTJkZGEzN2YyNDUzNGQ5Y2JkYWY5MmE2MjUzNTZlYzEgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42Nzk1NjI2LC03OS4zNzc1Mjk0MDAwMDAwMV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICIjMDBiNWViIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiIzAwYjVlYiIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF9lYzBiMjM0MmQzODU0NTgxOTE0NDMzZWE5MWExY2I5OCk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF84NDAyYzgyZTgwNjg0ZTQ3OTM4MTVkNWM0OTgwMzYxNCA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF8wNzdkZjE5NWIwNzM0NzY4YWQ1YTRmYmE1NWEzMGM2NyA9ICQoJzxkaXYgaWQ9Imh0bWxfMDc3ZGYxOTViMDczNDc2OGFkNWE0ZmJhNTVhMzBjNjciIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPlJvc2VkYWxlIENsdXN0ZXIgMjwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfODQwMmM4MmU4MDY4NGU0NzkzODE1ZDVjNDk4MDM2MTQuc2V0Q29udGVudChodG1sXzA3N2RmMTk1YjA3MzQ3NjhhZDVhNGZiYTU1YTMwYzY3KTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyX2EyZGRhMzdmMjQ1MzRkOWNiZGFmOTJhNjI1MzU2ZWMxLmJpbmRQb3B1cChwb3B1cF84NDAyYzgyZTgwNjg0ZTQ3OTM4MTVkNWM0OTgwMzYxNCk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl80MTNlMTAxOTc0ZDM0YmI5YjZlZWQ4ODM4ZjE1M2JkNiA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjY2Nzk2NywtNzkuMzY3Njc1M10sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICIjZmYwMDAwIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiI2ZmMDAwMCIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF9lYzBiMjM0MmQzODU0NTgxOTE0NDMzZWE5MWExY2I5OCk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF85MjgzNmU4ODVmZjA0MzY1YTVlYmYxYTgzYjUzYjRiMCA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF9mNjNjZDQxODBkOWY0YjNhOWFhZTFkZTUyZjA3ZmYwNiA9ICQoJzxkaXYgaWQ9Imh0bWxfZjYzY2Q0MTgwZDlmNGIzYTlhYWUxZGU1MmYwN2ZmMDYiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPlN0LiBKYW1lcyBUb3duLCBDYWJiYWdldG93biBDbHVzdGVyIDA8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzkyODM2ZTg4NWZmMDQzNjVhNWViZjFhODNiNTNiNGIwLnNldENvbnRlbnQoaHRtbF9mNjNjZDQxODBkOWY0YjNhOWFhZTFkZTUyZjA3ZmYwNik7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl80MTNlMTAxOTc0ZDM0YmI5YjZlZWQ4ODM4ZjE1M2JkNi5iaW5kUG9wdXAocG9wdXBfOTI4MzZlODg1ZmYwNDM2NWE1ZWJmMWE4M2I1M2I0YjApOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfM2E5YWJiZTU0NzdlNGYxZDljNTUwZGZlYjJmMzg5MmIgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42NjU4NTk5LC03OS4zODMxNTk5MDAwMDAwMV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICIjZmYwMDAwIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiI2ZmMDAwMCIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF9lYzBiMjM0MmQzODU0NTgxOTE0NDMzZWE5MWExY2I5OCk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF83ZDJkYzQ1ZTBmZWI0ZDUwYjQxOGQzMjdlMmM1ODFiOSA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF9kZTJhM2NjODA0OTk0ZWE5ODY5YWNiOTg1ZDk2NzJkOSA9ICQoJzxkaXYgaWQ9Imh0bWxfZGUyYTNjYzgwNDk5NGVhOTg2OWFjYjk4NWQ5NjcyZDkiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkNodXJjaCBhbmQgV2VsbGVzbGV5IENsdXN0ZXIgMDwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfN2QyZGM0NWUwZmViNGQ1MGI0MThkMzI3ZTJjNTgxYjkuc2V0Q29udGVudChodG1sX2RlMmEzY2M4MDQ5OTRlYTk4NjlhY2I5ODVkOTY3MmQ5KTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzNhOWFiYmU1NDc3ZTRmMWQ5YzU1MGRmZWIyZjM4OTJiLmJpbmRQb3B1cChwb3B1cF83ZDJkYzQ1ZTBmZWI0ZDUwYjQxOGQzMjdlMmM1ODFiOSk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl81ZThmOTE2ZWZlZjU0NzhmYWE5NGQ4OWUwYWJiMzQzNyA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjY1NDI1OTksLTc5LjM2MDYzNTldLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiI2ZmMDAwMCIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiNmZjAwMDAiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfZWMwYjIzNDJkMzg1NDU4MTkxNDQzM2VhOTFhMWNiOTgpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfZGMwNjdlODY1ZWE5NDI0ZGI0ZmRhNzQxOTNkNDMzODYgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfMTc0ODkwZTIzYWViNGMwOTgwN2FjMGIzM2I3NjU5OTggPSAkKCc8ZGl2IGlkPSJodG1sXzE3NDg5MGUyM2FlYjRjMDk4MDdhYzBiMzNiNzY1OTk4IiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5SZWdlbnQgUGFyaywgSGFyYm91cmZyb250IENsdXN0ZXIgMDwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfZGMwNjdlODY1ZWE5NDI0ZGI0ZmRhNzQxOTNkNDMzODYuc2V0Q29udGVudChodG1sXzE3NDg5MGUyM2FlYjRjMDk4MDdhYzBiMzNiNzY1OTk4KTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzVlOGY5MTZlZmVmNTQ3OGZhYTk0ZDg5ZTBhYmIzNDM3LmJpbmRQb3B1cChwb3B1cF9kYzA2N2U4NjVlYTk0MjRkYjRmZGE3NDE5M2Q0MzM4Nik7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl82N2QzZDNkOGE0NWM0YTQ5YmQxN2Q3YjJhMjYzMThkYyA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjY1NzE2MTgsLTc5LjM3ODkzNzA5OTk5OTk5XSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogIiNmZjAwMDAiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjZmYwMDAwIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwX2VjMGIyMzQyZDM4NTQ1ODE5MTQ0MzNlYTkxYTFjYjk4KTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwX2IzNzQ0ZWIxMjFkNjRhZmNhZDVmMWJiNmJhOWNmN2EyID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sX2QyMWU4ZGRkMjUwYjRhNTNiNjk1MGIwZGE2ZmQ2ODI5ID0gJCgnPGRpdiBpZD0iaHRtbF9kMjFlOGRkZDI1MGI0YTUzYjY5NTBiMGRhNmZkNjgyOSIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+R2FyZGVuIERpc3RyaWN0LCBSeWVyc29uIENsdXN0ZXIgMDwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfYjM3NDRlYjEyMWQ2NGFmY2FkNWYxYmI2YmE5Y2Y3YTIuc2V0Q29udGVudChodG1sX2QyMWU4ZGRkMjUwYjRhNTNiNjk1MGIwZGE2ZmQ2ODI5KTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzY3ZDNkM2Q4YTQ1YzRhNDliZDE3ZDdiMmEyNjMxOGRjLmJpbmRQb3B1cChwb3B1cF9iMzc0NGViMTIxZDY0YWZjYWQ1ZjFiYjZiYTljZjdhMik7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl80YWI2ZmU3ZDQ0ZmY0ZDk1ODEzNjY4YTNhY2Q2ZGJmZSA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjY1MTQ5MzksLTc5LjM3NTQxNzldLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiI2ZmMDAwMCIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiNmZjAwMDAiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfZWMwYjIzNDJkMzg1NDU4MTkxNDQzM2VhOTFhMWNiOTgpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfMDE3ZmJkMGVmYTYzNDc1ZmI3ODNhMTAyNGI4NGI4ODEgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfMzhiZmMxZjc3YmUyNDE5YThlMWE2ZmFjM2Q3YjhjY2QgPSAkKCc8ZGl2IGlkPSJodG1sXzM4YmZjMWY3N2JlMjQxOWE4ZTFhNmZhYzNkN2I4Y2NkIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5TdC4gSmFtZXMgVG93biBDbHVzdGVyIDA8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzAxN2ZiZDBlZmE2MzQ3NWZiNzgzYTEwMjRiODRiODgxLnNldENvbnRlbnQoaHRtbF8zOGJmYzFmNzdiZTI0MTlhOGUxYTZmYWMzZDdiOGNjZCk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl80YWI2ZmU3ZDQ0ZmY0ZDk1ODEzNjY4YTNhY2Q2ZGJmZS5iaW5kUG9wdXAocG9wdXBfMDE3ZmJkMGVmYTYzNDc1ZmI3ODNhMTAyNGI4NGI4ODEpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfNTY3MWE2NjcyMjZjNDJkNGJkNGJmNjE5YjE0OTc0ZTUgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42NDQ3NzA3OTk5OTk5OTYsLTc5LjM3MzMwNjRdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiI2ZmMDAwMCIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiNmZjAwMDAiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfZWMwYjIzNDJkMzg1NDU4MTkxNDQzM2VhOTFhMWNiOTgpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfZTMzZmU1NGNkYzE0NDlmMjgwNTdkNzJmNGVkZmQ2NWUgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfZWZjODc3OGFjM2FkNDc0NGFmNTNmOGZmN2JiOTM1YzcgPSAkKCc8ZGl2IGlkPSJodG1sX2VmYzg3NzhhYzNhZDQ3NDRhZjUzZjhmZjdiYjkzNWM3IiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5CZXJjenkgUGFyayBDbHVzdGVyIDA8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwX2UzM2ZlNTRjZGMxNDQ5ZjI4MDU3ZDcyZjRlZGZkNjVlLnNldENvbnRlbnQoaHRtbF9lZmM4Nzc4YWMzYWQ0NzQ0YWY1M2Y4ZmY3YmI5MzVjNyk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl81NjcxYTY2NzIyNmM0MmQ0YmQ0YmY2MTliMTQ5NzRlNS5iaW5kUG9wdXAocG9wdXBfZTMzZmU1NGNkYzE0NDlmMjgwNTdkNzJmNGVkZmQ2NWUpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfYmZlMDAwYTQ0N2ZlNDEyZmE0NDA5ZDA1NmE1MjNlN2UgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42NTc5NTI0LC03OS4zODczODI2XSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogIiNmZjAwMDAiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjZmYwMDAwIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwX2VjMGIyMzQyZDM4NTQ1ODE5MTQ0MzNlYTkxYTFjYjk4KTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzNhYTg2NjVkYWNjZTQ5N2Y5NzcwODM0MDYwZGE1YTBiID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sX2M0N2JkMjU2NDRiYTRiNmY5OWExMjc0ZDg4NzM5YWViID0gJCgnPGRpdiBpZD0iaHRtbF9jNDdiZDI1NjQ0YmE0YjZmOTlhMTI3NGQ4ODczOWFlYiIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+Q2VudHJhbCBCYXkgU3RyZWV0IENsdXN0ZXIgMDwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfM2FhODY2NWRhY2NlNDk3Zjk3NzA4MzQwNjBkYTVhMGIuc2V0Q29udGVudChodG1sX2M0N2JkMjU2NDRiYTRiNmY5OWExMjc0ZDg4NzM5YWViKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyX2JmZTAwMGE0NDdmZTQxMmZhNDQwOWQwNTZhNTIzZTdlLmJpbmRQb3B1cChwb3B1cF8zYWE4NjY1ZGFjY2U0OTdmOTc3MDgzNDA2MGRhNWEwYik7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl8xZWE5NDcyMDIwODE0NTcyYmIxNGYzN2VkM2U3YzI3NyA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjY1MDU3MTIwMDAwMDAxLC03OS4zODQ1Njc1XSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogIiNmZjAwMDAiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjZmYwMDAwIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwX2VjMGIyMzQyZDM4NTQ1ODE5MTQ0MzNlYTkxYTFjYjk4KTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwX2FiOGE3MTY3ZGIxNTRmMTdhMjQ5YmM5MzRlYjlmM2FmID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzRhZDA5ZDVmMWEyMjQ3OTY4NGU4Nzg4ZDJiOWJiZDhiID0gJCgnPGRpdiBpZD0iaHRtbF80YWQwOWQ1ZjFhMjI0Nzk2ODRlODc4OGQyYjliYmQ4YiIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+UmljaG1vbmQsIEFkZWxhaWRlLCBLaW5nIENsdXN0ZXIgMDwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfYWI4YTcxNjdkYjE1NGYxN2EyNDliYzkzNGViOWYzYWYuc2V0Q29udGVudChodG1sXzRhZDA5ZDVmMWEyMjQ3OTY4NGU4Nzg4ZDJiOWJiZDhiKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzFlYTk0NzIwMjA4MTQ1NzJiYjE0ZjM3ZWQzZTdjMjc3LmJpbmRQb3B1cChwb3B1cF9hYjhhNzE2N2RiMTU0ZjE3YTI0OWJjOTM0ZWI5ZjNhZik7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl8wZDQwYjYzYmMxNzA0NzMyODljNTIxZGE5ZmNjYzQyMSA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjY0MDgxNTcsLTc5LjM4MTc1MjI5OTk5OTk5XSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogIiNmZjAwMDAiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjZmYwMDAwIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwX2VjMGIyMzQyZDM4NTQ1ODE5MTQ0MzNlYTkxYTFjYjk4KTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzUzOGNiODkxNjRiYTQ5ZGRiODRjMDc5OWMyMzJkNTQxID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzVjZTMxZDYzODI0MTRhZjU5MTRhMjBjY2JkMWNlNmI2ID0gJCgnPGRpdiBpZD0iaHRtbF81Y2UzMWQ2MzgyNDE0YWY1OTE0YTIwY2NiZDFjZTZiNiIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+SGFyYm91cmZyb250IEVhc3QsIFVuaW9uIFN0YXRpb24sIFRvcm9udG8gSXNsYW5kcyBDbHVzdGVyIDA8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzUzOGNiODkxNjRiYTQ5ZGRiODRjMDc5OWMyMzJkNTQxLnNldENvbnRlbnQoaHRtbF81Y2UzMWQ2MzgyNDE0YWY1OTE0YTIwY2NiZDFjZTZiNik7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl8wZDQwYjYzYmMxNzA0NzMyODljNTIxZGE5ZmNjYzQyMS5iaW5kUG9wdXAocG9wdXBfNTM4Y2I4OTE2NGJhNDlkZGI4NGMwNzk5YzIzMmQ1NDEpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfNzdmNTdlZTRiNDg3NGFiODliOTcwNTA1ZDc2ZGIxOGEgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42NDcxNzY4LC03OS4zODE1NzY0MDAwMDAwMV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICIjZmYwMDAwIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiI2ZmMDAwMCIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF9lYzBiMjM0MmQzODU0NTgxOTE0NDMzZWE5MWExY2I5OCk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF8zYjk4NTQ5NjUxNzQ0ZDRlYWU3MDNhNmVhYWFhYThlMiA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF9jZGUzN2Y3NjAzMWI0MmJjOTE3Nzg1MWM4OGM5MGEzOSA9ICQoJzxkaXYgaWQ9Imh0bWxfY2RlMzdmNzYwMzFiNDJiYzkxNzc4NTFjODhjOTBhMzkiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPlRvcm9udG8gRG9taW5pb24gQ2VudHJlLCBEZXNpZ24gRXhjaGFuZ2UgQ2x1c3RlciAwPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF8zYjk4NTQ5NjUxNzQ0ZDRlYWU3MDNhNmVhYWFhYThlMi5zZXRDb250ZW50KGh0bWxfY2RlMzdmNzYwMzFiNDJiYzkxNzc4NTFjODhjOTBhMzkpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfNzdmNTdlZTRiNDg3NGFiODliOTcwNTA1ZDc2ZGIxOGEuYmluZFBvcHVwKHBvcHVwXzNiOTg1NDk2NTE3NDRkNGVhZTcwM2E2ZWFhYWFhOGUyKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzNlOGQ2YWJiYzc4ODQzMWViYzk5MzU1ZTdiYTNlM2YwID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNjQ4MTk4NSwtNzkuMzc5ODE2OTAwMDAwMDFdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiI2ZmMDAwMCIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiNmZjAwMDAiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfZWMwYjIzNDJkMzg1NDU4MTkxNDQzM2VhOTFhMWNiOTgpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfMTJjY2QxNzE1ZmZmNDUyNWFjM2FkMzc4ZjQwMzNiZmMgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfOWU5ODMyOTY5NTZlNGRlMDkyMDVkOTJlMDhiY2U1NmUgPSAkKCc8ZGl2IGlkPSJodG1sXzllOTgzMjk2OTU2ZTRkZTA5MjA1ZDkyZTA4YmNlNTZlIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5Db21tZXJjZSBDb3VydCwgVmljdG9yaWEgSG90ZWwgQ2x1c3RlciAwPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF8xMmNjZDE3MTVmZmY0NTI1YWMzYWQzNzhmNDAzM2JmYy5zZXRDb250ZW50KGh0bWxfOWU5ODMyOTY5NTZlNGRlMDkyMDVkOTJlMDhiY2U1NmUpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfM2U4ZDZhYmJjNzg4NDMxZWJjOTkzNTVlN2JhM2UzZjAuYmluZFBvcHVwKHBvcHVwXzEyY2NkMTcxNWZmZjQ1MjVhYzNhZDM3OGY0MDMzYmZjKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzJmZmZiZWMwNGI3NjQwMTZiMDdhYWExNjZjOWMyMzRmID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNzExNjk0OCwtNzkuNDE2OTM1NTk5OTk5OTldLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiIzgwZmZiNCIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiM4MGZmYjQiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfZWMwYjIzNDJkMzg1NDU4MTkxNDQzM2VhOTFhMWNiOTgpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfNzExNGVjYjc3YjVmNDNjZGE5MWUyNTQ4NWI1MzFjMWMgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfNzVhODAwMTQ2NmFmNGU2YzhjNGUzN2MzZTM3ZTZlYjMgPSAkKCc8ZGl2IGlkPSJodG1sXzc1YTgwMDE0NjZhZjRlNmM4YzRlMzdjM2UzN2U2ZWIzIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5Sb3NlbGF3biBDbHVzdGVyIDM8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzcxMTRlY2I3N2I1ZjQzY2RhOTFlMjU0ODViNTMxYzFjLnNldENvbnRlbnQoaHRtbF83NWE4MDAxNDY2YWY0ZTZjOGM0ZTM3YzNlMzdlNmViMyk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl8yZmZmYmVjMDRiNzY0MDE2YjA3YWFhMTY2YzljMjM0Zi5iaW5kUG9wdXAocG9wdXBfNzExNGVjYjc3YjVmNDNjZGE5MWUyNTQ4NWI1MzFjMWMpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfODljMWU5NWE2OWY5NGM1ZDg5M2UzMTA4NzUwZTdjMDUgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42OTY5NDc2LC03OS40MTEzMDcyMDAwMDAwMV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICIjZmYwMDAwIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiI2ZmMDAwMCIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF9lYzBiMjM0MmQzODU0NTgxOTE0NDMzZWE5MWExY2I5OCk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF9kNjExMDRjMmEwNDg0NzBmYjY4NzFjYzQ3ZjY3Nzk4NCA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF9hOTRiMjg3NGE0NTE0ODc2OTE0ZWVmN2IyYjI5MGIzNyA9ICQoJzxkaXYgaWQ9Imh0bWxfYTk0YjI4NzRhNDUxNDg3NjkxNGVlZjdiMmIyOTBiMzciIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkZvcmVzdCBIaWxsIE5vcnRoICZhbXA7IFdlc3QsIEZvcmVzdCBIaWxsIFJvYWQgUGFyayBDbHVzdGVyIDA8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwX2Q2MTEwNGMyYTA0ODQ3MGZiNjg3MWNjNDdmNjc3OTg0LnNldENvbnRlbnQoaHRtbF9hOTRiMjg3NGE0NTE0ODc2OTE0ZWVmN2IyYjI5MGIzNyk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl84OWMxZTk1YTY5Zjk0YzVkODkzZTMxMDg3NTBlN2MwNS5iaW5kUG9wdXAocG9wdXBfZDYxMTA0YzJhMDQ4NDcwZmI2ODcxY2M0N2Y2Nzc5ODQpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfOTlkNzEwZDMwZGFlNGZiOWJjYzNiZjA3ZmMzMGY4N2MgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42NzI3MDk3LC03OS40MDU2Nzg0MDAwMDAwMV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICIjZmYwMDAwIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiI2ZmMDAwMCIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF9lYzBiMjM0MmQzODU0NTgxOTE0NDMzZWE5MWExY2I5OCk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF9mYTY1ZGQzNjE3MTQ0Mzk1YmQ3YjQ3YTk0NmFjMDczMCA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF83ZTExNTZmYWM4MmQ0NzFjOGFlMjQzNzZhYTU0Y2M2ZCA9ICQoJzxkaXYgaWQ9Imh0bWxfN2UxMTU2ZmFjODJkNDcxYzhhZTI0Mzc2YWE1NGNjNmQiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPlRoZSBBbm5leCwgTm9ydGggTWlkdG93biwgWW9ya3ZpbGxlIENsdXN0ZXIgMDwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfZmE2NWRkMzYxNzE0NDM5NWJkN2I0N2E5NDZhYzA3MzAuc2V0Q29udGVudChodG1sXzdlMTE1NmZhYzgyZDQ3MWM4YWUyNDM3NmFhNTRjYzZkKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzk5ZDcxMGQzMGRhZTRmYjliY2MzYmYwN2ZjMzBmODdjLmJpbmRQb3B1cChwb3B1cF9mYTY1ZGQzNjE3MTQ0Mzk1YmQ3YjQ3YTk0NmFjMDczMCk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl9lMWJiODFhODhiODY0YjI5YTc4NTNiNjcyMzMxODRlYyA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjY2MjY5NTYsLTc5LjQwMDA0OTNdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiI2ZmMDAwMCIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiNmZjAwMDAiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfZWMwYjIzNDJkMzg1NDU4MTkxNDQzM2VhOTFhMWNiOTgpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfMDc5ZmJkYzM5NjJmNDE0NzhhNGIyMTlmOGE5ZWUzNGMgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfM2Y4NTQ4NWExZjg3NDVhMGJiODU2YzJmMmExODc5NGEgPSAkKCc8ZGl2IGlkPSJodG1sXzNmODU0ODVhMWY4NzQ1YTBiYjg1NmMyZjJhMTg3OTRhIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5Vbml2ZXJzaXR5IG9mIFRvcm9udG8sIEhhcmJvcmQgQ2x1c3RlciAwPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF8wNzlmYmRjMzk2MmY0MTQ3OGE0YjIxOWY4YTllZTM0Yy5zZXRDb250ZW50KGh0bWxfM2Y4NTQ4NWExZjg3NDVhMGJiODU2YzJmMmExODc5NGEpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfZTFiYjgxYTg4Yjg2NGIyOWE3ODUzYjY3MjMzMTg0ZWMuYmluZFBvcHVwKHBvcHVwXzA3OWZiZGMzOTYyZjQxNDc4YTRiMjE5ZjhhOWVlMzRjKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyX2I0ZmNiODk2ZmRiZjQyYTlhNGY2N2ExNjVhNjIyZDE3ID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNjUzMjA1NywtNzkuNDAwMDQ5M10sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICIjZmYwMDAwIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiI2ZmMDAwMCIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF9lYzBiMjM0MmQzODU0NTgxOTE0NDMzZWE5MWExY2I5OCk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF9jMzU3NDU0MDI1ZGQ0NmRlOTI2M2M2YzY3MTEzODI5YiA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF8zZTYyMDYxODg4NmM0MTU5YjY0NzY1M2MyZjQ5OWRkMCA9ICQoJzxkaXYgaWQ9Imh0bWxfM2U2MjA2MTg4ODZjNDE1OWI2NDc2NTNjMmY0OTlkZDAiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPktlbnNpbmd0b24gTWFya2V0LCBDaGluYXRvd24sIEdyYW5nZSBQYXJrIENsdXN0ZXIgMDwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfYzM1NzQ1NDAyNWRkNDZkZTkyNjNjNmM2NzExMzgyOWIuc2V0Q29udGVudChodG1sXzNlNjIwNjE4ODg2YzQxNTliNjQ3NjUzYzJmNDk5ZGQwKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyX2I0ZmNiODk2ZmRiZjQyYTlhNGY2N2ExNjVhNjIyZDE3LmJpbmRQb3B1cChwb3B1cF9jMzU3NDU0MDI1ZGQ0NmRlOTI2M2M2YzY3MTEzODI5Yik7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl9kYmMzMzhiNWRkOTQ0MTRlYjc0OTAxNDM1MjljMzY3OCA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjYyODk0NjcsLTc5LjM5NDQxOTldLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiI2ZmMDAwMCIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiNmZjAwMDAiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfZWMwYjIzNDJkMzg1NDU4MTkxNDQzM2VhOTFhMWNiOTgpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfYWNlODE0MWE2NTUzNDMwZTkzYTNiYzAzODY3NDVlZDIgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfMGIwY2E4MzQ2M2JjNDE4N2JkYzI2MDNlZDNjMTBmN2IgPSAkKCc8ZGl2IGlkPSJodG1sXzBiMGNhODM0NjNiYzQxODdiZGMyNjAzZWQzYzEwZjdiIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5DTiBUb3dlciwgS2luZyBhbmQgU3BhZGluYSwgUmFpbHdheSBMYW5kcywgSGFyYm91cmZyb250IFdlc3QsIEJhdGh1cnN0IFF1YXksIFNvdXRoIE5pYWdhcmEsIElzbGFuZCBhaXJwb3J0IENsdXN0ZXIgMDwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfYWNlODE0MWE2NTUzNDMwZTkzYTNiYzAzODY3NDVlZDIuc2V0Q29udGVudChodG1sXzBiMGNhODM0NjNiYzQxODdiZGMyNjAzZWQzYzEwZjdiKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyX2RiYzMzOGI1ZGQ5NDQxNGViNzQ5MDE0MzUyOWMzNjc4LmJpbmRQb3B1cChwb3B1cF9hY2U4MTQxYTY1NTM0MzBlOTNhM2JjMDM4Njc0NWVkMik7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl83N2QzZTEwMmY4NzU0NzdmYjFhNjc4ZDRjZGVlODg2OSA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjY0NjQzNTIsLTc5LjM3NDg0NTk5OTk5OTk5XSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogIiNmZjAwMDAiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjZmYwMDAwIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwX2VjMGIyMzQyZDM4NTQ1ODE5MTQ0MzNlYTkxYTFjYjk4KTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwX2UxMDg4NDhkN2M1YjRiZWFhN2MxZTQ1N2M1MjE0NTliID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzYxYmFkMWYxZTgwNDQ0OWZiYzk0YjU5NDAxYTI3NGZiID0gJCgnPGRpdiBpZD0iaHRtbF82MWJhZDFmMWU4MDQ0NDlmYmM5NGI1OTQwMWEyNzRmYiIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+U3RuIEEgUE8gQm94ZXMgQ2x1c3RlciAwPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF9lMTA4ODQ4ZDdjNWI0YmVhYTdjMWU0NTdjNTIxNDU5Yi5zZXRDb250ZW50KGh0bWxfNjFiYWQxZjFlODA0NDQ5ZmJjOTRiNTk0MDFhMjc0ZmIpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfNzdkM2UxMDJmODc1NDc3ZmIxYTY3OGQ0Y2RlZTg4NjkuYmluZFBvcHVwKHBvcHVwX2UxMDg4NDhkN2M1YjRiZWFhN2MxZTQ1N2M1MjE0NTliKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzY5ZjNmMWFhODdmOTQzNWNiZjE4Njk2NGEwMGE3Yzg4ID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNjQ4NDI5MiwtNzkuMzgyMjgwMl0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICIjZmYwMDAwIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiI2ZmMDAwMCIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF9lYzBiMjM0MmQzODU0NTgxOTE0NDMzZWE5MWExY2I5OCk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF83ZjFjYjM2YzU2YWY0MGZhYjFiODhmYjAyZTZkZTY0NCA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF80ZGViMzU4YzgwMDA0YzBmOGY2NmQ2MGFiMTZlNGU4MyA9ICQoJzxkaXYgaWQ9Imh0bWxfNGRlYjM1OGM4MDAwNGMwZjhmNjZkNjBhYjE2ZTRlODMiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkZpcnN0IENhbmFkaWFuIFBsYWNlLCBVbmRlcmdyb3VuZCBjaXR5IENsdXN0ZXIgMDwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfN2YxY2IzNmM1NmFmNDBmYWIxYjg4ZmIwMmU2ZGU2NDQuc2V0Q29udGVudChodG1sXzRkZWIzNThjODAwMDRjMGY4ZjY2ZDYwYWIxNmU0ZTgzKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzY5ZjNmMWFhODdmOTQzNWNiZjE4Njk2NGEwMGE3Yzg4LmJpbmRQb3B1cChwb3B1cF83ZjFjYjM2YzU2YWY0MGZhYjFiODhmYjAyZTZkZTY0NCk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl9iZmNmZGE3MTgzYjA0MzMxOWJkMmVjMGFiZjczMDQ5MiA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjY2OTU0MiwtNzkuNDIyNTYzN10sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICIjZmYwMDAwIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiI2ZmMDAwMCIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF9lYzBiMjM0MmQzODU0NTgxOTE0NDMzZWE5MWExY2I5OCk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF8wMTQzNDNjMGZlOTc0MzM5OTE4NDVlYzVlZWRiNzQwMSA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF8zN2Y3MWNmM2IzNTA0OTVlYjM3Y2Y3NTA1YzQzMmY2MiA9ICQoJzxkaXYgaWQ9Imh0bWxfMzdmNzFjZjNiMzUwNDk1ZWIzN2NmNzUwNWM0MzJmNjIiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkNocmlzdGllIENsdXN0ZXIgMDwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfMDE0MzQzYzBmZTk3NDMzOTkxODQ1ZWM1ZWVkYjc0MDEuc2V0Q29udGVudChodG1sXzM3ZjcxY2YzYjM1MDQ5NWViMzdjZjc1MDVjNDMyZjYyKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyX2JmY2ZkYTcxODNiMDQzMzE5YmQyZWMwYWJmNzMwNDkyLmJpbmRQb3B1cChwb3B1cF8wMTQzNDNjMGZlOTc0MzM5OTE4NDVlYzVlZWRiNzQwMSk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl84NmM0Zjk2ODJkOGQ0ZmY0ODg3ODViMjY4MmEyZDU3MyA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjY2OTAwNTEwMDAwMDAxLC03OS40NDIyNTkzXSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogIiNmZjAwMDAiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjZmYwMDAwIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwX2VjMGIyMzQyZDM4NTQ1ODE5MTQ0MzNlYTkxYTFjYjk4KTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzZkYTE3NDhlOTVkMDQxNDRhODQ1MmRlMmU4YjAwYzU5ID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzM4ZDM5NmNmMGI3MzQ5MTI5Y2Y0ODU4MTFiOGEyMzY0ID0gJCgnPGRpdiBpZD0iaHRtbF8zOGQzOTZjZjBiNzM0OTEyOWNmNDg1ODExYjhhMjM2NCIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+RHVmZmVyaW4sIERvdmVyY291cnQgVmlsbGFnZSBDbHVzdGVyIDA8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzZkYTE3NDhlOTVkMDQxNDRhODQ1MmRlMmU4YjAwYzU5LnNldENvbnRlbnQoaHRtbF8zOGQzOTZjZjBiNzM0OTEyOWNmNDg1ODExYjhhMjM2NCk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl84NmM0Zjk2ODJkOGQ0ZmY0ODg3ODViMjY4MmEyZDU3My5iaW5kUG9wdXAocG9wdXBfNmRhMTc0OGU5NWQwNDE0NGE4NDUyZGUyZThiMDBjNTkpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfODg2MjY1NTdiMTdkNDQ0NWIyNGEzMTZmNTM0MmNjMjcgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42NDc5MjY3MDAwMDAwMDYsLTc5LjQxOTc0OTddLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiI2ZmMDAwMCIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiNmZjAwMDAiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfZWMwYjIzNDJkMzg1NDU4MTkxNDQzM2VhOTFhMWNiOTgpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfNzZmZGFlMWMwNzRiNGMxZGI5N2NhYWRjMmFhYzUyNWQgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfZmRlNWI1MWIyMmM1NDIyYjk1ZWFmNDk2Mjc4NTY5MjMgPSAkKCc8ZGl2IGlkPSJodG1sX2ZkZTViNTFiMjJjNTQyMmI5NWVhZjQ5NjI3ODU2OTIzIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5MaXR0bGUgUG9ydHVnYWwsIFRyaW5pdHkgQ2x1c3RlciAwPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF83NmZkYWUxYzA3NGI0YzFkYjk3Y2FhZGMyYWFjNTI1ZC5zZXRDb250ZW50KGh0bWxfZmRlNWI1MWIyMmM1NDIyYjk1ZWFmNDk2Mjc4NTY5MjMpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfODg2MjY1NTdiMTdkNDQ0NWIyNGEzMTZmNTM0MmNjMjcuYmluZFBvcHVwKHBvcHVwXzc2ZmRhZTFjMDc0YjRjMWRiOTdjYWFkYzJhYWM1MjVkKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzEzOTA3Yjk2MDk5NzRiNTNhY2M3Y2U4MjVjMzBhNjBjID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNjM2ODQ3MiwtNzkuNDI4MTkxNDAwMDAwMDJdLAogICAgICAgICAgICAgICAgewogICJidWJibGluZ01vdXNlRXZlbnRzIjogdHJ1ZSwKICAiY29sb3IiOiAiI2ZmMDAwMCIsCiAgImRhc2hBcnJheSI6IG51bGwsCiAgImRhc2hPZmZzZXQiOiBudWxsLAogICJmaWxsIjogdHJ1ZSwKICAiZmlsbENvbG9yIjogIiNmZjAwMDAiLAogICJmaWxsT3BhY2l0eSI6IDAuNywKICAiZmlsbFJ1bGUiOiAiZXZlbm9kZCIsCiAgImxpbmVDYXAiOiAicm91bmQiLAogICJsaW5lSm9pbiI6ICJyb3VuZCIsCiAgIm9wYWNpdHkiOiAxLjAsCiAgInJhZGl1cyI6IDUsCiAgInN0cm9rZSI6IHRydWUsCiAgIndlaWdodCI6IDMKfQogICAgICAgICAgICAgICAgKS5hZGRUbyhtYXBfZWMwYjIzNDJkMzg1NDU4MTkxNDQzM2VhOTFhMWNiOTgpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfZjdhZmExZmQ1ZjQ4NDVmMzkxYTZhNmNiZDdhYWJiNmUgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfODNiOWFjNzM4MTQ1NDM5YzlkNWY1NDY5ZjA4M2FjZWMgPSAkKCc8ZGl2IGlkPSJodG1sXzgzYjlhYzczODE0NTQzOWM5ZDVmNTQ2OWYwODNhY2VjIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5Ccm9ja3RvbiwgUGFya2RhbGUgVmlsbGFnZSwgRXhoaWJpdGlvbiBQbGFjZSBDbHVzdGVyIDA8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwX2Y3YWZhMWZkNWY0ODQ1ZjM5MWE2YTZjYmQ3YWFiYjZlLnNldENvbnRlbnQoaHRtbF84M2I5YWM3MzgxNDU0MzljOWQ1ZjU0NjlmMDgzYWNlYyk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl8xMzkwN2I5NjA5OTc0YjUzYWNjN2NlODI1YzMwYTYwYy5iaW5kUG9wdXAocG9wdXBfZjdhZmExZmQ1ZjQ4NDVmMzkxYTZhNmNiZDdhYWJiNmUpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfMzI3MWQwMzFkOWUzNDAxOGIwNmFlN2YyOTJmY2M5OTEgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42NjE2MDgzLC03OS40NjQ3NjMyOTk5OTk5OV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICIjZmYwMDAwIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiI2ZmMDAwMCIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF9lYzBiMjM0MmQzODU0NTgxOTE0NDMzZWE5MWExY2I5OCk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF82OTJlMWY2YjZlNWE0ZGUwOGMwMmUwZDBjOTNmZmJkOCA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF83ZDVlMjFiZDkwMTE0NjFjYTZiMDRmNzRhYjJjNWU0NiA9ICQoJzxkaXYgaWQ9Imh0bWxfN2Q1ZTIxYmQ5MDExNDYxY2E2YjA0Zjc0YWIyYzVlNDYiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkhpZ2ggUGFyaywgVGhlIEp1bmN0aW9uIFNvdXRoIENsdXN0ZXIgMDwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfNjkyZTFmNmI2ZTVhNGRlMDhjMDJlMGQwYzkzZmZiZDguc2V0Q29udGVudChodG1sXzdkNWUyMWJkOTAxMTQ2MWNhNmIwNGY3NGFiMmM1ZTQ2KTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyXzMyNzFkMDMxZDllMzQwMThiMDZhZTdmMjkyZmNjOTkxLmJpbmRQb3B1cChwb3B1cF82OTJlMWY2YjZlNWE0ZGUwOGMwMmUwZDBjOTNmZmJkOCk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgY2lyY2xlX21hcmtlcl8wNzIzZGE5NjMzYjQ0OWU4YjNmMGZjZjZkY2I2M2ExNCA9IEwuY2lyY2xlTWFya2VyKAogICAgICAgICAgICAgICAgWzQzLjY0ODk1OTcsLTc5LjQ1NjMyNV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICIjZmYwMDAwIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiI2ZmMDAwMCIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF9lYzBiMjM0MmQzODU0NTgxOTE0NDMzZWE5MWExY2I5OCk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF9mMTA5MGE5ODEzNzY0ZjViOGI4MzI5MjhiZmQxN2FjNiA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF8xY2EyMDM4NjgyNjM0ZGEwODM1MjhjNGI5Nzg5ZDFlZSA9ICQoJzxkaXYgaWQ9Imh0bWxfMWNhMjAzODY4MjYzNGRhMDgzNTI4YzRiOTc4OWQxZWUiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPlBhcmtkYWxlLCBSb25jZXN2YWxsZXMgQ2x1c3RlciAwPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF9mMTA5MGE5ODEzNzY0ZjViOGI4MzI5MjhiZmQxN2FjNi5zZXRDb250ZW50KGh0bWxfMWNhMjAzODY4MjYzNGRhMDgzNTI4YzRiOTc4OWQxZWUpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfMDcyM2RhOTYzM2I0NDllOGIzZjBmY2Y2ZGNiNjNhMTQuYmluZFBvcHVwKHBvcHVwX2YxMDkwYTk4MTM3NjRmNWI4YjgzMjkyOGJmZDE3YWM2KTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyXzJlMGU1ZDI1NGYyMTRhODdhMDE3MmY5Zjc1MTM4NDU3ID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNjUxNTcwNiwtNzkuNDg0NDQ5OV0sCiAgICAgICAgICAgICAgICB7CiAgImJ1YmJsaW5nTW91c2VFdmVudHMiOiB0cnVlLAogICJjb2xvciI6ICIjZmYwMDAwIiwKICAiZGFzaEFycmF5IjogbnVsbCwKICAiZGFzaE9mZnNldCI6IG51bGwsCiAgImZpbGwiOiB0cnVlLAogICJmaWxsQ29sb3IiOiAiI2ZmMDAwMCIsCiAgImZpbGxPcGFjaXR5IjogMC43LAogICJmaWxsUnVsZSI6ICJldmVub2RkIiwKICAibGluZUNhcCI6ICJyb3VuZCIsCiAgImxpbmVKb2luIjogInJvdW5kIiwKICAib3BhY2l0eSI6IDEuMCwKICAicmFkaXVzIjogNSwKICAic3Ryb2tlIjogdHJ1ZSwKICAid2VpZ2h0IjogMwp9CiAgICAgICAgICAgICAgICApLmFkZFRvKG1hcF9lYzBiMjM0MmQzODU0NTgxOTE0NDMzZWE5MWExY2I5OCk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF84ZDg4MDY3ZWE4Yzk0MTk5YmFiMzgyYjQxMmQxNDRhYyA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF84M2EzYmY1ZWI3MzI0YzViYWFjZTM2MGY5MjQ0OTgxNCA9ICQoJzxkaXYgaWQ9Imh0bWxfODNhM2JmNWViNzMyNGM1YmFhY2UzNjBmOTI0NDk4MTQiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPlJ1bm55bWVkZSwgU3dhbnNlYSBDbHVzdGVyIDA8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzhkODgwNjdlYThjOTQxOTliYWIzODJiNDEyZDE0NGFjLnNldENvbnRlbnQoaHRtbF84M2EzYmY1ZWI3MzI0YzViYWFjZTM2MGY5MjQ0OTgxNCk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgY2lyY2xlX21hcmtlcl8yZTBlNWQyNTRmMjE0YTg3YTAxNzJmOWY3NTEzODQ1Ny5iaW5kUG9wdXAocG9wdXBfOGQ4ODA2N2VhOGM5NDE5OWJhYjM4MmI0MTJkMTQ0YWMpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIGNpcmNsZV9tYXJrZXJfNjIwN2JkNjRhNDExNGI1NjljMGVkMzFhM2Q0ZDM2ZDMgPSBMLmNpcmNsZU1hcmtlcigKICAgICAgICAgICAgICAgIFs0My42NjIzMDE1LC03OS4zODk0OTM4XSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogIiNmZjAwMDAiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjZmYwMDAwIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwX2VjMGIyMzQyZDM4NTQ1ODE5MTQ0MzNlYTkxYTFjYjk4KTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwX2MxMzA2YzY5MThlOTRmMTQ5NGYwNzcwNjQ2YTgwNzM5ID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzVjYjg2YTQ4MzBjMjQ5MWI5NWUwM2FmZDcxYjI1ZWYwID0gJCgnPGRpdiBpZD0iaHRtbF81Y2I4NmE0ODMwYzI0OTFiOTVlMDNhZmQ3MWIyNWVmMCIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+UXVlZW4mIzM5O3MgUGFyaywgT250YXJpbyBQcm92aW5jaWFsIEdvdmVybm1lbnQgQ2x1c3RlciAwPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF9jMTMwNmM2OTE4ZTk0ZjE0OTRmMDc3MDY0NmE4MDczOS5zZXRDb250ZW50KGh0bWxfNWNiODZhNDgzMGMyNDkxYjk1ZTAzYWZkNzFiMjVlZjApOwogICAgICAgICAgICAKCiAgICAgICAgICAgIGNpcmNsZV9tYXJrZXJfNjIwN2JkNjRhNDExNGI1NjljMGVkMzFhM2Q0ZDM2ZDMuYmluZFBvcHVwKHBvcHVwX2MxMzA2YzY5MThlOTRmMTQ5NGYwNzcwNjQ2YTgwNzM5KTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBjaXJjbGVfbWFya2VyX2U4YjVhZmQ4OTE1YTQxNWY5OWFmNjJmZGEzYzE2NTA3ID0gTC5jaXJjbGVNYXJrZXIoCiAgICAgICAgICAgICAgICBbNDMuNjYyNzQzOSwtNzkuMzIxNTU4XSwKICAgICAgICAgICAgICAgIHsKICAiYnViYmxpbmdNb3VzZUV2ZW50cyI6IHRydWUsCiAgImNvbG9yIjogIiNmZjAwMDAiLAogICJkYXNoQXJyYXkiOiBudWxsLAogICJkYXNoT2Zmc2V0IjogbnVsbCwKICAiZmlsbCI6IHRydWUsCiAgImZpbGxDb2xvciI6ICIjZmYwMDAwIiwKICAiZmlsbE9wYWNpdHkiOiAwLjcsCiAgImZpbGxSdWxlIjogImV2ZW5vZGQiLAogICJsaW5lQ2FwIjogInJvdW5kIiwKICAibGluZUpvaW4iOiAicm91bmQiLAogICJvcGFjaXR5IjogMS4wLAogICJyYWRpdXMiOiA1LAogICJzdHJva2UiOiB0cnVlLAogICJ3ZWlnaHQiOiAzCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwX2VjMGIyMzQyZDM4NTQ1ODE5MTQ0MzNlYTkxYTFjYjk4KTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzgxMDIwOWNjOGVmZDRkMDZhYmY5MTBiMDgyNDI2NDMwID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzBkY2JkMWRhYzlmMDQwYWE4MzIyN2MxODU5ZTA0OGM2ID0gJCgnPGRpdiBpZD0iaHRtbF8wZGNiZDFkYWM5ZjA0MGFhODMyMjdjMTg1OWUwNDhjNiIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+QnVzaW5lc3MgcmVwbHkgbWFpbCBQcm9jZXNzaW5nIENlbnRyZSwgU291dGggQ2VudHJhbCBMZXR0ZXIgUHJvY2Vzc2luZyBQbGFudCBUb3JvbnRvIENsdXN0ZXIgMDwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfODEwMjA5Y2M4ZWZkNGQwNmFiZjkxMGIwODI0MjY0MzAuc2V0Q29udGVudChodG1sXzBkY2JkMWRhYzlmMDQwYWE4MzIyN2MxODU5ZTA0OGM2KTsKICAgICAgICAgICAgCgogICAgICAgICAgICBjaXJjbGVfbWFya2VyX2U4YjVhZmQ4OTE1YTQxNWY5OWFmNjJmZGEzYzE2NTA3LmJpbmRQb3B1cChwb3B1cF84MTAyMDljYzhlZmQ0ZDA2YWJmOTEwYjA4MjQyNjQzMCk7CgogICAgICAgICAgICAKICAgICAgICAKPC9zY3JpcHQ+ onload="this.contentDocument.open();this.contentDocument.write(atob(this.getAttribute('data-html')));this.contentDocument.close();" allowfullscreen webkitallowfullscreen mozallowfullscreen></iframe></div></div>



## Examining Clusters

__Note that K-means clustering resulting in most neighborhoods in cluster 0, while clusters 1, 2, 3 and 4 containing only 1 neighborhood each__

__Cluster 0__


```python
Toronto_merged.loc[Toronto_merged['Cluster Labels'] == 0, Toronto_merged.columns[[1] + list(range(5, Toronto_merged.shape[1]))]]
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
      <th>Borough</th>
      <th>Cluster Labels</th>
      <th>1st Most Common Venue</th>
      <th>2nd Most Common Venue</th>
      <th>3rd Most Common Venue</th>
      <th>4th Most Common Venue</th>
      <th>5th Most Common Venue</th>
      <th>6th Most Common Venue</th>
      <th>7th Most Common Venue</th>
      <th>8th Most Common Venue</th>
      <th>9th Most Common Venue</th>
      <th>10th Most Common Venue</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>East Toronto</td>
      <td>0</td>
      <td>Pub</td>
      <td>Health Food Store</td>
      <td>Coffee Shop</td>
      <td>Trail</td>
      <td>Yoga Studio</td>
      <td>Discount Store</td>
      <td>Distribution Center</td>
      <td>Dog Run</td>
      <td>Doner Restaurant</td>
      <td>Donut Shop</td>
    </tr>
    <tr>
      <th>1</th>
      <td>East Toronto</td>
      <td>0</td>
      <td>Greek Restaurant</td>
      <td>Italian Restaurant</td>
      <td>Coffee Shop</td>
      <td>Furniture / Home Store</td>
      <td>Restaurant</td>
      <td>Ice Cream Shop</td>
      <td>Yoga Studio</td>
      <td>Liquor Store</td>
      <td>Spa</td>
      <td>Juice Bar</td>
    </tr>
    <tr>
      <th>2</th>
      <td>East Toronto</td>
      <td>0</td>
      <td>Park</td>
      <td>Sandwich Place</td>
      <td>Fast Food Restaurant</td>
      <td>Pet Store</td>
      <td>Pub</td>
      <td>Burrito Place</td>
      <td>Board Shop</td>
      <td>Italian Restaurant</td>
      <td>Fish &amp; Chips Shop</td>
      <td>Steakhouse</td>
    </tr>
    <tr>
      <th>3</th>
      <td>East Toronto</td>
      <td>0</td>
      <td>Café</td>
      <td>Coffee Shop</td>
      <td>Brewery</td>
      <td>Gastropub</td>
      <td>Bakery</td>
      <td>American Restaurant</td>
      <td>Yoga Studio</td>
      <td>Comfort Food Restaurant</td>
      <td>Seafood Restaurant</td>
      <td>Sandwich Place</td>
    </tr>
    <tr>
      <th>5</th>
      <td>Central Toronto</td>
      <td>0</td>
      <td>Gym</td>
      <td>Hotel</td>
      <td>Pizza Place</td>
      <td>Department Store</td>
      <td>Sandwich Place</td>
      <td>Breakfast Spot</td>
      <td>Food &amp; Drink Shop</td>
      <td>Park</td>
      <td>Gym / Fitness Center</td>
      <td>General Travel</td>
    </tr>
    <tr>
      <th>6</th>
      <td>Central Toronto</td>
      <td>0</td>
      <td>Clothing Store</td>
      <td>Coffee Shop</td>
      <td>Yoga Studio</td>
      <td>Sporting Goods Shop</td>
      <td>Salon / Barbershop</td>
      <td>Café</td>
      <td>Restaurant</td>
      <td>Rental Car Location</td>
      <td>Chinese Restaurant</td>
      <td>Park</td>
    </tr>
    <tr>
      <th>7</th>
      <td>Central Toronto</td>
      <td>0</td>
      <td>Pizza Place</td>
      <td>Sandwich Place</td>
      <td>Dessert Shop</td>
      <td>Sushi Restaurant</td>
      <td>Gym</td>
      <td>Italian Restaurant</td>
      <td>Café</td>
      <td>Coffee Shop</td>
      <td>Pharmacy</td>
      <td>Seafood Restaurant</td>
    </tr>
    <tr>
      <th>9</th>
      <td>Central Toronto</td>
      <td>0</td>
      <td>Coffee Shop</td>
      <td>Pub</td>
      <td>Bank</td>
      <td>Restaurant</td>
      <td>Liquor Store</td>
      <td>Supermarket</td>
      <td>Vietnamese Restaurant</td>
      <td>Sushi Restaurant</td>
      <td>Pizza Place</td>
      <td>American Restaurant</td>
    </tr>
    <tr>
      <th>11</th>
      <td>Downtown Toronto</td>
      <td>0</td>
      <td>Coffee Shop</td>
      <td>Italian Restaurant</td>
      <td>Pizza Place</td>
      <td>Pub</td>
      <td>Café</td>
      <td>Bakery</td>
      <td>Restaurant</td>
      <td>Chinese Restaurant</td>
      <td>Sandwich Place</td>
      <td>Playground</td>
    </tr>
    <tr>
      <th>12</th>
      <td>Downtown Toronto</td>
      <td>0</td>
      <td>Coffee Shop</td>
      <td>Japanese Restaurant</td>
      <td>Sushi Restaurant</td>
      <td>Restaurant</td>
      <td>Gay Bar</td>
      <td>Café</td>
      <td>Pub</td>
      <td>Men's Store</td>
      <td>Mediterranean Restaurant</td>
      <td>Hotel</td>
    </tr>
    <tr>
      <th>13</th>
      <td>Downtown Toronto</td>
      <td>0</td>
      <td>Coffee Shop</td>
      <td>Park</td>
      <td>Bakery</td>
      <td>Pub</td>
      <td>Theater</td>
      <td>Breakfast Spot</td>
      <td>Café</td>
      <td>Yoga Studio</td>
      <td>Mexican Restaurant</td>
      <td>French Restaurant</td>
    </tr>
    <tr>
      <th>14</th>
      <td>Downtown Toronto</td>
      <td>0</td>
      <td>Clothing Store</td>
      <td>Coffee Shop</td>
      <td>Middle Eastern Restaurant</td>
      <td>Japanese Restaurant</td>
      <td>Italian Restaurant</td>
      <td>Cosmetics Shop</td>
      <td>Café</td>
      <td>Bubble Tea Shop</td>
      <td>Bookstore</td>
      <td>Tea Room</td>
    </tr>
    <tr>
      <th>15</th>
      <td>Downtown Toronto</td>
      <td>0</td>
      <td>Coffee Shop</td>
      <td>Café</td>
      <td>Cocktail Bar</td>
      <td>Gastropub</td>
      <td>American Restaurant</td>
      <td>Restaurant</td>
      <td>Beer Bar</td>
      <td>Cosmetics Shop</td>
      <td>Moroccan Restaurant</td>
      <td>Department Store</td>
    </tr>
    <tr>
      <th>16</th>
      <td>Downtown Toronto</td>
      <td>0</td>
      <td>Coffee Shop</td>
      <td>Cocktail Bar</td>
      <td>Pub</td>
      <td>Café</td>
      <td>Bakery</td>
      <td>Restaurant</td>
      <td>Cheese Shop</td>
      <td>Beer Bar</td>
      <td>Seafood Restaurant</td>
      <td>Bistro</td>
    </tr>
    <tr>
      <th>17</th>
      <td>Downtown Toronto</td>
      <td>0</td>
      <td>Coffee Shop</td>
      <td>Italian Restaurant</td>
      <td>Café</td>
      <td>Sandwich Place</td>
      <td>Burger Joint</td>
      <td>Japanese Restaurant</td>
      <td>Department Store</td>
      <td>Salad Place</td>
      <td>Bubble Tea Shop</td>
      <td>Ice Cream Shop</td>
    </tr>
    <tr>
      <th>18</th>
      <td>Downtown Toronto</td>
      <td>0</td>
      <td>Coffee Shop</td>
      <td>Café</td>
      <td>Restaurant</td>
      <td>Clothing Store</td>
      <td>Hotel</td>
      <td>Gym</td>
      <td>Deli / Bodega</td>
      <td>Thai Restaurant</td>
      <td>Pizza Place</td>
      <td>Cosmetics Shop</td>
    </tr>
    <tr>
      <th>19</th>
      <td>Downtown Toronto</td>
      <td>0</td>
      <td>Coffee Shop</td>
      <td>Aquarium</td>
      <td>Café</td>
      <td>Hotel</td>
      <td>Italian Restaurant</td>
      <td>Fried Chicken Joint</td>
      <td>Scenic Lookout</td>
      <td>Restaurant</td>
      <td>Brewery</td>
      <td>Sporting Goods Shop</td>
    </tr>
    <tr>
      <th>20</th>
      <td>Downtown Toronto</td>
      <td>0</td>
      <td>Coffee Shop</td>
      <td>Café</td>
      <td>Hotel</td>
      <td>Restaurant</td>
      <td>American Restaurant</td>
      <td>Seafood Restaurant</td>
      <td>Salad Place</td>
      <td>Italian Restaurant</td>
      <td>Japanese Restaurant</td>
      <td>Deli / Bodega</td>
    </tr>
    <tr>
      <th>21</th>
      <td>Downtown Toronto</td>
      <td>0</td>
      <td>Coffee Shop</td>
      <td>Restaurant</td>
      <td>Café</td>
      <td>Hotel</td>
      <td>American Restaurant</td>
      <td>Gym</td>
      <td>Seafood Restaurant</td>
      <td>Japanese Restaurant</td>
      <td>Italian Restaurant</td>
      <td>Deli / Bodega</td>
    </tr>
    <tr>
      <th>23</th>
      <td>Central Toronto</td>
      <td>0</td>
      <td>Jewelry Store</td>
      <td>Trail</td>
      <td>Mexican Restaurant</td>
      <td>Sushi Restaurant</td>
      <td>Yoga Studio</td>
      <td>Discount Store</td>
      <td>Falafel Restaurant</td>
      <td>Event Space</td>
      <td>Ethiopian Restaurant</td>
      <td>Electronics Store</td>
    </tr>
    <tr>
      <th>24</th>
      <td>Central Toronto</td>
      <td>0</td>
      <td>Café</td>
      <td>Sandwich Place</td>
      <td>Coffee Shop</td>
      <td>BBQ Joint</td>
      <td>Liquor Store</td>
      <td>Pub</td>
      <td>Middle Eastern Restaurant</td>
      <td>History Museum</td>
      <td>Burger Joint</td>
      <td>Pizza Place</td>
    </tr>
    <tr>
      <th>25</th>
      <td>Downtown Toronto</td>
      <td>0</td>
      <td>Café</td>
      <td>Bakery</td>
      <td>Restaurant</td>
      <td>Italian Restaurant</td>
      <td>Japanese Restaurant</td>
      <td>Bar</td>
      <td>Bookstore</td>
      <td>Bank</td>
      <td>Beer Bar</td>
      <td>Beer Store</td>
    </tr>
    <tr>
      <th>26</th>
      <td>Downtown Toronto</td>
      <td>0</td>
      <td>Café</td>
      <td>Coffee Shop</td>
      <td>Mexican Restaurant</td>
      <td>Vietnamese Restaurant</td>
      <td>Bakery</td>
      <td>Bar</td>
      <td>Vegetarian / Vegan Restaurant</td>
      <td>Grocery Store</td>
      <td>Dessert Shop</td>
      <td>Gaming Cafe</td>
    </tr>
    <tr>
      <th>27</th>
      <td>Downtown Toronto</td>
      <td>0</td>
      <td>Airport Service</td>
      <td>Harbor / Marina</td>
      <td>Bar</td>
      <td>Plane</td>
      <td>Coffee Shop</td>
      <td>Rental Car Location</td>
      <td>Sculpture Garden</td>
      <td>Boat or Ferry</td>
      <td>Boutique</td>
      <td>Airport Lounge</td>
    </tr>
    <tr>
      <th>28</th>
      <td>Downtown Toronto</td>
      <td>0</td>
      <td>Coffee Shop</td>
      <td>Café</td>
      <td>Restaurant</td>
      <td>Pub</td>
      <td>Seafood Restaurant</td>
      <td>Japanese Restaurant</td>
      <td>Italian Restaurant</td>
      <td>Beer Bar</td>
      <td>Cocktail Bar</td>
      <td>Art Gallery</td>
    </tr>
    <tr>
      <th>29</th>
      <td>Downtown Toronto</td>
      <td>0</td>
      <td>Coffee Shop</td>
      <td>Café</td>
      <td>Japanese Restaurant</td>
      <td>Gym</td>
      <td>Hotel</td>
      <td>Restaurant</td>
      <td>Deli / Bodega</td>
      <td>Asian Restaurant</td>
      <td>Seafood Restaurant</td>
      <td>American Restaurant</td>
    </tr>
    <tr>
      <th>30</th>
      <td>Downtown Toronto</td>
      <td>0</td>
      <td>Grocery Store</td>
      <td>Café</td>
      <td>Park</td>
      <td>Restaurant</td>
      <td>Italian Restaurant</td>
      <td>Baby Store</td>
      <td>Diner</td>
      <td>Coffee Shop</td>
      <td>Candy Store</td>
      <td>Nightclub</td>
    </tr>
    <tr>
      <th>31</th>
      <td>West Toronto</td>
      <td>0</td>
      <td>Pharmacy</td>
      <td>Bakery</td>
      <td>Grocery Store</td>
      <td>Music Venue</td>
      <td>Pool</td>
      <td>Middle Eastern Restaurant</td>
      <td>Café</td>
      <td>Brewery</td>
      <td>Supermarket</td>
      <td>Bar</td>
    </tr>
    <tr>
      <th>32</th>
      <td>West Toronto</td>
      <td>0</td>
      <td>Bar</td>
      <td>Restaurant</td>
      <td>Asian Restaurant</td>
      <td>Men's Store</td>
      <td>Café</td>
      <td>Vegetarian / Vegan Restaurant</td>
      <td>Coffee Shop</td>
      <td>Miscellaneous Shop</td>
      <td>Record Shop</td>
      <td>Pizza Place</td>
    </tr>
    <tr>
      <th>33</th>
      <td>West Toronto</td>
      <td>0</td>
      <td>Café</td>
      <td>Coffee Shop</td>
      <td>Breakfast Spot</td>
      <td>Grocery Store</td>
      <td>Bakery</td>
      <td>Performing Arts Venue</td>
      <td>Pet Store</td>
      <td>Nightclub</td>
      <td>Climbing Gym</td>
      <td>Restaurant</td>
    </tr>
    <tr>
      <th>34</th>
      <td>West Toronto</td>
      <td>0</td>
      <td>Café</td>
      <td>Mexican Restaurant</td>
      <td>Thai Restaurant</td>
      <td>Grocery Store</td>
      <td>Furniture / Home Store</td>
      <td>Fast Food Restaurant</td>
      <td>Bookstore</td>
      <td>Flea Market</td>
      <td>Speakeasy</td>
      <td>Cajun / Creole Restaurant</td>
    </tr>
    <tr>
      <th>35</th>
      <td>West Toronto</td>
      <td>0</td>
      <td>Breakfast Spot</td>
      <td>Gift Shop</td>
      <td>Dessert Shop</td>
      <td>Movie Theater</td>
      <td>Eastern European Restaurant</td>
      <td>Dog Run</td>
      <td>Bar</td>
      <td>Italian Restaurant</td>
      <td>Restaurant</td>
      <td>Bookstore</td>
    </tr>
    <tr>
      <th>36</th>
      <td>West Toronto</td>
      <td>0</td>
      <td>Café</td>
      <td>Coffee Shop</td>
      <td>Diner</td>
      <td>Pizza Place</td>
      <td>Pub</td>
      <td>Tea Room</td>
      <td>Sushi Restaurant</td>
      <td>Italian Restaurant</td>
      <td>Bank</td>
      <td>Bar</td>
    </tr>
    <tr>
      <th>37</th>
      <td>Downtown Toronto</td>
      <td>0</td>
      <td>Coffee Shop</td>
      <td>Sushi Restaurant</td>
      <td>Yoga Studio</td>
      <td>Creperie</td>
      <td>Beer Bar</td>
      <td>Smoothie Shop</td>
      <td>Sandwich Place</td>
      <td>Burger Joint</td>
      <td>Burrito Place</td>
      <td>Café</td>
    </tr>
    <tr>
      <th>38</th>
      <td>East Toronto</td>
      <td>0</td>
      <td>Light Rail Station</td>
      <td>Auto Workshop</td>
      <td>Park</td>
      <td>Pizza Place</td>
      <td>Recording Studio</td>
      <td>Restaurant</td>
      <td>Butcher</td>
      <td>Burrito Place</td>
      <td>Brewery</td>
      <td>Skate Park</td>
    </tr>
  </tbody>
</table>
</div>



__Cluster 1__


```python
Toronto_merged.loc[Toronto_merged['Cluster Labels'] == 1, Toronto_merged.columns[[1] + list(range(5, Toronto_merged.shape[1]))]]
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
      <th>Borough</th>
      <th>Cluster Labels</th>
      <th>1st Most Common Venue</th>
      <th>2nd Most Common Venue</th>
      <th>3rd Most Common Venue</th>
      <th>4th Most Common Venue</th>
      <th>5th Most Common Venue</th>
      <th>6th Most Common Venue</th>
      <th>7th Most Common Venue</th>
      <th>8th Most Common Venue</th>
      <th>9th Most Common Venue</th>
      <th>10th Most Common Venue</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>8</th>
      <td>Central Toronto</td>
      <td>1</td>
      <td>Gym</td>
      <td>Trail</td>
      <td>Tennis Court</td>
      <td>Dessert Shop</td>
      <td>Falafel Restaurant</td>
      <td>Event Space</td>
      <td>Ethiopian Restaurant</td>
      <td>Electronics Store</td>
      <td>Eastern European Restaurant</td>
      <td>Dumpling Restaurant</td>
    </tr>
  </tbody>
</table>
</div>



__Cluster 2__


```python
Toronto_merged.loc[Toronto_merged['Cluster Labels'] == 2, Toronto_merged.columns[[1] + list(range(5, Toronto_merged.shape[1]))]]
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
      <th>Borough</th>
      <th>Cluster Labels</th>
      <th>1st Most Common Venue</th>
      <th>2nd Most Common Venue</th>
      <th>3rd Most Common Venue</th>
      <th>4th Most Common Venue</th>
      <th>5th Most Common Venue</th>
      <th>6th Most Common Venue</th>
      <th>7th Most Common Venue</th>
      <th>8th Most Common Venue</th>
      <th>9th Most Common Venue</th>
      <th>10th Most Common Venue</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>10</th>
      <td>Downtown Toronto</td>
      <td>2</td>
      <td>Park</td>
      <td>Playground</td>
      <td>Trail</td>
      <td>Yoga Studio</td>
      <td>Donut Shop</td>
      <td>Diner</td>
      <td>Discount Store</td>
      <td>Distribution Center</td>
      <td>Dog Run</td>
      <td>Doner Restaurant</td>
    </tr>
  </tbody>
</table>
</div>



__Cluster 3__


```python
Toronto_merged.loc[Toronto_merged['Cluster Labels'] == 3, Toronto_merged.columns[[1] + list(range(5, Toronto_merged.shape[1]))]]
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
      <th>Borough</th>
      <th>Cluster Labels</th>
      <th>1st Most Common Venue</th>
      <th>2nd Most Common Venue</th>
      <th>3rd Most Common Venue</th>
      <th>4th Most Common Venue</th>
      <th>5th Most Common Venue</th>
      <th>6th Most Common Venue</th>
      <th>7th Most Common Venue</th>
      <th>8th Most Common Venue</th>
      <th>9th Most Common Venue</th>
      <th>10th Most Common Venue</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>22</th>
      <td>Central Toronto</td>
      <td>3</td>
      <td>Garden</td>
      <td>Home Service</td>
      <td>Music Venue</td>
      <td>Dessert Shop</td>
      <td>Falafel Restaurant</td>
      <td>Event Space</td>
      <td>Ethiopian Restaurant</td>
      <td>Electronics Store</td>
      <td>Eastern European Restaurant</td>
      <td>Dumpling Restaurant</td>
    </tr>
  </tbody>
</table>
</div>



__Cluster 4__


```python
Toronto_merged.loc[Toronto_merged['Cluster Labels'] == 4, Toronto_merged.columns[[1] + list(range(5, Toronto_merged.shape[1]))]]
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
      <th>Borough</th>
      <th>Cluster Labels</th>
      <th>1st Most Common Venue</th>
      <th>2nd Most Common Venue</th>
      <th>3rd Most Common Venue</th>
      <th>4th Most Common Venue</th>
      <th>5th Most Common Venue</th>
      <th>6th Most Common Venue</th>
      <th>7th Most Common Venue</th>
      <th>8th Most Common Venue</th>
      <th>9th Most Common Venue</th>
      <th>10th Most Common Venue</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>4</th>
      <td>Central Toronto</td>
      <td>4</td>
      <td>Park</td>
      <td>Swim School</td>
      <td>Bus Line</td>
      <td>Yoga Studio</td>
      <td>Diner</td>
      <td>Falafel Restaurant</td>
      <td>Event Space</td>
      <td>Ethiopian Restaurant</td>
      <td>Electronics Store</td>
      <td>Eastern European Restaurant</td>
    </tr>
  </tbody>
</table>
</div>




```python

```
