---
title: Azure MFA 서버의 사용자 포털 - Azure Active Directory
description: Azure MFA 및 사용자 포털을 시작합니다.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/11/2018
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: ccd1192c7597b5a088b1c50b5367030a616b9d77
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88919593"
---
# <a name="user-portal-for-the-azure-multi-factor-authentication-server"></a>Azure Multi-Factor Authentication 서버의 사용자 포털

사용자 포털은 사용자가 Azure MFA(Multi-Factor Authentication)을 등록하고 계정을 관리할 수 있는 IIS 웹 사이트입니다. 사용자는 다음 로그온 시에 전화 번호를 변경하거나, PIN을 변경하거나, 2단계 검증을 바이패스할 수 있습니다.

사용자는 일반 사용자 이름과 암호를 사용하여 사용자 포털에 로그인한 다음 2단계 검증 통화를 완료하거나 보안 질문에 답변하여 인증을 완료합니다. 사용자 등록이 허용되면 사용자 포털에 처음 로그인할 때 전화 번호와 PIN을 구성합니다.

사용자 포털 관리자는 새 사용자를 추가하고 기존 사용자를 업데이트하기 위한 권한이 설정되고 부여될 수 있습니다.

사용자 환경에 따라 Azure Multi-factor Authentication 서버와 동일한 서버 또는 다른 인터넷 연결 서버에서 사용자 포털을 배포하는 것이 좋습니다.

> [!IMPORTANT]
> 2019 년 7 월 1 일부 터 Microsoft는 더 이상 새 배포를 위한 MFA 서버를 제공 하지 않습니다. 로그인 이벤트 중에 MFA (multi-factor authentication)를 요구 하려는 신규 고객은 클라우드 기반 Azure Multi-Factor Authentication를 사용 해야 합니다.
>
> 클라우드 기반 MFA를 시작 하려면 [자습서: Azure Multi-Factor Authentication를 사용 하 여 보안 사용자 로그인 이벤트](tutorial-enable-azure-mfa.md)를 참조 하세요.
>
> 2019 년 7 월 1 일 이전에 MFA 서버를 정품 인증 한 기존 고객은 평소와 같이 최신 버전, 향후 업데이트 및 활성화 자격 증명 생성을 다운로드할 수 있습니다.

![MFA 서버 사용자 포털 로그인 페이지](./media/howto-mfaserver-deploy-userportal/portal.png)

> [!NOTE]
> Multi-Factor Authentication 서버에서만 사용자 포털을 사용할 수 있습니다. 클라우드에서 Multi-Factor Authentication을 사용하는 경우 사용자는 [2단계 인증을 위한 계정 설정](../user-help/multi-factor-authentication-end-user-first-time.md) 또는 [2단계 인증을 위한 설정 관리](../user-help/multi-factor-authentication-end-user-manage-settings.md)를 참조합니다.

## <a name="install-the-web-service-sdk"></a>웹 서비스 SDK 설치

두 시나리오에서 Azure Multi-Factor Authentication 웹 서비스 SDK가 Azure MFA(Multi-Factor Authentication) 서버에 아직 설치되어 있지 **않으면** 다음 단계를 완료합니다.

1. Multi-Factor Authentication 서버 콘솔을 엽니다.
2. **웹 서비스 SDK**로 이동하고 **웹 서비스 SDK 설치**를 선택합니다.
3. 어떤 이유로든 변경해야 하는 경우가 아니면 기본값을 사용하여 설치를 완료합니다.
4. IIS에서 사이트에 TLS/SSL 인증서를 바인딩합니다.

IIS 서버에 TLS/SSL 인증서를 구성하는 방법에 대한 질문이 있다면 [IIS에서 SSL을 설정하는 방법](/iis/manage/configuring-security/how-to-set-up-ssl-on-iis) 문서를 참조하세요.

웹 서비스 SDK는 TLS/SSL 인증서로 보호되어야 합니다. 이 작업을 위해 자체 서명된 인증서를 사용해도 됩니다. TLS 연결을 시작할 때 해당 인증서를 신뢰할 수 있도록 사용자 포털 웹 서버에 있는 로컬 컴퓨터 계정의 "신뢰할 수 있는 루트 인증 기관" 저장소로 인증서를 가져옵니다.

![MFA 서버 구성 설정 웹 서비스 SDK](./media/howto-mfaserver-deploy-userportal/sdk.png)

