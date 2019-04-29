---
title: 가용성 영역을 사용하는 Azure 확장 집합 만들기 | Microsoft Docs
description: 가동 중단에 대비해서 중복성을 늘리기 위해 가용성 영역을 사용하는 Azure 가상 머신 확장 집합을 만드는 방법을 알아봅니다.
services: virtual-machine-scale-sets
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm
ms.devlang: na
ms.topic: article
ms.date: 08/08/2018
ms.author: cynthn
ms.openlocfilehash: 7fa903f65a6c7d244ff424eae4a0def258b50bbc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60803276"
---
# <a name="create-a-virtual-machine-scale-set-that-uses-availability-zones"></a>가용성 영역을 사용하는 가상 머신 확장 집합 만들기

데이터 센터 수준 오류로부터 가상 머신 확장 집합을 보호하려면 가용성 영역에서 확장 집합을 만들 수 있습니다. 가용성 영역을 지원하는 Azure 지역은 각각 독립적인 전원, 네트워크 및 냉각 기능을 갖춘 3개 이상의 별도 지역을 포함합니다. 자세한 내용은 [가용성 영역 개요](../availability-zones/az-overview.md)를 참조하세요.

## <a name="availability-considerations"></a>가용성 고려 사항

API 버전 *2017-12-01*부터 확장 집합을 하나 이상의 영역으로 배포하는 경우 "최대 분산" 또는 "정적 5 장애 도메인 분산"을 사용하여 배포할 수 있습니다. 최대 확산을 사용하여 확장 집합은 각 영역 내에서 가능한 많은 장애 도메인에서 VM을 분산합니다. 이 분산은 영역당 5개의 장애 도메인보다 크거나 작을 수 있습니다. "정적 5 장애 도메인 확산"을 사용하면 확장 집합은 영역당 정확히 5개의 장애 도메인에 VM을 분산합니다. 확장 집합이 할당 요청을 충족하는 영역당 5개의 개별 장애 도메인을 찾을 수 없으므로 요청이 실패합니다.

이러한 방식은 대부분의 경우 최적의 분산을 제공하므로 **대부분의 워크로드에 대해 최대 분산을 사용하여 배포하는 것이 좋습니다**. 별개의 하드웨어 격리 단위에 복제본을 분산시켜야 하는 경우 가용성 영역에 전체적으로 분산시키고 각 영역 내에서 최대 분산을 활용하는 것이 좋습니다.

최대 분산을 사용하면 VM이 분산되어 있는 장애 도메인의 수에 상관없이 확장 집합 VM 인스턴스 뷰 및 인스턴스 메타데이터에 하나의 장애 도메인만 표시됩니다. 각 영역 내 분산은 암시적입니다.

최대 분산을 사용하려면 *platformFaultDomainCount*를 *1*로 설정합니다. 정적 5 장애 도메인 분산을 사용하려면 *platformFaultDomainCount*를 *5*로 설정합니다. API 버전 *2017-12-01*에서 *platformFaultDomainCount*는 단일 영역 및 영역 간 확장 집합에 대한 기본값이 *1*로 설정됩니다. 현재 지역 확장 집합에 대해서는 정적 5 장애 도메인 분산만 지원됩니다.

### <a name="placement-groups"></a>배치 그룹

확장 집합을 배포할 때 가용성 영역당 하나의 [배치 그룹](./virtual-machine-scale-sets-placement-groups.md)으로 배포하거나 영역마다 여러 개의 배치 그룹으로 배포하는 옵션도 있습니다. 지역 확장 집합의 경우 지역에 단일 배치 그룹을 두거나 해당 지역에 다수의 배치 그룹을 두는 것입니다. 대부분 워크로드의 경우 더 큰 규모가 가능하도록 여러 배치 그룹을 사용하는 것이 좋습니다. API 버전 *2017-12-01*에서 확장 집합은 단일 영역 및 영역 간 확장 집합에 대해 여러 배치 그룹을 기본값으로 설정하지만 지역 확장 집합에 대해 단일 배치 그룹을 기본값으로 설정합니다.

> [!NOTE]
> 최대 분산을 사용하는 경우 여러 배치 그룹을 사용해야 합니다.

### <a name="zone-balancing"></a>영역 균형

