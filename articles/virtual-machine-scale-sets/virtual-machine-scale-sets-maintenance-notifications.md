---
title: Azure에서 가상 머신 확장 집합에 대한 유지 관리 알림 | Microsoft Docs
description: Azure에서 가상 머신 확장 집합에 대한 유지 관리 알림을 보고, 셀프 서비스 유지 관리를 시작합니다.
services: virtual-machine-scale-sets
documentationcenter: ''
author: shants123
editor: ''
tags: azure-service-management,azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/09/2018
ms.author: shants
ms.openlocfilehash: 31d4829c6adaf4bd5392ef393dcaefbeb7dc6255
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60618458"
---
# <a name="planned-maintenance-notifications-for-virtual-machine-scale-sets"></a>가상 머신 규모 집합에 대한 계획된 유지 관리 알림


Azure는 주기적으로 업데이트를 수행하여 VM(가상 머신)에 대한 호스트 인프라의 안정성, 성능 및 보안을 향상시킵니다. 업데이트에는 호스팅 환경 패치 또는 하드웨어 업그레이드 및 서비스 해제가 포함될 수 있습니다. 대부분의 업데이트는 호스팅된 VM에 영향을 주지 않습니다. 그러나 업데이트는 다음 시나리오에서 VM에 영향을 줍니다.

- 유지 관리에 다시 부팅이 필요하지 않은 경우 호스트가 업데이트되는 동안 Azure에서 전체 마이그레이션을 사용하여 VM을 일시 중지합니다. 다시 부팅할 필요가 없는 유지 관리 작업은 장애 도메인별로 장애 도메인에 적용됩니다. 경고 상태 신호를 받으면 진행이 중지됩니다.

- 유지 관리에 다시 부팅이 필요한 경우 유지 관리가 계획된 시기를 보여 주는 알림을 받게 됩니다. 이러한 경우 유지 관리가 가장 적합한 시점에 자체적으로 시작될 수 있는 시간 범위가 제공됩니다.


다시 부팅해야 하는 계획된 유지 관리는 웨이브에서 예약됩니다. 각 웨이브에는 서로 다른 범위(지역)가 있습니다.

- 웨이브는 고객에게 알림을 보내면서 시작합니다. 기본적으로 알림은 구독 소유자 및 공동 소유자에게 보내집니다. Azure [활동 로그 경고](../azure-monitor/platform/activity-logs-overview.md)를 사용하여 받는 사람 및 메시지 옵션(예: 이메일, SMS 및 웹후크)을 알림에 추가할 수 있습니다.  
- 알림을 통해 *셀프 서비스 기간*을 사용할 수 있습니다. 이 기간 동안 웨이브에 포함된 VM을 찾을 수 있습니다. 사용자 고유의 일정 요구 사항에 따라 사전에 유지 관리를 시작할 수 있습니다.
- 셀프 서비스 기간이 끝나면 *예약된 유지 관리 기간*이 시작됩니다. 이 기간 동안의 어떤 시점에서 Azure는 VM에 필요한 유지 관리를 예약하고 적용합니다. 

두 기간이 존재하는 이유는 Azure에서 유지 관리를 자동으로 시작할 시기를 파악하면서 유지 관리를 시작하고 VM을 다시 부팅하는 데 충분한 시간을 제공하기 위한 것입니다.

Azure Portal, PowerShell, REST API 및 Azure CLI를 사용하여 가상 머신 확장 집합 VM에 대한 유지 관리 기간을 쿼리하고, 셀프 서비스 유지 관리를 시작할 수 있습니다.

## <a name="should-you-start-maintenance-during-the-self-service-window"></a>셀프 서비스 기간 동안 유지 관리를 시작해야 하나요?  

다음 지침은 선택한 시간에 유지 관리를 시작할지 여부를 결정하는 데 도움이 됩니다.

> [!NOTE] 
> 일부 VM에는 셀프 서비스 유지 관리를 사용하지 못할 수도 있습니다. VM에 사전 예방적 재배포를 사용할 수 있는지 확인하려면 유지 관리 상태에서 **지금 시작**을 찾습니다. 현재 셀프 서비스 유지 관리는 Azure Cloud Services(웹/작업자 역할) 및 Azure Service Fabric에서 사용할 수 없습니다.


