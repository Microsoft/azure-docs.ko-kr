---
title: Azure SQL Database - 범용 및 중요 비즈니스용 | Microsoft Docs
description: 이 문서에서는 vCore 구매 모델의 범용 및 중요 비즈니스용 서비스 계층에 대해 설명합니다.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake, carlrab
manager: craigg
ms.date: 02/23/2019
ms.openlocfilehash: 6a027e6c0afd526139484240f288205fa946d557
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62111802"
---
# <a name="azure-sql-database-service-tiers"></a>Azure SQL Database 서비스 계층

Azure SQL Database는 인프라 오류의 경우에도 99.99%의 가용성을 보장하기 위해 클라우드 환경에 대해 조정되는 SQL Server 데이터베이스 엔진 아키텍처를 기반으로 합니다. Azure SQL Database에 사용되는 세 가지 아키텍처 모델이 있습니다.

- [범용](sql-database-service-tier-general-purpose.md): 대부분의 일반 워크로드용으로 디자인되었습니다.
- [중요 비즈니스용](sql-database-service-tier-business-critical.md): 읽기 가능한 단일 복제본을 포함하는 대기 시간이 짧은 워크로드용으로 디자인되었습니다.
- [하이퍼스케일](sql-database-service-tier-hyperscale.md): 여러 개의 읽기 가능한 복제본을 포함하는 초대형 데이터베이스(최대 100TB)용으로 디자인되었습니다.

이 문서에서는 vCore 기반 구매 모델의 범용 및 중요 비즈니스용 서비스 계층에 대한 저장소 및 백업 고려 사항을 설명합니다.

> [!NOTE]
> vCore 기반 구매 모델의 하이퍼스케일 서비스 계층에 대한 자세한 내용은 [하이퍼스케일 서비스 계층](sql-database-service-tier-hyperscale.md)을 참조하세요. vCore 기반 구매 모델과 DTU 기반 구매 모델의 비교는 [Azure SQL Database 구매 모델 및 리소스](sql-database-purchase-models.md)를 참조하세요.

## <a name="data-and-log-storage"></a>데이터 및 로그 저장

다음을 고려해 보세요.

- 할당된 저장소는 데이터 파일(MDF) 및 로그 파일(LDF) 파일에서 사용됩니다.
- 각 단일 데이터베이스 계산 크기는 최대 데이터베이스 크기를 지원하며 기본 최대 크기는 32GB입니다.
- 필요한 싱글 데이터베이스 크기(MDF 크기)를 구성하면 추가 저장소의 30%가 LDF를 지원하도록 자동으로 추가됩니다
- Managed Instance의 저장소 크기는 32GB의 배수로 지정해야 합니다.
- 단일 데이터베이스 크기는 10GB와 지원되는 최대 크기 사이에서 선택할 수 있습니다.
  - 표준 또는 범용 서비스 계층의 스토리지 크기는 10GB 단위로 늘리거나 줄이세요.
  - 프리미엄 또는 중요 비즈니스용 서비스 계층의 스토리지 크기는 250GB 단위로 늘리거나 줄이세요.
- 범용 서비스 계층의 경우 `tempdb`에서 연결형 SSD를 사용하며, 이 저장소의 비용이 vCore 가격에 포함됩니다.
- 중요 비즈니스 서비스 계층의 경우 `tempdb`에서 MDF 및 LDF 파일이 포함된 연결형 SSD를 공유하며, tempDB 저장소의 비용이 vCore 가격에 포함됩니다.

> [!IMPORTANT]
> MDF 및 LDF에 할당된 총 저장소 비용이 부과됩니다.

MDF 및 LDF의 현재 총 크기를 모니터링하려면 [sp_spaceused](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-spaceused-transact-sql)를 사용합니다. 개별 MDF 및 LDF 파일의 현재 크기를 모니터링하려면 [sys.database_files](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-database-files-transact-sql)를 사용합니다.

> [!IMPORTANT]
> 경우에 따라 사용하지 않는 공간을 회수하기 위해 데이터베이스를 축소해야 할 수도 있습니다. 자세한 내용은 [Azure SQL Database의 파일 공간 관리](sql-database-file-space-management.md)를 참조하세요.

## <a name="backups-and-storage"></a>백업 및 저장소

데이터베이스 백업용 저장소는 SQL Database의 PITR(특정 시점 복원) 및 [LTR(장기 보존)](sql-database-long-term-retention.md) 기능을 지원하도록 할당됩니다. 이 저장소는 각 데이터베이스에 대해 개별적으로 할당되며 데이터베이스당 별도의 두 가지 요금으로 청구됩니다.

- **PITR**: 개별 데이터베이스 백업은 [RA-GRS 스토리지](../storage/common/storage-designing-ha-apps-with-ragrs.md)에 자동으로 복사됩니다. 새 백업이 만들어지면 저장소 크기가 동적으로 증가합니다.  저장소는 주별 전체 백업, 일별 차등 백업 및 5분마다 복사되는 트랜잭션 로그 백업에 사용됩니다. 저장소 사용량은 데이터베이스 변경률과 보존 기간에 따라 다릅니다. 각 데이터베이스에 대한 개별적인 보존 기간은 7-35일 사이에서 구성할 수 있습니다. 데이터 크기의 1배에 해당하는 최소 저장소 크기는 추가 비용 없이 제공됩니다. 대부분의 데이터베이스에서 이 크기는 7일간의 백업을 저장하기에 충분합니다.
- **LTR**: SQL Database는 전체 백업의 장기 보존을 최대 10년 동안 구성하는 옵션을 제공합니다. LTR 정책을 사용하도록 설정하면 이러한 백업이 RA-GRS 저장소에 자동으로 저장되지만 백업이 복사되는 빈도를 제어할 수 있습니다. 서로 다른 준수 요구 사항을 충족하려면 주별, 월별 및/또는 연도별 백업에 대해 다른 보존 기간을 선택할 수 있습니다. 이 구성은 LTR 백업에 사용되는 저장소의 크기를 정의합니다. LTR 가격 계산기를 사용하여 LTR 저장소 비용을 추정할 수 있습니다. 자세한 내용은 [장기 보존](sql-database-long-term-retention.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

- 범용 및 중요 비즈니스용 서비스 계층의 단일 데이터베이스에 사용할 수 있는 특정 계산 크기 및 스토리지 크기 선택 사항에 대한 자세한 내용은 [단일 데이터베이스에 대한 SQL Database vCore 기반 리소스 제한](sql-database-vcore-resource-limits-single-databases.md#general-purpose-service-tier)을 참조하세요.
- 범용 및 중요 비즈니스용 서비스 계층의 탄력적 풀에 사용할 수 있는 특정 계산 크기 및 스토리지 크기 선택 사항에 대한 자세한 내용은 [탄력적 풀에 대한 SQL Database vCore 기반 리소스 제한](sql-database-vcore-resource-limits-elastic-pools.md#general-purpose-service-tier-storage-sizes-and-compute-sizes)을 참조하세요.
