---
title: Azure Application Gateway에서 종단 간 SSL 사용
description: 이 문서는 Application Gateway 종단 간 SSL 지원에 대한 개요입니다.
services: application-gateway
author: amsriva
ms.service: application-gateway
ms.topic: article
ms.date: 3/19/2019
ms.author: victorh
ms.openlocfilehash: 92799019d13de71d911767d8e400598513587667
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60715240"
---
# <a name="overview-of-ssl-termination-and-end-to-end-ssl-with-application-gateway"></a>SSL 종료 및 Application Gateway를 사용 하 여 종단 간 SSL 개요

Secure Sockets Layer (SSL)는 웹 서버와 브라우저 간에 암호화 된 링크를 설정 하기 위한 표준 보안 기술입니다. 이 링크를 사용 하면 모든 데이터가 웹 서버와 브라우저 간에 전달 되는지 개인 및 암호화 된 상태로 유지 합니다. Application gateway는 종단 간 SSL 암호화 뿐만 아니라 게이트웨이 모두 SSL 종료를 지원 합니다.

## <a name="ssl-termination"></a>SSL 종료

Application Gateway는 게이트웨이에서 SSL 종료를 지원합니다. 그 후의 트래픽은 일반적으로 암호화되지 않은 상태로 백 엔드 서버로 흐릅니다. Application gateway에서 SSL 종료를 수행 하는 장점이 많습니다.

- **향상 된 성능** – 가장 큰 성능 저하가 SSL 암호 해독을 수행 하는 경우 초기 핸드셰이크 됩니다. 성능 향상을 위해 암호 해독을 수행 하는 서버는 SSL 세션 Id를 캐시 하 고 TLS 세션 티켓을 관리 합니다. Application gateway에서 이렇게 하는 경우 동일한 클라이언트에서 모든 요청 캐시 된 값을 사용할 수 있습니다. 백 엔드 서버에서 완료 되는 클라이언트의 요청 클라이언트를 다른 서버로 이동 될 때마다는 re‑authenticate 합니다. TLS 티켓을 사용 하 여가이 문제를 줄이는 데 도움이 되지만 모든 클라이언트에서 지원 되지 않으며 구성 및 관리 하기 어려울 수 있습니다.
- **백 엔드 서버 사용률 향상** – SSL/TLS 처리는 매우 CPU를 많이 사용 잡고 키 크기가 커질수록 더 많이 사용 합니다. 무엇 인지 가장 효율적인에 집중할 수 있도록 백 엔드 서버에서이 작업을 제거에서 콘텐츠를 제공 합니다.
- **지능형 라우팅을** – 트래픽을 암호 해독 하 여 application gateway 헤더, URI 및 등과 같은 요청 콘텐츠에 대 한 액세스 권한이 및 경로 요청에이 데이터를 사용할 수 있습니다.
- **인증서 관리** – 인증서만 구매 하 여 application gateway 및 일부 백 엔드 서버에 설치 해야 합니다. 이렇게 하면 시간과 비용을 모두 저장 합니다.

SSL 종료를 구성 하려면 SSL 인증서를 SSL 프로토콜 사양에 따라 대칭 키가 파생 응용 프로그램 게이트웨이 사용 하도록 설정 하려면 수신기에 추가 해야 합니다. 대칭 키 암호화 및 게이트웨이에 전송 된 트래픽을 암호 해독에 사용 됩니다. SSL 인증서 개인 정보 교환 (PFX) 형식으로 있어야 합니다. 이 파일 형식을 사용하면 애플리케이션 게이트웨이에서 트래픽의 암호화 및 암호 해독을 수행하는 데 필요한 개인 키를 내보낼 수 있습니다.

> [!NOTE] 
>
> 응용 프로그램 게이트웨이 새 인증서 만들기 또는 인증 기관에 인증서 요청을 전송 하는 모든 기능을 제공 하지 않습니다.

SSL 연결이 작동 하려면 SSL 인증서는 다음 조건을 충족 하는지 확인 해야 합니다.

- 현재 날짜 및 시간 유효"인증서"하는 유효"날짜 범위 이내 이며
- 해당 인증서의 "" CN (일반 이름) 요청에 호스트 헤더와 일치 합니다. 예를 들어, 클라이언트 요청을 중이면 `https://www.contoso.com/`, CN 이어야 합니다. `www.contoso.com`합니다.

### <a name="certificates-supported-for-ssl-termination"></a>인증서를 SSL 종료를 위한 지원

Application gateway는 다음과 같은 유형의 인증서를 지원 합니다.

