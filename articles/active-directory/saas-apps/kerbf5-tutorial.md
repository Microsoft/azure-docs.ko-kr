---
title: '자습서: F5와 Azure AD Single Sign-On 통합 | Microsoft Docs'
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
ms.openlocfilehash: b48b8c1282f90788846b9dc3b64a5ea1ee018a3c
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88535988"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-f5"></a>자습서: F5와 Azure Active Directory SSO(Single Sign-On) 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 F5를 통합하는 방법에 대해 알아봅니다. F5를 Azure AD와 통합하면 다음을 수행할 수 있습니다.

* Azure AD에서 F5에 대한 액세스 권한이 있는 사용자를 제어합니다.
* 사용자가 해당 Azure AD 계정으로 F5에 자동으로 로그인되도록 설정합니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory를 사용한 애플리케이션 액세스 및 Single Sign-On이란 무엇인가요?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)를 참조하세요.

## <a name="prerequisites"></a>필수 구성 요소

시작하려면 다음 항목이 필요합니다.

* Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.

* F5 SSO(Single Sign-On)가 설정된 구독

* 공동 솔루션을 배포하려면 다음 라이선스가 필요합니다.
    * F5 BIG-IP&reg; Best 번들(또는)

    * F5 BIG-IP APM(Access Policy Manager)&trade; 독립 실행형 라이선스

    * 기존 BIG-IP F5 BIG-IP&reg; LTM(Local Traffic Manager)&trade;에 대한 F5 BIG-IP APM(Access Policy Manager)&trade; 추가 기능 라이선스.

    * F5 시스템에는 위의 라이선스 외에도 다음 라이선스가 부여될 수 있습니다.

        * URL 범주 데이터베이스를 사용하는 URL 필터링 구독

        * 알려진 공격자와 악성 트래픽을 탐지 및 차단하는 F5 IP 인텔리전스 구독

        * 강력한 인증을 위해 디지털 키를 보호하고 관리하는 HSM(네트워크 하드웨어 보안 모듈)

* F5 BIG-IP 시스템은 APM 모듈을 통해 프로비저닝됩니다(LTM은 선택 사항).

