---
title: 온-프레미스 Azure AD 암호 보호 모니터링
description: 온-프레미스 Active Directory Domain Services 환경의 Azure AD 암호 보호를 위한 로그를 모니터링 하 고 검토 하는 방법을 알아봅니다.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 02937c22cbc16defb0b7672ac7ebc56c2ae2beb5
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89068782"
---
# <a name="monitor-and-review-logs-for-on-premises-azure-ad-password-protection-environments"></a>온-프레미스 Azure AD 암호 보호 환경에 대 한 로그 모니터링 및 검토

Azure AD 암호 보호의 배포 후 모니터링 및 보고는 필수 작업입니다. 이 문서에서는 각 서비스에서 정보를 기록하는 위치 및 Azure AD 암호 보호 사용에 대한 보고 방법을 포함하여 다양한 모니터링 기술을 이해할 수 있도록 자세히 설명합니다.

모니터링 및 보고는 이벤트 로그 메시지 또는 PowerShell cmdlet을 실행 하 여 수행 됩니다. DC 에이전트와 프록시 서비스는 모두 이벤트 로그 메시지를 기록 합니다. 아래에 설명 된 모든 PowerShell cmdlet은 프록시 서버 에서만 사용할 수 있습니다 (AzureADPasswordProtection PowerShell 모듈 참조). DC 에이전트 소프트웨어는 PowerShell 모듈을 설치 하지 않습니다.

## <a name="dc-agent-event-logging"></a>DC 에이전트 이벤트 로깅

