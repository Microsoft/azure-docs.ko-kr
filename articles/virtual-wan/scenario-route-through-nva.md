---
title: '시나리오: NVA (네트워크 가상 어플라이언스)를 통해 트래픽 라우팅'
titleSuffix: Azure Virtual WAN
description: NVA를 통한 트래픽 라우팅
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 08/04/2020
ms.author: cherylmc
ms.custom: fasttrack-edit
ms.openlocfilehash: 6b62f8c33c73ded978c0c2e3a8c3b7fadea49c96
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88852086"
---
# <a name="scenario-route-traffic-through-an-nva"></a>시나리오: NVA를 통해 트래픽 라우팅

가상 WAN 가상 허브 라우팅으로 작업할 때 몇 가지 시나리오를 사용할 수 있습니다. 이 NVA 시나리오에서 목표는 NVA (네트워크 가상 어플라이언스)를 통해 분기에서 VNet으로, VNet에서 분기로 트래픽을 라우팅하는 것입니다. 가상 허브 라우팅에 대 한 자세한 내용은 [가상 허브 라우팅 정보](about-virtual-hub-routing.md)를 참조 하세요.

> [!NOTE]
> 새 기능 이전에 경로를 설정 하 여 [가상 허브 라우팅을](how-to-virtual-hub-routing.md) 사용할 수 있도록 구성 하는 방법에 대 한 설정이 이미 있는 경우 다음 문서 버전의 단계를 사용 하세요.
>* [Azure Portal 문서](virtual-wan-route-table-nva-portal.md)
>* [PowerShell 문서](virtual-wan-route-table-nva.md)
>

## <a name="design"></a><a name="design"></a>디자인

이 시나리오에서는 명명 규칙을 사용 합니다.

* 사용자가 NVA를 배포 하 고 다른 가상 네트워크를 스포크 ( **연결 매트릭스**의 vnet 2 및 vnet 4)로 연결 하는 가상 네트워크의 경우 "Nva vnet"입니다.
* "NVA 스포크"는 NVA VNet (아래 **연결 매트릭스**의 vnet 5, vnet 6, vnet 7 및 vnet 8)에 연결 된 가상 네트워크에 대 한 것입니다.
* NVA 또는 기타 Vnet 피어 링가 있는 가상 WAN에 연결 된 가상 네트워크에 대 한 "NVA Vnet" (아래 **연결 매트릭스**의 vnet 1 및 vnet 3).
* Microsoft에서 관리 하는 가상 WAN 허브의 "허브"로, NVA Vnet가 연결 됩니다. NVA 스포크 Vnet는 NVA Vnet에만 가상 WAN 허브에 연결할 필요가 없습니다.

다음 연결 매트릭스는이 시나리오에서 지원 되는 흐름을 요약 합니다.

**연결 매트릭스**

| 보낸 사람             | 아래와 같이 변경합니다.|   *NVA 스포크*|*NVA Vnet*|*NVA Vnet*|*분기*|
|---|---|---|---|---|---|
| **NVA 스포크**   | &#8594; | 0/0 UDR  |  피어링 |   0/0 UDR    |  0/0 UDR  |
| **NVA Vnet**    | &#8594; |   정적 |      X   |        X     |      X    |
| **NVA Vnet**| &#8594; |   정적 |      X   |        X     |      X    |
| **분기**     | &#8594; |   정적 |      X   |        X     |      X    |

연결 매트릭스의 각 셀은 가상 WAN 연결 (흐름의 "From" 쪽, 테이블의 행 머리글)이 특정 트래픽 흐름에 대 한 대상 접두사 (흐름의 "대상" 쪽, 테이블의 기울임꼴 열 머리글)를 학습 하는지 여부를 설명 합니다. 다음을 살펴보세요.

* NVA 스포크는 가상 WAN에서 관리 되지 않습니다. 그 결과, 다른 Vnet 또는 분기와 통신 하는 메커니즘이 사용자에 의해 유지 관리 됩니다. NVA VNet에 연결 하는 것은 VNet 피어 링에서 제공 되며 NVA를 가리키는 기본 경로 (NVA는 다음 홉에서 인터넷에 대 한 연결, 다른 스포크 및 분기)를 포함 해야 합니다.
* NVA Vnet는 고유한 NVA 스포크를 알 수 있지만 다른 NVA Vnet에 연결 된 NVA 스포크는 인식 하지 못합니다. 예를 들어 표 1에서 VNet 2는 vnet 5와 VNet 6에 대해 알고 있지만 VNet 7 및 VNet 8과 같은 다른 스포크는 인식 하지 못합니다. 정적 경로는 NVA Vnet에 다른 스포크 접두사를 삽입 하는 데 필요 합니다.
* 마찬가지로 NVA 스포크는 VWAN 허브에 연결 되지 않으므로 분기 및 NVA Vnet는 NVA 스포크를 알 수 없습니다. 따라서 여기서는 고정 경로만 필요 합니다.

NVA 스포크가 가상 WAN에 의해 관리 되지 않는 것을 고려 하 여 다른 모든 행은 동일한 연결 패턴을 보여 줍니다. 따라서 단일 경로 테이블 (기본 경로)은 다음과 같습니다.

* 가상 네트워크 (비 허브 Vnet 및 사용자 허브 Vnet):
  * 연결 된 경로 테이블: **기본값**
  * 경로 테이블에 전파: **기본값**
* 분기
  * 연결 된 경로 테이블: **기본값**
  * 경로 테이블에 전파: **기본값**

