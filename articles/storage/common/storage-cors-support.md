---
title: CORS(Cross-Origin Resource Sharing) 지원 | Microsoft Docs
description: Microsoft Azure Storage 서비스에 대해 CORS 지원을 사용하도록 설정하는 방법을 설명합니다.
services: storage
author: cbrooksmsft
ms.service: storage
ms.devlang: dotnet
ms.topic: article
ms.date: 2/22/2017
ms.author: cbrooks
ms.subservice: common
ms.openlocfilehash: 5e65965678ed042081e4a406d3a207fb7ede299f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61483488"
---
# <a name="cross-origin-resource-sharing-cors-support-for-the-azure-storage-services"></a>Azure Storage 서비스에 대한 CORS(Cross-Origin Resource Sharing) 지원
2013-08-15 버전부터 Azure 저장소 서비스는 Blob, 테이블, 큐 및 파일 서비스에 대해 CORS(원본 간 리소스 공유)를 지원합니다. CORS는 특정 도메인에서 실행되는 웹 애플리케이션이 다른 도메인의 자원에 액세스할 수 있도록 하는 HTTP 기능입니다. 웹 브라우저는 웹 페이지가 다른 도메인의 API를 호출할 수 없도록 하는 [동일 원본 정책](https://www.w3.org/Security/wiki/Same_Origin_Policy)이라는 보안 제한을 구현합니다. CORS는 특정 도메인(원본 도메인)에서 다른 도메인의 API를 호출할 수 있는 안전한 방법을 제공합니다. CORS에 대한 자세한 내용은 [CORS 사양](https://www.w3.org/TR/cors/)을 참조하세요.

[Blob 서비스 속성 설정](https://msdn.microsoft.com/library/hh452235.aspx), [큐 서비스 속성 설정](https://msdn.microsoft.com/library/hh452232.aspx) 및 [테이블 서비스 속성 설정](https://msdn.microsoft.com/library/hh452240.aspx)을 호출하여 각 저장소 서비스에 대해 CORS 규칙을 개별적으로 설정할 수 있습니다. 서비스에 대해 CORS 규칙을 설정하고 나면 다른 도메인에서 해당 서비스에 대해 수행하는 적절하게 권한이 부여된 요청을 평가하여 지정된 규칙에 따라 해당 요청이 허용되는지 여부를 결정합니다.

> [!NOTE]
> CORS는 인증 메커니즘이 아닙니다. CORS를 사용하도록 설정한 상태에서 저장소 리소스에 대해 수행하는 모든 요청은 적절한 인증 서명을 포함하거나 공용 자원에 대한 요청이어야 합니다.
> 
> 

## <a name="understanding-cors-requests"></a>CORS 요청 이해
원본 도메인의 CORS 요청은 두 가지 개별 요청으로 구성될 수 있습니다.

* 서비스에서 적용하는 CORS 제한을 쿼리하는 실행 전 요청. 요청 메서드가 GET, HEAD, POST 등의 [단순 메서드](https://www.w3.org/TR/cors/)가 아니면 실행 전 요청을 수행해야 합니다.
* 원하는 자원에 대해 수행될 실제 요청

### <a name="preflight-request"></a>실행 전 요청
실행 전 요청에서는 계정 소유자가 저장소 서비스에 대해 설정한 CORS 제한을 쿼리합니다. 웹 브라우저 또는 기타 사용자 에이전트는 요청 헤더, 메서드 및 원본 도메인이 포함된 OPTIONS 요청을 보냅니다. 그러면 저장소 서비스는 미리 구성된 CORS 규칙 집합에 따라 수행하려는 작업을 평가합니다. 이러한 규칙은 저장소 리소스에 대한 실제 요청에서 지정할 수 있는 원본 도메인, 요청 메서드 및 요청 헤더를 지정합니다.

서비스에 대해 CORS를 사용하도록 설정했으며 실행 전 요청과 일치하는 CORS 규칙이 있으면 서비스는 상태 코드 200(정상) 응답을 보내며 필수 Access-Control 헤더를 응답에 포함합니다.

서비스에 대해 CORS를 사용하도록 설정하지 않았거나 실행 전 요청과 일치하는 CORS 규칙이 없으면 서비스는 상태 코드 403(사용 권한 없음) 응답을 보냅니다.

OPTIONS 요청에 필수 CORS 헤더(Origin 및 Access-Control-Request-Method 헤더)가 없으면 서비스는 상태 코드 400(잘못된 요청) 응답을 보냅니다.

요청한 자원이 아닌 서비스(Blob, 큐, 테이블)에 대해 실행 전 요청을 평가합니다. 계정 소유자가 계정 서비스 속성의 일부분으로 CORS를 사용하도록 설정해야 요청이 성공합니다.

### <a name="actual-request"></a>실제 요청
실행 전 요청이 수락되고 응답이 반환되면 브라우저가 저장소 리소스에 대한 실제 요청을 디스패치합니다. 실행 전 요청이 거부되면 브라우저는 실제 요청을 즉시 거부합니다.

실제 요청은 저장소 서비스에 대한 일반 요청으로 처리됩니다. Origin 헤더가 있는 요청은 CORS 요청이며, 서비스에서는 일치하는 CORS 규칙이 있는지를 확인합니다. 일치하는 규칙이 있으면 Access-Control 헤더가 응답에 추가되어 클라이언트로 다시 전송됩니다. 일치하는 규칙이 없으면 CORS Access-Control 헤더가 반환되지 않습니다.

## <a name="enabling-cors-for-the-azure-storage-services"></a>Azure Storage 서비스에 대해 CORS 사용
CORS 규칙은 서비스 수준에서 설정되므로 Blob, 큐, 테이블 등의 각 서비스에 대해 개별적으로 CORS를 사용하거나 사용하지 않도록 설정해야 합니다. 기본적으로 CORS는 각 서비스에 대해 사용하지 않도록 설정됩니다. CORS를 사용하도록 설정하려면 버전 2013-08-15 이상을 사용하여 적절한 서비스 속성을 설정한 후에 CORS 규칙을 서비스 속성에 추가해야 합니다. 서비스에 대해 CORS를 사용하거나 사용하지 않도록 설정하고 CORS 규칙을 설정하는 방법에 대한 자세한 내용은 [Blob 서비스 속성 설정](https://msdn.microsoft.com/library/hh452235.aspx), [큐 서비스 속성 설정](https://msdn.microsoft.com/library/hh452232.aspx) 및 [테이블 서비스 속성 설정](https://msdn.microsoft.com/library/hh452240.aspx)을 참조하세요.

서비스 속성 설정 작업을 통해 지정한 단일 CORS 규칙의 샘플은 다음과 같습니다.

```xml
<Cors>    
    <CorsRule>
        <AllowedOrigins>http://www.contoso.com, http://www.fabrikam.com</AllowedOrigins>
        <AllowedMethods>PUT,GET</AllowedMethods>
        <AllowedHeaders>x-ms-meta-data*,x-ms-meta-target*,x-ms-meta-abc</AllowedHeaders>
        <ExposedHeaders>x-ms-meta-*</ExposedHeaders>
        <MaxAgeInSeconds>200</MaxAgeInSeconds>
    </CorsRule>
<Cors>
```

아래에는 CORS 규칙에 포함된 각 요소에 대한 설명이 나와 있습니다.

* **AllowedOrigins**: CORS를 통해 스토리지 서비스 서비스에 요청을 할 수 있는 원본 도메인입니다. 원본 도메인에서 요청이 시작됩니다. 이 원본은 사용자가 서비스로 보내는 원본과 대/소문자까지 정확하게 일치해야 합니다. 와일드카드 문자 '*'를 사용하여 모든 원본 도메인이 CORS를 통해 요청을 수행하도록 허용할 수도 있습니다. 도메인 http 위의 예제에서:\//www.contoso.com 및 http: \/ /www.fabrikam.com CORS를 사용 하 여 서비스에 대해 요청할 수 있습니다.
* **AllowedMethods**: 원본 도메인이 CORS 요청에 사용할 수 있는 메서드(HTTP 요청 동사)입니다. 위의 예제에서는 PUT 및 GET 요청만 허용됩니다.
* **AllowedHeaders**: 원본 도메인이 CORS 요청에 대해 지정할 수 있는 요청 헤더입니다. 위 예제에서는 x-ms-meta-data, x-ms-meta-target 및 x-ms-meta-abc로 시작하는 모든 메타데이터 헤더를 지정할 수 있습니다. 와일드카드 문자 '*'는 지정한 접두사로 시작하는 모든 헤더가 허용됨을 나타냅니다.
* **ExposedHeaders**: CORS 요청에 대한 응답에 포함하여 전송할 수 있으며 브라우저에서 요청 발급자에게 표시할 수 있는 응답 헤더입니다. 위의 예제에서 브라우저는 x-ms-meta로 시작하는 모든 헤더를 표시하도록 지정됩니다.
* **MaxAgeInSeconds**: 브라우저가 실행 전 OPTIONS 요청을 캐시해야 하는 최대 시간입니다.

Azure 저장소 서비스에서는 **허용되는 헤더** 및 **표시되는 헤더** 요소 둘 다에 대해 접두사 헤더를 지정할 수 있습니다. 헤더 범주를 허용하려는 경우 해당 범주에 공통 접두사를 지정하면 됩니다. 예를 들어 접두사 헤더로 *x-ms-meta*\*를 지정하면 x-ms-meta로 시작하는 모든 헤더와 일치하는 규칙이 설정됩니다.

CORS 규칙에는 다음 제한이 적용됩니다.

* Blob, 테이블, 큐 등의 저장소 서비스당 CORS 규칙을 5개까지 지정할 수 있습니다.
* 요청에 대한 모든 CORS 규칙 설정(XML 태그 제외)의 최대 크기는 2KB를 초과할 수 없습니다.
* 허용되는 헤더, 표시되는 헤더 또는 허용되는 원본의 길이는 256자를 초과할 수 없습니다.
* 허용되는 헤더와 표시되는 헤더는 다음 헤더 중 하나일 수 있습니다.
  * 리터럴 헤더 **x-ms-meta-processed**와 같은 정확한 헤더 이름을 입력합니다. 요청에 대해 최대 64개의 리터럴 헤더를 지정할 수 있습니다.
  * **x-ms-meta-data**\*와 같은 헤더의 접두사가 제공되는 접두사 헤더입니다. 이러한 방식으로 접두사를 지정하면 지정된 접두사로 시작하는 모든 헤더가 허용되거나 표시됩니다. 요청에 대해 최대 2개의 접두사 헤더를 지정할 수 있습니다.
* **허용되는 헤더** 요소에 지정된 메서드(HTTP 동사)는 Azure 저장소 서비스 API에서 지원하는 메서드를 따라야 합니다. 지원되는 메서드는 DELETE, GET, HEAD, MERGE, POST, OPTIONS, PUT입니다.

## <a name="understanding-cors-rule-evaluation-logic"></a>CORS 규칙 평가 논리 이해
저장소 서비스는 실행 전 요청이나 실제 요청을 받으면 해당하는 서비스 속성 설정 작업을 통해 서비스에 대해 설정한 CORS 규칙을 기준으로 하여 해당 요청을 평가합니다. CORS 규칙은 서비스 속성 설정 작업의 요청 본문에 설정된 순서로 평가됩니다.

다음과 같이 CORS 규칙을 평가합니다.

1. 먼저 요청의 원본 도메인이 **허용되는 원본** 요소에 대해 나열된 도메인에 포함되어 있는지 확인합니다. 원본 도메인이 목록에 포함되어 있거나 와일드카드 문자 '*'를 통해 모든 도메인이 허용되는 경우에는 규칙 평가가 계속 진행됩니다. 원본 도메인이 포함되어 있지 않으면 요청은 실패합니다.
2. 다음으로는 요청의 메서드(HTTP 동사)가 **허용되는 메서드** 요소에 대해 나열된 메서드에 포함되어 있는지 확인합니다. 메서드가 목록에 포함되어 있으면 규칙 평가가 계속 진행되고, 그렇지 않으면 요청이 실패합니다.
3. 요청의 원본 도메인과 메서드가 규칙과 일치하면 해당 규칙을 선택하여 요청을 처리하며 규칙을 추가로 평가하지 않습니다. 그러나 요청이 성공하려면 요청에 지정된 헤더가 **허용되는 메서드** 요소에 나열된 헤더에 포함되어 있는지를 확인해야 합니다. 전송된 헤더가 허용되는 헤더와 일치하지 않으면 요청이 실패합니다.

규칙은 요청 본문에 포함된 순서대로 처리되므로 모범 사례에 따르면 원본과 관련하여 가장 제한적인 규칙을 목록에서 첫 번째로 지정하는 것이 좋습니다. 그러면 해당 규칙이 먼저 평가됩니다. 모든 원본을 허용하는 규칙과 같이 제한 수준이 가장 낮은 규칙은 목록의 맨 끝에 지정합니다.

### <a name="example--cors-rules-evaluation"></a>예제 - CORS 규칙 평가
다음 예제에서는 저장소 서비스에 대해 CORS 규칙을 설정하는 작업의 요청 본문 중 일부분을 보여 줍니다. 요청을 생성하는 방법에 대한 자세한 내용은 [Blob 서비스 속성 설정](https://msdn.microsoft.com/library/hh452235.aspx), [큐 서비스 속성 설정](https://msdn.microsoft.com/library/hh452232.aspx) 및 [테이블 서비스 속성 설정](https://msdn.microsoft.com/library/hh452240.aspx)을 참조하세요.

```xml
<Cors>
    <CorsRule>
        <AllowedOrigins>http://www.contoso.com</AllowedOrigins>
        <AllowedMethods>PUT,HEAD</AllowedMethods>
        <MaxAgeInSeconds>5</MaxAgeInSeconds>
        <ExposedHeaders>x-ms-*</ExposedHeaders>
        <AllowedHeaders>x-ms-blob-content-type, x-ms-blob-content-disposition</AllowedHeaders>
    </CorsRule>
    <CorsRule>
        <AllowedOrigins>*</AllowedOrigins>
        <AllowedMethods>PUT,GET</AllowedMethods>
        <MaxAgeInSeconds>5</MaxAgeInSeconds>
        <ExposedHeaders>x-ms-*</ExposedHeaders>
        <AllowedHeaders>x-ms-blob-content-type, x-ms-blob-content-disposition</AllowedHeaders>
    </CorsRule>
    <CorsRule>
        <AllowedOrigins>http://www.contoso.com</AllowedOrigins>
        <AllowedMethods>GET</AllowedMethods>
        <MaxAgeInSeconds>5</MaxAgeInSeconds>
        <ExposedHeaders>x-ms-*</ExposedHeaders>
        <AllowedHeaders>x-ms-client-request-id</AllowedHeaders>
    </CorsRule>
</Cors>
```

다음으로는 아래 CORS 요청을 살펴보세요.

| 요청 |  |  | response |  |
| --- | --- | --- | --- | --- |
| **메서드** |**원본** |**요청 헤더** |**일치하는 규칙** |**결과** |
| **PUT** |http:\//www.contoso.com |x-ms-blob-콘텐츠-유형 |첫 번째 규칙 |성공 |
| **GET** |http:\//www.contoso.com |x-ms-blob-콘텐츠-유형 |두 번째 규칙 |성공 |
| **GET** |http:\//www.contoso.com |x-ms-client-request-id |두 번째 규칙 |실패 |

첫 번째 요청은 첫 번째 규칙과 일치합니다. 원본 도메인이 허용되는 도메인과 일치하고, 메서드가 허용되는 메서드와 일치하며, 헤더가 허용되는 헤더와 일치하므로 해당 요청은 성공합니다.

두 번째 요청의 경우 메서드가 허용되는 메서드와 일치하지 않으므로 첫 번째 규칙과 일치하지 않습니다. 그러나 두 번째 규칙과는 일치하므로 요청은 성공합니다.

세 번째 요청은 원본 도메인과 메서드가 두 번째 규칙과 일치하므로 규칙을 추가로 평가하지 않습니다. 그러나 *x-ms-client-request-id header* 는 두 번째 규칙에서 허용되지 않으므로 세 번째 규칙의 의미 체계에서 해당 요청이 성공하도록 허용하더라도 요청은 실패합니다.

> [!NOTE]
> 이 예제에서는 제한 수준이 낮은 규칙이 높은 규칙보다 먼저 표시되어 있지만 일반적으로는 가장 제한적인 규칙을 목록에 먼저 포함하는 것이 좋습니다.
> 
> 

## <a name="understanding-how-the-vary-header-is-set"></a>Vary 헤더 설정 방법 이해
표준 HTTP/1.1 헤더인 *Vary* 헤더는 요청을 처리하기 위해 서버에서 선택한 기준을 브라우저나 사용자 에이전트에 알려주는 요청 헤더 필드 집합으로 구성됩니다. *Vary* 헤더는 주로 프록시, 브라우저 및 CDN에서 캐싱용으로 사용하며 응답을 캐시할 방법을 결정하는 데 사용됩니다. 자세한 내용은 [Vary 헤더](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html)사양을 참조하세요.

브라우저 또는 다른 사용자 에이전트가 CORS 요청에서 응답을 캐시할 때는 원본 도메인이 허용되는 원본으로 캐시됩니다. 캐시가 활성 상태인 동안 두 번째 도메인에서 저장소 리소스에 대해 같은 요청을 실행하면 사용자 에이전트가 캐시된 원본 도메인을 검색합니다. 일반적인 경우에는 요청이 성공하지만, 이 경우에는 두 번째 도메인이 캐시된 도메인과 일치하지 않으므로 요청이 실패합니다. Azure Storage에서 Vary 헤더를 **Origin** 으로 설정하여 요청된 도메인이 캐시된 원본과 다를 때 서비스로 후속 CORS 요청을 보내도록 사용자 에이전트에 명령하는 경우도 있습니다.

Azure Storage는 다음과 같은 경우 실제 GET/HEAD 요청에 대한 *Vary* 헤더를 **Origin** 으로 설정합니다.

* 요청 원본이 CORS 규칙에 의해 정의된 허용되는 원본과 정확하게 일치하는 경우. 두 원본이 정확하게 일치하려면 CORS 규칙에 와일드카드 문자 '*'가 포함되지 않아야 합니다.
* 요청 원본과 일치하는 규칙은 없지만 저장소 서비스에 대해 CORS를 사용하도록 설정한 경우

GET/HEAD 요청이 모든 원본을 허용하는 CORS 규칙과 일치하는 경우 응답은 모든 원본이 허용됨을 나타내며, 사용자 에이전트 캐시는 해당 캐시가 활성 상태인 동안 모든 원본 도메인에서 수행하는 후속 요청을 허용합니다.

GET/HEAD가 아닌 메서드를 사용하는 요청의 경우 저장소 서비스는 Vary 헤더를 설정하지 않습니다. 사용자 에이전트는 이러한 메서드에 대한 응답을 캐시하지 않기 때문입니다.

다음 표에는 Azure 저장소가 앞에서 설명한 사례를 기준으로 하여 GET/HEAD 요청에 응답하는 방법이 나와 있습니다.

| 요청 | 계정 설정 및 규칙 평가 결과 |  |  | response |  |  |
| --- | --- | --- | --- | --- | --- | --- |
| **요청한 Origin 헤더 유무** |**이 서비스에 대한 CORS 규칙 지정 여부** |**모든 원본(*)을 허용하는 일치 규칙 유무** |**원본이 정확하게 일치하는지 확인하는 일치 규칙 유무** |**응답의 Vary 헤더가 Origin으로 설정되어 있는지 여부** |**응답의 Access-Control-Allowed-Origin 유무: "*"** |**응답의 Access-Control-Exposed-Headers 유무** |
| 아닙니다. |아니요 |아니요 |아니요 |아니요 |아니요 |아니요 |
| 아니요 |사용자 계정 컨트롤 |아니오 |아니요 |사용자 계정 컨트롤 |아니오 |아니요 |
| 아니요 |예 |예 |아니오 |아니요 |예 |예 |
| 예 |아니오 |아니요 |아니요 |아니요 |아니요 |아니요 |
| 예 |예 |no |예 |예 |no |예 |
| 예 |예 |아니오 |아니요 |사용자 계정 컨트롤 |아니오 |아니요 |
| 예 |예 |예 |아니오 |아니요 |예 |예 |

## <a name="billing-for-cors-requests"></a>CORS 요청에 대한 청구
[Blob 서비스 속성 설정](https://msdn.microsoft.com/library/hh452235.aspx), [큐 서비스 속성 설정](https://msdn.microsoft.com/library/hh452232.aspx) 또는 [테이블 서비스 속성 설정](https://msdn.microsoft.com/library/hh452240.aspx)을 호출하여 계정의 저장소 서비스에 대해 CORS를 사용하도록 설정한 경우 실행 전 요청이 성공하면 요금이 청구됩니다. 비용을 최소화하려면 사용자 에이전트가 요청을 캐시하도록 CORS 규칙의 **MaxAgeInSeconds** 요소를 큰 값으로 설정하는 것이 좋습니다.

실행 전 요청이 실패하면 요금이 청구되지 않습니다.

## <a name="next-steps"></a>다음 단계
[Blob 서비스 속성 설정](https://msdn.microsoft.com/library/hh452235.aspx)

[큐 서비스 속성 설정](https://msdn.microsoft.com/library/hh452232.aspx)

[테이블 서비스 속성 설정](https://msdn.microsoft.com/library/hh452240.aspx)

[W3C 교차 원본 자원 공유 사양](https://www.w3.org/TR/cors/)

