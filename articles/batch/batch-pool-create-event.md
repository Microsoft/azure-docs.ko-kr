---
title: Azure Batch 풀 만들기 이벤트
description: 풀을 만든 후에 내보내는 Batch 풀 만들기 이벤트에 대한 참조입니다. 로그의 내용은 풀에 대한 일반 정보를 노출합니다.
services: batch
author: LauraBrenner
manager: evansma
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/20/2017
ms.author: labrenne
ms.openlocfilehash: dea025b274278aa5fed2900c95b4a274541ffef9
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77022192"
---
# <a name="pool-create-event"></a>풀 만들기 이벤트

 이 이벤트는 풀이 생성되면 내보내집니다. 로그의 내용은 풀에 대한 일반 정보를 노출합니다. 풀의 대상 크기가 0 컴퓨팅 노드보다 큰 경우 이 이벤트 직 후 풀 크기 조정 시작 이벤트가 수행됩니다.

 다음 예에서는 `CloudServiceConfiguration` 속성을 사용 하 여 만든 풀의 풀 만들기 이벤트 본문을 보여 줍니다.

```
{
    "id": "myPool1",
    "displayName": "Production Pool",
    "vmSize": "Standard_F1s",
    "imageType": "VirtualMachineConfiguration",
    "cloudServiceConfiguration": {
        "osFamily": "3",
        "targetOsVersion": "*"
    },
    "networkConfiguration": {
        "subnetId": " "
    },
    "virtualMachineConfiguration": {
          "imageReference": {
            "publisher": " ",
            "offer": " ",
            "sku": " ",
            "version": " "
          },
          "nodeAgentId": " "
        },
    "resizeTimeout": "300000",
    "targetDedicatedNodes": 2,
    "targetLowPriorityNodes": 2,
    "maxTasksPerNode": 1,
    "vmFillType": "Spread",
    "enableAutoScale": false,
    "enableInterNodeCommunication": false,
    "isAutoPool": false
}
```

