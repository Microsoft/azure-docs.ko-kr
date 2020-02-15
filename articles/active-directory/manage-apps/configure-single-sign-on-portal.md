---
title: Single Sign-On 구성 - Azure Active Directory | Microsoft Docs
description: 이 자습서는 Azure Portal을 사용하여 Azure AD(Azure Active Directory)에서 애플리케이션에 대해 SAML 기반 Single Sign-On을 구성합니다.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: tutorial
ms.workload: identity
ms.date: 04/08/2019
ms.author: mimart
ms.reviewer: arvinh,luleon
ms.collection: M365-identity-device-management
ROBOTS: NOINDEX
ms.openlocfilehash: a415ac749d0d322bc2f71f64d4bec6e32ad1f12e
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/07/2020
ms.locfileid: "77063495"
---
# <a name="how-to-configure-saml-based-single-sign-on"></a>SAML 기반 Single Sign-On을 구성하는 방법

앱이 Azure AD 엔터프라이즈 애플리케이션에 추가되면 Single Sign-On 설정을 구성합니다. 이 문서에서는 비갤러리 앱에 대한 SAML 기반 Single Sign-On을 구성하는 방법에 대해 설명합니다. 

> [!NOTE]
> 갤러리 앱을 추가하나요? 이에 대한 단계별 설치 지침은 [SaaS 앱 자습서 목록](../saas-apps/tutorial-list.md)에서 확인하세요.

