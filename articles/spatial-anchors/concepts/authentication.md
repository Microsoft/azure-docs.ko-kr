---
title: 인증 및 권한 부여
description: 앱 또는 서비스에서 Azure 공간 앵커에 인증할 수 있는 다양 한 방법과 Azure 공간 앵커에 대 한 액세스를 게이트 하는 제어 수준에 대해 알아봅니다.
author: julianparismorgan
manager: vriveras
services: azure-spatial-anchors
ms.author: pmorgan
ms.date: 05/28/2019
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.openlocfilehash: 3de84e2d814acfca67bc722243a90fa41f6536e1
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77161684"
---
# <a name="authentication-and-authorization-to-azure-spatial-anchors"></a>Azure 공간 앵커에 대 한 인증 및 권한 부여

이 섹션에서는 앱 또는 웹 서비스에서 Azure 공간 앵커로 인증할 수 있는 다양 한 방법과 Azure Directory (Azure AD)에서 역할 기반 Access Control를 사용 하 여 공간 앵커 계정에 대 한 액세스를 제어 하는 방법을 설명 합니다.

## <a name="overview"></a>개요

![Azure 공간 앵커로의 인증에 대 한 개요](./media/spatial-anchors-authentication-overview.png)

지정 된 Azure 공간 앵커 계정에 액세스 하려면 먼저 클라이언트가 Azure Mixed Reality STS (보안 토큰 서비스)에서 액세스 토큰을 가져와야 합니다. 24 시간 동안 STS에서 가져온 토큰은 공간 앵커 서비스에 대 한 정보를 포함 하 여 계정에 대 한 권한 부여를 결정 하 고 권한 있는 사용자만이 계정에 액세스할 수 있도록 합니다.

액세스 토큰은 계정 키 또는 Azure AD에서 발급 한 토큰에서 exchange를 통해 가져올 수 있습니다.

계정 키를 사용 하면 Azure 공간 앵커 서비스를 사용 하 여 빠르게 시작할 수 있습니다. 그러나 응용 프로그램을 프로덕션 환경에 배포 하기 전에 Azure AD 기반 인증을 사용 하도록 앱을 업데이트 하는 것이 좋습니다.

Azure AD 인증 토큰은 다음 두 가지 방법으로 가져올 수 있습니다.

- 엔터프라이즈 응용 프로그램을 빌드하고 회사에서 해당 id 시스템으로 Azure AD를 사용 하는 경우 앱에서 사용자 기반 Azure AD 인증을 사용 하 고 기존 Azure AD 보안 그룹을 사용 하 여 공간 앵커 계정에 대 한 액세스 권한을 부여할 수 있습니다. 조직의 사용자에 게 직접.
- 그렇지 않으면 앱을 지 원하는 웹 서비스에서 Azure AD 토큰을 가져오는 것이 좋습니다. 지원 웹 서비스를 사용 하는 것은 클라이언트 응용 프로그램에서 Azure 공간 앵커에 액세스 하기 위한 자격 증명을 포함 하지 않기 때문에 프로덕션 응용 프로그램에 대해 권장 되는 인증 방법입니다.

## <a name="account-keys"></a>계정 키

Azure 공간 앵커 계정에 액세스 하기 위해 계정 키를 사용 하는 것은 시작 하는 가장 간단한 방법입니다. Azure Portal에서 계정 키를 찾을 수 있습니다. 계정으로 이동 하 여 "키" 탭을 선택 합니다.

![Azure 공간 앵커로의 인증에 대 한 개요](../../../includes/media/spatial-anchors-get-started-create-resource/view-account-key.png)


두 개의 키를 사용할 수 있으며이는 모두 공간 앵커 계정에 액세스 하기 위해 동시에 유효 합니다. 계정에 액세스 하는 데 사용 하는 키를 정기적으로 업데이트 하는 것이 좋습니다. 두 개의 개별 유효한 키를 사용 하면 가동 중지 시간 없이 이러한 업데이트를 사용할 수 있습니다. 기본 키와 보조 키만 업데이트 하면 됩니다.