|요소|유형|메모|
|-------------|----------|-----------|
|`id`|String|풀의 ID입니다.|
|`displayName`|String|풀의 표시 이름입니다.|
|`vmSize`|String|풀에 포함된 가상 머신의 크기입니다. 풀에 포함된 모든 가상 머신의 크기는 같습니다. <br/><br/> Cloud Services 풀(cloudServiceConfiguration을 사용하여 만든 풀)에 사용 가능한 가상 머신 크기에 대한 자세한 내용은 [Cloud Services에 적합한 크기](https://azure.microsoft.com/documentation/articles/cloud-services-sizes-specs/)를 참조하세요. Batch는 `ExtraSmall`을 제외한 모든 Cloud Services VM 크기를 지원합니다.<br/><br/> Virtual Machines Marketplace(virtualMachineConfiguration을 사용하여 만든 풀)의 이미지를 사용한 풀에 사용 가능한 VM 크기에 대한 자세한 내용은 [Virtual Machines에 적합한 크기](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-sizes/)(Linux) 또는 [Virtual Machines에 적합한 크기](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-sizes/)(Windows)를 참조하세요. 배치는 `STANDARD_A0`및 Premium Storage(`STANDARD_GS`, `STANDARD_DS` 및 `STANDARD_DSV2` 시리즈) 크기를 제외한 모든 Azure VM 크기를 지원합니다.|
|`imageType`|String|이미지에 대한 배포 방법입니다. 지원 되는 값은 `virtualMachineConfiguration` 또는 `cloudServiceConfiguration`|
|[`cloudServiceConfiguration`](#bk_csconf)|복합 형식|풀의 클라우드 서비스 구성입니다.|
|[`virtualMachineConfiguration`](#bk_vmconf)|복합 형식|풀의 가상 머신 구성입니다.|
|[`networkConfiguration`](#bk_netconf)|복합 형식|풀의 네트워크 구성입니다.|
|`resizeTimeout`|시간|풀에서 마지막 크기 조정 작업에 지정된 풀에 컴퓨팅 노드를 할당하는 데 정해진 제한 시간입니다.  (풀을 만들 때 초기 크기 조정은 크기 조정으로 간주됩니다.)|
|`targetDedicatedNodes`|Int32|풀에 대해 요청 되는 전용 계산 노드 수입니다.|
|`targetLowPriorityNodes`|Int32|풀에 대해 요청 된 우선 순위가 낮은 계산 노드 수입니다.|
|`enableAutoScale`|Bool|풀 크기가 시간이 지남에 따라 자동으로 조정되는지 여부를 지정합니다.|
|`enableInterNodeCommunication`|Bool|풀에 노드 간 직접 통신이 설정되어 있는지 여부를 지정합니다.|
|`isAutoPool`|Bool|풀이 작업의 자동 풀 메커니즘을 통해 만들어졌는지 여부를 지정합니다.|
|`maxTasksPerNode`|Int32|풀의 단일 컴퓨팅 노드에서 동시에 실행할 수 있는 최대 태스크 수입니다.|
|`vmFillType`|String|Batch 서비스는 풀의 컴퓨팅 노드 간에 태스크를 분배하는 방법을 정의합니다. 유효한 값은 Spread 또는 Pack입니다.|

###  <a name="bk_csconf"></a> cloudServiceConfiguration

|요소 이름|유형|메모|
|------------------|----------|-----------|
|`osFamily`|String|풀의 가상 머신에 설치할 Azure 게스트 OS 제품군입니다.<br /><br /> 가능한 값은 다음과 같습니다.<br /><br /> **2** – OS 제품군 2이며, Windows Server 2008 R2 SP1에 해당합니다.<br /><br /> **3** – OS 제품군 3이며, Windows Server 2012에 해당합니다.<br /><br /> **4** – OS 제품군 4이며, Windows Server 2012 R2에 해당합니다.<br /><br /> 자세한 내용은 [Azure 게스트 OS 릴리스](https://azure.microsoft.com/documentation/articles/cloud-services-guestos-update-matrix/#releases)를 참조하세요.|
|`targetOSVersion`|String|풀의 가상 머신에 설치할 Azure 게스트 OS 버전입니다.<br /><br /> 기본값은 지정된 제품군의 최신 운영 체제 버전을 지정하는 **\*** 입니다.<br /><br /> 기타 허용된 값은 [Azure 게스트 OS 릴리스](https://azure.microsoft.com/documentation/articles/cloud-services-guestos-update-matrix/#releases)를 참조하세요.|

###  <a name="bk_vmconf"></a> virtualMachineConfiguration

|요소 이름|유형|메모|
|------------------|----------|-----------|
|[`imageReference`](#bk_imgref)|복합 형식|사용할 플랫폼 또는 Marketplace 이미지에 대한 정보를 지정합니다.|
|`nodeAgentId`|String|컴퓨팅 노드에서 프로비전되는 Batch 노드 에이전트의 SKU입니다.|
|[`windowsConfiguration`](#bk_winconf)|복합 형식|가상 머신의 Windows 운영 체제 설정을 지정합니다. imageReference가 Linux OS 이미지를 참조하는 경우 이 속성을 지정해서는 안 됩니다.|

###  <a name="bk_imgref"></a> imageReference

|요소 이름|유형|메모|
|------------------|----------|-----------|
|`publisher`|String|이미지의 게시자입니다.|
|`offer`|String|이미지 제안입니다.|
|`sku`|String|이미지의 SKU입니다.|
|`version`|String|이미지의 버전입니다.|

###  <a name="bk_winconf"></a> windowsConfiguration

|요소 이름|유형|메모|
|------------------|----------|-----------|
|`enableAutomaticUpdates`|부울|가상 머신에 자동 업데이트가 활성화되었는지 여부를 나타냅니다. 이 속성을 지정하지 않을 경우 기본값은 true입니다.|

###  <a name="bk_netconf"></a> networkConfiguration

|요소 이름|유형|메모|
|------------------|--------------|----------|
|`subnetId`|String|풀의 컴퓨팅 노드가 생성된 서브넷의 리소스 식별자를 지정합니다.|
