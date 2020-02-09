---
title: Azure 가상 머신 확장 집합 인스턴스에 대 한 종료 알림
description: Azure 가상 머신 확장 집합 인스턴스에 대 한 종료 알림을 사용 하도록 설정 하는 방법을 알아봅니다.
author: shandilvarun
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 08/27/2019
ms.author: vashan
ms.openlocfilehash: a1b1e07fa0622ae25d8086ec65827816ec52a5ce
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/19/2020
ms.locfileid: "76271746"
---
# <a name="terminate-notification-for-azure-virtual-machine-scale-set-instances-preview"></a>Azure 가상 머신 확장 집합 인스턴스 (미리 보기)에 대 한 종료 알림
확장 집합 인스턴스는 인스턴스 종료 알림을 수신 하도록 옵트인 (opt in) 하 고 미리 정의 된 지연 시간 제한을 종료 작업으로 설정할 수 있습니다. 종료 알림은 다시 부팅 및 다시 배포와 같은 작업에 대 한 알림을 제공 하는 Azure Metadata Service – [Scheduled Events](../virtual-machines/windows/scheduled-events.md)를 통해 전송 됩니다. Preview 솔루션은 Scheduled Events 목록에 또 다른 이벤트 (종료 – 종료)를 추가 하 고, terminate 이벤트의 연결 된 지연 시간은 확장 집합 모델 구성에서 사용자에 의해 지정 된 지연 제한에 따라 달라 집니다.

기능에 등록 되 면 인스턴스를 삭제 하기 전에 확장 집합 인스턴스는 지정 된 제한 시간이 만료 될 때까지 기다릴 필요가 없습니다. 종료 알림을 받은 후에는 언제 든 지 종료 시간 제한이 만료 되기 전에 인스턴스를 삭제 하도록 선택할 수 있습니다.

> [!IMPORTANT]
> 확장 집합 인스턴스에 대 한 종료 알림은 현재 공개 미리 보기로 제공 됩니다. 아래에 설명 된 공개 미리 보기 기능을 사용 하는 데는 옵트인 절차가 필요 하지 않습니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="enable-terminate-notifications"></a>종료 알림 사용
아래 예제에 설명 된 대로 확장 집합 인스턴스에서 종료 알림을 사용 하도록 설정 하는 방법에는 여러 가지가 있습니다.

### <a name="rest-api"></a>REST API

다음 예에서는 확장 집합 모델에 대해 종료 알림을 사용 하도록 설정 합니다.

```
PUT on `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}?api-version=2019-03-01`
```

```json
{
  "properties": {
    "virtualMachineProfile": {
            "scheduledEventsProfile": {
                "terminateNotificationProfile": {
                    "notBeforeTimeout":"PT5M",
                    "enable":true
                }
            }
        }
    }        
}

```

위의 블록은 확장 집합의 모든 인스턴스에 대 한 모든 종료 작업에 대해 5 분의 제한 시간 지연 ( *PT5M*로 표시 됨)을 지정 합니다. *NotBeforeTimeout* 필드는 ISO 8601 형식에서 5 분에서 15 분 사이의 값을 사용할 수 있습니다. 위에 설명 된 *terminateNotificationProfile* 에서 *notBeforeTimeout* 속성을 수정 하 여 종료 작업에 대 한 기본 시간 제한을 변경할 수 있습니다.

크기 집합 모델에서 *scheduledEventsProfile* 를 사용 하도록 설정 하 고 *notBeforeTimeout*를 설정한 후 변경 내용을 반영 하도록 개별 인스턴스를 [최신 모델로](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model) 업데이트 합니다.

> [!NOTE]
>확장 집합 인스턴스에 대 한 종료 알림은 API 버전 2019-03-01 이상 에서만 사용할 수 있습니다.

### <a name="azure-powershell"></a>Azure PowerShell
새 확장 집합을 만들 때 [AzVmss](/powershell/module/az.compute/new-azvmss) cmdlet을 사용 하 여 확장 집합에 종료 알림을 사용 하도록 설정할 수 있습니다.

```azurepowershell-interactive
New-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -Location "EastUS" `
  -VMScaleSetName "myScaleSet" `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -PublicIpAddressName "myPublicIPAddress" `
  -LoadBalancerName "myLoadBalancer" `
  -UpgradePolicyMode "Automatic" `
  -TerminateScheduledEvents
