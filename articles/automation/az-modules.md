---
title: Azure Automation에서 Az 모듈 사용
description: 이 문서에서는 Azure Automation에서 Az 모듈을 사용하기 위한 정보를 제공합니다.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: georgewallace
ms.author: gwallace
ms.date: 02/08/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: a076c924d57aadfae477a5df0d128aad8e67af60
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61305420"
---
# <a name="az-module-support-in-azure-automation"></a>Azure Automation의 Az 모듈 지원

Azure Automation은 Runbook에서 [Azure Powershell Az 모듈](/powershell/azure/new-azureps-module-az?view=azps-1.1.0)을 사용하기 위한 기능을 지원합니다. Az 모듈을 새 또는 기존 Automation 계정으로는 자동으로 가져올 수 없습니다. 이 문서에서는 Azure Automation에서 Az 모듈을 사용하는 방법을 설명합니다.

## <a name="considerations"></a>고려 사항

Azure Automation에서 Az 모듈을 사용할 경우 여러 가지 사항을 고려해야 합니다. Runbook 및 모듈은 Automation 계정의 상위 솔루션에서 사용할 수 있습니다. Runbook을 편집하거나 모듈을 업그레이드하면 Runbook에 문제가 발생할 수 있습니다. 새 `Az` 모듈을 가져오기 전에 모든 Runbook 및 솔루션을 별도 Automation 계정에서 신중히 테스트해야 합니다. 모든 모듈 수정 내용은 업데이트 관리 및 작업 시간 외 VM 시작/중지와 같은 상위 솔루션에 부정적인 영향을 미칠 수 있습니다. 솔루션을 포함하는 Automation 계정의 모듈 및 Runbook은 변경하지 않는 것이 좋습니다. 이 동작은 Az 모듈과 관련이 없습니다. Automation 계정을 변경할 때는 이 동작을 고려해야 합니다.

Automation 계정으로 `Az` 모듈을 가져와도 Runbook이 사용하는 PowerShell 세션으로 모듈을 자동으로 가져오지 않습니다. 다음과 같은 상황에서 모듈을 PowerShell 세션으로 가져올 수 있습니다.

* 모듈의 cmdlet이 Runbook에서 호출될 때
* Runbook이 `Import-Module` cmdlet을 사용하여 명시적으로 가져올 때
* 모듈을 사용하는 다른 모듈을 PowerShell 세션으로 가져올 때

