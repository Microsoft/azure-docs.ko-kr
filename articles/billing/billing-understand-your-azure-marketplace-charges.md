---
title: Azure 외부 서비스 요금의 이해 | Microsoft Docs
description: 이전에는 Marketplace로 알려진 외부 서비스의 요금 청구, Azure의 요금에 대해 알아봅니다.
services: ''
documentationcenter: ''
author: jureid
manager: jureid
editor: ''
tags: billing
ms.assetid: 5e0e2a3c-d111-4054-8508-0c111c1b749b
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: banders
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 90753c6046425b60fda04fa99b2952e706d9c0e5
ms.sourcegitcommit: 5cb0b6645bd5dff9c1a4324793df3fdd776225e4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/21/2019
ms.locfileid: "67311921"
---
# <a name="understand-your-azure-external-services-charges"></a>Azure 외부 서비스 요금의 이해
외부 서비스는 Azure marketplace에서 타사 소프트웨어 공급 업체에 의해 게시 됩니다. 예를 들어, SendGrid는 Azure에서 구입할 수 있지만 Microsoft에서 게시 되지 않은 외부 서비스입니다. 일부 Microsoft 제품은 Azure marketplace를 통해 너무 판매 됩니다.

## <a name="how-external-services-are-billed"></a>외부 서비스에 요금이 청구되는 방식

