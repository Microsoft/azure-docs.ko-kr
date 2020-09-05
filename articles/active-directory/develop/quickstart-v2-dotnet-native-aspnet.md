---
title: Microsoft ID 플랫폼으로 보호되는 ASP.NET 웹 API 호출
description: 이 빠른 시작에서는 Windows 데스크톱(WPF) 애플리케이션에서 Microsoft ID 플랫폼으로 보호된 ASP.NET 웹 API를 호출하는 방법을 알아봅니다. WPF 클라이언트는 사용자를 인증하고, 액세스 토큰을 요청하고, 웹 API를 호출합니다.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 12/12/2019
ms.author: jmprieur
ms.custom: devx-track-csharp, aaddev, identityplatformtop40, scenarios:getting-started, languages:ASP.NET
ms.openlocfilehash: 0fc31fd397f8206f7c6f0509dd03495631dde609
ms.sourcegitcommit: c28fc1ec7d90f7e8b2e8775f5a250dd14a1622a6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2020
ms.locfileid: "88165637"
---
# <a name="quickstart-call-an-aspnet-web-api-protected-by-microsoft-identity-platform"></a>빠른 시작: Microsoft ID 플랫폼으로 보호되는 ASP.NET 웹 API 호출

이 빠른 시작에서는 인증된 사용자만 액세스할 수 있도록 웹 API를 노출하고 보호합니다. 이 샘플에서는 Microsoft ID 플랫폼과 통합된 회사 또는 조직의 회사 및 학교 계정뿐만 아니라 개인 계정(outlook.com, live.com 등)을 통해 발급된 액세스 토큰을 수락할 수 있도록 ASP.NET 웹 API를 노출하는 방법을 보여 줍니다.

이 샘플에는 웹 API에 액세스하기 위해 액세스 토큰을 요청하는 방법을 보여주는 Windows 데스크톱 애플리케이션(WPF) 클라이언트도 포함되어 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

이 샘플을 실행하려면 다음이 필요합니다.

