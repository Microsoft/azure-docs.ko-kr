---
title: Azure AD 응용 프로그램 프록시에 대 한 네트워크 토폴로지 고려 사항
description: Azure AD 애플리케이션 프록시를 사용할 때 네트워크 토폴로지 고려 사항을 다룹니다.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: mimart
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 075b2c92168afe0c366608266c38b14394b73cff
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/21/2019
ms.locfileid: "74275478"
---
# <a name="network-topology-considerations-when-using-azure-active-directory-application-proxy"></a>Azure Active Directory 애플리케이션 프록시를 사용할 때 네트워크 토폴로지 고려 사항

이 문서에서는 애플리케이션을 원격으로 게시 및 액세스하기 위해 Azure AD(Azure Active Directory) 애플리케이션 프록시를 사용할 때 네트워크 토폴로지 고려 사항을 설명합니다.

## <a name="traffic-flow"></a>트래픽 흐름

Azure AD 애플리케이션 프록시를 통해 애플리케이션을 게시할 때 사용자에게서 애플리케이션으로 이동하는 트래픽은 다음 3가지 연결을 통과합니다.

1. 사용자가 Azure의 Azure AD 애플리케이션 프록시 공용 엔드포인트에 연결
1. 애플리케이션 프록시 서비스에서 애플리케이션 프록시 커넥터에 연결
1. 애플리케이션 프록시 커넥터에서 대상 애플리케이션에 연결

![대상 애플리케이션에 사용자의 트래픽 흐름을 보여 주는 다이어그램](./media/application-proxy-network-topology/application-proxy-three-hops.png)

## <a name="tenant-location-and-application-proxy-service"></a>테넌트 위치 및 애플리케이션 프록시 서비스

Azure AD 테넌트에 등록할 때 테넌트의 지역은 지정 하는 국가/지역에 따라 결정 됩니다. 애플리케이션 프록시를 사용하도록 설정하면 테넌트에 대한 애플리케이션 프록시 서비스 인스턴스가 Azure AD 테넌트와 동일한 지역이나 가장 가까운 지역에서 선택되거나 만들어집니다.

예를 들어 Azure AD 테넌트의 국가 또는 지역이 영국인 경우 모든 애플리케이션 프록시 커넥터는 EU 데이터 센터의 서비스 인스턴스를 사용합니다. 사용자가 게시된 애플리케이션에 액세스할 때 해당 트래픽은 이러한 위치의 애플리케이션 프록시 서비스 인스턴스를 통과합니다.

## <a name="considerations-for-reducing-latency"></a>대기 시간 단축을 위한 고려 사항

모든 프록시 솔루션은 네트워크 연결에 대기 시간을 발생시킵니다. 원격 액세스 솔루션으로 프록시 또는 VPN 솔루션 중 어느 것을 선택하든지 간에 항상 회사 네트워크 내부에 연결을 사용하는 일련의 서버가 포함됩니다.

조직은 일반적으로 자신의 경계 네트워크에 서버 엔드포인트를 포함합니다. 그렇지만 Azure AD 애플리케이션 프록시를 사용할 경우 커넥터는 회사 네트워크에 있으나 트래픽은 클라우드의 프록시 서비스를 통해 흐릅니다. 경계 네트워크는 필요하지 않습니다.

다음 섹션에서는 대기 시간을 더 줄이는 데 도움이 되는 추가 제안 사항에 대해 설명합니다. 

### <a name="connector-placement"></a>커넥터 배치

애플리케이션 프록시는 테넌트 위치에 따라 사용자의 인스턴스 위치를 선택합니다. 그러나 커넥터를 설치할 위치를 결정하면 네트워크 트래픽의 대기 시간 특성을 정의할 수 있습니다.

애플리케이션 프록시 서비스를 설정할 때 다음과 같은 질문을 합니다.

- 앱은 어디에 있나요?
- 앱에 액세스하는 대부분의 사용자는 어디에 있나요?
- 애플리케이션 프록시 인스턴스는 어디에 있나요?
- Azure ExpressRoute 또는 유사한 VPN처럼 Azure 데이터 센터 설정에 대한 기존의 전용 네트워크 연결이 있나요?

