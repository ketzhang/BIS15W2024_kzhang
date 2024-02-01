---
title: "dplyr Superhero"
date: "2024-02-01"
output:
  html_document: 
    theme: spacelab
    toc: true
    toc_float: true
    keep_md: true
---

## Learning Goals  
*At the end of this exercise, you will be able to:*    
1. Develop your dplyr superpowers so you can easily and confidently manipulate dataframes.  
2. Learn helpful new functions that are part of the `janitor` package.  

## Instructions
For the second part of lab today, we are going to spend time practicing the dplyr functions we have learned and add a few new ones. This lab doubles as your homework. Please complete the lab and push your final code to GitHub.  

## Load the libraries

```r
library("tidyverse")
```

```
## ── Attaching core tidyverse packages ──────────────────────── tidyverse 2.0.0 ──
## ✔ dplyr     1.1.4     ✔ readr     2.1.4
## ✔ forcats   1.0.0     ✔ stringr   1.5.1
## ✔ ggplot2   3.4.4     ✔ tibble    3.2.1
## ✔ lubridate 1.9.3     ✔ tidyr     1.3.0
## ✔ purrr     1.0.2     
## ── Conflicts ────────────────────────────────────────── tidyverse_conflicts() ──
## ✖ dplyr::filter() masks stats::filter()
## ✖ dplyr::lag()    masks stats::lag()
## ℹ Use the conflicted package (<http://conflicted.r-lib.org/>) to force all conflicts to become errors
```

```r
library("janitor")
```

```
## 
## Attaching package: 'janitor'
## 
## The following objects are masked from 'package:stats':
## 
##     chisq.test, fisher.test
```

## Load the superhero data
These are data taken from comic books and assembled by fans. The include a good mix of categorical and continuous data.  Data taken from: https://www.kaggle.com/claudiodavi/superhero-set  

Check out the way I am loading these data. If I know there are NAs, I can take care of them at the beginning. But, we should do this very cautiously. At times it is better to keep the original columns and data intact.  

```r
superhero_info <- read_csv("data/heroes_information.csv", na = c("", "-99", "-")) #anything in the c() will be convert into NA
```

```
## Rows: 734 Columns: 10
## ── Column specification ────────────────────────────────────────────────────────
## Delimiter: ","
## chr (8): name, Gender, Eye color, Race, Hair color, Publisher, Skin color, A...
## dbl (2): Height, Weight
## 
## ℹ Use `spec()` to retrieve the full column specification for this data.
## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.
```

```r
superhero_powers <- read_csv("data/super_hero_powers.csv", na = c("", "-99", "-"))
```

```
## Rows: 667 Columns: 168
## ── Column specification ────────────────────────────────────────────────────────
## Delimiter: ","
## chr   (1): hero_names
## lgl (167): Agility, Accelerated Healing, Lantern Power Ring, Dimensional Awa...
## 
## ℹ Use `spec()` to retrieve the full column specification for this data.
## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.
```

## Data tidy
1. Some of the names used in the `superhero_info` data are problematic so you should rename them here. Before you do anything, first have a look at the names of the variables. You can use `rename()` or `clean_names()`. 

```r
superhero_info <- clean_names(superhero_info)
```


## `tabyl`
The `janitor` package has many awesome functions that we will explore. Here is its version of `table` which not only produces counts but also percentages. Very handy! Let's use it to explore the proportion of good guys and bad guys in the `superhero_info` data.  

```r
tabyl(superhero_info, alignment)
```

```
##  alignment   n     percent valid_percent
##        bad 207 0.282016349    0.28473177
##       good 496 0.675749319    0.68225585
##    neutral  24 0.032697548    0.03301238
##       <NA>   7 0.009536785            NA
```

1. Who are the publishers of the superheros? Show the proportion of superheros from each publisher. Which publisher has the highest number of superheros?  
**Marvel Comics has the highest number of superheros**

```r
tabyl(superhero_info,publisher)
```

```
##          publisher   n     percent valid_percent
##        ABC Studios   4 0.005449591   0.005563282
##          DC Comics 215 0.292915531   0.299026426
##  Dark Horse Comics  18 0.024523161   0.025034771
##       George Lucas  14 0.019073569   0.019471488
##      Hanna-Barbera   1 0.001362398   0.001390821
##      HarperCollins   6 0.008174387   0.008344924
##     IDW Publishing   4 0.005449591   0.005563282
##        Icon Comics   4 0.005449591   0.005563282
##       Image Comics  14 0.019073569   0.019471488
##      J. K. Rowling   1 0.001362398   0.001390821
##   J. R. R. Tolkien   1 0.001362398   0.001390821
##      Marvel Comics 388 0.528610354   0.539638387
##          Microsoft   1 0.001362398   0.001390821
##       NBC - Heroes  19 0.025885559   0.026425591
##          Rebellion   1 0.001362398   0.001390821
##           Shueisha   4 0.005449591   0.005563282
##      Sony Pictures   2 0.002724796   0.002781641
##         South Park   1 0.001362398   0.001390821
##          Star Trek   6 0.008174387   0.008344924
##               SyFy   5 0.006811989   0.006954103
##       Team Epic TV   5 0.006811989   0.006954103
##        Titan Books   1 0.001362398   0.001390821
##  Universal Studios   1 0.001362398   0.001390821
##          Wildstorm   3 0.004087193   0.004172462
##               <NA>  15 0.020435967            NA
```


