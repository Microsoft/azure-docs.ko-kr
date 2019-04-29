---
title: 다른 구독으로 Azure Automation 계정 이동
description: 이 문서에서는 Automation 계정을 다른 구독으로 이동 하는 방법 설명
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/11/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 59d433bfb888eaa41cc8f66bdf3ad28c16efbe5c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61302621"
---
# <a name="move-your-azure-automation-account-to-another-subscription"></a>다른 구독으로 Azure Automation 계정 이동

Azure는 일부의 리소스를 새 리소스 그룹 또는 구독으로 이동 하는 기능을 제공 합니다. Azure portal, PowerShell, Azure CLI 또는 REST API를 통해 리소스를 이동할 수 있습니다. 프로세스에 대 한 자세한 내용은 참조 하세요 [새 리소스 그룹 또는 구독으로 리소스 이동](../../azure-resource-manager/resource-group-move-resources.md)합니다. 

Azure Automation 계정을 이동할 수 있는 리소스 중 하나 이며 이 문서에서는 Automation 계정을 다른 리소스 또는 구독으로 이동 하는 단계에 알아봅니다.

Automation 계정 이동 하는 대략적인 단계는 다음과 같습니다.

1. 솔루션을 제거 합니다.
2. 작업 영역 연결 해제 합니다.
3. Automation 계정을 이동 합니다.
4. 삭제 하 고 실행 계정을 다시 만듭니다.
5. 다시 솔루션을 사용 하도록 설정 합니다.

## <a name="remove-solutions"></a>솔루션 제거

Automation 계정에서 작업 영역 연결을 끊을 작업 영역에서 이러한 솔루션을 제거 해야 합니다.
- **변경 내용 추적 및 인벤토리**
- **업데이트 관리** 
- **시간 외 Vm 시작/중지** 

리소스 그룹에서 각 솔루션 찾기 및 선택 **삭제**합니다. 에 **리소스 삭제** 페이지에서 확인 하는 리소스를 선택 하 고 제거할 **삭제**합니다.

![Azure portal에서 솔루션을 삭제 합니다.](../media/move-account/delete-solutions.png)

사용 하 여 동일한 작업을 수행할 수 있습니다 합니다 [Remove-azurermresource](/powershell/module/azurerm.resources/remove-azurermresource) 다음 예제에서와 같이 cmdlet:

```azurepowershell-interactive
$workspaceName = <myWorkspaceName>
$resourceGroupName = <myResourceGroup>
Remove-AzureRmResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "ChangeTracking($workspaceName)" -ResourceGroupName $resourceGroupName
Remove-AzureRmResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Updates($workspaceName)" -ResourceGroupName $resourceGroupName
Remove-AzureRmResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Start-Stop-VM($workspaceName)" -ResourceGroupName $resourceGroupName
```

### <a name="additional-steps-for-startstop-vms"></a>Vm 시작/중지에 대 한 추가 단계

에 대 한 합니다 **Vm 시작/중지** 솔루션도 해야 솔루션에서 만든 경고 규칙을 제거 합니다.

Azure portal에서 리소스 그룹으로 이동 하 고 선택 **모니터링** > **경고** > **경고 규칙 관리**합니다.

![경고 페이지 보여 주는 다양 한 경고 관리 규칙](../media/move-account/alert-rules.png)

에 **규칙** 페이지에서 해당 리소스 그룹에 구성 된 경고의 목록이 표시 됩니다. 합니다 **Vm 시작/중지** 솔루션은 세 가지 경고 규칙을 만듭니다.

* AutoStop_VM_Child
* ScheduledStartStop_Parent
* SequencedStartStop_Parent

선택이 세이 가지 경고 규칙을 선택한 후 **삭제**합니다. 이 작업은 이러한 경고 규칙을 제거 합니다.

![선택한 규칙에 대 한 삭제 확인을 요청 하는 규칙 페이지](../media/move-account/delete-rules.png)

> [!NOTE]
> 모든 경고 규칙을 표시 되지 않는 경우는 **규칙** 페이지에서 변경 합니다 **상태** 표시할 **사용 하지 않도록 설정** 수 않았기 때문에 경고 합니다.

경고 규칙을 제거 하는 경우에 대해 생성 된 작업 그룹을 제거 합니다 **Vm 시작/중지** 솔루션 알림.

Azure portal에서 선택 **모니터** > **경고** > **작업 그룹 관리**합니다.

선택 **StartStop_VM_Notification** 목록에서. 작업 그룹 페이지에서 선택 **삭제**합니다.

![삭제를 작업 그룹 페이지에서 선택](../media/move-account/delete-action-group.png)

마찬가지로, 사용 하 여 PowerShell을 사용 하 여 작업 그룹을 삭제할 수 있습니다 합니다 [Remove-azurermactiongroup](/powershell/module/azurerm.insights/remove-azurermactiongroup) 다음 예제와 같이 cmdlet:

```azurepowershell-interactive
Remove-AzureRmActionGroup -ResourceGroupName <myResourceGroup> -Name StartStop_VM_Notification
```

## <a name="unlink-your-workspace"></a>작업 영역 연결 해제

Azure portal에서 선택 **Automation 계정** > **관련 된 리소스** > **연결 된 작업 영역**합니다. 선택 **작업 영역 연결 해제** Automation 계정에서 작업 영역 연결 해제 합니다.

