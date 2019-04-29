---
author: WenJason
ms.service: dns
ms.topic: include
origin.date: 11/25/2018
ms.date: 12/24/2018
ms.author: v-jay
ms.openlocfilehash: 9cc650cea17acb8d89933c819c4ca60e2c459d1c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60563340"
---
SPF(Sender Policy Framework) 레코드는 도메인 이름 대신 이메일을 전송할 수 있는 이메일 서버를 지정하는 데 사용합니다. SPF 레코드를 올바르게 구성하는 것은 수신자가 이메일을 정크로 지정하지 않도록 하는 데 중요합니다.

DNS RFC는 원래 이 시나리오를 지원하기 위한 새로운 SPF 레코드 유형으로 소개되었습니다. 기존 이름 서버를 지원하기 위해 TXT 레코드 유형을 사용하여 SPF 레코드를 지정할 수도 있습니다. 이러한 모호성으로 인한 혼란은 [RFC 7208](http://tools.ietf.org/html/rfc7208#section-3.1)로 해결되었습니다. 여기서는 SPF 레코드를 TXT 레코드 형식을 사용하여 만들어야 한다고 명시합니다. 또한 SPF 레코드 유형이 더 이상 사용되지 않는다고 나와 있습니다.

**SPF 레코드는 Azure DNS에서 지원되며 TXT 레코드 유형을 사용하여 만들어야 합니다.** 사용하지 않는 SPF 레코드 유형은 지원되지 않습니다. [DNS 영역 파일을 가져올 경우](../articles/dns/dns-import-export.md) SPF 레코드 유형을 사용하는 SPF 레코드는 TXT 레코드 유형으로 전환됩니다.
