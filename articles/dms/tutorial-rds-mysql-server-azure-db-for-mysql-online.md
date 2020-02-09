---
title: '자습서: RDS MySQL online을 Azure Database for MySQL로 마이그레이션'
titleSuffix: Azure Database Migration Service
description: Azure Database Migration Service를 사용하여 RDS MySQL에서 Azure Database for MySQL로 온라인 마이그레이션하는 방법을 알아봅니다.
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
ms.openlocfilehash: c34de48d0184057f42d1b779abee56e1fa9ac169
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/08/2020
ms.locfileid: "75751294"
---
# <a name="tutorial-migrate-rds-mysql-to-azure-database-for-mysql-online-using-dms"></a>자습서: DMS를 사용 하 여 Azure Database for MySQL 온라인으로 RDS MySQL 마이그레이션

Azure Database Migration Service를 사용하면 마이그레이션 중에 원본 데이터베이스를 온라인 상태로 유지하면서 RDS MySQL 인스턴스에서 [Azure Database for MySQL](https://docs.microsoft.com/azure/mysql/)로 데이터베이스를 마이그레이션할 수 있습니다. 즉, 애플리케이션의 가동 중지 시간을 최소화하면서 마이그레이션을 수행할 수 있습니다. 이 자습서에서는 Azure Database Migration Service의 온라인 마이그레이션 작업을 사용하여 **Employees** 샘플 데이터베이스를 RDS MySQL 인스턴스에서 Azure Database for MySQL로 마이그레이션합니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.
> [!div class="checklist"]
>
> * mysqldump 및 mysql 유틸리티를 사용하여 샘플 스키마를 마이그레이션합니다.
> * Azure Database Migration Service 인스턴스를 만듭니다.
> * Azure Database Migration Service를 사용하여 마이그레이션 프로젝트를 만듭니다.
> * 마이그레이션을 실행합니다.
> * 마이그레이션을 모니터링합니다.

> [!NOTE]
> Azure Database Migration Service를 사용하여 온라인 마이그레이션을 수행하려면 프리미엄 가격 책정 계층에 따라 인스턴스를 만들어야 합니다. 자세한 내용은 Azure Database Migration Service [가격 책정](https://azure.microsoft.com/pricing/details/database-migration/) 페이지를 참조하세요.

> [!IMPORTANT]
> 최적의 마이그레이션 환경을 위해 대상 데이터베이스와 동일한 Azure 지역에서 Azure Database Migration Service의 인스턴스를 만드는 것이 좋습니다. 영역 또는 지역 간에 데이터를 이동하면 마이그레이션 프로세스 속도가 저하되고 오류가 발생할 수 있습니다.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

이 문서에서는 RDS MySQL 인스턴스에서 Azure Database for MySQL로 온라인 마이그레이션을 수행하는 방법을 설명합니다.

## <a name="prerequisites"></a>필수 조건

이 자습서를 완료하려면 다음이 필요합니다.

* 원본 MySQL 서버가 지원되는 MySQL 커뮤니티 버전을 실행 중인지 확인합니다. MySQL 인스턴스의 버전을 확인하려면 mysql 유틸리티 또는 MySQL Workbench에서 다음 명령을 실행합니다.

    ```
    SELECT @@version;
    ```

    자세한 내용은 [지원되는 Azure Database for MySQL 버전](https://docs.microsoft.com/azure/mysql/concepts-supported-versions) 문서를 참조하세요.

* [MySQL **Employees** 샘플 데이터베이스](https://dev.mysql.com/doc/employee/en/employees-installation.html)를 다운로드하여 설치합니다.
* [Azure Database for MySQL](https://docs.microsoft.com/azure/mysql/quickstart-create-mysql-server-database-using-azure-portal) 인스턴스를 만듭니다.
* [Express](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) 경로 또는 [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways)을 사용 하 여 온-프레미스 원본 서버에 대한 사이트 간 연결을 제공 하는 Azure Resource Manager 배포 모델을 사용 하 여 Azure Database Migration Service에 대한 Microsoft Azure Virtual Network를 만듭니다. 가상 네트워크를 만드는 방법에 대한 자세한 내용은 [Virtual Network 설명서](https://docs.microsoft.com/azure/virtual-network/)와 특히 단계별 정보를 포함 하는 빠른 시작 문서를 참조 하세요.
* 가상 네트워크 네트워크 보안 그룹 규칙에서 Azure Database Migration Service에 대한 인바운드 통신 포트 (443, 53, 9354, 445 및 12000)를 차단 하지 않는지 확인 합니다. Virtual network NSG 트래픽 필터링에 대한 자세한 내용은 [네트워크 보안 그룹을 사용 하 여 네트워크 트래픽 필터링](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg)문서를 참조 하세요.
* 데이터베이스 엔진 액세스를 허용하도록 [Windows 방화벽](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access)(또는 Linux 방화벽)을 구성합니다. MySQL 서버의 경우 연결을 위해 포트 3306을 허용합니다.

> [!NOTE]
> Azure Database for MySQL은 InnoDB 테이블만 지원합니다. MyISAM 테이블을 InnoDB로 변환하는 방법은 [MyISAM에서 InnoDB로 테이블 변환](https://dev.mysql.com/doc/refman/5.7/en/converting-tables-to-innodb.html) 문서를 참조하세요.

### <a name="set-up-aws-rds-mysql-for-replication"></a>AWS RDS MySQL에 복제 설정

1. 새 매개 변수 그룹을 만들려면 AWS에서 제공하는 [MySQL 데이터베이스 로그 파일](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_LogAccess.Concepts.MySQL.html) 문서의 **이진 로깅 형식** 섹션에 설명된 지침을 따릅니다.
2. 다음 구성을 사용하여 새 매개 변수 그룹을 만듭니다.
    * binlog_format = row
    * binlog_checksum = NONE
3. 새 매개 변수 그룹을 저장합니다.
4. 새 매개 변수 그룹을 RDS MySQL 인스턴스와 연결합니다. 다시 부팅이 필요할 수 있습니다.

## <a name="migrate-the-schema"></a>스키마 마이그레이션

1. 테이블 스키마, 인덱스, 저장 프로시저 같은 모든 데이터베이스 개체의 마이그레이션을 완료하려면 원본 데이터베이스에서 스키마를 추출하여 대상 데이터베이스에 적용해야 합니다.

    스키마만 마이그레이션하는 가장 쉬운 방법은 --no-data 매개 변수에 mysqldump를 사용하는 것입니다. 스키마를 마이그레이션하는 명령은 다음과 같습니다.

    ```
    mysqldump -h [servername] -u [username] -p[password] --databases [db name] --no-data > [schema file path]
    ```
    
    예를 들어 **Employees** 데이터베이스의 스키마 파일을 덤프하려면 다음 명령을 사용합니다.
    
    ```
    mysqldump -h 10.10.123.123 -u root -p --databases employees --no-data > d:\employees.sql
    ```

2. 대상 서비스, 즉, Azure Database for MySQL로 스키마를 가져옵니다. 스키마 덤프 파일을 복원하려면 다음 명령을 실행합니다.

    ```
    mysql.exe -h [servername] -u [username] -p[password] [database]< [schema file path]
    ```

    예를 들어 **Employees** 데이터베이스의 스키마를 가져오려면 다음을 수행합니다.

    ```
    mysql.exe -h shausample.mysql.database.azure.com -u dms@shausample -p employees < d:\employees.sql
    ```

3. 스키마에 외래 키가 있으면 마이그레이션의 초기 로드 및 지속적인 동기화가 실패합니다. 드롭 외래 키 스크립트를 추출하고 대상(Azure Database for MySQL)에서 외래 키 스크립트를 추가하려면 MySQL Workbench에서 다음 스크립트를 실행합니다.

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

4. 외래 키를 삭제하려면 쿼리 결과에서 drop foreign key(두 번째 열)를 실행합니다.

5. 데이터에 트리거(트리거 삽입 또는 업데이트)가 있으면 원본의 데이터를 복제하기 전에 대상에 데이터 무결성이 적용됩니다. 마이그레이션 중에 *대상의* 모든 테이블에서 트리거를 사용하지 않도록 설정한 다음, 마이그레이션이 완료되면 트리거를 사용하도록 설정하는 것이 좋습니다.

    대상 데이터베이스에서 트리거를 해제하려면 다음을 수행합니다.

    ```
    select concat ('alter table ', event_object_table, ' disable trigger ', trigger_name)
    from information_schema.triggers;
    ```

6. 테이블에 ENUM 데이터 형식의 인스턴스가 있으면 대상 테이블에서 'character varying' 데이터 형식으로 일시적으로 업데이트하는 것이 좋습니다. 데이터 복제가 완료되면 데이터 형식을 ENUM으로 되돌립니다.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Microsoft.DataMigration 리소스 공급자 등록

1. Azure Portal에 로그인하고, **모든 서비스**를 선택한 다음, **구독**을 선택합니다.

   ![포털 구독 표시](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/portal-select-subscription1.png)

2. Azure Database Migration Service의 인스턴스를 만들 구독을 선택한 다음, **리소스 공급자**를 선택합니다.

    ![리소스 공급자 보기](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/portal-select-resource-provider.png)

3. 마이그레이션을 검색한 다음 **Microsoft.DataMigration**의 오른쪽에서 **등록**을 선택합니다.

    ![리소스 공급자 등록](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/portal-register-resource-provider.png)

## <a name="create-an-instance-of-azure-database-migration-service"></a>Azure Database Migration Service 인스턴스 만들기

1. Azure Portal에서 **+ 리소스 만들기**를 선택하고, Azure Database Migration Service를 검색한 다음, 드롭다운 목록에서 **Azure Database Migration Service**를 선택합니다.

    ![Azure Marketplace](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/portal-marketplace.png)

2. **Azure Database Migration Service** 화면에서 **만들기**를 선택합니다.

    ![Azure Database Migration Service 인스턴스 만들기](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-create1.png)
  
3. **Migration Service 만들기** 화면에서 서비스, 구독, 신규 또는 기존 리소스 그룹의 이름을 지정합니다.

4. Azure Database Migration Service 인스턴스를 만들 위치를 선택합니다.

5. 기존 가상 네트워크를 선택 하거나 새 가상 네트워크를 만드세요.

    가상 네트워크는 원본 MySQL 인스턴스와 대상 Azure Database for MySQL 인스턴스에 대한 액세스 권한을 Azure Database Migration Service 제공 합니다.

    Azure Portal에서 가상 네트워크를 만드는 방법에 대한 자세한 내용은 [Azure Portal를 사용 하 여 가상 네트워크 만들기](https://aka.ms/DMSVnet)문서를 참조 하세요.

6. 가격 책정 계층을 선택 합니다. 이 온라인 마이그레이션의 경우 프리미엄: 4vCores 가격 책정 계층을 선택 해야 합니다.

    ![Azure Database Migration Service 인스턴스 설정 구성](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-settings3.png)

7. **만들기**를 선택하여 서비스를 만듭니다.

## <a name="create-a-migration-project"></a>마이그레이션 프로젝트 만들기

서비스가 생성된 후 Azure Portal에서 서비스를 찾아 연 다음, 새로운 마이그레이션 프로젝트를 만듭니다.

1. Azure Portal에서 **모든 서비스**를 선택하고, Azure Database Migration Service를 검색하고 나서, **Azure Database Migration Services**를 선택합니다.

      ![Azure Database Migration Service의 모든 인스턴스 찾기](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-search.png)

2. **Azure Database Migration Services** 화면에서 방금 만든 Azure Database Migration Service 인스턴스의 이름을 검색하고 인스턴스를 선택합니다.

     ![Azure Database Migration Service 인스턴스 찾기](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-instance-search.png)

3. **+ 새 마이그레이션 프로젝트**를 선택합니다.
4. **새 마이그레이션 프로젝트** 화면에서 프로젝트 이름을 지정하고, **원본 서버 유형** 텍스트 상자에서 **MySQL**을 선택한 다음, **대상 서버 유형** 텍스트 상자에서 **AzureDbForMySQL**을 선택합니다.
5. **활동 유형 선택** 섹션에서 **온라인 데이터 마이그레이션**을 선택합니다.

    > [!IMPORTANT]
    > **온라인 데이터 마이그레이션**을 선택합니다. 이 시나리오에서는 오프라인 마이그레이션이 지원되지 않습니다.

    ![Database Migration Service 프로젝트 만들기](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-create-project6.png)

    > [!NOTE]
    > 또는 **프로젝트만 만들기**를 선택하여 지금 마이그레이션 프로젝트를 만들고, 나중에 마이그레이션을 실행할 수도 있습니다.

6. **저장**을 선택합니다.

7. **작업 만들기 및 실행**을 선택하여 프로젝트를 만들고 마이그레이션 작업을 실행합니다.

    > [!NOTE]
    > 프로젝트 만들기 블레이드에서 온라인 마이그레이션을 설정하는 데 필요한 필수 조건을 기록해 둡니다.

## <a name="specify-source-details"></a>원본 세부 정보 지정

* **마이그레이션 원본 세부 정보** 화면에서 원본 MySQL 인스턴스의 연결 세부 정보를 지정합니다.

   ![원본 세부 정보](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-source-details5.png)

## <a name="specify-target-details"></a>대상 세부 정보 지정

1. **저장**을 선택한 다음, **대상 세부 정보** 화면에서 대상 Azure Database for MySQL 서버의 연결 세부 정보를 지정합니다. 이 서버는 MySQLDump를 사용하여 미리 프로비저닝되었고 **Employees** 스키마가 배포되어 있습니다.

    ![대상 선택](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-select-target5.png)

2. **저장**을 선택한 다음, **대상 데이터베이스에 매핑** 화면에서 마이그레이션하기 위해 원본 및 대상 데이터베이스를 매핑합니다.

    대상 데이터베이스의 이름이 원본 데이터베이스와 동일하면 Azure Database Migration Service는 기본적으로 이 대상 데이터베이스를 선택합니다.

    ![대상 데이터베이스에 매핑](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-map-targets-activity5.png)

3. **저장**을 선택하고, **마이그레이션 요약** 화면의 **작업 이름** 텍스트 상자에서 마이그레이션 작업의 이름을 지정한 다음, 요약을 검토하여 원본 및 대상 세부 정보가 이전에 지정한 내용과 일치하는지 확인합니다.

    ![마이그레이션 요약](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-migration-summary2.png)

## <a name="run-the-migration"></a>마이그레이션 실행

* **마이그레이션 실행**을 선택합니다.

    마이그레이션 작업 창이 나타나고, 작업 **상태**는 **초기화 중**입니다.

## <a name="monitor-the-migration"></a>마이그레이션 모니터링

1. 마이그레이션 작업 화면에서 **새로 고침**을 선택하여 마이그레이션 **상태**가 **실행 중**으로 표시될 때까지 디스플레이를 업데이트합니다.

    ![작업 상태 - 실행 중](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-activity-status4.png)

2. **데이터베이스 이름** 아래에서 특정 데이터베이스를 선택하여 **전체 데이터 로드** 및 **증분 데이터 동기화** 작업의 마이그레이션 상태로 이동합니다.

    **전체 데이터 로드**는 초기 로드 마이그레이션 상태를 보여주고, **증분 데이터 동기화**는 CDC(변경 데이터 캡처) 상태를 보여줍니다.

    ![인벤토리 화면 - 전체 데이터 로드](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-inventory-full-load.png)

    ![인벤토리 화면 - 증분 데이터 동기화](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-inventory-incremental.png)

## <a name="perform-migration-cutover"></a>마이그레이션 중단 수행

초기 전체 로드가 완료되면 데이터베이스가 **중단 준비 완료**로 표시됩니다.

1. 데이터베이스 마이그레이션을 완료할 준비가 되면 **중단 시작**을 선택합니다.

    ![중단 시작](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-inventory-start-cutover.png)

2. 원본 데이터베이스로 들어오는 모든 트랜잭션을 중지해야 합니다. **보류 중인 변경 내용** 카운터가 **0**으로 표시될 때까지 기다립니다.
3. **확인**, **적용**을 차례로 선택합니다.
4. 데이터베이스 마이그레이션 상태가 **완료됨**으로 표시되면 애플리케이션을 새로운 대상 Azure Database for MySQL 데이터베이스에 연결합니다.

MySQL 온-프레미스 인스턴스를 Azure Database for MySQL로 온라인 마이그레이션하는 작업이 완료되었습니다.

## <a name="next-steps"></a>다음 단계

* Azure Database Migration Service에 대한 자세한 내용은 [Azure Database Migration Service란?](https://docs.microsoft.com/azure/dms/dms-overview) 문서를 참조하세요.
* Azure Database for MySQL에 대한 자세한 내용은 [Azure Database for MySQL이란?](https://docs.microsoft.com/azure/mysql/overview) 문서를 참조하세요.
* 기타 질문은 [Azure 데이터베이스 마이그레이션 관련 질문](mailto:AskAzureDatabaseMigrations@service.microsoft.com)이라는 별칭으로 이메일을 보내주세요.
