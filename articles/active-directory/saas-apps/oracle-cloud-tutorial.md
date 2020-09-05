---
title: '자습서: Oracle Cloud Infrastructure Console과 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory와 Oracle Cloud Infrastructure Console 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
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
ms.openlocfilehash: d4a478c402346e33a4f27ff2a363de75285b435b
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88543842"
---
# <a name="tutorial-integrate-oracle-cloud-infrastructure-console-with-azure-active-directory"></a>자습서: Azure Active Directory와 Oracle Cloud Infrastructure Console 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Oracle Cloud Infrastructure Console을 통합하는 방법에 대해 알아봅니다. Azure AD와 Oracle Cloud Infrastructure Console을 통합하면 다음을 수행할 수 있습니다.

* Oracle Cloud Infrastructure Console에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다.
* 사용자가 해당 Azure AD 계정으로 Oracle Cloud Infrastructure Console에 자동으로 로그인되도록 설정할 수 있습니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory를 사용한 애플리케이션 액세스 및 Single Sign-On이란 무엇인가요?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)를 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

시작하려면 다음 항목이 필요합니다.

* Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* Oracle Cloud Infrastructure Console SSO(Single Sign-On)가 설정된 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD SSO를 구성하고 테스트합니다.

* Oracle Cloud Infrastructure Console은 **SP** 시작 SSO를 지원합니다.
* Oracle Cloud Infrastructure Console을 구성한 후에는 세션 제어를 적용하여 조직의 중요한 데이터의 반출 및 침입을 실시간으로 보호할 수 있습니다. 세션 제어는 조건부 액세스에서 확장됩니다. [Microsoft Cloud App Security를 사용하여 세션 제어를 적용하는 방법 알아보기](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-oracle-cloud-infrastructure-console-from-the-gallery"></a>갤러리에서 Oracle Cloud Infrastructure Console 추가

Azure AD에 Oracle Cloud Infrastructure Console을 통합하도록 구성하려면 갤러리의 Oracle Cloud Infrastructure Console을 관리형 SaaS 앱 목록에 추가해야 합니다.

1. [Azure Portal](https://portal.azure.com)에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션**으로 이동한 다음, **모든 애플리케이션**을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션**을 선택합니다.
1. **갤러리에서 추가** 섹션에서 검색 상자에 **Oracle Cloud Infrastructure Console**을 입력합니다.
1. 결과 패널에서 **Oracle Cloud Infrastructure Console**을 선택한 후 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성 및 테스트

**B. Simon**이라는 테스트 사용자를 사용하여 Oracle Cloud Infrastructure Console에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 Oracle Cloud Infrastructure Console의 관련 사용자 간에 연결 관계를 설정해야 합니다.

Oracle Cloud Infrastructure Console에서 Azure AD SSO를 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. 사용자가 이 기능을 사용할 수 있도록 **[Azure AD SSO를 구성](#configure-azure-ad-sso)** 합니다.
    1. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - B. Simon을 사용하여 Azure AD Single Sign-On을 테스트합니다.
    1. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - B. Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
1. **[Oracle Cloud Infrastructure Console을 구성](#configure-oracle-cloud-infrastructure-console)** 하여 애플리케이션 쪽에서 SSO 설정을 구성합니다.
    1. **[Oracle Cloud Infrastructure Console 테스트 사용자를 생성](#create-oracle-cloud-infrastructure-console-test-user)** 하여 Britta Simon의 Azure AD 표현과 연결된 해당 사용자를 Oracle Cloud Infrastructure Console에 만듭니다.
1. **[SSO를 테스트](#test-sso)** 하여 구성이 작동하는지 여부를 확인합니다.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)의 **Oracle Cloud Infrastructure Console** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾은 다음, **Single Sign-On**을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML**을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성**에 대한 편집(연필 모양) 아이콘을 클릭하여 설정을 편집합니다.

   ![기본 SAML 구성 편집](common/edit-urls.png)

1. **기본 SAML 구성** 페이지에서 다음 필드에 대한 값을 입력합니다.

   > [!NOTE]
   > 본 자습서의 **Oracle Cloud Infrastructure Console Single Sign-On 구성** 섹션에서 서비스 공급자 메타데이터 파일을 받을 수 있습니다.
    
   1. **메타데이터 파일 업로드**를 클릭합니다.

   1. **폴더 로고**를 클릭하여 메타데이터 파일을 선택하고 **업로드**를 클릭합니다.

   1. 메타데이터 파일이 성공적으로 업로드되면 **식별자** 및 **회신 URL** 값이 **기본 SAML 구성** 섹션 텍스트 상자에 자동으로 채워집니다.
    
      > [!NOTE]
      > **식별자** 및 **회신 URL** 값이 자동으로 입력되지 않으면 요구 사항에 따라 수동으로 값을 입력합니다.

      **로그인 URL** 텍스트 상자에서 `https://console.<REGIONNAME>.oraclecloud.com/` 패턴을 사용하여 URL을 입력합니다.

      > [!NOTE]
      > 이 값은 실제 값이 아닙니다. 이 값을 실제 로그온 URL로 업데이트합니다. 이러한 값을 확인하려면[Oracle Cloud Infrastructure Console 클라이언트 지원 팀](https://www.oracle.com/support/advanced-customer-support/products/cloud.html)에 문의하세요. Azure Portal의 **기본 SAML 구성** 섹션에 표시된 패턴을 참조할 수도 있습니다.

1. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **페더레이션 메타데이터 XML**을 찾고, **다운로드**를 선택하여 인증서를 컴퓨터에 다운로드 및 저장합니다.

   ![인증서 다운로드 링크](common/metadataxml.png)

1. Oracle Cloud Infrastructure Console 애플리케이션은 특정 형식의 SAML 어설션을 예상하며, 따라서 SAML 토큰 특성 구성에 사용자 지정 특성 매핑을 추가해야 합니다. 다음 스크린샷에서는 기본 특성의 목록을 보여 줍니다. **편집** 아이콘을 클릭하여 사용자 특성 대화 상자를 엽니다.

   ![이미지](common/edit-attribute.png)

1. 위에서 언급한 특성 외에도, Oracle Cloud Infrastructure Console 애플리케이션에는 SAML 응답에 다시 전달되어야 하는 몇 가지 특성이 추가로 필요합니다. **그룹 클레임(미리 보기)** 대화 상자의 **사용자 특성 및 클레임** 섹션에서 다음 단계를 수행합니다.

   1. **이름 식별자 값** 옆에 있는 **펜**을 클릭합니다.

   1. **이름 식별자 형식 선택**에 **지속**을 선택합니다.
 
   1. **저장**을 클릭합니다.

      ![이미지](./media/oracle-cloud-tutorial/config07.png)
    
      ![이미지](./media/oracle-cloud-tutorial/config11.png)

   1. **클레임에서 반환되는 그룹** 옆에 있는 **펜**을 클릭합니다.

   1. 라디오 단추 목록에서 **보안 그룹**을 선택합니다.

   1. **그룹 ID**의 **원본 특성**을 선택합니다.

   1. **그룹 클레임 이름 사용자 지정**을 선택합니다.

   1. **이름** 텍스트 상자에 **groupName**을 입력합니다.

   1. **네임스페이스(선택 사항)** 텍스트 상자에 `https://auth.oraclecloud.com/saml/claims`를 입력합니다.

   1. **저장**을 클릭합니다.

      ![이미지](./media/oracle-cloud-tutorial/config08.png)

1. **Oracle Cloud Infrastructure Console 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

   ![구성 URL 복사](common/copy-configuration-urls.png)



### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기

이 섹션에서는 Azure Portal에서 B. Simon이라는 테스트 사용자를 만듭니다.

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory**, **사용자**, **모든 사용자**를 차례로 선택합니다.
1. 화면 위쪽에서 **새 사용자**를 선택합니다.
1. **사용자** 속성에서 다음 단계를 수행합니다.
   1. **이름** 필드에 `B. Simon`을 입력합니다.  
   1. **사용자 이름** 필드에서 username@companydomain.extension을 입력합니다. `B. Simon@contoso.com`)을 입력합니다.
   1. **암호 표시** 확인란을 선택한 다음, **암호** 상자에 표시된 값을 적어둡니다.
   1. **만들기**를 클릭합니다.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 B. Simon에게 Oracle Cloud Infrastructure Console에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션**을 선택한 다음, **모든 애플리케이션**을 선택합니다.
1. 애플리케이션 목록에서 **Oracle Cloud Infrastructure Console**을 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고 **사용자 및 그룹**을 선택합니다.

   !["사용자 및 그룹" 링크](common/users-groups-blade.png)

1. **사용자 추가**를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

   ![사용자 추가 링크](common/add-assign-user.png)

1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **B. Simon**을 선택한 다음, 화면 아래쪽에서 **선택** 단추를 클릭합니다.
1. SAML 어설션에 역할 값이 필요한 경우 **역할 선택** 대화 상자의 목록에서 사용자에 대한 적절한 역할을 선택한 다음, 화면의 아래쪽에 있는 **선택** 단추를 클릭합니다.
1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

## <a name="configure-oracle-cloud-infrastructure-console"></a>Oracle Cloud Infrastructure Console 구성

1. 다른 웹 브라우저 창에서 Oracle Cloud Infrastructure Console에 관리자 권한으로 로그인합니다.

1. 메뉴의 왼쪽 부분을 클릭하고 **ID**를 클릭한 후 **페더레이션**으로 이동합니다.

   ![구성](./media/oracle-cloud-tutorial/config01.png)

1. **이 문서 다운로드** 링크를 클릭하여 **서비스 공급자 메타데이터 파일**을 저장하고 Azure Portal의 **기본 SAML 구성** 섹션에 업로드한 후 **ID 공급자 추가**를 클릭합니다.

   ![구성](./media/oracle-cloud-tutorial/config02.png)

1. **ID 공급자 추가** 팝업에서 다음 단계를 수행합니다.

   ![구성](./media/oracle-cloud-tutorial/config03.png)

   1. **이름** 텍스트 상자에 이름을 입력합니다.

   1. **설명** 텍스트 상자에 설명을 입력합니다.

   1. **유형**에 **Microsoft AD FS(Active Directory Federation Services) 또는 SAML 2.0 호환 ID 공급자**를 선택합니다.

   1. **찾아보기**를 클릭하여 Azure Portal에서 다운로드한 페더레이션 메타데이터 XML을 업로드합니다.

   1. **계속**을 클릭하고 **ID 공급자 편집** 섹션에서 다음 단계를 수행합니다.

      ![구성](./media/oracle-cloud-tutorial/config09.png)

   1. **ID 공급자 그룹**을 [사용자 지정 그룹]으로 선택해야 합니다. 그룹 ID는 Azure Active Directory의 그룹 GUID여야 합니다. 그룹을 **OCI 그룹** 필드의 해당 그룹과 매핑해야 합니다.

   1. Azure Portal의 설정 및 조직 요구 사항에 따라 여러 그룹을 매핑할 수 있습니다. **+ 매핑 추가**를 클릭하여 그룹을 필요한 만큼 추가합니다.

   1. **제출**을 클릭합니다.
   
### <a name="create-oracle-cloud-infrastructure-console-test-user"></a>Oracle Cloud Infrastructure Console 테스트 사용자 만들기

 Oracle Cloud Infrastructure Console은 Just-In-Time 프로비저닝을 지원하며 기본적으로 사용하도록 설정합니다. 이 섹션에 작업 항목이 없습니다. 액세스 시도 중에는 새로운 사용자가 생성되지 않으며, 사용자를 만들 필요도 없습니다.

### <a name="test-sso"></a>SSO 테스트

액세스 패널에서 Oracle Cloud Infrastructure Console 타일을 선택하면 Oracle Cloud Infrastructure Console 로그인 페이지로 리디렉션됩니다. 드롭다운 메뉴에서 **ID 공급자**를 선택하고 아래에 표시된 **계속**을 클릭하여 로그인합니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)를 참조하세요.

![구성](./media/oracle-cloud-tutorial/config10.png)

## <a name="additional-resources"></a>추가 리소스

- [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory의 조건부 액세스란?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [고급 표시 유형 및 컨트롤을 사용하여 Oracle Cloud Infrastructure Console을 보호하는 방법](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
