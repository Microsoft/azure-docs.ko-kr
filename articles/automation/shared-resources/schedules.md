---
title: Azure Automation의 일정
description: Automation 일정은 Azure Automation에서 Runbook이 자동으로 시작되도록 예약하는 데 사용됩니다. 특정 시간 또는 되풀이 일정에 따라 Runbook을 자동으로 시작할 수 있도록 일정을 만들고 관리하는 방법을 설명합니다.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: georgewallace
ms.author: gwallace
ms.date: 04/04/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 483f9092d29fc40937ed9d54510269af2af30872
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62128807"
---
# <a name="scheduling-a-runbook-in-azure-automation"></a>Azure Automation에서 Runbook 예약

Azure Automation에서 Runbook이 지정된 시간에 시작되도록 예약하려면 해당 Runbook을 하나 이상의 일정에 연결합니다. Azure Portal에서 일정이 Runbook에 대해 한 번 실행되거나 매시간 또는 매일 반복되도록 구성할 수 있습니다. 매주, 매월, 주의 특정 요일이나 매월 특정 요일에 또는 특정 날짜에 실행되도록 예약할 수도 있습니다. Runbook을 여러 일정에 연결할 수 있으며, 하나의 일정에 여러 Runbook을 연결할 수 있습니다.

> [!NOTE]
> 일정은 현재 Azure Automation DSC 구성을 지원하지 않습니다.

## <a name="powershell-cmdlets"></a>PowerShell Cmdlet

다음 표에 나와있는 cmdlet은 Azure Automation에서 PowerShell 사용 하 여 일정 만들기 및 관리에 사용 됩니다. 이러한 cmdlet은 [Azure PowerShell 모듈](/powershell/azure/overview)의 일부로 제공됩니다.

| Cmdlet | 설명 |
|:--- |:--- |
| [Get-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/get-azurermautomationschedule) |일정을 검색합니다. |
| [New-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/new-azurermautomationschedule) |새 일정을 만듭니다. |
| [Remove-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/remove-azurermautomationschedule) |일정을 제거합니다. |
| [Set-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/set-azurermautomationschedule) |기존 일정에 대한 속성을 설정합니다. |
| [Get-AzureRmAutomationScheduledRunbook](/powershell/module/azurerm.automation/get-azurermautomationscheduledrunbook) |예약된 Runbook을 검색합니다. |
| [Register-AzureRmAutomationScheduledRunbook](/powershell/module/azurerm.automation/register-azurermautomationscheduledrunbook) |Runbook을 일정에 연결합니다. |
| [Unregister-AzureRmAutomationScheduledRunbook](/powershell/module/azurerm.automation/unregister-azurermautomationscheduledrunbook) |일정에서 Runbook을 분리합니다. |

## <a name="creating-a-schedule"></a>일정 만들기

Azure portal 또는 PowerShell을 사용 하 여 runbook에 대 한 새 일정을 만들 수 있습니다.

> [!NOTE]
> Azure Automation은 예약된 새 작업이 실행될 때 Automation 계정의 최신 모듈을 사용합니다.  자동화하는 프로세스 및 Runbook에 영향을 주지 않으려면 먼저 테스트 전용 Automation 계정으로 일정을 연결한 모든 Runbook을 테스트해야 합니다.  이렇게 하면 예약된 Runbook이 제대로 계속 작동하는지 확인하고, 그렇지 않은 경우 프로덕션 환경에 업데이트된 Runbook 버전을 마이그레이션하기 전에 추가적으로 문제를 해결하고 필요한 모든 변경 내용을 적용할 수 있습니다.
> **모듈**에서 [Azure 모듈 업데이트](../automation-update-azure-modules.md) 옵션을 선택하여 수동으로 업데이트하지 않는 한 Automation 계정에서 새 버전의 모듈을 자동으로 가져오지 않습니다.

### <a name="to-create-a-new-schedule-in-the-azure-portal"></a>Azure 포털에서 새 일정을 만들려면

