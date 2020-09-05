---
title: 기업계약의 Azure 예약 사용량 이해
description: Enterprise 등록에서 Azure Reservation의 적용 방법을 이해하기 위해 사용량을 판독하는 방법을 알아봅니다.
author: bandersmsft
ms.reviewer: yashar
tags: billing
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: conceptual
ms.date: 02/13/2020
ms.author: banders
ms.openlocfilehash: 20a9fb6a158134ffc18dc5bbb7eddd34d2b79562
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88682011"
---
# <a name="get-enterprise-agreement-reservation-costs-and-usage"></a>기업계약 예약 비용 및 사용량 가져오기

예약 비용 및 사용량 데이터는 Azure Portal 및 REST API를 통해 기업계약 고객에게 제공됩니다. 이 문서는 다음 작업을 수행하는 데 도움이 됩니다.

- 예약 구매 데이터 가져오기
- 예약을 사용한 구독, 리소스 그룹 또는 리소스 파악
- 예약 사용에 대한 차지백
- 예약 절감액 계산
- 사용률이 낮은 예약 데이터 가져오기
- 예약 비용 분할

마켓플레이스 요금은 사용량 데이터에 통합됩니다. 단일 데이터 원본에서 자사 사용량, 마켓플레이스 사용량 및 구매 요금을 볼 수 있습니다.

## <a name="reservation-charges-in-azure-usage-data"></a>Azure 사용량 데이터의 예약 요금

데이터는 다음과 같은 두 가지 데이터 세트로 나뉩니다. 하나는 _실제 비용_이고 다른 하나는 _분할 상환 비용_입니다. 두 데이터 세트의 차이점은 다음과 같습니다.

**실제 비용** - 월별 청구서를 조정하는 데이터를 제공합니다. 이 데이터에는 예약 구매 비용 및 예약 적용 세부 정보가 포함되어 있습니다. 이 데이터를 사용하면 특정 날짜에 예약 할인을 받는 구독, 리소스 그룹 또는 리소스를 알 수 있습니다. 예약 할인을 받은 사용량의 EffectivePrice는 0입니다.

**분할 상환 비용** - 이 데이터 세트는 예약 할인을 받는 사용량의 EffectivePrice가 0이 아니라 예약 비용에 비례한다는 점을 제외하면 실제 비용 데이터 세트와 비슷합니다. 이 데이터를 통해 예약 사용의 금전적 가치를 구독, 리소스 그룹 또는 리소스별로 파악할 수 있으며, 내부적으로 예약 사용에 대해 차지백할 수 있습니다. 데이터 세트에도 사용되지 않은 예약 시간이 있습니다. 데이터 세트는 예약 구매 레코드가 없습니다.

두 데이터 세트의 비교:

| 데이터 | 실제 비용 데이터 세트 | 분할 상환 비용 데이터 세트 |
| --- | --- | --- |
| 예약 구매 | 이 보기에서 사용할 수 있습니다.<br><br>  이 데이터를 얻으려면 ChargeType = &quot;Purchase&quot;로 필터링합니다. <br><br> 요금이 부과되는 예약을 확인하려면 ReservationID 또는 ReservationName을 참조하세요.  | 이 보기에 적용할 수 없습니다. <br><br> 구매 비용은 분할 상환 데이터에 제공되지 않습니다. |
| EffectivePrice | 예약 할인을 받는 사용량의 금액은 0입니다. | 이 금액은 예약 할인이 적용되는 사용량에 대한 시간당 예약 비용에 비례합니다. |
| 사용하지 않는 예약(해당 날짜에 예약을 사용하지 않은 시간 및 폐기된 시간의 금전적 가치 제공) | 이 보기에서 사용할 수 없습니다. | 이 보기에서 사용할 수 있습니다.<br><br> 이 데이터를 얻으려면 ChargeType = &quot;UnusedReservation&quot;으로 필터링합니다.<br><br>  사용률이 낮은 예약을 확인하려면 ReservationID 또는 ReservationName을 참조하세요. 이 데이터를 통해 해당 날짜에 얼마나 많은 예약이 낭비되었는지 알 수 있습니다.  |
| UnitPrice(가격표의 리소스 가격) | 사용 가능 | 사용 가능 |

Azure 사용량 데이터에 제공되는 다른 정보가 변경되었습니다.

- 제품 및 미터 정보 - Azure는 이전과 마찬가지로 원래 사용된 미터를 ReservationId 및 ReservationName로 바꾸지 않습니다.
- ReservationId 및 ReservationName - 데이터의 고유한 필드입니다. 이전에는 AdditionalInfo에서만 사용할 수 있었습니다.
- ProductOrderId - 예약 주문 ID이며 자체 필드로 추가됩니다.
- ProductOrderName - 구매한 예약의 제품 이름입니다.
- Term - 12개월 또는 36개월입니다.
- RINormalizationRatio - AdditionalInfo에서 사용할 수 있습니다. 사용량 레코드에 예약이 적용되는 비율입니다. 예약에 인스턴스 크기 유연성을 사용하도록 설정한 경우 다른 크기에 적용될 수 있습니다. 이 값은 사용량 레코드에 예약이 적용된 비율을 보여줍니다.

