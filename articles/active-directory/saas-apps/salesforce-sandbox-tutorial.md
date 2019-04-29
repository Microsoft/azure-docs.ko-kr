---
title: '자습서: Salesforce Sandbox와 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory와 Salesforce Sandbox 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: ee54c39e-ce20-42a4-8531-da7b5f40f57c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/10/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6794d7eaccb488bb345227161f0bca02f14bc518
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62104968"
---
# <a name="tutorial-azure-active-directory-integration-with-salesforce-sandbox"></a>자습서: Salesforce Sandbox와 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Salesforce Sandbox를 통합하는 방법에 대해 알아봅니다.

샌드박스는 Salesforce 프로덕션 조직에서 데이터 및 애플리케이션을 손상시키지 않고 개발, 테스트 및 훈련과 같은 다양한 목적으로 별도의 환경에서 조직의 여러 복사본을 만들 수 있는 기능을 제공합니다.
자세한 내용은 [샌드박스 개요](https://help.salesforce.com/articleView?id=create_test_instance.htm&language=en_us&type=5)를 참조하세요.

Salesforce Sandbox를 Azure AD와 통합하면 다음과 같은 이점이 있습니다.

- Salesforce Sandbox에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다.
- 사용자가 해당 Azure AD 계정으로 Salesforce Sandbox에 자동으로 로그인(Single Sign-On)하도록 설정할 수 있습니다.
- 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory의 애플리케이션 액세스 및 Single Sign-On이란 무엇인가요?](../manage-apps/what-is-single-sign-on.md)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

Salesforce Sandbox와 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

- Azure AD 구독
- Salesforce Sandbox Single Sign-On이 설정된 구독

> [!NOTE]
> 이 자습서의 단계를 테스트하기 위해 프로덕션 환경을 사용하는 것은 바람직하지 않습니다.

이 자습서의 단계를 테스트하려면 다음 권장 사항을 준수해야 합니다.

