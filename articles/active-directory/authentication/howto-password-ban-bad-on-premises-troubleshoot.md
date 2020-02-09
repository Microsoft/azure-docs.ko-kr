---
title: 암호 보호 문제 해결-Azure Active Directory
description: Azure AD 암호 보호에 대 한 일반적인 문제 해결 이해
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: troubleshooting
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: bd609eb1f289c0a104bddaa08a60e7dc6202acee
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/05/2019
ms.locfileid: "74847663"
---
# <a name="azure-ad-password-protection-troubleshooting"></a>Azure AD 암호 보호 문제 해결

Azure AD 암호 보호를 배포한 후 문제를 해결해야 할 수도 있습니다. 이 문서에서는 몇 가지 일반적인 문제 해결 단계를 이해하는 데 유용한 세부 정보를 제공합니다.

## <a name="the-dc-agent-cannot-locate-a-proxy-in-the-directory"></a>DC 에이전트가 디렉터리에서 프록시를 찾을 수 없습니다.

이 문제의 주요 증상은 DC 에이전트 관리자 이벤트 로그에서 30017 이벤트입니다.

이 문제는 일반적으로 프록시가 아직 등록 되지 않은 경우에 발생 합니다. 프록시가 등록 된 경우 특정 DC 에이전트가 해당 프록시를 볼 수 있을 때까지 AD 복제 대기 시간으로 인해 약간의 지연이 발생할 수 있습니다.

## <a name="the-dc-agent-is-not-able-to-communicate-with-a-proxy"></a>DC 에이전트가 프록시와 통신할 수 없습니다.

이 문제의 주요 증상은 DC 에이전트 관리자 이벤트 로그에서 30018 이벤트입니다. 이 문제에는 여러 가지 원인이 있을 수 있습니다.

1. DC 에이전트가 등록 된 프록시에 대 한 네트워크 연결을 허용 하지 않는 격리 된 네트워크 부분에 있습니다. Azure에서 암호 정책을 다운로드 하기 위해 다른 DC 에이전트가 프록시와 통신할 수 있으면이 문제는 심각 하지 않을 수 있습니다. 다운로드 한 후에는 격리 된 DC에서 sysvol 공유의 정책 파일 복제를 통해 해당 정책을 가져옵니다.

1. 프록시 호스트 컴퓨터가 RPC 엔드포인트 매퍼 엔드포인트 (포트 135)에 대 한 액세스를 차단 하 고 있습니다.

   Azure AD 암호 보호 프록시 설치 관리자는 포트 135에 대 한 액세스를 허용 하는 Windows 방화벽 인바운드 규칙을 자동으로 만듭니다. 이 규칙을 나중에 삭제 하거나 사용 하지 않도록 설정 하면 DC 에이전트는 프록시 서비스와 통신할 수 없습니다. 다른 방화벽 제품 대신 builtin Windows 방화벽을 사용 하지 않도록 설정한 경우 포트 135에 대 한 액세스를 허용 하도록 방화벽을 구성 해야 합니다.

1. 프록시 호스트 컴퓨터가 프록시 서비스에서 수신 하는 RPC 엔드포인트 (동적 또는 정적)에 대 한 액세스를 차단 하 고 있습니다.

   Azure AD 암호 보호 프록시 설치 관리자는 Azure AD 암호 보호 프록시 서비스에서 수신 대기 하는 인바운드 포트에 대 한 액세스를 허용 하는 Windows 방화벽 인바운드 규칙을 자동으로 만듭니다. 이 규칙을 나중에 삭제 하거나 사용 하지 않도록 설정 하면 DC 에이전트는 프록시 서비스와 통신할 수 없습니다. 다른 방화벽 제품 대신 builtin Windows 방화벽을 사용 하지 않도록 설정한 경우 Azure AD 암호 보호 프록시 서비스에서 수신 대기 하는 인바운드 포트에 대 한 액세스를 허용 하도록 방화벽을 구성 해야 합니다. 프록시 서비스가 특정 고정 RPC 포트 (`Set-AzureADPasswordProtectionProxyConfiguration` cmdlet 사용)에서 수신 하도록 구성 된 경우이 구성은 더 구체적으로 지정할 수 있습니다.

