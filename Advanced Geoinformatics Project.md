```python
conda install -c conda-forge requests six pandas numpy osmnet pandana matplotlib geopy cartopy pyyaml scikit-learn
```

    Collecting package metadata (current_repodata.json): done
    Solving environment: done
    
    ## Package Plan ##
    
      environment location: /Users/radleyciego/opt/anaconda3
    
      added / updated specs:
        - cartopy
        - geopy
        - matplotlib
        - numpy
        - osmnet
        - pandana
        - pandas
        - pyyaml
        - requests
        - scikit-learn
        - six
    
    
    The following packages will be downloaded:
    
        package                    |            build
        ---------------------------|-----------------
        ca-certificates-2022.5.18.1|       h033912b_0         145 KB  conda-forge
        certifi-2022.5.18.1        |   py39h6e9494a_0         150 KB  conda-forge
        ------------------------------------------------------------
                                               Total:         295 KB
    
    The following packages will be UPDATED:
    
      ca-certificates                      2021.10.8-h033912b_0 --> 2022.5.18.1-h033912b_0
      certifi                          2021.10.8-py39h6e9494a_2 --> 2022.5.18.1-py39h6e9494a_0
    
    
    
    Downloading and Extracting Packages
    certifi-2022.5.18.1  | 150 KB    | ##################################### | 100% 
    ca-certificates-2022 | 145 KB    | ##################################### | 100% 
    Preparing transaction: done
    Verifying transaction: done
    Executing transaction: done
    
    Note: you may need to restart the kernel to use updated packages.



```python
conda install urbanaccess -c conda-forge
```

    Collecting package metadata (current_repodata.json): done
    Solving environment: done
    
    # All requested packages already installed.
    
    
    Note: you may need to restart the kernel to use updated packages.



```python
conda install -c conda-forge pyepsg
```

    Collecting package metadata (current_repodata.json): done
    Solving environment: done
    
    # All requested packages already installed.
    
    
    Note: you may need to restart the kernel to use updated packages.



```python
import matplotlib

import pandas as pd
import cartopy.crs as ccrs
import cartopy
import matplotlib.pyplot as plt
import pandana as pdna
import osmnet as osm
from pandana.loaders import osm
import time
import pyepsg
import h5py

import urbanaccess as ua
from urbanaccess.config import settings
from urbanaccess.gtfsfeeds import feeds
from urbanaccess import gtfsfeeds
from urbanaccess.gtfs.gtfsfeeds_dataframe import gtfsfeeds_dfs
from urbanaccess.network import ua_network, load_network

from simpledbf import Dbf5

%matplotlib inline
```


```python
settings.to_dict()
```




    {'data_folder': 'data',
     'logs_folder': 'logs',
     'log_file': True,
     'log_console': False,
     'log_name': 'urbanaccess',
     'log_filename': 'urbanaccess',
     'txt_encoding': 'utf-8',
     'gtfs_api': {'gtfsdataexch': 'http://www.gtfs-data-exchange.com/api/agencies?format=csv'}}




```python
settings.log_console = True
```


```python
settings.log_console = False
```


```python
feeds.to_dict()
```




    {'gtfs_feeds': {}}




```python
# GTFS feeds can be added using custom URLs by adding a dictionary with the key as the name of the transit agency
feeds.add_feed(add_dict={'MTA New York City Transit Authority': 'http://web.mta.info/developers/data/nyct/subway/google_transit.zip'})
feeds.add_feed(add_dict={'MTA Bronx Transit Bus': 'http://web.mta.info/developers/data/nyct/bus/google_transit_bronx.zip'})
feeds.add_feed(add_dict={'MTA Brooklyn Transit Bus': 'http://web.mta.info/developers/data/nyct/bus/google_transit_brooklyn.zip'})
feeds.add_feed(add_dict={'MTA Manhattan Transit Bus': 'http://web.mta.info/developers/data/nyct/bus/google_transit_manhattan.zip'})
feeds.add_feed(add_dict={'MTA Queens Transit Bus': 'http://web.mta.info/developers/data/nyct/bus/google_transit_queens.zip'})
feeds.add_feed(add_dict={'MTA Staten Island Transit Bus': 'http://web.mta.info/developers/data/nyct/bus/google_transit_staten_island.zip'})
```

    Added 1 feeds to gtfs_feeds: {'MTA New York City Transit Authority': 'http://web.mta.info/developers/data/nyct/subway/google_transit.zip'}
    Added 1 feeds to gtfs_feeds: {'MTA Bronx Transit Bus': 'http://web.mta.info/developers/data/nyct/bus/google_transit_bronx.zip'}
    Added 1 feeds to gtfs_feeds: {'MTA Brooklyn Transit Bus': 'http://web.mta.info/developers/data/nyct/bus/google_transit_brooklyn.zip'}
    Added 1 feeds to gtfs_feeds: {'MTA Manhattan Transit Bus': 'http://web.mta.info/developers/data/nyct/bus/google_transit_manhattan.zip'}
    Added 1 feeds to gtfs_feeds: {'MTA Queens Transit Bus': 'http://web.mta.info/developers/data/nyct/bus/google_transit_queens.zip'}
    Added 1 feeds to gtfs_feeds: {'MTA Staten Island Transit Bus': 'http://web.mta.info/developers/data/nyct/bus/google_transit_staten_island.zip'}



```python
# List the GTFS feeds in feed object ready to download
feeds.to_dict()
```




    {'gtfs_feeds': {'MTA New York City Transit Authority': 'http://web.mta.info/developers/data/nyct/subway/google_transit.zip',
      'MTA Bronx Transit Bus': 'http://web.mta.info/developers/data/nyct/bus/google_transit_bronx.zip',
      'MTA Brooklyn Transit Bus': 'http://web.mta.info/developers/data/nyct/bus/google_transit_brooklyn.zip',
      'MTA Manhattan Transit Bus': 'http://web.mta.info/developers/data/nyct/bus/google_transit_manhattan.zip',
      'MTA Queens Transit Bus': 'http://web.mta.info/developers/data/nyct/bus/google_transit_queens.zip',
      'MTA Staten Island Transit Bus': 'http://web.mta.info/developers/data/nyct/bus/google_transit_staten_island.zip'}}




```python
# Download function to download all feeds in the feeds object at once. 
gtfsfeeds.download()
```

    6 GTFS feed(s) will be downloaded here: data/gtfsfeed_zips
    MTA New York City Transit Authority GTFS feed downloaded successfully. Took 3.58 seconds for 4,648,447.0KB
    MTA Bronx Transit Bus GTFS feed downloaded successfully. Took 5.26 seconds for 8,786,764.0KB
    MTA Brooklyn Transit Bus GTFS feed downloaded successfully. Took 11.50 seconds for 16,590,008.0KB
    MTA Manhattan Transit Bus GTFS feed downloaded successfully. Took 4.71 seconds for 7,613,854.0KB
    MTA Queens Transit Bus GTFS feed downloaded successfully. Took 4.72 seconds for 8,260,856.0KB
    MTA Staten Island Transit Bus GTFS feed downloaded successfully. Took 3.65 seconds for 6,040,530.0KB
    GTFS feed download completed. Took 33.41 seconds
    MTA Staten Island Transit Bus.zip successfully extracted to: data/gtfsfeed_text/MTA Staten Island Transit Bus
    MTA Brooklyn Transit Bus.zip successfully extracted to: data/gtfsfeed_text/MTA Brooklyn Transit Bus
    MTA New York City Transit Authority.zip successfully extracted to: data/gtfsfeed_text/MTA New York City Transit Authority
    MTA Bronx Transit Bus.zip successfully extracted to: data/gtfsfeed_text/MTA Bronx Transit Bus
    MTA Manhattan Transit Bus.zip successfully extracted to: data/gtfsfeed_text/MTA Manhattan Transit Bus
    MTA Queens Transit Bus.zip successfully extracted to: data/gtfsfeed_text/MTA Queens Transit Bus
    GTFS feed zipfile extraction completed. Took 1.64 seconds for 6 files



