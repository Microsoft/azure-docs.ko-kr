---
title: 관리 ID(미리 보기)를 사용하여 Cosmos DB 계정 연결 설정
titleSuffix: Azure Cognitive Search
description: 관리 ID(미리 보기)를 사용하여 Cosmos DB 계정에 인덱서 연결을 설정하는 방법 알아보기
manager: luisca
author: markheff
ms.author: maheff
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 05/18/2020
ms.openlocfilehash: df37b7f1c5b1ed35b6c3779eea470b2fb0936ecf
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88936659"
---
# <a name="set-up-an-indexer-connection-to-a-cosmos-db-database-using-a-managed-identity-preview"></a>관리 ID(미리 보기)를 사용하여 Cosmos DB 데이터베이스에 인덱서 연결 설정

> [!IMPORTANT] 
> 관리 id를 사용 하 여 데이터 원본에 대 한 연결을 설정 하는 기능은 현재 공개 미리 보기로 제공 됩니다. 미리 보기 기능은 서비스 수준 계약 없이 제공되며, 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다.

이 페이지에서는 데이터 원본 개체 연결 문자열에 자격 증명을 제공하는 대신 관리 ID를 사용하여 Azure Cosmos DB 데이터베이스에 인덱서 연결을 설정하는 방법을 설명 합니다.

이 기능을 학습하기 전에 인덱서가 무엇인지와 데이터 원본에 대해 인덱서를 설정하는 방법을 이해하는 것이 좋습니다. 자세한 내용은 다음 링크에서 확인할 수 있습니다.
* [인덱서 개요](search-indexer-overview.md)
* [Azure Cosmos DB 인덱서](search-howto-index-cosmosdb.md)

## <a name="set-up-a-connection-using-a-managed-identity"></a>관리 ID를 사용하여 연결 설정

### <a name="1---turn-on-system-assigned-managed-identity"></a>1 - 시스템 할당 관리 ID 켜기

시스템 할당 관리 ID 사용이 설정되면 Azure는 동일한 테넌트 및 구독 내에서 다른 Azure 서비스에 인증하는 데 사용할 수 있는 검색 서비스 ID를 만듭니다. 그런 다음 인덱싱 중에 데이터 액세스를 허용하는 RBAC(역할 기반 액세스 제어) 할당에서 이 ID를 사용할 수 있습니다.

![시스템 할당 관리 ID 켜기](./media/search-managed-identities/turn-on-system-assigned-identity.png "시스템 할당 관리 ID 켜기")

**저장**을 선택한 후 검색 서비스에 할당된 개체 ID가 표시됩니다.

![개체 ID](./media/search-managed-identities/system-assigned-identity-object-id.png "개체 ID입니다.")
 
### <a name="2---add-a-role-assignment"></a>2 - 역할 할당 추가

이 단계에서는 Cosmos DB 데이터베이스에서 데이터를 읽을 수 있도록 Azure Cognitive Search 서비스에 권한을 부여합니다.

1. Azure Portal에서 인덱싱할 데이터가 포함된 Cosmos DB 계정으로 이동합니다.
2. **액세스 제어(IAM)** 를 선택합니다.
3. **추가**를 선택한 다음 **역할 할당 추가**를 선택합니다.

    ![역할 할당 추가](./media/search-managed-identities/add-role-assignment-cosmos-db.png "역할 할당 추가")

4. **Cosmos DB 계정 독자 역할**을 선택합니다.
5. **액세스 할당**을 **Azure AD 사용자, 그룹 또는 서비스 보안 주체**로 둡니다.
6. 검색 서비스를 검색하고 선택한 다음 **저장**을 선택합니다.

    ![읽기 및 데이터 액세스 역할 할당 추가](./media/search-managed-identities/add-role-assignment-cosmos-db-account-reader-role.png "읽기 및 데이터 액세스 역할 할당 추가")

### <a name="3---create-the-data-source"></a>3 - 데이터 원본 만들기

[REST API](/rest/api/searchservice/create-data-source), Azure Portal 및 [.net SDK](/dotnet/api/microsoft.azure.search.models.datasource?view=azure-dotnet) 는 관리 되는 id 연결 문자열을 지원 합니다. 다음은 [REST API](/rest/api/searchservice/create-data-source) 및 관리 되는 id 연결 문자열을 사용 하 여 Cosmos DB에서 데이터를 인덱싱하는 데이터 원본을 만드는 방법에 대 한 예입니다. 관리 되는 id 연결 문자열 형식은 REST API, .NET SDK 및 Azure Portal에 대해 동일 합니다.

관리 id를 사용 하 여 인증 하는 경우 **자격 증명** 은 계정 키를 포함 하지 않습니다.

