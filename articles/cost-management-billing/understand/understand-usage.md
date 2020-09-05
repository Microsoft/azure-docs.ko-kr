---
title: 세부 사용량 및 요금 이해
description: 세부 사용량 및 요금 파일을 읽고 이해하는 방법을 알아봅니다. 파일에 사용된 사용 약관 및 설명을 살펴봅니다.
author: bandersmsft
ms.reviewer: micflan
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: d113ad7d3de3478fbbdcce32363e048b7a8a75ce
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88681739"
---
# <a name="understand-the-terms-in-your-azure-usage-and-charges-file"></a>Azure 사용량 및 요금 파일의 사용 약관 이해

세부 사용량 및 요금 파일에는 협상된 요금을 기준으로 한 일일 적정 사용량, 구매(예: 예약, Marketplace 요금) 및 지정된 기간의 환불이 포함됩니다.
크레딧, 세금 또는 기타 요금이나 할인은 요금에 포함되지 않습니다.
다음 표에서는 각 계정 유형에 포함되는 요금을 설명합니다.

계정 유형 | Azure 사용량 | Marketplace 사용량 | 구매 | 환불
--- | --- | --- | --- | ---
EA(기업 계약) | 예 | 예 | 예 | 아니요
MCA(Microsoft 고객 계약) | 예 | 예 | 예 | 예
PAYG(종량제) | 예 | 예 | 예 | 아니요

Marketplace 주문(외부 서비스라고도 함)에 대해 자세히 알아보려면 [Azure 외부 서비스 요금 이해](understand-azure-marketplace-charges.md)를 참조하세요.

다운로드 지침은 [Azure 청구서 및 일별 사용량 데이터를 받는 방법](../manage/download-azure-invoice-daily-usage-date.md)을 참조하세요.
Microsoft Excel 또는 다른 스프레드시트 애플리케이션에서 사용량 및 요금 CSV 파일을 열 수 있습니다.

## <a name="list-of-terms-and-descriptions"></a>용어 및 설명 목록

다음 표에서는 최신 버전의 Azure 사용량 및 요금 파일에 사용되는 중요한 용어에 대해 설명합니다.
이 목록에는 PAYG(종량제), EA(기업계약) 및 Microsoft MCA(고객 계약) 계정이 포함되어 있습니다.

