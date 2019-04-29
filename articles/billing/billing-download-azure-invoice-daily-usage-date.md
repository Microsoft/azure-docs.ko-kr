---
title: Azure 청구서 및 일간 사용 현황 데이터 다운로드 | Microsoft Docs
description: Azure 청구서 및 일간 사용 현황 데이터를 다운로드하는 방법을 설명합니다.
keywords: 청구서, 청구서 다운로드, azure 청구서, azure 사용 현황
services: billing
documentationcenter: ''
author: genlin
manager: adpick
editor: ''
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: banders
ms.openlocfilehash: b78fb7d697f8a72b3c2f99c4509ea6ac5c5e5566
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60616454"
---
# <a name="download-or-view-your-azure-billing-invoice-and-daily-usage-data"></a>Azure 청구서 및 일간 사용 현황 데이터 다운로드 또는 보기

대부분의 구독에 대한 청구서는 [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)에서 다운로드하거나 메일로 전송 받을 수 있습니다. 기업계약이 체결된 Azure 고객(EA 고객)의 경우 조직의 청구서를 다운로드할 수 없습니다. 등록에 대한 청구서를 받도록 설정된 사람에게 청구서가 전송됩니다.

EA 고객 알고 싶거나를 [Microsoft 고객 계약](#check-access-to-a-microsoft-customer-agreement)에서 사용량을 다운로드할 수 있습니다 합니다 [Azure portal](https://portal.azure.com/)합니다. 다른 구독으로 이동 합니다 [Azure 계정 센터](https://account.azure.com/Subscriptions) 사용량 다운로드 합니다.

특정 역할에 대해서만 청구 계정 관리자 또는 엔터프라이즈 관리자와 같은 청구서 및 사용량 정보 수 있는 권한이 있습니다. 청구 정보에 액세스하는 방법에 대한 자세한 내용은 [역할을 사용하여 Azure 청구에 대한 액세스 관리](billing-manage-access.md)를 참조하세요.

Microsoft 고객 계약에 있는 경우 청구 프로필 소유자, 참가자, 판독기 이거나 있어야 manager 청구 및 사용량 정보를 보려면 청구서를 보냅니다. 자세한 청구 역할에 대 한 Microsoft 고객 계약에 대 한 참조 [청구 프로필 역할 및 작업](billing-understand-mca-roles.md#billing-profile-roles-and-tasks)합니다.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="download-your-azure-invoices-pdf"></a>Azure 청구서 (.pdf) 다운로드

대부분의 구독에 대 한 Azure portal에서 청구서를 다운로드할 수 있습니다. Microsoft 고객 계약에 있는 경우 다운로드를 참조 송장 청구 프로필에 대 한 합니다.

### <a name="download-invoices-for-an-individual-subscription"></a>개별 구독에 대 한 송장을 다운로드합니다

1. 구독을 선택 합니다 [구독 페이지](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) 으로 Azure portal의 [청구서 액세스 권한이 있는 사용자](billing-manage-access.md)합니다.

2. **청구서**를 선택합니다.

    ![청구 및 사용 현황 옵션을 보여 주는 스크린샷](./media/billing-download-azure-invoice-daily-usage-date/billingandusage.png)

3. **청구서 다운로드**를 클릭하여 PDF 청구서 사본을 확인합니다. **사용할 수 없음**이 표시될 경우 [왜 마지막 청구 기간에 대한 청구서가 보이지 않습니까?](#noinvoice)를 참조하세요.

    ![청구 기간, 다운로드 옵션 및 각 청구 기간별 총 요금을 보여 주는 스크린샷](./media/billing-download-azure-invoice-daily-usage-date/billing4.png)

4. 청구 기간을 클릭하여 일간 사용 현황을 볼 수도 있습니다.

청구서에 대한 자세한 내용은 [Microsoft Azure 청구서 이해](billing-understand-your-bill.md)를 참조하세요. 비용 관리에 대한 도움말은 [Azure 청구 및 비용 관리를 사용하여 예상치 못한 비용 방지](billing-getting-started.md)를 참조하세요.

### <a name="download-invoices-for-a-microsoft-customer-agreement"></a>Microsoft 고객 계약에 대 한 송장을 다운로드합니다

각각에 대해 생성 된 송장 [청구 프로필](billing-mca-overview.md#understand-billing-profiles) Microsoft 고객 계약에 있습니다. 청구 프로필 소유자, 참가자, 판독기 이거나 Azure portal에서 청구서를 다운로드 하려면 관리자는 청구서 있어야 합니다.

1. **Cost Management + 청구**에서 검색합니다.
2. 청구 프로필을 선택 합니다.
3. **청구서**를 선택합니다.
4. 송장 표의 다운로드할 청구서의 행을 찾습니다.
5. 줄임표를 클릭 (`...`) 행의 끝입니다.
6. 다운로드 상황에 맞는 메뉴에서 선택 **청구서**합니다.

마지막 청구 기간에 대 한 송장이 보이지 않으면 참조 **자세한 내용은**합니다. <!-- Fix this -->
### <a name="noinvoice"></a> 왜 마지막 청구 기간에 대한 청구서가 보이지 않습니까?

몇 가지 이유로 인해 청구서가 보이지 않을 수 있습니다.

- Azure를 구독한 지 30일 이내입니다.

- 청구서가 아직 생성되지 않았습니다. 청구 기간이 끝날 때까지 기다립니다.

- 청구서를 볼 수 있는 권한이 없습니다. Microsoft 고객 계약에 있는 경우 청구 프로필 소유자, 참가자, 판독기 되거나 관리자 송장 해야 합니다. 다른 구독에 대 한 표시 되지 않는 이전 청구서의 계정 관리자 아닌 경우. 청구 정보에 액세스하는 방법에 대한 자세한 내용은 [역할을 사용하여 Azure 청구에 대한 액세스 관리](billing-manage-access.md)를 참조하세요.

- 무료 평가판 또는 월별 크레딧 금액을 초과 하지는 구독에 있는 경우 Microsoft 고객 계약 없다면 청구서를 가져오려면 없습니다.

## <a name="get-your-invoice-in-email-pdf"></a>전자 메일로 청구서 받기(.pdf)

Azure 청구서를 전자 메일로 받을 추가 수취인을 옵트인하고 구성할 수 있습니다. 지원 제안, 기업 계약, Azure in Open 등의 특정 구독에는 이 기능이 제공되지 않을 수도 있습니다. Microsoft 고객 계약에 있는 경우 전자 메일에 송장 요금 청구 프로필 가져오기를 참조 하세요.

### <a name="get-your-subscriptions-invoices-in-email"></a>구독의 송장을 전자 메일로 받기

1. [구독 페이지](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)에서 구독을 선택합니다. 소유하고 있는 각 구독에 대해 옵트인합니다. **청구서**를 클릭한 다음 **전자 메일로 청구서 받기**를 클릭합니다.

    ![옵트인 흐름을 보여주는 스크린샷](./media/billing-download-azure-invoice-daily-usage-date/InvoicesDeepLink.PNG)

2. **옵트인**을 클릭하고 조건에 동의합니다.

    ![옵트인 흐름 2단계를 보여 주는 스크린샷](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep2.PNG)

3. 규약에 동의하면 추가 수취인을 구성할 수 있습니다. 받는 사람이 제거된 경우 전자 메일 주소는 더 이상 저장되지 않습니다. 생각이 바뀌었다면 제거한 받는 사람을 다시 추가해야 합니다.

    ![옵트인 흐름 3단계를 보여 주는 스크린샷](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep3.PNG)

이 단계를 따른 후 전자 메일을 받지 못하면 [프로필의 통신 기본 설정](https://account.windowsazure.com/profile)에서 전자 메일 주소가 올바른지 확인합니다.

### <a name="opt-out-of-getting-your-subscriptions-invoices-in-email"></a>전자 메일 구독 청구서 가져오기 옵트아웃

클릭 하 고 위의 단계를 수행 하 여 전자 메일을 통해 청구서를 가져오는 옵트아웃할 수 있습니다 **전자 메일로 보낸된 송장 옵트아웃**합니다. 그러면 청구서를 메일로 받기 위해 설정된 메일 주소가 모두 제거됩니다. 에 다시 가입 하면 받는 사람을 재구성할 수 있습니다.

 ![옵트아웃 흐름을 보여 주는 스크린샷](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep4.PNG)

### <a name="get-your-microsoft-customer-agreement-invoices-in-email"></a>Microsoft 고객 계약 청구서 전자 메일로 받기

Microsoft 고객 계약에 있는 경우에 전자 메일로 청구서 받기에 선택할 수 있습니다. 모든 청구 프로필 소유자, 참가자, 판독기 및 송장 관리자 전자 메일을 통해 청구서를 받습니다. 판독기는 전자 메일 청구서 기본 설정을 업데이트할 수 없습니다.

1. **Cost Management + 청구**에서 검색합니다.
1. 청구 프로필을 선택 합니다.
1. **설정** 아래에서 **속성**을 선택합니다.
1. 아래 **전자 메일 청구서**를 선택 **업데이트 전자 메일 청구서 기본**입니다.
1. 선택 **옵트인**합니다.
1. **업데이트**를 클릭합니다.

### <a name="opt-out-of-getting-your-billing-profile-invoices-in-email"></a>전자 메일의 청구 프로필 청구서 가져오기 옵트아웃

클릭 하 고 위의 단계를 수행 하 여 전자 메일을 통해 청구서를 가져오는 옵트아웃할 수 있습니다 **옵트아웃**합니다. 모든 소유자, 참가자, 판독기 및 송장 관리자 전자 메일, 너무 청구서를 가져오는에서 제외 됩니다. 판독기의 경우 전자 메일 청구서 기본 설정을 변경할 수 없습니다.

## <a name="download-usage"></a>사용량 다운로드

 대부분의 구독에 대한 일일 사용량 파일은 [Azure 계정 센터](https://account.azure.com/Subscriptions)에서 찾을 수 있습니다. EA 고객 인 Microsoft 고객 계약을가지고 있는 경우에 사용량을 다운로드할 수 있습니다 합니다 [Azure portal](https://portal.azure.com/)합니다. <!-- TO DO: update PayG experience to Ibiza once it ships-->

### <a name="download-usage-from-the-account-center-csv"></a>계정 센터에서 사용 현황 다운로드(.csv)

1. [Azure 계정 센터](https://account.windowsazure.com/subscriptions)에 계정 관리자로 로그인합니다.

2. 원하는 송장 및 사용 정보에 대한 구독을 선택합니다.

3. **청구 내역**을 선택합니다.

    ![청구 내역 옵션을 보여 주는 스크린샷](./media/billing-download-azure-invoice-daily-usage-date/Billinghisotry.png)

4. 이전 6개의 청구 기간 및 현재 미결제한 기간의 명세서를 볼 수 있습니다.

    ![청구 기간, 청구서 및 일간 사용 현황 다운로드 옵션, 각 청구 기간에 대한 총 요금을 보여 주는 스크린샷](./media/billing-download-azure-invoice-daily-usage-date/billingSum.png)

5. **현재 명세서 보기** 를 선택하여 예상 요금을 볼 수 있습니다. 이 정보는 매일 업데이트만 될 뿐 모든 사용량을 포함하지 않습니다. 월별 청구서는 이 예상과 다를 수 있습니다.

    ![현재 명세서 보기 옵션을 보여 주는 스크린샷](./media/billing-download-azure-invoice-daily-usage-date/billingSum2.png)

    ![현재 예상 요금을 보여 주는 스크린샷](./media/billing-download-azure-invoice-daily-usage-date/billingSum3.png)

6. **사용량 다운로드**를 선택하여 일간 사용 데이터를 CSV 파일로 다운로드 할 수 있습니다. 사용할 수 있는 두 가지 버전이 표시되면 버전 2를 다운로드합니다.

    ![사용 현황 다운로드 옵션을 보여 주는 스크린샷](./media/billing-download-azure-invoice-daily-usage-date/DLusage.png)

계정 관리자만 Azure 계정 센터에 액세스할 수 있습니다. 소유자 등의 기타 청구 관리자는 [청구 API](billing-usage-rate-card-overview.md)를 사용하여 사용량 정보를 볼 수 있습니다.

일간 사용 현황에 대한 자세한 내용은 [Microsoft Azure 청구서 이해](billing-understand-your-bill.md)를 참조하세요. 비용 관리에 대한 도움말은 [Azure 청구 및 비용 관리를 사용하여 예상치 못한 비용 방지](billing-getting-started.md)를 참조하세요.

### <a name="download-usage-for-ea-customers"></a>EA 고객의 사용량 다운로드

엔터프라이즈 관리자, 계정 소유자를 보고 하려면 EA 고객의 사용 현황 데이터를 다운로드 해야 하거나 뷰를 사용 하 여 부서 관리자 요금 정책을 사용 하도록 설정 합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 검색할 *비용 관리 + 청구*합니다.

    ![Azure Portal 검색을 보여 주는 스크린샷](./media/billing-download-azure-invoice-daily-usage-date/portal-cm-billing-search.png)

1. **사용량 + 요금**을 선택합니다.
1. 다운로드하려는 월의 **다운로드**를 선택합니다.

### <a name="download-usage-for-your-microsoft-customer-agreement"></a>Microsoft 고객 계약에 대 한 사용량 다운로드

청구 프로필 소유자, 참가자, 판독기 하거나 manager 송장 경우에을 보고 하려면 청구 프로필에 대 한 사용 현황 데이터 다운로드 있습니다.

#### <a name="download-usage-for-billed-charges"></a>청구 요금에 대 한 사용량 다운로드

1. **Cost Management + 청구**에서 검색합니다.
2. 청구 프로필을 선택 합니다.
3. **청구서**를 선택합니다.
4. 송장 표에서 해당 다운로드를 사용 하 여 청구서의 행을 찾습니다.
5. 줄임표를 클릭 (`...`) 행의 끝입니다.
6. 다운로드 상황에 맞는 메뉴에서 선택 **Azure 사용량 및 요금이**합니다.

#### <a name="download-usage-for-open-charges"></a>열기 요금에 대 한 사용량 다운로드

다운로드할 수도 있습니다 누계 달 사용량은 현재 청구 기간 동안 요금이 청구 되지가 의미 합니다.

1. **Cost Management + 청구**에서 검색합니다.
2. 청구 프로필을 선택 합니다.
3. 에 **개요** 블레이드에서 클릭 **다운로드 Azure 사용량 및 요금이**합니다.

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Microsoft 고객 계약에 대 한 액세스를 확인 합니다.
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>도움 필요 시 문의처

문의 사항이 있거나 도움이 필요한 경우 [지원 요청을 만드는](https://go.microsoft.com/fwlink/?linkid=2083458)합니다.

## <a name="next-steps"></a>다음 단계

청구서 및 요금에 대 한 자세한 내용은 다음을 참조 하세요.

- [Microsoft Azure 요금 청구 방식 이해](billing-understand-your-bill.md)
- [Azure 청구서에서 용어 이해](billing-understand-your-invoice.md)
- [Microsoft Azure 세부 사용량의 용어 이해](billing-understand-your-usage.md)
- [조직의 Azure 가격을 확인합니다](billing-ea-pricing.md)

Microsoft 고객 계약에 있는 경우 참조 하세요.

- [청구 프로필에 대 한 청구서 요금 이해](billing-mca-understand-your-bill.md)
- [청구 프로필에 대 한 청구서에서 용어 이해](billing-mca-understand-your-invoice.md)
- [청구 프로필에 대 한 Azure 사용량 및 요금 파일 이해](billing-mca-understand-your-usage.md)
- [보기 및 요금 청구 프로필에 대 한 세금 문서를 다운로드 합니다.](billing-mca-download-tax-document.md)
- [조직의 Azure 가격을 확인합니다](billing-ea-pricing.md)
