---
title: '자습서: Azure Database for PostgreSQL로 RDS PostgreSQL online 마이그레이션'
titleSuffix: Azure Database Migration Service
description: Azure Database Migration Service를 사용하여 RDS PostgreSQL에서 Azure Database for PostgreSQL로 온라인 마이그레이션하는 방법을 알아봅니다.
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
ms.openlocfilehash: 0930afeb02c79c9b3cf1da791e8cc5cda83c2820
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/08/2020
ms.locfileid: "75751274"
---
# <a name="tutorial-migrate-rds-postgresql-to-azure-database-for-postgresql-online-using-dms"></a>자습서: DMS를 사용 하 여 Azure Database for PostgreSQL online으로 RDS PostgreSQL 마이그레이션

Azure Database Migration Service를 사용하면 마이그레이션 중에 원본 데이터베이스를 온라인 상태로 유지하면서 RDS PostgreSQL 인스턴스에서 [Azure Database for PostgreSQL](https://docs.microsoft.com/azure/postgresql/)로 데이터베이스를 마이그레이션할 수 있습니다. 즉, 애플리케이션의 가동 중지 시간을 최소화하면서 마이그레이션을 수행할 수 있습니다. 이 자습서에서는 Azure Database Migration Service에서 온라인 마이그레이션 작업을 사용하여 **DVD Rental** 샘플 데이터베이스를 RDS PostgreSQL 9.6 인스턴스에서 Azure Database for PostgreSQL로 마이그레이션합니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.
> [!div class="checklist"]
>
> * pg_dump 유틸리티를 사용하여 샘플 스키마를 마이그레이션합니다.
> * Azure Database Migration Service 인스턴스를 만듭니다.
> * Azure Database Migration Service를 사용하여 마이그레이션 프로젝트를 만듭니다.
> * 마이그레이션을 실행합니다.
> * 마이그레이션을 모니터링합니다.

> [!NOTE]
> Azure Database Migration Service를 사용하여 온라인 마이그레이션을 수행하려면 프리미엄 가격 책정 계층에 따라 인스턴스를 만들어야 합니다. 자세한 내용은 Azure Database Migration Service [가격 책정](https://azure.microsoft.com/pricing/details/database-migration/) 페이지를 참조하세요.

> [!IMPORTANT]
> 최적의 마이그레이션 환경을 위해 대상 데이터베이스와 동일한 Azure 지역에서 Azure Database Migration Service의 인스턴스를 만드는 것이 좋습니다. 영역 또는 지역 간에 데이터를 이동하면 마이그레이션 프로세스 속도가 저하되고 오류가 발생할 수 있습니다.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

이 문서에서는 PostgreSQL의 온-프레미스 인스턴스에서 Azure Database for PostgreSQL로 온라인 마이그레이션을 수행하는 방법을 설명합니다.

## <a name="prerequisites"></a>필수 조건

이 자습서를 완료하려면 다음이 필요합니다.

* [PostgreSQL 커뮤니티 버전](https://www.postgresql.org/download/) 9.5, 9.6 또는 10을 다운로드하여 설치합니다. 원본 PostgreSQL 서버 버전은 9.5.11, 9.6.7, 10 이상이어야 합니다. 자세한 내용은 [지원되는 PostgreSQL 데이터베이스 버전](https://docs.microsoft.com/azure/postgresql/concepts-supported-versions) 문서를 참조하세요.

    또한 RDS PostgreSQL 버전은 Azure Database for PostgreSQL 버전과 일치해야 합니다. 예를 들어 RDS PostgreSQL 9.5.11.5는 Azure Database for PostgreSQL 9.5.11로만 마이그레이션할 수 있고, 버전 9.6.7로는 업그레이드할 수 없습니다.

* [Azure Database for PostgreSQL](https://docs.microsoft.com/azure/postgresql/quickstart-create-server-database-portal)의 인스턴스를 만듭니다. pgAdmin을 사용하여 PostgreSQL 서버에 연결하는 방법에 대한 자세한 내용은 문서의 이 [섹션](https://docs.microsoft.com/azure/postgresql/quickstart-create-server-database-portal#connect-to-the-postgresql-server-using-pgadmin)을 참조하세요.
* [Express](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) 경로 또는 [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways)을 사용 하 여 온-프레미스 원본 서버에 대한 사이트 간 연결을 제공 하는 Azure Resource Manager 배포 모델을 사용 하 여 Azure Database Migration Service에 대한 Microsoft Azure Virtual Network를 만듭니다. 가상 네트워크를 만드는 방법에 대한 자세한 내용은 [Virtual Network 설명서](https://docs.microsoft.com/azure/virtual-network/)와 특히 단계별 정보를 포함 하는 빠른 시작 문서를 참조 하세요.
* 가상 네트워크 네트워크 보안 그룹 규칙에서 Azure Database Migration Service에 대한 인바운드 통신 포트 (443, 53, 9354, 445 및 12000)를 차단 하지 않는지 확인 합니다. Virtual network NSG 트래픽 필터링에 대한 자세한 내용은 [네트워크 보안 그룹을 사용 하 여 네트워크 트래픽 필터링](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg)문서를 참조 하세요.
* [데이터베이스 엔진 액세스를 위한 Windows 방화벽](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access)을 구성합니다.
* Windows 방화벽을 열고 Azure Database Migration Service가 기본적으로 5432 TCP 포트인 원본 PostgreSQL 서버에 액세스할 수 있도록 허용합니다.
* 원본 데이터베이스 앞에 방화벽 어플라이언스를 사용하는 경우, Azure Database Migration Service가 마이그레이션을 위해 원본 데이터베이스에 액세스할 수 있게 허용하는 방화벽 규칙을 추가해야 합니다.
* Azure Database Migration Service가 대상 데이터베이스에 액세스할 수 있도록 Azure Database for PostgreSQL 서버에 대한 서버 수준 [방화벽 규칙](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure)을 만듭니다. Azure Database Migration Service에 사용 되는 가상 네트워크의 서브넷 범위를 제공 합니다.

### <a name="set-up-aws-rds-postgresql-for-replication"></a>AWS RDS PostgreSQL에 복제 설정

1. 새 매개 변수 그룹을 만들려면 AWS에서 제공하는 [DB 매개 변수 그룹 사용](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_WorkingWithParamGroups.html) 문서의 지침을 따릅니다.
2. 마스터 사용자 이름을 사용하여 Azure Database Migration Service에서 원본에 연결합니다. 마스터 사용자 계정이 아닌 다른 계정을 사용하는 경우 계정에 rds_superuser 역할 및 rds_replication 역할이 있어야 합니다. rds_replication 역할은 논리적 슬롯을 관리하고 논리적 슬롯을 사용하여 데이터를 스트리밍하는 권한을 부여합니다.
3. 다음 구성을 사용하여 새 매개 변수 그룹을 만듭니다. a. DB 매개 변수 그룹에서 rds.logical_replication 매개 변수를 1로 설정합니다.
    b. max_wal_senders = [동시 작업 수]: max_wal_senders 매개 변수는 실행할 수 있는 동시 작업 수를 설정합니다. 10으로 설정하는 것이 좋습니다.
    다. max_replication_slots – = [number of slots], 5개 슬롯으로 설정하는 것이 좋습니다.
4. 만든 매개 변수 그룹을 RDS PostgreSQL 인스턴스에 연결합니다.

## <a name="migrate-the-schema"></a>스키마 마이그레이션

1. 테이블 스키마, 인덱스, 저장 프로시저 같은 모든 데이터베이스 개체의 마이그레이션을 완료하려면 원본 데이터베이스에서 스키마를 추출하여 대상 데이터베이스에 적용해야 합니다.

    스키마만 마이그레이션하는 가장 쉬운 방법은 -s 옵션에 pg_dump를 사용하는 것입니다. 자세한 내용은 Postgres pg_dump 자습서의 [예제](https://www.postgresql.org/docs/9.6/app-pgdump.html#PG-DUMP-EXAMPLES)를 참조하세요.

    ```
    pg_dump -o -h hostname -U db_username -d db_name -s > your_schema.sql
    ```

    예를 들어 **dvdrental** 데이터베이스의 스키마 파일을 덤프하려면 다음 명령을 사용합니다.

    ```
    pg_dump -o -h localhost -U postgres -d dvdrental -s  > dvdrentalSchema.sql
    ```

2. 대상 서비스, 즉, Azure Database for PostgreSQL에 빈 데이터베이스를 만듭니다. 데이터베이스를 만들어서 연결하려면 다음 문서 중 하나를 참조하세요.

    * [Azure Portal을 사용하여 Azure Database for PostgreSQL 서버 만들기](https://docs.microsoft.com/azure/postgresql/quickstart-create-server-database-portal)
    * [Azure CLI를 사용하여 PostgreSQL용 Azure Database 만들기](https://docs.microsoft.com/azure/postgresql/quickstart-create-server-database-azure-cli)

3. 대상 서비스, 즉, Azure Database for PostgreSQL로 스키마를 가져옵니다. 스키마 덤프 파일을 복원하려면 다음 명령을 실행합니다.

    ```
    psql -h hostname -U db_username -d db_name < your_schema.sql
    ```

    예:

    ```
    psql -h mypgserver-20170401.postgres.database.azure.com  -U postgres -d dvdrental < dvdrentalSchema.sql
    ```

4. 스키마에 외래 키가 있으면 마이그레이션의 초기 로드 및 지속적인 동기화가 실패합니다. 드롭 외래 키 스크립트를 추출하고 대상(Azure Database for PostgreSQL)에서 외래 키 스크립트를 추가하려면 PgAdmin 또는 psql에서 다음 스크립트를 실행합니다.
  
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

5. 외래 키를 삭제하려면 쿼리 결과에서 drop foreign key(두 번째 열)를 실행합니다.

6. 데이터에 트리거(트리거 삽입 또는 업데이트)가 있으면 원본의 데이터를 복제하기 전에 대상에 데이터 무결성이 적용됩니다. 마이그레이션 중에 *대상의* 모든 테이블에서 트리거를 사용하지 않도록 설정한 다음, 마이그레이션이 완료되면 트리거를 사용하도록 설정하는 것이 좋습니다.

    대상 데이터베이스에서 트리거를 해제하려면 다음을 수행합니다.

    ```
    SELECT Concat('DROP TRIGGER ', Trigger_Name, ';') FROM  information_schema.TRIGGERS WHERE TRIGGER_SCHEMA = 'your_schema';
    ```

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Microsoft.DataMigration 리소스 공급자 등록

1. Azure Portal에 로그인하고, **모든 서비스**를 선택한 다음, **구독**을 선택합니다.

   ![포털 구독 표시](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/portal-select-subscription1.png)

2. Azure Database Migration Service의 인스턴스를 만들 구독을 선택한 다음, **리소스 공급자**를 선택합니다.

    ![리소스 공급자 보기](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/portal-select-resource-provider.png)

3. 마이그레이션을 검색한 다음 **Microsoft.DataMigration**의 오른쪽에서 **등록**을 선택합니다.

    ![리소스 공급자 등록](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/portal-register-resource-provider.png)

## <a name="create-an-instance-of-azure-database-migration-service"></a>Azure Database Migration Service 인스턴스 만들기

1. Azure Portal에서 **+ 리소스 만들기**를 선택하고, Azure Database Migration Service를 검색한 다음, 드롭다운 목록에서 **Azure Database Migration Service**를 선택합니다.

    ![Azure Marketplace](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/portal-marketplace.png)

2. **Azure Database Migration Service** 화면에서 **만들기**를 선택합니다.

    ![Azure Database Migration Service 인스턴스 만들기](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-create1.png)
  
3. **Migration Service 만들기** 화면에서 서비스, 구독, 신규 또는 기존 리소스 그룹의 이름을 지정합니다.

4. Azure Database Migration Service 인스턴스를 만들 위치를 선택합니다.

5. 기존 가상 네트워크를 선택 하거나 새 가상 네트워크를 만드세요.

    가상 네트워크는 원본 PostgreSQL 인스턴스 및 대상 Azure Database for PostgreSQL 인스턴스에 대한 액세스 권한을 Azure Database Migration Service 제공 합니다.

    Azure Portal에서 가상 네트워크를 만드는 방법에 대한 자세한 내용은 [Azure Portal를 사용 하 여 가상 네트워크 만들기](https://aka.ms/DMSVnet)문서를 참조 하세요.

6. 가격 책정 계층을 선택 합니다. 이 온라인 마이그레이션의 경우 프리미엄: 4vCores 가격 책정 계층을 선택 해야 합니다.

    ![Azure Database Migration Service 인스턴스 설정 구성](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-settings4.png)

7. **만들기**를 선택하여 서비스를 만듭니다.

## <a name="create-a-migration-project"></a>마이그레이션 프로젝트 만들기

서비스가 생성된 후 Azure Portal에서 서비스를 찾아 연 다음, 새로운 마이그레이션 프로젝트를 만듭니다.

1. Azure Portal에서 **모든 서비스**를 선택하고, Azure Database Migration Service를 검색하고 나서, **Azure Database Migration Services**를 선택합니다.

      ![Azure Database Migration Service의 모든 인스턴스 찾기](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-search.png)

2. **Azure Database Migration Services** 화면에서 방금 만든 Azure Database Migration Service 인스턴스의 이름을 검색하고 인스턴스를 선택합니다.

     ![Azure Database Migration Service 인스턴스 찾기](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-instance-search.png)

3. **+ 새 마이그레이션 프로젝트**를 선택합니다.
4. **새 마이그레이션 프로젝트** 화면에서 프로젝트 이름을 지정하고, **원본 서버 유형** 텍스트 상자에서 **AWS RDS for PostgreSQL**을 선택한 다음, **대상 서버 유형** 텍스트 상자에서 **Azure Database for PostgreSQL**을 선택합니다.
5. **활동 유형 선택** 섹션에서 **온라인 데이터 마이그레이션**을 선택합니다.

    > [!IMPORTANT]
    > **온라인 데이터 마이그레이션**을 선택합니다. 이 시나리오에서는 오프라인 마이그레이션이 지원되지 않습니다.

    ![Database Migration Service 프로젝트 만들기](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-create-project5.png)

    > [!NOTE]
    > 또는 **프로젝트만 만들기**를 선택하여 지금 마이그레이션 프로젝트를 만들고, 나중에 마이그레이션을 실행할 수도 있습니다.

6. **저장**을 선택합니다.

7. **작업 만들기 및 실행**을 선택하여 프로젝트를 만들고 마이그레이션 작업을 실행합니다.

    > [!NOTE]
    > 프로젝트 만들기 블레이드에서 온라인 마이그레이션을 설정하는 데 필요한 필수 조건을 기록해 둡니다.

## <a name="specify-source-details"></a>원본 세부 정보 지정

* **마이그레이션 원본 세부 정보** 화면에서 원본 PostgreSQL 인스턴스의 연결 세부 정보를 지정합니다.

   ![원본 세부 정보](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-source-details4.png)

## <a name="specify-target-details"></a>대상 세부 정보 지정

1. **저장**을 선택한 다음, **대상 세부 정보** 화면에서 대상 Azure Database for PostgreSQL 서버의 연결 세부 정보를 지정합니다. 이 서버는 pg_dump를 사용하여 미리 프로비저닝되었고 **DVD Rentals** 스키마가 배포되어 있습니다.

    ![대상 선택](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-select-target4.png)

2. **저장**을 선택한 다음, **대상 데이터베이스에 매핑** 화면에서 마이그레이션하기 위해 원본 및 대상 데이터베이스를 매핑합니다.

    대상 데이터베이스의 이름이 원본 데이터베이스와 동일하면 Azure Database Migration Service는 기본적으로 이 대상 데이터베이스를 선택합니다.

    ![대상 데이터베이스에 매핑](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-map-targets-activity5.png)

3. **저장**을 선택하고, **마이그레이션 요약** 화면의 **작업 이름** 텍스트 상자에서 마이그레이션 작업의 이름을 지정한 다음, 요약을 검토하여 원본 및 대상 세부 정보가 이전에 지정한 내용과 일치하는지 확인합니다.

    ![마이그레이션 요약](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-migration-summary1.png)

## <a name="run-the-migration"></a>마이그레이션 실행

* **마이그레이션 실행**을 선택합니다.

    마이그레이션 작업 창이 나타나고, 작업 **상태**는 **초기화 중**입니다.

## <a name="monitor-the-migration"></a>마이그레이션 모니터링

1. 마이그레이션 작업 화면에서 **새로 고침**을 선택하여 마이그레이션 **상태**가 **실행 중**으로 표시될 때까지 디스플레이를 업데이트합니다.

    ![작업 상태 - 실행 중](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-activity-status3.png)

2. **데이터베이스 이름** 아래에서 특정 데이터베이스를 선택하여 **전체 데이터 로드** 및 **증분 데이터 동기화** 작업의 마이그레이션 상태로 이동합니다.

    **전체 데이터 로드**는 초기 로드 마이그레이션 상태를 보여주고, **증분 데이터 동기화**는 CDC(변경 데이터 캡처) 상태를 보여줍니다.

    ![인벤토리 화면 - 전체 데이터 로드](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-inventory-full-load.png)

    ![인벤토리 화면 - 증분 데이터 동기화](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-inventory-incremental.png)

## <a name="perform-migration-cutover"></a>마이그레이션 중단 수행

초기 전체 로드가 완료되면 데이터베이스가 **중단 준비 완료**로 표시됩니다.

1. 데이터베이스 마이그레이션을 완료할 준비가 되면 **중단 시작**을 선택합니다.

    ![중단 시작](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-inventory-start-cutover.png)

2. 원본 데이터베이스로 들어오는 모든 트랜잭션을 중지해야 합니다. **보류 중인 변경 내용** 카운터가 **0**으로 표시될 때까지 기다립니다.
3. **확인**, **적용**을 차례로 선택합니다.
4. 데이터베이스 마이그레이션 상태가 **완료됨**으로 표시되면 애플리케이션을 새로운 대상 Azure Database for PostgreSQL 데이터베이스에 연결합니다.

PostgreSQL 온-프레미스 인스턴스를 Azure Database for PostgreSQL로 온라인 마이그레이션하는 작업이 완료되었습니다.

## <a name="next-steps"></a>다음 단계

* Azure Database Migration Service에 대한 자세한 내용은 [Azure Database Migration Service란?](https://docs.microsoft.com/azure/dms/dms-overview) 문서를 참조하세요.
* Azure Database for PostgreSQL에 대한 자세한 내용은 [Azure Database for PostgreSQL이란?](https://docs.microsoft.com/azure/postgresql/overview) 문서를 참조하세요.
* 기타 질문은 [Azure 데이터베이스 마이그레이션 관련 질문](mailto:AskAzureDatabaseMigrations@service.microsoft.com)이라는 별칭으로 이메일을 보내주세요.
