---
title: 보호 된 웹 API 앱 등록 | Microsoft
titleSuffix: Microsoft identity platform
description: 보호 된 웹 API와 앱을 등록 하는 데 필요한 정보를 빌드하는 방법에 대해 알아봅니다.
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 2d9e5d051f101288b8528b47fa88b4783a040950
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/28/2020
ms.locfileid: "76775159"
---
# <a name="protected-web-api-app-registration"></a>보호 된 웹 API: 앱 등록

이 문서에서는 보호 된 web API에 대 한 앱 등록의 세부 사항을 설명 합니다.

앱을 등록 하는 일반적인 단계는 빠른 시작 [: Microsoft id 플랫폼을 사용 하 여 응용 프로그램 등록](quickstart-register-app.md)을 참조 하세요.

## <a name="accepted-token-version"></a>수락 된 토큰 버전

Microsoft id 플랫폼 엔드포인트은 v1.0 토큰과 v 2.0 토큰을 발급할 수 있습니다. 이러한 토큰에 대 한 자세한 내용은 [액세스 토큰](access-tokens.md)을 참조 하세요.

허용 되는 토큰 버전은 응용 프로그램을 만들 때 선택 하는 **지원 되는 계정 유형** 값에 따라 달라 집니다.

- **지원 되는 계정 유형** 값이 **조직 디렉터리 및 개인 Microsoft 계정 (예: Skype, Xbox, Outlook.com)의 계정인**경우 수락 된 토큰 버전은 v2.0입니다.
- 그렇지 않은 경우 허용 되는 토큰 버전은 v 1.0입니다.

응용 프로그램을 만든 후 다음 단계를 수행 하 여 허용 된 토큰 버전을 확인 하거나 변경할 수 있습니다.

1. Azure Portal에서 앱을 선택한 다음 **매니페스트**를 선택 합니다.
1. 매니페스트에서 **accessTokenAcceptedVersion** 속성을 찾습니다. 속성의 기본값은 2입니다.
1. 값은 웹 API에서 허용 하는 토큰 버전을 Azure Active Directory (Azure AD)로 지정 합니다.
    - 값이 2 이면 web API는 v2.0 토큰을 허용 합니다.
    - 값이 **null**이면 web API는 v1.0 토큰을 허용 합니다.
1. 토큰 버전을 변경한 경우 **저장**을 선택 합니다.

> [!NOTE]
> 웹 API는 허용 하는 토큰 버전을 지정 합니다. 클라이언트가 v2.0 (Microsoft identity platform) 엔드포인트에서 web API에 대 한 토큰을 요청 하는 경우 클라이언트는 web API가 수락 하는 토큰 버전을 나타내는 토큰을 가져옵니다.

## <a name="no-redirect-uri"></a>리디렉션 URI 없음

대화형으로 로그인 한 사용자가 없기 때문에 웹 Api에서 리디렉션 URI를 등록할 필요가 없습니다.

## <a name="exposed-api"></a>노출 된 API

웹 Api에만 적용 되는 다른 설정에는 노출 된 API 및 노출 된 범위가 있습니다.

### <a name="application-id-uri-and-scopes"></a>응용 프로그램 ID URI 및 범위

범위는 일반적으로 `resourceURI/scopeName`형식입니다. Microsoft Graph의 경우 범위에는 바로 가기가 있습니다. 예를 들어 `User.Read`은 `https://graph.microsoft.com/user.read`의 바로 가기입니다.

앱을 등록 하는 동안 다음 매개 변수를 정의 해야 합니다.

- 리소스 URI
- 하나 이상의 범위
- 하나 이상의 앱 역할

기본적으로 응용 프로그램 등록 포털에서는 리소스 URI `api://{clientId}`를 사용 하는 것이 좋습니다. 이 URI는 고유 하지만 사람이 읽을 수는 없습니다. URI를 변경 하는 경우 새 값이 고유한 지 확인 합니다.

클라이언트 응용 프로그램에 대 한 범위는 *위임 된 권한* 으로 표시 되 고 앱 역할은 웹 API에 대 한 *응용 프로그램 권한* 으로 표시 됩니다.

범위는 앱의 사용자에 게 표시 되는 동의 창에도 표시 됩니다. 따라서 범위를 설명 하는 해당 문자열을 제공 해야 합니다.

- 사용자에 게 표시 됩니다.
- 테넌트 관리자에 게 표시 되는 대로 관리자 동의를 허용할 수 있습니다.

### <a name="exposing-delegated-permissions-scopes"></a>위임 된 권한 (범위) 노출

1. 응용 프로그램 등록에서 **API 노출** 을 선택 합니다.
1. **범위 추가**를 선택합니다.
1. 메시지가 표시 되 면 **저장 후 계속**을 선택 하 여 제안 된 응용 프로그램 ID URI (`api://{clientId}`)를 적용 합니다.
1. 다음 값을 지정 합니다.
    - **범위 이름** 을 선택 하 고 **access_as_user**를 입력 합니다.
    - **동의할 수 있는 사용자** 를 선택 하 고 **관리자 및 사용자** 가 선택 되어 있는지 확인 합니다.
    - **관리자 동의 표시 이름** 을 선택 하 고 **액세스 TodoListService를 사용자로**입력 합니다.
    - **관리자 동의 설명** 을 선택 하 고 enter 키를 **누르면 사용자로 TodoListService Web API에 액세스**합니다.
    - **사용자 동의 표시 이름** 을 선택 하 고 **액세스 TodoListService를 사용자로**입력 합니다.
    - 사용자 **동의 설명** 을 선택 하 고 enter 키를 **누르면 사용자로 TodoListService Web API에 액세스**합니다.
    - **상태** 값을 **사용**으로 설정 된 상태로 유지 합니다.
 1. **범위 추가**를 선택 합니다.

