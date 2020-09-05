---
title: Azure 예약을 사용하여 SAP HANA(대규모 인스턴스) 비용 절감
description: HANA 대규모 인스턴스 예약을 구매하기 전에 알아야 할 사항과 구매 방법을 이해합니다.
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: how-to
ms.date: 07/24/2020
ms.author: banders
ms.openlocfilehash: 44f7ce657ea9341779e15f6e4817e8fae1515e47
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88685972"
---
# <a name="save-on-sap-hana-large-instances-with-an-azure-reservation"></a>Azure 예약을 사용하여 SAP HANA(대규모 인스턴스) 비용 절감

1년 또는 3년 동안 Azure 예약을 선구매하면 SAP HLI(HANA(대규모 인스턴스)) 비용을 절감할 수 있습니다. 예약 할인은 구매한 예약 인스턴스와 일치하는 프로비저닝된 HLI SKU에 적용됩니다. 이 문서는 예약을 구매하기 전에 알아야 할 사항과 구매 방법을 이해하는 데 도움이 됩니다.

예약을 구매하면 1년 또는 3년 동안 HLI를 사용하기로 약정하는 것입니다. HLI 예약 용량 구매에는 SKU와 함께 번들로 제공되는 컴퓨팅 및 NFS 스토리지가 포함됩니다. 예약에는 운영 체제, SAP 또는 추가 스토리지 비용과 같은 소프트웨어 라이선스 비용이 포함되지 않습니다. 프로비저닝된 SAP HLI에는 예약 할인이 자동으로 적용됩니다. 예약 기간이 종료되면 프로비저닝된 리소스에 종량제 요금이 적용됩니다.

## <a name="purchase-considerations"></a>구매 시 고려 사항

예약 용량 구매를 진행하기 전에 HLI SKU를 프로비저닝해야 합니다. 예약 요금은 선결제 또는 월 단위로 결제할 수 있습니다. HLI 예약 용량에는 다음과 같은 제한 사항이 적용됩니다.

- 예약 할인은 기업계약 및 Microsoft 고객 계약 구독에만 적용됩니다. 다른 구독은 지원되지 않습니다.
- HLI 예약 용량에는 인스턴스 크기 유연성이 지원되지 않습니다. 예약은 SKU 및 예약을 구매하는 Azure 지역에만 적용됩니다.
- 셀프 서비스 취소 및 교환은 지원되지 않습니다.
- 예약 용량 범위는 단일 범위이므로 단일 구독 및 리소스 그룹에 적용됩니다. 구매한 용량은 다른 구독에서 사용하도록 업데이트할 수 없습니다.
- HANA 예약 용량의 예약 범위를 공유할 수 없습니다. 예약 범위를 분할, 병합 또는 업데이트할 수 없습니다.
- 예약 용량 API 호출을 사용하여 한 번에 하나의 HLI를 구매할 수 있습니다. 추가 수량을 구입하려면 API 호출을 추가로 수행해야 합니다.

