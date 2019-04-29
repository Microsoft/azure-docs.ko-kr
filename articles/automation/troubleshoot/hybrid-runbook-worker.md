---
title: 문제 해결 - Azure Automation Hybrid Runbook Worker
description: 이 문서에서는 Azure Automation Hybrid Runbook Worker 문제 해결 정보를 제공합니다.
services: automation
ms.service: automation
ms.subservice: ''
author: georgewallace
ms.author: gwallace
ms.date: 02/12/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: ea6599152d3cbf1f50132f5b207c19148401f798
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60564262"
---
# <a name="troubleshoot-hybrid-runbook-workers"></a>Hybrid Runbook Worker 문제 해결

이 문서에서는 Hybrid Runbook Worker 문제 해결에 대한 정보를 제공합니다.

## <a name="general"></a>일반

Hybrid Runbook Worker는 Automation 계정과 통신하여 작업자를 등록하고, Runbook 작업을 수신하고, 상태를 보고하는 에이전트를 사용합니다. 이 에이전트는 Windows의 경우 Microsoft Monitoring Agent이며, Linux의 경우 Linux용 OMS 에이전트입니다.

### <a name="runbook-execution-fails"></a>시나리오: Runbook 실행 실패

#### <a name="issue"></a>문제

Runbook 실행에 실패하고 다음 오류가 발생합니다.

```error
"The job action 'Activate' cannot be run, because the process stopped unexpectedly. The job action was attempted three times."
```

Runbook이 3회 실행 시도 직후 일시 중단됩니다. Runbook 완료를 중단할 수 있는 조건이 있습니다. 이 경우 관련 오류 메시지에 이유를 알려 주는 추가 정보가 포함되지 않을 수 있습니다.

#### <a name="cause"></a>원인

가능한 원인은 다음과 같습니다.

* 로컬 리소스를 사용하여 Runbook을 인증할 수 없습니다.

* Hybrid Worker가 프록시 또는 방화벽 뒤에 있습니다.

* 로컬 리소스를 사용하여 Runbook을 인증할 수 없습니다.

* Hybrid Runbook Worker 기능을 실행하도록 구성된 컴퓨터가 최소 하드웨어 요구 사항을 충족하지 않습니다.

#### <a name="resolution"></a>해결 방법

*.azure-automation.net에 대한 아웃바운드 액세스 권한이 컴퓨터의 443 포트에 있는지 확인합니다.

Hybrid Runbook Worker가 실행되는 컴퓨터는 이 기능을 호스트하도록 구성하기 전에, 최소 하드웨어 요구 사항을 충족해야 합니다. 컴퓨터에서 사용하는 Runbook 및 백그라운드 프로세스로 인해 시스템이 지나치게 사용되고 Runbook 작업이 지연되거나 시간이 초과될 수 있습니다.

Hybrid Runbook Worker 기능을 실행할 컴퓨터가 최소 하드웨어 요구 사항을 충족하는지 확인합니다. 충족하는 경우 CPU 및 메모리 사용을 모니터링하여 Hybrid Runbook Worker 프로세스의 성능과 Windows 사이에 어떠한 상관 관계가 있는지 확인합니다. 메모리 또는 CPU 압력이 있는 경우 리소스를 업그레이드해야 하는지 여부가 이 상관 관계에 나타날 수 있습니다. 최소 요구 사항을 지원할 수 있는 다른 컴퓨팅 리소스를 선택하고 워크로드 수요가 증가의 필요성을 나타낼 경우 규모를 확장할 수도 있습니다.