### <a name="if-your-web-api-is-called-by-a-daemon-app"></a>웹 API가 디먼 앱에 의해 호출 되는 경우

이 섹션에서는 디먼 앱에서 안전 하 게 호출할 수 있도록 보호 된 웹 API를 등록 하는 방법에 대해 알아봅니다.

- 디먼 앱은 사용자와 상호 작용 하지 않으므로 *응용 프로그램 권한만* 선언 하 고 노출 합니다. 위임 된 권한을 사용 하지 않는 것이 좋습니다.
- 테넌트 관리자는 API의 응용 프로그램 사용 권한 중 하나에 액세스 하도록 등록 된 응용 프로그램에만 웹 API 토큰을 발급 하도록 Azure AD를 요구할 수 있습니다.

#### <a name="exposing-application-permissions-app-roles"></a>응용 프로그램 사용 권한 노출 (앱 역할)

응용 프로그램 사용 권한을 노출 하려면 매니페스트를 편집 해야 합니다.

1. 응용 프로그램에 대 한 응용 프로그램 등록에서 **매니페스트**를 선택 합니다.
1. 매니페스트를 편집 하려면 `appRoles` 설정을 찾고 응용 프로그램 역할을 추가 합니다. 다음 샘플 JSON 블록에서 역할 정의를 제공 합니다.
1. `allowedMemberTypes`을 `"Application"`로만 설정 합니다.
1. `id`이 고유한 GUID 인지 확인 합니다.
1. `displayName` 및 `value`에 공백이 들어 있지 않은지 확인 합니다.
1. 매니페스트를 저장합니다.

다음 샘플에서는 `appRoles`의 내용을 보여 줍니다. 여기서 `id`의 값은 임의의 고유 GUID 일 수 있습니다.

```JSon
"appRoles": [
    {
    "allowedMemberTypes": [ "Application" ],
    "description": "Accesses the TodoListService-Cert as an application.",
    "displayName": "access_as_application",
    "id": "ccf784a6-fd0c-45f2-9c08-2f9d162a0628",
    "isEnabled": true,
    "lang": null,
    "origin": "Application",
    "value": "access_as_application"
    }
],
```

#### <a name="ensuring-that-azure-ad-issues-tokens-for-your-web-api-to-only-allowed-clients"></a>Azure AD에서 허용 되는 클라이언트만 웹 API에 대 한 토큰을 발급 하도록 보장

웹 API는 앱 역할을 확인 합니다. 이 역할은 소프트웨어 개발자가 응용 프로그램 사용 권한을 노출 하는 방법입니다. 또한 테넌트 관리자가 API 액세스에 대해 승인한 앱에만 API 토큰을 발급 하도록 Azure AD를 구성할 수 있습니다.

이 강화 된 보안을 추가 하려면 다음을 수행 합니다.

1. 앱 등록에 대 한 앱 **개요** 페이지로 이동 합니다.
1. **로컬 디렉터리의 관리 되는 응용 프로그램**에서 앱 이름으로 링크를 선택 합니다. 이 선택의 레이블은 잘릴 수 있습니다. 예를 들어 **에서 관리 되는 응용 프로그램** 을 볼 수 있습니다.

   > [!NOTE]
   >
   > 이 링크를 선택 하면 **엔터프라이즈 응용 프로그램 개요** 페이지로 이동 합니다. 이 페이지는 응용 프로그램을 만든 테넌트의 응용 프로그램에 대 한 서비스 사용자와 연결 됩니다. 브라우저의 뒤로 단추를 사용 하 여 앱 등록 페이지로 이동할 수 있습니다.

1. 엔터프라이즈 응용 프로그램 페이지의 **관리** 섹션에서 **속성** 페이지를 선택 합니다.
1. Azure AD에서 특정 클라이언트만 웹 API에 대 한 액세스를 허용 하도록 하려면 **사용자 할당 필요?** 를 **예**로 설정 합니다.

   > [!IMPORTANT]
   >
   > **사용자 할당 필요?** 를 **예**로 설정 하면 AZURE AD에서 웹 API 액세스 토큰을 요청할 때 클라이언트의 앱 역할 할당을 확인 합니다. 클라이언트가 앱 역할에 할당 되지 않은 경우 Azure AD는 "invalid_client: AADSTS501051: Application \<응용 프로그램 이름\> \<web API\>에 대 한 역할에 할당 되어 있지 않습니다." 라는 오류 메시지를 반환 합니다.
   >
   > **사용자 할당** 을 유지 해야 하는 경우 **아니요**로 설정 하면 클라이언트가 웹 API에 대 한 액세스 토큰을 요청할 때 Azure AD에서 앱 역할 할당을 확인 하지 않습니다. 클라이언트 자격 증명 흐름을 사용 하는 모든 클라이언트를 의미 하는 모든 디먼 클라이언트는 대상 그룹을 지정 하는 방법으로 API에 대 한 액세스 토큰을 가져올 수 있습니다. 모든 응용 프로그램은이에 대 한 권한을 요청 하지 않고도 API에 액세스할 수 있습니다.
   >
   > 그러나 이전 섹션에서 설명한 것 처럼 웹 API는 항상 응용 프로그램이 테넌트 관리자에 의해 권한이 부여 된 올바른 역할 인지 확인할 수 있습니다. API는 액세스 토큰에 역할 클레임이 있는지 확인 하 고이 클레임에 대 한 값이 올바른지 확인 하 여이 확인을 수행 합니다. 이전 JSON 샘플에서 값은 `access_as_application`입니다.

1. **저장**을 선택합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [앱 코드 구성](scenario-protected-web-api-app-configuration.md)
