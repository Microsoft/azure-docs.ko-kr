---
title: 셀프 서비스 교환 및 Azure 예약에 대 한 환불 | Microsoft Docs
description: 교환할 수 있습니다 하는 방법에 대해 알아봅니다. Azure 예약 환불 또는 합니다.
documentationcenter: ''
author: yashesvi
manager: yashesvi
editor: ''
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/13/2019
ms.author: banders
ms.openlocfilehash: 54578746ea8029a760663edc456660f98358abc5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60615976"
---
# <a name="self-service-exchanges-and-refunds-for-azure-reservations"></a>Azure 예약에 대 한 환불 및 셀프 서비스 교환

Azure 예약에 변화 하는 요구를 충족 하기 위해 유연성을 제공 합니다. 예약을 동일한 유형의 다른 예약으로 교환할 수 있습니다. 예약이 더 이상 필요 없는 경우 연간 50,000USD까지 예약을 환불할 수 있습니다.

US Government 기업계약 고객은 셀프 서비스 교환 및 취소 기능을 사용할 수 없습니다. 종 량 제 및 CSP를 포함 한 다른 US Government 구독 유형은 지원 됩니다.

바꾸거나 기존 예약 상환 예약 순서에 대 한 소유자 액세스를 해야 합니다.

## <a name="exchange-an-existing-reserved-instance"></a>기존 예약 된 인스턴스를 교환 합니다.

세 가지 빠른 단계를 사용 하 여 예약을 교환할 수 있습니다 합니다 [Azure portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade)합니다.

1. 환불 및 클릭 하려는 예약을 선택 **Exchange**합니다.  
    ![반환할 예약을 보여 주는 예제 이미지](./media/billing-azure-reservations-self-service-exchange-and-refund/exchange-refund-return.png)
