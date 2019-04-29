---
title: Azure SQL Database 관리형 인스턴스 데이터베이스에서 복제 구성 | Microsoft Docs
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
ms.openlocfilehash: b20a119a69ac796bc9ea85083d335f0a7d2fdf2d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60646767"
---
# <a name="configure-replication-in-an-azure-sql-database-managed-instance-database"></a>Azure SQL Database 관리형 인스턴스 데이터베이스에서 복제 구성

트랜잭션 복제를 사용하면 SQL Server 데이터베이스 또는 다른 인스턴스 데이터베이스에서 Azure SQL Database 관리형 인스턴스 데이터베이스로 데이터를 복제할 수 있습니다. 또한 트랜잭션 복제를 사용하여 Azure SQL Database 관리형 인스턴스의 인스턴스 데이터베이스에서 변경한 사항을 Azure SQL Database의 단일 데이터베이스, Azure SQL Database 탄력적 풀의 풀링된 데이터베이스로 내보낼 수 있습니다. 트랜잭션 복제는 [Azure SQL Database 관리형 인스턴스](sql-database-managed-instance.md)에서 공개 미리 보기로 제공됩니다. 관리되는 인스턴스는 게시자, 배포자 및 구독자 데이터베이스를 호스트할 수 있습니다. 사용 가능한 구성에 대해서는 [트랜잭션 복제 구성](sql-database-managed-instance-transactional-replication.md#common-configurations)을 참조하세요.

## <a name="requirements"></a>요구 사항

관리되는 인스턴스를 게시자 또는 배포자로 작동하도록 구성할 때 요구 사항은 다음과 같습니다.

- 관리되는 인스턴스가 현재 지역 복제 관계에 참여하고 있지 않습니다.

   >[!NOTE]
   >Azure SQL Database의 단일 데이터베이스 및 풀링된 데이터베이스만 구독자가 될 수 있습니다.

- 관리되는 인스턴스는 모두 동일한 vNet에 있어야 합니다.

- 연결은 복제 참가자 간에 SQL 인증을 사용합니다.

- 복제 작업 디렉터리에 대한 Azure Storage 계정 공유

- Azure 파일 공유에 액세스하려면 관리되는 인스턴스 서브넷의 보안 규칙에서 포트 445(TCP 아웃바운드)를 열어야 합니다.

## <a name="features"></a>기능

지원:

- Azure SQL Database의 SQL Server 온-프레미스 및 관리형 인스턴스의 트랜잭션 및 스냅숏 복제 조합.
- 구독자는 온-프레미스 SQL Server 데이터베이스를 Azure SQL Database 또는 Azure SQL Database 탄력적 풀에 풀링된 데이터베이스에 대 한 단일 데이터베이스/관리 되는 인스턴스 수 있습니다.
- 단방향 또는 양방향 복제.

Azure SQL Database의 관리형 인스턴스에서는 다음과 같은 기능이 지원되지 않습니다.

- 업데이트할 수 있는 구독
- [활성 지역 복제](sql-database-active-geo-replication.md) 하 고 [자동 장애 조치 그룹](sql-database-auto-failover-group.md) 트랜잭션 복제를 구성 된 경우 사용할 수 해야 합니다.

## <a name="configure-publishing-and-distribution-example"></a>게시 및 배포 예제 구성

1. 포털에서 [Azure SQL Database 관리형 인스턴스를 생성](sql-database-managed-instance-create-tutorial-portal.md)합니다.
2. 작업 디렉터리에 대한 [Azure Storage 계정을 만듭니다](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account#create-a-storage-account).

   저장소 키를 복사해야 합니다. [저장소 액세스 키 보기 및 복사](../storage/common/storage-account-manage.md#access-keys
)를 참조하세요.
3. 게시자에 대한 인스턴스 데이터베이스를 만듭니다.

   아래 예제 스크립트에서 `<Publishing_DB>`를 인스턴스 데이터베이스의 이름으로 바꿉니다.

4. 배포자에 대한 SQL 인증을 사용하여 데이터베이스 사용자를 만듭니다. 보안 암호를 사용합니다.

   아래 예제 스크립트에서 이 SQL Server 계정 데이터베이스 사용자 및 암호로 `<SQL_USER>` 및 `<PASSWORD>`를 사용합니다.

5. [SQL Database Managed Instance에 연결합니다](sql-database-connect-query-ssms.md).

6. 다음 쿼리를 실행하여 배포자 및 배포 데이터베이스를 추가합니다.

   ```sql
   USE [master]
   GO
   EXEC sp_adddistributor @distributor = @@ServerName;
   EXEC sp_adddistributiondb @database = N'distribution';
   ```

7. 지정된 배포 데이터베이스를 사용하도록 게시자를 구성하려면 다음 쿼리를 업데이트하고 실행합니다.

   `<SQL_USER>` 및 `<PASSWORD>`를 SQL Server 계정 및 암호로 바꿉니다.

   `\\<STORAGE_ACCOUNT>.file.core.windows.net\<SHARE>`를 저장소 계정 값으로 바꿉니다.  

   `<STORAGE_CONNECTION_STRING>`을 Microsoft Azure 저장소 계정의 **액세스 키** 탭에서 연결 문자열로 바꿉니다.

   다음 쿼리를 업데이트한 후 실행합니다.

   ```sql
   USE [master]
   EXEC sp_adddistpublisher @publisher = @@ServerName,
                @distribution_db = N'distribution',
                @security_mode = 0,
                @login = N'<SQL_USER>',
                @password = N'<PASSWORD>',
                @working_directory = N'\\<STORAGE_ACCOUNT>.file.core.windows.net\<SHARE>',
                @storage_connection_string = N'<STORAGE_CONNECTION_STRING>';
   GO
   ```

8. 복제에 대한 게시자를 구성합니다.

    다음 쿼리에서 `<Publishing_DB>`를 게시자 데이터베이스의 이름으로 바꿉니다.

    `<Publication_Name>`를 게시의 이름으로 바꿉니다.

    `<SQL_USER>` 및 `<PASSWORD>`를 SQL Server 계정 및 암호로 바꿉니다.

    쿼리를 업데이트한 후 실행하여 게시를 만듭니다.

   ```sql
   USE [<Publishing_DB>]
   EXEC sp_replicationdboption @dbname = N'<Publishing_DB>',
                @optname = N'publish',
                @value = N'true';

   EXEC sp_addpublication @publication = N'<Publication_Name>',
                @status = N'active';

   EXEC sp_changelogreader_agent @publisher_security_mode = 0,
                @publisher_login = N'<SQL_USER>',
                @publisher_password = N'<PASSWORD>',
                @job_login = N'<SQL_USER>',
                @job_password = N'<PASSWORD>';

   EXEC sp_addpublication_snapshot @publication = N'<Publication_Name>',
                @frequency_type = 1,
                @publisher_security_mode = 0,
                @publisher_login = N'<SQL_USER>',
                @publisher_password = N'<PASSWORD>',
                @job_login = N'<SQL_USER>',
                @job_password = N'<PASSWORD>'
   ```

9. 문서, 구독 및 밀어넣기 구독 에이전트를 추가합니다.

   이러한 개체를 추가하려면 다음 스크립트를 업데이트합니다.

   - `<Object_Name>`을 게시 개체의 이름으로 바꿉니다.
   - `<Object_Schema>`를 원본 스키마의 이름으로 바꿉니다.
   - 꺾쇠 괄호(`<>`)의 다른 매개 변수를 이전 스크립트의 값과 일치하도록 바꿉니다.

   ```sql
   EXEC sp_addarticle @publication = N'<Publication_Name>',
                @type = N'logbased',
                @article = N'<Object_Name>',
                @source_object = N'<Object_Name>',
                @source_owner = N'<Object_Schema>'

   EXEC sp_addsubscription @publication = N'<Publication_Name>',
                @subscriber = @@ServerName,
                @destination_db = N'<Subscribing_DB>',
                @subscription_type = N'Push'

   EXEC sp_addpushsubscription_agent @publication = N'<Publication_Name>',
                @subscriber = @@ServerName,
                @subscriber_db = N'<Subscribing_DB>',
                @subscriber_security_mode = 0,
                @subscriber_login = N'<SQL_USER>',
                @subscriber_password = N'<PASSWORD>',
                @job_login = N'<SQL_USER>',
                @job_password = N'<PASSWORD>'
   GO
   ```
   
## <a name="see-also"></a>관련 항목

- [트랜잭션 복제](sql-database-managed-instance-transactional-replication.md)
- [Managed Instance란?](sql-database-managed-instance.md)
