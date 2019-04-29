---
title: 여러 Azure SQL 데이터베이스에 대해 보고 쿼리 실행 | Microsoft Docs
description: 분산 쿼리를 사용한 교차 테넌트 보고
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewers: billgib,ayolubek
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: 9562d0cd1ad97a459c3630456a6070ac2b6e63f3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61484743"
---
# <a name="cross-tenant-reporting-using-distributed-queries"></a>분산 쿼리를 사용한 교차 테넌트 보고

이 자습서에서는 보고를 위해 테넌트 데이터베이스의 전체 집합에서 분산 쿼리를 실행합니다. 이러한 쿼리는 Wingtip Tickets SaaS 테넌트의 일일 운영 데이터에 담긴 정보를 추출할 수 있습니다. 이를 위해 추가 보고 데이터베이스를 카탈로그 서버에 배포하고 탄력적 쿼리를 사용하여 분산 쿼리를 사용하도록 설정합니다.


이 자습서에서는 다음에 대해 알아봅니다.

> [!div class="checklist"]
> 
> * 보고 데이터베이스를 배포하는 방법
> * 모든 테넌트 데이터베이스 간에 분산 쿼리를 실행하는 방법
> * 각 데이터베이스의 전역 보기에서 테넌트 간 효율적인 쿼리를 활성화하는 방법


이 자습서를 수행하려면 다음 필수 조건이 완료되었는지 확인합니다.


