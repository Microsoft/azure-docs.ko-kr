---
title: Azure Cosmos DB SQL API로 일 대 일 관계형 데이터 마이그레이션
description: SQL API에 대한 일 대 일 관계에 대한 복잡 한 데이터 마이그레이션을 처리 하는 방법을 알아봅니다.
author: TheovanKraay
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 12/12/2019
ms.author: thvankra
ms.openlocfilehash: 467e9627a2623779bd808ca5aebdf76d8a5eda42
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/11/2020
ms.locfileid: "75896637"
---
# <a name="migrate-one-to-few-relational-data-into-azure-cosmos-db-sql-api-account"></a>Azure Cosmos DB SQL API 계정으로 일 대 일 관계형 데이터 마이그레이션

관계형 데이터베이스에서 Azure Cosmos DB SQL API로 마이그레이션하려면 최적화를 위해 데이터 모델을 변경 해야 할 수 있습니다.

한 가지 일반적인 변환은 JSON 문서 하나에 관련 하위 항목을 포함 하 여 데이터를 비 정규화는 것입니다. 여기에서는 Azure Data Factory 또는 Azure Databricks를 사용 하 여이에 대한 몇 가지 옵션을 살펴보겠습니다. Cosmos DB에 대한 데이터 모델링에 대한 일반적인 지침은 [Azure Cosmos DB에서 데이터 모델링](modeling-data.md)을 검토 하세요.  

## <a name="example-scenario"></a>예제 시나리오

SQL database, Orders 및 OrderDetails에는 다음과 같은 두 개의 테이블이 있다고 가정 합니다.


![주문 세부 정보](./media/migrate-relational-to-cosmos-sql-api/orders.png)

마이그레이션하는 동안이 일대일 관계를 하나의 JSON 문서로 결합 하려고 합니다. 이렇게 하려면 아래와 같이 "FOR JSON"을 사용 하 여 T-sql 쿼리를 만들 수 있습니다.

```sql
SELECT
  o.OrderID,
  o.OrderDate,
  o.FirstName,
  o.LastName,
  o.Address,
  o.City,
  o.State,
  o.PostalCode,
  o.Country,
  o.Phone,
  o.Total,
  (select OrderDetailId, ProductId, UnitPrice, Quantity from OrderDetails od where od.OrderId = o.OrderId for json auto) as OrderDetails
FROM Orders o;
```

이 쿼리의 결과는 다음과 같습니다. 