각 도메인 컨트롤러에서 DC 에이전트 서비스 소프트웨어는 각 개별 암호 유효성 검사 작업의 결과(및 기타 상태)를 로컬 이벤트 로그에 씁니다.

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Admin`

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Operational`

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Trace`

DC 에이전트 관리자 로그는 소프트웨어 작동 방식의 주요 정보 소스입니다.

추적 로그는 기본적으로 꺼져 있습니다.

다양한 DC 에이전트 구성 요소가 기록하는 이벤트는 다음 범위에 속합니다.

|구성 요소 |이벤트 ID 범위|
| --- | --- |
|DC 에이전트 암호 필터 dll| 10000-19999|
|DC 에이전트 서비스 호스팅 프로세스| 20000-29999|
|DC 에이전트 서비스 정책 유효성 검사 논리| 30000-39999|

## <a name="dc-agent-admin-event-log"></a>DC 에이전트 관리자 이벤트 로그

### <a name="password-validation-outcome-events"></a>암호 유효성 검사 결과 이벤트

각 도메인 컨트롤러에서 DC 에이전트 서비스 소프트웨어는 각 개별 암호 유효성 검사의 결과를 DC 에이전트 관리자 이벤트 로그에 씁니다.

성공적인 암호 유효성 검사 작업의 경우 일반적으로 DC 에이전트 암호 필터 dll에서 기록된 이벤트가 있습니다. 실패한 암호 유효성 검사 작업의 경우 일반적으로 기록된 두 개의 이벤트 즉, DC 에이전트 서비스 이벤트 및 DC 에이전트 암호 필터 dll 이벤트가 있습니다.

이러한 상황을 캡처하려면 불연속 이벤트가 다음 요인을 기준으로 기록됩니다.

* 기존 암호가 설정되거나 변경됐는지 여부.
* 기존 암호의 유효성 검사 통과 또는 실패 여부.
* Microsoft 전역 정책, 조직 정책 또는 결합된 정책으로 인한 유효성 검사의 실패 여부.
* 감사 전용 모드가 현재 암호 정책에 대해 켜져 있는지 또는 꺼져 있는지 여부.

키 암호-유효성 검사-관련 이벤트는 다음과 같습니다.

| 이벤트 |암호 변경 |암호 설정|
| --- | :---: | :---: |
|합격 |10014 |10015|
|실패(고객 암호 정책으로 인해)| 10016, 30002| 10017, 30003|
|실패(Microsoft 암호 정책으로 인해)| 10016, 30004| 10017, 30005|
|실패(결합된 Microsoft 암호 정책 및 고객 암호 정책으로 인해)| 10016, 30026| 10017, 30027|
|감사 전용 합격(고객 암호 정책 실패)| 10024, 30008| 10025, 30007|
|감사 전용 합격(Microsoft 암호 정책 실패)| 10024, 30010| 10025, 30009|
|감사 전용 성공(결합된 Microsoft 암호 정책 및 고객 암호 정책 실패)| 10024, 30028| 10025, 30029|

위 표에서 “결합된 정책”을 참조하는 사례는 Microsoft 금지 암호 목록과 고객 금지 암호 목록의 토큰을 하나 이상 포함하는 사용자 암호가 발견된 경우를 가리킵니다.

이벤트 쌍이 함께 기록된 경우 동일한 CorrelationId를 사용하여 두 이벤트가 명시적으로 연결됩니다.

### <a name="password-validation-summary-reporting-via-powershell"></a>PowerShell을 통한 암호 유효성 검사 요약 보고

`Get-AzureADPasswordProtectionSummaryReport` cmdlet을 사용하여 암호 유효성 검사 활동의 요약 보기를 생성할 수 있습니다. 이 cmdlet의 출력 예는 다음과 같습니다.

```powershell
Get-AzureADPasswordProtectionSummaryReport -DomainController bplrootdc2
DomainController                : bplrootdc2
PasswordChangesValidated        : 6677
PasswordSetsValidated           : 9
PasswordChangesRejected         : 10868
PasswordSetsRejected            : 34
PasswordChangeAuditOnlyFailures : 213
PasswordSetAuditOnlyFailures    : 3
PasswordChangeErrors            : 0
PasswordSetErrors               : 1
```

– Forest,-Domain 또는 – DomainController 매개 변수 중 하나를 사용 하 여 cmdlet 보고의 범위에 영향을 미칠 수 있습니다. 매개 변수를 지정하지 않는 것은 –Forest를 의미합니다.

`Get-AzureADPasswordProtectionSummaryReport` cmdlet은 DC 에이전트 관리자 이벤트 로그를 쿼리한 다음, 표시된 각 결과 범주에 해당하는 총 이벤트 수를 계산하는 방식으로 작동합니다. 다음 표에는 각 결과와 해당 이벤트 ID 간의 매핑이 포함 되어 있습니다.

|Get-AzureADPasswordProtectionSummaryReport 속성 |해당 이벤트 ID|
| :---: | :---: |
|PasswordChangesValidated |10014|
|PasswordSetsValidated |10015|
|PasswordChangesRejected |10016|
|PasswordSetsRejected |10017|
|PasswordChangeAuditOnlyFailures |10024|
|PasswordSetAuditOnlyFailures |10025|
|PasswordChangeErrors |10012|
|PasswordSetErrors |10013|

`Get-AzureADPasswordProtectionSummaryReport` cmdlet은 PowerShell 스크립트 양식으로 제공되며, 필요한 경우 다음 위치에서 직접 참조될 수 있습니다.

`%ProgramFiles%\WindowsPowerShell\Modules\AzureADPasswordProtection\Get-AzureADPasswordProtectionSummaryReport.ps1`

> [!NOTE]
> 이 cmdlet은 각 도메인 컨트롤러에 대한 PowerShell 세션을 열어 작동합니다. 성공하기 위해 각 도메인 컨트롤러에 PowerShell 원격 세션 지원을 설정해야 하고, 클라이언트에 충분한 권한이 있어야 합니다. PowerShell 원격 세션 요구 사항에 대한 자세한 내용은 PowerShell 창에서 'Get-help about_Remote_Troubleshooting'을 실행합니다.

> [!NOTE]
> 이 cmdlet은 각 DC 에이전트 서비스의 관리 이벤트 로그를 원격으로 쿼리 하는 방식으로 작동 합니다. 이벤트 로그에 대량의 이벤트가 포함되어 있으면 cmdlet이 완료될 때까지 오래 걸릴 수 있습니다. 또한 대량 데이터 집합의 대량 네트워크 쿼리는 도메인 컨트롤러 성능에 영향을 줄 수 있습니다. 따라서 이 cmdlet은 프로덕션 환경에서 신중하게 사용해야 합니다.

### <a name="sample-event-log-message-for-event-id-10014-successful-password-change"></a>이벤트 ID 10014에 대한 샘플 이벤트 로그 메시지(성공적인 암호 변경)

```text
The changed password for the specified user was validated as compliant with the current Azure password policy.

 UserName: BPL_02885102771
 FullName:
```

### <a name="sample-event-log-message-for-event-id-10017-and-30003-failed-password-set"></a>이벤트 ID 10017 및 30003에 대한 샘플 이벤트 로그 메시지(실패한 암호 집합)

10017:

```text
The reset password for the specified user was rejected because it did not comply with the current Azure password policy. Please see the correlated event log message for more details.

 UserName: BPL_03283841185
 FullName:
```

30003:

```text
The reset password for the specified user was rejected because it matched at least one of the tokens present in the per-tenant banned password list of the current Azure password policy.

 UserName: BPL_03283841185
 FullName:
```

### <a name="sample-event-log-message-for-event-id-30001-password-accepted-due-to-no-policy-available"></a>이벤트 ID 30001에 대한 샘플 이벤트 로그 메시지(사용할 수 있는 정책 없음으로 인해 허용된 암호)

```text
The password for the specified user was accepted because an Azure password policy is not available yet

UserName: SomeUser
FullName: Some User

This condition may be caused by one or more of the following reasons:%n

1. The forest has not yet been registered with Azure.

   Resolution steps: an administrator must register the forest using the Register-AzureADPasswordProtectionForest cmdlet.

2. An Azure AD password protection Proxy is not yet available on at least one machine in the current forest.

   Resolution steps: an administrator must install and register a proxy using the Register-AzureADPasswordProtectionProxy cmdlet.

3. This DC does not have network connectivity to any Azure AD password protection Proxy instances.

   Resolution steps: ensure network connectivity exists to at least one Azure AD password protection Proxy instance.

4. This DC does not have connectivity to other domain controllers in the domain.

   Resolution steps: ensure network connectivity exists to the domain.
```

### <a name="sample-event-log-message-for-event-id-30006-new-policy-being-enforced"></a>이벤트 ID 30006에 대한 샘플 이벤트 로그 메시지(적용되는 새 정책)

```text
The service is now enforcing the following Azure password policy.

 Enabled: 1
 AuditOnly: 1
 Global policy date: ‎2018‎-‎05‎-‎15T00:00:00.000000000Z
 Tenant policy date: ‎2018‎-‎06‎-‎10T20:15:24.432457600Z
 Enforce tenant policy: 1
```

### <a name="sample-event-log-message-for-event-id-30019-azure-ad-password-protection-is-disabled"></a>이벤트 ID 30019에 대한 샘플 이벤트 로그 메시지(Azure AD 암호 보호를 사용할 수 없음)

```text
The most recently obtained Azure password policy was configured to be disabled. All passwords submitted for validation from this point on will automatically be considered compliant with no processing performed.

No further events will be logged until the policy is changed.%n

```

## <a name="dc-agent-operational-log"></a>DC 에이전트 작업 로그

DC 에이전트 서비스는 또한 다음 로그에 운영 관련 이벤트를 기록합니다.

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Operational`

## <a name="dc-agent-trace-log"></a>DC 에이전트 추적 로그