SDK는 계정 키를 사용 하 여 인증을 기본적으로 지원 합니다. cloudSession 개체에서 AccountKey 속성을 설정 하기만 하면 됩니다.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
this.cloudSession.Configuration.AccountKey = @"MyAccountKey";
```

# <a name="objctabobjc"></a>[ObjC](#tab/objc)

```objc
_cloudSession.configuration.accountKey = @"MyAccountKey";
```

# <a name="swifttabswift"></a>[Swift](#tab/swift)

```swift
_cloudSession!.configuration.accountKey = "MyAccountKey"
```

# <a name="javatabjava"></a>[Java](#tab/java)

```java
mCloudSession.getConfiguration().setAccountKey("MyAccountKey");
```

# <a name="c-ndktabcpp"></a>[C++NDK](#tab/cpp)

```cpp
auto configuration = cloudSession_->Configuration();
configuration->AccountKey(R"(MyAccountKey)");
```

# <a name="c-winrttabcppwinrt"></a>[C++형](#tab/cppwinrt)

```cpp
auto configuration = m_cloudSession.Configuration();
configuration.AccountKey(LR"(MyAccountKey)");
```

---

이 작업이 완료 되 면 SDK는 액세스 토큰에 대 한 계정 키의 교환과 앱에 필요한 토큰의 캐싱을 처리 합니다.

> [!WARNING]
> 계정 키를 사용 하는 것은 신속한 탑재를 위해 사용 하는 것이 좋습니다. 여기에 포함 된 계정 키를 사용 하 여 프로덕션에 응용 프로그램을 제공 하지 않고 다음에 나열 된 사용자 기반 또는 서비스 기반 Azure AD 인증 방법을 사용 하는 것이 좋습니다.

## <a name="azure-ad-user-authentication"></a>Azure AD 사용자 인증

Azure Active Directory 사용자를 대상으로 하는 응용 프로그램의 경우 사용자에 대 한 Azure AD 토큰을 사용 하는 것이 좋습니다 .이는 [Msal 라이브러리](../../active-directory/develop/msal-overview.md)를 사용 하 여 얻을 수 있습니다. [앱 등록 빠른](../../active-directory/develop/quickstart-register-app.md)시작에 나열 된 단계를 따라야 합니다. 여기에는 다음이 포함 됩니다.

1. Azure Portal 구성
    1.  응용 프로그램을 **네이티브 응용 프로그램**으로 Azure AD에 등록 합니다. 등록 하는 과정에서 응용 프로그램이 다중 테 넌 트 인지 여부를 확인 하 고 응용 프로그램에 허용 되는 리디렉션 Url을 제공 해야 합니다.
        1.  **API 사용 권한** 탭으로 전환 합니다.
        2.  **권한 추가를 선택 합니다** .
            1.  내 조직에서 탭을 **사용 하 여** **혼합 현실 리소스 공급자** 를 선택 합니다.
            2.  **위임 된 권한** 선택
            3.  **Mixedreality** 의 mixedreality에 대 한 확인란을 선택 합니다 **.**
            4.  **권한 추가** 를 선택 합니다.
        3.  **관리자 동의 부여** 를 선택 합니다.
    2.  응용 프로그램 또는 사용자에 게 리소스에 대 한 액세스 권한을 부여 합니다.
        1.  Azure Portal의 공간 앵커 리소스로 이동 합니다.
        2.  **액세스 제어 (IAM)** 탭으로 전환 합니다.
        3.  **역할 할당 추가** 적중
            1.  [역할 선택](#role-based-access-control)
            2.  **선택** 필드에 액세스를 할당 하려는 사용자, 그룹 및/또는 응용 프로그램의 이름을 입력 합니다 (예).
            3.  **저장**을 누릅니다.
2. 코드에서 다음을 수행 합니다.
    1.  사용자 고유의 Azure AD 응용 프로그램의 **응용 프로그램 ID** 및 **리디렉션 uri** 를 ADAL의 **클라이언트 ID** 및 **redirecturi** 매개 변수로 사용 해야 합니다.
    2.  테 넌 트 정보 설정:
        1.  응용 프로그램에서 **내 조직만**지 원하는 경우이 값을 **테 넌 트 ID** 또는 **테 넌 트 이름** (예: contoso.microsoft.com)으로 바꿉니다.
        2.  응용 프로그램에서 **조직 디렉터리의 계정을**지 원하는 경우이 값을 **조직** 으로 바꿉니다.
        3.  응용 프로그램에서 **모든 Microsoft 계정 사용자**를 지 원하는 경우이 값을 **일반** 값으로 바꿉니다.
    3.  토큰 요청에서 **리소스** 를 "https://sts.mixedreality.azure.com"로 설정 합니다. 이 "리소스"는 응용 프로그램이 Azure 공간 앵커 서비스에 대 한 토큰을 요청 하 고 있음을 Azure AD에 표시 합니다.

이를 통해 응용 프로그램은 MSAL에서 Azure AD 토큰을 얻을 수 있어야 합니다. 클라우드 세션 구성 개체에서 Azure AD 토큰을 **Authenticationtoken** 로 설정할 수 있습니다.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
this.cloudSession.Configuration.AuthenticationToken = @"MyAuthenticationToken";
```