[필드 정의 참조](https://docs.microsoft.com/rest/api/consumption/usagedetails/list#definitions)

## <a name="get-azure-consumption-and-reservation-usage-data-using-api"></a>API를 사용하여 Azure 소비 및 예약 사용량 데이터 가져오기

API를 사용하여 데이터를 가져오거나 Azure Portal에서 데이터를 다운로드할 수 있습니다.

새 데이터를 가져오려면 [사용량 세부 정보 API](/rest/api/consumption/usagedetails/list)를 호출합니다. 용어에 대한 자세한 내용은 [사용 약관](../understand/understand-usage.md)을 참조하세요. 호출자는 [EA Portal](https://ea.azure.com)을 사용하는 기업계약의 엔터프라이즈 관리자여야 합니다. 읽기 전용 엔터프라이즈 관리자도 데이터를 가져올 수 있습니다.

이 데이터는 [기업 고객을 위한 보고 API - 사용량 세부 정보](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail)에서 사용할 수 없습니다.

다음은 사용량 세부 정보 API에 대한 호출 예제입니다.

```
https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{enrollmentId}/providers/Microsoft.Billing/billingPeriods/{billingPeriodId}/providers/Microsoft.Consumption/usagedetails?metric={metric}&amp;api-version=2019-05-01&amp;$filter={filter}
```

{enrollmentId} 및 {billingPeriodId}에 대한 자세한 내용은 [사용량 세부 정보 – 나열](https://docs.microsoft.com/rest/api/consumption/usagedetails/list) API 문서를 참조하세요.

메트릭 및 필터에 대한 다음 표의 정보는 일반적인 예약 문제를 해결하는 데 도움이 될 수 있습니다.

| **API 데이터 유형** | API 호출 작업 |
| --- | --- |
| **모든 요금(사용량 및 구매)** | {metric}을 ActualCost로 바꾸기 |
| **예약 할인이 적용된 사용량** | {metric}을 ActualCost로 바꾸기<br><br>{filter}를 properties/reservationId%20ne%20으로 바꾸기 |
| **예약 할인이 적용되지 않은 사용량** | {metric}을 ActualCost로 바꾸기<br><br>{filter}를 properties/reservationId%20eq%20으로 바꾸기 |
| **분할 상환 요금(사용량 및 구매)** | {metric}을 AmortizedCost로 바꾸기 |
| **사용하지 않는 예약 보고서** | {metric}을 AmortizedCost로 바꾸기<br><br>{filter}를 properties/ChargeType%20eq%20'UnusedReservation'로 바꾸기 |
| **예약 구매** | {metric}을 ActualCost로 바꾸기<br><br>{filter}를 properties/ChargeType%20eq%20'Purchase'로 바꾸기  |
| **환불** | {metric}을 ActualCost로 바꾸기<br><br>{filter}를 properties/ChargeType%20eq%20'Refund'로 바꾸기 |

## <a name="download-the-usage-csv-file-with-new-data"></a>새 데이터가 포함된 사용량 CSV 파일 다운로드

EA 관리자는 Azure Portal에서 새로운 사용량 데이터가 포함된 CSV 파일을 다운로드할 수 있습니다. 이 데이터는 EA 포털(ea.azure.com)에서 사용할 수 없습니다. 새 데이터를 보려면 Azure Portal(portal.azure.com)에서 사용 파일을 다운로드해야 합니다.

Azure Portal에서 [Cost Management + 청구](https://portal.azure.com/#blade/Microsoft_Azure_Billing/ModernBillingMenuBlade/BillingAccounts)로 이동합니다.

1. 청구 계정을 선택합니다.
2. **사용량 + 요금**을 클릭합니다.
3. **다운로드**를 클릭합니다.  
![Azure Portal에서 CSV 사용량 데이터 파일을 다운로드하는 위치를 보여주는 예제](./media/understand-reserved-instance-usage-ea/portal-download-csv.png)
4. **사용량 및 요금 다운로드**의 **사용량 세부 정보 버전 2**에서 **모든 요금(사용량 및 구매)** 을 선택한 다음, [다운로드]를 클릭합니다. **분할 상환 요금(사용량 및 구매)** 반복

다운로드한 CSV 파일에는 실제 비용과 분할 상환 비용이 포함되어 있습니다.

## <a name="common-cost-and-usage-tasks"></a>일반적인 비용 및 사용량 작업

다음 섹션은 대부분의 사람들이 예약 비용 및 사용량 데이터를 보기 위해 사용하는 일반적인 작업입니다.

### <a name="get-reservation-purchase-costs"></a>예약 구매 비용 가져오기

예약 구매 비용은 실제 비용 데이터에서 사용할 수 있습니다. _ChargeType = Purchase_로 필터링합니다. 구매의 예약 주문을 확인하려면 ProductOrderID를 참조하세요.

### <a name="get-underutilized-reservation-quantity-and-costs"></a>사용률이 낮은 예약 수량 및 비용 가져오기

분할 상환 비용 데이터를 가져오고 _ChargeType_ _= UnusedReservation_으로 필터링합니다. 사용하지 않는 날짜별 예약 수량 및 비용을 가져옵니다. _ReservationId_ 또는 _ProductOrderId_를 사용하여 각각 예약 또는 예약 주문에 대한 데이터를 필터링할 수 있습니다. 예약 사용률이 100%이면 레코드의 수량은 0입니다.

### <a name="amortize-reservation-costs"></a>예약 비용 분할

분할 상환 비용 데이터를 가져오고, _ProductOrderID_로 예약 주문을 필터링하여 예약의 날짜별 분할 상환 비용을 가져옵니다.

### <a name="chargeback-for-a-reservation"></a>예약 사용 요금 차지백

구독, 리소스 그룹 또는 태그를 따라 예약 사용 요금을 다른 조직에 차지백할 수 있습니다. 분할 상환 비용 데이터는 다음 데이터 형식에 대한 예약 사용률의 금전적 가치를 제공합니다.

- 리소스(예: VM)
- Resource group
- 태그들
- Subscription

### <a name="get-the-blended-rate-for-chargeback"></a>차지백의 혼합 비율 구하기

혼합 비율을 확인하려면 분할 상환 비용 데이터를 가져와서 총 비용을 집계합니다. VM의 경우 AdditionalInfo JSON 데이터의 MeterName 또는 ServiceType 정보를 사용할 수 있습니다. 총 비용을 혼합 비율을 가져오는 데 사용된 수량으로 나눕니다.

### <a name="audit-optimum-reservation-use-for-instance-size-flexibility"></a>인스턴스 크기 유연성에 대한 최적 예약 사용 감사

수량과 AdditionalInfo의 _RINormalizationRatio_를 곱합니다. 그 결과는 사용량 레코드에 적용된 예약 사용 시간을 나타냅니다.

### <a name="determine-reservation-savings"></a>예약 절감액 확인

분할 상환 비용 데이터를 가져오고 예약된 인스턴스에 대한 데이터를 필터링합니다. 그렇다면

1. 예상 종량제 비용을 구합니다. 사용량에 예약 할인이 적용되지 않은 경우 _UnitPrice_ 값에 _Quantity_ 값을 곱하여 예상 종량제 비용을 구합니다.
2. 예약 비용을 구합니다. _Cost_ 값을 합산하여 예약 인스턴스에 대해 지불한 금전적 가치를 구합니다. 여기에는 사용된 예약 및 사용되지 않은 예약 비용이 포함됩니다.
3. 예상 종량제 비용에서 예약 비용을 빼서 예상 절감액을 구합니다.

## <a name="reservation-purchases-and-amortization-in-cost-analysis"></a>비용 분석의 예약 구매 및 분할 상환

예약 비용은 [비용 분석](https://aka.ms/costanalysis)에서 사용할 수 있습니다. 기본적으로 비용 분석은 **실제 비용**을 표시하며, 이 방식으로 청구서에 비용이 표시됩니다. 혜택을 사용한 리소스와 연결된 예약 구매를 항목별로 살펴보려면 **분할 상환**으로 전환합니다.

![비용 분석에서 분할 상환 비용을 선택하는 위치를 보여주는 예제](./media/understand-reserved-instance-usage-ea/portal-cost-analysis-amortized-view.png)

사용량, 구매 및 환불 내역을 보려면 요금 유형별로 그룹화하고, 예약 및 주문형 비용 내역을 보려면 예약별로 그룹화합니다. 실제 비용을 살펴볼 때 표시되는 유일한 예약 비용은 구매입니다. 그러나 분할 상환 비용을 살펴볼 때에는 혜택을 사용한 개별 리소스에 비용이 할당됩니다. 분할 상환 비용을 살펴볼 때 새로운 **UnusedReservation** 요금 유형도 표시됩니다.

## <a name="need-help-contact-us"></a>도움 필요 시 문의하세요.

질문이 있거나 도움이 필요한 경우 [지원 요청을 만드세요](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>다음 단계

Azure 예약에 대한 자세한 내용은 다음 문서를 참조하세요.

- [Azure 예약이란?](save-compute-costs-reservations.md)
- [Azure Reserved VM Instances를 사용하여 Virtual Machines 선불 결제](../../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Azure SQL Database 예약 용량을 사용하여 SQL Database 컴퓨팅 리소스 요금 선결제](../../azure-sql/database/reserved-capacity-overview.md)
- [Azure Reservations 관리](manage-reserved-vm-instance.md)
- [예약 할인이 적용되는 방식 이해](../manage/understand-vm-reservation-charges.md)
- [종량제 구독의 예약 사용량 이해](understand-reserved-instance-usage.md)
- [예약에 포함되지 않는 Windows 소프트웨어 비용](reserved-instance-windows-software-costs.md)
