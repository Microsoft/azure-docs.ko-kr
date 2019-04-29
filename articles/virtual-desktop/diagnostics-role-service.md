---
title: Windows 가상 데스크톱 미리 보기 진단 기능-Azure 사용 하 여 문제를 식별 합니다.
description: Windows 가상 데스크톱 미리 보기 진단 기능 및 사용 하는 방법을 설명 합니다.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: helohr
ms.openlocfilehash: 6b79a26d63c02dd06b62ea6ad09941f947704dc0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60870492"
---
# <a name="identify-issues-with-the-diagnostics-feature"></a>진단 기능을 사용하여 문제 식별

Windows 가상 데스크톱 미리 보기를 통해 관리자는 단일 인터페이스를 통해 문제를 식별 하는 진단 기능을 제공 합니다. Windows 가상 데스크톱 역할 시스템와 상호 작용할 때마다 진단 활동을 로그입니다. 각 로그는 트랜잭션, 오류 메시지, 테 넌 트 정보 및 사용자 정보에 포함 된 Windows 가상 데스크톱 역할 등 관련 정보를 포함합니다. 진단 활동 최종 사용자와 관리 작업에서 생성 되 고 주 세 개 버킷으로 분류할 수 있습니다.

* 구독 작업 피드: Microsoft 원격 데스크톱 응용 프로그램을 통해 해당 피드에 연결 하려고 할 때마다 최종 사용자가 이러한 작업을 트리거합니다.
* 연결 작업: Microsoft 원격 데스크톱 응용 프로그램을 통해 데스크톱 또는 RemoteApp에 연결 하려고 할 때마다 최종 사용자가 이러한 작업을 트리거합니다.
* 관리 작업: 관리자가 호스트 풀을 만들고 앱 그룹에 사용자를 할당, 역할 할당을 만드는 등 시스템의 관리 작업을 수행할 때마다 이러한 작업을 트리거합니다.
  
자체 진단 역할 서비스를 Windows 가상 데스크톱의 일부 이므로 진단 결과에서 Windows 가상 데스크톱에 도달 하지 하는 연결에 표시 되지 않습니다. Windows 가상 데스크톱 연결 문제는 최종 사용자가 네트워크 연결 문제가 발생 하는 경우에 발생할 수 있습니다.

시작 하려면 [다운로드 하 여 Windows 가상 데스크톱 PowerShell 모듈을 가져옵니다](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) 아직 없는 경우 PowerShell 세션에서 사용 하도록 합니다.

## <a name="diagnose-issues-with-powershell"></a>PowerShell 사용 하 여 문제 진단

Windows 가상 데스크톱 진단 하나만 PowerShell cmdlet을 사용 하지만 많은 선택적 매개 변수를 더 쉽게 파악할 하 고 문제를 격리를 포함 합니다. 다음 섹션에서는 문제를 진단 하는 동안 실행할 수 있습니다 cmdlet을 나열 합니다. 대부분의 필터는 함께 적용할 수 있습니다. 와 같이 대괄호에 나열 된 값 `<tenantName>`, 상황에 적용 되는 값으로 바꿔야 합니다.

### <a name="retrieve-diagnostic-activities-in-your-tenant"></a>테 넌 트의 진단 활동 검색

입력 하 여 진단 활동을 검색할 수 있습니다 합니다 **Get RdsDiagnosticActivities** cmdlet. 다음 예제 cmdlet는 가장 최근부터 가장 정렬 하는 진단 활동 목록을 반환 합니다.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName>
```

기타 Windows 가상 데스크톱 PowerShell cmdlet과 마찬가지로 사용 해야 합니다는 **-TenantName** 매개 변수를 쿼리에 대 한 사용 하려는 테 넌 트의 이름을 지정 합니다. 테 넌 트 이름을 거의 모든 진단 활동 쿼리에 대해 적용 됩니다.

### <a name="retrieve-detailed-diagnostic-activities"></a>자세한 진단 활동 검색

합니다 **-자세한** 매개 변수는 반환 된 각 진단 활동에 대 한 추가 세부 정보를 제공 합니다. 각 작업에 대 한 형식은 해당 활동 유형에 따라 달라 집니다. 합니다 **-자세한** 에 매개 변수를 추가할 수 있습니다 **Get RdsDiagnosticActivities** 다음 예와에서 같이 쿼리 합니다.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -Detailed
```

### <a name="retrieve-a-specific-diagnostic-activity-by-activity-id"></a>특정 진단 활동에서 활동 ID를 검색 합니다.

합니다 **ActivityId** 매개 변수는 특정 반환 존재 하는 경우 다음 예제 cmdlet에서와 같이 진단 활동입니다.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -ActivityId <ActivityIdGuid>
```

### <a name="filter-diagnostic-activities-by-user"></a>사용자가 진단 활동 필터링

합니다 **-UserName** 매개 변수는 다음 예제 cmdlet에서와 같이 지정된 된 사용자에 의해 시작 되는 진단 작업의 목록을 반환 합니다.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -UserName <UserUPN>
```

