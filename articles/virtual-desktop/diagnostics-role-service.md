---
title: Windows 가상 데스크톱 진단 문제-Azure
description: Windows 가상 데스크톱 진단 기능을 사용 하 여 문제를 진단 하는 방법입니다.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: helohr
ms.openlocfilehash: 0e04b075259ed1d003df4a03686d46b1adf694d3
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2019
ms.locfileid: "73606853"
---
# <a name="identify-and-diagnose-issues"></a>문제 식별 및 진단

Windows 가상 데스크톱은 관리자가 단일 인터페이스를 통해 문제를 식별할 수 있도록 하는 진단 기능을 제공 합니다. Windows 가상 데스크톱 역할은 사용자가 시스템과 상호 작용할 때마다 진단 활동을 기록 합니다. 각 로그에는 트랜잭션과 관련 된 Windows 가상 데스크톱 역할, 오류 메시지, 테넌트 정보 및 사용자 정보와 같은 관련 정보가 포함 되어 있습니다. 진단 작업은 최종 사용자 및 관리 작업에 의해 생성 되며 세 가지 주요 버킷으로 분류할 수 있습니다.

* 피드 구독 활동: 최종 사용자가 Microsoft 원격 데스크톱 응용 프로그램을 통해 피드에 연결 하려고 할 때마다 이러한 활동을 트리거합니다.
* 연결 작업: 최종 사용자가 Microsoft 원격 데스크톱 응용 프로그램을 통해 데스크톱 또는 RemoteApp에 연결 하려고 할 때마다 이러한 작업을 트리거합니다.
* 관리 활동: 관리자가 시스템에서 관리 작업을 수행할 때마다 (예: 호스트 풀 만들기, 앱 그룹에 사용자 할당, 역할 할당 만들기) 이러한 활동을 트리거합니다.
  
진단 역할 서비스 자체는 Windows 가상 데스크톱의 일부 이기 때문에 Windows 가상 데스크톱에 연결 되지 않은 연결은 진단 결과에 표시 되지 않습니다. Windows 가상 데스크톱 연결 문제는 최종 사용자에 게 네트워크 연결 문제가 발생 하는 경우에 발생할 수 있습니다.

시작 하려면 PowerShell 세션에서 사용할 [Windows 가상 데스크톱 powershell 모듈을 다운로드 하 고 가져옵니다](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) (아직 없는 경우). 그런 후, 다음 cmdlet을 실행하여 계정에 로그인합니다.

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="diagnose-issues-with-powershell"></a>PowerShell을 사용 하 여 문제 진단

Windows 가상 데스크톱 진단은 하나의 PowerShell cmdlet만 사용 하 고 여러 개의 선택적 매개 변수를 포함 하 여 문제를 축소 하 고 격리할 수 있도록 합니다. 다음 섹션에는 문제를 진단 하기 위해 실행할 수 있는 cmdlet이 나열 되어 있습니다. 대부분의 필터는 함께 적용할 수 있습니다. 괄호 안에 표시 되는 값 (예: `<tenantName>`)은 사용자의 상황에 적용 되는 값으로 바꾸어야 합니다.

### <a name="retrieve-diagnostic-activities-in-your-tenant"></a>테넌트에서 진단 활동을 검색 합니다.

**RdsDiagnosticActivities** cmdlet을 입력 하 여 진단 활동을 검색할 수 있습니다. 다음 예제 cmdlet은 가장 최근부터 최소까지 정렬 된 진단 활동 목록을 반환 합니다.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName>
```

다른 Windows 가상 데스크톱 PowerShell cmdlet과 마찬가지로 **-tenantname** 매개 변수를 사용 하 여 쿼리에 사용 하려는 테넌트의 이름을 지정 해야 합니다. 테넌트 이름은 거의 모든 진단 활동 쿼리에 적용 됩니다.

### <a name="retrieve-detailed-diagnostic-activities"></a>자세한 진단 작업 검색

**-Detailed** 매개 변수는 반환 되는 각 진단 작업에 대 한 추가 세부 정보를 제공 합니다. 각 활동의 형식은 활동 유형에 따라 달라 집니다. **-Detailed** 매개 변수는 다음 예제와 같이 **RdsDiagnosticActivities** 쿼리에 추가할 수 있습니다.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -Detailed
```

### <a name="retrieve-a-specific-diagnostic-activity-by-activity-id"></a>활동 ID로 특정 진단 활동 검색

다음 예제 cmdlet에 표시 된 것 처럼-작업 매개 변수는 특정 진단 작업 (있는 경우 **)** 을 반환 합니다.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -ActivityId <ActivityIdGuid>
```

### <a name="view-error-messages-for-a-failed-activity-by-activity-id"></a>작업 ID 별로 실패 한 작업에 대 한 오류 메시지 보기

실패 한 작업에 대 한 오류 메시지를 보려면 **-Detailed** 매개 변수를 사용 하 여 cmdlet을 실행 해야 합니다. **Select-Object** cmdlet을 실행 하 여 오류 목록을 볼 수 있습니다.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantname> -ActivityId <ActivityGuid> -Detailed | Select-Object -ExpandProperty Errors
```

