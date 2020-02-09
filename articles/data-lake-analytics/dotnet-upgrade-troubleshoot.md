---
title: .NET 4.7.2 업그레이드로 인 한 Azure Data Lake Analytics U-SQL 작업 오류 문제를 해결 하는 방법
description: .NET 4.7.2로 업그레이드 하 여 U-SQL 작업 오류 문제를 해결 합니다.
services: data-lake-analytics
author: guyhay
ms.author: guyhay
ms.reviewer: jasonwhowell
ms.service: data-lake-analytics
ms.topic: troubleshooting
ms.workload: big-data
ms.date: 10/11/2019
ms.openlocfilehash: 2be2f50558fef41659c9a3313871b17961f6ad6d
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/05/2019
ms.locfileid: "74873236"
---
# <a name="azure-data-lake-analytics-is-upgrading-to-the-net-framework-v472"></a>Azure Data Lake Analytics .NET Framework v 4.7.2로 업그레이드 하 고 있습니다.

Azure Data Lake Analytics 기본 런타임은 .NET Framework v 4.5.2에서 .NET Framework v 4.7.2로 업그레이드 하는 중입니다. 이러한 변경으로 인해 U-SQL 코드에서 사용자 지정 어셈블리를 사용 하 고 해당 사용자 지정 어셈블리가 .NET 라이브러리를 사용 하는 경우 주요 변경 사항이 발생할 위험이 있습니다.

.NET Framework 4.5.2에서 version 4.7.2로 업그레이드 하는 경우, 이제는 U SQL 런타임 (기본 런타임)에 배포 된 .NET Framework 항상 4.7.2 됩니다. .NET Framework 버전에 대한 side-by-side 옵션이 없습니다.

이 .NET 4.7.2 업그레이드를 완료 한 후에는 시스템의 관리 코드가 버전 4.7.2으로 실행 되 고, 사용자 지정 어셈블리와 같은 사용자 제공 라이브러리는 어셈블리가 생성 된 버전에 적합 한 이전 버전과 호환 되는 모드에서 실행 됩니다. 에 대한.

- 4\.5.2 버전에 대한 어셈블리 Dll이 생성 된 경우 배포 된 프레임 워크는이를 4.5.2 라이브러리로 처리 하 고 (몇 가지 예외 포함) 4.5.2 의미 체계를 제공 합니다.
- 이제 .NET Framework 4.7.2를 대상으로 하는 경우 버전 4.7.2 기능을 활용 하는 U-SQL 사용자 지정 어셈블리를 사용할 수 있습니다.

.NET 4.7.2로 업그레이드 하기 때문에 .NET 사용자 지정 어셈블리를 사용 하는 U-SQL 작업에 대한 주요 변경 내용이 발생할 수 있습니다. 아래 절차를 사용 하 여 이전 버전과의 호환성 문제를 확인 하는 것이 좋습니다.

## <a name="how-to-check-for-backwards-compatibility-issues"></a>이전 버전과의 호환성 문제를 확인 하는 방법

U-SQL 사용자 지정 어셈블리에서 .NET 코드에 대한 .NET 호환성 검사를 실행 하 여 이전 버전과의 호환성 문제가 발생할 수 있는지 확인 합니다.

> [!Note]
> 이 도구는 실제 주요 변경 내용을 검색 하지 않습니다. 특정 입력에 대해 (특정 입력의 경우) 문제를 일으킬 수 있는 .NET Api를 식별 합니다. 문제에 대한 알림이 표시 되는 경우에도 코드에 문제가 없을 수 있습니다. 자세한 내용은을 참조 하세요.