* 선택 사항이지만, HA(고가용성)를 위해 부동 IP 주소를 사용하여 F5 시스템을 활성-대기 쌍이 포함된 [S/F DG](https://techdocs.f5.com/content/techdocs/en-us/bigip-14-1-0/big-ip-device-service-clustering-administration-14-1-0.html)(동기화/장애 조치 디바이스 그룹)에 배포하는 것이 좋습니다. LACP(링크 집계 제어 프로토콜)를 사용하여 더 많은 인터페이스 중복성을 달성할 수 있습니다. LACP는 연결된 실제 인터페이스를 단일 가상 인터페이스(집계 그룹)로 관리하고 그룹 내의 인터페이스 오류를 검색합니다.

* Kerberos 애플리케이션의 경우 제한된 위임을 위한 온-프레미스 AD 서비스 계정입니다.  AD 위임 계정을 만들려면 [F5 설명서](https://support.f5.com/csp/article/K43063049)를 참조하세요.

## <a name="access-guided-configuration"></a>단계별 구성 액세스

* 단계별 구성 액세스'는 F5 TMOS 버전 13.1.0.8 이상에서 지원됩니다. BIG-IP 시스템에서 13.1.0.8 버전 미만을 실행하는 경우 **고급 구성** 섹션을 참조하세요.

* 단계별 구성 액세스는 완전히 새롭고 간소화된 사용자 환경을 제공합니다. 이 워크플로 기반 아키텍처는 선택한 토폴로지에 맞게 조정되어 다시 도입된 직관적인 구성 단계를 제공합니다.

* 구성을 진행하기 전에 [downloads.f5.com](https://login.f5.com/resource/login.jsp?ctx=719748)에서 최신 사용 사례 팩을 다운로드하여 단계별 구성을 업그레이드합니다. 업그레이드하려면 아래 절차를 따르세요.

    >[!NOTE]
    >아래 스크린샷은 최신 릴리스 버전(AGC 버전 5.0이 포함된 BIG-IP 15.0)에 대한 것입니다. 아래 구성 단계는 13.1.0.8부터 최신 BIG-IP 버전까지의 이 사용 사례에 유효합니다.

1. F5 BIG-IP 웹 UI에서 **Access >> Guide Configuration**(액세스 >> 단계별 구성)을 차례로 클릭합니다.

2. **Guided Configuration**(단계별 구성) 페이지의 왼쪽 위 모서리에서 **Upgrade Guided Configuration**(단계별 구성 업그레이드)을 클릭합니다.

    ![F5(Kerberos) 구성](./media/kerbf5-tutorial/configure14.png) 

3. Upgrade Guided Configuration 팝업 화면에서 **Choose File**(파일 선택)을 선택하여 다운로드한 사용 사례 팩을 업로드하고 **Upload and Install**(업로드 후 설치) 단추를 클릭합니다.

    ![F5(Kerberos) 구성](./media/kerbf5-tutorial/configure15.png) 

4. 업그레이드가 완료되면 **Continue**(계속) 단추를 클릭합니다.

    ![F5(Kerberos) 구성](./media/kerbf5-tutorial/configure16.png)

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD SSO를 구성하고 테스트합니다.

* F5는 **SP 및 IDP** 시작 SSO를 지원합니다.
* F5 SSO는 세 가지 방법으로 구성할 수 있습니다.

- [Kerberos 애플리케이션에 대한 F5 Single Sign-On 구성](#configure-f5-single-sign-on-for-kerberos-application)

- [헤더 기반 애플리케이션에 대한 F5 Single Sign-On 구성](headerf5-tutorial.md)

- [고급 Kerberos 애플리케이션에 대한 F5 Single Sign-On 구성](advance-kerbf5-tutorial.md)

### <a name="key-authentication-scenarios"></a>키 인증 시나리오

F5는 최신 인증 프로토콜(예: Open ID Connect, SAML 및 WS-Fed)에 대한 Azure Active Directory 네이티브 통합 지원 외에도 Azure AD를 통한 내부 및 외부 액세스 모두를 위해 레거시 기반 인증 앱에 대한 보안 액세스를 확장하여 이러한 애플리케이션에 대한 최신 시나리오(예 : 암호 없는 액세스)를 가능하게 합니다. 여기에 포함되는 앱은 다음과 같습니다.

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

1. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **페더레이션 메타데이터 XML** 및 **인증서(Base64)** 를 찾은 다음, **다운로드**를 선택하여 인증서를 다운로드하고 컴퓨터에 저장합니다.

    ![인증서 다운로드 링크](common/metadataxml.png)

1. **F5 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

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

- [헤더 기반 애플리케이션에 대한 F5 Single Sign-On 구성](headerf5-tutorial.md)

- [고급 Kerberos 애플리케이션에 대한 F5 Single Sign-On 구성](advance-kerbf5-tutorial.md)

### <a name="configure-f5-single-sign-on-for-kerberos-application"></a>Kerberos 애플리케이션에 대한 F5 Single Sign-On 구성

### <a name="guided-configuration"></a>단계별 구성

1. 새 웹 브라우저 창을 열고, F5(Kerberos) 회사 사이트에 관리자로 로그인하여 다음 단계를 수행합니다.

1. 메타데이터 인증서는 나중에 설정 프로세스에서 사용할 F5로 가져와야 합니다.

1. **System > Certificate Management > Traffic Certificate Management > SSL Certificate List**(시스템 > 인증서 관리 > 트래픽 인증서 관리 >> SSL 인증서 목록)로 차례로 이동합니다. 오른쪽 모서리에서 **Import**(가져오기)를 선택합니다. **Certificate Name**(인증서 이름)을 지정합니다(나중에 구성에서 참조됨). **Certificate Source**(인증서 원본)에서 Upload File(파일 업로드)을 선택하여 SAML Single Sign-On을 구성하는 동안 Azure에서 다운로드한 인증서를 지정합니다. **가져오기**를 클릭합니다.

    ![F5(Kerberos) 구성](./media/kerbf5-tutorial/configure01.png) 

1. **애플리케이션 호스트 이름에 대한 SSL 인증서도 필요합니다. System > Certificate Management > Traffic Certificate Management > SSL Certificate List(시스템 > 인증서 관리 > 트래픽 인증서 관리 > SSL 인증서 목록)로 차례로 이동합니다**. 오른쪽 모서리에서 **Import**(가져오기)를 선택합니다. **Import Type**(유형 가져오기)은 **PKCS 12(IIS)** 입니다. **Key Name**(키 이름)을 지정하고(나중에 구성에서 참조됨), PFX 파일을 지정합니다. PFX에 대한 **Password**(암호)를 지정합니다. **가져오기**를 클릭합니다.

    >[!NOTE]
    >이 예에서 앱 이름은 `Kerbapp.superdemo.live`이고, 키 이름이 `WildCard-SuperDemo.live`인 와일드카드 인증서를 사용하고 있습니다.

    ![F5(Kerberos) 구성](./media/kerbf5-tutorial/configure02.png) 
 
1. Guided Experience(단계별 환경)를 사용하여 Azure AD 페더레이션 및 애플리케이션 액세스를 설정합니다. F5 BIG-IP **Main**(기본)으로 이동하고, **Access > Guided Configuration > Federation > SAML Service Provider**(액세스 > 단계별 구성 > 페더레이션 > SAML 서비스 공급자)를 차례로 선택합니다. **Next**(다음)를 클릭한 다음, **Next**를 클릭하여 구성을 시작합니다.

    ![F5(Kerberos) 구성](./media/kerbf5-tutorial/configure03.png) 

    ![F5(Kerberos) 구성](./media/kerbf5-tutorial/configure04.png)

1. **Configuration Name**(구성 이름)을 제공합니다. **Entity ID**(엔터티 ID)를 지정합니다(Azure AD 애플리케이션 구성에서 구성한 것과 동일). **Host name**(호스트 이름)을 지정합니다. 참조를 위한 **Description**(설명)을 추가합니다. 나머지 기본 항목을 적용한 다음, **Save & Next**(저장 후 다음)를 선택하여 클릭합니다.

    ![F5(Kerberos) 구성](./media/kerbf5-tutorial/configure05.png) 

1. 이 예에서는 새 가상 서버를 443 포트가 있는 192.168.30.200으로 만듭니다. **Destination Address**(대상 주소)에서 가상 서버 IP 주소를 지정합니다. **Client SSL Profile**(클라이언트 SSL 프로필), Create new(새로 만들기)를 차례로 선택합니다. 이전에 업로드한 애플리케이션 인증서(이 예에서는 와일드카드 인증서) 및 연결된 키를 지정한 다음, **Save & Next**(저장 후 다음)를 클릭합니다.

    >[!NOTE]
    >이 예에서는 내부 웹 서버가 80 포트에서 실행되고 있고, 443 포트를 사용하여 게시하려고 합니다.

    ![F5(Kerberos) 구성](./media/kerbf5-tutorial/configure06.png)

1. **Select method to configure your IdP connector**(IdP 커넥터를 구성하는 방법 선택) 아래에서 Metadata(메타데이터)를 지정하고, Choose File(파일 선택)을 클릭하고, 이전에 Azure AD에서 다운로드한 메타데이터 XML 파일을 업로드합니다. SAML IDP 커넥터에 대해 고유한 **Name**(이름)을 지정합니다. 이전에 업로드한 **Metadata Signing Certificate**(메타데이터 서명 인증서)를 선택합니다. **Save & Next**(저장 후 다음)를 클릭합니다.

    ![F5(Kerberos) 구성](./media/kerbf5-tutorial/configure07.png)  

1. **Select a Pool**(풀 선택) 아래에서 **Create New**(새로 만들기)를 지정합니다(또는 이미 있는 풀 선택). 다른 값은 기본값으로 둡니다.    Pool Servers(풀 서버)의 **IP Address/Node Name**(IP 주소/노드 이름) 아래에서 IP 주소를 입력합니다. **Port**(포트)를 지정합니다. **Save & Next**(저장 후 다음)를 클릭합니다.
 
    ![F5(Kerberos) 구성](./media/kerbf5-tutorial/configure08.png)

1. Single Sign-On Settings(Single Sign-On 설정) 화면에서 **Enable Single Sign-On**(Single Sign-On 사용)을 선택합니다. **Selected Single Sign-On Type**(선택한 Single Sign-On 유형) 아래에서 **Kerberos**를 선택합니다. **Username Source**(사용자 이름 원본) 아래에서 **session.saml.last.Identity**를 **session.saml.last.attr.name.Identity**로 바꿉니다(이 변수는 Azure AD의 클레임 매핑을 사용하여 설정). **Show Advanced Setting**(고급 설정 표시)을 선택합니다. **Kerberos Realm**(Kerberos 영역) 아래에서 도메인 이름을 입력합니다. **Account Name/ Account Password**(계정 이름/계정 암호) 아래에서 APM 위임 계정 및 암호를 지정합니다. **KDC** 필드에서 도메인 컨트롤러 IP를 지정합니다. **Save & Next**(저장 후 다음)를 클릭합니다.

    ![F5(Kerberos) 구성](./media/kerbf5-tutorial/configure09.png)   

1. 이 지침에서는 엔드포인트 검사를 건너뜁니다.  자세한 내용은 F5 설명서를 참조하세요.  화면에서 **Save & Next**(저장 후 다음)를 선택합니다.

    ![F5(Kerberos) 구성](./media/kerbf5-tutorial/configure10.png) 

1. 기본값을 적용하고, **Save & Next**(저장 후 다음)를 클릭합니다. SAML 세션 관리 설정에 대한 자세한 내용은 F5 설명서를 참조하세요.


    ![F5(Kerberos) 구성](./media/kerbf5-tutorial/configure11.png) 
 
1. 요약 화면을 검토하고, **Deploy**(배포)를 선택하여 BIG-IP를 구성합니다.
 
    ![F5(Kerberos) 구성](./media/kerbf5-tutorial/configure12.png)

1. 애플리케이션이 구성되었으면 **마침**을 클릭합니다.

    ![F5(Kerberos) 구성](./media/kerbf5-tutorial/configure13.png)

## <a name="advanced-configuration"></a>고급 구성

>[!NOTE]
>[여기](https://techdocs.f5.com/kb/en-us/products/big-ip_apm/manuals/product/apm-authentication-single-sign-on-11-5-0/2.html)를 클릭하여 참조하세요.

### <a name="configuring-an-active-directory-aaa-server"></a>Active Directory AAA 서버 구성

APM(Access Policy Manager)에서 Active Directory AAA 서버를 구성하여 APM에서 사용자를 인증하는 데 사용할 도메인 컨트롤러 및 자격 증명을 지정합니다.

1. Main(기본) 탭에서 **Access Policy > AAA Servers > Active Directory**(액세스 정책 > AAA 서버 > Active Directory)를 차례로 클릭합니다. Active Directory 서버 목록 화면이 열립니다.

2. **만들기**를 클릭합니다. New Server(새 서버) 속성 화면이 열립니다.

3. **Name**(이름) 필드에서 고유한 인증 서버 이름을 입력합니다.

4. **Domain Name**(도메인 이름) 필드에서 Windows 도메인의 이름을 입력합니다.

5. **Server Connection**(서버 연결) 설정에 대해 다음 옵션 중 하나를 선택합니다.

   * AAA 서버를 고가용성으로 설정하려면 **Use Pool**(풀 사용)을 선택합니다.

   * AAA 서버를 독립 실행형 기능으로 설정하려면 **Direct**(직접)를 선택합니다.

6. **Direct**를 선택한 경우 **Domain Controller**(도메인 컨트롤러) 필드에서 이름을 입력합니다.

7. **Use Pool**을 선택한 경우 다음과 같이 풀을 구성합니다.

   * **Domain Controller Pool Name**(도메인 컨트롤러 풀 이름) 필드에서 이름을 입력합니다.

   * 각각에 대한 IP 주소와 호스트 이름을 입력하고 **추가** 단추를 클릭하여 풀에서 **Domain Controllers**(도메인 컨트롤러)를 지정합니다.

   * AAA 서버의 상태를 모니터링하기 위해 상태 모니터를 선택할 수 있는 옵션이 있습니다. 이 경우에는 **gateway_icmp** 모니터만 적합합니다. 이는 **Server Pool Monitor**(서버 풀 모니터) 목록에서 선택할 수 있습니다.

8. **Admin Name**(관리자 이름) 필드에서 Active Directory 관리자 권한이 있는 관리자에 대한 대/소문자를 구분하는 이름을 입력합니다. APM에서는 **Admin Name**(관리자 이름) 및 **Admin Password**(관리자 암호) 필드의 정보를 AD 쿼리에 사용합니다. Active Directory가 익명 쿼리에 대해 구성된 경우 Admin Name을 제공할 필요가 없습니다. 그렇지 않으면 암호 관련 기능을 지원하기 위해 Active Directory 서버에 바인딩하고, 사용자 그룹 정보를 가져오고, Active Directory 암호 정책을 가져올 수 있는 충분한 권한이 있는 계정이 APM에 필요합니다. (예를 들어 AD 쿼리 작업에서 만료 전에 사용자가 암호를 변경하라는 메시지를 표시하는 옵션(Prompt)을 선택한 경우 APM에서 암호 정책을 가져와야 합니다.) 이 구성에서 관리자 계정 정보를 제공하지 않으면 APM에서 사용자 계정을 사용하여 정보를 가져옵니다. 이는 사용자 계정에 충분한 권한이 있는 경우에 작동합니다.

9. **Admin Password** 필드에서 Domain Name과 연결된 관리자 암호를 입력합니다.

10. **Verify Admin Password**(관리자 암호 확인) 필드에서 **Domain Name** 설정과 연결된 관리자 암호를 다시 입력합니다.

11. **Group Cache Lifetime**(그룹 캐시 수명) 필드에서 일 수를 입력합니다. 기본 수명은 30일입니다.

12. **Password Security Object Cache Lifetime**(암호 보안 개체 캐시 수명) 필드에서 일 수를 입력합니다. 기본 수명은 30일입니다.

13. **Kerberos Preauthentication Encryption Type**(Kerberos 사전 인증 암호화 유형) 목록에서 암호화 유형을 선택합니다. 기본값은 **None**(없음)입니다. 암호화 유형을 지정하는 경우 BIG-IP 시스템에는 첫 번째 인증 서비스 요청(AS-REQ) 패킷 내의 Kerberos 사전 인증 데이터가 포함됩니다.

14. **Timeout**(시간 제한) 필드에서 AAA 서버에 대한 시간 제한 간격(초)을 입력합니다. (이 설정은 선택 사항입니다.)

15. **Finished**(마침)를 클릭합니다. 새 서버가 목록에 표시됩니다. 그러면 새 Active Directory 서버가 Active Directory 서버 목록에 추가됩니다.

    ![F5(Kerberos) 구성](./media/kerbf5-tutorial/configure17.png)

### <a name="saml-configuration"></a>SAML 구성

1. 메타데이터 인증서는 나중에 설정 프로세스에서 사용할 F5로 가져와야 합니다. **System > Certificate Management > Traffic Certificate Management > SSL Certificate List**(시스템 > 인증서 관리 > 트래픽 인증서 관리 >> SSL 인증서 목록)로 차례로 이동합니다. 오른쪽 모서리에서 **Import**(가져오기)를 선택합니다.

    ![F5(Kerberos) 구성](./media/kerbf5-tutorial/configure18.png)

2. SAML IDP를 설정하려면 **Access > Federation > SAML(액세스 > 페더레이션 > SAML): Service Provider > External Idp Connectors(서비스 공급자 > 외부 Idp 커넥터)** 로 차례로 이동하고, **Create > From Metadata**(만들기 > 메타데이터에서)를 차례로 클릭합니다.

    ![F5(Kerberos) 구성](./media/kerbf5-tutorial/configure19.png)

    ![F5(Kerberos) 구성](./media/kerbf5-tutorial/configure20.png)

    ![F5(Kerberos) 구성](./media/kerbf5-tutorial/configure21.png)

    ![F5(Kerberos) 구성](./media/kerbf5-tutorial/configure22.png)

    ![F5(Kerberos) 구성](./media/kerbf5-tutorial/configure23.png)

    ![F5(Kerberos) 구성](./media/kerbf5-tutorial/configure24.png)

1. SAML SP를 설정하려면 **Access > Federation > SAML 서비스 공급자 > Local SP Services**(액세스 > 페더레이션 > SAML Service Provider > 로컬 SP 서비스)로 차례로 이동하고, **Create**(만들기)를 클릭합니다. 다음 정보를 완성하고, **OK**(확인)를 클릭합니다.

    * Type Name(형식 이름): KerbApp200SAML
    * Entity ID(엔터티 ID)*: https://kerb-app.com.cutestat.com
    * SP Name Settings(SP 이름 설정)
    * Scheme(체계): https
    * Host(호스트): kerbapp200.superdemo.live
    * Description(설명): kerbapp200.superdemo.live

     ![F5(Kerberos) 구성](./media/kerbf5-tutorial/configure25.png)

     b. KerbApp200SAML SP 구성을 선택하고, **Bind/UnBind IdP Connectors**(IdP 커넥터 바인딩/바인딩 해제)를 클릭합니다.

     ![F5(Kerberos) 구성](./media/kerbf5-tutorial/configure26.png)

     ![F5(Kerberos) 구성](./media/kerbf5-tutorial/configure27.png)

     다. **Add New Row**(새 행 추가)를 클릭하고, 이전 단계에서 만든 **External IdP connector**(외부 IdP 커넥터)를 선택하고, **Update**(업데이트)를 클릭한 다음, **OK**(확인)를 클릭합니다.

     ![F5(Kerberos) 구성](./media/kerbf5-tutorial/configure28.png)

1. Kerberos SSO를 구성하려면 **Access > Single Sign-On > Kerberos**(액세스 > Single Sign-On > Kerberos)로 차례로 이동하고, 정보를 완성하고 **Finished**(마침)를 클릭합니다.

    >[!Note]
    > Kerberos 위임 계정을 만들고 지정해야 합니다. KCD 섹션을 참조하세요(변수 참조는 부록 참조).

    * **사용자 이름 원본**: session.saml.last.attr.name.http:\//schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname

    * **User Realm Source**(사용자 영역 원본): session.logon.last.domain

        ![F5(Kerberos) 구성](./media/kerbf5-tutorial/configure29.png)

1. 액세스 프로필을 구성하려면 **Access > Profile/Policies > Access Profile (per session policies)** (액세스 > 프로필/정책 > 액세스 프로필(세션 정책별))을 차례로 이동하고, **Create**(만들기)를 클릭하고, 다음 정보를 완성하고, **Finished**(마침)를 클릭합니다.

    * 이름: KerbApp200
    * Profile Type(프로필 유형): 모두
    * Profile Scope(프로필 범위): 프로필
    * 언어: 영어

        ![F5(Kerberos) 구성](./media/kerbf5-tutorial/configure30.png)

1. KerbApp200이라는 이름을 클릭하고, 다음 정보를 완성하고, **Update**(업데이트)를 클릭합니다.

    * Domain Cookie(도메인 쿠키): superdemo.live
    * SSO Configuration(SSO 구성): KerAppSSO_sso

        ![F5(Kerberos) 구성](./media/kerbf5-tutorial/configure31.png)

1. **Access Policy**(액세스 정책)를 클릭한 다음, "KerbApp200" 프로필에 대해 **Edit Access Policy**(액세스 정책 편집)를 클릭합니다.

    ![F5(Kerberos) 구성](./media/kerbf5-tutorial/configure32.png)

    ![F5(Kerberos) 구성](./media/kerbf5-tutorial/configure33.png)

    ![F5(Kerberos) 구성](./media/kerbf5-tutorial/configure34.png)

    * **session.logon.last.usernameUPN   expr {[mcget {session.saml.last.identity}]}**

    * **session.ad.lastactualdomain  TEXT superdemo.live**

        ![F5(Kerberos) 구성](./media/kerbf5-tutorial/configure35.png)

    * **(userPrincipalName=%{session.logon.last.usernameUPN})**

        ![F5(Kerberos) 구성](./media/kerbf5-tutorial/configure36.png)

        ![F5(Kerberos) 구성](./media/kerbf5-tutorial/configure37.png)

    * **session.logon.last.username  expr { "[mcget {session.ad.last.attr.sAMAccountName}]" }**

        ![F5(Kerberos) 구성](./media/kerbf5-tutorial/configure38.png)

    * **mcget {session.logon.last.username}**
    * **mcget {session.logon.last.password**

1. 새 노드를 추가하려면 **Local Traffic > Nodes > Node List**(로컬 트래픽 > 노드 > 노드 목록)로 차례로 이동하고, Create(만들기)를 클릭하고, 다음 정보를 완성하고, **Finished**(마침)를 클릭합니다.

    * 이름: KerbApp200
    * 설명: KerbApp200
    * 주소: 192.168.20.200

        ![F5(Kerberos) 구성](./media/kerbf5-tutorial/configure39.png)

1. 새 풀을 만들려면 **Local Traffic > Pools > Pool List**(로컬 트래픽 > 풀 > 풀 목록)로 차례로 이동하고, Create(만들기)를 클릭하고, 다음 정보를 완성하고, **Finished**(마침)를 클릭합니다.

    * 이름: KerbApp200-Pool
    * 설명: KerbApp200-Pool
    * Health Monitors(상태 모니터): http
    * 주소: 192.168.20.200
    * Service Port(서비스 포트): 81

        ![F5(Kerberos) 구성](./media/kerbf5-tutorial/configure40.png)

1. 가상 서버를 만들려면 **Local Traffic > Virtual Servers > Virtual Server List > +** (로컬 트래픽 > 가상 서버 > 가상 서버 목록 > +)로 차례로 이동하고, 다음 정보를 완성하고, **Finished**(마침)를 클릭합니다.

    * 이름: KerbApp200
    * Destination Address/Mask(대상 주소/마스크): Host(호스트) 192.168.30.200
    * Service Port(서비스 포트): Port(포트) 443 HTTPS
    * Access Profile(액세스 프로필): KerbApp200
    * 이전 단계에서 만든 액세스 프로필을 지정합니다.

        ![F5(Kerberos) 구성](./media/kerbf5-tutorial/configure41.png)

        ![F5(Kerberos) 구성](./media/kerbf5-tutorial/configure42.png)

### <a name="setting-up-kerberos-delegation"></a>Kerberos 위임 설정 

>[!NOTE]
>[여기](https://www.f5.com/pdf/deployment-guides/kerberos-constrained-delegation-dg.pdf)를 클릭하여 참조하세요.

*  **1단계:** 위임 계정 만들기

    **예:**
    * Domain Name(도메인 이름): **superdemo.live**

    * Sam Account Name(SAM 계정 이름): **big ipuser**

    * New-ADUser -Name "APM 위임 계정" -UserPrincipalName host/big-ipuser.superdemo.live@superdemo.live -SamAccountName "big-ipuser" -PasswordNeverExpires $true -Enabled $true -AccountPassword (Read-Host -AsSecureString "Password!1234")

* **2단계:** SPN 설정(APM 위임 계정에 대해)

    **예:**
    * setspn –A **host/big-ipuser.superdemo.live** big-ipuser

* **3단계:** SPN 위임(App Service 계정의 경우). F5 위임 계정에 대한 적절한 위임을 설정합니다.
    아래 예에서 APM 위임 계정은 FRP-App1.superdemo.live app의 KCD에 대해 구성되었습니다.

    ![F5(Kerberos) 구성](./media/kerbf5-tutorial/configure43.png)

* 위 참조 문서의 [이 부분](https://techdocs.f5.com/kb/en-us/products/big-ip_apm/manuals/product/apm-authentication-single-sign-on-11-5-0/2.html)에서 설명한 대로 세부 정보를 제공합니다.

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

- [헤더 기반 애플리케이션에 대한 F5 Single Sign-On 구성](headerf5-tutorial.md)

- [고급 Kerberos 애플리케이션에 대한 F5 Single Sign-On 구성](advance-kerbf5-tutorial.md)

