---
title: 활성 지리적 복제
description: 활성 지역 복제를 사용 하 여 동일한 데이터 센터 또는 다른 데이터 센터 지역에서 Azure SQL Database에 있는 개별 데이터베이스의 읽기 가능한 보조 데이터베이스를 만들 수 있습니다.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
ms.date: 08/27/2020
ms.openlocfilehash: a269796c072a235e4ecd47731ca37a774750a3cf
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89018368"
---
# <a name="creating-and-using-active-geo-replication---azure-sql-database"></a>활성 지역 복제 만들기 및 사용-Azure SQL Database
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

활성 지역 복제는 동일 하거나 다른 데이터 센터 (지역)에 있는 서버에서 개별 데이터베이스의 읽기 가능한 보조 데이터베이스를 만들 수 있는 Azure SQL Database 기능입니다.

> [!NOTE]
> 활성 지역 복제는 Azure SQL Managed Instance에서 지원 되지 않습니다. SQL Managed Instance 인스턴스의 지리적 장애 조치 (failover)의 경우 [자동 장애 조치 (failover) 그룹](auto-failover-group-overview.md)을 사용 합니다.

활성 지역 복제는 지역 재해 또는 대규모 중단이 발생한 경우 애플리케이션이 개별 데이터베이스의 빠른 재해 복구를 수행할 수 있도록 하는 비즈니스 연속성 솔루션으로 설계되었습니다. 지역 복제를 사용하는 경우 애플리케이션이 다른 Azure 지역에서 보조 데이터베이스에 대한 장애 조치를 시작할 수 있습니다. 동일하거나 다른 지역에서 최대 4개의 보조 데이터베이스가 지원되며 보조 데이터베이스는 읽기 전용 액세스 쿼리에 사용될 수도 있습니다. 장애 조치는 애플리케이션 또는 사용자에 의해 수동으로 시작되어야 합니다. 장애 조치 후 새 주 데이터베이스에는 다른 연결 엔드포인트가 있습니다.

