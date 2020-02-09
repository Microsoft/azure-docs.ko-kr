---
title: Azure 애플리케이션 게이트웨이에 대한 질문과 대답
description: Azure 애플리케이션 Gateway에 대한 자주 묻는 질문과 대답을 찾습니다.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 08/31/2019
ms.author: victorh
ms.openlocfilehash: f2f2e02cdb5698d7569e5be177d54ca4dcb0ae02
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/08/2020
ms.locfileid: "77086545"
---
# <a name="frequently-asked-questions-about-application-gateway"></a>Application Gateway에 대한 질문과 대답

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

다음은 Azure 애플리케이션 Gateway에 대해 묻는 일반적인 질문입니다.

## <a name="general"></a>일반

### <a name="what-is-application-gateway"></a>Application Gateway란?

Azure 애플리케이션 Gateway는 ADC (응용 프로그램 배달 컨트롤러)를 서비스로 제공 합니다. 응용 프로그램에 대한 다양 한 계층 7 부하 분산 기능을 제공 합니다. 이 서비스는 Azure에서 항상 사용 가능 하 고 확장 가능 하며 완전히 관리 됩니다.

### <a name="what-features-does-application-gateway-support"></a>Application Gateway에서 지원하는 기능은 어떤 것이 있나요?

Application Gateway 자동 크기 조정, SSL 오프 로딩 및 종단 간 SSL, WAF (웹 응용 프로그램 방화벽), 쿠키 기반 세션 선호도, URL 경로 기반 라우팅, 멀티 사이트 호스팅 및 기타 기능을 지원 합니다. 지원되는 기능의 전체 목록은 [Application Gateway 소개](application-gateway-introduction.md)를 참조하세요.

### <a name="how-do-application-gateway-and-azure-load-balancer-differ"></a>Application Gateway 및 Azure Load Balancer는 어떻게 다릅니까?

Application Gateway는 계층 7 부하 분산 장치입니다. 즉, 웹 트래픽 (HTTP, HTTPS, WebSocket 및 HTTP/2) 에서만 작동 합니다. SSL 종료, 쿠키 기반 세션 선호도, 부하 분산 트래픽에 대한 라운드 로빈 등의 기능을 지원 합니다. Load Balancer 계층 4 (TCP 또는 UDP)에서 트래픽 부하를 분산 합니다.

### <a name="what-protocols-does-application-gateway-support"></a>Application Gateway에서 지원하는 프로토콜은 무엇인가요?

Application Gateway는 HTTP, HTTPS, HTTP/2 및 WebSocket을 지원합니다.

### <a name="how-does-application-gateway-support-http2"></a>Application Gateway는 어떻게 HTTP/2를 지원하나요?