2. Notice that we have some neutral superheros! Who are they? List their names below.  

```r
superhero_info %>% 
  select(name, alignment) %>% 
  filter(alignment =="neutral")
```

```
## # A tibble: 24 × 2
##    name         alignment
##    <chr>        <chr>    
##  1 Bizarro      neutral  
##  2 Black Flash  neutral  
##  3 Captain Cold neutral  
##  4 Copycat      neutral  
##  5 Deadpool     neutral  
##  6 Deathstroke  neutral  
##  7 Etrigan      neutral  
##  8 Galactus     neutral  
##  9 Gladiator    neutral  
## 10 Indigo       neutral  
## # ℹ 14 more rows
```


## `superhero_info`
3. Let's say we are only interested in the variables name, alignment, and "race". How would you isolate these variables from `superhero_info`?

```r
superhero_info %>% 
  select(name, alignment, race)
```

```
## # A tibble: 734 × 3
##    name          alignment race             
##    <chr>         <chr>     <chr>            
##  1 A-Bomb        good      Human            
##  2 Abe Sapien    good      Icthyo Sapien    
##  3 Abin Sur      good      Ungaran          
##  4 Abomination   bad       Human / Radiation
##  5 Abraxas       bad       Cosmic Entity    
##  6 Absorbing Man bad       Human            
##  7 Adam Monroe   good      <NA>             
##  8 Adam Strange  good      Human            
##  9 Agent 13      good      <NA>             
## 10 Agent Bob     good      Human            
## # ℹ 724 more rows
```


## Not Human
4. List all of the superheros that are not human.

```r
superhero_info %>% 
  filter(race!="Human")
```

```
## # A tibble: 222 × 10
##    name  gender eye_color race  hair_color height publisher skin_color alignment
##    <chr> <chr>  <chr>     <chr> <chr>       <dbl> <chr>     <chr>      <chr>    
##  1 Abe … Male   blue      Icth… No Hair       191 Dark Hor… blue       good     
##  2 Abin… Male   blue      Unga… No Hair       185 DC Comics red        good     
##  3 Abom… Male   green     Huma… No Hair       203 Marvel C… <NA>       bad      
##  4 Abra… Male   blue      Cosm… Black          NA Marvel C… <NA>       bad      
##  5 Ajax  Male   brown     Cybo… Black         193 Marvel C… <NA>       bad      
##  6 Alien Male   <NA>      Xeno… No Hair       244 Dark Hor… black      bad      
##  7 Amazo Male   red       Andr… <NA>          257 DC Comics <NA>       bad      
##  8 Angel Male   <NA>      Vamp… <NA>           NA Dark Hor… <NA>       good     
##  9 Ange… Female yellow    Muta… Black         165 Marvel C… <NA>       good     
## 10 Anti… Male   yellow    God … No Hair        61 DC Comics <NA>       bad      
## # ℹ 212 more rows
## # ℹ 1 more variable: weight <dbl>
```


## Good and Evil
5. Let's make two different data frames, one focused on the "good guys" and another focused on the "bad guys".

```r
bad_guy <- superhero_info %>% 
  filter(alignment == "bad")
```


```r
good_guy <- superhero_info %>% 
  filter(alignment == "good")
```


6. For the good guys, use the `tabyl` function to summarize their "race".

```r
tabyl(good_guy, race)
```

