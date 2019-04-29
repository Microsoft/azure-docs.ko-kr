---
title: Azure DNS 문제 해결 가이드 | Microsoft Docs
description: Azure DNS와 관련된 일반적인 문제를 해결하는 방법
services: dns
documentationcenter: na
author: genlin
manager: cshepard
editor: ''
ms.assetid: 95b01dc3-ee69-4575-a259-4227131e4f9c
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/20/2017
ms.author: genli
ms.openlocfilehash: 535e7604915555f32a7636b739c49f72cb0220c8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60823898"
---
# <a name="azure-dns-troubleshooting-guide"></a>Azure DNS 문제 해결 가이드

이 페이지에서는 일반적인 Azure DNS 질문에 대한 문제 해결 정보를 제공합니다.

이 단계에서 문제를 해결하지 못하는 경우 [MSDN의 커뮤니티 지원 포럼](https://social.msdn.microsoft.com/Forums/en-US/home?forum=WAVirtualMachinesVirtualNetwork)에서 검색하거나 문제를 게시할 수 있습니다. 또는 Azure 지원 요청을 방문하세요.


## <a name="i-cant-create-a-dns-zone"></a>DNS 영역을 만들 수 없습니다.

일반적인 문제를 해결하려면 다음 단계 중 하나 이상을 수행해 봅니다.

1.  Azure DNS 감사 로그를 검토하여 실패 이유를 확인합니다.
2.  각 DNS 영역 이름은 해당 리소스 그룹 내에서 고유해야 합니다. 즉, 이름이 같은 두 개의 DNS 영역이 리소스 그룹을 공유할 수 없습니다. 다른 영역 이름을 사용하거나 다른 리소스 그룹을 사용해 보세요.
3.  "{subscription id} 구독에서 영역의 최대 수에 도달했거나 최대 수를 초과했습니다."라는 오류가 표시될 수 있습니다. 다른 Azure 구독을 사용하거나 일부 영역을 삭제하세요. 아니면 Azure 지원부에 문의하여 구독 제한을 높이세요.
4.  "영역 '{zone name}'을(를) 사용할 수 없습니다."라는 오류가 표시될 수 있습니다. 이 오류는 Azure DNS가 이 DNS 영역에 대해 이름 서버를 할당할 수 없음을 의미합니다. 다른 영역 이름을 사용해 보세요. 또는 도메인 이름 소유자인 경우에는 Azure 지원에 문의하세요. Azure 지원에서 이름 서버를 대신 할당해 드릴 수 있습니다.


### <a name="recommended-documents"></a>**권장되는 문서**

[DNS 영역 및 레코드](dns-zones-records.md)
<br>
[DNS 영역 만들기](dns-getstarted-create-dnszone-portal.md)

## <a name="i-cant-create-a-dns-record"></a>DNS 레코드를 만들 수 없습니다.

일반적인 문제를 해결하려면 다음 단계 중 하나 이상을 수행해 봅니다.

1.  Azure DNS 감사 로그를 검토하여 실패 이유를 확인합니다.
2.  레코드 집합이 이미 있습니까?  Azure DNS는 레코드 *집합*, 다시 말해서 이름과 유형이 같은 레코드 컬렉션을 사용하여 레코드를 관리합니다. 이름과 유형이 같은 레코드가 이미 있는 상태에서 같은 레코드를 추가하려면 기존 레코드 집합을 편집해야 합니다.
3.  DNS 영역 루트(영역의 '루트')에서 레코드를 만들려고 하시나요? 그렇다면 레코드 이름으로 ‘@’ 문자를 사용하는 것이 DNS 규칙입니다. 또한 DNS 표준은 영역 루트에 CNAME 레코드를 허용하지 않습니다.
4.  CNAME 충돌이 있나요?  DNS 표준은 다른 유형의 레코드와 이름이 같은 CNAME 레코드를 허용하지 않습니다. 기존 CNAME이 있으면 다른 유형의 레코드와 이름이 같은 레코드를 만들 수 없습니다.  마찬가지로 유형이 다른 기존 레코드와 이름이 일치하면 CNAME을 만들 수 없습니다. 다른 레코드를 제거하거나 다른 레코드 이름을 선택하여 충돌을 제거하세요.
5.  DNS 영역에서 허용되는 레코드 집합 수 제한에 도달했나요? 현재 레코드 집합 수와 최대 레코드 집합 수는 Azure Portal에서 해당 영역의 '속성'에 표시됩니다. 이 제한에 도달했으면 일부 레코드 집합을 삭제하거나 Azure 지원에 문의하여 이 영역의 레코드 집합 제한을 높인 후 다시 시도하세요. 


### <a name="recommended-documents"></a>**권장되는 문서**

[DNS 영역 및 레코드](dns-zones-records.md)
<br>
[DNS 영역 만들기](dns-getstarted-create-dnszone-portal.md)



## <a name="i-cant-resolve-my-dns-record"></a>DNS 레코드를 확인할 수 없음

DNS 이름 확인은 여러 가지 이유로 실패할 수 있는 다단계 프로세스입니다. 다음 단계를 따르면 Azure DNS에 호스트된 영역의 DNS 레코드에 대한 DNS 확인이 실패하는 이유를 조사할 수 있습니다.

1.  Azure DNS에서 DNS 레코드가 올바르게 구성되어 있는지 확인합니다. Azure Portal에서 DNS 레코드를 검토하여 영역 이름, 레코드 이름 및 레코드 유형이 올바른지 확인합니다.
2.  Azure DNS 이름 서버에서 DNS 레코드가 올바르게 확인되는지 확인합니다.
    - 로컬 PC에서 DNS 쿼리를 작성할 수 있다면 이름 서버의 현재 상태를 반영하지 않는 캐시된 결과가 표시될 수 있습니다.  또한 회사 네트워크는 종종 DNS 프록시 서버를 사용하는데, 이 서버는 DNS 쿼리를 특정 이름 서버로 보내지 못하게 합니다.  이러한 문제를 방지하려면 [digwebinterface](https://digwebinterface.com) 같은 웹 기반 이름 확인 서비스를 사용하세요.
    - Azure Portal에 표시된 대로 DNS 영역의 올바른 이름 서버를 지정하세요.
    - DNS 이름이 올바른지(영역 이름을 포함하여 정규화된 이름을 지정해야 함), 또 레코드 유형이 올바른지 확인합니다.
3.  DNS 도메인 이름이 [Azure DNS 이름 서버에 올바르게 위임되었는지](dns-domain-delegation.md) 확인합니다. [DNS 위임 유효성 검사를 제공하는 여러 타사 웹 사이트](https://www.bing.com/search?q=dns+check+tool)가 있습니다. 이 테스트는 *영역* 위임 테스트이므로 정규화된 레코드 이름이 아닌 DNS 영역 이름만 입력해야 합니다.
4.  위의 단계를 완료하면 DNS 레코드가 올바르게 확인될 것입니다. [digwebinterface](https://digwebinterface.com)를 사용하여 문제가 해결되었는지 확인할 수 있습니다. 이번에는 기본 이름 서버 설정을 사용하면 됩니다.


### <a name="recommended-documents"></a>**권장되는 문서**

[Azure DNS에 도메인 위임](dns-domain-delegation.md)



## <a name="how-do-i-specify-the-service-and-protocol-for-an-srv-record"></a>SRV 레코드에 대한 ‘서비스’ 및 ‘프로토콜’을 지정하려면 어떻게 해냐 하나요?

Azure DNS는 DNS 레코드를 레코드 집합, 다시 말해서 이름과 유형이 같은 레코드 컬렉션으로 관리합니다. SRV 레코드 집합의 경우 레코드 집합 이름에 포함하여 '서비스'와 '프로토콜'을 지정해야 합니다. 기타 SRV 매개 변수('우선 순위', '가중치', '포트', '대상')는 레코드 집합의 각 레코드에 대해 별도로 지정합니다.

SRV 레코드 이름(서비스 이름 'sip', 프로토콜 'tcp') 예제:

- \_sip.\_tcp (영역 루트에 레코드 생성)
- \_sip.\_tcp.sipservice ('sipservice'라는 이름의 레코드 생성)

### <a name="recommended-documents"></a>**권장되는 문서**

[DNS 영역 및 레코드](dns-zones-records.md)
<br>
[Azure Portal을 사용하여 DNS 레코드 집합 및 레코드 만들기](dns-getstarted-create-recordset-portal.md)
<br>
[SRV 레코드 형식(Wikipedia)](https://en.wikipedia.org/wiki/SRV_record)


## <a name="next-steps"></a>다음 단계

* [Azure DNS 영역 및 레코드](dns-zones-records.md) 알아보기
* Azure DNS 사용을 시작하려면 [DNS 영역 만들기](dns-getstarted-create-dnszone-portal.md) 및 [DNS 레코드 만들기](dns-getstarted-create-recordset-portal.md) 방법에 대해 알아보세요.
* 기존 DNS 영역을 마이그레이션하려면 [DNS 영역 파일 가져오기 및 내보내기](dns-import-export.md) 방법에 대해 알아보세요.

