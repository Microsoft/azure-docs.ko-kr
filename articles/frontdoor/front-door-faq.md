---
title: Azure 프런트 도어 서비스-첫 번째 관문에 대 한 질문과 대답 | Microsoft Docs
description: 이 페이지에서는 Azure 프런트 도어 서비스에 대 한 질문과 대답
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/08/2019
ms.author: sharadag
ms.openlocfilehash: b033f463722ddb3a0b7beabdf659900e7d7188df
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/22/2019
ms.locfileid: "67330876"
---
# <a name="frequently-asked-questions-for-azure-front-door-service"></a>Azure 프런트 도어 서비스에 대 한 질문과 대답

이 문서에서는 Azure 프런트 도어 서비스 기능에 대 한 일반적인 질문에 답변 합니다. 질문에 대한 답을 찾지 못한 경우 다음 채널을 통해 (제시된 채널 순서에 따라) 문의할 수 있습니다.

1. 이 문서의 의견 섹션입니다.
2. [Azure 프런트 도어 서비스 UserVoice](https://feedback.azure.com/forums/217313-networking?category_id=345025)합니다.
3. **Microsoft 지원:** 새 지원 요청을 만들려면 Azure Portal의 **도움말** 탭에서 **도움말 + 지원** 단추를 선택한 다음 **새 지원 요청**을 선택합니다.

## <a name="general"></a>일반

### <a name="what-is-azure-front-door-service"></a>Azure Front Door Service란?

Azure 프런트 도어 서비스가 응용 프로그램 배달 네트워크 (ADN) 서비스로 응용 프로그램에 대 한 다양 한 계층 7 부하 분산 기능을 제공 합니다. 전역 부하 분산을 거의 실시간으로 장애 조치 함께 동적 사이트 가속 DSA ()를 제공 합니다. Azure에서 완전히 관리 되는 고가용성 및 확장성이 뛰어난 서비스입니다.

### <a name="what-features-does-azure-front-door-service-support"></a>Azure 프런트 도어 서비스 기능을 지원 하나요?

Azure 프런트 도어 서비스는 동적 사이트 가속 (DSA), SSL 오프 로딩 및 종단 간 SSL, 웹 응용 프로그램 방화벽, 쿠키 기반 세션 선호도, url 경로 기반 라우팅, 사용 가능한 인증서 및 여러 도메인 관리 및 다른 사용자를 지원합니다. 지원 되는 기능의 전체 목록을 참조 하세요 [Azure 프런트 도어 서비스 개요](front-door-overview.md)합니다.

### <a name="what-is-the-difference-between-azure-front-door-service-and-azure-application-gateway"></a>Azure 프런트 도어 서비스와 Azure Application Gateway의 차이 무엇입니까?

첫 번째 관문와 Application Gateway는 7 (HTTP/HTTPS) 부하 분산 장치 계층는, 하는 동안 주요 차이점은 Application Gateway는 지역 서비스 반면 프런트 도어는 전역 서비스입니다. 첫 번째 관문 지역의 다른 배율 단위/클러스터/스탬프 단위를 사용 하 여 균형을 로드 하는 동안 응용 프로그램 게이트웨이 사용 하면 배율 단위 내에 있는 프로그램 v m/컨테이너 등 간에 부하를 분산 수 있습니다.

### <a name="when-should-we-deploy-an-application-gateway-behind-front-door"></a>응용 프로그램 게이트웨이 프런트 도어 뒤를 배포 해야 하는 경우는 우리가?

왜 첫 번째 관문 뒤에 있는 응용 프로그램 게이트웨이 사용 해야 하나는 주요 시나리오를 다음과 같습니다.

- 첫 번째 관문 경로 기반 부하 분산 하지만 한 트래픽 부하를 분산할 더욱 해당 가상 네트워크 (VNET)에 응용 프로그램 게이트웨이 사용 해야 하는 다음 하려는 전역 수준 에서만 수행할 수 있습니다.
- 첫 번째 관문 VM/컨테이너 수준에서 작동 하지 때문 따라서 불가능 연결 드레이닝 합니다. 그러나 Application Gateway 연결 드레이닝 할 수 있습니다. 
- AFD 뒤 Application Gateway를 사용 하 여 SSL 오프 로드 하 고 해당 가상 네트워크 (VNET)에 HTTP 요청을 라우팅하는 100% 얻을 수 있습니다.
- 첫 번째 관문 및 Application Gateway 세션 선호도 지원 합니다. 첫 번째 관문 동일한 클러스터 또는 지정된 된 지역에서 백 엔드 사용자 세션에서 후속 트래픽을 보낼 수 있습니다, 있지만 응용 프로그램 게이트웨이 클러스터 내에서 동일한 서버 트래픽은 선호도 설정 직접 수 합니다.  

### <a name="can-we-deploy-azure-load-balancer-behind-front-door"></a>Azure Load Balancer 프런트 도어 뒤를 배포할 수 있습니까?

Azure 프런트 도어 서비스는 공용 VIP 또는 공개적으로 사용할 수 있는 DNS 이름에 트래픽을 라우팅할 수 있어야 합니다. 일반적인 사용 사례는 Azure Load Balancer 프런트 도어 뒤 배포입니다.

### <a name="what-protocols-does-azure-front-door-service-support"></a>Azure 프런트 도어 서비스 프로토콜을 지원 하나요?

Azure 프런트 도어 서비스는 HTTP, HTTPS 및 HTTP/2를 지원합니다.

### <a name="how-does-azure-front-door-service-support-http2"></a>Azure 프런트 도어 서비스 HTTP/2를 어떻게 지원 하나요?

Http/2 프로토콜 지원은 Azure 프런트 도어 서비스에 연결 하는 클라이언트에 제공 됩니다. 백 엔드 풀에서 백 엔드에 대 한 통신은 HTTP/1.1 끝났습니다. Http/2 지원이 기본적으로 사용 됩니다.

### <a name="what-resources-are-supported-today-as-part-of-backend-pool"></a>현재 백 엔드 풀의 일부로 어떤 리소스가 지원되나요?

백 엔드 풀의 저장소, 웹 앱, Kubernetes 인스턴스 또는 다른 사용자 지정 호스트 이름을 공용 연결을 구성할 수 있습니다. Azure 프런트 도어 서비스 공용 IP 또는 공개적으로 확인 가능한 DNS 호스트 이름을 통해 백 엔드에 정의 되어 있는지 필요 합니다. 백 엔드 풀의 구성원으로는 공용 연결 영역, 영역 간 또는 Azure 외부 에서도 수 있습니다.

### <a name="what-regions-is-the-service-available-in"></a>어떤 지역에서 서비스를 사용할 수 있습니까?

Azure 프런트 도어 서비스는 글로벌 서비스 및 특정 Azure 지역에 연결 되지 않은 합니다. 프런트 도어를 만드는 동안 지정 해야 할 유일한 위치는 리소스 그룹에 대 한 메타 데이터를 저장할 기본적으로 지정 하는 리소스 그룹 위치를 합니다. 첫 번째 관문 리소스 자체 전역 리소스로 만들어지고 구성을 모든 Pop (Point of Presence)에 전역적으로 배포 됩니다. 

### <a name="what-are-the-pop-locations-for-azure-front-door-service"></a>Azure 프런트 도어 서비스에 대 한 POP 위치?

Azure 프런트 도어 서비스에는 Microsoft의 Azure CDN POP (Point of Presence) 위치 같은 목록을 있습니다. Pop의 전체 목록은 참조 수행할 [Microsoft Azure CDN POP 위치](https://docs.microsoft.com/azure/cdn/cdn-pop-locations)합니다.

### <a name="is-azure-front-door-service-a-dedicated-deployment-for-my-application-or-is-it-shared-across-customers"></a>고객 간에 공유 되는 Azure 프런트 도어 서비스 응용 프로그램에 대 한 전용된 배포 여부

Azure 프런트 도어 서비스는 전역적으로 분산 된 다중 테 넌 트 서비스입니다. 따라서 프런트 도어에 대 한 인프라는 모든 고객 간에 공유 됩니다. 그러나 첫 번째 관문 프로필을 만들어 응용 프로그램에 필요한 특정 구성을 정의 하 고 영향을 기타 프런트 도어 구성에 첫 번째 관문 변경 되지 않습니다.

### <a name="is-http-https-redirection-supported"></a>HTTP->HTTPS 리디렉션이 지원되나요?

예. 사실, Azure 프런트 도어 서비스 호스트는 지원, 경로 및 쿼리 문자열 URL 리디렉션의 뿐만 아니라 리디렉션. 에 대해 자세히 알아보세요 [URL 리디렉션](front-door-url-redirect.md)합니다. 

### <a name="in-what-order-are-routing-rules-processed"></a>어떤 순서로 처리 규칙을 라우팅하는?

첫 번째 관문 프로그램에 대 한 경로 정렬 되지 않습니다 하 고 최상의 일치를 기반으로 특정 경로가 선택 됩니다. 에 대해 자세히 알아보세요 [어떻게 프런트 도어 라우팅 규칙에는 요청과 일치](front-door-route-matching.md)합니다.

### <a name="how-do-i-lock-down-the-access-to-my-backend-to-only-azure-front-door-service"></a>잠그는 방법 대 한 액세스는 Azure 프런트 도어 Service에 내 백 엔드를?

만 Azure 프런트 도어 서비스에서 트래픽을 허용 하 여 백 엔드에 대 한 IP ACLing를 구성할 수 있습니다. Azure 프런트 도어 서비스의 백 엔드 IP 공간 에서만에서 들어오는 연결을 허용 하도록 응용 프로그램을 제한할 수 있습니다. 통합 노력 [Azure IP 범위 및 서비스 태그](https://www.microsoft.com/download/details.aspx?id=56519) 하지만 이제 아래와 같이 IP 범위를 참조할 수 있습니다.
 
- **IPv4** - `147.243.0.0/16`
- **IPv6** - `2a01:111:2050::/44`

> [!WARNING]
> 이 백 엔드 IP 공간 나중에 변경할 수 있습니다, 있지만 되도록 하는 것을 통합 했습니다 사용 하 여 이런 전에 [Azure IP 범위 및 서비스 태그](https://www.microsoft.com/download/details.aspx?id=56519)합니다. 구독 하는 것이 좋습니다 [Azure IP 범위 및 서비스 태그](https://www.microsoft.com/download/details.aspx?id=56519) 모든 변경 내용이 나 업데이트 합니다. 

### <a name="can-the-anycast-ip-change-over-the-lifetime-of-my-front-door"></a>애니캐스트 IP 내 첫 번째 관문의 수명 동안 변경할 수 있습니까?

첫 번째 관문 프로그램에 대 한 프런트 엔드 애니캐스트 IP는 일반적으로 변경 하지 않으며 첫 번째 관문의 수명 동안 정적 남아 있을 수 있습니다. 그러나 **보장 되지 않습니다** 동일한 합니다. 수행할 IP에 직접적인 종속성을 사용 하지 않습니다.  

### <a name="does-azure-front-door-service-support-static-or-dedicated-ips"></a>Azure 프런트 도어 서비스 정적 또는 전용 Ip를 지원 하나요?

아니요, Azure 프런트 도어 서비스 현재 지원 하지 않습니다 정적 또는 전용 프런트 엔드 애니캐스트 Ip입니다. 

### <a name="does-azure-front-door-service-support-x-forwarded-for-headers"></a>Azure 프런트 도어 서비스 x-전달 기능에 대 한 헤더를 지원 하나요?

예, Azure 프런트 도어 서비스 X-전달 기능에 대 한, X-전달-호스트 및 X 전달 프로토콜 헤더를 지원합니다. 에 대 한 X-전달 기능에 대 한 헤더를 이미 있는 다음 첫 번째 관문을 클라이언트 소켓 IP를 추가 합니다. 다른 값으로 클라이언트 소켓 IP 사용 하 여 헤더를 추가합니다. X 전달 호스트와 X 전달 프로토콜에 대 한 값이 재정의 됩니다.

에 대해 자세히 알아보세요 합니다 [지원 되는 HTTP 헤더 프런트 도어](front-door-http-headers-protocol.md)합니다.  

### <a name="how-long-does-it-take-to-deploy-an-azure-front-door-service-does-my-front-door-still-work-when-being-updated"></a>이 얼마 Azure 프런트 도어 서비스를 배포 하 시겠습니까? 내 첫 번째 관문 여전히 작동 업데이트할 때?

새 프런트 도어 만들거나 기존 프런트 도어는에 대 한 업데이트에는 전역 배포 3 ~ 5 분 정도 걸립니다. 즉, 약 3 ~ 5 분 안에 첫 번째 관문 구성에는 Pop의 전체에 전역적으로 배포 합니다.

참고-사용자 지정 SSL 인증서 업데이트 전역적으로 배포 되도록 30 분 정도 걸립니다.

## <a name="configuration"></a>구성

### <a name="can-azure-front-door-load-balance-or-route-traffic-within-a-virtual-network"></a>수 Azure 프런트 도어 부하 분산 또는 경로 트래픽 가상 네트워크 내에서?

Azure 프런트 도어 (AFD) 공용 IP 또는 공개적으로 확인 가능한 DNS 이름 트래픽 라우팅하는 데 필요합니다. 따라서 대답은 직접 없습니다 AFD 가상 네트워크 내에서 라우팅할 수 없습니다 있지만 사이 Application Gateway 또는 Azure Load Balancer를 사용 하 여이 시나리오를 구현 합니다.

### <a name="what-are-the-various-timeouts-and-limits-for-azure-front-door-service"></a>다양 한 시간 제한 및 Azure 프런트 도어 서비스에 대 한 제한은 무엇 인가요?

모든에 대해 알아봅니다를 문서화 [시간 제한 및 Azure 프런트 도어 서비스에 대 한 제한](https://docs.microsoft.com/azure/azure-subscription-service-limits#azure-front-door-service-limits)합니다.

## <a name="performance"></a>성능

### <a name="how-does-azure-front-door-service-support-high-availability-and-scalability"></a>Azure 프런트 도어 서비스 고가용성 및 확장성에 주는 지?

Azure 프런트 도어 서비스는 엄청난 양의 응용 프로그램의 확장성 요구를 충족 하는 용량을 사용 하 여 전역적으로 분산 된 다중 테 넌 트 플랫폼입니다. 첫 번째 관문 Microsoft의 글로벌 네트워크의 가장자리에서 배달, 지역 또는 다른 클라우드에 걸쳐 전체 응용 프로그램 또는 개별 마이크로 서비스 장애 조치할 수 있도록 전역 부하 분산 기능을 제공 합니다.

## <a name="ssl-configuration"></a>SSL 구성

### <a name="what-tls-versions-are-supported-by-azure-front-door-service"></a>TLS 버전은 Azure 프런트 도어 서비스에서 지원 되나요?

첫 번째 관문 TLS 버전 1.0, 1.1 및 1.2를 지원합니다. TLS 1.3 아직 지원 되지 않습니다.

### <a name="what-certificates-are-supported-on-azure-front-door-service"></a>어떤 인증서가 Azure 프런트 도어 서비스에서 지원?

안전 하 게 첫 번째 관문 사용자 지정 도메인에서 콘텐츠를 제공 하는 것에 대 한 HTTPS 프로토콜을 사용 하려면 Azure 프런트 도어 서비스에서 관리 되는 인증서를 사용 하거나 사용자 고유의 인증서를 사용 하 여 선택할 수 있습니다.
첫 번째 관문 Digicert 통해 표준 SSL 인증서를 프로 비전 옵션을 관리 및 앞에 도어의 키 자격 증명 모음을 저장 합니다. 사용자 고유의 인증서를 사용 하려는 경우 지원 되는 CA에서 인증서를 등록할 수 있습니다 및 표준 SSL, 확장된 유효성 검사 인증서 또는 와일드 카드 인증서도 될 수 있습니다. 자체 서명 된 인증서는 지원 되지 않습니다. 에 대해 알아봅니다 [사용자 지정 도메인에 대 한 HTTPS를 사용 하는 방법](https://aka.ms/FrontDoorCustomDomainHTTPS)합니다.

### <a name="does-front-door-support-auto-rotation-of-certificates"></a>첫 번째 관문에서 인증서의 자동 회전을 지원 하나요?

사용자 고유의 사용자 지정 SSL 인증서에 대 한 자동 회전 지원 되지 않습니다. 어떻게이 설정 된 지정된 된 사용자 지정 도메인을 위해 처음으로 비슷합니다는 해야 지점 프런트 도어를 올바른 인증서 버전 Key Vault에서 하 고 첫 번째 관문의 서비스 주체 키 자격 증명 모음에 대 한 액세스를 여전히에 있는지 확인 합니다. 첫 번째 관문에서이 업데이트 된 인증서 출시 작업 완전 한 원자성 이며 프로덕션 영향은 주체 이름을 제공 해도 또는 SAN 인증서에 대 한 변경 되지 않습니다.
</br>관리 되는 첫 번째 관문 인증서 옵션에 대 한 인증서가 첫 번째 관문에서 자동으로 회전 합니다.

### <a name="what-are-the-current-cipher-suites-supported-by-azure-front-door-service"></a>Azure 프런트 도어 서비스에서 지원 되는 현재 암호 그룹 이란?

다음은 Azure 프런트 도어 서비스에서 지원 되는 현재 암호 그룹입니다.

- TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA
- TLS_RSA_WITH_AES_256_GCM_SHA384
- TLS_RSA_WITH_AES_128_GCM_SHA256
- TLS_RSA_WITH_AES_256_CBC_SHA256
- TLS_RSA_WITH_AES_128_CBC_SHA256
- TLS_RSA_WITH_AES_256_CBC_SHA
- TLS_RSA_WITH_AES_128_CBC_SHA

### <a name="does-azure-front-door-service-also-support-re-encryption-of-traffic-to-the-backend"></a>백 엔드에 대 한 트래픽의 재 암호화는 Azure 프런트 도어 서비스에서도 지원 하나요?

예, Azure 프런트 도어 서비스는 SSL 오프 로드와 백 엔드에 대 한 트래픽을 다시 암호화 하는 종단 간 SSL을 지원 합니다. 사실, 백 엔드에 대 한 연결을 통해 발생 하므로 것이 공용 IP를 전달 프로토콜로 HTTPS를 사용 하 여 첫 번째 관문을 구성 하는 것이 좋습니다.

### <a name="can-i-configure-ssl-policy-to-control-ssl-protocol-versions"></a>SSL 프로토콜 버전을 제어하는 SSL 정책을 구성할 수 있나요?

아니요, 현재 프런트 도어 특정 TLS 버전을 거부 하도록 지원 하지 않습니다도 최소 TLS 버전을 설정할 수 있습니다. 

### <a name="can-i-configure-front-door-to-only-support-specific-cipher-suites"></a>프런트 도어만 특정 암호화 모음을 지원 하도록 구성할 수 있나요?

아니요, 특정 암호화 모음에 대 한 첫 번째 관문 구성도 지원 되지 않습니다. 

## <a name="diagnostics-and-logging"></a>진단 및 로깅

### <a name="what-types-of-metrics-and-logs-are-available-with-azure-front-door-service"></a>메트릭 및 로그의 유형을 Azure 프런트 도어 서비스를 사용 하 여 사용할 수 있습니까?

로그 및 기타 진단 기능에 대 한 자세한 내용은 [현관에 대 한 메트릭 및 로그 모니터링](front-door-diagnostics.md)합니다.

### <a name="what-is-the-retention-policy-on-the-diagnostics-logs"></a>진단 로그에서 보존 정책은 무엇인가요?

진단 로그는 고객 스토리지 계정으로 전달되고 고객은 기본 설정에 따라 보존 정책을 설정할 수 있습니다. 진단 로그를 이벤트 허브 또는 Azure Monitor 로그에도 전송할 수 있습니다. 자세한 내용은 [Azure 프런트 도어 서비스 진단](front-door-diagnostics.md)합니다.

### <a name="how-do-i-get-audit-logs-for-azure-front-door-service"></a>Azure 프런트 도어 서비스에 대 한 감사 로그 가져오기

감사 로그는 Azure 프런트 도어 서비스에 대해 사용할 수 있습니다. 포털에서 클릭 **활동 로그** 감사 로그에 액세스 하 여 첫 번째 관문의 메뉴 블레이드에 있는 합니다. 

### <a name="can-i-set-alerts-with-azure-front-door-service"></a>Azure 프런트 도어 서비스를 사용 하 여 경고를 설정할 수 있습니까?

예, Azure 프런트 도어 서비스 경고 지원지 않습니다. 경고는 메트릭에 대해 구성됩니다. 

## <a name="next-steps"></a>다음 단계

- [Front Door를 만드는 방법](quickstart-create-front-door.md)을 알아봅니다.
- [Front Door의 작동 원리](front-door-routing-architecture.md)를 알아봅니다.