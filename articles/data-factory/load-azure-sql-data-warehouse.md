---
title: Azure Data Factory를 사용하여 Azure SQL Data Warehouse에 데이터 로드 | Microsoft Docs
description: Azure Data Factory를 사용하여 Azure SQL Data Warehouse로 데이터 복사
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 06/22/2018
ms.author: jingwang
ms.openlocfilehash: 6a7e0a27d3cda4193a04467d541f851a9e57fa46
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60549088"
---
# <a name="load-data-into-azure-sql-data-warehouse-by-using-azure-data-factory"></a>Azure Data Factory를 사용하여 Azure SQL Data Warehouse에 데이터 로드

[Azure SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md)는 거대한 양의 관계형 및 비관계형 데이터를 처리할 수 있는 클라우드 기반 규모 확장 데이터베이스입니다. SQL Data Warehouse는 엔터프라이즈 데이터 웨어하우스 워크로드에 최적화된 MPP(대규모 병렬 처리) 아키텍처를 기반으로 합니다. 스토리지를 확장하고 개별적으로 계산할 수 있는 클라우드 탄력성을 유연하게 제공합니다.

이제는 Azure SQL Data Warehouse를 시작하는 것이 Azure Data Factory를 사용할 때보다 더 쉽습니다. Azure Data Factory는 완전히 관리되는 클라우드 기반 데이터 통합 서비스입니다. 분석 솔루션을 빌드할 때 서비스를 사용하여 SQL Data Warehouse를 기존 시스템의 데이터로 채우면 시간을 절약할 수 있습니다.

Azure Data Factory를 사용하여 Azure SQL Data Warehouse로 데이터를 로드하면 다음과 같은 이점이 있습니다.

* **간편한 설정**: 스크립팅이 필요 없는 직관적인 5 단계 마법사.
* **다양 한 데이터 저장소 지원**: 다양 한 온-프레미스 및 클라우드 기반 데이터 저장소에 대 한 기본 제공 지원 합니다. 자세한 목록은 [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats) 표를 참조하세요.
* **보안 및 규정 준수**: 데이터가는 HTTPS 또는 ExpressRoute를 통해 전송 됩니다. 글로벌 서비스가 제공되므로 데이터가 지리적 경계를 벗어나지 않습니다.
* **PolyBase를 사용 하 여 뛰어난된 성능을**: Polybase는 Azure SQL Data Warehouse로 데이터 이동에 대 한 가장 효율적인 방법입니다. 스테이징 Blob 기능을 사용하여 Azure Blob Storage 및 Data Lake Store를 포함하여 모든 유형의 데이터 스토리지에서 높은 로드 속도를 얻습니다. (Polybase는 기본적으로 Azure Blob Storage 및 Data Lake Store를 지원합니다.) 자세한 내용은 [복사 작업 성능](copy-activity-performance.md)을 참조하세요.

이 문서에서는 Data Factory 데이터 복사 도구를 사용하여 _Azure SQL Database의 데이터를 Azure SQL Data Warehouse로 로드_하는 방법을 설명합니다. 다른 데이터 저장소 유형에서 데이터를 복사할 때도 이와 유사한 단계를 따를 수 있습니다.

> [!NOTE]
> 자세한 내용은 [Azure Data Factory를 사용하여 Azure SQL Data Warehouse 간에 데이터 복사](connector-azure-sql-data-warehouse.md)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

* Azure 구독: Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/)을 만듭니다.
* Azure SQL Data Warehouse: 데이터 웨어하우스 SQL 데이터베이스에서 복사 된 데이터를 보유 합니다. Azure SQL Data Warehouse가 아직 없는 경우 [SQL Data Warehouse 만들기](../sql-data-warehouse/sql-data-warehouse-get-started-tutorial.md)의 지침을 참조하세요.
* Azure SQL Database: 이 자습서는 Adventure Works LT 샘플 데이터를 사용 하 여 Azure SQL database에서 데이터를 복사합니다. [Azure SQL 데이터베이스 만들기](../sql-database/sql-database-get-started-portal.md)의 지침을 따라 SQL 데이터베이스를 만들 수 있습니다. 
* Azure 저장소 계정: Azure 저장소로 사용 되는 _준비_ 대량 복사 작업에서 blob입니다. Azure 저장소 계정이 없는 경우 [저장소 계정 만들기](../storage/common/storage-quickstart-create-account.md)의 지침을 참조하세요.

## <a name="create-a-data-factory"></a>데이터 팩터리를 만듭니다.

1. 왼쪽 메뉴에서 **리소스 만들기** > **데이터 + 분석** > **Data Factory**를 차례로 선택합니다. 
   
   !["새로 만들기" 창에서 데이터 팩터리 선택](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)