*가용성 집합*을 사용하는 배포에는 셀프 서비스 유지 관리가 권장되지 않습니다. 가용성 집합은 항상 하나의 업데이트 도메인만 영향을 받는 고가용성 설정입니다. 가용성 집합의 경우 다음과 같습니다.

- Azure에서 유지 관리를 트리거할 수 있습니다. 다시 부팅이 필요한 유지 관리의 경우 유지 관리가 업데이트 도메인별로 도메인을 업데이트합니다. 업데이트 도메인은 반드시 유지 관리를 순차적으로 받지는 않습니다. 업데이트 도메인 간에는 30분의 일시 중지가 있습니다.
- 일부 용량이 일시적으로 손실되는 경우(1/업데이트 도메인 수), 유지 관리 기간 동안 추가 인스턴스를 할당하여 손실을 쉽게 보정할 수 있습니다.
- 다시 부팅이 필요하지 않은 유지 관리의 경우 업데이트가 장애 도메인 수준에서 적용됩니다. 
    
다음 시나리오에서는 셀프 서비스 유지 관리를 사용하지 **마세요**. 

- DevTest Labs를 수동으로 사용하거나, 자동 종료를 사용하거나, 일정에 따라 VM을 자주 종료하는 경우. 이러한 시나리오에서 셀프 서비스 유지 관리를 수행하면 유지 관리 상태를 되돌리고 추가 가동 중지 시간이 발생할 수 있습니다.
- 유지 관리 웨이브가 끝나기 전에 삭제될 것을 알고 있는 단기간용 VM. 
- 업데이트 후에 유지 관리하려는 로컬(임시) 디스크에 대규모 상태가 저장되는 워크로드의 경우 
- VM의 크기를 자주 변경하는 경우. 이 시나리오에서는 유지 관리 상태를 되돌릴 수 있습니다. 
- 유지 관리 종료가 종료되기 15분 전에 워크로드의 사전 예방적 장애 조치 또는 정상 종료가 가능한 예약된 이벤트를 도입한 경우

예약된 유지 관리 단계 동안 VM을 중단 없이 실행하려는 경우 셀프 서비스 유지 관리를 **사용**하고 앞에서 언급한 역기능이 적용되지 않습니다. 

다음과 같은 경우에는 셀프 서비스 유지 관리를 사용하는 것이 가장 좋습니다.

- 관리 또는 고객에게 정확한 유지 관리 기간을 전달해야 합니다. 
- 특정 날짜까지 유지 관리를 완료해야 합니다. 
- 유지 관리 순서를 제어해야 합니다(예: 안전한 복구를 보장하기 위해 다층 계층 애플리케이션에서).
- 두 업데이트 도메인 간에 30분을 초과하는 VM 복구 시간이 필요합니다. 업데이트 도메인 간의 시간을 제어하려면 한 번에 하나의 업데이트 도메인에서 VM의 유지 관리를 트리거해야 합니다.

 
## <a name="view-virtual-machine-scale-sets-that-are-affected-by-maintenance-in-the-portal"></a>포털에서 유지 관리의 영향을 받는 가상 머신 확장 집합 보기

계획된 유지 관리 웨이브가 예약되면 Azure Portal을 사용하여 예정된 유지 관리 웨이브의 영향을 받는 가상 머신 확장 집합 목록을 볼 수 있습니다. 

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 왼쪽 메뉴에서 **모든 서비스**를 선택한 다음, **가상 머신 확장 집합**을 선택합니다.
3. **가상 머신 확장 집합** 아래에서 **열 편집**을 선택하여 사용 가능한 열 목록을 엽니다.
4. **사용 가능한 열** 섹션에서 **셀프 서비스 유지 관리**를 선택한 다음, **선택한 열** 목록으로 이동합니다. **적용**을 선택합니다.  

    **셀프 서비스 유지 관리** 항목을 더 쉽게 찾으려면 **사용 가능한 열** 섹션의 드롭다운 옵션을 **모두**에서  **속성**으로 변경할 수 있습니다.

이제 **셀프 서비스 유지 관리** 열이 가상 머신 확장 집합 목록에 나타납니다. 각 가상 머신 확장 집합의 셀프 서비스 유지 관리 열에는 다음 값 중 하나가 표시될 수 있습니다.

