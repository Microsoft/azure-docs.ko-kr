---
title: .NET SDK 버전 10으로 업그레이드
titleSuffix: Azure Cognitive Search
description: 이전 버전에서 Azure Cognitive Search .NET SDK 버전 10으로 코드를 마이그레이션합니다. 새로운 기능과 필요한 코드 변경 내용을 알아봅니다.
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 11/04/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: bfe24ff38446fa0d0ccea96799e6f42b561713bf
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89002813"
---
# <a name="upgrade-to-azure-cognitive-search-net-sdk-version-10"></a>Azure Cognitive Search .NET SDK 버전 10으로 업그레이드

버전 9.0 또는 이전 버전의 [.NET SDK](/dotnet/api/overview/azure/search)를 사용 하는 경우이 문서를 참조 하 여 버전 10을 사용 하도록 응용 프로그램을 업그레이드할 수 있습니다.

Azure Search은 버전 10에서 Azure Cognitive Search로 이름이 변경 되지만 네임 스페이스와 패키지 이름은 변경 되지 않습니다. 이전 버전의 SDK (9.0 및 이전 버전)는 계속 해 서 이전 이름을 사용 합니다. 예제를 포함 하 여 SDK를 사용 하는 방법에 대 한 자세한 내용은 [.Net 응용 프로그램에서 Azure Cognitive Search를 사용 하는 방법](search-howto-dotnet-sdk.md)을 참조 하세요.