커넥터는 Azure 및 애플리케이션과 통신해야 하므로(트래픽 흐름 다이어그램 2단계 및 3단계) 커넥터의 배치는 이러한 두 연결의 대기 시간에 영향을 줍니다. 커넥터의 배치를 평가할 때 다음 사항에 유의하세요.

- Single Sign-On에 대해 KCD(Kerberos 제한 위임)을 사용하려는 경우 커넥터에서 데이터 센터에 대한 가시성을 확보해야 합니다. 또한 커넥터 서버는 도메인에 가입해야 합니다.  
- 확실하지 않은 경우 애플리케이션에 더 가깝게 커넥터를 설치합니다.

### <a name="general-approach-to-minimize-latency"></a>대기 시간을 최소화하는 일반적인 방법

각 네트워크 연결을 최적화하여 엔드투엔드 트래픽의 대기 시간을 최소화할 수 있습니다. 각 연결은 다음을 통해 최적화할 수 있습니다.

- 홉의 두 끝 사이의 거리 줄이기
- 트래버스하기 적합한 네트워크 선택. 예를 들어 공용 인터넷보다 프라이빗 네트워크를 트래버스하는 것이 전용 링크가 있으므로 더 빠를 수 있습니다.

Azure 및 회사 네트워크 간에 전용 VPN 또는 ExpressRoute가 있는 경우 다음을 사용하는 것이 좋습니다.

## <a name="focus-your-optimization-strategy"></a>최적화 전략에 집중

사용자와 애플리케이션 프록시 서비스 간의 연결을 제어할 수 있는 방법은 거의 없습니다. 사용자는 홈 네트워크, 커피숍 또는 다른 국가/지역에서 앱에 액세스할 수 있습니다. 대신 애플리케이션 프록시 서비스에서 애플리케이션 프록시 커넥터로, 그런 다음 앱으로 연결을 최적화할 수 있습니다. 사용자 환경에서 다음과 같은 패턴을 통합하는 것이 좋습니다.

### <a name="pattern-1-put-the-connector-close-to-the-application"></a>패턴 1: 커넥터를 애플리케이션 가까이에 배치

커넥터를 고객 네트워크의 대상 애플리케이션 가까이에 배치합니다. 이 구성은 커넥터와 애플리케이션이 가까이 있기 때문에 토폴로지 다이어그램에서 3단계를 최소화합니다.

도메인 컨트롤러에 대한 가시선이 커넥터에 필요한 경우 이 패턴이 유리합니다. 대부분의 시나리오에 적합하기 때문에 대부분의 고객이 이 패턴을 사용합니다. 이 패턴을 패턴 2와 조합하여 서비스와 커넥터 사이의 트래픽을 최적화할 수도 있습니다.

### <a name="pattern-2-take-advantage-of-expressroute-with-microsoft-peering"></a>패턴 2: Microsoft 피어 링으로 Express 경로 활용

Microsoft 피어링이 있는 ExpressRoute를 설정한 경우 애플리케이션 프록시와 커넥터 간 트래픽에 대해 더 빨라진 ExpressRoute 연결을 사용할 수 있습니다. 커넥터는 여전히 네트워크에서 앱에 가까운 위치를 유지합니다.

### <a name="pattern-3-take-advantage-of-expressroute-with-private-peering"></a>패턴 3: 프라이빗 피어링이 있는 ExpressRoute 활용

Azure 및 회사 네트워크 간에 프라이빗 피어링이 있는 전용 VPN 또는 ExpressRoute를 설정한 경우 다른 옵션이 있습니다. 이 구성에서는 일반적으로 Azure의 가상 네트워크가 회사 네트워크의 확장으로 간주됩니다. 따라서 Azure 데이터 센터에 커넥터를 설치할 수 있으며, 커넥터-앱 연결에 낮은 대기 시간 요구 사항을 계속해서 충족할 수 있습니다.

