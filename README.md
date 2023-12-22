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

#### 5. Create a heat map of the table comparing the vehicle types and colors
I exported the data as CSV file and used excel to create heat map.  
I have attached its picture obj2_heat_map for reference.  
The heat map allowed me to extract the top 3 vehicle types that were sedans and there was no relation with any specific color. But for trailer, silver and grey specifically showed a high number of thefts. The same trend was found for boat-trailer and Heavy-trailer. That makes sense because most heavy vehicles usually come in silver or grey color. For Utility and Light Van white was the highest in number.  
### Objective 3: Identify where vehicles are likely to be stolen.  
The third objective is to explore the population and density statistics in the regions table to identify where vehicles are getting stolen and visualize the results using a scatter plot and map.
#### 1. Find the number of vehicles that were stolen in each region.  
I combined the data from stolen_vehicles and location using Left Join.
##### Query
` SELECT region, COUNT(vehicle_id) AS num_vehicle `   
` FROM stolen_vehicles sv LEFT JOIN locations l `  
` 	ON sv.location_id = l.location_id `  
` GROUP BY region; `  
##### Results
|region|num_vehicle|
|:----------------:|:---------:|
|Auckland|1638|
|Gisborne|176|
|Hawke's Bay|100|
|Canterbury|660|
|Wellington|420|
|Otago|139|
|Manawatū-Whanganui|139|
|Northland|234|
|Bay of Plenty|446|
|Waikato|371|
|Nelson|92|
|Taranaki|112|
|Southland|26|  

#### 2. Combine the previous output with the population and density statistics for each region
##### Query
` SELECT l.region, COUNT(sv.vehicle_id) AS num_vehicle, l.population, l.density `   
` FROM stolen_vehicles sv LEFT JOIN locations l `  
` 	ON sv.location_id = l.location_id `  
` GROUP BY l.region, l.population, l.density `  
` ORDER BY num_vehicle DESC; `
##### Results
|region|num_vehicle|population|density|
|:-----------------------:|:----------:|:------------:|:----------:|
|Auckland|1638|1695200|343.09|
|Canterbury|660|655000|14.72|
|Bay of Plenty|446|347700|28.80|
|Wellington|420|543500|67.52|
|Waikato|371|513800|	21.50|
|Northland|234|201500|16.11|
|Gisborne|176|52100|6.21|
|Otago|139|246000|7.89|
|Manawatū-Whanganui	|139|258200|11.62|
|Taranaki|112|127300|17.55|
|Hawke's Bay|100|182700|12.92|
|Nelson|92|54500|129.15|
|Southland|26|	102400|3.28|

#### 3. Do the types of vehicles stolen in the three most dense regions differ from the three least dense regions?   
First, I found 3 most and least dense regions.  
##### Query  
` SELECT l.region, COUNT(sv.vehicle_id) AS num_vehicle, l.population, l.density `   
` FROM stolen_vehicles sv LEFT JOIN locations l `  
` 	ON sv.location_id = l.location_id `  
` GROUP BY l.region, l.population, l.density `  
` ORDER BY l.density DESC; `  
##### Results    
My top 3 regions were:    
|region|num_vehicle|population|density|
|:----------------:|:----------:|:---------------:|:-----------:|
|Auckland|1638|1695200|343.09|
|Nelson|92|54500|129.15|
|Wellington|420|543500|67.52|  			
			
			
And my last 3 regions were:  
|region|num_vehicle|population|density|
|:----------------:|:----------:|:---------------:|:-----------:|
|Otago|139|246000|7.89|
|Gisborne|176|52100|6.21|
|Southland|26|	102400|3.28|

