---
title: Azure AD 조건부 액세스 개발자 지침
description: Azure AD 조건부 액세스를 위한 개발자 지침 및 시나리오
services: active-directory
author: rwike77
manager: CelesteDG
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda
ms.date: 02/28/2019
ms.service: active-directory
ms.subservice: azuread-dev
ms.custom: aaddev
ms.topic: conceptual
ms.workload: identity
ROBOTS: NOINDEX
ms.openlocfilehash: b08b07ebcd6e45e95e23dff8686148889e9d4b39
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96173922"
---
# <a name="developer-guidance-for-the-azure-active-directory-conditional-access-feature"></a>Azure Active Directory 조건부 액세스를 위한 개발자 지침

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

> [!NOTE]
> 이 문서의 Microsoft ID 플랫폼 버전은 [Azure Active Directory 조건부 액세스를 위한 개발자 지침](../develop/v2-conditional-access-dev-guide.md)을 참조하십시오.

Azure AD(Azure Active Directory)의 조건부 액세스 기능은 앱의 보안을 유지하고 서비스를 보호하는 데 사용할 수 있는 여러 가지 방법 중 하나를 제공합니다. 조건부 액세스를 통해 개발자 및 기업 고객은 다음과 같은 다양한 방법으로 서비스를 보호할 수 있습니다.

* Multi-Factor Authentication
* Intune 등록 디바이스만 특정 서비스에 액세스할 수 있도록 허용
* 사용자 위치 및 IP 범위 제한

조건부 액세스의 전체 기능에 대한 자세한 내용은 [조건부 액세스란?](../conditional-access/overview.md)을 참조하세요.

이 문서에서는 Azure AD용 앱을 빌드하는 개발자를 위해 조건부 액세스를 사용하는 방법을 보여주고, 조건부 액세스 정책을 적용할 수 있는 제어 권한이 없는 리소스에 액세스하는 경우의 영향에 대해서도 알아봅니다. 또한 이 문서에서는 On-Behalf-Of 흐름, 웹앱, Microsoft Graph 액세스 및 API 호출에 수행되는 조건부 액세스의 영향도 알아봅니다.

[단일 및 다중 테넌트](../develop/howto-convert-app-to-be-multi-tenant.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) 앱과 [일반 인증 패턴](v1-authentication-scenarios.md)에 대해 알고 있다고 가정합니다.

## <a name="how-does-conditional-access-impact-an-app"></a>조건부 액세스가 앱에 어떤 영향을 주나요?

### <a name="app-types-impacted"></a>영향을 받는 앱 형식

대부분의 경우, 조건부 액세스는 앱의 동작을 변경하지 않으며 개발자의 변경이 필요하지 않습니다.  앱에서 서비스에 대한 토큰을 간접적으로 또는 자동으로 요청하는 특정한 경우에만 앱에서 조건부 액세스 "챌린지"를 처리하기 위해 코드를 변경해야 합니다.  이것은 대화형 로그인 요청을 수행하는 것처럼 간단할 수도 있습니다.

특히 다음 시나리오에는 조건부 액세스 "챌린지"를 처리하기 위한 코드가 필요합니다.

* On-Behalf-Of 흐름을 수행하는 앱
* 여러 서비스/리소스에 액세스하는 앱
* ADAL.js를 사용하는 단일 페이지 앱
* 리소스를 호출하는 Web Apps

조건부 액세스 정책은 앱뿐만 아니라 사용자 앱이 액세스하는 웹 API에도 적용할 수 있습니다. 조건부 액세스 정책을 구성하는 방법을 자세히 알아보려면 [일반 조건부 액세스 정책](../conditional-access/concept-conditional-access-policy-common.md)을 참조하세요.

시나리오에 따라 기업 고객은 언제든지 조건부 액세스 정책을 적용하고 제거할 수 있습니다. 새 정책이 적용될 때 앱이 계속 작동하도록 하려면 “챌린지” 처리를 구현해야 합니다. 다음 예에서는 챌린지 처리를 보여 줍니다.

