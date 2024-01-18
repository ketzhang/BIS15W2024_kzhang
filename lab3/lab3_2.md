---
title: "Importing Data Frames"
date: "2024-01-18"
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
1. Import .csv files as data frames using `read_csv()`.  
2. Use summary functions to explore the dimensions, structure, and contents of a data frame.  
3. Use the `select()` command of dplyr to sort data frames.  

## Review
At this point, you should have familiarity in RStudio, GitHub, and basic operations in R. You understand how to do arithmetic, assign values to objects, and work with vectors, data matrices, and data frames. If you are confused or need some extra help, please [email me](mailto: jmledford@ucdavis.edu).  

## Load the tidyverse

```r
library("tidyverse")
```

## Data Frames
For the remainder of the course, we will work exclusively with data frames. Recall that data frames store multiple classes of data. Last time, you were shown how to build data frames using the `data.frame()` command. However, scientists often make their data available as supplementary material associated with a publication. This is excellent scientific practice as it insures repeatability by showing exactly how analyses were performed. As data scientists, we capitalize on this by importing data directly into R.  

## Importing Data
R allows us to import a wide variety of data types. The most common type of file is a .csv file which stands for comma separated values. Spreadsheets are often developed in Excel then saved as .csv files for use in R. There are packages that allow you to open excel files and many other formats directly but .csv is the most common.  

An opinionated word about excel. It is fine to use excel for data entry and basic analysis. But, it often adds formatting that makes excel files difficult to work with in any program besides excel. R can read excel files, but I know of no R programmers that routinely use them. Instead they save copies of their excel files as .csv which strips away formatting but makes them easier to use in a variety of programs. We won't work with excel files in BIS 15L, but we will learn to import them.  

To import any file, first make sure that you are in the correct working directory. If you are not in the correct directory, R will not "see" the file.

```r
getwd()
```

```
## [1] "/Users/kzhang09/Desktop/BIS15W2024_kzhang/lab3"
```

## Load the data
Here we open a .csv file. Since we are using the tidyverse, we open the file using `read_csv()`. `readr` is included in the tidyverse set of packages. We specify the package and function with the `::` symbol. This becomes important if you have multiple packages loaded that contain functions with the same name.  

In the previous part of the lab, you exported a `.csv` of hot springs data. Let's try to reload that `.csv`.  

```r
hot_springs <- read_csv("hsprings_data.csv")
```

```
## Rows: 9 Columns: 4
## ── Column specification ────────────────────────────────────────────────────────
## Delimiter: ","
## chr (2): scientist, spring
## dbl (2): temp_C, depth_ft
## 
## ℹ Use `spec()` to retrieve the full column specification for this data.
## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.
```

Use the `str()` function to get an idea of the data structure of `hot_springs`.  

```r
str(hot_springs)
```

```
## spc_tbl_ [9 × 4] (S3: spec_tbl_df/tbl_df/tbl/data.frame)
##  $ temp_C   : num [1:9] 36.2 35.4 35.3 35.1 35.4 ...
##  $ scientist: chr [1:9] "Jill" "Susan" "Steve" "Jill" ...
##  $ spring   : chr [1:9] "Buckeye" "Buckeye" "Buckeye" "Benton" ...
##  $ depth_ft : num [1:9] 4.15 4.13 4.12 3.21 3.23 3.2 5.67 5.65 5.66
##  - attr(*, "spec")=
##   .. cols(
##   ..   temp_C = col_double(),
##   ..   scientist = col_character(),
##   ..   spring = col_character(),
##   ..   depth_ft = col_double()
##   .. )
##  - attr(*, "problems")=<externalptr>
```

What is the class of the scientist column? Change it to factor and then show the levels of that factor.  

```r
class(hot_springs$scientist)
```

```
## [1] "character"
```
replace scientst in hot spring database but make it into a factor, factor is a repeat set of type

```r
hot_springs$scientist <- as.factor(hot_springs$scientist)
hot_springs$spring <- as.factor(hot_springs$spring)
```

we use factor because we are measuring different springs

```r
levels(hot_springs$scientist)
```

```
## [1] "Jill"  "Steve" "Susan"
```

```r
levels(hot_springs$spring)
```

```
## [1] "Benton"     "Buckeye"    "Travertine"
```

