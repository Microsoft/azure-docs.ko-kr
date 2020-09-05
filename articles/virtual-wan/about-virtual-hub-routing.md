---
title: 가상 허브 라우팅 정보
titleSuffix: Azure Virtual WAN
description: 이 문서에서는 가상 허브 라우팅을 설명 합니다.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: cherylmc
ms.custom: fasttrack-edit
ms.openlocfilehash: f29a7e48fc1872f83b5a6ce127f38c1a559b2691
ms.sourcegitcommit: e2b36c60a53904ecf3b99b3f1d36be00fbde24fb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/24/2020
ms.locfileid: "88762320"
---
# <a name="about-virtual-hub-routing"></a>가상 허브 라우팅 정보

가상 허브의 라우팅 기능은 BGP (Border Gateway Protocol)를 사용 하 여 게이트웨이 간의 모든 라우팅을 관리 하는 라우터에서 제공 합니다. 가상 허브는 사이트 간 VPN 게이트웨이, Express 경로 게이트웨이, 지점 및 사이트 간 게이트웨이, Azure 방화벽과 같은 여러 게이트웨이를 포함할 수 있습니다. 또한이 라우터는 가상 허브에 연결 하는 가상 네트워크 간의 전송 연결을 제공 하며 50 Gbps의 최대 집계 처리량을 지원할 수 있습니다. 이러한 라우팅 기능은 표준 가상 WAN 고객에 게 적용 됩니다. 

라우팅을 구성 하려면 [가상 허브 라우팅을 구성 하는 방법](how-to-virtual-hub-routing.md)을 참조 하세요.

## <a name="routing-concepts"></a><a name="concepts"></a>라우팅 개념

다음 섹션에서는 가상 허브 라우팅의 주요 개념에 대해 설명 합니다.

### <a name="hub-route-table"></a><a name="hub-route"></a>허브 경로 테이블

가상 허브 경로 테이블은 하나 이상의 경로를 포함할 수 있습니다. 경로에는 라우팅할 패킷에 대 한 이름, 레이블, 대상 유형, 대상 접두사 목록, 다음 홉 정보 등이 포함 됩니다. 일반적으로 **연결** 에는 경로 테이블에 연결 하거나 경로 테이블에 전파 되는 라우팅 구성이 포함 됩니다.

### <a name="connection"></a><a name="connection"></a>연결

연결은 라우팅 구성이 있는 리소스 관리자 리소스입니다. 연결의 네 가지 유형은 다음과 같습니다.

* **Vpn 연결**: vpn 사이트를 가상 허브 vpn gateway에 연결 합니다.
* **Express 경로 연결**: 가상 허브 express 경로 게이트웨이에 express 경로 회로를 연결 합니다.
* **P2S 구성 연결**: 사용자 Vpn (지점 및 사이트 간) 구성을 가상 허브 사용자 Vpn (지점 및 사이트 간) 게이트웨이에 연결 합니다.
* **허브 가상 네트워크 연결**: 가상 허브에 가상 네트워크를 연결 합니다.

설치 하는 동안 가상 네트워크 연결에 대 한 라우팅 구성을 설정할 수 있습니다. 기본적으로 모든 연결은 기본 경로 테이블에 연결 및 전파 됩니다.

### <a name="association"></a><a name="association"></a>연결

각 연결은 하나의 경로 테이블에 연결 됩니다. 경로 테이블에 대 한 연결을 연결 하면 경로 테이블에 경로로 표시 된 대상으로 트래픽을 보낼 수 있습니다. 연결의 라우팅 구성에 연결 된 경로 테이블이 표시 됩니다.  여러 연결을 동일한 경로 테이블에 연결할 수 있습니다. 모든 VPN, Express 경로 및 사용자 VPN 연결은 동일한 (기본) 경로 테이블에 연결 됩니다.

기본적으로 모든 연결은 가상 허브의 **기본 경로 테이블** 에 연결 됩니다. 각 가상 허브에는 고정 경로를 추가 하도록 편집할 수 있는 자체 기본 경로 테이블이 있습니다. 정적으로 추가 된 경로는 동일한 접두사에 대해 동적으로 학습 된 경로 보다 우선적으로 적용 됩니다.

:::image type="content" source="./media/about-virtual-hub-routing/concepts-association.png" alt-text="연결":::

### <a name="propagation"></a><a name="propagation"></a>전파

연결은 경로 테이블에 경로를 동적으로 전파 합니다. VPN 연결, Express 경로 연결 또는 P2S 구성 연결을 사용 하 여 경로는 BGP를 사용 하 여 가상 허브에서 온-프레미스 라우터로 전파 됩니다. 하나 또는 여러 경로 테이블에 경로를 전파할 수 있습니다.

**None 경로 테이블** 은 각 가상 허브에도 사용할 수 있습니다. None 경로 테이블에 전파 하는 것은 연결에서 전파 해야 하는 경로가 없음을 의미 합니다. VPN, Express 경로 및 사용자 VPN 연결은 경로를 동일한 경로 테이블 집합으로 전파 합니다.

