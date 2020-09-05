---
title: '자습서: SharePoint 온-프레미스와 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory와 SharePoint 온-프레미스 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/15/2020
ms.author: miguego
ms.openlocfilehash: 996668751ee93d14b18e399035fd345f32c58fbe
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88552176"
---
# <a name="tutorial-azure-active-directory-single-sign-on-integration-with-sharepoint-on-premises"></a>자습서: SharePoint 온-프레미스와 Azure Active Directory Single Sign-On 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 SharePoint 온-프레미스를 통합하는 방법에 대해 알아봅니다. SharePoint 온-프레미스를 Azure AD와 통합하면 다음을 수행할 수 있습니다.

* Azure AD에서 SharePoint 온-프레미스에 액세스할 수 있는 사용자를 제어합니다.
* 사용자가 해당 Azure AD 계정으로 SharePoint 온-프레미스에 자동으로 로그인되도록 설정합니다.
* Azure Portal에서 계정을 관리합니다.

Azure AD와 SaaS(Software as a Service) 앱 통합에 대해 자세히 알아보려면 [Azure Active Directory를 사용한 애플리케이션 액세스 및 Single Sign-On이란?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)을 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

SharePoint 온-프레미스와 Azure AD의 통합을 구성하려면 다음 항목이 필요합니다.

* Azure AD 구독 Azure AD 환경이 없으면 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* SharePoint 2013 팜 이상

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD SSO(Single Sign-On)를 구성하고 테스트합니다. Azure AD의 사용자는 SharePoint 온-프레미스에 액세스할 수 있습니다.

## <a name="create-enterprise-applications-in-the-azure-portal"></a>Azure Portal에서 엔터프라이즈 애플리케이션 만들기

SharePoint 온-프레미스의 Azure AD 통합을 구성하려면 갤러리의 SharePoint 온-프레미스를 관리되는 SaaS 앱 목록에 추가해야 합니다.

갤러리에서 SharePoint 온-프레미스를 추가하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com)의 맨 왼쪽 창에서 **Azure Active Directory**를 선택합니다.

   > [!NOTE]
   > 요소를 사용할 수 없는 경우 맨 왼쪽 창의 위쪽에 있는 **모든 서비스** 링크를 통해 해당 요소를 열 수도 있습니다. 다음 개요에서 **Azure Active Directory** 링크는 **ID** 섹션에 있습니다. 필터 상자를 사용하여 검색할 수도 있습니다.

1. **엔터프라이즈 애플리케이션**으로 이동한 다음, **모든 애플리케이션**을 선택합니다.

1. 새 애플리케이션을 추가하려면 대화 상자 맨 위 있는 **새 애플리케이션**을 선택합니다.

1. 검색 상자에서 **SharePoint 온-프레미스**를 입력합니다. 결과 창에서 **SharePoint 온-프레미스**를 선택합니다.

    <kbd>![결과 목록의 SharePoint 온-프레미스](./media/sharepoint-on-premises-tutorial/search-new-app.png)</kbd>

1. SharePoint 온-프레미스 인스턴스의 이름을 지정하고, **추가**를 선택하여 애플리케이션을 추가합니다.

1. 새 엔터프라이즈 애플리케이션에서 **속성**을 선택하고, **사용자 할당 필요** 값을 확인합니다.

   <kbd>![사용자 할당 필요 설정/해제](./media/sharepoint-on-premises-tutorial/user-assignment-required.png)</kbd>

   이 시나리오에서 이 값은 **아니요**로 설정됩니다.

## <a name="configure-and-test-azure-ad"></a>Azure AD 구성 및 테스트

이 섹션에서는 SharePoint 온-프레미스에서 Azure AD SSO를 구성합니다. SSO가 작동하려면 Azure AD 사용자와 SharePoint 온-프레미스의 관련 사용자 간에 연결 관계를 설정합니다.

SharePoint 온-프레미스에서 Azure AD SSO를 구성하고 테스트하려면 다음 구성 요소를 완료합니다.

