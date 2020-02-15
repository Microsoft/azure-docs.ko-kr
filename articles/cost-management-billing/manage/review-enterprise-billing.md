---
title: REST API를 통한 Azure 기업 등록 청구 데이터 검토 | Microsoft Docs
description: Azure REST API를 사용하여 기업 등록 청구 정보를 검토하는 방법을 살펴봅니다.
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
ms.openlocfilehash: 222b0358f027e0a6687ca0710e3cf5b80f292c4e
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/31/2020
ms.locfileid: "75993467"
---
# <a name="review-enterprise-enrollment-billing-using-rest-apis"></a>REST API를 사용하여 기업 등록 청구 검토

Azure Reporting API는 Azure 비용의 검토 및 관리를 지원합니다.

이 문서에서는 Azure REST API를 사용하여 청구 계정, 부서 또는 EA(기업 계약) 등록 계정과 연결된 청구 정보를 검색하는 방법을 알아봅니다. 

## <a name="individual-account-billing"></a>개별 계정 청구

부서의 계정에 대한 사용량 세부 정보를 가져오려면,

```http
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/providers/Microsoft.Consumption/usageDetails?api-version=2018-06-30
Content-Type: application/json   
Authorization: Bearer
```

`{billingAccountId}` 매개 변수가 필요하며 계정의 ID를 포함해야 합니다.

다음과 같은 헤더가 필요합니다. 

|요청 헤더|Description|  
|--------------------|-----------------|  
|*Content-Type:*|필수 사항입니다. `application/json`로 설정합니다.|  
|*권한 부여*|필수 사항입니다. 유효한 `Bearer` [API 키](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based)를 설정합니다. |  

이 예에서는 현재 청구 주기의 세부 정보를 반환하는 동기 호출을 나타냅니다. 성능상의 이유로 동기 호출은 지난 달에 대한 정보를 반환합니다.  [API 비동기](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based) 호출을 통해 36개월의 데이터를 반환할 수도 있습니다.


## <a name="response"></a>응답  

상태 코드 200(OK)은 계정의 상세 비용 목록이 포함된 성공적인 응답에 대해 반환됩니다.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/BillingAccounts/1234/providers/Microsoft.Billing/billingPeriods/201702/providers/Microsoft.Consumption/usageDetails/usageDetailsId1",
      "name": "usageDetailsId1",
      "type": "Microsoft.Consumption/usageDetails",
      "properties": {
        ...
        "usageStart": "2017-02-13T00:00:00Z",
        "usageEnd": "2017-02-13T23:59:59Z",
        "instanceName": "shared1",
        "instanceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Default-Web-eastasia/providers/Microsoft.Web/sites/shared1",
        "currency": "USD",
        "usageQuantity": 0.00328,
        "billableQuantity": 0.00328,
        "pretaxCost": 0.67,
        "isEstimated": false,
        ...
      }
    }
  ]
}
```  

이 예제는 요약된 것입니다. 각 응답 필드 및 오류 처리에 대한 전체 설명은 [청구 계정에 대한 사용량 상세 정보 가져오기](/rest/api/consumption/usagedetails/list#billingaccountusagedetailslist-legacy)를 참조하세요.

## <a name="department-billing"></a>부서 청구 

부서의 모든 계정에 대해 집계된 사용량 세부 정보를 가져옵니다. 

```http
GET https://management.azure.com/providers/Microsoft.Billing/departments/{departmentId}/providers/Microsoft.Consumption/usageDetails?api-version=2018-06-30
Content-Type: application/json   
Authorization: Bearer
```

`{departmentId}` 매개 변수가 필요하며 등록 계정의 부서 ID를 포함해야 합니다.

다음과 같은 헤더가 필요합니다. 

|요청 헤더|Description|  
|--------------------|-----------------|  
|*Content-Type:*|필수 사항입니다. `application/json`로 설정합니다.|  
|*권한 부여*|필수 사항입니다. 유효한 `Bearer` [API 키](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based)를 설정합니다. |  

이 예에서는 현재 청구 주기의 세부 정보를 반환하는 동기 호출을 나타냅니다. 성능상의 이유로 동기 호출은 지난 달에 대한 정보를 반환합니다.  [API 비동기](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based) 호출을 통해 36개월의 데이터를 반환할 수도 있습니다.

### <a name="response"></a>응답  

상태 코드 200(OK)은 해당 부서의 청구서 ID, 지정된 청구 기간의 비용 및 자세한 사용량 정보의 목록이 포함된 성공적인 응답에 대해 반환됩니다.


다음 예제에서는 `1234` 부서에 대한 REST API의 출력을 보여줍니다.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/Departments/1234/providers/Microsoft.Billing/billingPeriods/201702/providers/Microsoft.Consumption/usageDetails/usageDetailsId1",
      "name": "usageDetailsId1",
      "type": "Microsoft.Consumption/usageDetails",
      "properties": {
        "billingPeriodId": "/providers/Microsoft.Billing/Departments/1234/providers/Microsoft.Billing/billingPeriods/201702",
        "invoiceId": "/providers/Microsoft.Billing/Departments/1234/providers/Microsoft.Billing/invoices/201703-123456789",
        "usageStart": "2017-02-13T00:00:00Z",
        "usageEnd": "2017-02-13T23:59:59Z",
        "instanceName": "shared1",
        "instanceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Default-Web-eastasia/providers/Microsoft.Web/sites/shared1",
        "instanceLocation": "eastasia",
        "currency": "USD",
        "usageQuantity": 0.00328,
        "billableQuantity": 0.00328,
        "pretaxCost": 0.67,
        ...
      }
    }
  ]
}
```  

