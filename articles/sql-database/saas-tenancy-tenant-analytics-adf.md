---
title: Azure SQL Data Warehouse를 사용하여 테넌트 데이터베이스에 대해 분석 쿼리 실행 | Microsoft Docs
description: Azure SQL Database, SQL Data Warehouse, Azure Data Factory 또는 Power BI에서 추출된 데이터를 사용하는 교차 테넌트 분석 쿼리입니다.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anumjs
ms.author: anjangsh
ms.reviewer: MightyPen, sstein
manager: craigg
ms.date: 12/18/2018
ms.openlocfilehash: a658e2fe32ec95dfabad54684a0c9095af7a341d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61485082"
---
# <a name="explore-saas-analytics-with-azure-sql-database-sql-data-warehouse-data-factory-and-power-bi"></a>Azure SQL Database, SQL Data Warehouse, Data Factory 및 Power BI를 사용한 SaaS 분석 탐색

이 자습서에서는 통합형 분석 시나리오를 처음부터 끝까지 살펴봅니다. 시나리오는 테넌트 데이터에 대한 분석을 통해 소프트웨어 공급 업체가 스마트한 결정을 내리는 데 도움을 받는 방법을 보여 줍니다. 각 테넌트 데이터베이스에서 추출된 데이터를 사용하여 분석을 통해 샘플 Wingtip Tickets SaaS 애플리케이션을 비롯한 테넌트 동작을 살펴보게 됩니다. 이 시나리오는 다음과 같이 3단계로 구성됩니다. 

1.  각 테넌트 데이터베이스에서 분석 저장소로 **데이터를 추출**합니다. 이 경우는 SQL Data Warehouse입니다.
2.  분석 처리를 위해 **추출된 데이터를 최적화**합니다.
3.  **비즈니스 인텔리전스** 도구를 사용하여 의사 결정에 도움이 되는 유용한 인사이트를 얻습니다. 

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> - 로딩을 위한 테넌트 분석 저장소를 만듭니다.
> - ADF(Azure Data Factory)를 사용하여 각 테넌트 데이터베이스에서 분석 데이터 웨어하우스로 데이터를 추출합니다.
> - 추출된 데이터 최적화하기(스타 스키마로 재구성하기).
> - 분석 데이터 웨어하우스를 쿼리합니다.
> - 데이터 시각화를 위해 Power BI를 사용하여 테넌트 데이터의 추세를 파악하고 개선을 위한 권장 사항 도출하기.

![architectureOverView](media/saas-tenancy-tenant-analytics/adf_overview.png)

## <a name="analytics-over-extracted-tenant-data"></a>추출된 테넌트 데이터에 대한 분석

SaaS 애플리케이션은 클라우드에서 방대한 양의 테넌트 데이터를 잠재적으로 보유합니다. 이 데이터는 애플리케이션의 작동 및 사용과 테넌트의 동작에 대한 풍부한 인사이트를 제공합니다. 이러한 인사이트는 기능 개발, 사용성 개선, 앱 및 플랫폼의 기타 투자에 대한 방향성을 제공할 수 있습니다.

모든 데이터가 하나의 다중 테넌트 데이터베이스에 저장되어 있다면 모든 테넌트가 손쉽게 데이터에 액세스할 수 있습니다. 그러나 데이터가 수천 개의 데이터베이스에 대규모로 분산되어 있다면 액세스가 복잡해집니다. 이러한 복잡성을 해결하는 한 가지 방법은 데이터를 분석 데이터베이스 또는 데이터 웨어하우스로 추출하여 쿼리하는 것입니다.

이 자습서에서는 Wingtip Tickets 애플리케이션에 대한 통합형 분석 시나리오를 제공합니다. 첫째, [ADF(Azure Data Factory)](../data-factory/introduction.md)는 각 테넌트 데이터베이스에서 티켓 판매량 및 관련 데이터를 추출하는 오케스트레이션 도구로 사용됩니다. 이 데이터는 분석 저장소의 준비 테이블로 로드됩니다. 분석 저장소로 SQL Database나 SQL Data Warehouse를 사용할 수 있습니다. 이 자습서에서는 [SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-overview-what-is)를 분석 저장소로 사용합니다.