2. **새 데이터 팩터리** 페이지에서 다음 그림에 표시된 필드의 값을 제공합니다.
      
   ![새 데이터 팩터리 페이지](./media/load-azure-sql-data-warehouse/new-azure-data-factory.png)
 
    * **이름**: Azure Data Factory의 전역적으로 고유 이름을 입력합니다. "데이터 팩터리 이름 \"LoadSQLDWDemo\"를 사용할 수 없습니다" 오류가 발생하면 데이터 팩터리에 다른 이름을 입력합니다. 예를 들어 _**yourname**_**ADFTutorialDataFactory**라는 이름을 사용할 수 있습니다. 데이터 팩터리를 다시 만들어 봅니다. 데이터 팩터리 아티팩트에 대한 명명 규칙은 [데이터 팩터리 명명 규칙](naming-rules.md)을 참조하세요.
    * **구독**: 데이터 팩터리를 만들 Azure 구독을 선택합니다. 
    * **리소스 그룹**: 드롭다운 목록에서 기존 리소스 그룹을 선택하거나 **새로 만들기** 옵션을 선택하고 리소스 그룹의 이름을 입력합니다. 리소스 그룹에 대한 자세한 내용은 [리소스 그룹을 사용하여 Azure 리소스 관리](../azure-resource-manager/resource-group-overview.md)를 참조하세요.  
    * **버전**: **V2**를 선택합니다.
    * **위치**: 데이터 팩터리의 위치를 선택합니다. 지원되는 위치만 드롭다운 목록에 표시됩니다. 데이터 팩터리에서 사용되는 데이터 저장소가 다른 위치 및 지역에 있어도 됩니다. 이러한 데이터 저장소는 Azure Data Lake Store, Azure Storage, Azure SQL Database 등을 포함합니다.

3. **만들기**를 선택합니다.
4. 만들기가 완료되면 데이터 팩터리로 이동합니다. 다음 그림과 같이 **데이터 팩터리** 홈페이지가 표시됩니다.
   
   ![데이터 팩터리 홈페이지](./media/load-azure-sql-data-warehouse/data-factory-home-page.png)

   **작성 및 모니터링** 타일을 선택하여 별도의 탭에서 데이터 통합 애플리케이션을 시작합니다.

## <a name="load-data-into-azure-sql-data-warehouse"></a>Azure SQL Data Warehouse에 데이터 로드

1. **시작** 페이지에서 **데이터 복사** 타일을 선택하여 데이터 복사 도구를 시작합니다.

   ![데이터 복사 도구 타일](./media/load-azure-sql-data-warehouse/copy-data-tool-tile.png)
1. **속성** 페이지에서 **작업 이름** 필드를 **CopyFromSQLToSQLDW**로 지정하고 **다음**을 선택합니다.

    ![속성 페이지](./media/load-azure-sql-data-warehouse/copy-data-tool-properties-page.png)

1. **원본 데이터 저장소** 페이지에서 다음 단계를 완료합니다.

    a. **+ 새 연결 만들기**를 클릭합니다.

    ![원본 데이터 저장소 페이지](./media/load-azure-sql-data-warehouse/new-source-linked-service.png)

    b. 갤러리에서 **Azure SQL Database**를 선택하고 **계속**을 선택합니다. 검색 상자에 "SQL"을 입력하여 커넥터를 필터링할 수 있습니다.

    ![Azure SQL DB 선택](./media/load-azure-sql-data-warehouse/select-azure-sql-db-source.png)

    다. 에 **새 연결 된 서비스** 페이지, DB 이름과 서버 이름 드롭다운 목록에서 선택 하 고, 사용자 이름 및 암호를 지정 합니다. **연결 테스트**를 클릭하여 설정의 유효성을 검사한 다음, **마침**을 선택합니다.
   
    ![Azure SQL DB 구성](./media/load-azure-sql-data-warehouse/configure-azure-sql-db.png)

    d. 새로 만든 연결된 서비스를 원본으로 선택하고 **다음**을 클릭합니다.

    ![원본 연결된 서비스 선택](./media/load-azure-sql-data-warehouse/select-source-linked-service.png)

1. **데이터를 복사할 테이블 선택 또는 사용자 지정 쿼리 사용** 페이지에서 **SalesLT**를 입력하여 테이블을 필터링합니다. 복사에 모든 테이블을 사용하도록 **(모두 선택)** 상자를 선택한 후 **다음**을 선택합니다. 

    ![원본 테이블 선택](./media/load-azure-sql-data-warehouse/select-source-tables.png)

1. **대상 데이터 저장소** 페이지에서 다음 단계를 완료합니다.

    a. **+ 새 연결 만들기**를 클릭하여 연결을 추가합니다.

    ![싱크 데이터 저장소 페이지](./media/load-azure-sql-data-warehouse/new-sink-linked-service.png)

    b. 갤러리에서 **Azure SQL Data Warehouse**를 선택하고 **다음**을 선택합니다.

    ![Azure SQL DW 선택](./media/load-azure-sql-data-warehouse/select-azure-sql-dw-sink.png)

    다. 에 **새 연결 된 서비스** 페이지, DB 이름과 서버 이름 드롭다운 목록에서 선택 하 고, 사용자 이름 및 암호를 지정 합니다. **연결 테스트**를 클릭하여 설정의 유효성을 검사한 다음, **마침**을 선택합니다.
   
    ![Azure SQL DW 구성](./media/load-azure-sql-data-warehouse/configure-azure-sql-dw.png)

    d. 새로 만든 연결된 서비스를 싱크로 선택하고 **다음**을 클릭합니다.

    ![연결된 싱크 서비스 선택](./media/load-azure-sql-data-warehouse/select-sink-linked-service.png)

