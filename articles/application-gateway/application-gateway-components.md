---
title: 응용 프로그램 게이트웨이 구성 요소
description: 이 문서에서는 응용 프로그램 게이트웨이의 다양 한 구성 요소에 대 한 정보를 제공 합니다.
services: application-gateway
author: surajmb
ms.service: application-gateway
ms.topic: conceptual
ms.date: 08/21/2020
ms.author: surmb
ms.openlocfilehash: ebd06b0b78ee511dce535ff4220df03087fb6906
ms.sourcegitcommit: 5b6acff3d1d0603904929cc529ecbcfcde90d88b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/21/2020
ms.locfileid: "88723319"
---
# <a name="application-gateway-components"></a>응용 프로그램 게이트웨이 구성 요소

 응용 프로그램 게이트웨이는 클라이언트의 단일 연락 지점으로 사용 됩니다. 들어오는 응용 프로그램 트래픽을 Azure Vm, 가상 머신 확장 집합, Azure App Service 및 온-프레미스/외부 서버를 포함 하는 여러 백 엔드 풀에 분산 합니다. 트래픽을 분산 하기 위해 application gateway는이 문서에 설명 된 여러 구성 요소를 사용 합니다.

![응용 프로그램 게이트웨이에서 사용 되는 구성 요소](./media/application-gateway-components/application-gateway-components.png)

## <a name="frontend-ip-addresses"></a>프런트 엔드 IP 주소

프런트 엔드 IP 주소는 application gateway와 연결 된 IP 주소입니다. 공용 IP 주소, 개인 IP 주소 또는 둘 다를 갖도록 응용 프로그램 게이트웨이를 구성할 수 있습니다. Application gateway는 하나의 공용 또는 하나의 개인 IP 주소를 지원 합니다. 가상 네트워크 및 공용 IP 주소는 application gateway와 동일한 위치에 있어야 합니다. 만든 후 프런트 엔드 IP 주소는 수신기와 연결 됩니다.

### <a name="static-versus-dynamic-public-ip-address"></a>정적 및 동적 공용 IP 주소

고정 내부 IP 주소와 정적 공용 IP 주소 또는 고정 공용 IP 주소만 지원 하도록 Azure 애플리케이션 Gateway V2 SKU를 구성할 수 있습니다. 고정 내부 IP 주소만 지원 하도록 구성할 수 없습니다.

V1 SKU는 고정 또는 동적 내부 IP 주소 및 동적 공용 IP 주소를 지원 하도록 구성할 수 있습니다. Application Gateway의 동적 IP 주소는 실행 중인 게이트웨이에서 변경 되지 않습니다. 게이트웨이를 중지 하거나 시작 하는 경우에만 변경할 수 있습니다. 시스템 오류, 업데이트, Azure 호스트 업데이트 등은 변경 되지 않습니다. 

Application gateway와 연결 된 DNS 이름은 게이트웨이의 수명 주기 동안 변경 되지 않습니다. 따라서 CNAME 별칭을 사용 하 여 application gateway의 DNS 주소를 가리키도록 해야 합니다.

## <a name="listeners"></a>수신기

수신기는 들어오는 연결 요청을 확인 하는 논리적 엔터티입니다. 요청과 연결 된 프로토콜, 포트, 호스트 이름 및 IP 주소가 수신기 구성과 연결 된 동일한 요소와 일치 하는 경우 수신기는 요청을 수락 합니다.

응용 프로그램 게이트웨이를 사용 하기 전에 하나 이상의 수신기를 추가 해야 합니다. Application gateway에 연결 된 수신기가 여러 개 있을 수 있으며, 동일한 프로토콜에 대해 사용할 수 있습니다.

수신기가 클라이언트에서 들어오는 요청을 감지 하면 응용 프로그램 게이트웨이는 이러한 요청을 규칙에 구성 된 백 엔드 풀의 멤버로 라우팅합니다.

수신기는 다음 포트 및 프로토콜을 지원 합니다.

### <a name="ports"></a>포트

포트는 수신기가 클라이언트 요청을 수신 대기 하는 위치입니다. V1 SKU의 경우 1에서 65502 사이의 포트를 구성 하 고, v2 SKU의 경우 1 ~ 65199로 구성할 수 있습니다.

### <a name="protocols"></a>프로토콜

