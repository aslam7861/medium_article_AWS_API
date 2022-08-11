
---
title: Automation on Airports using API and AWS
description: >-
  In this twenty-first century, technology has taken a major place in the real
  world. There are many things that can be done by sitting from…
date: ''
categories: []
keywords: []
slug: ''
---

In this twenty-first century, technology has taken a major place in the real world. There are many things that can be done by sitting from a lovable place. We all can see in our daily life how dependent we are nowadays with technology . Today, I am discussing here to find all the informations of major airports of Germany . Many taxis, scooters and bicycle renting companies can get benefited with the informations which I explain in this medium article.

The big advantage of modern technology to a company is optimising eveything and increasing the profit. For example, if the scooters company distributed many rented scooters to a big airports where there was maximum chance of raining then the company would have to bear the costs while distributing the scooters to different places.

![](img\1__gNblB__OOm3dEYEiXe9xVrw.png)

Once the company knows the weather conditions and the number of flights landing in each big cities then they can analyse the right quantity of scooters/taxis to be distributed in each places for renters.

#### Web scrapping:

We use Web scraping to collect the structured web data in an automation fashion. Web scrapping involves downloading a page and extracting the informations from it. This is the one of the important tool to bring the informations from wikipedia. The content of a page may be parsed and reformatted, and its data copied into a spreadsheet or loaded into a database. Web scrapers typically take something out of a page, to make use of it for another purpose somewhere else. For example we collect the informations of all the flights status of diffferent airports of Germany using web scrapping(using Beautifulsoup) and bring out the necessary information which we require.

![my_image](img\1__1__UYXjS0q0dc3126FDN6Lg.png)

To use the webscrapping we use API keys which you have to find on your own

OWM\_key = "your\_key"  
flight\_api\_key = "your\_api\_key"

We collect informations of all cities of Germany in a dataframe df\_cities which have a large airports using BeautifulSoup function.

**import** requests  
**from** bs4 **import** BeautifulSoup **as** bs  
**import** pandas **as** pd  
**import** unicodedata  
city\_lists=\[‘Berlin’, ‘Dresden’, ‘Frankfurt am Main’, ‘Münster’, ‘Hamburg’ ‘Cologne’, ‘Düsseldorf’, ‘Munich’, ‘Nuremberg’, ‘Leipzig’, ‘Stuttgart’, ‘Hannover’,‘Bremen’, ‘Dortmund’, ‘Karlsruhe’\]

countries=\["DE" for i in rannge(den(city\_lists))\]

airports\_icao=\['EDDB','EDDC', 'EDDF', 'EDDG', 'EDDH', 'EDDK','EDDL', 'EDDM', 'EDDN', 'EDDP', 'EDDS', 'EDDV', 'EDDW', 'EDLW', 'EDSB'\]