```python
# After downloading the data, load GTFS feeds into a combined network of Pandas DataFrames. 
# One or multiple feeds inside a root folder can be specified using the gtfsfeed_path paramater.
# Setting a bounding box limits GTFS data to specified area (NYC)

validation = True
verbose = True
# bbox for New York City
bbox = (-74.25909,40.477399,-73.700181,40.916178)
remove_stops_outsidebbox = True
append_definitions = True

loaded_feeds = ua.gtfs.load.gtfsfeed_to_df(gtfsfeed_path='/Users/radleyciego/data/gtfsfeed_text/',
                                           validation=validation,
                                           verbose=verbose,
                                           bbox=bbox,
                                           remove_stops_outsidebbox=remove_stops_outsidebbox,
                                           append_definitions=append_definitions)
```

    Checking GTFS text file header whitespace... Reading files using encoding: utf-8 set in configuration.
    GTFS text file header whitespace check completed. Took 1.53 seconds
    --------------------------------
    Processing GTFS feed: MTA Bronx Transit Bus
    The unique agency id: mta_new_york_city_transit was generated using the name of the agency in the agency.txt file.
    Unique agency id operation complete. Took 0.05 seconds
    Unique GTFS feed id operation complete. Took 0.01 seconds
    No GTFS feed stops were found to be outside the bounding box coordinates
    MTA Bronx Transit Bus GTFS feed stops: coordinates are in northwest hemisphere. Latitude = North (90); Longitude = West (-90).
    Appended route type to stops
    Appended route type to stop_times
    --------------------------------
    --------------------------------
    Processing GTFS feed: MTA Staten Island Transit Bus
    The unique agency id: mta_new_york_city_transit was generated using the name of the agency in the agency.txt file.
    Unique agency id operation complete. Took 0.03 seconds
    Unique GTFS feed id operation complete. Took 0.00 seconds
    No GTFS feed stops were found to be outside the bounding box coordinates
    MTA Staten Island Transit Bus GTFS feed stops: coordinates are in northwest hemisphere. Latitude = North (90); Longitude = West (-90).
    Appended route type to stops
    Appended route type to stop_times
    --------------------------------
    --------------------------------
    Processing GTFS feed: MTA Queens Transit Bus
    The unique agency id: mta_new_york_city_transit was generated using the name of the agency in the agency.txt file.
    Unique agency id operation complete. Took 0.05 seconds
    Unique GTFS feed id operation complete. Took 0.01 seconds
    No GTFS feed stops were found to be outside the bounding box coordinates
    MTA Queens Transit Bus GTFS feed stops: coordinates are in northwest hemisphere. Latitude = North (90); Longitude = West (-90).
    Appended route type to stops
    Appended route type to stop_times
    --------------------------------
    --------------------------------
    Processing GTFS feed: MTA New York City Transit Authority
    The unique agency id: mta_new_york_city_transit was generated using the name of the agency in the agency.txt file.
    Unique agency id operation complete. Took 0.02 seconds
    Unique GTFS feed id operation complete. Took 0.00 seconds
    No GTFS feed stops were found to be outside the bounding box coordinates
    MTA New York City Transit Authority GTFS feed stops: coordinates are in northwest hemisphere. Latitude = North (90); Longitude = West (-90).
    Appended route type to stops
    Appended route type to stop_times
    --------------------------------
    --------------------------------
    Processing GTFS feed: MTA Brooklyn Transit Bus
    The unique agency id: mta_new_york_city_transit was generated using the name of the agency in the agency.txt file.
    Unique agency id operation complete. Took 0.08 seconds
    Unique GTFS feed id operation complete. Took 0.01 seconds
    No GTFS feed stops were found to be outside the bounding box coordinates
    MTA Brooklyn Transit Bus GTFS feed stops: coordinates are in northwest hemisphere. Latitude = North (90); Longitude = West (-90).
    Appended route type to stops
    Appended route type to stop_times
    --------------------------------
    --------------------------------
    Processing GTFS feed: MTA Manhattan Transit Bus
    The unique agency id: mta_new_york_city_transit was generated using the name of the agency in the agency.txt file.
    Unique agency id operation complete. Took 0.04 seconds
    Unique GTFS feed id operation complete. Took 0.01 seconds
    No GTFS feed stops were found to be outside the bounding box coordinates
    MTA Manhattan Transit Bus GTFS feed stops: coordinates are in northwest hemisphere. Latitude = North (90); Longitude = West (-90).
    Appended route type to stops
    Appended route type to stop_times
    --------------------------------
    Added descriptive definitions to stops, routes, stop_times, and trips tables
    Successfully converted ['departure_time'] to seconds past midnight and appended new columns to stop_times. Took 19.49 seconds
    6 GTFS feed file(s) successfully read as dataframes:
         MTA Bronx Transit Bus
         MTA Staten Island Transit Bus
         MTA Queens Transit Bus
         MTA New York City Transit Authority
         MTA Brooklyn Transit Bus
         MTA Manhattan Transit Bus
         Took 40.97 seconds



```python
# The feeds output, now a global urbanaccess_gtfs_df object, can be accessed with the specified variable loaded_feeds.
loaded_feeds.stops.head()
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
      <th>stop_id</th>
      <th>stop_name</th>
      <th>stop_desc</th>
      <th>stop_lat</th>
      <th>stop_lon</th>
      <th>zone_id</th>
      <th>stop_url</th>
      <th>location_type</th>
      <th>parent_station</th>
      <th>unique_agency_id</th>
      <th>unique_feed_id</th>
      <th>route_type</th>
      <th>stop_code</th>
      <th>location_type_desc</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>100014</td>
      <td>BEDFORD PK BL/GRAND CONCOURSE</td>
      <td>NaN</td>
      <td>40.872562</td>
      <td>-73.888156</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0</td>
      <td>NaN</td>
      <td>mta_new_york_city_transit</td>
      <td>mta_bronx_transit_bus_1</td>
      <td>3.0</td>
      <td>NaN</td>
      <td>stop</td>
    </tr>
    <tr>
      <th>1</th>
      <td>100017</td>
      <td>PAUL AV/W 205 ST</td>
      <td>NaN</td>
      <td>40.876836</td>
      <td>-73.889710</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0</td>
      <td>NaN</td>
      <td>mta_new_york_city_transit</td>
      <td>mta_bronx_transit_bus_1</td>
      <td>3.0</td>
      <td>NaN</td>
      <td>stop</td>
    </tr>
    <tr>
      <th>2</th>
      <td>100018</td>
      <td>PAUL AV/W MOSHOLU PY S</td>
      <td>NaN</td>
      <td>40.880392</td>
      <td>-73.886081</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0</td>
      <td>NaN</td>
      <td>mta_new_york_city_transit</td>
      <td>mta_bronx_transit_bus_1</td>
      <td>3.0</td>
      <td>NaN</td>
      <td>stop</td>
    </tr>
    <tr>
      <th>3</th>
      <td>100019</td>
      <td>GRAND CONCOURSE/E 138 ST</td>
      <td>NaN</td>
      <td>40.813496</td>
      <td>-73.929489</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0</td>
      <td>NaN</td>
      <td>mta_new_york_city_transit</td>
      <td>mta_bronx_transit_bus_1</td>
      <td>3.0</td>
      <td>NaN</td>
      <td>stop</td>
    </tr>
    <tr>
      <th>4</th>
      <td>100020</td>
      <td>GRAND CONCOURSE/E 144 ST</td>
      <td>NaN</td>
      <td>40.816812</td>
      <td>-73.928001</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0</td>
      <td>NaN</td>
      <td>mta_new_york_city_transit</td>
      <td>mta_bronx_transit_bus_1</td>
      <td>3.0</td>
      <td>NaN</td>
      <td>stop</td>
    </tr>
  </tbody>
</table>
</div>




```python
loaded_feeds.stops.unique_agency_id.unique()
```




    array(['mta_new_york_city_transit'], dtype=object)




```python
# View the transit stop locations
loaded_feeds.stops.plot(kind='scatter', x='stop_lon', y='stop_lat', s=0.1)
```




    <AxesSubplot:xlabel='stop_lon', ylabel='stop_lat'>




    
![png](Advanced%20Geoinformatics%20Project_files/Advanced%20Geoinformatics%20Project_14_1.png)
    



```python
loaded_feeds.routes.head()
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
      <th>route_id</th>
      <th>agency_id</th>
      <th>route_short_name</th>
      <th>route_long_name</th>
      <th>route_desc</th>
      <th>route_type</th>
      <th>route_color</th>
      <th>route_text_color</th>
      <th>unique_agency_id</th>
      <th>unique_feed_id</th>
      <th>route_url</th>
      <th>route_type_desc</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>B1</td>
      <td>MTA NYCT</td>
      <td>B1</td>
      <td>Bay Ridge - Manhattan Beach</td>
      <td>via 86th St / Ocean Pkwy</td>
      <td>3</td>
      <td>00AEEF</td>
      <td>FFFFFF</td>
      <td>mta_new_york_city_transit</td>
      <td>mta_bronx_transit_bus_1</td>
      <td>NaN</td>
      <td>Bus</td>
    </tr>
    <tr>
      <th>1</th>
      <td>B11</td>
      <td>MTA NYCT</td>
      <td>B11</td>
      <td>Sunset Park - Midwood</td>
      <td>via 49th &amp; 50th St / Avenue J</td>
      <td>3</td>
      <td>006CB7</td>
      <td>FFFFFF</td>
      <td>mta_new_york_city_transit</td>
      <td>mta_bronx_transit_bus_1</td>
      <td>NaN</td>
      <td>Bus</td>
    </tr>
    <tr>
      <th>2</th>
      <td>B12</td>
      <td>MTA NYCT</td>
      <td>B12</td>
      <td>Lefferts Gardens - East New York</td>
      <td>via Clarkson Av / Empire Blvd / East New York Av</td>
      <td>3</td>
      <td>6CBE45</td>
      <td>FFFFFF</td>
      <td>mta_new_york_city_transit</td>
      <td>mta_bronx_transit_bus_1</td>
      <td>NaN</td>
      <td>Bus</td>
    </tr>
    <tr>
      <th>3</th>
      <td>B13</td>
      <td>MTA NYCT</td>
      <td>B13</td>
      <td>Spring Creek - Wyckoff Hospital</td>
      <td>via Crescent St / Jamaica Av / Wyckoff Av</td>
      <td>3</td>
      <td>FAA61A</td>
      <td>FFFFFF</td>
      <td>mta_new_york_city_transit</td>
      <td>mta_bronx_transit_bus_1</td>
      <td>NaN</td>
      <td>Bus</td>
    </tr>
    <tr>
      <th>4</th>
      <td>B14</td>
      <td>MTA NYCT</td>
      <td>B14</td>
      <td>Spring Creek - Crown Heights</td>
      <td>via Sutter Av / Pitkin Av</td>
      <td>3</td>
      <td>00AEEF</td>
      <td>FFFFFF</td>
      <td>mta_new_york_city_transit</td>
      <td>mta_bronx_transit_bus_1</td>
      <td>NaN</td>
      <td>Bus</td>
    </tr>
  </tbody>
