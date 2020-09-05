---
title: 관리 ID를 사용하여 스토리지 계정에 대한 연결 설정(미리 보기)
titleSuffix: Azure Cognitive Search
description: 관리 ID를 사용하여 Azure Storage 계정에 인덱서 연결을 설정하는 방법 알아보기(미리 보기)
manager: luisca
author: markheff
ms.author: maheff
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 05/18/2020
ms.openlocfilehash: 30305607ea291587f7751b7e8048924f995251e9
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88917978"
---
# <a name="set-up-a-connection-to-an-azure-storage-account-using-a-managed-identity-preview"></a>관리 ID를 사용하여 Azure Storage 계정에 대한 연결 설정(미리 보기)

> [!IMPORTANT] 
> 관리 id를 사용 하 여 데이터 원본에 대 한 연결을 설정 하는 기능은 현재 공개 미리 보기로 제공 됩니다. 미리 보기 기능은 서비스 수준 계약 없이 제공되며, 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다.

이 페이지에서는 데이터 원본 개체 연결 문자열에 자격 증명을 제공하는 대신 관리 ID를 사용하여 Azure Storage 계정에 인덱서 연결을 설정하는 방법을 설명합니다.

이 기능에 대해 자세히 알아보기 전에 인덱서가 무엇이며 데이터 원본에 대해 인덱서를 설정하는 방법을 이해하는 것이 좋습니다. 자세한 내용은 다음 링크에서 확인할 수 있습니다.
* [인덱서 개요](search-indexer-overview.md)
* [Azure Blob 인덱서](search-howto-indexing-azure-blob-storage.md)
* [Azure Data Lake Storage Gen2 인덱서](search-howto-index-azure-data-lake-storage.md)
* [Azure 테이블 인덱서](search-howto-indexing-azure-tables.md)

## <a name="set-up-the-connection"></a>연결 설정

### <a name="1---turn-on-system-assigned-managed-identity"></a>1 - 시스템 할당 관리 ID 켜기

시스템 할당 관리 ID 사용이 설정되면 Azure는 동일한 테넌트 및 구독 내에서 다른 Azure 서비스에 인증하는 데 사용할 수 있는 검색 서비스 ID를 만듭니다. 그런 다음 인덱싱 중에 데이터 액세스를 허용하는 RBAC(역할 기반 액세스 제어) 할당에서 이 ID를 사용할 수 있습니다.

![시스템 할당 관리 ID 켜기](./media/search-managed-identities/turn-on-system-assigned-identity.png "시스템 할당 관리 ID 켜기")

**저장**을 선택한 후 검색 서비스에 할당된 개체 ID가 표시됩니다.

![개체 ID](./media/search-managed-identities/system-assigned-identity-object-id.png "개체 ID입니다.")
 
### <a name="2---add-a-role-assignment"></a>2 - 역할 할당 추가

이 단계에서는 Azure Cognitive Search 서비스에 스토리지 계정에서 데이터를 읽을 수 있는 권한을 부여합니다.

1. Azure Portal에서 인덱싱하려는 데이터가 포함된 스토리지 계정으로 이동합니다.
2. **액세스 제어(IAM)** 를 선택합니다.
3. **추가**를 선택한 다음 **역할 할당 추가**를 선택합니다.

    ![역할 할당 추가](./media/search-managed-identities/add-role-assignment-storage.png "역할 할당 추가")

4. 인덱싱할 스토리지 계정 유형에 따라 적절한 역할을 선택합니다.
    1. Azure Blob storage를 사용 하려면 **저장소 Blob 데이터 판독기** 역할에 검색 서비스를 추가 해야 합니다.
    1. Azure Data Lake Storage Gen2 하려면 **저장소 Blob 데이터 판독기** 역할에 검색 서비스를 추가 해야 합니다.
    1. Azure 테이블 저장소를 사용 하려면 검색 서비스를 **판독기 및 데이터 액세스** 역할에 추가 해야 합니다.
5.  **액세스 할당**을 **Azure AD 사용자, 그룹 또는 서비스 보안 주체**로 둡니다.
6.  검색 서비스를 검색하고 선택한 다음 **저장**을 선택합니다.

    Azure Blob storage 및 Azure Data Lake Storage Gen2에 대 한 예제:

    ![스토리지 Blob 데이터 읽기 권한자 역할 할당 추가](./media/search-managed-identities/add-role-assignment-storage-blob-data-reader.png "스토리지 Blob 데이터 읽기 권한자 역할 할당 추가")

    Azure 테이블 저장소에 대 한 예제:

    ![읽기 및 데이터 액세스 역할 할당 추가](./media/search-managed-identities/add-role-assignment-reader-and-data-access.png "읽기 및 데이터 액세스 역할 할당 추가")

### <a name="3---create-the-data-source"></a>3 - 데이터 원본 만들기

