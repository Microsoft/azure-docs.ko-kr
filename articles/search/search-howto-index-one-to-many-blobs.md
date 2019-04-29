---
title: 포함 하는 blob을 인덱싱해야 검색 전체 텍스트 검색-Azure Search에 Azure Blob 인덱서에서 문서 인덱싱
description: Azure blob에 대 한 Azure Search Blob 인덱서를 사용 하 여 텍스트 콘텐츠를 탐색 합니다. 각 blob는 하나 이상의 Azure Search 인덱스 문서를 포함할 수 있습니다.
ms.date: 02/12/2019
author: arv100kri
manager: briansmi
ms.author: arjagann
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seofeb2018
ms.openlocfilehash: e95eff015340659b642dff800a03f615e22c1577
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60871202"
---
# <a name="indexing-blobs-producing-multiple-search-documents"></a>여러 검색 문서를 생성 하는 blob 인덱싱
기본적으로 blob 인덱서는 단일 검색 문서와 blob의 콘텐츠를 처리 합니다. 특정 **parsingMode** 은 경우에는 개별 blob 수에서 여러 문서 검색을 지원 합니다. 다양 한 유형의 **parsingMode** blob에서 하나의 검색 문서 보다는 인덱서를 추출 하는 수 있도록 합니다.
+ `delimitedText`
+ `jsonArray`
+ `jsonLines`

> [!IMPORTANT]
> `jsonLines` 구문 분석 모드 공개 미리 보기로 제공 되며 프로덕션 환경에서는 사용할 수 없습니다. 자세한 내용은 [REST api-version=2017-11-11-Preview](search-api-2017-11-11-preview.md)를 참조하세요. 

## <a name="one-to-many-document-key"></a>일대다 문서 키
Azure Search 인덱스에 표시 되는 각 문서는 문서 키로 고유 하 게 식별 됩니다. 

비 구문 분석 모드를 지정 하면 한 경우 명시적 매핑이 없습니다 Azure Search 인덱스의 키 필드에 대 한 자동으로 [매핑합니다](search-indexer-field-mappings.md) 는 `metadata_storage_path` 속성을 키로 합니다. 이 매핑은 각 blob distinct 검색 문서로 표시 되는지 확인 합니다.

위에 나열 된 구문 분석 모드 중 하나를 사용 하는 경우 하나의 blob 적합 하지 않은 메타 데이터는 blob만을 기반으로 하는 문서 키 "many" 검색 문서에 매핑합니다. 을 극복 하기 위해이 제약 조건은 Azure Search는 blob에서 추출 된 각 개별 엔터티에 대 한 "일대다" 문서 키를 생성할 수 있습니다. 이 속성의 이름은 `AzureSearch_DocumentKey` blob에서 추출 된 각 개별 엔터티에 추가 됩니다. 이 속성의 값은 각 개별 엔터티에 대 한 고유성이 보장 _blob에서_ 엔터티 검색을 별도 문서로 표시 됩니다.

키 인덱스 필드에 대 한 명시적 필드 매핑이 지정 되 면 기본적으로는 `AzureSearch_DocumentKey` 매핑될를 사용 하는 `base64Encode` 필드 매핑 함수입니다.

## <a name="example"></a>예
다음 필드를 사용 하 여 인덱스 정의 하면 가정 합니다.
+ `id`
+ `temperature`
+ `pressure`
+ `timestamp`

Blob 컨테이너에 다음과 같은 구조를 사용 하 여 blob을

_Blob1.json_

    { "temperature": 100, "pressure": 100, "timestamp": "2019-02-13T00:00:00Z" }
    { "temperature" : 33, "pressure" : 30, "timestamp": "2019-02-14T00:00:00Z" }

_Blob2.json_

    { "temperature": 1, "pressure": 1, "timestamp": "2018-01-12T00:00:00Z" }
    { "temperature" : 120, "pressure" : 3, "timestamp": "2013-05-11T00:00:00Z" }

인덱서를 만들 때 설정 된 **parsingMode** 에 `jsonLines` -암시적으로 다음 매핑이 키 필드에 대 한 매핑을 적용할 모든 명시적 필드를 지정 하지 않고
    
    {
        "sourceFieldName" : "AzureSearch_DocumentKey",
        "targetFieldName": "id",
        "mappingFunction": { "name" : "base64Encode" }
    }

이 설치 프로그램 (간단히 나타내기 위해 축소는 base64로 인코딩된 id)는 다음 정보가 포함 된 Azure Search 인덱스에 발생 합니다.

| id | 온도 | pressure | timestamp |
|----|-------------|----------|-----------|
| aHR0 ... YjEuanNvbjsx | 100 | 100 | 2019-02-13T00:00:00Z |
| aHR0 ... YjEuanNvbjsy | 33 | 30 | 2019-02-14T00:00:00Z |
| aHR0 ... YjIuanNvbjsx | 1 | 1 | 2018-01-12T00:00:00Z |
| aHR0 ... YjIuanNvbjsy | 120 | 3 | 2013-05-11T00:00:00Z |

## <a name="custom-field-mapping-for-index-key-field"></a>인덱스 키 필드에 대 한 사용자 지정 필드 매핑

Blob 컨테이너에 다음과 같은 구조를 사용 하 여 blob을 가정해 이전 예와 동일한 인덱스 정의 가정 합니다.

_Blob1.json_

    recordid, temperature, pressure, timestamp
    1, 100, 100,"2019-02-13T00:00:00Z" 
    2, 33, 30,"2019-02-14T00:00:00Z" 

_Blob2.json_

    recordid, temperature, pressure, timestamp
    1, 1, 1,"2018-01-12T00:00:00Z" 
    2, 120, 3,"2013-05-11T00:00:00Z" 

사용 하 여 인덱서를 만들면 `delimitedText` **parsingMode**, 키 필드에 필드 매핑 함수를 다음과 같이 설정 하는 자연스럽 게 느낄 수 있습니다.

    {
        "sourceFieldName" : "recordid",
        "targetFieldName": "id"
    }

그러나이 매핑에 _하지_ 4 문서를 인덱스에 표시 하기 때문에 발생 합니다 `recordid` 필드 고유 하지 않습니다. _blob에서_. 따라서 권장 하에서 적용 하는 암시적 필드 매핑을 사용 합니다 `AzureSearch_DocumentKey` "일대다" 구문 분석 모드에 대 한 키 인덱스 필드 속성.

명시적 필드 매핑을 설정 하려면, 되었는지 확인 합니다 _원본 필드_ 각 개별 엔터티에 대 한 고유한 **모든 blob에서**합니다.

> [!NOTE]
> 사용 된 방법인 `AzureSearch_DocumentKey` 보장 고유성 추출 엔터티별 변경 될을 따라서 응용 프로그램의 요구 사항에 대 한 해당 값에 의존 하지 해야 합니다.

## <a name="see-also"></a>참고 항목

+ [Azure Search의 인덱서](search-indexer-overview.md)
+ [Azure Search로 Azure Blob Storage 인덱싱](search-howto-index-json-blobs.md)
+ [Azure Search Blob 인덱서를 사용하여 CSV Blob 인덱싱](search-howto-index-csv-blobs.md)
+ [Azure Search blob 인덱서를 사용 하 여 JSON blob 인덱싱](search-howto-index-csv-blobs.md)

## <a name="NextSteps"></a>다음 단계
* Azure Search에 대해 자세히 알아보려면 [Search 서비스 페이지](https://azure.microsoft.com/services/search/)를 참조하세요.