## <a name="deploy-the-user-portal-on-the-same-server-as-the-azure-multi-factor-authentication-server"></a>Azure Multi-Factor Authentication 서버와 동일한 서버의 사용자 포털 배포

Azure Multi-Factor Authentication 서버와 **동일한 서버**에 사용자 포털을 설치하려면 다음 필수 요건이 충족되어야 합니다.

* ASP.NET 및 IIS 6 메타 베이스 호환성(IIS 7 이상)을 포함하는 IIS
* 해당되는 경우 컴퓨터 및 도메인에 대해 관리자 권한이 있는 계정 계정에는 Active Directory 보안 그룹을 만들 수 있는 사용 권한이 필요합니다.
* TLS/SSL 인증서를 사용하여 사용자 포털을 보호합니다.
* TLS/SSL 인증서를 사용하여 Azure Multi-Factor Authentication 웹 서비스 SDK를 보호합니다.

사용자 포털을 배포하려면 다음 단계를 따르세요.

1. Azure Multi-Factor Authentication 서버 콘솔을 열고, 왼쪽 메뉴에 있는 **사용자 포털** 아이콘을 클릭한 후 **사용자 포털 설치**를 클릭합니다.
2. 어떤 이유로든 변경해야 하는 경우가 아니면 기본값을 사용하여 설치를 완료합니다.
3. IIS에서 사이트에 TLS/SSL 인증서 바인딩

   > [!NOTE]
   > 이 TLS/SSL 인증서는 일반적으로 공개적으로 서명된 TLS/SSL 인증서입니다.

4. 원하는 컴퓨터에서 웹 브라우저를 열고 사용자 포털이 설치된 URL(예: `https://mfa.contoso.com/MultiFactorAuth`)로 이동합니다. 인증서 경고 또는 오류가 표시되지 않는지 확인합니다.

![MFA 서버 사용자 포털 설치](./media/howto-mfaserver-deploy-userportal/install.png)

IIS 서버에 TLS/SSL 인증서를 구성하는 방법에 대한 질문이 있다면 [IIS에서 SSL을 설정하는 방법](/iis/manage/configuring-security/how-to-set-up-ssl-on-iis) 문서를 참조하세요.

## <a name="deploy-the-user-portal-on-a-separate-server"></a>별도 서버에 사용자 포털 배포

Azure Multi-Factor Authentication 서버가 실행되고 있는 서버가 인터넷에 연결되지 않은 경우 **별도의 인터넷 연결 서버**에 사용자 포털을 설치해야 합니다.

조직이 확인 방법 중 하나로 Microsoft Authenticator 앱을 사용하여 자체 서버에 사용자 포털을 배포하려는 경우 다음 요구 사항을 완료합니다.

* v6.0 이상의 Azure Multi-Factor Authentication 서버를 사용합니다.
* Microsoft IIS(인터넷 정보 서비스) 6.x 이상이 실행되는 인터넷 연결 웹 서버에 사용자 포털을 설치합니다.
* IIS 6.x를 사용하는 경우 ASP.NET v2.0.50727이 설치되고, 등록되고, **허용**으로 설정되어야 합니다.
* IIS 7.x 이상, IIS을 사용하는 경우 기본 인증, ASP.NET 및 IIS 6 메타 베이스 호환성을 포함합니다.
* TLS/SSL 인증서를 사용하여 사용자 포털을 보호합니다.
* TLS/SSL 인증서를 사용하여 Azure Multi-Factor Authentication 웹 서비스 SDK를 보호합니다.
* 사용자 포털이 TLS/SSL을 통해 Azure Multi-Factor Authentication 웹 서비스 SDK에 연결될 수 있어야 합니다.
* 사용자 포털이 "PhoneFactor Admins" 보안 그룹에서 서비스 계정의 자격 증명을 사용하여 Azure Multi-Factor Authentication 웹 서비스 SDK에 인증해야 합니다. 이 서비스 계정 및 그룹은 Azure Multi-Factor Authentication 서버가 도메인 연결된 서버에서 실행 중인 경우 Active Directory에 존재해야 합니다. 도메인에 연결되지 않은 경우에는 이 서비스 계정 및 그룹이 Azure Multi-Factor Authentication 서버에 로컬로 존재합니다.

Azure Multi-Factor Authentication 서버 이외의 서버에 사용자 포털을 설치하려면 다음 단계가 필요합니다.