| 값 | 설명 |
|-------|-------------|
| 예 | 가상 머신 확장 집합에 있는 하나 이상의 VM이 셀프 서비스 기간에 있습니다. 이 셀프 서비스 기간 동안 언제든지 유지 관리를 시작할 수 있습니다. | 
| 아닙니다. | 영향을 받는 가상 머신 확장 집합의 셀프 서비스 기간에 VM이 없습니다. | 
| - | 가상 머신 확장 집합이 계획된 유지 관리 웨이브에 속하지 않습니다.| 

## <a name="notification-and-alerts-in-the-portal"></a>포털에서 알림 및 경고

Azure에서는 구독 소유자 및 공동 소유자 그룹에 이메일을 보내 계획된 유지 관리를 위한 일정을 알립니다. 활동 로그 경고를 만들어 받는 사람 및 채널을 이 통신에 추가할 수 있습니다. 자세한 내용은 [Azure 활동 로그로 구독 활동 모니터링](../azure-monitor/platform/activity-logs-overview.md)을 참조하세요.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 왼쪽 메뉴에서 **모니터**를 선택합니다. 
3. **모니터 - 경고(클래식)** 창에서 **+ 활동 로그 경고 추가**를 선택합니다.
4. **활동 로그 경고 추가** 페이지에서 필요한 정보를 선택하거나 입력합니다. **조건**에서 다음 값을 설정해야 합니다.
   - **이벤트 범주**: **서비스 상태**를 선택합니다.
   - **서비스**: **Virtual Machine Scale Sets 및 Virtual Machines**를 선택합니다.
   - **유형**: **계획된 유지 관리**를 선택합니다. 
    
활동 로그 경고를 구성하는 방법에 대한 자세한 내용은 [활동 로그 경고 만들기](../azure-monitor/platform/activity-log-alerts.md)를 참조하세요.
    
    
## <a name="start-maintenance-on-your-virtual-machine-scale-set-from-the-portal"></a>포털에서 가상 머신 확장 집합에 대해 유지 관리 시작

가상 머신 확장 집합의 개요에서 유지 관리 관련 세부 정보를 자세히 볼 수 있습니다. 가상 머신 확장 집합에 있는 하나 이상의 VM이 계획된 유지 관리 웨이브에 포함되면 페이지 위쪽에 새 알림 리본이 추가됩니다. 알림 리본을 선택하여 **유지 관리** 페이지로 이동합니다. 

**유지 관리** 페이지에서 계획된 유지 관리의 영향을 받는 VM 인스턴스를 볼 수 있습니다. 유지 관리를 시작하려면 영향을 받는 VM에 해당하는 확인란을 선택합니다. 그런 다음, **유지 관리 시작**을 선택합니다.

유지 관리가 시작되면 가상 머신 확장 집합에서 영향을 받는 VM이 유지 관리를 받고 일시적으로 사용할 수 없게 됩니다. 셀프 서비스 기간을 놓친 경우에도 Azure에서 가상 머신 확장 집합을 유지 관리하는 시간 범위는 계속 볼 수 있습니다.
 
## <a name="check-maintenance-status-by-using-powershell"></a>PowerShell을 사용하여 유지 관리 상태 확인

