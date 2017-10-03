---
layout: post
title: MTA Turnstile Data Analysis
image: /img/20170406_082924.jpg
---

![Subway turnstiles]({{ site.baseurl }}/img/20170406_082924.jpg)

This is my first blog post. So bear with me if I am breaking any unwritten rules of blogging.

Last Friday we completed our first week at Metis and as well as our first project as a group. Project involved analyzing NYC MTA turnstile data to help an imaginary client place teams to canvas subway stations to solicit for email addresses from subway riders, to be used in their mailing group for distribution of invitations for their annual Gala. Goal was to utilize data science techniques to provide the client with insights on which stations are ideal for placement of teams. We utilized exploratory data analysis skills that we learned during our first week in the bootcamp to explore MTA turnstile data and produce some visualizations our findings.

#### Reading Data
NYC MTA Turnstile information is freely available in CSV format through the following website:
http://web.mta.info/developers/turnstile.html

Data is available on a weekly basis and can be easily read into pandas. In the interest of time, I wrote a quick not so elegant function to read a series of CSV files from the website and return a data frame. Since we were only interested in 5-7 weeks of data, this worked well.

```python
import pandas as pd

def importMTAData(startMonthInt, endMonthInt):
    df =[]
    for m in range(startMonthInt,endMonthInt):
        for d in range(1,31):
            url = 'http://web.mta.info/developers/data/nyct/turnstile/turnstile_17' + ('%02d') % (m,) + ('%02d') % (d,) + '.txt'
            print('Trying to Reading :'+ ' '+ url)
            try:
                tempDF = pd.read_csv(url)
                print('Success!!!')
                if len(df) < 1:
                    df = tempDF
                else:
                    df = pd.concat([df,tempDF])
            except Exception:
                pass
    return(df)

df = importMTAData(3,5)
```

At first glance MTA turnstile dataset looked deceivingly simple. It was only when we were knee deep in data that we started to realize some issues and complexities of this data. Familiarity with the NYC subway system definitely helped us in identifying certain issues that someone unfamiliar with the system would have ignored.  

#### Keys
In order to uniquely identify a turnstile one would have to use all of the following columns:  
* C/A  
* UNIT  
* SCP  
* STATION  

Unique identification of stations require use of a combination of *station* and *line name*.

#### Entries and Exits
![Subway turnstiles registers]({{ site.baseurl }}/img/20170406_083012.jpg)


*Entries* and *exits* columns in the data are the register counts of a given turnstile at the audited time. In order to find the count of riders entering and exiting through a turnstile during each audit interval, one would have to calculate the difference in register counts between the current audit and the previous audit for the turnstile.  

```python
key = ['C/A',
       'UNIT',
       'SCP',
       'STATION',
       'LINENAME']

# create dateTime column in order perform chronological sorting
df['dateTime'] = pd.to_datetime(df.DATE +' '+ df.TIME, format='%m/%d/%Y %H:%M:%S')

def calcDiff(df):
    df = df.sort_values('dateTime')
    df['Incr_ENTRIES'] =  df.ENTRIES - df.ENTRIES.shift(1)
    df['Incr_EXITS'] = df.EXITS - df.EXITS.shift(1)
    return(df)

newDF = df.groupby(key, as_index=False).apply(lambda x: calcDiff(x)).reset_index()
```

### Outliers

We quickly discovered that the dataset is riddled with outliers. Here are some issues that we discovered:

##### Negative Incr_ENTRIES
There were instances where the register is reset from a very large counter value to a near zero value causing large negative *Incr_ENTRIES* values in few instances. It looked like that entry/exit counters were reseted to zero at these problematic turnstiles during repairs. There was another instance where turnstile counter was running backwards for nearly a day.

##### Large Jumps in Incr_ENTRIES
In addition to negative increments there were instances where large jumps in register counts occurred. This caused stations like Lorimer Street in Brooklyn to come up as busiest in NYC during our analysis. However, familiarity with the NYC subway system helped us quickly identify this as an issue and put in controls to eliminate outliers.

We decided to remove all problematic entries from our dataset.

```python
# keep only positive entries
newDF = newDF.loc[(newDF['Incr_ENTRIES']>0) & (newDF['Incr_EXITS']>0),]

# Get rid of outrageously large Increments in ENTRIES/EXITS  
newDF = newDF.loc[(newDF.Incr_ENTRIES < 20000) & (newDF.Incr_EXITS < 20000),]
```

### Findings

![Riders waiting for train]({{ site.baseurl }}/img/20170406_083102.jpg)

We decided to focus our analysis on riders entering each station as oppose to exiting the station. Our intuition was that riders entering the station would be more likely to talk to someone while waiting for a train to arrive at the station.

##### Top 10 busiest stations  

![Top 10 Stations]({{ site.baseurl }}/img/AverageEntriesForTop10Stations.png)

Grand Central Station, Herald Square and Penn Station seem to be top 3 busiest stations.

![10TopStationsOnMap]({{ site.baseurl }}/img/Top10StationsOnMap.png)

##### Busiest Days During Week  
![Traffic By Day of Week]({{ site.baseurl }}/images/MeanEntriesbyDay.png)

Week days are definitely busy in Grand Central, especially Wednesday through Friday.

##### Busiest Times during the Day
![Traffic By Time of Day]({{ site.baseurl }}/img/entriesIntoBusiestStations.png)

Most number of riders enter Grand Central Station and Herald Square during evening rush hour, between 4pm and 8pm.
