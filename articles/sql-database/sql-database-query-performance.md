---
title: Azure SQL Database에 대한 쿼리 성능 Insight | Microsoft Docs
description: 쿼리 성능 모니터링은 Azure SQL Database에 대한 대부분의 CPU 사용 쿼리를 식별합니다.
services: sql-database
author: stevestein
manager: craigg
ms.service: sql-database
ms.custom: monitor & tune
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: sstein
ms.openlocfilehash: f608beb0834b1c838b082e92340ebf9b650d8b3f
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34648737"
---
# <a name="azure-sql-database-query-performance-insight"></a>Azure SQL Database Query Performance Insight
관련 데이터베이스의 성능을 관리하고 튜닝하는 것은 많은 전문 지식과 시간 투자를 필요로 하는 어려운 일입니다. Query Performance Insight를 통해 다음을 제공하여 데이터베이스 성능 문제 해결 시간을 줄일 수 있습니다.

* 데이터베이스 리소스(DTU) 사용에 대한 보다 자세한 정보를 확인합니다. 
* CPU/기간/실행 횟수별 최상위 쿼리는 향상된 성능을 위해 잠재적으로 조정될 수 있습니다.
* 쿼리에 대한 세부 정보로 드릴다운하는 기능으로, 리소스 사용률에 대한 텍스트 및 기록을 표시합니다. 
* [SQL Azure 데이터베이스 관리자](sql-database-advisor.md)  



