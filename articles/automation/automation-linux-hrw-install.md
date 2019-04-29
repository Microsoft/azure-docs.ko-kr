---
title: Azure Automation Linux Hybrid Runbook Worker
description: 이 문서에서는 로컬 데이터 센터 또는 클라우드 환경의 Linux 기반 컴퓨터에서 Runbook을 실행할 수 있도록 Azure Automation Hybrid Runbook Worker를 설치하는 방법에 대한 정보를 제공합니다.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 06/28/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: cc07aa9c1b2c540c33949a8c591bd98f91b04666
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60738862"
---
# <a name="deploy-a-linux-hybrid-runbook-worker"></a>Linux Hybrid Runbook Worker 배포

Azure Automation의 Hybrid Runbook Worker 기능을 사용하여 역할을 호스팅하는 컴퓨터에서 직접 그리고 환경의 리소스에 대해 Runbook을 실행하여 해당 로컬 리소스를 관리할 수 있습니다. Linux Hybrid Runbook Worker는 Runbook을 승격이 필요한 명령을 실행하도록 승격할 수 있는 특수 사용자로 실행합니다. Runbook은 Azure Automation에 저장 및 관리된 후 하나 이상의 지정된 컴퓨터에 전달됩니다.

이 문서에서는 Linux 컴퓨터에 Hybrid Runbook Worker를 설치하는 방법을 설명합니다.

## <a name="supported-linux-operating-systems"></a>지원되는 Linux 운영 체제

Hybrid Runbook Worker 기능은 다음 배포를 지원합니다.

* Amazon Linux 2012.09 ~ 2015.09(x86/x64)
* CentOS Linux 5, 6 및 7(x86/x64)
* Oracle Linux 5, 6 및 7(x86/x64)
* Red Hat Enterprise Linux Server 5, 6 및 7(x86/x64)
* Debian GNU/Linux 6, 7, 8(x86/x64)
* Ubuntu 12.04 LTS, 14.04 LTS 및 16.04 LTS(x86/x64)
* SUSE Linux Enterprise Server 11 및 12(x86/x64)

## <a name="installing-a-linux-hybrid-runbook-worker"></a>Linux Hybrid Runbook Worker 설치

Linux 컴퓨터에서 Hybrid Runbook Worker를 설치 및 구성하려는 경우 간단한 프로세스에 따라 역할을 수동으로 설치하고 구성하면 됩니다. Azure Log Analytics 작업 영역에서 **Automation Hybrid Worker** 솔루션을 활성화한 다음, 일련의 명령을 실행하여 컴퓨터를 작업자로 등록하고 그룹에 추가해야 합니다.

Linux Hybrid Runbook Worker에 대한 최소 요구 사항은 다음과 같습니다.

* 두 개의 코어
* 4GB의 RAM
* 443 포트(아웃바운드)

### <a name="package-requirements"></a>패키지 요구 사항

| **필수 패키지** | **설명** | **최소 버전**|
|--------------------- | --------------------- | -------------------|
|Glibc |GNU C 라이브러리| 2.5-12 |
|Openssl| OpenSSL 라이브러리 | 1.0(TLS 1.1 및 TLS 1.2가 지원됨)|
|Curl | cURL 웹 클라이언트 | 7.15.5|
|Python-ctypes | |
|PAM | 플러그형 인증 모듈|
| **선택적 패키지** | **설명** | **최소 버전**|
| PowerShell Core | PowerShell Runbook을 실행하려면 PowerShell을 설치해야 합니다. 설치하는 방법에 대해 알아보려면 [Linux에 PowerShell Core 설치](/powershell/scripting/setup/installing-powershell-core-on-linux)를 참조하세요.  | 6.0.0 |

### <a name="installation"></a>설치

