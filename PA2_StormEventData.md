# The most harmful weather events on population health and on economy in USA
Nsi J.  
27 mai 2017  

#  1. Synopsis  

Storms and other severe weather events can cause both public health and economic problems for communities and municipalities. Many severe events can result in fatalities, injuries, and property damage, and preventing such outcomes to the extent possible is a key concern.    
This project involves exploring the U.S. National Oceanic and Atmospheric Administration's (NOAA) storm database. This database tracks characteristics of major storms and weather events in the United States, including when and where they occur, as well as estimates of any fatalities, injuries, and property damage.     
The events in the database began in the year 1950 and ended in November 2011.
The numerical data provided help knowing the total of fatalities, injuries, and property damage (in dollars) during this period.         

### The data analysis requires to address the following questions:       
1.	Across the United States, which types of events (as indicated in the EVTYPE variable) are most harmful with respect to population health?
2.	Across the United States, which types of events have the greatest economic consequences?  

#  2. Data pre/processing  
 


##  Download the data  

```r
library(utils)
url <- 'https://d396qusza40orc.cloudfront.net/repdata%2Fdata%2FStormData.csv.bz2'
if (!file.exists("data")) dir.create("data")
if (!file.exists("repdata-data-StormData.csv.bz2")) download.file(url, destfile = "./data/repdata-data-StormData.csv.bz2")
```
##  Code for reading, unzip the dataset, explore  the data, pre/process data and clean data


```r
# Load libraries
library(plyr)
library(reshape2)
library(ggplot2) 
library(grid)
library(gridExtra)
library(scales) 
library(utils)

# Read the file
stormdata_raw = read.csv(bzfile("repdata-data-StormData.csv.bz2"))

# Explore  the data
# head(stormdata_raw)  

##   STATE__           BGN_DATE BGN_TIME TIME_ZONE COUNTY COUNTYNAME STATE
## 1       1  4/18/1950 0:00:00     0130       CST     97     MOBILE    AL
## 2       1  4/18/1950 0:00:00     0145       CST      3    BALDWIN    AL
## 3       1  2/20/1951 0:00:00     1600       CST     57    FAYETTE    AL
## 4       1   6/8/1951 0:00:00     0900       CST     89    MADISON    AL
## 5       1 11/15/1951 0:00:00     1500       CST     43    CULLMAN    AL
## 6       1 11/15/1951 0:00:00     2000       CST     77 LAUDERDALE    AL
##    EVTYPE BGN_RANGE BGN_AZI BGN_LOCATI END_DATE END_TIME COUNTY_END
## 1 TORNADO         0                                               0
## 2 TORNADO         0                                               0
## 3 TORNADO         0                                               0
## 4 TORNADO         0                                               0
## 5 TORNADO         0                                               0
## 6 TORNADO         0                                               0
##   COUNTYENDN END_RANGE END_AZI END_LOCATI LENGTH WIDTH F MAG FATALITIES
## 1         NA         0                      14.0   100 3   0          0
## 2         NA         0                       2.0   150 2   0          0
## 3         NA         0                       0.1   123 2   0          0
## 4         NA         0                       0.0   100 2   0          0
## 5         NA         0                       0.0   150 2   0          0
## 6         NA         0                       1.5   177 2   0          0
##   INJURIES PROPDMG PROPDMGEXP CROPDMG CROPDMGEXP WFO STATEOFFIC ZONENAMES
## 1       15    25.0          K       0                                    
## 2        0     2.5          K       0                                    
## 3        2    25.0          K       0                                    
## 4        2     2.5          K       0                                    
## 5        2     2.5          K       0                                    
## 6        6     2.5          K       0                                    
##   LATITUDE LONGITUDE LATITUDE_E LONGITUDE_ REMARKS REFNUM
## 1     3040      8812       3051       8806              1
## 2     3042      8755          0          0              2
## 3     3340      8742          0          0              3
## 4     3458      8626          0          0              4
## 5     3412      8642          0          0              5
## 6     3450      8748          0          0              6

# dim(stormdata_raw)
## [1] 902297     37

#str(stormdata-raw)
#tail(stormdata_raw)
#class(stormdata_raw)
   

#table(stormdata_raw$EVTYPE)

#length(unique(stormdata_raw$EVTYPE))
## [1] 985


# Set the language
Sys.setlocale(category = "LC_ALL", locale = "english")
```

