---
title: Azure 외부 서비스 요금의 이해
description: 이전에는 Marketplace로 알려진 외부 서비스의 요금 청구, Azure의 요금에 대해 알아봅니다.
author: bandersmsft
ms.reviewer: judupont
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 08/20/2020
ms.author: banders
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 8da85ec5781ff9575cf380092fea9e41d6af8786
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88686125"
---
# <a name="understand-your-azure-external-services-charges"></a>Azure 외부 서비스 요금의 이해
외부 서비스는 Azure Marketplace의 타사 소프트웨어 공급 업체에 의해 게시됩니다. 예를 들어 SendGrid는 Azure에서 구입할 수 있지만 Microsoft에서 게시하지 않은 외부 서비스입니다. 일부 Microsoft 제품은 Azure Marketplace를 통해서도 판매됩니다.

## <a name="how-external-services-are-billed"></a>외부 서비스에 요금이 청구되는 방식

- MCA(Microsoft 고객 계약) 또는 MPA(Microsoft 파트너 계약)가 있는 경우 타사 서비스에는 나머지 Azure 서비스가 청구됩니다. [청구 계정 유형을 확인](#check-billing-account-type)하여 MCA 또는 MPA에 액세스할 수 있는지 확인합니다.
- MCA 또는 MPA가 없는 경우 외부 서비스는 Azure 서비스와는 별도로 청구됩니다. 청구 기간마다 두 개의 청구서, 즉 Azure 서비스 청구서 및 Marketplace 구매 청구서가 발부됩니다.
- 각 외부 서비스에는 다른 청구 모델이 있습니다. 일부 서비스는 종량제 방식으로 청구되는 반면, 다른 서비스는 월별 고정 요금을 사용합니다.
- 외부 서비스에 대한 월별 무료 크레딧을 사용할 수 없습니다. [무료 크레딧](https://azure.microsoft.com/pricing/spending-limits/)을 포함하는 Azure 구독을 사용하는 경우 외부 서비스에 대한 요금에 적용될 수 없습니다. 새 외부 서비스 또는 리소스를 프로비전하면 다음 경고가 표시됩니다.

    ![Marketplace 구매 경고](./media/understand-azure-marketplace-charges/credit-warning.png)

<!-- ## View external service spending and history in the Azure portal
You can view a list of the external services that are on each subscription within the [Azure portal](https://portal.azure.com/):

1. Sign in to the [Azure portal](https://portal.azure.com/) as the account administrator.
2. In the Hub menu, select **Subscriptions**.

    ![Select Subscriptions in the Hub menu](./media/understand-azure-marketplace-charges/sub-button.png)
3. In the **Subscriptions** blade, select the subscription that you want to view, and then select **External services**.

    ![Select a subscription in the billing blade](./media/understand-azure-marketplace-charges/select-sub-external-services.png)
4. You should see each of your external service orders, the publisher name, service tier you bought, name you gave the resource, and the current order status. To see past bills, select an external service.

    ![Select an external service](./media/understand-azure-marketplace-charges/external-service-blade2.png)
5. From here, you can view past bill amounts including the tax breakdown.

    ![View external services billing history](./media/understand-azure-marketplace-charges/billing-overview-blade.png) -->

## <a name="external-spending-for-ea-customers"></a>EA 고객에 대한 외부 지출

EA 고객은 EA 포털에서 외부 서비스 지출을 살펴보고 다운로드할 수 있습니다. 시작 방법은 [EA 고객을 위한 Azure Marketplace](https://ea.azure.com/helpdocs/azureMarketplace) 를 참조하세요.

## <a name="view-and-download-invoices-for-external-services"></a>외부 서비스에 대한 청구서 보기 및 다운로드

MCA(Microsoft 고객 계약) 또는 MPA(Microsoft 파트너 계약)가 있는 경우 타사 서비스는 나머지 Azure 서비스를 포함하여 단일 청구서로 청구됩니다. [청구 계정 유형을 확인](#check-billing-account-type)하여 MCA 또는 MPA에 액세스할 수 있는지 확인합니다. 이 경우에는 [Azure Portal에서 청구서 보기 및 다운로드](download-azure-invoice.md)를 참조하여 타사 요금을 확인합니다.

MCA 또는 MPA가 없는 경우 타사 요금에 대해 별도의 청구서를 받습니다. 

Azure Marketplace 요금은 현지 통화로 표시됩니다.

Azure Marketplace 청구서는 다음 단계에 따라 Azure Portal에서 보고 다운로드할 수 있습니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. **Cost Management + 청구**를 검색합니다.
1. 왼쪽 메뉴에서 **청구서**를 선택합니다.
1. 구독 드롭다운에서 Marketplace 서비스와 연결된 구독을 선택합니다.
1. 청구서 목록에서 **유형** 열을 검토합니다. Marketplace 서비스 청구서인 경우 유형은 **Azure Marketplace 및 Reservations**입니다. 

    ![청구서 그리드의 Azure Marketplace 유형에 대한 스크린샷](./media/understand-azure-marketplace-charges/marketplace-type-twd.png)

1. Azure Marketplace 및 Reservations 청구서만 표시하도록 유형별로 필터링하려면 **유형** 필터를 선택합니다. 그런 다음, 드롭다운에서 **Azure Marketplace 및 Reservations**를 선택합니다.

    ![드롭다운에서 선택한 Azure Marketplace 및 예약을 보여 주는 선택한 유형 필터의 스크린샷](./media/understand-azure-marketplace-charges/type-filter.png)

1. 다운로드하려는 청구서의 오른쪽에서 다운로드 아이콘을 선택합니다.

    ![청구서에 대해 선택한 다운로드 아이콘을 보여 주는 스크린샷](./media/understand-azure-marketplace-charges/download-icon-marketplace.png)

1. **청구서** 아래에서 파란색 **다운로드** 단추를 선택합니다.

    ![컨텍스트 창에서 청구서에 대한 다운로드 단추를 보여 주는 스크린샷](./media/understand-azure-marketplace-charges/invoice-download-marketplace.png)

## <a name="pay-for-external-services-in-the-azure-portal"></a>Azure Portal에서 외부 서비스에 대한 결제

MCA(Microsoft 고객 계약) 또는 MPA(Microsoft 파트너 계약)가 있는 경우 타사 서비스에는 나머지 Azure 서비스가 청구됩니다. [청구 계정 유형을 확인](#check-billing-account-type)하여 MCA 또는 MPA에 액세스할 수 있는지 확인합니다. 이렇게 하는 경우 [Microsoft Azure 청구서 결제](pay-bill.md)의 단계에 따라 Azure Portal에서 전체 청구서를 결제할 수 있습니다.

MCA 또는 MPA가 없는 경우 다음 단계에 따라 Azure Portal에서 Marketplace 청구서를 결제할 수 있습니다.

1. 앞에서 설명한 [외부 서비스 청구서 보기 및 다운로드](#view-and-download-invoices-for-external-services) 섹션의 단계에 따라 Marketplace 청구서를 찾습니다.
1. 결제하려는 청구서에 대한 파란색 **지금 결제** 링크를 선택합니다.

    ![청구서 그리드에서 선택한 지금 결제 링크를 보여 주는 스크린샷](./media/understand-azure-marketplace-charges/pay-now-twd.png)

    >[!NOTE]
    > 청구서 유형이 **Azure Marketplace 및 Reservations**이고 청구서 결제 상태가 만기일이거나 이를 넘긴 경우에만 **지금 결제** 링크가 표시됩니다.

1. 새 페이지에서 파란색 **결제 방법 선택**을 클릭합니다.

    ![선택한 결제 방법 선택 링크를 보여 주는 스크린샷](./media/understand-azure-marketplace-charges/select-payment-method-pay-now-twd.png)

1. 결제 방법을 선택한 후 페이지의 왼쪽 아래에 있는 파란색 **지금 결제** 단추를 클릭합니다.
    ![선택한 지금 결제 단추를 보여 주는 스크린샷](./media/understand-azure-marketplace-charges/pay-now-button-twd.png)

## <a name="change-default-payment-for-external-services"></a>외부 서비스에 대한 기본 결제 변경

외부 서비스를 구매할 때 리소스에 대한 Azure 구독을 선택합니다. 선택한 Azure 구독의 결제 방법은 외부 서비스에 대한 지불 방법이 됩니다. 외부 서비스에 대한 결제 방법을 변경하려면 해당 외부 서비스에 연결된 [Azure 구독의 지불 방법을 변경](../manage/change-credit-card.md)해야 합니다. 다음 단계를 수행하여 외부 서비스 주문이 연결된 구독을 파악할 수 있습니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 왼쪽 탐색 메뉴에서 **모든 리소스**를 클릭합니다.
     ![모든 리소스 메뉴 항목의 스크린샷](./media/understand-azure-marketplace-charges/all-resources.png)
1. 외부 서비스를 검색합니다.
1. **구독** 열에서 구독의 이름을 찾습니다.
    ![리소스에 대한 구독 이름의 스크린샷](./media/understand-azure-marketplace-charges/sub-selected.png)
1. 구독 이름을 클릭하고 [활성 결제 방법을 업데이트](../manage/change-credit-card.md)합니다.

## <a name="cancel-an-external-service-order"></a>외부 서비스 주문 취소

외부 서비스 주문을 취소하려는 경우 [Azure Portal](https://portal.azure.com)에서 리소스를 삭제합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 왼쪽 탐색 메뉴에서 **모든 리소스**를 클릭합니다.
    ![모든 리소스 메뉴 항목의 스크린샷](./media/understand-azure-marketplace-charges/all-resources.png)
1. 외부 서비스를 검색합니다.
1. 삭제하려는 리소스 옆에 있는 확인란을 선택합니다.
1. 명령 모음에서 **삭제**를 선택합니다.
    ![삭제 단추의 스크린샷](./media/understand-azure-marketplace-charges/delete-button.png)
1. 확인 블레이드에서 *‘예’* 를 입력합니다.
    ![리소스 삭제](./media/understand-azure-marketplace-charges/delete-resource.PNG)
1. **삭제**를 클릭합니다.

## <a name="check-billing-account-type"></a>청구 계정 유형 확인
[!INCLUDE [billing-check-account-type](../../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>도움 필요 시 문의하세요.

질문이 있거나 도움이 필요한 경우 [지원 요청을 만드세요](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>다음 단계
- [비용 분석 시작](../costs/quick-acm-cost-analysis.md)