---
title: Azure Automation에서 Azure 모듈 업데이트
description: 이 문서에서는 Azure Automation에 기본적으로 제공되는 일반 Azure PowerShell 모듈을 즉시 업데이트하는 방법을 설명합니다.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/11/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 03174e6336589f8aa49a7fc7197da1301ff54400
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61304305"
---
# <a name="how-to-update-azure-powershell-modules-in-azure-automation"></a>Azure Automation에서 Azure PowerShell 모듈을 업데이트하는 방법

Automation 계정의 Azure 모듈을 업데이트하려면 현재 오픈 소스인 [Azure 모듈 Runbook 업데이트](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update)를 사용하는 것이 좋습니다. 또한 포털의 **Azure 모듈 업데이트** 단추를 사용하여 Azure 모듈을 업데이트할 수 있습니다. 오픈 소스 Runbook을 사용하는 방법에 대한 자세한 내용은 [오픈 소스 Runbook을 사용하여 Azure 모듈 업데이트](#open-source)를 참조하세요.

기본적으로 각 Automation 계정에 가장 일반적인 Azure PowerShell 모듈이 제공됩니다. Azure 팀에서는 Azure 모듈을 정기적으로 업데이트합니다. 포털에서 새 버전을 사용할 수 있으면 Automation 계정에서 모듈을 업데이트하는 방법이 제공됩니다.

제품 그룹에 의해 정기적으로 모듈이 업데이트되므로 포함된 cmdlet이 변경될 수 있습니다. 이 작업은 매개 변수 이름을 바꾸거나 cmdlet을 완전히 중단하는 등 변경 형식에 따라 Runbook에 부정적인 영향을 줄 수 있습니다.

자동화하는 프로세스 및 Runbook에 영향을 주지 않으려면 계속하기 전에 테스트 및 유효성 검사를 수행합니다. 이 용도로 설계된 전용 Automation 계정이 없는 경우 만들어서 Runbook을 개발하는 동안 다양한 시나리오를 테스트할 수 있습니다. 이 테스트에는 PowerShell 모듈을 업데이트하는 등 반복적인 변경 내용도 포함되어야 합니다. 

로컬로 스크립트를 개발하는 경우 동일한 결과를 받기 위해 테스트 시 Automation 계정에서 사용하는 것과 동일한 모듈 버전을 로컬에서 사용하는 것이 좋습니다. 결과의 유효성을 검사하고 필수 변경 내용을 적용한 후에 변경 내용을 프로덕션으로 이동할 수 있습니다.

> [!NOTE]
> 새 자동화 계정에 최신 모듈이 없을 수도 있습니다.

## <a name="open-source"></a>오픈 소스 Runbook을 사용하여 Azure 모듈 업데이트

**Update-AutomationAzureModulesForAccount** Runbook을 사용하여 Azure 모듈을 업데이트하려면 GitHub의 [Azure 모듈 Runbook 리포지토리 업데이트](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update)에서 다운로드합니다. 그런 다음, Automation 계정으로 가져오거나 스크립트로 실행할 수 있습니다. 이 작업을 수행하는 방법에 대한 지침은 [Azure 모듈 Runbook 리포지토리 업데이트](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update)에서 확인할 수 있습니다.

### <a name="considerations"></a>고려 사항

다음은 이 프로세스를 사용하여 Azure 모듈을 업데이트할 때 고려해야 하는 몇 가지 사항입니다.

* 원래 이름이 `Update-AutomationAzureModulesForAccount`인 이 Runbook을 가져오면 이 이름을 가진 내부 Runbook을 재정의합니다. 따라서 **Azure 모듈 업데이트** 단추를 누르거나 Azure Resource Manager API를 통해 직접 이 Runbook을 이 Automation 계정에 대해 호출할 경우 가져온 Runbook이 실행됩니다.

* 이 runbook만 업데이트를 지원 합니다 **Azure** 하 고 **AzureRm** 현재 모듈입니다. [Azure PowerShell Az 모듈](/powershell/azure/new-azureps-module-az) Automation 계정에서 지원 되지만이 runbook을 사용 하 여 업데이트할 수 없습니다.

* Az 모듈이 포함된 Automation 계정에서 이 Runbook을 시작하지 마세요.

* 이 Runbook을 시작하기 전에 Automation 계정에 [Azure 실행 계정 자격 증명](manage-runas-account.md)이 만들어져 있는지 확인합니다.

* 이 코드를 Runbook이 아닌 일반 PowerShell 스크립트로 사용할 수 있습니다. 먼저 [Connect-AzureRmAccount](/powershell/module/azurerm.profile/connect-azurermaccount) 명령을 사용하여 Azure에 로그인한 다음, `-Login $false`를 스크립트에 전달하면 됩니다.

* 소버린 클라우드에서 이 Runbook을 사용하려면 `AzureRmEnvironment` 매개 변수를 사용하여 올바른 환경을 Runbook에 전달합니다.  허용되는 값은 **AzureCloud**, **AzureChinaCloud**, **AzureGermanCloud** 및 **AzureUSGovernment**입니다. `Get-AzureRmEnvironment | select Name`을 사용하여 이러한 값을 가져올 수 있습니다. 이 매개 변수에 값을 전달하지 않으면 Runbook이 기본적으로 **AzureCloud** Azure 공용 클라우드로 설정됩니다.

* PowerShell 갤러리에서 사용할 수 있는 최신 버전 대신 특정 Azure PowerShell 모듈 버전을 사용하려면 이러한 버전을 **Update-AutomationAzureModulesForAccount** Runbook의 선택적 `ModuleVersionOverrides` 매개 변수에 전달합니다. 예제는 [Update-AutomationAzureModulesForAccount.ps1](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update/blob/master/Update-AutomationAzureModulesForAccount.ps1
) Runbook을 참조하세요. `ModuleVersionOverrides` 매개 변수에 언급되지 않은 Azure PowerShell 모듈은 PowerShell 갤러리의 최신 모듈 버전으로 업데이트됩니다. `ModuleVersionOverrides` 매개 변수에 아무 것도 전달하지 않으면 모든 모듈이 PowerShell 갤러리의 최신 모듈 버전으로 업데이트됩니다. 이 동작은 **Azure 모듈 업데이트** 단추와 동일합니다.

## <a name="update-azure-modules-in-the-azure-portal"></a>Azure Portal에서 Azure 모듈 업데이트

1. Automation 계정의 모듈 계정 페이지에는 **Azure 모듈 업데이트**라는 옵션이 있습니다. 이 옵션은 항상 사용됩니다.<br><br> ![모듈 페이지의 Azure 모듈 업데이트 옵션](media/automation-update-azure-modules/automation-update-azure-modules-option.png)

   > [!NOTE]
   > Azure 모듈을 업데이트하기 전에 테스트 Automation 계정에서 업데이트하여 Azure 모듈을 업데이트하기 전에 기존 스크립트가 예상대로 작동하는지 확인하는 것이 좋습니다.
   >
   > **Azure 모듈 업데이트** 단추는 공용 클라우드에서만 사용할 수 있으며, [소버린 지역](https://azure.microsoft.com/global-infrastructure/)에서는 사용할 수 없습니다. Azure 모듈을 업데이트하려면 **Update-AutomationAzureModulesForAccount** Runbook을 사용하세요. [Azure 모듈 Runbook 리포지토리 업데이트](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update)에서 다운로드할 수 있습니다. 오픈 소스 Runbook을 사용하는 방법에 대한 자세한 내용은 [오픈 소스 Runbook을 사용하여 Azure 모듈 업데이트](#open-source)를 참조하세요.

2. **Azure 모듈 업데이트**를 클릭하면 계속 진행할 것인지 묻는 확인 알림이 나타납니다.<br><br> ![Azure 모듈 업데이트 알림](media/automation-update-azure-modules/automation-update-azure-modules-popup.png)

3. **예**를 클릭하면 모듈 업데이트 프로세스가 시작됩니다. 업데이트 프로세스에서 다음 모듈을 업데이트하는 데 15-20분 정도 걸립니다.

   * Azure
   * Azure.Storage
   * AzureRm.Automation
   * AzureRm.Compute
   * AzureRm.Profile
   * AzureRm.Resources
   * AzureRm.Sql
   * AzureRm.Storage

     모듈이 이미 최신 상태이면 프로세스가 몇 초 이내에 완료됩니다. 업데이트 프로세스가 완료되면 알림이 표시됩니다.<br><br> ![Azure 모듈 업데이트 상태 업데이트](media/automation-update-azure-modules/automation-update-azure-modules-updatestatus.png)

     .NET Core AzureRm 모듈(AzureRm.*.Core)은 Azure Automation에서 지원되지 않으므로 가져올 수 없습니다.

> [!NOTE]
> Azure Automation은 예약된 새 작업이 실행될 때 Automation 계정의 최신 모듈을 사용합니다.  

Runbook에서 이러한 Azure PowerShell 모듈의 cmdlet을 사용하는 경우 최신 모듈을 사용하도록 매달 이 업데이트 프로세스를 수행하는 것이 좋습니다. Azure Automation은 `AzureRunAsConnection` 연결을 사용하여 모듈을 업데이트할 때 인증합니다. 서비스 주체가 만료되거나 구독 수준에서 더 이상 존재하지 않는 경우 모듈 업데이트에 실패합니다.

## <a name="known-issues"></a>알려진 문제

0부터 시작 하는 숫자 이름의 리소스 그룹에 있는 Automation 계정에는 AzureRM 모듈을 업데이트 하는 알려진된 문제가 있습니다. Automation 계정에서 Azure 모듈 업데이트에 영숫자 이름을 가진 리소스 그룹에 이어야 합니다. 0부터 시작 하는 숫자 이름의 리소스 그룹에 AzureRM 모듈을 업데이트할 수 없는 합니다.

## <a name="next-steps"></a>다음 단계

자세한 내용은 오픈 소스 [Azure 모듈 Runbook 업데이트](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update)를 참조하세요.