```

위의 예제에서는 5 분의 기본 시간 제한을 사용 하 여 종료 알림이 설정 된 새 확장 집합을 만듭니다. 새 확장 집합을 만들 때 *TerminateScheduledEvents* 매개 변수에는 값이 필요 하지 않습니다. 제한 시간 값을 변경 하려면 *TerminateScheduledEventNotBeforeTimeoutInMinutes* 매개 변수를 통해 원하는 시간 제한을 지정 합니다.

[AzVmss](/powershell/module/az.compute/update-azvmss) cmdlet을 사용 하 여 기존 확장 집합에 대 한 종료 알림을 사용 하도록 설정 합니다.

```azurepowershell-interactive
Update-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -VMScaleSetName "myScaleSet" `
  -TerminateScheduledEvents $true
  -TerminateScheduledEventNotBeforeTimeoutInMinutes 15
```
위의 예에서는 기존 확장 집합에 대 한 종료 알림을 사용 하도록 설정 하 고 terminate 이벤트에 대해 15 분 시간 제한을 설정 합니다.

확장 집합 모델에서 예약 된 이벤트를 사용 하도록 설정 하 고 제한 시간을 설정한 후 변경 내용을 반영 하도록 개별 인스턴스를 [최신 모델로](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model) 업데이트 합니다.

## <a name="get-terminate-notifications"></a>종료 알림 가져오기

종료 알림은 Azure Metadata Service [Scheduled Events](../virtual-machines/windows/scheduled-events.md)을 통해 전달 됩니다. Azure 메타데이터 서비스는 VM 내에서 액세스할 수 있는 REST 엔드포인트를 사용하여 Virtual Machines 실행에 대한 정보를 공개합니다. 이 정보는 VM 외부에 노출 되지 않도록 라우팅할 수 없는 IP를 통해 사용할 수 있습니다.

이벤트에 대 한 요청을 처음으로 수행할 때 확장 집합에 대 한 Scheduled Events를 사용할 수 있습니다. 최대 2 분의 첫 번째 호출에서 지연 된 응답을 받을 수 있습니다. 정기적으로 엔드포인트을 쿼리하여 예정 된 유지 관리 이벤트와 진행 중인 유지 관리 작업의 상태를 검색 합니다.

확장 집합 인스턴스가 24 시간 동안 요청 하지 않는 경우 확장 집합에 대해 Scheduled Events를 사용할 수 없습니다.

### <a name="endpoint-discovery"></a>엔드포인트 검색
VNET 사용 Vm의 경우 Metadata Service는 라우팅 불가능 한 고정 IP 169.254.169.254에서 사용할 수 있습니다.

이 미리 보기에 대 한 최신 버전의 Scheduled Events에 대 한 전체 엔드포인트은 다음과 같습니다.
> 'http://169.254.169.254/metadata/scheduledevents?api-version=2019-01-01 '

### <a name="query-response"></a>쿼리 응답
응답에는 예약된 이벤트의 배열이 포함됩니다. 빈 배열은 현재 예약된 이벤트가 없음을 의미합니다.

예약된 이벤트가 있는 경우 응답에 다음과 같은 이벤트의 배열이 포함됩니다. "Terminate" 이벤트의 경우 응답은 다음과 같이 표시 됩니다.
```
{
    "DocumentIncarnation": {IncarnationID},
    "Events": [
        {
            "EventId": {eventID},
            "EventType": "Terminate",
            "ResourceType": "VirtualMachine",
            "Resources": [{resourceName}],
            "EventStatus": "Scheduled",
            "NotBefore": {timeInUTC},
        }
    ]
}
```
DocumentIncarnation은 ETag로, 이벤트 페이로드가 지난 번 쿼리 후 변경되었는지 검사하는 간편한 방법을 제공합니다.