- 있는 경우는 [Microsoft 고객 계약](#check-access-to-a-microsoft-customer-agreement), 타사 서비스는 Azure 서비스의 rest를 사용 하 여 요금이 청구 됩니다.
- Microsoft 고객 계약에 없는 경우에 Azure 서비스에서 외부 서비스 별도로 청구 됩니다.
- 각 외부 서비스에는 다른 청구 모델이 있습니다. 월별 요금 다른 해결 하는 동안 일부 서비스는 종 량 제 방식으로 요금이 청구 됩니다.
- 외부 서비스에 대한 월별 무료 크레딧을 사용할 수 없습니다. 포함 된 Azure 구독을 사용 하는 경우 [무료 크레딧](https://azure.microsoft.com/pricing/spending-limits/), 외부 서비스의 요금에 적용할 수 없습니다. 새 외부 서비스 또는 리소스를 프로비전하면 다음 경고가 표시됩니다.

    ![Marketplace 구매 경고](./media/billing-understand-your-azure-marketplace-charges/credit-warning.png)

<!-- ## View external service spending and history in the Azure portal
You can view a list of the external services that are on each subscription within the [Azure portal](https://portal.azure.com/): 

1. Sign in to the [Azure portal](https://portal.azure.com/) as the account administrator.
2. In the Hub menu, select **Subscriptions**.
   
    ![Select Subscriptions in the Hub menu](./media/billing-understand-your-azure-marketplace-charges/sub-button.png) 
3. In the **Subscriptions** blade, select the subscription that you want to view, and then select **External services**.
   
    ![Select a subscription in the billing blade](./media/billing-understand-your-azure-marketplace-charges/select-sub-external-services.png)
4. You should see each of your external service orders, the publisher name, service tier you bought, name you gave the resource, and the current order status. To see past bills, select an external service.
   
    ![Select an external service](./media/billing-understand-your-azure-marketplace-charges/external-service-blade2.png)
5. From here, you can view past bill amounts including the tax breakdown.
   
    ![View external services billing history](./media/billing-understand-your-azure-marketplace-charges/billing-overview-blade.png) -->

## <a name="view-and-download-azure-marketplace-invoices"></a>보기 및 Azure marketplace 청구서 다운로드

있는 경우는 [Microsoft 고객 계약](#check-access-to-a-microsoft-customer-agreement)는 동일한 Azure 요금이 청구서에 타사 요금은 부과 됩니다. 에 대해 알아봅니다 하는 방법 [살펴보고 Azure 청구서를 다운로드할](billing-download-azure-invoice.md) 제 요금의 보려면 Azure portal에서 합니다.

Microsoft 고객 계약에 없는 경우 별도 청구서에 대 한 타사 요금입니다. 확인 하 고이 단계를 수행 하 여 Azure portal에서 Azure Marketplace 청구서를 다운로드할 수 있습니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. **Cost Management + 청구**에서 검색합니다.
1. 왼쪽된 메뉴에서 선택 **송장**합니다.
1. 클릭 합니다 **Azure Marketplace 및 예약** 탭 합니다.  ![Azure marketplace 및 예약 탭 그림](./media/billing-understand-your-azure-marketplace-charges/invoice-tabs.png)
1. 구독 드롭다운 목록에서의 청구서를 확인 하려는 외부 서비스를 포함 하는 구독을 선택 합니다.

## <a name="view-external-service-spending-for-enterprise-agreement-ea-customers"></a>EA(기업 계약) 고객의 외부 서비스 지출 보기

EA 고객은 EA 포털에서 외부 서비스 지출을 살펴보고 다운로드할 수 있습니다. 시작 방법은 [EA 고객을 위한 Azure Marketplace](https://ea.azure.com/helpdocs/azureMarketplace) 를 참조하세요.

## <a name="manage-payment-methods-for-external-service-orders"></a>외부 서비스 주문에 대한 지불 방법 관리

외부 서비스를 구입 하는 경우에 리소스에 대 한 Azure 구독을 선택 합니다. 선택한 Azure 구독의 지불 방법을 외부 서비스에 대 한 지불 방법이 됩니다. 외부 서비스에 대 한 지불 방법을 변경 하려면 [Azure 구독 지불 방법 변경](billing-how-to-change-credit-card.md) 해당 외부 서비스에 연결 합니다. 다음이 단계를 수행 하 여 외부 서비스 주문에 연결 되어 구독을 확인할 수 있습니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 클릭할 **모든 리소스** 왼쪽된 탐색 메뉴에서.
     ![모든 리소스 메뉴 항목의 스크린샷](./media/billing-understand-your-azure-marketplace-charges/all-resources.png)
1. 외부 서비스를 검색 합니다.
1. 구독의 이름을 찾습니다 합니다 **구독** 열입니다.
    ![구독 이름 리소스에 대 한 스크린샷](./media/billing-understand-your-azure-marketplace-charges/sub-selected.png)
1. 구독 이름을 클릭 하 고 [활성 결제 방법을 업데이트](billing-how-to-change-credit-card.md)합니다.
 
<!-- Update your payment methods for external service orders from the [Account Center](https://account.windowsazure.com/).

> [!NOTE]
> If you purchased your subscription with a Work or School account, [contact support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) to make changes to your payment method.

1. Sign in to the [Account Center](https://account.windowsazure.com/) and [navigate to the **marketplace** tab](https://account.windowsazure.com/Store)
   
    ![Select marketplace in the account center](./media/billing-understand-your-azure-marketplace-charges/select-marketplace.png)
2. Select the external service you want to manage
   
    ![Select the external service you want to manage](./media/billing-understand-your-azure-marketplace-charges/select-ext-service.png)
3. Click **Change payment method** on the right side of the page. This link brings you to a different portal to manage your payment method.
   
    ![Order summary](./media/billing-understand-your-azure-marketplace-charges/change-payment.PNG)
4. Click **Edit info** and follow instructions to update your payment information.
   
    ![Select edit info](./media/billing-understand-your-azure-marketplace-charges/edit-info.png) -->

## <a name="cancel-an-external-service-order"></a>외부 서비스 주문 취소
외부 서비스 주문을 취소하려는 경우 [Azure Portal](https://portal.azure.com)에서 리소스를 삭제합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 클릭할 **모든 리소스** 왼쪽된 탐색 메뉴에서.
    ![모든 리소스 메뉴 항목의 스크린샷](./media/billing-understand-your-azure-marketplace-charges/all-resources.png)
1. 외부 서비스를 검색 합니다.
1. 삭제 하려는 리소스 옆의 확인란을 선택 합니다.
1. 선택 **삭제** 명령 모음에서.
    ![삭제 단추 스크린샷](./media/billing-understand-your-azure-marketplace-charges/delete-button.png)
1. 형식 *'예'* 확인 블레이드에서 합니다.
    ![리소스 삭제](./media/billing-understand-your-azure-marketplace-charges/delete-resource.PNG)
1. **삭제**를 클릭합니다.



## <a name="check-access-to-a-microsoft-customer-agreement"></a>Microsoft 고객 계약에 대 한 액세스를 확인 합니다.
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>도움 필요 시 문의하세요.

문의 사항이 있거나 도움이 필요한 경우 [지원 요청을 만드는](https://go.microsoft.com/fwlink/?linkid=2083458)합니다.

