---
title: Azure CDN 규칙 엔진의 X-EC-Debug HTTP 헤더 | Microsoft Docs
description: X-EC-Debug 디버그 캐시 요청 헤더는 요청된 자산에 적용되는 캐시 정책에 대한 추가 정보를 제공합니다. 이 헤더는 Verizon에만 해당됩니다.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/12/2018
ms.author: magattus
ms.openlocfilehash: 4ba42850ee28e2e212d9bc2b7b64be103218757c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60736975"
---
# <a name="x-ec-debug-http-headers-for-azure-cdn-rules-engine"></a>Azure CDN 규칙 엔진의 X-EC-Debug HTTP 헤더
`X-EC-Debug` 디버그 캐시 요청 헤더는 요청된 자산에 적용되는 캐시 정책에 대한 추가 정보를 제공합니다. 이러한 헤더는 **Verizon의 Azure CDN Premium** 제품에만 해당됩니다.

## <a name="usage"></a>사용 현황
POP 서버에서 사용자에게 보내는 응답에는 다음 조건이 충족되는 경우에만 `X-EC-Debug` 헤더가 포함됩니다.

- [디버그 캐시 응답 헤더 기능](cdn-rules-engine-reference-features.md#debug-cache-response-headers)은 지정된 요청에서 활성화됩니다.
- 지정된 요청은 응답에 포함될 디버그 캐시 응답 헤더 집합을 정의합니다.

## <a name="requesting-debug-cache-information"></a>디버그 캐시 정보 요청
지정된 요청에 다음 지시문을 사용하여 응답에 포함될 디버그 캐시 정보를 정의합니다.

요청 헤더 | 설명 |
---------------|-------------|
X-EC-Debug: x-ec-cache | [캐시 상태 코드](#cache-status-code-information)
X-EC-Debug: x-ec-cache-remote | [캐시 상태 코드](#cache-status-code-information)
X-EC-Debug: x-ec-check-cacheable | [캐시 가능](#cacheable-response-header)
X-EC-Debug: x-ec-cache-key | [캐시 키](#cache-key-response-header)
X-EC-Debug: x-ec-cache-state | [캐시 상태](#cache-state-response-header)

### <a name="syntax"></a>구문

디버그 캐시 응답 헤더는 요청에 다음 헤더 및 지정된 지시문을 포함하여 요청할 수 있습니다.

`X-EC-Debug: Directive1,Directive2,DirectiveN`

### <a name="sample-x-ec-debug-header"></a>X-EC-Debug 헤더 샘플

`X-EC-Debug: x-ec-cache,x-ec-check-cacheable,x-ec-cache-key,x-ec-cache-state`

## <a name="cache-status-code-information"></a>캐시 상태 코드 정보
X-EC-Debug 응답 헤더는 다음 지시문을 통해 서버 및 응답을 처리하는 방법을 식별할 수 있습니다.

헤더 | 설명
-------|------------
X-EC-Debug: x-ec-cache | 콘텐츠가 CDN을 통해 라우팅될 때마다 이 헤더가 보고됩니다. 요청을 수행한 POP 서버를 식별합니다.
X-EC-Debug: x-ec-cache-remote | 요청된 콘텐츠가 원본 실드 서버 또는 ADN 게이트웨이 서버에 캐시된 경우에만 이 헤더가 보고됩니다.

### <a name="response-header-format"></a>응답 헤더 형식

X-EC-Debug 헤더는 캐시 상태 코드 정보를 다음과 같은 형식으로 보고합니다.

- `X-EC-Debug: x-ec-cache: <StatusCode from Platform (POP/ID)>`

- `X-EC-Debug: x-ec-cache-remote: <StatusCode from Platform (POP/ID)>`

위의 응답 헤더 구문에 사용된 용어는 다음과 같이 정의됩니다.
- StatusCode: Cdn에서 캐시 상태 코드를 통해 표현 되는 요청 된 콘텐츠를 처리 하는 방법을 나타냅니다.
    
    TCP_DENIED 상태 코드는 토큰 기반 인증에 따라 권한이 없는 요청이 거부될 때 NONE 대신 보고될 수 있습니다. 그러나 캐시 상태 보고서 또는 원시 로그 데이터를 볼 때에는 NONE 상태 코드가 계속 사용됩니다.

- 플랫폼: 콘텐츠가 요청 된 플랫폼을 나타냅니다. 이 필드에 유효한 코드는 다음과 같습니다.

    코드  | 플랫폼
    ------| --------
    ECAcc | HTTP Large
    ECS   | HTTP Small
    ECD   | ADN(애플리케이션 전달 네트워크)

- POP: 나타냅니다 합니다 [POP](cdn-pop-abbreviations.md) 요청을 처리 하는 합니다. 

### <a name="sample-response-headers"></a>응답 헤더 샘플

요청에 대한 캐시 상태 코드 정보를 제공하는 헤더 샘플은 다음과 같습니다.

- `X-EC-Debug: x-ec-cache: TCP_HIT from ECD (lga/0FE8)`

- `X-EC-Debug: x-ec-cache-remote: TCP_HIT from ECD (dca/EF00)`

## <a name="cacheable-response-header"></a>캐시 가능 응답 헤더
`X-EC-Debug: x-ec-check-cacheable` 응답 헤더는 요청된 콘텐츠를 캐시할 수 있었는지 여부를 나타냅니다.

이 응답 헤더는 캐싱이 발생했는지 여부를 나타내지 않고, 오히려 요청이 캐싱에 적합했는지 여부를 나타냅니다.

### <a name="response-header-format"></a>응답 헤더 형식

요청이 캐시될 수 있었는지 여부를 보고하는 `X-EC-Debug` 응답 헤더의 형식은 다음과 같습니다.

`X-EC-Debug: x-ec-check-cacheable: <cacheable status>`

위의 응답 헤더 구문에 사용된 용어는 다음과 같이 정의됩니다.

값  | 설명
-------| --------
예    | 요청된 콘텐츠가 캐싱에 적합했음을 나타냅니다.
아니요     | 요청된 콘텐츠가 캐싱에 적합하지 않았음을 나타냅니다. 이 상태는 다음 이유 중 하나로 인해 발생할 수 있습니다. <br /> -고객별 구성: 계정에 특정 구성에서 자산을 캐시 pop 서버를 방지할 수 있습니다. 예를 들어 규칙 엔진은 인증 요청에 대한 캐시 무시 기능을 사용하도록 설정하여 자산을 캐시하지 못하도록 방지할 수 있습니다.<br /> -캐시 응답 헤더: 요청 된 자산의 Cache-control 및 Expires 헤더에서 자산을 캐시 POP 서버를 방지할 수 있습니다.
UNKNOWN | 서버에서 요청된 자산을 캐시할 수 있는지 여부를 평가할 수 없었음을 나타냅니다. 이 상태는 일반적으로 토큰 기반 인증에 따라 요청이 거부될 때 발생합니다.

### <a name="sample-response-header"></a>응답 헤더 샘플

다음 응답 헤더 샘플은 요청된 콘텐츠를 캐시할 수 있었는지 여부를 나타냅니다.

`X-EC-Debug: x-ec-check-cacheable: YES`

## <a name="cache-key-response-header"></a>캐시 키 응답 헤더
`X-EC-Debug: x-ec-cache-key` 응답 헤더는 요청된 콘텐츠와 연결된 실제 캐시 키를 나타냅니다. 실제 캐시 키는 캐싱을 위해 자산을 식별하는 경로로 구성됩니다. 즉 서버에서 cache-key에 정의된 경로에 따라 자산의 캐시된 버전을 확인합니다.

이 실제 캐시 키는 이중 슬래시(//)로 시작하고, 그 뒤에 콘텐츠(HTTP 또는 HTTPS)를 요청하는 데 사용되는 프로토콜이 나옵니다. 이 프로토콜 뒤에는 콘텐츠 액세스 지점(예: _/000001/_)으로 시작하는 요청된 자산에 대한 상대 경로가 나옵니다.

기본적으로 HTTP 플랫폼은 *표준 캐시*를 사용하도록 구성됩니다. 즉, 캐싱 메커니즘에서 쿼리 문자열이 무시됩니다. 이러한 유형의 구성은 캐시 키에서 쿼리 문자열 데이터를 포함하지 못하도록 방지합니다.

쿼리 문자열이 캐시 키에 기록되면 해당 해시 값으로 변환된 다음, 요청된 자산의 이름과 해당 파일 확장명 사이에 삽입됩니다(예: asset&lt;hash value&gt;.html).

### <a name="response-header-format"></a>응답 헤더 형식

`X-EC-Debug` 응답 헤더는 실제 캐시 키 정보를 다음과 같은 형식으로 보고합니다.

`X-EC-Debug: x-ec-cache-key: CacheKey`

### <a name="sample-response-header"></a>응답 헤더 샘플

다음 응답 헤더 샘플은 요청된 콘텐츠에 대한 실제 캐시 키를 나타냅니다.

`X-EC-Debug: x-ec-cache-key: //http/800001/origin/images/foo.jpg`

## <a name="cache-state-response-header"></a>캐시 상태 응답 헤더
`X-EC-Debug: x-ec-cache-state` 응답 헤더는 요청된 콘텐츠의 캐시 상태를 요청된 시간에 나타냅니다.

### <a name="response-header-format"></a>응답 헤더 형식

`X-EC-Debug` 응답 헤더는 캐시 상태 정보를 다음과 같은 형식으로 보고합니다.

`X-EC-Debug: x-ec-cache-state: max-age=MASeconds (MATimePeriod); cache-ts=UnixTime (ddd, dd MMM yyyy HH:mm:ss GMT); cache-age=CASeconds (CATimePeriod); remaining-ttl=RTSeconds (RTTimePeriod); expires-delta=ExpiresSeconds`

위의 응답 헤더 구문에 사용된 용어는 다음과 같이 정의됩니다.

- MASeconds: 요청된 된 콘텐츠의 Cache-control 헤더에 의해 정의 된 max-age 초 단위로 나타냅니다.

- MATimePeriod: 최대 처리 기간 값 (즉 MASeconds)을 더 큰 단위 (예를 들어, 일)으로 변환합니다. 

- UnixTime: Unix 시간에서 (즉, 요청 된 콘텐츠의 캐시 타임 스탬프를 나타냅니다. 또는 Unix Epoch라고도 함)으로 나타냅니다. 캐시 타임스탬프는 자산의 TTL이 계산되는 시작 날짜/시간을 나타냅니다. 

    원본 서버에서 타사 HTTP 캐싱 서버를 활용하지 않거나 해당 서버에서 Age 응답 헤더를 반환하지 않는 경우, 캐시 타임스탬프는 항상 자산을 검색하거나 유효성을 다시 검사한 날짜/시간이 됩니다. 이 고, 그렇지 POP 서버는 자산의 TTL을 다음과 같이 계산 필드를 사용 합니다. Retrieval/RevalidateDateTime - Age.

- ddd, dd MMM yyyy HH:mm:ss GMT: 요청 된 콘텐츠의 캐시 타임 스탬프를 나타냅니다. 자세한 내용은 위의 UnixTime 용어를 참조하세요.

- CASeconds: 캐시 타임 스탬프 이후 경과 된 시간 (초) 수를 나타냅니다.

- RTSeconds: 캐시 된 콘텐츠를 간주 되는 남은 시간 (초) 수를 나타냅니다. 이 값은 다음과 같이 계산 됩니다. RTSeconds = 최대 처리 기간-캐시 처리 기간입니다.

- RTTimePeriod: 나머지 TTL 값 (즉 RTSeconds)을 더 큰 단위 (예를 들어, 일)으로 변환합니다.

- ExpiresSeconds: 에 지정 된 날짜/시간까지 남은 시간 (초) 수를 나타냅니다는 `Expires` 응답 헤더입니다. 응답에 `Expires` 응답 헤더가 포함되지 않은 경우 이 용어의 값은 *none*입니다.

### <a name="sample-response-header"></a>응답 헤더 샘플

다음 응답 헤더 샘플은 요청된 콘텐츠의 캐시 상태를 요청된 시간에 나타냅니다.

```X-EC-Debug: x-ec-cache-state: max-age=604800 (7d); cache-ts=1341802519 (Mon, 09 Jul 2012 02:55:19 GMT); cache-age=0 (0s); remaining-ttl=604800 (7d); expires-delta=none```

