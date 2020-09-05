---
title: Azure 예약 용량을 사용하여 Azure Synapse Analytics 요금 절감
description: 예약 용량으로 Azure Synapse Analytics 요금에 대한 비용을 절감하는 방법을 알아봅니다.
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: how-to
ms.date: 07/24/2020
ms.author: banders
ms.openlocfilehash: ca83760f09ea29ba0b21a0f7177dd3f3188c44cd
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88686142"
---
# <a name="save-costs-for-azure-synapse-analytics-charges-with-reserved-capacity"></a>예약 용량을 사용하여 Azure Synapse Analytics 요금에 대한 비용 절감

1년 또는 3년 동안 cDWU 사용량에 대한 예약을 커밋하여 Azure Synapse Analytics 비용을 절약할 수 있습니다. Azure Synapse Analytics 예약 용량을 구매하려면 Azure 지역 및 용어를 선택해야 합니다. 그런 다음, Azure Synapse Analytics SKU를 카트에 추가하고 구매하려는 cDWU 단위의 용량을 선택합니다.

예약을 구매하는 경우 예약 특성과 일치하는 Azure Synapse Analytics 사용에 대해 종량제 요금이 더 이상 청구되지 않습니다.

예약은 Azure Synapse Analytics 사용과 관련된 스토리지 또는 네트워킹 요금은 포함되지 않습니다.

예약 용량이 만료되면 Azure Synapse Analytics 인스턴스가 계속 실행되지만 종량제 요금이 청구됩니다. 예약은 자동으로 갱신되지 않습니다.

가격 책정 정보는 [Azure Synapse Analytics 예약 용량 제품](https://azure.microsoft.com/pricing/details/synapse-analytics/)을 참조하세요.

[Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade)에서 Azure Synapse Analytics 예약 용량을 구매할 수 있습니다. 예약 요금은 [사전 결제 또는 월별 결제](monthly-payments-reservations.md)로 처리할 수 있습니다. 예약 용량을 구입할 수 있는 조건은 다음과 같습니다.

- 하나 이상의 Enterprise 또는 종량제 구독의 소유자 역할이 있어야 합니다.
- Enterprise 구독의 경우 [EA 포털](https://ea.azure.com/)에서 **예약 인스턴스 추가** 옵션을 활성화해야 합니다. 이 설정을 비활성화하려면 EA 관리자여야 합니다.
- CSP(클라우드 솔루션 공급자) 프로그램의 경우 관리 담당자 또는 판매 담당자가 Azure Synapse Analytics 예약 용량을 구매할 수 있습니다.

예약 구매에 대해 엔터프라이즈 고객 및 종량제 고객에게 요금이 청구되는 방법에 대한 자세한 내용은 [엔터프라이즈 등록에서 Azure 예약 사용량 이해](understand-reserved-instance-usage-ea.md) 및 [종량제 구독의 Azure 예약 사용량 이해](understand-reserved-instance-usage.md)를 참조하세요.

## <a name="choose-the-right-size-before-purchase"></a>구매 전 적절한 크기 선택

Azure Synapse Analytics 예약 크기는 사용하는 총 cDWU(컴퓨팅 데이터 웨어하우스 단위)를 기반으로 해야 합니다. 구매는 100 cDWU 증분으로 이루어집니다.

예를 들어 Azure Synapse Analytics의 총 사용량을 DW3000c라고 가정합니다. 모든 항목에 대한 예약된 용량을 구매하려고 합니다. 따라서 cDWU 예약 용량의 30 단위를 구매해야 합니다.

## <a name="buy-azure-synapse-analytics-reserved-capacity"></a>Azure Synapse Analytics 예약 용량 구입

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. **모든 서비스** > **예약**을 선택합니다.
3. 구독을 선택합니다. 예약 용량의 요금을 지불하는 데 사용되는 구독을 선택하려면 구독 목록을 사용합니다. 구독의 결제 방법에는 예약 용량에 대한 비용이 청구됩니다. 구독 유형은 기업계약(제안 번호: MS-AZR-0017P 또는 MS-AZR-0148P) 또는 종량제(제안 번호: MS-AZR-0003P 또는 MS-AZR-0023P)여야 합니다.
   - Enterprise 구독에 대한 요금은 등록의 금액 약정 잔액에서 차감되거나 초과 비용으로 청구됩니다.
   - 종량제 구독에 대한 요금은 신용 카드 또는 구독 시 선택한 청구서 결제 방법으로 청구됩니다.
4. 범위를 선택합니다. 구독 범위를 선택하려면 범위를 사용합니다.
   - **단일 리소스 그룹 범위** - 선택한 리소스 그룹의 일치하는 리소스에만 예약 할인을 적용합니다.
   - **단일 구독 범위** - 선택한 구독의 일치하는 리소스에만 예약 할인을 적용합니다.
   - **공유 범위** - 청구 컨텍스트에 있는 적격 구독의 일치하는 리소스에 예약 할인을 적용합니다. 기업계약 고객의 경우 청구 컨텍스트는 등록입니다. 종량제 요금이 적용되는 개별 구독의 경우 청구 범위는 계정 관리자가 만든 모든 적격 구독입니다.
   - 기업 계약의 경우 청구 컨텍스트는 EA 등록입니다.
   - 종량제 고객의 공유 범위는 계정 관리자가 만든 모든 종량제 구독입니다.
5. 지역을 선택하여 예약 용량에 적용되는 Azure 지역을 선택합니다.
6. 수량을 선택합니다. 구매하려는 100 cDWU(Data Warehouse 단위)의 수량을 입력합니다.    
   예를 들어 수량 30은 1시간마다 3,000 cDWU를 예약 용량으로 제공합니다.
7. **비용** 섹션에서 Azure Synapse Analytics 예약 용량 예약 비용을 검토합니다.
8. **구매**를 선택합니다.
9. 구매 상태를 보려면 **이 예약 보기**를 선택합니다.

## <a name="cancel-exchange-or-refund-reservations"></a>예약 취소, 교환 또는 환불

예약을 취소, 교환 또는 환불할 수 있지만 몇 가지 제한 사항은 있습니다. 자세한 내용은 [Azure Reservations의 셀프 서비스 교환 및 환불](exchange-and-refund-azure-reservations.md)을 참조하세요.

예약 할인은 Azure Synapse Analytics 예약 용량 예약 범위 및 지역과 일치하는 Azure Synapse Analytics 인스턴스의 수에 자동으로 적용됩니다. [Azure Portal](https://portal.azure.com/), PowerShell, CLI 또는 API를 통해 Azure Synapse Analytics 예약 용량의 범위를 업데이트할 수 있습니다.

## <a name="need-help-contact-us"></a>도움 필요 시 문의처

질문이 있거나 도움이 필요한 경우 [지원 요청을 만드세요](https://portal.azure.com/).

## <a name="next-steps"></a>다음 단계

- Azure Synapse Analytics에 예약 할인이 적용되는 방법에 대해 자세히 알아보려면 [Azure Synapse Analytics에 예약 할인이 적용되는 방법](prepay-sql-data-warehouse-charges.md)을 참조하세요.

- Azure 예약에 대한 자세한 내용은 다음 문서를 참조하세요.
  - [Azure 예약이란?](save-compute-costs-reservations.md)
  - [Azure Reservations 관리](manage-reserved-vm-instance.md)
  - [Azure 예약 할인 이해](understand-reservation-charges.md)
  - [종량제 구독의 예약 사용량 이해](understand-reserved-instance-usage.md)
  - [엔터프라이즈 등록에서 예약 사용량 이해](understand-reserved-instance-usage-ea.md)
