---
title: V 2.0 (Microsoft identity platform) 업데이트 Microsoft
description: V2.0 (Microsoft identity platform) 끝점과 Azure Active Directory (Azure AD) v1.0 끝점 간의 차이점을 확인 하 고 v 2.0으로 업데이트 하는 이점에 대해 알아봅니다.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.workload: identity
ms.topic: conceptual
ms.date: 11/26/2019
ms.author: ryanwi
ms.reviewer: saeeda, hirsin, jmprieur, sureshja, jesakowi, lenalepa, kkrishna, negoe
ms.custom: aaddev
ms.openlocfilehash: 56a5221504a3905855fa5dc713707f34ae357aea
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77165138"
---
# <a name="why-update-to-microsoft-identity-platform-v20"></a>V 2.0 (Microsoft identity platform)을 업데이트 하는 이유

새 응용 프로그램을 개발할 때 v2.0 (Microsoft identity platform) 및 Azure Active Directory (v1.0) 끝점 간의 차이점을 파악 하는 것이 중요 합니다. 이 문서에서는 끝점과 Microsoft id 플랫폼에 대 한 몇 가지 기존 제한 사항 간의 주요 차이점에 대해 설명 합니다.

> [!NOTE]
> Microsoft id 플랫폼 끝점은 모든 Azure AD 시나리오 및 기능을 지원 하지 않습니다. Microsoft id 플랫폼 끝점을 사용 해야 하는지 확인 하려면 [microsoft id 플랫폼 제한 사항](#limitations)을 참조 하세요.

## <a name="who-can-sign-in"></a>로그인할 수 있는 사용자

![v1.0 및 v2.0 엔드포인트에 로그인할 수 있는 사용자](media/azure-ad-endpoint-comparison/who-can-signin.svg)

* v1.0 엔드포인트의 경우 회사 및 학교 계정만 애플리케이션(Azure AD)에 로그인할 수 있습니다.
* Microsoft id 플랫폼 끝점을 사용 하면 Azure AD의 회사 및 학교 계정과 hotmail.com, outlook.com 및 msn.com와 같은 개인 Microsoft 계정 (MSA)을 사용 하 여 로그인 할 수 있습니다.
* 두 끝점은 *[단일 테 넌 트](../develop/single-and-multi-tenant-apps.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)* 로 구성 된 응용 프로그램에 대 한 Azure AD 디렉터리의 *[게스트 사용자](https://docs.microsoft.com/azure/active-directory/b2b/what-is-b2b)* 에 대 한 로그인 이나 테 넌 트 별 끝점 (`https://login.microsoftonline.com/{TenantId_or_Name}`)을 가리키도록 구성 된 *다중 테 넌 트* 응용 프로그램의 로그인도 허용 합니다.

Microsoft id 플랫폼 끝점을 사용 하 여 개인 Microsoft 계정, 회사 및 학교 계정에서의 로그인을 허용 하는 앱을 작성할 수 있습니다. 따라서 완전히 계정에 제약이 없는 앱을 작성할 수 있습니다. 예를 들어 앱이 [Microsoft Graph](https://graph.microsoft.io)를 호출하는 경우 일부 추가 기능 및 데이터를 해당 SharePoint 사이트 또는 디렉터리 데이터와 같은 회사 계정에서 사용할 수 있습니다. 단, [사용자의 이메일 읽기](https://developer.microsoft.com/graph/docs/api-reference/v1.0/api/user_list_messages)와 같은 많은 작업에서 동일한 코드가 개인 계정과 회사 및 학교 계정 이메일 모두에 액세스할 수 있습니다.

Microsoft id 플랫폼 끝점의 경우 MSAL (Microsoft 인증 라이브러리)을 사용 하 여 소비자, 교육 및 엔터프라이즈에 대 한 액세스 권한을 얻을 수 있습니다. Azure AD v1.0 엔드포인트는 회사 및 학교 계정에서만 로그인을 허용합니다.

## <a name="incremental-and-dynamic-consent"></a>증분 및 동적 동의

Azure AD v1.0 엔드포인트를 사용하는 앱은 필수 OAuth 2.0 권한을 사전에 지정해야 합니다. 예를 들면:

![권한 등록 UI를 보여 주는 예제](./media/azure-ad-endpoint-comparison/app-reg-permissions.png)

애플리케이션 등록에서 직접 설정한 권한은 **정적**입니다. Azure Portal에 정의된 앱의 정적 권한은 코드를 멋지고 간단하게 유지했지만, 개발자에게 몇 가지 가능한 문제를 줄 수 있습니다.

* 사용자가 처음 로그인할 때 앞으로 앱에서 필요로 하게 될 모든 권한을 요청해야 합니다. 이로 인해 권한 목록이 매우 길어서 최종 사용자는 처음 로그인 시 앱의 액세스를 승인하지 않을 수 있습니다.

* 앱이 액세스할 수도 있는 모든 리소스를 미리 알아야 합니다. 리소스의 임의 개수에 액세스할 수 있는 앱을 만들기 어려웠습니다.

Microsoft id 플랫폼 끝점을 사용 하 여 Azure Portal에서 앱 등록 정보에 정의 된 정적 권한을 무시 하 고 대신 증분 권한을 요청 하 여 요청 하는 최소 권한 집합을 요청 하는 것이 가능 합니다. 그리고 고객이 추가 앱 기능을 사용 하므로 시간이 지남에 따라 점점 증가 합니다. 이렇게 하려면 애플리케이션 등록 정보에 미리 정의할 필요 없이, 액세스 토큰을 요청할 때 `scope` 매개 변수에 새 범위를 포함시켜서 언제든지 앱에 필요한 범위를 지정할 수 있습니다. 사용자가 요청에 추가된 새 범위에 아직 동의하지 않은 경우에는 새 권한에만 동의하라는 메시지가 표시됩니다. 자세한 내용은 [권한, 동의 및 범위](../develop/v2-permissions-and-consent.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)를 참조하세요.

`scope` 매개 변수를 통해 앱이 동적으로 권한을 요청할 수 있도록 허용하면 개발자가 사용자 환경을 완전히 제어할 수 있습니다. 초기 단계에 동의 환경을 배치하고 하나의 초기 권한 부여 요청으로 모든 권한을 요청할 수도 있습니다. 앱이 많은 권한을 요청하는 경우 시간의 경과에 따라 앱의 특정 기능을 사용하도록 시도하는 것처럼 점진적으로 사용자로부터 권한을 수집할 수 있습니다.

조직 대신 관리자가 동의를 수행하려면 앱에 등록된 정적 권한이 필요하므로, 전체 조직을 대신하여 관리자가 동의해야 하는 경우 앱 등록 포털에서 앱에 대한 권한을 설정해야 합니다. 이렇게 하면 조직 관리자가 애플리케이션을 설정하는 데 필요한 주기가 줄어듭니다.

## <a name="scopes-not-resources"></a>리소스가 아닌 범위

v1.0 엔드포인트를 사용하는 앱은 **리소스** 또는 토큰 수신자로 작동할 수 있습니다. 리소스는 많은 **범위** 또는 이해할 수 있는 **oAuth2Permissions**를 정의할 수 있으며, 클라이언트 앱이 범위의 특정 집합에 대한 리소스에서 토큰을 요청하도록 허용합니다. 리소스의 예로 Microsoft Graph API를 살펴보겠습니다.

* 리소스 식별자 또는 `AppID URI`: `https://graph.microsoft.com/`
* 범위 또는 `oAuth2Permissions`: `Directory.Read`, `Directory.Write` 등

Microsoft id 플랫폼 끝점의 경우에도 마찬가지입니다. 앱은 여전히 리소스로 작동하고, 범위를 정의하고, URI로 식별될 수 있습니다. 클라이언트 앱은 여전히 해당 범위에 액세스 요청할 수 있습니다. 그러나 클라이언트에서 이러한 사용 권한을 요청 하는 방법이 변경 되었습니다.

v1.0 엔드포인트의 경우, Azure AD에 대한 OAuth 2.0 인증 요청은 다음과 같습니다.

```text
GET https://login.microsoftonline.com/common/oauth2/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&resource=https://graph.windows.net/
...
```

여기서 **resource** 매개 변수는 클라이언트 앱이 권한 부여를 요청하는 리소스를 나타냅니다. Azure AD는 Azure Portal의 정적 구성을 기준으로 앱에 필요한 권한을 계산하고, 이에 따라 토큰을 발급합니다.

Microsoft id 플랫폼 끝점을 사용 하는 응용 프로그램의 경우 동일한 OAuth 2.0 권한 부여 요청은 다음과 같습니다.

```text
GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&scope=https://graph.windows.net/directory.read%20https://graph.windows.net/directory.write
...
```

여기서 **scope** 매개 변수는 앱이 권한 부여를 요청하는 리소스와 권한을 나타냅니다. 원하는 리소스는 여전히 요청에 있으며, scope 매개 변수의 각 값에 포함되어 있습니다. 이러한 방식으로 범위 매개 변수를 사용 하면 Microsoft id 플랫폼 끝점이 OAuth 2.0 사양을 보다 잘 준수할 수 있으며 일반적인 업계 사례에 더 가깝게 맞춰집니다. 또한 응용 프로그램에서 프런트 엔드가 아닌 증분 동의를 요청 하는 경우에만 앱이 [증분 동의](#incremental-and-dynamic-consent) 를 수행할 수 있습니다.

## <a name="well-known-scopes"></a>잘 알려진 범위

### <a name="offline-access"></a>오프라인 액세스

Microsoft id 플랫폼 끝점을 사용 하는 앱은 앱에 대해 잘 알려진 새 권한 (`offline_access` 범위)을 사용 해야 할 수 있습니다. 모든 앱이 연장된 기간 동안 사용자를 대신하여 리소스에 액세스해야 할 경우, 사용자가 앱을 자주 쓰지 않는 경우에도 이 권한을 요청해야 합니다. `offline_access` 범위는 사용자에게 동의 대화 상자에 **언제든지 데이터에 액세스**로 표시되며 사용자는 여기에 반드시 동의해야 합니다. `offline_access` 권한을 요청 하면 웹 앱이 Microsoft id 플랫폼 끝점에서 OAuth 2.0 refresh_tokens를 받을 수 있습니다. 새로 고침 토큰은 수명이 길며, 오랜 기간 액세스하기 위해 새로운 OAuth 2.0 액세스 토큰으로 교환할 수 있습니다.

앱이 `offline_access` 범위를 요청 하지 않으면 새로 고침 토큰이 수신 되지 않습니다. 즉, OAuth 2.0 권한 부여 코드 흐름에서 인증 코드를 사용하면 `/token` 엔드포인트에서 액세스 토큰만 수신하게 됩니다. 이 액세스 토큰은 짧은 기간(일반적으로 1시간) 동안 유효하지만 결국 만료됩니다. 해당 시점에 앱은 사용자를 `/authorize` 엔드포인트로 다시 리디렉션하여 새 인증 코드를 검색해야 합니다. 리디렉션 중에 앱 유형에 따라 사용자가 자격 증명을 다시 입력하거나 권한에 다시 동의해야 할 수도 있고 그렇지 않을 수도 있습니다.

OAuth 2.0, `refresh_tokens`및 `access_tokens`에 대 한 자세한 내용은 [Microsoft id 플랫폼 프로토콜 참조를 참조](../develop/active-directory-v2-protocols.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)하세요.

### <a name="openid-profile-and-email"></a>OpenID, 프로필 및 메일

지금까지 Microsoft id 플랫폼을 사용 하는 가장 기본적인 Openid connect Connect 로그인 흐름은 결과 *id_token*에서 사용자에 대 한 많은 정보를 제공 합니다. id_token의 클레임에는 사용자의 이름, 기본 설정된 사용자 이름, 전자 메일 주소, 개체 ID 등이 있습니다.

이제 `openid` 범위를 통해 앱의 액세스가 허용되는 정보가 제한됩니다. `openid` 범위는 앱이 사용자를 로그인하고 해당 사용자의 앱 특정 식별자를 수신하는 작업만 허용합니다. 앱에 있는 사용자의 개인 데이터를 가져오려면 앱이 사용자로부터 추가 권한을 요청해야 합니다. 두 개의 새 범위(`email` 및 `profile` 범위)를 통해 추가 권한을 요청할 수 있습니다.

* `email` 범위는 사용자에게 주소 지정 가능한 메일 주소가 있다고 가정하여 앱이 id_token의 `email` 클레임을 통해 사용자의 기본 메일 주소에 액세스할 수 있도록 해줍니다.
* `profile` 범위는 id_token에서 사용자에 대 한 다른 모든 기본 정보 (예: 이름, 기본 설정 된 사용자 이름, 개체 ID 등)에 대 한 모든 기본 정보에 대 한 앱 액세스 권한을 가집니다.

이러한 범위를 사용하면 최소한의 공개로 앱을 코딩할 수 있으며, 작업 수행에 필요한 정보만 사용자에게 요청할 수 있습니다. 이러한 범위에 대 한 자세한 내용은 [Microsoft id 플랫폼 범위 참조](../develop/v2-permissions-and-consent.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)를 참조 하세요.

## <a name="token-claims"></a>토큰 클레임

Microsoft id 플랫폼 끝점은 페이로드를 작게 유지 하기 위해 기본적으로 해당 토큰에서 더 작은 클레임 집합을 발급 합니다. Microsoft id 플랫폼 토큰에서 더 이상 기본적으로 제공 되지 않는 v2.0 토큰의 특정 클레임에 종속 된 앱 및 서비스가 있는 경우 [선택적 클레임](../develop/active-directory-optional-claims.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) 기능을 사용 하 여 해당 클레임을 포함 하는 것이 좋습니다.

> [!IMPORTANT]
> v 1.0 및 v2.0 끝점 모두에서 v1.0 및 v2.0 토큰을 발급할 수 있습니다! id_tokens *항상* 요청 된 끝점과 일치 하 고, 액세스 토큰은 클라이언트가 해당 토큰을 사용 하 여 호출 하는 Web API에 필요한 *형식과 일치 해야* 합니다.  따라서 앱이 v2.0 끝점을 사용 하 여 Microsoft Graph를 호출 하는 토큰을 가져오는 경우 (v2.0 형식 액세스 토큰이 필요한 경우) 앱은 v1.0 형식으로 토큰을 받습니다.  

## <a name="limitations"></a>제한 사항

Microsoft id 플랫폼을 사용할 때 알아야 할 몇 가지 제한 사항이 있습니다.

Microsoft id 플랫폼과 통합 되는 응용 프로그램을 빌드할 때 Microsoft id 플랫폼 끝점과 인증 프로토콜이 사용자의 요구를 충족 하는지 여부를 결정 해야 합니다. V1.0 끝점 및 플랫폼은 완전히 지원 되며, 일부 측면에서 Microsoft id 플랫폼 보다 풍부한 기능을 갖추고 있습니다. 그러나 Microsoft id 플랫폼은 개발자에 게 [상당한 혜택](azure-ad-endpoint-comparison.md) 을 제공 합니다.

이제 개발자를 위한 단순화 된 권장 사항은 다음과 같습니다.

* 응용 프로그램에서 개인 Microsoft 계정을 지원 하거나 새 응용 프로그램을 작성 하려는 경우 Microsoft id 플랫폼을 사용 하세요. 그러나 그 전에 이 문서에 설명된 제한 사항을 이해해야 합니다.
* SAML에 의존 하는 응용 프로그램을 마이그레이션하거나 업데이트 하는 경우 Microsoft id 플랫폼을 사용할 수 없습니다. 대신 [AZURE AD v1.0 가이드](v1-overview.md)를 참조 하세요.

Microsoft id 플랫폼 끝점은 여기에 나열 된 제한 사항을 제거 하 여 Microsoft id 플랫폼 끝점을 사용 하기만 하면 됩니다. 그 동안이 문서를 사용 하 여 Microsoft id 플랫폼 끝점이 사용자에 게 적합 한지 확인 합니다. Microsoft id 플랫폼 끝점의 현재 상태를 반영 하도록이 문서를 계속 업데이트할 예정입니다. Microsoft id 플랫폼 기능에 대 한 요구 사항을 다시 평가 하려면 다시 확인 하세요.

### <a name="restrictions-on-app-registrations"></a>앱 등록에 대한 제한 사항

Microsoft id 플랫폼 끝점과 통합 하려는 각 앱에 대해 Azure Portal의 새로운 [ **앱 등록** 환경](https://aka.ms/appregistrations) 에서 앱 등록을 만들 수 있습니다. 기존 Microsoft 계정 앱은 포털과 호환 되지 않지만 모든 Azure AD 앱은 등록 된 위치와 상관 없이입니다.

회사 및 학교 계정과 개인 계정을 지원하는 앱 등록에는 다음과 같은 주의 사항이 있습니다.

* 애플리케이션 ID당 두 개의 앱 비밀만 허용됩니다.
* 테넌트에 등록되지 않은 애플리케이션은 등록한 계정에서만 관리할 수 있습니다. 다른 개발자와 공유할 수 없습니다. 이는 앱 등록 포털에서 개인 Microsoft 계정을 사용하여 등록된 대부분의 앱에 적용됩니다. 여러 개발자와 앱 등록을 공유 하려는 경우에는 Azure Portal의 새 **앱 등록** 섹션을 사용 하 여 테 넌 트에서 응용 프로그램을 등록 합니다.
* 허용된 리디렉션 URL 형식에 대한 몇 가지 제한 사항이 있습니다. 리디렉션 URL에 대한 자세한 내용은 다음 섹션을 참조하세요.

### <a name="restrictions-on-redirect-urls"></a>리디렉션 URI에 대한 제한

Microsoft id 플랫폼용으로 등록 된 앱은 제한 된 리디렉션 URL 값 집합으로 제한 됩니다. 웹앱 및 서비스에 대한 리디렉션 URL은 스키마 `https`로 시작해야 하고 모든 리디렉션 URL 값은 단일 DNS 도메인을 공유해야 합니다.  등록 시스템은 기존 리디렉션 URL의 전체 DNS 이름을 추가하려는 리디렉션 URL의 DNS 이름과 비교합니다. `http://localhost`도 리디렉션 URL로 지원됩니다.  

다음 조건 중 하나라도 충족되는 경우 DNS 이름을 추가하는 요청이 실패하게 됩니다.  

* 새 리디렉션 URL의 전체 DNS 이름이 기존 리디렉션 URL의 DNS 이름과 일치하지 않는 경우
* 새 리디렉션 URL의 전체 DNS 이름이 기존 리디렉션 URL의 하위 도메인이 아닌 경우

#### <a name="example-1"></a>예 1

앱에 `https://login.contoso.com`의 리디렉션 URL이 있는 경우 다음 예제와 같이 DNS 이름이 정확히 일치하는 리디렉션 URL을 추가할 수 있습니다.

`https://login.contoso.com/new`

또는 다음 예제와 같이 login.contoso.com의 DNS 하위 도메인을 참조할 수 있습니다.

`https://new.login.contoso.com`

#### <a name="example-2"></a>예제 2

`login-east.contoso.com` 및 `login-west.contoso.com`을 리디렉션 URL로 사용하는 앱이 필요하면 리디렉션 URL을 다음 순서로 추가해야 합니다.

`https://contoso.com`  
`https://login-east.contoso.com`  
`https://login-west.contoso.com`  

후자는 첫 번째 리디렉션 URL 인 contoso.com의 하위 도메인 이므로 추가할 수 있습니다.

특정 응용 프로그램에 대해 회신 Url을 20 개만 포함할 수 있습니다 .이 제한은 등록이 지 원하는 모든 앱 유형 (SPA (단일 페이지 응용 프로그램), native client, 웹 앱 및 서비스)에 적용 됩니다.  

Microsoft id 플랫폼에서 사용 하기 위해 앱을 등록 하는 방법을 알아보려면 [새로운 앱 등록 환경을 사용 하 여 앱 등록](../develop/quickstart-register-app.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)을 참조 하세요.

### <a name="restrictions-on-libraries-and-sdks"></a>라이브러리 및 SDK에 대한 제한 사항

현재 Microsoft id 플랫폼 끝점에 대 한 라이브러리 지원은 제한 되어 있습니다. 프로덕션 응용 프로그램에서 Microsoft identity platform 끝점을 사용 하려는 경우 다음 옵션을 사용할 수 있습니다.

* 웹 응용 프로그램을 작성 하는 경우 일반적으로 사용 가능한 서버 쪽 미들웨어를 안전 하 게 사용 하 여 로그인 및 토큰 유효성 검사를 수행할 수 있습니다. 여기에는 ASP.NET용 OWIN Open ID Connect 미들웨어 및 Node.js Passport 플러그 인이 포함됩니다. Microsoft 미들웨어를 사용 하는 코드 샘플은 [microsoft id 플랫폼 시작](../develop/v2-overview.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json#getting-started) 섹션을 참조 하세요.
* 데스크톱 또는 모바일 응용 프로그램을 빌드하는 경우 MSAL (Microsoft 인증 라이브러리) 중 하나를 사용할 수 있습니다. 이러한 라이브러리는 일반적으로 사용할 수 있거나 프로덕션 지원 미리 보기에서 사용할 수 있으므로 프로덕션 응용 프로그램에서 사용 하는 것이 안전 합니다. 미리 보기 조건 및 사용 가능한 라이브러리에 대한 자세한 내용은 [인증 라이브러리 참조](../develop/reference-v2-libraries.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)에서 확인할 수 있습니다.
* Microsoft 라이브러리에서 다루지 않는 플랫폼의 경우 응용 프로그램 코드에서 프로토콜 메시지를 직접 전송 및 수신 하 여 Microsoft id 플랫폼 끝점과 통합할 수 있습니다. Openid connect Connect 및 OAuth 프로토콜은 이러한 통합을 수행 하는 데 도움이 되도록 [명시적으로 문서화 되어](../develop/active-directory-v2-protocols.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) 있습니다.
* 마지막으로, 오픈 소스 Openid connect Connect 및 OAuth 라이브러리를 사용 하 여 Microsoft id 플랫폼 끝점과 통합할 수 있습니다. Microsoft id 플랫폼 끝점은 변경 없이 많은 오픈 소스 프로토콜 라이브러리와 호환 되어야 합니다. 이러한 라이브러리의 사용 가능 여부는 언어 및 플랫폼마다 다릅니다. [OpenID Connect](https://openid.net/connect/) 및 [OAuth 2.0](https://oauth.net/2/) 웹 사이트는 주요 구현 목록을 유지 관리합니다. 자세한 내용은 [microsoft id 플랫폼 및 인증 라이브러리](../develop/reference-v2-libraries.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)및 microsoft id 플랫폼 끝점으로 테스트 한 오픈 소스 클라이언트 라이브러리 및 샘플 목록을 참조 하세요.
* 참조를 위해 Microsoft id 플랫폼 공통 끝점에 대 한 `.well-known` 끝점은 `https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration`됩니다. `common`을 테넌트 ID로 바꾸어 테넌트와 관련된 데이터를 가져오세요.  

### <a name="protocol-changes"></a>프로토콜 변경 내용

Microsoft id 플랫폼 끝점이 SAML 또는 WS-FEDERATION을 지원 하지 않습니다. Openid connect Connect 및 OAuth 2.0만 지원 합니다.  v1.0 엔드포인트에서 OAuth 2.0 프로토콜의 주목할 만한 변경 내용은 다음과 같습니다. 

* `email` 클레임은 선택적 클레임이 구성되어 있는 경우 **또는** 요청에서 범위=메일로 지정된 경우 반환됩니다. 
* 이제 `scope` 매개 변수 대신 `resource` 매개 변수가 지원됩니다.  
* OAuth 2.0 사양을 보다 잘 준수하도록 많은 응답이 수정되었습니다(예: `expires_in`을 string 대신 int로 올바르게 반환).  

Microsoft id 플랫폼 끝점에서 지원 되는 프로토콜 기능의 범위를 더 잘 이해 하려면 [Openid connect Connect 및 OAuth 2.0 프로토콜 참조](../develop/active-directory-v2-protocols.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)를 참조 하세요.

#### <a name="saml-restrictions"></a>SAML 제한 사항

Windows 응용 프로그램에서 Active Directory 인증 라이브러리 (ADAL)를 사용한 경우 SAML (Security Assertion Markup Language) 어설션을 사용 하는 Windows 통합 인증을 활용 했을 수 있습니다. 이 권한 부여를 통해 페더레이션된 Azure AD 테넌트의 사용자는 자격 증명을 입력하지 않고도 해당 온-프레미스 Active Directory 인스턴스로 자동으로 인증할 수 있습니다. SAML 어설션 부여는 Microsoft id 플랫폼 끝점에서 지원 되지 않습니다.
