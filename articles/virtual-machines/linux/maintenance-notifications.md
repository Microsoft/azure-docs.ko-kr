---
title: Azure에서 Linux VM에 대한 유지 관리 알림 처리 | Microsoft Docs
description: Azure에서 실행 중인 Linux 가상 머신에 대한 유지 관리 알림을 확인하고 셀프 서비스 유지 관리를 시작합니다.
services: virtual-machines-linux
documentationcenter: ''
author: shants123
editor: ''
tags: azure-service-management,azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 07/02/2018
ms.author: shants
ms.openlocfilehash: 860cb2bee902c6559b7851eb05fa9c5270876fe9
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62126973"
---
# <a name="handling-planned-maintenance-notifications-for-linux-virtual-machines"></a>Linux 가상 머신에 대한 계획된 유지 관리 처리

Azure에서는 가상 머신에 대한 호스트 인프라의 안정성, 성능 및 보안을 향상시키기 위해 주기적으로 업데이트를 수행합니다. 업데이트란 호스팅 환경의 패치 적용 또는 하드웨어의 업그레이드 및 서비스 해제와 같은 변경 내용을 말합니다. 이러한 업데이트 중 대다수는 호스트된 가상 머신에 영향을 미치지 않고 수행됩니다. 그러나 업데이트가 다음 항목에 영향을 미치는 경우가 있습니다.

- 유지 관리를 다시 부팅하지 않아도 되는 경우 Azure에서는 호스트를 업데이트하는 동안 바로 마이그레이션을 사용하여 VM을 일시 중지합니다. 이러한 재부팅 불가능 유지 관리 작업은 장애 도메인에 의해 적용된 장애 도메인이고 경고 상태 신호를 받는 경우 진행률이 중지됩니다.

- 유지 관리를 다시 부팅해야 하는 경우 유지 관리가 계획된 시기에 대해 알림을 받을 수 있습니다. 이러한 경우에 사용자가 원하는 시점에 스스로 유지 관리를 시작할 수 있는 기간도 지정됩니다.


다시 부팅해야 하는 계획된 유지 관리는 웨이브에서 예약됩니다. 각 웨이브는 범위(지역)이 다릅니다.

- 웨이브는 고객에게 알림을 보내면서 시작합니다. 기본적으로 알림은 구독 소유자 및 공동 소유자에게 전송됩니다. Azure [활동 로그 경고](../../azure-monitor/platform/activity-logs-overview.md)를 사용하여 추가 수신자나 전자 메일, SMS 및 웹후크와 같은 메시지 옵션을 추가할 수 있습니다.  
- 알림과 동시에 *셀프 서비스 기간*이 제공됩니다. 이 기간 동안 이 웨이브에 포함되어 있는 가상 머신을 찾고, 일정에 따라 사전 예방적으로 유지 관리를 시작할 수 있습니다.
- 셀프 서비스 기간이 끝나면 *예약된 유지 관리 기간*이 시작됩니다. 이 기간 중 어떤 시점에 Azure는 가상 머신에 필요한 유지 관리를 예약하고 적용합니다. 

두 기간이 존재하는 이유는 Azure에서 유지 관리를 자동으로 시작하는 시기를 파악하면서 유지 관리를 시작하고 가상 머신을 다시 부팅하는 데 충분한 시간을 제공하기 위한 것입니다.


Azure Portal, PowerShell, REST API 및 CLI를 사용하여 사용자 VM에 대한 유지 관리 기간을 쿼리하고, 셀프 서비스 유지 관리를 시작할 수 있습니다.

 
## <a name="should-you-start-maintenance-using-during-the-self-service-window"></a>셀프 서비스 기간 동안 유지 관리를 시작해야 합니까?  

다음 지침은 이 기능을 사용하여 여유 있는 시간에 유지 관리를 시작해야 하는지 결정하는 데 도움이 됩니다. 

> [!NOTE] 
> 일부 VM에는 셀프 서비스 유지 관리를 사용하지 못할 수도 있습니다. VM에 사전 예방적 재배포를 사용할 수 있는지 확인하려면 유지 관리 상태에서 **지금 시작**을 살펴봅니다. 현재 셀프 서비스 유지 관리는 Cloud Services(웹/작업자 역할) 및 Service Fabric에서 사용할 수 없습니다.


