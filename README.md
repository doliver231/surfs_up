# surfs_up

## Overview

The purpose of this analysis is to review a dataset pertaining to weather conditions that has been stored in a SQLite database to provide information that will convince an investor that opening up a Surf n' Shake shop in Oahu, Hawaii is a good business idea. The idea is that the shop will sell surf boards and ice cream throughout the year, but the investor is hesitant because he invested in a similar business that failed due to the weather conditions. In order to get this investor on board, we need to provide statistical analysis specifically on the weather conditions in Oahu that will convince him that this will be a successful business venture.

In order to explore the data in the SQLite database, we used SQLAlchemy to connect and generate queries to pull the necessary information needed for our analysis. Throughout this module, we used Jupyter notebook to import dependencies and create the commands to pull the data from the SQLite database.

## Resources

### Data Source

[SQLite file: hawaii.sqlite](https://github.com/doliver231/surfs_up/blob/main/hawaii.sqlite), 
[Jupyter Notebook Challenge file](https://github.com/doliver231/surfs_up/blob/main/SurfsUp_Challenge.ipynb),
[Flask Python file: app.py](https://github.com/doliver231/surfs_up/blob/main/app.py)

### Software

* Flask (ver 2.2.2)
* SQLAlchemy (ver 1.4.44)
* Jupyter Notebook

## Analysis

Here are some of the dependencies and modules used on Jupyter Notebook:

```py
import pandas as pd
import datetime as dt
import sqlalchemy
from sqlalchemy.ext.automap import automap_base
from sqlalchemy.orm import Session
from sqlalchemy import create_engine, func, inspect, extract
from flask import Flask, jsonify
```

Using SQLAlchemy Object Relational Mapper (ORM) to begin our query from SQLite database:

```py
engine = create_engine("sqlite:///hawaii.sqlite")
Base = automap_base()
Base.prepare(engine, reflect=True)
Measurement = Base.classes.measurement
Station = Base.classes.station
session = Session(engine)
```

To query the temperatures for the months of June and December:

```py
june = session.query(Measurement.date, Measurement.tobs).\
                filter(extract('month', Measurement.date) == 6).all()
temps_june = list(june)
june_df = pd.DataFrame(temps_june, columns=['Date','Temperature in June'])
june_df.set_index(june_df['Date'], inplace = True)
june_df = june_df.sort_index()
june_df.describe()
```
```py
december = session.query(Measurement.date, Measurement.tobs).\
                filter(extract('month', Measurement.date) == 12).all()
temps_dec = list(december)
dec_df = pd.DataFrame(temps_dec, columns=['Date','Temperature in December'])
dec_df.set_index(dec_df['Date'], inplace = True)
dec_df = dec_df.sort_index()
dec_df.describe()
```

![June Temps Stats](https://github.com/doliver231/surfs_up/blob/main/Images/Temps_in_June_Stats.png)
![December Temps Stats](https://github.com/doliver231/surfs_up/blob/main/Images/Temps_in_December_Stats.png)

We also used Visual Studio Code to create Python applications to share the results via a webpage by creating routes and using Terminal to run the `Flask` app. The Terminal generated the routes in a web address http://127.0.0.1:5000 that could be shared. Here is a snippet of the `Flask` application created ([app.py](https://github.com/doliver231/surfs_up/blob/main/app.py)):

```py
app = Flask(__name__)
@app.route("/")
def welcome():
    return(
    '''
     Welcome to the Climate Analysis API!<br/>
    Available Routes:<br/>
    /api/v1.0/precipitation<br/>
    /api/v1.0/stations<br/>
    /api/v1.0/tobs<br/>
    /api/v1.0/temp/start/end<br/>
    ''')
@app.route("/api/v1.0/precipitation")
def precipitation():
   prev_year = dt.date(2017, 8, 23) - dt.timedelta(days=365)
   precipitation = session.query(Measurement.date, Measurement.prcp).\
    filter(Measurement.date >= prev_year).all()
   precip = {date: prcp for date, prcp in precipitation}
   return jsonify(precip)

if __name__ == '__main__':
    app.run(debug=True)
```

Here is the output on Integrated Terminal on VS Code:

![Terminal Flask](https://github.com/doliver231/surfs_up/blob/main/Images/Flask_terminal.png)

The generated webpage when opened:

![Flask webpage](https://github.com/doliver231/surfs_up/blob/main/Images/Flask_webpage.png)

## Summary of Results

Regardless of the year, the key differences/similarities in weather between June and December:

* The average temperature is in the 70's for both months; temperature does not have major fluctuations throughout the year.
* Although similar statistics, the temperatures in December are slighly below those of June, whether it is the average, minimum, and maximum.
* The standard deviation of the average temperature for December is slightly higher than of June.

If we do an additional query (as seen in the Jupyter [challenge file](https://github.com/doliver231/surfs_up/blob/main/SurfsUp_Challenge.ipynb)) and add precipation results for both June and December, our output shows the following:

![June Stats Prcp](https://github.com/doliver231/surfs_up/blob/main/Images/Temps_Prcp_June.png)
![December Stats Prcp](https://github.com/doliver231/surfs_up/blob/main/Images/Temps_Prcp_December.png)

The temperatures in Oahu is relatively the same throughout the year and the chances of continuous rainfall is low. As we can see, the average precipitation in those months showed:

* June = 0.136 (14%) (better month compared to December!)
* December = 0.216 (22%)

Looking at the precipitation and temperature data proves that investing in Surf n' Shake is a good business venture, and that Oahu, Hawaii is an ideal location.