## Practice
1. In your lab 3 folder there is another folder titled `data`. Inside the `data` folder there is a `.csv` titled `Gaeta_etal_CLC_data.csv`. Open this data and store them as an object called `fish`.  

The data are from: Gaeta J., G. Sass, S. Carpenter. 2012. Biocomplexity at North Temperate Lakes LTER: Coordinated Field Studies: Large Mouth Bass Growth 2006. Environmental Data Initiative.  [link](https://portal.edirepository.org/nis/mapbrowse?scope=knb-lter-ntl&identifier=267)  

```r
fish <- read_csv("data/Gaeta_etal_CLC_data.csv")
```

```
## Rows: 4033 Columns: 6
## ── Column specification ────────────────────────────────────────────────────────
## Delimiter: ","
## chr (2): lakeid, annnumber
## dbl (4): fish_id, length, radii_length_mm, scalelength
## 
## ℹ Use `spec()` to retrieve the full column specification for this data.
## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.
```

2. What is the structure of these data?

```r
str(fish)
```

```
## spc_tbl_ [4,033 × 6] (S3: spec_tbl_df/tbl_df/tbl/data.frame)
##  $ lakeid         : chr [1:4033] "AL" "AL" "AL" "AL" ...
##  $ fish_id        : num [1:4033] 299 299 299 300 300 300 300 301 301 301 ...
##  $ annnumber      : chr [1:4033] "EDGE" "2" "1" "EDGE" ...
##  $ length         : num [1:4033] 167 167 167 175 175 175 175 194 194 194 ...
##  $ radii_length_mm: num [1:4033] 2.7 2.04 1.31 3.02 2.67 ...
##  $ scalelength    : num [1:4033] 2.7 2.7 2.7 3.02 3.02 ...
##  - attr(*, "spec")=
##   .. cols(
##   ..   lakeid = col_character(),
##   ..   fish_id = col_double(),
##   ..   annnumber = col_character(),
##   ..   length = col_double(),
##   ..   radii_length_mm = col_double(),
##   ..   scalelength = col_double()
##   .. )
##  - attr(*, "problems")=<externalptr>
```


```r
glimpse(fish)
```

```
## Rows: 4,033
## Columns: 6
## $ lakeid          <chr> "AL", "AL", "AL", "AL", "AL", "AL", "AL", "AL", "AL", …
## $ fish_id         <dbl> 299, 299, 299, 300, 300, 300, 300, 301, 301, 301, 301,…
## $ annnumber       <chr> "EDGE", "2", "1", "EDGE", "3", "2", "1", "EDGE", "3", …
## $ length          <dbl> 167, 167, 167, 175, 175, 175, 175, 194, 194, 194, 194,…
## $ radii_length_mm <dbl> 2.697443, 2.037518, 1.311795, 3.015477, 2.670733, 2.13…
## $ scalelength     <dbl> 2.697443, 2.697443, 2.697443, 3.015477, 3.015477, 3.01…
```


```r
names(fish)
```

```
## [1] "lakeid"          "fish_id"         "annnumber"       "length"         
## [5] "radii_length_mm" "scalelength"
```

Notice that when the data are imported, you are presented with a message that tells you how R interpreted the column classes. This is also where error messages will appear if there are problems.  

## Summary functions
Once data have been uploaded, you may want to get an idea of its structure, contents, and dimensions. I routinely run one or more of these commands when data are first imported.  

We can summarize our data frame with the`summary()` function.   it cannot give you any mean that is not numeric

```r
summary(fish)
```

```
##     lakeid             fish_id       annnumber             length     
##  Length:4033        Min.   :  1.0   Length:4033        Min.   : 58.0  
##  Class :character   1st Qu.:156.0   Class :character   1st Qu.:253.0  
##  Mode  :character   Median :267.0   Mode  :character   Median :299.0  
##                     Mean   :258.3                      Mean   :293.3  
##                     3rd Qu.:376.0                      3rd Qu.:342.0  
##                     Max.   :478.0                      Max.   :420.0  
##  radii_length_mm    scalelength     
##  Min.   : 0.4569   Min.   : 0.6282  
##  1st Qu.: 2.3252   1st Qu.: 4.2596  
##  Median : 3.5380   Median : 5.4062  
##  Mean   : 3.6589   Mean   : 5.3821  
##  3rd Qu.: 4.8229   3rd Qu.: 6.4145  
##  Max.   :11.0258   Max.   :11.0258
```

`glimpse()` is another useful summary function.

```r
glimpse(fish)
```

```
## Rows: 4,033
## Columns: 6
## $ lakeid          <chr> "AL", "AL", "AL", "AL", "AL", "AL", "AL", "AL", "AL", …
## $ fish_id         <dbl> 299, 299, 299, 300, 300, 300, 300, 301, 301, 301, 301,…
## $ annnumber       <chr> "EDGE", "2", "1", "EDGE", "3", "2", "1", "EDGE", "3", …
## $ length          <dbl> 167, 167, 167, 175, 175, 175, 175, 194, 194, 194, 194,…
## $ radii_length_mm <dbl> 2.697443, 2.037518, 1.311795, 3.015477, 2.670733, 2.13…
## $ scalelength     <dbl> 2.697443, 2.697443, 2.697443, 3.015477, 3.015477, 3.01…
```

`nrow()` gives the numbers of rows. 4033 row or observation

```r
nrow(fish)
```

```
## [1] 4033
```

`ncol` gives the number of columns.

```r
ncol(fish)
```

```
## [1] 6
```

`dim()` gives the dimensions.

```r
dim(fish)
```

```
## [1] 4033    6
```

`names` gives the column names. give the name of variable

```r
names(fish)
```

```
## [1] "lakeid"          "fish_id"         "annnumber"       "length"         
## [5] "radii_length_mm" "scalelength"
```

`head()` prints the first n rows of the data frame.

```r
head(fish)
```

```
## # A tibble: 6 × 6
##   lakeid fish_id annnumber length radii_length_mm scalelength
##   <chr>    <dbl> <chr>      <dbl>           <dbl>       <dbl>
## 1 AL         299 EDGE         167            2.70        2.70
## 2 AL         299 2            167            2.04        2.70
## 3 AL         299 1            167            1.31        2.70
## 4 AL         300 EDGE         175            3.02        3.02
## 5 AL         300 3            175            2.67        3.02
## 6 AL         300 2            175            2.14        3.02
```

`tail()` prinst the last n rows of the data frame. give you the bottom observation of the fish

```r
tail(fish)
```

```
## # A tibble: 6 × 6
##   lakeid fish_id annnumber length radii_length_mm scalelength
##   <chr>    <dbl> <chr>      <dbl>           <dbl>       <dbl>
## 1 WS         180 6            403            5.41        11.0
## 2 WS         180 5            403            4.98        11.0
## 3 WS         180 4            403            4.22        11.0
## 4 WS         180 3            403            3.04        11.0
## 5 WS         180 2            403            2.03        11.0
## 6 WS         180 1            403            1.19        11.0
```

`table()` is useful when you have a limited number of categorical variables. It produces fast counts of the number of observations in a variable. We will come back to this later...  just fish is too big, you need to adjust it, the following is a table of the states and number of observation

```r
table(fish$lakeid)
```

```
## 
##  AL  AR  BO  BR  CR  DY  FD  JN  LC  LJ  LR LSG  MN  RD  UB  WS 
## 383 262 197 291 343 355 302 238 173 181 292 143 293 135 191 254
```

We can also click on the `fish` data frame in the Environment tab or type View(fish).

```r
#View(fish)
```

## Filter
Filter is a way of pulling out observations that meet specific criteria in a variable. We will work a lot more with this in the next lab.   you wanna pull out the small little fish only

```r
little_fish <- filter(fish, length <=100)
little_fish
```

```
## # A tibble: 5 × 6
##   lakeid fish_id annnumber length radii_length_mm scalelength
##   <chr>    <dbl> <chr>      <dbl>           <dbl>       <dbl>
## 1 LSG         58 EDGE          92           1.15        1.15 
## 2 LSG         59 EDGE          64           0.773       0.773
## 3 WS         151 EDGE          58           0.628       0.628
## 4 WS         152 EDGE          74           0.832       0.832
## 5 WS         153 EDGE          78           0.637       0.637
```

## Practice
1. Load the data `mammal_lifehistories_v2.csv` and place it into a new object called `mammals`.

```r
mammals <-read_csv("data/mammal_lifehistories_v2.csv")
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

2. Provide the dimensions of the data frame.

```r
dim(mammals)
```

```
## [1] 1440   13
```

3. Check the column names in the data frame. 


4. Use `str()` to show the structure of the data frame and its individual columns; compare this to `glimpse()`. 

```r
str(mammals)
```

```
## spc_tbl_ [1,440 × 13] (S3: spec_tbl_df/tbl_df/tbl/data.frame)
##  $ order       : chr [1:1440] "Artiodactyla" "Artiodactyla" "Artiodactyla" "Artiodactyla" ...
##  $ family      : chr [1:1440] "Antilocapridae" "Bovidae" "Bovidae" "Bovidae" ...
##  $ Genus       : chr [1:1440] "Antilocapra" "Addax" "Aepyceros" "Alcelaphus" ...
##  $ species     : chr [1:1440] "americana" "nasomaculatus" "melampus" "buselaphus" ...
##  $ mass        : num [1:1440] 45375 182375 41480 150000 28500 ...
##  $ gestation   : num [1:1440] 8.13 9.39 6.35 7.9 6.8 5.08 5.72 5.5 8.93 9.14 ...
##  $ newborn     : num [1:1440] 3246 5480 5093 10167 -999 ...
##  $ weaning     : num [1:1440] 3 6.5 5.63 6.5 -999 ...
##  $ wean mass   : num [1:1440] 8900 -999 15900 -999 -999 ...
##  $ AFR         : num [1:1440] 13.5 27.3 16.7 23 -999 ...
##  $ max. life   : num [1:1440] 142 308 213 240 -999 251 228 255 300 324 ...
##  $ litter size : num [1:1440] 1.85 1 1 1 1 1.37 1 1 1 1 ...
##  $ litters/year: num [1:1440] 1 0.99 0.95 -999 -999 2 -999 1.89 1 1 ...
##  - attr(*, "spec")=
##   .. cols(
##   ..   order = col_character(),
##   ..   family = col_character(),
##   ..   Genus = col_character(),
##   ..   species = col_character(),
##   ..   mass = col_double(),
##   ..   gestation = col_double(),
##   ..   newborn = col_double(),
##   ..   weaning = col_double(),
##   ..   `wean mass` = col_double(),
##   ..   AFR = col_double(),
##   ..   `max. life` = col_double(),
##   ..   `litter size` = col_double(),
##   ..   `litters/year` = col_double()
##   .. )
##  - attr(*, "problems")=<externalptr>
```


```r
glimpse(mammals)
```

```
## Rows: 1,440
## Columns: 13
## $ order          <chr> "Artiodactyla", "Artiodactyla", "Artiodactyla", "Artiod…
## $ family         <chr> "Antilocapridae", "Bovidae", "Bovidae", "Bovidae", "Bov…
## $ Genus          <chr> "Antilocapra", "Addax", "Aepyceros", "Alcelaphus", "Amm…
## $ species        <chr> "americana", "nasomaculatus", "melampus", "buselaphus",…
## $ mass           <dbl> 45375.0, 182375.0, 41480.0, 150000.0, 28500.0, 55500.0,…
## $ gestation      <dbl> 8.13, 9.39, 6.35, 7.90, 6.80, 5.08, 5.72, 5.50, 8.93, 9…
## $ newborn        <dbl> 3246.36, 5480.00, 5093.00, 10166.67, -999.00, 3810.00, …
## $ weaning        <dbl> 3.00, 6.50, 5.63, 6.50, -999.00, 4.00, 4.04, 2.13, 10.7…
## $ `wean mass`    <dbl> 8900, -999, 15900, -999, -999, -999, -999, -999, 157500…
## $ AFR            <dbl> 13.53, 27.27, 16.66, 23.02, -999.00, 14.89, 10.23, 20.1…
## $ `max. life`    <dbl> 142, 308, 213, 240, -999, 251, 228, 255, 300, 324, 300,…
## $ `litter size`  <dbl> 1.85, 1.00, 1.00, 1.00, 1.00, 1.37, 1.00, 1.00, 1.00, 1…
## $ `litters/year` <dbl> 1.00, 0.99, 0.95, -999.00, -999.00, 2.00, -999.00, 1.89…
```

5. . Try the `table()` command to produce counts of mammal order, family, and genus.  








## Wrap-up
Please review the learning goals and be sure to use the code here as a reference when completing the homework.  
-->[Home](https://jmledford3115.github.io/datascibiol/)
