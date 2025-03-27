---
title: "Data Wrangling"
author: "Maryam Saeed"
date: "2025-03-14"
output:
  md_document:
  html_document: default
  variant: gfm
  pdf_document: default
  word_document: default
---

```{r}
library(tidyverse)
```
```{r}
library(readr)
microbiome.fungi <- read.csv("Bull_richness.csv")
str(microbiome.fungi)
```


####'select()'
```{r}
microbiome.fungi2<- select(microbiome.fungi, SampleID, Crop, Compartment: Fungicide, richness)

```

####'filter()'
```{r}
head(filter(microbiome.fungi2, Treatment =="Conv."))

# A more complex using &
head(filter(microbiome.fungi2, Treatment == "Conv." & Fungicide == "C"))

#Another more complex example using or |
head(filter(microbiome.fungi2, Sample == "A" | Sample == "B")) # Samples A or B
```
#### 'mutate()'

Mutate allow us to quickly create new columns
```{r}
microbiome.fungi2$logRich<- log(microbiome.fungi2$richness)
#Create a new column called logRich
head(mutate(microbiome.fungi2, logRich = log(richness)))

#Create a new Column which combine crop and treatment
head(mutate(microbiome.fungi2, Crop_Treatment = paste(Crop, Treatment)))
```

#### 'pipe()'

we will combine all the previous steps into one large string of functions.
The data from the previous step is transfered to the next step.

```{r}
microbiome.fungi %>%
  select(SampleID, Crop, Compartment: Fungicide, richness) %>% #selecting columns
  filter(Treatment =="Conv.")%>% #sub-setting to only include the conventional treatment
  mutate(logRich = log(richness)) %>% #creating a new column of the log richness
  head() #displaying the first six rows
```
#### 'summarize()'
we can use 'summarise()' function to find things like mean and standard deviations/errors.

```{r}
microbiome.fungi %>%
  select(SampleID, Crop, Compartment: Fungicide, richness) %>% #selecting columns
  filter(Treatment =="Conv.")%>% #sub-setting to only include the conventional treatment
  mutate(logRich = log(richness)) %>% #creating a new column of the log richness
  summarise(Mean.rich = mean(logRich)) #Calculating the overall mean log richness within the conventionally managed treatment
```

we can also connect multiple summary statistics here.

```{r}
microbiome.fungi %>%
  select(SampleID, Crop, Compartment: Fungicide, richness) %>% #selecting columns
  filter(Treatment =="Conv.")%>% #sub-setting to only include the conventional treatment
  mutate(logRich = log(richness)) %>% #creating a new column of the log richness
  summarise(Mean.rich = mean(logRich), 
          n = n(),
          sd.dv = sd(logRich)) %>%
  mutate(std.err = sd.dv/sqrt(n))  #Calculating the mean richness, standard deviation nd standard error
```

In the order to see summart statistics by group! we can do that using 'group_by()' function.
```{r}
microbiome.fungi %>%
  select(SampleID, Crop, Compartment: Fungicide, richness) %>% #selecting columns
  group_by(Treatment, Fungicide)%>% #grouping the treatment and fungicide to later calculate summary stats by the group
  mutate(logRich = log(richness)) %>% #creating a new column of the log richness
  summarise(Mean.rich = mean(logRich), 
          n = n(),
          sd.dv = sd(logRich)) %>%
  mutate(std.err = sd.dv/sqrt(n))  #Calculating the mean richness, standard deviation nd standard error
```

Adding a ggplot

```{r}
microbiome.fungi %>%
  select(SampleID, Crop, Compartment: Fungicide, richness) %>% #selecting columns
  group_by(Treatment, Fungicide)%>% #grouping the treatment and fungicide to later calculate summary stats by the group
  mutate(logRich = log(richness)) %>% #creating a new column of the log richness
  summarise(Mean.rich = mean(logRich), 
          n = n(),
          sd.dv = sd(logRich)) %>%
  mutate(std.err = sd.dv/sqrt(n))  %>%
  ggplot(aes(x= Fungicide, y = Mean.rich))+   #adding a ggplot
  geom_bar(stat = "identity")+
  geom_errorbar(aes(x= Fungicide, ymin = Mean.rich, ymax = Mean.rich+ std.err), width = 0.4)+
  theme_minimal()+
  xlab("")+
  ylab("Log Richness")+
  facet_wrap(~ Treatment)
```

