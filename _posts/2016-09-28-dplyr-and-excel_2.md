---
layout: post
title: "從 Excel 學習 R 的 dplyr (二)"
author: Shun-Lung Chang
---

延續前篇如何從 Excel 學習 R 的 dplyr 以整理資料，本篇說明其他 dplyr 的資料整理函數。相較於前篇說明如何進行資料彙整分析，本篇著重在如何整理原始資料，如何產生新欄位、篩選原始資料、選取欄位、合併資料。

<!--more-->

(本篇文章使用的範例資料在 R 的 ggplot2 套件下的 mpg 資料集。細部資料描述可見[連結](http://docs.ggplot2.org/current/mpg.html))。

## 產生新欄位

在範例資料中，假設我們想計算 hwy 欄位減 cty 欄位的值，並取絕對值，在 Excel 中，我們可以先命名欄位名稱，再以 I 行的值減 H 行的值，再搭配 `abs()`。而在 R 中，可以新增欄位的函數為 `mutate()`。範例如下。由範例可看出，產生新欄位的過程類似 Excel，我們先命名新欄位名稱，接著，再相減我們兩個原本舊有的欄位並取絕對值。

```
library(dplyr)

mpg %>% 
	mutate(h_minus_c = abs(hwy - cty))
``` 

此外，mutate() 產生的新欄位可以搭配 R 其他的函數。例如我們想取出 trans 欄位的後三碼，範例如下。由範例可看出，我們可以命名新欄位 tran_last，接著透過 `substr()`，其使用方法同 Excel 的 MID()，取出 trans 欄位的後三碼。

```
mpg %>% 
    mutate(tran_last = substr(trans, nchar(trans) - 3, nchar(trans)))
```

最後，`mutate()`其實不只可以產生新欄位，此函數亦可以更換取代原始欄位內的值。例如，我們想將 fl 欄位內值的 p 變更為大寫 P。範例如下。透過 dplyr 內的 `if_else()` 函數，若是 fl 內的值為 p，則取代為 P，否則維持原始值。

```
mpg %>% 
    mutate(fl = if_else(fl == "p", "P", fl))
```

`if_else` 以取代值的方式亦可改由 dplyr 的 `recode()` 達成。範例如下。

```
mpg %>% 
    mutate(fl = recode(fl, "p" = "P"))
``` 

## 篩選原始資料

在 Excel 要篩選原始資料，可以使用「篩選」功能。在 R 的 dplyr 中，篩選資料可用已前篇介紹的 `filter()`。例如，我們要篩選 displ 欄位大於 3 的資料，範例如下。

```
mpg %>% 
    filter(displ > 3)
```

此外，`filter()` 內的條件可用 `&` 達成「且」，用 `|` (位於 Enter 鍵上方)達成「或」的條件式。例如，我們要篩選 displ 欄位大於 3 的資料「而且」manufacturer 為 audi，範例如下。另外，若省去 `&`，而以 `,` 代替，亦表示 「且」

```
mpg %>% 
    filter(displ > 3 & manufacturer == "audi")

mpg %>% 
    filter(displ > 3, manufacturer == "audi")

```

又或者例如，我們要篩選 manufacturer 是 toyota 下，「且」displ 大於 3 「或」小於 2 的資料，範例如下。

```
mpg %>% 
    dplyr::filter(manufacturer == "toyota" & (displ > 3 | displ < 2))
```

## 選取欄位

相較於前段所說的篩選資料，是針對每列進行篩選，此段會說明如何針對每行進行篩選。例如，在 Excel 中，我們可能會拿到有 100 個欄位，但其實只有 10 個欄位有用，我們會手動刪去不需要的欄位。而在 R 的 dplyr 套件裡，選取欄位是使用 `select()`。例如，我們要選出範例資料集的 model 跟 year，範例如下。

```
mpg %>% 
    select(model, year)
```

有時候，我們不需要的欄位只佔少數，例如我們只不要 model 欄位，我們可以在變數名稱加 `-`以捨去欄位，範例如下。

```
mpg %>% 
    select(-model)
```

此外，有時候資料集的欄位會有相似性，例如皆是以 year 開頭，後面接年份，而要一次選出這些欄位，可以用 `select(start_with("year"))` 選出。而以下的範例展示如何選出 mpg 資料集中以 m 開頭的欄位。

```
mpg %>% 
    select(starts_with("m"))
```

最後，`select()` 也可以用欄位序列值進行選取。例如我們要選取 mpg 第一到第三與第六行，可以用下面的範例做到。

```
mpg %>% 
    select(1:3, 6)
```

## 合併資料

合併資料的意思指的是合併多個資料集，而合併資料可以分為列資料 (row) 與行欄位 (column) 兩個方向。我們首先講列資料的合併。例如，我們有兩個資料集如下。

```
d1 <- data.frame(a = 1:3, b = 3:5, c = 5:7)
d2 <- data.frame(b = 1:2, c = 2:3, a = 5:6)
```

如果我們要將 d2 合併在 d1 資料集下方，可以使用 `rbind()` 函數，r 表示 row。此外，此函數可以確保兩資料集要有相同欄位名稱才會合併。例如以 d1 與 d2 合併，a 欄位的資料會是 1, 2, 3, 5, 6，而不會是合併到 d2 的第一個欄位 1 與 2。範例如下。

```
rbind(d1, d2)
```

行資料的合併部分，是使用 `cbind()` 函數，c 表示 column。但 cbind() 並不會確保合併的資料是相同一筆，例如以下的資料集，可看出 cbind() 並不會確保 id 相同的合併後會是同一列。

```
d1 <- data.frame(id = paste0("id", 1:3), a = 3:5, b = 5:7)
d2 <- data.frame(id = paste0("id", 3:1), c = 2:4, d = 5:7)
cbind(d1, d2)


   id a b  id c d
1 id1 3 5 id3 2 5
2 id2 4 6 id2 3 6
3 id3 5 7 id1 4 7
```

要解決這個問題，需透過 `join()` 系列函數達成。首先介紹 `inner_join()`，範例如下。透過 by = "id"，就可以確保相同 id 的資料才進行行欄位合併。

```
inner_join(d1, d2, by = "id")

   id a b c d
1 id1 3 5 4 7
2 id2 4 6 3 6
3 id3 5 7 2 5
```

此外，`inner_join()` 會確保兩個合併的資料集合併的參考行 (如上述的 id) 都有的值才會回傳。例如以下面的資料集，d2 的 id 只有 id1 與 id2，只會有兩個資料集都有的 id1，id2 合併的資料才會回傳。

```
d1 <- data.frame(id = paste0("id", 1:3), a = 3:5, b = 5:7)
d2 <- data.frame(id = paste0("id", 2:1), c = 2:3, d = 5:6)
inner_join(d1, d2, by = "id")

   id a b c d
1 id1 3 5 3 6
2 id2 4 6 2 5
```

另外若要回傳資料要保留 d1 所有的 id (如同 Excel 內的 vlookup())， d2 沒有的 id 就以 NA 表示，則可以用 `left_join()`。範例如下。

```
left_join(d1, d2, by = "id")

   id a b  c  d
1 id1 3 5  3  6
2 id2 4 6  2  5
3 id3 5 7 NA NA
```

## 總結

本篇文章介紹函數可整理如下。

- 產生新欄位: `mutate()`
- 篩選列資料: `filter()`
- 篩選行欄位: `select()`
- 以列合併資料: `rbind()`
- 以行合併資料: `inner_join()`, `left_join()`