Then I found the vehicle types in top 3 most dense and least dense regions.
##### Query  
` SELECT sv.vehicle_type, COUNT(sv.vehicle_id) as num_vehicle `  
` FROM stolen_vehicles sv LEFT JOIN locations l `  
` 	ON sv.location_id = l.location_id `  
` WHERE region IN ('Auckland', 'Nelson', 'Wellington') `  
` GROUP BY sv.vehicle_type `  
` ORDER BY num_vehicle DESC; `  
##### Results  
|vehicle_type|num_vehicle|
|:-------------------:|:-----------:|
|Stationwagon|	415|
|Saloon|403|
|Hatchback|361|
|Roadbike|217|
|Trailer|206|
|Utility|136|
|Moped|117|
|Light Van|99|
|Boat Trailer|44|
|Trailer – Heavy|42|
|Other Truck|21|
|Caravan|20|
|Sports Car|16|
|Flat Deck Truck|11|
|Convertible|9|
|Mobile Home – Light|5|
|Heavy Van|4|
|Cab and Chassis Only|4|
|Light Bus|3|
|Mobile Machine|2|  
To find the vehicle types in least 3 dense regions, I used the following query:  
##### Query  
` SELECT sv.vehicle_type, COUNT(sv.vehicle_id) as num_vehicle `  
` FROM stolen_vehicles sv LEFT JOIN locations l `  
` 	ON sv.location_id = l.location_id `  
` WHERE region IN ('Otago', 'Gisborne', 'Southland') `  
` GROUP BY sv.vehicle_type `  
` ORDER BY num_vehicle DESC; `    
##### Results  
|vehicle_type|num_vehicle|
|:------------------:|:-----------:|
|Stationwagon|	79|
|Saloon|77|
|Utility|50|
|Trailer|44|
|Hatchback|43|
|Roadbike|7|
|Boat Trailer|6|
|Trailer – Heavy|6|
|Moped|6|
|Sports Car|5|
|Light Van|4|
|Other Truck|3|
|Mobile Home – Light|3|
|Caravan|2|
|Cab and Chassis Only|1|
|Trail Bike|1|
|Flat Deck Truck|1|  

To compare the results of both queries, I had used a Union function because both had same columns and I also put a new column of High Density/Low Density to differentiate the results. Then I restricted the results to 5 for each query to make the results comparable.  
##### Query  
` (SELECT 'High Density', sv.vehicle_type, COUNT(sv.vehicle_id) as num_vehicle `  
` FROM stolen_vehicles sv LEFT JOIN locations l `  
` 	ON sv.location_id = l.location_id `  
` WHERE region IN ('Auckland', 'Nelson', 'Wellington') `  
` GROUP BY sv.vehicle_type `  
` ORDER BY num_vehicle DESC `  
` LIMIT 5) `  
` UNION `
` (SELECT 'Low Density', sv.vehicle_type, COUNT(sv.vehicle_id) as num_vehicle `  
` FROM stolen_vehicles sv LEFT JOIN locations l `  
` 	ON sv.location_id = l.location_id `  
` WHERE region IN ('Otago', 'Gisborne', 'Southland') `
` GROUP BY sv.vehicle_type `  
` ORDER BY num_vehicle DESC `  
` LIMIT 5); `     
##### Results  
|density|vehicle_type|num_vehicle|
|:---------------:|:---------------------:|:-----------:|
|High Density|Stationwagon|415|
|High Density|Saloon|403|
|High Density|Hatchback|361|
|High Density|Roadbike|217|
|High Density|Trailer|206|
|Low Density|Stationwagon|79|
|Low Density|Saloon|77|
|Low Density|Utility|50|
|Low Density|Trailer|44|
|Low Density|Hatchback|43|  



Only Roadbike and Utility were differentiators, rest of vehicle_type were same for both areas.    

#### 4. Create a scatter plot of population versus density, and change the size of the points based on the number of vehicles stolen in each region.
I exported the data in CSV format and used excel to create visualization. I have attached the picture Obj3a_scatter plot for your reference.  

The visualization showed that Auckland had the largest population, density and highest number of vehicles were stolen from there. In Auckland that made sense because a lot of vehicles were present in small area which increased the chances of car theft, but Nelson was outlier and showed some interesting results. It had high density in fact second highest density, but number of vehicles stolen were very low. It helped me to devise that biggest driver for vehicle thefts was population rather than density. The rest of results were showing the expected trend that higher the density and population, higher would be the number of thefts.







