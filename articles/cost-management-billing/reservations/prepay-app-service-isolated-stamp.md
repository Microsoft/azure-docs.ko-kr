---
title: 예약 용량으로 Azure App Service에 저장
description: 예약 용량으로 Azure App Service 격리 인지세 비용을 절감하는 방법을 알아봅니다.
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: how-to
ms.date: 07/24/2020
ms.author: banders
ms.openlocfilehash: c80ed02e137ea092bae1346260dba71cd417bed9
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88688932"
---
# <a name="save-costs-for-azure-app-service-isolated-stamp-fee-with-reserved-capacity"></a>예약 용량으로 Azure App Service 격리 인지세 비용 절감

3년간의 스탬프 사용량에 대한 예약을 커밋하여 Azure App Service 격리 인지세 비용을 절감할 수 있습니다. 격리 인지세 예약 용량을 구매하려면 스탬프를 배포할 Azure 지역 및 구매할 스탬프 수를 선택해야 합니다.

예약을 구매하는 경우 예약 특성과 일치하는 격리 인지세 사용에 대해 종량제 요금이 더 이상 청구되지 않습니다. 예약은 예약 용량 범위 및 지역과 일치하는 격리 스탬프 수에 자동으로 적용됩니다. 격리 스탬프에는 예약을 할당할 필요가 없습니다. 예약은 작업자에게 적용되지 않으므로 스탬프와 연결된 다른 모든 리소스에는 별도로 요금이 청구됩니다.

예약 용량이 만료되면 격리 스탬프가 계속 실행되지만 종량제 요금이 청구됩니다. 예약은 자동으로 갱신되지 않습니다.

## <a name="determine-the-right-reservation-to-purchase"></a>구매할 적절한 예약 결정

예약을 구매하면 향후 3년 동안 예약된 수량을 사용하도록 커밋하는 것입니다. 사용량 데이터를 확인하여 지속적으로 사용 중이고 나중에 사용할 수 있는 App Service 격리 스탬프 수를 확인하세요.

또한 격리 스탬프에서 Linux 또는 Windows 미터를 내보내는 방법을 이해해야 합니다.

- 기본적으로 비어 있는 격리 스탬프는 Windows 스탬프 미터를 내보냅니다. 예를 들어 배포된 작업자가 없는 경우입니다. Windows 작업자가 스탬프에 배포된 경우에는 계속해서 이 미터를 내보냅니다.
- Linux 작업자를 배포하는 경우 미터가 Linux 스탬프 미터로 변경됩니다.
- Linux 및 Windows 작업자가 모두 배포된 경우 스탬프는 Windows 미터를 내보냅니다.

따라서 스탬프 미터는 스탬프 수명 동안 Windows와 Linux 사이에서 변경될 수 있습니다.

스탬프에 Windows 작업자가 하나 이상 있는 경우 Windows 스탬프 예약을 구매하세요. Linux 스탬프 예약을 구매해야 하는 유일한 경우는 스탬프에 Linux 작업자_만_ 있게 하려는 경우입니다.

## <a name="buy-isolated-stamp-reserved-capacity"></a>격리 스탬프 예약 용량 구매

[Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22AppService%22%7D)에서 격리 스탬프 예약 용량을 구매할 수 있습니다. 예약 요금은 [사전 결제 또는 월별 결제](monthly-payments-reservations.md)로 처리할 수 있습니다. 예약 용량을 구매하려면 종량제 요금이 적용되는 하나 이상의 기업 구독 또는 개별 구독에 대한 소유자 역할이 있어야 합니다.

