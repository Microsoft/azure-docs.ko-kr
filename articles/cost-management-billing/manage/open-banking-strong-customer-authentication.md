---
title: Azure 고객을 위한 PSD2(오픈 뱅킹) 및 SCA(강력한 고객 인증)
description: 이 문서에서는 일부 Azure 구매에 다단계 인증이 필요한 이유와 인증을 완료하는 방법을 설명합니다.
author: bandersmsft
ms.reviewer: judupont
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: b4c2db76405e7bad7653c1801eb2bcc5db82fe9b
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88684799"
---
# <a name="open-banking-psd2-and-strong-customer-authentication-sca-for-azure-customers"></a>Azure 고객을 위한 PSD2(오픈 뱅킹) 및 SCA(강력한 고객 인증)

2019년 9월 14일 현재, [유럽 경제 지역](https://en.wikipedia.org/wiki/European_Economic_Area)에 속한 31개 국가/지역의 은행은 결제를 처리하기 전에 온라인 구매자의 신분을 의무적으로 확인해야 합니다. 신분을 확인하려면 다단계 인증이 필요하며, 신분 확인을 통해 온라인 구매를 안전하게 보호할 수 있습니다. 일부 국가/지역에서는 이 확인 요구 사항의 날짜가 지연될 예정입니다. 자세한 내용은 [PSD2에 대한 Microsoft FAQ](https://support.microsoft.com/en-us/help/4517854?preview)를 참조하세요.

## <a name="what-psd2-means-for-azure-customers"></a>Azure 고객을 위한 PSD2의 의미

[유럽 경제 지역](https://en.wikipedia.org/wiki/European_Economic_Area)의 은행에서 발급한 신용 카드로 Azure 요금을 지불하는 경우 계정의 결제 방법에 대해 다단계 인증을 완료해야 하는 경우가 있습니다. Azure 계정에 가입하거나 Azure 계정을 업그레이드할 때(제품을 구매하지 않더라도) 다단계 인증을 완료하라는 메시지가 표시될 수 있습니다. Azure 계정의 결제 방법을 변경하거나, 지출 한도를 제거하거나, Azure Portal에서 즉시 결제할 때 다단계 인증을 제공하라는 요청을 받을 수 있습니다(예: 미결제 잔액 설정 또는 Azure 크레딧 구매).

은행에서 월간 Azure 요금을 거부하는 경우 Azure에서 문제 해결 지침이 포함된 기한 경과 이메일을 받게 됩니다. Azure Portal에서 다단계 인증을 완료하고 미결제 요금을 정산할 수 있습니다.

## <a name="complete-multi-factor-authentication-in-the-azure-portal"></a>Azure Portal에서 다단계 인증 완료

다음 섹션에서는 Azure Portal에서 다단계 인증을 완료하는 방법을 설명합니다. 자신에게 적용되는 정보를 사용합니다.

### <a name="change-the-active-payment-method-of-your-azure-account"></a>Azure 계정의 활성 결제 방법 변경

다음 단계에 따라 Azure 계정의 활성 결제 방법을 변경할 수 있습니다.

1. 계정 관리자로 [Azure Portal](https://portal.azure.com)에 로그인하고 **Cost Management + 청구**로 이동합니다.
2. **개요** 페이지의 **내 구독** 그리드에서 해당하는 구독을 선택합니다.
3. '청구'에서 **결제 방법**을 선택합니다. 새 신용 카드를 추가하거나 기존 카드를 구독의 활성 결제 방법으로 설정할 수 있습니다. 은행에서 다단계 인증을 요구하는 경우 프로세스 중에 인증을 완료하라는 메시지가 표시됩니다.

자세한 내용은 [Azure에 대한 신용 카드 추가, 업데이트 또는 제거](change-credit-card.md)

### <a name="settle-outstanding-charges-for-azure-services"></a>Azure 서비스의 미결제 요금 정산

은행에서 요금을 거부하는 경우 Azure Portal의 Azure 계정 상태가 **기한 경과**로 변경됩니다. 다음 단계에 따라 계정 상태를 확인할 수 있습니다.

1. 계정 관리자 권한으로 [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. **Cost Management + 청구**를 검색합니다.
3. **Cost Management + 청구** **개요** 페이지에서 **내 구독** 그리드의 상태 열을 검토합니다.
4. 구독 상태가 **기한 초과**인 경우 **잔액 결제**를 클릭합니다. 이 과정에서 다단계 인증을 완료하라는 메시지가 표시됩니다.

### <a name="settle-outstanding-charges-for-marketplace-and-reservation-purchases"></a>Marketplace 및 예약 구매의 미결제 요금 정산

Marketplace 및 예약 구매 요금은 Azure 서비스와 별도로 청구됩니다. 은행에서 Marketplace 또는 예약 요금을 거부하는 경우 청구서는 기한이 경과한 것이며, Azure Portal에서 **지금 지불** 옵션이 표시됩니다. 지난 Marketplace 및 예약 청구서는 다음 단계에 따라 결제할 수 있습니다.

1. 계정 관리자 권한으로 [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. **Cost Management + 청구**를 검색합니다.
3. '청구' 아래에서 **청구서**를 선택합니다.
5. 구독 드롭다운 필터에서 Marketplace 또는 예약 구매와 관련된 구독을 선택합니다.
6. 청구서 그리드에서 유형 열을 검토합니다. 형식이 **Azure Marketplace 및 예약**인 경우 청구서가 만료되었거나 기한이 경과하면 **지금 지불** 링크가 표시됩니다. **지금 지불**이 표시되지 않으면 청구서가 이미 지불되었음을 의미합니다. 지금 지불 중에 다단계 인증을 완료하라는 메시지가 표시됩니다.

## <a name="next-steps"></a>다음 단계
- Azure 청구서를 결제해야 하는 경우 [Azure 구독의 미지불금액 해결](resolve-past-due-balance.md)을 참조하세요.
