---
title: 추출된 데이터를 사용하여 교차 테넌트 분석 실행 | Microsoft Docs
description: 단일 테넌트 앱의 여러 Azure SQL Database 데이터베이스에서 추출된 데이터를 사용하는 교차 테넌트 분석 쿼리입니다.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: anjangsh,billgib,genemi
manager: craigg
ms.date: 12/18/2018
ms.openlocfilehash: 0be39aaf5526ea288764fc72d6c498cca2d659b7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61486498"
---
# <a name="cross-tenant-analytics-using-extracted-data---single-tenant-app"></a>추출된 데이터를 사용하여 교차 테넌트 분석 - 단일 테넌트 앱
 
이 자습서에서는 단일 테넌트 구현용 전체 분석 시나리오를 진행합니다. 시나리오에서는 기업에서 스마트한 의사 결정을 내리는 데 분석을 어떻게 이용할 수 있는지 보여줍니다. 각 테넌트 데이터베이스에서 추출된 데이터를 사용하여 분석을 통해 샘플 Wingtip Tickets SaaS 애플리케이션을 비롯한 테넌트 동작을 살펴보게 됩니다. 이 시나리오는 다음과 같이 3단계로 구성됩니다. 

1.  각 테넌트 데이터베이스에서 분석 저장소로 데이터를 **추출**하고 **로드**합니다.
2.  분석 처리를 위해 **추출된 데이터를 변환**합니다.
3.  **비즈니스 인텔리전스** 도구를 사용하여 의사 결정에 도움이 되는 유용한 정보를 얻습니다. 

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> - 추출한 데이터를 저장할 테넌트 분석 저장소 만들기.
> - 탄력적 작업을 사용하여 각 테넌트 데이터베이스에서 분석 저장소로 데이터 추출하기.
> - 추출된 데이터 최적화하기(스타 스키마로 재구성하기).
> - 분석 데이터베이스를 대상으로 쿼리하기.
> - 데이터 시각화를 위해 Power BI를 사용하여 테넌트 데이터의 추세를 파악하고 개선을 위한 권장 사항 도출하기.

![architectureOverView](media/saas-tenancy-tenant-analytics/architectureOverview.png)

## <a name="offline-tenant-analytics-pattern"></a>오프라인 테넌트 분석 패턴

일반적으로 다중 테넌트 SaaS 애플리케이션에는 클라우드에 저장된 방대한 테넌트 데이터가 있습니다. 이러한 데이터는 애플리케이션의 작동 및 사용과 테넌트의 동작에 대한 풍부한 정보를 제공합니다. 이와 같은 인사이트는 기능 개발, 사용성 개선, 앱 및 플랫폼의 기타 투자에 대한 방향성을 제공할 수 있습니다.

모든 데이터가 하나의 다중 테넌트 데이터베이스에 저장되어 있다면 모든 테넌트가 손쉽게 데이터에 액세스할 수 있습니다. 그러나 데이터가 수천 개의 데이터베이스에 잠재적으로 분산되어 저장되어 있다면 액세스가 복잡해집니다. 이러한 복잡성을 해결하고 트랜잭션 데이터에 대한 분석 쿼리의 영향을 최소화하는 한 가지 방법은 데이터를 목적이 있는 분석 데이터베이스 또는 데이터 웨어하우스로 추출하는 것입니다.

이 자습서에서는 Wingtip Tickets SaaS 애플리케이션에 대한 분석 시나리오를 처음부터 끝까지 살펴봅니다. 먼저 ‘탄력적 작업’은 각 테넌트 데이터베이스에서 데이터를 추출하여 분석 저장소의 준비 테이블에 로드하는 데 사용됩니다. 분석 저장소로 SQL Database나 SQL Data Warehouse를 사용할 수 있습니다. 대규모 데이터 추출 시에는 [Azure Data Factory](../data-factory/introduction.md)를 사용하는 것이 권장됩니다.