Azure PowerShell을 사용하여 가상 머신 확장 집합의 VM이 유지 관리에 대해 예약된 시기를 볼 수 있습니다. 계획된 유지 관리 정보는 `-InstanceView` 매개 변수를 사용하는 경우 [Get-AzVmss](https://docs.microsoft.com/powershell/module/az.compute/get-azvmss) cmdlet을 통해 사용할 수 있습니다.
 
유지 관리가 계획된 경우에만 유지 관리 정보가 반환됩니다. VM 인스턴스에 영향을 주는 유지 관리가 예약되지 않은 경우 이 cmdlet에서 유지 관리 정보를 반환하지 않습니다. 

```powershell
Get-AzVmss -ResourceGroupName rgName -VMScaleSetName vmssName -InstanceId id -InstanceView
```

**MaintenanceRedeployStatus**로 반환되는 속성은 다음과 같습니다. 

| 값 | 설명 |

|-------|---------------| | IsCustomerInitiatedMaintenanceAllowed | 이 이번에 유지 관리를 시작할 vm 수 있는지 여부를 나타냅니다. | | PreMaintenanceWindowStartTime | 유지 관리 셀프 서비스 기간이 VM에서 유지 관리를 시작할 수 때 시작 합니다. | | PreMaintenanceWindowEndTime | 유지 관리 셀프 서비스 기간이 VM에서 유지 관리를 시작할 수 때 종료 합니다. | | MaintenanceWindowStartTime | Azure가 VM에서 유지 관리를 시작 하는 예약 된 유지 관리 시작 | | MaintenanceWindowEndTime | Azure가 VM에서 유지 관리를 시작 하는 유지 관리 예약 기간의 종료 합니다. | | LastOperationResultCode | VM에서 유지 관리를 시작 하려면 마지막 시도의 결과입니다. |



### <a name="start-maintenance-on-your-vm-instance-by-using-powershell"></a>PowerShell을 사용하여 VM 인스턴스에서 유지 관리 시작

**IsCustomerInitiatedMaintenanceAllowed**가 **true**로 설정되는 경우 VM에서 유지 관리를 시작할 수 있습니다. [Set-AzVmss](/powershell/module/az.compute/set-azvmss) cmdlet을 `-PerformMaintenance` 매개 변수와 함께 사용합니다.

```powershell
Set-AzVmss -ResourceGroupName rgName -VMScaleSetName vmssName -InstanceId id -PerformMaintenance 
```

## <a name="check-maintenance-status-by-using-the-cli"></a>CLI를 사용하여 유지 관리 상태 확인

계획된 유지 관리 정보는 [az vmss list-instances](/cli/azure/vmss?view=azure-cli-latest#az-vmss-list-instances)를 사용하여 볼 수 있습니다.
 
유지 관리가 계획된 경우에만 유지 관리 정보가 반환됩니다. VM 인스턴스에 영향을 주는 유지 관리가 예약되지 않은 경우 이 명령에서 유지 관리 정보를 반환하지 않습니다. 

```azure-cli
az vmss list-instances -g rgName -n vmssName --expand instanceView
```

**MaintenanceRedeployStatus**로 각 VM 인스턴스에 대해 반환되는 속성은 다음과 같습니다. 

| 값 | 설명 |

|-------|---------------| | IsCustomerInitiatedMaintenanceAllowed | 이 이번에 유지 관리를 시작할 vm 수 있는지 여부를 나타냅니다. | | PreMaintenanceWindowStartTime | 유지 관리 셀프 서비스 기간이 VM에서 유지 관리를 시작할 수 때 시작 합니다. | | PreMaintenanceWindowEndTime | 유지 관리 셀프 서비스 기간이 VM에서 유지 관리를 시작할 수 때 종료 합니다. | | MaintenanceWindowStartTime | Azure가 VM에서 유지 관리를 시작 하는 예약 된 유지 관리 시작 | | MaintenanceWindowEndTime | Azure가 VM에서 유지 관리를 시작 하는 유지 관리 예약 기간의 종료 합니다. | | LastOperationResultCode | VM에서 유지 관리를 시작 하려면 마지막 시도의 결과입니다. |


### <a name="start-maintenance-on-your-vm-instance-by-using-the-cli"></a>CLI를 사용하여 VM 인스턴스에서 유지 관리 시작

`IsCustomerInitiatedMaintenanceAllowed`가 **true**로 설정되는 경우 다음 호출을 통해 VM 인스턴스에서 유지 관리를 시작합니다.

```azure-cli
az vmss perform-maintenance -g rgName -n vmssName --instance-ids id
```

## <a name="faq"></a>FAQ

**Q: 지금 내 VM을 다시 부팅해야 하는 이유는 무엇인가요?**

**A:** Azure 플랫폼에 대한 대부분의 업데이트와 업그레이드는 VM 가용성에 영향을 주지 않지만, Azure에서 호스트되는 VM을 다시 부팅해야 하는 경우도 있습니다. 서버를 다시 시작해야 하는 여러 변경이 누적되어 VM이 다시 부팅됩니다.

**Q: 가용성 집합을 사용하여 고가용성 권장 사항을 따르는 경우 안전한가요?**

**A:** 가용성 집합이나 가상 머신 확장 집합에 배포된 가상 머신은 업데이트 도메인을 사용합니다. 유지 관리를 수행할 때 Azure는 업데이트 도메인 제약 조건을 준수하고 동일한 가용성 집합 내의 다른 업데이트 도메인에서 VM을 다시 부팅하지 않습니다. 또한 Azure는 다음 VM 그룹으로 이동하기 전에 30분 이상 기다립니다. 

고가용성에 대한 자세한 내용은 [Azure에서 가상 머신의 지역 및 가용성](../virtual-machines/windows/regions-and-availability.md)을 참조하세요.

**Q: 계획된 유지 관리에 관한 알림을 받으려면 어떻게 해야 하나요?**

**A:** 계획된 유지 관리 주기는 하나 이상의 Azure 지역에 예약을 설정하는 것에서 출발합니다. 곧 이메일 알림이 구독 소유자에게 전달됩니다(구독당 1개 이메일). 활동 로그 경고를 사용하여 이 알림에 대한 채널 및 받는 사람을 추가할 수 있습니다. 계획된 유지 관리가 이미 예약된 지역에 VM을 배포하는 경우 알림을 받지 못합니다. 대신 VM의 유지 관리 상태를 확인하세요.

**Q: 포털, PowerShell 또는 CLI에서 계획된 유지 관리에 대한 표시가 전혀 나타나지 않습니다. 무엇이 문제인가요?**

**A:** 계획된 유지 관리와 관련된 정보는 계획된 유지 관리의 영향을 받는 VM에 대해서만 계획된 유지 관리 웨이브 동안 사용할 수 있습니다. 데이터가 표시되지 않으면 유지 관리 웨이브가 이미 완료되었거나, 시작되지 않았거나, VM이 업데이트된 서버에서 이미 호스팅되었을 수 있습니다.

**Q: 내 VM이 언제 영향을 받는지 정확히 알 수 있는 방법이 있나요?**

**A:** 예약을 설정할 때 며칠의 시간 범위를 정의합니다. 이 기간 동안 서버(및 VM)가 영향을 받는 정확한 시기는 알 수 없습니다. VM이 업데이트되는 정확한 시간을 알려고 하는 경우 [예약된 이벤트](../virtual-machines/windows/scheduled-events.md)를 사용하면 됩니다. 예약된 이벤트를 사용하면 VM 내에서 쿼리하고, VM을 다시 부팅하기 15분 전에 알림을 받을 수 있습니다.

**Q: 내 VM을 다시 부팅하는 데 얼마나 걸리나요?**

**A:**  VM의 크기에 따라 셀프 서비스 유지 관리 기간 동안 다시 부팅하는 데 최대 몇 분이 걸릴 수 있습니다. Azure가 예약된 유지 관리 기간에서 다시 부팅을 시작하는 동안 일반적으로 다시 부팅하는 데 약 25분 정도 걸립니다. Cloud Services(웹/작업자 역할), 가상 머신 확장 집합 또는 가용성 집합을 사용하는 경우, 예약된 유지 관리 기간 동안 각 VM 그룹(도메인 업데이트) 간에 30분이 제공됩니다. 

**Q: 내 VM에 관한 유지 관리 정보가 전혀 표시되지 않았습니다. 무엇이 문제인가요?**

**A:** VM에 대한 유지 관리 정보가 전혀 표시되지 않는 데는 다음과 같은 몇 가지 이유가 있습니다.
   - *Microsoft 내부*로 표시된 구독을 사용하고 있습니다.
   - VM에 대한 유지 관리가 예약되어 있지 않습니다. VM이 더 이상 영향을 받지 않도록 유지 관리 웨이브가 종료되었거나, 취소되었거나, 수정되었을 수 있습니다.
   - VM 목록 보기에 **유지 관리** 열을 추가할 필요는 없습니다. 이 열을 기본 보기에 추가했지만 기본이 아닌 열을 표시하도록 보기를 구성한 경우 **유지 관리** 열을 VM 목록 보기에 수동으로 추가해야 합니다.

**Q: 내 VM에 두 번째 유지 관리가 예약되었습니다. 그 이유는 무엇일까요?**

**A:** 여러 사용 사례에서 이미 유지 관리를 완료하고 재배포한 후에 VM에 대한 유지 관리가 예약됩니다.
   - 유지 관리 주기를 취소하고 다른 페이로드에서 다시 시작합니다. 오류가 발생한 페이로드를 탐지했을 수 있으며, 추가 페이로드를 배포하기만 하면 됩니다.
   - VM은 하드웨어 오류로 인해 다른 노드에 대해 *조정된 서비스*입니다.
   - VM을 중지(할당 취소)하고 다시 시작하도록 선택했습니다.
   - VM에 대해 **자동 종료**를 실행했습니다.

## <a name="next-steps"></a>다음 단계

[예약된 이벤트](../virtual-machines/windows/scheduled-events.md)를 사용하여 VM 내에서 유지 관리 이벤트를 등록하는 방법을 알아보세요.