2. 구매하려는 VM 제품을 선택하고 수량을 입력합니다. 새 구매 합계 반환 총 이상 인지 확인 합니다. [구입 하기 전에 적절 한 크기 결정](../virtual-machines/windows/prepay-reserved-vm-instances.md#determine-the-right-vm-size-before-you-buy)합니다.  
    ![Exchange를 사용 하 여 구매할 VM 제품을 보여 주는 예제 이미지](./media/billing-azure-reservations-self-service-exchange-and-refund/exchange-refund-select-purchase.png)
3. 검토 하 고 트랜잭션을 완료 합니다.  
    ![VM 제품 반환을 완료 하는 exchange를 사용 하 여 구매를 보여 주는 예제 이미지](./media/billing-azure-reservations-self-service-exchange-and-refund/exchange-refund-confirm-exchange.png)

예약을 환불로 이동 **예약 세부 정보** 누릅니다 **환불**합니다.

## <a name="how-transactions-are-processed"></a>트랜잭션은 처리 하는 방법

먼저 Microsoft는 기존 예약을 취소 하 고 해당 예약에 대 한 비례 배분 금액을 환불 합니다. 교환 인 경우 새 구매 처리 됩니다. Microsoft는 결제 방법을 확인 하 고 계정 유형에 따라 다음 방법 중 하나를 사용 하 여 환불을 처리 합니다.

### <a name="enterprise-agreement-customers"></a>기업 계약 고객

Money 교환에 대 한 약정에 추가 되 고 원래 구매 하나를 사용 하 여 수행 된 경우 환불 합니다. 원래 구매는 다시 시작 되 고 있는지 현금 약정 rerated 있으므로 초과분 청구서 사용 됩니다. 약정 용어를 사용 하 여 예약을 구매한 더 이상 활성 상태인 경우 신용 현재 기업 계약 현금 약정 기간에 추가 됩니다.

원래 구매 초과분으로 만들어진 경우 크레딧 메모가 Microsoft에 발급 합니다.

### <a name="pay-as-you-go-invoice-payments-and-csp-program"></a>종 량 제 청구서 지급 및 CSP 프로그램

원래 예약 구매 송장이 취소가 만들어지고 새 송장을 환불이 가능 합니다. 교환에 대 한 새 청구서에는 환불 및 새 구매 보여 줍니다. 환불 금액의 구매에 대 한 조정 됩니다. 만 예약을 환불을 Microsoft와 비례 배분된 양을 유지 이후 예약 구매에 대해 조정 하는 고 합니다.

### <a name="pay-as-you-go-credit-card-customers"></a>종 량 제 신용 카드 고객

원래 송장을 취소 되 고 새 송장을 생성 됩니다. 돈은 원래 구입에 사용 된 신용 카드에 환불 됩니다. 카드를 변경한 경우 [지원에 문의](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)합니다.

## <a name="exchange-policies"></a>Exchange 정책

- 동일한 유형의 새 예약을 구매 하는 여러 기존 예약을 반환할 수 있습니다. 다른 한 가지 유형의 예약을 교환할 수 없습니다. 예를 들어 SQL 예약을 구매 하는 VM 예약을 반환할 수 없습니다.
- 예약 소유자만 교환을 처리할 수 있습니다. [에 대해 알아봅니다 예약을 관리할 수 있는 사용자를 추가 또는 변경 하는 방법을](https://docs.microsoft.com/azure/billing/billing-manage-reserved-vm-instance#add-or-change-users-who-can-manage-a-reservation)합니다.
- 환불 및 다시 처리 되는 exchange – 다른 트랜잭션을 취소 하 고 새 구매에 대해 생성 됩니다. 예약에 대 한 비례 배분된 예약 금액은 환불 보상 해당 합니다. 새 구매에 대 한 완벽 하 게 청구 됩니다. 비례 배분된 예약 크기에는 반환 되 고 예약 매일 비례 배분된 남아 있는 값입니다.
- 교환할 수 있습니다 또는 기업 계약 구매 하는 데 예약 하는 경우에 환불 예약 만료 되 고 새 계약으로 갱신 했습니다.
- 크기, 지역, quantity 및 용어는 exchange와 같은 예약 속성을 변경할 수 있습니다.
- 새 구매 합계는 동일 하거나 반환 된 크기 보다 클 수 해야 합니다.
- 새 exchange의 일부로 구매한 예약에 exchange의 시간에서 시작 하는 새 용어
- 저하 또는 교환에 대 한 연간 제한 하지 않습니다.

## <a name="refund-policies"></a>환불 정책

- 총 환불 금액에는 12 개월 롤링 창에서 $50,000 USD를 초과할 수 없습니다.
- 예약 소유자만 환불을 처리할 수 있습니다. [에 대해 알아봅니다 예약을 관리할 수 있는 사용자를 추가 또는 변경 하는 방법을](billing-manage-reserved-vm-instance.md#add-or-change-users-who-can-manage-a-reservation)합니다.
- Microsoft 권리를 모든 반환에 대 한 12% 페널티를 충전 페널티 현재 부과 되지 않지만 합니다.

## <a name="exchange-non-premium-storage-for-premium-storage"></a>Premium storage에 대 한 Exchange 비 premium storage

Premium storage는 해당 VM 크기를 지원 하지 않는 VM 크기에 대해 구매한 예약을 교환할 수 있습니다. 예를 들어를 _F1_ 에 _F1s_합니다. Exchange가 하도록 예약 세부 정보로 이동 하 고 클릭 **Exchange**합니다. Exchange가 새 트랜잭션을 만들거나 reserved instance의 용어를 다시 설정 하지 않습니다.

## <a name="need-help-contact-us"></a>도움 필요 시 문의하세요.

질문이 있거나 도움이 필요한 경우 [지원 요청을 만드세요](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>다음 단계

- 예약을 관리하는 방법을 알아보려면 [Azure Reservations 관리](billing-manage-reserved-vm-instance.md)를 참조하세요.
- Azure Reservations에 대한 자세한 내용은 다음 문서를 참조하세요.
    - [Azure Reservations란?](billing-save-compute-costs-reservations.md)
    - [Azure에서 Reservations 관리](billing-manage-reserved-vm-instance.md)
    - [예약 할인이 적용되는 방식 이해](billing-understand-vm-reservation-charges.md)
    - [종량제 구독의 예약 사용량 이해](billing-understand-reserved-instance-usage.md)
    - [엔터프라이즈 등록에서 예약 사용량 이해](billing-understand-reserved-instance-usage-ea.md)
    - [예약에 포함되지 않는 Windows 소프트웨어 비용](billing-reserved-instance-windows-software-costs.md)
    - [파트너 센터 CSP(클라우드 솔루션 공급자) 프로그램의 Azure 예약](/partner-center/azure-reservations)