[REST API](/rest/api/searchservice/create-data-source), Azure Portal 및 [.net SDK](/dotnet/api/microsoft.azure.search.models.datasource?view=azure-dotnet) 는 관리 되는 id 연결 문자열을 지원 합니다. 다음은 [REST API](/rest/api/searchservice/create-data-source) 및 관리 되는 id 연결 문자열을 사용 하 여 저장소 계정에서 데이터를 인덱싱하는 데이터 원본을 만드는 방법의 예입니다. 관리 되는 id 연결 문자열 형식은 REST API, .NET SDK 및 Azure Portal에 대해 동일 합니다.

스토리지 계정에서 인덱싱할 때 데이터 원본에는 다음과 같은 필수 속성이 있어야 합니다.

* **name**은 검색 서비스 내 데이터 원본의 고유 이름입니다.
* **type**
    * Azure Blob Storage: `azureblob`
    * Azure Table Storage: `azuretable`
    * Azure Data Lake Storage Gen2: [이 양식](https://aka.ms/azure-cognitive-search/mi-preview-request)을 사용하여 미리 보기에 등록하면 **type**이 제공됩니다.
* **credentials**
    * 관리 ID를 사용하여 인증하는 경우 **자격 증명** 형식은 관리 ID를 사용하지 않는 경우와 다릅니다. 여기서는 계정 키 또는 암호가 없는 ResourceId를 제공합니다. ResourceId는 스토리지 계정의 구독 ID, 스토리지 계정의 리소스 그룹 및 스토리지 계정 이름이 포함되어야 합니다.
    * 관리 ID 형식: 
        * *ResourceId=/subscriptions/**구독 ID**/resourceGroups/**구독 이름**/providers/Microsoft.Storage/storageAccounts/**스토리지 계정 이름**/;*
* **container**는 스토리지 계정에서 컨테이너 또는 테이블 이름을 지정합니다. 기본적으로 컨테이너 내의 모든 BLOB은 검색 가능합니다. 특정 가상 디렉터리의 BLOB만 인덱싱하려면 선택 사항인 **query** 매개 변수를 사용하여 해당 디렉터리를 지정할 수 있습니다,

다음은 [REST API](/rest/api/searchservice/create-data-source)를 사용하여 Blob 데이터 원본 개체를 만드는 방법의 예입니다.

```
POST https://[service name].search.windows.net/datasources?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
    "name" : "blob-datasource",
    "type" : "azureblob",
    "credentials" : { "connectionString" : "ResourceId=/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Storage/storageAccounts/storage-account-name/;" },
    "container" : { "name" : "my-container", "query" : "<optional-virtual-directory-name>" }
}   
```

### <a name="4---create-the-index"></a>4 - 인덱스 만들기

인덱스는 문서의 필드, 특성 및 검색 경험을 형성하는 기타 항목을 지정합니다.

다음은 검색 가능한`content` 필드가 있는 인덱스를 만들어 blob에서 추출된 텍스트를 저장하는 방법입니다.   

```http
    POST https://[service name].search.windows.net/indexes?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
          "name" : "my-target-index",
          "fields": [
            { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "content", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
          ]
    }
```

인덱스 만들기에 자세한 내용은 [인덱스 만들기](/rest/api/searchservice/create-index)를 참조하세요.

### <a name="5---create-the-indexer"></a>5 - 인덱서 만들기

인덱서는 데이터 원본을 대상 검색 인덱스와 연결하고 데이터 새로 고침을 자동화하는 일정을 제공합니다.

인덱스와 데이터 원본이 만들어지면 인덱서를 만들 준비가 된 것입니다.

Blob 인덱서에 대한 인덱서 정의 예:

```http
    POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "blob-indexer",
      "dataSourceName" : "blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" }
    }
```

이 인덱서는 2시간 간격으로 실행됩니다(일정 간격이 "PT2H"로 설정됨). 인덱서를 30분 간격으로 실행하려면 간격을 "PT30M"으로 설정합니다. 지원되는 가장 짧은 간격은 5분입니다. 일정은 선택 사항입니다. 생략하는 경우 인덱서는 만들어질 때 한 번만 실행됩니다. 그러나 언제든지 필요할 때 인덱서를 실행할 수 있습니다.   

인덱서 만들기 API에 대한 자세한 내용은 [인덱서 만들기](/rest/api/searchservice/create-indexer)를 확인하세요.

인덱서 일정을 정의하는 방법에 대한 자세한 내용은 [Azure Cognitive Search에 대한 인덱서 일정 지정 방법](search-howto-schedule-indexers.md)을 참조하세요.

## <a name="see-also"></a>참고 항목

Azure Storage 인덱서에 대한 자세한 정보:
* [Azure Blob 인덱서](search-howto-indexing-azure-blob-storage.md)
* [Azure Data Lake Storage Gen2 인덱서](search-howto-index-azure-data-lake-storage.md)
* [Azure 테이블 인덱서](search-howto-indexing-azure-tables.md)