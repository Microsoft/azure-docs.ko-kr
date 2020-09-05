---
title: Azure 청구서 및 일간 사용 현황 데이터 다운로드
description: Azure 청구서 및 일간 사용량 현황 데이터를 다운로드하는 방법을 설명합니다.
keywords: 청구서, 청구서 다운로드, azure 청구서, azure 사용 현황
author: genlin
ms.reviewer: dcscontentpm
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: a3930698bb2999ebaad4d78e0b545b83cc968ef2
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88690037"
---
# <a name="download-or-view-your-azure-billing-invoice-and-daily-usage-data"></a>Azure 청구서 및 일간 사용량 현황 데이터 다운로드 또는 보기

대부분의 구독에 대한 청구서는 [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)에서 다운로드하거나 메일로 전송 받을 수 있습니다. 기업계약이 체결된 Azure 고객(EA 고객)의 경우 조직의 청구서를 다운로드할 수 없습니다. 등록에 대한 청구서를 받도록 설정된 사람에게 청구서가 전송됩니다.

EA 고객이거나 [Microsoft 고객 계약](#check-access-to-a-microsoft-customer-agreement)이 있으면 [Azure Portal](https://portal.azure.com/)에서 사용량을 다운로드할 수 있습니다.

계정 관리자 또는 엔터프라이즈 관리자와 같은 특정 역할만 청구서 및 사용량 정보를 가져올 수 있는 권한이 있습니다. 청구 정보에 액세스하는 방법에 대한 자세한 내용은 [역할을 사용하여 Azure 청구에 대한 액세스 관리](manage-billing-access.md)를 참조하세요.

Microsoft 고객 계약이 있는 경우 청구 프로필 소유자, 기여자, 읽기 권한자 또는 청구서 관리자여야 청구 및 사용량 정보를 확인할 수 있습니다. Microsoft 고객 계약의 청구 역할에 대해 자세히 알아보려면 [청구 프로필 역할 및 작업](understand-mca-roles.md#billing-profile-roles-and-tasks)을 참조하세요.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="download-your-azure-invoices-pdf"></a>Azure 청구서(.pdf) 다운로드

대부분의 구독은 Azure Portal에서 청구서를 다운로드할 수 있습니다. Microsoft 고객 계약이 있으면 청구 프로필에 대한 청구서 다운로드를 참조하세요.

### <a name="download-invoices-for-an-individual-subscription"></a>개별 구독에 대한 청구서 다운로드

1. [청구서 액세스 권한이 있는 사용자](manage-billing-access.md)로 Azure Portal의 [구독 페이지](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)에서 구독을 선택합니다.

2. **청구서**를 선택합니다.

    ![청구 및 사용 현황 옵션을 보여 주는 스크린샷](./media/download-azure-invoice-daily-usage-date/billingandusage.png)

3. 다운로드 단추를 클릭하여 PDF 청구서의 복사본을 다운로드한 다음, **청구서 다운로드**를 선택합니다. **사용할 수 없음**이 표시될 경우 [왜 마지막 청구 기간에 대한 청구서가 보이지 않습니까?](#noinvoice)를 참조하세요.

    ![청구 기간, 다운로드 옵션 및 각 청구 기간별 총 요금을 보여 주는 스크린샷](./media/download-azure-invoice-daily-usage-date/downloadinvoice.png)

4. 또한 **csv 다운로드**를 클릭하여 일일 사용량 및 예상 요금을 다운로드할 수 있습니다.

    ![청구서 및 사용량 다운로드 옵션을 보여주는 스크린샷](./media/download-azure-invoice-daily-usage-date/usageandinvoice.png)

청구서에 대한 자세한 내용은 [Microsoft Azure 청구서 이해](../understand/review-individual-bill.md)를 참조하세요. 비용 관리에 대한 도움말은 [Azure 청구 및 비용 관리를 사용하여 예상치 못한 비용 방지](getting-started.md)를 참조하세요.

### <a name="download-invoices-for-a-microsoft-customer-agreement"></a>Microsoft 고객 계약 청구서 다운로드

Microsoft 고객 계약의 [청구 프로필](../understand/mca-overview.md#billing-profiles)마다 청구서가 생성됩니다. 청구 프로필 소유자, 기여자, 독자 또는 청구서 관리자여야 Azure Portal에서 청구서를 다운로드할 수 있습니다.

1. **Cost Management + 청구**를 검색합니다.
2. 청구 프로필을 선택합니다.
3. **청구서**를 선택합니다.
4. 청구서 그리드에서 다운로드하려는 청구서의 행을 찾습니다.
5. 행의 끝에 있는 다운로드 단추를 클릭합니다.
6. 다운로드 상황에 맞는 메뉴에서 **청구서**를 선택합니다.

마지막 청구 기간에 대한 청구서가 보이지 않으면 **추가 정보**를 참조하세요. <!-- Fix this -->
### <a name="why-dont-i-see-an-invoice-for-the-last-billing-period"></a><a name="noinvoice"></a> 왜 마지막 청구 기간에 대한 청구서가 보이지 않습니까?

몇 가지 이유로 인해 청구서가 보이지 않을 수 있습니다.

- Azure를 구독한 지 30일 이내입니다.

- 청구서가 아직 생성되지 않았습니다. 청구 기간이 끝날 때까지 기다립니다.

- 청구서를 볼 수 있는 권한이 없습니다. Microsoft 고객 계약이 있는 경우 청구 프로필 소유자, 기여자, 독자 또는 청구서 관리자여야 합니다. 다른 구독의 경우 계정 관리자가 아니면 이전 청구서가 보이지 않을 수 있습니다. 청구 정보에 액세스하는 방법에 대한 자세한 내용은 [역할을 사용하여 Azure 청구에 대한 액세스 관리](manage-billing-access.md)를 참조하세요.

- 구독에 초과하지 않은 월별 크레딧 금액 또는 평가판이 있는 경우, Microsoft 고객 계약이 있어야만 청구서를 받을 수 있습니다.

## <a name="get-your-invoice-in-email-pdf"></a>전자 메일로 청구서 받기(.pdf)

Azure 청구서를 전자 메일로 받을 추가 수취인을 옵트인하고 구성할 수 있습니다. 지원 제안, 기업 계약, Azure in Open 등의 특정 구독에는 이 기능이 제공되지 않을 수도 있습니다. Microsoft 고객 계약이 있는 경우 이메일로 청구 프로필 청구서 받기를 참조하세요.

### <a name="get-your-subscriptions-invoices-in-email"></a>이메일로 구독의 청구서 받기

1. [구독 페이지](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)에서 구독을 선택합니다. 소유하고 있는 각 구독에 대해 옵트인합니다. **청구서**를 클릭한 다음 **전자 메일로 청구서 받기**를 클릭합니다.

    ![옵트인 흐름을 보여주는 스크린샷](./media/download-azure-invoice-daily-usage-date/invoicesdeeplink01.png)

2. **옵트인**을 클릭하고 조건에 동의합니다.

    ![옵트인 흐름 2단계를 보여 주는 스크린샷](./media/download-azure-invoice-daily-usage-date/invoicearticlestep02.png)

3. 규약에 동의하면 추가 수취인을 구성할 수 있습니다. 받는 사람이 제거된 경우 전자 메일 주소는 더 이상 저장되지 않습니다. 생각이 바뀌었다면 제거한 받는 사람을 다시 추가해야 합니다.

    ![옵트인 흐름 3단계를 보여 주는 스크린샷](./media/download-azure-invoice-daily-usage-date/invoicearticlestep03.png)

이 단계를 따른 후 전자 메일을 받지 못하면 [프로필의 통신 기본 설정](https://account.windowsazure.com/profile)에서 전자 메일 주소가 올바른지 확인합니다.

### <a name="opt-out-of-getting-your-subscriptions-invoices-in-email"></a>이메일로 구독의 청구서 받기 옵트아웃

위의 단계를 수행하고 **이메일로 보낸 청구서 옵트아웃**을 클릭하면 이메일로 청구서 받기에서 옵트아웃할 수 있습니다. 그러면 청구서를 메일로 받기 위해 설정된 메일 주소가 모두 제거됩니다. 다시 옵트인하는 경우 받는 사람을 다시 구성할 수 있습니다.

 ![옵트아웃 흐름을 보여 주는 스크린샷](./media/download-azure-invoice-daily-usage-date/invoicearticlestep04.png)

### <a name="get-your-microsoft-customer-agreement-invoices-in-email"></a>이메일로 Microsoft 고객 계약 청구서 받기

Microsoft 고객 계약이 있으면 이메일로 청구서 받기로 옵트인할 수 있습니다. 모든 청구 프로필 소유자, 기여자, 독자 및 청구서 관리자는 이메일로 청구서를 받습니다. 읽기 권한자는 이메일 청구서 기본 설정을 업데이트할 수 없습니다.

1. **Cost Management + 청구**를 검색합니다.
1. 청구 프로필을 선택합니다.
1. **설정** 아래에서 **속성**을 선택합니다.
1. **이메일 청구서**에서 **이메일 청구서 기본 설정 업데이트**를 선택합니다.
1. **옵트인**을 선택합니다.
1. **업데이트**를 클릭합니다.

### <a name="opt-out-of-getting-your-billing-profile-invoices-in-email"></a>이메일로 청구 프로필 청구서 받기에서 옵트아웃

위의 단계를 수행하고 **옵트아웃**을 클릭하면 이메일로 청구서 받기에서 옵트아웃할 수 있습니다. 그러면 소유자, 기여자, 읽기 권한자 및 청구서 관리자가 이메일로 청구서 받기에서 모두 옵트아웃됩니다. 읽기 권한자는 이메일 청구서 기본 설정을 변경할 수 없습니다.

## <a name="download-usage-in-azure-portal"></a>Azure Portal에서 사용량 다운로드

 대부분의 구독은 다음 단계에 따라 일일 사용량을 확인할 수 있습니다.

1. [청구서 액세스 권한이 있는 사용자](manage-billing-access.md)로 Azure Portal의 [구독 페이지](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)에서 구독을 선택합니다.

2. **청구서**를 선택합니다.

    ![청구 및 사용 현황 옵션을 보여 주는 스크린샷](./media/download-azure-invoice-daily-usage-date/billingandusage.png)

3. 확인하려는 청구서 기간의 다운로드 단추를 클릭합니다.

4. **csv 다운로드**를 클릭하여 일일 사용량 및 예상 요금을 다운로드합니다.  csv 파일을 준비하는 데 몇 분 정도 걸릴 수 있습니다.

### <a name="download-usage-for-ea-customers"></a>EA 고객의 사용량 다운로드

EA 고객이 사용량 데이터를 살펴보고 다운로드하려면 요금 보기 정책을 사용하도록 설정된 엔터프라이즈 관리자, 계정 소유자 또는 부서 관리자여야 합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. *Cost Management + 청구*를 검색합니다.

    ![Azure Portal 검색을 보여 주는 스크린샷](./media/download-azure-invoice-daily-usage-date/portal-cm-billing-search.png)

1. **사용량 + 요금**을 선택합니다.
1. 다운로드하려는 월의 **다운로드**를 선택합니다.

### <a name="download-usage-for-your-microsoft-customer-agreement"></a>Microsoft 고객 계약에 대한 사용량 다운로드

청구 프로필에 대한 사용량 데이터를 살펴보고 다운로드하려면 청구 프로필 소유자, 기여자, 읽기 권한자 또는 청구서 관리자여야 합니다.

#### <a name="download-usage-for-billed-charges"></a>청구 요금에 대한 사용량 다운로드

1. **Cost Management + 청구**를 검색합니다.
2. 청구 프로필을 선택합니다.
3. **청구서**를 선택합니다.
4. 청구서 그리드에서 다운로드하려는 사용량에 해당하는 청구서의 행을 찾습니다.
5. 행 끝에 있는 줄임표(`...`)를 클릭합니다.
6. 다운로드 상황에 맞는 메뉴에서 **Azure 사용량 및 요금**을 선택합니다.

#### <a name="download-usage-for-open-charges"></a>미결 요금에 대한 사용량 다운로드

현재 청구 기간에 대한 월간 누계(즉, 요금이 아직 청구되지 않은) 사용량도 다운로드할 수 있습니다.

1. **Cost Management + 청구**를 검색합니다.
2. 청구 프로필을 선택합니다.
3. **개요** 블레이드에서 **Azure 사용량 및 요금 다운로드**를 클릭합니다.

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Microsoft 고객 계약에 대한 액세스 확인
[!INCLUDE [billing-check-mca](../../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>도움 필요 시 문의처

질문이 있거나 도움이 필요한 경우 [지원 요청을 만드세요](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>다음 단계

청구서 및 요금에 대한 자세한 내용은 다음을 참조하세요.

- [Microsoft Azure 요금 청구 방식 이해](../understand/review-individual-bill.md)
- [Azure 청구서의 용어 이해](../understand/understand-invoice.md)
- [Microsoft Azure 세부 사용량의 용어 이해](../understand/understand-usage.md)
- [조직의 Azure 가격 책정 보기](ea-pricing.md)

Microsoft 고객 계약이 있는 경우 다음을 참조하세요.

- [청구 프로필 청구서의 요금 이해](../understand/review-customer-agreement-bill.md)
- [청구 프로필 청구서의 용어 이해](../understand/mca-understand-your-invoice.md)
- [청구 프로필에 대한 Azure 사용량 및 요금 파일 이해](../understand/mca-understand-your-usage.md)
- [청구 프로필에 대한 세금 문서 보기 및 다운로드](../understand/mca-download-tax-document.md)
- [조직의 Azure 가격 책정 보기](ea-pricing.md)