### <a name="filter-diagnostic-activities-by-user"></a>사용자별 진단 작업 필터링

**-UserName** 매개 변수는 다음 예제 cmdlet와 같이 지정 된 사용자가 시작 하는 진단 활동의 목록을 반환 합니다.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -UserName <UserUPN>
```

**-UserName** 매개 변수를 다른 선택적 필터링 매개 변수와 함께 사용할 수도 있습니다.

### <a name="filter-diagnostic-activities-by-time"></a>시간별 진단 작업 필터링

**-StartTime** 및 **-EndTime** 매개 변수를 사용 하 여 반환 된 진단 작업 목록을 필터링 할 수 있습니다. **-StartTime** 매개 변수는 다음 예제와 같이 특정 날짜에서 시작 하는 진단 활동 목록을 반환 합니다.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -StartTime "08/01/2018"
```

\- **EndTime** 매개 변수를 cmdlet에 추가 하 여 결과를 수신 하려는 특정 기간을 지정할 수 있습니다. 다음 예제 cmdlet은 8 월 1 일부 터 8 월 10 일 사이에서 진단 활동의 목록을 반환 합니다.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -StartTime "08/01/2018" -EndTime "08/10/2018"
```

**-StartTime** 및 **-EndTime** 매개 변수는 다른 선택적 필터링 매개 변수와 함께 사용할 수도 있습니다.

### <a name="filter-diagnostic-activities-by-activity-type"></a>활동 유형별 진단 작업 필터링

**-ActivityType** 매개 변수를 사용 하 여 활동 유형별로 진단 활동을 필터링 할 수도 있습니다. 다음 cmdlet은 최종 사용자 연결의 목록을 반환 합니다.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -ActivityType Connection
```

다음 cmdlet은 관리자 관리 작업의 목록을 반환 합니다.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -ActivityType Management
```

**RdsDiagnosticActivities** cmdlet은 현재 ActivityType로 피드를 지정 하는 것을 지원 하지 않습니다.

### <a name="filter-diagnostic-activities-by-outcome"></a>결과로 진단 작업 필터링

반환 된 진단 작업 목록을 결과에 따라 **-결과** 매개 변수로 필터링 할 수 있습니다. 다음 예제 cmdlet은 성공적인 진단 활동의 목록을 반환 합니다.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -Outcome Success
```

