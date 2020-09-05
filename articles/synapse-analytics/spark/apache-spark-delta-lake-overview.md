---
title: Azure Synapse Analytics용 Apache Spark에서 Linux Foundation Delta Lake를 사용하는 방법에 대한 개요
description: Azure Synapse Analytics용 Apache Spark에서 Delta Lake를 사용하여 ACID 속성이 있는 테이블을 만들고 사용하는 방법을 알아봅니다.
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.reviewer: euang
ms.topic: overview
ms.subservice: spark
ms.date: 07/28/2020
ms.author: euang
ms.custom: devx-track-csharp
zone_pivot_groups: programming-languages-spark-all-minus-sql
ms.openlocfilehash: 655daeb0149228d78d5288b0e5d0d705a5743d28
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89008644"
---
# <a name="linux-foundation-delta-lake-overview"></a>Linux Foundation Delta Lake 개요

이 문서는 [여기](https://docs.delta.io/latest/quick-start.html)에 있는 원래의 관련 문서에서 더 명확하게 수정되었습니다. 이 문서를 통해 [Delta Lake](https://delta.io)의 주요 기능을 빠르게 살펴볼 수 있습니다. 이 문서에서는 대화형, 일괄 처리 및 스트리밍 쿼리에서 Delta Lake 테이블을 읽고 쓰는 방법을 보여 주는 코드 조각을 제공합니다. 이 코드 조각은 [여기의 PySpark](https://github.com/Azure-Samples/Synapse/blob/master/Notebooks/PySpark/Hitchikers%20Guide%20to%20Delta%20Lake%20-%20Python.ipynb), [여기의 Scala](https://github.com/Azure-Samples/Synapse/blob/master/Notebooks/Scala/Hitchikers%20Guide%20to%20Delta%20Lake%20-%20Scala.ipynb), [여기의 C#](https://github.com/Azure-Samples/Synapse/blob/master/Notebooks/Spark.NET%20C%23/Hitchikers%20Guide%20to%20Delta%20Lake%20-%20CSharp.ipynb) Notebook 세트에서도 사용할 수 있습니다.

여기서 설명하는 내용은 다음과 같습니다.

* 테이블 만들기
* 데이터 읽기
* 테이블 데이터 업데이트
* 테이블 데이터 덮어쓰기
* 덮어쓰지 않고 조건부 업데이트
* 시간 이동을 사용하여 이전 버전의 데이터 읽기
* 테이블에 데이터 스트림 쓰기
* 테이블에서 변경 내용 스트림 읽기
* SQL 지원

## <a name="configuration"></a>구성

사용자 환경에 맞게 아래 항목을 수정해야 합니다.

:::zone pivot = "programming-language-python"

```python
import random

session_id = random.randint(0,1000000)
delta_table_path = "/delta/delta-table-{0}".format(session_id)

delta_table_path
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
var sessionId = (new Random()).Next(10000000);
var deltaTablePath = $"/delta/delta-table-{sessionId}";

deltaTablePath
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
val sessionId = scala.util.Random.nextInt(1000000)
val deltaTablePath = s"/delta/delta-table-$sessionId";
```

::: zone-end

결과:

'/delta/delta-table-335323'

## <a name="create-a-table"></a>테이블 만들기

Delta Lake 테이블을 만들려면 DataFrame을 델타 형식으로 작성합니다. 형식을 Parquet, CSV, JSON 등에서 델타로 변경할 수 있습니다.

다음 코드에서는 DataFrame에서 유추된 스키마를 사용하여 새 Delta Lake 테이블을 만드는 방법을 보여 줍니다.

:::zone pivot = "programming-language-python"

```python
data = spark.range(0,5)
data.show()
data.write.format("delta").save(delta_table_path)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
var data = spark.Range(0,5);
data.Show();
data.Write().Format("delta").Save(deltaTablePath);
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
val data = spark.range(0, 5)
data.show
data.write.format("delta").save(deltaTablePath)
```

::: zone-end

결과:

| ID|
|---|
|  0|
|  1|
|  2|
|  3|
|  4|

## <a name="read-data"></a>데이터 읽기

파일의 경로와 델타 형식을 지정하여 Delta Lake 테이블의 데이터를 읽습니다.

:::zone pivot = "programming-language-python"

```python
df = spark.read.format("delta").load(delta_table_path)
df.show()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
var df = spark.Read().Format("delta").Load(deltaTablePath);
df.Show()
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
val df = spark.read.format("delta").load(deltaTablePath)
df.show()
```

::: zone-end

결과:

| ID|
|---|
|  1|
|  3|
|  4|
|  0|
|  2|

결과를 출력하기 전에 명시적으로 지정된 순서가 없었으므로 결과의 순서는 위와 다릅니다.

## <a name="update-table-data"></a>테이블 데이터 업데이트

Delta Lake는 표준 DataFrame API를 사용하여 테이블을 수정하는 여러 작업을 지원합니다. 이는 델타 형식에서 추가하여 크게 향상된 기능 중 하나입니다. 다음 예제에서는 테이블의 데이터를 덮어쓰는 일괄 작업을 실행합니다.

:::zone pivot = "programming-language-python"

```python
data = spark.range(5,10)
data.write.format("delta").mode("overwrite").save(delta_table_path)
df.show()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
var data = spark.Range(5,10);
data.Write().Format("delta").Mode("overwrite").Save(deltaTablePath);
df.Show();
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
val data = spark.range(5, 10)
data.write.format("delta").mode("overwrite").save(deltaTablePath)
df.show()
```

::: zone-end

결과:

| ID|
|---|
|  7|
|  8|
|  5|
|  9|
|  6|

여기서는 5개의 레코드에서 모두 새 값을 포함하도록 업데이트되었음을 확인할 수 있습니다.

## <a name="save-as-catalog-tables"></a>카탈로그 테이블로 저장

Delta Lake는 관리형 또는 외부 카탈로그 테이블에 쓸 수 있습니다.

:::zone pivot = "programming-language-python"

```python
data.write.format("delta").saveAsTable("ManagedDeltaTable")
spark.sql("CREATE TABLE ExternalDeltaTable USING DELTA LOCATION '{0}'".format(delta_table_path))
spark.sql("SHOW TABLES").show()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
data.Write().Format("delta").SaveAsTable("ManagedDeltaTable");
spark.Sql($"CREATE TABLE ExternalDeltaTable USING DELTA LOCATION '{deltaTablePath}'");
spark.Sql("SHOW TABLES").Show();
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
data.write.format("delta").saveAsTable("ManagedDeltaTable")
spark.sql(s"CREATE TABLE ExternalDeltaTable USING DELTA LOCATION '$deltaTablePath'")
spark.sql("SHOW TABLES").show
```

::: zone-end

결과:

|데이터베이스|         tableName|isTemporary|
|--------|------------------|-----------|
| default|externaldeltatable|      false|
| default| manageddeltatable|      false|

이 코드를 사용하여 관리형 테이블이라고 하는 기존 데이터 프레임에서 새 테이블을 카탈로그에 만들었습니다. 그런 다음, 외부 테이블이라고 하는 기존 위치를 사용하는 새 외부 테이블을 카탈로그에 정의했습니다. 출력에서 두 테이블이 만들어진 방법에 관계없이 모두 카탈로그에 나열되어 있음을 확인할 수 있습니다.

이제 이러한 두 테이블의 확장 속성을 볼 수 있습니다.

:::zone pivot = "programming-language-python"

```python
spark.sql("DESCRIBE EXTENDED ManagedDeltaTable").show(truncate=False)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
spark.Sql("DESCRIBE EXTENDED ManagedDeltaTable").Show(truncate: 0);
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
spark.sql("DESCRIBE EXTENDED ManagedDeltaTable").show(truncate=false)
```

::: zone-end

결과:

|col_name                    |data_type                                                                                                    |주석|
|----------------------------|-------------------------------------------------------------------------------------------------------------|-------|
|id                          |bigint                                                                                                       |null   |
|                            |                                                                                                             |       |
|자세한 테이블 정보  |                                                                                                             |       |
|데이터베이스                    |default                                                                                                      |       |
|테이블                       |manageddeltatable                                                                                            |       |
|소유자                       |trusted-service-user                                                                                         |       |
|만든 시간                |Sat Apr 25 00:35:34 UTC 2020                                                                                 |       |
|마지막 액세스                 |Thu Jan 01 00:00:00 UTC 1970                                                                                 |       |
|만든 사람                  |Spark 2.4.4.2.6.99.201-11401300                                                                              |       |
|유형                        |MANAGED                                                                                                      |       |
|공급자                    |delta                                                                                                        |       |
|테이블 속성            |[transient_lastDdlTime=1587774934]                                                                           |       |
|통계                  |2,407바이트                                                                                                   |       |
|위치                    |abfss://data@<data lake>.dfs.core.windows.net/synapse/workspaces/<workspace name>/warehouse/manageddeltatable|       |
|Serde 라이브러리               |org.apache.hadoop.hive.serde2.lazy.LazySimpleSerDe                                                           |       |
|InputFormat                 |org.apache.hadoop.mapred.SequenceFileInputFormat                                                             |       |
|OutputFormat                |org.apache.hadoop.hive.ql.io.HiveSequenceFileOutputFormat                                                    |       |
|스토리지 속성          |[serialization.format=1]                                                                                     |       |

:::zone pivot = "programming-language-python"

```python
spark.sql("DESCRIBE EXTENDED ExternalDeltaTable").show(truncate=False)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
spark.Sql("DESCRIBE EXTENDED ExternalDeltaTable").Show(truncate: 0);
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
spark.sql("DESCRIBE EXTENDED ExternalDeltaTable").show(truncate=false)
```

::: zone-end

결과:

|col_name                    |data_type                                                             |주석|
|----------------------------|----------------------------------------------------------------------|-------|
|id                          |bigint                                                                |null   |
|                            |                                                                      |       |
|자세한 테이블 정보  |                                                                      |       |
|데이터베이스                    |default                                                               |       |
|테이블                       |externaldeltatable                                                    |       |
|소유자                       |trusted-service-user                                                  |       |
|만든 시간                |Sat Apr 25 00:35:38 UTC 2020                                          |       |
|마지막 액세스                 |Thu Jan 01 00:00:00 UTC 1970                                          |       |
|만든 사람                  |Spark 2.4.4.2.6.99.201-11401300                                       |       |
|유형                        |EXTERNAL                                                              |       |
|공급자                    |DELTA                                                                 |       |
|테이블 속성            |[transient_lastDdlTime=1587774938]                                    |       |
|위치                    |abfss://data@<data lake>.dfs.core.windows.net/delta/delta-table-587152|       |
|Serde 라이브러리               |org.apache.hadoop.hive.serde2.lazy.LazySimpleSerDe                    |       |
|InputFormat                 |org.apache.hadoop.mapred.SequenceFileInputFormat                      |       |
|OutputFormat                |org.apache.hadoop.hive.ql.io.HiveSequenceFileOutputFormat             |       |
|스토리지 속성          |[serialization.format=1]                                              |       |

## <a name="conditional-update-without-overwrite"></a>덮어쓰지 않고 조건부 업데이트

Delta Lake는 테이블에서 데이터를 조건부 업데이트, 삭제 및 병합(일반적으로 upsert라고 함)을 수행할 수 있는 프로그래밍 방식 API를 제공합니다.

:::zone pivot = "programming-language-python"

```python
from delta.tables import *
from pyspark.sql.functions import *

delta_table = DeltaTable.forPath(spark, delta_table_path)

delta_table.update(
  condition = expr("id % 2 == 0"),
  set = { "id": expr("id + 100") })
delta_table.toDF().show()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
using Microsoft.Spark.Extensions.Delta;
using Microsoft.Spark.Extensions.Delta.Tables;
using Microsoft.Spark.Sql;
using static Microsoft.Spark.Sql.Functions;

var deltaTable = DeltaTable.ForPath(deltaTablePath);

deltaTable.Update(
  condition: Expr("id % 2 == 0"),
  set: new Dictionary<string, Column>(){{ "id", Expr("id + 100") }});
deltaTable.ToDF().Show();
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
import io.delta.tables._
import org.apache.spark.sql.functions._

val deltaTable = DeltaTable.forPath(deltaTablePath)

// Update every even value by adding 100 to it
deltaTable.update(
  condition = expr("id % 2 == 0"),
  set = Map("id" -> expr("id + 100")))
deltaTable.toDF.show
```

::: zone-end

결과:

| ID|
|---|
|106|
|108|
|  5|
|  7|
|  9|

여기서는 100을 모든 짝수 ID에 추가했습니다.

:::zone pivot = "programming-language-python"

```python
delta_table.delete("id % 2 == 0")
delta_table.toDF().show()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
deltaTable.Delete(condition: Expr("id % 2 == 0"));
deltaTable.ToDF().Show();
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
deltaTable.delete(condition = expr("id % 2 == 0"))
deltaTable.toDF.show
```

::: zone-end

결과:

| ID|
|---|
|  5|
|  7|
|  9|

모든 짝수 행이 삭제되었습니다.

:::zone pivot = "programming-language-python"

```python
new_data = spark.range(0,20).alias("newData")

delta_table.alias("oldData")\
    .merge(new_data.alias("newData"), "oldData.id = newData.id")\
    .whenMatchedUpdate(set = { "id": lit("-1")})\
    .whenNotMatchedInsert(values = { "id": col("newData.id") })\
    .execute()

delta_table.toDF().show(100)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
var newData = spark.Range(20).As("newData");

deltaTable
    .As("oldData")
    .Merge(newData, "oldData.id = newData.id")
    .WhenMatched()
        .Update(new Dictionary<string, Column>() {{"id", Lit("-1")}})
    .WhenNotMatched()
        .Insert(new Dictionary<string, Column>() {{"id", Col("newData.id")}})
    .Execute();

deltaTable.ToDF().Show(100);
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
val newData = spark.range(0, 20).toDF

deltaTable.as("oldData").
  merge(
    newData.as("newData"),
    "oldData.id = newData.id").
  whenMatched.
  update(Map("id" -> lit(-1))).
  whenNotMatched.
  insert(Map("id" -> col("newData.id"))).
  execute()

deltaTable.toDF.show()
```

::: zone-end

결과:

| ID|
|---|
| 18|
| 15|
| 19|
|  2|
|  1|
|  6|
|  8|
|  3|
| -1|
| 10|
| 13|
|  0|
| 16|
|  4|
| -1|
| 12|
| 11|
| 14|
| -1|
| 17|

여기에는 기존 데이터의 조합이 있습니다. 업데이트(WhenMatched) 코드 경로에서 -1 값이 기존 데이터에 할당되었습니다. 코드 조각의 위쪽에 만들어지고 삽입 코드 경로(WhenNotMatched)를 통해 추가된 새 데이터도 추가되었습니다.

### <a name="history"></a>기록

Delta Lake에는 테이블의 기록을 살펴볼 수 있는 기능이 있습니다. 즉, 기본 Delta 테이블에 적용된 변경 내용입니다. 아래 셀에서는 기록을 간단하게 검사하는 방법을 보여 줍니다.

:::zone pivot = "programming-language-python"

```python
delta_table.history().show(20, 1000, False)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
deltaTable.History().Show(20, 1000, false);
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
deltaTable.history.show(false)
```

::: zone-end

결과:

|버전|          timestamp|userId|userName|operation|                                                operationParameters| 작업(job)|Notebook|clusterId|readVersion|isolationLevel|isBlindAppend|
|-------|-------------------|------|--------|---------|-------------------------------------------------------------------|----|--------|---------|-----------|--------------|-------------|
|      4|2020-04-25 00:36:27|  null|    null|    MERGE|                       [predicate -> (oldData.`ID` = newData.`ID`)]|null|    null|     null|          3|          null|        false|
|      3|2020-04-25 00:36:08|  null|    null|   Delete|[predicate -> ["((`ID` % CAST(2 AS BIGINT)) = CAST(0 AS BIGINT))"]]|null|    null|     null|          2|          null|        false|
|      2|2020-04-25 00:35:51|  null|    null|   UPDATE| [predicate -> ((ID#744L % cast(2 as bigint)) = cast(0 as bigint))]|null|    null|     null|          1|          null|        false|
|      1|2020-04-25 00:35:05|  null|    null|    WRITE|                             [mode -> Overwrite, partitionBy -> []]|null|    null|     null|          0|          null|        false|
|      0|2020-04-25 00:34:34|  null|    null|    WRITE|                         [mode -> ErrorIfExists, partitionBy -> []]|null|    null|     null|       null|          null|         true|

여기서는 위의 코드 조각에 대한 모든 수정 내용을 확인할 수 있습니다.

## <a name="read-older-versions-of-data-using-time-travel"></a>시간 이동을 사용하여 이전 버전의 데이터 읽기

시간 이동이라는 기능을 사용하여 Delta Lake 테이블의 이전 스냅샷을 쿼리할 수 있습니다. 덮어쓴 데이터에 액세스하려면 versionAsOf 옵션을 사용하여 첫 번째 데이터 세트를 덮어쓰기 전의 테이블에 대한 스냅샷을 쿼리할 수 있습니다.

아래 셀이 실행되면 덮어쓰기 전의 첫 번째 데이터 세트가 표시됩니다. 시간 이동은 Delta Lake 트랜잭션 로그의 성능을 활용하여 더 이상 테이블에 없는 데이터에 액세스할 수 있는 매우 강력한 기능입니다. 버전 0 옵션을 제거하거나 버전 1을 지정하면 최신 데이터를 다시 볼 수 있습니다. 자세한 내용은 [테이블의 이전 스냅샷 쿼리](https://docs.delta.io/latest/delta-batch.html#deltatimetravel)를 참조하세요.

:::zone pivot = "programming-language-python"

```python
df = spark.read.format("delta").option("versionAsOf", 0).load(delta_table_path)
df.show()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
var df = spark.Read().Format("delta").Option("versionAsOf", 0).Load(deltaTablePath);
df.Show();
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
val df = spark.read.format("delta").option("versionAsOf", 0).load(deltaTablePath)
df.show()
```

::: zone-end

결과:

| ID|
|---|
|  0|
|  1|
|  4|
|  3|
|  2|

여기서는 가장 오래된 버전의 데이터로 돌아간 것을 확인할 수 있습니다.

## <a name="write-a-stream-of-data-to-a-table"></a>테이블에 데이터 스트림 쓰기

Spark의 구조적 스트리밍을 사용하여 Delta Lake 테이블에 쓸 수도 있습니다. Delta Lake 트랜잭션 로그는 테이블에 대해 동시에 실행되는 다른 스트림 또는 일괄 처리 쿼리가 있는 경우에도 정확히 한 번만 처리하도록 보장합니다. 기본적으로 스트림은 새 레코드를 테이블에 추가하는 추가 모드로 실행됩니다.

Delta Lake와 구조적 스트리밍을 통합하는 방법에 대한 자세한 내용은 [테이블 스트리밍 읽기 및 쓰기](https://docs.delta.io/latest/delta-streaming.html)를 참조하세요.

아래 셀에서 수행하는 작업은 다음과 같습니다.

* 셀 30 - 새로 추가된 데이터 표시
* 셀 31 - 기록 검사
* 셀 32 - 구조적 스트리밍 작업 중지
* 셀 33 - 기록 검사 <-- 추가가 중지되었음을 알 수 있습니다.

먼저 시퀀스를 생성하고 작업을 Delta 테이블에 쓰도록 하는 간단한 Spark 스트리밍 작업을 설정합니다.

:::zone pivot = "programming-language-python"

```python
streaming_df = spark.readStream.format("rate").load()
stream = streaming_df\
    .selectExpr("value as id")\
    .writeStream\
    .format("delta")\
    .option("checkpointLocation", "/tmp/checkpoint-{0}".format(session_id))\
    .start(delta_table_path)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
var streamingDf = spark.ReadStream().Format("rate").Load();
var stream = streamingDf.SelectExpr("value as id").WriteStream().Format("delta").Option("checkpointLocation", $"/tmp/checkpoint-{sessionId}").Start(deltaTablePath);
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
val streamingDf = spark.readStream.format("rate").load()
val stream = streamingDf.select($"value" as "id").writeStream.format("delta").option("checkpointLocation", s"/tmp/checkpoint-$sessionId").start(deltaTablePath)
```

::: zone-end

## <a name="read-a-stream-of-changes-from-a-table"></a>테이블에서 변경 내용 스트림 읽기

스트림에서 Delta Lake 테이블에 쓰는 동안 해당 테이블에서 스트리밍 원본으로 읽을 수도 있습니다. 예를 들어 Delta Lake 테이블의 모든 변경 내용을 출력하는 다른 스트리밍 쿼리를 시작할 수 있습니다.

:::zone pivot = "programming-language-python"

```python
delta_table.toDF().sort(col("id").desc()).show(100)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
deltaTable.ToDF().Sort(Col("id").Desc()).Show(100);
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
deltaTable.toDF.sort($"id".desc).show
```

::: zone-end

결과:

| ID|
|---|
| 19|
| 18|
| 17|
| 16|
| 15|
| 14|
| 13|
| 12|
| 11|
| 10|
|  8|
|  6|
|  4|
|  3|
|  2|
|  1|
|  0|
| -1|
| -1|
| -1|

:::zone pivot = "programming-language-python"

```python
delta_table.history().drop("userId", "userName", "job", "notebook", "clusterId", "isolationLevel", "isBlindAppend").show(20, 1000, False)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
deltaTable.History().Drop("userId", "userName", "job", "notebook", "clusterId", "isolationLevel", "isBlindAppend").Show(20, 1000, false);
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
deltaTable.history.show
```

::: zone-end

결과:

|버전|          timestamp|       operation|                                                                  operationParameters|readVersion|
|-------|-------------------|----------------|-------------------------------------------------------------------------------------|-----------|
|      5|2020-04-25 00:37:09|STREAMING UPDATE|[outputMode -> Append, queryId -> d26b4f8a-7e5a-44f2-a5fb-23a7bd02aef7, epochId -> 0]|          4|
|      4|2020-04-25 00:36:27|           MERGE|                                         [predicate -> (oldData.`id` = newData.`id`)]|          3|
|      3|2020-04-25 00:36:08|          Delete|                  [predicate -> ["((`id` % CAST(2 AS BIGINT)) = CAST(0 AS BIGINT))"]]|          2|
|      2|2020-04-25 00:35:51|          UPDATE|                   [predicate -> ((id#744L % cast(2 as bigint)) = cast(0 as bigint))]|          1|
|      1|2020-04-25 00:35:05|           WRITE|                                               [mode -> Overwrite, partitionBy -> []]|          0|
|      0|2020-04-25 00:34:34|           WRITE|                                           [mode -> ErrorIfExists, partitionBy -> []]|       null|

여기서는 기록 보기의 보기 환경을 간소화하기 위해 관련성이 낮은 열 중 일부를 삭제합니다.

:::zone pivot = "programming-language-python"

```python
stream.stop()
delta_table.history().drop("userId", "userName", "job", "notebook", "clusterId", "isolationLevel", "isBlindAppend").show(100, 1000, False)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
stream.Stop();
deltaTable.History().Drop("userId", "userName", "job", "notebook", "clusterId", "isolationLevel", "isBlindAppend").Show(100, 1000, false);
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
stream.stop
deltaTable.history.show
```

결과:

|버전|          timestamp|       operation|                                                                  operationParameters|readVersion|
|-------|-------------------|----------------|-------------------------------------------------------------------------------------|-----------|
|      5|2020-04-25 00:37:09|STREAMING UPDATE|[outputMode -> Append, queryId -> d26b4f8a-7e5a-44f2-a5fb-23a7bd02aef7, epochId -> 0]|          4|
|      4|2020-04-25 00:36:27|           MERGE|                                         [predicate -> (oldData.`id` = newData.`id`)]|          3|
|      3|2020-04-25 00:36:08|          Delete|                  [predicate -> ["((`id` % CAST(2 AS BIGINT)) = CAST(0 AS BIGINT))"]]|          2|
|      2|2020-04-25 00:35:51|          UPDATE|                   [predicate -> ((id#744L % cast(2 as bigint)) = cast(0 as bigint))]|          1|
|      1|2020-04-25 00:35:05|           WRITE|                                               [mode -> Overwrite, partitionBy -> []]|          0|
|      0|2020-04-25 00:34:34|           WRITE|                                           [mode -> ErrorIfExists, partitionBy -> []]|       null|

::: zone-end

## <a name="convert-parquet-to-delta"></a>Parquet를 Delta로 변환

Parquet 형식에서 Delta로의 내부 변환을 수행할 수 있습니다.

여기서는 기존 테이블이 델타 형식인지 여부를 테스트합니다.
:::zone pivot = "programming-language-python"

```python
parquet_path = "/parquet/parquet-table-{0}".format(session_id)
data = spark.range(0,5)
data.write.parquet(parquet_path)
DeltaTable.isDeltaTable(spark, parquet_path)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
var parquetPath = $"/parquet/parquet-table-{sessionId}";
var data = spark.Range(0,5);
data.Write().Parquet(parquetPath);
DeltaTable.IsDeltaTable(parquetPath)
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
val parquetPath = s"/parquet/parquet-table-$sessionId"
val data = spark.range(0,5)
data.write.parquet(parquetPath)
DeltaTable.isDeltaTable(parquetPath)
```

::: zone-end

결과:

False

이제 데이터를 델타 형식으로 변환하고 작동하는지 확인합니다.

:::zone pivot = "programming-language-python"

```python
DeltaTable.convertToDelta(spark, "parquet.`{0}`".format(parquet_path))
DeltaTable.isDeltaTable(spark, parquet_path)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
DeltaTable.ConvertToDelta(spark, $"parquet.`{parquetPath}`");
DeltaTable.IsDeltaTable(parquetPath)
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
DeltaTable.convertToDelta(spark, s"parquet.`$parquetPath`")
DeltaTable.isDeltaTable(parquetPath)
```

::: zone-end

결과:

True

## <a name="sql-support"></a>SQL 지원

Delta는 SQL을 통해 테이블 유틸리티 명령을 지원합니다. SQL을 사용하여 다음을 수행할 수 있습니다.

* DeltaTable 기록 가져오기
* DeltaTable 완전 비우기
* Parquet 파일을 Delta로 변환

:::zone pivot = "programming-language-python"

```python
spark.sql("DESCRIBE HISTORY delta.`{0}`".format(delta_table_path)).show()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
spark.Sql($"DESCRIBE HISTORY delta.`{deltaTablePath}`").Show();
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
spark.sql(s"DESCRIBE HISTORY delta.`$deltaTablePath`").show()
```

::: zone-end

결과:

|버전|          timestamp|userId|userName|       operation| operationParameters| 작업(job)|Notebook|clusterId|readVersion|isolationLevel|isBlindAppend|
|-------|-------------------|------|--------|----------------|--------------------|----|--------|---------|-----------|--------------|-------------|
|      5|2020-04-25 00:37:09|  null|    null|STREAMING UPDATE|[outputMode -> Ap...|null|    null|     null|          4|          null|         true|
|      4|2020-04-25 00:36:27|  null|    null|           MERGE|[predicate -> (ol...|null|    null|     null|          3|          null|        false|
|      3|2020-04-25 00:36:08|  null|    null|          Delete|[predicate -> ["(...|null|    null|     null|          2|          null|        false|
|      2|2020-04-25 00:35:51|  null|    null|          UPDATE|[predicate -> ((i...|null|    null|     null|          1|          null|        false|
|      1|2020-04-25 00:35:05|  null|    null|           WRITE|[mode -> Overwrit...|null|    null|     null|          0|          null|        false|
|      0|2020-04-25 00:34:34|  null|    null|           WRITE|[mode -> ErrorIfE...|null|    null|     null|       null|          null|         true|

:::zone pivot = "programming-language-python"

```python
spark.sql("VACUUM delta.`{0}`".format(delta_table_path)).show()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
spark.Sql($"VACUUM delta.`{deltaTablePath}`").Show();
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
spark.sql(s"VACUUM delta.`$deltaTablePath`").show()
```

::: zone-end

결과:

|                path|
|--------------------|
|abfss://data@arca...|

이제 테이블이 델타 형식 테이블이 아닌지 확인한 다음, Spark SQL을 사용하여 델타 형식으로 변환하고 올바르게 변환되었는지 확인합니다.

:::zone pivot = "programming-language-python"

```python
parquet_id = random.randint(0,1000)
parquet_path = "/parquet/parquet-table-{0}-{1}".format(session_id, parquet_path)
data = spark.range(0,5)
data.write.parquet(parquet_path)
DeltaTable.isDeltaTable(spark, parquet_path)
spark.sql("CONVERT TO DELTA parquet.`{0}`".format(parquet_path))
DeltaTable.isDeltaTable(spark, parquet_path)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
var parquetId =  (new Random()).Next(10000000);
var parquetPath = $"/parquet/parquet-table-{sessionId}-{parquetId}";
var data = spark.Range(0,5);
data.Write().Parquet(parquetPath);
DeltaTable.IsDeltaTable(parquetPath);
spark.Sql($"CONVERT TO DELTA parquet.`{parquetPath}`");
DeltaTable.IsDeltaTable(parquetPath);
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
val parquetId = scala.util.Random.nextInt(1000)
val parquetPath = s"/parquet/parquet-table-$sessionId-$parquetId"
val data = spark.range(0,5)
data.write.parquet(parquetPath)
DeltaTable.isDeltaTable(parquetPath)
spark.sql(s"CONVERT TO DELTA parquet.`$parquetPath`")
DeltaTable.isDeltaTable(parquetPath)
```

::: zone-end

결과:

True

전체 설명서는 [Delta Lake 설명서 페이지](https://docs.delta.io/latest/delta-intro.html)를 참조하세요.

자세한 내용은 [Delta Lake 프로젝트](https://github.com/delta-io/delta)를 참조하세요.

## <a name="next-steps"></a>다음 단계

* [Apache Spark용 .NET 설명서](/dotnet/spark?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
* [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
