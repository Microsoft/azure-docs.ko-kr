---
title: Azure 업데이트 관리의 오류 문제 해결
description: Azure의 업데이트 관리 솔루션 문제를 해결 하 고 문제를 해결 하는 방법을 알아봅니다.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 05/31/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 5ee1a20d4a3c46cab484b03b5fcc212a79d19047
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/22/2020
ms.locfileid: "76513272"
---
# <a name="troubleshooting-issues-with-update-management"></a>업데이트 관리 문제 해결

이 문서에서는 업데이트 관리 사용 하는 경우 발생할 수 있는 문제에 대 한 해결 방법을 설명 합니다.

Hybrid Worker 에이전트에 대 한 에이전트 문제 해결사를 사용 하 여 기본 문제를 확인 합니다. 이 문제 해결사에 대한 자세한 내용은 [업데이트 에이전트 문제 해결](update-agent-issues.md)을 참조하세요. 다른 모든 문제를 해결 하려면 다음 문제 해결 지침을 따르십시오.

VM (가상 컴퓨터)에서 솔루션을 등록 하는 동안 문제가 발생 하는 경우 로컬 컴퓨터의 **응용 프로그램 및 서비스 로그** 에서 이벤트 ID 4502 및 microsoft.enterprisemanagement.reporting.code를 포함 하는 이벤트 세부 정보를 포함 하는 이벤트에 대 한 **Operations Manager** 로그를 확인 합니다. **eventmessage**.

다음 섹션에서는 특정 오류 메시지와 각각에 대 한 가능한 해결 방법을 중점적으로 설명 합니다. 다른 온 보 딩 문제는 [솔루션 등록 문제 해결](onboarding.md)을 참조 하세요.

## <a name="nologs"></a>시나리오: 컴퓨터가 포털에 표시 되지 않음 업데이트 관리

### <a name="issue"></a>문제

다음과 같은 현상이 발생 합니다.

* 컴퓨터가 VM의 업데이트 관리 보기에서 **구성 되지 않은 것** 으로 표시 됩니다.

* Azure Automation 계정의 업데이트 관리 보기에 컴퓨터가 없습니다.

* **규정 준수**상태에서 **평가 되지 않음** 으로 표시 되는 컴퓨터가 있습니다. 그러나 Hybrid Runbook Worker에 대 한 Azure Monitor 로그에 하트 비트 데이터가 표시 되지만 업데이트 관리에는 표시 되지 않습니다.

### <a name="cause"></a>원인

이 문제는 로컬 구성 문제나 부적절 하 게 구성 된 범위 구성으로 인해 발생할 수 있습니다.

Hybrid Runbook Worker를 다시 등록 하 고 다시 설치 해야 할 수 있습니다.

작업 영역에 도달 하 여 추가 데이터 저장소를 방지 하는 할당량을 정의 했을 수 있습니다.

### <a name="resolution"></a>해상도

