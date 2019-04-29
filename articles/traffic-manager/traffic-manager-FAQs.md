---
title: Azure Traffic Manager - FAQ
description: 이 문서에서는 Traffic Manager에 대한 질문과 대답을 제공합니다.
services: traffic-manager
documentationcenter: ''
author: KumudD
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/26/2019
ms.author: kumud
ms.openlocfilehash: 6086c182763885b62f28ab093be2a7f3f8282b8a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60803045"
---
# <a name="traffic-manager-frequently-asked-questions-faq"></a>Traffic Manager FAQ(질문과 대답)

## <a name="traffic-manager-basics"></a>Traffic Manager 기본 사항

### <a name="what-ip-address-does-traffic-manager-use"></a>Traffic Manager가 사용하는 IP 주소는 어떻게 되나요?

[Traffic Manager 작동 방식](../traffic-manager/traffic-manager-how-it-works.md)에서 설명했듯이 Traffic Manager는 DNS 수준에서 작동합니다. 클라이언트를 적절한 서비스 엔드포인트로 보내기 위해 DNS 응답을 전송합니다. 그러면 클라이언트는 Traffic Manager를 통해서가 아니라 직접 서비스 엔드포인트에 연결합니다.

따라서 Traffic Manager는 클라이언트가 연결할 엔드포인트 또는 IP 주소를 제공하지 않습니다. 서비스에 고정 IP 주소가 필요한 경우 Traffic Manager가 아니라 서비스에서 구성해야 합니다.

