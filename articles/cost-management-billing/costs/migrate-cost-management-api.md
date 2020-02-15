---
title: Microsoft 고객 계약 API로 EA 마이그레이션 - Azure
description: 이 문서는 Microsoft EA(기업계약)를 Microsoft 고객 계약으로 마이그레이션하면 어떻게 되는지 이해하는 데 도움이 됩니다.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 01/21/2020
ms.topic: conceptual
ms.service: cost-management-billing
manager: micflan
ms.custom: ''
ms.openlocfilehash: b0c30cd35efd598767d21dbdafc5100d8896380c
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/31/2020
ms.locfileid: "76514717"
---
# <a name="migrate-from-enterprise-agreement-to-microsoft-customer-agreement-apis"></a>기업계약에서 Microsoft 고객 계약 API로 마이그레이션

이 문서는 EA(기업계약) 계정과 MCA(Microsoft 고객 계약) 계정의 데이터 구조, API 및 기타 시스템 통합 차이점을 이해하는 데 도움이 됩니다. Azure Cost Management는 두 계정 유형의 API를 모두 지원합니다. 계속하기 전에 Microsoft 고객 계약 문서에 대한 [청구 계정 설정](../manage/mca-setup-account.md)을 검토하세요.

기존 EA 계정이 있는 조직은 MCA 계정을 설정하는 것과 함께 이 문서를 검토해야 합니다. 이전에는 EA 계정을 갱신하려면 기존 등록에서 새 등록으로 이동하기 위한 최소한의 작업이 필요했습니다. 하지만 MCA 계정으로 마이그레이션하려면 추가 작업이 필요합니다. 추가 작업이 필요한 이유는 모든 비용 관련 API 및 서비스 제품에 영향을 주는 기본 청구 하위 시스템이 변경되었기 때문입니다.

## <a name="mca-apis-and-integration"></a>MCA API 및 통합

MCA API 및 새로운 통합을 통해 다음을 수행할 수 있습니다.

- 네이티브 Azure API를 통해 완전한 API 가용성을 확보합니다.
- 단일 청구 계정에서 여러 청구서를 구성합니다.
- Azure 서비스 사용, 타사 Marketplace 사용 및 Marketplace 구매를 통해 결합된 API에 액세스합니다.
- Azure Cost Management를 사용하여 청구 프로필(등록과 동일)의 비용을 봅니다.
- 새 API에 액세스하여 비용을 표시하고, 비용이 미리 정의된 임계값을 초과할 때 알림을 받고, 원시 데이터를 자동으로 내보냅니다.

## <a name="migration-checklist"></a>마이그레이션 검사 목록

다음 항목은 MCA API로 전환하는 데 도움이 됩니다.

