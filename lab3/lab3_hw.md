---
title: "Lab 3 Homework"
author: "Ketong Zhang"
date: "2024-01-22"
output:
  html_document: 
    theme: spacelab
    keep_md: true
---

## Instructions
Answer the following questions and complete the exercises in RMarkdown. Please embed all of your code and push your final work to your repository. Your final lab report should be organized, clean, and run free from errors. Remember, you must remove the `#` for the included code chunks to run. Be sure to add your name to the author header above.  

Make sure to use the formatting conventions of RMarkdown to make your report neat and clean!  

## Load the tidyverse

```r
library(tidyverse)
```

### Mammals Sleep  
1. For this assignment, we are going to use built-in data on mammal sleep patterns. From which publication are these data taken from? Since the data are built-in you can use the help function in R. The name of the data is `msleep`.  

```r
msleep 
```

```
## # A tibble: 83 × 11
##    name   genus vore  order conservation sleep_total sleep_rem sleep_cycle awake
##    <chr>  <chr> <chr> <chr> <chr>              <dbl>     <dbl>       <dbl> <dbl>
##  1 Cheet… Acin… carni Carn… lc                  12.1      NA        NA      11.9
##  2 Owl m… Aotus omni  Prim… <NA>                17         1.8      NA       7  
##  3 Mount… Aplo… herbi Rode… nt                  14.4       2.4      NA       9.6
##  4 Great… Blar… omni  Sori… lc                  14.9       2.3       0.133   9.1
##  5 Cow    Bos   herbi Arti… domesticated         4         0.7       0.667  20  
##  6 Three… Brad… herbi Pilo… <NA>                14.4       2.2       0.767   9.6
##  7 North… Call… carni Carn… vu                   8.7       1.4       0.383  15.3
##  8 Vespe… Calo… <NA>  Rode… <NA>                 7        NA        NA      17  
##  9 Dog    Canis carni Carn… domesticated        10.1       2.9       0.333  13.9
## 10 Roe d… Capr… herbi Arti… lc                   3        NA        NA      21  
## # ℹ 73 more rows
## # ℹ 2 more variables: brainwt <dbl>, bodywt <dbl>
```

2. Store these data into a new data frame `sleep`.  

```r
sleep <- data.frame(msleep)
```

3. What are the dimensions of this data frame (variables and observations)? How do you know? Please show the *code* that you used to determine this below.  

```r
dim(sleep)
```

```
## [1] 83 11
```

4. Are there any NAs in the data? How did you determine this? Please show your code.  
**Yes there are NAs in the data, and we determine it by using the anyNA function, when return TRUE, it means there are NAs.**

```r
anyNA(sleep)
```

```
## [1] TRUE
```

5. Show a list of the column names is this data frame.

```r
names(sleep)
```

```
##  [1] "name"         "genus"        "vore"         "order"        "conservation"
##  [6] "sleep_total"  "sleep_rem"    "sleep_cycle"  "awake"        "brainwt"     
## [11] "bodywt"
```

6. How many herbivores are represented in the data?  
**32 herbivores in total**

```r
table(sleep$vore)
```

```
## 
##   carni   herbi insecti    omni 
##      19      32       5      20
```

7. We are interested in two groups; small and large mammals. Let's define small as less than or equal to 19kg body weight and large as greater than or equal to 200kg body weight. Make two new dataframes (large and small) based on these parameters.

```r
small_mammals <- filter(sleep, bodywt <=19)
small_mammals
```

