---
title: Azure CDN을 통해 동적 사이트 가속
description: Azure CDN은 동적 콘텐츠가 포함된 파일에 대한 DSA(동적 사이트 가속) 최적화를 지원합니다.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/25/2019
ms.author: magattus
ms.openlocfilehash: 6bd1d24cdece91265a7355678ea2bc0b0f9e3910
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60768061"
---
# <a name="dynamic-site-acceleration-via-azure-cdn"></a>Azure CDN을 통해 동적 사이트 가속

소셜 미디어소셜 미디어, 전자 상거래 및 하이퍼 개인 설정 웹의 폭발적 증가로 인해 최종 사용자에게 제공되는 콘텐츠의 증가율도 실시간으로 빠르게 성장합니다. 사용자는 브라우저, 위치, 디바이스 또는 네트워크와 독립된 빠르고 안정적인 개인 설정 웹 환경을 기대합니다. 그러나 이러한 환경을 구성하는 매우 혁신적인 기능도 페이지 다운로드 속도를 저하시키고 소비자 환경의 품질을 위험에 노출합니다. 

표준 CDN(콘텐츠 배달 네트워크) 기능에는 고정 파일의 배달 속도를 향상시키기 위해 파일을 최종 사용자에게 가깝게 캐시하는 기능이 포함됩니다. 그러나 동적 웹 애플리케이션에서 서버가 사용자 동작에 대한 응답으로 콘텐츠를 생성하기 때문에 에지 위치에 해당 콘텐츠를 캐싱할 수 없습니다. 이러한 콘텐츠의 배달 속도를 향상시키는 작업은 기존의 에지 캐싱보다 더 복잡하고 배달할 전체 데이터 경로에 있는 각 요소를 처음부터 세밀하게 조정하는 종단 간 솔루션이 필요합니다. Azure CDN DSA(동적 사이트 가속) 최적화를 사용하여 동적 콘텐츠가 포함된 웹 페이지의 성능이 크게 향상되었습니다.

