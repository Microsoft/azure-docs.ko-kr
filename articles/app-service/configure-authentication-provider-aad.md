---
title: Azure AD 인증 구성
description: App Service 앱에 대한 id 공급자로 Azure Active Directory 인증을 구성 하는 방법에 대해 알아봅니다.
ms.assetid: 6ec6a46c-bce4-47aa-b8a3-e133baef22eb
ms.topic: article
ms.date: 09/03/2019
ms.custom: fasttrack-edit
ms.openlocfilehash: 69959418c52eb7324efe19ca41481e426b822ab4
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76842363"
---
# <a name="configure-your-app-service-app-to-use-azure-ad-login"></a>Azure AD 로그인을 사용 하도록 App Service 앱 구성

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

이 문서에서는 Azure Active Directory (Azure AD)를 인증 공급자로 사용 하도록 Azure App Service를 구성 하는 방법을 보여 줍니다.

앱과 인증을 설정할 때 다음 모범 사례를 따르세요.

- 각 App Service 앱에 고유한 사용 권한 및 동의를 제공 합니다.
- 자체 등록을 사용 하 여 각 App Service 앱을 구성 합니다.
- 별도의 배포 슬롯에 별도의 앱 등록을 사용 하 여 환경 간의 사용 권한 공유를 방지 합니다. 새 코드를 테스트할 때이 방법을 통해 문제가 프로덕션 앱에 영향을 주는 것을 방지할 수 있습니다.

## <a name="express"> </a>Express 설정을 사용 하 여 구성

1. [Azure Portal]에서 **App Services**를 검색 하 고 선택한 다음, 앱을 선택 합니다.
2. 왼쪽 탐색 영역에서 **인증/권한 부여** > **를**선택 합니다.
3. **Azure Active Directory** > **Express**를 선택 합니다.

   대신 기존 앱 등록을 선택 하려면 다음을 수행 합니다.

   1. **기존 AD 앱 선택**을 선택 하 고 **Azure AD 앱**을 클릭 합니다.
   2. 기존 앱 등록을 선택 하 고 **확인**을 클릭 합니다.

3. **확인**을 선택하여 Azure Active Directory에 App Service 앱을 등록합니다. 새 앱 등록이 생성 됩니다.
   
    ![Azure Active Directory의 Express 설정](./media/configure-authentication-provider-aad/express-settings.png)
   
4. 필드 기본적으로 App Service는 인증을 제공 하지만 사이트 콘텐츠 및 Api에 대한 권한 있는 액세스를 제한 하지 않습니다. 앱 코드에서 사용자 권한을 부여해야 합니다. Azure Active Directory에서 인증 한 사용자만 앱에 액세스할 수 있도록 제한 하려면 **요청이 인증 되지 않은 경우** **Azure Active Directory를 사용 하 여 로그인**하도록 작업을 설정 합니다. 이 기능을 설정 하면 앱에서 모든 요청을 인증 해야 합니다. 또한 인증을 위해 인증 되지 않은 모든 Azure Active Directory로 리디렉션합니다.

    > [!CAUTION]
    > 이러한 방식으로 액세스를 제한 하는 것은 앱에 대한 모든 호출에 적용 됩니다 .이는 여러 단일 페이지 응용 프로그램과 마찬가지로 공개적으로 사용 가능한 홈 페이지가 있는 앱에는 바람직하지 않을 수 있습니다. 이러한 응용 프로그램의 경우 **익명 요청 허용 (작업 없음)** 을 사용 하는 것이 좋습니다. 앱은 수동으로 로그인을 시작 합니다. 자세한 내용은 [인증 흐름](overview-authentication-authorization.md#authentication-flow)을 참조 하세요.
5. **저장**을 선택합니다.

## <a name="advanced"> </a>고급 설정으로 구성

다른 Azure AD 테넌트에서 앱 등록을 사용 하려면 앱 설정을 수동으로 구성할 수 있습니다. 이 사용자 지정 구성을 완료 하려면:

1. Azure AD에서 등록을 만듭니다.
2. App Service에 대한 등록 세부 정보를 제공 합니다.

### <a name="register"> </a>App Service 앱에 대한 Azure AD에서 앱 등록 만들기

App Service 앱을 구성 하는 경우 다음 정보가 필요 합니다.

- 클라이언트 ID
- 테넌트 ID
- 클라이언트 암호 (선택 사항)
- 응용 프로그램 ID URI

다음 단계를 수행합니다.

1. [Azure Portal]에 로그인 하 여 **App Services**를 검색 하 고 선택한 다음, 앱을 선택 합니다. 앱의 **URL**을 적어둡니다. 이를 사용 하 여 Azure Active Directory 앱 등록을 구성 합니다.
1. **새 등록** > **앱 등록** **Azure Active Directory** > 를 선택 합니다.
1. **응용 프로그램 등록** 페이지에서 앱 등록에 대한 **이름을** 입력 합니다.
1. **리디렉션 URI**에서 **웹** 을 선택 하 고 `<app-url>/.auth/login/aad/callback`를 입력 합니다. `https://contoso.azurewebsites.net/.auth/login/aad/callback`)을 입력합니다. 
1. **만들기**를 선택합니다.
1. 앱 등록을 만든 후 나중에 **응용 프로그램 (클라이언트) id** 와 **디렉터리 (테넌트) id** 를 복사 합니다.
1. **브랜딩**을 선택합니다. **홈 페이지 url**에서 App Service 앱의 url을 입력 하 고 **저장**을 선택 합니다.
1. API > **집합**표시 **를** 선택 합니다. App Service 앱의 URL에 붙여 넣고 **저장**을 선택 합니다.

   > [!NOTE]
   > 이 값은 앱 등록의 **응용 프로그램 ID URI** 입니다. 웹 앱이 클라우드의 API에 액세스 해야 하는 경우 클라우드 App Service 리소스를 구성할 때 웹 앱의 **응용 프로그램 ID URI** 가 필요 합니다. 예를 들어 클라우드 서비스에서 웹 앱에 대한 액세스 권한을 명시적으로 부여 하려는 경우이를 사용할 수 있습니다.

