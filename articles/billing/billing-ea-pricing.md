---
title: 보기 및 조직의 Azure 가격 책정 다운로드 | Microsoft Docs
description: 보기 및 가격 책정을 다운로드 하거나 조직의 가격으로 비용을 예상 하는 방법에 알아봅니다.
services: ''
documentationcenter: ''
author: adpick
manager: jureid
editor: ''
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/22/2019
ms.author: banders
ms.custom: seodec18
ms.openlocfilehash: d4c64433089f14d845cea8be9adf0ef13675cfd1
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62127636"
---
# <a name="view-and-download-your-organizations-azure-pricing"></a>보기 및 조직의 Azure 가격 책정 다운로드

Azure 고객으로는 EA (기업 계약) 또는 [Microsoft 고객 계약](#check-your-access-to-a-microsoft-customer-agreement) 확인 및 Azure portal에서 가격을 다운로드할 수 있습니다. Microsoft 고객 계약에 있는 경우에 보기 및 다운로드는 Microsoft 고객 계약에 대 한 가격 책정 참조 하십시오.

## <a name="view-and-download-ea-pricing"></a>EA 가격 보기 및 다운로드

엔터프라이즈 관리자가 조직에 대해 설정한 정책에 따라 특정 관리 역할만 조직의 EA 가격 정보에 대한 액세스 권한을 제공합니다. 자세한 내용은 [Azure의 Azure 기업계약 관리 역할 이해](billing-understand-ea-roles.md)를 참조하세요.

1. 엔터프라이즈 관리자로 [Azure Portal](https://portal.azure.com/)에 로그인합니다.
1. **Cost Management + 청구**에서 검색합니다.

   ![Azure Portal 검색을 보여 주는 스크린샷](./media/billing-ea-pricing/portal-cm-billing-search.png)

1. 청구 계정에서 **사용량 + 요금**을 선택합니다.

   ![청구 아래의 사용량 및 요금을 보여 주는 스크린샷](./media/billing-ea-pricing/ea-pricing-usage-charges-nav.png)

1. 해당 월의 ![Azure Portal 검색을 보여 주는 스크린샷](./media/billing-ea-pricing/download-icon.png) **다운로드**를 선택합니다.
1. **가격표** 아래에서 **csv 다운로드**를 선택합니다.

   ![가격표 csv 다운로드 단추를 보여 주는 스크린샷](./media/billing-ea-pricing/download-ea-price-sheet.png)

## <a name="view-and-download-pricing-for-your-microsoft-customer-agreement"></a>보기 및 다운로드 Microsoft 고객 계약에 대 한 가격 책정

청구 프로필 소유자, 참가자, 판독기 또는 송장 관리자로 보려면 가격 책정을 다운로드 하 여야 합니다. 자세한 청구 역할에 대 한 Microsoft 고객 계약에 대 한 참조 [청구 프로필 역할 및 작업](billing-understand-mca-roles.md#billing-profile-roles-and-tasks)합니다.

### <a name="download-price-sheets-for-the-current-billing-period"></a>현재 청구 기간의 가격표 다운로드

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. **Cost Management + 청구**에서 검색합니다.
1. 청구 프로필을 선택 합니다. 액세스를 따라 대금 청구 계정을 먼저 선택 해야 할 수 있습니다.
1. 에 **개요** 블레이드에서 월-일 요금은 아래 다운로드 링크를 찾습니다.
1. 선택 **Azure 가격표**합니다.
![다운로드 개요에서 보여 주는 스크린샷](./media/billing-ea-pricing/open-pricing.png)

### <a name="download-price-sheets-for-billed-charges"></a>청구 요금에 대 한 가격표를 다운로드 합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. **Cost Management + 청구**에서 검색합니다.
1. 청구 프로필을 선택 합니다. 액세스를 따라 대금 청구 계정을 먼저 선택 해야 할 수 있습니다.
1. **청구서**를 선택합니다.
1. 송장 그리드에서 다운로드 하려는 가격표에 해당 청구서의 행을 찾습니다.
1. 줄임표를 클릭 (`...`) 행의 끝입니다.
![선택 하는 줄임표 (...)를 보여 주는 스크린샷](./media/billing-ea-pricing/billingprofile-invoicegrid.png)

1. 선택한 청구서의 서비스 가격은 참조 하려는 경우 선택 **청구서 가격표**합니다.
1. 지정된 된 청구 기간에 대 한 모든 Azure 서비스에 대 한 가격을 보려면 원하는 경우 선택할 **Azure 가격표**합니다.

![가격표를 사용 하 여 상황에 맞는 메뉴를 보여 주는 스크린샷](./media/billing-ea-pricing/contextmenu-pricesheet.png)

## <a name="estimate-costs-with-the-azure-pricing-calculator"></a>Azure 가격 계산기를 사용 하 여 비용을 예상 합니다.

사용할 수도 있습니다 조직의 가격 Azure 가격 계산기를 사용 하 여 비용을 예측 합니다.

1. [Azure 가격 계산기](https://azure.microsoft.com/pricing/calculator)로 이동합니다.
1. 오른쪽 맨 위에서 **로그인**을 선택합니다.
1. **프로그램 및 제품** > **라이선스 프로그램** 아래에서 **EA(기업계약)** 를 선택합니다.
1. **프로그램 및 제품** > **선택한 계약** 아래에서 **선택된 항목 없음**을 선택합니다.

    ![가격표 csv 다운로드 단추를 보여 주는 스크린샷](./media/billing-ea-pricing/ea-pricing-calculator-estimate.png)

1. 조직을 선택합니다.
1. **적용**을 선택합니다.
1. 제품을 검색하고 예상에 추가합니다.
1. 표시된 예상 가격은 선택한 조직의 가격을 기준으로 합니다.

## <a name="check-your-access-to-a-microsoft-customer-agreement"></a>Microsoft 고객 계약에 대 한 액세스를 확인 합니다.
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="next-steps"></a>다음 단계

EA 고객 인 경우 참조 하세요.

- [Azure에 대 한 EA 청구 정보에 대 한 액세스 관리](billing-manage-access.md)
- [보기 및 Microsoft Azure 청구서를 다운로드 합니다.](billing-download-azure-invoice.md)
- [에 Microsoft Azure 사용 및 요금 보기 및 다운로드](billing-download-azure-daily-usage.md)
- [기업 계약 고객에 대 한 요금 청구 방식](billing-understand-your-bill-ea.md)

Microsoft 고객 계약에 있는 경우 참조 하세요.

- [Microsoft 고객 계약에 가격표의 용어 이해](billing-mca-understand-pricesheet.md)
- [보기 및 Microsoft Azure 청구서를 다운로드 합니다.](billing-download-azure-invoice.md)
- [에 Microsoft Azure 사용 및 요금 보기 및 다운로드](billing-download-azure-daily-usage.md)
- [보기 및 요금 청구 프로필에 대 한 세금 문서를 다운로드 합니다.](billing-mca-download-tax-document.md)
- [청구 프로필의 청구서 요금 이해](billing-mca-understand-your-bill.md)
