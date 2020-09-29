# Sanitary Sewer Overflows
![](https://encrypted-tbn0.gstatic.com/images?q=tbn%3AANd9GcQ4EreGzBWX2DaX8Scl4aT-SasVzuzGD__isw&usqp=CAU) ![](https://d33wubrfki0l68.cloudfront.net/e7ed9fe4bafe46e275c807d63591f85f9ab246ba/e2d28/assets/images/tux.png)

### Acknowledgement
Data is collected gathered from :star:[bloomington gov](https://data.bloomington.in.gov) 
- [data source 1](https://data.bloomington.in.gov/dataset/sanitary-sewer-overflows/resource/2e44981b-bb63-46b3-ba66-9b3b09786ec4) | [data source 2](https://data.world/city-of-bloomington/51fdd0d4-2fa2-4dd4-a877-8f683fb72f93) 
- data released under [Creative Commons Attribution license](http://opendefinition.org/licenses/cc-by/)

### Requirements
*The following are the requirements to run the notebooks*

---
- [Databricks community account](https://community.cloud.databricks.com/login.html) which is Free
- Cluster runtime 5.4 with spark 2.4 to 3.0
- Import the `waste-water-analysis.dbc` file to Databricks and it will generate all the required tools

## Overview
Sanitary Sewer Overflows (SSO) are releases of untreated sewage into the environment. City of Bloomington Utilities Department records and maintains data for all SSO events that occur within Bloomington's wastewater collection and treatment system. Additionally, each event is reported to the Indiana Department of Environmental Management.


Excel Worksheet labeled "Sanitary Sewer Overflow Master" is data recorded following each SSO event from 1996 forward. This contains SSO incidents from 1996 forward, including overflow dates, locations, estimated flow, and any additional data we have about the individual event (i.e. precipitation, blockage, power outage, snow melt, etc).


## Objectives
- Understanding Data Quality checks
- Generating Ideas on how we can achieve the main goal(s)
- Verify the given matadata
- Simulate the main project
- Learning Pyspark
- Data Cleaning, Data Quality checks
- Setup a Delta Lake Architecture


**Data Dictionary**

|Column	    |   Type	    |     Label |	Description |
|:-----------|:---------------|:-----------:|---------------:|
|Manhole    |	text		|           |               |
|Start_Date	|    timestamp	|	 n/a      |
|End_Date    |	text		|           |
|Location	|    text		|           |
|Event	    |     text		|           |     n/a
|Rain	    |    text		|           |
|Gallons     | 	text		|           |
|Lat         |	numeric		|           |
|Long        | 	numeric     |           |               |

---

## Tasks
1. verify features(columns).
2. verify data types.
3. understand the meaning of missing data.
4. verify data entries.
5. explode/split compound feaures to simpler/atomic features.
6. what assumptions can you draw from the data or what do you understand?
7. create new aggrigate fetures from your assumptions validated using domain knowledge.
8. visualize your assumption or relationships that might exist.
9. So what might be the main problem behind the problem and how can this data help better the situation?
10. Apply the thoughts from 9 and validate using domain knowledge or with stackholders.
11. suppose the ideas are valid and we have a stream of data, implement a SPARK STRUCTURED STREAM **ETL**
12. Build a DataBricks DashBoard using the streamed data/ static data.
13. Can the dashboard answer business Questions? 
14. if #13 is No/not sure! what is irralevant and what can be improved?

# Data quality checks overview Report
```python
# review 1st 5 rows
sewer_df.show(5)
```
**Output**
```
+-------+-------------------+-------------------+--------------------+-------------+----+-------+-----------+------------+
|Manhole|         Start_Date|           End_Date|            Location|        Event|Rain|Gallons|        Lat|        Long|
+-------+-------------------+-------------------+--------------------+-------------+----+-------+-----------+------------+
|   3430|1996-01-17 00:00:00|1996-01-17 00:00:00|        Gifford Road|Precipitation|null|   9000|39.15461147|-86.58559815|
|   1004|1996-01-17 00:00:00|1996-01-17 00:00:00|        Micro Motors|Precipitation|null| 378000|39.15424046|-86.53475475|
|   3607|1996-01-17 00:00:00|1996-01-17 00:00:00|  Sherwood Oaks Park|Precipitation|null|   6000|39.12983645|-86.51441395|
|   3138|1996-01-17 00:00:00|1996-01-17 00:00:00|Tapp Road Lift St...|Precipitation|null|  16000| 39.1366197|-86.56178514|
|   1004|1996-01-23 00:00:00|1996-01-23 00:00:00|        Micro Motors|Precipitation|null|  90000|39.15424046|-86.53475475|
+-------+-------------------+-------------------+--------------------+-------------+----+-------+-----------+------------+
only showing top 5 rows
```

```python
# let's verify schema with the data dctionary & what we saw from the previous cell
sewer_df.printSchema()
```
**output**
```
root
 |-- Manhole: string (nullable = true)
 |-- Start_Date: timestamp (nullable = true)
 |-- End_Date: string (nullable = true)
 |-- Location: string (nullable = true)
 |-- Event: string (nullable = true)
 |-- Rain: string (nullable = true)
 |-- Gallons: string (nullable = true)
 |-- Lat: double (nullable = true)
 |-- Long: double (nullable = true)

```
```python
# let's cast Manhole-->int, End_Date-->timestamp and Gallons-->integer/long
sewer_df = (sewer_df.withColumn('End_Date', sewer_df.End_Date.cast('timestamp'))
                    .withColumn('Gallons', sewer_df.Gallons.cast('int'))
                    .withColumn('Manhole', sewer_df.Manhole.cast('int'))
                    )
# print changes
sewer_df.printSchema()
```
**output**
```
root
 |-- Manhole: integer (nullable = true)
 |-- Start_Date: timestamp (nullable = true)
 |-- End_Date: timestamp (nullable = true)
 |-- Location: string (nullable = true)
 |-- Event: string (nullable = true)
 |-- Rain: string (nullable = true)
 |-- Gallons: integer (nullable = true)
 |-- Lat: double (nullable = true)
 |-- Long: double (nullable = true)
```

## Missing value report
---
It is important to discuss the missing data with the stackholders or the data team<br>
to understand the infomation convey by the missing data. 

- Lets take for example the Rain Feature with 608 missing data, what does that mean?
> The was no rain that particular day, right?<br>
> So this means we can't drop the rain feature instead we fill it with a reasonable value e.g **'No Rain'**

- **Location, long and lat** on the other hand might need the stackholders/ data team to clearify<br>
if the is some link/direction info between the pipes  which can help us infer the location, before any assumption.

- **'Event'** most being **Precipitation** is rain, snow, sleet, or hail — any kind of weather condition where something's falling from the sky.<br>
> This feature will make sense to be null/missing if the is also no rain, but not always.<br>
> let's mark Nan as **clear sky** unless validated otherwise.

**Lets investigate a bit more into these categorical features**


### Missing values Assumptions Review
---
 - So **Rain** is the measure of rain in volume/litres not what we suspected. **We must extend the data dictionary**,<br>
 > now is better we can fill Null for Rain with a Zero meaning the was no Rain.<br>
 > Wait! This is not a hackathon Allie, we might need to pull weather data from google Earth to validate the assumptions.
 
 - **'Event' has 48** most being **Precipitation** but we can see that we have 48 unique values for Event e.g **Blockage, Sewer main blockage , power and more**<br>
 
 - **Location & Event** Seems to be compound features,<br> we need to do some deep dive mining and  see how we can explode them if possible
 
 - Cast **Rain** to float which in pyspark is *DoubleType or Just FloatType* following Java data-types
 
 #### more drill and wrangling is required here
 
# WORK IN PROGRESS 
- Due to the kick-off the main PoC project this project had to be paused.
- Looking forward to complete all the task to enhence my skills
