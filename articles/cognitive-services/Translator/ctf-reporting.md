---
title: CTF (공동 변환 프레임 워크) 보고-번역기
titleSuffix: Azure Cognitive Services
description: CTF(Collaborative Translation Framework) 보고 사용 방법입니다.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 12/14/2017
ms.author: swmachan
ms.custom: devx-track-csharp
ms.openlocfilehash: 68b0de40940fa75dd4eb4e1572405f31ce1c22b8
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88934381"
---
# <a name="how-to-use-collaborative-translation-framework-ctf-reporting"></a>CTF(Collaborative Translation Framework) 보고 사용 방법

> [!NOTE]
> 이 메서드는 더 이상 사용되지 않습니다. 이 파일은 변환기의 V 3.0에서 사용할 수 없습니다.
> 
> 이전에 변환기의 V 2.0에서 사용할 수 있는 CTF (공동 작업 번역 프레임 워크)는 2018 년 2 월 1 일부 터 사용 되지 않습니다. AddTranslation 및 AddTranslationArray 함수를 사용하면 사용자가 Collaborative Translation Framework를 통해 수정할 수 있습니다. 2018년 1월 31일 이후에는 이러한 두 함수가 새 문장 제출을 허용하지 않고 사용자에게 오류 메시지가 표시됩니다. 이러한 함수는 사용 중지 되었으며 대체 되지 않습니다.

Collaborative(Collaborative Translation Framework) 보고 API는 CTF 스토어의 통계 및 실제 콘텐츠를 반환합니다. 이 API는 다음과 같은 점에서 GetTranslations() 메서드와 다릅니다.
* 계정(appId 또는 Azure Marketplace 계정)에서 번역된 콘텐츠와 해당 총수만 반환합니다.
* 원본 문장과 일치하지 않아도 번역된 콘텐츠와 해당 총수를 반환합니다.
* 자동 번역(기계 번역)을 반환하지 않습니다.

## <a name="endpoint"></a>엔드포인트
CTF 보고 API의 끝점은 https://api.microsofttranslator.com/v2/beta/ctfreporting.svc 입니다.

## <a name="methods"></a>메서드
| 속성 | Description|
|:---|:---|
| GetUserTranslationCounts 메서드 | 사용자가 만든 번역 개수를 가져옵니다. |
| GetUserTranslations 메서드 | 사용자가 만든 번역을 검색합니다. |

이러한 메서드를 사용하면 다음을 수행할 수 있습니다.
* 계정 ID 아래에서 다운로드할 사용자 번역 및 수정 사항의 전체 집합을 검색합니다.
* 빈번한 참가자 목록을 가져옵니다. AddTranslation()에 올바른 사용자 이름이 제공되었는지 확인합니다.
* 신뢰할 수 있는 사용자가 필요한 경우 URI 접두사를 기준으로 사이트의 특정 부분으로 제한해서 사용 가능한 모든 후보를 볼 수 있는 UI(사용자 인터페이스)를 빌드합니다.

> [!NOTE]
> 두 메서드 모두 비교적 느리고 비용이 많이 들기 때문에 신중하게 사용하는 것이 좋습니다.

## <a name="getusertranslationcounts-method"></a>GetUserTranslationCounts 메서드

이 메서드는 사용자가 만든 번역 개수를 가져옵니다. uriPrefix, from, to, user, minRating, maxRating 요청 매개 변수로 그룹화된 번역 개수 목록을 제공합니다.

**구문**

> [!div class="tabbedCodeSnippets"]
> ```cs
> UserTranslationCount[]GetUserTranslationCounts(
>            string appId,
>            string uriPrefix,
>            string from,
>            string to,
>            int? minRating,
>            int? maxRating,
>            string user,
>            string category
>            DateTime? minDateUtc,
>            DateTime? maxDateUtc,
>            int? skip,
>            int? take);
> ```

**매개 변수**

