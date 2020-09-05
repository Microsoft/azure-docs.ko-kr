---
title: Azure 스폿 Vm을 사용 하는 확장 집합 만들기
description: 집중 Vm을 사용 하 여 비용을 절약 하는 Azure 가상 머신 확장 집합을 만드는 방법을 알아봅니다.
author: cynthn
ms.author: cynthn
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: spot
ms.date: 03/25/2020
ms.reviewer: jagaveer
ms.custom: jagaveer, devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: b5888000028ba87d503bb0bc690aad6628a51a37
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89072743"
---
# <a name="azure-spot-vms-for-virtual-machine-scale-sets"></a>가상 머신 확장 집합에 대 한 Azure 스폿 Vm 

확장 집합에서 Azure 지점을 사용 하면 비용을 크게 절약할 수 있는 사용 되지 않는 용량을 활용할 수 있습니다. Azure에서 용량을 다시 필요로 하는 모든 시점에서 Azure 인프라는 스폿 인스턴스를 제거 합니다. 따라서 지점 인스턴스는 일괄 처리 작업, 개발/테스트 환경, 대규모 계산 워크 로드 등의 중단을 처리할 수 있는 워크 로드에 적합 합니다.

사용 가능한 용량의 크기는 크기, 지역, 시간 등에 따라 달라질 수 있습니다. 확장 집합에 별색 인스턴스를 배포 하는 경우 Azure는 사용 가능한 용량이 있는 경우에만 인스턴스를 할당 하지만 이러한 인스턴스에 대 한 SLA는 없습니다. 별색 확장 집합은 단일 장애 도메인에 배포 되며 고가용성을 보장 하지 않습니다.


## <a name="pricing"></a>가격 책정

지점 인스턴스의 가격은 지역 및 SKU를 기준으로 하는 변수입니다. 자세한 내용은 [Linux](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/linux/) 및 [Windows](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/windows/)가격 책정을 참조 하세요. 


가변 가격 책정을 사용 하는 경우 최대 5 개의 소수 자릿수를 사용 하 여 미국 달러 (USD)로 최대 가격을 설정 하는 옵션을 사용할 수 있습니다. 예를 들어 값은 `0.98765` 시간당 $0.98765 USD의 최대 가격이 됩니다. 최대 가격을로 설정 하면 `-1` 인스턴스는 가격에 따라 제거 되지 않습니다. 인스턴스의 가격은 사용 가능한 용량 및 할당량을 초과 하는 경우 더 작은 표준 인스턴스의 현재 가격 또는 가격입니다.

## <a name="eviction-policy"></a>제거 정책

별색 확장 집합을 만들 때 제거 정책을 *할당* 취소 (기본값) 또는 *삭제*로 설정할 수 있습니다. 

*할당* 취소 정책은 제거 된 인스턴스를 제거 된 인스턴스를 다시 배포할 수 있도록 중지-할당 취소 된 상태로 이동 합니다. 그러나 할당이 성공하리라는 보장은 없습니다. 할당 취소된 VM은 확장 집합 인스턴스 할당량에 따라 계산되며 기본 디스크에 대한 요금이 청구됩니다. 

별색 확장 집합의 인스턴스가 제거 될 때 삭제 되도록 하려면 제거 정책을 *삭제*로 설정할 수 있습니다. 삭제하도록 제거 정책을 설정하면 확장 집합 인스턴스 수 속성을 늘려 새 VM을 만들 수 있습니다. 제거된 VM은 기본 디스크와 함께 삭제되므로 스토리지에 대한 요금이 청구되지 않습니다. 확장 집합의 자동 크기 조정 기능을 사용하여 제거된 VM을 자동으로 시도하고 보정할 수 있지만 성공적인 할당을 보장하지는 않습니다. 디스크 비용을 방지 하 고 할당량 한도에 도달 하려면 제거 정책을 삭제로 설정 하는 경우에만 지점 크기 집합에서 자동 크기 조정 기능을 사용 하는 것이 좋습니다. 

사용자는 [Azure Scheduled Events](../virtual-machines/linux/scheduled-events.md)를 통해 VM 내 알림을 받도록 옵트인 (opt in) 할 수 있습니다. 이렇게 하면 Vm을 제거 하는 경우에 알림이 표시 되며, 제거 되기 전에 작업을 완료 하 고 종료 작업을 수행 하는 데 30 초 정도 걸립니다. 