```
##               race   n     percent valid_percent
##              Alien   3 0.006048387   0.010752688
##              Alpha   5 0.010080645   0.017921147
##             Amazon   2 0.004032258   0.007168459
##            Android   4 0.008064516   0.014336918
##             Animal   2 0.004032258   0.007168459
##          Asgardian   3 0.006048387   0.010752688
##          Atlantean   4 0.008064516   0.014336918
##         Bolovaxian   1 0.002016129   0.003584229
##              Clone   1 0.002016129   0.003584229
##             Cyborg   3 0.006048387   0.010752688
##           Demi-God   2 0.004032258   0.007168459
##              Demon   3 0.006048387   0.010752688
##            Eternal   1 0.002016129   0.003584229
##     Flora Colossus   1 0.002016129   0.003584229
##        Frost Giant   1 0.002016129   0.003584229
##      God / Eternal   6 0.012096774   0.021505376
##             Gungan   1 0.002016129   0.003584229
##              Human 148 0.298387097   0.530465950
##    Human / Altered   2 0.004032258   0.007168459
##     Human / Cosmic   2 0.004032258   0.007168459
##  Human / Radiation   8 0.016129032   0.028673835
##         Human-Kree   2 0.004032258   0.007168459
##      Human-Spartoi   1 0.002016129   0.003584229
##       Human-Vulcan   1 0.002016129   0.003584229
##    Human-Vuldarian   1 0.002016129   0.003584229
##      Icthyo Sapien   1 0.002016129   0.003584229
##            Inhuman   4 0.008064516   0.014336918
##    Kakarantharaian   1 0.002016129   0.003584229
##         Kryptonian   4 0.008064516   0.014336918
##            Martian   1 0.002016129   0.003584229
##          Metahuman   1 0.002016129   0.003584229
##             Mutant  46 0.092741935   0.164874552
##     Mutant / Clone   1 0.002016129   0.003584229
##             Planet   1 0.002016129   0.003584229
##             Saiyan   1 0.002016129   0.003584229
##           Symbiote   3 0.006048387   0.010752688
##           Talokite   1 0.002016129   0.003584229
##         Tamaranean   1 0.002016129   0.003584229
##            Ungaran   1 0.002016129   0.003584229
##            Vampire   2 0.004032258   0.007168459
##     Yoda's species   1 0.002016129   0.003584229
##      Zen-Whoberian   1 0.002016129   0.003584229
##               <NA> 217 0.437500000            NA
```


7. Among the good guys, Who are the Vampires?

```r
good_guy %>% 
  filter(race == "Vampire")
```

```
## # A tibble: 2 × 10
##   name  gender eye_color race   hair_color height publisher skin_color alignment
##   <chr> <chr>  <chr>     <chr>  <chr>       <dbl> <chr>     <chr>      <chr>    
## 1 Angel Male   <NA>      Vampi… <NA>           NA Dark Hor… <NA>       good     
## 2 Blade Male   brown     Vampi… Black         188 Marvel C… <NA>       good     
## # ℹ 1 more variable: weight <dbl>
```

8. Among the bad guys, who are the male humans over 200 inches in height?

```r
bad_guy %>% 
  filter(height > 200)
```

```
## # A tibble: 25 × 10
##    name  gender eye_color race  hair_color height publisher skin_color alignment
##    <chr> <chr>  <chr>     <chr> <chr>       <dbl> <chr>     <chr>      <chr>    
##  1 Abom… Male   green     Huma… No Hair       203 Marvel C… <NA>       bad      
##  2 Alien Male   <NA>      Xeno… No Hair       244 Dark Hor… black      bad      
##  3 Amazo Male   red       Andr… <NA>          257 DC Comics <NA>       bad      
##  4 Apoc… Male   red       Muta… Black         213 Marvel C… grey       bad      
##  5 Bane  Male   <NA>      Human <NA>          203 DC Comics <NA>       bad      
##  6 Bloo… Female blue      Human Brown         218 Marvel C… <NA>       bad      
##  7 Dark… Male   red       New … No Hair       267 DC Comics grey       bad      
##  8 Doct… Male   brown     Human Brown         201 Marvel C… <NA>       bad      
##  9 Doct… Male   brown     <NA>  Brown         201 Marvel C… <NA>       bad      
## 10 Doom… Male   red       Alien White         244 DC Comics <NA>       bad      
## # ℹ 15 more rows
## # ℹ 1 more variable: weight <dbl>
```


9. Are there more good guys or bad guys with green hair? 
**More good guys have green hair than bad guys**


```r
good_guy %>% 
  filter(hair_color == "Green")
```

```
## # A tibble: 7 × 10
##   name   gender eye_color race  hair_color height publisher skin_color alignment
##   <chr>  <chr>  <chr>     <chr> <chr>       <dbl> <chr>     <chr>      <chr>    
## 1 Beast… Male   green     Human Green         173 DC Comics green      good     
## 2 Capta… Male   red       God … Green          NA Marvel C… <NA>       good     
## 3 Doc S… Male   blue      Huma… Green         198 Marvel C… <NA>       good     
## 4 Hulk   Male   green     Huma… Green         244 Marvel C… green      good     
## 5 Lyja   Female green     <NA>  Green          NA Marvel C… <NA>       good     
## 6 Polar… Female green     Muta… Green         170 Marvel C… <NA>       good     
## 7 She-H… Female green     Human Green         201 Marvel C… <NA>       good     
## # ℹ 1 more variable: weight <dbl>
```


```r
bad_guy %>% 
  filter(`hair_color`=="Green")
```

```
## # A tibble: 1 × 10
##   name  gender eye_color race  hair_color height publisher skin_color alignment
##   <chr> <chr>  <chr>     <chr> <chr>       <dbl> <chr>     <chr>      <chr>    
## 1 Joker Male   green     Human Green         196 DC Comics white      bad      
## # ℹ 1 more variable: weight <dbl>
```


**Alternative ways to do this, when it should TRUE, this indicate the corresponding hero has green hair.  Good guys have 7 true and bad guys only 1 true**

```r
tabyl(good_guy$hair_color == "Green")
```

