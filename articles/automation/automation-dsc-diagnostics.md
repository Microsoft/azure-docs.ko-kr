---
title: Azure Monitor 로그에 Azure Automation 상태 구성 보고 데이터 전달
description: 이 문서에서는 Azure Automation 상태 구성에서 Azure Monitor 로그로 DSC (필요한 상태 구성) 보고 데이터를 전송 하 여 추가 통찰력 및 관리를 제공 하는 방법을 보여 줍니다.
services: automation
ms.service: automation
ms.subservice: dsc
author: mgoedtel
ms.author: magoedte
ms.date: 11/06/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 69801909c6bc8d215ca7dd3ccb7ac349201e8774
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77198567"
---
# <a name="forward-azure-automation-state-configuration-reporting-data-to-azure-monitor-logs"></a>Azure Monitor 로그에 Azure Automation 상태 구성 보고 데이터 전달

Azure Automation 상태 구성은 노드 상태 데이터를 30 일 동안 유지 합니다.
더 오랜 기간 동안이 데이터를 보존 하려면 노드 상태 데이터를 Log Analytics 작업 영역으로 보낼 수 있습니다.
Azure Portal에서 또는 PowerShell을 사용하여 노드 및 노드 구성의 개별 DSC 리소스에 대한 준수 상태를 볼 수 있습니다.
Azure Monitor 로그를 사용 하 여 다음을 수행할 수 있습니다.

- 관리되는 노드 및 개별 리소스에 대한 준수 정보 가져오기
- 준수 상태를 기준으로 전자 메일 또는 경고 트리거
- 관리되는 노드에서 고급 쿼리 작성
- Automation 계정에서 준수 상태 상호 연관
- 시간에 따른 노드 준수 내역 시각화

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>사전 요구 사항

Azure Monitor 로그에 Automation 상태 구성 보고서 보내기를 시작 하려면 다음이 필요 합니다.

