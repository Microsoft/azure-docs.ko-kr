---
title: Azure MFA NPS 확장 문제 해결-Azure Active Directory
description: Azure Multi-Factor Authentication용 NPS 확장 문제 해결에 대한 도움 얻기
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: troubleshooting
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.custom: has-adal-ref
ms.openlocfilehash: f991e38c184fe44f63af63809deb14eda22f8f4c
ms.sourcegitcommit: 6fc156ceedd0fbbb2eec1e9f5e3c6d0915f65b8e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/21/2020
ms.locfileid: "88716727"
---
# <a name="resolve-error-messages-from-the-nps-extension-for-azure-multi-factor-authentication"></a>Azure Multi-factor Authentication용 NPS 확장의 오류 메시지 해결

Azure Multi-factor Authentication용 NPS 확장에서 오류가 발생하는 경우 이 문서를 사용하여 해결 방법을 더 빠르게 찾을 수 있습니다. Nps 확장 로그는 이벤트 뷰어의 **사용자 지정 보기**  >  **서버 역할**  >  **네트워크 정책 및** nps 확장이 설치 된 서버의 액세스 서비스에서 찾을 수 있습니다.

## <a name="troubleshooting-steps-for-common-errors"></a>일반적인 오류에 대한 문제 해결 단계

| 오류 코드 | 문제 해결 단계 |
| ---------- | --------------------- |
| **CONTACT_SUPPORT** | [지원에 문의](#contact-microsoft-support)하고 로그를 수집하기 위한 단계 목록을 설명합니다. 테 넌 트 ID 및 UPN (사용자 계정 이름)을 포함 하 여 오류 이전에 발생 한 상황에 대 한 정보를 제공 합니다. |
| **CLIENT_CERT_INSTALL_ERROR** | 클라이언트 인증서가 설치되었거나 테넌트와 연결된 방식에 대해 문제가 있을 수 있습니다. [MFA NPS 확장 문제 해결](howto-mfa-nps-extension.md#troubleshooting)의 지침에 따라 클라이언트 인증서 문제를 조사합니다. |
| **ESTS_TOKEN_ERROR** | [MFA NPS 확장 문제 해결](howto-mfa-nps-extension.md#troubleshooting)의 지침에 따라 클라이언트 인증서 및 ADAL 토큰 문제를 조사합니다. |
| **HTTPS_COMMUNICATION_ERROR** | NPS 서버는 Azure MFA에서 응답을 받을 수 없습니다. 방화벽이 https://adnotifications.windowsazure.com과의 트래픽을 위해 양방향으로 열려 있는지 확인합니다. |
| **HTTP_CONNECT_ERROR** | NPS 확장을 실행하는 서버에서 `https://adnotifications.windowsazure.com`과 `https://login.microsoftonline.com/`에 연결할 수 있는지 확인합니다. 해당 사이트가 로드되지 않으면 해당 서버의 연결 문제를 해결합니다. |
| **Azure MFA용 NPS 확장:** <br> Azure MFA용 NPS 확장만 AccessAccept 상태에서 Radius 요청에 대해 보조 인증을 수행합니다. 요청을 무시하고 AccessReject 응답 상태를 통해 사용자 이름에 대해 받은 요청입니다. | 이 오류는 일반적으로 AD에서 인증 오류를 나타내거나, NPS 서버가 Azure AD에서 응답을 받을 수 없음을 보여줍니다. 포트 80 및 443을 사용하여 `https://adnotifications.windowsazure.com` 및 `https://login.microsoftonline.com` 간의 트래픽을 위해 방화벽이 양방향으로 열려 있는지 확인합니다. 네트워크 액세스 권한의 전화 접속 탭에서 설정이 "NPS 네트워크 정책을 통한 액세스 제어"로 설정되어 있는지 확인해야 합니다. 이 오류는 사용자에 게 라이선스가 할당 되지 않은 경우에도 트리거될 수 있습니다. |
| **REGISTRY_CONFIG_ERROR** | 설치 후에 [PowerShell 스크립트](howto-mfa-nps-extension.md#install-the-nps-extension)가 실행되지 않았으므로 레지스트리에 애플리케이션에 대한 키가 없습니다. 오류 메시지에 누락된 키가 포함됩니다. HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureMfa 아래에 키가 있는지 확인합니다. |
| **REQUEST_FORMAT_ERROR** <br> Radius 요청에 필수 Radius userName\Identifier 특성이 없습니다. NPS가 RADIUS 요청을 수신하는지 확인합니다. | 이 오류는 일반적으로 설치 문제를 반영합니다. RADIUS 요청을 받을 수 있는 NPS 확장을 NPS 서버에 설치해야 합니다. RRAS 및 RDG와 같은 서비스에 대한 종속성으로 설치된 NPS 서버가 radius 요청을 수신하지 않습니다. 이러한 설치 위에 설치된 NPS 확장은 작동하지 않으며 인증 요청의 세부 정보를 읽을 수 없으므로 오류가 발생합니다. |
| **REQUEST_MISSING_CODE** | NPS 서버와 NAS 서버 간의 암호 암호화 프로토콜이 사용 중인 보조 인증 방법을 지원하는지 확인합니다. **PAP**는 클라우드에서 전화 통화, 단방향 문자 메시지, 모바일 앱 알림 및 모바일 앱 확인 코드와 같은 Azure MFA의 모든 인증 방법을 지원합니다. **CHAPV2** 및 **EAP**는 전화 통화 및 모바일 앱 알림을 지원합니다. |
| **USERNAME_CANONICALIZATION_ERROR** | 사용자가 온-프레미스 Active Directory 인스턴스에 존재하는지와 NPS 서비스에 디렉터리에 액세스할 수 있는 권한이 있는지 확인합니다. 포리스트 간 트러스트를 사용하는 경우 [지원 서비스](#contact-microsoft-support) 추가 지원을 요청하세요. |

### <a name="alternate-login-id-errors"></a>대체 로그인 ID 오류

| 오류 코드 | 오류 메시지 | 문제 해결 단계 |
| ---------- | ------------- | --------------------- |
| **ALTERNATE_LOGIN_ID_ERROR** | 오류: userObjectSid 조회 실패 | 사용자가 온-프레미스 Active Directory 인스턴스에 있는지 확인합니다. 포리스트 간 트러스트를 사용하는 경우 [지원 서비스](#contact-microsoft-support) 추가 지원을 요청하세요. |
| **ALTERNATE_LOGIN_ID_ERROR** | 오류: 대체 LoginId 조회 실패 | LDAP_ALTERNATE_LOGINID_ATTRIBUTE가 [유효한 Active Directory 특성](/windows/win32/adschema/attributes-all)으로 설정되어 있는지 확인합니다. <br><br> LDAP_FORCE_GLOBAL_CATALOG가 True로 설정되어 있거나 LDAP_LOOKUP_FORESTS가 비어 있지 않은 값으로 구성된 경우 글로벌 카탈로그를 구성했는지와 AlternateLoginId 특성이 추가되었는지 확인합니다. <br><br> LDAP_LOOKUP_FORESTS가 비어 있지 않은 값으로 구성된 경우 해당 값이 올바른지 확인합니다. 포리스트 이름을 여러 개 있으면 이름은 공백 없이 세미콜론으로 구분해야 합니다. <br><br> 이러한 단계로도 문제가 해결되지 않으면 [지원 서비스에 문의](#contact-microsoft-support)하세요. |
| **ALTERNATE_LOGIN_ID_ERROR** | 오류: 대체 LoginId 값이 비어 있음 | AlternateLoginId 특성이 사용자에 대해 구성되어 있는지 확인합니다. |

## <a name="errors-your-users-may-encounter"></a>사용자에게 발생할 수 있는 오류

| 오류 코드 | 오류 메시지 | 문제 해결 단계 |
| ---------- | ------------- | --------------------- |
| **AccessDenied** | 호출자 테넌트에 사용자를 인증하기 위한 액세스 권한이 없습니다. | 테넌트 도메인 및 UPN(사용자 계정 이름)의 도메인이 동일한지 확인합니다. 예를 들어 user@contoso.com이 Contoso 테넌트에서 인증을 받으려고 하는지 확인합니다. UPN은 Azure의 테넌트에 대해 유효한 사용자를 나타냅니다. |
| **AuthenticationMethodNotConfigured** | 지정된 인증 방법이 사용자에 대해 구성되지 않았습니다. | [2단계 인증을 위한 설정 관리](../user-help/multi-factor-authentication-end-user-manage-settings.md)의 지침에 따라 사용자가 확인 방법을 추가하거나 확인하도록 합니다. |
| **AuthenticationMethodNotSupported** | 지정된 인증 방법이 지원되지 않습니다. | 이 오류를 포함하는 모든 로그를 수집하고 [지원 서비스에 문의](#contact-microsoft-support)합니다. 지원 서비스에 문의할 때 사용자 이름 및 오류를 트리거한 2차 확인 방법을 제공합니다. |
| **BecAccessDenied** | MSODS Bec 호출이 액세스 거부 오류를 반환했습니다. 테넌트에 사용자 이름이 정의되지 않은 것 같습니다. | 사용자가 온-프레미스의 Active Directory에 있으나 AD Connect에 의해 Azure AD와 동기화되지 않았습니다. 또는 테넌트에 해당 사용자가 없습니다. Azure AD에 사용자를 추가하고 [2단계 인증을 위한 설정 관리](../user-help/multi-factor-authentication-end-user-manage-settings.md)의 지침에 따라 확인 방법을 추가하도록 합니다. |
| **InvalidFormat** 또는 **StrongAuthenticationServiceInvalidParameter** | 전화 번호가 인식할 수 없는 형식입니다. | 사용자가 해당 확인 전화 번호를 수정하도록 합니다. |
| **InvalidSession** | 지정된 세션이 유효하지 않거나 만료되었을 수 있습니다. | 세션이 완료되는 데 3분 넘게 걸렸습니다. 사용자가 인증 요청을 시작하고 3분 이내에 확인 코드를 입력하는지 또는 앱 알림 인증 요청에 응답하는지 확인합니다. 그러도 문제가 해결되지 않으면 클라이언트, 서버 NAS, NPS 서버 및 Azure MFA 엔드포인트 간에 네트워크 대기 시간이 없는지 확인합니다.  |
| **NoDefaultAuthenticationMethodIsConfigured** | 사용자에 대해 기본 인증 방법이 구성되지 않았습니다. | [2단계 인증을 위한 설정 관리](../user-help/multi-factor-authentication-end-user-manage-settings.md)의 지침에 따라 사용자가 확인 방법을 추가하거나 확인하도록 합니다. 사용자가 기본 인증 방법을 선택했는지와 계정에 대해 해당 방법을 구성했는지 확인합니다. |
| **OathCodePinIncorrect** | 잘못된 코드 및 PIN을 입력했습니다. | NPS 확장에서는 이 오류가 발생하지 않습니다. 이 오류가 발생하는 경우 [지원 서비스](#contact-microsoft-support)에서 문제 해결을 위한 지원을 받습니다. |
| **ProofDataNotFound** | 지정된 인증 방법에 대해 교정 데이터가 구성되지 않았습니다. | 사용자에게 [2단계 인증을 위한 설정 관리](../user-help/multi-factor-authentication-end-user-manage-settings.md)의 지침에 따라 다른 확인 방법을 시도하거나 새 확인 방법을 추가하도록 합니다. 확인 방법이 올바르게 설정되었는지 확인한 후에도 이 오류가 계속 발생하면 [지원 서비스에 문의](#contact-microsoft-support)합니다. |
| **SMSAuthFailedWrongCodePinEntered** | 잘못된 코드 및 PIN을 입력했습니다. (OneWaySMS) | NPS 확장에서는 이 오류가 발생하지 않습니다. 이 오류가 발생하는 경우 [지원 서비스](#contact-microsoft-support)에서 문제 해결을 위한 지원을 받습니다. |
| **TenantIsBlocked** | 테넌트가 차단되었습니다. | Azure Portal의 Azure AD 속성 페이지에서 *테 넌 트 ID* 를 사용 하 여 [지원 담당자에 게 문의 하세요](#contact-microsoft-support) . |
| **UserNotFound** | 지정된 사용자를 찾을 수 없습니다. | 테넌트가 더 이상 Azure AD에서 활성으로 표시되지 않습니다. 구독이 활성 상태인지와 자사 앱을 요청했는지 확인합니다. 또한 인증서 주체의 테넌트가 예상과 같은지 및 인증서가 여전히 유효하고 서비스 사용자에 등록되어 있는지 확인합니다. |

## <a name="messages-your-users-may-encounter-that-arent-errors"></a>사용자에게 표시될 수 있는 오류가 아닌 메시지

경우에 따라 인증 요청이 실패했기 때문에 Multi-Factor Authentication에서 메시지가 표시될 수 있습니다. 이것은 제품 구성에 따른 오류가 아니며 인증 요청이 거부된 이유를 설명하는 의도적인 경고입니다.

| 오류 코드 | 오류 메시지 | 권장되는 단계 |
| ---------- | ------------- | ----------------- |
| **OathCodeIncorrect** | 잘못된 코드를 입력했거나 OATH 코드가 올바르지 않습니다. | 사용자가 잘못된 코드를 입력했습니다. 새 코드를 요청하거나 다시 로그인하여 다시 시도하도록 합니다. |
| **SMSAuthFailedMaxAllowedCodeRetryReached** | 허용되는 최대 코드 다시 시도 횟수에 도달했습니다. | 사용자가 너무 여러 번 확인 요청에 실패했습니다. 설정에 따라 관리자가 차단을 해제해야 할 수 있습니다.  |
| **SMSAuthFailedWrongCodeEntered** | 잘못된 코드를 입력했거나 텍스트 메시지 OTP가 올바르지 않습니다. | 사용자가 잘못된 코드를 입력했습니다. 새 코드를 요청하거나 다시 로그인하여 다시 시도하도록 합니다. |

## <a name="errors-that-require-support"></a>지원이 필요한 오류

이러한 오류 중 하나가 발생하면 [지원 서비스에 문의](#contact-microsoft-support)하여 진단 도움을 받는 것이 좋습니다. 이러한 오류를 해결할 수 있는 표준 단계는 없습니다. 지원 서비스에 문의할 때 오류를 발생시킨 단계에 대해 최대한 많은 정보를 제공하고 테넌트 정보도 제공해야 합니다.

| 오류 코드 | 오류 메시지 |
| ---------- | ------------- |
| **InvalidParameter** | 요청은 null이 아니어야 합니다. |
| **InvalidParameter** | ReplicationScope:{0}의 경우 ObjectId는 null 또는 공백이 아니어야 합니다. |
| **InvalidParameter** | CompanyName 길이 \{0}\이(가) 최대 허용 길이 {1}보다 깁니다. |
| **InvalidParameter** | UserPrincipalName은 null 또는 공백이 아니어야 합니다. |
| **InvalidParameter** | 제공된 TenantId가 올바른 형식이 아닙니다. |
| **InvalidParameter** | SessionId는 null 또는 공백이 아니어야 합니다. |
| **InvalidParameter** | 요청 또는 Msod에서 ProofData를 확인할 수 없습니다. ProofData를 알 수 없습니다. |
| **InternalError** |  |
| **OathCodePinIncorrect** |  |
| **VersionNotSupported** |  |
| **MFAPinNotSetup** |  |

## <a name="next-steps"></a>다음 단계

### <a name="troubleshoot-user-accounts"></a>사용자 계정 문제 해결

사용자에게 [2단계 인증 문제가 발생하면](../user-help/multi-factor-authentication-end-user-troubleshoot.md) 문제를 자체 진단하도록 도와주세요.

### <a name="health-check-script"></a>상태 검사 스크립트

[AZURE MFA Nps 확장 상태 검사 스크립트](/samples/azure-samples/azure-mfa-nps-extension-health-check/azure-mfa-nps-extension-health-check/) 는 nps 확장 문제를 해결할 때 기본 상태 검사를 수행 합니다. 스크립트를 실행 하 고 옵션 3을 선택 합니다.

### <a name="contact-microsoft-support"></a>Microsoft 지원에 문의

추가 지원이 필요한 경우 [Azure Multi-Factor Authentication 서버 지원](https://support.microsoft.com/oas/default.aspx?prid=14947)을 통해 지원 전문가에게 문의하세요. 문의하는 경우 가능한 문제에 대한 많은 정보를 제공해주시면 도움이 됩니다. 오류를 발견한 페이지, 특정 오류 코드, 특정 세션 ID, 오류를 발견한 사용자의 ID 및 디버그 로그를 포함하는 정보를 제공해 주시면 됩니다.

지원 진단에 대한 디버그 로그를 수집하려면 NPS 서버에서 다음 단계를 사용합니다.

1. 레지스트리 편집기를 열고, HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureMfa로 이동한 다음, **VERBOSE_LOG**를 **TRUE**로 설정합니다.
2. 관리자 명령 프롬프트를 열고 다음 명령을 실행합니다.

   ```
   Mkdir c:\NPS
   Cd NPS
   netsh trace start Scenario=NetConnection capture=yes tracefile=c:\NPS\nettrace.etl
   logman create trace "NPSExtension" -ow -o c:\NPS\NPSExtension.etl -p {7237ED00-E119-430B-AB0F-C63360C8EE81} 0xffffffffffffffff 0xff -nb 16 16 -bs 1024 -mode Circular -f bincirc -max 4096 -ets
   logman update trace "NPSExtension" -p {EC2E6D3A-C958-4C76-8EA4-0262520886FF} 0xffffffffffffffff 0xff -ets
   ```

3. 문제 재현

4. 다음 명령을 사용하여 추적을 중지합니다.

   ```
   logman stop "NPSExtension" -ets
   netsh trace stop
   wevtutil epl AuthNOptCh C:\NPS\%computername%_AuthNOptCh.evtx
   wevtutil epl AuthZOptCh C:\NPS\%computername%_AuthZOptCh.evtx
   wevtutil epl AuthZAdminCh C:\NPS\%computername%_AuthZAdminCh.evtx
   Start .
   ```

5. 레지스트리 편집기를 열고, HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureMfa로 이동한 다음, **VERBOSE_LOG**를 **FALSE**로 설정합니다.
6. C:\NPS 폴더의 내용을 Zip으로 압축하고 지원 사례에 Zip 파일을 첨부합니다.