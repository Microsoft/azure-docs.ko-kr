---
title: '자습서: Azure Database for MySQL에 MySQL online 마이그레이션'
titleSuffix: Azure Database Migration Service
description: Azure Database Migration Service를 사용하여 MySQL 온-프레미스에서 Azure Database for MySQL로 온라인 마이그레이션하는 방법을 알아봅니다.
services: dms
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 01/08/2020
ms.openlocfilehash: 77887f440da73436a995e0916c529f9df76e7d6f
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/08/2020
ms.locfileid: "75746960"
---
# <a name="tutorial-migrate-mysql-to-azure-database-for-mysql-online-using-dms"></a>자습서: DMS를 사용하여 Azure Database for MySQL로 온라인 MySQL 마이그레이션

Azure Database Migration Service를 사용하여 가동 중지 시간을 최소화하면서 데이터베이스를 온-프레미스 MySQL 인스턴스에서 [Azure Database for MySQL](https://docs.microsoft.com/azure/mysql/)로 마이그레이션할 수 있습니다. 즉 애플리케이션의 가동 중지 시간을 최소화하면서 마이그레이션을 수행할 수 있습니다. 이 자습서에서는 Azure Database Migration Service에서 온라인 마이그레이션 작업을 사용하여 **Employees** 샘플 데이터베이스를 MySQL 5.7의 온-프레미스 인스턴스에서 Azure Database for MySQL로 마이그레이션합니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.
> [!div class="checklist"]
>
> * mysqldump 유틸리티를 사용하여 샘플 스키마를 마이그레이션합니다.
> * Azure Database Migration Service 인스턴스를 만듭니다.
> * Azure Database Migration Service를 사용하여 마이그레이션 프로젝트를 만듭니다.
> * 마이그레이션을 실행합니다.
> * 마이그레이션을 모니터링합니다.

> [!NOTE]
> Azure Database Migration Service를 사용하여 온라인 마이그레이션을 수행하려면 프리미엄 가격 책정 계층에 따라 인스턴스를 만들어야 합니다.

> [!IMPORTANT]
> 최적의 마이그레이션 환경을 위해 Microsoft는 대상 데이터베이스와 동일한 Azure 지역에서 Azure Database Migration Service의 인스턴스를 만드는 것을 권장합니다. 영역 또는 지역 간에 데이터를 이동하면 마이그레이션 프로세스 속도가 저하되고 오류가 발생할 수 있습니다.

## <a name="prerequisites"></a>필수 조건

이 자습서를 완료하려면 다음이 필요합니다.

* [MySQL 커뮤니티 버전](https://dev.mysql.com/downloads/mysql/) 5.6 또는 5.7을 다운로드하여 설치합니다. 온-프레미스 MySQL 버전은 Azure Database for MySQL 버전과 일치해야 합니다. 예를 들어 MySQL 5.6은 Azure Database for MySQL 5.6으로만 마이그레이션할 수 있고, 5.7로는 업그레이드할 수 없습니다.
* [Azure Database for MySQL에 인스턴스를 만듭니다](https://docs.microsoft.com/azure/mysql/quickstart-create-mysql-server-database-using-azure-portal). Azure Portal을 사용하여 데이터베이스를 연결하고 만드는 방법에 대한 자세한 내용은 [MySQL Workbench를 사용하여 데이터 연결 및 쿼리](https://docs.microsoft.com/azure/mysql/connect-workbench) 문서를 참조하세요.  
* [Express](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) 경로 또는 [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways)을 사용 하 여 온-프레미스 원본 서버에 사이트 간 연결을 제공 하는 Azure Resource Manager 배포 모델을 사용 하 여 Azure Database Migration Service에 대한 Microsoft Azure Virtual Network를 만듭니다. 가상 네트워크를 만드는 방법에 대한 자세한 내용은 [Virtual Network 설명서](https://docs.microsoft.com/azure/virtual-network/)와 특히 단계별 정보를 포함 하는 빠른 시작 문서를 참조 하세요.

    > [!NOTE]
    > 가상 네트워크를 설정 하는 동안 Microsoft에 네트워크 피어 링을 사용 하는 Express 경로를 사용 하는 경우 서비스가 프로 비전 될 서브넷에 다음 서비스 [엔드포인트](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) 을 추가 합니다.
    >
    > * 대상 데이터베이스 엔드포인트(예: SQL 엔드포인트, Cosmos DB 엔드포인트 등)
    > * 스토리지 엔드포인트
    > * Service Bus 엔드포인트
    >
    > Azure Database Migration Service에는 인터넷 연결이 없으므로 이 구성이 필요합니다.

* 가상 네트워크 네트워크 보안 그룹 규칙에서 Azure Database Migration Service에 대한 인바운드 통신 포트 (443, 53, 9354, 445, 12000)를 차단 하지 않는지 확인 합니다. Virtual network NSG 트래픽 필터링에 대한 자세한 내용은 [네트워크 보안 그룹을 사용 하 여 네트워크 트래픽 필터링](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm)문서를 참조 하세요.
* [데이터베이스 엔진 액세스를 위한 Windows 방화벽](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access)을 구성합니다.
* Azure Database Migration Service가 기본적으로 3306 TCP 포트인 원본 MySQL Server에 액세스할 수 있도록 Windows 방화벽을 엽니다.
* 원본 데이터베이스 앞에 방화벽 어플라이언스를 사용하는 경우 Azure Database Migration Service에서 원본 데이터베이스에 액세스하여 마이그레이션할 수 있도록 허용하는 방화벽 규칙을 추가해야 합니다.
* Azure Database Migration Service에서 대상 데이터베이스에 액세스할 수 있도록 Azure Database for MySQL에 대한 서버 수준 [방화벽 규칙](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure)을 만듭니다. Azure Database Migration Service에 사용 되는 가상 네트워크의 서브넷 범위를 제공 합니다.
* 원본 MySQL은 지원되는 MySQL 커뮤니티 버전에 있어야 합니다. MySQL 유틸리티 또는 MySQL Workbench에서 MySQL 인스턴스의 버전을 확인하려면 다음 명령을 실행합니다.

    ```
    SELECT @@version;
    ```

* Azure Database for MySQL은 InnoDB 테이블만 지원합니다. MyISAM 테이블을 InnoDB로 변환하려면 [MyISAM에서 InnoDB로 테이블 변환](https://dev.mysql.com/doc/refman/5.7/en/converting-tables-to-innodb.html) 문서를 참조하세요.

* 다음 구성을 사용하여 원본 데이터베이스의 my.ini(Windows) 또는 my.cnf(Unix) 파일에서 이진 로깅을 사용하도록 설정합니다.

  * **server_id** = 1 이상(MySQL 5.6에만 해당)
  * **로그-bin** =\<경로 > (MySQL 5.6에만 해당). 예: log-bin = E:\ MySQL_logs \BinLog
  * **binlog_format** = row
  * **Expire_logs_days** = 5(0은 사용하지 않는 것이 좋음, MySQL 5.6에만 해당)
  * **Binlog_row_image** = full(MySQL 5.6에만 해당)
  * **log_slave_updates** = 1

* 다음 권한이 있는 ReplicationAdmin 역할이 사용자에게 있어야 합니다.

  * **REPLICATION CLIENT** - 변경 처리 작업에만 필요합니다. 즉, 전체 로드 전용 작업에는 이 권한이 필요하지 않습니다.
  * **REPLICATION REPLICA** - 변경 처리 작업에만 필요합니다. 즉, 전체 로드 전용 작업에는 이 권한이 필요하지 않습니다.
  * **SUPER** - MySQL 5.6.6 이전 버전에서만 필요합니다.

## <a name="migrate-the-sample-schema"></a>샘플 스키마 마이그레이션

테이블 스키마, 인덱스 및 저장 프로시저와 같은 모든 데이터베이스 개체를 완료하려면 원본 데이터베이스에서 스키마를 추출하고 데이터베이스에 적용해야 합니다. 스키마를 추출하려면 `--no-data` 매개 변수가 있는 mysqldump를 사용할 수 있습니다.

온-프레미스 시스템에 MySQL **Employees** 샘플 데이터베이스가 있다고 가정하는 경우 mysqldump를 사용하여 스키마 마이그레이션을 수행하는 명령은 다음과 같습니다.

```
mysqldump -h [servername] -u [username] -p[password] --databases [db name] --no-data > [schema file path]
```

예:

```
mysqldump -h 10.10.123.123 -u root -p --databases employees --no-data > d:\employees.sql
```

스키마를 Azure Database for MySQL 대상으로 가져오려면 다음 명령을 실행합니다.

```
mysql.exe -h [servername] -u [username] -p[password] [database]< [schema file path]
 ```

예:

```
mysql.exe -h shausample.mysql.database.azure.com -u dms@shausample -p employees < d:\employees.sql
 ```

스키마에 외래 키가 있으면 마이그레이션의 초기 로드 및 지속적인 동기화가 실패합니다.  MySQL Workbench에서 다음 스크립트를 실행하여 drop foreign key(외래 키 삭제) 스크립트를 추출하고 외래 키 스크립트를 추가합니다.

```
SET group_concat_max_len = 8192;
    SELECT SchemaName, GROUP_CONCAT(DropQuery SEPARATOR ';\n') as DropQuery, GROUP_CONCAT(AddQuery SEPARATOR ';\n') as AddQuery
    FROM
    (SELECT
    KCU.REFERENCED_TABLE_SCHEMA as SchemaName,
    KCU.TABLE_NAME,
    KCU.COLUMN_NAME,
    CONCAT('ALTER TABLE ', KCU.TABLE_NAME, ' DROP FOREIGN KEY ', KCU.CONSTRAINT_NAME) AS DropQuery,
    CONCAT('ALTER TABLE ', KCU.TABLE_NAME, ' ADD CONSTRAINT ', KCU.CONSTRAINT_NAME, ' FOREIGN KEY (`', KCU.COLUMN_NAME, '`) REFERENCES `', KCU.REFERENCED_TABLE_NAME, '` (`', KCU.REFERENCED_COLUMN_NAME, '`) ON UPDATE ',RC.UPDATE_RULE, ' ON DELETE ',RC.DELETE_RULE) AS AddQuery
    FROM INFORMATION_SCHEMA.KEY_COLUMN_USAGE KCU, information_schema.REFERENTIAL_CONSTRAINTS RC
    WHERE
      KCU.CONSTRAINT_NAME = RC.CONSTRAINT_NAME
      AND KCU.REFERENCED_TABLE_SCHEMA = RC.UNIQUE_CONSTRAINT_SCHEMA
  AND KCU.REFERENCED_TABLE_SCHEMA = 'SchemaName') Queries
  GROUP BY SchemaName;
 ```

외래 키를 삭제하려면 쿼리 결과에서 외래 키 삭제(두 번째 열)를 실행합니다.

> [!IMPORTANT]
> 백업을 사용하여 데이터를 가져오는 경우 mysqldump를 수행할 때 수동으로 또는 --skip-definer 명령을 사용하여 CREATE DEFINER 명령을 제거하세요. DEFINER는 Azure Database for MySQL에서 만들고 제한할 수 있는 슈퍼 권한이 필요합니다.

데이터에 트리거가 있으면(트리거 삽입 또는 업데이트) 원본의 복제된 데이터보다 먼저 대상에 데이터 무결성을 적용합니다. 마이그레이션 중에 대상의 모든 테이블에서 트리거를 사용하지 않도록 설정한 다음, 마이그레이션이 완료되면 트리거를 사용하도록 설정하는 것이 좋습니다.

대상 데이터베이스에서 트리거를 사용하지 않도록 설정하려면 다음 명령을 사용합니다.

```
SELECT Concat('DROP TRIGGER ', Trigger_Name, ';') FROM  information_schema.TRIGGERS WHERE TRIGGER_SCHEMA = 'your_schema';
```

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Microsoft.DataMigration 리소스 공급자 등록

1. Azure Portal에 로그인하고, **모든 서비스**를 선택한 다음, **구독**을 선택합니다.

   ![포털 구독 표시](media/tutorial-mysql-to-azure-mysql-online/portal-select-subscriptions.png)

2. Azure Database Migration Service의 인스턴스를 만들 구독을 선택한 다음, **리소스 공급자**를 선택합니다.

    ![리소스 공급자 보기](media/tutorial-mysql-to-azure-mysql-online/portal-select-resource-provider.png)

3. 마이그레이션을 검색한 다음 **Microsoft.DataMigration**의 오른쪽에서 **등록**을 선택합니다.

    ![리소스 공급자 등록](media/tutorial-mysql-to-azure-mysql-online/portal-register-resource-provider.png)

## <a name="create-a-dms-instance"></a>DMS 인스턴스 만들기

1. Azure Portal에서 **+ 리소스 만들기**를 선택하고, Azure Database Migration Service를 검색한 다음, 드롭다운 목록에서 **Azure Database Migration Service**를 선택합니다.

    ![Azure Marketplace](media/tutorial-mysql-to-azure-mysql-online/portal-marketplace.png)

2. **Azure Database Migration Service** 화면에서 **만들기**를 선택합니다.

    ![Azure Database Migration Service 인스턴스 만들기](media/tutorial-mysql-to-azure-mysql-online/dms-create1.png)
  
3. **Migration Service 만들기** 화면에서 서비스, 구독, 신규 또는 기존 리소스 그룹의 이름을 지정합니다.

4. 기존 가상 네트워크를 선택 하거나 새 가상 네트워크를 만드세요.

    가상 네트워크는 원본 SQL Server 및 대상 Azure SQL Database 인스턴스에 대한 액세스 권한을 Azure Database Migration Service 제공 합니다.

    Azure Portal에서 가상 네트워크를 만드는 방법에 대한 자세한 내용은 [Azure Portal를 사용 하 여 가상 네트워크 만들기](https://aka.ms/DMSVnet)문서를 참조 하세요.

5. 가격 책정 계층을 선택합니다.

    비용 및 가격 책정 계층에 대한 자세한 내용은 [가격 책정 페이지](https://aka.ms/dms-pricing)를 참조하세요.

    ![Azure Database Migration Service 인스턴스 설정 구성](media/tutorial-mysql-to-azure-mysql-online/dms-settings3.png)

6. **만들기**를 선택하여 서비스를 만듭니다.

## <a name="create-a-migration-project"></a>마이그레이션 프로젝트 만들기

서비스가 생성된 후 Azure Portal에서 서비스를 찾아 연 다음, 새로운 마이그레이션 프로젝트를 만듭니다.

1. Azure Portal에서 **모든 서비스**를 선택하고, Azure Database Migration Service를 검색하고 나서, **Azure Database Migration Services**를 선택합니다.

      ![Azure Database Migration Service의 모든 인스턴스 찾기](media/tutorial-mysql-to-azure-mysql-online/dms-search.png)

2. **Azure Database Migration Services** 화면에서 방금 만든 Azure Database Migration Service 인스턴스의 이름을 검색한 다음, 인스턴스를 선택합니다.

     ![Azure Database Migration Service 인스턴스 찾기](media/tutorial-mysql-to-azure-mysql-online/dms-instance-search.png)

3. **+ 새 마이그레이션 프로젝트**를 선택합니다.
4. **새 마이그레이션 프로젝트** 화면에서 프로젝트 이름을 지정하고, **원본 서버 유형** 텍스트 상자에서 **MySQL**을 선택하고, **대상 서버 유형** 텍스트 상자에서 **AzureDbForMySQL**을 선택합니다.
5. **활동 유형 선택** 섹션에서 **온라인 데이터 마이그레이션**을 선택합니다.

    ![Database Migration Service 프로젝트 만들기](media/tutorial-mysql-to-azure-mysql-online/dms-create-project4.png)

    > [!NOTE]
    > 또는 **프로젝트만 만들기**를 선택하여 지금 마이그레이션 프로젝트를 만들고, 나중에 마이그레이션을 실행할 수도 있습니다.

6. **저장**을 선택하고, DMS를 사용하여 데이터를 성공적으로 마이그레이션하기 위한 요구 사항을 기록한 다음, **활동 만들기 및 실행** 을 선택합니다.

## <a name="specify-source-details"></a>원본 세부 정보 지정

1. **원본 세부 정보 추가** 화면에서 원본 MySQL 인스턴스에 대한 연결 세부 정보를 지정합니다.

    ![원본 세부 정보 추가 화면](media/tutorial-mysql-to-azure-mysql-online/dms-add-source-details.png)

## <a name="specify-target-details"></a>대상 세부 정보 지정

1. **저장**을 선택한 다음, **대상 세부 정보** 화면에서 대상 Azure Database for MySQL 서버에 대한 연결 세부 정보를 지정합니다. 이 서버는 mysqldump를 사용하여 **Employees** 스키마가 배포된 Azure Database for MySQL의 미리 프로비전된 인스턴스입니다.

    ![대상 세부 정보 화면](media/tutorial-mysql-to-azure-mysql-online/dms-add-target-details.png)

2. **저장**을 선택한 다음, **대상 데이터베이스에 매핑** 화면에서 마이그레이션하기 위해 원본 및 대상 데이터베이스를 매핑합니다.

    대상 데이터베이스의 이름이 원본 데이터베이스와 동일하면 Azure Database Migration Service는 기본적으로 이 대상 데이터베이스를 선택합니다.

    ![대상 데이터베이스에 매핑](media/tutorial-mysql-to-azure-mysql-online/dms-map-target-details.png)

3. **저장**을 선택하고, **마이그레이션 요약** 화면의 **작업 이름** 텍스트 상자에서 마이그레이션 작업의 이름을 지정한 다음, 요약을 검토하여 원본 및 대상 세부 정보가 이전에 지정한 내용과 일치하는지 확인합니다.

    ![마이그레이션 요약](media/tutorial-mysql-to-azure-mysql-online/dms-migration-summary.png)

## <a name="run-the-migration"></a>마이그레이션 실행

* **마이그레이션 실행**을 선택합니다.

    마이그레이션 작업 창이 나타나고, 작업 **상태**는 **초기화 중**입니다.

## <a name="monitor-the-migration"></a>마이그레이션 모니터링

1. 마이그레이션 작업 화면에서 **새로 고침**을 선택하여 마이그레이션 **상태**가 **완료**로 표시될 때까지 디스플레이를 업데이트합니다.

     ![작업 상태 - 완료](media/tutorial-mysql-to-azure-mysql-online/dms-activity-completed.png)

2. **데이터베이스 이름** 아래에서 특정 데이터베이스를 선택하여 **전체 데이터 로드** 및 **증분 데이터 동기화** 작업에 대한 마이그레이션 상태로 이동합니다.

    전체 데이터 로드는 초기 로드 마이그레이션 상태를 표시하는 한편, 증분 데이터 동기화는 CDC(변경 데이터 캡처) 상태를 표시합니다.

     ![작업 상태 - 전체 로드 완료](media/tutorial-mysql-to-azure-mysql-online/dms-activity-full-load-completed.png)

     ![작업 상태 - 증분 데이터 동기화](media/tutorial-mysql-to-azure-mysql-online/dms-activity-incremental-data-sync.png)

## <a name="perform-migration-cutover"></a>마이그레이션 중단 수행

초기 전체 로드가 완료되면 데이터베이스가 **중단 준비 완료**로 표시됩니다.

1. 데이터베이스 마이그레이션을 완료할 준비가 되면 **중단 시작**을 선택합니다.

    ![중단 시작](media/tutorial-mysql-to-azure-mysql-online/dms-start-cutover.png)

2. 원본 데이터베이스로 들어오는 모든 트랜잭션을 중지해야 합니다. **보류 중인 변경 내용** 카운터가 **0**으로 표시될 때까지 기다립니다.
3. **확인**, **적용**을 차례로 선택합니다.
4. 데이터베이스 마이그레이션 상태가 **완료됨**으로 표시되면 애플리케이션을 새 대상 Azure SQL Database에 연결합니다.

## <a name="next-steps"></a>다음 단계

* Azure Database for MySQL 온라인 마이그레이션을 수행하는 경우와 관련하여 알려진 문제 및 제한 사항에 대한 자세한 내용은 [Azure Database for MySQL 온라인 마이그레이션의 알려진 문제 및 해결 방법](known-issues-azure-mysql-online.md) 문서를 참조하세요.
* Azure Database Migration Service에 대한 자세한 내용은 [Azure Database Migration Service란?](https://docs.microsoft.com/azure/dms/dms-overview) 문서를 참조하세요.
* Azure Database for MySQL에 대한 자세한 내용은 [Azure Database for MySQL이란?](https://docs.microsoft.com/azure/mysql/overview) 문서를 참조하세요.