### <a name="what-types-of-traffic-can-be-routed-using-traffic-manager"></a>Traffic Manager를 사용하여 라우팅할 수 있는 트래픽 유형은 무엇입니까?
[Traffic Manager 작동 방식](../traffic-manager/traffic-manager-how-it-works.md)의 설명처럼 Traffic Manager 엔드포인트는 Azure 내부 또는 외부에서 호스팅되는 모든 인터넷 연결 서비스가 될 수 있습니다. 따라서 Traffic Manager는 공용 인터넷에서 발생한 트래픽을 인터넷에 연결된 엔드포인트 집합으로 라우팅할 수 있습니다. 개인 네트워크 내부에 엔드포인트(예: [Azure Load Balancer](../load-balancer/load-balancer-overview.md#internalloadbalancer) 내부 버전)가 있거나 사용자가 그러한 내부 네트워크에서 DNS 요청을 수행하는 경우 Traffic Manager를 이러한 트래픽에 사용할 수 없습니다.


### <a name="does-traffic-manager-support-sticky-sessions"></a>Traffic Manager는 '고정' 세션을 지원하나요?

[Traffic Manager 작동 방식](../traffic-manager/traffic-manager-how-it-works.md)에서 설명했듯이 Traffic Manager는 DNS 수준에서 작동합니다. 클라이언트를 적절한 서비스 엔드포인트로 보내기 위해 DNS 응답을 사용합니다. 클라이언트는 Traffic Manager를 통해서가 아니라 직접 서비스 엔드포인트에 연결합니다. 따라서 Traffic Manager는 클라이언트와 서비스 간에 HTTP 트래픽을 표시하지 않습니다.

또한 Traffic Manager에서 수신하는 DNS 쿼리의 원본 IP 주소는 클라이언트가 아니라 재귀 DNS 서비스에 속합니다. 따라서 Traffic Manager에는 개별 클라이언트를 추적할 방법이 없으므로 '고정' 세션을 구현할 수 없습니다. 이 제한은 모든 DNS 기반 트래픽 관리 시스템에 공통적으로 적용되며, Traffic Manager에 특정되지 않습니다.

### <a name="why-am-i-seeing-an-http-error-when-using-traffic-manager"></a>Traffic Manager를 사용할 때 HTTP 오류가 나타나는 이유는 무엇인가요?

[Traffic Manager 작동 방식](../traffic-manager/traffic-manager-how-it-works.md)에서 설명했듯이 Traffic Manager는 DNS 수준에서 작동합니다. 클라이언트를 적절한 서비스 엔드포인트로 보내기 위해 DNS 응답을 사용합니다. 그러면 클라이언트는 Traffic Manager를 통해서가 아니라 직접 서비스 엔드포인트에 연결합니다. Traffic Manager는 클라이언트와 서버 간에 HTTP 트래픽을 표시하지 않습니다. 따라서 표시된 모든 HTTP 오류는 애플리케이션에서 가져온 것이어야 합니다. 클라이언트를 애플리케이션에 연결하는 경우 모든 DNS 확인 단계를 완료합니다. Traffic Manager가 애플리케이션 트래픽 흐름에 있는 모든 상호 작용을 포함합니다.

따라서 추가 조사는 애플리케이션에 중점을 두어야 합니다.

클라이언트의 브라우저에서 보낸 HTTP 호스트 헤더는 문제의 가장 일반적인 소스입니다. 사용자가 사용하는 도메인 이름에 대한 올바른 호스트 헤더를 수락하도록 애플리케이션을 구성하는지 확인합니다. Azure App Service를 사용하는 엔드포인트의 경우 [Traffic Manager를 사용하는 Azure App Service의 웹앱에 대한 사용자 지정 도메인 이름 구성](../app-service/web-sites-traffic-manager-custom-domain-name.md)을 참조하세요.

### <a name="what-is-the-performance-impact-of-using-traffic-manager"></a>Traffic Manager를 사용할 때 성능 영향은 무엇인가요?

[Traffic Manager 작동 방식](../traffic-manager/traffic-manager-how-it-works.md)에서 설명했듯이 Traffic Manager는 DNS 수준에서 작동합니다. 클라이언트가 서비스 엔드포인트에 직접 연결되므로 연결이 설정된 후 Traffic Manager를 사용하면 성능 영향이 발생하지 않습니다.

Traffic Manager는 DNS 수준에서 애플리케이션과 통합하므로 추가 DNS 조회를 DNS 확인 체인에 삽입해야 합니다. DNS 확인 시간에 대한 Traffic Manager의 영향은 최소입니다. Traffic Manager는 이름 서버의 글로벌 네트워크를 사용하며 DNS 쿼리가 사용 가능한 가장 가까운 이름 서버로 라우팅되도록 [애니캐스트](https://en.wikipedia.org/wiki/Anycast) 네트워킹을 사용합니다. 또한 DNS 응답의 캐싱은 Traffic Manager를 사용하여 발생한 추가 DNS 대기 시간이 세션 중 일부에만 적용되는 것을 의미합니다.

성능 메서드는 사용 가능한 가장 가까운 엔드포인트로 트래픽을 라우팅합니다. 최종적인 결론은 이 메서드와 연결된 전체 성능 영향을 최소화해야 한다는 것입니다. DNS 대기 시간 증가는 엔드포인트에 대한 더 낮은 네트워크 대기 시간으로 오프셋되어야 합니다.

### <a name="what-application-protocols-can-i-use-with-traffic-manager"></a>Traffic Manager에는 어떤 애플리케이션 프로토콜을 사용할 수 있나요?

[Traffic Manager 작동 방식](../traffic-manager/traffic-manager-how-it-works.md)에서 설명했듯이 Traffic Manager는 DNS 수준에서 작동합니다. DNS 조회가 완료되면 클라이언트는 Traffic Manager를 통해서가 아닌 애플리케이션 엔드포인트에 직접 연결됩니다. 따라서 이 연결에서는 모든 애플리케이션 프로토콜을 사용할 수 있습니다. TCP를 모니터링 프로토콜로 선택하면 애플리케이션 프로토콜을 사용하지 않고 Traffic Manager의 엔드포인트 상태 모니터링을 수행할 수 있습니다. 애플리케이션 프로토콜을 사용하여 상태가 확인되도록 선택한 경우 엔드포인트가 HTTP 또는 HTTPS GET 요청에 응답할 수 있어야 합니다.

### <a name="can-i-use-traffic-manager-with-a-naked-domain-name"></a>'naked' 도메인 이름으로 Traffic Manager를 사용할 수 있나요?
예. Azure Traffic Manager 프로필을 참조 하 여 도메인 이름 apex에 대 한 별칭 레코드를 만드는 방법에 알아보려면 참조 [구성에 루트 도메인 이름을 Traffic Manager와 함께 지원 별칭 레코드](../dns/tutorial-alias-tm.md)합니다.

### <a name="does-traffic-manager-consider-the-client-subnet-address-when-handling-dns-queries"></a>DNS 쿼리를 처리할 때 Traffic Manager는 클라이언트 서브넷 주소를 고려하나요? 
예, Traffic Manager는 수신한 DNS 쿼리의 원본 IP 주소(일반적으로 DNS 확인자의 IP 주소) 외에도 최종 사용자를 대신하여 요청하는 확인자에 의한 쿼리에 포함되는 경우 지리 및 성능 및 서브넷 라우팅 방법에 대한 조회를 수행할 때 클라이언트 서브넷 주소를 고려합니다.  
특히 지원하는 확인자의 클라이언트 서브넷 주소를 DNS 서버로 전달할 수 있는 [EDNS0(DNS에 대한 확장 메커니즘)](https://tools.ietf.org/html/rfc2671)을 제공하는 [RFC 7871 – DNS 쿼리의 클라이언트 서브넷](https://tools.ietf.org/html/rfc7871)입니다.

### <a name="what-is-dns-ttl-and-how-does-it-impact-my-users"></a>DNS TTL이란 무엇이며 사용자에게 어떤 영향을 주나요?

DNS 쿼리가 Traffic Manager에 도착하면 TTL(Time to Live)이라는 값을 응답에 설정합니다. 초 단위로 지정된 이 값은 이 응답을 캐시할 기간을 DNS 확인자 다운스트림에 나타냅니다. DNS 확인자가 항상 이 결과를 캐시하는 것은 아니지만 캐시하면 Traffic Manager DNS 서버로 이동하지 않고 캐시에서 후속 쿼리에 응답할 수 있습니다. 이 경우 응답에 다음과 같은 영향을 줍니다.
- TTL이 높을수록 Traffic Manager DNS 서버에 도착하는 쿼리 수가 감소하며, 처리된 쿼리 수가 청구 가능한 사용량이기 때문에 고객의 비용을 줄일 수 있습니다.
- TTL이 높을수록 DNS 조회를 수행하는 데 걸리는 시간을 줄일 수 있습니다.
- 또한 TTL이 높을수록 Traffic Manager가 검색 에이전트를 통해 얻은 최신 상태 정보가 데이터에 반영되지 않음을 의미합니다.

### <a name="how-high-or-low-can-i-set-the-ttl-for-traffic-manager-responses"></a>Traffic Manager 응답에 대해 설정할 수 있는 TTL 상한 또는 하한은 어느 정도인가요?

프로필 수준별로 DNS TTL을 0초에서 2,147,483,647초 사이로 설정할 수 있습니다(최대 범위는 [RFC-1035](https://www.ietf.org/rfc/rfc1035.txt )에 따름). TTL이 0이면 다운스트림 DNS 확인자가 쿼리 응답을 캐시하지 않고 모든 쿼리가 확인을 위해 Traffic Manager DNS 서버에 도착합니다.

### <a name="how-can-i-understand-the-volume-of-queries-coming-to-my-profile"></a>내 프로필로 들어오는 쿼리 볼륨을 파악하려면 어떻게 해야 할까요? 
Traffic Manager에서 제공하는 메트릭 중 하나로 프로필에서 응답한 쿼리 수가 있습니다. 이 정보는 프로필 수준 집계에서 얻을 수 있거나 더 자세히 분할하여 특정 엔드포인트가 반환된 쿼리 볼륨을 확인할 수 있습니다. 또한 쿼리 응답 볼륨이 설정한 조건과 어긋나는 경우 알리도록 경고를 설정할 수 있습니다. 자세한 내용은 [Traffic Manager 메트릭 및 경고](traffic-manager-metrics-alerts.md)를 참조하세요.

## <a name="traffic-manager-geographic-traffic-routing-method"></a>Traffic Manager 지리적 트래픽 라우팅 방법

### <a name="what-are-some-use-cases-where-geographic-routing-is-useful"></a>지리적 라우팅이 유용한 사용 사례에는 어떤 것이 있습니까? 
지리적 라우팅 형식은 Azure 고객이 지리적 지역에 따라 사용자를 구분해야 하는 시나리오에서 사용할 수 있습니다. 예를 들어 지리적 트래픽 라우팅 방법을 사용하게 되면 특정 지역의 사용자에게 다른 지역의 사용자와는 다른 사용자 환경을 제공할 수 있습니다. 또 다른 예는 특정 지역의 해당 사용자들이 해당 지역의 엔드포인트에서만 제공받도록 하는 로컬 데이터 독립성 지침을 준수하는 것입니다.

### <a name="how-do-i-decide-if-i-should-use-performance-routing-method-or-geographic-routing-method"></a>성능 라우팅 방법 또는 지리적 라우팅 방법을 사용해야 하는지를 결정하려면 어떻게 할까요? 
인기 있는 이 두 가지 라우팅 방법의 주요 차이점은 성능 라우팅 방법의 기본 목표가 호출자에 가장 짧은 대기 시간을 제공할 수 있는 엔드포인트에 트래픽을 보내는 것이지만, 지리적 라우팅의 기본 목표가 특정 엔드포인트로 의도적으로 라우팅할 수 있도록 호출자에 지리적 펜스를 적용하는 것에 있습니다. 지리적 인접성과 짧은 대기 시간 간의 상관 관계가 있으므로 서로 겹치지만 항상 그렇지는 않습니다. 호출자에게 더 나은 대기 시간 환경을 제공할 수 있는 다른 지역에 엔드포인트가 있을 수 있습니다. 이 경우 성능 라우팅은 사용자를 해당 엔드포인트로 보내지, 지리적 라우팅은 항상 사용자를 해당 지리적 영역에 매핑된 엔드포인트로 보냅니다. 더 명확히 하기 위해 다음 예제를 생각해 보겠습니다. 지리적 라우팅을 사용하면 아시아의 모든 트래픽을 미국의 엔드포인트로 보내고, 미국의 모든 트래픽을 아시아의 엔드포인트로 보내는 것과 같은 특수 매핑을 만들 수 있습니다. 이 경우 지리적 라우팅에서는 수행하도록 구성한 작업을 의도적으로 정확하게 수행하며 성능 최적화는 고려하지 않습니다. 
>[!NOTE]
>성능 및 지리적 라우팅 기능이 모두 필요한 시나리오가 있을 수 있습니다. 이러한 시나리오에서는 중첩된 프로필을 선택하는 것이 좋습니다. 예를 들어 북아메리카 지역의 모든 트래픽을 미국에 엔드포인트가 있는 중첩된 프로필로 보내고, 성능 라우팅을 사용하여 해당 트래픽을 해당 집합 내에서 가장 적합한 엔드포인트로 보내는 지리적 라우팅이 포함된 부모 프로필을 설정할 수 있습니다. 

### <a name="what-are-the-regions-that-are-supported-by-traffic-manager-for-geographic-routing"></a>지리적 라우팅에 대해 Traffic Manager에서 지원되는 지역은 어디입니까? 
Traffic Manager에서 사용되는 국가/지역 계층 구조는 [여기](traffic-manager-geographic-regions.md)에서 확인할 수 있습니다. 이 페이지는 변경 내용으로 최신 상태를 유지하지만, [Azure Traffic Manager REST API](https://docs.microsoft.com/rest/api/trafficmanager/)를 사용하여 동일한 정보를 프로그래밍 방식으로 검색할 수도 있습니다. 

### <a name="how-does-traffic-manager-determine-where-a-user-is-querying-from"></a>Traffic Manager는 어떻게 사용자가 쿼리하는 위치를 결정합니까? 
Traffic Manager는 쿼리의 원본 IP를 찾고(대개 사용자 대신 쿼리를 수행하는 로컬 DNS 확인자일 수 있음) 지역 맵에 대한 내부 IP를 사용하여 위치를 결정합니다. 이 맵은 인터넷에서 변화를 확인하기 위해 정기적으로 업데이트됩니다. 

### <a name="is-it-guaranteed-that-traffic-manager-can-correctly-determine-the-exact-geographic-location-of-the-user-in-every-case"></a>Traffic Manager가 모든 경우에 사용자의 정확한 지리적 위치를 올바르게 결정한다고 보장할 수 있나요?
아니요. 다음과 같은 이유로 Traffic Manager에서는 DNS 쿼리의 원본 IP 주소에서 유추되는 지리적 하위 지역이 사용자 위치에 항상 올바르게 응답한다고는 보장할 수 없습니다. 

- 첫째, 이전 FAQ에 설명된 것처럼 보고 있는 원본 IP 주소는 DNS 확인자가 사용자를 대신해서 조회를 수행하는 주소입니다. DNS 확인자의 지리적 위치는 사용자의 지리적 위치에 대한 적절한 프록시이지만, 해당 DNS 확인자 서비스의 사용 공간 및 고객이 사용하도록 선택한 특정 DNS 확인자 서비스에 따라 다를 수 있습니다. 예를 들어 말레이시아에 있는 고객은 싱가포르에 있는 해당 DNS 서버가 해당 사용자/디바이스에 대한 쿼리 확인을 처리하도록 선택되었을 수 있는 DNS 확인자 서비스를 사용하도록 디바이스 설정에서 지정할 수 있습니다. 이 경우 Traffic Manager는 싱가포르 위치에 해당하는 확인자의 IP 주소만 확인할 수 있습니다. 이 페이지에서 클라이언트 서브넷 주소 지원과 관련된 이전 FAQ도 참조하세요.

- 둘째, Traffic Manager는 내부 맵을 사용하여 IP 주소-지리적 하위 지역 변환을 수행합니다. 이 맵은 정확도를 높이고 인터넷의 변화하는 특성을 고려하기 위해 지속적으로 유효성이 검사되고 업데이트되지만, 제공하는 정보가 모든 IP 주소의 지리적 위치를 정확히 나타내지 않을 수도 있습니다.


###  <a name="does-an-endpoint-need-to-be-physically-located-in-the-same-region-as-the-one-it-is-configured-with-for-geographic-routing"></a>엔드포인트를 지리적 라우팅에 대해 구성된 것과 물리적으로 동일한 지역에 배치해야 합니까? 
아니요, 엔드포인트의 위치는 지역이 매핑될 수 있는 곳에 대한 제한이 없습니다. 예를 들어 인도에 있는 모든 사용자는 미국 중부 Azure 지역에 있는 엔드포인트로 향할 수 있습니다.

### <a name="can-i-assign-geographic-regions-to-endpoints-in-a-profile-that-is-not-configured-to-do-geographic-routing"></a>지리적 라우팅을 수행하도록 구성되어 있지 않은 프로필의 엔드포인트에 지리적 지역을 할당할 수 있습니까? 

예. 프로필의 라우팅 방법이 지리적이 아닌 경우 [Azure Traffic Manager REST API](https://docs.microsoft.com/rest/api/trafficmanager/)를 사용하여 지리적 지역을 해당 프로필의 엔드포인트에 할당할 수 있습니다. 비지리적 라우팅 형식 프로필의 경우 이 구성은 무시됩니다. 이러한 프로필을 나중에 지리적 라우팅 형식으로 변경하면 Traffic Manager에서 그러한 매핑을 사용할 수 있습니다.


### <a name="why-am-i-getting-an-error-when-i-try-to-change-the-routing-method-of-an-existing-profile-to-geographic"></a>기존 프로필의 라우팅 방법을 지리적으로 변경하려고 시도하면 오류가 발생하는 이유는 무엇인가요?

지리적 라우팅을 사용하는 프로필의 모든 엔드포인트에는 매핑되어 있는 지역이 하나 이상 있어야 합니다. 기존 프로필을 지리적 라우팅 형식으로 변환하려면 라우팅 형식을 지리적으로 변경하기 전에 먼저 [Azure Traffic Manager REST API](https://docs.microsoft.com/rest/api/trafficmanager/)를 사용하여 지리적 위치를 모든 해당 엔드포인트에 연결해야 합니다. 포털을 사용하는 경우 먼저 엔드포인트를 삭제하고, 프로필의 라우팅 방법을 지리적으로 변경한 다음 지리적 지역 매핑과 함께 엔드포인트를 추가합니다. 


###  <a name="why-is-it-strongly-recommended-that-customers-create-nested-profiles-instead-of-endpoints-under-a-profile-with-geographic-routing-enabled"></a>고객에게 지리적 라우팅이 활성화된 프로필의 엔드포인트보다 중첩 프로필을 만드는 것을 권장하는 이유는 무엇입니까? 

지리적 라우팅 방법을 사용하는 경우 프로필 내 하나의 엔드포인트에만 지역을 할당할 수 있습니다. 해당 엔드포인트가 연결된 자식 프로필을 갖는 중첩 형식이 아닌 경우, 엔드포인트가 비정상 상태가 되더라도 Traffic Manager는 트래픽을 계속 전송합니다. 트래픽을 전송하지 않는다고 더 나을 것이 없기 때문입니다. 할당된 지역이 비정상 상태가 된 엔드포인트에 할당된 지역의 "부모"인 경우라도 Traffic Manager는 다른 엔드포인트로 장애 조치(failover)하지 않습니다. 예를 들어 스페인 지역인 엔드포인트가 비정상 상태인 경우 유럽 지역이 할당된 다른 엔드포인트로 장애 조치(failover)하지 않습니다. 이는 고객이 그들의 프로필에서 설정한 지리적 경계를 Traffic Manager가 존중하도록 하기 위함입니다. 엔드포인트가 비정상 상태가 될 때 다른 엔드포인트로 장애 조치(failover)하는 방법을 활용하려면 지리적 지역을 개별 엔드포인트 대신 프로필 내 여러 엔드포인트를 사용하는 중첩 프로필에 할당하는 것이 좋습니다. 이 방식에서 중첩 자식 프로필의 엔드포인트가 실패하면 트래픽은 동일한 중첩 자식 프로필 내부의 다른 엔드포인트로 장애 조치할 수 있습니다.

### <a name="are-there-any-restrictions-on-the-api-version-that-supports-this-routing-type"></a>이 라우팅 형식을 지원하는 API 버전에 제한이 있습니까?

예. API 버전 2017-03-01 이상만 지리적 라우팅 형식을 지원합니다. 이전 API 버전은 지리적 라우팅 형식의 프로필을 생성하거나 지리적 지역을 엔드포인트로 할당하는 데 사용할 수 없습니다. 이전 API 버전을 사용하여 Azure 구독에서 프로필을 검색한 경우 지리적 라우팅 형식의 프로필은 반환되지 않습니다. 또한 이전 API 버전을 사용하는 경우, 지리적 지역 할당을 사용하는 엔드포인트가 있는 반환된 프로필에서 지리적 지역 할당이 표시되지 않습니다.

## <a name="traffic-manager-subnet-traffic-routing-method"></a>Traffic Manager 서브넷 트래픽 라우팅 방법

### <a name="what-are-some-use-cases-where-subnet-routing-is-useful"></a>서브넷 라우팅이 유용한 사용 사례에는 어떤 것이 있습니까?
서브넷 라우팅을 사용하면 DNS 요청 IP 주소의 원본 IP로 식별된 특정 사용자 집합에 대해 제공하는 경험을 구분할 수 있습니다. 예를 들어 사용자가 사용자 회사 HQ에서 웹 사이트에 연결하는 경우 다른 콘텐츠를 표시할 수 있습니다. 또 다른 경우 특정 ISP에서 IPv6을 사용할 때 해당 ISP의 성능이 낮은 경우 IPv4 연결만 지원하는 엔드포인트에 액세스하도록 사용자를 제한하는 것입니다.
서브넷 라우팅 방법을 사용하는 또 다른 이유는 중첩된 프로필 집합에서 다른 프로필과 함께 사용하기 때문입니다. 예를 들어, 사용자 지오펜싱에 대해서는 지리적 라우팅 방법을 사용하려고 하지만, 특정 ISP에 대해서는 다른 라우팅 방법을 수행하려는 경우, 부모 프로필과 같이 서브넷 라우팅 방법을 사용하여 프로필을 가질 수 있으며, 특정 자식 프로필을 사용하고 모든 사용자에 대해 표준 지리적 프로필을 갖도록 해당 ISP를 재정의할 수 있습니다.

### <a name="how-does-traffic-manager-know-the-ip-address-of-the-end-user"></a>Traffic Manager는 최종 사용자의 IP 주소를 어떻게 알 수 있나요?
일반적으로 최종 사용자 디바이스는 자신을 대신해 DNS 조회 수행하는 DNS 확인자를 사용합니다. Traffic Manager는 그러한 확인자의 송신 IP를 원본 IP로 인식합니다. 또한 서브넷 라우팅 방법은 요청과 함께 전달된 EDNS0 ECS(확장 클라이언트 서브넷) 정보가 있는지 여부를 확인하기 위해 조회합니다. ECS 정보가 있는 경우 라우팅을 결정하는 데 사용되는 주소입니다. ECS 정보가 없을 경우 쿼리의 원본 IP는 라우팅 목적에 사용됩니다.

### <a name="how-can-i-specify-ip-addresses-when-using-subnet-routing"></a>서브넷 라우팅을 사용하는 경우 IP 주소를 어떻게 지정할 수 있나요?
엔드포인트와 연결할 IP 주소는 두 가지 방법으로 지정할 수 있습니다. 먼저 시작 및 끝 주소를 통해 쿼드 점으로 구분된 10진수 8진수 표기법을 사용하여 범위를 지정할 수 있습니다(예: 1.2.3.4-5.6.7.8 또는 3.4.5.6-3.4.5.6). 두 번째로는 CIDR 표기법을 사용하여 범위를 지정할 수 있습니다(예: 1.2.3.0/24). 범위를 여러 개 지정할 수 있으며, 범위 집합에 두 가지 표기법 형식을 모두 사용할 수 있습니다. 몇 가지 제한 사항이 적용됩니다.
-   각 IP는 단일 엔드포인트에만 매핑해야 하므로 겹치는 주소 범위를 사용할 수 없습니다.
-   시작 주소가 끝 주소보다 클 수 없습니다.
-   CIDR 표기법의 경우 ‘/’ 앞의 IP 주소는 해당 범위의 시작 주소여야 합니다(예: 1.2.3.0/24는 올바르지만 1.2.3.4.4/24는 올바르지 않음).

### <a name="how-can-i-specify-a-fallback-endpoint-when-using-subnet-routing"></a>서브넷 라우팅을 사용하는 경우 대체(fallback) 엔드포인트를 어떻게 지정할 수 있나요?
서브넷 라우팅을 사용한 프로필에서, 엔드포인트에 매핑된 서브넷이 없을 경우 다른 엔드포인트와 일치하지 않는 요청은 여기로 이동됩니다. 요청이 들어오고 어떠한 엔드포인트에도 매핑되지 않은 경우 또는 엔드포인트에 매핑되었으나 엔드포인트 상태가 정상이 아닌 경우 Traffic Manager는 NXDOMAIN 응답을 반환하므로 프로필에 이러한 대체(fallback) 엔드포인트가 있는 것이 좋습니다.

### <a name="what-happens-if-an-endpoint-is-disabled-in-a-subnet-routing-type-profile"></a>서브넷 라우팅 형식 프로필에서 엔드포인트가 비활성화된 경우에는 어떻게 되나요?
서브넷 라우팅을 사용한 프로필에서 엔드포인트가 비활성화된 경우 Traffic Manager는 해당 엔드포인트 및 서브넷 매핑이 존재하지 않는 것처럼 작동합니다. Traffic Manager는 해당 IP 주소 매핑과 일치하지 않는 쿼리가 수신되고 엔드포인트가 비활성화된 경우에는 대체(fallback) 엔드포인트(매핑되지 않음)를 반환하고, 그러한 엔드포인트가 존재하지 않는 경우에는 NXDOMAIN 응답을 반환합니다.

## <a name="traffic-manager-multivalue-traffic-routing-method"></a>Traffic Manager 다중값 트래픽 라우팅 방법

### <a name="what-are-some-use-cases-where-multivalue-routing-is-useful"></a>다중값 라우팅이 유용한 사용 사례에는 어떤 것이 있습니까?
다중값 라우팅은 여러 정상 엔드포인트를 단일 쿼리 응답에 반환합니다. 이 방법의 주요 장점은 엔드포인트가 정상이 아닌 경우 클라이언트에 다른 DNS를 호출하지 않고도 재시도할 수 있는 추가 옵션이 있다는 점입니다(업스트림 캐시에서 동일한 값을 반환할 수도 있음). 작동 중단을 최소화하려는 가용성이 중요한 응용 프로그램에 적용할 수 있습니다.
다중값 라우팅 방법에 대한 다른 용도는 엔드포인트가 IPv4 및 IPv6 주소에 대해 “이중 홈 방식”이고, 호출자에게 엔드포인트에 대한 연결을 시작할 때 선택할 수 있는 두 옵션을 제공하려는 경우입니다.

### <a name="how-many-endpoints-are-returned-when-multivalue-routing-is-used"></a>다중값 라우팅이 사용될 때 얼마나 많은 엔드포인트가 반환되나요?
반환할 엔드포인트의 최대 수를 지정할 수 있습니다. 또한 쿼리가 수신될 때 다중값은 정상 엔드포인트 수 이하를 반환합니다. 이 구성에 대해 가능한 최댓값은 10입니다.

### <a name="will-i-get-the-same-set-of-endpoints-when-multivalue-routing-is-used"></a>다중값 라우팅이 사용될 때 동일한 엔드포인트 집합을 얻을 수 있나요?
각 쿼리에서 동일한 엔드포인트 집합이 반환된다고 보장할 수는 없습니다. 이는 엔드포인트가 응답에 포함되지 않는 지점에서 일부 엔드포인트가 정상이 아닐 수도 있다는 사실에 영향을 받습니다.

## <a name="real-user-measurements"></a>실제 사용자 측정

### <a name="what-are-the-benefits-of-using-real-user-measurements"></a>실제 사용자 측정을 사용하는 장점은 무엇인가요?
성능 라우팅 메서드를 사용하는 경우 Traffic Manager에서는 원본 IP와 EDNS 클라이언트 서브넷(통과되는 경우)을 검사하고 서비스에서 유지하는 네트워크 대기 시간 인텔리전스에 대해 확인하여 최종 사용자에 대해 최상의 Azure 지역을 연결하도록 선택합니다. 실제 사용자 측정은 최종 사용자가 Azure에 연결된 최종 사용자 네트워크에 이 대기 시간 테이블이 적절하게 걸쳐 있는지 확인할 뿐만 아니라 해당 성능이 해당 테이블에 영향을 주도록 하여 최종 사용자를 기반으로 향상시킵니다. 이로 인해 최종 사용자의 라우팅 정확도가 증가합니다.

### <a name="can-i-use-real-user-measurements-with-non-azure-regions"></a>비 Azure 지역에서 실제 사용자 측정을 사용할 수 있나요?
실제 사용자 측정은 Azure 지역에 도달하는 대기 시간을 측정하고 보고합니다. 비 Azure 지역에서 호스팅되는 엔드포인트에서 성능 기반 라우팅을 사용하는 경우 이 엔드포인트와 연결되도록 선택한 대표 Azure 지역에 대한 대기 시간 정보를 증가시켜서 이 기능을 사용할 수 있습니다.

### <a name="which-routing-method-benefits-from-real-user-measurements"></a>실제 사용자 측정을 사용할 수 있는 라우팅 메서드는 무엇인가요?
실제 사용자 측정을 통해 얻은 추가 정보는 성능 라우팅 메서드를 사용하는 프로필에만 적용 가능합니다. Azure Portal을 통해 볼 때 실제 사용자 측정 링크를 모든 프로필에서 사용할 수 있는지 확인합니다.

### <a name="do-i-need-to-enable-real-user-measurements-each-profile-separately"></a>각 프로필에 실제 사용자 측정을 개별적으로 사용하도록 설정해야 하나요?
아니요, 구독당 한 번만 사용하도록 설정하기만 하면 측정되고 보고된 모든 대기 시간 정보를 모든 프로필에 사용할 수 있습니다.

### <a name="how-do-i-turn-off-real-user-measurements-for-my-subscription"></a>내 구독에서 실제 사용자 측정을 해제하려면 어떻게 할까요?
클라이언트 애플리케이션에서 대기 시간 측정을 수집하고 다시 보내는 작업을 중지하는 경우 실제 사용자 측정과 관련하여 발생되는 요금이 중지될 수 있습니다. 예를 들어 JavaScript 측정값이 웹 페이지에 포함되면 JavaScript를 제거하거나 페이지를 렌더링할 때 해당 호출을 해제하여 이 기능의 사용을 중지할 수 있습니다.

키를 삭제하여 실제 사용자 측정을 해제할 수도 있습니다. 키를 삭제하면 해당 키가 있는 Traffic Manager로 전송되는 측정값이 삭제됩니다.

### <a name="can-i-use-real-user-measurements-with-client-applications-other-than-web-pages"></a>웹 페이지가 아닌 클라이언트 애플리케이션에서 실제 사용자 측정을 사용할 수 있나요?
예, 실제 사용자 측정은 다른 유형의 최종 사용자 클라이언트를 통해 수집된 데이터를 수집하도록 설계되었습니다. 이 FAQ는 새롭게 지원되는 유형의 클라이언트 애플리케이션으로 업데이트됩니다.

### <a name="how-many-measurements-are-made-each-time-my-real-user-measurements-enabled-web-page-is-rendered"></a>실제 사용자 측정을 사용하는 웹 페이지를 렌더링할 때마다 만들어지는 측정값은 얼마인가요?
실제 사용자 측정이 제공되는 JavaScript 측정값과 함께 사용되는 경우 렌더링되는 각 페이지는 결과적으로 6개의 측정값을 생성합니다. 이러한 측정값은 Traffic Manager 서비스로 다시 보고됩니다. Traffic Manager 서비스에 보고된 측정값의 수에 따라 이 기능에 대한 요금이 부과됩니다. 예를 들어, 측정값이 측정되고 보고되기 전에 사용자가 웹 페이지에서 벗어나는 경우 해당 측정값은 청구되는 요금에 포함되지 않습니다.

### <a name="is-there-a-delay-before-real-user-measurements-script-runs-in-my-webpage"></a>내 웹 페이지에서 실제 사용자 측정 스크립트를 실행하기 전에 지연되나요?
아니요, 스크립트를 호출하기 전에 프로그래밍된 지연은 없습니다.

### <a name="can-i-use-real-user-measurements-with-only-the-azure-regions-i-want-to-measure"></a>측정하려는 Azure 지역에서만 실제 사용자 측정을 사용할 수 있나요?
아니요, 호출될 때마다 실제 사용자 측정 스크립트는 서비스에 의해 결정된 대로 6개의 Azure 지역 집합을 측정합니다. 이 값은 다른 호출 간의 변경 내용을 설정하고 이러한 호출이 많이 발생하는 경우 측정 검사는 다른 Azure 지역으로 확장됩니다.

### <a name="can-i-limit-the-number-of-measurements-made-to-a-specific-number"></a>측정값 수를 지정된 숫자로 제한할 수 있나요?
JavaScript 측정값을 웹 페이지 내에 포함하고 사용 시작 및 중지 시기를 완전히 제어합니다. Traffic Manager 서비스가 측정될 Azure 지역 목록에 대한 요청을 받으면 지역 집합이 반환됩니다.

### <a name="can-i-see-the-measurements-taken-by-my-client-application-as-part-of-real-user-measurements"></a>실제 사용자 측정의 일환으로 클라이언트 애플리케이션에서 측정된 측정값을 볼 수 있나요?
측정 논리가 클라이언트 애플리케이션에서 실행되므로 대기 시간 측정값을 보는 작업을 포함하여 상황을 완전히 제어합니다. Traffic Manager는 구독에 연결된 키 아래에서 수신한 측정값의 집계 보기를 보고하지 않습니다.

### <a name="can-i-modify-the-measurement-script-provided-by-traffic-manager"></a>Traffic Manager에서 제공하는 측정 스크립트를 수정할 수 있나요?
웹 페이지에 포함된 기능을 제어하는 동안 대기 시간을 올바르게 측정하고 보고하는지 확인하기 위해 측정값 스크립트를 변경하지 않는 것이 가장 좋습니다.

### <a name="will-it-be-possible-for-others-to-see-the-key-i-use-with-real-user-measurements"></a>내가 실제 사용자 측정에서 사용한 키를 다른 사용자가 볼 수 있나요?
웹 페이지에 측정값 스크립트를 포함하는 경우 다른 사용자가 스크립트 및 RUM(실제 사용자 측정) 키를 볼 수 있습니다. 이 키가 구독 ID와 다르고 이러한 용도로 사용하기 위해 Traffic Manager에서 생성되는지를 아는 것이 중요합니다. RUM 키를 알고 있으면 Azure 계정 보안이 손상되지 않습니다.

### <a name="can-others-abuse-my-rum-key"></a>다른 사용자가 내 RUM 키를 공격할 수 있나요?
다른 사용자가 키를 사용하여 잘못된 정보를 Azure에 보낼 수 있지만, 수신된 다른 모든 측정값과 함께 계산되기 때문에 일부 잘못된 측정값으로 인해 라우팅이 변경되지는 않습니다. 키를 변경해야 하는 경우 이전 키가 삭제되는 지점에서 키를 다시 생성할 수 있습니다.

###  <a name="do-i-need-to-put-the-measurement-javascript-in-all-my-web-pages"></a>JavaScript 측정값을 전체 웹 페이지에 배치해야 하나요?
실제 사용자 측정은 여러 번 측정할수록 가치가 높아집니다. 물론, 모든 웹 페이지에 배치할지 아니면 일부를 선택할지는 사용자의 선택입니다. 사용자가 5초 이상 머무를 것으로 예상되는 가장 자주 방문한 페이지에 배치하기 시작하는 것이 좋습니다.

### <a name="can-information-about-my-end-users-be-identified-by-traffic-manager-if-i-use-real-user-measurements"></a>실제 사용자 측정을 사용하는 경우 Traffic Manager에서 최종 사용자에 대한 정보를 식별할 수 있나요?
제공된 JavaScript 측정값을 사용하는 경우 Traffic Manager는 사용한 최종 사용자의 클라이언트 IP 주소 및 로컬 DNS 해결 프로그램의 원본 IP 주소에 대한 표시 여부를 결정합니다. Traffic Manager는 클라이언트 IP 주소에서 측정값을 보낸 특정 최종 사용자를 식별하지 못하도록 자른 후에 해당 주소를 사용합니다. 

### <a name="does-the-webpage-measuring-real-user-measurements-need-to-be-using-traffic-manager-for-routing"></a>실제 사용자 측정을 측정하는 웹 페이지에서 라우팅을 위해 Traffic Manager를 사용해야 하나요?
아니요, Traffic Manager를 사용하지 않아도 됩니다. Traffic Manager의 라우팅 쪽은 실제 사용자 측정 부분에서 별도로 운영됩니다. 동일한 웹 속성에 두 측면을 모두 포함하는 것이 좋지만 필수는 아닙니다.

### <a name="do-i-need-to-host-any-service-on-azure-regions-to-use-with-real-user-measurements"></a>실제 사용자 측정에서 사용하기 위해 Azure 지역에서 모든 서비스를 호스트해야 하나요?
아니요, 실제 사용자 측정이 작동하기 위해 Azure에서 서버 쪽 구성 요소를 호스트하지 않아도 됩니다. 단일 픽셀 이미지를 JavaScript 측정값에서 다운로드하고 다른 Azure 지역에서 실행되는 서비스를 Azure에서 호스트하고 관리합니다. 

### <a name="will-my-azure-bandwidth-usage-increase-when-i-use-real-user-measurements"></a>실제 사용자 측정을 사용하는 경우 내 Azure 대역폭 사용량이 증가하나요?
이전 응답에서 설명한 대로 실제 사용자 측정의 서버 쪽 구성 요소는 Azure에서 소유하고 관리합니다. 즉, 실제 사용자 측정을 사용하기 때문에 Azure 대역폭 사용량이 증가되지 않습니다. Azure에서 요금을 청구하는 것 외에는 어떤 대역폭 사용량도 포함하지 않습니다. Azure 지역에 대한 대기 시간을 측정하는 단일 픽셀 이미지를 다운로드하여 사용되는 대역폭을 최소화합니다. 

## <a name="traffic-view"></a>트래픽 보기

### <a name="what-does-traffic-view-do"></a>트래픽 보기의 기능은 무엇인가요?
트래픽 보기는 Traffic Manager의 기능으로 사용자 및 사용자 환경을 더 이해할 수 있도록 합니다. 서비스에서 유지하는 Traffic Manager 및 네트워크 대기 시간 인텔리전스 테이블에서 받은 쿼리를 사용하여 다음을 제공합니다.
- 사용자가 Azure에서 엔드포인트에 연결하는 지역
- 이러한 지역에서 연결 중인 사용자의 볼륨
- 사용자가 라우팅되는 Azure 지역
- 이러한 Azure 지역에 대한 해당 대기 시간 환경

이 정보는 원시 데이터로 다운로드할 수 있을 뿐만 아니라 포털에서 지리적 맵 오버레이 및 테이블 형식 보기를 통해 사용할 수 있습니다.

### <a name="how-can-i-benefit-from-using-traffic-view"></a>트래픽 보기를 사용하는 장점은 무엇인가요?

트래픽 보기를 통해 Traffic Manager 프로필에서 수신하는 트래픽을 전반적으로 볼 수 있습니다. 특히, 사용자 기반이 연결하는 위치 및 동일하게 중요한 평균 대기 시간 환경을 이해하는 데 사용할 수 있습니다. 그런 다음 예를 들어, 해당 사용자에게 더 낮은 대기 시간을 제공할 수 있는 지역으로 Azure 공간을 확장하여 초점을 맞춰야 하는 영역을 찾는 데 이 정보를 사용할 수 있습니다. 트래픽 보기를 사용하여 파생될 수 있는 다른 정보는 다른 지역에 대한 트래픽 패턴을 확인하는 것입니다. 이로 인해 해당 지역에서 트래픽의 증가 또는 감소를 확인할 수 있습니다.

### <a name="how-is-traffic-view-different-from-the-traffic-manager-metrics-available-through-azure-monitor"></a>트래픽 보기와 Azure Monitor를 통해 사용할 수 있는 Traffic Manager 메트릭의 차이는 무엇인가요?

집계 수준에서 프로필과 해당 엔드포인트에서 받은 트래픽을 이해하는 데 Azure Monitor를 사용할 수 있습니다. 또한 이를 통해 상태 검사 결과를 노출하여 엔드포인트의 상태를 추적할 수 있습니다. 뿐만 아니라 지역 수준에서 Azure에 연결하는 최종 사용자 환경을 이해해야 할 때 트래픽 보기를 사용할 수 있습니다.

### <a name="does-traffic-view-use-edns-client-subnet-information"></a>트래픽 보기에서 EDNS 클라이언트 서브넷 정보를 사용하나요?

Azure Traffic Manager에서 제공되는 DNS 쿼리는 라우팅의 정확도를 높이기 위해 ECS 정보를 고려합니다. 하지만 사용자가 연결되는 위치를 보여주는 데이터 집합을 만들 때 트래픽 보기는 DNS 확인자의 IP 주소만을 사용합니다.

### <a name="how-many-days-of-data-does-traffic-view-use"></a>트래픽 보기는 며칠 동안의 데이터를 사용하나요?

트래픽 보기에서는 사용자가 보기 전날에서 이전 7일 간의 데이터를 처리하여 해당 출력을 만듭니다. 이동 창으로써 방문할 때마다 최신 데이터를 사용합니다.

### <a name="how-does-traffic-view-handle-external-endpoints"></a>트래픽 보기는 외부 엔드포인트를 어떻게 처리하나요?

Traffic Manager 프로필의 Azure 지역 외부에서 호스팅되는 외부 엔드포인트를 사용하는 경우 해당 대기 시간 특성에 대한 프록시인 Azure 지역에 매핑되도록 선택할 수 있습니다(실제로 성능 라우팅 메서드를 사용하는 경우 필요함). 이 Azure 지역이 매핑되는 경우 트래픽 보기를 만들 때 해당 Azure 지역의 대기 시간 메트릭을 사용합니다. Azure 지역을 지정하지 않은 경우 해당 외부 엔드포인트의 데이터에서 대기 시간 정보가 비게 됩니다.

### <a name="do-i-need-to-enable-traffic-view-for-each-profile-in-my-subscription"></a>내 구독의 각 프로필에 트래픽 보기를 사용하도록 설정해야 하나요?

미리 보기 기간 중 트래픽 보기는 구독 수준에서 활성화되었습니다. 일반 공급 이전에 만든 기능 향상의 일환으로 이제 프로필 수준에서 트래픽 보기를 활성화하여 더욱 세분화된 이 기능의 활성화를 얻을 수 있습니다. 기본적으로 트래픽 보기는 프로필에 대해 비활성화됩니다.

>[!NOTE]
>미리 보기 기간 동안 구독 수준에서 트래픽을 보기를 활성화한 경우 이제 해당 구독의 각 프로필에 대해 다시 활성화해야 합니다.
 
### <a name="how-can-i-turn-off-traffic-view"></a>트래픽 보기를 해제하려면 어떻게 할까요? 
포털 또는 REST API를 사용하여 모든 프로필에 대한 트래픽 보기를 해제할 수 있습니다. 

### <a name="how-does-traffic-view-billing-work"></a>트래픽 보기 요금 청구는 어떻게 작동하나요?

트래픽 보기 가격 책정은 출력을 생성하는 데 사용되는 데이터 요소의 수를 기반으로 합니다. 현재 지원되는 유일한 데이터 형식은 프로필이 수신하는 쿼리입니다. 또한 트래픽 보기를 사용할 수 있을 때 수행된 처리에 대해서만 요금이 청구됩니다. 즉, 한 달 내의 일정 기간 동안 트래픽 보기를 사용하도록 설정하고 그 외 시간 동안 해제한 경우 요금으로 계산되는 기능을 사용하는 동안에만 데이터 요소가 처리되었습니다.

## <a name="traffic-manager-endpoints"></a>Traffic Manager 엔드포인트

### <a name="can-i-use-traffic-manager-with-endpoints-from-multiple-subscriptions"></a>여러 구독에서 엔드포인트로 Traffic Manager를 사용할 수 있습니까?

Azure Web Apps의 경우 여러 구독에서 엔드포인트를 사용하는 것은 가능하지 않습니다. Azure Web Apps은 Web Apps와 함께 사용되는 모든 사용자 지정 도메인 이름을 단일 구독 내에서만 사용할 것을 요구합니다. 여러 구독에서 동일한 도메인 이름을 가지고 Web Apps를 사용하는 것은 불가능합니다.

다른 엔드포인트 유형의 경우 Traffic Manager를 둘 이상의 구독에서 엔드포인트와 함께 사용할 수 있습니다. Traffic Manager 프로필을 구성하는 사용자에게 엔드포인트에 대한 읽기 권한이 있는 한, Resource Manager에서 모든 구독의 엔드포인트를 Traffic Manager에 추가할 수 있습니다. 이러한 권한은 [Azure Resource Manager 역할 기반 액세스 제어(RBAC)](../role-based-access-control/role-assignments-portal.md)를 사용하여 부여될 수 있습니다.


### <a name="can-i-use-traffic-manager-with-cloud-service-staging-slots"></a>클라우드 서비스 '스테이징' 슬롯으로 Traffic Manager를 사용할 수 있습니까?

예. 클라우드 서비스 '스테이징' 슬롯은 외부 엔드포인트로 Traffic Manager에서 구성될 수 있습니다. 상태 검사는 여전히 Azure 엔드포인트 비율로 요금이 청구됩니다. 외부 엔드포인트 유형을 사용 중이므로 기본 서비스에 대한 변경 내용은 자동으로 선택되지 않습니다. 외부 엔드포인트인 경우 Traffic Manager는 클라우드 서비스가 중지되거나 삭제될 때 감지할 수 없습니다. 따라서 Traffic Manager는 엔드포인트가 비활성화되거나 삭제될 때까지 상태 검사에 대한 요금 청구를 계속합니다.

### <a name="does-traffic-manager-support-ipv6-endpoints"></a>Traffic Manager는 IPv6 엔드포인트를 지원하나요?

Traffic Manager는 현재 IPv6으로 주소 지정이 가능한 이름 서버를 제공하지 않습니다. 하지만 IPv6 클라이언트를 IPv6 엔드포인트에 연결하여 Traffic Manager를 여전히 사용할 수 있습니다. 클라이언트는 Traffic Manager에 직접 DNS 요청을 만들지 않습니다. 대신 클라이언트는 재귀 DNS 서비스를 사용합니다. IPv6 전용 클라이언트는 IPv6를 통해 재귀 DNS 서비스에 요청을 보냅니다. 그런 다음 재귀 서비스는 IPv4를 사용하여 Traffic Manager 이름 서버에 연결할 수 있어야 합니다.

Traffic Manager는 엔드포인트의 DNS 이름 또는 IP 주소로 응답합니다. IPv6 엔드포인트를 지원하려면 두 가지 옵션이 있습니다. 연결된 AAAA 레코드가 있는 DNS 이름으로 엔드포인트를 추가할 수 있으며, Traffic Manager는 해당 엔드포인트의 상태를 확인하고 이를 쿼리 응답에 CNAME 레코드 형식으로 반환합니다. IPv6 주소를 사용하여 해당 엔드포인트를 직접 추가할 수도 있습니다. 또한 Traffic Manager는 AAAA 형식 레코드를 쿼리 응답에 반환합니다. 

### <a name="can-i-use-traffic-manager-with-more-than-one-web-app-in-the-same-region"></a>동일한 지역에서 둘 이상의 Web App에 Traffic Manager를 사용할 수 있습니까?

일반적으로 Traffic Manager는 다른 지역에 배포된 애플리케이션에 트래픽을 보내는 데 사용됩니다. 그러나 애플리케이션이 동일한 지역에 둘 이상의 배포를 가지는 곳에 사용될 수도 있습니다. Traffic Manager Azure 엔드포인트는 동일한 Traffic Manager 프로필에 추가되는 동일한 Azure 지역에서 둘 이상의 웹앱 엔드포인트를 허용하지 않습니다.

### <a name="how-do-i-move-my-traffic-manager-profiles-azure-endpoints-to-a-different-resource-group"></a>Traffic Manager 프로필의 Azure 엔드포인트를 다른 리소스 그룹으로 이동하려면 어떻게 해야 하나요?

Traffic Manager 프로필과 연결된 Azure 엔드포인트는 리소스 ID를 사용하여 추적됩니다. 엔드포인트로 사용되는 Azure 리소스(공용 IP, Classic Cloud Service, WebApp, 또는 중첩되어 사용된 기타 Traffic Manager 프로필)가 다른 리소스 그룹으로 이동되면 리소스 ID도 변경됩니다. 이 시나리오의 경우 현재로서는 프로필에 먼저 엔드포인트를 삭제한 다음 다시 추가하여 Traffic Manager 프로필을 업데이트해야 합니다. 

##  <a name="traffic-manager-endpoint-monitoring"></a>Traffic Manager 엔드포인트 모니터링

### <a name="is-traffic-manager-resilient-to-azure-region-failures"></a>Traffic Manager 자체는 Azure 하위 지역 장애에 복원력이 있나요?

Traffic Manager는 Azure에서 가용성 높은 애플리케이션을 전달하는 데 있어서 핵심적인 구성 요소입니다.
높은 가용성을 제공하려면 Traffic Manager는 매우 높은 수준의 가용성을 유지해야 하고 지역 실패에 대해 복원력도 뛰어나야 합니다.

기본적으로 Traffic Manager 구성 요소는 모든 Azure 지역의 전체 오류에 대해 복원력을 갖습니다. 이러한 복원력은 DNS 이름 서버, API, 저장소 계층 및 엔드포인트 모니터링 서비스 등 모든 Traffic Manager 구성 요소에 해당됩니다.

전체 Azure 하위 지역의 가동 중단과 같은 드문 경우에도, Traffic Manager는 계속 정상적으로 작동해야 합니다. 여러 Azure 하위 지역에 배포된 애플리케이션은 Traffic Manager에 의존하여 애플리케이션의 사용 가능한 인스턴스로 트래픽을 전달할 수 있습니다.

### <a name="how-does-the-choice-of-resource-group-location-affect-traffic-manager"></a>리소스 그룹의 위치 선택은 Traffic Manager에 어떤 영향을 주나요?

Traffic Manager는 단일 전역 서비스입니다. 지역에 영향을 받지 않습니다. 리소스 그룹의 위치 선택은 해당 리소스 그룹에 배포된 Traffic Manager 프로필에 어떤 영향도 주지 않습니다.

Azure Resource Manager에서는 모든 리소스 그룹에서 '위치'를 지정해야 하며 이를 통해 해당 리소스 그룹에 배포된 리소스의 기본 위치가 결정됩니다. Traffic Manager 프로필을 만들면 리소스 그룹에 만들어집니다. 모든 Traffic Manager 프로필은 위치로 **전역**을 사용하고 리소스 그룹 기본값을 재정의합니다.

### <a name="how-do-i-determine-the-current-health-of-each-endpoint"></a>각 엔드포인트의 현재 상태를 확인하려면 어떻게 해야 하나요?

각 엔드포인트의 현재 상태 모니터링 상태 및 전체 프로필은 Azure 포털에 표시됩니다. 이 정보는 Traffic Manager [REST API](https://msdn.microsoft.com/library/azure/mt163667.aspx), [PowerShell cmdlets](https://docs.microsoft.com/powershell/module/az.trafficmanager) 및 [크로스 플랫폼 Azure CLI](../cli-install-nodejs.md)를 통해서도 사용할 수 있습니다.

Azure Monitor를 사용하여 엔드포인트의 상태를 추적하고 이에 대한 시각적 표현을 볼 수도 있습니다. Azure Monitor에 대한 자세한 내용은 [Azure Monitoring 설명서](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics)를 참조하세요.

### <a name="can-i-monitor-https-endpoints"></a>HTTPS 엔드포인트를 모니터링할 수 있나요?

예. Traffic Manager는 HTTPS를 통한 검색을 지원합니다. 모니터링 구성에서 프로토콜로 **HTTPS**를 구성합니다.

Traffic Manager는 다음을 포함하는 인증서 유효성 검사를 제공할 수 없습니다.

* 서버 쪽 인증서의 유효성이 검사되지 않습니다.
* SNI 서버 쪽 인증서가 지원되지 않습니다.
* 클라이언트 인증서는 지원되지 않습니다.

### <a name="do-i-use-an-ip-address-or-a-dns-name-when-adding-an-endpoint"></a>엔드포인트를 추가할 때 IP 주소 또는 DNS 이름을 사용하나요?
Traffic Manager는 DNS 이름, IPv4 주소 및 IPv6 주소를 참조하는 세 가지 방법으로 엔드포인트를 추가할 수 있도록 지원합니다. 엔드포인트가 IPv4 또는 IPv6 주소로 추가되면 쿼리 응답은 각각 레코드 형식 A 또는 AAAA가 됩니다. 엔드포인트가 DNS 이름으로 추가된 경우 쿼리 응답은 레코드 형식 CNAME이 됩니다. IPv4 또는 IPv6 주소로 엔드포인트를 추가하는 것은 엔드포인트가 **외부** 형식일 때만 허용됩니다.
모든 라우팅 방법 및 모니터링 설정은 세 가지 엔드포인트 주소 지정 형식에서 지원됩니다.

### <a name="what-types-of-ip-addresses-can-i-use-when-adding-an-endpoint"></a>엔드포인트를 추가할 때 사용할 수 있는 IP 주소의 형식은 무엇인가요?
Traffic Manager를 사용하면 IPv4 또는 IPv6 주소를 사용하여 엔드포인트를 지정할 수 있습니다. 아래 나열된 몇 가지 제한 사항이 있습니다.
- 예약된 사설 IP 주소 공간에 해당하는 주소는 허용되지 않습니다. 이러한 주소에는 RFC 1918, RFC 6890, RFC 5737, RFC 3068, RFC 2544 및 RFC 5771에서 호출한 주소가 포함됩니다.
- 주소에는 포트 범호가 없어야 합니다(프로필 구성 설정에 사용할 포트를 지정할 수 있음). 
- 동일한 프로필의 두 엔드포인트가 동일한 대상 IP 주소를 가질 수 없습니다.

### <a name="can-i-use-different-endpoint-addressing-types-within-a-single-profile"></a>단일 프로필 내에서 서로 다른 엔드포인트 주소 지정 형식을 사용할 수 있나요?
아니요, Traffic Manager에서는 IPv4 및 IPv6 주소 지정 형식을 혼합할 수 있는 다중값 라우팅 형식을 사용한 프로필의 경우 외에는 프로필 내에서 엔드포인트 주소 지정 형식을 혼합하지 못합니다.

### <a name="what-happens-when-an-incoming-querys-record-type-is-different-from-the-record-type-associated-with-the-addressing-type-of-the-endpoints"></a>들어오는 쿼리의 레코드 형식이 엔드포인트의 주소 지정 형식과 연결된 레코드 형식과 다른 경우에는 어떻게 되나요?
쿼리가 프로필에 대해 수신되면 Traffic Manager는 먼저 지정된 라우팅 방법을 기준으로 반환해야 하는 엔드포인트와 엔드포인트의 상태를 찾습니다. 그런 다음, 아래 테이블을 기반으로 응답을 반환하기 전에 들어오는 쿼리에서 요청된 레코드 형식 및 엔드포인트와 연결된 레코드 형식을 조회합니다.

다중값 이외의 모든 라우팅 방법을 사용한 프로필의 경우:

|들어오는 쿼리 요청|    엔드포인트 유형|  제공된 응답|
|--|--|--|
|모두 |  A / AAAA / CNAME |  대상 엔드포인트| 
|A |    A / CNAME | 대상 엔드포인트|
|A |    AAAA |  NODATA |
|AAAA | AAAA / CNAME |  대상 엔드포인트|
|AAAA | A | NODATA |
|CNAME |    CNAME | 대상 엔드포인트|
|CNAME  |A / AAAA | NODATA |
|

다중값으로 설정된 라우팅 방법을 사용한 프로필의 경우:

|들어오는 쿼리 요청|    엔드포인트 유형 | 제공된 응답|
|--|--|--|
|모두 |  A와 AAAA의 혼합 | 대상 엔드포인트|
|A |    A와 AAAA의 혼합 | 형식 A의 유일한 대상 엔드포인트|
|AAAA   |A와 AAAA의 혼합|     형식 AAAA의 유일한 대상 엔드포인트|
|CNAME |    A와 AAAA의 혼합 | NODATA |

### <a name="can-i-use-a-profile-with-ipv4--ipv6-addressed-endpoints-in-a-nested-profile"></a>중첩된 프로필에서 IPv4/IPv6으로 주소 지정된 엔드포인트를 사용한 프로필을 사용할 수 있나요?
예, 다중값 형식 프로필은 중첩된 프로필에서 부모 프로필이 될 수 없다는 예외를 제외하고는 사용할 수 있습니다.


### <a name="i-stopped-an-azure-cloud-service--web-application-endpoint-in-my-traffic-manager-profile-but-i-am-not-receiving-any-traffic-even-after-i-restarted-it-how-can-i-fix-this"></a>Traffic Manager 프로필에서 Azure 클라우드 서비스/웹 애플리케이션 엔드포인트를 중지했지만 다시 시작한 후에도 트래픽을 받지 못하고 있습니다. 이 문제를 어떻게 해결할 수 있나요?

Azure 클라우드 서비스/웹 애플리케이션 엔드포인트가 중지되면 Traffic Manager는 상태 검사를 중지하고 엔드포인트가 다시 시작된 것을 감지한 후에만 상태 검사를 다시 시작합니다. 이러한 지연을 방지하려면 엔드포인트를 다시 시작한 후에 Traffic Manager 프로필에서 해당 엔드포인트를 사용하지 않도록 설정한 후 다시 사용하도록 설정합니다.   

### <a name="can-i-use-traffic-manager-even-if-my-application-does-not-have-support-for-http-or-https"></a>애플리케이션에 HTTP 또는 HTTPS에 대한 지원이 없는 경우에도 Traffic Manager를 사용할 수 있나요?

예. TCP를 모니터링 프로토콜로 지정하면 Traffic Manager가 TCP 연결을 시작하고 엔드포인트의 응답을 기다릴 수 있습니다. 엔드포인트가 제한 시간 내에 연결을 설정하는 응답으로 연결 요청에 회신하면 해당 엔드포인트는 정상 상태로 표시됩니다.

### <a name="what-specific-responses-are-required-from-the-endpoint-when-using-tcp-monitoring"></a>TCP 모니터링을 사용하는 경우 엔드포인트에서 어떤 특정 응답이 필요한가요?

TCP 모니터링을 사용하는 경우 Traffic Manager는 엔드포인트의 지정된 포트에 SYN 요청을 전송하여 3방향 TCP 핸드셰이크를 시작합니다. 그런 다음 제한 시간 설정에 지정된 기간 동안 엔드포인트의 응답을 기다립니다. 엔드포인트가 모니터링 설정에 지정된 제한 시간 내에 SYN-ACK 응답으로 SYN 요청에 응답하면 해당 엔드포인트는 정상 상태로 간주됩니다. SYN-ACK 응답이 수신되면 Traffic Manager는 RST로 다시 응답하여 연결을 다시 설정합니다.

### <a name="how-fast-does-traffic-manager-move-my-users-away-from-an-unhealthy-endpoint"></a>Traffic Manager는 사용자를 비정상 엔드포인트에서 얼마나 빨리 이동하나요?

Traffic Manager는 다음과 같이 Traffic Manager 프로필의 장애 조치(failover) 동작을 제어할 수 있는 여러 설정을 제공합니다.
- 검색 간격을 10초로 설정하여 Traffic Manager가 엔드포인트를 더 자주 검색하도록 지정할 수 있습니다. 이렇게 하면 비정상 상태로 전환되는 엔드포인트를 가능한 한 빨리 검색할 수 있습니다. 
- 상태 검사 요청이 시간 초과되기 전에 기다릴 기간(최소 제한 시간 값은 5초임)을 지정할 수 있습니다.
- 엔드포인트가 비정상 상태로 표시되기 전에 발생할 수 있는 오류 수를 지정할 수 있습니다. 이 값의 하한은 0으로, 이 경우 첫 번째 상태 검사에 실패하는 즉시 엔드포인트가 비정상 상태로 표시됩니다. 그러나 허용되는 오류 수에 최소값 0을 사용하면 검색 시 발생할 수 있는 일시적인 문제로 인해 엔드포인트가 회전에서 제거될 수 있습니다.
- DNS 응답에 대한 TTL(Time to Live)을 하한인 0으로 지정할 수 있습니다. 이렇게 하면 DNS 확인자가 응답을 캐시할 수 없으며, 새 쿼리가 각각 Traffic Manager에 보유된 최신 상태 정보를 통합하는 응답을 받습니다.

이러한 설정을 통해 Traffic Manager는 10초 내에 장애 조치(failover)를 제공할 수 있으며, 이 시간 후에는 엔드포인트가 비정상 상태가 되고 DNS 쿼리가 해당 프로필에 대해 수행됩니다.

### <a name="how-can-i-specify-different-monitoring-settings-for-different-endpoints-in-a-profile"></a>프로필의 엔드포인트마다 다른 모니터링 설정을 지정하려면 어떻게 하나요?

Traffic Manager 모니터링 설정은 프로필 수준별로 지정됩니다. 하나의 엔드포인트에 대해서만 다른 모니터링 설정을 사용해야 하는 경우 해당 엔드포인트를 모니터링 설정이 부모 프로필과 다른 [중첩 프로필](traffic-manager-nested-profiles.md)로 설정하면 됩니다.

### <a name="how-can-i-assign-http-headers-to-the-traffic-manager-health-checks-to-my-endpoints"></a>Traffic Manager 상태 검사에서 HTTP 헤더를 어떻게 내 엔드포인트에 할당할 수 있나요?
Traffic Manager를 사용하면 시작하는 HTTP(S) 상태 검사에서 사용자 지정 헤더를 내 엔드포인트에 지정할 수 있습니다. 사용자 지정 헤더를 지정하려는 경우 프로필 수준에서 수행하거나(모든 엔드포인트에 적용 가능) 엔드포인트 수준에서 지정할 수 있습니다. 헤더가 두 수준 모두에서 정의되면 엔드포인트 수준에서 지정된 헤더는 프로필 수준의 헤더를 재정의합니다.
이에 대한 일반적인 사용 사례는 Traffic Manager 요청이 다중 테넌트 환경에서 호스트된 엔드포인트로 올바르게 라우팅되도록 호스트 헤더를 지정하는 것입니다. 또 다른 사용 사례는 엔드포인트의 HTTP(S) 요청 로그에서 Traffic Manager 요청을 식별하는 것입니다.

## <a name="what-host-header-do-endpoint-health-checks-use"></a>엔드포인트 상태 검사에 어떤 호스트 헤더가 사용되나요?
제공된 사용자 지정 호스트 헤더 설정이 없는 경우 Traffic Manager에서 사용된 호스트 헤더는 프로필에서 구성된 엔드포인트 대상의 DNS 이름(사용 가능한 경우)입니다. 


### <a name="what-are-the-ip-addresses-from-which-the-health-checks-originate"></a>상태 검사가 시작되는 IP 주소는 무엇인가요?

[여기](https://azuretrafficmanagerdata.blob.core.windows.net/probes/azure/probe-ip-ranges.json)를 클릭하여 Traffic Manager 상태 검사가 시작될 수 있는 IP 주소를 나열하는 JSON 파일을 볼 수 있습니다. JSON 파일에 나열된 IP를 검토하여 이 목록의 IP 주소에서 들어오는 연결의 엔드포인트에서 상태 검사가 허용되는지 확인합니다.

### <a name="how-many-health-checks-to-my-endpoint-can-i-expect-from-traffic-manager"></a>Traffic Manager에서 내 엔드포인트에 대해 수행하는 예상 상태 검사 수는 몇 개인가요?

엔드포인트에 도착하는 Traffic Manager 상태 검사 수는 다음 요인에 따라 달라집니다.
- 모니터링 간격에 대해 설정한 값(간격이 작을수록 주어진 기간 동안 더 많은 요청이 엔드포인트에 도착함)
- 상태 검사가 시작되는 위치 수(이러한 검사가 예상되는 IP 주소는 앞의 FAQ에 나와 있음)

### <a name="how-can-i-get-notified-if-one-of-my-endpoints-goes-down"></a>내 엔드포인트 중 하나가 중단된 경우 알림을 받으려면 어떻게 해야 할까요? 
Traffic Manager에서 제공하는 메트릭 중 하나로 프로필의 엔드포인트 상태가 있습니다. 이 상태는 프로필 내의 모든 엔드포인트에 대한 집계(예: 엔드포인트의 75%가 정상) 또는 엔드포인트 수준별 집계로 확인할 수 있습니다. Traffic Manager 메트릭은 Azure Monitor를 통해 공개되며, 엔드포인트 상태가 변경되면 알림을 받을 수 있도록 [경고 기능](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md)을 사용할 수 있습니다. 자세한 내용은 [Traffic Manager 메트릭 및 경고](traffic-manager-metrics-alerts.md)를 참조하세요.  

## <a name="traffic-manager-nested-profiles"></a>Traffic Manager 중첩 프로필

### <a name="how-do-i-configure-nested-profiles"></a>중첩 프로필을 구성하려면 어떻게 해야 하나요?

중첩 Traffic Manager 프로필은 Azure Resource Manager 및 클래식 Azure REST API, Azure PowerShell cmdlet, 플랫폼 간 Azure CLI 명령을 사용하여 구성할 수 있습니다. 새 Azure Portal을 통해서도 지원됩니다.

### <a name="how-many-layers-of-nesting-does-traffic-manger-support"></a>트래픽 관리자가 지원하는 중첩 계층 수는 얼마나 되나요?

프로필을 10개 수준 깊이까지 중첩할 수 있습니다. '루프'는 허용되지 않습니다.

### <a name="can-i-mix-other-endpoint-types-with-nested-child-profiles-in-the-same-traffic-manager-profile"></a>동일한 Traffic Manager 프로필에서 중첩 자식 프로필과 다른 엔드포인트 유형을 혼합할 수 있나요?

예. 프로필 내에서 서로 다른 유형의 엔드포인트를 결합하는 방법에는 제한이 없습니다.

### <a name="how-does-the-billing-model-apply-for-nested-profiles"></a>중첩 프로필에는 요금 청구 모델이 어떻게 적용되나요?

중첩 프로필을 사용한다고 해서 가격이 더 부과되지는 않습니다.

Traffic Manager 요금 청구는 엔드포인트 상태 검사 및 수백만 개의 DNS 쿼리로 구성됩니다.

* 엔드포인트 상태 검사: 부모 프로필에서 엔드포인트로 구성된 경우 자식 프로필에 대해서는 요금이 부과되지 않습니다. 하위 프로필에서 엔드포인트 모니터링은 일반적인 방식으로 청구됩니다.
* DNS 쿼리: 각 쿼리는 한 번만 계산됩니다. 하위 프로필의 엔드포인트를 반환하는 상위 프로필에 대한 쿼리는 상위 프로필에 대해서만 계산됩니다.

자세한 내용은 [Traffic Manager 가격 책정 페이지](https://azure.microsoft.com/pricing/details/traffic-manager/)를 참조하세요.

### <a name="is-there-a-performance-impact-for-nested-profiles"></a>중첩 프로필이 성능에 영향을 미치나요?

아니요. 중첩 프로필을 사용해도 성능에 미치는 영향은 없습니다.

Traffic Manager 이름 서버는 각 DNS 쿼리를 처리하는 경우 프로필 계층 구조를 내부적으로 통과합니다. 상위 프로필에 대한 DNS 쿼리는 하위 프로필의 엔드포인트에 대한 DNS 응답을 받을 수 있습니다. 단일 CNAME 레코드는 단일 프로필이나 중첩 프로필을 사용하는 경우 사용합니다. 계층 구조에서 각 프로필에 대한 CNAME 레코드를 만들 필요가 없습니다.

### <a name="how-does-traffic-manager-compute-the-health-of-a-nested-endpoint-in-a-parent-profile"></a>Traffic Manager에서 상위 프로필의 중첩 엔드포인트 상태를 계산하려면 어떻게 하나요?

상위 프로필에서는 하위 프로필에 대한 상태 검사를 직접 수행하지 않습니다. 대신, 하위 프로필 엔드포인트의 상태는 하위 프로필의 전반적인 상태를 계산하는 데 사용됩니다. 이 정보는 중첩 프로필 계층 구조로 전파되어 중첩 엔드포인트의 상태를 확인합니다. 상위 프로필은 이 집계된 상태를 사용하여 하위 프로필에 트래픽을 보낼 수 있을지 결정합니다.

다음 테이블에서는 중첩 엔드포인트에 대한 Traffic Manager의 상태 검사 동작에 대해 설명합니다.

| 자식 프로필 모니터 상태 | 부모 엔드포인트 모니터 상태 | 메모 |
| --- | --- | --- |
| Disabled. 하위 프로필을 사용하지 않도록 설정했습니다. |중지됨 |부모 엔드포인트 상태는 Stopped이며 Disabled가 아닙니다. Disabled 상태는 부모 프로필에서 엔드포인트를 사용할 수 없도록 설정했음을 표시하도록 예약되어 있습니다. |
| Degraded. 하나 이상의 자식 프로필 엔드포인트가 Degraded 상태입니다. |Online: 자식 프로필의 Online 엔드포인트 수가 MinChildEndpoints 값 이상입니다.<BR>CheckingEndpoint: 자식 프로필의 Online 및 CheckingEndpoint 엔드포인트 수 합계가 MinChildEndpoints 값 이상입니다.<BR>Degraded: 그렇지 않은 경우 |트래픽이 CheckingEndpoint 상태의 엔드포인트로 라우팅됩니다. MinChildEndpoints를 너무 높게 설정하는 경우 엔드포인트의 성능이 항상 저하됩니다. |
| Online. 하나 이상의 하위 프로필 엔드포인트가 온라인 상태입니다. 성능이 저하된 엔드포인트가 없습니다. |위 내용을 참조하세요. | |
| CheckingEndpoints. 하나 이상의 하위 프로필 엔드포인트가 'CheckingEndpoint'입니다. '온라인' 또는 '성능이 저하'된 엔드포인트가 없습니다. |위와 동일합니다. | |
| Inactive. 모든 하위 프로필 엔드포인트가 사용 안 함 또는 중지됨이거나 엔드포인트가 없는 프로필입니다. |중지됨 | |

## <a name="next-steps"></a>다음 단계:
- Traffic Manager [엔드포인트 모니터링 및 자동 장애 조치(failover)](../traffic-manager/traffic-manager-monitoring.md)에 대해 자세히 알아봅니다.
- Traffic Manager [트래픽 라우팅 방법](../traffic-manager/traffic-manager-routing-methods.md)에 대해 자세히 알아봅니다.
