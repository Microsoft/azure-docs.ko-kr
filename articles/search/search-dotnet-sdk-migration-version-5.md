---
title: Azure Search .NET SDK 버전 5로 업그레이드 - Azure Search
description: 이전 버전에서 Azure Search .NET SDK 버전 5로 코드를 마이그레이션합니다. 새로운 기능과 필요한 코드 변경 내용을 알아봅니다.
author: brjohnstmsft
manager: jlembicz
services: search
ms.service: search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 01/24/2019
ms.author: brjohnst
ms.custom: seodec2018
ms.openlocfilehash: d7684aa79ac9f58c2a047b01a6d9f5263795221d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61291710"
---
# <a name="upgrading-to-the-azure-search-net-sdk-version-5"></a>Azure Search .NET SDK 버전 5로 업그레이드
버전 4.0-preview 또는 이전 버전의 [Azure Search .NET SDK](https://aka.ms/search-sdk)를 사용하는 경우 이 문서를 참조하여 버전 5로 애플리케이션을 업그레이드할 수 있습니다.

예제를 비롯하여 SDK에 대한 보다 일반적인 연습은 [.NET 애플리케이션에서 Azure Search를 사용하는 방법](search-howto-dotnet-sdk.md)을 참조하세요.

Azure Search .NET SDK 버전 5에는 이전 버전에서 변경된 사항이 일부 포함되어 있습니다. 대부분 소소한 변경이므로 코드를 변경하는 데 최소한의 작업만 필요합니다. 새 SDK 버전을 사용하는 코드를 변경하는 방법에 대한 지침은 [업그레이드 단계](#UpgradeSteps) 를 참조하세요.

> [!NOTE]
> 2.0-preview 또는 이전 버전을 사용하는 경우 먼저 버전 3으로 업그레이드한 후 버전 5로 업그레이드해야 합니다. 지침은 [Azure Search .NET SDK 버전 3으로 업그레이드](search-dotnet-sdk-migration.md)를 참조하세요.
>
> Azure Search 서비스 인스턴스는 최신 버전을 포함한 여러 REST API 버전을 지원합니다. 더 이상 최신 버전이 아닌 버전을 계속 사용할 수는 있지만 코드를 마이그레이션하여 최신 버전을 사용하는 것이 좋습니다. REST API를 사용하는 경우 api-version 매개 변수를 통해 모든 요청에 API 버전을 지정해야 합니다. .NET SDK를 사용하는 경우 사용 중인 SDK의 버전에 따라 해당하는 REST API 버전이 결정됩니다. 이전 버전의 SDK를 사용하는 경우 최신 API 버전을 지원하기 위해 서비스가 업그레이드된 경우에도 변경 내용 없이 해당 코드를 계속 실행할 수 있습니다.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-5"></a>버전 5의 새로운 기능
Azure Search .NET SDK 버전 5는 Azure Search REST API의 최신 일반 공급 버전, 특히 2017-11-11을 대상으로 합니다. 이 버전이 있으면 다음을 비롯한 Azure Search의 새 기능을 .NET 애플리케이션에서 사용할 수 있습니다.

* [동의어](search-synonyms.md)
* 이제 프로그래밍 방식으로 인덱서 실행 기록에서 경고에 액세스할 수 있습니다. 자세한 내용은 [.NET 참조](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexerexecutionresult?view=azure-dotnet)에서 `IndexerExecutionResult`의 `Warning` 속성을 참조하세요.
* .NET Core 2 지원
* 새 패키지 구조는 필요한 SDK 부분만 사용하도록 지원합니다. 자세한 내용은 [버전 5의 주요 변경 내용](#ListOfChanges)을 참조하세요.

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>업그레이드 단계
먼저, NuGet 패키지 관리자 콘솔을 사용하거나 Visual Studio에서 프로젝트 참조를 마우스 오른쪽 단추로 클릭하고 "NuGet 패키지 관리..."를 선택하여 `Microsoft.Azure.Search` 에 대한 NuGet 참조를 업데이트합니다.

NuGet에서 새 패키지와 해당 종속성을 다운로드했으면 프로젝트를 다시 빌드합니다. 코드가 구조화된 방식에 따라 다시 빌드할 수 있습니다. 다시 빌드할 수 있으면 배포할 준비가 된 것입니다.

빌드가 실패하면 다음과 같은 빌드 오류가 표시됩니다.

    The name 'SuggesterSearchMode' does not exist in the current context

다음은 이러한 빌드 오류를 수정하는 단계입니다. 오류의 원인 및 해결 방법에 대한 자세한 내용은 [버전 5의 주요 변경 내용](#ListOfChanges)을 참조하세요.

Azure Search .NET SDK의 패키징이 변경되었으므로 버전 5를 사용하려면 애플리케이션을 다시 빌드해야 합니다. 이러한 변경 사항은 [버전 5의 주요 변경 내용](#ListOfChanges)에서 자세히 설명합니다.

사용되지 않은 메서드 또는 속성과 관련된 추가 빌드 경고가 표시될 수 있습니다. 경고에는 사용되지 않는 기능 대신 사용해야 하는 항목에 대한 지침이 포함됩니다. 예를 들어, 애플리케이션이 `IndexingParametersExtensions.DoNotFailOnUnsupportedContentType` 메서드를 사용하는 경우 “This behavior is now enabled by default, so calling this method is no longer necessary.”(이 동작은 기본적으로 사용되므로 더 이상 이 메서드를 호출할 필요가 없습니다.)라는 경고가 표시됩니다.

모든 빌드 오류 또는 경고가 수정되면 원하는 경우 새로운 기능을 활용하도록 애플리케이션을 변경할 수 있습니다. SDK의 새로운 기능에 대한 자세한 내용은 [버전 5의 새로운 기능](#WhatsNew)에 나와 있습니다.

<a name="ListOfChanges"></a>

## <a name="breaking-changes-in-version-5"></a>버전 5의 주요 변경 내용

### <a name="new-package-structure"></a>새 패키지 구조

버전 5에서 가장 크게 달라진 내용은 `Microsoft.Azure.Search` 어셈블리와 해당 내용이 4개의 개별 어셈블리로 4개의 독립된 NuGet 패키지로 배포된다는 점입니다.

 - `Microsoft.Azure.Search`: 다른 모든 Azure Search 패키지를 종속 항목으로 포함하는 메타 패키지입니다. 이전 버전의 SDK에서 업그레이드하는 경우 이 패키지를 업그레이드하고 다시 빌드하기만 하면 새 버전을 사용할 수 있습니다.
 - `Microsoft.Azure.Search.Data`: Azure Search를 사용하여 .NET 애플리케이션을 개발하는 경우 이 패키지를 사용합니다. 인덱스에서 문서를 쿼리하거나 업데이트하기만 하면 됩니다. 인덱스, 동의어 맵 또는 서비스 수준의 다른 리소스도 만들거나 업데이트해야 하는 경우에는 대신 `Microsoft.Azure.Search` 패키지를 사용합니다.
 - `Microsoft.Azure.Search.Service`: Azure Search 인덱스, 동의어 맵, 인덱서, 데이터 원본 또는 서비스 수준의 다른 리소스를 관리하기 위해 .NET에서 자동화를 개발하는 경우 이 패키지를 사용합니다. 인덱스에서 문서를 쿼리하거나 업데이트하기만 하면 되는 경우에는 대신 `Microsoft.Azure.Search.Data` 패키지를 사용합니다. Azure Search의 모든 기능이 필요한 경우에는 대신 `Microsoft.Azure.Search` 패키지를 사용합니다.
 - `Microsoft.Azure.Search.Common`: Azure Search .NET 라이브러리에 필요한 일반 형식입니다. 애플리케이션에서 직접 이 패키지를 사용할 필요는 없습니다. 종속 항목으로만 사용하는 용도입니다.
 
이 변경은 많은 유형이 어셈블리 간에 이동되었으므로 기술적으로 큰 변화입니다. 바로 이런 이유 때문에 SDK 버전 5로 업그레이드하려면 애플리케이션을 다시 빌드해야 합니다.

애플리케이션을 다시 빌드하는 것 외에도, 버전 5에는 코드 변경이 필요할 수 있는 몇 가지 주요 변경 내용이 있습니다.

### <a name="change-to-suggesters"></a>제안기로 변경 

`Suggester` 생성자에는 더 이상 `SuggesterSearchMode`에 대한 `enum` 매개 변수가 없습니다. 이 열거형에는 하나의 값만 있으므로 중복되었습니다. 이로 인해 빌드 오류가 표시되면 `SuggesterSearchMode` 매개 변수에 대한 참조만 제거하면 됩니다.

### <a name="removed-obsolete-members"></a>더 이상 사용되지 않는 멤버가 제거되었습니다.

이전 버전에서 사용되지 않는 것으로 표시되고 이후에 버전 5에서 제거된 메서드 및 속성 관련 빌드 오류가 확인될 수 있습니다. 이러한 오류가 발생하는 경우 해결 방법은 다음과 같습니다.

- `IndexingParametersExtensions.IndexStorageMetadataOnly` 메서드를 사용하는 경우에는 대신 `SetBlobExtractionMode(BlobExtractionMode.StorageMetadata)`를 사용합니다.
- `IndexingParametersExtensions.SkipContent` 메서드를 사용하는 경우에는 대신 `SetBlobExtractionMode(BlobExtractionMode.AllMetadata)`를 사용합니다.

### <a name="removed-preview-features"></a>제거된 미리 보기 기능

버전 4.0-preview에서 버전 5로 업그레이드하는 경우 Blob 인덱서의 JSON 배열 및 CSV 구문 분석 지원은 미리 보기에는 계속 포함되지만 버전 5에서는 제거됩니다. 특히, `IndexingParametersExtensions` 클래스의 다음 메서드가 제거되었습니다.

- `ParseJsonArrays`
- `ParseDelimitedTextFiles`

애플리케이션이 이러한 기능에 강력하게 종속될 경우 Azure Search .NET SDK 버전 5로 업그레이드하지 못할 수 있습니다. 버전 4.0-preview는 계속 사용할 수 있습니다. 하지만 **프로덕션 애플리케이션에서는 미리 보기 SDK를 사용하지 않는 것이 좋습니다**. 미리 보기 기능은 평가용일 뿐이며 변경될 수 있습니다.

## <a name="conclusion"></a>결론
Azure Search .NET SDK 사용에 대한 자세한 내용은 [.NET 방법](search-howto-dotnet-sdk.md)을 참조하세요.

SDK에 대한 귀하의 피드백을 환영합니다! 문제가 발생하면 [Azure Search MSDN 포럼](https://social.msdn.microsoft.com/Forums/azure/home?forum=azuresearch)을 통해 자유롭게 도움을 요청하세요. 버그를 발견하는 경우 [Azure .NET SDK GitHub 리포지토리](https://github.com/Azure/azure-sdk-for-net/issues)에 문제를 제출할 수 있습니다. 문제 제목에 "[Azure Search]"라는 접두사를 지정해야 합니다.

Azure Search를 이용해 주셔서 감사합니다!
