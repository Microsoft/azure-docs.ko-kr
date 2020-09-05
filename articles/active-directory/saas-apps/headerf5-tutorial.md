---
title: '자습서: F5와 Azure Active Directory SSO(Single Sign-On) 통합 | Microsoft Docs'
description: Azure Active Directory와 F5 간에 Single Sign-On을 구성하는 방법을 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/19/2019
ms.author: jeedes
ms.openlocfilehash: 60c699e35cb182c6a90ae60efe93303569a35014
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88540775"
---
# <a name="tutorial-configure-single-sign-on-sso-between-azure-active-directory-and-f5"></a>자습서: Azure Active Directory와 F5 간에 SSO(Single Sign-On) 구성

이 자습서에서는 Azure AD(Azure Active Directory)와 F5를 통합하는 방법에 대해 알아봅니다. F5를 Azure AD와 통합하면 다음을 수행할 수 있습니다.

* Azure AD에서 F5에 대한 액세스 권한이 있는 사용자를 제어합니다.
* 사용자가 해당 Azure AD 계정으로 F5에 자동으로 로그인되도록 설정합니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

Azure AD의 Single Sign-On과 SaaS 앱 통합에 대해 자세히 알아보려면 [Azure Active Directory의 애플리케이션에 대한 Single Sign-On](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)을 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

시작하려면 다음 항목이 필요합니다.

* Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.

* F5 SSO(Single Sign-On)가 설정된 구독

* 공동 솔루션을 배포하려면 다음 라이선스가 필요합니다.

    * F5 BIG-IP® Best 번들 (또는) 

    * F5 BIG-IP APM(Access Policy Manager)™ 독립 실행형 라이선스 

    * 기본 BIG-IP F5 BIG-IP® LTM(Local Traffic Manager)™에 대한 F5 BIG-IP Access Policy Manager™ (APM) 추가 기능 라이선스

    * F5 시스템에는 위의 라이선스 외에도 다음 라이선스가 부여될 수 있습니다. 

        * URL 범주 데이터베이스를 사용하는 URL 필터링 구독

        * 알려진 공격자와 악성 트래픽을 탐지 및 차단하는 F5 IP 인텔리전스 구독
     
        * 강력한 인증을 위해 디지털 키를 보호하고 관리하는 HSM(네트워크 하드웨어 보안 모듈)

* F5 BIG-IP 시스템은 APM 모듈을 통해 프로비저닝됩니다(LTM은 선택 사항).