# <a name="objctabobjc"></a>[ObjC](#tab/objc)

```objc
_cloudSession.configuration.authenticationToken = @"MyAuthenticationToken";
```

# <a name="swifttabswift"></a>[Swift](#tab/swift)

```swift
_cloudSession!.configuration.authenticationToken = "MyAuthenticationToken"
```

# <a name="javatabjava"></a>[Java](#tab/java)

```java
mCloudSession.getConfiguration().setAuthenticationToken("MyAuthenticationToken");
```

# <a name="c-ndktabcpp"></a>[C++NDK](#tab/cpp)

```cpp
auto configuration = cloudSession_->Configuration();
configuration->AuthenticationToken(R"(MyAuthenticationToken)");
```

# <a name="c-winrttabcppwinrt"></a>[C++형](#tab/cppwinrt)

```cpp
auto configuration = m_cloudSession.Configuration();
configuration.AuthenticationToken(LR"(MyAuthenticationToken)");
```

---

## <a name="azure-ad-service-authentication"></a>Azure AD 서비스 인증

프로덕션에 Azure 공간 앵커를 활용 하는 앱을 배포 하는 권장 옵션은 인증 요청을 브로커 하는 백 엔드 서비스를 활용 하는 것입니다. 일반적인 체계는 다음 다이어그램에 설명 된 것과 같아야 합니다.

![Azure 공간 앵커로의 인증에 대 한 개요](./media/spatial-anchors-aad-authentication.png)

여기서는 앱이 자체 메커니즘 (예: Microsoft 계정, PlayFab, Facebook, Google ID, 사용자 지정 사용자 이름/암호 등)을 사용 하 여 백 엔드 서비스를 인증 하는 것으로 가정 합니다. 사용자가 백 엔드 서비스에 인증 되 면 해당 서비스는 Azure AD 토큰을 검색 하 고, Azure 공간 앵커에 대 한 액세스 토큰에 대해 교환 하 고, 클라이언트 응용 프로그램에 다시 반환할 수 있습니다.

Azure AD 액세스 토큰은 [Msal 라이브러리](../../active-directory/develop/msal-overview.md)를 사용 하 여 검색 됩니다. [앱 등록 빠른](../../active-directory/develop/quickstart-register-app.md)시작에 나열 된 단계를 따라야 합니다. 여기에는 다음이 포함 됩니다.

