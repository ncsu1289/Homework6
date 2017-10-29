
Unit 6 | Assignment - What's the Weather Like?
Background
Whether financial, political, or social -- data's true power lies in its ability to answer 
questions definitively. So let's take what you've learned about Python requests, APIs, and JSON
traversals to answer a fundamental question: "What's the weather like as we approach the equator?"
    
Now, we know what you may be thinking: "Duh. It gets hotter..."
    
But, if pressed, how would you prove it?

Equator

WeatherPy

In this example, you'll be creating a Python script to visualize the weather of 500+ cities 
across the world of varying distance from the equator. To accomplish this, you'll be utilizing 
a simple Python library, the OpenWeatherMap API, and a little common sense to create a 
representative model of weather across world cities.

Your objective is to build a series of scatter plots to showcase the following relationships:
    
    √Temperature (F) vs. Latitude
    √Humidity (%) vs. Latitude
    √Cloudiness (%) vs. Latitude
    √Wind Speed (mph) vs. Latitude

Your final notebook must:
    √ Randomly select at least 500 unique (non-repeat) cities based on latitude and longitude.

    √ Perform a weather check on each of the cities using a series of successive API calls.

    √Include a print log of each city as it's being processed with the city number, city name, and 
        requested URL.

Save both a CSV of:
    √ all data retrieved and 
    √ png images for each scatter plot.

As final considerations:
    √ You must use the Matplotlib and Seaborn libraries.
    √ You must include a written description of three observable trends based on the data.
    √ You must use proper labeling of your plots, including aspects like: Plot Titles 
        (with date of analysis) and Axes Labels.
        
You must include an exported markdown version of your Notebook called  README.md in your GitHub 
repository.

See Example Solution for a reference on expected format.

Hints and Considerations

You may want to start this assignment by refreshing yourself on 4th grade geography, in 
particular, the geographic coordinate system.

Next, spend the requisite time necessary to study the OpenWeatherMap API. Based on your initial
study, you should be able to answer basic questions about the API: 
    Where do you request the APIkey? 
    Which Weather API in particular will you need? 
    What URL endpoints does it expect? 
    What JSON structure does it respond with? 
    Before you write a line of code, you should be aiming to have a crystal clear understanding
    of your intended outcome.
    
Though we've never worked with the citipy Python library, push yourself to decipher how it 
works,and why it might be relevant. Before you try to incorporate the library into your 
analysis, start by creating simple test cases outside your main script to confirm that you are 
using it correctly. Too often, when introduced to a new library, students get bogged down by the
most minor of errors -- spending hours investigating their entire code -- when, in fact, a 
simple and focused test would have shown their basic utilization of the library was wrong from 
the start. Don't let this be you!

Part of our expectation in this challenge is that you will use critical thinking skills to 
understand how and why we're recommending the tools we are. 
    What is Citipy for? 
    Why would you use it in conjunction with the OpenWeatherMap API? 
    How would you do so?
    
In building your script, pay attention to the cities you are using in your query pool. 
Are you getting coverage of the full gamut of latitudes and longitudes? 
Or are you simply choosing 500 cities concentrated in one region of the world? 
Even if you were a geographic genius, simply rattling 500 cities based on your human selection 
would create a biased dataset. Be thinking of how you should counter this. 
(Hint: Consider the full range of latitudes).
Lastly, remember -- this is a challenging activity. Push yourself! If you complete this task, 
then you can safely say that you've gained a strong mastery of the core foundations of data 
analytics and it will only go better from here. Good luck!

#Note: I downloaded and installed citipy, but could not get the function to work.  So, I used the 
raw data from citipy and created my own random city selection.

#OBSERVATIONS:
1 - There is a clear picture of a correspondence between Temperature and Latitude.  Evidenced by the ScatterPlot of
        Temperature v. Latitute.  As progress is made towards the Equator, the Temperature rises and the trend appears 
        to repeat itself as we progress towards the South Pole.
2 -  % Humidity seems to want to follow a similar trend as evidenced by the Scatter Plot of humidity v. Latitute. 
            This observation is made given that while the plot reflects numerous points of data, that data tends to 
            accumulate or group together more towards the higher humidity area (note red color on Seaborn scatter
            plot)
3 - The other Scatter Plots do not lend themselves to forcasting a trend.  The data may be skewed upon the time of
        observation, which may be further skewed based upon season (tilt of the earth) or other major weather issues.
