---
title: Cache 및 증분 보강 구성 (미리 보기)
titleSuffix: Azure Cognitive Search
description: 인식 기술에서 제어 되는 처리를 위해 보강 콘텐츠의 상태를 캐싱 및 유지할 수 있습니다. 이 기능은 현재 공개 미리 보기로 제공됩니다.
author: vkurpad
manager: eladz
ms.author: vikurpad
ms.service: cognitive-search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 01/06/2020
ms.openlocfilehash: c432b89574949b31612aeba862ece7687c12dde4
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88922840"
---
# <a name="how-to-configure-caching-for-incremental-enrichment-in-azure-cognitive-search"></a>Azure Cognitive Search에서 증분 보강 캐싱을 구성 하는 방법

> [!IMPORTANT] 
> 증분 보강 현재 공개 미리 보기 상태입니다. 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요. [REST API 버전 2019-05-06-미리 보기 및 2020-06-30-미리 보기](search-api-preview.md) 에서이 기능을 제공 합니다. 지금은 포털 또는 .NET SDK가 지원 되지 않습니다.

이 문서에서는 보강 파이프라인에 캐싱을 추가 하는 방법을 보여 줍니다. 이렇게 하면 매번 다시 빌드하지 않고도 단계를 증분 수정할 수 있습니다. 기본적으로 기술는 상태 비저장 이며 해당 컴포지션의 모든 부분을 변경 하려면 인덱서를 전체 다시 실행 해야 합니다. 증분 보강을 사용 하면 인덱서는 기술 또는 인덱서 정의에서 검색 된 변경 내용을 기반으로 새로 고쳐야 하는 문서 트리 부분을 확인할 수 있습니다. 처리 된 기존 출력은 가능한 모든 경우에 유지 되 고 다시 사용 됩니다. 

캐시 된 콘텐츠는 사용자가 제공 하는 계정 정보를 사용 하 여 Azure Storage에 배치 됩니다. 라는 컨테이너는 `ms-az-search-indexercache-<alpha-numerc-string>` 인덱서를 실행할 때 생성 됩니다. 검색 서비스에서 관리 하는 내부 구성 요소로 간주 해야 하며 수정 하면 안 됩니다.

인덱서를 설정 하는 방법을 잘 모르는 경우 [인덱서 개요](search-indexer-overview.md) 를 시작 하 고 [기술력과](cognitive-search-working-with-skillsets.md) 를 계속 진행 하 여 보강 파이프라인에 대해 알아보세요. 주요 개념에 대 한 자세한 배경 정보는 [증분 보강](cognitive-search-incremental-indexing-conceptual.md)를 참조 하세요.

## <a name="enable-caching-on-an-existing-indexer"></a>기존 인덱서에서 캐싱 사용

이미 기술가 있는 기존 인덱서가 있는 경우이 섹션의 단계에 따라 캐싱을 추가 합니다. 일회성 작업을 수행 하려면 증분 처리를 적용 하려면 먼저 인덱서를 다시 설정 하 고 다시 실행 해야 합니다.

> [!TIP]
> 개념 증명으로이 [포털 빠른](cognitive-search-quickstart-blob.md) 시작을 실행 하 여 필요한 개체를 만든 다음, postman 또는 portal을 사용 하 여 업데이트를 수행할 수 있습니다. 청구 가능 Cognitive Services 리소스를 연결 하는 것이 좋습니다. 인덱서를 여러 번 실행 하면 모든 단계를 완료 하기 전에 매일 무료 할당이 고갈 됩니다.

### <a name="step-1-get-the-indexer-definition"></a>1 단계: 인덱서 정의 가져오기

데이터 원본, 기술, 인덱스 등의 구성 요소가 있는 유효한 기존 인덱서를 사용 하 여 시작 합니다. 인덱서를 실행할 수 있어야 합니다. 

API 클라이언트를 사용 하 여 인덱서의 현재 구성을 가져오기 위한 [Get 인덱서 요청](/rest/api/searchservice/get-indexer) 을 생성 합니다. Preview API 버전을 사용 하 여 인덱서를 가져오는 경우 `cache` null로 설정 된 속성이 정의에 추가 됩니다.

