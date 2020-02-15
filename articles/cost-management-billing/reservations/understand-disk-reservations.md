---
title: Azure Disk Storage에 예약 할인이 적용되는 방식 이해
description: Azure 예약 디스크 할인이 프리미엄 SSD 관리 디스크에 적용되는 방법에 대해 알아봅니다.
author: roygara
ms.service: cost-management-billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/30/2020
ms.author: rogarana
ms.openlocfilehash: 7f23aaebc20b562768fb04ae988e4aff1b62fb21
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/31/2020
ms.locfileid: "76902136"
---
# <a name="understand-how-your-reservation-discount-is-applied-to-azure-disk-storage"></a>Azure Disk Storage에 예약 할인이 적용되는 방식 이해

Azure 디스크 예약된 용량을 구매한 후 예약의 조건과 일치하는 디스크 리소스에 예약 할인이 자동으로 적용됩니다. 예약 할인은 디스크 용량에만 적용되며, 디스크 스냅샷은 종량제 요금으로 청구됩니다.

Azure 디스크 예약에 대한 자세한 내용은 [Azure 디스크 예약을 사용하여 비용 절감](../../virtual-machines/linux/disks-reserved-capacity.md)을 참조하세요.
Azure 디스크 예약 가격 책정에 대한 자세한 내용은 [Azure Disk 가격 책정](https://azure.microsoft.com/pricing/details/managed-disks/)을 참조하세요.

## <a name="how-the-reservation-discount-is-applied"></a>예약 할인이 적용되는 방식

Azure 디스크 예약 할인은 "use-it-or-lose-it(사용 또는 소멸)" 할인으로, 관리 디스크 리소스에 매시간 적용됩니다. 지정된 시간에 예약 조건을 충족하는 관리 디스크 리소스가 없으면 해당 시간에 대한 예약 수량이 손실되며 사용되지 않은 시간은 이월되지 않습니다.

리소스를 삭제하면 지정된 범위에 있는 일치하는 다른 리소스에 예약 할인이 자동으로 적용됩니다. 지정된 범위에서 일치하는 리소스를 찾을 수 없는 경우 예약된 시간은 소멸됩니다.

## <a name="discount-examples"></a>할인 예제

다음 예제에서는 배포에 따라 Azure 디스크 예약 할인이 적용되는 방법을 보여 줍니다.

1년 동안 미국 서부 2 지역에서 100 P30 디스크(디스크당 1TiB)의 예약된 용량을 구입했다고 가정합니다. 이 샘플 예약의 비용을 140,100달러라고 가정합니다. 전체 금액을 미리 지불하거나 앞으로 12개월 동안 매월 11,675달러의 고정 월별 할부금을 지불하도록 선택할 수 있습니다.
이러한 예제에서는 월별 예약 지불 계획에 등록했다고 가정합니다. 다음 시나리오에서는 예약된 용량을 사용, 과용 또는 계층화하는 경우 발생하는 상황을 설명합니다.

### <a name="underusing-your-capacity"></a>용량 미달 사용

예약 기간 내의 지정된 시간에 100 P30 디스크 예약의 99 P30 프리미엄 SSD만 배포했다고 가정합니다. 나머지 1 P30은 해당 시간 동안 적용되지 않으며 이월되지 않습니다.

### <a name="overusing-your-capacity"></a>용량 초과 사용

예약 기간 내의 지정된 시간에 101 P30 프리미엄 SSD를 사용하고 있다고 가정합니다. 예약 할인은 100 P30 디스크에만 적용되며 나머지 1 P30 디스크는 해당 시간에 대한 종량제 요금으로 청구됩니다. 다음 시간 동안 사용량이 100 P30 디스크로 감소하는 경우 모든 사용량은 예약에 포함됩니다.

### <a name="tiering-your-capacity"></a>용량 계층화

예약 기간 내의 지정된 시간에 총 200 P30 프리미엄 SSD를 사용하려 한다고 가정합니다. 처음 30분의 경우 처음 30분 동안 100만 사용합니다. 이 기간 동안에는 100 P30 디스크에 대한 예약을 만든 이후 사용에 대한 내용이 모두 적용됩니다. 그런 다음, 처음 100의 사용을 중단하고(0을 사용하도록) 나머지 30분 동안 다른 100을 사용하기 시작하는 경우 해당 사용량이 예약에도 적용됩니다.

![용량 미달 사용, 과용 및 계층화 예제의 묘사](media/understand-disk-reservations/reserved-disks-example-scenarios.png)

## <a name="need-help-contact-us"></a>도움 필요 시 문의처

질문이 있거나 도움이 필요한 경우 [지원 요청을 만드세요](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>다음 단계

- [Azure 디스크 예약을 사용하여 비용 절감(Linux)](../../virtual-machines/linux/disks-reserved-capacity.md)
- [Azure 디스크 예약을 사용하여 비용 절감(Windows)](../../virtual-machines/windows/disks-reserved-capacity.md)
- [Azure 예약이란?](save-compute-costs-reservations.md)