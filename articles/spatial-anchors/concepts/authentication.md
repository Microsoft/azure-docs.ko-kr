---
title: 인증 및 권한 부여
description: 앱 또는 서비스가 Azure Spatial Anchors에 인증할 수 있는 다양한 방법과 Azure Spatial Anchors에 대한 액세스를 제어하는 제어 수준에 대해 알아봅니다.
author: craigktreasure
manager: vriveras
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 05/28/2019
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.custom: devx-track-csharp
ms.openlocfilehash: e4d25637498bec223e294eecf2be6dc88fa2aa0d
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "88997169"
---
# <a name="authentication-and-authorization-to-azure-spatial-anchors"></a>Azure Spatial Anchors에 대한 인증 및 권한 부여

이 섹션에서는 앱 또는 웹 서비스에서 Azure Spatial Anchors에 인증할 수 있는 다양한 방법과 Azure AD(Active Directory)의 역할 기반 액세스 제어를 사용하여 Spatial Anchors 계정에 대한 액세스를 제어하는 방법을 설명합니다.

## <a name="overview"></a>개요

![Azure Spatial Anchors에 대한 인증 개요](./media/spatial-anchors-authentication-overview.png)

지정된 Azure Spatial Anchors 계정에 액세스하려면 클라이언트는 먼저 Azure Mixed Reality STS(보안 토큰 서비스)에서 액세스 토큰을 가져와야 합니다. STS에서 가져온 토큰은 24시간 동안 유효하며, 포함된 Spatial Anchors 서비스에 대한 정보를 통해 계정에 대한 권한 부여를 결정하고 권한 있는 사용자만이 계정에 액세스할 수 있도록 합니다.

액세스 토큰은 계정 키 또는 Azure AD에서 발급한 토큰에서 교환하여 얻을 수 있습니다.

계정 키를 통해 Azure Spatial Anchors 서비스 사용을 빠르게 시작할 수 있습니다. 그러나 애플리케이션을 프로덕션 환경에 배포하기 전에 Azure AD 기반 인증을 사용하도록 앱을 업데이트하는 것이 좋습니다.

Azure AD 인증 토큰은 다음 두 가지 방법으로 가져올 수 있습니다.

- Azure AD를 ID 시스템으로 사용하는 회사에서 엔터프라이즈 애플리케이션을 빌드하는 경우 앱에서 사용자 기반 Azure AD 인증을 사용할 수 있으며 기존 Azure AD 보안 그룹을 사용하여 Spatial Anchors 계정에 액세스 권한을 부여하거나 조직의 사용자에게 직접 액세스 권한을 부여할 수 있습니다.
- 그렇지 않은 경우에는 앱을 지원하는 웹 서비스에서 Azure AD 토큰을 가져오는 것이 좋습니다. 클라이언트 애플리케이션에는 Azure Spatial Anchors에 액세스하기 위한 자격 증명이 포함되지 않기 때문에 프로덕션 애플리케이션 인증은 지원 웹 서비스를 사용하는 것이 좋습니다.

## <a name="account-keys"></a>계정 키

가장 간단한 시작 방법은 계정 키를 사용하여 Azure Spatial Anchors 계정에 액세스하는 것입니다. Azure Portal에서 계정 키를 찾을 수 있습니다. 계정으로 이동하여 "키" 탭을 선택합니다.

![Azure Spatial Anchors에 대한 인증 개요](../../../includes/media/spatial-anchors-get-started-create-resource/view-account-key.png)

Spatial Anchors 계정에 동시에 액세스할 수 있는 두 개의 유효한 키를 사용할 수 있습니다. 계정에 액세스하는 데 사용하는 키를 정기적으로 업데이트하는 것이 좋습니다. 두 개의 유효한 키를 사용하면 가동 중지 시간 없이 이러한 업데이트를 사용할 수 있습니다. 기본 키와 보조 키만 교대로 업데이트하면 됩니다.

