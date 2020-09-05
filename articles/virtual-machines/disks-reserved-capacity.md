---
title: 예약을 사용 하 여 Azure 디스크 저장소에 대 한 비용 최적화
description: 프리미엄 SSD 관리 디스크에 대 한 비용을 절감 하기 위해 Azure 디스크 저장소 예약 구매에 대해 알아봅니다.
author: roygara
ms.author: rogarana
ms.date: 01/30/2020
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: 9a6a56491a327d5d4eafc2c05dc2b54b137096b8
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/22/2020
ms.locfileid: "88752069"
---
# <a name="reduce-costs-with-azure-disks-reservation"></a>Azure 디스크 예약을 사용 하 여 비용 절감

예약 된 용량으로 Azure 디스크 저장소 사용량을 절약 하세요. Azure Reserved Virtual Machine Instances와 결합 된 Azure 디스크 저장소 예약을 사용 하면 총 VM (가상 머신) 비용을 낮출 수 있습니다. 예약 할인은 선택한 예약 범위에서 일치 하는 디스크에 자동으로 적용 됩니다. 이 자동 응용 프로그램 때문에 할인을 얻기 위해 예약을 관리 디스크에 할당할 필요가 없습니다.

할인은 디스크 사용량에 따라 매시간 적용 됩니다. 사용 하지 않는 예약 된 용량은 전달 되지 않습니다. Azure 디스크 저장소 예약 할인은 관리 되지 않는 디스크, 울트라 디스크 또는 페이지 blob 소비에 적용 되지 않습니다.

## <a name="determine-your-storage-needs"></a>저장소 요구 사항 확인

예약을 구입 하기 전에 저장소 요구 사항을 확인 하세요. 현재 Azure 디스크 저장소 예약은 Azure premium SSD Sku 선택에만 사용할 수 있습니다. 프리미엄 SSD의 SKU는 디스크의 크기와 성능을 결정 합니다.

저장소 요구를 결정할 때 용량을 기반으로 하는 디스크는 고려 하지 마세요. 예를 들어 P40 디스크에 대 한 예약을 사용 하 고이를 사용 하 여 두 개의 작은 P30 디스크에 대 한 요금을 지불할 수 없습니다. 예약을 구매 하는 경우 SKU 당 총 디스크 수에 대 한 예약만 구매 합니다.

디스크 예약은 디스크 SKU 당 생성 됩니다. 따라서 예약 소비는 제공 된 크기가 아닌 디스크 Sku의 단위를 기준으로 합니다.

예를 들어 2 TiB 프로 비전 된 저장소 용량을 가진 하나의 P40 디스크를 예약 한다고 가정 합니다. 또한 두 개의 P30 디스크만 할당 한다고 가정 합니다. 이 경우 P40 예약은 P30 소비를 고려 하지 않으며 P30 디스크에서 종 량 제 요금을 지불 합니다.
<br/>
<br/>
[!INCLUDE [disk-storage-premium-ssd-sizes](../../includes/disk-storage-premium-ssd-sizes.md)]

## <a name="purchase-considerations"></a>구매 시 고려 사항

디스크 예약 구매를 고려할 때 다음 방법을 권장 합니다.

- 사용 현황 정보를 분석 하 여 구매할 예약을 결정 합니다. 프로 비전 되거나 사용 된 디스크 용량이 아닌 디스크 Sku의 사용량을 추적 해야 합니다.
- VM 예약과 함께 디스크 예약을 검사 합니다. 최대 절약을 위해 VM 사용량과 디스크 사용에 대 한 예약을 수행 하는 것이 좋습니다. 먼저 올바른 VM 예약을 확인 한 다음 디스크 예약을 평가할 수 있습니다. 일반적으로 각 워크 로드에 대 한 표준 구성이 있습니다. 예를 들어 SQL Server 서버에는 두 개의 P40 데이터 디스크와 P30 운영 체제 디스크가 하나씩 있을 수 있습니다.
  
  이러한 종류의 패턴을 통해 구매할 수 있는 예약 된 용량을 확인할 수 있습니다. 이 방법은 평가 프로세스를 간소화 하 고 VM과 디스크 모두에 대 한 정렬 된 계획을가지고 있는지 확인할 수 있습니다. 계획에는 구독 또는 지역과 같은 고려 사항이 포함 됩니다.

## <a name="purchase-restrictions"></a>구매 제한

예약 할인은 현재 다음에 대해 사용할 수 없습니다.

- 관리 되지 않는 디스크 또는 페이지 blob.
- 표준 Ssd 또는 표준 하드 디스크 드라이브 (Hdd).
- P30: P1, P2, P3, P4, P6, P10, P15 및 P20 SSD Sku 보다 작은 프리미엄 SSD Sku
- Azure Government, Azure 독일 또는 Azure 중국 지역의 디스크.

드문 경우 지만 Azure는 지역에 용량이 부족 하 여 새 예약의 구매를 디스크 Sku의 하위 집합으로 제한 합니다.

## <a name="buy-a-disk-reservation"></a>디스크 예약 구입

