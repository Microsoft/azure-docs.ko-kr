---
title: Azure 예약 할인이 적용되는 방식
description: 이 문서는 예약 인스턴스 할인이 일반적으로 어떻게 적용되는지 이해하는 데 도움이 됩니다.
author: bandersmsft
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: conceptual
ms.date: 03/22/2020
ms.author: banders
ms.openlocfilehash: 1c038e896a9f9fc65c3b2201b3e763e966285c56
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88684680"
---
# <a name="how-a-reservation-discount-is-applied"></a>예약 할인이 적용되는 방법

이 문서는 예약 인스턴스 할인이 일반적으로 어떻게 적용되는지 이해하는 데 도움이 됩니다. 예약 할인은 예약 구매 시 선택한 특성과 일치하는 리소스 사용에 적용됩니다. 이러한 특성에는 일치하는 VM, SQL Database, Azure Cosmos DB 또는 기타 리소스가 실행되는 범위가 포함됩니다. 예를 들어 미국 서부 지역의 표준 D2 가상 머신 4개에 예약 할인을 원하는 경우 VM이 실행되고 있는 구독을 선택합니다.

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

- Reserved VM Instances: 예약을 구매할 때 **인스턴스 크기 유연성에 맞게 최적화**를 선택하는 경우 할인 적용 범위는 선택하는 VM 크기에 따라 달라집니다. 같은 크기 시리즈 그룹의 VM(가상 머신) 크기에 예약을 적용할 수 있습니다. 자세한 내용은 [예약 VM 인스턴스를 통해 유동적으로 가상 머신 크기 조정](../../virtual-machines/reserved-vm-instance-size-flexibility.md)을 참조하세요.
- Azure Storage 예약 용량: 표준 Azure Storage 계정에 대해 예약된 용량을 월 100TiB 또는 1PiB 단위로 구매할 수 있습니다. Azure Storage 예약 용량을 지원하는 지역에 대한 자세한 내용은 [블록 Blob 가격 책정](https://azure.microsoft.com/pricing/details/storage/blobs/)을 참조하세요. Azure Storage 예약 용량은 모든 액세스 계층(핫, 쿨 또는 보관)과 모든 복제 구성(LRS, GRS 또는 ZRS)에 사용할 수 있습니다.
- SQL Database 예약 용량: 할인 적용 범위는 선택하는 성능 계층에 따라 달라집니다. 자세한 내용은 [Azure 예약 할인이 적용되는 방식 이해](understand-reservation-charges.md)를 참조하세요.
- Azure Cosmos DB 예약 용량: 할인 적용 범위는 프로비전되는 처리량에 따라 달라집니다. 자세한 내용은 [Azure Cosmos DB 예약 할인이 적용되는 방식 이해](understand-cosmosdb-reservation-charges.md)를 참조하세요.

## <a name="how-discounts-apply-to-specific-azure-services"></a>특정 Azure 서비스에 할인이 적용되는 방법

특정 Azure 서비스에 할인이 적용되는 방법을 알아보려면 다음 문서를 읽어보세요.

- [App Service](reservation-discount-app-service-isolated-stamp.md)
- [Azure Cache for Redis](understand-azure-cache-for-redis-reservation-charges.md)
- [Cosmos DB](understand-cosmosdb-reservation-charges.md)
- [Database for MariaDB](understand-reservation-charges-mariadb.md)
- [Database for MySQL](understand-reservation-charges-mysql.md)
- [Database for PostgreSQL](understand-reservation-charges-postgresql.md)
- [Databricks](reservation-discount-databricks.md)
- [Data Explorer](understand-azure-data-explorer-reservation-charges.md)
- [Dedicated Hosts](billing-understand-dedicated-hosts-reservation-charges.md)
- [Disk Storage](understand-disk-reservations.md)
- [Red Hat Linux Enterprise](understand-rhel-reservation-charges.md)
- [소프트웨어 플랜](understand-suse-reservation-charges.md)
- [스토리지](understand-storage-charges.md)
- [SQL 데이터베이스](understand-reservation-charges.md)
- [Azure Synapse Analytics](reservation-discount-azure-sql-dw.md)
- [가상 머신](../manage/understand-vm-reservation-charges.md)


## <a name="next-steps"></a>다음 단계

- [Azure Reservations 관리](manage-reserved-vm-instance.md)
- [종량제 요금을 사용하는 구독의 예약 사용량 이해](understand-reserved-instance-usage.md)
- [엔터프라이즈 등록에서 예약 사용량 이해](understand-reserved-instance-usage-ea.md)
- [예약에 포함되지 않는 Windows 소프트웨어 비용](reserved-instance-windows-software-costs.md)