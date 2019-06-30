---
title: Azure 프런트 도어 서비스-URL 리디렉션 | Microsoft Docs
description: 이 문서에서는 Azure 프런트 도어 서비스에서 지 원하는 방법 URL 리디렉션은 해당 경로 대 한 구성 된 경우를 이해 하도록 도와줍니다.
services: front-door
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/08/2019
ms.author: sharadag
ms.openlocfilehash: 3d77a16d24a1a843b39d97904a675518c43a525a
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/22/2019
ms.locfileid: "67332697"
---
# <a name="url-redirect"></a>URL 리디렉션
트래픽을 리디렉션할 Azure 프런트 도어 서비스를 사용할 수 있습니다. (프로토콜, 호스트 이름, 경로, 쿼리 문자열) 여러 수준에서 트래픽을 리디렉션할 수 있습니다 및 기능을 모두 구성할 수 있습니다 개별 마이크로 서비스에 대 한 경로 기반 리디렉션 그대로입니다. 이를 통해 애플리케이션 구성이 간소화되고, 리소스 사용이 최적화되고, 전역 및 경로 기반 리디렉션을 비롯한 새로운 리디렉션 시나리오가 지원됩니다.
</br>

![Azure 프런트 도어 URL 리디렉션][1]

## <a name="redirection-types"></a>리디렉션 유형
리디렉션 형식 리디렉션의 용도 이해 하 여 클라이언트가 응답 상태 코드를 설정 합니다. 다음과 같은 유형의 리디렉션 지원 됩니다.

- **301 (영구적 이동)** : 대상 리소스가 새 영구 URI 할당 된이 리소스에 대 한 향후 참조를 반드시 포함된 Uri 중 하나를 사용 함을 나타냅니다. Http 및 HTTPS 간의 리디렉션으로 301 상태 코드를 사용 합니다. 
- **302 (있음)** : 대상 리소스에 일시적으로 다른 URI 아래 적용 있음을 나타냅니다. 리디렉션은 경우에 따라 변경 될 수 있으므로 클라이언트 계속 미래 요청에 유효한 요청 URI를 사용 하려면 반드시 합니다.
- **307 (임시 리디렉션)** : 사용자 에이전트 안 URI에는 자동 리디렉션을 수행 하는 경우 요청 메서드를 변경 하는 대상 리소스가 일시적으로 다른 URI 아래는 나타냅니다. 리디렉션 시간이 지남에 따라 변경 될 수, 있으므로 클라이언트 했던 이후 요청에 대 한 원래 효과적인 요청 URI를 사용 하 여 계속 합니다.
- **308 (영구 리디렉션)** : 대상 리소스가 새 영구 URI 할당 된이 리소스에 대 한 향후 참조를 반드시 포함된 Uri 중 하나를 사용 함을 나타냅니다. 링크 편집 기능을 사용 하 여 클라이언트에도 알려주시면 자동으로 다시 링크에 대 한 참조가 유효 하려면 요청 URI을 하나 이상의 가능한 경우 서버에서 전송 하 여 새로운 참조.

## <a name="redirection-protocol"></a>리디렉션 프로토콜
리디렉션에 사용할 프로토콜을 설정할 수 있습니다. 이렇게 하면 HTTP 및 HTTPS 간의 리디렉션으로 설정 하는 리디렉션 기능 가장 일반적인 사용 사례 중 하나입니다.

- **HTTPS만**: HTTP에서 HTTPS 트래픽을 리디렉션할 하려는 경우 https 프로토콜을만 설정 합니다. Azure 프런트 도어 서비스는 항상 설정 해야 리디렉션 HTTPS로만 하는 것이 좋습니다.
- **HTTP만**: 이 HTTP로 들어오는 요청을 리디렉션합니다. HTTP 트래픽을 유지 하려는 경우에, 암호화 되지 않은이 값을 사용 합니다.
- **일치 요청**: 이 옵션은 들어오는 요청에서 사용 된 프로토콜을 유지 합니다. 따라서 HTTP 요청을 계속 HTTP 및 HTTPS 요청을 HTTPS post 리디렉션 상태로 유지 됩니다.

## <a name="destination-host"></a>대상 호스트
라우팅 리디렉션 구성의 일환으로, 호스트 이름 또는 리디렉션 요청에 대 한 도메인을 변경할 수 있습니다. 리디렉션 URL에 호스트 이름을 변경 하거나 그렇지 않으면 들어오는 요청의 호스트를 유지 하려면이 필드를 설정할 수 있습니다. 따라서이 필드를 사용 하 여 리디렉션할 수 있습니다 전송 된 모든 요청 https://www.contoso.com/ *를 https://www.fabrikam.com/ * 합니다.

## <a name="destination-path"></a>대상 경로
의도치 않은 리디렉션의 일부로 URL의 경로 세그먼트를 대체 하려는 경우에 대 한 새 경로 값을 사용 하 여이 필드를 설정할 수 있습니다. 그렇지 않은 경우 이동의 일부로 path 값을 유지 하기 위해 선택할 수 있습니다. 따라서이 필드를 사용 하 여를 리디렉션할 수 있습니다에 전송 된 모든 요청 https://www.contoso.com/ *를 https://www.contoso.com/redirected-site 입니다.

## <a name="query-string-parameters"></a>쿼리 문자열 매개 변수
또한 리디렉션된 URL에 쿼리 문자열 매개 변수를 바꿀 수 있습니다. 들어오는 요청 URL에서 기존 쿼리 문자열을 대체 하려면이 필드 'Replace'를 설정 하 고 적절 한 값을 설정 합니다. 그렇지 않으면 'Preserve' 필드를 설정 하 여 원래 쿼리 문자열 집합을 유지할 수 있습니다. 예를 들어이 필드를 사용 하 여 트래픽을 리디렉션할 수 있습니다 모든 전송할 https://www.contoso.com/foo/bar 에 https://www.contoso.com/foo/bar?&utm_referrer=https%3A%2F%2Fwww.bing.com%2F 입니다. 

## <a name="destination-fragment"></a>대상 조각
대상 조각의 '#', 페이지의 특정 섹션으로 이동 하려면 브라우저에서 일반적으로 사용 후는 부분 URL입니다. 리디렉션 URL에 조각을 추가 하려면이 필드를 설정할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [Front Door를 만드는 방법](quickstart-create-front-door.md)을 알아봅니다.
- [Front Door의 작동 원리](front-door-routing-architecture.md)를 알아봅니다.

<!--Image references-->
[1]: ./media/front-door-url-redirect/front-door-url-redirect.png