```
## [1] "LC_COLLATE=English_United States.1252;LC_CTYPE=English_United States.1252;LC_MONETARY=English_United States.1252;LC_NUMERIC=C;LC_TIME=English_United States.1252"
```

```r
# The data set import contains 902297 observations and 37 attributes, too much information not all capital for the data analysis

# Reduce the data based on needed variables, columns based

requiredColumns <- c("EVTYPE", "FATALITIES", "INJURIES", "PROPDMG", "PROPDMGEXP", "CROPDMG", "CROPDMGEXP")
subsettedStormData <- stormdata_raw[, requiredColumns]

str(subsettedStormData)
```

```
## 'data.frame':	902297 obs. of  7 variables:
##  $ EVTYPE    : Factor w/ 985 levels "   HIGH SURF ADVISORY",..: 834 834 834 834 834 834 834 834 834 834 ...
##  $ FATALITIES: num  0 0 0 0 0 0 0 0 1 0 ...
##  $ INJURIES  : num  15 0 2 2 2 6 1 0 14 0 ...
##  $ PROPDMG   : num  25 2.5 25 2.5 2.5 2.5 2.5 2.5 25 25 ...
##  $ PROPDMGEXP: Factor w/ 19 levels "","-","?","+",..: 17 17 17 17 17 17 17 17 17 17 ...
##  $ CROPDMG   : num  0 0 0 0 0 0 0 0 0 0 ...
##  $ CROPDMGEXP: Factor w/ 9 levels "","?","0","2",..: 1 1 1 1 1 1 1 1 1 1 ...
```

```r
# head(subsettedStormData)

# Rename the variables
 colnames(subsettedStormData) <- c( "evtype", "fatalities", "injuries", "propdmg", "propdmgexp", "cropdmg", "cropdmgexp")

# names(subsettedStormData)
# table(subsettedStormData$EVTYPE)

# Associate event type
# The NOAA storm database code book reports 48 event type. The event types in the data set are more than 9 hundred.

length(unique(subsettedStormData$EVTYPE))
```

```
## [1] 0
```