계속 진행하기 전에 Automation 계정이 연결된 Log Analytics 작업 영역을 기록해 둡니다. 또한 Automation 계정에 대한 기본 키를 기록해 둡니다. 작업 영역과 기본 키는 모두 Azure Portal에서 찾을 수 있습니다. 자동화 계정을 선택하고 **작업 영역**을 선택하면 작업 영역 ID를, **키**를 선택하면 기본 키를 확인할 수 있습니다. Hybrid Runbook Worker에 필요한 포트 및 주소 정보는 [네트워크 구성](automation-hybrid-runbook-worker.md#network-planning)을 참조하세요.

1. 다음 방법 중 하나를 사용하여 Azure에서 **Automation Hybrid Worker** 솔루션을 활성화합니다.

   * 추가 합니다 **Automation Hybrid Worker** 의 절차를 사용 하 여 구독에 솔루션 [작업 영역에 솔루션을 기록 하는 Azure Monitor 추가](../log-analytics/log-analytics-add-solutions.md)합니다.
   * 다음 cmdlet을 실행합니다.

        ```azurepowershell-interactive
         Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName  <ResourceGroupName> -WorkspaceName <WorkspaceName> -IntelligencePackName  "AzureAutomation" -Enabled $true
        ```

1. 다음 명령을 실행하여 Linux용 Log Analytics 에이전트를 설치합니다. \<WorkspaceID\> 및 \<WorkspaceKey\>를 작업 영역에서 적절한 값으로 바꿉니다.

   [!INCLUDE [log-analytics-agent-note](../../includes/log-analytics-agent-note.md)] 

   ```bash
   wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <WorkspaceID> -s <WorkspaceKey>
   ```

1. *-w*, *-k*, *-g* 및 *-e* 매개 변수의 값을 변경하여 다음 명령을 실행합니다. *-g* 매개 변수의 경우 해당 값을 새 Linux Hybrid Runbook Worker가 조인해야 하는 Hybrid Runbook Worker 그룹의 이름으로 바꿉니다. 해당 이름이 Automation 계정에 없는 경우 해당 이름의 Hybrid Runbook Worker 그룹이 새로 만들어집니다.

   ```bash
   sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/onboarding.py --register -w <LogAnalyticsworkspaceId> -k <AutomationSharedKey> -g <hybridgroupname> -e <automationendpoint>
   ```

1. 명령이 완료되면 Azure Portal의 **Hybrid Worker 그룹** 페이지는 새 그룹 및 멤버 수를 표시합니다. 기존 그룹인 경우 멤버 수가 증가합니다. **Hybrid Worker 그룹** 페이지의 목록에서 그룹을 선택하고 **Hybrid Worker** 타일을 선택합니다. **Hybrid Worker** 페이지에서 나열된 그룹의 각 멤버를 확인합니다.

> [!NOTE]
> Azure VM에 대 한 Linux 용 Azure Monitor 가상 머신 확장을 사용 중인 경우 것이 좋습니다 설정 `autoUpgradeMinorVersion` 를 false로 자동으로 업그레이드 하는 버전 문제가 발생할 수 있습니다 Hybrid Runbook Worker입니다. 참조를 수동으로 확장을 업그레이드 하는 방법을 알아보려면 [Azure CLI 배포 ](../virtual-machines/extensions/oms-linux.md#azure-cli-deployment)합니다.

## <a name="turning-off-signature-validation"></a>서명 유효성 검사 끄기

기본적으로 Linux Hybrid Runbook Worker는 서명 유효성 검사를 요구합니다. 작업자에 대해 서명되지 않은 Runbook을 실행하는 경우 "서명 유효성 검사에 실패"를 나타내는 오류가 표시됩니다. 서명 유효성 검사를 해제하려면 다음 명령을 실행합니다. Log analytics 작업 영역 ID를 사용 하 여 두 번째 매개 변수 대체

 ```bash
 sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/require_runbook_signature.py --false <LogAnalyticsworkspaceId>
 ```

## <a name="supported-runbook-types"></a>지원되는 Runbook 유형

Linux Hybrid Runbook Worker는 Azure Automation에서 Runbook 유형의 전체 집합을 지원 하지 않습니다.

다음의 Runbook 유형은 Linux Hybrid Worker에서 작동합니다.

* Python 2
* PowerShell

  > [!NOTE]
  > PowerShell Runbook은 Linux 컴퓨터에 PowerShell Core를 설치해야 합니다. 설치하는 방법을 알아보려면 [Linux에 PowerShell Core 설치](/powershell/scripting/setup/installing-powershell-core-on-linux)를 참조하세요.

다음의 Runbook 유형은 Linux Hybrid Worker에서 작동하지 않습니다.

* PowerShell 워크플로
* 그래픽
* 그래픽 PowerShell 워크플로

## <a name="next-steps"></a>다음 단계

* 온-프레미스 데이터 센터 또는 다른 클라우드 환경의 프로세스를 자동화하도록 Runbook을 구성하는 방법을 알아보려면 [Hybrid Runbook Worker에서 Runbook 실행](automation-hrw-run-runbooks.md)을 참조하세요.
* Hybrid Runbook Worker를 제거하는 방법의 지침은 [Azure Automation Hybrid Runbook Worker 제거](automation-hybrid-runbook-worker.md#remove-a-hybrid-runbook-worker)를 참조하세요.
* Hybrid Runbook Worker 문제를 해결하는 방법을 알아보려면 [Linux Hybrid Runbook Worker 문제 해결](troubleshoot/hybrid-runbook-worker.md#linux)을 참조하세요.