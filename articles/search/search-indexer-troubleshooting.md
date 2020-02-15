---
title: 일반적인 검색 인덱서 문제 해결
titleSuffix: Azure Cognitive Search
description: 데이터 원본 연결, 방화벽 및 누락 된 문서를 포함 하 여 Azure Cognitive Search의 인덱서와 관련 된 오류 및 일반적인 문제를 해결 합니다.
manager: nitinme
author: mgottein
ms.author: magottei
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 1e3692920c35a6965a23c0305aeeebfc80505d85
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77190924"
---
# <a name="troubleshooting-common-indexer-issues-in-azure-cognitive-search"></a>Azure Cognitive Search의 일반적인 인덱서 문제 해결

인덱서는 데이터를 Azure Cognitive Search로 인덱싱할 때 많은 문제가 발생할 수 있습니다. 오류의 주요 범주는 다음과 같습니다.

* [데이터 원본 또는 기타 리소스에 연결](#connection-errors)
* [문서 처리](#document-processing-errors)
* [인덱스에 대한 문서 수집](#index-errors)

## <a name="connection-errors"></a>연결 오류

> [!NOTE]
> 인덱서는 데이터 원본 및 Azure 네트워크 보안 메커니즘을 통해 보안이 유지 되는 기타 리소스에 대 한 액세스가 제한적으로 지원 됩니다. 현재 인덱서는 해당 하는 경우 해당 IP 주소 범위 제한 메커니즘 또는 NSG 규칙을 통해서만 데이터 원본에 액세스할 수 있습니다. 지원 되는 각 데이터 원본에 액세스 하는 방법에 대 한 자세한 내용은 아래를 참조 하십시오.
>
> 정규화 된 도메인 이름 (예: `<your-search-service-name>.search.windows.net`)을 ping 하 여 검색 서비스의 IP 주소를 확인할 수 있습니다.
>
> [다운로드 가능한 JSON 파일](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#discover-service-tags-by-using-downloadable-json-files) 을 사용 하거나 [서비스 태그 검색 API](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#use-the-service-tag-discovery-api-public-preview)를 통해 `AzureCognitiveSearch` [서비스 태그](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#available-service-tags) 의 IP 주소 범위를 확인할 수 있습니다. IP 주소 범위는 매주 업데이트 됩니다.

### <a name="configure-firewall-rules"></a>방화벽 규칙 구성

Azure Storage CosmosDB 및 Azure SQL은 구성 가능한 방화벽을 제공 합니다. 방화벽을 사용하는 경우 특정 오류 메시지가 발생하지 않습니다. 일반적으로 방화벽 오류는 일반적 이며 `The remote server returned an error: (403) Forbidden` 또는 `Credentials provided in the connection string are invalid or have expired`처럼 보입니다.

이러한 인스턴스에서 이러한 리소스에 액세스할 수 있도록 하는 두 가지 옵션이 있습니다.

* **모든 네트워크** 에서 액세스할 수 있도록 허용 하 여 방화벽을 사용 하지 않도록 설정 합니다 (가능한 경우).
* 또는 리소스 (IP 주소 범위 제한)의 방화벽 규칙에서 검색 서비스의 IP 주소와 `AzureCognitiveSearch` [service 태그](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#available-service-tags) 의 ip 주소 범위에 대 한 액세스를 허용할 수 있습니다.

각 데이터 원본 유형에 대 한 IP 주소 범위 제한을 구성 하는 방법에 대 한 자세한 내용은 다음 링크를 참조 하십시오.

* [Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-network-security#grant-access-from-an-internet-ip-range)

* [Cosmos DB](https://docs.microsoft.com/azure/storage/common/storage-network-security#grant-access-from-an-internet-ip-range)

* [Azure SQL](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure#create-and-manage-ip-firewall-rules)

**제한**사항: Azure Storage에 대 한 위의 설명서에서 설명한 대로, 검색 서비스와 저장소 계정이 다른 지역에 있는 경우에만 IP 주소 범위 제한이 적용 됩니다.

[사용자 지정 Web Api 기술로](cognitive-search-custom-skill-web-api.md)사용할 수 있는 Azure 함수는 [IP 주소 제한](https://docs.microsoft.com/azure/azure-functions/ip-addresses#ip-address-restrictions)도 지원 합니다. 구성할 IP 주소 목록은 검색 서비스의 IP 주소 및 `AzureCognitiveSearch` service 태그의 IP 주소 범위입니다.

Azure VM에서 SQL server의 데이터에 액세스 하는 방법에 대 한 자세한 내용은 [여기](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md) 에 설명 되어 있습니다.

### <a name="configure-network-security-group-nsg-rules"></a>NSG (네트워크 보안 그룹) 규칙 구성

SQL 관리 되는 인스턴스의 데이터에 액세스 하거나 Azure VM이 [사용자 지정 Web Api 기술](cognitive-search-custom-skill-web-api.md)에 대 한 웹 서비스 URI로 사용 되는 경우 고객은 특정 IP 주소를 걱정 하지 않아도 됩니다.

이러한 경우 Azure VM 또는 SQL 관리 되는 인스턴스를 가상 네트워크 내에 상주 하도록 구성할 수 있습니다. 그러면 네트워크 보안 그룹을 구성 하 여 가상 네트워크 서브넷 및 네트워크 인터페이스에서 들어오고 나가는 네트워크 트래픽 유형을 필터링 할 수 있습니다.

`AzureCognitiveSearch` 서비스 태그는 IP 주소 범위를 조회할 필요 없이 인바운드 [Nsg 규칙](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group#work-with-security-rules) 에서 직접 사용할 수 있습니다.

SQL 관리 되는 인스턴스의 데이터에 액세스 하는 방법에 대 한 자세한 내용은 [여기](search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers.md) 에 설명 되어 있습니다.

### <a name="cosmosdb-indexing-isnt-enabled"></a>CosmosDB "인덱싱"이 사용 하도록 설정 되지 않음

Azure Cognitive Search는 Cosmos DB 인덱싱에 대해 암시적 종속성이 있습니다. Cosmos DB에서 자동 인덱싱을 해제 하는 경우 Azure Cognitive Search는 성공적인 상태를 반환 하지만 컨테이너 내용 인덱스에는 실패 합니다. 설정을 확인하고 인덱싱을 설정하는 방법에 대한 지침은 [Azure Cosmos DB에서 인덱싱 관리](https://docs.microsoft.com/azure/cosmos-db/how-to-manage-indexing-policy#use-the-azure-portal)를 참조하세요.

## <a name="document-processing-errors"></a>문서 처리 오류

### <a name="unprocessable-or-unsupported-documents"></a>처리할 수 없거나 지원되지 않는 문서

Blob 인덱서 [문서는 문서 형식을 명시적으로 지원합니다.](search-howto-indexing-azure-blob-storage.md#SupportedFormats) 경우에 따라 Blob Storage 컨테이너에는 지원되지 않는 문서가 포함되어 있습니다. 문제가 있는 다른 문서가 있을 수 있습니다. [구성 옵션을 변경](search-howto-indexing-azure-blob-storage.md#DealingWithErrors)하여 이러한 문서에서 인덱서를 중지하지 않을 수 있습니다.

```
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2019-05-06
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "failOnUnsupportedContentType" : false, "failOnUnprocessableDocument" : false } }
}
```

### <a name="missing-document-content"></a>누락된 문서 콘텐츠

Blob 인덱서는 [컨테이너의 blob에서 텍스트를 찾아서 추출합니다](search-howto-indexing-azure-blob-storage.md#how-azure-search-indexes-blobs). 텍스트 추출에 대한 몇 가지 문제는 다음과 같습니다.

* 문서에는 스캔된 이미지만 포함됩니다. 스캔된 이미지(JPG)와 같은 텍스트가 아닌 콘텐츠를 포함한 PDF Blob은 표준 Blob 인덱싱 파이프라인에서 결과를 생성하지 않습니다. 텍스트 요소가 있는 이미지 내용이 있는 경우 [인지 검색](cognitive-search-concept-image-scenarios.md) 을 사용 하 여 텍스트를 찾고 추출할 수 있습니다.
* Blob 인덱서는 메타데이터를 인덱스하도록 구성됩니다. 콘텐츠를 추출하려면 [콘텐츠 및 메타데이터를 모두 추출](search-howto-indexing-azure-blob-storage.md#controlling-which-parts-of-the-blob-are-indexed)하도록 Blob 인덱서를 구성해야 합니다.

```
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2019-05-06
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "dataToExtract" : "contentAndMetadata" } }
}
```

## <a name="index-errors"></a>인덱스 오류

### <a name="missing-documents"></a>누락된 문서

인덱서는 [데이터 원본](https://docs.microsoft.com/rest/api/searchservice/create-data-source)에서 문서를 찾습니다. 인덱싱되어야 하는 데이터 원본의 문서가 인덱스에서 누락된 것으로 나타나는 경우도 있습니다. 다음과 같은 오류가 발생할 수 있는 일반적인 몇 가지 이유가 있습니다.

* 문서는 인덱스되지 않았습니다. 성공적인 인덱서 실행을 위해 포털을 확인합니다.
* 문서는 인덱서 실행 후에 업데이트되었습니다. 인덱서가 [일정](https://docs.microsoft.com/rest/api/searchservice/create-indexer#indexer-schedule)대로 진행되는 경우 결국 문서를 다시 실행하고 선택합니다.
* 데이터 원본에 지정된 [쿼리](/rest/api/searchservice/create-data-source)는 문서를 제외합니다. 인덱서는 데이터 원본에 포함되지 않는 문서를 인덱싱할 수 없습니다.
* [필드 매핑](https://docs.microsoft.com/rest/api/searchservice/create-indexer#fieldmappings) 또는 [AI 보강](https://docs.microsoft.com/azure/search/cognitive-search-concept-intro) 문서를 변경 하 여 원하는 것과 다르게 보입니다.
* [문서 조회 API](https://docs.microsoft.com/rest/api/searchservice/lookup-document)를 사용하여 문서를 찾습니다.
