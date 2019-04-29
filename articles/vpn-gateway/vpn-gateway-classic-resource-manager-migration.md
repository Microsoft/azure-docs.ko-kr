---
title: Resource Manager 마이그레이션에 대한 VPN Gateway 클래식 | Microsoft Docs
description: 이 페이지에서는 Resource Manager 마이그레이션에 대한 VPN Gateway 클래식의 개요를 제공합니다.
documentationcenter: na
services: vpn-gateway
author: amsriva
manager: rossort
editor: amsriva
ms.assetid: caa8eb19-825a-4031-8b49-18fbf3ebc04e
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/02/2017
ms.author: amsriva
ms.openlocfilehash: b65b47389611bcc0e5acb3c7ebff672f72a87581
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60761587"
---
# <a name="vpn-gateway-classic-to-resource-manager-migration"></a>Resource Manager 마이그레이션에 대한 VPN Gateway 클래식
이제 VPN Gateway을 클래식에서 Resource Manager 배포 모델로 마이그레이션할 수 있습니다. [Azure Resource Manager 기능 및 이점](../azure-resource-manager/resource-group-overview.md)에 대해 자세히 알아볼 수 있습니다. 이 문서에서는 클래식 배포에서 새로운 Resource Manager 기반 모델로 마이그레이션하는 방법을 자세히 설명합니다. 

VPN Gateway는 VNet 마이그레이션의 일환으로 클래식에서 Resource Manager로 마이그레이션됩니다. 이 마이그레이션은 한 번에 하나의 VNet 꼴로 이뤄집니다. 도구 측면에서 추가 요구 사항이나 마이그레이션에 대한 전제 조건이 없습니다. 마이그레이션 단계는 기존 VNet 마이그레이션과 동일하며 [IaaS 리소스 마이그레이션 페이지](../virtual-machines/windows/migration-classic-resource-manager-ps.md)에 설명되어 있습니다. 마이그레이션 중에 데이터 경로 가동 중지 시간이 없습니다. 따라서 기존 작업은 마이그레이션 중에 온-프레미스 연결 손실 없이 계속 작동할 것입니다. VPN 게이트웨이에 연결된 공용 IP 주소는 마이그레이션 프로세스 중에 변경되지 않습니다. 즉, 마이그레이션이 완료되면 온-프레미스 라우터를 다시 구성할 필요가 없습니다.  

Resource Manager에 있는 모델은 클래식 모델과 다르며 가상 네트워크 게이트웨이, 로컬 네트워크 게이트웨이 및 연결 리소스로 구성됩니다. 이것들은 각기 VPN 게이트웨이 자체를, 온-프레미스 주소 공간을 나타내는 로컬 사이트 및 둘 간의 연결을 나타냅니다. 마이그레이션이 완료되면 게이트웨이는 클래식 모델에서 사용할 수 없게 될 것이며 가상 네트워크 게이트웨이, 로컬 네트워크 게이트웨이 및 연결 개체에서의 모든 관리 작업은 Resource Manager 모델을 사용하여 수행되어야 합니다.

## <a name="supported-scenarios"></a>지원되는 시나리오
가장 일반적인 VPN 연결 시나리오는 클래식에서 Resource Manager 마이그레이션에서 다뤄집니다. 지원되는 시나리오 -

* 지점 및 사이트 간 연결
* 온-프레미스 위치에서 연결된 VPN Gateway를 사용한 사이트 간 연결
* VPN Gateway를 사용한 두 VNet 사이의 VNet 간 연결
* 온-프레미스 위치의 같은 곳에 연결된 다중 VNet
* 다중 사이트 연결
* 강제 터널링 사용 설정된 VNet

지원되지 않는 시나리오 -  

* ExpressRoute 게이트웨이와 VPN Gateway가 모두 있는 VNet은 현재 지원되지 않습니다.
* VM 확장이 온-프레미스 서버에 연결된 시나리오를 전송합니다. 전송 시 VPN 연결 제한은 아래에 자세히 설명됩니다.

> [!NOTE]
> Resource Manager 모델의 CIDR 유효성 검사는 기존 모델에 비해 훨씬 엄격합니다. 주어진 클래식 주소 범위가 유효한 CIDR 형식을 준수하는지 마이그레이션이 시작되기 전에 확인 합니다. CIDR는 일반적인 CIDR 유효성 검사기를 사용하여 유효성을 검사할 수 있습니다. 마이그레이션할 때 유효하지 않은 CIDR 범위를 사용한 VNet 또는 로컬 사이트는 실패 상태가 됩니다.
> 
> 