이 예제는 요약된 것입니다. 각 응답 필드 및 오류 처리에 대한 전체 설명은 [부서에 대한 사용량 상세 정보 가져오기](/rest/api/consumption/usagedetails/list#departmentusagedetailslist-legacy)를 참조하세요.

## <a name="enrollment-account-billing"></a>등록 계정 청구

등록 계정에 대해 집계된 사용량 세부 정보를 가져옵니다.

```http
GET GET https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts/{enrollmentAccountId}/providers/Microsoft.Consumption/usageDetails?api-version=2018-06-30
Content-Type: application/json   
Authorization: Bearer
```

`{enrollmentAccountId}` 매개 변수가 필요하며 등록 계정의 ID를 포함해야 합니다.

다음과 같은 헤더가 필요합니다. 

|요청 헤더|Description|  
|--------------------|-----------------|  
|*Content-Type:*|필수 사항입니다. `application/json`로 설정합니다.|  
|*권한 부여*|필수 사항입니다. 유효한 `Bearer` [API 키](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based)를 설정합니다. |  

이 예에서는 현재 청구 주기의 세부 정보를 반환하는 동기 호출을 나타냅니다. 성능상의 이유로 동기 호출은 지난 달에 대한 정보를 반환합니다.  [API 비동기](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based) 호출을 통해 36개월의 데이터를 반환할 수도 있습니다.

### <a name="response"></a>응답  

상태 코드 200(OK)은 해당 부서의 청구서 ID, 지정된 청구 기간의 비용 및 자세한 사용량 정보의 목록이 포함된 성공적인 응답에 대해 반환됩니다.

다음 예제에서는 `1234` 기업 계약에 대한 REST API의 출력을 보여줍니다.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/EnrollmentAccounts/1234/providers/Microsoft.Billing/billingPeriods/201702/providers/Microsoft.Consumption/usageDetails/usageDetailsId1",
      "name": "usageDetailsId1",
      "type": "Microsoft.Consumption/usageDetails",
      "properties": {
        "billingPeriodId": "/providers/Microsoft.Billing/EnrollmentAccounts/1234/providers/Microsoft.Billing/billingPeriods/201702",
        "invoiceId": "/providers/Microsoft.Billing/EnrollmentAccounts/1234/providers/Microsoft.Billing/invoices/201703-123456789",
        "usageStart": "2017-02-13T00:00:00Z",
        "usageEnd": "2017-02-13T23:59:59Z",
        ....
        "currency": "USD",
        "usageQuantity": 0.00328,
        "billableQuantity": 0.00328,
        "pretaxCost": 0.67,
        ...
      }
    }
  ]
}
``` 

이 예제는 요약된 것입니다. 각 응답 필드 및 오류 처리에 대한 전체 설명은 [등록 계정에 대한 사용량 상세 정보 가져오기](/rest/api/consumption/usagedetails/list#enrollmentaccountusagedetailslist-legacy)를 참조하세요.

## <a name="next-steps"></a>다음 단계 
- [기업 보고 개요](https://docs.microsoft.com/azure/billing/billing-enterprise-api) 검토
- [기업 청구 REST API](https://docs.microsoft.com/rest/api/billing/) 살펴보기   
- [Azure REST API 시작하기](https://docs.microsoft.com/rest/api/azure/)   