cities**\=**city\_lists  
**def** City\_info(soup):  
      
    ret\_dict **\=** {}  
    ret\_dict\['city'\] **\=** soup**.**h1**.**get\_text()  
      
      
    **if** soup**.**select\_one('.mergedrow:-soup-contains("Mayor")>.infobox-label') **!=** **None**:  
        i **\=** soup**.**select\_one('.mergedrow:-soup-contains("Mayor")>.infobox-label')  
        mayor\_name\_html **\=** i**.**find\_next\_sibling()  
        mayor\_name **\=** unicodedata**.**normalize('NFKD',mayor\_name\_html**.**get\_text())  
        ret\_dict\['mayor'\]  **\=** mayor\_name  
      
    **if** soup**.**select\_one('.mergedrow:-soup-contains("City")>.infobox-label') **!=** **None**:  
        j **\=**  soup**.**select\_one('.mergedrow:-soup-contains("City")>.infobox-label')  
        area **\=** j**.**find\_next\_sibling('td')**.**get\_text()  
        ret\_dict\['city\_size'\] **\=** unicodedata**.**normalize('NFKD',area)  
  
    **if** soup**.**select\_one('.mergedtoprow:-soup-contains("Elevation")>.infobox-data') **!=** **None**:  
        k **\=** soup**.**select\_one('.mergedtoprow:-soup-contains("Elevation")>.infobox-data')  
        elevation\_html **\=** k**.**get\_text()  
        ret\_dict\['elevation'\] **\=** unicodedata**.**normalize('NFKD',elevation\_html)  
      
    **if** soup**.**select\_one('.mergedtoprow:-soup-contains("Population")') **!=** **None**:  
        l **\=** soup**.**select\_one('.mergedtoprow:-soup-contains("Population")')  
        c\_pop **\=** l**.**findNext('td')**.**get\_text()  
        ret\_dict\['city\_population'\] **\=** c\_pop  
      
    **if** soup**.**select\_one('.infobox-label>\[title^=Urban\]') **!=** **None**:  
        m **\=** soup**.**select\_one('.infobox-label>\[title^=Urban\]')  
        u\_pop **\=** m**.**findNext('td')  
        ret\_dict\['urban\_population'\] **\=** u\_pop**.**get\_text()  
  
    **if** soup**.**select\_one('.infobox-label>\[title^=Metro\]') **!=** **None**:  
        n **\=** soup**.**select\_one('.infobox-label>\[title^=Metro\]')  
        m\_pop **\=** n**.**findNext('td')  
        ret\_dict\['metro\_population'\] **\=** m\_pop**.**get\_text()  
      
    **if** soup**.**select\_one('.latitude') **!=** **None**:  
        o **\=** soup**.**select\_one('.latitude')  
        ret\_dict\['lat'\] **\=** o**.**get\_text()  
  
    **if** soup**.**select\_one('.longitude') **!=** **None**:      
        p **\=** soup**.**select\_one('.longitude')  
        ret\_dict\['long'\] **\=** p**.**get\_text()  
      
    **return** ret\_dict  
  
list\_of\_city\_info **\=** \[\]  
**for** city **in** cities:  
    url **\=** 'https://en.wikipedia.org/wiki/{}'**.**format(city)  
    web **\=** requests**.**get(url,'html.parser')  
    soup **\=** bs(web**.**content)  
    list\_of\_city\_info**.**append(City\_info(soup))  
df\_cities **\=** pd**.**DataFrame(list\_of\_city\_info)  
df\_cities.head(5)

![](C:\Users\HANA\Desktop\WBS\aslam\posts\md_1660208687829\img\1__SqO3gEVpM09vZ6__3QgWCJA.png)

  

We collect weather information using web scrapping with OWM API key:

\# look for the fields that could be relevant:   
\# better field descriptions [https://www.weatherbit.io/api/weather-forecast-5-day](https://www.weatherbit.io/api/weather-forecast-5-day)  
all\_weather=\[\]  
\# datetime, temperature, wind, prob\_perc, rain\_qty, snow = \[\], \[\], \[\], \[\], \[\], \[\]  
\# for forecast\_api in forecast\_apis:  
      
for i in range(len(city\_lists)):  
    city=city\_lists\[i\]  
    country=countries\[i\]  
    #response=responses\[i\]  
    forecast\_api = responses\[i\].json()\['list'\]  
    weather\_info = \[\]  
    for forecast\_3h in forecast\_api:   
        weather\_hour = {}  
        # datetime utc  
        weather\_hour\['datetime'\] = forecast\_3h\['dt\_txt'\]  
        # temperature   
        weather\_hour\['temperature'\] = forecast\_3h\['main'\]\['temp'\]  
        # wind  
        weather\_hour\['wind'\] = forecast\_3h\['wind'\]\['speed'\]  
        # probability precipitation   
        try: weather\_hour\['prob\_perc'\] = float(forecast\_3h\['pop'\])  
        except: weather\_hour\['prob\_perc'\] = 0  
        # rain  
        try: weather\_hour\['rain\_qty'\] = float(forecast\_3h\['rain'\]\['3h'\])  
        except: weather\_hour\['rain\_qty'\] = 0  
        # wind   
        try: weather\_hour\['snow'\] = float(forecast\_3h\['snow'\]\['3h'\])  
        except: weather\_hour\['snow'\] = 0  
              
        weather\_hour\['municipality\_iso\_country'\] = city + ',' + country  
        weather\_info.append(weather\_hour)  
    all\_weather.append(weather\_info)  
          