```
##                              name         genus    vore           order
## 1                      Owl monkey         Aotus    omni        Primates
## 2                 Mountain beaver    Aplodontia   herbi        Rodentia
## 3      Greater short-tailed shrew       Blarina    omni    Soricomorpha
## 4                Three-toed sloth      Bradypus   herbi          Pilosa
## 5                    Vesper mouse       Calomys    <NA>        Rodentia
## 6                             Dog         Canis   carni       Carnivora
## 7                        Roe deer     Capreolus   herbi    Artiodactyla
## 8                      Guinea pig         Cavis   herbi        Rodentia
## 9                          Grivet Cercopithecus    omni        Primates
## 10                     Chinchilla    Chinchilla   herbi        Rodentia
## 11                Star-nosed mole     Condylura    omni    Soricomorpha
## 12      African giant pouched rat    Cricetomys    omni        Rodentia
## 13      Lesser short-tailed shrew     Cryptotis    omni    Soricomorpha
## 14           Long-nosed armadillo       Dasypus   carni       Cingulata
## 15                     Tree hyrax   Dendrohyrax   herbi      Hyracoidea
## 16         North American Opossum     Didelphis    omni Didelphimorphia
## 17                  Big brown bat     Eptesicus insecti      Chiroptera
## 18              European hedgehog     Erinaceus    omni  Erinaceomorpha
## 19                   Patas monkey  Erythrocebus    omni        Primates
## 20      Western american chipmunk      Eutamias   herbi        Rodentia
## 21                   Domestic cat         Felis   carni       Carnivora
## 22                         Galago        Galago    omni        Primates
## 23                     Gray hyrax   Heterohyrax   herbi      Hyracoidea
## 24                 Mongoose lemur         Lemur   herbi        Primates
## 25           Thick-tailed opposum    Lutreolina   carni Didelphimorphia
## 26                        Macaque        Macaca    omni        Primates
## 27               Mongolian gerbil      Meriones   herbi        Rodentia
## 28                 Golden hamster  Mesocricetus   herbi        Rodentia
## 29                          Vole       Microtus   herbi        Rodentia
## 30                    House mouse           Mus   herbi        Rodentia
## 31               Little brown bat        Myotis insecti      Chiroptera
## 32           Round-tailed muskrat      Neofiber   herbi        Rodentia
## 33                     Slow loris     Nyctibeus   carni        Primates
## 34                           Degu       Octodon   herbi        Rodentia
## 35     Northern grasshopper mouse     Onychomys   carni        Rodentia
## 36                         Rabbit   Oryctolagus   herbi      Lagomorpha
## 37                Desert hedgehog   Paraechinus    <NA>  Erinaceomorpha
## 38                          Potto  Perodicticus    omni        Primates
## 39                     Deer mouse    Peromyscus    <NA>        Rodentia
## 40                      Phalanger     Phalanger    <NA>   Diprotodontia
## 41                        Potoroo      Potorous   herbi   Diprotodontia
## 42                     Rock hyrax      Procavia    <NA>      Hyracoidea
## 43                 Laboratory rat        Rattus   herbi        Rodentia
## 44          African striped mouse     Rhabdomys    omni        Rodentia
## 45                Squirrel monkey       Saimiri    omni        Primates
## 46          Eastern american mole      Scalopus insecti    Soricomorpha
## 47                     Cotton rat      Sigmodon   herbi        Rodentia
## 48                       Mole rat        Spalax    <NA>        Rodentia
## 49         Arctic ground squirrel  Spermophilus   herbi        Rodentia
## 50 Thirteen-lined ground squirrel  Spermophilus   herbi        Rodentia
## 51 Golden-mantled ground squirrel  Spermophilus   herbi        Rodentia
## 52                     Musk shrew        Suncus    <NA>    Soricomorpha
## 53            Short-nosed echidna  Tachyglossus insecti     Monotremata
## 54      Eastern american chipmunk        Tamias   herbi        Rodentia
## 55                         Tenrec        Tenrec    omni    Afrosoricida
## 56                     Tree shrew        Tupaia    omni      Scandentia
## 57                          Genet       Genetta   carni       Carnivora
## 58                     Arctic fox        Vulpes   carni       Carnivora
## 59                        Red fox        Vulpes   carni       Carnivora
##    conservation sleep_total sleep_rem sleep_cycle awake brainwt bodywt
## 1          <NA>        17.0       1.8          NA   7.0 0.01550  0.480
## 2            nt        14.4       2.4          NA   9.6      NA  1.350
## 3            lc        14.9       2.3   0.1333333   9.1 0.00029  0.019
## 4          <NA>        14.4       2.2   0.7666667   9.6      NA  3.850
## 5          <NA>         7.0        NA          NA  17.0      NA  0.045
## 6  domesticated        10.1       2.9   0.3333333  13.9 0.07000 14.000
## 7            lc         3.0        NA          NA  21.0 0.09820 14.800
## 8  domesticated         9.4       0.8   0.2166667  14.6 0.00550  0.728
## 9            lc        10.0       0.7          NA  14.0      NA  4.750
## 10 domesticated        12.5       1.5   0.1166667  11.5 0.00640  0.420
## 11           lc        10.3       2.2          NA  13.7 0.00100  0.060
## 12         <NA>         8.3       2.0          NA  15.7 0.00660  1.000
## 13           lc         9.1       1.4   0.1500000  14.9 0.00014  0.005
## 14           lc        17.4       3.1   0.3833333   6.6 0.01080  3.500
## 15           lc         5.3       0.5          NA  18.7 0.01230  2.950
## 16           lc        18.0       4.9   0.3333333   6.0 0.00630  1.700
## 17           lc        19.7       3.9   0.1166667   4.3 0.00030  0.023
## 18           lc        10.1       3.5   0.2833333  13.9 0.00350  0.770
## 19           lc        10.9       1.1          NA  13.1 0.11500 10.000
## 20         <NA>        14.9        NA          NA   9.1      NA  0.071
## 21 domesticated        12.5       3.2   0.4166667  11.5 0.02560  3.300
## 22         <NA>         9.8       1.1   0.5500000  14.2 0.00500  0.200
## 23           lc         6.3       0.6          NA  17.7 0.01227  2.625
## 24           vu         9.5       0.9          NA  14.5      NA  1.670
## 25           lc        19.4       6.6          NA   4.6      NA  0.370
## 26         <NA>        10.1       1.2   0.7500000  13.9 0.17900  6.800
## 27           lc        14.2       1.9          NA   9.8      NA  0.053
## 28           en        14.3       3.1   0.2000000   9.7 0.00100  0.120
## 29         <NA>        12.8        NA          NA  11.2      NA  0.035
## 30           nt        12.5       1.4   0.1833333  11.5 0.00040  0.022
## 31         <NA>        19.9       2.0   0.2000000   4.1 0.00025  0.010
## 32           nt        14.6        NA          NA   9.4      NA  0.266
## 33         <NA>        11.0        NA          NA  13.0 0.01250  1.400
## 34           lc         7.7       0.9          NA  16.3      NA  0.210
## 35           lc        14.5        NA          NA   9.5      NA  0.028
## 36 domesticated         8.4       0.9   0.4166667  15.6 0.01210  2.500
## 37           lc        10.3       2.7          NA  13.7 0.00240  0.550
## 38           lc        11.0        NA          NA  13.0      NA  1.100
## 39         <NA>        11.5        NA          NA  12.5      NA  0.021
## 40         <NA>        13.7       1.8          NA  10.3 0.01140  1.620
## 41         <NA>        11.1       1.5          NA  12.9      NA  1.100
## 42           lc         5.4       0.5          NA  18.6 0.02100  3.600
## 43           lc        13.0       2.4   0.1833333  11.0 0.00190  0.320
## 44         <NA>         8.7        NA          NA  15.3      NA  0.044
## 45         <NA>         9.6       1.4          NA  14.4 0.02000  0.743
## 46           lc         8.4       2.1   0.1666667  15.6 0.00120  0.075
## 47         <NA>        11.3       1.1   0.1500000  12.7 0.00118  0.148
## 48         <NA>        10.6       2.4          NA  13.4 0.00300  0.122
## 49           lc        16.6        NA          NA   7.4 0.00570  0.920
## 50           lc        13.8       3.4   0.2166667  10.2 0.00400  0.101
## 51           lc        15.9       3.0          NA   8.1      NA  0.205
## 52         <NA>        12.8       2.0   0.1833333  11.2 0.00033  0.048
## 53         <NA>         8.6        NA          NA  15.4 0.02500  4.500
## 54         <NA>        15.8        NA          NA   8.2      NA  0.112
## 55         <NA>        15.6       2.3          NA   8.4 0.00260  0.900
## 56         <NA>         8.9       2.6   0.2333333  15.1 0.00250  0.104
## 57         <NA>         6.3       1.3          NA  17.7 0.01750  2.000
## 58         <NA>        12.5        NA          NA  11.5 0.04450  3.380
## 59         <NA>         9.8       2.4   0.3500000  14.2 0.05040  4.230
```


