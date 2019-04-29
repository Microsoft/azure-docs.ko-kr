---
title: 포함 파일
description: 포함 파일
services: virtual-machines
author: shandilvarun
ms.service: virtual-machines
ms.topic: include
ms.date: 08/09/2018
ms.author: vashan, cynthn, rajsqr
ms.custom: include file
ms.openlocfilehash: 57f557a812ec5e4eea75b76ca1394ca360a85d30
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60419995"
---
Azure VM(Virtual Machines)은 *프로비전* 및 *전원* 상태로 분류될 수 있는 여러 상태를 거칩니다. 이 아티클은 이러한 상태를 설명하고, 고객에게 인스턴스 사용량에 요금이 청구된 경우 특히 강조 표시하려고 합니다. 

## <a name="power-states"></a>전원 상태

전원 상태는 VM의 마지막 알려진 상태를 나타냅니다.

![VM 전원 상태 다이어그램](./media/virtual-machines-common-states-lifecycle/vm-power-states.png)

<br>
다음 표에서는 각 인스턴스 상태를 설명하고 인스턴스 사용량에 대해 요금이 청구되는지 여부를 나타냅니다.

<table>
<tr>
<th>
시스템 상태
</th>
<th>
설명
</th>
<th>
인스턴스 사용량 청구
</th>
</tr>
<tr>
<td>
<p><b>시작 중</b></p>
</td>
<td>
<p>VM이 시작되고 있습니다.</p>
<code>"statuses": [<br>
   {<br>
      "code": "PowerState/starting",<br>
       "level": "Info",<br>
        "displayStatus": "VM starting"<br>
    }<br>
    ]</code><br>
</td>
<td>
<p><b>청구되지 않음</b></p>
</td>
</tr>
<tr>
<td>
<p><b>실행 중</b></p>
</td>
<td>
<p>VM의 정상적인 작동 상태</p>
<code>"statuses": [<br>
 {<br>
 "code": "PowerState/running",<br>
 "level": "Info",<br>
 "displayStatus": "VM running"<br>
 }<br>
 ]</code><br>
</td>
<td>
<p><b>청구됨</b></p>
</td>
</tr>
<tr>
<td>
<p><b>중지 중</b></p>
</td>
<td>
<p>전환 상태입니다. 작업이 완료되면 **중지됨**으로 표시됩니다.</p>
<code>"statuses": [<br>
 {<br>
 "code": "PowerState/stopping",<br>
 "level": "Info",<br>
 "displayStatus": "VM stopping"<br>
 }<br>
 ]</code><br>
</td>
<td>
<p><b>청구됨</b></p>
</td>
</tr>
<tr>
<td>
<p><b>중지</b></p>
</td>
<td>
<p>게스트 OS 내에서 또는 PowerOff API를 사용하여 VM이 종료되었습니다.</p>
<p>하드웨어가 여전히 VM에 할당되고 호스트에서 유지됩니다. </p>
<code>"statuses": [<br>
 {<br>
 "code": "PowerState/stopped",<br>
 "level": "Info",<br>
 "displayStatus": "VM stopped"<br>
 }<br>
 ]</code><br>
</td>
<td>
<p><b>청구됨&#42;</b></p>
</td>
</tr>
<tr>
<td>
<p><b>할당 취소 중</b></p>
</td>
<td>
<p>전환 상태입니다. 작업이 완료되면 VM은 **할당 취소됨**.</p>
<code>"statuses": [<br>
 {<br>
 "code": "PowerState/deallocating",<br>
 "level": "Info",<br>
 "displayStatus": "VM deallocating"<br>
 }<br>
 ]</code><br>
</td>
<td>
<p><b>청구되지 않음</b></p>
</td>
</tr>
<tr>
<td>
<p><b>할당 취소됨</b></p>
</td>
<td>
<p>VM이 성공적으로 중지되고 호스트에서 제거됩니다. </p>
<code>"statuses": [<br>
 {<br>
 "code": "PowerState/deallocated",<br>
 "level": "Info",<br>
 "displayStatus": "VM deallocated"<br>
 }<br>
 ]</code><br>