Azure Portal에서 또는 [REST API](https://docs.microsoft.com/rest/api/reserved-vm-instances/reservationorder/purchase)를 사용하여 예약 용량을 구매할 수 있습니다.

## <a name="buy-a-hana-large-instance-reservation"></a>HANA 대규모 인스턴스 예약 구매

다음 정보를 사용하여 [예약 주문 REST API](https://docs.microsoft.com/rest/api/reserved-vm-instances/reservationorder/purchase)를 통해 HLI 예약을 구매할 수 있습니다.

### <a name="get-the-reservation-order-and-price"></a>예약 주문 및 가격 정보 가져오기

먼저 [계산 가격](https://docs.microsoft.com/rest/api/reserved-vm-instances/reservationorder/calculate) API를 사용하여 프로비저닝된 HANA 대규모 인스턴스 SKU의 예약 주문 및 가격 정보를 가져옵니다.

다음 예제에서는 [armclient](https://github.com/projectkudu/ARMClient)를 사용하여 PowerShell을 통해 REST API를 호출합니다. 예약 주문과 계산 가격 API 요청 및 요청 본문은 다음과 비슷합니다.

```azurepowershell-interactive
armclient post /providers/Microsoft.Capacity/calculatePrice?api-version=2018-06-01 "{
    'sku': {
        'name': 'SAP_HANA_On_Azure_S224om'
    },
    'location': 'eastus',
    'properties': {
        'reservedResourceType': 'SapHana',
        'billingScopeId': '/subscriptions/11111111-1111-1111-111111111111',
        'term': 'P1Y',
        'quantity': '1',
        'displayName': 'testreservation_S224om',
        'appliedScopes': ['/subscriptions/11111111-1111-1111-111111111111'],
        'appliedScopeType': 'Single',
       'instanceFlexibility': 'NotSupported'
    }
}"
```

데이터 필드와 해당 설명에 대한 자세한 내용은 [HLI 예약 필드](#hli-reservation-fields)를 참조하세요.

다음 예제 응답은 반환되는 내용과 유사합니다. `quoteId`에 대해 반환된 값을 기록해 둡니다.

```
{
  "properties": {
    "currencyCode": "USD",
    "netTotal": 313219.0,
    "taxTotal": 0.0,
    "isTaxIncluded": false,
    "grandTotal": 313219.0,
    "purchaseRequest": {
      "sku": {
        "name": "SAP_HANA_On_Azure_S224om"
      },
      "location": "eastus",
      "properties": {
        "billingScopeId": "/subscriptions/11111111-1111-1111-111111111111",
        "term": "P1Y",
        "billingPlan": "Upfront",
        "quantity": 1,
        "displayName": "testreservation_S224om",
        "appliedScopes": [
          "/subscriptions/11111111-1111-1111-111111111111"
        ],
        "appliedScopeType": "Single",
        "reservedResourceType": "SapHana",
        "instanceFlexibility": "NotSupported"
      }
    },
    "quoteId": "d0fd3a890795",
    "isBillingPartnerManaged": true,
    "reservationOrderId": "22222222-2222-2222-2222-222222222222",
    "skuTitle": "SAP HANA on Azure Large Instances - S224om - US East",
    "skuDescription": "SAP HANA on Azure Large Instances, S224om",
    "pricingCurrencyTotal": {
      "currencyCode": "USD",
      "amount": 313219.0
    }
  }
}
```

### <a name="make-your-purchase"></a>구매

반환된 `quoteId`와 [예약 주문 및 가격 정보 가져오기](#get-the-reservation-order-and-price) 섹션에서 얻은 `reservationOrderId`를 사용하여 구매합니다.

다음은 요청 예제입니다.

```azurepowershell-interactive
armclient put /providers/Microsoft.Capacity/reservationOrders/22222222-2222-2222-2222-222222222222?api-version=2018-06-01 "{
    'sku': {
        'name': 'SAP_HANA_On_Azure_S224om'
    },
    'location': 'eastus',
    'properties': {
        'reservedResourceType': 'SapHana',
        'billingScopeId': '/subscriptions/11111111-1111-1111-111111111111',
        'term': 'P1Y',
        'quantity': '1',
        'displayName': ' testreservation_S224om',
        'appliedScopes': ['/subscriptions/11111111-1111-1111-111111111111/resourcegroups/123'],
        'appliedScopeType': 'Single',
       'instanceFlexibility': 'NotSupported',
       'renew': true,
       'quoteId': 'd0fd3a890795'
    }
}"
```

다음은 응답 예제입니다. 주문이 성공적으로 발주되면 `provisioningState`가 `creating`입니다.

```
{
  "id": "/providers/microsoft.capacity/reservationOrders/22222222-2222-2222-2222-222222222222",
  "type": "Microsoft.Capacity/reservationOrders",
  "name": "22222222-2222-2222-2222-222222222222",
  "etag": 1,
  "properties": {
    "displayName": "testreservation_S224om",
    "requestDateTime": "2020-07-14T05:42:34.3528353Z",
    "term": "P1Y",
    "provisioningState": "Creating",
    "reservations": [
      {
        "sku": {
          "name": "SAP_HANA_On_Azure_S224om"
        },
        "id": "/providers/microsoft.capacity/reservationOrders22222222-2222-2222-2222-222222222222/reservations/33333333-3333-3333-3333-3333333333333",
        "type": "Microsoft.Capacity/reservationOrders/reservations",
        "name": "22222222-2222-2222-2222-222222222222/33333333-3333-3333-3333-3333333333333",
        "etag": 1,
        "location": "eastus”
        "properties": {
          "appliedScopes": [
            "/subscriptions/11111111-1111-1111-111111111111/resourcegroups/123"
          ],
          "appliedScopeType": "Single",
          "quantity": 1,
          "provisioningState": "Creating",
          "displayName": " testreservation_S224om",
          "effectiveDateTime": "2020-07-14T05:42:34.3528353Z",
          "lastUpdatedDateTime": "2020-07-14T05:42:34.3528353Z",
          "reservedResourceType": "SapHana",
          "instanceFlexibility": "NotSupported",
          "skuDescription": "SAP HANA on Azure Large Instances – S224om - US East",
          "renew": true
        }
      }
    ],
    "originalQuantity": 1,
    "billingPlan": "Upfront"
  }
}
```

### <a name="verify-purchase-status-success"></a>구매 상태 성공 확인

예약 주문 GET 요청을 실행하여 구매 주문의 상태를 확인합니다. `provisioningState`가 `Succeeded`여야 합니다.

```azurepowershell-interactive
armclient get /providers/microsoft.capacity/reservationOrders/22222222-2222-2222-2222-222222222222?api-version=2018-06-01
```

응답은 다음 예제와 유사합니다.

```
{
  "id": "/providers/microsoft.capacity/reservationOrders/44444444-4444-4444-4444-444444444444",
  "type": "Microsoft.Capacity/reservationOrders",
  "name": "22222222-2222-2222-2222-222222222222 ",
  "etag": 8,
  "properties": {
    "displayName": "testreservation_S224om",
    "requestDateTime": "2020-07-14T05:42:34.3528353Z",
    "createdDateTime": "2020-07-14T05:44:47.157579Z",
    "expiryDate": "2021-07-14",
    "term": "P1Y",
    "provisioningState": "Succeeded",
    "reservations": [
      {
        "id": "/providers/microsoft.capacity/reservationOrders/22222222-2222-2222-2222-222222222222/reservations/33333333-3333-3333-3333-3333333333333"
      }
    ],
    "originalQuantity": 1,
    "billingPlan": "Upfront"
  }
}
```

## <a name="hli-reservation-fields"></a>HLI 예약 필드

다음 정보는 다양한 예약 필드의 의미를 설명합니다.

  **SKU** HLI SKU 이름입니다. `SAP_HANA_On_Azure_<SKUname>` 형식입니다.

  **위치** 사용 가능한 HLI 지역입니다. 사용 가능한 지역은 [Azure(대규모 인스턴스)의 SAP HANA에 대한 SKU](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-available-skus)를 참조하세요. 위치 문자열 형식을 가져오려면 [위치 가져오기 API 호출](https://docs.microsoft.com/rest/api/resources/subscriptions/listlocations#locationlistresult)을 사용합니다.

  **예약 리소스 종류** `SapHana`

  **구독** 예약 요금을 지불하는 데 사용되는 구독입니다. 구독의 결제 방법으로 예약 요금이 청구됩니다. 구독 유형은 기업계약(제안 번호: MS-AZR-0017P 또는 MS-AZR-0148P) 또는 Microsoft 고객 계약이어야 합니다. 요금은 약정 잔액에서 차감되거나(있는 경우) 초과분 요금으로 청구됩니다.

  **범위** 예약의 범위는 단일 범위여야 합니다.

  **기간** 1년 또는 3년입니다. `P1Y` 또는 `P3Y`로 표시됩니다.

  **수량** 예약에서 구매하는 인스턴스의 수입니다. 구매 수량은 한 번에 한 HLI입니다. 예약을 더 구매하려면 해당 필드에서 API 호출을 반복합니다.

## <a name="troubleshoot-errors"></a>오류 문제 해결

예약을 구매할 때 다음 예제와 같은 오류가 발생할 수 있습니다. HLI가 구매에 대해 프로비저닝되지 않은 것이 원인일 수 있습니다. 이 경우 Microsoft 계정 팀에 문의하여 HLI를 프로비저닝한 후 예약을 구매해야 합니다.

```
{
  "error": {
    "code": "BadRequest",
    "message": "Capacity check or quota check failed. Please select a different subscription or 
location. You can also go to https://aka.ms/corequotaincrease to learn about quota increase."
  }
} 
```

## <a name="next-steps"></a>다음 단계

- [Postman 및 cURL을 사용하여 Azure REST API를 호출하는 방법](https://docs.microsoft.com/rest/api/azure/#how-to-call-azure-rest-apis-with-postman)을 알아보세요.
- [Azure(대규모 인스턴스)의 SAP HANA에 대한 SKU](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-available-skus)에서 사용 가능한 SKU 목록 및 지역을 확인하세요.