1. 도메인 컨트롤러에서 컴퓨터에 로그온 할 수 있도록 프록시 호스트 컴퓨터가 구성 되어 있지 않습니다. 이 동작은 "네트워크에서이 컴퓨터 액세스" 사용자 권한 할당을 통해 제어 됩니다. 포리스트의 모든 도메인에 있는 모든 도메인 컨트롤러에이 권한을 부여 해야 합니다. 이 설정은 일반적으로 대규모 네트워크 강화 작업의 일부로 제약을 받습니다.

## <a name="proxy-service-is-unable-to-communicate-with-azure"></a>프록시 서비스가 Azure와 통신할 수 없습니다.

1. 프록시 컴퓨터가 [배포 요구 사항](howto-password-ban-bad-on-premises-deploy.md)에 나열 된 엔드포인트에 연결 되어 있는지 확인 합니다.

1. 포리스트와 모든 프록시 서버가 동일한 Azure 테넌트에 등록 되었는지 확인 합니다.

   `Get-AzureADPasswordProtectionProxy` 및 `Get-AzureADPasswordProtectionDCAgent` PowerShell cmdlet을 실행 하 고 반환 된 각 항목의 `AzureTenant` 속성을 비교 하 여이 요구 사항을 확인할 수 있습니다. 올바른 작업의 경우 보고 된 테넌트 이름은 모든 DC 에이전트와 프록시 서버에서 동일 해야 합니다.

   Azure 테넌트 등록 불일치 조건이 있는 경우 모든 등록에 대해 동일한 Azure 테넌트의 자격 증명을 사용 하 여 필요에 따라 `Register-AzureADPasswordProtectionProxy` 및/또는 `Register-AzureADPasswordProtectionForest` PowerShell cmdlet을 실행 하 여이 문제를 해결할 수 있습니다.

## <a name="dc-agent-is-unable-to-encrypt-or-decrypt-password-policy-files"></a>DC 에이전트가 암호 정책 파일을 암호화 하거나 암호 해독할 수 없습니다.

Azure AD 암호 보호는 Microsoft 키 배포 서비스에서 제공 하는 암호화 및 암호 해독 기능에 중요 한 종속성이 있습니다. 암호화 또는 암호 해독 오류는 다양 한 증상으로 매니페스트 될 수 있으며 몇 가지 잠재적인 원인이 있습니다.

1. KDS 서비스가 사용 하도록 설정 되어 있고 도메인의 모든 Windows Server 2012 이상 도메인 컨트롤러에서 작동 하는지 확인 합니다.

   기본적으로 KDS 서비스의 서비스 시작 모드는 수동 (트리거 시작)으로 구성 됩니다. 이 구성은 클라이언트가 처음으로 서비스를 사용 하려고 할 때 요청 시 시작 됨을 의미 합니다. Azure AD 암호 보호를 사용 하려면이 기본 서비스 시작 모드를 사용할 수 있습니다.

   KDS 서비스 시작 모드가 사용 안 함으로 구성 된 경우 Azure AD 암호 보호가 제대로 작동 하려면이 구성을 수정 해야 합니다.

   이 문제에 대 한 간단한 테스트는 서비스 관리 MMC 콘솔을 통해 또는 다른 관리 도구 (예: 명령 프롬프트 콘솔에서 "net start kdssvc.dll" 실행)를 사용 하 여 KDS 서비스를 수동으로 시작 하는 것입니다. KDS 서비스가 성공적으로 시작 되 고 계속 실행 되 고 있습니다.

   KDS 서비스를 시작할 수 없는 가장 일반적인 근본 원인은 Active Directory 도메인 컨트롤러 개체가 기본 도메인 컨트롤러 OU의 외부에 위치 하는 것입니다. 이 구성은 KDS 서비스에서 지원 되지 않으며 Azure AD 암호 보호에 적용 되는 제한 사항이 아닙니다. 이 조건에 대 한 해결 방법은 도메인 컨트롤러 개체를 기본 도메인 컨트롤러 OU 아래의 위치로 이동 하는 것입니다.

