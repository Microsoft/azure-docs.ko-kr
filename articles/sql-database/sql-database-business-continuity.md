---
title: 클라우드 무중단 업무 방식 - 데이터베이스 복구 - SQL Database | Microsoft Docs
description: Azure SQL Database에서 클라우드 무중단 업무 방식 및 데이터베이스 복구를 지원하고 중요 업무용 클라우드 애플리케이션을 계속해서 실행할 수 있도록 하는 방법을 알아봅니다.
keywords: 무중단 업무 방식, 클라우드 무중단 업무 방식, 데이터베이스 재해 복구, 데이터베이스 복구
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: WenJason
ms.author: v-jay
ms.reviewer: mathoma, carlrab
manager: digimobile
origin.date: 04/04/2019
ms.date: 04/15/2019
ms.openlocfilehash: dfa5d4cb2d782f1466329300157a64fd17765460
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61412346"
---
# <a name="overview-of-business-continuity-with-azure-sql-database"></a>Azure SQL Database의 비즈니스 연속성 개요

Azure SQL Database의 **비즈니스 연속성**은 비즈니스가 중단, 특히 자사의 컴퓨팅 인프라에서 계속 운영할 수 있도록 하는 메커니즘, 정책 및 절차를 나타냅니다. 대부분의 경우 Azure SQL Database는 클라우드 환경에서 발생할 수 있는 중단 이벤트를 처리하고 애플리케이션 및 비즈니스 프로세스를 계속 실행합니다. 그러나 SQL Database에서 처리할 수 없는 몇 가지 중단 이벤트는 다음과 같습니다.

- 사용자가 실수로 테이블 행을 삭제하거나 업데이트했습니다.
- 악의적인 공격자가 데이터를 삭제하거나 데이터베이스를 삭제하는 데 성공했습니다.
- 지진으로 인해 정전되었거나 데이터 센터가 일시적으로 사용할 수 없게 되었습니다.

이러한 경우는 Azure SQL Database에서 제어할 수 없으므로 데이터를 복구하고 애플리케이션을 계속 실행할 수 있도록 하는 SQL Database의 비즈니스 연속성 기능을 사용해야 합니다.

이 개요에서는 Azure SQL Database에서 비즈니스 연속성 및 재해 복구를 위해 제공하는 기능에 대해 설명합니다. 데이터 손실을 유발하거나 데이터베이스 및 애플리케이션을 사용할 수 없게 만들 수 있는 중단 이벤트에서의 복구와 관련된 옵션, 권장 사항 및 자습서에 대해 알아봅니다. 사용자 또는 애플리케이션 오류가 데이터 무결성에 영향을 주거나, Azure 지역에 가동 중단이 발생하거나, 애플리케이션에 유지 관리가 필요할 때 수행할 작업을 알아봅니다.

## <a name="sql-database-features-that-you-can-use-to-provide-business-continuity"></a>비즈니스 연속성을 제공하는 데 사용할 수 있는 SQL Database 기능

데이터베이스 관점에서 다음 네 가지 주요 잠재적 중단 시나리오가 있습니다.

- 로컬 하드웨어 또는 소프트웨어 오류 - 디스크 드라이브 오류와 같이 데이터베이스 노드에 영향을 미칩니다.
- 데이터 손상 또는 삭제 - 일반적으로 애플리케이션 버그 또는 사람의 실수로 인해 발생합니다. 이러한 오류는 본질적으로 애플리케이션에 따라 다르며, 일반적으로 인프라에서 자동으로 검색하거나 완화할 수 없습니다.
- 데이터 센터 중단 - 아마도 자연 재해로 인해 발생했을 수 있습니다. 이 시나리오에는 대체 데이터 센터로의 애플리케이션 장애 조치가 포함된 일부 수준의 지리적 중복성이 필요합니다.
- 업그레이드 또는 유지 관리 오류 – 애플리케이션이나 데이터베이스에 대한 계획된 업그레이드 또는 유지 관리 중에 발생하는 예기치 않은 문제로 인해 이전 데이터베이스 상태로 신속하게 롤백해야 할 수 있습니다.

SQL Database는 이러한 시나리오를 완화할 수 있는 자동화된 백업 및 선택적 데이터베이스 복제를 포함하여 몇 가지 비즈니스 연속성 기능을 제공합니다. 먼저 SQL Database [고가용성 아키텍처](sql-database-high-availability.md)에서 비즈니스 프로세스에 영향을 줄 수 있는 몇 가지 중단 이벤트에 대해 99.99%의 가용성과 복원력을 제공하는 방법을 이해해야 합니다.
그런 다음, SQL Database 고가용성 아키텍처에서 처리할 수 없는 중단 이벤트에서 복구하는 데 사용할 수 있는 다음 추가 메커니즘에 대해 알아볼 수 있습니다.

