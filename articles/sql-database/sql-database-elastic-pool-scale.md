---
title: 탄력적 풀 리소스 크기 조정 - Azure SQL Database | Microsoft Docs
description: 이 페이지에서는 Azure SQL Database에서 탄력적 풀의 리소스 크기를 조정하는 방법을 설명합니다.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: carlrab
manager: craigg
ms.date: 3/14/2019
ms.openlocfilehash: d8aaf51c836a8e88c4e9b92798067167cd044e72
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60848092"
---
# <a name="scale-elastic-pool-resources-in-azure-sql-database"></a>Azure SQL Database에서 탄력적 풀 리소스 크기 조정

이 문서에서는 Azure SQL Database에서 탄력적 풀 및 풀링된 데이터베이스에 사용할 수 있는 계산 및 스토리지 리소스의 크기를 조정하는 방법을 설명합니다.

## <a name="change-compute-resources-vcores-or-dtus"></a>변경 계산 리소스 (Dtu 또는 vcore 수)

Edtu 또는 Vcore 수를 처음 선택한 후 있습니다 수 늘리거나 탄력적 풀을 사용 하 여 실제 환경에 따라 동적으로 [Azure portal](sql-database-elastic-pool-manage.md#azure-portal-manage-elastic-pools-and-pooled-databases)하십시오 [PowerShell](/powershell/module/az.sql/Get-AzSqlElasticPool)의 [Azure CLI ](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-update), 또는 [REST API](https://docs.microsoft.com/rest/api/sql/elasticpools/update)합니다.

### <a name="impact-of-changing-service-tier-or-rescaling-compute-size"></a>서비스 계층 또는 크기 조정 계산 크기를 변경의 영향

서비스 변경 계층 또는 탄력적 풀의 크기 단일 데이터베이스의 경우와 비슷한 패턴을 따르며 주로 다음 단계를 수행 하는 서비스가 포함 되어 계산:

1. 탄력적 풀에 대 한 새 계산 인스턴스 만들기  

    요청 된 서비스 계층 및 계산 크기를 사용 하 여 탄력적 풀에 대 한 새 계산 인스턴스가 만들어집니다. 조합에 대 한 몇 가지 서비스 계층 및 계산 크기 변경, 각 데이터베이스의 복제본 데이터를 복사 하는 새 계산 인스턴스에서 다시 만들어야 하 고 전체 대기 시간에 크게 영향을 미칠 수 있습니다. 그럼에도 불구 하 고 데이터베이스는이 단계 동안 온라인 상태로 유지 하 고 연결을 원래 계산 인스턴스에 있는 데이터베이스에 계속 합니다.

2. 새 계산 인스턴스에 대 한 연결의 라우팅을 전환합니다

    원래 계산 인스턴스의 데이터베이스에 대 한 기존 연결이 삭제 됩니다. 모든 새 연결은 새 계산 인스턴스에 있는 데이터베이스에 설정 됩니다. 조합에 대 한 몇 가지 서비스 계층 및 계산 크기 변경, 데이터베이스 파일이 분리 되어 전환 하는 동안 다시 연결 합니다.  하지만 일반적으로 30 초 보다 작은 한 대개 몇 초만에 대 한 데이터베이스를 사용할 수 없을 때는 스위치 일시적인 서비스 중단 될 수 있습니다. 장기 실행 없으면 연결이 삭제 되 면 실행 트랜잭션의 경우이 단계 동안 중단 된 트랜잭션을 복구 하기 위해 오래 걸릴 수 있습니다. [데이터베이스 복구 accelerated](sql-database-accelerated-database-recovery.md) 장기 실행 트랜잭션 중단의 영향을 줄일 수 있습니다.

> [!IMPORTANT]
> 워크플로의 단계 중 데이터가 손실 됩니다.

### <a name="latency-of-changing-service-tier-or-rescaling-compute-size"></a>서비스 계층 또는 크기 조정 계산 크기를 변경 하는 대기 시간

서비스 계층을 변경 하거나 단일 데이터베이스 또는 탄력적 풀의 계산 크기 기간은 대기 시간은 다음과 같은 매개 변수화 됩니다.

|서비스 계층|기본 단일 데이터베이스</br>표준 (S0-S1)|기본 탄력적 풀</br>표준 (S2-S12) </br>하이퍼 스케일, </br>일반 목적 단일 데이터베이스 또는 탄력적 풀|단일 프리미엄 또는 중요 비즈니스용 데이터베이스 또는 탄력적 풀|
|:---|:---|:---|:---|
|**단일 기본 데이터베이스</br> 표준 (S0-S1)**|&bull; &nbsp;사용 된 공간 관계 없이 상수 시간 대기 시간</br>&bull; &nbsp;일반적으로 보다 작은 5 분|&bull; &nbsp;데이터 복사로 인해 사용 가능한 데이터베이스 공간에 비례하여 대기 시간</br>&bull; &nbsp;일반적으로 보다 작은 사용 공간 GB 당 1 분|&bull; &nbsp;데이터 복사로 인해 사용 가능한 데이터베이스 공간에 비례하여 대기 시간</br>&bull; &nbsp;일반적으로 보다 작은 사용 공간 GB 당 1 분|
|**기본 탄력적 풀 </br>표준 (S2-S12) </br>하이퍼 스케일, </br>범용 단일 데이터베이스 또는 탄력적 풀**|&bull; &nbsp;데이터 복사로 인해 사용 가능한 데이터베이스 공간에 비례하여 대기 시간</br>&bull; &nbsp;일반적으로 보다 작은 사용 공간 GB 당 1 분|&bull; &nbsp;사용 된 공간 관계 없이 상수 시간 대기 시간</br>&bull; &nbsp;일반적으로 보다 작은 5 분|&bull; &nbsp;데이터 복사로 인해 사용 가능한 데이터베이스 공간에 비례하여 대기 시간</br>&bull; &nbsp;일반적으로 보다 작은 사용 공간 GB 당 1 분|
|**단일 프리미엄 또는 중요 비즈니스용 데이터베이스 또는 탄력적 풀**|&bull; &nbsp;데이터 복사로 인해 사용 가능한 데이터베이스 공간에 비례하여 대기 시간</br>&bull; &nbsp;일반적으로 보다 작은 사용 공간 GB 당 1 분|&bull; &nbsp;데이터 복사로 인해 사용 가능한 데이터베이스 공간에 비례하여 대기 시간</br>&bull; &nbsp;일반적으로 보다 작은 사용 공간 GB 당 1 분|&bull; &nbsp;데이터 복사로 인해 사용 가능한 데이터베이스 공간에 비례하여 대기 시간</br>&bull; &nbsp;일반적으로 보다 작은 사용 공간 GB 당 1 분|

> [!NOTE]
>
> - 서비스 계층을 변경 또는 탄력적 풀의 계산 크기를 조정할 경우 풀의 모든 데이터베이스에서 사용 되는 공간의 합계 예상치를 계산 하려면 사용할 해야 합니다.
> - 탄력적 풀에서 데이터베이스를 이동 하는 경우 데이터베이스에서 사용 하는 공간만 대기 시간, 탄력적 풀에서 사용 하는 공간이 아니라 영향을 줍니다.
>
> [!TIP]
> 진행 중인 작업을 모니터링하려면 [Manage operations using the SQL REST API](https://docs.microsoft.com/rest/api/sql/operations/list)(SQL REST API를 사용하여 작업 관리), [Manage operations using CLI](/cli/azure/sql/db/op)(CLI를 사용하여 작업 관리), [Monitor operations using T-SQL](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database)(T-SQL을 사용하여 작업 관리) 및 다음 두 가지 PowerShell 명령, 즉 [Get-AzSqlDatabaseActivity](/powershell/module/az.sql/get-azsqldatabaseactivity) 하 고 [중지 AzSqlDatabaseActivity](/powershell/module/az.sql/stop-azsqldatabaseactivity)합니다.

### <a name="additional-considerations-when-changing-service-tier-or-rescaling-compute-size"></a>변경 시 추가 고려 사항 서비스 계층 또는 크기 조정 계산 크기

- 탄력적 풀의 Edtu 또는 Vcore로 다운 사이징 하는 경우 풀 사용 공간은 대상 서비스 계층 및 풀 Edtu의 크기에 허용 되는 최대값 보다 작아야 합니다.
- Vcore 수 또는 탄력적 풀의 Edtu 크기를 조정할 때 대상 풀에 풀의 저장소 최대 크기 (1)은 지원 되며 (2) 저장소 최대 크기가 대상 풀의 포함 된 저장소 용량을 초과 하는 경우 추가 저장소 비용이 적용 됩니다. 예를 들어 최대 크기가 100GB인 100 eDTU 표준 풀 크기를 50 eDTU 표준 풀로 줄이는 경우 대상 풀이 100GB의 최대 크기를 지원하고 포함된 저장소 크기는 50GB에 불과하므로 추가 저장소 비용이 적용됩니다. 따라서 추가 저장소 크기는 100GB – 50GB = 50GB입니다. 추가 저장소 가격 책정에 대한 자세한 내용은 [SQL Database 가격 책정](https://azure.microsoft.com/pricing/details/sql-database/)을 참조하세요. 실제 사용된 공간의 크기가 포함된 저장소의 크기보다 작은 경우 데이터베이스 최대 크기를 포함된 크기로 줄여 이러한 추가 비용을 방지할 수 있습니다.

### <a name="billing-during-rescaling"></a>크기 조정 중의 요금 청구

사용량 또는 데이터베이스가 한 시간 미만 동안 활성 상태였는지 여부와 관계없이, 해당 시간에 적용된 최고 서비스 계층 + 계산 크기를 사용하여 데이터베이스가 있었던 각 시간에 대해 요금이 청구됩니다. 예를 들어 단일 데이터베이스를 만들고 5분 후 삭제하더라도 청구서에는 데이터베이스 1시간 사용에 대한 요금이 반영됩니다.

## <a name="change-elastic-pool-storage-size"></a>탄력적 풀 스토리지 크기 변경

> [!IMPORTANT]
> 경우에 따라 사용하지 않는 공간을 회수하기 위해 데이터베이스를 축소해야 할 수도 있습니다. 자세한 내용은 [Azure SQL Database의 파일 공간 관리](sql-database-file-space-management.md)를 참조하세요.

### <a name="vcore-based-purchasing-model"></a>vCore 기반 구매 모델

- 저장소는 최대 크기 제한까지 프로비전할 수 있습니다.

  - 표준 또는 범용 서비스 계층의 스토리지 크기는 10GB 단위로 늘리거나 줄이세요.
  - 프리미엄 또는 중요 비즈니스용 서비스 계층의 스토리지 크기는 250GB 단위로 늘리거나 줄이세요.
- 최대 크기를 늘리거나 줄여서 탄력적 풀의 저장소를 프로비전할 수 있습니다.
- 탄력적 풀에 대한 저장소의 가격은 저장소 용량에 해당 서비스 계층의 저장소 단가를 곱한 값입니다. 추가 저장소 가격에 대한 자세한 내용은 [SQL Database 가격 책정](https://azure.microsoft.com/pricing/details/sql-database/)을 참조하세요.

> [!IMPORTANT]
> 경우에 따라 사용하지 않는 공간을 회수하기 위해 데이터베이스를 축소해야 할 수도 있습니다. 자세한 내용은 [Azure SQL Database의 파일 공간 관리](sql-database-file-space-management.md)를 참조하세요.

### <a name="dtu-based-purchasing-model"></a>DTU 기반 구매 모델

- 탄력적 풀에 대한 eDTU 가격에는 특정 크기의 저장소가 추가 비용 없이 포함됩니다. 포함된 용량 외 추가 저장소는 최대 250GB씩 총 1TB이 최대 크기 제한까지 추가 비용을 내고 프로비전할 수 있고 1TB 이상일 경우 256GB씩 프로비전할 수 있습니다. 포함된 스토리지 용량 및 최대 크기 제한에 대한 자세한 내용은 [탄력적 풀: 스토리지 크기 및 계산 크기](sql-database-dtu-resource-limits-elastic-pools.md#elastic-pool-storage-sizes-and-compute-sizes)를 참조하세요.
- 탄력적 풀에 대한 추가 저장소는 [Azure portal](sql-database-elastic-pool-manage.md#azure-portal-manage-elastic-pools-and-pooled-databases), [PowerShell](/powershell/module/az.sql/Get-AzSqlElasticPool), [Azure CLI](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-update) 또는 [REST API](https://docs.microsoft.com/rest/api/sql/elasticpools/update)를 통해 해당 최대 크기를 늘려 프로비전할 수 있습니다.
- 탄력적 풀에 대한 추가 저장소 가격은 추가 저장소 용량에 해당 서비스 계층의 추가 저장소 단가를 곱한 것입니다. 추가 저장소 가격에 대한 자세한 내용은 [SQL Database 가격 책정](https://azure.microsoft.com/pricing/details/sql-database/)을 참조하세요.

> [!IMPORTANT]
> 경우에 따라 사용하지 않는 공간을 회수하기 위해 데이터베이스를 축소해야 할 수도 있습니다. 자세한 내용은 [Azure SQL Database의 파일 공간 관리](sql-database-file-space-management.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

전반적인 리소스 제한은 [SQL Database vCore 기반 리소스 제한 - 탄력적 풀](sql-database-vcore-resource-limits-elastic-pools.md) 및 [SQL Database DTU 기반 리소스 제한 - 탄력적 풀](sql-database-dtu-resource-limits-elastic-pools.md)을 참조하세요.
