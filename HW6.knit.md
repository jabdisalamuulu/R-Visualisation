---
title: "R Visualization"
output:
  pdf_document: default
  word_document: default
date: "2023-10-01"
---


```r
setwd ("/Users/jumaabdi/Desktop/G_P/Spring23/RDS/Homework/HW6")
library(markdown)
library(ggplot2) 
library(tidyverse)
```

```
## -- Attaching core tidyverse packages ------------------------ tidyverse 2.0.0 --
## v dplyr     1.1.0     v readr     2.1.4
## v forcats   1.0.0     v stringr   1.5.0
## v lubridate 1.9.2     v tibble    3.1.8
## v purrr     1.0.1     v tidyr     1.3.0
## -- Conflicts ------------------------------------------ tidyverse_conflicts() --
## x dplyr::filter() masks stats::filter()
## x dplyr::lag()    masks stats::lag()
## i Use the ]8;;http://conflicted.r-lib.org/conflicted package]8;; to force all conflicts to become errors
```

```r
library(maps)
```

```
## 
## Attaching package: 'maps'
## 
## The following object is masked from 'package:purrr':
## 
##     map
```

```r
library(usmap)
library(htmltab)
library(ggmap)
```

```
## i Google's Terms of Service: <]8;;https://mapsplatform.google.comhttps://mapsplatform.google.com]8;;>
## i Please cite ggmap if you use it! Use `citation("ggmap")` for details.
```

```r
library(jsonlite)
```

```
## 
## Attaching package: 'jsonlite'
## 
## The following object is masked from 'package:purrr':
## 
##     flatten
```

## R Markdown


```r
nlsy_data <- read.table(file = "/Users/jumaabdi/Desktop/G_P/Spring23/RDS/Homework/HW6/nlsy.txt", header = TRUE, na.string = ".")
verbal <- nlsy_data$V3
math <- nlsy_data$V4
sex <-nlsy_data$sex
```




```r
ggplot(data = nlsy_data, aes(x = math)) +
  geom_histogram(bins = 30, aes(y = after_stat(density))) + 
  geom_density(linewidth=1.5, color='red')
```

```
## Warning: Removed 95 rows containing non-finite values (`stat_bin()`).
```

```
## Warning: Removed 95 rows containing non-finite values (`stat_density()`).
```

![](HW6_files/figure-latex/histogram generation for math and verbal-1.pdf)<!-- --> 

```r
ggplot(data = nlsy_data, aes(x = verbal)) +
  geom_histogram(bins = 30, aes(y = after_stat(density))) + 
  geom_density(linewidth=1.5, color='red')
```

```
## Warning: Removed 36 rows containing non-finite values (`stat_bin()`).
```

```
## Warning: Removed 36 rows containing non-finite values (`stat_density()`).
```

![](HW6_files/figure-latex/histogram generation for math and verbal-2.pdf)<!-- --> 

```r
ggplot(data = nlsy_data) + 
  geom_jitter(aes(math, verbal, color = math))
```

```
## Warning: Removed 117 rows containing missing values (`geom_point()`).
```

![](HW6_files/figure-latex/generating scatter plot-1.pdf)<!-- --> 

```r
#adding a regression line

ggplot(data = nlsy_data, aes(math, verbal, color = math)) +
  geom_jitter()+
  geom_smooth(method = lm, formula = y ~ x, se=F)+
  labs(title = "Correlation between math and verbal scores",
       x = "Math Score", y = "Verbal Score")
```

```
## Warning: Removed 117 rows containing non-finite values (`stat_smooth()`).
```

```
## Warning: The following aesthetics were dropped during statistical transformation: colour
## i This can happen when ggplot fails to infer the correct grouping structure in
##   the data.
## i Did you forget to specify a `group` aesthetic or to convert a numerical
##   variable into a factor?
```

```
## Warning: Removed 117 rows containing missing values (`geom_point()`).
```

![](HW6_files/figure-latex/generating scatter plot-2.pdf)<!-- --> 



```r
nlsy_data$sex2 <- factor(nlsy_data$sex, 
                      levels=1:2, 
                      labels=c('Male', 'Female'))
ggplot(data = nlsy_data, aes(math, verbal, color = sex2)) + 
  geom_jitter()+
  geom_smooth(method=lm, formula=y~x, se=FALSE) + 
  scale_color_discrete(name="By Sex")
```

```
## Warning: Removed 117 rows containing non-finite values (`stat_smooth()`).
```

```
## Warning: Removed 117 rows containing missing values (`geom_point()`).
```

![](HW6_files/figure-latex/comparing male and female participants-1.pdf)<!-- --> 

```r
# Parsing data from the webpage
population <- htmltab(doc="https://www.ipl.org/div/stateknow/popchart.html", which = 2)

# Renaming columns
colnames(population)[3] <- "population"
colnames(population)[2] <- "region"

# Converting string data to integer
population$population <- as.integer(gsub(",", "", population$population))

# Convert region names to lowercase for consistency
population$region <- tolower(population$region)

# Get state data in the form of latitude and longitude coordinates
usa_map <- map_data('state')

# Merging the population and state data
us_map_pop <- merge(usa_map, population, by.x = "region", by.y = "region", all.x = TRUE)

# Creating map by plotting the data
map_graph <- ggplot(data = us_map_pop) +
  geom_polygon(aes(x = long, y = lat, group = group, fill = population), 
               color = "black") +
  scale_fill_gradient(low = "white", high = "purple", na.value = "gray", trans = 'log10') +
  labs(title = "Population by State", fill = "Population") +
  coord_fixed(1.3)
print(map_graph)
```

![](HW6_files/figure-latex/mapping population data-1.pdf)<!-- --> 

