# sqlalchemy-challenge

- Find my work in my starter code folder

# Topic
Congratulations! You've decided to treat yourself to a long holiday vacation in Honolulu, Hawaii. To help with your trip planning, you decide to do a climate analysis about the area.

## Tools used
- Sqlite
- Python
- SQLAlchemy
- Flask
- Pandas
- Matplotlib

# Part 1: Analyze and Explore the Climate Data
In this section, you’ll use Python and SQLAlchemy to do a basic climate analysis and data exploration of your climate database. Specifically, you’ll use SQLAlchemy ORM queries, Pandas, and Matplotlib. To do so, complete the following steps:

1. Note that you’ll use the provided files (climate_starter.ipynb and hawaii.sqlite) to complete your climate analysis and data exploration.

2. Use the SQLAlchemy create_engine() function to connect to your SQLite database.
      
         engine = create_engine("sqlite:///Resources/hawaii.sqlite")
3. Use the SQLAlchemy automap_base() function to reflect your tables into classes, and then save references to the classes named station and measurement.
      
4. Link Python to the database by creating a SQLAlchemy session.
   
5. Perform a precipitation analysis and then a station analysis by completing the steps in the following two subsections.

   ## Precipitation Analysis
1. Find the most recent date in the dataset.

            recent_date = session.query(measurement.date).order_by(measurement.date.desc()).first()
            recent_date
3. Using that date, get the previous 12 months of precipitation data by querying the previous 12 months of data.

            last_year = dt.date(2017,8,23)- dt.timedelta(days = 365)
   
            year_data = session.query(measurement.date,func.max(measurement.prcp))\
          .filter(func.strftime('%Y-%m-%d',measurement.date) > last_year)\
          .group_by(measurement.date).all()
5. Select only the "date" and "prcp" values.

6. Load the query results into a Pandas DataFrame. Explicitly set the column names.

            df = pd.DataFrame(year_data, columns=['Date','Precipitation'])
7. Sort the DataFrame values by "date".

            df.sort_values('Date')
8. Plot the results by using the DataFrame plot method, as the following image shows:

            df.set_index('Date',inplace=True)
            plt.rcParams['figure.figsize'] = (10,5)
            df.plot(xticks = (0,60,120,180,240,300,365))
            plt.ylabel('Inches')
            plt.title('Aug 2016-Aug 2017 \n Daily Precipitation Measurements from Honolulu, HI' )
            plt.tight_layout()
   ![image](https://github.com/Jaynav04/sqlalchemy-challenge/assets/130405173/fbc586ea-4cde-480c-821a-342b1ee1f9b9)

10. Use Pandas to print the summary statistics for the precipitation data.

            precipitation_data = session.query(measurement.date,measurement.prcp)\
          .filter((measurement.date) > last_year).all()

            prec_df = pd.DataFrame(precipitation_data,columns = ['Date','Precipitation'])
            prec_df.dropna()

          prec_df.describe()
     ## Station Analysis
1. Design a query to calculate the total number of stations in the dataset.
   
            num_of_stations = session.query(station).count()
            num_of_stations
3. Design a query to find the most-active stations (that is, the stations that have the most rows). To do so, complete the following steps:
  - List the stations and observation counts in descending order.
  - Answer the following question: which station id has the greatest number of observations?

                active_stations = session.query(measurement.station,func.count(measurement.station))\
          .group_by(measurement.station)\
        .order_by(func.count(measurement.station).desc()).all()
            active_stations
3. Design a query that calculates the lowest, highest, and average temperatures that filters on the most-active station id found in the previous query.
   
            sel =[measurement.station,
            func.min(measurement.tobs),
            func.max(measurement.tobs),
            func.avg(measurement.tobs)]

            most_active_station = session.query(*sel).\
            filter(measurement.station == 'USC00519281').all()

            most_active_station
5. Design a query to get the previous 12 months of temperature observation (TOBS) data. To do so, complete the following steps:
   - Filter by the station that has the greatest number of observations.
   - Query the previous 12 months of TOBS data for that station.
  
                 last_12_months = session.query(measurement.tobs)\
            .filter(func.strftime(measurement.date) > last_year)\
            .filter(measurement.station == 'USC00519281').all()
   - Plot the results as a histogram with bins=12, as the following image shows
     
              temp_df = pd.DataFrame(last_12_months,columns=['tobs'])
            temp_df.plot.hist(bins=12)
            plt.show()
     ![image](https://github.com/Jaynav04/sqlalchemy-challenge/assets/130405173/0a4b0dfa-28e2-45c1-88b6-579cea1ffb69)

6. Close your session.

            session.close()
# Part 2: Design Your Climate App
Now that you’ve completed your initial analysis, you’ll design a Flask API based on the queries that you just developed. To do so, use Flask to create your routes as follows:

1. /
  - Start at the homepage.
  - List all the available routes.

2. /api/v1.0/precipitation
   - Convert the query results from your precipitation analysis (i.e. retrieve only the last 12 months of data) to a dictionary using date as the key and prcp as the value.
   - Return the JSON representation of your dictionary.

3. /api/v1.0/stations
  - Return a JSON list of stations from the dataset.

4. /api/v1.0/tobs
   - Query the dates and temperature observations of the most-active station for the previous year of data.
   - Return a JSON list of temperature observations for the previous year.

5. /api/v1.0/<start> and /api/v1.0/<start>/<end>
  - Return a JSON list of the minimum temperature, the average temperature, and the maximum temperature for a specified start or start-end range.
  - For a specified start, calculate TMIN, TAVG, and TMAX for all the dates greater than or equal to the start date.
  - For a specified start date and end date, calculate TMIN, TAVG, and TMAX for the dates from the start date to the end date, inclusive.

