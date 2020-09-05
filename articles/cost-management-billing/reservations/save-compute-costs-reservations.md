---
title: Azure Reservations란?
description: 가상 머신, SQL Database, Azure Cosmos DB 및 기타 리소스 비용을 절약하기 위한 Azure 예약 및 가격 책정 정보에 대해 알아봅니다.
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: overview
ms.date: 08/04/2020
ms.author: banders
ms.openlocfilehash: a5ab489e995506deb806fe5f0cee4a147ae42ac2
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88682589"
---
# <a name="what-are-azure-reservations"></a>Azure Reservations란?

Azure Reservations는 여러 제품의 1년 또는 3년 플랜에 따라 비용을 절감하는 데 도움이 됩니다. 약정하면 사용하는 리소스 요금에 대한 할인을 받을 수 있습니다. 예약을 사용하면 리소스 비용이 종량제 가격의 최대 72%까지 줄일 수 있습니다. 예약은 청구 할인을 제공하며, 리소스의 런타임 상태에 영향을 주지 않습니다. 예약을 구매한 후 일치하는 리소스에 할인이 자동으로 적용됩니다.

예약 요금을 선결제할 수도 있고 매달 결제할 수도 있습니다. 선불과 월별 예약의 총 비용은 동일하며, 매달 지불하기로 선택하면 추가 요금이 청구되지 않습니다. 월간 결제는 타사 제품이 아닌 Azure 예약에 사용할 수 있습니다.

예약은 [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade)에서 구입할 수 있습니다.

## <a name="why-buy-a-reservation"></a>예약을 구입하는 이유

예약을 지원하는 일관된 리소스 사용량이 있는 경우 예약을 구입하면 비용을 절감할 수 있는 옵션이 제공됩니다. 예를 들어 예약 없이 서비스의 인스턴스를 지속적으로 실행하는 경우 종량제 요금이 청구됩니다. 예약을 구매하면 즉시 예약 할인을 받습니다. 리소스 요금이 더 이상 종량제로 청구되지 않습니다.

## <a name="how-reservation-discount-is-applied"></a>예약 할인이 적용되는 방법

구매 후, 예약 할인은 예약 구매 시 선택한 특성과 일치하는 리소스 사용에 자동으로 적용됩니다. 특성에는 SKU, 지역(해당 하는 경우) 및 범위가 포함됩니다. 예약 범위는 예약 절감액이 적용될 위치를 선택합니다.

할인이 적용되는 방법에 대한 자세한 내용은 [예약 인스턴스 할인 애플리케이션](reservation-discount-application.md)을 참조하세요.

