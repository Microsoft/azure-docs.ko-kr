---
title: Azure 디스크 스토리지에 대한 예약 할인 이해
description: Azure 예약된 디스크 할인이 Azure 프리미엄 SSD 관리 디스크에 적용되는 방법을 알아봅니다.
author: roygara
ms.author: rogarana
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: conceptual
ms.date: 01/30/2020
ms.openlocfilehash: cd89c3ddc3c58de02f1104109ce7f243c4d1e6fd
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88682453"
---
# <a name="understand-how-your-reservation-discount-is-applied-to-azure-disk-storage"></a>예약 할인이 Azure 디스크 스토리지에 적용되는 방법 이해

Azure 디스크 예약된 용량을 구입하면 예약 할인이 예약의 조건과 일치하는 디스크 리소스에 자동으로 적용됩니다. 예약 할인은 디스크 SKU에만 적용됩니다. 디스크 스냅샷은 종량제 요율로 청구됩니다.

Azure 디스크 예약에 대한 자세한 내용은 [Azure 디스크 예약을 사용하여 비용 절감](../../virtual-machines/disks-reserved-capacity.md)을 참조하세요. Azure 디스크 예약 가격 책정에 대한 자세한 내용은 [Azure Managed Disks 가격](https://azure.microsoft.com/pricing/details/managed-disks/)을 참조하세요.

## <a name="how-the-reservation-discount-is-applied"></a>예약 할인이 적용되는 방식

Azure 디스크 예약 할인은 use-it-or-lose-it(사용, 아니면 손실) 할인입니다. 시간 단위로 관리 디스크 리소스에 적용됩니다. 지정된 시간 동안 예약 조건을 충족하는 관리 디스크 리소스가 없으면 해당 시간에 대한 예약 수량이 손실됩니다. 사용하지 않은 시간은 이월되지 않습니다.

리소스를 삭제하면 지정된 범위에 있는 일치하는 다른 리소스에 예약 할인이 자동으로 적용됩니다. 일치하는 리소스가 없으면 예약된 시간이 손실됩니다.

## <a name="discount-examples"></a>할인 예제

다음 예제에서는 Azure 디스크 예약 할인이 배포에 따라 적용되는 방법을 보여 줍니다.

미국 서부 2 지역에서 1년 동안 100개의 P30 디스크를 구입하여 예약한다고 가정합니다. 각 디스크에는 약 1TiB의 스토리지가 있습니다. 이 예약 샘플의 비용은 $140,100이라고 가정합니다. 다음 12개월 동안 전체 금액을 선불로 지불하거나 매월 $11,675의 고정 분할불로 지불하도록 선택할 수 있습니다.

다음 시나리오에서는 예약된 용량을 미달 사용, 초과 사용 또는 계층화하는 경우 발생하는 상황에 대해 설명합니다. 이러한 예제에서는 월간 예약 지불 계획에 가입했다고 가정합니다.

### <a name="underusing-your-capacity"></a>용량 미달 사용

예약 기간 내에 1시간 동안 예약된 100개의 Azure 프리미엄 SSD(반도체 드라이브) P30 디스크 중 99개만 배포한다고 가정합니다. 나머지 P30 디스크는 해당 시간 동안 적용되지 않습니다. 또한 이월되지 않습니다.

### <a name="overusing-your-capacity"></a>용량 초과 사용

예약 기간 내에 1시간 동안 101개의 프리미엄 SSD P30 디스크를 사용한다고 가정합니다. 예약 할인은 100개의 P30 디스크에만 적용됩니다. 나머지 P30 디스크는 해당 시간 동안 종량제 요금으로 청구됩니다. 다음 1시간 동안 사용량이 100개의 P30 디스크로 감소하면 모든 사용량이 예약에 포함됩니다.

### <a name="tiering-your-capacity"></a>용량 계층화

예약 기간 내의 지정된 시간에 총 200개의 P30 프리미엄 SSD를 사용하려고 한다고 가정합니다. 또한 처음 30분 동안 100개만 사용한다고 가정합니다. 이 기간 동안 100개의 P30 디스크를 예약했으므로 사용한 디스크가 모두 적용됩니다. 그런 다음, 처음 100의 사용을 중단하고(0을 사용하도록) 나머지 30분 동안 다른 100을 사용하기 시작하는 경우 해당 사용량이 예약에도 적용됩니다.

![용량 미달 사용, 초과 사용 및 계층화의 예](media/understand-disk-reservations/reserved-disks-example-scenarios.png)

## <a name="need-help-contact-us"></a>도움 필요 시 문의처

질문이 있거나 도움이 필요한 경우 [지원 요청을 만드세요](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>다음 단계

- [Azure 디스크 예약을 사용하여 비용 절감](../../virtual-machines/disks-reserved-capacity.md)
- [Azure 예약이란?](save-compute-costs-reservations.md)