### <a name="conditional-access-examples"></a>조건부 액세스 예제

일부 시나리오는 조건부 액세스를 처리하기 위해 코드 변경이 필요한 반면 다른 시나리오는 그대로 작동합니다. 다음에서 다단계 인증을 수행하기 위해 조건부 액세스를 사용하는 몇 가지 시나리오를 살펴보고 차이를 파악할 수 있습니다.

* 단일 테넌트 iOS 앱을 빌드하고 조건부 액세스 정책을 적용합니다. 사용자는 앱에 로그인하고 앱은 API에 대한 액세스를 요청하지 않습니다. 사용자가 로그인하면 정책이 자동으로 호출되고 사용자는 MFA(Multi-Factor Authentication)를 수행해야 합니다. 
* 중간 계층 서비스를 사용하여 다운스트림 API에 액세스하는 원시 앱을 빌드하고 있습니다. 이 앱을 사용하는 회사의 기업 고객은 다운스트림 API에 정책을 적용합니다. 최종 사용자가 로그인하면 원시 앱에서 중간 계층에 대한 액세스를 요청하고 토큰을 보냅니다. 중간 계층은 On-Behalf-Of 흐름을 수행하여 다운스트림 API에 대한 액세스를 요청합니다. 이때 클레임 “챌린지”가 중간 계층에 표시됩니다. 중간 계층은 조건부 액세스 정책을 준수해야 하는 네이티브 앱으로 챌린지를 다시 보냅니다.

#### <a name="microsoft-graph"></a>Microsoft Graph

Microsoft Graph는 조건부 액세스 환경에서 앱을 빌드할 때 특별히 고려해야 할 사항이 있습니다. 일반적으로 조건부 액세스의 메커니즘은 동일하게 작동하지만 사용자에게 표시되는 정책은 앱이 그래프에서 요청하는 기본 데이터를 기반으로 합니다. 

특히, 모든 Microsoft Graph 범위는 개별적으로 정책을 적용할 수 있는 일부 데이터 세트를 나타냅니다. 조건부 액세스 정책에는 특정 데이터 세트가 할당되기 때문에 Azure AD는 그래프 자체가 아니라 그래프 뒤에 있는 데이터를 기반으로 조건부 액세스 정책을 적용합니다.

예를 들어 앱이 다음 Microsoft Graph 범위를 요청하는 경우

```
scopes="Bookings.Read.All Mail.Read"
```

앱은 사용자가 예약 및 교환에 설정된 모든 정책을 충족하도록 할 수 있습니다. 일부 범위는 액세스 권한을 부여하는 경우 여러 데이터 세트에 매핑될 수 있습니다. 

### <a name="complying-with-a-conditional-access-policy"></a>조건부 액세스 정책 준수

여러 가지 서로 다른 앱 토폴로지의 경우 세션이 설정될 때 조건부 액세스 정책이 평가됩니다. 조건부 액세스 정책은 앱 및 서비스 단위로 작동하므로 호출되는 시점은 수행하려는 시나리오에 따라 크게 달라집니다.

앱이 조건부 액세스 정책으로 서비스에 대한 액세스를 시도하면 조건부 액세스 챌린지가 발생할 수 있습니다. 이 챌린지는 Azure AD의 응답에서 제공되는 `claims` 매개 변수에 인코딩됩니다. 이 챌린지 매개 변수의 예는 다음과 같습니다. 

```
claims={"access_token":{"polids":{"essential":true,"Values":["<GUID>"]}}}
```

개발자는 이 챌린지를 가져와 Azure AD에 대한 새 요청에 추가할 수 있습니다. 이 상태를 전달하면 최종 사용자에게 조건부 액세스 정책을 준수하는 데 필요한 모든 작업을 수행하라는 메시지가 나타납니다. 다음 시나리오에서 구체적인 오류 정보 및 매개 변수를 추출하는 방법을 설명합니다.

