---
title: 공용 IP 주소 접두사
description: Azure 공용 IP 주소 접두사가 무엇인지 예측 가능한 공용 IP 주소를 리소스에 할당하는 데 어떻게 도움이 되는지 알아봅니다.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/29/2020
ms.author: allensu
ms.openlocfilehash: 7671ccecb4bbda17853b33989b7a7bc2cdbd6f89
ms.sourcegitcommit: a434cfeee5f4ed01d6df897d01e569e213ad1e6f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/09/2021
ms.locfileid: "111809767"
---
# <a name="public-ip-address-prefix"></a>공용 IP 주소 접두사

공용 IP 주소 접두사는 Azure에서 예약된 IP 주소 범위입니다. Azure는 사용자가 지정한 개수에 따라 구독에 대한 인접한 주소 범위를 제공합니다. 

공용 주소에 대해 잘 모르는 경우 [공용 IP 주소](./public-ip-addresses.md#public-ip-addresses)를 참조하세요.

공용 IP 주소는 각 Azure 지역의 주소 풀에서 할당됩니다. Azure에서 각 지역에 사용하는 범위 목록을 [다운로드](https://www.microsoft.com/download/details.aspx?id=56519)할 수 있습니다. 예를 들어 40.121.0.0/16은 Azure에서 미국 동부 지역에 사용되는 100개가 넘는 범위 중 하나입니다. 범위에는 사용 가능한 주소(40.121.0.1 - 40.121.255.254)가 포함됩니다.

이름을 지정하고 접두사에 포함시킬 주소의 수를 지정하여 Azure 지역 및 구독에 공용 IP 주소 접두사를 만듭니다. 

공용 IP 주소 범위는 선택한 접두사를 사용하여 할당됩니다. /28 접두사를 생성할 경우 Azure는 해당 범위 중 하나에서 16개의 IP 주소를 제공합니다.

범위를 만들 때까지는 Azure에서 할당될 범위를 알 수 없지만 주소는 연속적입니다. 

공용 IP 주소 접두사에는 요금이 부과되며, 자세한 내용은 [공용 IP 주소 가격 책정](https://azure.microsoft.com/pricing/details/ip-addresses)을 참조하세요.

## <a name="why-create-a-public-ip-address-prefix"></a>공용 IP 주소 접두사를 만드는 이유

공용 IP 주소 리소스를 만드는 경우 Azure에서 한 지역에 사용되는 범위 중 하나를 선택하여 사용 가능한 공용 IP 주소가 할당합니다. 

Azure에서 IP 주소를 할당할 때까지 정확한 IP를 알 수 없습니다. 이 프로세스는 특정 IP 주소를 허용하는 방화벽 규칙을 만들 때 문제가 될 수 있습니다. 추가된 모든 IP 주소에 대해 해당하는 방화벽 규칙을 추가해야 합니다.

공용 IP 주소 접두사에서 리소스에 주소를 할당하는 경우 방화벽 규칙 업데이트는 필요하지 않습니다. 전체 범위가 규칙에 추가됩니다.

## <a name="benefits"></a>이점

- 알려진 범위에서 공용 IP 주소 리소스의 생성.
- 현재 할당한 공용 IP 주소 및 아직 할당하지 않은 주소를 포함하는 범위를 사용하는 방화벽 규칙 구성. 이렇게 구성하면 새 리소스에 IP 주소를 할당할 때 방화벽 규칙을 변경할 필요가 없습니다.
- 만들 수 있는 범위의 기본 크기는 IP 주소 /28 또는 16개입니다.
- 만들 수 있는 범위 수에 대한 제한은 없습니다. Azure 구독에 포함할 수 있는 정적 공용 IP 주소의 최대 수에는 제한이 있습니다. 따라서 만들 수 있는 범위의 개수는 구독에 포함할 수 있는 정적 공용 IP 주소의 개수 이상을 포함할 수 없습니다. 자세한 내용은 [Azure 제한](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)을 참조하세요.
- 접두사의 주소를 사용하여 만든 주소는 공용 IP 주소를 할당할 수 있는 Azure 리소스에 할당할 수 있습니다.
- 범위 내에서 어떤 IP 주소가 할당되거나 할당되지 않았는지 쉽게 볼 수 있습니다.

## <a name="scenarios"></a>시나리오
접두사의 고정 공용 IP 주소에는 다음과 같은 리소스를 연결할 수 있습니다.

|리소스|시나리오|단계|
|---|---|---|
|가상 머신| 접두사에서 공용 IP를 Azure의 가상 머신에 연결하면 방화벽의 IP를 허용 목록에 대한 관리 오버헤드가 줄어듭니다. 하나의 방화벽 규칙에서 전체 접두사를 허용 목록에 포함시킬 수 있습니다. Azure에서 가상 머신으로 확장할 때 동일한 접두사의 IP를 연결하면 비용, 시간 및 관리 오버헤드를 절약할 수 있습니다.| 접두사의 IP를 가상 머신에 연결하려면: </br> 1. [접두사를 만듭니다.](manage-public-ip-address-prefix.md) </br> 2. [접두사에서 IP를 만듭니다.](manage-public-ip-address-prefix.md) </br> 3. [IP를 가상 머신의 네트워크 인터페이스에 연결합니다.](virtual-network-network-interface-addresses.md#add-ip-addresses) </br> [가상 머신 확장 집합에 IP를 연결](https://azure.microsoft.com/resources/templates/vmms-with-public-ip-prefix/)할 수도 있습니다.
| 표준 부하 분산 장치 | 접두사의 공용 IP를 프런트 엔드 IP 구성 또는 부하 분산 장치의 아웃바운드 규칙에 연결하면 Azure 공용 IP 주소 공간이 간소화됩니다. 인접한 IP 주소 범위에서 아웃 바운드 연결을 제거하여 시나리오를 간소화 합니다. | 접두사의 IP를 부하 분산 장치에 연결하려면: </br> 1. [접두사를 만듭니다.](manage-public-ip-address-prefix.md) </br> 2. [접두사에서 IP를 만듭니다.](manage-public-ip-address-prefix.md) </br> 부하 분산 장치를 만들 때 위의 2단계에서 부하 분산 장치의 프런트 엔드 IP로 만든 IP를 선택하거나 업데이트합니다. |
| Azure Firewall | 아웃바운드 SNAT의 접두사에서 공용 IP를 사용할 수 있습니다. 모든 아웃바운드 가상 네트워크 트래픽은 [Azure Firewall](../firewall/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 공용 IP로 변환됩니다. | IP를 접두사에서 방화벽에 연결하려면 다음을 수행합니다. </br> 1. [접두사를 만듭니다.](manage-public-ip-address-prefix.md) </br> 2. [접두사에서 IP를 만듭니다.](manage-public-ip-address-prefix.md) </br> [Azure Firewall을 배포](../firewall/tutorial-firewall-deploy-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#deploy-the-firewall)할 때 접두사에서 이전에 할당한 IP를 반드시 선택해야 합니다.|
| VPN Gateway (AZ SKU) 또는 Application Gateway v2 | 영역 중복 VPN 또는 애플리케이션 게이트웨이 v2에 대해 접두사에서 공용 IP를 사용할 수 있습니다. | IP를 접두사에서 게이트웨이에 연결하려면 다음을 수행합니다. </br> 1. [접두사를 만듭니다.](manage-public-ip-address-prefix.md) </br> 2. [접두사에서 IP를 만듭니다.](manage-public-ip-address-prefix.md) </br> 3. [VPN Gateway](../vpn-gateway/tutorial-create-gateway-portal.md) 또는 [애플리케이션 게이트웨이](../application-gateway/quick-create-portal.md#create-an-application-gateway)를 배포할 때 이전에 접두사에서 제공한 IP를 선택해야 합니다.|

## <a name="constraints"></a>제약 조건

- 접두사에 대한 IP 주소를 지정할 수 없습니다. 사용자가 지정한 크기에 따라 접두사에 대한 IP 주소가 Azure에서 할당됩니다.
- 기본적으로 최대 16개의 IP 주소 또는 /28의 접두사를 만들 수 있습니다. 자세한 내용은 [네트워크 제한 증가 요청](../azure-portal/supportability/networking-quota-requests.md) 및 [Azure 제한](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)을 참조하세요.
- 접두사를 만든 후에는 범위를 변경할 수 없습니다.
- 표준 SKU로 만든 고정 공용 IP 주소만 접두사의 범위에서 할당할 수 있습니다. 공용 IP 주소 SKU에 대해 자세히 알아보려면 [공용 IP 주소](./public-ip-addresses.md#public-ip-addresses)를 참조하세요.
- 범위에서 가져온 주소는 Azure Resource Manager 리소스에만 할당할 수 있습니다. 클래식 배포 모델의 리소스에는 주소를 할당할 수 없습니다.
- prefix에서 생성된 모든 공용 IP 주소는 prefix와 동일한 Azure 지역 및 구독에 있어야 합니다. 동일한 지역 및 구독의 리소스에 주소를 할당해야 합니다.
- 접두사 내에 있는 주소가 리소스에 연결된 공용 IP 주소 리소스에 할당되어 있으면 접두사를 삭제할 수 없습니다. 먼저 접두사의 IP 주소가 할당되어 있는 모든 공용 IP 주소 리소스를 분리합니다.


## <a name="next-steps"></a>다음 단계

- 공용 IP 주소 접두사 [만들기](manage-public-ip-address-prefix.md)