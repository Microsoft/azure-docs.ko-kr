---
title: REST API로 Azure 구독 청구 데이터 검토 | Microsoft Docs
description: Azure REST API를 사용하여 구독 청구 세부 정보를 검토하는 방법을 살펴봅니다.
services: billing
documentationcenter: na
author: lleonard-msft
manager: ''
editor: ''
ms.assetid: 82D50B98-40F2-44B1-A445-4391EA9EBBAA
ms.service: cost-management-billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: 4dc7771862bc922702c5cc321fb3c566212d799a
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/31/2020
ms.locfileid: "75990568"
---
# <a name="review-subscription-billing-using-rest-apis"></a>Azure REST API를 사용하여 구독 청구 검토

Azure Reporting API는 Azure 비용의 검토 및 관리를 지원합니다.

필터를 통해 필요에 맞게 결과를 사용자 지정할 수 있습니다.

여기서는 REST API를 사용하여 특정 날짜 범위에 대해 구독 청구 세부 정보를 반환하는 방법을 살펴봅니다.

``` http
GET https://management.azure.com/subscriptions/${subscriptionID}/providers/Microsoft.Billing/billingPeriods/${billingPeriod}/providers/Microsoft.Consumption/usageDetails?$filter=properties/usageEnd ge '${startDate}' AND properties/usageEnd le '${endDate}'
Content-Type: application/json
Authorization: Bearer
```

## <a name="build-the-request"></a>요청 빌드

`{subscriptionID}` 매개 변수는 필수이며 대상 구독을 식별합니다.

`{billingPeriod}` 매개 변수는 필수이며 현재 [청구 기간](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-billing-periods)을 지정합니다.

`${startDate}` 및 `${endDate}` 매개 변수는 이 예에서는 필수이나 엔드포인트에서는 선택 사항입니다. YYYY-MM-DD 문자열 형식으로 날짜 범위를 지정합니다(예: `'20180501'` 및 `'20180615'`).

다음과 같은 헤더가 필요합니다.

|요청 헤더|Description|
|--------------------|-----------------|
|*Content-Type:*|필수 사항입니다. `application/json`로 설정합니다.|
|*권한 부여*|필수 사항입니다. 유효한 `Bearer` [액세스 토큰](https://docs.microsoft.com/rest/api/azure/#authorization-code-grant-interactive-clients)으로 설정합니다. |

## <a name="response"></a>응답

상태 코드 200(OK)은 계정의 상세 비용 목록이 포함된 성공적인 응답에 대해 반환됩니다.

``` json
{
  "value": [
    {
      "id": "/subscriptions/{$subscriptionID}/providers/Microsoft.Billing/billingPeriods/201702/providers/Microsoft.Consumption/usageDetails/{$detailsID}",
      "name": "{$detailsID}",
      "type": "Microsoft.Consumption/usageDetails",
      "properties": {
        "billingPeriodId": "/subscriptions/${subscriptionID}/providers/Microsoft.Billing/billingPeriods/${billingPeriod}",
        "invoiceId": "/subscriptions/${subscriptionID}/providers/Microsoft.Billing/invoices/${invoiceID}",
        "usageStart": "${startDate}}",
        "usageEnd": "${endDate}",
        "currency": "USD",
        "usageQuantity": "${usageQuantity}",
        "billableQuantity": "${billableQuantity}",
        "pretaxCost": "${cost}",
        "meterId": "${meterID}",
        "meterDetails": "${meterDetails}"
      }
    }
  ],
  "nextLink": "${nextLinkURL}"
}
```

**값**의 각 항목은 서비스 사용과 관련한 세부 정보를 나타냅니다.

|응답 속성|Description|
|----------------|----------|
|**subscriptionGuid** | 구독의 전역 고유 ID |
|**startDate** | 사용 시작 날짜 |
|**endDate** | 사용 종료 날짜 |
|**useageQuantity** | 사용한 수량 |
|**billableQuantity** | 실제 청구 수량 |
|**pretaxCost** | 세전 청구 금액 |
|**meterDetails** | 사용 관련 상세 정보 |
|**nextLink**| 설정한 경우 세부 정보의 다음 "페이지" URL을 지정합니다. 페이지가 마지막이면 비어 있습니다. |

이 예는 요약된 것입니다. 각 응답 필드에 대한 전체 설명은 [사용량 상세 정보 목록](https://docs.microsoft.com/rest/api/consumption/usagedetails/list#usagedetailslistforbillingperiod-legacy)을 참조하세요.

다른 상태 코드는 오류 조건을 나타냅니다. 이런 경우 응답 개체가 요청 실패 이유를 설명합니다.

``` json
{
  "error": [
    {
      "code": "Error type.",
      "message": "Error response describing why the operation failed."
    }
  ]
}
```

## <a name="next-steps"></a>다음 단계
- [기업 보고 개요](https://docs.microsoft.com/azure/billing/billing-enterprise-api) 검토
- [기업 청구 REST API](https://docs.microsoft.com/rest/api/billing/) 살펴보기
- [Azure REST API 시작하기](https://docs.microsoft.com/rest/api/azure/)