DC 에이전트 서비스는 다음 로그에 자세한 디버그 수준 추적 이벤트를 기록할 수도 있습니다.

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Trace`

추적 로그는 기본적으로 사용하지 않도록 설정되어 있습니다.

> [!WARNING]
> 사용하도록 설정되면 추적 로그는 많은 양의 이벤트를 받고 도메인 컨트롤러 성능에 영향을 줄 수 있습니다. 따라서 이 향상된 로그는 문제가 더 세부적인 조사를 필요로 할 때 및 최소한의 시간 동안만 사용하도록 설정돼야 합니다.

## <a name="dc-agent-text-logging"></a>DC 에이전트 텍스트 로깅

다음 레지스트리 값을 설정하여 텍스트 로그에 작성하도록 DC 에이전트 서비스를 구성할 수 있습니다.

```text
HKLM\System\CurrentControlSet\Services\AzureADPasswordProtectionDCAgent\Parameters!EnableTextLogging = 1 (REG_DWORD value)
```

텍스트 로그는 기본적으로 사용하지 않도록 설정되어 있습니다. 이 값에 대한 변경 내용을 적용하려면 DC 에이전트 서비스를 다시 시작해야 합니다. 사용하도록 설정하면 DC 에이전트 서비스는 아래에 있는 로그 파일에 작성합니다.

`%ProgramFiles%\Azure AD Password Protection DC Agent\Logs`

> [!TIP]
> 텍스트 로그는 추적 로그에 기록될 수 있는 동일한 디버그 수준 항목을 받지만 일반적으로 검토하고 분석하기 쉬운 형식입니다.

> [!WARNING]
> 사용하도록 설정되면 이 로그는 많은 양의 이벤트를 받고 도메인 컨트롤러 성능에 영향을 줄 수 있습니다. 따라서 이 향상된 로그는 문제가 더 세부적인 조사를 필요로 할 때 및 최소한의 시간 동안만 사용하도록 설정돼야 합니다.

## <a name="dc-agent-performance-monitoring"></a>DC 에이전트 성능 모니터링

DC 에이전트 서비스 소프트웨어는 **Azure AD 암호 보호**라는 성능 카운터 개체를 설치합니다. 다음 성능 카운터는 현재 사용할 수 있습니다.

|성능 카운터 이름 | 설명|
| --- | --- |
|처리된 암호 |이 카운터는 마지막 다시 시작한 이후에 (승인 또는 거부) 처리된 암호의 총 수를 표시합니다.|
|허용된 암호 |이 카운터는 마지막 다시 시작한 이후에 허용된 암호의 총 수를 표시합니다.|
|거부된 암호 |이 카운터는 마지막 다시 시작한 이후에 거부된 암호의 총 수를 표시합니다.|
|진행 중인 암호 필터 요청 |이 카운터는 현재 진행 중인 암호 필터 요청 수를 표시합니다.|
|최대 암호 필터 요청 |이 카운터는 마지막 다시 시작한 이후에 동시 암호 필터 요청 최대 수를 표시합니다.|
|암호 필터 요청 오류 |이 카운터는 마지막 다시 시작한 이후에 오류로 인해 실패한 암호 필터 요청의 총 수를 표시합니다. Azure AD 암호 보호 DC 에이전트 서비스가 실행 중이 아닐 때 오류가 발생할 수 있습니다.|
|암호 필터 요청 수/초 |이 카운터는 암호를 처리하는 속도를 표시합니다.|
|암호 필터 요청 처리 시간 |이 카운터는 암호 필터 요청을 처리하는 데 필요한 평균 시간을 표시합니다.|
|최대 암호 필터 요청 처리 시간 |이 카운터는 마지막 다시 시작한 이후에 최대 암호 필터 요청 처리 시간을 표시합니다.|
|감사 모드로 인해 허용된 암호 |이 카운터는 보통 거부됐었으나 (마지막 다시 시작한 이후에) 암호 정책이 감사 모드로 구성됐기 때문에 허용된 암호의 총 수를 표시합니다.|

## <a name="dc-agent-discovery"></a>DC 에이전트 검색

`Get-AzureADPasswordProtectionDCAgent` cmdlet은 도메인이나 포리스트에서 실행되는 다양한 DC 에이전트에 대한 기본 정보를 표시하는 데 사용될 수 있습니다. 이 정보는 DC 에이전트 서비스를 실행하여 등록된 serviceConnectionPoint 개체에서 검색됩니다.

이 cmdlet의 출력 예는 다음과 같습니다.

```powershell
Get-AzureADPasswordProtectionDCAgent
ServerFQDN            : bplChildDC2.bplchild.bplRootDomain.com
Domain                : bplchild.bplRootDomain.com
Forest                : bplRootDomain.com
PasswordPolicyDateUTC : 2/16/2018 8:35:01 AM
HeartbeatUTC          : 2/16/2018 8:35:02 AM
```

다양한 속성이 대략적인 시간 단위로 각 DC 에이전트 서비스에 의해 업데이트됩니다. 데이터는 여전히 Active Directory 복제 대기 시간의 적용을 받습니다.

Cmdlet 쿼리의 범위는 – Forest 또는 – Domain 매개 변수 중 하나를 사용 하 여 영향을 받을 수 있습니다.

HeartbeatUTC 값이 부실해지면 해당 도메인 컨트롤러의 Azure AD 암호 보호 DC 에이전트가 실행되고 있지 않거나, 제거되었거나, 머신이 강등되어 더 이상 도메인 컨트롤러가 아님을 나타낼 수 있습니다.

PasswordPolicyDateUTC 값이 오래 된 경우 해당 컴퓨터의 Azure AD 암호 보호 DC 에이전트가 제대로 작동 하지 않을 수 있습니다.

## <a name="dc-agent-newer-version-available"></a>DC 에이전트의 최신 버전 사용 가능

DC 에이전트 서비스는 새 버전의 DC 에이전트 소프트웨어를 사용할 수 있음을 감지 하면 작업 로그에 30034 경고 이벤트를 기록 합니다. 예를 들면 다음과 같습니다.

```text
An update for Azure AD Password Protection DC Agent is available.

