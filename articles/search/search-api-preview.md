---
title: REST API의 미리 보기 기능
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search service REST API 버전 2019-05-06-미리 보기에는 증분 보강에 대 한 지식 저장소 및 인덱서 캐싱과 같은 실험적 기능이 포함 되어 있습니다.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/11/2020
ms.openlocfilehash: db941152186127302680b5e659e43cd2d82a8908
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77162279"
---
# <a name="preview-features-in-azure-cognitive-search"></a>Azure Cognitive Search의 미리 보기 기능

이 문서는 현재 미리 보기로 제공 되는 기능을 나열 합니다. 미리 보기에서 일반 공급으로 전환 되는 기능은이 목록에서 제거 됩니다. [서비스 업데이트](https://azure.microsoft.com/updates/?product=search) 또는 일반 공급과 관련 된 알림의 [새로운 기능](whats-new.md) 을 확인할 수 있습니다.

포털 및 .NET SDK에서 일부 미리 보기 기능을 사용할 수 있지만 REST API에는 항상 미리 보기 기능이 있습니다.

+ 검색 작업의 경우 현재 미리 보기 버전은 [ **`2019-05-06-Preview`** ](https://docs.microsoft.com/rest/api/searchservice/index-2019-05-06-preview) 입니다.
+ 관리 작업의 경우 현재 미리 보기 버전은 [ **`2019-10-01-Preview`** ](https://docs.microsoft.com/rest/api/searchmanagement/index-2019-10-01-preview) 입니다.

> [!IMPORTANT]
> 미리 보기 기능은 서비스 수준 계약 없이 제공되며, 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="ai-enrichment-features"></a>AI 보강 기능

[미리 보기 검색 API](https://docs.microsoft.com/rest/api/searchservice/index-2019-05-06-preview)를 통해 AI 보강의 최신 개선 사항을 살펴보세요.

|||
|-|-|
| [사용자 지정 엔터티 조회 기술 (미리 보기)](cognitive-search-skill-custom-entity-lookup.md ) | 사용자 정의 된 단어 및 구 목록에서 텍스트를 찾는 인식 기술입니다. 이 목록을 사용 하 여 일치 하는 엔터티가 있는 모든 문서의 레이블을 표시 합니다. 이 기술은 유사 하지만 정확 하지 않은 일치 항목을 찾기 위해 적용할 수 있는 유사 항목 일치도 지원 합니다. | 
| [PII 감지 기술 (미리 보기)](cognitive-search-skill-pii-detection.md) | 입력 텍스트에서 개인적으로 식별할 수 있는 정보를 추출 하 고 다양 한 방법으로 해당 텍스트를 마스킹할 수 있는 옵션을 제공 하는 인덱싱 중에 사용 되는 인식 기술입니다.| 
| [증분 보강 (미리 보기)](cognitive-search-incremental-indexing-conceptual.md) | 보강 파이프라인에 캐싱을 추가 하 여 기술 또는 다른 개체에 대 한 업데이트와 같은 대상 수정에서 콘텐츠를 변경 하지 않는 경우 기존 출력을 다시 사용할 수 있도록 합니다. 캐싱은 기술에서 생성 된 보강 문서에만 적용 됩니다.| 
| [지식 저장소(미리 보기)](knowledge-store-concept-intro.md) | AI 기반 보강 파이프라인의 새 대상입니다. 물리적 데이터 구조는 Azure Blob storage 및 Azure Table storage에 존재 하며, 연결 된 인지 기술 있는 인덱서를 실행할 때 생성 되 고 채워집니다. 기술 자료 저장소 자체의 정의는 기술 정의 내에서 지정 됩니다. 기술 자료 저장소 정의 내에서 데이터의 모양을 결정 하는 프로젝션 요소, 데이터가 테이블 저장소 또는 Blob 저장소에 저장 되는지 여부 및 여러 뷰가 있는지 여부를 결정 하는 *프로젝션* 요소를 통해 데이터의 물리적 구조를 제어 합니다.| 

## <a name="indexing-and-query-features"></a>인덱싱 및 쿼리 기능

인덱서 미리 보기 기능은 미리 보기 검색 API에서 사용할 수 있습니다. 

|||
|-|-|
| [Cosmos DB 인덱서](search-howto-index-cosmosdb.md) | MongoDB API (미리 보기), Gremlin API (미리 보기) 및 Cassandra API (미리 보기) API 형식에 대 한 지원. | 
|  [Azure Data Lake Storage Gen2 인덱서 (미리 보기)](search-howto-index-azure-data-lake-storage.md) | Data Lake Storage Gen2에서 콘텐츠 및 메타 데이터를 인덱싱합니다.| 
| [moreLikeThis 쿼리 매개 변수 (미리 보기)](search-more-like-this.md) | 특정 문서와 관련 된 문서를 찾습니다. 이 기능은 이전 미리 보기에 있었습니다. | 

## <a name="management-features"></a>관리 기능

|||
|-|-|
| [개인 끝점 지원](service-create-private-endpoint.md) | 보안 클라이언트 (예: 가상 머신)를 사용 하 여 가상 네트워크를 만든 다음 개인 끝점을 사용 하는 검색 서비스를 만들 수 있습니다. |
| IP 액세스 제한 | 관리 REST API [`api-version=2019-10-01-Preview`](https://docs.microsoft.com/rest/api/searchmanagement/index-2019-10-01-preview) 사용 하 여 액세스를 허용 하는 IP 주소에 대 한 제한이 있는 서비스를 만들 수 있습니다. |

## <a name="earlier-preview-features"></a>이전 미리 보기 기능

이전 미리 보기에서 발표 된 기능은 일반 공급으로 전환 되지 않은 경우 아직 공개 미리 보기로 제공 됩니다. 이전 미리 보기 API 버전에서 API를 호출하는 경우 계속 해당 버전을 사용하거나 예상되는 동작을 변경하지 않고 `2019-05-06-Preview`로 전환할 수 있습니다.

## <a name="how-to-call-a-preview-api"></a>미리 보기 API를 호출하는 방법

이전 미리 보기가 여전히 작동하지만 시간이 지남에 따라 부실해집니다. 코드가 `api-version=2016-09-01-Preview` 또는 `api-version=2017-11-11-Preview`를 호출하는 경우 이러한 호출은 여전히 유효합니다. 그러나 최신 미리 보기 버전만 향상된 기능으로 새로 고쳐집니다. 

다음 예제 구문은 미리 보기 API 버전에 대한 호출을 설명합니다.

    GET https://[service name].search.windows.net/indexes/[index name]/docs?search=*&api-version=2019-05-06-Preview

Azure Cognitive Search 서비스는 여러 버전에서 사용할 수 있습니다. 자세한 내용은 [API 버전](search-api-versions.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

검색 REST API 참조 설명서를 검토 합니다. 문제가 발생 하는 경우 [Stackoverflow](https://stackoverflow.com/) 또는 [지원 담당자에 게](https://azure.microsoft.com/support/community/?product=search)도움을 요청 합니다.

> [!div class="nextstepaction"]
> [검색 서비스 REST API 참조](https://docs.microsoft.com/rest/api/searchservice/)