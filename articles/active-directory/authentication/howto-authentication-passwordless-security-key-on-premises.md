---
title: 온-프레미스 리소스에 대 한 암호 없는 보안 키 로그인 (미리 보기)-Azure Active Directory
description: Azure Active Directory (미리 보기)를 사용 하 여 온-프레미스 리소스에 대해 암호 없는 보안 키 로그인을 사용 하도록 설정 하는 방법에 대해 알아봅니다.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 03/09/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: librown, aakapo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9f20da2d2ecb4426c0deb1c01591ead5933090f6
ms.sourcegitcommit: 6fc156ceedd0fbbb2eec1e9f5e3c6d0915f65b8e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/21/2020
ms.locfileid: "88716999"
---
# <a name="enable-passwordless-security-key-sign-in-to-on-premises-resources-with-azure-active-directory-preview"></a>Azure Active Directory (미리 보기)를 사용 하 여 온-프레미스 리소스에 대해 암호 없는 보안 키 로그인 사용

이 문서에서는 **AZURE ad 조인** 및 **하이브리드 Azure ad 조인** Windows 10 장치를 모두 사용 하는 환경에 대해 온-프레미스 리소스에 대해 암호 없는 인증을 사용 하는 방법을 중점적으로 설명 합니다. 이 기능은 Microsoft 호환 보안 키를 사용 하 여 온-프레미스 리소스에 대 한 원활한 Single Sign-On (SSO)를 제공 합니다.

> [!NOTE]
> FIDO2 보안 키는 Azure Active Directory의 공개 미리 보기 기능입니다. 미리 보기에 대한 자세한 내용은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="sso-to-on-premises-resources-using-fido2-keys"></a>FIDO2 키를 사용 하 여 온-프레미스 리소스에 SSO

AD (Azure Active Directory)는 하나 이상의 Active Directory 도메인에 대해 Kerberos Tgt (허용 티켓)를 실행할 수 있습니다. 이 기능을 사용 하면 FIDO2 보안 키와 같은 최신 자격 증명을 사용 하 여 Windows에 로그인 하 고 기존 Active Directory 기반 리소스에 액세스할 수 있습니다. Kerberos 서비스 티켓 및 권한 부여는 온-프레미스 Active Directory 도메인 컨트롤러에 의해 계속 제어 됩니다.

Azure AD Kerberos 서버 개체는 온-프레미스 Active Directory 만든 다음 Azure Active Directory에 안전 하 게 게시 됩니다. 개체가 물리적 서버와 연결 되어 있지 않습니다. Azure Active Directory에서 Active Directory 도메인 Kerberos Tgt을 생성 하는 데 사용할 수 있는 리소스입니다.

![Azure AD에서 TGT (티켓 부여 티켓) 가져오기 및 AD DS](./media/howto-authentication-passwordless-on-premises/fido2-ticket-granting-ticket-exchange-process.png)

1. 사용자가 FIDO2 보안 키를 사용 하 여 Windows 10 장치에 로그인 하 고 Azure AD에 인증 합니다.
1. Azure AD는 디렉터리에서 사용자의 온-프레미스 AD 도메인과 일치 하는 Kerberos 서버 키를 확인 합니다.
   1. Azure AD는 사용자의 온-프레미스 AD 도메인에 대 한 Kerberos TGT를 생성 합니다. TGT는 사용자의 SID만 포함 합니다. TGT에는 인증 데이터가 포함 되지 않습니다.
1. TGT는 Azure AD 기본 새로 고침 토큰 (PRT)과 함께 클라이언트에 반환 됩니다.
1. 클라이언트 컴퓨터는 온-프레미스 AD 도메인 컨트롤러에 연결 하 고 완전히 구성 된 TGT의 부분 TGT를 거래 합니다.
1. 클라이언트 컴퓨터에는 이제 Azure AD PRT와 전체 Active Directory TGT가 있으며 클라우드 및 온-프레미스 리소스 모두에 액세스할 수 있습니다.

## <a name="requirements"></a>요구 사항

조직에서는이 문서의 단계를 완료 하기 전에 [Windows 10 장치 (미리 보기)에 대 한 암호 없는 보안 키 기호를 사용 하도록 설정](howto-authentication-passwordless-security-key.md) 하는 단계를 완료 해야 합니다.

조직에서는 다음과 같은 소프트웨어 요구 사항도 충족 해야 합니다.

