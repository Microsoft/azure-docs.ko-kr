---
title: V 1.0 앱 (MSAL)의 범위 | Microsoft
description: MSAL(Microsoft 인증 라이브러리)을 사용하여 v1.0 애플리케이션에 대한 범위를 알아봅니다.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/25/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: e0ef46d23ba267bbfc1ff539eb659fafe2b44f8e
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/08/2020
ms.locfileid: "77085689"
---
# <a name="scopes-for-a-web-api-accepting-v10-tokens"></a>v1.0 토큰을 허용하는 Web API에 대한 범위

OAuth2 권한은 개발자 용 Azure Active Directory (Azure AD) 웹 API (리소스) 응용 프로그램이 클라이언트 응용 프로그램에 노출 하는 사용 권한 범위입니다. 이러한 권한 범위를 동의를 통해 클라이언트 애플리케이션에 부여할 수 있습니다. `oauth2Permissions`Azure Active Directory 애플리케이션 매니페스트 참조[에서 ](reference-app-manifest.md#manifest-reference) 관련 섹션을 참조하세요.

## <a name="scopes-to-request-access-to-specific-oauth2-permissions-of-a-v10-application"></a>v1.0 애플리케이션의 특정 OAuth2 권한에 대한 액세스를 요청하는 범위

V1.0 응용 프로그램의 특정 범위에 대 한 토큰을 획득 하려면 (예: https://graph.windows.net)하는 Azure AD graph) 원하는 리소스 식별자를 해당 리소스에 대 한 원하는 OAuth2 사용 권한과 연결 하 여 범위를 만듭니다.

예를 들어 앱 ID URI가 `ResourceId`인 v1.0 web API에 사용자를 대신하여 액세스하려면 다음과 같습니다.

```csharp
var scopes = new [] {  ResourceId+"/user_impersonation"};
```

```javascript
var scopes = [ ResourceId + "/user_impersonation"];
```

Azure AD Graph API (https:\//graph.windows.net/)를 사용 하 여 MSAL.NET Azure AD에 대 한 읽기 및 쓰기를 수행 하려면 다음 예제와 같이 범위 목록을 만들어야 합니다.

```csharp
string ResourceId = "https://graph.windows.net/";
var scopes = new [] { ResourceId + "Directory.Read", ResourceID + "Directory.Write"}
```

```javascript
var ResourceId = "https://graph.windows.net/";
var scopes = [ ResourceId + "Directory.Read", ResourceID + "Directory.Write"];
```

Azure Resource Manager API (https:\//management.core.windows.net/)에 해당 하는 범위를 작성 하려면 다음 범위를 요청 해야 합니다 (두 개의 슬래시 참고).

```csharp
var scopes = new[] {"https://management.core.windows.net//user_impersonation"};
var result = await app.AcquireTokenInteractive(scopes).ExecuteAsync();

// then call the API: https://management.azure.com/subscriptions?api-version=2016-09-01
```

> [!NOTE]
> Azure Resource Manager API에는 해당 대상 그룹 클레임(aud)에 슬래시가 필요하고, 범위에서 API 이름을 분리하기 위한 슬래시가 있기 때문에 슬래시를 두 개 사용해야 합니다.

Azure AD에서 사용하는 논리는 다음과 같습니다.

- V2.0 액세스 토큰이 포함 된 ADAL (Azure AD v1.0) 엔드포인트의 경우 (가능한 경우) aud = resource
- MSAL (v2.0 (Microsoft identity platform))의 경우 v2.0 토큰을 수락 하는 리소스에 대 한 액세스 토큰을 요청 하는 엔드포인트 `aud=resource.AppId`
- V 1.0 액세스 토큰을 허용 하는 리소스 (위의 경우)에 대 한 액세스 토큰을 요청 하는 MSAL (v2.0 엔드포인트)의 경우, Azure AD는 마지막 슬래시 앞에 있는 모든 항목을 사용 하 고 리소스 식별자로 사용 하 여 요청 된 범위에서 원하는 대상 그룹을 구문 분석 합니다. 따라서 https:\//database.windows.net에 "https:\//database.windows.net/"의 대상이 필요한 경우 "https:\//database.windows.net//.default" 범위를 요청 해야 합니다. 참고 항목: GitHub 문제 [#747: 리소스 url의 후행 슬래시가 생략 되어 sql 인증 오류가 발생](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/747)했습니다.

## <a name="scopes-to-request-access-to-all-the-permissions-of-a-v10-application"></a>v1.0 애플리케이션의 모든 권한에 대한 액세스를 요청하는 범위

v1.0 애플리케이션의 모든 정적 범위에 대한 토큰을 획득하려면 “default”를 API의 앱 ID URI에 추가합니다.

```csharp
ResourceId = "someAppIDURI";
var scopes = new [] {  ResourceId+"/.default"};
```

```javascript
var ResourceId = "someAppIDURI";
var scopes = [ ResourceId + "/.default"];
```

## <a name="scopes-to-request-for-a-client-credential-flowdaemon-app"></a>클라이언트 자격 증명 흐름/디먼 앱에 대 한 요청 범위

클라이언트 자격 증명 흐름의 경우 전달하는 범위도 `/.default`가 됩니다. 이렇게 하면 관리자가 애플리케이션 등록에서 동의한 모든 앱 수준 권한을 요청한다고 Azure AD에 알려줍니다.
