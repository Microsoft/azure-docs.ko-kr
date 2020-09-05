---
title: Azure Application Gateway에서 엔드투엔드 TLS를 사용하도록 설정
description: 이 문서는 Application Gateway 엔드투엔드 TLS 지원에 대한 개요입니다.
services: application-gateway
author: surajmb
ms.service: application-gateway
ms.topic: conceptual
ms.date: 08/21/2020
ms.author: victorh
ms.openlocfilehash: 3d714b579bebb096745a47410da3f8f458e27161
ms.sourcegitcommit: 5b6acff3d1d0603904929cc529ecbcfcde90d88b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/21/2020
ms.locfileid: "88723302"
---
# <a name="overview-of-tls-termination-and-end-to-end-tls-with-application-gateway"></a>Application Gateway를 사용한 TLS 종료 및 종단간 TLS 개요

이전에 SSL(Secure Sockets Layer)로 알려진 TLS(전송 계층 보안)는 웹 서버와 브라우저 간에 암호화된 링크를 설정하기 위한 표준 보안 기술입니다. 이 링크를 통해 웹 서버와 브라우저 간에 전달되는 모든 데이터는 개인 및 암호화된 상태로 유지됩니다. Application Gateway는 게이트웨이에서 TLS 종료 및 엔드투엔드 TLS 암호화를 모두 지원합니다.

## <a name="tls-termination"></a>TLS 종료

Application Gateway는 게이트웨이에서 TLS 종료를 지원합니다. 그 후의 트래픽은 일반적으로 암호화되지 않은 상태로 백 엔드 서버로 흐릅니다. Application Gateway에서 TLS 종료를 수행하는 경우 다음과 같은 여러 이점이 있습니다.

- **성능 향상** - TLS 암호 해독을 수행할 때 가장 큰 성능 저하는 초기 핸드셰이크입니다. 성능을 향상시키기 위해 암호 해독을 수행하는 서버는 TLS 세션 ID를 캐시하고 TLS 세션 티켓을 관리합니다. Application Gateway에서 이 작업을 수행하는 경우 동일한 클라이언트의 모든 요청은 캐시된 값을 사용할 수 있습니다. 백 엔드 서버에서 작업이 수행된 경우 클라이언트의 요청이 다른 서버로 이동할 때마다 클라이언트는 다시 인증해야 합니다. TLS 티켓을 사용하면 이 문제를 완화할 수 있지만 모든 클라이언트에서 지원되지 않으며 구성 및 관리가 어려울 수 있습니다.
- **백 엔드 서버의 활용도 향상** - SSL/TLS 처리는 CPU를 많이 사용하며 키 크기가 증가하는 경우 더 많이 사용하게 됩니다. 백 엔드 서버에서 이 작업을 제거하면 이러한 작업을 통해 콘텐츠를 전달하는 가장 효율적인 항목에 집중할 수 있습니다.
- **지능형 라우팅** - 트래픽 암호를 해독하여 Application Gateway는 헤더, URI 등과 같은 요청 콘텐츠에 액세스할 수 있으며 이 데이터를 사용하여 요청을 라우팅할 수 있습니다.
- **인증서 관리** - 인증서를 구매하여 모든 백 엔드 서버가 아닌 Application Gateway에만 설치해야 합니다. 이렇게 하면 시간과 돈이 모두 절약됩니다.

TLS 종료를 구성하려면 TLS/SSL 인증서를 수신기에 추가하여 Application Gateway에서 TLS/SSL 프로토콜 사양에 따라 대칭 키를 파생할 수 있도록 해야 합니다. 이렇게 파생된 대칭 키는 게이트웨이로 전송되는 트래픽을 암호화하고 암호를 해독하는 데 사용됩니다. TLS/SSL 인증서는 PFX(개인 정보 교환) 형식이어야 합니다. 이 파일 형식을 사용하면 애플리케이션 게이트웨이에서 트래픽의 암호화 및 암호 해독을 수행하는 데 필요한 프라이빗 키를 내보낼 수 있습니다.

