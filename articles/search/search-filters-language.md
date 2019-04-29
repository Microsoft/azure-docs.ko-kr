---
title: 검색 인덱스의 다국어 콘텐츠에 대한 언어 필터 - Azure Search
description: 언어별 필드로 쿼리 실행 범위를 지정하는 다국어 검색을 지원하는 필터 기준입니다.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.workload: search
ms.topic: conceptual
ms.date: 10/23/2017
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 695fdfba1573ff97b05f8e8b50a05bef9dbf48de
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61289625"
---
# <a name="how-to-filter-by-language-in-azure-search"></a>Azure Search에서 언어에 따라 필터링하는 방법 

다국어 검색 애플리케이션의 핵심 요구 사항은 사용자의 언어로 검색하고 결과를 볼 수 있는 기능입니다. Azure Search에서 다국어 앱의 언어 요구 사항을 충족시키는 한 가지 방법은 문자열을 특정 언어로 저장하는 일련의 필드를 만든 다음 쿼리 시 전체 텍스트 검색을 해당 필드로 제한하는 것입니다.

요청에 대한 쿼리 매개 변수는 검색 작업 범위를 지정하고 전달하려는 검색 환경과 호환되는 콘텐츠를 제공하지 않는 필드의 결과를 잘라내는 데 사용됩니다.

| 매개 변수 | 목적 |
|-----------|--------------|
| **searchFields** | 전체 텍스트 검색을 명명된 필드 목록으로 제한합니다. |
| **$select** | 지정하는 필드만 포함하도록 응답을 잘라냅니다. 기본적으로 검색 가능한 모든 필드가 반환됩니다. **$select** 매개 변수를 통해 반환할 필드를 선택할 수 있습니다. |

이 기술의 성공은 필드 콘텐츠의 무결성에 달려 있습니다. Azure Search는 문자열을 번역하거나 언어 감지를 수행하지 않습니다. 필드에 예상하는 문자열이 포함되어 있는지 확인하는 것은 사용자의 몫입니다.

## <a name="define-fields-for-content-in-different-languages"></a>콘텐츠 필드를 다양한 언어로 정의

Azure Search에서 쿼리는 단일 인덱스를 대상으로 합니다. 단일 검색 환경에서 언어별 문자열을 제공하려는 개발자는 일반적으로 값을 저장하는 전용 필드(영어 문자열 필드 하나, 프랑스 문자열 필드 하나 등)를 정의합니다. 

아래에 표시된 [부동산 샘플](search-get-started-portal.md)을 비롯한 여기에 사용되는 샘플에서는 다음 스크린샷과 비슷한 필드 정의가 표시될 수 있습니다. 이 예제에서 이 인덱스의 필드에 대한 언어 분석기 할당을 보여 주는 방법에 유의하세요. 문자열이 포함된 필드는 대상 언어의 언어 규칙을 처리하도록 엔지니어링된 분석기와 함께 사용할 때 전체 텍스트 검색이 더 잘 수행됩니다.

  ![](./media/search-filters-language/lang-fields.png)

> [!Note]
> 언어 분석기로 필드 정의를 보여 주는 코드 예제는 [인덱스 정의(.NET)](https://docs.microsoft.com/azure/search/search-create-index-dotnet) 및 [인덱스 정의(REST)](search-create-index-rest-api.md)를 참조하세요.

## <a name="build-and-load-an-index"></a>인덱스 빌드 및 로드

중간(아마도 명백한) 단계에서 쿼리를 작성하기 전에 [인덱스를 빌드하고 채워야 합니다](https://docs.microsoft.com/azure/search/search-create-index-dotnet). 이 단계는 완전성을 위해 언급하는 것입니다. 인덱스 사용 가능 여부를 확인하는 한 가지는 [포털](https://portal.azure.com)에서 인덱스 목록을 확인하는 것입니다.

## <a name="constrain-the-query-and-trim-results"></a>쿼리 제한 및 결과 자르기

쿼리의 매개 변수는 검색을 특정 필드로 제한한 다음 시나리오에 도움이 되지 않는 필드의 결과를 자르는 데 사용됩니다. 프랑스어 문자열을 포함하는 필드로 검색을 제한하려면 **searchFields**를 사용하여 해당 언어의 문자열이 포함된 필드로 쿼리의 대상을 지정합니다. 

기본적으로 검색은 검색 가능으로 표시된 모든 필드를 반환합니다. 따라서 제공하려는 언어별 검색 환경을 따르지 않는 필드는 제외하려고 할 수 있습니다. 특히 프랑스어 문자열이 있는 필드로 검색을 제한하면 영어 문자열이 있는 필드를 결과에서 제외할 수 있습니다. **$select** 쿼리 매개 변수를 사용하면 호출 애플리케이션에 반환되는 필드를 제어할 수 있습니다.

```csharp
parameters =
    new SearchParameters()
    {
        searchFields = "description_fr" 
        Select = new[] { "description_fr"  }
    };
```
> [!Note]
> 쿼리에 $filter 인수가 없지만 이 사용 사례는 필터 개념과 밀접한 관련이 있으므로 필터링 시나리오로 제시합니다.

## <a name="see-also"></a>참고 항목

+ [Azure Search의 필터](search-filters.md)
+ [언어 분석기](https://docs.microsoft.com/rest/api/searchservice/language-support)
+ [Azure Search의 전체 텍스트 검색 작동 방식](search-lucene-query-architecture.md)
+ [문서 검색 REST API](https://docs.microsoft.com/rest/api/searchservice/search-documents)

