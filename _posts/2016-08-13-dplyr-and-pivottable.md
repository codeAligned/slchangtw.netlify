---
layout: post
title: "從 Excel 樞紐分析表學習 R 的 dplyr"
author: Shun-Lung Chang
---

<p class="message">
對於經常使用 Excel 用戶而言，樞紐分析表是做資料初步整理分析的常用工具，例如計算資料筆數、變數平均等。這篇文章會說明如何透過樞紐分析表來學習 R language 的 dplyr 套件，以幫助 Excel 用戶學習以 R 整理資料。
</p>

<!--more-->

(本篇文章使用的範例資料為 [Titanic: Machine Learning from Disaster](https://www.kaggle.com/c/titanic/data), 細部資料描述可見連結。)

一個樞紐分析表建立時，需要設定四個區塊，如下圖所示。分別是報表篩選、欄標籤、列標籤、個值。

<center><img src="/images/20160813_four_blocks.png" width="280" height="250"></center>

舉例來說，如果我們想要用範例資料建立了解依 Sex 跟 Pclass 分類的的存活人數。其樞紐分析表會如下圖所示。設定方式是將 Sex、Pclass 放到列標籤，將 Suvival 放到個值，並設定加總。

<center><img src="/images/20160813_example_1.png" width="300" height="250"></center>

在 R 的 [dplyr](https://cran.rstudio.com/web/packages/dplyr/vignettes/introduction.html) 中，樞紐分析表的列標籤對應到 **group_by()**, 個值對應到 **summarise()**。因此，要產生跟上面樞紐分析表的 R 語法如下。其中`%>%`是 pipe 運算子，其運作方式可見[此處](https://www.r-bloggers.com/magrittr-simplifying-r-code-with-pipes/)。因此，從下面的語法可看出只要將 Sex, Pclass 放到 group_by() 中，另再將 Survived 放到 summarise() 的並作 sum() 加總。以上的語法對應到樞紐分析表將 Sex、Pclass 放到列標籤，將 Suvival 放到個值，並設定加總。

```
library(dplyr)

titanic %>% 
	group_by(Sex, Pclass) %>%
	summarise(sum(Survived))
```

<center><img src="/images/20160813_example_1_R.png" width="300" height="250"></center>

另外，在樞紐分析表個值主要的摘要方式與 summarize 的變數匯總方式對應可整理如下表：

|  個值摘要方式  |   summarise   |
|--------------|:-------------:|
|      加總     |     sum()     |
|      計數     |      n()      |
|     平均值    |     mean()    |
|     最大值    |      max()    |
|     最小值    |      min()    |
|     標準差    |      sd()     |


雖然樞紐分析表的欄標籤並沒有對應的 dplyr 語法，然而可以透過[你的資料是寬的還是長的](/2016/08/12/long-wide-format/)介紹的 tidyr::spread() 建立欄標籤。舉例來說，我們在前面的樞紐分析表加入 Embarked 變數到欄標籤，結果如下圖。

<center><img src="/images/20160813_example_2.png" width="300" height="280"></center>

在 R 中要產生上面的樞紐分析表，其語法如下。可看到我們先在 group_by() 加入 Embarked 變數，接著做完 summarise() 後，設定 spread() 的 key 為 Embarked 即可完成。但此方法只能產生一層的欄標籤，無法做到兩層以上的欄標籤。

```
library(dplyr)
library(tidyr)

titanic %>% 
	group_by(Sex, Pclass, Embarked) %>% 
	summarise(s = sum(Survived)) %>% 
	spread(key = Embarked, value = s)
```

<center><img src="/images/20160813_example_2_R.png" width="400" height="280"></center>

樞紐分析表的報表篩選對應的 dplyr 的語法是 **filter()**。舉例來說，我們想要產生依 Sex 與 Pclass 分類，且限定 Embarked 在 C 與 Q 兩個地點的平均 Fare， 其樞紐分析表的設定如下圖。

<center><img src="/images/20160813_example_3.png" width="400" height="280"></center>

在 R 中要產生上面的樞紐分析表，其語法如下。可看到我們加入`dplyr::filter(Embarked %in% c("C", "Q"))`這段語法，`%in%` 運算子計算資料有無相符(Match), 更多的解釋可見[文件](https://stat.ethz.ch/R-manual/R-devel/library/base/html/match.html)。因 filter() 運行方式為回傳有符合條件的資料，故回傳 Embarked 是 C 和 Q 的資料。此外，`dplyr::filter()` 是呼叫在 dplyr 套件內的 filter()。因為在 R 中的原生套件 stat 中也有 filter()，而兩者使用場景且結果不同。因此若有時候發現 filter() 的結果與預期不合，可以使用 `dplyr::filter()` 指定。

```
titanic %>% 
    dplyr::filter(Embarked %in% c("C", "Q")) %>%
    group_by(Sex, Pclass) %>%
    summarize(mean(Fare))
```
<center><img src="/images/20160813_example_3_R.png" width="400" height="280"></center>

此外，filter() 其實也能作為列標籤或欄標籤的篩選。例如下面的樞紐分析表，我們僅篩選 Pclass 是 2 跟 3。在 R 中可以透過下面的語法完成。可看到我們 filter() 當中設定 `Pclass != 1`。

<center><img src="/images/20160813_example_4.png" width="400" height="280"></center>

```{r}
titanic %>% 
    group_by(Sex, Pclass) %>%
    summarize(mean(Fare)) %>%
    dplyr::filter(Pclass != 1) // also can be done by setting Pclass %in% c("2", "3")
```

<center><img src="/images/20160813_example_4_R.png" width="350" height="250"></center>

然而必須注意一點，R 進行 summarize() 運算後，原本資料集的變數就會只剩有在 group_by() 內的變數。因此，若你想要做的是樞紐分析表的報表篩選，務必將 filter() 放在 summarize() 之前。舉例來說，如下面的 R 語法就會回報錯誤，因為 Embarked 變數已不在 summarize() 後的資料集中。

```
titanic %>% 
    group_by(Sex, Pclass) %>%
    summarize(mean(Fare)) %>%
    dplyr::filter(Embarked %in% c("C", "Q"))
# Error: object 'Embarked' not found
```

總言之，樞紐分析表的四個設定區塊對應到 dplyr 的語法整理如下：

- 報表篩選：filter()
- 欄標籤：group_by() + tidyr::spread() (僅能產生一層欄標籤)
- 列標籤：group_by()
- 個值：summarise()

最後，在 R 中可以透過 rpivotTable 建立互動式樞紐分析表，可再嵌入 R markdown 中做成報告，可參考[此處](https://rawgit.com/ConnerChang/blog_examples/master/dplyr_pivottable.html)。

## 補充文件

- [dplyr cheatsheet](https://www.rstudio.com/wp-content/uploads/2015/02/data-wrangling-cheatsheet.pdf)