SDK는 계정 키 인증을 기본적으로 지원합니다. cloudSession 개체에서 AccountKey 속성을 설정하기만 하면 됩니다.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
this.cloudSession.Configuration.AccountKey = @"MyAccountKey";
```

# <a name="objc"></a>[ObjC](#tab/objc)

```objc
_cloudSession.configuration.accountKey = @"MyAccountKey";
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
_cloudSession!.configuration.accountKey = "MyAccountKey"
```

# <a name="java"></a>[Java](#tab/java)

```java
mCloudSession.getConfiguration().setAccountKey("MyAccountKey");
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
auto configuration = cloudSession_->Configuration();
configuration->AccountKey(R"(MyAccountKey)");
```

# <a name="c-winrt"></a>[C++ WinRT](#tab/cppwinrt)

```cpp
auto configuration = m_cloudSession.Configuration();
configuration.AccountKey(LR"(MyAccountKey)");
```

---

이 작업이 완료되면 SDK가 액세스 토큰에 대한 계정 키의 교환 및 앱에 필요한 토큰 캐싱을 처리합니다.

> [!WARNING]
> 빠른 온보딩에는 계정 키를 사용하는 것이 좋지만 개발/프로토타입 중에만 사용해야 합니다. 애플리케이션에 포함된 계정 키를 사용하여 프로덕션에 애플리케이션을 제공하지 않고 다음에 나열된 사용자 또는 서비스 기반 Azure AD 인증 방법을 사용하는 것이 좋습니다.

## <a name="azure-ad-user-authentication"></a>Azure AD 사용자 인증

Azure Active Directory 사용자를 대상으로 하는 애플리케이션의 경우 사용자에게 Azure AD 토큰을 사용하는 것이 좋으며 이는 [MSAL 라이브러리](../../active-directory/develop/msal-overview.md)를 사용하여 얻을 수 있습니다. [앱 빠른 시작 등록](../../active-directory/develop/quickstart-register-app.md)에 나열된 다음 단계를 수행해야 합니다.

1. Azure Portal의 구성
    1.  Azure AD에서 애플리케이션을 **네이티브 애플리케이션**으로 등록합니다. 등록하는 과정에서 애플리케이션이 다중 테넌트인지 여부를 확인하고 애플리케이션에 허용되는 리디렉션 URL을 제공해야 합니다.
        1.  **API 사용 권한** 탭으로 전환합니다.
        2.  **사용 권한 추가**를 선택합니다.
            1.  **내 조직에서 사용하는 API** 탭에서 **Microsoft Mixed Reality**를 선택합니다.
            2.  **위임된 권한**을 선택합니다.
            3.  **mixedreality**에서 **mixedreality.signin**에 대한 확인란을 선택합니다.
            4.  **사용 권한 추가**를 선택합니다.
        3.  **관리자 동의 허용**을 선택합니다.
    2.  리소스에 애플리케이션 또는 사용자 액세스 권한을 부여합니다.
        1.  Azure Portal의 Spatial Anchors 리소스로 이동합니다.
        2.  **액세스 제어(IAM)** 탭으로 전환합니다.
        3.  **역할 할당 추가**를 누릅니다.
            1.  [역할을 선택합니다.](#role-based-access-control)
            2.  **선택** 필드에서 액세스 권한을 할당하려는 사용자, 그룹 및/또는 애플리케이션의 이름을 입력합니다.
            3.  **저장**을 누릅니다.
2. 코드에서:
    1.  Azure AD 애플리케이션의 **애플리케이션 ID** 및 **리디렉션 URI**를 MSAL의 **클라이언트 ID** 및 **RedirectUri** 매개 변수로 사용해야 합니다.
    2.  테넌트 정보 설정:
        1.  애플리케이션이 **내 조직만**을 지원하는 경우 이 값을 **테넌트 ID** 또는 **테넌트 이름**(예: contoso.microsoft.com)으로 바꿉니다.
        2.  애플리케이션이 **모든 조직 디렉터리의 계정**을 지원하는 경우 이 값을 **조직**으로 바꿉니다.
        3.  애플리케이션이 **모든 Microsoft 계정 사용자**를 지원하는 경우 이 값을 **공용**으로 바꿉니다.
    3.  토큰 요청에서 **범위**를 "https://sts.mixedreality.azure.com//.default"로 설정합니다. 이 범위는 애플리케이션이 Mixed Reality STS(보안 토큰 서비스)에 대한 토큰을 요청하고 있음을 Azure AD에 나타냅니다.

이를 통해 애플리케이션은 MSAL에서 Azure AD 토큰을 얻을 수 있어야 합니다. 클라우드 세션 구성 개체에서 Azure AD 토큰을 **authenticationToken**으로 설정할 수 있습니다.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
this.cloudSession.Configuration.AuthenticationToken = @"MyAuthenticationToken";
```

