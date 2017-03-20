---
layout: post
title: "Berkerley CS105x Introduction to Apache Spark 筆記(二)"
author: Shun-Lung Chang
---

本系列紀錄 [Data Science and Engineering with Apache Spark](https://www.edx.org/xseries/data-science-engineering-apache-spark) 的上課與作業筆記。本文為 Introduction to Apache Spark Week 2 的筆記。

<!--more-->

(本筆記圖片若無特別標注來源，皆來自上課講義。)

在 Week 1 的課程中有提到，Spark 是作為 **Scalable, efficient analysis of Big Data** 的工具，而在前週已有說明如何透過 Spark DataFrame 進行分析。而 Week 2 首先說明 Spark 在 scalable 與 efficient 兩個面向。

## Map Reduce

在 scalable 面向，因現今硬體設備已經相當便宜，因此在大資料時代下，運算架構多是採叢集式運算。叢集式運算的一個重要問題是如何將運算作業拆分至各台機器平行計算。以字數計算 (Word Count) 為例，當我們拿到一份文本，而且想計算出現次數最高的詞，可將原文本拆成多份小文本，再分派到各台機器中，以 [hash table](https://en.wikipedia.org/wiki/Hash_table) 儲存每個字的出現次數，最後再將各機器的計算結果彙整到一台機器。

然而，若要能一次彙整最後計算結果，其機器需要有相當的運算能力。因此，可以在彙整階段分成多台機器平行彙整一部分計數，最後才彙整到一台機器回傳結果(借用 Divide and Conquer 的手法)。其概念圖如下。由圖可知，平行拆分計算階段為 Map，平行彙整計算階段為 Reduce。

<center><img src="http://imgur.com/at8jVoE.png" width="350" height="200" /></center>

## In-memory Computing

在 efficient 面向，傳統 Hadoop 平台的計算架構也是採用 Map Reduce。然而，其資料在各機器迭代計算是透過硬碟交換，因此需要許多 Disk I/O 作業，導致計算效率低落。而 Spark 的設計理念是希望盡量做到在記憶體運算 (In-memory Computing)，因此資料不需再寫回硬碟，計算速度通常可比 Hadoop 快上 10 倍至 100 倍。

<center><img src="http://i.imgur.com/aqARVBs.png" width="360" height="250"></center>

Hadoop 與 Spark 比較可見下圖。

<center><img src="http://i.imgur.com/lsSo29u.png" width="360" height="250"></center>

## Lab Exercises

Lab 1A 主要介紹 [PySpark SQL API](http://spark.apache.org/docs/latest/api/python/pyspark.sql.html#pyspark-sql-module)與練習常見的 **Transformation** 與 **Action**。

Lab 1B 是字詞次數計算的練習，兩個做題要點如下。首先，若要針對 Spark DateFrame 進行欄位的計算，是使用 df.select(func(df.column).alias("new column name")) 的形式。例如，(1b) 要將某欄位(值皆為字串)都加上 "s"，其語法如下。其中 `lit()` 是產生一整行的 s。`concat()` 是合併字串。

```
from pyspark.sql.functions import lit, concat

pluralDF = wordsDF.select(concat(wordsDF.word, lit("s")).alias("word"))
```

第二， Spark DateFrame 依某欄位分組彙整計算統計量是使用 df.groupBy("column name").func()，其中 func() 是如 `mean()`, `count()`, `sum()` 等函數。
