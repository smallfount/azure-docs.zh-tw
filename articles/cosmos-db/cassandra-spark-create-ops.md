---
title: 從 Spark 創建資料或將資料插入到 Azure 宇宙 DB Cassandra API 中
description: 本文將詳細說明如何將範例資料插入 Azure Cosmos DB Cassandra API 資料表
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 3eb23a3d8b1098110bd8b75faa22cc483637d183
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75442114"
---
# <a name="createinsert-data-into-azure-cosmos-db-cassandra-api-from-spark"></a>從 Spark 建立/插入資料至 Azure Cosmos DB Cassandra API
 
本文將說明如何從 Spark 將範例資料插入 Azure Cosmos DB Cassandra API 中的資料表。

## <a name="cassandra-api-configuration"></a>Cassandra API 設定

```scala
import org.apache.spark.sql.cassandra._
//Spark connector
import com.datastax.spark.connector._
import com.datastax.spark.connector.cql.CassandraConnector

//CosmosDB library for multiple retry
import com.microsoft.azure.cosmosdb.cassandra

//Connection-related
spark.conf.set("spark.cassandra.connection.host","YOUR_ACCOUNT_NAME.cassandra.cosmosdb.azure.com")
spark.conf.set("spark.cassandra.connection.port","10350")
spark.conf.set("spark.cassandra.connection.ssl.enabled","true")
spark.conf.set("spark.cassandra.auth.username","YOUR_ACCOUNT_NAME")
spark.conf.set("spark.cassandra.auth.password","YOUR_ACCOUNT_KEY")
spark.conf.set("spark.cassandra.connection.factory", "com.microsoft.azure.cosmosdb.cassandra.CosmosDbConnectionFactory")
//Throughput-related...adjust as needed
spark.conf.set("spark.cassandra.output.batch.size.rows", "1")
spark.conf.set("spark.cassandra.connection.connections_per_executor_max", "10")
spark.conf.set("spark.cassandra.output.concurrent.writes", "1000")
spark.conf.set("spark.cassandra.concurrent.reads", "512")
spark.conf.set("spark.cassandra.output.batch.grouping.buffer.size", "1000")
spark.conf.set("spark.cassandra.connection.keep_alive_ms", "600000000")
```
## <a name="dataframe-api"></a>Dataframe API

### <a name="create-a-dataframe-with-sample-data"></a>使用範例資料建立 Dataframe

```scala
// Generate a dataframe containing five records
val booksDF = Seq(
   ("b00001", "Arthur Conan Doyle", "A study in scarlet", 1887),
   ("b00023", "Arthur Conan Doyle", "A sign of four", 1890),
   ("b01001", "Arthur Conan Doyle", "The adventures of Sherlock Holmes", 1892),
   ("b00501", "Arthur Conan Doyle", "The memoirs of Sherlock Holmes", 1893),
   ("b00300", "Arthur Conan Doyle", "The hounds of Baskerville", 1901)
).toDF("book_id", "book_author", "book_name", "book_pub_year")

//Review schema
booksDF.printSchema

//Print
booksDF.show
```

> [!NOTE]
> 目前不支援資料列層級上的「若不存在即建立」功能。

### <a name="persist-to-azure-cosmos-db-cassandra-api"></a>保存到 Azure Cosmos DB Cassandra API

儲存資料時，您也可以設定存留時間和一致性原則設定，如下列範例所示：

```scala
//Persist
booksDF.write
  .mode("append")
  .format("org.apache.spark.sql.cassandra")
  .options(Map( "table" -> "books", "keyspace" -> "books_ks", "output.consistency.level" -> "ALL", "ttl" -> "10000000"))
  .save()
```

> [!NOTE]
> 尚不支援資料行層級的 TTL。

#### <a name="validate-in-cqlsh"></a>在 cqlsh 中驗證

```sql
use books_ks;
select * from books;
```

## <a name="resilient-distributed-database-rdd-api"></a>可復原分散式資料庫 (RDD) API

### <a name="create-a-rdd-with-sample-data"></a>使用範例資料建立 RDD
```scala
//Delete records created in the previous section 
val cdbConnector = CassandraConnector(sc)
cdbConnector.withSessionDo(session => session.execute("delete from books_ks.books where book_id in ('b00300','b00001','b00023','b00501','b09999','b01001','b00999','b03999','b02999');"))

//Create RDD
val booksRDD = sc.parallelize(Seq(
   ("b00001", "Arthur Conan Doyle", "A study in scarlet", 1887),
   ("b00023", "Arthur Conan Doyle", "A sign of four", 1890),
   ("b01001", "Arthur Conan Doyle", "The adventures of Sherlock Holmes", 1892),
   ("b00501", "Arthur Conan Doyle", "The memoirs of Sherlock Holmes", 1893),
   ("b00300", "Arthur Conan Doyle", "The hounds of Baskerville", 1901)
))

//Review
booksRDD.take(2).foreach(println)
```

> [!NOTE]
> 目前不支援「若不存在即建立」功能。

### <a name="persist-to-azure-cosmos-db-cassandra-api"></a>保存到 Azure Cosmos DB Cassandra API

將資料儲存到 Cassandra API 時，您也可以設定存留時間和一致性原則設定，如下列範例所示：

```scala
import com.datastax.spark.connector.writer._

//Persist
booksRDD.saveToCassandra("books_ks", "books", SomeColumns("book_id", "book_author", "book_name", "book_pub_year"),writeConf = WriteConf(ttl = TTLOption.constant(900000),consistencyLevel = ConsistencyLevel.ALL))
```

#### <a name="validate-in-cqlsh"></a>在 cqlsh 中驗證

```sql
use books_ks;
select * from books;
```

## <a name="next-steps"></a>後續步驟

將資料插入 Azure Cosmos DB Cassandra AP 資料表後，請繼續閱讀下列文章，以對 Cosmos DB Cassandra API 中儲存的資料執行其他作業：
 
* [讀取作業](cassandra-spark-read-ops.md)
* [作業](cassandra-spark-upsert-ops.md)
* [刪除作業](cassandra-spark-delete-ops.md)
* [聚合操作](cassandra-spark-aggregation-ops.md)
* [資料表複製作業](cassandra-spark-table-copy-ops.md)

