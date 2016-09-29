---
layout: post
title: "edX Berkerley CS105x Introduction to Apache Spark 筆記(二)"
author: Shun-Lung Chang
---

---

本系列紀錄我 [Data Science and Engineering with Apache Spark](https://www.edx.org/xseries/data-science-engineering-apache-spark) 的上課與作業筆記。本文為 Introduction to Apache Spark Week 2 的筆記。

<!--more-->

在 Week 1 的課程中有提到，Spark 是作為 **Scalable, efficient analysis of Big Data** 的工具，而在前週已有說明如何透過 Spark DataFrame 進行分析。而 Week 2 首先說明 Spark 在 scalable 與 efficient 兩個面向。

## Map Reduce

在 scalable 面向，因現今硬體設備已經相當便宜，因此在大資料時代下，運算架構多是採叢集式運算。叢集式運算的一個重要問題是如何將運算作業拆分至各台機器計算。以字數計算 (Word Count) 為例，當我們拿到一份文本，而且想計算出現次數最高的詞，可將原文本拆成多份小文本，再分派到各台機器中，以 [Hash Map](https://en.wikipedia.org/wiki/Hash_table) 儲存每個字的出現次數，最後再將各機器的計算結果彙整到一台機器。