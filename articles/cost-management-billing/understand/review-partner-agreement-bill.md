---
title: Microsoft 파트너 계약 청구서 검토 - Azure
description: 청구서 및 리소스 사용량을 검토하고 Microsoft 파트너 계약 청구서에 대한 요금을 확인하는 방법을 알아봅니다.
author: bandersmsft
ms.reviewer: judupont
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: tutorial
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: 9e5ecc67fe86afa15c66d175f0705818154588bf
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88684357"
---
# <a name="tutorial-review-your-microsoft-partner-agreement-invoice"></a>자습서: Microsoft 파트너 계약 청구서 검토

 Microsoft 파트너 계약의 청구 계정에서, 매월 청구 프로필마다 청구서가 생성됩니다. 청구서에는 이전 달의 모든 고객 요금이 포함됩니다. Azure Portal에서 개별 트랜잭션을 분석하여 청구서의 요금을 이해할 수 있습니다. Azure Portal에서 청구서를 보고 요금을 사용량 세부 정보 파일과 비교할 수도 있습니다.

자세한 내용은 [Azure Portal에서 청구서를 다운로드하는 방법](download-azure-invoice.md)을 참조하세요.

이 자습서는 Microsoft 파트너 계약을 체결한 Azure 파트너에게만 적용됩니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * Azure Portal에서 송장이 발부된 트랜잭션 검토
> * 보류 중인 요금을 검토하여 다음 달 청구서 예상
> * Azure 사용 요금 분석

## <a name="prerequisites"></a>필수 구성 요소

Microsoft 파트너 계약에 대한 청구 계정에 액세스할 수 있어야 합니다.

Azure를 구독한 지 30일 이상이어야 합니다. Azure는 청구서 기간이 끝날 때 사용자에게 청구합니다.

## <a name="sign-in-to-azure"></a>Azure에 로그인