1. **테이블 매핑** 페이지에서 콘텐츠를 검토하고, **다음**을 선택합니다. 지능형 테이블 매핑이 표시됩니다. 원본 테이블은 테이블 이름에 따라 대상 테이블에 매핑됩니다. 원본 테이블이 대상에 없으면 기본적으로 Azure Data Factory는 같은 이름으로 대상 테이블을 만듭니다. 기존 대상 테이블에 원본 테이블을 매핑할 수도 있습니다. 

   > [!NOTE]
   > SQL Data Warehouse 싱크에 대한 자동 테이블 만들기는 SQL Server 또는 Azure SQL Database가 원본일 때 적용됩니다. 다른 원본 데이터 저장소에서 데이터를 복사하는 경우, 데이터 복사를 실행하기 전에 싱크 Azure SQL Data Warehouse에서 스키마를 미리 만들어야 합니다.

   ![테이블 매핑 페이지](./media/load-azure-sql-data-warehouse/table-mapping.png)

1. **스키마 매핑** 페이지에서 콘텐츠를 검토하고, **다음**을 선택합니다. 지능형 테이블 매핑은 열 이름을 기반으로 합니다. 데이터 팩터리에서 테이블을 자동으로 만들도록 하는 경우 원본 및 대상 저장소 간에 비호환성 문제가 있을 때 데이터 형식 변환이 발생할 수 있습니다. 원본 및 대상 열 간에 지원되지 않는 데이터 형식 변환이 있는 경우 해당 테이블 옆에 오류 메시지가 표시됩니다.

    ![스키마 매핑 페이지](./media/load-azure-sql-data-warehouse/schema-mapping.png)

1. **설정** 페이지에서 다음 단계를 완료합니다.

    a. **준비 설정** 섹션에서 **+ 새로 만들기**를 클릭하여 준비 저장소를 새로 만듭니다. 저장소는 PolyBase를 사용하여 SQL Data Warehouse에 로드하기 전에, 데이터를 준비하는 데 사용됩니다. 복사가 완료되면 Azure Storage의 중간 데이터는 자동으로 정리됩니다. 

    ![준비 구성](./media/load-azure-sql-data-warehouse/configure-staging.png)

    b. **새로 연결된 서비스** 페이지에서 저장소 계정을 선택하고 **마침**을 선택합니다.
   
    ![Azure Storage 구성](./media/load-azure-sql-data-warehouse/configure-blob-storage.png)

    다. **고급 설정** 섹션에서 **형식 기본값 사용** 옵션의 선택을 취소한 다음, **다음**을 선택합니다.

    ![PolyBase 구성](./media/load-azure-sql-data-warehouse/configure-polybase.png)

1. **요약** 페이지에서 설정을 검토하고, **다음**을 선택합니다.

    ![요약 페이지](./media/load-azure-sql-data-warehouse/summary-page.png)
1. **배포 페이지**에서 **모니터**를 선택하여 파이프라인(작업)을 모니터링합니다.

    ![배포 페이지](./media/load-azure-sql-data-warehouse/deployment-page.png)
1. 왼쪽의 **모니터** 탭이 자동으로 선택됩니다. **작업** 열에는 활동 실행 세부 정보를 보고 파이프라인을 다시 실행하기 위한 링크가 있습니다. 

    ![파이프라인 실행 모니터링](./media/load-azure-sql-data-warehouse/pipeline-monitoring.png)
1. 파이프라인 실행과 연결된 활동 실행을 보려면 **작업** 열에서 **활동 실행 보기** 링크를 선택합니다. 파이프라인 실행 보기로 전환하려면 위쪽의 **파이프라인** 링크를 선택합니다. **새로 고침**을 선택하여 목록을 새로 고칩니다. 

    ![작업 실행 모니터링](./media/load-azure-sql-data-warehouse/activity-monitoring.png)

1. 각 복사 작업의 실행 세부 정보를 모니터링하려면 작업 모니터링 보기의 **작업** 아래에서 **세부 정보** 링크를 선택합니다. 원본에서 싱크로 복사되는 데이터 볼륨, 데이터 처리량, 해당 기간의 실행 단계, 사용되는 구성 등의 세부 정보를 모니터링할 수 있습니다.

    ![작업 실행 세부 정보 모니터링](./media/load-azure-sql-data-warehouse/monitor-activity-run-details.png)

## <a name="next-steps"></a>다음 단계

Azure SQL Data Warehouse 지원에 대한 자세한 내용은 다음 문서를 참조하세요. 

> [!div class="nextstepaction"]
>[Azure SQL Data Warehouse 커넥터](connector-azure-sql-data-warehouse.md)
