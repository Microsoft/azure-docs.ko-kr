---
title: 인덱서를 사용하여 Azure SQL Database 콘텐츠 연결 및 인덱싱 - Azure Search
description: Azure Search에서 전체 텍스트 검색을 위해 인덱서를 사용하여 Azure SQL Database의 데이터를 크롤링하는 방법을 알아봅니다. 이 문서에서는 연결, 인덱서 구성 및 데이터 수집에 대해 설명합니다.
ms.date: 03/01/2019
author: mgottein
manager: cgronlun
ms.author: magottei
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: 5453bcdd371c0639cb1d3568f05a1768e6204d3d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60817157"
---
# <a name="connect-to-and-index-azure-sql-database-content-using-azure-search-indexers"></a>Azure Search 인덱서를 사용하여 Azure SQL Database 콘텐츠에 연결 및 인덱싱

[Azure Search 인덱스](search-what-is-an-index.md)를 쿼리하기 전에 데이터를 채워야 합니다. 데이터가 Azure SQL 데이터베이스에 있는 경우 **Azure SQL Database용 Azure Search 인덱서**(또는 줄여서 **Azure SQL 인덱서**)를 사용하여 인덱싱 프로세스를 자동화할 수 있으므로 작성할 코드의 양과 신경 써야 할 인프라가 줄어듭니다.

이 문서에서는 [인덱서](search-indexer-overview.md) 사용 원리를 다루지만 Azure SQL 데이터베이스에서만 사용할 수 있는 기능(예: 통합 변경 내용 추적)에 대해서도 설명합니다. 