합니다 **-UserName** 다른 선택적 필터링 매개 변수를 사용 하 여 매개 변수를 결합할 수도 있습니다.

### <a name="filter-diagnostic-activities-by-time"></a>진단 작업 시간을 기준으로 필터링

반환 되는 진단 활동 목록을 필터링 할 수 있습니다 합니다 **-StartTime** 하 고 **-EndTime** 매개 변수입니다. 합니다 **-StartTime** 매개 변수는 다음 예와에서 같이 특정 날짜에서 시작 진단 활동 목록을 반환 합니다.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -StartTime "08/01/2018"
```

합니다 **-EndTime** 매개 변수를 사용 하 여 cmdlet에 추가할 수 있습니다 합니다 **-StartTime** 에 대 한 결과 수신 하려는 특정 기간을 지정 하려면 매개 변수입니다. 다음 예제 cmdlet는 8 월 1 일 및 월 10 사이 있는 진단 활동의 목록을 반환 합니다.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -StartTime "08/01/2018" -EndTime "08/10/2018"
```

합니다 **-StartTime** 하 고 **-EndTime** 다른 선택적 필터링 매개 변수를 사용 하 여 매개 변수를 결합할 수도 있습니다.

### <a name="filter-diagnostic-activities-by-activity-type"></a>작업 유형별 진단 활동 필터링

사용 하 여 활동 형식별으로 진단 활동을 필터링 할 수도 있습니다는 **-ActivityType** 매개 변수입니다. 다음 cmdlet은 최종 사용자 연결의 목록을 반환 합니다.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -ActivityType Connection
```

다음 cmdlet은 관리자 관리 작업의 목록을 반환 합니다.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -ActivityType Management
```

합니다 **Get RdsDiagnosticActivities** cmdlet는 ActivityType로 피드를 지정를 현재 지원 하지 않습니다.

### <a name="filter-diagnostic-activities-by-outcome"></a>진단 작업 결과 필터링

사용 하 여 결과 반환 되는 진단 활동 목록 필터링 할 수 있습니다 합니다 **-결과** 매개 변수입니다. 다음 예제 cmdlet는 성공적인 진단 활동의 목록을 반환 합니다.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -Outcome Success
```

다음 예제 cmdlet는 실패 한 진단 작업의 목록을 반환 합니다.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -Outcome Failure
```

합니다 **-결과** 다른 선택적 필터링 매개 변수를 사용 하 여 매개 변수를 결합할 수도 있습니다.

## <a name="common-error-scenarios"></a>일반적인 오류 시나리오

오류 시나리오는 서비스의 내부 및 외부 Windows 가상 데스크톱에 분류 되어 있습니다.

* 내부 문제: 테 넌 트 관리자가 완화 될 수 없습니다 및 지원 문제를 해결 해야 하는 시나리오를 지정 합니다. 티켓을 발생 시키는 제공할 때 작업 ID, 테 넌 트 이름 및 대략적인 시간 문제가 발생 했습니다.
* 외부 문제: 시스템 관리자가 완화 될 수 있는 시나리오와 관련이 있습니다. 이들은 Windows 가상 데스크톱에 외부입니다.

다음 표에서 관리자 발생할 수 있는 일반적인 오류를 나열 합니다.

>[!NOTE]
>이 미리 보기 오류에 대 한 전체 분류를 포함 하지 않습니다 하 고 정기적으로 업데이트 됩니다. 최신 정보를 유지 하도록 한 달에 한 번 이상이 문서에 다시 확인 해야 합니다.

### <a name="external-management-error-codes"></a>외부 관리 오류 코드