1. Windows Server 2012 r 2에서 Windows Server 2016로의 호환 되지 않는 KDS 암호화 된 버퍼 형식 변경

   KDS 보안 픽스는 KDS 암호화 된 버퍼의 형식을 수정 하는 Windows Server 2016에서 도입 되었습니다. 이러한 버퍼는 Windows Server 2012 및 Windows Server 2012 r 2에서 암호 해독에 실패할 수 있습니다. 역방향은 Windows server 2012에서 KDS 암호화 된 정상 버퍼 이며 windows server 2012 r 2는 windows Server 2016 이상에서 항상 암호를 해독 합니다. Active Directory 도메인의 도메인 컨트롤러에서 이러한 운영 체제를 혼합 하 여 실행 하는 경우 Azure AD 암호 보호 암호 해독 실패가 가끔 보고 될 수 있습니다. 보안 수정의 특성에 따라 이러한 오류의 타이밍 또는 증상을 정확 하 게 예측할 수 없으며, 지정 된 시간에 도메인 컨트롤러에서 데이터를 암호화 하는 Azure AD 암호 보호 DC 에이전트가 명확 하지 않을 수 있습니다.

   Microsoft는이 문제에 대 한 픽스를 조사 하지만 아직 에타를 사용할 수 없습니다. 그 동안에는 Active Directory 도메인에서 호환 되지 않는 이러한 운영 체제를 혼합 하 여 실행 하지 않도록 하는 것 외에이 문제에 대 한 해결 방법이 없습니다. 즉, windows Server 2012 및 Windows Server 2012 R2 도메인 컨트롤러만 실행 하거나 Windows Server 2016 이상 도메인 컨트롤러만 실행 해야 합니다.

## <a name="weak-passwords-are-being-accepted-but-should-not-be"></a>약한 암호는 허용 되지만

이 문제는 여러 원인이 있을 수 있습니다.

