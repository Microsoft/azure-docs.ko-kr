---
title: Windows 가상 데스크톱 진단 log analytics-Azure
description: Windows 가상 데스크톱 진단 기능을 사용 하 여 log analytics를 사용 하는 방법
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: helohr
ms.openlocfilehash: f50e5144080ffb1c91e834c757082bb57eeaffcb
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75479229"
---
# <a name="use-log-analytics-for-the-diagnostics-feature"></a>진단 기능에 Log Analytics 사용

Windows 가상 데스크톱은 관리자가 단일 인터페이스를 통해 문제를 식별할 수 있도록 하는 진단 기능을 제공 합니다. 이 기능은 Windows 가상 데스크톱 역할에 할당 된 사용자가 서비스를 사용할 때마다 진단 정보를 기록 합니다. 각 로그에는 작업에 관련 된 Windows 가상 데스크톱 역할, 세션 중에 표시 되는 오류 메시지, 테넌트 정보 및 사용자 정보에 대 한 정보가 포함 되어 있습니다. 진단 기능은 사용자 및 관리 작업 모두에 대 한 활동 로그를 만듭니다. 각 활동 로그는 세 가지 주요 범주로 구분 됩니다. 

- 피드 구독 활동: 사용자가 Microsoft 원격 데스크톱 응용 프로그램을 통해 피드에 연결 하려고 시도 하는 경우입니다.
- 연결 작업: 사용자가 Microsoft 원격 데스크톱 응용 프로그램을 통해 데스크톱 또는 RemoteApp에 연결 하려고 시도 하는 경우
- 관리 활동: 관리자가 시스템에서 관리 작업을 수행 하는 경우 (예: 호스트 풀 만들기, 앱 그룹에 사용자 할당 및 역할 할당 만들기)

진단 역할 서비스 자체는 Windows 가상 데스크톱의 일부 이기 때문에 Windows 가상 데스크톱에 연결 되지 않은 연결은 진단 결과에 표시 되지 않습니다. 사용자에 게 네트워크 연결 문제가 발생 하는 경우 Windows 가상 데스크톱 연결 문제가 발생할 수 있습니다.

## <a name="why-you-should-use-log-analytics"></a>Log Analytics를 사용 해야 하는 이유

단일 사용자 문제 해결을 초과 하는 Azure 클라이언트의 진단 데이터를 분석 하려면 Log Analytics를 사용 하는 것이 좋습니다. VM 성능 카운터를 Log Analytics으로 끌어올 수 있으므로 배포에 대 한 정보를 수집 하는 도구가 하나 있습니다.

## <a name="before-you-get-started"></a>시작하기 전에

