---
title: Azure Reservation 비용 차지백
description: 차지백에 대한 Azure Reservation 비용을 확인하는 방법을 알아봅니다.
author: yashesvi
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: how-to
ms.date: 07/24/2020
ms.author: banders
ms.openlocfilehash: 4387a1aa5b22c9808f36364b71f89a71736958b8
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88689476"
---
# <a name="charge-back-azure-reservation-costs"></a>Azure Reservation 비용 차지백

기업계약 및 Microsoft 고객 계약 청구 독자는 예약에 대한 분할 상환 비용 데이터를 볼 수 있습니다. 비용 데이터를 사용하여 구독, 리소스 그룹, 리소스 또는 태그에 대한 통화 값을 파트너에게 차지백할 수 있습니다. 분할 상환 데이터에서 유효 가격은 비례하는 시간당 예약 비용입니다. 비용은 해당 날짜에 대한 리소스의 총 예약 사용량 비용입니다.

개별 구독이 있는 사용자는 해당 사용량 파일에서 분할 상환 비용 데이터를 가져올 수 있습니다. 리소스가 예약 할인을 가져오는 경우 사용량 파일의 *AdditionalInfo* 섹션에는 예약 정보가 포함됩니다. 자세한 내용은 [Azure Portal에서 사용량 다운로드](https://docs.microsoft.com/azure/cost-management-billing/understand/download-azure-daily-usage#download-usage-from-the-azure-portal-csv)를 참조하세요.

## <a name="get-reservation-charge-back-data-for-chargeback"></a>차지백에 대한 예약 차지백 데이터 가져오기

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. **Cost Management + 청구**로 이동합니다.
1. **실제 비용**에서 **분할 상환 비용** 메트릭을 선택합니다.
1. 예약에서 사용된 리소스를 보려면 **예약** 필터를 적용한 다음, 예약을 선택합니다.
1. **세분성**을 **월별** 또는 **일별**로 설정합니다.
1. 차트 유형을 **테이블**로 설정합니다.
1. **그룹화 기준** 옵션을 **리소스**로 설정합니다.

[![차니백에 사용할 수 있는 예약 리소스 비용을 보여 주는 예제](./media/charge-back-usage/amortized-reservation-costs.png)](./media/charge-back-usage/amortized-reservation-costs.png#lightbox)

Azure Portal에서 예약 사용률 비용을 보는 방법을 보여 주는 비디오는 다음과 같습니다.

 > [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4sQOw] 

## <a name="need-help-contact-us"></a>도움 필요 시 문의하세요.

질문이 있거나 도움이 필요한 경우 [지원 요청을 만드세요](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>다음 단계

- 예약을 관리하는 방법을 알아보려면 [Azure Reservations 관리](manage-reserved-vm-instance.md)를 참조하세요.
- Azure 예약에 대한 자세한 내용은 다음 문서를 참조하세요.
  - [Azure 예약이란?](save-compute-costs-reservations.md)
  - [Azure에서 Reservations 관리](manage-reserved-vm-instance.md)
