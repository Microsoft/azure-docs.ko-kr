---
title: Azure Databricks 사전 구매 할인이 적용되는 방법
description: Azure Databricks 사전 구매 할인이 사용량에 적용되는 방법에 대해 알아봅니다. 구매 기간 중에 언제든지 이러한 Databricks를 사용할 수 있습니다.
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: banders
ms.openlocfilehash: f5a2102cedfa2b8631c1eb7ff6a3ff2452d315e3
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88690530"
---
# <a name="how-azure-databricks-pre-purchase-discount-is-applied"></a>Azure Databricks 사전 구매 할인이 적용되는 방법

구매 기간 동안 언제든지 사전 구매한 Azure DBCU(Databricks 커밋 단위)를 사용할 수 있습니다. 모든 Azure Databricks 사용량은 사전 구매한 DBCU에서 자동으로 차감됩니다.

VM과 달리 사전 구매한 단위는 시간별로 만료되지 않습니다. 구매 기간 동안 언제든지 사용할 수 있습니다. 사전 구매 할인을 받기 위해 사용량에 대해 Azure Databricks 작업 영역에 사전 구매 계획을 다시 배포하거나 할당할 필요가 없습니다.

사전 구매 할인은 Azure DBU(Databricks 단위) 사용량에만 적용됩니다. 컴퓨팅, 스토리지 및 네트워킹 등의 기타 요금은 별도로 청구됩니다.

## <a name="pre-purchase-discount-application"></a>사전 구매 할인 애플리케이션

Databricks 사전 구매는 모든 Databricks 워크로드 및 계층에 적용됩니다. 사전 구매는 선불 Databricks 커밋 단위의 풀로 간주할 수 있습니다. 워크로드 또는 계층에 관계없이 사용량은 풀에서 차감됩니다. 사용량은 다음과 같은 비율로 차감됩니다.

| **작업** | **DBU 애플리케이션 비율 — 표준 계층** | **DBU 애플리케이션 비율 - 프리미엄 계층** |
| --- | --- | --- |
| 데이터 분석 | 0.4 | 0.55 |
| 데이터 엔지니어링 | 0.15 | 0.30 |
| 데이터 엔지니어링 라이트 | 0.07 | 0.22 |

예를 들어 데이터 분석 수량 - 표준 계층을 사용하는 경우 사전 구매한 Databricks 커밋 단위는 0.4 단위로 차감됩니다. 데이터 엔지니어링 라이트 - 표준 계층을 사용하는 경우 사전 구매한 Databricks 커밋 단위는 0.07 단위로 차감됩니다.

## <a name="determine-plan-use"></a>요금제 사용 결정

DBCU 요금제 사용을 결정하려면 Azure Portal > **예약**으로 이동하여 구매한 Databricks 요금제를 클릭합니다. 현재까지 사용률은 나머지 단위로 표시됩니다. 예약 사용 결정에 대한 자세한 내용은 [예약 사용량 참조](reservation-apis.md#see-reservation-usage) 문서를 참조하세요.

## <a name="how-discount-application-shows-in-usage-data"></a>사용량 데이터에 할인 애플리케이션이 표시되는 방식

사전 구매 할인이 Databricks 사용량에 적용되는 경우 주문형 요금은 사용량 데이터에 0으로 표시됩니다. 예약 비용 및 사용량에 대한 자세한 내용은 [기업 계약 예약 비용 및 사용량 가져오기](understand-reserved-instance-usage-ea.md)를 참조하세요.

## <a name="need-help-contact-us"></a>도움 필요 시 문의하세요.

질문이 있거나 도움이 필요한 경우 [지원 요청을 만드세요](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>다음 단계

- 예약을 관리하는 방법을 알아보려면 [Azure Reservations 관리](manage-reserved-vm-instance.md)를 참조하세요.
- 비용 절감을 위한 사전 구매 Azure Databricks에 대해 자세히 알아보려면 [사전 구매를 사용하여 Azure Databricks 비용 최적화](prepay-databricks-reserved-capacity.md)를 참조하세요.
- Azure 예약에 대한 자세한 내용은 다음 문서를 참조하세요.
  - [Azure 예약이란?](save-compute-costs-reservations.md)
  - [Azure에서 Reservations 관리](manage-reserved-vm-instance.md)
  - [종량제 요금을 사용하는 구독에 대한 예약 사용량 이해](understand-reserved-instance-usage.md)
  - [엔터프라이즈 등록에서 예약 사용량 이해](understand-reserved-instance-usage-ea.md)
