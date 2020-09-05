---
title: Azure AD 응용 프로그램 프록시 질문과 대답 | Microsoft Docs
description: Azure AD 응용 프로그램 프록시을 사용 하 여 내부 온-프레미스 응용 프로그램을 원격 사용자에 게 게시 하는 방법에 대 한 FAQ (질문과 대답)에 대해 알아봅니다.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: reference
ms.date: 07/23/2020
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: edf51dad768e8d8b5ea5dc6c1eff88f43f0f6b70
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88589166"
---
# <a name="active-directory-azure-ad-application-proxy-frequently-asked-questions"></a>Active Directory (Azure AD) 응용 프로그램 프록시에 대 한 질문과 대답

이 페이지는 Azure Active Directory (Azure AD) 응용 프로그램 프록시에 대 한 자주 묻는 질문에 답변 합니다.

## <a name="enabling-azure-ad-application-proxy"></a>Azure AD 애플리케이션 프록시 사용

### <a name="what-license-is-required-to-use-azure-ad-application-proxy"></a>Azure AD 응용 프로그램 프록시을 사용 하는 데 필요한 라이선스는 무엇 인가요?

Azure AD 응용 프로그램 프록시을 사용 하려면 Azure AD Premium P1 또는 P2 라이선스가 있어야 합니다. 라이선스에 대 한 자세한 내용은 [Azure Active Directory 가격 책정](https://azure.microsoft.com/pricing/details/active-directory/) 을 참조 하세요.

### <a name="what-happens-to-azure-ad-application-proxy-in-my-tenant-if-my-license-expires"></a>내 라이선스가 만료 된 경우 내 테 넌 트의 Azure AD 응용 프로그램 프록시는 어떻게 되나요?
라이선스가 만료 되 면 응용 프로그램 프록시가 자동으로 사용 하지 않도록 설정 됩니다. 응용 프로그램 정보는 최대 1 년 동안 저장 됩니다.

### <a name="why-is-the-enable-application-proxy-button-grayed-out"></a>"응용 프로그램 프록시 사용 단추가 회색으로 표시 되는 이유는 무엇 인가요?

적어도 Azure AD Premium P1 또는 P2 라이선스와 Azure AD 응용 프로그램 프록시 커넥터가 설치 되어 있는지 확인 하세요. 첫 번째 커넥터를 성공적으로 설치 하면 Azure AD 응용 프로그램 프록시 서비스가 자동으로 사용 하도록 설정 됩니다.

## <a name="connector-configuration"></a>커넥터 구성

### <a name="can-application-proxy-connector-services-run-in-a-different-user-context-than-the-default"></a>응용 프로그램 프록시 커넥터 서비스가 기본값과 다른 사용자 컨텍스트에서 실행 될 수 있나요?

아니요,이 시나리오는 지원 되지 않습니다. 기본 설정은 다음과 같습니다.

- Microsoft AAD 응용 프로그램 프록시 커넥터-WAPCSvc-네트워크 서비스
- Microsoft AAD Application Proxy Connector Updater WAPCUpdaterSvc-NT 권한 없음

### <a name="can-a-guest-user-with-the-global-administrator-or-the-application-administrator-role-register-the-connector-for-the-guest-tenant"></a>전역 관리자 또는 응용 프로그램 관리자 역할이 있는 게스트 사용자가 (게스트) 테 넌 트에 대해 커넥터를 등록할 수 있나요?

아니요. 현재는 가능 하지 않습니다. 등록 시도는 항상 사용자의 홈 테 넌 트에 적용 됩니다.

### <a name="my-back-end-application-is-hosted-on-multiple-web-servers-and-requires-user-session-persistence-stickiness-how-can-i-achieve-session-persistence"></a>백 엔드 응용 프로그램은 여러 웹 서버에서 호스팅되며 사용자 세션 지 속성 (유지)이 필요 합니다. 세션 유지를 실현 하려면 어떻게 해야 하나요? 

권장 사항은 [응용 프로그램 프록시 커넥터 및 응용 프로그램의 고가용성 및 부하 분산](application-proxy-high-availability-load-balancing.md)을 참조 하세요.

### <a name="is-tls-termination-tlshttps-inspection-or-acceleration-on-traffic-from-the-connector-servers-to-azure-supported"></a>커넥터 서버에서 Azure로의 트래픽에 대 한 TLS 종료 (TLS/HTTPS 검사 또는 가속)가 지원 되나요?

응용 프로그램 프록시 커넥터는 Azure에 대 한 인증서 기반 인증을 수행 합니다. Tls 종료 (TLS/HTTPS 검사 또는 가속)는이 인증 방법을 중단 하며 지원 되지 않습니다. 커넥터에서 Azure로의 트래픽은 TLS 종료를 수행 하는 모든 장치를 무시 해야 합니다.  

### <a name="is-tls-12-required-for-all-connections"></a>모든 연결에 TLS 1.2이 필요 한가요?
예. 고객에게 동급 최고의 암호화를 제공하기 위해 애플리케이션 프록시 서비스는 TLS 1.2 프로토콜에 대한 액세스만 제한합니다. 이러한 변경 내용은 2019년 8월 31일 이후에 점진적으로 롤아웃되고 유효합니다. TLS 1.2를 사용하여 애플리케이션 프록시 서비스에 대한 연결을 유지하도록 모든 클라이언트-서버 및 브라우저-서버 조합을 업데이트해야 합니다. 여기에는 사용자가 애플리케이션 프록시를 통해 게시된 애플리케이션에 액세스할 때 사용하는 클라이언트도 포함됩니다. [Office 365의 TLS 1.2](https://docs.microsoft.com/microsoft-365/compliance/prepare-tls-1.2-in-office-365)에서 유용한 참고 자료 및 리소스를 확인하세요.

### <a name="can-i-place-a-forward-proxy-device-between-the-connector-servers-and-the-back-end-application-server"></a>커넥터 서버와 백 엔드 응용 프로그램 서버 간에 전달 프록시 장치를 놓을 수 있나요?
예,이 시나리오는 커넥터 버전 1.5.1526.0부터 지원 됩니다. [기존 온-프레미스 프록시 서버 작업을](application-proxy-configure-connectors-with-proxy-servers.md)참조 하세요.

### <a name="should-i-create-a-dedicated-account-to-register-the-connector-with-azure-ad-application-proxy"></a>Azure AD 응용 프로그램 프록시을 사용 하 여 커넥터를 등록 하기 위한 전용 계정을 만들어야 하나요?

이유가 없습니다. 모든 전역 관리자 또는 응용 프로그램 관리자 계정이 작동 합니다. 설치 중에 입력 한 자격 증명은 등록 프로세스 후에 사용 되지 않습니다. 대신, 해당 지점에서 인증에 사용 되는 인증서가 커넥터에 발급 됩니다.

### <a name="how-can-i-monitor-the-performance-of-the-azure-ad-application-proxy-connector"></a>Azure AD 응용 프로그램 프록시 커넥터의 성능을 모니터링 하려면 어떻게 해야 하나요?

커넥터와 함께 설치 되는 성능 모니터 카운터가 있습니다. 이 내용을 확인하려면 다음을 수행하십시오.  

1. **시작**을 선택 하 고 "Perfmon"을 입력 한 다음 enter 키를 누릅니다.
2. **성능 모니터** 를 선택 하 고 녹색 **+** 아이콘을 클릭 합니다.
3. 모니터링 하려는 **MICROSOFT AAD 응용 프로그램 프록시 커넥터** 카운터를 추가 합니다.

### <a name="does-the-azure-ad-application-proxy-connector-have-to-be-on-the-same-subnet-as-the-resource"></a>Azure AD 응용 프로그램 프록시 커넥터가 리소스와 동일한 서브넷에 있어야 하나요?

커넥터가 동일한 서브넷에 있을 필요는 없습니다. 그러나 리소스 및 필요한 네트워크 연결 (리소스에 대 한 라우팅, 리소스에서 열린 포트 등)에 대 한 이름 확인 (DNS, 호스트 파일)이 필요 합니다. 권장 사항은 Azure Active Directory 응용 프로그램 프록시를 [사용할 때 네트워크 토폴로지 고려 사항](application-proxy-network-topology.md)을 참조 하세요.

### <a name="what-versions-of-windows-server-can-i-install-a-connector-on"></a>커넥터를 설치할 수 있는 Windows Server 버전은 무엇입니까?
응용 프로그램 프록시에는 Windows Server 2012 R2 이상이 필요 합니다. 현재 Windows Server 2019에 대 한 HTTP2에는 제한이 있습니다. Windows Server 2019에서 커넥터를 성공적으로 사용 하려면 다음 레지스트리 키를 추가 하 고 서버를 다시 시작 해야 합니다.
    ```
    HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Internet Settings\WinHttp\EnableDefaultHttp2 (DWORD) Value: 0 
    ```


## <a name="application-configuration"></a>애플리케이션 구성

### <a name="i-am-receiving-an-error-about-an-invalid-certificate-or-possible-wrong-password"></a>잘못 된 인증서 또는 잘못 된 암호에 대 한 오류를 수신 하 고 있습니다.

SSL 인증서를 업로드 한 후에는 포털에서 "잘못 된 인증서, 잘못 된 암호를 사용할 수 있습니다." 라는 메시지가 표시 됩니다.

이 오류를 해결 하기 위한 몇 가지 팁은 다음과 같습니다.
- 인증서의 문제를 확인 합니다. 로컬 컴퓨터에 설치 합니다. 문제가 발생 하지 않으면 인증서가 양호 합니다.
- 암호에 특수 문자가 포함 되어 있지 않은지 확인 합니다. 테스트를 위해 암호는 문자 0-9, a-z 및 a-z만 포함 해야 합니다.
- Microsoft 소프트웨어 키 저장소 공급자를 사용 하 여 인증서를 만든 경우 RSA 알고리즘을 사용 해야 합니다.

### <a name="what-is-the-length-of-the-default-and-long-back-end-timeout-can-the-timeout-be-extended"></a>기본 및 "긴" 백 엔드 시간 제한의 길이는 어떻게 되나요? 제한 시간을 연장할 수 있나요?

기본 길이는 85 초입니다. "Long" 설정은 180 초입니다. 제한 시간 제한은 확장할 수 없습니다.

### <a name="how-do-i-change-the-landing-page-my-application-loads"></a>응용 프로그램에서 로드 하는 방문 페이지를 변경 어떻게 할까요??

응용 프로그램 등록 페이지에서 홈페이지 URL을 방문 페이지의 원하는 외부 URL로 변경할 수 있습니다. 응용 프로그램이 내 앱 또는 Office 365 포털에서 시작 되 면 지정 된 페이지가 로드 됩니다. 구성 단계는 Azure를 [사용 하 여 게시 된 앱에 대 한 사용자 지정 홈 페이지 설정](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-custom-home-page) 을 참조 하세요 AD 응용 프로그램 프록시

### <a name="can-only-iis-based-applications-be-published-what-about-web-applications-running-on-non-windows-web-servers-does-the-connector-have-to-be-installed-on-a-server-with-iis-installed"></a>IIS 기반 응용 프로그램만 게시할 수 있나요? Windows가 아닌 웹 서버에서 실행 되는 웹 응용 프로그램은 어떻게 되나요? IIS가 설치 된 서버에 커넥터를 설치 해야 하나요?

아니요, 게시 된 응용 프로그램에 대 한 IIS 요구 사항은 없습니다. Windows Server가 아닌 서버에서 실행 되는 웹 응용 프로그램을 게시할 수 있습니다. 그러나 웹 서버에서 Negotiate (Kerberos 인증)를 지원 하는지 여부에 따라 비 Windows 서버에서 사전 인증을 사용 하지 못할 수도 있습니다. 커넥터가 설치 된 서버에는 IIS가 필요 하지 않습니다.

### <a name="can-i-configure-application-proxy-to-add-the-hsts-header"></a>HSTS 헤더를 추가 하도록 응용 프로그램 프록시를 구성할 수 있나요?
응용 프로그램 프록시는 HTTPS 응답에 HTTP Strict-Transport-보안 헤더를 자동으로 추가 하지 않지만 게시 된 응용 프로그램에서 보낸 원래 응답에 있으면 헤더를 유지 관리 합니다. 이 기능을 사용 하도록 설정 하는 설정을 증명 하는 것은 로드맵에 있습니다. 이를 응답에 추가할 수 있는 미리 보기에 관심이 있는 경우에는에 aadapfeedback@microsoft.com 대 한 자세한 내용을 확인 하세요.

## <a name="integrated-windows-authentication"></a>Windows 통합 인증

### <a name="when-should-i-use-the-principalsallowedtodelegatetoaccount-method-when-setting-up-kerberos-constrained-delegation-kcd"></a>KCD (Kerberos 제한 위임)를 설정할 때 PrincipalsAllowedToDelegateToAccount 메서드를 사용 해야 하는 경우는 언제 인가요?

PrincipalsAllowedToDelegateToAccount 메서드는 커넥터 서버가 웹 응용 프로그램 서비스 계정과 다른 도메인에 있을 때 사용 됩니다. 리소스 기반 제한 위임을 사용 해야 합니다.
커넥터 서버와 웹 응용 프로그램 서비스 계정이 동일한 도메인에 있는 경우 사용자 및 컴퓨터 Active Directory를 사용 하 여 각 커넥터 컴퓨터 계정에 대 한 위임 설정을 구성 하 여 대상 SPN에 위임할 수 있습니다.

커넥터 서버와 웹 응용 프로그램 서비스 계정이 서로 다른 도메인에 있는 경우 리소스 기반 위임이 사용 됩니다. 위임 권한은 대상 웹 서버 및 웹 응용 프로그램 서비스 계정에서 구성 됩니다. 이 제한 된 위임 방법은 비교적 새로운 방법입니다. 메서드는 리소스 (웹 서비스) 소유자가 위임할 수 있는 컴퓨터 및 서비스 계정을 제어 하도록 허용 하 여 도메인 간 위임을 지 원하는 Windows Server 2012에서 도입 되었습니다. 이 구성에 도움이 되는 UI가 없으므로 PowerShell을 사용 해야 합니다.
자세한 내용은 [응용 프로그램 프록시를 사용 하 여 Kerberos 제한 된 위임 이해](https://aka.ms/kcdpaper)백서를 참조 하십시오.

### <a name="does-ntlm-authentication-work-with-azure-ad-application-proxy"></a>NTLM 인증은 Azure AD 응용 프로그램 프록시에서 작동 하나요?

NTLM 인증은 사전 인증 또는 Single Sign-On 방법으로 사용할 수 없습니다. NTLM 인증은 클라이언트와 게시 된 웹 응용 프로그램 간에 직접 협상할 수 있는 경우에만 사용할 수 있습니다. NTLM 인증을 사용 하면 일반적으로 브라우저에 로그인 프롬프트가 표시 됩니다.

## <a name="pass-through-authentication"></a>통과 인증

### <a name="can-i-use-conditional-access-policies-for-applications-published-with-pass-through-authentication"></a>통과 인증으로 게시 된 응용 프로그램에 대 한 조건부 액세스 정책을 사용할 수 있나요?

Azure AD에서 성공적으로 미리 인증 된 사용자에 대해서만 조건부 액세스 정책이 적용 됩니다. 통과 인증은 Azure AD 인증을 트리거하지 않으므로 조건부 액세스 정책을 적용할 수 없습니다. 통과 인증을 사용 하는 경우 온-프레미스 서버에서 MFA 정책을 구현 하거나 (가능한 경우) Azure AD 응용 프로그램 프록시에 대 한 사전 인증을 사용 하도록 설정 해야 합니다.

### <a name="can-i-publish-a-web-application-with-client-certificate-authentication-requirement"></a>클라이언트 인증서 인증 요구 사항으로 웹 응용 프로그램을 게시할 수 있나요?

아니요,이 시나리오는 응용 프로그램 프록시가 TLS 트래픽을 종료 하기 때문에 지원 되지 않습니다.  

## <a name="remote-desktop-gateway-publishing"></a>원격 데스크톱 게이트웨이 게시

### <a name="how-can-i-publish-remote-desktop-gateway-over-azure-ad-application-proxy"></a>Azure AD 응용 프로그램 프록시를 통해 원격 데스크톱 게이트웨이을 게시 하려면 어떻게 해야 하나요?

[Azure AD 응용 프로그램 프록시를 사용 하 여 원격 데스크톱 게시](application-proxy-integrate-with-remote-desktop-services.md)를 참조 하세요.

### <a name="can-i-use-kerberos-constrained-delegation-single-sign-on---windows-integrated-authentication-in-the-remote-desktop-gateway-publishing-scenario"></a>원격 데스크톱 게이트웨이 게시 시나리오에서 Kerberos 제한 된 위임 (Single Sign-on-Windows 통합 인증)을 사용할 수 있나요?

아니요,이 시나리오는 지원 되지 않습니다.  

### <a name="my-users-dont-use-internet-explorer-11-and-the-pre-authentication-scenario-doesnt-work-for-them-is-this-expected"></a>사용자는 Internet Explorer 11을 사용 하지 않으며 사전 인증 시나리오가 작동 하지 않습니다. 이것은 예상된 동작인가요?

예, 예상 됩니다. 사전 인증 시나리오에는 타사 브라우저에서 지원 되지 않는 ActiveX 컨트롤이 필요 합니다.

### <a name="is-the-remote-desktop-web-client-html5-supported"></a>HTML5 (원격 데스크톱 웹 클라이언트)가 지원 되나요?

예,이 시나리오는 현재 공개 미리 보기로 제공 됩니다. [Azure AD 응용 프로그램 프록시를 사용 하 여 원격 데스크톱 게시](application-proxy-integrate-with-remote-desktop-services.md)를 참조 하세요.

### <a name="after-i-configured-the-pre-authentication-scenario-i-realized-that-the-user-has-to-authenticate-twice-first-on-the-azure-ad-sign-in-form-and-then-on-the-rdweb-sign-in-form-is-this-expected-how-can-i-reduce-this-to-one-sign-in"></a>사전 인증 시나리오를 구성한 후에는 먼저 Azure AD 로그인 양식에서 사용자를 두 번 인증 한 다음 RDWeb 로그인 양식으로 인증 해야 합니다. 이것은 예상된 동작인가요? 이를 한 번의 로그인으로 줄이려면 어떻게 해야 하나요?

예, 예상 됩니다. 사용자의 컴퓨터가 Azure AD에 가입 되어 있으면 사용자가 자동으로 Azure AD에 로그인 합니다. 사용자는 RDWeb 로그인 양식에만 자격 증명을 제공 해야 합니다.

## <a name="sharepoint-publishing"></a>SharePoint 게시

### <a name="how-can-i-publish-sharepoint-over-azure-ad-application-proxy"></a>Azure AD 응용 프로그램 프록시을 통해 SharePoint를 게시 하려면 어떻게 하나요?

[Azure AD 응용 프로그램 프록시를 사용 하 여 SharePoint에 원격 액세스 사용](application-proxy-integrate-with-sharepoint-server.md)을 참조 하세요.

### <a name="can-i-use-the-sharepoint-mobile-app-ios-android-to-access-a-published-sharepoint-server"></a>SharePoint 모바일 앱 (iOS/Android)을 사용 하 여 게시 된 SharePoint 서버에 액세스할 수 있나요?

[SharePoint 모바일 앱](https://docs.microsoft.com/sharepoint/administration/supporting-the-sharepoint-mobile-apps-online-and-on-premises) 은 현재 Azure Active Directory 사전 인증을 지원 하지 않습니다.

## <a name="active-directory-federation-services-ad-fs-publishing"></a>Active Directory Federation Services (AD FS) 게시 

### <a name="can-i-use-azure-ad-application-proxy-as-ad-fs-proxy-like-web-application-proxy"></a>Azure AD 응용 프로그램 프록시을 AD FS 프록시 (예: 웹 응용 프로그램 프록시)로 사용할 수 있나요?

아니요. Azure AD 응용 프로그램 프록시는 Azure AD와 함께 사용할 수 있도록 설계 되었으며 AD FS 프록시 역할을 하는 요구 사항을 충족 하지 않습니다.

## <a name="websocket"></a>서버당

### <a name="does-websocket-support-work-for-applications-other-than-qliksense"></a>WebSocket은 QlikSense 이외의 응용 프로그램에 대해 작업을 지원 하나요?

현재 WebSocket 프로토콜 지원은 아직 공개 미리 보기 상태 이며 다른 응용 프로그램에서 작동 하지 않을 수 있습니다. 일부 고객의 경우 WebSocket 프로토콜을 사용 하 여 다른 응용 프로그램과의 성공을 혼합 했습니다. 이러한 시나리오를 테스트 하는 경우 결과를 듣고 싶습니다. 에서 사용자 의견을 보내 주시기 바랍니다 aadapfeedback@microsoft.com .

Windows 관리 센터 (WAC) 또는 원격 데스크톱 웹 클라이언트 (HTML5)의 기능 (Eventlogs, PowerShell 및 원격 데스크톱 서비스)은 현재 Azure AD 응용 프로그램 프록시를 통해 작동 하지 않습니다.

## <a name="link-translation"></a>링크 변환

### <a name="does-using-link-translation-affect-performance"></a>링크 변환을 사용 하면 성능에 영향을 미칩니까?

예. 링크 변환은 성능에 영향을 줍니다. 응용 프로그램 프록시 서비스는 응용 프로그램에서 하드 코드 된 링크를 검색 하 여 해당 링크를 사용자에 게 제공 하기 전에 게시 된 해당 외부 Url로 바꿉니다. 

최상의 성능을 위해 [사용자 지정 도메인](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-custom-domain)을 구성 하 여 동일한 내부 및 외부 url을 사용 하는 것이 좋습니다. 사용자 지정 도메인을 사용 하는 것이 불가능 한 경우에는 내 앱 보안 로그인 확장 또는 모바일의 Microsoft Edge 브라우저를 사용 하 여 링크 변환 성능을 향상 시킬 수 있습니다. [Azure AD 응용 프로그램 프록시로 게시 된 앱에 대 한 하드 코딩 된 링크 리디렉션](application-proxy-configure-hard-coded-link-translation.md)을 참조 하세요.

## <a name="wildcards"></a>와일드카드

### <a name="how-do-i-use-wildcards-to-publish-two-applications-with-the-same-custom-domain-name-but-with-different-protocols-one-for-http-and-one-for-https"></a>와일드 카드를 사용 하 여 동일한 사용자 지정 도메인 이름을 가진 두 응용 프로그램을 게시 하 고 다른 프로토콜 (HTTP의 경우 하나, HTTPS에는 다른 프로토콜을 사용 어떻게 할까요??

이 시나리오는 직접 지원 되지 않습니다. 이 시나리오에 대 한 옵션은 다음과 같습니다.

1. HTTP 및 HTTPS Url 모두 와일드 카드를 사용 하 여 별도의 응용 프로그램으로 게시 하지만 각 응용 프로그램에 서로 다른 사용자 지정 도메인을 제공 합니다. 이 구성은 외부 URL이 다르기 때문에 작동 합니다.

2. 와일드 카드 응용 프로그램을 통해 HTTPS URL을 게시 합니다. 이러한 응용 프로그램 프록시 PowerShell cmdlet을 사용 하 여 HTTP 응용 프로그램을 별도로 게시 합니다.
   - [응용 프로그램 프록시 응용 프로그램 관리](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#application_proxy_application_management)
   - [응용 프로그램 프록시 커넥터 관리](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#application_proxy_connector_management)
