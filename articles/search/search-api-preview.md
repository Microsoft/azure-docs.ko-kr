---
title: Azure Search 2017-11-11-Preview에 대한 Preview REST API - Azure Search
description: Azure Search Service REST API 버전 2017-11-11-Preview에는 동의어 및 moreLikeThis 검색과 같은 실험적 기능이 포함되어 있습니다.
services: search
author: HeidiSteen
manager: cgronlun
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: search
ms.date: 06/28/2018
ms.author: HeidiSteen
ms.custom: seodec2018
ms.openlocfilehash: 524c1a6d083db02349c7dae9a0131228613dc170
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61127096"
---
# <a name="azure-search-service-rest-api-version-2017-11-11-preview"></a>Azure Search Service REST api-version 2017-11-11-Preview
이 문서에서는 `api-version=2017-11-11-Preview` 버전의 Azure Search Service REST API를 설명하면서 아직 일반 공개되지 않은 실험적 기능을 제공합니다.

> [!NOTE]
> 미리 보기 기능은 사용자 의견을 수집하고 변경하기 위해 테스트 및 실험에 사용할 수 있습니다. 프로덕션 애플리케이션에서 미리 보기 API를 사용하지 않는 것이 좋습니다.


## <a name="new-in-2017-11-11-preview"></a>2017-11-11-Preview의 새로운 기능

[**자동 완성**](search-autocomplete-tutorial.md)은 검색 창에 보완적인 자동 완성 환경을 추가하는 기존의 [추천 단어 API](https://docs.microsoft.com/rest/api/searchservice/suggestions)를 결합합니다. 자동 완성은 후속 검색에 대한 쿼리 문자열로 선택할 수 있는 후보 쿼리 용어를 반환합니다. 추천 단어는 부분적인 입력에 대한 응답으로 실제 문서를 반환합니다. 검색 결과는 즉시 제공되며, 검색어 입력의 길이가 길어지고 좀 더 구체화됨에 따라 동적으로 변경됩니다.

지금 공개 미리 보기 상태인 [**인식 검색**](cognitive-search-concept-intro.md)은 텍스트가 아닌 원본 및 구분되지 않는 텍스트에서 잠재적인 정보를 찾아 이를 Azure Search에서 전체 텍스트 검색 가능한 콘텐츠로 변환하는 Azure Search의 새로운 보강 기능입니다. 미리 보기 REST API에서는 다음 리소스가 도입되거나 수정되었습니다. 다른 모든 REST API는 GA 버전을 호출하든 미리 보기 버전을 호출하든 상관없이 동일합니다.

+ [기술 집합 작업(api-version=2017-11-11-Preview)](https://docs.microsoft.com/rest/api/searchservice/skillset-operations)

+ [인덱서 만들기(api-version=2017-11-11-Preview)](https://docs.microsoft.com/rest/api/searchservice/create-indexer)

+ [미리 정의된 기술](cognitive-search-predefined-skills.md)

다른 모든 REST API는 API 버전을 설정하는 방법에 관계 없이 동일합니다. 예를 들어 `GET https://[service name].search.windows.net/indexes/hotels?api-version=2017-11-11-Preview` 및 `GET https://[service name].search.windows.net/indexes/hotels?api-version=2017-11-11`(`Preview` 없음)은 기능적으로 동일합니다.

## <a name="other-preview-features"></a>기타 미리 보기 기능

이전 미리 보기에서 공지된 기능이 여전히 공개 미리 보기 상태로 제공됩니다. 이전 미리 보기 API 버전에서 API를 호출하는 경우 계속 해당 버전을 사용하거나 예상되는 동작을 변경하지 않고 `2017-11-11-Preview`로 전환할 수 있습니다.

+ `api-version=2015-02-28-Preview`에 도입된 [Azure Blob 인덱싱의 CSV 파일](search-howto-index-csv-blobs.md)은 미리 보기 기능을 유지합니다. 이 기능은 Azure Blob 인덱싱의 일부이며 매개 변수 설정을 통해 호출됩니다. CSV 파일의 각 줄은 별도 문서로 인덱싱됩니다.

+ `api-version=2015-02-28-Preview`에 도입된 [Azure Blob 인덱싱의 JSON 배열](search-howto-index-json-blobs.md)은 미리 보기 기능을 유지합니다. 이 기능은 Azure Blob 인덱싱의 일부이며 매개 변수 설정을 통해 호출됩니다. 배열의 각 요소가 별도의 문서로 인덱싱됩니다.

+ [moreLikeThis 쿼리 매개 변수](search-more-like-this.md)는 특정 문서와 관련된 문서를 찾습니다. 이 기능은 이전 미리 보기에 있었습니다. 


## <a name="how-to-call-a-preview-api"></a>미리 보기 API를 호출하는 방법

이전 미리 보기가 여전히 작동하지만 시간이 지남에 따라 부실해집니다. 코드가 `api-version=2016-09-01-Preview` 또는 `api-version=2015-02-28-Preview`를 호출하는 경우 이러한 호출은 여전히 유효합니다. 그러나 최신 미리 보기 버전만 향상된 기능으로 새로 고쳐집니다. 

다음 예제 구문은 미리 보기 API 버전에 대한 호출을 설명합니다.

    GET https://[service name].search.windows.net/indexes/[index name]/docs?search=*&api-version=2017-11-11-Preview

Azure Search 서비스는 여러 버전으로 제공됩니다. 자세한 내용은 [API 버전](search-api-versions.md)을 참조하세요.
