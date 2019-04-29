---
title: 보안 끝점을 공개 하는 관리 되는 인스턴스-Azure SQL Database 관리 되는 인스턴스 | Microsoft Docs
description: 관리 되는 인스턴스를 사용 하 여 Azure에서 공용 끝점을 안전 하 게 사용
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.topic: conceptual
author: WenJason
ms.author: v-jay
ms.reviewer: vanto, carlrab
manager: digimobile
origin.date: 04/16/2019
ms.date: 04/29/2019
ms.openlocfilehash: 9d5a3d18e8a7d3c5a6cb08e16e74dd4fbda9ca31
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61315058"
---
# <a name="using-azure-sql-database-managed-instance-securely-with-public-endpoint"></a>인스턴스 공용 끝점을 사용 하 여 안전 하 게 관리 되는 Azure SQL Database를 사용 하 여

Azure SQL Database 관리 되는 인스턴스를 통해 사용자 연결을 위해 사용할 수 [공용 끝점](../virtual-network/virtual-network-service-endpoints-overview.md)합니다. 이 문서에서는이 구성을 더욱 안전 하 게 하는 방법을 안내 합니다.

## <a name="scenarios"></a>시나리오

관리 되는 인스턴스는 해당 가상 네트워크 내에서 연결을 사용 하도록 개인 끝점을 제공 합니다. 기본 옵션을 최대 격리를 제공 하는 것입니다. 그러나 가지 공용 끝점 연결을 필요한 시나리오가 있습니다.

- 유일한 PaaS 제품을 다중 테 넌 트와 통합 합니다.
- VPN을 사용 하 여 보다 데이터 교환의 더 높은 처리량입니다.
- 회사 정책을 회사 네트워크 내에서 PaaS를 금지 합니다.

## <a name="deploying-managed-instance-for-public-endpoint-access"></a>끝점에 대 한 공용 액세스에 대 한 관리 되는 인스턴스를 배포합니다.

하지만 필수 사항은 아니지만, 공용 끝점 액세스를 사용 하 여 관리 되는 인스턴스에 대 한 일반 배포 모델을 격리 된 전용된 가상 네트워크의 인스턴스를 만드는 것입니다. 이 구성에서는 가상 네트워크는 가상 클러스터 격리에 대해서만 사용 됩니다. 회사 네트워크 IP 주소 공간을 사용 하 여 관리 되는 인스턴스 IP 주소 공간이 겹치면에 하지 관련이 있습니다.

## <a name="securing-data-in-motion"></a>동작 중인 데이터 보호

관리 되는 인스턴스 데이터 트래픽이 암호화 클라이언트 드라이버를 지 원하는 경우에 항상 암호화 됩니다. 관리 되는 인스턴스 및 다른 Azure Virtual Machines 또는 Azure 서비스 간에 데이터를 Azure의 백본을 벗어나지 않습니다. 관리 되는 인스턴스는 온-프레미스 네트워크 연결의 경우는 것이 좋습니다 Expressroute Microsoft 피어 링 사용 합니다. Expressroute 공용 인터넷을 통해 데이터를 이동 되지 않도록 하는 데 도움이 됩니다 (관리 되는 인스턴스 개인 연결에 대 한 개인 피어 링만 사용할 수 있음).

## <a name="locking-down-inbound-and-outbound-connectivity"></a>인바운드 및 아웃 바운드 연결을 잠그는

다음 다이어그램에는 권장 되는 보안 구성을 보여 줍니다.

![managed-instance-vnet.png](media/sql-database-managed-instance-public-endpoint-securely/managed-instance-vnet.png)

관리 되는 인스턴스를 [전용 공용 끝점 주소가](sql-database-managed-instance-find-management-endpoint-ip-address.md)합니다. 이 IP 주소는 아웃 바운드 연결을 제한 하려면 클라이언트 쪽 아웃 바운드 방화벽 및 네트워크 보안 그룹 규칙에 설정 되어야 합니다.

을 보장 하기 위해 관리 되는 인스턴스로 트래픽을 신뢰할 수 있는 출처에서 제공 되는 것이 좋습니다 잘 알려진 IP 주소를 사용 하 여 원본에서 연결 합니다. 포트 3342에서 관리 되는 인스턴스 공용 끝점에 대 한 액세스를 제한할 네트워크 보안 그룹을 사용 합니다.

클라이언트를 온-프레미스 네트워크에서 연결을 시작 해야 하는 경우 원래 주소는 Ip의 잘 알려진 집합으로 변환 해야 합니다. 달성할 수 없으면 (예: 모바일 인력이 되는 일반적인 시나리오) 하는 경우에 사용 것이 좋습니다 [지점 및 사이트 간 VPN 연결 및 개인 끝점을](sql-database-managed-instance-configure-p2s.md)입니다.

연결에 Azure에서 시작 된 것이 좋습니다 트래픽을에서 제공 되는지 잘 알려진 할당 [VIP](../virtual-network/virtual-networks-reserved-public-ip.md) (예: 가상 머신). VIP 주소를 관리 편의성을 위해 고객 사용을 고려할 수 [공용 IP 주소 접두사](../virtual-network/public-ip-address-prefix.md)합니다.