## <a name="vnet-to-vnet-connectivity-migration"></a>VNet 간 연결 마이그레이션
클래식에서 VNet 간 연결은 연결된 VNet의 로컬 사이트 표현을 만들어 구현하였습니다. 고객은 함께 연결하는 데 필요한 두 VNet을 나타내는 두 개의 로컬 사이트를 만들어야 했습니다. 그런 다음 이 사이트들은 두 Vnet간 연결을 설정하기 위해 IPsec 터널을 사용하여 해당 Vnet에 연결되었습니다. 이 모델에서는 하나의 VNet에서 주소 범위가 변경되는 경우 해당 로컬 사이트 표시에서도 또한 유지되어야 하므로 관리 효율성 문제가 있습니다. Resource Manager 모델에서 이 해결 방법은 더 이상 필요하지 않습니다. 두 VNet 간의 연결은 연결 리소스에서 'Vnet2Vnet' 연결 형식을 사용하여 직접 구현할 수 있습니다. 

![VNet 간 마이그레이션 스크린샷.](./media/vpn-gateway-migration/migration1.png)

VNet 마이그레이션 도중에 사용자는 현재 VNet의 VPN 게이트웨이에 연결된 엔터티가 또 다른 VNet인지 감지하고 두 VNet의 마이그레이션이 완료되면 다른 VNet을 나타내는 두 로컬 사이트가 더 이상 보이지 않는지 확인합니다. 두 VPN 게이트웨이, 두 로컬 사이트 및 이들 간의 두 연결로 이뤄진 클래식 모델은 두 VPN 게이트웨이 및 Vnet2Vnet 형식의 두 연결로 이뤄진 Resource Manager 모델로 변환됩니다.

## <a name="transit-vpn-connectivity"></a>전송 시 VPN 연결
VNet에 대한 온-프레미스 연결이 온-프레미스에 직접 연결된 또 다른 VNet에 연결하여 구현되도록 토폴로지에서 VPN 게이트웨이를 구성할 수 있습니다. 이것은 첫 번째 VNet에서의 인스턴스가 온-프레미스에 직접 연결된 VNet에 있는 VPN 게이트웨이에 대한 전송을 통해 온-프레미스 리소스에 연결된 전송 시 VPN 연결입니다. 클래식 배포 모델에서 이 구성을 구축하기 위해 연결된 VNet 및 온-프레미스 주소 공간을 나타내는 집계된 접두사가 있는 로컬 사이트를 만들어야 합니다. 그런 다음 이 대표적 로컬 사이트는 전송 연결을 구축하기 위해 VNet에 연결됩니다. 또한 이 클래식 모델에는 온-프레미스 주소 범위의 변경 내용이 VNet 및 온-프레미스의 집계를 나타내는 로컬 사이트에도 유지되어야 하므로 비슷한 관리 효율성 문제가 있습니다. Resource Manager가 지원되는 게이트웨이에서 BGP 지원 도입은 연결된 게이트웨이가 접두사를 수동 수정하지 않고도 온-프레미스에서 경로를 알 수 있으므로 관리 효율성을 간소화합니다.

![전송 라우팅 시나리오의 스크린샷.](./media/vpn-gateway-migration/migration2.png)

로컬 사이트 없이 VNet 간 연결을 변환하기 때문에, 전송 시나리오는 온-프레미스에 간접 연결된 VNet에 대한 온-프레미스 연결을 잃습니다. 연결 손실은 마이그레이션이 완료된 후 다음 두 가지 방법으로 완화될 수 있습니다. 

* 서로 연결되고 온-프레미스에 연결된 VPN 게이트웨이에서 BGP를 사용하도록 설정합니다. BGP를 사용하도록 설정하면 경로가 VNet 게이트웨이 간에 학습되고 광고되기 때문에 다른 구성 변경 없이 연결을 복원합니다. BGP 옵션은 표준 및 상위 SKU에서만 사용할 수 있습니다.
* 영향을 받는 VNet에서 온-프레미스 위치를 나타내는 로컬 네트워크 게이트웨이로 명시적 연결을 설정합니다. 또한 IPsec 터널을 만들고 구성하기 위해 온-프레미스 라우터에서 구성을 변경해야 합니다.

## <a name="next-steps"></a>다음 단계
VPN 게이트웨이 마이그레이션 지원에 대해 학습한 후에 시작하려면 [클래식에서 Resource Manager로 IaaS 리소스의 플랫폼 지원 마이그레이션](../virtual-machines/windows/migration-classic-resource-manager-ps.md)으로 이동합니다.