- [Azure AD Single Sign-On 구성](#configure-azure-ad-single-sign-on) - 사용자가 이 기능을 사용할 수 있도록 합니다.
- [SharePoint 온-프레미스 구성](#configure-sharepoint-on-premises) - 애플리케이션 쪽에서 SSO 설정을 구성합니다.
- [Azure Portal에서 Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user-in-the-azure-portal) - SSO에 대한 새 사용자를 Azure AD에 만듭니다.
- [Azure Portal에서 Azure AD 보안 그룹 만들기](#create-an-azure-ad-security-group-in-the-azure-portal) - SSO에 대한 새 보안 그룹을 Azure AD에 만듭니다.
- [SharePoint 온-프레미스에서 Azure AD 계정에 권한 부여](#grant-permissions-to-an-azure-ad-account-in-sharepoint-on-premises) - 권한을 Azure AD 사용자에게 부여합니다.
- [SharePoint 온-프레미스에서 Azure AD 그룹에 권한 부여](#grant-permissions-to-an-azure-ad-group-in-sharepoint-on-premises) - 권한을 Azure AD 그룹에 부여합니다.
- [Azure Portal에서 SharePoint 온-프레미스에 게스트 계정에 대한 액세스 권한 부여](#grant-access-to-a-guest-account-to-sharepoint-on-premises-in-the-azure-portal) - SharePoint 온-프레미스에 대한 권한을 Azure AD의 게스트 계정에 부여합니다.
- [여러 웹 애플리케이션에 대해 신뢰할 수 있는 ID 공급자 구성](#configure-the-trusted-identity-provider-for-multiple-web-applications) - 신뢰할 수 있는 동일한 ID 공급자를 여러 웹 애플리케이션에 사용합니다.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 Azure AD SSO를 사용하도록 설정합니다.

SharePoint 온-프레미스에서 Azure AD SSO를 구성하려면 다음을 수행합니다.

1. [Azure Portal](https://portal.azure.com/)에서 **Azure Active Directory** > **엔터프라이즈 애플리케이션**을 차례로 선택합니다. 이전에 만든 엔터프라이즈 애플리케이션 이름을 선택하고, **Single Sign-On**을 선택합니다.

1. **Single Sign-On 방법 선택** 대화 상자에서 **SAML** 모드를 선택하여 SSO를 사용하도록 설정합니다.
 
1. **SAML로 Single Sign-On 설정** 페이지에서 **편집** 아이콘을 선택하여 **기본 SAML 구성** 대화 상자를 엽니다.

1. **기본 SAML 구성** 섹션에서 다음 단계를 수행합니다.

    ![SharePoint 온-프레미스 도메인 및 URL SSO 정보](./media/sharepoint-on-premises-tutorial/sp-identifier-reply.png)

    1. **식별자** 상자에서 `urn:<sharepointFarmName>:<federationName>` 패턴을 사용하여 URL을 입력합니다.

    1. **회신 URL** 상자에서 `https://<YourSharePointSiteURL>/_trust/` 패턴을 사용하여 URL을 입력합니다.

    1. **로그온 URL** 상자에서 `https://<YourSharePointSiteURL>/` 패턴을 사용하여 URL을 입력합니다.
    1. **저장**을 선택합니다.

    > [!NOTE]
    > 이러한 값은 실제 값이 아닙니다. 이러한 값을 실제 로그온 URL, 식별자 및 회신 URL로 업데이트하세요.

1. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **다운로드**를 선택하여 요구 사항에 따라 제공된 옵션에서 **인증서(Base64)** 를 다운로드하여 컴퓨터에 저장합니다.

    ![인증서 다운로드 링크](./media/sharepoint-on-premises-tutorial/certificatebase64.png)

1. **SharePoint 온-프레미스 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.
    
    - **로그인 URL**
    
        로그인 URL을 복사하고, https://login.microsoftonline.com/2c4f1a9f-be5f-10ee-327d-a95dac567e4f/wsfed 처럼 표시되도록 끝에 있는 **/saml2**를 **/wsfed**로 바꿉니다. (이 URL은 정확하지 않습니다.)

    - **Azure AD 식별자**
    - **로그아웃 URL**

    > [!NOTE]
    > 이 URL은 SharePoint에 있는 그대로 사용할 수 없습니다. **/saml2**를 **/wsfed**로 바꿔야 합니다. SharePoint 온-프레미스 애플리케이션에서 SAML 1.1 토큰을 사용하므로 Azure AD에는 SharePoint 서버로부터의 WS Fed 요청이 필요합니다. 그러면 인증 후에 SAML 1.1 토큰이 발급됩니다.

### <a name="configure-sharepoint-on-premises"></a>SharePoint 온-프레미스 구성

1. SharePoint Server 2016에서 신뢰할 수 있는 새 ID 공급자를 만듭니다.

    SharePoint 서버에 로그인하고, SharePoint 관리 셸을 엽니다. 다음 값을 입력합니다.
    - **$realm** - Azure Portal의 SharePoint 온-프레미스 도메인 및 URL 섹션의 식별자 값입니다.
    - **$wsfedurl** - SSO 서비스 URL입니다.
    - **$filepath** - Azure Portal에서 인증서 파일을 다운로드한 파일의 경로입니다.

    다음 명령을 실행하여 신뢰할 수 있는 새 ID 공급자를 구성합니다.

    > [!TIP]
    > PowerShell을 처음 사용하거나 PowerShell 작동 방법에 대해 자세히 알아보려면 [SharePoint PowerShell](https://docs.microsoft.com/powershell/sharepoint/overview?view=sharepoint-ps)을 참조하세요.


    ```
    $realm = "urn:sharepoint:sps201x"
    $wsfedurl="https://login.microsoftonline.com/2c4f1a9f-be5f-10ee-327d-a95dac567e4f/wsfed"
    $filepath="C:\temp\SharePoint 2019 OnPrem.cer"
    $cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($filepath)
    New-SPTrustedRootAuthority -Name "AzureAD" -Certificate $cert
    $map1 = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name" -IncomingClaimTypeDisplayName "name" -LocalClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn"
    $map2 = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.microsoft.com/ws/2008/06/identity/claims/role" -IncomingClaimTypeDisplayName "Role" -SameAsIncoming
    $ap = New-SPTrustedIdentityTokenIssuer -Name "AzureAD" -Description "Azure AD SharePoint server 201x" -realm $realm -ImportTrustCertificate $cert -ClaimsMappings $map1,$map2 -SignInUrl $wsfedurl -IdentifierClaim $map1.InputClaimType
    ```
1. 애플리케이션에 대해 신뢰할 수 있는 ID 공급자를 사용하도록 설정합니다.

    1. **중앙 관리**에서 **웹 애플리케이션 관리**로 이동하여 Azure AD를 사용하여 보호하려는 웹 애플리케이션을 선택합니다.

    1. 리본 메뉴에서 **인증 공급자**를 선택하고, 사용하려는 영역을 선택합니다.

    1. **신뢰할 수 있는 ID 공급자**를 선택하고, 방금 등록한 *AzureAD*라는 ID 공급자를 선택합니다.

    1. **확인**을 선택합니다.

    ![인증 공급자 구성](./media/sharepoint-on-premises-tutorial/config-auth-provider.png)

### <a name="create-an-azure-ad-test-user-in-the-azure-portal"></a>Azure Portal에서 Azure AD 테스트 사용자 만들기

이 섹션에서는 Azure Portal에서 테스트 사용자를 만듭니다.

1. Azure Portal의 맨 왼쪽 창에서 **Azure Active Directory**를 선택합니다. **관리** 창에서 **사용자**를 선택합니다.

1. 화면의 위쪽에서 **모든 사용자** > **새 사용자**를 차례로 선택합니다.

1. **사용자 만들기**를 선택하고, 사용자 속성에서 다음 단계를 수행합니다. 테넌트 접미사 또는 확인된 도메인을 사용하여 Azure AD에서 사용자를 만들 수 있습니다. 

    1. **이름** 상자에서 사용자 이름을 입력합니다. 여기서는 **TestUser**를 사용했습니다.
  
    1. **사용자 이름** 상자에 `TestUser@yourcompanydomain.extension`을 입력합니다. 이 예제에서는 `TestUser@contoso.com`을 보여 줍니다.

       ![사용자 대화 상자](./media/sharepoint-on-premises-tutorial/user-properties.png)

    1. **암호 표시** 확인란을 선택한 다음, **암호** 상자에 표시되는 값을 적어 둡니다.

    1. **만들기**를 선택합니다.

    1. 이제 사이트를 TestUser@contoso.com과 공유하고, 이 사용자가 액세스하도록 허용할 수 있습니다.

### <a name="create-an-azure-ad-security-group-in-the-azure-portal"></a>Azure Portal에서 Azure AD 보안 그룹 만들기

1. **Azure Active Directory** > **그룹**을 선택합니다.

1. **새 그룹**을 선택합니다.

1. **그룹 유형**, **그룹 이름**, **그룹 설명**, **멤버 자격 유형** 상자를 작성합니다. 화살표를 선택하여 멤버를 선택한 다음, 그룹에 추가하려는 멤버를 검색하거나 선택합니다. **선택**을 선택하여 선택한 멤버를 추가한 다음, **만들기**를 선택합니다.

![Azure AD 보안 그룹 만들기](./media/sharepoint-on-premises-tutorial/new-group.png)

### <a name="grant-permissions-to-an-azure-ad-account-in-sharepoint-on-premises"></a>SharePoint 온-프레미스에서 Azure AD 계정에 권한 부여

SharePoint 온-프레미스에서 액세스 권한을 Azure AD 사용자에게 부여하려면 사이트 모음을 공유하거나 Azure AD 사용자를 사이트 모음 그룹 중 하나에 추가합니다. 이제 사용자는 Azure AD의 ID를 사용하여 SharePoint 201x에 로그인할 수 있지만, 여전히 사용자 환경을 개선할 수 있는 기회가 있습니다. 예를 들어, 사용자 선택에서 사용자를 검색하면 여러 검색 결과가 표시됩니다. 클레임 매핑에서 만들어진 각 클레임 유형에 대한 검색 결과가 있습니다. 사용자 선택기를 사용하여 사용자를 선택하려면 사용자 이름을 정확하게 입력하고 **이름** 클레임 결과를 선택해야 합니다.

![클레임 검색 결과](./media/sharepoint-on-premises-tutorial/claims-search-results.png)

검색한 값에 대한 유효성 검사가 없으므로 맞춤법 오류가 발생하거나 사용자가 잘못된 클레임 유형을 실수로 선택할 수 있습니다. 이 경우 사용자는 리소스에 성공적으로 액세스하지 못할 수 있습니다.

사용자 선택기를 사용하여 이 시나리오를 해결하기 위해 [AzureCP](https://yvand.github.io/AzureCP/)라는 오픈 소스 솔루션에서 SharePoint 2013, 2016 및 2019에 대한 사용자 지정 클레임 공급자를 제공합니다. Microsoft Graph API를 사용하여 사용자가 입력한 내용을 확인하고 유효성을 검사합니다. 자세한 내용은 [AzureCP](https://yvand.github.io/AzureCP/)를 참조하세요.

  > [!NOTE]
  > AzureCP를 사용하지 않으면 Azure AD 그룹의 ID를 추가하여 그룹을 추가할 수 있지만, 이 방법은 사용자에게 친숙하고 안정적이지 않습니다. 다음과 같이 표시됩니다.
  > 
  >![SharePoint 그룹에 Azure AD 그룹 추가](./media/sharepoint-on-premises-tutorial/adding-group-by-id.png)
  
### <a name="grant-permissions-to-an-azure-ad-group-in-sharepoint-on-premises"></a>SharePoint 온-프레미스에서 Azure AD 그룹에 권한 부여

Azure AD 보안 그룹을 SharePoint 온-프레미스에 할당하려면 SharePoint 서버에 대한 사용자 지정 클레임 공급자를 사용해야 합니다. 이 예에서는 AzureCP를 사용합니다.

 > [!NOTE]
 > AzureCP는 Microsoft 제품이 아니며 Microsoft 지원에서 지원되지 않습니다. 온-프레미스 SharePoint 팜에서 AzureCP를 다운로드, 설치 및 구성하려면 [AzureCP](https://yvand.github.io/AzureCP/) 웹 사이트를 참조하세요. 

1. SharePoint 온-프레미스 팜 또는 다른 사용자 지정 클레임 공급자 솔루션에서 AzureCP를 구성합니다. AzureCP를 구성하려면 이 [AzureCP](https://yvand.github.io/AzureCP/Register-App-In-AAD.html) 웹 사이트를 참조하세요.

1. Azure Portal에서 **Azure Active Directory** > **엔터프라이즈 애플리케이션**을 차례로 선택합니다. 이전에 만든 엔터프라이즈 애플리케이션 이름을 선택하고, **Single Sign-On**을 선택합니다.

1. **SAML로 Single Sign-On 설정** 페이지에서 **사용자 특성 및 클레임** 섹션을 편집합니다.

1. **그룹 클레임 추가**를 선택합니다.

1. 클레임에서 반환되어야 하는 사용자와 연결된 그룹을 선택합니다. 이 경우 **모든 그룹**을 선택합니다. **원본 특성** 섹션에서 **그룹 ID**, **저장**을 차례로 선택합니다.

SharePoint 온-프레미스에서 액세스 권한을 Azure AD 보안 그룹에 부여하려면 사이트 모음을 공유하거나 Azure AD 사용자를 사이트 모음 그룹 중 하나에 추가합니다.

1. **SharePoint 사이트 모음**으로 이동합니다. 사이트 모음에 대한 **사이트 설정** 아래에서 **사용자 및 그룹**을 선택합니다. 

1. SharePoint 그룹을 선택한 다음, **새로 만들기** > **이 그룹에 사용자 추가**를 차례로 선택합니다. 그룹 이름을 입력함에 따라 사용자 선택기에서 Azure AD 보안 그룹이 표시됩니다.

    ![SharePoint 그룹에 Azure AD 그룹 추가](./media/sharepoint-on-premises-tutorial/permission-azure-ad-group.png)

### <a name="grant-access-to-a-guest-account-to-sharepoint-on-premises-in-the-azure-portal"></a>Azure Portal에서 SharePoint 온-프레미스에 게스트 계정에 대한 액세스 권한 부여

이제 UPN이 수정되었으므로 SharePoint 사이트에 대한 액세스 권한을 일관된 방식으로 게스트 계정에 부여할 수 있습니다. 예를 들어 `jdoe@outlook.com` 사용자는 `jdoe_outlook.com#ext#@TENANT.onmicrosoft.com`으로 표시됩니다. 사이트를 외부 사용자와 공유하려면 Azure Portal의 **사용자 특성 및 클레임** 섹션에서 몇 가지 수정 사항을 추가해야 합니다.

1. Azure Portal에서 **Azure Active Directory** > **엔터프라이즈 애플리케이션**을 차례로 선택합니다. 이전에 만든 엔터프라이즈 애플리케이션 이름을 선택하고, **Single Sign-On**을 선택합니다.

1. **SAML로 Single Sign-On 설정** 페이지에서 **사용자 특성 및 클레임** 섹션을 편집합니다.

1. **클레임 필요** 영역에서 **고유한 사용자 ID(이름 ID)** 를 선택합니다.

1. **원본 특성** 속성을 **user.localuserprincipalname** 값으로 변경하고, **저장**을 선택합니다.

    ![초기 사용자 특성 및 클레임](./media/sharepoint-on-premises-tutorial/manage-claim.png)

1. 리본 메뉴를 사용하여 **SAML 기반 로그온**으로 돌아갑니다. 이제 **사용자 특성 및 클레임** 섹션은 다음과 같습니다. 

    ![최종 사용자 특성 및 클레임](./media/sharepoint-on-premises-tutorial/user-attributes-claims-final.png)

    > [!NOTE]
    > 이 설정에서는 성과 이름이 필요하지 않습니다.

1. Azure Portal의 맨 왼쪽 창에서 **Azure Active Directory**를 선택한 다음, **사용자**를 선택합니다.

1. **새 게스트 사용자**를 선택합니다.

1. **사용자 초대** 옵션을 선택합니다. 사용자 속성을 입력하고 **초대**를 선택합니다.

1. 이제 사이트를 MyGuestAccount@outlook.com과 공유하고, 이 사용자가 액세스하도록 허용할 수 있습니다.

    ![게스트 계정과 사이트 공유](./media/sharepoint-on-premises-tutorial/sharing-guest-account.png)

### <a name="configure-the-trusted-identity-provider-for-multiple-web-applications"></a>여러 웹 애플리케이션에 대해 신뢰할 수 있는 ID 공급자 구성

이 구성은 단일 웹 애플리케이션에서 작동하지만 신뢰할 수 있는 동일한 ID 공급자를 여러 웹 애플리케이션에 사용하려는 경우 추가 구성이 필요합니다. 예를 들어 `https://sales.contoso.com` URL를 사용하도록 웹 애플리케이션을 확장하고 이제 사용자를 `https://marketing.contoso.com`에 인증하려고 한다고 가정합니다. 이렇게 하려면 WReply 매개 변수를 인식하도록 ID 공급자를 업데이트하고, 회신 URL을 추가하도록 Azure AD에서 애플리케이션 등록을 업데이트합니다.

1. Azure Portal에서 **Azure Active Directory** > **엔터프라이즈 애플리케이션**을 차례로 선택합니다. 이전에 만든 엔터프라이즈 애플리케이션 이름을 선택하고, **Single Sign-On**을 선택합니다.

1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성**을 편집합니다.

    ![기본 SAML 구성](./media/sharepoint-on-premises-tutorial/add-reply-url.png)

1. **회신 URL(Assention Consumer Service URL)** 에 대해 추가 웹 애플리케이션에 대한 URL을 추가하고, **저장**을 선택합니다.

    ![기본 SAML 구성 편집](./media/sharepoint-on-premises-tutorial/reply-url-for-web-application.png)

1. SharePoint 서버에서 SharePoint 201x 관리 셸을 열고 다음 명령을 실행합니다. 이전에 사용한 신뢰할 수 있는 ID 토큰 발급자의 이름을 사용합니다.
    ```
    $t = Get-SPTrustedIdentityTokenIssuer "AzureAD"
    $t.UseWReplyParameter=$true
    $t.Update()
    ```
1. **중앙 관리**에서 웹 애플리케이션으로 이동하여 기존의 신뢰할 수 있는 ID 공급자를 사용하도록 설정합니다.

내부 사용자에 대한 액세스 권한을 SharePoint 온-프레미스 인스턴스에 부여하려는 다른 시나리오가 있을 수 있습니다. 이 시나리오의 경우 온-프레미스 사용자를 Azure AD와 동기화할 수 있도록 Microsoft Azure Active Directory Connect를 배포해야 합니다. 이 설정은 다른 문서에서 설명합니다.

## <a name="additional-resources"></a>추가 리소스

- [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)
- [Azure Active Directory의 조건부 액세스란?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
- [Azure Active Directory를 사용하는 하이브리드 ID란?](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-hybrid-identity)
