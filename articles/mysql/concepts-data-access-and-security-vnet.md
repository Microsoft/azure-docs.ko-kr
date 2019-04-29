---
title: Azure Database for MySQL 서버 VNet 서비스 엔드포인트 개요 | Microsoft Docs
description: Azure Database for MySQL 서버에서 VNet 서비스 엔드포인트가 작동하는 방법을 설명합니다.
author: bolzmj
ms.author: mbolz
manager: jhubbard
ms.service: mysql
ms.topic: conceptual
ms.date: 08/20/2018
ms.openlocfilehash: 3a7eaacc4c234ec7d1d3d88455bd423256a07e90
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60614853"
---
# <a name="use-virtual-network-service-endpoints-and-rules-for-azure-database-for-mysql"></a>Azure Database for MySQL에서 Virtual Network 서비스 엔드포인트 및 규칙 사용

*가상 네트워크 규칙*은 Azure Database for MySQL 서버가 가상 네트워크의 특정 서브넷에서 보낸 통신을 수락할지 여부를 제어하는 하나의 방화벽 보안 기능입니다. 이 아티클에서는 경우에 따라 가상 네트워크 규칙 기능이 Azure Database for MySQL 서버에 대한 통신을 안전하게 허용하기 위한 가장 좋은 옵션인 이유를 설명합니다.

가상 네트워크 규칙을 만들려면 먼저 참조할 규칙에 대한 VNet([가상 네트워크 서비스 끝점][vm-virtual-network-overview]) 및 [가상 네트워크 서비스 엔드포인트][vm-virtual-network-service-endpoints-overview-649d]가 있어야 합니다. 다음 그림에서는 Virtual Network 서비스 엔드포인트가 Azure Database for MySQL에서 작동하는 방법을 보여줍니다.

![VNet 서비스 엔드포인트 작동 방식 예제](media/concepts-data-access-and-security-vnet/vnet-concept.png)

> [!NOTE]
> 이 기능은 범용 및 메모리 최적화 서버에 대해 Azure Database for MySQL이 배포된 모든 Azure 지역에서 사용할 수 있습니다.
> VNet 피어링의 경우 서비스 엔드포인트가 있는 공통 VNet 게이트웨이를 통해 트래픽이 이동하며 피어로 이동되어야 하는 경우 게이트웨이 VNet의 Azure Virtual Machines가 Azure Database for MySQL 서버에 액세스할 수 있도록 허용하는 ACL/VNet 규칙을 만드세요.

<a name="anch-terminology-and-description-82f" />

## <a name="terminology-and-description"></a>용어 및 설명

**가상 네트워크:** Azure 구독과 연결된 가상 네트워크가 있을 수 있습니다.

**서브넷:** 가상 네트워크에 **서브넷**이 포함됩니다. 소유한 Azure VM(가상 머신)은 서브넷에 할당됩니다. 하나의 서브넷에 여러 VM 또는 다른 계산 노드가 포함될 수 있습니다. 액세스를 허용하도록 보안을 구성해야 가상 네트워크 외부의 계산 노드가 가상 네트워크에 액세스할 수 있습니다.

**Virtual Network 서비스 엔드포인트:** [Virtual Network 서비스 엔드포인트][vm-virtual-network-service-endpoints-overview-649d]는 속성 값에 하나 이상의 정식 Azure 서비스 유형 이름이 포함된 서브넷입니다. 이 문서에서는 SQL Database라는 Azure 서비스를 나타내는 **Microsoft.Sql**의 형식 이름을 살펴봅니다. 이 서비스 태그는 Azure Database for MySQL 및 PostgreSQL 서비스에도 적용됩니다. **Microsoft.Sql** 서비스 태그를 VNet 서비스 엔드포인트에 적용하는 경우 서브넷에서 모든 Azure SQL Database, Azure Database for MySQL 및 Azure Database for PostgreSQL 서버에 대한 서비스 엔드포인트 트래픽을 구성해야 합니다. 

