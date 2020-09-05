---
title: 구입할 Azure 예약 결정
description: 이 문서는 어떤 예약을 구매할지 결정하는 데 도움이 됩니다.
author: bandersmsft
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: how-to
ms.date: 08/04/2020
ms.author: banders
ms.openlocfilehash: d89f890d3e2bb8238a00b4a529b8804a8c38fa6f
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88684748"
---
# <a name="determine-what-reservation-to-purchase"></a>구매할 예약 결정

Azure Databricks를 제외한 모든 예약은 매시간 적용됩니다. 일관된 기본 사용량에 따라 예약을 구매해야 합니다. 구매할 항목을 결정하는 방법에는 여러 가지가 있으며 이 문서는 어떤 예약을 구매할지 결정하는 데 도움이 됩니다.

이전 사용량보다 더 많은 용량을 구매하면 미달 사용 예약이 발생합니다. 가능하면 미달 사용을 피해야 합니다. 사용되지 않는 예약 용량은 한 시간에서 다음 시간으로 전달되지 않습니다. 예약 수량을 초과하는 사용량은 보다 고가의 종량제 요금을 사용하여 청구됩니다.

## <a name="analyze-usage-data"></a>사용량 데이터 분석

다음 섹션을 사용하여 일일 사용량 데이터를 분석하여 기본 사용량 및 구매할 예약을 결정합니다.

### <a name="analyze-usage-for-a-vm-reserved-instance-purchase"></a>VM 예약 인스턴스 구매의 사용량 분석

구매에 적합한 VM 크기를 식별합니다. 예를 들어 ES 시리즈 VM에 대해 구매한 예약은 E 시리즈 VM에 적용되지 않으며 그 반대의 경우도 마찬가지입니다.

프로모션 시리즈 VM은 예약 할인을 받을 수 없으므로 분석에서 제거합니다.

적합한 VM 사용량으로 범위를 좁히려면 사용량 데이터에 다음 필터를 적용합니다.

- **Virtual Machines**에 **MeterCategory** 필터를 적용합니다.
- **AdditionalInfo**에서 **ServiceType** 정보를 가져옵니다. 이 정보는 올바른 VM 크기를 제안합니다. 예를 들면, 표준 E32입니다.
- **ResourceLocation** 필드를 사용하여 사용량 데이터 센터를 확인합니다.

하루 사용량이 24시간 미만인 리소스를 무시합니다.

