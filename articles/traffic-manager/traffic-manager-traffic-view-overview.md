---
title: Azure Traffic Manager의 트래픽 보기
description: 이 소개에서는 Traffic manager 트래픽 보기의 작동 방식에 대해 알아봅니다.
services: traffic-manager
documentationcenter: traffic-manager
author: rohinkoul
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/16/2018
ms.author: rohink
ms.custom: ''
ms.openlocfilehash: d5a03fde564b14baee97f50fa63fd58bf83694b7
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/01/2020
ms.locfileid: "76938351"
---
# <a name="traffic-manager-traffic-view"></a>Traffic Manager 트래픽 보기

Traffic Manager는 DNS 수준 라우팅을 제공하므로 최종 사용자가 프로필을 만들 때 지정한 라우팅 방법을 기준으로 정상 상태의 엔드포인트로 라우팅될 수 있습니다. 트래픽 보기를 통해 Traffic Manager에는 사용자 기반(DNS 확인자 세분성 수준) 및 해당 트래픽 패턴 보기가 제공됩니다. 트래픽 보기를 사용하도록 설정하면 실행 가능한 정보를 제공할 수 있게 처리됩니다. 

트래픽 보기를 사용하여 다음을 수행할 수 있습니다.
- 사용자 기반이 있는 위치를 이해합니다(로컬 DNS 확인자 수준 세분성까지).
- 이 지역에서 생성된 트래픽 볼륨을 확인합니다(Azure Traffic Manager가 처리하는 DNS 쿼리로 관찰).
- 이러한 사용자가 경험한 대표적 대기 시간 정보를 파악합니다.
- 이러한 각 사용자 기반에서 엔드포인트가 있는 Azure 지역까지 구체적인 트래픽 패턴을 심층 탐구합니다. 

예를 들어 트래픽 보기를 사용하여 많은 수의 트래픽이 발생하지만 높은 대기 시간 문제를 겪고 있는 지역을 이해할 수 있습니다. 다음으로 새 Azure 지역에 대한 확장 계획을 세울 때 이 정보를 참조하여 사용자들이 대기 시간 문제를 덜 겪도록 할 수 있습니다.

## <a name="how-traffic-view-works"></a>트래픽 보기 작동 방식

트래픽 보기는 Traffic Manager가 이 기능이 사용되도록 설정된 프로필을 기준으로 지난 7일 동안 수신된 들어오는 쿼리를 비교할 수 있도록 합니다. 들어오는 쿼리 정보를 통해 트래픽 보기는 사용자 위치를 나타내는 데 사용되는 DNS 확인자의 원본 IP 주소를 추출합니다. 이러한 항목은 DNS 확인자 수준 세분성에서 함께 그룹화되며, Traffic Manager가 유지 관리하는 IP 주소의 지리적 정보를 사용하여 사용자 기반 지역이 만들어집니다. 그런 후 Traffic Manager는 쿼리가 라우팅된 Azure 지역을 확인하고, 해당 지역으로부터 사용자에 대한 트래픽 흐름 맵을 생성합니다.  
다음 단계에서 Traffic Manager는 사용자 기반 영역- Azure 지역 매핑과 여러 다른 최종 사용자 네트워크에 대해 유지 관리되는 네트워크 인텔리전스 대기 시간 표를 분석하고 상관 관계를 파악하여 Azure 지역에 연결될 때 해당 지역의 사용자가 겪게 되는 평균 대기 시간을 이해합니다. 그런 다음 이러한 모든 계산은 먼저 로컬 DNS 확인자별 IP 수준에서 결합된 후 사용자에게 제공됩니다. 다양한 방법으로 정보를 사용할 수 있습니다.

트래픽 보기 데이터 업데이트의 빈도는 여러 내부 서비스 변수에 따라 달라 집니다. 그러나 일반적으로 데이터는 24 시간 마다 한 번씩 업데이트 됩니다.

>[!NOTE]
>트래픽 보기에 설명된 대기 시간은 최종 사용자와 연결된 Azure 지역 간의 대표적인 대기 시간이며 DNS 조회 대기 시간이 아닙니다. 트래픽 보기에서는 로컬 DNS 해결 프로그램과 쿼리가 라우팅된 Azure 지역 간의 대기 시간을 측정하기 위해 최선을 다합니다. 사용할 수 있는 데이터가 충분치 않은 경우 반환되는 대기 시간은 null이 됩니다. 

## <a name="visual-overview"></a>시각적 개요

Traffic Manager 페이지에서 **트래픽 보기** 섹션으로 이동하면 트래픽 보기 정보의 오버레이가 있는 지리적 맵이 제공됩니다. 맵에는 Traffic Manager 프로필의 사용자 기반 및 엔드포인트에 대한 정보가 제공됩니다.

![Traffic Manager 트래픽 보기 지리적 보기][1]

