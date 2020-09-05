---
title: Azure SQL 데이터 검색
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search에서 전체 텍스트 검색을 위해 인덱서를 사용 하 여 Azure SQL Database 또는 SQL Managed Instance에서 데이터를 가져옵니다. 이 문서에서는 연결, 인덱서 구성 및 데이터 수집에 대해 설명합니다.
manager: nitinme
author: mgottein
ms.author: magottei
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 07/12/2020
ms.openlocfilehash: a1dd88e9007a878ffdf6e5d836391c30c952c35a
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88923027"
---
# <a name="connect-to-and-index-azure-sql-content-using-an-azure-cognitive-search-indexer"></a>Azure Cognitive Search 인덱서를 사용 하 여 Azure SQL 콘텐츠 연결 및 인덱싱

[Azure Cognitive Search 인덱스](search-what-is-an-index.md)를 쿼리하려면 먼저 데이터를 채워야 합니다. 데이터가 Azure SQL Database 또는 SQL Managed Instance에 있는 경우 **Azure SQL Database에 대 한 azure Cognitive Search 인덱서** (또는 Short 용 **azure SQL 인덱서** )는 인덱싱 프로세스를 자동화할 수 있습니다. 즉, 작성 하는 코드는 적고 관심 있는 인프라를 줄일 수 있습니다.

이 문서에서는 [인덱서](search-indexer-overview.md)를 사용 하는 방법에 대해 설명 하지만 AZURE SQL DATABASE 또는 SQL Managed Instance 에서만 사용할 수 있는 기능 (예: 통합 변경 내용 추적)에 대해서도 설명 합니다. 