|숫자 코드|오류 코드|제안 된 솔루션|
|---|---|---|
|3|UnauthorizedAccess|관리자 PowerShell cmdlet을 실행 하려고 했습니다. 사용자 권한이 없는 또는 사용자 이름을 잘못 입력 합니다.|
|1000|TenantNotFound|테 넌 트 이름을 입력 한 모든 기존 테 넌 트를 일치 하지 않습니다. 테 넌 트 이름 입력 오류를 검토 하 고 다시 시도 하세요.|
|1006|TenantCannotBeRemovedHasSessionHostPools|개체를 포함 하는 것 처럼 테 넌 트를 삭제할 수 없습니다. 세션 호스트 풀을 먼저 삭제 한 다음 다시 시도 하세요.|
|2000|HostPoolNotFound|호스트 풀 이름을 입력 한 모든 기존 호스트 풀이 일치 하지 않습니다. 호스트 풀 이름 입력 오류를 검토 하 고 다시 시도 하세요.|
|2005|HostPoolCannotBeRemovedHasApplicationGroups|개체를 포함 하는 것으로 호스트 풀을 삭제할 수 없습니다. 먼저 호스트 풀의 모든 앱 그룹을 제거 합니다.|
|2004|HostPoolCannotBeRemovedHasSessionHosts|먼저 이전 세션 호스트 풀을 삭제 하는 모든 세션 호스트를 제거 합니다.|
|5001|SessionHostNotFound|쿼리 세션 호스트를 오프 라인 상태가 될 수 있습니다. 호스트 풀의 상태를 확인 합니다.|
|5008|SessionHostUserSessionsExist |의도 한 관리 작업을 실행 하기 전에 세션 호스트의 모든 사용자가 로그 아웃 해야 있습니다.|
|6000|AppGroupNotFound|앱 그룹 이름을 입력 한 모든 기존 앱 그룹을 일치 하지 않습니다. 앱 그룹 이름 입력 오류를 검토 하 고 다시 시도 하세요.|
|6022|RemoteAppNotFound|RemoteApp 이름을 입력 한 모든 Remoteapp 일치 하지 않습니다. RemoteApp 이름 입력 오류를 검토 하 고 다시 시도 하세요.|
|6010|PublishedItemsExist|게시 하려는 리소스의 이름을 이미 존재 하는 리소스와 같습니다. 리소스 이름을 변경 하 고 다시 시도 하세요.|
|7002|NameNotValidWhiteSpace|이름에 공백 문자를 사용 하지 마세요.|
|8000|InvalidAuthorizationRoleScope|역할 이름을 입력 한 모든 기존 역할 이름이 일치 하지 않습니다. 역할 이름을 입력 오류를 검토 하 고 다시 시도 하세요. |
|8001|UserNotFound |사용자 이름을 입력 한 모든 기존 사용자 이름과 일치 하지 않습니다. 이름 입력 오류를 검토 하 고 다시 시도 합니다.|
|8005|UserNotFoundInAAD |사용자 이름을 입력 한 모든 기존 사용자 이름과 일치 하지 않습니다. 이름 입력 오류를 검토 하 고 다시 시도 합니다.|
|8008|TenantConsentRequired|지침을 따릅니다 [여기](tenant-setup-azure-active-directory.md#grant-azure-active-directory-permissions-to-the-windows-virtual-desktop-preview-service) 테 넌 트에 대 한 동의 제공 합니다.|

### <a name="external-connection-error-codes"></a>외부 연결 오류 코드

|숫자 코드|오류 코드|제안 된 솔루션|
|---|---|---|
|-2147467259|ConnectionFailedAdTrustedRelationshipFailure|세션 호스트가 Active Directory에 제대로 연결 되지 않았습니다.|
|-2146233088|ConnectionFailedUserHasValidSessionButRdshIsUnhealthy|연결 세션 호스트를 사용할 수 없기 때문에 실패 했습니다. 세션 호스트의 상태를 확인 합니다.|
|-2146233088|ConnectionFailedClientDisconnect|이 오류를 자주 표시 되 면 사용자의 컴퓨터가 네트워크에 연결 되어 있는지 확인 합니다.|
|-2146233088|ConnectionFailedNoHealthyRdshAvailable|호스트 사용자에 연결 하려고 하는 세션 정상 상태가 아니며 가상 컴퓨터를 디버그 합니다.|
|-2146233088|ConnectionFailedUserNotAuthorized|사용자는 게시 된 앱 또는 데스크톱에 액세스할 수 있는 권한이 없습니다. 관리자는 게시 된 리소스를 제거한 후에 오류가 나타날 수 있습니다. 원격 데스크톱 응용 프로그램에서 피드를 새로 고치려면 사용자에 게 요청 합니다.|
|2|FileNotFound|사용자가 액세스 하려고 하는 응용 프로그램 올바르게 설치 되었거나 잘못 된 경로로 설정 합니다.|
|3|InvalidCredentials|모든 기존 사용자 이름이 나 암호를 사용자 이름 또는 사용자가 입력 한 암호가 일치 하지 않습니다. 입력 오류에 대 한 자격 증명을 검토 하 고 다시 시도 하세요.|
|8|ConnectionBroken|클라이언트 및 게이트웨이 또는 서버 간의 연결이 끊어졌습니다. 작업이 예기치 않게 발생 하지 않으면 필요 하지 않습니다.|
|14|UnexpectedNetworkDisconnect|네트워크에 대 한 연결을 삭제 합니다. 사용자가 다시 연결을 요청 합니다.|
|24|ReverseConnectFailed|호스트 가상 컴퓨터에 없는 직접 시야를 RD 게이트웨이. 게이트웨이 IP 주소를 확인할 수 있는지 확인 합니다.|

## <a name="next-steps"></a>다음 단계

Windows Virtual Desktop 내에서 역할에 대 한 자세한 내용은 참조 하세요 [Windows 가상 데스크톱 미리 보기 환경](environment-setup.md)합니다.

Windows 가상 데스크톱, 사용할 수 있는 PowerShell cmdlet의 목록을 보려면 참조는 [PowerShell 참조](/powershell/windows-virtual-desktop/overview)합니다.