# <a name="objc"></a>[ObjC](#tab/objc)

```objc
_cloudSession.configuration.authenticationToken = @"MyAuthenticationToken";
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
_cloudSession!.configuration.authenticationToken = "MyAuthenticationToken"
```

# <a name="java"></a>[Java](#tab/java)

```java
mCloudSession.getConfiguration().setAuthenticationToken("MyAuthenticationToken");
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
auto configuration = cloudSession_->Configuration();
configuration->AuthenticationToken(R"(MyAuthenticationToken)");
```

# <a name="c-winrt"></a>[C++ WinRT](#tab/cppwinrt)

```cpp
auto configuration = m_cloudSession.Configuration();
configuration.AuthenticationToken(LR"(MyAuthenticationToken)");
```

---

## <a name="azure-ad-service-authentication"></a>Azure AD 서비스 인증

프로덕션에 Azure Spatial Anchors를 활용하는 앱을 배포하려면 인증 요청을 브로커하는 백 엔드 서비스를 활용하도록 선택하는 것이 좋습니다. 일반적인 체계는 다음 다이어그램에 설명된 대로 되어야 합니다.

![Azure Spatial Anchors에 대한 인증 개요](./media/spatial-anchors-aad-authentication.png)

여기서는 앱이 자체 메커니즘(예: Microsoft 계정, PlayFab, Facebook, Google ID, 사용자 지정 사용자 이름/암호 등)을 사용하여 백 엔드 서비스에 인증한다고 가정합니다. 사용자가 백 엔드 서비스에 인증되면, 해당 서비스는 Azure AD 토큰을 검색하여 Azure Spatial Anchors에 대한 액세스 토큰용으로 교환하며 클라이언트 애플리케이션으로 다시 반환할 수 있습니다.

Azure AD 액세스 토큰은 [MSAL 라이브러리](../../active-directory/develop/msal-overview.md)를 사용하여 검색됩니다. [앱 빠른 시작 등록](../../active-directory/develop/quickstart-register-app.md)에 나열된 다음 단계를 수행해야 합니다.

