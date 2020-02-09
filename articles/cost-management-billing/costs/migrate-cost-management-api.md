---
title: Microsoft 고객 규약 Api로 EA 마이그레이션-Azure
description: 이 문서는 microsoft 기업계약 (EA)를 Microsoft 고객 계약으로 마이그레이션하는 결과를 이해 하는 데 도움이 됩니다.
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
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/22/2020
ms.locfileid: "76514717"
---
# <a name="migrate-from-enterprise-agreement-to-microsoft-customer-agreement-apis"></a>기업계약에서 Microsoft 고객 계약 Api로 마이그레이션

이 문서는 기업계약 (EA)와 Microsoft 고객 계약 (MCA) 계정의 데이터 구조, API 및 기타 시스템 통합 차이점을 이해 하는 데 도움이 됩니다. Azure Cost Management는 두 계정 유형에 대 한 Api를 지원 합니다. 계속 하기 전에 Microsoft 고객 계약 [에 대 한 설정 청구 계정](../manage/mca-setup-account.md) 문서를 검토 합니다.

기존 EA 계정이 있는 조직은 MCA 계정을 설정 하는 것과 함께이 문서를 검토 해야 합니다. 이전에는 EA 계정을 갱신 하 여 이전 등록에서 새 등록으로 이동 하는 데 필요한 최소한의 작업을 수행 했습니다. 그러나 MCA 계정으로 마이그레이션하려면 추가 작업이 필요 합니다. 모든 비용 관련 Api 및 서비스 제공에 영향을 주는 기본 청구 하위 시스템의 변경 내용 때문에 추가 작업이 필요 합니다.

## <a name="mca-apis-and-integration"></a>MCA Api 및 통합

MCA Api 및 새로운 통합을 사용 하면 다음을 수행할 수 있습니다.

- 네이티브 Azure Api를 통해 전체 API를 사용할 수 있습니다.
- 단일 요금 청구 계정에서 여러 송장을 구성 합니다.
- Azure 서비스 사용, 타사 Marketplace 사용 및 Marketplace 구매를 통해 결합 된 API에 액세스 합니다.
- Azure Cost Management를 사용 하 여 청구 프로필 (등록와 동일) 간의 비용을 봅니다.
- 새 Api에 액세스 하 여 비용을 표시 하 고, 비용이 미리 정의 된 임계값을 초과 하는 경우에 대 한 통지를 받고, 원시 데이터

## <a name="migration-checklist"></a>마이그레이션 검사 목록

다음 항목은 MCA Api로 전환 하는 데 도움이 됩니다.

