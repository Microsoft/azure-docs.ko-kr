---
title: '필수 구성 요소 - ExpressRoute: Azure | Microsoft Docs'
description: 이 페이지에서는 Azure ExpressRoute 회로를 주문하기 전에 충족해야 하는 요구 사항 목록을 제공합니다. 검사 목록을 포함합니다.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: mialdrid
ms.custom: seodec18
ms.openlocfilehash: afe8d3971a51d57498e3e32b7e1cf5bf5a3263d6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60883273"
---
# <a name="expressroute-prerequisites--checklist"></a>ExpressRoute 필수 구성 요소 및 검사 목록
ExpressRoute를 사용하여 Microsoft 클라우드 서비스에 연결하려면 다음 섹션에 나열된 다음 요구 사항을 충족하는지 확인해야 합니다.

[!INCLUDE [expressroute-office365-include](../../includes/expressroute-office365-include.md)]

## <a name="azure-account"></a>Azure 계정
* 유효한 활성 Microsoft Azure 계정 ExpressRoute 회로를 설정하려면 이 계정이 필요합니다. ExpressRoute 회로는 Azure 구독 내의 리소스입니다. Azure 구독은 Office 365 서비스 및 Dynamics 365와 같은 Azure 이외의 Microsoft 클라우드 서비스에 대한 연결이 제한되는 경우에도 필요합니다.
* 활성 Office 365 구독(Office 365 서비스를 사용하는 경우). 자세한 내용은 이 문서의 Office 365 특정 요구 사항 섹션을 참조하세요.

## <a name="connectivity-provider"></a>연결 공급자

* Microsoft 클라우드에 연결하는 데 [ExpressRoute 연결 파트너](expressroute-locations.md#partners) 와 작업할 수 있습니다. [세 가지 방법](expressroute-introduction.md)으로 온-프레미스 네트워크와 Microsoft 간에 연결을 설정할 수 있습니다.
* 공급자가 ExpressRoute 연결 파트너가 아닌 경우 [클라우드 Exchange 공급자](expressroute-locations.md#connectivity-through-exchange-providers)를 통해 Microsoft 클라우드에 계속 연결할 수 있습니다.

## <a name="network-requirements"></a>네트워크 요구 사항
* **각 피어 링 위치에서 중복**: Microsoft는 Microsoft의 라우터 및 각 ExpressRoute 회로의 피어 링 라우터 간에 설정 되어야 하는 중복 BGP 세션을 요구 (도 있는 경우 방금 [클라우드 exchange에 하나의 실제 연결만](expressroute-faqs.md#onep2plink)).
* **재해 복구에 대 한 중복성**: 다른 피어 링 위치에 두 개 이상의 ExpressRoute 회로 단일 실패 지점을 방지를 설정 하는 것이 좋습니다.
* **라우팅**: Microsoft Cloud에 연결하는 방법에 따라 사용자와 공급자는 [라우팅 도메인](expressroute-circuit-peerings.md)에 대한 BGP 세션을 설정 및 관리해야 합니다. 일부 이더넷 연결 공급자 또는 클라우드 Exchange 공급자는 가치 추가 서비스로 BGP 관리를 제공할 수 있습니다.
* **NAT**: Microsoft만 Microsoft 피어링을 통해 공용 IP 주소를 허용합니다. 온-프레미스 네트워크에서 개인 IP 주소를 사용하는 경우 사용자 또는 공급자는 [NAT를 사용](expressroute-nat.md)하여 개인 IP 주소를 공용 IP 주소로 번역해야 합니다.
* **QoS**: 비즈니스용 Skype에는 차별화된 QoS 처리를 필요로 하는 다양한 서비스(예: 음성, 비디오, 텍스트)가 있습니다. 사용자와 공급자는 [QoS 요구 사항](expressroute-qos.md)을 따라야 합니다.
* **네트워크 보안**: ExpressRoute를 통해 Microsoft Cloud에 연결할 때 [네트워크 보안](../best-practices-network-security.md)을 고려합니다.

## <a name="office-365"></a>Office 365
ExpressRoute에서 Office 365를 사용하도록 설정하려는 경우 Office 365 요구 사항에 대한 자세한 내용은 다음 문서를 검토합니다.

* [Office 365용 ExpressRoute 개요](https://support.office.com/article/Azure-ExpressRoute-for-Office-365-6d2534a2-c19c-4a99-be5e-33a0cee5d3bd)
* [Office 365용 ExpressRoute로 라우팅](https://support.office.com/article/Routing-with-ExpressRoute-for-Office-365-e1da26c6-2d39-4379-af6f-4da213218408)
* [ExpressRoute를 사용한 고가용성 및 장애 조치](https://aka.ms/erhighavailability)
* [Office 365 URL 및 IP 주소 범위](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)
* [Office 365에 대한 네트워크 계획 및 성능 조정](https://support.office.com/article/Network-planning-and-performance-tuning-for-Office-365-e5f1228c-da3c-4654-bf16-d163daee8848)
* [네트워크 대역폭 계산기 및 도구](https://support.office.com/article/Network-and-migration-planning-for-Office-365-f5ee6c33-bcd7-4b0b-b0f8-dc1d9fb8d132)
* [온-프레미스 환경과 Office 365 통합](https://support.office.com/article/Office-365-integration-with-on-premises-environments-263faf8d-aa21-428b-aed3-2021837a4b65)
* [Office 365 고급 교육 비디오의 ExpressRoute](https://channel9.msdn.com/series/aer/)

## <a name="dynamics-365"></a>Dynamics 365
ExpressRoute에서 Dynamics 365를 사용하도록 설정하려는 경우 Dynamics 365에 대한 자세한 내용은 다음 문서를 검토합니다.

* [Dynamics 365 및 ExpressRoute 백서](https://download.microsoft.com/download/B/2/8/B2896B38-9832-417B-9836-9EF240C0A212/Microsoft%20Dynamics%20365%20and%20ExpressRoute.pdf)
* [Dynamics 365 URL](https://support.microsoft.com/kb/2655102) 및 [IP 주소 범위](https://support.microsoft.com/kb/2728473)

## <a name="next-steps"></a>다음 단계
* ExpressRoute에 대한 자세한 내용은 [ExpressRoute FAQ](expressroute-faqs.md)를 참조하세요.
* ExpressRoute 연결 공급자를 찾습니다. [ExpressRoute 파트너 및 피어링 위치](expressroute-locations.md)를 확인하세요.
* [라우팅](expressroute-routing.md), [NAT](expressroute-nat.md) 및 [QoS](expressroute-qos.md)에 대한 요구 사항을 참조합니다.
* ExpressRoute 연결을 구성합니다.
  * [ExpressRoute 회로 만들기](expressroute-howto-circuit-arm.md)
  * [라우팅 구성](expressroute-howto-routing-arm.md)
  * [VNet을 ExpressRoute 회로에 연결](expressroute-howto-linkvnet-arm.md)
