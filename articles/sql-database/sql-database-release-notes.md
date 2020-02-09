---
title: 릴리스 정보
description: Azure SQL Database 서비스 및 Azure SQL Database 설명서의 새로운 기능 및 향상 된 기능에 대해 알아봅니다.
services: sql-database
author: stevestein
ms.service: sql-database
ms.subservice: service
ms.devlang: ''
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: sstein
ms.openlocfilehash: bdea9af04008ef68578ff8c136760f2493a2ae35
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/21/2019
ms.locfileid: "74279224"
---
# <a name="sql-database-release-notes"></a>SQL Database 릴리스 정보

이 문서에는 현재 공개 미리 보기로 제공 되는 SQL Database 기능이 나열 되어 있습니다. SQL Database 업데이트 및 개선 사항에 대해서는 [SQL Database 서비스 업데이트](https://azure.microsoft.com/updates/?product=sql-database)를 참조 하세요. 다른 Azure 서비스에 대 한 업데이트 및 개선 사항은 [서비스 업데이트](https://azure.microsoft.com/updates)를 참조 하세요.

## <a name="features-in-public-preview"></a>공개 미리 보기의 기능

### <a name="single-databasetabsingle-database"></a>[단일 데이터베이스](#tab/single-database)

| 기능 | 세부 정보 |
| ---| --- |
| 새 Fsv2 시리즈 및 M 시리즈 하드웨어 생성| 자세한 내용은 [하드웨어 생성](sql-database-service-tiers-vcore.md#hardware-generations)을 참조 하세요.|
| [Azure 개인 링크](https://azure.microsoft.com/updates/private-link-now-available-in-preview/)| Private Link는 Azure 네트워크에 데이터를 유지하여 인터넷에 대한 데이터 노출을 제거함으로써 네트워크 아키텍처를 단순화하고 Azure의 엔드포인트 간 연결을 보호합니다. Private Link를 통해 Azure에서 자신만의 서비스를 만들고 렌더링할 수도 있습니다. |
| 단일 데이터베이스 및 탄력적 풀을 사용한 가속화 된 데이터베이스 복구 | 자세한 내용은 [가속화 데이터베이스 복구](sql-database-accelerated-database-recovery.md)를 참조 하세요.|
|대략적인 고유 카운트|자세한 내용은 [대략적인 개수 Distinct](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#approximate-query-processing)를 참조 하십시오.|
|Rowstore의 일괄 처리 모드 (호환성 수준 150)|자세한 내용은 [Rowstore의 일괄 처리 모드](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#batch-mode-on-rowstore)를 참조 하세요.|
| 데이터 검색 및 분류  |자세한 내용은 [Azure SQL Database 및 SQL Data Warehouse 데이터 검색 & 분류](sql-database-data-discovery-and-classification.md)를 참조 하세요.|
| 탄력적 데이터베이스 작업 | 자세한 내용은 [탄력적 작업 만들기, 구성 및 관리](elastic-jobs-overview.md)를 참조 하세요. |
| 탄력적 쿼리 | 자세한 내용은 [탄력적 쿼리 개요](sql-database-elastic-query-overview.md)를 참조 하세요. |
| 탄력적 트랜잭션 | [클라우드 데이터베이스 간 분산 트랜잭션](sql-database-elastic-transactions-overview.md). |
|메모리 부여 피드백 (행 모드) (호환성 수준 150)|자세한 내용은 [메모리 부여 피드백 (행 모드)](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#row-mode-memory-grant-feedback)을 참조 하세요.|
| Azure Portal의 쿼리 편집기 |자세한 내용은 [Azure Portal의 SQL 쿼리 편집기를 사용 하 여 데이터 연결 및 쿼리를](sql-database-connect-query-portal.md)참조 하세요.|
| 단일 데이터베이스 및 탄력적 풀을 사용 하 여 R services/기계 학습 |자세한 내용은 [Azure SQL Database Machine Learning Services](https://docs.microsoft.com/sql/advanced-analytics/what-s-new-in-sql-server-machine-learning-services?view=sql-server-2017#machine-learning-services-in-azure-sql-database)을 참조 하세요.|
|SQL 분석|자세한 내용은 [Azure SQL 분석](../azure-monitor/insights/azure-sql.md)를 참조 하세요.|
|테이블 변수 지연 컴파일 (호환성 수준 150 미만)|자세한 내용은 [테이블 변수 지연 컴파일](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#table-variable-deferred-compilation)을 참조 하세요.|
| &nbsp; |

### <a name="managed-instancetabmanaged-instance"></a>[Managed Instance](#tab/managed-instance)

| 기능 | 세부 정보 |
| ---| --- |
| <a href="/azure/sql-database/sql-database-managed-instance-connectivity-architecture#service-aided-subnet-configuration-public-preview-in-east-us-and-west-us">서비스-기타 서브넷 구성</a> | 서브넷 구성을 관리 하는 안전 하 고 편리한 방법입니다. |
| <a href="/azure/sql-database/sql-database-instance-pools">인스턴스 풀</a> | 더 작은 SQL 인스턴스를 클라우드로 마이그레이션하는 편리 하 고 비용 효율적인 방법입니다. |
| <a href="https://aka.ms/managed-instance-tde-byok">Bring Your Own Key (BYOK)를 사용 하는 TDE (투명 한 데이터 암호화)</a> |자세한 내용은 [Azure Key Vault: Bring Your Own Key 지원에서 고객이 관리 하는 키를 사용 하 여 AZURE SQL 투명한 데이터 암호화](transparent-data-encryption-byok-azure-sql.md)를 참조 하세요.|
| <a href="https://aka.ms/managed-instance-aadlogins">인스턴스 수준 Azure AD 서버 보안 주체 (로그인)</a> | <a href="https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">CREATE LOGIN FROM EXTERNAL PROVIDER</a> 문을 사용 하 여 서버 수준 로그인을 만듭니다. |
| [트랜잭션 복제자](sql-database-managed-instance-transactional-replication.md) | 테이블의 변경 내용을 관리 되는 인스턴스, 단일 데이터베이스 또는 SQL Server 인스턴스에 배치 된 다른 데이터베이스로 복제 하거나 다른 관리 되는 인스턴스 또는 SQL Server 인스턴스에서 일부 행이 변경 될 때 테이블을 업데이트 합니다. 자세한 내용은 [Azure SQL Database 관리 되는 인스턴스 데이터베이스에서 복제 구성](replication-with-sql-database-managed-instance.md)을 참조 하세요. |
| 위협 감지 |자세한 내용은 [Azure SQL Database 관리 되는 인스턴스에서 위협 감지 구성](sql-database-managed-instance-threat-detection.md)을 참조 하세요.|
| 관리 되는 인스턴스를 사용 하 여 삭제 된 데이터베이스 다시 만들기 |자세한 내용은 [AZURE SQL Managed Instance에서 삭제 된 데이터베이스 다시 만들기](https://medium.com/azure-sqldb-managed-instance/re-create-dropped-databases-in-azure-sql-managed-instance-dc369ed60266)를 참조 하세요.|
| &nbsp; |

---

## <a name="new-features"></a>새로운 기능

### <a name="managed-instance-h2-2019-updates"></a>관리 되는 인스턴스 H2 2019 업데이트

- [자동 장애 조치 그룹](https://azure.microsoft.com/updates/azure-sql-database-auto-failover-groups-feature-now-available-in-all-regions/) 을 사용 하면 주 인스턴스에서 다른 지역의 보조 인스턴스로 모든 데이터베이스를 복제할 수 있습니다.
- [전역 추적 플래그](https://azure.microsoft.com/updates/global-trace-flags-are-now-available-in-azure-sql-database-managed-instance/)를 사용 하 여 관리 되는 인스턴스 동작을 구성 합니다.

### <a name="managed-instance-h1-2019-updates"></a>관리 되는 인스턴스 H1 2019 업데이트

다음 기능은 H1 2019의 관리 되는 인스턴스 배포 모델에서 사용할 수 있습니다.
  - <a href="https://aka.ms/sql-mi-visual-studio-subscribers">Visual Studio 구독자를 위한 Azure 월간 크레딧을</a> 사용 하 여 구독에 대 한 지원을 제공 하 고 [지역별 제한을](sql-database-managed-instance-resource-limits.md#regional-resource-limitations)늘립니다.
  - <a href="https://docs.microsoft.com/sharepoint/administration/deploy-azure-sql-managed-instance-with-sharepoint-servers-2016-2019">SharePoint 2016 및 SharePoint 2019</a>와 <a href="https://docs.microsoft.com/business-applications-release-notes/october18/dynamics365-business-central/support-for-azure-sql-database-managed-instance">Dynamics 365 Business Central</a> 지원
  - <a href="https://aka.ms/managed-instance-collation">서버 수준 데이터 정렬</a> 및 원하는 <a href="https://azure.microsoft.com/updates/managed-instance-time-zone-ga/">표준 시간대</a> 를 사용 하 여 인스턴스를 만듭니다.
  - 이제 관리 되는 인스턴스가 <a href="sql-database-managed-instance-management-endpoint-verify-built-in-firewall.md">기본 제공 방화벽</a>으로 보호 됩니다.
  - [공용 엔드포인트](sql-database-managed-instance-public-endpoint-configure.md)을 사용 하 여 프록시를 구성 하 고, [프록시를 재정의](sql-database-connectivity-architecture.md#connection-policy) 하 여 네트워크 성능을 향상 하거나, <a href="https://aka.ms/four-cores-sql-mi-update">Gen5 하드웨어 세대에서 4 개의 vcores</a> 를 사용 하거나, 지정 시간 복원의 경우 <a href="https://aka.ms/managed-instance-configurable-backup-retention">최대 35 일 동안 백업 보존을 구성</a> 합니다. 장기 백업 보존 (최대 10 년)은 아직 사용 되지 않으므로 <a href="https://docs.microsoft.com/sql/relational-databases/backup-restore/copy-only-backups-sql-server">복사 전용 백업을</a> 대체 방법으로 사용할 수 있습니다.
  - 새 기능을 통해 <a href="https://medium.com/@jocapc/geo-restore-your-databases-on-azure-sql-instances-1451480e90fa">PowerShell을 사용 하 여 데이터베이스를 다른 데이터 센터로 지역 복원</a>하 고, [데이터베이스 이름을 바꾸고](https://azure.microsoft.com/updates/azure-sql-database-managed-instance-database-rename-is-supported/), [가상 클러스터를 삭제할](sql-database-managed-instance-delete-virtual-cluster.md)수 있습니다.
  - 새로운 기본 제공 [인스턴스 기여자 역할](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#sql-managed-instance-contributor) 을 사용 하면 보안 원칙 및 엔터프라이즈 표준 준수와 관련 하 여 의무 (안 함) 규정 준수를 구분할 수 있습니다.
  - 관리 되는 인스턴스는 다음 Azure Government 지역에서 GA (US Gov 텍사스, US Gov 애리조나) 및 중국 북부 2와 중국 동부 2에서 사용할 수 있습니다. 또한 오스트레일리아 중부, 오스트레일리아 중부 2, 브라질 남부, 프랑스 남부, 아랍에미리트 중부, 아랍에미리트 북부, 남아프리카 공화국 북부, 남아프리카 공화국에서 사용할 수 있습니다.

## <a name="fixed-known-issues"></a>알려진 문제 해결

- **8 월 2019** 포함 된 데이터베이스는 관리 되는 인스턴스에서 완전히 지원 됩니다.
- **10 월 2019** -원본 데이터베이스에 메모리 내 OLTP 개체가 포함 되어 있는 경우 중요 비즈니스용 계층에서 일반 용도의 계층으로 기본 제공 되는 지정 시간 데이터베이스 복원에 실패 합니다.
- **Oct 2019** -보안 연결을 사용 하 여 외부 (비 Azure) 메일 서버에 **데이터베이스 메일** 기능을 사용할 수 있습니다.
- **2019 년 11 월** -데이터베이스에서 데이터베이스 Azure Blob Storage를 복원한 후 `DBCC CHECKDB`를 사용 하 여 데이터베이스 일관성을 확인 합니다.

## <a name="updates"></a>업데이트

SQL Database 업데이트 및 개선 사항 목록은 [SQL Database 서비스 업데이트](https://azure.microsoft.com/updates/?product=sql-database)를 참조 하세요.

모든 Azure 서비스에 대 한 업데이트 및 개선 사항은 [서비스 업데이트](https://azure.microsoft.com/updates)를 참조 하세요.

## <a name="contribute-to-content"></a>콘텐츠에 기여

Azure SQL Database 설명서에 기여 하려면 [Docs 참여자 가이드](https://docs.microsoft.com/contribute/)를 참조 하세요.