- [https://portal.azure.com](https://portal.azure.com)에서 Azure Portal에 로그인합니다.

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Microsoft 고객 계약에 대한 액세스 확인

계약 유형을 검사하여 Microsoft 파트너 계약의 청구 계정에 액세스할 수 있는지 확인합니다.

Azure Portal에서 검색 상자에 *Cost Management + 청구*를 입력한 다음, **Cost Management + 청구**를 선택합니다.

![Azure Portal 검색을 보여 주는 스크린샷](./media/review-partner-agreement-bill/billing-search-cost-management-billing.png)

하나의 청구 범위에만 액세스할 수 있는 경우, 왼쪽에서 **속성**을 선택합니다. 청구 계정 유형이 **Microsoft 파트너 계약**인 경우, Microsoft 파트너 계약의 청구 계정에 액세스할 수 있습니다.

![속성 페이지의 Microsoft 파트너 계약을 보여주는 스크린샷](./media/review-partner-agreement-bill/billing-account-properties-partner-agreement.png)

여러 청구 범위에 액세스할 수 있는 경우 청구 계정 열의 유형을 확인합니다. 범위 중 하나의 청구 계정 유형이 **Microsoft 파트너 계약**인 경우, Microsoft 파트너 계약의 청구 계정에 액세스할 수 있습니다.

![청구 계정 목록 페이지의 Microsoft 파트너 계약을 보여주는 스크린샷](./media/review-partner-agreement-bill/mpa-in-the-list.png)

## <a name="review-invoiced-transactions-in-the-azure-portal"></a>Azure Portal에서 송장이 발부된 트랜잭션 검토

Cost Management + 청구의 페이지 왼쪽에서 **모든 트랜잭션**을 선택합니다. 액세스 권한에 따라 청구 계정, 청구 프로필 또는 고객을 선택한 다음, **모든 트랜잭션**을 선택해야 할 수도 있습니다.

[모든 트랜젝션] 페이지에는 다음 정보가 표시됩니다.

![청구된 트랜잭션 목록을 보여주는 스크린샷](./media/review-partner-agreement-bill/all-transactions.png)

|열  |정의  |
|---------|---------|
|Date     | 트랜잭션 날짜  |
|청구서 ID     | 트랜잭션 요금이 청구된 청구서의 식별자입니다. 지원 요청을 신속하게 처리할 수 있도록 지원 요청을 제출할 때 Azure 지원에 ID를 알려주세요. |
|트랜잭션 유형     |  구매, 취소, 사용 요금 등의 트랜잭션 유형  |
|제품군     | 가상 머신용 컴퓨팅 또는 Azure SQL Database용 데이터베이스와 같은 제품 범주|
|제품 SDKU     | 제품 인스턴스를 식별하는 고유 코드 |
|Amount     |  트랜잭션 양      |
|청구 프로필     | 청구 프로필의 청구서에 트랜잭션이 표시됩니다. |

청구서 ID를 검색하여 해당 청구서의 트랜잭션을 필터링합니다.

## <a name="review-pending-charges-to-estimate-your-next-invoice"></a>보류 중인 요금을 검토하여 다음 달 청구서 예상

요금은 예상 요금이며 청구서가 청구될 때까지 보류 중인 것으로 간주됩니다. Azure Portal에서 Microsoft 파트너 계약 청구 프로필에 대한 보류 중인 요금을 확인하여 다음 청구서를 예측할 수 있습니다. 보류 중인 요금은 예상 요금이며 세금이 포함되지 않은 가격입니다. 다음 달 청구서의 실제 요금은 보류 중인 요금과 다릅니다.

### <a name="view-pending-transactions"></a>보류 중인 트랜잭션 보기

보류 중인 요금을 확인할 때 요금에 포함된 개별 트랜잭션을 분석하여 요금을 이해할 수 있습니다. 현재 보류 중인 사용 요금은 [모든 트랜잭션] 페이지에 표시되지 않습니다. Azure 구독 페이지에서 보류 중인 사용 요금을 볼 수 있습니다.

Cost Management + 청구에서 청구 프로필을 선택합니다. 액세스 권한에 따라 청구 계정을 선택해야 할 수도 있습니다. 청구 계정에서 **청구 프로필**을 선택한 다음, 청구 프로필을 선택합니다.

페이지 왼쪽에서 **모든 트랜잭션**을 선택합니다.

*보류 중*을 검색합니다. **Timespan** 필터를 사용하여 이번 달 또는 지난 달의 보류 중인 요금을 봅니다.

<!-- ![Screenshot that shows the pending transactions list](./media/billing-mpa-understand-your-bill/mpa-billing-profile-pending-transactions.png) -->

### <a name="view-pending-charges-by-customer"></a>고객별 보류 중인 요금 보기

Cost Management + 청구에서 청구 프로필을 선택합니다. 액세스 권한에 따라 청구 계정을 선택해야 할 수도 있습니다. 청구 계정에서 **청구 프로필**을 선택한 다음, 청구 프로필을 선택합니다.

페이지 왼쪽에서 **고객**을 선택합니다.

<!-- ![screenshot of billing profile customers list](./media/billing-mpa-understand-your-bill/mpa-billing-profile-customers.png) -->

고객 페이지에는 청구 프로필과 연결된 각 고객에 대한 이번 달과 지난 달 요금이 표시됩니다. 월간 누계 요금은 이번 달의 보류 중인 요금이며 이번 달의 청구서가 생성될 때 청구됩니다. 지난 달의 청구서가 아직 생성되지 않은 경우 지난 달의 요금 역시 보류 중 요금입니다.

### <a name="view-pending-usage-charges"></a>보류 중인 사용 요금 보기

Cost Management + 청구에서 청구 프로필을 선택합니다. 액세스 권한에 따라 청구 계정을 선택해야 할 수도 있습니다. 청구 계정에서 **청구 프로필**을 선택한 다음, 청구 프로필을 선택합니다.

페이지 왼쪽에서 **Azure 구독**을 선택합니다. Azure 구독 페이지에는 청구 프로필의 각 구독에 대한 이번 달과 지난 달 요금이 표시 됩니다. 월간 누계 요금은 이번 달의 보류 중인 요금이며 이번 달의 청구서가 생성될 때 청구됩니다. 지난 달의 청구서가 아직 생성되지 않은 경우 지난 달의 요금 역시 보류 중 요금입니다.

<!--     ![Screenshot that shows the Azure subscriptions list for MPA billing profile](./media/billing-mpa-understand-your-bill/mpa-billing-profile-subscriptions-list.png) -->

## <a name="analyze-your-azure-usage-charges"></a>Azure 사용 요금 분석

Azure 사용량 및 요금 CSV 파일을 사용하여 사용량 기반 요금을 분석합니다. Azure 사용량 및 요금 파일을 필터링하여 청구서 pdf에 나열된 각 제품에 대한 사용량 요금을 조정할 수 있습니다. 특정 제품에 대한 자세한 사용량 요금을 보려면 Azure 사용량 및 요금 CSV 파일의 **제품** 열을 필터링하여 해당 제품의 이름만 포함하도록 합니다.

Azure 사용량 및 요금 CSV 파일에서 **customerName** 열을 필터링하여 각 고객에 대한 일일 사용량 요금을 볼 수도 있습니다. Azure 구독별 일일 사용량 요금을 확인하려면 **subscriptionName** 열을 필터링합니다.

## <a name="pay-your-bill"></a>청구서 결제

청구서 결제 방법에 대한 지침은 청구서 하단에 표시되어 있습니다. 청구서 날짜로부터 60일 이내에 회선 또는 수표로 요금을 지불할 수 있습니다.

이미 청구서를 결제한 경우 Azure Portal의 청구서 페이지에서 결제 상태를 확인할 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 작업 방법을 알아보았습니다.

> [!div class="checklist"]
> * Azure Portal에서 송장이 발부된 트랜잭션 검토
> * 보류 중인 요금을 검토하여 다음 달 청구서 예상
> * Azure 사용 요금 분석

파트너용 Azure Cost Management를 사용하는 방법에 대해 알아봅니다.

> [!div class="nextstepaction"]
> [파트너용 Azure Cost Management 시작](../costs/get-started-partners.md)
