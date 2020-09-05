---
title: Microsoft 고객 계약 청구서 검토 - Azure
description: 청구서 및 리소스 사용량을 검토하고 Microsoft 고객 계약 청구서에 대한 요금을 확인하는 방법을 알아봅니다.
author: bandersmsft
ms.reviewer: judupont
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: tutorial
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: 4adb3bb1408357d40e3869c2a0d251b11d015195
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88689527"
---
# <a name="tutorial-review-your-microsoft-customer-agreement-invoice"></a>자습서: Microsoft 고객 계약 청구서 검토

개별 트랜잭션을 분석하여 청구서의 요금을 검토할 수 있습니다. Microsoft 고객 계약의 청구 계정에서, 매월 청구 프로필마다 청구서가 생성됩니다. 청구서에는 이전 달의 모든 요금이 포함됩니다. Azure Portal에서 청구서를 보고 요금을 사용량 세부 정보 파일과 비교할 수 있습니다.

이 자습서는 Microsoft 고객 계약을 체결한 Azure 고객에게만 적용됩니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * Azure Portal에서 송장이 발부된 트랜잭션 검토
> * 보류 중인 요금을 검토하여 다음 달 청구서 예상
> * Azure 사용 요금 분석

## <a name="prerequisites"></a>사전 요구 사항

Microsoft 고객 계약에 대한 청구 계정이 있어야 합니다.