### <a name="user-base-information"></a>사용자 기반 정보

위치 정보를 사용할 수 있는 해당 로컬 DNS 확인자의 경우 맵에 표시됩니다. DNS 확인자의 색상은 Traffic Manager 쿼리에 해당 DNS 확인자를 사용하는 최종 사용자에게 발생하는 평균 대기 시간을 나타냅니다.

맵에서 DNS 확인자 위치를 가리키면 다음이 표시됩니다.
- DNS 확인자의 IP 주소
- Traffic Manager에서 표시하는 DNS 쿼리 트래픽 볼륨
- DNS 확인자의 트래픽에서 라우트된 엔드포인트, 엔드포인트와 DNS 확인자 사이의 선으로 표시됨 
- 해당 위치에서 엔드포인트까지 평균 대기 시간, 이 둘을 연결하는 선의 색상으로 표시됨

### <a name="endpoint-information"></a>엔드포인트 정보

엔드포인트가 있는 Azure 지역은 맵에서 파란색 점으로 표시됩니다. 엔드포인트가 외부에 있거나 매핑된 Azure 지역에 없는 경우 맵 위쪽에 표시됩니다. 엔드포인트를 클릭하면 해당 엔드포인트에 트래픽이 전송된 위치와 다른 위치(사용되는 DNS 확인자 기준)가 표시됩니다. 연결은 엔드포인트와 DNS 확인자 위치 사이에 선으로 표시되고 해당 쌍 간의 대표 대기 시간에 따라 색상이 지정됩니다. 또한 엔드포인트의 이름과 실행되는 Azure 지역, 이 Traffic Manager 프로필에서 전송한 요청의 총 볼륨을 볼 수 있습니다.


## <a name="tabular-listing-and-raw-data-download"></a>테이블 형식 목록 및 원시 데이터 다운로드

Azure Portal에서 테이블 형식으로 트래픽 보기 데이터를 볼 수 있습니다. 각 DNS 확인자 IP/엔드포인트 쌍에 DNS 확인자의 IP 주소, 엔드포인트가 위치한(있는 경우) Azure 지역의 이름 및 지리적 위치, 해당 DNS 확인자부터 해당 엔드포인트와 관련된 요청 볼륨 및 해당 DNS(사용 가능한 경우)를 사용하는 최종 사용자와 관련된 대표 대기 시간을 표시하는 항목이 있습니다. 또한 CSV 파일로 트래픽 보기 데이터를 다운로드하여 원하는 분석 워크플로의 일부로 사용할 수 있습니다.

## <a name="billing"></a>청구

트래픽 보기를 사용할 경우 제공되는 정보를 생성하는 데 사용된 데이터 요소 수에 따라 요금이 청구됩니다. 현재 사용되는 유일한 데이터 요소 형식은 Traffic Manager 프로필에 대해 수신된 쿼리입니다. 가격 책정에 대한 자세한 내용은 [Traffic Manager 가격 책정 페이지](https://azure.microsoft.com/pricing/details/traffic-manager/)를 참조하세요.

## <a name="faqs"></a>FAQ

* [트래픽 보기은 무엇 인가요?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-does-traffic-view-do)

* [트래픽 보기를 사용 하 여 혜택을 얻으려면 어떻게 해야 하나요?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-can-i-benefit-from-using-traffic-view)

* [Azure monitor를 통해 사용할 수 있는 Traffic Manager 메트릭과 트래픽 보기는 방법은 무엇 인가요?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-is-traffic-view-different-from-the-traffic-manager-metrics-available-through-azure-monitor)

* [EDNS 클라이언트 서브넷 정보를 사용 트래픽 보기?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#does-traffic-view-use-edns-client-subnet-information)

* [트래픽 보기 사용 하는 데이터의 수는 몇 개입니까?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-many-days-of-data-does-traffic-view-use)

* [트래픽 보기는 외부 엔드포인트을 어떻게 처리 하나요?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-does-traffic-view-handle-external-endpoints)

* [내 구독의 각 프로필에 대해 트래픽 보기를 사용 하도록 설정 해야 하나요?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#do-i-need-to-enable-traffic-view-for-each-profile-in-my-subscription)

* [트래픽 보기 해제 하려면 어떻게 해야 하나요?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-can-i-turn-off-traffic-view)

* [트래픽 보기 대금 청구는 어떻게 작동 하나요?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-does-traffic-view-billing-work)

## <a name="next-steps"></a>다음 단계

- [Traffic Manager 작동 방식](traffic-manager-overview.md)
- Traffic Manager가 지원하는 [트래픽 라우팅 방법](traffic-manager-routing-methods.md) 에 대해 자세히 알아봅니다.
- [Traffic Manager 프로필을 만드는](traffic-manager-create-profile.md)

<!--Image references-->
[1]: ./media/traffic-manager-traffic-view-overview/trafficview.png