---
title: Azure DNS 위임 개요
description: 도메인 위임을 변경하고 Azure DNS 이름 서버를 사용하여 도메인 호스팅을 제공하는 방법을 이해합니다.
services: dns
author: WenJason
ms.service: dns
origin.date: 2/19/2019
ms.date: 03/04/2019
ms.author: v-jay
ms.openlocfilehash: 70c1c1ab0dd5d1998054cf0c68325022803dff06
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60563161"
---
# <a name="delegation-of-dns-zones-with-azure-dns"></a>Azure DNS를 사용하여 DNS 영역 위임

Azure DNS를 사용하면 DNS 영역을 호스트하고 Azure에서 도메인에 대한 DNS 레코드를 관리할 수 있습니다. 도메인에 대한 DNS 쿼리가 Azure DNS에 도달하려면 부모 도메인에서 Azure DNS로 도메인을 위임해야 합니다. Azure DNS는 도메인 등록 기관이 아닙니다. 이 문서에서는 도메인 위임의 작동 방식 및 도메인을 Azure DNS로 위임하는 방법을 설명합니다.

## <a name="how-dns-delegation-works"></a>DNS 위임의 작동 방식

### <a name="domains-and-zones"></a>도메인 및 영역

Domain Name System은 도메인 계층 구조입니다. 계층 구조는 이름이 단순히 '**.**'인 'root' 도메인에서 시작합니다.  그 아래에 'com', 'net', 'org', 'uk' 또는 'jp'와 같은 최상위 도메인이 있습니다.  이러한 최상위 수준 도메인 아래는 'org.uk' 또는 'co.jp'와 같은 두 번째 수준의 도메인입니다.  방식으로 계속됩니다. DNS 계층 구조의 도메인은 별도의 DNS 영역을 사용하여 호스트됩니다. 이러한 영역은 전체적으로 분산되며 전 세계의 DNS 이름 서버에서 호스트됩니다.

**DNS 영역** - 도메인은 Domain Name System의 고유 이름입니다(예: 'contoso.com'). DNS 영역은 특정 도메인에 대한 DNS 레코드를 호스트하는 데 사용됩니다. 예를 들어 'contoso.com' 도메인은 'mail.contoso.com'(메일 서버) 및 'www.contoso.com'(웹 사이트)과 같은 여러 DNS 레코드를 포함할 수 있습니다.

**도메인 등록 기관** - 도메인 등록 기관은 인터넷 도메인 이름을 제공할 수 있는 회사입니다. 사용하려는 인터넷 도메인이 사용 가능한지 확인하고 구입할 수 있게 해줍니다. 도메인 이름이 등록되면 도메인 이름에 대한 법적 소유자가 됩니다. 인터넷 도메인이 이미 있는 경우 현재 도메인 등록 기관을 사용하여 Azure DNS에 위임합니다.

