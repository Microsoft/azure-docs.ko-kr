---
title: Azure Front Door Service - 회람 규칙 매칭 모니터링 | Microsoft Docs
description: 이 문서에서는 Azure Front Door Service가 수신 요청에 사용할 회람 규칙을 매칭하는 방법을 설명합니다.
services: front-door
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: sharadag
ms.openlocfilehash: eec99bde0ea73a99a9dc1345f938b821a95a7c05
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60736289"
---
# <a name="how-front-door-matches-requests-to-a-routing-rule"></a>Front Door에서 회람 규칙에 요청을 매칭하는 방법

연결을 설정하고 SSL 핸드셰이크를 수행한 후 Front Door 환경에 요청이 도달했을 때 Front Door가 수행하는 첫 번째 작업은 모든 구성에서 요청을 매칭할 특정 회람 규칙을 확인한 후 정의된 작업을 수행하는 것입니다. 다음 문서에서는 Front Door가 HTTP 요청을 처리할 때 사용할 경로 구성을 결정하는 방법을 설명합니다.

## <a name="structure-of-a-front-door-route-configuration"></a>Front Door 경로 구성의 구조
Front Door 회람 규칙 구성은 크게 "왼쪽"과 "오른쪽" 부분으로 구성됩니다. 들어오는 요청은 경로의 왼쪽에 매칭되고, 오른쪽은 요청을 처리하는 방법을 정의합니다.

### <a name="incoming-match-left-hand-side"></a>들어오는 매칭(왼쪽)
다음은 들어오는 요청이 회람 규칙(또는 왼쪽)과 매칭되는지 여부를 결정하는 속성입니다.

* **HTTP 프로토콜**(HTTP/HTTPS)
* **호스트** (예: www\.foo.com, \*. bar.com)
* **경로**(예: /\*, /users/\*, /file.gif)

이러한 속성은 각 프로토콜/호스트/경로 조합이 가능한 매칭 집합이 될 수 있도록 내부적으로 확장됩니다.

### <a name="route-data-right-hand-side"></a>경로 데이터(오른쪽)
요청을 처리하는 방법은 특정 경로에 캐싱을 사용할지 여부에 따라 달라집니다. 따라서 요청에 캐시된 응답이 없는 경우 구성된 백 엔드 풀의 적절한 백 엔드에 요청이 전달됩니다.

## <a name="route-matching"></a>경로 매칭
이 섹션에서는 지정된 Front Door 회람 규칙에 매칭하는 방법을 중점적으로 설명합니다. 기본 개념은 항상 "왼쪽"만 보면서 **가장 구체적인 첫 번째 매칭**에 매칭하는 것입니다.  HTTP 프로토콜, 프런트 엔드 호스트, 경로 순서로 매칭합니다.

### <a name="frontend-host-matching"></a>프런트 엔드 호스트 매칭
프런트 엔드 호스트를 매칭할 때는 다음과 같은 논리를 사용합니다.

1. 정확히 일치하는 호스트가 있는 회람 규칙을 찾습니다.
2. 정확히 일치하는 프런트 엔드 호스트가 없을 경우 요청을 거부하고 400 잘못된 요청 오류를 보냅니다.

이 프로세스를 자세히 설명하기 위해 Front Door 경로(왼쪽만 해당)의 구성 예제를 살펴보겠습니다.

| 라우팅 규칙 | 프런트 엔드 호스트 | path |
|-------|--------------------|-------|
| A | foo.contoso.com | /\* |
| b | foo.contoso.com | /users/\* |
| C | www\.fabrikam.com, foo.adventure-works.com  | /\*, /images/\* |

Front Door에 다음과 같은 요청이 전송된 경우, 이러한 요청은 위의 회람 규칙 중 다름 규칙과 일치합니다.

| 수신 프런트 엔드 호스트 | 일치하는 회람 규칙 |
|---------------------|---------------|
| foo.contoso.com | A, B |
| www\.fabrikam.com | C |
| images.fabrikam.com | 오류 400 표시: 잘못된 요청 |
| foo.adventure-works.com | C |
| contoso.com | 오류 400 표시: 잘못된 요청 |
| www\.adventure-works.com | 오류 400 표시: 잘못된 요청 |
| www\.northwindtraders.com | 오류 400 표시: 잘못된 요청 |

