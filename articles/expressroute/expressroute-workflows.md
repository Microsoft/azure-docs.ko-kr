---
title: 'Azure Express 경로: 회로 구성 워크플로'
description: 이 페이지는 ExpressRoute 회로 및 피어링을 구성하기 위한 워크플로를 보여줍니다.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 09/18/2018
ms.author: cherylmc
ms.openlocfilehash: e833e20085d7cfd8f727acb394851e96e7e19368
ms.sourcegitcommit: 12a26f6682bfd1e264268b5d866547358728cd9a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/10/2020
ms.locfileid: "75864369"
---
# <a name="expressroute-workflows-for-circuit-provisioning-and-circuit-states"></a>회로 프로비저닝 및 회로 상태에 대한 ExpressRoute 워크플로
이 페이지에서는 높은 수준에서 구성 워크플로 프로비전 및 라우팅 서비스를 안내합니다.

![회로 워크플로](./media/expressroute-workflows/expressroute-circuit-workflow.png)

다음 그림 및 해당 단계는 Express 경로 회로 종단 간 프로 비전을 위한 작업을 간략하게 설명 합니다. 

1. PowerShell을 사용하여 ExpressRoute 회로를 구성합니다. 자세한 세부 사항은 [ExpressRoute 회로 만들기](expressroute-howto-circuit-classic.md) 문서의 지침을 수행합니다.
2. 서비스 공급자에서 연결을 정렬합니다. 이 프로세스는 다양합니다. 연결을 정렬하는 방법에 대한 자세한 내용은 연결 공급자에게 문의합니다.
3. PowerShell 통해 상태를 프로비전하는 ExpressRoute 회로를 확인하여 회로를 성공적으로 프로비전했는지 확인합니다. 
4. 라우팅 도메인을 구성합니다. 연결 공급자가 3 계층 구성을 관리 하는 경우 회로에 대한 라우팅을 구성 합니다. 연결 공급자가 계층 2 서비스만 제공 하는 경우 라우팅 [요구 사항](expressroute-routing.md) 및 [라우팅 구성](expressroute-howto-routing-classic.md) 페이지에 설명 된 지침에 따라 라우팅을 구성 해야 합니다.
   
   * Azure 프라이빗 피어링 사용 - 이 피어링을 사용하여 가상 네트워크 내에 배포된 VM/클라우드 서비스에 연결합니다.

   * Microsoft 피어 링 사용-Office 365와 같은 Microsoft 온라인 서비스에 액세스 하려면이 기능을 사용 하도록 설정 합니다. 모든 Azure PaaS 서비스는 Microsoft 피어링을 통해 액세스할 수 있습니다.
     
     > [!IMPORTANT]
     > 인터넷에 사용하는 것 이외에 별도 프록시/Edge를 사용하여 Microsoft에 연결해야 합니다. ExpressRoute 및 인터넷 모두에 동일한 Edge를 사용하면 비대칭 라우팅이 발생하고 네트워크에 대한 연결 중단이 발생합니다.
     > 
     > 
     
     ![라우팅 워크플로](./media/expressroute-workflows/routing-workflow.png)
5. 가상 네트워크를 ExpressRoute 회로에 연결 - 가상 네트워크를 ExpressRoute 회로에 연결할 수 있습니다. 지침을 수행하여 회로에 [VNet을 연결](expressroute-howto-linkvnet-arm.md) 합니다. 이러한 VNet은 ExpressRoute 회로로써 동일한 Azure 구독에 있거나 다른 구독에 있을 수 있습니다.

## <a name="expressroute-circuit-provisioning-states"></a>ExpressRoute 회로 상태 프로비전
각 ExpressRoute 회로에는 두 가지 상태가 있습니다.

* 서비스 공급자 프로비전 상태
* 상태

상태는 Microsoft의 프로비전 상태를 나타냅니다. Express 경로 회로를 만들 때는 이 속성이 Enabled로 설정됩니다.

연결 공급자 프로비전 상태는 연결 공급자 측의 상태를 나타냅니다. *프로비전되지 않음*, *프로비전 중* 또는 *프로비전됨*일 수 있습니다. 피어 링을 구성 하려면 Express 경로 회로가 프로 비전 된 상태 여야 합니다.

### <a name="possible-states-of-an-expressroute-circuit"></a>ExpressRoute 회로의 가능한 상태
이 섹션에서는 Express 경로 회로의 가능한 상태를 나열 합니다.

**만드는 경우**

Express 경로 회로는 리소스 생성 시 다음 상태를 보고 합니다.

    ServiceProviderProvisioningState : NotProvisioned
    Status                           : Enabled


**연결 공급자에서 회로를 프로비전하고 있는 경우**

Express 경로 회로는 연결 공급자가 회로를 프로 비전 하기 위해 작업 하는 동안 다음 상태를 보고 합니다.

    ServiceProviderProvisioningState : Provisioning
    Status                           : Enabled


**연결 공급자에서 프로비전 프로세스를 완료한 경우**

Express 경로 회로는 연결 공급자가 성공적으로 회로를 프로 비전 한 후 다음 상태를 보고 합니다.

    ServiceProviderProvisioningState : Provisioned
    Status                           : Enabled


**연결 공급자에서 회로 프로비전을 해제하는 경우**

Express 경로 회로를 프로 비전 해제 해야 하는 경우 서비스 공급자가 프로 비전 해제 프로세스를 완료 한 후 회로에서 다음 상태를 보고 합니다.

    ServiceProviderProvisioningState : NotProvisioned
    Status                           : Enabled


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