</table>
</div>




```python
loaded_feeds.stop_times.head()
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
      <th>trip_id</th>
      <th>arrival_time</th>
      <th>departure_time</th>
      <th>stop_id</th>
      <th>stop_sequence</th>
      <th>pickup_type</th>
      <th>drop_off_type</th>
      <th>unique_agency_id</th>
      <th>unique_feed_id</th>
      <th>route_type</th>
      <th>stop_headsign</th>
      <th>shape_dist_traveled</th>
      <th>pickup_type_desc</th>
      <th>drop_off_type_desc</th>
      <th>departure_time_sec</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>GH_B2-Weekday-BM-143500_BX41_901</td>
      <td>23:55:00</td>
      <td>23:55:00</td>
      <td>104525</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>mta_new_york_city_transit</td>
      <td>mta_bronx_transit_bus_1</td>
      <td>3</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>Regularly Scheduled</td>
      <td>Regularly Scheduled</td>
      <td>86100</td>
    </tr>
    <tr>
      <th>1</th>
      <td>GH_B2-Weekday-BM-143500_BX41_901</td>
      <td>23:56:44</td>
      <td>23:56:44</td>
      <td>102795</td>
      <td>2</td>
      <td>0</td>
      <td>0</td>
      <td>mta_new_york_city_transit</td>
      <td>mta_bronx_transit_bus_1</td>
      <td>3</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>Regularly Scheduled</td>
      <td>Regularly Scheduled</td>
      <td>86204</td>
    </tr>
    <tr>
      <th>2</th>
      <td>GH_B2-Weekday-BM-143500_BX41_901</td>
      <td>23:58:58</td>
      <td>23:58:58</td>
      <td>102796</td>
      <td>3</td>
      <td>0</td>
      <td>0</td>
      <td>mta_new_york_city_transit</td>
      <td>mta_bronx_transit_bus_1</td>
      <td>3</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>Regularly Scheduled</td>
      <td>Regularly Scheduled</td>
      <td>86338</td>
    </tr>
    <tr>
      <th>3</th>
      <td>GH_B2-Weekday-BM-143500_BX41_901</td>
      <td>23:59:50</td>
      <td>23:59:50</td>
      <td>104560</td>
      <td>4</td>
      <td>0</td>
      <td>0</td>
      <td>mta_new_york_city_transit</td>
      <td>mta_bronx_transit_bus_1</td>
      <td>3</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>Regularly Scheduled</td>
      <td>Regularly Scheduled</td>
      <td>86390</td>
    </tr>
    <tr>
      <th>4</th>
      <td>GH_B2-Weekday-BM-143500_BX41_901</td>
      <td>24:00:49</td>
      <td>24:00:49</td>
      <td>102798</td>
      <td>5</td>
      <td>0</td>
      <td>0</td>
      <td>mta_new_york_city_transit</td>
      <td>mta_bronx_transit_bus_1</td>
      <td>3</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>Regularly Scheduled</td>
      <td>Regularly Scheduled</td>
      <td>86449</td>
    </tr>
  </tbody>
</table>
</div>




```python
loaded_feeds.trips.head()
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
      <th>route_id</th>
      <th>service_id</th>
      <th>trip_id</th>
      <th>trip_headsign</th>
      <th>direction_id</th>
      <th>shape_id</th>
      <th>unique_agency_id</th>
      <th>unique_feed_id</th>
      <th>block_id</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>BX41</td>
      <td>GH_B2-Weekday-BM</td>
      <td>GH_B2-Weekday-BM-143500_BX41_901</td>
      <td>THE HUB 150 ST via WEBSTER</td>
      <td>1</td>
      <td>BX410049</td>
      <td>mta_new_york_city_transit</td>
      <td>mta_bronx_transit_bus_1</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>1</th>
      <td>BX41</td>
      <td>GH_B2-Weekday</td>
      <td>GH_B2-Weekday-003900_BX41_901</td>
      <td>WILLAMSBRIDGE GUN HILL RD via WEBSTER</td>
      <td>0</td>
      <td>BX410047</td>
      <td>mta_new_york_city_transit</td>
      <td>mta_bronx_transit_bus_1</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2</th>
      <td>BX39</td>
      <td>GH_B2-Weekday</td>
      <td>GH_B2-Weekday-007600_BX41_901</td>
      <td>WAKEFIELD 241 ST via WHITE PLS RD</td>
      <td>0</td>
      <td>BX390019</td>
      <td>mta_new_york_city_transit</td>
      <td>mta_bronx_transit_bus_1</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>3</th>
      <td>BX39</td>
      <td>GH_B2-Weekday</td>
      <td>GH_B2-Weekday-009500_BX41_901</td>
      <td>GUN HILL RD</td>
      <td>1</td>
      <td>BX390167</td>
      <td>mta_new_york_city_transit</td>
      <td>mta_bronx_transit_bus_1</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>4</th>
      <td>BX41</td>
      <td>GH_B2-Weekday</td>
      <td>GH_B2-Weekday-011300_BX41_901</td>
      <td>THE HUB 150 ST via WEBSTER</td>
      <td>1</td>
      <td>BX410049</td>
      <td>mta_new_york_city_transit</td>
      <td>mta_bronx_transit_bus_1</td>
      <td>NaN</td>
    </tr>
  </tbody>
</table>
</div>




```python
loaded_feeds.calendar.head()
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
      <th>service_id</th>
      <th>monday</th>
      <th>tuesday</th>
      <th>wednesday</th>
      <th>thursday</th>
      <th>friday</th>
      <th>saturday</th>
      <th>sunday</th>
      <th>start_date</th>
      <th>end_date</th>
      <th>unique_agency_id</th>
      <th>unique_feed_id</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>GH_B2-Sunday</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>20220327</td>
      <td>20220619</td>
      <td>mta_new_york_city_transit</td>
      <td>mta_bronx_transit_bus_1</td>
    </tr>
    <tr>
      <th>1</th>
      <td>GH_B2-Weekday-SDon</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>20220328</td>
      <td>20220624</td>
      <td>mta_new_york_city_transit</td>
      <td>mta_bronx_transit_bus_1</td>
    </tr>
    <tr>
      <th>2</th>
      <td>GH_B2-Weekday-SDon-BM</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>20220327</td>
      <td>20220623</td>
      <td>mta_new_york_city_transit</td>
      <td>mta_bronx_transit_bus_1</td>
    </tr>
    <tr>
      <th>3</th>
      <td>GH_B2-Saturday</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>20220402</td>
      <td>20220625</td>
      <td>mta_new_york_city_transit</td>
      <td>mta_bronx_transit_bus_1</td>
    </tr>
    <tr>
      <th>4</th>
      <td>KB_B2-Sunday</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>20220327</td>
      <td>20220619</td>
      <td>mta_new_york_city_transit</td>
      <td>mta_bronx_transit_bus_1</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Create a time weighted graph during Monday morning peak period
ua.gtfs.network.create_transit_net(gtfsfeeds_dfs=loaded_feeds,
                                   day='monday',
                                   timerange=['07:00:00', '10:00:00'],
                                   calendar_dates_lookup=None)
```

    Using calendar to extract service_ids to select trips.
    56 service_ids were extracted from calendar
    63,885 trip(s) 39.22 percent of 162,875 total trip records were found in calendar for GTFS feed(s): ['mta bronx transit bus', 'mta staten island transit bus', 'mta queens transit bus', 'mta new york city transit authority', 'mta brooklyn transit bus', 'mta manhattan transit bus']
    NOTE: If you expected more trips to have been extracted and your GTFS feed(s) have a calendar_dates file, consider utilizing the calendar_dates_lookup parameter in order to add additional trips based on information inside of calendar_dates. This should only be done if you know the corresponding GTFS feed is using calendar_dates instead of calendar to specify service_ids. When in doubt do not use the calendar_dates_lookup parameter.
    63,885 of 162,875 total trips were extracted representing calendar day: monday. Took 0.30 seconds
    There are no departure time records missing from trips following the specified schedule. There are no records to interpolate.
    Difference between stop times has been successfully calculated. Took 8.80 seconds
    Stop times from 07:00:00 to 10:00:00 successfully selected 455,243 records out of 2,433,652 total records (18.71 percent of total). Took 0.83 seconds
    Starting transformation process for 15,180 total trips...
    stop time table transformation to Pandana format edge table completed. Took 18.77 seconds
    Time conversion completed: seconds converted to minutes.
    13,513 of 14,247 records selected from stops. Took 0.05 seconds
    stop time table transformation to Pandana format node table completed. Took 0.02 seconds
    route type successfully joined to transit edges. Took 13.16 seconds
    route id successfully joined to transit edges. Took 0.31 seconds
    Successfully created transit network. Took 53.11 seconds





    <urbanaccess.network.urbanaccess_network at 0x7fdb73b84400>




