---
title: '자습서: Oracle online을 Azure Database for PostgreSQL로 마이그레이션'
titleSuffix: Azure Database Migration Service
description: Azure Database Migration Service를 사용하여 Oracle 온-프레미스 또는 가상 머신에서 Azure Database for PostgreSQL로의 온라인 마이그레이션을 수행하는 방법을 알아봅니다.
services: dms
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 01/24/2020
ms.openlocfilehash: 956523e2b51795a4bc97c653dab8b408b06061f4
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/26/2020
ms.locfileid: "76759912"
---
# <a name="tutorial-migrate-oracle-to-azure-database-for-postgresql-online-using-dms-preview"></a>자습서: DMS (미리 보기)를 사용 하 여 Azure Database for PostgreSQL 온라인으로 Oracle 마이그레이션

Azure Database Migration Service를 사용하여 가동 중지 시간을 최소화하면서 온-프레미스 또는 가상 머신에서 호스팅되는 Oracle 데이터베이스에서 [Azure Database for PostgreSQL](https://docs.microsoft.com/azure/postgresql/)로 데이터베이스를 마이그레이션할 수 있습니다. 즉 애플리케이션에 대한 가동 중지 시간을 최소화하면서 마이그레이션을 완료할 수 있습니다. 이 자습서에서는 Azure Database Migration Service의 온라인 마이그레이션 작업을 사용하여 **HR** 샘플 데이터베이스를 Oracle 11g의 온-프레미스 또는 가상 머신 인스턴스에서 Azure Database for PostgreSQL로 마이그레이션합니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.
> [!div class="checklist"]
>
> * ora2pg 도구를 사용하여 마이그레이션 작업을 평가합니다.
> * ora2pg 도구를 사용하여 샘플 스키마를 마이그레이션합니다.
> * Azure Database Migration Service 인스턴스를 만듭니다.
> * Azure Database Migration Service를 사용하여 마이그레이션 프로젝트를 만듭니다.
> * 마이그레이션을 실행합니다.
> * 마이그레이션을 모니터링합니다.

> [!NOTE]
> Azure Database Migration Service를 사용하여 온라인 마이그레이션을 수행하려면 프리미엄 가격 책정 계층에 따라 인스턴스를 만들어야 합니다.

> [!IMPORTANT]
> 최적의 마이그레이션 환경을 위해 Microsoft는 대상 데이터베이스와 동일한 Azure 지역에서 Azure Database Migration Service의 인스턴스를 만드는 것을 권장합니다. 영역 또는 지역 간에 데이터를 이동하면 마이그레이션 프로세스 속도가 저하되고 오류가 발생할 수 있습니다.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

이 문서에서는 Oracle에서 Azure Database for PostgreSQL로의 온라인 마이그레이션을 수행하는 방법에 대해 설명합니다.

## <a name="prerequisites"></a>필수 조건

이 자습서를 완료하려면 다음이 필요합니다.

* [Oracle 11g 릴리스 2(Standard Edition, Standard Edition One 또는 Enterprise Edition)](https://www.oracle.com/technetwork/database/enterprise-edition/downloads/index.html)를 다운로드하여 설치합니다.
* [여기](https://docs.oracle.com/database/121/COMSC/installation.htm#COMSC00002)에서 **HR** 샘플 데이터베이스를 다운로드합니다.
* [Windows 또는 Linux에서 ora2pg](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/Steps%20to%20Install%20ora2pg%20on%20Windows%20and%20Linux.pdf)를 다운로드 하 여 설치 합니다.
* [Azure Database for PostgreSQL에서 인스턴스를 만듭니다](https://docs.microsoft.com/azure/postgresql/quickstart-create-server-database-portal).
* 이 [문서](https://docs.microsoft.com/azure/postgresql/tutorial-design-database-using-azure-portal)의 지침을 사용하여 인스턴스에 연결하고 데이터베이스를 만듭니다.
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
* Windows 방화벽을 열어 Azure Database Migration Service에서 원본 Oracle 서버(기본적으로 1521 TCP 포트)에 액세스하도록 허용합니다.
* 원본 데이터베이스 앞에 방화벽 어플라이언스를 사용하는 경우 Azure Database Migration Service에서 원본 데이터베이스에 액세스하여 마이그레이션할 수 있도록 허용하는 방화벽 규칙을 추가해야 합니다.
* Azure Database Migration Service에서 대상 데이터베이스에 액세스할 수 있도록 Azure Database for PostgreSQL에 대한 서버 수준 [방화벽 규칙](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure)을 만듭니다. Azure Database Migration Service에 사용 되는 가상 네트워크의 서브넷 범위를 제공 합니다.
* 원본 Oracle 데이터베이스에 대한 액세스를 사용하도록 설정합니다.

  > [!NOTE]
  > 사용자가 Oracle 원본에 연결하려면 DBA 역할이 필요합니다.

  * 다시 실행 로그 보관은 데이터 변경 내용을 캡처하기 위해 Azure Database Migration Service에서 증분 동기화에 필요합니다. 다음 단계에 따라 Oracle 원본을 구성합니다.
    * 다음 명령을 실행하여 SYSDBA 권한으로 로그인합니다.

      ```
      sqlplus (user)/(password) as sysdba
      ```

    * 다음 명령을 실행하여 데이터베이스 인스턴스를 종료합니다.

      ```
      SHUTDOWN IMMEDIATE;
      ```

      `'ORACLE instance shut down'`을 확인할 때까지 기다립니다.

    * 보관을 사용하거나 사용하지 않도록 설정하려면 다음 명령을 실행하여 새 인스턴스를 시작하고 데이터베이스를 탑재합니다(열지 않음).

      ```
      STARTUP MOUNT;
      ```

      데이터베이스를 탑재해야 하며, 'Oracle 인스턴스가 시작됨'을 확인할 때까지 기다립니다.

    * 다음 명령을 실행하여 데이터베이스 보관 모드를 변경합니다.

      ```
      ALTER DATABASE ARCHIVELOG;
      ```

    * 다음 명령을 실행하여 일반 작업을 수행할 데이터베이스를 엽니다.

      ```
      ALTER DATABASE OPEN;
      ```

      ARC 파일이 표시되도록 하기 위해 다시 시작해야 할 수도 있습니다.

    * 확인하려면 다음 명령을 실행합니다.

      ```
      SELECT log_mode FROM v$database;
      ```

      `'ARCHIVELOG'` 응답이 표시됩니다. 응답이 `'NOARCHIVELOG'`이면 요구 사항이 충족되지 않습니다.

  * 다음 옵션 중 하나를 사용하여 복제에 대한 추가 로깅을 사용하도록 설정합니다.

    * **옵션 1**.
      PK 및 고유 인덱스가 있는 테이블이 모두 포함되도록 데이터베이스 수준 추가 로깅을 변경합니다. 검색 쿼리에서 `'IMPLICIT'`를 반환합니다.

      ```
      ALTER DATABASE ADD SUPPLEMENTAL LOG DATA (PRIMARY KEY, UNIQUE) COLUMNS;
      ```

      테이블 수준 보조 로깅을 변경합니다. 데이터 조작이 있고 PK 또는 고유 인덱스가 없는 테이블에 대해서만 실행합니다.

      ```
      ALTER TABLE [TABLENAME] ADD SUPPLEMENTAL LOG DATA (ALL) COLUMNS;
      ```

    * **옵션 2**.
      모든 테이블이 포함되도록 데이터베이스 수준 추가 로깅을 변경하면 검색 쿼리에서 `'YES'`를 반환합니다.

      ```
      ALTER DATABASE ADD SUPPLEMENTAL LOG DATA;
      ```

      테이블 수준 보조 로깅을 변경합니다. 아래 논리에 따라 모든 테이블에 대해 하나의 명령문만 실행합니다.

      테이블에 기본 키가 있는 경우:

      ```
      ALTER TABLE xxx ADD SUPPLEMENTAL LOG DATA (PRIMARY KEY) COLUMNS;
      ```

      테이블에 고유 인덱스가 있는 경우:

      ```
      ALTER TABLE xxx ADD SUPPLEMENTAL LOG GROUP (first unique index columns) ALWAYS;
      ```

      그렇지 않으면 다음 명령을 실행합니다.

      ```
      ALTER TABLE xxx ADD SUPPLEMENTAL LOG DATA (ALL) COLUMNS;
      ```

    확인하려면 다음 명령을 실행합니다.

      ```
      SELECT supplemental_log_data_min FROM v$database;
      ```

    `'YES'` 응답이 표시됩니다.

## <a name="assess-the-effort-for-an-oracle-to-azure-database-for-postgresql-migration"></a>Oracle에서 Azure Database for PostgreSQL로의 마이그레이션 작업 평가

ora2pg를 사용하여 Oracle에서 Azure Database for PostgreSQL로 마이그레이션하는 데 필요한 작업을 평가하는 것이 좋습니다. `ora2pg -t SHOW_REPORT` 지시문을 사용하여 변환의 일환으로 특별한 주의가 필요할 수 있는 모든 Oracle 개체, 예상 마이그레이션 비용(개발자 작업 일 수) 및 특정 데이터베이스 개체를 나열하는 보고서를 만듭니다.

대부분의 고객은 평가 보고서를 검토하고 자동 및 수동 변환 작업을 고려하는 데 상당한 시간을 할애합니다.

Ora2pg를 구성 하 고 실행 하 여 평가 보고서를 만들려면 Oracle의 **Premigration: 평가** 섹션을 참조 [하 여 Cookbook를 Azure Database for PostgreSQL](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20Azure%20PostgreSQL%20Migration%20Cookbook.pdf)합니다. 샘플 ora2pg 평가 보고서는 [여기](https://ora2pg.darold.net/report.html)서 참조할 수 있습니다.

## <a name="export-the-oracle-schema"></a>Oracle 스키마 내보내기

ora2pg를 사용하여 Oracle 스키마 및 다른 Oracle 개체(형식, 프로시저, 함수 등)를 Azure Database for PostgreSQL과 호환되는 스키마로 변환하는 것이 좋습니다. ora2pg에는 특정 데이터 형식을 미리 정의하는 데 도움이 되는 많은 지시문이 있습니다. 예를 들어 `DATA_TYPE` 지시문을 사용하여 모든 NUMBER(*,0)를 NUMERIC(38) 대신 bigint로 바꿀 수 있습니다.

ora2pg를 실행하여 각 데이터베이스 개체를 .sql 파일로 내보낼 수 있습니다. 그러면 .sql 파일을 검토한 후에 psql을 사용하여 Azure Database for PostgreSQL로 가져오거나 PgAdmin에서 .SQL 스크립트를 실행할 수 있습니다.

```
psql -f [FILENAME] -h [AzurePostgreConnection] -p 5432 -U [AzurePostgreUser] -d database 
```

예:

```
psql -f %namespace%\schema\sequences\sequence.sql -h server1-server.postgres.database.azure.com -p 5432 -U username@server1-server -d database
```

스키마 변환을 위해 ora2pg을 구성 하 고 실행 하려면 Oracle의 **Migration: schema and data** 섹션 [to Azure Database for PostgreSQL Cookbook를](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20Azure%20PostgreSQL%20Migration%20Cookbook.pdf)참조 하세요.

## <a name="set-up-the-schema-in-azure-database-for-postgresql"></a>Azure Database for PostgreSQL에서 스키마 설정

Azure Database Migration Service에서 마이그레이션 파이프라인을 시작하기 전에, ora2pg를 사용하여 Oracle 테이블 스키마, 저장 프로시저, 패키지 및 기타 데이터베이스 개체가 Postgres와 호환되도록 변환할 수 있습니다. ora2pg로 작업하는 방법은 아래 링크를 참조하세요.

* [Windows에 ora2pg 설치](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/Steps%20to%20Install%20ora2pg%20on%20Windows%20and%20Linux.pdf)
* [Oracle에서 Azure PostgreSQL로 마이그레이션 쿡북](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20Azure%20PostgreSQL%20Migration%20Cookbook.pdf)

Azure Database Migration Service는 PostgreSQL 테이블 스키마도 만들 수 있습니다. 이 서비스는 연결된 Oracle 원본의 테이블 스키마에 액세스하여 Azure Database for PostgreSQL에서 호환 가능한 테이블 스키마를 만듭니다. Azure Database Migration Service에서 스키마 생성 및 데이터 이동이 완료되면, Azure Database for PostgreSQL에서 스키마 형식을 검증하고 확인해야 합니다.

> [!IMPORTANT]
> Azure Database Migration Service는 테이블 스키마만 만듭니다. 저장 프로시저, 패키지, 인덱스 등과 같은 다른 데이터베이스 개체는 생성되지 않습니다.

또한, 전체 로드를 실행하려면 대상 데이터베이스에서 외래 키를 삭제해야 합니다. 외래 키를 삭제하는 데 사용할 수 있는 스크립트는 [이 문서](https://docs.microsoft.com/azure/dms/tutorial-postgresql-azure-postgresql-online)의 **샘플 스키마 마이그레이션** 섹션을 참조하세요. Azure Database Migration Service를 사용하여 전체 로드 및 동기화를 실행합니다.

### <a name="when-the-postgresql-table-schema-already-exists"></a>PostgreSQL 테이블 스키마가 이미 있는 경우

Azure Database Migration Service를 통해 데이터 이동을 시작하기 전에 ora2pg와 같은 도구를 사용하여 PostgreSQL 스키마를 생성하는 경우에는 Azure Database Migration Service의 대상 테이블에 원본 테이블을 매핑합니다.

1. Azure Database for PostgreSQL 마이그레이션 프로젝트를 새로 만들 때 스키마 선택 단계에서 대상 데이터베이스와 대상 스키마를 선택하라는 메시지가 표시됩니다. 대상 데이터베이스와 대상 스키마를 채우세요.

   ![포털 구독 표시](media/tutorial-oracle-azure-postgresql-online/dms-map-to-target-databases.png)

2. **마이그레이션 설정** 화면에는 Oracle 원본에 있는 테이블 목록이 표시됩니다. Azure Database Migration Service는 테이블 이름을 기반으로 원본 테이블과 대상 테이블의 테이블을 매칭하려고 합니다. 일치하지만 대/소문자가 다른 대상 테이블이 여러 개 있으면 매핑할 대상 테이블을 선택할 수 있습니다.

    ![포털 구독 표시](media/tutorial-oracle-azure-postgresql-online/dms-migration-settings.png)

> [!NOTE]
> 원본 테이블 이름을 이름이 다른 테이블에 매핑해야 하는 경우 [dmsfeedback@microsoft.com](mailto:dmsfeedbac@microsoft.com)에 이메일을 보내면 프로세스를 자동화하는 스크립트를 제공받을 수 있습니다.

### <a name="when-the-postgresql-table-schema-doesnt-exist"></a>PostgreSQL 테이블 스키마가 없는 경우

대상 PostgreSQL 데이터베이스에 테이블 스키마 정보가 없으면 Azure Database Migration Service는 원본 스키마를 변환하여 대상 데이터베이스에서 다시 만듭니다. 다시 말하지만, Azure Database Migration Service는 테이블 스키마만 만들고, 저장 프로시저, 패키지 및 인덱스와 같은 다른 데이터베이스 개체는 만들지 않습니다.
Azure Database Migration Service에서 스키마가 생성되도록 하려면 대상 환경에 기존 테이블이 없는 스키마가 포함되어 있어야 합니다. Azure Database Migration Service에서 테이블이 검색되면 ora2pg와 같은 외부 도구를 통해 스키마가 생성된 것으로 가정합니다.

> [!IMPORTANT]
> Azure Database Migration Service에서는 Azure Database Migration Service나 ora2pg와 같은 도구 중 하나만 사용하여(둘 다 사용하면 안 됨) 모든 테이블을 동일한 방식으로 만들어야 합니다.

시작하려면:

1. 애플리케이션 요구 사항에 따라 대상 데이터베이스에 스키마를 만듭니다. 기본적으로 PostgreSQL 테이블 스키마와 열 이름은 소문자입니다. 반면에 Oracle 테이블 스키마와 열은 기본적으로 모두 대문자입니다.
2. 스키마 선택 단계에서 대상 데이터베이스와 대상 스키마를 지정합니다.
3. Azure Database for PostgreSQL에서 만든 스키마를 기반으로 Azure Database Migration Service는 다음과 같은 변환 규칙을 사용합니다.

    Oracle 원본의 스키마 이름과 Azure Database for PostgreSQL의 스키마 이름이 일치하면 Azure Database Migration Service는 *대상과 동일한 대/소문자를 사용하여 테이블 스키마를 생성*합니다.

    예:

    | 원본 Oracle 스키마 | 대상 PostgreSQL Database.Schema | DMS 생성 schema.table.column |
    | ------------- | ------------- | ------------- |
    | HR | targetHR.public | public.countries.country_id |
    | HR | targetHR.trgthr | trgthr.countries.country_id |
    | HR | targetHR.TARGETHR | "TARGETHR"."COUNTRIES"."COUNTRY_ID" |
    | HR | targetHR.HR | "HR"."COUNTRIES"."COUNTRY_ID" |
    | HR | targetHR.Hr | *대/소문자를 혼합하여 매핑할 수 없음 |

    *대상 PostgreSQL에서 대/소문자 혼합 스키마와 테이블 이름을 생성하려면 [dmsfeedback@microsoft.com](mailto:dmsfeedback@microsoft.com)에 문의하세요. 대상 PostgreSQL에서 대/소문자 혼합 스키마를 설정하는 스크립트를 제공받을 수 있습니다.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Microsoft.DataMigration 리소스 공급자 등록

1. Azure Portal에 로그인하고, **모든 서비스**를 선택한 다음, **구독**을 선택합니다.

   ![포털 구독 표시](media/tutorial-oracle-azure-postgresql-online/portal-select-subscriptions.png)

2. Azure Database Migration Service의 인스턴스를 만들 구독을 선택한 다음 **리소스 공급자**를 선택합니다.

    ![리소스 공급자 보기](media/tutorial-oracle-azure-postgresql-online/portal-select-resource-provider.png)

3. 마이그레이션을 검색한 다음 **Microsoft.DataMigration**의 오른쪽에서 **등록**을 선택합니다.

    ![리소스 공급자 등록](media/tutorial-oracle-azure-postgresql-online/portal-register-resource-provider.png)

## <a name="create-a-dms-instance"></a>DMS 인스턴스 만들기

1. Azure Portal에서 **+ 리소스 만들기**를 선택하고, Azure Database Migration Service를 검색한 다음, 드롭다운 목록에서 **Azure Database Migration Service**를 선택합니다.

    ![Azure Marketplace](media/tutorial-oracle-azure-postgresql-online/portal-marketplace.png)

2. **Azure Database Migration Service** 화면에서 **만들기**를 선택합니다.

    ![Azure Database Migration Service 인스턴스 만들기](media/tutorial-oracle-azure-postgresql-online/dms-create2.png)
  
3. **Migration Service 만들기** 화면에서 서비스, 구독, 신규 또는 기존 리소스 그룹의 이름을 지정합니다.

4. 기존 가상 네트워크를 선택 하거나 새 가상 네트워크를 만드세요.

    가상 네트워크는 원본 Oracle 및 대상 Azure Database for PostgreSQL 인스턴스에 대한 액세스 권한을 Azure Database Migration Service 제공 합니다.

    Azure Portal에서 가상 네트워크를 만드는 방법에 대한 자세한 내용은 [Azure Portal를 사용 하 여 가상 네트워크 만들기](https://aka.ms/DMSVnet)문서를 참조 하세요.

5. 가격 책정 계층을 선택합니다.

    비용 및 가격 책정 계층에 대한 자세한 내용은 [가격 책정 페이지](https://aka.ms/dms-pricing)를 참조하세요.

    ![Azure Database Migration Service 인스턴스 설정 구성](media/tutorial-oracle-azure-postgresql-online/dms-settings5.png)

6. **만들기**를 선택하여 서비스를 만듭니다.

## <a name="create-a-migration-project"></a>마이그레이션 프로젝트 만들기

서비스가 생성된 후 Azure Portal에서 서비스를 찾아 연 다음, 새로운 마이그레이션 프로젝트를 만듭니다.

1. Azure Portal에서 **모든 서비스**를 선택하고, Azure Database Migration Service를 검색하고 나서, **Azure Database Migration Services**를 선택합니다.

    ![Azure Database Migration Service의 모든 인스턴스 찾기](media/tutorial-oracle-azure-postgresql-online/dms-search.png)

2. **Azure Database Migration Services** 화면에서 방금 만든 Azure Database Migration Service 인스턴스의 이름을 검색하고 인스턴스를 선택합니다.

    ![Azure Database Migration Service 인스턴스 찾기](media/tutorial-oracle-azure-postgresql-online/dms-instance-search.png)

3. **+ 새 마이그레이션 프로젝트**를 선택합니다.
4. **새 마이그레이션 프로젝트** 화면에서 프로젝트 이름을 지정하고, **원본 서버 유형** 텍스트 상자에서 **Oracle**을 선택하고, **대상 서버 유형** 텍스트 상자에서 **Azure Database for PostgreSQL**을 선택합니다.
5. **활동 유형 선택** 섹션에서 **온라인 데이터 마이그레이션**을 선택합니다.

   ![Database Migration Service 프로젝트 만들기](media/tutorial-oracle-azure-postgresql-online/dms-create-project5.png)

   > [!NOTE]
   > 또는 **프로젝트만 만들기**를 선택하여 지금 마이그레이션 프로젝트를 만들고, 나중에 마이그레이션을 실행할 수도 있습니다.

6. **저장**을 선택하고, Azure Database Migration Service를 사용하여 온라인 마이그레이션을 성공적으로 수행하기 위한 요구 사항을 기록한 다음, **활동 만들기 및 실행**을 선택합니다.

## <a name="specify-source-details"></a>원본 세부 정보 지정

* **원본 세부 정보 추가** 화면에서 원본 Oracle 인스턴스에 대한 연결 세부 정보를 지정합니다.

  ![원본 세부 정보 추가 화면](media/tutorial-oracle-azure-postgresql-online/dms-add-source-details.png)

## <a name="upload-oracle-oci-driver"></a>Oracle OCI 드라이버를 업로드 합니다.

1. **저장**을 선택한 다음, **OCI 드라이버 설치** 화면에서 Oracle 계정에 로그인 하 고 **instantclient-basiclite-windows. x64-12.2.0.1.0** (37128586 바이트) (SHA1 Checksum: 865082268) 드라이버를 [여기](https://www.oracle.com/technetwork/topics/winx64soft-089540.html#ic_winx64_inst)에서 다운로드 합니다.
2. 드라이버를 공유 폴더에 다운로드합니다.

   폴더가 최소 읽기 전용 액세스 권한으로 지정한 사용자 이름과 공유되는지 확인합니다. Azure Database Migration Service는 사용자가 지정한 사용자 이름을 가장하여 OCI 드라이버를 Azure에 업로드하기 위해 공유에 액세스하여 이를 읽습니다.

   지정한 사용자 이름은 Windows 사용자 계정이어야 합니다.

   ![OCI 드라이버 설치](media/tutorial-oracle-azure-postgresql-online/dms-oci-driver-install.png)

## <a name="specify-target-details"></a>대상 세부 정보 지정

1. **저장**을 선택한 다음, **대상 세부 정보** 화면에서 대상 Azure Database for PostgreSQL 서버에 대한 연결 세부 정보를 지정합니다. 이 서버는 **HR** 스키마가 배포된 Azure Database for PostgreSQL의 미리 프로비저닝된 인스턴스입니다.

    ![대상 세부 정보 화면](media/tutorial-oracle-azure-postgresql-online/dms-add-target-details1.png)

2. **저장**을 선택한 다음, **대상 데이터베이스에 매핑** 화면에서 마이그레이션하기 위해 원본 및 대상 데이터베이스를 매핑합니다.

    대상 데이터베이스의 이름이 원본 데이터베이스와 동일하면 Azure Database Migration Service는 기본적으로 이 대상 데이터베이스를 선택합니다.

    ![대상 데이터베이스에 매핑](media/tutorial-oracle-azure-postgresql-online/dms-map-target-details.png)

3. **저장**을 선택하고, **마이그레이션 요약** 화면의 **작업 이름** 텍스트 상자에서 마이그레이션 작업의 이름을 지정한 다음, 요약을 검토하여 원본 및 대상 세부 정보가 이전에 지정한 내용과 일치하는지 확인합니다.

    ![마이그레이션 요약](media/tutorial-oracle-azure-postgresql-online/dms-migration-summary.png)

## <a name="run-the-migration"></a>마이그레이션 실행

* **마이그레이션 실행**을 선택합니다.

  마이그레이션 작업 창이 나타나고, 작업 **상태**는 **초기화 중**입니다.

## <a name="monitor-the-migration"></a>마이그레이션 모니터링

1. 마이그레이션 작업 화면에서 **새로 고침**을 선택하여 마이그레이션 **상태**가 **실행 중**으로 표시될 때까지 디스플레이를 업데이트합니다.

     ![작업 상태 - 실행 중](media/tutorial-oracle-azure-postgresql-online/dms-activity-running.png)

2. **데이터베이스 이름** 아래에서 특정 데이터베이스를 선택하여 **전체 데이터 로드** 및 **증분 데이터 동기화** 작업에 대한 마이그레이션 상태로 이동합니다.

    전체 데이터 로드는 초기 로드 마이그레이션 상태를 표시하는 한편, 증분 데이터 동기화는 CDC(변경 데이터 캡처) 상태를 표시합니다.

     ![작업 상태 - 전체 로드 완료](media/tutorial-oracle-azure-postgresql-online/dms-activity-full-load-completed.png)

     ![작업 상태 - 증분 데이터 동기화](media/tutorial-oracle-azure-postgresql-online/dms-activity-incremental-data-sync.png)

## <a name="perform-migration-cutover"></a>마이그레이션 중단 수행

초기 전체 로드가 완료되면 데이터베이스가 **중단 준비 완료**로 표시됩니다.

1. 데이터베이스 마이그레이션을 완료할 준비가 되면 **중단 시작**을 선택합니다.

2. 원본 데이터베이스로 들어오는 모든 트랜잭션을 중지해야 합니다. **보류 중인 변경 내용** 카운터가 **0**으로 표시될 때까지 기다립니다.

   ![중단 시작](media/tutorial-oracle-azure-postgresql-online/dms-start-cutover.png)

3. **확인**과 **적용**을 차례로 선택합니다.
4. 데이터베이스 마이그레이션 상태가 **완료됨**으로 표시되면 애플리케이션을 새 대상 Azure Database for PostgreSQL 인스턴스에 연결합니다.

 > [!NOTE]
 > PostgreSQL에는 기본적으로 소문자의 schema.table.column이 있으므로 이 문서의 앞부분에 있는 **Azure Database for PostgreSQL에서 스키마 설정** 섹션의 스크립트를 사용하여 대문자에서 소문자로 되돌릴 수 있습니다.

## <a name="next-steps"></a>다음 단계

* Azure Database for PostgreSQL로 온라인 마이그레이션을 수행하는 경우와 관련하여 알려진 문제 및 제한 사항에 대한 자세한 내용은 [Azure Database for PostgreSQL 온라인 마이그레이션의 알려진 문제 및 해결 방법](known-issues-azure-postgresql-online.md) 문서를 참조하세요.
* Azure Database Migration Service에 대한 자세한 내용은 [Azure Database Migration Service란?](https://docs.microsoft.com/azure/dms/dms-overview) 문서를 참조하세요.
* Azure Database for PostgreSQL에 대한 자세한 내용은 [Azure Database for PostgreSQL이란?](https://docs.microsoft.com/azure/postgresql/overview) 문서를 참조하세요.
