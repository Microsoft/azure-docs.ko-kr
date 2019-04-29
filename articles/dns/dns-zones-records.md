---
title: DNS 영역 및 레코드 개요-Azure DNS | Microsoft Docs
description: Microsoft Azure DNS에서 DNS 영역 및 레코드 호스팅에 대한 지원 개요.
services: dns
documentationcenter: na
author: vhorne
manager: jeconnoc
editor: ''
ms.assetid: be4580d7-aa1b-4b6b-89a3-0991c0cda897
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.custom: H1Hack27Feb2017
ms.workload: infrastructure-services
ms.date: 12/18/2017
ms.author: victorh
ms.openlocfilehash: 7da382a644c1db92b9915f1d3f1f3a459e8893b8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60563395"
---
# <a name="overview-of-dns-zones-and-records"></a>DNS 영역 및 레코드 개요

이 페이지는 도메인, DNS 영역, DNS 레코드 및 레코드 집합의 핵심 개념과 Azure DNS에서 지원되는 방식에 대해 설명합니다.

## <a name="domain-names"></a>도메인 이름

Domain Name System은 도메인 계층 구조입니다. 계층 구조는 이름이 단순히 '**.**'인 'root' 도메인에서 시작합니다.  그 아래에 'com', 'net', 'org', 'uk' 또는 'jp'와 같은 최상위 도메인이 있습니다.  그 아래에 'org.uk' 또는 'co.jp'와 같은 두 번째 수준의 도메인이 있는 DNS 계층 구조의 도메인은 전체적으로 분산되며 전 세계의 DNS 이름 서버에서 호스팅됩니다.

도메인 이름 등록 기관은 'contoso.com'과 같은 도메인 이름을 구입할 수 있는 조직입니다.  도메인 이름을 구입할 경우 해당 이름 하의 DNS 계층 구조를 관리할 수 있습니다. 예를 들어, 'www.contoso.com'이라는 이름을 회사 웹 사이트에 사용할 수 있습니다. 등록 기관은 사용자 대신 자체 이름 서버에 도메인을 호스트하거나 사용자가 다른 이름 서버를 지정할 수 있습니다.

Azure DNS는 전 세계적으로 분산된 고가용성 이름 서버 인프라를 제공하므로 조직은 이러한 인프라를 사용하여 도메인을 호스팅할 수 있습니다. Azure에 도메인을 호스팅하면 다른 Azure 서비스와 동일한 자격 증명, API, 도구 및 대금 청구를 사용하여 DNS 레코드를 관리할 수 있습니다.

Azure DNS는 현재 도메인 이름 구입을 지원하지 않습니다. 도메인 이름을 구입하려면 타사 도메인 이름 등록 기관을 이용해야 합니다. 등록 기관은 일반적으로 소액의 연간 요금을 부과합니다. 그런 다음, DNS 레코드의 관리를 위해 Azure DNS에 해당 도메인을 호스트할 수 있습니다. 자세한 내용은 [Azure DNS에 도메인 위임](dns-domain-delegation.md) 을 참조하세요.

## <a name="dns-zones"></a>DNS 영역 

[!INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]

## <a name="dns-records"></a>DNS 레코드