```
##  good_guy$hair_color == "Green"   n   percent valid_percent
##                           FALSE 375 0.7560484    0.98167539
##                            TRUE   7 0.0141129    0.01832461
##                              NA 114 0.2298387            NA
```


```r
tabyl(bad_guy$hair_color == "Green")
```

```
##  bad_guy$hair_color == "Green"   n     percent valid_percent
##                          FALSE 153 0.739130435   0.993506494
##                           TRUE   1 0.004830918   0.006493506
##                             NA  53 0.256038647            NA
```

10. Let's explore who the really small superheros are. In the `superhero_info` data, which have a weight less than 50? Be sure to sort your results by weight lowest to highest.  


```r
short_hero <- superhero_info %>% 
  select(name, weight) %>% 
  filter(weight < 50) %>% 
  arrange(weight)
short_hero
```

```
## # A tibble: 19 × 2
##    name              weight
##    <chr>              <dbl>
##  1 Iron Monger            2
##  2 Groot                  4
##  3 Jack-Jack             14
##  4 Galactus              16
##  5 Yoda                  17
##  6 Fin Fang Foom         18
##  7 Howard the Duck       18
##  8 Krypto                18
##  9 Rocket Raccoon        25
## 10 Dash                  27
## 11 Longshot              36
## 12 Robin V               38
## 13 Wiz Kid               39
## 14 Violet Parr           41
## 15 Franklin Richards     45
## 16 Swarm                 47
## 17 Hope Summers          48
## 18 Jolt                  49
## 19 Snowbird              49
```


11. Let's make a new variable that is the ratio of height to weight. Call this variable `height_weight_ratio`.    

```r
superhero_info %>% 
  select(name, height, weight) %>% 
  mutate(height_weight_ratio = height / weight) %>% 
  arrange(height_weight_ratio)
```

```
## # A tibble: 734 × 4
##    name        height weight height_weight_ratio
##    <chr>        <dbl>  <dbl>               <dbl>
##  1 Giganta       62.5    630              0.0992
##  2 Utgard-Loki   15.2     58              0.262 
##  3 Darkseid     267      817              0.327 
##  4 Juggernaut   287      855              0.336 
##  5 Red Hulk     213      630              0.338 
##  6 Sasquatch    305      900              0.339 
##  7 Hulk         244      630              0.387 
##  8 Bloodaxe     218      495              0.440 
##  9 Thanos       201      443              0.454 
## 10 A-Bomb       203      441              0.460 
## # ℹ 724 more rows
```


12. Who has the highest height to weight ratio?  

```r
superhero_info %>% 
  select(name, height, weight) %>% 
  mutate(height_weight_ratio = height / weight) %>% 
  arrange(desc(height_weight_ratio)) #highest to lowest
```

```
## # A tibble: 734 × 4
##    name            height weight height_weight_ratio
##    <chr>            <dbl>  <dbl>               <dbl>
##  1 Groot              701      4              175.  
##  2 Galactus           876     16               54.8 
##  3 Fin Fang Foom      975     18               54.2 
##  4 Longshot           188     36                5.22
##  5 Jack-Jack           71     14                5.07
##  6 Rocket Raccoon     122     25                4.88
##  7 Dash               122     27                4.52
##  8 Howard the Duck     79     18                4.39
##  9 Swarm              196     47                4.17
## 10 Yoda                66     17                3.88
## # ℹ 724 more rows
```


## `superhero_powers`
Have a quick look at the `superhero_powers` data frame. 

```r
summary(superhero_powers)
```