1. **MFA 서버에서** 설치 경로(예: C:\Program Files\multi-factor Authentication Server)를 찾고, **MultiFactorAuthenticationUserPortalSetup64** 파일을 설치할 인터넷 연결 서버에 액세스할 수 있는 위치에 복사합니다.
2. **인터넷 연결 웹 서버에서** MultiFactorAuthenticationUserPortalSetup64 설치 파일을 관리자로 실행하고, 필요한 경우 사이트를 변경하고, 원하는 경우 가상 디렉터리를 짧은 이름으로 변경합니다.
3. IIS에서 사이트에 TLS/SSL 인증서를 바인딩합니다.

   > [!NOTE]
   > 이 TLS/SSL 인증서는 일반적으로 공개적으로 서명된 TLS/SSL 인증서입니다.

4. **C:\inetpub\wwwroot\MultiFactorAuth**로 이동합니다.
5. 메모장에서 Web.Config 파일을 편집합니다.

    * **"USE_WEB_SERVICE_SDK"** 키를 찾고 **value="false"** 를 **value="true"** 로 변경합니다.
    * **"WEB_SERVICE_SDK_AUTHENTICATION_USERNAME"** 키를 찾고 **value=""** 를 **value="DOMAIN\User"** 로 변경합니다. 여기서 DOMAIN\User는 "PhoneFactor Admins" 그룹의 일부인 서비스 계정입니다.
    * **"WEB_SERVICE_SDK_AUTHENTICATION_PASSWORD"** 키를 찾고 **value=""** 를 **value="Password"** 로 변경합니다. 여기서 암호는 이전 줄에 입력한 서비스 계정의 암호입니다.
    * `https://www.contoso.com/MultiFactorAuthWebServiceSdk/PfWsSdk.asmx` 값을 확인하고 이 자리 표시자 URL을 2단계에서 설치한 웹 서비스 SDK URL로 변경합니다.
    * Web.Config 파일을 저장하고 메모장을 닫습니다.

6. 원하는 컴퓨터에서 웹 브라우저를 열고 사용자 포털이 설치된 URL(예: `https://mfa.contoso.com/MultiFactorAuth`)로 이동합니다. 인증서 경고 또는 오류가 표시되지 않는지 확인합니다.

IIS 서버에 TLS/SSL 인증서를 구성하는 방법에 대한 질문이 있다면 [IIS에서 SSL을 설정하는 방법](/iis/manage/configuring-security/how-to-set-up-ssl-on-iis) 문서를 참조하세요.

## <a name="configure-user-portal-settings-in-the-azure-multi-factor-authentication-server"></a>Azure Multi-Factor Authentication 서버에서 사용자 포털 설정 구성

사용자 포털이 설치되었으므로 해당 포털을 사용하도록 Azure Multi-Factor Authentication 서버를 구성해야 합니다.

1. Azure Multi-Factor Authentication 서버 콘솔에서 **사용자 포털** 아이콘을 클릭합니다. 설정 탭에서 사용자 포털 URL 텍스트 상자에 **사용자 포털 URL**을 입력합니다. 이 URL은 메일 기능이 사용되도록 설정된 경우 Azure Multi-Factor Authentication 서버로 가져올 때 사용자에게 전송되는 메일에 포함됩니다.
2. 사용자 포털에서 사용하려는 설정을 선택합니다. 예를 들어 사용자가 자신의 인증 방법을 선택할 수 있도록 허용되면, 사용자가 선택할 수 있는 방법과 함께 **사용자가 방법을 선택할 수 있도록 허용** 옵션이 선택되어 있는지 확인합니다.
3. **관리자** 탭에서 관리자가 누구여야 하는지 정의합니다. 추가/편집 상자에서 확인란 및 드롭다운을 사용하여 세분화된 관리 권한을 만들 수 있습니다.

선택적 구성:

- **보안 질문** - 환경에을 승인된 보안 질문 및 표시되는 언어를 정의합니다.
- **전달된 세션** - MFA를 사용하여 양식 기반 웹 사이트에서 사용자 포털 통합을 구성합니다.
- **신뢰할 수 있는 IP** - 사용자가 신뢰할 수 있는 IP 또는 범위 목록에서 인증하는 경우 MFA를 건너뛸 수 있도록 합니다.

![MFA 서버 사용자 포털 구성](./media/howto-mfaserver-deploy-userportal/config.png)