공인된 도메인 등록 기관에 대한 자세한 내용은 [ICANN 공인 등록 기관](https://www.icann.org/registrar-reports/accredited-list.html)을 참조하세요.

### <a name="resolution-and-delegation"></a>확인 및 위임

DNS 서버에는 다음 두 가지 유형이 있습니다.

* *권한이 있는* DNS 서버는 DNS 영역을 호스트합니다. 해당 영역의 레코드에 대한 DNS 쿼리에만 대답합니다.
* *재귀적* DNS 서버는 DNS 영역을 호스트하지 않습니다. 권한이 있는 DNS 서버를 호출하고 필요한 데이터를 수집하여 모든 DNS 쿼리에 응답합니다.

Azure DNS는 권한이 있는 DNS 서비스를 제공합니다.  재귀 DNS 서비스를 제공하지 않습니다. Azure의 Cloud Services 및 VM은 자동으로 구성하여 Azure 인프라의 일부로 제공되는 재귀 DNS 서비스를 별도로 사용합니다. 이러한 DNS 설정을 변경하는 방법에 대한 정보는 [Azure에서 이름 확인](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)을 참조하세요.

PC 또는 모바일 장치의 DNS 클라이언트는 일반적으로 재귀적 DNS 서버를 호출하여 클라이언트 애플리케이션에 필요한 DNS 쿼리를 수행합니다.

재귀적 DNS 서버는 'www.contoso.com'과 같은 DNS 레코드에 대한 쿼리를 받을 경우 먼저 'contoso.com' 도메인에 대한 영역을 호스트하는 이름 서버를 찾아야 합니다. 이름 서버를 찾으려면 루트 이름 서버에서 시작하여 'com' 영역을 호스트하는 이름 서버를 찾습니다. 그런 다음 'com' 이름 서버를 쿼리하여 'contoso.com' 영역을 호스트하는 이름 서버를 찾습니다.  끝으로, 이러한 이름 서버에 'www.contoso.com'을 쿼리할 수 있습니다.

이 절차는 DNS 이름 확인이라고 합니다. 엄격히 말해, DNS 확인에는 CNAME 따르기와 같은 추가 단계가 포함되지만 DNS 위임의 작동 방식을 이해하는 데에는 중요하지 않습니다.

부모 영역은 자식 영역에 대한 이름 서버를 어떻게 ‘가리킬까요'? 이 작업을 위해 NS 레코드(NS는 '이름 서버'의 약자)라는 특수 형식의 DNS 레코드를 사용합니다. 예를 들어 루트 영역에는 'com'에 대한 NS 레코드가 포함되며 'com' 영역에 대한 이름 서버가 표시됩니다. 마찬가지로, 'com' 영역에는 'contoso.com' 영역에 대한 이름 서버를 보여주는 'contoso.com'에 대한 NS 레코드가 포함됩니다. 부모 영역에서 자식 영역에 대한 NS 레코드를 설정하는 작업을 도메인 위임이라고 합니다.

다음 이미지는 DNS 쿼리 예제를 보여 줍니다. contoso.net 및 partners.contoso.net은 Azure DNS 영역입니다.

![Dns-nameserver](./media/dns-domain-delegation/image1.png)

1. 클라이언트가 로컬 DNS 서버에서 `www.partners.contoso.net`을 요청합니다.
2. 로컬 DNS 서버는 해당 레코드를 갖고 있지 않으므로 루트 이름 서버에 요청을 보냅니다.
3. 루트 이름 서버는 해당 레코드를 갖고 있지는 않지만 `.net` 이름 서버의 주소를 알고 있으므로 DNS 서버에 해당 주소를 제공합니다.
4. 로컬 DNS 서버에서 요청을 `.net` 이름 서버로 보냅니다.
5. `.net` 이름 서버는 해당 레코드를 갖고 있지 않지만 `contoso.net` 이름 서버의 주소를 알고 있습니다. 이 경우 Azure DNS에서 호스팅되는 DNS 영역에 대한 이름 서버의 주소를 사용하여 응답합니다.
6. 로컬 DNS 서버에서 요청을 Azure DNS에서 호스팅되는 `contoso.net` 영역의 이름 서버로 보냅니다.
7. `contoso.net` 영역은 해당 레코드를 갖고 있지 않지만 `partners.contoso.net`에 대한 이름 서버를 알고 있으며 해당 주소를 사용하여 응답합니다. 이 경우 Azure DNS에서 호스팅되는 DNS 영역입니다.
8. 로컬 DNS 서버에서 요청을 `partners.contoso.net` 영역의 이름 서버로 보냅니다.
9. `partners.contoso.net` 영역은 A 레코드를 갖고 있으며 IP 주소를 사용하여 응답합니다.
10. 로컬 DNS 서버에서 IP 주소를 클라이언트에 제공합니다.
11. 클라이언트가 `www.partners.contoso.net` 웹 사이트에 연결합니다.

실제로 각 위임에는 NS 레코드의 두 복사본이 있습니다. 하나는 자식을 가리키는 부모 영역의 NS 레코드이고 다른 하나는 자식 영역 자체의 NS 레코드입니다. 'contoso.net' 영역은 'net'의 NS 레코드뿐 아니라 'contoso.net'의 NS 레코드를 포함합니다. 이 레코드는 권한이 있는 NS 레코드라고 하며 하위 영역의 루트에 있습니다.

## <a name="next-steps"></a>다음 단계

[Azure DNS에 도메인을 위임](dns-delegate-domain-azure-dns.md)하는 방법을 알아봅니다.

