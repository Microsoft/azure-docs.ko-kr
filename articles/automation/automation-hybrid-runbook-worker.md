---
title: Azure Automation Hybrid Runbook Worker
description: 이 문서에서는 로컬 데이터 센터 또는 클라우드 공급자의 컴퓨터에서 Runbook을 실행할 수 있도록 해주는 Azure Automation의 기능인 Hybrid Runbook Worker를 설치하고 사용하는 방법에 대한 정보를 제공합니다.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 04/05/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 55dff6cf073612e3e5473da3a5f1bf722b2ccdbd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60739359"
---
# <a name="automate-resources-in-your-datacenter-or-cloud-by-using-hybrid-runbook-worker"></a>Hybrid Runbook Worker를 사용하여 데이터 센터 또는 클라우드의 리소스 자동화

Azure Automation의 Runbook은 Azure 클라우드 플랫폼에서 실행되므로 다른 클라우드 또는 온-프레미스 환경의 리소스에 액세스하지 못할 수도 없습니다. Azure Automation의 Hybrid Runbook Worker 기능을 사용하여 역할을 호스팅하는 컴퓨터에서 직접 그리고 환경의 리소스에 대해 Runbook을 실행하여 해당 로컬 리소스를 관리할 수 있습니다. Runbook은 Azure Automation에 저장 및 관리된 후 하나 이상의 할당된 컴퓨터에 전달됩니다.

이 기능은 다음 이미지에 나와 있습니다.

![Hybrid Runbook Worker 개요](media/automation-hybrid-runbook-worker/automation.png)

각 Hybrid Runbook Worker는 에이전트를 설치할 때 지정한 Hybrid Runbook Worker 그룹의 구성원입니다. 그룹은 단일 에이전트를 포함할 수 있지만 고가용성을 위해 그룹에 여러 에이전트를 설치할 수 있습니다.

