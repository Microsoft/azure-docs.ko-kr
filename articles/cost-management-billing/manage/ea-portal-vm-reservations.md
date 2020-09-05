---
title: Azure EA VM 예약 인스턴스
description: 이 문서에서는 VM 예약 인스턴스에 대한 Azure 예약을 통해 엔터프라이즈 등록과 관련된 비용을 절감할 수 있는 방법을 요약합니다.
author: bandersmsft
ms.author: banders
ms.date: 08/20/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: enterprise
ms.reviewer: boalcsva
ms.openlocfilehash: aa95cf8b649558dc3164fe12e4268f210301f755
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88687043"
---
# <a name="azure-ea-vm-reserved-instances"></a>Azure EA VM 예약 인스턴스

이 문서에서는 VM 예약 인스턴스에 대한 Azure 예약을 통해 엔터프라이즈 등록과 관련된 비용을 절감할 수 있는 방법을 요약합니다. 예약에 대한 자세한 내용은 [Azure Reservations란?](../reservations/save-compute-costs-reservations.md)을 참조하세요.

## <a name="reservation-exchanges-and-refunds"></a>예약 변경 및 환불

예약을 동일한 유형의 다른 예약으로 교환할 수 있습니다. 예약이 더 이상 필요 없는 경우 연간 50,000 USD까지 예약을 환불할 수 있습니다. Azure Portal을 통해 예약을 교환하거나 환불합니다. 자세한 내용은 [Azure Reservations의 셀프 서비스 교환 및 환불](../reservations/exchange-and-refund-azure-reservations.md)을 참조하세요.

## <a name="reservation-costs-and-usage"></a>예약 비용 및 사용량

기업 계약 고객은 Azure Portal 및 REST API에서 비용 및 사용량 데이터를 볼 수 있습니다. 예약 비용 및 사용량에 대해 다음을 수행할 수 있습니다.

- 예약 구매 데이터 가져오기.
- 예약을 사용한 구독, 리소스 그룹 또는 리소스를 파악.
- 예약 사용에 대한 차지백.
- 예약 절감액 계산.
- 사용률이 낮은 예약 데이터 가져오기.
- 예약 비용 분할.

예약 비용 및 사용량에 대한 자세한 내용은 [기업 계약 예약 비용 및 사용량 가져오기](../reservations/understand-reserved-instance-usage-ea.md)를 참조하세요.