all\_weather\_data = pd.concat(\[pd.DataFrame(a) for a in all\_weather\])

#### Airports detail of Germany using API keys of Aerodatabox:

![](C:\Users\HANA\Desktop\WBS\aslam\posts\md_1660208687829\img\1__7roZbOu4NEe8Jzee8RQZFw.jpeg)

In the following code we are extracting data using API of Aerodatabox of cities with time interval of 9 hours from actual time:

import requests  
from IPython.display import JSON  
responses=\[\]  
from datetime import datetime, timedelta

for i in range(len(airports\_icao)):  
    to\_local\_time = datetime.now().strftime('%Y-%m-%dT%H:00')  
    from\_local\_time = (datetime.now() + timedelta(hours=9)).strftime('%Y-%m-%dT%H:00')  
    url = f"[https://aerodatabox.p.rapidapi.com/flights/airports/icao/{airports\_icao\[i\]}/{to\_local\_time}/{from\_local\_time](https://aerodatabox.p.rapidapi.com/flights/airports/icao/%7Bairports_icao[i]%7D/%7Bto_local_time%7D/%7Bfrom_local_time)}"  
    querystring = {"withLeg":"true","withCancelled":"true","withCodeshared":"true","withCargo":"true","withPrivate":"false","withLocation":"false"}  
    headers = {  
        'x-rapidapi-host': "aerodatabox.p.rapidapi.com",  
        'x-rapidapi-key': flight\_api\_key  
        }  
    responses.append(requests.request("GET", url, headers=headers, params=querystring))

We collected all the information in **responses.** We apply **.json()** for each item of responses and collect the informations of flights which are landing in different cities and form a data frame **arrival\_cities** as computed below**:**

import requests  
from IPython.display import JSON  
responses=\[\]  
from datetime import datetime, timedelta

for i in range(len(airports\_icao)):  
  
arrivals\_list=\[\]  
for i in range(len(responses)):  
#for response in responses:  
    arrivals\_list.append(responses\[i\].json()\['arrivals'\])

def get\_flight\_info(flight\_json, icao):  
    # terminal  
    try: terminal = flight\_json\['arrival'\]\['terminal'\]  
    except: terminal = None  
    # aircraft  
    try: aircraft = flight\_json\['aircraft'\]\['model'\]  
    except: aircraft = None

return {  
        'dep\_airport':flight\_json\['departure'\]\['airport'\]\['name'\],  
        'sched\_arr\_loc\_time':flight\_json\['arrival'\]\['scheduledTimeLocal'\],  
        'terminal':terminal,  
        'status':flight\_json\['status'\],  
        'aircraft':aircraft,  
        'icao\_code':icao  
        #'icao\_code':airport\_icoa  
    }

import pandas as pd  
pds=\[\]  
for i in range(len(city\_lists)):  
    pds.append(pd.DataFrame(\[get\_flight\_info(flight, airports\_icao\[i\]) for flight in arrivals\_list\[i\]\]))

print(\[a.shape for a in pds\])  
arrivals\_cities=pd.concat(pds)

#### SQL Alchemy:

![](C:\Users\HANA\Desktop\WBS\aslam\posts\md_1660208687829\img\1__j7KCFGTuxk3Y__T6vcpsh6w.png)

SQLAlchemy is the **Python SQL toolkit** and Object Relational Mapper that gives application developers the full power and flexibility of SQL. Python has different libraries to bring necessary informations using APIs. One can clean the data using pandas in python. To produce a database in SQL we create first a Schema in MySQL using the following one line code:

