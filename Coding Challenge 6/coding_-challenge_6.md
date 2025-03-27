# Q.No.1

### Writing function and iteration helps to do repetative tasks and save alot of time it allow copy pasting and make the script easy to read and use

# Q.No.2

**To write function**

### To write a function we use function() keyword to perform a task. then function() take the input arguments and return the results by applying results().

**To write a loop**

### in the order to make a loop we use for and put i as a iteration in the data and then print i. In this way same code run for each one

``` r
### How to write a function in R
F_to_C <- function(f_temp){     # Example of writing a function
  Celcius<- (5*(f_temp -32)/9)
  return(Celcius)
}

F_to_C(80)
```

    ## [1] 26.66667

``` r
for(i in -10:10){           # writing a for loop function in R
  result<- F_to_C (i)
  print(result)
}
```

    ## [1] -23.33333
    ## [1] -22.77778
    ## [1] -22.22222
    ## [1] -21.66667
    ## [1] -21.11111
    ## [1] -20.55556
    ## [1] -20
    ## [1] -19.44444
    ## [1] -18.88889
    ## [1] -18.33333
    ## [1] -17.77778
    ## [1] -17.22222
    ## [1] -16.66667
    ## [1] -16.11111
    ## [1] -15.55556
    ## [1] -15
    ## [1] -14.44444
    ## [1] -13.88889
    ## [1] -13.33333
    ## [1] -12.77778
    ## [1] -12.22222

### syntax to write code and how codes are returned

#### function(argumnet){(code of operation) return(results)}

# Q.No.3 Read in cities.csv file using a relative path

``` r
cities <- read.csv("Cities.csv")
head(cities)
```

    ##          city  city_ascii state_id state_name county_fips county_name     lat
    ## 1    New York    New York       NY   New York       36081      Queens 40.6943
    ## 2 Los Angeles Los Angeles       CA California        6037 Los Angeles 34.1141
    ## 3     Chicago     Chicago       IL   Illinois       17031        Cook 41.8375
    ## 4       Miami       Miami       FL    Florida       12086  Miami-Dade 25.7840
    ## 5     Houston     Houston       TX      Texas       48201      Harris 29.7860
    ## 6      Dallas      Dallas       TX      Texas       48113      Dallas 32.7935
    ##        long population density
    ## 1  -73.9249   18832416 10943.7
    ## 2 -118.4068   11885717  3165.8
    ## 3  -87.6866    8489066  4590.3
    ## 4  -80.2101    6113982  4791.1
    ## 5  -95.3885    6046392  1386.5
    ## 6  -96.7667    5843632  1477.2

# Q.No.4

``` r
haversine_distance <- function(lat1, lon1, lat2, lon2){
 rad.lat1 <- lat1 * pi/180   # converting degrees to radians
 rad.lon1 <- lon1 * pi/180
 rad.lat2<- lat2* pi/180
 rad.lon2<- lon2 * pi/180
 delta_lat<- rad.lat2 - rad.lat1   # Haversine formula
 delta_lon<- rad.lon2 - rad.lon1
  a <- sin(delta_lat / 2)^2 + cos(rad.lat1) * cos(rad.lat2) * sin(delta_lon / 2)^2
c <- 2 * asin(sqrt(a)) 
earth_radius<- 6378137    # Earth radius in kilometer
distance_km<- (earth_radius * c)/1000 # final distance in kilometer
return(distance_km)
}
```

# Q.No.5(a)

``` r
# filter for newyork city and auburn
library(dplyr)
```

    ## 
    ## Attaching package: 'dplyr'

    ## The following objects are masked from 'package:stats':
    ## 
    ##     filter, lag

    ## The following objects are masked from 'package:base':
    ## 
    ##     intersect, setdiff, setequal, union

``` r
nyc<- cities %>% filter(city == "New York") 
auburn<- cities %>% filter(city == "Auburn")

# Extracting just latitude and longitude values
lat1<- nyc$lat
lon1<- nyc$long

lat2<- auburn$lat
lon2<- auburn$long

# Putting the inputs in the function
haversine_distance( lat1, lon1, lat2, lon2)
```

    ## [1] 1367.854

# Q.No.5(b)

``` r
haversine_distance( lat1, lon1, lat2, lon2)
```

    ## [1] 1367.854

# Q.No.6

``` r
# loops to calculate distance from auburn to each city
for(i in 1:nrow(cities)){
  if(cities$city[1] != "Auburn"){
    lat1<- cities$lat[i]
    lon1<- cities$long[i]
    
    dist<- haversine_distance(lat1, lon1, lat2, lon2)
    print(dist)
  }
}
```

    ## [1] 1367.854
    ## [1] 3051.838
    ## [1] 1045.521
    ## [1] 916.4138
    ## [1] 993.0298
    ## [1] 1056.022
    ## [1] 1239.973
    ## [1] 162.5121
    ## [1] 1036.99
    ## [1] 1665.699
    ## [1] 2476.255
    ## [1] 1108.229
    ## [1] 3507.959
    ## [1] 3388.366
    ## [1] 2951.382
    ## [1] 1530.2
    ## [1] 591.1181
    ## [1] 1363.207
    ## [1] 1909.79
    ## [1] 1380.138
    ## [1] 2961.12
    ## [1] 2752.814
    ## [1] 1092.259
    ## [1] 796.7541
    ## [1] 3479.538
    ## [1] 1290.549
    ## [1] 3301.992
    ## [1] 1191.666
    ## [1] 608.2035
    ## [1] 2504.631
    ## [1] 3337.278
    ## [1] 800.1452
    ## [1] 1001.088
    ## [1] 732.5906
    ## [1] 1371.163
    ## [1] 1091.897
    ## [1] 1043.273
    ## [1] 851.3423
    ## [1] 1382.372
    ## [1] 0

# Work for bonus point

``` r
# creating a empty data frame to store results
distance_df<- data.frame(city1 = character(),
                         city2 = character(),
                         distance_km = numeric())
# Loop through each city
for(i in 1:nrow(cities)){
  if(cities$city[i] != "Auburn"){
    lat1<- cities$lat[i]
    lon1<- cities$long[i]
# calculating the distance
    dist<- haversine_distance(lat1, lon1, lat2, lon2)
    
# Adding results as new row
    new_row<- data.frame(city1 = cities$city[i],
                         city2 = "Auburn",
                         distance_km = dist)
    
    distance_df<- rbind(distance_df, new_row)
  }
  
}

# first six rows of the data frame
head(distance_df)
```

    ##         city1  city2 distance_km
    ## 1    New York Auburn   1367.8540
    ## 2 Los Angeles Auburn   3051.8382
    ## 3     Chicago Auburn   1045.5213
    ## 4       Miami Auburn    916.4138
    ## 5     Houston Auburn    993.0298
    ## 6      Dallas Auburn   1056.0217

# Q.No.7 Adding a link to my github

(Github_Link)\[<https://github.com/meriumm/Reproducibility_5820.git>\]
