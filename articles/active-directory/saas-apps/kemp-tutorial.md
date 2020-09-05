---
title: '자습서: Kemp LoadMaster Azure AD integration과 Azure Active Directory SSO(Single Sign-On) 통합 | Microsoft Docs'
description: Azure Active Directory와 Kemp LoadMaster Azure AD integration 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/28/2020
ms.author: jeedes
ms.openlocfilehash: 1d974badd715d7966f552603a77d77aab95d70f7
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88549971"
---
# <a name="tutorial-azure-active-directory-sso-integration-with-kemp-loadmaster-azure-ad-integration"></a>자습서: Kemp LoadMaster Azure AD integration과 Azure Active Directory SSO(Single Sign-On) 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Kemp LoadMaster Azure AD integration을 통합하는 방법에 대해 알아봅니다. Azure AD와 Kemp LoadMaster Azure AD integration을 통합하면 다음을 수행할 수 있습니다.

* Kemp LoadMaster Azure AD integration에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어합니다.
* 사용자가 자신의 Azure AD 계정으로 Kemp LoadMaster Azure AD integration에 자동으로 로그인되도록 설정합니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory를 사용한 애플리케이션 액세스 및 Single Sign-On이란 무엇인가요?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)를 참조하세요.

## <a name="prerequisites"></a>필수 구성 요소

시작하려면 다음 항목이 필요합니다.

* Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* Kemp LoadMaster Azure AD integration SSO(Single Sign-On)가 설정된 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD SSO를 구성하고 테스트합니다.

