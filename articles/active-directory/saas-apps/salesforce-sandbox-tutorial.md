---
title: '자습서: Salesforce Sandbox와 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory와 Salesforce Sandbox 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/16/2020
ms.author: jeedes
ms.openlocfilehash: 277acbc84ab435ce1076c30a1e49f6ffdd2a0586
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88543723"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-salesforce-sandbox"></a>자습서: Salesforce Sandbox와 Azure Active Directory SSO(Single Sign-On) 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Salesforce Sandbox를 통합하는 방법에 대해 알아봅니다. Azure AD와 Salesforce Sandbox를 통합하면 다음을 수행할 수 있습니다.

* Salesforce Sandbox에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어합니다.
* 사용자가 자신의 Azure AD 계정으로 Salesforce Sandbox에 자동으로 로그인되도록 설정합니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory를 사용한 애플리케이션 액세스 및 Single Sign-On이란 무엇인가요?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)를 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

시작하려면 다음 항목이 필요합니다.

* Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* Salesforce Sandbox SSO(Single Sign-On)가 설정된 구독입니다.

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

* Salesforce Sandbox는 **SP 및 IDP** 시작 SSO를 지원합니다.
* Salesforce Sandbox는 **Just In Time** 사용자 프로비저닝을 지원합니다.
* Salesforce Sandbox는 [**자동화된** 사용자 프로비저닝](salesforce-sandbox-provisioning-tutorial.md)을 지원합니다.
* Salesforce Sandbox를 구성한 후에는 세션 제어를 적용하여 조직의 중요한 데이터의 반출 및 침입을 실시간으로 보호할 수 있습니다. 세션 제어는 조건부 액세스에서 확장됩니다. [Microsoft Cloud App Security를 사용하여 세션 제어를 적용하는 방법 알아보기](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-salesforce-sandbox-from-the-gallery"></a>갤러리에서 Salesforce Sandbox를 추가합니다.

Salesforce Sandbox의 Azure AD 통합을 구성하려면 갤러리의 Salesforce Sandbox를 관리되는 SaaS 앱 목록에 추가해야 합니다.

1. [Azure Portal](https://portal.azure.com)에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션**으로 이동한 다음, **모든 애플리케이션**을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션**을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에 **Salesforce Sandbox**를 입력합니다.
1. 결과 패널에서 **Salesforce Sandbox**를 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.


## <a name="configure-and-test-azure-ad-single-sign-on-for-salesforce-sandbox"></a>Salesforce Sandbox에 대한 Azure AD Single Sign-On 구성 및 테스트

**B.Simon**이라는 테스트 사용자를 사용하여 Salesforce Sandbox에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 Salesforce Sandbox의 관련 사용자 간에 연결 관계를 설정해야 합니다.

Salesforce Sandbox에서 Azure AD SSO를 구성하고 테스트하려면 다음 구성 요소를 완료합니다.

1. **[Azure AD SSO 구성](#configure-azure-ad-sso)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
    * **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - B.Simon을 사용하여 Azure AD Single Sign-On을 테스트합니다.
    * **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - B. Simon이 Azure AD Single Sign-On을 사용할 수 있도록 합니다.
1. **[Salesforce Sandbox SSO 구성](#configure-salesforce-sandbox-sso)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
    * **[Salesforce Sandbox 테스트 사용자 만들기](#create-salesforce-sandbox-test-user)** - B.Simon의 Azure AD 표현과 연결된 해당 사용자를 Salesforce Sandbox에 만듭니다.
1. **[SSO 테스트](#test-sso)** - 구성이 작동하는지 여부를 확인합니다.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)의 **Salesforce Sandbox** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾아 **Single Sign-On**을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML**을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성**에 대한 편집(연필 모양) 아이콘을 클릭하여 설정을 편집합니다.

   ![기본 SAML 구성 편집](common/edit-urls.png)

4. **서비스 공급자 메타데이터 파일**이 있고 **IDP** 시작 모드에서 구성하려면 **기본 SAML 구성** 섹션에서 다음 단계를 수행합니다.

    a. **메타데이터 파일 업로드**를 클릭합니다.

    ![메타데이터 파일 업로드](common/upload-metadata.png)

    b. **폴더 로고**를 클릭하여 메타데이터 파일을 선택하고 **업로드**를 클릭합니다.

    ![메타데이터 파일 선택](common/browse-upload-metadata.png)

    > [!NOTE]
    > 자습서 뒷부분에서 설명하는 Salesforce Sandbox 관리자 포털에서 서비스 공급자 메타데이터 파일을 가져옵니다.

    다. 메타데이터 파일을 성공적으로 업로드한 후 **회신 URL** 값은 **회신 URL** 텍스트 상자에 자동으로 채워집니다.

    ![이미지](common/both-replyurl.png)

    > [!Note]
    > **회신 URL** 값이 자동으로 입력되지 않으면 요구 사항에 따라 수동으로 값을 입력합니다.

5. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **다운로드**를 클릭하여 요구 사항에 따라 제공된 옵션에서 **메타데이터 XML**를 다운로드하고 컴퓨터에 저장합니다.

    ![인증서 다운로드 링크](common/metadataxml.png)

6. **Salesforce Sandbox 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

    ![구성 URL 복사](common/copy-configuration-urls.png)

    a. 로그인 URL

    b. Azure AD 식별자

    다. 로그아웃 URL

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기

이 섹션에서는 Azure Portal에서 B.Simon이라는 테스트 사용자를 만듭니다.

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory**, **사용자**, **모든 사용자**를 차례로 선택합니다.
1. 화면 위쪽에서 **새 사용자**를 선택합니다.
1. **사용자** 속성에서 다음 단계를 수행합니다.
   1. **이름** 필드에 `B.Simon`을 입력합니다.  
   1. **사용자 이름** 필드에서 username@companydomain.extension을 입력합니다. `B.Simon@contoso.com`)을 입력합니다.
   1. **암호 표시** 확인란을 선택한 다음, **암호** 상자에 표시된 값을 적어둡니다.
   1. **만들기**를 클릭합니다.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 B.Simon에게 Salesforce Sandbox에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션**을 선택한 다음, **모든 애플리케이션**을 선택합니다.
1. 애플리케이션 목록에서 **Salesforce Sandbox**를 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고 **사용자 및 그룹**을 선택합니다.

   !["사용자 및 그룹" 링크](common/users-groups-blade.png)

1. **사용자 추가**를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![사용자 추가 링크](common/add-assign-user.png)

1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **B.Simon**을 선택한 다음, 화면 아래쪽에서 **선택** 단추를 클릭합니다.
1. SAML 어설션에 역할 값이 필요한 경우 **역할 선택** 대화 상자의 목록에서 사용자에 대한 적절한 역할을 선택한 다음, 화면의 아래쪽에 있는 **선택** 단추를 클릭합니다.
1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

## <a name="configure-salesforce-sandbox-sso"></a>Salesforce Sandbox SSO 구성

1. 브라우저에서 새 탭을 열고 Salesforce Sandbox 관리자 계정으로 로그인합니다.

2. 페이지의 오른쪽 위 모서리에 있는 **설정 아이콘** 아래에서 **설정**을 클릭합니다.

    ![Single Sign-on 구성](./media/salesforce-sandbox-tutorial/configure1.png)

3. 왼쪽 탐색 창에서 **설정**으로 스크롤하고 **ID**를 클릭하여 관련 섹션을 확장합니다. 그런 다음 **Single Sign-On 설정**을 클릭합니다.

    ![Single Sign-on 구성](./media/salesforce-sandbox-tutorial/sf-admin-sso.png)

4. **Single Sign-on 설정** 페이지에서 **편집** 단추를 클릭합니다.

    ![Single Sign-on 구성](./media/salesforce-sandbox-tutorial/configure3.png)

5. **SAML 사용**을 선택한 다음 **저장**을 클릭합니다.

    ![Single Sign-on 구성](./media/salesforce-sandbox-tutorial/sf-enable-saml.png)

6. SAML Single Sign-On 설정을 구성하려면 **메타데이터 파일에서 새로 만들기**를 클릭합니다.

    ![Single Sign-on 구성](./media/salesforce-sandbox-tutorial/sf-admin-sso-new.png)

7. **파일 선택**을 클릭하여 Azure Portal에서 다운로드한 메타데이터 XML 파일을 업로드하고 **만들기**를 클릭합니다.

    ![Single Sign-on 구성](./media/salesforce-sandbox-tutorial/xmlchoose.png)

8. **SAML Single Sign-On 설정** 페이지에서 필드에 내용이 자동으로 입력되면 저장을 클릭합니다.

    ![Single Sign-on 구성](./media/salesforce-sandbox-tutorial/salesforcexml.png)

9. **Single Sign On 설정** 페이지에서 **메타데이터 다운로드** 단추를 클릭하여 서비스 공급자 메타데이터 파일을 다운로드합니다. 위에서 설명한 대로 필요한 URL을 구성하는 데 Azure Portal**의 기본 SAML 구성** 섹션에서 이 파일을 사용합니다.

    ![Single Sign-on 구성](./media/salesforce-sandbox-tutorial/configure4.png)

10. **SP** 시작 모드로 애플리케이션을 구성하려는 경우 다음 조건을 충족해야 합니다.

    a. 확인된 도메인이 있어야 합니다.

    b. Salesforce Sandbox에 도메인을 구성하고 사용하도록 설정해야 합니다. 이 작업 단계는 이 자습서의 뒷부분에서 설명합니다.

    다. Azure Portal의 **기본 SAML 구성** 섹션에서 **추가 URL 설정**을 클릭하여 다음 단계를 수행합니다.
  
    ![Salesforce Sandbox 도메인 및 URL Single Sign-On 정보](common/both-signonurl.png)

    **로그온 URL** 텍스트 상자에 다음 패턴으로 값을 입력합니다. `https://<instancename>--Sandbox.<entityid>.my.salesforce.com`

    > [!NOTE]
    > 이 값은 도메인을 사용하도록 설정한 후 Salesforce Sandbox 포털에서 복사해야 합니다.

11. **SAML 서명 인증서** 섹션에서 **페더레이션 메타데이터 XML**을 클릭한 다음, 컴퓨터에 xml 파일을 저장합니다.

    ![인증서 다운로드 링크](common/metadataxml.png)

12. 브라우저에서 새 탭을 열고 Salesforce Sandbox 관리자 계정으로 로그인합니다.

13. 페이지의 오른쪽 위 모서리에 있는 **설정 아이콘** 아래에서 **설정**을 클릭합니다.

    ![Single Sign-on 구성](./media/salesforce-sandbox-tutorial/configure1.png)

14. 왼쪽 탐색 창에서 **설정**으로 스크롤하고 **ID**를 클릭하여 관련 섹션을 확장합니다. 그런 다음 **Single Sign-On 설정**을 클릭합니다.

    ![Single Sign-on 구성](./media/salesforce-sandbox-tutorial/sf-admin-sso.png)

15. **Single Sign-on 설정** 페이지에서 **편집** 단추를 클릭합니다.

    ![Single Sign-on 구성](./media/salesforce-sandbox-tutorial/configure3.png)

16. **SAML 사용**을 선택한 다음 **저장**을 클릭합니다.

    ![Single Sign-on 구성](./media/salesforce-sandbox-tutorial/sf-enable-saml.png)

17. SAML Single Sign-On 설정을 구성하려면 **메타데이터 파일에서 새로 만들기**를 클릭합니다.

    ![Single Sign-on 구성](./media/salesforce-sandbox-tutorial/sf-admin-sso-new.png)

18. **파일 선택**을 클릭하여 메타데이터 XML 파일을 업로드하고 **만들기**를 클릭합니다.

    ![Single Sign-on 구성](./media/salesforce-sandbox-tutorial/xmlchoose.png)

19. **SAML Single Sign-On 설정** 페이지에서 필드가 자동으로 채워지면 구성의 이름(예: *SPSSOWAAD_Test*)을 **이름** 텍스트 상자에 입력하고 저장을 클릭합니다.

    ![Single Sign-on 구성](./media/salesforce-sandbox-tutorial/sf-saml-config.png)

20. Salesforce Sandbox에서 도메인을 사용하도록 설정하려면 다음 단계를 수행합니다.

    > [!NOTE]
    > 도메인을 사용하도록 설정하기 전에 Salesforce Sandbox에서 동일한 도메인을 만들어야 합니다. 자세한 내용은 [도메인 이름 정의](https://help.salesforce.com/HTViewHelpDoc?id=domain_name_define.htm&language=en_US)를 참조하세요. 도메인을 만든 후 올바르게 구성되어 있는지 확인합니다.

21. Salesforce Sandbox의 왼쪽 탐색 패널에서 **회사 설정**을 클릭하여 관련 섹션을 확장하고 **내 도메인**을 클릭합니다.

    ![Single Sign-on 구성](./media/salesforce-sandbox-tutorial/sf-my-domain.png)

22. **인증 구성** 섹션에서 **편집**을 클릭합니다.

    ![Single Sign-on 구성](./media/salesforce-sandbox-tutorial/sf-edit-auth-config.png)

23. **인증 구성** 섹션에서 **인증 서비스**로 Salesforce Sandbox의 SSO 구성 동안 설정한 SAML Single Sign-on 설정의 이름을 선택한 후 **저장**을 클릭합니다.

    ![Single Sign-on 구성](./media/salesforce-sandbox-tutorial/configure2.png)

### <a name="create-salesforce-sandbox-test-user"></a>Salesforce Sandbox 테스트 사용자 만들기

이 섹션에서는 Salesforce Sandbox에서 Britta Simon이라는 사용자를 만듭니다. Salesforce Sandbox는 기본적으로 설정되는 Just-In-Time 프로비전을 지원합니다. 이 섹션에 작업 항목이 없습니다. Salesforce Sandbox에 사용자가 없는 경우 Salesforce Sandbox에 액세스하려고 시도하면 새 사용자가 만들어집니다. Salesforce Sandbox는 자동 사용자 프로비전도 지원합니다. 자동 사용자 프로비전 구성 방법에 대한 자세한 내용은 [여기](salesforce-sandbox-provisioning-tutorial.md)에서 제공합니다.

## <a name="test-sso"></a>SSO 테스트 

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 Salesforce Sandbox 타일을 클릭하면, SSO를 설정한 Salesforce Sandbox에 자동으로 로그인됩니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

- [Azure Active Directory와 SaaS 앱을 통합하는 방법에 대한 자습서 목록](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory를 사용한 애플리케이션 액세스 및 Single Sign-On이란?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory의 조건부 액세스란?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Azure AD로 Salesforce Sandbox 사용해보기](https://aad.portal.azure.com/)

- [Microsoft Cloud App Security의 세션 제어란?](https://docs.microsoft.com/cloud-app-security/protect-salesforce)

- [사용자 프로비저닝 구성](salesforce-sandbox-provisioning-tutorial.md)

- [고급 표시 유형 및 컨트롤을 사용하여 Salesforce Sandbox를 보호하는 방법](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