진단 기능과 함께 Log Analytics를 사용 하려면 먼저 [작업 영역을 만들어야](../azure-monitor/learn/quick-collect-windows-computer.md#create-a-workspace)합니다.

작업 영역을 만든 후에는 [Azure Monitor에 Windows 컴퓨터 연결](../azure-monitor/platform/agent-windows.md#obtain-workspace-id-and-key) 의 지침에 따라 다음 정보를 가져옵니다. 

- 작업 영역 ID
- 작업 영역의 기본 키

이 정보는 나중에 설치 프로세스에서 필요 합니다.

## <a name="push-diagnostics-data-to-your-workspace"></a>작업 영역에 진단 데이터 푸시 

Windows 가상 데스크톱 테넌트의 진단 데이터를 작업 영역에 대 한 Log Analytics에 푸시할 수 있습니다. 작업 영역을 테넌트에 연결 하 여 테넌트를 처음 만들 때이 기능을 즉시 설정 하거나 나중에 기존 테넌트를 사용 하 여 설정할 수 있습니다.

새 테넌트를 설정 하는 동안 Log Analytics 작업 영역에 테넌트를 연결 하려면 다음 cmdlet을 실행 하 여 TenantCreator 사용자 계정을 사용 하 여 Windows 가상 데스크톱에 로그인 합니다. 

```powershell
Add-RdsAccount -DeploymentUrl https://rdbroker.wvd.microsoft.com 
```

새 테넌트 대신 기존 테넌트를 연결 하려는 경우 대신이 cmdlet을 실행 합니다. 

```powershell
Set-RdsTenant -Name <TenantName> -AzureSubscriptionId <SubscriptionID> -LogAnalyticsWorkspaceId <String> -LogAnalyticsPrimaryKey <String> 
```

Log Analytics에 연결 하려는 모든 테넌트에 대해 이러한 cmdlet을 실행 해야 합니다. 

>[!NOTE]
>테넌트를 만들 때 Log Analytics 작업 영역을 연결 하지 않으려면 `New-RdsTenant` cmdlet을 대신 실행 합니다. 

## <a name="cadence-for-sending-diagnostic-events"></a>진단 이벤트를 보내기 위한 흐름

완료 되 면 진단 이벤트가 Log Analytics 전송 됩니다.  

## <a name="example-queries"></a>쿼리 예

다음 예제 쿼리는 진단 기능이 시스템에서 가장 자주 수행 되는 작업에 대 한 보고서를 생성 하는 방법을 보여 줍니다.

이 첫 번째 예제에서는 지원 되는 원격 데스크톱 클라이언트를 사용 하 여 사용자가 시작한 연결 작업을 보여 줍니다.

```powershell
WVDActivityV1_CL 

| where Type_s == "Connection" 

| join kind=leftouter ( 

    WVDErrorV1_CL 

    | summarize Errors = makelist(pack('Time', Time_t, 'Code', ErrorCode_s , 'CodeSymbolic', ErrorCodeSymbolic_s, 'Message', ErrorMessage_s, 'ReportedBy', ReportedBy_s , 'Internal', ErrorInternal_s )) by ActivityId_g 

    ) on $left.Id_g  == $right.ActivityId_g   

| join  kind=leftouter (  

    WVDCheckpointV1_CL 

    | summarize Checkpoints = makelist(pack('Time', Time_t, 'ReportedBy', ReportedBy_s, 'Name', Name_s, 'Parameters', Parameters_s) ) by ActivityId_g 

    ) on $left.Id_g  == $right.ActivityId_g  

|project-away ActivityId_g, ActivityId_g1 
```

다음 예제 쿼리는 테넌트의 관리자에의 한 관리 활동을 보여 줍니다.

```powershell
WVDActivityV1_CL 

| where Type_s == "Management" 

| join kind=leftouter ( 

    WVDErrorV1_CL 

    | summarize Errors = makelist(pack('Time', Time_t, 'Code', ErrorCode_s , 'CodeSymbolic', ErrorCodeSymbolic_s, 'Message', ErrorMessage_s, 'ReportedBy', ReportedBy_s , 'Internal', ErrorInternal_s )) by ActivityId_g 

    ) on $left.Id_g  == $right.ActivityId_g   

| join  kind=leftouter (  

    WVDCheckpointV1_CL 

    | summarize Checkpoints = makelist(pack('Time', Time_t, 'ReportedBy', ReportedBy_s, 'Name', Name_s, 'Parameters', Parameters_s) ) by ActivityId_g 

    ) on $left.Id_g  == $right.ActivityId_g  

|project-away ActivityId_g, ActivityId_g1 
```
 
## <a name="stop-sending-data-to-log-analytics"></a>Log Analytics에 대 한 데이터 전송을 중지 합니다. 

기존 테넌트에서 Log Analytics로의 데이터 전송을 중지 하려면 다음 cmdlet을 실행 하 고 빈 문자열을 설정 합니다.

```powershell
Set-RdsTenant -Name <TenantName> -AzureSubscriptionId <SubscriptionID> -LogAnalyticsWorkspaceId <String> -LogAnalyticsPrimaryKey <String> 
```

데이터 보내기를 중지 하려는 모든 테넌트에 대해이 cmdlet을 실행 해야 합니다. 

## <a name="next-steps"></a>다음 단계 

진단 기능에서 식별할 수 있는 일반적인 오류 시나리오를 검토 하려면 [문제 식별 및 진단](diagnostics-role-service.md#common-error-scenarios)을 참조 하세요.