```http
GET https://[YOUR-SEARCH-SERVICE].search.windows.net/indexers/[YOUR-INDEXER-NAME]?api-version=2020-06-30-Preview
Content-Type: application/json
api-key: [YOUR-ADMIN-KEY]
```

응답에서 인덱서 정의를 복사 합니다.

### <a name="step-2-modify-the-cache-property-in-the-indexer-definition"></a>2 단계: 인덱서 정의의 cache 속성 수정

기본적으로 `cache` 속성은 null입니다. API 클라이언트를 사용 하 여 캐시 구성을 설정 합니다 (포털에서이 particulate 업데이트를 지원 하지 않음). 

다음 필수 및 선택적 속성을 포함 하도록 cache 개체를 수정 합니다. 

+ 는 `storageConnectionString` 필수 이며, Azure storage 연결 문자열로 설정 해야 합니다. 
+ `enableReprocessing`부울 속성은 선택적 이며 ( `true` 기본적으로) 증분 보강 사용 하도록 설정 되어 있음을 나타냅니다. 필요한 경우 `false` 새 문서 인덱싱과 같은 다른 리소스를 많이 사용 하는 작업이 진행 되는 동안 증분 처리를 일시 중단 하 고 나중에 다시 대칭 이동 하도록 설정할 수 있습니다 `true` .

```json
{
    "name": "<YOUR-INDEXER-NAME>",
    "targetIndexName": "<YOUR-INDEX-NAME>",
    "dataSourceName": "<YOUR-DATASOURCE-NAME>",
    "skillsetName": "<YOUR-SKILLSET-NAME>",
    "cache" : {
        "storageConnectionString" : "<YOUR-STORAGE-ACCOUNT-CONNECTION-STRING>",
        "enableReprocessing": true
    },
    "fieldMappings" : [],
    "outputFieldMappings": [],
    "parameters": []
}
```

### <a name="step-3-reset-the-indexer"></a>3 단계: 인덱서 다시 설정

모든 문서가 일관 된 상태가 되도록 기존 인덱서에 대 한 증분 보강을 설정할 때는 인덱서를 다시 설정 해야 합니다. 이 작업에 대 한 포털 또는 API 클라이언트 및 [다시 설정 인덱서 REST API](/rest/api/searchservice/reset-indexer) 을 사용할 수 있습니다.

```http
POST https://[YOUR-SEARCH-SERVICE].search.windows.net/indexers/[YOUR-INDEXER-NAME]/reset?api-version=2020-06-30-Preview
Content-Type: application/json
api-key: [YOUR-ADMIN-KEY]
```

### <a name="step-4-save-the-updated-definition"></a>4 단계: 업데이트 된 정의 저장

PUT 요청을 사용 하 여 [인덱서를 업데이트](/rest/api/searchservice/preview-api/update-indexer) 합니다. 요청 본문에는 cache 속성이 있는 업데이트 된 인덱서 정의가 포함 되어야 합니다. 400을 가져오는 경우 인덱서 정의를 확인 하 여 모든 요구 사항 (데이터 원본, 기술, 인덱스)이 충족 되는지 확인 합니다.

```http
PUT https://[YOUR-SEARCH-SERVICE].search.windows.net/indexers/[YOUR-INDEXER-NAME]?api-version=2020-06-30-Preview
Content-Type: application/json
api-key: [YOUR-ADMIN-KEY]
{
    "name" : "<YOUR-INDEXER-NAME>",
    ...
    "cache": {
        "storageConnectionString": "<YOUR-STORAGE-ACCOUNT-CONNECTION-STRING>",
        "enableReprocessing": true
    }
}
```

이제 인덱서에 다른 GET 요청을 실행 하면 서비스의 응답에 `ID` cache 개체의 속성이 포함 됩니다. 이 인덱서에 의해 처리 되는 각 문서의 중간 상태와 모든 캐시 된 결과를 포함 하는 컨테이너의 이름에 영숫자 문자열이 추가 됩니다. ID는 Blob 저장소에서 캐시의 이름을 고유 하 게 지정할 때 사용 됩니다.