- CA (인증 기관) 인증서: CA 인증서는 인증 기관 (CA)에서 발급 된 디지털 인증서
- EV (확장 유효성 검사) 인증서: EV 인증서는 업계 표준 인증서 지침 이며 브라우저 검색 표시줄을 녹색 설정 되 고 회사 이름에도 게시 됩니다.
- 와일드 카드 인증서: 이 인증서를 기반으로 하는 하위 도메인을 개수에 관계 없이 지원 *. site.com, 하위 도메인을 대신으로 대체 됩니다는 *입니다. 하지만 Site.com을 지원 그렇지 않은 경우 앞에 오는 "www"를 입력 하지 않고 웹 사이트에 액세스 하는 작업 사용자는, 와일드 카드 인증서 설명 하지는 하므로 합니다.
- 자체 서명 된 인증서: 클라이언트 브라우저는 이러한 인증서를 신뢰 하지 않는 및는 사용자에 게 경고 가상 서비스의 인증서 신뢰 체인의 일부가 아닙니다. 자체 서명 된 인증서는 테스트 또는 관리자가 클라이언트를 제어 하 고 브라우저의 보안 경고를 안전 하 게 무시할 수 있는 환경에 적합 합니다. 프로덕션 워크 로드는 자체 서명 된 인증서를 사용 하지 마십시오.

자세한 내용은 [응용 프로그램 게이트웨이 사용 하 여 SSL 종료를 구성](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal)합니다.

## <a name="end-to-end-ssl-encryption"></a>SSL 암호화를 종단 간

일부 고객은 백 엔드 서버에 암호화 되지 않은 통신을 원하지 않을 수 있습니다. 이는 보안 요구 사항, 규정 준수 요구 사항 때문이거나 애플리케이션이 보안 연결만 수락하는 것이기 때문일 수 있습니다. 이러한 애플리케이션을 위해 Application Gateway에서 종단 간 SSL 암호화를 지원합니다.

종단 간 SSL을 사용하면 Application Gateway에서 제공하는 계층 7 부하 분산 기능의 이점을 활용하면서 중요한 데이터를 암호화하여 백 엔드로 안전하게 전송할 수 있습니다. 이러한 기능 중 일부는 쿠키 기반 세션 선호도, URL 기반 라우팅, 사이트 기반 라우팅 지원 또는 X-Forwarded-* 헤더 삽입 기능입니다.

