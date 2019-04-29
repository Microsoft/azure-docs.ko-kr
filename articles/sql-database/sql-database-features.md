---
title: Azure SQL Database 기능 비교 | Microsoft Docs
description: 이 문서에서는 다양한 Azure SQL Database 버전에서 제공하는 SQL Server 기능을 비교합니다.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: bonova, sstein
manager: craigg
ms.date: 02/08/2019
ms.openlocfilehash: 857b1059df2edf34e58d38d335725e27159977a0
ms.sourcegitcommit: a95dcd3363d451bfbfea7ec1de6813cad86a36bb
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62738808"
---
# <a name="feature-comparison-azure-sql-database-versus-sql-server"></a>기능 비교: Azure SQL Database 및 SQL Server

Azure SQL Database는 SQL Server와 공용 코드 베이스를 공유합니다. Azure SQL Database에서 지원하는 SQL Server의 기능은 사용자가 만드는 Azure SQL 데이터베이스 형식에 따라 달라집니다. Azure SQL Database를 사용하면 데이터베이스를 [Managed Instance](sql-database-managed-instance.md) 또는 탄력적 풀의 일부분으로 만들거나 단일 데이터베이스로 만들 수 있습니다.

Microsoft는 Azure SQL Database에 계속해서 기능을 추가하고 있습니다. Azure용 서비스 업데이트 웹 페이지에서 다음 필터를 사용하여 최신 업데이트를 확인하세요.