다음 예제 cmdlet은 실패 한 진단 활동의 목록을 반환 합니다.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -Outcome Failure
```

**-결과** 매개 변수를 다른 선택적 필터링 매개 변수와 결합할 수도 있습니다.

## <a name="common-error-scenarios"></a>일반적인 오류 시나리오

오류 시나리오는 서비스 내부와 Windows 가상 데스크톱 외부에서 분류 됩니다.

* 내부 문제: 테넌트 관리자가 완화할 수 없고 지원 문제를 해결 해야 하는 시나리오를 지정 합니다. [Windows 가상 데스크톱 기술 커뮤니티](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)를 통해 피드백을 제공할 때 문제가 발생 했을 때의 작업 ID와 대략적인 시간 프레임을 포함 합니다.
* 외부 문제: 시스템 관리자가 완화할 수 있는 시나리오와 관련이 있습니다. 이러한 가상 데스크톱은 Windows 가상 데스크톱의 외부에 있습니다.

다음 표에는 관리자가 실행할 수 있는 일반적인 오류가 정리 되어 있습니다.

>[!NOTE]
>이 목록에는 가장 일반적인 오류가 포함 되며 일반 흐름에서 업데이트 됩니다. 최신 정보를 확인 하려면이 문서를 한 달에 한 번 이상 확인 해야 합니다.

### <a name="external-management-error-codes"></a>외부 관리 오류 코드

|숫자 코드|오류 코드|제안 된 솔루션|
|---|---|---|
|3|UnauthorizedAccess|관리 PowerShell cmdlet을 실행 하려는 사용자에 게 해당 사용자 이름을 잘못 입력 하거나 이름을 잘못 입력 한 권한이 없습니다.|
|1000|TenantNotFound|입력 한 테넌트 이름이 기존 테넌트와 일치 하지 않습니다. 오타가 있는지 테넌트 이름을 검토 하 고 다시 시도 하세요.|
|1006|TenantCannotBeRemovedHasSessionHostPools|개체를 포함 하는 경우 테넌트를 삭제할 수 없습니다. 먼저 세션 호스트 풀을 삭제 한 후 다시 시도 하십시오.|
|2000|HostPoolNotFound|입력 한 호스트 풀 이름이 기존 호스트 풀과 일치 하지 않습니다. 오타가 있는지 호스트 풀 이름을 검토 하 고 다시 시도 하세요.|
|2005|HostPoolCannotBeRemovedHasApplicationGroups|개체를 포함 하는 경우에는 호스트 풀을 삭제할 수 없습니다. 먼저 호스트 풀의 모든 앱 그룹을 제거 합니다.|
|2004|HostPoolCannotBeRemovedHasSessionHosts|세션 호스트 풀을 삭제 하기 전에 먼저 모든 세션 호스트를 제거 하십시오.|
|5001|SessionHostNotFound|쿼리 된 세션 호스트가 오프 라인 상태일 수 있습니다. 호스트 풀의 상태를 확인 합니다.|
|5008|Sessionhostusersession는 |원하는 관리 작업을 실행 하기 전에 세션 호스트의 모든 사용자를 로그 아웃 해야 합니다.|
|6000|AppGroupNotFound|입력 한 앱 그룹 이름이 기존 앱 그룹과 일치 하지 않습니다. 오타가 있는지 앱 그룹 이름을 검토 하 고 다시 시도 하세요.|
|6022|RemoteAppNotFound|입력 한 RemoteApp 이름이 어떤 Remoteapp 일치 하지 않습니다. 오타가 있는지 RemoteApp 이름을 검토 하 고 다시 시도 하세요.|
|6010|PublishedItemsExist|게시 하려고 하는 리소스의 이름이 이미 있는 리소스와 같습니다. 리소스 이름을 변경 하 고 다시 시도 하세요.|
|7002|NameNotValidWhiteSpace|이름에 공백을 사용 하지 마십시오.|
|8000|InvalidAuthorizationRoleScope|입력 한 역할 이름이 기존 역할 이름과 일치 하지 않습니다. 오타가 있는지 역할 이름을 검토 하 고 다시 시도 하세요. |
|8001|UserNotFound |입력 한 사용자 이름이 기존 사용자 이름과 일치 하지 않습니다. 오타가 있는지 확인 하 고 다시 시도 하세요.|
|8005|UserNotFoundInAAD |입력 한 사용자 이름이 기존 사용자 이름과 일치 하지 않습니다. 오타가 있는지 확인 하 고 다시 시도 하세요.|
|8008|TenantConsentRequired|테넌트에 대 한 동의를 제공 하려면 [여기](tenant-setup-azure-active-directory.md#grant-permissions-to-windows-virtual-desktop) 의 지침을 따르세요.|

### <a name="external-connection-error-codes"></a>외부 연결 오류 코드

|숫자 코드|오류 코드|제안 된 솔루션|
|---|---|---|
|-2147467259|Connectionfailedad를 연결 하지 못했습니다.|세션 호스트가 Active Directory에 올바르게 조인 되어 있지 않습니다.|
|-2146233088|Connectionfaileduserhas유효한 Sessionsrdshis비정상|세션 호스트를 사용할 수 없기 때문에 연결에 실패 했습니다. 세션 호스트의 상태를 확인 합니다.|
|-2146233088|ConnectionFailedClientDisconnect|이 오류가 자주 발생 하면 사용자의 컴퓨터가 네트워크에 연결 되어 있는지 확인 하십시오.|
|-2146233088|ConnectionFailedNoHealthyRdshAvailable|호스트 사용자가 연결 하려고 시도한 세션이 정상이 아닙니다. 가상 컴퓨터를 디버그 합니다.|
|-2146233088|ConnectionFailedUserNotAuthorized 있음|사용자에 게 게시 된 앱 또는 데스크톱에 액세스할 수 있는 권한이 없습니다. 관리자가 게시 된 리소스를 제거한 후에이 오류가 나타날 수 있습니다. 사용자에 게 원격 데스크톱 응용 프로그램에서 피드를 새로 고치도록 요청 합니다.|
|2|FileNotFound|사용자가 액세스 하려고 시도한 응용 프로그램이 잘못 설치 되었거나 잘못 된 경로로 설정 되었습니다.|
|3|InvalidCredentials|사용자가 입력 한 사용자 이름 또는 암호가 기존 사용자 이름 또는 암호와 일치 하지 않습니다. 오타가 있는지 확인 하 고 다시 시도 하세요.|
|8|ConnectionBroken|클라이언트와 게이트웨이 또는 서버 간 연결이 끊어졌습니다. 예기치 않게 발생 하지 않는 한 작업이 필요 하지 않습니다.|
|14|UnexpectedNetworkDisconnect|네트워크에 대 한 연결을 삭제 했습니다. 사용자에 게 다시 연결 하도록 요청 합니다.|
|24|ReverseConnectFailed|호스트 가상 컴퓨터는 RD 게이트웨이에 대 한 직접적인 시야를 갖지 않습니다. 게이트웨이 IP 주소를 확인할 수 있는지 확인 합니다.|

## <a name="next-steps"></a>다음 단계

Windows 가상 데스크톱의 역할에 대 한 자세한 내용은 [Windows 가상 데스크톱 환경](environment-setup.md)을 참조 하세요.

Windows 가상 데스크톱에 사용할 수 있는 PowerShell cmdlet의 목록을 보려면 [powershell 참조](/powershell/windows-virtual-desktop/overview)를 참조 하세요.