Azure Multi-Factor Authentication 서버에서는 사용자 포털에 대한 몇 가지 옵션을 제공합니다. 다음 테이블에서는 이러한 옵션 및 각 옵션의 용도를 설명하는 목록을 제공합니다.

| 사용자 포털 설정 | Description |
|:--- |:--- |
| User Portal URL(사용자 포털 URL) | 포털이 호스트되는 URL을 입력합니다. |
| Primary authentication(기본 인증) | 포털에 로그인할 때 사용할 인증 형식을 지정합니다. Windows, Radius 또는 LDAP 인증 중 하나입니다. |
| Allow users to log in(로그인 허용) | 사용자 포털의 로그인 페이지에서 사용자 이름 및 암호를 입력할 수 있습니다. 이 옵션을 선택하지 않으면 상자는 회색으로 표시됩니다. |
| Allow user enrollment(등록 허용) | 사용자가 설치 화면에서 전화 번호와 같은 추가 정보를 입력하여 Multi-Factor Authentication에 등록할 수 있습니다. 백업 휴대폰 프롬프트를 통해 보조 전화 번호를 지정할 수 있습니다. 타사 OATH 토큰 프롬프트를 통해 타사 OATH 토큰을 지정할 수 있습니다. |
| Allow users to initiate One-Time Bypass(일회성 바이패스 시작 허용) | 일회성 바이패스를 시작할 수 있습니다. 사용자가 이 옵션을 설정하면 다음번에 로그인할 때 적용됩니다. 바이패스(초) 프롬프트의 상자에서 기본값 300초를 변경할 수 있습니다. 변경하지 않으면 일회성 바이패스는 300초가 지나면 만료됩니다. |
| Allow users to select method(방법 선택 허용) | 사용자가 자신의 기본 연락 방법을 지정할 수 있습니다. 전화 통화, 문자 메시지, 모바일 앱 또는 OATH 토큰 옵션 중 방법을 선택할 수 있습니다. |
| Allow users to select language(언어 선택 허용) | 전화 통화, 문자 메시지, 모바일 앱 또는 OATH 토큰에 사용되는 언어를 변경할 수 있습니다. |
| Allow users to activate mobile app(모바일 앱 활성화 허용) | 서버에서 사용되는 모바일 앱 활성화 프로세스를 완료하기 위한 활성화 코드를 생성할 수 있습니다.  앱을 활성화할 수 있는 디바이스 수를 1부터 10 사이로 설정할 수도 있습니다. |
| Use security questions for fallback(대체 방법으로 보안 질문 사용) | 2단계 검증에 실패한 경우 보안 질문을 허용합니다. 답변이 맞아야 하는 보안 질문의 수를 지정할 수 있습니다. |
| Allow users to associate third-party OATH token(타사 OATH 토큰 연결 허용) | 타사 OATH 토큰을 지정할 수 있습니다. |
| Use OATH token for fallback(대체 방법으로 OATH 토큰 사용) | 2단계 검증이 실패한 경우 OATH 토큰을 사용할 수 있도록 합니다. 세션 제한 시간(분)을 지정할 수도 있습니다. |
| 로깅 사용 | 사용자 포털에서 로깅을 사용합니다. 로그 파일은 C:\Program Files\Multi-Factor Authentication Server\Logs에 있습니다. |

> [!IMPORTANT]
> 2019년 3월부터 무료/평가판 Azure AD 테넌트의 MFA 서버 사용자는 전화 통화 옵션을 사용할 수 없습니다. SMS 메시지는 이 변경의 영향을 받지 않습니다. 유료 Azure AD 테넌트의 사용자는 전화 통화를 계속 사용할 수 있습니다. 이 변경 내용은 무료/평가판 Azure AD 테넌트에만 영향을 줍니다.

이러한 설정을 사용하도록 설정하고 사용자 포털에 로그인하면 해당 설정이 포털에서 사용자에게 표시됩니다.

![사용자 포털을 사용하여 MFA 서버 계정 관리](./media/howto-mfaserver-deploy-userportal/portalsettings.png)

### <a name="self-service-user-enrollment"></a>셀프 서비스 사용자 등록

사용자가 로그인하고 등록할 수 있게 하려면 설정 탭에서 **사용자 로그인 허용**과 **사용자 등록 허용** 옵션을 선택해야 합니다. 선택한 설정은 사용자 로그인 환경에 영향을 줍니다.

