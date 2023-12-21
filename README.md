# NZ-Car-Theft
## Situation 
I was given the data to help raise awareness about motor vehicle thefts in New Zealand. The New Zealand police department planned to release a public service announcement to encourage citizens to be aware of thefts and stay safe. I dug into the stolen vehicles database to find details on when, which and where vehicles were most likely to be stolen. I used SQL workbench to write the queries to answer question and data analysis.  
##### Query

` SELECT YEAR(date_stolen), count(vehicle_id) AS num_vehicles `     
` FROM stolen_vehicles `      
` GROUP BY YEAR(date_stolen); `  
##### Results
2021	-	1668  
2022	-	2885
#### 2. Find the number of vehicles stolen each month.  
##### Query
` SELECT MONTH(date_stolen), count(vehicle_id) AS num_vehicles `  
` FROM stolen_vehicles `  
` GROUP BY MONTH(date_stolen) `  
` ORDER BY MONTH(date_stolen); `  
##### Results
	MONTH(date_stolen)	              num_vehicles  
	1	                                  740  
	2	                                  763  
	3	                                  1053  
	4	                                  329  
	10	                                  464  
	11	                                  560  
	12	                                  644  
The data showed anomaly for the month of April. The number was significantly low compared with other months data. I took a deeper look in April’s data and found that only 6 days of data was available for April causing the number of thefts to be so low.  
Looking at the data, I can clearly see that more vehicles were stolen during summer times (December to March).
#### 3. Find the number of vehicles stolen each day of the week.  
##### Query  
` SELECT DAYOFWEEK(date_stolen) AS dow, COUNT(vehicle_id) AS num_vehicles `  
` FROM stolen_vehicles `  
` GROUP BY DAYOFWEEK(date_stolen) `  
` ORDER BY dow; `  
##### Results  
    dow              num_vehicles       
     1	                595    
     2	                767  
     3	                711  
     4	                629  
     5	                619  
     6	                655  
     7	                577  
##### Query  
` SELECT DAYOFWEEK(date_stolen) AS dow, `    
`       CASE WHEN DAYOFWEEK(date_stolen) = 1 THEN 'Sunday' `  
	     `          WHEN DAYOFWEEK(date_stolen) = 2 THEN 'Monday' `    
         `          WHEN DAYOFWEEK(date_stolen) = 3 THEN 'Tuesday' `  
         `          WHEN DAYOFWEEK(date_stolen) = 4 THEN 'Wednesday' `  
         `          WHEN DAYOFWEEK(date_stolen) = 5 THEN 'Thursday' `  
         `          WHEN DAYOFWEEK(date_stolen) = 6 THEN 'Friday' `  
 `      ELSE 'Saturday' END AS day_of_week, `  
	` COUNT(vehicle_id) AS num_vehicles `  
` FROM stolen_vehicles `  
` GROUP BY DAYOFWEEK(date_stolen), day_of_week `  
` ORDER BY dow; `		      
##### Results  
    dow       day_of_week    num_vehicles  
    1	        Sunday	          595  
    2	        Monday	          767  
    3	        Tuesday	          711  
    4	       Wednesday	  629  
    5	       Thursday	          619  
    6	        Friday	          655  
    7	       Saturday	          577  
#### 5. Create a bar chart that shows the number of vehicles stolen on each day of the week
To create bar char, I exported my data to excel as CSV file.
To my surprise the numbers were highest on Mondays and Tuesdays. Then, there was a drop and it again rose on Friday.  
Graph uploaded as file Obj1_bar chart  
### OBJECTIVE 2: Identify which vehicles are likely to be stolen
The second objective is to explore the vehicle type, age, luxury vs standard and color fields in the stolen_vehicles table to identify which vehicles are most likely to be stolen.

### OBJECTIVE 2: Identify which vehicles are likely to be stolen
The second objective is to explore the vehicle type, age, luxury vs standard and color fields in the stolen_vehicles table to identify which vehicles are most likely to be stolen.

#### 1. Find the vehicle types that are most often and least often stolen.     
##### Query
` SELECT vehicle_type, COUNT(vehicle_id) as num_vehicles `  
` FROM stolen_vehicles `  
` Group By vehicle_type `  
` Order BY num_vehicles DESC `  
` LIMIT 5; `
##### Results
|Vehicle_type|num_vehicles| 
|:------------------:|:------------:| 
|Stationwagon|945| 
|Saloon|851|  
|Hatchback|644|  
|Trailer|582|  
|Utility|466|
##### Query 
` SELECT vehicle_type, COUNT(vehicle_id) as num_vehicles`  
` FROM stolen_vehicles `  
` GROUP BY vehicle_type `  
` ORDER BY num_vehicles `  
` LIMIT 5; `

##### Results
|vehicle_type|num_vehicles|
|:-------------------:|:--------:|
|Articulated Truck|1|
|Special Purpose Vehicle|1|
|Mobile Machine|2|
|Trail Bike|2|
|Tractor|4|  

I found that Stationwagon is highest stolen vehicle type, on the other hand Articulated Truck/Special Purpose vehicle is lowest in number.

