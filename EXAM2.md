---
title: "EXAM_2_NOTES"
author: "Ketong Zhang"
date: "2024-02-26"
output: 
  html_document: 
    keep_md: true
---


LAB 8
## Load the libraries

```r
library("tidyverse")
library("skimr")
library("janitor")
library("palmerpenguins")
```

## Midterm 1 Review
Let's briefly review the questions from midterm 1 so you can get an idea of how I was thinking about the problems. Remember, there is more than one way to get at these answers, so don't worry if yours looks different than mine!  

## Penguins
We rushed through the second part of lab 7 last week, so let's do a quick review of some key functions. As you know, summarize() and group_by() are powerful tools that we can use to produce clean summaries of data. Especially when used together, we can quickly group variables of interest and save time. Let's do some practice with the [palmerpenguins(https://allisonhorst.github.io/palmerpenguins/articles/intro.html) data to refresh our memory.


```r
?penguins
```

Recall that group_by() and summarize() work great together. Let's say we were interested in how body mass varied by island. It is reasonable to assume that the islands are different, so maybe the penguins are as well.

```r
penguins %>% 
  group_by(island) %>% 
  summarize(mean_body_mass_g=mean(body_mass_g, na.rm=T)) # remember to remove those NA's!
```

```
## # A tibble: 3 × 2
##   island    mean_body_mass_g
##   <fct>                <dbl>
## 1 Biscoe               4716.
## 2 Dream                3713.
## 3 Torgersen            3706.
```

What if we are interested in the number of observations (penguins) by species and island?

```r
penguins %>% 
  group_by(island, species) %>% 
  summarize(n_penguins=n(), .groups = 'keep')
```

```
## # A tibble: 5 × 3
## # Groups:   island, species [5]
##   island    species   n_penguins
##   <fct>     <fct>          <int>
## 1 Biscoe    Adelie            44
## 2 Biscoe    Gentoo           124
## 3 Dream     Adelie            56
## 4 Dream     Chinstrap         68
## 5 Torgersen Adelie            52
```

Recall that that `count()` works like a combination of `group_by()` and `summarize()` but just shows the number of observations.

```r
penguins %>% 
  count(island, species)
```

```
## # A tibble: 5 × 3
##   island    species       n
##   <fct>     <fct>     <int>
## 1 Biscoe    Adelie       44
## 2 Biscoe    Gentoo      124
## 3 Dream     Adelie       56
## 4 Dream     Chinstrap    68
## 5 Torgersen Adelie       52
```

`tabyl()` will also produce counts, but the output is different. It is just a matter of personal preference.

```r
penguins %>% 
  tabyl(island, species)
```

```
##     island Adelie Chinstrap Gentoo
##     Biscoe     44         0    124
##      Dream     56        68      0
##  Torgersen     52         0      0
```

## Practice
1. Produce a summary of the mean for bill_length_mm, bill_depth_mm, flipper_length_mm, and body_mass_g within Adelie penguins only. Be sure to provide the number of samples.

```r
penguins %>% 
  filter(species=="Adelie") %>% 
  select(bill_length_mm, bill_depth_mm, flipper_length_mm, body_mass_g) %>% 
  summarize(mean_bill_length_mm=mean(bill_length_mm, na.rm=T),
            mean_bill_depth_mm=mean(bill_depth_mm, na.rm=T),
            mean_flipper_length_mm=mean(flipper_length_mm, na.rm=T),
            mean_body_mass_g=mean(body_mass_g, na.rm=T),
            n_samples=n())
```

```
## # A tibble: 1 × 5
##   mean_bill_length_mm mean_bill_depth_mm mean_flipper_length_mm mean_body_mass_g
##                 <dbl>              <dbl>                  <dbl>            <dbl>
## 1                38.8               18.3                   190.            3701.
## # ℹ 1 more variable: n_samples <int>
```

2. How does the mean of `bill_length_mm` compare between penguin species?

```r
penguins %>% 
  group_by(species) %>% # remember to group by species
  summarize(mean_bill_length_mm=mean(bill_length_mm, na.rm=T))
```

```
## # A tibble: 3 × 2
##   species   mean_bill_length_mm
##   <fct>                   <dbl>
## 1 Adelie                   38.8
## 2 Chinstrap                48.8
## 3 Gentoo                   47.5
```


3. For some penguins, their sex is listed as NA. Where do these penguins occur?

```r
penguins %>% 
  count(sex, island)
```

```
## # A tibble: 9 × 3
##   sex    island        n
##   <fct>  <fct>     <int>
## 1 female Biscoe       80
## 2 female Dream        61
## 3 female Torgersen    24
## 4 male   Biscoe       83
## 5 male   Dream        62
## 6 male   Torgersen    23
## 7 <NA>   Biscoe        5
## 8 <NA>   Dream         1
## 9 <NA>   Torgersen     5
```

## `across()`
How do we use `filter()` and `select()` across multiple variables? There is a function in dplyr called `across()` which is designed to help make this efficient.  

Here we are summarizing the mean of all variables that contain "mm".

```r
penguins %>%
  summarize(across(contains("mm"), mean, na.rm=T))
```

```
## Warning: There was 1 warning in `summarize()`.
## ℹ In argument: `across(contains("mm"), mean, na.rm = T)`.
## Caused by warning:
## ! The `...` argument of `across()` is deprecated as of dplyr 1.1.0.
## Supply arguments directly to `.fns` through an anonymous function instead.
## 
##   # Previously
##   across(a:b, mean, na.rm = TRUE)
## 
##   # Now
##   across(a:b, \(x) mean(x, na.rm = TRUE))
```

```
## # A tibble: 1 × 3
##   bill_length_mm bill_depth_mm flipper_length_mm
##            <dbl>         <dbl>             <dbl>
## 1           43.9          17.2              201.
```

`group_by` also works.

```r
penguins %>%
  group_by(sex) %>% 
  summarize(across(contains("mm"), mean, na.rm=T))
```

```
## # A tibble: 3 × 4
##   sex    bill_length_mm bill_depth_mm flipper_length_mm
##   <fct>           <dbl>         <dbl>             <dbl>
## 1 female           42.1          16.4              197.
## 2 male             45.9          17.9              205.
## 3 <NA>             41.3          16.6              199
```

Here we summarize across all variables.

```r
penguins %>%
  summarise_all(n_distinct)
```

```
## # A tibble: 1 × 8
##   species island bill_length_mm bill_depth_mm flipper_length_mm body_mass_g
##     <int>  <int>          <int>         <int>             <int>       <int>
## 1       3      3            165            81                56          95
## # ℹ 2 more variables: sex <int>, year <int>
```

Operators can also work, here I am summarizing `n_distinct()` across all variables except `species`, `island`, and `sex`.

```r
penguins %>%
  summarize(across(!c(species, island, sex, year), 
                   mean, na.rm=T))
```

```
## # A tibble: 1 × 4
##   bill_length_mm bill_depth_mm flipper_length_mm body_mass_g
##            <dbl>         <dbl>             <dbl>       <dbl>
## 1           43.9          17.2              201.       4202.
```

All variables that include "bill"...all of the other dplyr operators also work.

```r
penguins %>%
  summarise(across(starts_with("bill"), n_distinct))
```

```
## # A tibble: 1 × 2
##   bill_length_mm bill_depth_mm
##            <int>         <int>
## 1            165            81
```

## Practice
1. Produce separate summaries of the mean and standard deviation for bill_length_mm, bill_depth_mm, and flipper_length_mm for each penguin species. Be sure to provide the number of samples.  

```r
penguins %>% 
  group_by(species) %>% 
  summarize(across(c(contains("mm"), body_mass_g), mean, na.rm=T ),
            n_samples=n())
```

```
## # A tibble: 3 × 6
##   species   bill_length_mm bill_depth_mm flipper_length_mm body_mass_g n_samples
##   <fct>              <dbl>         <dbl>             <dbl>       <dbl>     <int>
## 1 Adelie              38.8          18.3              190.       3701.       152
## 2 Chinstrap           48.8          18.4              196.       3733.        68
## 3 Gentoo              47.5          15.0              217.       5076.       124
```

## Load the libraries

```r
library("tidyverse")
library("naniar")
library("skimr")
library("janitor")
```

## Review
When working with "wild" data, dealing with NA's is a fundamental part of the data cleaning process. Data scientists spend most of their time cleaning and transforming data- including managing NA's. There isn't a single approach that will always work so you need to be careful about using replacement strategies across an entire data set. And, as the data sets become larger NA's can become trickier to deal with.  

For the following, we will use life history data for mammals. The [data](http://esapubs.org/archive/ecol/E084/093/) are from:  
**S. K. Morgan Ernest. 2003. Life history characteristics of placental non-volant mammals. Ecology 84:3402.**  

## Load the mammals life history data and clean the names  

```r
life_history <- read_csv("data/mammal_lifehistories_v3.csv") %>% clean_names()
```

## Are there any NA's?
Sometimes using one or more of the summary functions can give us clues to how the authors have represented missing data. This doesn't always work, but it is a good place to start.

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

This will give you a quick summary of the number of NA's in each variable. Notice that, at least for now, it doesn't look like there are any NA's. But, given the size of the data this is unlikely.

```r
life_history %>% map_df(~ sum(is.na(.)))
```

```
## # A tibble: 1 × 13
##   order family genus species  mass gestation newborn weaning wean_mass   afr
##   <int>  <int> <int>   <int> <int>     <int>   <int>   <int>     <int> <int>
## 1     0      0     0       0     0         0       0       0         0     0
## # ℹ 3 more variables: max_life <int>, litter_size <int>, litters_year <int>
```

## Practice
1. Can we use a single approach to deal with NA's in this data set? Given what you learned in the previous lab, how would you manage the NA values?

```r
life_history_no_nas <- 
  read_csv(file = "data/mammal_lifehistories_v3.csv", na = c("NA", " ", ".", "-999")) %>% clean_names() # notice that I am creating a new object/ dataframe that doesn't have any NA's
```

```
## Warning: One or more parsing issues, call `problems()` on your data frame for details,
## e.g.:
##   dat <- vroom(...)
##   problems(dat)
```

```
## Rows: 1440 Columns: 13
## ── Column specification ────────────────────────────────────────────────────────
## Delimiter: ","
## chr (5): order, family, Genus, species, newborn
## dbl (8): mass, gestation, weaning, wean mass, AFR, max. life, litter size, l...
## 
## ℹ Use `spec()` to retrieve the full column specification for this data.
## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.
```

Recheck

```r
life_history_no_nas %>% map_df(~ sum(is.na(.))) # we fixed most of the problems
```

```
## # A tibble: 1 × 13
##   order family genus species  mass gestation newborn weaning wean_mass   afr
##   <int>  <int> <int>   <int> <int>     <int>   <int>   <int>     <int> <int>
## 1     0      0     0       0    85       418       0     619      1039   607
## # ℹ 3 more variables: max_life <int>, litter_size <int>, litters_year <int>
```

## `naniar`
[naniar](https://naniar.njtierney.com/articles/replace-with-na.html#:~:text=replace_with_na_all()%20Replaces%20NA%20for,numeric%2C%20is.) is a package that is built to manage NA's. Many of the functions it performs can also be performed using tidyverse functions, but it does provide some interesting alternatives.  

`miss_var_summary` provides a summary of NA's across the data frame.

```r
miss_var_summary(life_history_no_nas)
```

```
## # A tibble: 13 × 3
##    variable     n_miss pct_miss
##    <chr>         <int>    <dbl>
##  1 wean_mass      1039    72.2 
##  2 litters_year    689    47.8 
##  3 weaning         619    43.0 
##  4 afr             607    42.2 
##  5 gestation       418    29.0 
##  6 mass             85     5.90
##  7 litter_size      84     5.83
##  8 order             0     0   
##  9 family            0     0   
## 10 genus             0     0   
## 11 species           0     0   
## 12 newborn           0     0   
## 13 max_life          0     0
```

Notice that `max_life` has no NA's. Does that make sense in the biological context of this data? Why would an animal have 0 life span?

```r
hist(life_history_no_nas$max_life) #we found another way that NA's are represented
```

![](EXAM2_files/figure-html/unnamed-chunk-23-1.png)<!-- -->

Let's use `mutate()` and use `na_if()` to replace 0's with NA's in `max_life`. This chunk allows us to address problems in a single variable.

```r
life_history_no_nas <- life_history_no_nas %>% 
  mutate(max_life=na_if(max_life, 0))
```

We can also use `miss_var_summary` with `group_by()`. This helps us better evaluate where NA's are in the data.

```r
life_history_no_nas %>%
  group_by(order) %>%
  miss_var_summary(order=T)
```

```
## # A tibble: 204 × 4
## # Groups:   order [17]
##    order        variable     n_miss pct_miss
##    <chr>        <chr>         <int>    <dbl>
##  1 Artiodactyla wean_mass       134    83.2 
##  2 Artiodactyla litters_year     77    47.8 
##  3 Artiodactyla weaning          68    42.2 
##  4 Artiodactyla max_life         62    38.5 
##  5 Artiodactyla afr              32    19.9 
##  6 Artiodactyla gestation        11     6.83
##  7 Artiodactyla litter_size       5     3.11
##  8 Artiodactyla mass              4     2.48
##  9 Artiodactyla family            0     0   
## 10 Artiodactyla genus             0     0   
## # ℹ 194 more rows
```

`naniar` also has a nice replace functions which will allow you to precisely control which values you want replaced with NA's in each variable. This is a nice alternative to `mutate()` and `na_if()`.  

```r
life_history %>% #going back to the original data
  replace_with_na(replace = list(newborn = "not measured", 
                                 weaning= -999, 
                                 wean_mass= -999, 
                                 afr= -999, 
                                 max_life= 0, 
                                 litter_size= -999, 
                                 gestation= -999, 
                                 mass= -999)) %>% 
miss_var_summary()
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

You can also use naniar to replace a specific value (like -999) with NA across the entire data set.  

```r
life_history %>% #going back to the original data
  replace_with_na_all(condition = ~.x == -999)%>% 
miss_var_summary()
```

```
## # A tibble: 13 × 3
##    variable     n_miss pct_miss
##    <chr>         <int>    <dbl>
##  1 wean_mass      1039    72.2 
##  2 litters_year    689    47.8 
##  3 weaning         619    43.0 
##  4 afr             607    42.2 
##  5 gestation       418    29.0 
##  6 mass             85     5.90
##  7 litter_size      84     5.83
##  8 order             0     0   
##  9 family            0     0   
## 10 genus             0     0   
## 11 species           0     0   
## 12 newborn           0     0   
## 13 max_life          0     0
```

Finally, naniar has some built-in examples of common values or character strings used to represent NA's. The chunk below will use these built-in parameters to replace NA's across the entire data set.

```r
common_na_strings
```

```
##  [1] "missing" "NA"      "N A"     "N/A"     "#N/A"    "NA "     " NA"    
##  [8] "N /A"    "N / A"   " N / A"  "N / A "  "na"      "n a"     "n/a"    
## [15] "na "     " na"     "n /a"    "n / a"   " a / a"  "n / a "  "NULL"   
## [22] "null"    ""        "\\?"     "\\*"     "\\."
```


```r
common_na_numbers
```

```
## [1]    -9   -99  -999 -9999  9999    66    77    88
```


```r
life_history %>% #going back to the original data
  replace_with_na_all(condition = ~.x %in% c(common_na_strings, common_na_numbers)) %>% 
  mutate(newborn=na_if(newborn, "not measured"))
```

```
## # A tibble: 1,440 × 13
##    order   family genus species   mass gestation newborn weaning wean_mass   afr
##    <chr>   <chr>  <chr> <chr>    <dbl>     <dbl> <chr>     <dbl>     <dbl> <dbl>
##  1 Artiod… Antil… Anti… americ… 4.54e4      8.13 3246.36    3         8900  13.5
##  2 Artiod… Bovid… Addax nasoma… 1.82e5      9.39 5480       6.5         NA  27.3
##  3 Artiod… Bovid… Aepy… melamp… 4.15e4      6.35 5093       5.63     15900  16.7
##  4 Artiod… Bovid… Alce… busela… 1.5 e5      7.9  10166.…    6.5         NA  23.0
##  5 Artiod… Bovid… Ammo… clarkei 2.85e4      6.8  <NA>      NA           NA  NA  
##  6 Artiod… Bovid… Ammo… lervia  5.55e4      5.08 3810       4           NA  14.9
##  7 Artiod… Bovid… Anti… marsup… 3   e4      5.72 3910       4.04        NA  10.2
##  8 Artiod… Bovid… Anti… cervic… 3.75e4      5.5  3846       2.13        NA  20.1
##  9 Artiod… Bovid… Bison bison   4.98e5      8.93 20000     10.7     157500  29.4
## 10 Artiod… Bovid… Bison bonasus 5   e5      9.14 23000.…    6.6         NA  30.0
## # ℹ 1,430 more rows
## # ℹ 3 more variables: max_life <dbl>, litter_size <dbl>, litters_year <dbl>
```

## Practice
Let's practice evaluating NA's in a large data set. The data are compiled from [CITES](https://cites.org/eng). This is the international organization that tracks trade in endangered wildlife. You can find information about the data [here](https://www.kaggle.com/cites/cites-wildlife-trade-database).  

Some key information:  
[country codes](https://en.wikipedia.org/wiki/ISO_3166-1_alpha-2)  

1. Import the data and do a little exploration. Be sure to clean the names if necessary.

```r
cites <- read_csv("data/cites.csv") %>% clean_names()
```

```
## Rows: 67161 Columns: 16
## ── Column specification ────────────────────────────────────────────────────────
## Delimiter: ","
## chr (13): App., Taxon, Class, Order, Family, Genus, Importer, Exporter, Orig...
## dbl  (3): Year, Importer reported quantity, Exporter reported quantity
## 
## ℹ Use `spec()` to retrieve the full column specification for this data.
## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.
```

2. Use `naniar` to summarize the NA's in each variable.

```r
miss_var_summary(cites)
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

3. Try using `group_by()` with `naniar`. Look specifically at `class` and `exporter_reported_quantity`. For which taxonomic classes do we have the highest number of missing export data?

```r
cites %>%
  select(class, exporter_reported_quantity) %>% 
  group_by(class) %>%
  miss_var_summary() %>% 
  arrange(desc(n_miss))
```

```
## # A tibble: 17 × 4
## # Groups:   class [17]
##    class          variable                   n_miss pct_miss
##    <chr>          <chr>                       <int>    <dbl>
##  1 <NA>           exporter_reported_quantity   7002     34.6
##  2 Reptilia       exporter_reported_quantity   5323     28.9
##  3 Anthozoa       exporter_reported_quantity   3858     43.9
##  4 Mammalia       exporter_reported_quantity   3731     43.9
##  5 Aves           exporter_reported_quantity   1792     26.1
##  6 Actinopteri    exporter_reported_quantity    726     26.3
##  7 Amphibia       exporter_reported_quantity    190     45.2
##  8 Bivalvia       exporter_reported_quantity    165     61.3
##  9 Gastropoda     exporter_reported_quantity    104     54.5
## 10 Insecta        exporter_reported_quantity     74     23.9
## 11 Hydrozoa       exporter_reported_quantity     61     33.7
## 12 Elasmobranchii exporter_reported_quantity     58     51.3
## 13 Arachnida      exporter_reported_quantity     32     47.8
## 14 Hirudinoidea   exporter_reported_quantity     11     32.4
## 15 Holothuroidea  exporter_reported_quantity     10    100  
## 16 Dipneusti      exporter_reported_quantity      3     75  
## 17 Coelacanthi    exporter_reported_quantity      0      0
```

## Dealing with NA's in advance- nuclear option
If you are sure that you know how NA's are treated in the data, then you can deal with them in advance using `na()` as part of the `readr` package. Notice that you still need to deal with some variables seperately. For example, you don't want to replace all 0's with NA, just the `max_life` variable.  

```r
read_csv(file = "data/mammal_lifehistories_v3.csv", na = c("NA", " ", ".", "-999", "not measured")) %>% clean_names()
```

```
## Warning: One or more parsing issues, call `problems()` on your data frame for details,
## e.g.:
##   dat <- vroom(...)
##   problems(dat)
```

```
## Rows: 1440 Columns: 13
## ── Column specification ────────────────────────────────────────────────────────
## Delimiter: ","
## chr (4): order, family, Genus, species
## dbl (9): mass, gestation, newborn, weaning, wean mass, AFR, max. life, litte...
## 
## ℹ Use `spec()` to retrieve the full column specification for this data.
## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.
```

```
## # A tibble: 1,440 × 13
##    order   family genus species   mass gestation newborn weaning wean_mass   afr
##    <chr>   <chr>  <chr> <chr>    <dbl>     <dbl>   <dbl>   <dbl>     <dbl> <dbl>
##  1 Artiod… Antil… Anti… americ… 4.54e4      8.13   3246.    3         8900  13.5
##  2 Artiod… Bovid… Addax nasoma… 1.82e5      9.39   5480     6.5         NA  27.3
##  3 Artiod… Bovid… Aepy… melamp… 4.15e4      6.35   5093     5.63     15900  16.7
##  4 Artiod… Bovid… Alce… busela… 1.5 e5      7.9   10167.    6.5         NA  23.0
##  5 Artiod… Bovid… Ammo… clarkei 2.85e4      6.8      NA    NA           NA  NA  
##  6 Artiod… Bovid… Ammo… lervia  5.55e4      5.08   3810     4           NA  14.9
##  7 Artiod… Bovid… Anti… marsup… 3   e4      5.72   3910     4.04        NA  10.2
##  8 Artiod… Bovid… Anti… cervic… 3.75e4      5.5    3846     2.13        NA  20.1
##  9 Artiod… Bovid… Bison bison   4.98e5      8.93  20000    10.7     157500  29.4
## 10 Artiod… Bovid… Bison bonasus 5   e5      9.14  23000.    6.6         NA  30.0
## # ℹ 1,430 more rows
## # ℹ 3 more variables: max_life <dbl>, litter_size <dbl>, litters_year <dbl>
```


LAB 9

## Tidy data
Most "wild" data are organized incorrectly for work in R and, as you might expect, the tools used to transform data are a core part of the tidyverse. I will attempt to summarize the most important points below, but you should read [chapter 12 of the data science text](https://r4ds.had.co.nz/tidy-data.html) for a more thorough explanation.  

`Tidy` data follows three conventions:   
(1) each variable has its own column  
(2) each observation has its own row  
(3) each value has its own cell  

This is summarized in the image below. The package used to tidy data is called **tidyr** and is a core part of the tidyverse.  
![*Tidy Data.*](tidy-1.png)

## `pivot_longer()`  
Scientists frequently use spreadsheets that are organized to make data entry efficient. This is often referred to as **wide format**. Unfortunately, the wide format creates a problem because column names may actually represent values of a variable. The command `pivot_longer()` shifts data from wide to long format.   

Rules:  
+ `pivot_longer`(cols, names_to, values_to)
+ `cols` - Columns to pivot to longer format
+ `names_to` - Name of the new column; it will contain the column names of gathered columns as values
+ `values_to` - Name of the new column; it will contain the data stored in the values of gathered columns

## Example 1: column names are data
The following data show results from a drug trial with four treatments on six patients. The values represent resting heart rate.  

```r
heartrate <- read_csv("data/heartrate.csv")
```

```
## Rows: 6 Columns: 5
## ── Column specification ────────────────────────────────────────────────────────
## Delimiter: ","
## chr (1): patient
## dbl (4): a, b, c, d
## 
## ℹ Use `spec()` to retrieve the full column specification for this data.
## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.
```

```r
heartrate
```

```
## # A tibble: 6 × 5
##   patient      a     b     c     d
##   <chr>    <dbl> <dbl> <dbl> <dbl>
## 1 Margaret    72    74    80    68
## 2 Frank       84    84    88    76
## 3 Hawkeye     64    66    68    64
## 4 Trapper     60    58    64    58
## 5 Radar       74    72    78    70
## 6 Henry       88    87    88    72
```

Let's assess whether or not these data are tidy.  
(1) each variable has its own column?  
 
(2) each observation has its own row?  

(3) each value has its own cell?  
  
To fix this problem, we need to reshape the table to long format while keeping track of column names and values. We do this using `pivot_longer()`. Notice that the dimensions of the data frame change.  

```r
heartrate %>% 
  pivot_longer(-patient, #patient will not move
               names_to = "drug", #make a new column called "drug"
               values_to="heartrate" #values moved to a new column called "heartrate"
               )
```

```
## # A tibble: 24 × 3
##    patient  drug  heartrate
##    <chr>    <chr>     <dbl>
##  1 Margaret a            72
##  2 Margaret b            74
##  3 Margaret c            80
##  4 Margaret d            68
##  5 Frank    a            84
##  6 Frank    b            84
##  7 Frank    c            88
##  8 Frank    d            76
##  9 Hawkeye  a            64
## 10 Hawkeye  b            66
## # ℹ 14 more rows
```

## Practice
1. Import the file `relig_income.csv` and store it as a new object `relig_income`.  

```r
relig_income <- read_csv("data/relig_income.csv")
```

```
## Rows: 18 Columns: 11
## ── Column specification ────────────────────────────────────────────────────────
## Delimiter: ","
## chr  (1): religion
## dbl (10): <$10k, $10-20k, $20-30k, $30-40k, $40-50k, $50-75k, $75-100k, $100...
## 
## ℹ Use `spec()` to retrieve the full column specification for this data.
## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.
```

```r
relig_income
```

```
## # A tibble: 18 × 11
##    religion `<$10k` `$10-20k` `$20-30k` `$30-40k` `$40-50k` `$50-75k` `$75-100k`
##    <chr>      <dbl>     <dbl>     <dbl>     <dbl>     <dbl>     <dbl>      <dbl>
##  1 Agnostic      27        34        60        81        76       137        122
##  2 Atheist       12        27        37        52        35        70         73
##  3 Buddhist      27        21        30        34        33        58         62
##  4 Catholic     418       617       732       670       638      1116        949
##  5 Don’t k…      15        14        15        11        10        35         21
##  6 Evangel…     575       869      1064       982       881      1486        949
##  7 Hindu          1         9         7         9        11        34         47
##  8 Histori…     228       244       236       238       197       223        131
##  9 Jehovah…      20        27        24        24        21        30         15
## 10 Jewish        19        19        25        25        30        95         69
## 11 Mainlin…     289       495       619       655       651      1107        939
## 12 Mormon        29        40        48        51        56       112         85
## 13 Muslim         6         7         9        10         9        23         16
## 14 Orthodox      13        17        23        32        32        47         38
## 15 Other C…       9         7        11        13        13        14         18
## 16 Other F…      20        33        40        46        49        63         46
## 17 Other W…       5         2         3         4         2         7          3
## 18 Unaffil…     217       299       374       365       341       528        407
## # ℹ 3 more variables: `$100-150k` <dbl>, `>150k` <dbl>,
## #   `Don't know/refused` <dbl>
```

2. Why are these data untidy?  

3. Use `pivot_longer()` to make the data tidy.  

```r
relig_income %>% 
  pivot_longer(-religion,
               names_to = "income",
               values_to = "total"
  )
```

```
## # A tibble: 180 × 3
##    religion income             total
##    <chr>    <chr>              <dbl>
##  1 Agnostic <$10k                 27
##  2 Agnostic $10-20k               34
##  3 Agnostic $20-30k               60
##  4 Agnostic $30-40k               81
##  5 Agnostic $40-50k               76
##  6 Agnostic $50-75k              137
##  7 Agnostic $75-100k             122
##  8 Agnostic $100-150k            109
##  9 Agnostic >150k                 84
## 10 Agnostic Don't know/refused    96
## # ℹ 170 more rows
```

## Example 2: some column names are data
Some (but not all) of the column names are data. We also have NA's.

```r
billboard <- read_csv("data/billboard.csv")
```

```
## Rows: 317 Columns: 79
## ── Column specification ────────────────────────────────────────────────────────
## Delimiter: ","
## chr   (2): artist, track
## dbl  (65): wk1, wk2, wk3, wk4, wk5, wk6, wk7, wk8, wk9, wk10, wk11, wk12, wk...
## lgl  (11): wk66, wk67, wk68, wk69, wk70, wk71, wk72, wk73, wk74, wk75, wk76
## date  (1): date.entered
## 
## ℹ Use `spec()` to retrieve the full column specification for this data.
## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.
```

```r
billboard
```

```
## # A tibble: 317 × 79
##    artist     track date.entered   wk1   wk2   wk3   wk4   wk5   wk6   wk7   wk8
##    <chr>      <chr> <date>       <dbl> <dbl> <dbl> <dbl> <dbl> <dbl> <dbl> <dbl>
##  1 2 Pac      Baby… 2000-02-26      87    82    72    77    87    94    99    NA
##  2 2Ge+her    The … 2000-09-02      91    87    92    NA    NA    NA    NA    NA
##  3 3 Doors D… Kryp… 2000-04-08      81    70    68    67    66    57    54    53
##  4 3 Doors D… Loser 2000-10-21      76    76    72    69    67    65    55    59
##  5 504 Boyz   Wobb… 2000-04-15      57    34    25    17    17    31    36    49
##  6 98^0       Give… 2000-08-19      51    39    34    26    26    19     2     2
##  7 A*Teens    Danc… 2000-07-08      97    97    96    95   100    NA    NA    NA
##  8 Aaliyah    I Do… 2000-01-29      84    62    51    41    38    35    35    38
##  9 Aaliyah    Try … 2000-03-18      59    53    38    28    21    18    16    14
## 10 Adams, Yo… Open… 2000-08-26      76    76    74    69    68    67    61    58
## # ℹ 307 more rows
## # ℹ 68 more variables: wk9 <dbl>, wk10 <dbl>, wk11 <dbl>, wk12 <dbl>,
## #   wk13 <dbl>, wk14 <dbl>, wk15 <dbl>, wk16 <dbl>, wk17 <dbl>, wk18 <dbl>,
## #   wk19 <dbl>, wk20 <dbl>, wk21 <dbl>, wk22 <dbl>, wk23 <dbl>, wk24 <dbl>,
## #   wk25 <dbl>, wk26 <dbl>, wk27 <dbl>, wk28 <dbl>, wk29 <dbl>, wk30 <dbl>,
## #   wk31 <dbl>, wk32 <dbl>, wk33 <dbl>, wk34 <dbl>, wk35 <dbl>, wk36 <dbl>,
## #   wk37 <dbl>, wk38 <dbl>, wk39 <dbl>, wk40 <dbl>, wk41 <dbl>, wk42 <dbl>, …
```

Solution 1: specify a range of columns that you want to pivot.

```r
billboard2 <- 
  billboard %>% 
  pivot_longer(wk1:wk76, # a range of columns
               names_to = "week",
               values_to = "rank", 
               values_drop_na = TRUE #this will drop the NA's
               )
billboard2
```

```
## # A tibble: 5,307 × 5
##    artist  track                   date.entered week   rank
##    <chr>   <chr>                   <date>       <chr> <dbl>
##  1 2 Pac   Baby Don't Cry (Keep... 2000-02-26   wk1      87
##  2 2 Pac   Baby Don't Cry (Keep... 2000-02-26   wk2      82
##  3 2 Pac   Baby Don't Cry (Keep... 2000-02-26   wk3      72
##  4 2 Pac   Baby Don't Cry (Keep... 2000-02-26   wk4      77
##  5 2 Pac   Baby Don't Cry (Keep... 2000-02-26   wk5      87
##  6 2 Pac   Baby Don't Cry (Keep... 2000-02-26   wk6      94
##  7 2 Pac   Baby Don't Cry (Keep... 2000-02-26   wk7      99
##  8 2Ge+her The Hardest Part Of ... 2000-09-02   wk1      91
##  9 2Ge+her The Hardest Part Of ... 2000-09-02   wk2      87
## 10 2Ge+her The Hardest Part Of ... 2000-09-02   wk3      92
## # ℹ 5,297 more rows
```

Solution 2: OR, specify columns that you want to stay fixed.

```r
billboard3 <- 
  billboard %>% 
  pivot_longer(-c(artist, track, date.entered), #specific columns to stay fixed
               names_to = "week",
               values_to = "rank",
               values_drop_na = TRUE
               )
billboard3
```

```
## # A tibble: 5,307 × 5
##    artist  track                   date.entered week   rank
##    <chr>   <chr>                   <date>       <chr> <dbl>
##  1 2 Pac   Baby Don't Cry (Keep... 2000-02-26   wk1      87
##  2 2 Pac   Baby Don't Cry (Keep... 2000-02-26   wk2      82
##  3 2 Pac   Baby Don't Cry (Keep... 2000-02-26   wk3      72
##  4 2 Pac   Baby Don't Cry (Keep... 2000-02-26   wk4      77
##  5 2 Pac   Baby Don't Cry (Keep... 2000-02-26   wk5      87
##  6 2 Pac   Baby Don't Cry (Keep... 2000-02-26   wk6      94
##  7 2 Pac   Baby Don't Cry (Keep... 2000-02-26   wk7      99
##  8 2Ge+her The Hardest Part Of ... 2000-09-02   wk1      91
##  9 2Ge+her The Hardest Part Of ... 2000-09-02   wk2      87
## 10 2Ge+her The Hardest Part Of ... 2000-09-02   wk3      92
## # ℹ 5,297 more rows
```

Solution 3: identify columns by a prefix, remove the prefix and all NA's.

```r
billboard %>% 
   pivot_longer(
   cols = starts_with("wk"), #columns that start with "wk"
   names_to = "week",
   names_prefix = "wk",
   values_to = "rank",
   values_drop_na = TRUE)
```

```
## # A tibble: 5,307 × 5
##    artist  track                   date.entered week   rank
##    <chr>   <chr>                   <date>       <chr> <dbl>
##  1 2 Pac   Baby Don't Cry (Keep... 2000-02-26   1        87
##  2 2 Pac   Baby Don't Cry (Keep... 2000-02-26   2        82
##  3 2 Pac   Baby Don't Cry (Keep... 2000-02-26   3        72
##  4 2 Pac   Baby Don't Cry (Keep... 2000-02-26   4        77
##  5 2 Pac   Baby Don't Cry (Keep... 2000-02-26   5        87
##  6 2 Pac   Baby Don't Cry (Keep... 2000-02-26   6        94
##  7 2 Pac   Baby Don't Cry (Keep... 2000-02-26   7        99
##  8 2Ge+her The Hardest Part Of ... 2000-09-02   1        91
##  9 2Ge+her The Hardest Part Of ... 2000-09-02   2        87
## 10 2Ge+her The Hardest Part Of ... 2000-09-02   3        92
## # ℹ 5,297 more rows
```

## Practice  
1. Import `plant_data.csv` as a new object `plant_data`.  

```r
plant_data <- read_csv("data/plant_data.csv")
```

```
## Rows: 3 Columns: 33
## ── Column specification ────────────────────────────────────────────────────────
## Delimiter: ","
## chr  (3): genotype, water_sched_prog, greenhouse
## dbl (30): day1, day2, day3, day4, day5, day6, day7, day8, day9, day10, day11...
## 
## ℹ Use `spec()` to retrieve the full column specification for this data.
## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.
```

```r
plant_data
```

```
## # A tibble: 3 × 33
##   genotype water_sched_prog greenhouse  day1  day2  day3  day4  day5  day6  day7
##   <chr>    <chr>            <chr>      <dbl> <dbl> <dbl> <dbl> <dbl> <dbl> <dbl>
## 1 control  A                A761        21.7  19.9  20.7  19.4  20.2  19.2  20.6
## 2 mutant1  A                A761        24.3  24.9  25.2  25.3  27.4  25.5  25.4
## 3 mutant2  A                A761        20.7  21.3  21.3  22.5  19.8  21.4  21.9
## # ℹ 23 more variables: day8 <dbl>, day9 <dbl>, day10 <dbl>, day11 <dbl>,
## #   day12 <dbl>, day13 <dbl>, day14 <dbl>, day15 <dbl>, day16 <dbl>,
## #   day17 <dbl>, day18 <dbl>, day19 <dbl>, day20 <dbl>, day21 <dbl>,
## #   day22 <dbl>, day23 <dbl>, day24 <dbl>, day25 <dbl>, day26 <dbl>,
## #   day27 <dbl>, day28 <dbl>, day29 <dbl>, day30 <dbl>
```

2. Why are these data not tidy?  


3. Use `pivot_longer()` to make the data tidy. Focus the data only on genotype, water_sched_prog, and greenhouse.  

```r
plant_data %>% 
  pivot_longer(-c(genotype, water_sched_prog, greenhouse),
               names_to = "v1",
               values_to = "v2",
               values_drop_na = T
               )
```

```
## # A tibble: 90 × 5
##    genotype water_sched_prog greenhouse v1       v2
##    <chr>    <chr>            <chr>      <chr> <dbl>
##  1 control  A                A761       day1   21.7
##  2 control  A                A761       day2   19.9
##  3 control  A                A761       day3   20.7
##  4 control  A                A761       day4   19.4
##  5 control  A                A761       day5   20.2
##  6 control  A                A761       day6   19.2
##  7 control  A                A761       day7   20.6
##  8 control  A                A761       day8   19.9
##  9 control  A                A761       day9   19.2
## 10 control  A                A761       day10  20.4
## # ℹ 80 more rows
```

## Example 3: more than one variable in a column name
In this case, there are two observations in each column name.

```r
qpcr_untidy <- read_csv("data/qpcr_untidy.csv")
```

```
## Rows: 5 Columns: 10
## ── Column specification ────────────────────────────────────────────────────────
## Delimiter: ","
## chr (1): gene
## dbl (9): exp1_rep1, exp1_rep2, exp1_rep3, exp2_rep1, exp2_rep2, exp2_rep3, e...
## 
## ℹ Use `spec()` to retrieve the full column specification for this data.
## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.
```

```r
qpcr_untidy
```

```
## # A tibble: 5 × 10
##   gene  exp1_rep1 exp1_rep2 exp1_rep3 exp2_rep1 exp2_rep2 exp2_rep3 exp3_rep1
##   <chr>     <dbl>     <dbl>     <dbl>     <dbl>     <dbl>     <dbl>     <dbl>
## 1 A          21.7      19.8      20.7      18.3      20.4      17.6      20.6
## 2 B          24.3      24.8      25.2      26.0      29.9      26.4      25.4
## 3 C          20.7      21.5      21.3      25.5      18.7      22.3      21.9
## 4 D          26.9      28.0      27.7      33.1      24.3      28.9      28.5
## 5 E          25.0      22.7      23.8      21.1      23.4      20.2      23.7
## # ℹ 2 more variables: exp3_rep2 <dbl>, exp3_rep3 <dbl>
```

`names_sep` helps pull these apart, but we still have "exp" and "rep" to deal with.  

```r
qpcr_untidy %>% 
  pivot_longer(
    exp1_rep1:exp3_rep3,
    names_to= c("experiment", "replicate"),
    names_sep="_",
    values_to="mRNA_expression"
  )
```

```
## # A tibble: 45 × 4
##    gene  experiment replicate mRNA_expression
##    <chr> <chr>      <chr>               <dbl>
##  1 A     exp1       rep1                 21.7
##  2 A     exp1       rep2                 19.8
##  3 A     exp1       rep3                 20.7
##  4 A     exp2       rep1                 18.3
##  5 A     exp2       rep2                 20.4
##  6 A     exp2       rep3                 17.6
##  7 A     exp3       rep1                 20.6
##  8 A     exp3       rep2                 19.9
##  9 A     exp3       rep3                 19.2
## 10 B     exp1       rep1                 24.3
## # ℹ 35 more rows
```

## `pivot_longer()`
Recall that we use `pivot_longer()` when our column names actually represent variables. A classic example would be that the column names represent observations of a variable.

```r
datasets::USPersonalExpenditure
```

```
##                       1940   1945  1950 1955  1960
## Food and Tobacco    22.200 44.500 59.60 73.2 86.80
## Household Operation 10.500 15.500 29.00 36.5 46.20
## Medical and Health   3.530  5.760  9.71 14.0 21.10
## Personal Care        1.040  1.980  2.45  3.4  5.40
## Private Education    0.341  0.974  1.80  2.6  3.64
```

```r
?USPersonalExpenditure
```

Here we add a new column of expenditure types, which are stored as rownames above, with `mutate()`. The `USPersonalExpenditures` data also needs to be converted to a data frame before we can use the tidyverse functions, because it comes as a matrix.

```r
expenditures <- USPersonalExpenditure %>% 
  as_tibble() %>% #this transforms the matrix into a data frame
  mutate(expenditure = rownames(USPersonalExpenditure))
expenditures
```

```
## # A tibble: 5 × 6
##   `1940` `1945` `1950` `1955` `1960` expenditure        
##    <dbl>  <dbl>  <dbl>  <dbl>  <dbl> <chr>              
## 1 22.2   44.5    59.6    73.2  86.8  Food and Tobacco   
## 2 10.5   15.5    29      36.5  46.2  Household Operation
## 3  3.53   5.76    9.71   14    21.1  Medical and Health 
## 4  1.04   1.98    2.45    3.4   5.4  Personal Care      
## 5  0.341  0.974   1.8     2.6   3.64 Private Education
```

## Practice
1. Are these data tidy? Please use `pivot_longer()` to tidy the data.

```r
expenditures %>% 
  pivot_longer(-expenditure, #not moving ependiture
               names_to = "year", # new variable year
               values_to = "bn_dollars") #moving values into bn_dollars
```

```
## # A tibble: 25 × 3
##    expenditure         year  bn_dollars
##    <chr>               <chr>      <dbl>
##  1 Food and Tobacco    1940        22.2
##  2 Food and Tobacco    1945        44.5
##  3 Food and Tobacco    1950        59.6
##  4 Food and Tobacco    1955        73.2
##  5 Food and Tobacco    1960        86.8
##  6 Household Operation 1940        10.5
##  7 Household Operation 1945        15.5
##  8 Household Operation 1950        29  
##  9 Household Operation 1955        36.5
## 10 Household Operation 1960        46.2
## # ℹ 15 more rows
```

2. Restrict the data to private education only. Sort in ascending order.

```r
expenditures %>% 
  pivot_longer(-expenditure,
               names_to = "year",
               values_to = "bn_dollars") %>% 
  filter(expenditure=="Private Education")
```

```
## # A tibble: 5 × 3
##   expenditure       year  bn_dollars
##   <chr>             <chr>      <dbl>
## 1 Private Education 1940       0.341
## 2 Private Education 1945       0.974
## 3 Private Education 1950       1.8  
## 4 Private Education 1955       2.6  
## 5 Private Education 1960       3.64
```

## `separate()`
In this new heart rate example, we have the sex of each patient included with their name. Are these data tidy? No, there is more than one value per cell in the patient column and the columns a, b, c, d once again represent values.

```r
heartrate2 <- read_csv("data/heartrate2.csv")
```

```
## Rows: 6 Columns: 5
## ── Column specification ────────────────────────────────────────────────────────
## Delimiter: ","
## chr (1): patient
## dbl (4): a, b, c, d
## 
## ℹ Use `spec()` to retrieve the full column specification for this data.
## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.
```

```r
heartrate2
```

```
## # A tibble: 6 × 5
##   patient        a     b     c     d
##   <chr>      <dbl> <dbl> <dbl> <dbl>
## 1 Margaret_f    72    74    80    68
## 2 Frank_m       84    84    88    76
## 3 Hawkeye_m     64    66    68    64
## 4 Trapper_m     60    58    64    58
## 5 Radar_m       74    72    78    70
## 6 Henry_m       88    87    88    72
```

We need to start by separating the patient names from their sexes. `separate()` needs to know which column you want to split, the names of the new columns, and what to look for in terms of breaks in the data.

```r
heartrate2 %>% 
  separate(patient, into= c("patient", "sex"), sep = "_")
```

```
## # A tibble: 6 × 6
##   patient  sex       a     b     c     d
##   <chr>    <chr> <dbl> <dbl> <dbl> <dbl>
## 1 Margaret f        72    74    80    68
## 2 Frank    m        84    84    88    76
## 3 Hawkeye  m        64    66    68    64
## 4 Trapper  m        60    58    64    58
## 5 Radar    m        74    72    78    70
## 6 Henry    m        88    87    88    72
```

## Practice
1. Re-examine `heartrate2`. Use `separate()` for the sexes, `pivot_longer()` to tidy, and `arrange()` to organize by patient and drug. Store this as a new object `heartrate3`.  

```r
heartrate3 <- heartrate2 %>% 
  separate(patient, into=c("patient", "sex"), sep="_") %>% 
  pivot_longer(-c(patient, sex),
               names_to = "drug",
               values_to = "heartrate")
heartrate3
```

```
## # A tibble: 24 × 4
##    patient  sex   drug  heartrate
##    <chr>    <chr> <chr>     <dbl>
##  1 Margaret f     a            72
##  2 Margaret f     b            74
##  3 Margaret f     c            80
##  4 Margaret f     d            68
##  5 Frank    m     a            84
##  6 Frank    m     b            84
##  7 Frank    m     c            88
##  8 Frank    m     d            76
##  9 Hawkeye  m     a            64
## 10 Hawkeye  m     b            66
## # ℹ 14 more rows
```

2. `unite()` is the opposite of separate(). Its syntax is straightforward. You only need to give a new column name and then list the columns to combine with a separation character.  Give it a try below by recombining patient and sex from `heartrate3`.  

```r
heartrate3 %>% 
  unite(patient_sex, "patient", "sex", sep=" ")
```

```
## # A tibble: 24 × 3
##    patient_sex drug  heartrate
##    <chr>       <chr>     <dbl>
##  1 Margaret f  a            72
##  2 Margaret f  b            74
##  3 Margaret f  c            80
##  4 Margaret f  d            68
##  5 Frank m     a            84
##  6 Frank m     b            84
##  7 Frank m     c            88
##  8 Frank m     d            76
##  9 Hawkeye m   a            64
## 10 Hawkeye m   b            66
## # ℹ 14 more rows
```

## `pivot_wider()`
The opposite of `pivot_longer()`. You use `pivot_wider()` when you have an observation scattered across multiple rows. In the example below, `cases` and `population` represent variable names not observations.  

Rules:  
+ `pivot_wider`(names_from, values_from)  
+ `names_from` - Values in the `names_from` column will become new column names  
+ `values_from` - Cell values will be taken from the `values_from` column  


```r
tb_data <- read_csv("data/tb_data.csv")
```

```
## Rows: 12 Columns: 4
## ── Column specification ────────────────────────────────────────────────────────
## Delimiter: ","
## chr (2): country, key
## dbl (2): year, value
## 
## ℹ Use `spec()` to retrieve the full column specification for this data.
## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.
```

```r
tb_data
```

```
## # A tibble: 12 × 4
##    country      year key             value
##    <chr>       <dbl> <chr>           <dbl>
##  1 Afghanistan  1999 cases             745
##  2 Afghanistan  1999 population   19987071
##  3 Afghanistan  2000 cases            2666
##  4 Afghanistan  2000 population   20595360
##  5 Brazil       1999 cases           37737
##  6 Brazil       1999 population  172006362
##  7 Brazil       2000 cases           80488
##  8 Brazil       2000 population  174504898
##  9 China        1999 cases          212258
## 10 China        1999 population 1272915272
## 11 China        2000 cases          213766
## 12 China        2000 population 1280428583
```


```r
tb_data %>% 
  filter(key=="population")
```

```
## # A tibble: 6 × 4
##   country      year key             value
##   <chr>       <dbl> <chr>           <dbl>
## 1 Afghanistan  1999 population   19987071
## 2 Afghanistan  2000 population   20595360
## 3 Brazil       1999 population  172006362
## 4 Brazil       2000 population  174504898
## 5 China        1999 population 1272915272
## 6 China        2000 population 1280428583
```



When using `pivot_wider()` we use `names_from` to identify the variables (new column names) and `values_from` to identify the values associated with the new columns.

```r
tb_data %>% 
  pivot_wider(names_from = "key", #the observations under key will become new columns
              values_from = "value") #the values under value will be moved to the new columns
```

```
## # A tibble: 6 × 4
##   country      year  cases population
##   <chr>       <dbl>  <dbl>      <dbl>
## 1 Afghanistan  1999    745   19987071
## 2 Afghanistan  2000   2666   20595360
## 3 Brazil       1999  37737  172006362
## 4 Brazil       2000  80488  174504898
## 5 China        1999 212258 1272915272
## 6 China        2000 213766 1280428583
```

## Practice
1. Load the `gene_exp.csv` data as a new object `gene_exp`. Are these data tidy? Use `pivot_wider()` to tidy the data.

```r
gene_exp <- read_csv("data/gene_exp.csv")
```

```
## Rows: 6 Columns: 3
## ── Column specification ────────────────────────────────────────────────────────
## Delimiter: ","
## chr (2): gene_id, type
## dbl (1): L4_values
## 
## ℹ Use `spec()` to retrieve the full column specification for this data.
## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.
```

```r
gene_exp
```

```
## # A tibble: 6 × 3
##   gene_id type      L4_values
##   <chr>   <chr>         <dbl>
## 1 gene1   treatment      15.6
## 2 gene1   control        19.3
## 3 gene2   treatment      22.2
## 4 gene2   control        16.0
## 5 gene3   treatment      17.7
## 6 gene3   control        26.9
```


```r
gene_exp %>% 
  pivot_wider(names_from = "type",
              values_from = "L4_values")
```

```
## # A tibble: 3 × 3
##   gene_id treatment control
##   <chr>       <dbl>   <dbl>
## 1 gene1        15.6    19.3
## 2 gene2        22.2    16.0
## 3 gene3        17.7    26.9
```

## Practice
For the last practice example, I will use data from the awesome [R Ladies Sydney](https://rladiessydney.org/courses/ryouwithme/02-cleanitup-5/) blog. This data set is compiled by the NSW Office of Environment and Heritage contains the enterococci counts in water samples obtained from Sydney beaches as part of the Beachwatch Water Quality Program! The data set we’ll be working with is current as of October 13th 2018.  

1. Load the beachbugs data and have a look.

```r
beachbugs <- read_csv("data/beachbugs_long.csv")
```

```
## Rows: 66 Columns: 3
## ── Column specification ────────────────────────────────────────────────────────
## Delimiter: ","
## chr (1): site
## dbl (2): year, buglevels
## 
## ℹ Use `spec()` to retrieve the full column specification for this data.
## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.
```

```r
beachbugs
```

```
## # A tibble: 66 × 3
##     year site                    buglevels
##    <dbl> <chr>                       <dbl>
##  1  2013 Bondi Beach                 32.2 
##  2  2013 Bronte Beach                26.8 
##  3  2013 Clovelly Beach               9.28
##  4  2013 Coogee Beach                39.7 
##  5  2013 Gordons Bay (East)          24.8 
##  6  2013 Little Bay Beach           122.  
##  7  2013 Malabar Beach              101.  
##  8  2013 Maroubra Beach              47.1 
##  9  2013 South Maroubra Beach        39.3 
## 10  2013 South Maroubra Rockpool     96.4 
## # ℹ 56 more rows
```

2. Use `pivot_wider` to transform the data into wide format.

```r
beachbugs_wide <- beachbugs %>% 
  pivot_wider(names_from = site,
              values_from = buglevels)
beachbugs_wide
```

```
## # A tibble: 6 × 12
##    year `Bondi Beach` `Bronte Beach` `Clovelly Beach` `Coogee Beach`
##   <dbl>         <dbl>          <dbl>            <dbl>          <dbl>
## 1  2013          32.2           26.8             9.28           39.7
## 2  2014          11.1           17.5            13.8            52.6
## 3  2015          14.3           23.6             8.82           40.3
## 4  2016          19.4           61.3            11.3            59.5
## 5  2017          13.2           16.8             7.93           20.7
## 6  2018          22.9           43.4            10.6            21.6
## # ℹ 7 more variables: `Gordons Bay (East)` <dbl>, `Little Bay Beach` <dbl>,
## #   `Malabar Beach` <dbl>, `Maroubra Beach` <dbl>,
## #   `South Maroubra Beach` <dbl>, `South Maroubra Rockpool` <dbl>,
## #   `Tamarama Beach` <dbl>
```

3. Now, use `pivot_longer` to transform them back to long!

```r
beachbugs_wide %>% 
  pivot_longer(-year,
               names_to = "site",
               values_to = "buglevels")
```

```
## # A tibble: 66 × 3
##     year site                    buglevels
##    <dbl> <chr>                       <dbl>
##  1  2013 Bondi Beach                 32.2 
##  2  2013 Bronte Beach                26.8 
##  3  2013 Clovelly Beach               9.28
##  4  2013 Coogee Beach                39.7 
##  5  2013 Gordons Bay (East)          24.8 
##  6  2013 Little Bay Beach           122.  
##  7  2013 Malabar Beach              101.  
##  8  2013 Maroubra Beach              47.1 
##  9  2013 South Maroubra Beach        39.3 
## 10  2013 South Maroubra Rockpool     96.4 
## # ℹ 56 more rows
```


LAB 10

## Grammar of Graphics
The ability to quickly produce and edit graphs and charts is a strength of R. These data visualizations are produced by the package `ggplot2` and it is a core part of the tidyverse. The syntax for using ggplot is specific and common to all of the plots. This is what Hadley Wickham calls a [Grammar of Graphics](http://vita.had.co.nz/papers/layered-grammar.pdf). The "gg" in `ggplot` stands for grammar of graphics.

## Philosophy
What makes a good chart? In my opinion a good chart is elegant in its simplicity. It provides a clean, clear visual of the data without being overwhelming to the reader. This can be hard to do and takes some careful thinking. Always keep in mind that the reader will almost never know the data as well as you do so you need to be mindful about presenting the facts.  

## Data Types
We first need to define some of the data types we will use to build plots.  

+ `discrete` quantitative data that only contains integers
+ `continuous` quantitative data that can take any numerical value
+ `categorical` qualitative data that can take on a limited number of values

## Basics
The syntax used by ggplot takes some practice to get used to, especially for customizing plots, but the basic elements are the same. It is helpful to think of plots as being built up in layers.  

In short, **plot= data + geom_ + aesthetics**.  

We start by calling the ggplot function, identifying the data, and specifying the axes. We then add the `geom` type to describe how we want our data represented. Each `geom_` works with specific types of data and R is capable of building plots of single variables, multiple variables, and even maps. Lastly, we add aesthetics.

## Example
To make things easy, let's start with some built in data.

```r
names(iris)
```

```
## [1] "Sepal.Length" "Sepal.Width"  "Petal.Length" "Petal.Width"  "Species"
```


```r
glimpse(iris)
```

```
## Rows: 150
## Columns: 5
## $ Sepal.Length <dbl> 5.1, 4.9, 4.7, 4.6, 5.0, 5.4, 4.6, 5.0, 4.4, 4.9, 5.4, 4.…
## $ Sepal.Width  <dbl> 3.5, 3.0, 3.2, 3.1, 3.6, 3.9, 3.4, 3.4, 2.9, 3.1, 3.7, 3.…
## $ Petal.Length <dbl> 1.4, 1.4, 1.3, 1.5, 1.4, 1.7, 1.4, 1.5, 1.4, 1.5, 1.5, 1.…
## $ Petal.Width  <dbl> 0.2, 0.2, 0.2, 0.2, 0.2, 0.4, 0.3, 0.2, 0.2, 0.1, 0.2, 0.…
## $ Species      <fct> setosa, setosa, setosa, setosa, setosa, setosa, setosa, s…
```

To make a plot, we need to first specify the data and map the aesthetics. The aesthetics include how each variable in our data set will be used. In the example below, I am using the aes() function to identify the x and y variables in the plot.

```r
ggplot(data=iris, #specify the data
       mapping=aes(x=Species, y=Petal.Length)) #map the aesthetics
```

![](EXAM2_files/figure-html/unnamed-chunk-66-1.png)<!-- -->

Notice that we have a nice background, labeled axes, and even a value range of our variables on the y-axis- but no plot. This is because we need to tell ggplot how we want our data represented. This is called the geometry or `geom()`. There are many types of `geom`, see the ggplot [cheatsheet](https://posit.co/resources/cheatsheets/).

Here we specify that we want a boxplot, indicated by `geom_boxplot()`.

```r
ggplot(data=iris, #specify the data
       mapping=aes(x=Species, y=Petal.Length))+ #map the aesthetics
  geom_boxplot() #add the plot type
```

![](EXAM2_files/figure-html/unnamed-chunk-67-1.png)<!-- -->

## Practice
1. Use the iris data to build a scatterplot that compares sepal length vs. sepal width. Use the cheat sheet for help to find the correct `geom_` for a scatterplot.

```r
names(iris)
```

```
## [1] "Sepal.Length" "Sepal.Width"  "Petal.Length" "Petal.Width"  "Species"
```


```r
ggplot(data=iris, 
       mapping=aes(x=Sepal.Width, y=Sepal.Length)) +
  geom_point()
```

![](EXAM2_files/figure-html/unnamed-chunk-69-1.png)<!-- -->

## Scatterplots and barplots
Now that we have a general idea of the syntax, let's start by working with two common plots: 1) scatter plots and 2) bar plots.

## Data
**Database of vertebrate home range sizes.**  
Reference: Tamburello N, Cote IM, Dulvy NK (2015) Energy and the scaling of animal space use. The American Naturalist 186(2):196-211. http://dx.doi.org/10.1086/682070.  
Data: http://datadryad.org/resource/doi:10.5061/dryad.q5j65/1  

```r
homerange <- read_csv("data/Tamburelloetal_HomeRangeDatabase.csv")
```

```
## Rows: 569 Columns: 24
## ── Column specification ────────────────────────────────────────────────────────
## Delimiter: ","
## chr (16): taxon, common.name, class, order, family, genus, species, primarym...
## dbl  (8): mean.mass.g, log10.mass, mean.hra.m2, log10.hra, dimension, preyma...
## 
## ℹ Use `spec()` to retrieve the full column specification for this data.
## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.
```

## Practice
1. What is the structure of the `homerange` data? Does it have any NA's? Is it tidy? Do a quick exploratory analysis of your choice below.

```r
glimpse(homerange)
```

```
## Rows: 569
## Columns: 24
## $ taxon                      <chr> "lake fishes", "river fishes", "river fishe…
## $ common.name                <chr> "american eel", "blacktail redhorse", "cent…
## $ class                      <chr> "actinopterygii", "actinopterygii", "actino…
## $ order                      <chr> "anguilliformes", "cypriniformes", "cyprini…
## $ family                     <chr> "anguillidae", "catostomidae", "cyprinidae"…
## $ genus                      <chr> "anguilla", "moxostoma", "campostoma", "cli…
## $ species                    <chr> "rostrata", "poecilura", "anomalum", "fundu…
## $ primarymethod              <chr> "telemetry", "mark-recapture", "mark-recapt…
## $ N                          <chr> "16", NA, "20", "26", "17", "5", "2", "2", …
## $ mean.mass.g                <dbl> 887.00, 562.00, 34.00, 4.00, 4.00, 3525.00,…
## $ log10.mass                 <dbl> 2.9479236, 2.7497363, 1.5314789, 0.6020600,…
## $ alternative.mass.reference <chr> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA,…
## $ mean.hra.m2                <dbl> 282750.00, 282.10, 116.11, 125.50, 87.10, 3…
## $ log10.hra                  <dbl> 5.4514026, 2.4504031, 2.0648696, 2.0986437,…
## $ hra.reference              <chr> "Minns, C. K. 1995. Allometry of home range…
## $ realm                      <chr> "aquatic", "aquatic", "aquatic", "aquatic",…
## $ thermoregulation           <chr> "ectotherm", "ectotherm", "ectotherm", "ect…
## $ locomotion                 <chr> "swimming", "swimming", "swimming", "swimmi…
## $ trophic.guild              <chr> "carnivore", "carnivore", "carnivore", "car…
## $ dimension                  <dbl> 3, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 3, 3…
## $ preymass                   <dbl> NA, NA, NA, NA, NA, NA, 1.39, NA, NA, NA, N…
## $ log10.preymass             <dbl> NA, NA, NA, NA, NA, NA, 0.1430148, NA, NA, …
## $ PPMR                       <dbl> NA, NA, NA, NA, NA, NA, 530, NA, NA, NA, NA…
## $ prey.size.reference        <chr> NA, NA, NA, NA, NA, NA, "Brose U, et al. 20…
```

### 1. Scatter Plots
Scatter plots are good at revealing relationships that are not readily visible in the raw data. For now, we will not add regression aka. "best of fit" lines or calculate any r^2^ values.  

In the case below, we are exploring whether or not there is a relationship between animal mass and home range. We are using the **log transformed values** because there is a large difference in mass and home range among the different species in the data.

```r
names(homerange)
```

```
##  [1] "taxon"                      "common.name"               
##  [3] "class"                      "order"                     
##  [5] "family"                     "genus"                     
##  [7] "species"                    "primarymethod"             
##  [9] "N"                          "mean.mass.g"               
## [11] "log10.mass"                 "alternative.mass.reference"
## [13] "mean.hra.m2"                "log10.hra"                 
## [15] "hra.reference"              "realm"                     
## [17] "thermoregulation"           "locomotion"                
## [19] "trophic.guild"              "dimension"                 
## [21] "preymass"                   "log10.preymass"            
## [23] "PPMR"                       "prey.size.reference"
```


```r
ggplot(data=homerange, #specify the data
       mapping=aes(x=log10.mass, y=log10.hra))+ #map the aesthetics
  geom_point() #add the plot type
```

![](EXAM2_files/figure-html/unnamed-chunk-73-1.png)<!-- -->

In big data sets with lots of overlapping values, over-plotting can be an issue. `geom_jitter()` is similar to `geom_point()` but it helps with over plotting by adding some random noise to the data and separating some of the individual points.

```r
ggplot(data=homerange, mapping=aes(x=log10.mass, y=log10.hra))+
  geom_jitter() #adds some random noise
```

![](EXAM2_files/figure-html/unnamed-chunk-74-1.png)<!-- -->

To add a regression (best of fit) line, we just add another layer.

```r
ggplot(data=homerange, mapping=aes(x=log10.mass, y=log10.hra))+
  geom_point()+
  geom_smooth(method=lm, se=T) #add a regression line
```

```
## `geom_smooth()` using formula = 'y ~ x'
```

![](EXAM2_files/figure-html/unnamed-chunk-75-1.png)<!-- -->

```r
names(homerange)
```

```
##  [1] "taxon"                      "common.name"               
##  [3] "class"                      "order"                     
##  [5] "family"                     "genus"                     
##  [7] "species"                    "primarymethod"             
##  [9] "N"                          "mean.mass.g"               
## [11] "log10.mass"                 "alternative.mass.reference"
## [13] "mean.hra.m2"                "log10.hra"                 
## [15] "hra.reference"              "realm"                     
## [17] "thermoregulation"           "locomotion"                
## [19] "trophic.guild"              "dimension"                 
## [21] "preymass"                   "log10.preymass"            
## [23] "PPMR"                       "prey.size.reference"
```

### Practice
1. What is the relationship between log10.hra and log10.preymass? What do you notice about how ggplot treats NA's?

```r
ggplot(data=homerange,
       mapping=aes(x=log10.hra, y=log10.preymass))+
  geom_point(na.rm=T)+
  geom_smooth(method=lm, se=T, na.rm=T)
```

```
## `geom_smooth()` using formula = 'y ~ x'
```

![](EXAM2_files/figure-html/unnamed-chunk-77-1.png)<!-- -->

### Bar Plot: `geom_bar()`
The simplest type of bar plot counts the number of observations in a categorical variable. In this case, we want to know how many observations are present in the variable `trophic.guild`. Notice that we do not specify a y-axis because it is count by default.  


```r
names(homerange)
```

```
##  [1] "taxon"                      "common.name"               
##  [3] "class"                      "order"                     
##  [5] "family"                     "genus"                     
##  [7] "species"                    "primarymethod"             
##  [9] "N"                          "mean.mass.g"               
## [11] "log10.mass"                 "alternative.mass.reference"
## [13] "mean.hra.m2"                "log10.hra"                 
## [15] "hra.reference"              "realm"                     
## [17] "thermoregulation"           "locomotion"                
## [19] "trophic.guild"              "dimension"                 
## [21] "preymass"                   "log10.preymass"            
## [23] "PPMR"                       "prey.size.reference"
```


```r
homerange %>% 
  count(trophic.guild)
```

```
## # A tibble: 2 × 2
##   trophic.guild     n
##   <chr>         <int>
## 1 carnivore       342
## 2 herbivore       227
```

Also notice that we can use pipes! The `mapping=` function is implied by `aes` and so is often left out. 

```r
homerange %>% 
  ggplot(aes(trophic.guild)) + 
  geom_bar() #good for counts
```

![](EXAM2_files/figure-html/unnamed-chunk-80-1.png)<!-- -->

### Bar Plot: `geom_col()`
Unlike `geom_bar()`, `geom_col()` allows us to specify an x-axis and a y-axis.

```r
homerange %>% 
  filter(family=="salmonidae") %>%
  select(common.name, log10.mass) %>% 
  ggplot(aes(x=common.name, y=log10.mass))+ #notice the switch in x and y
  geom_col()+
  coord_flip()
```

![](EXAM2_files/figure-html/unnamed-chunk-81-1.png)<!-- -->

`geom_bar()` with `stat="identity"`
`stat="identity"` allows us to map a variable to the y-axis so that we aren't restricted to counts.

```r
homerange %>% 
  filter(family=="salmonidae") %>% 
  ggplot(aes(x=common.name, y=log10.mass))+
  geom_bar(stat="identity")
```

![](EXAM2_files/figure-html/unnamed-chunk-82-1.png)<!-- -->

## Practice
1. Filter the `homerange` data to include `mammals` only.

```r
homerange %>% 
  filter(class=="mammalia")
```

```
## # A tibble: 238 × 24
##    taxon   common.name      class order family genus species primarymethod N    
##    <chr>   <chr>            <chr> <chr> <chr>  <chr> <chr>   <chr>         <chr>
##  1 mammals giant golden mo… mamm… afro… chrys… chry… trevel… telemetry*    <NA> 
##  2 mammals Grant's golden … mamm… afro… chrys… erem… granti  telemetry*    <NA> 
##  3 mammals pronghorn        mamm… arti… antil… anti… americ… telemetry*    <NA> 
##  4 mammals impala           mamm… arti… bovid… aepy… melamp… telemetry*    <NA> 
##  5 mammals hartebeest       mamm… arti… bovid… alce… busela… telemetry*    <NA> 
##  6 mammals barbary sheep    mamm… arti… bovid… ammo… lervia  telemetry*    <NA> 
##  7 mammals American bison   mamm… arti… bovid… bison bison   telemetry*    <NA> 
##  8 mammals European bison   mamm… arti… bovid… bison bonasus telemetry*    <NA> 
##  9 mammals goat             mamm… arti… bovid… capra hircus  telemetry*    <NA> 
## 10 mammals Spanish ibex     mamm… arti… bovid… capra pyrena… telemetry*    <NA> 
## # ℹ 228 more rows
## # ℹ 15 more variables: mean.mass.g <dbl>, log10.mass <dbl>,
## #   alternative.mass.reference <chr>, mean.hra.m2 <dbl>, log10.hra <dbl>,
## #   hra.reference <chr>, realm <chr>, thermoregulation <chr>, locomotion <chr>,
## #   trophic.guild <chr>, dimension <dbl>, preymass <dbl>, log10.preymass <dbl>,
## #   PPMR <dbl>, prey.size.reference <chr>
```

2. Are there more herbivores or carnivores in mammals? Make a bar plot that shows their relative numbers.

```r
homerange %>% 
  filter(class=="mammalia") %>% 
  count(trophic.guild)
```

```
## # A tibble: 2 × 2
##   trophic.guild     n
##   <chr>         <int>
## 1 carnivore        80
## 2 herbivore       158
```

```r
homerange %>% 
  filter(class=="mammalia") %>% 
  ggplot(aes(x=trophic.guild))+
  geom_bar()
```

![](EXAM2_files/figure-html/unnamed-chunk-85-1.png)<!-- -->




```r
homerange %>% 
  filter(class=="mammalia") %>% 
  count(trophic.guild) %>% 
  ggplot(aes(x=trophic.guild, y=n))+
  geom_col()
```

![](EXAM2_files/figure-html/unnamed-chunk-86-1.png)<!-- -->

3. Make a bar plot that shows the masses of the top 10 smallest mammals.

```r
homerange %>% 
  filter(class=="mammalia") %>% 
  top_n(-10, log10.mass) %>% 
  ggplot(aes(x=common.name, y=log10.mass))+
  geom_col()+
  coord_flip()
```

![](EXAM2_files/figure-html/unnamed-chunk-87-1.png)<!-- -->

## Load the libraries

```r
library(tidyverse)
library(skimr)
library(janitor)
library(palmerpenguins)
```

## Review
Now that you have been introduced to `ggplot`, let's practice a few more plot types. Remember that plots are built in layers: **plot= data + geom_ + aesthetics**. We have to specify each of these in order for a plot to be produced. If you get stuck, it is often helpful to stop and make a quick sketch of what you want or expect to see on a piece of scratch paper.  

Let's review using the `penguins` data. First, get an idea of the structure: Are the data tidy? Are there NA's? Are the variables discrete, categorical, or continuous?

```r
penguins
```

```
## # A tibble: 344 × 8
##    species island    bill_length_mm bill_depth_mm flipper_length_mm body_mass_g
##    <fct>   <fct>              <dbl>         <dbl>             <int>       <int>
##  1 Adelie  Torgersen           39.1          18.7               181        3750
##  2 Adelie  Torgersen           39.5          17.4               186        3800
##  3 Adelie  Torgersen           40.3          18                 195        3250
##  4 Adelie  Torgersen           NA            NA                  NA          NA
##  5 Adelie  Torgersen           36.7          19.3               193        3450
##  6 Adelie  Torgersen           39.3          20.6               190        3650
##  7 Adelie  Torgersen           38.9          17.8               181        3625
##  8 Adelie  Torgersen           39.2          19.6               195        4675
##  9 Adelie  Torgersen           34.1          18.1               193        3475
## 10 Adelie  Torgersen           42            20.2               190        4250
## # ℹ 334 more rows
## # ℹ 2 more variables: sex <fct>, year <int>
```

In a previous lab, we asked how many penguins were measured on each island.

```r
penguins %>% 
  count(island)
```

```
## # A tibble: 3 × 2
##   island        n
##   <fct>     <int>
## 1 Biscoe      168
## 2 Dream       124
## 3 Torgersen    52
```

Make this output more visual by adding a plot...

```r
penguins %>% 
  count(island) %>% 
  ggplot(aes(x=island, y=n))+
  geom_col()
```

![](EXAM2_files/figure-html/unnamed-chunk-91-1.png)<!-- -->

## Practice
1. What if we wanted a plot that showed the number of measured penguins for each species?

```r
penguins %>% count(species)
```

```
## # A tibble: 3 × 2
##   species       n
##   <fct>     <int>
## 1 Adelie      152
## 2 Chinstrap    68
## 3 Gentoo      124
```


```r
penguins %>% 
  count(species) %>% 
  ggplot(aes(x=species, y=n))+
  geom_col()
```

![](EXAM2_files/figure-html/unnamed-chunk-93-1.png)<!-- -->


```r
penguins %>% 
  ggplot(aes(x=species))+
  geom_bar()
```

![](EXAM2_files/figure-html/unnamed-chunk-94-1.png)<!-- -->

2. How about average bill length by sex?

```r
penguins %>% 
  filter(sex!="NA") %>% 
  group_by(sex) %>% 
  summarize(ave_bill_length_mean=mean(bill_length_mm)) %>% 
  ggplot(aes(x=sex, y=ave_bill_length_mean))+
  geom_col()
```

![](EXAM2_files/figure-html/unnamed-chunk-95-1.png)<!-- -->



## Box Plots
For the next series of examples, we will use the `homerange` data.  **Database of vertebrate home range sizes.**   

Reference: Tamburello N, Cote IM, Dulvy NK (2015) Energy and the scaling of animal space use. The American Naturalist 186(2):196-211. http://dx.doi.org/10.1086/682070.  
Data: http://datadryad.org/resource/doi:10.5061/dryad.q5j65/1  


```r
homerange <- read_csv("data/Tamburelloetal_HomeRangeDatabase.csv")
```

Boxplots help us visualize a range of values. So, on the x-axis we typically have something categorical and the y-axis is the range. In the case below, we are plotting `log10.mass` by taxonomic class in the `homerange` data. `geom_boxplot()` is the geom type for a standard box plot. The center line in each box represents the median, not the mean.

Let's look at the variable `log10.mass` grouped by taxonomic class.

```r
homerange %>% 
  group_by(class) %>% 
  summarize(min_log10.mass=min(log10.mass),
            max_log10.mass=max(log10.mass),
            median_log10.mass=median(log10.mass))
```

```
## # A tibble: 4 × 4
##   class          min_log10.mass max_log10.mass median_log10.mass
##   <chr>                   <dbl>          <dbl>             <dbl>
## 1 actinopterygii         -0.658           3.55              2.08
## 2 aves                    0.712           4.95              1.82
## 3 mammalia                0.620           6.60              3.33
## 4 reptilia                0.539           4.03              2.51
```


```r
homerange %>% 
  ggplot(aes(x = class, y = log10.mass)) +
  geom_boxplot()
```

![](EXAM2_files/figure-html/unnamed-chunk-98-1.png)<!-- -->

## Practice
1. There are more herbivores than carnivores in the homerange data, but how do their masses compare? Make a summary and boxplot that compares their masses. Use `log10.mass`.

```r
names(homerange)
```

```
##  [1] "taxon"                      "common.name"               
##  [3] "class"                      "order"                     
##  [5] "family"                     "genus"                     
##  [7] "species"                    "primarymethod"             
##  [9] "N"                          "mean.mass.g"               
## [11] "log10.mass"                 "alternative.mass.reference"
## [13] "mean.hra.m2"                "log10.hra"                 
## [15] "hra.reference"              "realm"                     
## [17] "thermoregulation"           "locomotion"                
## [19] "trophic.guild"              "dimension"                 
## [21] "preymass"                   "log10.preymass"            
## [23] "PPMR"                       "prey.size.reference"
```


```r
homerange %>% 
  group_by(trophic.guild) %>% 
  summarize(mean_log10_mass=mean(log10.mass))
```

```
## # A tibble: 2 × 2
##   trophic.guild mean_log10_mass
##   <chr>                   <dbl>
## 1 carnivore                2.24
## 2 herbivore                3.13
```


```r
homerange %>% 
  group_by(trophic.guild) %>% 
  summarize(mean_log10_mass=mean(log10.mass)) %>% 
  ggplot(aes(x=trophic.guild, y=mean_log10_mass))+
  geom_col()
```

![](EXAM2_files/figure-html/unnamed-chunk-101-1.png)<!-- -->


```r
homerange %>% 
  ggplot(aes(x=trophic.guild, y=log10.mass))+
  geom_boxplot()
```

![](EXAM2_files/figure-html/unnamed-chunk-102-1.png)<!-- -->


2. Have a closer look at carnivorous mammals. Summarize the range of log10.mass by family.

```r
homerange %>% 
  filter(trophic.guild=="carnivore" & class=="mammalia") %>% 
  select(family, trophic.guild, log10.mass) %>% 
  ggplot(aes(x=family, y=log10.mass))+
  geom_boxplot()+
  coord_flip()
```

![](EXAM2_files/figure-html/unnamed-chunk-103-1.png)<!-- -->


3. Now use a boxplot to visualize the range of log10.mass by family of mammalian carnivore.

```r
homerange %>% 
  filter(trophic.guild=="carnivore" & class=="mammalia") %>% 
  select(family, trophic.guild, log10.mass) %>% 
  ggplot(aes(x=family, y=log10.mass))+
  geom_boxplot()+
  coord_flip()
```

![](EXAM2_files/figure-html/unnamed-chunk-104-1.png)<!-- -->



LAB 11
## Load the libraries

```r
library("tidyverse")
library("janitor")
```

## Load the data
Let's revisit the mammal life history data to practice our ggplot skills. The [data](http://esapubs.org/archive/ecol/E084/093/) are from: *S. K. Morgan Ernest. 2003. Life history characteristics of placental non-volant mammals. Ecology 84:3402.*

```r
life_history <- read_csv("data/mammal_lifehistories_v2.csv", na="-999") %>% clean_names()
```

## Bar Plots
Bar plots count the number of observations in a categorical variable. What is the difference between `geom_bar` and `geom_col`? Make two bar plots showing the number of observations for each order using each geom type.


```r
names(life_history)
```

```
##  [1] "order"        "family"       "genus"        "species"      "mass"        
##  [6] "gestation"    "newborn"      "weaning"      "wean_mass"    "afr"         
## [11] "max_life"     "litter_size"  "litters_year"
```

`geom_col`

```r
life_history %>% 
  count(order, sort=T) %>%   #this is the same as arrange
  ggplot(aes(x=order, y=n))+
  geom_col()+
  coord_flip()
```

![](EXAM2_files/figure-html/unnamed-chunk-108-1.png)<!-- -->

`geom_bar`

```r
life_history %>% 
  ggplot(aes(x=order))+
  geom_bar()+
  coord_flip()
```

![](EXAM2_files/figure-html/unnamed-chunk-109-1.png)<!-- -->

Remember that ggplot build plots in layers. These layers can significantly improve the appearance of the plot. What if we wanted a bar plot of the mean mass for each order? Would we use `geom_bar` or `geom_col`?  

```r
life_history %>% 
  group_by(order) %>% 
  summarize(mean_mass=mean(mass, na.rm=T)) %>% 
  ggplot(aes(x=order, y=mean_mass))+
  geom_col()+
  coord_flip()
```

![](EXAM2_files/figure-html/unnamed-chunk-110-1.png)<!-- -->

There are a few problems here. First, the y-axis is in scientific notation. We can fix this by adjusting the options for the session.

```r
options(scipen=999)#cancels scientific notation for the session
```

Next, the y-axis is not on a log scale. We can fix this by adding `scale_y_log10()`.

```r
life_history %>% 
  group_by(order) %>% 
  summarize(mean_mass=mean(mass, na.rm=T)) %>% 
  ggplot(aes(x=order, y=mean_mass))+
  geom_col()+
  coord_flip()+
  scale_y_log10()
```

![](EXAM2_files/figure-html/unnamed-chunk-112-1.png)<!-- -->

Lastly, we can adjust the x-axis labels to make them more readable. We do this using `reorder`.

```r
life_history %>% 
  group_by(order) %>% 
  summarize(mean_mass=mean(mass, na.rm=T)) %>% 
  ggplot(aes(x=reorder(order, mean_mass), y=mean_mass))+ #this allows us to order the x axis by mean_mass
  geom_col()+
  coord_flip()+
  scale_y_log10()
```

![](EXAM2_files/figure-html/unnamed-chunk-113-1.png)<!-- -->

## Scatterplots
Scatter plots allow for comparisons of two continuous variables. Make a scatterplot below that compares gestation time and weaning mass.

```r
life_history %>% 
  ggplot(aes(x=gestation, y=wean_mass))+
  geom_jitter(na.rm=T)+
  scale_y_log10()
```

![](EXAM2_files/figure-html/unnamed-chunk-114-1.png)<!-- -->

## Boxplots
Box plots help us visualize a range of values. So, on the x-axis we typically have something categorical and the y-axis is the range. Let's make a box plot that compares mass across taxonomic orders.

```r
life_history %>% 
  filter(order!="Cetacea") %>% 
  ggplot(aes(x=order, y=mass))+
  geom_boxplot(na.rm=T)+
  coord_flip()
```

![](EXAM2_files/figure-html/unnamed-chunk-115-1.png)<!-- -->
## Aesthetics: Labels
Now that we have practiced scatter plots, bar plots, and box plots we need to learn how to adjust their appearance to suit our needs. Let's start with labeling x and y axes.  

For this exercise, let's use the `ElephantsMF` data. These data are from Phyllis Lee, Stirling University, and are related to Lee, P., et al. (2013), "Enduring consequences of early experiences: 40-year effects on survival and success among African elephants (Loxodonta africana)," Biology Letters, 9: 20130011. [kaggle](https://www.kaggle.com/mostafaelseidy/elephantsmf).

```r
elephants <- read_csv("data/elephantsMF.csv") %>% clean_names()
```

```
## Rows: 288 Columns: 3
## ── Column specification ────────────────────────────────────────────────────────
## Delimiter: ","
## chr (1): Sex
## dbl (2): Age, Height
## 
## ℹ Use `spec()` to retrieve the full column specification for this data.
## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.
```

Make a plot that compares age and height of elephants.

```r
elephants %>% 
  ggplot(aes(x=age, y=height))+
  geom_point()+
  geom_smooth(method = lm, se=F)
```

```
## `geom_smooth()` using formula = 'y ~ x'
```

![](EXAM2_files/figure-html/unnamed-chunk-117-1.png)<!-- -->

The plot looks clean, but it is incomplete. A reader unfamiliar with the data might have a difficult time interpreting the labels. To add custom labels, we use the `labs` command.

```r
elephants %>% 
  ggplot(aes(x=age, y=height)) + 
  geom_point() + 
  geom_smooth(method=lm, se=F)+
  labs(title="Elephant Age vs. Height", #adds a title
       x="Age (years)",
       y="Height (cm)")
```

```
## `geom_smooth()` using formula = 'y ~ x'
```

![](EXAM2_files/figure-html/unnamed-chunk-118-1.png)<!-- -->

We can improve the plot further by adjusting the size and face of the text. We do this using `theme()`. The `rel()` option changes the relative size of the title to keep things consistent. Adding `hjust` allows control of title position.

```r
elephants %>% 
  ggplot(aes(x=age, y=height)) + 
  geom_point() + 
  geom_smooth(method=lm, se=T)+
  labs(title="Elephant Age vs. Height", #adds a title
       x="Age (years)",
       y="Height (cm)")+
  theme(plot.title=element_text(size=rel(1.75), hjust = 0.5))
```

```
## `geom_smooth()` using formula = 'y ~ x'
```

![](EXAM2_files/figure-html/unnamed-chunk-119-1.png)<!-- -->

## Other Aesthetics
There are lots of options for aesthetics. An aesthetic can be assigned to either numeric or categorical data. `fill` is a common grouping option; notice that an appropriate key is displayed when you use one of these options.

```r
elephants %>% 
  ggplot(aes(x=sex, fill=sex))+ #fill is a grouping option
  geom_bar()
```

![](EXAM2_files/figure-html/unnamed-chunk-120-1.png)<!-- -->

`size` adjusts the size of points relative to a continuous variable.

```r
life_history %>% 
  ggplot(aes(x=gestation, y=log10(mass), size=mass))+
  geom_point(na.rm=T)
```

![](EXAM2_files/figure-html/unnamed-chunk-121-1.png)<!-- -->

## Load the libraries

```r
library(tidyverse)
library(janitor)
library(palmerpenguins)
```

## Removes scientific notation

```r
options(scipen=999) #cancels the use of scientific notation for the session
```

## Data
**Database of vertebrate home range sizes.**  
Reference: Tamburello N, Cote IM, Dulvy NK (2015) Energy and the scaling of animal space use. The American Naturalist 186(2):196-211. http://dx.doi.org/10.1086/682070.  
Data: http://datadryad.org/resource/doi:10.5061/dryad.q5j65/1  

```r
homerange <- 
  read_csv("data/Tamburelloetal_HomeRangeDatabase.csv", na = c("", "NA", "\\"))
```

```
## Rows: 569 Columns: 24
## ── Column specification ────────────────────────────────────────────────────────
## Delimiter: ","
## chr (16): taxon, common.name, class, order, family, genus, species, primarym...
## dbl  (8): mean.mass.g, log10.mass, mean.hra.m2, log10.hra, dimension, preyma...
## 
## ℹ Use `spec()` to retrieve the full column specification for this data.
## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.
```

## A few more useful aesthetics
There are many options to create nice plots in ggplot. One useful trick is to store the plot as a new object and then experiment with geom's and aesthetics. Let's setup a plot that compares `log10.mass` and `log10.hra.` Notice that we are not specifying a geom.

```r
p <- homerange %>% 
  ggplot(aes(x= log10.mass, y= log10.hra))
```

Play with point size by adjusting the `size` argument.

```r
p + geom_point(size=2)
```

![](EXAM2_files/figure-html/unnamed-chunk-126-1.png)<!-- -->

We can color the points by a categorical variable.

```r
p + geom_point(aes(color=thermoregulation), size=1.5) #for scatterplots, color is the same as fill in barplots
```

![](EXAM2_files/figure-html/unnamed-chunk-127-1.png)<!-- -->

We can also map shapes to another categorical variable.

```r
p + geom_point(aes(color=thermoregulation, shape=thermoregulation), size=1.5)
```

![](EXAM2_files/figure-html/unnamed-chunk-128-1.png)<!-- -->

## Barplots and multiple variables
At this point you should be comfortable building bar plots that show counts of observations using `geom_bar()`. Last time we explored the `fill` option as a way to bring color to the plot; i.e. we filled by the same variable that we were plotting. What happens when we fill by a different categorical variable?  
Let's start by counting how many observations we have in each taxonomic group.

```r
homerange %>% count(taxon, sort=T)
```

```
## # A tibble: 9 × 2
##   taxon             n
##   <chr>         <int>
## 1 mammals         238
## 2 birds           140
## 3 marine fishes    90
## 4 snakes           41
## 5 river fishes     14
## 6 turtles          14
## 7 tortoises        12
## 8 lizards          11
## 9 lake fishes       9
```

Now let's make a bar plot of these data.

```r
homerange %>% 
  ggplot(aes(taxon))+
  geom_bar()+
  coord_flip()+
  labs(title="Observations by Taxon",
       x="Taxonomic Group")
```

![](EXAM2_files/figure-html/unnamed-chunk-130-1.png)<!-- -->

By specifying `fill=trophic.guild` we build a stacked bar plot that shows the proportion of a given taxonomic group that is an herbivore or carnivore.

```r
homerange %>% 
  ggplot(aes(x=taxon, fill=trophic.guild))+
  geom_bar()+
  coord_flip()+
  labs(title="Observations by Taxon",
       x="Taxonomic Group")
```

![](EXAM2_files/figure-html/unnamed-chunk-131-1.png)<!-- -->

We can also have counts of each trophic guild within taxonomic group shown side-by-side by specifying `position="dodge"`.

```r
homerange %>% 
  ggplot(aes(x=taxon, fill=trophic.guild))+
  geom_bar(position="dodge")+
  coord_flip()+
  labs(title="Observations by Taxon",
       x="Taxonomic Group")
```

![](EXAM2_files/figure-html/unnamed-chunk-132-1.png)<!-- -->

Here is the same plot oriented vertically.

```r
homerange %>% 
  ggplot(aes(x=taxon, fill=trophic.guild))+
  geom_bar(position="dodge")+
  theme(axis.text.x=element_text(angle=15, hjust = 1))+
  labs(title="Observations by Taxon",
       x="Taxonomic Group")
```

![](EXAM2_files/figure-html/unnamed-chunk-133-1.png)<!-- -->

We can also scale all bars to a percentage.

```r
homerange %>% 
  ggplot(aes(x = taxon, fill = trophic.guild))+
  geom_bar(position = position_fill())+ 
  scale_y_continuous(labels = scales::percent)+
  coord_flip()
```

![](EXAM2_files/figure-html/unnamed-chunk-134-1.png)<!-- -->

## Practice
For this practice, let's use the [palmerpenguins](https://allisonhorst.github.io/palmerpenguins/articles/intro.html) data.  

1. Make a bar plot that shows counts of individuals by island. Fill by species, and try both a stacked bar plot and `position="dodge"`.

```r
penguins %>% 
  ggplot(aes(x=island, fill=species))+
  geom_bar()+
  labs(title="Counts of Individuals by Island",
       x="Island",
       fill="Species")
```

![](EXAM2_files/figure-html/unnamed-chunk-135-1.png)<!-- -->


```r
penguins %>% 
  ggplot(aes(x=island, fill=species))+
  geom_bar(position="dodge")+
  labs(title="Counts of Individuals by Island",
       x="Island",
       fill="Species")
```

![](EXAM2_files/figure-html/unnamed-chunk-136-1.png)<!-- -->

2. Make another bar plot that shows the number of individuals by sex on each island?

```r
penguins %>% 
  #filter(sex!="NA") %>% # this removes the NA's
  ggplot(aes(x=island, fill=sex))+
  geom_bar(position="dodge")+
  labs(title="Counts of Individuals by Island",
       x="Island",
       fill="Penguin Species")
```

![](EXAM2_files/figure-html/unnamed-chunk-137-1.png)<!-- -->

## Using `group`
In addition to `fill`, `group` is an aesthetic that accomplishes the same function but does not add color.

Here is a box plot that shows `log10.mass` by taxonomic `class.`

```r
homerange %>% 
  ggplot(aes(x = class, y = log10.mass)) +
  geom_boxplot()
```

![](EXAM2_files/figure-html/unnamed-chunk-138-1.png)<!-- -->

I use `group` to make individual box plots for each taxon within class.

```r
homerange %>% 
  ggplot(aes(x = class, y = log10.mass, group = taxon)) +
  geom_boxplot()
```

![](EXAM2_files/figure-html/unnamed-chunk-139-1.png)<!-- -->

I can also use `fill` to associate the different taxa with a color coded key.

```r
homerange %>% 
  ggplot(aes(x = class, y = log10.mass, fill=taxon)) +
  geom_boxplot(alpha=0.6)
```

![](EXAM2_files/figure-html/unnamed-chunk-140-1.png)<!-- -->


LAB 12
## Install `RColorBrewer`, `paletteer`, and `ggthemes`
`RColorBrewer` and `paletteer` are helpful in selecting palettes of color that work with your data. Please install these now, we will work with them in part 2 of today's lab.

```r
#install.packages("RColorBrewer")
#install.packages("paletteer")
#install.packages("ggthemes")
```

## Load the libraries

```r
library(tidyverse)
library(RColorBrewer)
library(paletteer)
library(janitor)
```


```r
options(scipen=999) #cancels the use of scientific notation for the session
```

## Data
For this tutorial, we will use two data sets.  

[Desert ecology](http://esapubs.org/archive/ecol/E090/118/). The data are from: S. K. Morgan Ernest, Thomas J. Valone, and James H. Brown. 2009. Long-term monitoring and experimental manipulation of a Chihuahuan Desert ecosystem near Portal, Arizona, USA. Ecology 90:1708.  

```r
deserts <- read_csv("data/surveys_complete.csv")
```

[Homerange](http://datadryad.org/resource/doi:10.5061/dryad.q5j65/1). The data are from: Tamburello N, Cote IM, Dulvy NK (2015) Energy and the scaling of animal space use. The American Naturalist 186(2):196-211. http://dx.doi.org/10.1086/682070.  

```r
homerange <- read_csv("data/Tamburelloetal_HomeRangeDatabase.csv", na = c("", "NA", "\\"))
```

## Review
1. Use the `homerange` data to make a boxplot that shows the range of `log10.mass` by taxonomic class.

```r
homerange %>% 
  ggplot(aes(x=class, y=log10.mass))+
  geom_boxplot()
```

![](EXAM2_files/figure-html/unnamed-chunk-146-1.png)<!-- -->

2. Now, add a layer to this plot that fills color for each box by taxonomic class

```r
homerange %>% 
  ggplot(aes(x=class, y=log10.mass, fill=class))+
  geom_boxplot()
```

![](EXAM2_files/figure-html/unnamed-chunk-147-1.png)<!-- -->

3. Use `group` to show the range of `log10.mass` by taxon.

```r
homerange %>% 
  ggplot(aes(x=class, y=log10.mass, group=taxon))+
  geom_boxplot()
```

![](EXAM2_files/figure-html/unnamed-chunk-148-1.png)<!-- -->

4. Use `fill` to show the range of `log10.mass` by taxon with color codes.

```r
homerange %>% 
  ggplot(aes(x=class, y=log10.mass, fill=taxon))+
  geom_boxplot()
```

![](EXAM2_files/figure-html/unnamed-chunk-149-1.png)<!-- -->

## Line plots
Line plots are great when you need to show changes over time. Here we look at the number of samples for species DM and DS over the years represented in the `deserts` data. This takes some careful thought- we want to know how sampling has changed over time for these two species.    

Let's start by making a clear x and y so we know what we are going to plot.

```r
deserts %>% 
  filter(species_id=="DM" | species_id=="DS") %>% 
  mutate(year=as.factor(year)) %>% 
  group_by(year, species_id) %>% 
  summarize(n=n(), .groups = 'keep') %>% 
  pivot_wider(names_from = species_id, values_from = n)
```

```
## # A tibble: 26 × 3
## # Groups:   year [26]
##    year     DM    DS
##    <fct> <int> <int>
##  1 1977    264    98
##  2 1978    389   320
##  3 1979    209   204
##  4 1980    493   346
##  5 1981    559   354
##  6 1982    609   354
##  7 1983    528   280
##  8 1984    396    76
##  9 1985    667    98
## 10 1986    406    88
## # ℹ 16 more rows
```


```r
deserts %>% 
  filter(species_id=="DM" | species_id=="DS") %>% 
  mutate(year=as.factor(year)) %>% 
  group_by(year, species_id) %>% 
  summarize(n=n(), .groups = 'keep') %>% 
  ggplot(aes(x=year, y=n, group=species_id, color=species_id))+
  geom_line()+
  geom_point(shape=3)+ #you can experiment with shapes
  theme(axis.text.x=element_text(angle=60, hjust=1))+
  labs(title="Number of Samples for Species DM & DS",
       x="Year",
       y="n")
```

![](EXAM2_files/figure-html/unnamed-chunk-151-1.png)<!-- -->

## Histograms
Histograms are frequently used by biologists; they show the distribution of continuous variables. As students, you have seen histograms of grade distributions. A histogram `bins` the data and you specify the number of bins that encompass a range of observations. For something like grades, this is easy because the number of bins corresponds to the grades A-F. By default, R uses a formula to calculate the number of bins but some adjustment may be required.  

What does the distribution of body mass look like in the `homerange` data?

```r
homerange %>% 
  ggplot(aes(x = log10.mass)) +
  geom_histogram(bins = 20)+ #we can adjust the number of bins with the bins argument
  labs(title = "Distribution of Body Mass")
```

![](EXAM2_files/figure-html/unnamed-chunk-152-1.png)<!-- -->

Let's play with the colors. This shows all 657 of R's built-in colors. Notice that `color` and `fill` do different things!

```r
grDevices::colors()
```

```
##   [1] "white"                "aliceblue"            "antiquewhite"        
##   [4] "antiquewhite1"        "antiquewhite2"        "antiquewhite3"       
##   [7] "antiquewhite4"        "aquamarine"           "aquamarine1"         
##  [10] "aquamarine2"          "aquamarine3"          "aquamarine4"         
##  [13] "azure"                "azure1"               "azure2"              
##  [16] "azure3"               "azure4"               "beige"               
##  [19] "bisque"               "bisque1"              "bisque2"             
##  [22] "bisque3"              "bisque4"              "black"               
##  [25] "blanchedalmond"       "blue"                 "blue1"               
##  [28] "blue2"                "blue3"                "blue4"               
##  [31] "blueviolet"           "brown"                "brown1"              
##  [34] "brown2"               "brown3"               "brown4"              
##  [37] "burlywood"            "burlywood1"           "burlywood2"          
##  [40] "burlywood3"           "burlywood4"           "cadetblue"           
##  [43] "cadetblue1"           "cadetblue2"           "cadetblue3"          
##  [46] "cadetblue4"           "chartreuse"           "chartreuse1"         
##  [49] "chartreuse2"          "chartreuse3"          "chartreuse4"         
##  [52] "chocolate"            "chocolate1"           "chocolate2"          
##  [55] "chocolate3"           "chocolate4"           "coral"               
##  [58] "coral1"               "coral2"               "coral3"              
##  [61] "coral4"               "cornflowerblue"       "cornsilk"            
##  [64] "cornsilk1"            "cornsilk2"            "cornsilk3"           
##  [67] "cornsilk4"            "cyan"                 "cyan1"               
##  [70] "cyan2"                "cyan3"                "cyan4"               
##  [73] "darkblue"             "darkcyan"             "darkgoldenrod"       
##  [76] "darkgoldenrod1"       "darkgoldenrod2"       "darkgoldenrod3"      
##  [79] "darkgoldenrod4"       "darkgray"             "darkgreen"           
##  [82] "darkgrey"             "darkkhaki"            "darkmagenta"         
##  [85] "darkolivegreen"       "darkolivegreen1"      "darkolivegreen2"     
##  [88] "darkolivegreen3"      "darkolivegreen4"      "darkorange"          
##  [91] "darkorange1"          "darkorange2"          "darkorange3"         
##  [94] "darkorange4"          "darkorchid"           "darkorchid1"         
##  [97] "darkorchid2"          "darkorchid3"          "darkorchid4"         
## [100] "darkred"              "darksalmon"           "darkseagreen"        
## [103] "darkseagreen1"        "darkseagreen2"        "darkseagreen3"       
## [106] "darkseagreen4"        "darkslateblue"        "darkslategray"       
## [109] "darkslategray1"       "darkslategray2"       "darkslategray3"      
## [112] "darkslategray4"       "darkslategrey"        "darkturquoise"       
## [115] "darkviolet"           "deeppink"             "deeppink1"           
## [118] "deeppink2"            "deeppink3"            "deeppink4"           
## [121] "deepskyblue"          "deepskyblue1"         "deepskyblue2"        
## [124] "deepskyblue3"         "deepskyblue4"         "dimgray"             
## [127] "dimgrey"              "dodgerblue"           "dodgerblue1"         
## [130] "dodgerblue2"          "dodgerblue3"          "dodgerblue4"         
## [133] "firebrick"            "firebrick1"           "firebrick2"          
## [136] "firebrick3"           "firebrick4"           "floralwhite"         
## [139] "forestgreen"          "gainsboro"            "ghostwhite"          
## [142] "gold"                 "gold1"                "gold2"               
## [145] "gold3"                "gold4"                "goldenrod"           
## [148] "goldenrod1"           "goldenrod2"           "goldenrod3"          
## [151] "goldenrod4"           "gray"                 "gray0"               
## [154] "gray1"                "gray2"                "gray3"               
## [157] "gray4"                "gray5"                "gray6"               
## [160] "gray7"                "gray8"                "gray9"               
## [163] "gray10"               "gray11"               "gray12"              
## [166] "gray13"               "gray14"               "gray15"              
## [169] "gray16"               "gray17"               "gray18"              
## [172] "gray19"               "gray20"               "gray21"              
## [175] "gray22"               "gray23"               "gray24"              
## [178] "gray25"               "gray26"               "gray27"              
## [181] "gray28"               "gray29"               "gray30"              
## [184] "gray31"               "gray32"               "gray33"              
## [187] "gray34"               "gray35"               "gray36"              
## [190] "gray37"               "gray38"               "gray39"              
## [193] "gray40"               "gray41"               "gray42"              
## [196] "gray43"               "gray44"               "gray45"              
## [199] "gray46"               "gray47"               "gray48"              
## [202] "gray49"               "gray50"               "gray51"              
## [205] "gray52"               "gray53"               "gray54"              
## [208] "gray55"               "gray56"               "gray57"              
## [211] "gray58"               "gray59"               "gray60"              
## [214] "gray61"               "gray62"               "gray63"              
## [217] "gray64"               "gray65"               "gray66"              
## [220] "gray67"               "gray68"               "gray69"              
## [223] "gray70"               "gray71"               "gray72"              
## [226] "gray73"               "gray74"               "gray75"              
## [229] "gray76"               "gray77"               "gray78"              
## [232] "gray79"               "gray80"               "gray81"              
## [235] "gray82"               "gray83"               "gray84"              
## [238] "gray85"               "gray86"               "gray87"              
## [241] "gray88"               "gray89"               "gray90"              
## [244] "gray91"               "gray92"               "gray93"              
## [247] "gray94"               "gray95"               "gray96"              
## [250] "gray97"               "gray98"               "gray99"              
## [253] "gray100"              "green"                "green1"              
## [256] "green2"               "green3"               "green4"              
## [259] "greenyellow"          "grey"                 "grey0"               
## [262] "grey1"                "grey2"                "grey3"               
## [265] "grey4"                "grey5"                "grey6"               
## [268] "grey7"                "grey8"                "grey9"               
## [271] "grey10"               "grey11"               "grey12"              
## [274] "grey13"               "grey14"               "grey15"              
## [277] "grey16"               "grey17"               "grey18"              
## [280] "grey19"               "grey20"               "grey21"              
## [283] "grey22"               "grey23"               "grey24"              
## [286] "grey25"               "grey26"               "grey27"              
## [289] "grey28"               "grey29"               "grey30"              
## [292] "grey31"               "grey32"               "grey33"              
## [295] "grey34"               "grey35"               "grey36"              
## [298] "grey37"               "grey38"               "grey39"              
## [301] "grey40"               "grey41"               "grey42"              
## [304] "grey43"               "grey44"               "grey45"              
## [307] "grey46"               "grey47"               "grey48"              
## [310] "grey49"               "grey50"               "grey51"              
## [313] "grey52"               "grey53"               "grey54"              
## [316] "grey55"               "grey56"               "grey57"              
## [319] "grey58"               "grey59"               "grey60"              
## [322] "grey61"               "grey62"               "grey63"              
## [325] "grey64"               "grey65"               "grey66"              
## [328] "grey67"               "grey68"               "grey69"              
## [331] "grey70"               "grey71"               "grey72"              
## [334] "grey73"               "grey74"               "grey75"              
## [337] "grey76"               "grey77"               "grey78"              
## [340] "grey79"               "grey80"               "grey81"              
## [343] "grey82"               "grey83"               "grey84"              
## [346] "grey85"               "grey86"               "grey87"              
## [349] "grey88"               "grey89"               "grey90"              
## [352] "grey91"               "grey92"               "grey93"              
## [355] "grey94"               "grey95"               "grey96"              
## [358] "grey97"               "grey98"               "grey99"              
## [361] "grey100"              "honeydew"             "honeydew1"           
## [364] "honeydew2"            "honeydew3"            "honeydew4"           
## [367] "hotpink"              "hotpink1"             "hotpink2"            
## [370] "hotpink3"             "hotpink4"             "indianred"           
## [373] "indianred1"           "indianred2"           "indianred3"          
## [376] "indianred4"           "ivory"                "ivory1"              
## [379] "ivory2"               "ivory3"               "ivory4"              
## [382] "khaki"                "khaki1"               "khaki2"              
## [385] "khaki3"               "khaki4"               "lavender"            
## [388] "lavenderblush"        "lavenderblush1"       "lavenderblush2"      
## [391] "lavenderblush3"       "lavenderblush4"       "lawngreen"           
## [394] "lemonchiffon"         "lemonchiffon1"        "lemonchiffon2"       
## [397] "lemonchiffon3"        "lemonchiffon4"        "lightblue"           
## [400] "lightblue1"           "lightblue2"           "lightblue3"          
## [403] "lightblue4"           "lightcoral"           "lightcyan"           
## [406] "lightcyan1"           "lightcyan2"           "lightcyan3"          
## [409] "lightcyan4"           "lightgoldenrod"       "lightgoldenrod1"     
## [412] "lightgoldenrod2"      "lightgoldenrod3"      "lightgoldenrod4"     
## [415] "lightgoldenrodyellow" "lightgray"            "lightgreen"          
## [418] "lightgrey"            "lightpink"            "lightpink1"          
## [421] "lightpink2"           "lightpink3"           "lightpink4"          
## [424] "lightsalmon"          "lightsalmon1"         "lightsalmon2"        
## [427] "lightsalmon3"         "lightsalmon4"         "lightseagreen"       
## [430] "lightskyblue"         "lightskyblue1"        "lightskyblue2"       
## [433] "lightskyblue3"        "lightskyblue4"        "lightslateblue"      
## [436] "lightslategray"       "lightslategrey"       "lightsteelblue"      
## [439] "lightsteelblue1"      "lightsteelblue2"      "lightsteelblue3"     
## [442] "lightsteelblue4"      "lightyellow"          "lightyellow1"        
## [445] "lightyellow2"         "lightyellow3"         "lightyellow4"        
## [448] "limegreen"            "linen"                "magenta"             
## [451] "magenta1"             "magenta2"             "magenta3"            
## [454] "magenta4"             "maroon"               "maroon1"             
## [457] "maroon2"              "maroon3"              "maroon4"             
## [460] "mediumaquamarine"     "mediumblue"           "mediumorchid"        
## [463] "mediumorchid1"        "mediumorchid2"        "mediumorchid3"       
## [466] "mediumorchid4"        "mediumpurple"         "mediumpurple1"       
## [469] "mediumpurple2"        "mediumpurple3"        "mediumpurple4"       
## [472] "mediumseagreen"       "mediumslateblue"      "mediumspringgreen"   
## [475] "mediumturquoise"      "mediumvioletred"      "midnightblue"        
## [478] "mintcream"            "mistyrose"            "mistyrose1"          
## [481] "mistyrose2"           "mistyrose3"           "mistyrose4"          
## [484] "moccasin"             "navajowhite"          "navajowhite1"        
## [487] "navajowhite2"         "navajowhite3"         "navajowhite4"        
## [490] "navy"                 "navyblue"             "oldlace"             
## [493] "olivedrab"            "olivedrab1"           "olivedrab2"          
## [496] "olivedrab3"           "olivedrab4"           "orange"              
## [499] "orange1"              "orange2"              "orange3"             
## [502] "orange4"              "orangered"            "orangered1"          
## [505] "orangered2"           "orangered3"           "orangered4"          
## [508] "orchid"               "orchid1"              "orchid2"             
## [511] "orchid3"              "orchid4"              "palegoldenrod"       
## [514] "palegreen"            "palegreen1"           "palegreen2"          
## [517] "palegreen3"           "palegreen4"           "paleturquoise"       
## [520] "paleturquoise1"       "paleturquoise2"       "paleturquoise3"      
## [523] "paleturquoise4"       "palevioletred"        "palevioletred1"      
## [526] "palevioletred2"       "palevioletred3"       "palevioletred4"      
## [529] "papayawhip"           "peachpuff"            "peachpuff1"          
## [532] "peachpuff2"           "peachpuff3"           "peachpuff4"          
## [535] "peru"                 "pink"                 "pink1"               
## [538] "pink2"                "pink3"                "pink4"               
## [541] "plum"                 "plum1"                "plum2"               
## [544] "plum3"                "plum4"                "powderblue"          
## [547] "purple"               "purple1"              "purple2"             
## [550] "purple3"              "purple4"              "red"                 
## [553] "red1"                 "red2"                 "red3"                
## [556] "red4"                 "rosybrown"            "rosybrown1"          
## [559] "rosybrown2"           "rosybrown3"           "rosybrown4"          
## [562] "royalblue"            "royalblue1"           "royalblue2"          
## [565] "royalblue3"           "royalblue4"           "saddlebrown"         
## [568] "salmon"               "salmon1"              "salmon2"             
## [571] "salmon3"              "salmon4"              "sandybrown"          
## [574] "seagreen"             "seagreen1"            "seagreen2"           
## [577] "seagreen3"            "seagreen4"            "seashell"            
## [580] "seashell1"            "seashell2"            "seashell3"           
## [583] "seashell4"            "sienna"               "sienna1"             
## [586] "sienna2"              "sienna3"              "sienna4"             
## [589] "skyblue"              "skyblue1"             "skyblue2"            
## [592] "skyblue3"             "skyblue4"             "slateblue"           
## [595] "slateblue1"           "slateblue2"           "slateblue3"          
## [598] "slateblue4"           "slategray"            "slategray1"          
## [601] "slategray2"           "slategray3"           "slategray4"          
## [604] "slategrey"            "snow"                 "snow1"               
## [607] "snow2"                "snow3"                "snow4"               
## [610] "springgreen"          "springgreen1"         "springgreen2"        
## [613] "springgreen3"         "springgreen4"         "steelblue"           
## [616] "steelblue1"           "steelblue2"           "steelblue3"          
## [619] "steelblue4"           "tan"                  "tan1"                
## [622] "tan2"                 "tan3"                 "tan4"                
## [625] "thistle"              "thistle1"             "thistle2"            
## [628] "thistle3"             "thistle4"             "tomato"              
## [631] "tomato1"              "tomato2"              "tomato3"             
## [634] "tomato4"              "turquoise"            "turquoise1"          
## [637] "turquoise2"           "turquoise3"           "turquoise4"          
## [640] "violet"               "violetred"            "violetred1"          
## [643] "violetred2"           "violetred3"           "violetred4"          
## [646] "wheat"                "wheat1"               "wheat2"              
## [649] "wheat3"               "wheat4"               "whitesmoke"          
## [652] "yellow"               "yellow1"              "yellow2"             
## [655] "yellow3"              "yellow4"              "yellowgreen"
```

Let's rebuild the histogram, but this time we will specify the color and fill. Do a little experimentation on your own with the different colors.

```r
homerange %>% 
  ggplot(aes(x = log10.mass)) +
  geom_histogram(color = "black", fill = "violetred4", bins=10)+
  labs(title = "Distribution of Body Mass")
```

![](EXAM2_files/figure-html/unnamed-chunk-154-1.png)<!-- -->

## Density plots  
Density plots are similar to histograms but they use a smoothing function to make the distribution more even and clean looking. They do not use bins.

```r
homerange %>% 
  ggplot(aes(x = log10.mass)) +
  geom_density(fill="deepskyblue4", alpha  =0.4, color = "black")+ #alpha is the transparency
  labs(title = "Distribution of Body Mass")
```

![](EXAM2_files/figure-html/unnamed-chunk-155-1.png)<!-- -->

I like to see both the histogram and the density curve so I often plot them together. Note that I assign the density plot a different color.

```r
homerange %>% 
  ggplot(aes(x=log10.mass)) +
  geom_histogram(aes(y = after_stat(density)), fill = "deepskyblue4", alpha = 0.4, color = "black")+
  geom_density(color = "red")+
  labs(title = "Distribution of Body Mass")
```

![](EXAM2_files/figure-html/unnamed-chunk-156-1.png)<!-- -->

## Practice
1. Make a histogram of `log10.hra`. Make sure to add a title.

```r
homerange %>% 
  ggplot(aes(x=log10.hra))+ #only need to specify an x
  geom_histogram(color="black", fill="violetred4", alpha=0.3)+
  labs(title="Distribution of Homerange")
```

```
## `stat_bin()` using `bins = 30`. Pick better value with `binwidth`.
```

![](EXAM2_files/figure-html/unnamed-chunk-157-1.png)<!-- -->

2. Now plot the same variable using `geom_density()`.

```r
homerange %>% 
  ggplot(aes(x=log10.hra))+ #only need to specify an x
  geom_density(color="black", fill="violetred4", alpha=0.3)+
  labs(title="Distribution of Homerange")
```

![](EXAM2_files/figure-html/unnamed-chunk-158-1.png)<!-- -->

3. Combine them both!

```r
homerange %>% 
  ggplot(aes(x=log10.hra))+
  geom_histogram(aes(y=after_stat(density)), fill="violetred4", alpha=0.3, color="black")+
  geom_density(color="orange")+
  labs(title="Distribution of Homerange")
```

```
## `stat_bin()` using `bins = 30`. Pick better value with `binwidth`.
```

![](EXAM2_files/figure-html/unnamed-chunk-159-1.png)<!-- -->

## Create Categories with mutate and case_when()
`case_when()` is a very handy function from `dplyr` which allows us to calculate a new variable from other variables. We use `case_when()` within `mutate()` to do this.`case_when()` allows us to specify multiple conditions. Let's reclassify the body mass variable into a new factor variable with small, medium, and large animals. In this case, we are making a continuous variable into a categorical variable.  

```r
glimpse(homerange)
```

```
## Rows: 569
## Columns: 24
## $ taxon                      <chr> "lake fishes", "river fishes", "river fishe…
## $ common.name                <chr> "american eel", "blacktail redhorse", "cent…
## $ class                      <chr> "actinopterygii", "actinopterygii", "actino…
## $ order                      <chr> "anguilliformes", "cypriniformes", "cyprini…
## $ family                     <chr> "anguillidae", "catostomidae", "cyprinidae"…
## $ genus                      <chr> "anguilla", "moxostoma", "campostoma", "cli…
## $ species                    <chr> "rostrata", "poecilura", "anomalum", "fundu…
## $ primarymethod              <chr> "telemetry", "mark-recapture", "mark-recapt…
## $ N                          <chr> "16", NA, "20", "26", "17", "5", "2", "2", …
## $ mean.mass.g                <dbl> 887.00, 562.00, 34.00, 4.00, 4.00, 3525.00,…
## $ log10.mass                 <dbl> 2.9479236, 2.7497363, 1.5314789, 0.6020600,…
## $ alternative.mass.reference <chr> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA,…
## $ mean.hra.m2                <dbl> 282750.00, 282.10, 116.11, 125.50, 87.10, 3…
## $ log10.hra                  <dbl> 5.4514026, 2.4504031, 2.0648696, 2.0986437,…
## $ hra.reference              <chr> "Minns, C. K. 1995. Allometry of home range…
## $ realm                      <chr> "aquatic", "aquatic", "aquatic", "aquatic",…
## $ thermoregulation           <chr> "ectotherm", "ectotherm", "ectotherm", "ect…
## $ locomotion                 <chr> "swimming", "swimming", "swimming", "swimmi…
## $ trophic.guild              <chr> "carnivore", "carnivore", "carnivore", "car…
## $ dimension                  <dbl> 3, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 3, 3…
## $ preymass                   <dbl> NA, NA, NA, NA, NA, NA, 1.39, NA, NA, NA, N…
## $ log10.preymass             <dbl> NA, NA, NA, NA, NA, NA, 0.1430148, NA, NA, …
## $ PPMR                       <dbl> NA, NA, NA, NA, NA, NA, 530, NA, NA, NA, NA…
## $ prey.size.reference        <chr> NA, NA, NA, NA, NA, NA, "Brose U, et al. 20…
```


```r
homerange %>% 
  select(log10.mass) %>% 
  summarize(min=min(log10.mass),
            max=max(log10.mass))
```

```
## # A tibble: 1 × 2
##      min   max
##    <dbl> <dbl>
## 1 -0.658  6.60
```

```r
summary(homerange$log10.mass)
```

```
##    Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
## -0.6576  1.6990  2.5185  2.5947  3.3324  6.6021
```


```r
homerange %>% 
  mutate(mass_category=case_when(log10.mass<=1.75 ~ "small",
                                 log10.mass>1.75 & log10.mass<=2.75 ~ "medium",
                                 log10.mass>2.75 ~ "large"))
```

```
## # A tibble: 569 × 25
##    taxon        common.name class order family genus species primarymethod N    
##    <chr>        <chr>       <chr> <chr> <chr>  <chr> <chr>   <chr>         <chr>
##  1 lake fishes  american e… acti… angu… angui… angu… rostra… telemetry     16   
##  2 river fishes blacktail … acti… cypr… catos… moxo… poecil… mark-recaptu… <NA> 
##  3 river fishes central st… acti… cypr… cypri… camp… anomal… mark-recaptu… 20   
##  4 river fishes rosyside d… acti… cypr… cypri… clin… fundul… mark-recaptu… 26   
##  5 river fishes longnose d… acti… cypr… cypri… rhin… catara… mark-recaptu… 17   
##  6 river fishes muskellunge acti… esoc… esoci… esox  masqui… telemetry     5    
##  7 marine fish… pollack     acti… gadi… gadid… poll… pollac… telemetry     2    
##  8 marine fish… saithe      acti… gadi… gadid… poll… virens  telemetry     2    
##  9 marine fish… lined surg… acti… perc… acant… acan… lineat… direct obser… <NA> 
## 10 marine fish… orangespin… acti… perc… acant… naso  litura… telemetry     8    
## # ℹ 559 more rows
## # ℹ 16 more variables: mean.mass.g <dbl>, log10.mass <dbl>,
## #   alternative.mass.reference <chr>, mean.hra.m2 <dbl>, log10.hra <dbl>,
## #   hra.reference <chr>, realm <chr>, thermoregulation <chr>, locomotion <chr>,
## #   trophic.guild <chr>, dimension <dbl>, preymass <dbl>, log10.preymass <dbl>,
## #   PPMR <dbl>, prey.size.reference <chr>, mass_category <chr>
```


Here we check how the newly created body mass categories compare across `trophic.guild`. 

```r
homerange %>% 
  mutate(mass_category=case_when(log10.mass<=1.75 ~ "small",
                                 log10.mass>1.75 & log10.mass<=2.75 ~ "medium",
                                 log10.mass>2.75 ~ "large")) %>% 
  ggplot(aes(x=mass_category, fill=trophic.guild))+
  geom_bar(position="dodge")
```

![](EXAM2_files/figure-html/unnamed-chunk-164-1.png)<!-- -->

## Practice
1. Use case_when() to make a new column `range_category` that breaks down `log10.hra` into very small, small, medium, and large classes based on quartile.

```r
summary(homerange$log10.hra)
```

```
##    Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
##  -1.523   3.653   4.595   4.709   6.016   9.550
```


```r
library(gtools)
#install.packages("gtools")
```


```r
quartiles <- quantcut(homerange$log10.hra)
table(quartiles)
```

```
## quartiles
## [-1.52,3.65]  (3.65,4.59]  (4.59,6.02]  (6.02,9.55] 
##          143          142          142          142
```


```r
homerange %>% 
  mutate(range_category=case_when(log10.hra<3.65 ~ "very small",
                                  log10.hra>=3.65 & log10.hra<=4.59 ~ "small",
                                  log10.hra>4.59 & log10.hra<=6.02 ~ "medium",
                                  log10.hra>=6.02 ~ "large"))
```

```
## # A tibble: 569 × 25
##    taxon        common.name class order family genus species primarymethod N    
##    <chr>        <chr>       <chr> <chr> <chr>  <chr> <chr>   <chr>         <chr>
##  1 lake fishes  american e… acti… angu… angui… angu… rostra… telemetry     16   
##  2 river fishes blacktail … acti… cypr… catos… moxo… poecil… mark-recaptu… <NA> 
##  3 river fishes central st… acti… cypr… cypri… camp… anomal… mark-recaptu… 20   
##  4 river fishes rosyside d… acti… cypr… cypri… clin… fundul… mark-recaptu… 26   
##  5 river fishes longnose d… acti… cypr… cypri… rhin… catara… mark-recaptu… 17   
##  6 river fishes muskellunge acti… esoc… esoci… esox  masqui… telemetry     5    
##  7 marine fish… pollack     acti… gadi… gadid… poll… pollac… telemetry     2    
##  8 marine fish… saithe      acti… gadi… gadid… poll… virens  telemetry     2    
##  9 marine fish… lined surg… acti… perc… acant… acan… lineat… direct obser… <NA> 
## 10 marine fish… orangespin… acti… perc… acant… naso  litura… telemetry     8    
## # ℹ 559 more rows
## # ℹ 16 more variables: mean.mass.g <dbl>, log10.mass <dbl>,
## #   alternative.mass.reference <chr>, mean.hra.m2 <dbl>, log10.hra <dbl>,
## #   hra.reference <chr>, realm <chr>, thermoregulation <chr>, locomotion <chr>,
## #   trophic.guild <chr>, dimension <dbl>, preymass <dbl>, log10.preymass <dbl>,
## #   PPMR <dbl>, prey.size.reference <chr>, range_category <chr>
```




2. Make a plot that shows how many and which taxonomic classes are represented in each `range_category`.   

```r
homerange %>% 
  mutate(range_category=case_when(log10.hra<3.65 ~ "very small",
                                  log10.hra>=3.65 & log10.hra<=4.59 ~ "small",
                                  log10.hra>4.59 & log10.hra<=6.02 ~ "medium",
                                  log10.hra>=6.02 ~ "large")) %>% 
  ggplot(aes(x=range_category, fill=class))+
  geom_bar(position="dodge", alpha=0.6, color="black")
```

![](EXAM2_files/figure-html/unnamed-chunk-169-1.png)<!-- -->

3. Isolate the small `range_category` and plot the range of `log10.mass` by taxonomic class.

```r
homerange %>% 
  mutate(range_category=case_when(log10.hra<3.65 ~ "very small",
                                  log10.hra>=3.65 & log10.hra<=4.59 ~ "small",
                                  log10.hra>4.59 & log10.hra<=6.02 ~ "medium",
                                  log10.hra>=6.02 ~ "large")) %>% 
  filter(range_category=="small") %>% 
  ggplot(aes(x=class, y=log10.mass, fill=class))+
  geom_boxplot()
```

![](EXAM2_files/figure-html/unnamed-chunk-170-1.png)<!-- -->

## Load the libraries

```r
library(tidyverse)
library(RColorBrewer)
library(paletteer)
library(janitor)
```


```r
options(scipen=999) #cancels the use of scientific notation for the session
```

## Data
For this tutorial, we will use:  
[Homerange](http://datadryad.org/resource/doi:10.5061/dryad.q5j65/1). The data are from: Tamburello N, Cote IM, Dulvy NK (2015) Energy and the scaling of animal space use. The American Naturalist 186(2):196-211. http://dx.doi.org/10.1086/682070.  

```r
homerange <- read_csv("data/Tamburelloetal_HomeRangeDatabase.csv", na = c("", "NA", "\\"))
```

## ggplot themes
There are many options to change the theme of your plots within ggplot. Have a look [here]https://ggplot2.tidyverse.org/reference/ggtheme.html) for a list of the themes.  

Let's start by building a simple barplot.  

```r
p <- homerange %>% 
  ggplot(aes(x=taxon, fill=trophic.guild))+
  geom_bar(na.rm=T, position="dodge")
```

Have a look at the `linedraw` theme; I am adding it as another layer.

```r
p + theme_light()+
  theme(axis.text.x = element_text(angle = 60, hjust=1))+
  labs(title = "Observations by Taxon in Homerange Data",
       x = NULL,
       y= "n",
       fill= "Trophic Guild")
```

![](EXAM2_files/figure-html/unnamed-chunk-175-1.png)<!-- -->

## Practice
1. Build a scatterplot that compares `log10.mass` and `log10.preymass`. Color the points by `taxon`. Store this plot as object `q`.

```r
q <- homerange %>% 
  ggplot(aes(x=log10.mass, y=log10.preymass, color=taxon))+
  geom_point(na.rm=T)
```

2. Use plot `q` but add the classic theme.

```r
q+theme_minimal()
```

![](EXAM2_files/figure-html/unnamed-chunk-177-1.png)<!-- -->

## Legends
There are lots of options to manipulate legends. Have a look [here](http://www.sthda.com/english/wiki/ggplot2-legend-easy-steps-to-change-the-position-and-the-appearance-of-a-graph-legend-in-r-software).

```r
p+theme_linedraw()+
  theme(legend.position = "left",
        axis.text.x = element_text(angle = 60, hjust=1))+
  labs(title = "Observations by Taxon in Homerange Data",
       x = NULL, #removes the label from the x axis
       y= "n",
       fill= "Trophic Guild")
```

![](EXAM2_files/figure-html/unnamed-chunk-178-1.png)<!-- -->

## Not enough? Try using `ggthemes`
There are many packages that include additional themes, one of which is [ggthemes](https://yutannihilation.github.io/allYourFigureAreBelongToUs/). Some of these are nice because they are designed to mimic the look of popular publications.

```r
#install.packages("ggthemes")
library(ggthemes)
```

Here is a list of the `ggthemes`

```r
ls("package:ggthemes")[grepl("theme_", ls("package:ggthemes"))]
```

```
##  [1] "theme_base"            "theme_calc"            "theme_clean"          
##  [4] "theme_economist"       "theme_economist_white" "theme_excel"          
##  [7] "theme_excel_new"       "theme_few"             "theme_fivethirtyeight"
## [10] "theme_foundation"      "theme_gdocs"           "theme_hc"             
## [13] "theme_igray"           "theme_map"             "theme_pander"         
## [16] "theme_par"             "theme_solarized"       "theme_solarized_2"    
## [19] "theme_solid"           "theme_stata"           "theme_tufte"          
## [22] "theme_wsj"
```


```r
p + 
  theme_few()+
  theme(legend.position = "bottom",
        axis.text.x = element_text(angle = 60, hjust=1))+
  labs(title = "Observations by Taxon in Homerange Data",
       x = NULL,
       y= "n",
       fill= "Trophic Guild")
```

![](EXAM2_files/figure-html/unnamed-chunk-181-1.png)<!-- -->

## Practice
1. Use plot `q` and try the `theme_economist()` theme.

```r
p + 
  theme_economist()+
  theme(legend.position = "bottom",
        axis.text.x = element_text(angle = 60, hjust=0))+
  labs(title = "Observations by Taxon in Homerange Data",
       x = NULL,
       y= "n",
       fill= "Trophic Guild")
```

![](EXAM2_files/figure-html/unnamed-chunk-182-1.png)<!-- -->

## RColorBrewer
The default colors used by ggplot are often uninspiring. They don't make plots pop out in presentations or publications, and you may want to use a customized palette to make things visually consistent.  

Access the help for `RColorBrewer`.

```r
?RColorBrewer
```

The thing to notice is that there are three different color palettes: 1) sequential, 2) diverging, and 3) qualitative. Within each of these there are several selections. You can bring up the colors by using `display.brewer.pal()`. Specify the number of colors that you want and the palette name.

```r
display.brewer.pal(5,"PuBuGn") #sequential palette
```

![](EXAM2_files/figure-html/unnamed-chunk-184-1.png)<!-- -->

The [R Color Brewer](http://colorbrewer2.org/#type=sequential&scheme=BuGn&n=3) website is very helpful for getting an idea of the color palettes. To make things easy, use these two guidelines:

+`scale_colour_brewer()` is for points  
+`scale_fill_brewer()` is for fills  

Here I chose the `Paired` palette. Take a moment and experiment with other options.

```r
p+scale_fill_brewer(palette = "PuBuGn")+
  theme(legend.position = "bottom",
        axis.text.x = element_text(angle = 60, hjust=1))+
  labs(title = "Observations by Taxon in Homerange Data",
       x = NULL,
       y= "n",
       fill= "Trophic Guild")
```

![](EXAM2_files/figure-html/unnamed-chunk-185-1.png)<!-- -->

## Practice
1. Keep using the `same `q` plot, but test out one of R Color Brewer palettes.

```r
q+scale_colour_brewer(palette = "PuBuGn")
```

![](EXAM2_files/figure-html/unnamed-chunk-186-1.png)<!-- -->

## Manually Setting Colors
You can also use `paleteer` to build a custom palette for consistency. To access the `paleteer` collection, I add it to a new object.

```r
colors <- paletteer::palettes_d_names
```

Now we can display the palettes. Assign the palette to `my_palette` and then build this base R bar plot. There are a lot of options; `paleteer` is a collection of popular palettes. I really like the [`ggsci` package] (https://cran.r-project.org/web/packages/ggsci/vignettes/ggsci.html)

```r
my_palette <- paletteer_d("ggsci::nrc_npg") #store your palette
```


```r
barplot(rep(1,6), axes=FALSE, col=my_palette) # show your palette
```

![](EXAM2_files/figure-html/unnamed-chunk-189-1.png)<!-- -->

Now we just identify `my_palette` as part of `scale_fill_manual()`

```r
p+scale_fill_manual(values=my_palette)+
  theme(legend.position = "bottom",
        axis.text.x = element_text(angle = 60, hjust=1))+
  labs(title = "Observations by Taxon in Homerange Data",
       x = NULL,
       y= "n",
       fill= "Trophic Guild")
```

![](EXAM2_files/figure-html/unnamed-chunk-190-1.png)<!-- -->

## Practice
1. Keep using the `same `q` plot, but test out one of `paleteer` themes.

```r
q+theme_classic()+scale_colour_manual(values=my_palette)
```

![](EXAM2_files/figure-html/unnamed-chunk-191-1.png)<!-- -->

## Faceting
[Faceting](https://ggplot2-book.org/facet.html) is one of the amazing features of ggplot. It allows us to make multi-panel plots for easy comparison.  Here is a boxplot that shows the range of log10.mass by taxon.

```r
homerange %>% 
  ggplot(aes(x=taxon, y=log10.mass))+
  geom_boxplot()+
  theme(axis.text.x = element_text(angle = 60, hjust=1))
```

![](EXAM2_files/figure-html/unnamed-chunk-192-1.png)<!-- -->

There are other categorical variables that might be interesting to overlay. `facet_wrap()` makes a ribbon of panels by a specified categorical variable and allows you to control how you want them arranged.  

```r
homerange %>% 
  ggplot(aes(x=taxon, y=log10.mass))+
  geom_boxplot()+
  facet_wrap(~trophic.guild)+
  theme(axis.text.x = element_text(angle = 60, hjust=1))
```

![](EXAM2_files/figure-html/unnamed-chunk-193-1.png)<!-- -->

`facet_grid()` allows control over the faceted variable; it can be arranged in rows or columns. rows~columns. 

```r
homerange %>% 
  ggplot(aes(x=taxon, y=log10.mass))+
  geom_boxplot()+
  facet_grid(.~trophic.guild)+
  theme(axis.text.x = element_text(angle = 60, hjust=1))
```

![](EXAM2_files/figure-html/unnamed-chunk-194-1.png)<!-- -->

`facet_grid()` will also allow the comparison of two categorical variables, just remember a~b where a is rows and b is columns.  

```r
homerange %>% 
  ggplot(aes(x=taxon, y=log10.mass))+
  geom_boxplot()+
  facet_grid(trophic.guild~thermoregulation)+
  theme(axis.text.x = element_text(angle = 60, hjust=1))
```

![](EXAM2_files/figure-html/unnamed-chunk-195-1.png)<!-- -->
