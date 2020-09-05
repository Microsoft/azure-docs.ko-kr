---
title: Azure 엔터프라이즈 REST API
description: 이 문서에서는 Azure 엔터프라이즈 등록에 사용되는 REST API에 대해 설명합니다.
author: bandersmsft
ms.author: banders
ms.date: 08/20/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: enterprise
ms.reviewer: boalcsva
ms.openlocfilehash: d9ea567f3285ad773007a69a651e406eb2c94821
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88687436"
---
# <a name="azure-enterprise-rest-apis"></a>Azure 엔터프라이즈 REST API

이 문서에서는 Azure 엔터프라이즈 등록에 사용되는 REST API에 대해 설명합니다. REST API의 일반적인 문제를 해결하는 방법에 대해서도 설명합니다.

## <a name="consumption-and-usage-apis"></a>소비 및 사용량 API

Microsoft Enterprise Azure 고객은 REST API를 통해 사용량 및 청구 정보를 얻을 수 있습니다. 역할 소유자(엔터프라이즈 관리자, 부서 관리자, 계정 소유자)는 Azure EA Portal에서 키를 생성하여 API에 대한 액세스를 사용하도록 설정해야 합니다. 그러면 등록 번호와 키를 받은 사람은 누구든지 API를 통해 데이터에 액세스할 수 있습니다.

### <a name="available-apis"></a>사용 가능한 API

**잔액 및 요약** - [잔액 및 요약 API](../../billing/billing-enterprise-api-balance-summary.md)는 잔액, 신규 구매, Azure Marketplace 서비스 요금, 조정 및 초과분 요금에 대한 월별 요약 정보를 제공합니다. 자세한 내용은 [기업 고객을 위한 보고 API - 잔액 및 요약](/rest/api/billing/enterprise/billing-enterprise-api-balance-summary)을 참조하세요.

**사용량 세부 정보** - [사용량 세부 정보 API](../../billing/billing-enterprise-api-usage-detail.md)는 등록별 사용량과 예상 요금의 일별 분석 결과를 제공합니다. 이 결과에는 인스턴스, 미터 및 부서에 대한 정보도 포함됩니다. 청구 기간 또는 지정된 시작 날짜와 종료 날짜를 기준으로 API를 쿼리할 수 있습니다. 자세한 내용은 [기업 고객을 위한 보고 API - 사용량 세부 정보](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail)를 참조하세요.

**Marketplace 스토어 요금** - [Marketplace 스토어 요금 API](../../billing/billing-enterprise-api-marketplace-storecharge.md)는 지정된 청구 기간 또는 시작 날짜와 종료 날짜의 일별 사용량 기반 마켓플레이스 요금 분석 결과를 반환합니다. 자세한 내용은 [기업 고객을 위한 보고 API - Marketplace 스토어 요금](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge)을 참조하세요.

**가격표**: [가격표 API](/rest/api/billing/enterprise/billing-enterprise-api-pricesheet)는 등록 및 청구 기간의 각 미터에 적용할 수 있는 가격을 제공합니다. 자세한 내용은 [기업 고객을 위한 보고 API - 가격표](/rest/api/billing/enterprise/billing-enterprise-api-pricesheet)를 참조하세요.

**청구 기간** - [청구 기간 API](../../billing/billing-enterprise-api-billing-periods.md)는 등록의 사용량 데이터를 역방향 시간 순서로 표시한 청구 기간 목록을 반환합니다. 각 기간에는 4개의 데이터 세트(BalanceSummary, UsageDetails, Marketplace 요금 및 PriceSheet)에 대한 API 경로를 가리키는 속성이 포함되어 있습니다. 자세한 내용은 [기업 고객을 위한 보고 API - 청구 기간](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods)을 참조하세요.

### <a name="enable-api-data-access"></a>API 데이터 액세스 사용

역할 소유자는 Azure EA Portal에서 다음 단계를 수행할 수 있습니다. **보고서** > **사용량 다운로드** > **API 액세스 키**로 이동합니다. 그러면 다음을 수행할 수 있습니다.

- 기본 및 보조 액세스 키를 생성합니다.
- 액세스 키를 사용하지 않도록 설정합니다.
- 액세스 키의 시작 날짜와 종료 날짜를 봅니다.

### <a name="generate-or-retrieve-the-api-key"></a>API 키 생성 또는 검색

1. 엔터프라이즈 관리자로 로그인합니다.
2. 왼쪽 탐색 창에서 **보고서**를 클릭한 다음, **사용량 다운로드**를 클릭합니다.
3. **API 액세스 키**를 클릭합니다.
4. **등록 액세스 키** 아래에서 키 생성 기호를 선택하여 기본 키 또는 보조 키를 생성합니다.
5. **키 확장**을 선택하여 생성된 전체 API 액세스 키를 살펴봅니다.
6. 즉시 사용할 수 있도록 **복사**를 선택하여 API 액세스 키를 가져옵니다.

