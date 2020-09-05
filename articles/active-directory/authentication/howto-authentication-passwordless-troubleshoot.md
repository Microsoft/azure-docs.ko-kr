---
title: 하이브리드 FIDO2 보안 키에 대 한 알려진 문제 및 문제 해결-Azure Active Directory
description: Azure Active Directory (미리 보기)를 사용 하 여 암호 없는 하이브리드 FIDO2 보안 키 로그인 문제를 해결 하는 몇 가지 알려진 문제 및 방법에 대해 알아봅니다.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: troubleshooting
ms.date: 08/19/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: aakapo
ms.collection: M365-identity-device-management
ms.openlocfilehash: ecab82e43bff6c0d1d83c9c1cdc38cafd809e277
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89236664"
---
# <a name="troubleshooting-for-hybrid-deployments-of-fido2-security-keys-in-azure-ad-preview"></a>Azure AD에서 FIDO2 보안 키 하이브리드 배포에 대 한 문제 해결 (미리 보기)

이 문서에서는 하이브리드 Azure AD 조인 장치에 대 한 질문과 대답 및 온-프레미스 리소스에 대 한 암호 없는 로그인에 대 한 질문과 대답을 다룹니다. 이 암호 없는 기능을 사용 하면 FIDO2 보안 키를 사용 하 여 하이브리드 azure ad 조인 장치에 대해 Windows 10 장치에서 Azure ad 인증을 사용 하도록 설정할 수 있습니다. 사용자는 FIDO2 키와 같은 최신 자격 증명을 사용 하 여 장치에서 Windows에 로그인 하 고 온-프레미스 리소스에 대해 원활한 Single Sign-On (SSO) 환경을 사용 하 여 기존 Active Directory Domain Services (AD DS) 기반 리소스에 액세스할 수 있습니다.

하이브리드 환경의 사용자에 대 한 다음 시나리오가 지원 됩니다.

* FIDO2 보안 키를 사용 하 여 하이브리드 Azure AD 조인 장치에 로그인 하 고 온-프레미스 리소스에 대 한 SSO 액세스를 가져옵니다.
* FIDO2 보안 키를 사용 하 여 Azure AD 조인 장치에 로그인 하 고 온-프레미스 리소스에 대 한 SSO 액세스를 가져옵니다.

FIDO2 보안 키 및 온-프레미스 리소스에 대 한 하이브리드 액세스를 시작 하려면 다음 문서를 참조 하세요.

* [Passwordless 보안 키](howto-authentication-passwordless-security-key.md)
* [암호 없는 Windows 10](howto-authentication-passwordless-security-key-windows.md)
* [Passwordless 온-프레미스](howto-authentication-passwordless-security-key-on-premises.md)

> [!NOTE]
> FIDO2 보안 키는 Azure Active Directory의 공개 미리 보기 기능입니다. 미리 보기에 대한 자세한 내용은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="known-issues"></a>알려진 문제