가격 책정에 대한 자세한 내용은 [Linux Virtual Machines 가격 책정](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) 또는 [Windows Virtual Machines 가격 책정](https://azure.microsoft.com/pricing/details/virtual-machines/windows/)을 참조하세요.

## <a name="reserved-instances-api-support"></a>예약 인스턴스 API 지원

Azure API를 사용하여 프로그래밍 방식으로 Azure 서비스 또는 소프트웨어 예약에 대한 사용자 조직의 정보를 가져옵니다. 예를 들어 API를 사용하여 다음을 수행합니다.

- 구입할 예약 찾기
- 예약 구입
- 구매한 예약 보기
- 예약 액세스 보기 및 관리
- 예약 분할 또는 병합
- 예약 범위 변경

자세한 내용은 [Azure 예약 자동화를 위한 API](../reservations/reservation-apis.md)를 참조하세요.

## <a name="azure-reserved-virtual-machine-instances"></a>Azure 예약 가상 머신 인스턴스

예약된 인스턴스는 모든 VM에서 종량제 가격에 대한 가상 머신 비용을 최대 72%까지 줄일 수 있습니다. 또는 Azure 하이브리드 혜택과 결합하여 최대 82%까지 절감할 수 있습니다. 예약 인스턴스를 사용하면 1년 또는 3년 사용 약관에 대한 선불 결제를 통해 워크로드, 예산 및 예측을 보다 효율적으로 관리할 수 있습니다. 비즈니스 요구 사항이 변경되면 예약을 변경 또는 취소할 수 있습니다.

### <a name="how-to-buy-reserved-virtual-machine-instances"></a>예약 가상 머신 인스턴스를 구매하는 방법

Azure 예약 가상 머신 인스턴스를 구매하려면 Enterprise Azure 등록 관리자가 _예약 인스턴스_ 구매 옵션을 사용하도록 설정해야 합니다. 이 옵션은 [Azure EA Portal](https://ea.azure.com/)의 _등록_ 탭에 있는 _등록 세부 정보_ 섹션에 있습니다.

EA 등록을 사용하도록 설정하여 예약된 인스턴스를 추가하면 EA 등록과 연결된 활성 구독이 있는 계정 소유자는 [Azure Portal](https://aka.ms/reservations)에서 예약 가상 머신 인스턴스를 구매할 수 있습니다. 자세한 내용은 [가상 머신에 대해 선불 결제하고 예약 가상 머신 인스턴스를 사용하여 비용 절감](https://go.microsoft.com/fwlink/?linkid=861721)을 참조하세요.

### <a name="how-to-view-reserved-instance-purchase-details"></a>예약 인스턴스 구매 세부 정보를 보는 방법

[Azure Portal](https://aka.ms/reservations) 또는 [Azure EA Portal](https://ea.azure.com/)의 왼쪽에 있는 _예약_ 메뉴를 통해 예약 인스턴스 구매 세부 정보를 볼 수 있습니다. 왼쪽 메뉴에서 **보고서**를 선택하고 _사용량 요약_ 탭에서 _서비스별 청구_ 섹션까지 아래로 스크롤합니다. 섹션의 아래쪽으로 스크롤하면 예약된 인스턴스 구매 및 사용량이 서비스 이름 옆에 `1 year` 또는 `3 years` 지정으로 표시된 것처럼 마지막에 나열됩니다(예: `Standard_DS1_v2 eastus 1 year` 또는 `Standard_D2s_v3 eastus2 3 years`).

### <a name="how-can-i-change-the-subscription-associated-with-reserved-instance-or-transfer-my-reserved-instance-benefits-to-a-subscription-under-the-same-account"></a>예약된 인스턴스와 연결된 구독을 변경하거나 예약 인스턴스 혜택을 동일한 계정의 구독에 전송하려면 어떻게 해야 하나요?

다음을 수행하여 예약 인스턴스 혜택을 받는 구독을 변경할 수 있습니다.

- [Azure Portal](https://aka.ms/reservations)에 로그인합니다.
- 동일한 계정에서 다른 구독을 연결하여 적용된 구독 범위를 업데이트합니다.

예약 범위를 변경하는 방법에 대한 자세한 내용은 [예약 범위 변경](../reservations/manage-reserved-vm-instance.md#change-the-reservation-scope)을 참조하세요.

### <a name="how-to-view-reserved-instance-usage-details"></a>예약 인스턴스 사용량 세부 정보를 보는 방법

[Azure Portal](https://aka.ms/reservations) 또는 [Azure EA Portal](https://ea.azure.com/)(대금 청구 정보를 볼 수 있는 EA 고객용)에서 _보고서_ > _사용량 요약_ > _서비스별 청구_에 있는 예약 인스턴스 사용량 세부 정보를 볼 수 있습니다. 예약된 인스턴스는 '예약'을 포함하는 서비스 이름으로 식별할 수 있습니다(예: `Reservation-Base VM or Virtual Machines Reservation-Windows Svr (1 Core)`).

사용량 세부 정보 및 고급 보고서 다운로드 CSV에는 추가 예약 인스턴스 사용량 정보가 있습니다. _추가 정보_ 필드는 예약 인스턴스 사용량을 식별하는 데 도움이 됩니다.

Azure 하이브리드 혜택을 사용하여 Azure Reserved VM Instances를 구입하지 않은 경우 예약된 인스턴스는 두 가지 요금제(하드웨어 및 소프트웨어)를 내보냅니다. Azure 하이브리드 혜택을 사용하여 예약 인스턴스를 구매한 경우 예약 인스턴스 사용량 세부 정보에 소프트웨어 요금제가 표시되지 않습니다.

### <a name="reserved-instance-billing"></a>예약된 인스턴스 청구

기업 고객의 경우 Azure 선불을 사용하여 Azure 예약 VM 인스턴스를 구입할 수 있습니다. 등록에 예약 인스턴스 구매를 처리하기에 충분한 Azure 선불 잔액이 있는 경우 해당 금액이 Azure 선불 잔액에서 공제되고 구매에 대한 송장은 제공되지 않습니다.

Azure EA 고객이 모든 Azure 선불을 사용한 경우에도 예약된 인스턴스를 계속 구입할 수 있으며 그러한 구입은 다음 초과분 청구서로 요금이 청구됩니다. 예약된 인스턴스가 초과되는 경우에는 정기적인 초과 청구서에 포함됩니다.

### <a name="reserved-instance-expiration"></a>예약된 인스턴스 만료

예약 및 만료되기까지 30일 동안 이메일 알림을 보내드립니다. 예약이 만료되면 배포된 VM은 계속 실행되며 종량제 요금으로 청구됩니다. 자세한 내용은 [Reserved Virtual Machine Instances 제품](https://azure.microsoft.com/pricing/reserved-vm-instances/)을 참조하세요.

## <a name="next-steps"></a>다음 단계

- Azure 예약에 대한 자세한 내용은 [Azure Reservations란?](../reservations/save-compute-costs-reservations.md)을 참조하세요.
- 기업 예약 비용 및 사용량에 대한 자세한 내용은 [기업 계약 예약 비용 및 사용량 가져오기](../reservations/understand-reserved-instance-usage-ea.md)를 참조하세요.
- 가격 책정에 대한 자세한 내용은 [Linux Virtual Machines 가격 책정](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) 또는 [Windows Virtual Machines 가격 책정](https://azure.microsoft.com/pricing/details/virtual-machines/windows/)을 참조하세요.