```r
## [1] 985

# Mix in naming of some values e.g.Damaging Freeze and DAMAGING FREEZE
# Seems to have a hierachy related to the event : to unify event description i choose to group values onto 17 events

# Function : Give each eventype an unified event description

EvtUnifiedName <- function(dd) 
  {
    evtgrouped <- data.frame(reg = c("NADO|FUNNEL|WATERSPOUT", "THUNDER|STORM|WIND", "HAIL", "FROST|FREEZ|BLIZZARD|WINTER|COLD|LOW TEMP|RECORD LOW|SNOW|ICE", "HEAT|WARM|RECORD HIGH", "COSTAL STORM", "SUNAMI", "RIP CURRENT", "FLASH FLOOD|FLD|FLOOD", "RIVER FLOOD|URBAN FLOOD", "TROPICALSTORM|TROPICAL", "HURRICANE", "DROUGHT",  "DUST STORM", "DUST DEVIL", "RAIN", "LIGHTNING"))

#  Lower cases
factorId <- c("Tornado", "Thunderstorm wind", "Hail", "Cold", "Heat", "Costal   Storm", "Sunami", "Rip current", "Flash flood", "River flood", "Tropical Storm", "Hurricane", "Drought", "Dust storm", "Dust devil", "Rain", "Ligntning")

    for (i in 1:nrow(evtgrouped)) 
      {
        indexFit <- grep(evtgrouped[i, "reg"], toupper(dd[, "evtype"]))
        if (length(indexFit) > 0) 
          {
            dd[indexFit, "event"] <- factorId[i]
          }
     }
    return(dd)
}

# Put value into event
subsettedStormData$event <- ("-")

subsettedStormData <- EvtUnifiedName(subsettedStormData)
otherIndex <- grep("-", subsettedStormData[, "event"])
subsettedStormData[otherIndex, "event"] <- "Other"
subsettedStormData$event <- as.factor(subsettedStormData$event)

# The event and its numbers
# table(subsettedStormData$event)

##              Cold           Drought        Dust devil        Dust storm 
##             46131              2512               150               429 
##       Flash flood              Hail              Heat         Hurricane 
##             85530            290400              2969               288 
##         Ligntning             Other              Rain       Rip current 
##             15776              9558             12238               777 
##       River flood            Sunami Thunderstorm wind           Tornado 
##               569                20            362662             71531 
##    Tropical Storm 
##               757

# PROPDMG and CROPDMG
##  Property and crop damage estimated for the event. 
##  These values are used to estimate the economic impact for type of events.
##  PROPDMGEXP and CROPDMGEXP are used as exponents to interpret the numeric values for the damage

#   Missing values
## Format the DMG and DMGEXP fields in absolute values. Undefined EXP properties, like +, ?, make the record NA

## Symbols 
## B ->  1,000,000,000
## M ->  1,000,000
## K ->  1,000
## H ->  100
## NA or BLANK ->	1

# Function : Caculate each entry for the property and crop damage value by 'dmg'
# and 'exp' variables

# Compute the amounts for property and corp damage 

## Formula
## propdmgValue = propdmg * propdmgexp
## cropdmgValue = cropdmg * cropdmgexp

# Caculate the property and crop damg by 'dmg' and 'exponent' variable

ActualDmgValue <- function(dd) {
    unit <- data.frame(cha = c("B", "M", "K", "H"), val = c(1e+09, 1e+06, 1000, 
        100))
    multi <- c(1e+09, 1e+06, 1000, 100)
    
    for (i in 1:nrow(unit)) {
        # index that match the unit
        indexCrodFit <- grep(unit[i, "cha"], toupper(dd[, "cropdmgexp"]))
        
        if (length(indexCrodFit) > 0) {
            
            # Caculate the actual value
            dd[indexCrodFit, "cropdmgvalue"] <- multi[i] * dd[indexCrodFit, 
                "cropdmg"]
                                     }
        
 # Same procudure for property damage
        indexProdFit <- grep(unit[i, "cha"], toupper(dd[, "propdmgexp"]))
        
        if (length(indexProdFit) > 0) {
            
            dd[indexProdFit, "propdmgvalue"] <- multi[i] * dd[indexProdFit, 
                "propdmg"]
                                      }
            }
    return(dd)
}

# Default value of the damage equals to variable 'dmg'
subsettedStormData$cropdmgvalue <- subsettedStormData$cropdmg
subsettedStormData$propdmgvalue <- subsettedStormData$propdmg
subsettedStormData <- ActualDmgValue(subsettedStormData)

# The summary of crop damage and property damage

summary(subsettedStormData$cropdmgvalue)
```

```
##      Min.   1st Qu.    Median      Mean   3rd Qu.      Max. 
## 0.000e+00 0.000e+00 0.000e+00 5.442e+04 0.000e+00 5.000e+09
```

```r
##     Min.  1st Qu.   Median     Mean  3rd Qu.     Max. 
## 0.00e+00 0.00e+00 0.00e+00 5.44e+04 0.00e+00 5.00e+09

summary(subsettedStormData$propdmgvalue)
```

```
##      Min.   1st Qu.    Median      Mean   3rd Qu.      Max. 
## 0.000e+00 0.000e+00 0.000e+00 4.736e+05 5.000e+02 1.150e+11
```

```r
##     Min.  1st Qu.   Median     Mean  3rd Qu.     Max. 
## 0.00e+00 0.00e+00 0.00e+00 4.74e+05 5.00e+02 1.15e+11

# Formula : Damage on economic, Damage on population
# ecodmgvalue = cropdmgvalue + propdmgvalue
# pophealthdmg = injures + fatalites

subsettedStormData$ecodmgvalue <- subsettedStormData$cropdmgvalue + subsettedStormData$propdmgvalue
subsettedStormData$pophealthdmg <- subsettedStormData$injuries + subsettedStormData$fatalities
summary(subsettedStormData$ecodmgvalue)
```

```
##     Min.  1st Qu.   Median     Mean  3rd Qu.     Max. 
## 0.00e+00 0.00e+00 0.00e+00 5.28e+05 1.00e+03 1.15e+11
```

