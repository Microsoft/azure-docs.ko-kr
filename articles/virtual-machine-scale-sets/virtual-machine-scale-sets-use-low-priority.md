---
title: 우선 순위가 낮은 VM을 사용하는 Azure 확장 집합 만들기(미리 보기) | Microsoft Docs
description: 우선 순위가 낮은 VM을 사용하여 비용을 절약하는 Azure 가상 머신 확장 집합을 만드는 방법을 알아봅니다.
services: virtual-machine-scale-sets
documentationcenter: ''
author: mmccrory
manager: rajraj
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/01/2018
ms.author: memccror
ms.openlocfilehash: 861c68ae8163e0ba8c2af2a3d96153ac3e84855f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60803198"
---
# <a name="low-priority-vms-on-scale-sets-preview"></a>확장 집합에서 우선 순위가 낮은 VM(미리 보기)

확장 집합에서 우선 순위가 낮은 VM을 사용하면 사용되지 않은 용량을 상당한 비용 절약 수단으로 활용할 수 있습니다. Azure에 용량이 다시 필요한 경우 언제든지 Azure 인프라에서 우선 순위가 낮은 VM을 제거합니다. 따라서 우선 순위가 낮은 VM은 일괄 처리 작업, 개발/테스트 환경, 대규모 컴퓨팅 작업 등과 같은 중단을 처리할 수 있는 작업에 매우 적합합니다.

사용 가능한 미사용 용량의 크기는 크기, 지역, 하루 중 시간 등에 따라 달라질 수 있습니다. 우선 순위가 낮은 VM을 확장 집합에 배포할 때 사용 가능한 용량이 있는 경우, Azure에서 VM을 할당하지만 이러한 VM에 대한 SLA는 없습니다. 우선 순위가 낮은 확장 집합은 단일 장애 도메인에 배포되며 고가용성 보장을 제공하지 않습니다.

## <a name="eviction-policy"></a>제거 정책

우선 순위가 낮은 확장 집합을 만들 때 *할당 취소*(기본값) 또는 *삭제*하려면 제거 정책을 설정할 수 있습니다. 

*할당 취소* 정책은 제거된 VM을 할당 취소 중지된 상태로 전환하여 제거된 인스턴스를 다시 배포할 수 있습니다. 그러나 할당이 성공하리라는 보장은 없습니다. 할당 취소된 VM은 확장 집합 인스턴스 할당량에 따라 계산되며 기본 디스크에 대한 요금이 청구됩니다. 

우선 순위가 낮은 확장 집합의 VM을 제거할 때 삭제하려면 *삭제*하도록 제거 정책을 설정할 수 있습니다. 삭제하도록 제거 정책을 설정하면 확장 집합 인스턴스 수 속성을 늘려 새 VM을 만들 수 있습니다. 제거된 VM은 기본 디스크와 함께 삭제되므로 저장소에 대한 요금이 청구되지 않습니다. 확장 집합의 자동 크기 조정 기능을 사용하여 제거된 VM을 자동으로 시도하고 보정할 수 있지만 성공적인 할당을 보장하지는 않습니다. 삭제하도록 제거 정책을 설정하는 경우 디스크 비용을 절약하고 할당량 한도에 도달하지 않도록 방지하기 위해 우선 순위가 낮은 확장 집합에서만 자동 크기 조정 기능을 사용하는 것이 좋습니다. 

