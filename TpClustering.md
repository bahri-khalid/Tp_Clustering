Le travail à faire réalisé par *BAHRI Khalid*
---

```r
library(learnr)
library(tidyverse)
library(stats)
library(factoextra)
tutorial_options(exercise.timelimit = 60)
knitr::opts_chunk$set(error = TRUE)
```

## __Introduction__

## __Lecture des données__

```r
library(stats)  
library(factoextra)   
```
## Nous utiliserons le jeu de données de démonstration `decathlon2` du package `factoextra`.  

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
![](https://github.com/bahri-khalid/Tp_Clustering/raw/master/tpc1.png)
### __Calcul du clustering k-means __

## Le grapheique ci-dessus donne les valeurs de la variance "`WSS`" dans les clusters pour differents valeurs de `k`, et on peut remarquer que pour les valeurs de k plus grand que 5 la variance interne est trés impotante alors que les valeurs au-delà de 5 on peu de valeur.
```r
set.seed(123) 
df <- scale(decathlon2[-13])
km.res <- kmeans(df, 5, nstart = 25)  
print(km.res) 
```
![](https://github.com/bahri-khalid/Tp_Clustering/raw/master/tpc2.png)
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
<!-- ```r

for(i in c(1:5)){
     var=colnames(decathlon2_C)[i] 
     print(ggplot(decathlon2_C, aes(y=decathlon2_C[[i]], fill=cluster)) + 
     geom_boxplot()+ ylab(var)) 
     } 
``` -->
![](https://github.com/bahri-khalid/Tp_Clustering/raw/master/tpc3.png)
![](https://github.com/bahri-khalid/Tp_Clustering/raw/master/tpc4.png)

## D'après les graphiques ci-dessus on peut visualiser la distribution des variables de notre jeu de données dans chaque cluster,par exemple pour le graphique du variable **`Shot.put`** on remarque que les valeurs sont parfaitement  distribuer dans le cluster `4,5` et un peut moin distribuer dans `1,2` alors que il ya une distribution déséquilibré au niveau du cluster `3`.


### __Accès aux résultats de la fonction `kmeans()`__   

```r
km.res$cluster
km.res$centers
```
![](https://github.com/bahri-khalid/Tp_Clustering/raw/master/tpc5.png)

### __Visualisation des clusters produits par `kmeans()`__   


```r
fviz_cluster(km.res, data = df,
palette = c("#2E9FDF", "#00AFBB", "#E7B800", "#FC4E07","#112233"),
ellipse.type = "euclid", 
star.plot = TRUE, 
repel = TRUE, 
ggtheme = theme_minimal()
)  
```
![](https://github.com/bahri-khalid/Tp_Clustering/raw/master/tpc6.png)
## Dans ce graphique on a une visualisation sur deux composants principaux des clusters en utilisant le nuage de points et une coloration de que point de données en fonction de son cluster, entourer par des ellipses de concentration, on remaque que notre choix de `k = 5` nous faire sortir trois cluster bien séparé `(1,3 et 4)` et deux autres cluster un peu fusionnés.


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
![](https://github.com/bahri-khalid/Tp_Clustering/raw/master/tpc7.png)

## D'aprés la matrice de dissimilarité calculer par la fonction `dist()` on peut construire l'arbre hiérarchique ci-dessus, on peut remarquer qu'on a presque les mêmes resultats que les graphiques presedents, par exemple *`Clay`* et  *`Sebrle`*  sont réunis dans la même branche anissi que *`Kapov`* , et ces trois elements constituent le premier cluster.

### __Vérification de l’arborescence du clustering__


```r
res.coph <- cophenetic(res.hc)     
cor(res.dist, res.coph)
```
![](https://github.com/bahri-khalid/Tp_Clustering/raw/master/tpc8.png)


```r
res.hc2 <- hclust(res.dist, method = "average") 
cor(res.dist, cophenetic(res.hc2)) 
```
![](https://github.com/bahri-khalid/Tp_Clustering/raw/master/tpc9.png)

## On remarque que le coefficient de corrélation montre que l’utilisation d’une méthode de liaison/linkage différente crée un arbre qui représente légèrement mieux les distances initiales.

### __découpage du dendrogramme en clusters__




```r
grp <- cutree(res.hc, k = 5)  
head(grp, n = 10) 
table(grp)    
rownames(df)[grp == 1]
```
![](https://github.com/bahri-khalid/Tp_Clustering/raw/master/tpc10.png)

```r 
fviz_dend(res.hc, k = 5, 
          cex = 0.5, 
          k_colors = c("#2E9FDF", "#00AFBB", "#E7B800", "#FC4E07","#112233"),
          color_labels_by_k = TRUE, 
          rect = TRUE 
          )
```
![](https://github.com/bahri-khalid/Tp_Clustering/raw/master/tpc11.png)

## En utilisant le fonction `cutree()` on peut decouper l'arbre généré en plusieurs clusters .

```r
fviz_cluster(list(data = df, cluster = grp),
             palette = c("#2E9FDF", "#00AFBB", "#E7B800", "#FC4E07","#112233"),
             ellipse.type = "convex", 
             repel = TRUE, 
             show.clust.cent = FALSE, ggtheme = theme_minimal())
```
![](https://github.com/bahri-khalid/Tp_Clustering/raw/master/tpc12.png)
## visualisation de la résultat dans un diagrame de dispersion.
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
![](https://github.com/bahri-khalid/Tp_Clustering/raw/master/tpc13.png)

```r
fviz_dend(res.hk, cex = 0.6, palette = "jco",rect = TRUE, 
          rect_border = "jco", rect_fill = TRUE)
fviz_cluster(res.hk, palette = "jco", repel = TRUE,
             ggtheme = theme_classic())
```
![](https://github.com/bahri-khalid/Tp_Clustering/raw/master/tpc14.png)

## On remarque que aprés la combinaison du k-means et le Clustering hiérarchique on obtient des resultats et des visualisation plus significatif et plus stable surtout au niveau de diagrame de dispersion, il ya un grand difference dans la qualité de la representation.