* 선택 사항이지만, HA(고가용성)를 위해 부동 IP 주소를 사용하여 F5 시스템을 활성-대기 쌍이 포함된 [S/F DG](https://techdocs.f5.com/content/techdocs/en-us/bigip-14-1-0/big-ip-device-service-clustering-administration-14-1-0.html)(동기화/장애 조치 디바이스 그룹)에 배포하는 것이 좋습니다. LACP(링크 집계 제어 프로토콜)를 사용하여 더 많은 인터페이스 중복성을 달성할 수 있습니다. LACP는 연결된 실제 인터페이스를 단일 가상 인터페이스(집계 그룹)로 관리하고 그룹 내의 인터페이스 오류를 검색합니다.

* Kerberos 애플리케이션의 경우 제한된 위임을 위한 온-프레미스 AD 서비스 계정입니다.  AD 위임 계정을 만들려면 [F5 설명서](https://support.f5.com/csp/article/K43063049)를 참조하세요.

## <a name="access-guided-configuration"></a>단계별 구성 액세스

* 단계별 구성 액세스는 F5 TMOS 버전 13.1.0.8 이상에서 지원됩니다. BIG-IP 시스템에서 13.1.0.8 버전 미만을 실행하는 경우 **고급 구성** 섹션을 참조하세요.

* 단계별 구성 액세스는 완전히 새롭고 간소화된 사용자 환경을 제공합니다. 이 워크플로 기반 아키텍처는 선택한 토폴로지에 맞게 조정되어 다시 도입된 직관적인 구성 단계를 제공합니다.

* 구성을 진행하기 전에 [downloads.f5.com](https://login.f5.com/resource/login.jsp?ctx=719748)에서 최신 사용 사례 팩을 다운로드하여 단계별 구성을 업그레이드합니다. 업그레이드하려면 아래 절차를 따르세요.

    >[!NOTE]
    >아래 스크린샷은 최신 릴리스 버전(AGC 버전 5.0이 포함된 BIG-IP 15.0)에 대한 것입니다. 아래 구성 단계는 13.1.0.8부터 최신 BIG-IP 버전까지의 이 사용 사례에 유효합니다.

1. F5 BIG-IP 웹 UI에서 **Access >> Guide Configuration**(액세스 >> 단계별 구성)을 차례로 클릭합니다.

1. **Guided Configuration**(단계별 구성) 페이지의 왼쪽 위 모서리에서 **Upgrade Guided Configuration**(단계별 구성 업그레이드)을 클릭합니다.

    ![F5(헤더 기반) 구성](./media/headerf5-tutorial/configure14.png) 

1. Upgrade Guided Configuration 팝업 화면에서 **Choose File**(파일 선택)을 선택하여 다운로드한 사용 사례 팩을 업로드하고 **Upload and Install**(업로드 후 설치) 단추를 클릭합니다.

    ![F5(헤더 기반) 구성](./media/headerf5-tutorial/configure15.png) 

1. 업그레이드가 완료되면 **Continue**(계속) 단추를 클릭합니다.

    ![F5(헤더 기반) 구성](./media/headerf5-tutorial/configure16.png)

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD SSO를 구성하고 테스트합니다.

* F5 SSO는 세 가지 방법으로 구성할 수 있습니다.

- [헤더 기반 애플리케이션에 대한 F5 Single Sign-On 구성](#configure-f5-single-sign-on-for-header-based-application)

- [Kerberos 애플리케이션에 대한 F5 Single Sign-On 구성](kerbf5-tutorial.md)

- [고급 Kerberos 애플리케이션에 대한 F5 Single Sign-On 구성](advance-kerbf5-tutorial.md)

### <a name="key-authentication-scenarios"></a>키 인증 시나리오

* F5는 최신 인증 프로토콜(예: Open ID Connect, SAML 및 WS-Fed)에 대한 Azure Active Directory 네이티브 통합 지원 외에도 Azure AD를 통한 내부 및 외부 액세스 모두를 위해 레거시 기반 인증 앱에 대한 보안 액세스를 확장하여 이러한 애플리케이션에 대한 최신 시나리오(예 : 암호 없는 액세스)를 가능하게 합니다. 여기에 포함되는 앱은 다음과 같습니다.

* 헤더 기반 인증 앱

* Kerberos 인증 앱

* 익명 인증 또는 기본 제공 인증 앱 없음

* NTLM 인증 앱(이중 프롬프트를 사용하여 사용자 보호)

* 양식 기반 애플리케이션(이중 프롬프트를 사용하여 사용자 보호)

## <a name="adding-f5-from-the-gallery"></a>갤러리에서 F5 추가

F5가 Azure AD에 통합되도록 구성하려면 갤러리에서 F5를 관리형 SaaS 앱 목록에 추가해야 합니다.

1. [Azure Portal](https://portal.azure.com)에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션**으로 이동한 다음, **모든 애플리케이션**을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션**을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에 **F5**를 입력합니다.
1. 결과 패널에서 **F5**를 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.

## <a name="configure-and-test-azure-ad-single-sign-on-for-f5"></a>F5에 대한 Azure AD Single Sign-On 구성 및 테스트

**B.Simon**이라는 테스트 사용자를 사용하여 F5에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 F5의 관련 사용자 간에 연결 관계를 설정해야 합니다.

F5에서 Azure AD SSO를 구성하고 테스트하려면 다음 구성 요소를 완료합니다.

1. **[Azure AD SSO 구성](#configure-azure-ad-sso)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
    1. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - B.Simon을 사용하여 Azure AD Single Sign-On을 테스트합니다.
    1. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - B. Simon이 Azure AD Single Sign-On을 사용할 수 있도록 합니다.
1. **[F5 SSO 구성](#configure-f5-sso)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
    1. **[F5 테스트 사용자 만들기](#create-f5-test-user)** - B.Simon의 Azure AD 표현과 연결된 해당 사용자를 F5에 만듭니다.
1. **[SSO 테스트](#test-sso)** - 구성이 작동하는지 여부를 확인합니다.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)의 **F5** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾고, **Single Sign-On**을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML**을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성**에 대한 편집(연필 모양) 아이콘을 클릭하여 설정을 편집합니다.

   ![기본 SAML 구성 편집](common/edit-urls.png)

1. **IDP** 섹션에서 애플리케이션을 구성하려면 **기본 SAML 구성** 섹션에서 다음 필드 값을 입력합니다.

    a. **식별자** 텍스트 상자에서 `https://<YourCustomFQDN>.f5.com/` 패턴을 사용하여 URL을 입력합니다.

    b. **회신 URL** 텍스트 상자에서 `https://<YourCustomFQDN>.f5.com/` 패턴을 사용하여 URL을 입력합니다.

1. **SP** 시작 모드에서 애플리케이션을 구성하려면 **추가 URL 설정**를 클릭하고 다음 단계를 수행합니다.

    **로그인 URL** 텍스트 상자에서 `https://<YourCustomFQDN>.f5.com/` 패턴을 사용하여 URL을 입력합니다.

    > [!NOTE]
    > 이러한 값은 실제 값이 아닙니다. 실제 식별자, 회신 URL 및 로그온 URL을 사용하여 이러한 값을 업데이트합니다. 이러한 값을 얻으려면 [F5 클라이언트 지원 팀](https://support.f5.com/csp/knowledge-center/software/BIG-IP?module=BIG-IP%20APM45)에 문의하세요. Azure Portal의 **기본 SAML 구성** 섹션에 표시된 패턴을 참조할 수도 있습니다.

1. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **페더레이션 메타데이터 XML** 및 **인증서(Base64)** 를 찾고, **다운로드**를 선택하여 인증서를 다운로드하고 컴퓨터에 저장합니다.

    ![인증서 다운로드 링크](common/metadataxml.png)

1. **F5 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

    ![구성 URL 복사](common/copy-configuration-urls.png)

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

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 B.Simon에게 F5에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션**을 선택한 다음, **모든 애플리케이션**을 선택합니다.
1. 애플리케이션 목록에서 **F5**를 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고 **사용자 및 그룹**을 선택합니다.

   !["사용자 및 그룹" 링크](common/users-groups-blade.png)

1. **사용자 추가**를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![사용자 추가 링크](common/add-assign-user.png)

1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **B.Simon**을 선택한 다음, 화면 아래쪽에서 **선택** 단추를 클릭합니다.
1. SAML 어설션에 역할 값이 필요한 경우 **역할 선택** 대화 상자의 목록에서 사용자에 대한 적절한 역할을 선택한 다음, 화면의 아래쪽에 있는 **선택** 단추를 클릭합니다.
1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.
1. **조건부 액세스**를 클릭합니다.
1. **새 정책**을 클릭합니다.
1. 이제 F5 앱을 CA 정책의 리소스로 표시하고 다단계 인증, 디바이스 기반 액세스 제어 또는 ID 보호 정책을 포함한 모든 조건부 액세스를 적용할 수 있습니다.

## <a name="configure-f5-sso"></a>F5 SSO 구성

- [Kerberos 애플리케이션에 대한 F5 Single Sign-On 구성](kerbf5-tutorial.md)

- [고급 Kerberos 애플리케이션에 대한 F5 Single Sign-On 구성](advance-kerbf5-tutorial.md)

### <a name="configure-f5-single-sign-on-for-header-based-application"></a>헤더 기반 애플리케이션에 대한 F5 Single Sign-On 구성

### <a name="guided-configuration"></a>단계별 구성

1. 새 웹 브라우저 창을 열고, F5(헤더 기반) 회사 사이트에 관리자로 로그인하여 다음 단계를 수행합니다.

1. **System > Certificate Management > Traffic Certificate Management > SSL Certificate List**(시스템 > 인증서 관리 > 트래픽 인증서 관리 >> SSL 인증서 목록)로 차례로 이동합니다. 오른쪽 모서리에서 **Import**(가져오기)를 선택합니다. **Certificate Name**(인증서 이름)을 지정합니다(나중에 구성에서 참조됨). **Certificate Source**(인증서 원본)에서 Upload File(파일 업로드)을 선택하여 SAML Single Sign-On을 구성하는 동안 Azure에서 다운로드한 인증서를 지정합니다. **가져오기**를 클릭합니다.

    ![F5(헤더 기반) 구성](./media/headerf5-tutorial/configure12.png)
 
1. **애플리케이션 호스트 이름에 대한 SSL 인증서도 필요합니다. System > Certificate Management > Traffic Certificate Management > SSL Certificate List(시스템 > 인증서 관리 > 트래픽 인증서 관리 > SSL 인증서 목록)로 차례로 이동합니다**. 오른쪽 모서리에서 **Import**(가져오기)를 선택합니다. **Import Type**(유형 가져오기)은 **PKCS 12(IIS)** 입니다. **Key Name**(키 이름)을 지정하고(나중에 구성에서 참조됨), PFX 파일을 지정합니다. PFX에 대한 **Password**(암호)를 지정합니다. **가져오기**를 클릭합니다.

    >[!NOTE]
    >이 예에서 앱 이름은 `Headerapp.superdemo.live`이고, 키 이름이 `WildCard-SuperDemo.live`인 와일드카드 인증서를 사용하고 있습니다.

    ![F5(헤더 기반) 구성](./media/headerf5-tutorial/configure13.png)

1. Guided Experience(단계별 환경)를 사용하여 Azure AD 페더레이션 및 애플리케이션 액세스를 설정합니다. F5 BIG-IP **Main**(기본)으로 이동하고, **Access > Guided Configuration > Federation > SAML Service Provider**(액세스 > 단계별 구성 > 페더레이션 > SAML 서비스 공급자)를 차례로 선택합니다. **Next**(다음)를 클릭한 다음, **Next**를 클릭하여 구성을 시작합니다.

    ![F5(헤더 기반) 구성](./media/headerf5-tutorial/configure01.png)

    ![F5(헤더 기반) 구성](./media/headerf5-tutorial/configure02.png)
 
1. **Configuration Name**(구성 이름)을 제공합니다. **Entity ID**(엔터티 ID)를 지정합니다(Azure AD 애플리케이션 구성에서 구성한 것과 동일). **Host name**(호스트 이름)을 지정합니다. 참조를 위한 **Description**(설명)을 추가합니다. 나머지 기본 항목을 적용한 다음, **Save & Next**(저장 후 다음)를 선택하여 클릭합니다.

    ![F5(헤더 기반) 구성](./media/headerf5-tutorial/configure03.png) 

1. 이 예에서는 새 가상 서버를 443 포트가 있는 192.168.30.20으로 만듭니다. **Destination Address**(대상 주소)에서 가상 서버 IP 주소를 지정합니다. **Client SSL Profile**(클라이언트 SSL 프로필), Create new(새로 만들기)를 차례로 선택합니다. 이전에 업로드한 애플리케이션 인증서(이 예에서는 와일드카드 인증서) 및 연결된 키를 지정한 다음, **Save & Next**(저장 후 다음)를 클릭합니다.

    >[!NOTE]
    >이 예에서는 내부 웹 서버가 888 포트에서 실행되고 있고, 443 포트를 사용하여 게시하려고 합니다.

    ![F5(헤더 기반) 구성](./media/headerf5-tutorial/configure04.png) 

1. **Select method to configure your IdP connector**(IdP 커넥터를 구성하는 방법 선택) 아래에서 Metadata(메타데이터)를 지정하고, Choose File(파일 선택)을 클릭하고, 이전에 Azure AD에서 다운로드한 메타데이터 XML 파일을 업로드합니다. SAML IDP 커넥터에 대해 고유한 **Name**(이름)을 지정합니다. 이전에 업로드한 **Metadata Signing Certificate**(메타데이터 서명 인증서)를 선택합니다. **Save & Next**(저장 후 다음)를 클릭합니다.

    ![F5(헤더 기반) 구성](./media/headerf5-tutorial/configure05.png)
 
1. **Select a Pool**(풀 선택) 아래에서 **Create New**(새로 만들기)를 지정합니다(또는 이미 있는 풀 선택). 다른 값은 기본값으로 둡니다. Pool Servers(풀 서버)의 **IP Address/Node Name**(IP 주소/노드 이름) 아래에서 IP 주소를 입력합니다. **Port**(포트)를 지정합니다. **Save & Next**(저장 후 다음)를 클릭합니다.

    ![F5(헤더 기반) 구성](./media/headerf5-tutorial/configure06.png)

1. Single Sign-On Settings(Single Sign-On 설정) 화면에서 **Enable Single Sign-On**(Single Sign-On 사용)을 선택합니다. Selected Single Sign-On Type(선택한 Single Sign-On 유형) 아래에서 **HTTP header-based**(HTTP 헤더 기반)를 선택합니다. Username Source(사용자 이름 원본) 아래에서 **session.saml.last.Identity**를 **session.saml.last.attr.name.Identity**로 바꿉니다(이 변수는 Azure AD의 클레임 매핑을 사용하여 설정). SSO Headers(SSO 헤더) 아래에서 다음과 같이 수행합니다.

    * **Header Name(헤더 이름): MyAuthorization**

    * **Header Value(헤더 값): %{session.saml.last.attr.name.Identity}**

    * **Save & Next**(저장 후 다음)를 클릭합니다.

    변수 및 값의 전체 목록은 부록을 참조하세요. 필요에 따라 더 많은 헤더를 추가할 수 있습니다.

    >[!NOTE]
    >Account Name(계정 이름)은 F5 Delegation Account Created(생성된 F5 위임 계정)입니다(F5 설명서 확인).

    ![F5(헤더 기반) 구성](./media/headerf5-tutorial/configure07.png) 

1. 이 지침에서는 엔드포인트 검사를 건너뜁니다.  자세한 내용은 F5 설명서를 참조하세요. **Save & Next**(저장 후 다음)를 선택합니다.

    ![F5(헤더 기반) 구성](./media/headerf5-tutorial/configure08.png)

1. 기본값을 적용하고, **Save & Next**(저장 후 다음)를 클릭합니다. SAML 세션 관리 설정에 대한 자세한 내용은 F5 설명서를 참조하세요.

    ![F5(헤더 기반) 구성](./media/headerf5-tutorial/configure09.png)

1. 요약 화면을 검토하고, **Deploy**(배포)를 선택하여 BIG-IP를 구성합니다. **Finish**(마침)를 클릭합니다.

    ![F5(헤더 기반) 구성](./media/headerf5-tutorial/configure10.png)

    ![F5(헤더 기반) 구성](./media/headerf5-tutorial/configure11.png)

## <a name="advanced-configuration"></a>고급 구성

이 섹션은 단계별 구성을 사용할 수 없거나 추가 매개 변수를 추가/수정하려는 경우에 사용됩니다. 애플리케이션 호스트 이름에 대한 TLS/SSL 인증서가 필요합니다.

1. **System > Certificate Management > Traffic Certificate Management > SSL Certificate List**(시스템 > 인증서 관리 > 트래픽 인증서 관리 >> SSL 인증서 목록)로 차례로 이동합니다. 오른쪽 모서리에서 **Import**(가져오기)를 선택합니다. **Import Type**(유형 가져오기)은 **PKCS 12(IIS)** 입니다. **Key Name**(키 이름)을 지정하고(나중에 구성에서 참조됨), PFX 파일을 지정합니다. PFX에 대한 **Password**(암호)를 지정합니다. **가져오기**를 클릭합니다.

    >[!NOTE]
    >이 예에서 앱 이름은 `Headerapp.superdemo.live`이고, 키 이름이 `WildCard-SuperDemo.live`인 와일드카드 인증서를 사용하고 있습니다.
  
    ![F5(헤더 기반) 구성](./media/headerf5-tutorial/configure17.png)

### <a name="adding-a-new-web-server-to-bigip-f5"></a>BigIP-F5에 새 웹 서버 추가

1. **Main > IApps > Application Services > Application > Create**(기본 > IApps > 애플리케이션 서비스 > 애플리케이션 > 만들기)를 차례로 클릭합니다.

1. **Name**(이름)을 제공하고, **Template**(템플릿)에서 **f5.http**를 선택합니다.
 
    ![F5(헤더 기반) 구성](./media/headerf5-tutorial/configure18.png)

1. 이 경우 HeaderApp2를 HTTPS로 외부에 게시합니다. **how should the BIG-IP system handle SSL Traffic?** (BIG-IP 시스템에서 SSL 트래픽을 어떻게 처리해야 합니까?) 아래에서 **Terminate SSL from Client, Plaintext to servers (SSL Offload)** (클라이언트에서 SSL 종료, 일반 텍스트에서 서버로(SSL 오프로드))를 지정합니다. **어떤 SSL 인증서를 사용하시겠습니까?** 및 **어떤 SSL 프라이빗 키를 사용하시겠습니까?** 아래에서 인증서와 키를 지정합니다. **What IP Address do you want to use for the Virtual Server?** (어떤 IP 주소를 가상 서버에 사용하시겠습니까?) 아래에서 가상 서버 IP를 지정합니다. 

    * **기타 세부 정보 지정**

        * FQDN  

        * 기존 앱 풀을 지정하거나 새 애플리케이션 풀을 만듭니다.

        * 새 앱 서버를 만드는 경우 **internal IP Address**(내부 IP 주소) 및 **port number**(포트 번호)를 지정합니다.

        ![F5(헤더 기반) 구성](./media/headerf5-tutorial/configure19.png) 

1. **Finished**(마침)를 클릭합니다.

    ![F5(헤더 기반) 구성](./media/headerf5-tutorial/configure20.png) 

1. 앱 속성을 수정할 수 있는지 확인합니다. **Main > IApps > Application Services(기본 > IApps > 애플리케이션 서비스): Applications(애플리케이션) >> HeaderApp2**를 차례로 클릭합니다. **Strict Updates**(엄격한 업데이트)의 선택을 취소합니다(GUI 외부에서 일부 설정을 수정함). **Update**(업데이트) 단추를 클릭합니다.

    ![F5(헤더 기반) 구성](./media/headerf5-tutorial/configure21.png) 

1. 이 시점에서 가상 서버를 검색할 수 있습니다.

### <a name="configuring-f5-as-sp-and-azure-as-idp"></a>F5를 SP로, Azure를 IDP로 구성

1.  **Access > Federation> SAML Service Provider > Local SP Service**(액세스 > 페더레이션 > SAML 서비스 공급자 > 로컬 SP 서비스)를 차례로 클릭하고, Create(만들기) 또는 + 기호를 클릭합니다.

    ![F5(헤더 기반) 구성](./media/headerf5-tutorial/configure22.png)

1. 서비스 공급자 서비스에 대한 세부 정보를 지정합니다. F5 SP 구성을 나타내는 **Name**(이름)을 지정합니다. **Entity ID**(엔터티 ID)를 지정합니다(일반적으로 애플리케이션 URL과 동일).

    ![F5(헤더 기반) 구성](./media/headerf5-tutorial/configure23.png)

    ![F5(헤더 기반) 구성](./media/headerf5-tutorial/configure24.png)

    ![F5(헤더 기반) 구성](./media/headerf5-tutorial/configure25.png)

    ![F5(헤더 기반) 구성](./media/headerf5-tutorial/configure26.png)

    ![F5(헤더 기반) 구성](./media/headerf5-tutorial/configure27.png)

    ![F5(헤더 기반) 구성](./media/headerf5-tutorial/configure28.png)

### <a name="create-idp-connector"></a>Idp 커넥터 만들기

1. **Bind/Unbind IdP Connectors**(IdP 커넥터 바인딩/ 바인딩 해제) 단추를 클릭하고, **Create New IdP Connector**(새 IdP 커넥터 만들기), **From Metadata**(메타데이터에서) 옵션을 차례로 선택하고, 다음 단계를 수행합니다.
 
    ![F5(헤더 기반) 구성](./media/headerf5-tutorial/configure29.png)

    a. Azure AD에서 다운로드한 metadata.xml 파일을 찾고, **Identity Provider Name**(ID 공급자 이름)을 지정합니다.

    b. **OK**(확인)를 클릭합니다.

    다. 커넥터가 만들어지고, 인증서가 메타데이터 xml 파일에서 자동으로 준비됩니다.
    
    ![F5(헤더 기반) 구성](./media/headerf5-tutorial/configure30.png)

    d. 모든 요청을 Azure AD로 보내도록 F5BIG-IP를 구성합니다.

    e. **Add New Row**(새 행 추가)를 클릭하고, **AzureIDP**를 선택합니다(이전 단계에서 만든 대로 지정함). 

    f. **Matching Source(일치하는 원본) = %{session.server.landinguri}** 

    g. **Matching Value(일치하는 값) = /** *

    h. **Update**(업데이트)를 클릭합니다.

    i. **확인**을 클릭합니다.

    j. **SAML IDP 설정이 완료되었습니다.**
    
    ![F5(헤더 기반) 구성](./media/headerf5-tutorial/configure31.png)

### <a name="configure-f5-policy-to-redirect-users-to-azure-saml-idp"></a>사용자를 Azure SAML IDP로 리디렉션하도록 F5 정책 구성

1. 사용자를 Azure SAML IDP로 리디렉션하도록 F5 정책을 구성하려면 다음 단계를 수행합니다.

    a. **Main > Access > Profile/Policies > Access Profiles**(기본 > 액세스 > 프로필/정책 > 액세스 프로필)를 차례로 클릭합니다.

    b. **만들기** 단추를 클릭합니다.

    ![F5(헤더 기반) 구성](./media/headerf5-tutorial/configure32.png)
 
    다. **Name**(이름)을 지정합니다(이 예에서는 HeaderAppAzureSAMLPolicy).

    d. 다른 설정을 사용자 지정할 수 있습니다. F5 설명서를 참조하세요.

    ![F5(헤더 기반) 구성](./media/headerf5-tutorial/configure33.png)

    ![F5(헤더 기반) 구성](./media/headerf5-tutorial/configure34.png) 

    e. **Finished**(마침)를 클릭합니다.

    f. 정책 만들기가 완료되면 Policy(정책)를 클릭하고, **Access Policy**(정책 액세스) 탭으로 이동합니다.

    ![F5(헤더 기반) 구성](./media/headerf5-tutorial/configure35.png)
 
    g. **Visual Policy Editor**(시각적 정책 편집기)를 클릭하고, **Access Policy for Profile**(프로필에 대한 액세스 정책) 링크를 편집합니다.

    h. Visual Policy Editor에서 + Sign in(+ 로그인)을 클릭하고, **SAML Auth**(SAML 인증)를 선택합니다.

    ![F5(헤더 기반) 구성](./media/headerf5-tutorial/configure36.png)

    ![F5(헤더 기반) 구성](./media/headerf5-tutorial/configure37.png)
 
    i. **Add Item**(항목 추가)을 클릭합니다.

    j. **Properties**(속성) 아래에서 **Name**(이름)을 지정하고, **AAA Server**(AAA 서버) 아래에서 이전에 구성한 SP를 선택하고, **SAVE**(저장)를 클릭합니다.
 
    ![F5(헤더 기반) 구성](./media/headerf5-tutorial/configure38.png)

    k. 기본 정책이 준비되었습니다. 정책을 사용자 지정하여 추가 원본/특성 저장소를 통합할 수 있습니다.

    ![F5(헤더 기반) 구성](./media/headerf5-tutorial/configure39.png)
 
    l. 위쪽에 있는 **Apply Access Policy**(액세스 정책 적용) 링크를 클릭해야 합니다.

### <a name="apply-access-profile-to-the-virtual-server"></a>가상 서버에 액세스 프로필 적용

1. F5 BIG-IP APM에서 프로필 설정을 들어오는 트래픽에 적용하고 이전에 정의한 액세스 정책을 실행할 수 있도록 액세스 프로필을 가상 서버에 할당합니다.

    a. **Main** > **Local Traffic** > **Virtual Servers**(기본 > 로컬 트래픽 > 가상 서버)를 차례로 클릭합니다.

    ![F5(헤더 기반) 구성](./media/headerf5-tutorial/configure40.png)
 
    b. 가상 서버를 클릭하고, **Access Profile**(액세스 프로필) 드롭다운에서 **Access Policy**(액세스 정책) 섹션으로 스크롤하고, 만든 SAML 정책(이 예에서는 HeaderAppAzureSAMLPolicy)을 선택합니다.

    다. **Update**(업데이트)를 클릭합니다.
 
    ![F5(헤더 기반) 구성](./media/headerf5-tutorial/configure41.png)

    d. 들어오는 어설션에서 사용자 지정 SAML 특성을 추출하여 HTTP 헤더로 백 엔드 테스트 애플리케이션에 전달하는 F5 BIG-IP iRule®을 만듭니다. **Main > Local Traffic > iRules > iRule List**(기본 > 로컬 트래픽 > iRule > iRule 목록)를 차례로 클릭하고, Create(만들기)를 클릭합니다.

    ![F5(헤더 기반) 구성](./media/headerf5-tutorial/configure42.png)
 
    e. 아래의 F5 BIG-IP iRule 텍스트를 Definition(wjddml) 창에 붙여넣습니다.

    ![F5(헤더 기반) 구성](./media/headerf5-tutorial/configure43.png)
 
    when RULE_INIT {  set static::debug 0  }  when ACCESS_ACL_ALLOWED {

    set AZUREAD_USERNAME [ACCESS::session data get "session.saml.last.attr.name. http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name"] if { $static::debug } { log local0. "AZUREAD_USERNAME = $AZUREAD_USERNAME" } if { !([HTTP::header exists "AZUREAD_USERNAME"]) } { HTTP::header insert "AZUREAD_USERNAME" $AZUREAD_USERNAME }

    set AZUREAD_DISPLAYNAME [ACCESS::session data get "session.saml.last.attr.name. http://schemas.microsoft.com/identity/claims/displayname"] if { $static::debug } { log local0. "AZUREAD_DISPLAYNAME = $AZUREAD_DISPLAYNAME" } if { !([HTTP::header exists "AZUREAD_DISPLAYNAME"]) } { HTTP::header insert "AZUREAD_DISPLAYNAME" $AZUREAD_DISPLAYNAME }

    set AZUREAD_EMAILADDRESS [ACCESS::session data get "session.saml.last.attr.name. http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress"] if { $static::debug } { log local0. "AZUREAD_EMAILADDRESS = $AZUREAD_EMAILADDRESS" } if { !([HTTP::header exists "AZUREAD_EMAILADDRESS"]) } { HTTP::header insert "AZUREAD_EMAILADDRESS" $AZUREAD_EMAILADDRESS }}

    **샘플 출력은 아래와 같습니다.**

    ![F5(헤더 기반) 구성](./media/headerf5-tutorial/configure44.png)
 
### <a name="create-f5-test-user"></a>F5 테스트 사용자 만들기

이 섹션에서는 F5에서 B.Simon이라는 사용자를 만듭니다.  [F5 클라이언트 지원 팀](https://support.f5.com/csp/knowledge-center/software/BIG-IP?module=BIG-IP%20APM45)과 협력하여 사용자를 F5 플랫폼에 추가합니다. Single Sign-On을 사용하려면 먼저 사용자를 만들고 활성화해야 합니다. 

## <a name="test-sso"></a>SSO 테스트 

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 F5 타일을 클릭하면 SSO를 설정한 F5에 자동으로 로그인됩니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

- [Azure Active Directory와 SaaS 앱을 통합하는 방법에 대한 자습서 목록](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory를 사용한 애플리케이션 액세스 및 Single Sign-On이란?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory의 조건부 액세스란?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Azure AD로 F5 사용해 보기](https://aad.portal.azure.com/)

- [Kerberos 애플리케이션에 대한 F5 Single Sign-On 구성](kerbf5-tutorial.md)

- [고급 Kerberos 애플리케이션에 대한 F5 Single Sign-On 구성](advance-kerbf5-tutorial.md)