## <a name="scenarios"></a>시나리오

### <a name="prerequisites"></a>사전 요구 사항

Azure AD 조건부 액세스는 [Azure AD Premium](../fundamentals/active-directory-whatis.md)에 포함된 기능입니다. [허가되지 않은 사용 현황 보고서](../reports-monitoring/overview-reports.md)에서 라이선스 요구 사항에 대해 자세히 알아볼 수 있습니다. 개발자는 Azure AD Premium이 포함된 Enterprise Mobility Suite에 대한 평가판 구독이 속한 [Microsoft Developer Network](/)에 참여할 수 있습니다.

### <a name="considerations-for-specific-scenarios"></a>특정 시나리오에 대한 고려 사항

다음 정보는 이러한 조건부 액세스 시나리오에만 적용됩니다.

* On-Behalf-Of 흐름을 수행하는 앱
* 여러 서비스/리소스에 액세스하는 앱
* ADAL.js를 사용하는 단일 페이지 앱

다음 섹션에서는 더 복잡한 일반적인 시나리오에 대해 설명합니다. 핵심 작동 원리는 조건부 액세스 정책이 적용된 서비스에 대해 토큰을 요청할 때 조건부 액세스 정책이 평가된다는 것입니다.

## <a name="scenario-app-performing-the-on-behalf-of-flow"></a>시나리오: On-Behalf-Of 흐름을 수행하는 앱

이 시나리오에서는 원시 앱이 웹 서비스/API를 호출하는 경우를 연습합니다. 이 서비스에서는 "On-Behalf-Of" 흐름을 차례로 수행하여 다운스트림 서비스를 호출합니다. 이 경우, 다운스트림 서비스(Web API 2)에 조건부 액세스 정책을 적용했고 서버/디먼 앱보다는 네이티브 앱을 사용합니다. 

![On-Behalf-Of 흐름을 수행하는 앱 다이어그램](./media/conditional-access-dev-guide/app-performing-on-behalf-of-scenario.png)

Web API 1에서 항상 다운스트림 API를 호출하는 것은 아니므로 Web API 1에 대한 초기 토큰 요청 시 최종 사용자에게 다단계 인증을 요구하지 않습니다. Web API 1이 Web API 2에 대해 사용자를 대신하여 토큰 요청을 시도하면 사용자가 다단계 인증으로 로그인하지 않았으므로 요청에 실패합니다.

Azure AD는 몇 가지 흥미로운 데이터가 포함된 HTTP 응답을 반환합니다.

> [!NOTE]
> 이 예에서는 다단계 인증 오류에 대한 설명이지만 조건부 액세스와 관련하여 광범위한 `interaction_required`가 포함될 수 있습니다.

```
HTTP 400; Bad Request
error=interaction_required
error_description=AADSTS50076: Due to a configuration change made by your administrator, or because you moved to a new location, you must use multi-factor authentication to access '<Web API 2 App/Client ID>'.
claims={"access_token":{"polids":{"essential":true,"Values":["<GUID>"]}}}
```

Web API 1에서는 `error=interaction_required` 오류를 catch하고 `claims` 챌린지를 데스크톱 앱에 다시 보냅니다. 이 시점에 데스크톱 앱은 `acquireToken()`을 새로 호출하고 `claims` 챌린지를 추가 쿼리 문자열 매개 변수로 추가합니다. 이 새로운 요청은 사용자가 다단계 인증을 수행하도록 요구한 후 이 새로운 토큰을 Web API 1로 다시 보내고 On-Behalf-Of 흐름을 완료합니다.

이 시나리오를 사용해 보려면 [.NET 샘플 코드](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof-ca)를 참조하세요. 여기서는 Web API 1에서 네이티브 앱으로 클레임 챌린지를 전달하는 방법과 클라이언트 앱 내부에 새 요청을 생성하는 방법을 보여 줍니다.