```
##   hero_names         Agility        Accelerated Healing Lantern Power Ring
##  Length:667         Mode :logical   Mode :logical       Mode :logical     
##  Class :character   FALSE:425       FALSE:489           FALSE:656         
##  Mode  :character   TRUE :242       TRUE :178           TRUE :11          
##  Dimensional Awareness Cold Resistance Durability       Stealth       
##  Mode :logical         Mode :logical   Mode :logical   Mode :logical  
##  FALSE:642             FALSE:620       FALSE:410       FALSE:541      
##  TRUE :25              TRUE :47        TRUE :257       TRUE :126      
##  Energy Absorption   Flight        Danger Sense    Underwater breathing
##  Mode :logical     Mode :logical   Mode :logical   Mode :logical       
##  FALSE:590         FALSE:455       FALSE:637       FALSE:646           
##  TRUE :77          TRUE :212       TRUE :30        TRUE :21            
##  Marksmanship    Weapons Master  Power Augmentation Animal Attributes
##  Mode :logical   Mode :logical   Mode :logical      Mode :logical    
##  FALSE:548       FALSE:562       FALSE:659          FALSE:642        
##  TRUE :119       TRUE :105       TRUE :8            TRUE :25         
##  Longevity       Intelligence    Super Strength  Cryokinesis    
##  Mode :logical   Mode :logical   Mode :logical   Mode :logical  
##  FALSE:554       FALSE:509       FALSE:307       FALSE:648      
##  TRUE :113       TRUE :158       TRUE :360       TRUE :19       
##  Telepathy       Energy Armor    Energy Blasts   Duplication    
##  Mode :logical   Mode :logical   Mode :logical   Mode :logical  
##  FALSE:575       FALSE:659       FALSE:520       FALSE:651      
##  TRUE :92        TRUE :8         TRUE :147       TRUE :16       
##  Size Changing   Density Control  Stamina        Astral Travel  
##  Mode :logical   Mode :logical   Mode :logical   Mode :logical  
##  FALSE:612       FALSE:652       FALSE:378       FALSE:663      
##  TRUE :55        TRUE :15        TRUE :289       TRUE :4        
##  Audio Control   Dexterity        Omnitrix       Super Speed    
##  Mode :logical   Mode :logical   Mode :logical   Mode :logical  
##  FALSE:660       FALSE:661       FALSE:666       FALSE:418      
##  TRUE :7         TRUE :6         TRUE :1         TRUE :249      
##  Possession      Animal Oriented Powers Weapon-based Powers Electrokinesis 
##  Mode :logical   Mode :logical          Mode :logical       Mode :logical  
##  FALSE:659       FALSE:627              FALSE:609           FALSE:645      
##  TRUE :8         TRUE :40               TRUE :58            TRUE :22       
##  Darkforce Manipulation Death Touch     Teleportation   Enhanced Senses
##  Mode :logical          Mode :logical   Mode :logical   Mode :logical  
##  FALSE:657              FALSE:660       FALSE:595       FALSE:578      
##  TRUE :10               TRUE :7         TRUE :72        TRUE :89       
##  Telekinesis     Energy Beams      Magic         Hyperkinesis   
##  Mode :logical   Mode :logical   Mode :logical   Mode :logical  
##  FALSE:606       FALSE:625       FALSE:623       FALSE:666      
##  TRUE :61        TRUE :42        TRUE :44        TRUE :1        
##     Jump         Clairvoyance    Dimensional Travel Power Sense    
##  Mode :logical   Mode :logical   Mode :logical      Mode :logical  
##  FALSE:602       FALSE:663       FALSE:644          FALSE:664      
##  TRUE :65        TRUE :4         TRUE :23           TRUE :3        
##  Shapeshifting   Peak Human Condition Immortality     Camouflage     
##  Mode :logical   Mode :logical        Mode :logical   Mode :logical  
##  FALSE:601       FALSE:637            FALSE:598       FALSE:646      
##  TRUE :66        TRUE :30             TRUE :69        TRUE :21       
##  Element Control  Phasing        Astral Projection Electrical Transport
##  Mode :logical   Mode :logical   Mode :logical     Mode :logical       
##  FALSE:659       FALSE:636       FALSE:638         FALSE:666           
##  TRUE :8         TRUE :31        TRUE :29          TRUE :1             
##  Fire Control    Projection      Summoning       Enhanced Memory
##  Mode :logical   Mode :logical   Mode :logical   Mode :logical  
##  FALSE:635       FALSE:665       FALSE:663       FALSE:642      
##  TRUE :32        TRUE :2         TRUE :4         TRUE :25       
##   Reflexes       Invulnerability Energy Constructs Force Fields   
##  Mode :logical   Mode :logical   Mode :logical     Mode :logical  
##  FALSE:503       FALSE:550       FALSE:629         FALSE:581      
##  TRUE :164       TRUE :117       TRUE :38          TRUE :86       
##  Self-Sustenance Anti-Gravity     Empathy        Power Nullifier
##  Mode :logical   Mode :logical   Mode :logical   Mode :logical  
##  FALSE:630       FALSE:666       FALSE:648       FALSE:663      
##  TRUE :37        TRUE :1         TRUE :19        TRUE :4        
##  Radiation Control Psionic Powers  Elasticity      Substance Secretion
##  Mode :logical     Mode :logical   Mode :logical   Mode :logical      
##  FALSE:660         FALSE:618       FALSE:656       FALSE:650          
##  TRUE :7           TRUE :49        TRUE :11        TRUE :17           
##  Elemental Transmogrification Technopath/Cyberpath Photographic Reflexes
##  Mode :logical                Mode :logical        Mode :logical        
##  FALSE:661                    FALSE:644            FALSE:664            
##  TRUE :6                      TRUE :23             TRUE :3              
##  Seismic Power   Animation       Precognition    Mind Control   
##  Mode :logical   Mode :logical   Mode :logical   Mode :logical  
##  FALSE:664       FALSE:662       FALSE:645       FALSE:645      
##  TRUE :3         TRUE :5         TRUE :22        TRUE :22       
##  Fire Resistance Power Absorption Enhanced Hearing Nova Force     
##  Mode :logical   Mode :logical    Mode :logical    Mode :logical  
##  FALSE:649       FALSE:655        FALSE:595        FALSE:665      
##  TRUE :18        TRUE :12         TRUE :72         TRUE :2        
##   Insanity       Hypnokinesis    Animal Control  Natural Armor  
##  Mode :logical   Mode :logical   Mode :logical   Mode :logical  
##  FALSE:662       FALSE:644       FALSE:658       FALSE:631      
##  TRUE :5         TRUE :23        TRUE :9         TRUE :36       
##  Intangibility   Enhanced Sight  Molecular Manipulation Heat Generation
##  Mode :logical   Mode :logical   Mode :logical          Mode :logical  
##  FALSE:647       FALSE:642       FALSE:625              FALSE:643      
##  TRUE :20        TRUE :25        TRUE :42               TRUE :24       
##  Adaptation       Gliding        Power Suit      Mind Blast     
##  Mode :logical   Mode :logical   Mode :logical   Mode :logical  
##  FALSE:662       FALSE:657       FALSE:634       FALSE:655      
##  TRUE :5         TRUE :10        TRUE :33        TRUE :12       
##  Probability Manipulation Gravity Control Regeneration    Light Control  
##  Mode :logical            Mode :logical   Mode :logical   Mode :logical  
##  FALSE:658                FALSE:651       FALSE:639       FALSE:652      
##  TRUE :9                  TRUE :16        TRUE :28        TRUE :15       
##  Echolocation    Levitation      Toxin and Disease Control   Banish       
##  Mode :logical   Mode :logical   Mode :logical             Mode :logical  
##  FALSE:665       FALSE:641       FALSE:657                 FALSE:666      
##  TRUE :2         TRUE :26        TRUE :10                  TRUE :1        
##  Energy Manipulation Heat Resistance Natural Weapons Time Travel    
##  Mode :logical       Mode :logical   Mode :logical   Mode :logical  
##  FALSE:615           FALSE:624       FALSE:609       FALSE:634      
##  TRUE :52            TRUE :43        TRUE :58        TRUE :33       
##  Enhanced Smell  Illusions       Thirstokinesis  Hair Manipulation
##  Mode :logical   Mode :logical   Mode :logical   Mode :logical    
##  FALSE:635       FALSE:629       FALSE:666       FALSE:666        
##  TRUE :32        TRUE :38        TRUE :1         TRUE :1          
##  Illumination    Omnipotent       Cloaking       Changing Armor 
##  Mode :logical   Mode :logical   Mode :logical   Mode :logical  
##  FALSE:665       FALSE:660       FALSE:660       FALSE:666      
##  TRUE :2         TRUE :7         TRUE :7         TRUE :1        
##  Power Cosmic    Biokinesis      Water Control   Radiation Immunity
##  Mode :logical   Mode :logical   Mode :logical   Mode :logical     
##  FALSE:660       FALSE:666       FALSE:654       FALSE:657         
##  TRUE :7         TRUE :1         TRUE :13        TRUE :10          
##  Vision - Telescopic Toxin and Disease Resistance Spatial Awareness
##  Mode :logical       Mode :logical                Mode :logical    
##  FALSE:624           FALSE:619                    FALSE:666        
##  TRUE :43            TRUE :48                     TRUE :1          
##  Energy Resistance Telepathy Resistance Molecular Combustion Omnilingualism 
##  Mode :logical     Mode :logical        Mode :logical        Mode :logical  
##  FALSE:660         FALSE:634            FALSE:665            FALSE:646      
##  TRUE :7           TRUE :33             TRUE :2              TRUE :21       
##  Portal Creation Magnetism       Mind Control Resistance Plant Control  
##  Mode :logical   Mode :logical   Mode :logical           Mode :logical  
##  FALSE:663       FALSE:656       FALSE:655               FALSE:659      
##  TRUE :4         TRUE :11        TRUE :12                TRUE :8        
##    Sonar         Sonic Scream    Time Manipulation Enhanced Touch 
##  Mode :logical   Mode :logical   Mode :logical     Mode :logical  
##  FALSE:663       FALSE:661       FALSE:647         FALSE:660      
##  TRUE :4         TRUE :6         TRUE :20          TRUE :7        
##  Magic Resistance Invisibility    Sub-Mariner     Radiation Absorption
##  Mode :logical    Mode :logical   Mode :logical   Mode :logical       
##  FALSE:661        FALSE:645       FALSE:647       FALSE:660           
##  TRUE :6          TRUE :22        TRUE :20        TRUE :7             
##  Intuitive aptitude Vision - Microscopic  Melting        Wind Control   
##  Mode :logical      Mode :logical        Mode :logical   Mode :logical  
##  FALSE:666          FALSE:648            FALSE:665       FALSE:664      
##  TRUE :1            TRUE :19             TRUE :2         TRUE :3        
##  Super Breath    Wallcrawling    Vision - Night  Vision - Infrared
##  Mode :logical   Mode :logical   Mode :logical   Mode :logical    
##  FALSE:644       FALSE:633       FALSE:633       FALSE:645        
##  TRUE :23        TRUE :34        TRUE :34        TRUE :22         
##  Grim Reaping    Matter Absorption The Force       Resurrection   
##  Mode :logical   Mode :logical     Mode :logical   Mode :logical  
##  FALSE:664       FALSE:656         FALSE:661       FALSE:652      
##  TRUE :3         TRUE :11          TRUE :6         TRUE :15       
##  Terrakinesis    Vision - Heat   Vitakinesis     Radar Sense    
##  Mode :logical   Mode :logical   Mode :logical   Mode :logical  
##  FALSE:665       FALSE:648       FALSE:665       FALSE:661      
##  TRUE :2         TRUE :19        TRUE :2         TRUE :6        
##  Qwardian Power Ring Weather Control Vision - X-Ray  Vision - Thermal
##  Mode :logical       Mode :logical   Mode :logical   Mode :logical   
##  FALSE:665           FALSE:659       FALSE:644       FALSE:644       
##  TRUE :2             TRUE :8         TRUE :23        TRUE :23        
##  Web Creation    Reality Warping Odin Force      Symbiote Costume
##  Mode :logical   Mode :logical   Mode :logical   Mode :logical   
##  FALSE:653       FALSE:651       FALSE:665       FALSE:658       
##  TRUE :14        TRUE :16        TRUE :2         TRUE :9         
##  Speed Force     Phoenix Force   Molecular Dissipation Vision - Cryo  
##  Mode :logical   Mode :logical   Mode :logical         Mode :logical  
##  FALSE:666       FALSE:666       FALSE:666             FALSE:665      
##  TRUE :1         TRUE :1         TRUE :1               TRUE :2        
##  Omnipresent     Omniscient     
##  Mode :logical   Mode :logical  
##  FALSE:665       FALSE:665      
##  TRUE :2         TRUE :2
```

