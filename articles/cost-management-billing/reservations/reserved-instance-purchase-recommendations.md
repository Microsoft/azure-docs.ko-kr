---
title: Azure 예약 권장 사항
description: Azure 예약 권장 사항에 대해 알아봅니다.
author: banders
ms.author: banders
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: conceptual
ms.date: 08/04/2020
ms.openlocfilehash: 813cf91195a43a2fc68236febe114c67eb5a3378
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88684612"
---
# <a name="reservation-recommendations"></a>예약 권장 사항

Azure RI(reserved instance) 구매 권장 사항은 Azure Consumption [예약 권장 사항 API](/rest/api/consumption/reservationrecommendations), [Azure Advisor](../../advisor/advisor-cost-recommendations.md#buy-reserved-virtual-machine-instances-to-save-money-over-pay-as-you-go-costs) 및 Azure Portal의 예약 구매 경험을 통해 제공됩니다.

다음 단계는 권장 사항을 계산하는 방법을 정의합니다.

1. 권장 사항 엔진은 지난 7일, 30일 및 60일 동안 지정된 범위에서 리소스에 대한 시간별 사용량을 평가합니다.
2. 사용량 데이터에 따라 엔진은 예약 여부에 관계 없이 비용을 시뮬레이션합니다.
3. 비용은 다양한 수량에 대해 시뮬레이션되며, 절약 효과를 최대화하는 수량이 권장됩니다.
4. 리소스가 정기적으로 종료되면 시뮬레이션에서 비용을 절감하지 못하며 구매 권장 사항이 제공되지 않습니다.

## <a name="recommendations-in-the-azure-portal"></a>Azure Portal의 권장 사항

예약 구매 권장 사항은 구매 환경의 Azure Portal에도 표시됩니다. 권장 사항은 **권장 수량**과 함께 표시됩니다. 예약을 구매할 때 Azure에서 권장하는 수량을 선택하면 비용을 최대한 절감할 수 있습니다. 권장 수량과 다른 수량을 구매해도 되지만, 비용 절감이 최적화되지 않습니다.

몇 가지 예를 통해 그 이유를 살펴보겠습니다.

엄선된 권장 사항에 대한 다음 예제 이미지에서 Azure는 구매 수량 6을 권장합니다.

:::image type="content" source="./media/reserved-instance-purchase-recommendations/recommended-quantity.png" alt-text="예약 구매 권장 사항을 보여주는 예제" lightbox="./media/reserved-instance-purchase-recommendations/recommended-quantity.png" :::

권장 사항에 대한 자세한 내용은 **자세히 보기** 링크를 선택하면 표시됩니다. 다음 이미지는 권장 사항에 대한 세부 정보를 보여줍니다. 권장 수량은 과거의 사용량을 기반으로 발생 가능한 최대 사용량을 계산하여 결정됩니다. 사용량이 일정하지 않은 경우 사용률 100%에 대한 권장 수량이 아닐 수 있습니다. 이 예제에서는 시간이 지나면서 사용률이 변합니다. 예약 비용, 가능한 절감액 및 사용률 백분율이 표시됩니다.

:::image type="content" source="./media/reserved-instance-purchase-recommendations/recommended-quantity-details.png" alt-text="예약 구매 권장 사항 세부 정보를 보여주는 예제" :::

권장 사항을 초과하여 수량을 늘리거나 줄이면 차트 및 예상 값이 변경됩니다. 예약 수량을 늘리면 예약 사용률이 감소하므로 절감액이 줄어듭니다. 즉, 완전히 사용되지 않는 예약 비용을 지불해야 합니다.

예약 수량을 줄이면 절감액도 줄어듭니다. 사용률은 높아지겠지만, 사용량을 완벽하게 감당할 수 없는 기간이 발생할 가능성이 높습니다. 예약 수량을 초과하는 사용량은 더 비싼 종량제 리소스를 사용합니다. 다음 예제 이미지는 이 시점을 보여줍니다. 예약 수량을 수동으로 4로 줄였습니다. 예약 사용률은 증가하지만, 종량제 비용이 발생하므로 전반적인 절감액이 줄어듭니다.

:::image type="content" source="./media/reserved-instance-purchase-recommendations/recommended-quantity-details-changed.png" alt-text="변경된 예약 구매 권장 사항 세부 정보를 보여주는 예제" :::

예약의 비용 절감 효과를 최대화하려면 권장 수량과 최대한 비슷한 수량을 구매하는 것이 좋습니다.

## <a name="recommendations-in-azure-advisor"></a>Azure Advisor의 권장 사항

예약 구매 권장 사항은 Azure Advisor에서 사용할 수 있습니다. 다음 사항을 주의하세요.

- Advisor에는 단일 구독 범위 권장 사항만 있습니다.
- 권장 사항은 이전 30일의 사용 추세를 고려하여 계산됩니다.
- 권장 수량 및 절감액은 3년 예약(사용 가능한 경우) 기준입니다. 해당 서비스의 3년 예약이 판매되지 않는 경우 1년 예약 가격을 사용하여 권장 수량을 계산합니다.
- 권장 수량에는 주문형 사용 요금에 대한 특별 할인이 적용됩니다.
- 공유 범위 예약을 구매하는 경우 Advisor 예약 구매 권장 사항은 사라지는 데 최대 30일이 걸릴 수 있습니다.

## <a name="other-expected-api-behavior"></a>기타 예상 API 동작

- 7일 간의 조회 기간을 사용하는 경우 VM이 하루 이상 종료될 때 권장 사항을 얻지 못할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [Azure 예약 할인이 가상 머신에 적용되는 방법](../manage/understand-vm-reservation-charges.md)에 대해 알아봅니다.
