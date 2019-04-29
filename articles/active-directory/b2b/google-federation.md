---
title: Google을 id 공급자로 B2B-Azure Active Directory에 대 한 추가 | Microsoft Docs
description: 게스트 사용자가 자신의 Gmail 계정을 사용하여 Azure AD 앱에 로그인할 수 있도록 Google과 페더레이션
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 12/17/2018
ms.author: mimart
author: msmimart
manager: daveba
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0d61f233b2eb901bcf1e6b5b4ff147893f918e8f
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62104861"
---
# <a name="add-google-as-an-identity-provider-for-b2b-guest-users"></a>Google을 B2B 게스트 사용자에 대한 ID 공급자로 추가

Google과의 페더레이션을 설정하면 초대된 사용자가 MSA(Microsoft 계정) 또는 Azure AD 계정을 만들 필요 없이 자신의 Google 계정을 사용하여 공유된 앱 및 리소스에 로그인하도록 허용할 수 있습니다.  
> [!NOTE]
> Google 게스트 사용자는 테넌트 컨텍스트를 포함하는 링크를 사용하여 로그인해야 합니다(예: `https://myapps.microsoft.com/?tenantid=<tenant id>` 또는 `https://portal.azure.com/<tenant id>`/확인된 기본 도메인의 경우 `https://myapps.microsoft.com/<verified domain>.onmicrosoft.com`). 애플리케이션 및 리소스에 대한 직접 링크는 테넌트 컨텍스트를 포함하는 한 작동합니다. 게스트 사용자는 현재 테넌트 컨텍스트가 없는 엔드포인트를 사용하여 로그인할 수 없습니다. 예를 들어 `https://myapps.microsoft.com`, `https://portal.azure.com` 또는 팀 공통 엔드포인트를 사용하면 오류가 발생합니다.
 
## <a name="what-is-the-experience-for-the-google-user"></a>Google 사용자를 위한 환경이란?
Google Gmail 사용자에게 초대를 보낼 때 게스트 사용자는 테넌트 컨텍스트를 포함하는 링크를 사용하여 공유된 앱 또는 리소스에 액세스해야 합니다. 환경은 Google에 이미 로그인했는지 여부에 따라 달라집니다.
  - 게스트 사용자가 Google에 로그인하지 않은 경우 Google에 로그인하라는 메시지가 표시됩니다.
  - 게스트 사용자가 Google에 이미 로그인된 경우에는 사용하려는 계정을 선택하라는 메시지가 표시됩니다. 게스트 사용자는 초대에 사용된 계정을 선택해야 합니다.

게스트 사용자에게 “헤더가 너무 김” 오류가 표시되는 경우 자신의 쿠키를 지우거나 비공개 또는 시크릿 창을 열고 다시 로그인을 시도해 볼 수 있습니다.

![페이지에서 Google 로그인을 보여 주는 스크린샷](media/google-federation/google-sign-in.png)

## <a name="step-1-configure-a-google-developer-project"></a>1단계: Google 개발자 프로젝트 구성
먼저 Google 개발자 콘솔에서 새 프로젝트를 만들어 나중에 Azure AD에 추가할 수 있는 클라이언트 ID 및 클라이언트 비밀을 가져옵니다. 
1. https://console.developers.google.com에서 Google API로 이동하고, Google 계정으로 로그인합니다. 공유 팀 Google 계정을 사용하는 것이 좋습니다.
2. 새 프로젝트를 만듭니다. 대시보드에서 **프로젝트 만들기**를 선택한 다음, **만들기**를 선택합니다. 새 프로젝트 페이지에서 **프로젝트 이름**을 입력한 다음, **만들기**를 선택합니다.
   
   ![Google에 대 한 새 프로젝트 페이지를 보여 주는 스크린샷](media/google-federation/google-new-project.png)

3. 새 프로젝트가 프로젝트 메뉴에서 선택되어 있는지 확인합니다. 그런 다음, 왼쪽 위에 있는 메뉴를 열고 **API 및 서비스** > **자격 증명**을 선택합니다.

   ![자격 증명 옵션을 Google API를 보여 주는 스크린샷](media/google-federation/google-api.png)
 
4. **OAuth 동의 화면** 탭을 선택하고 **애플리케이션 이름**을 입력합니다. (다른 설정은 변경하지 말고 그대로 둡니다.)

   ![Google OAuth 동의 화면 옵션을 보여 주는 스크린샷](media/google-federation/google-oauth-consent-screen.png)

5. **권한 있는 도메인** 섹션으로 스크롤하고 microsoftonline.com을 입력합니다.

   ![권한이 부여 된 도메인 섹션을 보여 주는 스크린샷](media/google-federation/google-oauth-authorized-domains.png)

6. **저장**을 선택합니다.

7. **자격 증명** 탭을 선택합니다. **자격 증명 만들기** 메뉴에서 **OAuth 클라이언트 ID**를 선택합니다.

   ![Google Api를 보여 주는 스크린 샷 만들기 자격 증명 옵션](media/google-federation/google-api-credentials.png)

