---
title: Azure AD 인증 구성
description: App Service 또는 Azure Functions 앱의 ID 공급자로 Azure Active Directory 인증을 구성하는 방법에 대해 알아봅니다.
ms.assetid: 6ec6a46c-bce4-47aa-b8a3-e133baef22eb
ms.topic: article
ms.date: 04/14/2020
ms.custom: seodec18, fasttrack-edit, has-adal-ref
ms.openlocfilehash: 4022ca97f88e2f06d3b4c1eeb103bb60d1856eda
ms.sourcegitcommit: 648c8d250106a5fca9076a46581f3105c23d7265
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "88962148"
---
# <a name="configure-your-app-service-or-azure-functions-app-to-use-azure-ad-login"></a>Azure AD 로그인을 사용하도록 App Service 또는 Azure Functions 앱 구성

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

이 문서에서는 Azure AD(Azure Active Directory)를 인증 공급자로 사용하도록 Azure App Services 또는 Azure Functions를 구성하는 방법을 보여줍니다.

> [!NOTE]
> 기본 설정 흐름에서는 AAD V1 애플리케이션 등록을 설정합니다. [Azure Active Directory v2.0](../active-directory/develop/v2-overview.md)([MSAL](../active-directory/develop/msal-overview.md) 포함)을 사용하려면 [고급 구성 지침](#advanced)을 따르세요.

앱과 인증을 설정할 때 다음 모범 사례를 따릅니다.

- 각 App Service 앱에 고유한 권한 및 동의를 제공합니다.
- 각 App Service 앱을 각각 고유한 등록으로 구성합니다.
- 환경 간에 권한을 공유하는 일이 없도록 배포 슬롯마다 별도의 앱 등록을 사용합니다. 이렇게 하면 새 코드를 테스트할 때 문제가 프로덕션 앱에 영향을 주는 것을 방지할 수 있습니다.

> [!NOTE]
> 이 기능은 현재 Azure Functions용 Linux 사용 계획에서 사용할 수 없습니다.

## <a name="configure-with-express-settings"></a><a name="express"> </a>기본 설정을 사용하여 구성

> [!NOTE]
> **기본** 옵션은 정부 클라우드에 사용할 수 없습니다.

1. [Azure Portal]에서 **App Services**를 검색하여 선택한 다음, 앱을 선택합니다.
2. 왼쪽 탐색 영역에서 **인증/권한 부여** > **켜기**를 선택합니다.
3. **Azure Active Directory** > **기본**을 선택합니다.

   기존 앱 등록을 선택하려면 다음을 수행합니다.

   1. **기존 AD 앱 선택**을 선택한 다음, **Azure AD 앱**을 클릭합니다.
   2. 기존 앱 등록을 선택하고 **확인**을 클릭합니다.

3. **확인**을 선택하여 Azure Active Directory에 App Service 앱을 등록합니다. 새 앱이 등록됩니다.

    ![Azure Active Directory의 기본 설정](./media/configure-authentication-provider-aad/express-settings.png)

4. (선택 사항) 기본적으로 App Service는 인증을 제공하지만, 사이트 콘텐츠 및 API에 대한 권한 있는 액세스를 제한하지는 않습니다. 앱 코드에서 사용자 권한을 부여해야 합니다. Azure Active Directory에서 인증된 사용자만 앱에 액세스할 수 있도록 제한하려면 **요청이 인증되지 않은 경우 수행할 작업**을 **Azure Active Directory로 로그인**으로 설정합니다. 이 기능을 설정하면 앱에서 모든 요청을 인증해야 합니다. 또한 앱은 인증되지 않은 모든 요청을 인증받기 위해 Azure Active Directory로 리디렉션합니다.

    > [!CAUTION]
    > 이러한 방식으로 액세스를 제한하면 모든 앱 호출에 제한이 적용되며, 여러 단일 페이지 애플리케이션이 그렇듯이 공개적으로 사용 가능한 홈페이지가 있는 앱에는 이 방법이 바람직하지 않을 수 있습니다. 이러한 애플리케이션에서는 **익명 요청 허용(작업 없음)** 으로 설정하고, 앱에서 수동으로 로그인을 시작하는 것이 더 좋은 방법일 수 있습니다. 자세한 내용은 [인증 흐름](overview-authentication-authorization.md#authentication-flow)을 참조하세요.
5. **저장**을 선택합니다.

## <a name="configure-with-advanced-settings"></a><a name="advanced"> </a>고급 설정을 사용하여 구성

다른 Azure AD 테넌트에서 앱 등록을 사용하려면 앱 설정을 수동으로 구성하면 됩니다. 이 사용자 지정 구성을 완료하려면 다음을 수행합니다.

1. Azure AD에서 등록 리소스를 만듭니다.
2. App Service에 대한 등록 세부 정보를 입력합니다.

### <a name="create-an-app-registration-in-azure-ad-for-your-app-service-app"></a><a name="register"> </a>Azure AD에서 App Service 앱의 앱 등록 만들기

App Service 앱을 구성할 때 다음 정보가 필요합니다.

- 클라이언트 ID
- 테넌트 ID
- 클라이언트 암호(선택 사항)
- 애플리케이션 ID URI

다음 단계를 수행합니다.

1. [Azure Portal]에 로그인하고 **App Services**를 검색하여 선택한 다음, 자신의 앱을 선택합니다. 앱 **URL**을 적어 둡니다. Azure Active Directory 앱 등록을 구성할 때 이 정보를 사용합니다.
1. **Azure Active Directory** > **앱 등록** > **새 등록**을 선택합니다.
1. **애플리케이션 등록** 페이지에서 앱 등록의 **이름**을 입력합니다.
1. **리디렉션 URI**에서 **웹**을 선택하고 `<app-url>/.auth/login/aad/callback`를 입력합니다. `https://contoso.azurewebsites.net/.auth/login/aad/callback`)을 입력합니다.
1. **만들기**를 선택합니다.
1. 앱 등록을 만든 후에는 나중에 사용할 수 있도록 **애플리케이션(클라이언트) ID** 및 **디렉터리(테넌트) ID**를 복사합니다.
1. **인증**을 선택합니다. **암시적 권한 부여**에서 **ID 토큰**을 사용하도록 설정하여 OpenID Connect 사용자가 App Service에서 로그인하는 것을 허용합니다.
1. (선택 사항) **브랜딩**을 선택합니다. **홈페이지 URL**에서 App Service 앱의 URL을 입력하고 **저장**을 선택합니다.
1. **API 표시** > **설정**을 선택합니다. 단일 테넌트 앱의 경우 App Service 앱의 URL에 붙여넣고 **저장**을 선택합니다. 다중 테넌트 앱의 경우 테넌트가 확인된 도메인 중 하나를 기반으로 하는 URL을 붙여넣고 **저장**을 선택합니다.

   > [!NOTE]
   > 이 값은 앱 등록의 **애플리케이션 ID URI**입니다. 웹앱이 클라우드에서 API에 액세스해야 하는 경우 클라우드 App Service 리소스를 구성할 때 웹앱의 **애플리케이션 ID URI**가 필요합니다. 예를 들어 클라우드 서비스에서 웹앱에 대한 액세스 권한을 명시적으로 부여할 때 사용할 수 있습니다.

1. **범위 추가**를 선택합니다.
   1. **범위 이름**에 *user_impersonation*을 입력합니다.
   1. 텍스트 상자에는 동의 페이지에서 사용자에게 표시할 동의 범위 이름 및 설명을 입력합니다. 예를 들어 *내 앱에 액세스*를 입력합니다.
   1. **범위 추가**를 선택합니다.
1. (선택 사항) 클라이언트 암호를 만들려면 **인증서 및 비밀** > **새 클라이언트 암호** > **추가**를 선택합니다. 페이지에 표시되는 클라이언트 암호 값을 복사합니다. 다시 표시되지 않습니다.
1. (선택 사항) **회신 URL**을 여러 개 추가하려면 **인증**을 선택합니다.

### <a name="enable-azure-active-directory-in-your-app-service-app"></a><a name="secrets"> </a>Azure Active Directory에서 Azure Active Directory 사용

1. [Azure Portal]에서 **App Services**를 검색하여 선택한 다음, 앱을 선택합니다.
1. 왼쪽 창의 **설정**에서 **인증/권한 부여** > **켜기**를 선택합니다.
1. (선택 사항) 기본적으로 App Service 인증은 앱에 대한 인증되지 않은 액세스를 허용합니다. 사용자 인증을 적용하려면 **요청이 인증되지 않은 경우 수행할 작업**을 **Azure Active Directory로 로그인**으로 설정합니다.
1. **인증 공급자**에서 **Azure Active Directory**를 선택합니다.
1. **관리 모드**에서 **고급**을 선택하고 다음 표에 따라 App Service 인증을 구성합니다.

    |필드|Description|
    |-|-|
    |클라이언트 ID| 앱 등록의 **애플리케이션(클라이언트) ID**를 사용합니다. |
    |발급자 URL| 를 사용 하 `<authentication-endpoint>/<tenant-id>/v2.0` 고를 *\<authentication-endpoint>* [클라우드 환경에 대 한 인증 끝점](../active-directory/develop/authentication-national-cloud.md#azure-ad-authentication-endpoints) (예: https://login.microsoft.com 글로벌 Azure의 경우 "")으로 바꾸고,를 *\<tenant-id>* 앱 등록이 만들어진 **디렉터리 (테 넌 트) ID** 로 바꿉니다. 이 값은 사용자를 올바른 Azure AD 테넌트로 리디렉션하고 적절한 메타데이터를 다운로드하여 적절한 토큰 서명 키와 토큰 발급자 클레임 값을 확인하는 등의 용도로 사용됩니다. Azure AD v1 및 Azure Functions apps를 사용 하는 응용 프로그램의 경우 `/v2.0` URL에서 생략 합니다.|
    |클라이언트 암호(선택 사항)| 앱 등록에서 생성한 클라이언트 암호를 사용합니다.|
    |허용되는 토큰 대상| 앱이 클라우드 또는 서버 앱이고 웹앱의 인증 토큰을 허용하려면 웹앱의 **애플리케이션 ID URI**를 여기에 추가합니다. 구성된 **클라이언트 ID**는 암시적으로 *항상* 허용되는 대상으로 간주됩니다. |

2. **확인**을 선택하고 **저장**을 선택합니다.

이제 App Service 앱에서 Azure Active Directory를 인증에 사용할 준비가 되었습니다.

## <a name="configure-a-native-client-application"></a>네이티브 클라이언트 애플리케이션 구성

**Active Directory 인증 라이브러리** 같은 클라이언트 라이브러리를 사용하여 앱에 호스트되는 Web API에 대한 인증을 허용하도록 네이티브 클라이언트를 등록할 수 있습니다.

1. [Azure portal]에서 **Active Directory** > **앱 등록** > **새 등록**을 선택합니다.
1. **애플리케이션 등록** 페이지에서 앱 등록의 **이름**을 입력합니다.
1. **리디렉션 URI**에서 **퍼블릭 클라이언트(모바일 및 데스크톱)** 를 선택하고 URL로 `<app-url>/.auth/login/aad/callback`을 입력합니다. `https://contoso.azurewebsites.net/.auth/login/aad/callback`)을 입력합니다.

    > [!NOTE]
    > Microsoft Store 애플리케이션의 경우 URI로 [패키지 SID](/previous-versions/azure/app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library#package-sid)를 대신 사용합니다.
1. **만들기**를 선택합니다.
1. 앱 등록을 만든 후에는 **애플리케이션(클라이언트) ID**의 값을 복사합니다.
1. **API 사용 권한** > **사용 권한 추가** > **내 API**를 선택합니다.
1. 앞에서 App Service 앱에 대해 만든 앱 등록을 선택합니다. 앱 등록이 표시되지 않으면 [Azure AD에서 App Service 앱의 앱 등록 만들기](#register)에서 **user_impersonation** 범위를 추가했는지 확인합니다.
1. **위임된 권한**에서 **user_impersonation**을 선택한 다음, **사용 권한 추가**를 선택합니다.

사용자 대신 App Service 앱에 액세스할 수 있는 네이티브 클라이언트 애플리케이션 구성이 완료되었습니다.

## <a name="configure-a-daemon-client-application-for-service-to-service-calls"></a>서비스 간 호출에 대한 디먼 클라이언트 애플리케이션 구성

애플리케이션은 App Service 또는 애플리케이션 대신(사용자 대신이 아님) 함수 앱에서 호스트되는 웹 API를 호출하는 토큰을 획득할 수 있습니다. 이 시나리오는 로그인한 사용자가 없는 상태에서 작업을 수행하는 비대화형 디먼 애플리케이션에 유용합니다. 이 파일은 표준 OAuth 2.0 [클라이언트 자격 증명](../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md) 권한 부여를 사용합니다.

1. [Azure portal]에서 **Active Directory** > **앱 등록** > **새 등록**을 선택합니다.
1. **애플리케이션 등록** 페이지에서 디먼 앱 등록의 **이름**을 입력합니다.
1. 디먼 애플리케이션의 경우에는 리디렉션 URI가 필요하지 않으므로 비워 둘 수 있습니다.
1. **만들기**를 선택합니다.
1. 앱 등록을 만든 후에는 **애플리케이션(클라이언트) ID**의 값을 복사합니다.
1. **인증서 및 암호** > **새 클라이언트 암호** > **추가**를 선택합니다. 페이지에 표시되는 클라이언트 암호 값을 복사합니다. 다시 표시되지 않습니다.

이제 `resource` 매개 변수를 대상 앱의 **애플리케이션 ID URI**로 설정하여 [클라이언트 ID 및 클라이언트 암호를 사용하여 액세스 토큰을 요청](../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md#first-case-access-token-request-with-a-shared-secret)할 수 있습니다. 그런 다음, 결과적인 액세스 토큰은 표준 [OAuth 2.0 인증 헤더](../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md#use-the-access-token-to-access-the-secured-resource)를 사용하여 대상 앱에 제공될 수 있으며, App Service 인증/권한 부여는 일반적인 방법으로 토큰의 유효성을 검사하고 사용하여 이제 호출자(이 경우에는 사용자가 아닌 애플리케이션)가 인증되었음을 표시합니다.

현재 이 기능을 사용하면 Azure AD 테넌트의 ‘모든’ 클라이언트 애플리케이션이 액세스 토큰을 요청하고 대상 앱에 대한 인증을 수행할 수 있습니다. 특정 클라이언트 애플리케이션만 허용하도록 ‘권한 부여’를 적용하려면 몇 가지 추가 구성을 수행해야 합니다.

1. 보호하려는 App Service 또는 함수 앱을 나타내는 앱 등록의 매니페스트에서 [앱 역할을 정의합니다](../active-directory/develop/howto-add-app-roles-in-azure-ad-apps.md).
1. 권한이 부여되어야 하는 클라이언트를 나타내는 앱 등록에서 **API 사용 권한** > **사용 권한 추가** > **내 API**를 선택합니다.
1. 이전에 만든 앱 등록을 선택합니다. 앱 등록이 표시되지 않으면 [앱 역할을 추가](../active-directory/develop/howto-add-app-roles-in-azure-ad-apps.md)했는지 확인합니다.
1. **애플리케이션 사용 권한**에서 이전에 만든 앱 역할을 선택하고 **사용 권한 추가**를 선택합니다.
1. 권한을 요청하도록 클라이언트 애플리케이션에 권한을 부여하려면 **관리자 동의 부여**를 클릭해야 합니다.
1. 이전 시나리오(어떤 역할도 추가되기 전)와 마찬가지로 이제 동일한 대상 `resource`에 대한 [액세스 토큰을 요청](../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md#first-case-access-token-request-with-a-shared-secret)하면, 액세스 토큰에는 클라이언트 애플리케이션에 대한 권한이 부여된 앱 역할을 포함하는 `roles` 클레임이 포함됩니다.
1. 이제 대상 App Service 또는 함수 앱 코드 내에서 예상한 역할이 토큰에 있는지 확인할 수 있습니다(App Service 인증/권한 부여에 의해 수행되지 않음). 자세한 내용은 [사용자 클레임 액세스](app-service-authentication-how-to.md#access-user-claims)를 참조하세요.

자체 ID를 사용하는 App Service 앱에 액세스할 수 있는 디먼 클라이언트 애플리케이션 구성이 완료되었습니다.

## <a name="next-steps"></a><a name="related-content"> </a>다음 단계

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]
* [자습서: Azure App Service에서 엔드투엔드 사용자 인증 및 권한 부여](tutorial-auth-aad.md)
<!-- URLs. -->

[Azure Portal]: https://portal.azure.com/