---
title: CORS에서 Azure CDN 사용 | Microsoft Docs
description: CORS(크로스-원본 자원 공유)와 함께 CDN(Content Delivery Network)을 사용하는 방법을 알아봅니다.
services: cdn
documentationcenter: ''
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: 86740a96-4269-4060-aba3-a69f00e6f14e
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: 3dbf0aea50f382a0b325bf068a200cde42098733
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60679873"
---
# <a name="using-azure-cdn-with-cors"></a>CORS에서 Azure CDN 사용
## <a name="what-is-cors"></a>CORS의 정의
CORS(크로스 원본 자원 공유)는 특정 도메인에서 실행되는 웹 애플리케이션이 다른 도메인의 자원에 액세스할 수 있도록 하는 HTTP 기능입니다. 사이트 간 스크립팅 공격 가능성을 줄이기 위해 모든 최신 웹 브라우저는 [동일 원본 정책](https://www.w3.org/Security/wiki/Same_Origin_Policy)이라는 보안 제한을 구현합니다.  이 경우 웹 페이지는 다른 도메인의 API를 호출할 수 없습니다.  CORS는 한 도메인(원본 도메인)에서 다른 도메인의 API를 호출할 수 있는 안전한 방법을 제공합니다.

## <a name="how-it-works"></a>작동 방법
CORS 요청에는 *간단한 요청*과 *복잡한 요청*의 두 가지 유형이 있습니다.

### <a name="for-simple-requests"></a>간단한 요청:

1. 브라우저는 추가 **원본** HTTP 요청 헤더를 포함하여 CORS 요청을 보냅니다. 이 헤더의 값은 부모 페이지를 제공한 원본입니다. 이 값은 *프로토콜* *도메인* 및 *포트*로 정의됩니다.  https://www.contoso.com의 페이지가 fabrikam.com 원본의 사용자 데이터에 액세스하려고 하면 다음 요청 헤더가 fabrikam.com으로 전송됩니다.

   `Origin: https:\//www.contoso.com`

2. 서버는 다음으로 응답할 수 있습니다.

   * 허용되는 원본 사이트를 나타내는 응답의 **Access-Control-Allow-Origin** 헤더 예를 들면 다음과 같습니다.

     `Access-Control-Allow-Origin: https://www.contoso.com`

   * 서버가 원본 헤더를 확인한 후 교차 원본 요청을 허용하지 않으면 403과 같은 HTTP 오류 코드 표시

   * 모든 원본을 허용하는 와일드카드를 사용한 **Access-Control-Allow-Origin** 헤더

     `Access-Control-Allow-Origin: *`

### <a name="for-complex-requests"></a>복잡한 요청:

복잡한 요청은 실제 CORS 요청을 전송하기 전에 브라우저가 *실행 전 요청*(즉, 예비 프로브)을 전송해야 하는 CORS 요청입니다. 실행 전 요청은 원래 CORS 요청을 진행할 수 있고 동일한 URL에 대한 `OPTIONS` 요청인 경우에 서버 승인을 요청합니다.

> [!TIP]
> CORS 흐름 및 일반적인 문제에 대한 자세한 내용은 [REST API에 대한 CORS 가이드](https://www.moesif.com/blog/technical/cors/Authoritative-Guide-to-CORS-Cross-Origin-Resource-Sharing-for-REST-APIs/)를 참조하세요.
>
>

## <a name="wildcard-or-single-origin-scenarios"></a>와일드카드 또는 단일 원본 시나리오
Azure CDN의 CORS는 **Access-Control-Allow-Origin** 헤더가 와일드카드(*) 또는 단일 원본으로 설정될 때 추가 구성 없이 자동으로 작동합니다.  CDN은 첫 번째 응답을 캐시하며 후속 요청은 동일한 헤더를 사용하게 됩니다.

원본에 대해 설정된 CORS 이전에 CDN에 대해 이미 요청이 수행된 경우 끝점 콘텐츠의 콘텐츠를 제거하여 **Access-Control-Allow-Origin** 헤더가 있는 콘텐츠를 다시 로드해야 합니다.

## <a name="multiple-origin-scenarios"></a>여러 원본 시나리오
특정 목록의 원본이 CORS에 대해 허용되도록 해야 할 경우 문제가 좀 더 복잡해집니다. CDN이 첫 번째 CORS 원본에 대해 **Access-Control-Allow-Origin** 헤더를 캐시할 때 이러한 문제가 발생합니다.  다른 CORS 원본이 후속 요청을 수행하면 CDN은 캐시된 **Access-Control-Allow-Origin** 를 제공하며 이 경우 일치하지 않게 됩니다.  이를 해결하는 몇 가지 방법이 있습니다.

### <a name="azure-cdn-premium-from-verizon"></a>Verizon의 Azure CDN Premium
이 기능을 사용하도록 설정하는 가장 좋은 방법은 일부 고급 기능을 제공하는 **Verizon의 Azure CDN Premium**을 사용하는 것입니다. 

요청의 [Origin](cdn-rules-engine.md) 헤더를 확인하는 **규칙을 만들어야** 합니다.  유효한 원본인 경우 규칙은 요청에 제공된 원본을 사용하여 **Access-Control-Allow-Origin** 헤더를 설정합니다.  **Origin** 헤더에 지정된 원본이 허용되지 않을 경우 규칙은 **Access-Control-Allow-Origin** 헤더를 생략하며 이로 인해 브라우저가 요청을 거부하게 됩니다. 

규칙 엔진을 사용하여 이러한 작업을 수행하는 방법은 두 가지입니다. 두 경우 모두 파일 원본 서버의 **Access-Control-Allow-Origin** 헤더가 무시되고 CDN의 규칙 엔진이 허용되는 CORS 원본을 완전하게 관리합니다.

#### <a name="one-regular-expression-with-all-valid-origins"></a>유효한 모든 원본을 포함하는 단일 정규식
이 경우 허용하려는 모든 원본을 포함하는 정규식을 만듭니다. 

    https?:\/\/(www\.contoso\.com|contoso\.com|www\.microsoft\.com|microsoft.com\.com)$

> [!TIP]
> **Verizon의 Azure CDN Premium**은 [Perl 호환 정규식](https://pcre.org/)를 정규식에 대한 엔진으로 사용합니다.  [Regular Expressions 101](https://regex101.com/) 과 같은 도구를 사용하여 정규식이 유효한지 검사할 수 있습니다.  "/" 문자는 정규식에서 유효하며 이스케이프할 필요가 없지만 일부 정규식 유효성 검사기에서는 이 문자의 이스케이프를 모범 사례로 간주하고 예상합니다.
> 
> 

정규식이 일치하는 경우 규칙은 원본의 **Access-Control-Allow-Origin** 헤더(있는 경우)를 요청을 전송한 원본으로 바꿉니다.  **Access-Control-Allow-Methods**와 같은 CORS 헤더를 더 추가할 수도 있습니다.

![정규식을 사용하는 규칙 예제](./media/cdn-cors/cdn-cors-regex.png)

#### <a name="request-header-rule-for-each-origin"></a>각 원본에 대한 요청 헤더 규칙입니다.
정규식을 사용하는 대신, **요청 헤더 와일드카드** [일치 조건](/previous-versions/azure/mt757336(v=azure.100)#Anchor_1)을 사용하여 허용하려는 각 원본에 대해 별도의 규칙을 만들 수 있습니다. 정규식 방법을 사용할 때처럼 규칙 엔진은 단독으로 CORS 헤더를 설정합니다. 

![정규식을 사용하지 않는 규칙 예제](./media/cdn-cors/cdn-cors-no-regex.png)

> [!TIP]
> 위 예제에서 와일드카드 문자 *가 사용되었으므로 규칙 엔진은 HTTP 및 HTTPS를 둘 다 일치 항목으로 검색합니다.
> 
> 

### <a name="azure-cdn-standard-profiles"></a>Azure CDN 표준 프로필
Azure CDN 표준 프로필(**Microsoft의 Azure CDN 표준**, **Akamai의 Azure CDN 표준** 및 **Verizon의 Azure CDN 표준**)에서 와일드카드 원본을 사용하지 않고 여러 원본을 사용할 수 있도록 하는 유일한 메커니즘은 [쿼리 문자열 캐싱](cdn-query-string.md)을 사용하는 것입니다. CDN 엔드포인트에 대해 쿼리 문자열 설정을 사용하도록 지정하고 허용된 각 도메인의 요청에 대해 고유한 쿼리 문자열을 사용하도록 설정합니다. 이렇게 하면 CDN은 고유한 각 쿼리 문자열에 대해 별도의 개체를 캐싱합니다. 그렇지만 이 방법은 동일한 파일의 여러 복사본이 CDN에 캐시되므로 이상적이지 않습니다.  

