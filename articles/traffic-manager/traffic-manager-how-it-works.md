---
title: Azure Traffic Manager 작동 방식 | Microsoft Docs
description: 이 문서는 Traffic Manager가 웹 애플리케이션의 고성능 및 고가용성을 위해 트래픽을 라우팅하는 방식을 이해하는 데 도움이 됩니다.
services: traffic-manager
documentationcenter: ''
author: duongau
manager: twooley
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/05/2019
ms.author: duau
ms.openlocfilehash: 376aa04228113c56f0f797f737833802c9eca021
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/07/2021
ms.locfileid: "107029491"
---
# <a name="how-traffic-manager-works"></a>Traffic Manager 작동 방식

Azure Traffic Manager를 사용하면 애플리케이션 엔드포인트에 트래픽 분산을 제어할 수 있습니다. 엔드포인트는 Azure의 내부 또는 외부에서 호스팅되는 모든 인터넷 연결 서비스입니다.

Traffic Manager는 다음과 같은 두 가지 주요 이점을 제공합니다.

- 여러 가지 [트래픽 라우팅 메서드](traffic-manager-routing-methods.md)
- [엔드포인트 상태 연속 모니터링](traffic-manager-monitoring.md) 및 엔드포인트가 실패할 경우 자동 장애 조치(failover)

클라이언트가 서비스에 연결하려고 시도하면 먼저 IP 주소에 대한 서비스의 DNS 이름을 확인합니다. 그런 다음, 클라이언트는 해당 IP 주소에 연결하여 서비스에 액세스합니다.

**이해해야 할 가장 중요한 점은 Traffic Manager가 애플리케이션 계층(계층-7)에 있는 DNS 수준에서 작동한다는 것입니다.**  Traffic Manager는 DNS를 사용하여 트래픽 라우팅 메서드의 규칙에 따라 클라이언트를 특정 서비스 엔드포인트에 연결합니다. 클라이언트는 선택한 엔드포인트에 **직접** 연결됩니다. Traffic Manager는 프록시 또는 게이트웨이가 아닙니다. Traffic Manager는 클라이언트와 서비스 간에 전달되는 트래픽을 표시하지 않습니다.

## <a name="traffic-manager-example"></a>Traffic Manager 예제

Contoso Corp에서 새 파트너 포털을 개발했습니다. 이 포털의 URL은 `https://partners.contoso.com/login.aspx`입니다. 애플리케이션은 3개의 Azure 지역에서 호스팅됩니다. 가용성을 개선하고 전역 성능을 최대화하려면 Traffic Manager를 사용하여 클라이언트 트래픽을 사용 가능한 가장 가까운 엔드포인트에 배포합니다.

이 구성을 만들기 위해 다음 단계를 완료합니다.

1. 3개의 서비스 인스턴스를 배포합니다. 이러한 배포의 DNS 이름은 'contoso us.cloudapp.net', 'contoso eu.cloudapp.net' 및 'contoso asia.cloudapp.net'입니다.
1. 'contoso.trafficmanager.net'이라는 Traffic Manager 프로필을 만들고 3개의 엔드포인트에서 '성능' 트래픽 라우팅 방법을 사용하도록 구성합니다.
1. DNS CNAME 레코드를 사용하여 베니티 도메인 이름 'partners.contoso.com'이 'contoso.trafficmanager.net'을 가리키도록 구성합니다.

![Traffic Manager DNS 구성][1]

> [!NOTE]
> Azure Traffic Manager에서 베니티 도메인을 사용할 때는 베니티 도메인 이름을 Traffic Manager 도메인 이름으로 가리키는 CNAME을 사용해야 합니다. DNS 표준은 도메인의 'apex'(또는 루트)에서 CNAME을 만들 수 없습니다. 따라서 'contoso.com'('naked' 도메인이라고도 함)에 대한 CNAME을 만들 수 없습니다. 'contoso.com'의 도메인(예: 'www.contoso.com')에 대해서만 CNAME을 만들 수 있습니다. 이 제한을 해결하려면 [Azure DNS](../dns/dns-overview.md)에서 DNS 도메인을 호스팅하고 [별칭 레코드](../dns/tutorial-alias-tm.md)를 사용하여 트래픽 관리자 프로필을 가리키는 것이 좋습니다. 또는 간단한 HTTP 리디렉션을 사용하여 'contoso.com'에 대한 직접 요청을 'www.contoso.com'.과 같은 대체 이름으로 지정할 수 있습니다.

