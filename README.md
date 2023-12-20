# NZ-Car-Theft
## Situation 
I was given the data to help raise awareness about motor vehicle thefts in New Zealand. The New Zealand police department planned to release a public service announcement to encourage citizens to be aware of thefts and stay safe. I dug into the stolen vehicles database to find details on when, which and where vehicles were most likely to be stolen. I used SQL workbench to write the queries to answer question and data analysis.

` SELECT YEAR(date_stolen), count(vehicle_id) AS num_vehicles `     
` FROM stolen_vehicles `      
`  GROUP BY YEAR(date_stolen); `

