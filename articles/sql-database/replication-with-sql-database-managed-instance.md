---
title: Azure SQL Database 관리 되는 인스턴스 데이터베이스에서 복제 구성 | Microsoft Docs
description: Azure SQL Database 관리형 인스턴스 데이터베이스에서 트랜잭션 복제를 구성하는 방법 알아보기
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
ms.date: 02/07/2019
ms.openlocfilehash: e4d056aacf8f3969b645747e2303574f3fea3bda
ms.sourcegitcommit: a7ea412ca4411fc28431cbe7d2cc399900267585
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/25/2019
ms.locfileid: "67357112"
---
# <a name="configure-replication-in-an-azure-sql-database-managed-instance-database"></a>Azure SQL Database 관리형 인스턴스 데이터베이스에서 복제 구성

트랜잭션 복제를 사용하면 SQL Server 데이터베이스 또는 다른 인스턴스 데이터베이스에서 Azure SQL Database 관리형 인스턴스 데이터베이스로 데이터를 복제할 수 있습니다. 

또한에서 Azure SQL Database 관리 되는 인스턴스는 인스턴스 데이터베이스에 대 한 변경 내용을 푸시할 트랜잭션 복제를 사용할 수 있습니다.

- SQL Server 데이터베이스입니다.
- Azure SQL Database에서 단일 데이터베이스입니다.
- Azure SQL Database 탄력적 풀에 풀링된 데이터베이스입니다.
 