| 매개 변수 | Description |
|:---|:---|
| appId | **필수** 인증 헤더를 사용하는 경우 appid 필드를 비워 두고, 사용하지 않는 경우 "Bearer" + " " + 액세스 토큰을 포함하는 문자열을 지정합니다.|
| uriPrefix | **선택 사항** 번역의 URI 접두사를 포함하는 문자열입니다.|
| 원본 | **선택 사항** 번역 텍스트의 언어 코드를 나타내는 문자열입니다. |
| 을 | **선택 사항** 텍스트를 번역할 언어 코드를 나타내는 문자열입니다.|
| minRating| **선택 사항** 번역된 텍스트의 최소 품질 등급을 나타내는 정수 값입니다. 유효한 값은 -10에서 10 사이입니다. 기본값은 1입니다.|
| maxRating| **선택 사항** 번역된 텍스트의 최대 품질 등급을 나타내는 정수 값입니다. 유효한 값은 -10에서 10 사이입니다. 기본값은 1입니다.|
| 사용자 | **선택 사항** 제출을 보낸 사람에 따라 결과를 필터링하는 데 사용되는 문자열입니다. |
| category| **선택 사항** 번역의 범주 또는 도메인을 포함하는 문자열입니다. 이 매개 변수는 기본 옵션인 general만 지원합니다.|
| minDateUtc| **선택 사항** 번역을 검색하려는 시작 날짜입니다. 날짜는 UTC 형식이어야 합니다. |
| maxDateUtc| **선택 사항** 번역을 검색하려는 종료 날짜입니다. 날짜는 UTC 형식이어야 합니다. |
| skip| **선택 사항** 페이지에서 건너뛸 결과 수입니다. 예를 들어 결과의 처음 20개 행을 건너뛰고 21번째 결과 레코드부터 보려면 이 매개 변수에 20을 지정합니다. 이 매개 변수의 기본값은 0입니다.|
| take | **선택 사항** 검색할 결과 수입니다. 각 요청의 최대 개수는 100개입니다. 기본값은 100입니다.|

> [!NOTE]
> skip 및 take 요청 매개 변수를 사용하면 결과 레코드 수가 많을 경우 페이지 매김이 사용됩니다.

**반환 값**

결과 집합에는 **UserTranslationCount** 배열이 포함됩니다. 각 UserTranslationCount에는 다음 요소가 있습니다.

| 필드 | Description |
|:---|:---|
| 개수| 검색된 결과 수입니다.|
| From | 원본 언어입니다.|
| 등급| AddTranslation() 메서드 호출에서 제출자가 적용한 등급입니다.|
| 작업| 대상 언어입니다.|
| URI| AddTranslation() 메서드 호출에서 적용된 URI입니다.|
| 사용자| 사용자 이름입니다.|

**예외**

| 예외 | 메시지 | 조건 |
|:---|:---|:---|
| ArgumentOutOfRangeException | ‘**maxDateUtc**’ 매개 변수는 ‘**minDateUtc**’보다 크거나 같아야 합니다.| **maxDateUtc** 매개 변수의 값은 **minDateUtc** 매개 변수의 값보다 작습니다.|
| TranslateApiException | IP가 할당량을 초과했습니다.| <ul><li>분당 요청 수의 한도에 도달했습니다.</li><li>요청 크기는 계속해서 10000자로 제한됩니다.</li><li>매시간 및 매일 할당량은 변환기에서 허용 하는 문자 수를 제한 합니다.</li></ul>|
| TranslateApiException | AppId가 할당량을 초과했습니다.| 애플리케이션 ID가 시간별 또는 일별 할당량을 초과했습니다.|

> [!NOTE]
> 서비스의 모든 사용자에게 공정하도록 할당량이 조정됩니다.

