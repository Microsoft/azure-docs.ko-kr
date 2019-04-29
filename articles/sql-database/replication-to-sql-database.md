---
title: Azure SQL Database에 복제 | Microsoft Docs"
description: Azure SQL Database 단일 데이터베이스와 탄력적 풀의 데이터베이스와 함께 SQL Server 복제 사용에 대해 알아봅니다.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: allenwux
ms.author: xiwu
ms.reviewer: mathoma
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: b9d6569504b5352c6187afe12d903c986019c517
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60646817"
---
# <a name="replication-to-sql-database-single-and-pooled-databases"></a>SQL Database 단일 및 풀링된 데이터베이스에 복제

Azure SQL Database에서 [SQL Database 서버](sql-database-servers.md)의 단일 및 풀링된 데이터베이스에 대해 SQL Server 복제를 구성할 수 있습니다.  

## <a name="supported-configurations"></a>**지원되는 구성:**
  
- SQL Server는 온-프레미스에서 실행 중인 SQL Server의 인스턴스 또는 클라우드의 Azure 가상 머신에서 실행 중인 SQL Server의 인스턴스일 수 있습니다. 자세한 내용은 [Azure Virtual Machines의 SQL Server 개요](https://azure.microsoft.com/documentation/articles/virtual-machines-sql-server-infrastructure-services/)를 참조하세요.  
- Azure SQL 데이터베이스는 SQL Server 게시자의 밀어넣기 구독자여야 합니다.  
- 배포 데이터베이스 및 복제 에이전트는 Azure SQL 데이터베이스에 배치할 수 없습니다.  
- 스냅숏 및 단방향 트랜잭션 복제가 지원됩니다. 피어 투 피어 트랜잭션 복제 및 병합 복제는 지원되지 않습니다.
- 복제는 Azure SQL Database Managed Instance에서 공개 미리 보기로 제공됩니다. Managed Instance는 게시자, 배포자 및 구독자 데이터베이스를 호스트할 수 있습니다. 자세한 내용은 [SQL Database Managed Instance를 사용하여 복제](replication-with-sql-database-managed-instance.md)를 참조하세요.

## <a name="versions"></a>버전  

- 게시자 및 배포자는 다음 버전 중 하나에 있어야 합니다.  
- SQL Server 2017(14.x)
- SQL Server 2016(13.x)
- SQL Server 2014(12.x) SP1 CU3
- SQL Server 2014(12.x) RTM CU10
- SQL Server 2012(11.x) SP2 CU8 또는 SP3
- 이전 버전을 사용하여 복제를 구성하는 시도는 오류 번호 MSSQL_REPL20084(프로세스가 구독자에 연결할 수 없습니다.) 및 MSSQL_REPL40532(로그인에서 요청한 서버 \<이름>을 열 수 없습니다. 로그인이 실패했습니다.)가 발생할 수 있습니다.  
- Azure SQL Database의 모든 기능을 사용하려면 최신 버전의 [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) 및 [SQL Server Data Tools](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt)를 사용해야 합니다.  
  
## <a name="remarks"></a>설명

- [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)를 사용하거나 게시자에서 Transact-SQL 명령문을 실행하여 복제를 구성할 수 있습니다. Azure Portal을 사용하여 복제를 구성할 수 없습니다.  
- 복제는 SQL Server 인증 로그인만을 사용하여 Azure SQL 데이터베이스에 연결할 수 있습니다.
- 복제된 테이블에는 기본 키가 있어야 합니다.  
- 기존 Azure 구독이 있어야 합니다.  
- Azure SQL 데이터베이스 구독자는 모든 지역에 있을 수 있습니다.  
- SQL Server에서 단일 게시는 Azure SQL Database 및 SQL Server(온-프레미스 및 Azure 가상 머신의 SQL Server) 구독자를 모두 지원할 수 있습니다.  
- 복제 관리, 모니터링 및 문제 해결은 온-프레미스 SQL Server에서 수행되어야 합니다.  
- Azure SQL Database에 대한 밀어넣기 구독만 지원됩니다.  
- `@subscriber_type = 0`만 SQL Database에 대한 **sp_addsubscription**에서 지원됩니다.  
- Azure SQL Database는 양방향, 즉시, 업데이트 가능한 또는 피어 투 피어 복제를 지원하지 않습니다.

## <a name="replication-architecture"></a>복제 아키텍처  

![replication-to-sql-database](./media/replication-to-sql-database/replication-to-sql-database.png)  

## <a name="scenarios"></a>시나리오  

### <a name="typical-replication-scenario"></a>일반적인 복제 시나리오  

1. 온-프레미스 SQL Server 데이터베이스에서 트랜잭션 복제 게시를 만듭니다.  
2. 온-프레미스 SQL Server에서 **새 구독 마법사** 또는 Transact-SQL 명령문을 사용하여 Azure SQL Database에 대한 구독 밀어넣기를 생성합니다.  
3. Azure SQL Database에 단일 및 풀링된 데이터베이스를 사용하는 경우 초기 데이터 세트는 스냅숏 에이전트에 의해 생성되고 배포 에이전트에 의해 배포 및 적용된 스냅숏입니다. 관리형 인스턴스 데이터베이스와 함께 데이터베이스 백업을 사용하여 구독자 데이터베이스를 시드할 수도 있습니다.

### <a name="data-migration-scenario"></a>데이터 마이그레이션 시나리오  

1. 트랜잭션 복제를 사용하여 온-프레미스 SQL Server 데이터베이스에서 Azure SQL Database로 데이터를 복제합니다.  
2. 클라이언트 또는 중간 계층 애플리케이션을 리디렉션하여 Azure SQL 데이터베이스 복사본을 업데이트합니다.  
3. 테이블의 SQL Server 버전 업데이트를 중지하고 게시를 제거합니다.  

## <a name="limitations"></a>제한 사항

다음 옵션은 Azure SQL Database 구독에 대해 지원되지 않습니다.

- 파일 그룹 연결 복사  
- 테이블 파티션 구성표 복사  
- 인덱스 파티션 구성표 복사  
- 사용자 정의 통계 복사  
- 기본 바인딩 복사  
- 규칙 바인딩 복사  
- 전체 텍스트 인덱스 복사  
- XML XSD 복사  
- XML 인덱스 복사  
- 권한 복사  
- 공간 인덱스 복사  
- 필터링된 인덱스 복사  
- 데이터 압축 특성 복사  
- 스파스 열 특성 복사  
- 파일 스트림을 MAX 데이터 형식으로 변환  
- hierarchyid를 MAX 데이터 형식으로 변환  
- 공간을 MAX 데이터 형식으로 변환  
- 확장 속성 복사  
- 권한 복사  

### <a name="limitations-to-be-determined"></a>추후 결정될 제한 사항

- 데이터 정렬 복사  
- SP의 직렬화된 트랜잭션에서 실행  

## <a name="examples"></a>예

게시 및 밀어넣기 구독을 만듭니다. 자세한 내용은 다음을 참조하세요.
  
- [게시 만들기](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication)
- 구독자로는 Azure SQL Database 서버 이름(예: **N'azuresqldbdns.database.windows.net'**)을, 대상 데이터베이스로는 Azure SQL 데이터베이스 이름(예: **AdventureWorks**)을 사용하여 [밀어넣기 구독을 만듭니다](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription/).  

## <a name="see-also"></a>관련 항목  

- [트랜잭션 복제](sql-database-managed-instance-transactional-replication.md)
- [게시 만들기](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication)
- [밀어넣기 구독 만들기](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription/)
- [복제 유형](https://docs.microsoft.com/sql/relational-databases/replication/types-of-replication)
- [모니터링(복제)](https://docs.microsoft.com/sql/relational-databases/replication/monitor/monitoring-replication)
- [구독 초기화](https://docs.microsoft.com/sql/relational-databases/replication/initialize-a-subscription)  
