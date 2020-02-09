---
title: 비용을 절감 하기 위한 Azure 데이터 탐색기 태그의 선불
description: Azure 데이터 탐색기 비용을 절감 하기 위해 Azure 데이터 탐색기 예약 용량을 구입 하는 방법을 알아봅니다.
author: avneraa
ms.author: avnera
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 11/03/2019
ms.openlocfilehash: bbb2108967353b851a0fa0ee610ec30380e3fc50
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/15/2020
ms.locfileid: "75969278"
---
# <a name="prepay-for-azure-data-explorer-markup-units-with-azure-data-explorer-reserved-capacity"></a>Azure 데이터 탐색기 예약 된 용량을 사용 하 여 Azure 데이터 탐색기 태그 단위에 대한 선불

종 량 제 가격과 비교 하 여 태그 단위를 총액 하 여 Azure 데이터 탐색기로 비용을 절감 하세요. Azure 데이터 탐색기 reserved capacity를 사용 하 여 azure 데이터 탐색기 태그 비용에 대해 상당한 할인을 얻기 위해 1 년 또는 3 년 동안 Azure 데이터 탐색기에 대한 약정을 설정 합니다. Azure 데이터 탐색기 reserved capacity를 구매 하려면 용어를 지정 하기만 하면 모든 지역에서 Azure 데이터 탐색기의 모든 배포에 적용 됩니다.