- Enterprise 구독의 경우 [EA 포털](https://ea.azure.com/)에서 **예약 인스턴스 추가** 옵션을 활성화해야 합니다. 이 설정을 비활성화하려면 EA 관리자여야 합니다.
- CSP(클라우드 솔루션 공급자) 프로그램의 경우 관리 담당자 또는 판매 담당자가 Azure Synapse Analytics 예약 용량을 구매할 수 있습니다.

**구매하려면 다음을 수행합니다.**

1. [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22AppService%22%7D)로 이동합니다.
1. 구독을 선택합니다. 예약 용량의 요금을 지불하는 데 사용되는 구독을 선택하려면 **구독** 목록을 사용하세요. 구독의 결제 방법에는 예약 용량에 대한 비용이 청구됩니다. 구독 유형은 기업계약(제안 번호: MS-AZR-0017P 또는 MS-AZR-0148P) 또는 종량제(제안 번호: MS-AZR-0003P 또는 MS-AZR-0023P) 또는 CSP 구독입니다.
    - Enterprise 구독에 대한 요금은 등록의 금액 약정 잔액에서 차감되거나 초과 비용으로 청구됩니다.
    - 종량제 구독에 대한 요금은 신용 카드 또는 구독 시 선택한 청구서 결제 방법으로 청구됩니다.
1. 구독 범위를 선택하려면 **범위**를 선택합니다.
    - **단일 리소스 그룹 범위** - 선택한 리소스 그룹의 일치하는 리소스에만 예약 할인을 적용합니다.
    - **단일 구독 범위** - 선택한 구독의 일치하는 리소스에만 예약 할인을 적용합니다.
    - **공유 범위** - 청구 컨텍스트에 있는 적격 구독의 일치하는 리소스에 예약 할인을 적용합니다. 기업계약 고객의 경우 청구 컨텍스트는 등록입니다. 종량제 요금이 적용되는 개별 구독의 경우 청구 범위는 계정 관리자가 만든 모든 적격 구독입니다.
1. **지역**을 선택하여 예약 용량에 포함되는 Azure 지역을 선택하고 카트에 예약을 추가합니다.
1. 격리된 계획 유형을 선택한 후 **선택**을 클릭합니다.  
    ![예 ](./media/prepay-app-service-isolated-stamp/app-service-isolated-stamp-select.png)
1. 예약할 App Service 격리 스탬프 수량을 입력합니다. 예를 들어 수량 3은 지역에 예약된 3개의 스탬프를 제공합니다. **다음: 검토 + 구매**를 클릭합니다.
1. 검토하고 **지금 구매**를 클릭합니다.

구매 후에는 [예약](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade)으로 이동하여 구매 상태를 확인하고 언제든지 모니터링할 수 있습니다.

## <a name="cancel-exchange-or-refund-reservations"></a>예약 취소, 교환 또는 환불

예약을 취소, 교환 또는 환불할 수 있지만 몇 가지 제한 사항은 있습니다. 자세한 내용은 [Azure Reservations의 셀프 서비스 교환 및 환불](exchange-and-refund-azure-reservations.md)을 참조하세요.

## <a name="discount-application-shown-in-usage-data"></a>사용량 데이터에 표시되는 할인 애플리케이션

예약 할인이 적용되는 사용량의 경우 사용량 데이터의 유효 가격은 0입니다. 사용량 데이터는 각 예약에서 각 스탬프 인스턴스의 예약 할인을 보여줍니다.

사용량 데이터에 예약 할인이 표시되는 방식에 대한 자세한 내용은 [기업계약 예약 비용 및 사용량 확인](understand-reserved-instance-usage-ea.md)(EA(기업계약) 고객인 경우)을 참조하세요. 그렇지 않은 경우 [종량제 요금을 사용하는 개별 구독에 대한 Azure 예약 사용량 이해](understand-reserved-instance-usage.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

- Azure 예약에 대한 자세한 내용은 다음 문서를 참조하세요.
  - [Azure 예약이란?](save-compute-costs-reservations.md)
  - [Azure App Service 격리 스탬프 예약 할인이 적용되는 방식 이해](reservation-discount-app-service-isolated-stamp.md)
  - [엔터프라이즈 등록에서 예약 사용량 이해](understand-reserved-instance-usage-ea.md)