마지막으로 여러 영역에서 배포되는 확장 집합의 경우 "최상의 노력 영역 균형" 또는 "엄격한 영역 균형"을 선택하는 옵션을 갖습니다. 확장 집합은 각 영역에 동일한 수의 VM이 있거나 확장 집합의 다른 모든 영역에서 VM이 +\\- 1이면 "균형"으로 간주됩니다. 예를 들면 다음과 같습니다.

- 영역 1에 2개의 VM, 영역 2에 3개의 VM, 영역 3에 3개의 VM이 있는 확장 집합은 균형으로 간주됩니다. VM 수가 다른 영역은 하나 뿐이며 다른 영역보다 1개만 적습니다. 
- 영역 1에 1개의 VM, 영역 2에 3개의 VM, 영역 3에 3개의 VM이 있는 확장 집합은 불균형으로 간주됩니다. 영역 1에는 영역 2 및 3보다 VM이 2개 적게 있습니다.

확장 집합에서 VM이 만들어졌어도 해당 VM에서 확장은 배포되지 않을 수 있습니다. 확장이 실패한 이러한 VM은 확장 집합이 분산된 경우를 결정할 때에 여전히 계산됩니다. 예를 들어 영역 1에 3개의 VM, 영역 2에 3개의 VM, 영역 3에 3개의 VM이 있는 확장 집합은 모든 확장이 영역 1에서 실패하고 모든 확장이 영역 2 및 3에서 성공한 경우에도 균형으로 간주됩니다.

최상의 영역 균형을 사용하면 확장 집합은 균형을 유지하면서 규모 확장 및 감축을 시도합니다. 그러나 어떤 이유로든 이것이 불가능한 경우(예: 하나의 영역이 다운되어 확장 집합에서 해당 영역에 새 VM을 만들 수 없는 경우) 확장 집합을 사용하면 일시적인 불균형을 성공적으로 확장 또는 감축할 수 있습니다. 후속 스케일 아웃 시도에서 확장 집합은 확장 집합이 균형을 이루기 위해 VM이 더 많이 필요한 영역에 VM을 추가합니다. 마찬가지로 후속 규모 축소 시도에서 확장 집합은 확장 집합이 균형을 이루기 위해 더 적은 VM이 필요한 영역에서 VM을 제거합니다. "엄격한 영역 균형"을 사용하면 확장 집합은 규모 확장 또는 축소 시도에 실패하고 그렇게 하는 경우 불균형이 발생합니다.

최상의 영역 균형을 사용하려면 *zoneBalance*를 *false*로 설정합니다. 이 설정은 API 버전 *2017-12-01*에서 기본값입니다. 엄격한 영역 균형을 사용하려면 *zoneBalance*를 *true*로 설정합니다.

## <a name="single-zone-and-zone-redundant-scale-sets"></a>단일 영역 및 영역 중복 확장 집합

가상 머신 확장 집합을 배포하면 지역에서 단일 가용성 영역 또는 여러 영역을 사용할 수 있습니다.

단일 영역에서 확장 집합을 만들 경우 해당 VM 인스턴스가 모두 실행되는 영역을 제어하고 해당 영역 내에서만 확장 집합을 관리하고 확장 집합의 크기를 자동 조정합니다. 영역 중복 확장 집합을 사용하여 여러 영역에 걸쳐 있는 단일 확장 집합을 만들 수 있습니다. VM 인스턴스를 만들 때 기본적으로 영역에 균등하게 배포됩니다. 영역 중 하나가 중단되어야 확장 집합이 용량이 증가하도록 자동으로 확장되지 않습니다. 가장 좋은 방법은 CPU 또는 메모리 사용량을 기반으로 자동 크기 조정 규칙을 구성하는 것입니다. 확장 집합은 자동 크기 조정 규칙을 통해 다른 작업 영역에서 새 인스턴스를 확장하여 한 영역의 VM 인스턴스 손실에 대응할 수 있습니다.