Hybrid Runbook Worker에서 Runbook을 시작할 경우 이를 실행할 그룹을 지정합니다. 그룹의 각 작업자는 Azure Automation을 폴링하여 사용할 수 있는 작업이 있는지 확인합니다. 작업을 사용할 수 있는 경우 작업을 가져올 수 있는 첫 번째 작업자가 해당 작업을 수행합니다. 작업 큐의 처리 시간은 하이브리드 작업자 하드웨어 프로필 및 로드에 따라 달라집니다. 특정 작업자를 지정할 수 없습니다. Hybrid Runbook Worker는 Azure의 샌드박스에 있는 제한 중 다수를 공유하지 않습니다. 디스크 공간, 메모리 또는 네트워크 소켓에 동일한 제한이 없습니다. Hybrid Runbook Worker는 Hybrid Runbook Worker 자체의 리소스에 의해서만 제한됩니다. 또한 Hybrid Runbook Worker는 Azure 샌드박스가 적용하는 180분 [공평 분배](automation-runbook-execution.md#fair-share) 시한을 공유하지 않습니다. Azure 샌드박스 및 Hybrid Runbook Worker의 서비스 제한에 대해 자세히 알아보려면 [한도](../azure-subscription-service-limits.md#automation-limits) 페이지를 참조하세요.

> [!NOTE]
> Azure 중국에서 hybrid Runbook Worker는 사용 하는 것이 없습니다.

## <a name="install-a-hybrid-runbook-worker"></a>Hybrid Runbook Worker 설치

Hybrid Runbook Worker를 설치하는 프로세스는 OS에 따라 다릅니다. 설치에 사용할 수 있는 방법에 대한 링크는 다음 표에 있습니다.

Windows Hybrid Runbook Worker를 설치 및 구성하려면 2가지 방법을 사용할 수 있습니다. 권장되는 방법은 Automation Runbook을 사용하여 Windows 컴퓨터를 구성하는 프로세스를 완전히 자동화하는 것입니다. 두 번째 방법은 단계별 절차에 따라 역할을 수동으로 설치하고 구성하는 것입니다. Linux 컴퓨터의 경우 Python 스크립트를 실행하여 컴퓨터에 에이전트를 설치합니다.

|OS  |배포 형식  |
|---------|---------|
|Windows     | [PowerShell](automation-windows-hrw-install.md#automated-deployment)<br>[수동](automation-windows-hrw-install.md#manual-deployment)        |
|Linux     | [Python](automation-linux-hrw-install.md#installing-a-linux-hybrid-runbook-worker)        |

> [!NOTE]
> DSC(필요한 상태 구성)로 Hybrid Runbook Worker 역할을 지원하는 서버의 구성을 관리하려면 이들을 DSC 노드로 추가해야 합니다. DSC를 통한 관리를 위한 온보드에 대한 정보는 [Azure Automation DSC를 통한 관리를 위한 컴퓨터 온보드](automation-dsc-onboarding.md)를 참조하세요.
>
>[업데이트 관리 솔루션](automation-update-management.md)을 사용하도록 설정하면 이 솔루션에 포함된 Runbook을 지원하기 위해 Azure Log Analytics 작업 영역에 연결된 모든 컴퓨터가 자동으로 Hybrid Runbook Worker로 구성됩니다. 그러나 컴퓨터가 Automation 계정에서 이미 정의한 어떤 Hybrid Worker 그룹에도 등록되지 않았습니다. 솔루션과 Hybrid Runbook Worker 그룹 멤버 자격에 동일한 계정을 사용하는 한 Automation Runbook을 지원하기 위해 Automation 계정의 Hybrid Runbook Worker 그룹에 컴퓨터를 추가할 수 있습니다. 이 기능은 Hybrid Runbook Worker의 7.2.12024.0 버전에 추가되었습니다.

Hybrid Runbook Worker 배포를 시작하기 전에 [네트워크 계획 정보](#network-planning)를 검토합니다. 작업자를 성공적으로 배포한 후에는 [Hybrid Runbook Worker에서 Runbook 실행](automation-hrw-run-runbooks.md)을 검토하여 온-프레미스 데이터 센터 또는 다른 클라우드 환경의 프로세스를 자동화하도록 Runbook을 구성하는 방법을 알아봅니다.

## <a name="remove-a-hybrid-runbook-worker"></a>Hybrid Runbook Worker 제거

그룹에서 하나 이상의 Hybrid Runbook Worker를 제거하거나 요구 사항에 따라 그룹을 제거할 수 있습니다. 온-프레미스 컴퓨터에서 Hybrid Runbook Worker를 제거하려면 다음 단계를 수행합니다.

1. Azure Portal에서 Automation 계정으로 이동합니다.
2. **계정 설정**에서 **키**를 선택하고 **URL** 및 **기본 액세스 키**의 값을 확인합니다. 이 정보는 다음 단계에서 필요합니다.

### <a name="windows"></a>Windows

관리자 모드에서 PowerShell 세션을 열고 다음 명령을 실행합니다. 제거 과정에 대한 자세한 로그를 보려면 **-Verbose** 스위치를 사용합니다.

```powershell-interactive
Remove-HybridRunbookWorker -url <URL> -key <PrimaryAccessKey>
```

Hybrid Worker 그룹에서 부실한 컴퓨터를 제거하려면 선택적 `machineName` 매개 변수를 사용합니다.

```powershell-interactive
Remove-HybridRunbookWorker -url <URL> -key <PrimaryAccessKey> -machineName <ComputerName>
```

### <a name="linux"></a>Linux

Hybrid Runbook Worker에서 `ls /var/opt/microsoft/omsagent` 명령을 사용하여 workspaceid를 가져올 수 있습니다. 디렉터리에 폴더 이름이 작업 영역 ID인 폴더가 있습니다.

```bash
sudo python onboarding.py --deregister --endpoint="<URL>" --key="<PrimaryAccessKey>" --groupname="Example" --workspaceid="<workspaceId>"
```

> [!NOTE]
> 이 코드는 컴퓨터의 Microsoft Monitoring Agent가 아닌 Hybrid Runbook Worker 역할의 기능 및 구성만을 제거합니다.

## <a name="remove-a-hybrid-worker-group"></a>Hybrid Worker 그룹 제거

그룹을 제거하려면 먼저 앞서 보았던 절차를 사용하여 그룹의 구성원인 모든 컴퓨터에서 Hybrid Runbook Worker를 제거합니다. 이후 다음 단계를 사용하여 그룹을 제거합니다.

1. Azure Portal에서 Automation 계정을 엽니다.
2. **프로세스 자동화**에서 **Hybrid Worker 그룹**을 선택합니다. 삭제할 그룹을 선택합니다. 해당 그룹에 대한 속성 페이지가 표시됩니다.

   ![속성 페이지](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-group-properties.png)

3. 선택한 그룹에 대한 속성 페이지에서 **삭제**를 선택합니다. 이 작업의 확인을 요청하는 메시지가 나타납니다. 계속하려면 **예**를 선택합니다.

   ![확인 메시지](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-confirm-delete.png)

   이 프로세스를 마치는 데 몇 초 정도 걸릴 수 있습니다. 메뉴의 **알림**에서 진행 상황을 추적할 수 있습니다.

## <a name="network-planning"></a>네트워크 구성

### <a name="hybrid-worker-role"></a>Hybrid Worker 역할

연결 하 여 Azure Automation을 등록 하는 Hybrid Runbook Worker에 대 한 포트 번호 및이 섹션에 설명 된 Url에 액세스할 수 있어야 합니다. 이 액세스는 위쪽를 [Microsoft Monitoring Agent에 대 한 필요한 포트 및 Url](../azure-monitor/platform/agent-windows.md) Azure Monitor 로그에 연결 합니다.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

에이전트와 Azure Automation 서비스 간의 통신에 프록시 서버를 사용 하는 경우 적절 한 리소스에 액세스할 수 있는지 확인 합니다. Hybrid Runbook Worker에서 Automation 서비스 요청에 대 한 제한 시간은 30 초입니다. 3 번 시도 후 요청이 실패 합니다. 방화벽을 사용하여 인터넷 액세스를 제한하는 경우 액세스를 허용하도록 방화벽을 구성해야 합니다. Log Analytics 게이트웨이를 프록시로 사용하면 하이브리드 작업자에 대해 구성되었는지 확인합니다. 이 작업을 수행하는 방법에 대한 지침은 [Automation Hybrid Worker에 대한 Log Analytics 게이트웨이 구성](https://docs.microsoft.com/azure/log-analytics/log-analytics-oms-gateway)을 참조하세요.

Hybrid Runbook Worker 역할에서 Automation 역할과 통신하려면 다음 포트와 URL이 필요합니다.

* 포트: 아웃바운드 인터넷 액세스에는 443 TCP 포트만 필요합니다.
* 글로벌 URL: *.azure-automation.net
* US Gov 버지니아의 전역 URL: *.azure-automation.us
* 에이전트 서비스: https://\<workspaceId\>.agentsvc.azure-automation.net

예외를 정의할 때 나열된 주소를 사용하는 것이 좋습니다. IP 주소의 경우 [Microsoft Azure 데이터 센터 IP 범위](https://www.microsoft.com/download/details.aspx?id=41653)를 다운로드할 수 있습니다. 이 파일은 매주 업데이트되고 현재 배포된 범위와 향후 예정된 IP 범위 변경 내용을 포함합니다.

특정 지역에 대해 정의된 Automation 계정이 있는 경우 해당 지역 데이터 센터에 대한 통신을 제한할 수 있습니다. 다음 표에서는 각 지역에 대한 DNS 레코드를 제공합니다.

| **지역** | **DNS 레코드** |
| --- | --- |
| 미국 중서부 | wcus-jobruntimedata-prod-su1.azure-automation.net</br>wcus-agentservice-prod-1.azure-automation.net |
| 미국 중남부 |scus-jobruntimedata-prod-su1.azure-automation.net</br>scus-agentservice-prod-1.azure-automation.net |
| 미국 동부 2 |eus2-jobruntimedata-prod-su1.azure-automation.net</br>eus2-agentservice-prod-1.azure-automation.net |
| 미국 서부 2 |wus2-jobruntimedata-prod-su1.azure-automation.net</br>wus2-agentservice-prod-1.azure-automation.net |
| 캐나다 중부 |cc-jobruntimedata-prod-su1.azure-automation.net</br>cc-agentservice-prod-1.azure-automation.net |
| 서유럽 |we-jobruntimedata-prod-su1.azure-automation.net</br>we-agentservice-prod-1.azure-automation.net |
| 북유럽 |ne-jobruntimedata-prod-su1.azure-automation.net</br>ne-agentservice-prod-1.azure-automation.net |
| 동남아시아 |sea-jobruntimedata-prod-su1.azure-automation.net</br>sea-agentservice-prod-1.azure-automation.net|
| 인도 중부 |cid-jobruntimedata-prod-su1.azure-automation.net</br>cid-agentservice-prod-1.azure-automation.net |
| 일본 동부 |jpe-jobruntimedata-prod-su1.azure-automation.net</br>jpe-agentservice-prod-1.azure-automation.net |
| 오스트레일리아 동부 |ae-jobruntimedata-prod-su1.azure-automation.net</br>ae-agentservice-prod-1.azure-automation.net |
| 오스트레일리아 동남부 |ase-jobruntimedata-prod-su1.azure-automation.net</br>ase-agentservice-prod-1.azure-automation.net |
| 영국 남부 | uks-jobruntimedata-prod-su1.azure-automation.net</br>uks-agentservice-prod-1.azure-automation.net |
| 미국 정부 버지니아 | usge-jobruntimedata-prod-su1.azure-automation.us<br>usge-agentservice-prod-1.azure-automation.us |

지역 이름 대신 지역 IP 주소 목록을 보려면 Microsoft 다운로드 센터에서 [Azure 데이터 센터 IP 주소](https://www.microsoft.com/download/details.aspx?id=41653) XML 파일을 다운로드하세요.

> [!NOTE]
> Azure 데이터 센터 IP 주소 XML 파일은 Microsoft Azure 데이터 센터에서 사용되는 IP 주소 범위를 나열합니다. 이 파일에는 계산, SQL 및 스토리지 범위가 포함되어 있습니다.
>
>업데이트된 파일이 매주 게시됩니다. 이 파일에는 현재 배포된 범위 및 IP 범위에 대해 예정된 변경 내용이 반영됩니다. 파일에 제시된 새 범위는 데이터 센터에서 적어도 한 주 동안 사용되지 않습니다.
>
> 새 XML 파일을 매주 다운로드하는 것이 좋습니다. 그런 다음 Azure에서 실행되는 서비스를 올바르게 식별하도록 사이트를 업데이트합니다. Azure ExpressRoute 사용자는 이 파일을 사용해 Azure 공간에 대한 BGP(Border Gateway Protocol) 공지 사항을 매월 첫 주에 업데이트하고 있음에 유의해야 합니다.

### <a name="update-management"></a>업데이트 관리

Hybrid Runbook Worker에 필요한 표준 주소 및 포트 외에도, 업데이트 관리를 위해 특별히 다음 주소가 필요합니다. 이러한 주소에 대한 통신은 443 포트를 통해 수행됩니다.

|Azure 공용  |Azure Government  |
|---------|---------|
|*.ods.opinsights.azure.com     |*.ods.opinsights.azure.us         |
|*.oms.opinsights.azure.com     | *.oms.opinsights.azure.us        |
|\*.blob.core.windows.net|*.blob.core.usgovcloudapi.net|

## <a name="next-steps"></a>다음 단계

* 온-프레미스 데이터 센터 또는 다른 클라우드 환경의 프로세스를 자동화하도록 Runbook을 구성하는 방법을 알아보려면 [Hybrid Runbook Worker에서 Runbook 실행](automation-hrw-run-runbooks.md)을 참조하세요.
* Hybrid Runbook Worker 문제를 해결하는 방법을 알아보려면 [Hybrid Runbook Worker 문제 해결](troubleshoot/hybrid-runbook-worker.md#general)을 참조하세요.

