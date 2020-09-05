---
title: Red Hat 예약 요금제 할인 - Azure
description: 가상 머신의 Red Hat 소프트웨어에 Red Hat 요금제 할인이 적용되는 방법을 알아봅니다.
author: yashesvi
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: conceptual
ms.date: 06/11/2020
ms.author: banders
ms.openlocfilehash: 4314a52488473bcacd4aa62a0e8f44d88d2b75f1
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88681603"
---
# <a name="understand-how-the-red-hat-linux-enterprise-software-reservation-plan-discount-is-applied-for-azure"></a>Azure에 Red Hat Linux Enterprise 소프트웨어 예약 요금제 할인이 적용되는 방식 이해

Red Hat Linux 요금제를 구입한 후에 예약에 해당하는 배포된 Red Hat VM(가상 머신)에 자동으로 할인이 적용됩니다. Red Hat Linux 요금제는 Azure VM에서 Red Hat 소프트웨어를 실행하는 비용을 포함합니다.

적절한 Red Hat Linux 요금제를 구입하려면 실행하는 Red Hat VM과 해당 VM에 있는 vCPU 수를 이해해야 합니다. 다음 섹션은 사용량 CSV 파일에서 구입할 요금제를 식별하는 데 도움이 됩니다.

## <a name="discount-applies-to-different-vm-sizes"></a>다른 VM 크기에 할인이 적용됩니다.

Reserved VM Instances와 같이 Red Hat 요금제 구매는 인스턴스 크기 유연성을 제공합니다. 즉, 다른 vCPU 수를 사용하는 VM을 배포할 때도 할인이 적용됩니다. 소프트웨어 요금제 내의 다른 VM 크기에도 할인이 적용됩니다.

할인 금액은 다음 표에 나열된 비율에 따라 달라집니다. 이 비율은 해당 그룹의 각 VM 크기에 대한 상대적 공간을 비교합니다. 이 비율은 VM vCPU에 따라 달라집니다. Red Hat Linux 요금제 할인을 받는 VM 인스턴스 수를 계산할 때 이 비율 값을 사용합니다.

예를 들어 1~4개의 vCPU가 있는 VM에 대해 Red Hat Linux Enterprise Server용 요금제를 구입하는 경우 해당 예약에 대한 비율은 1입니다. 이 할인은 다음에 대한 Red Hat 소프트웨어 비용을 포함합니다.

- 1~4개의 vCPU가 있는 배포된 VM 1개
- 또는 5개 이상의 vCPU가 있는 VM의 경우 Red Hat Enterprise Linux 비용의 약 0.46 또는 46%입니다.

### <a name="red-hat-enterprise-linux"></a>Red Hat Enterprise Linux

Azure Portal Marketplace 이름:
- Red Hat Enterprise Linux 6.7
- Red Hat Enterprise Linux 6.8
- Red Hat Enterprise Linux 6.9
- Red Hat Enterprise Linux 6.10
- Red Hat Enterprise Linux 7
- Red Hat Enterprise Linux 7.2
- Red Hat Enterprise Linux 7.3
- Red Hat Enterprise Linux 7.4
- Red Hat Enterprise Linux 7.5
- Red Hat Enterprise Linux 7.6
- Red Hat Enterprise Linux 8.2

[계획이 적용되는 Red Hat Enterprise Linux 미터 확인](https://isfratio.blob.core.windows.net/isfratio/RHELRatios.csv)

## <a name="next-steps"></a>다음 단계

예약에 대해 자세히 알아보려면 다음 문서를 참조하세요.

- [Azure 예약이란?](save-compute-costs-reservations.md)
- [Azure 예약에서 Red Hat 소프트웨어 요금제에 대한 선불](../../virtual-machines/linux/prepay-rhel-software-charges.md)
- [Azure Reserved VM Instances를 사용하여 Virtual Machines 선불 결제](../../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Azure용 예약 관리](manage-reserved-vm-instance.md)
- [종량제 구독의 예약 사용량 이해](understand-reserved-instance-usage.md)
- [엔터프라이즈 등록에서 예약 사용량 이해](understand-reserved-instance-usage-ea.md)

## <a name="need-help-contact-us"></a>도움 필요 시 문의처

질문이 있거나 도움이 필요한 경우 [지원 요청을 만드세요](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).
