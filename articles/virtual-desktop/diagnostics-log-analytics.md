---
title: Windows 가상 데스크톱 진단 log analytics-Azure
description: Windows 가상 데스크톱 진단 기능을 사용 하 여 log analytics를 사용 하는 방법
author: Heidilohr
ms.topic: how-to
ms.date: 05/27/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: a3fccc934fafd8ff7db2cffbd6ba641329ba8de2
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89006808"
---
# <a name="use-log-analytics-for-the-diagnostics-feature"></a>진단 기능에 Log Analytics 사용

>[!IMPORTANT]
>이 콘텐츠는 Azure Resource Manager Windows Virtual Desktop 개체를 통해 Windows Virtual Desktop에 적용됩니다. Azure Resource Manager 개체 없이 Windows Virtual Desktop(클래식)을 사용하는 경우 [이 문서](./virtual-desktop-fall-2019/diagnostics-log-analytics-2019.md)를 참조하세요.

Windows 가상 데스크톱은 다른 여러 Azure 서비스와 같은 모니터링 및 경고에 [Azure Monitor](../azure-monitor/overview.md) 를 사용 합니다. 이를 통해 관리자는 단일 인터페이스를 통해 문제를 식별할 수 있습니다. 서비스는 사용자 및 관리 작업 모두에 대 한 활동 로그를 만듭니다. 각 활동 로그는 다음과 같은 범주에 속합니다.

- 관리 작업:
    - Api 또는 PowerShell을 사용 하 여 Windows 가상 데스크톱 개체를 변경 하려는 시도가 성공 했는지 여부를 추적 합니다. 예를 들어 사용자가 PowerShell을 사용 하 여 호스트 풀을 성공적으로 만들 수 있나요?
- 방향
    - 사용자가 작업 영역을 성공적으로 구독할 수 있나요?
    - 사용자가 원격 데스크톱 클라이언트에 게시 된 모든 리소스를 볼 수 있나요?
- 연결:
    - 사용자가 서비스에 대 한 연결을 시작 하 고 완료 하는 경우
- 호스트 등록:
    - 세션 호스트가 연결 시 서비스에 성공적으로 등록 되었습니까?
- Errors:
    - 사용자에 게 특정 작업에 대 한 문제가 발생 하나요? 이 기능은 정보를 활동과 조인 하는 한 활동 데이터를 추적 하는 테이블을 생성할 수 있습니다.
- 검사점
    - 작업 수명의 특정 단계에 도달 했습니다. 예를 들어, 세션 중에 사용자가 특정 호스트로 부하가 분산 된 다음 연결 중에 사용자가 로그온 했습니다.

진단 역할 서비스 자체가 Windows Virtual Desktop의 일부이므로 Windows Virtual Desktop에 도달하지 않는 연결은 진단 결과에 표시되지 않습니다. 사용자에 게 네트워크 연결 문제가 발생 하는 경우 Windows 가상 데스크톱 연결 문제가 발생할 수 있습니다.

Azure Monitor를 사용 하 여 Windows 가상 데스크톱 데이터를 분석 하 고 동일한 도구 내에서 VM (가상 컴퓨터) 성능 카운터를 검토할 수 있습니다. 이 문서에서는 Windows 가상 데스크톱 환경에 대해 진단을 사용 하도록 설정 하는 방법에 대해 자세히 설명 합니다.

