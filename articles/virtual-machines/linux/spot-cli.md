---
title: CLI를 사용 하 여 Azure 스폿 Vm 배포 (미리 보기)
description: CLI를 사용 하 여 비용을 절감 하기 위해 Azure 스폿 Vm을 배포 하는 방법을 알아봅니다.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/11/2020
ms.author: cynthn
ms.openlocfilehash: 110e935671ab1d640b2ff3dc26c203b262e999fe
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77163095"
---
# <a name="preview-deploy-spot-vms-using-the-azure-cli"></a>미리 보기: Azure CLI을 사용 하 여 스폿 Vm 배포

[Azure 스폿 vm](spot-vms.md) 을 사용 하면 비용을 크게 절약할 수 있는 사용 되지 않는 용량을 활용할 수 있습니다. Azure에서 용량을 다시 필요로 하는 모든 시점에서 Azure 인프라는 스폿 Vm을 제거 합니다. 따라서 지점 Vm은 일괄 처리 작업, 개발/테스트 환경, 대규모 계산 워크 로드 등의 중단을 처리할 수 있는 워크 로드에 적합 합니다.

지점 Vm의 가격은 지역 및 SKU에 따라 가변적입니다. 자세한 내용은 [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) 및 [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/)에 대 한 VM 가격 책정을 참조 하세요. 

VM에 대해 시간당 요금을 지불할 최대 가격을 설정 하는 옵션이 있습니다. 지점 VM의 최대 가격은 미국 달러 (USD)로 설정 하 여 최대 5 개의 소수 자릿수를 사용할 수 있습니다. 예를 들어 `0.98765`값은 시간당 $0.98765 USD의 최대 가격이 됩니다. 최대 가격을 `-1`로 설정 하는 경우 가격에 따라 VM이 제거 되지 않습니다. VM의 가격은 사용 가능한 용량과 할당량을 초과 하는 경우 더 작은 표준 VM의 현재 가격 또는 가격입니다. 최대 가격을 설정 하는 방법에 대 한 자세한 내용은 [지점 vm-가격 책정](spot-vms.md#pricing)을 참조 하세요.

Azure CLI를 사용 하 여 VM을 만드는 프로세스는 [빠른 시작 문서](/azure/virtual-machines/linux/quick-create-cli)에 자세히 설명 된 것과 같습니다. '--Priority 스팟 ' 매개 변수를 추가 하 고 최대 가격 또는 `-1`을 제공 하면 됩니다.

> [!IMPORTANT]
> 현재 스폿 인스턴스는 공개 미리 보기로 제공 됩니다.
> 이 미리 보기 버전은 프로덕션 워크 로드에는 권장 되지 않습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.
>



## <a name="install-azure-cli"></a>Azure CLI 설치

스폿 Vm을 만들려면 Azure CLI 버전 2.0.74 이상을 실행 해야 합니다. 버전을 찾으려면 **az --version**을 실행합니다. 설치 또는 업그레이드가 필요한 경우, [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요. 

[az login](/cli/azure/reference-index#az-login)을 사용하여 Azure에 로그인합니다.

```azurecli
az login
```

## <a name="create-a-spot-vm"></a>스폿 VM 만들기

이 예제에서는 가격에 따라 제거 되지 않을 Linux 스폿 VM을 배포 하는 방법을 보여 줍니다. 

```azurecli
az group create -n mySpotGroup -l eastus
az vm create \
    --resource-group mySpotGroup \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --priority Spot \
    --max-price -1
```

VM을 만든 후에 쿼리 하 여 리소스 그룹의 모든 Vm에 대 한 최대 청구 가격을 확인할 수 있습니다.

```azurecli
az vm list \
   -g mySpotGroup \
   --query '[].{Name:name, MaxPrice:billingProfile.maxPrice}' \
   --output table
```

**다음 단계**

[Azure PowerShell](../windows/spot-powershell.md) 또는 [템플릿을](spot-template.md)사용 하 여 스폿 VM을 만들 수도 있습니다.

오류가 발생 하는 경우 [오류 코드](../error-codes-spot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)를 참조 하세요.
