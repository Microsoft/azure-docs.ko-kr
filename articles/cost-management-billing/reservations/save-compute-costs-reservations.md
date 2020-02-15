---
title: Azure Reservations란?
description: 가상 머신, SQL Database, Azure Cosmos DB 및 기타 리소스 비용을 절약하기 위한 Azure 예약 및 가격 책정 정보에 대해 알아봅니다.
author: yashesvi
manager: yashar
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 11/22/2019
ms.author: banders
ms.openlocfilehash: 10cf5b5a0ec4edda3d3c868cda34352e827604b0
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/31/2020
ms.locfileid: "76841883"
---
# <a name="what-are-azure-reservations"></a>Azure Reservations란?

Azure Reservations는 가상 머신, Azure Blob 스토리지 또는 Azure Data Lake Storage Gen2, SQL Database 컴퓨팅 용량, Azure Disk Storage, Azure Cosmos DB 처리량 또는 기타 Azure 리소스에 대한 1년 또는 3년 요금제를 약정하여 비용을 절감시킬 수 있습니다. 약정하면 사용하는 리소스 요금에 대한 할인을 받을 수 있습니다. 예약을 사용하면 리소스 비용이 종량제 가격의 최대 72%까지 줄일 수 있습니다. 예약은 청구 할인을 제공하며, 리소스의 런타임 상태에 영향을 주지 않습니다.

예약 요금을 선결제할 수도 있고 매달 결제할 수도 있습니다. 선불과 월별 예약의 총 비용은 동일하며, 매달 지불하기로 선택하면 추가 요금이 청구되지 않습니다. 월간 결제는 타사 제품이 아닌 Azure 예약에 사용할 수 있습니다.

