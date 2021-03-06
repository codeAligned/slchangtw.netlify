---
layout: post
title: "你的資料是寬的還是長的？"
author: Shun-Lung Chang
---

這篇文章說明寬格式 (Wide Format) 與長格式 (Long Format) 兩種資料格式。並說明在特定情境下寬格式的缺點，並如何透過 R 套件 tidyr 的語法快速互相轉換寬格式與長格式資料。

<!--more-->

在資料呈現中，可分為寬格式與長格式。這兩種格式差異在於增加新資料時，資料集的欄位(或者變數)是否會增加。細部來說，寬格式的資料可能會隨資料增加而新增欄位，而長格式的欄位個數是固定的。舉例來說，下面的資料呈現的是 A, B, C 三種產品在每天生產量與銷售量。從表可看出每天日期是作為欄位，可以想像的是，當需要紀錄 1/11 以後的資料，需要新增欄位。因此，以下的資料集是以寬格式呈現。

<center><img src="http://i.imgur.com/pHoSlFM.png" width="700" height="200"></center>

另一方面，下面的資料是以日期作為單一欄位。當有新日期的資料，會增加的是列數而非欄位個數，也就是說，欄位個數並不隨資料增加而增加。因此，以下的資料集是以長格式呈現。

<center><img src="http://i.imgur.com/WOirinQ.png" width="280" height="200"></center>

寬與長兩種格式各有其適用的場景。但在匯總統計量，如平均數或筆數時，寬格式會難以計算。舉例來說，對於使用 Excel 的人員來說，匯總計算通常是以樞紐分析表完成。若他們拿到的是寬格式，要計算每天的總生產量跟總銷售量會十分麻煩，必須手動新增每日日期欄位，如下圖。

<center><img src="http://i.imgur.com/ZVcH9W9.png" width="280" height="200"></center>

但以長格式資料做成的樞紐分析表可快速篩選日期。如下圖所述。

<center><img src="/images/20160812_long_pivot.png" width="280" height="200"></center>

由上可看出長格式在資料匯總的優勢。然而，我們無法要求資料一定要是長格式。更常發生的情境會是我們拿到寬格式的資料，再手動轉換成長格式。[此文](http://www.listendata.com/2015/02/excel-formula-convert-data-from-wide-to.html) 有說明如何以 Excel 進行轉換。但是，若這件工作是每日例行作業就會十分擾人。當然，Excel 可以透過 VBA 完成自動化流程，但撰寫程式碼的過程也會十分冗長。但如果透過 R， 僅僅只要一行語法就可以快速轉換兩種格式。

在 R 中達成轉換的任務是使用 tidyr 套件的 `gather()` 與 `spread()`。`gather()` 的關鍵是 key 和 value 的搭配與要轉換的欄位。以前面的寬資料來說，`gather()` 語法如下：

```
gather(data, key = date, value = quantity, 3:12)
```

語法分成四個部份，語法的第一個參數是要轉換的資料，第二個參數是合併成新的這一行的名稱，所以可以自己命名，以此例來說，要匯總各個日期欄位，因此命名為 date。第三個參數是值的名稱，所以也可以自己命名，故命名為 quantity。第三塊是告訴程式我們對哪幾個變數做合併的轉換
。此例為第 3 行到第 12 行。透過上面的語法，可將寬格式轉換成長格式如下圖。

<center><img src="http://i.imgur.com/VyLaNE6.png" width="280" height="200"></center>

反之，若要透過 R 將長格式轉成寬格式，需要透過 `spread()`。以前例來說，`spread() `語法如下。可看出 spread() 語法的參數跟 `gather()` 相近，只是兩者作相反的轉換。更多的例子可參考[說明文件](https://cran.r-project.org/web/packages/tidyr/tidyr.pdf)。

```
spread(long_data, key = date, value = quantity)
```

額外補充，若要透過 Python pandas 做到寬格式轉換成長格式，可透過 `melt()`， 語法如下所示：

<center><img src="http://i.imgur.com/pf4wjvv.png" width="400" height="150"></center>

總言之，此篇文章說明寬與長兩種格式的資料，與寬格式在作資料匯總的劣勢。最後說明如何透過 R 套件語法快速轉換兩種格式。

### 補充文件

- [The Wide and Long Data Format for Repeated Measures Data](http://www.theanalysisfactor.com/wide-and-long-data/)
- [Tidy Data](https://ramnathv.github.io/pycon2014-r/explore/tidy.html)