> [!IMPORTANT] 
> 수신기의 인증서를 사용 하려면 전체 인증서 체인 (CA의 루트 인증서, intermediates 및 리프 인증서)을 업로드 하 여 신뢰 체인을 설정 해야 합니다. 


> [!NOTE] 
>
> Application Gateway는 새 인증서를 만들거나 인증 기관에 인증서 요청을 보내는 기능을 제공하지 않습니다.

TLS 연결이 작동하려면 TLS/SSL 인증서가 다음 조건을 충족하는지 확인해야 합니다.

- 현재 날짜와 시간이 인증서의 "유효 기간 시작" 및 "유효 기간 종료" 날짜 범위 내에 있습니다.
- 인증서의 CN(일반 이름)이 요청의 호스트 헤더와 일치합니다. 예를 들어 클라이언트가 `https://www.contoso.com/`에 요청하는 경우 CN이 `www.contoso.com`이어야 합니다.

### <a name="certificates-supported-for-tls-termination"></a>TLS 종료에 대해 지원되는 인증서

Application Gateway는 다음과 같은 유형의 인증서를 지원합니다.

- CA(인증 기관) 인증서: CA 인증서는 CA(인증 기관)에서 발급한 디지털 인증서입니다.
- EV(확장 유효성 검사) 인증서: EV 인증서는 업계 표준 인증서 지침을 준수하는 인증서입니다. 그러면 브라우저 로케이터 막대가 녹색으로 바뀌고 회사 이름도 게시됩니다.
- 와일드카드 인증서: 이 인증서는 *.site.com을 기반으로 하는 여러 하위 도메인을 지원합니다. 여기서 하위 도메인은 *을 대체합니다. 그러나 site.com이 지원되지 않으므로 사용자가 "www"를 입력하지 않고 웹 사이트에 액세스하는 경우 와일드카드 인증서에 포함되지 않습니다.
- 자체 서명된 인증서: 클라이언트 브라우저는 이러한 인증서를 신뢰하지 않으며 사용자에게 가상 서비스의 인증서가 신뢰 체인에 포함되지 않는다고 경고합니다. 자체 서명된 인증서는 관리자가 클라이언트를 제어하고 브라우저의 보안 경고를 안전하게 무시할 수 있는 테스트 또는 환경에 적합합니다. 프로덕션 워크로드는 자체 서명된 인증서를 사용해서는 안 됩니다.

자세한 내용은 [Application Gateway를 사용하여 TLS 종료 구성](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal)을 참조하세요.