```http
    "cache": {
        "ID": "<ALPHA-NUMERIC STRING>",
        "enableReprocessing": true,
        "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<YOUR-STORAGE-ACCOUNT>;AccountKey=<YOUR-STORAGE-KEY>;EndpointSuffix=core.windows.net"
    }
```

### <a name="step-5-run-the-indexer"></a>5 단계: 인덱서 실행

인덱서를 실행 하려면 포털 또는 API를 사용할 수 있습니다. 포털의 인덱서 목록에서 인덱서를 선택 하 고 **실행**을 클릭 합니다. 포털을 사용 하는 경우의 이점 중 하나는 인덱서 상태를 모니터링 하 고, 작업 기간을 확인 하 고, 처리 되는 문서 수를 확인 하는 것입니다. 포털 페이지는 몇 분 마다 새로 고쳐집니다.

또는 REST를 사용 하 여 [인덱서를 실행할](/rest/api/searchservice/run-indexer)수 있습니다.

```http
POST https://[YOUR-SEARCH-SERVICE].search.windows.net/indexers/[YOUR-INDEXER-NAME]/run?api-version=2020-06-30-Preview
Content-Type: application/json
api-key: [YOUR-ADMIN-KEY]
```

인덱서를 실행 한 후에는 Azure Blob 저장소에서 캐시를 찾을 수 있습니다. 컨테이너 이름의 형식은 다음과 같습니다. `ms-az-search-indexercache-<YOUR-CACHE-ID>`

> [!NOTE]
> 인덱서를 다시 설정 하 고 다시 실행 하면 콘텐츠를 캐시할 수 있도록 전체 다시 작성 됩니다. 모든 인지 강화은 모든 문서에서 다시 실행 됩니다.
>

### <a name="step-6-modify-a-skillset-and-confirm-incremental-enrichment"></a>6 단계: 기술 수정 및 증분 보강 확인

기술를 수정 하려면 포털 또는 API를 사용할 수 있습니다. 예를 들어 텍스트 번역을 사용 하는 경우에서 `en` 로 또는 다른 언어로의 간단한 인라인 변경은 `es` 증분 보강의 개념 증명 테스트에 충분 합니다.

인덱서를 다시 실행 합니다. 보강 문서 트리의 부분만 업데이트 됩니다. 개념 증명으로 [포털 빠른](cognitive-search-quickstart-blob.md) 시작을 사용 하 여 텍스트 번역 기술을 ' s '로 수정 하면 원본 14 대신 8 개의 문서만 업데이트 됩니다. 변환 프로세스의 영향을 받지 않는 이미지 파일은 캐시에서 다시 사용 됩니다.

## <a name="enable-caching-on-new-indexers"></a>새 인덱서에 캐싱을 사용 하도록 설정

새 인덱서에 대해 증분 보강을 설정 하려면 `cache` [Create 인덱서 (2020-06-30-Preview)](/rest/api/searchservice/preview-api/create-indexer)를 호출할 때 인덱서 정의 페이로드에 속성을 포함 해야 합니다. 


```json
{
    "name": "<YOUR-INDEXER-NAME>",
    "targetIndexName": "<YOUR-INDEX-NAME>",
    "dataSourceName": "<YOUR-DATASOURCE-NAME>",
    "skillsetName": "<YOUR-SKILLSET-NAME>",
    "cache" : {
        "storageConnectionString" : "<YOUR-STORAGE-ACCOUNT-CONNECTION-STRING>",
        "enableReprocessing": true
    },
    "fieldMappings" : [],
    "outputFieldMappings": [],
    "parameters": []
    }
}
```

## <a name="checking-for-cached-output"></a>캐시 된 출력을 확인 하는 중

캐시는 인덱서를 사용 하 여 생성, 사용 및 관리 되며, 해당 콘텐츠는 사람이 읽을 수 있는 형식으로 표시 되지 않습니다. 캐시가 사용 되는지 여부를 확인 하는 가장 좋은 방법은 실행 시간 및 문서 수에 대해 인덱서를 실행 하 고 이전 및 이후 메트릭을 비교 하 여 실행 하는 것입니다. 