예약 범위의 작동 방식에 대한 자세한 내용은 [예약 범위 지정](prepare-buy-reservation.md#scope-reservations)을 참조하세요.

## <a name="determine-what-to-purchase"></a>구매할 항목 확인 

Azure Databricks를 제외한 모든 예약은 매시간 적용됩니다. 일관된 기본 사용량에 따라 예약 구매를 고려합니다. 사용량 데이터를 분석하거나 예약 권장 사항을 참고하여 구매할 예약을 결정할 수 있습니다. 권장 사항을 볼 수 있는 곳은 다음과 같습니다.

- Azure Advisor(VM만 해당)
- Azure Portal의 예약 구매 환경
- Cost Management Power BI 앱
- API 

자세한 내용은  [구매할 예약 결정](determine-reservation-purchase.md)을 참조하세요. 

## <a name="buying-a-reservation"></a>예약 구매 

Azure Portal, API, PowerShell 및 CLI에서 예약을 구매할 수 있습니다. 

구매하려면 Azure Portal(https://ms.portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/Docs) 로 이동 

자세한 내용은  [예약 구매](prepare-buy-reservation.md)를 참조하세요. 

## <a name="how-is-a-reservation-billed"></a>예약 요금은 어떻게 청구되나요? 

예약 요금은 구독에 연결된 결제 방법으로 청구됩니다. 가능한 경우, 예액 비용은 현금 약정 잔액에서 차감됩니다. 현금 약정 잔액으로 예약 비용이 해결되지 않는 경우 초과분에 대한 요금이 청구됩니다. 종량제 요금을 사용하는 개별 플랜의 구독이 있는 경우 계정에 등록된 신용 카드로 선결제 요금이 즉시 청구됩니다. 월간 결제 금액이 청구서에 표시되며 매월 신용 카드로 요금이 청구됩니다. 요금 청구를 청구서로 받는 경우 다음 달 청구서에 요금이 표시됩니다. 

## <a name="permissions-to-view-and-manage-reservations"></a>예약 보기 및 관리 권한 할당 

예약을 구매한 사용자와 예약 대금 청구에 사용된 구독의 계정 관리자에게는 예약 주문 및 예약에 대한 소유자 역할이 부여됩니다.

예약 주문 또는 예약에 대한 역할에 사용자를 추가하여 예약 관리를 위임할 수 있습니다. Azure Portal 또는 API와 PowerShell을 사용하여 역할을 할당합니다. 

자세한 내용은  [예약을 관리할 수 있는 사용자 추가 또는 변경](manage-reserved-vm-instance.md#add-or-change-users-who-can-manage-a-reservation)을 참조하세요. 

## <a name="get-reservation-details-and-utilization-after-purchase"></a>구매 후 예약 세부 정보 및 사용률 가져오기

예약을 볼 권한이 있는 경우 Azure Portal에서 예약과 예약 사용량을 볼 수 있습니다. API를 사용하여 데이터를 가져올 수도 있습니다. 

Azure Portal에서 예약을 확인하는 방법에 대한 자세한 내용은  [Azure Portal에서 예약 보기](view-reservations.md)를 참조하세요. 

## <a name="manage-reservations-after-purchase"></a>구매 후 예약 관리 

Azure 예약을 구매한 후 범위를 업데이트하여 다른 구독에 예약을 적용하거나, 예약을 관리할 수 있는 사용자를 변경하거나, 예약을 작게 나누거나, 인스턴스 크기 유연성을 변경할 수 있습니다. 

자세한 내용은  [Azure 리소스에 대한 예약 관리](manage-reserved-vm-instance.md)를 참조하세요. 

## <a name="flexibility-with-azure-reservations"></a>Azure 예약의 유연성

Azure Reservations는 진화하는 요구 사항을 충족하는 데 유용한 유연성을 제공합니다. 예약을 동일한 유형의 다른 예약으로 교환할 수 있습니다. 예약이 더 이상 필요 없는 경우 12개월 롤링 기간에 $50,000 USD까지 예약을 환불할 수 있습니다. 환불의 최대 한도는 Microsoft와의 계약 범위 내의 모든 예약에 적용됩니다.

자세한 내용은 [Azure Reservations의 셀프 서비스 교환 및 환불](exchange-and-refund-azure-reservations.md)을 참조하세요.


## <a name="charges-covered-by-reservation"></a>예약에 포함되는 요금

- **예약 Virtual Machine 인스턴스** - 예약에는 가상 머신 및 클라우드 서비스 컴퓨팅 비용만 포함됩니다. 추가 소프트웨어, Windows, 네트워킹 또는 스토리지 요금은 포함되지 않습니다.
- **Azure Storage 예약 용량** - 예약에는 Blob 스토리지 또는 Azure Data Lake Gen2 스토리지의 표준 스토리지 계정에 대한 스토리지 용량이 포함됩니다. 예약에는 대역폭 또는 트랜잭션 요금이 포함되지 않습니다.
- **Azure Cosmos DB 예약 용량** - 리소스에 프로비저닝된 처리량이 포함됩니다. 스토리지 및 네트워킹 요금은 포함하지 않습니다.
- **SQL Database 예약 vCore** - 컴퓨팅 비용만 예약에 포함됩니다. SQL 라이선스는 별도로 청구됩니다.
- **Azure Synapse Analytics** - 예약에는 cDWU 사용이 포함됩니다. Azure Synapse Analytics 사용과 관련된 스토리지 또는 네트워킹 요금은 포함되지 않습니다.
- **Azure Databricks** - 예약에는 DBU 사용량만 포함됩니다. 컴퓨팅, 스토리지 및 네트워킹과 같은 기타 요금은 별도로 적용됩니다.
- **App Service 스탬프 요금** - 예약에 스탬프 사용이 포함됩니다. 예약은 작업자에게 적용되지 않으므로 스탬프와 연결된 다른 모든 리소스에는 별도의 요금이 청구됩니다.
- **Azure Database for MySQL** - 컴퓨팅 비용만 예약에 포함됩니다. 예약에는 MySQL Database 서버와 연결된 소프트웨어, 네트워킹 또는 스토리지 요금이 포함되지 않습니다.
- **Azure Database for PostgreSQL** - 컴퓨팅 비용만 예약에 포함됩니다. 예약에는 PostgreSQL Database 서버와 연결된 소프트웨어, 네트워킹 또는 스토리지 요금이 포함되지 않습니다.
- **Azure Database for MariaDB** - 컴퓨팅 비용만 예약에 포함됩니다. 예약에는 MariaDB Database 서버와 연결된 소프트웨어, 네트워킹 또는 스토리지 요금이 포함되지 않습니다.
- **Azure Data Explorer** - 예약에는 태그 요금이 포함됩니다. 클러스터와 연결된 컴퓨팅, 네트워킹 또는 스토리지 요금에는 예약이 적용되지 않습니다.
- **Azure Cache for Redis** - 컴퓨팅 비용만 예약에 포함됩니다. 예약에는 Redis 캐시 인스턴스와 연결된 네트워킹 또는 스토리지 요금이 포함되지 않습니다.
- **Azure Dedicated Host** - 컴퓨팅 비용만 전용 호스트에 포함됩니다.
- **Azure Disk Storage 예약** - 예약은 P30 크기 이상의 프리미엄 SSD에만 적용됩니다. P30보다 작은 다른 디스크 유형이나 크기는 다루지 않습니다.

소프트웨어 플랜:

- **SUSE Linux** - 예약에는 소프트웨어 계획 비용이 포함됩니다. 할인은 SUSE 미터에만 적용되며 가상 머신 사용량에는 적용되지 않습니다.
- **Red Hat 플랜** - 예약에는 소프트웨어 계획 비용이 포함됩니다. 할인은 RedHat 미터에만 적용되며 가상 머신 사용량에는 적용되지 않습니다.
- **Azure VMware Solution by CloudSimple** - 예약에는 VMWare CloudSimple 노드가 포함됩니다. 추가 소프트웨어 비용이 여전히 적용됩니다.
- **Azure Red Hat OpenShift** - 예약은 Azure 인프라 비용이 아닌 OpenShift 비용에 적용됩니다.

Windows 가상 머신 및 SQL Database의 경우, 예약 할인은 소프트웨어 비용에 적용되지 않습니다. [Azure 하이브리드 혜택](https://azure.microsoft.com/pricing/hybrid-benefit/)을 사용하여 라이선스 비용을 처리할 수 있습니다.

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
    - [Azure SQL Database 예약 용량을 사용하는 SQL Database 컴퓨팅 리소스](../../azure-sql/database/reserved-capacity-overview.md)
    - [Azure Cache for Redis 예약 용량을 사용하는 Azure Cache for Redis 리소스](../../azure-cache-for-redis/cache-reserved-pricing.md) 다음과 같은 소프트웨어 플랜 예약에 대해 자세히 알아보세요.
    - [Azure 예약의 Red Hat 소프트웨어 플랜](../../virtual-machines/linux/prepay-rhel-software-charges.md)
    - [Azure 예약의 SUSE 소프트웨어 플랜](../../virtual-machines/linux/prepay-suse-software-charges.md)