비갤러리 애플리케이션에 대해 *코드를 작성하지 않고* Single Sign-On을 구성하려면 Azure AD Premium 구독이 있어야 하며 애플리케이션이 SAML 2.0을 지원해야 합니다. Azure AD 버전에 대한 자세한 내용은 [Azure AD 가격 책정](https://azure.microsoft.com/pricing/details/active-directory/)에서 확인할 수 있습니다.

## <a name="before-you-begin"></a>시작하기 전에

- 애플리케이션이 Azure AD 테넌트에 추가되지 않은 경우 [갤러리 앱 추가](add-gallery-app.md) 또는 [비갤러리 앱 추가](add-non-gallery-app.md)를 참조하세요.
- 다음 설정에 대한 정확한 정보를 얻으려면 애플리케이션 공급업체에 문의하세요.

    | 기본 SAML 구성 설정 | SP 시작 | idP 시작 | Description |
    |:--|:--|:--|:--|
    | 식별자(엔터티 ID) | 일부 앱의 경우 필수 | 일부 앱의 경우 필수 | 구성될 Single Sign-On에 대해 애플리케이션을 고유하게 식별합니다. Azure AD는 SAML 토큰의 대상 매개 변수로 애플리케이션에 식별자를 보냅니다. 애플리케이션이 식별자의 유효성을 검사해야 합니다. 또한 이 값은 애플리케이션에서 제공하는 모든 SAML 메타데이터 내에서 엔터티 ID로 표시됩니다. *이 값은 애플리케이션에서 보낸 **AuthnRequest**(SAML 요청)에서 **Issuer**(발급자) 요소로 찾을 수 있습니다.* |
    | 회신 URL | 옵션 | 필수 | 애플리케이션이 SAML 토큰을 수신해야 하는 위치를 지정합니다. 회신 URL은 ACS(Assertion Consumer Service) URL이라고도 합니다. |
    | 로그온 URL | 필수 | 지정하지 않음 | 사용자가 이 URL을 열면 서비스 공급자가 Azure AD를 리디렉션하여 사용자를 인증하고 로그온하도록 합니다. Azure AD는 URL을 사용하여 Office 365 또는 Azure AD 액세스 패널에서 애플리케이션을 시작합니다. 비어 있는 경우 사용자가 애플리케이션을 시작할 때 Azure AD에서 ID 공급자를 사용하여 Single Sign-On을 시작합니다.|
    | 릴레이 상태 | 옵션 | 옵션 | 인증이 완료되면 사용자를 리디렉션할 위치를 애플리케이션에 지정합니다. 일반적으로 이 값은 애플리케이션에 대한 올바른 URL입니다. 그러나 일부 애플리케이션에서는 이 필드를 다르게 사용합니다. 자세한 내용은 애플리케이션 공급 업체에 요청하세요.
    | 로그아웃 URL | 옵션 | 옵션 | SAML 로그아웃 응답을 애플리케이션에 다시 보내는 데 사용됩니다.

## <a name="step-1-edit-the-basic-saml-configuration"></a>1단계. 기본 SAML 구성 편집

1. [Azure Portal](https://portal.azure.com)에 Azure AD 테넌트의 클라우드 애플리케이션 관리자 또는 애플리케이션 관리자로 로그인합니다.

1. **Azure Active Directory** > **엔터프라이즈 애플리케이션**으로 차례로 이동하고, 목록에서 애플리케이션을 선택합니다. 
   
   - 애플리케이션을 검색하려면 **애플리케이션 유형** 메뉴에서 **모든 애플리케이션**, **적용**을 차례로 선택합니다. 검색 상자에서 애플리케이션 이름을 입력한 다음, 결과에서 애플리케이션을 선택합니다.

1. **관리** 섹션 아래에서 **Single Sign-On**을 선택합니다. 

1. **SAML**을 선택합니다. **SAML로 Single Sign-On 설정 - 미리 보기** 페이지가 표시됩니다.

1. 기본 SAML 구성 옵션을 편집하려면 **기본 SAML 구성** 섹션의 오른쪽 위 모서리에 있는 **편집** 아이콘(연필 모양)을 선택합니다.

     ![인증서 구성](media/configure-single-sign-on-portal/basic-saml-configuration-edit-icon.png)

1. 해당 필드에서 [시작하기 전에](#before-you-begin) 섹션에 설명된 정보를 입력합니다.

1. 페이지 맨 위에서 **저장**을 선택합니다.

## <a name="step-2-configure-user-attributes-and-claims"></a>2단계. 사용자 특성 및 클레임 구성 

애플리케이션에는 사용자가 로그인할 때 Azure AD로부터 받은 SAML 토큰의 특정 사용자 특성 또는 클레임이 필요할 수 있습니다. 예를 들어 특정 클레임 URI 또는 클레임 값이 필요할 수도 있고, **이름**이 Microsoft ID 플랫폼에 저장된 사용자 이름 이외의 다른 이름이어야 할 수도 있습니다. 갤러리 앱에 대한 요구 사항은 [애플리케이션 관련 자습서](../saas-apps/tutorial-list.md)에 설명되어 있거나 애플리케이션 공급업체에 문의할 수 있습니다. 사용자 특성 및 클레임을 구성하는 일반적인 단계는 다음과 같습니다.

1. **사용자 특성 및 클레임** 섹션의 오른쪽 위 모서리에서 **편집** 아이콘(연필)을 선택합니다.

1. **이름 식별자 값**을 확인합니다. 기본값은 *user.principalname*입니다. 사용자 ID는 애플리케이션 내에서 각 사용자를 고유하게 식별합니다. 예를 들어 이메일 주소가 사용자 이름 및 고유 식별자 모두인 경우 값을 *user.mail*로 설정합니다.

1. **이름 식별자 값**을 수정하려면 **이름 식별자 값** 필드에 대한 **편집** 아이콘(연필 모양)을 선택합니다. 필요에 따라 식별자 형식과 원본을 적절하게 변경합니다. 자세한 내용은 [NameId 편집](https://docs.microsoft.com/azure/active-directory//develop/active-directory-saml-claims-customization#editing-nameid)을 참조하세요. 완료되면 변경 내용을 저장합니다. 
 
1. 그룹 클레임을 구성하려면 **클레임에서 반환되는 그룹** 필드에 대한 **편집** 아이콘을 선택합니다. 자세한 내용은 [그룹 클레임 구성](../hybrid/how-to-connect-fed-group-claims.md)을 참조하세요.

3. 클레임을 추가하려면 페이지의 위쪽에서 **새 클레임 추가**를 선택합니다. **이름**을 입력하고 적절한 원본을 선택합니다. **특성** 원본을 선택하는 경우 사용하려는 **원본 특성**을 선택해야 합니다. **번역** 원본을 선택하는 경우 사용하려는 **변환** 및 **매개 변수 1**을 선택해야 합니다. 자세한 내용은 [애플리케이션 관련 클레임 추가](https://docs.microsoft.com/azure/active-directory//develop/active-directory-saml-claims-customization#adding-application-specific-claims)를 참조하세요. 완료되면 변경 내용을 저장합니다. 

4. **저장**을 선택합니다. 테이블에 새 클레임이 표시됩니다.

   > [!NOTE]
   > Azure AD에서 애플리케이션에 대한 SAML 토큰을 사용자 지정하는 다른 방법은 다음 리소스를 참조하세요.
   >- Azure Portal를 통해 사용자 지정 역할을 만들려면 [역할 클레임 구성](../develop/active-directory-enterprise-app-role-management.md)을 참조하세요.
   >- PowerShell을 통해 클레임을 사용자 지정하려면 [클레임 사용자 지정 - PowerShell](../develop/active-directory-claims-mapping.md)을 참조하세요.
   >- 애플리케이션에 대한 선택적 클레임을 구성하도록 애플리케이션 매니페스트를 수정하려면 [선택적 클레임 구성](../develop/active-directory-optional-claims.md)을 참조하세요.
   >- 새로 고침 토큰, 액세스 토큰, 세션 토큰 및 ID 토큰에 대 한 토큰 수명 정책을 설정하려면 [토큰 수명 구성](../develop/active-directory-configurable-token-lifetimes.md)을 참조하세요. 또는 Azure AD 조건부 액세스를 통해 인증 세션을 제한하려면 [인증 세션 관리 기능](https://go.microsoft.com/fwlink/?linkid=2083106)을 참조하세요.

## <a name="step-3-manage-the-saml-signing-certificate"></a>3단계. SAML 서명 인증서 관리

Azure AD는 인증서를 사용하여 애플리케이션에 보내는 SAML 토큰에 서명합니다. **SAML로 Single Sign-On 설정** 페이지에서 활성 인증서를 보거나 다운로드할 수 있습니다. 인증서를 업데이트하거나 만들거나 가져올 수도 있습니다. 갤러리 애플리케이션의 경우 인증서 형식에 대한 자세한 내용은 애플리케이션의 SAML 설명서에서 확인할 수 있습니다([애플리케이션 관련 자습서](../saas-apps/tutorial-list.md) 참조). 

1. **SAML 서명 인증서** 섹션으로 이동합니다. 애플리케이션 유형에 따라 인증서를 Base64 형식, 원시 형식 또는 페더레이션 메타데이터 XML로 다운로드하는 옵션이 표시됩니다. 또한 Azure AD는 `https://login.microsoftonline.com/<Directory ID>/federationmetadata/2007-06/federationmetadata.xml?appid=<Application ID>` 형식의 애플리케이션과 관련된 메타데이터에 액세스할 수 있는 **앱 페더레이션 메타데이터 URL**을 제공합니다.

1. 인증서를 관리하거나 만들거나 가져오려면 **SAML 서명 인증서** 섹션의 오른쪽 위 모서리에서 **편집** 아이콘(연필)을 선택하고, 다음 중 하나를 수행합니다.

   - 새 인증서를 만들려면 **새 인증서**, **만료 날짜**, **저장**을 차례로 선택합니다. 인증서를 활성화하려면 상황에 맞는 메뉴( **...** ), **인증서를 활성 상태로 만들기**를 차례로 선택합니다.
   - 프라이빗 키 및 pfx 자격 증명을 사용하여 인증서를 업로드하려면 **인증서 가져오기**를 선택하여 해당 인증서로 이동합니다. **PFX 암호**를 입력한 다음, **추가**를 선택합니다.  
   - 고급 인증서 서명 옵션을 구성하려면 다음 옵션을 사용합니다. 이러한 옵션에 대한 설명은 [고급 인증서 서명 옵션](certificate-signing-options.md) 문서를 참조하세요.
      - **서명 옵션** 드롭다운 목록에서 **SAML 응답 서명**, **SAML 어설션 서명** 또는 **SAML 응답 및 어설션 서명**을 선택합니다.
      - **서명 알고리즘** 드롭다운 목록에서 **SHA-1** 또는 **SHA-256**을 선택합니다.
   - 활성 인증서의 만료 날짜가 가까워질 때 추가 사용자에게 알리려면 **알림 이메일 주소** 필드에서 이메일 주소를 입력합니다.

1. **SAML 서명 인증서** 섹션의 위쪽에서 **저장**을 선택합니다. 

## <a name="step-4-set-up-the-application-to-use-azure-ad"></a>4단계. Azure AD를 사용하도록 애플리케이션 설정

**\<applicationName> 설정** 섹션에는 애플리케이션에서 구성해야 하는 값이 나열되어 있으므로 Azure AD를 SAML ID 공급자로 사용합니다. 필요한 값은 애플리케이션에 따라 달라집니다. 자세한 내용은 애플리케이션의 SAML 설명서를 참조합니다.

1. **\<applicationName> 설정** 섹션까지 아래로 스크롤합니다. 
2. 필요에 따라 이 섹션의 각 행에서 값을 복사하고, 해당 값을 애플리케이션에 추가하기 위한 애플리케이션 관련 지침을 따릅니다. 갤러리 앱의 경우 **단계별 지침 보기**를 선택하여 설명서를 볼 수 있습니다. 
   - **로그인 URL** 및 **로그아웃 URL** 값은 모두 동일한 엔드포인트로 확인됩니다. 이 엔드포인트는 Azure AD 인스턴스에 대한 SAML 요청 처리 엔드포인트입니다. 
   - **Azure AD 식별자**는 애플리케이션에 발급된 SAML 토큰의 **Issuer**(발급자) 값입니다.
1. 모든 값을 해당 필드에 붙여넣은 다음, **저장**을 선택합니다.

## <a name="step-5-validate-single-sign-on"></a>5단계. Single Sign-On 유효성 검사

설정을 테스트하여 Single Sign-On이 관리자에게 적합한지 확인할 준비가 되었습니다.  

1. 애플리케이션의 Single Sign-On 설정을 엽니다. 
2. **<applicationName>을(를) 사용하여 Single Sign-On 유효성 검사** 섹션으로 스크롤합니다. 이 자습서에서 이 섹션은 **GitHub-test 설정**이라고 합니다.
3. **테스트**를 선택합니다. 테스트 옵션이 표시됩니다.
4. **현재 사용자로 로그인**을 선택합니다. 

로그온에 성공하면 사용자 및 그룹을 SAML 애플리케이션에 할당할 준비가 된 것입니다.
오류 메시지가 표시되면 다음 단계를 수행합니다.

1. 세부 정보를 **어떤 오류인 것 같습니까?** 상자에 복사하고 붙여넣습니다.

    !["어떤 오류인 것 같습니까" 상자를 사용하여 해결 지침을 가져옵니다.](media/configure-single-sign-on-portal/error-guidance.png)

1. **해결 지침 가져오기**를 선택합니다. 근본 원인 및 해결 지침이 표시됩니다.  이 예제에서 사용자는 애플리케이션에 할당되지 않았습니다.
1. 해결 지침을 읽은 다음, 가능한 경우 문제를 해결합니다.
1. 성공적으로 완료될 때까지 테스트를 다시 실행합니다.

## <a name="next-steps"></a>다음 단계

- [애플리케이션에 사용자 또는 그룹 할당](methods-for-assigning-users-and-groups.md)
- [자동 사용자 계정 프로비저닝 구성](../app-provisioning/configure-automatic-user-provisioning-portal.md)
