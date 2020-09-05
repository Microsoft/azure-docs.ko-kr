---
title: 가상 WAN에서 개인 링크 서비스 공유
titleSuffix: Azure Virtual WAN
description: 가상 WAN에서 개인 링크를 구성 하는 단계
services: virtual-wan
author: erjosito
ms.service: virtual-wan
ms.topic: how-to
ms.date: 08/18/2020
ms.author: jomore
ms.custom: fasttrack-new
ms.openlocfilehash: 98142e3a8904bcbb0352fa768fc72966412dae0b
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88590683"
---
# <a name="use-private-link-in-virtual-wan"></a>가상 WAN에서 개인 링크 사용

[Azure 개인 링크](../private-link/private-link-overview.md) 는 개인 [끝점](../private-link/private-endpoint-overview.md)을 노출 하 여 개인 IP 주소 연결을 통해 Azure 플랫폼 서비스 제공 서비스를 연결 하는 데 사용할 수 있는 기술입니다. Azure 가상 WAN을 사용 하면 가상 허브에 연결 된 가상 네트워크 중 하나에 개인 끝점을 배포할 수 있습니다. 이는 동일한 가상 WAN에 연결 된 다른 가상 네트워크 또는 분기에 대 한 연결을 제공 합니다.

## <a name="before-you-begin"></a>시작하기 전 주의 사항

이 문서의 단계에서는 하나 이상의 허브 및 가상 WAN에 연결 된 두 개 이상의 가상 네트워크를 사용 하 여 가상 WAN을 이미 배포 했다고 가정 합니다.

새 Virtual WAN 및 새 허브를 만들려면 다음 문서의 단계를 수행합니다.

