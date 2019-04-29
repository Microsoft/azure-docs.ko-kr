---
title: Azure 청구 및 비용 관리 자동화 시나리오 | Microsoft Docs
description: 일반적인 청구 및 비용 관리 시나리오가 다양한 API에 어떻게 매핑되는지 알아봅니다.
services: billing
documentationcenter: ''
author: Erikre
manager: dougeby
editor: ''
tags: billing
ms.assetid: 204b15b2-6667-4b6c-8ea4-f32c06f287fd
ms.service: billing
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: billing
ms.date: 6/13/2018
ms.author: erikre
ms.openlocfilehash: 0d2b0f2d3fad318ac0152d92fe92614d8dadda1e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60615870"
---
# <a name="automation-scenarios-for-billing-and-cost-management"></a>청구 및 비용 관리 자동화 시나리오

이 문서에서는 Azure 청구 및 비용 관리에 대한 일반적인 시나리오를 설명합니다. 또한 이러한 시나리오를 사용할 수 있는 API와 매핑해서 설명합니다. 각 시나리오-API 매핑에서 API의 요약 및 API가 제공하는 기능을 확인할 수 있습니다.

## <a name="common-scenarios"></a>일반적인 시나리오

다양한 시나리오에서 청구 및 비용 관리 API를 사용하여 비용 및 사용량 관련 질문에 답변할 수 있습니다. 일반적인 시나리오의 개요는 다음과 같습니다.

- **청구서 조정**: Microsoft가 적절한 금액을 청구했나요?  내 청구서는 무엇이며 내가 직접 계산할 수 있나요?

- **교차 청구**: 청구 금액은 알았는데, 조직에서 누가 지불해야 하나요?

- **비용 최적화**: 청구 금액이 얼마인지 알고 있습니다. 어떻게 하면 Azure에서 지출하는 것보다 더 많은 돈을 벌 수 있나요?

- **비용 추적**: 시간별로 Azure를 얼마나 많이 사용하고 있으며 비용은 얼마나 되는지 알고 싶습니다. 추세란 무엇인가요? 어떻게 하면 더 잘 수행할 수 있나요?

- **월별 Azure 지출**: 현재까지 이번 달 지출액은 얼마나 되나요? Azure의 지출 및/또는 사용량을 변경해야 하나요? 해당 월 중에 언제 나는 Azure를 가장 많이 소비했나요?

- **경고**: 리소스 기반 소비 또는 통화 기반 알림을 설정하려면 어떻게 하나요?

## <a name="scenario-to-api-mapping"></a>시나리오-API 매핑

|         API        | 청구서 조정    | 교차 청구    | 비용 최적화    | 비용 추적    | 월별 지출    | 경고    |
|:---------------------------:|:-------------------------:|:----------------:|:--------------------:|:----------------:|:------------------:|:---------:|
| 예산                     |                           |                  |           X          |                  |                    |     X     |
| Marketplace 요금                |             X             |         X        |           X          |         X        |          X         |     X     |
| 가격표                 |             X             |         X        |           X          |         X        |          X         |           |
| 예약 권장 사항 |                           |                  |           X          |                  |                    |           |
| 예약 세부 정보         |                           |                  |           X          |         X        |                    |           |
| 예약 요약       |                           |                  |           X          |         X        |                    |           |
| 사용량 세부 정보               |             X             |         X        |           X          |         X        |          X         |     X     |
| 청구 기간             |             X             |         X        |           X          |         X        |                    |           |
| 송장                    |             X             |         X        |           X          |         X        |                    |           |
| RateCard                    |             X             |                  |           X          |         X        |          X         |           |
| 요금 미지정 사용량               |             X             |                  |           X          |                  |          X         |           |

> [!NOTE]
> 시나리오-API 매핑은 기업 소비량 API를 포함하지 않습니다. 가능한 경우 새로운 개발 시나리오에는 일반 사용량 API를 사용합니다.