- [SQL Database 서비스](https://azure.microsoft.com/updates/?service=sql-database)에 대해 필터링되었습니다.
- SQL Database 기능의 [GA(General Availability) 공지](https://azure.microsoft.com/updates/?service=sql-database&update-type=general-availability) 에 대해 필터링되었습니다.

## <a name="sql-server-feature-support-in-azure-sql-database"></a>Azure SQL Database에서 지원되는 SQL Server 기능

다음 표에서는 SQL Server의 주요 기능을 나열하고, 기능이 부분적으로 지원되는지 또는 완전히 지원되는지에 대한 정보 및 기능에 대한 자세한 정보로 링크를 제공합니다.

| **SQL 기능** | **단일 데이터베이스 및 탄력적 풀에서 지원** | **관리 되는 인스턴스에서 지원** |
| --- | --- | --- |
| [활성 지역 복제](sql-database-active-geo-replication.md) | 모든 대규모 이외의 계층을 서비스 하는 예- | 아니요([자동 장애 조치(failover) 그룹](sql-database-auto-failover-group.md) 참조) |
| [자동 장애 조치(failover) 그룹](sql-database-auto-failover-group.md) | 모든 대규모 이외의 계층을 서비스 하는 예- | 예, [공개 미리 보기 상태](sql-database-auto-failover-group.md)|
| [Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine) | 예 - [인증서 저장소](sql-database-always-encrypted.md) 및 [키 자격 증명 모음](sql-database-always-encrypted-azure-key-vault.md) 참조 | 예 - [인증서 저장소](sql-database-always-encrypted.md) 및 [키 자격 증명 모음](sql-database-always-encrypted-azure-key-vault.md) 참조 |
| [Always On 가용성 그룹](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server) | [고가용성](sql-database-high-availability.md)은 모든 데이터베이스에 포함됩니다. 재해 복구는 [Azure SQL Database의 비즈니스 연속성 개요](sql-database-business-continuity.md)에서 설명합니다. | [고가용성](sql-database-high-availability.md)은 모든 데이터베이스에 포함됩니다. 재해 복구는 [Azure SQL Database의 비즈니스 연속성 개요](sql-database-business-continuity.md)에서 설명합니다. |
| [데이터베이스 연결](https://docs.microsoft.com/sql/relational-databases/databases/attach-a-database) | 아닙니다. | 아닙니다. |
| [애플리케이션 역할](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/application-roles) | 예 | 예 |
|[감사](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine) | [예](sql-database-auditing.md)| [예](sql-database-managed-instance-auditing.md) |
| [자동 백업](sql-database-automated-backups.md) | 예 | 예 |
| [자동 조정(강제 진행 계획)](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning)| [예](sql-database-automatic-tuning.md)| [예](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning) |
| [자동 조정(인덱스)](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning)| [예](sql-database-automatic-tuning.md)| 아닙니다. |
| [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/what-is) | 예 | 예 |
| [BACPAC 파일(내보내기)](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application) | 예 - [SQL Database 내보내기](sql-database-export.md) 참조 | 예 |
| [BACPAC 파일(가져오기)](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database) | 예 - [SQL Database 가져오기](sql-database-import.md) 참조 | 예 |
| [BACKUP 명령](https://docs.microsoft.com/sql/t-sql/statements/backup-transact-sql) | 아니요. 시스템에서 시작한 자동 백업만 - [자동 백업](sql-database-automated-backups.md) 참조 | 시스템 시작 자동 백업 및 사용자 시작 복사 전용 백업 - [백업 차이점](sql-database-managed-instance-transact-sql-information.md#backup) 참조 |
| [기본 제공 함수](https://docs.microsoft.com/sql/t-sql/functions/functions) | 대부분 - 개별 함수 참조 | 예- [저장 프로시저, 함수, 트리거 차이점](sql-database-managed-instance-transact-sql-information.md#stored-procedures-functions-triggers) 참조 |
| [변경 데이터 캡처](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-data-capture-sql-server) | 아닙니다. | 예 |
| [변경 내용 추적](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-tracking-sql-server) | 예 |예 |
| [데이터 정렬 - 데이터베이스](https://docs.microsoft.com/sql/relational-databases/collations/set-or-change-the-database-collation) | 예 | 예 |
| [데이터 정렬 - 서버/인스턴스](https://docs.microsoft.com/sql/relational-databases/collations/set-or-change-the-server-collation) | 아니요, 기본 `SQL_Latin1_General_CP1_CI_AS` 항상 사용 됩니다. | 예, 설정할 수 있습니다 시기를 [인스턴스가 만들어집니다](scripts/sql-managed-instance-create-powershell-azure-resource-manager-template.md) 업데이트할 수 없습니다. |
| [Columnstore 인덱스](https://docs.microsoft.com/sql/relational-databases/indexes/columnstore-indexes-overview) | 예 - [프리미엄 계층, 표준 계층 - S3 이상, 범용 계층 및 중요 비즈니스 계층](https://docs.microsoft.com/sql/relational-databases/indexes/columnstore-indexes-overview) |예 |
| [CLR(공용 언어 런타임)](https://docs.microsoft.com/sql/relational-databases/clr-integration/common-language-runtime-clr-integration-programming-concepts) | 아닙니다. | 예, 하지만 파일 시스템 기능에 액세스 하지 않고 참조 [CLR 차이점](sql-database-managed-instance-transact-sql-information.md#clr) |
| [포함된 데이터베이스](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases) | 예 | 이상 [복원 지정 시간 복원을 비롯 한 결함으로 인해](sql-database-managed-instance-transact-sql-information.md#cannot-restore-contained-database) |
| [포함된 사용자](https://docs.microsoft.com/sql/relational-databases/security/contained-database-users-making-your-database-portable) | 예 | 예 |
| [흐름 제어 언어 키워드](https://docs.microsoft.com/sql/t-sql/language-elements/control-of-flow) | 예 | 예 |
| [데이터베이스 간 쿼리](https://docs.microsoft.com/sql/relational-databases/linked-servers/linked-servers-database-engine) | 아니요 - [탄력적 쿼리](sql-database-elastic-query-overview.md) 참조 | 예. 추가적으로 [탄력적 쿼리](sql-database-elastic-query-overview.md) |
| [데이터베이스 간 트랜잭션](https://docs.microsoft.com/sql/relational-databases/linked-servers/linked-servers-database-engine) | 아닙니다. | 예, 인스턴스 내에서. 참조 [server 차이로 연결](sql-database-managed-instance-transact-sql-information.md#linked-servers) 인스턴스 간 쿼리에 대 한 합니다. |
| [커서](https://docs.microsoft.com/sql/t-sql/language-elements/cursors-transact-sql) | 예 |예 |
| [데이터 압축](https://docs.microsoft.com/sql/relational-databases/data-compression/data-compression) | 예 |예 |
| [데이터베이스 메일](https://docs.microsoft.com/sql/relational-databases/database-mail/database-mail) | 아닙니다. | 예 |
| [DMS(데이터 마이그레이션 서비스)](https://docs.microsoft.com/sql/dma/dma-overview) | 예 | 예 |
| [데이터베이스 미러링](https://docs.microsoft.com/sql/database-engine/database-mirroring/database-mirroring-sql-server) | 아닙니다. | 아닙니다. |
| [데이터베이스 구성 설정](https://docs.microsoft.com/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) | 예 | 예 |
| [DQS(Data Quality Services)](https://docs.microsoft.com/sql/data-quality-services/data-quality-services) | 아닙니다. | 아닙니다. |
| [데이터베이스 스냅숏](https://docs.microsoft.com/sql/relational-databases/databases/database-snapshots-sql-server) | 아닙니다. | 아닙니다. |
| [데이터 형식](https://docs.microsoft.com/sql/t-sql/data-types/data-types-transact-sql) | 예 |예 |
| [DBCC 문](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-transact-sql) | 대부분 - 개별 문 참조 | 예 - [DBCC 차이점](sql-database-managed-instance-transact-sql-information.md#dbcc) 참조 |
| [DDL 문](https://docs.microsoft.com/sql/t-sql/statements/statements) | 대부분 - 개별 문 참조 | 예 - [T-SQL 차이점](sql-database-managed-instance-transact-sql-information.md) 참조 |
| [DDL 트리거](https://docs.microsoft.com/sql/relational-databases/triggers/ddl-triggers) | 데이터베이스에만 해당 |  예 |
| [분산된 파티션 뷰](https://docs.microsoft.com/sql/t-sql/statements/create-view-transact-sql#partitioned-views) | 아닙니다. | 예 |
| [분산된 트랜잭션 - MS DTC](https://docs.microsoft.com/sql/relational-databases/native-client-ole-db-transactions/supporting-distributed-transactions) | 아니요 - [탄력적 트랜잭션](sql-database-elastic-transactions-overview.md) 참조 |  아니요-참조 [server 차이로 연결](sql-database-managed-instance-transact-sql-information.md#linked-servers) |
| [DML 문](https://docs.microsoft.com/sql/t-sql/queries/queries) | 예 | 예 |
| [DML 트리거](https://docs.microsoft.com/sql/relational-databases/triggers/create-dml-triggers) | 대부분 - 개별 문 참조 |  예 |
| [DMV](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views) | 대부분 - 개별 DMV 참조 |  예 - [T-SQL 차이점](sql-database-managed-instance-transact-sql-information.md) 참조 |
|[동적 데이터 마스킹](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking)|[예](sql-database-dynamic-data-masking-get-started.md)| [예](sql-database-dynamic-data-masking-get-started.md) |
| [탄력적 풀](sql-database-elastic-pool.md) | 예 | 기본 제공 - 단일 Managed Instance는 동일한 리소스 풀을 공유하는 여러 데이터베이스를 유지할 수 있습니다. |
| [이벤트 알림](https://docs.microsoft.com/sql/relational-databases/service-broker/event-notifications) | 아니요 - [경고](sql-database-insights-alerts-portal.md) 참조 | 아닙니다. |
| [식](https://docs.microsoft.com/sql/t-sql/language-elements/expressions-transact-sql) |예 | 예 |
| [확장 이벤트](https://docs.microsoft.com/sql/relational-databases/extended-events/extended-events) | 일부 - [SQL Database의 확장 이벤트](sql-database-xevent-db-diff-from-svr.md) 참조 | 예 - [확장 이벤트 차이](sql-database-managed-instance-transact-sql-information.md#extended-events) 참조 |
| [확장된 저장 프로시저](https://docs.microsoft.com/sql/relational-databases/extended-stored-procedures-programming/creating-extended-stored-procedures) | 아닙니다. | 아닙니다. |
[파일 및 파일 그룹](https://docs.microsoft.com/sql/relational-databases/databases/database-files-and-filegroups) | 기본 파일 그룹만 해당 | 예 |
| [FileStream](https://docs.microsoft.com/sql/relational-databases/blob/filestream-sql-server) | 아닙니다. | 아닙니다. |
| [전체 텍스트 검색](https://docs.microsoft.com/sql/relational-databases/search/full-text-search) |  지원되지 않는 타사 단어 분리기 |지원되지 않는 타사 단어 분리기 |
| [함수](https://docs.microsoft.com/sql/t-sql/functions/functions) | 대부분 - 개별 함수 참조 | 예- [저장 프로시저, 함수, 트리거 차이점](sql-database-managed-instance-transact-sql-information.md#stored-procedures-functions-triggers) 참조 |
| [지역 복원](sql-database-recovery-using-backups.md#geo-restore) | 모든 대규모 이외의 계층을 서비스 하는 예- | 예-를 사용 하 여 [Azure PowerShell](https://medium.com/azure-sqldb-managed-instance/geo-restore-your-databases-on-azure-sql-instances-1451480e90fa)합니다. |
| [그래프 처리 중](https://docs.microsoft.com/sql/relational-databases/graphs/sql-graph-overview) | 예 | 예 |
| [메모리 내 최적화](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization) | 예- [프리미엄 계층 및 중요 비즈니스 계층만](sql-database-in-memory.md) | 예 - [중요 비즈니스용 계층만 해당](sql-database-managed-instance.md) |
| [JSON 데이터 지원](https://docs.microsoft.com/sql/relational-databases/json/json-data-sql-server) | [예](sql-database-json-features.md) | [예](sql-database-json-features.md) |
| [언어 요소](https://docs.microsoft.com/sql/t-sql/language-elements/language-elements-transact-sql) | 대부분 - 개별 요소 참조 |  예 - [T-SQL 차이점](sql-database-managed-instance-transact-sql-information.md) 참조 |
| [연결된 서버](https://docs.microsoft.com/sql/relational-databases/linked-servers/linked-servers-database-engine) | 아니요 - [탄력적 쿼리](sql-database-elastic-query-horizontal-partitioning.md) 참조 | SQL Server 및 SQL Database만 |
| [로그 전달](https://docs.microsoft.com/sql/database-engine/log-shipping/about-log-shipping-sql-server) | [고가용성](sql-database-high-availability.md)은 모든 데이터베이스에 포함됩니다. 재해 복구는 [Azure SQL Database의 비즈니스 연속성 개요](sql-database-business-continuity.md)에서 설명합니다. |[고가용성](sql-database-high-availability.md)은 모든 데이터베이스에 포함됩니다. 재해 복구는 [Azure SQL Database의 비즈니스 연속성 개요](sql-database-business-continuity.md)에서 설명합니다. |
| [MDS(Master Data Services)](https://docs.microsoft.com/sql/master-data-services/master-data-services-overview-mds) | 아닙니다. | 아닙니다. |
| [대량 가져오기에서 최소 로깅](https://docs.microsoft.com/sql/relational-databases/import-export/prerequisites-for-minimal-logging-in-bulk-import) | 아닙니다. | 아닙니다. |
| [시스템 데이터 수정](https://docs.microsoft.com/sql/relational-databases/databases/system-databases) | 아닙니다. | 예 |
| [OLE Automation](https://docs.microsoft.com/sql/database-engine/configure-windows/ole-automation-procedures-server-configuration-option) | 아닙니다. | 아닙니다. |
| [온라인 인덱스 작업](https://docs.microsoft.com/sql/relational-databases/indexes/perform-index-operations-online) | 예 | 예 |
| [OPENDATASOURCE](https://docs.microsoft.com/sql/t-sql/functions/opendatasource-transact-sql)|아닙니다.|예, 다른 Azure SQL Database 및 SQL Server에만 있습니다. 참조 [T-SQL 차이점](sql-database-managed-instance-transact-sql-information.md)|
| [OPENJSON](https://docs.microsoft.com/sql/t-sql/functions/openjson-transact-sql)|예|예|
| [OPENQUERY](https://docs.microsoft.com/sql/t-sql/functions/openquery-transact-sql)|아닙니다.|예, 다른 Azure SQL Database 및 SQL Server에만 있습니다. 참조 [T-SQL 차이점](sql-database-managed-instance-transact-sql-information.md)|
| [OPENROWSET](https://docs.microsoft.com/sql/t-sql/functions/openrowset-transact-sql)|예, Azure Blob 저장소에서 가져올 수만 있습니다. |예만 다른 Azure SQL Database 및 SQL Server 및 Azure Blob storage에서 가져오기. 참조 [T-SQL 차이점](sql-database-managed-instance-transact-sql-information.md)|
| [OPENXML](https://docs.microsoft.com/sql/t-sql/functions/openxml-transact-sql)|예|예|
| [연산자](https://docs.microsoft.com/sql/t-sql/language-elements/operators-transact-sql) | 대부분 - 개별 연산자 참조 |예 - [T-SQL 차이점](sql-database-managed-instance-transact-sql-information.md) 참조 |
| [분할](https://docs.microsoft.com/sql/relational-databases/partitions/partitioned-tables-and-indexes) | 예 | 예 |
| [지정 시간 데이터베이스 복원](https://docs.microsoft.com/sql/relational-databases/backup-restore/restore-a-sql-server-database-to-a-point-in-time-full-recovery-model) | 예-대규모-이외의 모든 서비스 계층 참조 [SQL 데이터베이스 복구](sql-database-recovery-using-backups.md#point-in-time-restore) | 예 - [SQL Database 복구](sql-database-recovery-using-backups.md#point-in-time-restore) 참조 |
| [PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) | 아닙니다. | 아닙니다. |
| [정책 기반 관리](https://docs.microsoft.com/sql/relational-databases/policy-based-management/administer-servers-by-using-policy-based-management) | 아닙니다. | 아닙니다. |
| [조건자](https://docs.microsoft.com/sql/t-sql/queries/predicates) | 예 | 예 |
| [쿼리 알림](https://docs.microsoft.com/sql/relational-databases/native-client/features/working-with-query-notifications) | 아닙니다. | 예 |
| [Query Performance Insights](sql-database-query-performance.md) | 예 | 아닙니다. |
| [R 서비스](https://docs.microsoft.com/sql/advanced-analytics/r-services/sql-server-r-services) | 예, [공개 미리 보기 상태](https://docs.microsoft.com/sql/advanced-analytics/what-s-new-in-sql-server-machine-learning-services)  | 아닙니다. |
| [리소스 관리자](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor) | 아닙니다. | 예 |
| [RESTORE 문](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-for-restoring-recovering-and-managing-backups-transact-sql) | 아닙니다. | 예 - [복원 차이점](sql-database-managed-instance-transact-sql-information.md#restore-statement) 참조 |
| [백업에서 데이터베이스 복원](https://docs.microsoft.com/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases#restore-data-backups) | 자동 백업에서만 - [SQL Database 복구](sql-database-recovery-using-backups.md) 참조 | 자동 백업에서 - [SQL Database 복구](sql-database-recovery-using-backups.md) 참조, 전체 백업에서 - [백업 차이점](sql-database-managed-instance-transact-sql-information.md#backup) 참조 |
| [행 수준 보안](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) | 예 | 예 |
| [의미 체계 검색](https://docs.microsoft.com/sql/relational-databases/search/semantic-search-sql-server) | 아닙니다. | 아닙니다. |
| [시퀀스 번호](https://docs.microsoft.com/sql/relational-databases/sequence-numbers/sequence-numbers) | 예 | 예 |
| [Service Broker](https://docs.microsoft.com/sql/database-engine/configure-windows/sql-server-service-broker) | 아닙니다. | 예, 인스턴스 내에 있습니다. 참조 [Service Broker 차이점](sql-database-managed-instance-transact-sql-information.md#service-broker) |
| [서버 구성 설정](https://docs.microsoft.com/sql/database-engine/configure-windows/server-configuration-options-sql-server) | 아닙니다. | 예 - [T-SQL 차이점](sql-database-managed-instance-transact-sql-information.md) 참조 |
| [Set 문](https://docs.microsoft.com/sql/t-sql/statements/set-statements-transact-sql) | 대부분 - 개별 문 참조 | 예 - [T-SQL 차이점](sql-database-managed-instance-transact-sql-information.md) 참조|
| [SMO](https://docs.microsoft.com/sql/relational-databases/server-management-objects-smo/sql-server-management-objects-smo-programming-guide) | [예](https://www.nuget.org/packages/Microsoft.SqlServer.SqlManagementObjects) | 예 [버전 150(preview)](https://www.nuget.org/packages/Microsoft.SqlServer.SqlManagementObjects) |
| [Spatial](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-sql-server) | 예 | 예 |
| [SQL Analytics](https://docs.microsoft.com/azure/azure-monitor/insights/azure-sql) | 예 | 예 |
| [SQL 데이터 동기화](sql-database-get-started-sql-data-sync.md) | 예 | 아닙니다. |
| [SQL Server 에이전트](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent) | 아니요 - [탄력적 작업](sql-database-elastic-jobs-getting-started.md) 참조 | 예 - [SQL Server Agent 차이점](sql-database-managed-instance-transact-sql-information.md#sql-server-agent) 참조 |
| [SSAS(SQL Server Analysis Services)](https://docs.microsoft.com/sql/analysis-services/analysis-services) | 아니요 - [Azure Analysis Services](https://azure.microsoft.com/services/analysis-services/) 참조 | 아니요 - [Azure Analysis Services](https://azure.microsoft.com/services/analysis-services/) 참조 |
| [SQL Server 감사](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine) | 아니요 - [SQL Database 감사](sql-database-auditing.md) 참조 | 예 - [감사 차이점](sql-database-managed-instance-transact-sql-information.md#auditing) 참조 |
| [SSDT(SQL Server Data Tools)](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt) | 예 | 예 |
| [SSIS(SQL Server Integration Services)](https://docs.microsoft.com/sql/integration-services/sql-server-integration-services) | 예, ADF(Azure Data Factory) 환경의 관리 SSIS를 사용합니다. 여기서 패키지는 Azure SQL Database에서 호스트되는 SSISDB에 저장되고 Azure SSIS IR(Integration Runtime)에서 실행됩니다. [ADF에서 Azure-SSIS IR 만들기](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)를 참조하세요. <br/><br/>SQL Database 서버 및 Managed Instance의 SSIS 기능을 비교하려면 [Azure SQL Database 단일 데이터베이스/탄력적 풀 및 Managed Instance 비교](../data-factory/create-azure-ssis-integration-runtime.md#compare-sql-database-single-databaseelastic-pool-and-sql-database-managed-instance)를 참조하세요. | 예, ADF(Azure Data Factory) 환경의 관리 SSIS를 사용합니다. 여기서 패키지는 Managed Instance에서 호스트되는 SSISDB에 저장되고 Azure SSIS IR(Integration Runtime)에서 실행됩니다. [ADF에서 Azure-SSIS IR 만들기](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)를 참조하세요. <br/><br/>SQL Database 및 Managed Instance의 SSIS 기능을 비교하려면 [Azure SQL Database 단일 데이터베이스/탄력적 풀 및 Managed Instance 비교](../data-factory/create-azure-ssis-integration-runtime.md#compare-sql-database-single-databaseelastic-pool-and-sql-database-managed-instance)를 참조하세요. |
| [SSMS(SQL Server Management Studio)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) | 예 | 예 [18.0 이상 버전](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms#ssms-180-rc1) |
| [SQL Server PowerShell](https://docs.microsoft.com/sql/relational-databases/scripting/sql-server-powershell) | 예 | 예 |
| [SQL Server Profiler](https://docs.microsoft.com/sql/tools/sql-server-profiler/sql-server-profiler) | 아니요 - [확장 이벤트](sql-database-xevent-db-diff-from-svr.md) 참조 | 예 |
| [SQL Server 복제](https://docs.microsoft.com/sql/relational-databases/replication/sql-server-replication) | [트랜잭션 및 스냅숏 복제 구독자만 해당](sql-database-single-database-migrate.md) | 예, [공개 미리 보기 상태](https://docs.microsoft.com/sql/relational-databases/replication/replication-with-sql-database-managed-instance) |
| [SSRS(SQL Server Reporting Services)](https://docs.microsoft.com/sql/reporting-services/create-deploy-and-manage-mobile-and-paginated-reports) | 아니요 - [Power BI 참조](https://docs.microsoft.com/power-bi/) | 아니요 - [Power BI 참조](https://docs.microsoft.com/power-bi/) |
| [저장 프로시저](https://docs.microsoft.com/sql/relational-databases/stored-procedures/stored-procedures-database-engine) | 예 | 예 |
| [시스템 저장 함수](https://docs.microsoft.com/sql/relational-databases/system-functions/system-functions-for-transact-sql) | 대부분 - 개별 함수 참조 | 예- [저장 프로시저, 함수, 트리거 차이점](sql-database-managed-instance-transact-sql-information.md#stored-procedures-functions-triggers) 참조 |
| [시스템 저장 프로시저](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/system-stored-procedures-transact-sql) | 일부 - 개별 저장 프로시저 참조 | 예- [저장 프로시저, 함수, 트리거 차이점](sql-database-managed-instance-transact-sql-information.md#stored-procedures-functions-triggers) 참조 |
| [시스템 테이블](https://docs.microsoft.com/sql/relational-databases/system-tables/system-tables-transact-sql) | 일부 - 개별 테이블 참조 | 예 - [T-SQL 차이점](sql-database-managed-instance-transact-sql-information.md) 참조 |
| [시스템 카탈로그 뷰](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/catalog-views-transact-sql) | 일부 - 개별 뷰 참조 | 예 - [T-SQL 차이점](sql-database-managed-instance-transact-sql-information.md) 참조 |
| [임시 테이블](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql#database-scoped-global-temporary-tables-azure-sql-database) | 로컬 및 데이터베이스 범위 전역 임시 테이블 | 로컬 및 인스턴스 범위 전역 임시 테이블 |
| [임시 테이블](https://docs.microsoft.com/sql/relational-databases/tables/temporal-tables) | [예](sql-database-temporal-tables.md) | [예](sql-database-temporal-tables.md) |
| 표준 시간대 선택 | 아닙니다. | [Yes(preview)](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-timezone) |
|위협 감지|  [예](sql-database-threat-detection.md)|[예](sql-database-managed-instance-threat-detection.md)|
| [추적 플래그](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql) | 아닙니다. | 아닙니다. |
| [변수](https://docs.microsoft.com/sql/t-sql/language-elements/variables-transact-sql) | 예 | 예 |
| [TDE(투명한 데이터 암호화)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-tde) | 예 - 범용 및 중요 비즈니스 서비스 계층만 해당| [예](transparent-data-encryption-azure-sql.md) |
[VNet](../virtual-network/virtual-networks-overview.md) | 부분 - [VNet 엔드포인트](sql-database-vnet-service-endpoint-rule-overview.md) 참조 | 예. Resource Manager 모델만 |
| [Windows Server 장애 조치(failover) 클러스터링](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/windows-server-failover-clustering-wsfc-with-sql-server) | [고가용성](sql-database-high-availability.md)은 모든 데이터베이스에 포함됩니다. 재해 복구는 [Azure SQL Database의 비즈니스 연속성 개요](sql-database-business-continuity.md)에서 설명합니다. | [고가용성](sql-database-high-availability.md)은 모든 데이터베이스에 포함됩니다. 재해 복구는 [Azure SQL Database의 비즈니스 연속성 개요](sql-database-business-continuity.md)에서 설명합니다. |
| [XML 인덱스](https://docs.microsoft.com/sql/t-sql/statements/create-xml-index-transact-sql) | 예 | 예 |

## <a name="next-steps"></a>다음 단계

- Azure SQL Database 서비스에 대한 정보는 [SQL Database 정의](sql-database-technical-overview.md)를 참조하세요.
- Managed Instance에 대한 자세한 내용은 [Managed Instance란?](sql-database-managed-instance.md)을 참조하세요.