```python
urbanaccess_net = ua.network.ua_network
```


```python
urbanaccess_net.transit_edges.head()
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
      <th>node_id_from</th>
      <th>node_id_to</th>
      <th>weight</th>
      <th>unique_agency_id</th>
      <th>unique_trip_id</th>
      <th>sequence</th>
      <th>id</th>
      <th>route_type</th>
      <th>unique_route_id</th>
      <th>net_type</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>104N_mta_new_york_city_transit</td>
      <td>103N_mta_new_york_city_transit</td>
      <td>1.5</td>
      <td>mta_new_york_city_transit</td>
      <td>AFA21GEN-1091-Weekday-00_037150_1..N03R_mta_ne...</td>
      <td>1</td>
      <td>AFA21GEN-1091-Weekday-00_037150_1..N03R_mta_ne...</td>
      <td>1</td>
      <td>1_mta_new_york_city_transit</td>
      <td>transit</td>
    </tr>
    <tr>
      <th>1</th>
      <td>103N_mta_new_york_city_transit</td>
      <td>101N_mta_new_york_city_transit</td>
      <td>1.5</td>
      <td>mta_new_york_city_transit</td>
      <td>AFA21GEN-1091-Weekday-00_037150_1..N03R_mta_ne...</td>
      <td>2</td>
      <td>AFA21GEN-1091-Weekday-00_037150_1..N03R_mta_ne...</td>
      <td>1</td>
      <td>1_mta_new_york_city_transit</td>
      <td>transit</td>
    </tr>
    <tr>
      <th>2</th>
      <td>135S_mta_new_york_city_transit</td>
      <td>136S_mta_new_york_city_transit</td>
      <td>1.0</td>
      <td>mta_new_york_city_transit</td>
      <td>AFA21GEN-1091-Weekday-00_037300_1..S03R_mta_ne...</td>
      <td>1</td>
      <td>AFA21GEN-1091-Weekday-00_037300_1..S03R_mta_ne...</td>
      <td>1</td>
      <td>1_mta_new_york_city_transit</td>
      <td>transit</td>
    </tr>
    <tr>
      <th>3</th>
      <td>136S_mta_new_york_city_transit</td>
      <td>137S_mta_new_york_city_transit</td>
      <td>1.5</td>
      <td>mta_new_york_city_transit</td>
      <td>AFA21GEN-1091-Weekday-00_037300_1..S03R_mta_ne...</td>
      <td>2</td>
      <td>AFA21GEN-1091-Weekday-00_037300_1..S03R_mta_ne...</td>
      <td>1</td>
      <td>1_mta_new_york_city_transit</td>
      <td>transit</td>
    </tr>
    <tr>
      <th>4</th>
      <td>137S_mta_new_york_city_transit</td>
      <td>138S_mta_new_york_city_transit</td>
      <td>1.0</td>
      <td>mta_new_york_city_transit</td>
      <td>AFA21GEN-1091-Weekday-00_037300_1..S03R_mta_ne...</td>
      <td>3</td>
      <td>AFA21GEN-1091-Weekday-00_037300_1..S03R_mta_ne...</td>
      <td>1</td>
      <td>1_mta_new_york_city_transit</td>
      <td>transit</td>
    </tr>
  </tbody>
</table>
</div>




```python
urbanaccess_net.transit_nodes.head()
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
      <th>x</th>
      <th>y</th>
      <th>unique_agency_id</th>
      <th>route_type</th>
      <th>stop_id</th>
      <th>stop_name</th>
      <th>parent_station</th>
      <th>stop_code</th>
      <th>zone_id</th>
      <th>location_type</th>
      <th>net_type</th>
    </tr>
    <tr>
      <th>node_id</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
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
      <th>100014_mta_new_york_city_transit</th>
      <td>-73.888156</td>
      <td>40.872562</td>
      <td>mta_new_york_city_transit</td>
      <td>3.0</td>
      <td>100014</td>
      <td>BEDFORD PK BL/GRAND CONCOURSE</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0</td>
      <td>transit</td>
    </tr>
    <tr>
      <th>100017_mta_new_york_city_transit</th>
      <td>-73.889710</td>
      <td>40.876836</td>
      <td>mta_new_york_city_transit</td>
      <td>3.0</td>
      <td>100017</td>
      <td>PAUL AV/W 205 ST</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0</td>
      <td>transit</td>
    </tr>
    <tr>
      <th>100018_mta_new_york_city_transit</th>
      <td>-73.886081</td>
      <td>40.880392</td>
      <td>mta_new_york_city_transit</td>
      <td>3.0</td>
      <td>100018</td>
      <td>PAUL AV/W MOSHOLU PY S</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0</td>
      <td>transit</td>
    </tr>
    <tr>
      <th>100019_mta_new_york_city_transit</th>
      <td>-73.929489</td>
      <td>40.813496</td>
      <td>mta_new_york_city_transit</td>
      <td>3.0</td>
      <td>100019</td>
      <td>GRAND CONCOURSE/E 138 ST</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0</td>
      <td>transit</td>
    </tr>
    <tr>
      <th>100021_mta_new_york_city_transit</th>
      <td>-73.927096</td>
      <td>40.818856</td>
      <td>mta_new_york_city_transit</td>
      <td>3.0</td>
      <td>100021</td>
      <td>GRAND CONCOURSE/E 149 ST</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0</td>
      <td>transit</td>
    </tr>
  </tbody>
</table>
</div>




```python
urbanaccess_net.transit_nodes.plot(kind='scatter', x='x', y='y', s=0.1)
```




    <AxesSubplot:xlabel='x', ylabel='y'>




    
![png](Advanced%20Geoinformatics%20Project_files/Advanced%20Geoinformatics%20Project_23_1.png)
    



```python
network = ua.network.load_network(dir='data', filename='nyc_net.h5')
network
```

    Successfully read store: data/nyc_net.h5 with the following keys: ['/edges', '/nodes']
    Successfully read store: data/nyc_net.h5 with the following keys: ['/edges', '/nodes']





    <urbanaccess.network.urbanaccess_network at 0x7fdb73b84400>




```python
# Download OpenStreetMap pedestrian street network data using New York City bounding box
nodes, edges = ua.osm.load.ua_network_from_bbox(bbox=(-74.25909,40.477399,-73.700181,40.916178),
                                                remove_lcn=True) 
```

    Requesting network data within bounding box from Overpass API in 1 request(s)
    Posting to http://www.overpass-api.de/api/interpreter with timeout=180, "{'data': '[out:json][timeout:180];(way["highway"]["highway"!~"motor|proposed|construction|abandoned|platform|raceway"]["foot"!~"no"]["pedestrians"!~"no"](40.47739900,-74.25909000,40.91617800,-73.70018100);>;);out;'}"
    Downloaded 185,258.8KB from www.overpass-api.de in 21.66 seconds
    Downloaded OSM network data within bounding box from Overpass API in 1 request(s) and 25.79 seconds
    Returning OSM data with 1,086,444 nodes and 291,401 ways...
    Edge node pairs completed. Took 267.47 seconds
    Returning processed graph with 404,466 nodes and 1,217,672 edges...
    Completed OSM data download and Pandana node and edge table creation in 306.13 seconds
    checking for low connectivity nodes...
    Generating contraction hierarchies with 8 threads.
    Setting CH node vector of size 404466
    Setting CH edge vector of size 1259704
    Range graph removed 1322362 edges of 2519408
    . 10% . 20% . 30% . 40% . 50% . 60% . 70% . 80% . 90% . 100%
    1,149 out of 404,466 nodes (0.28 percent of total) were identified as having low connectivity and have been removed.
    Completed OSM data download and graph node and edge table creation in 969.46 seconds



```python
# Create travel time weighted pedestrian network. Pedestrian travel speed set at 3mph. osm_edges and osm_nodes will
# hold nodes and edges, respectively.
ua.osm.network.create_osm_net(osm_edges=edges,
                              osm_nodes=nodes,
                              travel_speed_mph=3)
```

    Created OSM network with travel time impedance using a travel speed of 3 MPH. Took 0.03 seconds





    <urbanaccess.network.urbanaccess_network at 0x7fdb73b84400>