Azure SQL 데이터베이스 외에도 Azure Search는 [Azure Cosmos DB](search-howto-index-cosmosdb.md), [Azure Blob Storage](search-howto-indexing-azure-blob-storage.md) 및 [Azure Table Storage](search-howto-indexing-azure-tables.md)에 대한 인덱서를 제공합니다. 다른 데이터 원본에 대한 지원을 요청하려면 [Azure Search 피드백 포럼](https://feedback.azure.com/forums/263029-azure-search/)에 의견을 남겨 주시기 바랍니다.

## <a name="indexers-and-data-sources"></a>인덱서 및 데이터 원본

**데이터 원본**은 인덱싱할 데이터, 데이터 액세스를 위한 자격 증명 및 데이터 변경 내용(예: 수정되거나 삭제된 행)을 효율적으로 식별할 수 있도록 해주는 정책을 지정합니다. 이는 독립된 리소스로 정의되므로 여러 인덱서에서 사용할 수 있습니다.

**인덱서**는 단일 데이터 원본을 대상 검색 인덱스에 연결하는 리소스입니다. 인덱서는 다음과 같은 방법으로 사용됩니다.

* 인덱스를 채우기 위해 데이터에 대한 일회성 복사를 수행합니다.
* 예약에 따라 데이터 원본의 변경 내용으로 인덱스를 업데이트합니다.
* 필요에 따라 요청 시 인덱스 업데이트를 실행합니다.

단일 인덱서는 테이블 또는 뷰를 하나만 사용할 수 있지만 여러 검색 인덱스를 채우려는 경우 여러 인덱서를 만들 수 있습니다. 개념에 대한 자세한 내용은 [인덱서 작업: 일반적인 워크플로](https://docs.microsoft.com/rest/api/searchservice/Indexer-operations#typical-workflow)를 참조하세요.

다음을 사용하여 Azure SQL 인덱서를 설정하고 구성할 수 있습니다.

* [Azure Portal](https://portal.azure.com)의 데이터 가져오기 마법사
* Azure Search [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer?view=azure-dotnet)
* Azure Search [REST API](https://docs.microsoft.com/rest/api/searchservice/indexer-operations)

이 문서에서는 REST API를 사용하여 **인덱서** 및 **데이터 원본**을 만듭니다.

## <a name="when-to-use-azure-sql-indexer"></a>Azure SQL 인덱서를 사용하는 경우
데이터와 관련된 여러 요소에 따라 Azure SQL 인덱서를 사용하는 것이 적절할 수도 있고 그렇지 않을 수도 있습니다. 데이터가 다음 요구 사항에 적합한 경우 Azure SQL 인덱서를 사용할 수 있습니다.

| 조건 | 세부 정보 |
|----------|---------|
| 단일 테이블 또는 뷰에서 발생한 데이터 | 데이터가 여러 테이블에 분산된 경우 데이터에 대한 단일 뷰를 만들 수 있습니다. 그러나 뷰를 사용하는 경우 증분 변경 내용으로 인덱스를 새로 고치는 데 SQL Server 통합 변경 검색을 사용할 수 없습니다. 자세한 내용은 아래의 [변경 및 삭제된 행 캡처](#CaptureChangedRows)를 참조하세요. |
| 호환되는 데이터 형식 | Azure Search 인덱스에서는 전부는 아니지만 대부분의 SQL 형식이 지원됩니다. 목록은 [데이터 형식 매핑](#TypeMapping)을 참조하세요. |
| 실시간 데이터 동기화가 필요하지 않습니다. | 인덱서는 최대 5분마다 테이블을 다시 인덱싱할 수 있습니다. 데이터가 자주 변경되고 변경 내용을 몇 초 또는 몇 분 이내에 인덱스에 반영해야 하는 경우에는 [REST API](https://docs.microsoft.com/rest/api/searchservice/AddUpdate-or-Delete-Documents) 또는 [.NET SDK](search-import-data-dotnet.md)를 사용하여 업데이트된 행을 직접 푸시하는 것이 좋습니다. |
| 증분 인덱싱 가능 | 데이터 집합이 크고 일정에 따라 인덱서를 실행하려는 경우 Azure Search에서 새 행, 변경된 행 및 삭제된 행을 효율적으로 식별할 수 있어야 합니다. 비-증분 인덱싱은 주문 시(일정을 따르지 않고) 인덱싱하거나 100,000 미만의 행을 인덱싱하는 경우에만 허용됩니다. 자세한 내용은 아래의 [변경 및 삭제된 행 캡처](#CaptureChangedRows)를 참조하세요. |

> [!NOTE] 
> Azure Search는 SQL Server 인증만 지원합니다. Azure Active Directory 암호 인증에 대한 지원이 필요한 경우 이 [UserVoice 제안](https://feedback.azure.com/forums/263029-azure-search/suggestions/33595465-support-azure-active-directory-password-authentica)에 투표하세요.

## <a name="create-an-azure-sql-indexer"></a>Azure SQL 인덱서 만들기

1. 데이터 원본을 만듭니다.

   ```
    POST https://myservice.search.windows.net/datasources?api-version=2017-11-11
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

2. 대상 Azure Search 인덱스가 없는 경우 새로 만듭니다. [포털](https://portal.azure.com) 또는 [인덱스 만들기 API](https://docs.microsoft.com/rest/api/searchservice/Create-Index)를 사용하여 인덱스를 만들 수 있습니다. 대상 인덱스의 스키마가 원본 테이블의 스키마와 호환되는지 확인합니다. [SQL 데이터 형식과 Azure Search 데이터 형식 사이의 매핑](#TypeMapping)을 참조하세요.

3. 이름을 지정하고 데이터 원본 및 대상 인덱스를 참조하여 인덱서는 만듭니다.

    ```
    POST https://myservice.search.windows.net/indexers?api-version=2017-11-11
    Content-Type: application/json
    api-key: admin-key

    {
        "name" : "myindexer",
        "dataSourceName" : "myazuresqldatasource",
        "targetIndexName" : "target index name"
    }
    ```

이 방법으로 만든 인덱서에는 일정이 없습니다. 만들어지면 자동으로 한 번 실행됩니다. 언제든지 **run indexer** 요청을 사용하여 다시 실행할 수 있습니다.

    POST https://myservice.search.windows.net/indexers/myindexer/run?api-version=2017-11-11
    api-key: admin-key

인덱서 동작의 몇 가지 측면(예: 배치 크기, 인덱서 실행에 실패하기 전에 건너뛸 수 있는 문서 수)을 사용자 지정할 수 있습니다. 자세한 내용은 [인덱서 API 만들기](https://docs.microsoft.com/rest/api/searchservice/Create-Indexer)를 참조하세요.

Azure 서비스에서 데이터베이스에 연결하도록 허용해야 할 수 있습니다. 이 작업을 수행하는 방법에 대한 지침은 [Azure에서 연결](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) 을 참조하세요.

인덱서 상태 및 실행 기록(인덱싱된 항목 수, 오류 등)을 모니터링하려면 **indexer status** 요청을 사용합니다.

    GET https://myservice.search.windows.net/indexers/myindexer/status?api-version=2017-11-11
    api-key: admin-key

응답은 다음과 같아야 합니다.

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

실행 기록은 50개의 최근에 완료한 실행까지 포함할 수 있으며, 시간 순서의 반대로 정렬됩니다(최신 항목이 응답에서 먼저 표시됨)
응답에 대한 추가 정보는 [인덱서 상태 가져오기](https://go.microsoft.com/fwlink/p/?LinkId=528198)에서 확인할 수 있습니다.

## <a name="run-indexers-on-a-schedule"></a>일정에 따라 인덱서 실행
일정에 따라 주기적으로 실행되도록 인덱서를 정렬할 수도 있습니다. 이렇게 하려면 인덱서를 만들거나 업데이트할 때 **schedule** 속성을 추가합니다. 아래 예제에서는 인덱서를 업데이트하는 PUT 요청을 보여 줍니다.

    PUT https://myservice.search.windows.net/indexers/myindexer?api-version=2017-11-11
    Content-Type: application/json
    api-key: admin-key

    {
        "dataSourceName" : "myazuresqldatasource",
        "targetIndexName" : "target index name",
        "schedule" : { "interval" : "PT10M", "startTime" : "2015-01-01T00:00:00Z" }
    }

**interval** 매개 변수는 필수 사항입니다. 두 개의 연속된 인덱서 실행 간의 시작 시간 간격을 나타냅니다. 허용되는 가장 작은 간격은 5분이고 가장 긴 간격은 1일입니다. 형식은 XSD "dayTimeDuration" 값( [ISO 8601 기간](https://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) 값의 제한된 하위 집합)이어야 합니다. 해당 패턴은 `P(nD)(T(nH)(nM))`입니다. 예를 들어 15분 간격이면 `PT15M`, 2시간 간격이면 `PT2H`입니다.

선택적 **startTime**은 예약된 실행을 시작해야 하는 시점을 나타냅니다. 생략한 경우 현재 UTC 시간이 사용됩니다. 이 시간은 과거의 시간일 수 있습니다. 이 경우 첫 번째 실행은 인덱서가 startTime 이후에 지속적으로 실행된 것처럼 예약됩니다.  

인덱서의 실행은 한 번에 하나만 실행할 수 있습니다. 인덱서가 실행 중일 때 실행이 예약된 경우 다음 예약 시간까지 실행이 연기됩니다.

좀 더 구체적인 예제를 살펴보겠습니다. 다음 시간별 일정을 구성했다고 가정해 보겠습니다.

    "schedule" : { "interval" : "PT1H", "startTime" : "2015-03-01T00:00:00Z" }

다음과 같은 상황이 발생합니다.

1. 첫 번째 인덱서 실행은 2015년 3월 1일 오전 12시경(UTC)에 시작됩니다.
2. 이 실행에 20분(또는 1시간 미만의 기간)이 걸리는 것으로 가정합니다.
3. 두 번째 인덱서 실행은 2015년 3월 1일 오전 1시경에 시작됩니다.
4. 이제 이 실행이 오전 2시 10분경에 완료되도록 1시간이 넘게(예: 70분) 걸린다고 가정해 보겠습니다.
5. 세 번째 실행의 시작 시간은 오전 2시입니다. 그러나 오전 1시에 시작된 두 번째 실행이 여전히 실행 중이므로 세 번째 실행을 건너뜁니다. 세 번째 실행은 오전 3시에 시작됩니다.

**PUT indexer** 요청을 사용하여 기존 인덱서에 대한 일정을 추가, 변경 또는 삭제할 수 있습니다.

<a name="CaptureChangedRows"></a>

## <a name="capture-new-changed-and-deleted-rows"></a>새 행, 변경된 행 및 삭제된 행 캡처

Azure Search는 **증분 인덱싱**을 사용하여 전체 테이블을 다시 인덱싱하거나 인덱서가 실행될 때마다 표시될 필요가 없도록 합니다. Azure Search는 증분 인덱싱을 지원하는 두 가지 변경 검색 정책을 제공합니다. 

### <a name="sql-integrated-change-tracking-policy"></a>SQL 통합 변경 내용 추적 정책
SQL 데이터베이스에서 [변경 내용 추적](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-tracking-sql-server)을 지원하는 경우 **SQL 통합 변경 내용 추적 정책**을 사용하는 것이 좋습니다. 가장 효율적인 정책입니다. 또한 테이블에 "soft delete" 열을 명시적으로 추가하지 않고도 Azure Search에서 삭제된 행을 식별할 수 있습니다.

#### <a name="requirements"></a>요구 사항 

+ 데이터베이스 버전 요구 사항:
  * SQL Server 2012 SP3 이상(Azure VM에서 SQL Server를 사용하는 경우)
  * Azure SQL Database V12(Azure SQL Database를 사용하는 경우)
+ 테이블만(뷰 제외). 
+ 데이터베이스에서 테이블에 대해 [변경 내용 추적을 설정](https://docs.microsoft.com/sql/relational-databases/track-changes/enable-and-disable-change-tracking-sql-server)합니다. 
+ 테이블에서 복합 기본 키(두 개 이상의 열을 포함하는 기본 키)가 없습니다.  

#### <a name="usage"></a>사용 현황

이 정책을 사용하려면 다음과 같이 데이터 원본을 만들거나 업데이트합니다.

    {
        "name" : "myazuresqldatasource",
        "type" : "azuresql",
        "credentials" : { "connectionString" : "connection string" },
        "container" : { "name" : "table or view name" },
        "dataChangeDetectionPolicy" : {
           "@odata.type" : "#Microsoft.Azure.Search.SqlIntegratedChangeTrackingPolicy"
      }
    }

SQL 통합 변경 내용 추적 정책을 사용할 때는 별도의 데이터 삭제 검색 정책을 지정하지 마세요. 이 정책은 삭제된 행 식별을 기본적으로 지원합니다. 그러나 삭제가 "자동"으로 탐지되려면 검색 인덱스의 문서 키가 SQL 테이블의 기본 키와 동일해야 합니다. 

> [!NOTE]  
> SQL 테이블에서 많은 수의 행을 제거하기 위해 [TRUNCATE TABLE](https://docs.microsoft.com/sql/t-sql/statements/truncate-table-transact-sql)을 사용할 때 인덱서를 [다시 설정](https://docs.microsoft.com/rest/api/searchservice/reset-indexer)하여 행 삭제를 선택하도록 변경 추적 상태를 다시 설정해야 합니다.

<a name="HighWaterMarkPolicy"></a>

### <a name="high-water-mark-change-detection-policy"></a>상위 워터마크 변경 검색 정책

이 변경 검색 정책은 행이 마지막으로 업데이트된 버전 또는 시간을 캡처하는 “상위 워터 마크” 열을 사용합니다. 뷰를 사용하는 경우 상위 워터 마크 정책을 사용해야 합니다. 상위 워터 마크 열은 다음 요구 사항을 충족해야 합니다.

#### <a name="requirements"></a>요구 사항 

* 모든 삽입 시 열의 값을 지정합니다.
* 항목에 대한 모든 업데이트는 열의 값도 변경합니다.
* 삽입 또는 업데이트할 때마다 이 열의 값이 증가합니다.
* 다음 WHERE 및 ORDER BY 절이 포함된 쿼리를 효율적으로 실행할 수 있습니다. `WHERE [High Water Mark Column] > [Current High Water Mark Value] ORDER BY [High Water Mark Column]`

> [!IMPORTANT] 
> 상위 워터 마크 열에는 [rowversion](https://docs.microsoft.com/sql/t-sql/data-types/rowversion-transact-sql) 데이터 형식을 사용하는 것이 좋습니다. 다른 데이터 형식을 사용하는 경우 변경 추적이 인덱서 쿼리와 동시에 실행되는 트랜잭션의 모든 변경 내용을 캡처하지는 않습니다. 읽기 전용 복제본이 있는 구성에 **rowversion**을 사용하는 경우 주 복제본에서 인덱서를 가리켜야 합니다. 데이터 동기화 시나리오에는 주 복제본만 사용할 수 있습니다.

#### <a name="usage"></a>사용 현황

높은 워터 마크 정책을 사용하려면 다음과 같이 데이터 원본을 만들거나 업데이트합니다.

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

> [!WARNING]
> 원본 테이블의 상위 워터 마크 열에 인덱스가 없는 경우 SQL 인덱서에서 사용하는 쿼리가 시간 초과될 수 있습니다. 특히 테이블에 많은 행이 포함된 경우 `ORDER BY [High Water Mark Column]` 절에서 인덱스를 효율적으로 실행해야 합니다.
>
>

시간 초과 오류를 발생하는 경우 `queryTimeout` 인덱서 구성 설정을 사용하여 쿼리 제한 시간을 기본값(5분)보다 높은 값으로 설정할 수 있습니다. 예를 들어 제한 시간을 10분으로 설정하려면 다음 구성을 사용하여 인덱서를 만들거나 업데이트합니다.

    {
      ... other indexer definition properties
     "parameters" : {
            "configuration" : { "queryTimeout" : "00:10:00" } }
    }

`ORDER BY [High Water Mark Column]` 절을 사용하지 않도록 설정할 수도 있습니다. 그러나 인덱서 실행이 오류로 인해 중단된 경우 그때까지 거의 모든 행을 처리했더라도 나중에 실행될 때 모든 행을 다시 처리해야 하므로 이는 권장되지 않습니다. `ORDER BY` 절을 사용하지 않도록 설정하려면 인덱서 정의에서 `disableOrderByHighWaterMarkColumn` 설정을 사용합니다.  

    {
     ... other indexer definition properties
     "parameters" : {
            "configuration" : { "disableOrderByHighWaterMarkColumn" : true } }
    }

### <a name="soft-delete-column-deletion-detection-policy"></a>Soft Delete 열 삭제 검색 정책
원본 테이블에서 행이 삭제된 경우 검색 인덱스에서도 해당 행을 삭제할 수 있습니다. SQL 통합 변경 내용 추적 정책을 사용하는 경우 이 작업이 자동으로 수행됩니다. 그러나 상위 워터 마크 변경 내용 추적 정책은 삭제된 행을 지원하지 않습니다. 그렇다면 어떻게 해야 할까요?

행이 테이블에서 실제로 제거된 경우 Azure Search에서 더 이상 존재하지 않는 레코드의 현재 상태를 유추할 방법이 없습니다.  그러나 "일시 삭제" 기술을 사용하여 테이블에서 제거하지 않고 논리적으로 행을 삭제할 수 있습니다. 테이블 또는 뷰에 열을 추가하고 이 열을 사용하여 행을 삭제된 것으로 표시합니다.

일시 삭제 기술을 사용하는 경우 데이터 원본을 만들거나 업데이트할 때 다음과 같이 일시 삭제 정책을 지정할 수 있습니다.

    {
        …,
        "dataDeletionDetectionPolicy" : {
           "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
           "softDeleteColumnName" : "[a column name]",
           "softDeleteMarkerValue" : "[the value that indicates that a row is deleted]"
        }
    }

**softDeleteMarkerValue**는 문자열이어야 합니다. 실제 값의 문자열 표현을 사용하세요. 예를 들어 삭제된 행이 값 1로 표시된 정수 열이 있는 경우 `"1"`을 사용합니다. 삭제된 행이 부울 true 값으로 표시된 BIT 열이 있는 경우 문자열 리터럴 `True` 또는 `true`를 사용합니다. 대소문자는 상관 없습니다.

<a name="TypeMapping"></a>

## <a name="mapping-between-sql-and-azure-search-data-types"></a>SQL과 Azure Search 데이터 형식 사이의 매핑
| SQL 데이터 형식 | 허용되는 대상 인덱스 필드 유형 | 메모 |
| --- | --- | --- |
| bit |Edm.Boolean, Edm.String | |
| int, smallint, tinyint |Edm.Int32, Edm.Int64, Edm.String | |
| bigint |Edm.Int64, Edm.Int64, Edm.String | |
| real, float |Edm.Double, Edm.String | |
| smallmoney, money decimal numeric |Edm.String |Azure Search에서는 decimal 형식을 Edm.Double로 변환하면 정밀도가 떨어지기 때문에 이를 지원하지 않습니다. |
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

    {
      ... other indexer definition properties
     "parameters" : {
            "configuration" : { "queryTimeout" : "00:10:00" } }
    }

## <a name="faq"></a>FAQ

**Q: Azure의 IaaS VM에서 실행되는 SQL Database에서 Azure SQL 인덱서를 사용할 수 있나요?**

예. 그러나 검색 서비스에서 데이터베이스에 연결할 수 있도록 허용해야 합니다. 자세한 내용은 [Azure VM에서 Azure Search 인덱서로부터 SQL Server로의 연결 구성](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md) 문서를 참조하세요.

**Q: 온-프레미스에서 실행되는 SQL Database에서 Azure SQL 인덱서를 사용할 수 있나요?**

직접 끌 수는 없습니다. 직접 연결은 권장되거나 지원되지 않습니다. 이렇게 하려면 데이터베이스를 인터넷 트래픽에 개방해야 하기 때문입니다. 고객은 Azure Data Factory와 같은 브리지 기술을 사용하여 이 시나리오를 성공적으로 수행했습니다. 자세한 내용은 [Azure Data Factory를 사용하여 Azure Search 인덱스에 데이터 푸시](https://docs.microsoft.com/azure/data-factory/data-factory-azure-search-connector)를 참조하세요.

**Q: Azure의 IaaS에서 실행되는 SQL Server가 아닌 데이터베이스에서 Azure SQL 인덱서를 사용할 수 있나요?**

아니요. SQL Server가 아닌 데이터베이스에서는 인덱서를 테스트하지 않았기 때문에 이 시나리오는 지원되지 않습니다.  

**Q: 일정에 따라 실행되는 여러 인덱서를 만들 수 있나요?**

예. 그러나 한 번에 하나의 인덱서만 실행할 수 있습니다. 여러 인덱서를 동시에 실행하려면 둘 이상의 검색 단위로 검색 서비스를 확장하는 것이 좋습니다.

**Q: 인덱서를 실행하면 쿼리 작업이 영향을 받나요?**

예. 인덱서는 검색 서비스의 노드 중 하나에서 실행되므로 해당 노드의 리소스가 인덱싱 및 쿼리 지원 트래픽과 다른 API 요청 간에 공유됩니다. 많은 인덱싱 및 쿼리 작업을 실행하는 경우 503 오류가 자주 발생하거나 응답 시간이 증가하면 [검색 서비스를 확장](search-capacity-planning.md)하는 것이 좋습니다.

**Q: [장애 조치(failover) 클러스터](https://docs.microsoft.com/azure/sql-database/sql-database-geo-replication-overview)에서 데이터 원본으로 보조 복제본을 사용할 수 있나요?**

경우에 따라 다릅니다. 테이블 또는 뷰의 전체 인덱싱에 대해 보조 복제본을 사용할 수 있습니다. 

증분 인덱싱의 경우 Azure Search는 SQL 통합 변경 내용 추적 및 상위 워터마크의 두 가지 변경 검색 정책을 지원합니다.

읽기 전용 복제본에서 SQL 데이터베이스는 통합된 변경 내용 추적을 지원하지 않습니다. 따라서 상위 워터 마크 정책을 사용해야 합니다. 

상위 워터 마크 열에는 rowversion 데이터 형식을 사용하는 것이 일반적으로 권장됩니다. 그러나 rowversion 사용 시 SQL Database의 `MIN_ACTIVE_ROWVERSION` 함수를 사용하는 데, 이는 읽기 전용 복제본에서는 지원되지 않습니다. 따라서 rowversion를 사용하는 경우 인덱서가 주 복제본을 가리키도록 해야 합니다.

읽기 전용 복제본에서 rowversion을 사용하려고 하면 다음 오류가 표시됩니다. 

    "Using a rowversion column for change tracking is not supported on secondary (read-only) availability replicas. Please update the datasource and specify a connection to the primary availability replica.Current database 'Updateability' property is 'READ_ONLY'".

**Q: 상위 워터마크 변경 내용 추적에 대해 rowversion이 아닌 대체 열을 사용할 수 있나요?**

권장되지 않습니다. 신뢰할 수 있는 데이터 동기화를 위해서는 **rowversion**만 허용됩니다. 그러나 애플리케이션 논리에 따라 다음과 같은 경우 안전할 수 있습니다.

+ 인덱서가 실행될 때 인덱싱되는 테이블에 미해결 트랜잭션이 있는지 확인할 수 있습니다(예를 들어 모든 테이블 업데이트가 일정에 따라 일괄 처리되고 Azure Search 인덱서 일정이 테이블 업데이트 일정과 겹치지 않도록 설정됨).  

+ 모든 누락된 행을 선택하기 위해 전체 다시 인덱싱을 정기적으로 수행합니다. 