## <a name="placement-groups"></a>배치 그룹
배치 그룹은 자체 장애 도메인 및 업그레이드 도메인을 포함 하는 Azure 가용성 집합과 비슷한 구문입니다. 기본적으로 확장 집합은 최대 100대의 VM을 갖춘 단일 배치 그룹으로 구성됩니다. 확장 집합 속성이 `singlePlacementGroup` *false*로 설정 된 경우 확장 집합은 여러 배치 그룹으로 구성 될 수 있으며 범위는 0 ~ 1000 인 vm입니다. 

> [!IMPORTANT]
> HPC와 함께 Infiniband를 사용 하지 않는 경우 확장 집합 속성을 false로 설정 하 여 `singlePlacementGroup` 여러 *false* 배치 그룹을 사용 하 여 지역 또는 영역 전체의 크기를 조정 하는 것이 좋습니다. 

## <a name="deploying-spot-vms-in-scale-sets"></a>확장 집합에 지점 Vm 배포

확장 집합에 별색 Vm을 배포 하려면 새 *우선 순위* *플래그를 지정 합니다.* 확장 집합의 모든 Vm이 지점으로 설정 됩니다. 지점 Vm을 사용 하 여 확장 집합을 만들려면 다음 방법 중 하나를 사용 합니다.
- [Azure Portal](#portal)
- [Azure CLI](#azure-cli)
- [Azure PowerShell](#powershell)
- [Azure 리소스 관리자 템플릿](#resource-manager-templates)

## <a name="portal"></a>포털

스폿 Vm을 사용 하는 확장 집합을 만드는 프로세스는 [시작 문서](quick-create-portal.md)에 자세히 설명 된 것과 동일 합니다. 확장 집합을 배포 하는 경우 별색 플래그를 설정 하 고 제거 정책: ![ 스폿 vm을 사용 하 여 확장 집합 만들기를 선택할 수 있습니다.](media/virtual-machine-scale-sets-use-spot/vmss-spot-portal-max-price.png)


## <a name="azure-cli"></a>Azure CLI

지점 Vm을 사용 하 여 확장 집합을 만드는 프로세스는 [시작 문서](quick-create-cli.md)에 자세히 설명 된 것과 동일 합니다. '--우선 순위 '를 추가 하 고를 추가 하기만 하면 `--max-price` 됩니다. 이 예에서는에를 사용 하 여 `-1` `--max-price` 가격에 따라 인스턴스를 제거 하지 않습니다.

```azurecli
az vmss create \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --image UbuntuLTS \
    --upgrade-policy-mode automatic \
    --single-placement-group false \
    --admin-username azureuser \
    --generate-ssh-keys \
    --priority Spot \
    --max-price -1 
```

## <a name="powershell"></a>PowerShell

지점 Vm을 사용 하 여 확장 집합을 만드는 프로세스는 [시작 문서](quick-create-powershell.md)에 자세히 설명 된 것과 동일 합니다.
'-Priority 스폿 '을 추가 하 고 AzVmssConfig에를 제공 `-max-price` 합니다. [New-AzVmssConfig](/powershell/module/az.compute/new-azvmssconfig)

```powershell
$vmssConfig = New-AzVmssConfig `
    -Location "East US 2" `
    -SkuCapacity 2 `
    -SkuName "Standard_DS2" `
    -UpgradePolicyMode Automatic `
    -Priority "Spot" `
    --max-price -1
```

## <a name="resource-manager-templates"></a>리소스 관리자 템플릿

별색 Vm을 사용 하는 확장 집합을 만드는 프로세스는 [Linux](quick-create-template-linux.md) 또는 [Windows](quick-create-template-windows.md)용 시작 문서에 설명 된 것과 동일 합니다. 

별색 템플릿 배포의 경우 이상을 사용 `"apiVersion": "2019-03-01"` 합니다. 

`priority`, 및 속성을 섹션에 추가 하 `evictionPolicy` `billingProfile` `"virtualMachineProfile":` 고 `"singlePlacementGroup": false,` 속성을 `"Microsoft.Compute/virtualMachineScaleSets"` 템플릿의 섹션에 추가 합니다.

```json

{
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  },
  "properties": {
    "singlePlacementGroup": false,
    }

        "virtualMachineProfile": {
              "priority": "Spot",
                "evictionPolicy": "Deallocate",
                "billingProfile": {
                    "maxPrice": -1
                }
            },
```

제거 된 인스턴스를 삭제 하려면 `evictionPolicy` 매개 변수를로 변경 `Delete` 합니다.

## <a name="faq"></a>FAQ

**Q:** 만든 후에는 표준 인스턴스와 동일한 스폿 인스턴스입니다.

**A:** 예. 단, 지점 Vm에 대 한 SLA는 없으며 언제 든 지 제거할 수 있습니다.


**Q:** 제거 되었지만 여전히 용량이 필요한 경우 수행할 작업

**A:** 용량을 당장 사용 해야 하는 경우에는 스폿 Vm 대신 표준 Vm을 사용 하는 것이 좋습니다.


**Q:** 할당량은 어떻게 관리 되나요?

**A:** 스폿 인스턴스와 표준 인스턴스는 별도의 할당량 풀을 갖게 됩니다. 지점 할당량은 Vm과 확장 집합 인스턴스 간에 공유 됩니다. 자세한 내용은 [Azure 구독 및 서비스 제한, 할당량 및 제약 조건](../azure-resource-manager/management/azure-subscription-service-limits.md)을 참조 하세요.


**Q:** 추가 할당량을 요청할 수 있나요?

**A:** 예, [표준 할당량 요청 프로세스](../azure-portal/supportability/per-vm-quota-requests.md)를 통해 지점 vm에 대 한 할당량을 늘리기 위해 요청을 제출할 수 있습니다.


**Q:** 기존 확장 집합을 별색 집합으로 변환할 수 있나요?

**A:** 아니요 `Spot` . 플래그 설정은 만들 때에만 지원 됩니다.


**Q:** `low` 낮은 우선 순위의 확장 집합에를 사용 하는 경우를 대신 사용 해야 하나요 `Spot` ?

**A:** 지금은 `low` 및 `Spot` 가 모두 작동 하지만를 사용 하도록 전환 하기 시작 해야 합니다 `Spot` .


**Q:** 일반 Vm과 스폿 Vm을 모두 사용 하 여 확장 집합을 만들 수 있나요?

**A:** 아니요. 확장 집합은 둘 이상의 우선 순위 유형을 지원할 수 없습니다.


**Q:**  별색 크기 집합에 자동 크기 조정을 사용할 수 있나요?

**A:** 예, 별색 크기 집합에 자동 크기 조정 규칙을 설정할 수 있습니다. Vm을 제거 하는 경우 자동 크기 조정에서 새 스폿 Vm을 만들려고 할 수 있습니다. 그렇지만 이 용량은 보장되지 않습니다. 


**Q:**  자동 크기 조정은 제거 정책 (할당 취소 및 삭제) 모두에서 작동 하나요?

**A:** 자동 크기 조정을 사용 하는 경우 삭제 하도록 제거 정책을 설정 하는 것이 좋습니다. 할당 취소된 인스턴스가 확장 집합에서 용량 수에 따라 계산되기 때문입니다. 자동 크기 조정을 사용할 때 할당 취소되고 제거된 인스턴스로 인해 신속하게 대상 인스턴스 수를 달성할 수 있을 것 같습니다. 


**Q:** 지점 Vm을 지 원하는 채널은 무엇 인가요?

**A:** 지점 VM 가용성에 대 한 아래 표를 참조 하세요.

<a name="channel"></a>

| Azure 채널               | Azure 스폿 Vm 가용성       |
|------------------------------|-----------------------------------|
| 기업 계약         | 예                               |
| 종량제 통화 요금                | 예                               |
| CSP(클라우드 서비스 공급자) | [파트너에 게 문의](/partner-center/azure-plan-get-started) |
| 이점                     | 사용할 수 없음                     |
| 후원                    | 예                               |
| 무료 평가판                   | 사용할 수 없음                     |


**Q:** 어디에서 질문을 게시할 수 있나요?

**A:** `azure-spot` [Q&A](/answers/topics/azure-spot.html)를 사용 하 여 질문을 게시 하 고 태그를 지정할 수 있습니다. 

## <a name="next-steps"></a>다음 단계

가격 책정에 대한 자세한 내용은 [가상 머신 확장 집합 가격 페이지](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/linux/)를 확인하세요.
