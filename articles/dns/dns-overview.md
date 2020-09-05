---
title: Azure DNS란?
description: Microsoft Azure의 DNS 호스팅 서비스 개요입니다. Microsoft Azure에 도메인을 호스트하세요.
author: rohinkoul
ms.service: dns
ms.topic: overview
ms.date: 3/21/2019
ms.author: rohink
ms.openlocfilehash: d59f166897674e9dcff8086b8f5c906605155ee3
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/07/2020
ms.locfileid: "87924277"
---
# <a name="what-is-azure-dns"></a>Azure DNS란?

Azure DNS는 Microsoft Azure 인프라를 사용하여 이름 확인을 제공하는 DNS 도메인에 대한 호스팅 서비스입니다. Azure에 도메인을 호스트하면 다른 Azure 서비스와 동일한 자격 증명, API, 도구 및 대금 청구를 사용하여 DNS 레코드를 관리할 수 있습니다.

Azure DNS를 사용하여 도메인 이름을 구매할 수 없습니다. 연간 요금의 경우 [App Service 도메인](https://docs.microsoft.com/azure/app-service/manage-custom-dns-buy-domain#buy-the-domain) 또는 타사 도메인 이름 등록자를 사용하여 도메인 이름을 구매할 수 있습니다. 그러면 Azure DNS에 도메인을 호스트하여 레코드 관리에 사용할 수 있습니다. 자세한 내용은 [Azure DNS에 도메인 위임](dns-domain-delegation.md)을 참조하세요.

다음 기능이 Azure DNS에 포함됩니다.

## <a name="reliability-and-performance"></a>안정성 및 성능

Azure DNS의 DNS 도메인은 DNS 이름 서버의 Azure 글로벌 네트워크에 호스팅됩니다. Azure DNS는 애니캐스트 네트워킹을 사용합니다. 도메인에 대해 빠른 성능과 고가용성을 제공하는 가장 가까운 사용 가능한 DNS 서버에서 각 DNS 쿼리에 응답합니다.

## <a name="security"></a>보안

 Azure DNS에서 Azure Resource Manager를 기반으로 하여 제공하는 기능은 다음과 같습니다.

* [Azure RBAC(Azure 역할 기반 액세스 제어)](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) - 조직에 대한 특정 작업에 액세스하는 사람을 제어할 수 있습니다.

* [활동 로그](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) - 문제를 해결할 때 조직의 사용자가 리소스를 수정한 방법을 모니터링하거나 오류를 찾을 수 있습니다.

* [리소스 잠금](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-lock-resources) - 구독, 리소스 그룹 또는 리소스를 잠급니다. 잠금은 조직의 다른 사용자가 실수로 중요한 리소스를 삭제하거나 수정하는 것을 방지합니다.

자세한 내용은 [DNS 영역 및 레코드를 보호하는 방법](dns-protect-zones-recordsets.md)을 참조하세요. 

## <a name="dnssec"></a>DNSSEC

Azure DNS는 현재 DNSSEC를 지원하지 않습니다. 대부분의 경우 애플리케이션에서 HTTPS/TLS를 일관되게 사용하여 DNSSEC에 대한 필요성을 줄일 수 있습니다. DNSSEC가 DNS 영역에 대한 중요한 요구 사항인 경우 타사 DNS 호스팅 공급자에서 이러한 영역을 호스팅할 수 있습니다.

## <a name="ease-of-use"></a>사용 편의성

 Azure DNS는 Azure 서비스에 대한 DNS 레코드를 관리하고, 외부 리소스에 대한 DNS도 제공할 수 있습니다. Azure DNS는 Azure Portal에 통합되며 다른 Azure 서비스와 동일한 자격 증명, 지원 계약 및 청구를 사용합니다. 

DNS 요금 청구는 Azure에서 호스팅되는 DNS 영역의 수와 수신되는 DNS 쿼리의 수에 따라 결정됩니다. 가격 책정에 대한 자세한 내용은 [Azure DNS 가격 책정](https://azure.microsoft.com/pricing/details/dns/)을 참조하세요.

도메인과 레코드는 Azure Portal, Azure PowerShell cmdlet 및 플랫폼 간 Azure CLI를 사용하여 관리할 수 있습니다. 자동 DNS 관리가 필요한 애플리케이션은 REST API 및 SDK를 사용하여 서비스와 통합할 수 있습니다.

## <a name="customizable-virtual-networks-with-private-domains"></a>프라이빗 도메인을 사용하여 사용자 지정할 수 있는 가상 네트워크

Azure DNS는 프라이빗 DNS 도메인도 지원합니다. 이 기능을 통해 현재 Azure에서 제공하는 이름 대신 사용자 고유의 사용자 지정 도메인 이름을 프라이빗 가상 네트워크에 사용할 수 있습니다.

자세한 내용은 [프라이빗 도메인에 Azure DNS 사용](private-dns-overview.md)을 참조하세요.

## <a name="alias-records"></a>별칭 레코드

Azure DNS는 별칭 레코드 집합을 지원합니다. 별칭 레코드 세트를 사용하여 Azure 공용 IP 주소, Azure Traffic Manager 프로필 또는 Azure CDN(Content Delivery Network) 엔드포인트와 같은 Azure 리소스를 참조할 수 있습니다. 기본 리소스의 IP 주소가 변경되면 별칭 레코드 집합도 DNS 확인 중에 자체적으로 원활하게 업데이트됩니다. 별칭 레코드 집합은 서비스 인스턴스를 가리키며 서비스 인스턴스는 IP 주소에 연결됩니다.

또한 이제는 별칭 레코드를 사용하여 apex 또는 naked 도메인을 Traffic Manager 프로필 또는 CDN 엔드포인트로 지정할 수도 있습니다. 예를 들어 contoso.com이 있습니다.

자세한 내용은 [Azure DNS 별칭 레코드 개요](dns-alias.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

* DNS 영역 및 레코드에 대해 알아보려면 [DNS 영역 및 레코드 개요](dns-zones-records.md)를 참조하세요.

* Azure DNS에 영역을 만드는 방법을 알아보려면 [DNS 영역 만들기](./dns-getstarted-create-dnszone-portal.md)를 참조하세요.

* Azure DNS에 대한 질문과 대답을 보려면 [Azure DNS FAQ](dns-faq.md)를 참조하세요.