트래픽은 전용 연결을 통해 전송되므로 대기 시간에 나쁜 영향을 주지 않습니다. 또한 커넥터가 Azure AD 테넌트 위치와 가까운 Azure 데이터 센터에 설치되어 있으므로 애플리케이션 프록시 서비스와 커넥터 간 대기 시간이 단축됩니다.

![Azure 데이터 센터 내에 설치된 커넥터를 보여 주는 다이어그램](./media/application-proxy-network-topology/application-proxy-expressroute-private.png)

### <a name="other-approaches"></a>다른 방법

이 문서에서는 커넥터 배치를 집중적으로 설명하지만 대기 시간을 개선하기 위해 애플리케이션의 배치를 변경할 수도 있습니다.

조직은 더욱 더 네트워크를 호스티드 환경으로 이동하고 있습니다. 이렇게 함으로써 회사 네트워크에 포함되는 호스티드 환경에 앱을 배치할 수 있으며 계속 도메인 내에 있게 됩니다. 이 경우 이전 섹션에서 설명한 패턴을 새 애플리케이션 위치에 적용할 수 있습니다. 이 옵션을 고려하는 경우 [Azure AD Domain Services](../../active-directory-domain-services/overview.md)를 참조하세요.

또한 다른 위치 및 네트워크에 있는 대상 앱에 [커넥터 그룹](application-proxy-connector-groups.md)을 사용하여 커넥터를 구성하는 것이 좋습니다.

## <a name="common-use-cases"></a>일반 사용 예

이 섹션에서는 몇 가지 일반적인 시나리오를 살펴봅니다. Azure AD 테넌트(즉, 프록시 서비스 엔드포인트)가 미국에 위치한다고 가정합니다. 이러한 사용 사례에서 설명한 고려 사항은 전 세계 다른 지역에도 적용됩니다.

이러한 시나리오에서는 각 연결을 "홉"으로 지칭하고 보다 쉬운 이해를 위해 번호를 매깁니다.

- **홉 1**: 사용자-애플리케이션 프록시 연결
- **홉 2**: 애플리케이션 프록시 서비스-애플리케이션 프록시 커넥터 연결
- **홉 3**: 애플리케이션 프록시 커넥터-대상 애플리케이션 연결 

### <a name="use-case-1"></a>사용 사례 1

**시나리오:** 앱이 동일한 지역의 사용자와 함께 미국에 있는 조직의 네트워크에 있습니다. Azure 데이터 센터 및 회사 네트워크 간에 ExpressRoute 또는 VPN이 존재하지 않습니다.

**권장 사항:** 이전 섹션에서 설명한 대로 패턴 1을 따릅니다. 대기 시간 개선을 위해 필요한 경우 ExpressRoute를 사용하는 것이 좋습니다.

이것은 단순한 패턴입니다. 커넥터를 앱 가까이에 배치하여 홉 3을 최적화합니다. 일반적으로 커넥터는 KCD 작업을 수행하기 위해 앱과 데이터 센터에 대한 시야를 사용하여 설치되므로 자연스러운 선택이기도 합니다.

![사용자, 프록시, 커넥터 및 앱이 모두 미국에 있음을 보여 주는 다이어그램](./media/application-proxy-network-topology/application-proxy-pattern1.png)

### <a name="use-case-2"></a>사용 사례 2

**시나리오:** 앱이 전 세계에 분산된 사용자와 함께 미국에 있는 조직의 네트워크에 있습니다. Azure 데이터 센터 및 회사 네트워크 간에 ExpressRoute 또는 VPN이 존재하지 않습니다.

**권장 사항:** 이전 섹션에서 설명한 대로 패턴 1을 따릅니다.

일반적인 패턴은 홉 3을 최적화하는 것이며, 이 경우 커넥터를 앱 근처에 배치합니다. 홉 3이 모두 동일한 지역에 있는 경우 일반적으로 비용이 많이 들지 않습니다. 하지만 전 세계의 사용자가 미국에 있는 애플리케이션 프록시 인스턴스에 액세스하므로 홉 1은 사용자 위치에 따라 비용이 더 높을 수 있습니다. 모든 프록시 솔루션은 전 세계에 분산된 사용자에 따라 유사한 특성을 포함한다는 것에 주목해야 합니다.