> [!IMPORTANT]
> Automation 계정의 Runbook이 Runbook에서 사용하는 PowerShell 세션으로 `Az` 또는 `AzureRM` 모듈 중 하나만 가져오도록 하는 것이 중요합니다. Runbook에서 `AzureRM`보다 먼저 `Az`를 가져오는 경우 Runbook이 완료되지만 [get_SerializationSettings 메서드를 참조하는 오류](troubleshoot/runbooks.md#get-serializationsettings)가 작업 스트림에 표시되고 cmdlet이 제대로 실행되지 않았을 수 있습니다. `AzureRM`을 가져온 후 `Az`를 가져오면 Runbook은 완료되지만, `Az` 및 `AzureRM` 중 하나만 동일한 세션으로 가져오거나 동일한 Runbook에서 사용할 수 있다는 오류가 작업 스트림에 표시됩니다.

## <a name="migrating-to-az-modules"></a>Az 모듈로 마이그레이션

테스트 Automation 계정에서 AzureRM 모듈 대신 Az 모듈을 사용하여 마이그레이션을 테스트하는 것이 좋습니다. 일단 Automation 계정을 만든 후에는 다음 단계에 따라 마이그레이션을 원활하게 진행할 수 있습니다.

### <a name="stop-and-unschedule-all-runbook-that-uses-azurerm-modules"></a>AzureRM 모듈을 사용하는 모든 Runbook 중지 및 예약 취소

`AzureRM` cmdlet을 사용하는 기존 Runbook을 실행하지 않으려면 `AzureRM` 모듈을 사용하는 모든 Runbook을 중지한 후 예약 취소합니다. 다음 예제를 실행하여 존재하는 일정과 제거해야 하는 일정을 확인할 수 있습니다.

  ```powershell-interactive
  Get-AzureRmAutomationSchedule -AutomationAccountName "<AutomationAccountName>" -ResourceGroupName "<ResourceGroupName>" | Remove-AzureRmAutomationSchedule -WhatIf
  ```

각 일정을 별도로 검토하고 필요에 따라 향후에 Runbook을 다시 예약할 수 있도록 하는 것이 중요합니다.

### <a name="import-the-az-modules"></a>Az 모듈 가져오기

Runbook에 필요한 Az 모듈만 가져옵니다. 롤업 `Az` 모듈의 경우 가져올 모든 `Az.*` 모듈을 포함하므로 가져오지 마세요. 이 지침은 모든 모듈에 대해 동일합니다.

[Az.Accounts](https://www.powershellgallery.com/packages/Az.Accounts/1.1.0) 모듈은 다른 `Az.*` 모듈에 대한 종속성입니다. 이러한 이유로 인해, 다른 모듈을 가져오기 전에 먼저 이 모듈을 Automation 계정으로 가져와야 합니다.

Automation 계정의 **공유 리소스** 아래에서 **모듈**을 선택합니다. **갤러리 찾아보기**를 클릭하여 **갤러리 찾아보기** 페이지를 엽니다.  검색 표시줄에서 모듈 이름(예: `Az.Accounts`)을 입력합니다. PowerShell 모듈 페이지에서 **가져오기**를 클릭하여 Automation 계정으로 모듈을 가져옵니다.

![Automation 계정에서 모듈 가져오기](media/az-modules/import-module.png)

이 가져오기 프로세스는 모듈을 검색하여 [PowerShell 갤러리](https://www.powershellgallery.com)를 통해 수행할 수도 있습니다. 모듈을 찾은 후에는 선택하고 **Azure Automation** 탭 아래에서 **Azure Automation에 배포**를 클릭합니다.

![갤러리에서 직접 모듈 가져오기](media/az-modules/import-gallery.png)

## <a name="test-your-runbooks"></a>Runbook 테스트

`Az` 모듈을 Automation 계정에서 가져오면 대신 해당 Az 모듈을 사용하도록 Runbook을 편집할 수 있습니다. 대부분의 cmdlet은 `AzureRM`이 `Az`로 변경되었다는 점을 제외하고 동일한 이름을 갖습니다. 이 프로세스를 따르지 않는 모듈 목록에 대해서는 [예외 목록](/powershell/azure/migrate-from-azurerm-to-az?view=azps-1.1.0#change-module-imports-and-cmdlet-names)을 참조하세요.

새로운 cmdlet을 사용하도록 Runbook을 수정하기 전에 Runbook을 테스트하는 한 가지 방법은 Runbook 맨 처음에 `Enable-AzureRMAlias -Scope Process`를 사용하는 것입니다. 이 항목을 Runbook에 추가하기 전에는 Runbook을 변경 없이 실행할 수 있습니다.

## <a name="after-migration-details"></a>마이그레이션 후 세부 정보

마이그레이션이 완료된 후에는 더 이상 계정에서 `AzureRM` 모듈을 사용하여 Runbook을 시작하지 않도록 합니다. 또한 이 계정에서 `AzureRM` 모듈을 가져오거나 업데이트하지 않는 것이 좋습니다. 이때부터 이 계정이 `Az`로 마이그레이션되었다고 간주하고 `Az` 모듈만 사용하는 것이 좋습니다. 새 Automation 계정을 만들 때 기존 `AzureRM` 모듈은 계속 설치되고, 자습서 Runbook은 `AzureRM` cmdlet으로 계속 작성됩니다. 이러한 runbook은 실행 하지 마십시오.

## <a name="next-steps"></a>다음 단계

Az 모듈을 사용하는 방법에 대한 자세한 내용은 [Az 모듈 시작](/powershell/azure/get-started-azureps?view=azps-1.1.0)을 참조하세요.