#### Joining

We can do match up of data by rows using joining function

 -left_join()
    - Keep all rows of x and add matching row from y. Any row that dont match x will be excluded.
    
  -right_join
     - reverse of left join()
     
  -inner_join()
     - only keep rows that are common to both X and Y
     
  -fully_join()
     - Keep any columns that are in either X or Y

We have to create a metadata and taxonomy using 'sample()' function

```{r}
#Select just richness and sample ID
richness<- microbiome.fungi %>%
  select(SampleID, richness)

#Selecting columns that dont include the richness
metadata<- microbiome.fungi %>%
  select(SampleID, Fungicide, Crop, Compartment, GrowthStage, Treatment, Rep, Sample)

head(metadata)
head(richness)

head(left_join(metadata, richness, by = "SampleID")) #Adding richness data to metadata on the common column of the sample ID
```
#### Pivoting
It convert wide to long format data and back again. we can do that using 'pivot_longer()'
and 'pivot_wider()'
Our data is wide format we will convert it into long format

```{r}
microbiome.fungi%>%
  select(SampleID,Crop, Compartment:Fungicide, richness) %>%
  group_by(Treatment, Fungicide) %>% #grouping to later calculate summary stats by group
  summarise(Mean = mean(richness)) #Calculate the mean per treatment and fungicide
```

```{r}
microbiome.fungi%>%
  select(SampleID,Crop, Compartment:Fungicide, richness) %>%
  group_by(Treatment, Fungicide) %>% #grouping to later calculate summary stats by group
 summarise(Mean = mean(richness))%>%     #Calculate the mean per treatment and fungicide
   pivot_wider(names_from = Fungicide, values_from = Mean) #pivot to wider format
```
Difference between fungicide and control now

```{r}
microbiome.fungi%>%
 select(SampleID, Crop, Compartment: Fungicide, richness) %>%
  group_by(Treatment, Fungicide) %>% #grouping to later calculate summary stats by group
 summarise(Mean = mean(richness))%>%     #Calculate the mean per treatment and fungicide
   pivot_wider(names_from = Fungicide, values_from = Mean)%>% #pivot to wider format
   mutate(diff.fungicide = C - F) #Calculating the difference between the means
```


---
title: "Coding Challenge 6"
author: "Maryam Saeed Noor Fatima"
date: "2025-03-27"
output:
  word_document: default
  html_document: default
md_document:
  variant: gfm
---

# Q.No.1

### Writing function and iteration helps to do repetative tasks and save alot of time it allow copy pasting and make the script easy to read and use


# Q.No.2
**To write function**

### To write a function we use function() keyword to perform a task. then function() take the input arguments and return the results by applying results().

**To write a loop**

### in the order to make a loop we use for and put i as a iteration in the data and then print i. In this way same code run for each one



```{r}
### How to write a function in R
F_to_C <- function(f_temp){     # Example of writing a function
  Celcius<- (5*(f_temp -32)/9)
  return(Celcius)
}

F_to_C(80)
```

```{r}
for(i in -10:10){           # writing a for loop function in R
  result<- F_to_C (i)
  print(result)
}
```

### syntax to write code and how codes are returned
#### function(argumnet){(code of operation) return(results)}

# Q.No.3 Read in cities.csv file using a relative path

```{r}
cities <- read.csv("Cities.csv")
head(cities)
```
# Q.No.4

```{r}
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

```{r}
# filter for newyork city and auburn
library(dplyr)
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

# Q.No.5(b)

```{r}
haversine_distance( lat1, lon1, lat2, lon2)
```

# Q.No.6

```{r}
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


# Work for bonus point

```{r}
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

# Q.No.7 Adding a link to my github

(Github_Link)[https://github.com/meriumm/Reproducibility_5820.git]

