**가상 네트워크 규칙:** Azure Database for MySQL 서버에 대한 가상 네트워크 규칙은 Azure Database for MySQL 서버의 ACL(액세스 제어 목록)에 나열된 서브넷입니다. Azure Database for MySQL 서버에 대한 ACL에 나열되려면 서브넷에는 **Microsoft.Sql** 형식 이름이 있어야 합니다.

가상 네트워크 규칙은 서브넷에 있는 모든 노드에서 보낸 통신을 수락하도록 Azure Database for MySQL 서버에 지시합니다.







<a name="anch-benefits-of-a-vnet-rule-68b" />

## <a name="benefits-of-a-virtual-network-rule"></a>가상 네트워크 규칙의 이점

작업을 수행할 때까지 서브넷의 VM은 Azure Database for MySQL 서버와 통신할 수 없습니다. 통신을 설정하는 한 동작은 가상 네트워크 규칙을 생성하는 것입니다. VNet 규칙 접근 방식을 선택하는 원리에는 방화벽에서 제공된 경쟁 보안 옵션에 관련된 비교-대비 토론이 필요합니다.

### <a name="a-allow-access-to-azure-services"></a>a. Azure 서비스에 대한 액세스 허용

연결 보안 창에는 **Azure 서비스에 대한 액세스 허용**이라고 표시된 **켜기/끄기** 단추가 있습니다. **켜기** 설정은 모든 Azure IP 주소와 모든 Azure 서브넷에서 보낸 통신을 허용합니다. 이러한 Azure IP 또는 서브넷은 사용자가 소유할 수 없습니다. 이 **켜기** 설정은 Azure Database for MySQL에 필요한 것보다 더 개방적일 수 있습니다. 가상 네트워크 규칙 기능으로 훨씬 더 세밀하게 제어할 수 있습니다.

### <a name="b-ip-rules"></a>B. IP 규칙

Azure Database for MySQL 방화벽을 사용하면 Azure Database for MySQL 데이터베이스에 대한 통신이 수락되는 IP 주소 범위를 지정할 수 있습니다. 이 방법은 Azure 개인 네트워크 외부에 있는 안정적인 IP 주소에 적합합니다. 하지만 Azure 개인 네트워크 내부의 많은 노드는 *동적* IP 주소로 구성됩니다. 동적 IP 주소는, 예를 들어 VM이 다시 시작될 때 변경될 수 있습니다. 프로덕션 환경의 방화벽 규칙에서는 동적 IP 주소를 지정하면 안 됩니다.

VM에 대한 *정적* IP 주소를 가져와서 IP 옵션을 복원할 수 있습니다. 자세한 내용은 [Azure Portal을 사용하여 가상 머신에 대한 개인 IP 주소 구성][vm-configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-portal-321w]을 참조하세요.

그러나 정적 IP 방법은 관리가 어려워질 수 있고 대규모로 이루어질 경우 비용이 많이 듭니다. 가상 네트워크 규칙은 설정 및 관리가 더 쉽습니다.

### <a name="c-cannot-yet-have-azure-database-for-mysql-on-a-subnet-without-defining-a-service-endpoint"></a>C. 서비스 엔드포인트를 정의하지 않고 서브넷에서 Azure Database for MySQL을 설치할 수 없습니다.

**Microsoft.Sql** 서버가 가상 네트워크에 있는 서브넷의 노드인 경우 가상 네트워크 내의 모든 노드가 Azure Database for MySQL 서버와 통신할 수 있습니다. 이 경우에 VM은 가상 네트워크 규칙이나 IP 규칙이 없이도 Azure Database for MySQL과 통신할 수 있습니다.

그러나 2018년 8월 현재, Azure Database for MySQL 서버는 아직 서브넷에 할당될 수 있는 서비스가 아닙니다.

<a name="anch-details-about-vnet-rules-38q" />

## <a name="details-about-virtual-network-rules"></a>가상 네트워크 규칙에 대한 세부 정보

이 섹션에서는 가상 네트워크 규칙에 대한 여러 가지 세부 정보를 설명합니다.

