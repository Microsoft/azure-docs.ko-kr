---
title: Azure에서 VM의 수명 주기 및 상태
description: 언제 든 지 VM이 있을 수 있는 다양 한 상태에 대 한 설명을 포함 하 여 Azure의 VM 수명 주기에 대 한 개요입니다.
services: virtual-machines
author: shandilvarun
ms.service: virtual-machines
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 08/09/2018
ms.author: vashan
ms.openlocfilehash: cd104a31fdba932481889db198ae3cc4998a4466
ms.sourcegitcommit: 64ad2c8effa70506591b88abaa8836d64621e166
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/17/2020
ms.locfileid: "88261891"
---
# <a name="virtual-machines-lifecycle-and-states"></a>가상 머신 수명 주기 및 상태

Azure VM(Virtual Machines)은 *프로비전* 및 *전원* 상태로 분류될 수 있는 여러 상태를 거칩니다. 이 아티클은 이러한 상태를 설명하고, 고객에게 인스턴스 사용량에 요금이 청구된 경우 특히 강조 표시하려고 합니다. 

## <a name="power-states"></a>전원 상태

전원 상태는 VM의 마지막 알려진 상태를 나타냅니다.

![VM 전원 상태 다이어그램](./media/virtual-machines-common-states-lifecycle/vm-power-states.png)

<br>
다음 표에서는 각 인스턴스 상태를 설명하고 인스턴스 사용량에 대해 요금이 청구되는지 여부를 나타냅니다.

:::row:::
   :::column span="":::

   **State**
   
   :::column-end:::
   :::column span="":::

   **설명**

   :::column-end:::
   :::column span="":::

   **인스턴스 사용량 청구 됨**

   :::column-end:::
:::row-end:::

:::row:::
   :::column span="":::

   **시작 중**

   :::column-end:::
   :::column span="":::

   VM이 시작되고 있습니다.

   ```json
   "statuses": [
    {
    "code": "PowerState/starting",
    "level": "Info",
    "displayStatus": "VM starting"
    }
   ]
   ```
   :::column-end:::
   :::column span="":::

   **청구되지 않음**

   :::column-end:::
:::row-end:::

:::row:::
   :::column span="":::

   **실행 중**

   :::column-end:::
   :::column span="":::

   VM의 정상적인 작동 상태

   ```json
   "statuses": [
    {
    "code": "PowerState/running",
    "level": "Info",
    "displayStatus": "VM running"
    }
  ]
  ```
   :::column-end:::
   :::column span="":::

   **청구**

   :::column-end:::
:::row-end:::

:::row:::
   :::column span="":::

   **중지 중**

   :::column-end:::
   :::column span="":::

   전환 상태입니다. 작업이 완료되면 **중지됨**으로 표시됩니다.

   ```json
   "statuses": [
    {
    "code": "PowerState/stopping",
    "level": "Info",
    "displayStatus": "VM stopping"
    }
   ]
  ```
   :::column-end:::
   :::column span="":::

   **청구**

   :::column-end:::
:::row-end:::

:::row:::
   :::column span="":::

   **중지됨**

   :::column-end:::
   :::column span="":::

   게스트 OS 내에서 또는 PowerOff API를 사용하여 VM이 종료되었습니다.

   하드웨어가 여전히 VM에 할당되고 호스트에서 유지됩니다.

   ```json
   "statuses": [
    {
    "code": "PowerState/stopped",
    "level": "Info",
    "displayStatus": "VM stopped"
    }
   ]
  ```
   :::column-end:::
   :::column span="":::

   **청구***

   :::column-end:::
:::row-end:::

:::row:::
   :::column span="":::

   **할당 취소 중**

   :::column-end:::
   :::column span="":::

   전환 상태입니다. 작업이 완료되면 VM은 **할당 취소됨**.

   ```json
   "statuses": [
    {
    "code": "PowerState/deallocating",
    "level": "Info",
    "displayStatus": "VM deallocating"
    }
   ]
  ```
   :::column-end:::
   :::column span="":::

   **청구 되지 않음***

   :::column-end:::
:::row-end:::

:::row:::
   :::column span="":::

   **할당 취소됨**

   :::column-end:::
   :::column span="":::

   VM이 성공적으로 중지되고 호스트에서 제거됩니다.

   ```json
   "statuses": [
    {
    "code": "PowerState/deallocated",
    "level": "Info",
    "displayStatus": "VM deallocated"
    }
   ]
  ```
   :::column-end:::
   :::column span="":::

   **청구되지 않음**

   :::column-end:::
:::row-end:::


일부 Azure 리소스 (예: 디스크 및 네트워킹)에는 요금이 부과 됩니다. &#42; 인스턴스의 소프트웨어 라이선스에는 요금이 부과되지 않습니다.

## <a name="provisioning-states"></a>프로비전 상태

프로비전 상태는 VM에서 사용자가 시작한 제어 평면 작업의 상태입니다. 이러한 상태는 VM의 전원 상태와 별개입니다.

- **만들기** - VM 생성입니다.

- **업데이트** – 기존 VM에 대한 모델을 업데이트합니다. 또한 시작/다시 시작과 같은 VM에 일부 모델이 아닌 변경 내용이 업데이트 대상입니다.