종단 간 SSL 통신 모드로 구성한 경우 Application Gateway가 게이트웨이에서 SSL 세션을 종료하고 사용자 트래픽을 암호 해독합니다. 그런 다음 구성된 규칙을 적용하여 트래픽을 라우팅할 적절한 백 엔드 풀 인스턴스를 선택합니다. 그런 다음, Application Gateway에서 백 엔드 서버에 대한 새로운 SSL 연결을 시작하고, 백 엔드 서버의 공개 키 인증서를 사용하여 데이터를 다시 암호화한 다음, 백 엔드에 해당 요청을 전송합니다. 웹 서버의 모든 응답은 동일한 프로세스를 거쳐 최종 사용자에게 돌아갑니다. 프로토콜 설정에서 설정 하 여 종단 간 SSL 사용 하도록 설정할지 [백 엔드 HTTP 설정](https://docs.microsoft.com/azure/application-gateway/configuration-overview#http-settings) HTTPS로 다음에 적용 되는 백 엔드 풀입니다.

SSL 정책 프런트 엔드 및 백 엔드 트래픽에 적용 됩니다. 프런트 엔드에 Application Gateway가 서버 역할을 중지 되 고 정책을 적용 합니다. 백 엔드 응용 프로그램 게이트웨이 클라이언트로 동작할 및 SSL 핸드셰이크 중 기본 설정으로 프로토콜/암호 정보를 보냅니다.

Application gateway만 통신 하거나 허용 목록에 있는 해당 백 엔드 인스턴스를 사용 하 여 application gateway 또는 해당 인증서는 인증서 CN이 http에서 호스트 이름과 일치 하는 잘 알려진 CA 기관에서 서명 된 인증서 백 엔드 설정입니다. 여기에 Azure App service web apps 및 Azure API Management와 같은 신뢰할 수 있는 Azure 서비스 포함 됩니다.

백 엔드 풀에서 멤버의 인증서 잘 알려진 CA 기관에서 서명 되지 않은 경우 보안 통신을 허용 하도록 인증서를 사용 하 여 사용 하도록 설정 하는 종단 간 SSL 사용 하 여 백 엔드 풀의 각 인스턴스에 구성 되어야 합니다. 인증서를 추가하면 Application Gateway가 알려진 백 엔드 인스턴스하고만 통신하게 됩니다. 그러면 종단 간 통신의 보안이 유지됩니다.

> [!NOTE] 
>
> 인증 인증서 설정 Azure App service web apps 및 Azure API Management와 같은 신뢰할 수 있는 Azure 서비스에 대 한 필요 하지 않습니다.

> [!NOTE] 
>
> 에 추가 된 인증서 **백 엔드 HTTP 설정** 백 엔드 인증 서버 수에 추가 된 인증서와 동일 합니다 **수신기** 마다 또는 응용 프로그램 게이트웨이에서 SSL 종료를 위한 향상 된 보안입니다.

![종단 간 SSL 시나리오][1]

이 예제에서는 요청 TLS1.2를 사용하여 종단 간 SSL을 사용하여 백엔드 서버 Pool1에 라우팅됩니다.

## <a name="end-to-end-ssl-and-whitelisting-of-certificates"></a>종단 간 SSL 및 인증서 허용 목록

Application Gateway는 알려진 백 엔드 인스턴스, 즉 Application Gateway을 통해 인증서가 허용 목록에 추가된 인스턴스와 유일하게 통신할 수 있습니다. 인증서 허용 목록을 사용하려면 백 엔드 서버 인증서의 공개 키를 애플리케이션 게이트웨이(루트 인증서가 아니라)에 업로드해야 합니다. 그러면 알려진 허용 목록의 백 엔드에 대한 연결만 허용됩니다. 나머지 백 엔드는 게이트웨이 오류를 초래합니다. 자체 서명된 인증서는 테스트 용도이며 프로덕션 워크로드에는 권장하지 않습니다. 마찬가지로 이러한 인증서를 사용하려면 위에서 설명했듯이 애플리케이션 게이트웨이를 통해 허용 목록에 추가해야 합니다.

> [!NOTE]
> Azure App Service와 같은 신뢰할 수 있는 Azure 서비스에는 인증 인증서 설정이 필요하지 않습니다.

## <a name="end-to-end-ssl-with-the-v2-sku"></a>v2 SKU를 사용하는 종단 간 SSL

인증 인증서는 Application Gateway v2 SKU에서 더 이상 사용되지 않으며 신뢰할 수 있는 루트 인증서로 대체되었습니다. 이러한 인증서는 다음과 같은 몇 가지 주요 차이점을 제외하고 인증 인증서와 비슷합니다.

- 해당 CN이 HTTP 백 엔드 설정의 호스트 이름과 일치하는 잘 알려진 CA 기관에서 서명한 인증서의 경우 종단 간 SSL 작동을 위해 추가 단계가 필요하지 않습니다. 

   예를 들어, 백 엔드 인증서가 잘 알려진 CA에서 발급한 것이고 CN이 contoso.com이며, 백 엔드 http 설정의 호스트 필드도 contoso.com으로 설정된 경우 추가 단계가 필요하지 않습니다. 백 엔드 http 설정 프로토콜을 HTTPS로 설정할 수 있고, 상태 프로브 및 데이터 경로 둘 다 SSL을 사용하도록 설정할 수 있습니다. Azure App Service 또는 다른 Azure 웹 서비스를 백 엔드로 사용 하는, 이러한도 암시적으로 신뢰할 수 있는 추가 단계가 필요 하지 않습니다 종단 간 SSL에 대 한 고 합니다.
- 인증서가 자체 서명되었거나 알 수 없는 매개체가 서명한 경우 v2 SKU에서 종단 간 SSL을 사용하도록 설정하려면 신뢰할 수 있는 루트 인증서를 정의해야 합니다. Application Gateway는 해당 서버 인증서의 루트 인증서가 풀과 연결된 백 엔드 http 설정의 신뢰할 수 있는 루트 인증서 목록 중 하나와 일치하는 백 엔드와만 통신합니다.
- 루트 인증서 일치 외에, Application Gateway는 백 엔드 http 설정에 지정된 호스트 설정이 백 엔드 서버의 SSL 인증서가 제공하는 CN(공통 이름)의 호스트 설정과 일치하는지도 확인합니다. 백 엔드에 대한 SSL 연결을 설정하려고 하면 Application Gateway는 SNI(서버 이름 표시) 확장을 백 엔드 http 설정에 지정된 호스트로 설정합니다.
- 백 엔드 http 설정의 호스트 필드 대신 **pick hostname from backend address**(백 엔드 주소에서 호스트 이름 선택)를 선택하면 SNI 헤더가 항상 백 엔드 풀 FQDN으로 설정되고, 백 엔드 서버 SSL 인증서의 CN은 해당 FQDN과 일치해야 합니다. Ip 사용 하 여 백 엔드 풀 멤버는이 시나리오에서 지원 되지 않습니다.
- 루트 인증서는 백 엔드 서버 인증서의 base64 인코딩 루트 인증서입니다.

## <a name="next-steps"></a>다음 단계

종단 간 SSL에 대해 학습한 후에는 [PowerShell에서 Application Gateway를 사용하여 종단 간 SSL 구성](application-gateway-end-to-end-ssl-powershell.md)으로 이동하고 종단 간 SSL을 사용하는 Application Gateway를 만듭니다.

<!--Image references-->

[1]: ./media/ssl-overview/scenario.png