- 새로운 [Microsoft 고객 계약 청구 계정](../understand/mca-overview.md)에 익숙해져야 합니다.
- 사용 하는 Api를 확인 하 고 다음 섹션에서 대체 되는 Api를 확인 합니다.
- [AZURE RESOURCE MANAGER REST api](/rest/api/azure)에 대해 잘 알고 있어야 합니다.
- Azure Resource Manager Api를 아직 사용 하지 않는 경우 [AZURE AD에 클라이언트 앱을 등록](/rest/api/azure/#register-your-client-application-with-azure-ad)합니다.
- [AZURE AD 인증을 사용](/rest/api/azure/#create-the-request)하도록 모든 프로그래밍 코드를 업데이트 합니다.
- 모든 프로그래밍 코드를 업데이트 하 여 MCA api 호출로 EA API 호출을 바꿉니다.
- 새 오류 코드를 사용 하 여 오류 처리를 업데이트 합니다.
- Cloudyn 및 Power BI 같은 추가 통합 제품을 검토 하 여 필요한 다른 작업을 수행 합니다.

## <a name="ea-apis-replaced-with-mca-apis"></a>EA Api를 MCA Api로 대체

EA Api는 인증 및 권한 부여를 위해 API 키를 사용 합니다. MCA Api는 Azure AD 인증을 사용 합니다.

| 용도 | EA API | MCA API |
| --- | --- | --- |
| 잔액 및 크레딧을 | [/balancesummary](/rest/api/billing/enterprise/billing-enterprise-api-balance-summary) | Microsoft.Billing/billingAccounts/billingProfiles/availableBalanceussae |
| 사용량 (JSON) | [/usagedetails](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#json-format)[/usagedetailsbycustomdate](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#json-format) | [Microsoft.Consumption/usageDetails](/rest/api/consumption/usagedetails)<sup>1</sup> |
| 사용 (CSV) | [/usagedetails/download](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#csv-format)[/usagedetails/submit](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#csv-format) | [Microsoft.Consumption/usageDetails/download](/rest/api/consumption/usagedetails)<sup>1</sup> |
| Marketplace 사용 (CSV) | [/marketplacecharges](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge)[/marketplacechargesbycustomdate](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge) | [Microsoft.Consumption/usageDetails/download](/rest/api/consumption/usagedetails)<sup>1</sup> |
| 청구 기간 | [/billingperiods](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods) | Microsoft.Billing/billingAccounts/billingProfiles/invoices |
| 가격표 | [/pricesheet](/rest/api/billing/enterprise/billing-enterprise-api-pricesheet) | Microsoft. 청구/billingAccounts/billingProfiles/가격표/default/download format = json|csv Microsoft.Billing/billingAccounts/…/billingProfiles/…/invoices/… /pricesheet/default/download format = json|csv Microsoft. 청구/billingAccounts/.. /billingProfiles/.. /providers/Microsoft.Consumption/pricesheets/download  |
| 예약 구매 | [/reservationcharges](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-charges) | Microsoft.Billing/billingAccounts/billingProfiles/transactions |
| 예약 권장 사항 | [/SharedReservationRecommendations](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-shared-reserved-instance-recommendations)[/](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-single-reserved-instance-recommendations)[SingleReservationRecommendations](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-single-reserved-instance-recommendations) | [Microsoft.Consumption/reservationRecommendations](/rest/api/consumption/reservationrecommendations/list) |
| 예약 사용 | [/reservationdetails](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage#request-for-reserved-instance-usage-details)[/reservationsummaries](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage) | [Microsoft.Consumption/reservationDetails](/rest/api/consumption/reservationsdetails)[Microsoft.Consumption/reservationSummaries](/rest/api/consumption/reservationssummaries) |

<sup>1</sup> Azure 서비스 및 타사 Marketplace 사용은 [사용량 세부 정보 API](/rest/api/consumption/usagedetails)에서 사용할 수 있습니다.

다음 Api는 MCA 청구 계정에 사용할 수 있습니다.

| 용도 | Microsoft MCA (고객 계약) API |
| --- | --- |
| 청구 계정<sup>2</sup> | Microsoft.Billing/billingAccounts |
| 청구 프로필<sup>2</sup> | Microsoft.Billing/billingAccounts/billingProfiles |
| 청구서 섹션<sup>2</sup> | Microsoft.Billing/billingAccounts/invoiceSections |
| 송장 | Microsoft.Billing/billingAccounts/billingProfiles/invoices |
| 청구 구독 | {scope}/billingSubscriptions |

<sup>2</sup> api는 범위에 해당 하는 개체의 목록을 반환 합니다. 여기에는 Azure Portal 및 api가 작동 하는 Cost Management 환경이 있습니다. Cost Management 범위에 대 한 자세한 내용은 [범위 이해 및 범위](understand-work-scopes.md)에 대 한 작업을 참조 하세요.

기존 EA Api를 사용 하는 경우 MCA 청구 계정을 지원 하도록 업데이트 해야 합니다. 다음 표에서는 다른 통합 변경 내용을 보여 줍니다.

| 용도 | 이전 제품 | 새 제품 |
| --- | --- | --- |
| Cloudyn | [Cloudyn.com](https://www.cloudyn.com) | [Azure Cost Management](https://azure.microsoft.com/services/cost-management/) |
| Power BI | [Microsoft 소비량 정보](/power-bi/desktop-connect-azure-consumption-insights) 콘텐츠 팩 및 커넥터 |  [Azure Consumption Insights 커넥터](/power-bi/desktop-connect-azure-consumption-insights) |

## <a name="apis-to-get-balance-and-credits"></a>잔액 및 크레딧을 얻기 위한 Api

[잔액 요약](/rest/api/billing/enterprise/billing-enterprise-api-balance-summary) API는 다음에 대 한 월별 요약을 제공 합니다.

- 잔액
- 새 구매
- Azure Marketplace 서비스 요금
- 조정
- 서비스 초과분 요금

모든 소비 Api는 인증 및 권한 부여에 Azure AD를 사용 하는 네이티브 Azure Api로 대체 됩니다. Azure REST Api를 호출 하는 방법에 대 한 자세한 내용은 [rest 시작](/rest/api/azure/#create-the-request)을 참조 하세요.

잔액 요약 API는 Microsoft. 청구/billingAccounts/billingProfiles/제공 되는 잔액 API로 대체 됩니다.

사용 가능한 잔액 API를 사용 하 여 잔액을 확보 하려면 다음을 수행 합니다.

| 방법 | 요청 URI |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/availableBalances?api-version=2018-11-01-preview` |

## <a name="apis-to-get-cost-and-usage"></a>비용 및 사용을 얻기 위한 Api

다음 Api를 사용 하 여 Azure 서비스 사용량, 타사 Marketplace 사용 및 기타 Marketplace 구매의 비용에 대 한 일일 분석을 받습니다. Azure 서비스 및 타사 Marketplace 사용을 위해 다음과 같은 별도의 Api가 병합 되었습니다. 이전 Api는 [Microsoft 사용량 세부 정보/](/rest/api/consumption/usagedetails) API로 대체 되었습니다. 이전에는 잔액 요약에만 표시 되었던 Marketplace 구매를 추가 합니다.

- [사용량 정보 가져오기/다운로드](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#csv-format)
- [사용량 정보 가져오기/제출](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#csv-format)
- [사용량 세부 정보 가져오기/사용량 세부 정보](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#json-format)
- [사용량 세부 정보 가져오기/usagedetailsbycustomdate](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#json-format)
- [Marketplace 스토어 요금/marketplacecharges 가져오기](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge)
- [Marketplace 스토어 요금/marketplacechargesbycustomdate 가져오기](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge)

모든 소비 Api는 인증 및 권한 부여에 Azure AD를 사용 하는 네이티브 Azure Api로 대체 됩니다. Azure REST Api를 호출 하는 방법에 대 한 자세한 내용은 [rest 시작](/rest/api/azure/#create-the-request)을 참조 하세요.

모든 이전 Api는 소비/사용 정보 API로 대체 됩니다.

사용량 세부 정보 API를 사용 하 여 사용 세부 정보를 가져오려면:

| 방법 | 요청 URI |
| --- | --- |
| GET | `https://management.azure.com/{scope}/providers/Microsoft.Consumption/usageDetails?api-version=2019-01-01` |

모든 Cost Management Api와 마찬가지로 사용 정보 API는 여러 범위에서 사용할 수 있습니다. 송장 발부 된 비용은 일반적으로 등록 수준에서 받게 되므로 청구 프로필 범위를 사용 합니다.  Cost Management 범위에 대 한 자세한 내용은 [범위 이해 및 범위](understand-work-scopes.md)에 대 한 작업을 참조 하세요.

| 유형 | ID 형식 |
| --- | --- |
| 청구 계정 | `/Microsoft.Billing/billingAccounts/{billingAccountId}` |
| 청구 프로필 | `/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}` |
| Subscription | `/subscriptions/{subscriptionId}` |
| 리소스 그룹 | `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}` |

다음 querystring 매개 변수를 사용 하 여 프로그래밍 코드를 업데이트 합니다.

| 이전 매개 변수 | 새 매개 변수 |
| --- | --- |
| `billingPeriod={billingPeriod}` | 지원하지 않음 |
| `endTime=yyyy-MM-dd` | `endDate=yyyy-MM-dd` |
| `startTime=yyyy-MM-dd` | `startDate=yyyy-MM-dd` |

응답의 본문도 변경 되었습니다.

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

데이터에서 _값_으로 변경 된 사용 레코드의 배열을 포함 하는 속성 이름입니다. 각 레코드는 상세 속성의 단순 목록을 포함 하는 데 사용 됩니다. 그러나 이제 각 레코드는 태그를 제외 하 고 _속성_이라는 중첩 된 속성에 있습니다. 새 구조는 다른 Azure Api와 일치 합니다. 일부 속성 이름이 변경 되었습니다. 다음 표에서는 해당 하는 속성을 보여 줍니다.

| 이전 속성 | 새 속성 | 메모 |
| --- | --- | --- |
| AccountId | N/A | 구독 작성자는 추적 되지 않습니다. InvoiceSectionId (departmentId과 동일)를 사용 합니다. |
| AccountNameAccountOwnerId 및 AccountOwnerEmail | N/A | 구독 작성자는 추적 되지 않습니다. InvoiceSectionName (departmentName과 동일)를 사용 합니다. |
| AdditionalInfo | additionalInfo | &nbsp;  |
| ChargesBilledSeparately | isAzureCreditEligible | 이러한 속성은 opposites입니다. IsAzureCreditEnabled가 true 이면 ChargesBilledSeparately는 false가 됩니다. |
| ConsumedQuantity | quantity | &nbsp; |
| ConsumedService | consumedService | 정확한 문자열 값은 다를 수 있습니다. |
| ConsumedServiceId | 없음 | &nbsp; |
| CostCenter | costCenter | &nbsp; |
| 날짜 및 usageStartDate | date | &nbsp;  |
| 일 | 없음 | 날짜에서 날짜를 구문 분석 합니다. |
| DepartmentId | invoiceSectionId | 정확한 값은 서로 다릅니다. |
| DepartmentName | invoiceSectionName | 정확한 문자열 값은 다를 수 있습니다. 필요한 경우 부서와 일치 하도록 송장 섹션을 구성 합니다. |
| ExtendedCost 및 Cost | costInBillingCurrency | &nbsp;  |
| InstanceId | resourceId | &nbsp;  |
| 되풀이 요금 | 없음 | &nbsp;  |
| 위치 | 위치 | &nbsp;  |
| MeterCategory | meterCategory | 정확한 문자열 값은 다를 수 있습니다. |
| MeterId | meterId | 정확한 문자열 값은 서로 다릅니다. |
| MeterName | meterName | 정확한 문자열 값은 다를 수 있습니다. |
| MeterRegion | meterRegion | 정확한 문자열 값은 다를 수 있습니다. |
| MeterSubCategory | meterSubCategory | 정확한 문자열 값은 다를 수 있습니다. |
| 월 | 없음 | 날짜에서 월을 구문 분석 합니다. |
| 제품 이름 | 없음 | 가 나 Ername 및 제품 Ordername을 사용 합니다. |
| OfferID | 없음 | &nbsp;  |
| 주문 번호 | 없음 | &nbsp;  |
| PartNumber | 없음 | MeterId 및 제품 Ordername을 사용 하 여 가격을 고유 하 게 식별 합니다. |
| 플랜 이름 | productOrderName | &nbsp;  |
| 제품 | 제품 |   |
| ProductId | productId | 정확한 문자열 값은 서로 다릅니다. |
| 게시자 이름 | publisherName | &nbsp;  |
| ResourceGroup | resourceGroupName | &nbsp;  |
| ResourceGuid | meterId | 정확한 문자열 값은 서로 다릅니다. |
| ResourceLocation | resourceLocation | &nbsp;  |
| ResourceLocationId | 없음 | &nbsp;  |
| ResourceRate | effectivePrice | &nbsp;  |
| ServiceAdministratorId | N/A | &nbsp;  |
| ServiceInfo1 | serviceInfo1 | &nbsp;  |
| ServiceInfo2 | serviceInfo2 | &nbsp;  |
| ServiceName | meterCategory | 정확한 문자열 값은 다를 수 있습니다. |
| ServiceTier | meterSubCategory | 정확한 문자열 값은 다를 수 있습니다. |
| StoreServiceIdentifier | N/A | &nbsp;  |
| SubscriptionGuid | subscriptionId | &nbsp;  |
| SubscriptionId | subscriptionId | &nbsp;  |
| SubscriptionName | subscriptionName | &nbsp;  |
| 태그 | tags | Tags 속성은 root 개체에 적용 되며 nested properties 속성에는 적용 되지 않습니다. |
| UnitOfMeasure | unitOfMeasure | 정확한 문자열 값은 서로 다릅니다. |
| usageEndDate | date | &nbsp;  |
| 년 | 없음 | 날짜에서 연도를 구문 분석 합니다. |
| (신규) | billingCurrency | 요금에 사용 되는 통화입니다. |
| (신규) | billingProfileId | 청구 프로필에 대 한 고유 ID입니다 (등록과 동일). |
| (신규) | billingProfileName | 청구 프로필의 이름 (등록과 같음)입니다. |
| (신규) | chargeType | 을 사용 하 여 Azure 서비스 사용, Marketplace 사용 및 구매를 구분 합니다. |
| (신규) | invoiceId | 송장에 대 한 고유 ID입니다. 현재 열려 있는 달의 경우 비어 있습니다. |
| (신규) | publisherType | 구매할 게시자의 유형입니다. 사용 하기에는 비어 있습니다. |
| (신규) | serviceFamily | 구매 유형입니다. 사용 하기에는 비어 있습니다. |
| (신규) | servicePeriodEndDate | 구매한 서비스의 종료 날짜입니다. |
| (신규) | servicePeriodStartDate | 구매한 서비스의 시작 날짜입니다. |

## <a name="billing-periods-api-replaced-by-invoices-api"></a>청구서 API로 교체 된 청구 기간 API

MCA 청구 계정은 청구 기간을 사용 하지 않습니다. 대신 청구서를 사용 하 여 특정 청구 기간에 비용 범위를 지정 합니다. [청구 기간 api](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods) 는 청구서 api로 대체 됩니다. 모든 소비 Api는 인증 및 권한 부여에 Azure AD를 사용 하는 네이티브 Azure Api로 대체 됩니다. Azure REST Api를 호출 하는 방법에 대 한 자세한 내용은 [rest 시작](/rest/api/azure/#create-the-request)을 참조 하세요.

청구서 API를 사용 하 여 송장을 받으려면:

| 방법 | 요청 URI |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/invoices?api-version=2018-11-01-preview` |

## <a name="price-sheet-apis"></a>가격표 Api

이 섹션에서는 기존 가격표 Api에 대해 설명 하 고 Microsoft 고객 계약에 대 한 가격표 API로 이동 하기 위한 권장 사항을 제공 합니다. 또한 Microsoft 고객 계약에 대 한 가격표 API 및 가격표의 필드에 대해 설명 합니다. [Enterprise get price sheet](/rest/api/billing/enterprise/billing-enterprise-api-pricesheet) 및 [enterprise get 청구 기간](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods) Api는 Microsoft 고객 계약 (Microsoft. 청구/billingAccounts/billingProfiles/가격표)에 대 한 가격표 api로 대체 됩니다. 새 API는 비동기 REST 형식으로 JSON 및 CSV 형식을 모두 지원 합니다. 모든 소비 Api는 인증 및 권한 부여에 Azure AD를 사용 하는 네이티브 Azure Api로 대체 됩니다. Azure REST Api를 호출 하는 방법에 대 한 자세한 내용은 [rest 시작](/rest/api/azure/#create-the-request)을 참조 하세요.

### <a name="billing-enterprise-apis"></a>청구 엔터프라이즈 Api

엔터프라이즈 등록에서 청구 하는 엔터프라이즈 Api를 사용 하 여 가격 및 청구 기간 정보를 가져옵니다. 웹 토큰 Azure Active Directory 사용 되는 인증 및 권한 부여입니다.

가격 책정 시트 및 청구 기간 Api를 사용 하 여 지정 된 엔터프라이즈 등록에 적용 가능한 가격을 가져오려면 다음을 수행 합니다.

| 방법 | 요청 URI |
| --- | --- |
| GET | `https://consumption.azure.com/v2/enrollments/{enrollmentNumber}/pricesheet` |
| GET | `https://consumption.azure.com/v2/enrollments/{enrollmentNumber}/billingPeriods/{billingPeriod}/pricesheet` |

### <a name="price-sheet-api-for-microsoft-customer-agreements"></a>Microsoft 고객 계약에 대 한 가격표 API

Microsoft 고객 계약에 대 한 가격표 API를 사용 하 여 모든 Azure 사용량과 Marketplace 소비 서비스의 가격을 확인할 수 있습니다. 청구 프로필에 대해 표시 된 가격은 청구 프로필에 속한 모든 구독에 적용 됩니다.

가격 책정 시트 API를 사용 하 여 모든 Azure 사용 서비스 가격표 데이터를 CSV 형식으로 볼 수 있습니다.

| 방법 | 요청 URI |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/pricesheet/default/download?api-version=2018-11-01-preview&startDate=2019-01-01&endDate=2019-01-31&format=csv` |

가격 책정 시트 API를 사용 하 여 JSON 형식으로 모든 Azure 사용량 서비스 가격표 데이터를 표시 합니다.

| 방법 | 요청 URI |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/pricesheet/default/download?api-version=2018-11-01-preview&startDate=2019-01-01&endDate=2019-01-31&format=json` |

API를 사용 하면 전체 계정에 대 한 가격표가 반환 됩니다. 그러나 요약 버전의 가격표는 PDF 형식으로 가져올 수도 있습니다. 요약에는 특정 송장에 대해 청구 되는 Azure 사용량과 Marketplace 소비 서비스가 포함 됩니다. 청구서는 송장 요약 PDF 파일에 표시 된 **청구서 번호** 와 동일한 {invoiceId}로 식별 됩니다. 예를 들면 다음과 같습니다.

![InvoiceId에 해당 하는 청구서 번호를 보여 주는 예제 이미지](./media/migrate-cost-management-api/invoicesummary.png)

CSV 형식의 가격표 API를 사용 하 여 청구서 정보를 보려면 다음을 수행 합니다.

| 방법 | 요청 URI |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/2909cffc-b0a2-5de1-bb7b-5d3383764184/billingProfiles/2dcffe0c-ee92-4265-8647-515b8fe7dc78/invoices/{invoiceId}/pricesheet/default/download?api-version=2018-11-01-preview&format=csv` |

JSON 형식으로 가격표 API를 사용 하 여 청구서 정보를 보려면 다음을 수행 합니다.

| 방법 | 요청 URI |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/2909cffc-b0a2-5de1-bb7b-5d3383764184/billingProfiles/2dcffe0c-ee92-4265-8647-515b8fe7dc78/invoices/{invoiceId}/pricesheet/default/download?api-version=2018-11-01-preview&format=json` |

현재 열려 있는 청구 주기 또는 서비스 기간에 Azure 소비 또는 Marketplace 소비 서비스에 대 한 예상 가격을 볼 수도 있습니다.

CSV 형식의 가격표 API를 사용 하 여 사용량 서비스에 대 한 예상 가격을 보려면:

| 방법 | 요청 URI |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billing AccountId}/billingProfiles/{billingProfileId}/pricesheet/default/download?api-version=2018-11-01-preview&format=csv` |

JSON 형식의 가격표 API를 사용 하 여 사용량 서비스에 대 한 예상 가격을 보려면:

| 방법 | 요청 URI |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billing AccountId}/billingProfiles/{billingProfileId}/pricesheet/default/download?api-version=2018-11-01-preview&format=json` |

Microsoft 고객 계약 가격표 Api는 *비동기 REST api*입니다. Api에 대 한 응답은 이전 동기 Api에서 변경 되었습니다. API 응답의 본문도 변경 되었습니다.

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

Api는 [AZURE REST 비동기](../../azure-resource-manager/management/async-operations.md) 형식을 지원 합니다. GET을 사용 하 여 API를 호출 하면 다음과 같은 응답이 표시 됩니다.

```
No Response Body

HTTP Status 202 Accepted
```

다음 헤더는 출력 위치와 함께 전송 됩니다.

```
Location:https://management.azure.com/providers/Microsoft.Consumption/operationresults/{operationId}?sessiontoken=XZDFSnvdkbkdsb==

Azure-AsyncOperation:https://managment.azure.com/providers/Microsoft.Consumption/operationStatus/{operationId}?sessiontoken=XZDFSnvdkbkdsb==

Retry-After: 10

OData-EntityId: {operationId}

```

위치에 대해 다른 GET 호출을 수행 합니다. GET 호출에 대 한 응답은 작업이 완료 또는 실패 상태에 도달할 때까지 동일 합니다. 완료 되 면 GET call location에 대 한 응답으로 다운로드 URL이 반환 됩니다. 작업이 동시에 실행 된 것과 같습니다. 예를 들면 다음과 같습니다.

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

클라이언트는 `Azure-AsyncOperation`에 대 한 GET 호출을 수행할 수도 있습니다. 엔드포인트은 작업에 대 한 상태를 반환 합니다.

다음 표에서는 이전 Enterprise Get price sheet API의 필드를 보여 줍니다. Microsoft 고객 계약에 대 한 새 가격표에 해당 하는 필드를 포함 합니다.

| 이전 속성 | 새 속성 | 메모 |
| --- | --- | --- |
| billingPeriodId  | _해당 사항 없음_ | 해당 사항 없음 Microsoft 고객 계약의 경우 청구서 및 관련 된 가격표가 billingPeriodId의 개념으로 바뀌었습니다. |
| meterId  | meterId | &nbsp;  |
| unitOfMeasure  | unitOfMeasure | 정확한 문자열 값은 다를 수 있습니다. |
| includedQuantity  | includedQuantity | Microsoft 고객 계약의 서비스에는 적용 되지 않습니다. |
| partNumber  | _해당 사항 없음_ | 대신, offerID와 동일 하 게 제품 Ordername의 조합을 사용 하 고 meterID를 사용 합니다. |
| unitPrice  | unitPrice | 단가는 Microsoft 고객 계약에서 사용 되는 서비스에 적용 됩니다. |
| currencyCode  | pricingCurrency | Microsoft 고객 계약에는 가격 책정 통화 및 청구 통화 가격이 표시 됩니다. CurrencyCode는 Microsoft 고객 계약의 pricingCurrency에 해당 합니다. |
| offerID | productOrderName | OfferID 대신에는 제품 Ordername을 사용할 수 있지만 OfferID와는 다릅니다. 그러나 제품 Ordername 및 측정기는 레거시 등록의 meterId 및 OfferID와 관련 된 Microsoft 고객 계약의 가격을 결정 합니다. |

## <a name="consumption-price-sheet-api-operations"></a>소비 가격 시트 API 작업

기업 계약의 경우 subscriptionId 또는 청구 기간에 따라 범위에 대 한 사용량 가격 시트 API [get](/rest/api/consumption/pricesheet/get) 및 [get 청구 기간](/rest/api/consumption/pricesheet/getbybillingperiod) 작업을 사용 했습니다. API는 Azure 리소스 관리 인증을 사용 합니다.

가격표 API를 사용 하 여 범위에 대 한 가격표 정보를 가져오려면 다음을 수행 합니다.

| 방법 | 요청 URI |
| --- | --- |
| GET | `https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Consumption/pricesheets/default?api-version=2018-10-01` |

가격표 API를 사용 하 여 청구 기간에 따라 가격표 정보를 가져오려면:

| 방법 | 요청 URI |
| --- | --- |
| GET | `https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Billing/billingPeriods/{billingPeriodName}/providers/Microsoft.Consumption/pricesheets/default?api-version=2018-10-01` |

위의 API 엔드포인트 대신 Microsoft 고객 계약에 대해 다음을 사용 합니다.

**Microsoft 고객 계약에 대 한 가격표 API (비동기 REST API)**

이 API는 Microsoft 고객 계약을 위한 것 이며 추가 특성을 제공 합니다.

**청구 계정에서 청구 프로필 범위의 가격표**

이 API는 기존 API입니다. 청구 계정에 청구 프로필에 대 한 가격표를 제공 하도록 업데이트 되었습니다.

## <a name="price-sheet-for-a-scope-by-billing-account"></a>청구 계정에의 한 범위에 대 한 가격표

Azure Resource Manager 인증은 청구 계정의 등록 범위에서 가격표를 가져올 때 사용 됩니다.

청구 계정의 등록 계정에서 가격표를 가져오려면 다음을 수행 합니다.

| 방법 | 요청 URI |
| --- | --- |
| GET | `/providers/Microsoft.Billing/billingAccounts/65085863/providers/Microsoft.Consumption/pricesheets/download?api-version=2019-01-01` |

Microsoft 고객 계약의 경우 다음 섹션의 정보를 사용 하십시오. Microsoft 고객 계약에 사용 되는 필드 속성을 제공 합니다.

### <a name="price-sheet-for-a-billing-profile-scope-in-a-billing-account"></a>청구 계정에서 청구 프로필 범위의 가격표

청구 계정 API를 통해 업데이트 된 가격표는 가격 시트를 CSV 형식으로 가져옵니다. MCA의 청구 프로필 범위에서 가격표를 가져오려면 다음을 수행 합니다.

| 방법 | 요청 URI |
| --- | --- |
| GET | `/providers/Microsoft.Billing/billingAccounts/28ae4b7f-41bb-581e-9fa4-8270c857aa5f/billingProfiles/ef37facb-cd6f-437a-9261-65df15b673f9/providers/Microsoft.Consumption/pricesheets/download?api-version=2019-01-01` |

EA의 등록 범위에서 API 응답과 속성은 동일 합니다. 속성은 동일한 MCA 속성에 해당 합니다.

[Azure Resource Manager 가격표 api](/rest/api/consumption/pricesheet) 및 동일한 새 속성에 대 한 이전 속성은 다음 표에 나와 있습니다.

| 이전 Azure Resource Manager 가격표 API 속성  | 새 Microsoft 고객 계약 가격표 API 속성   | Description |
| --- | --- | --- |
| 측정기 ID | _meterId_ | 측정기의 고유 식별자입니다. MeterID와 동일 합니다. |
| 미터 이름 | meterName | 측정기의 이름입니다. 측정기는 Azure 서비스 배포 가능 리소스를 나타냅니다. |
| 미터 범주  | 서비스 | 측정기에 대한 분류 범주의 이름입니다. Microsoft 고객 계약 가격표의 서비스와 동일 합니다. 정확한 문자열 값은 서로 다릅니다. |
| 미터 하위 범주 | meterSubCategory | 미터의 하위 분류 범주 이름입니다. 서비스에서 높은 수준의 기능 집합 차별화의 분류를 기준으로 합니다. 예를 들어 기본 SQL DB와 표준 SQL DB가 있습니다. |
| 측정기 지역 | meterRegion | &nbsp;  |
| 단위 | _해당 사항 없음_ | 측정값에서 측정값을 구문 분석할 수 있습니다. |
| 측정 단위 | unitOfMeasure | &nbsp;  |
| 파트 번호 | _해당 사항 없음_ | 부품 번호 대신, 제품 Ordername 및 MeterID를 사용 하 여 청구 프로필의 가격을 고유 하 게 식별 합니다. 필드는 mca 청구서의 부품 번호 대신 MCA 청구서에 나열 됩니다. |
| 단가 | unitPrice | Microsoft 고객 계약 단가. |
| 통화 코드 | pricingCurrency | Microsoft 고객 계약은 가격 책정 통화 및 청구 통화 가격을 나타냅니다. 통화 코드는 Microsoft 고객 계약의 pricingCurrency와 동일 합니다. |
| 포함된 용량 | includedQuantity | Microsoft 고객 계약의 서비스에는 적용 되지 않습니다. 값이 0 인을 표시 합니다. |
|  제품 ID  | productOrderName | OfferID 대신, 제품 Ordername을 사용 합니다. OfferID와 동일 하지는 않지만 Microsoft 고객 계약의 가격은 제품 주문 이름 및 측정기에 따라 결정 됩니다. 레거시 등록의 meterId 및 OfferID와 관련 되어 있습니다. |

Microsoft 고객 계약의 가격은 엔터프라이즈 계약과 다르게 정의 됩니다. Enterprise 등록의 서비스 가격은 제품, 부품 번호, 미터 및 제안에 대해 고유 합니다. Microsoft 고객 계약에는 부품 번호가 사용 되지 않습니다.

Microsoft 고객 계약의 일부인 Azure 사용량 서비스 가격은 제품 Ordername 및 meterID에 대해 고유 합니다. 서비스 측정기와 제품 계획을 나타냅니다.

사용 세부 정보 API에서 가격표와 사용량을 조정 하려면 제품 Ordername 및 meterID를 사용할 수 있습니다.

청구 프로필 소유자, 참가자, 읽기 권한자 및 송장 관리자 권한이 있는 사용자는 가격표를 다운로드할 수 있습니다.

가격 책정 시트에는 사용량을 기준으로 하는 서비스에 대 한 가격이 포함 됩니다. 서비스에는 Azure 사용량과 Marketplace 사용이 포함 됩니다. 각 서비스 기간이 끝날 때 가장 늦은 가격은 잠기고 단일 서비스 기간 동안 사용에 적용 됩니다. Azure 소비 서비스의 경우 서비스 기간은 일반적으로 달력 월입니다.

### <a name="retired-price-sheet-api-fields"></a>사용 중지 된 가격표 API 필드

Microsoft 고객 계약 가격표 Api에서 다음 필드를 사용할 수 없거나 동일한 필드가 있습니다.

|사용 중지 필드| Description|
|---|---|
| billingPeriodId | 적용할 수 없습니다. MCA의 InvoiceId에 해당 합니다. |
| offerID | 해당 사항 없음 MCA의 제품 Ordername에 해당 합니다. |
| meterCategory  | 해당 사항 없음 는 MCA의 서비스에 해당 합니다. |
| 단위 | 해당 사항 없음 측정값에서 측정값을 구문 분석할 수 있습니다. |
| currencyCode | MCA의 pricingCurrency와 동일 합니다. |
| meterLocation | MCA의 meterRegion와 동일 합니다. |
| partNumber partnumber | 부품 번호가 MCA 청구서에 나열 되지 않기 때문에 적용할 수 없습니다. 부품 번호 대신 meterId 및 제품 Ordername 조합을 사용 하 여 가격을 고유 하 게 식별 합니다. |
| totalIncludedQuantity | 해당 사항 없음 |
| pretaxStandardRate  | 해당 사항 없음 |

## <a name="reservation-instance-charge-api-replaced"></a>예약 인스턴스 요금 API 교체 됨

[예약 인스턴스 요금 API](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-charges)를 사용 하 여 예약 구입에 대 한 청구 트랜잭션을 가져올 수 있습니다. 새 API에는 타사 마켓플레이스 제품을 비롯 한 모든 구매가 포함 됩니다. 모든 소비 Api는 인증 및 권한 부여에 Azure AD를 사용 하는 네이티브 Azure Api로 대체 됩니다. Azure REST Api를 호출 하는 방법에 대 한 자세한 내용은 [rest 시작](/rest/api/azure/#create-the-request)을 참조 하세요. 예약 인스턴스 요금 API는 트랜잭션 API로 대체 됩니다.

트랜잭션 API를 사용 하 여 예약 구매 트랜잭션을 가져오려면 다음을 수행 합니다.

| 방법 | 요청 URI |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/transactions?api-version=2018-11-01-preview` |

## <a name="recommendations-apis-replaced"></a>권장 사항 Api 대체 됨

예약 인스턴스 구매 권장 사항 Api는 최근 7 일, 30 일 또는 60 일 동안 가상 머신 사용량을 제공 합니다. Api는 예약 구매 권장 사항도 제공 합니다. 해당 기능은 아래와 같습니다.

- [공유 예약 인스턴스 권장 사항 API](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-shared-reserved-instance-recommendations)
- [단일 예약 인스턴스 권장 사항 API](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-single-reserved-instance-recommendations)

모든 소비 Api는 인증 및 권한 부여에 Azure AD를 사용 하는 네이티브 Azure Api로 대체 됩니다. Azure REST Api를 호출 하는 방법에 대 한 자세한 내용은 [rest 시작](/rest/api/azure/#create-the-request)을 참조 하세요. 이전에 나열 된 예약 권장 구성 Api는 [reservationRecommendations/](/rest/api/consumption/reservationrecommendations/list) api로 대체 되었습니다.

예약 권장 구성 API를 사용 하 여 예약 권장 사항을 가져오려면:

| 방법 | 요청 URI |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Consumption/reservationRecommendations?api-version=2019-01-01` |

## <a name="reservation-usage-apis-replaced"></a>예약 사용 Api 대체 됨

예약 인스턴스 사용량 API를 사용 하 여 등록에서 예약 사용을 가져올 수 있습니다. 등록에 예약 된 인스턴스가 두 개 이상 있는 경우이 API를 사용 하 여 모든 예약 인스턴스 구매 사용을 가져올 수도 있습니다.

해당 기능은 아래와 같습니다.

- [예약 인스턴스 사용량 세부 정보](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage#request-for-reserved-instance-usage-details)
- [예약 인스턴스 사용량 요약](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage)

모든 소비 Api는 인증 및 권한 부여에 Azure AD를 사용 하는 네이티브 Azure Api로 대체 됩니다. Azure REST Api를 호출 하는 방법에 대 한 자세한 내용은 [rest 시작](/rest/api/azure/#create-the-request)을 참조 하세요. 이전에 나열 된 예약 권장 구성 Api는 [microsoft. 사용량과/reservationDetails](/rest/api/consumption/reservationsdetails) 및 [reservationSummaries](/rest/api/consumption/reservationssummaries) api로 대체 되었습니다.

예약 세부 정보 API를 사용 하 여 예약 세부 정보를 가져오려면:

| 방법 | 요청 URI |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Consumption/reservationDetails?api-version=2019-01-01` |

예약 요약 API를 사용 하 여 예약 요약을 가져오려면 다음을 수행 합니다.

| 방법 | 요청 URI |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Consumption/reservationSummaries?api-version=2019-01-01` |



## <a name="move-from-cloudyn-to-cost-management"></a>Cloudyn에서 Cost Management로 이동

[Cloudyn](https://cloudyn.com) 를 사용 하는 조직은 비용 관리 요구 사항에 따라 [Azure Cost Management](https://azure.microsoft.com/services/cost-management/) 사용을 시작 해야 합니다. Cost Management는 온 보 딩 및 8 시간 대기 시간 없이 Azure Portal에서 사용할 수 있습니다. 자세한 내용은 [Cost Management 설명서](../index.yml)를 참조 하세요.

Azure Cost Management를 사용하면 다음을 수행할 수 있습니다.

- 미리 정의 된 예산에 대해 시간에 따른 비용을 봅니다. 일일 비용 패턴을 분석하여 지출 이상점을 파악하고 차단하세요. 태그, 리소스 그룹, 서비스 및 위치를 기준으로 비용을 나눕니다.
- 예산을 만들어 사용량 및 비용에 대 한 제한을 설정 하 고 중요 한 임계값에 도달 하면 알림 받기를 설정 합니다. 작업 그룹을 기준으로 자동화를 설정하여 사용자 지정 이벤트를 트리거하고 원하는 대로 하드 제한을 적용할 수 있습니다.
- Azure Advisor에서 권장 사항을 사용 하 여 비용 및 사용을 최적화 합니다. 예약을 사용 하 여 구매 최적화를 검색 하 고, 사용 하지 않은 가상 컴퓨터를 다운 크기 조정 하 고, 사용 하지 않는 리소스를 삭제 하
- 매일 저장소 계정에 CSV 파일을 게시 하기 위해 비용 및 사용 데이터 내보내기를 예약 합니다. 외부 시스템과의 통합을 자동화 하 여 청구 데이터를 동기화 된 상태로 유지 하 고 최신 상태로 유지 합니다.

## <a name="power-bi-integration"></a>Power BI 통합

비용 보고를 위해 Power BI를 사용할 수도 있습니다. Power BI Desktop에 대 한 [Azure Cost Management 커넥터](/power-bi/desktop-connect-azure-cost-management) 를 사용 하 여 Azure 소비를 보다 잘 이해할 수 있도록 강력 하 고 사용자 지정 된 보고서를 만들 수 있습니다. Azure Cost Management 커넥터는 현재 Microsoft 고객 계약 또는 EA (기업계약)를 사용 하는 고객을 지원 합니다.

## <a name="next-steps"></a>다음 단계

- Azure 지출을 모니터링 하 고 제어 하는 방법을 알아보려면 [Cost Management 설명서](../index.yml) 를 참조 하세요. 또는 Cost Management를 사용 하 여 리소스 사용을 최적화 하려는 경우.
