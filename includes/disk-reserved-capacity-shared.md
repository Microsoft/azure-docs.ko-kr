---
title: 포함 파일
description: 포함 파일
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 01/15/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 89586f932ee358664a2869c87ced72594336b404
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76847135"
---
예약 된 용량을 사용 하 여 프리미엄 SSD (반도체 드라이브) 사용량을 절약 하 고 예약 된 가상 머신 인스턴스와 결합 하 여 총 VM 비용을 줄일 수 있습니다. 예약 할인은 선택한 예약 범위에서 일치 하는 디스크에 자동으로 적용 되므로 할인을 얻기 위해 예약을 관리 디스크에 할당할 필요가 없습니다. 할인은 디스크 사용량에 매시간 적용 되며 사용 되지 않는 예약 된 용량은 모두 사용 되지 않습니다. 관리 되는 디스크 예약 할인은 관리 되지 않는 디스크, 울트라 디스크 또는 페이지 blob 소비에 적용 되지 않습니다.

## <a name="determine-your-storage-needs"></a>저장소 요구 사항 확인

예약을 구입 하기 전에 저장소에 필요한 사항을 결정 해야 합니다. 현재 디스크 예약은 select premium SSD Sku 에서만 사용할 수 있습니다. 프리미엄 SSD의 SKU는 디스크의 크기와 성능을 결정 합니다. 저장소 요구를 결정 하는 경우 디스크를 총 용량으로 생각 하는 것이 좋습니다. 즉, 더 큰 디스크 (예: P40)에 대한 예약을 사용 하 고이를 사용 하 여 두 개의 작은 디스크 (P30)에 대한 비용을 지불할 수 없습니다. 예약을 구매할 때 SKU 당 총 디스크 수만 구매 합니다.

디스크 SKU 당 디스크 예약이 생성 되므로 예약 소비는 제공 된 크기가 아닌 디스크 Sku의 단위를 기준으로 합니다. 예를 들어 프로 비전 된 1 P40 TiB 프로 비전 된 용량의 2 개의 P30 디스크만 할당 한 경우 두 개의 P30 소비는 P40 예약에 대해 고려 되지 않으며 종 량 제 요금이 부과 됩니다.



[!INCLUDE [disk-storage-premium-ssd-sizes](disk-storage-premium-ssd-sizes.md)]

## <a name="purchase-considerations"></a>구매 고려 사항

디스크 예약 구매를 고려할 때 다음 모범 사례를 따르는 것이 좋습니다.

- 사용 현황 정보를 분석 하 여 구매할 예약을 결정 합니다. 프로 비전 되거나 사용 된 디스크 용량이 아닌 디스크 Sku의 사용량을 추적 하 고 있는지 확인 합니다. 
- VM 예약과 함께 디스크 예약을 검사 합니다. 최대 절약을 위해 VM 및 디스크 사용에 대한 예약을 수행 하는 것이 좋습니다. 올바른 VM 예약을 확인 하 고 그에 따라 디스크 예약을 평가할 수 있습니다. 일반적으로 각 워크 로드에 대한 표준 구성이 있습니다. 예를 들어 SQL server에는 두 개의 P40 데이터 디스크와 하나의 P30 OS 디스크가 있을 수 있습니다. 이러한 종류의 패턴은 구매할 수 있는 예약의 양을 결정 하는 데 도움이 될 수 있습니다. 이 방법은 평가 프로세스를 간소화 하 고 구독, 지역 및 기타 측면에서 VM과 디스크 모두에 대한 정렬 된 계획을가지고 있는지도 확인할 수 있습니다. 

## <a name="purchase-restrictions"></a>구매 제한

예약 할인은 현재 다음 디스크에 대해 사용할 수 없습니다.
- 관리 되지 않는 디스크/페이지 blob
- 표준 SSD 또는 표준 HDD
- P30 보다 작은 프리미엄 SSD Sku-예약은 P1/P2/P3/P4/P6/P10/P15/P20 프리미엄 SSD Sku에 사용할 수 없습니다.
- 클라우드-예약은 Azure .Gov, 독일 또는 중국 지역에서 구매할 수 없습니다.
- 용량 제한-드문 경우 지만 Azure는 지역에 용량이 부족 하 여 디스크 Sku의 하위 집합에 대한 새 예약 구매를 제한 합니다.