**Microsoft-SMA** 이벤트 로그에 *Win32 프로세스가[4294967295] 코드와 함께 종료되었습니다.* 라고 설명하는 이벤트가 있는지 확인합니다. 이 오류의 원인은 Runbook에 인증을 구성하지 않았거나 Hybrid Worker 그룹에 대해 실행 자격 증명을 지정하지 않았기 때문일 수 있습니다. [Runbook 권한](../automation-hrw-run-runbooks.md#runbook-permissions)을 검토하여 Runbook에 대한 인증을 올바르게 구성했는지 확인합니다.

### <a name="no-cert-found"></a>시나리오: Hybrid Runbook Worker의 인증서 저장소에서 인증서를 찾을 수 없음

#### <a name="issue"></a>문제

Hybrid Runbook Worker에서 실행되는 Runbook이 실패하고 다음 오류 메시지가 표시됩니다.

```error
Connect-AzureRmAccount : No certificate was found in the certificate store with thumbprint 0000000000000000000000000000000000000000
At line:3 char:1
+ Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID -Appl ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Connect-AzureRmAccount], ArgumentException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Profile.ConnectAzureRmAccountCommand
```

#### <a name="cause"></a>원인

이 오류는 실행 계정 인증서가 없는 Hybrid Runbook Worker에서 실행되는 Runbook에서 [실행 계정](../manage-runas-account.md)을 사용하려고 시도할 때 발생합니다. Hybrid Runbook Worker의 경우 제대로 작동하기 위해 실행 계정에 필요한 인증서 자산이 기본적으로 로컬에 없습니다.

#### <a name="resolution"></a>해결 방법

Hybrid Runbook Worker가 Azure VM인 경우 [Azure 리소스의 관리 ID](../automation-hrw-run-runbooks.md#managed-identities-for-azure-resources)를 대신 사용할 수 있습니다. 이 시나리오를 사용하면 실행 계정 대신 Azure VM의 관리 ID를 사용하여 Azure 리소스에 인증할 수 있어서 인증이 간소화됩니다. Hybrid Runbook Worker가 온-프레미스 머신인 경우 머신에 실행 계정 인증서를 설치해야 합니다. 인증서를 설치하는 방법을 알아보려면 [Export-RunAsCertificateToHybridWorker](../automation-hrw-run-runbooks.md#runas-script) Runbook을 실행하는 단계를 참조하세요.

## <a name="linux"></a>Linux

Linux Hybrid Runbook Worker는 Automation 계정과 통신하여 작업자를 등록하고, Runbook 작업을 수신하고, 상태를 보고하는 Linux용 OMS 에이전트에 따라 달라집니다. 작업자 등록이 실패하는 경우 다음과 같은 몇 가지 오류 원인이 있을 수 있습니다.

### <a name="oms-agent-not-running"></a>시나리오: Linux용 OMS 에이전트가 실행되고 있지 않습니다.

#### <a name="issue"></a>문제

Linux용 OMS 에이전트가 실행되고 있지 않습니다.

#### <a name="cause"></a>원인

Linux용 OMS 에이전트가 실행되고 있지 않으면 Linux Hybrid Runbook Worker가 Azure Automation과 통신할 수 없습니다. 다양한 이유로 에이전트를 실행하지 못할 수 있습니다.

#### <a name="resolution"></a>해결 방법

 `ps -ef | grep python` 명령을 입력하여 에이전트가 실행 중인지 확인하세요. 다음과 비슷한 출력, 즉 **nxautomation** 사용자 계정을 사용하는 python 프로세스가 표시됩니다. 업데이트 관리 또는 Azure Automation 솔루션을 사용하도록 설정하지 않은 경우 다음 프로세스 중 어떤 것도 실행되지 않습니다.

```bash
nxautom+   8567      1  0 14:45 ?        00:00:00 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/main.py /var/opt/microsoft/omsagent/state/automationworker/oms.conf rworkspace:<workspaceId> <Linux hybrid worker version>
nxautom+   8593      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/state/automationworker/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
nxautom+   8595      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/<workspaceId>/state/automationworker/diy/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
```

다음 목록은 Linux Hybrid Runbook Worker에 대해 시작된 프로세스를 보여줍니다. 모두 `/var/opt/microsoft/omsagent/state/automationworker/` 디렉터리에 있습니다.


* **oms.conf** - 이 값은 작업자 관리자 프로세스입니다. DSC에서 직접 시작됩니다.

* **worker.conf** - 이 프로세스는 자동 등록 Hybrid Worker 프로세스로, 작업자 관리자가 시작합니다. 이 프로세스는 업데이트 관리에서 사용되며 사용자에게 투명합니다. 컴퓨터에서 업데이트 관리 솔루션을 사용하도록 설정하지 않으면 이 프로세스가 없습니다.

* **diy/worker.conf** - 이 프로세스는 DIY 하이브리드 작업자 프로세스입니다. DIY 하이브리드 작업자 프로세스는 Hybrid Runbook Worker에서 사용자 Runbook을 실행하는 데 사용됩니다. 다른 구성을 사용한다는 점 외에는 자동 등록 하이브리드 작업자 프로세스와 차이가 없습니다. Azure Automation 솔루션을 사용하지 않도록 설정하고 DIY Linux Hybrid Worker가 등록되지 않으면 이 프로세스가 없습니다.

Linux용 OMS 에이전트가 실행되고 있지 않으면 `sudo /opt/microsoft/omsagent/bin/service_control restart` 명령을 실행하여 서비스를 시작합니다.

### <a name="class-does-not-exist"></a>시나리오: 지정된 클래스가 없음

오류가 표시되는 경우 **지정된 클래스가 없습니다.** `/var/opt/microsoft/omsconfig/omsconfig.log`에서 보이는 경우 Linux용 OMS 에이전트를 업데이트해야 합니다. 다음 명령을 실행하여 OMS 에이전트를 다시 설치합니다.

```bash
wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <WorkspaceID> -s <WorkspaceKey>
```

## <a name="windows"></a>Windows

Windows Hybrid Runbook Worker는 Automation 계정과 통신하여 작업자를 등록하고, Runbook 작업을 수신하고, 상태를 보고하는 Microsoft Monitoring Agent에 따라 달라집니다. 작업자 등록이 실패하는 경우 다음과 같은 몇 가지 오류 원인이 있을 수 있습니다.

### <a name="mma-not-running"></a>시나리오: Microsoft Monitoring Agent가 실행되고 있지 않음

#### <a name="issue"></a>문제

`healthservice` 서비스가 Hybrid Runbook Worker 컴퓨터에서 실행되고 있지 않습니다.

#### <a name="cause"></a>원인

Microsoft Monitoring Agent Windows 서비스가 실행되고 있지 않으면 이 상태로 인해 Hybrid Runbook Worker가 Azure Automation과 통신할 수 없습니다.

#### <a name="resolution"></a>해결 방법

PowerShell에서 `Get-Service healthservice` 명령을 입력하여 에이전트가 실행 중인지 확인하세요. 서비스가 중지된 경우 PowerShell에서 `Start-Service healthservice` 명령을 입력하여 서비스를 시작하세요.

### <a name="event-4502"></a> Operations Manager 로그의 4502 이벤트

#### <a name="issue"></a>문제

**Application and Services Logs\Operations Manager** 이벤트 로그에서 이벤트 4502 및 다음 설명과 **Microsoft.EnterpriseManagement.HealthService.AzureAutomation.HybridAgent**를 포함하는 EventMessage를 볼 수 있습니다. *\<wsid\>.oms.opinsights.azure.com 서비스에서 제공한 인증서를 Microsoft 서비스에 사용된 인증 기관에서 발급하지 않았습니다. 네트워크 관리자에게 문의하여 TLS/SSL 통신을 가로채는 프록시를 실행하고 있는지 확인하세요. 문서 KB3126513에는 연결 문제에 대한 추가 문제 해결 정보가 들어 있습니다.*

#### <a name="cause"></a>원인

이 문제는 프록시 또는 네트워크 방화벽이 Microsoft Azure와의 통신을 차단하기 때문일 수 있습니다. *.azure-automation.net에 대한 아웃바운드 액세스 권한이 컴퓨터의 443 포트에 있는지 확인합니다.

#### <a name="resolution"></a>해결 방법

로그는 각 Hybrid Worker의 로컬에 저장되며 위치는 C:\ProgramData\Microsoft\System Center\Orchestrator\7.2\SMA\Sandboxes입니다. Azure Automation에 역할을 온보딩하는 데 영향을 미치는 연결 또는 기타 문제가 있거나 정상 작동 동안 문제가 발생했음을 나타내는 경고 또는 오류 이벤트가 **Application and Services Logs\Microsoft SMA\Operations** 및 **Application and Services Logs\Operations Manager** 이벤트 로그에 있는지 확인할 수 있습니다.

[Runbook 출력 및 메시지](../automation-runbook-output-and-messages.md)는 클라우드에서 실행되는 Runbook 작업처럼 Hybrid Worker에서 Azure Automation으로 전송됩니다. Verbose 및 Progress 스트림을 다른 Runbook과 같은 방식으로 사용할 수도 있습니다.

### <a name="corrupt-cache"></a> Hybrid Runbook Worker가 보고하지 않음

#### <a name="issue"></a>문제

Hybrid Runbook Worker 시스템이 실행 중이지만 작업 영역에 해당 시스템에 대한 하트비트 데이터가 표시되지 않습니다.

다음 예제 쿼리는 작업 영역의 시스템과 해당 마지막 하트비트를 보여 줍니다.

```loganalytics
// Last heartbeat of each computer
Heartbeat 
| summarize arg_max(TimeGenerated, *) by Computer
```

#### <a name="cause"></a>원인

이 문제는 Hybrid Runbook Worker의 손상된 캐시로 인해 발생할 수 있습니다.

#### <a name="resolution"></a>해결 방법

이 문제를 해결하려면 Hybrid Runbook Worker에 로그인하고 다음 스크립트를 실행합니다. 이 스크립트는 Microsoft Monitoring Agent를 중지하고 해당 캐시를 제거한 후 서비스를 다시 시작합니다. 이 작업을 통해 Hybrid Runbook Worker는 강제로 Azure Automation에서 해당 구성을 다시 다운로드하게 됩니다.

```powershell
Stop-Service -Name HealthService

Remove-Item -Path 'C:\Program Files\Microsoft Monitoring Agent\Agent\Health Service State' -Recurse

Start-Service -Name HealthService
```

### <a name="already-registered"></a>시나리오: Hybrid Runbook Worker를 추가할 수 없습니다.

#### <a name="issue"></a>문제

`Add-HybridRunbookWorker` cmdlet을 사용하여 Hybrid Runbook Worker를 추가하려고 할 때 다음 메시지가 수신됩니다.

```error
Machine is already registered
```

#### <a name="cause"></a>원인

이는 머신이 이미 다른 Automation 계정에 등록되어 있거나 머신에서 Hybrid Runbook Worker를 제거한 후 다시 추가하려고 하는 경우에 발생할 수 있습니다.

#### <a name="resolution"></a>해결 방법

이 문제를 해결하려면 다음 레지스트리 키를 제거하고 `HealthService`를 다시 시작한 다음, `Add-HybridRunbookWorker` cmdlet을 다시 시도합니다.

`HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\HybridRunbookWorker`

## <a name="next-steps"></a>다음 단계

문제가 표시되지 않거나 문제를 해결할 수 없는 경우 다음 채널 중 하나를 방문하여 추가 지원을 받으세요.

* [Azure 포럼](https://azure.microsoft.com/support/forums/)을 통해 Azure 전문가로부터 답변을 얻습니다.
* [@AzureSupport](https://twitter.com/azuresupport)를 사용하여 연결 – Azure 커뮤니티를 적절한 리소스(답변, 지원 및 전문가)에 연결하여 고객 환경을 개선하는 공식 Microsoft Azure 계정입니다.
* 추가 지원이 필요한 경우, Azure 기술 지원 인시던트를 제출할 수 있습니다. [Azure 지원 사이트](https://azure.microsoft.com/support/options/) 로 가서 **지원 받기**를 선택합니다.