>[!NOTE]
>Azure에서 Vm을 모니터링 하는 방법에 대 한 자세한 내용은 [Azure Monitor를 사용 하 여 azure virtual Machines 모니터링](../azure-monitor/insights/monitor-vm-azure.md)을 참조 하세요. 또한 세션 호스트에서 사용자 환경을 보다 잘 이해 하려면 [성능 카운터 임계값을 검토](../virtual-desktop/virtual-desktop-fall-2019/deploy-diagnostics.md#windows-performance-counter-thresholds) 해야 합니다.

## <a name="before-you-get-started"></a>시작하기 전에

Log Analytics를 사용 하려면 먼저 작업 영역을 만들어야 합니다. 이렇게 하려면 다음 두 문서 중 하나에 있는 지침을 따르세요.

- Azure Portal를 사용 하는 것을 선호 하 [는 경우 Azure Portal에서 Log Analytics 작업 영역 만들기](../azure-monitor/learn/quick-create-workspace.md)를 참조 하세요.
- PowerShell을 선호 하는 경우 [powershell을 사용 하 여 Log Analytics 작업 영역 만들기](../azure-monitor/learn/quick-create-workspace-posh.md)를 참조 하세요.

작업 영역을 만든 후에는 [Azure Monitor에 Windows 컴퓨터 연결](../azure-monitor/platform/log-analytics-agent.md#workspace-id-and-key) 의 지침에 따라 다음 정보를 가져옵니다.

- 작업 영역 ID
- 작업 영역의 기본 키

이 정보는 나중에 설치 프로세스에서 필요 합니다.

Windows 가상 데스크톱 환경을 모니터링 하 고 유지 관리 하는 사용자에 대 한 데이터 액세스를 사용 하도록 설정 하려면 Azure Monitor에 대 한 권한 관리를 검토 해야 합니다. 자세한 내용은 Azure Monitor를 사용 하 여 [역할, 권한 및 보안 시작](../azure-monitor/platform/roles-permissions-security.md)을 참조 하세요.

## <a name="push-diagnostics-data-to-your-workspace"></a>작업 영역에 진단 데이터 푸시

Windows 가상 데스크톱 개체의 진단 데이터를 작업 영역에 대 한 Log Analytics로 푸시할 수 있습니다. 개체를 처음 만들 때 바로이 기능을 설정할 수 있습니다.

새 개체에 대 한 Log Analytics를 설정 하려면 다음을 수행 합니다.

1. Azure Portal에 로그인 하 고 **Windows 가상 데스크톱**으로 이동 합니다.

2. 로그 및 이벤트를 캡처할 개체 (예: 호스트 풀, 앱 그룹 또는 작업 영역)로 이동 합니다.

3. 화면 왼쪽의 메뉴에서 **진단 설정** 을 선택 합니다.

4. 화면 오른쪽에 표시 되는 메뉴에서 **진단 설정 추가** 를 선택 합니다.

    진단 설정 페이지에 표시 되는 옵션은 편집 하는 개체의 종류에 따라 달라 집니다.

    예를 들어 응용 프로그램 그룹에 대 한 진단을 사용 하도록 설정 하는 경우 검사점, 오류 및 관리를 구성 하는 옵션이 표시 됩니다. 작업 영역의 경우 이러한 범주는 사용자가 앱 목록을 구독할 때 추적할 피드를 구성 합니다. 진단 설정에 대해 자세히 알아보려면 [Azure에서 리소스 로그 및 메트릭을 수집 하는 진단 설정 만들기를](../azure-monitor/platform/diagnostic-settings.md)참조 하세요.

     >[!IMPORTANT]
     >모니터링할 각 Azure Resource Manager 개체에 대해 진단을 사용 하도록 설정 해야 합니다. 진단이 사용 하도록 설정 된 후에는 작업에 대 한 데이터를 사용할 수 있습니다. 처음 설정한 후 몇 시간이 걸릴 수 있습니다.

5. 설정 구성의 이름을 입력 한 다음 **Log Analytics 보내기를**선택 합니다. 사용할 이름에는 공백이 없어야 하며 [Azure 명명 규칙](../azure-resource-manager/management/resource-name-rules.md)을 준수 해야 합니다. 로그의 일부로 Log Analytics에 추가 하려는 모든 옵션 (예: 검사점, 오류, 관리 등)을 선택할 수 있습니다.

6. **저장**을 선택합니다.

>[!NOTE]
>Log Analytics은 [Event Hubs](../event-hubs/event-hubs-about.md) 데이터를 스트리밍하는 옵션을 제공 하 여 저장소 계정에 보관 합니다. 이 기능에 대 한 자세한 내용은 [azure 모니터링 데이터를 이벤트 허브로 스트리밍](../azure-monitor/platform/stream-monitoring-data-event-hubs.md) 및 [저장소 계정에 Azure 리소스 로그 보관](../azure-monitor/platform/resource-logs-collect-storage.md)을 참조 하세요.

## <a name="how-to-access-log-analytics"></a>Log Analytics에 액세스 하는 방법

Azure Portal 또는 Azure Monitor에서 Log Analytics 작업 영역에 액세스할 수 있습니다.

### <a name="access-log-analytics-on-a-log-analytics-workspace"></a>Log Analytics 작업 영역에서 Log Analytics 액세스

1. Azure Portal에 로그인합니다.

2. **Log Analytics 작업 영역**을 검색 합니다.

3. 서비스에서 **Log Analytics 작업 영역**을 선택 합니다.

4. 목록에서 Windows 가상 데스크톱 개체에 대해 구성한 작업 영역을 선택 합니다.

5. 작업 영역에서 **로그**를 선택 합니다. **검색** 함수를 사용 하 여 메뉴 목록을 필터링 할 수 있습니다.

### <a name="access-log-analytics-on-azure-monitor"></a>Azure Monitor에서 Log Analytics 액세스

1. Azure Portal에 로그인합니다.

2. **모니터**를 검색하고 선택합니다.

3. **로그**를 선택합니다.

4. 로깅 페이지의 지침에 따라 쿼리의 범위를 설정 합니다.

5. 진단을 쿼리할 준비가 되었습니다. 모든 진단 테이블에는 "WVD" 접두사가 있습니다.

>[!NOTE]
>Azure Monitor 로그에 저장 된 테이블에 대 한 자세한 내용은 [Azure Monitor 데이터](https://docs.microsoft.com/azure/azure-monitor/reference/)참조를 참조 하세요. Windows 가상 데스크톱과 관련 된 모든 테이블에는 "WVD" 레이블이 지정 됩니다.

## <a name="cadence-for-sending-diagnostic-events"></a>진단 이벤트를 보내기 위한 흐름

완료 되 면 진단 이벤트가 Log Analytics 전송 됩니다.

연결 작업에 대 한 이러한 중간 상태의 보고서만 Log Analytics 합니다.

- 시작 됨: 사용자가 원격 데스크톱 클라이언트에서 앱 또는 데스크톱을 선택 하 고 연결 하는 경우
- 연결 됨: 사용자가 앱 또는 데스크톱이 호스트 되는 VM에 성공적으로 연결 된 경우
- 완료 됨: 사용자 또는 서버에서 활동이 발생 한 세션의 연결을 끊은 경우

## <a name="example-queries"></a>쿼리 예

Azure Monitor Log Analytics UI를 통해 쿼리 예제에 액세스 합니다.
1. Log Analytics 작업 영역으로 이동한 다음 **로그**를 선택 합니다. 예제 쿼리 UI가 자동으로 표시 됩니다.
1. 필터를 **Category**로 변경 합니다.
1. **Windows 가상 데스크톱** 을 선택 하 여 사용 가능한 쿼리를 검토 합니다.
1. **실행** 을 선택 하 여 선택한 쿼리를 실행 합니다.

[Azure Monitor Log Analytics에서 저장 된 쿼리의](../azure-monitor/log-query/saved-queries.md)샘플 쿼리 인터페이스에 대해 자세히 알아보세요.

다음 쿼리 목록에서는 단일 사용자에 대 한 연결 정보 또는 문제를 검토할 수 있습니다. 이러한 쿼리는 [Log Analytics 쿼리 편집기](../azure-monitor/log-query/get-started-portal.md#write-and-run-basic-queries)에서 실행할 수 있습니다. 각 쿼리에 대해을 `userupn` 조회할 사용자의 UPN으로 바꿉니다.


단일 사용자에 대 한 모든 연결을 찾으려면 다음을 수행 합니다.

```kusto
WVDConnections
|where UserName == "userupn"
|take 100
|sort by TimeGenerated asc, CorrelationId
```


하루에 연결 된 사용자 수를 찾으려면 다음을 수행 합니다.

```kusto
WVDConnections
|where UserName == "userupn"
|take 100
|sort by TimeGenerated asc, CorrelationId
|summarize dcount(CorrelationId) by bin(TimeGenerated, 1d)
```

사용자가 세션 기간을 찾으려면:

```kusto
let Events = WVDConnections | where UserName == "userupn" ;
Events
| where State == "Connected"
| project CorrelationId , UserName, ResourceAlias , StartTime=TimeGenerated
| join (Events
| where State == "Completed"
| project EndTime=TimeGenerated, CorrelationId)
on CorrelationId
| project Duration = EndTime - StartTime, ResourceAlias
| sort by Duration asc
```

특정 사용자에 대 한 오류를 찾으려면 다음을 수행 합니다.

```kusto
WVDErrors
| where UserName == "userupn"
|take 100
```

다른 사용자에 대 한 특정 오류가 발생 했는지 확인 하려면:

```kusto
WVDErrors
| where CodeSymbolic =="ErrorSymbolicCode"
| summarize count(UserName) by CodeSymbolic
```


>[!NOTE]
>- 사용자가 전체 데스크톱을 여는 경우 세션의 해당 앱 사용은 WVDCheckpoints 테이블의 검사점으로 추적 되지 않습니다.
>- WVDConnections 테이블의 Sourcealias 열에는 사용자가 전체 데스크톱 또는 게시 된 앱에 연결 되어 있는지 여부가 표시 됩니다. 열은 연결 중에 처음으로 연 앱만 표시 합니다. 사용자가 여는 게시 된 앱은 WVDCheckpoints에서 추적 됩니다.
>- WVDErrors 표에서는 사용자가 앱 또는 데스크톱 목록을 구독 하는 동안 발생 하는 관리 오류, 호스트 등록 문제 및 기타 문제를 보여 줍니다.
>- WVDErrors를 사용 하면 관리 작업을 통해 해결할 수 있는 문제를 식별할 수 있습니다. ServiceError의 값은 항상 이러한 유형의 문제에 대해 "false"를 표시 합니다. ServiceError = "true" 인 경우 문제를 Microsoft로 에스컬레이션 해야 합니다. 에스컬레이션 하는 오류에 대해 CorrelationID를 제공 해야 합니다.

## <a name="next-steps"></a>다음 단계

진단 기능에서 식별할 수 있는 일반적인 오류 시나리오를 검토 하려면 [문제 식별 및 진단](diagnostics-role-service.md#common-error-scenarios)을 참조 하세요.