Microsoft 고객 계약에 액세스할 수 있어야 합니다. 청구 프로필 소유자, 기여자, 읽기 권한자 또는 청구서 관리자여야 청구 및 사용량 정보를 확인할 수 있습니다. Microsoft 고객 계약의 청구 역할에 대해 자세히 알아보려면 [청구 프로필 역할 및 작업](../manage/understand-mca-roles.md#billing-profile-roles-and-tasks)을 참조하세요.

Azure를 구독한 지 30일 이상이어야 합니다. Azure는 청구서 기간이 끝날 때 사용자에게 청구합니다.

## <a name="sign-in-to-azure"></a>Azure에 로그인

- [https://portal.azure.com](https://portal.azure.com)에서 Azure Portal에 로그인합니다.

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Microsoft 고객 계약에 대한 액세스 확인

계약 유형을 검사하여 Microsoft 고객 계약의 청구 계정에 액세스할 수 있는지 확인합니다.

Azure Portal에서 검색 상자에 *Cost Management + 청구*를 입력한 다음, **Cost Management + 청구**를 선택합니다.

![Cost Management + 청구에 대한 Azure Portal 검색을 보여 주는 스크린샷](./media/review-customer-agreement-bill/billing-search-cost-management-billing.png)

하나의 청구 범위에만 액세스할 수 있는 경우, 왼쪽에서 **속성**을 선택합니다. 청구 계정 유형이 **Microsoft 고객 계약**인 경우, Microsoft 고객 계약의 청구 계정에 액세스할 수 있습니다.

![속성 페이지의 Microsoft 고객 계약을 보여주는 스크린샷](./media/review-customer-agreement-bill/billing-mca-property.png)

여러 청구 범위에 액세스할 수 있는 경우 청구 계정 열의 유형을 확인합니다. 범위 중 하나의 청구 계정 유형이 **Microsoft 고객 계약**인 경우, Microsoft 고객 계약의 청구 계정에 액세스할 수 있습니다.

![청구 계정 목록 페이지의 Microsoft 고객 계약을 보여주는 스크린샷](./media/review-customer-agreement-bill/billing-mca-in-the-list.png)

## <a name="review-invoiced-transactions-in-the-azure-portal"></a>Azure Portal에서 송장이 발부된 트랜잭션 검토

Azure Portal에서 페이지 왼쪽에 있는 **모든 트랜잭션**을 선택합니다. 액세스 권한에 따라 청구 계정, 청구 프로필 또는 청구서 섹션을 선택한 다음, **모든 트랜잭션**을 선택해야 할 수도 있습니다.

[모든 트랜젝션] 페이지에는 다음 정보가 표시됩니다.

![청구된 트랜잭션 목록을 보여주는 스크린샷](./media/review-customer-agreement-bill/mca-billed-transactions-list.png)

|열  |정의  |
|---------|---------|
|Date     | 트랜잭션 날짜  |
|청구서 ID     | 트랜잭션 요금이 청구된 청구서의 식별자입니다. 지원 요청을 신속하게 처리할 수 있도록 지원 요청을 제출할 때 Azure 지원에 ID를 알려주세요. |
|트랜잭션 유형     |  구매, 취소, 사용 요금 등의 트랜잭션 유형  |
|제품군     | 가상 머신용 컴퓨팅 또는 Azure SQL용 데이터베이스 같은 제품 범주|
|제품 SKU     | 제품 인스턴스를 식별하는 고유 코드 |
|Amount     |  트랜잭션 양      |
|청구서 섹션     | 청구 프로필 청구서의 이 섹션에 트랜잭션이 표시됩니다. |
|청구 프로필     | 청구 프로필의 청구서에 트랜잭션이 표시됩니다. |

청구서 ID를 검색하여 해당 청구서의 트랜잭션을 필터링합니다.

### <a name="view-transactions-by-invoice-sections"></a>청구서 섹션별 트랜잭션 보기

청구서 섹션에서는 청구 프로필의 청구서 비용을 구성할 수 있습니다. 자세한 내용은 청구서가 생성되면 청구 프로필의 모든 섹션에 대한 요금이 청구서에 표시됩니다.

다음 이미지는 샘플 청구서의 회계 부서 요금 청구서 섹션을 보여줍니다.

![청구서 섹션 정보를 기준으로 세부 정보를 표시하는 예제 이미지](./media/review-customer-agreement-bill/invoicesection-details.png)

청구서 섹션의 요금을 확인한 후에는 Azure Portal에서 트랜잭션을 살펴보며 요금을 이해할 수 있습니다.

Azure Portal의 [모든 트랜잭션] 페이지로 이동하여 청구서의 트랜잭션을 볼 수 있습니다.

트랜잭션을 보려면 청구서 섹션 이름으로 필터링하세요.

## <a name="review-pending-charges-to-estimate-your-next-invoice"></a>보류 중인 요금을 검토하여 다음 달 청구서 예상

Microsoft 고객 계약의 청구 계정에서, 요금은 청구서가 작성되기 전에는 예상 요금이며 보류 중인 요금으로 간주됩니다. Azure Portal에서 보류 중인 요금을 확인하여 다음 달 청구서를 예상할 수 있습니다. 보류 중인 요금은 예상 요금이며 세금이 포함되지 않은 가격입니다. 다음 달 청구서의 실제 요금은 보류 중인 요금과 다릅니다.

### <a name="view-summary-of-pending-charges"></a>보류 중인 요금의 요약 정보 보기

[Azure Portal](https://portal.azure.com)에 로그인합니다.

청구 프로필을 선택합니다. 액세스 권한에 따라 청구 계정을 선택해야 할 수도 있습니다. 청구 계정에서 **청구 프로필**을 선택한 다음, 청구 프로필을 선택합니다.

화면 맨 위에서 **요약** 탭을 선택합니다.

요금 섹션에는 월간 누계 요금과 지난 달 요금이 표시됩니다.

![Cost Management + 청구에 대한 Azure Portal 검색을 보여 주는 스크린샷](./media/review-customer-agreement-bill/mca-billing-profile-summary.png)

월간 누계 요금은 이번 달의 보류 중인 요금이며 이번 달의 청구서가 생성될 때 청구됩니다. 지난 달의 청구서가 아직 생성되지 않은 경우 지난 달의 요금 역시 보류 중 요금이며 다음 달 청구서에 표시됩니다.

### <a name="view-pending-transactions"></a>보류 중인 트랜잭션 보기

보류 중인 요금을 확인할 때 요금에 포함된 개별 트랜잭션을 분석하여 요금을 이해할 수 있습니다. 현재 보류 중인 사용 요금은 [모든 트랜잭션] 페이지에 표시되지 않습니다. Azure 구독 페이지에서 보류 중인 사용 요금을 볼 수 있습니다.

[Azure Portal](https://portal.azure.com)에 로그인합니다.

Azure Portal에서 검색 상자에 *Cost Management + 청구*를 입력한 다음, **Cost Management + 청구**를 선택합니다.

청구 프로필을 선택합니다. 액세스 권한에 따라 청구 계정을 선택해야 할 수도 있습니다. 청구 계정에서 **청구 프로필**을 선택한 다음, 청구 프로필을 선택합니다.

페이지 왼쪽에서 **모든 트랜잭션**을 선택합니다.

*보류 중*을 검색합니다. **Timespan** 필터를 사용하여 이번 달 또는 지난 달의 보류 중인 요금을 봅니다.

![보류 중인 트랜잭션 목록을 보여주는 스크린샷](./media/review-customer-agreement-bill/mca-pending-transactions-list.png)

### <a name="view-pending-usage-charges"></a>보류 중인 사용 요금 보기

[Azure Portal](https://portal.azure.com)에 로그인합니다.

Azure Portal에서 검색 상자에 *Cost Management + 청구*를 입력한 다음, **Cost Management + 청구**를 선택합니다.

청구 프로필을 선택합니다. 액세스 권한에 따라 청구 계정을 선택해야 할 수도 있습니다. 청구 계정에서 **청구 프로필**을 선택한 다음, 청구 프로필을 선택합니다.

페이지 왼쪽에 있는 **모든 구독**을 선택합니다.

Azure 구독 페이지에는 청구 프로필의 각 구독에 대한 이번 달과 지난 달 요금이 표시 됩니다. 월간 누계 요금은 이번 달의 보류 중인 요금이며 이번 달의 청구서가 생성될 때 청구됩니다. 지난 달의 청구서가 아직 생성되지 않은 경우 지난 달의 요금 역시 보류 중 요금입니다.

![청구 프로필의 Azure 구독 목록을 보여주는 스크린샷](./media/review-customer-agreement-bill/mca-billing-profile-subscriptions-list.png)

## <a name="analyze-your-azure-usage-charges"></a>Azure 사용 요금 분석

Azure 사용량 및 요금 CSV 파일을 사용하여 사용량 기반 요금을 분석합니다. 청구서 또는 보류 중인 요금에 대한 파일을 다운로드할 수 있습니다.

### <a name="download-your-invoice-and-usage-details"></a>청구서 및 사용량 세부 정보 다운로드

액세스 권한에 따라 Cost Management + 청구에서 청구 계정 또는 청구 프로필을 선택해야 할 수도 있습니다. 왼쪽 메뉴에서 **청구** 아래의 **청구서**를 선택합니다. 청구서 그리드에서 다운로드하려는 청구서의 행을 찾습니다. 다운로드 기호 또는 행 끝에 있는 줄임표(...)를 클릭합니다. **다운로드** 상자에서 사용량 세부 정보 파일 및 청구서를 다운로드합니다.

### <a name="view-detailed-usage-by-invoice-section"></a>청구서 섹션별로 자세한 사용량 보기

Azure 사용량 및 요금 파일을 필터링하여 청구서 섹션의 사용 요금을 조정할 수 있습니다.

다음 정보는 회계 부서 청구서 섹션의 컴퓨팅 요금을 조정하는 단계를 안내합니다.

![청구서 섹션 정보를 기준으로 세부 정보를 표시하는 예제 이미지](./media/review-customer-agreement-bill/invoicesection-details.png)

| 청구서 PDF | Azure 사용량 및 요금 CSV |
| --- | --- |
|회계 부서 |invoiceSectionName |
|사용 요금 - Microsoft Azure 플랜 |productOrderName |
|컴퓨팅 |serviceFamily |

CSV 파일의 **invoiceSectionName** 열을 **회계 부서**로 필터링합니다. 그런 다음, CSV 파일의 **productOrderName** 열을 **Microsoft Azure 플랜**으로 필터링합니다. 다음으로, CSV 파일의 **serviceFamily** 열을 **Microsoft.Compute**로 필터링합니다.

![청구서 섹션을 기준으로 필터링한 사용량 및 요금 파일을 보여주는 스크린샷](./media/review-customer-agreement-bill/billing-usage-file-filtered-by-invoice-section.png)

### <a name="view-detailed-usage-by-subscription"></a>구독별 세부 사용량 보기

Azure 사용량 및 요금 CSV 파일을 필터링하여 구독의 사용 요금을 조정할 수 있습니다. 구독의 요금을 확인할 때 Azure 사용량 및 요금 CSV 파일을 사용하여 요금을 분석하세요.

다음 이미지는 Azure Portal의 구독 목록을 보여줍니다.

![청구 프로필의 Azure 구독 목록을 보여주는 스크린샷](./media/review-customer-agreement-bill/mca-billing-profile-subscriptions-list-highlighted.png)

WA_Subscription의 자세한 사용 요금을 확인하려면 Azure 사용량 및 요금 CSV 파일의 **subscriptionName** 열을 **WA_Subscription**으로 필터링합니다.

![구독을 기준으로 필터링한 사용량 및 요금 파일을 보여주는 스크린샷](./media/review-customer-agreement-bill/billing-usage-file-filtered-by-subscription.png)

## <a name="pay-your-bill"></a>청구서 결제

청구서 결제 방법에 대한 지침은 청구서 하단에 표시되어 있습니다. [결제 방법을 알아보세요](mca-understand-your-invoice.md#how-to-pay).

이미 청구서를 결제한 경우 Azure Portal의 청구서 페이지에서 결제 상태를 확인할 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 작업 방법을 알아보았습니다.

> [!div class="checklist"]
> * Azure Portal에서 송장이 발부된 트랜잭션 검토
> * 보류 중인 요금을 검토하여 다음 달 청구서 예상
> * Azure 사용 요금 분석

빠른 시작을 완료하여 비용 분석 사용을 시작합니다.

> [!div class="nextstepaction"]
> [비용 분석 시작](../costs/quick-acm-cost-analysis.md)