> [!NOTE]
> 미리 보기로 있는 동안에는 [Azure Portal](#use-the-azure-portal) 및 [Azure Resource Manager 템플릿](#use-azure-resource-manager-templates)을 사용하여 제거 정책을 설정할 수 있습니다. 

## <a name="deploying-low-priority-vms-on-scale-sets"></a>확장 집합에 우선 순위가 낮은 VM 배포

확장 집합에 우선 순위가 낮은 VM을 배포하려면 새 *Priority* 플래그를 *Low*로 설정할 수 있습니다. 확장 집합의 모든 VM은 낮은 우선 순위로 설정됩니다. 우선 순위가 낮은 VM이 포함된 확장 집합을 만들려면 다음 방법 중 하나를 사용합니다.
- [Azure Portal](#use-the-azure-portal)
- Azure CLI
- [Azure PowerShell](#use-azure-powershell)
- [Azure 리소스 관리자 템플릿](#use-azure-resource-manager-templates)

## <a name="use-the-azure-portal"></a>Azure Portal 사용

우선 순위가 낮은 VM을 사용하는 확장 집합을 만드는 프로세스는 [시작 문서](quick-create-portal.md)에서 자세히 설명한 프로세스와 동일합니다. 확장 집합을 배포할 때 우선 순위가 낮은 플래그 및 제거 정책: ![우선 순위가 낮은 VM으로 확장 집합 만들기](media/virtual-machine-scale-sets-use-low-priority/vmss-low-priority-portal.png)를 설정하도록 선택할 수 있습니다.

## <a name="use-the-azure-cli"></a>Azure CLI 사용

우선 순위가 낮은 VM이 포함된 확장 집합을 만드는 프로세스는 [시작 문서](quick-create-cli.md)에서 자세히 설명한 프로세스와 동일합니다. 아래 예제와 같이 '--Priority' 매개 변수를 CLI 호출에 추가하고 *Low*로 설정하면 됩니다.

```azurecli
az vmss create \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --image UbuntuLTS \
    --upgrade-policy-mode automatic \
    --admin-username azureuser \
    --generate-ssh-keys \
    --priority Low
```

## <a name="use-azure-powershell"></a>Azure PowerShell 사용

우선 순위가 낮은 VM이 포함된 확장 집합을 만드는 프로세스는 [시작 문서](quick-create-powershell.md)에서 자세히 설명한 프로세스와 동일합니다.
아래 예제와 같이 '-Priority' 매개 변수를 [New-AzVmssConfig](/powershell/module/az.compute/new-azvmssconfig)에 추가하고 *Low*로 설정하면 됩니다.

```powershell
$vmssConfig = New-AzVmssConfig `
    -Location "East US 2" `
    -SkuCapacity 2 `
    -SkuName "Standard_DS2" `
    -UpgradePolicyMode Automatic `
    -Priority "Low"
```

## <a name="use-azure-resource-manager-templates"></a>Azure Resource Manager 템플릿 사용

우선 순위가 낮은 VM을 사용하는 확장 집합을 만드는 프로세스는 [Linux](quick-create-template-linux.md) 또는 [Windows](quick-create-template-windows.md)용 시작 문서에서 자세히 설명한 프로세스와 동일합니다. 템플릿의 *Microsoft.Compute/virtualMachineScaleSets/virtualMachineProfile* 리소스 종류에 'priority' 속성을 추가하고 값으로 *Low*를 지정합니다. *2018-03-01* API 버전 이상을 사용해야 합니다. 

삭제하도록 제거 정책을 설정하려면 'evictionPolicy' 매개 변수를 추가하고 *delete*로 설정합니다.

다음 예제에서는 우선 순위가 낮은 *myScaleSet*이라는 Linux 확장 집합을 *West Central US*에 만들어 제거 시 확장 집합에서 VM을 *삭제*합니다.

```json
{
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  "name": "myScaleSet",
  "location": "East US 2",
  "apiVersion": "2018-03-01",
  "sku": {
    "name": "Standard_DS2_v2",
    "capacity": "2"
  },
  "properties": {
    "upgradePolicy": {
      "mode": "Automatic"
    },
    "virtualMachineProfile": {
       "priority": "Low",
       "evictionPolicy": "delete",
       "storageProfile": {
        "osDisk": {
          "caching": "ReadWrite",
          "createOption": "FromImage"
        },
        "imageReference":  {
          "publisher": "Canonical",
          "offer": "UbuntuServer",
          "sku": "16.04-LTS",
          "version": "latest"
        }
      },
      "osProfile": {
        "computerNamePrefix": "myvmss",
        "adminUsername": "azureuser",
        "adminPassword": "P@ssw0rd!"
      }
    }
  }
}
```
## <a name="faq"></a>FAQ

### <a name="can-i-convert-existing-scale-sets-to-low-priority-scale-sets"></a>기존 확장 집합을 우선 순위가 낮은 확장 집합으로 변환할 수 있습니까?
아니요. 우선 순위가 낮은 플래그를 설정하면 만들 때만 지원됩니다.

### <a name="can-i-create-a-scale-set-with-both-regular-vms-and-low-priority-vms"></a>기본 VM 및 우선 순위가 낮은 VM 모두를 사용하여 확장 집합을 만들 수 있습니까?
아니요. 확장 집합은 1 초과 우선 순위 형식을 지원할 수 없습니다.

### <a name="how-is-quota-managed-for-low-priority-vms"></a>우선 순위가 낮은 VM에 대해 할당량은 어떻게 관리됩니까?
우선 순위가 낮은 VM 및 일반 VM은 동일한 할당량 풀을 공유합니다. 

### <a name="can-i-use-autoscale-with-low-priority-scale-sets"></a>우선 순위가 낮은 확장 집합으로 자동 크기 조정을 사용할 수 있습니까?
예. 우선 순위가 낮은 확장 집합에서 자동 크기 조정 규칙을 설정할 수 있습니다. VM이 제거된 경우 새 우선 순위가 낮은 VM을 만들기 위해 자동 크기 조정을 시도할 수 있습니다. 그렇지만 이 용량은 보장되지 않습니다. 

### <a name="does-autoscale-work-with-both-eviction-policies-deallocate-and-delete"></a>두 제거 정책(할당 취소 및 삭제)으로 자동 크기 조정이 작동합니까?
자동 크기 조정을 사용하는 경우 제거 정책을 삭제하도록 설정하는 것이 좋습니다. 할당 취소된 인스턴스가 확장 집합에서 용량 수에 따라 계산되기 때문입니다. 자동 크기 조정을 사용할 때 할당 취소되고 제거된 인스턴스로 인해 신속하게 대상 인스턴스 수를 달성할 수 있을 것 같습니다. 

## <a name="next-steps"></a>다음 단계
우선 순위가 낮은 VM이 포함된 확장 집합을 만들었으므로 [낮은 우선 순위를 사용하는 자동 크기 조정 템플릿](https://github.com/Azure/vm-scale-sets/tree/master/preview/lowpri)을 배포합니다.

가격 책정에 대한 자세한 내용은 [가상 머신 확장 집합 가격 페이지](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/linux/)를 확인하세요.
