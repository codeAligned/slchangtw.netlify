---
layout: post
title: "Berkerley CS105x Introduction to Apache Spark 筆記(三)"
author: Shun-Lung Chang
---

本系列紀錄 [Data Science and Engineering with Apache Spark](https://www.edx.org/xseries/data-science-engineering-apache-spark) 的上課與作業筆記。本文為 Introduction to Apache Spark Week 3 的筆記。

<!--more-->

第三週的課程著重在如何分析半結構化 (Semi-structured) 的資料。探討的範例為 Apache Web Server Log。

## Apache Web Server Log Format

Apache Web Server Log Format 會包含如下的內容：
[Client IP address] [User identity from remote machine] [User identity from local logon] [Request time] [Client request] [Status code] [Size of the object returned to client]

例子如下：
127.0.0.1 - - [01/Aug/1995:00:00:01-W0400] "GET /images/launchWlogo.gif HTTP/1.0" 200 1839

此例中 [User identity from remote machine] 與 [User identity from local logon] 皆為 - ，表示 not available。

## Lab Exercises

練習從第四部分開始，主要還是複習 `df.select()` 與 `df.groupBy("column name")` 的語法。另外 `df.distinct()` 可回傳沒有重複列的 dataframe。