4 - Time of day also plays a part in the outcome with the excdeption of max Temp.  Throughout the various queries, the 
    data would appear to change based upon early morning vs. late evening (with the exception of the basic curve
    structure of Max temperature vs. Latitude.


```python
import csv
import random
import pandas as pd
import numpy as np
import json
import requests as req
import matplotlib.pyplot as plt
import seaborn as sns
from matplotlib.colors import ListedColormap
import time
import datetime
from datetime import date
```


```python
# Save config information
api_key = "555f071860e562f7871e3f299ddd26ba"
url1 = "http://api.openweathermap.org/data/2.5/weather?"
city_path="../Homework6/Resources/worldcities.csv"
units="imperial"


```


```python
#print(url1)
```


```python
#Partial query
#ran_url= url1+ "appid=" +api_key +"&units=" + units
#ran_url used as basis for trying to retrieve data via latitude and longitude
ran_url2=url1+ "appid=" +api_key +"&units=" + units +"&q="
```


```python
#print(ran_url2)
```


```python
cities_check=pd.read_csv(city_path,encoding="utf=8")

#cities_check.keys()

```


```python
#cities_check.head()
```


```python
#Random city call
cc=cities_check.iloc[28795,:]
#cc.head()
```


```python
#Random city call
#checking latitude data
cc=cities_check.iloc[40000,:]
#cc.head()
```


```python
#Testing recognition of a single city name (capitalized)
tester3=req.get(ran_url2 + "Baltimore").json()
#tester3
```


```python
#Testing city and country seperated by a comma (lower case)
tester2=req.get(ran_url2 + "mabilo,ph").json()
#tester2
```


```python
#Testing recognigtion of a city (lower case) where the city name is more than one work, e.g., Los Angeles, New York
tester4=req.get(ran_url2 +"andorra la vella").json()
#tester4
```


```python
#Looking for cities that might have empty rows
cities_batch=cities_check.count()
#cities_batch
```


```python
#Removing cities with empty rows
cities_batch=cities_check.dropna(how="any")
#cities_batch.count()
```

#experimenting with calling cities by latitute and longitude - Not successful yet.
ran_city_weather=[]
request_cc_weather=req.get(ran_url + lat=11.6784&lon=122.408).json()
ran_city_weather.append(request_cc_weather)
#ran_city_weather


```python
#Main code for looping and pulling information for 500 random cities, storing the country index and dats 
#   within a list "sum_ran_city"
#Apparently I could have used random.choice instead of selecting a random index (quicker?)
count1=1
ran_city=[]
ran_city_weather=[]
ran_city_time=[]
sum_ran_city=[]
record_process=[]
print("Beginning Data Retrieval")
print("------------------------")
for i in range(550):
    x=random.randint(0,len(cities_batch))
    #print(x)
    #print(cities_batch.iloc[x,:])
    #print(cities_check.iloc[x,:])
    ran_city=(cities_batch.iloc[x,:])
    sum_ran_city.append(ran_city)
    ran_name=ran_city["City"]
    ran_country=ran_city["Country"]
    print("Processing Record "+ str(i) + "|" + ran_name)
    print(ran_url2 + ran_name +","+ran_country)
    record_process.append("Processing Record "+ str(i) + "|" + ran_name)
    record_process.append(ran_url2 + ran_name +","+ran_country)
    result_of_query=req.get(ran_url2 + ran_name +","+ran_country).json()
    val=result_of_query["dt"]
    daytime_info=datetime.datetime.fromtimestamp(int(val)).strftime('%Y-%m-%d %H:%M:%S %p')
    result_of_query["dt"]=daytime_info
    ran_city_weather.append(result_of_query)
```

    Beginning Data Retrieval
    ------------------------
    Processing Record 0|nabari
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=nabari,jp
    Processing Record 1|volya
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=volya,ru
    Processing Record 2|eregli
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=eregli,tr
    Processing Record 3|carlogani
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=carlogani,ro
    Processing Record 4|rome
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=rome,us
    Processing Record 5|otradnoye
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=otradnoye,ru
    Processing Record 6|lodja
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=lodja,cd
    Processing Record 7|kaitong
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=kaitong,cn
    Processing Record 8|carvin
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=carvin,fr
    Processing Record 9|ilongero
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=ilongero,tz
    Processing Record 10|hoogstraten
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=hoogstraten,be
    Processing Record 11|wabrzezno
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=wabrzezno,pl
    Processing Record 12|fuchu
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=fuchu,jp
    Processing Record 13|chandla
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=chandla,in
    Processing Record 14|loos
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=loos,fr
    Processing Record 15|rutherford
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=rutherford,us
    Processing Record 16|racoasa
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=racoasa,ro
    Processing Record 17|jumilla
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=jumilla,es
    Processing Record 18|almere
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=almere,nl
    Processing Record 19|braesti
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=braesti,ro
    Processing Record 20|muncar
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=muncar,id
    Processing Record 21|savyon
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=savyon,il
    Processing Record 22|ilihan
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=ilihan,ph
    Processing Record 23|secuieni
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=secuieni,ro
    Processing Record 24|batala
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=batala,in
    Processing Record 25|sofiyivka
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=sofiyivka,ua
    Processing Record 26|la plata
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=la plata,us
    Processing Record 27|aleksandrovskoye
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=aleksandrovskoye,ru
    Processing Record 28|chore
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=chore,py
    Processing Record 29|jakar
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=jakar,bt
    Processing Record 30|lorena
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=lorena,br
    Processing Record 31|ellezelles
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=ellezelles,be
    Processing Record 32|traian
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=traian,ro
    Processing Record 33|saint-maurice
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=saint-maurice,fr
    Processing Record 34|chimoio
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=chimoio,mz
    Processing Record 35|rimoc
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=rimoc,hu
    Processing Record 36|jiexiu
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=jiexiu,cn
    Processing Record 37|maugat
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=maugat,ph
    Processing Record 38|kinamayan
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=kinamayan,ph
    Processing Record 39|montebello
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=montebello,co
    Processing Record 40|paltinis
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=paltinis,ro
    Processing Record 41|shilovo
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=shilovo,ru
    Processing Record 42|college place
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=college place,us
    Processing Record 43|huwei
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=huwei,tw
    Processing Record 44|indi
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=indi,in
    Processing Record 45|sibucao
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=sibucao,ph
    Processing Record 46|tahlequah
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=tahlequah,us
    Processing Record 47|west palm beach
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=west palm beach,us
    Processing Record 48|cap estate
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=cap estate,lc
    Processing Record 49|ratanpur
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=ratanpur,in
    Processing Record 50|kisarazu
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=kisarazu,jp
    Processing Record 51|ribeira grande
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=ribeira grande,pt
    Processing Record 52|plasy
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=plasy,cz
    Processing Record 53|galiwinku
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=galiwinku,au
    Processing Record 54|ha noi
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=ha noi,vn
    Processing Record 55|hawalli
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=hawalli,kw
    Processing Record 56|ericeira
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=ericeira,pt
    Processing Record 57|blagoveshchensk
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=blagoveshchensk,ru
    Processing Record 58|cossato
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=cossato,it
    Processing Record 59|manay
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=manay,ph
    Processing Record 60|notre-dame-de-gravenchon
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=notre-dame-de-gravenchon,fr
    Processing Record 61|palmeiras de goias
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=palmeiras de goias,br
    Processing Record 62|jalu
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=jalu,ly
    Processing Record 63|grosbous
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=grosbous,lu
    Processing Record 64|sinayawan
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=sinayawan,ph
    Processing Record 65|hennenman
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=hennenman,za
    Processing Record 66|siempre viva
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=siempre viva,ph
    Processing Record 67|miandoab
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=miandoab,ir
    Processing Record 68|chester
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=chester,ca
    Processing Record 69|inverkeithing
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=inverkeithing,gb
    Processing Record 70|pembroke pines
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=pembroke pines,us
    Processing Record 71|ondjiva
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=ondjiva,ao
    Processing Record 72|minusinsk
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=minusinsk,ru
    Processing Record 73|morkov
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=morkov,cz
    Processing Record 74|murrhardt
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=murrhardt,de
    Processing Record 75|manchester
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=manchester,us
    Processing Record 76|turka
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=turka,ru
    Processing Record 77|agua buena
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=agua buena,pa
    Processing Record 78|galesti
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=galesti,ro
    Processing Record 79|mazabuka
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=mazabuka,zm
    Processing Record 80|nove hrady
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=nove hrady,cz
    Processing Record 81|pinahan
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=pinahan,ph
    Processing Record 82|didao
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=didao,cn
    Processing Record 83|lingating
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=lingating,ph
    Processing Record 84|saint petersburg
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=saint petersburg,ru
    Processing Record 85|paltinoasa
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=paltinoasa,ro
    Processing Record 86|cisaat
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=cisaat,id
    Processing Record 87|valenton
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=valenton,fr
    Processing Record 88|maneromango
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=maneromango,tz
    Processing Record 89|windsor
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=windsor,ca
    Processing Record 90|riverdale
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=riverdale,us
    Processing Record 91|plataiai
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=plataiai,gr
    Processing Record 92|zarautz
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=zarautz,es
    Processing Record 93|qiryat motzkin
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=qiryat motzkin,il
    Processing Record 94|elyria
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=elyria,us
    Processing Record 95|nuevo guarare
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=nuevo guarare,pa
    Processing Record 96|tabas
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=tabas,ir
    Processing Record 97|triel-sur-seine
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=triel-sur-seine,fr
    Processing Record 98|sunzha
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=sunzha,ru
    Processing Record 99|millau
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=millau,fr
    Processing Record 100|digos
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=digos,ph
    Processing Record 101|roth
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=roth,de
    Processing Record 102|rajnagar
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=rajnagar,in
    Processing Record 103|jaynagar
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=jaynagar,in
    Processing Record 104|biharkeresztes
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=biharkeresztes,hu
    Processing Record 105|ramnicu valcea
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=ramnicu valcea,ro
    Processing Record 106|gilau
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=gilau,ro
    Processing Record 107|bom jesus do galho
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=bom jesus do galho,br
    Processing Record 108|stanislav
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=stanislav,ua
    Processing Record 109|bala
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=bala,ro
    Processing Record 110|motrico
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=motrico,ph
    Processing Record 111|pueblo nuevo vinas
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=pueblo nuevo vinas,gt
    Processing Record 112|gandai
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=gandai,in
    Processing Record 113|northam
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=northam,au
    Processing Record 114|czersk
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=czersk,pl
    Processing Record 115|pensilvania
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=pensilvania,co
    Processing Record 116|nuevo colon
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=nuevo colon,co
    Processing Record 117|lipin bor
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=lipin bor,ru
    Processing Record 118|east haven
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=east haven,us
    Processing Record 119|tsukuba
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=tsukuba,jp
    Processing Record 120|balete
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=balete,ph
    Processing Record 121|vapnyarka
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=vapnyarka,ua
    Processing Record 122|ajka
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=ajka,hu
    Processing Record 123|busay
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=busay,ph
    Processing Record 124|northfield
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=northfield,us
    Processing Record 125|te anau
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=te anau,nz
    Processing Record 126|boyle
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=boyle,ie
    Processing Record 127|vasterhaninge
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=vasterhaninge,se
    Processing Record 128|colleyville
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=colleyville,us
    Processing Record 129|gagnoa
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=gagnoa,ci
    Processing Record 130|sunrise manor
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=sunrise manor,us
    Processing Record 131|lompoc
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=lompoc,us
    Processing Record 132|tyumen
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=tyumen,ru
    Processing Record 133|linares
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=linares,es
    Processing Record 134|lake elsinore
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=lake elsinore,us
    Processing Record 135|kampala
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=kampala,ug
    Processing Record 136|princes town
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=princes town,tt
    Processing Record 137|port orange
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=port orange,us
    Processing Record 138|ifanadiana
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=ifanadiana,mg
    Processing Record 139|guiong
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=guiong,ph
    Processing Record 140|colina
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=colina,br
    Processing Record 141|yudong
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=yudong,cn
    Processing Record 142|pasaco
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=pasaco,gt
    Processing Record 143|rosehearty
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=rosehearty,gb
    Processing Record 144|adalaj
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=adalaj,in
    Processing Record 145|ralivka
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=ralivka,ua
    Processing Record 146|teignmouth
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=teignmouth,gb
    Processing Record 147|santa barbara
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=santa barbara,gt
    Processing Record 148|chachoengsao
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=chachoengsao,th
    Processing Record 149|hagersville
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=hagersville,ca
    Processing Record 150|masalipit
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=masalipit,ph
    Processing Record 151|lourdes
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=lourdes,ph
    Processing Record 152|bunawan
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=bunawan,ph
    Processing Record 153|alcaniz
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=alcaniz,es
    Processing Record 154|lucban
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=lucban,ph
    Processing Record 155|petange
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=petange,lu
    Processing Record 156|bristol
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=bristol,us
    Processing Record 157|bosanska kostajnica
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=bosanska kostajnica,ba
    Processing Record 158|oracon
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=oracon,ph
    Processing Record 159|malbago
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=malbago,ph
    Processing Record 160|eisenhuttenstadt
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=eisenhuttenstadt,de
    Processing Record 161|sunderland
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=sunderland,gb
    Processing Record 162|araguacu
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=araguacu,br
    Processing Record 163|maribondo
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=maribondo,br
    Processing Record 164|kitcharao
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=kitcharao,ph
    Processing Record 165|kunda
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=kunda,in
    Processing Record 166|topolobampo
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=topolobampo,mx
    Processing Record 167|polican
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=polican,al
    Processing Record 168|helsinge
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=helsinge,dk
    Processing Record 169|brookhaven
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=brookhaven,us
    Processing Record 170|wijnegem
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=wijnegem,be
    Processing Record 171|el paraiso
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=el paraiso,hn
    Processing Record 172|poco branco
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=poco branco,br
    Processing Record 173|east moline
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=east moline,us
    Processing Record 174|tandayag
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=tandayag,ph
    Processing Record 175|krithia
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=krithia,gr
    Processing Record 176|quatre bornes
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=quatre bornes,mu
    Processing Record 177|pagadian
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=pagadian,ph
    Processing Record 178|moskovskiy
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=moskovskiy,tj
    Processing Record 179|moravsky beroun
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=moravsky beroun,cz
    Processing Record 180|horizontina
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=horizontina,br
    Processing Record 181|yancheng
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=yancheng,cn
    Processing Record 182|nea mikhaniona
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=nea mikhaniona,gr
    Processing Record 183|patti
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=patti,in
    Processing Record 184|kingsgate
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=kingsgate,us
    Processing Record 185|stadskanaal
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=stadskanaal,nl
    Processing Record 186|nim ka thana
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=nim ka thana,in
    Processing Record 187|vista hermosa
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=vista hermosa,mx
    Processing Record 188|ecublens
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=ecublens,ch
    Processing Record 189|megali vrisi
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=megali vrisi,gr
    Processing Record 190|bajos de haina
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=bajos de haina,do
    Processing Record 191|noci
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=noci,it
    Processing Record 192|hinganghat
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=hinganghat,in
    Processing Record 193|balch springs
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=balch springs,us
    Processing Record 194|mabilao
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=mabilao,ph
    Processing Record 195|cermeno
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=cermeno,pa
    Processing Record 196|mbanza-ngungu
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=mbanza-ngungu,cd
    Processing Record 197|senov
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=senov,cz
    Processing Record 198|san giovanni valdarno
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=san giovanni valdarno,it
    Processing Record 199|rewa
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=rewa,in
    Processing Record 200|tyniste nad orlici
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=tyniste nad orlici,cz
    Processing Record 201|jobner
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=jobner,in
    Processing Record 202|pasichna
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=pasichna,ua
    Processing Record 203|zamboanga
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=zamboanga,ph
    Processing Record 204|jua
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=jua,in
    Processing Record 205|west allis
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=west allis,us
    Processing Record 206|onokhoy
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=onokhoy,ru
    Processing Record 207|aitolikon
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=aitolikon,gr
    Processing Record 208|sugcad
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=sugcad,ph
    Processing Record 209|wollerau
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=wollerau,ch
    Processing Record 210|nakhodka
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=nakhodka,ru
    Processing Record 211|temerloh
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=temerloh,my
    Processing Record 212|sapouy
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=sapouy,bf
    Processing Record 213|charlestown
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=charlestown,kn
    Processing Record 214|darmstadt
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=darmstadt,de
    Processing Record 215|poste de flacq
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=poste de flacq,mu
    Processing Record 216|sotara
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=sotara,co
    Processing Record 217|kuvshinovo
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=kuvshinovo,ru
    Processing Record 218|nigrita
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=nigrita,gr
    Processing Record 219|daud khel
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=daud khel,pk
    Processing Record 220|san rafael
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=san rafael,ph
    Processing Record 221|dolgoye
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=dolgoye,ru
    Processing Record 222|taua
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=taua,br
    Processing Record 223|cuapiaxtla
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=cuapiaxtla,mx
    Processing Record 224|gharb
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=gharb,mt
    Processing Record 225|marshall
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=marshall,us
    Processing Record 226|aiquile
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=aiquile,bo
    Processing Record 227|polikhni
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=polikhni,gr
    Processing Record 228|maluay
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=maluay,ph
    Processing Record 229|pula
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=pula,hr
    Processing Record 230|ausa
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=ausa,in
    Processing Record 231|malicboy
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=malicboy,ph
    Processing Record 232|jambol
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=jambol,bg
    Processing Record 233|kalemie
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=kalemie,cd
    Processing Record 234|qorveh
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=qorveh,ir
    Processing Record 235|kopong
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=kopong,bw
    Processing Record 236|belmonte mezzagno
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=belmonte mezzagno,it
    Processing Record 237|ono
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=ono,jp
    Processing Record 238|newton
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=newton,us
    Processing Record 239|koberice
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=koberice,cz
    Processing Record 240|binzhou
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=binzhou,cn
    Processing Record 241|aramus
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=aramus,am
    Processing Record 242|ojhar
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=ojhar,in
    Processing Record 243|pinamopoan
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=pinamopoan,ph
    Processing Record 244|vershino-shakhtaminskiy
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=vershino-shakhtaminskiy,ru
    Processing Record 245|ajoloapan
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=ajoloapan,mx
    Processing Record 246|chilpancingo
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=chilpancingo,mx
    Processing Record 247|aknalich
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=aknalich,am
    Processing Record 248|daesti
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=daesti,ro
    Processing Record 249|tatarskaya pishlya
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=tatarskaya pishlya,ru
    Processing Record 250|ada
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=ada,rs
    Processing Record 251|mihalyi
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=mihalyi,hu
    Processing Record 252|cabugao
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=cabugao,ph
    Processing Record 253|san isidro
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=san isidro,cr
    Processing Record 254|bugko
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=bugko,ph
    Processing Record 255|lalig
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=lalig,ph
    Processing Record 256|dhuwaran
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=dhuwaran,in
    Processing Record 257|bababe
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=bababe,mr
    Processing Record 258|cajamarca
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=cajamarca,pe
    Processing Record 259|goragorskiy
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=goragorskiy,ru
    Processing Record 260|bandora
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=bandora,in
    Processing Record 261|igurusi
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=igurusi,tz
    Processing Record 262|taupo
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=taupo,nz
    Processing Record 263|izhmorskiy
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=izhmorskiy,ru
    Processing Record 264|basiao
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=basiao,ph
    Processing Record 265|ermont
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=ermont,fr
    Processing Record 266|dundee
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=dundee,za
    Processing Record 267|sanarate
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=sanarate,gt
    Processing Record 268|gorom-gorom
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=gorom-gorom,bf
    Processing Record 269|loksa
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=loksa,ee
    Processing Record 270|lake elsinore
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=lake elsinore,us
    Processing Record 271|markova
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=markova,ua
    Processing Record 272|pozega
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=pozega,hr
    Processing Record 273|mapili
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=mapili,ph
    Processing Record 274|velsk
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=velsk,ru
    Processing Record 275|ujjain
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=ujjain,in
    Processing Record 276|strabychovo
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=strabychovo,ua
    Processing Record 277|kubuta
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=kubuta,sz
    Processing Record 278|haverfordwest
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=haverfordwest,gb
    Processing Record 279|huaraz
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=huaraz,pe
    Processing Record 280|holesov
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=holesov,cz
    Processing Record 281|soleuvre
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=soleuvre,lu
    Processing Record 282|piliscsaba
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=piliscsaba,hu
    Processing Record 283|la gomera
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=la gomera,gt
    Processing Record 284|bohodukhiv
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=bohodukhiv,ua
    Processing Record 285|myski
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=myski,ru
    Processing Record 286|godfrey
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=godfrey,us
    Processing Record 287|bucovat
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=bucovat,ro
    Processing Record 288|rjukan
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=rjukan,no
    Processing Record 289|targsorul-vechi
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=targsorul-vechi,ro
    Processing Record 290|bari sadri
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=bari sadri,in
    Processing Record 291|corciano
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=corciano,it
    Processing Record 292|sumbal
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=sumbal,in
    Processing Record 293|sortland
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=sortland,no
    Processing Record 294|mpulungu
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=mpulungu,zm
    Processing Record 295|bad hall
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=bad hall,at
    Processing Record 296|jidvei
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=jidvei,ro
    Processing Record 297|abda
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=abda,hu
    Processing Record 298|islamey
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=islamey,ru
    Processing Record 299|friendswood
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=friendswood,us
    Processing Record 300|mondorf-les-bains
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=mondorf-les-bains,lu
    Processing Record 301|agde
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=agde,fr
    Processing Record 302|fritzlar
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=fritzlar,de
    Processing Record 303|menuma
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=menuma,jp
    Processing Record 304|fortaleza
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=fortaleza,br
    Processing Record 305|katsikas
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=katsikas,gr
    Processing Record 306|jaswantnagar
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=jaswantnagar,in
    Processing Record 307|paranga
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=paranga,ru
    Processing Record 308|ancud
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=ancud,cl
    Processing Record 309|panuco
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=panuco,mx
    Processing Record 310|shkotovo-26
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=shkotovo-26,ru
    Processing Record 311|narimanov
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=narimanov,ru
    Processing Record 312|mandal
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=mandal,in
    Processing Record 313|sint-pieters-leeuw
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=sint-pieters-leeuw,be
    Processing Record 314|kot khai
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=kot khai,in
    Processing Record 315|casanayan
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=casanayan,ph
    Processing Record 316|garavuti
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=garavuti,tj
    Processing Record 317|polje
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=polje,ba
    Processing Record 318|sao joaquim de bicas
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=sao joaquim de bicas,br
    Processing Record 319|pilot butte
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=pilot butte,ca
    Processing Record 320|kandrian
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=kandrian,pg
    Processing Record 321|whitewater
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=whitewater,us
    Processing Record 322|parla
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=parla,es
    Processing Record 323|steffisburg
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=steffisburg,ch
    Processing Record 324|lianran
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=lianran,cn
    Processing Record 325|burg
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=burg,de
    Processing Record 326|davidson
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=davidson,ca
    Processing Record 327|altagracia de orituco
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=altagracia de orituco,ve
    Processing Record 328|bauta
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=bauta,cu
    Processing Record 329|izumi
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=izumi,jp
    Processing Record 330|kecsked
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=kecsked,hu
    Processing Record 331|kroya
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=kroya,id
    Processing Record 332|buxton
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=buxton,us
    Processing Record 333|malakal
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=malakal,sd
    Processing Record 334|bewar
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=bewar,in
    Processing Record 335|yelniki
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=yelniki,ru
    Processing Record 336|pangdan
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=pangdan,ph
    Processing Record 337|solnechnoye
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=solnechnoye,ru
    Processing Record 338|ohaba
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=ohaba,ro
    Processing Record 339|godella
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=godella,es
    Processing Record 340|ulundi
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=ulundi,za
    Processing Record 341|morgantown
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=morgantown,us
    Processing Record 342|fizesu gherlii
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=fizesu gherlii,ro
    Processing Record 343|cheran
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=cheran,mx
    Processing Record 344|putol
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=putol,ph
    Processing Record 345|kemin
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=kemin,kg
    Processing Record 346|pestera
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=pestera,ro
    Processing Record 347|mogosani
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=mogosani,ro
    Processing Record 348|borova
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=borova,ua
    Processing Record 349|kehychivka
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=kehychivka,ua
    Processing Record 350|slevik
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=slevik,no
    Processing Record 351|cutlerville
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=cutlerville,us
    Processing Record 352|la piedad
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=la piedad,mx
    Processing Record 353|fond du sac
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=fond du sac,mu
    Processing Record 354|goshen
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=goshen,us
    Processing Record 355|barsana
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=barsana,in
    Processing Record 356|gataia
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=gataia,ro
    Processing Record 357|jedburgh
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=jedburgh,gb
    Processing Record 358|longkou
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=longkou,cn
    Processing Record 359|santa sofia
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=santa sofia,co
    Processing Record 360|pagatin
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=pagatin,ph
    Processing Record 361|longniddry
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=longniddry,gb
    Processing Record 362|stanilesti
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=stanilesti,ro
    Processing Record 363|ryugasaki
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=ryugasaki,jp
    Processing Record 364|santa luzia
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=santa luzia,br
    Processing Record 365|ayia galini
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=ayia galini,gr
    Processing Record 366|ghatampur
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=ghatampur,in
    Processing Record 367|vilcun
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=vilcun,cl
    Processing Record 368|aknashen
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=aknashen,am
    Processing Record 369|bluff
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=bluff,nz
    Processing Record 370|scornicesti
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=scornicesti,ro
    Processing Record 371|tinogboc
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=tinogboc,ph
    Processing Record 372|coxim
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=coxim,br
    Processing Record 373|cuautitlan
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=cuautitlan,mx
    Processing Record 374|acambay
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=acambay,mx
    Processing Record 375|matiri
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=matiri,tz
    Processing Record 376|soimari
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=soimari,ro
    Processing Record 377|nuevo ideal
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=nuevo ideal,mx
    Processing Record 378|gimbi
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=gimbi,et
    Processing Record 379|ituni
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=ituni,gy
    Processing Record 380|spirovo
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=spirovo,ru
    Processing Record 381|seia
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=seia,pt
    Processing Record 382|malitbog
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=malitbog,ph
    Processing Record 383|somerton
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=somerton,us
    Processing Record 384|yaritagua
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=yaritagua,ve
    Processing Record 385|dhilwan
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=dhilwan,in
    Processing Record 386|karatau
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=karatau,kz
    Processing Record 387|fontvieille
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=fontvieille,mc
    Processing Record 388|el rodeo
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=el rodeo,hn
    Processing Record 389|medenychi
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=medenychi,ua
    Processing Record 390|urulga
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=urulga,ru
    Processing Record 391|wellington
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=wellington,us
    Processing Record 392|cecava
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=cecava,ba
    Processing Record 393|tourlaville
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=tourlaville,fr
    Processing Record 394|daru
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=daru,sl
    Processing Record 395|meget
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=meget,ru
    Processing Record 396|suhum
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=suhum,gh
    Processing Record 397|gomez palacio
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=gomez palacio,mx
    Processing Record 398|miyang
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=miyang,cn
    Processing Record 399|shaturtorf
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=shaturtorf,ru
    Processing Record 400|himare
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=himare,al
    Processing Record 401|talusan
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=talusan,ph
    Processing Record 402|oberwil
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=oberwil,ch
    Processing Record 403|tralee
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=tralee,ie
    Processing Record 404|aryanah
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=aryanah,tn
    Processing Record 405|zambrow
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=zambrow,pl
    Processing Record 406|farmington
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=farmington,us
    Processing Record 407|vaiano
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=vaiano,it
    Processing Record 408|varciorog
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=varciorog,ro
    Processing Record 409|yauya
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=yauya,pe
    Processing Record 410|windsor locks
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=windsor locks,us
    Processing Record 411|bure
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=bure,et
    Processing Record 412|ayer keroh
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=ayer keroh,my
    Processing Record 413|new britain
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=new britain,us
    Processing Record 414|mendeleyevsk
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=mendeleyevsk,ru
    Processing Record 415|vizianagaram
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=vizianagaram,in
    Processing Record 416|meaford
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=meaford,ca
    Processing Record 417|arvore
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=arvore,pt
    Processing Record 418|nivelles
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=nivelles,be
    Processing Record 419|california city
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=california city,us
    Processing Record 420|yverdon
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=yverdon,ch
    Processing Record 421|leczyca
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=leczyca,pl
    Processing Record 422|ban chang
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=ban chang,th
    Processing Record 423|igboho
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=igboho,ng
    Processing Record 424|privokzalnyy
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=privokzalnyy,ru
    Processing Record 425|gurmatkal
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=gurmatkal,in
    Processing Record 426|abucay
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=abucay,ph
    Processing Record 427|benin
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=benin,ng
    Processing Record 428|ragusa
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=ragusa,it
    Processing Record 429|lodwar
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=lodwar,ke
    Processing Record 430|kopeysk
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=kopeysk,ru
    Processing Record 431|concepcion
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=concepcion,ph
    Processing Record 432|sidi ali
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=sidi ali,dz
    Processing Record 433|salisbury
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=salisbury,gb
    Processing Record 434|liloy
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=liloy,ph
    Processing Record 435|crna na koroskem
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=crna na koroskem,si
    Processing Record 436|that phanom
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=that phanom,th
    Processing Record 437|sremska mitrovica
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=sremska mitrovica,rs
    Processing Record 438|san miguel
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=san miguel,ph
    Processing Record 439|goregaon
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=goregaon,in
    Processing Record 440|rahuri
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=rahuri,in
    Processing Record 441|acuna
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=acuna,mx
    Processing Record 442|zatyshshya
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=zatyshshya,ua
    Processing Record 443|marmora
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=marmora,ca
    Processing Record 444|karabash
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=karabash,ru
    Processing Record 445|dongdu
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=dongdu,cn
    Processing Record 446|furano
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=furano,jp
    Processing Record 447|florin
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=florin,us
    Processing Record 448|ratangarh
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=ratangarh,in
    Processing Record 449|aksha
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=aksha,ru
    Processing Record 450|coon rapids
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=coon rapids,us
    Processing Record 451|tezoyuca
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=tezoyuca,mx
    Processing Record 452|kapatan
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=kapatan,ph
    Processing Record 453|arlington
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=arlington,us
    Processing Record 454|sassari
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=sassari,it
    Processing Record 455|pajacuaran
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=pajacuaran,mx
    Processing Record 456|aga
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=aga,ph
    Processing Record 457|campbell river
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=campbell river,ca
    Processing Record 458|binubusan
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=binubusan,ph
    Processing Record 459|stupari
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=stupari,ba
    Processing Record 460|karymskoye
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=karymskoye,ru
    Processing Record 461|ambositra
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=ambositra,mg
    Processing Record 462|pio xii
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=pio xii,br
    Processing Record 463|tungi
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=tungi,bd
    Processing Record 464|miguel alves
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=miguel alves,br
    Processing Record 465|massakory
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=massakory,td
    Processing Record 466|roxas
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=roxas,ph
    Processing Record 467|san remo
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=san remo,it
    Processing Record 468|pribor
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=pribor,cz
    Processing Record 469|vozuca
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=vozuca,ba
    Processing Record 470|doesburg
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=doesburg,nl
    Processing Record 471|gura humorului
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=gura humorului,ro
    Processing Record 472|milcoiu
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=milcoiu,ro
    Processing Record 473|nordhausen
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=nordhausen,de
    Processing Record 474|belo horizonte
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=belo horizonte,br
    Processing Record 475|rock hill
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=rock hill,us
    Processing Record 476|alcala la real
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=alcala la real,es
    Processing Record 477|xinye
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=xinye,cn
    Processing Record 478|kheda
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=kheda,in
    Processing Record 479|tulghes
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=tulghes,ro
    Processing Record 480|sevasti
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=sevasti,gr
    Processing Record 481|lyubech
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=lyubech,ua
    Processing Record 482|mets masrik
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=mets masrik,am
    Processing Record 483|yamasaki
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=yamasaki,jp
    Processing Record 484|monteprandone
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=monteprandone,it
    Processing Record 485|bodo
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=bodo,no
    Processing Record 486|ipinda
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=ipinda,tz
    Processing Record 487|gyorujbarat
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=gyorujbarat,hu
    Processing Record 488|milas
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=milas,ro
    Processing Record 489|solotcha
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=solotcha,ru
    Processing Record 490|bikramganj
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=bikramganj,in
    Processing Record 491|cabra
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=cabra,ph
    Processing Record 492|folsom
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=folsom,us
    Processing Record 493|muroran
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=muroran,jp
    Processing Record 494|chene-bougeries
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=chene-bougeries,ch
    Processing Record 495|agdam
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=agdam,az
    Processing Record 496|eqbaliyeh
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=eqbaliyeh,ir
    Processing Record 497|musaler
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=musaler,am
    Processing Record 498|capagao
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=capagao,ph
    Processing Record 499|plonsk
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=plonsk,pl
    Processing Record 500|karabudakhkent
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=karabudakhkent,ru
    Processing Record 501|tetchea
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=tetchea,ro
    Processing Record 502|krasnoarmeysk
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=krasnoarmeysk,ua
    Processing Record 503|vetas
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=vetas,co
    Processing Record 504|elias fausto
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=elias fausto,br
    Processing Record 505|malidong
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=malidong,ph
    Processing Record 506|guepsa
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=guepsa,co
    Processing Record 507|oregon city
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=oregon city,us
    Processing Record 508|yanam
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=yanam,in
    Processing Record 509|owasso
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=owasso,us
    Processing Record 510|winchendon
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=winchendon,us
    Processing Record 511|ayios nikolaos
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=ayios nikolaos,gr
    Processing Record 512|kipalbig
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=kipalbig,ph
    Processing Record 513|santa ana
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=santa ana,mx
    Processing Record 514|charleston
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=charleston,us
    Processing Record 515|podstepki
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=podstepki,ru
    Processing Record 516|vasvar
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=vasvar,hu
    Processing Record 517|lagodekhi
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=lagodekhi,ge
    Processing Record 518|aniche
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=aniche,fr
    Processing Record 519|ayacucho
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=ayacucho,pe
    Processing Record 520|liboganon
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=liboganon,ph
    Processing Record 521|grand-santi
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=grand-santi,gf
    Processing Record 522|olaine
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=olaine,lv
    Processing Record 523|mitla
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=mitla,mx
    Processing Record 524|piacenza
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=piacenza,it
    Processing Record 525|vicuna
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=vicuna,cl
    Processing Record 526|wadhwan
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=wadhwan,in
    Processing Record 527|san jose
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=san jose,bo
    Processing Record 528|biharamulo
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=biharamulo,tz
    Processing Record 529|kremasti
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=kremasti,gr
    Processing Record 530|ip
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=ip,ro
    Processing Record 531|gansing
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=gansing,ph
    Processing Record 532|jorhat
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=jorhat,in
    Processing Record 533|sachica
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=sachica,co
    Processing Record 534|gastouni
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=gastouni,gr
    Processing Record 535|lysyye gory
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=lysyye gory,ru
    Processing Record 536|rucheng
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=rucheng,cn
    Processing Record 537|chiang mai
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=chiang mai,th
    Processing Record 538|luorong
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=luorong,cn
    Processing Record 539|maguling
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=maguling,ph
    Processing Record 540|dinghai
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=dinghai,cn
    Processing Record 541|esterhazy
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=esterhazy,ca
    Processing Record 542|dragalina
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=dragalina,ro
    Processing Record 543|walnut creek
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=walnut creek,us
    Processing Record 544|lisiy nos
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=lisiy nos,ru
    Processing Record 545|naushki
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=naushki,ru
    Processing Record 546|krasnogorskiy
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=krasnogorskiy,uz
    Processing Record 547|uvarovka
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=uvarovka,ru
    Processing Record 548|lakhnadon
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=lakhnadon,in
    Processing Record 549|maidens
    http://api.openweathermap.org/data/2.5/weather?appid=555f071860e562f7871e3f299ddd26ba&units=imperial&q=maidens,gb



```python
print("-----------------------")
print("Data Retrieval Complete")
print("-----------------------")
record_process.append("-----------------------")
record_process.append("Data Retrieval Complete")
record_process.append("-----------------------")
```

    -----------------------
    Data Retrieval Complete
    -----------------------



```python
#print(json.dumps(result_of_query,indent=4))
```

#test area for converting datetime information and restoring back into results
val=result_of_query["dt"]
daytime_info=datetime.datetime.fromtimestamp(int(val)).strftime('%Y-%m-%d %H:%M:%S %p')
daytime


```python
rcw=pd.DataFrame(ran_city_weather)
#rcw.head()
```


```python
src=pd.DataFrame(sum_ran_city)
#src.head()
```


```python
#Temperature (F) vs. Latitude
#Humidity (%) vs. Latitude
#Cloudiness (%) vs. Latitude
#Wind Speed (mph) vs. Latitude
lat_info=[info.get("coord").get("lat") for info in ran_city_weather]
lon_info=[info.get("coord").get("lon") for info in ran_city_weather]
temp_info=[info.get("main").get("temp_max") for info in ran_city_weather]
hum_info=[info.get("main").get("humidity") for info in ran_city_weather]
cloud_info=[info.get("clouds").get("all") for info in ran_city_weather]
wind_info=[info.get("wind").get("speed") for info in ran_city_weather]
city_info=[info.get("name") for info in ran_city_weather]
country_info=[info.get("sys").get("country") for info in ran_city_weather]
date_info=[info.get("dt") for info in ran_city_weather]

```


```python
c_data={"City":city_info,"Cloudiness":cloud_info,"Country":country_info,"Date / Time":date_info,"Humidity":hum_info,"Lat":lat_info,"Lon":lon_info,"Max Temp":temp_info,"Wind Speed":wind_info}
city_data=pd.DataFrame(c_data)
city_data.head()
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
      <th>City</th>
      <th>Cloudiness</th>
      <th>Country</th>
      <th>Date / Time</th>
      <th>Humidity</th>
      <th>Lat</th>
      <th>Lon</th>
      <th>Max Temp</th>
      <th>Wind Speed</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Nabari</td>
      <td>75</td>
      <td>JP</td>
      <td>2017-10-29 17:16:00 PM</td>
      <td>58</td>
      <td>34.62</td>
      <td>136.08</td>
      <td>55.40</td>
      <td>21.92</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Volya</td>
      <td>0</td>
      <td>RU</td>
      <td>2017-10-29 17:30:00 PM</td>
      <td>81</td>
      <td>51.77</td>
      <td>39.54</td>
      <td>42.80</td>
      <td>8.95</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Eregli</td>
      <td>20</td>
      <td>TR</td>
      <td>2017-10-29 18:22:06 PM</td>
      <td>97</td>
      <td>37.51</td>
      <td>34.05</td>
      <td>46.11</td>
      <td>1.83</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Carlogani</td>
      <td>0</td>
      <td>RO</td>
      <td>2017-10-29 18:00:00 PM</td>
      <td>66</td>
      <td>44.52</td>
      <td>24.15</td>
      <td>48.20</td>
      <td>18.34</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Rome</td>
      <td>20</td>
      <td>US</td>
      <td>2017-10-29 17:55:00 PM</td>
      <td>52</td>
      <td>34.26</td>
      <td>-85.16</td>
      <td>46.40</td>
      <td>8.05</td>
    </tr>
  </tbody>
</table>
</div>




```python
city_data.to_csv("City Summary.csv",index=False, header=True)
```


```python
temp_lat={"temperature (F)":temp_info,"Latitude":lat_info}
temp_lat=pd.DataFrame(temp_lat)
#temp_lat.head()

```


```python
today=date.today()
today.strftime("%A,%d,%B %Y")

#today
```




    'Sunday,29,October 2017'




```python
temp_lat.plot(kind="scatter",x="Latitude",y="temperature (F)",grid=True,title="City Latitude vs. Temperature (F) "+str(today))
plt.xlabel=("Latitude")
plt.ylabel=("City Temperature (F)")
plt.savefig("City_Latitude_v_Temp_Matplot.png")
plt.show()
```


![png](output_29_0.png)



```python
current_palette=sns.color_palette("muted",n_colors=6)
cmap=ListedColormap(sns.color_palette(current_palette).as_hex())
temp_lat.plot(kind="scatter",x="Latitude",y="temperature (F)",grid=True,title="City Latitude vs. temperature (˚F) "+str(today),c=0,cmap=cmap)
plt.xlabel=("Latitude")
plt.ylabel=("temperature (˚F)")
plt.savefig("City_Latitude_v_Temp_Seaborn.png")
plt.show()
```


![png](output_30_0.png)



```python
#temp_lat.dtypes
```


```python
hum_lat={"Humidity":hum_info,"Latitude":lat_info}
hum_lat=pd.DataFrame(hum_lat)
#hum_lat.head()
```


```python
hum_lat.plot(kind="scatter",x="Latitude",y="Humidity",grid=True,title="City Latitude vs. % Humidity "+str(today))
plt.xlabel=("Latitude")
plt.ylabel=("Humidity %")
plt.savefig("City_Latitude_v_Humidity_Matplot.png")
plt.show()
```


![png](output_33_0.png)



```python
current_palette=sns.color_palette("muted",n_colors=6)
cmap=ListedColormap(sns.color_palette(current_palette).as_hex())
hum_lat.plot(kind="scatter",x="Latitude",y="Humidity",grid=True,title="City Latitude vs. % Humidity "+str(today),c=1,cmap=cmap)
plt.xlabel=("Latitude")
plt.ylabel=("% Humidity")
plt.savefig("City_Latitude_v_Humidity_Seaborn.png")
plt.show()
```


![png](output_34_0.png)



```python
cloud_lat={"% Cloudiness":cloud_info,"Latitude":lat_info}
cloud_lat=pd.DataFrame(cloud_lat)
#cloud_lat.head()
```


```python
cloud_lat.plot(kind="scatter",x="Latitude",y="% Cloudiness",grid=True,title="City Latitude vs. % Cloudiness "+str(today))
plt.xlabel=("Latitude")
plt.ylabel=("% Cloudiness")
plt.savefig("City_Latitude_v_%Cloudiness_Matplot.png")
plt.show()
```


![png](output_36_0.png)



```python
current_palette=sns.color_palette("muted",n_colors=8)
cmap=ListedColormap(sns.color_palette(current_palette).as_hex())
cloud_lat.plot(kind="scatter",x="Latitude",y="% Cloudiness",grid=True,title="City Latitude vs. % Cloudiness "+str(today),c=1,cmap=cmap)
plt.xlabel=("Latitude")
plt.ylabel=("% Cloudiness")
plt.savefig("City_Latitude_v_%Cloudiness_Seaborn.png")
plt.show()
```


![png](output_37_0.png)



```python
wind_lat={"Wind_Speed_(mph)":wind_info,"Latitude":lat_info}
wind_lat=pd.DataFrame(wind_lat)
wind_lat.to_csv("Wind_v_Lat.csv",index=False,header=True)
#wind_lat.head()
```


```python
wind_lat.plot(kind="scatter",x="Latitude",y="Wind_Speed_(mph)",grid=True,title="City Latitude vs. Wind Speed (mph) "+str(today))
plt.xlabel=("Latitude")
plt.ylabel=("Wind Speed (mph)")
plt.savefig("City_Latitude_v_WindSpeed_Matplot.png")
plt.show()
```


![png](output_39_0.png)



```python
#ran_city_weather
```


```python
current_palette=sns.color_palette("muted",n_colors=8)
cmap=ListedColormap(sns.color_palette(current_palette).as_hex())
wind_lat.plot(kind="scatter",x="Latitude",y="Wind_Speed_(mph)",grid=True,title="City Latitude vs. Wind Speed (mph) "+str(today),c=0,cmap=cmap)
plt.xlabel=("Latitude")
plt.ylabel=("Wind Speed (mph)")
plt.savefig("City_Latitude_v_Wind_Speed_Seaborn.png")
plt.show()
```


![png](output_41_0.png)



```python

```


```python

```