:::image type="content" source="./media/about-virtual-hub-routing/concepts-propagation.png" alt-text="전파":::

### <a name="labels"></a><a name="static"></a>레이블
레이블은 경로 테이블을 논리적으로 그룹화 하는 메커니즘을 제공 합니다. 이는 연결에서 여러 경로 테이블로 경로를 전파 하는 동안 특히 유용 합니다. 예를 들어 기본 경로 테이블에는 ' Default ' 라는 기본 제공 레이블이 있습니다. 사용자가 연결 경로를 ' Default ' 레이블에 전파 하면 가상 WAN의 모든 허브에서 모든 기본 경로 테이블에 자동으로 적용 됩니다. 

### <a name="configuring-static-routes-in-a-virtual-network-connection"></a><a name="static"></a>가상 네트워크 연결에서 정적 경로 구성

정적 경로를 구성 하면 가상 허브에 연결 된 스포크 VNet에 프로 비전 된 NVA (네트워크 가상 어플라이언스)가 될 수 있는 다음 홉 IP를 통해 트래픽을 유도 하는 메커니즘이 제공 됩니다. 고정 경로는 경로 이름, 대상 접두사 목록 및 다음 홉 IP로 구성 됩니다.

## <a name="reset-hub"></a><a name="route"></a>허브 다시 설정
이 옵션은 Azure Portal 에서만 사용할 수 있으며,이 옵션은 경로 테이블, 허브 라우터 또는 가상 허브 리소스 자체와 같은 실패 한 리소스를 정당한 프로 비전 상태로 다시 가져오는 방법을 사용자에 게 제공 합니다. Microsoft에 지원을 요청 하기 전에 사용자가 고려해 야 할 추가 옵션입니다. 이 작업을 수행 해도 가상 허브의 게이트웨이는 다시 설정 되지 않습니다. 

## <a name="route-tables-in-basic-and-standard-virtual-wans-prior-to-the-feature-set-of-association-and-propagation"></a><a name="route"></a>연결 및 전파 기능 집합 전에 기본 및 표준 가상 Wan의 테이블 경로

이제 경로 테이블은 연결 및 전파 기능을 제공합니다. 기존 경로 테이블은 이러한 기능이 없는 경로 테이블입니다. 허브 라우팅에 기존 경로를 사용 중이고 새 기능을 사용하려는 경우 다음 사항을 고려하세요.

* **가상 허브의 기존 경로를 사용 하는 표준 가상 WAN 고객**:

Azure Portal에서 허브에 대 한 라우팅 섹션에 기존 경로를 설치한 경우 먼저 해당 경로를 삭제 한 다음 새 경로 테이블을 만들어야 합니다 (Azure Portal의 허브에 대 한 경로 테이블 섹션에서 사용 가능).

* **가상 허브의 기존 경로를 사용 하는 기본 가상 WAN 고객**: Azure Portal에서 허브에 대 한 라우팅 섹션에 기존 경로를 사용 하는 경우 먼저 해당 경로를 삭제 한 다음 기본 가상 Wan을 표준 가상 wan으로 **업그레이드** 해야 합니다. [가상 WAN을 기본에서 표준으로 업그레이드](upgrade-virtual-wan.md)를 참조하세요.

## <a name="virtual-wan-routing-considerations"></a><a name="considerations"></a>가상 WAN 라우팅 고려 사항

가상 WAN 라우팅을 구성할 때 다음 사항을 고려 하세요.

* 모든 분기 연결 (지점 및 사이트 간, 사이트 간 및 Express 경로)을 기본 경로 테이블에 연결 해야 합니다. 이렇게 하면 모든 분기가 동일한 접두사를 배우게 됩니다.
* 모든 분기 연결에서 경로를 동일한 경로 테이블 집합에 전파 해야 합니다. 예를 들어 분기가 기본 경로 테이블에 전파 되도록 결정 한 경우이 구성은 모든 분기에서 일치 해야 합니다. 따라서 기본 경로 테이블에 연결 된 모든 연결에서 모든 분기에 연결할 수 있습니다.
* Azure 방화벽을 통한 분기 간 분기는 현재 지원 되지 않습니다.
* 여러 지역에서 Azure 방화벽을 사용 하는 경우 모든 스포크 가상 네트워크를 동일한 경로 테이블에 연결 해야 합니다. 예를 들어 Vnet 하위 집합이 Azure 방화벽을 통과 하는 반면, 다른 Vnet는 동일한 가상 허브에서 Azure 방화벽을 우회 하는 것은 불가능 합니다.
* VNet 연결당 하나의 다음 홉 IP를 구성할 수 있습니다.
* 가상 허브는 0.0.0.0/0 및 다음 홉 Virtual Network 연결 (또는 VNet 연결의 어플라이언스 IP)에 대 한 고정 경로를 지원 하지 않습니다.

## <a name="next-steps"></a>다음 단계

라우팅을 구성 하려면 [가상 허브 라우팅을 구성 하는 방법](how-to-virtual-hub-routing.md)을 참조 하세요.

Virtual WAN에 대한 자세한 내용은 [FAQ](virtual-wan-faq.md)를 참조하세요.
