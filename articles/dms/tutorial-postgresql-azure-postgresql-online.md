---
title: '자습서: PostgreSQL online을 Azure Database for PostgreSQL로 마이그레이션'
titleSuffix: Azure Database Migration Service
description: Azure Database Migration Service를 사용하여 PostgreSQL 온-프레미스에서 Azure Database for PostgreSQL로 온라인 마이그레이션을 수행하는 방법을 알아봅니다.
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
ms.openlocfilehash: ee5863497ce067d2ff056c3fc1c64b00d3004cd8
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/31/2020
ms.locfileid: "76903928"
---
# <a name="tutorial-migrate-postgresql-to-azure-database-for-postgresql-online-using-dms"></a>자습서: DMS를 사용하여 PostgreSQL을 Azure Database for PostgreSQL로 온라인 마이그레이션

Azure Database Migration Service를 사용하여 가동 중지 시간을 최소화하면서 온-프레미스 PostgreSQL 인스턴스에서 [Azure Database for PostgreSQL](https://docs.microsoft.com/azure/postgresql/)로 데이터베이스를 마이그레이션할 수 있습니다. 즉, 애플리케이션의 가동 중지 시간을 최소화하면서 마이그레이션을 수행할 수 있습니다. 이 자습서에서는 Azure Database Migration Service에서 온라인 마이그레이션 작업을 사용하여 **DVD 대여** 샘플 데이터베이스를 PostgreSQL 9.6의 온-프레미스 인스턴스에서 Azure Database for PostgreSQL로 마이그레이션합니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.
> [!div class="checklist"]
>
> * pg_dump 유틸리티를 사용하여 샘플 스키마를 마이그레이션합니다.
> * Azure Database Migration Service의 인스턴스를 만듭니다.
> * Azure Database Migration Service를 사용하여 마이그레이션 프로젝트를 만듭니다.
> * 마이그레이션을 실행합니다.
> * 마이그레이션을 모니터링합니다.

> [!NOTE]
> Azure Database Migration Service를 사용하여 온라인 마이그레이션을 수행하려면 프리미엄 가격 책정 계층에 따라 인스턴스를 만들어야 합니다.

> [!IMPORTANT]
> 최적의 마이그레이션 환경을 위해 Microsoft는 대상 데이터베이스와 동일한 Azure 지역에서 Azure Database Migration Service의 인스턴스를 만드는 것을 권장합니다. 영역 또는 지역 간에 데이터를 이동하면 마이그레이션 프로세스 속도가 저하되고 오류가 발생할 수 있습니다.

## <a name="prerequisites"></a>필수 조건

이 자습서를 완료하려면 다음이 필요합니다.

* [PostgreSQL 커뮤니티 버전](https://www.postgresql.org/download/) 9.5, 9.6 또는 10을 다운로드하여 설치합니다. 원본 PostgreSQL 서버 버전은 9.5.11, 9.6.7, 10 이상이어야 합니다. 자세한 내용은 [지원되는 PostgreSQL 데이터베이스 버전](https://docs.microsoft.com/azure/postgresql/concepts-supported-versions) 문서를 참조하세요.

    또한 온-프레미스 PostgreSQL 버전은 Azure Database for PostgreSQL 버전과 일치해야 합니다. 예를 들어 PostgreSQL 9.5.11.5는 Azure Database for PostgreSQL 9.5.11로만 마이그레이션할 수 있고, 버전 9.6.7로는 업그레이드할 수 없습니다.

* [Azure Database for PostgreSQL에서 인스턴스를 만듭니다](https://docs.microsoft.com/azure/postgresql/quickstart-create-server-database-portal).  
* [Express](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) 경로 또는 [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways)을 사용 하 여 온-프레미스 원본 서버에 대한 사이트 간 연결을 제공 하는 Azure Resource Manager 배포 모델을 사용 하 여 Azure Database Migration Service에 대한 Microsoft Azure Virtual Network를 만듭니다. 가상 네트워크를 만드는 방법에 대한 자세한 내용은 [Virtual Network 설명서](https://docs.microsoft.com/azure/virtual-network/)와 특히 단계별 정보를 포함 하는 빠른 시작 문서를 참조 하세요.

    > [!NOTE]
    > 가상 네트워크를 설정 하는 동안 Microsoft에 네트워크 피어 링을 사용 하는 Express 경로를 사용 하는 경우 서비스가 프로 비전 될 서브넷에 다음 서비스 [엔드포인트](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) 을 추가 합니다.
    >
    > * 대상 데이터베이스 엔드포인트(예: SQL 엔드포인트, Cosmos DB 엔드포인트 등)
    > * 스토리지 엔드포인트
    > * Service Bus 엔드포인트
    >
    > Azure Database Migration Service에는 인터넷 연결이 없으므로 이 구성이 필요합니다.

* 가상 네트워크 NSG (네트워크 보안 그룹) 규칙이 Azure Database Migration Service에 대해 443, 53, 9354, 445, 12000 인바운드 통신 포트를 차단 하지 않는지 확인 합니다. Virtual network NSG 트래픽 필터링에 대한 자세한 내용은 [네트워크 보안 그룹을 사용 하 여 네트워크 트래픽 필터링](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm)문서를 참조 하세요.
* [데이터베이스 엔진 액세스를 위한 Windows 방화벽](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access)을 구성합니다.
* Windows 방화벽을 열고 Azure Database Migration Service에서 기본적으로 5432 TCP 포트인 원본 PostgreSQL 서버에 액세스할 수 있도록 허용합니다.
* 원본 데이터베이스 앞에 방화벽 어플라이언스를 사용하는 경우, Azure Database Migration Service가 마이그레이션을 위해 원본 데이터베이스에 액세스할 수 있게 허용하는 방화벽 규칙을 추가해야 합니다.
* Azure Database Migration Service에서 대상 데이터베이스에 액세스할 수 있도록 Azure Database for PostgreSQL에 대한 서버 수준 [방화벽 규칙](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure)을 만듭니다. Azure Database Migration Service에 사용 되는 가상 네트워크의 서브넷 범위를 제공 합니다.
* CLI를 호출하는 방법은 두 가지가 있습니다.

  * Azure Portal의 오른쪽 위에서 Cloud Shell 단추를 선택합니다.

       ![Azure Portal의 Cloud Shell 단추](media/tutorial-postgresql-to-azure-postgresql-online/cloud-shell-button.png)

  * CLI를 로컬로 설치하고 실행합니다. CLI 2.0은 Azure 리소스를 관리하기 위한 명령줄 도구입니다.

       CLI를 다운로드하려면 [Azure CLI 2.0 설치](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) 문서에 제시된 지침을 참조하세요. 이 문서는 또한 CLI 2.0을 지원하는 플랫폼을 나열합니다.

       Linux용 Windows 하위 시스템(WSL)을 설정하려면 [Windows 10 설치 가이드](https://docs.microsoft.com/windows/wsl/install-win10)의 지침을 따릅니다.

* postgresql.config 파일의 논리적 복제를 활성화하고 다음 매개 변수를 설정합니다.

  * wal_level = **logical**
  * max_replication_slots = [number of slots], **5개 슬롯**으로 설정하는 것이 좋습니다.
  * max_wal_senders =[동시 작업 수] - max_wal_senders 매개 변수는 실행할 수 있는 동시 작업 수를 설정합니다. **10작업**으로 설정하는 것이 좋습니다.

## <a name="migrate-the-sample-schema"></a>샘플 스키마 마이그레이션

테이블 스키마, 인덱스 및 저장 프로시저와 같은 모든 데이터베이스 개체를 완료하려면 원본 데이터베이스에서 스키마를 추출하고 데이터베이스에 적용해야 합니다.

1. pg_dump -s 명령을 사용하여 데이터베이스에 대한 스키마 덤프 파일을 만듭니다. 

    ```
    pg_dump -o -h hostname -U db_username -d db_name -s > your_schema.sql
    ```

    예를 들어 스키마 파일 dvdrental 데이터베이스를 덤프하려면:
    ```
    pg_dump -o -h localhost -U postgres -d dvdrental -s  > dvdrentalSchema.sql
    ```

    pg_dump 유틸리티를 사용하는 방법에 대한 자세한 내용은 [pg-dump](https://www.postgresql.org/docs/9.6/static/app-pgdump.html#PG-DUMP-EXAMPLES) 자습서의 예제를 참조하세요.

2. Azure Database for PostgreSQL인 대상 환경에서 빈 데이터베이스를 만듭니다.

    연결하고 데이터베이스를 만드는 방법에 대한 자세한 내용은 [Azure Portal에서 Azure Database for PostgreSQL 서버 만들기](https://docs.microsoft.com/azure/postgresql/quickstart-create-server-database-portal) 문서를 참조하세요.

3. 스키마 덤프 파일을 복원하여 만든 대상 데이터베이스에 스키마를 가져옵니다.

    ```
    psql -h hostname -U db_username -d db_name < your_schema.sql 
    ```

    예:

    ```
    psql -h mypgserver-20170401.postgres.database.azure.com  -U postgres -d dvdrental < dvdrentalSchema.sql
    ```

4. 스키마에 외래 키가 있으면 마이그레이션의 초기 로드 및 지속적인 동기화가 실패합니다. PgAdmin 또는 psql에서 다음 스크립트를 실행하여 드롭 외래 키 스크립트를 추출하고 대상(Azure Database for PostgreSQL)에서 외래 키 스크립트를 추가합니다.
  
    ```
    SELECT Queries.tablename
           ,concat('alter table ', Queries.tablename, ' ', STRING_AGG(concat('DROP CONSTRAINT ', Queries.foreignkey), ',')) as DropQuery
                ,concat('alter table ', Queries.tablename, ' ',
                                                STRING_AGG(concat('ADD CONSTRAINT ', Queries.foreignkey, ' FOREIGN KEY (', column_name, ')', 'REFERENCES ', foreign_table_name, '(', foreign_column_name, ')' ), ',')) as AddQuery
        FROM
        (SELECT
        tc.table_schema,
        tc.constraint_name as foreignkey,
        tc.table_name as tableName,
        kcu.column_name,
        ccu.table_schema AS foreign_table_schema,
        ccu.table_name AS foreign_table_name,
        ccu.column_name AS foreign_column_name
    FROM
        information_schema.table_constraints AS tc
        JOIN information_schema.key_column_usage AS kcu
          ON tc.constraint_name = kcu.constraint_name
          AND tc.table_schema = kcu.table_schema
        JOIN information_schema.constraint_column_usage AS ccu
          ON ccu.constraint_name = tc.constraint_name
          AND ccu.table_schema = tc.table_schema
    WHERE constraint_type = 'FOREIGN KEY') Queries
      GROUP BY Queries.tablename;
    ```

    쿼리 결과에서 외래 키 삭제(두 번째 열)를 실행합니다.

5. 데이터의 트리거(트리거 삽입 또는 업데이트)는 원본의 복제된 데이터보다 먼저 대상에 데이터 무결성을 적용합니다. 마이그레이션 중에 **대상**의 모든 테이블에서 트리거를 사용하지 않도록 설정한 다음, 마이그레이션이 완료되면 트리거를 사용하도록 설정하는 것이 좋습니다.

    대상 데이터베이스에서 트리거를 사용하지 않도록 설정하려면 다음 명령을 사용합니다.

    ```
    select concat ('alter table ', event_object_table, ' disable trigger ', trigger_name)
    from information_schema.triggers;
    ```

6. 테이블에 ENUM 데이터 형식이 있으면 대상 테이블에서 'character varying' 데이터 형식으로 일시적으로 업데이트하는 것이 좋습니다. 데이터 복제가 완료되면 데이터 형식을 ENUM으로 되돌립니다.

## <a name="provisioning-an-instance-of-dms-using-the-cli"></a>CLI를 사용하여 DMS의 인스턴스 프로비전

1. dms 동기화 확장을 설치합니다.
   * 다음 명령을 실행하여 Azure에 로그인합니다.
       ```
       az login
       ```

   * 메시지가 표시되면 웹 브라우저를 열고 디바이스 인증을 위해 코드를 입력합니다. 나열된 지침을 따릅니다.
   * dms 확장을 추가합니다.
       * 사용 가능한 확장을 나열하려면 다음 명령을 실행합니다.

           ```
           az extension list-available –otable
           ```

       * 확장을 설치하려면 다음 명령을 실행합니다.

           ```
           az extension add –n dms-preview
           ```

   * dms 확장이 올바르게 설치되었는지 확인하려면 다음 명령을 실행합니다.

       ```
       az extension list -otable
       ```
       다음 출력이 표시됩니다.

       ```
       ExtensionType    Name
       ---------------  ------
       whl              dms
       ```

   * 언제든지 다음을 실행하여 DMS에서 지원되는 모든 명령을 봅니다.

       ```
       az dms -h
       ```

   * 여러 Azure 구독이 있는 경우 다음 명령을 실행하여 DMS 서비스의 인스턴스를 프로비전하는 데 사용하려는 구독을 설정합니다.

        ```
       az account set -s 97181df2-909d-420b-ab93-1bff15acb6b7
        ```

2. 다음 명령을 실행하여 DMS의 인스턴스를 프로비전합니다.

   ```
   az dms create -l [location] -n <newServiceName> -g <yourResourceGroupName> --sku-name Premium_4vCores --subnet/subscriptions/{vnet subscription id}/resourceGroups/{vnet resource group}/providers/Microsoft.Network/virtualNetworks/{vnet name}/subnets/{subnet name} –tags tagName1=tagValue1 tagWithNoValue
   ```

   예를 들어 다음 명령은 서비스를 만듭니다.

   * 위치: 미국 동부 2
   * 구독: 97181df2-909d-420b-ab93-1bff15acb6b7
   * 리소스 그룹 이름: PostgresDemo
   * DMS 서비스 이름: PostgresCLI

   ```
   az dms create -l eastus2 -g PostgresDemo -n PostgresCLI --subnet /subscriptions/97181df2-909d-420b-ab93-1bff15acb6b7/resourceGroups/ERNetwork/providers/Microsoft.Network/virtualNetworks/AzureDMS-CORP-USC-VNET-5044/subnets/Subnet-1 --sku-name Premium_4vCores
   ```

   DMS 서비스의 인스턴스를 만드는 데 약 10~12분이 걸립니다.

3. Postgres pg_hba.conf 파일에 추가할 수 있도록 DMS 에이전트의 IP 주소를 식별하려면 다음 명령을 실행합니다.

    ```
    az network nic list -g <ResourceGroupName>--query '[].ipConfigurations | [].privateIpAddress'
    ```

    예:

    ```
    az network nic list -g PostgresDemo --query '[].ipConfigurations | [].privateIpAddress'
    ```

    다음 주소와 유사한 결과가 표시되어야 합니다. 

    ```
    [
      "172.16.136.18"
    ]
    ```

4. DMS 에이전트의 IP 주소를 Postgres pg_hba.conf 파일에 추가합니다.

    * DMS에서 프로비전을 완료한 후 DMS IP 주소를 기록해 둡니다.
    * 다음 항목과 유사하게 원본의 pg_hba.conf 파일에 IP 주소를 추가합니다.

        ```
        host    all     all     172.16.136.18/10    md5
        host    replication     postgres    172.16.136.18/10    md5
        ```

5. 다음으로 다음 명령을 실행하여 PostgreSQL 마이그레이션 프로젝트를 만듭니다.
    
    ```
    az dms project create -l <location> -g <ResourceGroupName> --service-name <yourServiceName> --source-platform PostgreSQL --target-platform AzureDbforPostgreSQL -n <newProjectName>
    ```

    예를 들어 다음 명령은 이러한 매개 변수를 사용하여 프로젝트를 만듭니다.

   * 위치: 미국 중서부
   * 리소스 그룹 이름: PostgresDemo
   * 서비스 이름: PostgresCLI
   * 프로젝트 이름: PGMigration
   * 원본 플랫폼: PostgreSQL
   * 대상 플랫폼: AzureDbForPostgreSql

     ```
     az dms project create -l westcentralus -n PGMigration -g PostgresDemo --service-name PostgresCLI --source-platform PostgreSQL --target-platform AzureDbForPostgreSql
     ```

6. 다음 단계를 사용하여 PostgreSQL 마이그레이션 작업을 만듭니다.

    이 단계는 원본 IP, UserID 및 암호, 대상 IP, UserID, 암호 사용 및 연결을 설정하는 작업 유형을 포함합니다.

   * 옵션의 전체 목록을 보려면 명령을 실행합니다.

       ```
       az dms project task create -h
       ```

       원본 및 대상 연결의 경우 입력 매개 변수는 개체 목록이 있는 json 파일을 참조합니다.

       PostgreSQL 연결에 대한 연결 JSON 개체의 형식입니다.
        
       ```
       {
                   "userName": "user name",    // if this is missing or null, you will be prompted
                   "password": null,           // if this is missing or null (highly recommended) you will
               be prompted
                   "serverName": "server name",
                   "databaseName": "database name", // if this is missing, it will default to the 'postgres'
               server
                   "port": 5432                // if this is missing, it will default to 5432
               }
       ```

   * json 개체를 나열하는 데이터베이스 옵션 json 파일도 있습니다. PostgreSQL의 경우 데이터베이스 옵션 JSON 개체의 형식은 다음과 같습니다.

       ```
       [
           {
               "name": "source database",
               "target_database_name": "target database",
           },
           ...n
       ]
       ```

   * 메모장을 사용하여 json 파일을 만들고, 다음 명령을 복사하고 파일에 붙여넣은 다음, C:\DMS\source.json에서 파일을 저장합니다.

        ```
       {
                   "userName": "postgres",    
                   "password": null,           
               be prompted
                   "serverName": "13.51.14.222",
                   "databaseName": "dvdrental", 
                   "port": 5432                
               }
        ```

   * target.json이라는 다른 파일을 만들고 C:\DMS\target.json으로 저장합니다. 다음 명령을 포함합니다.

       ```
       {
               "userName": " dms@builddemotarget",    
               "password": null,           
               "serverName": " builddemotarget.postgres.database.azure.com",
               "databaseName": "inventory", 
               "port": 5432                
           }
       ```

   * 마이그레이션할 데이터베이스로 인벤토리를 나열하는 데이터베이스 옵션 json 파일을 만듭니다.

       ``` 
       [
           {
               "name": "dvdrental",
               "target_database_name": "dvdrental",
           }
       ]
       ```

   * 원본, 대상 및 DB 옵션 json 파일을 사용하는 다음 명령을 실행합니다.

       ``` 
       az dms project task create -g PostgresDemo --project-name PGMigration --source-platform postgresql --target-platform azuredbforpostgresql --source-connection-json c:\DMS\source.json --database-options-json C:\DMS\option.json --service-name PostgresCLI --target-connection-json c:\DMS\target.json –task-type OnlineMigration -n runnowtask    
       ```

     이 시점에서 마이그레이션 작업을 성공적으로 전송했습니다.

7. 작업의 진행률을 표시하려면 다음 명령을 실행합니다.

   ```
   az dms project task show --service-name PostgresCLI --project-name PGMigration --resource-group PostgresDemo --name Runnowtask
   ```

   또는

    ```
   az dms project task show --service-name PostgresCLI --project-name PGMigration --resource-group PostgresDemo --name Runnowtask --expand output
    ```

8. 확장 출력에서 migrationState에 대해 쿼리할 수도 있습니다.

    ```
    az dms project task show --service-name PostgresCLI --project-name PGMigration --resource-group PostgresDemo --name Runnowtask --expand output --query 'properties.output[].migrationState | [0]' "READY_TO_COMPLETE"
    ```

## <a name="understanding-migration-task-status"></a>마이그레이션 작업 상태 이해

출력 파일에서 마이그레이션의 진행률을 나타내는 몇 가지 매개 변수가 있습니다. 예를 들어 아래의 출력 파일을 참조하세요.

    ```
    "output": [                                 Database Level
          {
            "appliedChanges": 0,        //Total incremental sync applied after full load
            "cdcDeleteCounter": 0       // Total delete operation  applied after full load
            "cdcInsertCounter": 0,      // Total insert operation applied after full load
            "cdcUpdateCounter": 0,      // Total update operation applied after full load
            "databaseName": "inventory",
            "endedOn": null,
            "fullLoadCompletedTables": 2,   //Number of tables completed full load
            "fullLoadErroredTables": 0, //Number of tables that contain migration error
            "fullLoadLoadingTables": 0, //Number of tables that are in loading status
            "fullLoadQueuedTables": 0,  //Number of tables that are in queued status
            "id": "db|inventory",
            "incomingChanges": 0,       //Number of changes after full load
            "initializationCompleted": true,
            "latency": 0,
            "migrationState": "READY_TO_COMPLETE",  //Status of migration task. READY_TO_COMPLETE means the database is ready for cutover
            "resultType": "DatabaseLevelOutput",
            "startedOn": "2018-07-05T23:36:02.27839+00:00"
          },
          {
            "databaseCount": 1,
            "endedOn": null,
            "id": "dd27aa3a-ed71-4bff-ab34-77db4261101c",
            "resultType": "MigrationLevelOutput",
            "sourceServer": "138.91.123.10",
            "sourceVersion": "PostgreSQL",
            "startedOn": "2018-07-05T23:36:02.27839+00:00",
            "state": "PENDING",
            "targetServer": "builddemotarget.postgres.database.azure.com",
            "targetVersion": "Azure Database for PostgreSQL"
          },
          {                                     Table 1
            "cdcDeleteCounter": 0,
            "cdcInsertCounter": 0,
            "cdcUpdateCounter": 0,
            "dataErrorsCount": 0,
            "databaseName": "inventory",
            "fullLoadEndedOn": "2018-07-05T23:36:20.740701+00:00",  //Full load completed time
            "fullLoadEstFinishTime": "1970-01-01T00:00:00+00:00",
            "fullLoadStartedOn": "2018-07-05T23:36:15.864552+00:00",    //Full load started time
            "fullLoadTotalRows": 10,                    //Number of rows loaded in full load
            "fullLoadTotalVolumeBytes": 7056,               //Volume in Bytes in full load
            "id": "or|inventory|public|actor",          
            "lastModifiedTime": "2018-07-05T23:36:16.880174+00:00",
            "resultType": "TableLevelOutput",
            "state": "COMPLETED",                   //State of migration for this table
            "tableName": "public.catalog",              //Table name
            "totalChangesApplied": 0                //Total sync changes that applied after full load
          },
          {                                     Table 2
            "cdcDeleteCounter": 0,
            "cdcInsertCounter": 50,
            "cdcUpdateCounter": 0,
            "dataErrorsCount": 0,
            "databaseName": "inventory",
            "fullLoadEndedOn": "2018-07-05T23:36:23.963138+00:00",
            "fullLoadEstFinishTime": "1970-01-01T00:00:00+00:00",
            "fullLoadStartedOn": "2018-07-05T23:36:19.302013+00:00",
            "fullLoadTotalRows": 112,
            "fullLoadTotalVolumeBytes": 46592,
            "id": "or|inventory|public|address",
            "lastModifiedTime": "2018-07-05T23:36:20.308646+00:00",
            "resultType": "TableLevelOutput",
            "state": "COMPLETED",
            "tableName": "public.orders",
            "totalChangesApplied": 0
          }
        ],                          DMS migration task state
        "state": "Running", //Migration task state – Running means it is still listening to any changes that might come in                  
        "taskType": null
      },
      "resourceGroup": "PostgresDemo",
      "type": "Microsoft.DataMigration/services/projects/tasks"
    ```

## <a name="cutover-migration-task"></a>중단 마이그레이션 작업

전체 로드가 완료되면 데이터베이스는 중단할 준비가 됩니다. 원본 서버에 새 트랜잭션이 들어오는 빈도에 따라 DMS 작업은 전체 로드가 완료된 후 여전히 변경 내용을 적용할 수 있습니다.

모든 데이터가 수집되었는지 확인하려면 원본 및 대상 데이터베이스 간의 행 개수를 확인합니다. 예를 들어 다음 명령을 사용할 수 있습니다.

```
"migrationState": "READY_TO_COMPLETE", //Status of migration task. READY_TO_COMPLETE means database is ready for cutover
 "incomingChanges": 0,  //continue to check for a period of 5-10 minutes to make sure no new incoming changes that need to be applied to the target server
   "fullLoadTotalRows": 10, //full load for table 1
    "cdcDeleteCounter": 0,  //delete, insert and update counter on incremental sync after full load
    "cdcInsertCounter": 50,
    "cdcUpdateCounter": 0,
     "fullLoadTotalRows": 112,  //full load for table 2
```

1. 다음 명령을 사용하여 중단 데이터베이스 마이그레이션 작업을 수행합니다.

    ```
    az dms project task cutover -h
    ```

    예:

    ```
    az dms project task cutover --service-name PostgresCLI --project-name PGMigration --resource-group PostgresDemo --name Runnowtask  --object-name Inventory
    ```

2. 중단 진행률을 모니터링하려면 다음 명령을 실행합니다.

    ```
    az dms project task show --service-name PostgresCLI --project-name PGMigration --resource-group PostgresDemo --name Runnowtask
    ```

## <a name="service-project-task-cleanup"></a>서비스, 프로젝트, 작업 정리

모든 DMS 작업, 프로젝트 또는 서비스를 취소 또는 삭제해야 할 경우 다음 순서로 취소를 수행합니다.

* 실행 중인 작업 취소
* 작업 삭제
* 프로젝트 삭제
* DMS 서비스 삭제

1. 실행 중인 작업을 취소하려면 다음 명령을 사용합니다.

    ```
    az dms project task cancel --service-name PostgresCLI --project-name PGMigration --resource-group PostgresDemo --name Runnowtask
     ```

2. 실행 중인 작업을 삭제하려면 다음 명령을 사용합니다.
    ```
    az dms project task delete --service-name PostgresCLI --project-name PGMigration --resource-group PostgresDemo --name Runnowtask
    ```

3. 실행 중인 프로젝트를 취소하려면 다음 명령을 사용합니다.
     ```
    az dms project task cancel -n runnowtask --project-name PGMigration -g PostgresDemo --service-name PostgresCLI
     ```

4. 실행 중인 프로젝트를 삭제하려면 다음 명령을 사용합니다.
    ```
    az dms project task delete -n runnowtask --project-name PGMigration -g PostgresDemo --service-name PostgresCLI
    ```

5. DMS 서비스를 삭제하려면 다음 명령을 사용합니다.

     ```
    az dms delete -g ProgresDemo -n PostgresCLI
     ```

## <a name="next-steps"></a>다음 단계

* Azure Database for PostgreSQL로 온라인 마이그레이션을 수행하는 경우와 관련하여 알려진 문제 및 제한 사항에 대한 자세한 내용은 [Azure Database for PostgreSQL 온라인 마이그레이션의 알려진 문제 및 해결 방법](known-issues-azure-postgresql-online.md) 문서를 참조하세요.
* Azure Database Migration Service에 대한 자세한 내용은 [Azure Database Migration Service란?](https://docs.microsoft.com/azure/dms/dms-overview) 문서를 참조하세요.
* Azure Database for PostgreSQL에 대한 자세한 내용은 [Azure Database for PostgreSQL이란?](https://docs.microsoft.com/azure/postgresql/overview) 문서를 참조하세요.
