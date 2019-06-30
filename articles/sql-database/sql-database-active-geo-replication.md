---
title: 활성 지역 복제 - Azure SQL Database | Microsoft Docs
description: 활성 지역 복제를 사용하여 동일하거나 다른 데이터 센터(Azure 지역)에 개별 데이터베이스의 읽기 기능한 보조 데이터베이스를 만들 수 있습니다.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
manager: craigg
ms.date: 06/18/2019
ms.openlocfilehash: 826944fd3713f5cc3e99f20cb140055bfdb11a14
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/24/2019
ms.locfileid: "67341441"
---
# <a name="creating-and-using-active-geo-replication"></a>만들기 및 활성 지역 복제를 사용 합니다.

활성 지역 복제는 동일하거나 다른 데이터 센터(Azure 지역)의 SQL Database 서버에 개별 데이터베이스의 읽기 가능한 보조 데이터베이스를 만들 수 있는 Azure SQL Database 기능입니다.

> [!NOTE]
> Managed Instance는 활성 지역 복제를 지원하지 않습니다. Managed Instance의 지리적 장애 조치(failover)에 대한 내용은 [자동 장애 조치(failover) 그룹](sql-database-auto-failover-group.md)을 참조하세요.

활성 지역 복제는 지역 재해 또는 대규모 중단이 발생한 경우 애플리케이션이 개별 데이터베이스의 빠른 재해 복구를 수행할 수 있도록 하는 비즈니스 연속성 솔루션으로 설계되었습니다. 지역 복제를 사용하는 경우 애플리케이션이 다른 Azure 지역에서 보조 데이터베이스에 대한 장애 조치를 시작할 수 있습니다. 동일하거나 다른 지역에서 최대 4개의 보조 데이터베이스가 지원되며 보조 데이터베이스는 읽기 전용 액세스 쿼리에 사용될 수도 있습니다. 장애 조치는 애플리케이션 또는 사용자에 의해 수동으로 시작되어야 합니다. 장애 조치 후 새 주 데이터베이스에는 다른 연결 엔드포인트가 있습니다. 다음 다이어그램은 활성 지역 복제를 사용하는 지역 중복 클라우드 애플리케이션의 일반적인 구성을 보여 줍니다.

![활성 지역 복제](./media/sql-database-active-geo-replication/geo-replication.png )

> [!IMPORTANT]
> SQL Database는 자동 장애 조치(failover) 그룹도 지원합니다. 자세한 내용은 [자동 장애 조치(failover) 그룹](sql-database-auto-failover-group.md) 사용을 참조하세요. 또한 Managed Instance 내에서 만든 데이터베이스에 대해서는 활성 지역 복제가 지원되지 않습니다. Managed Instance에는 [장애 조치(failover) 그룹](sql-database-auto-failover-group.md)을 사용하는 것이 좋습니다.

어떠한 이유로든 주 데이터베이스가 실패하거나 단순히 오프라인으로 전환해야 하는 경우 보조 데이터베이스로 장애 조치(failover)를 시작할 수 있습니다. 장애 조치가 보조 데이터베이스 중 하나로 활성화된 경우 모든 다른 보조가 새 보조로 자동으로 연결됩니다.

활성 지역 복제를 사용하여 서버 또는 탄력적 풀에 있는 개별 데이터베이스 또는 데이터베이스 집합의 복제 및 장애 조치를 관리할 수 있습니다. 다음을 사용하여 수행할 수 있습니다.

