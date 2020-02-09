---
title: Azure의 역방향 DNS 개요-Azure DNS
description: 이 학습 경로에서 역방향 DNS가 작동 하는 방법 및 Azure에서 사용 하는 방법에 대한 학습을 시작 합니다.
services: dns
documentationcenter: na
author: rohinkoul
manager: KumuD
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/29/2017
ms.author: rohink
ms.openlocfilehash: bf3da62e989f0e029efdc8e9c70f5f45e0ddd765
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/01/2020
ms.locfileid: "76932307"
---
# <a name="overview-of-reverse-dns-and-support-in-azure"></a>Azure의 역방향 DNS 및 지원 개요

이 문서에서는 역방향 DNS가 작동하는 방식과 Azure에서 지원되는 역방향 DNS 시나리오에 대해 대략적으로 설명합니다.

## <a name="what-is-reverse-dns"></a>역방향 DNS란?

기본 DNS 레코드를 통해 DNS 이름(예: 'www.contoso.com')을 IP 주소(예: 64.4.6.100)에 매핑할 수 있습니다.  역방향 DNS로는 IP 주소(64.4.6.100)를 다시 이름('www.contoso.com')으로 변환할 수 있습니다.

역방향 DNS 레코드는 다양한 상황에 사용됩니다. 예를 들어 역방향 DNS 레코드는 보낸 사람의 메일 메시지를 확인하여 스팸 메일을 방지하는 데 널리 사용됩니다.  수신 메일 서버는 보내는 서버의 IP 주소의 역방향 DNS 레코드를 검색하고, 해당 호스트가 원래의 도메인에서 메일을 보내도록 허가를 받았는지 확인합니다. 

## <a name="how-reverse-dns-works"></a>역방향 DNS 작동 방식

역방향 DNS 레코드는 'ARPA' 영역이라는 특수한 DNS 영역에서 호스팅됩니다.  이러한 영역은 'contoso.com'과 같은 일반적인 계층 구조 호스팅 도메인과 병렬로 별도의 DNS 계층 구조를 형성합니다.

예를 들어 DNS 레코드 'www.contoso.com'은 영역 'contoso.com'에 이름 'www'를 포함하는 DNS 'A' 레코드를 사용하여 구현됩니다.  이 A 레코드는 해당 IP 주소(이 경우 64.4.6.100)를 가리킵니다.  역방향 조회는 ' 6.4.64.in-addr ' 영역에서 ' 100 ' 이라는 ' PTR ' 레코드를 사용 하 여 개별적으로 구현 됩니다 (IP 주소는 node.js 영역에서 반전 됨).  이 PTR 레코드는 올바르게 구성 된 경우 ' www.contoso.com ' 이름을 가리킵니다.

조직에 IP 주소 블록이 할당되면 해당 ARPA 영역을 관리할 권한도 획득하게 됩니다. Azure에 사용되는 IP 주소 블록에 해당되는 ARPA 영역은 Microsoft에서 호스트 및 관리합니다. ISP는 사용자 고유의 IP 주소에 대한 ARPA 영역을 호스트하거나 사용자가 Azure DNS와 같은 선택한 DNS 서비스에서 ARPA 영역을 호스트하도록 할 수 있습니다.

> [!NOTE]
> 정방향 및 역방향 DNS 조회를 DNS 계층과 함께 별도로 구현합니다. 'www.contoso.com'에 대한 역방향 조회는 영역 'contoso.com'에서 호스트되지 **않으며** 대신 해당 IP 주소 블록에 대한 ARPA 영역에서 호스트됩니다. IPv4 및 IPv6 주소 블록에 별도 영역이 사용됩니다.

### <a name="ipv4"></a>IPv4

IPv4 역방향 조회 영역의 이름은 `<IPv4 network prefix in reverse order>.in-addr.arpa` 형식이어야 합니다.

예를 들어 192.0.2.0/24 접두사에 있는 IP를 갖는 호스트의 레코드를 호스트하기 위해 역방향 영역을 만들 경우 주소의 네트워크 접두사를 분리한 다음(192.0.2) 순서를 뒤집고(2.0.192) 접미사 `.in-addr.arpa`를 추가하여 영역 이름을 만듭니다.

|서브넷 클래스|네트워크 접두사  |역방향 네트워크 접두사  |표준 접미사  |역방향 영역 이름 |
|-------|----------------|------------|-----------------|---------------------------|
|클래스 A|203.0.0.0/8     | 203        | .in-addr.arpa   | `203.in-addr.arpa`        |
|클래스 B|198.51.0.0/16   | 51.198     | .in-addr.arpa   | `51.198.in-addr.arpa`     |
|클래스 C|192.0.2.0/24    | 2.0.192    | .in-addr.arpa   | `2.0.192.in-addr.arpa`    |

### <a name="classless-ipv4-delegation"></a>클래스 없는 IPv4 위임

일부 경우에 조직에 할당된 IP 범위가 클래스 C(/24) 범위보다 작습니다. 이 경우 IP 범위는 `.in-addr.arpa` 영역 계층 구조 내의 영역 경계에 속하지 않으므로 하위 영역으로 위임할 수 없습니다.

대신 다른 메커니즘을 통해 개별 역방향 조회(PTR) 레코드에 대한 제어 권한을 전용 DNS 영역으로 전송합니다. 이 메커니즘은 각 IP 범위에 대한 자식 영역을 위임한 다음 CNAME 레코드를 사용하여 범위의 각 IP 주소를 해당 자식 영역에 개별적으로 매핑합니다.