```python
# Visualize OSM network nodes
urbanaccess_net.osm_nodes.head()
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
      <th>x</th>
      <th>y</th>
      <th>id</th>
      <th>net_type</th>
    </tr>
    <tr>
      <th>id</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>30807308</th>
      <td>-73.962176</td>
      <td>40.792024</td>
      <td>30807308</td>
      <td>walk</td>
    </tr>
    <tr>
      <th>30807314</th>
      <td>-73.963576</td>
      <td>40.790720</td>
      <td>30807314</td>
      <td>walk</td>
    </tr>
    <tr>
      <th>30807351</th>
      <td>-73.966988</td>
      <td>40.785589</td>
      <td>30807351</td>
      <td>walk</td>
    </tr>
    <tr>
      <th>30978747</th>
      <td>-73.973425</td>
      <td>40.774267</td>
      <td>30978747</td>
      <td>walk</td>
    </tr>
    <tr>
      <th>30978752</th>
      <td>-73.974360</td>
      <td>40.774776</td>
      <td>30978752</td>
      <td>walk</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Visualize OSM network edges
urbanaccess_net.osm_edges.head()
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
      <th></th>
      <th>from</th>
      <th>to</th>
      <th>distance</th>
      <th>name</th>
      <th>ref</th>
      <th>highway</th>
      <th>service</th>
      <th>bridge</th>
      <th>tunnel</th>
      <th>access</th>
      <th>...</th>
      <th>lanes</th>
      <th>maxspeed</th>
      <th>hgv</th>
      <th>hov</th>
      <th>area</th>
      <th>width</th>
      <th>est_width</th>
      <th>junction</th>
      <th>weight</th>
      <th>net_type</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>278630910</th>
      <th>6933321234</th>
      <td>278630910</td>
      <td>6933321234</td>
      <td>16.146700</td>
      <td>Boerum Place</td>
      <td>NaN</td>
      <td>primary</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>...</td>
      <td>3</td>
      <td>25 mph</td>
      <td>destination</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0.200662</td>
      <td>walk</td>
    </tr>
    <tr>
      <th rowspan="2" valign="top">6933321234</th>
      <th>278630910</th>
      <td>6933321234</td>
      <td>278630910</td>
      <td>16.146700</td>
      <td>Boerum Place</td>
      <td>NaN</td>
      <td>primary</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>...</td>
      <td>3</td>
      <td>25 mph</td>
      <td>destination</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0.200662</td>
      <td>walk</td>
    </tr>
    <tr>
      <th>4209410680</th>
      <td>6933321234</td>
      <td>4209410680</td>
      <td>75.472688</td>
      <td>Boerum Place</td>
      <td>NaN</td>
      <td>primary</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>...</td>
      <td>3</td>
      <td>25 mph</td>
      <td>destination</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0.937933</td>
      <td>walk</td>
    </tr>
    <tr>
      <th>4209410680</th>
      <th>6933321234</th>
      <td>4209410680</td>
      <td>6933321234</td>
      <td>75.472688</td>
      <td>Boerum Place</td>
      <td>NaN</td>
      <td>primary</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>...</td>
      <td>3</td>
      <td>25 mph</td>
      <td>destination</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0.937933</td>
      <td>walk</td>
    </tr>
    <tr>
      <th>4205830390</th>
      <th>3602678205</th>
      <td>4205830390</td>
      <td>3602678205</td>
      <td>27.601179</td>
      <td>West 106th Street</td>
      <td>NaN</td>
      <td>secondary</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>destination</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0.343012</td>
      <td>walk</td>
    </tr>
  </tbody>
</table>
<p>5 rows × 22 columns</p>
</div>




```python
urbanaccess_net.osm_nodes.plot(kind='scatter', x='x', y='y', s=0.1)
```




    <AxesSubplot:xlabel='x', ylabel='y'>




    
![png](Advanced%20Geoinformatics%20Project_files/Advanced%20Geoinformatics%20Project_29_1.png)
    



```python
# Integrate pedestrian and transit networks
ua.network.integrate_network(urbanaccess_network=urbanaccess_net,
                             headways=False)
```

    Loaded UrbanAccess network components comprised of:
         Transit: 13,513 nodes and 440,063 edges;
         OSM: 403,317 nodes and 1,215,368 edges
    Connector edges between the OSM and transit network nodes successfully completed. Took 3.89 seconds
    Edge and node tables formatted for Pandana with integer node ids: id_int, to_int, and from_int. Took 13.04 seconds
    Network edge and node network integration completed successfully resulting in a total of 416,830 nodes and 1,719,560 edges:
         Transit: 13,513 nodes 440,063 edges;
         OSM: 403,317 nodes 1,215,368 edges; and
         OSM/Transit connector: 27,026 edges.





    <urbanaccess.network.urbanaccess_network at 0x7fdb73b84400>




```python
urbanaccess_net.net_nodes.head()
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
      <th>id</th>
      <th>x</th>
      <th>y</th>
      <th>unique_agency_id</th>
      <th>route_type</th>
      <th>stop_id</th>
      <th>stop_name</th>
      <th>parent_station</th>
      <th>stop_code</th>
      <th>zone_id</th>
      <th>location_type</th>
      <th>net_type</th>
    </tr>
    <tr>
      <th>id_int</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
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
      <th>1</th>
      <td>100014_mta_new_york_city_transit</td>
      <td>-73.888156</td>
      <td>40.872562</td>
      <td>mta_new_york_city_transit</td>
      <td>3.0</td>
      <td>100014</td>
      <td>BEDFORD PK BL/GRAND CONCOURSE</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0.0</td>
      <td>transit</td>
    </tr>
    <tr>
      <th>2</th>
      <td>100017_mta_new_york_city_transit</td>
      <td>-73.889710</td>
      <td>40.876836</td>
      <td>mta_new_york_city_transit</td>
      <td>3.0</td>
      <td>100017</td>
      <td>PAUL AV/W 205 ST</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0.0</td>
      <td>transit</td>
    </tr>
    <tr>
      <th>3</th>
      <td>100018_mta_new_york_city_transit</td>
      <td>-73.886081</td>
      <td>40.880392</td>
      <td>mta_new_york_city_transit</td>
      <td>3.0</td>
      <td>100018</td>
      <td>PAUL AV/W MOSHOLU PY S</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0.0</td>
      <td>transit</td>
    </tr>
    <tr>
      <th>4</th>
      <td>100019_mta_new_york_city_transit</td>
      <td>-73.929489</td>
      <td>40.813496</td>
      <td>mta_new_york_city_transit</td>
      <td>3.0</td>
      <td>100019</td>
      <td>GRAND CONCOURSE/E 138 ST</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0.0</td>
      <td>transit</td>
    </tr>
    <tr>
      <th>5</th>
      <td>100021_mta_new_york_city_transit</td>
      <td>-73.927096</td>
      <td>40.818856</td>
      <td>mta_new_york_city_transit</td>
      <td>3.0</td>
      <td>100021</td>
      <td>GRAND CONCOURSE/E 149 ST</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0.0</td>
      <td>transit</td>
    </tr>
  </tbody>
</table>
</div>