- 새 [Microsoft 고객 계약 청구 계정](../understand/mca-overview.md)을 숙지합니다.
- 사용하는 API를 확인하고 다음 섹션에서 대체되는 API를 확인합니다.
- [Azure Resource Manager REST API](/rest/api/azure)를 숙지합니다.
- 아직 Azure Resource Manager API를 사용하지 않는 경우 [클라이언트 앱을 Azure AD에 등록](/rest/api/azure/#register-your-client-application-with-azure-ad)합니다.
- [Azure AD 인증](/rest/api/azure/#create-the-request)을 사용하도록 모든 프로그래밍 코드를 업데이트합니다.
- 모든 프로그래밍 코드를 업데이트하여 EA API 호출을 MCA API 호출로 바꿉니다.
- 새 오류 코드를 사용하도록 오류 처리를 업데이트합니다.
- Cloudyn 및 Power BI 같은 추가 통합 제품을 검토하여 필요한 조치가 더 있는지 확인합니다.

## <a name="ea-apis-replaced-with-mca-apis"></a>EA API를 MCA API로 대체

EA API는 인증 및 권한 부여에 API 키를 사용합니다. MCA API는 Azure AD 인증을 사용합니다.

| 목적 | EA API | MCA API |
| --- | --- | --- |
| 잔액 및 크레딧 | [/balancesummary](/rest/api/billing/enterprise/billing-enterprise-api-balance-summary) | Microsoft.Billing/billingAccounts/billingProfiles/availableBalanceussae |
| 사용량(JSON) | [/usagedetails](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#json-format)[/usagedetailsbycustomdate](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#json-format) | [Microsoft.Consumption/usageDetails](/rest/api/consumption/usagedetails)<sup>1</sup> |
| 사용량(CSV) | [/usagedetails/download](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#csv-format)[/usagedetails/submit](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#csv-format) | [Microsoft.Consumption/usageDetails/download](/rest/api/consumption/usagedetails)<sup>1</sup> |
| Marketplace 사용량(CSV) | [/marketplacecharges](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge)[/marketplacechargesbycustomdate](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge) | [Microsoft.Consumption/usageDetails/download](/rest/api/consumption/usagedetails)<sup>1</sup> |
| 청구 기간 | [/billingperiods](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods) | Microsoft.Billing/billingAccounts/billingProfiles/invoices |
| 가격표 | [/pricesheet](/rest/api/billing/enterprise/billing-enterprise-api-pricesheet) | Microsoft.Billing/billingAccounts/billingProfiles/pricesheet/default/download format=json|csv Microsoft.Billing/billingAccounts/…/billingProfiles/…/invoices/… /pricesheet/default/download format=json|csv Microsoft.Billing/billingAccounts/../billingProfiles/../providers/Microsoft.Consumption/pricesheets/download  |
| 예약 구매 | [/reservationcharges](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-charges) | Microsoft.Billing/billingAccounts/billingProfiles/transactions |
| 예약 권장 사항 | [/SharedReservationRecommendations](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-shared-reserved-instance-recommendations)[/](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-single-reserved-instance-recommendations)[SingleReservationRecommendations](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-single-reserved-instance-recommendations) | [Microsoft.Consumption/reservationRecommendations](/rest/api/consumption/reservationrecommendations/list) |
| 예약 사용량 | [/reservationdetails](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage#request-for-reserved-instance-usage-details)[/reservationsummaries](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage) | [Microsoft.Consumption/reservationDetails](/rest/api/consumption/reservationsdetails)[Microsoft.Consumption/reservationSummaries](/rest/api/consumption/reservationssummaries) |

<sup>1</sup> Azure 서비스 및 타사 Marketplace 사용량은 [사용량 세부 정보 API](/rest/api/consumption/usagedetails)에서 사용할 수 있습니다.

다음 API는 MCA 청구 계정에 사용할 수 있습니다.

| 목적 | MCA(Microsoft 고객 계약) API |
| --- | --- |
| 청구 계정<sup>2</sup> | Microsoft.Billing/billingAccounts |
| 청구 프로필<sup>2</sup> | Microsoft.Billing/billingAccounts/billingProfiles |
| 청구서 섹션<sup>2</sup> | Microsoft.Billing/billingAccounts/invoiceSections |
| 송장 | Microsoft.Billing/billingAccounts/billingProfiles/invoices |
| 청구 및 구독 | {scope}/billingSubscriptions |

<sup>2</sup> API는 범위에 해당하는 개체 목록을 반환하고, 범위는 Azure Portal 및 API에서 Cost Management 환경이 작동하는 위치입니다. Cost Management 범위에 대한 자세한 내용은 [범위 이해 및 작업](understand-work-scopes.md)을 참조하세요.

기존 EA API를 사용하는 경우 MCA 청구 계정을 지원하도록 업데이트해야 합니다. 다음 표에서는 다른 통합 변경 내용을 보여줍니다.

| 목적 | 이전 제품 | 새 제품 |
| --- | --- | --- |
| Cloudyn | [Cloudyn.com](https://www.cloudyn.com) | [Azure Cost Management](https://azure.microsoft.com/services/cost-management/) |
| Power BI | [Microsoft Consumption Insights](/power-bi/desktop-connect-azure-consumption-insights) 콘텐츠 팩 및 커넥터 |  [Azure Consumption Insights 커넥터](/power-bi/desktop-connect-azure-consumption-insights) |

## <a name="apis-to-get-balance-and-credits"></a>잔액 및 크레딧을 얻기 위한 API

[Get Balance Summary](/rest/api/billing/enterprise/billing-enterprise-api-balance-summary) API는 다음 항목에 대한 월별 요약 정보를 제공합니다.

- 잔액
- 새 구매
- Azure Marketplace 서비스 요금
- 조정
- 서비스 초과분 요금

모든 소비 API는 인증 및 권한 부여에 Azure AD를 사용하는 네이티브 Azure API로 대체되었습니다. Azure REST API 호출에 대한 자세한 내용은 [REST 시작](/rest/api/azure/#create-the-request)을 참조하세요.

Get Balance Summary API는 Microsoft.Billing/billingAccounts/billingProfiles/availableBalance API로 대체됩니다.

Available Balance API를 사용하여 가용 잔액 정보를 가져오는 방법은 다음과 같습니다.

| 방법 | 요청 URI |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/availableBalances?api-version=2018-11-01-preview` |

## <a name="apis-to-get-cost-and-usage"></a>비용 및 사용 정보를 가져오는 API

다음 API를 사용하여 Azure 서비스 사용량, 타사 Marketplace 사용량 및 기타 Marketplace 구매의 일별 비용 분석 정보를 가져올 수 있습니다. Azure 서비스 및 타사 Marketplace 사용을 위해 다음과 같은 별도의 API가 병합되었습니다. 이전 API는 [Microsoft.Consumption/usageDetails](/rest/api/consumption/usagedetails) API로 대체되었습니다. 이전에는 현재 잔액 요약 정보에만 표시되던 Marketplace 구매가 추가됩니다.

- [Get usage detail/download](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#csv-format)
- [Get usage detail/submit](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#csv-format)
- [Get usage detail/usagedetails](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#json-format)
- [Get usage detail/usagedetailsbycustomdate](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#json-format)
- [Get marketplace store charge/marketplacecharges](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge)
- [Get marketplace store charge/marketplacechargesbycustomdate](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge)

모든 소비 API는 인증 및 권한 부여에 Azure AD를 사용하는 네이티브 Azure API로 대체되었습니다. Azure REST API 호출에 대한 자세한 내용은 [REST 시작](/rest/api/azure/#create-the-request)을 참조하세요.

모든 이전 API는 Consumption/Usage Details API로 대체됩니다.

Usage Details API를 사용하여 사용량 세부 정보를 가져오는 방법은 다음과 같습니다.

| 방법 | 요청 URI |
| --- | --- |
| GET | `https://management.azure.com/{scope}/providers/Microsoft.Consumption/usageDetails?api-version=2019-01-01` |

모든 Cost Management API와 마찬가지로, Usage Details API는 여러 범위에서 사용할 수 있습니다. 청구서가 발부된 비용의 경우 기존에 등록 수준에서 받던 것처럼 청구 프로필 범위를 사용합니다.  Cost Management 범위에 대한 자세한 내용은 [범위 이해 및 작업](understand-work-scopes.md)을 참조하세요.

| Type | ID 형식 |
| --- | --- |
| 청구 계정 | `/Microsoft.Billing/billingAccounts/{billingAccountId}` |
| 청구 프로필 | `/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}` |
| Subscription | `/subscriptions/{subscriptionId}` |
| Resource group | `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}` |

다음 querystring 매개 변수를 사용하여 프로그래밍 코드를 업데이트합니다.

| 이전 매개 변수 | 새 매개 변수 |
| --- | --- |
| `billingPeriod={billingPeriod}` | 지원되지 않음 |
| `endTime=yyyy-MM-dd` | `endDate=yyyy-MM-dd` |
| `startTime=yyyy-MM-dd` | `startDate=yyyy-MM-dd` |

응답 본문도 변경되었습니다.

이전 응답 본문:

```
{
  "id": "string",
  "data": [{...}, ...],
  "nextLink": "string"
}
```

새 응답 본문:

```
{
  "value": [{
    "id": "{scope}/providers/Microsoft.Consumption/usageDetails/###",
    "name": "###",
    "type": "Microsoft.Consumption/usageDetails",
    "tags": {...},
    "properties": [{...}, ...],
    "nextLink": "string"
  }, ...]
}
```

사용량 레코드 배열을 포함하는 속성 이름이 데이터에서 _값_으로 변경되었습니다. 각 레코드는 상세 속성의 단순 목록을 포함하는 데 사용되었습니다. 하지만 이제 각 레코드는 태그를 제외하고 _속성_이라는 중첩 속성에 있습니다. 새 구조는 다른 Azure API와 일치합니다. 일부 속성 이름이 변경되었습니다. 다음 표에서는 해당하는 속성을 보여줍니다.

| 이전 속성 | 새 속성 | 메모 |
| --- | --- | --- |
| AccountId | 해당 없음 | 구독 작성자는 추적되지 않습니다. invoiceSectionId(departmentId와 동일)를 사용합니다. |
| AccountNameAccountOwnerId 및 AccountOwnerEmail | 해당 없음 | 구독 작성자는 추적되지 않습니다. invoiceSectionName(departmentName과 동일)을 사용합니다. |
| AdditionalInfo | additionalInfo | &nbsp;  |
| ChargesBilledSeparately | isAzureCreditEligible | 이러한 속성은 서로 반대입니다. isAzureCreditEnabled가 true이면 ChargesBilledSeparately는 false가 됩니다. |
| ConsumedQuantity | quantity | &nbsp; |
| ConsumedService | consumedService | 정확한 문자열 값은 다를 수 있습니다. |
| ConsumedServiceId | None | &nbsp; |
| CostCenter | costCenter | &nbsp; |
| Date 및 usageStartDate | date | &nbsp;  |
| 일 | None | 날짜에서 일을 구문 분석합니다. |
| DepartmentId | invoiceSectionId | 정확한 값은 다릅니다. |
| DepartmentName | invoiceSectionName | 정확한 문자열 값은 다를 수 있습니다. 필요한 경우 부서와 일치하도록 청구서 섹션을 구성합니다. |
| ExtendedCost 및 Cost | costInBillingCurrency | &nbsp;  |
| InstanceId | resourceId | &nbsp;  |
| 반복 청구 여부 | None | &nbsp;  |
| 위치 | 위치 | &nbsp;  |
| MeterCategory | meterCategory | 정확한 문자열 값은 다를 수 있습니다. |
| MeterId | meterId | 정확한 문자열 값은 다릅니다. |
| MeterName | meterName | 정확한 문자열 값은 다를 수 있습니다. |
| MeterRegion | meterRegion | 정확한 문자열 값은 다를 수 있습니다. |
| MeterSubCategory | meterSubCategory | 정확한 문자열 값은 다를 수 있습니다. |
| Month | None | 날짜에서 월을 구문 분석합니다. |
| 제품 이름 | None | publisherName 및 productOrderName을 사용합니다. |
| OfferID | None | &nbsp;  |
| 주문 번호 | None | &nbsp;  |
| PartNumber | None | meterId 및 productOrderName을 사용하여 가격을 고유하게 식별합니다. |
| 플랜 이름 | productOrderName | &nbsp;  |
| Product | Product |   |
| ProductId | productId | 정확한 문자열 값은 다릅니다. |
| 게시자 이름 | publisherName | &nbsp;  |
| ResourceGroup | resourceGroupName | &nbsp;  |
| ResourceGuid | meterId | 정확한 문자열 값은 다릅니다. |
| ResourceLocation | resourceLocation | &nbsp;  |
| ResourceLocationId | None | &nbsp;  |
| ResourceRate | effectivePrice | &nbsp;  |
| ServiceAdministratorId | 해당 없음 | &nbsp;  |
| ServiceInfo1 | serviceInfo1 | &nbsp;  |
| ServiceInfo2 | serviceInfo2 | &nbsp;  |
| ServiceName | meterCategory | 정확한 문자열 값은 다를 수 있습니다. |
| ServiceTier | meterSubCategory | 정확한 문자열 값은 다를 수 있습니다. |
| StoreServiceIdentifier | 해당 없음 | &nbsp;  |
| SubscriptionGuid | subscriptionId | &nbsp;  |
| SubscriptionId | subscriptionId | &nbsp;  |
| SubscriptionName | subscriptionName | &nbsp;  |
| 태그들 | tags | tags 속성은 루트 개체에 적용되며 nested properties 속성에는 적용되지 않습니다. |
| UnitOfMeasure | unitOfMeasure | 정확한 문자열 값은 다릅니다. |
| usageEndDate | date | &nbsp;  |
| Year | None | 날짜에서 연도를 구문 분석합니다. |
| (새 기능) | billingCurrency | 요금에 사용되는 통화입니다. |
| (새 기능) | billingProfileId | 청구 프로필의 고유 ID입니다(등록과 동일). |
| (새 기능) | billingProfileName | 청구 프로필의 이름입니다(등록과 동일). |
| (새 기능) | chargeType | Azure 서비스 사용, Marketplace 사용 및 구매를 구분하는 데 사용됩니다. |
| (새 기능) | invoiceId | 청구서의 고유 ID입니다. 현재 진행 중인 달의 경우 비어 있습니다. |
| (새 기능) | publisherType | 구매의 게시자의 유형입니다. 사용의 경우 비어 있습니다. |
| (새 기능) | serviceFamily | 구매 유형입니다. 사용의 경우 비어 있습니다. |
| (새 기능) | servicePeriodEndDate | 구매한 서비스의 종료 날짜입니다. |
| (새 기능) | servicePeriodStartDate | 구매한 서비스의 시작 날짜입니다. |

## <a name="billing-periods-api-replaced-by-invoices-api"></a>Invoices API로 대체된 Billing Periods API

MCA 청구 계정은 청구 기간을 사용하지 않습니다. 그 대신 청구서를 사용하여 비용 범위를 특정 청구 기간으로 지정합니다. [Billing Periods API](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods)는 Invoices API로 대체되었습니다. 모든 소비 API는 인증 및 권한 부여에 Azure AD를 사용하는 네이티브 Azure API로 대체되었습니다. Azure REST API 호출에 대한 자세한 내용은 [REST 시작](/rest/api/azure/#create-the-request)을 참조하세요.

Invoices API로 청구서를 받는 방법은 다음과 같습니다.

| 방법 | 요청 URI |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/invoices?api-version=2018-11-01-preview` |

## <a name="price-sheet-apis"></a>가격표 API

이 섹션에서는 기존 Price Sheet API에 대해 설명하고 Microsoft 고객 계약에 대한 Price Sheet API로 전환하기 위한 추천을 제공합니다. 또한 Microsoft 고객 계약에 대한 Price Sheet API 및 가격표의 필드에 대해 설명합니다. [Enterprise Get price sheet](/rest/api/billing/enterprise/billing-enterprise-api-pricesheet) 및 [Enterprise Get billing periods](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods) API는 Microsoft 고객 계약에 대한 Price Sheet API(billingAccounts/billingProfiles/가격표)로 대체되었습니다. 새 API는 비동기 REST 형식으로 JSON 및 CSV 형식을 모두 지원합니다. 모든 소비 API는 인증 및 권한 부여에 Azure AD를 사용하는 네이티브 Azure API로 대체되었습니다. Azure REST API 호출에 대한 자세한 내용은 [REST 시작](/rest/api/azure/#create-the-request)을 참조하세요.

### <a name="billing-enterprise-apis"></a>Billing Enterprise API

Billing Enterprise API를 Enterprise 등록에 사용하여 가격 및 청구 기간 정보를 가져왔습니다. 인증 및 권한 부여에는 Azure Active Directory 웹 토큰을 사용했습니다.

Price Sheet 및 Billing Period API를 사용하여 지정된 Enterprise 등록에 적용 가능한 가격을 가져오는 방법은 다음과 같습니다.

| 방법 | 요청 URI |
| --- | --- |
| GET | `https://consumption.azure.com/v2/enrollments/{enrollmentNumber}/pricesheet` |
| GET | `https://consumption.azure.com/v2/enrollments/{enrollmentNumber}/billingPeriods/{billingPeriod}/pricesheet` |

### <a name="price-sheet-api-for-microsoft-customer-agreements"></a>Microsoft 고객 계약에 대한 Price Sheet API

Microsoft 고객 계약에 대한 Price Sheet API를 사용하여 모든 Azure Consumption 및 Marketplace 사용 서비스의 가격을 볼 수 있습니다. 청구 프로필에 대해 표시된 가격은 청구 프로필에 속한 모든 구독에 적용됩니다.

다음과 같은 방법으로 Price Sheet API를 사용하여 모든 Azure Consumption 서비스 가격표 데이터를 CSV 형식으로 볼 수 있습니다.

| 방법 | 요청 URI |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/pricesheet/default/download?api-version=2018-11-01-preview&startDate=2019-01-01&endDate=2019-01-31&format=csv` |

다음과 같은 방법으로 Price Sheet API를 사용하여 모든 Azure Consumption 서비스 가격표 데이터를 JSON 형식으로 볼 수 있습니다.

| 방법 | 요청 URI |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/pricesheet/default/download?api-version=2018-11-01-preview&startDate=2019-01-01&endDate=2019-01-31&format=json` |

API를 사용하면 전체 계정의 가격표가 반환됩니다. 그러나 요약 버전의 가격표를 PDF 형식으로 가져올 수도 있습니다. 요약 버전에는 특정 청구서로 요금이 청구되는 Azure Consumption 및 Marketplace 사용 서비스가 포함됩니다. 청구서는 청구서 요약 PDF 파일에 표시된 **청구서 번호**와 동일한 {invoiceId}로 식별됩니다. 예를 들면 다음과 같습니다.

![InvoiceId에 해당하는 청구서 번호를 보여주는 예제 이미지](./media/migrate-cost-management-api/invoicesummary.png)

Price Sheet API를 사용하여 청구서 정보를 CSV 형식으로 보는 방법은 다음과 같습니다.

| 방법 | 요청 URI |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/2909cffc-b0a2-5de1-bb7b-5d3383764184/billingProfiles/2dcffe0c-ee92-4265-8647-515b8fe7dc78/invoices/{invoiceId}/pricesheet/default/download?api-version=2018-11-01-preview&format=csv` |

Price Sheet API를 사용하여 청구서 정보를 JSON 형식으로 보는 방법은 다음과 같습니다.

| 방법 | 요청 URI |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/2909cffc-b0a2-5de1-bb7b-5d3383764184/billingProfiles/2dcffe0c-ee92-4265-8647-515b8fe7dc78/invoices/{invoiceId}/pricesheet/default/download?api-version=2018-11-01-preview&format=json` |

현재 진행 중인 청구 주기 또는 서비스 기간의 Azure Consumption 또는 Marketplace 사용 서비스 예상 가격도 볼 수 있습니다.

Price Sheet API를 사용하여 사용 서비스의 예상 가격을 CSV 형식으로 보는 방법은 다음과 같습니다.

| 방법 | 요청 URI |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billing AccountId}/billingProfiles/{billingProfileId}/pricesheet/default/download?api-version=2018-11-01-preview&format=csv` |

Price Sheet API를 사용하여 사용 서비스의 예상 가격을 JSON 형식으로 보는 방법은 다음과 같습니다.

| 방법 | 요청 URI |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billing AccountId}/billingProfiles/{billingProfileId}/pricesheet/default/download?api-version=2018-11-01-preview&format=json` |

Microsoft 고객 계약 Price Sheet API는 *비동기 REST API*입니다. API에 대한 응답이 이전의 동기 API에서 변경되었습니다. API 응답 본문도 변경되었습니다.

#### <a name="old-response-body"></a>이전 응답 본문

다음은 동기 REST API 응답의 예입니다.

```
[
        {
            "id": "enrollments/573549891/billingperiods/2016011/products/343/pricesheets",
            "billingPeriodId": "201704",
            "meterId": "dc210ecb-97e8-4522-8134-2385494233c0",
            "meterName": "A1 VM",
            "unitOfMeasure": "100 Hours",
            "includedQuantity": 0,
            "partNumber": "N7H-00015",
            "unitPrice": 0.00,
            "currencyCode": "USD"
        },
        {
    ]
```

#### <a name="new-response-body"></a>새 응답 본문

API에서 [Azure REST 비동기](../../azure-resource-manager/management/async-operations.md) 형식을 지원합니다. GET을 사용하여 API를 호출하면 다음 응답이 수신됩니다.

```
No Response Body

HTTP Status 202 Accepted
```

다음 헤더가 출력 위치와 함께 전송됩니다.

```
Location:https://management.azure.com/providers/Microsoft.Consumption/operationresults/{operationId}?sessiontoken=XZDFSnvdkbkdsb==

Azure-AsyncOperation:https://managment.azure.com/providers/Microsoft.Consumption/operationStatus/{operationId}?sessiontoken=XZDFSnvdkbkdsb==

Retry-After: 10

OData-EntityId: {operationId}

```

위치에 대한 또 다른 GET 호출을 수행합니다. 작업이 완료 또는 실패 상태에 도달할 때까지 GET 호출에 대한 응답이 동일합니다. 작업이 완료되면 GET 호출 위치에 대한 응답에서 다운로드 URL을 반환합니다. 마치 작업이 동시에 실행된 것과 같습니다. 예를 들면 다음과 같습니다.

```
HTTP Status 200

{
  "id": "providers/Microsoft.Consumption/operationresults/{operationId}",
  "name": {operationId},
  "type": “Microsoft.Consumption/operationResults",
  "properties" : {
    "downloadUrl": {urltoblob},
    "validTill": "Date"
  }
}
```

클라이언트에서도 `Azure-AsyncOperation`에 대한 GET 호출을 수행할 수 있습니다. 엔드포인트에서 작업의 상태를 반환합니다.

다음 표에서는 이전 Enterprise Get price sheet API의 필드를 보여줍니다. 여기에는 Microsoft 고객 계약에 대한 새 가격표의 해당 필드가 포함됩니다.

| 이전 속성 | 새 속성 | 메모 |
| --- | --- | --- |
| billingPeriodId  | _해당 사항 없음_ | 해당 사항 없음 Microsoft 고객 계약의 경우 billingPeriodId 개념이 청구서 및 관련 가격표로 대체되었습니다. |
| meterId  | meterId | &nbsp;  |
| unitOfMeasure  | unitOfMeasure | 정확한 문자열 값은 다를 수 있습니다. |
| includedQuantity  | includedQuantity | Microsoft 고객 계약의 서비스에는 적용되지 않습니다. |
| partNumber  | _해당 사항 없음_ | 그 대신, productOrderName(offerID와 동일)과 meterID의 조합을 사용합니다. |
| unitPrice  | unitPrice | 단가는 Microsoft 고객 계약에서 사용된 서비스에 적용됩니다. |
| currencyCode  | pricingCurrency | Microsoft 고객 계약에는 가격이 가격 책정 통화 및 청구 통화로 표시됩니다. currencyCode는 Microsoft 고객 계약의 pricingCurrency에 해당합니다. |
| offerID | productOrderName | OfferID 대신 productOrderName을 사용해도 되지만 OfferID와는 다릅니다. 그러나 productOrderName과 미터는 레거시 등록의 meterId 및 OfferID와 관련된 Microsoft 고객 계약의 가격을 결정합니다. |

## <a name="consumption-price-sheet-api-operations"></a>Consumption Price Sheet API 작업

기업계약의 경우 Consumption Price Sheet API [Get](/rest/api/consumption/pricesheet/get) 및 [Get By Billing Period](/rest/api/consumption/pricesheet/getbybillingperiod) 작업을 subscriptionId 및 청구 기간별 범위에 사용했습니다. 이 API는 Azure 리소스 관리 인증을 사용합니다.

Price Sheet API를 사용하여 범위의 가격표 정보를 가져오는 방법은 다음과 같습니다.

| 방법 | 요청 URI |
| --- | --- |
| GET | `https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Consumption/pricesheets/default?api-version=2018-10-01` |

Price Sheet API를 사용하여 청구 기간별 가격표 정보를 가져오는 방법은 다음과 같습니다.

| 방법 | 요청 URI |
| --- | --- |
| GET | `https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Billing/billingPeriods/{billingPeriodName}/providers/Microsoft.Consumption/pricesheets/default?api-version=2018-10-01` |

위의 API 엔드포인트 대신 다음 엔드포인트를 Microsoft 고객 계약에 사용합니다.

**Microsoft 고객 계약에 대한 Price Sheet API(비동기 REST API)**

이 API는 Microsoft 고객 계약을 위한 것이며 추가 특성을 제공합니다.

**청구 계정의 청구 프로필 범위 가격표**

이 API는 기존 API입니다. 청구 계정의 청구 프로필에 대한 가격표를 제공하도록 업데이트되었습니다.

## <a name="price-sheet-for-a-scope-by-billing-account"></a>청구 계정별 범위의 가격표

Azure Resource Manager 인증은 청구 계정의 등록 범위에서 가격표를 가져올 때 사용됩니다.

청구 계정의 등록 계정에서 가격표를 가져오는 방법은 다음과 같습니다.

| 방법 | 요청 URI |
| --- | --- |
| GET | `/providers/Microsoft.Billing/billingAccounts/65085863/providers/Microsoft.Consumption/pricesheets/download?api-version=2019-01-01` |

Microsoft 고객 계약의 경우 다음 섹션의 정보를 사용합니다. Microsoft 고객 계약에 사용되는 필드 속성을 제공합니다.

### <a name="price-sheet-for-a-billing-profile-scope-in-a-billing-account"></a>청구 계정의 청구 프로필 범위 가격표

청구 계정 API를 통해 업데이트된 가격표는 CSV 형식으로 가격표를 가져옵니다. MCA의 청구 프로필 범위에서 가격표를 가져오는 방법은 다음과 같습니다.

| 방법 | 요청 URI |
| --- | --- |
| GET | `/providers/Microsoft.Billing/billingAccounts/28ae4b7f-41bb-581e-9fa4-8270c857aa5f/billingProfiles/ef37facb-cd6f-437a-9261-65df15b673f9/providers/Microsoft.Consumption/pricesheets/download?api-version=2019-01-01` |

EA의 등록 범위에서 API 응답과 속성은 동일합니다. 속성은 동일한 MCA 속성에 해당합니다.

[Azure Resource Manager Price Sheet API](/rest/api/consumption/pricesheet)의 이전 속성 및 그와 동일한 새 속성이 다음 표에 나와 있습니다.

| 이전 Azure Resource Manager Price Sheet API 속성  | 새 Microsoft 고객 계약 Price Sheet API 속성   | Description |
| --- | --- | --- |
| 측정기 ID | _meterId_ | 측정기의 고유 식별자입니다. meterID와 동일합니다. |
| 미터 이름 | meterName | 측정기의 이름입니다. 미터는 Azure 서비스의 배포 가능한 리소스를 나타냅니다. |
| 미터 범주  | 서비스 | 측정기에 대한 분류 범주의 이름입니다. Microsoft 고객 계약 가격표의 서비스와 동일합니다. 정확한 문자열 값은 다릅니다. |
| 미터 하위 범주 | meterSubCategory | 미터의 하위 분류 범주 이름입니다. 서비스의 상위 수준 기능 세트 차별화 분류를 기준으로 합니다. 기본 SQL DB와 표준 SQL DB를 예로 들 수 있습니다. |
| 미터 영역 | meterRegion | &nbsp;  |
| 단위 | _해당 사항 없음_ | unitOfMeasure에서 구문 분석할 수 있습니다. |
| 측정 단위 | unitOfMeasure | &nbsp;  |
| 부품 번호 | _해당 사항 없음_ | 부품 번호 대신, productOrderName 및 MeterID를 사용하여 청구 프로필의 가격을 고유하게 식별합니다. 필드는 MCA 청구서의 부품 번호 대신 MCA 청구서에 나열됩니다. |
| 단가 | unitPrice | Microsoft 고객 계약 단가입니다. |
| 통화 코드 | pricingCurrency | Microsoft 고객 계약에는 가격이 가격 책정 통화 및 청구 통화로 표시됩니다. 통화 코드는 Microsoft 고객 계약의 pricingCurrency에 해당합니다. |
| 포함된 수량 | includedQuantity | Microsoft 고객 계약의 서비스에는 적용되지 않습니다. 값 0으로 표시됩니다. |
|  제품 ID  | productOrderName | OfferID 대신 productOrderName을 사용합니다. OfferID와 동일하지는 않지만, productOrderName 및 미터는 Microsoft 고객 계약의 가격을 결정합니다. 레거시 등록의 meterId 및 OfferID와 관련되어 있습니다. |

Microsoft 고객 계약의 가격은 기업계약과 다르게 정의됩니다. Enterprise 등록의 서비스 가격은 제품, 부품 번호, 미터 및 제안별로 고유합니다. 부품 번호는 Microsoft 고객 계약에 사용되지 않습니다.

Microsoft 고객 계약에 포함된 Azure Consumption 서비스 가격은 productOrderName 및 meterID에 대해 고유합니다. 서비스 미터와 제품 계획을 나타냅니다.

Usage Details API에서 가격표와 사용량을 조정하려면 productOrderName 및 meterID를 사용하면 됩니다.

청구 프로필 소유자, 기여자, 읽기 권한자 및 청구서 관리자 권한이 있는 사용자는 가격표를 다운로드할 수 있습니다.

가격표에는 사용량 기준 서비스의 가격이 포함됩니다. 서비스에는 Azure 사용량 및 Marketplace 사용량이 포함됩니다. 각 서비스 기간이 끝날 때 가장 마지막 가격이 고정되고 단일 서비스 기간의 사용량에 적용됩니다. Azure 사용 서비스의 경우 서비스 기간은 일반적으로 달력 월입니다.

### <a name="retired-price-sheet-api-fields"></a>사용 중지된 Price Sheet API 필드

다음 필드는 Microsoft 고객 계약 Price Sheet API에서 사용할 수 없거나 동일한 필드가 있습니다.

|사용 중지된 필드| Description|
|---|---|
| billingPeriodId | 적용할 수 없습니다. MCA의 InvoiceId에 해당합니다. |
| offerID | 해당 사항 없음 MCA의 productOrderName에 해당합니다. |
| meterCategory  | 해당 사항 없음 MCA의 서비스에 해당합니다. |
| 단위 | 해당 사항 없음 unitOfMeasure에서 구문 분석할 수 있습니다. |
| currencyCode | MCA의 pricingCurrency와 동일합니다. |
| meterLocation | MCA의 meterRegion과 동일합니다. |
| partNumber partnumber | 부품 번호가 MCA 청구서에 나열되지 않기 때문에 적용할 수 없습니다. 부품 번호 대신 meterId와 productOrderName 조합을 사용하여 가격을 고유하게 식별하세요. |
| totalIncludedQuantity | 해당 사항 없음 |
| pretaxStandardRate  | 해당 사항 없음 |

## <a name="reservation-instance-charge-api-replaced"></a>Reservation Instance Charge API 대체됨

[Reserved Instance Charge API](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-charges)를 사용하여 예약 구매의 청구 트랜잭션을 가져올 수 있습니다. 이 새 API에는 타사 Marketplace 제품을 비롯한 모든 구매가 포함됩니다. 모든 소비 API는 인증 및 권한 부여에 Azure AD를 사용하는 네이티브 Azure API로 대체되었습니다. Azure REST API 호출에 대한 자세한 내용은 [REST 시작](/rest/api/azure/#create-the-request)을 참조하세요. Reserved Instance Charge API는 Transactions API로 대체되었습니다.

Transactions API를 사용하여 예약 구매 트랜잭션을 가져오는 방법은 다음과 같습니다.

| 방법 | 요청 URI |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/transactions?api-version=2018-11-01-preview` |

## <a name="recommendations-apis-replaced"></a>Recommendations API 대체됨

Reserved Instance Purchase Recommendations API는 지난 7일, 30일 또는 60일의 가상 머신 사용량을 제공합니다. 이 API는 예약 구매 추천도 제공합니다. 해당 기능은 아래와 같습니다.

- [Shared Reserved Instance Recommendation API](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-shared-reserved-instance-recommendations)
- [Single Reserved Instance Recommendations API](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-single-reserved-instance-recommendations)

모든 소비 API는 인증 및 권한 부여에 Azure AD를 사용하는 네이티브 Azure API로 대체되었습니다. Azure REST API 호출에 대한 자세한 내용은 [REST 시작](/rest/api/azure/#create-the-request)을 참조하세요. 앞에서 언급한 예약 추천 API는 [Microsoft.Consumption/reservationRecommendations](/rest/api/consumption/reservationrecommendations/list) API로 대체되었습니다.

Reservation Recommendations API를 사용하여 예약 추천을 가져오는 방법은 다음과 같습니다.

| 방법 | 요청 URI |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Consumption/reservationRecommendations?api-version=2019-01-01` |

## <a name="reservation-usage-apis-replaced"></a>Reservation Usage APIs 대체됨

Reserved Instance Usage API를 사용하여 등록의 예약 사용량을 가져올 수 있습니다. 등록에 예약 인스턴스가 여러 개 있는 경우 이 API를 사용하여 모든 예약 인스턴스 구매의 사용량을 가져올 수도 있습니다.

해당 기능은 아래와 같습니다.

- [예약 인스턴스 사용량 세부 정보](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage#request-for-reserved-instance-usage-details)
- [예약 인스턴스 사용량 요약 정보](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage)

모든 소비 API는 인증 및 권한 부여에 Azure AD를 사용하는 네이티브 Azure API로 대체되었습니다. Azure REST API 호출에 대한 자세한 내용은 [REST 시작](/rest/api/azure/#create-the-request)을 참조하세요. 앞에서 언급한 예약 추천 API는 [Microsoft.Consumption/reservationDetails](/rest/api/consumption/reservationsdetails) 및 [Microsoft.Consumption/reservationSummaries](/rest/api/consumption/reservationssummaries) API로 대체되었습니다.

Reservation Details API를 사용하여 예약 세부 정보를 가져오는 방법은 다음과 같습니다.

| 방법 | 요청 URI |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Consumption/reservationDetails?api-version=2019-01-01` |

Reservation Summaries API를 사용하여 예약 요약 정보를 가져오는 방법은 다음과 같습니다.

| 방법 | 요청 URI |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Consumption/reservationSummaries?api-version=2019-01-01` |



## <a name="move-from-cloudyn-to-cost-management"></a>Cloudyn에서 Cost Management로 전환

[Cloudyn](https://cloudyn.com)을 사용하는 조직은 지금부터 비용 관리 요구 사항에 [Azure Cost Management](https://azure.microsoft.com/services/cost-management/)를 사용하는 것이 좋습니다. Cost Management는 온보딩 작업 및 8시간의 대기 시간 없이 Azure Portal에서 사용할 수 있습니다. 자세한 내용은 [Cost Management 설명서](../index.yml)를 참조하세요.

Azure Cost Management로 다음과 같은 일을 할 수 있습니다.

- 미리 정의된 예산에 대한 시간별 비용을 봅니다. 일별 비용 패턴을 분석하여 이상 지출을 식별하고 중지합니다. 태그, 리소스 그룹, 서비스 및 위치별로 비용을 분석합니다.
- 예산을 작성하여 사용량 및 비용에 대한 제한을 설정하고, 중요한 임계값에 도달하면 알림을 받습니다. 사용자 지정 이벤트를 트리거하고 사용 약관에 대한 하드 한도를 적용하도록 작업 그룹을 사용하여 자동화를 설정합니다.
- Azure Advisor의 추천을 사용하여 비용 및 사용량을 최적화합니다. 예산을 초과하지 않도록 예약을 사용하여 구매 최적화를 검색하고, 사용량이 낮은 가상 머신의 크기를 줄이고, 사용하지 않는 리소스를 삭제합니다.
- 매일 스토리지 계정에 CSV 파일을 게시하도록 비용 및 사용량 데이터 내보내기를 예약합니다. 외부 시스템과의 통합을 자동화하여 청구 데이터를 동기화 및 최신 상태로 유지합니다.

## <a name="power-bi-integration"></a>Power BI 통합

비용 보고에 Power BI를 사용할 수도 있습니다. Power BI Desktop을 위한 [Azure Cost Management 커넥터](/power-bi/desktop-connect-azure-cost-management)를 사용하여 Azure 지출을 정확하게 파악할 수 있는 강력하고 사용자 지정된 보고서를 만들 수 있습니다. Azure Cost Management 커넥터는 현재 Microsoft 고객 계약 또는 EA(기업계약)를 체결한 고객을 지원합니다.

## <a name="next-steps"></a>다음 단계

- [Cost Management 설명서](../index.yml)를 읽고 Azure 지출을 모니터링하고 제어하는 방법 또는 Cost Management를 사용하여 리소스 사용을 최적화하는 방법에 대해 알아보세요.
