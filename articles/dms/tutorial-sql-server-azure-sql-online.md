---
title: '자습서: 온라인으로 SQL Server SQL 단일 데이터베이스 마이그레이션'
titleSuffix: Azure Database Migration Service
description: Azure Database Migration Service를 사용하여 SQL Server 온-프레미스에서 Azure SQL Database의 단일 데이터베이스 또는 풀링된 데이터베이스로 온라인 마이그레이션을 수행하는 방법을 알아봅니다.
services: dms
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 01/21/2020
ms.openlocfilehash: 012e6eca13c2b1afe00343da98dd58ca1f683dd5
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/22/2020
ms.locfileid: "76315102"
---
# <a name="tutorial-migrate-sql-server-to-a-single-database-or-pooled-database-in-azure-sql-database-online-using-dms"></a>자습서: DMS를 사용 하 여 Azure SQL Database online에서 단일 데이터베이스 또는 풀링된 데이터베이스로 SQL Server 마이그레이션

Azure Database Migration Service를 사용하여 가동 중지 시간을 최소화하면서 데이터베이스를 온-프레미스 SQL Server 인스턴스에서 [Azure SQL Database](https://docs.microsoft.com/azure/sql-database/)로 마이그레이션할 수 있습니다. 이 자습서에서는 Azure Database Migration Service를 사용하여 SQL Server 2016 이상의 온-프레미스 인스턴스에 복원된 **Adventureworks2012** 데이터베이스를 Azure SQL Database의 단일 데이터베이스 또는 풀링된 데이터베이스로 마이그레이션합니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.
> [!div class="checklist"]
>
> - Data Migration Assistant를 사용하여 온-프레미스 데이터베이스를 평가합니다.
> - Data Migration Assistant를 사용하여 샘플 스키마를 마이그레이션합니다.
> - Azure Database Migration Service의 인스턴스를 만듭니다.
> - Azure Database Migration Service를 사용하여 마이그레이션 프로젝트를 만듭니다.
> - 마이그레이션을 실행합니다.
> - 마이그레이션을 모니터링합니다.
> - 마이그레이션 보고서를 다운로드합니다.

> [!NOTE]
> Azure Database Migration Service를 사용하여 온라인 마이그레이션을 수행하려면 프리미엄 가격 책정 계층에 따라 인스턴스를 만들어야 합니다. 자세한 내용은 Azure Database Migration Service [가격 책정](https://azure.microsoft.com/pricing/details/database-migration/) 페이지를 참조하세요.

> [!IMPORTANT]
> 최적의 마이그레이션 환경을 위해 대상 데이터베이스와 동일한 Azure 지역에서 Azure Database Migration Service의 인스턴스를 만드는 것이 좋습니다. 영역 또는 지역 간에 데이터를 이동하면 마이그레이션 프로세스 속도가 저하되고 오류가 발생할 수 있습니다.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

이 문서에서는 SQL Server에서 Azure SQL Database의 단일 데이터베이스 또는 풀링된 데이터베이스로 온라인 마이그레이션하는 방법을 설명합니다. 오프라인 마이그레이션의 경우 [DMS를 사용하여 Azure SQL Database로 SQL Server 오프라인 마이그레이션](tutorial-sql-server-to-azure-sql.md)을 참조하세요.

## <a name="prerequisites"></a>필수 조건

이 자습서를 완료하려면 다음이 필요합니다.

- [SQL Server 2012 이상](https://www.microsoft.com/sql-server/sql-server-downloads)을 다운로드하여 설치합니다.
- 문서 [서버 네트워크 프로토콜 사용 또는 사용 안 함](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure)의 지침을 수행하여 SQL Server Express를 설치하는 동안 기본적으로 사용 안 함으로 설정되어 있는 TCP/IP 프로토콜을 사용하도록 설정합니다.
- [Azure Portal을 사용하여 Azure SQL Database에서 단일 데이터베이스 만들기](https://docs.microsoft.com/azure/sql-database/sql-database-single-database-get-started) 문서의 세부 지침에 따라 Azure SQL Database에 단일(또는 풀링된) 데이터베이스를 만듭니다.

    > [!NOTE]
    > SSIS(SQL Server Integration Services)를 사용하고 있고, SSIS 프로젝트/패키지(SSISDB)용 카탈로그 데이터베이스를 SQL Server에서 Azure SQL Database로 마이그레이션하려는 경우 ADF(Azure Data Factory)에서 SSIS를 프로비저닝할 때 대상 SSISDB가 자동으로 생성되고 관리됩니다. SSIS 패키지 마이그레이션에 대한 자세한 내용은 [SQL Server Integration Services 패키지를 Azure로 마이그레이션](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages) 문서를 참조하세요.

- [DMA(Data Migration Assistant)](https://www.microsoft.com/download/details.aspx?id=53595) v3.3 이상을 다운로드하여 설치합니다.
- [Express](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) 경로 또는 [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways)을 사용 하 여 온-프레미스 원본 서버에 대한 사이트 간 연결을 제공 하는 Azure Resource Manager 배포 모델을 사용 하 여 Azure Database Migration Service에 대한 Microsoft Azure Virtual Network를 만듭니다. 가상 네트워크를 만드는 방법에 대한 자세한 내용은 [Virtual Network 설명서](https://docs.microsoft.com/azure/virtual-network/)와 특히 단계별 정보를 포함 하는 빠른 시작 문서를 참조 하세요.

    > [!NOTE]
    > 가상 네트워크를 설정 하는 동안 Microsoft에 네트워크 피어 링을 사용 하는 Express 경로를 사용 하는 경우 서비스가 프로 비전 될 서브넷에 다음 서비스 [엔드포인트](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) 을 추가 합니다.
    > - 대상 데이터베이스 엔드포인트(예: SQL 엔드포인트, Cosmos DB 엔드포인트 등)
    > - 스토리지 엔드포인트
    > - Service Bus 엔드포인트
    >
    > Azure Database Migration Service에는 인터넷 연결이 없으므로 이 구성이 필요합니다.

- 가상 네트워크 네트워크 보안 그룹 규칙에서 Azure Database Migration Service에 대한 인바운드 통신 포트 (443, 53, 9354, 445, 12000)를 차단 하지 않는지 확인 합니다. Azure Virtual Network NSG 트래픽 필터링에 대한 자세한 내용은 [네트워크 보안 그룹을 사용 하 여 네트워크 트래픽 필터링](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg)문서를 참조 하세요.
- [데이터베이스 엔진 액세스를 위한 Windows 방화벽](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access)을 구성합니다.
- Azure Database Migration Service가 기본적으로 TCP 포트 1433인 원본 SQL Server에 액세스하도록 허용하려면 Windows 방화벽을 엽니다.
- 동적 포트를 사용하여 명명된 여러 SQL Server 인스턴스를 실행하는 경우, SQL Browser 서비스를 사용하도록 설정하고 방화벽을 통해 1434 UDP 포트에 액세스하도록 허용하여 Azure Database Migration Service가 원본 서버에서 명명된 인스턴스에 연결할 수 있습니다.
- 원본 데이터베이스 앞에 방화벽 어플라이언스를 사용하는 경우, Azure Database Migration Service가 마이그레이션을 위해 원본 데이터베이스에 액세스할 수 있게 허용하는 방화벽 규칙을 추가해야 합니다.
- 대상 데이터베이스에 대한 Azure Database Migration Service 액세스를 허용하도록 Azure SQL Database 서버에 서버 수준 [방화벽 규칙](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure)을 만듭니다. Azure Database Migration Service에 사용 되는 가상 네트워크의 서브넷 범위를 제공 합니다.
- 원본 SQL Server 인스턴스에 연결하는 데 사용되는 자격 증명에는 [CONTROL SERVER](https://docs.microsoft.com/sql/t-sql/statements/grant-server-permissions-transact-sql) 권한이 있어야 합니다.
- 대상 Azure SQL Database 인스턴스에 연결 하는 데 사용 되는 자격 증명에 대상 Azure SQL Database 인스턴스에 대한 CONTROL DATABASE 권한이 있는지 확인 하십시오.
- 원본 SQL Server 버전은 SQL Server 2005 이상이어야 합니다. SQL Server 인스턴스에서 실행하는 버전을 확인하려면 [SQL Server의 버전, 에디션 및 업데이트 수준과 해당 구성 요소를 확인하는 방법 ](https://support.microsoft.com/help/321185/how-to-determine-the-version-edition-and-update-level-of-sql-server-an) 문서를 참조하세요.
- 데이터베이스는 대량 로그 또는 전체 복구 모드에 있어야 합니다. SQL Server 인스턴스에 대해 구성된 복구 모델을 확인하려면 [데이터베이스 복구 모델 보기 또는 변경(SQL Server)](https://docs.microsoft.com/sql/relational-databases/backup-restore/view-or-change-the-recovery-model-of-a-database-sql-server?view=sql-server-2017)을 참조하세요.
- 데이터베이스에 대해 전체 데이터베이스 백업을 수행해야 합니다. 전체 데이터베이스 백업을 만들려면 [방법: 전체 데이터베이스 백업 만들기(Transact-SQL)](https://docs.microsoft.com/previous-versions/sql/sql-server-2008-r2/ms191304(v=sql.105)) 문서를 참조하세요.
- 테이블 중 하나에 기본 키가 없으면 데이터베이스 및 특정 테이블에서 CDC(변경 데이터 캡처)를 사용하도록 설정합니다.
    > [!NOTE]
    > 아래 스크립트를 사용하여 기본 키가 없는 테이블을 찾을 수 있습니다.
    
    ```sql
    USE <DBName>;
    go
    SELECT is_tracked_by_cdc, name AS TableName
    FROM sys.tables WHERE type = 'U' and is_ms_shipped = 0 AND
    OBJECTPROPERTY(OBJECT_ID, 'TableHasPrimaryKey') = 0;
    ```

    결과에 'is_tracked_by_cdc'가 '0'인 하나 이상의 테이블이 표시되면, [변경 데이터 캡처 설정 및 해제(SQL Server)](https://docs.microsoft.com/sql/relational-databases/track-changes/enable-and-disable-change-data-capture-sql-server?view=sql-server-2017) 문서에서 설명하는 프로세스에 따라 데이터베이스 및 특정 테이블에 대한 변경 캡처를 사용하도록 설정합니다.

- 원본 SQL Server에 대한 배포자 역할을 구성합니다.

    >[!NOTE]
    > 아래 쿼리를 사용하여 복제 구성 요소가 설치되어 있는지 확인할 수 있습니다.

    ```sql
    USE master;
    DECLARE @installed int;
    EXEC @installed = sys.sp_MS_replication_installed;
    SELECT @installed as installed;
    ```

    결과에서 복제 구성 요소를 설치하도록 제안하는 오류 메시지가 반환되면 [SQL Server 복제 설치](https://docs.microsoft.com/sql/database-engine/install-windows/install-sql-server-replication?view=sql-server-2017) 문서의 프로세스에 따라 SQL Server 복제 구성 요소를 설치합니다.

    복제가 이미 설치되어 있으면 아래의 T-SQL 명령을 사용하여 원본 SQL Server에 배포 역할이 구성되어 있는지 확인합니다.

    ```sql
    EXEC sp_get_distributor;
    ```

    배포가 설정되지 않은 경우, 즉 배포 서버에서 위 명령 출력에 대해 NULL을 표시하면 [배포 구성](https://docs.microsoft.com/sql/relational-databases/replication/configure-publishing-and-distribution?view=sql-server-2017) 문서에 제공된 지침에 따라 배포를 구성합니다.

- 대상 Azure SQL Database에서 데이터베이스 트리거를 사용하지 않도록 설정합니다.
    >[!NOTE]
    > 다음 쿼리를 사용하여 대상 Azure SQL Database에서 데이터베이스 트리거를 찾을 수 있습니다.

    ```sql
    Use <Database name>
    select * from sys.triggers
    DISABLE TRIGGER (Transact-SQL)
    ```

    자세한 내용은 [DISABLE TRIGGER(Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/disable-trigger-transact-sql?view=sql-server-2017) 문서를 참조하세요. 

## <a name="assess-your-on-premises-database"></a>온-프레미스 데이터베이스 평가

온-프레미스 SQL Server 인스턴스에서 Azure SQL Database의 단일 데이터베이스 또는 풀링된 데이터베이스로 데이터를 마이그레이션하려면 먼저 마이그레이션을 방해할 수 있는 차단 문제가 있는지 SQL Server 데이터베이스를 평가해야 합니다. Data Migration Assistant v3.3 이상에서 [SQL Server 마이그레이션 평가 수행](https://docs.microsoft.com/sql/dma/dma-assesssqlonprem) 문서에 설명된 단계를 수행하여 온-프레미스 데이터베이스 평가를 완료합니다.

온-프레미스 데이터베이스를 평가하려면 다음 단계를 수행합니다.

1. DMA에서 새로 만들기(+) 아이콘을 선택한 다음, **평가** 프로젝트 형식을 선택합니다.
2. 프로젝트 이름을 지정하고, **원본 서버 유형** 텍스트 상자에서 **SQL Server**를 선택하고, **대상 서버 유형** 텍스트 상자에서 **Azure SQL Database**를 선택한 다음, **만들기**를 선택하여 프로젝트를 만듭니다.

   Azure SQL Database의 단일 데이터베이스 또는 풀링된 데이터베이스로 마이그레이션할 원본 SQL Server 데이터베이스를 평가할 때 다음 평가 보고서 유형 중 하나 또는 둘 다 선택할 수 있습니다.

   - 데이터베이스 호환성 확인
   - 기능 패리티 확인

   두 보고서 유형이 모두 기본적으로 선택됩니다.

3. DMA의 **옵션** 화면에서 **다음**을 선택합니다.
4. **원본 선택** 화면의 **서버에 연결** 대화 상자에서 SQL Server에 대한 연결 세부 정보를 제공하고 **연결**을 선택합니다.
5. **원본 추가** 대화 상자에서 **AdventureWorks2012**를 선택하고 **추가**를 선택한 후 **평가 시작**을 선택합니다.

    > [!NOTE]
    > SSIS를 사용하는 경우 DMA가 현재 원본 SSISDB의 평가를 지원하지 않습니다. 그러나 SSIS 프로젝트/패키지는 Azure SQL Database에서 호스팅하는 대상 SSISDB에 재배포되므로 평가/유효성 검사되지 않습니다. SSIS 패키지 마이그레이션에 대한 자세한 내용은 [SQL Server Integration Services 패키지를 Azure로 마이그레이션](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages) 문서를 참조하세요.

    평가가 완료되면 다음 그래픽에 표시된 대로 결과가 표시됩니다.

    ![데이터 마이그레이션 평가](media/tutorial-sql-server-to-azure-sql-online/dma-assessments.png)

    Azure SQL Database의 단일 데이터베이스 또는 풀링된 데이터베이스의 경우 평가를 통해 단일 데이터베이스 또는 풀링된 데이터베이스에 배포하는 것을 방해하는 기능 패리티 문제 및 마이그레이션 차단 문제를 식별합니다.

    - **SQL Server 기능 패리티** 범주는 전체 권장 사항 집합, Azure에서 사용 가능한 대체 방법 및 마이그레이션 프로젝트에 대한 작업을 계획하는 데 도움이 되는 완화 단계를 제공합니다.
    - **호환성 문제** 범주는 온-프레미스 SQL Server 데이터베이스를 Azure SQL Database로 마이그레이션하는 작업을 차단할 수 있는 호환성 문제를 반영하는 기능을 부분적으로 지원되거나 지원되지 않는 기능을 식별합니다. 해당 문제를 해결하는 데 도움이 되는 권장 사항도 제공됩니다.

6. 특정 옵션을 선택하여 마이그레이션 차단 문제와 기능 패리티 문제에 대한 평가 결과를 검토합니다.

## <a name="migrate-the-sample-schema"></a>샘플 스키마 마이그레이션

평가가 만족스럽고 선택한 데이터베이스가 Azure SQL Database의 단일 데이터베이스 또는 풀링된 데이터베이스로 마이그레이션하는 데 적합한 후보라고 판단되면 DMA를 사용하여 스키마를 Azure SQL Database로 마이그레이션합니다.

> [!NOTE]
> DMA에서 마이그레이션 프로젝트를 만들기 전에 필수 구성 요소에서 설명한 대로 Azure SQL 데이터베이스를 이미 프로비전했는지 확인합니다. 이 자습서에서는 Azure SQL Database의 이름은 **AdventureWorksAzure**로 가정하지만, 원하는 이름으로 지정할 수 있습니다.

> [!IMPORTANT]
> SSIS를 사용하는 경우 DMA는 현재 원본 SSISDB 마이그레이션을 지원하지 않지만 Azure SQL Database에서 호스팅하는 대상 SSISDB에 SSIS 프로젝트/패키지를 재배포할 수 있습니다. SSIS 패키지 마이그레이션에 대한 자세한 내용은 [SQL Server Integration Services 패키지를 Azure로 마이그레이션](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages) 문서를 참조하세요.

**AdventureWorks2012** 스키마를 단일 데이터베이스 또는 풀링된 데이터베이스 Azure SQL Database로 마이그레이션하려면 다음 단계를 수행합니다.

1. Data Migration Assistant에서 새로 만들기(+) 아이콘을 선택하고 **속성 유형**에서 **마이그레이션**을 선택합니다.
2. 프로젝트 이름을 지정하고, **원본 서버 유형** 텍스트 상자에서 **SQL Server**를 선택한 다음, **대상 서버 유형** 텍스트 상자에서 **Azure SQL Database**를 선택합니다.
3. **마이그레이션 범위**에서 **스키마만**을 선택합니다.

    이전 단계가 수행되면 다음 그래픽과 같이 DMA 인터페이스가 표시됩니다.

    ![Data Migration Assistant 프로젝트 만들기](media/tutorial-sql-server-to-azure-sql-online/dma-create-project.png)

4. **만들기**를 선택하여 프로젝트를 만듭니다.
5. DMA에서 SQL Server에 대한 원본 연결 세부 정보를 지정하고, **연결**을 선택한 다음, **AdventureWorks2012** 데이터베이스를 선택합니다.

    ![Data Migration Assistant 원본 연결 세부 정보](media/tutorial-sql-server-to-azure-sql-online/dma-source-connect.png)

6. **대상 서버에 연결** 아래에서 **다음**을 선택하고, Azure SQL 데이터베이스에 대한 대상 연결 세부 정보를 지정하고, **연결**을 선택한 다음, Azure SQL Database에서 미리 프로비전된 **AdventureWorksAzure** 데이터베이스를 선택합니다.

    ![Data Migration Assistant 대상 연결 세부 정보](media/tutorial-sql-server-to-azure-sql-online/dma-target-connect.png)

7. **다음**을 선택하여 **개체 선택** 화면으로 이동합니다. 여기에서 Azure SQL Database에 배포해야 하는 **AdventureWorks2012** 데이터베이스의 스키마 개체를 지정할 수 있습니다.

    기본적으로 모든 개체가 선택됩니다.

    ![SQL 스크립트 생성](media/tutorial-sql-server-to-azure-sql-online/dma-assessment-source.png)

8. **SQL 스크립트 생성**을 선택하여 SQL 스크립트를 만들고 스크립트에 오류가 있는지 검토합니다.

    ![스키마 스크립트](media/tutorial-sql-server-to-azure-sql-online/dma-schema-script.png)

9. **스키마 배포**를 선택하여 Azure SQL Database에 스키마를 배포하고, 스키마가 배포된 후 대상 서버에 이상이 있는지 확인합니다.

    ![스키마 배포](media/tutorial-sql-server-to-azure-sql-online/dma-schema-deploy.png)

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Microsoft.DataMigration 리소스 공급자 등록

1. Azure Portal에 로그인하고, **모든 서비스**를 선택한 다음, **구독**을 선택합니다.

   ![포털 구독 표시](media/tutorial-sql-server-to-azure-sql-online/portal-select-subscription1.png)

2. Azure Database Migration Service의 인스턴스를 만들 구독을 선택한 다음 **리소스 공급자**를 선택합니다.

    ![리소스 공급자 보기](media/tutorial-sql-server-to-azure-sql-online/portal-select-resource-provider.png)

3. 마이그레이션을 검색한 다음 **Microsoft.DataMigration**의 오른쪽에서 **등록**을 선택합니다.

    ![리소스 공급자 등록](media/tutorial-sql-server-to-azure-sql-online/portal-register-resource-provider.png)

## <a name="create-an-instance"></a>인스턴스 만들기

1. Azure Portal에서 **+ 리소스 만들기**를 선택하고, Azure Database Migration Service를 검색한 다음, 드롭다운 목록에서 **Azure Database Migration Service**를 선택합니다.

    ![Azure Marketplace](media/tutorial-sql-server-to-azure-sql-online/portal-marketplace.png)

2. **Azure Database Migration Service** 화면에서 **만들기**를 선택합니다.

    ![Azure Database Migration Service 인스턴스 만들기](media/tutorial-sql-server-to-azure-sql-online/dms-create1.png)
  
3. **Migration Service 만들기** 화면에서 서비스, 구독, 신규 또는 기존 리소스 그룹의 이름을 지정합니다.

4. Azure Database Migration Service의 인스턴스를 만들 위치를 선택합니다. 

5. 기존 가상 네트워크를 선택 하거나 새 가상 네트워크를 만드세요.

    가상 네트워크는 원본 SQL Server 및 대상 Azure SQL Database 인스턴스에 액세스할 수 있는 Azure Database Migration Service을 제공 합니다.

    Azure Portal에서 가상 네트워크를 만드는 방법에 대한 자세한 내용은 [Azure Portal를 사용 하 여 가상 네트워크 만들기](https://aka.ms/DMSVnet)문서를 참조 하세요.

6. 가격 책정 계층을 선택합니다.

    비용 및 가격 책정 계층에 대한 자세한 내용은 [가격 책정 페이지](https://aka.ms/dms-pricing)를 참조하세요.

    ![Azure Database Migration Service 인스턴스 설정 구성](media/tutorial-sql-server-to-azure-sql-online/dms-settings2.png)

7. **만들기**를 선택하여 서비스를 만듭니다.

## <a name="create-a-migration-project"></a>마이그레이션 프로젝트 만들기

서비스가 생성된 후 Azure Portal에서 서비스를 찾아 연 다음, 새로운 마이그레이션 프로젝트를 만듭니다.

1. Azure Portal에서 **모든 서비스**를 선택하고, Azure Database Migration Service를 검색하고 나서, **Azure Database Migration Services**를 선택합니다.

    ![Azure Database Migration Service의 모든 인스턴스 찾기](media/tutorial-sql-server-to-azure-sql-online/dms-search.png)

2. **Azure Database Migration Services** 화면에서 방금 만든 Azure Database Migration Service 인스턴스의 이름을 검색하고 인스턴스를 선택합니다.

    ![Azure Database Migration Service 인스턴스 찾기](media/tutorial-sql-server-to-azure-sql-online/dms-instance-search.png)

3. **+ 새 마이그레이션 프로젝트**를 선택합니다.
4. **새 마이그레이션 프로젝트** 화면에서 프로젝트 이름을 지정하고, **원본 서버 유형** 텍스트 상자에서 **SQL Server**를 선택하고, **대상 서버 유형** 텍스트 상자에서 **Azure SQL Database**를 선택합니다.
5. **활동 유형 선택** 섹션에서 **온라인 데이터 마이그레이션**을 선택합니다.

    ![Database Migration Service 프로젝트 만들기](media/tutorial-sql-server-to-azure-sql-online/dms-create-project3.png)

    > [!NOTE]
    > 또는 **프로젝트만 만들기**를 선택하여 지금 마이그레이션 프로젝트를 만들고, 나중에 마이그레이션을 실행할 수도 있습니다.

6. **저장**을 선택합니다.

7. **작업 만들기 및 실행**을 선택하여 프로젝트를 만들고 마이그레이션 작업을 실행합니다.

    ![Database Migration Service 작업 만들기 및 실행](media/tutorial-sql-server-to-azure-sql-online/dms-create-and-run-activity.png)

## <a name="specify-source-details"></a>원본 세부 정보 지정

1. **마이그레이션 원본 세부 정보** 화면에서 원본 SQL Server 인스턴스에 대한 연결 세부 정보를 지정합니다.

    원본 SQL Server 인스턴스 이름에 정규화된 도메인 이름(FQDN)을 사용하도록 합니다. 또한 DNS 이름을 확인할 수 없는 경우에는 IP 주소를 사용할 수도 있습니다.

2. 원본 서버에 신뢰할 수 있는 인증서가 설치되지 않은 경우 **서버 인증서 신뢰** 확인란을 선택합니다.

    신뢰할 수 있는 인증서가 설치되어 있지 않으면 인스턴스가 시작될 때 SQL Server에서 자체 서명 인증서를 생성합니다. 이 인증서는 클라이언트 연결에 대한 자격 증명을 암호화하는 데 사용됩니다.

    > [!CAUTION]
    > 자체 서명 인증서를 사용하여 암호화된 SSL 연결은 강력한 보안을 제공하지 않습니다. 중간자 공격(man-in-the-middle)을 받기 쉽습니다. 프로덕션 환경이나 인터넷에 연결된 서버에서 자체 서명 인증서를 사용하는 SSL을 신뢰해서는 안 됩니다.

   ![원본 세부 정보](media/tutorial-sql-server-to-azure-sql-online/dms-source-details3.png)

    > [!IMPORTANT]
    > SSIS를 사용하는 경우 DMS는 현재 원본 SSISDB 마이그레이션을 지원하지 않지만 Azure SQL Database에서 호스팅하는 대상 SSISDB에 SSIS 프로젝트/패키지를 재배포할 수 있습니다. SSIS 패키지 마이그레이션에 대한 자세한 내용은 [SQL Server Integration Services 패키지를 Azure로 마이그레이션](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages) 문서를 참조하세요.

## <a name="specify-target-details"></a>대상 세부 정보 지정

1. **저장**을 선택한 다음, **마이그레이션 대상 세부 정보** 화면에서 대상 Azure SQL Database 서버에 대한 연결 세부 정보를 지정합니다. 대상은 DMA를 통해 **AdventureWorks2012** 스키마가 배포된 미리 프로비저닝된 Azure SQL Database입니다.

    ![대상 선택](media/tutorial-sql-server-to-azure-sql-online/dms-select-target3.png)

2. **저장**을 선택한 다음, **대상 데이터베이스에 매핑** 화면에서 마이그레이션하기 위해 원본 및 대상 데이터베이스를 매핑합니다.

    대상 데이터베이스의 이름이 원본 데이터베이스와 동일하면 Azure Database Migration Service는 기본적으로 대상 데이터베이스를 선택합니다.

    ![대상 데이터베이스에 매핑](media/tutorial-sql-server-to-azure-sql-online/dms-map-targets-activity3.png)

3. **저장**을 선택하고, **테이블 선택** 화면에서 테이블 목록을 확장하한 다음, 영향받는 필드 목록을 검토합니다.

    Azure Database Migration Service는 대상 Azure SQL Database 인스턴스에 있는 모든 빈 원본 테이블을 자동으로 선택합니다. 이미 데이터를 포함하는 테이블을 다시 마이그레이션하려면 이 블레이드에서 테이블을 명시적으로 선택해야 합니다.

    ![테이블 선택](media/tutorial-sql-server-to-azure-sql-online/dms-configure-setting-activity3.png)

4. **저장**을 선택하고, **마이그레이션 요약** 화면의 **작업 이름** 텍스트 상자에서 마이그레이션 작업의 이름을 지정한 다음, 요약을 검토하여 원본 및 대상 세부 정보가 이전에 지정한 내용과 일치하는지 확인합니다.

    ![마이그레이션 요약](media/tutorial-sql-server-to-azure-sql-online/dms-migration-summary.png)

## <a name="run-the-migration"></a>마이그레이션 실행

- **마이그레이션 실행**을 선택합니다.

    마이그레이션 작업 창이 나타나고, 작업 **상태**는 **초기화 중**입니다.

    ![작업 상태 - 초기화 중](media/tutorial-sql-server-to-azure-sql-online/dms-activity-status2.png)

## <a name="monitor-the-migration"></a>마이그레이션 모니터링

1. 마이그레이션 작업 화면에서 **새로 고침**을 선택하여 마이그레이션 **상태**가 **실행 중**으로 표시될 때까지 디스플레이를 업데이트합니다.

2. 특정 데이터베이스를 클릭하여 **전체 데이터 로드** 및 **증분 데이터 동기화** 작업에 대한 마이그레이션 상태로 이동합니다.

    ![작업 상태 - 진행 중](media/tutorial-sql-server-to-azure-sql-online/dms-activity-in-progress.png)

## <a name="perform-migration-cutover"></a>마이그레이션 중단 수행

초기 전체 로드가 완료되면 데이터베이스가 **중단 준비 완료**로 표시됩니다.

1. 데이터베이스 마이그레이션을 완료할 준비가 되면 **중단 시작**을 선택합니다.

    ![중단 시작](media/tutorial-sql-server-to-azure-sql-online/dms-start-cutover.png)

2. 원본 데이터베이스로 들어오는 모든 트랜잭션을 중지해야 합니다. **보류 중인 변경 내용** 카운터가 **0**으로 표시될 때까지 기다립니다.
3. **확인**, **적용**을 차례로 선택합니다.
4. 데이터베이스 마이그레이션 상태가 **완료됨**으로 표시되면 애플리케이션을 새 대상 Azure SQL Database에 연결합니다.

    ![작업 상태 - 완료됨](media/tutorial-sql-server-to-azure-sql-online/dms-activity-completed.png)

## <a name="next-steps"></a>다음 단계

- Azure SQL Database로 온라인 마이그레이션을 수행할 때 발생하는 알려진 문제 및 제한 사항에 대한 자세한 내용은 [Azure SQL Database 온라인 마이그레이션의 알려진 문제 및 해결 방법](known-issues-azure-sql-online.md) 문서를 참조하세요.
- Azure Database Migration Service에 대한 자세한 내용은 [Azure Database Migration Service란?](https://docs.microsoft.com/azure/dms/dms-overview) 문서를 참조하세요.
- Azure SQL Database에 대한 자세한 내용은 [Azure SQL Database 서비스란?](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview) 문서를 참조하세요.