**가용성 집합**을 사용하는 배포의 경우 특정 시간에 업데이트 도메인 하나만 영향을 받는 고가용성 설치이므로 셀프 서비스 유지 관리를 사용하지 않는 것이 좋습니다. 
- Azure에서 유지 관리를 트리거할 수 있습니다. 다시 부팅이 필요한 유지 관리의 경우 유지 관리는 업데이트 도메인에 의해 업데이트 도메인을 수행하고, 업데이트 도메인은 반드시 유지 관리를 순차적으로 수신할 필요가 없고, 업데이트 도메인 간에 30분의 일시 중지가 있다는 점에 유의합니다. 
- 일부 용량이 일시적으로 손실(1/업데이트 도메인 수)될 것이 우려되는 경우 유지 관리 기간에 추가 인스턴스를 할당하면 간단하게 보정할 수 있습니다. 
- 다시 부팅이 필요하지 않은 유지 관리의 경우 업데이트는 장애 도메인 수준에서 적용됩니다. 

다음 시나리오에서는 셀프 서비스 유지 관리를 사용하지 **마세요**. 
- 수동으로, DevTest Labs를 사용하여, 자동 종료를 사용하여 또는 일정에 따라 VM을 자주 종료하는 경우 유지 관리 상태를 되돌려서 가동 중지 시간이 추가로 발생할 수 있습니다.
- 유지 관리 웨이브가 끝나기 전에 삭제될 것을 알고 있는 단기간용 VM. 
- 업데이트 시 유지 관리하려는 로컬(임시) 디스크에 대규모 상태가 저장되는 워크로드. 
- VM 크기를 자주 조정하는 경우, 유지 관리 상태를 되돌릴 수 있기 때문입니다. 
- 유지 관리 시스템 종료 15분 전에 워크로드의 사전 예방적 장애 조치(failover) 또는 정상 종료가 가능한 예약된 이벤트를 도입한 경우.

예약된 유지 관리 단계 동안 중단 없이 VM을 실행하고 위에서 언급한 금기 중 어떤 것에도 해당하지 않는 경우 셀프 서비스 유지 관리를 **사용**합니다. 

다음과 같은 경우에는 셀프 서비스 유지 관리를 사용하는 것이 가장 좋습니다.
- 관리 부서 또는 최종 사용자에게 정확한 유지 관리 기간을 전달해야 합니다. 
- 지정된 날짜까지 유지 관리를 완료해야 합니다. 
- 유지 관리의 순서를 제어해야 합니다(예: 안전 복구를 보장하기 위한 다층 계층 애플리케이션).
- 두 UD(업데이트 도메인) 간에 30분의 VM 복구 시간이 필요합니다. 업데이트 도메인 사이의 시간을 제어하려면 한 번에 하나의 UD(업데이트 도메인)에서 VM 유지 관리를 트리거해야 합니다.



## <a name="find-vms-scheduled-for-maintenance-using-cli"></a>CLI를 사용하여 유지 관리가 예약된 VM 찾기

계획된 유지 관리 정보는 [azure vm get-instance-view](/cli/azure/vm?view=azure-cli-latest)를 사용하여 볼 수 있습니다.
 
유지 관리 정보는 계획된 유지 관리가 있는 경우에만 반환됩니다. VM에 영향을 미치는 예약된 유지 관리가 없는 경우 명령은 유지 관리 정보를 반환하지 않습니다. 

```azure-cli
az vm get-instance-view -g rgName -n vmName
```

MaintenanceRedeployStatus의 다음과 같은 값이 반환됩니다. 

