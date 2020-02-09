---
title: Azure Database for MariaDB에 대 한 개인 링크 (미리 보기)
description: Azure Database for MariaDB에 대 한 개인 링크의 작동 방식에 대해 알아봅니다.
author: kummanish
ms.author: manishku
ms.service: mariadb
ms.topic: conceptual
ms.date: 01/09/2020
ms.openlocfilehash: 92d7522c8382ded182c5f482df3f3d917b4b3a14
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/15/2020
ms.locfileid: "75982375"
---
# <a name="private-link-for-azure-database-for-mariadb-preview"></a>Azure Database for MariaDB에 대 한 개인 링크 (미리 보기)

개인 링크를 사용 하면 개인 엔드포인트을 통해 Azure의 다양 한 PaaS 서비스에 연결할 수 있습니다. Azure 개인 링크는 기본적으로 VNet (개인 Virtual Network) 내에 Azure 서비스를 제공 합니다. PaaS 리소스는 VNet의 다른 리소스와 마찬가지로 개인 IP 주소를 사용 하 여 액세스할 수 있습니다.

개인 링크 기능을 지 원하는 PaaS 서비스 목록을 보려면 개인 링크 [설명서](https://docs.microsoft.com/azure/private-link/index)를 검토 하세요. 프라이빗 엔드포인트는 특정 [VNet](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) 및 서브넷 내의 개인 IP 주소입니다.

> [!NOTE]
> 이 기능은 Azure Database for MariaDB 범용 및 메모리 액세스에 최적화 된 가격 책정 계층을 지 원하는 모든 Azure 지역에서 사용할 수 있습니다.

## <a name="data-exfiltration-prevention"></a>데이터 반출 방지

Azure Database for MariaDB의 데이터 필터링은 데이터베이스 관리자와 같은 권한 있는 사용자가 한 시스템에서 데이터를 추출 하 여 조직 외부의 다른 위치나 시스템으로 이동할 수 있는 경우입니다. 예를 들어 사용자는 데이터를 타사 소유의 스토리지 계정으로 이동합니다.

Azure Database for MariaDB 인스턴스에 연결 하는 Azure VM 내에서 Aadb 워크 벤치를 실행 하는 사용자에 대 한 시나리오를 고려 합니다. 이 MariaDB 인스턴스는 미국 서 부 데이터 센터에 있습니다. 아래 예제에서는 네트워크 액세스 제어를 사용 하 여 Azure Database for MariaDB에서 공용 엔드포인트에 대 한 액세스를 제한 하는 방법을 보여 줍니다.

* Azure 서비스 허용을 OFF로 설정 하 여 공용 엔드포인트을 통해 Azure Database for MariaDB에 대 한 모든 Azure 서비스 트래픽을 사용 하지 않도록 설정 합니다. [방화벽 규칙](https://docs.microsoft.com/azure/mariadb/concepts-firewall-rules) 또는 [가상 네트워크 서비스 엔드포인트](https://docs.microsoft.com/azure/mariadb/concepts-data-access-security-vnet)을 통해 서버에 액세스할 수 있는 IP 주소 또는 범위가 없어야 합니다.

* VM의 개인 IP 주소를 사용 하 여 Azure Database for MariaDB에 대 한 트래픽만 허용 합니다. 자세한 내용은 [서비스 엔드포인트](concepts-data-access-security-vnet.md) 및 [VNet 방화벽 규칙](howto-manage-vnet-portal.md) 문서를 참조하세요.

* Azure VM에서 다음과 같이 NSGs (네트워크 보안 그룹) 및 서비스 태그를 사용 하 여 나가는 연결의 범위를 좁힙니다.

    * 서비스 태그 = SQL에 대 한 트래픽을 허용 하도록 NSG 규칙을 지정 합니다. WestUs-미국 서 부에서 Azure Database for MariaDB에 대 한 연결만 허용
    * 서비스 태그 = 모든 지역에서 MariaDB 데이터베이스에 대 한 연결을 거부 하는 NSG 규칙 (우선 순위가 높음)을 지정 합니다.</br></br>

이 설정이 완료 되 면 Azure VM은 미국 서 부 지역의 Azure Database for MariaDB에만 연결할 수 있습니다. 그러나 연결은 단일 Azure Database for MariaDB으로 제한 되지 않습니다. VM은 구독에 포함 되지 않은 데이터베이스를 포함 하 여 미국 서 부 지역의 모든 Azure Database for MariaDB에 계속 연결할 수 있습니다. 위의 시나리오에서 데이터 반출의 범위를 특정 지역으로 좁혔지만 완전히 제거하지는 않았습니다.</br>

이제 개인 링크를 사용 하 여 개인 엔드포인트에 대 한 액세스를 제한 하는 NSGs와 같은 네트워크 액세스 제어를 설정할 수 있습니다. 그러면 개별 Azure PaaS 리소스가 특정 프라이빗 엔드포인트에 매핑됩니다. 악의적인 참가자는 매핑된 PaaS 리소스 (예: Azure Database for MariaDB)에만 액세스할 수 있으며 다른 리소스는 액세스할 수 없습니다.

## <a name="on-premises-connectivity-over-private-peering"></a>프라이빗 피어링을 통한 온-프레미스 연결

온-프레미스 컴퓨터에서 공용 엔드포인트에 연결 하는 경우 서버 수준 방화벽 규칙을 사용 하 여 ip 기반 방화벽에 IP 주소를 추가 해야 합니다. 이 모델은 개발 또는 테스트 워크로드를 위해 개별 머신에 액세스할 수 있도록 하는 데 효과적이지만 프로덕션 환경에서는 관리하기가 어렵습니다.

개인 링크를 사용 하 여 ER ( [Express Route](https://azure.microsoft.com/services/expressroute/) ), 개인 피어 링 또는 [VPN 터널](https://docs.microsoft.com/azure/vpn-gateway/)을 사용 하 여 개인 엔드포인트에 대 한 프레미스 간 액세스를 사용 하도록 설정할 수 있습니다. 이후에는 공용 엔드포인트을 통해 모든 액세스를 사용 하지 않도록 설정 하 고 IP 기반 방화벽을 사용 하지 않을 수 있습니다.

## <a name="configure-private-link-for-azure-database-for-mariadb"></a>Azure Database for MariaDB에 대 한 개인 링크 구성

### <a name="creation-process"></a>만들기 프로세스

개인 엔드포인트은 개인 링크를 사용 하도록 설정 하는 데 필요 합니다. 다음 방법 가이드를 사용 하 여이 작업을 수행할 수 있습니다.

* [Azure Portal](https://docs.microsoft.com/azure/mariadb/howto-configure-privatelink-portal)
* [CLI](https://docs.microsoft.com/azure/mariadb/howto-configure-privatelink-cli)

### <a name="approval-process"></a>승인 프로세스

네트워크 관리자가 PE (개인 엔드포인트)를 만든 후 관리자는 PEC (개인 엔드포인트 연결)를 관리 하 여 Azure Database for MariaDB 수 있습니다.

> [!NOTE]
> 현재 Azure Database for MariaDB에서는 개인 엔드포인트에 대 한 자동 승인만 지원 합니다.

* Azure Portal에서 Azure Database for MariaDB 서버 리소스로 이동 합니다. 
    * 왼쪽 창에서 개인 엔드포인트 연결을 선택 합니다.
    * 모든 개인 엔드포인트 연결의 목록을 표시 합니다 (PECs).
    * 만든 해당 개인 엔드포인트 (PE)

![개인 엔드포인트 포털을 선택 합니다.](media/concepts-data-access-and-security-private-link/select-private-link-portal.png)

* 목록에서 개별 PEC를 선택합니다.

![개인 엔드포인트 승인 보류 중을 선택 합니다.](media/concepts-data-access-and-security-private-link/select-private-link.png)

* MariaDB 서버 관리자는 PEC를 승인 또는 거부 하 고 선택적으로 짧은 텍스트 응답을 추가 하도록 선택할 수 있습니다.

![개인 엔드포인트 메시지 선택](media/concepts-data-access-and-security-private-link/select-private-link-message.png)

* 승인 또는 거부 후에는 목록에 응답 텍스트와 함께 적절 한 상태가 반영 됩니다.

![개인 엔드포인트 최종 상태를 선택 합니다.](media/concepts-data-access-and-security-private-link/show-private-link-approved-connection.png)

## <a name="use-cases-of-private-link-for-azure-database-for-mariadb"></a>Azure Database for MariaDB에 대 한 개인 링크 사용 사례

클라이언트는 동일한 VNet에서 프라이빗 엔드포인트에 연결하거나, 동일한 지역에서 피어링된 VNet에 연결하거나, 여러 지역의 VNet 간 연결을 통해 연결할 수 있습니다. 또한 클라이언트는 ExpressRoute, 프라이빗 피어링 또는 VPN 터널링을 사용하여 온-프레미스에서 연결할 수 있습니다. 다음은 일반적인 사용 사례를 보여 주는 간소화된 다이어그램입니다.

![개인 엔드포인트 개요를 선택 합니다.](media/concepts-data-access-and-security-private-link/show-private-link-overview.png)

### <a name="connecting-from-an-azure-vm-in-peered-virtual-network-vnet"></a>피어링된 VNet(가상 네트워크)의 Azure VM에서 연결
[Vnet 피어 링](https://docs.microsoft.com/azure/virtual-network/tutorial-connect-virtual-networks-powershell) 을 구성 하 여 피어 링 Vnet의 Azure VM에서 Azure Database for MariaDB에 대 한 연결을 설정 합니다.

### <a name="connecting-from-an-azure-vm-in-vnet-to-vnet-environment"></a>VNet 간 환경의 Azure VM에서 연결
[Vnet 간 VPN gateway 연결](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal) 을 구성 하 여 다른 지역 또는 구독의 Azure VM에서 Azure Database for MariaDB에 대 한 연결을 설정 합니다.

### <a name="connecting-from-an-on-premises-environment-over-vpn"></a>VPN을 통해 온-프레미스 환경에서 연결
온-프레미스 환경에서 Azure Database for MariaDB 연결을 설정 하려면 다음 옵션 중 하나를 선택 하 고 구현 합니다.

* [지점 및 사이트 간 연결](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps)
* [사이트 간 VPN 연결](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell)
* [ExpressRoute 회로](https://docs.microsoft.com/azure/expressroute/expressroute-howto-linkvnet-portal-resource-manager)

## <a name="private-link-combined-with-firewall-rules"></a>방화벽 규칙과 결합 된 개인 링크

방화벽 규칙과 함께 개인 링크를 사용 하는 경우 다음과 같은 상황 및 결과가 발생할 수 있습니다.

* 방화벽 규칙을 구성 하지 않으면 기본적으로 트래픽이 Azure Database for MariaDB에 액세스할 수 없게 됩니다.

* 공용 트래픽 또는 서비스 엔드포인트을 구성 하 고 개인 엔드포인트을 만드는 경우 해당 유형의 방화벽 규칙에 따라 들어오는 트래픽 유형이 서로 다릅니다.

* 공용 트래픽 또는 서비스 엔드포인트을 구성 하지 않고 개인 엔드포인트을 만드는 경우 전용 엔드포인트을 통해서만 Azure Database for MariaDB에 액세스할 수 있습니다. 공용 트래픽 또는 서비스 엔드포인트을 구성 하지 않은 경우 승인 된 모든 개인 엔드포인트을 거부 하거나 삭제 한 후에는 어떤 트래픽도 Azure Database for MariaDB에 액세스할 수 없게 됩니다.

## <a name="next-steps"></a>다음 단계

Azure Database for MariaDB 보안 기능에 대해 자세히 알아보려면 다음 문서를 참조 하세요.

* Azure Database for MariaDB에 대 한 방화벽을 구성 하려면 [방화벽 지원](https://docs.microsoft.com/azure/mariadb/concepts-firewall-rules)을 참조 하세요.

* Azure Database for MariaDB에 대 한 가상 네트워크 서비스 엔드포인트을 구성 하는 방법을 알아보려면 [가상 네트워크에서 액세스 구성](https://docs.microsoft.com/azure/mariadb/concepts-data-access-security-vnet)을 참조 하세요.

* Azure Database for MariaDB 연결에 대 한 개요는 [Azure Database for MariaDB 연결 아키텍처](https://docs.microsoft.com/azure/MariaDB/concepts-connectivity-architecture) 를 참조 하세요.