```r
# Aggregate data subsetted 

StormData_corrected <- aggregate(cbind(injuries, fatalities, propdmgvalue, cropdmgvalue) ~ event, subsettedStormData, FUN = sum)
#   str(StormData_corrected)
## 'data.frame':	17 obs. of  5 variables:
##  $ event       : Factor w/ 17 levels "Cold","Drought",..: 1 2 3 4 5 6 7 8 9 10 ...
##  $ injuries    : num  6350 19 43 440 8680 ...
##  $ fatalities  : num  1088 6 2 22 1547 ...
##  $ propdmgvalue: num  1.27e+10 1.05e+09 7.19e+05 5.60e+06 1.62e+11 ...
##  $ cropdmgvalue: num  8.73e+09 1.40e+10 0.00 3.60e+06 7.22e+09 ...

StormData_tidy <- melt(StormData_corrected, id.var = "event", variable.name = "variable")
# str(StormData_tidy)
## 'data.frame':	68 obs. of  3 variables:
##  $ event     : Factor w/ 17 levels "Cold","Drought",..: 1 2 3 4 5 6 7 8 9 10 .
##  $ damagetype: Factor w/ 4 levels "injuries","fatalities",..: 1 1 1 1 1 1 1 1 1 1 ...
##  $ value     : num  6350 19 43 440 8680 ...

colnames(StormData_tidy) <- c("event", "damagetype", "value")
print(StormData_tidy)
```

```
##                event   damagetype        value
## 1               Cold     injuries         6350
## 2            Drought     injuries           19
## 3         Dust devil     injuries           43
## 4         Dust storm     injuries          440
## 5        Flash flood     injuries         8680
## 6               Hail     injuries         1467
## 7               Heat     injuries         9228
## 8          Hurricane     injuries         1328
## 9          Ligntning     injuries         5232
## 10             Other     injuries         3565
## 11              Rain     injuries          305
## 12       Rip current     injuries          529
## 13       River flood     injuries            3
## 14            Sunami     injuries          129
## 15 Thunderstorm wind     injuries        11388
## 16           Tornado     injuries        91439
## 17    Tropical Storm     injuries          383
## 18              Cold   fatalities         1088
## 19           Drought   fatalities            6
## 20        Dust devil   fatalities            2
## 21        Dust storm   fatalities           22
## 22       Flash flood   fatalities         1547
## 23              Hail   fatalities           45
## 24              Heat   fatalities         3172
## 25         Hurricane   fatalities          135
## 26         Ligntning   fatalities          817
## 27             Other   fatalities          657
## 28              Rain   fatalities          114
## 29       Rip current   fatalities          577
## 30       River flood   fatalities            5
## 31            Sunami   fatalities           33
## 32 Thunderstorm wind   fatalities         1220
## 33           Tornado   fatalities         5639
## 34    Tropical Storm   fatalities           66
## 35              Cold propdmgvalue  12683339763
## 36           Drought propdmgvalue   1046306000
## 37        Dust devil propdmgvalue       719130
## 38        Dust storm propdmgvalue      5599000
## 39       Flash flood propdmgvalue 162312938980
## 40              Hail propdmgvalue  17619991567
## 41              Heat propdmgvalue     20125750
## 42         Hurricane propdmgvalue  84756180010
## 43         Ligntning propdmgvalue    939007947
## 44             Other propdmgvalue   9577576400
## 45              Rain propdmgvalue   3265197192
## 46       Rip current propdmgvalue       163000
## 47       River flood propdmgvalue   5259639600
## 48            Sunami propdmgvalue    144062000
## 49 Thunderstorm wind propdmgvalue  64968720255
## 50           Tornado propdmgvalue  57002958829
## 51    Tropical Storm propdmgvalue   7716127550
## 52              Cold cropdmgvalue   8730107950
## 53           Drought cropdmgvalue  13972621780
## 54        Dust devil cropdmgvalue            0
## 55        Dust storm cropdmgvalue      3600000
## 56       Flash flood cropdmgvalue   7216670200
## 57              Hail cropdmgvalue   3114212873
## 58              Heat cropdmgvalue    904423500
## 59         Hurricane cropdmgvalue   5515292800
## 60         Ligntning cropdmgvalue     12097090
## 61             Other cropdmgvalue    572173030
## 62              Rain cropdmgvalue    919315800
## 63       Rip current cropdmgvalue            0
## 64       River flood cropdmgvalue   5058774000
## 65            Sunami cropdmgvalue        20000
## 66 Thunderstorm wind cropdmgvalue   1975024138
## 67           Tornado cropdmgvalue    414963020
## 68    Tropical Storm cropdmgvalue    694896000
```
# 3. Plots illustrating the results