- 꼭 필요한 경우가 아니면 프로덕션 환경을 사용하지 마세요.
- Azure AD 평가판 환경이 없으면 [1개월 평가판을 얻을](https://azure.microsoft.com/pricing/free-trial/) 수 있습니다.

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 테스트 합니다. 이 자습서에 설명된 시나리오는 다음 두 가지 주요 구성 요소로 이루어져 있습니다.

1. 갤러리에서 Salesforce Sandbox를 추가합니다.
2. Azure AD Single Sign-on 구성 및 테스트

## <a name="adding-salesforce-sandbox-from-the-gallery"></a>갤러리에서 Salesforce Sandbox를 추가합니다.

Salesforce Sandbox의 Azure AD 통합을 구성하려면 갤러리의 Salesforce Sandbox를 관리되는 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 Salesforce Sandbox를 추가하려면 다음 단계를 수행합니다.**

1. **[Azure Portal](https://portal.azure.com)** 의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다. 

    ![Azure Active Directory 단추][1]

2. **엔터프라이즈 애플리케이션**으로 이동합니다. 그런 후 **모든 애플리케이션**으로 이동합니다.

    ![엔터프라이즈 애플리케이션 블레이드][2]

3. 새 애플리케이션을 추가하려면 대화 상자 맨 위 있는 **새 애플리케이션** 단추를 클릭합니다.

    ![새 애플리케이션 단추][3]

4. 검색 상자에 **Salesforce Sandbox**를 입력하고 결과 패널에서 **Salesforce Sandbox**를 선택한 다음, **추가** 단추를 클릭하여 애플리케이션을 추가합니다.

    ![결과 목록의 Salesforce Sandbox](./media/salesforce-sandbox-tutorial/tutorial_salesforcesandbox_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성 및 테스트

이 섹션에서는 “Britta Simon”이라는 테스트 사용자를 기반으로 Salesforce Sandbox에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

Single Sign-On이 작동하려면 Azure AD에서 Azure AD 사용자에 해당하는 Salesforce Sandbox 사용자가 누구인지 알고 있어야 합니다. 즉, Azure AD 사용자와 Salesforce Sandbox의 관련 사용자 간에 연결 관계가 형성되어야 합니다.

Salesforce Sandbox에서 Azure AD의 **사용자 이름** 값을 **Username** 값으로 할당하여 연결 관계를 설정합니다.

Salesforce Sandbox에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Azure AD Single Sign-On 구성](#configure-azure-ad-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On을 테스트하는 데 사용합니다.
3. **[Salesforce Sandbox 테스트 사용자 만들기](#create-a-salesforce-sandbox-test-user)** - Azure AD의 Britta Simon 사용자에 연결된 Salesforce Sandbox 사용자를 만듭니다.
4. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
5. **[Single Sign-On 테스트](#test-single-sign-on)** - 구성이 작동하는지 여부를 확인합니다.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정하고 Salesforce Sandbox 애플리케이션에서 Single Sign-On을 구성합니다.

**Salesforce Sandbox에서 Azure AD Single Sign-On을 구성하려면 다음 단계를 수행합니다.**

1. Azure Portal의 **Salesforce Sandbox** 애플리케이션 통합 페이지에서 **Single Sign-On**을 클릭합니다.

    ![Single Sign-On 구성 링크][4]

2. 화면 위쪽에서 **Single Sign-On 모드 변경**을 클릭하여 **SAML** 모드를 선택합니다.

      ![Single Sign-On 구성 링크](./media/salesforce-sandbox-tutorial/tutorial_general_300.png)

3. **Single Sign-On 방법 선택** 대화 상자에서 **SAML** 모드에 대해 **선택**을 클릭하여 Single Sign-On을 사용하도록 설정합니다.

    ![Single Sign-On 구성 링크](./media/salesforce-sandbox-tutorial/tutorial_general_301.png)

4. **SAML로 Single Sign-On 설정** 페이지에서 **편집** 단추를 클릭하여 **기본 SAML 구성** 대화 상자를 엽니다.
   
    ![Single Sign-On 구성 링크](./media/salesforce-sandbox-tutorial/tutorial_general_302.png)

5. **IDP** 시작 모드에서 응용 프로그램을 구성하려면 **기본 SAML 구성** 섹션에서 다음 단계를 수행합니다.

    a. **메타데이터 파일 업로드**를 클릭합니다.

    ![메타데이터 파일 업로드](./media/salesforce-sandbox-tutorial/upload_metadata.png)

    b. **폴더 로고**를 클릭하여 메타데이터 파일을 선택하고 **업로드**를 클릭합니다.

    ![메타데이터 파일 선택](./media/salesforce-sandbox-tutorial/browse_upload_metadata.png)

    > [!NOTE]
    > 자습서 뒷부분에서 설명하는 Salesforce Sandbox 관리자 포털에서 서비스 공급자 메타데이터 파일을 가져옵니다.

    다. 메타데이터 파일을 성공적으로 업로드한 후 **회신 URL** 값은 **회신 URL** 텍스트 상자에 자동으로 채워집니다.

    ![Salesforce Sandbox 도메인 및 URL Single Sign-On 정보](./media/salesforce-sandbox-tutorial/tutorial_salesforcesandbox_url1.png)

6. **SAML 서명 인증서** 섹션에서 **다운로드**를 클릭하여 **페더레이션 메타데이터 XML**을 다운로드한 다음, 컴퓨터에 xml 파일을 저장합니다.

    ![인증서 다운로드 링크](./media/salesforce-sandbox-tutorial/tutorial_salesforcesandbox_certificate.png)

7. 브라우저에서 새 탭을 열고 Salesforce Sandbox Administrator 계정으로 로그인합니다.

8. 페이지의 오른쪽 위 모서리에 있는 **설정 아이콘** 아래에서 **설정**을 클릭합니다.

    ![Configure Single Sign-On](./media/salesforce-sandbox-tutorial/configure1.png)

9. 왼쪽 탐색 창에서 **설정**으로 스크롤하고 **ID**를 클릭하여 관련 섹션을 확장합니다. 그런 다음 **Single Sign-On 설정**을 클릭합니다.

    ![Configure Single Sign-On](./media/salesforce-sandbox-tutorial/sf-admin-sso.png)

10. **Single Sign-on 설정** 페이지에서 **편집** 단추를 클릭합니다.

    ![Configure Single Sign-On](./media/salesforce-sandbox-tutorial/configure3.png)

11. **SAML 사용**을 선택한 다음 **저장**을 클릭합니다.

    ![Configure Single Sign-On](./media/salesforce-sandbox-tutorial/sf-enable-saml.png)

12. SAML Single Sign-On 설정을 구성하려면 **메타데이터 파일에서 새로 만들기**를 클릭합니다.

    ![Configure Single Sign-On](./media/salesforce-sandbox-tutorial/sf-admin-sso-new.png)

13. **파일 선택**을 클릭하여 Azure Portal에서 다운로드한 메타데이터 XML 파일을 업로드하고 **만들기**를 클릭합니다.

    ![Configure Single Sign-On](./media/salesforce-sandbox-tutorial/xmlchoose.png)

14. **SAML Single Sign-On 설정** 페이지에서 필드에 내용이 자동으로 입력되면 저장을 클릭합니다.

    ![Configure Single Sign-On](./media/salesforce-sandbox-tutorial/salesforcexml.png)

15. **Single Sign On 설정** 페이지에서 **메타데이터 다운로드** 단추를 클릭하여 서비스 공급자 메타데이터 파일을 다운로드합니다. 위에서 설명한 대로 필요한 URL을 구성하는 데 Azure Portal**의 기본 SAML 구성** 섹션에서 이 파일을 사용합니다.

    ![Configure Single Sign-On](./media/salesforce-sandbox-tutorial/configure4.png)

16. **SP** 시작 모드로 애플리케이션을 구성하려는 경우 다음 조건을 충족해야 합니다.

    a. 확인된 도메인이 있어야 합니다.

    b. Salesforce Sandbox에 도메인을 구성하고 사용하도록 설정해야 합니다. 이 작업 단계는 이 자습서의 뒷부분에서 설명합니다.

    다. Azure Portal의 **기본 SAML 구성** 섹션에서 **추가 URL 설정**을 클릭하여 다음 단계를 수행합니다.
  
    ![Salesforce Sandbox 도메인 및 URL Single Sign-On 정보](./media/salesforce-sandbox-tutorial/tutorial_salesforcesandbox_url.png)

    **로그온 URL** 텍스트 상자에 다음 패턴으로 값을 입력합니다. `https://<instancename>--Sandbox.<entityid>.my.salesforce.com` 

    > [!NOTE]
    > 이 값은 도메인을 사용하도록 설정한 후 Salesforce Sandbox 포털에서 복사해야 합니다.

17. **SAML 서명 인증서** 섹션에서 **페더레이션 메타데이터 XML**을 클릭한 다음, 컴퓨터에 xml 파일을 저장합니다.

    ![인증서 다운로드 링크](./media/salesforce-sandbox-tutorial/tutorial_salesforcesandbox_certificate.png)

18. 브라우저에서 새 탭을 열고 Salesforce Sandbox Administrator 계정으로 로그인합니다.

19. 페이지의 오른쪽 위 모서리에 있는 **설정 아이콘** 아래에서 **설정**을 클릭합니다.

    ![Configure Single Sign-On](./media/salesforce-sandbox-tutorial/configure1.png)

20. 왼쪽 탐색 창에서 **설정**으로 스크롤하고 **ID**를 클릭하여 관련 섹션을 확장합니다. 그런 다음 **Single Sign-On 설정**을 클릭합니다.

    ![Configure Single Sign-On](./media/salesforce-sandbox-tutorial/sf-admin-sso.png)

21. **Single Sign-on 설정** 페이지에서 **편집** 단추를 클릭합니다.

    ![Configure Single Sign-On](./media/salesforce-sandbox-tutorial/configure3.png)

22. **SAML 사용**을 선택한 다음 **저장**을 클릭합니다.

    ![Configure Single Sign-On](./media/salesforce-sandbox-tutorial/sf-enable-saml.png)

23. SAML Single Sign-On 설정을 구성하려면 **메타데이터 파일에서 새로 만들기**를 클릭합니다.

    ![Configure Single Sign-On](./media/salesforce-sandbox-tutorial/sf-admin-sso-new.png)

24. **파일 선택**을 클릭하여 메타데이터 XML 파일을 업로드하고 **만들기**를 클릭합니다.

    ![Configure Single Sign-On](./media/salesforce-sandbox-tutorial/xmlchoose.png)

25. **SAML Single Sign-On 설정** 페이지에서 필드가 자동으로 채워지면 구성의 이름(예: *SPSSOWAAD_Test*)을 **이름** 텍스트 상자에 입력하고 [저장]을 클릭합니다.

    ![Configure Single Sign-On](./media/salesforce-sandbox-tutorial/sf-saml-config.png)

26. Salesforce Sandbox에서 도메인을 사용하도록 설정하려면 다음 단계를 수행합니다.

    > [!NOTE]
    > 도메인을 사용하도록 설정하기 전에 Salesforce Sandbox에서 동일한 도메인을 만들어야 합니다. 자세한 내용은 [도메인 이름 정의](https://help.salesforce.com/HTViewHelpDoc?id=domain_name_define.htm&language=en_US)를 참조하세요. 도메인을 만든 후 올바르게 구성되어 있는지 확인합니다.

    * Salesforce Sandbox의 왼쪽 탐색 패널에서 **회사 설정**을 클릭하여 관련 섹션을 확장하고 **내 도메인**을 클릭합니다.

         ![Configure Single Sign-On](./media/salesforce-sandbox-tutorial/sf-my-domain.png)

    * **인증 구성** 섹션에서 **편집**을 클릭합니다.

        ![Configure Single Sign-On](./media/salesforce-sandbox-tutorial/sf-edit-auth-config.png)

    * **인증 구성** 섹션에서 **인증 서비스**로 Salesforce Sandbox의 SSO 구성 동안 설정한 SAML Single Sign-on 설정의 이름을 선택한 후 **저장**을 클릭합니다.

        ![Configure Single Sign-On](./media/salesforce-sandbox-tutorial/configure2.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기

이 섹션의 목적은 Azure Portal에서 Britta Simon이라는 테스트 사용자를 만드는 것입니다.

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory**, **사용자**를 차례로 선택하고 **모든 사용자**를 선택합니다.

    ![Azure AD 사용자 만들기][100]

2. 화면 위쪽에서 **새 사용자**를 선택합니다.

    ![Azure AD 테스트 사용자 만들기](./media/salesforce-sandbox-tutorial/create_aaduser_01.png) 

3. 사용자 속성에서 다음 단계를 수행합니다.

    ![Azure AD 테스트 사용자 만들기](./media/salesforce-sandbox-tutorial/create_aaduser_02.png)

    a. **이름** 필드에 **BrittaSimon**을 입력합니다.
  
    b. **사용자 이름** 필드에 **brittasimon\@yourcompanydomain.extension**을 입력합니다.  
    예를 들어 BrittaSimon@contoso.com

    다. **속성**을 선택하고 **암호 표시** 확인란을 선택한 다음, 암호 상자에 표시된 값을 적어 둡니다.

    d. **만들기**를 선택합니다.

### <a name="create-a-salesforce-sandbox-test-user"></a>Salesforce Sandbox 테스트 사용자 만들기

이 섹션에서는 Salesforce Sandbox에서 Britta Simon이라는 사용자를 만듭니다. Salesforce Sandbox는 기본적으로 설정되는 Just-In-Time 프로비전을 지원합니다. 이 섹션에 작업 항목이 없습니다. Salesforce Sandbox에 사용자가 없는 경우 Salesforce Sandbox에 액세스하려고 시도하면 새 사용자가 만들어집니다. Salesforce Sandbox는 자동 사용자 프로비전도 지원합니다. 자동 사용자 프로비전 구성 방법에 대한 자세한 내용은 [여기](salesforce-sandbox-provisioning-tutorial.md)에서 제공합니다.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 Britta Simon에게 Salesforce Sandbox에 대한 액세스 권한을 부여합니다.

![사용자 역할 할당][200]

**Salesforce Sandbox에 사용자를 할당하려면 다음 단계를 수행합니다.**

1. Azure Portal에서 애플리케이션 보기를 연 다음 디렉터리 보기로 이동하고 **엔터프라이즈 애플리케이션**으로 이동한 후 **모든 애플리케이션**을 클릭합니다.

    ![사용자 할당][201] 

2. 애플리케이션 목록에서 **Salesforce Sandbox**를 선택합니다.

    ![애플리케이션 목록의 Salesforce Sandbox 링크](./media/salesforce-sandbox-tutorial/tutorial_salesforcesandbox_app.png)  

3. 왼쪽 메뉴에서 **사용자 및 그룹**을 클릭합니다.

    !["사용자 및 그룹" 링크][202]

4. **사용자 추가** 단추를 클릭합니다. 그런 후 **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![할당 추가 창][203]

5. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon**을 선택합니다.

6. **사용자 및 그룹** 대화 상자에서 **선택** 단추를 클릭합니다.

7. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.
    
### <a name="test-single-sign-on"></a>Single Sign-On 테스트

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 Salesforce Sandbox 타일을 클릭하면 Salesforce Sandbox 애플리케이션에 자동으로 로그온됩니다.
액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](../user-help/active-directory-saas-access-panel-introduction.md)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

* [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](tutorial-list.md)
* [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](../manage-apps/what-is-single-sign-on.md)
* [사용자 프로비저닝 구성](salesforce-sandbox-provisioning-tutorial.md)

<!--Image references-->

[1]: ./media/salesforce-sandbox-tutorial/tutorial_general_01.png
[2]: ./media/salesforce-sandbox-tutorial/tutorial_general_02.png
[3]: ./media/salesforce-sandbox-tutorial/tutorial_general_03.png
[4]: ./media/salesforce-sandbox-tutorial/tutorial_general_04.png

[100]: ./media/salesforce-sandbox-tutorial/tutorial_general_100.png

[200]: ./media/salesforce-sandbox-tutorial/tutorial_general_200.png
[201]: ./media/salesforce-sandbox-tutorial/tutorial_general_201.png
[202]: ./media/salesforce-sandbox-tutorial/tutorial_general_202.png
[203]: ./media/salesforce-sandbox-tutorial/tutorial_general_203.png