```r
colnames(superhero_powers)
```

```
##   [1] "hero_names"                   "Agility"                     
##   [3] "Accelerated Healing"          "Lantern Power Ring"          
##   [5] "Dimensional Awareness"        "Cold Resistance"             
##   [7] "Durability"                   "Stealth"                     
##   [9] "Energy Absorption"            "Flight"                      
##  [11] "Danger Sense"                 "Underwater breathing"        
##  [13] "Marksmanship"                 "Weapons Master"              
##  [15] "Power Augmentation"           "Animal Attributes"           
##  [17] "Longevity"                    "Intelligence"                
##  [19] "Super Strength"               "Cryokinesis"                 
##  [21] "Telepathy"                    "Energy Armor"                
##  [23] "Energy Blasts"                "Duplication"                 
##  [25] "Size Changing"                "Density Control"             
##  [27] "Stamina"                      "Astral Travel"               
##  [29] "Audio Control"                "Dexterity"                   
##  [31] "Omnitrix"                     "Super Speed"                 
##  [33] "Possession"                   "Animal Oriented Powers"      
##  [35] "Weapon-based Powers"          "Electrokinesis"              
##  [37] "Darkforce Manipulation"       "Death Touch"                 
##  [39] "Teleportation"                "Enhanced Senses"             
##  [41] "Telekinesis"                  "Energy Beams"                
##  [43] "Magic"                        "Hyperkinesis"                
##  [45] "Jump"                         "Clairvoyance"                
##  [47] "Dimensional Travel"           "Power Sense"                 
##  [49] "Shapeshifting"                "Peak Human Condition"        
##  [51] "Immortality"                  "Camouflage"                  
##  [53] "Element Control"              "Phasing"                     
##  [55] "Astral Projection"            "Electrical Transport"        
##  [57] "Fire Control"                 "Projection"                  
##  [59] "Summoning"                    "Enhanced Memory"             
##  [61] "Reflexes"                     "Invulnerability"             
##  [63] "Energy Constructs"            "Force Fields"                
##  [65] "Self-Sustenance"              "Anti-Gravity"                
##  [67] "Empathy"                      "Power Nullifier"             
##  [69] "Radiation Control"            "Psionic Powers"              
##  [71] "Elasticity"                   "Substance Secretion"         
##  [73] "Elemental Transmogrification" "Technopath/Cyberpath"        
##  [75] "Photographic Reflexes"        "Seismic Power"               
##  [77] "Animation"                    "Precognition"                
##  [79] "Mind Control"                 "Fire Resistance"             
##  [81] "Power Absorption"             "Enhanced Hearing"            
##  [83] "Nova Force"                   "Insanity"                    
##  [85] "Hypnokinesis"                 "Animal Control"              
##  [87] "Natural Armor"                "Intangibility"               
##  [89] "Enhanced Sight"               "Molecular Manipulation"      
##  [91] "Heat Generation"              "Adaptation"                  
##  [93] "Gliding"                      "Power Suit"                  
##  [95] "Mind Blast"                   "Probability Manipulation"    
##  [97] "Gravity Control"              "Regeneration"                
##  [99] "Light Control"                "Echolocation"                
## [101] "Levitation"                   "Toxin and Disease Control"   
## [103] "Banish"                       "Energy Manipulation"         
## [105] "Heat Resistance"              "Natural Weapons"             
## [107] "Time Travel"                  "Enhanced Smell"              
## [109] "Illusions"                    "Thirstokinesis"              
## [111] "Hair Manipulation"            "Illumination"                
## [113] "Omnipotent"                   "Cloaking"                    
## [115] "Changing Armor"               "Power Cosmic"                
## [117] "Biokinesis"                   "Water Control"               
## [119] "Radiation Immunity"           "Vision - Telescopic"         
## [121] "Toxin and Disease Resistance" "Spatial Awareness"           
## [123] "Energy Resistance"            "Telepathy Resistance"        
## [125] "Molecular Combustion"         "Omnilingualism"              
## [127] "Portal Creation"              "Magnetism"                   
## [129] "Mind Control Resistance"      "Plant Control"               
## [131] "Sonar"                        "Sonic Scream"                
## [133] "Time Manipulation"            "Enhanced Touch"              
## [135] "Magic Resistance"             "Invisibility"                
## [137] "Sub-Mariner"                  "Radiation Absorption"        
## [139] "Intuitive aptitude"           "Vision - Microscopic"        
## [141] "Melting"                      "Wind Control"                
## [143] "Super Breath"                 "Wallcrawling"                
## [145] "Vision - Night"               "Vision - Infrared"           
## [147] "Grim Reaping"                 "Matter Absorption"           
## [149] "The Force"                    "Resurrection"                
## [151] "Terrakinesis"                 "Vision - Heat"               
## [153] "Vitakinesis"                  "Radar Sense"                 
## [155] "Qwardian Power Ring"          "Weather Control"             
## [157] "Vision - X-Ray"               "Vision - Thermal"            
## [159] "Web Creation"                 "Reality Warping"             
## [161] "Odin Force"                   "Symbiote Costume"            
## [163] "Speed Force"                  "Phoenix Force"               
## [165] "Molecular Dissipation"        "Vision - Cryo"               
## [167] "Omnipresent"                  "Omniscient"
```