```r
# List of events with highest fatalities

top10_fatal <-StormData_corrected[order(-StormData_corrected$fatalities), ][1:10, ]
# print(top10_fatal
##                event injuries fatalities propdmgvalue cropdmgvalue
## 16           Tornado    91439       5639  57002958829    414963020
## 7               Heat     9228       3172     20125750    904423500
## 5        Flash flood     8680       1547 162312938980   7216670200

# List of events with highest injuries
top10_injury <-StormData_corrected[order(-StormData_corrected$injuries), ][1:10, ]

# print(top10_injury)
##                event injuries fatalities propdmgvalue cropdmgvalue
## 16           Tornado    91439       5639  57002958829    414963020
## 15 Thunderstorm wind    11388       1220  64968720255   1975024138
## 7               Heat     9228       3172     20125750    904423500
## 5        Flash flood     8680       1547 162312938980   7216670200

par(mfrow = c(1, 2), mar = c(12, 4, 3, 2), mgp = c(3, 1, 0), cex = 0.8)
barplot(top10_fatal$fatalities, las = 3, names.arg = top10_fatal$event, main = "Events with Highest Fatalities", ylab = "Number of fatalities", col = "blue")

barplot(top10_fatal$injuries, las = 3, names.arg = top10_fatal$event, main = "Events with Highest Injuries", 
 ylab = "Number of injuries", col = "red")
```

![](PA2_StormEventData_files/figure-html/Top10_ events_with_highest_fatalities_and_highest_ injuries-1.png)<!-- -->

```r
echo = TRUE
```

```r
# List of events with highest property damages

top10_propdmg <-StormData_corrected[order(-StormData_corrected$propdmgvalue), ][1:10, ]

# print(top10_propdmg)
##                     event injuries fatalities propdmgvalue cropdmgvalue
## 5             Flash flood     8680       1547 162312938980   7216670200
## 8               Hurricane     1328        135  84756180010   5515292800
## 15      Thunderstorm wind    11388       1220  64968720255   1975024138
## 16                Tornado    91439       5639  57002958829    414963020

# List of events with highest corp damages
top10_cropdmg <-StormData_corrected[order(-StormData_corrected$cropdmgvalue), ][1:10, ]

# print(top10_cropdmg)
##                     event injuries fatalities propdmgvalue cropdmgvalue
## 2                 Drought       19          6   1046306000  13972621780
## 1                    Cold     6350       1088  12683339763   8730107950
## 5             Flash flood     8680       1547 162312938980   7216670200
## 8               Hurricane     1328        135  84756180010   5515292800

par(mfrow = c(1, 2), mar = c(12, 4, 3, 2), mgp = c(3, 1, 0), cex = 0.8)
barplot(top10_propdmg$propdmgvalue/(10^9), las = 3, names.arg = top10_propdmg$event, 
        main = "Events with Highest Property Damages", ylab = "Damage Cost ($ billions)", 
        col = "blue")
barplot(top10_cropdmg$cropdmgvalue/(10^9), las = 3, names.arg = top10_cropdmg$event, 
        main = "Events With Highest Crop Damages", ylab = "Damage Cost ($ billions)", 
        col = "red")
```

![](PA2_StormEventData_files/figure-html/Top10_ events_with_highest_properties_damages_and_highest_crop_damages-1.png)<!-- -->

```r
echo = TRUE
```
# 4. Conclusion

The first event that caused the maximum number of fatalities and injuries was Tornados. It was followed by Excessive Heat for fatalities and Thunderstorm wind for injuries.

The first event that caused the maximum property damage was Flood followed by Hurricanes/Typhoos. 

The first event that caused the maximum crop damage was Drought followed by Cold. 
From the results obtaining from the plots, i can say that       
1. The most harmful weather event for population health is tornado    
2. The most harmful weather event for economy is flash flood by adding this event both for property and crop damage

 