| 값 | 설명   |
|-------|---------------|
| IsCustomerInitiatedMaintenanceAllowed | 이번에 VM에서 유지 관리를 시작할 수 있는지 여부를 나타냅니다. |
| PreMaintenanceWindowStartTime         | VM에서 유지 관리를 시작할 수 있을 때 유지 관리 셀프 서비스 기간의 시작 시간입니다. |
| PreMaintenanceWindowEndTime           | VM에서 유지 관리를 시작할 수 있을 때 유지 관리 셀프 서비스 기간의 종료 시간입니다. |
| MaintenanceWindowStartTime            | Azure가 VM에서 유지 관리를 시작하는 유지 관리 예약 기간의 시작 시간입니다. |
| MaintenanceWindowEndTime              | Azure가 VM에서 유지 관리를 시작하는 유지 관리 예약 기간의 종료 시간입니다. |
| LastOperationResultCode               | VM에서 유지 관리를 시작하는 마지막 시도의 결과입니다. |




## <a name="start-maintenance-on-your-vm-using-cli"></a>CLI를 사용하여 VM에서 유지 관리 시작

`IsCustomerInitiatedMaintenanceAllowed`가 true로 설정되는 경우 다음 호출이 VM에서 유지 관리를 시작합니다.

```azure-cli
az vm perform-maintenance -g rgName -n vmName 
```

[!INCLUDE [virtual-machines-common-maintenance-notifications](../../../includes/virtual-machines-common-maintenance-notifications.md)]

## <a name="classic-deployments"></a>클래식 배포

클래식 배포 모델을 사용하여 배포된 레거시 VM이 아직 있는 경우 Azure 클래식 CLI를 사용하여 VM을 쿼리하고 유지 관리를 시작할 수 있습니다.

다음을 입력하여 클래식 VM에서 작동하도록 올바른 모드에 있는지 확인합니다.

```
azure config mode asm
```

*myVM*이라는 VM에 대한 유지 관리 상태를 가져오려면 다음을 입력합니다.

```
azure vm show myVM 
``` 

*myService* 서비스 및 *myDeployment* 배포에서 *myVM*이라는 클래식 VM에 대한 유지 관리를 시작하려면 다음을 입력합니다.

```
azure compute virtual-machine initiate-maintenance --service-name myService --name myDeployment --virtual-machine-name myVM
```


## <a name="faq"></a>FAQ


**Q: 왜 내 가상 머신을 지금 다시 부팅해야 하나요?**

**A:** Azure 플랫폼에 대한 대부분의 업데이트와 업그레이드는 가상 머신의 가용성에 영향을 주지 않으나, Azure에서 호스트되는 가상 머신을 불가피하게 다시 부팅해야 하는 경우가 있습니다. 서버 재시작이 필요한 여러 변경 사항이 누적되면 가상 머신 다시 부팅이 발생하게 됩니다.

**Q: 가용성 집합을 사용하여 고가용성 권장 사항을 따르는 경우 안전한가요?**

**A:** 가용성 집합 또는 가상 머신 확장 집합에 배포된 가상 머신에는 UD(업데이트 도메인) 개념이 있습니다. 유지 관리를 수행할 때 Azure는 UD 제약 조건을 적용하고 다른 UD(동일한 가용성 집합 내)의 가상 머신을 다시 부팅하지 않습니다.  또 Azure는 다음 가상 머신 그룹으로 이동하기 전에 30분 이상 대기합니다. 

고가용성에 대한 자세한 내용은 [Azure에서 가상 머신의 지역 및 가용성](regions-and-availability.MD)을 참조하세요.

**Q: 계획된 유지 관리에 관한 알림은 어떻게 받나요?**

**A:** 계획된 유지 관리 주기는 하나 이상의 Azure 지역에 예약을 설정하는 것에서 출발합니다. 곧 이메일 알림이 구독 소유자에게 전달됩니다(구독당 1개 이메일). 이 알림에 대한 추가 채널과 받는 사람은 활동 로그 경고를 통해 구성할 수 있습니다.  계획된 유지 관리가 이미 예약된 지역에 가상 머신을 배포하는 경우 알림이 전달되지 않으므로 VM의 유지 관리 상태를 확인해야 합니다.

**Q: 포털, Powershell 또는 CLI에서 계획된 유지 관리의 표시가 전혀 나타나지 않습니다. 무엇이 문제인가요?.**

**A:** 계획된 유지 관리 관련 정보는 영향을 받게 되는 VM에 대해서만 계획된 유지 관리 주기 중에 제공됩니다. 즉 데이터가 표시되지 않는다면 유지 관리 주기가 이미 완료되었거나(또는 시작되지 않음) 가상 머신이 이미 업데이트된 서버에서 호스팅되는 것일 수 있습니다.

