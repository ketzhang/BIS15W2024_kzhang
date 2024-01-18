---
title: "lab 3 warmup"
output: 
  html_document: 
    keep_md: yes
date: "2024-01-18"
---




### 1 plant height vector

```r
plant_height <- c(30.7,37.6,28.4,NA,33.2)
```

### 2 plant mass vector

```r
plant_weight <- c(4,5.2,3.7,NA,4.6)
```

### 3 label for data matrix

```r
samples <- c("plan1","plant2","plant3","plant4","plant5")
measured <- c("height","weight")
```

### 4 combine data for height and weight

```r
plant_experiment <- c(plant_height, plant_weight)
```

### 5 build the data matrix, using byrow equal false

```r
plant_experiment_matrix <- matrix(plant_experiment, nrow = 5, byrow = F)
plant_experiment_matrix
```

```
##      [,1] [,2]
## [1,] 30.7  4.0
## [2,] 37.6  5.2
## [3,] 28.4  3.7
## [4,]   NA   NA
## [5,] 33.2  4.6
```
###6 name the column and row

```r
colnames(plant_experiment_matrix) <- measured
rownames(plant_experiment_matrix) <- samples
plant_experiment_matrix
```

```
##        height weight
## plan1    30.7    4.0
## plant2   37.6    5.2
## plant3   28.4    3.7
## plant4     NA     NA
## plant5   33.2    4.6
```
###calculate the mean

```r
plant_means <- colMeans(plant_experiment_matrix, na.rm = T)
plant_means
```

```
## height weight 
## 32.475  4.375
```

### add a new row

```r
plant_experiment_matrix.final <- rbind(plant_experiment_matrix,plant_means)
plant_experiment_matrix.final
```

```
##             height weight
## plan1       30.700  4.000
## plant2      37.600  5.200
## plant3      28.400  3.700
## plant4          NA     NA
## plant5      33.200  4.600
## plant_means 32.475  4.375
```

