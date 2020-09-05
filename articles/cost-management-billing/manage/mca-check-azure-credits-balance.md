---
title: Microsoft 고객 계약에 대한 Azure 크레딧 잔액 추적
description: Microsoft 고객 계약에 대한 Azure 크레딧 잔액을 확인하는 방법을 알아봅니다.
author: bandersmsft
ms.reviewer: amberb
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 08/20/2019
ms.author: banders
ms.openlocfilehash: 334193f64c49b546bab5f44840df5b3f59e32c4f
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88690581"
---
# <a name="track-microsoft-customer-agreement-azure-credit-balance"></a>Microsoft 고객 계약 Azure 크레딧 잔액 추적

Azure Portal에서 또는 REST API를 통해 Microsoft 고객 계약에 대한 청구 계정의 Azure 크레딧 잔액을 확인할 수 있습니다.

Microsoft 고객 계약에 대한 청구 계정에서 크레딧이 청구 프로필에 할당됩니다. 각 청구 프로필에는 청구서의 요금에 자동으로 적용되는 자체 크레딧이 있습니다. 청구 프로필에 대한 Azure 크레딧 잔액을 확인하려면 청구 프로필에 대한 소유자, 기여자, 읽기 권한자 또는 청구서 관리자 역할이 있거나 청구 계정에 대한 소유자, 기여자 또는 읽기 권한자 역할이 있어야 합니다. 역할에 대해 자세히 알아보려면 [Azure의 Microsoft 고객 계약 관리자 역할 이해](understand-mca-roles.md)를 참조하세요.

> [!NOTE]
> 새 크레딧이 Azure Portal에 표시되는 데 최대 24시간이 걸릴 수 있습니다. 새 크레딧을 받고 포털에서 볼 수 없는 경우 24시간 동안 표시됩니다.

