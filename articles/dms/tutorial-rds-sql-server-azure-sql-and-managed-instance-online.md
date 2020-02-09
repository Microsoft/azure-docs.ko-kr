---
title: '자습서: SQL Database로 RDS SQL Server online 마이그레이션'
titleSuffix: Azure Database Migration Service
description: Azure Database Migration Service를 사용 하 여 RDS SQL Server에서 Azure SQL Database 단일 데이터베이스 또는 관리 되는 인스턴스로 온라인 마이그레이션을 수행 하는 방법에 대해 알아봅니다.
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
ms.openlocfilehash: 52a6ee282e12f0ece5f16c1fa67c38f07f9d86e7
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/08/2020
ms.locfileid: "75751283"
---
# <a name="tutorial-migrate-rds-sql-server-to-azure-sql-database-or-an-azure-sql-database-managed-instance-online-using-dms"></a>자습서: DMS를 사용 하 여 RDS SQL Server Azure SQL Database 또는 Azure SQL Database 관리 되는 인스턴스로 마이그레이션
Azure Database Migration Service를 사용하여 가동 중지 시간을 최소화하면서 데이터베이스를 RDS SQL Server 인스턴스에서 [Azure SQL Database](https://docs.microsoft.com/azure/sql-database/) 또는 [Azure SQL Database 관리형 인스턴스](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-index)로 마이그레이션할 수 있습니다. 이 자습서에서는 Azure Database Migration Service를 사용하여 SQL Server 2012 이상의 RDS SQL Server 인스턴스로 복원된 **Adventureworks2012** 데이터베이스를 Azure SQL Database 또는 Azure SQL Database 관리형 인스턴스로 마이그레이션합니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.
> [!div class="checklist"]
> * Azure SQL Database 인스턴스 또는 Azure SQL Database 관리형 인스턴스를 만듭니다. 
> * Data Migration Assistant를 사용하여 샘플 스키마를 마이그레이션합니다.
> * Azure Database Migration Service의 인스턴스를 만듭니다.
> * Azure Database Migration Service를 사용하여 마이그레이션 프로젝트를 만듭니다.
> * 마이그레이션을 실행합니다.
> * 마이그레이션을 모니터링합니다.
> * 마이그레이션 보고서를 다운로드합니다.

> [!NOTE]
> Azure Database Migration Service를 사용하여 온라인 마이그레이션을 수행하려면 프리미엄 가격 책정 계층에 따라 인스턴스를 만들어야 합니다. 자세한 내용은 Azure Database Migration Service [가격 책정](https://azure.microsoft.com/pricing/details/database-migration/) 페이지를 참조하세요.

> [!IMPORTANT]
> 최적의 마이그레이션 환경을 위해 대상 데이터베이스와 동일한 Azure 지역에서 Azure Database Migration Service의 인스턴스를 만드는 것이 좋습니다. 영역 또는 지역 간에 데이터를 이동하면 마이그레이션 프로세스 속도가 저하되고 오류가 발생할 수 있습니다.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

이 문서에서는 RDS SQL Server에서 Azure SQL Database 또는 Azure SQL Database 관리형 인스턴스로의 온라인 마이그레이션에 대해 설명합니다.

## <a name="prerequisites"></a>필수 조건
이 자습서를 완료하려면 다음이 필요합니다.

* [RDS SQL Server 데이터베이스](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_GettingStarted.CreatingConnecting.SQLServer.html)를 만듭니다.
* [Azure Portal에서 Azure SQL 데이터베이스 만들기](https://docs.microsoft.com/azure/sql-database/sql-database-get-started-portal) 문서의 세부 지침을 수행하여 Azure SQL Database 인스턴스를 만듭니다.

    > [!NOTE]
    > Azure SQL Database 관리형 인스턴스로 마이그레이션하는 경우 [Azure SQL Database 관리형 인스턴스 만들기](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started) 문서의 세부 지침을 수행한 다음, **AdventureWorks2012**라는 빈 데이터베이스를 만듭니다. 
 
* [DMA(Data Migration Assistant)](https://www.microsoft.com/download/details.aspx?id=53595) v3.3 이상을 다운로드하여 설치합니다.
* Azure Resource Manager 배포 모델을 사용 하 여 Azure Database Migration Service에 대한 Microsoft Azure Virtual Network를 만듭니다. Azure SQL Database 관리 되는 인스턴스로 마이그레이션하는 경우에는 Azure SQL Database 관리 되는 인스턴스에 사용 되는 것과 동일한 가상 네트워크에 있는 DMS 인스턴스를 다른 서브넷에 만들어야 합니다.  또는 DMS에 대해 다른 가상 네트워크를 사용 하는 경우 두 가상 네트워크 간에 가상 네트워크 피어 링을 만들어야 합니다. 가상 네트워크를 만드는 방법에 대한 자세한 내용은 [Virtual Network 설명서](https://docs.microsoft.com/azure/virtual-network/)와 특히 단계별 정보를 포함 하는 빠른 시작 문서를 참조 하세요.

    > [!NOTE]
    > 가상 네트워크를 설정 하는 동안 Microsoft에 네트워크 피어 링을 사용 하는 Express 경로를 사용 하는 경우 서비스가 프로 비전 될 서브넷에 다음 서비스 [엔드포인트](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) 을 추가 합니다.
    >
    > * 대상 데이터베이스 엔드포인트(예: SQL 엔드포인트, Cosmos DB 엔드포인트 등)
    > * 스토리지 엔드포인트
    > * Service Bus 엔드포인트
    >
    > Azure Database Migration Service에는 인터넷 연결이 없으므로 이 구성이 필요합니다. 

* 가상 네트워크 네트워크 보안 그룹 규칙에서 Azure Database Migration Service에 대한 인바운드 통신 포트 (443, 53, 9354, 445, 12000)를 차단 하지 않는지 확인 합니다. Virtual network NSG 트래픽 필터링에 대한 자세한 내용은 [네트워크 보안 그룹을 사용 하 여 네트워크 트래픽 필터링](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg)문서를 참조 하세요.
* [데이터베이스 엔진 액세스를 위한 Windows 방화벽](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access)을 구성합니다.
* Azure Database Migration Service가 기본적으로 TCP 포트 1433인 원본 SQL Server에 액세스하도록 허용하려면 Windows 방화벽을 엽니다.
* 대상 데이터베이스에 대한 Azure Database Migration Service 액세스를 허용하도록 Azure SQL Database 서버에 서버 수준 [방화벽 규칙](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure)을 만듭니다. Azure Database Migration Service에 사용 되는 가상 네트워크의 서브넷 범위를 제공 합니다.
* 원본 RDS SQL Server 인스턴스에 연결하는 데 사용되는 자격 증명이 "Processadmin" 서버 역할의 멤버이자 마이그레이션할 모든 데이터베이스의 "db_owner" 데이터베이스 역할 멤버인 계정과 연결되어야 합니다.
* Azure SQL Database 관리형 인스턴스로 마이그레이션하는 경우 대상 Azure SQL Database 인스턴스에 연결하는 데 사용되는 자격 증명에는 대상 Azure SQL 데이터베이스 및 sysadmin 역할의 멤버에 대한 CONTROL DATABASE 권한이 있어야 합니다.
* 원본 RDS SQL Server 버전은 SQL Server 2012 이상이어야 합니다. SQL Server 인스턴스에서 실행하는 버전을 확인하려면 [SQL Server의 버전, 에디션 및 업데이트 수준과 해당 구성 요소를 확인하는 방법 ](https://support.microsoft.com/help/321185/how-to-determine-the-version-edition-and-update-level-of-sql-server-an) 문서를 참조하세요.
* 마이그레이션하기로 선택한 RDS SQL Server 데이터베이스 및 모든 사용자 테이블에 CDC(변경 데이터 캡처)를 사용하도록 설정합니다.
    > [!NOTE]
    > 아래 스크립트를 사용하여 RDS SQL Server 데이터베이스에 CDC를 사용하도록 설정할 수 있습니다.
    ```
    exec msdb.dbo.rds_cdc_enable_db 'AdventureWorks2012'
    ```
    > 아래 스크립트를 사용하여 모든 테이블에 CDC를 사용하도록 설정할 수 있습니다.
    ```
    use <Database name>
    go
    exec sys.sp_cdc_enable_table 
    @source_schema = N'Schema name', 
    @source_name = N'table name', 
    @role_name = NULL, 
    @supports_net_changes = 1 --for PK table 1, non PK tables 0
    GO
    ```
* 대상 Azure SQL Database에서 데이터베이스 트리거를 사용하지 않도록 설정합니다.
    > [!NOTE]
    > 다음 쿼리를 사용하여 대상 Azure SQL Database에서 데이터베이스 트리거를 찾을 수 있습니다.
    ```
    Use <Database name>
    select * from sys.triggers
    DISABLE TRIGGER (Transact-SQL)
    ```
    자세한 내용은 [DISABLE TRIGGER(Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/disable-trigger-transact-sql?view=sql-server-2017) 문서를 참조하세요.

## <a name="migrate-the-sample-schema"></a>샘플 스키마 마이그레이션
DMA를 사용하여 Azure SQL Database로 스키마를 마이그레이션합니다.

> [!NOTE]
> DMA에서 마이그레이션 프로젝트를 만들기 전에 필수 구성 요소에서 설명한 대로 Azure SQL 데이터베이스를 이미 프로비전했는지 확인합니다. 이 자습서에서는 Azure SQL Database 이름이 **AdventureWorks2012**라고 가정하지만, 본인이 원하는 이름으로 지정해도 됩니다.

**AdventureWorks2012** 스키마를 Azure SQL Database로 마이그레이션하려면 다음 단계를 수행합니다.

1. Data Migration Assistant에서 새로 만들기(+) 아이콘을 선택하고 **속성 유형**에서 **마이그레이션**을 선택합니다.
2. 프로젝트 이름을 지정하고, **원본 서버 유형** 텍스트 상자에서 **SQL Server**를 선택한 다음, **대상 서버 유형** 텍스트 상자에서 **Azure SQL Database**를 선택합니다.
3. **마이그레이션 범위**에서 **스키마만**을 선택합니다.

    이전 단계가 수행되면 다음 그래픽과 같이 DMA 인터페이스가 표시됩니다.

    ![Data Migration Assistant 프로젝트 만들기](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dma-create-project.png)

4. **만들기**를 선택하여 프로젝트를 만듭니다.
5. DMA에서 SQL Server에 대한 원본 연결 세부 정보를 지정하고, **연결**을 선택한 다음, **AdventureWorks2012** 데이터베이스를 선택합니다.

    ![Data Migration Assistant 원본 연결 세부 정보](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dma-source-connect.png)

6. **대상 서버에 연결** 아래에서 **다음**을 선택하고, Azure SQL 데이터베이스에 대한 대상 연결 세부 정보를 지정하고, **연결**을 선택한 다음, Azure SQL Database에서 미리 프로비저닝한 **AdventureWorksAzure** 데이터베이스를 선택합니다.

    ![Data Migration Assistant 대상 연결 세부 정보](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dma-target-connect.png)

7. **다음**을 선택하여 **개체 선택** 화면으로 이동합니다. 여기에서 Azure SQL Database에 배포해야 하는 **AdventureWorks2012** 데이터베이스의 스키마 개체를 지정할 수 있습니다.

    기본적으로 모든 개체가 선택됩니다.

    ![SQL 스크립트 생성](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dma-assessment-source.png)

8. **SQL 스크립트 생성**을 선택하여 SQL 스크립트를 만들고 스크립트에 오류가 있는지 검토합니다.

    ![스키마 스크립트](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dma-schema-script.png)

9. **스키마 배포**를 선택하여 Azure SQL Database에 스키마를 배포하고, 스키마가 배포된 후 대상 서버에 이상이 있는지 확인합니다.

    ![스키마 배포](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dma-schema-deploy.png)

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Microsoft.DataMigration 리소스 공급자 등록

1. Azure Portal에 로그인하고, **모든 서비스**를 선택한 다음, **구독**을 선택합니다.

   ![포털 구독 표시](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/portal-select-subscription1.png)

2. Azure Database Migration Service의 인스턴스를 만들 구독을 선택한 다음 **리소스 공급자**를 선택합니다.

    ![리소스 공급자 보기](media/tutorial-sql-server-to-azure-sql-online/portal-select-resource-provider.png)

3. 마이그레이션을 검색한 다음 **Microsoft.DataMigration**의 오른쪽에서 **등록**을 선택합니다.

    ![리소스 공급자 등록](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/portal-register-resource-provider.png)    

## <a name="create-an-instance"></a>인스턴스 만들기

1. Azure Portal에서 **+ 리소스 만들기**를 선택하고, Azure Database Migration Service를 검색한 다음, 드롭다운 목록에서 **Azure Database Migration Service**를 선택합니다.

    ![Azure Marketplace](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/portal-marketplace.png)

2. **Azure Database Migration Service** 화면에서 **만들기**를 선택합니다.

    ![Azure Database Migration Service 인스턴스 만들기](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-create1.png)
  
3. **Migration Service 만들기** 화면에서 서비스, 구독, 신규 또는 기존 리소스 그룹의 이름을 지정합니다.

4. Azure Database Migration Service의 인스턴스를 만들 위치를 선택합니다. 

5. 기존 가상 네트워크를 선택 하거나 새 가상 네트워크를 만드세요.

    가상 네트워크는 원본 SQL Server 및 대상 Azure SQL Database 인스턴스에 대한 액세스 권한을 Azure Database Migration Service 제공 합니다.

    Azure Portal에서 가상 네트워크를 만드는 방법에 대한 자세한 내용은 [Azure Portal를 사용 하 여 가상 네트워크 만들기](https://aka.ms/DMSVnet)문서를 참조 하세요.

6. 이 온라인 마이그레이션의 가격 책정 계층을 선택합니다. 프리미엄 가격 책정 계층을 선택해야 합니다.

    비용 및 가격 책정 계층에 대한 자세한 내용은 [가격 책정 페이지](https://aka.ms/dms-pricing)를 참조하세요.

     ![Azure Database Migration Service 인스턴스 설정 구성](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-settings3.png)

7. **만들기**를 선택하여 서비스를 만듭니다.

## <a name="create-a-migration-project"></a>마이그레이션 프로젝트 만들기

서비스가 생성된 후 Azure Portal에서 서비스를 찾아 연 다음, 새로운 마이그레이션 프로젝트를 만듭니다.

1. Azure Portal에서 **모든 서비스**를 선택하고, Azure Database Migration Service를 검색하고 나서, **Azure Database Migration Services**를 선택합니다.

      ![Azure Database Migration Service의 모든 인스턴스 찾기](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-search.png)

2. **Azure Database Migration Services** 화면에서 방금 만든 Azure Database Migration Service 인스턴스의 이름을 검색하고 인스턴스를 선택합니다.

     ![Azure Database Migration Service 인스턴스 찾기](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-instance-search.png)

3. **+ 새 마이그레이션 프로젝트**를 선택합니다.
4. **새 마이그레이션 프로젝트** 화면에서 프로젝트 이름을 지정하고, **원본 서버 유형** 텍스트 상자에서 **AWS RDS for SQL Server**를 선택하고, **대상 서버 유형** 텍스트 상자에서 **Azure SQL Database**를 선택합니다.

    > [!NOTE]
    > 대상 서버 유형에 대해 Azure SQL Database 싱글톤 데이터베이스 및 Azure SQL Database 관리형 인스턴스로 마이그레이션하려면 **Azure SQL Database**를 선택합니다.

5. **활동 유형 선택** 섹션에서 **온라인 데이터 마이그레이션**을 선택합니다.

    > [!IMPORTANT]
    > **온라인 데이터 마이그레이션**을 선택합니다. 이 시나리오에서는 오프라인 마이그레이션이 지원되지 않습니다.

    ![Database Migration Service 프로젝트 만들기](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-create-project4.png)

    > [!NOTE]
    > 또는 **프로젝트만 만들기**를 선택하여 지금 마이그레이션 프로젝트를 만들고, 나중에 마이그레이션을 실행할 수도 있습니다.

6. **저장**을 선택합니다.

7. **작업 만들기 및 실행**을 선택하여 프로젝트를 만들고 마이그레이션 작업을 실행합니다.

    ![Database Migration Service 작업 만들기 및 실행](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-create-and-run-activity1.png)

## <a name="specify-source-details"></a>원본 세부 정보 지정

1. **마이그레이션 원본 세부 정보** 화면에서 원본 SQL Server 인스턴스에 대한 연결 세부 정보를 지정합니다.

    원본 SQL Server 인스턴스 이름에 정규화된 도메인 이름(FQDN)을 사용하도록 합니다.

2. 원본 서버에 신뢰할 수 있는 인증서가 설치되지 않은 경우 **서버 인증서 신뢰** 확인란을 선택합니다.

    신뢰할 수 있는 인증서가 설치되어 있지 않으면 인스턴스가 시작될 때 SQL Server에서 자체 서명 인증서를 생성합니다. 이 인증서는 클라이언트 연결에 대한 자격 증명을 암호화하는 데 사용됩니다.

    > [!CAUTION]
    > 자체 서명 인증서를 사용하여 암호화된 SSL 연결은 강력한 보안을 제공하지 않습니다. 중간자 공격(man-in-the-middle)을 받기 쉽습니다. 프로덕션 환경이나 인터넷에 연결된 서버에서 자체 서명 인증서를 사용하는 SSL을 신뢰해서는 안 됩니다.

   ![원본 세부 정보](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-source-details3.png)

## <a name="specify-target-details"></a>대상 세부 정보 지정

1. **저장**을 선택한 다음, **마이그레이션 대상 세부 정보** 화면에서 대상 Azure SQL Database 서버에 대한 연결 세부 정보를 지정합니다. 대상은 DMA를 통해 **AdventureWorks2012** 스키마가 배포된 미리 프로비저닝된 Azure SQL Database입니다.

    ![대상 선택](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-select-target3.png)

2. **저장**을 선택한 다음, **대상 데이터베이스에 매핑** 화면에서 마이그레이션하기 위해 원본 및 대상 데이터베이스를 매핑합니다.

    대상 데이터베이스의 이름이 원본 데이터베이스와 동일하면 Azure Database Migration Service는 기본적으로 대상 데이터베이스를 선택합니다.

    ![대상 데이터베이스에 매핑](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-map-targets-activity4.png)

3. **저장**을 선택하고, **테이블 선택** 화면에서 테이블 목록을 확장하한 다음, 영향받는 필드 목록을 검토합니다.

    Azure Database Migration Service는 대상 Azure SQL Database 인스턴스에 있는 모든 빈 원본 테이블을 자동으로 선택합니다. 이미 데이터를 포함하고 있는 테이블을 다시 마이그레이션하려면 이 화면에서 테이블을 명시적으로 선택해야 합니다.

    ![테이블 선택](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-configure-setting-activity4.png)

4. 다음 **고급 온라인 마이그레이션 설정**을 지정한 후 **저장**을 선택합니다.

    | 설정 | Description |
    | ------------- | ------------- |
    | **동시에 로드할 수 있는 최대 테이블 수** | 마이그레이션하는 동안 DMS가 병렬로 실행하는 테이블 수를 지정합니다. 기본값은 5이며, POC 마이그레이션에 따라 특정 마이그레이션 요구 사항에 맞는 최적의 값으로 설정할 수 있습니다. |
    | **원본 테이블이 잘리는 경우** | 마이그레이션하는 동안 DMS가 대상 테이블을 자를지 여부를 지정합니다. 하나 이상의 테이블이 마이그레이션 프로세스의 일환으로 잘리는 경우 이 설정이 도움이 될 수 있습니다. |
    | **큰 개체(LOB) 데이터에 대한 설정 구성** | DMS가 LOB 데이터를 무제한으로 마이그레이션하게 할 것인지 아니면 마이그레이션되는 LOB 데이터를 특정 크기로 제한할 것인지 지정합니다.  마이그레이션되는 LOB 데이터가 제한되면 제한을 초과하는 LOB 데이터는 잘립니다. 프로덕션 마이그레이션의 경우 데이터 손실을 방지하기 위해 **무제한 LOB 크기 허용**을 선택하는 것이 좋습니다. 무제한 LOB 크기를 허용하도록 지정할 때, 성능 향상을 위해 **LOB 크기가 지정된 크기(KB)보다 작은 경우 LOB 데이터를 단일 블록에 마이그레이션** 확인란을 선택합니다. |

    ![고급 온라인 마이그레이션 설정 지정](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-advanced-online-migration-settings.png)

5. **저장**을 선택하고, **마이그레이션 요약** 화면의 **작업 이름** 텍스트 상자에서 마이그레이션 작업의 이름을 지정한 다음, 요약을 검토하여 원본 및 대상 세부 정보가 이전에 지정한 내용과 일치하는지 확인합니다.

    ![마이그레이션 요약](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-migration-summary.png)

## <a name="run-the-migration"></a>마이그레이션 실행

* **마이그레이션 실행**을 선택합니다.

    마이그레이션 작업 창이 나타나고, 작업 **상태**는 **초기화 중**입니다.

    ![작업 상태 - 초기화 중](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-activity-status2.png)

## <a name="monitor-the-migration"></a>마이그레이션 모니터링

1. 마이그레이션 작업 화면에서 **새로 고침**을 선택하여 마이그레이션 **상태**가 **실행 중**으로 표시될 때까지 디스플레이를 업데이트합니다.

2. 특정 데이터베이스를 클릭하여 **전체 데이터 로드** 및 **증분 데이터 동기화** 작업에 대한 마이그레이션 상태로 이동합니다.

    ![작업 상태 - 진행 중](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-activity-in-progress.png)

## <a name="perform-migration-cutover"></a>마이그레이션 중단 수행

초기 전체 로드가 완료되면 데이터베이스가 **중단 준비 완료**로 표시됩니다.

1. 데이터베이스 마이그레이션을 완료할 준비가 되면 **중단 시작**을 선택합니다.

    ![중단 시작](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-start-cutover.png)

2. 원본 데이터베이스로 들어오는 모든 트랜잭션을 중지해야 합니다. **보류 중인 변경 내용** 카운터가 **0**으로 표시될 때까지 기다립니다.
3. **확인**, **적용**을 차례로 선택합니다.
4. 데이터베이스 마이그레이션 상태가 **완료됨**으로 표시되면 애플리케이션을 새 대상 Azure SQL Database에 연결합니다.

    ![작업 상태 - 완료됨](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-activity-completed.png)

## <a name="next-steps"></a>다음 단계

* Azure SQL Database로 온라인 마이그레이션을 수행할 때 발생하는 알려진 문제 및 제한 사항에 대한 자세한 내용은 [Azure SQL Database 온라인 마이그레이션의 알려진 문제 및 해결 방법](known-issues-azure-sql-online.md) 문서를 참조하세요.
* Azure Database Migration Service에 대한 자세한 내용은 [Azure Database Migration Service란?](https://docs.microsoft.com/azure/dms/dms-overview) 문서를 참조하세요.
* Azure SQL Database에 대한 자세한 내용은 [Azure SQL Database 서비스란?](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview) 문서를 참조하세요.
* Azure SQL Database 관리형 인스턴스에 대한 자세한 내용은 [Azure SQL Database Managed Instance](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-index) 페이지를 참조하세요.