- 장치에서 Windows 10 Insider Build 18945 이상 버전을 실행 해야 합니다.
- [Azure AD Connect](../hybrid/how-to-connect-install-roadmap.md#install-azure-ad-connect)버전 1.4.32.0 이상 이어야 합니다.
  - 사용 가능한 Azure AD 하이브리드 인증 옵션에 대 한 자세한 내용은 [Azure Active Directory 하이브리드 id 솔루션에 적합 한 인증 방법 선택](../hybrid/choose-ad-authn.md) 및 [Azure AD Connect에 사용할 설치 유형 선택](../hybrid/how-to-connect-install-select-installation.md)을 참조 하세요.
- Windows Server 도메인 컨트롤러에는 다음 패치가 설치 되어 있어야 합니다.
    - Windows Server 2016- https://support.microsoft.com/help/4534307/windows-10-update-kb4534307
    - Windows Server 2019- https://support.microsoft.com/help/4534321/windows-10-update-kb4534321

### <a name="supported-scenarios"></a>지원되는 시나리오

시나리오는 다음 두 시나리오에서 모두 SSO (Single Sign-On)를 지원 합니다.

- Office 365 및 기타 SAML 사용 응용 프로그램과 같은 클라우드 리소스
- 온-프레미스 리소스와 웹 사이트에 대 한 Windows 통합 인증 리소스에는 IIS 인증을 요구 하는 웹 사이트와 SharePoint 사이트 및 NTLM 인증을 사용 하는 리소스가 포함 될 수 있습니다.

### <a name="unsupported-scenarios"></a>지원되지 않는 시나리오

지원 되지 않는 시나리오는 다음과 같습니다.

- Windows Server Active Directory Domain Services (AD DS) 도메인 가입 (온-프레미스 전용 장치) 배포.
- 보안 키를 사용 하는 RDP, VDI 및 Citrix 시나리오.
- 보안 키를 사용 하는 S/MIME.
- 보안 키를 사용 하 여 "실행" 합니다.
- 보안 키를 사용 하 여 서버에 로그인 합니다.

## <a name="create-kerberos-server-object"></a>Kerberos 서버 개체 만들기

관리자는 Azure AD Connect 서버에서 PowerShell 도구를 사용 하 여 온-프레미스 디렉터리에 Azure AD Kerberos 서버 개체를 만듭니다. Azure AD 사용자를 포함 하는 조직의 각 도메인 및 포리스트에서 다음 단계를 실행 합니다.

1. 최신 버전의 Azure AD Connect로 업그레이드 합니다. 이 지침에서는 하이브리드 환경을 지원 하도록 Azure AD Connect를 이미 구성 했다고 가정 합니다.
1. Azure AD Connect 서버에서 관리자 권한으로 PowerShell 프롬프트를 열고로 이동 합니다. `C:\Program Files\Microsoft Azure Active Directory Connect\AzureADKerberos\`
1. 다음 PowerShell 명령을 실행 하 여 온-프레미스 Active Directory 도메인 및 Azure Active Directory 테 넌 트 둘 다에서 새 Azure AD Kerberos 서버 개체를 만듭니다.

> [!NOTE]
> `contoso.corp.com`다음 예제에서를 온-프레미스 Active Directory 도메인 이름으로 바꿉니다.

```powerShell
Import-Module ".\AzureAdKerberos.psd1"

# Specify the on-premises Active Directory domain. A new Azure AD
# Kerberos Server object will be created in this Active Directory domain.
$domain = "contoso.corp.com"

# Enter an Azure Active Directory global administrator username and password.
$cloudCred = Get-Credential

# Enter a domain administrator username and password.
$domainCred = Get-Credential

# Create the new Azure AD Kerberos Server object in Active Directory
# and then publish it to Azure Active Directory.
Set-AzureADKerberosServer -Domain $domain -CloudCredential $cloudCred -DomainCredential $domainCred
```

### <a name="viewing-and-verifying-the-azure-ad-kerberos-server"></a>Azure AD Kerberos 서버 보기 및 확인

다음 명령을 사용 하 여 새로 만든 Azure AD Kerberos 서버를 확인 하 고 확인할 수 있습니다.

```powerShell
Get-AzureADKerberosServer -Domain $domain -CloudCredential $cloudCred -DomainCredential $domainCred
```

이 명령은 Azure AD Kerberos 서버 속성을 출력 합니다. 속성을 검토 하 여 모든 것이 올바른 순서 인지 확인할 수 있습니다.

| 속성 | Description |
| --- | --- |
| ID | AD DS DC 개체의 고유 ID입니다. 이 ID는 "슬롯" 또는 "분기 ID" 라고도 합니다. |
| DomainDnsName | Active Directory 도메인 DNS 도메인 이름입니다. |
| 컴퓨터 계정 | Azure AD Kerberos 서버 개체 (DC)의 컴퓨터 계정 개체입니다. |
| UserAccount | Azure AD Kerberos 서버 TGT 암호화 키를 보유 하는 사용 하지 않도록 설정 된 사용자 계정 개체입니다. 이 계정의 DN은 다음과 같습니다. `CN=krbtgt_AzureAD,CN=Users,<Domain-DN>` |
| KeyVersion | Azure AD Kerberos 서버 TGT 암호화 키의 키 버전입니다. 이 버전은 키를 만들 때 할당 됩니다. 그런 다음 키를 회전할 때마다 버전이 증가 합니다. 증분은 복제 메타 데이터를 기반으로 하며 1 보다 클 수 있습니다. 예를 들어 초기 *Keyversion* 은 *192272*일 수 있습니다. 키를 처음 회전할 때 버전은 *212621*로 이동 될 수 있습니다. 확인 해야 하는 중요 한 점은 온-프레미스 개체의 *keyversion* 과 클라우드 개체의 *cloudkeyversion* 이 동일한 지 확인 하는 것입니다. |
| KeyUpdatedOn | Azure AD Kerberos 서버 TGT 암호화 키가 업데이트 또는 생성 된 날짜와 시간입니다. |
| KeyUpdatedFrom | Azure AD Kerberos 서버 TGT 암호화 키가 마지막으로 업데이트 된 DC입니다. |
| CloudId | Azure AD 개체의 ID입니다. 위의 ID와 일치 해야 합니다. |
| CloudDomainDnsName | Azure AD 개체의 *DomainDnsName* 입니다. 위의 *DomainDnsName* 일치 해야 합니다. |
| CloudKeyVersion | Azure AD 개체의 *Keyversion* 입니다. 위의 *Keyversion* 과 일치 해야 합니다. |
| CloudKeyUpdatedOn | Azure AD 개체의 *KeyUpdatedOn* 입니다. 위의 *KeyUpdatedOn* 일치 해야 합니다. |

### <a name="rotating-the-azure-ad-kerberos-server-key"></a>Azure AD Kerberos 서버 키 회전

Azure AD Kerberos 서버 암호화 krbtgt 키는 정기적으로 회전 해야 합니다. 다른 모든 Active Directory 도메인 Controller krbtgt 키를 회전 하는 데 사용 하는 것과 동일한 일정을 따르는 것이 좋습니다.

> [!WARNING]
> Krbtgt 키를 회전할 수 있는 다른 도구가 있지만이 문서에 언급 된 도구를 사용 하 여 Azure AD Kerberos 서버의 krbtgt 키를 회전 해야 합니다. 이렇게 하면 키가 온-프레미스 AD와 Azure AD에서 모두 업데이트 됩니다.

```powerShell
Set-AzureADKerberosServer -Domain $domain -CloudCredential $cloudCred -DomainCredential $domainCred -RotateServerKey
```

### <a name="removing-the-azure-ad-kerberos-server"></a>Azure AD Kerberos 서버 제거

시나리오를 되돌리고 온-프레미스 Active Directory 및 Azure Active Directory에서 Azure AD Kerberos 서버를 제거 하려면 다음 명령을 실행 합니다.

```powerShell
Remove-AzureADKerberosServer -Domain $domain -CloudCredential $cloudCred -DomainCredential $domainCred
```

### <a name="multi-forest-and-multi-domain-scenarios"></a>다중 포리스트 및 다중 도메인 시나리오

Azure AD Kerberos 서버 개체는 Azure AD에서 *KerberosDomain* 개체로 표시 됩니다. 각 온-프레미스 Active Directory 도메인은 Azure AD에서 단일 *KerberosDomain* 개체로 표현 됩니다.

예를 들어 조직에는 및 라는 두 개의 도메인이 있는 Active Directory 포리스트가 있습니다 `contoso.com` `fabrikam.com` . Azure AD에서 전체 포리스트에 대해 Kerberos Tgt을 실행 하도록 선택 하는 경우 Azure AD에는 두 개의 *KerberosDomain* 개체가 있습니다. 에 대해 하나의 *KerberosDomain* 개체 `contoso.com` ,에 대해 `fabrikam.com` 하나씩 Active Directory 포리스트가 여러 개인 경우 각 포리스트의 각 도메인에 대해 하나의 *KerberosDomain* 개체가 있습니다.

Azure AD 사용자를 포함 하는 조직의 각 도메인 및 포리스트에서 [Kerberos 서버 개체를 만드는](#create-kerberos-server-object) 단계를 실행 해야 합니다.

## <a name="known-behavior"></a>알려진 동작

암호가 만료 되 면 FIDO로 로그인이 차단 됩니다. 사용자가 자신의 암호를 다시 설정 하 여 FIDO를 사용 하 여 로그인 할 수 있습니다.

## <a name="troubleshooting-and-feedback"></a>문제 해결 및 피드백

이 기능을 미리 보는 동안 피드백을 공유 하거나 문제를 해결 하려는 경우 다음 단계를 사용 하 여 Windows 피드백 허브 앱을 통해 공유 하세요.

1. **피드백 허브** 를 시작 하 고 로그인 했는지 확인 합니다.
1. 다음 분류에 따라 사용자 의견을 제출 합니다.
   - 범주: 보안 및 개인 정보
   - 하위 범주: FIDO
1. 로그를 캡처하려면이 옵션을 사용 하 여 **문제를 다시 만드십시오** .

## <a name="frequently-asked-questions"></a>자주 묻는 질문

### <a name="does-this-work-in-my-on-premises-environment"></a>온-프레미스 환경에서 작동 하나요?

이 기능은 순수한 온-프레미스 Active Directory Domain Services (AD DS) 환경에서 작동 하지 않습니다.

### <a name="my-organization-requires-two-factor-authentication-to-access-resources-what-can-i-do-to-support-this-requirement"></a>조직에서 리소스에 액세스 하려면 2 단계 인증이 필요 합니다. 이 요구 사항을 지원 하기 위해 수행할 수 있는 작업은 무엇 인가요?

보안 키는 다양 한 폼 팩터를 제공 합니다. 관심 있는 장치 제조업체에 문의 하 여 PIN 또는 생체 인식으로 두 번째 요소로 장치를 사용 하도록 설정 하는 방법을 설명 합니다.

### <a name="can-admins-set-up-security-keys"></a>관리자가 보안 키를 설정할 수 있나요?

이 기능의 GA (일반 공급)에 대해이 기능을 사용 하 고 있습니다.

### <a name="where-can-i-go-to-find-compliant-security-keys"></a>규격 보안 키를 찾을 수 있는 위치

[FIDO2 보안 키](concept-authentication-passwordless.md#fido2-security-keys)

### <a name="what-do-i-do-if-i-lose-my-security-key"></a>보안 키를 분실 한 경우 어떻게 해야 하나요?

**보안 정보** 페이지로 이동 하 고 보안 키를 제거 하 여 Azure Portal에서 키를 제거할 수 있습니다.

### <a name="im-not-able-to-use-fido-immediately-after-i-create-a-hybrid-azure-ad-joined-machine"></a>하이브리드 Azure AD 조인 컴퓨터를 만든 직후에 FIDO를 사용할 수 없습니다.

하이브리드 Azure AD 조인 컴퓨터를 새로 설치 하는 경우 도메인 가입 및 다시 시작 프로세스 후에 암호를 사용 하 여 로그인 하 고 FIDO를 사용 하 여 로그인 할 수 있기 전에 정책이 동기화 될 때까지 기다려야 합니다.

- 명령 창에를 입력 하 여 현재 상태를 확인 `dsregcmd /status` 하 고 *AzureAdJoined* 및 *domainjoined* 모두 *예*를 표시 하는지 확인 합니다.
- 이 지연은 도메인에 가입 된 장치에 대 한 알려진 제한 사항이 며 FIDO는 관련이 없습니다.

### <a name="im-unable-to-get-sso-to-my-ntlm-network-resource-after-signing-in-with-fido-and-get-a-credential-prompt"></a>FIDO를 사용 하 여 로그인 한 후 NTLM 네트워크 리소스에 대 한 SSO를 가져올 수 없으며 자격 증명 프롬프트를 가져옵니다.

리소스 요청을 처리할 때까지 충분 한 도메인 컨트롤러가 패치 되었는지 확인 합니다. 기능을 실행 하는 도메인 컨트롤러를 볼 수 있는지 확인 하려면의 출력을 검토 `nltest /dsgetdc:contoso /keylist /kdc` 합니다.

## <a name="next-steps"></a>다음 단계

[암호 없는에 대해 자세히 알아보기](concept-authentication-passwordless.md)