1. Azure Portal의 Automation 계정에서 왼쪽의 **공유 리소스** 섹션 아래에 있는 **일정**을 선택합니다.
2. 페이지의 위쪽에서 **일정 추가**를 클릭합니다.
3. **새 일정** 창에서 **이름**을 입력하고, 선택적으로 새 일정에 대한 **설명**을 입력합니다.
4. **한 번** 또는 **되풀이**를 선택하여 일정을 한 번 실행할지 또는 되풀이 일정에 따라 실행할지를 선택합니다. **한 번**을 선택하는 경우 **시작 시간**을 지정한 다음, **만들기**를 클릭합니다. **되풀이**를 선택하는 경우 **시작 시간**을 지정하고 **되풀이 간격**에서는 Runbook 반복 빈도를 **시간**, **일**, **주** 또는 **월** 단위로 지정합니다.
    1. 선택 하는 경우 **주**, 선택할 요일 목록이 제공 됩니다. 요일을 원하는 수만큼 선택합니다. 첫 번째 일정은 시작 시간 이후에 선택한 첫 번째 요일에 실행됩니다. 예를 들어 주말 일정을 선택 하려면 선택할 **토요일** 하 고 **일요일**합니다.

       ![설정 주말 되풀이 일정](../media/schedules/week-end-weekly-recurrence.png)

    2. 선택 하는 경우 **월**, 다양 한 옵션을 표시 합니다. 에 대 한 합니다 **월별 발생 빈도** 옵션을 선택 하거나 **월 일** 또는 **요일**합니다. 선택 하면 **월 일**, 일정은 원하는 만큼 많은 일을 선택할 수 있도록 표시 됩니다. 현재 달에 발생 하지 않습니다는 31 일 같은 날짜를 선택 하면 일정 실행 되지 않습니다. 일정이 해당 월의 말일에 실행되도록 하려면 **매월 말일 실행** 아래에서 **예**를 선택합니다. **평일**을 선택하는 경우에는 **되풀이 간격** 옵션이 표시됩니다. **첫 번째**, **두 번째**, **세 번째**, **네 번째** 또는 **마지막** 중 하나를 선택하고, 마지막으로 반복할 요일을 선택합니다.

       ![해당 월의 첫 번째, 15 번째 및 마지막 일에 월별 일정](../media/schedules/monthly-first-fifteenth-last.png)

5. 선택을 완료한 후 **만들기**를 클릭합니다.

### <a name="to-create-a-new-schedule-with-powershell"></a>PowerShell을 사용 하 여 새 일정을 만들려면

[New-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/new-azurermautomationschedule) cmdlet을 사용하여 일정을 만듭니다. 일정의 시작 시간 및 실행 빈도를 지정합니다. 다음 예제에서는 여러 다른 일정 시나리오를 만드는 방법을 보여 줍니다.

#### <a name="create-a-one-time-schedule"></a>만들 시간 예약

다음 명령 예제에서는 만들 시간 일정 합니다.

```azurepowershell-interactive
$TimeZone = ([System.TimeZoneInfo]::Local).Id
New-AzureRmAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Schedule01" -StartTime "23:00" -OneTime -ResourceGroupName "ResourceGroup01" -TimeZone $TimeZone
```

#### <a name="create-a-recurring-schedule"></a>되풀이 일정을 만들려면

다음 명령 예제에는 매일 1 년 동안 오후 1 시에 실행 되는 되풀이 일정을 만드는 방법을 보여 줍니다.

```azurepowershell-interactive
$StartTime = Get-Date "13:00:00"
$EndTime = $StartTime.AddYears(1)
New-AzureRmAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Schedule02" -StartTime $StartTime -ExpiryTime $EndTime -DayInterval 1 -ResourceGroupName "ResourceGroup01"
```

#### <a name="create-a-weekly-recurring-schedule"></a>매주 되풀이 일정을 만들려면

다음 명령 예제에는 주중에 실행 되는 주별 일정을 만드는 방법을 보여 줍니다.

```azurepowershell-interactive
$StartTime = (Get-Date "13:00:00").AddDays(1)
[System.DayOfWeek[]]$WeekDays = @([System.DayOfWeek]::Monday..[System.DayOfWeek]::Friday)
New-AzureRmAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Schedule03" -StartTime $StartTime -WeekInterval 1 -DaysOfWeek $WeekDays -ResourceGroupName "ResourceGroup01"
```

#### <a name="create-a-weekly-recurring-schedule-for-weekends"></a>주말 매주 되풀이 일정 만들기

다음 명령 예제에는 주말만 실행 되는 주별 일정을 만드는 방법을 보여 줍니다.

```azurepowershell-interactive
$StartTime = (Get-Date "18:00:00").AddDays(1)
[System.DayOfWeek[]]$WeekendDays = @([System.DayOfWeek]::Saturday,[System.DayOfWeek]::Sunday)
New-AzureRmAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Weekends 6PM" -StartTime $StartTime -WeekInterval 1 -DaysOfWeek $WeekendDays -ResourceGroupName "ResourceGroup01"
```

#### <a name="create-a-recurring-schedule-for-first-15th-and-last-days-of-the-month"></a>첫 번째 되풀이 일정을 만들고 해당 월의 마지막 15 한 일

다음 명령 예제에는 월간 일과 15 일, 지난 날에 실행 되는 되풀이 일정을 만드는 방법을 보여 줍니다.

```azurepowershell-interactive
$StartTime = (Get-Date "18:00:00").AddDays(1)
New-AzureRmAutomationSchedule -AutomationAccountName "TestAzureAuto" -Name "1st, 15th and Last" -StartTime $StartTime -DaysOfMonth @("One", "Fifteenth", "Last") -ResourceGroupName "TestAzureAuto" -MonthInterval 1
```

## <a name="linking-a-schedule-to-a-runbook"></a>Runbook에 일정 연결

Runbook을 여러 일정에 연결할 수 있으며, 하나의 일정에 여러 Runbook을 연결할 수 있습니다. Runbook에 매개 변수가 있는 경우 값을 제공할 수 있습니다. 필수 매개 변수의 값은 반드시 제공해야 하며, 선택적 매개 변수의 값은 필요에 따라 제공할 수 있습니다. 이러한 값은 Runbook이 이 일정에 따라 시작할 때마다 사용됩니다. 동일한 Runbook을 다른 일정에 연결하고 다른 매개 변수 값을 지정할 수 있습니다.