1.  Azure Portal의 구성:
    1.  Azure AD에 애플리케이션을 등록합니다.
        1.  Azure Portal에서 **Azure Active Directory**로 이동하고 **앱 등록**을 선택합니다.
        2.  **새 애플리케이션 등록**을 선택합니다.
        3.  애플리케이션 이름을 입력하고 애플리케이션 유형으로 **웹앱/API**를 선택한 후 서비스의 인증 URL을 입력합니다. 그런 다음 **만들기**를 누릅니다.
        4.  해당 응용 프로그램에서 **설정**을 클릭 한 다음 **인증서 및 암호** 탭을 선택 합니다. 새 클라이언트 암호를 만들고, 기간을 선택 하 고, **추가**를 누릅니다. 웹 서비스의 코드에 포함 해야 하므로 비밀 값을 저장 해야 합니다.
    2.  애플리케이션 및/또는 사용자에게 리소스에 대한 액세스 권한을 부여합니다.
        1.  Azure Portal의 Spatial Anchors 리소스로 이동합니다.
        2.  **액세스 제어(IAM)** 탭으로 전환합니다.
        3.  **역할 할당 추가**를 누릅니다.
        1.  [역할을 선택합니다.](#role-based-access-control)
        2.  **선택** 필드에 만든 애플리케이션의 이름을 입력하고 액세스 권한을 할당합니다. 앱의 사용자에게 Spatial Anchors 계정에 다른 역할을 지정하려면 Azure AD에서 여러 애플리케이션을 등록하고 각 사용자에게 별도 역할을 할당해야 합니다. 그런 다음 사용자에게 올바른 역할을 사용하도록 권한 부여 논리를 구현합니다.
        3.  참고- **역할 할당 추가** 선택에서 **할당 액세스** 를 "Azure AD 사용자, 그룹 또는 서비스 사용자"로 설정 하려고 합니다.
    3.  **저장**을 누릅니다.
2.  코드에서(참고: GitHub에 포함된 서비스 샘플을 사용할 수 있음):
    1.  고유의 Azure AD 애플리케이션의 애플리케이션 ID, 애플리케이션 비밀 및 리디렉션 URI를 MSAL의 클라이언트 ID, 비밀 및 RedirectUri 매개 변수로 사용해야 합니다.
    2.  MSAL의 인증 기관 매개 변수에서 테넌트 ID를 고유의 Azure ADD 테넌트 ID로 설정합니다.
    3.  토큰 요청에서 **범위**를 "https://sts.mixedreality.azure.com//.default"로 설정합니다.

이를 통해 백 엔드 서비스는 Azure AD 토큰을 검색할 수 있습니다. 그런 다음 클라이언트로 다시 반환할 MR 토큰으로 교환할 수 있습니다. Azure AD 토큰을 사용한 MR 토큰 검색은 REST 호출을 통해 수행됩니다. 다음은 샘플 호출입니다.

```
GET https://sts.mixedreality.azure.com/Accounts/35d830cb-f062-4062-9792-d6316039df56/token HTTP/1.1
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1Ni<truncated>FL8Hq5aaOqZQnJr1koaQ
Host: sts.mixedreality.azure.com
Connection: Keep-Alive

HTTP/1.1 200 OK
Date: Sun, 24 Feb 2019 08:00:00 GMT
Content-Type: application/json; charset=utf-8
Content-Length: 1153
Accept: application/json
MS-CV: 05JLqWeKFkWpbdY944yl7A.0
{"AccessToken":"eyJhbGciOiJSUzI1NiIsImtpZCI6IjI2MzYyMTk5ZTI2NjQxOGU4ZjE3MThlM2IyMThjZTIxIiwidHlwIjoiSldUIn0.eyJqdGkiOiJmMGFiNWIyMy0wMmUxLTQ1MTQtOWEzNC0xNzkzMTA1NTc4NzAiLCJjYWkiOiIzNWQ4MzBjYi1mMDYyLTQwNjItOTc5Mi1kNjMxNjAzOWRmNTYiLCJ0aWQiOiIwMDAwMDAwMC0wMDAwLTAwMDAtMDAwMC0wMDAwMDAwMDAwMDAiLCJhaWQiOiIzNWQ4MzBjYi1mMDYyLTQwNjItOTc5Mi1kNjMxNjAzOWRmNTYiLCJhYW8iOi0xLCJhcHIiOiJlYXN0dXMyIiwicmlkIjoiL3N1YnNjcmlwdGlvbnMvNzIzOTdlN2EtNzA4NC00ODJhLTg3MzktNjM5Y2RmNTMxNTI0L3Jlc291cmNlR3JvdXBzL3NhbXBsZV9yZXNvdXJjZV9ncm91cC9wcm92aWRlcnMvTWljcm9zb2Z0Lk1peGVkUmVhbGl0eS9TcGF0aWFsQW5jaG9yc0FjY291bnRzL2RlbW9fYWNjb3VudCIsIm5iZiI6MTU0NDU0NzkwMywiZXhwIjoxNTQ0NjM0MzAzLCJpYXQiOjE1NDQ1NDc5MDMsImlzcyI6Imh0dHBzOi8vbXJjLWF1dGgtcHJvZC50cmFmZmljbWFuYWdlci5uZXQvIiwiYXVkIjoiaHR0cHM6Ly9tcmMtYW5jaG9yLXByb2QudHJhZmZpY21hbmFnZXIubmV0LyJ9.BFdyCX9UJj0i4W3OudmNUiuaGgVrlPasNM-5VqXdNAExD8acFJnHdvSf6uLiVvPiQwY1atYyPbOnLYhEbIcxNX-YAfZ-xyxCKYb3g_dbxU2w8nX3zDz_X3XqLL8Uha-rkapKbnNgxq4GjM-EBMCill2Svluf9crDmO-SmJbxqIaWzLmlUufQMWg_r8JG7RLseK6ntUDRyDgkF4ex515l2RWqQx7cw874raKgUO4qlx0cpBAB8cRtGHC-3fA7rZPM7UQQpm-BC3suXqRgROTzrKqfn_g-qTW4jAKBIXYG7iDefV2rGMRgem06YH_bDnpkgUa1UgJRRTckkBuLkO2FvA"}
```

권한 부여 헤더의 형식은 다음과 같습니다. `Bearer <Azure_AD_token>`

응답에는 일반 텍스트의 MR 토큰이 포함되어 있습니다.

그러면 MR 토큰이 클라이언트로 반환됩니다. 그런 다음 클라이언트 앱의 클라우드 세션 구성에서 액세스 토큰으로 설정할 수 있습니다.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
this.cloudSession.Configuration.AccessToken = @"MyAccessToken";
```

# <a name="objc"></a>[ObjC](#tab/objc)

```objc
_cloudSession.configuration.accessToken = @"MyAccessToken";
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
_cloudSession!.configuration.accessToken = "MyAccessToken"
```

# <a name="java"></a>[Java](#tab/java)

```java
mCloudSession.getConfiguration().setAccessToken("MyAccessToken");
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
auto configuration = cloudSession_->Configuration();
configuration->AccessToken(R"(MyAccessToken)");
```

# <a name="c-winrt"></a>[C++ WinRT](#tab/cppwinrt)

```cpp
auto configuration = m_cloudSession.Configuration();
configuration.AccessToken(LR"(MyAccessToken)");
```

---

## <a name="role-based-access-control"></a>역할 기반 액세스 제어

애플리케이션, 서비스 또는 서비스의 Azure AD 사용자에게 부여되는 액세스 수준을 제어할 수 있도록 만든 다음 역할을 필요에 따라 Azure Spatial Anchors 계정에 할당할 수 있습니다.

- **Spatial Anchors 계정 소유자**: 이 역할의 애플리케이션 또는 사용자는 Spatial Anchors를 만들고, 이를 쿼리하고, 삭제할 수 있습니다. 계정 키를 사용하여 계정에 인증하는 경우 **Spatial Anchors 계정 소유자** 역할이 인증된 보안 주체에 할당됩니다.
- **Spatial Anchors 계정 참여자**: 이 역할의 애플리케이션 또는 사용자는 Spatial Anchors를 만들 수 있으며, 이를 쿼리하지만 삭제할 수 없습니다.
- **Spatial Anchors 계정 판독기**: 이 역할의 애플리케이션 또는 사용자는 Spatial Anchors에 대해서 쿼리할 수는 있지만 새 항목을 만들고 기존 항목을 삭제하거나 Spatial Anchors에서 메타데이터를 업데이트할 수 없습니다. 이는 일반적으로 일부 사용자가 환경을 큐레이팅하는 애플리케이션에 사용되지만, 다른 사용자는 이전에 해당 환경에 배치된 앵커만 회수할 수 있습니다.

## <a name="next-steps"></a>다음 단계

Azure Spatial Anchors를 사용하여 첫 번째 앱을 만듭니다.

> [!div class="nextstepaction"]
> [Unity(HoloLens)](../quickstarts/get-started-unity-hololens.md)

> [!div class="nextstepaction"]
> [Unity(iOS)](../quickstarts/get-started-unity-ios.md)

> [!div class="nextstepaction"]
> [Unity(Android)](../quickstarts/get-started-unity-android.md)

> [!div class="nextstepaction"]
> [iOS](../quickstarts/get-started-ios.md)

> [!div class="nextstepaction"]
> [Android](../quickstarts/get-started-android.md)

> [!div class="nextstepaction"]
> [HoloLens](../quickstarts/get-started-hololens.md)

> [!div class="nextstepaction"]
> [Xamarin(Android)](../quickstarts/get-started-xamarin-android.md)

> [!div class="nextstepaction"]
> [Xamarin(iOS)](../quickstarts/get-started-xamarin-ios.md)
