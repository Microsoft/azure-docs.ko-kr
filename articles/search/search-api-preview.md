---
title: 미리 보기 기능 목록
titleSuffix: Azure Cognitive Search
description: 미리 보기 기능은 고객이 디자인 및 유틸리티에 대 한 피드백을 제공할 수 있도록 릴리스됩니다. 이 문서는 현재 미리 보기로 제공 되는 모든 기능의 포괄적인 목록입니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 07/09/2020
ms.openlocfilehash: 0e83f63e3c39f2aa20cd46f098185aba523e2478
ms.sourcegitcommit: e69bb334ea7e81d49530ebd6c2d3a3a8fa9775c9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "88950479"
---
# <a name="preview-features-in-azure-cognitive-search"></a>Azure Cognitive Search의 미리 보기 기능

이 문서는 공개 미리 보기에 있는 모든 기능의 포괄적인 목록입니다. 미리 보기 기능은 서비스 수준 계약 없이 제공되며, 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

일반 공급으로 전환 되는 미리 보기 기능은이 목록에서 제거 됩니다. 기능이 아래에 나열 되지 않은 경우 일반적으로 사용 가능 하다 고 가정할 수 있습니다. 일반 가용성에 대 한 공지 사항은 [서비스 업데이트](https://azure.microsoft.com/updates/?product=search) 또는 [새로운 기능](whats-new.md)을 참조 하세요.

|기능&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  | 범주 | Description | 가용성  |
|---------|------------------|-------------|---------------|
| [**Azure Machine Learning (AML) 기술**](cognitive-search-aml-skill.md) | AI 보강| Azure Machine Learning에서 추론 끝점을 통합 하는 새 기술 유형입니다. [이 자습서](cognitive-search-tutorial-aml-custom-skill.md)를 시작합니다. | [검색 REST API 2020-06-30-미리 보기](/rest/api/searchservice/) 또는 2019-05-06-미리 보기를 사용 합니다. 포털에서 기술 디자인을 사용 하 여 Cognitive Search 및 Azure ML 서비스가 동일한 구독에 배포 되는 것으로 가정 합니다. |
| [**featuresMode 매개 변수**](/rest/api/searchservice/search-documents#featuresmode) | 관련성 (점수 매기기) | 세부 정보를 포함 하는 관련성 점수 확장: 필드별 유사성 점수, 필드별 별 빈도 및 일치 하는 고유 토큰의 필드 당 수 [사용자 지정 점수 매기기 솔루션](https://github.com/Azure-Samples/search-ranking-tutorial)에서 이러한 데이터 요소를 사용할 수 있습니다. | Api-version = 2020-06 -30-Preview 또는 2019-05-06-Preview를 사용 하 여 [문서 검색 (REST)](/rest/api/searchservice/search-documents) 을 사용 하 여이 쿼리 매개 변수를 추가 합니다. |
| [**관리 서비스 ID**](search-howto-managed-identities-data-sources.md) | 인덱서, 보안| Azure Active Directory를 사용 하 여 검색 서비스를 등록 한 다음 신뢰할 수 있는 서비스로 설정 하 고 Azure 데이터 원본에 대 한 RBAC 권한을 사용 하 여 인덱서에 읽기 전용 액세스를 허용 합니다. | 이 기능에 액세스 하려면 api-version = 2020-06 -30 또는 api-version = 2019-05 -06-Preview를 사용 하 여 포털을 사용 하거나 [데이터 원본 (REST)을 만듭니다](/rest/api/searchservice/create-data-source) . |
| [**디버그 세션**](cognitive-search-debug-session.md) | 포털, AI 보강 (기술) | 기술와 관련 된 문제를 조사 하 고 해결 하는 데 사용 되는 세션 내 기술 편집기입니다. 디버그 세션 중에 적용 되는 픽스는 서비스의 기술에 저장할 수 있습니다. | 개요 페이지의 중간 페이지 링크를 사용 하 여 디버그 세션을 열 수 있습니다. |
| [**네이티브 blob 일시 삭제**](search-howto-indexing-azure-blob-storage.md#incremental-indexing-and-deletion-detection) | 인덱서, Azure blob| Azure Cognitive Search의 Azure Blob Storage 인덱서는 일시 삭제 된 상태에 있는 blob을 인식 하 고 인덱싱 중에 해당 하는 검색 문서를 제거 합니다. | [Create 인덱서 (REST)](/rest/api/searchservice/create-indexer) 를 사용 하 여 Create 인덱서-version = 2020-06 -30 또는 api-version = 2019-05 -06-preview를 사용 하 여이 구성 설정을 추가 합니다. |
| [**사용자 지정 엔터티 조회 기술**](cognitive-search-skill-custom-entity-lookup.md ) | AI 보강 (기술) | 사용자 정의 된 단어 및 구 목록에서 텍스트를 찾는 인식 기술입니다. 이 목록을 사용하면 일치하는 엔터티가 있는 모든 문서에 레이블이 지정됩니다. 또한 이 기술은 비슷하지만 정확하지 않는 일치 항목을 찾는 데 적용할 수 있는 유사 항목 일치 수준을 지원합니다. | 포털에서 기술 편집기를 사용 하 여이 미리 보기 기술을 참조 하거나 api-version = 2020-06 -30-Preview 또는 api-version = 2019-05 -06-Preview를 사용 하 여 [기술 (REST) 만들기](/rest/api/searchservice/create-skillset) 를 참조 하세요. |
| [**PII 검색 기술**](cognitive-search-skill-pii-detection.md) | AI 보강 (기술) | 입력 텍스트에서 개인적으로 식별할 수 있는 정보를 추출 하 고 다양 한 방법으로 해당 텍스트를 마스킹할 수 있는 옵션을 제공 하는 인덱싱 중에 사용 되는 인식 기술입니다. | 포털에서 기술 편집기를 사용 하 여이 미리 보기 기술을 참조 하거나 api-version = 2020-06 -30-Preview 또는 api-version = 2019-05 -06-Preview를 사용 하 여 [기술 (REST) 만들기](/rest/api/searchservice/create-skillset) 를 참조 하세요. |
| [**증분 보강**](cognitive-search-incremental-indexing-conceptual.md) | 인덱서 구성| 보강 파이프라인에 캐싱을 추가 하 여 기술 또는 다른 개체에 대 한 업데이트와 같은 대상 수정에서 콘텐츠를 변경 하지 않는 경우 기존 출력을 다시 사용할 수 있도록 합니다. 캐싱은 기술에서 생성 된 보강 문서에만 적용 됩니다.| [Create 인덱서 (REST)](/rest/api/searchservice/create-indexer) 를 사용 하 여 Create 인덱서-version = 2020-06 -30 또는 api-version = 2019-05 -06-preview를 사용 하 여이 구성 설정을 추가 합니다. |
| [**Cosmos DB 인덱서: MongoDB API, Gremlin API, Cassandra API**](search-howto-index-cosmosdb.md) | 인덱서 데이터 원본 | Cosmos DB의 경우 SQL API는 일반적으로 사용할 수 있지만 MongoDB, Gremlin 및 Cassandra Api는 미리 보기 상태입니다. | Gremlin 및 Cassandra에만 해당 하는 경우 [먼저 등록](https://aka.ms/azure-cognitive-search/indexer-preview) 하 여 백 엔드에서 구독에 대해 지원을 사용할 수 있도록 합니다. MongoDB 데이터 원본은 포털에서 구성할 수 있습니다. 그렇지 않은 경우 세 가지 모든 Api에 대 한 데이터 원본 구성은 [Create Data source (REST)](/rest/api/searchservice/create-data-source) 를 사용 하 여 api-version = 2020-06 -30 또는 api-version = 2019-05 -06-preview를 사용 하 여 지원 됩니다. |
|  [**Azure Data Lake Storage Gen2 인덱서**](search-howto-index-azure-data-lake-storage.md) | 인덱서 데이터 원본 | Data Lake Storage Gen2에서 콘텐츠 및 메타 데이터를 인덱싱합니다.| 백 엔드에서 구독에 대 한 지원을 사용 하도록 설정 하려면 [등록](https://aka.ms/azure-cognitive-search/indexer-preview) 해야 합니다. Api-version = 2020-06 -30-Preview 또는 api-version = 2019-05 -06-Preview를 사용 하 여 [데이터 원본 만들기 (REST)](/rest/api/searchservice/create-data-source) 를 사용 하 여이 데이터 원본에 액세스 합니다. |
| [**moreLikeThis**](search-more-like-this.md) | 쿼리 | 특정 문서와 관련 된 문서를 찾습니다. 이 기능은 이전 미리 보기에 있었습니다. | Api-version = 2020-06 -30-Preview, 2019-05-06-Preview, 2016-09-01-Preview 또는 2017-11-11-Preview를 사용 하 여 [문서 검색 (REST)](/rest/api/searchservice/search-documents) 호출에서이 쿼리 매개 변수를 추가 합니다. |

## <a name="how-to-call-a-preview-rest-api"></a>미리 보기 REST API 호출 하는 방법

Azure Cognitive Search는 먼저 REST API를 통해 실험적 기능을 미리 릴리스한 후 시험판 버전의 .NET SDK를 통해 미리 릴리스 합니다.

미리 보기 기능은 기능 디자인 및 구현에 관한 사용자 의견을 수집하기 위해 테스트 및 실험에 사용할 수 있습니다. 이러한 이유로 미리 보기 기능은 시간에 따라 이전 버전과 호환되지 않는 방식으로 변경될 수 있습니다. 이는 이전 버전과 호환되는 일부 수정 및 향상된 기능 외에는 변경되지 않으며 안정적인 GA 버전의 기능과 대조됩니다. 또한 미리 보기 기능이 항상 GA로 릴리스되는 것은 아닙니다.

포털 및 .NET SDK에서 일부 미리 보기 기능을 사용할 수 있지만 REST API에는 항상 미리 보기 기능이 있습니다.

+ 검색 작업의 경우 [**`2020-06-30-Preview`**](/rest/api/searchservice/index-preview) 는 현재 미리 보기 버전입니다.

+ 관리 작업의 경우 [**`2019-10-01-Preview`**](/rest/api/searchmanagement/index-2019-10-01-preview) 는 현재 미리 보기 버전입니다.

이전 미리 보기가 여전히 작동하지만 시간이 지남에 따라 부실해집니다. 코드에서 `api-version=2019-05-06-Preview` 또는 `api-version=2016-09-01-Preview` 또는를 호출 하는 경우 `api-version=2017-11-11-Preview` 해당 호출은 여전히 유효 합니다. 그러나 최신 미리 보기 버전만 향상된 기능으로 새로 고쳐집니다.

다음 예제 구문은 미리 보기 API 버전에 대한 호출을 설명합니다.

```HTTP
POST https://[service name].search.windows.net/indexes/hotels-idx/docs/search?api-version=2020-06-30-Preview  
  Content-Type: application/json  
  api-key: [admin key]
```

Azure Cognitive Search 서비스는 여러 버전에서 사용할 수 있습니다. 자세한 내용은 [API 버전](search-api-versions.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

검색 REST 미리 보기 API 참조 설명서를 검토 합니다. 문제가 발생 하는 경우 [Stack Overflow](https://stackoverflow.com/) 에 대 한 도움을 요청 하거나 [고객 지원에 문의](https://azure.microsoft.com/support/community/?product=search)하세요.

> [!div class="nextstepaction"]
> [검색 서비스 REST API 참조 (미리 보기)](/rest/api/searchservice/index-preview)