## <a name="prerequisites"></a>필수 조건
* Query Performance Insight를 위해서는 데이터베이스에서 [쿼리 저장소](https://msdn.microsoft.com/library/dn817826.aspx) 가 활성 상태여야 합니다. 쿼리 저장소가 실행되지 않는 경우 저장소를 켜라는 포털 메시지가 표시됩니다.

## <a name="permissions"></a>권한
Query Performance Insight를 사용하려면 다음 [역할 기반 액세스 제어](../role-based-access-control/overview.md) 권한이 필요합니다. 

* 최상위 리소스 사용 쿼리 및 차트를 보려면 **판독기**, **소유자**, **참여자**, **SQL DB 참여자** 또는 **SQL Server 참여자** 권한이 필요합니다. 
* 쿼리 텍스트를 보려면 **소유자**, **참여자**, **SQL DB 참여자** 또는 **SQL Server 참여자** 권한이 필요합니다.

## <a name="using-query-performance-insight"></a>Query Performance Insight 사용
Query Performance Insight는 쉽게 사용할 수 있습니다.

* [Azure 포털](https://portal.azure.com/) 을 열고 검사하려는 데이터베이스를 찾습니다. 
  * 왼쪽 메뉴의 지원 및 문제 해결에서 "Query Performance Insight"를 선택합니다.
* 첫 번째 탭에서 최상위 리소스 사용 쿼리의 목록을 검토합니다.
* 해당하는 세부 정보를 보려면 개별 쿼리를 선택합니다.
* [SQL Azure 데이터베이스 관리자](sql-database-advisor.md) 를 열고 권장 사항을 사용할 수 있는지 확인합니다.
* 슬라이더를 또는 확대/축소 아이콘을 사용하여 관찰 간격을 변경합니다.
  
    ![성능 대시보드](./media/sql-database-query-performance/performance.png)

> [!NOTE]
> Query Performance Insight를 제공하는 SQL Database용 쿼리 저장소로 데이터를 캡처하는 데 몇 시간이 필요합니다. 데이터베이스에 아무런 작업이 없거나 쿼리 저장소가 특정 기간 동안 비활성 상태였던 경우 해당 기간을 표시할 때 차트가 비어 있게 됩니다. 실행하지 않는 경우 언제든지 쿼리 저장소를 활성화할 수 있습니다.   
> 
> 

## <a name="review-top-cpu-consuming-queries"></a>최상위 CPU 소비 쿼리 검토
[포털](http://portal.azure.com) 에서 다음을 수행합니다.

1. SQL 데이터베이스로 이동한 후 **모든 설정** > **지원 + 문제 해결** > **Query Performance Insight**를 클릭합니다. 
   
    ![쿼리][1]
   
    최상위 쿼리 뷰가 열리고 최상위 CPU 사용 쿼리가 나열됩니다.
2. 자세한 내용은 차트 주위를 클릭합니다.<br>위쪽 줄에는 데이터베이스에 대한 전체 DTU%가 표시되고 막대에는 선택한 기간 중에 선택한 쿼리에서 사용된 CPU%가 표시됩니다. 예를 들어 **지난주**를 선택하면 각 막대는 1일을 나타냅니다.
   
    ![최상위 쿼리][2]
   
    아래 그리드는 표시되는 쿼리에 대해 집계된 정보를 나타냅니다.
   
   * 쿼리 ID – 데이터베이스 내 쿼리의 고유 식별자입니다.
   * 예측 가능한 간격 동안 쿼리당 CPU입니다(집계 함수에 따라 다름).
   * 쿼리당 기간입니다(집계 함수에 따라 다름).
   * 특정 쿼리에 대한 총 실행 수입니다.
     
     확인란을 사용하여 개별 쿼리를 선택하거나 지워 차트에서 포함하거나 제외합니다.
3. 데이터가 최신 상태가 아닌 경우 **새로 고침** 단추를 클릭합니다.
4. 슬라이더 및 확대/축소 단추를 사용하여 관찰 간격을 변경하고 스파이크를 조사할 수 있습니다. ![설정](./media/sql-database-query-performance/zoom.png)
5. 필요에 따라 다른 보기를 원할 경우 **사용자 지정** 탭을 선택하고 다음을 설정합니다.
   
   * 메트릭(CPU, 기간, 실행 횟수)
   * 시간 간격(지난 24시간, 지난주, 지난달) 
   * 쿼리 수
   * 집계 함수
     
     ![설정](./media/sql-database-query-performance/custom-tab.png)

## <a name="viewing-individual-query-details"></a>개별 쿼리 세부 정보 보기
쿼리 세부 정보를 보려면:

1. 맨 위 쿼리 목록에서 쿼리를 클릭합니다.
   
    ![세부 정보](./media/sql-database-query-performance/details.png)
2. 세부 정보 보기가 열리고 쿼리 CPU 사용/기간/실행 횟수가 시간에 따라 분석됩니다.
3. 자세한 내용은 차트 주위를 클릭합니다.
   
   * 맨 위 차트에는 전체 데이터베이스 DTU %가 선으로 표시되고, 선택한 쿼리에서 사용되는 CPU %가 막대로 표시됩니다.
   * 두 번째 차트에는 선택한 쿼리의 전체 기간이 표시됩니다.
   * 맨 아래 차트에는 선택한 쿼리의 전체 실행 횟수가 표시됩니다.
     
     ![쿼리 세부 정보][3]
4. 필요에 따라 슬라이더, 확대/축소 단추를 사용하거나 **설정** 을 클릭하여 쿼리 데이터 표시 방법을 사용자 지정하거나 다른 기간을 표시합니다.

## <a name="review-top-queries-per-duration"></a>기간당 최상위 쿼리 검토
Query Performance Insight의 최신 업데이트에는 잠재적인 병목 상태를 식별하는 데 도움이 되는 두 가지 새로운 메트릭인 기간과 실행 횟수가 제공됩니다.<br>

장기 실행 쿼리는 리소스를 더 오래 잠그고, 다른 사용자를 차단하고, 확장성을 제한할 가능성이 가장 높습니다. 또한 최적화에 가장 적합한 후보이기도 합니다.<br>

장기 실행 쿼리를 식별하려면 다음을 수행합니다.

1. 선택한 데이터베이스에 대한 Query Performance Insight에서 **사용자 지정** 탭 열기
2. 메트릭을 **기간**
3. 쿼리 수 및 관찰 간격 선택
4. 집계 함수 선택
   
   * **Sum** 은 전체 관찰 간격 동안 모든 쿼리 실행 시간을 합합니다.
   * **Max** 는 전체 관찰 간격에는 실행 시간이 최대였던 쿼리를 찾습니다.
   * **Avg** 는 모든 쿼리 실행의 평균 실행 시간을 찾은 후 이러한 평균을 초과하는 항목을 보여 줍니다. 
     
     ![쿼리 기간][4]

## <a name="review-top-queries-per-execution-count"></a>실행 횟수 최상위 쿼리 검토
실행 횟수가 높을 경우 데이터베이스 자체에는 영향을 주지 않고 리소스 사용량이 적어질 수 있지만 전체 응용 프로그램이 느려질 수도 있습니다.

일부 경우에 실행 횟수가 매우 높을 경우 네트워크 왕복이 증가할 수 있습니다. 왕복은 성능에 지대한 영향을 미칩니다. 네트워크 대기 시간 및 다운스트림 서버 대기 시간이 발생할 수 있습니다. 

예를 들어, 많은 데이터 기반 웹 사이트는 사용자 요청이 있을 때마다 데이터베이스에 과도하게 액세스합니다. 연결 풀링이 도움이 되지만, 데이터베이스 서버에서 네트워크 트래픽 및 처리 부하가 높아져 성능이 저하될 수 있습니다.  일반적으로는 왕복을 최소로 유지하는 것이 좋습니다.

자주 실행되는 쿼리("번잡한") 쿼리를 식별하려면

1. 선택한 데이터베이스에 대한 Query Performance Insight에서 **사용자 지정** 탭 열기
2. 메트릭을 **실행 횟수**
3. 쿼리 수 및 관찰 간격 선택
   
    ![쿼리 실행 횟수][5]

## <a name="understanding-performance-tuning-annotations"></a>성능 튜닝 주석 이해
Query Performance Insight에서 워크로드를 살펴보는 동안 차트 맨 위에 세로줄이 있는 아이콘이 표시될 수 있습니다.<br>

이러한 아이콘은 주석으로, [SQL Azure 데이터베이스 관리자](sql-database-advisor.md)가 수행한 작업에 영향을 미치는 성능을 나타냅니다. 주석에 마우스를 가져가면 해당 작업에 대한 기본 정보가 표시됩니다.

![쿼리 주석][6]

더 자세히 알고 싶거나 관리자 권장 구성을 적용하려는 경우 이 아이콘을 클릭합니다. 작업의 세부 정보가 열립니다. 활성 권장 사항은 명령을 사용하여 바로 적용할 수 있습니다.

![쿼리 주석 세부 정보][7]

### <a name="multiple-annotations"></a>여러 주석.
확대/축소 수준으로 인해 서로 가까이 있는 주석은 하나로 축소되어 표시될 수 있습니다. 이러한 경우 특수 아이콘이 표시됩니다. 이 아이콘을 클릭하면 그룹화된 주석 목록이 표시되는 새 블레이드가 열립니다.
쿼리 및 성능 조정 작업의 상관 관계를 파악하면 워크로드를 파악하는 데 도움이 됩니다. 

## <a name="optimizing-the-query-store-configuration-for-query-performance-insight"></a>Query Performance Insight에 대한 쿼리 저장소 구성 최적화
Query Performance Insight를 사용하는 동안 다음 쿼리 저장소 메시지가 나타날 수 있습니다.

* "이 데이터베이스의 쿼리 저장소가 적절하게 구성되어 있지 않습니다. 자세한 내용은 여기를 클릭하세요.
* "이 데이터베이스의 쿼리 저장소가 적절하게 구성되어 있지 않습니다. 여기를 클릭하여 설정을 변경하세요." 

이러한 메시지는 일반적으로 쿼리 저장소가 새 데이터를 수집할 수 없을 때 표시됩니다. 

첫 번째 경우는 쿼리 저장소가 읽기 전용 상태이고 매개 변수가 최적으로 설정되었을 때 발생합니다. 쿼리 저장소의 크기를 늘리거나 쿼리 저장소를 지워서 이 문제를 해결할 수 있습니다.

![qds 단추][8]

두 번째 경우는 쿼리 저장소가 꺼져 있거나 매개 변수가 최적 상태로 설정되어 있지 않을 때 발생합니다. <br>아래 명령을 실행하거나 포털에서 직접 보존 및 캡처 정책을 변경하고 쿼리 저장소를 사용하도록 설정할 수 있습니다.

![qds 단추][9]

### <a name="recommended-retention-and-capture-policy"></a>권장된 보존 및 캡처 정책
보존 정책에는 다음과 같은 두 종류가 있습니다.

* 크기 기반 - AUTO로 설정된 경우 최대 크기에 가까워지면 데이터를 자동으로 지웁니다.
* 시간 기반 - 기본적으로 30일로 설정됩니다. 즉, 쿼리 저장소의 공간이 부족해지면 30일이 지난 쿼리 정보를 삭제합니다.

캡처 정책은 다음과 같이 설정할 수 있습니다.

* **모두** – 모든 쿼리를 캡처합니다.
* **자동** – 컴파일 및 실행 기간이 중요하지 않고 사용 빈도가 적은 쿼리가 무시됩니다. 실행 횟수, 컴파일 및 런타임 기간에 대한 임계값을 내부적으로 결정합니다. 기본 옵션입니다.
* **없음** – 쿼리 저장소에서 새 쿼리의 캡처를 중지하지만 이미 캡처된 쿼리에 대한 런타임 통계는 계속 수집됩니다.

모든 정책을 AUTO로 설정하고 클린 정책을 30일로 설정하는 것이 좋습니다.

    ALTER DATABASE [YourDB] 
    SET QUERY_STORE (SIZE_BASED_CLEANUP_MODE = AUTO);

    ALTER DATABASE [YourDB] 
    SET QUERY_STORE (CLEANUP_POLICY = (STALE_QUERY_THRESHOLD_DAYS = 30));

    ALTER DATABASE [YourDB] 
    SET QUERY_STORE (QUERY_CAPTURE_MODE = AUTO);

쿼리 저장소의 크기를 늘립니다. 이는 데이터베이스에 연결하고 다음 쿼리를 실행하여 수행할 수 있습니다.

    ALTER DATABASE [YourDB]
    SET QUERY_STORE (MAX_STORAGE_SIZE_MB = 1024);

하지만 이러한 설정을 적용하면 결과적으로 쿼리 저장소가 새 쿼리를 수집하게 되지만, 기다리는 것을 원치 않을 경우 쿼리 저장소를 지울 수 있습니다. 

> [!NOTE]
> 다음 쿼리를 실행하면 쿼리 저장소의 모든 현재 정보가 삭제됩니다. 
> 
> 

    ALTER DATABASE [YourDB] SET QUERY_STORE CLEAR;


## <a name="summary"></a>요약
쿼리 성능 Insight를 통해 쿼리 작업의 영향 및 데이터베이스 리소스 사용의 관계를 이해할 수 있습니다. 이 기능을 사용하여 최상위 사용 쿼리를 확인하고 문제가 되기 전에 해결할 쿼리를 쉽게 식별할 수 있습니다.

## <a name="next-steps"></a>다음 단계
SQL 데이터베이스의 성능 향상에 관한 추가 권장 사항은 [Query Performance Insight](sql-database-advisor.md) 블레이드의 **권장 사항** 을 클릭하여 확인합니다.

![성능 관리자](./media/sql-database-query-performance/ia.png)

<!--Image references-->
[1]: ./media/sql-database-query-performance/tile.png
[2]: ./media/sql-database-query-performance/top-queries.png
[3]: ./media/sql-database-query-performance/query-details.png
[4]: ./media/sql-database-query-performance/top-duration.png
[5]: ./media/sql-database-query-performance/top-execution.png
[6]: ./media/sql-database-query-performance/annotation.png
[7]: ./media/sql-database-query-performance/annotation-details.png
[8]: ./media/sql-database-query-performance/qds-off.png
[9]: ./media/sql-database-query-performance/qds-button.png