Application Gateway는 HTTP, HTTPS, HTTP/2 및 WebSocket의 네 가지 프로토콜을 지원 합니다.
>[!NOTE]
>Application Gateway 수신기에 연결하는 클라이언트의 경우에만 HTTP/2 프로토콜이 지원됩니다. 백 엔드 서버 풀에 대 한 통신은 항상 HTTP/1.1을 통해입니다. 기본적으로 HTTP/2 지원은 사용할 수 없습니다. 이 기능을 사용 하도록 선택할 수 있습니다.

- 수신기 구성에서 HTTP 및 HTTPS 프로토콜을 지정 합니다.
- [Websocket 및 HTTP/2 프로토콜](features.md#websocket-and-http2-traffic) 에 대 한 지원은 기본적으로 제공 되며 [websocket 지원은](application-gateway-websocket.md) 기본적으로 사용 하도록 설정 되어 있습니다. WebSocket 지원을 선택적으로 사용하거나 사용하지 않도록 설정하는 사용자 구성 가능 설정은 없습니다. HTTP 및 HTTPS 수신기 모두에서 Websocket을 사용 합니다.

TLS 종료에 HTTPS 수신기를 사용 합니다. HTTPS 수신기는 암호화 및 암호 해독 작업을 응용 프로그램 게이트웨이로 오프 로드 하므로 웹 서버가 오버 헤드로 부담이 되지 않습니다.

### <a name="custom-error-pages"></a>사용자 지정 오류 페이지

Application Gateway를 사용 하면 기본 오류 페이지를 표시 하는 대신 사용자 지정 오류 페이지를 만들 수 있습니다. 사용자 지정 오류 페이지를 사용하여 사용자 고유의 브랜딩과 레이아웃을 사용할 수 있습니다. Application Gateway 요청에서 백 엔드에 연결할 수 없는 경우 사용자 지정 오류 페이지가 표시 됩니다.

자세한 내용은 [application gateway에 대 한 사용자 지정 오류 페이지](custom-error.md)를 참조 하세요.

### <a name="types-of-listeners"></a>수신기 형식

수신기에는 두 가지 유형이 있습니다.

- **기본**. 이 유형의 수신기는 단일 도메인 사이트를 수신 대기 합니다. 여기에는 응용 프로그램 게이트웨이의 IP 주소에 대 한 단일 DNS 매핑이 있습니다. 이 수신기 구성은 응용 프로그램 게이트웨이 뒤에 단일 사이트를 호스트 하는 경우에 필요 합니다.

- **다중 사이트**. 이 수신기 구성은 동일한 응용 프로그램 게이트웨이에서 둘 이상의 웹 응용 프로그램에 대 한 호스트 이름 또는 도메인 이름을 기반으로 하는 라우팅을 구성 하려는 경우에 필요 합니다. 이 기능을 사용하면 최대 100개의 웹 사이트를 하나의 애플리케이션 게이트웨이에 추가하여 배포에 대해 보다 효율적인 토폴로지를 구성할 수 있습니다. 각 웹 사이트는 고유한 백 엔드 풀로 이동할 수 있습니다. 예를 들어 contoso.com, fabrikam.com 및 adatum.com이라는 세 개의 도메인이 애플리케이션 게이트웨이의 IP 주소를 가리킵니다. 3 개의 [다중 사이트 수신기](multiple-site-overview.md) 를 만들고 각 포트 및 프로토콜 설정에 대해 각 수신기를 구성 합니다. 

    또한 다중 사이트 수신기에서 와일드카드 호스트 이름을 정의하고 수신기당 최대 5개의 호스트 이름을 정의할 수 있습니다. 자세한 내용은 [수신기의 와일드 카드 호스트 이름 (미리 보기)](multiple-site-overview.md#wildcard-host-names-in-listener-preview)을 참조 하세요.

    다중 사이트 수신기를 구성 하는 방법에 대 한 자세한 내용은 [Azure Portal를 사용 하 여 Application Gateway에서 다중 사이트 호스팅](create-multiple-sites-portal.md)을 참조 하세요.

수신기를 만든 후에는 요청 라우팅 규칙과 연결 합니다. 이 규칙은 수신기에서 받은 요청을 백 엔드로 라우팅하는 방법을 결정 합니다. 또한 요청 라우팅 규칙에는로 라우팅할 백 엔드 풀과 백 엔드 포트, 프로토콜 등이 언급 된 HTTP 설정이 포함 됩니다.

## <a name="request-routing-rules"></a>라우팅 규칙 요청

요청 라우팅 규칙은 수신기에서 트래픽을 라우팅하는 방법을 결정 하기 때문에 응용 프로그램 게이트웨이의 주요 구성 요소입니다. 규칙은 수신기, 백 엔드 서버 풀 및 백 엔드 HTTP 설정을 바인딩합니다.

수신기에서 요청을 수락 하는 경우 요청 라우팅 규칙은 요청을 백 엔드로 전달 하거나 다른 위치로 리디렉션합니다. 요청이 백 엔드로 전달 되는 경우 요청 라우팅 규칙은 전달할 백 엔드 서버 풀을 정의 합니다. 요청 라우팅 규칙은 요청 헤더를 다시 쓸지 여부도 결정 합니다. 하나의 수신기를 하나의 규칙에 연결할 수 있습니다.

요청 라우팅 규칙에는 다음과 같은 두 가지 유형이 있습니다.

- **기본**. 연결 된 수신기의 모든 요청 (예: blog.contoso.com/*)은 연결 된 HTTP 설정을 사용 하 여 연결 된 백 엔드 풀로 전달 됩니다.

- **경로를 기반으로**합니다. 이 라우팅 규칙을 사용 하면 요청 된 URL에 따라 연결 된 수신기의 요청을 특정 백 엔드 풀로 라우팅할 수 있습니다. 요청에 있는 URL의 경로가 경로 기반 규칙의 경로 패턴과 일치 하는 경우 규칙은 해당 요청을 라우팅합니다. 경로 패턴은 해당 쿼리 매개 변수가 아닌 URL 경로에만 적용 됩니다. 수신기 요청의 URL 경로가 경로 기반 규칙과 일치 하지 않으면 기본 백 엔드 풀 및 HTTP 설정에 요청을 라우팅합니다.

자세한 내용은 [URL 기반 라우팅](url-route-overview.md)을 참조 하세요.

### <a name="redirection-support"></a>리디렉션 지원

또한 요청 라우팅 규칙을 사용 하면 응용 프로그램 게이트웨이에서 트래픽을 리디렉션할 수 있습니다. 이는 일반 리디렉션 메커니즘 이므로 규칙을 사용 하 여 정의 하는 모든 포트로 리디렉션할 수 있습니다.

리디렉션 대상을 다른 수신기 (자동 HTTP에서 HTTPS로의 리디렉션을 사용 하도록 설정 하는 데 도움이 될 수 있음) 또는 외부 사이트를 선택할 수 있습니다. 리디렉션을 일시적 또는 영구적으로 또는 URI 경로 및 쿼리 문자열을 리디렉션된 URL에 추가 하도록 선택할 수도 있습니다.

자세한 내용은 [응용 프로그램 게이트웨이에서 트래픽 리디렉션](redirect-overview.md)을 참조 하세요.

### <a name="rewrite-http-headers-and-url"></a>HTTP 헤더 및 URL 다시 쓰기

다시 쓰기 규칙을 사용 하 여 요청 및 응답 패킷이 응용 프로그램 게이트웨이를 통해 클라이언트와 백 엔드 풀 간에 이동할 때 URL 경로 및 쿼리 문자열 매개 변수 뿐만 아니라 HTTP 요청 및 응답 헤더를 추가, 제거 또는 업데이트할 수 있습니다.

헤더 및 URL 매개 변수는 정적 값 또는 다른 헤더 및 서버 변수로 설정할 수 있습니다. 클라이언트 IP 주소 추출, 백 엔드에 대 한 중요 한 정보 제거, 보안 추가 등의 중요 한 사용 사례에 도움이 됩니다.

자세한 내용은 [application gateway에서 HTTP 헤더 및 URL 다시 작성](rewrite-http-headers-url.md)을 참조 하세요.

## <a name="http-settings"></a>HTTP 설정

응용 프로그램 게이트웨이는이 구성 요소에 자세히 설명 된 포트 번호, 프로토콜 및 기타 설정을 사용 하 여 백 엔드 서버 (HTTP 설정을 포함 하는 요청 라우팅 규칙에 지정)로 트래픽을 라우팅합니다.

HTTP 설정에 사용 되는 포트 및 프로토콜에 따라 응용 프로그램 게이트웨이와 백 엔드 서버 간의 트래픽이 암호화 되는지 (종단 간 TLS 제공) 또는 암호화 되지 않은 지 결정 됩니다.

이 구성 요소는 다음 작업에도 사용 됩니다.

- [쿠키 기반 세션 선호도](features.md#session-affinity)를 사용 하 여 사용자 세션을 동일한 서버에 유지할지 여부를 결정 합니다.

- [연결 드레이닝](features.md#connection-draining)을 사용 하 여 백 엔드 풀 멤버를 정상적으로 제거 합니다.

- 사용자 지정 프로브를 연결 하 여 백 엔드 상태를 모니터링 하 고, 요청 시간 제한 간격을 설정 하 고, 요청에서 호스트 이름 및 경로를 재정의 하 고, 한 번 클릭으로 App Service 백 엔드에 대 한 설정을 지정할 수 있습니다.

## <a name="backend-pools"></a>백 엔드 풀

백 엔드 풀은 요청을 처리 하는 백 엔드 서버에 요청을 라우팅합니다. 백 엔드 풀에는 다음이 포함 될 수 있습니다.

- NIC
- 가상 머신 크기 집합
- 공용 IP 주소
- 내부 IP 주소
- FQDN
- 다중 테 넌 트 백 엔드 (예: App Service)

Application Gateway 백 엔드 풀 멤버는 가용성 집합에 연결 되지 않습니다. 응용 프로그램 게이트웨이는 가상 네트워크 외부의 인스턴스와 통신할 수 있습니다. 따라서 백 엔드 풀의 멤버는 IP 연결이 있는 한 클러스터, 데이터 센터 전체 또는 Azure 외부에 있을 수 있습니다.

내부 Ip를 백 엔드 풀 멤버로 사용 하는 경우 [가상 네트워크 피어 링](../virtual-network/virtual-network-peering-overview.md) 또는 [VPN gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md)를 사용 해야 합니다. 가상 네트워크 피어 링은 지원 되며 다른 가상 네트워크의 부하를 분산 하는 데 유용 합니다.

또한 트래픽이 허용 되는 경우 응용 프로그램 게이트웨이는 Azure Express 경로 또는 VPN 터널을 통해 연결 된 경우 온-프레미스 서버와 통신할 수 있습니다.

여러 유형의 요청에 대해 서로 다른 백 엔드 풀을 만들 수 있습니다. 예를 들어 일반 요청에 대해 하나의 백 엔드 풀을 만든 다음 응용 프로그램의 마이크로 서비스에 대 한 요청에 대 한 다른 백 엔드 풀을 만듭니다.

## <a name="health-probes"></a>상태 프로브

기본적으로 응용 프로그램 게이트웨이는 백 엔드 풀의 모든 리소스 상태를 모니터링 하 고 비정상 상태를 자동으로 제거 합니다. 그런 다음 비정상 인스턴스를 모니터링 하 고 사용할 수 있게 되 고 상태 프로브에 응답할 때 정상 백 엔드 풀에 다시 추가 합니다.

기본 상태 프로브 모니터링 사용 외에도 애플리케이션 프로그의 요구 사항에 맞게 상태 프로브를 사용자 지정할 수도 있습니다. 사용자 지정 프로브를 통해 상태 모니터링을 보다 세부적으로 제어할 수 있습니다. 사용자 지정 프로브를 사용 하는 경우 백 엔드 풀 인스턴스를 비정상, 사용자 지정 상태 코드 및 응답 본문 일치 항목으로 표시 하기 전에 사용자 지정 호스트 이름, URL 경로, 프로브 간격 및 허용할 실패 응답 횟수를 구성할 수 있습니다. 각 백 엔드 풀의 상태를 모니터링 하도록 사용자 지정 프로브를 구성 하는 것이 좋습니다.

자세한 내용은 [application gateway의 상태 모니터링](../application-gateway/application-gateway-probe-overview.md)을 참조 하세요.

## <a name="next-steps"></a>다음 단계

애플리케이션 게이트웨이를 만듭니다.

* [Azure Portal에서](quick-create-portal.md)
* [Azure PowerShell 사용](quick-create-powershell.md)
* [Azure CLI 사용](quick-create-cli.md)
