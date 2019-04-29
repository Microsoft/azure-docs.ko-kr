---
author: WenJason
ms.service: dns
ms.topic: include
origin.date: 11/25/2018
ms.date: 12/24/2018
ms.author: v-jay
ms.openlocfilehash: f7fa018225d82e2db4a3c51b333aea6fcb9a1514
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60563394"
---
### <a name="record-names"></a>레코드 이름

Azure DNS에서는 상대 이름을 사용하여 레코드를 지정합니다. FQDN(*정규화된* 도메인 이름)은 영역 이름을 포함하는 반면 *상대* 이름은 영역 이름을 포함하지 않습니다. 예를 들어 'contoso.com' 영역의 상대 레코드 이름 'www'는 정규화된 레코드 이름 'www.contoso.com'을 제공합니다.

*apex* 레코드는 DNS 영역의 루트(또는 *apex*)에 있는 DNS 레코드입니다. 예를 들어 DNS 영역 'contoso.com'에서 루트 레코드는 정규화된 이름 'contoso.com'도 가집니다(*naked* 도메인이라고도 함).  규칙에 따라 루트 레코드를 나타내는 데 '\@' 상대 이름을 사용합니다.

### <a name="record-types"></a>레코드 유형

각 DNS 레코드에는 이름 및 형식이 있습니다. 레코드는 포함된 데이터에 따라 다양한 형식으로 구성됩니다. 가장 일반적인 형식은 이름을 IPv4 주소에 매핑하는 'A' 레코드입니다. 또 다른 일반적인 형식은 이름을 메일 서버에 매핑하는 'MX' 레코드입니다.

Azure DNS는 모든 일반적인 DNS 레코드 형식을 지원 합니다. A, AAAA, CAA, CNAME, MX, NS, PTR, SOA, SRV 및 TXT입니다. [SPF 레코드는 TXT 레코드를 사용하여 표현됩니다](../articles/dns/dns-zones-records.md#spf-records).

### <a name="record-sets"></a>레코드 집합

지정된 이름 및 형식을 가진 DNS 레코드를 두 개 이상 만들어야 하는 경우도 있습니다. 예를 들어 'www.contoso.com' 웹 사이트가 서로 다른 두 IP 주소에서 호스트된다고 가정합니다. 웹 사이트에는 각 IP 주소마다 하나씩, 두 개의 A 레코드가 있어야 합니다. 다음은 레코드 집합의 예제입니다.

    www.contoso.com.        3600    IN    A    134.170.185.46
    www.contoso.com.        3600    IN    A    134.170.188.221

Azure DNS는 *레코드 집합*을 사용하여 모든 DNS 레코드를 관리합니다. 레코드 집합(*리소스* 레코드 집합이라고도 함)은 영역 내에서 동일한 이름과 형식을 가진 DNS 레코드의 컬렉션입니다. 대부분의 레코드 집합은 단일 레코드를 포함합니다. 그러나 레코드 집합에서 레코드를 둘 이상 포함하는 위와 같은 예제도 드물지 않습니다.

예를 들어 'contoso.com' 영역에 IP 주소 '134.170.185.46'(위 첫 번째 레코드)을 가리키는 A 레코드 'www'를 만든 경우를 가정해 보겠습니다.  두 번째 레코드를 만들려면 추가 레코드 집합을 만드는 대신 기존 레코드 집합에 해당 레코드를 추가합니다.

SOA 및 CNAME 레코드 유형은 예외입니다. DNS 표준에서는 이러한 유형의 이름이 같은 여러 레코드를 허용하지 않으므로 이러한 레코드 집합은 단일 레코드만 포함할 수 있습니다.