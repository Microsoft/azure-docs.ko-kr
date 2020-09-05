---
title: Azure Reservation 구매 및 환불 트랜잭션 보기
description: Azure Reservation 구매 및 환불 트랜잭션을 보는 방법에 대해 알아봅니다.
author: yashesvi
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: how-to
ms.date: 07/24/2020
ms.author: banders
ms.openlocfilehash: d9e5269468f7cd4571e7ae686af7f1ef159b4ef3
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88681705"
---
# <a name="view-reservation-purchase-and-refund-transactions"></a>예약 구매 및 환불 트랜잭션 보기

몇 가지 방법으로 예약 구매 및 환불 트랜잭션을 볼 수 있습니다. Azure Portal, Power BI 및 REST API를 사용할 수 있습니다.

## <a name="view-reservation-transactions-in-the-azure-portal"></a>Azure Portal에서 예약 트랜잭션 보기

기업 등록 또는 Microsoft 고객 계약 청구 관리자는 Cost Management 및 청구에서 예약 트랜잭션을 볼 수 있습니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. **Cost Management + 청구**를 검색합니다.
1. **예약 트랜잭션**을 선택합니다.
1. 결과를 필터링하려면 **시간 범위**, **유형** 또는 **설명**을 선택합니다.
1. **적용**을 선택합니다.

[![Azure Portal에서 예약 트랜잭션을 보여 주는 스크린샷](./media/view-purchase-refunds/azure-portal-reservation-transactions.png)](./media/view-purchase-refunds/azure-portal-reservation-transactions.png#lightbox)

## <a name="view-reservation-transactions-in-power-bi"></a>Power BI에서 예약 트랜잭션 보기

기업 등록 또는 Microsoft 고객 계약 청구 관리자는 Cost Management Power BI 앱을 사용하여 예약 트랜잭션을 볼 수 있습니다.

1. [Cost Management Power BI 앱](https://appsource.microsoft.com/product/power-bi/costmanagement.azurecostmanagementapp)을 가져옵니다.
1. RI 구매 보고서로 이동합니다.

[![예약 트랜잭션을 보여 주는 예제](./media/view-purchase-refunds/power-bi-reservation-transactions.png)](./media/view-purchase-refunds/power-bi-reservation-transactions.png#lightbox)

자세한 내용은 [기업계약에 대한 Azure Cost Management Power BI 앱](https://docs.microsoft.com/azure/cost-management-billing/costs/analyze-cost-data-azure-cost-management-power-bi-template-app)을 참조하세요.

## <a name="use-apis-to-get-reservation-transactions"></a>API를 사용하여 예약 트랜잭션 가져오기

EA(기업계약) 및 Microsoft 고객 계약 사용자는 [예약 트랜잭션 - 목록 API](https://docs.microsoft.com/rest/api/consumption/reservationtransactions/list)를 사용하여 예약 트랜잭션 데이터를 가져올 수 있습니다.

## <a name="need-help-contact-us"></a>도움 필요 시 문의하세요.

질문이 있거나 도움이 필요한 경우 [지원 요청을 만드세요](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>다음 단계

- 예약을 관리하는 방법을 알아보려면 [Azure Reservations 관리](manage-reserved-vm-instance.md)를 참조하세요.
- Azure 예약에 대한 자세한 내용은 다음 문서를 참조하세요.
  - [Azure 예약이란?](save-compute-costs-reservations.md)
  - [Azure에서 Reservations 관리](manage-reserved-vm-instance.md)