```python
urbanaccess_net.net_edges.head()
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
      <th>from</th>
      <th>to</th>
      <th>weight</th>
      <th>unique_agency_id</th>
      <th>unique_trip_id</th>
      <th>sequence</th>
      <th>edge_id</th>
      <th>route_type</th>
      <th>unique_route_id</th>
      <th>net_type</th>
      <th>...</th>
      <th>lanes</th>
      <th>maxspeed</th>
      <th>hgv</th>
      <th>hov</th>
      <th>area</th>
      <th>width</th>
      <th>est_width</th>
      <th>junction</th>
      <th>from_int</th>
      <th>to_int</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>104N_mta_new_york_city_transit</td>
      <td>103N_mta_new_york_city_transit</td>
      <td>1.5</td>
      <td>mta_new_york_city_transit</td>
      <td>AFA21GEN-1091-Weekday-00_037150_1..N03R_mta_ne...</td>
      <td>1.0</td>
      <td>AFA21GEN-1091-Weekday-00_037150_1..N03R_mta_ne...</td>
      <td>1.0</td>
      <td>1_mta_new_york_city_transit</td>
      <td>transit</td>
      <td>...</td>
      <td>nan</td>
      <td>nan</td>
      <td>nan</td>
      <td>nan</td>
      <td>nan</td>
      <td>nan</td>
      <td>nan</td>
      <td>nan</td>
      <td>6098</td>
      <td>6096</td>
    </tr>
    <tr>
      <th>1</th>
      <td>103N_mta_new_york_city_transit</td>
      <td>101N_mta_new_york_city_transit</td>
      <td>1.5</td>
      <td>mta_new_york_city_transit</td>
      <td>AFA21GEN-1091-Weekday-00_037150_1..N03R_mta_ne...</td>
      <td>2.0</td>
      <td>AFA21GEN-1091-Weekday-00_037150_1..N03R_mta_ne...</td>
      <td>1.0</td>
      <td>1_mta_new_york_city_transit</td>
      <td>transit</td>
      <td>...</td>
      <td>nan</td>
      <td>nan</td>
      <td>nan</td>
      <td>nan</td>
      <td>nan</td>
      <td>nan</td>
      <td>nan</td>
      <td>nan</td>
      <td>6096</td>
      <td>6094</td>
    </tr>
    <tr>
      <th>2</th>
      <td>135S_mta_new_york_city_transit</td>
      <td>136S_mta_new_york_city_transit</td>
      <td>1.0</td>
      <td>mta_new_york_city_transit</td>
      <td>AFA21GEN-1091-Weekday-00_037300_1..S03R_mta_ne...</td>
      <td>1.0</td>
      <td>AFA21GEN-1091-Weekday-00_037300_1..S03R_mta_ne...</td>
      <td>1.0</td>
      <td>1_mta_new_york_city_transit</td>
      <td>transit</td>
      <td>...</td>
      <td>nan</td>
      <td>nan</td>
      <td>nan</td>
      <td>nan</td>
      <td>nan</td>
      <td>nan</td>
      <td>nan</td>
      <td>nan</td>
      <td>6159</td>
      <td>6161</td>
    </tr>
    <tr>
      <th>3</th>
      <td>136S_mta_new_york_city_transit</td>
      <td>137S_mta_new_york_city_transit</td>
      <td>1.5</td>
      <td>mta_new_york_city_transit</td>
      <td>AFA21GEN-1091-Weekday-00_037300_1..S03R_mta_ne...</td>
      <td>2.0</td>
      <td>AFA21GEN-1091-Weekday-00_037300_1..S03R_mta_ne...</td>
      <td>1.0</td>
      <td>1_mta_new_york_city_transit</td>
      <td>transit</td>
      <td>...</td>
      <td>nan</td>
      <td>nan</td>
      <td>nan</td>
      <td>nan</td>
      <td>nan</td>
      <td>nan</td>
      <td>nan</td>
      <td>nan</td>
      <td>6161</td>
      <td>6163</td>
    </tr>
    <tr>
      <th>4</th>
      <td>137S_mta_new_york_city_transit</td>
      <td>138S_mta_new_york_city_transit</td>
      <td>1.0</td>
      <td>mta_new_york_city_transit</td>
      <td>AFA21GEN-1091-Weekday-00_037300_1..S03R_mta_ne...</td>
      <td>3.0</td>
      <td>AFA21GEN-1091-Weekday-00_037300_1..S03R_mta_ne...</td>
      <td>1.0</td>
      <td>1_mta_new_york_city_transit</td>
      <td>transit</td>
      <td>...</td>
      <td>nan</td>
      <td>nan</td>
      <td>nan</td>
      <td>nan</td>
      <td>nan</td>
      <td>nan</td>
      <td>nan</td>
      <td>nan</td>
      <td>6163</td>
      <td>6165</td>
    </tr>
  </tbody>
</table>
<p>5 rows × 30 columns</p>
</div>




```python
urbanaccess_net.net_edges[urbanaccess_net.net_edges['net_type'] == 'transit'].head()
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
      <th>from</th>
      <th>to</th>
      <th>weight</th>
      <th>unique_agency_id</th>
      <th>unique_trip_id</th>
      <th>sequence</th>
      <th>edge_id</th>
      <th>route_type</th>
      <th>unique_route_id</th>
      <th>net_type</th>
      <th>...</th>
      <th>lanes</th>
      <th>maxspeed</th>
      <th>hgv</th>
      <th>hov</th>
      <th>area</th>
      <th>width</th>
      <th>est_width</th>
      <th>junction</th>
      <th>from_int</th>
      <th>to_int</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>104N_mta_new_york_city_transit</td>
      <td>103N_mta_new_york_city_transit</td>
      <td>1.5</td>
      <td>mta_new_york_city_transit</td>
      <td>AFA21GEN-1091-Weekday-00_037150_1..N03R_mta_ne...</td>
      <td>1.0</td>
      <td>AFA21GEN-1091-Weekday-00_037150_1..N03R_mta_ne...</td>
      <td>1.0</td>
      <td>1_mta_new_york_city_transit</td>
      <td>transit</td>
      <td>...</td>
      <td>nan</td>
      <td>nan</td>
      <td>nan</td>
      <td>nan</td>
      <td>nan</td>
      <td>nan</td>
      <td>nan</td>
      <td>nan</td>
      <td>6098</td>
      <td>6096</td>
    </tr>
    <tr>
      <th>1</th>
      <td>103N_mta_new_york_city_transit</td>
      <td>101N_mta_new_york_city_transit</td>
      <td>1.5</td>
      <td>mta_new_york_city_transit</td>
      <td>AFA21GEN-1091-Weekday-00_037150_1..N03R_mta_ne...</td>
      <td>2.0</td>
      <td>AFA21GEN-1091-Weekday-00_037150_1..N03R_mta_ne...</td>
      <td>1.0</td>
      <td>1_mta_new_york_city_transit</td>
      <td>transit</td>
      <td>...</td>
      <td>nan</td>
      <td>nan</td>
      <td>nan</td>
      <td>nan</td>
      <td>nan</td>
      <td>nan</td>
      <td>nan</td>
      <td>nan</td>
      <td>6096</td>
      <td>6094</td>
    </tr>
    <tr>
      <th>2</th>
      <td>135S_mta_new_york_city_transit</td>
      <td>136S_mta_new_york_city_transit</td>
      <td>1.0</td>
      <td>mta_new_york_city_transit</td>
      <td>AFA21GEN-1091-Weekday-00_037300_1..S03R_mta_ne...</td>
      <td>1.0</td>
      <td>AFA21GEN-1091-Weekday-00_037300_1..S03R_mta_ne...</td>
      <td>1.0</td>
      <td>1_mta_new_york_city_transit</td>
      <td>transit</td>
      <td>...</td>
      <td>nan</td>
      <td>nan</td>
      <td>nan</td>
      <td>nan</td>
      <td>nan</td>
      <td>nan</td>
      <td>nan</td>
      <td>nan</td>
      <td>6159</td>
      <td>6161</td>
    </tr>
    <tr>
      <th>3</th>
      <td>136S_mta_new_york_city_transit</td>
      <td>137S_mta_new_york_city_transit</td>
      <td>1.5</td>
      <td>mta_new_york_city_transit</td>
      <td>AFA21GEN-1091-Weekday-00_037300_1..S03R_mta_ne...</td>
      <td>2.0</td>
      <td>AFA21GEN-1091-Weekday-00_037300_1..S03R_mta_ne...</td>
      <td>1.0</td>
      <td>1_mta_new_york_city_transit</td>
      <td>transit</td>
      <td>...</td>
      <td>nan</td>
      <td>nan</td>
      <td>nan</td>
      <td>nan</td>
      <td>nan</td>
      <td>nan</td>
      <td>nan</td>
      <td>nan</td>
      <td>6161</td>
      <td>6163</td>
    </tr>
    <tr>
      <th>4</th>
      <td>137S_mta_new_york_city_transit</td>
      <td>138S_mta_new_york_city_transit</td>
      <td>1.0</td>
      <td>mta_new_york_city_transit</td>
      <td>AFA21GEN-1091-Weekday-00_037300_1..S03R_mta_ne...</td>
      <td>3.0</td>
      <td>AFA21GEN-1091-Weekday-00_037300_1..S03R_mta_ne...</td>
      <td>1.0</td>
      <td>1_mta_new_york_city_transit</td>
      <td>transit</td>
      <td>...</td>
      <td>nan</td>
      <td>nan</td>
      <td>nan</td>
      <td>nan</td>
      <td>nan</td>
      <td>nan</td>
      <td>nan</td>
      <td>nan</td>
      <td>6163</td>
      <td>6165</td>
    </tr>
  </tbody>
</table>
<p>5 rows × 30 columns</p>
</div>




```python
# Save network to root data folder under file name nyc_net.h5
ua.network.save_network(urbanaccess_network=urbanaccess_net,
                        filename='nyc_net.h5',
                        overwrite_key = True)
```

    Using existing data/nyc_net.h5 hdf5 store.
    Existing edges overwritten in data/nyc_net.h5 hdf5 store.
    Using existing data/nyc_net.h5 hdf5 store.
    Existing nodes overwritten in data/nyc_net.h5 hdf5 store.



```python
# Load nyc_net HDF5 network file
urbanaccess_net = ua.network.load_network(filename='nyc_net.h5')
```

    Successfully read store: data/nyc_net.h5 with the following keys: ['/edges', '/nodes']
    Successfully read store: data/nyc_net.h5 with the following keys: ['/edges', '/nodes']



```python
#Visualize pedestrian network
ua.plot.plot_net(nodes=urbanaccess_net.net_nodes,
                 edges=urbanaccess_net.net_edges,
                 bbox=(-74.25909,40.477399,-73.700181,40.916178),
                 fig_height=30, margin=0.02,
                 edge_color='#999999', edge_linewidth=1, edge_alpha=1,
                 node_color='black', node_size=1.1, node_alpha=1, node_edgecolor='none', node_zorder=3, nodes_only=False)
```

    Figure created. Took 113.02 seconds



    