8. **애플리케이션 유형**에서 **웹 애플리케이션**을 선택한 다음, **권한이 부여된 리디렉션 URI**에서 다음 URI를 입력합니다.
   - `https://login.microsoftonline.com` 
   - `https://login.microsoftonline.com/te/<directory id>/oauth2/authresp` <br>(여기서 `<directory id>`는 디렉터리 ID입니다.)
   
     > [!NOTE]
     > 디렉터리 ID를 찾으려면 https://portal.azure.com으로 이동하고, **Azure Active Directory**에서 **속성**을 선택하고 **디렉터리 ID**를 복사합니다.

   ![스크린샷은 권한이 부여 된 리디렉션 Uri 섹션](media/google-federation/google-create-oauth-client-id.png)

9. **만들기**를 선택합니다. 클라이언트 ID 및 클라이언트 비밀을 복사합니다. Azure AD 포털에서 ID 공급자를 추가할 때 사용하게 됩니다.

   ![OAuth 클라이언트 ID 및 클라이언트 암호를 보여 주는 스크린샷](media/google-federation/google-auth-client-id-secret.png)

## <a name="step-2-configure-google-federation-in-azure-ad"></a>2단계: Azure AD에서 Google 페더레이션 구성 
이제 Azure AD 포털에 입력하거나 PowerShell을 사용하여 Google 클라이언트 ID 및 클라이언트 비밀을 설정합니다. Gmail 주소를 사용하고 자신을 초대하고, 초대된 Google 계정을 사용하여 초대 사용을 시도해봄으로써 Google 페더레이션 구성을 테스트해야 합니다. 

#### <a name="to-configure-google-federation-in-the-azure-ad-portal"></a>Azure AD 포털에서 Google 페더레이션을 구성하려면 다음을 수행합니다. 
1. [Azure 포털](https://portal.azure.com)로 이동합니다. 왼쪽 창에서 **Azure Active Directory**를 선택합니다. 
2. **조직 관계**를 선택합니다.
3. **ID 공급자**를 선택한 다음, **Google** 단추를 클릭합니다.
4. 이름을 입력합니다. 그런 다음, 이전에 가져온 클라이언트 ID 및 클라이언트 비밀을 입력합니다. **저장**을 선택합니다. 

   ![추가 Google id 공급자 페이지를 보여 주는 스크린샷](media/google-federation/google-identity-provider.png)

#### <a name="to-configure-google-federation-by-using-powershell"></a>PowerShell을 사용하여 Google 페더레이션을 구성하려면 다음을 수행합니다.
1. 그래프 모듈에 대한 Azure AD PowerShell의 최신 버전을 설치합니다([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview)).
2. 다음 명령을 실행합니다. `Connect-AzureAD`
3. 로그인 프롬프트에서 관리되는 글로벌 관리자 계정으로 로그인합니다.  
4. 다음 명령 실행: 
   
   `New-AzureADMSIdentityProvider -Type Google -Name Google -ClientId [Client ID] -ClientSecret [Client secret]`
 
   > [!NOTE]
   > “1단계: Google 개발자 프로젝트 구성”에서 만든 앱의 클라이언트 ID 및 클라이언트 암호를 사용합니다. 자세한 내용은 [New-AzureADMSIdentityProvider](https://docs.microsoft.com/powershell/module/azuread/new-azureadmsidentityprovider?view=azureadps-2.0-preview) 문서를 참조하세요. 
 
## <a name="how-do-i-remove-google-federation"></a>Google 페더레이션은 어떻게 제거하나요?
Google 페더레이션 설치 프로그램을 삭제할 수 있습니다. 이렇게 하면 이미 초대를 사용한 Google 게스트 사용자는 로그인할 수 없게 되지만, 디렉터리에서 해당 사용자를 삭제하고 다시 초대하여 리소스에 대한 액세스 권한을 다시 부여할 수 있습니다. 
 
### <a name="to-delete-google-federation-in-the-azure-ad-portal"></a>Azure AD 포털에서 Google 페더레이션을 삭제하려면 다음을 수행합니다. 
1. [Azure 포털](https://portal.azure.com)로 이동합니다. 왼쪽 창에서 **Azure Active Directory**를 선택합니다. 
2. **조직 관계**를 선택합니다.
3. 선택 **Id 공급자**합니다.
4. 에 **Google** 상황에 맞는 메뉴를 선택 (**...** )를 선택한 **삭제**합니다. 
   
   ![소셜 id 공급자에 대 한 삭제 옵션을 보여 주는 스크린샷](media/google-federation/google-social-identity-providers.png)

1. **예**를 선택하여 삭제를 확인합니다. 

### <a name="to-delete-google-federation-by-using-powershell"></a>PowerShell을 사용하여 Google 페더레이션을 삭제하려면 다음을 수행합니다. 
1. 그래프 모듈에 대한 Azure AD PowerShell의 최신 버전을 설치합니다([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview)).
2. `Connect-AzureAD`을 실행합니다.  
4. 로그인 프롬프트에서 관리되는 글로벌 관리자 계정으로 로그인합니다.  
5. 다음 명령을 입력합니다.

    `Remove-AzureADMSIdentityProvider -Id Google-OAUTH`

   > [!NOTE]
   > 자세한 내용은 [Remove-AzureADMSIdentityProvider](https://docs.microsoft.com/powershell/module/azuread/Remove-AzureADMSIdentityProvider?view=azureadps-2.0-preview)를 참조하세요. 