```r
large_mammals <- filter(sleep, bodywt >=200)
large_mammals
```

```
##               name         genus  vore          order conservation sleep_total
## 1              Cow           Bos herbi   Artiodactyla domesticated         4.0
## 2   Asian elephant       Elephas herbi    Proboscidea           en         3.9
## 3            Horse         Equus herbi Perissodactyla domesticated         2.9
## 4          Giraffe       Giraffa herbi   Artiodactyla           cd         1.9
## 5      Pilot whale Globicephalus carni        Cetacea           cd         2.7
## 6 African elephant     Loxodonta herbi    Proboscidea           vu         3.3
## 7  Brazilian tapir       Tapirus herbi Perissodactyla           vu         4.4
##   sleep_rem sleep_cycle awake brainwt   bodywt
## 1       0.7   0.6666667 20.00   0.423  600.000
## 2        NA          NA 20.10   4.603 2547.000
## 3       0.6   1.0000000 21.10   0.655  521.000
## 4       0.4          NA 22.10      NA  899.995
## 5       0.1          NA 21.35      NA  800.000
## 6        NA          NA 20.70   5.712 6654.000
## 7       1.0   0.9000000 19.60   0.169  207.501
```

8. What is the mean weight for both the small and large mammals?

```r
mean(large_mammals$bodywt, na.rm = T)
```

```
## [1] 1747.071
```


```r
mean(small_mammals$bodywt, na.rm = T)
```

```
## [1] 1.797847
```

9. Using a similar approach as above, do large or small animals sleep longer on average?  
**Small animals sleep longer on average.**

```r
mean(large_mammals$sleep_total, na.rm = T)
```

```
## [1] 3.3
```


```r
mean(small_mammals$sleep_total, na.rm = T)
```

```
## [1] 11.78644
```


10. Which animal is the sleepiest among the entire dataframe?
**Little brown bat is the sleepiest among the entire dataframe**

```r
?max
max(sleep$sleep_total) #find the maximum value from sleep_total
```

```
## [1] 19.9
```


```r
filter(sleep, sleep_total>=max(sleep$sleep_total) )
```

```
##               name  genus    vore      order conservation sleep_total sleep_rem
## 1 Little brown bat Myotis insecti Chiroptera         <NA>        19.9         2
##   sleep_cycle awake brainwt bodywt
## 1         0.2   4.1 0.00025   0.01
```


## Push your final code to GitHub!
Please be sure that you check the `keep md` file in the knit preferences.   