가용성 영역을 사용하려면 [지원되는 Azure 지역](../availability-zones/az-overview.md#services-support-by-region)에 확장 집합을 만들어야 합니다. 다음 방법 중 하나를 사용하여 가용성 영역을 사용하는 확장 집합을 만들 수 있습니다.

- [Azure Portal](#use-the-azure-portal)
- Azure CLI
- [Azure PowerShell](#use-azure-powershell)
- [Azure 리소스 관리자 템플릿](#use-azure-resource-manager-templates)

## <a name="use-the-azure-portal"></a>Azure Portal 사용

가용성 영역을 사용하는 확장 집합을 만드는 프로세스는 [시작 문서](quick-create-portal.md)에 자세히 설명된 프로세스와 같습니다. 지원되는 Azure 지역을 선택할 때 다음 예제에 표시된 대로 하나 이상의 사용 가능한 지역에 확장 집합을 만들 수 있습니다.

![단일 가용성 영역에서 확장 집합 만들기](media/virtual-machine-scale-sets-use-availability-zones/vmss-az-portal.png)

확장 집합과 Azure Load Balancer 및 공용 IP 주소와 같은 지원 리소스가 지정된 단일 지역에서 만들어집니다.

## <a name="use-the-azure-cli"></a>Azure CLI 사용

가용성 영역을 사용하는 확장 집합을 만드는 프로세스는 [시작 문서](quick-create-cli.md)에 자세히 설명된 프로세스와 같습니다. 가용성 영역을 사용하려면 지원되는 Azure 지역에 확장 집합을 만들어야 합니다.

`--zones` 매개 변수를 [az vmss create](/cli/azure/vmss) 명령에 추가하고, 사용할 지역(예: 지역 *1*, *2* 또는 *3*)을 지정합니다. 다음 예제에서는 영역 *1*에 *myScaleSet*이라는 단일 영역 확장 집합을 만듭니다.

```azurecli
az vmss create \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --image UbuntuLTS \
    --upgrade-policy-mode automatic \
    --admin-username azureuser \
    --generate-ssh-keys \
    --zones 1
```

단일 영역 확장 집합 및 네트워크 리소스의 전체 예제는 [이 샘플 CLI 스크립트](https://github.com/Azure/azure-docs-cli-python-samples/blob/master/virtual-machine-scale-sets/create-single-availability-zone/create-single-availability-zone.sh)를 참조하세요.

### <a name="zone-redundant-scale-set"></a>영역 중복 확장 집합

영역 중복 확장 집합을 만들려면 *표준* SKU 공용 IP 주소 및 부하 분산 장치를 사용합니다. 향상된 중복성을 위해 *표준* SKU는 영역 중복 네트워크 리소스를 만듭니다. 자세한 내용은 [Azure Load Balancer 표준 개요](../load-balancer/load-balancer-standard-overview.md) 및 [표준 Load Balancer 및 가용성 영역](../load-balancer/load-balancer-standard-availability-zones.md)을 참조하세요.

영역 중복 확장 집합을 만들려면 여러 영역을 `--zones` 매개 변수로 지정합니다. 다음 예제에서는 *1,2,3* 영역에 *myScaleSet*라는 영역 중복 확장 집합을 만듭니다.

```azurecli
az vmss create \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --image UbuntuLTS \
    --upgrade-policy-mode automatic \
    --admin-username azureuser \
    --generate-ssh-keys \
    --zones 1 2 3
```

지정한 영역에 확장 집합 리소스와 VM을 모두 만들고 구성하는 데 몇 분 정도 걸립니다. 영역 중복 확장 집합 및 네트워크 리소스의 전체 예제는 [이 샘플 CLI 스크립트](https://github.com/Azure/azure-docs-cli-python-samples/blob/master/virtual-machine-scale-sets/create-zone-redundant-scale-set/create-zone-redundant-scale-set.sh)를 참조하세요.

## <a name="use-azure-powershell"></a>Azure PowerShell 사용

가용성 영역을 사용하려면 지원되는 Azure 지역에 확장 집합을 만들어야 합니다. `-Zone` 매개 변수를 [New-AzVmssConfig](/powershell/module/az.compute/new-azvmssconfig) 명령에 추가하고, 사용할 지역(예: 영역 *1*, *2* 또는 *3*)을 지정합니다.

다음 예제에서는 *미국 동부 2* 영역 *1*에 *myScaleSet*이라는 단일 영역 확장 집합을 만듭니다. 가상 네트워크, 공용 IP 주소 및 부하 분산 장치에 대한 Azure 네트워크 리소스가 자동으로 만들어집니다. 메시지가 표시되면 확장 집합에서 VM 인스턴스에 대해 원하는 관리 자격 증명을 제공합니다.

```powershell
New-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -Location "EastUS2" `
  -VMScaleSetName "myScaleSet" `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -PublicIpAddressName "myPublicIPAddress" `
  -LoadBalancerName "myLoadBalancer" `
  -UpgradePolicy "Automatic" `
  -Zone "1"
```

### <a name="zone-redundant-scale-set"></a>영역 중복 확장 집합

영역 중복 확장 집합을 만들려면 여러 영역을 `-Zone` 매개 변수로 지정합니다. 다음 예제에서는 *미국 동부 2* 영역 *1, 2, 3*에 *myScaleSet*이라는 영역 중복 확장 집합을 만듭니다. 가상 네트워크, 공용 IP 주소 및 부하 분산 장치에 대한 영역 중복 Azure 네트워크 리소스가 자동으로 만들어집니다. 메시지가 표시되면 확장 집합에서 VM 인스턴스에 대해 원하는 관리 자격 증명을 제공합니다.

```powershell
New-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -Location "EastUS2" `
  -VMScaleSetName "myScaleSet" `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -PublicIpAddressName "myPublicIPAddress" `
  -LoadBalancerName "myLoadBalancer" `
  -UpgradePolicy "Automatic" `
  -Zone "1", "2", "3"
```

## <a name="use-azure-resource-manager-templates"></a>Azure 리소스 관리자 템플릿 사용

가용성 영역을 사용하는 확장 집합을 만드는 프로세스는 [Linux](quick-create-template-linux.md) 또는 [Windows](quick-create-template-windows.md)용 시작 문서에 자세히 설명된 프로세스와 같습니다. 가용성 영역을 사용하려면 지원되는 Azure 지역에 확장 집합을 만들어야 합니다. `zones` 속성을 템플릿의 *Microsoft.Compute/virtualMachineScaleSets* 리소스 종류에 추가하고 사용할 지역(예: 지역 *1*, *2* 또는 *3*)을 지정합니다.

다음 예제에서는 *미국 동부 2* 영역 *1*에 *myScaleSet*이라는 Linux 단일 영역 확장 집합을 만듭니다.

```json
{
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  "name": "myScaleSet",
  "location": "East US 2",
  "apiVersion": "2017-12-01",
  "zones": ["1"],
  "sku": {
    "name": "Standard_A1",
    "capacity": "2"
  },
  "properties": {
    "upgradePolicy": {
      "mode": "Automatic"
    },
    "virtualMachineProfile": {
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

단일 영역 확장 집합 및 네트워크 리소스의 전체 예제는 [이 샘플 Resource Manager 템플릿](https://github.com/Azure/vm-scale-sets/blob/master/preview/zones/singlezone.json)을 참조하세요.

### <a name="zone-redundant-scale-set"></a>영역 중복 확장 집합

영역 중복 확장 집합을 만들려면 *Microsoft.Compute/virtualMachineScaleSets* 리소스 종류에 대한 `zones` 속성에 여러 값을 지정합니다. 다음 예제에서는 *미국 동부 2* 영역 *1,2,3*에 *myScaleSet*라는 영역 중복 확장 집합을 만듭니다.

```json
{
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  "name": "myScaleSet",
  "location": "East US 2",
  "apiVersion": "2017-12-01",
  "zones": [
        "1",
        "2",
        "3"
      ]
}
```

공용 IP 주소 또는 부하 분산 장치를 만드는 경우 *"sku": { "name": "Standard" }"* 속성을 지정하여 영역 중복 네트워크 리소스를 만듭니다. 또한 모든 트래픽을 허용하는 네트워크 보안 그룹 및 규칙을 만들어야 합니다. 자세한 내용은 [Azure Load Balancer 표준 개요](../load-balancer/load-balancer-standard-overview.md) 및 [표준 Load Balancer 및 가용성 영역](../load-balancer/load-balancer-standard-availability-zones.md)을 참조하세요.

영역 중복 확장 집합 및 네트워크 리소스의 전체 예제는 [이 샘플 Resource Manager 템플릿](https://github.com/Azure/vm-scale-sets/blob/master/preview/zones/multizone.json)을 참조하세요.

## <a name="next-steps"></a>다음 단계

지금까지 가용성 영역에서 확장 집합을 만들었으므로 [가상 머신 확장 집합에 애플리케이션 배포](tutorial-install-apps-cli.md) 또는 [가상 머신 확장 집합에 자동 크기 조정 사용](tutorial-autoscale-cli.md) 방법을 알아볼 수 있습니다.
