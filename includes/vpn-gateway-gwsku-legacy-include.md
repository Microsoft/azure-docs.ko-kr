---
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 11/09/2018
ms.author: cherylmc
ms.openlocfilehash: 622a2f69c2e7b82f2df10d6120ee2dd466961915
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/14/2020
ms.locfileid: "77211424"
---
레거시(이전) VPN 게이트웨이 SKU는 다음과 같습니다.

* 기본값 (기본)
* Standard
* HighPerformance

VPN Gateway는 UltraPerformance 게이트웨이 SKU를 사용하지 않습니다. UltraPerformance SKU에 대한 내용은 [ExpressRoute](../articles/expressroute/expressroute-about-virtual-network-gateways.md) 설명서를 참조하세요.

이전 SKU를 사용할 경우 다음 사항을 고려합니다.

* 정책 기반 VPN 유형을 사용하려는 경우 기본 SKU를 사용해야 합니다. 정책 기반 VPN(이전에는 정적 라우팅이라고 함)은 다른 SKU에서 지원되지 않습니다.
* BGP는 기본 SKU에 지원되지 않습니다.
* ExpressRoute-VPN Gateway 공존 구성은 기본 SKU에서 지원되지 않습니다.
* 활성-활성 S2S VPN Gateway 연결은 HighPerformance SKU에서만 구성할 수 있습니다.