[HTTP/2 지원](https://docs.microsoft.com/azure/application-gateway/configuration-overview#http2-support)을 참조 하세요.

### <a name="what-resources-are-supported-as-part-of-a-backend-pool"></a>백 엔드 풀의 일부로 지원 되는 리소스는 무엇 인가요?

[지원 되는 백 엔드 리소스](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#backend-pools)를 참조 하세요.

### <a name="in-what-regions-is-application-gateway-available"></a>Application Gateway 사용할 수 있는 지역은 어디 인가요?

Application Gateway는 Azure 전체의 모든 지역에서 사용할 수 있습니다. [Azure 중국 21vianet](https://www.azure.cn/) 및 [Azure Government](https://azure.microsoft.com/overview/clouds/government/)에서도 사용할 수 있습니다.

### <a name="is-this-deployment-dedicated-for-my-subscription-or-is-it-shared-across-customers"></a>이 배포는 내 구독 전용 이거나 고객 간에 공유 되나요?

Application Gateway는 가상 네트워크에서 전용 배포입니다.

### <a name="does-application-gateway-support-http-to-https-redirection"></a>HTTP-HTTPS 리디렉션을 지원 Application Gateway?

리디렉션은 지원됩니다. [Application Gateway 리디렉션 개요](application-gateway-redirect-overview.md)를 참조 하세요.

### <a name="in-what-order-are-listeners-processed"></a>수신기는 어떤 순서로 처리되나요?

[수신기 처리 순서](https://docs.microsoft.com/azure/application-gateway/configuration-overview#order-of-processing-listeners)를 확인 합니다.

### <a name="where-do-i-find-the-application-gateway-ip-and-dns"></a>Application Gateway IP 및 DNS는 어디에서 찾을 까 요?

공용 IP 주소를 엔드포인트으로 사용 하는 경우 공용 IP 주소 리소스에 대한 IP 및 DNS 정보를 찾을 수 있습니다. 또는 포털의 응용 프로그램 게이트웨이에 대한 개요 페이지에서 찾을 수 있습니다. 내부 IP 주소를 사용 하는 경우 개요 페이지에서 정보를 확인 하세요.

### <a name="what-are-the-settings-for-keep-alive-timeout-and-tcp-idle-timeout"></a>Keep-alive 시간 제한 및 TCP 유휴 시간 제한에 대한 설정은 무엇 인가요?

*Keep-alive 제한 시간은* 클라이언트가 영구적 연결을 다시 사용 하거나 닫기 전에 다른 HTTP 요청을 전송 하기 위해 대기 Application Gateway 하는 시간을 제어 합니다. *Tcp 유휴 시간 제한은* 작업이 없는 경우 tcp 연결이 열린 상태로 유지 되는 기간을 제어 합니다. 

Application Gateway v1 SKU의 연결 *유지 제한 시간은* 120 초이 고, v2 sku에서는 75 초입니다. Application Gateway의 v1 및 v2 SKU 모두의 프런트 엔드 VIP (가상 IP)에 대한 *TCP 유휴 시간 제한은* 4 분의 기본값입니다. 

### <a name="does-the-ip-or-dns-name-change-over-the-lifetime-of-the-application-gateway"></a>응용 프로그램 게이트웨이의 수명 동안 IP 또는 DNS 이름이 변경 되나요?

Application Gateway V1 SKU에서는 응용 프로그램 게이트웨이를 중지 하 고 시작 하는 경우 VIP가 변경 될 수 있습니다. 하지만 application gateway와 연결 된 DNS 이름은 게이트웨이의 수명 동안 변경 되지 않습니다. DNS 이름이 변경 되지 않으므로 CNAME 별칭을 사용 하 여 응용 프로그램 게이트웨이의 DNS 주소를 가리키도록 해야 합니다. Application Gateway V2 SKU에서 IP 주소를 정적으로 설정할 수 있으므로 응용 프로그램 게이트웨이의 수명 동안 IP 및 DNS 이름이 변경 되지 않습니다. 

### <a name="does-application-gateway-support-static-ip"></a>Application Gateway에서 고정 IP를 지원하나요?

예, Application Gateway v2 SKU는 고정 공용 IP 주소를 지원 합니다. v1 SKU는 고정 내부 IP를 지원합니다.

### <a name="does-application-gateway-support-multiple-public-ips-on-the-gateway"></a>Application Gateway가 게이트웨이에서 여러 공용 IP를 지원하나요?

응용 프로그램 게이트웨이는 공용 IP 주소를 하나만 지원 합니다.

### <a name="how-large-should-i-make-my-subnet-for-application-gateway"></a>Application Gateway에 대해 얼마나 큰 서브넷을 만들어야 하나요?

[Application Gateway 서브넷 크기 고려 사항](https://docs.microsoft.com/azure/application-gateway/configuration-overview#size-of-the-subnet)을 참조 하세요.

### <a name="can-i-deploy-more-than-one-application-gateway-resource-to-a-single-subnet"></a>둘 이상의 Application Gateway 리소스를 단일 서브넷에 배포할 수 있나요?

예. 지정 된 Application Gateway 배포의 여러 인스턴스 외에 다른 Application Gateway 리소스를 포함 하는 기존 서브넷에 다른 고유한 Application Gateway 리소스를 프로 비전 할 수 있습니다.

단일 서브넷은 Standard_v2와 표준 Application Gateway를 함께 지원할 수 없습니다.

### <a name="does-application-gateway-support-x-forwarded-for-headers"></a>Application Gateway에서 x-forwarded-for 헤더를 지원하나요?

예. [요청에 대한 수정을](https://docs.microsoft.com/azure/application-gateway/how-application-gateway-works#modifications-to-the-request)참조 하세요.

### <a name="how-long-does-it-take-to-deploy-an-application-gateway-will-my-application-gateway-work-while-its-being-updated"></a>응용 프로그램 게이트웨이를 배포 하는 데 걸리는 시간 응용 프로그램 게이트웨이가 업데이트 되는 동안 작동 하나요?

새 Application Gateway v1 SKU 배포 시 프로비전하는 데 최대 20분이 걸릴 수 있습니다. 인스턴스 크기 또는 수에 대한 변경 내용은 중단 되지 않으며이 시간 동안 게이트웨이가 활성 상태로 유지 됩니다.

V2 SKU를 사용 하는 대부분의 배포는 프로 비전 하는 데 약 6 분이 소요 됩니다. 그러나 배포 유형에 따라 시간이 더 오래 걸릴 수 있습니다. 예를 들어 여러 개의 인스턴스가 있는 여러 가용성 영역에 배포 하는 경우 6 분 넘게 걸릴 수 있습니다. 

### <a name="can-i-use-exchange-server-as-a-backend-with-application-gateway"></a>Application Gateway를 사용 하 여 백 엔드로 Exchange Server를 사용할 수 있나요?

아니요. Application Gateway는 SMTP, IMAP, POP3 등의 메일 프로토콜을 지원 하지 않습니다. 

## <a name="performance"></a>성능

### <a name="how-does-application-gateway-support-high-availability-and-scalability"></a>Application Gateway는 고가용성과 확장성을 어떤 방식으로 지원하나요?

Application Gateway v1 SKU는 둘 이상의 인스턴스를 배포한 경우 고가용성 시나리오를 지원 합니다. Azure는 이러한 인스턴스를 업데이트 및 장애 도메인 간에 배포 하 여 모든 인스턴스가 동시에 실패 하지 않도록 합니다. v1 SKU는 로드를 공유하기 위해 동일한 게이트웨이의 여러 인스턴스를 추가하여 확장성을 지원합니다.

v2 SKU의 경우에는 여러 장애 도메인과 업데이트 도메인에 새 인스턴스가 자동으로 분산됩니다. 영역 중복성을 선택 하는 경우 최신 인스턴스는 영역 장애 복구를 제공 하기 위해 가용성 영역에도 분산 됩니다.

### <a name="how-do-i-achieve-a-dr-scenario-across-datacenters-by-using-application-gateway"></a>Application Gateway를 사용 하 여 데이터 센터 간에 DR 시나리오를 달성할 어떻게 할까요? 있나요?

Traffic Manager를 사용 하 여 여러 데이터 센터의 여러 응용 프로그램 게이트웨이에서 트래픽을 분산 시킬 수 있습니다.

### <a name="does-application-gateway-support-autoscaling"></a>자동 크기 조정을 지원할 Application Gateway 있나요?

예. Application Gateway v2 SKU는 자동 크기 조정을 지원합니다. 자세한 내용은 자동 크기 조정 [및 영역 중복 Application Gateway](application-gateway-autoscaling-zone-redundant.md)를 참조 하세요.

### <a name="does-manual-or-automatic-scale-up-or-scale-down-cause-downtime"></a>수동 또는 자동 확장 또는 규모 축소를 수행 하면 가동 중지 시간이 발생 하나요?

아니요. 인스턴스가 업그레이드 도메인 및 장애 도메인 간에 배포됩니다.

### <a name="does-application-gateway-support-connection-draining"></a>Application Gateway는 연결 드레이닝을 지원하나요?

예. 연결 드레이닝을 설정 하 여 중단 없이 백 엔드 풀 내에서 구성원을 변경할 수 있습니다. 자세한 내용은 [Application Gateway의 연결 드레이닝 섹션](overview.md#connection-draining)을 참조 하십시오.

### <a name="can-i-change-instance-size-from-medium-to-large-without-disruption"></a>중단 없이 인스턴스 크기를 중간에서 큼으로 변경할 수 있나요?

예.

## <a name="configuration"></a>구성

### <a name="is-application-gateway-always-deployed-in-a-virtual-network"></a>Application Gateway가 가상 네트워크에서 항상 배포되나요?

예. Application Gateway는 항상 가상 네트워크 서브넷에 배포 됩니다. 이 서브넷은 응용 프로그램 게이트웨이만 포함할 수 있습니다. 자세한 내용은 [가상 네트워크 및 서브넷 요구 사항](https://docs.microsoft.com/azure/application-gateway/configuration-overview#azure-virtual-network-and-dedicated-subnet)을 참조 하세요.

### <a name="can-application-gateway-communicate-with-instances-outside-of-its-virtual-network-or-outside-of-its-subscription"></a>가상 네트워크 외부 또는 해당 구독 외부의 인스턴스와 통신할 Application Gateway 있습니다.

IP 연결이 있는 한 Application Gateway는 가상 네트워크 외부의 인스턴스와 통신할 수 있습니다. Application Gateway는 구독 외부의 인스턴스와도 통신할 수 있습니다. 내부 Ip를 백 엔드 풀 구성원으로 사용 하려면 [가상 네트워크 피어 링](../virtual-network/virtual-network-peering-overview.md) 또는 [Azure VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md)를 사용 합니다.

### <a name="can-i-deploy-anything-else-in-the-application-gateway-subnet"></a>Application Gateway 서브넷에서 다른 항목을 배포할 수 있나요?

아니요. 그러나 서브넷에 다른 응용 프로그램 게이트웨이를 배포할 수 있습니다.

### <a name="are-network-security-groups-supported-on-the-application-gateway-subnet"></a>Application gateway 서브넷에서 네트워크 보안 그룹이 지원 되나요?

[Application Gateway 서브넷의 네트워크 보안 그룹](https://docs.microsoft.com/azure/application-gateway/configuration-overview#network-security-groups-on-the-application-gateway-subnet)을 참조 하세요.

### <a name="does-the-application-gateway-subnet-support-user-defined-routes"></a>Application gateway 서브넷은 사용자 정의 경로를 지원 하나요?

[Application Gateway 서브넷에서 지원 되는 사용자 정의 경로](https://docs.microsoft.com/azure/application-gateway/configuration-overview#user-defined-routes-supported-on-the-application-gateway-subnet)를 참조 하세요.

### <a name="what-are-the-limits-on-application-gateway-can-i-increase-these-limits"></a>Application Gateway에서 한도는 어떻게 되나요? 이러한 한도를 늘릴 수 있나요?

[Application Gateway 제한](../azure-resource-manager/management/azure-subscription-service-limits.md#application-gateway-limits)을 참조 하세요.

### <a name="can-i-simultaneously-use-application-gateway-for-both-external-and-internal-traffic"></a>외부 및 내부 트래픽에 대해 동시에 Application Gateway를 사용할 수 있나요?

예. Application Gateway는 하나의 내부 IP와 응용 프로그램 게이트웨이 당 하나의 외부 IP를 지원 합니다.

### <a name="does-application-gateway-support-virtual-network-peering"></a>가상 네트워크 피어 링을 지원 Application Gateway?

예. 가상 네트워크 피어 링은 다른 가상 네트워크의 트래픽 부하를 분산 하는 데 도움이 됩니다.

### <a name="can-i-talk-to-on-premises-servers-when-theyre-connected-by-expressroute-or-vpn-tunnels"></a>Express 경로 또는 VPN 터널을 통해 연결 된 경우 온-프레미스 서버와 통신할 수 있나요?

예, 트래픽이 허용되기만 한다면 가능합니다.

### <a name="can-one-backend-pool-serve-many-applications-on-different-ports"></a>하나의 백 엔드 풀에서 여러 응용 프로그램을 서로 다른 포트에 제공할 수 있나요?

마이크로 서비스 아키텍처가 지원 됩니다. 다른 포트를 검색 하려면 여러 HTTP 설정을 구성 해야 합니다.

### <a name="do-custom-probes-support-wildcards-or-regex-on-response-data"></a>사용자 지정 프로브는 응답 데이터에서 와일드 카드나 정규식을 지원 하나요?

아니요. 

### <a name="how-are-routing-rules-processed-in-application-gateway"></a>Application Gateway에서 라우팅 규칙은 어떻게 처리 되나요?

[처리 규칙의 순서를](https://docs.microsoft.com/azure/application-gateway/configuration-overview#order-of-processing-rules)확인 합니다.

### <a name="for-custom-probes-what-does-the-host-field-signify"></a>사용자 지정 프로브의 경우 호스트 필드는 무엇을 의미 하나요?

호스트 필드는 Application Gateway에서 멀티 사이트를 구성한 경우 프로브를 보낼 이름을 지정 합니다. 그렇지 않으면 ' 127.0.0.1 '을 사용 합니다. 이 값은 가상 컴퓨터 호스트 이름과 다릅니다. 해당 형식은 \<프로토콜\>//\<호스트\>:\<포트\>\<경로\>입니다.

### <a name="can-i-allow-application-gateway-access-to-only-a-few-source-ip-addresses"></a>일부 원본 IP 주소에만 Application Gateway 액세스를 허용할 수 있나요?

예. [특정 원본 ip에 대한 액세스 제한을](https://docs.microsoft.com/azure/application-gateway/configuration-overview#allow-application-gateway-access-to-a-few-source-ips)참조 하세요.

### <a name="can-i-use-the-same-port-for-both-public-facing-and-private-facing-listeners"></a>공용 및 개인 연결 수신기 모두에 동일한 포트를 사용할 수 있나요?

아니요.

### <a name="is-there-guidance-available-to-migrate-from-the-v1-sku-to-the-v2-sku"></a>V1 SKU에서 v2 SKU로 마이그레이션하는 데 사용할 수 있는 지침이 있나요?

예. 자세한 내용은 v 1 [에서 v 2로 Azure 애플리케이션 게이트웨이 및 웹 응용 프로그램 방화벽 마이그레이션](migrate-v1-v2.md)을 참조 하세요.

### <a name="does-application-gateway-support-ipv6"></a>I p v 6을 지원 Application Gateway?

Application Gateway v2는 현재 i p v 6을 지원 하지 않습니다. IPv4만 사용 하는 이중 스택 VNet에서 작동할 수 있지만 게이트웨이 서브넷은 IPv4 전용 이어야 합니다. Application Gateway v1은 이중 스택 Vnet을 지원 하지 않습니다. 

## <a name="configuration---ssl"></a>구성-SSL

### <a name="what-certificates-does-application-gateway-support"></a>Application Gateway 지원 되는 인증서는 무엇입니까?

Application Gateway는 자체 서명 된 인증서, CA (인증 기관) 인증서, EV (확장 유효성 검사) 인증서 및 와일드 카드 인증서를 지원 합니다.

### <a name="what-cipher-suites-does-application-gateway-support"></a>지원 Application Gateway 암호 그룹은 무엇 인가요?

Application Gateway는 다음과 같은 암호 그룹을 지원 합니다. 

- TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA
- TLS_DHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_DHE_RSA_WITH_AES_256_CBC_SHA
- TLS_DHE_RSA_WITH_AES_128_CBC_SHA
- TLS_RSA_WITH_AES_256_GCM_SHA384
- TLS_RSA_WITH_AES_128_GCM_SHA256
- TLS_RSA_WITH_AES_256_CBC_SHA256
- TLS_RSA_WITH_AES_128_CBC_SHA256
- TLS_RSA_WITH_AES_256_CBC_SHA
- TLS_RSA_WITH_AES_128_CBC_SHA
- TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA
- TLS_DHE_DSS_WITH_AES_256_CBC_SHA256
- TLS_DHE_DSS_WITH_AES_128_CBC_SHA256
- TLS_DHE_DSS_WITH_AES_256_CBC_SHA
- TLS_DHE_DSS_WITH_AES_128_CBC_SHA
- TLS_RSA_WITH_3DES_EDE_CBC_SHA
- TLS_DHE_DSS_WITH_3DES_EDE_CBC_SHA

SSL 옵션을 사용자 지정 하는 방법에 대한 자세한 내용은 [Application Gateway에서 ssl 정책 버전 및 암호 그룹 구성](application-gateway-configure-ssl-policy-powershell.md)을 참조 하세요.

### <a name="does-application-gateway-support-reencryption-of-traffic-to-the-backend"></a>백 엔드에 대한 트래픽 재 암호화 지원할 Application Gateway 있나요?

예. Application Gateway는 SSL 오프 로드와 백 엔드에 대한 트래픽을 전송 하는 종단 간 SSL을 지원 합니다.

### <a name="can-i-configure-ssl-policy-to-control-ssl-protocol-versions"></a>Ssl 프로토콜 버전을 제어 하는 SSL 정책을 구성할 수 있나요?

예. TLS 1.0, TLS 1.1 및 TLS 1.2를 거부 하도록 Application Gateway를 구성할 수 있습니다. 기본적으로 SSL 2.0 및 3.0은 이미 사용 하지 않도록 설정 되어 있으며 구성할 수 없습니다.

### <a name="can-i-configure-cipher-suites-and-policy-order"></a>암호 그룹 및 정책 순서를 구성할 수 있나요?

예. Application Gateway에서 [암호 그룹을 구성할](application-gateway-ssl-policy-overview.md)수 있습니다. 사용자 지정 정책을 정의 하려면 다음 암호 그룹 중 하나 이상을 사용 하도록 설정 합니다. 

* TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 
* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
* TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
* TLS_RSA_WITH_AES_128_GCM_SHA256
* TLS_RSA_WITH_AES_256_CBC_SHA256
* TLS_RSA_WITH_AES_128_CBC_SHA256

Application Gateway는 백 엔드 관리를 위해 SHA256을 사용 합니다.

### <a name="how-many-ssl-certificates-does-application-gateway-support"></a>Application Gateway 지원 되는 SSL 인증서는 몇 개입니까?

Application Gateway은 최대 100 SSL 인증서를 지원 합니다.

### <a name="how-many-authentication-certificates-for-backend-reencryption-does-application-gateway-support"></a>백 엔드 재 암호화 지원 Application Gateway 인증 인증서는 몇 개입니까?

Application Gateway는 최대 100 인증 인증서를 지원 합니다.

### <a name="does-application-gateway-natively-integrate-with-azure-key-vault"></a>Azure Key Vault와 기본적으로 통합 Application Gateway?

예, Application Gateway v2 SKU는 Key Vault를 지원 합니다. 자세한 내용은 [Key Vault 인증서를 사용 하는 SSL 종료](key-vault-certs.md)를 참조 하십시오.

### <a name="how-do-i-configure-https-listeners-for-com-and-net-sites"></a>.Com 및 .net 사이트에 대해 HTTPS 수신기를 구성 어떻게 할까요?? 

여러 도메인 기반 (호스트 기반) 라우팅의 경우 멀티 사이트 수신기를 만들고, HTTPS를 프로토콜로 사용 하는 수신기를 설정 하 고, 수신기를 라우팅 규칙과 연결할 수 있습니다. 자세한 내용은 [Application Gateway를 사용 하 여 여러 사이트 호스팅](https://docs.microsoft.com/azure/application-gateway/multiple-site-overview)을 참조 하세요.

### <a name="can-i-use-special-characters-in-my-pfx-file-password"></a>.Pfx 파일 암호에 특수 문자를 사용할 수 있나요?

아니요, .pfx 파일 암호에 영숫자 문자만 사용 합니다.

## <a name="configuration---web-application-firewall-waf"></a>구성-WAF (웹 응용 프로그램 방화벽)

### <a name="does-the-waf-sku-offer-all-the-features-available-in-the-standard-sku"></a>WAF SKU는 표준 SKU에서 사용할 수 있는 모든 기능을 제공 하나요?

예. WAF는 표준 SKU의 모든 기능을 지원 합니다.

### <a name="how-do-i-monitor-waf"></a>WAF를 모니터링하려면 어떻게 하나요?

진단 로깅을 통해 WAF를 모니터링 합니다. 자세한 내용은 [Application Gateway에 대한 진단 로깅 및 메트릭](application-gateway-diagnostics.md)을 참조 하세요.

### <a name="does-detection-mode-block-traffic"></a>검색 모드에서 트래픽을 차단하나요?

아니요. 검색 모드는 WAF 규칙을 트리거하는 트래픽만 로깅합니다.

### <a name="can-i-customize-waf-rules"></a>WAF 규칙을 사용자 지정할 수 있나요?

예. 자세한 내용은 [WAF 규칙 그룹 및 규칙 사용자 지정](application-gateway-customize-waf-rules-portal.md)을 참조 하세요.

### <a name="what-rules-are-currently-available-for-waf"></a>WAF에 대해 현재 사용할 수 있는 규칙은 무엇 인가요?

WAF는 현재 CRS [2.2.9](../web-application-firewall/ag/application-gateway-crs-rulegroups-rules.md#owasp229), [3.0](../web-application-firewall/ag/application-gateway-crs-rulegroups-rules.md#owasp30)및 [3.1](../web-application-firewall/ag/application-gateway-crs-rulegroups-rules.md#owasp31)을 지원 합니다. 이러한 규칙은 OWASP (Web Application Security Project)에서 식별 하는 대부분의 상위 10 개 취약점에 대한 기준선 보안을 제공 합니다. 

* SQL 삽입 공격 보호
* 사이트 간 스크립팅 보호
* 명령 삽입, HTTP 요청 밀 반입, HTTP 응답 분할 및 원격 파일 포함 공격과 같은 일반적인 웹 공격 으로부터 보호
* HTTP 프로토콜 위반 보호
* 누락된 호스트 사용자-에이전트 및 수락 헤더 같은 HTTP 프로토콜 이상 보호
* 보트, 크롤러 및 스캐너 방지
* 일반적인 응용 프로그램 잘못 된 기능 (즉, Apache, IIS 등) 검색

자세한 내용은 [OWASP top-10 취약성](https://www.owasp.org/index.php/Top10#OWASP_Top_10_for_2013)을 참조 하세요.

### <a name="does-waf-support-ddos-protection"></a>WAF는 DDoS protection을 지원 하나요?

예. Application gateway가 배포 된 가상 네트워크에서 DDoS 보호를 사용 하도록 설정할 수 있습니다. 이 설정은 Azure DDoS Protection 서비스가 응용 프로그램 게이트웨이 VIP (가상 IP)도 보호 하도록 합니다.

### <a name="is-there-guidance-available-to-migrate-from-the-v1-sku-to-the-v2-sku"></a>V1 SKU에서 v2 SKU로 마이그레이션하는 데 사용할 수 있는 지침이 있나요?

예. 자세한 내용은 v 1 [에서 v 2로 Azure 애플리케이션 게이트웨이 및 웹 응용 프로그램 방화벽 마이그레이션](migrate-v1-v2.md)을 참조 하세요.

## <a name="configuration---ingress-controller-for-aks"></a>AKS에 대한 구성-수신 컨트롤러

### <a name="what-is-an-ingress-controller"></a>수신 컨트롤러는 무엇 인가요?

Kubernetes를 사용 하면 `deployment` 및 `service` 리소스를 만들어 클러스터에서 내부적으로 pod 그룹을 노출할 수 있습니다. 외부에서 동일한 서비스를 노출 하기 위해 부하 분산, SSL 종료 및 이름 기반 가상 호스팅을 제공 하는 [`Ingress`](https://kubernetes.io/docs/concepts/services-networking/ingress/) 리소스가 정의 됩니다.
이 `Ingress` 리소스를 충족 하기 위해 수신 컨트롤러는 `Ingress` 리소스의 변경 내용을 수신 대기 하 고 부하 분산 장치 정책을 구성 하는 데 필요 합니다.

Application Gateway 수신 컨트롤러를 사용 하면 [Azure 애플리케이션 게이트웨이](https://azure.microsoft.com/services/application-gateway/) 를 AKS 클러스터 라고도 하는 [Azure Kubernetes 서비스](https://azure.microsoft.com/services/kubernetes-service/) 에 대한 수신으로 사용할 수 있습니다.

### <a name="can-a-single-ingress-controller-instance-manage-multiple-application-gateways"></a>단일 수신 컨트롤러 인스턴스가 여러 응용 프로그램 게이트웨이를 관리할 수 있나요?

현재 수신 컨트롤러의 한 인스턴스는 하나의 Application Gateway에만 연결 될 수 있습니다.

## <a name="diagnostics-and-logging"></a>진단 및 로깅

### <a name="what-types-of-logs-does-application-gateway-provide"></a>어떤 유형의 로그를 제공 Application Gateway 있나요?

Application Gateway는 세 개의 로그를 제공 합니다. 

* **Applicationgatewayaccesslog**: 액세스 로그에는 application gateway 프런트 엔드에 제출 된 각 요청이 포함 됩니다. 데이터에는 호출자의 IP, 요청 된 URL, 응답 대기 시간, 반환 코드 및 바이트 (in/out)가 포함 됩니다. 액세스 로그는 300 초 마다 수집 됩니다. 응용 프로그램 게이트웨이 당 하나의 레코드를 포함 합니다.
* **ApplicationGatewayPerformanceLog**: 성능 로그는 각 application gateway에 대한 성능 정보를 캡처합니다. 이 정보에는 처리량 (바이트), 처리 된 총 요청 수, 실패 한 요청 수, 정상 및 비정상 백 엔드 인스턴스 수 등이 포함 됩니다.
* **ApplicationGatewayFirewallLog**: waf를 사용 하 여 구성 하는 응용 프로그램 게이트웨이의 경우 방화벽 로그에는 검색 모드 또는 방지 모드를 통해 기록 되는 요청이 포함 됩니다.

자세한 내용은 [Application Gateway에 대한 백 엔드 상태, 진단 로그 및 메트릭](application-gateway-diagnostics.md)을 참조 하세요.

### <a name="how-do-i-know-if-my-backend-pool-members-are-healthy"></a>내 백 엔드 풀 멤버가 정상인지 어떻게 알 수 있나요?

PowerShell cmdlet `Get-AzApplicationGatewayBackendHealth` 또는 포털을 사용 하 여 상태를 확인 합니다. 자세한 내용은 [Application Gateway 진단](application-gateway-diagnostics.md)을 참조하세요.

### <a name="whats-the-retention-policy-for-the-diagnostic-logs"></a>진단 로그에 대한 보존 정책은 무엇 인가요?

진단 로그는 고객의 저장소 계정으로 전달 됩니다. 고객은 기본 설정에 따라 보존 정책을 설정할 수 있습니다. 진단 로그는 이벤트 허브 또는 Azure Monitor 로그에 보낼 수도 있습니다. 자세한 내용은 [Application Gateway 진단](application-gateway-diagnostics.md)을 참조하세요.

### <a name="how-do-i-get-audit-logs-for-application-gateway"></a>Application Gateway에 대한 감사 로그를 어떻게 얻나요?

포털의 응용 프로그램 게이트웨이 메뉴 블레이드에서 **활동 로그** 를 선택 하 여 감사 로그에 액세스 합니다. 

### <a name="can-i-set-alerts-with-application-gateway"></a>Application Gateway로 경고를 설정할 수 있나요?

예. Application Gateway에서 경고는 메트릭에 대해 구성 됩니다. 자세한 내용은 [Application Gateway 메트릭](https://docs.microsoft.com/azure/application-gateway/application-gateway-metrics) 및 [경고 알림 받기](../monitoring-and-diagnostics/insights-receive-alert-notifications.md)를 참조 하세요.

### <a name="how-do-i-analyze-traffic-statistics-for-application-gateway"></a>Application Gateway에 대한 트래픽 통계를 분석하려면 어떻게 해야 하나요?

액세스 로그는 여러 가지 방법으로 보고 분석할 수 있습니다. Azure Monitor 로그, Excel, Power BI 등을 사용 합니다.

Application Gateway 액세스 로그를 위해 인기 있는 [Goaccess](https://goaccess.io/) log analyzer를 설치 하 고 실행 하는 리소스 관리자 템플릿을 사용할 수도 있습니다. GoAccess는 고유한 방문자, 요청 된 파일, 호스트, 운영 체제, 브라우저 및 HTTP 상태 코드와 같은 중요 한 HTTP 트래픽 통계를 제공 합니다. 자세한 내용은 GitHub에서 [리소스 관리자 템플릿 폴더의 추가 정보 파일](https://aka.ms/appgwgoaccessreadme)을 참조 하세요.

### <a name="what-could-cause-backend-health-to-return-an-unknown-status"></a>백 엔드 상태에서 알 수 없는 상태를 반환 하는 이유는 무엇 인가요?

일반적으로 백 엔드에 대한 액세스가 응용 프로그램 게이트웨이 서브넷의 NSG (네트워크 보안 그룹), 사용자 지정 DNS 또는 UDR (사용자 정의 라우팅)에 의해 차단 되는 경우 알 수 없음 상태가 표시 됩니다. 자세한 내용은 [Application Gateway에 대한 백 엔드 상태, 진단 로깅 및 메트릭](application-gateway-diagnostics.md)을 참조 하세요.

### <a name="is-there-any-case-where-nsg-flow-logs-wont-show-allowed-traffic"></a>NSG 흐름 로그에 허용 된 트래픽이 표시 되지 않는 경우가 있나요?

예. 구성이 다음 시나리오와 일치 하는 경우 NSG 흐름 로그에 허용 된 트래픽이 표시 되지 않습니다.
- Application Gateway v2를 배포 했습니다.
- 응용 프로그램 게이트웨이 서브넷에 NSG가 있습니다.
- 해당 NSG에서 NSG 흐름 로그를 사용 하도록 설정 했습니다.

### <a name="how-do-i-use-application-gateway-v2-with-only-private-frontend-ip-address"></a>사설 프런트 엔드 IP 주소만 사용 하는 어떻게 할까요? Application Gateway V2를 사용 하 시겠습니까?

현재 Application Gateway V2는 개인 IP 모드만 지원 하지 않습니다. 다음 조합을 지원 합니다.
* 개인 IP 및 공용 IP
* 공용 IP만

하지만 개인 IP만 포함 된 Application Gateway v 2를 사용 하려는 경우 다음 프로세스를 수행할 수 있습니다.
1. 공용 및 개인 프런트 엔드 IP 주소를 사용 하 여 Application Gateway 만들기
2. 공용 프런트 엔드 IP 주소에 대한 수신기를 만들지 마세요. 수신기가 생성 되지 않은 경우 Application Gateway은 공용 IP 주소에 대한 트래픽을 수신 대기 하지 않습니다.
3. 우선 순위에 따라 다음 구성을 사용 하 여 Application Gateway 서브넷에 대한 [네트워크 보안 그룹](https://docs.microsoft.com/azure/virtual-network/security-overview) 을 만들고 연결 합니다.
    
    a. 원본에서 **게이트웨이 관리자** 서비스 태그 및 **대상으로의** 트래픽을 **65200-65535**로 허용 합니다. 이 포트 범위는 Azure 인프라 통신에 필요합니다. 이러한 포트는 인증서 인증을 통해 보호 (잠금 해제) 됩니다. 게이트웨이 사용자 관리자를 비롯 한 외부 엔터티는 적절 한 인증서를 사용 하지 않고 해당 엔드포인트에 대한 변경 내용을 시작할 수 없습니다.
    
    b. 원본에서 **Azureloadbalancer** 서비스 태그로 트래픽 및 대상 및 대상 포트를 **Any** 로 허용
    
    다. 원본에서 **인터넷** 서비스 태그로 모든 인바운드 트래픽을 거부 하 고 대상 및 대상 포트를 **Any**로 거부 합니다. 인바운드 규칙에서이 규칙의 *최소 우선 순위* 를 지정 합니다.
    
    d. 개인 IP 주소에 대한 액세스가 차단 되지 않도록 VirtualNetwork 인바운드 허용과 같은 기본 규칙을 유지 합니다.
    
    e. 아웃바운드 인터넷 연결은 차단할 수 없습니다. 그렇지 않으면 로깅, 메트릭 등의 문제를 발생 시킵니다.

개인 IP 전용 액세스에 대한 샘플 NSG 구성: 개인 IP 액세스 전용 ![Application Gateway V2 NSG 구성](./media/application-gateway-faq/appgw-privip-nsg.png)


## <a name="next-steps"></a>다음 단계

Application Gateway에 대해 자세히 알아보려면 [Azure 애플리케이션 게이트웨이의 정의](overview.md)를 참조 하세요.