> [!NOTE]
> 활성 지역 복제는 스트리밍 데이터베이스 트랜잭션 로그를 통해 변경 내용을 복제 합니다. DML (INSERT, UPDATE, DELETE) 명령을 실행 하 여 변경 내용을 복제 하는 [트랜잭션 복제](https://docs.microsoft.com/sql/relational-databases/replication/transactional/transactional-replication)와는 관련이 없습니다.

다음 다이어그램은 활성 지역 복제를 사용하는 지역 중복 클라우드 애플리케이션의 일반적인 구성을 보여 줍니다.

![활성 지역 복제](./media/active-geo-replication-overview/geo-replication.png )

> [!IMPORTANT]
> SQL Database는 자동 장애 조치(failover) 그룹도 지원합니다. 자세한 내용은 [자동 장애 조치(failover) 그룹](auto-failover-group-overview.md) 사용을 참조하세요.

어떠한 이유로든 주 데이터베이스가 실패하거나 단순히 오프라인으로 전환해야 하는 경우 보조 데이터베이스로 장애 조치(failover)를 시작할 수 있습니다. 장애 조치가 보조 데이터베이스 중 하나로 활성화된 경우 모든 다른 보조가 새 보조로 자동으로 연결됩니다.

활성 지역 복제를 사용하여 서버 또는 탄력적 풀에 있는 개별 데이터베이스 또는 데이터베이스 집합의 복제 및 장애 조치를 관리할 수 있습니다. 다음을 사용하여 수행할 수 있습니다.

- [Azure Portal](active-geo-replication-configure-portal.md)
- [PowerShell: 단일 데이터베이스](scripts/setup-geodr-and-failover-database-powershell.md)
- [PowerShell: 탄력적 풀](scripts/setup-geodr-and-failover-elastic-pool-powershell.md)
- [Transact-SQL: 단일 데이터베이스 또는 탄력적 풀](/sql/t-sql/statements/alter-database-azure-sql-database)
- [REST API: 단일 데이터베이스](https://docs.microsoft.com/rest/api/sql/replicationlinks)

활성 지역 복제는 데이터베이스 엔진의 [Always On 가용성 그룹](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server) 기술을 활용 하 여 스냅숏 격리를 통해 주 데이터베이스의 커밋된 트랜잭션을 보조 데이터베이스로 비동기적으로 복제 합니다. 자동 장애 조치 그룹은 활성 지역 복제를 기반으로 하는 그룹 의미 체계를 제공하지만 동일한 비동기 복제 메커니즘이 사용됩니다. 지정된 지점에서 보조 데이터베이스는 주 데이터베이스보다 약간 뒤에 있을 수 있는 반면 보조 데이터는 절대 부분 트랜잭션을 갖지 않습니다. 지역 간 중복을 사용하면 자연 재해, 인간의 치명적인 실수 또는 악의적 행위로 인해 데이터 센터의 일부 또는 전체가 영구적으로 손실되더라도 애플리케이션을 빠르게 복구할 수 있습니다. 특정 RPO 데이터를 [비즈니스 연속성 개요](business-continuity-high-availability-disaster-recover-hadr-overview.md)에서 찾을 수 있습니다.

> [!NOTE]
> 두 지역 간에 네트워크 연결이 실패하면 연결을 다시 설정하도록 10초마다 재시도합니다.

> [!IMPORTANT]
> 장애 조치(failover) 전에 주 데이터베이스의 중요한 변경 내용이 보조 데이터베이스에 복제되도록 보장하려면 동기화를 강제 적용하여 중요한 변경 내용(예: 암호 업데이트)이 복제되도록 할 수 있습니다. 강제 동기화는 모든 커밋된 트랜잭션이 복제될 때까지 호출 스레드를 차단하므로 성능에 영향을 주게 됩니다. 자세한 내용은 [sp_wait_for_database_copy_sync](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync)를 참조하세요. 주 데이터베이스와 지역 보조 데이터베이스 간의 복제 지연을 모니터링하려면 [sys.dm_geo_replication_link_status](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database)를 참조하세요.

다음 그림은 미국 중북부 지역에 주 데이터베이스, 미국 중남부 지역에 보조 데이터베이스가 구성된 활성 지역 복제의 예입니다.

![지역에서 복제 관계](./media/active-geo-replication-overview/geo-replication-relationship.png)

보조 데이터베이스를 읽을 수 있기 때문에 보고 작업과 같은 읽기 전용 워크로드를 오프로드하는 데 사용할 수 있습니다. 활성 지역 복제를 사용하는 경우 주 데이터베이스와 동일한 지역에 보조 데이터베이스를 만들 수 있지만 치명적인 장애에 대한 애플리케이션의 복원성을 높이지는 않습니다. 자동 장애 조치(failover) 그룹을 사용하는 경우, 보조 데이터베이스는 항상 다른 지역에 만들어집니다.

활성 지역 복제는 재해 복구 외에도 다음과 같은 시나리오에서 사용할 수 있습니다.

- **데이터베이스 마이그레이션**: 활성 지역 복제를 사용하여 최소 가동 중지 시간으로 하나의 서버에서 다른 온라인으로 데이터베이스를 마이그레이션할 수 있습니다.
- **애플리케이션 업그레이드**: 애플리케이션을 업그레이드하는 동안 추가 보조 데이터베이스를 장애 복구 복사본으로 만들 수 있습니다.

실제 비즈니스 연속성을 달성하기 위해 데이터 센터 간에 데이터베이스 중복을 추가하는 것은 솔루션의 일부입니다. 치명적인 오류 후 애플리케이션(서비스) 엔드투엔드 복구에는 서비스 및 모든 종속성 서비스를 구성하는 모든 구성 요소의 복구가 필요합니다. 이러한 구성 요소의 예에는 클라이언트 소프트웨어(예: 사용자 지정 JavaScript를 사용한 브라우저), 웹 프런트 엔드, 스토리지 및 DNS가 포함됩니다. 모든 구성 요소는 동일한 오류에 탄력적이며 애플리케이션의 복구 시간 목표(RTO) 내에서 사용할 수 있는 것이 중요합니다. 따라서 모든 종속성 서비스를 확인하고 제고하는 보장 사항 및 기능을 이해해야 합니다. 그런 다음 의존하는 서비스 장애 조치 중 서비스 기능을 확인하도록 적절한 단계를 수행해야 합니다. 재해 복구를 위한 솔루션 설계에 대 한 자세한 내용은 [활성 지역 복제를 사용 하 여 재해 복구를 위한 클라우드 솔루션 설계](designing-cloud-solutions-for-disaster-recovery.md)를 참조 하세요.

## <a name="active-geo-replication-terminology-and-capabilities"></a>활성 지역 복제 용어 및 기능

- **자동 비동기 복제**

  보조 데이터베이스는 기존 데이터베이스에 추가하는 방법으로만 만들 수 있습니다. 모든 서버에서 보조 데이터베이스를 만들 수 있습니다. 한번 만들면 보조 데이터베이스는 주 데이터베이스에서 복사된 데이터로 채워집니다. 이 프로세스를 시드라고 합니다. 보조 데이터베이스가 생성 및 시드되면 주 데이터베이스에 대한 업데이트가 보조 데이터베이스에 자동으로 비동기적으로 복사됩니다. 비동기 복제에서는 트랜잭션이 보조 데이터베이스에 복제되기 전에 주 데이터베이스에 커밋됩니다.

- **읽을 수 있는 보조 데이터베이스**

  애플리케이션은 주 데이터베이스에 액세스하는 데 사용된 동일한 또는 다른 보안 주체를 사용하여 읽기 전용 작업에 대한 보조 데이터베이스에 액세스할 수 있습니다. 보조 데이터베이스에서 실행되는 쿼리 때문에 주 데이터베이스(로그 재생)의 업데이트 복제가 지연되지 않도록 보장하기 위해 보조 데이터베이스는 스냅샷 격리 모드에서 작동합니다.

> [!NOTE]
> 주 데이터베이스에 스키마 업데이트가 있는 경우 보조 데이터베이스에서 로그 재생이 지연됩니다. 후자의 경우 보조 데이터베이스에 대한 스키마 잠금이 필요합니다.

> [!IMPORTANT]
> 지역에서 복제를 사용 하 여 주 데이터베이스와 동일한 지역에 보조 데이터베이스를 만들 수 있습니다. 이 보조 데이터베이스를 사용 하 여 동일한 지역에서 읽기 전용 작업의 부하를 분산할 수 있습니다. 그러나 동일한 지역의 보조 데이터베이스는 추가 오류 복원 력을 제공 하지 않으므로 재해 복구를 위한 적절 한 장애 조치 (failover) 대상이 아닙니다. 또한 가용성 영역 격리를 보장 하지 않습니다. [영역 중복 구성을](high-availability-sla.md#zone-redundant-configuration) 통해 업무상 중요 한 또는 프리미엄 서비스 계층을 사용 하 여 가용성 영역 격리를 달성할 수 있습니다.
>

- **계획 된 장애 조치**

  계획 된 장애 조치 (failover)는 전체 동기화가 완료 된 후 주 데이터베이스와 보조 데이터베이스의 역할을 전환 합니다. 데이터 손실을 초래 하지 않는 온라인 작업입니다. 작업 시간은 동기화 해야 하는 주 복제본의 트랜잭션 로그 크기에 따라 달라 집니다. 계획 된 장애 조치 (failover)는 데이터 손실이 허용 되지 않는 경우 프로덕션 환경에서 DR 드릴을 수행 하는 (a) 시나리오를 위해 설계 되었습니다. (b) 다른 지역으로 데이터베이스를 재배치 하려면 가동 중단이 완화 (장애 복구) 된 후 데이터베이스를 주 지역으로 반환 하는 경우 (c)

- **계획 되지 않은 장애 조치**

  계획되지 않은 장애 조치 또는 강제 장애 조치(failover)는 주 데이터베이스와의 동기화 없이 보조 역할을 주 역할로 즉시 전환합니다. 주 복제본에 커밋된 모든 트랜잭션은 손실 됩니다. 이 작업은 주 데이터베이스에 액세스할 수 없지만 데이터베이스 가용성을 신속 하 게 복원 해야 하는 경우 중단 시 복구 방법으로 설계 되었습니다. 원래 주 데이터베이스가 다시 온라인 상태가 되 면 자동으로 다시 연결 되 고 새 보조 데이터베이스가 됩니다. 장애 조치 (failover) 전에 동기화 되지 않은 모든 트랜잭션은 백업 파일에 유지 되지만 충돌을 피하기 위해 새 주 복제본과 동기화 되지 않습니다. 이러한 트랜잭션을 주 데이터베이스의 최신 버전과 수동으로 병합 해야 합니다.

- **여러 읽기 가능 보조 데이터베이스**

  주 데이터베이스마다 최대 4개의 보조 데이터베이스를 만들 수 있습니다. 보조 데이터베이스가 하나밖에 없는 경우 새 보조 데이터베이스를 만들 때까지 애플리케이션이 더 높은 위험에 노출됩니다. 보조 데이터베이스가 여러 개 있는 경우 보조 데이터베이스 중 하나가 실패하더라도 애플리케이션이 계속해서 보호됩니다. 추가 보조 데이터베이스를 사용하여 읽기 전용 워크로드를 확장할 수도 있습니다.

  > [!NOTE]
  > 활성 지역 복제를 사용하여 전역으로 분산된 애플리케이션을 빌드하고 5개 이상의 지역에 있는 데이터에 대한 읽기 전용 액세스를 제공해야 하는 경우 보조 데이터베이스의 보조 데이터베이스를 만들 수 있습니다(체이닝으로 알려진 프로세스). 이렇게 하면 거의 제한이 없는 규모의 데이터베이스 복제를 수행할 수 있습니다. 또한 체이닝을 사용하면 주 데이터베이스의 복제 오버헤드가 줄어듭니다. 단점은 리프가 가장 많은 보조 데이터베이스의 복제 지연 시간이 증가하는 것입니다.

- **탄력적 풀에서 데이터베이스 지역 복제**

  각 보조 데이터베이스가 탄력적 풀에 개별적으로 참여하거나 탄력적 풀에 참여하지 않을 수 있습니다. 각 보조 데이터베이스에 대한 풀 선택은 별개이며 다른 보조 데이터베이스의 구성(주 데이터베이스인지, 보조 데이터베이스인지 여부)에 종속되지 않습니다. 각 탄력적 풀은 단일 지역 내에 포함되므로 동일한 토폴로지의 여러 보조 데이터베이스에서 탄력적 풀을 공유할 수 없습니다.

- **사용자 제어 장애 조치 및 장애 복구**

  보조 데이터베이스는 애플리케이션 또는 사용자에 의해 언제든지 주 데이터베이스 역할로 전환될 수 있습니다. 실제 중단 중에는 "계획 되지 않음" 옵션을 사용 해야 합니다 .이 옵션을 사용 하면 보조 데이터베이스를 기본으로 즉시 승격 합니다. 중지된 주 데이터베이스가 복구되어 작동 가능한 상태가 되면 시스템에서는 복구된 주 데이터베이스를 자동으로 보조 데이터베이스로 표시하고 새로운 주 데이터베이스와 함께 최신 상태로 전환합니다. 주 데이터베이스가 가장 최근의 변경 사항을 보조 데이터베이스로 복제하기 전에 중단될 경우 비동기 복제의 특성상 계획되지 않은 장애 조치(failover)가 진행되는 동안 소량의 데이터가 손실될 수 있습니다. 보조 데이터베이스가 여러 개 있는 주 데이터베이스가 중단되면 시스템에서 사용자의 개입 없이 자동으로 복제 관계를 다시 구성하고 남아 있는 보조 데이터베이스를 새로 승격되는 주 데이터베이스에 연결합니다. 장애 조치(failover)를 일으킨 작동 중단 상황이 완화된 후에는 애플리케이션을 주 지역으로 반환하는 것이 바람직할 수 있습니다. 이렇게 하려면 "계획 된" 옵션을 사용 하 여 장애 조치 (failover) 명령을 호출 해야 합니다.

## <a name="preparing-secondary-database-for-failover"></a>장애 조치 (failover)를 위한 보조 데이터베이스 준비

장애 조치 (failover) 후 응용 프로그램이 새 주 데이터베이스에 즉시 액세스할 수 있도록 하려면 보조 서버와 데이터베이스에 대 한 인증 요구 사항이 올바르게 구성 되어 있는지 확인 합니다. 자세한 내용은 [재해 복구 후의 SQL Database 보안](active-geo-replication-security-configure.md)을 참조하세요. 장애 조치 (failover) 후 준수를 보장 하려면 보조 데이터베이스에 대 한 백업 보존 정책이 주 데이터베이스의 백업 보존 정책과 일치 하는지 확인 합니다. 이러한 설정은 데이터베이스에 포함 되지 않으며 복제 되지 않습니다. 기본적으로 보조 복제본은 7 일의 기본 PITR 보존 기간으로 구성 됩니다. 자세한 내용은 [SQL Database 자동화된 백업](automated-backups-overview.md)을 참조하세요.

> [!IMPORTANT]
> 데이터베이스가 장애 조치 (failover) 그룹의 구성원 인 경우 지역에서 복제 장애 조치 (failover) 명령을 사용 하 여 장애 조치 (failover)를 시작할 수 없습니다. 그룹에 대해 장애 조치 (failover) 명령을 사용 합니다. 개별 데이터베이스를 장애 조치 (failover) 해야 하는 경우 먼저 장애 조치 (failover) 그룹에서 제거 해야 합니다. 자세한 내용은  [장애 조치 (failover) 그룹](auto-failover-group-overview.md) 을 참조 하세요.

## <a name="configuring-secondary-database"></a>보조 데이터베이스 구성

동일한 서비스 계층을 확보하려면 주 데이터베이스와 보조 데이터베이스 모두 필요합니다. 주 데이터베이스와 동일한 계산 크기 (Dtu 또는 vCores)를 사용 하 여 보조 데이터베이스를 만드는 것이 좋습니다. 주 데이터베이스에 많은 쓰기 작업이 발생 하는 경우에는 계산 크기가 낮은 보조 데이터베이스를 사용할 수 없습니다. 이로 인해 보조 복제본에서 다시 실행 지연이 발생 하 고 보조 복제본의 가용성을 사용할 수 없게 됩니다. 이러한 위험을 완화 하기 위해 활성 지역 복제는 보조가 해당 보조 데이터베이스를 사용할 수 있도록 하는 데 필요한 경우 주 데이터베이스의 트랜잭션 로그 속도를 제한 합니다.

불균형 보조 구성의 또 다른 결과는 장애 조치 (failover) 후 새 주 서버의 계산 용량이 부족 하기 때문에 응용 프로그램 성능이 저하 될 수 있다는 것입니다. 이 경우 데이터베이스 서비스 목표를 필요한 수준으로 확장 해야 하며,이는 상당한 시간과 계산 리소스를 사용할 수 있으며, 확장 프로세스가 끝날 때 고가용성 장애 조치 (failover [)가 필요](high-availability-sla.md) 합니다.

계산 크기가 낮은 보조를 만들도록 결정 한 경우 Azure Portal의 로그 IO 백분율 차트는 복제 로드를 유지 하는 데 필요한 보조의 최소 계산 크기를 예측 하는 좋은 방법을 제공 합니다. 예를 들어 주 데이터베이스가 P6 (1000 DTU)이 고 해당 로그 쓰기 백분율이 50% 이면 보조 데이터베이스는 최소한 P4 (500 DTU) 이상 이어야 합니다. 기록 로그 IO 데이터를 검색 하려면 [resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) 뷰를 사용 합니다. 로그 속도가 단기 급증을 반영 하는 더 높은 세분성으로 최근 로그 쓰기 데이터를 검색 하려면 [dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) 보기를 사용 합니다.

보조에서 계산 크기가 더 낮은 경우 주 데이터베이스에 대 한 트랜잭션 로그 전송률 제한이 HADR_THROTTLE_LOG_RATE_MISMATCHED_SLO 대기 유형을 사용 하 여 보고 됩니다. [dm_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) 및 [sys. dm_os_wait_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql) 데이터베이스 뷰에 표시 됩니다.

> [!NOTE]
> 주 데이터베이스의 트랜잭션 로그 비율은 보조에서 계산 크기가 낮은 것과 관련이 없는 이유로 제한 될 수 있습니다. 이러한 종류의 제한은 보조 데이터베이스의 계산 크기가 주 데이터베이스와 같거나 높은 경우에도 발생할 수 있습니다. 다른 종류의 로그 전송률 제한에 대 한 대기 유형을 포함 하는 자세한 내용은 [트랜잭션 로그 요금 관리](resource-limits-logical-server.md#transaction-log-rate-governance)를 참조 하세요.

SQL Database 컴퓨팅 크기에 대한 자세한 내용은 [SQL Database 서비스 계층이란?](purchasing-models.md)를 참조하세요.

## <a name="cross-subscription-geo-replication"></a>구독 간 지역에서 복제

서로 다른 구독에 속한 두 데이터베이스 간에 활성 지역 복제를 설정 하려면 (동일한 테 넌 트에 있는지 여부에 관계 없이)이 섹션에 설명 된 특수 절차를 따라야 합니다.  이 절차는 SQL 명령을 기반으로 하며 다음이 필요 합니다.

- 두 서버에서 권한 있는 로그인 만들기
- 두 서버에서 변경을 수행 하는 클라이언트의 허용 목록에 IP 주소를 추가 합니다 (예: SQL Server Management Studio를 실행 하는 호스트의 IP 주소).

변경을 수행 하는 클라이언트에는 주 서버에 대 한 네트워크 액세스가 필요 합니다. 클라이언트의 동일한 IP 주소를 보조 서버의 허용 목록에 추가 해야 하지만, 보조 서버에 대 한 네트워크 연결은 반드시 필요한 것은 아닙니다.

### <a name="on-the-master-of-the-primary-server"></a>주 서버의 마스터

1. 변경 작업을 수행 하는 클라이언트의 허용 목록에 IP 주소를 추가 합니다. 자세한 내용은 [방화벽 구성](firewall-configure.md)을 참조 하세요.
1. 활성 지역 복제를 설정 하는 전용 로그인을 만들고 필요에 따라 자격 증명을 조정 합니다.

   ```sql
   create login geodrsetup with password = 'ComplexPassword01'
   ```

1. 해당 사용자를 만들어 dbmanager 역할에 할당 합니다.

   ```sql
   create user geodrsetup for login geodrsetup
   alter role dbmanager add member geodrsetup
   ```

1. 이 쿼리를 사용 하 여 새 로그인의 SID를 기록해 둡니다.

   ```sql
   select sid from sys.sql_logins where name = 'geodrsetup'
   ```

### <a name="on-the-source-database-on-the-primary-server"></a>주 서버의 원본 데이터베이스

1. 동일한 로그인에 대 한 사용자 만들기:

   ```sql
   create user geodrsetup for login geodrsetup
   ```

1. 사용자를 db_owner 역할에 추가 합니다.

   ```sql
   alter role db_owner add member geodrsetup
   ```

### <a name="on-the-master-of-the-secondary-server"></a>보조 서버의 마스터

1. 보조 서버에 대 한 방화벽 규칙의 허용 목록에 클라이언트 IP 주소를 추가 합니다. 주 서버에 추가 된 것과 정확히 동일한 클라이언트 IP 주소가 보조 데이터베이스에도 추가 되었는지 확인 합니다. ALTER DATABASE ADD SECONDARY 명령을 실행 하 여 지역에서 복제를 시작 하기 전에 수행 해야 하는 필수 단계입니다.

1. 동일한 사용자 이름 및 SID를 사용 하 여 주 서버에서와 동일한 로그인을 만듭니다.

   ```sql
   create login geodrsetup with password = 'ComplexPassword01', sid=0x010600000000006400000000000000001C98F52B95D9C84BBBA8578FACE37C3E
   ```

1. 해당 사용자를 만들어 dbmanager 역할에 할당 합니다.

   ```sql
   create user geodrsetup for login geodrsetup;
   alter role dbmanager add member geodrsetup
   ```

### <a name="on-the-master-of-the-primary-server"></a>주 서버의 마스터

1. 새 로그인을 사용 하 여 주 서버의 마스터에 로그인 합니다.
1. 보조 서버에 원본 데이터베이스의 보조 복제본을 만듭니다 (필요에 따라 데이터베이스 이름 및 서버 이름 조정).

   ```sql
   alter database dbrep add secondary on server <servername>
   ```

초기 설치 후에는 만든 사용자, 로그인 및 방화벽 규칙을 제거할 수 있습니다.

## <a name="keeping-credentials-and-firewall-rules-in-sync"></a>자격 증명 및 방화벽 규칙의 동기화 유지

지리적으로 복제 된 데이터베이스에는 [데이터베이스 수준 IP 방화벽 규칙](firewall-configure.md) 을 사용 하는 것이 좋습니다. 이렇게 하면 모든 보조 데이터베이스가 주 데이터베이스와 동일한 IP 방화벽 규칙을 갖도록 하기 위해 데이터베이스를 사용 하 여 이러한 규칙을 복제할 수 있습니다. 이렇게 하면 고객이 주 데이터베이스 및 보조 데이터베이스를 호스팅하는 서버에서 수동으로 방화벽 규칙을 구성하고 유지 관리할 필요가 없습니다. 마찬가지로, 데이터 액세스에 [포함된 데이터베이스 사용자](logins-create-manage.md)를 사용하면 주 데이터베이스와 보조 데이터베이스의 사용자 자격 증명이 항상 똑같기 때문에 장애 조치(failover) 시에 로그인과 암호가 불일치하여 중단되는 일이 없습니다. [Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md)가 추가되면서 고객은 주 데이터베이스 및 보조 데이터베이스에 대한 사용자 액세스를 관리할 수 있으므로 데이터베이스의 자격 증명을 모두 관리할 필요가 없습니다.

## <a name="upgrading-or-downgrading-primary-database"></a>주 데이터베이스 업그레이드 또는 다운 그레이드

보조 데이터베이스와의 연결을 끊지 않고도 주 데이터베이스를 다른 컴퓨팅 크기(동일한 서비스 계층 내, 범용 및 중요 비즈니스용 사이 아님)로 업그레이드하거나 다운그레이드할 수 있습니다. 업그레이드하는 경우에는 보조 데이터베이스를 먼저 업그레이드한 다음에 주 데이터베이스를 업그레이드하는 것이 좋습니다. 다운그레이드하는 경우에는 반대 순서로 주 데이터베이스를 먼저 다운그레이드하고 보조 데이터베이스를 다운그레이드합니다. 데이터베이스를 다른 서비스 계층으로 업그레이드하거나 다운그레이드할 때 이 권장 사항이 적용됩니다.

> [!NOTE]
> 장애 조치 그룹 구성의 일부로 보조 데이터베이스를 만든 경우 보조 데이터베이스를 다운그레이드하지 않는 것이 좋습니다. 이렇게 하면 장애 조치가 활성화된 후 데이터 계층에서 일반 워크로드를 처리할 수 있을 만큼 충분한 용량을 갖출 수 있습니다.

> [!IMPORTANT]
> 장애 조치 (failover) 그룹의 주 데이터베이스는 보조 데이터베이스가 상위 계층으로 먼저 확장 되지 않는 한 더 높은 계층으로 확장할 수 없습니다. 보조 데이터베이스의 크기를 조정 하기 전에 주 데이터베이스의 크기를 조정 하려고 하면 다음과 같은 오류가 표시 될 수 있습니다.
>
> `Error message: The source database 'Primaryserver.DBName' cannot have higher edition than the target database 'Secondaryserver.DBName'. Upgrade the edition on the target before upgrading the source.`
>

## <a name="preventing-the-loss-of-critical-data"></a>중요한 데이터 손실 방지

광역 네트워크의 높은 대기 시간으로 인해 연속 복사는 비동기 복제 메커니즘을 사용합니다. 비동기 복제를 수행하면 오류가 발생하는 경우에 일부 데이터 손실은 불가피합니다. 그러나 일부 애플리케이션은 데이터 손실이 없어야 합니다. 이러한 중요한 업데이트를 보호하기 위해 애플리케이션 개발자는 트랜잭션을 커밋한 후 즉시 [sp_wait_for_database_copy_sync](/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync) 시스템 프로시저를 호출할 수 있습니다. **sp_wait_for_database_copy_sync** 호출은 마지막으로 커밋된 트랜잭션이 보조 데이터베이스로 전송될 때까지 호출 스레드를 차단합니다. 그러나 전송된 트랜잭션이 보조 데이터베이스에서 재생 및 커밋될 때까지 기다리지 않습니다. **sp_wait_for_database_copy_sync**는 그 범위가 특정 연속 복사 링크로 한정됩니다. 주 데이터베이스에 대한 연결 권한이 있는 모든 사용자는 이 프로시저를 호출할 수 있습니다.

> [!NOTE]
> **sp_wait_for_database_copy_sync**는 장애 조치 후 데이터 손실을 방지하지만 읽기 액세스를 위한 전체 동기화를 보장하지 않습니다. **sp_wait_for_database_copy_sync** 프로시저 호출로 인한 지연은 심각할 수 있으며 호출 시 트랜잭션 로그의 크기에 따라 달라집니다.

## <a name="monitoring-geo-replication-lag"></a>지역에서 복제 지연 시간 모니터링

RPO와 관련 하 여 지연을 모니터링 하려면 주 데이터베이스에서 [dm_geo_replication_link_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database) 의 *replication_lag_sec* 열을 사용 합니다. 주 데이터베이스에서 커밋되고 보조 데이터베이스에서 지속 되는 트랜잭션 간의 지연 시간 (초)을 보여 줍니다. 예를 들어 지연 값이 1 초 이면 주 복제본이 현재 가동 중단의 영향을 받을 수 있고 장애 조치 (failover)가 시작 됨을 의미 합니다. 최근 전환 중 1 초가 저장 되지 않습니다.

보조 복제본에서 적용 된 주 데이터베이스의 변경 내용에 대해 지연 시간을 측정 하려면 (예: 보조 데이터베이스에서 읽기 가능) 보조 데이터베이스의 *last_commit* 시간을 주 데이터베이스의 동일한 값과 비교 합니다.

> [!NOTE]
> 주 데이터베이스에 대 한 *REPLICATION_LAG_SEC* NULL 값을 가지는 경우가 있습니다. 즉, 주 데이터베이스에서 현재 보조 복제본이 얼마나 떨어져 있는지 알 수 없습니다.   이는 일반적으로 프로세스가 다시 시작 된 후에 발생 하며 일시적인 상태 여야 합니다. *Replication_lag_sec* 에서 오랜 시간 동안 NULL을 반환 하는 경우 응용 프로그램을 경고 하는 것이 좋습니다. 영구적 연결 오류로 인해 보조 데이터베이스가 주 데이터베이스와 통신할 수 없음을 나타낼 수 있습니다. 보조 데이터베이스와 주 데이터베이스에서 *last_commit* 시간 사이의 차이를 일으킬 수 있는 조건도 있습니다. 예를 들어 변경 내용이 없는 긴 기간 후 주 복제본에서 커밋이 수행 되는 경우에는 0으로 빠르게 반환 하기 전에 차이가 큰 값으로 이동 합니다. 이러한 두 값 간의 차이가 오랜 시간 동안 크게 유지 되는 경우 오류 상태를 고려해 야 합니다.

## <a name="programmatically-managing-active-geo-replication"></a>활성 지역 복제를 프로그래밍 방식으로 관리

앞서 설명한 것처럼 Azure PowerShell 및 REST API를 사용하여 활성 지역 복제를 프로그래밍 방식으로 관리할 수 있습니다. 다음 표는 사용 가능한 명령의 집합을 보여 줍니다. 활성 지역 복제는 관리를 위해 [Azure SQL Database REST API](https://docs.microsoft.com/rest/api/sql/) 및 [Azure PowerShell cmdlet](https://docs.microsoft.com/powershell/azure/)을 비롯한 Azure Resource Manager API 세트를 포함합니다. 이러한 API는 리소스 그룹을 사용해야 하며 RBAC(역할 기반 보안)를 지원합니다. 액세스 역할을 구현 하는 방법에 대 한 자세한 내용은 azure [역할 기반 액세스 제어 (AZURE RBAC)](../../role-based-access-control/overview.md)를 참조 하세요.

### <a name="t-sql-manage-failover-of-single-and-pooled-databases"></a>T-sql: 단일 및 풀링된 데이터베이스의 장애 조치 (failover) 관리

> [!IMPORTANT]
> 이러한 Transact-SQL 명령은 활성 지역 복제에만 적용되고 장애 조치(failover) 그룹에는 적용되지 않습니다. 따라서 장애 조치 (failover) 그룹만 지원 하므로 SQL Managed Instance 인스턴스에는 적용 되지 않습니다.

| 명령 | 설명 |
| --- | --- |
| [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current) |기존 데이터베이스에 대한 보조 데이터베이스를 만들고 데이터 복제를 시작하려면 ADD SECONDARY ON SERVER 인수를 사용합니다. |
| [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current) |장애 조치를 시작하기 위해 보조 데이터베이스를 기본 데이터베이스로 전환하려면 FAILOVER 또는 FORCE_FAILOVER_ALLOW_DATA_LOSS를 사용합니다. |
| [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current) |SQL Database와 지정된 보조 데이터베이스 간의 데이터 복제를 종료하려면 REMOVE SECONDARY ON SERVER를 사용합니다. |
| [sys.geo_replication_links](/sql/relational-databases/system-dynamic-management-views/sys-geo-replication-links-azure-sql-database) |서버에 있는 각 데이터베이스의 모든 기존 복제 링크에 대 한 정보를 반환 합니다. |
| [sys.dm_geo_replication_link_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database) |지정 된 데이터베이스의 복제 링크에 대 한 마지막 복제 시간, 마지막 복제 지연 및 기타 정보를 가져옵니다. |
| [sys.dm_operation_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) |복제 링크의 상태를 비롯한 모든 데이터베이스 작업에 대한 상태를 표시합니다. |
| [sp_wait_for_database_copy_sync](/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync) |커밋된 모든 트랜잭션이 복제되고 활성 보조 데이터베이스에서 승인될 때까지 애플리케이션이 대기하도록 합니다. |
|  | |

### <a name="powershell-manage-failover-of-single-and-pooled-databases"></a>PowerShell: 단일 및 풀링된 데이터베이스의 장애 조치 (failover) 관리

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure Resource Manager 모듈은 여전히 Azure SQL Database에서 지원되지만 향후의 모든 개발은 Az.Sql 모듈을 위한 것입니다. 이러한 cmdlet은 [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)을 참조하세요. Az 모듈 및 AzureRm 모듈의 명령에 대한 인수는 실질적으로 동일합니다.

| cmdlet | 설명 |
| --- | --- |
| [Get-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabase) |하나 이상의 데이터베이스를 가져옵니다. |
| [New-AzSqlDatabaseSecondary](https://docs.microsoft.com/powershell/module/az.sql/new-azsqldatabasesecondary) |기존 데이터베이스에 대한 보조 데이터베이스를 만들고 데이터 복제를 시작합니다. |
| [Set-AzSqlDatabaseSecondary](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabasesecondary) |장애 조치를 시작하기 위해 보조 데이터베이스로 전환합니다. |
| [Remove-AzSqlDatabaseSecondary](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqldatabasesecondary) |SQL Database와 지정된 보조 데이터베이스 간의 데이터 복제를 종료합니다. |
| [Get-AzSqlDatabaseReplicationLink](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasereplicationlink) |Azure SQL Database와 리소스 그룹 또는 논리 SQL 서버 간의 지역 복제 링크를 가져옵니다. |
|  | |

> [!IMPORTANT]
> 샘플 스크립트는 [활성 지역 복제를 사용하여 단일 데이터베이스 구성 및 장애 조치(failover)](scripts/setup-geodr-and-failover-database-powershell.md) 및 [활성 지역 복제를 사용하여 풀링된 데이터베이스 구성 및 장애 조치(failover)](scripts/setup-geodr-and-failover-elastic-pool-powershell.md)를 참조하세요.

### <a name="rest-api-manage-failover-of-single-and-pooled-databases"></a>REST API: 단일 및 풀링된 데이터베이스의 장애 조치 (failover) 관리

| API | 설명 |
| --- | --- |
| [데이터베이스 생성 또는 업데이트(createMode=Restore)](https://docs.microsoft.com/rest/api/sql/databases/createorupdate) |주 보조 데이터베이스 또는 보조 데이터베이스를 만들거나, 업데이트하거나, 복원합니다. |
| [데이터베이스 만들기 또는 업데이트 상태 가져오기](https://docs.microsoft.com/rest/api/sql/databases/createorupdate) |만들기 작업 동안 상태를 반환합니다. |
| [보조 데이터베이스를 주 데이터베이스로 설정(계획된 장애 조치(failover))](https://docs.microsoft.com/rest/api/sql/replicationlinks/failover) |현재 주 데이터베이스에서 장애 조치(failover)를 통해 주 데이터베이스로 사용할 보조 데이터베이스를 설정합니다. **이 옵션은 SQL Managed Instance에 대해 지원 되지 않습니다.**|
| [보조 데이터베이스를 주 데이터베이스로 설정(계획되지 않은 장애 조치(failover))](https://docs.microsoft.com/rest/api/sql/replicationlinks/failoverallowdataloss) |현재 주 데이터베이스에서 장애 조치(failover)를 통해 주 데이터베이스로 사용할 보조 데이터베이스를 설정합니다. 이 작업으로 인해 데이터가 손실될 수 있습니다. **이 옵션은 SQL Managed Instance에 대해 지원 되지 않습니다.**|
| [복제 링크 가져오기](https://docs.microsoft.com/rest/api/sql/replicationlinks/get) |지역에서 복제 파트너 관계의 지정 된 데이터베이스에 대 한 특정 복제 링크를 가져옵니다. sys.geo_replication_links 카탈로그 뷰에 표시되는 정보를 검색합니다. **이 옵션은 SQL Managed Instance에 대해 지원 되지 않습니다.**|
| [복제 링크 - 데이터베이스 기준 목록](https://docs.microsoft.com/rest/api/sql/replicationlinks/listbydatabase) | 지역에서 복제 파트너 관계의 지정 된 데이터베이스에 대 한 모든 복제 링크를 가져옵니다. sys.geo_replication_links 카탈로그 뷰에 표시되는 정보를 검색합니다. |
| [복제 링크 삭제](https://docs.microsoft.com/rest/api/sql/replicationlinks/delete) | 데이터베이스 복제 링크를 삭제합니다. 장애 조치(failover) 중에 수행할 수 없습니다. |
|  | |

## <a name="next-steps"></a>다음 단계

- 샘플 스크립트에 대해서는 다음을 참조하세요.
  - [활성 지역 복제를 사용하여 단일 데이터베이스 구성 및 장애 조치(Failover)](scripts/setup-geodr-and-failover-database-powershell.md)
  - [활성 지역 복제를 사용하여 풀링된 데이터베이스 구성 및 장애 조치(Failover)](scripts/setup-geodr-and-failover-elastic-pool-powershell.md)
- SQL Database는 자동 장애 조치(failover) 그룹도 지원합니다. 자세한 내용은 [자동 장애 조치(failover) 그룹](auto-failover-group-overview.md) 사용을 참조하세요.
- 비즈니스 연속성의 개요 및 시나리오를 보려면 [비즈니스 연속성 개요](business-continuity-high-availability-disaster-recover-hadr-overview.md)
- 자동화 된 백업 Azure SQL Database에 대 한 자세한 내용은 [자동화 된 백업 SQL Database](automated-backups-overview.md)을 참조 하세요.
- 복구를 위해 자동화 된 백업을 사용 하는 방법을 알아보려면 [서비스에서 시작한 백업에서 데이터베이스 복원](recovery-using-backups.md)을 참조 하세요.
- 새로운 주 서버 및 데이터베이스의 인증 요구 사항에 대해 알아보려면 [재해 복구 후의 SQL Database 보안](active-geo-replication-security-configure.md)을 참조하세요.
