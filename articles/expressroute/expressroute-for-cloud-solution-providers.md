---
title: 클라우드 솔루션 공급자용 ExpressRoute -Azure | Microsoft Docs
description: 이 문서에서는 Azure 서비스 및 ExpressRoute를 자신의 제품에 통합하려는 클라우드 솔루션 공급자에 대한 정보를 제공합니다.
services: expressroute
author: richcar
ms.service: expressroute
ms.topic: article
ms.date: 10/10/2016
ms.author: ricarlse
ms.custom: seodec18
ms.openlocfilehash: a3bd48f32dfcee1a666ff842cfcab2384a5459ec
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/12/2019
ms.locfileid: "67849255"
---
# <a name="expressroute-for-cloud-solution-providers-csp"></a>클라우드 솔루션 공급자(CSP)용 ExpressRoute
Microsoft는 새 서비스 개발에 투자할 필요 없이 고객을 위한 새 서비스 및 솔루션을 신속하게 프로비전할 수 있도록 기존 대리점 및 배포자(CSP)를 위한 대규모 서비스를 제공합니다. 클라우드 솔루션 공급자(CSP)에 이러한 새 서비스를 직접 관리하는 능력을 부여하기 위해 Microsoft는 CSP가 고객을 대신해 Microsoft Azure 리소스를 관리할 수 있도록 하는 프로그램 및 API를 제공합니다. 이러한 리소스 중 하나가 ExpressRoute입니다. ExpressRoute를 통해 CSP는 기존 고객 리소스를 Azure 서비스에 연결할 수 있습니다. ExpressRoute는 Azure에서 고속의 프라이빗 통신 링크입니다. 

ExpressRoute는 단일 고객 구독에 연결되고 여러 고객이 공유할 수 없는 고가용성을 위한 한 쌍의 회로로 구성됩니다. 각 회로는 고가용성을 유지하기 위해 다른 라우터에서 종료해야 합니다.

> [!NOTE]
> ExpressRoute에는 대역폭 및 연결 한도가 있으므로 크고 복잡한 구현에는 단일 고객에 대해 여러 ExpressRoute 회로가 필요합니다.
> 
> 

Microsoft Azure에서는 고객에게 제공할 수 있는 서비스를 점점 더 많이 제공합니다. ExpressRoute는 Microsoft Azure 환경에 고속의 짧은 대기 시간 액세스를 제공하여 사용자와 사용자의 고객이 이러한 서비스를 활용하는 데 도움을 줍니다.