트랜잭션 복제에서 공개 미리 보기 상태인 [Azure SQL Database 관리 되는 인스턴스](sql-database-managed-instance.md)합니다. 관리되는 인스턴스는 게시자, 배포자 및 구독자 데이터베이스를 호스트할 수 있습니다. 사용 가능한 구성에 대해서는 [트랜잭션 복제 구성](sql-database-managed-instance-transactional-replication.md#common-configurations)을 참조하세요.

  > [!NOTE]
  > Azure Database를 사용 하 여 복제를 구성에 사용자를 안내 하도록이 문서에서는 리소스 그룹을 만들면부터 끝까지 인스턴스를 관리 합니다. 이미 배포 된 인스턴스가 관리 있어야 하는 경우 건너 뛰 세요 [4 단계](#4---create-a-publisher-database) 게시자 데이터베이스를 만들려면 또는 [6 단계](#6---configure-distribution) 이미 게시자 및 구독자 데이터베이스를 시작할 준비가 복제를 구성 합니다.  

## <a name="requirements"></a>요구 사항

게시자 및/또는 배포자로 작동 하도록 관리 되는 인스턴스를 구성 하는 작업에 필요 합니다.

- 관리되는 인스턴스가 현재 지역 복제 관계에 참여하고 있지 않습니다.
- 배포자 및 구독자와 동일한 가상 네트워크에는 관리 되는 인스턴스는 게시자는 또는 [vNet 피어 링](../virtual-network/tutorial-connect-virtual-networks-powershell.md) 세 개의 모든 엔터티의 가상 네트워크 간에 설정 된 합니다. 
- 연결은 복제 참가자 간에 SQL 인증을 사용합니다.
- 복제 작업 디렉터리에 대한 Azure Storage 계정 공유
- 445 포트 (TCP 아웃 바운드) Azure 파일 공유에 액세스 하려면 관리 되는 인스턴스에 대 한 NSG의 보안 규칙에서 열려 있습니다. 


 > [!NOTE]
 > Azure SQL Database의 단일 데이터베이스 및 풀링된 데이터베이스만 구독자가 될 수 있습니다. 


## <a name="features"></a>기능

지원:

- Azure SQL Database의 SQL Server 온-프레미스 및 관리형 인스턴스의 트랜잭션 및 스냅샷 복제 조합.
- 구독자는 온-프레미스 SQL Server 데이터베이스를 Azure SQL Database 또는 Azure SQL Database 탄력적 풀에 풀링된 데이터베이스에 대 한 단일 데이터베이스/관리 되는 인스턴스 수 있습니다.
- 단방향 또는 양방향 복제.

Azure SQL Database의 관리형 인스턴스에서는 다음과 같은 기능이 지원되지 않습니다.

- [업데이트할 수 있는 구독](/sql/relational-databases/replication/transactional/updatable-subscriptions-for-transactional-replication)합니다.
- [활성 지역 복제](sql-database-active-geo-replication.md) 하 고 [자동 장애 조치 그룹](sql-database-auto-failover-group.md) 트랜잭션 복제를 구성 된 경우 사용할 수 해야 합니다.
 
## <a name="1---create-a-resource-group"></a>1-리소스 그룹 만들기

사용 된 [Azure portal](https://portal.azure.com) 이름의 리소스 그룹을 만들려면 `SQLMI-Repl`합니다.  

## <a name="2---create-managed-instances"></a>2-관리 되는 인스턴스 만들기

사용 합니다 [Azure portal](https://portal.azure.com) 두 만들려면 [관리 되는 인스턴스](sql-database-managed-instance-create-tutorial-portal.md) 동일한 가상 네트워크 및 서브넷에 합니다. 두 개의 관리 되는 인스턴스 이름을 지정 해야 합니다.

- `sql-mi-pub`
- `sql-mi-sub`

또한 해야 [연결 하는 Azure VM 구성](sql-database-managed-instance-configure-vm.md) 를 Azure SQL Database 관리 되는 인스턴스. 

## <a name="3---create-azure-storage-account"></a>3-Azure Storage 계정 만들기

[Azure Storage 계정을 만듭니다](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account#create-a-storage-account) 작업 디렉터리에 대 한을 만든 다음을 [파일 공유](../storage/files/storage-how-to-create-file-share.md) 저장소 계정 내에서. 

형식의 파일 공유 경로 복사 합니다. `\\storage-account-name.file.core.windows.net\file-share-name`

형식의 저장소 액세스 키를 복사 합니다. `DefaultEndpointsProtocol=https;AccountName=<Storage-Account-Name>;AccountKey=****;EndpointSuffix=core.windows.net`

 자세한 내용은 [저장소 액세스 키 보기 및 복사](../storage/common/storage-account-manage.md#access-keys)를 참조하세요. 

## <a name="4---create-a-publisher-database"></a>4-게시자 데이터베이스 만들기

연결 프로그램 `sql-mi-pub` 관리 되는 SQL Server Management Studio를 사용 하 여 인스턴스 및 게시자 데이터베이스를 만들려면 다음 TRANSACT-SQL (T-SQL) 코드를 실행 합니다.

```sql
USE [master]
GO

CREATE DATABASE [ReplTran_PUB]
GO

USE [ReplTran_PUB]
GO
CREATE TABLE ReplTest (
    ID INT NOT NULL PRIMARY KEY,
    c1 VARCHAR(100) NOT NULL,
    dt1 DATETIME NOT NULL DEFAULT getdate()
)
GO


USE [ReplTran_PUB]
GO

INSERT INTO ReplTest (ID, c1) VALUES (6, 'pub')
INSERT INTO ReplTest (ID, c1) VALUES (2, 'pub')
INSERT INTO ReplTest (ID, c1) VALUES (3, 'pub')
INSERT INTO ReplTest (ID, c1) VALUES (4, 'pub')
INSERT INTO ReplTest (ID, c1) VALUES (5, 'pub')
GO
SELECT * FROM ReplTest
GO
```

## <a name="5---create-a-subscriber-database"></a>5-구독자 데이터베이스 만들기

연결 프로그램 `sql-mi-sub` 관리 되는 SQL Server Management Studio를 사용 하 여 인스턴스 및 빈 구독자 데이터베이스를 만들려면 다음 T-SQL 코드를 실행 합니다.

```sql
USE [master]
GO

CREATE DATABASE [ReplTran_SUB]
GO

USE [ReplTran_SUB]
GO
CREATE TABLE ReplTest (
    ID INT NOT NULL PRIMARY KEY,
    c1 VARCHAR(100) NOT NULL,
    dt1 DATETIME NOT NULL DEFAULT getdate()
)
GO
```

## <a name="6---configure-distribution"></a>6-배포를 구성 합니다.

연결 프로그램 `sql-mi-pub` 관리 되는 SQL Server Management Studio를 사용 하 여 인스턴스 및 배포 데이터베이스를 구성 하려면 다음 T-SQL 코드를 실행 합니다. 

```sql
USE [master]
GO

EXEC sp_adddistributor @distributor = @@ServerName;
EXEC sp_adddistributiondb @database = N'distribution';
GO
```

## <a name="7---configure-publisher-to-use-distributor"></a>7--배포자를 사용 하도록 게시자를 구성 하는 중 

게시자의 관리 되는 인스턴스 `sql-mi-pub`를 변경 하 고 쿼리 실행 [SQLCMD](/sql/ssms/scripting/edit-sqlcmd-scripts-with-query-editor) 모드 및 게시자를 사용 하 여 새 배포자를 등록 하려면 다음 코드를 실행 합니다. 

```sql
:setvar username loginUsedToAccessSourceManagedInstance
:setvar password passwordUsedToAccessSourceManagedInstance
:setvar file_storage "\\storage-account-name.file.core.windows.net\file-share-name"
:setvar file_storage_key "DefaultEndpointsProtocol=https;AccountName=<Storage-Account-Name>;AccountKey=****;EndpointSuffix=core.windows.net"


USE [master]
EXEC sp_adddistpublisher
  @publisher = @@ServerName,
  @distribution_db = N'distribution',
  @security_mode = 0,
  @login = N'$(username)',
  @password = N'$(password)',
  @working_directory = N'$(file_storage)',
  @storage_connection_string = N'$(file_storage_key)'; -- Remove this parameter for on-premises publishers
```

연결된 된 서버를 추가 하는이 스크립트는 관리 되는 인스턴스는 로컬 게시자를 구성 하 고 SQL Server 에이전트에 대 한 작업 집합을 만듭니다. 

## <a name="8---create-publication-and-subscriber"></a>8-게시 및 구독자 만들기

사용 하 여 [SQLCMD](/sql/ssms/scripting/edit-sqlcmd-scripts-with-query-editor) 모드에서는 데이터베이스에 대 한 복제를 사용 하도록 설정 하 고 게시자, 배포자 및 구독자 간의 복제를 구성 하려면 다음 T-SQL 스크립트를 실행 합니다. 

```sql
-- Set variables
:setvar username sourceLogin
:setvar password sourcePassword
:setvar source_db ReplTran_PUB
:setvar publication_name PublishData
:setvar object ReplTest
:setvar schema dbo
:setvar target_server "sql-mi-sub.wdec33262scj9dr27.database.windows.net"
:setvar target_username targetLogin
:setvar target_password targetPassword
:setvar target_db ReplTran_SUB

-- Enable replication for your source database
USE [$(source_db)]
EXEC sp_replicationdboption
  @dbname = N'$(source_db)',
  @optname = N'publish',
  @value = N'true';

-- Create your publication
EXEC sp_addpublication
  @publication = N'$(publication_name)',
  @status = N'active';


-- Configure your log reaer agent
EXEC sp_changelogreader_agent
  @publisher_security_mode = 0,
  @publisher_login = N'$(username)',
  @publisher_password = N'$(password)',
  @job_login = N'$(username)',
  @job_password = N'$(password)';

-- Add the publication snapshot
EXEC sp_addpublication_snapshot
  @publication = N'$(publication_name)',
  @frequency_type = 1,
  @publisher_security_mode = 0,
  @publisher_login = N'$(username)',
  @publisher_password = N'$(password)',
  @job_login = N'$(username)',
  @job_password = N'$(password)';

-- Add the ReplTest table to the publication
EXEC sp_addarticle 
  @publication = N'$(publication_name)',
  @type = N'logbased',
  @article = N'$(object)',
  @source_object = N'$(object)',
  @source_owner = N'$(schema)';

-- Add the subscriber
EXEC sp_addsubscription
  @publication = N'$(publication_name)',
  @subscriber = N'$(target_server)',
  @destination_db = N'$(target_db)',
  @subscription_type = N'Push';

-- Create the push subscription agent
EXEC sp_addpushsubscription_agent
  @publication = N'$(publication_name)',
  @subscriber = N'$(target_server)',
  @subscriber_db = N'$(target_db)',
  @subscriber_security_mode = 0,
  @subscriber_login = N'$(target_username)',
  @subscriber_password = N'$(target_password)',
  @job_login = N'$(target_username)',
  @job_password = N'$(target_password)';

-- Initialize the snapshot
EXEC sp_startpublication_snapshot
  @publication = N'$(publication_name)';
```

## <a name="9---modify-agent-parameters"></a>9-에이전트 매개 변수를 수정 합니다.

Azure SQL Database 관리 되는 인스턴스는 현재 문제가 몇 가지 백 엔드 복제 에이전트를 사용 하 여 연결 합니다. 이 문제는 해결 중인 해결 복제 에이전트에 대 한 로그인 제한 시간 값을 늘리려면 문제를 해결 합니다. 

로그인 제한 시간을 향상 하려면 게시자에서 다음 T-SQL 명령을 실행 합니다. 

```sql
-- Increase login timeout to 150s
update msdb..sysjobsteps set command = command + N' -LoginTimeout 150' 
where subsystem in ('Distribution','LogReader','Snapshot') and command not like '%-LoginTimeout %'
```

다시 로그인 제한 시간을 기본값으로 다시 설정 하려면 다음 T-SQL 명령을 실행 해야 할 작업을 수행 합니다.

```sql
-- Increase login timeout to 30
update msdb..sysjobsteps set command = command + N' -LoginTimeout 30' 
where subsystem in ('Distribution','LogReader','Snapshot') and command not like '%-LoginTimeout %'
```

이러한 변경 내용을 적용할 모든 세 명의 에이전트를 다시 시작 합니다. 

## <a name="10---test-replication"></a>10-복제를 테스트 합니다.

복제를 구성한 후에 게시자에 새 항목을 삽입 하 고 변경 내용이 구독자로 전파를 시청 하 여 테스트할 수 있습니다. 

구독자에서 행을 보려면 다음 T-SQL 코드 조각을 실행 합니다.

```sql
select * from dbo.ReplTest
```

게시자에서 추가 행을 삽입 하려면 다음 T-SQL 코드 조각을 실행 하 고 구독자에서 다시 행을 확인 합니다. 

```sql
INSERT INTO ReplTest (ID, c1) VALUES (15, 'pub')
```

## <a name="clean-up-resources"></a>리소스 정리

게시를 삭제 하려면 다음 T-SQL 명령을 실행 합니다.

```sql
-- Drops the publication
USE [ReplTran_PUB]
EXEC sp_droppublication @publication = N'PublishData'
GO
```

데이터베이스에서 복제 옵션을 제거 하려면 다음 T-SQL 명령을 실행 합니다.

```sql
-- Disables publishing of the database
USE [ReplTran_PUB]
EXEC sp_removedbreplication
GO
```

게시 및 배포를 해제 하려면 다음 T-SQL 명령을 실행 합니다.

```sql
-- Drops the distributor
USE [master]
EXEC sp_dropdistributor @no_checks = 1
GO
```

하 여 Azure 리소스를 정리할 수 있습니다 [리소스 그룹에서 관리 되는 인스턴스 리소스 삭제](../azure-resource-manager/manage-resources-portal.md#delete-resources) 한 다음 리소스 그룹을 삭제 하 고 `SQLMI-Repl`합니다. 

   
## <a name="see-also"></a>관련 항목

- [트랜잭션 복제](sql-database-managed-instance-transactional-replication.md)
- [Managed Instance란?](sql-database-managed-instance.md)