* OS에 따라 [Windows](update-agent-issues.md#troubleshoot-offline) 또는 [Linux](update-agent-issues-linux.md#troubleshoot-offline)에 대 한 문제 해결사를 실행 합니다.

* 컴퓨터가 올바른 작업 영역에 보고 하 고 있는지 확인 합니다. 이 측면을 확인 하는 방법에 대 한 지침은 [에이전트에서 Log Analytics에 대 한 연결 확인](../../azure-monitor/platform/agent-windows.md#verify-agent-connectivity-to-log-analytics)을 참조 하세요. 또한이 작업 영역이 Azure Automation 계정에 연결 되어 있는지 확인 합니다. 확인 하려면 Automation 계정으로 이동 하 고 **관련 리소스**에서 **연결 된 작업 영역** 을 선택 합니다.

* 컴퓨터가 Log Analytics 작업 영역에 표시 되는지 확인 합니다. Automation 계정에 연결 된 Log Analytics 작업 영역에서 다음 쿼리를 실행 합니다.

  ```loganalytics
  Heartbeat
  | summarize by Computer, Solutions
  ```
  쿼리 결과에 컴퓨터가 표시 되지 않으면 최근에 체크 인 되지 않은 것입니다. 즉, 로컬 구성 문제가 있는 것 이므로 [에이전트를 다시 설치](../../azure-monitor/learn/quick-collect-windows-computer.md#install-the-agent-for-windows)해야 합니다. 컴퓨터가 쿼리 결과에 표시 되는 경우이 목록에서 다음 글머리 기호 항목에 지정 된 범위 구성을 확인 해야 합니다.

* 범위 구성 문제를 확인 합니다. [범위 구성](../automation-onboard-solutions-from-automation-account.md#scope-configuration) 에 따라 솔루션에 대해 구성 되는 컴퓨터가 결정 됩니다. 컴퓨터가 작업 영역에 표시 되지만 **업데이트 관리** 포털에는 표시 되지 않는 경우 컴퓨터를 대상으로 하도록 범위 구성을 구성 해야 합니다. 이 작업을 수행 하는 방법에 대 한 자세한 내용은 [작업 영역에서 컴퓨터](../automation-onboard-solutions-from-automation-account.md#onboard-machines-in-the-workspace)등록을 참조 하세요.

* 작업 영역에서 다음 쿼리를 실행 합니다.

  ```loganalytics
  Operation
  | where OperationCategory == 'Data Collection Status'
  | sort by TimeGenerated desc
  ```
  `Data collection stopped due to daily limit of free data reached. Ingestion status = OverQuota` 결과를 얻는 경우 작업 영역에 도달 하 여 데이터가 저장 되지 않도록 중지 된 할당량이 정의 됩니다. 작업 영역에서 **사용량 및 예상 비용** > **데이터 볼륨 관리** 로 이동 하 여 할당량을 확인 하거나 제거 합니다.

* 이러한 단계를 수행 해도 문제가 해결 되지 않으면 windows [Hybrid Runbook Worker 배포](../automation-windows-hrw-install.md) 의 단계를 수행 하 여 windows 용 Hybrid Worker를 다시 설치 합니다. 또는 Linux의 경우 [linux Hybrid Runbook Worker를 배포](../automation-linux-hrw-install.md)합니다.

## <a name="rp-register"></a>시나리오: 구독에 대 한 자동화 리소스 공급자를 등록할 수 없습니다.

### <a name="issue"></a>문제

Automation 계정에서 솔루션을 사용 하는 경우 다음과 같은 오류가 발생 합니다.

```error
Error details: Unable to register Automation Resource Provider for subscriptions:
```

### <a name="cause"></a>원인

자동화 리소스 공급자가 구독에 등록 되어 있지 않습니다.

### <a name="resolution"></a>해상도

자동화 리소스 공급자를 등록 하려면 Azure Portal에서 다음 단계를 수행 합니다.

1. 포털의 맨 아래에 있는 Azure 서비스 목록에서 **모든 서비스**를 선택 하 고 일반 서비스 그룹에서 **구독** 을 선택 합니다.
2. 구독을 선택합니다.
3. **설정**아래에서 **리소스 공급자**를 선택 합니다.
4. 리소스 공급자 목록에서 **Microsoft Automation** 리소스 공급자가 등록 되어 있는지 확인 합니다.
5. 나열 되지 않은 경우 [리소스 공급자 등록 오류 해결](/azure/azure-resource-manager/resource-manager-register-provider-errors)의 단계에 따라 **Microsoft Automation** 공급자를 등록 합니다.

## <a name="components-enabled-not-working"></a>시나리오: 업데이트 관리 솔루션의 구성 요소를 사용 하도록 설정 했으므로 이제이 가상 머신을 구성 하는 중입니다.

### <a name="issue"></a>문제

온보딩 후 15분이 지났는데도 가상 머신에 다음 메시지가 계속 표시됩니다.

```error
The components for the 'Update Management' solution have been enabled, and now this virtual machine is being configured. Please be patient, as this can sometimes take up to 15 minutes.
```

### <a name="cause"></a>원인

이 오류는 다음과 같은 이유로 발생할 수 있습니다.

- Automation 계정과의 통신이 차단 되 고 있습니다.
- 등록 중인 VM이 MMA (Microsoft Monitoring Agent)로 sysprep 되지 않은 복제 된 컴퓨터에서 발생 했을 수 있습니다.

### <a name="resolution"></a>해상도

1. [네트워크 계획](../automation-hybrid-runbook-worker.md#network-planning) 으로 이동 하 여 업데이트 관리 작동 하는 데 허용 되어야 하는 주소 및 포트에 대해 알아봅니다.
2. 복제 된 이미지를 사용 하는 경우:
   1. Log Analytics 작업 영역에서 표시 되는 경우 `MicrosoftDefaultScopeConfig-Updates` 범위 구성에 대해 저장 된 검색에서 VM을 제거 합니다. 저장된 검색은 작업 영역의 **일반**에서 찾을 수 있습니다.
   2. `Remove-Item -Path "HKLM:\software\microsoft\hybridrunbookworker" -Recurse -Force`을 실행합니다.
   3. `Restart-Service HealthService`를 실행하여 `HealthService`를 다시 시작합니다. 그러면 키가 다시 만들어지고 새 UUID가 생성 됩니다.
   4. 이 방법이 작동 하지 않으면 먼저 이미지에서 sysprep을 실행 한 다음 MMA를 설치 합니다.

## <a name="multi-tenant"></a>시나리오: 다른 Azure 테넌트의 컴퓨터에 대 한 업데이트 배포를 만들 때 연결 된 구독 오류가 표시 됨

### <a name="issue"></a>문제

다른 Azure 테넌트의 컴퓨터에 대 한 업데이트 배포를 만들려고 할 때 다음과 같은 오류가 발생 합니다.

```error
The client has permission to perform action 'Microsoft.Compute/virtualMachines/write' on scope '/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/Microsoft.Automation/automationAccounts/automationAccountName/softwareUpdateConfigurations/updateDeploymentName', however the current tenant '00000000-0000-0000-0000-000000000000' is not authorized to access linked subscription '00000000-0000-0000-0000-000000000000'.
```

### <a name="cause"></a>원인

이 오류는 업데이트 배포에 포함 된 다른 테넌트에 Azure Vm이 있는 업데이트 배포를 만들 때 발생 합니다.

### <a name="resolution"></a>해상도

다음 해결 방법을 사용 하 여 예약 된 항목을 가져옵니다. `-ForUpdate` 스위치와 함께 [AzureRmAutomationSchedule](/powershell/module/azurerm.automation/new-azurermautomationschedule) cmdlet을 사용 하 여 일정을 만들 수 있습니다. 그런 다음 [AzureRmAutomationSoftwareUpdateConfiguration](/powershell/module/azurerm.automation/new-azurermautomationsoftwareupdateconfiguration
) cmdlet을 사용 하 여 다른 테넌트의 컴퓨터를 `-NonAzureComputer` 매개 변수에 전달 합니다. 다음 예제에 이 작업을 수행하는 방법이 나와 있습니다.

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$s = New-AzureRmAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdate

New-AzureRmAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName $aa -Schedule $s -Windows -AzureVMResourceId $azureVMIdsW -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

## <a name="node-reboots"></a>시나리오: 설명이 없는 재부팅

### <a name="issue"></a>문제

다시 부팅 안 함 **옵션을** **다시 부팅 안 함**으로 설정한 경우에도 업데이트가 설치 된 후에도 컴퓨터가 다시 부팅 됩니다.

### <a name="cause"></a>원인

Windows 업데이트은 여러 레지스트리 키로 수정할 수 있으며,이 중 하나는 다시 부팅 동작을 수정할 수 있습니다.

### <a name="resolution"></a>해상도

[다시 시작을 관리 하는 데 사용 되](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart) 는 레지스트리 및 레지스트리 키를 [편집 하 여 자동 업데이트 구성](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-registry) 에 나열 된 레지스트리 키를 검토 하 여 컴퓨터가 제대로 구성 되었는지 확인 합니다.

## <a name="failed-to-start"></a>시나리오: 컴퓨터에서 업데이트 배포에 "시작 하지 못했습니다." 표시

### <a name="issue"></a>문제

컴퓨터에서 상태 **를 시작 하지 못했습니다** .가 표시 됩니다. 컴퓨터에 대 한 특정 세부 정보를 볼 때 다음과 같은 오류가 표시 됩니다.

```error
Failed to start the runbook. Check the parameters passed. RunbookName Patch-MicrosoftOMSComputer. Exception You have requested to create a runbook job on a hybrid worker group that does not exist.
```

### <a name="cause"></a>원인

이 오류는 다음 이유 중 하나로 인해 발생할 수 있습니다.

* 컴퓨터가 더 이상 존재 하지 않습니다.
* 컴퓨터가 꺼져 있고 연결할 수 없습니다.
* 컴퓨터에 네트워크 연결 문제가 있으므로 컴퓨터의 hybrid worker에 연결할 수 없습니다.
* SourceComputerId를 변경한 MMA 업데이트 되었습니다.
* Automation 계정에서 2000의 동시 작업 제한에 도달 하는 경우 업데이트 실행이 제한 되었습니다. 각 배포는 작업으로 간주 되며, 업데이트 배포의 각 컴퓨터는 작업으로 계산 됩니다. Automation 계정에서 현재 실행 되 고 있는 다른 모든 automation 작업 또는 업데이트 배포는 동시 작업 제한에 계산 됩니다.

### <a name="resolution"></a>해상도

해당 하는 경우 업데이트 배포에 [동적 그룹](../automation-update-management-groups.md) 을 사용 합니다. 또한 다음 작업도 수행해야 합니다.

* 컴퓨터가 존재 하 고 연결할 수 있는지 확인 합니다. 존재 하지 않는 경우 배포를 편집 하 고 컴퓨터를 제거 합니다.
* 업데이트 관리에 필요한 포트 및 주소 목록은 [네트워크 계획](../automation-update-management.md#ports) 섹션을 참조 하 고 컴퓨터가 이러한 요구 사항을 충족 하는지 확인 합니다.
* Log Analytics에서 다음 쿼리를 실행 하 여 `SourceComputerId` 변경 된 환경에서 컴퓨터를 찾을 수 있습니다. `Computer` 값이 같지만 `SourceComputerId` 값이 다른 컴퓨터를 찾습니다. 

   ```loganalytics
   Heartbeat | where TimeGenerated > ago(30d) | distinct SourceComputerId, Computer, ComputerIP
   ```
   영향을 받는 컴퓨터를 찾은 후에는 해당 컴퓨터를 대상으로 하는 업데이트 배포를 편집한 다음 `SourceComputerId`가 올바른 값을 반영 하도록 제거 하 고 다시 추가 합니다.

## <a name="updates-nodeployment"></a>시나리오: 배포 없이 업데이트가 설치 됨

### <a name="issue"></a>문제

업데이트 관리에서 Windows 컴퓨터를 등록 하면 배포 없이 설치 된 업데이트가 표시 됩니다.

### <a name="cause"></a>원인

Windows에서는 업데이트가 제공 되는 즉시 자동으로 설치 됩니다. 컴퓨터에 배포할 업데이트를 예약 하지 않은 경우이 동작으로 인해 혼란이 발생할 수 있습니다.

### <a name="resolution"></a>해상도

`HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU` 레지스트리 키는 기본값 4: **자동 다운로드 및 설치**로 설정 됩니다.

업데이트 관리 클라이언트의 경우이 키를 3으로 설정 하는 것이 좋습니다. **자동 다운로드 하지만 자동으로 설치 되지**않습니다.

자세한 내용은 [자동 업데이트 구성](https://docs.microsoft.com/windows/deployment/update/waas-wu-settings#configure-automatic-updates)을 참조 하세요.

## <a name="machine-already-registered"></a>시나리오: 컴퓨터가 이미 다른 계정에 등록되어 있음

### <a name="issue"></a>문제

다음과 같은 오류 메시지가 표시됩니다.

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception System.InvalidOperationException: {"Message":"Machine is already registered to a different account."}
```

### <a name="cause"></a>원인

컴퓨터가 업데이트 관리의 다른 작업 영역에 이미 등록 되었습니다.

### <a name="resolution"></a>해상도

1. 컴퓨터에서 [업데이트 관리 아래의 포털에 표시 되지 않는](#nologs) 단계를 수행 하 여 컴퓨터가 올바른 작업 영역에 보고 하는지 확인 합니다.
2. [Hybrid runbook 그룹을 삭제](../automation-hybrid-runbook-worker.md#remove-a-hybrid-worker-group)하 여 컴퓨터에서 오래 된 아티팩트를 정리한 후 다시 시도 하세요.

## <a name="machine-unable-to-communicate"></a>시나리오: 컴퓨터에서 서비스와 통신할 수 없습니다.

### <a name="issue"></a>문제

다음과 같은 오류 메시지 중 하나가 표시됩니다.

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception System.Net.Http.HttpRequestException: An error occurred while sending the request. ---> System.Net.WebException: The underlying connection was closed: An unexpected error occurred on a receive. ---> System.ComponentModel.Win32Exception: The client and server can't communicate, because they do not possess a common algorithm
```

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception Newtonsoft.Json.JsonReaderException: Error parsing positive infinity value.
```

```error
The certificate presented by the service <wsid>.oms.opinsights.azure.com was not issued by a certificate authority used for Microsoft services. Contact your network administrator to see if they are running a proxy that intercepts TLS/SSL communication.
```

```error
Access is denied. (Exception form HRESULT: 0x80070005(E_ACCESSDENIED))
```

### <a name="cause"></a>원인

프록시, 게이트웨이 또는 방화벽에서 네트워크 통신을 차단 하 고 있을 수 있습니다. 

### <a name="resolution"></a>해상도

네트워킹을 검토 하 고 적절 한 포트 및 주소가 허용 되는지 확인 합니다. 업데이트 관리 및 Hybrid Runbook Worker에 필요한 포트 및 주소 목록은 [네트워크 요구 사항](../automation-hybrid-runbook-worker.md#network-planning) 을 참조 하세요.

## <a name="unable-to-create-selfsigned-cert"></a>시나리오: 자체 서명된 인증서를 만들 수 없음

### <a name="issue"></a>문제

다음과 같은 오류 메시지 중 하나가 표시됩니다.

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception AgentService.HybridRegistration. PowerShell.Certificates.CertificateCreationException: Failed to create a self-signed certificate. ---> System.UnauthorizedAccessException: Access is denied.
```

### <a name="cause"></a>원인

Hybrid Runbook Worker 자체 서명 된 인증서를 생성할 수 없습니다.

### <a name="resolution"></a>해상도

시스템 계정에 **C:\ProgramData\Microsoft\Crypto\RSA** 폴더에 대 한 읽기 권한이 있는지 확인 한 후 다시 시도 하십시오.

## <a name="mw-exceeded"></a>시나리오: 예약 된 업데이트가 MaintenanceWindowExceeded 오류로 인해 실패 했습니다.

### <a name="issue"></a>문제

업데이트에 대 한 기본 유지 관리 기간은 120 분입니다. 유지 관리 기간은 최대 6 시간 또는 360 분으로 늘릴 수 있습니다.

### <a name="resolution"></a>해상도

모든 실패 한 예약 업데이트 배포를 편집 하 고 유지 관리 기간을 늘립니다.

유지 관리 기간에 대 한 자세한 내용은 [업데이트 설치](../automation-tutorial-update-management.md#schedule-an-update-deployment)를 참조 하세요.

## <a name="hresult"></a>시나리오: 컴퓨터를 "평가 되지 않음"으로 표시 하 고 HResult 예외를 표시 합니다.

### <a name="issue"></a>문제

* 컴퓨터가 **규정 준수**에서 **평가되지 않음**으로 표시되며 그 아래에 예외 메시지가 표시됩니다.
* 평가 되지 않음으로 표시 되는 컴퓨터가 있습니다.
* 포털에 HRESULT 오류 코드가 표시 됩니다.

### <a name="cause"></a>원인

업데이트 에이전트 (Windows의 Windows 업데이트 에이전트, Linux 배포용 패키지 관리자)가 올바르게 구성 되어 있지 않습니다. 업데이트 관리은 컴퓨터의 업데이트 에이전트를 사용 하 여 필요한 업데이트, 패치의 상태 및 배포 된 패치의 결과를 제공 합니다. 이 정보가 없으면 업데이트 관리는 필요한 패치를 제대로 보고할 수 없습니다.

### <a name="resolution"></a>해상도

컴퓨터에서 로컬로 업데이트를 수행 합니다. 이 작업이 실패 하면 일반적으로 업데이트 에이전트에 구성 오류가 있음을 의미 합니다.

이 문제는 네트워크 구성 및 방화벽 문제로 인해 발생 하는 경우가 많습니다. 다음을 시도해 보세요.

* Linux의 경우 적절 한 설명서를 확인 하 여 패키지 리포지토리의 네트워크 엔드포인트에 연결할 수 있는지 확인 합니다.
* Windows의 경우 [인트라넷 엔드포인트에서 다운로드 하지 않음 (WSUS/SCCM)](/windows/deployment/update/windows-update-troubleshooting#updates-arent-downloading-from-the-intranet-endpoint-wsussccm)에 나열 된 대로 에이전트 구성을 확인 합니다.
  * 컴퓨터가 Windows 업데이트 하도록 구성 된 경우 [HTTP/프록시와 관련 된 문제](/windows/deployment/update/windows-update-troubleshooting#issues-related-to-httpproxy)에 설명 된 엔드포인트에 연결할 수 있는지 확인 합니다.
  * Windows Server Update Services (WSUS)에 대해 구성 된 컴퓨터의 경우 [WUServer 레지스트리 키](/windows/deployment/update/waas-wu-settings)로 구성 된 wsus 서버에 연결할 수 있는지 확인 합니다.

HRESULT가 표시 되 면 빨간색으로 표시 된 예외를 두 번 클릭 하 여 전체 예외 메시지를 확인 합니다. 다음 표에서 잠재적 솔루션 또는 권장 조치를 검토 합니다.

|예외  |해결 방법 또는 동작  |
|---------|---------|
|`Exception from HRESULT: 0x……C`     | [Windows 업데이트 오류 코드 목록](https://support.microsoft.com/help/938205/windows-update-error-code-list) 에서 관련 오류 코드를 검색 하 여 예외의 원인에 대 한 추가 정보를 확인 합니다.        |
|`0x8024402C`</br>`0x8024401C`</br>`0x8024402F`      | 네트워크 연결 문제를 표시 합니다. 컴퓨터에 업데이트 관리에 대 한 네트워크 연결이 있는지 확인 합니다. 필요한 포트 및 주소 목록은 [네트워크 계획](../automation-update-management.md#ports) 섹션을 참조 하세요.        |
|`0x8024001E`| 서비스 또는 시스템이 종료 되었으므로 업데이트 작업이 완료 되지 않았습니다.|
|`0x8024002E`| Windows 업데이트 서비스를 사용할 수 없습니다.|
|`0x8024402C`     | WSUS 서버를 사용 하는 경우 `WUServer`에 대 한 레지스트리 값 및 `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate` 레지스트리 키 아래 `WUStatusServer` 올바른 WSUS 서버를 지정 해야 합니다.        |
|`0x80072EE2`|네트워크 연결 문제나 구성 된 WSUS 서버와 통신 하는 데 문제가 있습니다. WSUS 설정을 확인 하 고 클라이언트에서 서비스에 액세스할 수 있는지 확인 합니다.|
|`The service cannot be started, either because it is disabled or because it has no enabled devices associated with it. (Exception from HRESULT: 0x80070422)`     | Windows 업데이트 서비스 (wuauserv)가 실행 중이 고 사용 하지 않도록 설정 되어 있는지 확인 합니다.        |
|`0x80070005`| 액세스 거부 오류는 다음 중 하나에 의해 발생할 수 있습니다.<br> 감염 된 컴퓨터<br> Windows 업데이트 설정이 올바르게 구성 되지 않음<br> %WinDir%\SoftwareDistribution 폴더에 파일 사용 권한 오류가 있습니다.<br> 시스템 드라이브 (C:)의 디스크 공간이 부족 합니다.
|다른 제네릭 예외     | 인터넷에서 검색을 실행 하 여 가능한 해결 방법을 제공 하 고 로컬 IT 지원에 문의 하세요.         |

%Windir%\Windowsupdate.log 파일을 검토 하면 가능한 원인을 확인 하는 데 도움이 될 수도 있습니다. 로그를 읽는 방법에 대 한 자세한 내용은 [windowsupdate.log 파일을 읽는 방법](https://support.microsoft.com/en-ca/help/902093/how-to-read-the-windowsupdate-log-file)을 참조 하세요.

[Windows 업데이트 문제 해결사](https://support.microsoft.com/help/4027322/windows-update-troubleshooter) 를 다운로드 하 여 실행 하 여 컴퓨터에서 Windows 업데이트 관련 된 문제를 확인할 수도 있습니다.

> [!NOTE]
> [Windows 업데이트 문제 해결사](https://support.microsoft.com/help/4027322/windows-update-troubleshooter) 설명서는 windows 클라이언트에서 사용 하는 것을 나타내지만 windows Server 에서도 작동 합니다.

## <a name="scenario-update-run-returns-failed-status-linux"></a>시나리오: 업데이트 실행에서 "실패" 상태 반환 (Linux)

### <a name="issue"></a>문제

업데이트 실행이 시작 되지만 실행 중에 오류가 발생 합니다.

### <a name="cause"></a>원인

가능한 원인:

* 패키지 관리자가 비정상입니다.
* 업데이트 에이전트 (Windows 용 WUA, Linux 용 배포판 패키지 관리자)가 잘못 구성 되었습니다.
* 특정 패키지가 클라우드 기반 패치를 방해 합니다.
* 컴퓨터에 연결할 수 없습니다.
* 업데이트에 해결 되지 않은 종속성이 있습니다.

### <a name="resolution"></a>해상도

성공적으로 시작 된 후 업데이트를 실행 하는 동안 오류가 발생 하는 경우 실행 시 영향을 받는 컴퓨터에서 [작업 출력을 확인](../manage-update-multi.md#view-results-of-an-update-deployment) 합니다. 조사 하 고 조치를 취할 수 있는 특정 오류 메시지를 컴퓨터에서 찾을 수 있습니다. 업데이트 관리에서 업데이트 배포에 성공하려면 패키지 관리자가 정상 상태여야 합니다.

작업이 실패 하기 직전에 특정 패치, 패키지 또는 업데이트가 표시 되는 경우 다음 업데이트 배포에서 [제외](../automation-tutorial-update-management.md#schedule-an-update-deployment) 하는 것을 시도할 수 있습니다. Windows 업데이트에서 로그 정보를 수집 하려면 [Windows 업데이트 로그 파일](/windows/deployment/update/windows-update-logs)을 참조 하세요.

패치 문제를 해결할 수 없는 경우 다음 로그 파일의 복사본을 만들고 다음 업데이트 배포를 시작 *하기 전에* 문제 해결을 위해 유지 합니다.

```bash
/var/opt/microsoft/omsagent/run/automationworker/omsupdatemgmt.log
```

## <a name="patches-arent-installed"></a>패치가 설치 되지 않음

### <a name="machines-dont-install-updates"></a>컴퓨터에서 업데이트를 설치 하지 않음

* 머신에서 직접 업데이트를 실행해 봅니다. 컴퓨터에서 업데이트를 적용할 수 없는 경우 [문제 해결 가이드에서 잠재적 오류 목록을](https://docs.microsoft.com/azure/automation/troubleshoot/update-management#hresult)참조 하십시오.
* 업데이트를 로컬로 실행 하는 경우 [업데이트 관리에서 VM 제거의](https://docs.microsoft.com/azure/automation/automation-onboard-solutions-from-browse#clean-up-resources)지침에 따라 컴퓨터에서 에이전트를 제거 하 고 다시 설치 해 봅니다.

### <a name="i-know-updates-are-available-but-they-dont-show-as-available-on-my-machines"></a>업데이트를 사용할 수 있지만 내 컴퓨터에서 사용할 수 있는 것으로 표시 되지 않습니다.

* 이는 컴퓨터에서 WSUS 또는 Microsoft 엔드포인트 Configuration Manager 업데이트를 가져오도록 구성 되었지만 WSUS 및 Configuration Manager 업데이트를 승인 하지 않은 경우에 주로 발생 합니다.
* [이 문서의 "레지스트리를 편집 하 여 자동 업데이트 구성" 섹션의 레지스트리 키에 대 한 usewuserver가 레지스트리 키를 상호 참조](https://support.microsoft.com/help/328010/how-to-configure-automatic-updates-by-using-group-policy-or-registry-s)하 여 컴퓨터가 WSUS 및 SCCM에 대해 구성 되었는지 여부를 확인할 수 있습니다.
* 업데이트가 WSUS에서 승인 되지 않으면 설치 되지 않습니다. 다음 쿼리를 실행 하 여 Log Analytics에서 승인 되지 않은 업데이트를 확인할 수 있습니다.

  ```loganalytics
  Update | where UpdateState == "Needed" and ApprovalSource == "WSUS" and Approved == "False" | summarize max(TimeGenerated) by Computer, KBID, Title
  ```

### <a name="updates-show-as-installed-but-i-cant-find-them-on-my-machine"></a>업데이트는 설치 된 것으로 표시 되지만 내 컴퓨터에서 찾을 수 없습니다.

* 업데이트가 종종 다른 업데이트로 대체됩니다. 자세한 내용은 Windows 업데이트 문제 해결 가이드의 [업데이트를 대체](https://docs.microsoft.com/windows/deployment/update/windows-update-troubleshooting#the-update-is-not-applicable-to-your-computer) 합니다.

### <a name="installing-updates-by-classification-on-linux"></a>Linux에서 분류를 기준으로 업데이트 설치

* 분류("중요 업데이트 및 보안 업데이트")를 기준으로 Linux에 업데이트를 배포할 때, 특히 CentOS와 관련하여 중요한 주의 사항이 있습니다. 이러한 제한 사항은 [업데이트 관리 개요 페이지](https://docs.microsoft.com/azure/automation/automation-update-management#linux-2)에 설명 되어 있습니다.

### <a name="kb2267602-is-consistently-missing"></a>KB2267602가 일관 되 게 누락 됨

* KB2267602는 [Windows Defender 정의 업데이트](https://www.microsoft.com/wdsi/definitions)이며 매일 업데이트 됩니다.

## <a name="next-steps"></a>다음 단계

문제가 표시 되지 않거나 문제를 해결할 수 없는 경우 추가 지원을 위해 다음 채널 중 하나를 시도해 보세요.

* Azure [포럼](https://azure.microsoft.com/support/forums/)을 통해 azure 전문가 로부터 답변을 받으세요.
* Azure 커뮤니티를 적절 한 리소스 (답변, 지원 및 전문가)에 연결 하 여 고객 환경을 개선 하기 위한 공식 Microsoft Azure 계정인 [@AzureSupport](https://twitter.com/azuresupport)와 연결 하세요.
* Azure 지원 인시던트 제출 [Azure 지원 사이트](https://azure.microsoft.com/support/options/) 로 가서 **지원 받기**를 선택합니다.