[Azure Portal](https://portal.azure.com/)를 통해 Azure 디스크 저장소 예약을 구매할 수 있습니다. 예약에 대 한 요금을 지불할 수 있습니다. 월별 지불로 구매 하는 방법에 대 한 자세한 내용은 [월별 지불로 예약 구매](../cost-management-billing/reservations/prepare-buy-reservation.md#buy-reservations-with-monthly-payments)를 참조 하세요.

예약 된 용량을 구매 하려면 다음 단계를 수행 합니다.

1. Azure Portal에서 [구매 예약](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/Browse_AddCommand) 창으로 이동 합니다.

1. **Azure Managed Disks** 를 선택 하 여 예약을 구매 합니다.

    ![구매 예약 창](media/disks-reserved-capacity/disks-reserved-purchase-reservation.png) 

1. 다음 표에서 설명 하는 필수 값을 지정 합니다.

   |요소  |Description  |
   |---------|---------|
   |**범위**   |  예약과 관련 된 청구 혜택을 사용할 수 있는 구독 수입니다. 또한이 값은 특정 구독에 예약이 적용 되는 방법을 지정 합니다. <br/><br/> **공유**를 선택 하면 청구 컨텍스트 내의 모든 구독에서 Azure Storage 용량에 예약 할인이 적용 됩니다. 청구 컨텍스트는 Azure에 등록한 방법에 따라 결정됩니다. 기업 고객의 공유 범위는 등록이며 등록 내의 모든 구독을 포함합니다. 종 량 제 고객의 경우 공유 범위에는 계정 관리자가 만든 종 량 제 요금이 포함 된 모든 개별 구독이 포함 됩니다.  <br/><br/>  **단일 구독**을 선택 하면 선택한 구독의 Azure Storage 용량에 예약 할인이 적용 됩니다. <br/><br/> **단일 리소스 그룹**을 선택 하는 경우 선택한 구독의 Azure Storage 용량과 해당 구독의 선택 된 리소스 그룹에 예약 할인이 적용 됩니다. <br/><br/> 예약을 구매한 후 예약 범위를 변경할 수 있습니다.  |
   |**구독**  | Azure Storage 예약에 대해 지불 하는 데 사용 하는 구독입니다. 선택한 구독의 결제 방법을 사용 하 여 비용을 청구 합니다. 구독은 다음 형식 중 하나 여야 합니다.<br/><ul><li> 기업계약 (제품 번호 MS-AZR-0017P-0017P 및 MS-AZR-0017P-Ms-azr-0148p). Enterprise 구독에 대한 요금은 등록의 금액 약정 잔액에서 차감되거나 초과분에 대한 요금으로 청구됩니다.</li><br/><li>종 량 제 요금이 있는 개별 구독 (제품 번호 MS-AZR-0017P-0003P 및 MS-AZR-0017P-0023P). 종 량 제 요금이 있는 개별 구독의 경우 요금 청구는 구독에 대 한 신용 카드 또는 청구서 지불 방법으로 청구 됩니다.</li></ul>    |
   | **디스크** | 만들려는 SKU입니다. |
   | **지역** | 예약이 적용 되는 지역입니다. |
   | **청구 빈도** | 예약에 대 한 계정을 청구 하는 빈도입니다. 옵션에는 **월별** 및 **선행**이 포함 됩니다. |

    ![purchase.png 하려는 제품을 선택 하는 창](media/disks-reserved-capacity/premium-ssd-reserved-purchase-selection.png)

1. 예약에 대 한 값을 지정 하면 Azure Portal에 비용이 표시 됩니다. 또한 포털은 종 량 제 요금 청구에 대 한 할인율을 보여 줍니다. **다음** 을 선택 하 여 **구매 예약** 창으로 이동 합니다.

1. **구매 예약** 창에서 예약의 이름을 지정 하 고 원하는 예약의 총 수량을 선택할 수 있습니다. 예약 수는 디스크 수에 매핑됩니다. 예를 들어, 디스크를 100 개 예약 하려면 **수량** 값 **100**을 입력 합니다.

1. 예약의 총 비용을 검토 합니다.

    ![구매 예약 창](media/disks-reserved-capacity/premium-ssd-reserved-selecting-sku-total-purchase.png)

예약을 구매한 후에는 예약 용어와 일치 하는 기존 디스크 저장소 리소스에 자동으로 적용 됩니다. 디스크 저장소 리소스를 아직 만들지 않은 경우 예약 용어와 일치 하는 리소스를 만들 때마다 예약이 적용 됩니다. 두 경우 모두 성공적으로 구매한 후 예약 기간이 즉시 시작 됩니다.

## <a name="cancel-exchange-or-refund-reservations"></a>예약 취소, 교환 또는 환불

특정 제한 내에서 예약을 취소, 교환 또는 환불 할 수 있습니다. 자세한 내용은 [Azure Reservations의 셀프 서비스 교환 및 환불](https://docs.microsoft.com/azure/cost-management-billing/reservations/exchange-and-refund-azure-reservations)을 참조하세요.

## <a name="expiration-of-a-reservation"></a>예약 만료

예약이 만료 되 면 해당 예약에서 사용 하는 모든 Azure 디스크 저장소 용량은 종 량 제 요금으로 청구 됩니다. 예약은 자동으로 갱신되지 않습니다.

예약이 만료 되기 30 일 전에 전자 메일 알림을 받게 되 고 만료 날짜가 다시 표시 됩니다. 예약이 제공 하는 비용 절감 효과를 계속 활용 하려면 만료 날짜 보다 나중에 갱신 합니다.

## <a name="need-help-contact-us"></a>도움 필요 시 문의처

질문이 있거나 도움이 필요한 경우 [지원 요청을 만드세요](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>다음 단계

- [Azure 예약이란?](../cost-management-billing/reservations/save-compute-costs-reservations.md)
- [Azure Disk Storage에 예약 할인이 적용되는 방식 이해](../cost-management-billing/reservations/understand-disk-reservations.md)
