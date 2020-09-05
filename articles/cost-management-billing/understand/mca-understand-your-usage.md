---
title: Microsoft 고객 계약 Azure 사용량 및 요금 파일 조건
description: 청구 프로필에 대한 Azure 사용량 및 요금 CSV 섹션을 읽고 이해하는 방법을 알아봅니다.
author: bandersmsft
ms.reviewer: amberb
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: 826217091b3297da0c068336ff67a0a58841716d
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88690462"
---
# <a name="terms-in-the-azure-usage-and-charges-file-for-a-microsoft-customer-agreement"></a>Microsoft 고객 계약에 대한 Azure 사용량 및 요금 파일의 용어

이 문서는 Microsoft 고객 계약에 대한 청구 계정에 적용됩니다. [Microsoft 고객 계약에 액세스할 수 있는지 확인하세요](#check-access-to-a-microsoft-customer-agreement).

Azure 사용량 및 요금 CSV 파일에는 현재 청구 기간에 대한 일별 요금과 측정기 수준 사용 요금이 포함되어 있습니다.

Azure 사용량 및 요금 파일을 가져오려면 [Microsoft 고객 계약에 대한 Azure 사용량 및 요금 보기 및 다운로드](download-azure-daily-usage.md)를 참조하세요. 스프레드시트 애플리케이션에서 열 수 있는 쉼표로 구분된 값(.csv) 파일 형식으로 제공됩니다.

사용 요금은 구독에 대한 총 **월별** 요금입니다. 사용량 요금에서는 신용 또는 할인이 고려되지 않습니다.

## <a name="changes-from-azure-ea-usage-and-charges"></a>Azure EA 사용량 및 요금에 대한 변경 내용

EA 고객이었으면 Azure 청구 프로필 사용량 CSV 파일의 용어가 Azure EA 사용량 CSV 파일의 용어와 다른 것을 알 수 있습니다. 다음은 EA 사용량 용어를 청구 프로필 사용량 용어에 매핑한 내용입니다.

| Azure EA 사용량 CSV | Microsoft 고객 계약 Azure 사용량 및 요금 CSV |
| --- | --- |
| Date | date |
| Month| date |
| 일 | date |
| Year | date |
| Product | product |
| MeterId | meterID |
| MeterCategory | meterCategory |
| MeterSubCategory | meterSubCategory |
| MeterRegion | meterRegion |
| MeterName | meterName |
| ConsumedQuantity | quantity |
| ResourceRate | effectivePrice |
| ExtendedCost | cost |
| ResourceLocation | resourceLocation |
| ConsumedService | consumedService |
| InstanceId | instanceId |
| ServiceInfo1 | serviceInfo1 |
| ServiceInfo2 | serviceInfo2 |
| AdditionalInfo | additionalInfo |
| 태그들 | tags |
| StoreServiceIdentifier | 해당 없음 |
| DepartmentName | invoiceSection |
| CostCenter | costCenter |
| UnitOfMeasure | unitofMeasure |
| ResourceGroup | resourceGroup |
| ChargesBilledSeparately | isAzureCreditEligible |

## <a name="detailed-terms-and-descriptions"></a>자세한 용어 및 설명

다음 용어는 Azure 사용량 및 요금 파일에 표시됩니다.

용어 | Description
--- | ---
invoiceId | 청구서 PDF에 나열되는 고유 문서 ID
previousInvoiceId | 이 품목이 환불인 경우 원래 청구서에 대한 참조
billingAccountName | 청구 계정의 이름
billingAccountId | 루트 청구 계정의 고유 식별자
billingProfileId | 청구된 요금이 발생하는 청구 프로필의 이름
billingProfileName | 청구된 요금이 발생하는 청구 프로필에 대한 고유 식별자
invoiceSectionId | 청구서 섹션에 대한 고유 식별자
invoiceSectionName | 청구서 섹션의 이름
costCenter | 비용 추적을 위해 구독에 정의된 비용 센터(유효 청구 기간에만 사용 가능)
billingPeriodStartDate | 송장이 생성된 청구 기간의 시작 날짜
billingPeriodEndDate | 송장이 생성된 청구 기간의 종료 날짜
servicePeriodStartDate | 소비하거나 구매한 서비스에 대한 가격 책정을 정의하고 고정한 평가 기간의 시작 날짜
servicePeriodEndDate | 소비하거나 구매한 서비스에 대한 가격 책정을 정의하고 고정한 평가 기간의 종료 날짜
date | Azure 및 Marketplace 사용량 기반 요금의 경우, 평가 날짜입니다. 일회성 구매(예약, Marketplace) 또는 고정된 반복 요금(지원 제품)의 경우, 구매 날짜입니다.
serviceFamily | 서비스가 속하는 서비스 제품군
productOrderId | 제품 주문에 대한 고유 식별자
productOrderName | 제품 주문의 고유 이름
consumedService | 사용한 서비스의 이름
meterId | 미터의 고유 식별자
meterName | 미터의 이름
meterCategory | 측정기에 대한 분류 범주의 이름입니다. 예: *클라우드 서비스*, *네트워킹* 등.
meterSubCategory | 미터 하위 분류 범주의 이름
meterRegion | 서비스에 대한 측정기를 사용할 수 있는 지역의 이름입니다. 데이터 센터 위치에 따라 가격이 책정되는 특정 서비스에 대한 데이터 센터의 위치를 식별합니다.
제품 | 구매한 제품의 이름
PayGPrice | 리소스의 소매 가격입니다.
PricingModel | 미터 요금을 책정하는 방식을 나타내는 식별자(값: 주문형, 예약, 지점)
productId | 요금이 발생하는 제품의 고유 식별자
product | 요금이 발생하는 제품의 이름
구독 ID | 요금이 발생하는 구독의 고유 식별자
subscriptionName | 요금이 발생하는 구독의 이름
reservationId | 구매한 예약 인스턴스의 고유 식별자
reservationName | 구매한 예약 인스턴스의 이름
publisherType | 게시자 유형(값: firstParty, thirdPartyReseller, thirdPartyAgency)
publisherName | Marketplace 서비스의 게시자
resourceGroupId | 리소스와 연결된 리소스 그룹의 고유 식별자
resourceGroupName | 리소스와 연결된 리소스 그룹의 이름
resourceId | 리소스 인스턴스에 대한 고유 식별자
resourceType | 리소스 인스턴스의 유형
resourceLocation | 리소스가 실행되는 데이터 센터의 위치를 식별합니다.
위치 | 동일한 지역에 대해 서로 다른 리소스 위치가 구성된 경우 리소소의 정규화된 위치
quantity | 구매하거나 사용한 단위 수
unitOfMeasure | 서비스 청구를 위한 측정 단위. 예를 들어 컴퓨팅 서비스는 시간당 청구됩니다.
chargeType | 요금의 유형. 값 <ul><li>AsCharged-Usage: Azure 서비스 사용량을 기준으로 발생하는 요금. 여기에는 예약된 인스턴스로 인해 요금이 청구되지 않은 VM에 대한 사용량이 포함됩니다.</li><li>AsCharged-PurchaseMarketplace: Marketplace 구매의 일회성 요금 또는 고정된 반복 요금</li><li>AsCharged-UsageMarketplace: 소비 단위를 기반으로 청구되는 Marketplace 서비스에 대한 요금</li></ul>
isAzureCreditEligible | 서비스에 대한 요금을 Azure 크레딧을 사용하여 지불할 수 있는지 여부를 나타내는 플래그(값: True, False)
serviceInfo1 | 서비스 관련 메타데이터
serviceInfo2 | 선택적 서비스 특정 메타데이터를 캡처하는 레거시 필드입니다.
additionalInfo | 추가 서비스 관련 메타데이터
tags | 리소스에 할당하는 태그

### <a name="make-sure-that-charges-are-correct"></a>요금이 정확한지 확인

자세한 사용 현황 파일의 요금이 정확한지 확인하려면, 검증하면 됩니다. [청구 프로필 청구서의 요금 이해](review-customer-agreement-bill.md)를 참조하세요.

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Microsoft 고객 계약에 대한 액세스 확인
[!INCLUDE [billing-check-mca](../../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>도움 필요 시 문의하세요.

질문이 있거나 도움이 필요한 경우 [지원 요청을 만드세요](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>다음 단계

- [Microsoft Azure 청구서 보기 및 다운로드](download-azure-invoice.md)
- [Microsoft Azure 사용량 및 요금 보기 및 다운로드](download-azure-daily-usage.md)