[!INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

### <a name="time-to-live"></a>Time-to-Live

Time-to-Live, 즉 TTL은 각 레코드가 다시 쿼리되기 전에 클라이언트에 캐시되는 기간을 지정합니다. 위 예제에서 TTL은 3600초, 즉 1시간입니다.

Azure DNS에서 TTL은 각 레코드가 아니라 레코드 집합에 대해 지정되므로 해당 레코드 집합 내의 모든 레코드에 동일한 값이 사용됩니다.  1 ~ 2,147,483,647초 사이의 TTL 값을 지정할 수 있습니다.

### <a name="wildcard-records"></a>와일드카드 레코드

Azure DNS는 [와일드카드 레코드](https://en.wikipedia.org/wiki/Wildcard_DNS_record)를 지원합니다. 와일드카드 레코드는 쿼리에 대한 응답으로 일치하는 이름을 반환합니다(와일드카드가 아닌 레코드 집합에서 가까운 일치 항목이 없는 경우). Azure DNS는 NS 및 SOA를 제외한 모든 레코드 종류에 대해 와일드카드 레코드 집합을 지원합니다.

와일드카드 레코드 집합을 만들려면 레코드 집합 이름 '\*'을 사용합니다. 또는 맨 왼쪽의 레이블로 '\*'가 포함된 이름을 사용할 수 있습니다(예: '\*.foo').

### <a name="caa-records"></a>CAA 레코드

CAA 레코드를 사용하면 도메인 소유자가 자신의 도메인에 대한 인증서를 발급할 권한이 있는 CA(인증 기관)를 지정할 수 있습니다. 이렇게 하면 일부 환경에서는 인증서가 착오 발급되는 것을 방지할 수 있습니다. CAA 레코드에는 다음 세 가지 속성이 있습니다.
* **플래그**: 이 0과 당 특별 한 의미가 있는 중요 한 플래그를 나타내는 데 255 사이의 정수를 [RFC](https://tools.ietf.org/html/rfc6844#section-3)
* **Tag**: ASCII 문자열이며 다음 중 하나일 수 있습니다.
    * **issue**: 인증서(모든 유형)를 발급할 권한이 있는 CA를 지정하려는 경우에 사용합니다.
    * **issuewild**: 인증서(와일드카드 인증서만 해당)를 발급할 권한이 있는 CA를 지정하려는 경우에 사용합니다.
    * **iodef**: 권한이 없는 인증서 발급 요청에 대해 알릴 수 있는 메일 주소 또는 호스트 이름을 지정합니다.
* **Value**: 선택한 특정 태그에 대한 값입니다.

### <a name="cname-records"></a>CNAME 레코드

CNAME 레코드 집합은 동일한 이름의 다른 레코드 집합과 함께 존재할 수 없습니다. 예를 들어 상대 이름이 'www'인 CNAME 레코드 집합과 상대 이름이 'www'인 A 레코드를 동시에 만들 수 없습니다.

영역 루트(이름 = '\@')는 항상 영역을 만들 때 생성된 NS 및 SOA 레코드 집합을 포함하므로 영역 루트에 CNAME 레코드 집합을 만들 수 없습니다.

이러한 제약 조건은 DNS 표준에서 발생하며 Azure DNS의 제한 사항이 아닙니다.

### <a name="ns-records"></a>NS 레코드

영역 루트의 NS 레코드 집합(name '\@')은 각 DNS 영역과 함께 자동으로 생성되며 영역이 삭제될 경우 자동으로 삭제됩니다(별도로 삭제할 수 없음).

이 레코드 집합에는 영역에 할당된 Azure DNS 이름 서버의 이름이 포함됩니다. 이 NS 레코드 집합에 추가 이름 서버를 추가하여 DNS 공급자가 2개 이상 있는 공동 호스팅 도메인을 지원할 수 있습니다. 또한 이 레코드 집합의 TTL 및 메타데이터를 수정할 수 있습니다.또한 이 레코드 집합의 TTL 및 메타데이터를 수정할 수 있습니다. 그러나 미리 채워진 Azure DNS 이름 서버를 제거 또는 수정할 수 없습니다. 

이는 영역 루트에 있는 NS 레코드 집합에만 적용됩니다. 영역의 다른 NS 레코드 집합은 제약 없이 생성, 수정 및 삭제할 수 있습니다(자식 영역을 위임하는 데 사용되므로).

### <a name="soa-records"></a>SOA 레코드

SOA 레코드는 각 영역의 루트(name = '\@')에서 자동으로 생성되며 영역이 삭제될 경우 자동으로 삭제됩니다.  SOA 레코드는 별도로 생성 또는 삭제할 수 없습니다.

SOA 레코드에서 'host' 속성(Azure DNS에서 제공한 기본 이름 서버 이름을 참조하도록 사전 구성됨)을 제외한 모든 속성을 수정할 수 있습니다.

### <a name="spf-records"></a>SPF 레코드

[!INCLUDE [dns-spf-include](../../includes/dns-spf-include.md)]

### <a name="srv-records"></a>SRV 레코드

[SRV 레코드](https://en.wikipedia.org/wiki/SRV_record)는 다양한 서비스에서 서버 위치를 지정하는 데 사용됩니다. Azure DNS에서 SRV 레코드를 지정할 경우

* *서비스*와 *프로토콜*은 레코드 집합 이름에 포함하여 지정하고 밑줄로 접두사를 지정해야 합니다.  예를 들어 '\_sip.\_tcp.name'입니다.  영역 루트에 있는 레코드의 경우 레코드 이름에 '\@'을 지정하지 않아도 되며 서비스와 프로토콜만 사용합니다(예: '\_sip.\_tcp').
* *우선 순위*, *가중치*, *포트*, *대상*은 레코드 집합에서 각 레코드의 매개 변수로 지정됩니다.

### <a name="txt-records"></a>TXT 레코드

TXT 레코드는 도메인 이름을 임의의 텍스트 문자열에 매핑하는 데 사용됩니다. 이들은 특히 [SPF(Sender Policy Framework)](https://en.wikipedia.org/wiki/Sender_Policy_Framework) 및 [DKIM(DomainKeys Identified Mail)](https://en.wikipedia.org/wiki/DomainKeys_Identified_Mail)과 같은 이메일 구성과 관련된 여러 애플리케이션에서 사용됩니다.

DNS 표준은 여러 문자열을 포함하는 하나의 TXT 레코드를 허용하며 각각의 문자열 길이는 최대 254자까지 가능합니다. 여러 문자열이 사용되는 경우 이러한 문자열은 클라이언트에 의해 연결되고 단일 문자열로 처리됩니다.

Azure DNS REST API를 호출할 때 각 TXT 문자열을 별도로 지정해야 합니다.  Azure Portal, PowerShell 또는 CLI 인터페이스를 사용할 때는 레코드당 하나의 문자열을 지정해야 하며 필요한 경우 자동으로 254자 세그먼트로 나뉩니다.

DNS 레코드의 여러 문자열을 TXT 레코드 집합의 여러 TXT 레코드와 혼동해서는 안 됩니다.  TXT 레코드 집합은 여러 레코드를 포함할 수 있으며 *각각의 레코드*는 여러 문자열을 포함할 수 있습니다.  Azure DNS는 모든 레코드가 결합된 각 TXT 레코드 집합에서 총 문자열 길이를 최대 1024자까지 지원합니다.

## <a name="tags-and-metadata"></a>태그 및 메타데이터

### <a name="tags"></a>태그들

태그는 이름-값 쌍의 목록으로, Azure Resource Manager에서 리소스에 레이블을 지정하는 데 사용됩니다.  Azure Resource Manager는 태그를 사용하여 Azure 청구서를 필터링하여 표시할 수 있으며 태그가 필요한 정책을 설정할 수 있습니다. 태그에 대한 자세한 내용은 [태그를 사용하여 Azure 리소스 구성](../azure-resource-manager/resource-group-using-tags.md)을 참조하십시오.

Azure DNS에서는 DNS 영역 리소스에 Azure Resource Manager 태그를 사용할 수 있으며  아래 설명된 대로 대체 ‘메타데이터’가 DNS 레코드 집합에서 지원되기는 하지만 DNS 레코드 집합의 태그는 지원하지 않습니다.

### <a name="metadata"></a>Metadata

Azure DNS에서는 레코드 집합 태그 대신 '메타데이터'를 사용하여 레코드 집합에 주석을 추가할 수 있습니다.  태그와 유사한 메타데이터를 사용하면 각 레코드 집합에 이름-값 쌍을 연결할 수 있습니다.  이는 각 레코드 집합의 목적을 기록하는 데 유용할 수 있습니다.  태그와 달리, 메타데이터는 Azure 청구서를 필터링하여 표시하는 데 사용할 수 없으며 Azure Resource Manager 정책에서 지정할 수 없습니다.

## <a name="etags"></a>Etag

두 사용자 또는 두 프로세스가 동시에 DNS 레코드 수정을 시도한다고 가정합니다. 어느 쪽이 성공할까요? 그리고 성공한 사용자 자신이 다른 사용자가 만든 변경 내용을 덮어썼다는 것을 알고 있을까요?

Azure DNS는 Etag를 사용하여 동일한 리소스에 대한 동시 변경을 안전하게 처리합니다. Etags는 [Azure Resource Manager '태그'](#tags)와 구분됩니다. 각 DNS 리소스(영역 또는 레코드 집합)에는 Etag가 연결되어 있습니다. 리소스를 검색할 때마다 해당 Etag도 검색됩니다. 리소스를 업데이트할 때 Azure DNS에서 서버의 Etag가 일치하는지 확인할 수 있도록 Etag를 다시 전달하도록 선택할 수 있습니다. 리소스를 업데이트할 때마다 Etag가 다시 생성되므로 Etag 불일치는 동시 변경이 발생했음을 나타냅니다. 새 리소스를 만들 때도 Etag를 사용하여 리소스가 존재하지 않는지 확인합니다.

기본적으로 Azure DNS PowerShell은 Etag를 사용하여 영역 및 레코드 집합에 대한 동시 변경을 차단합니다. 선택적 *-Overwrite* 스위치를 사용하여 Etag 검사를 무시할 수 있으며, 이 경우 발생한 동시 변경 내용을 덮어쓰게 됩니다.

Azure DNS REST API 수준에서 Etag는 HTTP 헤더를 사용하여 지정됩니다.  해당 동작은 다음 표에 나와 있습니다.

| 헤더 | 동작 |
| --- | --- |
| 없음 |PUT 항상 성공(Etag 검사 안 함) |
| If-match <etag> |리소스가 있고 Etag가 일치하는 경우에만 PUT 성공 |
| If-match * |리소스가 있는 경우에만 PUT 성공 |
| If-none-match * |리소스가 없는 경우에만 PUT 성공 |


## <a name="limits"></a>제한

Azure DNS를 사용할 경우 다음과 같은 기본 제한이 적용됩니다.

[!INCLUDE [dns-limits](../../includes/dns-limits.md)]

## <a name="next-steps"></a>다음 단계

* Azure DNS 사용을 시작하려면 [DNS 영역 만들기](dns-getstarted-create-dnszone-portal.md) 및 [DNS 레코드 만들기](dns-getstarted-create-recordset-portal.md) 방법에 대해 알아보세요.
* 기존 DNS 영역을 마이그레이션하려면 [DNS 영역 파일 가져오기 및 내보내기](dns-import-export.md) 방법에 대해 알아보세요.