## <a name="scenario-app-accessing-multiple-services"></a>시나리오: 여러 서비스에 액세스하는 앱

이 시나리오에서는 웹앱이 조건부 액세스 정책이 할당된 두 서비스에 액세스하는 경우를 살펴봅니다. 사용자 앱 논리에 따라, 앱에서 두 웹 서비스에 액세스하지 않아도 되는 경로가 존재할 수 있습니다. 이 시나리오에서 토큰을 요청하는 순서는 최종 사용자 환경에서 중요한 역할을 합니다.

웹 서비스 A와 B가 있고 웹 서비스 B에 조건부 액세스 정책이 적용되었다고 가정해 보겠습니다. 초기 대화형 인증 요청에는 두 서비스에 대한 동의가 필요하지만 모든 경우에 조건부 액세스 정책이 요구되지는 않습니다. 앱에서 웹 서비스 B에 대한 토큰을 요청하는 경우 정책이 호출되고 다음과 같이 웹 서비스 A에 대한 후속 요청도 성공합니다.

![여러 서비스 흐름에 액세스하는 앱 다이어그램](./media/conditional-access-dev-guide/app-accessing-multiple-services-scenario.png)

또는 앱이 웹 서비스 A에 대한 토큰을 처음으로 요청하는 경우 최종 사용자는 조건부 액세스 정책을 호출하지 않습니다. 이렇게 하면 앱 개발자가 최종 사용자 환경을 제어하고 모든 경우에 조건부 액세스 정책이 강제로 호출되는 것을 막을 수 있습니다. 까다로운 사례는 앱이 웹 서비스 B에 대한 토큰을 나중에 요청하는 경우입니다. 이때 최종 사용자는 조건부 액세스 정책을 준수해야 합니다. 앱에서 `acquireToken`을 시도하면 다음 오류를 생성할 수 있습니다(다음 다이어그램에 설명됨).

```
HTTP 400; Bad Request
error=interaction_required
error_description=AADSTS50076: Due to a configuration change made by your administrator, or because you moved to a new location, you must use multi-factor authentication to access '<Web API App/Client ID>'.
claims={"access_token":{"polids":{"essential":true,"Values":["<GUID>"]}}}
```

![새 토큰을 요청하는 여러 서비스에 액세스하는 앱](./media/conditional-access-dev-guide/app-accessing-multiple-services-new-token.png)

앱에서 ADAL 라이브러리를 사용하는 경우 토큰을 획득하지 못하면 항상 대화형으로 다시 시도합니다. 이 대화형 요청이 발생하면 최종 사용자는 조건부 액세스를 준수할 수 있는 기회를 얻게 됩니다. 요청이 `AcquireTokenSilentAsync` 또는 `PromptBehavior.Never`인 경우를 제외하고 앱은 최종 사용자가 정책을 준수할 수 있는 기회를 제공하기 위해 대화형 ```AcquireToken``` 요청을 수행해야 합니다.

## <a name="scenario-single-page-app-spa-using-adaljs"></a>시나리오: ADAL.js를 사용하는 SPA(단일 페이지 앱)

이 시나리오에서는 ADAL.js를 사용하여 조건부 액세스 보호된 웹 API를 호출하는 SPA(단일 페이지 앱)가 있는 경우를 살펴봅니다. 간단한 아키텍처이지만 조건부 액세스를 기반으로 개발할 때 고려해야 하는 미묘한 차이가 있습니다.

ADAL.js에는 토큰을 얻기 위핸 몇 가지 함수가 있습니다(예: `login()`, `acquireToken(...)`, `acquireTokenPopup(…)` 및 `acquireTokenRedirect(…)`).