- **삭제** – VM 삭제입니다.

- **할당 취소** – VM이 중지되고 호스트에서 제거되는 위치입니다. VM의 할당이 취소되면 업데이트라고 간주되므로 업데이트와 관련된 프로비전 상태를 표시합니다.



플랫폼이 사용자 시작 작업을 허용한 후에 전환 작업 상태는 다음과 같습니다.

:::row:::
   :::column span="":::

   **State**
   
   :::column-end:::
   :::column span="2":::

   **설명**

   :::column-end:::

:::row-end:::

:::row:::
   :::column span="":::

   **만드는 중**

   :::column-end:::
   :::column span="2":::

  ```json
   "statuses": [
    {
    "code": "ProvisioningState/creating",
    "level": "Info",
    "displayStatus": "Creating"
    }
   [
   ```
   :::column-end:::

:::row-end:::

:::row:::
   :::column span="":::

   **업데이트**

   :::column-end:::
   :::column span="2":::

   ```json
   "statuses": [
    {
    "code": "ProvisioningState/updating",
    "level": "Info",
    "displayStatus": "Updating"
    }
   [
   ```
   :::column-end:::

:::row-end:::

:::row:::
   :::column span="":::

   **삭제 중**

   :::column-end:::
   :::column span="2":::

   ```json
   "statuses": [
    {
    "code": "ProvisioningState/deleting",
    "level": "Info",
    "displayStatus": "Deleting"
    }
   [
   ```
   :::column-end:::

:::row-end:::

:::row:::
   :::column span="":::

   **OS 프로비전 상태**
   
   :::column-end:::
   :::column span="2":::

   **설명**

   :::column-end:::

:::row-end:::

:::row:::
   :::column span="":::



   :::column-end:::
   :::column span="2":::

   특수화된 이미지가 아닌 OS 이미지를 사용하여 VM을 만든 경우 다음 하위 상태를 관찰할 수 있습니다.

   :::column-end:::

:::row-end:::

:::row:::
   :::column span="":::

   **OSProvisioningInprogress**

   :::column-end:::
   :::column span="2":::

   VM이 실행 중 이며 게스트 OS 설치가 진행 중입니다.
 
   ```json
   "statuses": [
    {
    "code": "ProvisioningState/creating/OSProvisioningInprogress",
    "level": "Info",
    "displayStatus": "OS Provisioning In progress"
    }
   [
   ```
   :::column-end:::

:::row-end:::

:::row:::
   :::column span="":::

   **OSProvisioningComplete**

   :::column-end:::
   :::column span="2":::
   
   단기 상태. 확장을 설치할 필요가 없으면 VM은 **성공**으로 신속하게 전환됩니다. 확장을 설치하는 데 시간이 걸릴 수 있습니다.
   
   ```json
   "statuses": [
    {
    "code": "ProvisioningState/creating/OSProvisioningComplete",
    "level": "Info",
    "displayStatus": "OS Provisioning Complete"
    }
   [
   ```
   
   **참고**: OS 오류가 발생하지 않거나 OS를 시간 내에 설치하지 않으면 OS 프로비전은 **실패됨**으로 전환될 수 있습니다. 인프라에 배포된 VM에 대해 고객에게 비용이 청구됩니다.

   :::column-end:::

:::row-end:::

작업이 완료되면 VM은 다음 중 하나의 상태로 전환됩니다.

- **성공함** – 사용자 시작 작업이 완료되었습니다.

    ```
  "statuses": [ 
  {
     "code": "ProvisioningState/succeeded",
     "level": "Info",
     "displayStatus": "Provisioning succeeded",
     "time": "time"
  }
  ]
    ```

 

- **실패함** – 실패한 작업을 나타냅니다. 오류 코드를 참조하여 자세한 내용 및 가능한 솔루션을 가져옵니다.

    ```
  "statuses": [
    {
      "code": "ProvisioningState/failed/InternalOperationError",
      "level": "Error",
      "displayStatus": "Provisioning failed",
      "message": "Operation abandoned due to internal error. Please try again later.",
      "time": "time"
    }
    ]
    ```



## <a name="vm-instance-view"></a>VM 인스턴스 보기

인스턴스 보기 API는 VM 실행 상태 정보를 제공합니다. 자세한 내용은 [Virtual Machines - 인스턴스 보기](https://docs.microsoft.com/rest/api/compute/virtualmachines/instanceview) API 설명서를 참조하세요.

Azure 리소스 탐색기는 VM 실행 상태를 보기 위한 간단한 UI를 제공합니다. [리소스 탐색기](https://resources.azure.com/)

프로비전 상태는 VM 속성 및 인스턴스 보기에 표시됩니다. 전원 상태는 VM의 인스턴스 보기에서 사용할 수 있습니다.

구독에 있는 모든 Vm의 전원 상태를 검색 하려면 매개 변수 **Statusonly** 를 사용 하 여 [모든 API Virtual Machines 목록](https://docs.microsoft.com/rest/api/compute/virtualmachines/listall) 에서 *true*로 설정 합니다.

## <a name="next-steps"></a>다음 단계

VM 모니터링에 대 한 자세한 내용은 [Azure에서 virtual Machines 모니터링](../azure-monitor/insights/monitor-vm-azure.md)을 참조 하세요.