### <a name="only-one-geographic-region"></a>단 하나의 지리적 지역

각 Virtual Network 서비스 엔드포인트는 하나의 Azure 지역에만 적용됩니다. 엔드포인트를 사용하여 다른 지역이 서브넷에서 보낸 통신을 수락하도록 할 수 없습니다.

가상 네트워크 규칙은 기본 엔드포인트가 적용되는 지역으로 제한됩니다.

### <a name="server-level-not-database-level"></a>데이터베이스 수준이 아님, 서버 수준

각 가상 네트워크 규칙은 서버에 있는 하나의 특정 데이터베이스가 아니라 전체 Azure Database for MySQL 서버에 적용됩니다. 즉, 가상 네트워크 규칙은 데이터베이스 수준이 아니라 서버 수준에서 적용됩니다.

### <a name="security-administration-roles"></a>보안 관리 역할

Virtual Network 서비스 엔드포인트 관리에는 보안 역할 분리가 있습니다. 다음과 같은 각 역할의 작업이 필요합니다.

- **네트워크 관리자:** &nbsp; 엔드포인트를 켭니다.
- **데이터베이스 관리자:** &nbsp; ACL(액세스 제어 목록)을 업데이트하여 제공된 서브넷을 Azure Database for MySQL 서버에 추가합니다.

*RBAC 대체:*

네트워크 관리자 및 데이터베이스 관리자 역할에는 가상 네트워크 규칙을 관리하는 데 필요한 것보다 많은 기능이 포함됩니다. 해당 기능의 하위 집합만 필요합니다.

Azure에서 [RBAC(역할 기반 액세스 제어)][rbac-what-is-813s]를 사용하여 기능의 필요한 하위 집합만 포함된 단일 사용자 지정 역할을 만들 수도 있습니다. 네트워크 관리자 또는 데이터베이스 관리자를 포함하는 대신 사용자 지정 역할을 사용할 수 있습니다. 사용자 지정 역할에 사용자를 추가할 경우 다른 두 개의 주요 관리자 역할에 사용자를 추가하는 것보다 보안 노출의 노출 영역이 감소합니다.

> [!NOTE]
> Azure Database for MySQL 및 VNet 서브넷이 서로 다른 구독에 있는 경우도 있습니다. 이러한 경우에는 다음과 같은 구성을 확인해야 합니다.
> - 두 구독은 모두 동일한 Azure Active Directory 테넌트에 있어야 합니다.
> - 서비스 엔드포인트를 사용하도록 설정하고 지정된 서버에 VNet 서브넷을 추가하는 등의 작업을 시작하는 데 필요한 권한이 사용자에게 있습니다.

## <a name="limitations"></a>제한 사항

Azure Database for MySQL의 경우 가상 네트워크 규칙 기능에는 다음과 같은 제한이 있습니다.

- 웹앱을 VNet/서브넷의 개인 IP에 매핑할 수 있습니다. 서비스 엔드포인트가 지정된 VNet/서브넷에서 ON으로 설정되어 있는 경우에도 웹앱과 서버 간 연결은 VNet/서브넷 원본이 아닌 Azure 공용 IP 원본을 가집니다. VNet 방화벽 규칙이 있는 서버에 웹 앱에서 연결을 사용 하려면 허용 Azure 서비스가 서버에서 서버에 액세스 해야 합니다.

- Azure Database for MySQL에 대한 방화벽에서 각 가상 네트워크 규칙은 서브넷을 참조합니다. 이렇게 참조된 모든 서브넷은 Azure Database for MySQL을 호스팅하는 동일한 지리적 위치에서 호스팅되어야 합니다.

- 각 Azure Database for MySQL 서버에는 특정 가상 네트워크에 대해 최대 128개 ACL 항목이 포함될 수 있습니다.

- 가상 네트워크 규칙은 Azure Resource Manager 가상 네트워크에만 적용되고 [클래식 배포 모델][arm-deployment-model-568f] 네트워크에는 적용되지 않습니다.

