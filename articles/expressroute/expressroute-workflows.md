---
title: 'Azure Express 경로: 회로 구성 워크플로'
description: 이 페이지에서는 Express 경로 회로 및 피어 링을 구성 하기 위한 워크플로를 보여 줍니다.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 08/24/2020
ms.author: cherylmc
ms.custom: contperfq1
ms.openlocfilehash: 229b7c145fa38443d2bc5f99005078ffa7f77065
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88814079"
---
# <a name="expressroute-workflows-for-circuit-provisioning-and-circuit-states"></a>회로 프로비전 및 회로 상태에 대한 ExpressRoute 워크플로

이 문서에서는 서비스 프로 비전 및 라우팅 구성 워크플로를 개략적으로 안내 합니다. 다음 섹션에서는 Express 경로 회로 종단 간을 프로 비전 하는 작업을 간략하게 설명 합니다.

## <a name="workflow-steps"></a>워크플로 단계

### <a name="1-prerequisites"></a>1. 사전 요구 사항

필수 구성 요소가 충족 되는지 확인 합니다. 전체 목록은 [전제 조건 및 검사 목록](expressroute-prerequisites.md)을 참조 하세요.

* Azure 구독이 생성 되었습니다.
* 물리적 연결은 Express 경로 파트너를 사용 하 여 설정 되거나 Express 경로 직접를 통해 구성 됩니다. 위치 검토, 위치 [및 파트너](expressroute-locations-providers.md#partners) 를 참조 하 여 피어 링 위치에서 express 경로 파트너 및 Express 경로 직접 연결을 확인 합니다.

### <a name="2-order-connectivity-or-configure-expressroute-direct"></a>2. 연결 순서를 설정 하거나 Express 경로 직접 구성

서비스 공급자의 연결을 주문 하거나 Express 경로 직접 구성

#### <a name="expressroute-partner-model"></a>Express 경로 파트너 모델

서비스 공급자에서 연결을 정렬합니다. 이 프로세스는 다양합니다. 연결을 정렬하는 방법에 대한 자세한 내용은 연결 공급자에게 문의합니다.

* Express 경로 파트너 선택
* 피어 링 위치 선택
* 대역폭 선택
* 청구 모델 선택
* 표준 또는 프리미엄 추가 기능 선택

#### <a name="expressroute-direct-model"></a>Express 경로 직접 모델

* 피어 링 위치에서 사용 가능한 Express 경로 직접 용량을 봅니다.
* Azure 구독에서 Express 경로 직접 리소스를 만들어 포트를 예약 합니다.
* 권한 부여의 문자를 요청 하 고 수신 하 고 피어 링 위치 공급자에서 실제 교차 연결 순서를 요청 합니다.
* 관리 상태를 사용 하도록 설정 하 고 [Azure Monitor](expressroute-monitoring-metrics-alerts.md#expressroute-direct-metrics)를 사용 하 여 라이트 수준 및 실제 링크를 확인 합니다.

### <a name="3-create-an-expressroute-circuit"></a>3. Express 경로 회로 만들기

#### <a name="expressroute-partner-model"></a>Express 경로 파트너 모델

Express 경로 파트너가 연결을 프로 비전 할 준비가 되었는지 확인 합니다. ExpressRoute 회로는 서비스 키가 발급된 순간부터 비용이 청구됩니다. [Express 경로 회로 만들기](expressroute-howto-circuit-portal-resource-manager.md) 의 지침을 사용 하 여 회로를 만듭니다.

#### <a name="expressroute-direct-model"></a>Express 경로 직접 모델

두 링크 모두에서 실제 링크 및 관리 상태를 사용 하도록 설정 해야 합니다. 지침은 [express 경로 다이렉트를 구성 하는 방법](how-to-expressroute-direct-portal.md) 을 참조 하세요. ExpressRoute 회로는 서비스 키가 발급된 순간부터 비용이 청구됩니다. [Express 경로 회로 만들기](expressroute-howto-circuit-portal-resource-manager.md) 의 지침을 사용 하 여 회로를 만듭니다.

### <a name="4-service-provider-provisions-connectivity"></a>4. 서비스 공급자가 연결을 프로 비전 합니다.

이 섹션은 Express 경로 파트너 연결 모델에만 해당 됩니다.

* 연결 공급자에 서비스 키 (s 키)를 제공 합니다.
* 연결 공급자에 필요한 추가 정보 (예: VPN ID)를 제공 합니다.
* 공급자가 라우팅 구성을 관리 하는 경우 필요한 세부 정보를 제공 합니다.

PowerShell, Azure Portal 또는 CLI를 사용 하 여 Express 경로 회로 프로 비전 상태를 확인 하 여 회로가 성공적으로 프로 비전 되었는지 확인할 수 있습니다.

### <a name="5-configure-routing-domains"></a>5. 라우팅 도메인 구성

라우팅 도메인을 구성합니다. 연결 공급자가 3 계층 구성을 관리 하는 경우 회로에 대 한 라우팅을 구성 합니다. 연결 공급자가 계층 2 서비스만 제공 하거나 Express 경로 직접를 사용 하는 경우 라우팅 [요구 사항](expressroute-routing.md) 및 [라우팅 구성](expressroute-howto-routing-classic.md) 문서에 설명 된 지침에 따라 라우팅을 구성 해야 합니다.

#### <a name="for-azure-private-peering"></a>Azure 개인 피어 링의 경우

개인 피어 링을 사용 하도록 설정 하 여 Azure 가상 네트워크 내에 배포 된 Vm 및 클라우드 서비스에 연결 합니다.

* 경로 1에 대 한 피어 링 서브넷 (/30)
* 경로 2에 대 한 피어 링 서브넷 (/30)
* 피어 링에 대 한 VLAN ID
* 피어 링 용 ASN
* Express 경로 ASN = 12076
* MD5 해시 (옵션)

#### <a name="for-microsoft-peering"></a>Microsoft 피어 링의 경우

이 기능을 사용 하 여 Office 365와 같은 Microsoft 온라인 서비스에 액세스할 수 있습니다. 또한 모든 Azure PaaS 서비스는 Microsoft 피어링을 통해 액세스할 수도 있습니다. 인터넷에 사용 하는 것과 다른 프록시/edge를 사용 하 여 Microsoft에 연결 해야 합니다. ExpressRoute 및 인터넷 모두에 동일한 Edge를 사용하면 비대칭 라우팅이 발생하고 네트워크에 대한 연결 중단이 발생합니다.

* 경로 1 (/30)에 대 한 피어 링 서브넷은 공용 IP 여야 합니다.
* 경로 2 (/30)의 피어 링 서브넷은 공용 IP 여야 합니다.
* 피어 링에 대 한 VLAN ID
* 피어 링 용 ASN
* 보급 접두사-공용 IP 접두사 여야 합니다.
* Customer ASN (피어 링 ASN과 다른 경우 선택 사항)
* IP 및 ASN 유효성 검사에 대 한 RIR/IRR
* Express 경로 ASN = 12076
* MD5 해시 (옵션)

### <a name="6-start-using-the-expressroute-circuit"></a>6. Express 경로 회로 사용 시작

* Azure 가상 네트워크를 Express 경로 회로에 연결 하 여 온-프레미스에서 Azure virtual network로의 연결을 사용할 수 있습니다. 지침은 [회로에 VNet 연결](expressroute-howto-linkvnet-arm.md) 문서를 참조 하세요. 이러한 VNet은 ExpressRoute 회로로써 동일한 Azure 구독에 있거나 다른 구독에 있을 수 있습니다.
* Microsoft 피어 링을 통해 Azure 서비스 및 Microsoft 클라우드 서비스에 연결 합니다.

##  <a name="expressroute-partner-circuit-provisioning-states"></a><a name="expressroute-circuit-provisioning-states"></a>Express 경로 파트너 회로 프로 비전 상태

다음 섹션에서는 Express 경로 파트너 연결 모델에 대 한 다양 한 Express 경로 회로 상태를 간략하게 설명 합니다.
각 Express 경로 파트너 회로에는 두 가지 상태가 있습니다.

* **ServiceProviderProvisioningState** 는 연결 공급자 측의 상태를 나타냅니다. *프로비전되지 않음*, *프로비전 중* 또는 *프로비전됨*일 수 있습니다. 피어 링을 구성 하기 위해 Express 경로 회로는 프로 비전 된 상태 여야 합니다. **이 상태는 express 경로 파트너 회로와만 관련 되며 express 경로 직접 회로의 속성에는 표시 되지**않습니다.

* **상태** 는 Microsoft의 프로 비전 상태를 나타냅니다. Express 경로 회로를 만들 때이 속성을 사용으로 설정 합니다.

### <a name="possible-states-of-an-expressroute-circuit"></a>ExpressRoute 회로의 가능한 상태

이 섹션에서는 Express 경로 파트너 연결 모델에서 만든 Express 경로 회로의 가능한 상태를 간략히 설명 합니다.

**생성 시**

Express 경로 회로는 리소스 생성 시 다음 상태를 보고 합니다.

```output
ServiceProviderProvisioningState : NotProvisioned
Status                           : Enabled
```

**연결 공급자가 회로를 프로 비전 하는 중입니다.**

Express 경로 회로는 연결 공급자가 회로를 프로 비전 하기 위해 작업 하는 동안 다음 상태를 보고 합니다.

```output
ServiceProviderProvisioningState : Provisioning
Status                           : Enabled
```

**연결 공급자가 프로 비전 프로세스를 완료 한 경우**

Express 경로 회로는 연결 공급자가 성공적으로 회로를 프로 비전 한 후 다음 상태를 보고 합니다.

```output
ServiceProviderProvisioningState : Provisioned
Status                           : Enabled
```

**연결 공급자가 회로를 프로 비전 해제 하는 경우**

Express 경로 회로를 프로 비전 해제 해야 하는 경우 서비스 공급자가 프로 비전 해제 프로세스를 완료 한 후 회로에서 다음 상태를 보고 합니다.

```output
ServiceProviderProvisioningState : NotProvisioned
Status                           : Enabled
```

필요에 따라 PowerShell cmdlet을 다시 사용하거나 실행하여 회로를 삭제할 수 있습니다.  

> [!IMPORTANT]
> ServiceProviderProvisioningState를 프로 비전 하거나 프로 비전 할 때 회로를 삭제할 수 없습니다. 연결 공급자는 회로를 프로 비전 해제 해야 삭제할 수 있습니다. Microsoft는 Azure에서 Express 경로 회로 리소스가 삭제 될 때까지 계속 회로를 청구 합니다.
> 

## <a name="routing-session-configuration-state"></a>라우팅 세션 구성 상태

Bgp 프로 비전 상태는 Microsoft edge에서 BGP 세션을 사용 하도록 설정 했는지 여부를 보고 합니다. 개인 또는 Microsoft 피어 링을 사용 하려면 상태를 사용 하도록 설정 해야 합니다.

특히 Microsoft 피어링의 경우 BGP 세션 상태를 확인하는 것이 중요합니다. BGP 프로비전 상태 외에도 *보급된 공용 접두사 상태*를 호출하는 다른 상태가 있습니다. 보급 된 공용 접두사 상태는 BGP 세션을 사용 하 고 라우팅이 종단 간에 작동 하기 위해 *구성* 된 상태 여야 합니다. 

보급된 공용 접두사 상태가 *유효성 검사가 필요한* 상태로 설정된 경우 보급된 접두사가 라우팅 레지스트리의의 AS 번호와 일치하지 않기 때문에 BGP 세션을 사용하지 않습니다.

> [!IMPORTANT]
> 보급 된 공용 접두사 상태가 *수동 유효성 검사* 상태 이면 [Microsoft 지원](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) 으로 지원 티켓을 열고 관련 된 자치 시스템 번호와 함께 보급 된 IP 주소를 소유 하는 증명 정보를 제공 해야 합니다.
> 
> 

## <a name="next-steps"></a>다음 단계

* ExpressRoute 연결을 구성합니다.
  
  * [ExpressRoute 회로 만들기](expressroute-howto-circuit-arm.md)
  * [라우팅 구성](expressroute-howto-routing-arm.md)
  * [VNet을 ExpressRoute 회로에 연결](expressroute-howto-linkvnet-arm.md)