</td>
<td>
<p><b>청구되지 않음</b></p>
</td>
</tr>
</tbody>
</table>


&#42;일부 Azure 리소스(예: 디스크 및 네트워킹)에는 요금이 부과됩니다. 인스턴스의 소프트웨어 라이선스에는 요금이 부과되지 않습니다.

## <a name="provisioning-states"></a>프로비전 상태

프로비전 상태는 VM에서 사용자가 시작한 제어 평면 작업의 상태입니다. 이러한 상태는 VM의 전원 상태와 별개입니다.

- **만들기** - VM 생성입니다.

- **업데이트** – 기존 VM에 대한 모델을 업데이트합니다. 또한 시작/다시 시작과 같은 VM에 일부 모델이 아닌 변경 내용이 업데이트 대상입니다.

- **삭제** – VM 삭제입니다.

- **할당 취소** – VM이 중지되고 호스트에서 제거되는 위치입니다. VM의 할당이 취소되면 업데이트라고 간주되므로 업데이트와 관련된 프로비전 상태를 표시합니다.



플랫폼이 사용자 시작 작업을 허용한 후에 전환 작업 상태는 다음과 같습니다.

<br>

<table>
<tbody>
<tr>
<td width="162">
<p><b>상태</b></p>
</td>
<td width="366">
<p>설명</p>
</td>
</tr>
<tr>
<td width="162">
<p><b>만드는 중</b></p>
</td>
<td width="366">
<code>"statuses": [<br>
 {<br>
 "code": "ProvisioningState/creating",<br>
 "level": "Info",<br>
 "displayStatus": "Creating"<br>
 }</code><br>
</td>
</tr>
<tr>
<td width="162">
<p><b>업데이트 중</b></p>
</td>
<td width="366">
<code>"statuses": [<br>
 {<br>
 "code": "ProvisioningState/updating",<br>
 "level": "Info",<br>
 "displayStatus": "Updating"<br>
 }<br>
 ]</code><br>
</td>
</tr>
<tr>
<td width="162">
<p><b>삭제 중</b></p>
</td>
<td width="366">
<code>"statuses": [<br>
 {<br>
 "code": "ProvisioningState/deleting",<br>
 "level": "Info",<br>
 "displayStatus": "Deleting"<br>
 }<br>
 ]</code><br>
</td>
</tr>
<tr>
<td width="162">
<p><b>OS 프로비전 상태</b></p>
</td>
<td width="366">
<p>특수화된 이미지가 아닌 OS 이미지를 사용하여 VM을 만든 경우 다음 하위 상태를 관찰할 수 있습니다.</p>
<p>1. <b>OSProvisioningInprogress</b> &ndash; VM이 실행되고 있으며 게스트 OS의 설치가 진행 중입니다. <p /> 
<code> "statuses": [<br>
 {<br>
 "code": "ProvisioningState/creating/OSProvisioningInprogress",<br>
 "level": "Info",<br>
 "displayStatus": "OS Provisioning In progress"<br>
 }<br>
]</code><br>
<p>2. <b>OSProvisioningComplete</b> &ndash; 일시적인 상태입니다. 확장을 설치할 필요가 없으면 VM은 **성공**으로 신속하게 전환됩니다. 확장을 설치하는 데 시간이 걸릴 수 있습니다. <br />
<code> "statuses": [<br>
 {<br>
 "code": "ProvisioningState/creating/OSProvisioningComplete",<br>
 "level": "Info",<br>
 "displayStatus": "OS Provisioning Complete"<br>
 }<br>
]</code><br>
<p><b>참고</b>: OS 오류가 발생하지 않거나 OS를 시간 내에 설치하지 않으면 OS 프로비전은 **실패됨**으로 전환될 수 있습니다. 인프라에 배포된 VM에 대해 고객에게 비용이 청구됩니다.</p>
</td>
</tr>
</table>


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

