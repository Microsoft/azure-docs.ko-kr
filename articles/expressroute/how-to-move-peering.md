---
title: 공용 피어링을 Microsoft 피어링으로 이동 - Azure ExpressRoute | Microsoft Docs
description: 이 문서에서는 ExpressRoute에서 공용 피어링을 Microsoft 피어링으로 이동하는 단계를 보여 줍니다.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: article
ms.date: 03/12/2018
ms.author: cherylmc
ms.custom: seodec18
ms.openlocfilehash: 6b2bce6b488698db0a72c9a17f67c2555c6afa5b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60883149"
---
# <a name="move-a-public-peering-to-microsoft-peering"></a>공용 피어링을 Microsoft 피어링으로 이동

이 문서에서는 가동 중지 없이 공용 피어링 구성을 Microsoft 피어링으로 이동할 수 있습니다. ExpressRoute는 Azure 스토리지 및 Azure SQL Database와 같은 Azure PaaS 서비스에 대한 경로 필터를 사용하여 Microsoft 피어링 사용을 지원합니다. Microsoft PaaS 및 SaaS 서비스에 액세스하려면 하나의 라우팅 도메인만 있으면 됩니다. 경로 필터를 사용하여 사용하려는 Azure 지역에 대한 PaaS 서비스 접두사를 선택적으로 보급할 수 있습니다. 라우팅 도메인 및 피어링에 대한 자세한 내용은 [ExpressRoute 회로 및 라우팅 도메인](expressroute-circuit-peerings.md)을 참조하세요.

## <a name="before"></a>시작하기 전에

* Microsoft 피어링에 연결하려면 NAT를 설정하고 관리해야 합니다. 연결 공급자에서 NAT를 관리 서비스로 설정하고 관리할 수 있습니다. Microsoft 피어링에서 Azure PaaS 및 Azure SaaS 서비스에 액세스하려는 경우 NAT IP 풀의 크기를 올바르게 조정해야 합니다. ExpressRoute용 NAT에 대한 자세한 내용은 [Microsoft 피어링에 대한 NAT 요구 사항](expressroute-nat.md#nat-requirements-for-microsoft-peering)을 참조하세요.

* 공용 피어링을 사용하고 있고 현재 [Azure Storage](../storage/common/storage-network-security.md)나 [Azure SQL Database](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md)에 액세스하는 데 사용되는 공용 IP 주소에 대한 IP 네트워크 규칙이 있는 경우, Microsoft 피어링으로 구성된 NAT IP 풀이 Azure 스토리지 계정 또는 Azure SQL 계정의 공용 IP 주소 목록에 포함되어 있는지 확인해야 합니다.

* 가동 중지 없이 Microsoft 피어링으로 이동하려면 이 문서의 단계를 제시된 순서대로 사용하십시오.

## <a name="create"></a>1. Microsoft 피어링 만들기

Microsoft 피어링을 만들지 않은 경우 다음 문서 중 하나를 사용하여 Microsoft 피어링을 만듭니다. 연결 공급자에서 3계층 관리 서비스를 제공하는 경우 회로에 대해 Microsoft 피어링을 사용하도록 연결 공급자에 요청할 수 있습니다.

  * [Azure Portal을 사용하여 Microsoft 피어링 만들기](expressroute-howto-routing-portal-resource-manager.md#msft)
  * [Azure Powershell을 사용하여 Microsoft 피어링 만들기](expressroute-howto-routing-arm.md#msft)
  * [Azure CLI를 사용하여 Microsoft 피어링 만들기](howto-routing-cli.md#msft)

## <a name="validate"></a>2. Microsoft 피어링 사용에 대한 유효성 검사

Microsoft 피어링을 사용하도록 설정되고 보급된 공용 접두사가 구성되어 있는지 확인합니다.

  * [Azure Portal](expressroute-howto-routing-portal-resource-manager.md#getmsft)
  * [Azure PowerShell](expressroute-howto-routing-arm.md#getmsft)
  * [Azure CLI](howto-routing-cli.md#getmsft)

## <a name="routefilter"></a>3. 회로에 경로 필터 구성 및 연결

새 Microsoft 피어링은 기본적으로 경로 필터가 회로에 연결될 때까지 어떤 접두사도 보급하지 않습니다. 경로 필터 규칙을 만들 때 다음 스크린샷과 같이 Azure PaaS 서비스에 사용하려는 Azure 지역에 대한 서비스 커뮤니티의 목록을 지정할 수 있습니다.

![공용 피어링 병합](./media/how-to-move-peering/public.png)

다음 문서 중 하나를 사용하여 경로 필터를 구성합니다.

  * [Azure Portal을 사용하여 Microsoft 피어링에 대한 경로 필터 구성](how-to-routefilter-portal.md)
  * [Azure PowerShell을 사용하여 Microsoft 피어링에 대한 경로 필터 구성](how-to-routefilter-powershell.md)
  * [Azure CLI를 사용하여 Microsoft 피어링에 대한 경로 필터 구성](how-to-routefilter-cli.md)

## <a name="delete"></a>4. 공용 피어링 삭제

Microsoft 피어링이 구성되어 있고 사용하려는 접두사가 Microsoft 피어링에서 올바르게 보급되었는지 확인한 후에는 공용 피어링을 삭제할 수 있습니다. 공용 피어링을 삭제하려면 다음 문서 중 하나를 사용합니다.

  * [Azure Portal을 사용하여 Azure 공용 피어링 삭제](expressroute-howto-routing-portal-resource-manager.md#deletepublic)
  * [Azure PowerShell을 사용하여 Azure 공용 피어링 삭제](expressroute-howto-routing-arm.md#deletepublic)
  * [CLI를 사용하여 Azure 공용 피어링 삭제](howto-routing-cli.md#deletepublic)
  
## <a name="view"></a>5. 피어링 보기
  
Azure Portal에서 모든 ExpressRoute 회로 및 피어링 목록을 확인할 수 있습니다. 자세한 내용은 [Microsoft 피어링 세부 정보 보기](expressroute-howto-routing-portal-resource-manager.md#getmsft)를 참조하세요.

## <a name="next-steps"></a>다음 단계

ExpressRoute에 대한 자세한 내용은 [ExpressRoute FAQ](expressroute-faqs.md)를 참조하세요.