* [Virtual WAN 만들기](virtual-wan-site-to-site-portal.md#openvwan)
* [허브 만들기](virtual-wan-site-to-site-portal.md#hub)
* [허브에 VNet 연결](virtual-wan-site-to-site-portal.md#hub)

## <a name="create-a-private-link-endpoint"></a><a name="endpoint"></a>개인 링크 엔드포인트 만들기

다양 한 서비스에 대 한 개인 링크 끝점을 만들 수 있습니다. 이 예제에서는 Azure SQL Database를 사용 합니다. Azure SQL Database에 대 한 개인 끝점을 만드는 방법에 대 한 자세한 내용은 빠른 시작 [: Azure Portal를 사용 하 여 개인 끝점 만들기를 사용 하](../private-link/create-private-endpoint-portal.md)여 확인할 수 있습니다. 다음 이미지는 Azure SQL Database의 네트워크 구성을 보여 줍니다.

:::image type="content" source="./media/howto-private-link/create-private-link.png" alt-text="개인 링크 만들기" lightbox="./media/howto-private-link/create-private-link.png":::

Azure SQL Database를 만든 후 개인 끝점을 검색 하는 개인 끝점 IP 주소를 확인할 수 있습니다.

:::image type="content" source="./media/howto-private-link/endpoints.png" alt-text="전용 끝점" lightbox="./media/howto-private-link/endpoints.png":::

만든 개인 끝점을 클릭 하면 해당 개인 IP 주소 및 FQDN (정규화 된 도메인 이름)이 표시 됩니다. 개인 끝점에는 배포 된 VNet 범위 (10.1.3.0/24)의 IP 주소가 있습니다.

:::image type="content" source="./media/howto-private-link/sql-endpoint.png" alt-text="SQL 끝점" lightbox="./media/howto-private-link/sql-endpoint.png":::

## <a name="verify-connectivity-from-the-same-vnet"></a><a name="connectivity"></a>동일한 VNet에서 연결 확인

이 예제에서는 MS SQL tools가 설치 된 Ubuntu 가상 머신에서 Azure SQL Database에 대 한 연결을 확인 합니다. 첫 번째 단계는 DNS 확인이 작동 하 고 Azure SQL Database 정규화 된 도메인 이름이 개인 끝점이 배포 된 동일한 VNet (10.1.3.0/24)에서 개인 IP 주소로 확인 되는지 확인 하는 것입니다.

```bash
$ nslookup wantest.database.windows.net
Server:         127.0.0.53
Address:        127.0.0.53#53

Non-authoritative answer:
wantest.database.windows.net    canonical name = wantest.privatelink.database.windows.net.
Name:   wantest.privatelink.database.windows.net
Address: 10.1.3.228
```

이전 출력에서 볼 수 있듯이 FQDN은 `wantest.database.windows.net` 에 매핑되고, 개인 끝점을 `wantest.privatelink.database.windows.net` 따라 만든 개인 DNS 영역은 개인 IP 주소로 확인 됩니다 `10.1.3.228` . 개인 DNS 영역을 살펴보면 개인 IP 주소에 매핑된 개인 끝점에 대 한 A 레코드가 있는지 확인 합니다.

:::image type="content" source="./media/howto-private-link/dns-zone.png" alt-text="DNS 영역" lightbox="./media/howto-private-link/dns-zone.png":::

올바른 DNS 확인을 확인 한 후 데이터베이스에 연결을 시도할 수 있습니다.

```bash
$ query="SELECT CONVERT(char(15), CONNECTIONPROPERTY('client_net_address'));"
$ sqlcmd -S wantest.database.windows.net -U $username -P $password -Q "$query"

10.1.3.75
```

여기에서 볼 수 있듯이 SQL server가 클라이언트에서 볼 수 있는 원본 IP 주소를 제공 하는 특별 SQL 쿼리를 사용 하 고 있습니다. 이 경우 서버는 개인 IP ()를 사용 하 여 클라이언트를 확인 합니다 `10.1.3.75` . 즉, 트래픽은 공용 인터넷을 통해 이동 하지 않고 개인 끝점으로 바로 이동 합니다.

`username` `password` 이 가이드의 예제를 사용 하려면 및 변수를 Azure SQL Database에 정의 된 자격 증명과 일치 하도록 설정 해야 합니다.

## <a name="connect-from-a-different-vnet"></a><a name="vnet"></a>다른 VNet에서 연결

이제 Azure 가상 WAN의 한 VNet이 개인 끝점에 연결 되어 있으므로 가상 WAN에 연결 된 다른 모든 Vnet 분기도 액세스할 수 있습니다. 임의의 [시나리오](scenario-any-to-any.md) 또는 [공유 서비스 VNet 시나리오](scenario-shared-services-vnet.md)와 같이 Azure 가상 WAN에서 지 원하는 모든 모델을 통해 연결을 제공 하 여 두 가지 예를 이름을 지정 해야 합니다.

개인 끝점이 배포 된 VNet에 VNet 또는 분기를 연결 했으면 DNS 확인을 구성 해야 합니다.

* VNet에서 개인 끝점에 연결 하는 경우 Azure SQL Database를 사용 하 여 만든 것과 동일한 개인 영역을 사용할 수 있습니다.
* 분기 (사이트 간 VPN, 지점 및 사이트 간 VPN 또는 Express 경로)에서 개인 끝점에 연결 하는 경우 온-프레미스 DNS 확인을 사용 해야 합니다.

이 예제에서는 다른 VNet에서 연결 합니다. 따라서 먼저 개인 IP 주소에 대 한 Azure SQL Database 정규화 된 도메인 이름을 확인할 수 있도록 개인 DNS 영역을 새 VNet에 연결 합니다. 이 작업은 개인 DNS 영역을 새 VNet에 연결 하 여 수행 됩니다.

:::image type="content" source="./media/howto-private-link/dns-link.png" alt-text="DNS 링크" lightbox="./media/howto-private-link/dns-link.png":::

이제 연결 된 VNet의 모든 가상 컴퓨터가 개인 링크의 개인 IP 주소에 대 한 Azure SQL Database FQDN을 올바르게 확인 해야 합니다.

```bash
$ nslookup wantest.database.windows.net
Server:         127.0.0.53
Address:        127.0.0.53#53

Non-authoritative answer:
wantest.database.windows.net    canonical name = wantest.privatelink.database.windows.net.
Name:   wantest.privatelink.database.windows.net
Address: 10.1.3.228
```

이 VNet (10.1.1.0/24)이 개인 끝점이 구성 된 원래 VNet (10.1.3.0/24)에 연결 되어 있는지를 두 번 확인 하기 위해 VNet의 가상 머신에서 유효한 경로 테이블을 확인할 수 있습니다.

:::image type="content" source="./media/howto-private-link/effective-routes.png" alt-text="유효 경로" lightbox="./media/howto-private-link/effective-routes.png":::

여기에서 볼 수 있듯이 Azure Virtual WAN의 Virtual Network 게이트웨이에서 삽입 한 VNet 10.1.3.0/24를 가리키는 경로가 있습니다. 이제 데이터베이스에 대 한 연결을 테스트할 수 있습니다.

```bash
$ query="SELECT CONVERT(char(15), CONNECTIONPROPERTY('client_net_address'));"
$ sqlcmd -S wantest.database.windows.net -U $username -P $password -Q "$query"

10.1.1.75
```

이 예제에서는 가상 WAN에 연결 된 Vnet 중 하나에서 개인 끝점을 만드는 것이 가상 WAN의 Vnet 및 분기의 나머지 부분에 대 한 연결을 제공 하는 방법을 살펴보았습니다.

## <a name="next-steps"></a>다음 단계

Virtual WAN에 대한 자세한 내용은 [FAQ](virtual-wan-faq.md)를 참조하세요.
