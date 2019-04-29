---
author: WenJason
ms.service: dns
ms.topic: include
origin.date: 11/25/2018
ms.date: 12/24/2018
ms.author: victorh
ms.openlocfilehash: 74031a8dbc9b64d6a09533789eed1296ff334d47
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60563381"
---
DNS 영역은 특정 도메인에 대한 DNS 레코드를 호스트하는 데 사용됩니다. Azure DNS에서 도메인 호스팅을 시작하려면 해당 도메인 이름의 DNS 영역을 만들어야 합니다. 그러면 이 DNS 영역 안에 도메인의 각 DNS 레코드가 생성됩니다.

예를 들어 'contoso.com' 도메인은 'mail.contoso.com'(메일 서버) 및 'www.contoso.com'(웹 사이트)과 같은 여러 DNS 레코드를 포함할 수 있습니다.

Azure DNS에서 DNS 영역을 만드는 경우:

* 영역 이름은 리소스 그룹 내에서 고유해야 하며, 영역이 존재해서는 안 됩니다. 그렇지 않으면 작업이 실패합니다.
* 서로 다른 리소스 그룹이나 Azure 구독에서는 동일한 영역 이름을 다시 사용할 수 있습니다.
* 복수 영역이 동일한 이름을 공유할 경우 인스턴스는 각각 다른 이름 서버 주소에 할당됩니다. 도메인 이름 등록 기관에는 한 가지 주소 집합만 구성할 수 있습니다.

> [!NOTE]
> Azure DNS에서 해당 도메인 이름으로 DNS 영역을 만들기 위해 도메인을 소유할 필요는 없습니다. 하지만 도메인 이름 등록 기관에 도메인 이름의 올바른 이름 서버로 Azure DNS 이름 서버를 구성하려면 도메인을 소유해야 합니다.
> 
> 자세한 내용은 [Azure DNS에 도메인 위임](../articles/dns/dns-domain-delegation.md)을 참조하세요.