#### 2. For each vehicle type, find the average age of the cars that are stolen.
##### Query
` SELECT vehicle_type, AVG(YEAR(date_stolen) - model_year) AS avg_age `  
` FROM stolen_vehicles `  
` GROUP BY vehicle_type `  
` Order BY avg_age DESC `  
##### Results
|vehicle_type|avg_age|
|:-------------------------------:|:--------------:|
|Special Purpose Vehicle|64.0000|
|Mobile Home – Light|34.6667|
|Flat Deck Truck|27.8235|
|Caravan|27.8182|
|Other Truck|23.1905|
|Convertible|22.6667|
|Heavy Van|22.5714|
|Sports Car|22.0000|
|Trail Bike|20.5000|
|Light Van|19.4351|
|Stationwagon|	19.2148|
|Saloon|19.0529|
|Utility|17.8176|
|Hatchback|16.2671|
|Articulated Truck|15.0000|
|Light Bus|14.6667|
|Boat Trailer|13.2762|
|Trailer – Heavy|11.4556|
|Trailer|11.4141|
|All Terrain Vehicle|10.6000|
|Cab and Chassis Only|10.5000|
|Roadbike|9.5488|
|Moped|7.4813|
|Tractor|7.0000|
|Mobile Machine|4.0000|  

The age of vehicle was not given, so I first calculated the age. The results suggested that newer mobile machine, tractors, and Mopeds were often stolen.   

#### 3. For each vehicle type, find the percent of vehicles stolen that are luxury versus standard.  
I did not have the information of luxury or standard in stolen_vehicles table, it was present in make_details table. So, I used LEFT JOIN to create a new table with data from both stolen_vehicles and make_details table. I used LEFT JOIN because stolen_vehicles was my main table with all data. Then, I used WITH clause to create a common table with relevant data only.
##### Query
` WITH lux_standard AS (SELECT vehicle_type, CASE WHEN make_type = 'Luxury' THEN 1 ELSE 0 END AS luxury `  
` FROM stolen_vehicles sv LEFT JOIN make_details md `  
` ON sv.make_id = md.make_id) `  
` SELECT vehicle_type, SUM(luxury)/COUNT(luxury) * 100 AS pct_luxury `
` FROM lux_standard `  
` GROUP BY vehicle_type `  
` ORDER BY pct_luxury DESC; `  

##### Results  
|vehicle_type|pct_luxury|
|:--------------------:|:--------------:|
|Convertible|50.0000|
|Sports Car|22.5000
|Heavy Van|14.2857|
|Saloon|12.9260|
|Stationwagon|3.7037|
|Hatchback|3.2609|
|Roadbike|1.3468|
|Light Van|1.2987|
|Utility|0.2146|
|Trailer|0.0000|
|Boat Trailer|0.0000|
|Moped|0.0000|
|Trailer – Heavy|0.0000|
|Caravan|0.0000|
|Tractor|0.0000|
|Trail Bike|0.0000|
|All Terrain Vehicle|0.0000|
|Other Truck|0.0000|
|Flat Deck Truck|0.0000|
|Light Bus|0.0000|
|Mobile Home – Light|0.0000|
|Special Purpose Vehicle|0.0000|
|Articulated Truck|0.0000|
|Cab and Chassis Only|0.0000|
|Mobile Machine|0.0000|  

The results showed that half of convertibles and about 22% sports cars stolen were luxury vehicles.  

#### 4. Create a table where the rows represent the top 10 vehicle types, the columns represent the top 7 vehicle colors (plus 1 column for all other colors) and the values are the number of vehicles stolen 
##### Query
` SELECT color, COUNT(vehicle_id) AS num_vehicles `  
` FROM stolen_vehicles `  
` GROUP BY color `  
` ORDER BY num_vehicles DESC; `  

` SELECT vehicle_type, COUNT(vehicle_id) AS num_vehicle, `  
`		SUM(CASE WHEN color = 'Silver' THEN 1 ELSE 0 END) AS Silver, `  
` 		SUM(CASE WHEN color = 'White' THEN 1 ELSE 0 END) AS White, `  
` 		SUM(CASE WHEN color = 'Black' THEN 1 ELSE 0 END) AS Black, `  
` 		SUM(CASE WHEN color = 'Blue' THEN 1 ELSE 0 END) AS Blue, `  
` 		SUM(CASE WHEN color = 'Red' THEN 1 ELSE 0 END) AS Red, `  
` 		SUM(CASE WHEN color = 'Grey' THEN 1 ELSE 0 END) AS Grey, `  
` 		SUM(CASE WHEN color = 'Green' THEN 1 ELSE 0 END) AS Green, `  
` 		SUM(CASE WHEN color IN ('Gold', 'Brown', 'Yellow', 'Orange',                      'Purple', 'Cream', 'Pink') `     
` 		THEN 1 ELSE 0 END) AS Other `  
` FROM stolen_vehicles `  
` GROUP BY vehicle_type `  
` ORDER BY num_vehicle DESC `  
` LIMIT 10; `  


##### Results
|Vehicle_type|num_vehicle|Silver|White|Black|Blue|Red|Grey|Green|Other| 
|:--------------------:|:--------:|:------:|:------:|:------:|:------:|:------:|:------:|:------:|:------:|
|Stationwagon|	945|223|159|141|142|84|84|	59|53|
|Saloon|851|226|160|99|125|	75|71|52|43|
|Hatchback|644|172|114|76|104|58|46|24|50|
|Trailer|582|399|21|29|17|9|73|22|12|
|Utility|466|71|183|36|46|45|32|38|15|
|Roadbike|297|17|42|105|38|	51|11|12|21|
|Moped|187|8|25|85|	18|34|3|1|13|
|Light Van|154|19|104|0|5|7|	7|6|6|
|Boat Trailer|105|67|5|3|0|0|	29|0|1|
|Trailer – Heavy|90|53|9|1|3|0|12|2|10|  

The results showed Silver and White are the top colors for most of the vehicle types.