* `login()`은 대화형 로그인 요청을 통해 ID 토큰을 가져오지만 조건부 액세스 보호되는 Web API를 비롯한 모든 서비스에 대한 액세스 토큰을 가져오지는 않습니다
* 그런 다음 `acquireToken(…)`을 사용하여 액세스 토큰을 자동으로 얻을 수 있는데, 이는 어떤 상황에서도 UI가 표시되지 않음을 의미합니다.
* `acquireTokenPopup(…)` 및 `acquireTokenRedirect(…)`는 리소스에 대한 토큰을 대화형으로 요청하는 데 사용되며 이는 항상 로그인 UI가 표시됨을 의미합니다.

앱에서 Web API를 호출하는 데 액세스 토큰이 필요한 경우 `acquireToken(…)`을 시도합니다. 토큰 세션이 만료되거나 조건부 액세스 정책을 준수해야 하는 경우 *acquireToken* 함수가 실패하며 앱은 `acquireTokenPopup()` 또는 `acquireTokenRedirect()`를 사용합니다.

![ADAL 흐름을 사용하는 단일 페이지 앱 다이어그램](./media/conditional-access-dev-guide/spa-using-adal-scenario.png)

이제 조건부 액세스 시나리오에 대한 예를 살펴보겠습니다. 최종 사용자는 방금 사이트에 연결되었고 세션이 없습니다. `login()` 호출을 수행하면 다단계 인증 없이 ID 토큰을 가져옵니다. 그러면 사용자는 단추를 눌러 앱이 Web API로부터 데이터를 요청하도록 해야 합니다. 앱은 `acquireToken()` 호출을 수행하려고 하지만 사용자가 아직 다단계 인증을 수행하지 않았고 조건부 액세스 정책을 준수해야 하므로 실패합니다.

Azure AD는 다음 HTTP 응답을 다시 보냅니다.

```
HTTP 400; Bad Request
error=interaction_required
error_description=AADSTS50076: Due to a configuration change made by your administrator, or because you moved to a new location, you must use multi-factor authentication to access '<Web API App/Client ID>'.
```

앱은 `error=interaction_required`를 catch해야 합니다. 그러면 애플리케이션은 동일한 리소스에서 `acquireTokenPopup()` 또는 `acquireTokenRedirect()`를 사용할 수 있습니다. 사용자는 다단계 인증을 수행해야 합니다. 사용자가 다단계 인증을 완료하면 앱에는 요청된 리소스에 대한 새로운 액세스 토큰이 발급됩니다.

이 시나리오를 사용해 보려면 [JS SPA On-behalf-of 샘플 코드](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof-ca)를 참조하세요. 이 샘플 코드에서는 이 시나리오를 설명하기 위해 JS SPA와 함께 이전에 등록한 조건부 액세스 정책 및 Web API를 사용합니다. 클레임 챌린지를 올바르게 처리하고 Web API에 사용할 수 있는 액세스 토큰을 가져오는 방법을 보여 줍니다. 또한 Angular SPA에 대한 지침은 일반 [Angular.js 샘플 코드](https://github.com/Azure-Samples/active-directory-angularjs-singlepageapp)를 확인하세요.

## <a name="see-also"></a>참고 항목

* 기능에 대해 자세히 알아보려면 [Azure Active Directory의 조건부 액세스](../conditional-access/overview.md)를 참조하세요.
* 더 많은 Azure AD 샘플 코드를 보려면 [샘플 코드의 GitHub 리포지토리](https://github.com/azure-samples?utf8=%E2%9C%93&q=active-directory)를 참조하세요.
* ADAL SDK에 대한 자세한 내용을 보고 참조 설명서에 액세스하려면 [라이브러리 가이드](active-directory-authentication-libraries.md)를 참조하세요.
* 다중 테넌트 시나리오에 대한 자세한 내용은 [다중 테넌트 패턴을 사용하여 사용자를 로그인하는 방법](../develop/howto-convert-app-to-be-multi-tenant.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)을 참조하세요.