다음으로, 추출된 데이터를 일련의 [스타 스키마](https://www.wikipedia.org/wiki/Star_schema) 테이블로 변환해 로드합니다. 테이블은 중앙의 팩트 테이블과 관련 차원 테이블로 이루어집니다.

- 중앙의 팩트 테이블은 스타 스키마로 되어 있고 티켓 데이터를 포함합니다.
- 차원 데이터는 행사장, 이벤트, 고객, 구입 날짜와 같은 데이터를 포함합니다.

중앙 테이블과 차원 테이블을 함께 사용하면 효율적인 분석 처리가 가능해집니다. 아래의 그림에서 이 자습서에서 사용하고 있는 스타 스키마를 볼 수 있습니다.
 
![architectureOverView](media/saas-tenancy-tenant-analytics/starschematables.JPG)

마지막으로 스타 스키마 테이블을 대상으로 쿼리합니다. 쿼리 결과는 Power BI를 사용하여 시각적으로 표시되어 테넌트 동작과 테넌트가 애플리케이션을 사용하는 방식을 확인할 수 있습니다. 이 스타 스키마를 사용하여 공개하는 쿼리를 실행합니다.

- 누가 어느 행사장에서 어떤 티켓을 구입하는가
- 티켓 판매량의 패턴 및 추세입니다.
- 각 행사장의 상대적 인기도.

이 자습서에서는 Wingtip Tickets 데이터로부터 창출할 수 있는 기본적인 인사이트를 제공합니다. 각 행사장이 서비스를 사용하는 방법을 이해하면 Wingtip Tickets 공급 업체가 예를 들어 다소 활동적인 행사장을 대상으로 다양한 서비스 계획을 고려하게 할 수 있습니다. 

## <a name="setup"></a>설정

### <a name="prerequisites"></a>필수 조건

> [!NOTE]
> 이 자습서에서는 현재 미리 보기가 제한된 Azure Data Factory의 기능을 사용합니다(연결된 서비스 매개 변수화). 이 자습서를 수행하려는 경우 [여기에](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxrVywox1_tHk9wgd5P8SVJUNlFINjNEOElTVFdMUEREMjVVUlJCUDdIRyQlQCN0PWcu) 구독 ID를 제공합니다. 구독을 사용하도록 설정하는 즉시 확인을 전송합니다.

이 자습서를 수행하려면 다음 필수 조건이 충족되었는지 확인합니다.
- Wingtip Tickets SaaS Database Per Tenant 애플리케이션이 배포되어 있어야 합니다. 5분 이내에 배포하려면 [Wingtip SaaS 애플리케이션 배포 및 탐색](saas-dbpertenant-get-started-deploy.md)을 참조하세요.
- Wingtip Tickets SaaS Database Per Tenant 스크립트와 애플리케이션 [소스 코드](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant/)를 GitHub에서 다운로드해야 합니다. 다운로드 지침을 참조하세요. 콘텐츠를 추출하기 전에 *zip 파일의 차단을 해제*해야 합니다.
- Power BI Desktop이 설치되어 있어야 합니다. [Power BI Desktop 다운로드](https://powerbi.microsoft.com/downloads/).
- 추가 테넌트 배치가 프로비전되어 있어야 합니다. [**테넌트 프로비전 자습서**](saas-dbpertenant-provision-and-catalog.md)를 참조하세요.

### <a name="create-data-for-the-demo"></a>데모를 위한 데이터 만들기

이 자습서는 티켓 판매량 데이터에 대한 분석을 탐색합니다. 이 단계에서는 모든 테넌트의 티켓 데이터를 생성합니다. 다음 단계에서 이 데이터는 분석을 위해 추출됩니다. (앞서 설명한 대로) 충분한 데이터가 있어 다른 티켓 구매 패턴의 범위를 공개할 수 있도록 _테넌트의 일괄 처리를 프로비전했는지 확인합니다_.

1. PowerShell ISE에서 *…\Learning Modules\Operational Analytics\Tenant Analytics DW\Demo-TenantAnalyticsDW.ps1*을 열고 다음 값을 설정합니다.
    - **$DemoScenario** = **1** 모든 행사장에서 이벤트 티켓 구입
2. **F5** 키를 눌러 스크립트를 실행하고 모든 행사장에 대한 티켓 구입 기록을 만듭니다. 20개 테넌트를 사용하여 스크립트는 티켓 수만 장을 생성하는 데 10분 이상 걸릴 수 있습니다.

### <a name="deploy-sql-data-warehouse-data-factory-and-blob-storage"></a>SQL Data Warehouse, Data Factory 및 Blob Storage 배포 
Wingtip Tickets 앱에서 테넌트의 트랜잭션 데이터는 많은 데이터베이스에 배포됩니다. ADF(Azure Data Factory)는 데이터 웨어하우스로 이 데이터의 ELT(추출, 로드, 변환)를 오케스트레이션하는 데 사용됩니다. SQL Data Warehouse로 데이터를 가장 효율적으로 로드하려면 ADF는 데이터를 중간 BLOB 파일로 추출한 다음, [PolyBase](https://docs.microsoft.com/azure/sql-data-warehouse/design-elt-data-loading)를 사용하여 데이터를 데이터웨어하우스로 로드합니다.   

이 단계에서는 자습서에서 사용되는 추가 리소스를 배포합니다. 즉, _tenantanalytics_이라는 SQL Data Warehouse, _dbtodwload-\<user\>_ 라는 Azure Data Factory 및 _wingtipstaging\<user\>_ 라는 Azure 저장소 계정입니다. 저장소 계정은 데이터 웨어하우스에 로드하기 전에 추출된 데이터 파일을 BLOB으로 임시 보유하는 데 사용됩니다. 또한 이 단계에서는 데이터 웨어하우스 스키마를 배포하고 ELT 프로세스를 오케스트레이션하는 ADF 파이프라인을 정의합니다.
1. PowerShell ISE에서 *…\Learning Modules\Operational Analytics\Tenant Analytics DW\Demo-TenantAnalyticsDW.ps1*을 열고 다음을 설정합니다.
    - **$DemoScenario** = **2** 테넌트 분석 데이터 웨어하우스, Blob Storage 및 데이터 팩터리 배포 
1. **F5** 키를 눌러 데모 스크립트를 실행하고 Azure 리소스를 배포합니다. 

이제 배포한 Azure 리소스를 검토합니다.
#### <a name="tenant-databases-and-analytics-store"></a>테넌트 데이터베이스 및 분석 저장소
[SSMS(SQL Server Management Studio)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)를 사용하여 **tenants1-dpt-&lt;user&gt;** 및 **catalog-dpt-&lt;user&gt;** 서버에 연결합니다. &lt;user&gt;를 앱을 배포할 때 사용한 값으로 바꿉니다. 로그인을 사용 하 여 = *개발자* 및 암호 = *P\@ssword1*합니다. 자세한 내용은 [입문용 자습서](saas-dbpertenant-wingtip-app-overview.md)를 참조하세요.

![SSMS에서 SQL Database 서버에 연결](media/saas-tenancy-tenant-analytics/ssmsSignIn.JPG)

개체 탐색기에서:

1. *tenants1-dpt-&lt;user&gt;* 서버를 확장합니다.
1. Databases 노드를 확장하여 테넌트 데이터베이스 목록을 확인합니다.
1. *catalog-dpt-&lt;user&gt;* 서버를 확장합니다.
1. 다음 개체가 포함된 저장소 분석이 표시되는지 확인합니다.
    1. 테이블 **raw_Tickets**, **raw_Customers**, **raw_Events** 및 **raw_Venues**은 테넌트 데이터베이스에서 추출된 원시 데이터를 보유합니다.
    1. 스타 스키마 테이블은 **fact_Tickets**, **dim_Customers**, **dim_Venues**, **dim_Events**, **dim_Dates**입니다.
    1. 저장된 프로시저 **sp_transformExtractedData**는 데이터를 변환하여 스타 스키마 테이블에 로드하는 데 사용됩니다.

![DWtables](media/saas-tenancy-tenant-analytics/DWtables.JPG)

#### <a name="blob-storage"></a>Blob 저장소
1. [Azure Portal](https://ms.portal.azure.com)에서 애플리케이션을 배포하는 데 사용한 리소스 그룹으로 이동합니다. **wingtipstaging\<user\>** 이라는 저장소 계정이 추가되었는지 확인합니다.

   ![DWtables](media/saas-tenancy-tenant-analytics/adf-staging-storage.PNG)

1. **wingtipstaging\<user\>** 저장소 계정을 클릭하여 존재하는 개체를 탐색합니다.
1. **Blob** 타일 클릭
1. 컨테이너 **configfile** 클릭
1. **configfile**이 **TableConfig.json**라는 JSON 파일을 포함하는지 확인합니다. 이 파일에는 원본 및 대상 테이블 이름, 열 이름 및 추적기 열 이름이 포함됩니다.

#### <a name="azure-data-factory-adf"></a>ADF(Azure Data Factory)
리소스 그룹의 [Azure Portal](https://ms.portal.azure.com)에서 _dbtodwload-\<user\>_ 라는 Azure Data Factory이 추가되었는지 확인합니다. 

 ![adf_portal](media/saas-tenancy-tenant-analytics/adf-data-factory-portal.png)

이 섹션에서는 만든 데이터 팩터리를 탐색합니다. 아래 단계를 따라 데이터 팩터리를 시작합니다.
1. 포털에서 **dbtodwload-\<user\>** 라는 데이터 팩터리 포털을 클릭합니다.
2. **작성 및 모니터링** 타일을 클릭하여 별도의 탭에서 Data Factory 디자이너를 실행합니다. 

## <a name="extract-load-and-transform-data"></a>데이터 ELT(추출, 로드, 변환)
Azure Data Factory는 데이터의 추출, 로드 및 변환을 오케스트레이션하는 데 사용됩니다. 이 자습서에서는 각 테넌트 데이터베이스에서 **rawTickets**, **rawCustomers**, **rawEvents** 및 **rawVenues**의 4개 SQL 보기로부터 데이터를 추출합니다. 이러한 뷰에는 행사장 Id가 포함되어 데이터 웨어하우스의 각 행사장과 데이터를 구별할 수 있습니다. 데이터는 데이터 웨어하우스에서 **raw_Tickets**, **raw_customers**, **raw_Events** 및 **raw_Venue** 드의 해당 준비 테이블로 로드됩니다. 저장된 프로시저는 원시 데이터를 변환하여 **fact_Tickets**, **dim_Customers**, **dim_Venues**, **dim_Events** 및 **dim_Dates** 등의 스타 스키마 테이블을 채웁니다.

이전 섹션에서 데이터 팩터리를 포함하여 필요한 Azure 리소스를 배포하고 초기화했습니다. 배포된 데이터 팩터리는 테넌트 데이터를 추출, 로드 및 변환하는 데 필요한 파이프라인, 데이터 세트, 연결된 서비스 등을 포함합니다. 데이터를 데이터베이스에서 데이터 웨어하우스로 이동하려면 이러한 개체를 추가 탐색한 다음, 파이프라인을 트리거해 봅니다.

### <a name="data-factory-pipeline-overview"></a>데이터 팩터리 파이프라인 개요
이 섹션에서는 데이터 팩터리에서 만든 개체를 탐색합니다. 다음 그림에서는 이 자습서에 사용된 ADF 파이프라인의 전체 워크플로에 대해 설명합니다. 나중에 파이프라인을 탐색하고서 먼저 결과를 확인하려는 경우 다음 섹션 **파이프라인 실행 트리거**로 건너뜁니다.

![adf_overview](media/saas-tenancy-tenant-analytics/adf-data-factory.PNG)

개요 페이지에서 왼쪽 패널의 **작성자** 탭으로 전환하고 세 [파이프라인](https://docs.microsoft.com/azure/data-factory/concepts-pipelines-activities) 및 세 [데이터 세트](https://docs.microsoft.com/azure/data-factory/concepts-datasets-linked-services)가 있는지 확인합니다.
![adf_author](media/saas-tenancy-tenant-analytics/adf_author_tab.JPG)

세 개의 중첩 파이프라인은 SQLDBToDW, DBCopy 및 TableCopy입니다.

**파이프라인 1 - SQLDBToDW**는 카탈로그 데이터베이스에 저장된 테넌트 데이터베이스의 이름을 조회하고(테이블 이름: [__ShardManagement].[ShardsGlobal]) 각 테넌트 데이터베이스에 대해 **DBCopy** 파이프라인을 실행합니다. 완료하면 제공된 **sp_TransformExtractedData** 저장 프로시저 스키마가 실행됩니다. 이 저장 프로시저는 준비 테이블에서 로드된 데이터를 변환하고 스타 스키마 테이블을 채웁니다.

**파이프라인 2 - DBCopy**는 Blob Storage에 저장된 구성 파일에서 열과 원본 테이블의 이름을 조회합니다.  그런 다음, **TableCopy** 파이프라인이 4개의 각 테이블, 즉 TicketFacts, CustomerFacts, EventFacts 및 VenueFacts별로 실행됩니다. **[Foreach](https://docs.microsoft.com/azure/data-factory/control-flow-for-each-activity)** 활동은 20개 데이터베이스 모두에 대해 병렬로 실행됩니다. ADF를 사용하면 최대 20회 루프 반복을 병렬로 실행할 수 있습니다. 더 많은 데이터베이스에 대한 여러 파이프라인을 만듭니다.    

**파이프라인 3 - TableCopy**는 SQL Database(_rowversion_)에서 행 버전 번호를 사용하여 변경되거나 업데이트된 행을 식별합니다. 이 작업은 원본 테이블에서 행을 추출하기 위해 시작 및 마지막 행 버전을 찾습니다. 각 테넌트 데이터베이스에 저장된 **CopyTracker** 테이블은 각각 실행되는 각 원본 테이블에서 추출된 마지막 행을 추적합니다. 새로운 또는 변경된 행은 데이터 웨어하우스에서 **raw_Tickets**, **raw_Customers**, **raw_Venues** 및 **raw_Events**의 해당 준비 테이블로 복사됩니다. 마지막으로 마지막 행 버전은 **CopyTracker** 테이블에 저장되어 다음 추출에 대한 초기 행 버전으로 사용됩니다. 

원본 SQL Database, 대상 SQL Data Warehouse 및 중간 Blob Storage에 데이터 팩토리를 연결하는 세 개의 매개 변수가 있는 연결된 서비스가 있습니다. 다음 그림에 설명된 것 처럼 **작성자** 탭에서 **연결**을 클릭하여 연결된 서비스를 탐색합니다.

![adf_linkedservices](media/saas-tenancy-tenant-analytics/linkedservices.JPG)

세 연결된 서비스에 상응하면서, 파이프라인 활동에서 입력 또는 출력으로 사용하는 데이터를 참조하는 세 데이터 세트가 있습니다. 사용된 연결 및 매개 변수를 확인하려면 각 데이터 세트를 탐색합니다. _AzureBlob_은 각 원본의 추적기 열 뿐만 아니라 원본 및 대상 테이블과 열을 포함하는 구성 파일을 가리킵니다.
  
### <a name="data-warehouse-pattern-overview"></a>데이터 웨어하우스 패턴 개요
SQL Data Warehouse는 테넌트 데이터에 대해 집계를 수행하기 위한 분석 저장소로 사용됩니다. 이 샘플에서 PolyBase는 SQL Data Warehouse에 데이터를 로드하는 데 사용됩니다. 원시 데이터는 스타 스키마 테이블로 변환된 행을 추적하는 ID 열이 있는 준비 테이블에 로드됩니다. 다음 이미지는 ![loadingpattern](media/saas-tenancy-tenant-analytics/loadingpattern.JPG) 같은 부하 패턴을 보여줍니다.

SCD(Slowly Changing Dimension) 유형 1 차원 테이블을 이 예제에서 사용합니다. 각 차원에는 ID 열을 사용하여 정의한 서로게이트 키가 있습니다. 모범 사례로 날짜 차원 테이블은 시간을 절약하기 위해 미리 채워져 있습니다. 다른 차원 테이블의 경우 CREATE TABLE AS SELECT... (CTAS) 문은 서로게이트 키와 함께 기존 수정 및 미수정 행을 포함하는 임시 테이블을 만드는 데 사용됩니다. 이는 IDENTITY_INSERT = ON을 사용하여 완료됩니다. 그런 다음, 새 행은 IDENTITY_INSERT=OFF를 사용하여 테이블에 삽입됩니다. 쉬운 롤백을 위해 기존 차원 테이블 및 임시 테이블의 이름을 바꾸어 새 차원 테이블로 만듭니다. 각 실행에 앞서 기존 차원 테이블은 삭제됩니다.

차원 테이블은 팩트 테이블보다 먼저 로드됩니다. 이 시퀀싱은 각 도착 팩트에 대해 모든 참조된 차원이 이미 존재하는지 확인합니다. 팩트가 로드되면 각 해당 차원에 대한 비즈니스 키가 일치하고 해당 서로게이트 키가 각 팩트에 추가됩니다.

변환의 마지막 단계로 파이프라인의 다음 실행에 대비한 준비 데이터를 삭제합니다.
   
### <a name="trigger-the-pipeline-run"></a>파이프라인 실행 트리거
모든 테넌트 데이터베이스에 대해 파이프라인의 추출, 부하 및 변환을 완벽히 실행하려면 다음 단계를 따릅니다.
1. ADF 사용자 인터페이스의 **작성자** 탭의 왼쪽 창에서 **SQLDBToDW** 파이프라인을 선택합니다.
1. **트리거**를 클릭하고 끌어온 메뉴에서 **트리거 시작**을 클릭합니다. 이 작업은 파이프라인을 즉시 실행합니다. 프로덕션 시나리오에서 일정에 따라 데이터를 새로 고치려면 파이프라인을 실행하기 위한 시간표를 정의합니다.
  ![adf_trigger](media/saas-tenancy-tenant-analytics/adf_trigger.JPG)
1. **파이프라인 실행** 페이지에서 **마침**을 클릭합니다.
 
### <a name="monitor-the-pipeline-run"></a>파이프라인 실행을 모니터링합니다.
1. ADF 사용자 인터페이스의 왼쪽 메뉴에서 **모니터** 탭으로 전환합니다.
1. SQLDBToDW 파이프라인의 상태가 **성공**할 때까지 **새로 고침**을 클릭합니다.
  ![adf_monitoring](media/saas-tenancy-tenant-analytics/adf_monitoring.JPG)
1. 이러한 테이블에 데이터가 로드되었는지 확인하려면 스타 스키마 테이블을 쿼리하고 SSMS로 데이터 웨어하우스에 연결합니다.

파이프라인이 완료되면 팩트 테이블은 모든 행사장에 대한 티켓 판매량 데이터를 보유하고 차원 테이블은 해당 행사장, 이벤트 및 고객으로 채워집니다.

## <a name="data-exploration"></a>데이터 탐색

### <a name="visualize-tenant-data"></a>테넌트 데이터 시각화하기

스타 스키마에 입력된 데이터에는 분석에 필요한 모든 티켓 판매량 데이터가 포함되어 있습니다. 데이터를 그래픽으로 시각화하면 방대한 데이터 집합에서 추세를 쉽게 파악할 수 있습니다. 이 섹션에서는 데이터 웨어하우스에서 **Power BI**를 사용하여 테넌트 데이터를 조작하고 시각화합니다.

다음 단계에 따라 Power BI에 접속하고 앞에서 만든 보기를 가져옵니다.

1. Power BI Desktop을 실행합니다.
2. 홈 리본에서 **데이터 가져오기**를 선택하고 메뉴에서 **자세히…** 를 선택합니다. (채널 만들기...)을 선택합니다.
3. **데이터 가져오기** 창에서 **Azure SQL Database**를 선택합니다.
4. 데이터베이스 로그인 창에 서버 이름(**catalog-dpt-&lt;User&gt;.database.windows.net**)을 입력합니다. **데이터 연결 모드**에서 **가져오기**를 선택하고 **확인**을 클릭합니다. 

    ![sign-in-to-power-bi](./media/saas-tenancy-tenant-analytics/powerBISignIn.PNG)

5. 선택 **데이터베이스** 왼쪽된 창에서 다음 사용자 이름을 입력 = *개발자*에 암호를 입력 하 고 = *P\@ssword1*합니다. **Connect**를 클릭합니다.  

    ![database-sign-in](./media/saas-tenancy-tenant-analytics/databaseSignIn.PNG)

6. **탐색기** 패널의 분석 데이터베이스 아래에서 스타 스키마 테이블(**fact_Tickets**, **dim_Events**, **dim_Venues**, **dim_Customers** 및 **dim_Dates**)을 선택합니다. 그런 다음 **로드**를 선택합니다. 

축하합니다! Power BI에 데이터를 성공적으로 로드했습니다. 지금부터 시각화 데이터를 탐색하여 테넌트에 대한 유용한 정보를 얻습니다. 분석을 사용하여 Wingtip Tickets 비즈니스 팀에게 일부 데이터 기반 권장 사항을 제공하는 방법을 살펴보겠습니다. 권장 사항을 바탕으로 비즈니스 모델과 고객 경험을 최적화할 수 있습니다.

먼저 티켓 판매량 데이터를 분석하여 행사장별 판매량의 차이를 확인합니다. Power BI에 있는 옵션을 선택하여 각 행사장에서 판매된 총 티켓 수를 막대형 차트로 표시합니다. (티켓 생성기가 임의로 작동하기 때문에 결과가 그림과 다르게 나타날 수 있습니다.)
 
![TotalTicketsByVenues](./media/saas-tenancy-tenant-analytics/TotalTicketsByVenues-DW.PNG)

위 차트를 통해 각 행사장에서 판매된 티켓의 수가 다르다는 사실을 알 수 있습니다. 티켓이 더 많이 판매된 행사장에서는 상대적으로 적게 판매된 행사장보다 서비스를 많이 이용하는 것입니다. 이에 따라 각 테넌트의 니즈에 맞게 리소스 할당량을 변경할 수 있습니다.

데이터를 분석하여 시간 경과에 따른 티켓 판매량을 확인할 수도 있습니다. Power BI에서 아래 그림과 같이 옵션을 선택하여 60일 동안의 일일 티켓 판매량을 차트로 표시합니다.
 
![SaleVersusDate](./media/saas-tenancy-tenant-analytics/SaleVersusDate-DW.PNG)

위 차트에서 일부 행사장의 티켓 판매량이 특정 날짜에 급증하는 것을 볼 수 있습니다. 이를 통해 해당 행사장에서 시스템 리소스를 불균형적으로 사용하고 있다고 가정할 수 있습니다. 아직까지 뚜렷한 판매량 급증 패턴은 볼 수 없습니다.

이번에는 판매량 급증에 유의미한 추세가 있는지 알아보겠습니다. 티켓 판매 개시 이후 어느 시점에 판매량 급증되는 것일까요? Power BI에서 아래 그림에 표시된 옵션을 선택하여 판매 개시 이후 일별 티켓 판매량을 차트로 표시합니다.

![SaleDayDistribution](./media/saas-tenancy-tenant-analytics/SaleDistributionPerDay-DW.PNG)

이 차트를 통해 일부 행사장에서 판매 개시 첫날에 다량의 티켓을 판매하고 있는 것을 알 수 있습니다. 아마도 티켓 판매가 개시되자마자 티켓을 사려는 사람들이 몰려드는 것으로 보입니다. 이처럼 몇몇 행사장의 활동이 급증하면 다른 테넌트의 서비스에 영향이 갈 수 있습니다.

데이터를 분석하여 해당 행사장의 모든 이벤트마다 개시 첫날 판매량 급증이 똑같이 발생하는지 확인해 볼 수 있습니다. 위 차트에서 Contoso Concert Hall의 티켓 판매량이 특히 높고, 특정 날짜에 판매량이 급증한다는 사실을 확인했습니다. Power BI 옵션을 이리저리 조정하여 각 행사별 판매 추세를 중심으로 Contoso Concert Hall의 누적 티켓 판매량을 차트로 표시해 봅니다. 모든 이벤트마다 판매량 패턴이 동일하게 나타나나요? 아래와 같은 차트를 생성하려고 합니다.

![ContosoSales](media/saas-tenancy-tenant-analytics/EventSaleTrends.PNG)

각 이벤트에 대한 Contoso Concert Hall의 시간에 따른 누적 티켓 판매량을 표시한 이 차트는 모든 이벤트에 사람들이 몰려들지 않는다는 것을 보여 줍니다. 필터 옵션을 이리저리 조정하여 다른 행사장의 판매량 추세도 확인해 봅니다.

티켓 판매량 패턴을 파악하면 Wingtip Tickets가 비즈니스 모델을 최적화할 방향을 찾게 될 수도 있습니다. 모든 테넌트에 동일한 요금을 부과하는 대신 서로 다른 컴퓨팅 크기를 갖는 등급별 서비스 모델을 도입하는 것이 좋을 수도 있습니다. 하루에 더 많은 티켓을 판매해야 하는 대형 행사장에는 높은 SLA(서비스 수준 계약)를 갖는 높은 등급을 제안할 수 있습니다. 이러한 행사장에서는 데이터베이스당 리소스 한도가 더 높은 풀에 자신의 데이터베이스를 배치할 수 있습니다. 각 서비스 등급에 시간당 판매량을 할당하고, 할당량이 초과되면 추가 요금을 부과할 수도 있을 것입니다. 주기적으로 판매량이 급증하는 대형 행사장이라면 높은 등급의 서비스를 이용하는 것이 이익이 될 것입니다. 또한, Wingtip Tickets은 서비스의 수익을 더 효율적으로 높일 수 있게 됩니다.

한편, Wingtip Tickets의 고객들은 자신들이 지불하는 서비스 요금보다 티켓 판매량이 현저히 떨어진다고 불만을 토로하고 있습니다. 데이터를 분석하여 성적이 좋지 않은 행사장의 티켓 판매량을 높일 방안이 있는지 확인해 볼 수 있습니다. 판매량이 높아지면 서비스의 체감 가치가 높아지게 됩니다. fact_Tickets를 마우스 오른쪽 단추로 클릭하고 **새 측정값**을 선택합니다. **AverageTicketsSold**라는 새 측정값에 다음과 같은 식을 입력합니다.

```
AverageTicketsSold = DIVIDE(DIVIDE(COUNTROWS(fact_Tickets),DISTINCT(dim_Venues[VenueCapacity]))*100, COUNTROWS(dim_Events))
```

아래 그림을 보고 시각화 옵션을 설정하여 각 행사장별 티켓 판매량 비율 차트를 표시합니다. 이를 통해 상대적인 판매 성적을 확인할 수 있습니다.

![AvgTicketsByVenues](media/saas-tenancy-tenant-analytics/AvgTicketsByVenues-DW.PNG)

위 차트를 통해 행사장 중 다수가 티켓의 80%를 판매하고 있지만, 좌석의 절반을 채우기도 어려운 행사장도 있음을 알 수 있습니다. 값을 조정하여 각 행사장별 판매량 비율의 최대값과 최소값을 선택합니다.

## <a name="embedding-analytics-in-your-apps"></a>앱에 분석 포함 
이 자습서에서는 소프트웨어 공급 업체의 해당 테넌트에 대한 이해를 개선하는 데 사용된 테넌트 간 분석에 중점을 뒀습니다. 분석은 _테넌트_에 인사이트를 제공하여 비즈니스 자체를 보다 효율적으로 관리할 수 있도록 도와줍니다. 

Wingtip Tickets 예제에서는 일찌기 티켓 판매량이 예측 가능한 패턴을 따르는 경향이 있음을 알았습니다. 이 인사이트는 성적이 좋지 않은 행사장이 티켓 판매를 높이도록 돕는 데 사용될 수 있습니다. 이벤트의 티켓 판매량을 예측하는 데 기계 학습 기법을 사용할 기회가 있습니다. 가격 변경의 효과를 모델링하여 할인 제공의 영향을 예측할 수 있습니다. 판매된 총 티켓에 대한 할인 영향 및 판매가 저조한 행사장의 수익을 포함한 예측 판매량을 시각화하려면 Power BI Embedded를 관리 애플리케이션에 통합할 수 있습니다. Power BI Embedded를 사용하여 티켓 가격에 실제로 할인을 적용하여 시각화 환경에 통합할 수도 있습니다.


## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * 테넌트 분석에 대한 스타 스키마로 채워진 SQL Data Warehouse를 배포합니다.
> * Azure Data Factory를 사용하여 각 테넌트 데이터베이스에서 분석 데이터 웨어하우스로 데이터를 추출합니다.
> * 추출된 데이터 최적화하기(스타 스키마로 재구성하기).
> * 분석 데이터 웨어하우스를 쿼리합니다. 
> * Power BI를 사용하여 모든 테넌트 간 데이터 추세를 시각화합니다.

축하합니다!

## <a name="additional-resources"></a>추가 리소스

- [Wingtip SaaS 애플리케이션을 사용하는 또 다른 자습서](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials).