* [사용자가 FIDO2 보안 키를 사용 하 여 로그인 할 수 없습니다. Windows Hello Face는 너무 빠르고 기본 로그인 메커니즘입니다.](#users-are-unable-to-sign-in-using-fido2-security-keys-as-windows-hello-face-is-too-quick-and-is-the-default-sign-in-mechanism)
* [사용자는 하이브리드 Azure AD 조인 컴퓨터를 만든 직후에 FIDO2 보안 키를 사용할 수 없습니다.](#users-arent-able-to-use-fido2-security-keys-immediately-after-they-create-a-hybrid-azure-ad-joined-machine)
* [FIDO2 보안 키로 로그인 하 고 자격 증명 프롬프트를 받은 후에는 사용자가 NTLM 네트워크 리소스에 대 한 SSO를 가져올 수 없습니다.](#users-are-unable-to-get-sso-to-my-ntlm-network-resource-after-signing-in-with-a-fido2-security-key-and-receiving-a-credential-prompt)

### <a name="users-are-unable-to-sign-in-using-fido2-security-keys-as-windows-hello-face-is-too-quick-and-is-the-default-sign-in-mechanism"></a>사용자가 FIDO2 보안 키를 사용 하 여 로그인 할 수 없습니다. Windows Hello Face는 너무 빠르고 기본 로그인 메커니즘입니다.

Windows Hello Face는 사용자가 등록 된 장치에 대 한 최상의 환경입니다. FIDO2 보안 키는 공유 장치 또는 비즈니스용 Windows Hello 등록이 장벽에 사용 하기 위한 것입니다.

Windows Hello Face에서 사용자가 FIDO2 보안 키 로그인 시나리오를 시도 하지 않는 경우 사용자는 **설정 > 로그인 옵션**에서 얼굴 등록을 제거 하 여 Hello 얼굴 로그인을 해제할 수 있습니다.

### <a name="users-arent-able-to-use-fido2-security-keys-immediately-after-they-create-a-hybrid-azure-ad-joined-machine"></a>사용자는 하이브리드 Azure AD 조인 컴퓨터를 만든 직후에 FIDO2 보안 키를 사용할 수 없습니다.

하이브리드 Azure AD 조인 컴퓨터를 새로 설치 하는 경우 도메인 가입 및 다시 시작 프로세스 후에는 암호를 사용 하 여 로그인 하 고 정책이 동기화 될 때까지 기다려야 FIDO2 보안 키를 사용 하 여 로그인 할 수 있습니다.

이 동작은 도메인에 가입 된 장치에 대 한 알려진 제한 사항으로, FIDO2 보안 키에 국한 되지 않습니다.

현재 상태를 확인 하려면 `dsregcmd /status` 명령을 사용 합니다. *AzureAdJoined* 및 *Domainjoined* 모두 *예*를 표시 하는지 확인 합니다.

### <a name="users-are-unable-to-get-sso-to-my-ntlm-network-resource-after-signing-in-with-a-fido2-security-key-and-receiving-a-credential-prompt"></a>FIDO2 보안 키로 로그인 하 고 자격 증명 프롬프트를 받은 후에는 사용자가 NTLM 네트워크 리소스에 대 한 SSO를 가져올 수 없습니다.

리소스 요청을 처리할 때까지 충분 한 Dc가 응답 하도록 패치 되었는지 확인 합니다. 기능을 실행 하는 서버를 볼 수 있는지 확인 하려면 다음 출력을 검토 하십시오. `nltest /dsgetdc:<dc name> /keylist /kdc`

이 기능을 사용 하 여 DC를 볼 수 있는 경우 사용자의 암호가 로그인 된 이후에 변경 되었거나 다른 문제가 있을 수 있습니다. Microsoft 지원 팀이 디버그할 수 있도록 다음 섹션에서 설명 하는 대로 로그를 수집 합니다.

## <a name="troubleshoot"></a>문제 해결

문제 해결에는 두 가지 영역, 즉 [windows 클라이언트 문제](#windows-client-issues)또는 [배포 문제가](#deployment-issues)있습니다.

### <a name="windows-client-issues"></a>Windows 클라이언트 문제

Windows에 로그인 하거나 Windows 10 장치에서 온-프레미스 리소스에 액세스 하는 것과 관련 된 문제를 해결 하는 데 도움이 되는 데이터를 수집 하려면 다음 단계를 완료 합니다.

1. **피드백 허브** 앱을 엽니다. 앱의 왼쪽 아래에 이름이 있는지 확인 하 고 **새 피드백 항목 만들기**를 선택 합니다.

    피드백 항목 형식으로 *문제*를 선택 합니다.

1. *보안 및 개인 정보* 범주를 선택 하 고 *FIDO* 하위 범주를 선택 합니다.
1. *내 피드백과 함께 첨부 파일 및 진단을 Microsoft에 보내기*확인란을 선택/해제 합니다.
1. *내 문제 다시 만들기*를 선택 하 고 *캡처를 시작*합니다.
1. FIDO2 보안 키를 사용 하 여 컴퓨터를 잠그고 잠금 해제 합니다. 문제가 발생 하는 경우 다른 자격 증명을 사용 하 여 잠금을 해제 하십시오.
1. **피드백 허브**로 돌아가서 **캡처 중지**를 선택 하 고 피드백을 제출 합니다.
1. *피드백* 페이지로 이동 하 여 *내 피드백* 탭으로 이동 합니다. 최근 제출 된 피드백을 선택 합니다.
1. 오른쪽 위 모서리에서 *공유* 단추를 선택 하 여 피드백에 대 한 링크를 가져옵니다. 지원 사례를 열거나 기존 지원 사례에 할당 된 엔지니어에 게 회신 하는 경우이 링크를 포함 합니다.

다음 이벤트 로그 및 레지스트리 키 정보가 수집 됩니다.

**레지스트리 키**

* *HKEY_LOCAL_MACHINE \SOFTWARE\Policies\Microsoft\FIDO [ \* ]*
* *HKEY_LOCAL_MACHINE \SOFTWARE\Policies\Microsoft\PasswordForWork \* [ \* ]*
* *HKEY_LOCAL_MACHINE \SOFTWARE\Microsoft\Policies\PasswordForWork \* [ \* ]*

**진단 정보**

* 라이브 커널 덤프
* AppX 패키지 정보 수집
* UIF 컨텍스트 파일

**이벤트 로그**

* *%SystemRoot%\System32\winevt\Logs\Microsoft-Windows-AAD%40Operational.evtx*
* *%SystemRoot%\System32\winevt\Logs\Microsoft-Windows-WebAuthN%40Operational.evtx*
* *%SystemRoot%\System32\winevt\Logs\Microsoft-Windows-HelloForBusiness%40Operational.evtx*

### <a name="deployment-issues"></a>배포 문제

Azure AD Kerberos 서버 배포 문제를 해결 하려면 Azure AD Connect에 포함 된 새 PowerShell 모듈을 사용 합니다.

#### <a name="viewing-the-logs"></a>로그 보기

Azure AD Kerberos 서버 PowerShell cmdlet은 표준 Azure AD Connect 마법사와 동일한 로깅을 사용 합니다. Cmdlet에서 정보 또는 오류 정보를 보려면 다음 단계를 완료 합니다.

1. Azure AD Connect 서버에서로 이동 `C:\ProgramData\AADConnect\` 합니다. 이 폴더는 기본적으로 숨겨져 있습니다.
1. 디렉터리에 있는 가장 최근 파일을 열고 확인 합니다 `trace-*.log` .

#### <a name="viewing-the-azure-ad-kerberos-server-objects"></a>Azure AD Kerberos 서버 개체 보기

Azure AD Kerberos 서버 개체를 확인 하 고 적절 한 순서 대로 확인 하려면 다음 단계를 완료 합니다.

1. Azure AD Connect 서버에서 PowerShell을 열고로 이동 합니다. `C:\Program Files\Microsoft Azure Active Directory Connect\AzureADKerberos\`
1. 다음 PowerShell 명령을 실행 하 여 Azure AD와 온-프레미스 AD DS에서 Azure AD Kerberos 서버를 확인 합니다.

    *Corp.contoso.com* 을 온-프레미스 AD DS 도메인의 이름으로 바꿉니다.

    ```powershell
    Import-Module ".\AzureAdKerberos.psd1"

    # Specify the on-premises AD DS domain.
    $domain = "corp.contoso.com"

    # Enter an Azure Active Directory Global Admin username and password.
    $cloudCred = Get-Credential

    # Enter a Domain Admin username and password.
    $domainCred = Get-Credential

    # Get the Azure AD Kerberos Server Object
    Get-AzureADKerberosServer -Domain $domain -CloudCredential $cloudCred -DomainCredential
    $domainCred
    ```

명령은 Azure AD와 온-프레미스 AD DS 둘 다에서 Azure AD Kerberos 서버 속성을 출력 합니다. 속성을 검토 하 여 모든 것이 올바른 순서 인지 확인 합니다. 아래 표를 사용 하 여 속성을 확인 하십시오.

첫 번째 속성 집합은 온-프레미스 AD DS 환경의 개체에서 가져온 것입니다. 두 번째 절반 (* Cloud * *로 시작 하는 속성)은 Azure AD의 Kerberos 서버 개체에서 가져온 것입니다.

| 속성           | Description  |
|--------------------|--------------|
| Id                 | AD DS 도메인 컨트롤러 개체의 고유 *Id* 입니다. |
| DomainDnsName      | AD DS 도메인의 DNS 도메인 이름입니다. |
| 컴퓨터 계정    | Azure AD Kerberos 서버 개체 (DC)의 컴퓨터 계정 개체입니다. |
| UserAccount        | Azure AD Kerberos 서버 TGT 암호화 키를 보유 하는 사용 하지 않도록 설정 된 사용자 계정 개체입니다. 이 계정의 DN은 *cn = krbtgt_AzureAD, cn = Users, <도메인-dn>* |
| KeyVersion         | Azure AD Kerberos 서버 TGT 암호화 키의 키 버전입니다. 이 버전은 키를 만들 때 할당 됩니다. 그런 다음 키를 회전할 때마다 버전이 증가 합니다. 증분은 복제 메타 데이터를 기반으로 하므로 1 보다 클 수 있습니다.<br /><br /> 예를 들어 초기 *Keyversion* 은 *192272*일 수 있습니다. 키를 처음 회전할 때 버전은 *212621*로 이동 될 수 있습니다.<br /><br /> 확인 해야 하는 중요 한 점은 온-프레미스 개체의 *keyversion* 과 클라우드 개체의 *cloudkeyversion* 이 동일한 지 확인 하는 것입니다. |
| KeyUpdatedOn       | Azure AD Kerberos 서버 TGT 암호화 키가 업데이트 또는 생성 된 날짜와 시간입니다. |
| KeyUpdatedFrom     | Azure AD Kerberos 서버 TGT 암호화 키가 마지막으로 업데이트 된 DC입니다. |
| CloudId            | Azure AD 개체의 *Id* 입니다. 위의 *Id* 와 일치 해야 합니다. |
| CloudDomainDnsName | Azure AD 개체의 *DomainDnsName* 입니다. 위의 *DomainDnsName* 일치 해야 합니다. |
| CloudKeyVersion    | Azure AD 개체의 *Keyversion* 입니다. 위의 *Keyversion* 과 일치 해야 합니다. |
| CloudKeyUpdatedOn  | Azure AD 개체의 *KeyUpdatedOn* 입니다. 위의 *KeyUpdatedOn* 일치 해야 합니다. |

## <a name="next-steps"></a>다음 단계

FIDO2 보안 키 및 온-프레미스 리소스에 대 한 하이브리드 액세스를 시작 하려면 다음 문서를 참조 하세요.

* [암호 없는 FIDO2 보안 키](howto-authentication-passwordless-security-key.md)
* [암호 없는 Windows 10](howto-authentication-passwordless-security-key-windows.md)
* [암호 없는 온-프레미스](howto-authentication-passwordless-security-key-on-premises.md)