![png](Advanced%20Geoinformatics%20Project_files/Advanced%20Geoinformatics%20Project_36_1.png)
    





    (<Figure size 2751.37x2160 with 1 Axes>, <AxesSubplot:>)




```python
# Visualize integrated transit and pedestrian network
edgecolor = ua.plot.col_colors(df=urbanaccess_net.net_edges, col='weight', cmap='gist_heat_r', num_bins=5)
ua.plot.plot_net(nodes=urbanaccess_net.net_nodes,
                 edges=urbanaccess_net.net_edges,
                 bbox=(-74.25909,40.477399,-73.700181,40.916178),
                 fig_height=30, margin=0.02,
                 edge_color=edgecolor, edge_linewidth=1, edge_alpha=0.7,
                 node_color='black', node_size=0, node_alpha=1, node_edgecolor='none', node_zorder=3, nodes_only=False)
```

    Figure created. Took 96.15 seconds



    
![png](Advanced%20Geoinformatics%20Project_files/Advanced%20Geoinformatics%20Project_37_1.png)
    





    (<Figure size 2751.37x2160 with 1 Axes>, <AxesSubplot:>)




```python
# Visualize the transit network including trains and buses, by network type (transit)
ua.plot.plot_net(nodes=urbanaccess_net.net_nodes,
                 edges=urbanaccess_net.net_edges[urbanaccess_net.net_edges['net_type']=='transit'],
                 bbox=None,
                 fig_height=30, margin=0.02,
                 edge_color='#999999', edge_linewidth=1, edge_alpha=1,
                 node_color='black', node_size=0, node_alpha=1, node_edgecolor='none', node_zorder=3, nodes_only=False)
```

    Figure created. Took 25.72 seconds



    
![png](Advanced%20Geoinformatics%20Project_files/Advanced%20Geoinformatics%20Project_38_1.png)
    





    (<Figure size 2904.78x2160 with 1 Axes>, <AxesSubplot:>)




```python
ua.gtfs.headways.headways(gtfsfeeds_df=loaded_feeds,
                          headway_timerange=['07:00:00','10:00:00'])
```

    Stop times from 07:00:00 to 10:00:00 successfully selected 455,243 records out of 2,433,652 total records (18.71 percent of total). Took 1.04 seconds
    Starting route stop headway calculation for 19,491 route stops...
    Route stop headway calculation complete. Took 36.43 seconds
    headway calculation complete. Took 42.79 seconds





    <urbanaccess.gtfs.gtfsfeeds_dataframe.urbanaccess_gtfs_df at 0x7fdb73988b50>




```python
loaded_feeds.headways.head()
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
      <th>count</th>
      <th>mean</th>
      <th>std</th>
      <th>min</th>
      <th>25%</th>
      <th>50%</th>
      <th>75%</th>
      <th>max</th>
      <th>unique_stop_id</th>
      <th>unique_route_id</th>
      <th>node_id_route</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>911504</th>
      <td>109.0</td>
      <td>1.568807</td>
      <td>3.356494</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>12.0</td>
      <td>100014_mta_new_york_city_transit</td>
      <td>BX26_mta_new_york_city_transit</td>
      <td>100014_mta_new_york_city_transit_BX26_mta_new_...</td>
    </tr>
    <tr>
      <th>911505</th>
      <td>109.0</td>
      <td>1.568807</td>
      <td>3.356494</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>12.0</td>
      <td>100014_mta_new_york_city_transit</td>
      <td>BX26_mta_new_york_city_transit</td>
      <td>100014_mta_new_york_city_transit_BX26_mta_new_...</td>
    </tr>
    <tr>
      <th>911506</th>
      <td>109.0</td>
      <td>1.568807</td>
      <td>3.356494</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>12.0</td>
      <td>100014_mta_new_york_city_transit</td>
      <td>BX26_mta_new_york_city_transit</td>
      <td>100014_mta_new_york_city_transit_BX26_mta_new_...</td>
    </tr>
    <tr>
      <th>911507</th>
      <td>109.0</td>
      <td>1.568807</td>
      <td>3.356494</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>12.0</td>
      <td>100014_mta_new_york_city_transit</td>
      <td>BX26_mta_new_york_city_transit</td>
      <td>100014_mta_new_york_city_transit_BX26_mta_new_...</td>
    </tr>
    <tr>
      <th>911508</th>
      <td>109.0</td>
      <td>1.568807</td>
      <td>3.356494</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>12.0</td>
      <td>100014_mta_new_york_city_transit</td>
      <td>BX26_mta_new_york_city_transit</td>
      <td>100014_mta_new_york_city_transit_BX26_mta_new_...</td>
    </tr>
  </tbody>
</table>
</div>




```python
ua.network.integrate_network(urbanaccess_network=urbanaccess_net,
                             headways=True,
                             urbanaccess_gtfsfeeds_df=loaded_feeds,
                             headway_statistic='mean')
```

    Loaded UrbanAccess network components comprised of:
         Transit: 13,513 nodes and 440,063 edges;
         OSM: 403,317 nodes and 1,215,368 edges
    routes successfully joined to transit nodes. Took 0.81 seconds
    Connector edges between the OSM and transit network nodes successfully completed. Took 6.67 seconds
    mean route stop headway will be used for pedestrian to transit edge impedance.
    Headway impedance calculation completed. Took 1.12 seconds
    Edge and node tables formatted for Pandana with integer node ids: id_int, to_int, and from_int. Took 37.52 seconds
    Network edge and node network integration completed successfully resulting in a total of 422,808 nodes and 3,788,597 edges:
         Transit: 19,491 nodes 440,063 edges;
         OSM: 403,317 nodes 1,215,368 edges; and
         OSM/Transit connector: 2,133,166 edges.





    <urbanaccess.network.urbanaccess_network at 0x7fdb73b84400>




```python
# Visualize integrated transit and pedestrian network, weighted by transit route frequency
edgecolor = ua.plot.col_colors(df=urbanaccess_net.net_edges, col='weight', cmap='gist_heat_r', num_bins=5)
ua.plot.plot_net(nodes=urbanaccess_net.net_nodes,
                 edges=urbanaccess_net.net_edges,
                 bbox=bbox,
                 fig_height=30, margin=0.02,
                 edge_color=edgecolor, edge_linewidth=1, edge_alpha=0.7,
                 node_color='black', node_size=0, node_alpha=1, node_edgecolor='none', node_zorder=3, nodes_only=False)
```

    Figure created. Took 227.48 seconds



    
![png](Advanced%20Geoinformatics%20Project_files/Advanced%20Geoinformatics%20Project_42_1.png)
    





    (<Figure size 2751.37x2160 with 1 Axes>, <AxesSubplot:>)




```python
# Load NY state block data
nybg = pd.read_csv("NY_Blocks.csv")
nybg
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
      <th>GEOID</th>
      <th>ALAND20</th>
      <th>AWATER20</th>
      <th>x</th>
      <th>y</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>360894925011010</td>
      <td>0</td>
      <td>21972</td>
      <td>-75.182586</td>
      <td>44.284402</td>
    </tr>
    <tr>
      <th>1</th>
      <td>360894926002022</td>
      <td>113014</td>
      <td>0</td>
      <td>-75.216830</td>
      <td>44.363189</td>
    </tr>
    <tr>
      <th>2</th>
      <td>360894926002034</td>
      <td>50829</td>
      <td>0</td>
      <td>-75.239639</td>
      <td>44.356982</td>
    </tr>
    <tr>
      <th>3</th>
      <td>360894926002029</td>
      <td>397018</td>
      <td>0</td>
      <td>-75.224958</td>
      <td>44.348747</td>
    </tr>
    <tr>
      <th>4</th>
      <td>360894926002010</td>
      <td>58704</td>
      <td>0</td>
      <td>-75.198572</td>
      <td>44.347600</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>288814</th>
      <td>360290091091003</td>
      <td>36854</td>
      <td>0</td>
      <td>-78.765500</td>
      <td>42.990761</td>
    </tr>
    <tr>
      <th>288815</th>
      <td>360290080024000</td>
      <td>12580</td>
      <td>0</td>
      <td>-78.852498</td>
      <td>42.985665</td>
    </tr>
    <tr>
      <th>288816</th>
      <td>360290150021039</td>
      <td>655048</td>
      <td>0</td>
      <td>-78.498732</td>
      <td>42.748689</td>
    </tr>
    <tr>
      <th>288817</th>
      <td>360290154021007</td>
      <td>0</td>
      <td>15105</td>
      <td>-78.970125</td>
      <td>42.714141</td>
    </tr>
    <tr>
      <th>288818</th>
      <td>360290175022047</td>
      <td>582261</td>
      <td>0</td>
      <td>-78.821359</td>
      <td>42.489494</td>
    </tr>
  </tbody>
</table>
<p>288819 rows × 5 columns</p>
</div>




