---
title: 소프트웨어 계획-Azure 예약에 대 한 요금을 선불 | Microsoft Docs
description: 종 량 제 비용 보다 비용 절약 하는 소프트웨어 계획에 대해 선불 결제 수 하는 방법에 대해 알아봅니다.
documentationcenter: ''
author: yashesvi
manager: yashesvi
editor: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/12/2019
ms.author: banders
ms.openlocfilehash: e15dcdbbcaed32d836bb751ef93ce17e90bd6905
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60771246"
---
# <a name="prepay-for-azure-software-plans"></a>Azure 소프트웨어 플랜 선결제

Azure에서 SUSE 및 RedHat 소프트웨어 사용량에 대 한 이용 하면 선불 하는 경우에 종 량 제 비용 보다 비용을 저장할 수 있습니다. 할인은 SUSE 및 RedHat 미터와 가상 머신 사용량에 없는만 적용 됩니다. 추가적인 절약을 위해 별도로 virtual machines에 대 한 예약을 구입할 수 있습니다.

Azure portal에서 SUSE 및 RedHat 소프트웨어 계획을 구입할 수 있습니다. 플랜을 구매하려면:

- 하나 이상의 Enterprise 또는 종 량 제 구독에 대 한 소유자 역할이 있어야 합니다.
- Enterprise 구독에 대 한 합니다 **Reserved Instances 추가** 옵션에서 사용할 수 있어야 합니다 합니다 [EA 포털](https://ea.azure.com/). 설정 하지 않으면 구독에 대해 EA 관리자 여야 합니다.
- 클라우드 솔루션 공급자 (CSP) 프로그램에 대 한 관리 에이전트 및 영업 에이전트 소프트웨어 계획을 구입할 수 있습니다.

## <a name="buy-a-software-plan"></a>소프트웨어 요금제 구매

1. Azure portal에 로그인 하 고 이동할 [예약](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade)합니다.
2. 클릭 **추가** 다음 구입 하려는 소프트웨어 계획을 선택 하 고 있습니다.
필수 필드를 입력합니다. 모든 SUSE Linux VM 또는 RedHat VM 구입할 있습니다의 특성과 일치 하는 할인을 가져옵니다. 할인을 받을 실제 배포 수는 선택한 범위 및 수량에 따라 달라집니다.
3. 구독을 선택합니다. 계획에 대 한 요금을 지불 하는 것이 됩니다.
구독 결제 방법은 예약에 대해 선불로 비용이 청구 됩니다. 구독 유형은 기업 계약을 체결 해야 합니다. (번호를 제공 합니다. MS-AZR-0017P 또는 MS-AZR-0148P) 또는 종량제(제안 번호: MS-AZR-0003P 또는 MS-AZR-0023P)여야 합니다.
    - Enterprise 구독에 대한 요금은 등록의 금액 약정 잔액에서 차감되거나 초과 비용으로 청구됩니다.
    - 종 량 제 구독에 대 한 구독의 신용 카드 또는 청구서 지불 방법으로 요금이 청구 됩니다.
4. 범위를 선택 합니다. 범위에는 하나 또는 여러 개의 구독(공유 범위)이 포함될 수 있습니다.
    - 구독에 대 한 사용량을 일치 하는 단일 구독-계획 할인 적용 됩니다.
    - 공유-청구 컨텍스트에서 모든 구독에 있는 인스턴스를 일치 하는 계획 할인이 적용 됩니다. 기업 고객을 위한 청구 컨텍스트는 등록 및 등록에 대 한 모든 구독을 포함 합니다. 종 량 제 고객에 대 한 청구 컨텍스트는 계정 관리자가 만든 모든 종 량 제 구독입니다.
5. VM 크기 및 이미지 형식을 선택 하는 제품을 선택 합니다. 선택한 VM 크기에만에 할인이 적용 됩니다.
6. 1 년 또는 3 년 기간을 선택 합니다.
7. 청구 할인을 받을 수 있는 선불 VM 인스턴스의 수는 수량을 선택 합니다.
8. 카트, 검토 및 구매 제품을 추가 합니다.

예약 할인에 대 한 사전 지불 하는 소프트웨어 미터에 자동으로 적용 됩니다. 계획에 VM 계산 요금이 포함 되지 않습니다. VM 예약을 별도로 구입할 수 있습니다.

## <a name="discount-applies-to-different-suse-vm-sizes"></a>할인은 다른 SUSE VM 크기에 적용 됩니다.

예약된 VM 인스턴스를 같은 SUSE Linux 계획 인스턴스 크기 유연성을 제공합니다. 할인에는 구입한 SUSE 계획에서 크기가 다른 VM을 배포한 경우에 적용 됩니다. 자세한 내용은 [소프트웨어 계획 할인이 적용 되는 방식을 이해](../../billing/billing-understand-suse-reservation-charges.md)합니다.

## <a name="redhat-plan-discount"></a>RedHat 계획 할인

계획은 Red Hat Enterprise Linux 가상 머신에 대해서만 사용할 수 있습니다. RedHat Enterprise Linux의 SAP HANA Vm 또는 RedHat Enterprise Linux SAP 비즈니스 앱 Vm 할인이 적용 되지 않습니다.

RedHat 계획 할인 구매 시 선택 하는 VM 크기에만 적용 됩니다. RHEL 계획 환불 또는 구입 후 교환 될 수 없습니다.


## <a name="cancellation-and-exchanges-not-allowed"></a>취소 및 교환은 허용되지 않습니다.

취소 하거나 exchange 구입한 SUSE 또는 RedHat 계획 수 없습니다. 올바른 플랜을 구매했는지 확인하려면 사용량을 확인합니다. 구입을 식별 하는 도움말을 참조 하세요 [소프트웨어 계획 할인이 적용 되는 방식을 이해](../../billing/billing-understand-suse-reservation-charges.md)합니다.

## <a name="need-help-contact-us"></a>도움 필요 시 문의하세요.

질문이 있거나 도움이 필요한 경우 [지원 요청을 만드세요](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>다음 단계

예약을 관리하는 방법을 알아보려면 [Azure 예약 관리](../../billing/billing-manage-reserved-vm-instance.md)를 참조합니다.

자세한 내용은 다음 문서를 참조하세요.

- [Azure 예약이란?](../../billing/billing-save-compute-costs-reservations.md)
- [Azure에서 Reservations 관리](../../billing/billing-manage-reserved-vm-instance.md)
- [SUSE 예약 할인이 적용되는 방식 이해](../../billing/billing-understand-suse-reservation-charges.md)
- [종량제 구독의 예약 사용량 이해](../../billing/billing-understand-reserved-instance-usage.md)
- [엔터프라이즈 등록에서 예약 사용량 이해](../../billing/billing-understand-reserved-instance-usage-ea.md)