13. How many superheros have a combination of agility, stealth, super_strength, stamina?

```r
superhero_powers %>% 
  select(hero_names, Agility, Stealth, `Super Strength`, Stamina) %>% 
  filter(Agility == "TRUE" & Stealth == "TRUE", `Super Strength`=="TRUE", Stamina =="TRUE")
```

```
## # A tibble: 40 × 5
##    hero_names  Agility Stealth `Super Strength` Stamina
##    <chr>       <lgl>   <lgl>   <lgl>            <lgl>  
##  1 Alex Mercer TRUE    TRUE    TRUE             TRUE   
##  2 Angel       TRUE    TRUE    TRUE             TRUE   
##  3 Ant-Man II  TRUE    TRUE    TRUE             TRUE   
##  4 Aquaman     TRUE    TRUE    TRUE             TRUE   
##  5 Batman      TRUE    TRUE    TRUE             TRUE   
##  6 Black Flash TRUE    TRUE    TRUE             TRUE   
##  7 Black Manta TRUE    TRUE    TRUE             TRUE   
##  8 Brundlefly  TRUE    TRUE    TRUE             TRUE   
##  9 Buffy       TRUE    TRUE    TRUE             TRUE   
## 10 Cable       TRUE    TRUE    TRUE             TRUE   
## # ℹ 30 more rows
```