### <a name="to-link-a-schedule-to-a-runbook-with-the-azure-portal"></a>Azure 포털을 사용하여 Runbook에 일정을 연결하려면

1. Azure Portal의 Automation 계정에서 왼쪽의 **프로세스 자동화** 섹션 아래에 있는 **Runbook**을 선택합니다.
2. 예약할 Runbook의 이름을 클릭합니다.
3. 현재 runbook 일정에 연결 되지 않습니다, 경우 새 일정 만들거나 기존 일정에 연결 하는 옵션을 제공 하 고 있습니다.
4. Runbook에 매개 변수가 하는 경우에 옵션을 선택할 수 있습니다 **실행된 설정 (기본값: Azure) 수정** 하며 **매개 변수** 정보를 입력할 수 있는 창이 표시 됩니다.

### <a name="to-link-a-schedule-to-a-runbook-with-powershell"></a>PowerShell 사용 하 여 runbook에 일정을 연결 하려면

일정과 마찬가지로 [Register-AzureRmAutomationScheduledRunbook](/powershell/module/azurerm.automation/register-azurermautomationscheduledrunbook) cmdlet을 사용합니다. Parameters 매개 변수를 사용하여 Runbook의 매개 변수 값을 지정할 수 있습니다. 매개 변수 값 지정에 대한 자세한 내용은 [Azure Automation에서 Runbook 시작](../automation-starting-a-runbook.md)을 참조하세요.
다음 샘플 명령에서는 매개 변수를 포함하는 Azure Resource Manager cmdlet을 사용하여 runbook에 일정을 연결하는 방법을 보여 줍니다.

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$runbookName = "Test-Runbook"
$scheduleName = "Sample-DailySchedule"
$params = @{"FirstName"="Joe";"LastName"="Smith";"RepeatCount"=2;"Show"=$true}
Register-AzureRmAutomationScheduledRunbook –AutomationAccountName $automationAccountName `
–Name $runbookName –ScheduleName $scheduleName –Parameters $params `
-ResourceGroupName "ResourceGroup01"
```

## <a name="scheduling-runbooks-more-frequently"></a>Runbook을 보다 빈번하게 예약하기

Azure Automation에서 일정에 구성할 수 있는 가장 빈번한 간격은 1시간입니다. 일정을 이보다 더 자주 실행해야 하는 경우 두 가지 옵션이 있습니다.

* Runbook에 대한 [웹후크](../automation-webhooks.md)를 만들고 [Azure Scheduler](../../scheduler/scheduler-get-started-portal.md)를 사용하여 웹후크를 호출합니다. Azure Scheduler는 일정을 정의할 때 보다 세분화된 세분성을 제공합니다.

* 한 시간에 한 번씩 실행되는 일정을 4개 만들어서 서로 15분 이내에 시작되도록 설정합니다. 이 시나리오를 적용하면 Runbook을 다른 일정으로 15분마다 실행할 수 있습니다.

## <a name="disabling-a-schedule"></a>일정 해제

일정을 사용하지 않도록 설정하면 연결된 모든 Runbook이 해당 일정에서 더 이상 실행되지 않습니다. 일정을 수동으로 해제하거나, 일정을 만들 때 빈도를 사용하여 일정에 대한 만료 시간을 설정할 수 있습니다. 만료 시간에 도달 하면, 일정을 사용 합니다.

### <a name="to-disable-a-schedule-from-the-azure-portal"></a>Azure 포털에서 일정을 비활성화하려면

1. Azure Portal의 Automation 계정에서 왼쪽의 **공유 리소스** 섹션 아래에 있는 **일정**을 선택합니다.
2. 일정 이름을 클릭하여 해당 세부 정보 창을 엽니다.
3. **사용**을 **아니오**로 변경합니다.

> [!NOTE]
> 시작 시간이 과거에는 일정을 사용 하지 않도록 설정 하려는 경우 나중에 저장 하기 전에 한 번에 시작 날짜를 변경 해야 합니다.

### <a name="to-disable-a-schedule-with-powershell"></a>PowerShell 사용 하 여 일정을 사용 하지 않도록 설정

[Set-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/set-azurermautomationschedule) cmdlet을 사용하여 기존 일정의 속성을 변경할 수 있습니다. 일정을 해제하려면 **IsEnabled** 매개 변수에 대해 **false**를 지정합니다.

다음 샘플 명령에서는 Azure Resource Manager cmdlet을 사용하여 runbook에 일정을 비활성화하는 방법을 보여 줍니다.

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$scheduleName = "Sample-MonthlyDaysOfMonthSchedule"
Set-AzureRmAutomationSchedule –AutomationAccountName $automationAccountName `
–Name $scheduleName –IsEnabled $false -ResourceGroupName "ResourceGroup01"
```

## <a name="next-steps"></a>다음 단계

* Azure Automation에서 Runbook을 시작하려면 [Azure Automation에서 Runbook 시작](../automation-starting-a-runbook.md)

