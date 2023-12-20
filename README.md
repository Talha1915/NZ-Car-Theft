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
#### 4. Replace the numeric day of week values with the full name of each day of the week (Sunday, Monday, Tuesday, etc.)  
##### Query  
` SELECT DAYOFWEEK(date_stolen) AS dow,     
		CASE WHEN DAYOFWEEK(date_stolen) = 1 THEN 'Sunday'    
			WHEN DAYOFWEEK(date_stolen) = 2 THEN 'Monday'       
		WHEN DAYOFWEEK(date_stolen) = 3 THEN 'Tuesday'    
		WHEN DAYOFWEEK(date_stolen) = 4 THEN 'Wednesday'     
		WHEN DAYOFWEEK(date_stolen) = 5 THEN 'Thursday'     
		WHEN DAYOFWEEK(date_stolen) = 6 THEN 'Friday'    
		ELSE 'Saturday' END AS day_of_week,    
			COUNT(vehicle_id) AS num_vehicles    
FROM stolen_vehicles    
GROUP BY DAYOFWEEK(date_stolen), day_of_week    
ORDER BY dow; `      
##### Results  
    dow       day_of_week    num_vehicles  
    1	        Sunday	          595  
    2	        Monday	          767  
    3	        Tuesday	          711  
    4	       Wednesday	  629  
    5	       Thursday	          619  
    6	        Friday	          655  
    7	       Saturday	          577  
     





