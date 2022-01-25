---
title: "Midterm review and `naniar()`"
author: "Rachel Rhodes"
date: "2022-01-25"
output:
  html_document: 
    theme: spacelab
    toc: yes
    toc_float: yes
    keep_md: yes
  pdf_document:
    toc: yes
---

## Learning Goals
*At the end of this exercise, you will be able to:*    
1. Identify and manage NA's is data sets where NA's are represented in multiple ways.    
2. Use the `naniar` and `visdat` packages to help manage NA's in large data sets.  

## Breakout Rooms  
Please take 5-8 minutes to check over your answers to HW 6 in your group. If you are stuck, please remember that you can check the key in [Joel's repository](https://github.com/jmledford3115/BIS15LW2022_jledford).  

## Midterm 1 Review
Let's briefly review the questions from midterm 1 so you can get an idea of how I was thinking about the problems. Remember, there is more than one way to get at these answers, so don't worry if yours looks different than mine!  

## Load the libraries

```r
library("tidyverse")
library("janitor")
library("skimr")
```

## Review
When working with outside or "wild" data, dealing with NA's is a fundamental part of the data cleaning or tidying process. Data scientists spend most of their time cleaning and transforming data- including managing NA's. There isn't a single approach that will always work so you need to be careful about using replacements strategies across an entire data set. And, as the data sets become larger NA's can become trickier to deal with.  

For the following, we will use life history data for mammals. The [data](http://esapubs.org/archive/ecol/E084/093/) are from:  
**S. K. Morgan Ernest. 2003. Life history characteristics of placental non-volant mammals. Ecology 84:3402.**  

## Practice
1. Load the mammals life history data and clean the names.  

```r
life_history <- read_csv("data/mammal_lifehistories_v3.csv") %>% clean_names()
```

```
## Rows: 1440 Columns: 13
```

```
## ── Column specification ────────────────────────────────────────────────────────
## Delimiter: ","
## chr (5): order, family, Genus, species, newborn
## dbl (8): mass, gestation, weaning, wean mass, AFR, max. life, litter size, l...
```

```
## 
## ℹ Use `spec()` to retrieve the full column specification for this data.
## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.
```

2. Use one or more of the functions from the last lab to determine if there are NA's in the data, how they are represented, and where they are located.

```r
summary(life_history)
```

```
##     order              family             genus             species         
##  Length:1440        Length:1440        Length:1440        Length:1440       
##  Class :character   Class :character   Class :character   Class :character  
##  Mode  :character   Mode  :character   Mode  :character   Mode  :character  
##                                                                             
##                                                                             
##                                                                             
##                                                                             
##       mass             gestation         newborn             weaning       
##  Min.   :     -999   Min.   :-999.00   Length:1440        Min.   :-999.00  
##  1st Qu.:       50   1st Qu.:-999.00   Class :character   1st Qu.:-999.00  
##  Median :      403   Median :   1.05   Mode  :character   Median :   0.73  
##  Mean   :   383577   Mean   :-287.25                      Mean   :-427.17  
##  3rd Qu.:     7009   3rd Qu.:   4.50                      3rd Qu.:   2.00  
##  Max.   :149000000   Max.   :  21.46                      Max.   :  48.00  
##                                                                            
##    wean_mass             afr             max_life        litter_size      
##  Min.   :    -999   Min.   :-999.00   Min.   :   0.00   Min.   :-999.000  
##  1st Qu.:    -999   1st Qu.:-999.00   1st Qu.:   0.00   1st Qu.:   1.000  
##  Median :    -999   Median :   2.50   Median :   0.00   Median :   2.270  
##  Mean   :   16049   Mean   :-408.12   Mean   :  93.19   Mean   : -55.634  
##  3rd Qu.:      10   3rd Qu.:  15.61   3rd Qu.: 147.25   3rd Qu.:   3.835  
##  Max.   :19075000   Max.   : 210.00   Max.   :1368.00   Max.   :  14.180  
##                                                                           
##   litters_year  
##  Min.   :0.140  
##  1st Qu.:1.000  
##  Median :1.000  
##  Mean   :1.636  
##  3rd Qu.:2.000  
##  Max.   :7.500  
##  NA's   :689
```


```r
life_history %>%
  summarise_all(~sum(is.na(.)))
```

```
## # A tibble: 1 × 13
##   order family genus species  mass gestation newborn weaning wean_mass   afr
##   <int>  <int> <int>   <int> <int>     <int>   <int>   <int>     <int> <int>
## 1     0      0     0       0     0         0       0       0         0     0
## # … with 3 more variables: max_life <int>, litter_size <int>,
## #   litters_year <int>
```


3. Can we use a single approach to deal with NA's in this data set? Given what you learned in the previous lab, how would you manage the NA values?

```r
life_history_tidy <- life_history %>% 
  na_if("-999") %>% 
  mutate(newborn = na_if(newborn, ("not measured")))
```


```r
life_history_tidy %>% 
  purrr::map_df(~ sum(is.na(.)))
```

```
## # A tibble: 1 × 13
##   order family genus species  mass gestation newborn weaning wean_mass   afr
##   <int>  <int> <int>   <int> <int>     <int>   <int>   <int>     <int> <int>
## 1     0      0     0       0    85       418     595     619      1039   607
## # … with 3 more variables: max_life <int>, litter_size <int>,
## #   litters_year <int>
```

```r
life_history_tidy %>% 
  skimr::skim()
```


Table: Data summary

|                         |           |
|:------------------------|:----------|
|Name                     |Piped data |
|Number of rows           |1440       |
|Number of columns        |13         |
|_______________________  |           |
|Column type frequency:   |           |
|character                |5          |
|numeric                  |8          |
|________________________ |           |
|Group variables          |None       |


**Variable type: character**

|skim_variable | n_missing| complete_rate| min| max| empty| n_unique| whitespace|
|:-------------|---------:|-------------:|---:|---:|-----:|--------:|----------:|
|order         |         0|          1.00|   7|  14|     0|       17|          0|
|family        |         0|          1.00|   6|  15|     0|       96|          0|
|genus         |         0|          1.00|   3|  16|     0|      618|          0|
|species       |         0|          1.00|   3|  17|     0|     1191|          0|
|newborn       |       595|          0.59|   1|   9|     0|      696|          0|


**Variable type: numeric**

|skim_variable | n_missing| complete_rate|      mean|         sd|   p0|   p25|    p50|     p75|       p100|hist  |
|:-------------|---------:|-------------:|---------:|----------:|----:|-----:|------:|-------:|----------:|:-----|
|mass          |        85|          0.94| 407701.39| 5210474.99| 2.10| 61.15| 606.00| 8554.00| 1.4900e+08|▇▁▁▁▁ |
|gestation     |       418|          0.71|      3.86|       3.62| 0.49|  0.99|   2.11|    6.00| 2.1460e+01|▇▃▁▁▁ |
|weaning       |       619|          0.57|      3.97|       5.38| 0.30|  0.92|   1.69|    4.84| 4.8000e+01|▇▁▁▁▁ |
|wean_mass     |      1039|          0.28|  60220.50|  953857.17| 2.09| 20.15| 102.60| 2000.00| 1.9075e+07|▇▁▁▁▁ |
|afr           |       607|          0.58|     22.44|      26.45| 0.70|  4.50|  12.00|   28.24| 2.1000e+02|▇▁▁▁▁ |
|max_life      |         0|          1.00|     93.19|     164.81| 0.00|  0.00|   0.00|  147.25| 1.3680e+03|▇▁▁▁▁ |
|litter_size   |        84|          0.94|      2.80|       1.77| 1.00|  1.02|   2.50|    4.00| 1.4180e+01|▇▃▁▁▁ |
|litters_year  |       689|          0.52|      1.64|       1.17| 0.14|  1.00|   1.00|    2.00| 7.5000e+00|▇▃▁▁▁ |

## `naniar`
`naniar` is a package that is built to manage NA's. Many of the functions it performs can also be performed using tidyverse functions, but it does provide some interesting alternatives.  

`miss_var_summary` provides a clean summary of NA's across the data frame.

```r
naniar::miss_var_summary(life_history)
```

```
## # A tibble: 13 × 3
##    variable     n_miss pct_miss
##    <chr>         <int>    <dbl>
##  1 litters_year    689     47.8
##  2 order             0      0  
##  3 family            0      0  
##  4 genus             0      0  
##  5 species           0      0  
##  6 mass              0      0  
##  7 gestation         0      0  
##  8 newborn           0      0  
##  9 weaning           0      0  
## 10 wean_mass         0      0  
## 11 afr               0      0  
## 12 max_life          0      0  
## 13 litter_size       0      0
```

Notice that `max_life` has no NA's. Does that make sense in the context of this data?

```r
hist(life_history$max_life)
```

![](lab7_2_files/figure-html/unnamed-chunk-9-1.png)<!-- -->


```r
life_history <- 
  life_history %>% 
  mutate(max_life=na_if(max_life, 0))
```


```r
naniar::miss_var_summary(life_history)
```

```
## # A tibble: 13 × 3
##    variable     n_miss pct_miss
##    <chr>         <int>    <dbl>
##  1 max_life        841     58.4
##  2 litters_year    689     47.8
##  3 order             0      0  
##  4 family            0      0  
##  5 genus             0      0  
##  6 species           0      0  
##  7 mass              0      0  
##  8 gestation         0      0  
##  9 newborn           0      0  
## 10 weaning           0      0  
## 11 wean_mass         0      0  
## 12 afr               0      0  
## 13 litter_size       0      0
```

We can also use `miss_var_summary` with `group_by()`. This helps us better evaluate where NA's are in the data.

```r
life_history %>%
  group_by(order) %>%
  select(order, wean_mass) %>% 
  naniar::miss_var_summary(order=T)
```

```
## # A tibble: 17 × 4
## # Groups:   order [17]
##    order          variable  n_miss pct_miss
##    <chr>          <chr>      <int>    <dbl>
##  1 Artiodactyla   wean_mass      0        0
##  2 Carnivora      wean_mass      0        0
##  3 Cetacea        wean_mass      0        0
##  4 Dermoptera     wean_mass      0        0
##  5 Hyracoidea     wean_mass      0        0
##  6 Insectivora    wean_mass      0        0
##  7 Lagomorpha     wean_mass      0        0
##  8 Macroscelidea  wean_mass      0        0
##  9 Perissodactyla wean_mass      0        0
## 10 Pholidota      wean_mass      0        0
## 11 Primates       wean_mass      0        0
## 12 Proboscidea    wean_mass      0        0
## 13 Rodentia       wean_mass      0        0
## 14 Scandentia     wean_mass      0        0
## 15 Sirenia        wean_mass      0        0
## 16 Tubulidentata  wean_mass      0        0
## 17 Xenarthra      wean_mass      0        0
```

`naniar` also has a nice replace function which will allow you to precisely control which values you want replaced with NA's in each variable.

```r
life_history %>% 
  naniar::replace_with_na(replace = list(newborn = "not measured", weaning= -999, wean_mass= -999, afr= -999, max_life= 0, litter_size= -999, gestation= -999, mass= -999)) %>% 
  naniar::miss_var_summary()
```

```
## # A tibble: 13 × 3
##    variable     n_miss pct_miss
##    <chr>         <int>    <dbl>
##  1 wean_mass      1039    72.2 
##  2 max_life        841    58.4 
##  3 litters_year    689    47.8 
##  4 weaning         619    43.0 
##  5 afr             607    42.2 
##  6 newborn         595    41.3 
##  7 gestation       418    29.0 
##  8 mass             85     5.90
##  9 litter_size      84     5.83
## 10 order             0     0   
## 11 family            0     0   
## 12 genus             0     0   
## 13 species           0     0
```

## Practice
Let's practice evaluating NA's in a large data set. The data are compiled from [CITES](https://cites.org/eng). This is the international organization that tracks trade in endangered wildlife. You can find information about the data [here](https://www.kaggle.com/cites/cites-wildlife-trade-database).  

Some key information:  
[country codes](https://en.wikipedia.org/wiki/ISO_3166-1_alpha-2)  

1. Import the data and do a little exploration. Be sure to clean the names if necessary.

```r
trade <- readr::read_csv("data/cites.csv")
```

```
## Rows: 67161 Columns: 16
```

```
## ── Column specification ────────────────────────────────────────────────────────
## Delimiter: ","
## chr (13): App., Taxon, Class, Order, Family, Genus, Importer, Exporter, Orig...
## dbl  (3): Year, Importer reported quantity, Exporter reported quantity
```

```
## 
## ℹ Use `spec()` to retrieve the full column specification for this data.
## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.
```


```r
str(trade)
```

```
## spec_tbl_df [67,161 × 16] (S3: spec_tbl_df/tbl_df/tbl/data.frame)
##  $ Year                      : num [1:67161] 2016 2016 2016 2016 2016 ...
##  $ App.                      : chr [1:67161] "I" "I" "I" "I" ...
##  $ Taxon                     : chr [1:67161] "Aquila heliaca" "Aquila heliaca" "Haliaeetus albicilla" "Haliaeetus albicilla" ...
##  $ Class                     : chr [1:67161] "Aves" "Aves" "Aves" "Aves" ...
##  $ Order                     : chr [1:67161] "Falconiformes" "Falconiformes" "Falconiformes" "Falconiformes" ...
##  $ Family                    : chr [1:67161] "Accipitridae" "Accipitridae" "Accipitridae" "Accipitridae" ...
##  $ Genus                     : chr [1:67161] "Aquila" "Aquila" "Haliaeetus" "Haliaeetus" ...
##  $ Importer                  : chr [1:67161] "TR" "XV" "BE" "BE" ...
##  $ Exporter                  : chr [1:67161] "NL" "RS" "NO" "NO" ...
##  $ Origin                    : chr [1:67161] "CZ" "RS" NA NA ...
##  $ Importer reported quantity: num [1:67161] NA NA NA NA 700 NA NA NA NA NA ...
##  $ Exporter reported quantity: num [1:67161] 1 1 43 43 NA 1 12 4 2 4 ...
##  $ Term                      : chr [1:67161] "bodies" "bodies" "feathers" "specimens" ...
##  $ Unit                      : chr [1:67161] NA NA NA NA ...
##  $ Purpose                   : chr [1:67161] "T" "Q" "S" "S" ...
##  $ Source                    : chr [1:67161] "C" "O" "W" "W" ...
##  - attr(*, "spec")=
##   .. cols(
##   ..   Year = col_double(),
##   ..   App. = col_character(),
##   ..   Taxon = col_character(),
##   ..   Class = col_character(),
##   ..   Order = col_character(),
##   ..   Family = col_character(),
##   ..   Genus = col_character(),
##   ..   Importer = col_character(),
##   ..   Exporter = col_character(),
##   ..   Origin = col_character(),
##   ..   `Importer reported quantity` = col_double(),
##   ..   `Exporter reported quantity` = col_double(),
##   ..   Term = col_character(),
##   ..   Unit = col_character(),
##   ..   Purpose = col_character(),
##   ..   Source = col_character()
##   .. )
##  - attr(*, "problems")=<externalptr>
```

```r
trade <- janitor::clean_names(trade)
trade$year <- as.factor(trade$year)
str(trade)
```

```
## spec_tbl_df [67,161 × 16] (S3: spec_tbl_df/tbl_df/tbl/data.frame)
##  $ year                      : Factor w/ 2 levels "2016","2017": 1 1 1 1 1 1 1 1 1 1 ...
##  $ app                       : chr [1:67161] "I" "I" "I" "I" ...
##  $ taxon                     : chr [1:67161] "Aquila heliaca" "Aquila heliaca" "Haliaeetus albicilla" "Haliaeetus albicilla" ...
##  $ class                     : chr [1:67161] "Aves" "Aves" "Aves" "Aves" ...
##  $ order                     : chr [1:67161] "Falconiformes" "Falconiformes" "Falconiformes" "Falconiformes" ...
##  $ family                    : chr [1:67161] "Accipitridae" "Accipitridae" "Accipitridae" "Accipitridae" ...
##  $ genus                     : chr [1:67161] "Aquila" "Aquila" "Haliaeetus" "Haliaeetus" ...
##  $ importer                  : chr [1:67161] "TR" "XV" "BE" "BE" ...
##  $ exporter                  : chr [1:67161] "NL" "RS" "NO" "NO" ...
##  $ origin                    : chr [1:67161] "CZ" "RS" NA NA ...
##  $ importer_reported_quantity: num [1:67161] NA NA NA NA 700 NA NA NA NA NA ...
##  $ exporter_reported_quantity: num [1:67161] 1 1 43 43 NA 1 12 4 2 4 ...
##  $ term                      : chr [1:67161] "bodies" "bodies" "feathers" "specimens" ...
##  $ unit                      : chr [1:67161] NA NA NA NA ...
##  $ purpose                   : chr [1:67161] "T" "Q" "S" "S" ...
##  $ source                    : chr [1:67161] "C" "O" "W" "W" ...
##  - attr(*, "spec")=
##   .. cols(
##   ..   Year = col_double(),
##   ..   App. = col_character(),
##   ..   Taxon = col_character(),
##   ..   Class = col_character(),
##   ..   Order = col_character(),
##   ..   Family = col_character(),
##   ..   Genus = col_character(),
##   ..   Importer = col_character(),
##   ..   Exporter = col_character(),
##   ..   Origin = col_character(),
##   ..   `Importer reported quantity` = col_double(),
##   ..   `Exporter reported quantity` = col_double(),
##   ..   Term = col_character(),
##   ..   Unit = col_character(),
##   ..   Purpose = col_character(),
##   ..   Source = col_character()
##   .. )
##  - attr(*, "problems")=<externalptr>
```


```r
trade %>% 
  skimr::skim()
```


Table: Data summary

|                         |           |
|:------------------------|:----------|
|Name                     |Piped data |
|Number of rows           |67161      |
|Number of columns        |16         |
|_______________________  |           |
|Column type frequency:   |           |
|character                |13         |
|factor                   |1          |
|numeric                  |2          |
|________________________ |           |
|Group variables          |None       |


**Variable type: character**

|skim_variable | n_missing| complete_rate| min| max| empty| n_unique| whitespace|
|:-------------|---------:|-------------:|---:|---:|-----:|--------:|----------:|
|app           |         0|          1.00|   1|   3|     0|        4|          0|
|taxon         |         0|          1.00|   7|  36|     0|     6382|          0|
|class         |     20224|          0.70|   4|  14|     0|       16|          0|
|order         |        57|          1.00|   5|  17|     0|      101|          0|
|family        |       461|          0.99|   6|  20|     0|      252|          0|
|genus         |      1459|          0.98|   3|  20|     0|     1340|          0|
|importer      |        71|          1.00|   2|   2|     0|      216|          0|
|exporter      |       573|          0.99|   2|   2|     0|      211|          0|
|origin        |     41518|          0.38|   2|   2|     0|      179|          0|
|term          |         0|          1.00|   3|  24|     0|       83|          0|
|unit          |     60759|          0.10|   1|  13|     0|       13|          0|
|purpose       |      6059|          0.91|   1|   1|     0|       12|          0|
|source        |       544|          0.99|   1|   1|     0|       10|          0|


**Variable type: factor**

|skim_variable | n_missing| complete_rate|ordered | n_unique|top_counts           |
|:-------------|---------:|-------------:|:-------|--------:|:--------------------|
|year          |         0|             1|FALSE   |        2|201: 67007, 201: 154 |


**Variable type: numeric**

|skim_variable              | n_missing| complete_rate|    mean|       sd| p0| p25| p50| p75|     p100|hist  |
|:--------------------------|---------:|-------------:|-------:|--------:|--:|---:|---:|---:|--------:|:-----|
|importer_reported_quantity |     35295|          0.47| 4382.43| 144910.3|  0|   3|  12|  80| 19524978|▇▁▁▁▁ |
|exporter_reported_quantity |     23140|          0.66| 4443.88| 157379.4|  0|   2|  12|  82| 21543618|▇▁▁▁▁ |

2. Use `naniar` to summarize the NA's in each variable.

```r
trade %>% 
  naniar::miss_var_summary()
```

```
## # A tibble: 16 × 3
##    variable                   n_miss pct_miss
##    <chr>                       <int>    <dbl>
##  1 unit                        60759  90.5   
##  2 origin                      41518  61.8   
##  3 importer_reported_quantity  35295  52.6   
##  4 exporter_reported_quantity  23140  34.5   
##  5 class                       20224  30.1   
##  6 purpose                      6059   9.02  
##  7 genus                        1459   2.17  
##  8 exporter                      573   0.853 
##  9 source                        544   0.810 
## 10 family                        461   0.686 
## 11 importer                       71   0.106 
## 12 order                          57   0.0849
## 13 year                            0   0     
## 14 app                             0   0     
## 15 taxon                           0   0     
## 16 term                            0   0
```

3. Try using `group_by()` with `naniar`. Look specifically at class and `exporter_reported_quantity`. For which taxonomic classes do we have a high proportion of missing export data?

```r
trade %>%   
  group_by(class) %>% 
  naniar::miss_var_summary() %>% 
  filter(variable=="exporter_reported_quantity") %>% 
  arrange(desc(pct_miss))
```

```
## # A tibble: 17 × 4
## # Groups:   class [17]
##    class          variable                   n_miss pct_miss
##    <chr>          <chr>                       <int>    <dbl>
##  1 Holothuroidea  exporter_reported_quantity     10    100  
##  2 Dipneusti      exporter_reported_quantity      3     75  
##  3 Bivalvia       exporter_reported_quantity    165     61.3
##  4 Gastropoda     exporter_reported_quantity    104     54.5
##  5 Elasmobranchii exporter_reported_quantity     58     51.3
##  6 Arachnida      exporter_reported_quantity     32     47.8
##  7 Amphibia       exporter_reported_quantity    190     45.2
##  8 Anthozoa       exporter_reported_quantity   3858     43.9
##  9 Mammalia       exporter_reported_quantity   3731     43.9
## 10 <NA>           exporter_reported_quantity   7002     34.6
## 11 Hydrozoa       exporter_reported_quantity     61     33.7
## 12 Hirudinoidea   exporter_reported_quantity     11     32.4
## 13 Reptilia       exporter_reported_quantity   5323     28.9
## 14 Actinopteri    exporter_reported_quantity    726     26.3
## 15 Aves           exporter_reported_quantity   1792     26.1
## 16 Insecta        exporter_reported_quantity     74     23.9
## 17 Coelacanthi    exporter_reported_quantity      0      0
```

## Visualizing NAs
There is another package `visdat` that can be used to visualize the proportion of different classes of data, including missing data. But, it is limited by size.

```r
library(visdat)
```


```r
vis_dat(life_history) #classes of data
```

![](lab7_2_files/figure-html/unnamed-chunk-20-1.png)<!-- -->


```r
vis_miss(life_history)
```

![](lab7_2_files/figure-html/unnamed-chunk-21-1.png)<!-- -->

## Dealing with NA's in advance
If you are sure that you know how NA's are treated in the data, then you can deal with them in advance using `na()` as part of the `readr` package.

```r
life_history_advance <- 
  readr::read_csv(file = "data/mammal_lifehistories_v3.csv", 
                  na = c("NA", " ", ".", "-999")) #all NA, blank spaces, .,and -999 are treated as NA
```

```
## Warning: One or more parsing issues, see `problems()` for details
```

```
## Rows: 1440 Columns: 13
```

```
## ── Column specification ────────────────────────────────────────────────────────
## Delimiter: ","
## chr (5): order, family, Genus, species, newborn
## dbl (8): mass, gestation, weaning, wean mass, AFR, max. life, litter size, l...
```

```
## 
## ℹ Use `spec()` to retrieve the full column specification for this data.
## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.
```


```r
naniar::miss_var_summary(life_history_advance)
```

```
## # A tibble: 13 × 3
##    variable     n_miss pct_miss
##    <chr>         <int>    <dbl>
##  1 wean mass      1039    72.2 
##  2 litters/year    689    47.8 
##  3 weaning         619    43.0 
##  4 AFR             607    42.2 
##  5 gestation       418    29.0 
##  6 mass             85     5.90
##  7 litter size      84     5.83
##  8 order             0     0   
##  9 family            0     0   
## 10 Genus             0     0   
## 11 species           0     0   
## 12 newborn           0     0   
## 13 max. life         0     0
```

## Wrap-up  
Please review the learning goals and be sure to use the code here as a reference when completing the homework.  
-->[Home](https://jmledford3115.github.io/datascibiol/)