예를 들어 사용자 포털에 처음으로 로그인하면 [Azure Multi-Factor Authentication 사용자 설정] 페이지로 이동합니다. Azure Multi-Factor Authentication 구성 방식에 따라 사용자는 인증 방법을 선택할 수 있습니다.

음성 통화 확인 방법을 선택하거나 해당 방법을 사용하도록 미리 구성했으면 해당하는 경우 페이지에 사용자의 기본 전화 번호와 내선 번호를 입력하라는 메시지가 표시됩니다. 백업 전화 번호도 입력할 수도 있습니다.

![기본 및 백업 전화 번호 등록](./media/howto-mfaserver-deploy-userportal/backupphone.png)

사용자가 PIN을 사용해서 인증해야 하는 경우 페이지에 PIN을 만들라는 메시지가 표시됩니다. 전화 번호와 PIN(해당되는 경우)을 입력한 후 **지금 전화로 인증**이라는 단추를 클릭합니다. Azure Multi-Factor Authentication은 사용자의 기본 전화 번호로 전화 통화 확인을 수행합니다. 사용자는 전화 통화에 응답하고 PIN(해당되는 경우)을 입력하고 # 키를 눌러야 자체 등록 프로세스의 다음 단계로 이동할 수 있습니다.

사용자가 텍스트 문자 확인 방법을 선택하거나 해당 방법을 사용하도록 미리 구성했으면 페이지에 휴대폰 번호를 입력하라는 메시지가 표시됩니다. 사용자가 PIN을 사용해서 인증해야 하는 경우 페이지에 PIN을 입력하라는 메시지가 표시됩니다.  전화 번호와 PIN(해당되는 경우)을 입력한 후 **지금 문자로 인증**이라는 단추를 클릭합니다. Azure Multi-Factor Authentication은 사용자의 휴대폰으로 SMS 확인을 수행합니다. 사용자는 일회용 암호(OTP)를 포함한 텍스트 메시지를 받은 다음 적용할 수 있는 경우 해당 OTP와 PIN을 사용하여 메시지에 회신합니다.

![SMS를 사용한 사용자 포털 인증](./media/howto-mfaserver-deploy-userportal/text.png)

사용자가 Mobile App 확인 방법을 선택하면 페이지에는 해당 디바이스에서 Microsoft Authenticator 앱을 설치하고 활성화 코드를 생성하라는 메시지가 표시됩니다. 앱을 설치한 후 [활성화 코드 생성] 단추를 클릭합니다.

> [!NOTE]
> Microsoft 인증 앱을 사용하려면 해당 디바이스에 푸시 알림을 사용하도록 설정해야 합니다.

그러면 페이지에 활성화 코드와 URL이 바코드 그림과 함께 표시됩니다. 사용자가 PIN을 사용해서 인증해야 하는 경우 페이지에 추가로 PIN을 입력하라는 메시지가 표시됩니다. Microsoft 인증 앱에 활성화 코드와 URL을 입력하거나 바코드 스캐너를 사용하여 바코드 그림을 스캔하고 [활성화] 단추를 클릭합니다.

활성화를 완료한 후에 **지금 인증**이라는 단추를 클릭합니다. Azure Multi-Factor Authentication은 사용자의 모바일 앱에서 인증을 수행합니다. 사용자는 PIN(해당되는 경우)을 입력하고 모바일 앱에서 Authenticate(인증) 단추를 눌러야 자체 등록 프로세스의 다음 단계로 이동할 수 있습니다.

관리자가 보안 질문 및 답변을 수집하도록 Azure Multi-Factor Authentication 서버를 구성했으면 보안 질문 페이지로 이동됩니다. 사용자는 4개의 보안 질문을 선택하고 해당 질문에 대한 답변을 입력해야 합니다.

![사용자 포털 보안 질문](./media/howto-mfaserver-deploy-userportal/secq.png)

이제 사용자 자체 등록이 완료되어 사용자가 사용자 포털에 로그인됩니다. 해당 방법을 변경하도록 관리자가 허용하는 경우 사용자는 나중에 언제든지 사용자 포털에 다시 로그인하여 전화 번호, PIN, 인증 방법 및 보안 질문을 변경할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [Azure Multi-Factor Authentication 서버 모바일 앱 웹 서비스 배포](howto-mfaserver-deploy-mobileapp.md)