Azure Cognitive Search는 Azure SQL Database 및 SQL Managed Instance 외에도 [Azure Cosmos DB](search-howto-index-cosmosdb.md), [azure Blob storage](search-howto-indexing-azure-blob-storage.md)및 [azure table storage](search-howto-indexing-azure-tables.md)에 대 한 인덱서를 제공 합니다. 다른 데이터 원본에 대 한 지원을 요청 하려면 [Azure Cognitive Search 피드백 포럼](https://feedback.azure.com/forums/263029-azure-search/)에 피드백을 제공 하세요.

## <a name="indexers-and-data-sources"></a>인덱서 및 데이터 원본

**데이터 원본**은 인덱싱할 데이터, 데이터 액세스를 위한 자격 증명 및 데이터 변경 내용(예: 수정되거나 삭제된 행)을 효율적으로 식별할 수 있도록 해주는 정책을 지정합니다. 이는 독립된 리소스로 정의되므로 여러 인덱서에서 사용할 수 있습니다.

**인덱서**는 단일 데이터 원본을 대상 검색 인덱스에 연결하는 리소스입니다. 인덱서는 다음과 같은 방법으로 사용됩니다.

* 인덱스를 채우기 위해 데이터에 대한 일회성 복사를 수행합니다.
* 예약에 따라 데이터 원본의 변경 내용으로 인덱스를 업데이트합니다.
* 필요에 따라 요청 시 인덱스 업데이트를 실행합니다.

단일 인덱서는 테이블 또는 뷰를 하나만 사용할 수 있지만 여러 검색 인덱스를 채우려는 경우 여러 인덱서를 만들 수 있습니다. 개념에 대한 자세한 내용은 [인덱서 작업: 일반적인 워크플로](/rest/api/searchservice/Indexer-operations#typical-workflow)를 참조하세요.

다음을 사용하여 Azure SQL 인덱서를 설정하고 구성할 수 있습니다.

* [Azure Portal](https://portal.azure.com)의 데이터 가져오기 마법사
* Azure Cognitive Search [.NET SDK](/dotnet/api/microsoft.azure.search.models.indexer?view=azure-dotnet)
* Azure Cognitive Search [REST API](/rest/api/searchservice/indexer-operations)

이 문서에서는 REST API를 사용하여 **인덱서** 및 **데이터 원본**을 만듭니다.

## <a name="when-to-use-azure-sql-indexer"></a>Azure SQL 인덱서를 사용하는 경우
데이터와 관련된 여러 요소에 따라 Azure SQL 인덱서를 사용하는 것이 적절할 수도 있고 그렇지 않을 수도 있습니다. 데이터가 다음 요구 사항에 적합한 경우 Azure SQL 인덱서를 사용할 수 있습니다.

| 조건 | 세부 정보 |
|----------|---------|
| 단일 테이블 또는 뷰에서 발생한 데이터 | 데이터가 여러 테이블에 분산된 경우 데이터에 대한 단일 뷰를 만들 수 있습니다. 그러나 뷰를 사용하는 경우 증분 변경 내용으로 인덱스를 새로 고치는 데 SQL Server 통합 변경 검색을 사용할 수 없습니다. 자세한 내용은 아래의 [변경 및 삭제된 행 캡처](#CaptureChangedRows)를 참조하세요. |
| 호환되는 데이터 형식 | 모든 SQL 유형은 Azure Cognitive Search 인덱스에서 지원 됩니다. 목록은 [데이터 형식 매핑](#TypeMapping)을 참조하세요. |
| 실시간 데이터 동기화가 필요하지 않습니다. | 인덱서는 최대 5분마다 테이블을 다시 인덱싱할 수 있습니다. 데이터가 자주 변경되고 변경 내용을 몇 초 또는 몇 분 이내에 인덱스에 반영해야 하는 경우에는 [REST API](/rest/api/searchservice/AddUpdate-or-Delete-Documents) 또는 [.NET SDK](./search-get-started-dotnet.md)를 사용하여 업데이트된 행을 직접 푸시하는 것이 좋습니다. |
| 증분 인덱싱 가능 | 대량 데이터 집합이 있고 일정에 따라 인덱서를 실행 하려는 경우 Azure Cognitive Search는 새 행, 변경 된 행 또는 삭제 된 행을 효율적으로 식별할 수 있어야 합니다. 비-증분 인덱싱은 주문 시(일정을 따르지 않고) 인덱싱하거나 100,000 미만의 행을 인덱싱하는 경우에만 허용됩니다. 자세한 내용은 아래의 [변경 및 삭제된 행 캡처](#CaptureChangedRows)를 참조하세요. |

> [!NOTE] 
> Azure Cognitive Search는 SQL Server 인증만 지원 합니다. Azure Active Directory 암호 인증에 대한 지원이 필요한 경우 이 [UserVoice 제안](https://feedback.azure.com/forums/263029-azure-search/suggestions/33595465-support-azure-active-directory-password-authentica)에 투표하세요.

## <a name="create-an-azure-sql-indexer"></a>Azure SQL 인덱서 만들기

1. 데이터 원본을 만듭니다.

   ```
    POST https://myservice.search.windows.net/datasources?api-version=2020-06-30
    Content-Type: application/json
    api-key: admin-key

    {
        "name" : "myazuresqldatasource",
        "type" : "azuresql",
        "credentials" : { "connectionString" : "Server=tcp:<your server>.database.windows.net,1433;Database=<your database>;User ID=<your user name>;Password=<your password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;" },
        "container" : { "name" : "name of the table or view that you want to index" }
    }
   ```

   `ADO.NET connection string` 옵션을 사용하여 [Azure Portal](https://portal.azure.com)에서 연결 문자열을 가져올 수 있습니다.

2. 아직 없는 경우 대상 Azure Cognitive Search 인덱스를 만듭니다. [포털](https://portal.azure.com) 또는 [인덱스 만들기 API](/rest/api/searchservice/Create-Index)를 사용하여 인덱스를 만들 수 있습니다. 대상 인덱스의 스키마가 원본 테이블의 스키마와 호환 되는지 확인 합니다. [SQL과 Azure 인식 검색 데이터 형식 간의 매핑](#TypeMapping)을 참조 하세요.

3. 이름을 지정하고 데이터 원본 및 대상 인덱스를 참조하여 인덱서는 만듭니다.

   ```
    POST https://myservice.search.windows.net/indexers?api-version=2020-06-30
    Content-Type: application/json
    api-key: admin-key

    {
        "name" : "myindexer",
        "dataSourceName" : "myazuresqldatasource",
        "targetIndexName" : "target index name"
    }
   ```

이 방법으로 만든 인덱서에는 일정이 없습니다. 만들어지면 자동으로 한 번 실행됩니다. 언제든지 **run indexer** 요청을 사용하여 다시 실행할 수 있습니다.

```
    POST https://myservice.search.windows.net/indexers/myindexer/run?api-version=2020-06-30
    api-key: admin-key
```

인덱서 동작의 몇 가지 측면(예: 배치 크기, 인덱서 실행에 실패하기 전에 건너뛸 수 있는 문서 수)을 사용자 지정할 수 있습니다. 자세한 내용은 [인덱서 API 만들기](/rest/api/searchservice/Create-Indexer)를 참조하세요.

Azure 서비스에서 데이터베이스에 연결하도록 허용해야 할 수 있습니다. 이 작업을 수행하는 방법에 대한 지침은 [Azure에서 연결](../azure-sql/database/firewall-configure.md) 을 참조하세요.

인덱서 상태 및 실행 기록(인덱싱된 항목 수, 오류 등)을 모니터링하려면 **indexer status** 요청을 사용합니다.

```
    GET https://myservice.search.windows.net/indexers/myindexer/status?api-version=2020-06-30
    api-key: admin-key
```

응답은 다음과 같아야 합니다.

```
    {
        "\@odata.context":"https://myservice.search.windows.net/$metadata#Microsoft.Azure.Search.V2015_02_28.IndexerExecutionInfo",
        "status":"running",
        "lastResult": {
            "status":"success",
            "errorMessage":null,
            "startTime":"2015-02-21T00:23:24.957Z",
            "endTime":"2015-02-21T00:36:47.752Z",
            "errors":[],
            "itemsProcessed":1599501,
            "itemsFailed":0,
            "initialTrackingState":null,
            "finalTrackingState":null
        },
        "executionHistory":
        [
            {
                "status":"success",
                "errorMessage":null,
                "startTime":"2015-02-21T00:23:24.957Z",
                "endTime":"2015-02-21T00:36:47.752Z",
                "errors":[],
                "itemsProcessed":1599501,
                "itemsFailed":0,
                "initialTrackingState":null,
                "finalTrackingState":null
            },
            ... earlier history items
        ]
    }
```

실행 기록은 50개의 최근에 완료한 실행까지 포함할 수 있으며, 시간 순서의 반대로 정렬됩니다(최신 항목이 응답에서 먼저 표시됨)
응답에 대한 추가 정보는 [인덱서 상태 가져오기](/rest/api/searchservice/get-indexer-status)에서 확인할 수 있습니다.

## <a name="run-indexers-on-a-schedule"></a>일정에 따라 인덱서 실행
일정에 따라 주기적으로 실행되도록 인덱서를 정렬할 수도 있습니다. 이렇게 하려면 인덱서를 만들거나 업데이트할 때 **schedule** 속성을 추가합니다. 아래 예제에서는 인덱서를 업데이트하는 PUT 요청을 보여 줍니다.

```
    PUT https://myservice.search.windows.net/indexers/myindexer?api-version=2020-06-30
    Content-Type: application/json
    api-key: admin-key

    {
        "dataSourceName" : "myazuresqldatasource",
        "targetIndexName" : "target index name",
        "schedule" : { "interval" : "PT10M", "startTime" : "2015-01-01T00:00:00Z" }
    }
```

**interval** 매개 변수는 필수 사항입니다. 두 개의 연속된 인덱서 실행 간의 시작 시간 간격을 나타냅니다. 허용되는 가장 작은 간격은 5분이고 가장 긴 간격은 1일입니다. 형식은 XSD "dayTimeDuration" 값( [ISO 8601 기간](https://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) 값의 제한된 하위 집합)이어야 합니다. 해당 패턴은 `P(nD)(T(nH)(nM))`입니다. 예를 들어 15분 간격이면 `PT15M`, 2시간 간격이면 `PT2H`입니다.

인덱서 일정을 정의하는 방법에 대한 자세한 내용은 [Azure Cognitive Search에 대한 인덱서 일정 지정 방법](search-howto-schedule-indexers.md)을 참조하세요.

<a name="CaptureChangedRows"></a>

## <a name="capture-new-changed-and-deleted-rows"></a>새 행, 변경된 행 및 삭제된 행 캡처

Azure Cognitive Search는 **증분 인덱싱을** 사용 하 여 인덱서가 실행 될 때마다 전체 테이블 또는 뷰를 인덱싱하지 않아도 됩니다. Azure Cognitive Search는 증분 인덱싱을 지원 하기 위해 두 가지 변경 검색 정책을 제공 합니다. 

### <a name="sql-integrated-change-tracking-policy"></a>SQL 통합 변경 내용 추적 정책
SQL 데이터베이스에서 [변경 내용 추적](/sql/relational-databases/track-changes/about-change-tracking-sql-server)을 지 원하는 경우 **Sql 통합 변경 내용 추적 정책을**사용 하는 것이 좋습니다. 가장 효율적인 정책입니다. 또한 테이블에 "소프트 삭제" 열을 명시적으로 추가 하지 않고도 Azure Cognitive Search에서 삭제 된 행을 식별할 수 있습니다.

#### <a name="requirements"></a>요구 사항 

+ 데이터베이스 버전 요구 사항:
  * SQL Server 2012 SP3 이상(Azure VM에서 SQL Server를 사용하는 경우)
  * Azure SQL Database 또는 SQL Managed Instance.
+ 테이블만(뷰 제외). 
+ 데이터베이스에서 테이블에 대해 [변경 내용 추적을 설정](/sql/relational-databases/track-changes/enable-and-disable-change-tracking-sql-server)합니다. 
+ 테이블에서 복합 기본 키(두 개 이상의 열을 포함하는 기본 키)가 없습니다.  

#### <a name="usage"></a>사용

이 정책을 사용하려면 다음과 같이 데이터 원본을 만들거나 업데이트합니다.

```
    {
        "name" : "myazuresqldatasource",
        "type" : "azuresql",
        "credentials" : { "connectionString" : "connection string" },
        "container" : { "name" : "table or view name" },
        "dataChangeDetectionPolicy" : {
           "@odata.type" : "#Microsoft.Azure.Search.SqlIntegratedChangeTrackingPolicy"
      }
    }
```

SQL 통합 변경 내용 추적 정책을 사용할 때는 별도의 데이터 삭제 검색 정책을 지정하지 마세요. 이 정책은 삭제된 행 식별을 기본적으로 지원합니다. 그러나 삭제가 "자동"으로 탐지되려면 검색 인덱스의 문서 키가 SQL 테이블의 기본 키와 동일해야 합니다. 

> [!NOTE]  
> SQL 테이블에서 많은 수의 행을 제거하기 위해 [TRUNCATE TABLE](/sql/t-sql/statements/truncate-table-transact-sql)을 사용할 때 인덱서를 [다시 설정](/rest/api/searchservice/reset-indexer)하여 행 삭제를 선택하도록 변경 추적 상태를 다시 설정해야 합니다.

<a name="HighWaterMarkPolicy"></a>

### <a name="high-water-mark-change-detection-policy"></a>상위 워터마크 변경 검색 정책

이 변경 검색 정책은 행이 마지막으로 업데이트된 버전 또는 시간을 캡처하는 “상위 워터 마크” 열을 사용합니다. 뷰를 사용하는 경우 상위 워터 마크 정책을 사용해야 합니다. 상위 워터 마크 열은 다음 요구 사항을 충족해야 합니다.

#### <a name="requirements"></a>요구 사항 

* 모든 삽입 시 열의 값을 지정합니다.
* 항목에 대한 모든 업데이트는 열의 값도 변경합니다.
* 삽입 또는 업데이트할 때마다 이 열의 값이 증가합니다.
* 다음 WHERE 및 ORDER BY 절이 포함된 쿼리를 효율적으로 실행할 수 있습니다. `WHERE [High Water Mark Column] > [Current High Water Mark Value] ORDER BY [High Water Mark Column]`

> [!IMPORTANT] 
> 상위 워터 마크 열에는 [rowversion](/sql/t-sql/data-types/rowversion-transact-sql) 데이터 형식을 사용하는 것이 좋습니다. 다른 데이터 형식을 사용하는 경우 변경 추적이 인덱서 쿼리와 동시에 실행되는 트랜잭션의 모든 변경 내용을 캡처하지는 않습니다. 읽기 전용 복제본이 있는 구성에 **rowversion**을 사용하는 경우 주 복제본에서 인덱서를 가리켜야 합니다. 데이터 동기화 시나리오에는 주 복제본만 사용할 수 있습니다.

#### <a name="usage"></a>사용

높은 워터 마크 정책을 사용하려면 다음과 같이 데이터 원본을 만들거나 업데이트합니다.

```
    {
        "name" : "myazuresqldatasource",
        "type" : "azuresql",
        "credentials" : { "connectionString" : "connection string" },
        "container" : { "name" : "table or view name" },
        "dataChangeDetectionPolicy" : {
           "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
           "highWaterMarkColumnName" : "[a rowversion or last_updated column name]"
      }
    }
```

> [!WARNING]
> 원본 테이블의 상위 워터 마크 열에 인덱스가 없는 경우 SQL 인덱서에서 사용 하는 쿼리는 시간 초과 될 수 있습니다. 특히 `ORDER BY [High Water Mark Column]` 테이블에 많은 행이 포함 된 경우 절에서 인덱스를 효율적으로 실행 해야 합니다.
>
>

<a name="convertHighWaterMarkToRowVersion"></a>

##### <a name="converthighwatermarktorowversion"></a>convertHighWaterMarkToRowVersion

상위 워터 마크 열에 [rowversion](/sql/t-sql/data-types/rowversion-transact-sql) 데이터 형식을 사용 하는 경우 인덱서 구성 설정을 사용 하는 것이 좋습니다 `convertHighWaterMarkToRowVersion` . `convertHighWaterMarkToRowVersion`는 다음 두 가지 작업을 수행합니다.

* 인덱서 sql 쿼리의 상위 워터 마크 열에는 rowversion 데이터 형식을 사용 합니다. 올바른 데이터 형식을 사용 하면 인덱서 쿼리 성능이 향상 됩니다.
* 인덱서 쿼리를 실행 하기 전에 rowversion 값에서 1을 뺍니다. 1 개에서 다 수의 조인이 있는 뷰에 중복 된 rowversion 값이 있는 행이 있을 수 있습니다. 1을 빼면 인덱서 쿼리에서 이러한 행을 누락 하지 않도록 합니다.

이 기능을 사용 하도록 설정 하려면 다음 구성을 사용 하 여 인덱서를 만들거나 업데이트 합니다.

```
    {
      ... other indexer definition properties
     "parameters" : {
            "configuration" : { "convertHighWaterMarkToRowVersion" : true } }
    }
```

<a name="queryTimeout"></a>

##### <a name="querytimeout"></a>queryTimeout

시간 초과 오류를 발생하는 경우 `queryTimeout` 인덱서 구성 설정을 사용하여 쿼리 제한 시간을 기본값(5분)보다 높은 값으로 설정할 수 있습니다. 예를 들어 제한 시간을 10분으로 설정하려면 다음 구성을 사용하여 인덱서를 만들거나 업데이트합니다.

```
    {
      ... other indexer definition properties
     "parameters" : {
            "configuration" : { "queryTimeout" : "00:10:00" } }
    }
```

<a name="disableOrderByHighWaterMarkColumn"></a>

##### <a name="disableorderbyhighwatermarkcolumn"></a>disableOrderByHighWaterMarkColumn

`ORDER BY [High Water Mark Column]` 절을 사용하지 않도록 설정할 수도 있습니다. 그러나 인덱서 실행이 오류로 인해 중단된 경우 그때까지 거의 모든 행을 처리했더라도 나중에 실행될 때 모든 행을 다시 처리해야 하므로 이는 권장되지 않습니다. `ORDER BY` 절을 사용하지 않도록 설정하려면 인덱서 정의에서 `disableOrderByHighWaterMarkColumn` 설정을 사용합니다.  

```
    {
     ... other indexer definition properties
     "parameters" : {
            "configuration" : { "disableOrderByHighWaterMarkColumn" : true } }
    }
```

### <a name="soft-delete-column-deletion-detection-policy"></a>Soft Delete 열 삭제 검색 정책
원본 테이블에서 행이 삭제된 경우 검색 인덱스에서도 해당 행을 삭제할 수 있습니다. SQL 통합 변경 내용 추적 정책을 사용하는 경우 이 작업이 자동으로 수행됩니다. 그러나 상위 워터 마크 변경 내용 추적 정책은 삭제된 행을 지원하지 않습니다. 그렇다면 어떻게 해야 할까요?

행이 테이블에서 실제로 제거 된 경우 Azure Cognitive Search는 더 이상 존재 하지 않는 레코드의 존재를 유추할 수 없습니다.  그러나 "일시 삭제" 기술을 사용하여 테이블에서 제거하지 않고 논리적으로 행을 삭제할 수 있습니다. 테이블 또는 뷰에 열을 추가하고 이 열을 사용하여 행을 삭제된 것으로 표시합니다.

일시 삭제 기술을 사용하는 경우 데이터 원본을 만들거나 업데이트할 때 다음과 같이 일시 삭제 정책을 지정할 수 있습니다.

```
    {
        …,
        "dataDeletionDetectionPolicy" : {
           "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
           "softDeleteColumnName" : "[a column name]",
           "softDeleteMarkerValue" : "[the value that indicates that a row is deleted]"
        }
    }
```

**softDeleteMarkerValue**는 문자열이어야 합니다. 실제 값의 문자열 표현을 사용하세요. 예를 들어 삭제된 행이 값 1로 표시된 정수 열이 있는 경우 `"1"`을 사용합니다. 삭제된 행이 부울 true 값으로 표시된 BIT 열이 있는 경우 문자열 리터럴 `True` 또는 `true`를 사용합니다. 대소문자는 상관 없습니다.

<a name="TypeMapping"></a>

## <a name="mapping-between-sql-and-azure-cognitive-search-data-types"></a>SQL과 Azure Cognitive Search 데이터 형식 간 매핑
| SQL 데이터 형식 | 허용되는 대상 인덱스 필드 유형 | 참고 |
| --- | --- | --- |
| bit |Edm.Boolean, Edm.String | |
| int, smallint, tinyint |Edm.Int32, Edm.Int64, Edm.String | |
| bigint |Edm.Int64, Edm.Int64, Edm.String | |
| real, float |Edm.Double, Edm.String | |
| smallmoney, money decimal numeric |Edm.String |Azure Cognitive Search는 소수 형식을 Edm으로 변환 하는 것을 지원 하지 않습니다 .이는 전체 자릿수가 손실 될 수 있기 때문입니다. |
| char, nchar, varchar, nvarchar |Edm.String<br/>Collection(Edm.String) |SQL 문자열은 문자열이 JSON 배열을 나타내는 경우 Collection(Edm.String) 필드를 채우는 데 사용할 수 있습니다. `["red", "white", "blue"]` |
| smalldatetime, datetime, datetime2, date, datetimeoffset |Edm.DateTimeOffset, Edm.String | |
| uniqueidentifer |Edm.String | |
| geography |Edm.GeographyPoint |SRID가 4326(기본값)인 POINT 형식의 지리 인스턴스만 지원됩니다. |
| rowversion |N/A |행 버전 열은 변경 내용 추적에 사용할 수 있지만 검색 인덱스에 저장할 수는 없습니다. |
| time, timespan, binary, varbinary, image, xml, geometry, CLR types |N/A |지원되지 않음 |

## <a name="configuration-settings"></a>구성 설정
SQL 인덱서는 여러 구성 설정을 노출합니다.

| 설정 | 데이터 형식 | 목적 | 기본값 |
| --- | --- | --- | --- |
| queryTimeout |문자열 |SQL 쿼리 실행의 제한 시간 설정 |5분("00:05:00") |
| disableOrderByHighWaterMarkColumn |bool |상위 워터 마크 정책에서 사용하는 SQL 쿼리에서 ORDER BY 절이 생략되도록 합니다. [상위 워터 마크 정책](#HighWaterMarkPolicy)을 참조하세요. |false |

이러한 설정은 인덱서 정의의 `parameters.configuration` 개체에 사용됩니다. 예를 들어 쿼리 제한 시간을 10분으로 설정하려면 다음 구성을 사용하여 인덱서를 만들거나 업데이트합니다.

```
    {
      ... other indexer definition properties
     "parameters" : {
            "configuration" : { "queryTimeout" : "00:10:00" } }
    }
```

## <a name="faq"></a>FAQ

**Q:** Azure의 IaaS VM에서 실행되는 SQL 데이터베이스에서 Azure SQL 인덱서를 사용할 수 있습니까?

예. 그러나 검색 서비스에서 데이터베이스에 연결할 수 있도록 허용해야 합니다. 자세한 내용은 azure VM에서 [azure Cognitive Search 인덱서에 SQL Server로 연결 구성](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md)을 참조 하세요.

**Q:** 온-프레미스에서 실행되는 SQL 데이터베이스에서 Azure SQL 인덱서를 사용할 수 있습니까?

직접 끌 수는 없습니다. 직접 연결은 권장되거나 지원되지 않습니다. 이렇게 하려면 데이터베이스를 인터넷 트래픽에 개방해야 하기 때문입니다. 고객은 Azure Data Factory와 같은 브리지 기술을 사용하여 이 시나리오를 성공적으로 수행했습니다. 자세한 내용은 [Azure Data Factory를 사용 하 여 Azure Cognitive Search 인덱스에 데이터 푸시](../data-factory/v1/data-factory-azure-search-connector.md)를 참조 하세요.

**Q: Azure의 IaaS에서 실행 되는 SQL Server 이외의 데이터베이스에서 Azure SQL 인덱서를 사용할 수 있나요?**

아니요. SQL Server가 아닌 데이터베이스에서는 인덱서를 테스트하지 않았기 때문에 이 시나리오는 지원되지 않습니다.  

**Q: 일정에 따라 실행 되는 여러 인덱서를 만들 수 있나요?**

예. 그러나 한 번에 하나의 인덱서만 실행할 수 있습니다. 여러 인덱서를 동시에 실행하려면 둘 이상의 검색 단위로 검색 서비스를 확장하는 것이 좋습니다.

**Q: 인덱서를 실행 하면 쿼리 워크 로드에 영향을 미칩니까?**

예. 인덱서는 검색 서비스의 노드 중 하나에서 실행되므로 해당 노드의 리소스가 인덱싱 및 쿼리 지원 트래픽과 다른 API 요청 간에 공유됩니다. 많은 인덱싱 및 쿼리 작업을 실행 하 고 503 오류가 발생 하거나 응답 시간이 증가 하는 경우 [검색 서비스를 확장](search-capacity-planning.md)하는 것이 좋습니다.

**Q: [장애 조치(failover) 클러스터](../azure-sql/database/auto-failover-group-overview.md)에서 데이터 원본으로 보조 복제본을 사용할 수 있습니까?**

경우에 따라 다릅니다. 테이블 또는 뷰의 전체 인덱싱에 대해 보조 복제본을 사용할 수 있습니다. 

증분 인덱싱의 경우 Azure Cognitive Search는 SQL 통합 변경 내용 추적 및 상위 워터 마크와 같은 두 가지 변경 검색 정책을 지원 합니다.

읽기 전용 복제본에서 SQL Database는 통합 변경 내용 추적을 지원 하지 않습니다. 따라서 상위 워터 마크 정책을 사용해야 합니다. 

상위 워터 마크 열에는 rowversion 데이터 형식을 사용하는 것이 일반적으로 권장됩니다. 그러나 rowversion를 사용 하는 `MIN_ACTIVE_ROWVERSION` 것은 읽기 전용 복제본에서 지원 되지 않는 함수에 의존 합니다. 따라서 rowversion를 사용하는 경우 인덱서가 주 복제본을 가리키도록 해야 합니다.

읽기 전용 복제본에서 rowversion을 사용하려고 하면 다음 오류가 표시됩니다. 

"보조 (읽기 전용) 가용성 복제본에서는 변경 내용 추적에 rowversion 열을 사용할 수 없습니다. 데이터 원본을 업데이트 하 고 주 가용성 복제본에 대 한 연결을 지정 하십시오. 현재 데이터베이스 ' 업데이트 ' 속성은 ' READ_ONLY ' "입니다.

**Q: 상위 워터 마크 변경 내용 추적에 대체의 rowversion이 아닌 열을 사용할 수 있습니까?**

권장되지 않습니다. 신뢰할 수 있는 데이터 동기화를 위해서는 **rowversion**만 허용됩니다. 그러나 애플리케이션 논리에 따라 다음과 같은 경우 안전할 수 있습니다.

+ 인덱서가 실행 될 때 인덱싱되는 테이블에 처리 중인 트랜잭션이 없는 경우 (예: 모든 테이블 업데이트는 일정에 따라 일괄 처리로 수행 되 고 Azure Cognitive Search 인덱서 일정은 테이블 업데이트 일정과 겹치지 않도록 설정 됨)를 확인할 수 있습니다.  

+ 모든 누락된 행을 선택하기 위해 전체 다시 인덱싱을 정기적으로 수행합니다.