**Akamai의 Azure CDN** 및 **Verizon의 Azure CDN**은 끝점을 만드는 동안 **최적화 목표** 메뉴를 통해 DSA 최적화를 제공합니다. Microsoft의 동적 사이트 가속을 통해 제공 됩니다 [Azure 프런트 도어 서비스](https://docs.microsoft.com/azure/frontdoor/front-door-overview)합니다.

> [!Important]
> **Akamai의 Azure CDN** 프로필의 경우 CDN 엔드포인트를 만든 후에 최적화를 변경할 수 있습니다.
>   
> **Verizon의 Azure CDN** 프로필의 경우 CDN 엔드포인트를 만든 후에 최적화를 변경할 수 없습니다.

## <a name="cdn-endpoint-configuration-to-accelerate-delivery-of-dynamic-files"></a>동적 파일 배달을 가속화하도록 CDN 엔드포인트 구성

동적 파일 배달을 최적화하도록 CDN 엔드포인트를 구성하려면 Azure Portal, REST API 또는 클라이언트 SDK 중 하나를 사용하여 프로그래밍 방식으로 동일한 작업을 수행하면 됩니다. 

**Azure Portal을 사용하여 DSA 최적화를 위해 CDN 엔드포인트를 구성하려면:**

1. **CDN 프로필** 페이지에서 **엔드포인트**를 선택합니다.

   ![새 CDN 엔드포인트 추가](./media/cdn-dynamic-site-acceleration/cdn-endpoint-profile.png) 

   **엔드포인트 추가** 창이 나타납니다.

2. **최적화 목표**에서 **동적 사이트 가속**을 선택합니다.

    ![DSA를 사용하여 새 CDN 엔드포인트 만들기](./media/cdn-dynamic-site-acceleration/cdn-endpoint-dsa.png)

3. **프로브 경로**의 경우 파일에 올바른 경로를 입력합니다.

    프로브 경로는 DSA에 지정된 기능이고 생성을 위해 유효한 경로가 필요합니다. DSA는 원본 서버에 위치한 작은 *프로브 경로* 파일을 사용하여 CDN에 대한 네트워크 라우팅 구성을 최적화합니다. 프로브 경로 파일의 경우 사이트에 샘플 파일을 다운로드하고 업로드하거나 크기가 약 10KB인 원본의 기존 자산을 사용할 수 있습니다.

4. 다른 필수 엔드포인트 옵션(자세한 내용은 [새 CDN 엔드포인트 만들기](cdn-create-new-endpoint.md#create-a-new-cdn-endpoint) 참조)을 입력한 다음, **추가**를 선택합니다.

   CDN 엔드포인트를 만든 후에 특정 조건에 맞는 모든 파일에 대해 DSA 최적화를 적용합니다. 


**DSA에 대한 기존 엔드포인트를 구성하려면(Akamai의 Azure CDN 프로필만 해당):**

1. **CDN 프로필** 페이지에서 수정하려는 CDN 엔드포인트를 선택합니다.

2. 왼쪽 창에서 **최적화**를 선택합니다. 

   **최적화** 페이지가 나타납니다.

3. **최적화 목표**에서 **동적 사이트 가속**을 선택한 다음, **저장**을 선택합니다.

> [!Note]
> DSA 사용 시 별도 요금이 발생합니다. 자세한 정보는 [Content Delivery Network 가격 책정](https://azure.microsoft.com/pricing/details/cdn/)을 참조하세요.

## <a name="dsa-optimization-using-azure-cdn"></a>Azure CDN을 사용하여 DSA 최적화

Azure CDN의 동적 사이트 가속은 다음 기술을 사용하여 동적 자산 배달 속도를 향상시킵니다.

-   [경로 최적화](#route-optimization)
-   [TCP 최적화](#tcp-optimizations)
-   [개체 사전 인출(Akamai의 Azure CDN에만 해당)](#object-prefetch-azure-cdn-from-akamai-only)
-   [적응 이미지 압축(Akamai의 Azure CDN에만 해당)](#adaptive-image-compression-azure-cdn-from-akamai-only)

### <a name="route-optimization"></a>경로 최적화

인터넷이 매우 동적이기 때문에 경로 최적화가 중요합니다. 여기서 트래픽 및 일시적인 중단은 지속적으로 네트워크 토폴로지를 변경합니다. BGP(경계 게이트웨이 프로토콜)는 인터넷의 라우팅 프로토콜이지만 중계 PoP(Point of Presence) 서버를 통한 더 빠른 경로가 있을 수 있습니다. 

경로 최적화는 사이트에 계속 액세스할 수 있고 동적 콘텐츠를 가능한 가장 빠르고 가장 안정적인 경로를 통해 최종 사용자에게 배달하도록 원본에 최적인 경로를 선택합니다. 

Akamai 네트워크는 원본과 CDN 에지 간에 가장 빠른 경로를 결정하기 위해 오픈 인터넷에 기본 BGP 경로를 사용할 뿐만 아니라 실시간 데이터를 수집하고 Akamai 서버에서 다른 노드를 통한 다양한 경로를 비교하는 기술을 사용합니다. 이러한 기술은 인터넷 정체 지점과 긴 경로를 회피합니다. 

마찬가지로, Verizon 네트워크는 Anycast DNS, 고용량 지원 PoP 및 상태 점검 조합을 사용하여 클라이언트에서 원본까지 최상의 경로 데이터에 대한 최적의 게이트웨이를 결정합니다.
 
결과적으로 완전히 동적인 트랜잭션 콘텐츠를 캐시할 수 없는 경우에도 보다 빠르고 안정적으로 최종 사용자에게 전달할 수 있습니다. 

### <a name="tcp-optimizations"></a>TCP 최적화

TCP(Transmission Control Protocol)는 IP 네트워크의 애플리케이션 간에 정보를 전달하는 데 사용되는 인터넷 프로토콜 모음의 표준입니다.  기본적으로 대규모의 비효율성이 발생하는 네트워크 정체를 방지하는 제한뿐만 아니라 TCP 연결을 설정하는 데 몇 가지 요청이 필요합니다. **Akamai의 Azure CDN**은 3개의 영역을 최적화하여 이 문제를 다룹니다. 

 - [TCP 느린 시작 제거](#eliminating-tcp-slow-start)
 - [영구 연결 활용](#leveraging-persistent-connections)
 - [TCP 패킷 매개 변수 조정](#tuning-tcp-packet-parameters)

#### <a name="eliminating-tcp-slow-start"></a>TCP 느린 시작 제거

TCP *느린 시작*은 네트워크를 통해 전송되는 데이터 양을 제한하여 네트워크 정체를 방지하는 TCP 프로토콜의 알고리즘입니다. 이 기능은 최대값에 도달하거나 패킷 손실이 감지될 때까지 먼저 발신자와 수신자 사이의 작은 정체 창 크기로 시작합니다.

 **Akamai의 Azure CDN** 및 **Verizon의 Azure CDN**은 모두 다음 세 가지 단계에서 TCP 느린 시작을 제거합니다.

1. 상태 및 대역폭 모니터링을 사용하여 에지 PoP 서버 간 연결의 대역폭을 측정합니다.
    
2. 에지 PoP 서버 간에 메트릭이 공유되므로 서버는 주위의 다른 PoP의 네트워크 조건 및 서버 상태에 대해 알게 됩니다.  
    
3. CDN 에지 서버는 근접한 다른 CDN 에지 서버와 통신할 때 최적의 창 크기와 같은 일부 전송 매개 변수에 대해 가정합니다. 이 단계는 CDN 에지 서버 간의 연결 상태가 더 많은 패킷의 데이터를 전송할 수 있는 경우 초기 정체 창 크기를 늘릴 수 있음을 의미합니다.  

#### <a name="leveraging-persistent-connections"></a>영구 연결 활용

CDN을 사용하면 원본에 직접 연결된 사용자와 비교하여 원본 서버에 연결된 고유한 컴퓨터 수가 줄어듭니다. 또한 Azure CDN은 사용자 요청을 함께 풀링하여 원본과 연결 수를 줄입니다.

위에서 언급한 대로 몇몇 핸드셰이크 요청은 TCP 연결을 설정해야 합니다. `Keep-Alive` HTTP 헤더에서 구현하는 영구 연결은 여러 HTTP 요청에 기존 TCP 연결을 다시 사용하여 왕복 시간을 저장하고 배달 속도를 향상시킵니다. 

또한 **Verizon의 Azure CDN**은 TCP 연결을 통해 정기적인 연결 유지 패킷을 보내서 열린 연결이 닫히지 않게 방지합니다.

#### <a name="tuning-tcp-packet-parameters"></a>TCP 패킷 매개 변수 조정

또한 **Akamai의 Azure CDN**은 다음 기술을 사용하여 서버 간 연결을 제어하는 매개 변수를 조정하고 사이트에 포함된 콘텐츠를 검색하는 데 필요한 긴 장거리 왕복 시간을 감소시킵니다.

- 초기 정체 창을 증가시키면 승인을 기다리지 않고 더 패킷을 이동할 수 있습니다.
- 초기 재전송 시간 제한을 감소시키면 손실을 감지하고 재전송을 더 빠르게 수행할 수 있습니다.
- 전송 중에 손실된 패킷을 가정하기 전에 대기 시간을 줄이기 위해 최소 및 최대 재전송 시간 제한을 감소시킵니다.

### <a name="object-prefetch-azure-cdn-from-akamai-only"></a>개체 사전 인출(Akamai의 Azure CDN에만 해당)

대부분의 웹 사이트는 이미지 및 스크립트와 같은 다양한 리소스를 참조하는 HTML 페이지로 구성됩니다. 일반적으로 클라이언트가 웹 페이지를 요청하면 브라우저는 먼저 HTML 개체를 다운로드하고 구문을 분석한 다음 전체 페이지를 로드하는 데 필요한 연결된 자산을 추가로 요청합니다. 

*사전 인출*은 HTML이 브라우저에 제공되는 반면 브라우저에서 이러한 개체를 요청하기 전에 HTML 페이지에 포함된 이미지 및 스크립트를 검색하는 기술입니다. 

사전 인출 옵션을 사용하면 CDN이 HTML 기반 페이지를 클라이언트의 브라우저에 제공하는 시점에 켜집니다. CDN은 HTML 파일의 구문을 분석하고, 연결된 리소스를 추가로 요청하고, 해당 캐시에 저장합니다. 클라이언트에서 연결된 자산을 요청하는 경우 CDN 에지 서버에는 이미 요청된 개체가 있으므로 원본에 왕복하지 않고 즉시 제공할 수 있습니다. 이 최적화는 캐시할 수 있는 콘텐츠 및 캐시할 수 없는 콘텐츠에 모두 제공됩니다.

### <a name="adaptive-image-compression-azure-cdn-from-akamai-only"></a>적응 이미지 압축(Akamai의 Azure CDN에만 해당)

특히 모바일과 같은 일부 디바이스는 때때로 네트워크 속도가 저하되는 경우가 있습니다. 이러한 시나리오에서는 사용자가 고해상도 이미지를 오래 기다리지 않고 해당 웹 페이지에서 작은 이미지를 신속하게 수신할 수 있다는 장점이 있습니다.

이 기능은 자동으로 네트워크 품질을 모니터링하여 네트워크가 느려지는 경우 배달 속도를 개선하기 위해 표준 JPEG 압축 메서드를 사용합니다.

적응 이미지 압축 | 파일 확장명  
--- | ---  
JPEG 압축 | .jpg, .jpeg, .jpe, .jig, .jgig, .jgi

## <a name="caching"></a>구성

DSA를 사용하면 원본이 응답에서 `Cache-Control` 또는 `Expires` 헤더를 포함하는 경우에도 캐싱이 CDN에서 기본적으로 해제됩니다. DSA는 각 클라이언트에 고유하기 때문에 캐시되지 않아야 하는 동적 자산에 일반적으로 사용됩니다. 캐싱은 이 동작을 손상시킬 수 있습니다.

고정 자산과 동적 자산이 혼합된 웹 사이트가 있는 경우 최상의 성능을 얻기 위해서는 하이브리드 접근 방식을 취하는 것이 가장 좋습니다. 

**Verizon의 Azure CDN 표준** 및 **Akamai의 Azure CDN 표준** 프로필의 경우, [캐싱 규칙](cdn-caching-rules.md)을 사용하여 특정 DSA 엔드포인트에 대한 캐싱을 설정할 수 있습니다.

캐싱 규칙에 액세스하려면

1. **CDN 프로필** 페이지의 설정 아래에서 **캐싱 규칙**을 선택합니다.  
    
    ![CDN 캐싱 규칙 단추](./media/cdn-dynamic-site-acceleration/cdn-caching-rules-btn.png)

    **캐싱 규칙** 페이지가 열립니다.

2. DSA 엔드포인트에 대한 캐싱을 설정하려면 전역 또는 사용자 지정 캐싱 규칙을 만듭니다. 

**Verizon의 Azure CDN 프리미엄** 프로필만 [규칙 엔진](cdn-rules-engine.md)을 사용하여 특정 DSA 엔드포인트에 대한 캐싱을 설정합니다. 생성되는 모든 규칙은 DSA에 최적화된 프로필의 해당 엔드포인트에만 영향을 줍니다. 

규칙 엔진에 액세스하려면
    
1. **CDN 프로필** 페이지에서 **관리**를 선택합니다.  
    
    ![CDN 프로필 관리 단추](./media/cdn-dynamic-site-acceleration/cdn-manage-btn.png)

    CDN 관리 포털이 열립니다.

2. CDN 관리 포털에서 **ADN**을 선택한 다음, **규칙 엔진**을 선택합니다. 

    ![DSA에 대한 규칙 엔진](./media/cdn-dynamic-site-acceleration/cdn-dsa-rules-engine.png)



또는 동적 자산을 제공하는 DSA를 사용하여 최적화된 엔드포인트와 일반 웹 배달 등의 고정 최적화 형식을 사용하여 최적화된 다른 엔드포인트 등 두 개의 CDN 엔드포인트를 사용하여 캐싱 가능한 자산을 제공할 수 있습니다. 웹 페이지 URL을 수정하여 사용하려는 CDN 엔드포인트의 자산에 직접 연결합니다. 

예: `mydynamic.azureedge.net/index.html`은 동적 페이지이고 DSA 엔드포인트에서 로드됩니다.  html 페이지는 `mystatic.azureedge.net/banner.jpg` 및 `mystatic.azureedge.net/scripts.js` 등의 정적 CDN 엔드포인트에서 로드한 JavaScript 라이브러리 또는 이미지와 같은 여러 정적 자산을 참조합니다. 