![API 액세스 키 페이지를 보여주는 예제](./media/ea-portal-rest-apis/ea-create-generate-or-retrieve-the-api-key.png)

등록에서 엔터프라이즈 관리자가 아닌 사용자에게 API 액세스 키를 제공하려면 다음 단계를 수행합니다.

1. 왼쪽 탐색 창에서 **관리**를 클릭합니다.
2. **DA 요금 보기**(부서 관리자 요금 보기) 옆에 있는 연필 기호를 클릭합니다.
3. **사용**을 선택한 다음, **저장**을 클릭합니다.
4. **AO 요금 보기**(계정 소유자 요금 보기) 옆에 있는 연필 기호를 클릭합니다.
5. **사용**을 선택한 다음, **저장**을 클릭합니다.

![DA 및 AO 요금 보기가 설정된 것을 보여주는 예제](./media/ea-portal-rest-apis/create-ea-generate-or-retrieve-api-key-enable-ao-do-view.png) 이전 단계에서는 API 액세스 키 보유자에게 사용량 보고서의 비용 및 가격 책정 정보에 대한 액세스 권한을 제공합니다.

### <a name="pass-keys-in-the-api"></a>API에서 키 전달

인증 및 권한 부여 요청이 있을 때마다 API 키를 전달합니다. HTTP 헤더에 다음 속성을 전달합니다.

| 요청 헤더 키 | 값 |
| --- | --- |
| 권한 부여 | **전달자 {API\_KEY}** 형식으로 값을 지정합니다.
예: 전달자 \&lt;APIKey\&gt; |

### <a name="swagger"></a>Swagger