### <a name="path-matching"></a>경로 매칭
특정 프런트 엔드 호스트를 확인하고 해당 프런트 엔드 호스트를 사용하여 가능한 회람 규칙을 경로에만 필터링하면 Front Door에서 요청 경로에 따라 회람 규칙을 필터링합니다. 프런트 엔드 호스트와 유사한 논리를 사용하겠습니다.

1. 정확히 일치하는 경로가 있는 회람 규칙을 찾습니다.
2. 정확히 일치하는 경로가 없을 경우 일치하는 와일드카드 경로가 있는 회람 규칙을 찾습니다.
3. 라우팅 규칙이 없고 일치 하는 경로 사용 하 여 발견 되 면 요청을 거부 하 고 400을 반환 합니다. 잘못 된 요청 오류 HTTP 응답입니다.

>[!NOTE]
> 와일드카드가 없는 모든 경로는 정확히 일치하는 경로로 간주됩니다. 경로가 슬래시로 끝나는 경우에도 정확한 일치 항목으로 간주됩니다.

자세한 설명을 위해 또 다른 예제 집합을 살펴보겠습니다.

| 라우팅 규칙 | 프런트 엔드 호스트    | path     |
|-------|---------|----------|
| A     | www\.contoso.com | /        |
| b     | www\.contoso.com | /\*      |
| C     | www\.contoso.com | /ab      |
| D     | www\.contoso.com | /abc     |
| E     | www\.contoso.com | /abc/    |
| F     | www\.contoso.com | /abc/\*  |
| G     | www\.contoso.com | /abc/def |
| H     | www\.contoso.com | /path/   |

해당 구성을 고려할 때 다음과 같이 일치하는 테이블 예제의 결과는 다음과 같습니다.

| 들어오는 요청    | 일치하는 경로 |
|---------------------|---------------|
| www\.contoso.com/            | A             |
| www\.contoso.com/a           | b             |
| www\.contoso.com/ab          | C             |
| www\.contoso.com/abc         | D             |
| www\.contoso.com/abzzz       | b             |
| www\.contoso.com/abc/        | E             |
| www\.contoso.com/abc/d       | F             |
| www\.contoso.com/abc/def     | G             |
| www\.contoso.com/abc/defzzz  | F             |
| www\.contoso.com/abc/def/ghi | F             |
| www\.contoso.com/path        | b             |
| www\.contoso.com/path/       | H             |
| www\.contoso.com/path/zzz    | b             |

>[!WARNING]
> </br> 모두 캐치 라우팅 경로(`/*`)와 정확히 일치하는 프런트 엔드 호스트에 대한 회람 규칙이 없을 경우 회람 규칙과 일치하는 항목이 없는 것입니다.
>
> 예제 구성:
>
> | 라우팅 | 호스트             | path    |
> |-------|------------------|---------|
> | A     | profile.contoso.com | /api/\* |
>
> 일치하는 테이블:
>
> | 들어오는 요청       | 일치하는 경로 |
> |------------------------|---------------|
> | profile.domain.com/other | 없음. 오류 400 표시: 잘못된 요청 |

### <a name="routing-decision"></a>라우팅 의사 결정
단일 Front Door 회람 규칙에 대한 매칭이 완료되면 요청을 처리하는 방법을 선택해야 합니다. Front Door는 일치하는 회람 규칙에 사용 가능한 캐시된 응답이 있을 경우 동일한 응답을 클라이언트로 다시 제공합니다. 그렇지 않을 경우 일치하는 회람 규칙에 [URL 재작성(사용자 지정 전달 경로)](front-door-url-rewrite.md)이 구성되어 있는지 여부가 평가됩니다. 사용자 지정 전달 경로가 정의되어 있지 않을 경우 구성된 백 엔드 풀의 적절한 백 엔드로 요청이 그대로 전달됩니다. 또는 정의된 [사용자 지정 전달 규칙](front-door-url-rewrite.md)에 따라 요청 경로가 업데이트된 후 백 엔드로 전달됩니다.

## <a name="next-steps"></a>다음 단계

- [Front Door를 만드는 방법](quickstart-create-front-door.md)을 알아봅니다.
- [Front Door의 작동 원리](front-door-routing-architecture.md)를 알아봅니다.
