---
title: 여러 Azure SQL 데이터베이스에 대해 임시 보고 쿼리 실행 | Microsoft Docs
description: 다중 테넌트 앱 예에서 여러 SQL Database에 대해 임시 보고 쿼리를 실행합니다.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: AyoOlubeko
ms.author: ayolubek
ms.reviewer: sstein
manager: craigg
ms.date: 10/30/2018
ms.openlocfilehash: d4c5a2ca88f982626c8c2a8b37e4a7d6dfdbe599
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61485826"
---
# <a name="run-ad-hoc-analytics-queries-across-multiple-azure-sql-databases"></a>여러 Azure SQL 데이터베이스에 대해 임시 분석 쿼리 실행

이 자습서에서는 테넌트 데이터베이스의 전체 집합에서 배포된 쿼리를 실행하여 임시 대화형 보고를 사용하도록 설정합니다. 이러한 쿼리는 Wingtip Tickets SaaS 앱의 일일 운영 데이터에 담긴 정보를 추출할 수 있습니다. 이러한 추출을 수행하려면 추가 분석 데이터베이스를 카탈로그 서버에 배포하고 탄력적 쿼리를 사용하여 배포된 쿼리를 사용하도록 설정합니다.


이 자습서에서는 다음에 대해 알아봅니다.

> [!div class="checklist"]
> 
> * 임시 보고 데이터베이스를 배포하는 방법
> * 모든 테넌트 데이터베이스 간에 분산 쿼리를 실행하는 방법


이 자습서를 수행하려면 다음 필수 조건이 완료되었는지 확인합니다.

