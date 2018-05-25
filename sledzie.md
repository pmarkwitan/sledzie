---
title: "Śledzie"
author: "Piotr Markwitan"
date: '26 maj 2018'
output: 
  html_document: 
    keep_md: yes
    toc: yes
    toc_depth: 3
    number_sections: no
    
---
W raporcie wykorzystano następujące biblioteki języka R:

```r
library(ggplot2)
library(plotly)
library(dplyr)
#REM library(stringr)
library(caret)
```


#Podsumowanie
Celem ćwiczenia jest analiza wskazanego pliku pod kątem wpływu czynników zewnętrznych na rozmiar śledzia atlantyckiego. Na wstępie zbadano zmiany wielkości łowionych śledzi w kolejnych pomiarach. Widać, że do ok. 40% pomiarów średnia długość śledzia rośnie. Potem spada i jest to trwały trend. Analiza parametrów wykazała, że największy wpływ na rozmiar śledzi ma temperatura wody przy powierzchni. Wzrost temperatury powoduje zmniejszenie średniej długości śledzia atlantyckiego.

#Analiza danych źródłowych
##Dane źródłowe
Dane źródłowe są dostępne w pliku http://www.cs.put.poznan.pl/dbrzezinski/teaching/sphd/sledzie.csv
Zgodnie z opisem zawiera on pomiary złowionych śledzi oraz opis warunków.
Kolejne kolumny w zbiorze danych to:

- length: długość złowionego śledzia [cm];
- cfin1: dostępność planktonu [zagęszczenie Calanus finmarchicus gat. 1];
- cfin2: dostępność planktonu [zagęszczenie Calanus finmarchicus gat. 2];
- chel1: dostępność planktonu [zagęszczenie Calanus helgolandicus gat. 1];
- chel2: dostępność planktonu [zagęszczenie Calanus helgolandicus gat. 2];
- lcop1: dostępność planktonu [zagęszczenie widłonogów gat. 1];
- lcop2: dostępność planktonu [zagęszczenie widłonogów gat. 2];
- fbar: natężenie połowów w regionie [ułamek pozostawionego narybku];
- recr: roczny narybek [liczba śledzi];
- cumf: łączne roczne natężenie połowów w regionie [ułamek pozostawionego narybku];
- totaln: łączna liczba ryb złowionych w ramach połowu [liczba śledzi];
- sst: temperatura przy powierzchni wody [°C];
- sal: poziom zasolenia wody [Knudsen ppt];
- xmonth: miesiąc połowu [numer miesiąca];
- nao: oscylacja północnoatlantycka [mb].

Wczytany zbiór ma 52582 wierszy.

Wyniki pomiarów są zapisane chronologicznie można zatem sprawdzić czy da się zauważyć zmianę długości łowionych śledzi i ewentualny trend.
<!--html_preserve--><div id="28d46d5b390d" style="width:672px;height:480px;" class="plotly html-widget"></div>

Spadek długości śledzi w ostatnich latach jest zauważalny i widać stały trend spadkowy.

##Próbka oryginalnych danych
Wycinek pliku źródłowego:

```
##   X length   cfin1   cfin2   chel1    chel2   lcop1    lcop2  fbar   recr
## 1 0   23.0 0.02778 0.27785 2.46875       NA 2.54787 26.35881 0.356 482831
## 2 1   22.5 0.02778 0.27785 2.46875 21.43548 2.54787 26.35881 0.356 482831
## 3 2   25.0 0.02778 0.27785 2.46875 21.43548 2.54787 26.35881 0.356 482831
## 4 3   25.5 0.02778 0.27785 2.46875 21.43548 2.54787 26.35881 0.356 482831
## 5 4   24.0 0.02778 0.27785 2.46875 21.43548 2.54787 26.35881 0.356 482831
## 6 5   22.0 0.02778 0.27785 2.46875 21.43548 2.54787       NA 0.356 482831
##        cumf   totaln      sst      sal xmonth nao
## 1 0.3059879 267380.8 14.30693 35.51234      7 2.8
## 2 0.3059879 267380.8 14.30693 35.51234      7 2.8
## 3 0.3059879 267380.8 14.30693 35.51234      7 2.8
## 4 0.3059879 267380.8 14.30693 35.51234      7 2.8
## 5 0.3059879 267380.8 14.30693 35.51234      7 2.8
## 6 0.3059879 267380.8 14.30693 35.51234      7 2.8
```

##Podsumowanie oryginalnych danych
Podstawowe parametry wczytanych wartości:

```
##        X             length         cfin1             cfin2        
##  Min.   :    0   Min.   :19.0   Min.   : 0.0000   Min.   : 0.0000  
##  1st Qu.:13145   1st Qu.:24.0   1st Qu.: 0.0000   1st Qu.: 0.2778  
##  Median :26291   Median :25.5   Median : 0.1111   Median : 0.7012  
##  Mean   :26291   Mean   :25.3   Mean   : 0.4458   Mean   : 2.0248  
##  3rd Qu.:39436   3rd Qu.:26.5   3rd Qu.: 0.3333   3rd Qu.: 1.7936  
##  Max.   :52581   Max.   :32.5   Max.   :37.6667   Max.   :19.3958  
##                                 NA's   :1581      NA's   :1536     
##      chel1            chel2            lcop1              lcop2       
##  Min.   : 0.000   Min.   : 5.238   Min.   :  0.3074   Min.   : 7.849  
##  1st Qu.: 2.469   1st Qu.:13.427   1st Qu.:  2.5479   1st Qu.:17.808  
##  Median : 5.750   Median :21.673   Median :  7.0000   Median :24.859  
##  Mean   :10.006   Mean   :21.221   Mean   : 12.8108   Mean   :28.419  
##  3rd Qu.:11.500   3rd Qu.:27.193   3rd Qu.: 21.2315   3rd Qu.:37.232  
##  Max.   :75.000   Max.   :57.706   Max.   :115.5833   Max.   :68.736  
##  NA's   :1555     NA's   :1556     NA's   :1653       NA's   :1591    
##       fbar             recr              cumf             totaln       
##  Min.   :0.0680   Min.   : 140515   Min.   :0.06833   Min.   : 144137  
##  1st Qu.:0.2270   1st Qu.: 360061   1st Qu.:0.14809   1st Qu.: 306068  
##  Median :0.3320   Median : 421391   Median :0.23191   Median : 539558  
##  Mean   :0.3304   Mean   : 520367   Mean   :0.22981   Mean   : 514973  
##  3rd Qu.:0.4560   3rd Qu.: 724151   3rd Qu.:0.29803   3rd Qu.: 730351  
##  Max.   :0.8490   Max.   :1565890   Max.   :0.39801   Max.   :1015595  
##                                                                        
##       sst             sal            xmonth            nao          
##  Min.   :12.77   Min.   :35.40   Min.   : 1.000   Min.   :-4.89000  
##  1st Qu.:13.60   1st Qu.:35.51   1st Qu.: 5.000   1st Qu.:-1.89000  
##  Median :13.86   Median :35.51   Median : 8.000   Median : 0.20000  
##  Mean   :13.87   Mean   :35.51   Mean   : 7.258   Mean   :-0.09236  
##  3rd Qu.:14.16   3rd Qu.:35.52   3rd Qu.: 9.000   3rd Qu.: 1.63000  
##  Max.   :14.73   Max.   :35.61   Max.   :12.000   Max.   : 5.08000  
##  NA's   :1584
```

##Korelacja wartości wejściowych

```
##        wsp. korelacji
## X         -0.33913000
## length     1.00000000
## cfin1      0.08122553
## cfin2      0.09832515
## chel1      0.22091226
## chel2     -0.01430766
## lcop1      0.23775402
## lcop2      0.04894328
## fbar       0.25697135
## recr      -0.01034244
## cumf       0.01152544
## totaln     0.09605811
## sst       -0.45167059
## sal        0.03223550
## xmonth     0.01371195
## nao       -0.25684475
```
Z powyższej analizy widać, że najbardziej skorelowanym z długością śledzi jest parametr _sst_ oznaczający temperaturę oceanu. Drugim parametrem jest _X_ określający numer wiersza. Z oczywistych przyczyn nie będziemy zajmować się analizą parametru porządkowego. Parametry _fbar_ i _nao_ mają prawie identyczną korelację z długością śledzi. Do dalszej analizy wybieramy _fbar_ ponieważ _nao_ jest silnie skorelowany z _sst_.
![](sledzie_files/figure-html/unnamed-chunk-3-1.png)<!-- -->![](sledzie_files/figure-html/unnamed-chunk-3-2.png)<!-- -->

##Histogramy
![](sledzie_files/figure-html/histogram-1.png)<!-- -->![](sledzie_files/figure-html/histogram-2.png)<!-- -->

##Czyszczenie danych wejściowych
Po analizie korelacji zdecydowano o wyborze następujących parametrów:

- X: jako parametr porządkowy
- fbar: natężenie połowów w regionie [ułamek pozostawionego narybku];
- sst: temperatura przy powierzchni wody [°C];


Jak widać w podsumowaniu danych tylko parametr _sst_ ma puste wartości. Ze względu na niewielką ich liczbę w porównaniu do całkowitego wolumenu (1584/52582) zdecydowano o wykluczeniu tych pomiarów z dalszej analizy. W sytuacji, gdy pustych wartości byłoby więcej należałoby zastosować inne podejście, np. wypełnienie średnią wartością, średnią kroczącą itp.


#Regresja
Utworzenie regresora z  wykorzystaniem metody kNN.

```r
set.seed(23)
inTraining <- 
    createDataPartition(
        y = df2$length,
        p = .75,
        list = FALSE)

training <- df2[ inTraining,]
testing  <- df2[-inTraining,]

ctrl <- trainControl(
    # powtórzona ocena krzyżowa
    method = "repeatedcv",
    # liczba podziałów
    number = 2,
    # liczba powtórzeń
    repeats = 5)

fit <- train(length ~ .,
             data = training,
             method = "knn",
             trControl = ctrl,
             preProcess = c("center","scale"),
             tuneLength = 5)
```
Parametry regresora:

```
## k-Nearest Neighbors 
## 
## 38250 samples
##     4 predictor
## 
## Pre-processing: centered (4), scaled (4) 
## Resampling: Cross-Validated (2 fold, repeated 5 times) 
## Summary of sample sizes: 19124, 19126, 19127, 19123, 19126, 19124, ... 
## Resampling results across tuning parameters:
## 
##   k   RMSE      Rsquared   MAE      
##    5  1.127596  0.5445332  0.8833578
##    7  1.111266  0.5540285  0.8705737
##    9  1.106173  0.5564194  0.8671249
##   11  1.104861  0.5565396  0.8669588
##   13  1.105787  0.5552225  0.8680676
## 
## RMSE was used to select the optimal model using the smallest value.
## The final value used for the model was k = 11.
```

![](sledzie_files/figure-html/unnamed-chunk-6-1.png)<!-- -->