![Automation 계정에서 작업 영역 연결 해제](../media/move-account/unlink-workspace.png)

## <a name="move-your-automation-account"></a>Automation 계정 이동

이전 항목을 제거한 후 Automation 계정 및 해당 runbook을 제거 하려면 계속 수 있습니다. Azure portal에서 Automation 계정 리소스 그룹을 찾습니다. 선택 **이동** > **다른 구독으로 이동**합니다.

![리소스 그룹 페이지에서 다른 구독으로 이동](../media/move-account/move-resources.png)

이동 하려는 리소스 그룹에서 리소스를 선택 합니다. 포함 해야 하면 **Automation 계정**, **Runbook**, 및 **Log Analytics 작업 영역** 리소스.

전환 완료 되 면 모든 것을 작동 하는 데 필요한 추가 단계가 있습니다.

## <a name="re-create-run-as-accounts"></a>다시 실행 계정 만들기

[실행 계정과](../manage-runas-account.md) Azure 리소스를 사용 하 여 인증 하도록 Azure Active Directory에 서비스 주체를 만듭니다. 구독을 변경 하면 Automation 계정이 더 이상 기존 실행 계정을 사용 합니다.

선택한 Automation 계정에서 새 구독으로 이동 **계정으로 실행** 아래에서 **계정 설정**합니다. 이제 실행 계정 표시으로 불완전 한 볼 수 있습니다.

![실행 계정과 완전 하지 않습니다.](../media/move-account/run-as-accounts.png)

각 실행 계정을 선택 합니다. 에 **속성** 페이지에서 **삭제** 실행 계정을 삭제 하도록 합니다.

> [!NOTE]
> 실행 계정을 보거나 만들 수 있는 권한이 없으면 다음과 같은 메시지가 나타납니다. `You do not have permissions to create an Azure Run As account (service principal) and grant the Contributor role to the service principal.` 실행 계정을 구성 하는 데 필요한 권한에 대 한 자세한 참조 [실행 계정을 구성 하는 데 필요한 권한을](../manage-runas-account.md#permissions)합니다.

실행 계정 삭제 된 후 선택 **Create** 아래에서 **Azure 실행 계정**합니다. 에 **추가 Azure 실행 계정** 페이지에서 **만들기** 실행 계정 및 서비스 주체를 만들려면. 사용 하 여 위의 단계를 반복 합니다 **Azure 클래식 실행 계정을**합니다.

## <a name="enable-solutions"></a>솔루션 사용

실행 계정을 다시 만든 후 이동 하기 전에 제거 하는 솔루션을 다시 활성화할 수 있습니다. 켜려면 **변경 내용 추적 및 인벤토리** 하 고 **업데이트 관리**, Automation 계정에 해당 하는 기능을 선택 합니다. 위로 이동 하 여 Log Analytics 작업 영역을 선택 하 고 선택 **사용**합니다.

![다시 이동된 Automation 계정에 솔루션을 사용 하도록 설정](../media/move-account/reenable-solutions.png)

기존 Log Analytics 작업 영역을 연결한 경우 솔루션을 사용 하 여 등록 된 컴퓨터를 볼 수 있습니다.

설정 하는 **Vm 시작/중지** , 작업이 없는 동안 솔루션을 다시 배포 해야 합니다. 아래 **관련 된 리소스**를 선택 **Vm 시작/중지** > **에 대 한 자세한 정보 및 솔루션을 사용 하도록 설정** > **만들기** 배포를 시작 합니다.

에 **솔루션 추가** 페이지, Log Analytics 작업 영역 및 Automation 계정을 선택 합니다.  

![솔루션 메뉴 추가](../media/move-account/add-solution-vm.png)

솔루션 구성에 대 한 자세한 내용은 참조 하세요. [Azure Automation의 작업이 없는 동안 Vm 시작/중지](../automation-solution-vm-management.md)합니다.

## <a name="post-move-verification"></a>사후 이동 확인

이동이 완료 되 면 다음 목록을 확인 해야 하는 작업을 확인 합니다.

|기능|테스트|연결 문제 해결|
|---|---|---|
|Runbook|성공적으로 runbook을 실행 하 고 Azure 리소스에 연결할 수 있습니다.|[Runbook 문제 해결](../troubleshoot/runbooks.md)
| 소스 제어|원본 제어 리포지토리에에서 수동 동기화를 실행할 수 있습니다.|[소스 제어 통합](../source-control-integration.md)|
|변경 내용 추적 및 인벤토리|컴퓨터에서 현재 인벤토리 데이터를 표시를 확인 합니다.|[변경 내용 추적 문제 해결](../troubleshoot/change-tracking.md)|
|업데이트 관리|컴퓨터를 표시 하 정상 상태를 확인 합니다.</br>테스트 소프트웨어 업데이트 배포를 실행 합니다.|[업데이트 관리 문제 해결](../troubleshoot/update-management.md)|

## <a name="next-steps"></a>다음 단계

Azure에서 리소스를 이동 하는 방법에 대 한 자세한 내용은 참조 하세요 [Azure에서 리소스를 이동](../../azure-resource-manager/move-support-resources.md)합니다.
