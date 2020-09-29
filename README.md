# Sanitary Sewer Overflows
![](https://encrypted-tbn0.gstatic.com/images?q=tbn%3AANd9GcQ4EreGzBWX2DaX8Scl4aT-SasVzuzGD__isw&usqp=CAU) ![](https://d33wubrfki0l68.cloudfront.net/e7ed9fe4bafe46e275c807d63591f85f9ab246ba/e2d28/assets/images/tux.png)

### Acknowledgement
:star:[bloomington gov](https://data.bloomington.in.gov) 
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

# WORK IN PROGRESS 
- Due to the kick-off the main PoC project this project had to be paused.
- Looking forward to complete all the task to enhence my skills
