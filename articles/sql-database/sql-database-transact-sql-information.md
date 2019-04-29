---
title: T-SQL 차이점 해결-마이그레이션-Azure SQL Database | Microsoft Docs
description: Azure SQL Database에서 완전히 지원되지 않는 Transact-SQL 문
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 12/03/2018
ms.openlocfilehash: 4d3f27d48819a4bd997cbb62177f5aae4afc85eb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60615391"
---
# <a name="resolving-transact-sql-differences-during-migration-to-sql-database"></a>SQL Database로의 마이그레이션 중 Transact-SQL 차이점 해결

SQL Server에서 Azure SQL Server로 [데이터베이스를 마이그레이션](sql-database-single-database-migrate.md)하는 경우 SQL Server를 마이그레이션하려면 먼저 데이터베이스를 다시 엔지니어링해야 할 수 있습니다. 이 문서에서는 엔지니어링을 다시 수행하는 데 도움이 되며 다시 엔지니어링해야 하는 근본적인 이유를 이해하는 데에도 도움이 되는 지침을 제공합니다. 비호환성을 검색하려면 [DMA(Data Migration Assistant)](https://www.microsoft.com/download/details.aspx?id=53595)를 사용합니다.

## <a name="overview"></a>개요

애플리케이션에서 사용하는 대부분의 Transact-SQL 기능은 Microsoft SQL Server와 Azure SQL Database 모두에서 완전하게 지원됩니다. 예를 들어, 데이터 형식, 연산자, 문자열, 산술, 논리 및 커서 함수 같은 핵심 SQL 구성 요소는 SQL Server 및 SQL Database에서 동일하게 작동합니다. 그러나 DDL(데이터 정의 언어) 및 DML(데이터 조작 언어) 요소에는 몇 가지 T-SQL 차이점이 있으며 그 결과 T-SQL 문 및 쿼리가 부분적으로만 지원됩니다(이 문서의 뒷부분에서 설명).

또한 Azure SQL Database는 마스터 데이터베이스 및 운영 체제에 대한 종속성으로부터 기능을 격리하도록 설계되어 전혀 지원되지 않는 기능과 구문도 있습니다. 따라서 대부분의 서버 수준 작업이 SQL Database에 적합하지 않습니다. T-SQL 문과 옵션은 서버 수준 옵션 또는 운영 체제 구성 요소를 구성하거나 파일 시스템 구성을 지정하는 경우에 사용할 수 없습니다. 이러한 기능이 필요한 경우 SQL Database 또는 다른 Azure 기능이나 서비스에서 다른 방법으로 사용할 수 있는 적합한 대안이 있는 경우가 많습니다.

예를 들어 [Always On 가용성 그룹](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server)과 비슷한 기술을 사용하여 고가용성을 Azure SQL Database에 기본 제공합니다. 가용성 그룹과 관련된 T-SQL 문은 SQL Database에서 지원되지 않으며 Always On 가용성 그룹에 관련된 동적 관리 뷰도 지원되지 않습니다.

SQL Database에서 지원되는 기능 및 지원되지 않는 기능 목록은  [Azure SQL Database 기능 비교](sql-database-features.md)를 참조하세요. 이 페이지의 목록은 해당 지침 및 기능 문서를 보완하고 Transact-SQL 문을 중점적으로 다룹니다.

## <a name="transact-sql-syntax-statements-with-partial-differences"></a>부분적으로 차이점이 있는 Transact-SQL 구문

핵심적인 DDL(데이터 정의 언어) 문은 사용할 수 있지만 일부 DDL 문에는 디스크 배치 및 지원되지 않는 기능과 관련된 확장이 포함되어 있습니다.

- CREATE 및 ALTER DATABASE 문에는 36개가 넘는 옵션이 있습니다. 이러한 문에는 SQL Server에만 적용되는 파일 배치, FILESTREAM 및 Service Broker 옵션이 포함되어 있습니다. 마이그레이션하기 전에 데이터베이스를 만드는 경우에는 문제가 되지 않지만 데이터베이스를 만드는 T-SQL 코드를 마이그레이션하는 경우에는 [CREATE DATABASE(Azure SQL Database)](https://msdn.microsoft.com/library/dn268335.aspx)를 [CREATE DATABASE(SQL Server Transact-SQL)](https://msdn.microsoft.com/library/ms176061.aspx)의 SQL Server 구문과 비교하여 사용하는 모든 옵션이 지원되는지 확인해야 합니다. Azure SQL Database의 CREATE DATABASE에는 SQL Database에만 적용되는 서비스 목표와 탄력적인 확장 옵션도 포함되어 있습니다.
- CREATE 및 ALTER TABLE 문에는 FILESTREAM이 지원되지 않으므로 SQL Database에서 사용할 수 없는 FileTable 옵션이 포함되어 있습니다.
- CREATE 및 ALTER login 문은 지원되지만 일부 옵션만 SQL Database에서 제공합니다. 데이터베이스 이식 가능성을 높이려면 SQL Database에서 가능한 경우 로그인 대신 포함된 데이터베이스 사용자를 사용하도록 권장합니다. 자세한 내용은 [CREATE/ALTER LOGIN](https://msdn.microsoft.com/library/ms189828.aspx) 및 [데이터베이스 액세스 제어 및 권한 부여](sql-database-manage-logins.md)를 참조하세요.

## <a name="transact-sql-syntax-not-supported-in-azure-sql-database"></a>Azure SQL Database에서 지원되지 않는 Transact-SQL 구문

 [Azure SQL Database 기능 비교](sql-database-features.md)에서 설명한 지원되지 않는 기능과 관련된 Transact-SQL 문 외에도 다음 명령문 및 명령문 그룹이 지원되지 않습니다. 따라서 마이그레이션할 데이터베이스에서 다음 기능을 사용하고 있는 경우 T-SQL을 다시 엔지니어링하여 해당 T-SQL 기능 및 문을 제거합니다.

- 시스템 개체의 데이터 정렬
- 연결 관련: 엔드포인트 문입니다. SQL Database는 Windows 인증을 지원하지 않으며 비슷한 Azure Active Directory 인증은 지원합니다. 최신 버전의 SSMS를 사용해야 하는 인증 형식도 있습니다. 자세한 내용은 [Azure Active Directory 인증을 사용하여 SQL Database 또는 SQL Data Warehouse에 연결](sql-database-aad-authentication.md)을 참조하세요.
- 세 개 또는 네 개의 부분으로 된 이름을 사용하여 데이터베이스 쿼리를 교차합니다. 읽기 전용 데이터베이스 간 쿼리는 [탄력적 데이터베이스 쿼리](sql-database-elastic-query-overview.md)를 통해 지원됩니다.
- 데이터베이스 간 소유권 체인, `TRUSTWORTHY` 설정
- `EXECUTE AS LOGIN` 대신 '사용자로 실행'을 사용합니다.
- 확장 가능 키 관리를 제외하고 암호화를 지원합니다.
- 이벤트: 이벤트, 이벤트 알림, 쿼리 알림
- 파일 배치: 데이터베이스 파일 배치, 크기 및 Microsoft Azure에서 자동으로 관리되는 데이터베이스 파일과 관련된 구문입니다.
- 고가용성: Microsoft Azure 계정을 통해 관리되는 고가용성과 관련된 구문입니다. 백업, 복원, Alwayson, 데이터베이스 미러링, 로그 전달, 복구 모드에 대한 구문을 포함합니다.
- 로그 판독기: SQL Database에서 사용할 수 없는 로그 판독기를 사용하는 구문: 밀어넣기 복제, 변경 데이터 캡처입니다. SQL Database는 푸시 복제 문서의 구독자가 될 수 있습니다.
- 함수: `fn_get_sql`, `fn_virtualfilestats`, `fn_virtualservernodes`
- 하드웨어: 하드웨어 관련 서버 설정과 관련된 구문: 메모리, 작업자 스레드 수, CPU 선호도, 추적 플래그 등입니다. 대신 서비스 계층 및 컴퓨팅 크기를 사용합니다.
- `KILL STATS JOB`
- `OPENQUERY`, `OPENROWSET`, `OPENDATASOURCE` 및 네 부분으로 된 이름
- .NET Framework: CLR과 SQL Server 통합
- 의미 체계 검색
- 서버 자격 증명: 대신 [데이터베이스 범위 자격 증명](https://msdn.microsoft.com/library/mt270260.aspx)을 사용합니다.
- 서버 수준 항목: 서버 역할, `sys.login_token`. 서버 수준 사용 권한의 `GRANT`, `REVOKE`, `DENY`은 사용할 수 없지만 일부는 데이터베이스 수준 사용 권한으로 대체됩니다. 몇 가지 유용한 서버 수준 DMV에는 동등한 데이터베이스 수준 DMV가 있습니다.
- `SET REMOTE_PROC_TRANSACTIONS`
- `SHUTDOWN`
- `sp_addmessage`
- `sp_configure` 옵션 및 `RECONFIGURE`입니다. [ALTER DATABASE SCOPED CONFIGURATION](https://msdn.microsoft.com/library/mt629158.aspx)을 사용하면 일부 옵션은 사용 가능합니다.
- `sp_helpuser`
- `sp_migrate_user_to_contained`
- SQL Server 에이전트: SQL Server 에이전트 또는 MSDB 데이터베이스를 사용하는 구문: 경고, 운영자, 중앙 관리 서버입니다. 대신 Azure PowerShell과 같은 스크립팅을 사용합니다.
- SQL Server 감사: 대신 SQL Database 감사를 사용합니다.
- SQL Server 추적
- 추적 플래그: 일부 추적 플래그 항목은 호환 모드로 이동되었습니다.
- TRANSACT-SQL 디버깅
- 트리거: 서버 범위 또는 로그온 트리거
- `USE` 문: 데이터베이스 컨텍스트를 다른 데이터베이스로 변경하려면 새 데이터베이스에 대한 새 연결을 설정해야 합니다.

## <a name="full-transact-sql-reference"></a>전체 TRANSACT-SQL 참조

Transact-SQL 문법, 사용법 및 예제에 대한 자세한 내용은 SQL Server 온라인 설명서의  [Transact-SQL 참조(데이터베이스 엔진)](https://msdn.microsoft.com/library/bb510741.aspx) 를 참조하세요.

### <a name="about-the-applies-to-tags"></a>'적용 대상' 태그 정보

Transact-SQL 참조에는 현재까지 게시된 SQL Server 버전 2008과 관련된 문서가 포함되어 있습니다. 문서 제목 아래에 있는 아이콘 모음에는 SQL Server 플랫폼 4개와 적용 가능 여부가 나타납니다. 예를 들어 가용성 그룹은 SQL Server 2012에서 도입되었습니다.  [CREATE AVAILABILITY GROUP](https://msdn.microsoft.com/library/ff878399.aspx) 문서는 이 명령문이 **SQL Server 2012**부터 적용됨을 나타냅니다. SQL Server 2008, SQL Server 2008 R2, Azure SQL Database, Azure SQL Data Warehouse 또는 병렬 데이터 웨어하우스에는 이 문이 적용되지 않습니다.

경우에 따라 문서의 일반 제목이 제품에 사용할 수 있지만 제품 간에 사소한 차이가 있습니다. 차이점은 문서의 중간에 적절하게 표시됩니다. 경우에 따라 문서의 일반 제목이 제품에 사용할 수 있지만 제품 간에 사소한 차이가 있습니다. 차이점은 문서의 중간에 적절하게 표시됩니다. 예를 들어 CREATE TRIGGER 문서는 SQL Database에서 사용할 수 있습니다. 하지만 서버 수준 트리거에 필요한 **ALL SERVER** 옵션은 SQL Database에서 서버 수준 트리거를 사용할 수 없음을 나타냅니다. 대신 데이터베이스 수준 트리거를 사용합니다.

## <a name="next-steps"></a>다음 단계

SQL Database에서 지원되는 기능 및 지원되지 않는 기능 목록은  [Azure SQL Database 기능 비교](sql-database-features.md)를 참조하세요. 이 페이지의 목록은 해당 지침 및 기능 문서를 보완하고 Transact-SQL 문을 중점적으로 다룹니다.
