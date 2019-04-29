---
title: 보기 및 다운로드 Azure 사용량 및 요금이 | Microsoft Docs
description: 보거나 Azure 일별 사용 현황 및 요금을 다운로드 하는 방법을 설명 합니다.
keywords: 사용 대금 청구, 요금, 사용, 사용 현황 보기 azure 청구서, azure 사용량 다운로드
services: billing
documentationcenter: ''
author: genlin
manager: jureid
editor: ''
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: banders
ms.openlocfilehash: 520d3f6a45b44ba2023dee34642f796689f48221
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60919004"
---
# <a name="view-and-download-your-azure-usage-and-charges"></a>Azure 사용량 및 요금에 보기 및 다운로드

EA 고객 알고 싶거나를 [Microsoft 고객 계약](#check-your-access-to-a-microsoft-customer-agreement), Azure 사용량 및 요금이에서 다운로드할 수 있습니다 합니다 [Azure portal](https://portal.azure.com/)합니다. 다른 구독으로 이동 합니다 [Azure 계정 센터](https://account.azure.com/Subscriptions) 사용량 다운로드 합니다.

특정 역할에 대해서만 계정 관리자 또는 엔터프라이즈 관리자와 같은 Azure 사용량 정보를 가져올 수 있는 권한이 있습니다. 청구 정보에 액세스하는 방법에 대한 자세한 내용은 [역할을 사용하여 Azure 청구에 대한 액세스 관리](billing-manage-access.md)를 참조하세요.

있는 경우는 [Microsoft 고객 계약](#check-your-access-to-a-microsoft-customer-agreement), 청구 프로필 소유자, 참가자, 판독기 이거나 송장 manager Azure 사용량 및 요금을 볼 수 있어야 합니다. 자세한 청구 역할에 대 한 Microsoft 고객 계약에 대 한 참조 [청구 프로필 역할 및 작업](billing-understand-mca-roles.md#billing-profile-roles-and-tasks)합니다.

## <a name="download-usage-from-the-account-center-csv"></a>계정 센터에서 사용 현황 다운로드(.csv)

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

## <a name="download-usage-for-ea-customers"></a>EA 고객의 사용량 다운로드

엔터프라이즈 관리자, 계정 소유자를 보고 하려면 EA 고객의 사용 현황 데이터를 다운로드 해야 하거나 뷰를 사용 하 여 부서 관리자 요금 정책을 사용 하도록 설정 합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 검색할 *비용 관리 + 청구*합니다.

    ![Azure Portal 검색을 보여 주는 스크린샷](./media/billing-download-azure-invoice-daily-usage-date/portal-cm-billing-search.png)

1. **사용량 + 요금**을 선택합니다.
1. 다운로드하려는 월의 **다운로드**를 선택합니다.

## <a name="download-usage-for-your-microsoft-customer-agreement"></a>Microsoft 고객 계약에 대 한 사용량 다운로드

Microsoft 고객 계약에 있는 경우 청구 프로필에 대 한 Azure 사용량 및 요금을 다운로드할 수 있습니다. 청구 프로필 소유자, 참가자, 판독기 이거나 관리자를 다운로드 하 고 Azure 사용 요금 CSV 송장 있어야 합니다.

### <a name="download-usage-for-billed-charges"></a>청구 요금에 대 한 사용량 다운로드

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. **Cost Management + 청구**에서 검색합니다.
3. 청구 프로필을 선택 합니다. 액세스를 따라 대금 청구 계정을 먼저 선택 해야 할 수 있습니다.
4. **청구서**를 선택합니다.
5. 송장 표에서 해당 다운로드를 사용 하 여 청구서의 행을 찾습니다.
6. 줄임표를 클릭 (`...`) 행의 끝입니다.

    ![행의 끝에 줄임표 (...)를 보여 주는 스크린샷](./media/billing-download-azure-invoice/billingprofile-invoicegrid.png)

7. 다운로드 상황에 맞는 메뉴에서 선택 **Azure 사용량 및 요금이**합니다.

     ![Azure 사용량 및 선택한 요금을 보여 주는 스크린샷](./media/billing-download-azure-usage/contextmenu-usage.png)

### <a name="download-usage-for-pending-charges"></a>에 대 한 사용량 요금 보류 중인 다운로드

또한 현재 청구 기간에 대 한 누계 월 사용량을 다운로드할 수 있습니다. 아직 청구 하지는 이러한 사용량 요금입니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. **Cost Management + 청구**에서 검색합니다.
3. 청구 프로필을 선택 합니다. 액세스를 따라 대금 청구 계정을 먼저 선택 해야 할 수 있습니다.
4. 에 **개요** 블레이드에서 월-일 요금은 아래 다운로드 링크를 찾습니다.
5. 선택 **Azure 사용량 및 요금이**합니다.

    ![다운로드 개요에서 보여 주는 스크린샷](./media/billing-download-azure-usage/open-usage.png)

## <a name="check-your-access-to-a-microsoft-customer-agreement"></a>Microsoft 고객 계약에 대 한 액세스를 확인 합니다.
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>도움 필요 시 문의처

문의 사항이 있거나 도움이 필요한 경우 [지원 요청을 만드는](https://go.microsoft.com/fwlink/?linkid=2083458)합니다.

## <a name="next-steps"></a>다음 단계

청구서 및 사용량 요금에 대 한 자세한 내용은 다음을 참조 하세요.

- [Microsoft Azure 세부 사용량의 용어 이해](billing-understand-your-usage.md)
- [Microsoft Azure 요금 청구 방식 이해](billing-understand-your-bill.md)
- [보기 및 Microsoft Azure 청구서를 다운로드 합니다.](billing-download-azure-invoice.md)
- [보기 및 조직의 Azure 가격 책정 다운로드](billing-ea-pricing.md)

Microsoft 고객 계약에 있는 경우 참조 하세요.

- [Microsoft 고객 계약 Azure에서 용어 이해의 자세한 사용 현황](billing-mca-understand-your-usage.md)
- [Microsoft 고객 계약 청구서의 요금 이해](billing-mca-understand-your-bill.md)
- [보기 및 Microsoft Azure 청구서를 다운로드 합니다.](billing-download-azure-invoice.md)
- [보기 및 Microsoft 고객 계약에 대 한 세금 문서 다운로드](billing-mca-download-tax-document.md)
- [보기 및 조직의 Azure 가격 책정 다운로드](billing-ea-pricing.md)