- [temporal 테이블](sql-database-temporal-tables.md)을 사용하면 특정 시점의 행 버전을 복원할 수 있습니다.
- [기본 제공 자동화된 백업](sql-database-automated-backups.md) 및 [특정 시점 복원](sql-database-recovery-using-backups.md#point-in-time-restore)을 사용하면 전체 데이터베이스를 지난 35일 이내의 특정 시점으로 복원할 수 있습니다.
- **SQL Database 서버가 삭제되지 않은 경우** [삭제된 데이터베이스를 삭제된 시점으로 복원](sql-database-recovery-using-backups.md#deleted-database-restore)할 수 있습니다.
- [장기 백업 보존](sql-database-long-term-retention.md)을 사용하면 백업을 최대 10년 동안 유지할 수 있습니다.
- [활성 지역 복제](sql-database-active-geo-replication.md)를 사용하면 데이터 센터 중단 또는 애플리케이션 업그레이드 시 읽기 쉬운 복제본을 만들고 복제본에 수동으로 장애 조치(failover)할 수 있습니다.
- [자동 장애 조치(failover) 그룹](sql-database-auto-failover-group.md#auto-failover-group-terminology-and-capabilities)을 사용하면 데이터 센터 중단 시 애플리케이션에서 빠른 재해 복구를 수행할 수 있습니다.

이러한 기능은 최근 트랜잭션에 대한 ERT(예상 복구 시간) 및 잠재적 데이터 손실에 대해 각기 다른 특성을 갖습니다. 이러한 옵션을 이해하면 적절한 옵션을 선택할 수 있습니다. 대부분의 시나리오에서 이러한 옵션을 함께 사용할 수 있습니다. 비즈니스 연속성 계획을 개발할 때는 중단 이벤트가 발생한 후 애플리케이션이 완전히 복구되기까지 허용되는 최대 시간을 이해해야 합니다. 애플리케이션을 완전히 복구하는 데 필요한 시간을 RTO(복구 시간 목표)라고 합니다. 또한 중단 이벤트가 발생한 후 복구될 때 애플리케이션에서 손실을 허용할 수 있는 최근 데이터 업데이트의 최대 기간(시간 간격)도 이해해야 합니다. 손실될 수 있는 업데이트 기간을 RPO(복구 지점 목표)라고 합니다.

다음 표에서 가장 일반적인 시나리오에 대 한 각 서비스 계층의 ERT 및 RPO를 비교합니다.

| 기능 | Basic | 표준 | Premium | 범용 | 중요 비즈니스용
| --- | --- | --- | --- |--- |--- |
| 백업에서 특정 시점 복원 |7일 이내의 모든 복원 지점 |35일 이내의 모든 복원 지점 |35일 이내의 모든 복원 지점 |구성된 기간 이내의 모든 복원 지점(최대 35일)|구성된 기간 이내의 모든 복원 지점(최대 35일)|
| 지리적으로 복제된 백업에서 지역 복원 |ERT < 12시간<br> RPO < 1시간 |ERT < 12시간<br>RPO < 1시간 |ERT < 12시간<br>RPO < 1시간 |ERT < 12시간<br>RPO < 1시간|ERT < 12시간<br>RPO < 1시간|
| 자동 장애 조치 그룹 |RTO = 1시간<br>RPO 5초 미만 |RTO = 1시간<br>RPO 5초 미만 |RTO = 1시간<br>RPO 5초 미만 |RTO = 1시간<br>RPO 5초 미만|RTO = 1시간<br>RPO 5초 미만|
| 수동으로 데이터베이스 장애 조치 |ERT 30 = s<br>RPO 5초 미만 |ERT 30 = s<br>RPO 5초 미만 |ERT 30 = s<br>RPO 5초 미만 |ERT 30 = s<br>RPO 5초 미만|ERT 30 = s<br>RPO 5초 미만|

> [!NOTE]
> *수동으로 데이터베이스 장애 조치* 참조를 해당 지역에서 복제 보조를 사용 하 여 단일 데이터베이스의 장애 조치 하는 [계획 되지 않은 모드](sql-database-active-geo-replication.md#active-geo-replication-terminology-and-capabilities)합니다.

## <a name="recover-a-database-to-the-existing-server"></a>기존 서버에 데이터베이스 복구

SQL Database는 데이터 손실로부터 비즈니스를 보호하기 위해 매주 전체 데이터베이스 백업, 일반적으로 12시간 간격의 차등 데이터베이스 백업 및 5-10분 간격의 트랜잭션 로그 백업을 조합하여 자동으로 수행합니다. 백업은 7일 동안 백업이 저장되는 기본 DTU 서비스 계층을 제외한 모든 서비스 계층에 대해 35일 동안 RA-GRS 스토리지에 저장됩니다. 자세한 내용은 [자동 데이터베이스 백업](sql-database-automated-backups.md)을 참조하세요. Azure Portal, PowerShell 또는 REST API를 사용하여 자동화된 백업의 기존 데이터베이스를 특정 이전 시점의 상태로 동일한 SQL Database 서버에 새 데이터베이스로 복원할 수 있습니다. 자세한 내용은 [특정 시점 복원](sql-database-recovery-using-backups.md#point-in-time-restore)을 참조하세요.

지원되는 최대 PITR(Point-in-Time 복원) 보존 기간이 애플리케이션에 대해 충분하지 않을 경우에 데이터베이스에 대한 LTR(장기 보존) 정책을 구성하여 확장할 수 있습니다. 자세한 내용은 [장기 백업 보존](sql-database-long-term-retention.md)을 참조하세요.

이러한 자동 데이터베이스 백업을 사용하여 다양한 중단 이벤트에서 데이터 센터 내로 및 다른 데이터 센터로 데이터베이스를 복구할 수 있습니다. 복구 시간은 일반적으로 12시간 미만입니다. 대규모이거나 활성 데이터베이스를 복구하는 데 더 많은 시간이 걸릴 수 있습니다. 자동 데이터베이스 백업을 사용할 경우 예상 복구 시간은 동일한 지역에서 동시에 복구되는 총 데이터베이스 수, 데이터베이스 크기, 트랜잭션 로그 크기 및 네트워크 대역폭에 따라 좌우됩니다. 복구 시간에 대한 자세한 내용은 [데이터베이스 복구 시간](sql-database-recovery-using-backups.md#recovery-time)을 참조하세요. 다른 데이터 영역으로 복구할 때 잠재적인 데이터 손실은 지역 중복 백업을 사용하여 1시간으로 제한됩니다.

다음과 같은 애플리케이션의 경우 자동화된 백업 및 [특정 시점 복원](sql-database-recovery-using-backups.md#point-in-time-restore)을 비즈니스 연속성 및 복구 메커니즘으로 사용합니다.

- 중요 업무용으로 간주되지 않습니다.
- 연계된 SLA가 없으므로 24시간 이상의 가동 중지가 발생해도 재무적 책임이 없습니다.
- 데이터 변경(시간당 낮은 트랜잭션) 속도가 느리고, 최대 1시간의 데이터 변경 내용 손실은 비교적 허용 가능한 데이터 손실에 해당합니다.
- 비용에 민감합니다.

빠른 복구가 필요한 경우 [활성 지역 복제](sql-database-active-geo-replication.md) 또는 [자동 장애 조치(failover) 그룹](sql-database-auto-failover-group.md)을 사용합니다. 35일이 지난 기간에서 데이터를 복구해야 하는 경우 [장기 보존](sql-database-long-term-retention.md)을 사용합니다.

## <a name="recover-a-database-to-another-region"></a>데이터베이스를 다른 지역으로 복구

드문 경우지만 Azure 데이터 센터에서 가동 중단이 발생할 수 있습니다. 가동 중단이 발생하면 몇 분 내지 몇 시간 동안 지속될 수 있는 업무 중단이 발생합니다.

- 한 가지 방법은 데이터 센터 가동 중단이 끝날 때 데이터베이스가 다시 온라인 상태가 될 때까지 기다리는 것입니다. 이러한 방법은 데이터베이스의 오프라인 유지가 가능한 애플리케이션에 적합합니다. 예를 들어 지속적으로 작업할 필요가 없는 개발 프로젝트 또는 무료 평가판이 있습니다. 데이터 센터가 가동 중단되면 중단이 얼마나 지속될지 알 수 없으므로 이 옵션은 잠시 데이터베이스가 필요 없어도 되는 경우에만 적합합니다.
- 또 다른 옵션은 [지역 중복 데이터베이스 백업](sql-database-recovery-using-backups.md#geo-restore)(지역 복원)을 사용하여 모든 Azure 지역의 모든 서버에서 데이터베이스를 복원하는 것입니다. 지리적 복원은 지역 중복 백업을 해당 원본으로 사용하고 가동 중단으로 인해 데이터베이스 또는 데이터 센터에 액세스할 수 없는 경우에도 데이터베이스를 복구하는 데 사용될 수 있습니다.
- 마지막으로 복구할 수 있습니다 신속 하 게 가동 중단에서 사용 하 여 지역 보조 복제본을 구성한 경우 [활성 지역 복제](sql-database-active-geo-replication.md) 요소나 [자동 장애 조치 그룹](sql-database-auto-failover-group.md) 데이터베이스 또는 데이터베이스에 대 한 합니다. 이러한 기술의 선택에 따라 수동 또는 자동 장애 조치(failover)를 사용할 수 있습니다. 장애 조치(failover) 자체는 몇 초밖에 걸리지 않지만 서비스에서 장애 조치(failover)를 활성화하려면 최소 1시간이 걸립니다. 가동 중단 규모가 장애 조치(failover)를 수행하기에 적합한지를 확인해야 하기 때문입니다. 또한 비동기 복제의 특성상 장애 조치(failover)로 인해 데이터가 약간 손실될 수도 있습니다. 자동 장애 조치(failover) RTO 및 RPO 세부 정보는 이 문서 앞부분의 표를 참조하세요.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-protecting-important-DBs-from-regional-disasters-is-easy/player]
>
> [!IMPORTANT]
> 활성 지역 복제 및 자동 장애 조치(failover) 그룹을 사용하려면 SQL Server에서 구독 소유자이거나 관리 권한을 보유해야 합니다. 구독에 대한 권한을 통해 Azure Portal, PowerShell 또는 REST API를 사용하거나, Azure 구독 사용 권한을 사용하거나, SQL Server 사용 권한이 있는 Transact-SQL을 사용하여 구성하고 장애 조치할 수 있습니다.

애플리케이션이 다음 조건에 맞는 경우 자동 장애 조치(failover) 그룹을 사용합니다.

- 중요 업무용입니다.
- 12시간 이상의 가동 중지 시간을 허용하지 않는 SLA(서비스 수준 약정)가 있습니다.
- 가동 중지는 재무적 책임을 유발할 수 있습니다.
- 데이터 변경률이 높고 1시간에 해당하는 데이터 손실은 허용할 수 없는 수준입니다.
- 활성 지역 복제를 사용할 때 발생하는 추가적인 비용이 잠재적인 재무적 책임과 연계된 비즈니스 손실보다 낮습니다.

작업을 수행하는 시기, 복구하는 데 걸리는 시간 및 발생하는 데이터 손실의 양은 애플리케이션에서 비즈니스 연속성 기능을 어떤 방법으로 사용할 것인지에 따라 달라집니다. 실제로 애플리케이션 요구 사항에 따라 데이터베이스 백업 및 활성 지역 복제를 함께 사용하도록 선택할 수 있습니다. 이러한 비즈니스 지속성 기능을 사용하는 독립 실행형 데이터베이스 및 탄력적 풀에 대한 애플리케이션 디자인 고려 사항에 대한 논의는 [클라우드 재해 복구를 위한 애플리케이션 디자인](sql-database-designing-cloud-solutions-for-disaster-recovery.md) 및 [탄력적 풀 재해 복구 전략](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md)을 참조하세요.


다음 섹션에서는 데이터베이스 백업 또는 활성 지역 복제를 사용하는 복구 단계를 대략적으로 설명합니다. 계획 요구 사항, 복구 후 단계 및 가동 중단을 시뮬레이션하여 재해 복구 훈련을 수행하는 방법을 포함하는 자세한 단계는 [중단 상태에서 SQL Database 복구](sql-database-disaster-recovery.md)를 참조하세요.

### <a name="prepare-for-an-outage"></a>가동 중단에 대비

어떤 비즈니스 연속성 기능을 사용하든 관계없이 다음 작업을 수행해야 합니다.

- 서버 수준 IP 방화벽 규칙, 로그인 및 master 데이터베이스 수준 사용 권한을 포함한 대상 서버를 식별하고 준비합니다.
- 클라이언트 및 클라이언트 애플리케이션을 새 서버로 리디렉션하는 방법을 결정합니다.
- 감사 설정 및 경고와 같은 다른 종속성을 문서화합니다.

적절한 준비 없이 장애 조치나 데이터베이스 복구 이후에 애플리케이션을 온라인 상태로 전환하면 추가 시간이 소요되고, 바쁜 업무 중 문제 해결이 필요할 수도 있어 비효율적입니다.

### <a name="fail-over-to-a-geo-replicated-secondary-database"></a>지역에서 복제된 보조 데이터베이스로 장애 조치

활성 지역 복제 또는 자동 장애 조치 그룹을 사용 하 여 복구 메커니즘으로는, 하는 경우 자동 장애 조치 정책을 구성 하 하거나 사용할 수 있습니다 [수동 장애 조치](sql-database-active-geo-replication-portal.md#initiate-a-failover)합니다. 일단 장애 조치가 시작되면 보조 데이터베이스는 새로운 주 데이터베이스로 승격되며, 새 트랜잭션을 기록하고 쿼리에 응답할 준비를 갖춥니다. 이때 최소한의 데이터 손실이 있을 수 있으나 아직 복제되지 않습니다. 장애 조치(failover) 프로세스 디자인에 대한 자세한 내용은 [클라우드 재해 복구를 위해 애플리케이션 디자인](sql-database-designing-cloud-solutions-for-disaster-recovery.md)을 참조하세요.

> [!NOTE]
> 데이터 센터가 다시 온라인 상태가 되면 이전 주 데이터베이스는 새 주 데이터베이스에 자동으로 다시 연결되고 보조 데이터베이스가 됩니다. 주 데이터베이스를 다시 원래 지역으로 재배치해야 할 경우 계획된 장애 조치를 수동으로 시작할 수 있습니다(장애 복구).

### <a name="perform-a-geo-restore"></a>지역 복원 수행

지역 중복 저장소(기본적으로 사용)와 함께 자동화된 백업을 사용하는 경우 [지역 복원](sql-database-disaster-recovery.md#recover-using-geo-restore)을 사용하여 데이터베이스를 복구할 수 있습니다. 일반적으로 복구는 12시간 이내에 수행됩니다. 이때 마지막 로그 백업의 실행 및 복제를 기준으로 최대 1시간의 데이터 손실이 있을 수 있습니다. 복구가 완료될 때까지 데이터베이스는 어떤 트랜잭션도 기록할 수 없으며 쿼리에 응답할 수 없습니다. 지역 복원은 데이터베이스를 마지막으로 사용할 수 있는 시점으로만 복원합니다.

> [!NOTE]
> 애플리케이션이 복구된 데이터베이스로 전환하기 전에 데이터 센터가 다시 온라인 상태가 되면 복구를 취소할 수 있습니다.

### <a name="perform-post-failover--recovery-tasks"></a>사후 장애 조치(failover)/복구 작업 수행

복구 메커니즘에서 복구한 후에는 사용자 및 애플리케이션이 다시 실행되기 전에 다음과 같은 추가 작업을 수행해야 합니다.

- 클라이언트 및 클라이언트 애플리케이션을 새 서버 및 복원된 데이터베이스로 리디렉션
- 사용자가 연결할 수 있는 또는 [데이터베이스 수준 방화벽](sql-database-firewall-configure.md#manage-server-level-ip-firewall-rules-using-the-azure-portal)을 사용하여 적절한 규칙을 설정할 수 있는 적절한 서버 수준 IP 방화벽 규칙이 적용되고 있는지 확인합니다.
- 적절한 로그인 및 master 데이터베이스 수준 사용 권한이 설정되었는지 확인합니다(또는 [포함된 사용자](https://docs.microsoft.com/sql/relational-databases/security/contained-database-users-making-your-database-portable)사용).
- 필요에 따라 감사를 구성합니다.
- 필요에 따라 경고를 구성합니다.

> [!NOTE]
> 장애 조치(failover) 그룹을 사용 중이며 읽기-쓰기 수신기를 사용하여 데이터베이스에 연결하는 경우에는 장애 조치(failover) 후의 리디렉션이 애플리케이션에 투명하게 자동으로 수행됩니다.

## <a name="upgrade-an-application-with-minimal-downtime"></a>최소 가동 중단으로 애플리케이션 업그레이드

애플리케이션 업그레이드와 같은 계획된 유지 관리로 인해 애플리케이션을 오프라인 상태로 전환해야 하는 경우도 있습니다. [애플리케이션 업그레이드 관리](sql-database-manage-application-rolling-upgrade.md)에서는 활성 지역 복제를 사용하여 클라우드 애플리케이션의 롤링 업그레이드를 사용하도록 설정함으로써 업그레이드 기간에 가동 중지 시간을 최소화하고 오류가 발생한 경우 복구 경로를 제공하는 방법을 설명합니다.

## <a name="next-steps"></a>다음 단계

독립 실행형 데이터베이스 및 탄력적 풀에 대한 애플리케이션 디자인 고려 사항에 대한 논의는 [클라우드 재해 복구를 위한 애플리케이션 디자인](sql-database-designing-cloud-solutions-for-disaster-recovery.md) 및 [탄력적 풀 재해 복구 전략](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md)을 참조하세요.
