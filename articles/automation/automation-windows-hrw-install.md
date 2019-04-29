---
title: Azure Automation Windows Hybrid Runbook Worker
description: 이 문서에서는 로컬 데이터 센터 또는 클라우드 환경의 Windows 기반 컴퓨터에서 Runbook을 실행할 수 있도록 해주는 Azure Automation Hybrid Runbook Worker를 설치하는 방법에 대한 정보를 제공합니다.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 04/22/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 0cc00b4f2075ba77490d310080b9968bedb8dc1f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61304959"
---
# <a name="deploy-a-windows-hybrid-runbook-worker"></a>Windows Hybrid Runbook Worker 배포

Azure Automation의 Hybrid Runbook Worker 기능을 사용하여 역할을 호스팅하는 컴퓨터에서 직접 그리고 환경의 리소스에 대해 Runbook을 실행하여 해당 로컬 리소스를 관리할 수 있습니다. Runbook은 Azure Automation에 저장 및 관리된 후 하나 이상의 지정된 컴퓨터에 전달됩니다. 이 문서에서는 Windows 컴퓨터에 Hybrid Runbook Worker를 설치하는 방법에 대해 설명합니다.

## <a name="installing-the-windows-hybrid-runbook-worker"></a>Windows Hybrid Runbook Worker 설치

Windows Hybrid Runbook Worker를 설치 및 구성하려면 2가지 방법을 사용할 수 있습니다. 권장되는 방법은 Automation Runbook을 사용하여 Windows 컴퓨터를 구성하는 프로세스를 완전히 자동화하는 것입니다. 두 번째 방법은 단계별 절차에 따라 역할을 수동으로 설치하고 구성하는 것입니다.

> [!NOTE]
> DSC(필요한 상태 구성)로 Hybrid Runbook Worker 역할을 지원하는 서버의 구성을 관리하려면 이들을 DSC 노드로 추가해야 합니다.

Windows Hybrid Runbook Worker에 대한 최소 요구 사항은 다음과 같습니다.