용어 | 계정 유형 | 설명
--- | --- | ---
AccountName | EA, PAYG | EA 등록계약 계정 또는 PAYG 청구 계정의 표시 이름입니다.
AccountOwnerId<sup>1</sup> | EA, PAYG | EA 등록계약 계정 또는 PAYG 청구 계정의 고유 식별자입니다.
AdditionalInfo | 모두 | 서비스 특정 메타데이터입니다. 예를 들어 가상 머신용 이미지 형식입니다.
BillingAccountId<sup>1</sup> | 모두 | 루트 청구 계정의 고유 식별자입니다.
BillingAccountName | 모두 | 청구 계정의 이름입니다.
BillingCurrency | 모두 | 청구 계정과 연결된 통화입니다.
BillingPeriod | EA, PAYG | 요금 청구 기간입니다.
BillingPeriodEndDate | 모두 | 청구 기간의 종료 날짜입니다.
BillingPeriodStartDate | 모두 | 청구 기간의 시작 날짜입니다.
BillingProfileId<sup>1</sup> | 모두 | EA 등록계약, PAYG 구독, MCA 청구 프로필 또는 AWS 통합 계정의 고유 식별자입니다.
BillingProfileName | 모두 | EA 등록계약, PAYG 구독, MCA 청구 프로필 또는 AWS 통합 계정의 이름입니다.
ChargeType | 모두 | 요금이 사용량(**Usage**), 구매(**Purchase**) 또는 환불(**Refund**) 중 무엇을 나타내는지 알려줍니다.
ConsumedService | 모두 | 요금과 연결된 서비스의 이름입니다.
CostCenter<sup>1</sup> | EA, MCA | 비용을 추적하는 구독에 대해 정의된 비용 센터입니다(MCA 계정의 유효 청구 기간에만 사용 가능).
비용 | EA, PAYG | CostInBillingCurrency를 참조하세요.
CostInBillingCurrency | MCA | 크레딧 또는 세금이 적용되지 않은 요금입니다(청구 통화 기준).
CostInPricingCurrency | MCA | 크레딧 또는 세금이 적용되지 않은 요금입니다(가격 책정 통화 기준).
통화 | EA, PAYG | BillingCurrency를 참조하세요.
Date<sup>1</sup> | 모두 | 요금의 사용량 또는 구매 날짜입니다.
EffectivePrice | 모두 | 기간의 혼합 단가입니다. 혼합 가격은 단가의 변동 평균을 계산한 것입니다(예: 시간이 지남에 따라 수량이 증가하면 가격을 할인해주는 점진적 차등 적용).
ExchangeRateDate | MCA | 환율이 설정된 날짜입니다.
ExchangeRatePricingToBilling | MCA | 가격 책정 통화의 비용을 청구 통화로 변환하는 데 사용되는 환율입니다.
빈도 | 모두 | 요금이 반복될 것으로 예상되는지 여부를 나타냅니다. 요금은 일회성(**OneTime**)이거나, 매월 또는 매년 반복되거나(**Recurring**), 사용량을 기준으로 할 수 있습니다(**UsageBased**).
InvoiceId | PAYG, MCA | 청구서 PDF에 나열된 고유 문서 ID입니다.
InvoiceSection | MCA | InvoiceSectionName을 참조하세요.
InvoiceSectionId<sup>1</sup> | EA, MCA | EA 부서 또는 MCA 청구서 섹션의 고유 식별자입니다.
InvoiceSectionName | EA, MCA | EA 부서 또는 MCA 청구서 섹션의 이름입니다.
IsAzureCreditEligible | 모두 | Azure 크레딧 사용 시 요금이 청구될 수 있는지 여부를 나타냅니다(값: True, False).
위치 | MCA | 리소스가 실행되고 있는 데이터 센터 위치입니다.
MeterCategory | 모두 | 측정기에 대한 분류 범주의 이름입니다. 예를 들어 *클라우드 서비스* 및 *네트워킹*입니다.
MeterId<sup>1</sup> | 모두 | 미터의 고유 식별자입니다.
MeterName | 모두 | 미터의 이름입니다.
MeterRegion | 모두 | 위치에 따라 가격이 책정되는 서비스의 데이터 센터 위치 이름입니다. Location을 참조하세요.
MeterSubCategory | 모두 | 미터의 하위 분류 범주 이름입니다.
OfferId<sup>1</sup> | 모두 | 구매한 제품의 이름입니다.
PayGPrice | 모두 | 리소스의 소매 가격입니다.
PartNumber<sup>1</sup> | EA, PAYG | 특정 미터 가격 책정을 확인하는 데 사용되는 식별자입니다.
PlanName | EA, PAYG | Marketplace 계획 이름입니다.
PreviousInvoiceId | MCA | 이 품목이 환불인 경우 원본 청구서에 대한 참조입니다.
PricingCurrency | MCA | 협상된 가격을 기준으로 요금이 책정되는 경우 사용되는 통화입니다.
PricingModel | 모두 | 미터 요금을 책정하는 방식을 나타내는 식별자입니다. (값: 주문형, 예약, 지점)
Product | 모두 | 제품의 이름입니다.
ProductId<sup>1</sup> | MCA | 제품의 고유 식별자입니다.
ProductOrderId | 모두 | 제품 주문의 고유 식별자입니다.
ProductOrderName | 모두 | 제품 주문의 이름입니다.
PublisherName | 모두 | Marketplace 서비스의 게시자입니다.
PublisherType | 모두 | 게시자 유형(값: **Azure**, **AWS**, **Marketplace**)입니다.
수량 | 모두 | 구매 또는 소비된 단위 수입니다.
ReservationId | EA, MCA | 구매한 예약 인스턴스의 고유 식별자입니다.
ReservationName | EA, MCA | 구매한 예약 인스턴스의 이름입니다.
ResourceGroup | 모두 | 리소스가 속하는 [리소스 그룹](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)의 이름입니다. 모든 요금이 리소스 그룹에 배포된 리소스에서 발생하는 것은 아닙니다. 리소스 그룹이 없는 요금은 null/empty, **기타** 또는 **적용되지 않음**으로 표시됩니다.
ResourceId<sup>1</sup> | 모두 | [Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/resources) 리소스의 고유 식별자입니다.
ResourceLocation | 모두 | 리소스가 실행되고 있는 데이터 센터 위치입니다. Location을 참조하세요.
ResourceName | EA, PAYG | 리소스의 이름입니다. 모든 요금이 배포된 리소스에서 발생하는 것은 아닙니다. 리소스 유형이 없는 요금은 null/empty, **기타** 또는 **적용되지 않음**으로 표시됩니다.
ResourceType | MCA | 리소스 인스턴스의 유형입니다. 모든 요금이 배포된 리소스에서 발생하는 것은 아닙니다. 리소스 유형이 없는 요금은 null/empty, **기타** 또는 **적용되지 않음**으로 표시됩니다.
ServiceFamily | MCA | 서비스가 속한 서비스 제품군입니다.
ServiceInfo1 | 모두 | 서비스 특정 메타데이터입니다.
ServiceInfo2 | 모두 | 선택적 서비스 특정 메타데이터를 포함하는 레거시 필드입니다.
ServicePeriodEndDate | MCA | 소비하거나 구매한 서비스의 가격 책정을 정의하고 잠근 요금 책정 기간의 종료 날짜입니다.
ServicePeriodStartDate | MCA | 소비하거나 구매한 서비스의 가격 책정을 정의하고 잠근 요금 책정 기간의 시작 날짜입니다.
SubscriptionId<sup>1</sup> | 모두 | Azure 구독의 고유 식별자입니다.
SubscriptionName | 모두 | Azure 구독의 이름입니다.
Tags<sup>1</sup> | 모두 | 리소스에 할당된 태그입니다. 리소스 그룹 태그를 포함하지 않습니다. 내부 지불 거절 비용을 그룹화하거나 균등 맞춤하는 데 사용할 수 있습니다. 자세한 내용은 [태그를 사용하여 Azure 리소스 구성](https://azure.microsoft.com/updates/organize-your-azure-resources-with-tags/)을 참조하세요.
용어 | 모두 | 제품의 유효 기간을 표시합니다. 예를 들면 다음과 같습니다. 예약된 인스턴스의 경우 12개월을 기간으로 표시합니다. 일회성 구매 또는 되풀이 구매의 기간은 1개월(SaaS, Marketplace 지원)입니다. Azure 소비에는 적용되지 않습니다.
UnitOfMeasure | 모두 | 서비스 청구를 위한 측정 단위. 예를 들어 컴퓨팅 서비스는 시간당 청구됩니다.
UnitPrice | EA, PAYG | 요금 단위당 가격입니다.

_<sup>**1**</sup> 단일 비용 레코드의 고유 ID를 빌드하는 데 사용되는 필드입니다._

일부 필드는 계정 유형 간에 대/소문자 구분과 간격이 다를 수 있습니다.
종량제 사용량 파일의 이전 버전에는 명세서 및 일일 사용량에 대한 별도의 섹션이 있습니다.

### <a name="list-of-terms-from-older-apis"></a>이전 API의 용어 목록
다음 표에는 이전 API에서 사용된 용어에 대응하는 새로운 용어가 나와 있습니다. 위의 표에서 이러한 설명을 참조하세요.

이전 용어 | 새 용어
--- | ---
ConsumedQuantity | 수량
IncludedQuantity | 해당 없음
InstanceId | ResourceId
비용 | EffectivePrice
단위 | UnitOfMeasure
UsageDate | Date
UsageEnd | Date
UsageStart | Date


## <a name="ensure-charges-are-correct"></a>요금이 올바른지 확인

세부 사용량 및 요금에 대해 자세히 알아보려면 [종량제](review-individual-bill.md) 또는 [Microsoft 고객 계약](review-customer-agreement-bill.md) 청구서를 이해하는 방법에 대해 읽어보세요.

## <a name="need-help-contact-us"></a>도움 필요 시 문의하세요.

질문이 있거나 도움이 필요한 경우 [지원 요청을 만드세요](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>다음 단계

- [Microsoft Azure 청구서 보기 및 다운로드](download-azure-invoice.md)
- [Microsoft Azure 사용량 및 요금 보기 및 다운로드](download-azure-daily-usage.md)