```
POST https://[service name].search.windows.net/datasources?api-version=2020-06-30
Content-Type: application/json
api-key: [Search service admin key]

{
    "name": "cosmos-db-datasource",
    "type": "cosmosdb",
    "credentials": {
        "connectionString": "Database=sql-test-db;ResourceId=/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/cosmos-db-resource-group/providers/Microsoft.DocumentDB/databaseAccounts/my-cosmos-db-account/;"
    },
    "container": { "name": "myCollection", "query": null },
    "dataChangeDetectionPolicy": {
        "@odata.type": "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
        "highWaterMarkColumnName": "_ts"
    }
}
```    

요청 본문에는 다음 필드를 포함해야 하는 데이터 소스 정의가 포함됩니다.

| 필드   | Description |
|---------|-------------|
| **name** | 필수 사항입니다. 데이터 원본 개체를 나타낼 이름을 선택합니다. |
|**type**| 필수 사항입니다. `cosmosdb`이어야 합니다. |
|**credentials** | 필수 사항입니다. <br/><br/>관리 ID를 사용하여 연결하는 경우 **자격 증명** 형식은 다음과 같아야 합니다. *Database=[database-name];ResourceId=[resource-id-string];(ApiKind=[api-kind];)*<br/> <br/>ResourceId 형식: *ResourceId=/subscriptions/**your subscription ID**/resourceGroups/**your resource group name**/providers/Microsoft.DocumentDB/databaseAccounts/**your cosmos db account name**/;*<br/><br/>SQL 컬렉션의 경우 연결 문자열에 ApiKind가 필요하지 않습니다.<br/><br/>MongoDB 컬렉션의 경우 **ApiKind=MongoDb**를 연결 문자열에 추가합니다. <br/><br/>Gremlin 그래프 및 Cassandra 테이블의 경우 [제어된 인덱서 미리 보기](https://aka.ms/azure-cognitive-search/indexer-preview)에 등록하여 미리 보기 액세스와 자격 증명 형식 지정 방법에 관한 정보를 얻습니다.<br/>|
| **container** | 다음과 같은 요소가 있습니다. <br/>**name**: 필수 사항입니다. 인덱싱할 데이터베이스 컬렉션의 ID를 지정합니다.<br/>**query**: (선택 사항) 추상 JSON 문서를 Azure Cognitive Search가 인덱싱할 수 있는 평면 스키마로 평면화하는 쿼리를 지정할 수 있습니다.<br/>MongoDB API, Gremlin API 및 Cassandra API의 경우 쿼리는 지원되지 않습니다. |
| **dataChangeDetectionPolicy** | 권장 |
|**dataDeletionDetectionPolicy** | 옵션 |

### <a name="4---create-the-index"></a>4 - 인덱스 만들기

인덱스는 문서의 필드, 특성 및 검색 경험을 형성하는 기타 항목을 지정합니다.

검색 가능한 `booktitle` 필드를 사용하여 인덱스를 만드는 방법은 다음과 같습니다.

```
POST https://[service name].search.windows.net/indexes?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
    "name" : "my-target-index",
    "fields": [
    { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
    { "name": "booktitle", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
    ]
}
```

인덱스 만들기에 자세한 내용은 [인덱스 만들기](/rest/api/searchservice/create-index)를 참조하세요.

### <a name="5---create-the-indexer"></a>5 - 인덱서 만들기

인덱서는 데이터 원본을 대상 검색 인덱스와 연결하고 데이터 새로 고침을 자동화하는 일정을 제공합니다.

인덱스와 데이터 원본이 만들어지면 인덱서를 만들 준비가 된 것입니다.

예제 인덱서 정의:

```http
    POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "cosmos-db-indexer",
      "dataSourceName" : "cosmos-db-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" }
    }
```

이 인덱서는 2시간 간격으로 실행됩니다(일정 간격이 "PT2H"로 설정됨). 인덱서를 30분 간격으로 실행하려면 간격을 "PT30M"으로 설정합니다. 지원되는 가장 짧은 간격은 5분입니다. 일정은 선택 사항입니다. 생략하는 경우 인덱서는 만들어질 때 한 번만 실행됩니다. 그러나 언제든지 필요할 때 인덱서를 실행할 수 있습니다.   

인덱서 만들기 API에 대한 자세한 내용은 [인덱서 만들기](/rest/api/searchservice/create-indexer)를 확인하세요.

인덱서 일정을 정의하는 방법에 대한 자세한 내용은 [Azure Cognitive Search에 대한 인덱서 일정 지정 방법](search-howto-schedule-indexers.md)을 참조하세요.

## <a name="see-also"></a>참고 항목

Cosmos DB 인덱서에 대해 자세히 알아보기:
* [Azure Cosmos DB 인덱서](search-howto-index-cosmosdb.md)