## <a name="buy-a-disk-reservation"></a>디스크 예약 구입

[Azure Portal](https://portal.azure.com/)를 통해 Azure 디스크 예약을 구매할 수 있습니다. 예약에 대한 요금을 지불 하거나 월별 지불을 수행할 수 있습니다. 월별 지불로 구매 하는 방법에 대한 자세한 내용은 [월별 지불로 예약 구매](../articles/cost-management-billing/reservations/monthly-payments-reservations.md)를 참조 하세요.

예약 된 용량을 구매 하려면 다음 단계를 수행 합니다.

1. Azure Portal에서 [구매 예약](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/Browse_AddCommand) 블레이드로 이동 합니다.
1. **Azure Managed Disks** 를 선택 하 여 예약을 구매 합니다.

    ![disks-reserved-purchase-reservation](media/disks-reserved-capacity/disks-reserved-purchase-reservation.png)    

1. 아래 표에서 설명한 대로 필요한 필드를 채웁니다.

   |필드  |Description  |
   |---------|---------|
   |**범위**   |  예약과 관련 된 청구 혜택을 사용할 수 있는 구독 수를 나타냅니다. 또한 예약이 특정 구독에 적용되는 방식을 제어합니다. <br/><br/> **공유**를 선택 하면 청구 컨텍스트 내의 모든 구독에서 Azure Storage 용량에 예약 할인이 적용 됩니다. 청구 컨텍스트는 Azure에 등록한 방법에 따라 결정됩니다. 기업 고객의 공유 범위는 등록이며 등록 내의 모든 구독을 포함합니다. 종 량 제 고객의 경우 공유 범위에는 계정 관리자가 만든 종 량 제 요금이 포함 된 모든 개별 구독이 포함 됩니다.  <br/><br/>  **단일 구독**을 선택 하면 선택한 구독의 Azure Storage 용량에 예약 할인이 적용 됩니다. <br/><br/> **단일 리소스 그룹**을 선택 하는 경우 예약 할인이 선택한 구독의 Azure Storage 용량과 해당 구독 내에서 선택한 리소스 그룹에 적용 됩니다. <br/><br/> 예약을 구매한 후 예약 범위를 변경할 수 있습니다.  |
   |**구독**  | Azure Storage 예약에 대해 지불 하는 데 사용 되는 구독입니다. 선택한 구독의 결제 방법을 사용 하 여 비용을 청구 합니다. 구독은 다음 형식 중 하나 여야 합니다. <br/><br/>  기업계약 (제품 번호: MS-AZR-0017P-0017P 또는 MS-AZR-0017P-Ms-azr-0148p): Enterprise 구독의 경우 요금 청구는 등록의 금액 약정 잔액에서 공제 되거나 초과분로 청구 됩니다. <br/><br/> 종 량 제 요금이 있는 개별 구독 (제품 번호: MS-MS-AZR-0017P-0003P 또는 MS-AZR-0017P-0023P): 종 량 제 요금이 있는 개별 구독의 경우 요금 청구는 구독에 대한 신용 카드나 청구서 지불 방법으로 청구 됩니다.    |
   | **디스크** | 만들려는 SKU입니다. |
   | **지역** | 예약이 적용 되는 지역입니다. |
   | **청구 빈도** | 예약에 대한 계정 청구 빈도를 나타냅니다. 옵션에는 *월별* 또는 *선행*이 포함 됩니다. |

    ![premium-ssd-reserved-purchase-selection](media/disks-reserved-capacity/premium-ssd-reserved-purchase-selection.png)

1. 예약에 대한 매개 변수를 선택 하면 Azure Portal에 비용이 표시 됩니다. 또한 포털은 종 량 제 요금 청구에 대한 할인율을 보여 줍니다. **다음** 을 선택 하 여 **구매 예약** 블레이드로 이동 합니다.

1. **구매 예약** 창에서 예약의 이름을 지정 하 고 원하는 예약의 총 수량을 선택할 수 있습니다. 디스크 수에 대한 예약의 수입니다. 예를 들어 100 개의 디스크를 예약 하려는 경우 **수량** 을 100로 변경 합니다.
1. 예약의 총 비용을 검토 합니다.

    ![premium-ssd-reserved-selecting-sku-total-purchase](media/disks-reserved-capacity/premium-ssd-reserved-selecting-sku-total-purchase.png)

예약을 구매 하면 예약의 조건과 일치 하는 기존 Azure 디스크 저장소 리소스에 자동으로 적용 됩니다. Azure Disk storage 리소스를 아직 만들지 않은 경우 예약의 조건과 일치 하는 리소스를 만들 때마다 예약이 적용 됩니다. 두 경우 모두 성공적으로 구매한 후 예약 기간이 즉시 시작 됩니다.

## <a name="exchange-or-refund-a-reservation"></a>예약 교환 또는 환불

특정 제한 사항을 사용 하 여 예약을 교환 하거나 환불 받을 수 있습니다.

예약을 교환 하거나 환불 하려면 Azure Portal의 예약 세부 정보로 이동 합니다. **Exchange 또는 환불**을 선택 하 고 지침에 따라 지원 요청을 제출 합니다. 요청이 처리 되 면 Microsoft에서 요청 완료를 확인 하는 전자 메일을 보냅니다.

Azure Reservations 정책에 대한 자세한 내용은 [셀프 서비스 교환 및 Azure Reservations에 대한 환불](../articles/cost-management-billing/reservations/exchange-and-refund-azure-reservations.md)를 참조 하세요.

### <a name="exchange-a-reservation"></a>예약 교환

예약을 교환 하면 예약의 사용 되지 않는 부분을 기준으로 비례 있는 환불을 받을 수 있습니다. 그런 다음 새 Azure 디스크 예약의 구매 가격에 환불을 적용할 수 있습니다.
만들 수 있는 교환 수에 대한 제한은 없습니다. 또한 교환과 연관된 요금은 없습니다. 구매한 새 예약은 원래 예약에서 비례 하는 크레딧을 초과 하는 값 보다 크거나 같아야 합니다. Azure 디스크 예약은 다른 azure 서비스에 대한 예약이 아닌 다른 Azure 디스크 예약에 대해서만 교환할 수 있습니다.

### <a name="refund-a-reservation"></a>예약 상환

언제 든 지 Azure 디스크 예약을 취소할 수 있습니다. 취소 하는 경우 예약의 남은 기간을 기준으로 비례를 계산 하 여 12%의 조기 종료 수수료를 뺀 값이 표시 됩니다. 연간 최대 환불은 $5만입니다.
예약을 취소 하면 즉시 예약이 종료 되 고 남은 월이 Microsoft로 돌아옵니다. 남은 비례 잔액을 제외 하 고 원래 구매 형태에 환불 됩니다.

## <a name="expiration-of-a-reservation"></a>예약 만료

예약이 만료 되 면 해당 예약에서 사용 하는 모든 Azure 디스크 용량은 종 량 제 요금으로 청구 됩니다. 예약은 자동으로 갱신되지 않습니다.
예약이 만료 되기 30 일 전에 전자 메일 알림을 받고 만료 날짜가 다시 청구 됩니다. 예약이 제공 하는 비용 절감 효과를 계속 활용 하려면 만료 날짜 보다 나중에 갱신 합니다.

## <a name="need-help-contact-us"></a>도움이 필요하세요? 문의하기

질문이 있거나 도움이 필요한 경우 [지원 요청을 만드세요](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>다음 단계

- [Azure 예약이란?](../articles/cost-management-billing/reservations/save-compute-costs-reservations.md)
- [Azure 디스크 저장소에 예약 할인이 적용 되는 방식을 이해 합니다.](../articles/cost-management-billing/reservations/understand-disk-reservations.md)