---
layout: post
title: "edX Berkerley CS105x Introduction to Apache Spark 筆記(一)"
author: Shun-Lung Chang
---

本系列紀錄我 [Data Science and Engineering with Apache Spark](https://www.edx.org/xseries/data-science-engineering-apache-spark) 的上課與作業筆記。本文為 Introduction to Apache Spark Week 1 的筆記。

<!--more-->

(本筆記圖片若無特別標注來源，皆來自上課講義。)

## What is Apache Spark?

Week 1 的課程先闡述為何需要使用 Spark: 若資料量成長到無法使用在單機環境下的傳統資料分析工具，如 Unix shell commands (grep, awk, sed), pandas, R, 就會需要分散式的架構進行資料儲存與分析。而在 Spark 主要就是提供 **Scalable, efficient analysis of Big Data** 的工具，而在未來的課程中，會說明 Spark 是如何擁有這些特性。

## The Structure Spectrum

在進入 Spark 的分析架構前，課程先說明在資料管理上的概念：schema, 課程定義如下:

```A schema is a description of a particular collection of data, using a given data model.``` 

白話來說，schema 可以看作是對於資料集的描述，例如定義資料表名稱、每筆資料的欄位名稱與屬性等。而透過 schema, 我們可將資料分成 Structured, Semi-structured 與 Unstructured 三種型態，其分類表如下圖。

![alt text](/images/20160810_spectrum.png)

簡言之，Structured data 在資料產生前就會定義出 schema, 規定資料集中的每筆資料該有哪些欄位。而 Semi-structured 則是在資料產生過程中沒有固定的 schema，也就是每筆資料可能有不同的欄位。而 Unstructured data 並不會特別定義任何出 schema. 這三種資料形態的差異，更多解釋與例子可見[此處](https://kevinwang.gitbooks.io/bigdata/content/general/structured-data.html)。

## Analysis and Apache Spark

在本次課程中，是透過 [DataFrame](http://spark.apache.org/docs/latest/api/python/pyspark.sql.html#pyspark.sql.DataFrame) 進行資料分析，也就是 R 與 Python pandas 的資料分析格式，而不是透過 Resilient Distributed Dataset (RDD)。Spark DataFrame 的基本特性與 RDD 大致相同，都包含:

1. 建立後就不可改變(Immuntable)
2. Track lineage information 的機制以計算遺失的資料
3. 可作分散式運算

此外，Spark DataFrame 的每一列都是 [Row](http://spark.apache.org/docs/latest/api/python/pyspark.sql.html#pyspark.sql.Row) 物件。而要取得欄位的值可透過以下的方式：

```
>>>row = Row(name="Alice",+age=11)
>>>row
Row(age=11,+name='Alice')

>>>row['name'], row['age']
('Alice',+11)

>>>row.name, row.age
('Alice',+11)
```

### Working with Spark DataFrames

Spark DataFrame 可以透過 python pandas, R DataFrame 或者從其他儲存系統 (如HDFS) 內的文件產生，產生方式可參考[文件](http://spark.apache.org/docs/latest/sql-programming-guide.html)。

在分析過程中會需要對 DataFrame 做各種操作，如篩選變數、產生新變數、計算變數平均等。Spark 的操作可分為 **Transformation** 與 **Action** 兩種。在這裡有一個非常重要的一個觀念，就是 Transformation 的操作是惰性求值 (lazy), 也就是進行 Transformation 操作並不會實際計算值，而是要到進行 Action 操作時，才會實際計算。這邊我可以舉我自己的例子，我曾經將一個約 7,000 萬筆的資料以 pyspark 的 api 匯入 Spark, 之後進行 select() 與 filter() 的操作 (皆為 Transformation 操作), 這邊的執行時間都是約 1 秒就執行完畢，但當我執行 describe() 操作時 (Action 操作)，就執行約 20 分鐘左右。因此，所有的 Transformation 的執行時間都是: 假的！只有進行 Action 操作時， Spark 才會開始進行運算。

常見的 Spark DataFrame Transformation 操作包含欄位選取，篩選值與 GroudBy 等操作。如 select(), 主要是選取 DataFrame 的某些欄位，使用方法如下：

```
>>>df.select('*') // 選取 df 所有欄位

>>>df.select('name','age') // 選取 df 中的 name 跟 age 欄位

>>>df.select(df.name, (df.age+10).alias('age')) // 選取 df 中的 name 並將 age 欄位的值加 10, 並將新值的欄位名稱命名為 age 
```

其他常見的 Transformation 操作可見下表：

![alt text](/images/20160810_transformation_1.png)

![alt text](/images/20160810_transformation_2.png)

範例操作可見[文件](http://spark.apache.org/docs/latest/api/python/pyspark.sql.html)。

Spark DataFrame 的 Action 操作包含印出資料、計算資料筆數與計算基本統計特徵量等。常見操作可見下表。

![alt text](/images/20160810_action_1.png)

在執行各種操作中，會有一些反覆使用到的 DataFrames, 此時可使用將這些 DataFrames 快取到記憶體中，減少重複運算。語法如下：

```
df.cache() //將 df 這個 DataFrame 快取到記憶體中
```
因此，透過上述的過程，匯入資料、進行 Transformation 與 Action 操作，並快取某些 DataFrames 到記憶體中，課程中整理出以 Spark DataFrame 進行分析的週期如下圖。

![alt text](/images/20160810_lifecycle.png)

最後，Spark 執行運算時是採 Driver 和多個 Executors 的架構. Spark 運行程式是在 Driver 中，而 Transfomation 的操作是在 Executors 中執行，而 Action 是在 Driver 和 Executors 完成。而必須注意的是，通常在觀察資料時會使用 collect() 這個 Action 操作，但一旦執行後，會將所有分散在 Executors 的資料拉回到 Driver 中而導致 Out of memory. 因此，若要查看資料，建議使用 take(n) 這個 Action 操作。此外，撰寫 Spark 程式時，盡量多翻閱[文件](http://spark.apache.org/docs/latest/api/python/pyspark.sql.html)查看是否有 api 可以達到所想要的目的，並充分使用到 Spark Driver-Executors 架構下的效能。

## Spark RDD 補充文件

本次課程中是以 Spark DataFrame 作為分析工具，並沒有提到 Spark RDD, 若對 Spark RDD 有興趣者，可參閱此[文件](http://eighty20.cc/apps/e2-spk-v01/present/e2-spk-s01/index.html#/13).