버전 10은 여러 기능과 버그 수정을 추가 하 여 REST API 버전과 동일한 기능 수준으로 제공 `2019-05-06` 합니다. 변경이 기존 코드를 중단 하는 경우 [문제를 해결 하는 데 필요한 단계](#UpgradeSteps)를 안내 합니다.

> [!NOTE]
> 버전 8.0-preview 또는 이전 버전을 사용 하는 경우 먼저 버전 9로 업그레이드 한 후 버전 10으로 업그레이드 해야 합니다. 지침은 [Azure Search .NET SDK 버전 9로 업그레이드를](search-dotnet-sdk-migration-version-9.md) 참조 하세요.
>
> 검색 서비스 인스턴스는 최신 버전을 포함 하 여 여러 REST API 버전을 지원 합니다. 더 이상 최신 버전이 아닌 버전을 계속 사용할 수는 있지만 코드를 마이그레이션하여 최신 버전을 사용하는 것이 좋습니다. REST API를 사용하는 경우 api-version 매개 변수를 통해 모든 요청에 API 버전을 지정해야 합니다. .NET SDK를 사용하는 경우 사용 중인 SDK의 버전에 따라 해당하는 REST API 버전이 결정됩니다. 이전 버전의 SDK를 사용하는 경우 최신 API 버전을 지원하기 위해 서비스가 업그레이드된 경우에도 변경 내용 없이 해당 코드를 계속 실행할 수 있습니다.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-10"></a>버전 10의 새로운 기능
Azure Cognitive Search .NET SDK의 버전 10은 `2019-05-06` 다음 업데이트를 REST API 합니다.

* 두 가지 새로운 기술- [조건부 기술](cognitive-search-skill-conditional.md) 및 [텍스트 번역](cognitive-search-skill-text-translation.md)기술을 소개 합니다.
* [Shaper 기술](cognitive-search-skill-shaper.md) 입력은 중첩 된 컨텍스트에서 통합을 수용할 수 있도록 재구성 되었습니다. 자세한 내용은이 [예제 JSON 정의](./cognitive-search-skill-shaper.md#scenario-3-input-consolidation-from-nested-contexts)를 참조 하세요.
* 새 [필드 매핑 함수](search-indexer-field-mappings.md)두 개 추가:
    - [urlEncode](./search-indexer-field-mappings.md#urlencode-function)
    - [urlDecode](./search-indexer-field-mappings.md#urldecode-function)
* 경우에 따라 [인덱서 실행 상태](/rest/api/searchservice/get-indexer-status) 에 표시 되는 오류 및 경고에는 디버깅에 도움이 되는 추가 정보가 있을 수 있습니다. `IndexerExecutionResult` 이이 동작을 반영 하도록 업데이트 되었습니다.
* [기술](cognitive-search-defining-skillset.md) 내에 정의 된 개별 기술은 속성을 지정 하 여 선택적으로 식별할 수 있습니다 `name` .
* `ServiceLimits`[복합 형식의](./search-howto-complex-data-types.md) 제한을 보여 주고 `IndexerExecutionInfo` 관련 인덱서 제한/할당량을 표시 합니다.

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>업그레이드 단계

1. `Microsoft.Azure.Search`Nuget 패키지 관리자 콘솔을 사용 하거나 프로젝트 참조를 마우스 오른쪽 단추로 클릭 하 고 "Nuget 패키지 관리 ..."를 선택 하 여에 대 한 nuget 참조를 업데이트 합니다. Visual Studio에서

2. NuGet에서 새 패키지와 해당 종속성을 다운로드했으면 프로젝트를 다시 빌드합니다. 

3. 빌드에 실패 하는 경우 각 빌드 오류를 수정 해야 합니다. 잠재적 빌드 오류를 해결 하는 방법에 대 한 자세한 내용은 [버전 10의 주요 변경 내용](#ListOfChanges) 을 참조 하세요.

4. 모든 빌드 오류 또는 경고가 수정되면 원하는 경우 새로운 기능을 활용하도록 애플리케이션을 변경할 수 있습니다. SDK의 새로운 기능은 [버전 10의 새로운](#WhatsNew)기능에 자세히 설명 되어 있습니다.

<a name="ListOfChanges"></a>

## <a name="breaking-changes-in-version-10"></a>버전 10의 주요 변경 내용

버전 10에서는 응용 프로그램을 다시 빌드하는 것 외에도 코드를 변경 해야 할 수 있는 몇 가지 주요 변경 사항이 있습니다.

> [!NOTE]
> 아래 변경 목록은 완전 하지 않습니다. 일부 변경 내용은 빌드 오류가 발생 하지 않을 수 있지만, 이전 버전의 Azure Cognitive Search .NET SDK 어셈블리에 종속 된 어셈블리와 이진 호환성이 중단 되기 때문에 기술적으로 중단 됩니다. 이 범주에 속하는 중요 한 변경 내용도 권장 사항과 함께 나열 됩니다. 이진 호환성 문제를 방지 하려면 버전 10으로 업그레이드할 때 응용 프로그램을 다시 빌드 하세요.

### <a name="custom-web-api-skill-definition"></a>사용자 지정 Web API 기술 정의

[사용자 지정 WEB API 기술](cognitive-search-custom-skill-web-api.md) 정의가 버전 9 이상에서 잘못 지정 되었습니다. 

`WebApiSkill` `HttpHeaders` 사전을 _포함_ 하는 개체 속성으로 지정 된의 모델입니다. 이 방식으로 생성 된를 사용 하 여 기술를 만들면 `WebApiSkill` REST API는 요청이 잘못 된 형식으로 간주 되기 때문에 예외가 발생 합니다. 이 문제는 `HttpHeaders` 모델 자체에 대 한 **최상위 사전 속성** `WebApiSkill` (REST API의 유효한 요청으로 간주 됨)을 만들어 수정 되었습니다.

예를 들어 이전에를 다음과 같이 인스턴스화하려고 시도한 경우 `WebApiSkill`

```csharp

var webApiSkill = new WebApiSkill(
            inputs, 
            outputs,
            uri: "https://contoso.example.org")
{
    HttpHeaders = new WebApiHttpHeaders()
    {
        Headers = new Dictionary<string, string>()
        {
            ["header"] = "value"
        }
    }
};

```

REST API에서 유효성 검사 오류를 방지 하려면 다음과 같이 변경 합니다.

```csharp

var webApiSkill = new WebApiSkill(
            inputs, 
            outputs,
            uri: "https://contoso.example.org")
{
    HttpHeaders = new Dictionary<string, string>()
    {
        ["header"] = "value"
    }
};

```

## <a name="shaper-skill-allows-nested-context-consolidation"></a>Shaper 스킬은 중첩 된 컨텍스트 통합을 허용 합니다.

Shaper 스킬은 이제 중첩 된 컨텍스트에서 입력 통합을 허용할 수 있습니다. 이 변경을 사용 하려면 `InputFieldMappingEntry` `Source` 속성만 지정 하거나 및 속성을 모두 지정 하 여 인스턴스화할 수 있도록 수정 했습니다 `SourceContext` `Inputs` .

코드를 변경 하지 않아도 되는 경우가 많습니다. 그러나 이러한 두 조합 중 하나만 사용할 수 있습니다. 이는 다음을 의미합니다.

- 만 초기화 되는를 만들 수 `InputFieldMappingEntry` `Source` 있습니다.
- 및가 초기화 되는를 만드는 `InputFieldMappingEntry` `SourceContext` `Inputs` 것은 유효 합니다.
- 이러한 세 가지 속성을 포함 하는 다른 모든 조합은 유효 하지 않습니다.

이 새로운 기능을 사용 하려면 먼저 모든 클라이언트를 업데이트 하 여 해당 변경 내용을 롤아웃하기 전에 먼저 버전 10을 사용 하도록 해야 합니다. 그렇지 않으면 이전 버전의 SDK를 사용 하 여 클라이언트에서 Shaper 기술에 업데이트 하는 경우 유효성 검사 오류가 발생할 수 있습니다.

> [!NOTE]
> 기본 `InputFieldMappingEntry` 모델이 중첩 된 컨텍스트에서 통합을 허용 하도록 수정 된 경우에도 사용은 Shaper 기술 정의 내 에서만 유효 합니다. 다른 기술에서이 기능을 사용 하는 경우 컴파일 시간에 유효한 반면 런타임에 유효성 검사 오류가 발생 합니다.

## <a name="skills-can-be-identified-by-a-name"></a>기술은 이름으로 식별할 수 있습니다.

이제 기술 내의 각 기술에는 새 속성이 있습니다 .이 속성은 `Name` 코드에서 초기화 하 여 기술 식별에 도움이 됩니다. 이 옵션은 선택 사항입니다. 지정 하지 않으면 (명시적 코드가 변경 되지 않은 경우 기본값) 기술의 기술에 대 한 1 기반 인덱스를 사용 하 여 기본 이름이 지정 되 고 접두사는 ' # ' 문자로 지정 됩니다. 예를 들어 다음 기술 정의에서 (간단히 하기 위해 대부분의 초기화를 건너뛰었습니다.)

```csharp
var skillset = new Skillset()
{
    Skills = new List<Skill>()
    {
        new SentimentSkill(),
        new WebApiSkill(),
        new ShaperSkill(),
        ...
    }
}
```

`SentimentSkill` 에는 이름, 할당 됨 `#1` `WebApiSkill` `#2` , `ShaperSkill` 할당 `#3` 됨 등이 할당 됩니다.

사용자 지정 이름으로 기술을 식별 하도록 선택 하는 경우 먼저 클라이언트의 모든 인스턴스를 SDK 버전 10으로 업데이트 해야 합니다. 그렇지 않으면 이전 버전의 SDK를 사용 하는 클라이언트가 기술에 대 한 속성을 사용 하 여 `null` `Name` 클라이언트가 기본 이름 지정 체계를 대체 하도록 할 가능성이 있습니다.

## <a name="details-about-errors-and-warnings"></a>오류 및 경고에 대 한 세부 정보

`ItemError` 인덱서를 실행 하는 `ItemWarning` 동안 발생 하는 오류 및 경고에 대 한 세부 정보를 캡슐화 하는 및 모델은 인덱서 디버깅에 도움이 되는 목표가 포함 된 세 개의 새로운 속성을 포함 하도록 수정 되었습니다. 이러한 속성은 다음과 같습니다.

- `Name`: 오류가 발생 한 원본의 이름입니다. 예를 들어 연결 된 기술의 특정 기술을 참조할 수 있습니다.
- `Details`: 오류 또는 경고에 대 한 추가 세부 정보입니다.
- `DocumentationLink`: 특정 오류 또는 경고에 대 한 문제 해결 가이드에 대 한 링크입니다.

> [!NOTE]
> 가능 하면 이러한 유용한 세부 정보를 포함 하는 오류 및 경고를 구조화 하기 시작 했습니다. 모든 오류 및 경고에 대 한 세부 정보가 표시 되는 것을 확인 하기 위해 노력 하 고 있습니다 .이 작업은 진행 중 이며, 이러한 추가 세부 정보는 항상 채워지지 않을 수 있습니다.

## <a name="next-steps"></a>다음 단계

- Shaper 기술에 대 한 변경 내용은 새 코드나 기존 코드에 대 한 가능성이 가장 높습니다. 다음 단계로 입력 구조를 설명 하는이 예제를 다시 방문 해야 합니다. [Shaper 기술 JSON 정의 예제](cognitive-search-skill-shaper.md)
- [AI 보강 개요](cognitive-search-concept-intro.md)를 참조 하세요.
- SDK에 대한 귀하의 피드백을 환영합니다! 문제가 발생 하는 경우 [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-search)에 대 한 도움을 요청 하세요. 버그를 발견하는 경우 [Azure .NET SDK GitHub 리포지토리](https://github.com/Azure/azure-sdk-for-net/issues)에 문제를 제출할 수 있습니다. 문제 제목에 "[Azure Cognitive Search]" 라는 접두사를 지정 해야 합니다.