## <a name="api-summaries"></a>API 요약

### <a name="consumption"></a>Consumption
Web Direct 및 기업 고객은 명시된 경우를 제외하고 다음의 모든 API를 사용할 수 있습니다.

-   [예산 API](https://docs.microsoft.com/rest/api/consumption/budgets)(*기업 고객만*): 리소스, 리소스 그룹 또는 청구 측정 단위에 대한 비용 또는 사용 예산을 만들 수 있습니다. 예산을 만들면 정의한 예산 임계값을 초과했을 때 알리도록 경고를 구성할 수 있습니다. 예산액에 도달하면 작업이 진행되도록 구성할 수도 있습니다.

-   [Marketplace 요금 API](https://docs.microsoft.com/rest/api/consumption/marketplaces): 모든 Azure Marketplace 리소스(Azure 파트너 제품)의 요금 및 사용량 데이터를 가져옵니다. 이 데이터를 사용하여 모든 Marketplace 리소스에 걸쳐 비용을 합산하거나 특정 리소스에 대한 비용/사용량을 조사할 수 있습니다.

-   [가격표 API](https://docs.microsoft.com/rest/api/consumption/pricesheet)(*기업 고객만*): 모든 요금제의 사용자 지정 가격을 가져옵니다. 기업은 사용량 세부 정보 및 마켓플레이스 사용량 정보를 이 데이터와 함께 사용하여 사용량 및 Marketplace 데이터를 사용한 비용 계산을 수행할 수 있습니다. 

-   [예약 권장 사항 API](https://docs.microsoft.com/rest/api/consumption/reservationrecommendations): Reserved VM Instances 구매에 대한 권장 사항을 가져옵니다. 권장 사항은 예상된 비용 절감 및 구매 금액을 분석하는 데 도움이 됩니다. 자세한 내용은 [Azure 예약 자동화를 위한 API](billing-reservation-apis.md)를 참조하세요.

-   [예약 세부 정보 API](https://docs.microsoft.com/rest/api/consumption/reservationsdetails): 예약된 소비량과 실제로 사용된 양과 같은 이전에 구매한 VM 예약 정보에 대한 내용을 참조하세요. VM 수준별로 데이터를 자세히 볼 수 있습니다. 자세한 내용은 [Azure 예약 자동화를 위한 API](billing-reservation-apis.md)를 참조하세요.

-   [예약 요약 API](https://docs.microsoft.com/rest/api/consumption/reservationssummaries): 예약된 소비량 및 집계에 사용된 소비량과 같은 조직이 구매한 VM 예약의 집계 정보를 참조하세요. 자세한 내용은 [Azure 예약 자동화를 위한 API](billing-reservation-apis.md)를 참조하세요.

-   [사용량 세부 정보 API](https://docs.microsoft.com/rest/api/consumption/usagedetails): Microsoft에서 모든 Azure 리소스의 비용 및 사용량 정보를 가져옵니다. 정보는 하루에 미터당 한 번씩 보내지는 사용량 세부 정보 레코드 형식으로 제공됩니다. 이 정보를 사용하여 모든 리소스에 걸쳐 비용을 합산하거나 특정 리소스에 대한 비용/사용량을 조사할 수 있습니다.

-   [RateCard API](/previous-versions/azure/reference/mt219005(v=azure.100)): Web Direct 고객인 경우 요금제 요율을 가져옵니다. 그런 다음, 해당 리소스 사용 정보를 반환된 정보와 함께 사용하여 예상된 청구서를 직접 계산할 수 있습니다. 

-   [요금 미지정 사용량 API](/previous-versions/azure/reference/mt219003(v=azure.100)): Azure가 계량/청구를 수행하기 이전의 원시 사용량 정보를 가져옵니다.

### <a name="billing"></a>결제
-   [청구 기간 API](https://docs.microsoft.com/rest/api/billing/billingperiods): 해당 기간의 청구서 ID와 함께 분석할 청구 기간을 결정합니다. 청구서 API와 청구서 ID를 사용할 수 있습니다.

-   [청구서 API](https://docs.microsoft.com/rest/api/billing/invoices): 청구 기간에 대한 청구서의 다운로드 URL을 PDF 형식으로 가져옵니다.

### <a name="enterprise-consumption"></a>기업 소비량
다음 API는 기업 전용입니다.

-   [잔액 요약 API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-balance-summary): 잔액, 신규 구매, Azure Marketplace 서비스 요금, 조정 및 초과 요금에 대한 월별 정보 요약을 가져옵니다. 현재 청구 기간 또는 과거의 특정 기간에 대해 이 정보를 얻을 수 있습니다. 기업은 이 데이터를 사용하여 수동으로 계산한 요약 요금과 비교할 수 있습니다. 이 API는 리소스 관련 정보 또는 비용에 대한 집계 보기는 제공하지 않습니다.

-   [사용량 세부 정보 API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail): 당월, 특정 청구 기간 또는 사용자 지정 기간 동안의 Azure 사용량(Microsoft 제품)에 대한 정보를 가져옵니다. 기업에서는 이 데이터를 사용하여 요율 및 사용량을 기준으로 청구 금액을 수동으로 계산할 수 있습니다. 기업은 부서/조직 정보를 사용하여 조직 전체에 비용을 부과할 수도 있습니다. 데이터는 리소스별 사용량/비용 보기를 제공합니다.

-   [Marketplace 스토리지 요금 API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge): 당월, 특정 청구 기간 또는 사용자 지정 기간 동안의 Azure 사용량(파트너 제품)에 대한 정보를 가져옵니다. 기업에서는 이 데이터를 사용하여 요율 및 사용량을 기준으로 청구 금액을 수동으로 계산할 수 있습니다. 기업은 부서/조직 정보를 사용하여 조직 전체에 비용을 부과할 수도 있습니다. 이 API는 리소스별 사용량/비용 보기를 제공합니다.

-   [가격표 API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-pricesheet): 지정된 등록 및 청구 기간에 대한 각 측정기에 적용할 수 있는 가격을 제공합니다. 이 요금 정보를 사용하여 사용량 세부 정보 및 마켓플레이스 사용 정보와 함께 사용하여 예상된 청구서를 직접 계산할 수 있습니다.

-   [청구 기간 API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-billing-periods): 청구 기간 목록을 가져옵니다. 또한 이 API는 청구 기간에 속하는 4개의 엔터프라이즈 API 데이터 세트의 API 경로를 가리키는 속성인 BalanceSummary, UsageDetails, Marketplace Charges 및 PriceSheet도 제공합니다.

-   [예약 인스턴스 권장 사항 API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation): 7일, 30일 또는 60일 동안의 가상 머신 사용량을 보고, 단일 및 공유 구매 권장 사항을 가져옵니다. 이 API를 사용하여 예상 비용 절감 및 권장 구매 금액을 분석할 수 있습니다. 자세한 내용은 [Azure 예약 자동화를 위한 API](billing-reservation-apis.md)를 참조하세요.

## <a name="frequently-asked-questions"></a>질문과 대답

### <a name="whats-the-difference-between-the-enterprise-reporting-apis-and-the-consumption-apis-when-should-i-use-each"></a>기업 보고 API와 소비량 API 간의 차이는 무엇인가요? 언제 사용해야 하나요?
이러한 API에는 유사한 기능 집합이 있고 청구 및 비용 관리 공간에서 동일한 광범위한 질문의 집합에 대답할 수 있습니다. 그렇지만 다음과 같이 각기 다른 사용자를 대상으로 합니다. 

- 기업 보고 API는 협상된 현금 약정 금액 및 사용자 지정 가격 책정에 대한 액세스를 부여하는 Microsoft와 기업계약을 체결한 고객에게 제공됩니다. API에는 [Enterprise Portal](https://ea.azure.com)을 통해 얻을 수 있는 키가 필요합니다. 이러한 API에 대한 설명은 [기업 고객을 위한 보고 API 개요](billing-enterprise-api.md)를 참조하세요.

- 소비량 API는 몇 가지 예외로 모든 고객에게 제공됩니다. 자세한 내용은 [Azure 소비량 API 개요](billing-consumption-api-overview.md) 및 [Azure 소비량 API 참조](https://docs.microsoft.com/rest/api/consumption/)를 참조하세요. 제공된 API는 최신 개발 시나리오의 솔루션으로 권장됩니다. 

### <a name="whats-the-difference-between-the-usage-details-api-and-the-usage-api"></a>사용량 세부 정보 API와 사용량 API 간의 차이는 무엇인가요?
이러한 API는 기본적으로 다른 데이터를 제공합니다.

- [사용량 세부 정보 API](https://docs.microsoft.com/rest/api/consumption/usagedetails)는 미터 인스턴스당 Azure 사용량 및 비용 정보를 제공합니다. 제공된 데이터는 이미 Azure의 비용 계량 시스템을 통과했으며 다른 가능한 변경과 함께 적용된 비용이 있었습니다.

   - 선불된 현금 약정 금액의 사용을 위한 계정 변경
   - Azure에서 검색된 사용량 불일치에 대한 계정 변경

- [사용량 API](/previous-versions/azure/reference/mt219003(v=azure.100))는 Azure의 비용 계량 시스템을 통과하기 전에 원시 Azure 사용량 정보를 제공합니다. 이 데이터는 Azure 요금 계량 시스템 후에 표시되는 사용량 또는 요금량과 상관 관계가 없을 수 있습니다.

### <a name="whats-the-difference-between-the-invoice-api-and-the-usage-details-api"></a>청구서 API와 사용량 세부 정보 API 간의 차이는 무엇인가요?
이러한 API는 동일한 데이터의 다른 보기를 제공합니다.

- [청구서 API](https://docs.microsoft.com/rest/api/billing/invoices)는 Web Direct 고객 전용입니다. 이 API는 각 요금제 유형의 집계 요금을 기준으로 청구액의 월별 롤업을 제공합니다. 

- [사용량 세부 정보 API](https://docs.microsoft.com/rest/api/consumption/usagedetails)는 각 날짜에 대한 사용량/비용 레코드의 세부적인 보기를 제공합니다. 기업 및 Web Direct 고객 둘 다 이 API를 사용할 수 있습니다.

### <a name="whats-the-difference-between-the-price-sheet-api-and-the-ratecard-api"></a>가격표 API와 RateCard API 간의 차이는 무엇인가요?
이러한 API는 유사한 데이터 집합을 제공하지만 서로 다른 대상을 갖습니다.

- [가격표 API](https://docs.microsoft.com/rest/api/consumption/pricesheet)는 Enterprise 고객에 대해 협상된 사용자 지정 가격을 제공합니다.

- [RateCard API](/previous-versions/azure/reference/mt219005(v=azure.100))는 Web Direct 고객에게 적용되는 공개 가격을 제공합니다.

## <a name="next-steps"></a>다음 단계

- Azure API를 사용하여 Azure 사용량에 대한 정보를 프로그래밍 방식으로 얻는 방법에 대한 자세한 내용은 [Azure 소비량 API 개요](billing-consumption-api-overview.md) 및 [Azure 청구 API 개요](billing-usage-rate-card-overview.md)를 참조하세요.

- 청구서를 자세한 일별 사용 현황 파일 및 Azure Portal의 비용 관리 보고서와 비교하려면 [Microsoft Azure 요금 청구 방식 이해](billing-understand-your-bill.md)를 참조하세요.

- 문의 사항이 있거나 도움이 필요한 경우 [지원 요청을 만드는](https://go.microsoft.com/fwlink/?linkid=2083458)합니다.
