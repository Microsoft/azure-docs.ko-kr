---
title: Azure Portal에서 검색 인덱스 만들기
titleSuffix: Azure Cognitive Search
description: 기본 제공 포털 인덱스 디자이너를 사용 하 여 Azure Cognitive Search에 대 한 인덱스를 만드는 방법에 대해 알아봅니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: f2e875c625431867e6e83cfd1e0b2c6d7a2781f7
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74112854"
---
# <a name="create-an-azure-cognitive-search-index-in-the-portal"></a>포털에서 Azure Cognitive Search 인덱스 만들기

Azure Cognitive Search에는 Azure Cognitive Search 서비스에서 호스트 되는 [검색 인덱스](search-what-is-an-index.md) 를 만들거나 프로토타입에 유용 하 게 사용할 포털의 기본 제공 인덱스 디자이너가 포함 되어 있습니다. 이 도구는 스키마 생성에 사용됩니다. 정의를 저장 하면 빈 인덱스가 Azure Cognitive Search에 완전히 표현 됩니다. 검색 가능한 콘텐츠로 부하를 로드 하는 방법은 사용자가 결정 합니다.

인덱스 디자이너는 인덱스를 만들기 위한 유일한 방법입니다. 또는 [데이터 가져오기 마법사](search-get-started-portal.md)를 사용 하 여 인덱스를 만들고 로드할 수 있습니다. 마법사는 자신이 만든 인덱스에 대해서만 작동 합니다. 프로그래밍 방식으로 [.NET](search-create-index-dotnet.md) 또는 [REST](search-create-index-rest-api.md) API를 사용하여 인덱스를 만들 수 있습니다.

## <a name="start-index-designer"></a>인덱스 디자이너 시작