**GitHub에서 코드 예제 보기**
* [C#](https://github.com/MicrosoftTranslator/CustomTranslator-API-CSharp)
* [PHP](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-PHP)

## <a name="getusertranslations-method"></a>GetUserTranslations 메서드

이 메서드는 사용자가 만든 번역을 검색합니다. uriPrefix, from, to, user, minRating 및 maxRating 요청 매개 변수로 그룹화된 번역을 제공합니다.

**구문**

> [!div class="tabbedCodeSnippets"]
> ```cs
> UserTranslation[] GetUserTranslations (
>             string appId,
>             string uriPrefix,
>             string from,
>             string to,
>             int? minRating,
>             int? maxRating,
>             string user,
>             string category
>             DateTime? minDateUtc,
>             DateTime? maxDateUtc,
>             int? skip,
>             int? take);
> ```

**매개 변수**

| 매개 변수 | Description |
|:---|:---|
| appId | **필수** 인증 헤더를 사용하는 경우 appid 필드를 비워 두고, 사용하지 않는 경우 "Bearer" + " " + 액세스 토큰을 포함하는 문자열을 지정합니다.|
| uriPrefix| **선택 사항** 번역의 URI 접두사를 포함하는 문자열입니다.|
| 원본| **선택 사항** 번역 텍스트의 언어 코드를 나타내는 문자열입니다.|
| 을| **선택 사항** 텍스트를 번역할 언어 코드를 나타내는 문자열입니다.|
| minRating| **선택 사항** 번역된 텍스트의 최소 품질 등급을 나타내는 정수 값입니다. 유효한 값은 -10에서 10 사이입니다. 기본값은 1입니다.|
| maxRating| **선택 사항** 번역된 텍스트의 최대 품질 등급을 나타내는 정수 값입니다. 유효한 값은 -10에서 10 사이입니다. 기본값은 1입니다.|
| 사용자| **필드. 제출 작성자를 기준으로 결과를 필터링 하는 데 사용 되는 문자열입니다.**|
| category| **선택 사항** 번역의 범주 또는 도메인을 포함하는 문자열입니다. 이 매개 변수는 기본 옵션인 general만 지원합니다.|
| minDateUtc| **선택 사항** 번역을 검색하려는 시작 날짜입니다. 날짜는 UTC 형식이어야 합니다.|
| maxDateUtc| **선택 사항** 번역을 검색하려는 종료 날짜입니다. 날짜는 UTC 형식이어야 합니다.|
| skip| **선택 사항** 페이지에서 건너뛸 결과 수입니다. 예를 들어 결과의 처음 20개 행을 건너뛰고 21번째 결과 레코드부터 보려면 이 매개 변수에 20을 지정합니다. 이 매개 변수의 기본값은 0입니다.|
| take| **선택 사항** 검색할 결과 수입니다. 각 요청의 최대 개수는 100개입니다. 기본값은 50입니다.|

> [!NOTE]
> skip 및 take 요청 매개 변수를 사용하면 결과 레코드 수가 많을 경우 페이지 매김이 사용됩니다.

**반환 값**

결과 집합에는 **UserTranslation** 배열이 포함됩니다. 각 UserTranslation에는 다음 요소가 있습니다.

| 필드 | Description |
|:---|:---|
| CreatedDateUtc| AddTranslation()을 사용하여 항목을 만든 날짜입니다.|
| From| 원본 언어입니다.|
| OriginalText| 요청을 제출할 때 사용되는 원본 언어 텍스트입니다.|
|등급 |AddTranslation() 메서드 호출에서 제출자가 적용한 등급입니다.|
|작업|    대상 언어입니다.|
|TranslatedText|    AddTranslation() 메서드 호출에서 제출된 번역입니다.|
|URI|   AddTranslation() 메서드 호출에서 적용된 URI입니다.|
|사용자   |사용자 이름입니다.|

**예외**

| 예외 | 메시지 | 조건 |
|:---|:---|:---|
| ArgumentOutOfRangeException | ‘**maxDateUtc**’ 매개 변수는 ‘**minDateUtc**’보다 크거나 같아야 합니다.| **maxDateUtc** 매개 변수의 값은 **minDateUtc** 매개 변수의 값보다 작습니다.|
| TranslateApiException | IP가 할당량을 초과했습니다.| <ul><li>분당 요청 수의 한도에 도달했습니다.</li><li>요청 크기는 계속해서 10000자로 제한됩니다.</li><li>매시간 및 매일 할당량은 변환기에서 허용 하는 문자 수를 제한 합니다.</li></ul>|
| TranslateApiException | AppId가 할당량을 초과했습니다.| 애플리케이션 ID가 시간별 또는 일별 할당량을 초과했습니다.|

> [!NOTE]
> 서비스의 모든 사용자에게 공정하도록 할당량이 조정됩니다.

**GitHib에서 코드 예제 보기**
* [C#](https://github.com/MicrosoftTranslator/CustomTranslator-API-CSharp)
* [PHP](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-PHP)