### <a name="size-of-the-certificate"></a>인증서의 크기
[Application Gateway 제한](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#application-gateway-limits) 섹션을 확인하여 지원되는 최대 TLS/SSL 인증서 크기를 파악합니다.

## <a name="end-to-end-tls-encryption"></a>엔드투엔드 TLS 암호화

백 엔드 서버에 암호화되지 않은 통신을 원하지 않을 수 있습니다. 보안 요구 사항, 규정 준수 요구 사항이 있거나 애플리케이션에서 보안 연결만 수락하는 것일 수 있습니다. Azure Application Gateway에는 이러한 요구 사항을 지원하기 위한 엔드투엔드 TLS 암호화가 있습니다.

엔드투엔드 TLS를 사용하면 Application Gateway의 계층 7 부하 분산 기능을 사용하는 동안 중요한 데이터를 암호화하여 백 엔드로 안전하게 전송할 수 있습니다. 이러한 기능에는 쿠키 기반 세션 선호도, URL 기반 라우팅, 사이트 기반 라우팅 지원 또는 X-Forwarded-* 헤더 재작성 또는 삽입 기능 등이 포함됩니다.

엔드투엔드 TLS 통신 모드로 구성한 경우 Application Gateway가 게이트웨이에서 TLS 세션을 종료하고 사용자 트래픽을 암호 해독합니다. 그런 다음 구성된 규칙을 적용하여 트래픽을 라우팅할 적절한 백 엔드 풀 인스턴스를 선택합니다. 그런 다음, Application Gateway에서 백 엔드 서버에 대한 새로운 TLS 연결을 시작하고, 백 엔드 서버의 공개 키 인증서를 사용하여 데이터를 다시 암호화한 다음, 백 엔드에 해당 요청을 전송합니다. 웹 서버의 모든 응답은 동일한 프로세스를 거쳐 최종 사용자에게 돌아갑니다. 엔드투엔드 TLS는 [백 엔드 HTTP 설정](https://docs.microsoft.com/azure/application-gateway/configuration-overview#http-settings)에서 프로토콜 설정을 HTTPS로 설정하여 활성화된 후에 백 엔드 풀에 적용됩니다.

Application Gateway 및 WAF v1 SKU의 경우 TLS 정책은 프런트 엔드 및 백 엔드 트래픽에 모두 적용됩니다. 프런트 엔드에서 Application Gateway는 서버 역할을 하고 정책을 적용합니다. 백 엔드에서 Application Gateway는 클라이언트 역할을 하고 TLS 핸드셰이크 중에 프로토콜/암호 정보를 기본 설정으로 보냅니다.

Application Gateway 및 WAF v2 SKU의 경우 TLS 정책은 프런트 엔드 트래픽에만 적용되며, 핸드셰이크 중에 특정 암호 및 TLS 버전을 선택할 수 있는 제어가 있는 백 엔드 서버에 모든 암호가 제공됩니다.

Application Gateway는 인증서가 Application Gateway에 나열 되도록 허용 하거나, 잘 알려진 CA 기관에서 인증서를 서명 하 고, 인증서의 CN이 HTTP 백 엔드 설정의 호스트 이름과 일치 하는 백엔드 서버와만 통신 합니다. 여기에는 Azure App Service/Web Apps, Azure API Management와 같은 신뢰할 수 있는 Azure 서비스가 포함됩니다.

백 엔드 풀에 있는 구성원의 인증서가 잘 알려진 CA 기관에 의해 서명되지 않은 경우에는 보안 통신을 허용하도록 엔드투엔드 TLS를 사용하는 백 엔드 풀의 각 인스턴스가 인증서로 구성되어야 합니다. 인증서를 추가하면 Application Gateway가 알려진 백 엔드 인스턴스하고만 통신하게 됩니다. 그러면 엔드투엔드 통신의 보안이 유지됩니다.

> [!NOTE] 
>
> 백 엔드 서버를 인증하기 위해 **백 엔드 HTTP 설정**에 추가된 인증서는 애플리케이션 게이트웨이에서 TLS 종료를 위해 **수신기**에 추가된 인증서와 동일할 수 있고 또는 향상된 보안의 경우 다를 수 있습니다.

![엔드투엔드 TLS 시나리오][1]

이 예제에서는 TLS1.2를 사용하는 요청은 엔드투엔드 TLS를 사용하여 Pool1의 백 엔드 서버에 라우팅됩니다.

## <a name="end-to-end-tls-and-allow-listing-of-certificates"></a>종단 간 TLS 및 인증서 목록 허용

Application Gateway는 응용 프로그램 게이트웨이와 함께 인증서를 나열할 수 있는 알려진 백 엔드 인스턴스와만 통신 합니다. 사용되는 Application Gateway 버전과 관련하여 엔드투엔드 TLS 설치 프로세스에는 몇 가지 차이점이 있습니다. 다음 섹션에서는 이에 대해 개별적으로 설명합니다.

## <a name="end-to-end-tls-with-the-v1-sku"></a>v1 SKU를 사용하는 엔드투엔드 TLS

백 엔드 서버에서 엔드투엔드 TLS를 사용하도록 설정하고 Application Gateway가 백 엔드 서버에 요청을 라우팅하려면 상태 프로브가 성공 상태로 표시되고 정상 응답을 반환해야 합니다.

HTTPS 상태 프로브의 경우 Application Gateway v1 SKU는 HTTP 설정에 업로드할 인증 인증서(루트 인증서가 아닌 백 엔드 서버 인증서의 공개 키)와 정확하게 일치하는 항목을 사용합니다.

그러면 알려진 및 허용 된 백 엔드에 대 한 연결만 허용 됩니다. 나머지 백 엔드는 상태 프로브에 의해 비정상 상태로 간주됩니다. 자체 서명된 인증서는 테스트 용도이며 프로덕션 워크로드에는 권장하지 않습니다. 이러한 인증서는 이전 단계에서 설명한 대로 응용 프로그램 게이트웨이와 함께 나열 되어 있어야 사용할 수 있습니다.

> [!NOTE]
> Azure App Service와 같은 신뢰할 수 있는 Azure 서비스에는 인증 및 신뢰할 수 있는 루트 인증서 설정이 필요하지 않습니다. 해당 서비스는 기본적으로 신뢰할 수 있는 것으로 간주됩니다.

## <a name="end-to-end-tls-with-the-v2-sku"></a>v2 SKU를 사용하는 엔드투엔드 TLS

인증 인증서는 Application Gateway v2 SKU에서 더 이상 사용되지 않으며 신뢰할 수 있는 루트 인증서로 대체되었습니다. 이러한 인증서는 다음과 같은 몇 가지 주요 차이점을 제외하고 인증 인증서와 비슷합니다.

- 해당 CN이 HTTP 백 엔드 설정의 호스트 이름과 일치하는 잘 알려진 CA 기관에서 서명한 인증서의 경우 엔드투엔드 TLS 작동을 위해 추가 단계가 필요하지 않습니다. 

   예를 들어, 백 엔드 인증서가 잘 알려진 CA에서 발급한 것이고 CN이 contoso.com이며, 백 엔드 http 설정의 호스트 필드도 contoso.com으로 설정된 경우 추가 단계가 필요하지 않습니다. 백 엔드 http 설정 프로토콜을 HTTPS로 설정할 수 있고, 상태 프로브 및 데이터 경로 둘 다 TLS를 사용하도록 설정할 수 있습니다. Azure App Service 또는 다른 Azure 웹 서비스를 백 엔드로 사용하는 경우 이러한 항목도 암시적으로 신뢰되며, 엔드투엔드 TLS를 위한 추가 단계가 필요하지 않습니다.
   
> [!NOTE] 
>
> TLS/SSL 인증서를 신뢰할 수 있도록 하려면 알려진 CA에서 백 엔드 서버의 인증서를 발급해야 합니다. 인증서가 신뢰할 수 있는 CA에서 발급되지 않은 경우 Application Gateway는 신뢰할 수 있는 CA를 찾을 때까지(이때 신뢰할 수 있는 보안 연결이 설정됨) 또는 신뢰할 수 있는 CA를 찾을 수 없을 때까지(이때 Application Gateway에서 백 엔드가 비정상으로 표시됨) 해당 인증서가 신뢰할 수 있는 CA에서 발급한 것인지 여부를 확인합니다. 따라서 백 엔드 서버 인증서에 루트 및 중간 CA가 모두 포함되는 것이 좋습니다.

- 인증서가 자체 서명되었거나 알 수 없는 매개체가 서명한 경우 v2 SKU에서 엔드투엔드 TLS를 사용하도록 설정하려면 신뢰할 수 있는 루트 인증서를 정의해야 합니다. Application Gateway는 해당 서버 인증서의 루트 인증서가 풀과 연결된 백 엔드 http 설정의 신뢰할 수 있는 루트 인증서 목록 중 하나와 일치하는 백 엔드와만 통신합니다.

- 루트 인증서 일치 외에, Application Gateway v2는 백 엔드 http 설정에 지정된 호스트 설정이 백 엔드 서버의 TLS/SSL 인증서가 제공하는 CN(공통 이름)의 호스트 설정과 일치하는지도 확인합니다. 백 엔드에 대한 TLS 연결을 설정하려고 하면 Application Gateway v2는 SNI(서버 이름 표시) 확장을 백 엔드 http 설정에 지정된 호스트로 설정합니다.

- 백 엔드 http 설정에서 호스트 필드 대신 **백 엔드 대상에서 호스트 이름 선택** 이 선택 된 경우 SNI 헤더는 항상 백 엔드 풀 FQDN으로 설정 되 고 백 엔드 서버 TLS/SSL 인증서의 CN은 해당 fqdn과 일치 해야 합니다. IP를 사용하는 백 엔드 풀 멤버는 이 시나리오에서 지원되지 않습니다.

- 루트 인증서는 백 엔드 서버 인증서의 base64 인코딩 루트 인증서입니다.

## <a name="sni-differences-in-the-v1-and-v2-sku"></a>v1 및 v2 SKU의 SNI 차이점

앞에서 설명한 것처럼 Application Gateway는 Application Gateway 수신기에서 클라이언트의 TLS 트래픽을 종료하고(프런트 엔드 연결이라고 함), 트래픽을 암호 해독하고, 요청이 전달되어야 하는 백 엔드 서버를 결정하는 데 필요한 규칙을 적용하고, 백 엔드 서버를 사용하여 새 TLS 세션을 설정합니다(백 엔드 연결이라고 함).

다음 표에서는 프런트 엔드 및 백 엔드 연결을 기준으로 v1 및 v2 SKU 간의 SNI 차이를 간략하게 설명합니다.

### <a name="frontend-tls-connection-client-to-application-gateway"></a>프런트 엔드 TLS 연결(클라이언트에서 Application Gateway로)

---
시나리오 | v1 | v2 |
| --- | --- | --- |
| 클라이언트에서 SNI 헤더를 지정하고 모든 다중 사이트 수신기가 "SNI 필요" 플래그를 사용하도록 설정된 경우 | 적절한 인증서를 반환합니다. 사이트가 없는 경우 (server_name에 따라) 연결이 다시 설정됩니다. | 가능한 경우 적절한 인증서를 반환합니다. 그렇지 않으면 구성된 첫 번째 HTTPS 수신기의 인증서를 순서대로 반환합니다.|
| 클라이언트에서 SNI 헤더를 지정하지 않는 경우 및 모든 다중 사이트 헤더가 "SNI 필요"를 사용하도록 설정된 경우 | 연결을 다시 설정합니다. | 구성된 첫 번째 HTTPS 수신기의 인증서를 순서대로 반환합니다.
| 클라이언트에서 SNI 헤더를 지정하지 않는 경우 및 인증서로 구성된 기본 수신기가 있는 경우 | 기본 수신기에 구성된 인증서를 클라이언트에 반환합니다(기본 또는 대체 인증서). | 구성된 첫 번째 HTTPS 수신기의 인증서를 순서대로 반환합니다. |

### <a name="backend-tls-connection-application-gateway-to-the-backend-server"></a>백 엔드 TLS 연결(Application Gateway를 백 엔드 서버에 연결)

#### <a name="for-probe-traffic"></a>프로브 트래픽의 경우

---
시나리오 | v1 | v2 |
| --- | --- | --- |
| FQDN으로 TLS 핸드셰이크 중 SNI(server_name) 헤더 | 백 엔드 풀에서 FQDN으로 설정합니다. [RFC 6066](https://tools.ietf.org/html/rfc6066)에 따라 리터럴 IPv4 및 IPv6 주소는 SNI 호스트 이름에 허용되지 않습니다. <br> **참고:** 백 엔드 풀의 FQDN은 DNS를 백 엔드 서버의 IP 주소(공용 또는 개인)로 확인해야 합니다. | SNI 헤더(server_name)는 HTTP 설정(구성된 경우)에 연결된 사용자 지정 프로브에서 호스트 이름으로 설정됩니다. 그렇지 않으면 HTTP 설정에 언급된 호스트 이름에서 설정되고, 그렇지 않으면 백 엔드 풀에 언급된 FQDN에서 설정됩니다. 우선 순위는 사용자 지정 프로브 > HTTP 설정 > 백 엔드 풀입니다. <br> **참고:** HTTP 설정 및 사용자 지정 프로브에서 구성된 호스트 이름이 다른 경우에는 우선 순위에 따라 SNI가 사용자 지정 프로브에서 호스트 이름으로 설정됩니다.
| 백 엔드 풀 주소가 IP 주소(v1)이거나 사용자 지정 프로브 호스트 이름이 IP 주소(v2)로 구성된 경우 | SNI(server_name)를 설정하지 않습니다. <br> **참고:** 이 경우 백 엔드 서버는 기본/대체 인증서를 반환할 수 있어야 하며,이는 인증 인증서의 HTTP 설정에 나열 되어야 합니다. 백 엔드 서버에 구성된 기본/대체 인증서가 없고 SNI가 필요한 경우 서버에서 연결을 다시 설정하여 프로브 오류가 발생할 수 있습니다. | 앞에서 설명한 우선 순위대로 IP 주소를 호스트 이름으로 사용할 경우 SNI는 [RFC 6066](https://tools.ietf.org/html/rfc6066)에 따라 설정되지 않습니다. <br> **참고:** 사용자 지정 프로브를 구성하지 않고 HTTP 설정 또는 백 엔드 풀에 대해 호스트 이름이 설정되어 있지 않은 경우에도 v2 프로브에서 SNI를 설정하지 않습니다. |

> [!NOTE] 
> 사용자 지정 프로브를 구성 하지 않은 경우에는에서 기본 프로브를 ( \<protocol\> ://127.0.0.1:/) 형식으로 Application Gateway 보냅니다. \<port\> 예를 들어 기본 HTTPS 프로브의 경우 https://127.0.0.1:443/ 으로 전송됩니다. 여기에 언급된 127.0.0.1은 HTTP 호스트 헤더로만 사용되며 RFC 6066에 따라 SNI 헤더로 사용되지 않습니다. 상태 프로브 오류에 대한 자세한 내용은 [백 엔드 상태 문제 해결 가이드](application-gateway-backend-health-troubleshooting.md)를 참조하세요.

#### <a name="for-live-traffic"></a>라이브 트래픽의 경우

---
시나리오 | v1 | v2 |
| --- | --- | --- |
| FQDN으로 TLS 핸드셰이크 중 SNI(server_name) 헤더 | 백 엔드 풀에서 FQDN으로 설정합니다. [RFC 6066](https://tools.ietf.org/html/rfc6066)에 따라 리터럴 IPv4 및 IPv6 주소는 SNI 호스트 이름에 허용되지 않습니다. <br> **참고:** 백 엔드 풀의 FQDN은 DNS를 백 엔드 서버의 IP 주소(공용 또는 개인)로 확인해야 합니다. | SNI 헤더(server_name)는 HTTP 설정에서 호스트 이름으로 설정되고, 그렇지 않으면 *PickHostnameFromBackendAddress* 옵션을 선택하거나 호스트 이름을 언급하지 않으면 백 엔드 풀 구성에서 FQDN으로 설정됩니다.
| 백 엔드 풀 주소가 IP 주소이거나 HTTP 설정에 호스트 이름이 설정되어 있지 않은 경우 | 백 엔드 풀 항목이 FQDN이 아니면 SNI는 [RFC 6066](https://tools.ietf.org/html/rfc6066)에 따라 설정되지 않습니다. | SNI는 클라이언트에서 입력 FQDN의 호스트 이름으로 설정되고 백 엔드 인증서의 CN은 이 호스트 이름과 일치해야 합니다.

## <a name="next-steps"></a>다음 단계

엔드투엔드 TLS에 대해 학습한 후에는 [PowerShell에서 Application Gateway를 사용하여 엔드투엔드 TLS 구성](application-gateway-end-to-end-ssl-powershell.md)으로 이동하고 엔드투엔드 TLS를 사용하는 Application Gateway를 만듭니다.

<!--Image references-->

[1]: ./media/ssl-overview/scenario.png
