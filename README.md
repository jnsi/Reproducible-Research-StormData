# 

## Session information  
sessionInfo()
R version 3.3.3 (2017-03-06)
Platform: x86_64-w64-mingw32/x64 (64-bit)
Running under: Windows 7 x64 (build 7601) Service Pack 1


## Data analysis steps by steps     
1- Download raw file   
2- Explore data with str, head, dim, class ...commands lines     

3- Process/transform the data (if necessary) into a format suitable for analysis    
31- Subset data by selecting some variables based on impact analysis for health and economic against weather
    "EVTYPE", "FATALITIES", "INJURIES", "PROPDMG", "PROPDMGEXP", 
    "CROPDMG", "CROPDMGEXP"   
 32- Group event types to reduce the number from 48 to 17    
 33- Give to each eventype an unified event description   
 34- Compute PROPDMG and CROPDMG variables,
    Choose a strategy to fill all of the missing values with 1 when exponent for PROPDMGEXP" and CROPDMGEXP has a value as +, ?     
 35- Caculate the property and crop damge by 'dmg' and 'exponent' variable and also for property damage       
 36- After cleaning data, obtain a tidy_data    

### Tidy dataset   

The tidy dataset, used for the analysis, is obtained by:   
1- Retaining the required variables(columns) good for data analysis   
2- Renames these variables    
3- Group the events types to reduce them to 17 event types. The NOAA storm databasecode book reports 48 event type. The event types in the data set are more than 9  hundred. Some events have 0 or very lower counts    
  
   table(stormdata_raw$EVTYPE)      
   length(unique(stormdata_raw$EVTYPE))        
   [1] 985       
  
4- Applying the exponent to the property and crop amounts         

### With the corrected dataset      
1- Aggregate data     
2- plots the 10 top of storm events for fatalities, injuries, property and crop damages     
            
## Conclusion 
Answer the 2 main questions
 


   