## <a name="microsoft-azure-management"></a>Microsoft Azure 관리
Microsoft는 CSP에게 사용자 고유의 서비스 관리 시스템과 프로그래밍 방식의 통합을 허용하여 Azure 고객 구독을 관리하는 API를 제공합니다. 지원되는 관리 기능은 [여기](https://msdn.microsoft.com/library/partnercenter/dn974944.aspx)에서 확인할 수 있습니다.

## <a name="microsoft-azure-resource-management"></a>Microsoft Azure 리소스 관리
고객과 맺은 계약에 따라 구독이 관리되는 방법이 결정됩니다. CSP는 리소스의 생성 및 유지 관리를 직접 관리할 수 있으며 고객은 Microsoft Azure 구독에 대한 제어를 유지 관리하고 필요한 Azure 리소스를 만들 수 있습니다. 고객이 Microsoft Azure 구독에서 리소스 생성을 관리하는 경우 다음의 두 가지 모델 중 하나를 사용합니다. “*Connect-Through*” 모델 또는 “*Direct-To*” 모델 이러한 모델은 다음 섹션에 자세히 설명되어 있습니다.  

### <a name="connect-through-model"></a>Connect-through 모델
![대체 텍스트](./media/expressroute-for-cloud-solution-providers/connect-through.png)  

Connect-through 모델에서 CSP는 데이터 센터 및 고객의 Azure 구독 간 직접 연결을 만듭니다. 직접 연결은 ExpressRoute를 사용하여 이루어지며 Azure와 함께 네트워크에 연결됩니다. 그런 다음 고객이 네트워크에 연결됩니다. 이 시나리오에서는 고객이 Azure 서비스에 액세스하려면 CSP 네트워크를 통과해야 합니다. 

고객에게 관리하지 않는 다른 Azure 구독이 있는 경우 공용 인터넷 또는 고유의 프라이빗 연결을 사용하여 비CSP 구독으로 프로비전된 해당 서비스에 연결합니다. 

Azure 서비스를 관리하는 CSP의 경우, CSP에는 CSP 구독 관리를 위해 AOBO(Administrate-On-Behalf-Of)를 통해 Azure Active Directory에 복제될 이전에 설정한 고객 ID 저장소가 있다고 가정합니다. 이 시나리오의 핵심 추진력은 지정된 파트너 또는 서비스 공급자가 고객과의 관계를 설정한 경우가 포함되며 고객은 현재 공급자 서비스를 사용 중이고 파트너는 공급자 호스팅 및 Azure 호스팅 솔루션의 조합을 제공하여 유연성을 제공하고 CSP만으로는 해결할 수 없는 고객 과제를 해결하기를 원합니다. 이 모델은 아래 **그림**에 설명되어 있습니다.

![대체 텍스트](./media/expressroute-for-cloud-solution-providers/connect-through-model.png)

### <a name="connect-to-model"></a>Connect-to 모델
![대체 텍스트](./media/expressroute-for-cloud-solution-providers/connect-to.png)

Connect-To 모델에서 서비스 공급자는 고객의 네트워크를 통해 ExpressRoute를 사용하여 고객의 데이터 센터와 CSP 프로비전된 Azure 구독 간의 직접 연결을 만듭니다.

> [!NOTE]
> ExpressRoute의 경우 고객이 ExpressRoute 회로를 만들고 유지 관리해야 합니다.  
> 
> 

이 연결 시나리오에서는 고객이 CSP 관리 Azure 구독에 액세스하기 위해 고객이 전체 또는 부분적으로 생성, 소유 및 관리하는 직접 네트워크 연결을 사용하여 고객 네트워크를 통해 직접 연결해야 합니다. 이러한 고객의 경우 공급자에게 현재 설정된 고객 ID 저장소가 없다고 가정하고 공급자는 AOBO를 통한 구독 관리를 위해 고객이 현재 ID 저장소를 Azure Active Directory로 복제하는 것을 지원합니다. 이 시나리오의 핵심 추진력은 지정된 파트너 또는 서비스 공급자가 고객과의 관계를 설정한 경우가 포함되며 고객은 현재 공급자 서비스를 사용 중이고 파트너는 기존 공급자 데이터 센터 또는 인프라 없이 전적으로 Azure 호스팅 솔루션을 기반으로 서비스를 제공하기를 원합니다.

![대체 텍스트](./media/expressroute-for-cloud-solution-providers/connect-to-model.png)

이러한 두 옵션 간의 선택은 고객의 요구 사항 및 현재 Azure 서비스를 제공해야 하는지에 따라 결정합니다. 이러한 모델 및 연결된 역할 기반 액세스 제어, 네트워킹, ID 디자인 패턴에 대한 자세한 내용은 다음 링크에서 자세히 설명합니다.

* **역할 기반 Access Control(RBAC)** - RBAC는 Azure Active Directory를 기반으로 합니다.  Azure RBAC에 대한 자세한 내용은 [여기](../role-based-access-control/role-assignments-portal.md)를 참조하세요.
* **네트워킹** – Microsoft Azure의 다양한 네트워킹 항목에 대해 다룹니다.
* **Azure AD(Azure Active Directory)** – Azure AD는 Microsoft Azure 및 타사 SaaS 애플리케이션에 대한 ID 관리를 제공합니다. Azure AD에 대한 자세한 내용은 [여기](https://azure.microsoft.com/documentation/services/active-directory/)를 참조하세요.  

## <a name="network-speeds"></a>네트워크 속도
ExpressRoute는 50Mb/s ~ 10Gb/s까지 네트워크 속도를 지원합니다. 따라서 고객은 자신의 고유 환경에 필요한 네트워크 대역폭의 양을 구입할 수 있습니다.

> [!NOTE]
> 통신을 방해하지 않고도 필요에 따라 네트워크 대역폭을 늘릴 수 있지만 네트워크 속도를 줄이려면 회로를 끊고 낮은 네트워크 속도로 다시 생성해야 합니다.  
> 
> 

ExpressRoute는 고속 연결을 효율적으로 활용하기 위해 여러 vNet에서 단일 ExpressRoute 회로로의 연결을 지원합니다. 동일한 고객을 소유 하는 여러 Azure 구독 중에서 단일 ExpressRoute 회로를 공유할 수 있습니다.

## <a name="configuring-expressroute"></a>ExpressRoute 구성
단일 ExpressRoute 회로를 통해 세 가지 유형의 트래픽([라우팅 도메인](#expressroute-routing-domains))을 지원하도록 ExpressRoute를 구성할 수 있습니다. 이 트래픽은 Microsoft 피어링, Azure 공용 피어링 및 프라이빗 피어링으로 분리됩니다. ExpressRoute 회로 크기 및 고객이 필요로 하는 격리에 따라 단일 ExpressRoute 회로를 통하거나 여러 ExpressRoute 회로를 사용하여 전송하도록 하나 또는 모든 유형의 트래픽을 선택할 수 있습니다. 고객의 보안 상태에서 동일한 회로를 통해 공용 트래픽 및 프라이빗 트래픽을 트래버스하는 것을 허용하지 않을 수 있습니다.

### <a name="connect-through-model"></a>Connect-through 모델
connect-through 구성에서는 고객 데이터 센터 리소스를 Azure에서 호스트된 구독으로 연결하기 위한 모든 네트워킹 기초를 사용자가 담당합니다. Azure 기능을 사용하려는 각 고객은 고유한 ExpressRoute 연결이 필요하며 이는 사용자가 관리합니다. 사용자는 ExpressRoute 회로를 확보하는 데 고객이 사용하는 것과 동일한 방법을 사용합니다. 회로 프로비전 및 회로 상태에 대한 [ExpressRoute 워크플로](expressroute-workflows.md) 문서에 설명된 동일한 단계를 따릅니다. 그런 다음 온-프레미스 네트워크와 Azure vNet 간의 트래픽 흐름을 제어하기 위해 BGP(Border Gateway Protocol) 경로를 구성합니다.

### <a name="connect-to-model"></a>Connect-to 모델
connect-to 구성에서 고객은 Azure에 대한 기존 연결을 보유하거나 ExpressRoute를 고객이 소유한 데이터 센터에서 사용자 데이터 센터가 아닌 Azure로 직접 연결하는 인터넷 서비스 공급자로의 연결을 시작합니다. 프로비전 프로세스를 시작하려면 고객은 위의 Connect-Through 모델에 설명된 단계를 따릅니다. 회로가 설정된 후 고객은 네트워크 및 Azure vNet 모두에 액세스할 수 있도록 온-프레미스 라우터를 구성해야 합니다.

데이터 센터의 리소스가 데이터 센터의 클라이언트 리소스 또는 Azure에서 호스트된 리소스와 통신하도록 연결 설정 및 경로 구성을 지원할 수 있습니다.

## <a name="expressroute-routing-domains"></a>ExpressRoute 라우팅 도메인
ExpressRoute는 공용, 프라이빗 및 Microsoft 피어링의 세 가지 라우팅 도메인을 제공합니다. 각 라우팅 도메인은 고가용성을 위해 활성-활성 구성에서 동일한 라우터로 구성됩니다. ExpressRoute 라우팅 도메인에 대한 자세한 내용은 [여기](expressroute-circuit-peerings.md)를 확인하세요.

허용 또는 필요한 경로만 허용하도록 사용자 지정 경로 필터를 정의할 수 있습니다. 자세한 내용 또는 이러한 변경을 수행하는 방법을 보려면 다음 문서를 참조하세요. 라우팅 필터에 대한 자세한 내용은 [PowerShell을 사용하여 ExpressRoute 회로에 대한 라우팅 만들기 및 수정](expressroute-howto-routing-classic.md)을 참조하세요.

> [!NOTE]
> Microsoft에서 공용 피어링 연결은 고객 또는 CSP가 소유한 공용 IP 주소를 통해야 하며 정의된 모든 규칙을 준수해야 합니다. 자세한 내용은 [ExpressRoute 필수 구성 요소](expressroute-prerequisites.md) 페이지를 참조하세요.  
> 
> 

## <a name="routing"></a>라우팅
ExpressRoute는 Azure Virtual Network 게이트웨이를 통해 Azure 네트워크에 연결됩니다. 네트워크 게이트웨이는 Azure 가상 네트워크에 대한 라우팅을 제공합니다.

Azure Virtual Network를 만들면 vNet에서 vNet 서브넷 간의 직접 트래픽을 위한 기본 라우팅 테이블도 만들어집니다. 솔루션에서 기본 경로 테이블로 부족한 경우 사용자 지정 어플라이언스로 나가는 트래픽을 경로 지정하거나 특정 서브넷 또는 외부 네트워크로 경로를 차단하는 사용자 지정 경로를 만들 수 있습니다.

### <a name="default-routing"></a>기본 라우팅
기본 경로 테이블에는 다음과 같은 경로가 포함됩니다.

* 한 서브넷 내에서 라우팅
* 가상 네트워크 내에서 서브넷-서브넷
* 인터넷으로
* VPN 게이트웨이를 사용하여 가상 네트워크-가상 네트워크
* VPN 또는 ExpressRoute 게이트웨이를 사용하여 가상 네트워크-온-프레미스 네트워크

![대체 텍스트](./media/expressroute-for-cloud-solution-providers/default-routing.png)  

### <a name="user-defined-routing-udr"></a>UDR(사용자 정의 라우팅)
사용자 정의 경로를 사용하여 가상 네트워크의 할당된 서브넷에서 다른 서브넷으로 또는 기타 미리 정의된 게이트웨이 중 하나를 통한 트래픽 아웃바운드를 제어할 수 있습니다(ExpressRoute, 인터넷 또는 VPN). 기본 시스템 라우팅 테이블을 기본 라우팅 테이블을 사용자 지정 경로로 대체하는 사용자 정의 라우팅 테이블로 대체할 수 있습니다. 사용자 정의 라우팅을 사용하면 고객은 방화벽, 침입 탐지 어플라이언스와 같은 어플라이언스로 특정 경로를 만들거나 사용자 정의 경로를 호스팅하는 서브넷에서 특정 서브넷으로 액세스를 차단할 수 있습니다. 사용자 정의 경로에 대한 개요는 [여기](../virtual-network/virtual-networks-udr-overview.md)에서 확인하세요. 

## <a name="security"></a>보안
사용 중인 모델(Connect-To 또는 Connect-Through)에 따라 고객은 vNet에 보안 정책을 정의하고 CSP에 vNet에 대해 정의할 보안 정책 요구 사항을 제공합니다. 다음 보안 기준을 정의할 수 있습니다.

1. **고객 격리** - Azure 플랫폼은 안전한 데이터베이스에 GRE 터널에서 각 고객의 트래픽을 캡슐화하는 데 사용되는 고객 ID 및 vNet 정보를 저장하여 고객 격리를 제공합니다.
2. **NSG(네트워크 보안 그룹)** 규칙은 Azure의 vNet 내에서 서브넷 내부 및 외부로 허용된 트래픽을 정의하기 위한 것입니다. 기본적으로 NSG에는 인터넷에서 vNet으로 트래픽을 차단하는 차단 규칙과 vNet 내의 트래픽에 대한 허용 규칙이 포함됩니다. 네트워크 보안 그룹에 대한 자세한 내용은 [여기](https://azure.microsoft.com/blog/network-security-groups/)를 확인하세요.
3. **강제 터널링** - Azure에서 ExpressRoute 연결을 통해 온-프레미스 데이터 센터로 리디렉션하도록 생성된 인터넷 바인딩된 트래픽을 리디렉션하는 옵션입니다. 강제 터널링에 대한 자세한 내용은 [여기](expressroute-routing.md#advertising-default-routes)를 확인하세요.  
4. **암호화** - ExpressRoute 회로가 특정 고객에게 전용으로 지정되었더라도 네트워크 공급자가 이를 위반할 수 있으며 침입자가 패킷 트래픽을 검사할 수 있습니다. 이 문제를 해결하기 위해 고객 또는 CSP는 온-프레미스 리소스 및 Azure 리소스 간에 흐르는 모든 트래픽에 대해 IPSec 터널 모드 정책을 정의하여 연결을 통해 트래픽을 암호화할 수 있습니다(위의 그림 5: ExpressRoute 보안에서 고객 1에 대한 선택적 터널 모드 IPSec 참조). 두 번째 옵션은 ExpressRoute 회로의 엔드포인트 각각에서 방화벽 어플라이언스를 사용하는 것입니다. ExpressRoute 회로를 통한 트래픽을 암호화하려면 양쪽 끝에 추가 타사 방화벽 VM/어플라이언스를 설치해야 합니다.

![대체 텍스트](./media/expressroute-for-cloud-solution-providers/expressroute-security.png)  

## <a name="next-steps"></a>다음 단계
클라우드 솔루션 공급자 서비스는 기본 아웃소싱 공급자로 현재 입지를 유지하면서 비싼 인프라와 기능을 구입할 필요 없이 고객에게 가치를 높이는 방법을 제공합니다. CSP API를 통해 Microsoft Azure와 원활하게 통합이 가능하며 기존 관리 프레임워크 내에서 Microsoft Azure의 관리를 통합할 수 있습니다.  

추가 정보는 다음 링크에서 확인할 수 있습니다.

[클라우드 솔루션 공급자 프로그램의 Azure](https://docs.microsoft.com/azure/cloud-solution-provider)  
[클라우드 솔루션 공급자로 거래 준비](https://partner.microsoft.com/en-us/solutions/cloud-reseller-pre-launch).  
[Microsoft Cloud 솔루션 공급자 리소스](https://partner.microsoft.com/en-us/solutions/cloud-reseller-resources).
