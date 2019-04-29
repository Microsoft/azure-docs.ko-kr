---
title: Azure 웹 응용 프로그램 방화벽-질문과 대답
description: 이 페이지에서는 Azure 프런트 도어 서비스에 대 한 질문과 대답
services: frontdoor
documentationcenter: ''
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/02/2019
ms.author: kumud;tyao
ms.openlocfilehash: 05d01851d0a3dc9df6c396e862ce93defd957c70
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61461972"
---
# <a name="frequently-asked-questions-for-azure-web-application-firewall"></a>Azure 웹 응용 프로그램 방화벽에 대 한 질문과 대답

이 문서에서는 Azure 웹 응용 프로그램 방화벽 (WAF) 기능에 대 한 일반적인 질문에 답변 합니다. 

## <a name="what-is-azure-waf"></a>Azure WAF는 무엇입니까?

Azure WAF는 SQL 주입, 교차 사이트 스크립팅 및 기타 웹 공격 같은 일반적인 위협 으로부터 웹 응용 프로그램 보호는 웹 응용 프로그램 방화벽을 합니다. 웹 응용 프로그램에 대 한 액세스를 제어 하는 사용자 지정 및 관리 되는 규칙의 조합으로 구성 된 WAF 정책을 정의할 수 있습니다.

Application Gateway 또는 Azure 프런트 도어 서비스에서 호스팅되는 웹 응용 프로그램에는 Azure WAF 정책을 적용할 수 있습니다.

## <a name="what-is-waf-for-azure-front-door-service"></a>Azure 프런트 도어 서비스에 대 한 WAF 란? 

Azure 프런트 도어는 뛰어나고 전역으로 분산 된 응용 프로그램 및 콘텐츠 배달 네트워크. Azure WAF와 첫 번째 관문을 통합 하는 경우 거부-서비스를 중지 하 고 대상 Azure 네트워크에 지에서 응용 프로그램 공격, 공격 원본 가까이 가상 네트워크를 입력 하기 전에 제품 보호 성능 저하 없이.

## <a name="how-will-i-be-charged-for-azure-waf-for-front-door"></a>어떻게 요금이 청구 프런트 도어에 대 한 Azure WAF에 대 한?
공개 미리 보기 중 첫 번째 관문에서 WAF 사용이 무료로 제공 됩니다. 첫 번째 관문 요금은 추가 참고 합니다. 첫 번째 관문 Service 가격 책정을 참조 하세요 [여기](https://azure.microsoft.com/pricing/details/frontdoor/)합니다.

## <a name="does-azure-waf-support-https"></a>Azure WAF는 HTTPS를 지원 하나요?

첫 번째 관문 서비스는 SSL 오프 로딩을 제공합니다. WAF는 프런트 도어를 사용 하 여 고유 하 게 통합 및 암호를 해독 한 후 요청을 검사할 수 있습니다.

## <a name="does-azure-waf-support-ipv6"></a>Azure WAF IPv6를 지원 하나요?

예. IPv4 및 IPv6에 대 한 IP 제한을 구성할 수 있습니다.

## <a name="how-up-to-date-are-the-managed-rule-sets"></a>관리 되는 규칙 집합이 얼마나 최신?

최고의 위협 환경 변경 대처 하기 위해 수행 합니다. 새 규칙을 업데이트 되 면 기본 규칙 집합에 새 버전 번호를 사용 하 여 추가 됩니다.

## <a name="what-is-the-propagation-time-if-i-make-a-change-to-my-waf-policy"></a>WAF 정책 내에 변경 전파 시간 이란?

WAF 정책을 배포 하는 전역적으로 일반적으로 5 분 정도의 시간이 소요 되 고 종종 더 빨리를 완료 합니다.

## <a name="can-waf-policies-be-different-for-different-regions"></a>WAF 정책을 서로 다른 지역에 대 한 다양 한 수 있나요?

첫 번째 관문 서비스와 통합 면, WAF 전역 리소스입니다. 모든 프런트 도어 위치에서 동일한 구성을 적용 됩니다.
 
## <a name="how-do-i-limit-access-to-my-back-end-to-be-from-front-door-only"></a>내 백 엔드에만 첫 번째 관문에서 되도록 액세스 제한지 않습니다는 방법

IP 액세스 제어 목록만 첫 번째 관문 아웃 바운드 IP 주소 범위에 대 한 허용 및 인터넷에서 직접 액세스를 거부 하려면 백 엔드에 구성할 수 있습니다. 서비스 태그는 가상 네트워크에서 사용할 수 있습니다에 대 한 지원 됩니다. 또한 웹 응용 프로그램에 대 한 X 전달 호스트 HTTP 헤더 필드 올바른지 확인할 수 있습니다.




## <a name="which-azure-waf-options-should-i-choose"></a>Azure WAF 옵션을 선택 해야 하나요?

Azure에서 WAF 정책을 적용 하는 경우 두 가지 옵션이 있습니다. WAF Azure 프런트 도어를 사용 하 여 전역적으로 분산 된에 지 보안 솔루션입니다. Application gateway WAF는 지역, 전용 솔루션입니다. 전반적인 성능 및 보안 요구 사항에 따라 솔루션을 선택 하는 것이 좋습니다. 자세한 내용은 [Azure의 응용 프로그램 배달 suite를 사용 하 여 부하 분산](https://docs.microsoft.com/azure/frontdoor/front-door-lb-with-azure-app-delivery-suite)합니다.


## <a name="do-you-support-same-waf-features-in-all-integrated-platforms"></a>모든 통합된 플랫폼에 동일한 WAF 기능을 지원 하나요?

현재 ModSec CRS 2.2.9 및 CRS 3.0 규칙 Application Gateway에서 WAF를 사용 하 여 에서만 지원 됩니다. 속도 제한, 지역 필터링 및 Azure 관리 되는 기본 규칙 집합 규칙은 Azure 프런트 도어에서 WAF를 사용 하 여만 지원 됩니다.

## <a name="is-ddos-protection-integrated-with-front-door"></a>DDoS protection 프런트 도어와 통합 되어 있습니까? 

Azure 프런트 도어 수 Azure 네트워크 가장자리에서 전체적으로 분산 absorb 고 지리적으로 대규모 공격을 격리 합니다. 사용자 지정 WAF 정책을 자동으로 블록 및 속도 제한을 http (s) 공격 서명을 알려진가 있는 만들 수 있습니다. 추가로 더 설정할 수 있습니다. 백 엔드 배포 되는 위치는 VNet에서 DDoS Protection 표준입니다. Azure DDoS Protection 표준은 고객 공격 하는 동안 비용 보호, SLA 보장 및 즉시 도움말에 대 한 DDoS 신속한 대응 팀에서 전문가 게 액세스를 비롯 한 추가 혜택을 받습니다. 

## <a name="next-steps"></a>다음 단계

- 에 대 한 자세한 [Azure 웹 응용 프로그램 방화벽](waf-overview.md)합니다.
- 에 대해 자세히 알아보세요 [Azure 프런트 도어](front-door-overview.md)합니다.