* Wingtip Tickets SaaS Database Per Tenant 앱이 배포됩니다. 5분 내에 배포하려면 [Wingtip Tickets SaaS Database Per Tenant 애플리케이션 배포 및 탐색](saas-dbpertenant-get-started-deploy.md)을 참조하세요.
* Azure PowerShell이 설치되었습니다. 자세한 내용은 [Azure PowerShell 시작](https://docs.microsoft.com/powershell/azure/get-started-azureps)을 참조하세요.
* SSMS(SQL Server Management Studio)가 설치되었습니다. SSMS를 다운로드하고 설치하려면 [SSMS(SQL Server Management Studio) 다운로드](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)를 참조하세요.


## <a name="cross-tenant-reporting-pattern"></a>교차 테넌트 보고 패턴

![교차 테넌트 분산 쿼리 패턴](media/saas-tenancy-cross-tenant-reporting/cross-tenant-distributed-query.png)

SaaS 애플리케이션을 사용했을 때의 장점은 애플리케이션 작업 및 사용에 대한 정보를 얻기 위해 클라우드에 저장된 광범위한 테넌트 데이터를 활용할 수 있다는 점입니다. 이러한 정보는 기능 개발, 활용성 향상 및 기타 앱 및 서비스 투자를 유도할 수 있습니다.

단일 다중 테넌트 데이터베이스에 있을 때 이 데이터에 액세스하는 것은 쉽지만 잠재적인 수천 개의 데이터베이스 규모로 분산되는 경우는 그렇게 쉽지 않습니다. 한 가지 방법은 공통 스키마를 통해 배포된 데이터베이스 집합 전체에서 쿼리를 구현하는 [탄력적 쿼리](sql-database-elastic-query-overview.md)를 사용하는 것입니다. 이러한 데이터베이스는 여러 다른 리소스 그룹 및 구독에서 배포할 수 있으나 공통 로그인을 공유해야 합니다. 탄력적 쿼리는 분산된(테넌트) 데이터베이스에서 테이블 또는 뷰를 미러링하는 외부 테이블이 정의되는 단일 *헤드* 데이터베이스를 사용합니다. 이 헤드 데이터베이스에 제출된 쿼리는 분산 쿼리 계획을 생성하기 위해 컴파일되며 이 쿼리 중 일부는 필요에 따라 테넌트 데이터베이스에 푸시됩니다. 탄력적 쿼리는 카탈로그 데이터베이스에서 분할된 데이터베이스 맵을 사용하여 모든 테넌트 데이터베이스의 위치를 확인합니다. 헤드 데이터베이스의 설정 및 쿼리는 일반 [Transact-SQL](https://docs.microsoft.com/sql/t-sql/language-reference)을 사용하여 바로 진행되며 Power BI 또는 Excel 같은 도구에서의 쿼리를 지원합니다.

탄력적 쿼리는 테넌트 데이터베이스에 쿼리를 배포하여 라이브 프로덕션 데이터에 즉시 정보를 제공합니다. 탄력적 쿼리가 잠재적으로 많은 데이터베이스에서 데이터를 가져오면 쿼리 대기 시간이 단일 다중 테넌트 데이터베이스에 제출되는 해당 쿼리보다 높아질 수 있습니다. 헤드 데이터베이스에 반환되는 데이터를 최소화하도록 쿼리를 디자인합니다. 탄력적 쿼리는 자주 사용되는 문서 또는 복잡한 분석 쿼리나 보고서를 빌드하는 경우와 달리 적은 양의 실시간 데이터를 쿼리하는 데 적합합니다. 쿼리 성능이 좋지 않은 경우 [실행 계획](https://docs.microsoft.com/sql/relational-databases/performance/display-an-actual-execution-plan)을 보고 원격 데이터베이스에 푸시되는 쿼리의 부분 및 반환되는 데이터의 양을 확인할 수 있습니다. 테넌트 데이터를 분석 쿼리에 최적화된 데이터베이스 또는 데이터 웨어하우스로 추출하는 경우 복잡한 집계 또는 분석 처리를 필요로 하는 쿼리가 효율적으로 제공됩니다. 이 패턴은 [테넌트 분석 자습서](saas-tenancy-tenant-analytics.md)에서 설명됩니다. 

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Wingtip Tickets SaaS Database Per Tenant 애플리케이션 스크립트 가져오기

Wingtip Tickets SaaS 다중 테넌트 데이터베이스 스크립트 및 애플리케이션 소스 코드는 [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) GitHub 리포지토리에서 확인할 수 있습니다. Wingtip Tickets SaaS 스크립트를 다운로드하고 차단을 해제하는 단계는 [일반 지침](saas-tenancy-wingtip-app-guidance-tips.md)을 확인하세요.

## <a name="create-ticket-sales-data"></a>티켓 판매 데이터 만들기

흥미로운 데이터 집합에 대해 쿼리를 실행하려면 티켓 생성기를 실행하여 티켓 판매 데이터를 만듭니다.

1. *PowerShell ISE*에서 \\학습 모듈\\운영 분석\\임시 보고\\*Demo-AdhocReporting.ps1* 스크립트를 열고 다음 값을 설정합니다.
   * **$DemoScenario** = 1, **모든 부문에서 이벤트 티켓을 구입합니다**.
2. **F5** 키를 눌러 스크립트를 실행하고 티켓 판매를 생성합니다. 스크립트를 실행하는 동안 이 자습서의 단계를 계속합니다. *임시 배포된 쿼리 실행* 섹션에서 티켓 데이터를 쿼리하기 때문에 티켓 생성자가 완료될 때까지 기다립니다.

## <a name="explore-the-global-views"></a>전역 뷰 탐색

Wingtip Tickets SaaS Database Per Tenant 애플리케이션에서는 각각의 테넌트에 데이터베이스가 부여됩니다. 따라서 데이터베이스 테이블에 담긴 데이터의 범위가 단일 테넌트 범위로 제한됩니다. 그러나 탄력적 쿼리는 모든 데이터베이스에 쿼리할 경우 테넌트 별로 분할된 단일 논리 데이터베이스의 일부인 경우와 같이 해당 데이터를 처리합니다. 

이 패턴을 시뮬레이션하기 위해 '전역' 뷰 집합이 전역적으로 쿼리되는 각 테이블에 테넌트 ID를 프로젝션하는 테넌트 데이터베이스에 추가됩니다. 예를 들어 *VenueEvents* 보기는 계산된 *VenueId*를 *이벤트* 테이블에서 프로젝션된 열에 추가합니다. 마찬가지로 *VenueTicketPurchases* 및 *VenueTickets* 뷰는 해당 테이블로부터 계산된 *VenueId* 열을 추가합니다. *VenueId* 열이 있으면 쿼리를 병렬화하여 적합한 원격 테넌트 데이터베이스에 푸시하기 위해 Elastic 쿼리에서 사용하는 뷰입니다. 이렇게 하면 반환되는 데이터의 양이 크게 줄어듭니다. 그 결과 많은 쿼리의 성능이 크게 증가합니다. 이러한 전역 보기는 모든 테넌트 데이터베이스에서 미리 생성됩니다.

1. SSMS를 열고 [테넌트1-&lt;사용자&gt; 서버에 연결](saas-tenancy-wingtip-app-guidance-tips.md#explore-database-schema-and-execute-sql-queries-using-ssms)합니다.
1. **데이터베이스**를 확장하고 _contosoconcerthall_을 마우스 오른쪽 단추로 클릭하고 **새 쿼리**를 선택합니다.
1. 다음 쿼리를 실행하여 단일 테넌트 테이블과 전역 뷰 간의 차이를 탐색합니다.

   ```T-SQL
   -- The base Venue table, that has no VenueId associated.
   SELECT * FROM Venue

   -- Notice the plural name 'Venues'. This view projects a VenueId column.
   SELECT * FROM Venues

   -- The base Events table, which has no VenueId column.
   SELECT * FROM Events

   -- This view projects the VenueId retrieved from the Venues table.
   SELECT * FROM VenueEvents
   ```

이러한 보기에서 *VenueId*는 부문 이름의 해시로 계산되지만 어떤 방법으로든 고유한 값을 도입할 수 있습니다. 이 방법은 카탈로그에서 사용하기 위해 테넌트 키를 계산하는 방식과 비슷합니다.

*부문* 보기의 정의를 확인하려면:

1. **개체 탐색기**에서 **contosoconcethall** > **보기**를 확장합니다.

   ![뷰](media/saas-tenancy-cross-tenant-reporting/views.png)

2. **dbo.Venues**를 마우스 오른쪽 단추로 클릭합니다.
3. **뷰 스크립팅** > **CREATE** > **새 쿼리 편집기 창**를 선택합니다.

다른 *부문* 보기를 스크립팅하여 *VenueId*를 추가하는 방법을 확인합니다.

## <a name="deploy-the-database-used-for-distributed-queries"></a>분산 쿼리에 사용되는 데이터베이스 배포

이 연습에서는 _adhocreporting_ 데이터베이스를 배포합니다. 모든 테넌트 데이터베이스에 쿼리하는 데 사용되는 스키마를 포함하는 헤드 데이터베이스입니다. 데이터베이스는 샘플 앱에서 모든 관리 관련 데이터베이스에 사용되는 서버인 기존 카탈로그 서버에 배포됩니다.

1. *PowerShell ISE*에서 \\학습 모듈\\운영 분석\\임시 보고\\*Demo-AdhocReporting.ps1*을 엽니다. 

1. 설정 **$DemoScenario = 2**하십시오 _배포 임시 보고 데이터베이스_합니다.

1. 스크립트를 실행하고 *adhocreporting* 데이터베이스를 만들려면 **F5** 키를 누릅니다.

다음 섹션에서 배포된 쿼리를 실행하는 데 사용할 수 있도록 데이터베이스에 스키마를 추가합니다.

## <a name="configure-the-head-database-for-running-distributed-queries"></a>배포된 쿼리를 실행하기 위한 'head' 데이터베이스 구성

이 연습에서는 모든 테넌트 데이터베이스에서 쿼리할 수 있도록 하는 _adhocreporting_ 데이터베이스에 스키마(외부 데이터 원본 및 외부 테이블 정의)를 추가합니다.

1. SQL Server Management Studio를 열고 이전 단계에서 만든 임시 보고 데이터베이스에 연결합니다. 데이터베이스의 이름은 *adhocreporting*입니다.
2. SSMS에서 \학습 모듈\운영 분석\임시 보고\ _Initialize-AdhocReportingDB.sql_을 엽니다.
3. SQL 스크립트를 검토하고 다음 사항을 참고합니다.

   탄력적인 쿼리는 데이터베이스 범위 자격 증명을 사용하여 각 테넌트 데이터베이스에 액세스합니다. 이 자격 증명은 모든 데이터베이스에서 사용할 수 있어야 하며 일반적으로 이러한 쿼리를 사용하는 데 필요한 최소 권한이 부여되어야 합니다.

    ![자격 증명 만들기](media/saas-tenancy-cross-tenant-reporting/create-credential.png)

   이 카탈로그 데이터베이스를 외부 데이터 원본으로 사용하면 쿼리가 실행되는 경우 카탈로그에 등록된 모든 데이터베이스에 쿼리가 배포됩니다. 각 배포에 대한 서버 이름이 다르기 때문에 이 스크립트는 스크립트가 실행되는 현재 서버(@@servername)에서 카탈로그 데이터베이스의 위치를 가져옵니다.

    ![외부 데이터 원본 만들기](media/saas-tenancy-cross-tenant-reporting/create-external-data-source.png)

   이전 섹션에서 설명한 전역 뷰를 참조하고 **DISTRIBUTION = SHARDED(VenueId)** 로 정의되는 외부 테이블입니다. 각 *VenueId*가 개별 데이터베이스에 매핑되기 때문에 다음 섹션에 표시된 대로 다양한 시나리오의 성능이 향상됩니다.

    ![외부 테이블 만들기](media/saas-tenancy-cross-tenant-reporting/external-tables.png)

   생성되고 채워진 로컬 테이블 _VenueTypes_. 이 참조 데이터 테이블은 모든 테넌트 데이터베이스에서 일반적이므로 여기에서 로컬 테이블로 표시되며 일반 데이터로 채워질 수 있습니다. 일부 쿼리의 경우, 헤드 데이터베이스에 정의된 이 테이블을 사용하여 헤드 데이터베이스로 이동해야 하는 데이터 양을 줄일 수 있습니다.

    ![테이블 만들기](media/saas-tenancy-cross-tenant-reporting/create-table.png)

   이러한 방식으로 참조 테이블을 포함하는 경우 테넌트 데이터베이스를 업데이트할 때마다 테이블 스키마 및 데이터를 업데이트해야 합니다.

4. 스크립트를 실행하고 *adhocreporting* 데이터베이스를 초기화하려면 **F5** 키를 누릅니다. 

이제 배포된 쿼리를 실행하고 모든 테넌트 간에 정보를 수집할 수 있습니다.

## <a name="run-distributed-queries"></a>분산 쿼리 실행

이제 *adhocreporting* 데이터베이스가 설정되었으므로 앞에서 배포된 몇 가지 쿼리를 실행합니다. 쿼리 처리가 발생하는 위치를 이해하는 실행 계획을 포함합니다. 

실행 계획을 검사할 때 자세한 내용을 보려면 계획 아이콘 위로 마우스를 가져갑니다. 

기억할 점으로, 외부 데이터 원본이 정의되었을 때 **DISTRIBUTION = SHARDED(VenueId)** 를 설정하면 여러 시나리오의 성능이 향상됩니다. 각 *VenueId* 개별 데이터베이스에 지도 쉽게 필터링 원격으로 필요한 데이터만 반환 합니다.

1. SSMS에서 \\학습 모듈\\운영 분석\\임시 보고\\*Demo-AdhocReportingQueries.sql*을 엽니다.
2. **adhocreporting** 데이터베이스에 연결되었는지 확인합니다.
3. **쿼리** 메뉴를 선택하고 **실제 실행 계획 포함**을 클릭합니다.
4. *현재 등록된 부문* 쿼리를 강조 표시하고 **F5** 키를 누릅니다.

   쿼리는 전체 부문 목록을 반환하고 모든 테넌트에 쉽고 빠르게 쿼리하는 방법 및 각 테넌트의 데이터를 반환하는 방법을 보여줍니다.

   계획을 검사하고 전체 비용이 원격 쿼리에 있는지 확인합니다. 각 테넌트 데이터베이스가 원격으로 쿼리를 실행하며, 헤드 데이터베이스에 장소 정보를 반환합니다.

   ![SELECT * FROM dbo.Venues](media/saas-tenancy-cross-tenant-reporting/query1-plan.png)

5. 다음 쿼리를 선택하고 **F5** 키를 누릅니다.

   이 쿼리는 테넌트 데이터베이스 및 로컬 *VenueTypes* 테이블의 데이터를 조인합니다(로컬인 *adhocreporting* 데이터베이스의 테이블임).

   계획을 검사하여 비용의 상당 부분이 원격 쿼리 때문에 발생하는지 확인합니다. 각 테넌트 데이터베이스는 해당 장소 정보를 반환하고 *VenueTypes* 테이블에 로컬 조인을 수행하여 익숙한 이름을 표시합니다.

   ![원격 및 로컬 데이터에 조인](media/saas-tenancy-cross-tenant-reporting/query2-plan.png)

6. 이제 *가장 많은 티켓을 판매한 날짜* 쿼리를 선택하고 **F5** 키를 누릅니다.

   이 쿼리는 좀 더 복잡한 조인 및 집계를 수행합니다. 대부분의 작업은 원격으로 처리됩니다.  각 장소의 일일 티켓에 대한 일별 판매 횟수가 포함된 단일 행만 헤드 데이터베이스로 반환됩니다.

   ![쿼리](media/saas-tenancy-cross-tenant-reporting/query3-plan.png)


## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> 
> * 모든 테넌트 데이터베이스에서 분산 쿼리 실행
> * 보고 데이터베이스를 배포하고 분산 쿼리를 실행하는 데 필요한 스키마를 정의합니다.


이제 [테넌트 분석 자습서](saas-tenancy-tenant-analytics.md)를 시도하여 복잡한 분석을 처리하기 위해 별도 분석 데이터베이스에 추출하는 데이터를 탐색합니다.

## <a name="additional-resources"></a>추가 리소스

* [Tickets SaaS Database Per Tenant 애플리케이션을 기반으로 빌드되는 추가 자습서](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [탄력적 쿼리](sql-database-elastic-query-overview.md)