If autoupgrade is enabled, this message may be ignored.

If autoupgrade is disabled, refer to the following link for the latest version available:

https://aka.ms/AzureADPasswordProtectionAgentSoftwareVersions

Current version: 1.2.116.0
```

위의 이벤트는 최신 버전의 소프트웨어를 지정 하지 않습니다. 해당 정보에 대 한 이벤트 메시지의 링크로 이동 해야 합니다.

> [!NOTE]
> 위의 이벤트 메시지에서 "autoupgrade"에 대 한 참조에도 불구 하 고 DC 에이전트 소프트웨어는 현재이 기능을 지원 하지 않습니다.

## <a name="proxy-service-event-logging"></a>프록시 서비스 이벤트 로깅

프록시 서비스는 최소 이벤트 세트를 다음 이벤트 로그에 내보냅니다.

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\ProxyService\Admin`

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\ProxyService\Operational`

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\ProxyService\Trace`

추적 로그는 기본적으로 꺼져 있습니다.

> [!WARNING]
> 사용하도록 설정되면 추적 로그는 많은 양의 이벤트를 받고 도메인 프록시 호스트의 성능에 영향을 줄 수 있습니다. 따라서 이 로그는 문제가 더 세부적인 조사를 필요로 할 때 및 최소한의 시간 동안만 사용하도록 설정돼야 합니다.

이벤트는 다음 범위를 사용하여 다양한 프록시 구성 요소에 의해 기록됩니다.

|구성 요소 |이벤트 ID 범위|
| --- | --- |
|프록시 서비스 호스팅 프로세스| 10000-19999|
|프록시 서비스 핵심 비즈니스 논리| 20000-29999|
|PowerShell cmdlet| 30000-39999|

## <a name="proxy-service-text-logging"></a>프록시 서비스 텍스트 로깅

다음 레지스트리 값을 설정하여 텍스트 로그에 작성하도록 프록시 서비스를 구성할 수 있습니다.

HKLM\System\CurrentControlSet\Services\AzureADPasswordProtectionProxy\Parameters!EnableTextLogging = 1 (REG_DWORD 값)

텍스트 로그는 기본적으로 사용하지 않도록 설정되어 있습니다. 이 값에 대한 변경 내용을 적용하려면 프록시 서비스를 다시 시작해야 합니다. 사용하도록 설정하면 프록시 서비스는 아래에 있는 로그 파일에 작성합니다.