1. DC 에이전트가 만료 된 공개 미리 보기 소프트웨어 버전을 실행 하 고 있습니다. [공개 미리 보기 DC 에이전트 소프트웨어가 만료 되었습니다](howto-password-ban-bad-on-premises-troubleshoot.md#public-preview-dc-agent-software-has-expired).를 참조 하세요.

1. DC 에이전트가 정책을 다운로드할 수 없거나 기존 정책의 암호를 해독할 수 없습니다. 위의 항목에서 가능한 원인을 확인 하십시오.

1. 암호 정책 강제 적용 모드가 여전히 감사로 설정되어 있습니다. 이 구성이 적용 되는 경우 Azure AD 암호 보호 포털을 사용 하 여 적용 하도록 다시 구성 합니다. [암호 보호 사용](howto-password-ban-bad-on-premises-operations.md#enable-password-protection)을 참조 하세요.

1. 암호 정책이 사용하지 않도록 설정되어 있습니다. 이 구성이 적용 되는 경우 Azure AD 암호 보호 포털을 사용 하 여 사용으로 다시 구성 합니다. [암호 보호 사용](howto-password-ban-bad-on-premises-operations.md#enable-password-protection)을 참조 하세요.

1. 도메인의 모든 도메인 컨트롤러에 DC 에이전트 소프트웨어를 설치 하지 않았습니다. 이 경우 원격 Windows 클라이언트가 암호 변경 작업 중에 특정 도메인 컨트롤러를 대상으로 하도록 하는 것은 어렵습니다. DC 에이전트 소프트웨어가 설치 된 특정 DC의 대상이 성공적으로 지정 된 경우에는 DC 에이전트 관리자 이벤트 로그를 두 번 확인 하 여 확인할 수 있습니다. 결과와 상관 없이 암호의 결과를 문서화 하는 이벤트가 하나 이상 있습니다. 유효성 검사. 암호가 변경 된 사용자에 게 존재 하는 이벤트가 없는 경우 다른 도메인 컨트롤러에서 암호 변경을 처리 했을 수 있습니다.

   대체 테스트로, DC 에이전트 소프트웨어가 설치 된 DC에 직접 로그인 한 상태에서 암호를 변경 하는 것을 시도 합니다. 프로덕션 Active Directory 도메인에는이 방법을 사용할 수 없습니다.

   이러한 제한 사항에 따라 DC 에이전트 소프트웨어의 증분 배포를 지원 하지만 도메인의 모든 도메인 컨트롤러에 가능한 한 빨리 DC 에이전트 소프트웨어를 설치 하는 것이 좋습니다.

1. 암호 유효성 검사 알고리즘이 실제로 예상 대로 작동 하 고 있을 수 있습니다. [암호 평가 방법을](concept-password-ban-bad.md#how-are-passwords-evaluated)참조 하세요.

## <a name="ntdsutilexe-fails-to-set-a-weak-dsrm-password"></a>Ntdsutil.exe에서 weak DSRM 암호를 설정 하지 못함

Active Directory는 항상 새 디렉터리 서비스 복구 모드 암호의 유효성을 검사 하 여 도메인의 암호 복잡성 요구 사항을 충족 하는지 확인 합니다. 이 유효성 검사는 또한 Azure AD 암호 보호와 같은 암호 필터 dll을 호출 합니다. 새 DSRM 암호가 거부 되 면 다음과 같은 오류 메시지가 나타납니다.

```text
C:\>ntdsutil.exe
ntdsutil: set dsrm password
Reset DSRM Administrator Password: reset password on server null
Please type password for DS Restore Mode Administrator Account: ********
Please confirm new password: ********
Setting password failed.
        WIN32 Error Code: 0xa91
        Error Message: Password doesn't meet the requirements of the filter dll's
```

Azure AD 암호 보호에서 Active Directory DSRM 암호에 대 한 암호 유효성 검사 이벤트 로그 이벤트를 기록 하는 경우 이벤트 로그 메시지에 사용자 이름이 포함 되지 않습니다. 이 동작은 DSRM 계정이 실제 Active Directory 도메인에 속하지 않는 로컬 계정 이기 때문에 발생 합니다.  

## <a name="domain-controller-replica-promotion-fails-because-of-a-weak-dsrm-password"></a>도메인 컨트롤러 복제본의 승격은 약한 DSRM 암호로 인해 실패 합니다.

DC 수준 올리기 프로세스 중에 유효성 검사를 위해 새 디렉터리 서비스 복구 모드 암호가 도메인의 기존 DC로 전송 됩니다. 새 DSRM 암호가 거부 되 면 다음과 같은 오류 메시지가 나타납니다.

```powershell
Install-ADDSDomainController : Verification of prerequisites for Domain Controller promotion failed. The Directory Services Restore Mode password does not meet a requirement of the password filter(s). Supply a suitable password.
```

위의 문제와 마찬가지로 Azure AD 암호 보호 암호 유효성 검사 결과 이벤트에는이 시나리오에 대 한 빈 사용자 이름이 포함 됩니다.

## <a name="domain-controller-demotion-fails-due-to-a-weak-local-administrator-password"></a>취약 한 로컬 관리자 암호로 인해 도메인 컨트롤러 강등을 실패 합니다.

여전히 DC 에이전트 소프트웨어를 실행하는 도메인 컨트롤러의 수준을 내리기 위해 지원됩니다. 하지만 관리자는 DC 에이전트 소프트웨어가 수준 내리기 절차 중 현재 암호 정책을 계속 실행하고 적용한다는 것을 인식해야 합니다. 새 로컬 관리자 계정 암호(수준 내리기 작업의 일부로 지정됨)는 다른 모든 암호와 같이 유효성을 검사합니다. DC 수준 내리기 절차의 일부로 로컬 관리자 계정에 대해 보안 암호를 선택 하는 것이 좋습니다.

수준 내리기가 성공하고 도메인 컨트롤러가 다시 부팅되고 일반 멤버 서버로 다시 실행되면 DC 에이전트 소프트웨어는 수동 모드 실행으로 되돌아갑니다. 언제든 제거될 수 있습니다.

## <a name="booting-into-directory-services-repair-mode"></a>디렉터리 서비스 복구 모드로 부팅

도메인 컨트롤러가 디렉터리 서비스 복구 모드로 부팅 되는 경우 DC 에이전트 암호 필터 dll은이 조건을 감지 하 고 현재 활성 정책에 관계 없이 모든 암호 유효성 검사 또는 적용 작업을 사용 하지 않도록 설정 합니다. 구성. DC 에이전트 암호 필터 dll은 10023 경고 이벤트를 관리자 이벤트 로그에 기록 합니다. 예를 들면 다음과 같습니다.

```text
The password filter dll is loaded but the machine appears to be a domain controller that has been booted into Directory Services Repair Mode. All password change and set requests will be automatically approved. No further messages will be logged until after the next reboot.
```
## <a name="public-preview-dc-agent-software-has-expired"></a>공용 미리 보기 DC 에이전트 소프트웨어가 만료 되었습니다.

Azure AD 암호 보호 공개 미리 보기 기간 동안 DC 에이전트 소프트웨어는 다음 날짜에 대 한 암호 유효성 검사 요청 처리를 중지 하도록 하드 코딩 되었습니다.

* 버전 1.2.65.0는 9 월 1 2019에 대 한 암호 유효성 검사 요청 처리를 중지 합니다.
* 1 2019 7 월에 대 한 암호 유효성 검사 요청 처리 버전 1.2.25.0 및 이전에 중지 되었습니다.

최종 기한에 따라 시간이 제한 된 모든 DC 에이전트 버전은 부팅 시 DC 에이전트 관리자 이벤트 로그에 10021 이벤트를 내보냅니다. 예를 들면 다음과 같습니다.

```text
The password filter dll has successfully loaded and initialized.

The allowable trial period is nearing expiration. Once the trial period has expired, the password filter dll will no longer process passwords. Please contact Microsoft for an newer supported version of the software.

Expiration date:  9/01/2019 0:00:00 AM

This message will not be repeated until the next reboot.
```

최종 기한이 지나면 시간이 제한 된 모든 DC 에이전트 버전은 부팅 시 DC 에이전트 관리자 이벤트 로그에 10022 이벤트를 내보냅니다. 예를 들면 다음과 같습니다.

```text
The password filter dll is loaded but the allowable trial period has expired. All password change and set requests will be automatically approved. Please contact Microsoft for a newer supported version of the software.

No further messages will be logged until after the next reboot.
```

최종 기한는 초기 부팅 시에만 확인 되므로 달력 최종 기한이 지난 후에도 이러한 이벤트가 표시 되지 않을 수 있습니다. 최종 기한을 인식 한 후에는 모든 암호가 자동으로 승인 되는 것이 아니라 도메인 컨트롤러나 더 큰 환경에 부정적인 영향을 주지 않습니다.

> [!IMPORTANT]
> 만료 된 공개 미리 보기 DC 에이전트는 즉시 최신 버전으로 업그레이드 하는 것이 좋습니다.

업그레이드 해야 하는 사용자 환경에서 DC 에이전트를 검색 하는 쉬운 방법은 `Get-AzureADPasswordProtectionDCAgent` cmdlet을 실행 하는 것입니다. 예를 들면 다음과 같습니다.

```powershell
PS C:\> Get-AzureADPasswordProtectionDCAgent

ServerFQDN            : bpl1.bpl.com
SoftwareVersion       : 1.2.125.0
Domain                : bpl.com
Forest                : bpl.com
PasswordPolicyDateUTC : 8/1/2019 9:18:05 PM
HeartbeatUTC          : 8/1/2019 10:00:00 PM
AzureTenant           : bpltest.onmicrosoft.com
```

이 항목의 경우, 버전 필드는 분명히 확인할 키 속성입니다. PowerShell 필터링을 사용 하 여 이미 필요한 기준 버전 이상인 DC 에이전트를 필터링 할 수도 있습니다. 예를 들면 다음과 같습니다.

```powershell
PS C:\> $LatestAzureADPasswordProtectionVersion = "1.2.125.0"
PS C:\> Get-AzureADPasswordProtectionDCAgent | Where-Object {$_.SoftwareVersion -lt $LatestAzureADPasswordProtectionVersion}
```

Azure AD 암호 보호 프록시 소프트웨어는 어떤 버전 에서도 시간이 제한 되지 않습니다. DC와 프록시 에이전트는 모두 릴리스 될 때 최신 버전으로 업그레이드 하는 것이 좋습니다. `Get-AzureADPasswordProtectionProxy` cmdlet을 사용 하 여 DC 에이전트에 대 한 위의 예제와 유사 하 게 업그레이드를 필요로 하는 프록시 에이전트를 찾을 수 있습니다.

특정 업그레이드 절차에 대 한 자세한 내용은 [DC 에이전트 업그레이드](howto-password-ban-bad-on-premises-deploy.md#upgrading-the-dc-agent) 및 [프록시 에이전트 업그레이드](howto-password-ban-bad-on-premises-deploy.md#upgrading-the-proxy-agent) 를 참조 하세요.

## <a name="emergency-remediation"></a>응급 업데이트 관리

DC 에이전트 서비스가 문제를 일으키는 상황이 발생하면 DC 에이전트 서비스는 즉시 종료될 수 있습니다. DC 에이전트 암호 필터 dll은 여전히 실행 중이 아닌 서비스를 호출하고 경고 이벤트(10012, 10013)를 기록하지만 해당 시간 동안 들어오는 모든 암호는 허용됩니다. DC 에이전트 서비스는 필요에 따라 "Disabled" 시작 유형을 사용하여 Windows 서비스 제어 관리자를 통해 구성될 수 있습니다.

다른 수정 측정값을 Azure AD 암호 보호 포털에서 사용 모드를 안 함으로 설정할 수 있습니다. 업데이트된 정책이 다운로드되면, 각 DC 에이전트 서비스는 모든 암호가 있는 그대로 허용되는 정지 모드가 됩니다. 자세한 내용은 [적용 모드](howto-password-ban-bad-on-premises-operations.md#enforce-mode)를 참조하세요.

## <a name="removal"></a>제거

Azure AD 암호 보호 소프트웨어를 제거 하 고 도메인 및 포리스트에서 관련 된 모든 상태를 정리 하기로 결정 한 경우 다음 단계를 사용 하 여이 작업을 수행할 수 있습니다.

> [!IMPORTANT]
> 이러한 단계를 순서대로 수행하는 것이 중요합니다. 프록시 서비스의 인스턴스가 실행되는 경우 주기적으로 해당 serviceConnectionPoint 개체를 다시 만듭니다. DC 에이전트 서비스의 인스턴스가 실행되는 경우 주기적으로 해당 serviceConnectionPoint 개체 및 sysvol 상태를 다시 만듭니다.

1. 모든 머신에서 프록시 소프트웨어를 제거합니다. 이 단계는 재부팅이 필요하지 **않습니다**.
2. 모든 도메인 컨트롤러에서 DC 에이전트 소프트웨어를 제거합니다. 이 단계는 재부팅이 **필요합니다**.
3. 각 도메인 명명 컨텍스트에서 모든 프록시 서비스 연결점을 수동으로 제거합니다. 다음 Active Directory PowerShell 명령을 사용하여 이러한 개체의 위치를 검색할 수 있습니다.

   ```powershell
   $scp = "serviceConnectionPoint"
   $keywords = "{ebefb703-6113-413d-9167-9f8dd4d24468}*"
   Get-ADObject -SearchScope Subtree -Filter { objectClass -eq $scp -and keywords -like $keywords }
   ```

   $keywords 변수 값의 끝에 별표("*")를 생략하지 마십시오.

   `Get-ADObject` 명령을 통해 찾은 결과 개체는 `Remove-ADObject`에 파이프되거나 수동으로 삭제될 수 있습니다.

4. 각 도메인 명명 컨텍스트에서 모든 DC 에이전트 연결점을 수동으로 제거합니다. 이러한 개체는 해당 소프트웨어의 배포 방법에 따라 포리스트의 도메인 컨트롤러 마다 하나씩 있을 수 있습니다. 다음 Active Directory PowerShell 명령을 사용하여 해당 개체의 위치를 검색할 수 있습니다.

   ```powershell
   $scp = "serviceConnectionPoint"
   $keywords = "{2bac71e6-a293-4d5b-ba3b-50b995237946}*"
   Get-ADObject -SearchScope Subtree -Filter { objectClass -eq $scp -and keywords -like $keywords }
   ```

   `Get-ADObject` 명령을 통해 찾은 결과 개체는 `Remove-ADObject`에 파이프되거나 수동으로 삭제될 수 있습니다.

   $keywords 변수 값의 끝에 별표("*")를 생략하지 마십시오.

5. 포리스트 수준의 구성 상태를 수동으로 제거합니다. 포리스트 구성 상태는 Active Directory 구성 명명 컨텍스트의 컨테이너에 유지됩니다. 다음과 같이 검색 및 삭제될 수 있습니다.

   ```powershell
   $passwordProtectionConfigContainer = "CN=Azure AD Password Protection,CN=Services," + (Get-ADRootDSE).configurationNamingContext
   Remove-ADObject -Recursive $passwordProtectionConfigContainer
   ```

6. 수동으로 다음 폴더와 해당 내용 모두를 삭제하여 모든 sysvol 관련 상태를 수동으로 제거합니다.

   `\\<domain>\sysvol\<domain fqdn>\AzureADPasswordProtection`

   필요한 경우 이 경로는 기존 도메인 컨트롤러에서 로컬로 액세스될 수도 있으며, 기본 위치는 다음 경로와 같이 표시됩니다.

   `%windir%\sysvol\domain\Policies\AzureADPasswordProtection`

   Sysvol 공유가 기본이 아닌 위치에 구성된 경우 이 경로는 다릅니다.

## <a name="next-steps"></a>다음 단계

[Azure AD 암호 보호에 대한 질문과 대답](howto-password-ban-bad-on-premises-faq.md)

전역 및 사용자 지정 금지된 암호 목록에 대한 자세한 내용은 [잘못된 암호 금지](concept-password-ban-bad.md) 문서 참조