예를 들어 조직에는 ISP에 의해 IP 범위 192.0.2.128/26이 부여됩니다. 이것은 192.0.2.128에서 192.0.2.191까지의 64개 IP 주소를 나타냅니다. 이 범위에 대한 역방향 DNS는 다음과 같이 구현됩니다.
- 조직에서는 128-26.2.0.192.in-addr.arpa라는 역방향 조회 영역을 만듭니다. 접두사 '128-26'은 클래스 C(/24) 범위 내 조직에 할당된 네트워크 세그먼트를 나타냅니다.
- ISP는 클래스 C 부모 영역에서 위 영역에 대한 DNS 위임을 설정하기 위한 NS 레코드를 만듭니다. 또한 부모(클래스 C) 역방향 조회 영역에 CNAME 레코드를 생성하고 IP 범위의 각 IP 주소를 조직에서 만드는 새 영역에 매핑합니다.

```
$ORIGIN 2.0.192.in-addr.arpa
; Delegate child zone
128-26    NS       <name server 1 for 128-26.2.0.192.in-addr.arpa>
128-26    NS       <name server 2 for 128-26.2.0.192.in-addr.arpa>
; CNAME records for each IP address
129       CNAME    129.128-26.2.0.192.in-addr.arpa
130       CNAME    130.128-26.2.0.192.in-addr.arpa
131       CNAME    131.128-26.2.0.192.in-addr.arpa
; etc
```
- 그런 다음 해당 자식 영역 내에서 개별 PTR 레코드를 관리합니다.

```
$ORIGIN 128-26.2.0.192.in-addr.arpa
; PTR records for each UIP address. Names match CNAME targets in parent zone
129      PTR    www.contoso.com
130      PTR    mail.contoso.com
131      PTR    partners.contoso.com
; etc
```
IP 주소 '192.0.2.129'에 대한 역방향 조회는 이름이 '129.2.0.192.in-addr.arpa'인 PTR 레코드를 쿼리합니다. 이 쿼리는 부모 영역의 CNAME을 통해 자식 영역에 있는 PTR 레코드를 확인합니다.

### <a name="ipv6"></a>IPv6

IPv6 역방향 조회 영역의 이름은 `<IPv6 network prefix in reverse order>.ip6.arpa` 형식이어야 합니다.

예제: 2001:db8:1000:abdc::/64 접두사에 있는 IP를 갖는 호스트의 레코드를 호스트하기 위해 역방향 영역을 만들 경우 주소의 네트워크 접두사를 분리하여 영역 이름을 만듭니다(2001:db8:abdc::). 그런 다음 [제로 압축](https://technet.microsoft.com/library/cc781672(v=ws.10).aspx)이 IPv6 주소 접두사를 줄이기 위해 사용된 경우 이 압축을 제거하여 IPv6 네트워크 접두사를 확장합니다(2001:0db8:abdc:0000::). 접두사의 각 16진수 숫자 간을 마침표로 구분하고 순서를 뒤집어 역순서의 네트워크 접두사(`0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2`)를 작성하고 접미사 `.ip6.arpa`를 추가합니다.


|네트워크 접두사  |확장된 역방향 네트워크 접두사 |표준 접미사 |역방향 영역 이름  |
|---------|---------|---------|---------|
|2001:db8:abdc::/64    | 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2        | .ip6.arpa        | `0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa`       |
|2001:db8:1000:9102::/64    | 2.0.1.9.0.0.0.1.8.b.d.0.1.0.0.2        | .ip6.arpa        | `2.0.1.9.0.0.0.1.8.b.d.0.1.0.0.2.ip6.arpa`        |


## <a name="azure-support-for-reverse-dns"></a>역방향 DNS에 대한 Azure 지원

Azure에서는 역방향 DNS와 관련하여 두 가지 별도의 시나리오를 지원합니다.

**IP 주소 블록에 해당하는 역방향 조회 영역 호스팅**
Azure DNS를 사용하여 IPv4 및 IPv6 둘 다에 대해 [역방향 조회 영역을 호스트하고 각 역방향 DNS 조회를 위해 PTR 레코드를 관리](dns-reverse-dns-hosting.md)할 수 있습니다.  역방향 조회(ARPA) 영역을 만들고 위임을 설정하며 PTR 레코드를 구성하는 과정은 일반 DNS 영역과 같습니다.  유일한 차이점은 DNS 등록 기관보다는 ISP를 통해 위임을 구성해야 하며 PTR 레코드 유형만 사용해야 한다는 점입니다.

**Azure 서비스에 할당된 IP 주소에 대한 역방향 DNS를 구성합니다.** Azure에서는 [Azure 서비스에 할당된 IP 주소에 대한 역방향 조회를 구성](dns-reverse-dns-for-azure-services.md)할 수 있습니다.  이 역방향 조회는 Azure에 의해 해당 ARPA 영역에서 PTR 레코드로 구성됩니다.  Azure에 사용되는 모든 IP 범위에 해당되는 이러한 ARPA 영역은 Microsoft에서 호스트합니다.

## <a name="next-steps"></a>다음 단계

역방향 DNS에 대한 자세한 내용은 [Wikipedia에서 역방향 DNS 조회](https://en.wikipedia.org/wiki/Reverse_DNS_lookup)를 참조하세요.
<br>
[Azure DNS에서 ISP 할당 IP 범위에 대한 역방향 조회 영역 호스트](dns-reverse-dns-for-azure-services.md) 방법을 알아봅니다.
<br>
[Azure 서비스에 대한 역방향 DNS 레코드를 관리](dns-reverse-dns-for-azure-services.md)하는 방법을 알아봅니다.