그러나이 시나리오에서는 구성할 정적 경로에 대해 생각해 야 합니다. 각 고정 경로에는 두 개의 구성 요소가 포함 됩니다. 가상 WAN 허브에는 각 스포크에 사용할 연결을 지정 하는 가상 wan의 구성 요소와 NVA에 할당 된 구체적인 IP 주소 (또는 여러 Nva 앞의 부하 분산 장치)를 가리키는 특정 연결의 또 다른 구성 요소가 있습니다 **Figure 1** .

**그림 1**

:::image type="content" source="media/routing-scenarios/nva/nva-static-concept.png" alt-text="그림 1":::

이를 통해 NVA VNet 뒤에 NVA 스포크로 트래픽을 전송 하기 위해 기본 테이블에 필요한 고정 경로는 다음과 같습니다.

| 설명 | 경로 테이블 | 고정 경로              |
| ----------- | ----------- | ------------------------- |
| VNet 2       | 기본값     | 10.2.0.0/16-> e usconn |
| VNet 4       | 기본값     | 10.4.0.0/16-> weconn     |

이제 가상 WAN에서 패킷을 보낼 연결을 알고 있지만 연결에서 이러한 패킷을 받을 때 수행할 작업을 알고 있어야 합니다. 연결 경로 테이블이 사용 됩니다. 여기서는 더 짧은 접두사 (/16이 아닌/24 대신)를 사용 하 여 이러한 경로가 NVA Vnet (VNet 2 및 VNet 4)에서 가져온 경로 보다 우선적으로 적용 되도록 합니다.

| 설명 | 연결 | 고정 경로            |
| ----------- | ---------- | ----------------------- |
| VNet 5       | eastusconn | 10.2.1.0/24-> 10.2.0.5 |
| VNet 6       | eastusconn | 10.2.2.0/24-> 10.2.0.5 |
| VNet 7       | weconn     | 10.4.1.0/24-> 10.4.0.5 |
| VNet 8       | weconn     | 10.4.2.0/24-> 10.4.0.5 |

이제 NVA Vnet, NVA Vnet 및 분기에서 모든 NVA 스포크에 연결 하는 방법을 알 수 있습니다. 가상 허브 라우팅에 대 한 자세한 내용은 [가상 허브 라우팅 정보](about-virtual-hub-routing.md)를 참조 하세요.

## <a name="architecture"></a><a name="architecture"></a>아키텍처

**그림 2**에는 두 개의 허브가 있습니다. **Hub1** 및 **Hub2**.

* **Hub1** 및 **Hub2** 는 nva Vnet **vnet 2** 및 **vnet 4**에 직접 연결 됩니다.

* Vnet **5** 와 **Vnet 6** 은 **vnet 2**와 피어 링 됩니다.

* Vnet **7** 및 **Vnet 8** 은 **vnet 4**로 피어 링 됩니다.

* **Vnet 5, 6, 7, 8** 은 간접 스포크 이며, 가상 허브에 직접 연결 되지 않습니다.

**그림 2**

:::image type="content" source="./media/routing-scenarios/nva/nva.png" alt-text="그림 2" lightbox="./media/routing-scenarios/nva/nva.png":::

## <a name="scenario-workflow"></a><a name="workflow"></a>시나리오 워크플로

NVA를 통해 라우팅을 설정 하려면 다음 단계를 고려해 야 합니다.

1. NVA 스포크 VNet 연결을 식별 합니다. **그림 2**에서는 **vnet 2 연결 (eastusconn)** 및 **vnet 4 연결 (weconn)** 을 사용할 수 있습니다.

   UDRs가 설정 되어 있는지 확인 합니다.
   * VNet 5와 VNet 6에서 VNet 2 NVA IP로
   * VNet 7 및 VNet 8에서 VNet 4 NVA IP로 
   
   Vnet 5, 6, 7, 8을 가상 허브에 직접 연결할 필요는 없습니다. Vnet 5, 6, 7, 8의 NSGs가 branch (VPN/ER/P2S)에 대 한 트래픽 또는 원격 Vnet에 연결 된 Vnet를 허용 하는지 확인 합니다. 예를 들어, Vnet 5, 6은 NSGs가 원격 허브 2에 연결 된 온-프레미스 주소 접두사 및 Vnet 7, 8에 대 한 트래픽을 허용 하는지 확인 해야 합니다.

2. Vnet 2, 5, 6에서 허브 1의 기본 경로 테이블에 대 한 집계 된 고정 경로 항목을 추가 합니다.

   :::image type="content" source="./media/routing-scenarios/nva/nva-static-expand.png" alt-text="예제":::

3. VNet 2의 가상 네트워크 연결에서 Vnet 5, 6의 고정 경로를 구성 합니다. 가상 네트워크 연결에 대 한 라우팅 구성을 설정 하려면 [가상 허브 라우팅](how-to-virtual-hub-routing.md#routing-configuration)을 참조 하세요.

4. Vnet 4, 7, 8의 집계 된 정적 경로 항목을 허브 1의 기본 경로 테이블에 추가 합니다.

5. 허브 2의 기본 경로 테이블에 대해 2, 3 및 4 단계를 반복 합니다.

이렇게 하면 아래 **그림 3**에 표시 된 것 처럼 라우팅 구성 변경 내용이 발생 합니다.

**그림 3**

   :::image type="content" source="./media/routing-scenarios/nva/nva-result.png" alt-text="그림 3" lightbox="./media/routing-scenarios/nva/nva-result.png":::

## <a name="next-steps"></a>다음 단계

* Virtual WAN에 대한 자세한 내용은 [FAQ](virtual-wan-faq.md)를 참조하세요.
* 가상 허브 라우팅에 대 한 자세한 내용은 [가상 허브 라우팅 정보](about-virtual-hub-routing.md)를 참조 하세요.