### <a name="how-clients-connect-using-traffic-manager"></a>Traffic Manager를 사용하여 클라이언트를 연결하는 방법

이전 예제를 계속하면 클라이언트가 `https://partners.contoso.com/login.aspx` 페이지를 요청하는 경우 클라이언트는 다음 단계를 수행하여 DNS 이름을 확인하고 연결을 설정합니다.

![Traffic Manager를 사용하여 연결 설정][2]

1. 클라이언트는 DNS 쿼리를 구성된 재귀 DNS 서비스로 전송하여 'partners.contoso.com'이라는 이름을 확인합니다. '로컬 DNS' 서비스라고도 하는 재귀 DNS 서비스는 DNS 도메인을 직접 호스트하지 않습니다. 대신 클라이언트에서는 DNS 이름을 확인하는 데 필요한 인터넷에 권한 있는 다양한 DNS 서비스를 연결하는 작업을 오프로드합니다.
2. DNS 이름을 확인하기 위해 재귀 DNS 서비스는 'contoso.com' 도메인에 대한 이름 서버를 찾습니다. 그런 다음 이러한 이름 서버에 연결하여 'partners.contoso.com' DNS 레코드를 요청합니다. contoso.com DNS 서버는 contoso.trafficmanager.net을 가리키는 CNAME 레코드를 반환합니다.
3. 다음으로 재귀 DNS 서비스는 Azure Traffic Manager 서비스에서 제공하는 'trafficmanager.net' 도메인에 대한 이름 서버를 찾습니다. 그런 다음 해당 DNS 서버에 'contoso.trafficmanager.net' DNS 레코드에 대한 요청을 보냅니다.
4. Traffic Manager 이름 서버가 요청을 받습니다. 다음 항목에 따라 엔드포인트를 선택합니다.

    - 각 엔드포인트의 구성된 상태(사용하지 않는 엔드포인트는 반환되지 않음)
    - Traffic Manager 상태 검사에서 확인된 각 엔드포인트의 현재 상태. 자세한 내용은 [Traffic Manager 엔드포인트 모니터링](traffic-manager-monitoring.md)을 참조하세요.
    - 선택된 트래픽 라우팅 메서드. 자세한 내용은 [Traffic Manager 라우팅 메서드](traffic-manager-routing-methods.md)를 참조하세요.

5. 선택한 엔드포인트는 다른 DNS CNAME 레코드로 반환됩니다. 이 경우 contoso-eu.cloudapp.net을 반환한다고 가정하겠습니다.
6. 다음으로 재귀 DNS 서비스가 'cloudapp.net' 도메인에 대한 이름 서버를 찾습니다. 이러한 이름 서버에 연결하여 'contoso-eu.cloudapp.net' DNS 레코드를 요청합니다. EU 기반 서비스 엔드포인트의 IP 주소를 포함하는 DNS 'A' 레코드가 반환됩니다.
7. 재귀 DNS 서비스는 결과를 통합하고 클라이언트에 단일 DNS 응답을 반환합니다.
8. 클라이언트는 DNS 결과를 받고 지정 IP 주소에 연결합니다. 클라이언트는 Traffic Manager를 통해서가 아니라 직접 애플리케이션 서비스 엔드포인트에 연결합니다. 해당 엔드포인트는 HTTPS 엔드포인트이므로 클라이언트는 필요한 SSL/TLS 핸드셰이크를 수행한 다음 ‘/login.aspx’ 페이지에 대해 HTTP GET 요청을 합니다.