```python
nyjobs = pd.read_csv("NYC_Jobs.csv")
nyjobs
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
      <th>GEOID</th>
      <th>Jobs</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>360010001001008</td>
      <td>19</td>
    </tr>
    <tr>
      <th>1</th>
      <td>360010001001009</td>
      <td>15</td>
    </tr>
    <tr>
      <th>2</th>
      <td>360010001001010</td>
      <td>10</td>
    </tr>
    <tr>
      <th>3</th>
      <td>360010001001011</td>
      <td>51</td>
    </tr>
    <tr>
      <th>4</th>
      <td>360010001001012</td>
      <td>38</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>231550</th>
      <td>361231505004017</td>
      <td>4</td>
    </tr>
    <tr>
      <th>231551</th>
      <td>361231505004019</td>
      <td>6</td>
    </tr>
    <tr>
      <th>231552</th>
      <td>361231505004020</td>
      <td>1</td>
    </tr>
    <tr>
      <th>231553</th>
      <td>361231505004021</td>
      <td>2</td>
    </tr>
    <tr>
      <th>231554</th>
      <td>361231505004022</td>
      <td>3</td>
    </tr>
  </tbody>
</table>
<p>231555 rows × 2 columns</p>
</div>




```python
df = pd.merge(nyjobs, 
              nybg,
              on=['GEOID','GEOID'])
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
      <th>GEOID</th>
      <th>Jobs</th>
      <th>ALAND20</th>
      <th>AWATER20</th>
      <th>x</th>
      <th>y</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>360010001001008</td>
      <td>19</td>
      <td>7783</td>
      <td>0</td>
      <td>-73.736149</td>
      <td>42.676900</td>
    </tr>
    <tr>
      <th>1</th>
      <td>360010001001009</td>
      <td>15</td>
      <td>6149</td>
      <td>0</td>
      <td>-73.737863</td>
      <td>42.677447</td>
    </tr>
    <tr>
      <th>2</th>
      <td>360010001001010</td>
      <td>10</td>
      <td>37825</td>
      <td>0</td>
      <td>-73.740476</td>
      <td>42.677820</td>
    </tr>
    <tr>
      <th>3</th>
      <td>360010001001011</td>
      <td>51</td>
      <td>16416</td>
      <td>0</td>
      <td>-73.741709</td>
      <td>42.678003</td>
    </tr>
    <tr>
      <th>4</th>
      <td>360010001001012</td>
      <td>38</td>
      <td>12999</td>
      <td>0</td>
      <td>-73.739613</td>
      <td>42.677171</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Convert dataframe into .h5 file
df = pd.DataFrame(df)
df.to_hdf('nycblocks.h5', key='df', mode='a')
```


```python
blocks = pd.read_hdf('nycblocks.h5')
# remove blocks that contain all water
blocks = blocks[blocks['ALAND20'] != 0]
print('Total number of blocks: {:,}'.format(len(blocks)))
blocks.head()
```

    Total number of blocks: 148,432





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
      <th>GEOID</th>
      <th>Jobs</th>
      <th>ALAND20</th>
      <th>AWATER20</th>
      <th>x</th>
      <th>y</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>360010001001008</td>
      <td>19</td>
      <td>7783</td>
      <td>0</td>
      <td>-73.736149</td>
      <td>42.676900</td>
    </tr>
    <tr>
      <th>1</th>
      <td>360010001001009</td>
      <td>15</td>
      <td>6149</td>
      <td>0</td>
      <td>-73.737863</td>
      <td>42.677447</td>
    </tr>
    <tr>
      <th>2</th>
      <td>360010001001010</td>
      <td>10</td>
      <td>37825</td>
      <td>0</td>
      <td>-73.740476</td>
      <td>42.677820</td>
    </tr>
    <tr>
      <th>3</th>
      <td>360010001001011</td>
      <td>51</td>
      <td>16416</td>
      <td>0</td>
      <td>-73.741709</td>
      <td>42.678003</td>
    </tr>
    <tr>
      <th>4</th>
      <td>360010001001012</td>
      <td>38</td>
      <td>12999</td>
      <td>0</td>
      <td>-73.739613</td>
      <td>42.677171</td>
    </tr>
  </tbody>
</table>
</div>




```python
lng_max, lat_min, lng_min, lat_max = bbox
outside_bbox = blocks.loc[~(((lng_max < blocks["x"]) & (blocks["x"] < lng_min)) & ((lat_min < blocks["y"]) & (blocks["y"] < lat_max)))]
blocks = blocks.drop(outside_bbox.index)
print('Total number of subset blocks: {:,}'.format(len(blocks)))
```

    Total number of subset blocks: 28,358



```python
# Plot merged block dataframe
blocks.plot(kind='scatter', x='x', y='y', s=0.3)
```




    <AxesSubplot:xlabel='x', ylabel='y'>




    
![png](Advanced%20Geoinformatics%20Project_files/Advanced%20Geoinformatics%20Project_49_1.png)
    



```python
# Initialize Pandana network using combined transit and pedestrian network
s_time = time.time()
transit_ped_net = pdna.Network(urbanaccess_net.net_nodes["x"],
                               urbanaccess_net.net_nodes["y"],
                               urbanaccess_net.net_edges["from_int"],
                               urbanaccess_net.net_edges["to_int"],
                               urbanaccess_net.net_edges[["weight"]], 
                               twoway=True)
print('Took {:,.2f} seconds'.format(time.time() - s_time))
```

    Generating contraction hierarchies with 8 threads.
    Setting CH node vector of size 422808
    Setting CH edge vector of size 3788597
    Range graph removed 6304476 edges of 7577194
    . 10% . 20% . 30% . 40% . 50% . 60% . 70% . 80% . 90% . 100%
    Took 46.89 seconds



```python
# Set blocks onto network
blocks['node_id'] = transit_ped_net.get_node_ids(blocks['x'], blocks['y'])
```


```python
transit_ped_net.set(blocks.node_id, variable = blocks.Jobs, name='Jobs')
```


```python
s_time = time.time()
jobs_45 = transit_ped_net.aggregate(45, type='sum', decay='linear', name='Jobs')
jobs_30 = transit_ped_net.aggregate(30, type='sum', decay='linear', name='Jobs')
jobs_15 = transit_ped_net.aggregate(15, type='sum', decay='linear', name='Jobs')
print('Took {:,.2f} seconds'.format(time.time() - s_time))
```

    Took 1,575.08 seconds



```python
print(jobs_30)
```

    id_int
    1         198208.416811
    2         175368.463487
    3         315693.745675
    4         314908.160651
    5         347635.869061
                  ...      
    422804      2332.081280
    422805      2492.003429
    422806      2506.295022
    422807      2618.433428
    422808      2631.997514
    Length: 422808, dtype: float64



```python
s_time = time.time()

fig = plt.subplots(figsize=(20,20))

data_crs = ccrs.PlateCarree()
ax = plt.axes(projection=ccrs.epsg(2263))
ax.add_feature(cartopy.feature.GSHHSFeature(scale='full'), edgecolor='grey')

plt.scatter(transit_ped_net.nodes_df.x, transit_ped_net.nodes_df.y, 
            c=jobs_15, s=4, cmap='gist_heat_r', edgecolor='none', transform=data_crs)
cb = plt.colorbar()

print('Took {:,.2f} seconds'.format(time.time() - s_time))
```

    Took 1.11 seconds



    
![png](Advanced%20Geoinformatics%20Project_files/Advanced%20Geoinformatics%20Project_55_1.png)
    



```python
s_time = time.time()

fig = plt.subplots(figsize=(20,20))

data_crs = ccrs.PlateCarree()
ax = plt.axes(projection=ccrs.epsg(2263))
ax.add_feature(cartopy.feature.GSHHSFeature(scale='full'), edgecolor='grey')

plt.scatter(transit_ped_net.nodes_df.x, transit_ped_net.nodes_df.y, 
            c=jobs_30, s=4, cmap='gist_heat_r', edgecolor='none', transform=data_crs)
cb = plt.colorbar()

print('Took {:,.2f} seconds'.format(time.time() - s_time))
```

    Took 0.89 seconds



    
![png](Advanced%20Geoinformatics%20Project_files/Advanced%20Geoinformatics%20Project_56_1.png)
    



```python
s_time = time.time()

fig = plt.subplots(figsize=(20,20))

data_crs = ccrs.PlateCarree()
ax = plt.axes(projection=ccrs.epsg(2263))
ax.add_feature(cartopy.feature.GSHHSFeature(scale='full'), edgecolor='grey')

plt.scatter(transit_ped_net.nodes_df.x, transit_ped_net.nodes_df.y, 
            c=jobs_45, s=4, cmap='gist_heat_r', edgecolor='none', transform=data_crs)
cb = plt.colorbar()

print('Took {:,.2f} seconds'.format(time.time() - s_time))
```

    Took 0.72 seconds



    
![png](Advanced%20Geoinformatics%20Project_files/Advanced%20Geoinformatics%20Project_57_1.png)
    

