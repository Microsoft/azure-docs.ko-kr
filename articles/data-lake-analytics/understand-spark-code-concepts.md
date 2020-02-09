---
title: Azure Data Lake Analytics U SQL 개발자를 위한 Apache Spark 코드 개념을 이해 합니다.
description: 이 문서에서는 SQL 개발자가 Spark 코드 개념을 이해 하는 데 도움이 되는 Apache Spark 개념을 설명 합니다.
author: guyhay
ms.author: guyhay
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: conceptual
ms.custom: Understand-apache-spark-code-concepts
ms.date: 10/15/2019
ms.openlocfilehash: bdb38e36a9f1344a3adde15d349a2ec176c0fe95
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/23/2019
ms.locfileid: "74424011"
---
# <a name="understand-apache-spark-code-for-u-sql-developers"></a>U SQL 개발자를 위한 Apache Spark 코드 이해

이 섹션에서는 Apache Spark로 U-SQL 스크립트를 변환 하는 방법에 대한 개략적인 지침을 제공 합니다.

- [두 언어의 처리 패러다임을 비교](#understand-the-u-sql-and-spark-language-and-processing-paradigms) 하 여 시작 합니다.
- 다음 방법에 대한 팁을 제공 합니다.
   - U-SQL의 [행 집합 식을](#transform-u-sql-rowset-expressions-and-sql-based-scalar-expressions) 포함 하는 [Transform 스크립트](#transform-u-sql-scripts)
   - [.NET 코드](#transform-net-code)
   - [데이터 형식](#transform-typed-values)
   - [카탈로그 개체](#transform-u-sql-catalog-objects)입니다.

## <a name="understand-the-u-sql-and-spark-language-and-processing-paradigms"></a>U-SQL 및 Spark 언어 및 처리 패러다임 이해

Azure Data Lake Analytics ' U-SQL 스크립트를 Spark로 마이그레이션하기 전에 두 시스템의 일반적인 언어 및 처리 방법을 이해 하는 것이 유용 합니다.

U-SQL은 데이터 흐름 패러다임을 사용 하며 .NET으로 작성 된 사용자 코드 (예: C#Python 및 R)를 쉽게 포함 하 고 확장할 수 있도록 하는 sql과 유사한 선언적 쿼리 언어입니다. 사용자 확장은 단순 식이나 사용자 정의 함수를 구현할 수 있지만 사용자가 행 집합 수준 변환을 수행 하기 위해 사용자 지정 연산자를 구현 하는 사용자 정의 연산자를 구현 하는 기능을 제공할 수도 있습니다. 추출 및 출력을 쓰는 중입니다.

Spark는 Scala, Java, Python, .NET 등의 여러 언어 바인딩을 제공 하는 스케일 아웃 프레임 워크입니다. 이러한 언어 중 하나로 주로 코드를 작성 하는 경우 RDD (복원 력 있는 분산 데이터), 데이터 프레임 및 데이터 집합 이라는 데이터 추상화를 만듭니다. 그런 다음 LINQ와 유사한 DSL (도메인별 언어)을 사용 하 여 변환 합니다. 또한 데이터 프레임 및 데이터 집합 추상화에 선언적 하위 SparkSQL 제공 합니다. DSL은 변환 및 동작 이라는 두 가지 범주의 작업을 제공 합니다. 데이터 추상화에 변환을 적용 해도 변환이 실행 되지 않고, 작업 (예: 임시 테이블 또는 파일에 결과를 기록 하는 등)을 사용 하 여 평가를 위해 제출 되는 실행 계획을 작성 합니다. 결과).

따라서 U-SQL 스크립트를 Spark 프로그램으로 변환 하는 경우 최소한 데이터 프레임 추상화 (현재 가장 자주 사용 되는 데이터 추상화)를 생성 하는 데 사용할 언어를 결정 하 고 선언적으로 작성 하려는 언어를 결정 해야 합니다. DSL 또는 SparkSQL를 사용 하는 데이터 흐름 변환 일부 복잡 한 경우에는 U-SQL 스크립트를 Azure Batch 또는 Azure Functions를 사용 하 여 구현 된 일련의 Spark 및 기타 단계로 분할 해야 할 수 있습니다.

또한 Azure Data Lake Analytics은 서버를 사용 하지 않는 작업 서비스 환경에서 T-SQL을 제공 하지만 Azure Databricks와 Azure HDInsight는 모두 클러스터 서비스의 형태로 Spark를 제공 합니다. 응용 프로그램을 변환할 때 이제 클러스터 만들기, 크기 조정, 크기 조정 및 서비스 해제의 영향을 고려해 야 합니다.

## <a name="transform-u-sql-scripts"></a>Transform U-SQL 스크립트

U-SQL 스크립트는 다음 처리 패턴을 따릅니다.

1. 데이터는 `EXTRACT` 문, 위치 또는 파일 집합 사양을 사용 하 여 구조화 되지 않은 파일에서, 기본 제공 되거나 사용자 정의 된 추출기와 원하는 스키마를 사용 하 여 또는 U-SQL 테이블 (관리 또는 외부 테이블)에서 읽습니다. 행 집합으로 표시 됩니다.
2. 행 집합은 행 집합에 U-SQL 식을 적용 하 고 새 행 집합을 생성 하는 여러 개의 U-SQL 문으로 변환 됩니다.
3. 마지막으로 결과 행 집합은 위치 및 기본 제공 또는 사용자 정의 outputter를 지정 하는 `OUTPUT` 문을 사용 하는 두 파일 또는 U SQL 테이블로 출력 됩니다.

스크립트는 지연 계산 됩니다. 즉, 각 추출 및 변환 단계가 식 트리로 구성 되 고 전역적으로 계산 됩니다 (데이터 흐름).

Spark 프로그램은 Spark 커넥터를 사용 하 여 데이터를 읽고 데이터 프레임을 만든 다음, LINQ와 유사한 DSL 또는 SparkSQL를 사용 하 여 데이터 프레임에 변환을 적용 하 고, 결과를 파일, 임시 Spark 테이블에 기록 하는 것과 비슷합니다. 일부 프로그래밍 언어 형식 또는 콘솔

## <a name="transform-net-code"></a>.NET 코드 변환

U-SQL의 식 언어는 C# 이며 사용자 지정 .net 코드를 확장 하는 다양 한 방법을 제공 합니다.

Spark는 현재 .NET 코드 실행을 기본적으로 지원 하지 않으므로 식을 해당 Spark, Scala, Java 또는 Python 식으로 다시 작성 하거나 .NET 코드를 호출 하는 방법을 찾아야 합니다. 스크립트에서 .NET 라이브러리를 사용 하는 경우 다음과 같은 옵션을 사용할 수 있습니다.

- .NET 코드를 Scala 또는 Python으로 변환 합니다.
- Azure Batch 프로세스를 사용 하 여 .NET 변환을 적용 하는 여러 단계로 U SQL 스크립트 분할 (적합 한 규모를 얻을 수 있는 경우)
- Moebius 라는 오픈 소스에서 사용할 수 있는 .NET 언어 바인딩을 사용 합니다. 이 프로젝트는 지원 되는 상태가 아닙니다.

어떤 경우 든, U-SQL 스크립트에 많은 양의 .NET 논리가 있는 경우 Microsoft 계정 담당자에 게 문의 하 여 추가 지침을 확인 하세요.

다음은 T-SQL 스크립트에서 사용 되는 .NET 및 C# 사용에 대한 자세한 내용입니다.

### <a name="transform-scalar-inline-u-sql-c-expressions"></a>스칼라 인라인 U-SQL C# 식 변환

U-SQL의 식 언어는 C#입니다. .NET framework를 호출 하 여 더 복잡 한 식을 실행할 수 있는 반면, 많은 스칼라 인라인 U-SQL 식은 기본적으로 성능 향상을 위해 구현 됩니다.

Spark에는 DSL 또는 SparkSQL의 일부로 고유한 스칼라 식 언어가 있으므로 호스팅 언어로 작성 된 사용자 정의 함수를 호출할 수 있습니다.

U-SQL에 스칼라 식이 있는 경우 먼저 가장 적합 한 고유 하 게 인식 되는 Spark 스칼라 식을 찾은 후 가장 많은 성능을 확보 하 고 다른 식을 선택한 Spark 호스팅 언어의 사용자 정의 함수에 매핑합니다.

.NET 및 C# 에는 spark 호스팅 언어와 SPARK의 DSL과는 다른 형식 의미 체계가 있습니다. 형식 시스템 차이점에 대한 자세한 내용은 [아래](#transform-typed-values) 를 참조 하세요.

### <a name="transform-user-defined-scalar-net-functions-and-user-defined-aggregators"></a>사용자 정의 스칼라 .NET 함수 및 사용자 정의 집계 변환

U-SQL은 임의의 스칼라 .NET 함수를 호출 하 고 .NET으로 작성 된 사용자 정의 집계를 호출 하는 방법을 제공 합니다.

또한 spark는 Spark의 DSL 및 SparkSQL에서 호출할 수 있는 대부분의 호스팅 언어로 작성 된 사용자 정의 함수 및 사용자 정의 집계에 대한 지원도 제공 합니다.

### <a name="transform-user-defined-operators-udos"></a>UDOs (사용자 정의 연산자) 변환

추출기, 출력기, 리 듀 서, Processor, 적용 자 및 결합 자와 같은 다양 한 범주의 사용자 정의 연산자 (UDOs)를 제공 하는 U-SQL은 .NET으로 작성 될 수 있습니다.

Spark는 연산자에 대해 동일한 확장성 모델을 제공 하지 않지만 일부에 대해서는 동일한 기능을 제공 합니다.

추출기 및 출력기에 해당 하는 Spark는 Spark 커넥터입니다. 많은 U-SQL 추출기 Spark 커뮤니티에서 해당 하는 커넥터를 찾을 수 있습니다. 다른 사용자는 사용자 지정 커넥터를 작성 해야 합니다. U-SQL 추출기가 복잡 하 고 여러 .NET 라이브러리를 사용 하는 경우 interop를 사용 하 여 데이터의 실제 처리를 수행 하는 .NET 라이브러리를 호출 하는 Scala에서 커넥터를 빌드하는 것이 더 적합할 수 있습니다. 이 경우에는 .NET Core 런타임을 Spark 클러스터에 배포 하 고 참조 된 .NET 라이브러리가 2.0 규격 .NET Standard 있는지 확인 해야 합니다.

다른 유형의 U-SQL UDOs는 사용자 정의 함수 및 집계와 의미상 적절 한 Spark DL 또는 SparkSQL 식을 사용 하 여 다시 작성 해야 합니다. 예를 들어 데이터 프레임를 인수로 사용 하 고 데이터 프레임을 반환 하는 함수로 패키지 된 다양 한 UDF 호출의 SELECT에 프로세서를 매핑할 수 있습니다.

### <a name="transform-u-sqls-optional-libraries"></a>Transform U-SQL의 선택적 라이브러리

U-SQL은 [Python](data-lake-analytics-u-sql-python-extensions.md), [R](data-lake-analytics-u-sql-r-extensions.md), [JSON, XML, AVRO 지원](https://github.com/Azure/usql/tree/master/Examples/DataFormats)및 일부 [인식 서비스 기능](data-lake-analytics-u-sql-cognitive.md)을 제공 하는 선택적 및 데모 라이브러리 집합을 제공 합니다.

Spark는 각각 자체 Python 및 R 통합, pySpark 및 SparkR를 제공 하 고, JSON, XML 및 AVRO를 읽고 쓰기 위한 커넥터를 제공 합니다.

인식 서비스 라이브러리를 참조 하는 스크립트를 변환 해야 하는 경우 Microsoft 계정 담당자에 게 문의 하는 것이 좋습니다.

## <a name="transform-typed-values"></a>형식화 된 값 변환

U-SQL의 유형 시스템은 .NET 유형 시스템을 기반으로 하며 Spark에는 호스트 언어 바인딩의 영향을 받는 자체 형식 시스템이 있으므로, 작동 하는 형식이 특정 형식 인지, 특정 형식 인지 확인 해야 합니다. , 전체 자릿수 및/또는 소수 자릿수는 약간 다를 수 있습니다. 또한, U-SQL 및 Spark는 `null` 값을 다르게 처리 합니다.

### <a name="data-types"></a>데이터 형식

다음 표에서는 지정 된 Scala 및 PySpark에서 지정 된 U SQL 형식에 해당 하는 형식에 대해 설명 합니다.

| U-SQL | Spark |  스칼라 | PySpark |
| ------ | ------ | ------ | ------ |
|`byte`       ||||
|`sbyte`      |`ByteType` |`Byte` | `ByteType`|
|`int`        |`IntegerType` |`Int` | `IntegerType`|
|`uint`       ||||
|`long`       |`LongType` |`Long` | `LongType`|
|`ulong`      ||||
|`float`      |`FloatType` |`Float` | `FloatType`|
|`double`     |`DoubleType` |`Double` | `DoubleType`|
|`decimal`    |`DecimalType` |`java.math.BigDecimal` | `DecimalType`|
|`short`      |`ShortType` |`Short` | `ShortType`|
|`ushort`     ||||
|`char`   | |`Char`||
|`string` |`StringType` |`String` |`StringType` |
|`DateTime`   |`DateType`, `TimestampType` |`java.sql.Date`, `java.sql.Timestamp` | `DateType`, `TimestampType`|
|`bool`   |`BooleanType` |`Boolean` | `BooleanType`|
|`Guid`   ||||
|`byte[]` |`BinaryType` |`Array[Byte]` | `BinaryType`|
|`SQL.MAP<K,V>`   |`MapType(keyType, valueType, valueContainsNull)` |`scala.collection.Map` | `MapType(keyType, valueType, valueContainsNull=True)`|
|`SQL.ARRAY<T>`   |`ArrayType(elementType, containsNull)` |`scala.collection.Seq` | `ArrayType(elementType, containsNull=True)`|

자세한 내용은

- [org .sql. types](https://spark.apache.org/docs/latest/api/scala/index.html#org.apache.spark.sql.types.package)
- [Spark SQL 및 데이터 프레임 형식](https://spark.apache.org/docs/latest/sql-reference.html#data-types)
- [Scala 값 형식](https://www.scala-lang.org/api/current/scala/AnyVal.html)
- [pyspark](https://spark.apache.org/docs/latest/api/python/pyspark.sql.html#module-pyspark.sql.types)

### <a name="treatment-of-null"></a>NULL 처리

Spark에서는 U n-SQL에서 NULL 값을 허용 하는 형식입니다. Spark를 사용 하면 열을 null을 허용 하지 않는 것으로 정의할 수 있지만 제약 조건을 적용 하지 않으며 [결과가 잘못 될 수 있습니다](https://medium.com/@weshoffman/apache-spark-parquet-and-troublesome-nulls-28712b06f836).

Spark에서 NULL은 값을 알 수 없음을 나타냅니다. Spark NULL 값은 자체를 포함 하 여 모든 값과 다릅니다. 두 Spark NULL 값을 비교 하거나 NULL 값과 다른 값을 비교 하 여 각 NULL 값을 알 수 없기 때문에 unknown을 반환 합니다.  

이 동작은 `null` 값이 다른 값과 다른 의미 체계 C# 를 따르는 U-SQL과는 다릅니다.  

따라서 `WHERE column_name = NULL`를 사용 하는 SparkSQL `SELECT` 문은 `column_name`에 NULL 값이 있어도 0 행을 반환 하지만, U n-SQL에서는 `column_name`가 `null`로 설정 된 행을 반환 합니다. 마찬가지로 `WHERE column_name != NULL`를 사용 하는 Spark `SELECT` 문은 `column_name`에 null이 아닌 값이 있어도 0 행을 반환 하지만, U n-SQL에서는 null이 아닌 행을 반환 합니다. 따라서 U-SQL null 검사 의미 체계가 필요한 경우에는 각각 [isnull](https://spark.apache.org/docs/2.3.0/api/sql/index.html#isnull) 및 [isnotnull](https://spark.apache.org/docs/2.3.0/api/sql/index.html#isnotnull) 을 사용 해야 합니다 (또는 그에 상응 하는 DSL).

## <a name="transform-u-sql-catalog-objects"></a>Transform U SQL catalog 개체

한 가지 중요 한 차이점은 U-SQL 스크립트가 해당 카탈로그 개체를 사용할 수 있다는 것입니다 .이 중 상당수에는 직접 Spark가 없는 것입니다.

Spark는 Hive 메타 저장소 개념, 주로 데이터베이스 및 테이블에 대한 지원을 제공 하므로, U-SQL 데이터베이스 및 스키마를 Hive 데이터베이스에 매핑할 수 있으며, U-sql 테이블을 Spark 테이블에 매핑할 수 있습니다 ( [u sql 테이블에 저장 된 데이터 이동](understand-spark-data-formats.md#move-data-stored-in-u-sql-tables)참조). 그러나 뷰를 지원 하지 않습니다. Tvf (테이블 반환 함수), 저장 프로시저, U-SQL 어셈블리, 외부 데이터 원본 등

보기, Tvf, 저장 프로시저, 어셈블리 등의 U-SQL 코드 개체는 Spark의 코드 함수 및 라이브러리를 통해 모델링 되 고 호스트 언어의 함수 및 절차 추상화 메커니즘을 사용 하 여 참조 될 수 있습니다 (예: 가져오기를 통해). Python 모듈 또는 Scala 함수 참조).

T-SQL 카탈로그가 프로젝트와 팀에서 데이터 및 코드 개체를 공유 하는 데 사용 되는 경우 공유에 대한 동등한 메커니즘 (예: 코드를 공유 하기 위한 Maven)을 사용 해야 합니다.

## <a name="transform-u-sql-rowset-expressions-and-sql-based-scalar-expressions"></a>Transform U SQL 행 집합 식 및 SQL 기반 스칼라 식

U-SQL의 핵심 언어가 행 집합을 변환 하 고 있으며 SQL을 기반으로 합니다. 다음은 U-SQL에서 제공 되는 가장 일반적인 행 집합 식의 완전 하지 않은 목록입니다.

- `SELECT`/`FROM`/`WHERE`/`GROUP BY`+ 집계 +`HAVING`/`ORDER BY`+`FETCH`
- `INNER`/`OUTER`/`CROSS`/`SEMI` `JOIN` 식
- `CROSS`/`OUTER` `APPLY` 식
- `PIVOT`/`UNPIVOT` 식
- `VALUES` 행 집합 생성자

- 식 설정 `UNION`/`OUTER UNION`/`INTERSECT`/`EXCEPT`

또한 U-SQL은와 같은 다양 한 SQL 기반 스칼라 식을 제공 합니다.

- `OVER` 창 고 식
- 다양 한 기본 제공 집계 및 순위 함수 (`SUM`, `FIRST` 등)
- 가장 익숙한 SQL 스칼라 식 중 일부는 `CASE`, `LIKE`, (`NOT`) `IN`, `AND`, `OR` 등입니다.

Spark는 대부분의 이러한 식에 대해 DSL 및 SparkSQL 형식에 동일한 식을 제공 합니다. Spark에서 기본적으로 지원 되지 않는 일부 식은 기본 Spark 식과 의미상 동등한 패턴의 조합을 사용 하 여 다시 작성 해야 합니다. 예를 들어 `OUTER UNION`은 프로젝션 및 공용 구조체의 동일한 조합으로 변환 되어야 합니다.

NULL 값을 다르게 처리 하기 때문에 두 열에 모두 NULL 값이 포함 된 경우에는 두 열에 모두 NULL 값이 포함 되는 반면, Spark의 조인은 명시적 null 검사를 추가 하지 않으면 이러한 열과 일치 하지 않습니다.

## <a name="transform-other-u-sql-concepts"></a>다른 U-SQL 개념 변환

또한 U-SQL은 SQL Server 데이터베이스, 매개 변수, 스칼라 및 람다 식 변수, 시스템 변수, `OPTION` 힌트에 대한 페더레이션된 쿼리와 같은 다양 한 다른 기능 및 개념을 제공 합니다.

### <a name="federated-queries-against-sql-server-databasesexternal-tables"></a>SQL Server 데이터베이스/외부 테이블에 대한 페더레이션된 쿼리

U-SQL은 데이터 원본 및 외부 테이블 뿐만 아니라 Azure SQL Database에 대한 직접 쿼리를 제공 합니다. Spark는 동일한 개체 추상화를 제공 하지 않지만 SQL 데이터베이스를 쿼리 하는 데 사용할 수 있는 [Azure SQL Database에 대한 spark 커넥터](../sql-database/sql-database-spark-connector.md) 를 제공 합니다.

### <a name="u-sql-parameters-and-variables"></a>U-SQL 매개 변수 및 변수

매개 변수 및 사용자 변수의 개념은 Spark 및 해당 호스팅 언어와 동일 합니다.

예를 들어 Scala에서 다음과 같이 `var` 키워드를 사용 하 여 변수를 정의할 수 있습니다.

```
var x = 2 * 3;
println(x)
```

U-SQL의 시스템 변수 (`@@`로 시작 하는 변수)는 다음과 같은 두 가지 범주로 나눌 수 있습니다.

- 스크립트 동작에 영향을 주는 특정 값으로 설정할 수 있는 설정 가능한 시스템 변수
- 시스템 및 작업 수준 정보를 조회 하는 정보 시스템 변수

설정 가능한 시스템 변수의 대부분은 Spark에 직접 대응 되지 않습니다. 일부 정보 시스템 변수는 작업 실행 중에 인수로 정보를 전달 하 여 모델링할 수 있으며, 다른 사용자는 Spark의 호스팅 언어에서 동일한 기능을 사용할 수 있습니다.

### <a name="u-sql-hints"></a>U-SQL 힌트

U-SQL은 쿼리 최적화 프로그램 및 실행 엔진에 힌트를 제공 하는 여러 가지 구문을 제공 합니다.  

- U-SQL 시스템 변수 설정
- 데이터 또는 계획 힌트를 제공 하기 위해 행 집합 식과 연결 된 `OPTION` 절입니다.
- 조인 식의 구문에 있는 조인 힌트 (예: `BROADCASTLEFT`)

Spark의 비용 기반 쿼리 최적화 프로그램은 힌트를 제공 하 고 쿼리 성능을 조정 하는 고유한 기능을 제공 합니다. 해당 설명서를 참조 하세요.

## <a name="next-steps"></a>다음 단계

- [U SQL 개발자를 위한 Spark 데이터 형식 이해](understand-spark-data-formats.md)
- [Apache Spark 용 .NET](https://docs.microsoft.com/dotnet/spark/what-is-apache-spark-dotnet)
- [Azure Data Lake Storage Gen1에서 Azure Data Lake Storage Gen2로 빅 데이터 분석 솔루션 업그레이드](../storage/blobs/data-lake-storage-upgrade.md)
- [Azure Data Factory에서 Spark 작업을 사용 하 여 데이터 변환](../data-factory/transform-data-using-spark.md)
- [Azure Data Factory Hadoop Hive 작업을 사용 하 여 데이터 변환](../data-factory/transform-data-using-hadoop-hive.md)
- [Azure HDInsight의 Apache Spark 정의](../hdinsight/spark/apache-spark-overview.md)