## Your Favorite
14. Pick your favorite superhero and let's see their powers!  

```r
superhero_powers %>% 
  filter(hero_names == "Catwoman")
```

```
## # A tibble: 1 × 168
##   hero_names Agility `Accelerated Healing` `Lantern Power Ring`
##   <chr>      <lgl>   <lgl>                 <lgl>               
## 1 Catwoman   TRUE    FALSE                 FALSE               
## # ℹ 164 more variables: `Dimensional Awareness` <lgl>, `Cold Resistance` <lgl>,
## #   Durability <lgl>, Stealth <lgl>, `Energy Absorption` <lgl>, Flight <lgl>,
## #   `Danger Sense` <lgl>, `Underwater breathing` <lgl>, Marksmanship <lgl>,
## #   `Weapons Master` <lgl>, `Power Augmentation` <lgl>,
## #   `Animal Attributes` <lgl>, Longevity <lgl>, Intelligence <lgl>,
## #   `Super Strength` <lgl>, Cryokinesis <lgl>, Telepathy <lgl>,
## #   `Energy Armor` <lgl>, `Energy Blasts` <lgl>, Duplication <lgl>, …
```


15. Can you find your hero in the superhero_info data? Show their info!  

```r
superhero_info %>% 
  filter(name == "Catwoman")
```

```
## # A tibble: 1 × 10
##   name   gender eye_color race  hair_color height publisher skin_color alignment
##   <chr>  <chr>  <chr>     <chr> <chr>       <dbl> <chr>     <chr>      <chr>    
## 1 Catwo… Female green     Human Black         175 DC Comics <NA>       good     
## # ℹ 1 more variable: weight <dbl>
```


## Push your final code to GitHub!
Please be sure that you check the `keep md` file in the knit preferences.  