예약을 구매 하면 1 년 또는 3 년 동안 태그 비용을 미리 지불 하 게 됩니다. 예약을 구매 하는 즉시, 예약 특성과 일치 하는 Azure 데이터 탐색기 태그 요금은 더 이상 종 량 제 요금으로 청구 되지 않습니다. 이미 실행 중인 Azure 데이터 탐색기 클러스터 또는 새로 배포 된 클러스터는 자동으로 혜택을 받게 됩니다. 이 예약에는 클러스터와 연결 된 계산, 네트워킹 또는 저장소 요금이 포함 되지 않습니다. 이러한 리소스에 대한 예약 된 용량은 별도로 구입 해야 합니다. 예약 기간이 끝나면 청구 혜택이 만료 되며 Azure 데이터 탐색기 태그 단위는 종 량 제 요금으로 청구 됩니다. 예약은 자동 갱신 되지 않습니다. 가격 책정 정보는 [Azure 데이터 탐색기 가격 책정 페이지](https://azure.microsoft.com/pricing/details/data-explorer/)를 참조 하세요.

[Azure Portal](https://portal.azure.com)에서 Azure 데이터 탐색기 reserved capacity를 구매할 수 있습니다. Azure 데이터 탐색기 예약 된 용량을 구매 하려면 다음을 수행 합니다.

* 하나 이상의 Enterprise 또는 종 량 제 구독 소유자 여야 합니다.
* Enterprise 구독의 경우 [EA 포털](https://ea.azure.com)에서 **예약 인스턴스 추가**를 활성화해야 합니다. 또는 해당 설정을 사용 하지 않도록 설정한 경우에는 구독에 대한 EA 관리자 여야 합니다.
* CSP (클라우드 솔루션 공급자) 프로그램의 경우 관리 에이전트 또는 판매 에이전트만 Azure 데이터 탐색기 예약 된 용량을 구매할 수 있습니다.

엔터프라이즈 고객과 종 량 제 고객이 예약 구매에 대해 부과 하는 방법에 대한 자세한 내용은 다음을 참조 하세요.
* [기업 등록에 대한 Azure 예약 사용량 이해](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md) 
* [종 량 제 구독에 대한 Azure 예약 사용량을 이해](../cost-management-billing/reservations/understand-reserved-instance-usage.md)합니다.

## <a name="determine-the-right-markup-usage-before-purchase"></a>구매 하기 전에 올바른 태그 사용 확인

예약 크기는 기존 또는 곧 배포 되는 Azure 데이터 탐색기 클러스터에서 사용 하는 Azure 데이터 탐색기 태그 단위의 총 수를 기반으로 해야 합니다. 태그 단위 수는 프로덕션의 Azure 데이터 탐색기 엔진 클러스터 코어 수와 같습니다 (개발/테스트 SKU를 포함 하지 않음). 

## <a name="buy-azure-data-explorer-reserved-capacity"></a>Azure 데이터 탐색기 예약 된 용량 구입

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. **모든 서비스** > **예약** > **지금 구입**을 선택 합니다. **추가**를 선택합니다.
1. **제품 유형 선택** 창에서 azure **데이터 탐색기** 를 선택 하 여 azure 데이터 탐색기 태그 단위에 대한 새 예약을 구매 합니다. 
1. **구매** 선택
1. 필수 필드를 입력합니다. 

    ![구매 페이지](media/pricing-reserved-capacity/purchase-page.png)

1. **비용** 섹션에서 Azure 데이터 탐색기 태그 예약 용량 예약의 비용을 검토 합니다.
1. **구매**를 선택합니다.
1. 구매 상태를 보려면 **이 예약 보기**를 선택합니다.

## <a name="cancellations-and-exchanges"></a>취소 및 교환

Azure 데이터 탐색기 예약 된 용량 예약을 취소 해야 하는 경우 12% 조기 종료 요금이 있을 수 있습니다. 환불는 구매 가격의 가장 낮은 가격 또는 예약의 현재 가격을 기준으로 합니다. 환불은 연간 $50,000로 제한됩니다. 받는 환불은 12%의 조기 종료 수수료를 뺀 나머지 비례 배분한 잔액입니다. 취소를 요청하려면 Azure Portal의 예약으로 이동하고 **환불**을 선택하여 지원 요청을 만듭니다.

Azure 데이터 탐색기 예약 된 용량 예약을 다른 용어로 변경 해야 하는 경우 값이 같거나 큰 다른 예약에 대해 교환할 수 있습니다. 새 예약에 대한 기간 시작일은 교환된 예약에서 이월되지 않습니다. 새 예약을 만들 때 1년 또는 3년의 기간이 시작됩니다. 교환을 요청하려면 Azure Portal의 예약으로 이동하고, **교환**을 선택하여 지원 요청을 만듭니다.

예약을 교환 하거나 환불 하는 방법에 대한 자세한 내용은 [예약 교환 및 환불](../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md)을 참조 하세요.

## <a name="manage-your-reserved-capacity-reservation"></a>예약 된 용량 예약 관리

Azure 데이터 탐색기 태그 단위 예약 할인은 Azure 데이터 탐색기 예약 된 용량 예약 범위 및 특성과 일치 하는 태그 단위 수에 자동으로 적용 됩니다. 


> [!NOTE]
> * [Azure Portal](https://portal.azure.com), POWERSHELL, CLI 또는 API를 통해 Azure 데이터 탐색기 예약 된 용량 예약의 범위를 업데이트할 수 있습니다.
> * Azure 데이터 탐색기 예약 된 용량 예약을 관리 하는 방법을 알아보려면 [azure 데이터 탐색기 예약 된 용량 관리](../cost-management-billing/reservations/understand-azure-data-explorer-reservation-charges.md)를 참조 하세요.

## <a name="next-steps"></a>다음 단계

Azure 예약에 대한 자세한 내용은 다음 문서를 참조하세요.

* [Azure 예약이란?](../cost-management-billing/reservations/save-compute-costs-reservations.md)
* [Azure Reservations 관리](../cost-management-billing/reservations/manage-reserved-vm-instance.md)
* [Azure 예약 할인 이해](../cost-management-billing/reservations/understand-reservation-charges.md)
* [종량제 구독의 예약 사용량 이해](../cost-management-billing/reservations/understand-reserved-instance-usage.md)
* [엔터프라이즈 등록에서 예약 사용량 이해](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)
* [파트너 센터 CSP(클라우드 솔루션 공급자) 프로그램의 Azure 예약](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-us"></a>도움이 필요하세요? 문의하기

질문이 있거나 도움이 필요한 경우 [지원 요청을 만드세요](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).