* Wingtip Tickets SaaS 다중 테넌트 데이터베이스 앱이 배포되어 있어야 합니다. 5분 내에 배포하려면 [Wingtip Tickets SaaS 다중 테넌트 데이터베이스 애플리케이션 배포 및 탐색](saas-multitenantdb-get-started-deploy.md)을 참조하세요.
* Azure PowerShell이 설치되었습니다. 자세한 내용은 [Azure PowerShell 시작](https://docs.microsoft.com/powershell/azure/get-started-azureps)을 참조하세요.
* SSMS(SQL Server Management Studio)가 설치되었습니다. SSMS를 다운로드하고 설치하려면 [SSMS(SQL Server Management Studio) 다운로드](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)를 참조하세요.


## <a name="ad-hoc-reporting-pattern"></a>임시 보고 패턴

![임시 보고 패턴](media/saas-multitenantdb-adhoc-reporting/adhocreportingpattern_shardedmultitenantDB.png)

SaaS 애플리케이션은 클라우드에 중앙 집중식으로 저장되는 방대한 분량의 테넌트 데이터를 분석할 수 있습니다. 분석을 통해 애플리케이션의 사용 및 작업에 대한 정보를 얻습니다. 이러한 정보는 기능 개발, 활용성 향상 및 기타 앱 및 서비스 투자를 유도할 수 있습니다.

단일 다중 테넌트 데이터베이스에 있을 때 이 데이터에 액세스하는 것은 쉽지만 잠재적인 수천 개의 데이터베이스 규모로 분산되는 경우는 그렇게 쉽지 않습니다. 한 가지 방법은 공통 스키마를 통해 배포된 데이터베이스 집합 전체에서 쿼리를 구현하는 [탄력적 쿼리](sql-database-elastic-query-overview.md)를 사용하는 것입니다. 이러한 데이터베이스는 여러 다른 리소스 그룹 및 구독에서 배포할 수 있습니다. 아직 하나의 공통 로그인으로 모든 데이터베이스에서 데이터를 추출하기 위한 액세스 권한을 얻어야 합니다. 탄력적 쿼리는 분산된(테넌트) 데이터베이스에서 테이블 또는 뷰를 미러링하는 외부 테이블이 정의되는 단일 *헤드* 데이터베이스를 사용합니다. 이 헤드 데이터베이스에 제출된 쿼리는 분산 쿼리 계획을 생성하기 위해 컴파일되며 이 쿼리 중 일부는 필요에 따라 테넌트 데이터베이스에 푸시됩니다. 탄력적 쿼리는 카탈로그 데이터베이스에서 분할된 데이터베이스 맵을 사용하여 모든 테넌트 데이터베이스의 위치를 확인합니다. 설정 및 쿼리는 일반 [Transact-SQL](https://docs.microsoft.com/sql/t-sql/language-reference)을 사용하여 바로 진행되며 Power BI 또는 Excel 같은 도구에서의 임시 쿼리를 지원합니다.

탄력적 쿼리는 테넌트 데이터베이스에 쿼리를 배포하여 라이브 프로덕션 데이터에 즉시 정보를 제공합니다. 그러나 탄력적 쿼리가 잠재적으로 많은 데이터베이스에서 데이터를 가져오면 쿼리 대기 시간이 단일 다중 테넌트 데이터베이스에 제출되는 해당 쿼리보다 높아지는 경우가 있습니다. 반환되는 데이터를 최소화하도록 쿼리를 디자인합니다. 탄력적 쿼리는 자주 사용되는 문서 또는 복잡한 분석 쿼리나 보고서를 빌드하는 경우와 달리 적은 양의 실시간 데이터를 쿼리하는 데 적합합니다. 쿼리 성능이 좋지 않은 경우 [실행 계획](https://docs.microsoft.com/sql/relational-databases/performance/display-an-actual-execution-plan)을 보고 원격 데이터베이스에 푸시되는 쿼리의 부분을 확인합니다. 또한 반환되는 데이터의 양을 평가합니다. 추출한 테넌트 데이터를 분석 쿼리에 최적화된 데이터베이스에 저장하는 경우 복잡한 분석 처리를 필요로 하는 쿼리가 효율적으로 제공됩니다. SQL Database 및 SQL Data Warehouse는 이러한 분석 데이터베이스를 호스트할 수 있습니다.

분석에 대한 이 패턴은 [테넌트 분석 자습서](saas-multitenantdb-tenant-analytics.md)에서 설명되어 있습니다.

## <a name="get-the-wingtip-tickets-saas-multi-tenant-database-application-source-code-and-scripts"></a>Wingtip Tickets SaaS 다중 테넌트 데이터베이스 애플리케이션 소스 코드 및 스크립트 가져오기

Wingtip Tickets SaaS 다중 테넌트 데이터베이스 스크립트 및 애플리케이션 소스 코드는 [WingtipTicketsSaaS-MultitenantDB](https://github.com/microsoft/WingtipTicketsSaaS-MultiTenantDB) GitHub 리포지토리에서 확인할 수 있습니다. Wingtip Tickets SaaS 스크립트를 다운로드하고 차단을 해제하는 단계는 [일반 지침](saas-tenancy-wingtip-app-guidance-tips.md)을 확인하세요.

## <a name="create-ticket-sales-data"></a>티켓 판매 데이터 만들기

흥미로운 데이터 집합에 대해 쿼리를 실행하려면 티켓 생성기를 실행하여 티켓 판매 데이터를 만듭니다.

1. *PowerShell ISE*에서 \\학습 모듈\\운영 분석\\임시 보고\\*Demo-AdhocReporting.ps1* 스크립트를 열고 다음 값을 설정합니다.
   * **$DemoScenario** = 1, **모든 부문에서 이벤트 티켓을 구입합니다**.
2. **F5** 키를 눌러 스크립트를 실행하고 티켓 판매를 생성합니다. 스크립트를 실행하는 동안 이 자습서의 단계를 계속합니다. *임시 배포된 쿼리 실행* 섹션에서 티켓 데이터를 쿼리하기 때문에 티켓 생성자가 완료될 때까지 기다립니다.

## <a name="explore-the-tenant-tables"></a>테넌트 테이블 탐색 

Wingtip Tickets SaaS 다중 테넌트 데이터베이스 애플리케이션에서 테넌트는 하이브리드 테넌트 관리 모델에 저장됩니다. 여기서 테넌트 데이터는 다중 테넌트 데이터베이스 또는 단일 테넌트 데이터베이스에 저장되며 둘 간에 이동될 수 있습니다. 탄력적 쿼리는 모든 테넌트 데이터베이스에 쿼리할 경우 테넌트 별로 분할된 단일 논리 데이터베이스의 일부인 경우와 같이 해당 데이터를 처리합니다. 

이 패턴을 달성하기 위해 모든 테넌트 테이블에는 데이터가 속하는 테넌트를 식별하는 *VenueId* 열이 포함되어 있습니다. *VenueId*는 부문 이름의 해시로 계산되지만 어떤 방법으로든 이 열에 대한 고유한 값을 도입할 수 있습니다. 이 방법은 카탈로그에서 사용하기 위해 테넌트 키를 계산하는 방식과 비슷합니다. *VenueId*를 포함하는 테이블은 탄력적 쿼리에서 쿼리를 병렬화하고 해당 원격 테넌트 데이터베이스로 푸시하는 데 사용됩니다. 이를 통해 반환되는 데이터 양이 크기 감소되며, 결과적으로 특히 데이터가 단일 테넌트 데이터베이스에 저장되는 여러 개의 테넌트가 있는 경우에 성능이 향상됩니다.

## <a name="deploy-the-database-used-for-ad-hoc-distributed-queries"></a>임시 배포된 쿼리에 사용되는 데이터베이스 배포

이 연습에서는 *adhocreporting* 데이터베이스를 배포합니다. 모든 테넌트 데이터베이스에 쿼리하는 데 사용되는 스키마를 포함하는 헤드 데이터베이스입니다. 데이터베이스는 샘플 앱에서 모든 관리 관련 데이터베이스에 사용되는 서버인 기존 카탈로그 서버에 배포됩니다.

1. *PowerShell ISE*에서 \\학습 모듈\\운영 분석\\임시 보고\\*Demo-AdhocReporting.ps1*을 열고 다음 값을 설정합니다.
   * **$DemoScenario** = 2, **임시 분석 데이터베이스 배포**.

2. 스크립트를 실행하고 *adhocreporting* 데이터베이스를 만들려면 **F5** 키를 누릅니다.

다음 섹션에서 배포된 쿼리를 실행하는 데 사용할 수 있도록 데이터베이스에 스키마를 추가합니다.

## <a name="configure-the-head-database-for-running-distributed-queries"></a>배포된 쿼리를 실행하기 위한 'head' 데이터베이스 구성

이 연습에서는 모든 테넌트 데이터베이스에서 쿼리할 수 있도록 하는 임시 보고 데이터베이스에 스키마(외부 데이터 원본 및 외부 테이블 정의)를 추가합니다.

1. SQL Server Management Studio를 열고 이전 단계에서 만든 임시 보고 데이터베이스에 연결합니다. 데이터베이스의 이름은 *adhocreporting*입니다.
2. SSMS에서 \학습 모듈\운영 분석\임시 보고\ *Initialize-AdhocReportingDB.sql*을 엽니다.
3. SQL 스크립트를 검토하고 다음을 참고합니다.

   탄력적인 쿼리는 데이터베이스 범위 자격 증명을 사용하여 각 테넌트 데이터베이스에 액세스합니다. 이 자격 증명은 모든 데이터베이스에서 사용할 수 있어야 하며 일반적으로 이러한 임시 쿼리를 사용하는 데 필요한 최소 권한이 부여되어야 합니다.

    ![자격 증명 만들기](media/saas-multitenantdb-adhoc-reporting/create-credential.png)

   이 카탈로그 데이터베이스를 외부 데이터 원본으로 사용하면 쿼리가 실행되는 경우 카탈로그에 등록된 모든 데이터베이스에 쿼리가 배포됩니다. 각 배포에 대한 서버 이름이 다르기 때문에 이 초기화 스크립트는 스크립트가 실행되는 현재 서버(@@servername)를 검색하여 카탈로그 데이터베이스의 위치를 가져옵니다.

    ![외부 데이터 원본 만들기](media/saas-multitenantdb-adhoc-reporting/create-external-data-source.png)

   테넌트 테이블을 참조하는 외부 테이블은 **DISTRIBUTION = SHARDED(VenueId)** 로 정의합니다. 이 경우 특정 *VenueId*에 대한 쿼리 경로가 해당 데이터베이스로 지정되어 다음 섹션에 표시되는 것처럼 많은 시나리오에서 성능이 향상됩니다.

    ![외부 테이블 만들기](media/saas-multitenantdb-adhoc-reporting/external-tables.png)

   생성되고 채워진 로컬 테이블 *VenueTypes*. 이 참조 데이터 테이블은 모든 테넌트 데이터베이스에서 일반적이므로 여기에서 로컬 테이블로 표시되며 일반 데이터로 채워질 수 있습니다. 이 일부 쿼리의 경우 테넌트 데이터베이스와 *adhocreporting* 데이터베이스 간에 이동되는 데이터의 양을 줄일 수 있습니다.

    ![테이블 만들기](media/saas-multitenantdb-adhoc-reporting/create-table.png)

   이러한 방식으로 참조 테이블을 포함하는 경우 테넌트 데이터베이스를 업데이트할 때마다 테이블 스키마 및 데이터를 업데이트해야 합니다.

4. 스크립트를 실행하고 *adhocreporting* 데이터베이스를 초기화하려면 **F5** 키를 누릅니다. 

이제 배포된 쿼리를 실행하고 모든 테넌트 간에 정보를 수집할 수 있습니다.

## <a name="run-ad-hoc-distributed-queries"></a>임시 배포된 쿼리 실행

이제 *adhocreporting* 데이터베이스가 설정되었으므로 앞에서 배포된 몇 가지 쿼리를 실행합니다. 쿼리 처리가 발생하는 위치를 이해하는 실행 계획을 포함합니다. 

실행 계획을 검사할 때 자세한 내용을 보려면 계획 아이콘 위로 마우스를 가져갑니다. 

1. *SSMS*에서 \\학습 모듈\\운영 분석\\임시 보고\\*Demo-AdhocReportingQueries.sql*을 엽니다.
2. **adhocreporting** 데이터베이스에 연결되었는지 확인합니다.
3. **쿼리** 메뉴를 선택하고 **실제 실행 계획 포함**을 클릭합니다.
4. *현재 등록된 부문* 쿼리를 강조 표시하고 **F5** 키를 누릅니다.

   쿼리는 전체 부문 목록을 반환하고 모든 테넌트에 쉽고 빠르게 쿼리하는 방법 및 각 테넌트의 데이터를 반환하는 방법을 보여줍니다.

   각 테넌트 데이터베이스로 이동하고 부문 정보를 선택하기 때문에 계획을 검사하고 전체 비용이 원격 쿼리인지를 확인합니다.

   ![SELECT * FROM dbo.Venues](media/saas-multitenantdb-adhoc-reporting/query1-plan.png)

5. 다음 쿼리를 선택하고 **F5** 키를 누릅니다.

   이 쿼리는 테넌트 데이터베이스 및 로컬 *VenueTypes* 테이블의 데이터를 조인합니다(로컬인 *adhocreporting* 데이터베이스의 테이블임).

   각 테넌트의 부문 정보(dbo.Venues)를 쿼리한 다음 *VenueTypes* 테이블과 빠르게 로컬 조인하여 친숙한 이름을 표시하기 때문에 계획을 검사하고 비용의 대부분이 원격 쿼리인지 확인합니다.

   ![원격 및 로컬 데이터에 조인](media/saas-multitenantdb-adhoc-reporting/query2-plan.png)

6. 이제 *가장 많은 티켓을 판매한 날짜* 쿼리를 선택하고 **F5** 키를 누릅니다.

   이 쿼리는 좀 더 복잡한 조인 및 집계를 수행합니다. 대부분 처리를 원격으로 수행하는 것이 중요합니다. 또한 여기서는 다시 필요한 행만을 가져오고 일당 각 부문의 집계 티켓 판매 개수의 단일 행만을 반환합니다.

   ![쿼리](media/saas-multitenantdb-adhoc-reporting/query3-plan.png)


## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> 
> * 모든 테넌트 데이터베이스에서 분산 쿼리 실행
> * 임시 보고 데이터베이스를 배포하고 스키마를 추가하여 배포된 쿼리를 실행합니다.

이제 [테넌트 분석 자습서](saas-multitenantdb-tenant-analytics.md)를 시도하여 복잡한 분석을 처리하기 위해 별도 분석 데이터베이스에 추출하는 데이터를 탐색합니다.

## <a name="additional-resources"></a>추가 리소스

<!-- ??
* Additional [tutorials that build upon the Wingtip Tickets SaaS Multi-tenant Database application](saas-multitenantdb-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
-->

* [탄력적 쿼리](sql-database-elastic-query-overview.md)