이 문서는 Microsoft 고객 계약에 대한 청구 계정에 적용됩니다. [Microsoft 고객 계약에 액세스할 수 있는지 확인하세요](#check-access-to-a-microsoft-customer-agreement).

## <a name="check-your-credit-balance"></a>크레딧 잔액 확인

### <a name="azure-portal"></a>[Azure Portal](#tab/portal)

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. **Cost Management + 청구**를 검색합니다.

    ![Cost Management + 청구에 대한 포털 내 검색을 보여주는 스크린샷](./media/mca-check-azure-credits-balance/billing-search-cost-management-billing.png)

3. 청구 범위 페이지에서 크레딧 잔액을 추적할 청구 계정을 선택합니다. 청구 계정은 **Microsoft 고객 계약** 유형이어야 합니다.

    ![Cost Management + 청구에 대한 포털 내 검색을 보여주는 스크린샷](./media/mca-check-azure-credits-balance/list-of-scopes.png)

    > [!NOTE]
    >
    > Azure Portal은 사용자가 액세스한 마지막 청구 범위를 기억했다가 다음에 Cost Management + 청구 페이지로 이동하면 해당 범위를 표시합니다. 이전에 Cost Management + 청구 페이지를 방문한 경우 청구 범위 페이지가 표시되지 않습니다. 이런 경우, [올바른 범위](#check-access-to-a-microsoft-customer-agreement)에 있는지 확인하십시오. 그렇지 않으면, [범위를 전환](view-all-accounts.md#switch-billing-scope-in-the-azure-portal)하여 Microsoft 고객 계약에 대한 청구 계정을 선택하십시오.

3. 왼쪽에서 **결제 방법**을 선택한 다음, **Azure 크레딧**을 선택합니다.

   ![청구 프로필에 대한 크레딧 잔액 스크린샷](./media/mca-check-azure-credits-balance/mca-payment-methods.png)

4. Azure 크레딧 페이지에는 다음과 같은 섹션이 있습니다.

   #### <a name="balance"></a>Balance

   잔액 섹션에는 Azure 크레딧 잔액의 요약이 표시됩니다.

   ![청구 프로필에 대한 크레딧 잔액 스크린샷](./media/mca-check-azure-credits-balance/mca-credit-balance.png)

   | 용어               | 정의                           |
   |--------------------|--------------------------------------------------------|
   | 예상 잔액  | 청구된 트랜잭션 및 보류 중인 트랜잭션을 모두 고려한 후의 예상되는 크레딧 금액 |
   | 현재 잔액    | 마지막 청구서의 크레딧 금액입니다. 보류 중인 트랜잭션이 포함되지 않습니다. |

   예상 잔액이 0이 되면 크레딧에 적합한 제품을 비롯한 모든 사용량에 대해 요금이 청구됩니다.

   #### <a name="credits-list"></a>크레딧 목록

   크레딧 목록 섹션에는 Azure 크레딧 목록이 나열됩니다.

   ![청구 프로필에 대한 크레딧 목록 스크린샷](./media/mca-check-azure-credits-balance/mca-credits-list.png)

   | 용어 | 정의 |
   |---|---|
   | 원본 | 크레딧의 취득 원본 |
   | 시작 날짜 | 크레딧을 취득한 날짜 |
   | 만료 날짜 | 크레딧이 만료되는 날짜 |
   | 현재 잔액 | 마지막 청구서의 잔액 |
   | 원래 금액 | 원래 크레딧 금액 |
   | 상태 | 크레딧의 현재 상태입니다. 상태는 활성, 사용됨, 만료됨 또는 곧 만료될 예정임일 수 있습니다. |

   #### <a name="transactions"></a>트랜잭션

   트랜잭션 섹션에는 크레딧 잔액에 영향을 미치는 모든 트랜잭션이 표시됩니다.

   ![청구 프로필에 대한 크레딧 트랜잭션 스크린샷](./media/mca-check-azure-credits-balance/mca-credits-transactions.png)

   | 용어 | 정의 |
   |---|---|
   | 트랜잭션 날짜 | 트랜잭션이 발생한 날짜 |
   | Description | 트랜잭션에 대한 설명 |
   | Amount| 트랜잭션 양 |
   | Balance | 트랜잭션 후 잔액 |

    > [!NOTE]
    >
    > 결제 방법 페이지에 Azure 크레딧이 표시되지 않으면 크레딧이 없거나 올바른 범위를 선택하지 않은 것입니다. 크레딧 또는 청구 프로필 중 하나를 포함하는 결제 계정을 선택하십시오. 범위를 변경하는 방법을 알아보려면 [Azure Portal에서 청구 범위 전환](view-all-accounts.md#switch-billing-scope-in-the-azure-portal)을 참조하세요.

5. 청구 계정 범위에서 Azure 크레딧을 보는 경우 청구 계정에 둘 이상의 청구 프로필이 있으면, Azure 크레딧 페이지에 각 청구 프로필에 대한 Azure 크레딧 요약을 포함하는 표가 표시됩니다. 목록에서 청구 프로필을 선택하고 결제 방법을 선택한 다음, Azure 크레딧을 선택하면 청구 프로필에 대한 세부 정보를 볼 수 있습니다.

    ![청구 계정에 대한 크레딧 목록 스크린샷](./media/mca-check-azure-credits-balance/mca-account-credit-list.png)

### <a name="rest-api"></a>[REST API](#tab/rest)

[Azure 청구](https://docs.microsoft.com/rest/api/billing/) 및 [사용량](https://docs.microsoft.com/rest/api/consumption/) API를 사용하여 청구 계정의 크레딧 잔액을 프로그래밍 방식으로 가져올 수 있습니다.

아래에 표시된 예제에서는 REST API를 사용합니다. 현재 PowerShell 및 Azure CLI는 지원되지 않습니다.

### <a name="find-billing-profiles-you-have-access-to"></a>액세스 권한이 있는 청구 프로필 찾기

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts?$expand=billingProfiles&api-version=2019-10-01-preview
```
API 응답은 청구 계정 및 청구 프로필 목록을 반환합니다.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx",
      "name": "5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx",
      "properties": {
        "accountId": "5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "accountStatus": "Active",
        "accountType": "Enterprise",
        "agreementType": "MicrosoftCustomerAgreement",
        "billingProfiles": [
          {
            "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx",
            "name": "PBFV-xxxx-xxx-xxx",
            "properties": {
              "address": {
                "addressLine1": "AddressLine1",
                "city": "City",
                "companyName": "CompanyName",
                "country": "Country",
                "postalCode": "xxxxx",
                "region": "Region"
              },
              "currency": "USD",
              "displayName": "Development",
              "hasReadAccess": true,
              "invoiceDay": 5,
              "invoiceEmailOptIn": true
            },
            "type": "Microsoft.Billing/billingAccounts/billingProfiles"
          }
        ],
        "displayName": "Contoso",
        "hasReadAccess": true,
      },
      "type": "Microsoft.Billing/billingAccounts"
    }
  ]
}
```

청구 프로필의 `displayName` 속성을 사용하면 크레딧 잔액을 확인하려는 청구 프로필을 식별할 수 있습니다. 청구 프로필의 `id`를 복사합니다. 예를 들어 **개발** 청구 프로필에 대한 크레딧 잔액을 확인하려는 경우 ```/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx```를 복사합니다. 다음 단계에서 사용할 수 있도록 이 값을 어딘가에 붙여 넣습니다.

### <a name="get-azure-credit-balance"></a>Azure 크레딧 잔액 가져오기

`<billingProfileId>`를 첫 번째 단계(```/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx```)에서 복사한 `id`로 바꿔서 다음 요청을 수행합니다.

```json
GET https://management.azure.com<billingProfileId>/providers/Microsoft.Consumption/credits/balanceSummary?api-version=2019-10-01
```

API 응답은 청구 프로필에 대한 예상 및 현재 잔액을 반환합니다.

```json
{
  "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx/providers/Microsoft.Consumption/credits/balanceSummary/57c2e8df-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "name": "57c2e8df-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "Microsoft.Consumption/credits/balanceSummary",
  "eTag": null,
  "properties": {
    "balanceSummary": {
      "estimatedBalance": {
        "currency": "USD",
        "value": 996.13
      },
      "currentBalance": {
        "currency": "USD",
        "value": 997.87
      }
    },
    "pendingCreditAdjustments": {
      "currency": "USD",
      "value": 0.0
    },
    "expiredCredit": {
      "currency": "USD",
      "value": 0.0
    },
    "pendingEligibleCharges": {
      "currency": "USD",
      "value": -1.74
    }
  }
}
```

| 요소 이름  | Description                                                                           |
|---------------|---------------------------------------------------------------------------------------|
| `estimatedBalance` | 청구된 트랜잭션 및 보류 중인 트랜잭션을 모두 고려한 후의 예상되는 크레딧 금액입니다. |
| `currentBalance`   | 마지막 청구서의 크레딧 금액입니다. 보류 중인 트랜잭션이 포함되지 않습니다.    |
| `pendingCreditAdjustments`      | 아직 청구서가 발부되지 않은 환불과 같은 조정입니다.  |
| `expiredCredit`      |  마지막 청구서 이후에 만료된 크레딧입니다.  |
| `pendingEligibleCharges`  | 아직 청구서가 발부되지 않은 크레딧 적격 요금입니다.   |

### <a name="get-list-of-credits"></a>크레딧 목록 가져오기

`<billingProfileId>`를 첫 번째 단계(```/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx```)에서 복사한 `id`로 바꿔서 다음 요청을 수행합니다.

```json
GET https://management.azure.com<billingProfileId>/providers/Microsoft.Consumption/lots?api-version=2019-10-01
```
API 응답은 청구 프로필에 대한 Azure 크레딧 목록을 반환합니다.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx/providers/Microsoft.Consumption/lots/f2ecfd94-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "f2ecfd94-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "type": "Microsoft.Consumption/lots",
      "eTag": null,
      "properties": {
        "originalAmount": {
          "currency": "USD",
          "value": 500.0
        },
        "closedBalance": {
          "currency": "USD",
          "value": 500.0
        },
        "source": "Azure Promotional Credit",
        "startDate": "09/18/2019 21:47:31",
        "expirationDate": "09/18/2020 21:47:30",
        "poNumber": ""
      }
    },
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/xxxx-xxxx-xxx-xxx/providers/Microsoft.Consumption/lots/4ea40eb5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "4ea40eb5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "type": "Microsoft.Consumption/lots",
      "eTag": null,
      "properties": {
        "originalAmount": {
          "currency": "USD",
          "value": 500.0
        },
        "closedBalance": {
          "currency": "USD",
          "value": 497.87
        },
        "source": "Azure Promotional Credit",
        "startDate": "09/18/2019 21:47:31",
        "expirationDate": "09/18/2020 21:47:30",
        "poNumber": ""
      }
    }
  ]
}
```
| 요소 이름  | Description                                                                                               |
|---------------|-----------------------------------------------------------------------------------------------------------|
| `originalAmount` | 원래 크레딧 금액입니다. |
| `closedBalance`   | 마지막 청구서의 잔액입니다.    |
| `source`      | 크레딧을 획득한 사람 및 방법을 정의하는 원본입니다. |
| `startDate`      |  크레딧이 활성화된 날짜입니다.  |
| `expirationDate`  | 크레딧이 만료되는 날짜입니다.   |
| `poNumber`  | 크레딧이 청구된 청구서의 구매 주문 번호입니다.   |

### <a name="get-transactions-that-affected-credit-balance"></a>크레딧 잔액에 영향을 주는 트랜잭션 가져오기

`<billingProfileId>`를 첫 번째 단계(```providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx```)에서 복사한 `id`로 바꿔서 다음 요청을 수행합니다. 필요한 기간에 대한 트랜잭션을 가져오려면 **startDate** 및 **endDate**를 전달해야 합니다.

```json
GET https://management.azure.com<billingProfileId>/providers/Microsoft.Consumption/events?api-version=2019-10-01&startDate=2018-10-01T00:00:00.000Z&endDate=2019-10-11T12:00:00.000Z?api-version=2019-10-01
```
API 응답은 청구 프로필에 대한 크레딧 잔액에 영향을 주는 모든 트랜잭션을 반환합니다.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx`/providers/Microsoft.Consumption/events/e2032eb5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "e2032eb5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "type": "Microsoft.Consumption/events",
      "eTag": null,
      "properties": {
        "transactionDate": "10/11/2019",
        "description": "Credit eligible charges as of 10/11/2019",
        "newCredit": {
          "currency": "USD",
          "value": 0.0
        },
        "adjustments": {
          "currency": "USD",
          "value": 0.0
        },
        "creditExpired": {
          "currency": "USD",
          "value": 0.0
        },
        "charges": {
          "currency": "USD",
          "value": -1.74
        },
        "closedBalance": {
          "currency": "USD",
          "value": 998.26
        },
        "eventType": "PendingCharges",
        "invoiceNumber": ""
      }
    },
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx/providers/Microsoft.Consumption/events/381efd80-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "381efd80-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "type": "Microsoft.Consumption/events",
      "eTag": null,
      "properties": {
        "transactionDate": "09/18/2019",
        "description": "New credit added on 09/18/2019",
        "newCredit": {
          "currency": "USD",
          "value": 500.0
        },
        "adjustments": {
          "currency": "USD",
          "value": 0.0
        },
        "creditExpired": {
          "currency": "USD",
          "value": 0.0
        },
        "charges": {
          "currency": "USD",
          "value": 0.0
        },
        "closedBalance": {
          "currency": "USD",
          "value": 1000.0
        },
        "eventType": "PendingNewCredit",
        "invoiceNumber": ""
      }
    }
  ]
}
```
| 요소 이름  | Description                                                                                               |
|---------------|-----------------------------------------------------------------------------------------------------------|
| `transactionDate` | 트랜잭션이 발생한 날짜입니다. |
| `description` | 트랜잭션에 대한 설명입니다. |
| `adjustments`   | 트랜잭션에 대한 크레딧 조정입니다.    |
| `creditExpired`      | 만료된 크레딧 금액입니다. |
| `charges`      |  트랜잭션에 대한 요금입니다.  |
| `closedBalance`  | 트랜잭션 후의 잔액입니다.   |
| `eventType`  | 트랜잭션 형식입니다.   |
| `invoiceNumber`  | 트랜잭션이 청구되는 청구서의 청구서 번호입니다. 보류 중인 트랜잭션에 대해서는 비어 있습니다.   |

---

## <a name="how-credits-are-used"></a>크레딧을 사용하는 방법

Microsoft 고객 계약의 청구 계정에는 청구서 및 결제 방법을 관리하는 데 청구 프로필을 사용합니다. 각 청구 프로필에 대해 월별 청구서가 생성되며 결제 방법을 사용하여 청구서 금액을 지불합니다.

청구 프로필에 획득하는 크레딧을 할당합니다. 청구 프로필에 대한 청구서가 생성되면 크레딧이 총 요금에 자동으로 적용되어 지불해야 하는 금액이 계산됩니다. 수표/전신 송금 또는 신용 카드와 같은 지불 방법으로 남은 금액을 지불합니다.

## <a name="products-that-arent-covered-by-azure-credits"></a>Azure 크레딧이 적용되지 않는 제품

 다음 제품은 Azure 크레딧이 적용되지 않습니다. 크레딧 잔액에 관계 없이 이러한 제품 사용에 대한 요금이 청구됩니다.

- Canonical
- Citrix XenApp Essentials
- Citrix XenDesktop
- Registered User
- OpenLogic
- Remote Access Rights XenApp Essentials Registered User
- Ubuntu Advantage
- Visual Studio Enterprise(월간)
- Visual Studio Enterprise(연간)
- Visual Studio Professional(월간)
- Visual Studio Professional(연간)
- Azure Marketplace 제품
- Azure 지원 플랜

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Microsoft 고객 계약에 대한 액세스 확인
[!INCLUDE [billing-check-mca](../../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>도움 필요 시 지원에 문의

도움이 필요한 경우 [지원에 문의](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)하여 문제를 신속하게 해결하세요.

## <a name="next-steps"></a>다음 단계

- [Microsoft 고객 계약에 대한 청구 계정 이해](../understand/mca-overview.md)
- [Microsoft 고객 계약 청구서의 용어 이해](../understand/mca-understand-your-invoice.md)