재귀 DNS 서비스는 받는 DNS 응답을 캐시합니다. 클라이언트 디바이스의 DNS 확인자도 결과를 캐시합니다. 캐싱을 통해 후속 DNS 쿼리는 다른 이름 서버를 쿼리하는 대신 캐시의 데이터를 사용하여 더 신속하게 답변을 받을 수 있습니다. 캐시의 기간은 각 DNS 레코드의 'TTL(time-to-live)' 속성에 의해 결정됩니다. 짧은 값은 캐시가 빨리 만료되므로 Traffic Manager 이름 서버에 여러 차례의 왕복이 발생합니다. 긴 값은 실패한 엔드포인트에서 트래픽을 멀리 이동하는 데 더 긴 시간이 걸립니다. Traffic Manager를 사용하면 Traffic Manager DNS 응답에 사용되는 TTL을 0초에서 2,147,483,647초([RFC-1035](https://www.ietf.org/rfc/rfc1035.txt)에 따른 최대 범위) 사이로 구성할 수 있으므로 애플리케이션의 요구에 가장 맞는 값을 선택할 수 있습니다.

## <a name="faqs"></a>FAQ

* [Traffic Manager가 사용하는 IP 주소는 어떻게 되나요?](./traffic-manager-faqs.md#what-ip-address-does-traffic-manager-use)

* [Traffic Manager를 사용하여 라우팅할 수 있는 트래픽 유형은 무엇입니까?](./traffic-manager-faqs.md#what-types-of-traffic-can-be-routed-using-traffic-manager)

* [Traffic Manager는 "고정" 세션을 지원하나요?](./traffic-manager-faqs.md#does-traffic-manager-support-sticky-sessions)

* [Traffic Manager를 사용할 때 HTTP 오류가 나타나는 이유는 무엇인가요?](./traffic-manager-faqs.md#why-am-i-seeing-an-http-error-when-using-traffic-manager)

* [Traffic Manager를 사용할 때 성능 영향은 무엇인가요?](./traffic-manager-faqs.md#what-is-the-performance-impact-of-using-traffic-manager)

* [Traffic Manager에는 어떤 애플리케이션 프로토콜을 사용할 수 있나요?](./traffic-manager-faqs.md#what-application-protocols-can-i-use-with-traffic-manager)

* ["naked" 도메인 이름으로 Traffic Manager를 사용할 수 있나요?](./traffic-manager-faqs.md#can-i-use-traffic-manager-with-a-naked-domain-name)

* [DNS 쿼리를 처리할 때 Traffic Manager는 클라이언트 서브넷 주소를 고려하나요?](./traffic-manager-faqs.md#does-traffic-manager-consider-the-client-subnet-address-when-handling-dns-queries)

* [DNS TTL이란 무엇이며 사용자에게 어떤 영향을 주나요?](./traffic-manager-faqs.md#what-is-dns-ttl-and-how-does-it-impact-my-users)

* [Traffic Manager 응답에 대해 설정할 수 있는 TTL 상한 또는 하한은 어느 정도인가요?](./traffic-manager-faqs.md#how-high-or-low-can-i-set-the-ttl-for-traffic-manager-responses)

* [내 프로필로 들어오는 쿼리 볼륨을 파악하려면 어떻게 해야 할까요?](./traffic-manager-faqs.md#how-can-i-understand-the-volume-of-queries-coming-to-my-profile)

## <a name="next-steps"></a>다음 단계

Traffic Manager [엔드포인트 모니터링 및 자동 장애 조치(failover)](traffic-manager-monitoring.md)에 대해 자세히 알아봅니다.

Traffic Manager [트래픽 라우팅 방법](traffic-manager-routing-methods.md)에 대해 자세히 알아봅니다.

<!--Image references-->
[1]: ./media/traffic-manager-how-traffic-manager-works/dns-configuration.png
[2]: ./media/traffic-manager-how-traffic-manager-works/flow.png
