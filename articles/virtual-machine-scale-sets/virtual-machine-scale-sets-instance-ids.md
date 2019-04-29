---
title: Azure VM 확장 집합 VM의 인스턴스 ID 이해 | Microsoft Docs
description: Azure VM 확장 집합 VM의 인스턴스 ID 이해
services: virtual-machine-scale-sets
documentationcenter: ''
author: mayanknayar
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: e229664e-ee4e-4f12-9d2e-a4f456989e5d
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2018
ms.author: manayar
ms.openlocfilehash: 6aeba722a0661979664f8d61efdb9b2bf47ad801
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60618503"
---
# <a name="understand-instance-ids-for-azure-vm-scale-set-vms"></a>Azure VM 확장 집합 VM의 인스턴스 ID 이해
이 문서에서는 확장 집합의 인스턴스 ID와 이것이 노출되는 다양한 방식을 설명합니다.

## <a name="scale-set-instance-ids"></a>확장 집합 인스턴스 ID

확장 집합의 VM마다 고유하게 식별하는 인스턴스 ID를 가져옵니다. 인스턴스 ID는 확장 집합에 있는 특정 VM에 작업을 수행하기 위해 확장 집합 API에 사용됩니다. 예를 들어 reimage API를 사용하는 경우 특정 인스턴스 ID를 지정하여 이미지를 다시 만들 수 있습니다.

REST API: `POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/reimage?api-version={apiVersion}`(자세한 내용은 [REST API 설명서](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/reimage) 참조)

Powershell: `Set-AzVmssVM -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName} -InstanceId {instanceId} -Reimage`(자세한 내용은 [Powershell 설명서](https://docs.microsoft.com/powershell/module/az.compute/set-azvmssvm) 참조)

CLI: `az vmss reimage -g {resourceGroupName} -n {vmScaleSetName} --instance-id {instanceId}`(자세한 내용은 [CLI 설명서](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest) 참조)

확장 집합에 있는 모든 인스턴스를 나열하여 인스턴스 ID 목록을 가져올 수 있습니다.

REST API: `GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/virtualMachines?api-version={apiVersion}`(자세한 내용은 [REST API 설명서](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesetvms/list) 참조)

Powershell: `Get-AzVmssVM -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName}`(자세한 내용은 [Powershell 설명서](https://docs.microsoft.com/powershell/module/az.compute/get-azvmssvm) 참조)

CLI: `az vmss list-instances -g {resourceGroupName} -n {vmScaleSetName}`(자세한 내용은 [CLI 설명서](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest) 참조)

[resources.azure.com](https://resources.azure.com) 또는 [Azure SDK](https://azure.microsoft.com/downloads/)를 사용하여 확장 집합의 VM을 나열할 수도 있습니다.

출력의 정확한 표시는 명령에 제공하는 옵션에 따라 달라지지만 CLI의 몇 가지 샘플 출력은 다음과 같습니다.

```
$ az vmss show -g {resourceGroupName} -n {vmScaleSetName}
[
  {
    "instanceId": "85",
    "latestModelApplied": true,
    "location": "westus",
    "name": "nsgvmss_85",
    .
    .
    .
```

보이는 것처럼 "instanceId" 속성은 10진수입니다. 이전 인스턴스가 삭제되면 새 인스턴스에 인스턴스 ID를 다시 사용할 수 있습니다.

>[!NOTE]
> 인스턴스 ID가 확장 집합의 VM에 할당되는 방식에 대한 **보장은 없습니다**. 때때로 순차적으로 증가하는 것으로 보일 수도 있지만, 항상 그렇지는 않습니다. 인스턴스 ID가 VM에 할당되는 특정 방식에 의존하지 마십시오.

## <a name="scale-set-vm-names"></a>확장 집합 VM 이름

위의 샘플 출력에 VM의 “이름”도 있습니다. 이름의 형식은 "{scale-set-name}_{instance-id}"입니다. 이 이름은 확장 집합의 인스턴스를 나열할 때 Azure Portal에 표시되는 이름입니다.

![](./media/virtual-machine-scale-sets-instance-ids/vmssInstances.png)

이름의 {instance-id} 부분은 앞서 얘기한 "instanceId" 속성의 10진수와 동일합니다.

## <a name="instance-metadata-vm-name"></a>인스턴스 메타데이터 VM 이름

확장 집합 VM 내에서 [인스턴스 메타데이터](../virtual-machines/windows/instance-metadata-service.md)를 쿼리하면 출력에 “이름”이 표시됩니다.

```
{
  "compute": {
    "location": "westus",
    "name": "nsgvmss_85",
    .
    .
    .
```

이 이름은 앞에서 설명한 이름과 같습니다.

## <a name="scale-set-vm-computer-name"></a>확장 집합 VM 컴퓨터 이름

확장 집합의 VM마다 컴퓨터 이름이 할당됩니다. 컴퓨터 이름은 [가상 네트워크 내의 Azure 제공 DNS 이름 확인](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)에 있는 VM의 호스트 이름입니다. 이 컴퓨터 이름의 형식은 "{computer-name-prefix}{base-36-instance-id}"입니다.

{base-36-instance-id}는 [base 36](https://en.wikipedia.org/wiki/Base36) 형식이며 길이는 항상 6자리입니다. 숫자의 base 36 표현이 6자리 미만이면 길이를 6자리로 만들기 위해 {base-36-instance-id}가 0으로 채워집니다. 예를 들어 {computer-name-prefix}가 "nsgvmss"이고 인스턴스 ID가 85인 인스턴스의 컴퓨터 이름은 "nsgvmss00002D"입니다.

>[!NOTE]
> 컴퓨터 이름 접두사는 설정이 가능한 확장 집합 모델의 속성이기 때문에 확장 집합 이름 자체와는 다를 수 있습니다.
