---
title: Azure 업데이트 관리에서 Linux 에이전트 확인 결과 이해
description: 업데이트 관리 에이전트의 문제를 해결하는 방법을 알아봅니다.
services: automation
author: georgewallace
ms.author: gwallace
ms.date: 04/22/2019
ms.topic: conceptual
ms.service: automation
ms.subservice: update-management
manager: carmonm
ms.openlocfilehash: 075cf254dbf7c5d03f1afac64315f6c6f773159c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60597805"
---
# <a name="understand-the-linux-agent-check-results-in-update-management"></a>업데이트 관리에서 Linux 에이전트 확인 결과 이해

업데이트 관리에서 컴퓨터가 **준비**를 표시하지 않는 이유에는 여러 가지가 있을 수 있습니다. 업데이트 관리에서 Hybrid Worker 에이전트의 상태를 확인하여 기본 문제를 검토할 수 있습니다. 이 문서에서는 Azure Portal의 Azure 머신 및 [오프라인 시나리오](#troubleshoot-offline)의 경우 Azure 이외 머신에 대해 문제 해결사를 실행하는 방법을 설명합니다.

다음 목록은 컴퓨터가 나타낼 수 있는 세 가지 준비 상태입니다.

* **준비** - 업데이트 에이전트가 배포되었으며 마지막으로 확인된 시간이 1시간 이내입니다.
* **연결이 끊김** - 업데이트 에이전트가 배포되었으며 마지막으로 확인된 시간이 1시간을 넘습니다.
* **구성되지 않음** - 업데이트 에이전트가 확인되지 않거나 온보딩을 완료하지 않았습니다.

> [!NOTE]
> Azure 포털에 표시 하 고 컴퓨터의 현재 상태 간에 약간의 지연이 있을 수 있습니다.

## <a name="start-the-troubleshooter"></a>문제 해결사 시작

Azure 머신의 경우 포털의 **업데이트 에이전트 준비** 열에서 **문제 해결** 링크를 클릭하여 **업데이트 에이전트 문제 해결** 페이지를 시작합니다. Azure 이외 머신의 경우 해당 링크를 클릭하면 이 문서로 이동합니다. Azure 이외 머신의 문제를 해결하려면 오프라인 지침을 참조하세요.

![VM 목록 페이지](../media/update-agent-issues-linux/vm-list.png)

> [!NOTE]
> 검사를 수행하려면 VM이 실행되고 있어야 합니다. VM이 실행되고 있지 않으면 **VM 시작** 단추가 제공됩니다.

**업데이트 에이전트 문제 해결** 페이지에서 **검사 실행**을 클릭하여 문제 해결사를 시작합니다. 문제 해결사는 [실행 명령](../../virtual-machines/linux/run-command.md)을 사용하여 컴퓨터에서 에이전트가 갖는 종속성을 확인하는 스크립트를 실행합니다. 문제 해결사가 완료되면 검사 결과를 반환합니다.

![문제 해결 페이지](../media/update-agent-issues-linux/troubleshoot-page.png)

완료되면 창에 결과가 반환됩니다. 검사 섹션에서는 각 검사가 찾으려는 정보를 제공합니다.

![업데이트 에이전트 검사 페이지](../media/update-agent-issues-linux/update-agent-checks.png)

## <a name="prerequisite-checks"></a>필수 구성 요소 검사

### <a name="operating-system"></a>운영 체제

OS 검사는 Hybrid Runbook Worker에서 다음 운영 체제 중 하나가 실행 중인지 확인합니다.

|운영 체제  |메모  |
|---------|---------|
|CentOS 6(x86/x64) 및 7(x64)      | Linux 에이전트에는 업데이트 리포지토리에 대한 액세스 권한이 있어야 합니다. 분류 기반 패치에는 CentOS에 기본 제공되지 않은 보안 데이터를 반환하기 위해 'yum'이 필요합니다.         |
|Red Hat Enterprise 6(x86/x64) 및 7(x64)     | Linux 에이전트에는 업데이트 리포지토리에 대한 액세스 권한이 있어야 합니다.        |
|SUSE Linux Enterprise Server 11(x86/x64) 및 12(x64)     | Linux 에이전트에는 업데이트 리포지토리에 대한 액세스 권한이 있어야 합니다.        |
|Ubuntu 14.04 LTS, 16.04 LTS 및 18.04 LTS(x86/x64)      |Linux 에이전트에는 업데이트 리포지토리에 대한 액세스 권한이 있어야 합니다.         |

## <a name="monitoring-agent-service-health-checks"></a>에이전트 서비스 상태 검사 모니터링

### <a name="oms-agent"></a>OMS 에이전트

이 검사를 통해 Linux용 OMS 에이전트가 설치되어 있는지 확인합니다. 이 에이전트를 설치하는 방법에 대한 지침은 [Linux용 에이전트 설치](../../azure-monitor/learn/quick-collect-linux-computer.md#install-the-agent-for-linux
)를 참조하세요.

### <a name="oms-agent-status"></a>OMS 에이전트 상태

이 검사를 통해 Linux용 OMS 에이전트가 실행되고 있는지 확인합니다. 에이전트가 실행되지 않는 경우 다음 명령을 실행하여 다시 시작할 수 있습니다. 에이전트 문제 해결에 대한 자세한 내용은 [Linux Hybrid Runbook Worker 문제 해결](hybrid-runbook-worker.md#linux)을 참조하세요.

```bash
sudo /opt/microsoft/omsagent/bin/service_control restart
```

### <a name="multihoming"></a>멀티 호밍

이 검사에서는 에이전트가 여러 작업 영역에 보고하는지 확인합니다. 멀티 호밍은 업데이트 관리에서 지원되지 않습니다.

### <a name="hybrid-runbook-worker"></a>Hybrid Runbook Worker

이를 통해 Linux용 OMS 에이전트에 Hybrid Runbook Worker 패키지가 포함되어 있는지 확인합니다. 이 패키지에는 작업할 업데이트 관리가 필요합니다.

### <a name="hybrid-runbook-worker-status"></a>Hybrid Runbook Worker 상태

이 검사에서는 Hybrid Runbook Worker가 머신에서 실행되고 있는지 확인합니다. Hybrid Runbook Worker가 제대로 실행되는 경우 다음 프로세스가 있어야 합니다. 자세한 내용은 [Linux용 Log Analytics 에이전트 문제 해결](hybrid-runbook-worker.md#oms-agent-not-running)을 참조하세요.

```bash
nxautom+   8567      1  0 14:45 ?        00:00:00 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/main.py /var/opt/microsoft/omsagent/state/automationworker/oms.conf rworkspace:<workspaceId> <Linux hybrid worker version>
nxautom+   8593      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/state/automationworker/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
nxautom+   8595      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/<workspaceId>/state/automationworker/diy/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
```

## <a name="connectivity-checks"></a>연결 검사

### <a name="general-internet-connectivity"></a>일반적인 인터넷 연결

이 검사에서는 머신이 인터넷에 대한 액세스 권한이 있는지 확인합니다.

### <a name="registration-endpoint"></a>등록 엔드포인트

이 검사는 에이전트가 에이전트 서비스와 제대로 통신할 수 있는지를 확인합니다.

프록시 및 방화벽 구성에서는 Hybrid Runbook Worker 에이전트가 등록 엔드포인트와 통신하도록 허용해야 합니다. 주소 및 열 포트 목록에 대해서는 [Hybrid Worker에 대한 네트워크 계획](../automation-hybrid-runbook-worker.md#network-planning)을 참조하세요.

### <a name="operations-endpoint"></a>작업 엔드포인트

이 검사는 에이전트가 작업 런타임 데이터 서비스와 제대로 통신할 수 있는지를 확인합니다.

프록시 및 방화벽 구성에서는 Hybrid Runbook Worker 에이전트가 작업 런타임 데이터 서비스와 통신하도록 허용해야 합니다. 주소 및 열 포트 목록에 대해서는 [Hybrid Worker에 대한 네트워크 계획](../automation-hybrid-runbook-worker.md#network-planning)을 참조하세요.

### <a name="log-analytics-endpoint-1"></a>Log Analytics 엔드포인트 1

이 검사에서는 머신이 Log Analytics 에이전트에 필요한 엔드포인트에 대해 액세스 권한이 있는지 확인합니다.

### <a name="log-analytics-endpoint-2"></a>Log Analytics 엔드포인트 2

이 검사에서는 머신이 Log Analytics 에이전트에 필요한 엔드포인트에 대해 액세스 권한이 있는지 확인합니다.

### <a name="log-analytics-endpoint-3"></a>Log Analytics 엔드포인트 3

이 검사에서는 머신이 Log Analytics 에이전트에 필요한 엔드포인트에 대해 액세스 권한이 있는지 확인합니다.

## <a name="troubleshoot-offline"></a>오프라인으로 문제 해결

이 스크립트를 로컬로 실행하여 Hybrid Runbook Worker에서 오프라인으로 문제 해결사를 사용할 수 있습니다. Python 스크립트 [update_mgmt_health_check.py](https://gallery.technet.microsoft.com/scriptcenter/Troubleshooting-utility-3bcbefe6)는 스크립트 센터에서 찾을 수 있습니다. 이 스크립트의 출력 예제는 다음 예제에 표시됩니다.

```output
Debug: Machine Information:   Static hostname: LinuxVM2
         Icon name: computer-vm
           Chassis: vm
        Machine ID: 00000000000000000000000000000000
           Boot ID: 00000000000000000000000000000000
    Virtualization: microsoft
  Operating System: Ubuntu 16.04.5 LTS
            Kernel: Linux 4.15.0-1025-azure
      Architecture: x86-64


Passed: Operating system version is supported

Passed: Microsoft Monitoring agent is installed

Debug: omsadmin.conf file contents:
        WORKSPACE_ID=00000000-0000-0000-0000-000000000000
        AGENT_GUID=00000000-0000-0000-0000-000000000000
        LOG_FACILITY=local0
        CERTIFICATE_UPDATE_ENDPOINT=https://00000000-0000-0000-0000-000000000000.oms.opinsights.azure.com/ConfigurationService.Svc/RenewCertificate
        URL_TLD=opinsights.azure.com
        DSC_ENDPOINT=https://scus-agentservice-prod-1.azure-automation.net/Accou            nts/00000000-0000-0000-0000-000000000000/Nodes\(AgentId='00000000-0000-0000-0000-000000000000'\)
        OMS_ENDPOINT=https://00000000-0000-0000-0000-000000000000.ods.opinsights            .azure.com/OperationalData.svc/PostJsonDataItems
        AZURE_RESOURCE_ID=/subscriptions/00000000-0000-0000-0000-000000000000/re            sourcegroups/myresourcegroup/providers/microsoft.compute/virtualmachines/linuxvm            2
        OMSCLOUD_ID=0000-0000-0000-0000-0000-0000-00
        UUID=00000000-0000-0000-0000-000000000000


Passed: Microsoft Monitoring agent is running

Passed: Machine registered with log analytics workspace:['00000000-0000-0000-0000-000000000000']

Passed: Hybrid worker package is present

Passed: Hybrid worker is running

Passed: Machine is connected to internet

Passed: TCP test for {scus-agentservice-prod-1.azure-automation.net} (port 443)             succeeded

Passed: TCP test for {eus2-jobruntimedata-prod-su1.azure-automation.net} (port 4            43) succeeded

Passed: TCP test for {00000000-0000-0000-0000-000000000000.ods.opinsights.azure.            com} (port 443) succeeded

Passed: TCP test for {00000000-0000-0000-0000-000000000000.oms.opinsights.azure.            com} (port 443) succeeded

Passed: TCP test for {ods.systemcenteradvisor.com} (port 443) succeeded

```

## <a name="next-steps"></a>다음 단계

Hybrid Runbook Worker의 추가 문제를 해결하려면 [문제 해결 - Hybrid Runbook Worker](hybrid-runbook-worker.md)를 참조하세요.

