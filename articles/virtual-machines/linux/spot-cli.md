---
title: CLI를 사용 하 여 Azure 스폿 Vm 배포
description: CLI를 사용 하 여 비용을 절감 하기 위해 Azure 스폿 Vm을 배포 하는 방법을 알아봅니다.
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 06/26/2020
ms.author: cynthn
ms.reviewer: jagaveer
ms.openlocfilehash: 165a2c6221c8d3c14f71134deef962d0859bb438
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88816714"
---
# <a name="deploy-spot-vms-using-the-azure-cli"></a>Azure CLI를 사용 하 여 스폿 Vm 배포

[Azure 스폿 vm](spot-vms.md) 을 사용 하면 비용을 크게 절약할 수 있는 사용 되지 않는 용량을 활용할 수 있습니다. Azure에서 용량을 다시 필요로 하는 모든 시점에서 Azure 인프라는 스폿 Vm을 제거 합니다. 따라서 지점 Vm은 일괄 처리 작업, 개발/테스트 환경, 대규모 계산 워크 로드 등의 중단을 처리할 수 있는 워크 로드에 적합 합니다.

지점 Vm의 가격은 지역 및 SKU에 따라 가변적입니다. 자세한 내용은 [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) 및 [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/)에 대 한 VM 가격 책정을 참조 하세요. 

VM에 대해 시간당 요금을 지불할 최대 가격을 설정 하는 옵션이 있습니다. 지점 VM의 최대 가격은 미국 달러 (USD)로 설정 하 여 최대 5 개의 소수 자릿수를 사용할 수 있습니다. 예를 들어 값은 `0.98765` 시간당 $0.98765 USD의 최대 가격이 됩니다. 최대 가격을로 설정 하는 경우 `-1` 가격에 따라 VM이 제거 되지 않습니다. VM의 가격은 사용 가능한 용량과 할당량을 초과 하는 경우 더 작은 표준 VM의 현재 가격 또는 가격입니다. 최대 가격을 설정 하는 방법에 대 한 자세한 내용은 [지점 vm-가격 책정](../spot-vms.md#pricing)을 참조 하세요.

Azure CLI를 사용 하 여 VM을 만드는 프로세스는 [빠른 시작 문서](./quick-create-cli.md)에 자세히 설명 된 것과 같습니다. '--Priority 스폿 ' 매개 변수를 추가 하 고를 `--eviction-policy` 할당 취소 (기본값) 또는로 설정 하 `Delete` 고 최대 가격 또는을 제공 합니다 `-1` . 


## <a name="install-azure-cli"></a>Azure CLI 설치

스폿 Vm을 만들려면 Azure CLI 버전 2.0.74 이상을 실행 해야 합니다. 버전을 찾으려면 **az --version**을 실행합니다. 설치 또는 업그레이드가 필요한 경우, [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요. 

[az login](/cli/azure/reference-index#az-login)을 사용하여 Azure에 로그인합니다.

```azurecli
az login
```

## <a name="create-a-spot-vm"></a>스폿 VM 만들기

이 예제에서는 가격에 따라 제거 되지 않을 Linux 스폿 VM을 배포 하는 방법을 보여 줍니다. 제거 정책은 나중에 다시 시작할 수 있도록 VM의 할당을 취소 하도록 설정 됩니다. Vm을 제거할 때 VM 및 기본 디스크를 삭제 하려면 `--eviction-policy` 를로 설정 `Delete` 합니다.

```azurecli
az group create -n mySpotGroup -l eastus
az vm create \
    --resource-group mySpotGroup \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --priority Spot \
    --max-price -1 \
    --eviction-policy Deallocate
```



VM을 만든 후에 쿼리 하 여 리소스 그룹의 모든 Vm에 대 한 최대 청구 가격을 확인할 수 있습니다.

```azurecli
az vm list \
   -g mySpotGroup \
   --query '[].{Name:name, MaxPrice:billingProfile.maxPrice}' \
   --output table
```

**다음 단계**

[Azure PowerShell](../windows/spot-powershell.md), [포털](../windows/spot-portal.md)또는 [템플릿을](spot-template.md)사용 하 여 스폿 VM을 만들 수도 있습니다.

오류가 발생 하는 경우 [오류 코드](../error-codes-spot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)를 참조 하세요.