1.  Azure Portal 구성:
    1.  Azure AD에서 응용 프로그램을 등록 합니다.
        1.  Azure Portal에서 **Azure Active Directory**로 이동 하 고 **앱 등록** 을 선택 합니다.
        2.  **새 응용 프로그램 등록** 을 선택 합니다.
        3.  응용 프로그램의 이름을 입력 하 고, 응용 프로그램 유형으로 **웹 앱/** a p i를 선택 하 고, 서비스에 대 한 인증 URL을 입력 합니다. 그런 다음 **만들기**를 누릅니다.
        4.  해당 응용 프로그램에서 **설정**을 누르고 **키** 탭을 선택 합니다. 키 이름을 입력 하 고, 기간을 선택 하 고, **저장**을 누릅니다. 웹 서비스의 코드에 포함 해야 하므로 해당 시간에 표시 되는 키 값을 저장 해야 합니다.
    2.  응용 프로그램 및/또는 사용자에 게 리소스에 대 한 액세스 권한을 부여 합니다.
        1.  Azure Portal의 공간 앵커 리소스로 이동 합니다.
        2.  **액세스 제어 (IAM)** 탭으로 전환 합니다.
        3.  **역할 할당 추가** 적중
        1.  [역할 선택](#role-based-access-control)
        2.  **선택** 필드에서 사용자가 만든 응용 프로그램의 이름을 입력 하 고 액세스 권한을 할당 합니다. 앱의 사용자에 게 공간 앵커 계정에 대해 다른 역할을 지정 하려면 Azure AD에서 여러 응용 프로그램을 등록 하 고 별도의 각 역할에 할당 해야 합니다. 그런 다음 사용자에 게 올바른 역할을 사용 하도록 권한 부여 논리를 구현 합니다.
    3.  **저장**을 누릅니다.
2.  코드에서 (참고: GitHub에 포함 된 서비스 샘플을 사용할 수 있음):
    1.  사용자 고유의 Azure AD 응용 프로그램의 응용 프로그램 ID, 응용 프로그램 비밀 및 리디렉션 Uri를 ADAL의 클라이언트 ID, 비밀 및 RedirectUri 매개 변수로 사용 해야 합니다.
    2.  사용자 고유의 AAAzure 테 넌 트 id를 ADAL의 authority 매개 변수에 추가 테 넌 트 id로 설정 합니다.
    3.  토큰 요청에서 **리소스** 를 "https://sts.mixedreality.azure.com"로 설정 합니다.

이를 통해 백 엔드 서비스는 Azure AD 토큰을 검색할 수 있습니다. 그런 다음 클라이언트에 다시 반환 될 MR 토큰에 대해이를 교환할 수 있습니다. Azure AD 토큰을 사용 하 여 MR 토큰을 검색 하는 작업은 REST 호출을 통해 수행 됩니다. 다음은 샘플 호출입니다.

```
GET https://mrc-auth-prod.trafficmanager.net/Accounts/35d830cb-f062-4062-9792-d6316039df56/token HTTP/1.1
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1Ni<truncated>FL8Hq5aaOqZQnJr1koaQ
Host: mrc-auth-prod.trafficmanager.net
Connection: Keep-Alive

HTTP/1.1 200 OK
Date: Sun, 24 Feb 2019 08:00:00 GMT
Content-Type: application/json; charset=utf-8
Content-Length: 1153
Accept: application/json
MS-CV: 05JLqWeKFkWpbdY944yl7A.0
{"AccessToken":"eyJhbGciOiJSUzI1NiIsImtpZCI6IjI2MzYyMTk5ZTI2NjQxOGU4ZjE3MThlM2IyMThjZTIxIiwidHlwIjoiSldUIn0.eyJqdGkiOiJmMGFiNWIyMy0wMmUxLTQ1MTQtOWEzNC0xNzkzMTA1NTc4NzAiLCJjYWkiOiIzNWQ4MzBjYi1mMDYyLTQwNjItOTc5Mi1kNjMxNjAzOWRmNTYiLCJ0aWQiOiIwMDAwMDAwMC0wMDAwLTAwMDAtMDAwMC0wMDAwMDAwMDAwMDAiLCJhaWQiOiIzNWQ4MzBjYi1mMDYyLTQwNjItOTc5Mi1kNjMxNjAzOWRmNTYiLCJhYW8iOi0xLCJhcHIiOiJlYXN0dXMyIiwicmlkIjoiL3N1YnNjcmlwdGlvbnMvNzIzOTdlN2EtNzA4NC00ODJhLTg3MzktNjM5Y2RmNTMxNTI0L3Jlc291cmNlR3JvdXBzL3NhbXBsZV9yZXNvdXJjZV9ncm91cC9wcm92aWRlcnMvTWljcm9zb2Z0Lk1peGVkUmVhbGl0eS9TcGF0aWFsQW5jaG9yc0FjY291bnRzL2RlbW9fYWNjb3VudCIsIm5iZiI6MTU0NDU0NzkwMywiZXhwIjoxNTQ0NjM0MzAzLCJpYXQiOjE1NDQ1NDc5MDMsImlzcyI6Imh0dHBzOi8vbXJjLWF1dGgtcHJvZC50cmFmZmljbWFuYWdlci5uZXQvIiwiYXVkIjoiaHR0cHM6Ly9tcmMtYW5jaG9yLXByb2QudHJhZmZpY21hbmFnZXIubmV0LyJ9.BFdyCX9UJj0i4W3OudmNUiuaGgVrlPasNM-5VqXdNAExD8acFJnHdvSf6uLiVvPiQwY1atYyPbOnLYhEbIcxNX-YAfZ-xyxCKYb3g_dbxU2w8nX3zDz_X3XqLL8Uha-rkapKbnNgxq4GjM-EBMCill2Svluf9crDmO-SmJbxqIaWzLmlUufQMWg_r8JG7RLseK6ntUDRyDgkF4ex515l2RWqQx7cw874raKgUO4qlx0cpBAB8cRtGHC-3fA7rZPM7UQQpm-BC3suXqRgROTzrKqfn_g-qTW4jAKBIXYG7iDefV2rGMRgem06YH_bDnpkgUa1UgJRRTckkBuLkO2FvA"}
```

권한 부여 헤더의 형식은 다음과 같습니다. `Bearer <accoundId>:<accountKey>`

응답에는 일반 텍스트의 MR 토큰이 포함 되어 있습니다.

그러면 MR 토큰이 클라이언트에 반환 됩니다. 그런 다음 클라이언트 앱에서 클라우드 세션 구성의 액세스 토큰으로 설정할 수 있습니다.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
this.cloudSession.Configuration.AccessToken = @"MyAccessToken";
```

# <a name="objctabobjc"></a>[ObjC](#tab/objc)

```objc
_cloudSession.configuration.accessToken = @"MyAccessToken";
```

# <a name="swifttabswift"></a>[Swift](#tab/swift)

```swift
_cloudSession!.configuration.accessToken = "MyAccessToken"
```

# <a name="javatabjava"></a>[Java](#tab/java)

```java
mCloudSession.getConfiguration().setAccessToken("MyAccessToken");
```

# <a name="c-ndktabcpp"></a>[C++NDK](#tab/cpp)

```cpp
auto configuration = cloudSession_->Configuration();
configuration->AccessToken(R"(MyAccessToken)");
```

# <a name="c-winrttabcppwinrt"></a>[C++형](#tab/cppwinrt)

```cpp
auto configuration = m_cloudSession.Configuration();
configuration.AccessToken(LR"(MyAccessToken)");
```

---

## <a name="role-based-access-control"></a>역할 기반 액세스 제어

서비스의 응용 프로그램, 서비스 또는 Azure AD 사용자에 게 부여 되는 액세스 수준을 제어 하기 위해 Azure 공간 앵커 계정에 필요에 따라 할당할 수 있도록 다음 역할이 생성 되었습니다.

- **공간 앵커 계정 소유자**:이 역할이 있는 응용 프로그램 또는 사용자는 공간 앵커를 만들고,이를 쿼리하고, 삭제할 수 있습니다. 계정 키를 사용 하 여 계정에 인증 하는 경우 **공간 앵커 계정 소유자** 역할은 인증 된 보안 주체에 할당 됩니다.
- **공간 앵커 계정 기여자**:이 역할이 있는 응용 프로그램 또는 사용자는 공간 앵커를 만들 수 있으며,이를 위해 쿼리 하지만 삭제할 수는 없습니다.
- **공간 앵커 계정 판독기**:이 역할이 있는 응용 프로그램 또는 사용자는 공간 앵커에 대해서만 쿼리할 수 있지만 새 공간 앵커를 만들거나 기존 항목을 삭제 하거나 공간 앵커에서 메타 데이터를 업데이트할 수 없습니다. 이는 일반적으로 일부 사용자가 환경을 만드는 응용 프로그램에 사용 되 고, 다른 사용자는 이전에 해당 환경에 배치 된 앵커로 회수할 수 있습니다.

## <a name="next-steps"></a>다음 단계

Azure 공간 앵커를 사용 하 여 첫 번째 앱을 만듭니다.

> [!div class="nextstepaction"]
> [Unity](../unity-overview.yml)

> [!div class="nextstepaction"]
> [iOS](../quickstarts/get-started-ios.md)

> [!div class="nextstepaction"]
> [Android](../quickstarts/get-started-android.md)

> [!div class="nextstepaction"]
> [HoloLens](../quickstarts/get-started-hololens.md)
