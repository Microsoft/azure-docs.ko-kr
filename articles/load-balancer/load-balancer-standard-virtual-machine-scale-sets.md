---
title: Azure 표준 Load Balancer 및 Virtual Machine Scale Sets
titleSuffix: Azure Standard Load Balancer and Virtual Machine Scale Sets
description: 이 학습 경로를 사용 하 여 Azure 표준 Load Balancer 및 Virtual Machine Scale Sets를 시작 합니다.
services: load-balancer
documentationcenter: na
author: irenehua
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/17/2020
ms.author: irenehua
ms.openlocfilehash: 41f1d6c18eab35bd1a41d4cfa98d0cbda69b35ac
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88650335"
---
# <a name="azure-load-balancer-with-azure-virtual-machine-scale-sets"></a>Azure 가상 머신 확장 집합을 사용 하 여 Azure Load Balancer

가상 머신 확장 집합 및 부하 분산 장치를 사용 하는 경우 다음 지침을 고려해 야 합니다.

## <a name="port-forwarding-and-inbound-nat-rules"></a>포트 전달 및 인바운드 NAT 규칙:
  * 확장 집합을 만든 후에는 부하 분산 장치의 상태 프로브에서 사용 하는 부하 분산 규칙에 대해 백 엔드 포트를 수정할 수 없습니다. 포트를 변경 하려면 Azure 가상 머신 확장 집합을 업데이트 하 고, 포트를 업데이트 한 후 상태 프로브를 다시 구성 하 여 상태 프로브를 제거할 수 있습니다.
  * 부하 분산 장치의 백 엔드 풀에 있는 가상 머신 확장 집합을 사용 하는 경우 기본 인바운드 NAT 규칙이 자동으로 만들어집니다.
## <a name="inbound-nat-pool"></a>인바운드 NAT 풀:
  * 각 가상 머신 확장 집합에는 하나 이상의 인바운드 NAT 풀이 있어야 합니다. 
  * 인바운드 NAT 풀은 인바운드 NAT 규칙의 컬렉션입니다. 하나의 인바운드 NAT 풀에서 여러 가상 머신 확장 집합을 지원할 수 없습니다.
  * 기존 가상 머신 확장 집합에서 NAT 풀을 삭제 하려면 먼저 확장 집합에서 NAT 풀을 제거 해야 합니다. CLI를 사용 하는 전체 예제는 다음과 같습니다.
```azurecli-interactive
  az vmss update
     --resource-group MyResourceGroup
     --name MyVMSS
     --remove virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerInboundNatPools
  az vmss update-instances
     -–instance-ids *
     --resource-group MyResourceGroup
     --name MyVMSS
  az network lb inbound-nat-pool delete
     --resource-group MyResourceGroup
     -–lb-name MyLoadBalancer
     --name MyNatPool
```
## <a name="load-balancing-rules"></a>부하 분산 규칙:
  * 부하 분산 장치의 백 엔드 풀에 있는 가상 머신 확장 집합을 사용 하는 경우 기본 부하 분산 규칙이 자동으로 만들어집니다.
## <a name="outbound-rules"></a>아웃 바운드 규칙:
  *  부하 분산 규칙에서 이미 참조 하는 백 엔드 풀에 대 한 아웃 바운드 규칙을 만들려면 인바운드 부하 분산 규칙을 만들 때 먼저 포털에서 **"암시적 아웃 바운드 규칙 만들기"** 를 **아니요** 로 표시 해야 합니다.

  :::image type="content" source="./media/vm-scale-sets/load-balancer-and-vm-scale-sets.png" alt-text="부하 분산 규칙 만들기" border="true":::

다음 메서드는 기존 Azure 부하 분산 장치를 사용 하 여 가상 머신 확장 집합을 배포 하는 데 사용할 수 있습니다.

* [Azure Portal를 사용 하 여 기존 Azure Load Balancer를 사용 하 여 가상 머신 확장 집합을 구성](https://docs.microsoft.com/azure/load-balancer/configure-vm-scale-set-portal)합니다.
* [Azure PowerShell를 사용 하 여 기존 Azure Load Balancer를 사용 하 여 가상 머신 확장 집합을 구성](https://docs.microsoft.com/azure/load-balancer/configure-vm-scale-set-powershell)합니다.
* [Azure CLI를 사용 하 여 기존 Azure Load Balancer를 사용 하 여 가상 머신 확장 집합을 구성](https://docs.microsoft.com/azure/load-balancer/configure-vm-scale-set-cli)합니다.