Swagger 엔드포인트는 다음 API에 대한 [엔터프라이즈 보고 v3 API](https://consumption.azure.com/swagger/ui/index)에서 사용할 수 있습니다. Swagger는 API를 검사하는 데 도움이 됩니다. Swagger를 사용하여 [AutoRest](https://github.com/Azure/AutoRest) 또는 [Swagger CodeGen](https://swagger.io/swagger-codegen/)을 통해 클라이언트 SDK를 생성합니다. 2014년 5월 1일 이후부터 사용 가능한 데이터는 API를 통해 사용할 수 있습니다.

### <a name="api-response-codes"></a>API 응답 코드

API를 사용하는 경우 응답 상태 코드가 표시됩니다. 다음 표에서는 이러한 코드에 대해 설명합니다.

| 응답 상태 코드 | 메시지 | Description |
| --- | --- | --- |
| 200 | 확인 | 오류 없음 |
| 401 | 권한 없음 | API 키를 찾을 수 없음, 유효하지 않음, 만료됨 등 |
| 404 | 사용할 수 없음 | 보고서 엔드포인트를 찾을 수 없음 |
| 400 | 잘못된 요청 | 잘못된 매개 변수 - 날짜 범위, EA 숫자 등 |
| 500 | 서버 오류 | 예기치 않은 오류 처리 요청 |

### <a name="usage-and-billing-data-update-frequency"></a>사용량 및 청구 데이터 업데이트 빈도

사용량 및 청구 데이터 파일은 현재 청구 월의 24시간마다 업데이트됩니다. 그러나 최대 3일 동안 데이터 대기 시간이 발생할 수 있습니다. 예를 들어 월요일에 사용된 데이터가 목요일까지 데이터 파일에 표시되지 않을 수 있습니다.

### <a name="test-enrollment-for-development"></a>개발을 위한 테스트 등록

Azure 엔터프라이즈 등록이 없는 파트너 또는 개발자가 API에 액세스하려면 테스트 등록을 사용하면 됩니다. 등록 이름은 _EnrollmentNumber 100_이며, 2018년 6월까지 사용 정보를 찾아 테스트할 수 있습니다. 그런 후, 다음 키를 사용하여 API를 호출하고 샘플 데이터를 볼 수 있습니다.

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6ImpoeXA2UU9DWlZmY1pmdmhDVGR1OFdxeTJ5byJ9.eyJFbnJvbGxtZW50TnVtYmVyIjoiMTAwIiwiSWQiOiI1ZTc2ZmNiMy0xN2I4LTQ5ZDItYjdkOC0zMDU0YjUwOWY0MWYiLCJSZXBvcnRWaWV3IjoiU3lzdGVtIiwiUGFydG5lcklkIjoiIiwiRGVwYXJ0bWVudElkIjoiIiwiQWNjb3VudElkIjoiIiwiaXNzIjoiZWEubWljcm9zb2Z0YXp1cmUuY29tIiwiYXVkIjoiY2xpZW50LmVhLm1pY3Jvc29mdGF6dXJlLmNvbSIsImV4cCI6MTU4NjM5MDA2OSwibmJmIjoxNTcwNTc4ODY5fQ.lENR5pCBph6iZCVexUlN1b-j7StaILCyBewVHoILD-_fn8S2o2bHY1qUseGOkBwNlaFQfk2OZIo-jQYvnf3eP3UNrNVTCINT0APbc1RqgwSjZSxugVVHH9jnSzEjONkJaSKmi4tlidk6zkF1-uY-TPJkKxYN_9ar7BgLshF9JGXk7t8OZhxSCxDZc-smntu6ORFDl4gRZZVBKXhqOGjOAdYX5tPiGDF2Bxb68RSzh9Xyr5PXxKLx5yivZzUdo0-GFHo13V9w6a5VQM4R1w4_ro8jF8WAo3mpGZ_ovx_U5IY6zMNmi_AoA1mUyvTGotgcu94RragutoJRxAGHbNJZ0Q
```

### <a name="azure-service-catalog"></a>Azure 서비스 카탈로그

모든 Azure 서비스는 Azure 스토리지 블로그의 카탈로그에 CSV 형식으로 게시됩니다. 카탈로그는 시스템에 사용할 모든 Azure 서비스의 큐레이팅된 카탈로그를 만들어야 하는 경우에 유용합니다. 현재 카탈로그는 [https://azurecatalog.blob.core.windows.net/catalog/AzureCatalog.csv](https://azurecatalog.blob.core.windows.net/catalog/AzureCatalog.csv)에 있습니다.

### <a name="csv-data-file-details"></a>CSV 데이터 파일 정보

다음 정보는 API 보고서의 속성을 설명합니다.

#### <a name="usage-summary"></a>사용 요약

JSON 형식은 CSV 보고서에서 생성됩니다. 따라서 형식이 요약 CSV 형식과 동일합니다. 열 이름은 wielded이므로 JSON 요약 데이터를 사용할 때 데이터 테이블로 역직렬화해야 합니다.

| CSV 열 이름 | JSON 열 이름 | JSON 새 열 | 주석 |
| --- | --- | --- | --- |
| AccountOwnerId | AccountOwnerLiveId | AccountOwnerLiveId |   |
| 계정 이름 | AccountName | AccountName |   |
| ServiceAdministratorId | ServiceAdministratorLiveId | ServiceAdministratorLiveId |   |
| SubscriptionId | SubscriptionId | SubscriptionId |   |
| SubscriptionGuid | MOCPSubscriptionGuid | SubscriptionGuid |   |
| 구독 이름 | SubscriptionName | SubscriptionName |   |
| Date | Date | Date | 서비스 카탈로그 보고서가 실행된 날짜를 표시합니다. 형식은 타임스탬프 없는 날짜 문자열입니다. |
| Month | Month | Month |   |
| 일 | 일 | 일 |   |
| Year | Year | Year |   |
| Product | BillableItemName | Product |   |
| 측정기 ID | ResourceGUID | MeterId |   |
| 미터 범주 | 서비스 | MeterCategory | 서비스를 찾는 데 도움이 됩니다. 여러 ServiceType이 있는 서비스와 관련이 있습니다. Virtual Machines를 예로 들 수 있습니다. |
| 측정기 하위 범주 | ServiceType | MeterSubCategory | 서비스에 대한 두 번째 수준의 세부 정보를 제공합니다. A1 VM(Windows 외)을 예로 들 수 있습니다.  |
| 미터 영역 | ServiceRegion | MeterRegion | 서비스에 필요한 세 번째 수준의 세부 정보입니다. ResourceGUID의 영역 컨텍스트를 찾는 데 유용합니다. |
| 미터 이름 | ServiceResource | MeterName | 서비스의 이름입니다. |
| 사용량 | ResourceQtyConsumed | ConsumedQuantity |   |
| ResourceRate | ResourceRate | ResourceRate |   |
| ExtendedCost | ExtendedCost | ExtendedCost |   |
| 리소스 위치 | ServiceSubRegion | ResourceLocation |   |
| 사용되는 서비스 | ServiceInfo | ConsumedService |   |
| 인스턴스 ID | 구성 요소 | InstanceId |   |
| ServiceInfo1 | ServiceInfo1 | ServiceInfo1 |   |
| ServiceInfo2 | ServiceInfo2 | ServiceInfo2 |   |
| AdditionalInfo | AdditionalInfo | AdditionalInfo |   |
| 태그들 | 태그들 | 태그들 |   |
| 스토어 서비스 식별자   | OrderNumber | StoreServiceIdentifier   |   |
| 부서 이름 | DepartmentName | DepartmentName |   |
| 비용 센터 | CostCenter | CostCenter |   |
| 측정 단위 | UnitOfMeasure | UnitOfMeasure | 예제 값: 시간, GB, 이벤트, 푸시, 단위, 단위 시간, MB, 일별 단위 |
| ResourceGroup | ResourceGroup | ResourceGroup |   |

#### <a name="azure-marketplace-report"></a>Azure Marketplace 보고서

| CSV 열 이름 | JSON 열 이름 | JSON 새 열 |
| --- | --- | --- |
| AccountOwnerId | AccountOwnerId | AccountOwnerId |
| 계정 이름 | AccountName | AccountName |
| SubscriptionId | SubscriptionId | SubscriptionId |
| SubscriptionGuid | SubscriptionGuid | SubscriptionGuid |
| 구독 이름 | SubscriptionName |  SubscriptionName |
| Date | BillingCycle |  날짜(날짜 문자열만 있음. 타임스탬프 없음)
| Month | Month |  Month |
| 일 | 일 |  일 |
| Year | Year |  Year |
| 측정기 ID | MeterResourceId |  MeterId |
| 게시자 이름 | PublisherFriendlyName |  PublisherName |
| 제품 이름 | OfferFriendlyName |  OfferName |
| 플랜 이름 | PlanFriendlyName |  PlanName |
| 사용량 | BilledQty |  ConsumedQuantity |
| ResourceRate | ResourceRate | ResourceRate |
| ExtendedCost | ExtendedCost | ExtendedCost |
| 측정 단위 | UnitOfMeasure | UnitOfMeasure |
| 인스턴스 ID | InstanceId | InstanceId |
| 추가 정보 | AdditionalInfo | AdditionalInfo |
| 태그들 | 태그들 | 태그들 |
| 주문 번호 | OrderNumber | OrderNumber |
| 부서 이름 | DepartmentNames | DepartmentName |
| 비용 센터 | CostCenters |  CostCenter |
| 리소스 그룹 | ResourceGroup |  ResourceGroup |

#### <a name="price-sheet"></a>가격표

| CSV 열 이름 | JSON 열 이름 | 주석 |
| --- | --- | --- |
| 서비스 | 서비스 |  가격 변경 없음 |
| 측정 단위 | UnitOfMeasure |   |
| 초과분 부품 번호 | ConsumptionPartNumber |   |
| 초과분 단가 | ConsumptionPrice |   |
| 통화 코드 | CurrencyCode |     |

### <a name="common-api-issues"></a>일반적인 API 이슈

Azure 엔터프라이즈 REST API를 사용할 때 다음과 같은 일반적인 이슈가 발생할 수 있습니다.

올바른 권한 부여 형식이 없는 API 키를 사용하려 시도할 수 있습니다. API 키는 다음과 같은 사람이 생성합니다.

- 엔터프라이즈 관리자
- DA(부서 관리자)
- AO(계정 소유자)

EA 관리자가 생성한 키는 해당 등록에 대한 모든 정보의 액세스 권한을 제공합니다. 읽기 전용 EA 관리자는 API 키를 생성할 수 없습니다.

DA 또는 AO가 생성한 키는 잔액, 요금 및 가격표 정보에 대한 액세스 권한을 제공하지 않습니다.

API 키는 6개월마다 만료됩니다. 키가 만료되면 키를 다시 생성해야 합니다.

시간 제한 오류가 발생하면 시간 제한 임계값 제한을 늘려서 해결할 수 있습니다.

401 오류(권한 없음) 만료 오류가 발생할 수 있습니다. 일반적으로 이 오류는 키가 만료되어 발생합니다. 키가 만료된 경우 키를 다시 생성할 수 있습니다.

선택한 날짜 범위에 사용 가능한 데이터가 없는 경우 API 호출에서 400 및 404(사용할 수 없음) 오류가 반환될 수 있습니다. 예를 들어 등록 전송이 최근에 시작된 경우 이 오류가 발생할 수 있습니다. 특정 날짜 이후의 데이터는 이제 새 등록에 상주합니다. 그렇지 않으면 새 등록 번호를 사용하여 이전 등록에 있는 정보를 검색하는 경우 오류가 발생할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- Azure EA Portal 관리자는 [Azure EA Portal 관리](ea-portal-administration.md)를 읽고 일반적인 관리 작업에 대해 알아보아야 합니다.
- Azure EA Portal 이슈를 해결하는 데 도움이 필요한 경우 [Azure EA Portal 액세스 문제 해결](ea-portal-troubleshoot.md)을 참조하세요.