예를 들어 스캔 한 문서의 이미지 분석과 OCR (광학 인식)으로 시작 하는 기술 결과 텍스트의 다운스트림 분석으로 가정 합니다. 다운스트림 텍스트 기술을 수정 하면 인덱서가 이전에 처리 된 모든 이미지 및 OCR 콘텐츠를 캐시에서 검색할 수 있으며, 편집으로 표시 된 텍스트 관련 변경 내용만 업데이트 하 고 처리할 수 있습니다. 문서 수에서 문서 수 (8/8 예: 원래 실행의 14/14과 반대), 짧은 실행 시간 및 청구서에 대 한 요금을 줄일 수 있습니다.

## <a name="working-with-the-cache"></a>캐시 작업

캐시가 작동 하면 인덱서는 [실행 인덱서](/rest/api/searchservice/run-indexer) 가 호출 될 때마다 캐시를 확인 하 여 기존 출력의 어떤 부분을 사용할 수 있는지 확인 합니다. 

다음 표에는 다양 한 Api가 캐시와 관련 된 방법이 요약 되어 있습니다.

| API           | 캐시 영향     |
|---------------|------------------|
| [인덱서 만들기 (2020-06-30-미리 보기)](/rest/api/searchservice/preview-api/create-indexer) | 인덱서 정의에서 지정 하는 경우 캐시 만들기를 포함 하 여 처음 사용할 때 인덱서를 만들고 실행 합니다. |
| [인덱서 실행](/rest/api/searchservice/run-indexer) | 요청 시 보강 파이프라인을 실행 합니다. 이 API는 캐시에 있는 경우 캐시를 읽고 업데이트 된 인덱서 정의에 캐싱을 추가한 경우 캐시를 만듭니다. 캐싱이 설정 된 인덱서를 실행 하는 경우 캐시 된 출력을 사용할 수 있으면 인덱서는 단계를 생략 합니다. 이 API의 일반 공급 또는 미리 보기 API 버전을 사용할 수 있습니다.|
| [인덱서 다시 설정](/rest/api/searchservice/reset-indexer)| 모든 증분 인덱싱 정보에 대 한 인덱서를 지웁니다. 모든 스킬을 다시 실행 하 고 캐시를 다시 작성 하는 것을 포함 하 여 다음 인덱서 실행 (주문형 또는 schedule)은 완전히 다시 처리 됩니다. 인덱서를 삭제 하 고 다시 만드는 것과 기능적으로 동일 합니다. 이 API의 일반 공급 또는 미리 보기 API 버전을 사용할 수 있습니다.|
| [기술 다시 설정](/rest/api/searchservice/preview-api/reset-skills) | 어떠한 기술도 수정 하지 않은 경우에도 다음 인덱서 실행 시 다시 실행할 기술을 지정 합니다. 캐시는 그에 따라 업데이트 됩니다. 기술 자료 저장소 또는 검색 인덱스와 같은 출력은 캐시의 재사용 가능한 데이터와 업데이트 된 기술 당 새 콘텐츠를 사용 하 여 새로 고쳐집니다. |

캐시에 발생 하는 작업을 제어 하는 방법에 대 한 자세한 내용은 [캐시 관리](cognitive-search-incremental-indexing-conceptual.md#cache-management)를 참조 하세요.

## <a name="next-steps"></a>다음 단계

증분 보강는 기술력과를 포함 하는 인덱서에 적용할 수 있습니다. 다음 단계로 기술 설명서를 방문 하 여 개념과 컴퍼지션을 이해 하세요. 

또한 캐시를 사용 하도록 설정 하면 특정 동작을 재정의 하거나 강제 적용 하는 방법을 비롯 하 여 캐시에 해당 하는 매개 변수 및 Api에 대해 알고 싶을 것입니다. 자세한 내용은 다음 링크를 참조하세요.

+ [기술 개념 및 컴퍼지션](cognitive-search-working-with-skillsets.md)
+ [기술 세트를 만드는 방법](cognitive-search-defining-skillset.md)
+ [증분 보강 및 캐싱 소개](cognitive-search-incremental-indexing-conceptual.md)