- [Azure Portal](sql-database-geo-replication-portal.md)
- [PowerShell: 단일 데이터베이스](scripts/sql-database-setup-geodr-and-failover-database-powershell.md)
- [PowerShell: 탄력적 풀](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md)
- [Transact-SQL: 단일 데이터베이스 또는 탄력적 풀](/sql/t-sql/statements/alter-database-azure-sql-database)
- [REST API: 단일 데이터베이스](https://docs.microsoft.com/rest/api/sql/replicationlinks)

장애 조치(failover) 후에는 새로운 주 데이터베이스에서 서버 및 데이터베이스의 인증 요구 사항이 구성되어 있는지 확인합니다. 자세한 내용은 [재해 복구 후의 SQL Database 보안](sql-database-geo-replication-security-config.md)을 참조하세요.

활성 지역 복제는 스냅샷 격리를 사용하여 주 데이터베이스의 커밋된 트랜잭션을 보조 데이터베이스로 비동기적으로 복제하는 SQL Server의 [Always On](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server) 기술을 활용합니다. 자동 장애 조치 그룹은 활성 지역 복제를 기반으로 하는 그룹 의미 체계를 제공하지만 동일한 비동기 복제 메커니즘이 사용됩니다. 지정된 지점에서 보조 데이터베이스는 주 데이터베이스보다 약간 뒤에 있을 수 있는 반면 보조 데이터는 절대 부분 트랜잭션을 갖지 않습니다. 지역 간 중복을 사용하면 자연 재해, 인간의 치명적인 실수 또는 악의적 행위로 인해 데이터 센터의 일부 또는 전체가 영구적으로 손실되더라도 애플리케이션을 빠르게 복구할 수 있습니다. 특정 RPO 데이터를 [비즈니스 연속성 개요](sql-database-business-continuity.md)에서 찾을 수 있습니다.

> [!NOTE]
> 두 지역 간에 네트워크 연결이 실패하면 연결을 다시 설정하도록 10초마다 재시도합니다.
> [!IMPORTANT]
> 장애 조치(failover) 전에 주 데이터베이스의 중요한 변경 내용이 보조 데이터베이스에 복제되도록 보장하려면 동기화를 강제 적용하여 중요한 변경 내용(예: 암호 업데이트)이 복제되도록 할 수 있습니다. 강제 동기화는 모든 커밋된 트랜잭션이 복제될 때까지 호출 스레드를 차단하므로 성능에 영향을 주게 됩니다. 자세한 내용은 [sp_wait_for_database_copy_sync](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync)를 참조하세요. 주 데이터베이스와 지역 보조 데이터베이스 간의 복제 지연을 모니터링하려면 [sys.dm_geo_replication_link_status](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database)를 참조하세요.

다음 그림은 미국 중북부 지역에 주 데이터베이스, 미국 중남부 지역에 보조 데이터베이스가 구성된 활성 지역 복제의 예입니다.

![지역에서 복제 관계](./media/sql-database-active-geo-replication/geo-replication-relationship.png)

보조 데이터베이스를 읽을 수 있기 때문에 보고 작업과 같은 읽기 전용 워크로드를 오프로드하는 데 사용할 수 있습니다. 활성 지역 복제를 사용하는 경우 주 데이터베이스와 동일한 지역에 보조 데이터베이스를 만들 수 있지만 치명적인 장애에 대한 애플리케이션의 복원성을 높이지는 않습니다. 자동 장애 조치(failover) 그룹을 사용하는 경우, 보조 데이터베이스는 항상 다른 지역에 만들어집니다.

활성 지역 복제는 재해 복구 외에도 다음과 같은 시나리오에서 사용할 수 있습니다.

- **데이터베이스 마이그레이션**: 활성 지역 복제를 사용하여 최소 가동 중지 시간으로 하나의 서버에서 다른 온라인으로 데이터베이스를 마이그레이션할 수 있습니다.
- **애플리케이션 업그레이드**: 애플리케이션을 업그레이드하는 동안 추가 보조 데이터베이스를 장애 복구 복사본으로 만들 수 있습니다.

실제 비즈니스 연속성을 달성하기 위해 데이터 센터 간에 데이터베이스 중복을 추가하는 것은 솔루션의 일부입니다. 치명적인 오류 후 애플리케이션(서비스) 엔드투엔드 복구에는 서비스 및 모든 종속성 서비스를 구성하는 모든 구성 요소의 복구가 필요합니다. 이러한 구성 요소의 예에는 클라이언트 소프트웨어(예: 사용자 지정 JavaScript를 사용한 브라우저), 웹 프런트 엔드, 저장소 및 DNS가 포함됩니다. 모든 구성 요소는 동일한 오류에 탄력적이며 애플리케이션의 복구 시간 목표(RTO) 내에서 사용할 수 있는 것이 중요합니다. 따라서 모든 종속성 서비스를 확인하고 제고하는 보장 사항 및 기능을 이해해야 합니다. 그런 다음 의존하는 서비스 장애 조치 중 서비스 기능을 확인하도록 적절한 단계를 수행해야 합니다. 재해 복구를 위한 솔루션 설계에 대한 자세한 내용은 [활성 지역 복제를 사용하여 재해 복구를 위한 클라우드 솔루션 설계](sql-database-designing-cloud-solutions-for-disaster-recovery.md)를 참조하세요.

## <a name="active-geo-replication-terminology-and-capabilities"></a>활성 지역 복제 용어 및 기능

- **자동 비동기 복제**

  보조 데이터베이스는 기존 데이터베이스에 추가하는 방법으로만 만들 수 있습니다. 보조 데이터베이스는 모든 Azure SQL Database 서버에 만들 수 있습니다. 한번 만들면 보조 데이터베이스는 주 데이터베이스에서 복사된 데이터로 채워집니다. 이 프로세스를 시드라고 합니다. 보조 데이터베이스가 생성 및 시드되면 주 데이터베이스에 대한 업데이트가 보조 데이터베이스에 자동으로 비동기적으로 복사됩니다. 비동기 복제에서는 트랜잭션이 보조 데이터베이스에 복제되기 전에 주 데이터베이스에 커밋됩니다.

- **읽기 가능한 보조 데이터베이스**

  애플리케이션은 주 데이터베이스에 액세스하는 데 사용된 동일한 또는 다른 보안 주체를 사용하여 읽기 전용 작업에 대한 보조 데이터베이스에 액세스할 수 있습니다. 보조 데이터베이스에서 실행되는 쿼리 때문에 주 데이터베이스(로그 재생)의 업데이트 복제가 지연되지 않도록 보장하기 위해 보조 데이터베이스는 스냅샷 격리 모드에서 작동합니다.

> [!NOTE]
> 주 데이터베이스에 스키마 업데이트가 있는 경우 보조 데이터베이스에서 로그 재생이 지연됩니다. 후자의 경우 보조 데이터베이스에 대한 스키마 잠금이 필요합니다.
> [!IMPORTANT]
> 주 데이터베이스와 동일한 지역에서 보조 데이터베이스를 만들려면 지역에서 복제를 사용할 수 있습니다. 동일한 지역에서이 보조는 읽기 전용 부하 분산 워크 로드에 사용할 수 있습니다. 그러나 동일한 지역에 보조 데이터베이스 추가 장애 복구를 제공 하지 않습니다 및 재해 복구를 위한 적합 한 장애 조치 대상 이므로 합니다. 또한 보장 하지 않습니다 avaialability 영역 격리 합니다. 중요 한 비즈니스 또는 Premium 서비스 계층을 사용 하 여 [영역 중복 구성을](sql-database-high-availability.md#zone-redundant-configuration) avaialability 영역 격리 달성 하기 위해.   
>

- **계획된 장애 조치**

  계획된 장애 조치(failover)는 보조 역할이 주 역할로 전환되기 전에 주 데이터베이스와 보조 데이터베이스 간에 전체 동기화를 수행합니다. 이렇게 하면 데이터 손실이 발생하지 않습니다. 계획된 장애 조치(failover)는 (a) 데이터 손실이 허용되지 않을 때 프로덕션에서 DR 드릴 수행, (b) 데이터베이스를 다른 지역에 재배치, (c) 중단이 완화(장애 복구(failback))된 후 데이터베이스를 주 지역으로 반환 시나리오에서 사용됩니다.

- **계획되지 않은 장애 조치**

  계획되지 않은 장애 조치 또는 강제 장애 조치(failover)는 주 데이터베이스와의 동기화 없이 보조 역할을 주 역할로 즉시 전환합니다. 이 작업으로 인해 데이터가 손실됩니다. 계획되지 않은 장애 조치(failover)는 주 데이터베이스에 액세스할 수 없는 중단 중에 복구 방법으로 사용됩니다. 원래 주 데이터베이스가 다시 온라인 상태가 되면 동기화 없이 자동으로 다시 연결되고 새 보조 데이터베이스가 됩니다.

- **여러 읽기 가능 보조 데이터베이스**

  주 데이터베이스마다 최대 4개의 보조 데이터베이스를 만들 수 있습니다. 보조 데이터베이스가 하나밖에 없는 경우 새 보조 데이터베이스를 만들 때까지 애플리케이션이 더 높은 위험에 노출됩니다. 보조 데이터베이스가 여러 개 있는 경우 보조 데이터베이스 중 하나가 실패하더라도 애플리케이션이 계속해서 보호됩니다. 추가 보조 데이터베이스를 사용하여 읽기 전용 워크로드를 확장할 수도 있습니다.

  > [!NOTE]
  > 활성 지역 복제를 사용하여 전역으로 분산된 애플리케이션을 빌드하고 5개 이상의 지역에 있는 데이터에 대한 읽기 전용 액세스를 제공해야 하는 경우 보조 데이터베이스의 보조 데이터베이스를 만들 수 있습니다(체이닝으로 알려진 프로세스). 이렇게 하면 거의 제한이 없는 규모의 데이터베이스 복제를 수행할 수 있습니다. 또한 체이닝을 사용하면 주 데이터베이스의 복제 오버헤드가 줄어듭니다. 단점은 리프가 가장 많은 보조 데이터베이스의 복제 지연 시간이 증가하는 것입니다.

- **탄력적 풀에서 데이터베이스 지역 복제**

  각 보조 데이터베이스가 탄력적 풀에 개별적으로 참여하거나 탄력적 풀에 참여하지 않을 수 있습니다. 각 보조 데이터베이스에 대한 풀 선택은 별개이며 다른 보조 데이터베이스의 구성(주 데이터베이스인지, 보조 데이터베이스인지 여부)에 종속되지 않습니다. 각 탄력적 풀은 단일 지역 내에 포함되므로 동일한 토폴로지의 여러 보조 데이터베이스에서 탄력적 풀을 공유할 수 없습니다.


- **사용자 제어 장애 조치 및 장애 복구**

  보조 데이터베이스는 애플리케이션 또는 사용자에 의해 언제든지 주 데이터베이스 역할로 전환될 수 있습니다. 실제로 가동이 중단되면 보조 데이터베이스를 즉시 주 데이터베이스로 승격하는 "계획되지 않음" 옵션을 사용해야 합니다. 중지된 주 데이터베이스가 복구되어 작동 가능한 상태가 되면 시스템에서는 복구된 주 데이터베이스를 자동으로 보조 데이터베이스로 표시하고 새로운 주 데이터베이스와 함께 최신 상태로 전환합니다. 주 데이터베이스가 가장 최근의 변경 사항을 보조 데이터베이스로 복제하기 전에 중단될 경우 비동기 복제의 특성상 계획되지 않은 장애 조치(failover)가 진행되는 동안 소량의 데이터가 손실될 수 있습니다. 보조 데이터베이스가 여러 개 있는 주 데이터베이스가 중단되면 시스템에서 사용자의 개입 없이 자동으로 복제 관계를 다시 구성하고 남아 있는 보조 데이터베이스를 새로 승격되는 주 데이터베이스에 연결합니다. 장애 조치(failover)를 일으킨 작동 중단 상황이 완화된 후에는 애플리케이션을 주 지역으로 반환하는 것이 바람직할 수 있습니다. 장애 조치(failover)를 수행하려면 “계획됨” 옵션을 사용하여 명령을 호출해야 합니다.

- **자격 증명 및 방화벽 규칙의 동기화 유지**

사용 하는 것이 좋습니다 [데이터베이스 수준 방화벽 규칙 IP](sql-database-firewall-configure.md) 모든 보조 데이터베이스에는 주 데이터베이스와 동일한 IP 방화벽 규칙을 확인 하기 위해 데이터베이스를 사용 하 여 이러한 규칙을 복제할 수 있도록 지리적으로 복제 된 데이터베이스에 대 한 합니다. 이렇게 하면 고객이 주 데이터베이스 및 보조 데이터베이스를 호스팅하는 서버에서 수동으로 방화벽 규칙을 구성하고 유지 관리할 필요가 없습니다. 마찬가지로, 데이터 액세스에 [포함된 데이터베이스 사용자](sql-database-manage-logins.md)를 사용하면 주 데이터베이스와 보조 데이터베이스의 사용자 자격 증명이 항상 똑같기 때문에 장애 조치(failover) 시에 로그인과 암호가 불일치하여 중단되는 일이 없습니다. [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md)가 추가되면서 고객은 주 데이터베이스 및 보조 데이터베이스에 대한 사용자 액세스를 관리할 수 있으므로 데이터베이스의 자격 증명을 모두 관리할 필요가 없습니다.

## <a name="configuring-secondary-database"></a>보조 데이터베이스 구성

동일한 서비스 계층을 확보하려면 주 데이터베이스와 보조 데이터베이스 모두 필요합니다. 또한 보조 데이터베이스는 주 데이터베이스와 동일한 컴퓨팅 크기(DTU 또는 vCore 수)로 만드는 것이 좋습니다. 주 데이터베이스는 많은 쓰기 작업에 발생 하는 경우 낮은 계산 크기를 사용 하 여 보조를 유지할 것 못할 수 있습니다. 보조에서 잠재적인 비 가용성으로 다시 실행 지연 하면 결과적으로 및 장애 조치 후 후속 데이터 손실의 위험에 노출 합니다. 따라서 게시된 RPO = 5초를 보증할 수 없습니다. 또한 발생할 오류 또는 주 데이터베이스의 다른 워크 로드의 상태일 합니다. 

불균형 보조 구성의 다른 결과 장애 조치 후 새 주 복제본의 부족 하 여 계산 용량으로 인해 응용 프로그램의 성능이 저하 됩니다. 더 높은 계산을 불가능 가동 중단이 완화 될 때까지 필요한 수준으로 업그레이드 하는 데 필요한 것입니다. 

> [!NOTE]
> 현재 주 데이터베이스의 업그레이드는 오프 라인 보조 데이터베이스가 있는 경우 가능한 수 없습니다. 


컴퓨팅 크기가 더 작은 보조 데이터베이스를 만들려는 경우 Azure Portal의 로그 IO 백분율 차트에서 복제 로드를 유지하는 데 필요한 보조 데이터베이스의 최소 컴퓨팅 크기를 추정하는 좋은 방법을 제공합니다. 예를 들어 주 데이터베이스가 P6(1000 DTU)이면 해당 로그 IO 백분율은 50%이고 보조 데이터베이스는 최소한 P4(500 DTU) 이상이어야 합니다. [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) 또는 [ys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) 데이터베이스 뷰를 사용하여 로그 IO 데이터를 검색할 수도 있습니다.  SQL Database 컴퓨팅 크기에 대한 자세한 내용은 [SQL Database 서비스 계층이란?](sql-database-purchase-models.md)를 참조하세요.

## <a name="upgrading-or-downgrading-primary-database"></a>업그레이드 또는 주 데이터베이스를 다운 그레이드

보조 데이터베이스와의 연결을 끊지 않고도 주 데이터베이스를 다른 컴퓨팅 크기(동일한 서비스 계층 내, 범용 및 중요 비즈니스용 사이 아님)로 업그레이드하거나 다운그레이드할 수 있습니다. 업그레이드하는 경우에는 보조 데이터베이스를 먼저 업그레이드한 다음에 주 데이터베이스를 업그레이드하는 것이 좋습니다. 다운그레이드하는 경우에는 반대 순서로 주 데이터베이스를 먼저 다운그레이드하고 보조 데이터베이스를 다운그레이드합니다. 데이터베이스를 다른 서비스 계층으로 업그레이드하거나 다운그레이드할 때 이 권장 사항이 적용됩니다.

> [!NOTE]
> 장애 조치 그룹 구성의 일부로 보조 데이터베이스를 만든 경우 보조 데이터베이스를 다운그레이드하지 않는 것이 좋습니다. 이렇게 하면 장애 조치가 활성화된 후 데이터 계층에서 일반 워크로드를 처리할 수 있을 만큼 충분한 용량을 갖출 수 있습니다.

> [!IMPORTANT]
> 장애 조치 그룹에서 주 데이터베이스는 보조 데이터베이스를 먼저 더 높은 계층으로 확장 하지 않는 한 상위 계층으로 확장할 수 없습니다. 보조 데이터베이스의 크기를 조정 하기 전에 주 데이터베이스를 확장 하려는 경우 다음 오류가 발생할 수 있습니다.
>
> `Error message: The source database 'Primaryserver.DBName' cannot have higher edition than the target database 'Secondaryserver.DBName'. Upgrade the edition on the target before upgrading the source.`
>

## <a name="preventing-the-loss-of-critical-data"></a>중요한 데이터 손실 방지

광역 네트워크의 높은 대기 시간으로 인해 연속 복사는 비동기 복제 메커니즘을 사용합니다. 비동기 복제를 수행하면 오류가 발생하는 경우에 일부 데이터 손실은 불가피합니다. 그러나 일부 애플리케이션은 데이터 손실이 없어야 합니다. 이러한 중요한 업데이트를 보호하기 위해 애플리케이션 개발자는 트랜잭션을 커밋한 후 즉시 [sp_wait_for_database_copy_sync](/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync) 시스템 프로시저를 호출할 수 있습니다. **sp_wait_for_database_copy_sync** 호출은 마지막으로 커밋된 트랜잭션이 보조 데이터베이스로 전송될 때까지 호출 스레드를 차단합니다. 그러나 전송된 트랜잭션이 보조 데이터베이스에서 재생 및 커밋될 때까지 기다리지 않습니다. **sp_wait_for_database_copy_sync**는 그 범위가 특정 연속 복사 링크로 한정됩니다. 주 데이터베이스에 대한 연결 권한이 있는 모든 사용자는 이 프로시저를 호출할 수 있습니다.

> [!NOTE]
> **sp_wait_for_database_copy_sync**는 장애 조치 후 데이터 손실을 방지하지만 읽기 액세스를 위한 전체 동기화를 보장하지 않습니다. **sp_wait_for_database_copy_sync** 프로시저 호출로 인한 지연은 심각할 수 있으며 호출 시 트랜잭션 로그의 크기에 따라 달라집니다.

## <a name="monitoring-geo-replication-lag"></a>지역에서 복제 지연 모니터링

RPO에 대해 지연 시간을 모니터링 하려면 *replication_lag_sec* 열의 [sys.dm_geo_replication_link_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database) 주 데이터베이스에서. 주 복제본에서 커밋 및 보조 복제본에서 지속형 트랜잭션 간의 초 지연 보여 줍니다. 예: 1 초 지연의 값을 사용 하는 경우 주 지금은 가동 중단의 영향을 받는 장애 조치를 시작 하 고 가장 최근의 전환의 1 초 저장 되지 것입니다 하는 경우 의미 합니다. 

즉, 보조 데이터베이스에서 읽을 수 있는 보조 데이터베이스에 적용 된 주 데이터베이스에서 변경 내용에 대해 지연 시간을 측정 하려면 비교 *last_commit* 주 복제본에서 동일한 값을 사용 하 여 보조 데이터베이스 시간 데이터베이스입니다.

> [!NOTE]
> 때로는 *replication_lag_sec* 주 데이터베이스에 주 데이터베이스는 현재 얼마나 보조가 알지 즉 NULL 값이 있습니다.   프로세스 다시 시작 해야 된 후 일반적으로 발생이 일시적인 현상일 수 있습니다. 하는 경우 응용 프로그램을 경고 하는 것이 좋습니다.는 *replication_lag_sec* 오랜 시간에 대 한 NULL을 반환 합니다. 보조 데이터베이스 영구 연결 오류로 인해 주 데이터베이스와 통신할 수 없습니다를 나타냅니다. 도 간의 차이 일으킬 수 있는 조건이 있습니다 *last_commit* 커질 주 데이터베이스 및 보조 복제본에서 시간입니다. 예: 커밋 후 오랜 기간 동안 변경 하지 않고 주 복제본에서 설정 되는 경우 차이점 0를 빠르게 반환 하기 전에 큰 값까지 이동 합니다. 이러한 두 값 간의 차이 오랜 시간 동안 큰 유지 되 면 오류 조건을 고려 합니다.


## <a name="programmatically-managing-active-geo-replication"></a>활성 지역 복제를 프로그래밍 방식으로 관리

앞서 설명한 것처럼 Azure PowerShell 및 REST API를 사용하여 활성 지역 복제를 프로그래밍 방식으로 관리할 수 있습니다. 다음 표는 사용 가능한 명령의 집합을 보여 줍니다. 활성 지역 복제는 관리를 위해 [Azure SQL Database REST API](https://docs.microsoft.com/rest/api/sql/) 및 [Azure PowerShell cmdlet](https://docs.microsoft.com/powershell/azure/overview)을 비롯한 Azure Resource Manager API 세트를 포함합니다. 이러한 API는 리소스 그룹을 사용해야 하며 RBAC(역할 기반 보안)를 지원합니다. 액세스 역할을 구현하는 방법에 대한 자세한 내용은 [Azure 역할 기반 Access Control](../role-based-access-control/overview.md)을 참조하세요.

### <a name="t-sql-manage-failover-of-single-and-pooled-databases"></a>T-SQL: 단일 및 풀링된 데이터베이스의 장애 조치(failover) 관리

> [!IMPORTANT]
> 이러한 Transact-SQL 명령은 활성 지역 복제에만 적용되고 장애 조치(failover) 그룹에는 적용되지 않습니다. 따라서 장애 조치(failover) 그룹만 지원하는 Managed Instance에도 적용되지 않습니다.

| 명령 | 설명 |
| --- | --- |
| [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current) |기존 데이터베이스에 대한 보조 데이터베이스를 만들고 데이터 복제를 시작하려면 ADD SECONDARY ON SERVER 인수를 사용합니다. |
| [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current) |장애 조치를 시작하기 위해 보조 데이터베이스를 기본 데이터베이스로 전환하려면 FAILOVER 또는 FORCE_FAILOVER_ALLOW_DATA_LOSS를 사용합니다. |
| [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current) |SQL Database와 지정된 보조 데이터베이스 간의 데이터 복제를 종료하려면 REMOVE SECONDARY ON SERVER를 사용합니다. |
| [sys.geo_replication_links](/sql/relational-databases/system-dynamic-management-views/sys-geo-replication-links-azure-sql-database) |Azure SQL Database 서버의 각 데이터베이스에 대한 모든 기존 복제 링크에 대한 정보를 반환합니다. |
| [sys.dm_geo_replication_link_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database) |지정된 SQL Database의 복제 링크에 대한 마지막 복제 시간, 마지막 복제 지연 및 기타 정보를 가져옵니다. |
| [sys.dm_operation_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) |복제 링크의 상태를 비롯한 모든 데이터베이스 작업에 대한 상태를 표시합니다. |
| [sp_wait_for_database_copy_sync](/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync) |커밋된 모든 트랜잭션이 복제되고 활성 보조 데이터베이스에서 승인될 때까지 애플리케이션이 대기하도록 합니다. |
|  | |

### <a name="powershell-manage-failover-of-single-and-pooled-databases"></a>PowerShell: 단일 및 풀링된 데이터베이스의 장애 조치(failover) 관리

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure Resource Manager 모듈은 Azure SQL 데이터베이스에서 계속 지원되지만 향후 모든 개발은 Az.Sql 모듈에 대해 진행됩니다. 이러한 cmdlet에 대한 내용은 [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)을 참조합니다. Az 모듈과 AzureRm 모듈에서 명령의 인수는 실질적으로 동일합니다.

| Cmdlet | 설명 |
| --- | --- |
| [Get-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabase) |하나 이상의 데이터베이스를 가져옵니다. |
| [New-AzSqlDatabaseSecondary](https://docs.microsoft.com/powershell/module/az.sql/new-azsqldatabasesecondary) |기존 데이터베이스에 대한 보조 데이터베이스를 만들고 데이터 복제를 시작합니다. |
| [Set-AzSqlDatabaseSecondary](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabasesecondary) |장애 조치를 시작하기 위해 보조 데이터베이스로 전환합니다. |
| [Remove-AzSqlDatabaseSecondary](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqldatabasesecondary) |SQL Database와 지정된 보조 데이터베이스 간의 데이터 복제를 종료합니다. |
| [Get-AzSqlDatabaseReplicationLink](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasereplicationlink) |Azure SQL Database와 리소스 그룹 또는 SQL Server 간의 지역에서 복제 링크를 가져옵니다. |
|  | |

> [!IMPORTANT]
> 샘플 스크립트는 [활성 지역 복제를 사용하여 단일 데이터베이스 구성 및 장애 조치(failover)](scripts/sql-database-setup-geodr-and-failover-database-powershell.md) 및 [활성 지역 복제를 사용하여 풀링된 데이터베이스 구성 및 장애 조치(failover)](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md)를 참조하세요.

### <a name="rest-api-manage-failover-of-single-and-pooled-databases"></a>REST API: 단일 및 풀링된 데이터베이스의 장애 조치(failover) 관리

| API | 설명 |
| --- | --- |
| [데이터베이스 생성 또는 업데이트(createMode=Restore)](https://docs.microsoft.com/rest/api/sql/databases/createorupdate) |주 보조 데이터베이스 또는 보조 데이터베이스를 만들거나, 업데이트하거나, 복원합니다. |
| [데이터베이스 만들기 또는 업데이트 상태 가져오기](https://docs.microsoft.com/rest/api/sql/databases/createorupdate) |만들기 작업 동안 상태를 반환합니다. |
| [보조 데이터베이스를 주 데이터베이스로 설정(계획된 장애 조치(failover))](https://docs.microsoft.com/rest/api/sql/replicationlinks/failover) |현재 주 데이터베이스에서 장애 조치(failover)를 통해 주 데이터베이스로 사용할 보조 데이터베이스를 설정합니다. **이 옵션은 Managed Instance에 대해 지원되지 않습니다.**|
| [보조 데이터베이스를 주 데이터베이스로 설정(계획되지 않은 장애 조치(failover))](https://docs.microsoft.com/rest/api/sql/replicationlinks/failoverallowdataloss) |현재 주 데이터베이스에서 장애 조치(failover)를 통해 주 데이터베이스로 사용할 보조 데이터베이스를 설정합니다. 이 작업으로 인해 데이터가 손실될 수 있습니다. **이 옵션은 Managed Instance에 대해 지원되지 않습니다.**|
| [복제 링크 가져오기](https://docs.microsoft.com/rest/api/sql/replicationlinks/get) |지역에서 복제 파트너 관계의 지정된 SQL 데이터베이스에 대한 특정 복제 링크를 가져옵니다. sys.geo_replication_links 카탈로그 뷰에 표시되는 정보를 검색합니다. **이 옵션은 Managed Instance에 대해 지원되지 않습니다.**|
| [복제 링크 - 데이터베이스 기준 목록](https://docs.microsoft.com/rest/api/sql/replicationlinks/listbydatabase) | 지역에서 복제 파트너 관계의 지정된 SQL 데이터베이스에 대한 모든 복제 링크를 가져옵니다. sys.geo_replication_links 카탈로그 뷰에 표시되는 정보를 검색합니다. |
| [복제 링크 삭제](https://docs.microsoft.com/rest/api/sql/replicationlinks/delete) | 데이터베이스 복제 링크를 삭제합니다. 장애 조치(failover) 중에 수행할 수 없습니다. |
|  | |

## <a name="next-steps"></a>다음 단계

- 샘플 스크립트에 대해서는 다음을 참조하세요.
  - [활성 지역 복제를 사용하여 단일 데이터베이스 구성 및 장애 조치(Failover)](scripts/sql-database-setup-geodr-and-failover-database-powershell.md)
  - [활성 지역 복제를 사용하여 풀된 데이터베이스 구성 및 장애 조치(failover)](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md)
- SQL Database는 자동 장애 조치(failover) 그룹도 지원합니다. 자세한 내용은 [자동 장애 조치(failover) 그룹](sql-database-auto-failover-group.md) 사용을 참조하세요.
- 비즈니스 연속성의 개요 및 시나리오를 보려면 [비즈니스 연속성 개요](sql-database-business-continuity.md)
- Azure SQL Database 자동화 백업에 대한 자세한 내용은 [SQL Database 자동화 백업](sql-database-automated-backups.md)을 참조하세요.
- 복구를 위해 자동화된 백업을 사용하는 방법을 알아보려면 [서비스에서 시작한 백업에서 데이터베이스 복원](sql-database-recovery-using-backups.md)을 참조하세요.
- 새로운 주 서버 및 데이터베이스의 인증 요구 사항에 대해 알아보려면 [재해 복구 후의 SQL Database 보안](sql-database-geo-replication-security-config.md)을 참조하세요.