1. [Azure Portal](https://portal.azure.com)에 로그인하여 서비스 대시보드를 엽니다. 표시줄에서 **모든 서비스**를 클릭하면 현재 구독에서 기존 "검색 서비스"를 검색할 수 있습니다. 

2. 페이지 맨 위에 있는 명령 모음에서 **인덱스 추가** 링크를 클릭합니다.

   ![명령 모음에서 인덱스 링크 추가](media/search-create-index-portal/add-index.png "명령 모음에서 인덱스 링크 추가")

3. Azure Cognitive Search 인덱스의 이름을로 합니다. 인덱스 이름은 인덱싱 및 쿼리 작업에서 참조됩니다. 인덱스 이름은 인덱스에 연결 하 고 Azure Cognitive Search REST API에서 HTTP 요청을 보내는 데 사용 되는 엔드포인트 URL의 일부가 됩니다.

   * 문자로 시작합니다.
   * 소문자, 숫자 또는 대시(“-”)만 사용합니다.
   * 이름은 60자로 제한합니다.

## <a name="add-fields"></a>필드 추가

인덱스 컴퍼지션은 인덱스에서 검색 가능한 데이터를 정의하는 *필드 컬렉션*을 포함합니다. 필드 컬렉션은 모두 개별적으로 업로드하는 문서 구조를 지정합니다. 필드 컬렉션에는 필드가 사용되는 방식을 결정하는 인덱스 특성과 함께 이름 및 형식이 지정된 필수 및 옵션 필드가 포함됩니다.

1. 업로드할 문서를 완전히 지정하는 필드를 추가하고 각 필드의 [데이터 형식](https://docs.microsoft.com/rest/api/searchservice/supported-data-types)을 설정합니다. 예를 들어 문서가 *hotel-id*, *hotel-name*, *address*, *city* 및 *region*으로 구성된 경우 인덱스에 각 항목의 해당 필드를 만듭니다. 특성 설정에 대한 도움말은 [아래 섹션의 디자인 지침](#design)을 참조하세요.

1. 들어오는 데이터가 계층적 이면 스키마에 중첩 된 구조를 나타내는 [복합 형식이](search-howto-complex-data-types.md) 포함 되어야 합니다. 기본 제공 샘플 데이터 집합 호텔은 각 호텔에 대해 일 대 일 관계를 갖는 주소 (여러 개의 하위 필드 포함)를 사용 하는 복합 형식을 보여 주며, 대화방 복합 컬렉션은 여러 개의 대화방에 연결 되어 있습니다. 

1. Edm.String 형식의 *key* 필드를 지정합니다. 키 필드는 모든 Azure Cognitive Search 인덱스에 필수 이며 문자열 이어야 합니다. 이 필드의 값은 각 문서를 고유하게 식별해야 합니다. 기본적으로 필드 이름은 *id*로 지정되지만 문자열이 [명명 규칙](https://docs.microsoft.com/rest/api/searchservice/Naming-rules)을 충족하기만 하면 이름을 변경할 수 있습니다. 예를 들어 필드 컬렉션에 *hotel-id*가 포함된 경우 해당 필드를 키로 선택합니다. 

1. 각 필드의 특성을 설정합니다. 인덱스 디자이너는 데이터 형식에 유효하지 않은 모든 특성을 제외하지만 포함할 특성을 제안하지는 않습니다. 특성의 용도를 파악하려면 다음 섹션의 지침을 참조하세요.

    Azure Cognitive Search API 설명서에는 간단한 *호텔* 인덱스를 포함 하는 코드 예제가 포함 되어 있습니다. 아래 스크린샷에서는 인덱스 정의 중에 지정된 프랑스어 분석기를 포함한 인덱스 정의를 확인할 수 있으며, 포털에서 연습으로 다시 만들 수 있습니다.

    ![호텔 데모 인덱스](media/search-create-index-portal/field-definitions.png "호텔 데모 인덱스")

1. 완료되면 **만들기**를 클릭하여 인덱스를 저장하고 만듭니다.

<a name="design"></a>

## <a name="set-attributes"></a>특성 설정

언제든지 새 필드를 추가할 수 있지만 기존 필드 정의는 인덱스 수명 동안 잠겨 있습니다. 이러한 이유로 개발자는 일반적으로 포털을 사용하여 간단한 인덱스를 만들거나 아이디어를 테스트하거나 포털 페이지를 사용하여 설정을 조회합니다. 인덱스를 쉽게 다시 작성할 수 있도록 코드 기반 접근 방식을 따르는 경우 인덱스 대한 빈번한 반복 디자인이 보다 효율적입니다.

인덱스를 저장하기 전에 분석기 및 확인기를 필드와 연결합니다. 만드는 동안 인덱스 정의에 언어 분석기 또는 제안기를 추가해야 합니다.

문자열 필드는 보통 **검색 가능** 및 **조회 가능**으로 표시됩니다. 검색 결과 범위를 좁히는 데 사용되는 필드는 **정렬 가능**, **필터링 가능** 및 **패싯 가능**이 있습니다.

필드 특성에 따라 필드가 사용되는 방식(즉, 전체 텍스트 검색, 패싯 탐색, 정렬 작업 등)이 결정됩니다. 아래 표에서 각 특성에 대해 설명합니다.

|특성|설명|  
|---------------|-----------------|  
|**검색 가능**|전체 텍스트 검색 가능하며, 인덱싱 중에 단어 분리 등의 어휘 분석이 적용됩니다. 검색 가능 필드를 “sunny day” 등의 값으로 설정하면 내부적으로 해당 필드가 개별 토큰 “sunny”와 “day”로 분할됩니다. 자세한 내용은 [전체 텍스트 검색 작동 방식](search-lucene-query-architecture.md)을 참조하세요.|  
|**필터링 가능**|**$filter** 쿼리에서 참조됩니다. 형식이 `Edm.String` 또는 `Collection(Edm.String)`인 필터링 가능 필드의 경우 단어 분리가 수행되지 않으므로 정확하게 일치하는 항목만 비교합니다. 예를 들어 이러한 필드 f를 “sunny day”로 설정하면 `$filter=f eq 'sunny'`에서는 일치하는 항목이 발견되지 않지만 `$filter=f eq 'sunny day'`에서는 일치하는 항목이 발견됩니다. |  
|**정렬 가능**|기본적으로 시스템은 점수에 따라 결과를 정렬하지만 문서에 있는 필드를 기반으로 정렬하도록 구성할 수 있습니다. 형식이 `Collection(Edm.String)`인 필드는 **정렬 가능**으로 설정할 수 없습니다. |  
|**패싯 가능**|일반적으로 카테고리별 적중 횟수가 포함된 검색 결과를 표시하는 데 사용됩니다(예: 특정 도시의 호텔). 형식이 `Edm.GeographyPoint`인 필드에는 이 옵션을 사용할 수 없습니다. `Edm.String`필터링 가능 **,** 정렬 가능**또는**패싯 가능**이고 형식이** 인 필드는 최대 32KB일 수 있습니다. 자세한 내용은 [인덱스 만들기(REST API)](https://docs.microsoft.com/rest/api/searchservice/create-index)를 참조하세요.|  
|**key**|인덱스 내의 문서에 대한 고유 식별자입니다. 정확히 하나의 필드를 키 필드로 선택해야 하며 해당 필드의 형식은 `Edm.String`이어야 합니다.|  
|**조회 가능**|검색 결과에서 필드를 반환할 수 있는지 여부를 결정합니다. *이익률* 등의 필드를 필터, 정렬 또는 점수 매기기 메커니즘으로 사용하며 최종 사용자에게는 필드를 표시하지 않으려는 경우 이 기능을 사용하면 유용합니다. `true` for `key` 로 설정해야 합니다.|  

## <a name="next-steps"></a>다음 단계

Azure Cognitive Search 인덱스를 만든 후 다음 단계로 이동할 수 있습니다. [검색 가능한 데이터를 인덱스에 업로드](search-what-is-data-import.md)합니다.

또는 [인덱스를 보다 자세히 살펴볼](search-what-is-an-index.md) 수도 있습니다. 필드 컬렉션 외에도 인덱스는 분석기, 확인기, 점수 매기기 프로필 및 CORS 설정도 지정합니다. 포털은 필드, 분석기 및 확인기와 같은 가장 일반적인 요소를 정의하기 위한 탭 페이지를 제공합니다. 다른 요소를 만들거나 수정하려면 REST API 또는 .NET SDK를 만들면 됩니다.

## <a name="see-also"></a>참고 항목:

 [전체 텍스트 검색 작동 방식](search-lucene-query-architecture.md)  
 [Search 서비스 REST API](https://docs.microsoft.com/rest/api/searchservice/) [.NET SDK](https://docs.microsoft.com/dotnet/api/overview/azure/search?view=azure-dotnet)