위의 각 필드에 대 한 자세한 내용은 [Windows](../virtual-machines/windows/scheduled-events.md#event-properties) 및 [Linux](../virtual-machines/linux/scheduled-events.md#event-properties)에 대 한 Scheduled Events 설명서를 참조 하세요.

### <a name="respond-to-events"></a>이벤트에 응답
예정 된 이벤트를 학습 고 정상 종료를 위한 논리를 완료 하면 EventId를 사용 하 여 메타 데이터 서비스에 대 한 POST 호출을 수행 하 여 처리 중인 이벤트를 승인할 수 있습니다. POST 호출은 VM 삭제를 계속할 수 있음을 Azure에 나타냅니다.

POST 요청 본문에 필요한 json은 다음과 같습니다. 요청에 StartRequests 목록이 포함 되어 있어야 합니다. 각 StartRequest에는 신속 하 게 원하는 이벤트의 EventId가 포함 됩니다.
```
{
    "StartRequests" : [
        {
            "EventId": {EventId}
        }
    ]
}
```

확장 집합의 모든 VM이 해당 VM과 관련 된 EventID만 승인 하는지 확인 합니다. VM은 [인스턴스 메타 데이터를 통해](virtual-machine-scale-sets-instance-ids.md#instance-metadata-vm-name)자체 vm 이름을 가져올 수 있습니다. 이 이름은 "{scale-set-name} _ {instance-id}" 형식을 사용 하며 위에서 설명한 쿼리 응답의 ' Resources ' 섹션에 표시 됩니다.

[PowerShell](../virtual-machines/windows/scheduled-events.md#powershell-sample) 및 [Python](../virtual-machines/linux/scheduled-events.md#python-sample)을 사용 하 여 이벤트 쿼리 및 응답에 대 한 샘플 스크립트를 참조할 수도 있습니다.

## <a name="tips-and-best-practices"></a>팁과 모범 사례
-   ' 삭제 ' 작업에 대 한 알림만 종료 – 확장 집합에서 *scheduledEventsProfile* 을 사용 하도록 설정한 경우 모든 삭제 작업 (수동 삭제 또는 자동 크기 조정 시작 된 확장)에서 종료 이벤트를 생성 합니다. 다시 부팅, 이미지로 다시 설치, 다시 배포 및 중지/할당 취소와 같은 다른 작업은 종료 이벤트를 생성 하지 않습니다. 우선 순위가 낮은 Vm에 대해서는 종료 알림을 사용 하도록 설정할 수 없습니다.
-   시간 제한에 대 한 필수 대기 없음 – 이벤트를 받은 후 이벤트의 *NotBefore* 시간이 만료 되기 전에 언제 든 지 terminate 작업을 시작할 수 있습니다.
-   제한 시간에 필수 삭제 – 미리 보기는 이벤트가 생성 된 후 시간 제한 값을 확장 하는 기능을 제공 하지 않습니다. 제한 시간이 만료 되 면 보류 중인 종료 이벤트가 처리 되 고 VM이 삭제 됩니다.
-   수정 가능한 시간 제한 값 – 인스턴스를 삭제 하기 전에 언제 든 지 확장 집합 모델에서 *notBeforeTimeout* 속성을 수정 하 고 VM 인스턴스를 최신 모델로 업데이트 하 여 시간 제한 값을 수정할 수 있습니다.
-   보류 중인 삭제 모두 승인 – 승인 되지 않은 VM_1에 보류 중인 삭제가 있고 VM_2에서 다른 terminate 이벤트를 승인한 경우 VM_1에 대 한 terminate 이벤트가 승인 되거나 해당 시간 제한이 경과할 때까지 VM_2 삭제 되지 않습니다. VM_1에 대 한 terminate 이벤트를 승인 하면 VM_1 및 VM_2 모두 삭제 됩니다.
-   모든 동시 삭제 승인 – 위의 예제를 확장 하 고 VM_1 및 VM_2 동일한 *NotBefore* 시간이 있는 경우에는 종료 이벤트를 모두 승인 하거나 시간 제한이 만료 되기 전에 VM을 모두 삭제 하지 않아야 합니다.

## <a name="troubleshoot"></a>문제 해결
### <a name="failure-to-enable-scheduledeventsprofile"></a>ScheduledEventsProfile를 사용 하도록 설정 하지 못했습니다.
"VirtualMachineProfile ' 형식의 개체에서 ' scheduledEventsProfile ' 멤버를 찾을 수 없습니다." 라는 오류 메시지와 함께 ' BadRequest ' 오류가 표시 되 면 확장 집합 작업에 사용 된 API 버전을 확인 합니다. 이 미리 보기에는 Compute API 버전 **2019-03-01** 이상이 필요 합니다.

### <a name="failure-to-get-terminate-events"></a>종료 이벤트를 가져오지 못했습니다.
Scheduled Events를 통해 **종료** 이벤트를 가져오지 않는 경우 이벤트를 가져오는 데 사용 된 API 버전을 확인 합니다. 종료 이벤트에 Metadata Service API 버전 **2019-01-01** 이상이 필요 합니다.
>'http://169.254.169.254/metadata/scheduledevents?api-version=2019-01-01 '

### <a name="getting-terminate-event-with-incorrect-notbefore-time"></a>NotBefore 시간이 잘못 된 Terminate 이벤트를 가져오는 중  
크기 집합 모델에서 *scheduledEventsProfile* 를 사용 하도록 설정 하 고 *notBeforeTimeout*를 설정한 후 변경 내용을 반영 하도록 개별 인스턴스를 [최신 모델로](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model) 업데이트 합니다.

## <a name="next-steps"></a>다음 단계
가상 머신 확장 집합에 [애플리케이션을 배포하는 방법](virtual-machine-scale-sets-deploy-app.md)에 대해 알아봅니다.