![주문 세부 정보](./media/migrate-relational-to-cosmos-sql-api/for-json-query-result.png#lightbox)


이상적으로는 단일 Azure Data Factory (ADF) 복사 작업을 사용 하 여 SQL 데이터를 원본으로 쿼리하고 Azure Cosmos DB 싱크에 적절 한 JSON 개체로 출력을 직접 작성 하는 것이 좋습니다. 현재는 하나의 복사 작업에서 필요한 JSON 변환을 수행할 수 없습니다. 위의 쿼리 결과를 Azure Cosmos DB SQL API 컨테이너에 복사 하려고 하면 예상 되는 JSON 배열이 아니라 문서에 대한 문자열 속성으로 OrderDetails 필드가 표시 됩니다.

다음 방법 중 하나를 사용 하 여이 현재 제한을 해결할 수 있습니다.

* **두 개의 복사 작업을 사용 하 여 Azure Data Factory를 사용**합니다. 
  1. SQL에서 중간 blob 저장소 위치의 텍스트 파일로 JSON 형식 데이터를 가져옵니다. 
  2. JSON 텍스트 파일의 데이터를 Azure Cosmos DB 컨테이너에 로드 합니다.

* **Azure Databricks를 사용 하 여 SQL에서 읽고 Azure Cosmos DB에 쓸 수** 있습니다. 여기에는 두 가지 옵션이 제공 됩니다.


이러한 방법을 보다 자세히 살펴보겠습니다.

## <a name="azure-data-factory"></a>Azure Data Factory

OrderDetails를 대상 Cosmos DB 문서에 JSON 배열로 포함할 수는 없지만 두 가지 별도의 복사 작업을 사용 하 여 문제를 해결할 수 있습니다.

### <a name="copy-activity-1-sqljsontoblobtext"></a>복사 작업 #1: SqlJsonToBlobText

원본 데이터의 경우 SQL 쿼리를 사용 하 여 SQL Server OPENJSON 및 JSON 경로 기능을 사용 하 여 행 당 하나의 JSON 개체 (순서를 나타냄)가 있는 단일 열로 결과 집합을 가져옵니다.

```sql
SELECT [value] FROM OPENJSON(
  (SELECT
    id = o.OrderID,
    o.OrderDate,
    o.FirstName,
    o.LastName,
    o.Address,
    o.City,
    o.State,
    o.PostalCode,
    o.Country,
    o.Phone,
    o.Total,
    (select OrderDetailId, ProductId, UnitPrice, Quantity from OrderDetails od where od.OrderId = o.OrderId for json auto) as OrderDetails
   FROM Orders o FOR JSON PATH)
)
```

![ADF 복사본](./media/migrate-relational-to-cosmos-sql-api/adf1.png)


SqlJsonToBlobText 복사 작업의 싱크에 대해 "구분 된 텍스트"를 선택 하 고 동적으로 생성 된 고유한 파일 이름 (예: '@concat(파이프라인 ())을 사용 하 여 Azure Blob Storage의 특정 폴더를 가리킵니다. RunId, '. json ').
텍스트 파일은 "분리 된" 것이 아니므로 쉼표를 사용 하 여 별도의 열로 구문 분석 하지 않고 큰따옴표 (")를 유지 하려는 경우" 열 구분 기호 "를 탭 (" \t ")으로 설정 하거나 데이터에서 발생 하지 않는 다른 문자를" 따옴표 문자 "로 설정 합니다.  "따옴표 문자 없음"으로

![ADF 복사본](./media/migrate-relational-to-cosmos-sql-api/adf2.png)

### <a name="copy-activity-2-blobjsontocosmos"></a>복사 작업 #2: BlobJsonToCosmos

다음으로 첫 번째 작업에서 만든 텍스트 파일에 대해 Azure Blob Storage를 찾는 두 번째 복사 작업을 추가 하 여 ADF 파이프라인을 수정 합니다. 텍스트 파일에 있는 JSON 행당 하나의 문서로 Cosmos DB 싱크에 삽입 하는 "JSON" 소스로 처리 합니다.

![ADF 복사본](./media/migrate-relational-to-cosmos-sql-api/adf3.png)

필요에 따라 파이프라인에 "삭제" 작업을 추가 하 여 각 실행 전에/Orders/폴더에 남아 있는 이전 파일을 모두 삭제 합니다. 이제 ADF 파이프라인이 다음과 같이 표시 됩니다.

![ADF 복사본](./media/migrate-relational-to-cosmos-sql-api/adf4.png)

위의 파이프라인을 트리거한 후에는 행 마다 하나의 JSON 개체를 포함 하는 중간 Azure Blob Storage 위치에 생성 된 파일을 볼 수 있습니다.

![ADF 복사본](./media/migrate-relational-to-cosmos-sql-api/adf5.png)

Cosmos DB 컬렉션에 올바르게 포함 된 OrderDetails 삽입 된 주문 문서도 표시 됩니다.

![ADF 복사본](./media/migrate-relational-to-cosmos-sql-api/adf6.png)


## <a name="azure-databricks"></a>Azure Databricks

[Azure Databricks](https://azure.microsoft.com/services/databricks/) 에서 Spark를 사용 하 여 Azure Blob Storage에서 중간 텍스트/JSON 파일을 만들지 않고 SQL Database 원본에서 Azure Cosmos DB 대상으로 데이터를 복사할 수도 있습니다. 

> [!NOTE]
> 명확성과 단순성을 위해 아래 코드 조각은 더미 데이터베이스 암호를 명시적으로 인라인으로 포함 하지만 항상 Azure Databricks 암호를 사용 해야 합니다.
>

먼저 필요한 [SQL 커넥터](https://docs.databricks.com/data/data-sources/sql-databases-azure.html) 와 [Azure Cosmos DB 커넥터](https://docs.databricks.com/data/data-sources/azure/cosmosdb-connector.html) 라이브러리를 만들어 Azure Databricks 클러스터에 연결 합니다. 클러스터를 다시 시작 하 여 라이브러리가 로드 되었는지 확인 합니다.

![Databricks](./media/migrate-relational-to-cosmos-sql-api/databricks1.png)

다음으로 Scala 및 Python에 대한 두 가지 샘플을 제공 합니다. 

### <a name="scala"></a>스칼라
여기서는 "FOR JSON" 출력을 사용 하 여 SQL 쿼리 결과를 데이터 프레임로 가져옵니다.

```scala
// Connect to Azure SQL https://docs.databricks.com/data/data-sources/sql-databases-azure.html
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._
val configSql = Config(Map(
  "url"          -> "xxxx.database.windows.net",
  "databaseName" -> "xxxx",
  "queryCustom"  -> "SELECT o.OrderID, o.OrderDate, o.FirstName, o.LastName, o.Address, o.City, o.State, o.PostalCode, o.Country, o.Phone, o.Total, (SELECT OrderDetailId, ProductId, UnitPrice, Quantity FROM OrderDetails od WHERE od.OrderId = o.OrderId FOR JSON AUTO) as OrderDetails FROM Orders o",
  "user"         -> "xxxx", 
  "password"     -> "xxxx" // NOTE: For clarity and simplicity, this example includes secrets explicitely as a string, but you should always use Databricks secrets
))
// Create DataFrame from Azure SQL query
val orders = sqlContext.read.sqlDB(configSql)
display(orders)
```

![Databricks](./media/migrate-relational-to-cosmos-sql-api/databricks2.png)

다음으로 Cosmos DB 데이터베이스 및 컬렉션에 연결 합니다.

```scala
// Connect to Cosmos DB https://docs.databricks.com/data/data-sources/azure/cosmosdb-connector.html
import org.joda.time._
import org.joda.time.format._
import com.microsoft.azure.cosmosdb.spark.schema._
import com.microsoft.azure.cosmosdb.spark.CosmosDBSpark
import com.microsoft.azure.cosmosdb.spark.config.Config
import org.apache.spark.sql.functions._
import org.joda.time._
import org.joda.time.format._
import com.microsoft.azure.cosmosdb.spark.schema._
import com.microsoft.azure.cosmosdb.spark.CosmosDBSpark
import com.microsoft.azure.cosmosdb.spark.config.Config
import org.apache.spark.sql.functions._
val configMap = Map(
  "Endpoint" -> "https://xxxx.documents.azure.com:443/",
  // NOTE: For clarity and simplicity, this example includes secrets explicitely as a string, but you should always use Databricks secrets
  "Masterkey" -> "xxxx",
  "Database" -> "StoreDatabase",
  "Collection" -> "Orders")
val configCosmos = Config(configMap)
```

마지막으로, 스키마를 정의 하 고 from_json를 사용 하 여 CosmosDB 컬렉션에 저장 하기 전에 데이터 프레임를 적용 합니다.

```scala
// Convert DataFrame to proper nested schema
import org.apache.spark.sql.types._
val orderDetailsSchema = ArrayType(StructType(Array(
    StructField("OrderDetailId",IntegerType,true),
    StructField("ProductId",IntegerType,true),
    StructField("UnitPrice",DoubleType,true),
    StructField("Quantity",IntegerType,true)
  )))
val ordersWithSchema = orders.select(
  col("OrderId").cast("string").as("id"),
  col("OrderDate").cast("string"),
  col("FirstName").cast("string"),
  col("LastName").cast("string"),
  col("Address").cast("string"),
  col("City").cast("string"),
  col("State").cast("string"),
  col("PostalCode").cast("string"),
  col("Country").cast("string"),
  col("Phone").cast("string"),
  col("Total").cast("double"),
  from_json(col("OrderDetails"), orderDetailsSchema).as("OrderDetails")
)
display(ordersWithSchema)
// Save nested data to Cosmos DB
CosmosDBSpark.save(ordersWithSchema, configCosmos)
```

![Databricks](./media/migrate-relational-to-cosmos-sql-api/databricks3.png)


### <a name="python"></a>Python

다른 방법으로 Spark에서 JSON 변환을 실행 해야 할 수도 있습니다 (원본 데이터베이스가 "FOR JSON" 또는 유사한 작업을 지원 하지 않는 경우). 또는 매우 큰 데이터 집합에 대해 병렬 작업을 사용 하려는 경우가 있습니다. 여기에서는 PySpark 샘플을 제공 합니다. 첫 번째 셀에서 원본 및 대상 데이터베이스 연결을 구성 하 여 시작 합니다.

```python
import uuid
import pyspark.sql.functions as F
from pyspark.sql.functions import col
from pyspark.sql.types import StringType,DateType,LongType,IntegerType,TimestampType
 
#JDBC connect details for SQL Server database
jdbcHostname = "jdbcHostname"
jdbcDatabase = "OrdersDB"
jdbcUsername = "jdbcUsername"
jdbcPassword = "jdbcPassword"
jdbcPort = "1433"
 
connectionProperties = {
  "user" : jdbcUsername,
  "password" : jdbcPassword,
  "driver" : "com.microsoft.sqlserver.jdbc.SQLServerDriver"
}
jdbcUrl = "jdbc:sqlserver://{0}:{1};database={2};user={3};password={4}".format(jdbcHostname, jdbcPort, jdbcDatabase, jdbcUsername, jdbcPassword)
 
#Connect details for Target Azure Cosmos DB SQL API account
writeConfig = {
    "Endpoint": "Endpoint",
    "Masterkey": "Masterkey",
    "Database": "OrdersDB",
    "Collection": "Orders",
    "Upsert": "true"
}
```

그런 다음 주문 및 주문 정보 레코드에 대한 원본 데이터베이스 (이 경우 SQL Server)를 쿼리하여 결과를 Spark 데이터 프레임에 넣습니다. 또한 모든 주문 Id와 병렬 작업용 스레드 풀을 포함 하는 목록을 만듭니다.

```python
import json
import ast
import pyspark.sql.functions as F
import uuid
import numpy as np
import pandas as pd
from functools import reduce
from pyspark.sql import SQLContext
from pyspark.sql.types import *
from pyspark.sql import *
from pyspark.sql.functions import exp
from pyspark.sql.functions import col
from pyspark.sql.functions import lit
from pyspark.sql.functions import array
from pyspark.sql.types import *
from multiprocessing.pool import ThreadPool
 
#get all orders
orders = sqlContext.read.jdbc(url=jdbcUrl, table="orders", properties=connectionProperties)
 
#get all order details
orderdetails = sqlContext.read.jdbc(url=jdbcUrl, table="orderdetails", properties=connectionProperties)
 
#get all OrderId values to pass to map function 
orderids = orders.select('OrderId').collect()
 
#create thread pool big enough to process merge of details to orders in parallel
pool = ThreadPool(10)
```

그런 다음 대상 SQL API 컬렉션에 주문을 기록 하는 함수를 만듭니다. 이 함수는 지정 된 주문 ID의 모든 주문 정보를 필터링 하 고, JSON 배열로 변환 하 고,이 순서에 따라 대상 SQL API 컬렉션에 작성할 JSON 문서에 배열을 삽입 합니다.

```python
def writeOrder(orderid):
  #filter the order on current value passed from map function
  order = orders.filter(orders['OrderId'] == orderid[0])
  
  #set id to be a uuid
  order = order.withColumn("id", lit(str(uuid.uuid1())))
  
  #add details field to order dataframe
  order = order.withColumn("details", lit(''))
  
  #filter order details dataframe to get details we want to merge into the order document
  orderdetailsgroup = orderdetails.filter(orderdetails['OrderId'] == orderid[0])
  
  #convert dataframe to pandas
  orderpandas = order.toPandas()
  
  #convert the order dataframe to json and remove enclosing brackets
  orderjson = orderpandas.to_json(orient='records', force_ascii=False)
  orderjson = orderjson[1:-1] 
  
  #convert orderjson to a dictionaory so we can set the details element with order details later
  orderjsondata = json.loads(orderjson)
  
  
  #convert orderdetailsgroup dataframe to json, but only if details were returned from the earlier filter
  if (orderdetailsgroup.count() !=0):
    #convert orderdetailsgroup to pandas dataframe to work better with json
    orderdetailsgroup = orderdetailsgroup.toPandas()
    
    #convert orderdetailsgroup to json string
    jsonstring = orderdetailsgroup.to_json(orient='records', force_ascii=False)
    
    #convert jsonstring to dictionary to ensure correct encoding and no corrupt records
    jsonstring = json.loads(jsonstring)
    
    #set details json element in orderjsondata to jsonstring which contains orderdetailsgroup - this merges order details into the order 
    orderjsondata['details'] = jsonstring
 
  #convert dictionary to json
  orderjsondata = json.dumps(orderjsondata)
 
  #read the json into spark dataframe
  df = spark.read.json(sc.parallelize([orderjsondata]))
  
  #write the dataframe (this will be a single order record with merged many-to-one order details) to cosmos db using spark the connector
  #https://docs.microsoft.com/azure/cosmos-db/spark-connector
  df.write.format("com.microsoft.azure.cosmosdb.spark").mode("append").options(**writeConfig).save()
```

마지막으로 스레드 풀에서 map 함수를 사용 하 여 위를 호출 하 여 병렬로 실행 하 고 앞에서 만든 주문 Id 목록을 전달 합니다.

```python
#map order details to orders in parallel using the above function
pool.map(writeOrder, orderids)
```
어느 쪽이 든 끝으로 Cosmos DB 컬렉션의 각 주문 문서 내에 포함 된 OrderDetails를 올바르게 저장 해야 합니다.

![Databricks](./media/migrate-relational-to-cosmos-sql-api/databricks4.png)

## <a name="next-steps"></a>다음 단계
* [Azure Cosmos DB에서 데이터 모델링](https://docs.microsoft.com/azure/cosmos-db/modeling-data) 에 대한 자세한 정보
* [Azure Cosmos DB에서 데이터를 모델링 하 고 분할 하는 방법을](https://docs.microsoft.com/azure/cosmos-db/how-to-model-partition-example) 알아봅니다.
