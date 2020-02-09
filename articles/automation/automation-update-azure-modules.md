---
title: Azure Automation에서 Azure 모듈 업데이트
description: 이 문서에서는 Azure Automation에 기본적으로 제공되는 일반 Azure PowerShell 모듈을 즉시 업데이트하는 방법을 설명합니다.
services: automation
ms.subservice: process-automation
ms.date: 06/14/2019
ms.topic: conceptual
ms.openlocfilehash: 3d7eaae452f307b350c111452b819576cf7f17e5
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75420477"
---
# <a name="how-to-update-azure-powershell-modules-in-azure-automation"></a>Azure Automation에서 Azure PowerShell 모듈을 업데이트하는 방법

Automation 계정에서 Azure 모듈을 업데이트 하려면 오픈 소스로 사용할 수 있는 [azure 모듈 업데이트 runbook](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update)을 사용 해야 합니다. **Update-AutomationAzureModulesForAccount** Runbook을 사용하여 Azure 모듈을 업데이트하려면 GitHub의 [Azure 모듈 Runbook 리포지토리 업데이트](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update)에서 다운로드합니다. 그런 다음, Automation 계정으로 가져오거나 스크립트로 실행할 수 있습니다. Automation 계정에서 runbook을 가져오는 방법을 알아보려면 [Runbook 가져오기](manage-runbooks.md#import-a-runbook)를 참조 하세요.

가장 일반적인 AzureRM PowerShell 모듈은 각 Automation 계정에 기본적으로 제공 됩니다. Azure 팀이 Azure 모듈을 정기적으로 업데이트 하므로 최신 상태를 유지 하려면 [AutomationAzureModulesForAccount](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) runbook을 사용 하 여 Automation 계정의 모듈을 업데이트 합니다.

제품 그룹에 의해 정기적으로 모듈이 업데이트되므로 포함된 cmdlet이 변경될 수 있습니다. 이 작업은 매개 변수 이름을 바꾸거나 cmdlet을 완전히 중단하는 등 변경 형식에 따라 Runbook에 부정적인 영향을 줄 수 있습니다.

자동화하는 프로세스 및 Runbook에 영향을 주지 않으려면 계속하기 전에 테스트 및 유효성 검사를 수행합니다. 이 용도로 설계된 전용 Automation 계정이 없는 경우 만들어서 Runbook을 개발하는 동안 다양한 시나리오를 테스트할 수 있습니다. 이 테스트에는 PowerShell 모듈을 업데이트하는 등 반복적인 변경 내용도 포함되어야 합니다.

로컬로 스크립트를 개발하는 경우 동일한 결과를 받기 위해 테스트 시 Automation 계정에서 사용하는 것과 동일한 모듈 버전을 로컬에서 사용하는 것이 좋습니다. 결과의 유효성을 검사하고 필수 변경 내용을 적용한 후에 변경 내용을 프로덕션으로 이동할 수 있습니다.

> [!NOTE]
> 새 자동화 계정에 최신 모듈이 없을 수도 있습니다.

> [!NOTE]
> Automation에서 제공 하는 전역 모듈 모듈은 삭제할 수 없습니다.

## <a name="considerations"></a>고려 사항

다음은 이 프로세스를 사용하여 Azure 모듈을 업데이트할 때 고려해야 하는 몇 가지 사항입니다.

* 이 runbook은 기본적으로 **Azure** 및 **AzureRm** 모듈의 업데이트를 지원 합니다. 이 runbook은 **Az** modules의 업데이트도 지원 합니다. 이 runbook을 사용 하 여 `Az` 모듈을 업데이트 하는 방법에 대한 자세한 내용은 [Azure 모듈 Runbook 업데이트 추가](https://github.com/microsoft/AzureAutomation-Account-Modules-Update/blob/master/README.md) 정보를 검토 하세요. Automation 계정에서 `Az` 모듈을 사용할 때 고려해 야 하는 추가 중요 요소가 있습니다. 자세한 내용은 [Automation 계정에서 Az Modules 사용](az-modules.md)을 참조 하세요.

* 이 Runbook을 시작하기 전에 Automation 계정에 [Azure 실행 계정 자격 증명](manage-runas-account.md)이 만들어져 있는지 확인합니다.

* 이 코드를 runbook 대신 일반 PowerShell 스크립트로 사용할 수 있습니다. 먼저 [connect-azurermaccount](/powershell/module/azurerm.profile/connect-azurermaccount) 명령을 사용 하 여 Azure에 로그인 한 다음 스크립트에 `-Login $false`를 전달 하면 됩니다.

* 소버린 클라우드에서 이 Runbook을 사용하려면 `AzureRmEnvironment` 매개 변수를 사용하여 올바른 환경을 Runbook에 전달합니다.  허용되는 값은 **AzureCloud**, **AzureChinaCloud**, **AzureGermanCloud** 및 **AzureUSGovernment**입니다. `Get-AzureRmEnvironment | select Name`을 사용하여 이러한 값을 가져올 수 있습니다. 이 매개 변수에 값을 전달하지 않으면 Runbook이 기본적으로 **AzureCloud** Azure 퍼블릭 클라우드로 설정됩니다.

* PowerShell 갤러리에서 사용할 수 있는 최신 버전 대신 특정 Azure PowerShell 모듈 버전을 사용하려면 이러한 버전을 **Update-AutomationAzureModulesForAccount** Runbook의 선택적 `ModuleVersionOverrides` 매개 변수에 전달합니다. 예제는 [Update-AutomationAzureModulesForAccount.ps1](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update/blob/master/Update-AutomationAzureModulesForAccount.ps1
) Runbook을 참조하세요. `ModuleVersionOverrides` 매개 변수에 언급되지 않은 Azure PowerShell 모듈은 PowerShell 갤러리의 최신 모듈 버전으로 업데이트됩니다. `ModuleVersionOverrides` 매개 변수에 아무 것도 전달하지 않으면 모든 모듈이 PowerShell 갤러리의 최신 모듈 버전으로 업데이트됩니다. 이 동작은 **Azure 모듈 업데이트** 단추와 동일합니다.

## <a name="next-steps"></a>다음 단계

자세한 내용은 오픈 소스 [Azure 모듈 Runbook 업데이트](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update)를 참조하세요.
