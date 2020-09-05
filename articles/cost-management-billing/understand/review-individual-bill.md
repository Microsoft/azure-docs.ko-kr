---
title: 개별 Azure 청구서 검토
description: 청구서 및 리소스 사용량을 이해하고 개별 Azure 구독에 대한 요금을 확인하는 방법을 알아봅니다.
author: bandersmsft
ms.reviewer: judupont
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: tutorial
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: a91f3781b490ee6f724e7d28a6be9a96c853426f
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88684629"
---
# <a name="tutorial-review-your-individual-azure-bill"></a>자습서: 개별 Azure 청구서 검토

이 문서는 Azure 청구서를 이해하고 검토하는 데 도움이 됩니다. 각 청구 기간 동안에는 일반적으로 이메일로 청구서를 받습니다. 청구서는 Azure 청구서를 나타냅니다. Azure Portal에서도 청구서에 대한 동일한 비용 정보를 확인할 수 있습니다. 이 자습서에서는 청구서를 자세한 일별 사용량 파일 및 Azure Portal의 비용 분석과 비교합니다.

이 자습서는 개별 구독을 체결한 Azure 고객에게만 적용됩니다. 일반적인 개별 구독은 Azure 웹 사이트에서 직접 구매한 종량제 요금입니다.

예기치 않은 요금을 이해하는 데 도움이 필요한 경우 [예기치 않은 요금 분석](https://docs.microsoft.com/azure/cost-management-billing/manage/getting-started#analyze-unexpected-charges)을 참조하세요. 또는 Azure 구독을 취소해야 하는 경우 [Azure 구독 취소](../manage/cancel-azure-subscription.md)를 참조하세요.

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * 사용 파일을 사용하여 송장이 청구된 요금 비교
> * 비용 분석에서 요금 및 사용량 비교

## <a name="prerequisites"></a>필수 구성 요소

유료 *Microsoft Online Services 프로그램* 청구 계정이 있어야 합니다. 계정은 Azure 웹 사이트를 통해 Azure에 가입할 때 생성됩니다. 예를 들어 [종량제 요금을 사용하는 계정](https://azure.microsoft.com/offers/ms-azr-0003p/)이 있거나 [Visual Studio 구독자](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/)인 경우입니다.

[Azure 체험 계정](https://azure.microsoft.com/offers/ms-azr-0044p/)에 대한 송장은 월별 크레딧 금액을 초과하는 경우에만 생성됩니다.

Azure를 구독한 지 30일 이상이어야 합니다. Azure는 청구서 기간이 끝날 때 사용자에게 청구합니다.

## <a name="sign-in-to-azure"></a>Azure에 로그인

- [https://portal.azure.com](https://portal.azure.com)에서 Azure Portal에 로그인합니다.

## <a name="compare-invoiced-charges-with-usage-file"></a>사용 파일을 사용하여 송장이 청구된 요금 비교

<a name="charges"></a>

사용량 및 비용을 비교하는 첫 번째 단계는 청구서 및 사용 현황 파일을 다운로드하는 것입니다. 자세한 사용 현황 CSV 파일은 청구 기간 및 일별 사용 현황별로 청구 요금을 보여 줍니다. 세금 정보는 포함되지 않습니다. 파일을 다운로드하려면 계정 관리자이거나 소유자 역할이 있어야 합니다.

Azure Portal에서 검색 상자에 *구독*을 입력한 다음, [구독](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)을 클릭합니다.

[![구독으로 이동](./media/review-individual-bill/navigate-subscriptions.png)](./media/review-individual-bill/navigate-subscriptions.png#lightbox)

구독 목록에서 구독을 클릭합니다.

**청구** 아래에서 **청구서**를 클릭합니다.

청구서 목록에서 다운로드하려는 항목을 찾은 다음, 다운로드 기호를 클릭합니다. 이전 청구서를 보려면 시간 간격을 변경해야 할 수도 있습니다. 사용 세부 정보 파일 및 청구서를 생성하는 데 몇 분 정도 걸릴 수 있습니다.

![청구 기간, 다운로드 옵션 및 각 청구 기간별 총 요금을 보여 주는 스크린샷](./media/review-individual-bill/download-invoice.png)

사용량 + 요금 다운로드 창에서 **csv 다운로드**를 클릭하고 **청구서 다운로드**를 클릭합니다.

![청구서 및 사용량 다운로드 옵션을 보여주는 스크린샷](./media/review-individual-bill/usageandinvoice.png)

**사용할 수 없는** 경우에는 다음과 같은 여러 가지 이유로 인해 사용 세부 정보나 청구서가 표시되지 않을 수 있습니다.

- Azure를 구독한 지 30일 이내입니다.
- 청구 기간 동안 사용량이 없습니다.
- 청구서가 아직 생성되지 않았습니다. 청구 기간이 끝날 때까지 기다립니다.
- 청구서를 볼 수 있는 권한이 없습니다. 계정 관리자가 아니면 이전 청구서가 표시되지 않을 수 있습니다. 청구 정보에 액세스하는 방법에 대한 자세한 내용은 [역할을 사용하여 Azure 청구에 대한 액세스 관리](../manage/manage-billing-access.md)를 참조하세요.
- 구독에 초과하지 않은 월별 크레딧 금액 또는 평가판이 있는 경우, Microsoft 고객 계약이 있어야만 청구서를 받을 수 있습니다.

다음으로, 요금을 검토합니다. 청구서에는 세금 및 사용 요금에 대한 값이 표시됩니다.

![Azure 청구서 예제](./media/review-individual-bill/invoice-usage-charge.png)

다운로드한 CSV 사용 현황 파일을 엽니다. 파일의 마지막에 *Cost* 열의 모든 항목에 대한 값의 합계를 계산합니다.

![합계 비용이 포함된 사용 현황 파일의 예제](./media/review-individual-bill/usage-file-usage-charges.png)

 합계 *비용* 값이 청구서에 있는 *사용 요금*과 정확하게 일치해야 합니다.

사용 요금은 미터 수준으로 표시됩니다. 다음 용어는 청구서와 자세한 사용 현황 파일에서 동일한 의미로 사용됩니다. 예를 들어 청구서의 청구 주기는 세부 사용량 파일에 표시된 청구 기간과 같습니다.

| 청구서(PDF) | 자세한 사용 현황(CSV)|
| --- | --- |
|청구 주기 | BillingPeriodStartDate BillingPeriodEndDate |
|Name |미터 범주 |
|Type |미터 하위 범주 |
|리소스 |MeterName |
|지역 |MeterRegion |
|사용 | 수량 |
|포함 |포함된 수량 |
|청구 대상 |초과량 |
|비용 | EffectivePrice|
| 값 | 비용 |

청구서의 **사용 요금** 섹션에는 청구 기간 동안 사용한 각 미터의 총 값(요금)이 나와 있습니다. 예를 들어 다음 이미지는 *P10 Disks* 리소스의 Azure Storage 서비스에 대한 사용량 요금을 보여줍니다.

![청구서 사용 요금](./media/review-individual-bill/invoice-usage-charges.png)

CSV 사용 현황 파일에서 청구서에 표시된 해당 리소스에 대한 *MeterName*을 필터링합니다. 그런 다음, 열의 항목에 대한 *Cost* 값의 합계를 계산합니다. 다음은 송장의 동일한 품목에 해당하는 미터 이름(P10 디스크)에 중점을 둔 예제입니다.

![MeterName에 대한 사용 현황 파일 합계 값](./media/review-individual-bill/usage-file-usage-charge-resource.png)

합계 *비용* 값이 청구서에 청구된 개별 리소스의 *사용 요금*과 정확하게 일치해야 합니다.

자세한 내용은 [Azure 청구서 이해](understand-invoice.md) 및 [Azure 세부 사용 현황 이해](understand-usage.md)를 참조하세요.

## <a name="compare-charges-and-usage-in-cost-analysis"></a>비용 분석에서 요금 및 사용량 비교

Azure Portal의 비용 분석도 요금을 확인하는 데 도움이 됩니다. 송장이 발부된 사용량과 요금에 대한 간략한 개요를 보려면 Azure Portal의 [구독 페이지](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)에서 구독을 선택합니다. 다음으로, **비용 분석**을 클릭한 다음, 보기 목록에서 **청구서 정보**를 클릭합니다.

![청구서 세부 정보 선택을 보여주는 예제](./media/review-individual-bill/cost-analysis-select-invoice-details.png)

다음으로, 날짜 범위 목록에서 청구서에 사용할 기간을 선택합니다. 청구서 번호에 대한 필터를 추가한 다음, 청구서에 있는 것과 일치하는 InvoiceNumber를 선택합니다. 비용 분석에 청구된 항목에 대한 비용 세부 정보가 표시됩니다.

![비용 분석에서 청구된 비용 세부 정보를 보여주는 예제](./media/review-individual-bill/cost-analysis-service-usage-charges.png)

비용 분석에 표시된 요금이 청구서에 청구된 개별 리소스의 *사용 요금*과 정확하게 일치해야 합니다.

![청구서 사용 요금](./media/review-individual-bill/invoice-usage-charges.png)

## <a name="external-services-billed-separately"></a><a name="external"></a>별도로 청구되는 외부 서비스

외부 서비스 또는 마켓플레이스 요금은 타사 소프트웨어 공급 업체가 만든 리소스에 해당됩니다. 이러한 리소스는 Azure Marketplace에서 사용할 수 있습니다. 예를 들어 Barracuda Firewall은 타사가 제공하는 Azure Marketplace 리소스입니다. 방화벽 및 방화벽에 해당하는 미터의 모든 요금은 외부 서비스 요금으로 표시됩니다.

외부 서비스 요금은 별도로 청구됩니다. 이 요금은 Azure 청구서에 표시되지 않습니다. 자세한 내용은 [Azure 외부 서비스 요금의 이해](understand-azure-marketplace-charges.md)를 참조하세요.

### <a name="resources-billed-by-usage-meters"></a>사용량 미터로 청구되는 리소스

Azure는 리소스 요금을 기준으로 직접 청구하지 않습니다. 리소스 요금은 하나 이상의 미터를 사용하여 계산됩니다. 미터는 수명 주기 동안 리소스의 사용을 추적하는 데 사용됩니다. 이러한 미터는 청구서를 계산하는 데 사용됩니다.

예를 들어 가상 머신과 같은 단일 Azure 리소스를 만드는 경우 하나 이상의 미터 인스턴스가 생성됩니다. 미터는 시간별 리소스 사용량을 추적하는 데 사용됩니다. 각 미터는 Azure에서 청구서를 계산하는 데 사용하는 사용량 레코드를 내보냅니다.

예를 들어 Azure에서 만든 단일 VM(가상 머신)의 경우 사용량을 추적하기 위해 다음과 같은 미터가 생성될 수 있습니다.

- 컴퓨팅 시간
- IP 주소 시간
- 데이터 수신
- 데이터 송신
- 표준 관리 디스크
- 표준 관리 디스크 작업
- 표준 IO-디스크
- 표준 IO-블록 Blob 읽기
- 표준 IO-블록 Blob 쓰기
- 표준 IO-블록 Blob 삭제

VM이 생성되면 각 미터가 사용량 레코드를 내보내기 시작합니다. 이 사용량과 미터의 요금은 Azure 계량 시스템에서 추적됩니다.

이전 예제에서와 같이 사용량 CSV 파일에서 청구서를 계산하는 데 사용된 미터를 확인할 수 있습니다.

## <a name="pay-your-bill"></a><a name="payment"></a>청구서 결제

결제 방법으로 신용 카드를 설정한 경우 청구 기간 종료 후 10일 이내에 결제가 자동으로 이루어집니다. 신용 카드 명세서에는 **MSFT Azure** 항목으로 표시됩니다.

청구되는 신용 카드를 변경하려면 [Azure에 대한 신용 카드 추가, 업데이트 또는 제거](../manage/change-credit-card.md)를 참조하세요.

[청구서 결제](../manage/pay-by-invoice.md)의 경우 청구서 맨 아래에 기재된 위치로 결제 금액을 송금합니다.

결제 상태를 확인하려면 [지원 티켓을 만듭니다](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 작업 방법을 알아보았습니다.

> [!div class="checklist"]
> * 사용 파일을 사용하여 송장이 청구된 요금 비교
> * 비용 분석에서 요금 및 사용량 비교

빠른 시작을 완료하여 비용 분석 사용을 시작합니다.

> [!div class="nextstepaction"]
> [비용 분석 시작](../costs/quick-acm-cost-analysis.md)