* Kemp LoadMaster Azure AD integration에서 **IDP** 시작 SSO를 지원합니다.
* Kemp LoadMaster Azure AD 통합이 구성되면 세션 제어를 적용하여 조직의 중요한 데이터의 반출 및 반입을 실시간으로 보호할 수 있습니다. 세션 제어는 조건부 액세스에서 확장됩니다. [Microsoft Cloud App Security를 사용하여 세션 제어를 적용하는 방법을 알아봅니다](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-kemp-loadmaster-azure-ad-integration-from-the-gallery"></a>갤러리에서 Kemp LoadMaster Azure AD integration 추가

Kemp LoadMaster Azure AD integration의 Azure AD 통합을 구성하려면 갤러리의 Kemp LoadMaster Azure AD integration을 관리형 SaaS 앱 목록에 추가해야 합니다.

1. [Azure Portal](https://portal.azure.com)에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션**으로 이동한 다음, **모든 애플리케이션**을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션**을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에 **Kemp LoadMaster Azure AD integration**을 입력합니다.
1. 결과 패널에서 **Kemp LoadMaster Azure AD integration**을 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.

## <a name="configure-and-test-azure-ad-sso-for-kemp-loadmaster-azure-ad-integration"></a>Kemp LoadMaster Azure AD integration에 대한 Azure AD SSO 구성 및 테스트

**B.Simon**이라는 테스트 사용자를 사용하여 Kemp LoadMaster Azure AD integration에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 Kemp LoadMaster Azure AD integration의 관련 사용자 간에 연결 관계를 설정해야 합니다.

Kemp LoadMaster Azure AD integration에서 Azure AD SSO를 구성하고 테스트하려면 다음 구성 요소를 완료합니다.

1. **[Azure AD SSO 구성](#configure-azure-ad-sso)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
    1. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - B.Simon을 사용하여 Azure AD Single Sign-On을 테스트합니다.
    1. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - B. Simon이 Azure AD Single Sign-On을 사용할 수 있도록 합니다.
1. **[Kemp LoadMaster Azure AD integration SSO 구성](#configure-kemp-loadmaster-azure-ad-integration-sso)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.

1. **[웹 서버 게시](#publishing-web-server)**
    1. **[가상 서비스 만들기](#create-a-virtual-service)**
    1. **[인증서 및 보안](#certificates-and-security)**
    1. **[Kemp LoadMaster Azure AD integration SAML 프로필](#kemp-loadmaster-azure-ad-integration-saml-profile)**
    1. **[변경 내용 확인](#verify-the-changes)**
1. **[Kerberos 기반 인증 구성](#configuring-kerberos-based-authentication)**
    1. **[Kemp LoadMaster Azure AD integration에 대한 Kerberos 위임 계정 만들기](#create-a-kerberos-delegation-account-for-kemp-loadmaster-azure-ad-integration)**
    1. **[Kemp LoadMaster Azure AD integration KCD(Kerberos 위임 계정)](#kemp-loadmaster-azure-ad-integration-kcd-kerberos-delegation-accounts)**
    1. **[Kemp LoadMaster Azure AD integration ESP](#kemp-loadmaster-azure-ad-integration-esp)**
    1. **[Kemp LoadMaster Azure AD integration 테스트 사용자 만들기](#create-kemp-loadmaster-azure-ad-integration-test-user)** - Britta Simon의 Azure AD 표현과 연결된 해당 사용자를 Kemp LoadMaster Azure AD integration에 만듭니다.
1. **[SSO 테스트](#test-sso)** - 구성이 작동하는지 여부를 확인합니다.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)의 **Kemp LoadMaster Azure AD integration** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾은 다음, **Single Sign-On**을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML**을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성**에 대한 편집(연필 모양) 아이콘을 클릭하여 설정을 편집합니다.

   ![기본 SAML 구성 편집](common/edit-urls.png)

1. **기본 SAML 구성** 섹션에서 다음 필드에 대한 값을 입력합니다.

    a. **식별자(엔터티 ID)** 텍스트 상자에서 `https://<KEMP-CUSTOMER-DOMAIN>.com/` URL을 입력합니다.

    b. **회신 URL** 텍스트 상자에서 `https://<KEMP-CUSTOMER-DOMAIN>.com/` URL을 입력합니다.

    > [!NOTE]
    > 이러한 값은 실제 값이 아닙니다. 실제 식별자 및 회신 URL로 해당 값을 업데이트합니다. 이러한 값을 얻으려면 [Kemp LoadMaster Azure AD integration 클라이언트 지원 팀](mailto:support@kemp.ax)에 문의하세요. Azure Portal의 기본 SAML 구성 섹션에 표시된 패턴을 참조할 수도 있습니다.

1. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **인증서(Base64)** 및 **페더레이션 메타데이터 XML**을 찾고, **다운로드**를 선택하여 인증서와 페더레이션 메타데이터 XML 파일을 다운로드하고 컴퓨터에 저장합니다.

    ![인증서 다운로드 링크](./media/kemp-tutorial/certificate-base-64.png)

1. **Kemp LoadMaster Azure AD integration 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

    ![구성 URL 복사](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기

이 섹션에서는 Azure Portal에서 B.Simon이라는 테스트 사용자를 만듭니다.

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory**, **사용자**, **모든 사용자**를 차례로 선택합니다.
1. 화면 위쪽에서 **새 사용자**를 선택합니다.
1. **사용자** 속성에서 다음 단계를 수행합니다.
   1. **이름** 필드에 `B.Simon`을 입력합니다.  
   1. **사용자 이름** 필드에서 username@companydomain.extension을 입력합니다. 예들 들어 `B.Simon@contoso.com`입니다.
   1. **암호 표시** 확인란을 선택한 다음, **암호** 상자에 표시된 값을 적어둡니다.
   1. **만들기**를 클릭합니다.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 B. Simon에게 Kemp LoadMaster Azure AD integration에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션**을 선택한 다음, **모든 애플리케이션**을 선택합니다.
1. 애플리케이션 목록에서 **Kemp LoadMaster Azure AD integration**을 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고 **사용자 및 그룹**을 선택합니다.

   !["사용자 및 그룹" 링크](common/users-groups-blade.png)

1. **사용자 추가**를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![사용자 추가 링크](common/add-assign-user.png)

1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **B.Simon**을 선택한 다음, 화면 아래쪽에서 **선택** 단추를 클릭합니다.
1. SAML 어설션에 역할 값이 필요한 경우 **역할 선택** 대화 상자의 목록에서 사용자에 대한 적절한 역할을 선택한 다음, 화면의 아래쪽에 있는 **선택** 단추를 클릭합니다.
1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

## <a name="configure-kemp-loadmaster-azure-ad-integration-sso"></a>Kemp LoadMaster Azure AD integration SSO 구성

## <a name="publishing-web-server"></a>웹 서버 게시 

### <a name="create-a-virtual-service"></a>가상 서비스 만들기 

1. Kemp LoadMaster Azure AD integration 로드 마스터 웹 UI > 가상 서비스 > 새로 추가로 이동합니다.

1. [새로 추가]를 클릭합니다.

1. 가상 서비스에 대한 매개 변수를 지정합니다.

    ![Kemp LoadMaster Azure AD integration 웹 서버](./media/kemp-tutorial/kemp-1.png)

    a. 가상 주소
    
    b. 포트
    
    다. 프로필 이름(선택 사항)
    
    d. 프로토콜 

1. [실제 서버] 섹션으로 이동합니다.

1. [새로 추가]를 클릭합니다.

1. 실제 서버에 대한 매개 변수를 지정합니다.
    
    ![Kemp LoadMaster Azure AD integration 웹 서버](./media/kemp-tutorial/kemp-2.png)

    a. [원격 주소 허용] 선택
    
    b. 실제 서버 주소 입력
    
    다. 포트
    
    d. 전달 방법
    
    e. 무게
    
    f. 연결 제한
    
    g. [이 실제 서버 추가] 클릭

## <a name="certificates-and-security"></a>인증서 및 보안
 
### <a name="import-certificate-on-kemp-loadmaster-azure-ad-integration"></a>Kemp LoadMaster Azure AD integration 인증서 가져오기 
 
1. Kemp LoadMaster Azure AD integration 웹 포털 > 인증서 및 보안 > SSL 인증서로 이동합니다.

1. 인증서 관리 > 인증서 구성에서

1. [인증서 가져오기]를 클릭합니다.

1. 인증서를 포함하고 있는 파일 이름을 지정합니다. 이 파일은 프라이빗 키도 포함할 수 있습니다. 이 파일에 프라이빗 키가 없으면 프라이빗 키를 포함하고 있는 파일도 지정해야 합니다. 인증서는 .PEM 또는 .PFX(IIS) 형식입니다.

1. [인증서 파일]에서 [파일 선택]을 클릭합니다.

1. [키 파일]\(선택 사항)을 클릭합니다.

1. 저장을 클릭합니다.

### <a name="ssl-acceleration"></a>SSL 가속
 
1. Kemp Load 마스터 웹 UI > 가상 서비스 > 가상 서비스로 이동합니다.

1. [작업] 아래에서 [수정]을 클릭합니다.

1. tcp/x.x.x.:443 (ID:6) - 계층 7에서 작동에 대한 [속성]에서 [SSL 속성]을 클릭합니다.
    
    ![Kemp LoadMaster Azure AD integration 웹 서버](./media/kemp-tutorial/kemp-3.png)
    
    a. [SSL 가속]에서 [사용]을 클릭합니다.
    
    b. [사용 가능한 인증서]에서 가져온 인증서를 선택하고 `>` 기호를 클릭합니다.
    
    다. 지정한 SSL 인증서가 [할당된 인증서]에 표시되면 **인증서 설정**을 클릭합니다.

    > [!NOTE]
    > 반드시 **인증서 설정**을 클릭해야 합니다.

## <a name="kemp-loadmaster-azure-ad-integration-saml-profile"></a>Kemp LoadMaster Azure AD integration SAML 프로필
 
### <a name="import-idp-certificate"></a>IdP 인증서 가져오기

Kemp LoadMaster Azure AD integration 웹 콘솔로 이동합니다. 

1. [인증서 및 인증 기관] 아래에서 [중간 인증서]를 클릭합니다.

    ![Kemp LoadMaster Azure AD integration 웹 서버](./media/kemp-tutorial/kemp-6.png)

    a. [새 중간 인증서 추가]에서 [파일 선택]을 클릭합니다.
    
    b. 이전에 Azure AD Enterprise 애플리케이션에서 다운로드한 인증서 파일로 이동합니다.
    
    다. [열기]를 클릭합니다.
    
    d. [인증서 이름]에 이름을 입력합니다.
    
    e. [인증서 추가]를 클릭합니다.

### <a name="create-authentication-policy"></a>인증 정책 만들기 
 
[가상 서비스]에서 [SSO 관리]로 이동합니다.

   ![Kemp LoadMaster Azure AD integration 웹 서버](./media/kemp-tutorial/kemp-7.png)
   
   a. 이름을 지정한 후 [새 클라이언트 쪽 구성 추가]에서 [추가]를 클릭합니다.

   b. [인증 프로토콜]에서 SAML을 선택합니다.

   다. [IdP 프로비저닝]에서 [메타데이터 파일]을 선택합니다. 

   d. [파일 선택]을 클릭합니다.

   e. 이전에 Azure Portal에서 다운로드한 XML로 이동합니다.

   f. [열기]를 클릭하고 [IdP 메타데이터 파일 가져오기]를 클릭합니다.

   g. [IdP 인증서]에서 중간 인증서를 선택합니다.

   h. Azure Portal에서 만든 ID와 일치해야 하는 SP 엔터티 ID를 설정합니다. 

   i. [SP 엔터티 ID 설정]을 클릭합니다.

### <a name="set-authentication"></a>인증 설정  
 
Kemp LoadMaster Azure AD integration 웹 콘솔에서

1. [가상 서비스]를 클릭합니다.

1. [서비스 보기/수정]을 클릭합니다.

1. [수정]을 클릭하고 [ESP 옵션]으로 이동합니다.
    
    ![Kemp LoadMaster Azure AD integration 웹 서버](./media/kemp-tutorial/kemp-8.png)

    a. [ESP 사용]을 클릭합니다.
    
    b. [클라이언트 인증 모드]에서 SAML을 선택합니다.
    
    다. SSO 도메인에서 이전에 만든 클라이언트 쪽 인증을 선택합니다.
    
    d. [허용되는 가상 호스트]에 호스트 이름을 입력하고 [허용되는 가상 호스트 설정]을 클릭합니다.
    
    e. [허용되는 가상 디렉터리]에 /*를 입력하고(액세스 요구 사항에 따라) [허용되는 디렉터리 설정]을 클릭합니다.

### <a name="verify-the-changes"></a>변경 내용 확인 
 
애플리케이션 URL로 이동 

이전에 인증되지 않은 액세스 대신 테넌트 로그인 페이지가 표시됩니다. 

![Kemp LoadMaster Azure AD integration 웹 서버](./media/kemp-tutorial/kemp-9.png)

## <a name="configuring-kerberos-based-authentication"></a>Kerberos 기반 인증 구성 
 
### <a name="create-a-kerberos-delegation-account-for-kemp-loadmaster-azure-ad-integration"></a>Kemp LoadMaster Azure AD integration에 대한 Kerberos 위임 계정 만들기 

1. 사용자 계정(이 예제에서는 AppDelegation)을 만듭니다.
    
    ![Kemp LoadMaster Azure AD integration 웹 서버](./media/kemp-tutorial/kemp-10.png)


    a. [특성 편집기] 탭을 선택합니다.

    b. servicePrincipalName으로 이동합니다. 

    다. servicePrincipalName을 선택하고 [편집]을 클릭합니다.

    d. 필드를 추가할 값에 http/kcduser를 입력하고 [추가]를 클릭합니다. 

    e. [적용]을 클릭하고 [확인]을 클릭합니다. 창을 닫았다가 다시 엽니다(새 [위임] 탭을 표시하기 위해). 

1. 사용자 속성 창을 다시 열면 [위임] 탭을 사용할 수 있게 됩니다. 

1. [위임] 탭을 선택합니다.

    ![Kemp LoadMaster Azure AD integration 웹 서버](./media/kemp-tutorial/kemp-11.png)

    a. [지정한 서비스에 대한 위임용으로만 이 사용자 트러스트]를 선택합니다.

    b. [모든 인증 프로토콜 사용]을 선택합니다.

    다. 실제 서버를 추가하고 http를 서비스로 추가합니다. 
    
    d. [확장] 확인란을 선택합니다. 

    e. 호스트 이름과 FQDN을 모두 사용하는 서버가 전부 표시됩니다.
    
    f. [확인]을 클릭합니다.

> [!NOTE]
> 애플리케이션/웹 사이트에서 해당하는 SPN을 설정합니다. 애플리케이션 풀 ID가 설정된 경우 애플리케이션에 액세스하려면 다음을 수행합니다. FQDN 이름을 사용하여 IIS 애플리케이션에 액세스하려면 실제 서버 명령 프롬프트로 이동한 다음, 필수 매개 변수를 사용하여 SetSpn을 입력합니다. 예: Setspn –S HTTP/sescoindc.sunehes.co.in suneshes\kdcuser 

### <a name="kemp-loadmaster-azure-ad-integration-kcd-kerberos-delegation-accounts"></a>Kemp LoadMaster Azure AD integration KCD(Kerberos 위임 계정) 

Kemp LoadMaster Azure AD integration 웹 콘솔 > 가상 서비스 > SSO 관리로 이동합니다.

![Kemp LoadMaster Azure AD integration 웹 서버](./media/kemp-tutorial/kemp-12.png)

a. 서버 쪽 Single Sign On 구성으로 이동합니다.

b. [새 서버 쪽 구성 추가]에 이름을 입력하고 [추가]를 클릭합니다.

다. [인증 프로토콜]에서 [Kerberos 제한된 위임]을 선택합니다.

d. [Kerberos 영역]에 도메인 이름을 입력합니다.

e. [Kerberos 영역 설정]을 클릭합니다.

f. Kerberos 키 배포 센터에서 도메인 컨트롤러 IP 주소를 입력합니다.

g. [Kerberos KDC 설정]을 클릭합니다.

h. [Kerberos 신뢰할 수 있는 사용자 이름]에 KCD 사용자 이름을 입력합니다.

i. [KDC 신뢰할 수 있는 사용자 이름 설정]을 클릭합니다.

j. [Kerberos 신뢰할 수 있는 사용자 암호]에 암호를 입력합니다.

k. [KCD 신뢰할 수 있는 사용자 암호 설정]을 클릭합니다.

### <a name="kemp-loadmaster-azure-ad-integration-esp"></a>Kemp LoadMaster Azure AD integration ESP 

가상 서비스 > 서비스 보기/수정으로 이동합니다.

![Kemp LoadMaster Azure AD integration 웹 서버](./media/kemp-tutorial/kemp-13.png)

a. [가상 서비스의 별칭]에서 [수정]을 클릭합니다.
    
b. [ESP 옵션]을 클릭합니다.
    
다. [서버 인증 모드]에서 KCD를 선택합니다.
        
d. [서버 쪽 구성]에서 이전에 만든 서버 쪽 프로필을 선택합니다.

### <a name="create-kemp-loadmaster-azure-ad-integration-test-user"></a>Kemp LoadMaster Azure AD integration 테스트 사용자 만들기

이 섹션에서는 Kemp LoadMaster Azure AD integration에서 Britta Simon이라는 사용자를 만듭니다. Kemp LoadMaster Azure AD integration 플랫폼에서 사용자를 추가하려면 [Kemp LoadMaster Azure AD integration 지원 팀](mailto:support@kemp.ax)에 문의하세요. Single Sign-On을 사용하려면 먼저 사용자를 만들고 활성화해야 합니다.

## <a name="test-sso"></a>SSO 테스트 

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 Kemp LoadMaster Azure AD integration 타일을 클릭하면 SSO를 설정한 Kemp LoadMaster Azure AD integration에 자동으로 로그인됩니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

- [Azure Active Directory와 SaaS 앱을 통합하는 방법에 대한 자습서 목록](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory를 사용한 애플리케이션 액세스 및 Single Sign-On이란?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory의 조건부 액세스란?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Azure AD로 Kemp LoadMaster Azure AD integration 사용해 보기](https://aad.portal.azure.com/)

- [Microsoft Cloud App Security의 세션 제어란?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [고급 표시 유형 및 컨트롤을 사용하여 Kemp LoadMaster Azure AD integration을 보호하는 방법](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
