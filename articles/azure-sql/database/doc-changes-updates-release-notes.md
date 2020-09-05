---
title: 새로운 기능은 무엇입니까?
titleSuffix: Azure SQL Database & SQL Managed Instance
description: SQL Managed Instance & Azure SQL Database에 대 한 새로운 기능 및 설명서 개선 사항에 대해 알아봅니다.
services: sql-database
author: stevestein
ms.service: sql-db-mi
ms.subservice: service
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
ms.date: 06/17/2020
ms.author: sstein
ms.openlocfilehash: 0e44280c0a6c0d39c98e3aeecd5e9a3707332e81
ms.sourcegitcommit: 3bf69c5a5be48c2c7a979373895b4fae3f746757
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88236576"
---
# <a name="whats-new-in-azure-sql-database--sql-managed-instance"></a>SQL Managed Instance & Azure SQL Database의 새로운 기능
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

이 문서에는 현재 공개 미리 보기로 제공 되는 Azure SQL Database 및 Azure SQL Managed Instance 기능이 나열 되어 있습니다. SQL Database 및 SQL Managed Instance 업데이트 및 개선 사항에 대해서는 [SQL Database & sql Managed Instance 서비스 업데이트](https://azure.microsoft.com/updates/?product=sql-database)를 참조 하세요. 다른 Azure 서비스에 대한 개선 사항 및 업데이트는 [서비스 업데이트](https://azure.microsoft.com/updates)를 참조하세요.

## <a name="whats-new"></a>새로운 기능

Azure SQL Database 및 Azure SQL Managed Instance에 대 한 설명서는 개별 섹션으로 분할 되었습니다. 또한 관리 되는 인스턴스를 *Azure SQL Database 관리* 되는 인스턴스에서 *Azure SQL Managed Instance*로 참조 하는 방법도 업데이트 했습니다.

이 작업은 단일 데이터베이스와 관리 되는 인스턴스 간에 일부 기능과 기능이 크게 달라 지므로 개별 공유 문서에서 Azure SQL Database와 Azure SQL Managed Instance 간의 복잡 한 미묘한 차이을 설명 하는 데 점점 더 많은 도움이 될 것입니다.

Azure의 단일 관리 되는 Azure SQL Database 데이터베이스, Azure SQL Managed Instance에서 여러 데이터베이스를 호스트 하는 완전히 관리 되는 인스턴스 또는 Azure의 가상 컴퓨터에서 호스트 되는 친숙 한 온-프레미스 SQL Server 제품을 호스트 하는 경우, 다양 한 Azure SQL 제품 간의 이러한 이해를 통해 Azure에서 SQL Server 데이터베이스 엔진으로 작업 하는 프로세스를 간소화 하 고 간소화 해야 합니다.

이 작업은 진행 중인 작업 이며 모든 아티클이 아직 업데이트 되지 않은 것을 고려해 야 합니다. 예를 들어 Transact-sql (T-sql) 문, 저장 프로시저 및 Azure SQL Database와 Azure SQL Managed Instance 간에 공유 되는 다양 한 기능에 대 한 설명서는 아직 완전 하지 않으므로 콘텐츠를 명확 하 게 이해 하는 동안 감사 드립니다. 

이 표에서는 용어 변경에 대 한 빠른 비교를 제공 합니다. 


|**새 용어**  | **이전 용어**  |**설명** |
|---------|---------|---------|
|**Azure SQL Managed Instance** | Azure SQL Database *관리 되는 인스턴스*| Azure SQL Managed Instance는 Azure SQL Database 내의 배포 옵션 보다는 Azure SQL 제품군 내에서 고유한 제품입니다. | 
|**Azure SQL Database**|*단일 데이터베이스* Azure SQL Database| 달리 명시적으로 지정 하지 않는 한 제품 이름 Azure SQL Database에는 단일 데이터베이스와 탄력적 풀에 배포 된 데이터베이스가 모두 포함 됩니다. |
|**Azure SQL Database**|Azure SQL Database *탄력적 풀*| 달리 명시적으로 지정 하지 않는 한 제품 이름 Azure SQL Database에는 단일 데이터베이스와 탄력적 풀에 배포 된 데이터베이스가 모두 포함 됩니다.  |
|**Azure SQL Database** |Azure SQL Database | 용어는 동일 하 게 유지 되지만 이제는 단일 데이터베이스 및 탄력적 풀 배포에만 적용 되며 관리 되는 인스턴스는 포함 하지 않습니다. |
| **Azure SQL**| 해당 없음 | Azure에서 사용할 수 있는 SQL Server 데이터베이스 엔진 제품의 패밀리 (Azure SQL Database, Azure SQL Managed Instance 및 Azure Vm의 SQL Server를 나타냅니다. | 

## <a name="features-in-public-preview"></a>공개 미리 보기의 기능

### <a name="azure-sql-database"></a>[Azure SQL Database](#tab/single-database)

| 기능 | 세부 정보 |
| ---| --- |
| 단일 데이터베이스와 탄력적 풀을 통한 데이터베이스 복구 가속화 | 자세한 내용은 [데이터베이스 복구 가속화](../accelerated-database-recovery.md)를 참조하세요.|
| 데이터 검색 및 분류  |자세한 내용은 [Azure SQL Database 및 Azure Synapse Analytics 데이터 검색 & 분류](data-discovery-and-classification-overview.md)를 참조 하세요.|
| 탄력적 데이터베이스 작업 (미리 보기) | 자세한 내용은 [탄력적 작업 만들기, 구성 및 관리](elastic-jobs-overview.md)를 참조하세요. |
| 탄력적 쿼리 | 자세한 내용은 [탄력적 쿼리 개요](elastic-query-overview.md)를 참조하세요. |
| 탄력적 트랜잭션 | [클라우드 데이터베이스의 분산 트랜잭션](elastic-transactions-overview.md). |
| Azure Portal의 쿼리 편집기 |자세한 내용은 [Azure Portal의 SQL 쿼리 편집기를 사용하여 데이터 연결 및 쿼리](connect-query-portal.md)를 참조하세요.|
| 단일 데이터베이스 및 탄력적 풀을 통한 R 서비스/기계 학습 |자세한 내용은 [Azure SQL Database의 Machine Learning Services](https://docs.microsoft.com/sql/advanced-analytics/what-s-new-in-sql-server-machine-learning-services?view=sql-server-2017#machine-learning-services-in-azure-sql-database)를 참조하세요.|
|SQL 분석|자세한 내용은 [Azure SQL 분석](../../azure-monitor/insights/azure-sql.md)을 참조하세요.|
| &nbsp; |

### <a name="azure-sql-managed-instance"></a>[Azure SQL Managed Instance](#tab/managed-instance)

| 기능 | 세부 정보 |
| ---| --- |
| <a href="/azure/sql-database/sql-database-instance-pools">인스턴스 풀</a> | 작은 SQL 인스턴스를 클라우드로 마이그레이션하는 편리하고 비용 효율적인 방법입니다. |
| <a href="https://aka.ms/managed-instance-aadlogins">인스턴스 수준 Azure AD 서버 보안 주체(로그인)</a> | <a href="https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">CREATE LOGIN FROM EXTERNAL PROVIDER</a> 문을 사용 하 여 인스턴스 수준 로그인을 만듭니다. |
| [트랜잭션 복제](../managed-instance/replication-transactional-overview.md) | 테이블의 변경 내용을 SQL Managed Instance, SQL Database 또는 SQL Server의 다른 데이터베이스로 복제 합니다. 또는 SQL Managed Instance 또는 SQL Server의 다른 인스턴스에서 일부 행이 변경 될 때 테이블을 업데이트 합니다. 자세한 내용은 [AZURE SQL Managed Instance에서 복제 구성](../managed-instance/replication-between-two-instances-configure-tutorial.md)을 참조 하세요. |
| 위협 탐지 |자세한 내용은 [AZURE SQL Managed Instance에서 위협 감지 구성](../managed-instance/threat-detection-configure.md)을 참조 하세요.|
| 장기 백업 보존 | 자세한 내용은 현재 제한 된 공개 미리 보기로 제공 되는 [AZURE SQL Managed Instance에서 장기 백업 보존 구성](../managed-instance/long-term-backup-retention-configure.md)을 참조 하세요. | 

---

## <a name="sql-managed-instance-new-features-and-known-issues"></a>SQL Managed Instance 새로운 기능 및 알려진 문제

### <a name="sql-managed-instance-h2-2019-updates"></a>SQL Managed Instance H2 2019 업데이트

- [서비스-사용자 서브넷 구성은](https://azure.microsoft.com/updates/service-aided-subnet-configuration-for-managed-instance-in-azure-sql-database-available/) SQL Managed Instance가 중단 된 관리 트래픽을 방지 하는 동시에 데이터 트래픽을 제어 하는 서브넷 구성을 관리 하는 안전 하 고 편리한 방법입니다.
- [BYOK (투명 한 데이터 암호화)를 Bring Your Own Key 사용](https://azure.microsoft.com/updates/general-avilability-transparent-data-encryption-with-customer-managed-keys-for-azure-sql-database-managed-instance/) 하면 미사용 데이터 보호를 위한 byok (사용자에 게는 byok) 시나리오를 사용할 수 있으며, 조직에서 키 및 데이터에 대 한 관리 의무를 구분할 수 있습니다.
- [자동 장애 조치(failover) 그룹](https://azure.microsoft.com/updates/azure-sql-database-auto-failover-groups-feature-now-available-in-all-regions/)을 사용하면 주 인스턴스의 모든 데이터베이스를 다른 지역의 보조 인스턴스로 복제할 수 있습니다.
- [전역 추적 플래그](https://azure.microsoft.com/updates/global-trace-flags-are-now-available-in-azure-sql-database-managed-instance/) 를 사용 하 여 SQL Managed Instance 동작을 구성할 수 있습니다.

### <a name="sql-managed-instance-h1-2019-updates"></a>SQL Managed Instance H1 2019 업데이트

다음 기능은 H1 2019의 SQL Managed Instance 배포 모델에서 사용할 수 있습니다.
  - <a href="https://aka.ms/sql-mi-visual-studio-subscribers">Visual Studio 구독자를 위한 Azure 월간 크레딧</a>으로 구독이 가능하며 [지역별 한도](../managed-instance/resource-limits.md#regional-resource-limitations)가 증가되었습니다.
  - <a href="https://docs.microsoft.com/sharepoint/administration/deploy-azure-sql-managed-instance-with-sharepoint-servers-2016-2019">Sharepoint 2016 및 sharepoint 2019</a> 및 <a href="https://docs.microsoft.com/business-applications-release-notes/october18/dynamics365-business-central/support-for-azure-sql-database-managed-instance">Dynamics 365 Business Central 지원.</a>
  - <a href="https://aka.ms/managed-instance-collation">인스턴스 수준 데이터 정렬과</a> 원하는 <a href="https://azure.microsoft.com/updates/managed-instance-time-zone-ga/">표준 시간대</a> 를 사용 하 여 관리 되는 인스턴스를 만듭니다.
  - Managed Instance가 이제 [기본 제공 방화벽](../managed-instance/management-endpoint-verify-built-in-firewall.md)으로 보호됩니다.
  - [공용 끝점](../managed-instance/public-endpoint-configure.md)을 사용 하 여 SQL Managed Instance를 구성 하 고, [프록시를 재정의](connectivity-architecture.md#connection-policy) 하 여 네트워크 성능을 향상 하거나, <a href="https://aka.ms/four-cores-sql-mi-update">Gen5 하드웨어 세대에서 4 개의 vcores</a> 를 구성 하거나, 지정 시간 복원에 대해 <a href="https://aka.ms/managed-instance-configurable-backup-retention">최대 35 일의 백업 보존을 구성</a> 합니다. [장기 백업 보존](long-term-retention-overview.md#sql-managed-instance-support)(최대 10년)은 현재 제한된 공개 미리 보기로 제공됩니다.  
  - 새로운 기능을 통해 <a href="https://medium.com/@jocapc/geo-restore-your-databases-on-azure-sql-instances-1451480e90fa">PowerShell을 사용하여 데이터베이스를 다른 데이터 센터에 지리적 복원하고</a>, [데이터베이스 이름을 바꾸고](https://azure.microsoft.com/updates/azure-sql-database-managed-instance-database-rename-is-supported/), [가상 클러스터를 삭제](../managed-instance/virtual-cluster-delete.md)할 수 있습니다.
  - 기본 제공되는 새로운 [인스턴스 기여자 역할](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#sql-managed-instance-contributor)을 통해 보안 원칙에 따른 SoD(업무 분리) 준수 및 엔터프라이즈 표준 준수가 가능합니다.
  - SQL Managed Instance는 다음 Azure Government 지역에서 GA (US Gov 텍사스, US Gov 애리조나) 뿐만 아니라 중국 북부 2와 중국 동부 2에서 사용할 수 있습니다. 다음 공용 지역에서도 사용할 수 있습니다. 오스트레일리아 중부, 오스트레일리아 중부 2, 브라질 남부, 프랑스 남부, 아랍에미리트 중부, 아랍에미리트 북부, 남아프리카 공화국 북부, 남아프리카 공화국 서부.

### <a name="known-issues"></a>알려진 문제

|문제  |발견된 날짜  |상태  |해결된 날짜  |
|---------|---------|---------|---------|
|[서비스 주체는 Azure AD 및 AKV에 액세스할 수 없습니다.](#service-principal-cannot-access-azure-ad-and-akv)|8 월 2020|해결 방법 있음||
|[CHECKSUM 없는 수동 백업 복원이 실패할 수 있음](#restoring-manual-backup-without-checksum-might-fail)|2020년 5월|해결됨|2020년 6월|
|[기존 작업을 수정, 사용 안 함 또는 사용 하도록 설정할 때 에이전트가 응답 하지 않습니다.](#agent-becomes-unresponsive-upon-modifying-disabling-or-enabling-existing-jobs)|2020년 5월|해결됨|2020년 6월|
|[리소스 그룹에 대 한 사용 권한이 SQL Managed Instance에 적용 되지 않음](#permissions-on-resource-group-not-applied-to-sql-managed-instance)|2020년 2월|해결 방법 있음||
|[장애 조치(failover) 그룹용 포털을 통한 수동 장애 조치(failover)의 제한 사항](#limitation-of-manual-failover-via-portal-for-failover-groups)|2020년 1월|해결 방법 있음||
|[SQL 에이전트 역할에는 non-sysadmin 로그인에 대한 명시적 EXECUTE 권한 필요](#in-memory-oltp-memory-limits-are-not-applied)|2019년 12월|해결 방법 있음||
|[에이전트 프로세스를 다시 시작하면 SQL 에이전트 작업이 중단될 수 있음](#sql-agent-jobs-can-be-interrupted-by-agent-process-restart)|2019년 12월|해결됨|2020년 3월|
|[Azure AD 로그인 및 사용자는 SSDT에서 지원 되지 않습니다.](#azure-ad-logins-and-users-are-not-supported-in-ssdt)|2019년 11월|해결 방법 없음||
|[메모리 내 OLTP 메모리 제한이 적용되지 않음](#in-memory-oltp-memory-limits-are-not-applied)|2019년 10월|해결 방법 있음||
|[비어 있지 않은 파일을 제거하는 동안 잘못된 오류가 반환됨](#wrong-error-returned-while-trying-to-remove-a-file-that-is-not-empty)|2019년 10월|해결 방법 있음||
|[진행 중인 데이터베이스 복원으로 인해 서비스 계층 변경 및 인스턴스 만들기 작업이 차단됨](#change-service-tier-and-create-instance-operations-are-blocked-by-ongoing-database-restore)|2019년 9월|해결 방법 있음||
|[장애 조치(failover) 후 중요 비즈니스용 서비스 계층의 Resource Governor를 재구성해야 할 수도 있음](#resource-governor-on-business-critical-service-tier-might-need-to-be-reconfigured-after-failover)|2019년 9월|해결 방법 있음||
|[서비스 계층 업그레이드 후 데이터베이스 간 Service Broker 대화 상자를 다시 초기화 해야 함](#cross-database-service-broker-dialogs-must-be-reinitialized-after-service-tier-upgrade)|2019년 8월|해결 방법 있음||
|[Azure AD 로그인 유형의 가장은 지원 되지 않습니다.](#impersonation-of-azure-ad-login-types-is-not-supported)|2019년 7월|해결 방법 없음||
|[@query 매개 변수가 sp_send_db_mail에서 지원되지 않음](#-parameter-not-supported-in-sp_send_db_mail)|2019년 4월|해결 방법 없음||
|[지역 장애 조치(failover) 후 트랜잭션 복제를 다시 구성해야 함](#transactional-replication-must-be-reconfigured-after-geo-failover)|2019년 3월|해결 방법 없음||
|[복원 작업 중에 임시 데이터베이스가 사용됨](#temporary-database-is-used-during-restore-operation)||해결 방법 있음||
|[TEMPDB 구조와 콘텐츠가 다시 생성됨](#tempdb-structure-and-content-is-re-created)||해결 방법 없음||
|[작은 데이터베이스 파일로 스토리지 공간 초과](#exceeding-storage-space-with-small-database-files)||해결 방법 있음||
|[데이터베이스 이름 대신 GUID 값이 표시됨](#guid-values-shown-instead-of-database-names)||해결 방법 있음||
|[오류 로그가 지속되지 않음](#error-logs-arent-persisted).||해결 방법 없음||
|[동일한 인스턴스 내의 두 데이터베이스에 대한 트랜잭션 범위가 지원되지 않음](#transaction-scope-on-two-databases-within-the-same-instance-isnt-supported)||해결 방법 있음|2020년 3월|
|[CLR 모듈 및 연결된 서버에서 로컬 IP 주소를 참조할 수 없는 경우가 있음](#clr-modules-and-linked-servers-sometimes-cant-reference-a-local-ip-address)||해결 방법 있음||
|Azure Blob Storage에서 데이터베이스를 복원한 후 DBCC CHECKDB를 사용하여 데이터베이스 일관성을 확인하지 못함||해결됨|2019년 11월|
|원본 데이터베이스에 메모리 내 OLTP 개체가 포함되어 있으면 중요 비즈니스용 계층에서 범용 계층으로 특정 시점 데이터베이스 복원에 실패함||해결됨|2019년 10월|
|보안 연결을 사용 하는 외부 (비 Azure) 메일 서버를 사용 하는 데이터베이스 메일 기능||해결됨|2019년 10월|
|포함 된 데이터베이스는 SQL Managed Instance 지원 되지 않습니다.||해결됨|2019년 8월|

### <a name="service-principal-cannot-access-azure-ad-and-akv"></a>서비스 주체는 Azure AD 및 AKV에 액세스할 수 없습니다.

경우에 따라 Azure AD 및 Azure Key Vault (AKV) 서비스에 액세스 하는 데 사용 되는 서비스 주체에 문제가 있을 수 있습니다. 따라서이 문제는 SQL Managed Instance를 사용 하 여 Azure AD 인증 및 TDE (투명 한 데이터베이스 암호화) 사용에 영향을 줍니다. 이는 일시적인 연결 문제 또는 외부 공급자의 로그인/사용자 만들기 또는 EXECUTE AS LOGIN/USER와 같은 문을 실행 하지 못하는 경우에 발생할 수 있습니다. 새 Azure SQL Managed Instance에서 고객 관리 키를 사용 하 여 TDE를 설정 하는 것도 일부 환경에서 작동 하지 않을 수 있습니다.

**해결 방법**: 업데이트 명령을 실행 하기 전에 SQL Managed Instance에서이 문제가 발생 하지 않도록 하거나, 업데이트 명령 후에이 문제가 이미 발생 한 경우 Azure Portal로 이동 하 여 sql Managed Instance [Active Directory 관리자 블레이드](https://docs.microsoft.com/azure/azure-sql/database/authentication-aad-configure?tabs=azure-powershell#azure-portal)로 이동 합니다. "Managed Instance에 Azure Active Directory 액세스 하려면 서비스 주체가 필요 합니다. 라는 오류 메시지가 표시 되는지 확인 합니다. 서비스 주체를 만들려면 여기를 클릭 하십시오. " 이 오류 메시지가 발생 한 경우이를 클릭 하 고이 오류가 해결 될 때까지 제공 된 단계별 지침을 따르세요.

### <a name="restoring-manual-backup-without-checksum-might-fail"></a>CHECKSUM 없는 수동 백업 복원이 실패할 수 있음

특정 상황에서는 체크섬을 사용 하지 않고 관리 되는 인스턴스에서 만들어진 데이터베이스의 수동 백업이 복원 되지 않을 수 있습니다. 이 경우 성공할 때까지 백업 복원을 다시 시도 합니다.

**해결 방법**: 체크섬을 사용 하도록 설정 된 관리 되는 인스턴스에서 데이터베이스를 수동으로 백업 합니다.

### <a name="agent-becomes-unresponsive-upon-modifying-disabling-or-enabling-existing-jobs"></a>기존 작업을 수정, 사용 안 함 또는 사용 하도록 설정할 때 에이전트가 응답 하지 않습니다.

특정 상황에서 기존 작업을 수정, 사용 안 함 또는 사용 하도록 설정 하면 에이전트가 응답 하지 않을 수 있습니다. 이 문제는 검색 시 자동으로 완화 되므로 에이전트 프로세스를 다시 시작 합니다.

### <a name="permissions-on-resource-group-not-applied-to-sql-managed-instance"></a>리소스 그룹에 대 한 사용 권한이 SQL Managed Instance에 적용 되지 않음

SQL Managed Instance 참여자 Azure 역할을 리소스 그룹 (RG)에 적용 하는 경우 SQL Managed Instance에 적용 되지 않으며 효과가 없습니다.

**해결 방법**: 구독 수준에서 사용자에 대 한 SQL Managed Instance 참가자 역할을 설정 합니다.

### <a name="limitation-of-manual-failover-via-portal-for-failover-groups"></a>장애 조치(failover) 그룹용 포털을 통한 수동 장애 조치(failover)의 제한 사항

장애 조치 (failover) 그룹이 다른 Azure 구독 또는 리소스 그룹의 여러 인스턴스에 걸쳐 있으면 장애 조치 (failover) 그룹의 주 인스턴스에서 수동 장애 조치 (failover)를 시작할 수 없습니다.

**해결 방법**: 지역 보조 인스턴스에서 포털을 통해 장애 조치 (failover)를 시작 합니다.

### <a name="sql-agent-roles-need-explicit-execute-permissions-for-non-sysadmin-logins"></a>SQL 에이전트 역할에는 non-sysadmin 로그인에 대한 명시적 EXECUTE 권한이 필요함

Sysadmin이 아닌 로그인이 모든 [SQL 에이전트 고정 데이터베이스 역할](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent-fixed-database-roles)에 추가 되 면 이러한 로그인이 작동 하기 위해 마스터 저장 프로시저에 명시적 실행 권한을 부여 해야 하는 문제가 있습니다. 이런 문제가 발생하면 "개체 <object_name>에 대한 EXECUTE 권한이 거부되었습니다(Microsoft SQL Server, 오류: 229)" 오류 메시지가 표시됩니다.

**해결 방법**: SQL 에이전트 고정 데이터베이스 역할 (SQLAgentUserRole, SQLAgentReaderRole 또는 SQLAgentOperatorRole)에 로그인을 추가 하면 이러한 역할에 추가 된 각 로그인에 대해 아래 t-sql 스크립트를 실행 하 여 나열 된 저장 프로시저에 대 한 실행 권한을 명시적으로 부여 합니다.

```tsql
USE [master]
GO
CREATE USER [login_name] FOR LOGIN [login_name]
GO
GRANT EXECUTE ON master.dbo.xp_sqlagent_enum_jobs TO [login_name]
GRANT EXECUTE ON master.dbo.xp_sqlagent_is_starting TO [login_name]
GRANT EXECUTE ON master.dbo.xp_sqlagent_notify TO [login_name]
```

### <a name="sql-agent-jobs-can-be-interrupted-by-agent-process-restart"></a>에이전트 프로세스를 다시 시작하면 SQL 에이전트 작업이 중단될 수 있음

**(3 월 2020 일에 해결 됨)** SQL 에이전트는 작업이 시작 될 때마다 새 세션을 만들고 메모리 사용을 점차적으로 늘립니다. 예약 된 작업의 실행을 차단 하는 내부 메모리 제한에 도달 하지 않도록 하기 위해 메모리 소비가 임계값에 도달 하면 에이전트 프로세스가 다시 시작 됩니다. 이로 인해 다시 시작될 때 실행 중인 작업이 중단될 수 있습니다.

### <a name="in-memory-oltp-memory-limits-are-not-applied"></a>메모리 내 OLTP 메모리 제한이 적용되지 않음

경우에 따라 중요 비즈니스용 서비스 계층은 [메모리 액세스에 최적화 된 개체에 대 한 최대 메모리 제한을](../managed-instance/resource-limits.md#in-memory-oltp-available-space) 올바르게 적용 하지 않습니다. SQL Managed Instance를 사용 하면 메모리 내 OLTP 작업에 더 많은 메모리를 사용 하 여 작업을 수행할 수 있으며,이는 인스턴스의 가용성과 안정성에 영향을 줄 수 있습니다. 한도에 도달한 메모리 내 OLTP 쿼리는 즉시 실패하지 않을 수도 있습니다. 이 문제는 곧 해결될 예정입니다. 메모리 내 OLTP 메모리를 더 많이 사용 하는 쿼리는 [제한](../managed-instance/resource-limits.md#in-memory-oltp-available-space)에 도달 하면 더 빨리 장애 조치 (failover) 됩니다.

**해결 방법**: [SQL Server Management Studio](/sql/relational-databases/in-memory-oltp/monitor-and-troubleshoot-memory-usage#bkmk_Monitoring) 를 사용 하 여 [메모리 내 OLTP 저장소 사용량을 모니터링](https://docs.microsoft.com/azure/azure-sql/in-memory-oltp-monitor-space) 하 여 작업에서 사용 가능한 메모리를 초과 하 여 사용 하지 않도록 합니다. vCore 수에 따라 메모리 한도를 늘리거나 메모리를 덜 사용하도록 워크로드를 최적화합니다.
 
### <a name="wrong-error-returned-while-trying-to-remove-a-file-that-is-not-empty"></a>비어 있지 않은 파일을 제거하는 동안 잘못된 오류가 반환됨

SQL Server 및 SQL Managed Instance [사용자가 비어 있지 않은 파일을 삭제할 수](/sql/relational-databases/databases/delete-data-or-log-files-from-a-database#Prerequisites)없습니다. 문을 사용 하 여 비어 있지 않은 데이터 파일을 제거 하려고 하면 `ALTER DATABASE REMOVE FILE` 오류가 `Msg 5042 – The file '<file_name>' cannot be removed because it is not empty` 즉시 반환 되지 않습니다. SQL Managed Instance는 파일을 삭제 하려고 계속 시도 하며, 30 분 후에 작업이 실패 합니다 `Internal server error` .

**해결 방법**: 명령을 사용 하 여 파일의 내용을 제거 합니다 `DBCC SHRINKFILE (N'<file_name>', EMPTYFILE)` . 파일 그룹의 유일한 파일인 경우 파일을 축소 하기 전에이 파일 그룹에 연결 된 테이블이 나 파티션에서 데이터를 삭제 하 고 필요에 따라이 데이터를 다른 테이블/파티션에 로드 해야 합니다.

### <a name="change-service-tier-and-create-instance-operations-are-blocked-by-ongoing-database-restore"></a>진행 중인 데이터베이스 복원으로 인해 서비스 계층 변경 및 인스턴스 만들기 작업이 차단됨

진행 중인 `RESTORE` 문, 데이터 마이그레이션 서비스 마이그레이션 프로세스 및 기본 제공 시점 복원은 복원 프로세스가 완료 될 때까지 서비스 계층을 업데이트 하거나 기존 인스턴스의 크기를 조정 하 고 새 인스턴스를 만드는 것을 차단 합니다. 

복원 프로세스는 복원 프로세스가 실행 되는 동일한 서브넷의 관리 되는 인스턴스 및 인스턴스 풀에서 이러한 작업을 차단 합니다. 인스턴스 풀의 인스턴스는 영향을 받지 않습니다. 서비스 계층 작업을 만들거나 변경 하면 실패 하거나 시간이 초과 되지 않습니다. 복원 프로세스가 완료 되거나 취소 되 면 계속 됩니다.

**해결 방법**: 복원 프로세스가 완료 될 때까지 기다리거나, 만들기 또는 업데이트-서비스 계층 작업의 우선 순위가 더 높은 경우 복원 프로세스를 취소 합니다.

### <a name="resource-governor-on-business-critical-service-tier-might-need-to-be-reconfigured-after-failover"></a>장애 조치(failover) 후 중요 비즈니스용 서비스 계층의 Resource Governor를 재구성해야 할 수도 있음

사용자 작업에 할당 된 리소스를 제한할 수 있도록 하는 [Resource Governor](/sql/relational-databases/resource-governor/resource-governor) 기능은 장애 조치 (failover) 후 또는 사용자가 시작한 서비스 계층 변경 (예: 최대 vcore 또는 최대 인스턴스 저장소 크기의 변경)으로 인해 일부 사용자 작업을 잘못 분류 하는 것일 수 있습니다.

**해결 방법**: `ALTER RESOURCE GOVERNOR RECONFIGURE` [Resource Governor](/sql/relational-databases/resource-governor/resource-governor)를 사용 하는 경우 인스턴스가 시작 될 때 sql 태스크를 실행 하는 sql 에이전트 작업의 일부로 정기적으로 또는를 실행 합니다.

### <a name="cross-database-service-broker-dialogs-must-be-reinitialized-after-service-tier-upgrade"></a>서비스 계층 업그레이드 후 데이터베이스 간 Service Broker 대화 상자를 다시 초기화 해야 함

데이터베이스 간 Service Broker dialog는 서비스 계층 변경 작업 후에 다른 데이터베이스의 서비스로 메시지를 배달하지 않습니다. 메시지는 *손실 되지*않으며 발신자 큐에서 찾을 수 있습니다. SQL Managed Instance에서 vCores 또는 인스턴스 저장소 크기를 변경 하면 `service_broke_guid` 모든 데이터베이스에 대해 [sys. 데이터베이스](/sql/relational-databases/system-catalog-views/sys-databases-transact-sql) 보기의 값이 변경 됩니다. `DIALOG`다른 데이터베이스의 Service broker를 참조 하는 [BEGIN DIALOG](/sql/t-sql/statements/begin-dialog-conversation-transact-sql) 문을 사용 하 여 만든 모든는 대상 서비스에 대 한 메시지 배달을 중지 합니다.

**해결 방법**: 서비스 계층을 업데이트 하기 전에 데이터베이스 간 Service Broker 대화 상자를 사용 하는 작업을 중지 하 고 나중에 다시 초기화 합니다. 서비스 계층 변경 후 배달 되지 않은 메시지가 남아 있으면 원본 큐에서 메시지를 읽고 대상 큐에 다시 보냅니다.

### <a name="impersonation-of-azure-ad-login-types-is-not-supported"></a>Azure AD 로그인 유형의 가장은 지원 되지 않습니다.

`EXECUTE AS USER` `EXECUTE AS LOGIN` 다음 Azure Active Directory (Azure AD) 보안 주체를 사용 하는 가장이 지원 되지 않습니다.
-   별칭을 지정 하는 Azure AD 사용자입니다. 이 경우 다음과 같은 오류가 반환 됩니다 `15517` ..
- Azure ad 응용 프로그램 또는 서비스 주체를 기반으로 하는 azure AD 로그인 및 사용자입니다. 이 경우 및와 같은 오류가 반환 됩니다. `15517` `15406`

### <a name="query-parameter-not-supported-in-sp_send_db_mail"></a>@query 매개 변수가 sp_send_db_mail에서 지원되지 않음

[sp_send_db_mail](/sql/relational-databases/system-stored-procedures/sp-send-dbmail-transact-sql) 프로시저의 `@query` 매개 변수가 작동하지 않습니다.

### <a name="transactional-replication-must-be-reconfigured-after-geo-failover"></a>지역 장애 조치(failover) 후 트랜잭션 복제를 다시 구성해야 함

자동 장애 조치 그룹의 데이터베이스에서 트랜잭션 복제를 사용 하는 경우 SQL Managed Instance 관리자는 이전 주 데이터베이스에서 모든 게시를 정리 하 고 다른 지역으로 장애 조치 (failover) 한 후 새 주 데이터베이스에서 해당 게시를 다시 구성 해야 합니다. 자세한 내용은 [Replication](../managed-instance/transact-sql-tsql-differences-sql-server.md#replication)을 참조 하세요.

### <a name="azure-ad-logins-and-users-are-not-supported-in-ssdt"></a>Azure AD 로그인 및 사용자는 SSDT에서 지원 되지 않습니다.

SQL Server Data Tools는 Azure AD 로그인 및 사용자를 완전히 지원 하지 않습니다.

### <a name="temporary-database-is-used-during-restore-operation"></a>복원 작업 중에 임시 데이터베이스가 사용됨

데이터베이스가 SQL Managed Instance에서 복원 되는 경우 복원 서비스는 먼저 원하는 이름으로 빈 데이터베이스를 만들어 인스턴스에 이름을 할당 합니다. 잠시 후에이 데이터베이스가 삭제 되 고 실제 데이터베이스 복원이 시작 됩니다. 

*복원* 중인 상태에 있는 데이터베이스에는 이름 대신 임의의 GUID 값이 일시적으로 포함 됩니다. 복원 프로세스가 완료 되 면 임시 이름이 문에 지정 된 원하는 이름으로 변경 됩니다 `RESTORE` . 

초기 단계에서는 사용자가 빈 데이터베이스에 액세스 하 고이 데이터베이스에 테이블을 만들거나 데이터를 로드할 수도 있습니다. 임시 데이터베이스는 복원 서비스가 두 번째 단계를 시작하면 삭제됩니다.

**해결 방법**: 복원이 완료된 것을 볼 때까지 복원 중인 데이터베이스에 액세스하지 마십시오.

### <a name="tempdb-structure-and-content-is-re-created"></a>TEMPDB 구조와 콘텐츠가 다시 생성됨

`tempdb`데이터베이스는 항상 12 개의 데이터 파일로 분할 되며 파일 구조를 변경할 수 없습니다. 파일당 최대 크기는 변경할 수 없으며, `tempdb`에 새 파일을 추가할 수 없습니다. `Tempdb`는 인스턴스가 시작되거나 장애 조치(failover)될 때 항상 빈 데이터베이스로 다시 생성되며 `tempdb`에서 변경한 내용은 유지되지 않습니다.

### <a name="exceeding-storage-space-with-small-database-files"></a>작은 데이터베이스 파일이 포함된 스토리지 공간 초과

`CREATE DATABASE`, `ALTER DATABASE ADD FILE`, `RESTORE DATABASE` 문은 인스턴스가 Azure Storage 제한에 도달할 수 있기 때문에 실패할 수 있습니다.

SQL Managed Instance의 각 범용 인스턴스에는 Azure Premium 디스크 공간 용으로 예약 된 저장소 35 TB가 있습니다. 각 데이터베이스 파일은 별도의 실제 디스크에 배치됩니다. 디스크 크기는 128GB, 256GB, 512GB, 1TB 또는 4TB일 수 있습니다. 디스크에서 사용되지 않은 공간은 요금이 부과되지 않지만 Azure 프리미엄 디스크 크기의 총 합계는 35TB를 초과할 수 없습니다. 경우에 따라 총 8TB가 필요하지 않은 관리형 인스턴스는 내부 조각화로 인해 스토리지 크기에 대한 35TB Azure 제한을 초과할 수 있습니다.

예를 들어 SQL Managed Instance의 범용 인스턴스에는 4tb 디스크에 1.2 TB 크기의 큰 파일이 있을 수 있습니다. 또한 각각 1gb 이며 별도의 128 GB 디스크에 배치 되는 248 개의 파일이 있을 수 있습니다. 이 예제에서:

- 전체 할당된 디스크 스토리지 크기는 1x4TB + 248x128GB = 35TB입니다.
- 인스턴스에서 데이터베이스에 대해 예약된 총 공간은 1x1.2TB + 248x1GB = 1.4TB입니다.

이 예에서는 특정 상황에서 파일의 특정 배포로 인해 SQL Managed Instance 인스턴스가 연결 된 Azure Premium 디스크에 대해 예약 된 35-TB 제한에 도달할 수 있음을 보여 줍니다.

이 예에서 기존 데이터베이스는 계속 작동하며, 새 파일이 추가되지 않는 한 문제 없이 커질 수 있습니다. 모든 데이터베이스의 총 크기가 인스턴스 크기 제한에 도달하지 않더라도 새 디스크 드라이브에 대한 충분한 공간이 없기 때문에 새 데이터베이스를 만들거나 복원할 수 없습니다. 이 경우 반환되는 오류가 명확하지 않습니다.

시스템 뷰를 사용하여 [남은 파일 수를 식별](https://medium.com/azure-sqldb-managed-instance/how-many-files-you-can-create-in-general-purpose-azure-sql-managed-instance-e1c7c32886c1)할 수 있습니다. 이 제한에 도달하면 [DBCC SHRINKFILE 문을 사용하여 작은 파일 중 일부를 비우고 삭제](/sql/t-sql/database-console-commands/dbcc-shrinkfile-transact-sql#d-emptying-a-file)해보거나 [이런 제한이 없는 중요 비즈니스용 계층](/azure/sql-database/sql-database-managed-instance-resource-limits#service-tier-characteristics)으로 전환합니다.

### <a name="guid-values-shown-instead-of-database-names"></a>데이터베이스 이름 대신 GUID 값이 표시됨

몇 가지 시스템 뷰, 성능 카운터, 오류 메시지, XEvent 및 오류 로그 항목에는 실제 데이터베이스 이름 대신 GUID 데이터베이스 식별자가 표시됩니다. 이러한 GUID 식별자는 나중에 실제 데이터베이스 이름으로 대체되기 때문에 사용하지 마세요.

**해결 방법**: sys.debug 뷰를 사용 하 여 실제 데이터베이스 이름에서 GUID 데이터베이스 식별자 형식으로 지정 된 실제 데이터베이스 이름을 확인 합니다.

```tsql
SELECT name as ActualDatabaseName, physical_database_name as GUIDDatabaseIdentifier 
FROM sys.databases
WHERE database_id > 4
```

### <a name="error-logs-arent-persisted"></a>오류 로그가 지속되지 않음

SQL Managed Instance에서 사용할 수 있는 오류 로그는 지속 되지 않으며 크기는 최대 저장소 제한에 포함 되지 않습니다. 장애 조치(failover)가 발생하면 오류 로그가 자동으로 지워질 수 있습니다. 여러 가상 머신에서 SQL Managed Instance 여러 번 이동 되었으므로 오류 로그 기록에 차이가 있을 수 있습니다.

### <a name="transaction-scope-on-two-databases-within-the-same-instance-isnt-supported"></a>동일한 인스턴스 내의 두 데이터베이스에 대한 트랜잭션 범위가 지원되지 않음

**(2020년 3월에 해결됨)** 동일한 트랜잭션 범위의 동일한 인스턴스 내에서 데이터베이스 두 개에 쿼리 두 개를 보내면 .NET의 `TransactionScope` 클래스가 작동하지 않습니다.

```csharp
using (var scope = new TransactionScope())
{
    using (var conn1 = new SqlConnection("Server=quickstartbmi.neu15011648751ff.database.windows.net;Database=b;User ID=myuser;Password=mypassword;Encrypt=true"))
    {
        conn1.Open();
        SqlCommand cmd1 = conn1.CreateCommand();
        cmd1.CommandText = string.Format("insert into T1 values(1)");
        cmd1.ExecuteNonQuery();
    }

    using (var conn2 = new SqlConnection("Server=quickstartbmi.neu15011648751ff.database.windows.net;Database=b;User ID=myuser;Password=mypassword;Encrypt=true"))
    {
        conn2.Open();
        var cmd2 = conn2.CreateCommand();
        cmd2.CommandText = string.Format("insert into b.dbo.T2 values(2)");        cmd2.ExecuteNonQuery();
    }

    scope.Complete();
}

```

**해결 방법 (3 월 2020 이후에는 필요 하지 않음)**: 두 개의 연결을 사용 하는 대신 [SqlConnection 데이터베이스 (String)](/dotnet/api/system.data.sqlclient.sqlconnection.changedatabase) 를 사용 하 여 연결 컨텍스트에서 다른 데이터베이스를 사용 합니다.

### <a name="clr-modules-and-linked-servers-sometimes-cant-reference-a-local-ip-address"></a>CLR 모듈 및 연결된 서버에서 로컬 IP 주소를 참조할 수 없는 경우가 있음

SQL Managed Instance 및 연결 된 서버 또는 현재 인스턴스를 참조 하는 분산 쿼리의 CLR 모듈은 종종 로컬 인스턴스의 IP를 확인할 수 없습니다. 이 오류는 일시적인 문제입니다.

**해결 방법**: 가능한 경우 CLR 모듈에서 컨텍스트 연결을 사용 합니다.

## <a name="updates"></a>업데이트

SQL Database 업데이트 및 개선 사항 목록은 [SQL Database 서비스 업데이트](https://azure.microsoft.com/updates/?product=sql-database)를 참조하세요.

모든 Azure 서비스에 대한 개선 사항 및 업데이트는 [서비스 업데이트](https://azure.microsoft.com/updates)를 참조하세요.

## <a name="contribute-to-content"></a>콘텐츠에 기여

Azure SQL 설명서에 기여 하려면 [Docs 참여자 가이드](https://docs.microsoft.com/contribute/)를 참조 하세요.