* Visual Studio 2017 또는 2019  [체험용 Visual Studio](https://www.visualstudio.com/downloads/) 다운로드

* [Microsoft 계정](https://www.outlook.com) 또는 [Microsoft 365 개발자 프로그램](/office/developer-program/office-365-developer-program)

## <a name="download-or-clone-this-sample"></a>이 샘플 다운로드 또는 복제

셸 또는 명령줄에서 이 샘플을 복제할 수 있습니다.

  ```console
  git clone https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet.git
  ```

또는 [샘플을 ZIP 파일로 다운로드](https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet/archive/complete.zip)할 수 있습니다.

## <a name="register-your-web-api-in-the-application-registration-portal"></a>애플리케이션 등록 포털에서 웹 API 등록

### <a name="choose-the-azure-ad-tenant-where-you-want-to-create-your-applications"></a>애플리케이션을 만들려는 Azure AD 테넌트를 선택합니다.

앱을 수동으로 등록하려면 먼저 다음을 수행해야 합니다.

1. [Azure Portal](https://portal.azure.com)에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 계정이 여러 Azure AD 테넌트에 있는 경우 페이지 상단 메뉴의 오른쪽 위 모서리에서 프로필을 선택한 다음, **디렉터리를 전환**합니다.
   포털 세션을 원하는 Azure AD 테넌트로 변경합니다.

### <a name="register-the-service-app-todolistservice"></a>서비스 앱 등록(TodoListService)

1. 개발자용 Microsoft ID 플랫폼 [앱 등록](https://go.microsoft.com/fwlink/?linkid=2083908) 페이지로 이동합니다.
1. **새 등록**을 선택합니다.
1. **애플리케이션 등록** 페이지가 표시되면 애플리케이션의 등록 정보를 입력합니다.
   - **이름** 섹션에서 앱의 사용자에게 표시되는 의미 있는 애플리케이션 이름(예: `AppModelv2-NativeClient-DotNet-TodoListService`)을 입력합니다.
   - **지원되는 계정 유형**을 **조직 디렉터리의 계정**으로 변경합니다.
   - **등록**을 선택하여 애플리케이션을 만듭니다.

1. 나중에 사용할 수 있도록 앱 **개요** 페이지에서 **애플리케이션(클라이언트) ID** 값을 찾아서 기록해 둡니다. 이 프로젝트에 대한 Visual Studio 구성 파일(`TodoListService\Web.config`의`ClientId`)을 구성하는 데 필요합니다.
1. **API 표시** 섹션을 선택하고 다음을 수행합니다.
   - **범위 추가**를 선택합니다.
   - **저장 후 계속**을 선택하여 제안된 애플리케이션 ID URI(api://{clientId})를 수락합니다.
   - 다음 매개 변수를 입력합니다.
     - **범위 이름**으로 `access_as_user`를 사용합니다.
     - **동의할 수 있는 사람**에 대해 **관리자 및 사용자** 옵션을 선택했는지 확인합니다.
     - **관리자 동의 표시 이름**에 `Access TodoListService as a user`를 입력합니다.
     - **관리자 동의 설명**에 `Accesses the TodoListService web API as a user`를 입력합니다.
     - **사용자 동의 표시 이름**에 `Access TodoListService as a user`를 입력합니다.
     - **사용자 동의 설명**에 `Accesses the TodoListService web API as a user`를 입력합니다.
     - **상태**를 **사용**으로 유지합니다.
     - **범위 추가**를 선택합니다.

### <a name="configure-the-service-project-to-match-the-registered-web-api"></a>등록된 웹 API와 일치하도록 서비스 프로젝트 구성

1. Visual Studio에서 솔루션을 연 다음, **TodoListService** 프로젝트의 루트 아래에서 **Web.config** 파일을 엽니다.
1. `ida:ClientId` 매개 변수 값을 방금 애플리케이션 등록 포털에서 등록한 애플리케이션의 **클라이언트 ID(애플리케이션 ID)** 로 바꿉니다.

### <a name="add-the-new-scope-to-the-todolistclients-appconfig"></a>*TodoListClient*의 app.config에 새 범위를 추가합니다.

* **TodoListClient** 프로젝트의 루트 폴더에 있는 **app.config** 파일을 연 다음, 방금 `TodoListServiceScope` 매개 변수 아래의 *TodoListService*에 대해 등록한 애플리케이션의 **애플리케이션 ID**를 붙여넣고 `{Enter the Application ID of your TodoListService from the app registration portal}` 문자열을 바꿉니다.
   >[!NOTE]
   >다음 형식을 사용하는지 확인합니다.
   >
   > `api://{TodoListService-Application-ID}/access_as_user`
   >
   >(여기서 {TodoListService-Application-ID}는 TodoListService의 애플리케이션 ID를 나타내는 GUID입니다.)

## <a name="register-the-client-app-todolistclient"></a>클라이언트 앱 등록(TodoListClient)

이 단계에서는 애플리케이션 등록 포털에서 새 애플리케이션을 등록하여 *TodoListClient* 프로젝트를 구성합니다. 클라이언트와 서버가 *동일한 애플리케이션*으로 간주되는 경우에는 '2단계'에서 등록한 애플리케이션을 다시 사용해도 됩니다. 사용자가 Microsoft 개인 계정으로 로그인하도록 하려면 동일한 애플리케이션을 사용해야 합니다.

### <a name="register-the-todolistclient-application-in-the-application-registration-portal"></a>*애플리케이션 등록 포털에서 *TodoListClient* 애플리케이션 등록*

1. 개발자용 Microsoft ID 플랫폼 [앱 등록](https://go.microsoft.com/fwlink/?linkid=2083908) 페이지로 이동합니다.
1. **새 등록**을 선택합니다.
1. **애플리케이션 등록** 페이지가 표시되면 애플리케이션의 등록 정보를 입력합니다.
   - **이름** 섹션에서 앱의 사용자에게 표시되는 의미 있는 애플리케이션 이름(예: `NativeClient-DotNet-TodoListClient`)을 입력합니다.
   - **지원되는 계정 유형**을 **모든 조직 디렉터리의 계정 및 개인 Microsoft 계정**으로 변경합니다.
   - **등록**을 선택하여 애플리케이션을 만듭니다.
   
   > [!NOTE]
   > *TodoListClient* 프로젝트의 **app.config**에서, `ida:Tenant`의 기본 값은 `common`으로 되어있습니다.
   >
   > `common`의 의미는 회사나 학교 계정 또는 Microsoft의 개인 계정을 사용하여 로그인 할 수 있음을 나타냅니다.(위 단계에서 **계정 유형**을 **모든 조직 디렉터리의 계정 및 개인 Microsoft 계정**을 선택했기 때문입니다.)
   >
   > `organizations`은 회사나 학교 계정으로만 로그인 할 수 있음을 나타냅니다.
   >
   > `consumers`은 Microsoft의 개인 계정으로만 로그인 할 수 있음을 나타냅니다.
   >

1. 앱의 [개요] 페이지에서 **인증** 섹션을 선택합니다.
   1. **플랫폼 구성** 아래의 **플랫폼 추가**를 선택합니다.
   1. **모바일 및 데스크톱 애플리케이션** 아래의 **모바일 및 데스크톱 애플리케이션**을 선택합니다.
   1. **리디렉션 URI**아래의 **https://login.microsoftonline.com/common/oauth2/nativeclient**를 선택합니다.
   1. **구성**을 선택합니다.
1. **API 사용 권한** 섹션을 선택합니다.
   1. **권한 추가** 단추를 클릭합니다.
   1. **내 API** 탭을 선택합니다.
   1. API 목록에서 `AppModelv2-NativeClient-DotNet-TodoListService API`를 선택하거나, 웹 API에 대해 입력한 이름을 선택합니다.
   1. **access_as_user** 권한을 아직 선택하지 않은 경우 지금 선택합니다. 필요한 경우 검색 상자를 사용합니다.
   1. **권한 추가** 단추를 선택합니다.

### <a name="configure-your-todolistclient-project"></a>*TodoListClient* 프로젝트 구성

1. *애플리케이션 등록 포털*의 **개요** 페이지에서 **애플리케이션(클라이언트) ID** 값을 복사합니다.
1. **TodoListClient** 프로젝트의 루트 폴더에 있는 **app.config** 파일을 연 다음, `ida:ClientId` 매개 변수에 값을 붙여넣습니다.

## <a name="run-your-project"></a>프로젝트 실행

1. `<F5>` 키를 눌러 프로젝트를 실행합니다. *TodoListClient*가 열립니다.
1. 오른쪽 위에서 **로그인**를 선택하고 애플리케이션을 등록할 때 사용한 것과 동일한 사용자 또는 같은 디렉터리에 있는 사용자로 로그인합니다.
1. 이번 로그인이 첫 번째 로그인이면 *TodoListService* Web API에 동의하라는 메시지가 표시될 수 있습니다.
1. 또한 로그인하려면 *TodoListService* Web API에 액세스하고 *To-Do* 목록을 조작하기 위한 *access_as_user* 범위에 대한 액세스 토큰이 필요합니다.

## <a name="pre-authorize-your-client-application"></a>클라이언트 애플리케이션 미리 인증

다른 디렉터리의 사용자가 웹 API에 액세스할 수 있도록 허용하는 방법 중 하나는 웹 API에 대한 *미리 인증된* 애플리케이션 목록에 있는 클라이언트 애플리케이션에서 애플리케이션 ID를 추가하여 클라이언트 애플리케이션의 웹 API 액세스를 *미리 인증*하는 것입니다. 미리 인증된 클라이언트를 추가하면 사용자가 웹 API 사용에 동의할 필요가 없습니다. 아래 단계에 따라 웹 애플리케이션을 미리 인증합니다.

1. *애플리케이션 등록 포털*로 돌아가서 **TodoListService**의 속성을 엽니다.
1. **API 표시** 섹션의 *인증된 클라이언트 애플리케이션* 섹션 아래에서 **클라이언트 애플리케이션 추가**를 클릭합니다.
1. *Client ID* 필드에 `TodoListClient` 애플리케이션의 애플리케이션 ID를 붙여넣습니다.
1. *권한 있는 범위* 섹션에서 이 웹 API `api://<Application ID>/access_as_user`의 범위를 선택합니다.
1. 페이지 아래쪽에 있는 **애플리케이션 추가** 단추를 누릅니다.

## <a name="run-your-project"></a>프로젝트 실행

1. `<F5>` 키를 눌러 프로젝트를 실행합니다. *TodoListClient*가 열립니다.
1. 오른쪽 위에서 **로그인**(또는 캐시 지우기/로그인)을 선택한 다음, 개인 Microsoft 계정(live.com 또는 hotmail.com) 또는 회사/학교 계정을 사용하여 로그인합니다.

## <a name="optional-restrict-sign-in-access-to-your-application"></a>선택 사항: 애플리케이션에 대한 로그인 액세스 제한

기본적으로 위의 단계에 따라 이 코드 샘플을 다운로드하고 Azure Active Directory v2 엔드포인트를 사용하도록 애플리케이션을 구성할 때 Azure AD와 통합된 조직의 회사 또는 학교 계정은 물론이고 개인 계정(예: outlook.com, live.com 등)도 토큰을 요청하여 웹 API에 액세스할 수 있습니다.

애플리케이션에 로그인할 수 있는 사람을 제한하려면 다음 옵션 중 하나를 사용합니다.

### <a name="option-1-restrict-access-to-a-single-organization-single-tenant"></a>옵션 1: 단일 조직(단일 테넌트)에 대한 액세스 제한

애플리케이션에 대한 로그인 액세스를 단일 Azure AD 테넌트의 사용자 계정(해당 테넌트의 *게스트 계정* 포함)으로 제한할 수 있습니다. 이 시나리오는 *LOB(기간 업무) 애플리케이션*에 공통적으로 적용됩니다.

1. **App_Start\Startup.Auth** 파일을 열고, `OpenIdConnectSecurityTokenProvider`에 전달된 메타데이터 엔드포인트의 값을 `"https://login.microsoftonline.com/{Tenant ID}/v2.0/.well-known/openid-configuration"`으로 변경합니다(`contoso.onmicrosoft.com` 같은 테넌트 이름을 사용할 수도 있음).
2. 같은 파일에서 `TokenValidationParameters`의 `ValidIssuer` 속성을 `"https://sts.windows.net/{Tenant ID}/"`로 설정하고 `ValidateIssuer` 인수를 `true`로 설정합니다.

### <a name="option-2-use-a-custom-method-to-validate-issuers"></a>옵션 2: 사용자 지정 메서드를 사용하여 발급자 유효성 검사

**IssuerValidator** 매개 변수를 사용하여 발급자의 유효성을 검사하는 사용자 지정 메서드를 구현할 수 있습니다. 이 매개 변수를 사용하는 방법에 대해 자세히 알아보려면 [TokenValidationParameters 클래스](/dotnet/api/microsoft.identitymodel.tokens.tokenvalidationparameters?view=azure-dotnet)를 알아보세요.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>다음 단계
Microsoft ID 플랫폼에서 지원하는 보호된 웹 API 시나리오에 대해 자세히 알아보세요.
> [!div class="nextstepaction"]
> [보호된 웹 API 시나리오](scenario-protected-web-api-overview.md)
