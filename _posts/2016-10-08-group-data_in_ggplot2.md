---
layout: post
title: "如何在 ggplot2 裡依組別畫圖"
author: Shun-Lung Chang
---

<p class="message">
進行資料視覺化時，我們時常需要將資料依變數分組繪製圖形，如將男女分組後的薪資趨勢圖，以得知男女的薪資走向。透過 R 的繪圖套件 ggplot2 ，我們可以設定參數很快地完成這個目的。然而，當資料集的分組變數分散在各行時，ggplot2 無法直接繪製分組圖形。因此，在本文中會說明如何以 tidyr 套件的 gather() 函數，將資料整理成 ggplot2 可使用的格式，以進行分組繪圖。
</p>

<!--more-->

## 在 ggplot2 依變數分組繪製圖形

在本節中，會使用 [mtcars 資料集](https://stat.ethz.ch/R-manual/R-devel/library/datasets/html/mtcars.html)(R 預設即有)。假設我們將繪製在 mtcars 中，以 vs 變數 (0, 1) 分組出在不同 cyl (4, 6, 8) 下的資料筆數。首先，第一種方法是我們可以以顏色標明分組，範例如下。由範例可看出，我們可透過設定 fill 參數來決定填滿長條圖內的顏色。另外 factor() 函數是使變數變成因子型態，而非原本預設的整數型態。另外 geom_bar() 內的 `position = "dodge"` 使長條圖非預設疊加呈現。

```
library(ggplot2)

mtcars %>% 
    ggplot(aes(x = factor(cyl), fill = factor(vs))) +
        geom_bar(position = "dodge")
```

<center><img src="/images/20161008_mtcars_1.png" width="520" height="280"></center>

而第二種方法，是可以透過 facet 進行分組，範例如下。由範例可看出，我們可透過加 ```facet_grid()```，並搭配 `~ factor(vs)` 完成繪圖。另 `~` 在 R 中表示的意思是函數關係，`~` 左邊的變數會依賴 (depend on) 右邊的變數。如 `y ~ x1 + x2` 表示變數 y 依賴於變數 x1 與 x2。以下方的範例來說，可解釋為進行 facet 分組時，是依賴於 factor(vs) 進行分組。`~` 會在建立迴歸模型時更加常用。

```
mtcars %>% 
    ggplot(aes(x = factor(cyl))) +
        geom_bar() +
        facet_grid(~ factor(vs))

```

<center><img src="/images/20161008_mtcars_2.png" width="520" height="280"></center>

除以上兩種方法外，若在繪製散佈圖時，可用點圖形與大小以分組，範例可見[文件](http://docs.ggplot2.org/current/geom_point.html)。

## 欄位轉換成資料，再進行分組繪圖

雖然已講述了兩種方法進行分組繪圖，但若以[中國信託歷史匯率](http:/goo.gl/2XzN9o)資料為例，由下表可看出雖資料乍看下是屬於長格式(每日資料會向下成長)，但若想繪製如其網頁的兩種類型匯率走勢圖，其提供的資料無法讓我們以 ggplot2 分組繪製，因為我們一次僅能畫一個欄位的資料(y 只能一次放 Buy 或 Sell)，範例如下。

<center><img src="/images/20161008_rate_table.png" width="520" height="200"></center>

<center><img src="/images/20161008_rate_plot.png" width="520" height="200"></center>

```
library(ggplot2)

d <- read.csv("path/to/your/data/rate.csv", stringsAsFactors = FALSE)

# y 只能放一個變數
ggplot(d, aes(x = as.Date(Date), y = Buy)) +
    geom_line() +
    labs(x = "Date")
```

<center><img src="/images/20161008_rate_ggplot2_1.png" width="520" height="280"></center>


為解決這樣的問題，我們需要將資料集的 Buy 與 Sell 欄位彙整一個新的欄位，才可以將此新欄位如第一節所說，以 ggplot2 設定分組欄位。其轉換模式如同轉換資料寬格式到長格式。透過 tidyr 的 gather() 可快速幫我們達成，範例如下。透過 gather()，我們將 Buy 跟 Sell 兩個變數彙總成 Type 變數，並把數值命名為 Rate 變數。接著再透過 ggplot2 進行繪圖。注意線圖(趨勢圖)顏色分組的參數是 `colour`，而非 `fill`，因為線圖並非如長條圖是以顏色填滿。

```
library(ggplot2)
library(tidyr)

# convert d from wide format to long format
d_long <- d %>% 
    gather(Type, Rate, -Date) 
 
# draw
ggplot(d_long, aes(x = as.Date(Date), y = Rate, colour = Type)) +
    geom_line() +
    labs(x = "Date", y = "Rate", title = "Trend of Exchange Rate")
```

<center><img src="/images/20161008_rate_ggplot2_2.png" width="520" height="280"></center>

## 總結

本文首先說明 ggplot2 如何繪製分組資料，接著說明當我們遇到資料，其分組變數是分散在各行時，如何透過 gather() 函數將其轉換成列資料，以繪製分組資料。另本文範例程式碼可見[此處](https://rawgit.com/ConnerChang/blog_examples/master/group_data_in_ggplot2.html)。