`%ProgramFiles%\Azure AD Password Protection Proxy\Logs`

> [!TIP]
> 텍스트 로그는 추적 로그에 기록될 수 있는 동일한 디버그 수준 항목을 받지만 일반적으로 검토하고 분석하기 쉬운 형식입니다.

> [!WARNING]
> 사용하도록 설정되면 이 로그는 많은 양의 이벤트를 받고 머신 성능에 영향을 줄 수 있습니다. 따라서 이 향상된 로그는 문제가 더 세부적인 조사를 필요로 할 때 및 최소한의 시간 동안만 사용하도록 설정돼야 합니다.

## <a name="powershell-cmdlet-logging"></a>PowerShell cmdlet 로깅

상태를 변경하는 PowerShell cmdlet(예: Register-AzureADPasswordProtectionProxy)은 일반적으로 작업 로그에 결과 이벤트를 기록합니다.

또한 대부분의 Azure AD 암호 보호 PowerShell cmdlet은 아래에 있는 텍스트 로그에 기록 합니다.

`%ProgramFiles%\Azure AD Password Protection Proxy\Logs`

cmdlet 오류가 발생하고 원인 및/또는 솔루션이 쉽게 드러나지 않는 경우 이러한 텍스트 로그를 참조할 수도 있습니다.

## <a name="proxy-discovery"></a>프록시 검색

`Get-AzureADPasswordProtectionProxy` cmdlet은 도메인이나 포리스트에서 실행되는 다양한 Azure AD 암호 보호 프록시 서비스에 대한 기본 정보를 표시하는 데 사용될 수 있습니다. 이 정보는 프록시 서비스를 실행하여 등록된 serviceConnectionPoint 개체에서 검색됩니다.

이 cmdlet의 출력 예는 다음과 같습니다.

```powershell
Get-AzureADPasswordProtectionProxy
ServerFQDN            : bplProxy.bplchild2.bplRootDomain.com
Domain                : bplchild2.bplRootDomain.com
Forest                : bplRootDomain.com
HeartbeatUTC          : 12/25/2018 6:35:02 AM
```

다양한 속성이 대략 1시간마다 각 프록시 서비스에 의해 업데이트됩니다. 데이터는 여전히 Active Directory 복제 대기 시간의 적용을 받습니다.

Cmdlet 쿼리의 범위는 – Forest 또는 – Domain 매개 변수 중 하나를 사용 하 여 영향을 받을 수 있습니다.

HeartbeatUTC 값이 부실해지면 해당 머신의 Azure AD 암호 보호 프록시가 실행되지 않거나 제거되었음을 나타낼 수 있습니다.

## <a name="proxy-agent-newer-version-available"></a>프록시 에이전트의 최신 버전을 사용할 수 있습니다.

프록시 서비스는 최신 버전의 프록시 소프트웨어를 사용할 수 있음을 감지 하면 작업 로그에 20002 경고 이벤트를 기록 합니다. 예를 들면 다음과 같습니다.

```text
An update for Azure AD Password Protection Proxy is available.

If autoupgrade is enabled, this message may be ignored.

If autoupgrade is disabled, refer to the following link for the latest version available:

https://aka.ms/AzureADPasswordProtectionAgentSoftwareVersions

Current version: 1.2.116.0
.
```

위의 이벤트는 최신 버전의 소프트웨어를 지정 하지 않습니다. 해당 정보에 대 한 이벤트 메시지의 링크로 이동 해야 합니다.

Autoupgrade enabled를 사용 하 여 프록시 에이전트가 구성 된 경우에도이 이벤트를 내보냅니다.

## <a name="next-steps"></a>다음 단계

[Azure AD 암호 보호 문제 해결](howto-password-ban-bad-on-premises-troubleshoot.md)

전역 및 사용자 지정 금지된 암호 목록에 대한 자세한 내용은 [잘못된 암호 금지](concept-password-ban-bad.md) 문서 참조