1. **범위 추가**를 선택합니다.
   1. **범위 이름**에 *user_impersonation*을 입력 합니다.
   1. 텍스트 상자에 동의 페이지에서 사용자에 게 표시할 동의 범위 이름 및 설명을 입력 합니다. 예를 들어 *내 앱에 액세스*를 입력 합니다. 
   1. **범위 추가**를 선택 합니다.
1. 필드 클라이언트 암호를 만들려면 **인증서 & 비밀** > **새 클라이언트 암호** > **추가**를 선택 합니다. 페이지에 표시 된 클라이언트 암호 값을 복사 합니다. 다시 표시되지 않습니다.
1. 필드 여러 **회신 url**을 추가 하려면 **인증**을 선택 합니다.

### <a name="secrets"> </a>App Service 앱에서 Azure Active Directory 사용

1. [Azure Portal]에서 **App Services**를 검색 하 고 선택한 다음, 앱을 선택 합니다. 
1. 왼쪽 창의 **설정**에서 **인증/권한 부여** > **를**선택 합니다.
1. 필드 기본적으로 App Service 인증은 앱에 대한 인증 되지 않은 액세스를 허용 합니다. 사용자 인증을 적용 하려면 **요청이 인증 되지 않은 경우 수행할 작업** 을 Azure Active Directory를 **사용 하 여 로그인**하도록 설정 합니다.
1. **인증 공급자**에서 **Azure Active Directory**를 선택합니다.
1. **관리 모드**에서 **고급** 을 선택 하 고 다음 표에 따라 App Service 인증을 구성 합니다.

    |필드|Description|
    |-|-|
    |클라이언트 ID| 앱 등록의 **응용 프로그램 (클라이언트) ID** 를 사용 합니다. |
    |발급자 ID| `https://login.microsoftonline.com/<tenant-id>`를 사용 하 *\<테넌트 id >* 를 앱 등록의 **디렉터리 (테넌트) id** 로 바꿉니다. |
    |클라이언트 암호 (선택 사항)| 앱 등록에서 생성 한 클라이언트 암호를 사용 합니다.|
    |허용 된 토큰 대상| 클라우드 또는 서버 앱 인 경우 웹 앱에서 인증 토큰을 허용 하려면 여기에 웹 앱의 **응용 프로그램 ID URI** 를 추가 합니다. 구성 된 **클라이언트 ID** 는 *항상* 암시적으로 허용 되는 대상으로 간주 됩니다. |

2. **확인**을 선택하고 **저장**을 선택합니다.

이제 App Service 앱에서 인증에 Azure Active Directory를 사용할 준비가 되었습니다.

## <a name="configure-a-native-client-application"></a>네이티브 클라이언트 애플리케이션 구성

**Active Directory 인증 라이브러리**와 같은 클라이언트 라이브러리를 사용 하 여 인증을 허용 하도록 네이티브 클라이언트를 등록할 수 있습니다.

1. [Azure Portal]에서 **Active Directory** > 를 선택 하 **앱 등록** **새 등록**을 > 합니다.
1. **응용 프로그램 등록** 페이지에서 앱 등록에 대한 **이름을** 입력 합니다.
1. **리디렉션 URI**에서 **공용 클라이언트 (모바일 & 데스크톱)** 를 선택 하 고 URL `<app-url>/.auth/login/aad/callback`을 입력 합니다. `https://contoso.azurewebsites.net/.auth/login/aad/callback`)을 입력합니다.

    > [!NOTE]
    > Windows 응용 프로그램의 경우 대신 [패키지 SID](../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md#package-sid) 를 URI로 사용 합니다.
1. **만들기**를 선택합니다.
1. 앱 등록을 만든 후에는 **응용 프로그램 (클라이언트) ID**의 값을 복사 합니다.
1. **Api 사용 권한** > **내 api** > **사용 권한 추가를** 선택 합니다.
1. App Service 앱에 대해 이전에 만든 앱 등록을 선택 합니다. 앱 등록이 표시 되지 않으면 [App Service 앱에 대한 AZURE AD에서 앱 등록 만들기](#register)에서 **user_impersonation** 범위를 추가 했는지 확인 합니다.
1. **User_impersonation**을 선택 하 고 **사용 권한 추가**를 선택 합니다.

이제 App Service 앱에 액세스할 수 있는 네이티브 클라이언트 애플리케이션을 구성했습니다.

## <a name="related-content"> </a>다음 단계

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- URLs. -->

[Azure Portal]: https://portal.azure.com/