* Windows Server 2012 이상
* Windows PowerShell 5.1 이상([WMF 5.1 다운로드](https://www.microsoft.com/download/details.aspx?id=54616))
* .NET Framework 4.6.2 이상
* 코어 2개
* RAM 4GB
* 443 포트(아웃바운드)

Hybrid Runbook Worker에 대한 상세한 네트워킹 요구 사항은 [네트워크 구성](automation-hybrid-runbook-worker.md#network-planning)을 참조하세요.

DSC를 통한 관리를 위한 온보드에 대한 정보는 [Azure Automation DSC를 통한 관리를 위한 컴퓨터 온보드](automation-dsc-onboarding.md)를 참조하세요.
[업데이트 관리 솔루션](../operations-management-suite/oms-solution-update-management.md)을 사용하도록 설정하면 이 솔루션에 포함된 Runbook을 지원하기 위해 Azure Log Analytics 작업 영역에 연결된 모든 Windows 컴퓨터가 자동으로 Hybrid Runbook Worker로 구성됩니다. 그러나 Automation 계정에서 이미 정의한 모든 Hybrid Worker 그룹에 등록되지 않았습니다. 

솔루션과 Hybrid Runbook Worker 그룹 멤버 자격에 동일한 계정을 사용하는 한 Automation Runbook을 지원하기 위해 Automation 계정의 Hybrid Runbook Worker 그룹에 컴퓨터를 추가할 수 있습니다. 이 기능은 Hybrid Runbook Worker의 7.2.12024.0 버전에 추가되었습니다.

Runbook Worker를 성공적으로 배포한 후에는 [Hybrid Runbook Worker에서 Runbook 실행](automation-hrw-run-runbooks.md)을 검토하여 온-프레미스 데이터 센터 또는 다른 클라우드 환경의 프로세스를 자동화하도록 Runbook을 구성하는 방법을 알아봅니다.

### <a name="automated-deployment"></a>자동화된 배포

다음 단계에 따라 Windows Hybrid Worker 역할의 설치와 구성을 자동화합니다.

1. Hybrid Runbook Worker 역할을 실행하는 컴퓨터에서 직접 또는 사용자 환경의 다른 컴퓨터에서 [PowerShell 갤러리](https://www.powershellgallery.com/packages/New-OnPremiseHybridWorker)의 새로 OnPremiseHybridWorker.ps1 스크립트를 다운로드합니다. 스크립트를 작업자에 복사합니다.

   New-OnPremiseHybridWorker.ps1 스크립트에는 실행 중 다음 매개 변수가 필요합니다.

   * *AutomationAccountName*(필수): Automation 계정의 이름입니다.
   * *AAResourceGroupName*(필수): Automation 계정과 연결된 리소스 그룹의 이름입니다.
   * *OMSResourceGroupName*(선택 사항): Log Analytics 작업 영역에 대한 리소스 그룹의 이름입니다. 이 리소스 그룹을 지정하지 않으면 *AAResourceGroupName*을 사용합니다. 
   * *HybridGroupName*(필수): 이 시나리오를 지원하는 Runbook에 대한 대상으로 지정할 Hybrid Runbook Worker 그룹의 이름입니다.
   * *SubscriptionID*(필수): Automation 계정이 있는 Azure 구독 ID입니다.
   * *WorkspaceName*(선택 사항): Log Analytics 작업 영역 이름입니다. Log Analytics 작업 영역이 없는 경우 스크립트에서 하나를 만들어 구성합니다.

     > [!NOTE]
     > Azure Monitor 로그와의 통합에 대 한 지원만 Automation 지역에는 현재 **오스트레일리아 남동부**를 **미국 동부 2**합니다 **동남 아시아**, 및 **유럽 서 부**합니다. 이러한 영역 중 하나에 Automation 계정에 없는 경우 스크립트에서 Log Analytics 작업 영역을 만들지만 연결할 수 없다고 경고합니다.

2. 컴퓨터에서 관리자 모드의 **시작** 화면에서 **Windows PowerShell**을 엽니다.
3. PowerShell 명령줄 셸에서 다운로드한 스크립트가 포함된 폴더로 이동합니다. *-AutomationAccountName*, *-AAResourceGroupName*, *-OMSResourceGroupName*, *-HybridGroupName*, *-SubscriptionId* 및 *-WorkspaceName* 매개 변수의 값을 변경합니다. 그런 다음, 스크립트를 실행합니다.

     > [!NOTE]
     > 스크립트를 실행한 후에 Azure 인증을 요청하는 메시지가 나타납니다. 구독 관리자 역할의 멤버이자 구독의 공동 관리자인 계정으로 *로그인해야* 합니다.

   ```powershell-interactive
   .\New-OnPremiseHybridWorker.ps1 -AutomationAccountName <NameofAutomationAccount> -AAResourceGroupName <NameofResourceGroup>`
   -OMSResourceGroupName <NameofOResourceGroup> -HybridGroupName <NameofHRWGroup> `
   -SubscriptionId <AzureSubscriptionId> -WorkspaceName <NameOfLogAnalyticsWorkspace>
   ```

4. NuGet 설치에 동의를 요청하는 메시지가 표시되고 Azure 자격 증명으로 인증을 받도록 요구됩니다.

5. 스크립트가 완료되면 **Hybrid Worker 그룹** 페이지에 새 그룹 및 멤버 수가 표시됩니다. 기존 그룹인 경우 멤버 수가 증가합니다. **Hybrid Worker 그룹** 페이지의 목록에서 그룹을 선택하고 **Hybrid Worker** 타일을 선택합니다. **Hybrid Worker** 페이지에서 나열된 그룹의 각 멤버를 확인합니다.

### <a name="manual-deployment"></a>수동 배포

Automation 환경에 대해 처음 두 단계를 한 번 수행한 후 각 Worker 컴퓨터에 대해 나머지 단계를 반복합니다.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

#### <a name="1-create-a-log-analytics-workspace"></a>1. Log Analytics 작업 영역 만들기

Log Analytics 작업 영역이 아직 없는 경우 [작업 영역 관리](../azure-monitor/platform/manage-access.md)의 지침에 따라 작업 영역을 만듭니다. 이미 있는 경우에는 기존 작업 영역을 사용할 수 있습니다.

#### <a name="2-add-the-automation-solution-to-the-log-analytics-workspace"></a>2. Log Analytics 작업 영역에 Automation 솔루션 추가

Azure Monitor Automation 로그 솔루션 Hybrid Runbook Worker에 대 한 지원을 비롯 하 여 Azure Automation에 대 한 기능을 추가 합니다. 작업 영역에 솔루션을 추가할 때 다음 단계에서 설치할 에이전트 컴퓨터로 Worker 구성 요소를 자동으로 푸시합니다.

추가할 합니다 **Automation** Azure Monitor 다음 PowerShell을 실행 작업 영역에 솔루션을 기록 합니다.

```powershell-interactive
Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName <logAnalyticsResourceGroup> -WorkspaceName <LogAnalyticsWorkspaceName> -IntelligencePackName "AzureAutomation" -Enabled $true
```

#### <a name="3-install-the-microsoft-monitoring-agent"></a>3. Microsoft Monitoring Agent 설치

Microsoft Monitoring Agent를 Azure Monitor 로그에 컴퓨터를 연결합니다. 온-프레미스 컴퓨터에 에이전트를 설치하고 작업 영역에 연결하면 Hybrid Runbook Worker에 필요한 구성 요소가 자동으로 다운로드됩니다.

온-프레미스 컴퓨터에 에이전트를 설치 하려면의 지침을 따릅니다 [Azure Monitor 로그에 연결 하는 Windows 컴퓨터](../log-analytics/log-analytics-windows-agent.md)합니다. 이 과정을 여러 컴퓨터에 반복하여 사용자의 환경에 여러 작업자를 추가합니다.

에 나열 된 에이전트는 Azure Monitor 로그에 연결 하는 경우는 **연결 된 원본** log analytics의 탭 **설정** 페이지입니다. 에이전트에서 Automation 솔루션 다운로드를 완료했는지 확인하려면 C:\Program Files\Microsoft Monitoring Agent\Agent에 **AzureAutomationFiles** 폴더가 있는지 확인합니다. Hybrid Runbook Worker의 버전을 확인하려면 C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\으로 이동하고 \\*version* 하위 폴더를 적어두세요.

#### <a name="4-install-the-runbook-environment-and-connect-to-azure-automation"></a>4. Runbook 환경을 설치하고 Azure Automation에 연결

Azure Monitor 로그로 에이전트를 추가 하면 Automation 솔루션이 푸시 다운 합니다 **HybridRegistration** 를 포함 하는 PowerShell 모듈을 **Add-hybridrunbookworker** cmdlet. 이 cmdlet을 사용하여 컴퓨터에 Runbook 환경을 설치하고 Azure Automation에 등록합니다.

관리자 모드에서 PowerShell 세션을 열고 다음 명령을 실행하여 모듈을 가져옵니다.

```powershell-interactive
cd "C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\<version>\HybridRegistration"
Import-Module .\HybridRegistration.psd1
```

그런 다음, 아래 구문을 사용하여 **Add-HybridRunbookWorker** cmdlet을 실행합니다.

```powershell-interactive
Add-HybridRunbookWorker –GroupName <String> -EndPoint <Url> -Token <String>
```

Azure portal의 **키 관리** 페이지에서 이 cmdlet에 필요한 정보를 가져올 수 있습니다. Automation 계정의 **설정** 페이지에서 **키** 옵션을 선택하여 이 페이지를 엽니다.

![“키 관리” 페이지](media/automation-hybrid-runbook-worker/elements-panel-keys.png)

* **GroupName**은 Hybrid Runbook Worker 그룹의 이름입니다. 이 그룹이 자동화 계정에 이미 있으면 현재 컴퓨터가 추가되고, 그룹이 없으면 추가됩니다.
* **엔드포인트**는 **키 관리** 페이지의 **URL** 항목입니다.
* **토큰**은 **키 관리** 페이지의 **기본 액세스 키** 항목입니다.

설치에 대해 자세한 정보를 받으려면 **Add-HybridRunbookWorker**와 함께 **-Verbose** 스위치를 사용합니다.

#### <a name="5-install-powershell-modules"></a>5. PowerShell 모듈 설치

Runbook은 Azure Automation 환경에 설치된 모듈에 정의된 활동 및 cmdlet을 사용할 수 있습니다. 이러한 모듈은 온-프레미스 컴퓨터에 자동으로 배포되지 않으므로 수동으로 설치해야 합니다. 단, 기본적으로 설치되어 Azure Automation의 모든 Azure 서비스 및 활동에 사용되는 cmdlet에 대한 액세스를 제공하는 Azure 모듈은 예외입니다.

Hybrid Runbook Worker 기능의 주 목적은 로컬 리소스를 관리하는 것이므로 이러한 리소스를 지원하는 모듈을 설치해야 할 수 있습니다. Windows PowerShell 모듈 설치에 대한 자세한 내용은 [모듈 설치](/powershell/developer/windows-powershell)를 참조하세요. 

설치된 모듈은 Hybrid Worker가 자동으로 가져올 수 있도록 **PSModulePath** 환경 변수가 참조하는 위치에 있어야 합니다. 상세 정보는 [PSModulePath 설치 경로 수정](/powershell/developer/windows-powershell)을 참조하세요.

## <a name="next-steps"></a>다음 단계

* 온-프레미스 데이터 센터 또는 다른 클라우드 환경의 프로세스를 자동화하도록 Runbook을 구성하는 방법을 알아보려면 [Hybrid Runbook Worker에서 Runbook 실행](automation-hrw-run-runbooks.md)을 참조하세요.
* Hybrid Runbook Worker를 제거하는 방법의 지침은 [Azure Automation Hybrid Runbook Worker 제거](automation-hybrid-runbook-worker.md#remove-a-hybrid-runbook-worker)를 참조하세요.
* Hybrid Runbook Worker 문제를 해결하는 방법을 알아보려면 [Windows Hybrid Runbook Worker 문제 해결](troubleshoot/hybrid-runbook-worker.md#windows)을 참조하세요.
* 업데이트 관리 관련 문제를 해결하는 방법에 대한 추가 단계는 [업데이트 관리: 문제 해결](troubleshoot/update-management.md)을 참조하세요.