- [Azure PowerShell](/powershell/azure/overview)의 2016년 11월(v2.3.0) 이후 릴리스
- Azure Automation 계정. 자세한 내용은 [Azure Automation 소개](automation-intro.md)를 참조 하세요.
- Automation & Control 서비스 제공이 포함 된 Log Analytics 작업 영역입니다. 자세한 내용은 [Azure Monitor에서 Log Analytics 시작](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal)을 참조하세요.
- 적어도 하나 이상의 Azure Automation 상태 구성 노드 자세한 내용은 [Azure Automation 상태 구성을 통해 관리를 위한 컴퓨터 온 보 딩](automation-dsc-onboarding.md)을 참조 하세요.
- [Xdscdiagnostics](https://www.powershellgallery.com/packages/xDscDiagnostics/2.7.0.0) 모듈, 버전 2.7.0.0 이상 설치 단계는 [Azure Automation 필요한 상태 구성 문제 해결](./troubleshoot/desired-state-configuration.md#steps-to-troubleshoot-desired-state-configuration-dsc)을 참조 하세요.

## <a name="set-up-integration-with-azure-monitor-logs"></a>Azure Monitor 로그와의 통합 설정

DSC Azure Automation에서 Azure Monitor 로그로 데이터 가져오기를 시작 하려면 다음 단계를 완료 합니다.

1. PowerShell에서 Azure 계정에 로그인합니다. [Azure PowerShell를 사용 하 여 로그인을](https://docs.microsoft.com/powershell/azure/authenticate-azureps)참조 하세요.
1. 다음 PowerShell cmdlet을 실행 하 여 Automation 계정의 리소스 ID를 가져옵니다. Automation 계정이 둘 이상 있는 경우 구성 하려는 계정에 대 한 리소스 ID를 선택 합니다.

   ```powershell
   # Find the ResourceId for the Automation account
   Get-AzResource -ResourceType 'Microsoft.Automation/automationAccounts'
   ```

1. 다음 PowerShell cmdlet을 실행 하 여 Log Analytics 작업 영역의 리소스 ID를 가져옵니다. 둘 이상의 작업 영역이 있는 경우 구성할 작업 영역에 대 한 리소스 ID를 선택 합니다.

   ```powershell
   # Find the ResourceId for the Log Analytics workspace
   Get-AzResource -ResourceType 'Microsoft.OperationalInsights/workspaces'
   ```

1. 다음 PowerShell cmdlet을 실행 하 여 `<AutomationResourceId>` 및 `<WorkspaceResourceId>`를 이전 단계 각각의 *ResourceId* 값으로 바꿉니다.

   ```powershell
   Set-AzDiagnosticSetting -ResourceId <AutomationResourceId> -WorkspaceId <WorkspaceResourceId> -Enabled $true -Category 'DscNodeStatus'
   ```

1. Azure Automation 상태 구성에서 Azure Monitor 로그로 데이터 가져오기를 중지 하려면 다음 PowerShell cmdlet을 실행 합니다.

   ```powershell
   Set-AzDiagnosticSetting -ResourceId <AutomationResourceId> -WorkspaceId <WorkspaceResourceId> -Enabled $false -Category 'DscNodeStatus'
   ```

## <a name="view-the-state-configuration-logs"></a>상태 구성 로그 보기

Automation 상태 구성 데이터에 대 한 Azure Monitor 로그와의 통합을 설정한 후에는 상태 구성 (DSC) 페이지의 왼쪽 창에 있는 **모니터링** 섹션에서 **로그** 를 선택 하 여 볼 수 있습니다.

![로그](media/automation-dsc-diagnostics/automation-dsc-logs-toc-item.png)

**로그 검색** 창이 열리고 Automation 계정 리소스로 범위가 지정 된 쿼리 영역이 열립니다. Azure Monitor 로그에서 검색 하 여 DSC 작업에 대 한 상태 구성 로그를 검색할 수 있습니다. DSC 작업에 대 한 레코드는 AzureDiagnostics 테이블에 저장 됩니다. 예를 들어 호환 되지 않는 노드를 찾으려면 다음 쿼리를 입력 합니다.

```AzureDiagnostics
| where Category == 'DscNodeStatus' 
| where OperationName contains 'DSCNodeStatusData'
| where ResultType != 'Compliant'
```
필터링 세부 정보:

* 각 상태 구성 노드에 대 한 작업을 반환 하도록 *Dscnodestatusdata* 를 필터링 합니다.
* *DscResourceStatusData* 를 필터링 하 여 해당 리소스에 적용 된 노드 구성에서 호출 된 각 DSC 리소스에 대 한 작업을 반환 합니다. 
* 실패 한 모든 DSC 리소스에 대 한 오류 정보를 반환 하려면 *DscResourceStatusData* 를 필터링 합니다.

데이터를 찾기 위한 로그 쿼리를 구성 하는 방법에 대 한 자세한 내용은 [Azure Monitor의 로그 쿼리 개요](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview)를 참조 하세요.

### <a name="send-an-email-when-a-state-configuration-compliance-check-fails"></a>상태 구성 준수 확인이 실패할 경우 이메일 보내기

고객이 자주 묻는 질문 중 하나는 DSC 구성에 문제가 발생한 경우 전자 메일 또는 텍스트를 보낼 수 있는지 여부입니다.

경고 규칙을 만들려면 경고를 호출 해야 하는 상태 구성 보고서 레코드에 대 한 로그 검색을 만드는 것으로 시작 합니다. **+ 새 경고 규칙** 단추를 클릭하여 경고 규칙을 만들고 구성합니다.

1. Log Analytics 작업 영역 개요 페이지에서 **로그**를 클릭 합니다.
1. 쿼리 필드에 다음 검색을 입력하여 경고에 대한 로그 검색 쿼리를 만듭니다. `Type=AzureDiagnostics Category='DscNodeStatus' NodeName_s='DSCTEST1' OperationName='DscNodeStatusData' ResultType='Failed'`.

   둘 이상의 Automation 계정 또는 구독에서 작업 영역으로의 로그를 설정한 경우 구독 또는 Automation 계정별로 경고를 그룹화할 수 있습니다. DscNodeStatusData 검색의 리소스 필드에서 Automation 계정 이름을 파생 합니다.
1. **규칙 만들기** 화면을 열려면 페이지 위쪽에서 **+ 새 경고 규칙**을 클릭합니다. 

경고를 구성 하는 옵션에 대 한 자세한 내용은 [경고 규칙 만들기](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md)를 참조 하세요.

### <a name="find-failed-dsc-resources-across-all-nodes"></a>모든 노드에서 실패한 DSC 리소스 찾기

Azure Monitor 로그를 사용 하는 경우의 이점 중 하나는 노드 간 실패 한 검사를 검색할 수 있다는 것입니다.
실패 한 DSC 리소스의 모든 인스턴스를 찾으려면 다음을 수행 합니다.

1. Log Analytics 작업 영역 개요 페이지에서 **로그**를 클릭 합니다.
1. 쿼리 필드에 다음 검색을 입력하여 경고에 대한 로그 검색 쿼리를 만듭니다. `Type=AzureDiagnostics Category='DscNodeStatus' OperationName='DscResourceStatusData' ResultType='Failed'`.

### <a name="view-historical-dsc-node-status"></a>DSC 노드 상태 기록 보기

시간이 지남에 따라 DSC 노드 상태 기록을 시각화 하려면 다음 쿼리를 사용할 수 있습니다.

`Type=AzureDiagnostics ResourceProvider="MICROSOFT.AUTOMATION" Category=DscNodeStatus NOT(ResultType="started") | measure Count() by ResultType interval 1hour`

이 쿼리는 시간 경과에 따른 노드 상태의 차트를 표시 합니다.

## <a name="azure-monitor-logs-records"></a>Azure Monitor 로그 레코드

Azure Automation 진단은 Azure Monitor 로그에 두 가지 범주의 레코드를 만듭니다.

* 노드 상태 데이터 (DscNodeStatusData)
* 리소스 상태 데이터 (DscResourceStatusData)

### <a name="dscnodestatusdata"></a>DscNodeStatusData

| 속성 | Description |
| --- | --- |
| TimeGenerated |준수 확인이 실행된 날짜 및 시간입니다. |
| OperationName |DscNodeStatusData. |
| ResultType |노드가 규정을 준수하는지 여부입니다. |
| NodeName_s |관리되는 노드의 이름입니다. |
| NodeComplianceStatus_s |노드가 규정을 준수하는지 여부입니다. |
| DscReportStatus |준수 검사가 성공적으로 실행되었는지 여부입니다. |
| ConfigurationMode | 구성이 노드에 적용되는 방식입니다. 가능한 값은 다음과 같습니다. <ul><li>*ApplyOnly*: DSC는 구성을 적용하며, 새 구성이 대상 노드에 푸시되지 않거나 서버에서 새 구성을 가져올 때 아무 작업도 수행하지 않습니다. 새 구성의 애플리케이션이 초기에 적용된 후 DSC는 이전에 구성된 상태에서 달라졌는지 여부를 확인하지 않습니다. DSC는 *Applyonly* 값이 적용 되기 전에 성공할 때까지 구성을 적용 하려고 시도 합니다. </li><li>*ApplyAndMonitor*: 기본값입니다. LCM는 새 구성을 적용합니다. 새 구성의 애플리케이션이 초기에 적용된 후 대상 노드가 원하는 상태에서 다른 상태로 바뀌면 DSC는 불일치 상황을 로그에 보고합니다. DSC는 *Applyandmonitor* 값이 적용 되기 전에 성공할 때까지 구성을 적용 하려고 시도 합니다.</li><li>*ApplyAndAutoCorrect*: DSC에서 모든 새 구성을 적용합니다. 새 구성의 애플리케이션이 초기에 적용된 후 대상 노드가 원하는 상태에서 다른 상태로 바뀌면 DSC는 불일치 상황을 로그에 보고하고 현재 구성을 다시 적용합니다.</li></ul> |
| HostName_s | 관리되는 노드의 이름입니다. |
| IPAddress | 관리되는 노드의 IPv4 주소입니다. |
| Category | DscNodeStatus입니다. |
| 리소스 | Azure Automation 계정의 이름입니다. |
| Tenant_g | 호출자에 대한 테넌트를 식별하는 GUID입니다. |
| NodeId_g |관리되는 노드를 식별하는 GUID입니다. |
| DscReportId_g |보고서를 식별하는 GUID입니다. |
| LastSeenTime_t |보고서를 마지막으로 본 날짜 및 시간입니다. |
| ReportStartTime_t |보고서가 시작된 날짜 및 시간입니다. |
| ReportEndTime_t |보고서가 완료된 날짜 및 시간입니다. |
| NumberOfResources_d |노드에 적용된 구성에서 호출된 DSC 리소스의 수입니다. |
| SourceSystem | Azure Monitor 로그가 데이터를 수집 하는 방법입니다. Azure 진단의 경우 항상 "Azure"입니다. |
| ResourceId |Azure Automation 계정의 식별자입니다. |
| ResultDescription | 이 작업에 대한 설명입니다. |
| SubscriptionId | Automation 계정에 대 한 Azure 구독 ID (GUID)입니다. |
| ResourceGroup | Automation 계정에 대한 리소스 그룹의 이름입니다. |
| ResourceProvider | MICROSOFT. 자동화할. |
| ResourceType | AUTOMATIONACCOUNTS. |
| CorrelationId |준수 보고서의 상관 관계 식별자 인 GUID입니다. |

### <a name="dscresourcestatusdata"></a>DscResourceStatusData

| 속성 | Description |
| --- | --- |
| TimeGenerated |준수 확인이 실행된 날짜 및 시간입니다. |
| OperationName |DscResourceStatusData|
| ResultType |리소스가 규정을 준수하는지 여부입니다. |
| NodeName_s |관리되는 노드의 이름입니다. |
| Category | DscNodeStatus입니다. |
| 리소스 | Azure Automation 계정의 이름입니다. |
| Tenant_g | 호출자에 대한 테넌트를 식별하는 GUID입니다. |
| NodeId_g |관리되는 노드를 식별하는 GUID입니다. |
| DscReportId_g |보고서를 식별하는 GUID입니다. |
| DscResourceId_s |DSC 리소스 인스턴스의 이름입니다. |
| DscResourceName_s |DSC 리소스의 이름입니다. |
| DscResourceStatus_s |DSC 리소스가 규정을 준수하는지 여부입니다. |
| DscModuleName_s |DSC 리소스를 포함하는 PowerShell 모듈의 이름입니다. |
| DscModuleVersion_s |DSC 리소스를 포함하는 PowerShell 모듈의 버전입니다. |
| DscConfigurationName_s |노드에 적용되는 구성의 이름입니다. |
| ErrorCode_s | 리소스가 실패한 경우의 오류 코드입니다. |
| ErrorMessage_s |리소스가 실패한 경우의 오류 메시지입니다. |
| DscResourceDuration_d |DSC 리소스가 실행된 시간(초)입니다. |
| SourceSystem | Azure Monitor 로그가 데이터를 수집 하는 방법입니다. Azure 진단의 경우 항상 *Azure*입니다. |
| ResourceId |Azure Automation 계정을 지정합니다. |
| ResultDescription | 이 작업에 대한 설명입니다. |
| SubscriptionId | Automation 계정에 대 한 Azure 구독 ID (GUID)입니다. |
| ResourceGroup | Automation 계정에 대한 리소스 그룹의 이름입니다. |
| ResourceProvider | MICROSOFT. 자동화할. |
| ResourceType | AUTOMATIONACCOUNTS. |
| CorrelationId |준수 보고서의 상관 관계 ID 인 GUID입니다. |

## <a name="summary"></a>요약

Automation 상태 구성 데이터를 Azure Monitor 로그에 전송 하 여 다음과 같은 방법으로 자동화 상태 구성 노드의 상태를 더 잘 파악할 수 있습니다.

- 문제가 발생할 때 알리도록 경고 설정
- 사용자 지정 보기와 검색 쿼리를 사용하여 runbook 결과, runbook 작업 상태 및 기타 관련된 핵심 지표 또는 메트릭 시각화.

Azure Monitor 로그는 자동화 상태 구성 데이터에 대 한 작업 가시성을 개선 하 고 문제를 보다 신속 하 게 해결 하는 데 도움이 됩니다.

## <a name="next-steps"></a>다음 단계

- 개요는 [Azure Automation 상태 구성](automation-dsc-overview.md)을 참조하세요.
- 시작하려면 [Azure Automation 상태 구성 시작하기](automation-dsc-getting-started.md)를 참조하세요.
- DSC 구성을 대상 노드에 할당할 수 있도록 DSC 구성을 컴파일하는 방법에 대해 알아보려면 [Azure Automation 상태 구성에서 구성 컴파일](automation-dsc-compile.md)을 참조하세요.
- PowerShell cmdlet 참조는 [Azure Automation 상태 구성 cmdlet](/powershell/module/azurerm.automation/#automation)을 참조하세요.
- 가격 책정 정보는 [Azure Automation 상태 구성 가격 책정](https://azure.microsoft.com/pricing/details/automation/)을 참조하세요.
- 지속적인 배포 파이프라인에서 Azure Automation 상태 구성을 사용하는 예제는 [Azure Automation 상태 구성 및 Chocolatey를 사용한 지속적인 배포](automation-dsc-cd-chocolatey.md)를 참조하세요.
- 다른 검색 쿼리를 생성 하 고 Azure Monitor 로그를 사용 하 여 자동화 상태 구성 로그를 검토 하는 방법에 대 한 자세한 내용은 [Azure Monitor 로그의 로그 검색](../log-analytics/log-analytics-log-searches.md) 을 참조 하세요.
- Azure Monitor 로그 및 데이터 수집 원본에 대해 자세히 알아보려면 [Azure Monitor 로그에서 Azure storage 데이터 수집 개요](../azure-monitor/platform/collect-azure-metrics-logs.md) 를 참조 하세요.