- **Microsoft.Sql** 서비스 태그를 사용하여 Azure Database for MySQL에 가상 네트워크 서비스 엔드포인트를 설정하면 모든 Azure Database 서비스 (Azure Database for MySQL, Azure Database for PostgreSQL, Azure SQL Database 및 Azure SQL Data Warehouse)에 엔드포인트를 사용하도록 설정할 수 있습니다.

- VNet 서비스 엔드포인트는 범용 및 메모리 최적화 서버에 대해서만 지원됩니다.

- 방화벽에서 IP 주소 범위는 다음 네트워킹 항목에 적용되지만 가상 네트워크 규칙에는 적용되지 않습니다.
    - [S2S(사이트 간) VPN(가상 사설망)][vpn-gateway-indexmd-608y]
    - [ExpressRoute][expressroute-indexmd-744v]를 통한 온-프레미스

## <a name="expressroute"></a>ExpressRoute

네트워크가 [ExpressRoute][expressroute-indexmd-744v]의 사용을 통해 Azure 네트워크에 연결된 경우 각 회로는 Microsoft Edge에서 두 개의 공용 IP 주소로 구성됩니다. 두 개의 IP 주소는 Azure 공용 피어링을 사용하여 Azure Storage와 같은 Microsoft 서비스에 연결하는 데 사용됩니다.

회로에서 Azure Database for MySQL의 통신을 허용하려면 회로의 공용 IP 주소에 대한 IP 네트워크 규칙을 만들어야 합니다. ExpressRoute 회로의 공용 IP 주소를 찾기 위해 Azure Portal을 사용하여 ExpressRoute에서 지원 티켓을 엽니다.

## <a name="adding-a-vnet-firewall-rule-to-your-server-without-turning-on-vnet-service-endpoints"></a>VNET 서비스 엔드포인트를 켜지 않고 서버에 VNET 방화벽 규칙 추가

단순히 방화벽 규칙을 설정하는 것은 VNet에 서버를 보호하는 데 도움이 되지 않습니다. 보안이 효력을 나타내려면 VNet 서비스 엔드포인트도 **켜야** 합니다. 서비스 엔드포인트를 **켜면** 전환을 **끈** 상태에서 **켠** 상태로 완료할 때까지 VNet 서브넷에서 가동 중지 시간이 발생합니다. 이는 특히 대규모 VNet의 컨텍스트에 적용됩니다. **IgnoreMissingServiceEndpoint** 플래그를 사용하여 전환 시 가동 중지 시간을 줄이거나 없앨 수 있습니다.

Azure CLI 또는 Azure Portal을 사용하여 **IgnoreMissingServiceEndpoint** 플래그를 설정할 수 있습니다.

## <a name="related-articles"></a>관련 문서
- [Azure 가상 네트워크][vm-virtual-network-overview]
- [Azure 가상 네트워크 서비스 엔드포인트][vm-virtual-network-service-endpoints-overview-649d]

## <a name="next-steps"></a>다음 단계
VNet 규칙 만들기에 대한 아티클은 다음을 참조하세요.
- [Azure Portal을 사용하여 Azure Database for MySQL VNet 규칙 만들기 및 관리](howto-manage-vnet-using-portal.md)
- [Azure CLI를 사용하여 Azure Database for MySQL VNet 규칙 만들기 및 관리](howto-manage-vnet-using-cli.md)

<!-- Link references, to text, Within this same GitHub repo. -->
[arm-deployment-model-568f]: ../azure-resource-manager/resource-manager-deployment-model.md

[vm-virtual-network-overview]: ../virtual-network/virtual-networks-overview.md

[vm-virtual-network-service-endpoints-overview-649d]: ../virtual-network/virtual-network-service-endpoints-overview.md

[vm-configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-portal-321w]: ../virtual-network/virtual-networks-static-private-ip-arm-pportal.md

[rbac-what-is-813s]: ../active-directory/role-based-access-control-what-is.md

[vpn-gateway-indexmd-608y]: ../vpn-gateway/index.yml

[expressroute-indexmd-744v]: ../expressroute/index.yml