예약은 [Azure Portal](https://ms.portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=Reservations&Microsoft_Azure_Reservations=true#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade)에서 구입할 수 있습니다.

## <a name="why-buy-a-reservation"></a>예약을 구입하는 이유

많은 용량 또는 처리량을 사용하거나 장기간 실행되는 가상 머신, Blob 스토리지 데이터, Azure Cosmos DB 또는 SQL Database가 있는 경우 예약을 구매하면 가장 비용 효율적인 가격이 제공됩니다. 예를 들어 예약 없이 한 서비스의 인스턴스 4개를 지속적으로 실행하는 경우 종량제 요금이 청구됩니다. 이러한 리소스에 대한 예약을 구매하면 즉시 예약 할인을 받습니다. 리소스 요금이 더 이상 종량제로 청구되지 않습니다.

## <a name="charges-covered-by-reservation"></a>예약에 포함되는 요금

서비스 플랜:

- **예약 Virtual Machine 인스턴스** - 예약에 가상 머신 컴퓨팅 비용만 포함됩니다. 추가 소프트웨어, 네트워킹 또는 스토리지 요금은 포함되지 않습니다.
- **Azure Storage 예약 용량** - 예약에는 Blob 스토리지 또는 Azure Data Lake Gen2 스토리지의 표준 스토리지 계정에 대한 스토리지 용량이 포함됩니다. 예약에는 대역폭 또는 트랜잭션 요금이 포함되지 않습니다.
- **Azure Disk Storage 예약** - 예약은 P30 크기 이상의 프리미엄 SSD에만 적용됩니다. P30보다 작은 디스크 유형이나 크기는 다루지 않습니다.
- **Azure Cosmos DB 예약 용량** - 리소스에 프로비저닝된 처리량이 포함됩니다. 스토리지 및 네트워킹 요금은 포함하지 않습니다.
- **SQL Database 예약 vCore** - 컴퓨팅 비용만 예약에 포함됩니다. 라이선스는 별도로 청구됩니다.
- **SQL Data Warehouse** - 예약에 cDWU 사용이 포함됩니다. SQL Data Warehouse 사용과 관련된 스토리지 또는 네트워킹 요금은 포함되지 않습니다.
- **App Service 스탬프 요금** - 예약에 스탬프 사용이 포함됩니다. 예약은 작업자에게 적용되지 않으므로 스탬프와 연결된 다른 모든 리소스에는 별도의 요금이 청구됩니다.
- Azure Database for MySQL
- Azure Database for PostgreSQL
- Azure Database for MariaDB
- Azure Data Explorer


Windows 가상 머신 및 SQL Database의 경우 [Azure 하이브리드 혜택](https://azure.microsoft.com/pricing/hybrid-benefit/)으로 라이선스 비용을 처리할 수 있습니다.

## <a name="whos-eligible-to-purchase-a-reservation"></a>예약을 구매할 수 있는 주체는?

플랜을 구매하려면 엔터프라이즈(MS-AZR-0017P 또는 MS-AZR-0148P), 종량제 구독(MS-AZR-0003P 또는 MS-AZR-0023P) 또는 Microsoft 고객 계약 구독의 구독 소유자 역할이 있어야 합니다. 클라우드 솔루션 공급자는 Azure Portal 또는  [파트너 센터](/partner-center/azure-reservations) 를 사용하여 Azure 예약을 구매할 수 있습니다.

EA(기업계약) 고객은 EA Portal에서 **예약 인스턴스 추가** 옵션을 해제하여 EA 관리자만 구매 가능하도록 제한할 수 있습니다. 예약을 구매하려면 EA 관리자가 하나 이상의 EA 구독에 대한 구독 소유자여야 합니다. 이 옵션은 중앙의 팀이 다양한 비용 센터의 예약을 대신 구매하도록 하려는 기업에 유용합니다. 구매 후에는 중앙의 팀이 예약에 비용 센터 소유자를 추가할 수 있습니다. 그러면 소유자는 예약의 범위를 구독으로 지정할 수 있습니다. 중앙의 팀은 예약을 구매하는 구독 소유자 액세스 권한이 필요 없습니다.

예약 할인은 엔터프라이즈, CSP(클라우드 솔루션 공급자), Microsoft 고객 계약 및 종량제 요금을 사용하는 개별 플랜을 통해 구매한 구독과 연결된 리소스에만 적용됩니다.

## <a name="scope-reservations"></a>예약 범위 지정

예약의 범위를 구독 또는 리소스 그룹으로 지정할 수 있습니다. 예약의 범위를 지정하면 예약 할인이 적용되는 위치가 선택됩니다. 예약의 범위를 리소스 그룹으로 지정하면 구독 전체가 아닌 리소스 그룹에만 예약 할인이 적용됩니다.

### <a name="reservation-scoping-options"></a>예약 범위 지정 옵션

리소스 그룹 범위를 사용하는 경우 요구 사항에 따라 예약 범위를 지정하는 세 가지 옵션이 있습니다.

- **단일 리소스 그룹 범위** - 선택한 리소스 그룹의 일치하는 리소스에만 예약 할인을 적용합니다.
- **단일 구독 범위** - 선택한 구독의 일치하는 리소스에만 예약 할인을 적용합니다.
- **공유 범위** - 청구 컨텍스트에 있는 적격 구독의 일치하는 리소스에 예약 할인을 적용합니다. 기업계약 고객의 경우 청구 컨텍스트는 등록입니다. Microsoft 고객 계약 고객의 경우 청구 범위는 청구 프로필입니다. 종량제 요금이 적용되는 개별 구독의 경우 청구 범위는 계정 관리자가 만든 모든 적격 구독입니다.

사용 요금에 예약 할인을 적용할 때 Azure는 다음과 같은 순서로 예약을 처리합니다.

1. 리소스 그룹으로 범위가 지정된 예약
2. 단일 범위 예약
3. 공유 범위 예약

단일 리소스 그룹은 예약의 범위를 지정하는 방법에 따라 여러 예약의 예약 할인을 받을 수 있습니다.

### <a name="scope-a-reservation-to-a-resource-group"></a>예약 범위를 리소스 그룹으로 지정

예약을 구매할 때 예약 범위를 리소스 그룹으로 지정할 수도 있고 구매 후에 범위를 설정할 수도 있습니다. 예약 범위를 리소스 그룹으로 지정하려면 구독 소유자여야 합니다.

범위를 설정하려면 Azure Portal에서 [예약 구매](https://ms.portal.azure.com/#blade/Microsoft\_Azure\_Reservations/CreateBlade/referrer/Browse\_AddCommand) 페이지로 이동합니다. 구매할 예약 유형을 선택합니다. **구매할 제품 선택** 양식에서 범위 값을 단일 리소스 그룹으로 변경합니다. 그런 다음, 리소스 그룹을 선택합니다.

![VM 예약 구매 옵션을 보여주는 예제](./media/save-compute-costs-reservations/select-product-to-purchase.png)

가상 머신 예약의 리소스 그룹에 대한 구매 추천이 표시됩니다. 추천은 최근 30일의 사용량을 분석하여 계산됩니다. 예약 인스턴스로 리소스를 실행하는 비용이 종량제 요금으로 리소스를 실행하는 비용보다 저렴하면 구매 추천이 작성됩니다. 예약 구매 추천에 대한 자세한 내용은 [사용량 패턴에 따라 예약 인스턴스 구매 추천 가져오기](https://azure.microsoft.com/blog/get-usage-based-reserved-instance-recommendations)를 참조하세요.

예약을 구매한 후 언제든지 범위를 업데이트할 수 있습니다. 업데이트하려면 예약으로 이동하고, **구성**을 클릭하여 예약 범위를 다시 지정합니다. 예약 범위 재지정은 상거래 트랜잭션이 아닙니다. 따라서 예약 기간이 변경되지 않았습니다. 범위 업데이트에 대한 자세한 내용은 [예약을 구매한 후 범위 업데이트](manage-reserved-vm-instance.md#change-the-reservation-scope)를 참조하세요.

![예약 범위 변경을 보여주는 예제](./media/save-compute-costs-reservations/rescope-reservation-resource-group.png)

### <a name="monitor-and-optimize-reservation-usage"></a>예약 사용량 모니터링 및 최적화

여러 가지 방법으로(Azure Portal, API 또는 사용량 데이터를 통해) 예약 사용량을 모니터링할 수 있습니다. 액세스 권한이 있는 모든 예약을 보려면 Azure Portal에서 **예약**으로 이동합니다. 예약 그리드는 예약에 대해 마지막으로 기록된 사용률 비율을 보여줍니다. 예약을 클릭하면 예약의 장기 사용률이 표시됩니다.

또한 기업 계약 또는 Microsoft 고객 계약 고객의 경우 [API](reservation-apis.md#see-reservation-usage) 및 [사용량 데이터](understand-reserved-instance-usage-ea.md#common-cost-and-usage-tasks)를 통해 예약 사용률을 확인할 수 있습니다.

범위가 리소스 그룹으로 지정된 예약의 사용률이 낮은 것으로 확인되는 경우 예약 범위를 단일 구독으로 업데이트하거나 청구 컨텍스트에서 공유할 수 있습니다. 예약을 분할하고 그 결과로 얻은 예약을 여러 리소스 그룹에 적용할 수도 있습니다.

### <a name="other-considerations"></a>기타 고려 사항

리소스 그룹에 일치하는 리소스가 없는 경우 예약 사용률이 낮아집니다. 예약은 사용률이 낮은 다른 리소스 그룹 또는 구독에 자동으로 적용되지 않습니다.

리소스 그룹을 한 구독에서 다른 구독으로 이동하는 경우 예약 범위가 자동으로 업데이트되지 않습니다. 리소스 그룹을 삭제하면 범위가 업데이트되지 않습니다. [예약 범위를 다시 지정](manage-reserved-vm-instance.md#change-the-reservation-scope)해야 합니다. 그렇지 않으면 예약 사용률이 낮아집니다.

## <a name="discounted-subscription-and-offer-types"></a>할인된 구독 및 제안 유형

예약 할인은 다음과 같은 적격 구독 및 제품 유형에 적용됩니다.

- 기업계약(제품 번호: MS-AZR-0017P 또는 MS-AZR-0148P)
- Microsoft 고객 계약 구독.
- 종량제 요금을 사용한 개별 계획(제품 번호: MS-AZR-0003P 또는 MS-AZR-0023P)
- CSP 구독

다른 유형의 구독에서 실행되는 리소스는 예약 할인을 받지 못합니다.

## <a name="how-is-a-reservation-billed"></a>예약 요금은 어떻게 청구되나요?

예약 요금은 구독에 연결된 결제 방법으로 청구됩니다. 가능한 경우, 예액 비용은 현금 약정 잔액에서 차감됩니다. 현금 약정 잔액으로 예약 비용이 해결되지 않는 경우 초과분에 대한 요금이 청구됩니다. 종량제 요금을 사용하는 개별 플랜의 구독이 있는 경우 계정에 등록된 신용 카드로 선결제 요금이 즉시 청구됩니다. 월간 결제 금액이 청구서에 표시되며 매월 신용 카드로 요금이 청구됩니다. 요금 청구를 청구서로 받는 경우 다음 달 청구서에 요금이 표시됩니다.

## <a name="how-reservation-discount-is-applied"></a>예약 할인이 적용되는 방법

예약 할인은 예약 구매 시 선택한 특성과 일치하는 리소스 사용에 적용됩니다. 이러한 특성에는 일치하는 VM, SQL Database, Azure Cosmos DB 또는 기타 리소스가 실행되는 범위가 포함됩니다. 예를 들어 미국 서부 지역의 표준 D2 가상 머신 4개에 예약 할인을 원하는 경우 VM이 실행되고 있는 구독을 선택합니다.

예약 할인은 "*use-it-or-lose-it*" 방식입니다. 특정 시점에 일치하는 리소스가 없으면 해당 시간의 예약 수량이 사라집니다. 사용하지 않는 예약 시간은 이월할 수 없습니다.

리소스를 종료할 때 예약 할인이 지정된 범위에서 일치하는 다른 리소스에 자동으로 적용됩니다. 지정된 범위에서 일치하는 리소스를 찾을 수 없는 경우 예약된 시간이 *사라집니다*.

예를 들어 나중에 리소스를 만들었는데 일치하는 예약의 사용률이 낮다고 가정해 봅시다. 이 경우 일치하는 새 리소스에 예약 할인이 자동으로 적용됩니다.

가상 머신이 사용자의 등록/계정 내 다른 구독에서 실행되는 경우는 범위를 공유로 선택합니다. 공유 범위를 사용하면 예약 할인을 구독 전체에 적용할 수 있습니다. 예약 구매 후 범위를 변경할 수 있습니다. 자세한 내용은 [Azure 예약 관리](manage-reserved-vm-instance.md)를 참조하세요.

예약 할인은 Enterprise, Microsoft 고객 계약, CSP 또는 종량제를 사용하는 구독과 연결된 리소스에만 적용됩니다. 다른 유형의 구독에서 실행되는 리소스는 예약 할인을 받지 못합니다.

## <a name="when-the-reservation-term-expires"></a>예약 기간이 만료되는 경우

예약 기간이 끝나면 청구 할인이 만료되고 리소스는 종량제 요금으로 청구됩니다. 기본적으로 예약은 자동 갱신되도록 설정되지 않습니다. 갱신 설정에서 옵션을 선택하여 예약 자동 갱신을 사용하도록 선택할 수 있습니다. 자동 갱신을 사용하면 기존 예약이 만료될 때 교체 예약이 구매됩니다. 기본적으로 교체 예약은 만료 예약과 동일한 특성을 가지며 필요에 따라 갱신 설정에서 대금 청구 주기, 기간 또는 수량을 변경할 수 있습니다. 청구에 사용된 구독 및 예약에 대한 소유자 액세스 권한이 있는 모든 사용자는 갱신을 설정할 수 있습니다.  

## <a name="discount-applies-to-different-sizes"></a>여러 크기에 할인 적용

예약 구매 시에는 동일 크기 그룹 내의 특성이 적용되는 다른 인스턴스에도 할인을 적용할 수 있습니다. 이 기능을 인스턴스 크기 유연성이라고 합니다. 할인 유동 적용 여부는 예약 구매 시 선택하는 특성과 예약의 유형에 따라 달라집니다.

서비스 플랜:

- Reserved VM Instances: 예약을 구매할 때 **인스턴스 크기 유연성에 맞게 최적화**를 선택하는 경우 할인 적용 범위는 선택하는 VM 크기에 따라 달라집니다. 같은 크기 시리즈 그룹의 VM(가상 머신) 크기에 예약을 적용할 수 있습니다. 자세한 내용은 [예약 VM 인스턴스를 통해 유동적으로 가상 머신 크기 조정](../../virtual-machines/windows/reserved-vm-instance-size-flexibility.md)을 참조하세요.
- Azure Storage 예약 용량: 표준 Azure Storage 계정에 대해 예약된 용량을 월 100TiB 또는 1PiB 단위로 구매할 수 있습니다. Azure Storage 예약 용량은 모든 액세스 계층(핫, 쿨 또는 보관) 및 모든 복제 옵션(LRS, GRS 또는 ZRS)에 대해 모든 지역에서 사용할 수 있습니다.
- SQL Database 예약 용량: 할인 적용 범위는 선택하는 성능 계층에 따라 달라집니다. 자세한 내용은 [Azure 예약 할인이 적용되는 방식 이해](understand-reservation-charges.md)를 참조하세요.
- Azure Cosmos DB 예약 용량: 할인 적용 범위는 프로비전되는 처리량에 따라 달라집니다. 자세한 내용은 [Azure Cosmos DB 예약 할인이 적용되는 방식 이해](understand-cosmosdb-reservation-charges.md)를 참조하세요.

## <a name="reservation-notifications"></a>예약 알림

Azure 구독 요금을 결제하는 방법에 따라 조직의 다음 사용자에게 이메일로 예약 알림을 보내드립니다. 다음을 비롯한 다양한 이벤트에 대한 알림이 전송됩니다.

- Purchase
- 곧 만료되는 예약
- Expiry
- 갱신
- 취소
- 범위 변경

EA 구독을 사용하는 고객의 경우:
- 구매 알림이 구매자 및 EA 알림 연락처로 전달됩니다.
- 기타 예약 수명 주기 알림은 EA 알림 연락처로만 전달됩니다.
- RBAC(IAM) 권한을 사용하여 예약에 추가된 사용자에게는 이메일 알림이 전달되지 않습니다.

개별 구독을 사용하는 고객의 경우:
- 구매자에게 구매 알림이 전달됩니다.
- 구매 시 구독 청구 계정 소유자에게 구매 알림이 전달됩니다.
- 그 외의 모든 알림은 계정 소유자에게 전달됩니다.


## <a name="need-help-contact-us"></a>도움 필요 시 문의하세요.

질문이 있거나 도움이 필요한 경우 [지원 요청을 만드세요](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>다음 단계

- Azure 예약에 대한 자세한 내용은 다음 문서를 참조하세요.
    - [Azure Reservations 관리](manage-reserved-vm-instance.md)
    - [종량제 요금을 사용하는 구독의 예약 사용량 이해](understand-reserved-instance-usage.md)
    - [엔터프라이즈 등록에서 예약 사용량 이해](understand-reserved-instance-usage-ea.md)
    - [예약에 포함되지 않는 Windows 소프트웨어 비용](reserved-instance-windows-software-costs.md)
    - [파트너 센터 CSP(클라우드 솔루션 공급자) 프로그램의 Azure 예약](/partner-center/azure-reservations)

- 다음과 같은 서비스 플랜 예약에 대해 자세히 알아보세요.
    - [Azure Reserved VM Instances를 사용하는 Virtual Machines](../../virtual-machines/windows/prepay-reserved-vm-instances.md)
    - [Azure Cosmos DB 예약 용량을 사용하는 Azure Cosmos DB 리소스](../../cosmos-db/cosmos-db-reserved-capacity.md)
    - [Azure SQL Database 예약 용량을 사용하는 SQL Database 컴퓨팅 리소스](../../sql-database/sql-database-reserved-capacity.md) 다음과 같은 소프트웨어 플랜 예약에 대해 자세히 알아보세요.
    - [Azure 예약의 Red Hat 소프트웨어 플랜](../../virtual-machines/linux/prepay-rhel-software-charges.md)
    - [Azure 예약의 SUSE 소프트웨어 플랜](../../virtual-machines/linux/prepay-suse-software-charges.md)