인스턴스 크기 패밀리 수준에서 분석하려면 [https://isfratio.blob.core.windows.net/isfratio/ISFRatio.csv](https://isfratio.blob.core.windows.net/isfratio/ISFRatio.csv)에서 인스턴스 크기 유연성 값을 가져올 수 있습니다. 값과 데이터를 결합하여 분석을 수행합니다. 인스턴스 크기 유연성에 대한 자세한 내용은 [예약 VM 인스턴스에서 가상 머신 크기 유연성](../../virtual-machines/reserved-vm-instance-size-flexibility.md)을 참조하세요.

### <a name="analyze-usage-for-an-azure-synapse-analytics-reserved-instance-purchase"></a>Azure Synapse Analytics 예약 인스턴스 구매 사용량 분석

예약 용량은 Azure Synapse Analytics DWU 가격 책정에 적용됩니다. Azure Synapse Analytics 라이선스 비용 또는 컴퓨팅 이외의 비용에는 적용되지 않습니다.

적격 사용 범위를 좁히려면 사용량 데이터에 다음 필터를 적용합니다.


- **SQL Database**에 대해 **MeterCategory** 필터를 적용합니다.
- **vCore**에 대해 **MeterName** 필터를 적용합니다.
- 이름에 _Compute_가 있는 모든 사용량 레코드에 대해 **MeterSubCategory** 필터를 적용합니다.

**AdditionalInfo**에서 **vCores** 값을 가져옵니다. 사용된 vCores의 수를 알려줍니다. 수량은 **vCores**에 데이터베이스가 사용된 시간을 곱한 값입니다.

데이터는 다음에 대한 일관된 사용량에 대해 알려줍니다.

- 데이터베이스 형식의 조합. 예를 들어 관리형 인스턴스 또는 단일 데이터베이스당 탄력적 풀.
- 서비스 계층. 예를 들어 범용 또는 중요 비즈니스용.
- 세대. 예를 들어 Gen 5.
- 리소스 위치

### <a name="analysis-for-azure-synapse-analytics"></a>Azure Synapse Analytics에 대한 분석

예약 용량은 Azure Synapse Analytics DWU 사용량에 적용되며 100 DWU 증분 단위로 구매됩니다. 적격 사용 범위를 좁히려면 사용량 데이터에 다음 필터를 적용합니다.

- **100 DWUs**에 대해 **MeterName** 필터를 적용합니다.
- **Compute Optimized Gen2**에 대해 **Meter Sub-Category** 필터를 적용합니다.

**리소스 위치** 필드를 사용하여 지역의 Azure Synapse Analytics 사용량을 확인합니다.

Azure Synapse Analytics 사용량은 하루 종일 확장 및 축소할 수 있습니다. Azure Synapse Analytics 인스턴스를 관리하는 팀에 문의하여 기본 사용량에 대해 알아보세요.

Azure Portal의 예약으로 이동하여 100 DWU의 배수로 Azure Synapse Analytics 예약 용량을 구매합니다.

## <a name="reservation-purchase-recommendations"></a>예약 구매 권장 사항

예약 구매 권장 사항은 최근 7일, 30일 및 60일 동안 시간별 사용량 데이터를 분석하여 계산됩니다. Azure는 예약했을 경우의 비용을 계산하고 해당 기간 동안 발생한 실제 종량제 비용과 비교합니다. 계산은 시간 프레임 동안 사용한 모든 수량에 대해 수행됩니다. 절감액을 극대화하는 수량이 추천됩니다.

예를 들어 대부분 500대의 VM을 사용하지만 종종 700대의 VM으로 사용량이 급증하는 경우를 들 수 있습니다. 이 예에서 Azure는 500 및 700대의 VM에 대한 절감액을 계산합니다. 700대의 VM 사용량은 이따금 발생하므로 권장 사항 계산에 따라 500대 VM 예약 구매의 경우가 절감액이 극대화되며, 따라서 500대 수량이 추천됩니다.

다음 사항에 유의하세요.

- 예약 권장 사항은 사용자에게 적용되는 주문형 사용 요금을 사용하여 계산됩니다.
- 권장 사항은 인스턴스 크기 패밀리가 아닌 개별 크기에 대해 계산됩니다.
- 범위에 대한 예약을 구입하는 당일에 권장되는 범위 수량이 줄어듭니다.
    - 그러나 범위에 걸친 예약 수량 권장 사항에 대한 업데이트는 최대 25일까지 걸릴 수 있습니다. 예를 들어 범위 권장 사항을 기반으로 구매하는 경우 단일 구독 범위 권장 사항을 조정하는 데 최대 25일이 걸릴 수 있습니다.

## <a name="recommendations-in-the-azure-portal"></a>Azure Portal의 권장 사항

권장 사항 엔진에서 계산된 예약 구매는 [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/docs)의 **권장** 탭에 표시됩니다. 다음은 예제 이미지입니다.

![권장 사항을 보여주는 이미지](./media/determine-reservation-purchase/select-product-ri.png)

[권장 사항](reserved-instance-purchase-recommendations.md#recommendations-in-the-azure-portal)에 대해 자세히 알아봅니다.

## <a name="recommendations-in-the-cost-management-power-bi-app"></a>Cost Management Power BI 앱의 권장 사항

기업계약 및 Microsoft 고객 계약 고객은 VM 및 구매 권장 사항에 대한 VM RI 적용 범위 보고서를 사용할 수 있습니다. 적용 범위 보고서에는 총 사용량과 예약된 인스턴스에 의해 적용되는 사용량이 표시 됩니다.

1. [Cost Management](https://appsource.microsoft.com/product/power-bi/costmanagement.azurecostmanagementapp) 앱을 가져옵니다.
2. 구매하고자 하는 범위에 따라 VM RI 적용 범위 보고서 – 공유 또는 단일 범위로 이동합니다.
3. 선택된 필터에 대한 사용량, RI 적용 범위 및 구매 권장 사항을 보려면 지역, 인스턴스 크기 패밀리를 선택합니다.

## <a name="recommendations-in-azure-advisor"></a>Azure Advisor의 권장 사항

예약 구매 권장 사항은 [Azure Advisor](https://portal.azure.com/#blade/Microsoft_Azure_Expert/AdvisorMenuBlade/overview)에서 사용할 수 있습니다.

- Advisor에는 단일 구독 범위 권장 사항만 있습니다.
- Advisor 권장 사항은 30일의 조회 기간을 사용하여 계산됩니다. 예측된 절감액은 3년 예약 기간에 대한 것입니다.
- 공유 범위 예약을 구매하는 경우 Advisor 예약 구매 권장 사항은 사라지는 데 최대 30일이 걸릴 수 있습니다.

## <a name="recommendations-using-apis"></a>API를 사용한 권장 사항

[예약 권장 사항](/rest/api/consumption/reservationrecommendations/list) REST API를 사용하여 프로그래밍 방식으로 권장 사항을 봅니다.

## <a name="next-steps"></a>다음 단계

- [Azure Reservations 관리](manage-reserved-vm-instance.md)
- [종량제 요금을 사용하는 구독의 예약 사용량 이해](understand-reserved-instance-usage.md)
- [엔터프라이즈 등록에서 예약 사용량 이해](understand-reserved-instance-usage-ea.md)
- [예약에 포함되지 않는 Windows 소프트웨어 비용](reserved-instance-windows-software-costs.md)