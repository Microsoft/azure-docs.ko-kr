---
title: Azure 예약 권장 사항을 만드는 방법
description: 가상 머신에 대한 Azure 예약 권장 사항을 만드는 방법을 알아봅니다.
author: banders
ms.reviewer: yashar
ms.service: cost-management-billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/28/2020
ms.author: banders
ms.openlocfilehash: 2f7b09c14553fdb5d642080d286ce123176b997f
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/04/2020
ms.locfileid: "76991051"
---
# <a name="how-reservation-recommendations-are-created"></a>예약 권장 사항을 만드는 방법
Azure RI(reserved instance) 구매 권장 사항은 Azure Consumption [예약 권장 사항 API](/rest/api/consumption/reservationrecommendations), [Azure Advisor](../..//advisor/advisor-cost-recommendations.md#buy-reserved-virtual-machine-instances-to-save-money-over-pay-as-you-go-costs) 및 Azure Portal의 예약 구매 경험을 통해 제공됩니다.

다음 단계는 권장 사항을 계산하는 방법을 정의합니다. 
1. 권장 사항 엔진은 지난 7일, 30일 및 60일 동안 지정된 범위에서 리소스에 대한 시간별 사용량을 평가합니다.
2. 사용량 데이터에 따라 엔진은 예약 여부에 관계 없이 비용을 시뮬레이션합니다.
3. 비용은 다양한 수량에 대해 시뮬레이션되며, 절약 효과를 최대화하는 수량이 권장됩니다.
4. 리소스가 정기적으로 종료되면 시뮬레이션에서 비용을 절감하지 못하며 구매 권장 사항이 제공되지 않습니다.

## <a name="recommendations-in-azure-advisor"></a>Azure Advisor의 권장 사항
가상 머신에 대한 예약 구매 권장 사항은 Azure Advisor에서 사용할 수 있습니다. 다음 사항을 주의하세요. 
- Advisor에는 단일 구독 범위 권장 사항만 있습니다.
- 30일의 조회 기간으로 계산된 권장 사항은 Advisor에서 사용할 수 있습니다.
- 공유 범위 예약을 구매하는 경우 Advisor 예약 구매 권장 사항은 사라지는 데 최대 30일이 걸릴 수 있습니다.

## <a name="other-expected-api-behavior"></a>기타 예상 API 동작
- 7일 간의 조회 기간을 사용하는 경우 VM이 하루 이상 종료될 때 권장 사항을 얻지 못할 수 있습니다.

## <a name="next-steps"></a>다음 단계
- [Azure 예약 할인이 가상 머신에 적용되는 방법](../manage/understand-vm-reservation-charges.md)에 대해 알아봅니다.