CREATE DATABASE project;

After creating the database the following code connects our python code to SQL:

**import** pandas **as** pd  
  
schema**\=** "project"  
host**\=**"abcd"  
user**\=**"abcd"  
password**\=**"password"  
port**\=**3306  
con **\=** f'mysql+pymysql://{user}:{password}@{host}:{port}/{schema}'

Once the connection is created between python and SQL Schema, we just need to hit the following code:

all\_weather\_data.to\_sql('weather', if\_exists='append', con=con, index=False)  
arrivals\_cities.to\_sql('arrivals', if\_exists='append', con=con, index=False)  
df\_cities.to\_sql('cities', if\_exists='append', con=con, index=False)

in Python which sends all the information in our Schema. If you need more informations to be included then you extract informations using API keys and push it in your schema.

I have sent all the informations in my schema known as ‘project’ and produced the following ERR diagram:

![](C:\Users\HANA\Desktop\WBS\aslam\posts\md_1660208687829\img\1__zPpcG1IZ47uB76YKb28UZw.png)

  

#### SQL-Tableau: 

The data base ‘project’ consists of information in the time between 15:20–00:20 of August 09, 2022(**For you change the time accordingly**) from which I constructed a table which explains number of flights landing with other details:

![](C:\Users\HANA\Desktop\WBS\aslam\posts\md_1660208687829\img\1__QWJ5OgTdnoc5lQq5VcaD7A.png)

From above table we construct a graph which consists of all flight landing at aforementioned time.

![](C:\Users\HANA\Desktop\WBS\aslam\posts\md_1660208687829\img\1__8mwVuckElWO8xkoz0XLBSQ.png)

### AWS(Amazon Web Service):

![](C:\Users\HANA\Desktop\WBS\aslam\posts\md_1660208687829\img\1__z3AdCo4IojGNezkdxhhNww.png)

####   

AWS is a cloud service from Amazon, which provides services including computing, storage, networking, database, analytics etc.. It is also used to form the building blocks to create and deploy any type of applications in the cloud. Building blocks are actully designed to work with each other, and result in applications that are sophisticated and highly scalable.

#### Update all the python code into AWS interface:

The beauty of this article is to update all the python code into AWS Lambda function to generate all the information automatically according to our choices. I generate all my code to find details of airports and the planes landing for next nine hours. With this output I generate two tables:one consists number of flights arriving in the airport along with weather informations and another with the similar informations but hourly. We can easily get the time of big demands of the scooters from hourly detail. We follow the following instructions to obtain our results:

#### 1\. Set the Lambda function on AWS:

Once you have created an account in AWS you have to set a Lambda function. The Lambda function you have created does not cosists some of the python libraries, so you have to add these libraries on Add layer which you can find below on Lmbda function.

![](C:\Users\HANA\Desktop\WBS\aslam\posts\md_1660208687829\img\1__omNGOsi__lfWBxzgLFF9pZA.png)

#### 2\. Generate the python code:

Once you have added the pyhton libraries you just need to set your code to lambda\_function.py as shown nelow:

![](C:\Users\HANA\Desktop\WBS\aslam\posts\md_1660208687829\img\1__bKRGyGwCGL4kvmd2XK9L8A.png)

Apply **Deploy** button to save your code and then pess **Test** button to produce the result which you require. One can set here the time accordingly to get the results when ever required.

#### 3\. Add Trigger:

In the Lambda function, we create an event trigger

![](C:\Users\HANA\Desktop\WBS\aslam\posts\md_1660208687829\img\1__xZ5907j6N8V39by2TSSWXg.png)

using **CloudWatch Events** to produce the results regulary to set the plan for car/scooter companies to optimize the costs regularly. For example, I modify my code to collect all informations in the time between 0:00 to 23:59 every next day. 

#### Contact:

#### Github: [https://github.com/aslam7861](https://github.com/aslam7861)

**Email: aliaslam9439@gmail.com**
