---
title: Azure API Management로 고급 요청 제한
description: Azure API Management로 유연한 할당량 및 속도 제한 정책을 만들고 적용하는 방법에 대해 알아봅니다.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: fc813a65-7793-4c17-8bb9-e387838193ae
ms.service: api-management
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/03/2018
ms.author: apimpm
ms.openlocfilehash: 22c3987121e2ab3479274c89c359c679f5f1135e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61087133"
---
# <a name="advanced-request-throttling-with-azure-api-management"></a>Azure API Management로 고급 요청 제한
들어오는 요청을 제한하는 것은 Azure API Management의 중요한 역할입니다. Azure API Management는 요청 속도 또는 전송된 총 요청/데이터를 제어하여 API 공급자가 자신의 API가 남용되지 않도록 보호하고 다양한 API 제품 계층에 대해 가치를 창출할 수 있도록 합니다.

## <a name="product-based-throttling"></a>제품 기반 제한
현재까지, 속도 제한 기능은 Azure Portal에 정의된 특정 제품 구독으로 범위를 제한했습니다. 이렇게 하면 API 공급자가 해당 API를 사용하기 위해 등록한 개발자에게 제한을 적용하는 데는 유용하지만 예를 들어 API의 개별 최종 사용자를 제한하는 데는 도움이 되지 않습니다. 개발자 애플리케이션의 단일 사용자가 전체 할당량을 사용한 후 개발자의 다른 고객이 해당 애플리케이션을 사용하지 못하도록 할 수 있습니다. 또한, 대용량의 요청을 생성할 수 있는 여러 고객이 간헐적 사용자에 대한 액세스를 제한할 수 있습니다.

## <a name="custom-key-based-throttling"></a>사용자 지정 키 기반 제한
새로운 [rate-limit-by-key](/azure/api-management/api-management-access-restriction-policies#LimitCallRateByKey) 및 [quota-by-key](/azure/api-management/api-management-access-restriction-policies#SetUsageQuotaByKey) 정책은 트래픽 제어에 유연한 솔루션을 제공합니다. 이 새로운 정책을 통해 트래픽 사용량을 추적하는 데 사용할 키를 식별하는 식을 정의할 수 있습니다. 이러한 작동 방식은 예제를 통해 가장 쉽게 이해할 수 있습니다. 

## <a name="ip-address-throttling"></a>IP 주소 제한
다음 정책은 단일 클라이언트 IP 주소를 1분에 10개 호출만으로 제한하고 1개월에 총 1,000,000개 호출과 10,000킬로바이트의 대역폭으로 제한합니다. 

```xml
<rate-limit-by-key  calls="10"
          renewal-period="60"
          counter-key="@(context.Request.IpAddress)" />

<quota-by-key calls="1000000"
          bandwidth="10000"
          renewal-period="2629800"
          counter-key="@(context.Request.IpAddress)" />
```

인터넷의 모든 클라이언트가 고유 IP 주소를 사용한 경우 사용자별로 사용량을 제한하는 효과적인 방법일 수 있습니다. 그러나 사용자는 NAT 디바이스를 통해 인터넷에 액세스하므로 여러 사용자가 하나의 공용 IP 주소를 공유할 가능성이 높습니다. 그럼에도 불구하고 `IpAddress` 에 대한 무단 액세스를 허용하는 API에 대해서는 최상의 옵션일 수 있습니다.

## <a name="user-identity-throttling"></a>사용자 ID 제한
최종 사용자가 인증된 후 해당 사용자를 고유하게 식별하는 정보를 기반으로 제한 키를 생성할 수 있습니다.

```xml
<rate-limit-by-key calls="10"
    renewal-period="60"
    counter-key="@(context.Request.Headers.GetValueOrDefault("Authorization","").AsJwt()?.Subject)" />
```

이 예제에서는 인증 헤더를 추출하여 `JWT` 개체로 변환하고 토큰의 주체를 사용하여 사용자를 식별하고 속도 제한 키로 사용하는 방법을 보여 줍니다. 사용자 ID가 `JWT`에 다른 클레임 중 하나로 저장된 경우 해당 값을 대신 사용할 수 있습니다.

## <a name="combined-policies"></a>결합된 정책
새 제한 정책이 기존 제한 정책보다 더 많은 제어를 제공하더라도 여전히 두 기능을 결합하는 값이 있습니다. 제품 구독 키로 제한([구독으로 호출 속도 제한](/azure/api-management/api-management-access-restriction-policies#LimitCallRate) 및 [구독으로 사용 할당량 설정](/azure/api-management/api-management-access-restriction-policies#SetUsageQuota))은 사용 수준에 따라 요금을 청구하여 API 수익화를 실현하는 뛰어난 방법입니다. 사용자별로 제한할 수 있도록 세분화된 제어로 보완되며 한 사용자의 동작이 다른 사용자의 환경에 부정적인 영향을 주지 않도록 합니다. 

## <a name="client-driven-throttling"></a>클라이언트 기반 제한
제한 키가 [정책 식](/azure/api-management/api-management-policy-expressions)을 사용하여 정의된 경우 제한 범위를 정하는 방식을 선택하는 것은 API 공급자입니다. 그러나 개발자가 자신의 고객을 속도 제한하는 방식을 제어하려 할 수 있습니다. API 공급자가 개발자의 클라이언트 애플리케이션이 키를 API에 전달하도록 허용하는 사용자 지정 헤더를 도입하여 이를 수행할 수 있습니다.

```xml
<rate-limit-by-key calls="100"
          renewal-period="60"
          counter-key="@(request.Headers.GetValueOrDefault("Rate-Key",""))"/>
```

따라서 개발자의 클라이언트 애플리케이션이 속도 제한 키를 만드는 방법을 선택할 수 있습니다. 클라이언트 개발자는 키 집합을 사용자에 할당하고 키 사용을 회전하여 자신의 고유한 속도 계층을 만들 수 있습니다.

## <a name="summary"></a>요약
Azure API Management에서는 속도 및 할당량 제한을 제공하여 API 서비스를 보호하고 가치를 더합니다. 사용자 지정 범위 규칙이 포함된 새로운 제한 정책을 통해 정책을 세밀하게 제어할 수 있으므로 고객이 훨씬 향상된 애플리케이션을 구축할 수 있도록 합니다. 이 문서의 예제에서는 클라이언트 IP 주소, 사용자 ID 및 클라이언트에서 생성한 값으로 속도 제한 키를 제조하여 이러한 새 정책을 사용하는 방법을 보여줍니다. 그러나 사용자 에이전트, URL 경로 조각, 메시지 크기와 같이 사용할 수 있는 메시지의 많은 다른 부분이 있습니다.

## <a name="next-steps"></a>다음 단계
이 항목에 대한 Disqus 스레드에 의견을 보내주세요. 귀하의 시나리오에서 논리적으로 선택된 잠재적인 다른 키 값에 대한 의견을 환영합니다.

