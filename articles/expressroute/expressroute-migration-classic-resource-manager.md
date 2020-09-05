---
title: 'Azure Express 경로: 클래식 Vnet을 리소스 관리자로 마이그레이션'
description: 이 페이지에서는 회로를 이동한 후에 ExpressRoute에 연결된 가상 네트워크를 Resource Manager로 마이그레이션하는 방법을 설명합니다.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: how-to
ms.date: 02/06/2020
ms.author: cherylmc
ms.openlocfilehash: 10a2da903ecd59abb7930f9f20c5c905748891cb
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88509895"
---
# <a name="migrate-expressroute-associated-virtual-networks-from-classic-to-resource-manager"></a>ExpressRoute에 연결된 가상 네트워크를 클래식에서 Resource Manager로 마이그레이션

이 문서에서는 ExpressRoute 회로를 이동한 후에 ExpressRoute에 연결된 가상 네트워크를 클래식 배포 모델에서 Azure Resource Manager 배포 모델로 마이그레이션하는 방법을 설명합니다. 

## <a name="before-you-begin"></a>시작하기 전에

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* 최신 버전의 Azure PowerShell 모듈이 있는지 확인 합니다. 자세한 내용은 [Azure PowerShell을 설치 및 구성하는 방법](/powershell/azure/)을 참조하세요. PowerShell 서비스 관리 모듈 (클래식 배포 모델에 필요 함)을 설치 하려면 [Azure PowerShell 서비스 관리 모듈 설치](/powershell/azure/servicemanagement/install-azure-ps)를 참조 하세요.
* 구성을 시작하기 전에 [필수 조건](expressroute-prerequisites.md), [라우팅 요구 사항](expressroute-routing.md) 및 [워크플로](expressroute-workflows.md)를 검토했는지 확인합니다.
* [클래식에서 Resource Manager로 ExpressRoute 회로 이동](expressroute-move.md)에서 제공되는 정보를 검토합니다. 제한 및 제한 사항을 완전히 이해해야 합니다.
* 클래식 배포 모델에서 회로가 완벽하게 작동되는지 확인합니다.
* 리소스 관리자 배포 모델에 만든 리소스 그룹이 있는지 확인합니다.
* 다음 리소스 마이그레이션 설명서를 검토합니다.

    * [클래식에서 Azure Resource Manager로 IaaS 리소스의 플랫폼 지원 마이그레이션](../virtual-machines/windows/migration-classic-resource-manager-overview.md)
    * [클래식에서 Azure Resource Manager로의 플랫폼 지원 마이그레이션에 대한 기술 정보](../virtual-machines/migration-classic-resource-manager-deep-dive.md)
    * [FAQ: 클래식에서 Azure Resource Manager로 IaaS 리소스의 플랫폼 지원 마이그레이션](../virtual-machines/migration-classic-resource-manager-faq.md)
    * [가장 일반적인 마이그레이션 오류 및 완화 방법 검토](../virtual-machines/windows/migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="supported-and-unsupported-scenarios"></a>지원되는 및 지원되지 않는 시나리오

* ExpressRoute 회로를 가동 중지 시간 없이 클래식에서 Resource Manager 환경으로 이동할 수 있습니다. ExpressRoute 회로를 클래식에서 Resource Manager 환경으로 가동 중지 시간 없이 이동할 수 있습니다. [PowerShell을 사용하여 클래식에서 Resource Manager 배포 모델로 ExpressRoute 회로 이동](expressroute-howto-move-arm.md)의 지침을 따르세요. 가상 네트워크에 연결된 리소스를 이동하기 위한 필수 구성 요소입니다.
* 동일한 구독에서 ExpressRoute 회로에 연결된 가상 네트워크, 게이트웨이 및 가상 네트워크 내 연결된 배포를 가동 중지 시간 없이 Resource Manager 환경으로 마이그레이션할 수 있습니다. 나중에 설명하는 단계에 따라 가상 네트워크, 게이트웨이 및 가상 네트워크 내 배포된 가상 머신과 같은 리소스를 마이그레이션할 수 있습니다. 마이그레이션하기 전에 가상 네트워크가 올바르게 구성되어 있는지 확인해야 합니다. 
* ExpressRoute 회로와 다른 구독의 가상 네트워크, 게이트웨이 및 가상 네트워크 내 연결된 배포는 마이그레이션을 완료하는 데 가동 중지 시간이 필요합니다. 문서의 마지막 섹션에서는 리소스 마이그레이션을 위해 따라야 하는 단계를 설명합니다.
* ExpressRoute 게이트웨이와 VPN Gateway가 모두 있는 가상 네트워크는 마이그레이션할 수 없습니다.
* ExpressRoute 회로 구독 간 마이그레이션은 지원되지 않습니다. 자세한 내용은 [Microsoft 네트워크 이동 지원](../azure-resource-manager/management/move-support-resources.md#microsoftnetwork)을 참조 하세요.

## <a name="move-an-expressroute-circuit-from-classic-to-resource-manager"></a>클래식에서 Resource Manager로 ExpressRoute 회로 이동
ExpressRoute 회로에 연결된 리소스를 마이그레이션하기 전에 ExpressRoute 회로를 클래식에서 Resource Manager 환경으로 이동해야 합니다. 이 작업을 완료하려면 다음 문서를 참조하세요.

* [클래식에서 Resource Manager로 ExpressRoute 회로 이동](expressroute-move.md)에서 제공되는 정보를 검토합니다.
* [Azure PowerShell을 사용하여 클래식에서 Resource Manager로 회로를 이동합니다](expressroute-howto-move-arm.md).
* Azure Service 관리 포털을 사용합니다. [새 ExpressRoute 회로 만들기](expressroute-howto-circuit-portal-resource-manager.md)에 대한 워크플로를 수행하고 가져오기 옵션을 선택할 수 있습니다. 

이 작업에는 가동 중지 시간이 없습니다. 마이그레이션을 진행하는 동안 프레미스와 Microsoft 간 데이터 전송을 계속할 수 있습니다.

## <a name="migrate-virtual-networks-gateways-and-associated-deployments"></a>가상 네트워크, 게이트웨이 및 연결된 배포 마이그레이션

마이그레이션을 위해 수행하는 단계는 리소스가 동일한 구독에 있는지, 서로 다른 구독에 있는지 또는 둘 다에 따라 달라집니다.

### <a name="migrate-virtual-networks-gateways-and-associated-deployments-in-the-same-subscription-as-the-expressroute-circuit"></a>ExpressRoute 회로와 동일한 구독의 가상 네트워크, 게이트웨이 및 연결된 배포 마이그레이션
이 섹션에서는 ExpressRoute 회로와 동일한 구독의 가상 네트워크, 게이트웨이 및 연결된 배포를 배포하기 위한 단계를 설명합니다. 이 마이그레이션은 가동 중지 시간이 없습니다. 마이그레이션 프로세스 동안 모든 리소스를 계속 사용할 수 있습니다. 마이그레이션이 진행되는 동안에는 관리 평면이 잠겨 있습니다. 

1. ExpressRoute 회로를 클래식에서 Resource Manager 환경으로 이동하였는지 확인합니다.
2. 가상 네트워크를 마이그레이션에 적합하게 준비하였는지 확인합니다.
3. 리소스 마이그레이션을 위해 구독을 등록합니다. 리소스 마이그레이션을 위해 구독을 등록하려면 다음 PowerShell 코드 조각을 사용합니다.

   ```powershell 
   Select-AzSubscription -SubscriptionName <Your Subscription Name>
   Register-AzResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
   Get-AzResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
   ```
4. 유효성을 검사하고 준비한 후, 마이그레이션합니다. 가상 네트워크를 이동하려면 다음 PowerShell 코드 조작을 사용합니다.

   ```powershell
   Move-AzureVirtualNetwork -Validate -VirtualNetworkName $vnetName
   Move-AzureVirtualNetwork -Prepare -VirtualNetworkName $vnetName
   Move-AzureVirtualNetwork -Commit -VirtualNetworkName $vnetName
   ```

   또한 다음 PowerShell cmdlet을 실행하여 마이그레이션을 중단할 수 있습니다.

   ```powershell
   Move-AzureVirtualNetwork -Abort $vnetName
   ```

## <a name="next-steps"></a>다음 단계
* [클래식에서 Azure Resource Manager로 IaaS 리소스의 플랫폼 지원 마이그레이션](../virtual-machines/windows/migration-classic-resource-manager-overview.md)
* [클래식에서 Azure Resource Manager로의 플랫폼 지원 마이그레이션에 대한 기술 정보](../virtual-machines/migration-classic-resource-manager-deep-dive.md)
* [FAQ: 클래식에서 Azure Resource Manager로 IaaS 리소스의 플랫폼 지원 마이그레이션](../virtual-machines/migration-classic-resource-manager-faq.md)
* [가장 일반적인 마이그레이션 오류 및 완화 방법 검토](../virtual-machines/windows/migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
