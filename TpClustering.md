Le travail à faire réalisé par *BAHRI Khalid*
---

```r
library(learnr)
library(tidyverse)
library(stats)
#library(FactoMineR)
library(factoextra)
#library(corrplot)
tutorial_options(exercise.timelimit = 60)
knitr::opts_chunk$set(error = TRUE)
```

## __Introduction__

## __Lecture des données__

```r
library(stats)  
library(factoextra)   
```


```r
data(decathlon2[-13])
str(decathlon2[-13])
head(decathlon2[-13])
tail(decathlon2[-13])
colnames(decathlon2[-13])
```


## __K-means__

### __Estimation du nombre optimal de clusters__
  
```r

```

```r
library(factoextra)    
data("decathlon2") # Loading the data set    
df <- scale(decathlon2[-13]) # Scaling the data   

fviz_nbclust(df, kmeans, method = "wss") +
geom_vline(xintercept = 5, linetype = 2)
```
![](./tpc1.png)
### __Calcul du clustering k-means __

```r
set.seed(123) 
df <- scale(decathlon2[-13])
km.res <- kmeans(df, 5, nstart = 25)  
print(km.res) 
```
![](./tpc2.png)
```r
set.seed(123) 
df <- scale(decathlon2[-13])
km.res <- kmeans(df, 5, nstart = 25) 
```


```r
aggregate(decathlon2[-13], by=list(cluster=km.res$cluster), mean) 
aggregate(decathlon2[-13], by=list(cluster=km.res$cluster), sd) 
decathlon2_C=cbind(decathlon2[-13], cluster=as.factor(km.res$cluster)) 
ggplot(decathlon2_C, aes(y=X400m, fill=cluster)) + geom_boxplot()  
ggplot(decathlon2_C, aes(y=Shot.put, fill=cluster)) + geom_boxplot()  
```
```r

for(i in c(1:5)){
     var=colnames(decathlon2_C)[i] 
     print(ggplot(decathlon2_C, aes(y=decathlon2_C[[i]], fill=cluster)) + 
     geom_boxplot()+ ylab(var)) 
     } 
```
![](./tpc3.png)
![](./tpc4.png)

### __Accès aux résultats de la fonction `kmeans()`__   

```r
km.res$cluster
km.res$centers
```
![](./tpc5.png)

### __Visualisation des clusters produits par `kmeans()`__   


```r
fviz_cluster(km.res, data = df,
palette = c("#2E9FDF", "#00AFBB", "#E7B800", "#FC4E07","#112233"),
ellipse.type = "euclid", # Concentration ellipse
star.plot = TRUE, # Add segments from centroids to items
repel = TRUE, # Avoid label overplotting (slow)
ggtheme = theme_minimal()
)  
```
![](./tpc6.png)
## __Clustering hiérarchique__

```r
set.seed(123) 
df <- scale(decathlon2[-13])
km.res <- kmeans(df, 5, nstart = 25) 
res.dist <- dist(df, method = "euclidean") 
res.hc <- hclust(d = res.dist, method = "ward.D2") 
grp <- cutree(res.hc, k = 5)
```

### __Dendrogramme__


```r
fviz_dend(res.hc, cex = 0.5)  
```
![](./tpc7.png)


### __Vérification de l’arborescence du clustering__


```r
res.coph <- cophenetic(res.hc)     
cor(res.dist, res.coph)
```
![](./tpc8.png)


```r
res.hc2 <- hclust(res.dist, method = "average") 
cor(res.dist, cophenetic(res.hc2)) 
```
![](./tpc9.png)


### __découpage du dendrogramme en clusters__




```r
grp <- cutree(res.hc, k = 5)  
head(grp, n = 10) 
table(grp)    
rownames(df)[grp == 1]
```
![](tpc10.png)

```r 
fviz_dend(res.hc, k = 5, 
          cex = 0.5, 
          k_colors = c("#2E9FDF", "#00AFBB", "#E7B800", "#FC4E07","#112233"),
          color_labels_by_k = TRUE, 
          rect = TRUE 
          )
```
![](./tpc11.png)


```r
fviz_cluster(list(data = df, cluster = grp),
             palette = c("#2E9FDF", "#00AFBB", "#E7B800", "#FC4E07","#112233"),
             ellipse.type = "convex", 
             repel = TRUE, 
             show.clust.cent = FALSE, ggtheme = theme_minimal())
```
![](./tpc12.png)

## __Hierarchical K-Means Clustering__

 
```r
res.hk <-hkmeans(df, 5) 
names(res.hk) 
res.hk
```

### __Visualisation des résultats de `hkmeans`__

```r
set.seed(123) 
df <- scale(decathlon2[-13])
km.res <- kmeans(df, 5, nstart = 25) 
res.dist <- dist(df, method = "euclidean") 
res.hk <-hkmeans(df, 5) 
```
![](./tpc13.png)

```r
fviz_dend(res.hk, cex = 0.6, palette = "jco",rect = TRUE, 
          rect_border = "jco", rect_fill = TRUE)
fviz_cluster(res.hk, palette = "jco", repel = TRUE,
             ggtheme = theme_classic())
```
![](./tpc14.png)