**Q: 내 가상 머신이 정확히 언제 영향을 받는지 확인할 수 있나요?**

**A:** 예약을 설정할 때 며칠의 기간을 정의합니다. 그러나 이 창 내에서의 정확한 서버(및 VM) 순서는 알 수 없습니다. VM에 해당하는 정확한 시간을 알고자 하는 고객은 [예약된 이벤트](scheduled-events.md)를 사용하고 가상 머신 안에서 쿼리하여 VM이 다시 부팅되기 15분 전에 알림을 수신할 수 있습니다.

**Q: 가상 머신을 다시 부팅하는 데 얼마나 걸리나요?**

**A:**  VM의 크기에 따라 다시 부팅은 셀프 서비스 유지 관리 기간 동안 최대 몇 분이 소요될 수 있습니다. Azure가 예약된 유지 관리 기간에서 다시 부팅을 시작하는 동안 다시 부팅은 일반적으로 약 25분 정도가 걸립니다. Cloud Services(웹/작업자 역할), Virtual Machine Scale Sets 또는 가용성 집합을 사용하는 경우 예약된 유지 관리 기간 동안 각 VM 그룹(UD) 사이에 30분이 주어집니다.

**Q: Virtual Machine Scale Sets의 경우에 환경이란?**

**A:** 이제 계획된 유지 관리는 Virtual Machine Scale Sets에 지원됩니다. 셀프 서비스 유지 관리를 시작하는 방법에 대한 지침은 [VMSS에 대한 계획된 유지 관리](../../virtual-machine-scale-sets/virtual-machine-scale-sets-maintenance-notifications.md) 문서를 참조하세요.

**Q: Cloud Services(웹/작업자 역할) 및 Service Fabric의 경우에 환경이란?**

**A:** 이러한 플랫폼은 계획된 유지 관리의 영향을 받지만, 이런 플랫폼을 사용하는 고객은 특정 시간에 단일 UD(업그레이드 도메인)의 VM만 영향을 받는다면 안전하다고 할 수 있습니다. 현재 셀프 서비스 유지 관리는 Cloud Services(웹/작업자 역할) 및 Service Fabric에서 사용할 수 없습니다.

**Q: 내 VM에 관한 유지 관리 정보가 전혀 표시되지 않았습니다. 무엇이 문제인가요?**

**A:** VM에 대한 유지 관리 정보가 전혀 표시되지 않는 데는 몇 가지 이유가 있습니다.
1.  Microsoft 내부로 표시된 구독을 사용하고 있습니다.
2.  VM에 유지 관리가 예약되어 있지 않습니다. 유지 관리 주기가 종료, 취소 또는 수정되면 VM이 더 이상 해당 주기의 영향을 받지 않습니다.
3.  VM 목록 보기에 **유지 관리** 열을 추가할 필요는 없습니다. 기본 보기에 이 열을 추가했지만, 기본이 아는 열을 보도록 구성한 고객은 수동으로 **유지 관리** 열을 VM 목록 보기에 추가해야 합니다.

**Q: 내 VM에 두 번째 유지 관리가 예약되었습니다. 그 이유는 무엇일까요?**

**A:** 유지 관리 재배포를 이미 완료한 후에도 VM에 유지 관리가 예약되는 몇 가지 사용 사례가 있습니다.
1.  유지 관리 주기를 취소하고 다른 페이로드에서 다시 시작합니다. 오류가 발생한 페이로드를 탐지하여 단순히 추가 페이로드를 배포해야 합니다.
2.  VM은 하드웨어 오류로 인해 다른 노드에 대해 *조정된 서비스*입니다.
3.  VM을 중지(할당 취소)하고 다시 시작하도록 선택했습니다.
4.  VM에 대해 **자동 종료**를 실행했습니다.


## <a name="next-steps"></a>다음 단계

[예약된 이벤트](scheduled-events.md)를 사용하여 VM 내에서 유지 관리 이벤트에 등록하는 방법을 알아보세요.