1. 다음 방법으로 .NET Dll에서 이전 버전과의 호환성 검사를 실행 합니다.
   1. [.Net 이식성 분석기 Visual Studio 확장](https://marketplace.visualstudio.com/items?itemName=ConnieYau.NETPortabilityAnalyzer) 에서 Visual studio 확장 사용
   1. [GitHub dotnetapiport](https://github.com/microsoft/dotnet-apiport)에서 독립 실행형 도구 다운로드 및 사용 독립 실행형 도구를 실행 하기 위한 지침은 [GitHub dotnetapiport 주요 변경 내용](https://github.com/microsoft/dotnet-apiport/blob/dev/docs/HowTo/BreakingChanges.md) 에 있습니다.
   1. 4\.7.2의 경우 호환성, `read isRetargeting == True` 가능한 문제를 식별 합니다.
2. 이 도구는 가능한 이전 버전과의 호환성에 의해 코드가 영향을 받을 수 있는지를 표시 하는 경우 (비호환의 일반적인 예는 아래에 나와 있음) 다음을 추가로 확인할 수 있습니다.
   1. 코드를 분석 하 고 코드가 영향을 받는 Api에 값을 전달 하는지 확인
   1. 런타임 검사를 수행 합니다. 런타임 배포는 ADLA에서 나란히 수행 되지 않습니다. VisualStudio 로컬 실행을 사용 하 여 대표 데이터 집합에 대해 로컬 .NET Framework 4.7.2를 사용 하 여 업그레이드 전에 런타임 검사를 수행할 수 있습니다.
3. 이전 버전과의 호환성에 영향을 주는 경우이를 해결 하는 데 필요한 단계를 수행 합니다 (예: 데이터 또는 코드 논리 수정).

대부분의 경우에는 이전 버전과의 호환성에 영향을 받지 않아야 합니다.

## <a name="timeline"></a>타임라인

[런타임 문제를 해결](runtime-troubleshoot.md)하 고 이전에 성공한 작업을 살펴보면 새 런타임의 배포를 확인할 수 있습니다.

### <a name="what-if-i-cant-get-my-code-reviewed-in-time"></a>내 코드를 검토 한 시간에 가져올 수 없는 경우

이전 런타임 버전 (4.5.2를 대상으로 하는 빌드)에 대해 작업을 제출할 수 있지만, side-by-side 기능이 .NET Framework 부족 하기 때문에 4.5.2 호환성 모드 에서만 실행 됩니다. 이 동작으로 인해 일부 이전 버전과의 호환성 문제가 여전히 발생할 수 있습니다.

### <a name="what-are-the-most-common-backwards-compatibility-issues-you-may-encounter"></a>발생할 수 있는 가장 일반적인 이전 버전과의 호환성 문제

검사기가 식별할 가능성이 가장 높은 가장 일반적인 비 호환성 (이 목록을 생성 했습니다)은 영향을 받는 라이브러리입니다. (참고: 라이브러리를 간접적 으로만 호출할 수 있습니다. 따라서 #1 필요한 작업을 수행 하 여 작업이 영향을 받는지 여부를 확인 하는 것이 중요 하며, 가능한 작업을 해결 하는 것이 중요 합니다. 참고: 거의 모든 경우에 자신의 작업에 대한 대부분의 주요 변경 내용으로 인해 경고가 가양성으로 natures.

- 결과 작업을 완료하려면 IAsyncResult.CompletedSynchronously 속성이 정확해야 함
  - TaskFactory.FromAsync를 호출할 때 IAsyncResult.CompletedSynchronously 속성이 올바르게 구현되어야 결과 작업을 완료할 수 있습니다. 즉, 이 속성은 구현이 동기적으로 완료된 경우에만 true를 반환해야 합니다. 이전에 속성을 선택하지 않았습니다.
  - 영향을 받는 라이브러리: mscorlib, 시스템. 작업
  - 제안 된 작업: System.threading.tasks.taskfactory.fromasync가 true를 올바르게 반환 하는지 확인 합니다.

- DataObject.GetData가 이제 데이터를 UTF-8로 검색
  - .NET Framework 4를 대상으로 하거나 .NET Framework 4.5.1 이하 버전에서 실행되는 앱의 경우, DataObject.GetData는 HTML 형식의 데이터를 ASCII 문자열로 검색합니다. 따라서 ASCII가 아닌 문자 (ASCII 코드가 0x7F 보다 큰 문자)는 임의의 두 문자로 표시 됩니다. #N # #N # .NET Framework 4.5 이상 버전을 대상으로 하며 .NET Framework 4.5.2에서 실행 되는 앱의 경우에는 보다 큰 문자를 올바르게 나타내는 u t f-8로 HTML 형식의 데이터를 검색 `DataObject.GetData`.
  - 영향을 받는 라이브러리: 인 글 o
  - 제안 된 작업: 검색 된 데이터가 원하는 형식 인지 확인 합니다.

- 잘못된 서로게이트 쌍에서 XmlWriter가 throw함
  - .NET Framework 4.5.2 또는 이전 버전을 대상으로 하는 앱의 경우 예외 대체(fallback) 처리를 사용하여 잘못된 서로게이트 쌍을 작성해도 항상 예외가 발생하지는 않습니다. .NET Framework 4.6을 대상으로 하는 앱의 경우 잘못된 서로게이트 쌍을 쓰려고 하면 `ArgumentException`을 throw합니다.
  - 영향을 받는 라이브러리: System.xml, system.xml. x m l. x m l.
  - 제안 된 작업: 인수 예외를 발생 시키는 잘못 된 서로게이트 쌍을 작성 하 고 있지 않은지 확인 합니다.

- HtmlTextWriter가 `<br/>` 요소를 올바르게 렌더링하지 않음
  - .NET Framework 4.6부터 `<BR />` 요소로 `HtmlTextWriter.RenderBeginTag()` 및 `HtmlTextWriter.RenderEndTag()`를 호출하면 (두 개가 아닌) 단 하나의 `<BR />`만 올바르게 삽입합니다.
  - 영향을 받는 라이브러리: System.web
  - 제안 된 작업: 프로덕션 작업에 임의의 동작이 표시 되지 않도록 하려는 `<BR />`의 양을 삽입 하 고 있는지 확인 합니다.

- Null 인수를 사용한 CreateDefaultAuthorizationContext 호출이 변경되었습니다.
  - Null authorizationPolicies를 사용하는 `CreateDefaultAuthorizationContext(IList<IAuthorizationPolicy>)`에 대한 호출로 반환된 AuthorizationContext의 구현이 .NET Framework 4.6에서 변경되었습니다.
  - 영향을 받는 라이브러리: System.identitymodel
  - 제안 된 작업: null 권한 부여 정책이 있을 때 새로운 예상 동작을 처리 하 고 있는지 확인 합니다.
  
- 이제 RSACng가 비표준 키 크기의 RSA 키를 올바르게 로드함
  - 4\.6.2 이전의 .NET Framework 버전의 경우, RSA 인증서의 비표준 키 크기를 가진 고객은 `GetRSAPublicKey()` 및 `GetRSAPrivateKey()` 확장 메서드를 통해 해당 키에 액세스할 수 없습니다. "요청한 키 크기가 지원 되지 않습니다." 라는 메시지가 포함 된 `CryptographicException`이 throw 됩니다. .NET Framework 4.6.2이 문제가 해결 되었습니다. 마찬가지로 `RSA.ImportParameters()` 및 `RSACng.ImportParameters()`는 `CryptographicException`의를 throw 하지 않고 비표준 키 크기를 사용 합니다.
  - 영향을 받는 라이브러리: mscorlib, System. 핵심
  - 제안 된 작업: RSA 키가 예상 대로 작동 하는지 확인 합니다.

- 경로 콜론 검사가 더욱 엄격해짐
  - .NET Framework 4.6.2에서 이전에 지원되지 않던 경로(길이 및 형식 모두)를 지원하도록 여러 가지가 변경되었습니다. 적절한 드라이브 구분 기호(콜론) 구문에 대해 검사가 좀 더 정확해졌습니다. 이 구문은 허용되었던 일부 선택 경로 API에서 일부 URI 경로가 차단되는 부작용이 있었습니다.
  - 영향을 받는 라이브러리: mscorlib, System. 확장명
  - 제안 된 작업:

- ClaimsIdentity 생성자 호출
  - .NET Framework 4.6.2부터는 `T:System.Security.Principal.IIdentity` 매개 변수로 `T:System.Security.Claims.ClaimsIdentity` 생성자가 `P:System.Security.Claims.ClaimsIdentify.Actor` 속성을 설정하는 방법이 변경되었습니다. `T:System.Security.Principal.IIdentity` 인수가 `T:System.Security.Claims.ClaimsIdentity` 개체이고 해당 `T:System.Security.Claims.ClaimsIdentity` 개체의 `P:System.Security.Claims.ClaimsIdentify.Actor` 속성이 `null`이 아닌 경우 `M:System.Security.Claims.ClaimsIdentity.Clone` 메서드를 사용하여 `P:System.Security.Claims.ClaimsIdentify.Actor` 속성이 연결됩니다. 프레임 워크 4.6.1 이전 버전에서는 `P:System.Security.Claims.ClaimsIdentify.Actor` 속성이 기존 참조로 연결 됩니다. 이러한 변경으로 인해 .NET Framework 4.6.2부터 새 `T:System.Security.Claims.ClaimsIdentity` 개체의 `P:System.Security.Claims.ClaimsIdentify.Actor` 속성은 생성자의 `T:System.Security.Principal.IIdentity` 인수에 대한 `P:System.Security.Claims.ClaimsIdentify.Actor` 속성과 같지 않습니다. .NET Framework 4.6.1 이전 버전에서는 이 속성이 같습니다.
  - 영향을 받는 라이브러리: mscorlib
  - 제안 된 작업: 새 런타임에서 ClaimsIdentity이 예상 대로 작동 하는지 확인 합니다.

- DataContractJsonSerializer를 사용한 제어 문자의 serialization가 이제 ECMAScript V6 및 V8과 호환됨
  - .NET framework 4.6.2 이전 버전에서는 DataContractJsonSerializer가 ECMAScript V6 및 V8 표준과 호환 되는 방식으로 \b, \f 및 \t와 같은 특수 제어 문자를 직렬화 하지 않았습니다. .NET Framework 4.7부터는 이러한 제어 문자의 serialization가 ECMAScript V6 및 V8과 호환됩니다.
  - 영향을 받는 라이브러리: System.object
  - 제안 된 작업: DataContractJsonSerializer와 동일한 동작을 보장 합니다.