다음으로, 집계된 데이터를 일련의 [스타 스키마](https://www.wikipedia.org/wiki/Star_schema) 테이블로 변환합니다. 테이블은 중앙의 팩트 테이블과 관련 차원 테이블로 이루어집니다.  Wingtip Tickets의 경우 다음과 같습니다.

- 중앙의 팩트 테이블은 스타 스키마로 되어 있고 티켓 데이터를 포함합니다.
- 차원 데이터는 장소, 이벤트, 고객, 구입 날짜와 같은 데이터를 설명합니다.

중앙 팩트와 차원 테이블을 함께 사용하면 효율적인 분석 처리가 가능해집니다. 아래의 그림에서 이 자습서에서 사용하고 있는 스타 스키마를 볼 수 있습니다.
 
![architectureOverView](media/saas-tenancy-tenant-analytics/StarSchema.png)

마지막으로 테넌트 동작에 대한 정보와 Wingtip Tickets 애플리케이션의 사용을 강조 표시하기 위해 **PowerBI**를 통해 분석 저장소가 쿼리됩니다. 다음과 같은 쿼리를 실행합니다.
 
- 각 장소의 상대적 인기도를 표시하는 쿼리
- 다른 이벤트에 대한 티켓 판매의 패턴을 강조 표시하는 쿼리
- 이벤트를 판매하는 다른 장소의 상대적인 판매 성적을 표시하는 쿼리

각 테넌트가 서비스를 사용하는 방법에 대해 이해하면 테넌트가 더 성공적인 성과를 거둘 수 있도록 지원하기 위한 서비스 수익화 및 서비스 개선에 대한 옵션을 알아볼 수 있습니다. 이 자습서에서는 테넌트 데이터로부터 창출할 수 있는 기본적인 정보를 다양하게 살펴볼 수 있습니다.

## <a name="setup"></a>설정

### <a name="prerequisites"></a>필수 조건

이 자습서를 수행하려면 다음 필수 조건이 충족되었는지 확인합니다.

- Wingtip Tickets SaaS Database Per Tenant 애플리케이션이 배포되어 있어야 합니다. 5분 내에 배포하려면 [Wingtip SaaS 애플리케이션 배포 및 탐색](saas-dbpertenant-get-started-deploy.md)을 참조하세요.
- Wingtip Tickets SaaS Database Per Tenant 스크립트와 애플리케이션 [소스 코드](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant/)를 GitHub에서 다운로드해야 합니다. 다운로드 지침을 참조하세요. 콘텐츠를 추출하기 전에 *zip 파일의 차단을 해제*해야 합니다. Wingtip Tickets SaaS 스크립트를 다운로드하고 차단을 해제하는 단계는 [일반 지침](saas-tenancy-wingtip-app-guidance-tips.md)을 확인하세요.
- Power BI Desktop이 설치되어 있어야 합니다. [Power BI Desktop 다운로드](https://powerbi.microsoft.com/downloads/)
- 추가 테넌트 배치가 프로비전되어 있어야 합니다. [**테넌트 프로비전 자습서**](saas-dbpertenant-provision-and-catalog.md)를 참조하세요.
- 작업 계정과 jobaccount 데이터베이스가 생성되어 있어야 합니다. [**스키마 관리 자습서**](saas-tenancy-schema-management.md#create-a-job-agent-database-and-new-job-agent)에서 해당 단계를 참조하세요.

### <a name="create-data-for-the-demo"></a>데모를 위한 데이터 만들기

이 자습서에서는 티켓 판매량 데이터를 대상으로 분석을 수행합니다. 이 단계에서는 모든 테넌트의 티켓 데이터를 생성합니다.  생성된 데이터는 나중에 분석을 위해 추출됩니다. *유의미한 데이터 양을 확보하기 위해 앞에서 설명한 바와 같이 테넌트 배치가 프로비전되어 있어야 합니다*. 데이터가 일정 양을 넘어서면 다양한 티켓 구매 패턴을 파악할 수 있습니다.

1. PowerShell ISE에서 *…\Learning Modules\Operational Analytics\Tenant Analytics\Demo-TenantAnalytics.ps1*을 열고 다음 값을 설정합니다.
    - **$DemoScenario** = **1** 모든 행사장에서 이벤트 티켓 구입
2. **F5** 키를 눌러 스크립트를 실행하고 모든 행사장의 모든 이벤트에 대한 티켓 구매 이력을 생성합니다.  스크립트가 몇 분 동안 실행되며 수만 개의 티켓을 생성합니다.

### <a name="deploy-the-analytics-store"></a>분석 저장소 배포하기
모든 테넌트 데이터가 여러 트랜잭션 데이터베이스에 분산되어 저장되는 경우가 많습니다. 이 경우 여러 트랜잭션 데이터의 테넌트 데이터를 하나의 분석 저장소로 집계해야 합니다. 이렇게 하면 데이터를 효율적으로 쿼리할 수 있습니다. 이 자습서에서는 집계된 데이터를 저장하는 데 Azure SQL Database 데이터베이스를 사용합니다.

이어지는 단계에서는 **tenantanalytics**라는 분석 저장소를 배포합니다. 자습서 뒷부분에서 자동으로 입력되게 되는 사전 정의된 테이블도 배포합니다.
1. PowerShell ISE에서 *…\Learning Modules\Operational Analytics\Tenant Analytics\Demo-TenantAnalytics.ps1*을 엽니다. 
2. 선택한 분석 저장소에 맞도록 $DemoScenario 값을 설정합니다.
    - 열 저장소가 없는 SQL 데이터베이스를 사용하려면 **$DemoScenario** = **2**로 설정합니다.
    - 열 저장소가 있는 SQL 데이터베이스를 사용하려면 **$DemoScenario** = **3**으로 설정합니다.  
3. 키를 누릅니다 **F5** 데모 스크립트를 실행 (호출 하는 합니다 *Deploy-tenantanalytics\<XX >. ps1* 스크립트) 테 넌 트 분석 저장소를 만듭니다. 

사용 하 여 응용 프로그램을 배포 하 고 관심 있는 테 넌 트 데이터를 사용 하 여 입력 했으므로 [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) 연결할 **tenants1-dpt-&lt;사용자&gt;**  및 **catalog-dpt-&lt;사용자&gt;**  로그인을 사용 하 여 서버 = *개발자*, 암호 = *P\@ssword1*합니다. 자세한 내용은 [입문용 자습서](saas-dbpertenant-wingtip-app-overview.md)를 참조하세요.

![architectureOverView](media/saas-tenancy-tenant-analytics/ssmsSignIn.png)

개체 탐색기에서 다음 단계를 수행합니다.

1. *tenants1-dpt-&lt;User&gt;* 서버를 확장합니다.
2. Databases 노드를 확장하여 테넌트 데이터베이스 목록을 확인합니다.
3. *catalog-dpt-&lt;User&gt;* 서버를 확장합니다.
4. 분석 저장소와 jobaccount 데이터베이스가 보이는지 확인합니다.

SSMS 개체 탐색기에서 분석 저장소 노드를 확장하여 다음과 같은 데이터베이스 항목을 확인합니다.

- **TicketsRawData** 테이블과 **EventsRawData** 테이블은 테넌트 데이터베이스에서 추출된 원시 데이터를 포함합니다.
- 스타 스키마 테이블은 **fact_Tickets**, **dim_Customers**, **dim_Venues**, **dim_Events**, **dim_Dates**입니다.
- 스타 스키마 테이블에 원시 데이터 테이블을 입력하는 데 저장 프로시저가 사용됩니다.

![architectureOverView](media/saas-tenancy-tenant-analytics/tenantAnalytics.png)

## <a name="data-extraction"></a>데이터 추출 

### <a name="create-target-groups"></a>대상 그룹 만들기 

계속하기 전에 작업 계정과 jobaccount 데이터베이스가 배포되었는지 확인해야 합니다. 이어지는 단계에서는 탄력적 작업을 사용하여 각 테넌트 데이터베이스에서 데이터를 추출하고 추출한 데이터를 분석 저장소에 저장합니다. 그런 다음 두 번째 작업을 사용하여 데이터를 단편화하고 단편화된 데이터를 스타 스키마 테이블에 저장합니다. 이 두 개의 작업은 **TenantGroup** 그룹과 **AnalyticsGroup** 그룹이라는 서로 다른 그룹을 대상으로 실행됩니다. 추출 작업은 모든 테넌트 데이터베이스를 포함하는 TenantGroup을 대상으로 실행됩니다. 단편화 작업은 분석 저장소만 포함하는 AnalyticsGroup을 대상으로 실행됩니다. 다음 단계에 따라 대상 그룹을 만듭니다.

1. SSMS에서 catalog-dpt-&lt;User&gt;의 **jobaccount** 데이터베이스에 접속합니다.
2. SSMS에서 *…\Learning Modules\Operational Analytics\Tenant Analytics\ TargetGroups.sql*을 엽니다. 
3. 스크립트 상단의 @User 변수를 수정합니다. 즉, Wingtip SaaS 앱을 배포할 때 사용한 사용자 값으로 `<User>`를 변경합니다.
4. **F5** 키를 눌러 스크립트를 실행하여 두 개의 대상 그룹을 만듭니다.

### <a name="extract-raw-data-from-all-tenants"></a>모든 테넌트에서 원시 데이터 추출하기

*티켓 및 고객* 데이터는 *이벤트 및 행사장* 데이터보다 데이터 수정이 훨씬 많이 일어날 수 있습니다. 따라서 자주 추출되는 티켓 및 고객 데이터를 이벤트 및 행사장 데이터와 별개로 추출하는 것이 좋습니다. 이 섹션에서는 다음과 같은 두 개의 작업을 정의하고 일정을 예약합니다.

- 티켓 및 고객 데이터 추출.
- 이벤트 및 행사장 데이터 추출.

각 작업은 데이터를 추출하고 추출한 데이터를 분석 저장소에 저장합니다. 분석 저장소에서는 데이터가 또 다른 작업에 의해 분석 스타 스키마로 단편화됩니다.

1. SSMS에서 catalog-dpt-&lt;User&gt; 서버의 **jobaccount** 데이터베이스에 접속합니다.
2. SSMS에서 *...\Learning Modules\Operational Analytics\Tenant Analytics\ExtractTickets.sql*을 엽니다.
3. 스크립트 상단의 @User를 수정합니다. 즉, Wingtip SaaS 앱을 배포할 때 사용한 사용자 이름으로 `<User>`를 변경합니다. 
4. F5 키를 눌러 스크립트를 실행합니다. 각 테넌트 데이터베이스에서 티켓 및 고객 데이터를 추출하는 작업이 생성되고 실행됩니다. 추출된 데이터는 분석 저장소에 저장됩니다.
5. tenantanalytics 데이터베이스의 TicketsRawData 테이블을 쿼리하여 모든 테넌트의 티켓 정보가 테이블에 입력되었는지 확인합니다.

![ticketExtracts](media/saas-tenancy-tenant-analytics/ticketExtracts.png)

위 단계를 반복합니다. 이번에는 2단계에서 **\ExtractTickets.sql** 대신 **\ExtractVenuesEvents.sql**을 사용합니다.

작업이 성공적으로 실행되면 분석 저장소의 EventsRawData 테이블에 모든 테넌트의 이벤트 및 행사장 정보가 입력됩니다. 

## <a name="data-reorganization"></a>데이터 재구성

### <a name="shred-extracted-data-to-populate-star-schema-tables"></a>추출된 데이터를 단편화하여 스타 스키마 테이블에 입력하기

이번에는 추출된 원시 데이터를 단편화하여 분석 쿼리에 최적화된 일련의 테이블에 저장하는 작업을 수행합니다. 이때 스타 스키마가 사용됩니다. 중앙의 팩트 테이블에는 개별 티켓 판매량 기록이 입력됩니다. 팩트 테이블을 제외한 다른 테이블에는 행사장, 이벤트, 고객에 관한 데이터가 입력됩니다. 이 밖에도 시간 차원 테이블이 있습니다. 

이 섹션에서는 추출된 원시 데이터와 스타 스키마 테이블의 데이터를 병합하는 작업을 정의하고 실행합니다. 병합 작업이 완료되면 원시 데이터가 삭제되고 테이블에 다음번 테넌트 데이터 추출 작업에 의해 추출된 데이터를 입력할 준비가 됩니다.

1. SSMS에서 catalog-dpt-&lt;User&gt;의 **jobaccount** 데이터베이스에 접속합니다.
2. SSMS에서 *…\Learning Modules\Operational Analytics\Tenant Analytics\ShredRawExtractedData.sql*을 엽니다.
3. **F5** 키를 눌러 스크립트를 실행합니다. 분석 저장소의 sp_ShredRawExtractedData 저장 프로시저를 호출하는 작업이 정의됩니다.
4. 작업이 완료될 때까지 기다립니다.
    - jobs.jobs_execution 테이블의 **Lifecycle** 열에서 작업의 상태를 확인합니다. 작업의 상태가 **Succeeded**로 바뀌면 다음 단계로 넘어갑니다. 작업이 성공적으로 완료되면 데이터가 아래 그림과 같이 표시됩니다.

![단편화](media/saas-tenancy-tenant-analytics/shreddingJob.PNG)

## <a name="data-exploration"></a>데이터 탐색

### <a name="visualize-tenant-data"></a>테넌트 데이터 시각화하기

스타 스키마 테이블에 입력된 데이터에는 분석에 필요한 모든 티켓 판매량 데이터가 포함되어 있습니다. 이처럼 방대한 데이터 집합에서 추세를 쉽게 파악하기 위해서는 데이터를 그래픽으로 시각화해야 합니다.  이 섹션에서는 앞에서 추출하고 구성한 테넌트 데이터를 **Power BI**를 사용하여 조작하고 시각화하는 방법을 알아봅니다.

다음 단계에 따라 Power BI에 접속하고 앞에서 만든 보기를 가져옵니다.

1. Power BI Desktop을 실행합니다.
2. 홈 리본에서 **데이터 가져오기**를 선택하고 메뉴에서 **자세히…** 를 선택합니다. (채널 만들기...)을 선택합니다.
3. **데이터 가져오기** 창에서 Azure SQL Database를 선택합니다.
4. 데이터베이스 로그인 창에 서버 이름(catalog-dpt-&lt;User&gt;.database.windows.net)을 입력합니다. **데이터 연결 모드**에서 **가져오기**를 선택하고 확인을 클릭합니다. 

    ![signinpowerbi](./media/saas-tenancy-tenant-analytics/powerBISignIn.PNG)

5. 선택 **데이터베이스** 왼쪽된 창에서 다음 사용자 이름을 입력 = *개발자*에 암호를 입력 하 고 = *P\@ssword1*합니다. **Connect**를 클릭합니다.  

    ![databasesignin](./media/saas-tenancy-tenant-analytics/databaseSignIn.PNG)

6. **탐색기** 패널의 분석 데이터베이스 아래에서 스타 스키마 테이블(fact_Tickets, dim_Events, dim_Venues, dim_Customers, dim_Dates)을 선택합니다. 그런 다음 **로드**를 선택합니다. 

축하합니다! Power BI에 데이터를 성공적으로 로드했습니다. 지금부터 시각화 데이터를 살펴보고 테넌트에 대한 유용한 정보를 얻을 수 있습니다. 이번에는 Wingtip Tickets 비즈니스 팀이 분석을 사용하여 데이터 기반 권장 사항을 확인하는 예를 살펴보겠습니다. 권장 사항을 바탕으로 비즈니스 모델과 고객 경험을 최적화할 수 있습니다.

먼저 티켓 판매량 데이터를 분석하여 행사장별 판매량의 차이를 확인합니다. Power BI에서 아래 그림과 같이 옵션을 선택하여 각 행사장에서 판매된 총 티켓 수를 막대형 차트로 표시합니다. 티켓 생성기가 임의로 작동하기 때문에 결과가 그림과 다르게 나타날 수 있습니다.
 
![TotalTicketsByVenues](./media/saas-tenancy-tenant-analytics/TotalTicketsByVenues.PNG)

위 차트를 통해 각 행사장에서 판매된 티켓의 수가 다르다는 사실을 알 수 있습니다. 티켓이 더 많이 판매된 행사장에서는 상대적으로 적게 판매된 행사장보다 서비스를 많이 이용하는 것입니다. 이에 따라 각 테넌트의 니즈에 맞게 리소스 할당량을 변경할 수 있습니다.

데이터를 분석하여 시간 경과에 따른 티켓 판매량을 확인할 수도 있습니다. Power BI에서 아래 그림과 같이 옵션을 선택하여 60일 동안의 일일 티켓 판매량을 차트로 표시합니다.
 
![SaleVersusDate](./media/saas-tenancy-tenant-analytics/SaleVersusDate.PNG)

위 차트에서 일부 행사장의 티켓 판매량이 특정 날짜에 급증하는 것을 볼 수 있습니다. 이를 통해 해당 행사장에서 시스템 리소스를 불균형적으로 사용하고 있다고 가정할 수 있습니다. 아직까지 뚜렷한 판매량 급증 패턴은 볼 수 없습니다.

이번에는 판매량 급증에 유의미한 추세가 있는지 알아보겠습니다. 티켓 판매 개시 이후 어느 시점에 판매량 급증되는 것일까요? Power BI에서 아래 그림과 같이 옵션을 선택하여 판매 개시 이후 일차별 티켓 판매량을 차트로 표시합니다.

![SaleDayDistribution](./media/saas-tenancy-tenant-analytics/SaleDistributionPerDay.PNG)

위 차트를 통해 일부 행사장에서 판매 개시 첫날에 다량의 티켓을 판매하고 있는 것을 알 수 있습니다. 아마도 티켓 판매가 개시되자마자 티켓을 사려는 사람들이 몰려드는 것으로 보입니다. 이처럼 몇몇 행사장의 활동이 급증하면 다른 테넌트의 서비스에 영향이 갈 수 있습니다.

데이터를 분석하여 해당 행사장의 모든 이벤트마다 개시 첫날 판매량 급증이 똑같이 발생하는지 확인해 볼 수 있습니다. 위 차트에서 Contoso Concert Hall의 티켓 판매량이 특히 높고, 특정 날짜에 판매량이 급증한다는 사실을 확인했습니다. Power BI 옵션을 이리저리 조정하여 각 행사별 판매 추세를 중심으로 Contoso Concert Hall의 누적 티켓 판매량을 차트로 표시해 봅니다. 모든 이벤트마다 판매량 패턴이 동일하게 나타나나요?

![ContosoSales](media/saas-tenancy-tenant-analytics/EventSaleTrends.PNG)

위의 Contoso Concert Hall 차트를 보면 판매 개시 첫날 판매량이 급증하는 이벤트도 있고, 급증하지 않는 이벤트도 있음을 알 수 있습니다. 필터 옵션을 이리저리 조정하여 다른 행사장의 판매량 추세도 확인해 봅니다.

티켓 판매량 패턴을 파악하면 Wingtip Tickets가 비즈니스 모델을 최적화할 방향을 찾게 될 수도 있습니다. 모든 테넌트에 동일한 요금을 부과하는 대신 서로 다른 컴퓨팅 크기를 갖는 등급별 서비스 모델을 도입하는 것이 좋을 수도 있습니다. 하루에 더 많은 티켓을 판매해야 하는 대형 행사장에는 높은 SLA(서비스 수준 계약)를 갖는 높은 등급을 제안할 수 있습니다. 이러한 행사장에서는 데이터베이스당 리소스 한도가 더 높은 풀에 자신의 데이터베이스를 배치할 수 있습니다. 각 서비스 등급에 시간당 판매량을 할당하고, 할당량이 초과되면 추가 요금을 부과할 수도 있을 것입니다. 주기적으로 판매량이 급증하는 대형 행사장이라면 높은 등급의 서비스를 이용하는 것이 이익이 될 것입니다. 또한, Wingtip Tickets은 서비스의 수익을 더 효율적으로 높일 수 있게 됩니다.

한편, Wingtip Tickets의 고객들은 자신들이 지불하는 서비스 요금보다 티켓 판매량이 현저히 떨어진다고 불만을 토로하고 있습니다. 데이터를 분석하여 성적이 좋지 않은 행사장의 티켓 판매량을 높일 방안이 있는지 확인해 볼 수 있습니다. 판매량이 높아지면 서비스의 체감 가치가 높아지게 됩니다. fact_Tickets를 마우스 오른쪽 단추로 클릭하고 **새 측정값**을 선택합니다. **AverageTicketsSold**라는 새 측정값에 다음과 같은 식을 입력합니다.

```
AverageTicketsSold = AVERAGEX( SUMMARIZE( TableName, TableName[Venue Name] ), CALCULATE( SUM(TableName[Tickets Sold] ) ) )
```

아래 그림을 보고 시각화 옵션을 설정하여 각 행사장별 티켓 판매량 비율 차트를 표시합니다. 이를 통해 상대적인 판매 성적을 확인할 수 있습니다.

![AvgTicketsByVenues](media/saas-tenancy-tenant-analytics/AvgTicketsByVenues.PNG)

위 차트를 통해 행사장 중 다수가 티켓의 80%를 판매하고 있지만, 좌석의 절반을 채우기도 어려운 행사장도 있음을 알 수 있습니다. 값을 조정하여 각 행사장별 판매량 비율의 최대값과 최소값을 선택합니다.

앞에서는 티켓 판매량이 예측 가능한 패턴을 따른다는 사실을 분석했습니다. Wingtip Tickets는 이 사실을 바탕으로 동적 가격 책정 모델을 제안하여 성적이 부진한 행사장이 티켓 판매량을 높일 수 있도록 도울 수 있습니다. 또한, 각 이벤트의 티켓 판매량을 예측하는 데 기계 학습 기법을 사용할 기회도 제시됩니다. 티켓 판매 시 할인 혜택을 제공할 경우 각 행사장에 어떤 영향이 가해지는지를 예측해 볼 수도 있습니다. Power BI Embedded를 이벤트 관리 애플리케이션에 통합하는 것도 가능합니다. 통합이 적용되면 예상 판매량과 할인율별 영향을 시각화해 볼 수도 있습니다. 이벤트 관리 애플리케이션을 이용하여 분석 그래프에서 최적의 할인율을 곧바로 적용하는 방안도 고안할 수 있습니다.

지금까지 WingTip 애플리케이션의 테넌트 데이터 추세를 관찰해 보았습니다. 이 앱을 사용하여 SaaS 애플리케이션 벤더들이 비즈니스 의사 결정을 내리는 데 도움이 되는 다양한 방안을 생각해 볼 수 있을 것입니다. 벤더들은 이러한 방안을 고안하여 테넌트들의 다양한 니즈에 부응할 수 있게 됩니다. 이 자습서를 통해 테넌트 데이터에 분석을 적용하여 데이터를 기반으로 하는 의사 결정을 내리는 데 필요한 지식을 확충하셨기 바랍니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> - 사전 정의된 스타 스키마 테이블을 사용하여 테넌트 분석 데이터베이스 배포하기
> - 탄력적 작업을 사용하여 모든 테넌트 데이터베이스에서 데이터 추출하기
> - 추출된 데이터를 분석을 위해 작성한 스타 스키마 테이블에 병합하기
> - 분석 데이터베이스 쿼리하기 
> - 데이터 시각화를 위해 Power BI를 사용하여 테넌트 데이터의 추세 관찰하기 

축하합니다!

## <a name="additional-resources"></a>추가 리소스

- [Wingtip SaaS 애플리케이션을 사용하는 또 다른 자습서](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials).
- [탄력적 작업](sql-database-elastic-jobs-overview.md).
- [추출된 데이터를 사용하여 교차 테넌트 분석 - 다중 테넌트 앱](saas-multitenantdb-tenant-analytics.md)