![사용자는 전 세계에 분산 되어 있지만 다른 모든 항목은 미국에 있습니다.](./media/application-proxy-network-topology/application-proxy-pattern2.png)

### <a name="use-case-3"></a>사용 사례 3

**시나리오:** 앱이 미국에 있는 조직의 네트워크에 있습니다. Microsoft 피어링이 있는 ExpressRoute는 Azure 및 회사 네트워크 간에 존재합니다.

**권장 사항:** 이전 섹션에서 설명한 패턴 1과 2를 따릅니다.

먼저 커넥터를 앱에 최대한 가까이 배치합니다. 그런 다음 시스템에서 홉 2에 ExpressRoute를 자동으로 사용합니다.

ExpressRoute 링크가 Microsoft 피어링을 사용하는 경우 프록시와 커넥터 간의 트래픽은 해당 링크를 통해 흐르게 됩니다. 홉 2는 대기 시간을 최적화하게 됩니다.

![프록시와 커넥터 간 ExpressRoute를 보여 주는 다이어그램](./media/application-proxy-network-topology/application-proxy-pattern3.png)

### <a name="use-case-4"></a>사용 사례 4

**시나리오:** 앱이 미국에 있는 조직의 네트워크에 있습니다. 프라이빗 피어링이 있는 ExpressRoute는 Azure 및 회사 네트워크 간에 존재합니다.

**권장 사항:** 이전 섹션에서 설명한 패턴 3을 따릅니다.

ExpressRoute 프라이빗 피어링을 통해 회사 네트워크에 연결된 Azure 데이터 센터에 커넥터를 배치합니다.

커넥터를 Azure 데이터 센터에 배치할 수 있습니다. 커넥터가 프라이빗 네트워크를 통해 애플리케이션 및 데이터 센터에 대한 가시권을 계속 확보하므로 홉 3은 최적화된 상태로 유지됩니다. 또한 홉 2는 더욱 최적화됩니다.

![Azure 데이터 센터의 커넥터, 커넥터와 앱 간 Express 경로](./media/application-proxy-network-topology/application-proxy-pattern4.png)

### <a name="use-case-5"></a>사용 사례 5

**시나리오:** 앱은 EU의 조직 네트워크에 있고 애플리케이션 프록시 인스턴스와 대부분의 사용자는 미국에 있습니다.

**권장 사항:** 커넥터를 앱 가까이 배치합니다. 미국 사용자는 동일한 지역에 있는 애플리케이션 프록시 인스턴스에 액세스하므로 홉 1의 비용은 그다지 비싸지 않습니다. 홉 3이 최적화됩니다. ExpressRoute를 사용하여 홉 2를 최적화하는 것이 좋습니다.

![다이어그램은 EU의 미국, 커넥터 및 앱에서 사용자 및 프록시를 보여줍니다.](./media/application-proxy-network-topology/application-proxy-pattern5b.png)

이 상황에서 다른 한 가지 변수를 사용하도록 고려할 수 있습니다. 조직에 있는 대부분 사용자가 미국에 있는 경우 네트워크가 미국으로도 확장될 가능성이 있습니다. 커넥터를 미국에 배치하고, 전용 내부 회사 네트워크 회선을 유럽에 있는 애플리케이션에 사용할 수 있습니다. 이 방식으로 홉 2와 3이 최적화됩니다.

![다이어그램은 미국, 유럽의 앱에서 사용자, 프록시 및 커넥터를 보여줍니다.](./media/application-proxy-network-topology/application-proxy-pattern5c.png)

## <a name="next-steps"></a>다음 단계

- [애플리케이션 프록시 사용](application-proxy-add-on-premises-application.md)
- [Single Sign-On 사용](application-proxy-configure-single-sign-on-with-kcd.md)
- [조건부 액세스 사용](application-proxy-integrate-with-sharepoint-server.md)
- [애플리케이션 프록시에서 발생한 문제 해결](application-proxy-troubleshoot.md)
