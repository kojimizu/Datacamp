Unsupervised Learning
================
Koji Mizumura
2019-04-25

  - [Unsupervised learning](#unsupervised-learning)
      - [k-means clustering](#k-means-clustering)
      - [Results of k-means](#results-of-k-means)
      - [Visualizing and interpreting results of
        kmeans()](#visualizing-and-interpreting-results-of-kmeans)
      - [How kmeans() works and practical
        matters](#how-kmeans-works-and-practical-matters)
          - [Objective](#objective)
          - [For model selection](#for-model-selection)
          - [Determine number of
            clusters](#determine-number-of-clusters)
      - [Handling random algorithms](#handling-random-algorithms)
      - [Selecting number of clusters](#selecting-number-of-clusters)
      - [Practical matters: working with real
        data](#practical-matters-working-with-real-data)
  - [Hierachical clustering](#hierachical-clustering)
      - [Hierarchical clustering with
        results](#hierarchical-clustering-with-results)
      - [Selecting number of clusters](#selecting-number-of-clusters-1)
          - [Dendrogram](#dendrogram)
      - [Cutting the tree](#cutting-the-tree)
      - [Linkage methods](#linkage-methods)
      - [Comparing linkage methods](#comparing-linkage-methods)
      - [Practical matters: scaling](#practical-matters-scaling)
      - [Comparing kmeans() and hclust()](#comparing-kmeans-and-hclust)
  - [Dimensionality reduction by PCA](#dimensionality-reduction-by-pca)
      - [Introduction to PCA](#introduction-to-pca)
      - [PCA using prcomp()](#pca-using-prcomp)
      - [Results of PCA](#results-of-pca)
      - [Additional results of PCA](#additional-results-of-pca)
      - [Variance explained](#variance-explained)
      - [Visualize variance explained](#visualize-variance-explained)
      - [Practical issues with PCA](#practical-issues-with-pca)
      - [Practical issues: scaling](#practical-issues-scaling)
  - [Putting it all together with a case
    study](#putting-it-all-together-with-a-case-study)
      - [Introduction](#introduction)
      - [Preparing the data](#preparing-the-data)
      - [EDA](#eda)
      - [Peform PCA](#peform-pca)
      - [Interpreting PCA results](#interpreting-pca-results)
      - [Variance explained](#variance-explained-1)
      - [Communicating PCA results](#communicating-pca-results)
      - [PCA review and next steps](#pca-review-and-next-steps)
      - [Hierachical clustering of case
        data](#hierachical-clustering-of-case-data)
      - [Results of hierachical
        clustering](#results-of-hierachical-clustering)
      - [Selecting number of clusters](#selecting-number-of-clusters-2)
      - [k-means clustering and comparing
        results](#k-means-clustering-and-comparing-results)
      - [Clustering on PCA results](#clustering-on-pca-results)

``` r
library(tidyverse)
```

    ## Warning: package 'tidyverse' was built under R version 3.5.1

    ## -- Attaching packages --------

    ## √ ggplot2 3.1.0     √ purrr   0.2.5
    ## √ tibble  1.4.2     √ dplyr   0.7.8
    ## √ tidyr   0.8.1     √ stringr 1.3.1
    ## √ readr   1.1.1     √ forcats 0.3.0

    ## Warning: package 'ggplot2' was built under R version 3.5.1

    ## Warning: package 'purrr' was built under R version 3.5.1

    ## Warning: package 'dplyr' was built under R version 3.5.1

    ## -- Conflicts -----------------
    ## x dplyr::filter() masks stats::filter()
    ## x dplyr::lag()    masks stats::lag()

``` r
library(magrittr)
```

    ## 
    ## Attaching package: 'magrittr'

    ## The following object is masked from 'package:purrr':
    ## 
    ##     set_names

    ## The following object is masked from 'package:tidyr':
    ## 
    ##     extract

# Unsupervised learning

## k-means clustering

We have created some two-dimensional data and stored it in a variable
called `x` in your workspace. The scatter plot on the right is a visual
representation of the data.

In this exercise, your task is to create a k-means model of the x data
using 3 clusters, then to look at the structure of the resulting model
using the `summary()` function.

``` r
iris %>% head()
```

    ##   Sepal.Length Sepal.Width Petal.Length Petal.Width Species
    ## 1          5.1         3.5          1.4         0.2  setosa
    ## 2          4.9         3.0          1.4         0.2  setosa
    ## 3          4.7         3.2          1.3         0.2  setosa
    ## 4          4.6         3.1          1.5         0.2  setosa
    ## 5          5.0         3.6          1.4         0.2  setosa
    ## 6          5.4         3.9          1.7         0.4  setosa

``` r
# Create the k-means model: km.out
km.out <- kmeans(iris[,1:4], centers=3, nstart=20)

# Inspect the result
summary(km.out)
```

    ##              Length Class  Mode   
    ## cluster      150    -none- numeric
    ## centers       12    -none- numeric
    ## totss          1    -none- numeric
    ## withinss       3    -none- numeric
    ## tot.withinss   1    -none- numeric
    ## betweenss      1    -none- numeric
    ## size           3    -none- numeric
    ## iter           1    -none- numeric
    ## ifault         1    -none- numeric

## Results of k-means

The `kmeans()` function produces several outputs. In the video, we
discussed one output of modeling, the cluster membership.

In this exercise, you will access the `cluster` component directly. This
is useful anytime you need the cluster membership for each observation
of the data used to build the clustering model. A future exercise will
show an example of how this cluster membership might be used to help
communicate the results of k-means modeling.

k-means models also have a print method to give a human friendly output
of basic modeling results. This is available by using `print()` or
simply typing the name of the model.

``` r
# Print the cluster membership component of the model
km.out$cluster
```

    ##   [1] 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1
    ##  [36] 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 2 2 3 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2
    ##  [71] 2 2 2 2 2 2 2 3 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 3 2 3 3 3
    ## [106] 3 2 3 3 3 3 3 3 2 2 3 3 3 3 2 3 2 3 2 3 3 2 2 3 3 3 3 3 2 3 3 3 3 2 3
    ## [141] 3 3 2 3 3 3 2 3 3 2

``` r
# Print the km.out object
km.out
```

    ## K-means clustering with 3 clusters of sizes 50, 62, 38
    ## 
    ## Cluster means:
    ##   Sepal.Length Sepal.Width Petal.Length Petal.Width
    ## 1     5.006000    3.428000     1.462000    0.246000
    ## 2     5.901613    2.748387     4.393548    1.433871
    ## 3     6.850000    3.073684     5.742105    2.071053
    ## 
    ## Clustering vector:
    ##   [1] 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1
    ##  [36] 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 2 2 3 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2
    ##  [71] 2 2 2 2 2 2 2 3 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 3 2 3 3 3
    ## [106] 3 2 3 3 3 3 3 3 2 2 3 3 3 3 2 3 2 3 2 3 3 2 2 3 3 3 3 3 2 3 3 3 3 2 3
    ## [141] 3 3 2 3 3 3 2 3 3 2
    ## 
    ## Within cluster sum of squares by cluster:
    ## [1] 15.15100 39.82097 23.87947
    ##  (between_SS / total_SS =  88.4 %)
    ## 
    ## Available components:
    ## 
    ## [1] "cluster"      "centers"      "totss"        "withinss"    
    ## [5] "tot.withinss" "betweenss"    "size"         "iter"        
    ## [9] "ifault"

## Visualizing and interpreting results of kmeans()

One of the more intuitive ways to interpret the results of k-means
models is by plotting the data as a scatter plot and using color to
label the samples’ cluster membership. In this exercise, you will use
the standard `plot()` function to accomplish this.

To create a scatter plot, you can pass data with two features
(i.e. columns) to `plot()` with an extra argument `col =
km.out$cluster`, which sets the color of each point in the scatter plot
according to its cluster membership.

``` r
# Scatter plot of x
plot(iris, col = km.out$cluster)
```

![](Unsupervised_learning_files/figure-gfm/unnamed-chunk-3-1.png)<!-- -->

## How kmeans() works and practical matters

### Objective

  - Explain how k-means algorithm is implemented visually
  - Model selection: determining numbers of clusters

### For model selection

  - recall k-means has a random component
  - best outcome is based on total within cluster sum of squares
      - for each cluster
      - For each observation in the cluster
      - Determine squared distance from the observation to cluster
        center
      - sum of them altogether

We can select `nstart`, running algorithm multiple times helps to find
the global minimum total within cluster sum of squares.

``` r
# k-means algorithm with 5 centers, run 20 times
kmeans(x, centers =5, nstart=20)
```

### Determine number of clusters

  - Trial and error is not the best approach
  - One approach is elbow plot.

## Handling random algorithms

``` r
# Set up 2 x 3 plotting grid
par(mfrow = c(2, 3))

# Set seed
set.seed(1)

x <- iris[,1:4]

for(i in 1:6) {
  # Run kmeans() on x with three clusters and one start
  km.out <- kmeans(x, centers = 3, nstart = 1)
  
  # Plot clusters
  plot(x, col = km.out$cluster, 
       main = km.out$tot.withinss)
}
```

![](Unsupervised_learning_files/figure-gfm/unnamed-chunk-5-1.png)<!-- -->![](Unsupervised_learning_files/figure-gfm/unnamed-chunk-5-2.png)<!-- -->![](Unsupervised_learning_files/figure-gfm/unnamed-chunk-5-3.png)<!-- -->![](Unsupervised_learning_files/figure-gfm/unnamed-chunk-5-4.png)<!-- -->![](Unsupervised_learning_files/figure-gfm/unnamed-chunk-5-5.png)<!-- -->

## Selecting number of clusters

The k-means algorithm assumes the number of clusters as part of the
input. If you know the number of clusters in advance (e.g. due to
certain business constraints) this makes setting the number of clusters
easy. However, as you saw in the video, if you do not know the number of
clusters and need to determine it, you will need to run the algorithm
multiple times, each time with a different number of clusters. From
this, you can observe how a measure of model quality changes with the
number of clusters.

In this exercise, you will run `kmeans()` multiple times to see how
model quality changes as the number of clusters changes. Plots
displaying this information help to determine the number of clusters and
are often referred to as scree plots.

The ideal plot will have an elbow where the quality measure improves
more slowly as the number of clusters increases. This indicates that the
quality of the model is no longer improving substantially as the model
complexity (i.e. number of clusters) increases. In other words, the
elbow indicates the number of clusters inherent in the data.

``` r
# Initialize total within sum of squares error: wss
wss <- 0

# For 1 to 15 cluster centers
for (i in 1:15) {
  km.out <- kmeans(x, centers = i, nstart=20)
  # Save total within sum of squares to wss variable
  wss[i] <- km.out$tot.withinss
}

# Plot total within sum of squares vs. number of clusters
plot(1:15, wss, type = "b", 
     xlab = "Number of Clusters", 
     ylab = "Within groups sum of squares")
```

![](Unsupervised_learning_files/figure-gfm/unnamed-chunk-6-1.png)<!-- -->

``` r
# Set k equal to the number of clusters corresponding to the elbow location
k <- 2
```

## Practical matters: working with real data

Dealing with real data is often more challenging than dealing with
synthetic data. Synthetic data helps with learning new concepts and
techniques, but the next few exercises will deal with data that is
closer to the type of real data you might find in your professional or
academic pursuits.

The first challenge with the Pokemon data is that there is no
pre-determined number of clusters. You will determine the appropriate
number of clusters, keeping in mind that in real data the elbow in the
scree plot might be less of a sharp elbow than in synthetic data. Use
your judgement on making the determination of the number of clusters.

The second part of this exercise includes plotting the outcomes of the
clustering on two dimensions, or features, of the data. These features
were chosen somewhat arbitrarily for this exercise. Think about how you
would use plotting and clustering to communicate interesting groups of
Pokemon to other people.

An additional note: this exercise utilizes the iter.max argument to
`kmeans()`. As you’ve seen, `kmeans()` is an iterative algorithm,
repeating over and over until some stopping criterion is reached. The
default number of iterations for `kmeans()` is 10, which is not enough
for the algorithm to converge and reach its stopping criterion, so we’ll
set the number of iterations to 50 to overcome this issue. To see what
happens when `kmeans()` does not converge, try running the example with
a lower number of iterations (e.g. 3). This is another example of what
might happen when you encounter real data and use real
cases.

``` r
pokemon <- readr::read_csv("C:/Protected/Data Science/Datacamp/5. Unsupervised learning/Pokemon.csv")
```

    ## Parsed with column specification:
    ## cols(
    ##   Number = col_integer(),
    ##   Name = col_character(),
    ##   Type1 = col_character(),
    ##   Type2 = col_character(),
    ##   Total = col_integer(),
    ##   HitPoints = col_integer(),
    ##   Attack = col_integer(),
    ##   Defense = col_integer(),
    ##   SpecialAttack = col_integer(),
    ##   SpecialDefense = col_integer(),
    ##   Speed = col_integer(),
    ##   Generation = col_integer(),
    ##   Legendary = col_character()
    ## )

``` r
# Initialize total within sum of squares error: wss
wss <- 0
# Look over 1 to 15 possible clusters
for (i in 1:15) {
  # Fit the model: km.out
  km.out <- kmeans(pokemon[,5:12], centers = i, nstart = 20, iter.max = 50)
  # Save the within cluster sum of squares
  wss[i] <- km.out$tot.withinss
}

# Produce a scree plot
plot(1:15, wss, type = "b", 
     xlab = "Number of Clusters", 
     ylab = "Within groups sum of squares")
```

![](Unsupervised_learning_files/figure-gfm/unnamed-chunk-7-1.png)<!-- -->

``` r
# Select number of clusters
k <- 3

# Build model with k clusters: km.out
km.out <- kmeans(pokemon[,5:12], centers = k, nstart = 20, iter.max = 50)

# View the resulting model
km.out
```

    ## K-means clustering with 3 clusters of sizes 123, 288, 389
    ## 
    ## Cluster means:
    ##      Total HitPoints    Attack   Defense SpecialAttack SpecialDefense
    ## 1 622.5691  88.91057 117.72358 100.65854     116.33333      101.86179
    ## 2 303.8958  50.14931  53.95486  52.78472      47.85417       49.49306
    ## 3 472.9666  77.19280  85.30077  80.95373      77.54499       79.02057
    ##      Speed Generation
    ## 1 97.08130   3.341463
    ## 2 49.65972   3.315972
    ## 3 72.95373   3.323907
    ## 
    ## Clustering vector:
    ##   [1] 2 3 3 1 2 3 3 1 1 2 3 3 1 2 2 3 2 2 3 3 2 2 3 1 2 3 2 3 2 3 2 3 2 3 2
    ##  [36] 2 3 2 2 3 2 3 2 3 2 3 2 3 2 3 3 2 3 2 3 2 3 2 3 2 3 2 3 2 1 2 2 3 2 3
    ##  [71] 3 1 2 3 3 2 3 3 2 3 2 3 3 3 3 2 3 1 2 3 2 2 3 2 3 2 3 2 3 2 3 3 1 2 2
    ## [106] 3 2 3 2 3 2 3 2 3 3 3 2 2 3 2 3 3 3 3 1 2 3 2 3 2 3 3 3 3 3 3 3 1 3 2
    ## [141] 3 1 3 2 2 3 3 3 3 2 3 2 3 3 1 3 1 1 1 2 3 1 1 1 1 1 2 3 3 2 3 3 2 3 3
    ## [176] 2 3 2 3 2 3 2 3 3 2 3 2 2 2 2 3 2 3 2 2 3 1 3 2 3 3 3 2 2 3 2 2 3 3 2
    ## [211] 3 3 3 3 3 3 2 3 3 2 3 3 3 3 1 2 3 3 3 1 3 3 1 3 2 3 2 3 2 3 2 2 3 2 3
    ## [246] 3 2 3 1 3 2 3 3 3 2 2 3 2 2 2 3 3 1 1 1 2 3 1 1 1 1 1 2 3 3 1 2 3 3 1
    ## [281] 2 3 3 1 2 3 2 3 2 2 3 2 2 2 2 3 2 2 3 2 3 2 3 2 2 3 1 2 3 2 3 2 3 1 2
    ## [316] 3 2 2 2 3 2 3 2 2 2 2 2 3 2 3 2 3 3 1 2 3 3 2 3 1 3 3 3 3 3 2 3 2 3 1
    ## [351] 3 3 2 3 1 3 2 3 2 2 2 3 2 3 2 3 1 3 3 3 3 2 3 2 3 2 3 2 3 2 3 2 3 3 3
    ## [386] 2 3 1 2 3 3 3 3 1 2 2 3 1 2 3 3 2 3 3 3 2 2 3 1 1 2 3 1 1 1 1 1 1 1 1
    ## [421] 1 1 1 1 1 1 1 1 1 1 1 1 2 3 3 2 3 3 2 3 3 2 2 3 2 3 2 2 2 2 3 2 3 2 3
    ## [456] 2 3 2 3 3 3 3 2 3 3 2 3 2 3 2 3 3 2 3 2 3 1 3 3 2 3 2 2 3 2 3 2 2 2 3
    ## [491] 3 2 3 1 1 3 2 3 1 2 3 2 3 2 3 3 2 3 2 2 3 1 3 3 3 3 3 3 3 3 3 3 3 3 3
    ## [526] 3 3 1 3 3 3 3 3 3 3 3 3 1 1 1 1 1 1 1 1 1 1 3 1 1 1 1 1 1 2 3 3 2 3 3
    ## [561] 2 3 3 2 3 2 2 3 2 3 2 3 2 3 2 3 2 3 2 2 3 2 3 2 3 3 2 3 2 3 3 3 2 3 3
    ## [596] 2 2 3 3 3 2 2 3 2 2 3 2 3 2 3 3 2 2 3 2 3 3 3 2 3 2 3 3 2 3 2 3 3 1 2
    ## [631] 3 2 3 2 3 2 3 3 2 2 3 2 3 2 3 3 2 3 3 2 3 2 3 2 3 3 2 3 2 3 2 3 3 2 3
    ## [666] 3 2 3 2 2 3 2 3 3 2 3 3 2 3 3 2 3 3 2 3 2 3 3 2 3 2 3 3 3 2 3 1 2 1 1
    ## [701] 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 2 3 3 2 3 3 2 3 3 2 3 2 2 3 2 2 3
    ## [736] 2 3 2 2 1 2 3 2 3 3 2 3 3 2 3 3 3 2 3 2 3 2 3 2 3 2 3 2 3 2 3 2 3 2 3
    ## [771] 3 3 3 3 2 3 1 3 2 3 2 2 2 2 3 3 3 3 2 3 2 3 1 1 1 1 1 1 1 1
    ## 
    ## Within cluster sum of squares by cluster:
    ## [1]  908595.3 1152885.3 2189884.7
    ##  (between_SS / total_SS =  73.1 %)
    ## 
    ## Available components:
    ## 
    ## [1] "cluster"      "centers"      "totss"        "withinss"    
    ## [5] "tot.withinss" "betweenss"    "size"         "iter"        
    ## [9] "ifault"

``` r
# Plot of Defense vs. Speed by cluster membership
plot(pokemon[, c("Defense", "Speed")],
     col = km.out$cluster,
     main = paste("k-means clustering of Pokemon with", k, "clusters"),
     xlab = "Defense", ylab = "Speed")
```

![](Unsupervised_learning_files/figure-gfm/unnamed-chunk-7-2.png)<!-- -->

# Hierachical clustering

  - Number of clusters is not know ahead of time
  - Two kinds: bottom-up and top-down, this course bottom-up

<!-- end list -->

``` r
# calculate similarity as Euclidean distance between observations

dist_matrix <- dist(x)

# retruns hierachical clustering model
hclust(d=dist_matrix)
```

    ## 
    ## Call:
    ## hclust(d = dist_matrix)
    ## 
    ## Cluster method   : complete 
    ## Distance         : euclidean 
    ## Number of objects: 150

## Hierarchical clustering with results

In this exercise, you will create your first hierarchical clustering
model using the `hclust()` function.

We have created some data that has two dimensions and placed it in a
variable called `x`. Your task is to create a hierarchical clustering
model of `x`. Remember from the video that the first step to
hierarchical clustering is determining the similarity between
observations, which you will do with the `dist()` function.

You will look at the structure of the resulting model using the
`summary()` function.

``` r
# Create hierarchical clustering model: hclust.out
hclust.out <- hclust(dist(x))

library(magrittr)
x %>% dist() %>% head()
```

    ## [1] 0.5385165 0.5099020 0.6480741 0.1414214 0.6164414 0.5196152

``` r
# Inspect the result
summary(hclust.out)
```

    ##             Length Class  Mode     
    ## merge       298    -none- numeric  
    ## height      149    -none- numeric  
    ## order       150    -none- numeric  
    ## labels        0    -none- NULL     
    ## method        1    -none- character
    ## call          2    -none- call     
    ## dist.method   1    -none- character

## Selecting number of clusters

``` r
# create hierachical cluster model: hclust.out
hclust.out <- hclust(dist(x))

# inspect the results
summary(hclust.out)
```

    ##             Length Class  Mode     
    ## merge       298    -none- numeric  
    ## height      149    -none- numeric  
    ## order       150    -none- numeric  
    ## labels        0    -none- NULL     
    ## method        1    -none- character
    ## call          2    -none- call     
    ## dist.method   1    -none- character

### Dendrogram

Tree shaped strcuture used to interpret hierachical clustering models

``` r
# draw a dendrogram
plot(hclust.out)
abline(h=2, col="red")
```

![](Unsupervised_learning_files/figure-gfm/unnamed-chunk-11-1.png)<!-- -->

need to cut the tree to get cluster assigments

``` r
# cut by heright h
cutree(hclust.out, h=2) 
```

    ##   [1] 1 1 1 1 1 2 1 1 1 1 2 2 1 1 2 2 2 1 2 2 2 2 1 2 2 1 2 1 1 1 1 2 2 2 1
    ##  [36] 1 2 1 1 1 1 1 1 2 2 1 2 1 2 1 3 3 3 4 3 4 3 4 3 4 4 4 4 3 4 3 4 4 3 4
    ##  [71] 3 4 3 3 3 3 3 3 3 4 4 4 4 3 4 3 3 3 4 4 4 3 4 4 4 4 4 3 4 4 5 3 6 5 5
    ## [106] 6 4 6 5 6 5 3 5 3 3 5 5 6 6 3 5 3 6 3 5 6 3 3 5 6 6 6 5 3 3 6 5 5 3 5
    ## [141] 5 5 3 5 5 5 3 5 5 3

``` r
# cut by number of cluster k
cutree(hclust.out, k=6)
```

    ##   [1] 1 1 1 1 1 2 1 1 1 1 2 2 1 1 2 2 2 1 2 2 2 2 1 2 2 1 2 1 1 1 1 2 2 2 1
    ##  [36] 1 2 1 1 1 1 1 1 2 2 1 2 1 2 1 3 3 3 4 3 4 3 4 3 4 4 4 4 3 4 3 4 4 3 4
    ##  [71] 3 4 3 3 3 3 3 3 3 4 4 4 4 3 4 3 3 3 4 4 4 3 4 4 4 4 4 3 4 4 5 3 6 5 5
    ## [106] 6 4 6 5 6 5 3 5 3 3 5 5 6 6 3 5 3 6 3 5 6 3 3 5 6 6 6 5 3 3 6 5 5 3 5
    ## [141] 5 5 3 5 5 5 3 5 5 3

## Cutting the tree

Remember from the video that `cutree()` is the R function that cuts a
hierarchical model. The h and k arguments to `cutree()` allow you to cut
the tree based on a certain height h or a certain number of clusters k.

In this exercise, you will use `cutree()` to cut the hierarchical model
you created earlier based on each of these two criteria.

``` r
# Cut by height
library(magrittr)
hclust.out %>% cutree(h=7)
```

    ##   [1] 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1
    ##  [36] 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 2 2 2 1 2 1 2 1 2 1 1 1 1 2 1 2 1 1 2 1
    ##  [71] 2 1 2 2 2 2 2 2 2 1 1 1 1 2 1 2 2 2 1 1 1 2 1 1 1 1 1 2 1 1 2 2 2 2 2
    ## [106] 2 1 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2
    ## [141] 2 2 2 2 2 2 2 2 2 2

``` r
# Cut by number of clusters
hclust.out %>% cutree(k=3)
```

    ##   [1] 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1
    ##  [36] 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 2 2 2 3 2 3 2 3 2 3 3 3 3 2 3 2 3 3 2 3
    ##  [71] 2 3 2 2 2 2 2 2 2 3 3 3 3 2 3 2 2 2 3 3 3 2 3 3 3 3 3 2 3 3 2 2 2 2 2
    ## [106] 2 3 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2
    ## [141] 2 2 2 2 2 2 2 2 2 2

## Linkage methods

In this exercise, you will produce hierarchical clustering models using
different linkages and plot the dendrogram for each, observing the
overall structure of the trees.

You’ll be asked to interpret the results in the next exercise.

``` r
# Cluster using complete linkage: hclust.complete
hclust.complete <- hclust(dist(x), method = "complete")

# Cluster using average linkage: hclust.average
hclust.average <- hclust(dist(x), method = "average")

# Cluster using single linkage: hclust.single
hclust.single <- hclust(dist(x), method = "single")

# Plot dendrogram of hclust.complete
plot(hclust.complete, main="Complete")
```

![](Unsupervised_learning_files/figure-gfm/unnamed-chunk-14-1.png)<!-- -->

``` r
# Plot dendrogram of hclust.average
plot(hclust.average,main="Average")
```

![](Unsupervised_learning_files/figure-gfm/unnamed-chunk-14-2.png)<!-- -->

``` r
# Plot dendrogram of hclust.single
plot(hclust.single,
main="Single")
```

![](Unsupervised_learning_files/figure-gfm/unnamed-chunk-14-3.png)<!-- -->

## Comparing linkage methods

The models you created in the last exercise— `hclust.complete`,
`hclust.average`, and hclust.single—are available in your workspace.

Which linkage(s) produce balanced trees?

## Practical matters: scaling

Recall from the video that clustering real data may require scaling the
features if they have different distributions. So far in this chapter,
you have been working with synthetic data that did not need scaling.

In this exercise, you will go back to working with “real” data, the
pokemon dataset introduced in the first chapter. You will observe the
distribution (mean and standard deviation) of each feature, scale the
data accordingly, then produce a hierarchical clustering model using the
complete linkage method.

``` r
# View column means
library(magrittr)
pokemon %>% colMeans()

# View column standard deviations
apply(pokemon, sd, MARGIN=2)

# Scale the data
pokemon.scaled <- scale(pokemon)

# Create hierarchical clustering model: hclust.pokemon
hclust.pokemon = hclust(dist(pokemon.scaled), method="complete")
```

## Comparing kmeans() and hclust()

Comparing k-means and hierarchical clustering, you’ll see the two
methods produce different cluster memberships. This is because the two
algorithms make different assumptions about how the data is generated.
In a more advanced course, we could choose to use one model over another
based on the quality of the models’ assumptions, but for now, it’s
enough to observe that they are different.

``` r
# Apply cutree() to hclust.pokemon: cut.pokemon
cut.pokemon <- cutree(hclust.pokemon, k=3)

# Compare methods
table(km.pokemon$cluster, cut.pokemon)
```

# Dimensionality reduction by PCA

## Introduction to PCA

  - Two methods of clustering - finding groups of homogeneous items
  - Next up, dimensionality reduction

Dimensionality reduction is

  - A popular method is PCA
  - Three goals when finding lower dimensional representation of
    features
      - Find linear combination of variables to create principla
        components
      - Maintain most variance in the data
      - Principal componnets are uncorrelated (i.e., orthogonal to each
        other)

<!-- end list -->

``` r
pr.iris <- prcomp(
  x = iris[-5],
  scale = FALSE,
  center = TRUE
)

summary(pr.iris)
```

    ## Importance of components:
    ##                           PC1     PC2    PC3     PC4
    ## Standard deviation     2.0563 0.49262 0.2797 0.15439
    ## Proportion of Variance 0.9246 0.05307 0.0171 0.00521
    ## Cumulative Proportion  0.9246 0.97769 0.9948 1.00000

``` r
pr.iris$x %>% 
  plot()
```

![](Unsupervised_learning_files/figure-gfm/unnamed-chunk-17-1.png)<!-- -->

## PCA using prcomp()

In this exercise, you will create your first PCA model and observe the
diagnostic results.

We have loaded the Pokemon data from earlier, which has four dimensions,
and placed it in a variable called pokemon. Your task is to create a PCA
model of the data, then to inspect the resulting model using the
`summary()` function.

``` r
# Perform scaled PCA: pr.out
pokemon <- iris[-5]

pr.out <- prcomp(pokemon, scale=TRUE)

# Inspect model output
summary(pr.out)
```

    ## Importance of components:
    ##                           PC1    PC2     PC3     PC4
    ## Standard deviation     1.7084 0.9560 0.38309 0.14393
    ## Proportion of Variance 0.7296 0.2285 0.03669 0.00518
    ## Cumulative Proportion  0.7296 0.9581 0.99482 1.00000

## Results of PCA

This exercise will check your understanding of the `summary()` of a PCA
model. Your model from the last exercise, pr.out, and the pokemon
dataset are still available in your workspace.

What is the minimum number of principal components that are required to
describe at least 75% of the cumulative variance in this dataset?

## Additional results of PCA

PCA models in R produce additional diagnostic and output components:

  - `center`: the column means used to center to the data, or FALSE if
    the data weren’t centered
  - `scale`: the column standard deviations used to scale the data, or
    FALSE if the data weren’t scaled
  - `rotation`: the directions of the principal component vectors in
    terms of the original features/variables. This information allows
    you to define new data in terms of the original principal components
  - `x`: the value of each observation in the original dataset projected
    to the principal components

You can access these the same as other model components. For example,
use pr.out$rotation to access the rotation component.

Which of the following statements is not correct regarding the pr.out
model fit on the pokemon data?

The directions of the principal component vectors are presented in a
table with the same dimensions as the original data

## Variance explained

The second common plot type for understanding PCA models is a scree
plot. A scree plot shows the variance explained as the number of
principal components increases. Sometimes the cumulative variance
explained is plotted as well.

In this and the next exercise, you will prepare data from the pr.out
model you created at the beginning of the chapter for use in a scree
plot. Preparing the data for plotting is required because there is not a
built-in function in R to create this type of plot.

``` r
# Variability of each principal component: pr.var
pr.var <- pr.out$sdev^2

# Variance explained by each principal component: pve
pve <- pr.var / sum(pr.var)
```

## Visualize variance explained

Now you will create a scree plot showing the proportion of variance
explained by each principal component, as well as the cumulative
proportion of variance explained.

Recall from the video that these plots can help to determine the number
of principal components to retain. One way to determine the number of
principal components to retain is by looking for an elbow in the scree
plot showing that as the number of principal components increases, the
rate at which variance is explained decreases substantially. In the
absence of a clear elbow, you can use the scree plot as a guide for
setting a threshold.

``` r
# Plot variance explained for each principal component
library(magrittr)
pve
```

    ## [1] 0.729624454 0.228507618 0.036689219 0.005178709

``` r
plot(pve, xlab = "Principal Component",
     ylab = "Proportion of Variance Explained",
     ylim = c(0, 1), type = "b")
```

![](Unsupervised_learning_files/figure-gfm/unnamed-chunk-20-1.png)<!-- -->

``` r
# Plot cumulative proportion of variance explained
plot(cumsum(pve), xlab = "Principal Component",
     ylab = "Cumulative Proportion of Variance Explained",
     ylim = c(0, 1), type = "b")
```

![](Unsupervised_learning_files/figure-gfm/unnamed-chunk-20-2.png)<!-- -->

## Practical issues with PCA

  - Scaling the data:
  - Missing values:
      - Drop observations with missing values
      - Impute / estimate missing values
  - categorical data:
      - Do not use categorical data features
      - Encode categorical features as numbers

<!-- end list -->

``` r
data(mtcars)
head(mtcars)
```

    ##                    mpg cyl disp  hp drat    wt  qsec vs am gear carb
    ## Mazda RX4         21.0   6  160 110 3.90 2.620 16.46  0  1    4    4
    ## Mazda RX4 Wag     21.0   6  160 110 3.90 2.875 17.02  0  1    4    4
    ## Datsun 710        22.8   4  108  93 3.85 2.320 18.61  1  1    4    1
    ## Hornet 4 Drive    21.4   6  258 110 3.08 3.215 19.44  1  0    3    1
    ## Hornet Sportabout 18.7   8  360 175 3.15 3.440 17.02  0  0    3    2
    ## Valiant           18.1   6  225 105 2.76 3.460 20.22  1  0    3    1

``` r
# Means and standard deviation vary a lot
round(colMeans(mtcars),2)
```

    ##    mpg    cyl   disp     hp   drat     wt   qsec     vs     am   gear 
    ##  20.09   6.19 230.72 146.69   3.60   3.22  17.85   0.44   0.41   3.69 
    ##   carb 
    ##   2.81

``` r
round(apply(mtcars, 2,sd),2)
```

    ##    mpg    cyl   disp     hp   drat     wt   qsec     vs     am   gear 
    ##   6.03   1.79 123.94  68.56   0.53   0.98   1.79   0.50   0.50   0.74 
    ##   carb 
    ##   1.62

Scaling and PCA in R

``` r
prcomp(x, center = TRUE, scale = FALSE)
```

## Practical issues: scaling

You saw in the video that scaling your data before doing PCA changes the
results of the PCA modeling. Here, you will perform PCA with and without
scaling, then visualize the results using biplots.

Sometimes scaling is appropriate when the variances of the variables are
substantially different. This is commonly the case when variables have
different units of measurement, for example, degrees Fahrenheit
(temperature) and miles (distance). Making the decision to use scaling
is an important step in performing a principal component analysis.

``` r
# Mean of each variable
colMeans(pokemon)
```

    ## Sepal.Length  Sepal.Width Petal.Length  Petal.Width 
    ##     5.843333     3.057333     3.758000     1.199333

``` r
# Standard deviation of each variable
apply(pokemon, 2, sd)
```

    ## Sepal.Length  Sepal.Width Petal.Length  Petal.Width 
    ##    0.8280661    0.4358663    1.7652982    0.7622377

``` r
# PCA model with scaling: pr.with.scaling
pr.with.scaling <- prcomp(pokemon, scale=TRUE)

# PCA model without scaling: pr.without.scaling
pr.without.scaling <- prcomp(pokemon, scale=FALSE)

# Create biplots of both for comparison
biplot(pr.with.scaling)
```

![](Unsupervised_learning_files/figure-gfm/unnamed-chunk-24-1.png)<!-- -->

``` r
biplot(pr.without.scaling)
```

![](Unsupervised_learning_files/figure-gfm/unnamed-chunk-24-2.png)<!-- -->

# Putting it all together with a case study

## Introduction

  - Download data and prepare data for modeling
  - Exploratory data analysis (\#observation, \#features, etc.)
  - Perform PCa nad interpret results
  - Complete two types of clustering
  - Inderstand and compare two types
  - Combine PCA and
clustering

## Preparing the data

``` r
url <- "http://s3.amazonaws.com/assets.datacamp.com/production/course_1903/datasets/WisconsinCancer.csv"

# Download the data: wisc.df
wisc.df <- read.csv(url)

# Convert the features of the data: wisc.data
wisc.data = as.matrix(wisc.df[,3:32])

# Set the row names of wisc.data
row.names(wisc.data) <- wisc.df$id

# Create diagnosis vector
diagnosis <- as.numeric(wisc.df$diagnosis == "M")
```

## EDA

The first step of any data analysis, unsupervised or supervised, is to
familiarize yourself with the data.

The variables you created before, wisc.data and diagnosis, are still
available in your workspace. Explore the data to answer the following
questions:

1.  How many observations are in this dataset?
2.  How many variables/features in the data are suffixed with \_mean?
3.  How many of the observations have a malignant
    diagnosis?

<!-- end list -->

``` r
wisc.df %>% head()
```

    ##         id diagnosis radius_mean texture_mean perimeter_mean area_mean
    ## 1   842302         M       17.99        10.38         122.80    1001.0
    ## 2   842517         M       20.57        17.77         132.90    1326.0
    ## 3 84300903         M       19.69        21.25         130.00    1203.0
    ## 4 84348301         M       11.42        20.38          77.58     386.1
    ## 5 84358402         M       20.29        14.34         135.10    1297.0
    ## 6   843786         M       12.45        15.70          82.57     477.1
    ##   smoothness_mean compactness_mean concavity_mean concave.points_mean
    ## 1         0.11840          0.27760         0.3001             0.14710
    ## 2         0.08474          0.07864         0.0869             0.07017
    ## 3         0.10960          0.15990         0.1974             0.12790
    ## 4         0.14250          0.28390         0.2414             0.10520
    ## 5         0.10030          0.13280         0.1980             0.10430
    ## 6         0.12780          0.17000         0.1578             0.08089
    ##   symmetry_mean fractal_dimension_mean radius_se texture_se perimeter_se
    ## 1        0.2419                0.07871    1.0950     0.9053        8.589
    ## 2        0.1812                0.05667    0.5435     0.7339        3.398
    ## 3        0.2069                0.05999    0.7456     0.7869        4.585
    ## 4        0.2597                0.09744    0.4956     1.1560        3.445
    ## 5        0.1809                0.05883    0.7572     0.7813        5.438
    ## 6        0.2087                0.07613    0.3345     0.8902        2.217
    ##   area_se smoothness_se compactness_se concavity_se concave.points_se
    ## 1  153.40      0.006399        0.04904      0.05373           0.01587
    ## 2   74.08      0.005225        0.01308      0.01860           0.01340
    ## 3   94.03      0.006150        0.04006      0.03832           0.02058
    ## 4   27.23      0.009110        0.07458      0.05661           0.01867
    ## 5   94.44      0.011490        0.02461      0.05688           0.01885
    ## 6   27.19      0.007510        0.03345      0.03672           0.01137
    ##   symmetry_se fractal_dimension_se radius_worst texture_worst
    ## 1     0.03003             0.006193        25.38         17.33
    ## 2     0.01389             0.003532        24.99         23.41
    ## 3     0.02250             0.004571        23.57         25.53
    ## 4     0.05963             0.009208        14.91         26.50
    ## 5     0.01756             0.005115        22.54         16.67
    ## 6     0.02165             0.005082        15.47         23.75
    ##   perimeter_worst area_worst smoothness_worst compactness_worst
    ## 1          184.60     2019.0           0.1622            0.6656
    ## 2          158.80     1956.0           0.1238            0.1866
    ## 3          152.50     1709.0           0.1444            0.4245
    ## 4           98.87      567.7           0.2098            0.8663
    ## 5          152.20     1575.0           0.1374            0.2050
    ## 6          103.40      741.6           0.1791            0.5249
    ##   concavity_worst concave.points_worst symmetry_worst
    ## 1          0.7119               0.2654         0.4601
    ## 2          0.2416               0.1860         0.2750
    ## 3          0.4504               0.2430         0.3613
    ## 4          0.6869               0.2575         0.6638
    ## 5          0.4000               0.1625         0.2364
    ## 6          0.5355               0.1741         0.3985
    ##   fractal_dimension_worst  X
    ## 1                 0.11890 NA
    ## 2                 0.08902 NA
    ## 3                 0.08758 NA
    ## 4                 0.17300 NA
    ## 5                 0.07678 NA
    ## 6                 0.12440 NA

``` r
wisc.df %>% dim()
```

    ## [1] 569  33

## Peform PCA

The next step in your analysis is to perform PCA on wisc.data.

You saw in the last chapter that it’s important to check if the data
need to be scaled before performing PCA. Recall two common reasons for
scaling data:

1.  The input variables use different units of measurement.
2.  The input variables have significantly different variances.

<!-- end list -->

``` r
# Check column means and standard deviations
dim(wisc.data)
```

    ## [1] 569  30

``` r
colMeans(wisc.data)
```

    ##             radius_mean            texture_mean          perimeter_mean 
    ##            1.412729e+01            1.928965e+01            9.196903e+01 
    ##               area_mean         smoothness_mean        compactness_mean 
    ##            6.548891e+02            9.636028e-02            1.043410e-01 
    ##          concavity_mean     concave.points_mean           symmetry_mean 
    ##            8.879932e-02            4.891915e-02            1.811619e-01 
    ##  fractal_dimension_mean               radius_se              texture_se 
    ##            6.279761e-02            4.051721e-01            1.216853e+00 
    ##            perimeter_se                 area_se           smoothness_se 
    ##            2.866059e+00            4.033708e+01            7.040979e-03 
    ##          compactness_se            concavity_se       concave.points_se 
    ##            2.547814e-02            3.189372e-02            1.179614e-02 
    ##             symmetry_se    fractal_dimension_se            radius_worst 
    ##            2.054230e-02            3.794904e-03            1.626919e+01 
    ##           texture_worst         perimeter_worst              area_worst 
    ##            2.567722e+01            1.072612e+02            8.805831e+02 
    ##        smoothness_worst       compactness_worst         concavity_worst 
    ##            1.323686e-01            2.542650e-01            2.721885e-01 
    ##    concave.points_worst          symmetry_worst fractal_dimension_worst 
    ##            1.146062e-01            2.900756e-01            8.394582e-02

``` r
apply(wisc.data, 2, sd)
```

    ##             radius_mean            texture_mean          perimeter_mean 
    ##            3.524049e+00            4.301036e+00            2.429898e+01 
    ##               area_mean         smoothness_mean        compactness_mean 
    ##            3.519141e+02            1.406413e-02            5.281276e-02 
    ##          concavity_mean     concave.points_mean           symmetry_mean 
    ##            7.971981e-02            3.880284e-02            2.741428e-02 
    ##  fractal_dimension_mean               radius_se              texture_se 
    ##            7.060363e-03            2.773127e-01            5.516484e-01 
    ##            perimeter_se                 area_se           smoothness_se 
    ##            2.021855e+00            4.549101e+01            3.002518e-03 
    ##          compactness_se            concavity_se       concave.points_se 
    ##            1.790818e-02            3.018606e-02            6.170285e-03 
    ##             symmetry_se    fractal_dimension_se            radius_worst 
    ##            8.266372e-03            2.646071e-03            4.833242e+00 
    ##           texture_worst         perimeter_worst              area_worst 
    ##            6.146258e+00            3.360254e+01            5.693570e+02 
    ##        smoothness_worst       compactness_worst         concavity_worst 
    ##            2.283243e-02            1.573365e-01            2.086243e-01 
    ##    concave.points_worst          symmetry_worst fractal_dimension_worst 
    ##            6.573234e-02            6.186747e-02            1.806127e-02

``` r
# Execute PCA, scaling if appropriate: wisc.pr
wisc.pr = prcomp(wisc.data, scale=TRUE)

# Look at summary of results
summary(wisc.pr)
```

    ## Importance of components:
    ##                           PC1    PC2     PC3     PC4     PC5     PC6
    ## Standard deviation     3.6444 2.3857 1.67867 1.40735 1.28403 1.09880
    ## Proportion of Variance 0.4427 0.1897 0.09393 0.06602 0.05496 0.04025
    ## Cumulative Proportion  0.4427 0.6324 0.72636 0.79239 0.84734 0.88759
    ##                            PC7     PC8    PC9    PC10   PC11    PC12
    ## Standard deviation     0.82172 0.69037 0.6457 0.59219 0.5421 0.51104
    ## Proportion of Variance 0.02251 0.01589 0.0139 0.01169 0.0098 0.00871
    ## Cumulative Proportion  0.91010 0.92598 0.9399 0.95157 0.9614 0.97007
    ##                           PC13    PC14    PC15    PC16    PC17    PC18
    ## Standard deviation     0.49128 0.39624 0.30681 0.28260 0.24372 0.22939
    ## Proportion of Variance 0.00805 0.00523 0.00314 0.00266 0.00198 0.00175
    ## Cumulative Proportion  0.97812 0.98335 0.98649 0.98915 0.99113 0.99288
    ##                           PC19    PC20   PC21    PC22    PC23   PC24
    ## Standard deviation     0.22244 0.17652 0.1731 0.16565 0.15602 0.1344
    ## Proportion of Variance 0.00165 0.00104 0.0010 0.00091 0.00081 0.0006
    ## Cumulative Proportion  0.99453 0.99557 0.9966 0.99749 0.99830 0.9989
    ##                           PC25    PC26    PC27    PC28    PC29    PC30
    ## Standard deviation     0.12442 0.09043 0.08307 0.03987 0.02736 0.01153
    ## Proportion of Variance 0.00052 0.00027 0.00023 0.00005 0.00002 0.00000
    ## Cumulative Proportion  0.99942 0.99969 0.99992 0.99997 1.00000 1.00000

## Interpreting PCA results

Now you’ll use some visualizations to better understand your PCA model.
You were introduced to one of these visualizations, the biplot, in an
earlier chapter.

You’ll run into some common challenges with using biplots on real-world
data containing a non-trivial number of observations and variables, then
you’ll look at some alternative visualizations. You are encouraged to
experiment with additional visualizations before moving on to the next
exercise.

``` r
# Create a biplot of wisc.pr
biplot(wisc.pr)
```

![](Unsupervised_learning_files/figure-gfm/unnamed-chunk-28-1.png)<!-- -->

``` r
wisc.pr$x %>% head()
```

    ##                PC1        PC2        PC3       PC4        PC5         PC6
    ## 842302   -9.184755  -1.946870 -1.1221788 3.6305364  1.1940595  1.41018364
    ## 842517   -2.385703   3.764859 -0.5288274 1.1172808 -0.6212284  0.02863116
    ## 84300903 -5.728855   1.074229 -0.5512625 0.9112808  0.1769302  0.54097615
    ## 84348301 -7.116691 -10.266556 -3.2299475 0.1524129  2.9582754  3.05073750
    ## 84358402 -3.931842   1.946359  1.3885450 2.9380542 -0.5462667 -1.22541641
    ## 843786   -2.378155  -3.946456 -2.9322967 0.9402096  1.0551135 -0.45064213
    ##                  PC7         PC8         PC9       PC10       PC11
    ## 842302    2.15747152  0.39805698 -0.15698023 -0.8766305 -0.2627243
    ## 842517    0.01334635 -0.24077660 -0.71127897  1.1060218 -0.8124048
    ## 84300903 -0.66757908 -0.09728813  0.02404449  0.4538760  0.6050715
    ## 84348301  1.42865363 -1.05863376 -1.40420412 -1.1159933  1.1505012
    ## 84358402 -0.93538950 -0.63581661 -0.26357355  0.3773724 -0.6507870
    ## 843786    0.49001396  0.16529843 -0.13335576 -0.5299649 -0.1096698
    ##                PC12        PC13         PC14         PC15        PC16
    ## 842302   -0.8582593  0.10329677 -0.690196797  0.601264078  0.74446075
    ## 842517    0.1577838 -0.94269981 -0.652900844 -0.008966977 -0.64823831
    ## 84300903  0.1242777 -0.41026561  0.016665095 -0.482994760  0.32482472
    ## 84348301  1.0104267 -0.93245070 -0.486988399  0.168699395  0.05132509
    ## 84358402 -0.1104183  0.38760691 -0.538706543 -0.310046684 -0.15247165
    ## 843786    0.0813699 -0.02625135  0.003133944 -0.178447576 -0.01270566
    ##                 PC17        PC18       PC19        PC20         PC21
    ## 842302   -0.26523740 -0.54907956  0.1336499  0.34526111  0.096430045
    ## 842517   -0.01719707  0.31801756 -0.2473470 -0.11403274 -0.077259494
    ## 84300903  0.19075064 -0.08789759 -0.3922812 -0.20435242  0.310793246
    ## 84348301  0.48220960 -0.03584323 -0.0267241 -0.46432511  0.433811661
    ## 84358402  0.13302526 -0.01869779  0.4610302  0.06543782 -0.116442469
    ## 843786    0.19671335 -0.29727706 -0.1297265 -0.07117453 -0.002400178
    ##                 PC22        PC23         PC24         PC25         PC26
    ## 842302   -0.06878939  0.08444429  0.175102213  0.150887294 -0.201326305
    ## 842517    0.09449530 -0.21752666 -0.011280193  0.170360355 -0.041092627
    ## 84300903  0.06025601 -0.07422581 -0.102671419 -0.171007656  0.004731249
    ## 84348301  0.20308706 -0.12399554 -0.153294780 -0.077427574 -0.274982822
    ## 84358402  0.01763433  0.13933105  0.005327110 -0.003059371  0.039219780
    ## 843786    0.10108043  0.03344819 -0.002837749 -0.122282765 -0.030272333
    ##                 PC27          PC28         PC29          PC30
    ## 842302   -0.25236294 -0.0338846387  0.045607590  0.0471277407
    ## 842517    0.18111081  0.0325955021 -0.005682424  0.0018662342
    ## 84300903  0.04952586  0.0469844833  0.003143131 -0.0007498749
    ## 84348301  0.18330078  0.0424469831 -0.069233868  0.0199198881
    ## 84358402  0.03213957 -0.0347556386  0.005033481 -0.0211951203
    ## 843786   -0.08438081  0.0007296587 -0.019703996 -0.0034564331

``` r
# Scatter plot observations by components 1 and 2
plot(wisc.pr$x[, c(1, 2)], col = (diagnosis + 1), 
     xlab = "PC1", ylab = "PC2")
```

![](Unsupervised_learning_files/figure-gfm/unnamed-chunk-28-2.png)<!-- -->

``` r
# Repeat for components 1 and 3
plot(wisc.pr$x[,c(1,3)], col = (diagnosis + 1), 
     xlab = "PC1", ylab = "PC3")
```

![](Unsupervised_learning_files/figure-gfm/unnamed-chunk-28-3.png)<!-- -->

``` r
# Do additional data exploration of your choosing below (optional)
```

## Variance explained

n this exercise, you will produce scree plots showing the proportion of
variance explained as the number of principal components increases. The
data from PCA must be prepared for these plots, as there is not a
built-in function in R to create them directly from the PCA model.

As you look at these plots, ask yourself if there’s an elbow in the
amount of variance explained that might lead you to pick a natural
number of principal components. If an obvious elbow does not exist, as
is typical in real-world datasets, consider how else you might determine
the number of principal components to retain based on the scree plot.

``` r
# Set up 1 x 2 plotting grid
par(mfrow = c(1, 2))

# Calculate variability of each component
summary(wisc.pr)
```

    ## Importance of components:
    ##                           PC1    PC2     PC3     PC4     PC5     PC6
    ## Standard deviation     3.6444 2.3857 1.67867 1.40735 1.28403 1.09880
    ## Proportion of Variance 0.4427 0.1897 0.09393 0.06602 0.05496 0.04025
    ## Cumulative Proportion  0.4427 0.6324 0.72636 0.79239 0.84734 0.88759
    ##                            PC7     PC8    PC9    PC10   PC11    PC12
    ## Standard deviation     0.82172 0.69037 0.6457 0.59219 0.5421 0.51104
    ## Proportion of Variance 0.02251 0.01589 0.0139 0.01169 0.0098 0.00871
    ## Cumulative Proportion  0.91010 0.92598 0.9399 0.95157 0.9614 0.97007
    ##                           PC13    PC14    PC15    PC16    PC17    PC18
    ## Standard deviation     0.49128 0.39624 0.30681 0.28260 0.24372 0.22939
    ## Proportion of Variance 0.00805 0.00523 0.00314 0.00266 0.00198 0.00175
    ## Cumulative Proportion  0.97812 0.98335 0.98649 0.98915 0.99113 0.99288
    ##                           PC19    PC20   PC21    PC22    PC23   PC24
    ## Standard deviation     0.22244 0.17652 0.1731 0.16565 0.15602 0.1344
    ## Proportion of Variance 0.00165 0.00104 0.0010 0.00091 0.00081 0.0006
    ## Cumulative Proportion  0.99453 0.99557 0.9966 0.99749 0.99830 0.9989
    ##                           PC25    PC26    PC27    PC28    PC29    PC30
    ## Standard deviation     0.12442 0.09043 0.08307 0.03987 0.02736 0.01153
    ## Proportion of Variance 0.00052 0.00027 0.00023 0.00005 0.00002 0.00000
    ## Cumulative Proportion  0.99942 0.99969 0.99992 0.99997 1.00000 1.00000

``` r
pr.var <- wisc.pr$sdev^2
pr.var
```

    ##  [1] 1.328161e+01 5.691355e+00 2.817949e+00 1.980640e+00 1.648731e+00
    ##  [6] 1.207357e+00 6.752201e-01 4.766171e-01 4.168948e-01 3.506935e-01
    ## [11] 2.939157e-01 2.611614e-01 2.413575e-01 1.570097e-01 9.413497e-02
    ## [16] 7.986280e-02 5.939904e-02 5.261878e-02 4.947759e-02 3.115940e-02
    ## [21] 2.997289e-02 2.743940e-02 2.434084e-02 1.805501e-02 1.548127e-02
    ## [26] 8.177640e-03 6.900464e-03 1.589338e-03 7.488031e-04 1.330448e-04

``` r
# Variance explained by each principal component: pve
pve <- pr.var/sum(pr.var) 

# Plot variance explained for each principal component
plot(pve, xlab = "Principal Component", 
     ylab = "Proportion of Variance Explained", 
     ylim = c(0, 1), type = "b")

# Plot cumulative proportion of variance explained
plot(cumsum(pve), xlab = "Principal Component", 
     ylab = "Cumulative Proportion of Variance Explained", 
     ylim = c(0, 1), type = "b")
```

![](Unsupervised_learning_files/figure-gfm/unnamed-chunk-29-1.png)<!-- -->

## Communicating PCA results

This exercise will check your understanding of the PCA results, in
particular the loadings and variance explained. The loadings,
represented as vectors, explain the mapping from the original features
to the principal components. The principal components are naturally
ordered from the most variance explained to the least variance
explained.

The variables you created before—wisc.data, diagnosis, wisc.pr, and
pve—are still available.

For the first principal component, what is the component of the loading
vector for the feature concave.points\_mean? What is the minimum number
of principal components required to explain 80% of the variance of the
data?

## PCA review and next steps

  - Complete hierchacical clustering
  - Complete k-means clustering
  - Combine PCA and clustering
  - contrast results of hierchacial clustering with diagnosis
  - compare hierachical and k-means clustering reuslts
  - PCA as a pre-procesing step for clustering

<!-- end list -->

``` r
# pair-wise distance between observation - Euclidiean dsitance
s <- dist(x)
hclust(x)

# one observation per row, one feature per column
# k-means has arandom component
# run algorithm multiple times to improve odds of the best model
```

## Hierachical clustering of case data

The goal of this exercise is to do hierarchical clustering of the
observations. Recall from Chapter 2 that this type of clustering does
not assume in advance the number of natural groups that exist in the
data.

As part of the preparation for hierarchical clustering, distance between
all pairs of observations are computed. Furthermore, there are different
ways to link clusters together, with `single`, `complete`, and `average`
being the most common linkage methods.

``` r
# Scale the wisc.data data: data.scaled
data.scaled <- scale(wisc.data)

# Calculate the (Euclidean) distances: data.dist
data.dist <- dist(data.scaled)

# Create a hierarchical clustering model: wisc.hclust
wisc.hclust <- hclust(data.dist, method ="complete")
```

## Results of hierachical clustering

Let’s use the hierarchical clustering model you just created to
determine a height (or distance between clusters) where a certain number
of clusters exists. The variables you created before—`wisc.data`,
`diagnosis`, `wisc.pr`, `pve`, and `wisc.hclust` —are all available in
your workspace.

Using the `plot()` function, what is the height at which the clustering
model has 4
clusters?

``` r
plot(wisc.hclust)
```

![](Unsupervised_learning_files/figure-gfm/unnamed-chunk-32-1.png)<!-- -->

## Selecting number of clusters

In this exercise, you will compare the outputs from your hierarchical
clustering model to the actual diagnoses. Normally when performing
unsupervised learning like this, a target variable isn’t available. We
do have it with this dataset, however, so it can be used to check the
performance of the clustering model.

When performing supervised learning—that is, when you’re trying to
predict some target variable of interest and that target variable is
available in the original data—using clustering to create new features
may or may not improve the performance of the final model. This exercise
will help you determine if, in this case, hierarchical clustering
provides a promising new feature.

``` r
# Cut tree so that it has 4 clusters: wisc.hclust.clusters
wisc.hclust.clusters <- cutree(wisc.hclust, k=4)

# Compare cluster membership to actual diagnoses
table(wisc.hclust.clusters, diagnosis)
```

    ##                     diagnosis
    ## wisc.hclust.clusters   0   1
    ##                    1  12 165
    ##                    2   2   5
    ##                    3 343  40
    ##                    4   0   2

## k-means clustering and comparing results

As you now know, there are two main types of clustering: hierarchical
and k-means.

In this exercise, you will create a k-means clustering model on the
Wisconsin breast cancer data and compare the results to the actual
diagnoses and the results of your hierarchical clustering model. Take
some time to see how each clustering model performs in terms of
separating the two diagnoses and how the clustering models compare to
each other.

``` r
# Create a k-means model on wisc.data: wisc.km
wisc.km <- kmeans(scale(wisc.data), centers=2, nstart=20)

# Compare k-means to actual diagnoses
table(wisc.km$cluster, diagnosis)
```

    ##    diagnosis
    ##       0   1
    ##   1 343  37
    ##   2  14 175

``` r
# Compare k-means to hierarchical clustering
table(wisc.km$cluster, wisc.hclust.clusters)
```

    ##    wisc.hclust.clusters
    ##       1   2   3   4
    ##   1  17   0 363   0
    ##   2 160   7  20   2

## Clustering on PCA results

In this final exercise, you will put together several steps you used
earlier and, in doing so, you will experience some of the creativity
that is typical in unsupervised learning.

Recall from earlier exercises that the PCA model required significantly
fewer features to describe 80% and 95% of the variability of the data.
In addition to normalizing data and potentially avoiding overfitting,
PCA also uncorrelates the variables, sometimes improving the performance
of other modeling techniques.

Let’s see if PCA improves or degrades the performance of hierarchical
clustering.

``` r
# Create a hierarchical clustering model: wisc.pr.hclust
wisc.pr.hclust <- hclust(dist(wisc.pr$x[, 1:7]), method = "complete")

# Cut model into 4 clusters: wisc.pr.hclust.clusters
wisc.pr.hclust.clusters <- cutree(wisc.pr.hclust, k = 4)

# Compare to actual diagnoses
table(diagnosis, wisc.pr.hclust.clusters)
```

    ##          wisc.pr.hclust.clusters
    ## diagnosis   1   2   3   4
    ##         0   5 350   2   0
    ##         1 113  97   0   2

``` r
# Compare to k-means and hierarchical
table(diagnosis, wisc.hclust.clusters)
```

    ##          wisc.hclust.clusters
    ## diagnosis   1   2   3   4
    ##         0  12   2 343   0
    ##         1 165   5  40   2

``` r
table(diagnosis, wisc.km$cluster)
```

    ##          
    ## diagnosis   1   2
    ##         0 343  14
    ##         1  37 175

Fin.
