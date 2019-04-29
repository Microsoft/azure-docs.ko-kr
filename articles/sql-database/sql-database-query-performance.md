---
title: Azure SQL Database에 대한 Query Performance Insight | Microsoft Docs
description: 쿼리 성능 모니터링은 Azure SQL 데이터베이스에 대한 대부분의 CPU 사용 쿼리를 식별합니다.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
manager: craigg
ms.date: 01/03/2019
ms.openlocfilehash: 5d892005881436dec89c0d0d010f7f02e7bdebf9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60585379"
---
# <a name="query-performance-insight-for-azure-sql-database"></a>Azure SQL Database에 대한 Query Performance Insight

관계형 데이터베이스의 성능을 관리하고 조정하기 위해서는 전문 지식과 시간이 필요합니다. Query Performance Insight는 Azure SQL Database 지능형 성능 제품군의 일부입니다. 이를 통해 다음을 제공하여 데이터베이스 성능 문제 해결 시간을 줄일 수 있습니다.

* 데이터베이스 리소스(DTU) 사용에 대한 보다 자세한 정보를 확인합니다.
* CPU, 기간 및 실행 횟수(성능 향상에 대한 잠재적 조정 후보)에 따른 상위 데이터베이스 쿼리의 세부 정보.
* 리소스 사용률에 대한 텍스트 및 기록을 확인할 수 있도록 쿼리에 대한 세부 정보로 드릴다운하는 기능.
* [SQL Database Advisor](sql-database-advisor.md)에서 성능 권장 사항을 표시하는 주석.

![쿼리](./media/sql-database-query-performance/opening-title.png)

> [!TIP]
> Azure SQL Database를 사용한 기본 성능 모니터링의 경우 Query Performance Insight가 권장됩니다. 이 문서에 게시된 제품 제한 사항을 참고하세요. 규모가 큰 데이터베이스 성능의 고급 모니터링은 [Azure SQL Analytics](../azure-monitor/insights/azure-sql.md)가 권장됩니다. 자동화된 성능 문제 해결을 위한 인텔리전스가 기본 제공됩니다. 가장 일반적인 데이터베이스 성능 문제를 자동으로 조정하기 위해 [자동 조정](sql-database-automatic-tuning.md)이 권장됩니다.

## <a name="prerequisites"></a>필수 조건

Query Performance Insight를 위해서는 데이터베이스에서 [쿼리 저장소](https://msdn.microsoft.com/library/dn817826.aspx) 가 활성 상태여야 합니다. 이는 기본적으로 모든 Azure SQL 데이터베이스에 대해 자동으로 활성화됩니다. 쿼리 저장소를 실행하지 않는 경우 Azure Portal에서 사용하도록 설정하라는 메시지가 나타납니다.

> [!NOTE]
> 포털에서 “쿼리 저장소가 이 데이터베이스에서 올바르게 구성되지 않음” 메시지가 표시되는 경우 [쿼리 저장소 구성 최적화](#optimize-the-query-store-configuration-for-query-performance-insight)를 참조하세요.
>

## <a name="permissions"></a>권한

Query Performance Insight를 사용하려면 다음 [역할 기반 액세스 제어](../role-based-access-control/overview.md) 권한이 필요합니다.

* 최상위 리소스 사용 쿼리 및 차트를 보려면 **판독기**, **소유자**, **기여자**, **SQL DB 기여자** 또는 **SQL Server 기여자** 권한이 필요합니다.
* 쿼리 텍스트를 보려면 **소유자**, **참여자**, **SQL DB 참여자** 또는 **SQL Server 참여자** 권한이 필요합니다.

## <a name="use-query-performance-insight"></a>Query Performance Insight 사용

Query Performance Insight는 쉽게 사용할 수 있습니다.

1. [Azure Portal](https://portal.azure.com/)을 열고 검사하려는 데이터베이스를 찾습니다.
2. 왼쪽 메뉴에서 **지능형 성능** > **Query Performance Insight**를 엽니다.
  
   ![메뉴에 표시된 Query Performance Insight](./media/sql-database-query-performance/tile.png)

3. 첫 번째 탭에서 최상위 리소스 사용 쿼리의 목록을 검토합니다.
4. 해당하는 세부 정보를 보려면 개별 쿼리를 선택합니다.
5. **지능형 성능** > **성능 권장 사항**을 열고 성능 권장 사항을 사용할 수 있는지 확인합니다. 기본 제공 성능 권장 사항에 대한 자세한 내용은 [SQL Database Advisor](sql-database-advisor.md)를 참조하세요.
6. 슬라이더 또는 확대/축소 아이콘을 사용하여 관찰 간격을 변경합니다.

   ![성능 대시보드](./media/sql-database-query-performance/performance.png)

> [!NOTE]
> Query Performance Insight에서 정보를 렌더링하는 SQL Database의 경우 쿼리 저장소는 몇 시간의 데이터를 캡처해야 합니다. 데이터베이스에 아무런 작업이 없거나 쿼리 저장소가 특정 기간 동안 비활성 상태였다면 Query Performance Insight에서 해당 기간을 표시할 때 차트가 비어 있게 됩니다. 쿼리 저장소는 실행 중이 아닌 경우 언제든지 활성화할 수 있습니다. 자세한 내용은 [쿼리 저장소 모범 사례](https://docs.microsoft.com/sql/relational-databases/performance/best-practice-with-the-query-store)를 참조하세요.

## <a name="review-top-cpu-consuming-queries"></a>최상위 CPU 소비 쿼리 검토

기본적으로 Query Performance Insight는 처음 열 때 상위 5개의 CPU 사용 쿼리를 표시합니다.

1. 확인란을 사용하여 개별 쿼리를 선택하거나 삭제하면 차트에 추가시키거나 삭제할 수 있습니다.

    맨 윗 줄에는 데이터베이스에 대한 전체 DTU 백분율이 표시됩니다. 막대는 선택한 간격 동안 선택한 쿼리가 사용한 CPU 백분율을 보여줍니다. 예를 들어 경우 **지난주**를 선택하면 각 막대는 하루를 나타냅니다.

    ![상위 쿼리](./media/sql-database-query-performance/top-queries.png)

   > [!IMPORTANT]
   > 표시된 DTU 줄은 한 시간에 최대 사용량 값으로 집계됩니다. 이는 쿼리 실행 통계를 사용한 상위 수준 비교만을 위한 것입니다. 일부 경우에 DTU 사용률이 실행된 쿼리에 비해 너무 높아 보일 수도 있으나, 그렇지 않을 수도 있습니다.
   >
   > 예를 들어, 쿼리가 DTU를 단 몇 분 동안 100%로 최대화한 경우 Query Performance Insight의 DTU 줄은 전체 소비 시간을 100%로 표시하게 됩니다(최대 집계값의 결과).
   >
   > 더 세부적인 비교(최대 1분)는 사용자 지정 DTU 사용률 차트를 생성하는 것을 고려합니다.
   >
   > 1. Azure Portal에서 **Azure SQL Database** > **모니터링**을 선택합니다.
   > 2. **메트릭**을 선택합니다.
   > 3. **+차트 추가**를 선택합니다.
   > 4. 차트에서 DTU 백분율을 선택합니다.
   > 5. 또한 왼쪽 상단 메뉴에서 **최근 24시간**을 선택하고 이를 1분으로 변경합니다.
   >
   > 쿼리 실행 차트와 비교하려면 세부 정보 수준이 더 높은 사용자 정의 DTU 차트를 사용합니다.

   아래 그리드는 표시된 쿼리에 대해 집계된 정보를 나타냅니다.

   * 쿼리 ID는 데이터베이스에서 쿼리에 대한 고유 식별자입니다.
   * 집계 함수에 따라 다른 관찰 가능한 간격 동안의 쿼리당 CPU입니다.
   * 쿼리당 기간으로, 역시 집계 함수에 따라 다릅니다.
   * 특정 쿼리에 대한 총 실행 횟수입니다.

2. 데이터가 최신 상태가 아닌 경우 **새로 고침** 단추를 선택합니다.

3. 슬라이더 및 확대/축소 단추를 사용하여 관찰 간격을 변경하고 사용량 스파이크를 조사할 수 있습니다.

   ![간격 변경을 위한 슬라이더 및 확대/축소 단추](./media/sql-database-query-performance/zoom.png)

4. 필요에 따라 **사용자 지정** 탭을 선택하여 다음에 대한 보기를 사용자 지정할 수 있습니다.

   * 메트릭(CPU, 기간, 실행 횟수)
   * 시간 간격(지난 24시간, 지난주, 지난달)
   * 쿼리 수
   * 집계 함수
  
   ![사용자 지정 탭](./media/sql-database-query-performance/custom-tab.png)
  
5. 사용자 지정된 보기를 표시하려면 **이동 >** 단추를 선택합니다.

   > [!IMPORTANT]
   > Query Performance Insight는 사용자의 선택에 따라 상위 5 ~ 20개의 사용 쿼리를 표시하도록 제한됩니다. 데이터베이스는 표시된 상위 쿼리 외에 더 많은 쿼리를 실행할 수 있으며 이러한 쿼리는 차트에 포함되지 않습니다.
   >
   > 표시된 상위 쿼리 외에 순위가 낮은 쿼리 여러 개가 자주 실행되어 대부분의 DTU를 사용하는 데이터베이스 워크로드 유형이 있을 수 있습니다. 이러한 쿼리는 성능 차트에 표시되지 않습니다.
   >
   > 예를 들어, 쿼리는 상당량의 DTU를 한동안 소비했을 수 있지만, 관찰된 기간의 총 소비량은 다른 상위 소비 쿼리보다 적습니다. 이러한 경우 이 쿼리의 리소스 사용률은 차트에 표시되지 않습니다.
   >
   > Query Performance Insight의 제한을 초과하는 상위 쿼리 실행에 대해 파악해야 하는 경우 고급 데이터베이스 성능 모니터링 및 문제 해결을 위해 [Azure SQL Analytics](../azure-monitor/insights/azure-sql.md) 사용을 고려합니다.
   >

## <a name="view-individual-query-details"></a>개별 쿼리 세부 정보 보기

쿼리 세부 정보를 보려면:

1. 상위 쿼리 목록에서 쿼리를 선택합니다.

    ![상위 쿼리 목록](./media/sql-database-query-performance/details.png)

   자세히 보기가 열립니다. 시간에 따른 CPU 사용량, 기간 및 실행 횟수를 보여줍니다.

2. 세부 정보에 대한 차트 기능을 선택합니다.

   * 상위 차트에는 전체 데이터베이스 DTU 백분율이 포함된 줄이 표시됩니다. 막대는 선택한 쿼리가 사용한 CPU 백분율입니다.
   * 두 번째 차트에는 선택한 쿼리의 전체 기간이 표시됩니다.
   * 맨 아래 차트에는 선택한 쿼리의 전체 실행 횟수가 표시됩니다.

   ![쿼리 세부 정보](./media/sql-database-query-performance/query-details.png)

3. 필요에 따라 슬라이더, 확대/축소 단추를 사용하거나 **설정**을 선택하여 쿼리 데이터의 표시 방법을 사용자 지정하거나 다른 시간 범위를 지정합니다.

   > [!IMPORTANT]
   > Query Performance Insight는 DDL 쿼리를 캡처하지 않습니다. 경우에 따라 모든 임시 쿼리를 캡처하지 않을 수도 있습니다.
   >

## <a name="review-top-queries-per-duration"></a>시간별 최상위 쿼리 검토

Query Performance Insight의 기간 및 실행 수라는 두 메트릭은 잠재적인 병목 상태를 찾는 데 도움이 될 수 있습니다.

장기 실행 쿼리는 리소스를 더 오래 잠그고, 다른 사용자를 차단하고, 확장성을 제한할 가능성이 가장 높습니다. 또한 최적화에 가장 적합한 후보이기도 합니다.

장기 실행 쿼리를 식별하려면 다음을 수행합니다.

1. 선택한 데이터베이스에 대한 Query Performance Insight에서 **사용자 지정** 탭을 엽니다.
2. 메트릭을 **기간**으로 변경합니다.
3. 쿼리 수 및 관찰 간격을 선택합니다.
4. 집계 함수를 선택합니다.

   * **합계**는 전체 관찰 간격 동안 모든 쿼리 실행 시간을 합산합니다.
   * **최대**는 전체 관찰 간격 동안 실행 시간이 최대였던 쿼리를 찾습니다.
   * **평균**은 모든 쿼리 실행의 평균 실행 시간을 찾고 이러한 평균에서 상위 항목을 표시합니다.

   ![쿼리 기간](./media/sql-database-query-performance/top-duration.png)

5. 사용자 지정된 보기를 표시하려면 **이동 >** 단추를 선택합니다.

   > [!IMPORTANT]
   > 쿼리 보기를 조정하는 경우 DTU 줄을 업데이트되지 않습니다. DTU 줄은 항상 간격에 대한 최대 사용량 값을 표시합니다.
   >
   > 데이터베이스 DTU 사용량을 더 자세하게(최대 1분) 파악하려면 Azure Portal에서 사용자 지정 차트 만들기를 고려합니다.
   >
   > 1. **Azure SQL Database** > **모니터링**을 선택합니다.
   > 2. **메트릭**을 선택합니다.
   > 3. **+차트 추가**를 선택합니다.
   > 4. 차트에서 DTU 백분율을 선택합니다.
   > 5. 또한 왼쪽 상단 메뉴에서 **최근 24시간**을 선택하고 이를 1분으로 변경합니다.
   >
   > 쿼리 성능 차트를 비교하려면 사용자 지정 DTU 차트를 사용하는 것이 좋습니다.
   >

## <a name="review-top-queries-per-execution-count"></a>실행 횟수당 최상위 쿼리 검토

많은 수의 실행이 데이터베이스 자체에 영향을 미치지 않고 리소스 사용량이 낮다 하더라도, 데이터베이스를 사용하는 사용자 애플리케이션은 느려질 수 있다.

일부 경우에 실행 횟수가 높으면 네트워크 왕복이 증가할 수 있습니다. 왕복은 성능에 영향을 줍니다. 네트워크 대기 시간 및 다운스트림 서버 대기 시간이 발생할 수 있습니다.

예를 들어, 많은 데이터 기반 웹 사이트는 사용자 요청이 있을 때마다 데이터베이스에 과도하게 액세스합니다. 연결 풀링이 도움이 되지만, 데이터베이스 서버에서 네트워크 트래픽 및 처리 부하가 높아져서 성능이 저하될 수 있습니다. 일반적으로 왕복을 최소로 유지합니다.

자주 실행되는(“번잡한”) 쿼리를 식별하려면 다음을 수행합니다.

1. 선택한 데이터베이스에 대한 Query Performance Insight에서 **사용자 지정** 탭을 엽니다.
2. 메트릭을 **실행 횟수**로 변경합니다.
3. 쿼리 수 및 관찰 간격을 선택합니다.
4. 사용자 지정된 보기를 표시하려면 **이동 >** 단추를 선택합니다.

   ![쿼리 실행 횟수](./media/sql-database-query-performance/top-execution.png)

## <a name="understand-performance-tuning-annotations"></a>성능 조정 주석 이해

Query Performance Insight에서 워크로드를 살펴보는 동안 차트 상단에 있는 세로줄에 아이콘이 표시될 수 있습니다.

이러한 아이콘은 주석입니다. [SQL Database Advisor](sql-database-advisor.md)의 성능 권장 사항을 표시합니다. 주석을 마우스로 가리키면 성능 권장 사항에 대한 요약된 정보를 가져올 수 있습니다.

   ![쿼리 주석](./media/sql-database-query-performance/annotation.png)

Advisor 권장 사항에 대해 자세히 알아보거나 적용하려는 경우 아이콘을 선택하여 권장되는 작업의 세부 정보를 엽니다. 해당 아이콘이 활성 권장 사항인 경우 포털에서 바로 적용할 수 있습니다.

   ![쿼리 주석 세부 정보](./media/sql-database-query-performance/annotation-details.png)

경우에 따라 확대/축소 수준으로 인해 서로 가까이 있는 주석들이 하나의 주석으로 축소될 가능성이 있습니다. Query Performance Insight는 이를 그룹 주석 아이콘으로 나타냅니다. 그룹 주석 아이콘을 선택하면 주석을 나열하는 새 블레이드가 열립니다.

쿼리 및 성능 조정 작업의 상관 관계를 파악하면 워크로드를 더 잘 이해할 수 있습니다.

## <a name="optimize-the-query-store-configuration-for-query-performance-insight"></a>Query Performance Insight에 대한 쿼리 저장소 구성 최적화

Query Performance Insight를 사용하는 동안 다음 쿼리 저장소 오류 메시지가 나타날 수 있습니다.

* "이 데이터베이스의 쿼리 저장소가 적절하게 구성되어 있지 않습니다. 자세한 내용은 여기를 클릭하세요.
* "이 데이터베이스의 쿼리 저장소가 적절하게 구성되어 있지 않습니다. 여기를 클릭하여 설정을 변경하세요."

이러한 메시지는 일반적으로 쿼리 저장소가 새 데이터를 수집할 수 없는 경우 나타납니다.

첫 번째 경우는 쿼리 저장소가 읽기 전용 상태이고 매개 변수가 최적으로 설정되었을 때 발생합니다. 이 문제는 데이터 저장소의 크기를 늘리거나 쿼리 저장소를 지워서 해결할 수 있습니다. (쿼리 저장소를 지우는 경우 이전에 수집된 모든 원격 분석이 손실됩니다.)

   ![쿼리 저장소 세부 정보](./media/sql-database-query-performance/qds-off.png)

두 번째 경우는 쿼리 저장소가 사용하도록 설정되어 있지 않거나 매개 변수가 최적 상태로 설정되어 있지 않을 때 발생합니다. [SSMS(SQL Server Management Studio)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) 또는 Azure Portal에서 제공되는 다음 명령을 실행하여 보존 및 캡처 정책을 변경하고 쿼리 저장소를 사용하도록 설정할 수 있습니다.

### <a name="recommended-retention-and-capture-policy"></a>권장된 보존 및 캡처 정책

보존 정책에는 다음과 같은 두 종류가 있습니다.

* **크기 기반**: 이 정책이 **자동**으로 설정된 경우 최대 크기에 가까워지면 데이터를 자동으로 지웁니다.
* **시간 기반**: 기본적으로 이 정책은 30일로 설정되어 있습니다. 쿼리 저장소 공간이 부족하면 30일이 지난 쿼리 정보를 삭제합니다.

캡처 정책을 다음으로 설정할 수 있습니다.

* **모두**: 쿼리 저장소에서 모든 쿼리를 캡처합니다.
* **자동**: 쿼리 저장소에서 잘 사용하지 않는 쿼리와 컴파일 및 실행 기간이 적은 쿼리들을 무시합니다. 실행 횟수, 컴파일 기간 및 런타임 기간에 대한 임계값은 내부적으로 결정됩니다. 기본 옵션입니다.
* **없음**: 쿼리 저장소에서 새 쿼리의 캡처를 중지하지만 이미 캡처된 쿼리에 대한 런타임 통계는 계속 수집됩니다.

[SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) 또는 Azure Portal에서 다음 명령을 실행하여 모든 정책은 **자동**으로, 삭제 정책은 30일로 설정하는 것이 좋습니다. (`YourDB`를 데이터베이스 이름으로 바꿉니다.)

```sql
    ALTER DATABASE [YourDB]
    SET QUERY_STORE (SIZE_BASED_CLEANUP_MODE = AUTO);

    ALTER DATABASE [YourDB]
    SET QUERY_STORE (CLEANUP_POLICY = (STALE_QUERY_THRESHOLD_DAYS = 30));

    ALTER DATABASE [YourDB]
    SET QUERY_STORE (QUERY_CAPTURE_MODE = AUTO);
```

[SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) 또는 Azure Portal을 통해 데이터베이스에 연결하고 다음 쿼리를 실행하여 쿼리 저장소의 크기를 늘립니다. (`YourDB`를 데이터베이스 이름으로 바꿉니다.)

```T-SQL
    ALTER DATABASE [YourDB]
    SET QUERY_STORE (MAX_STORAGE_SIZE_MB = 1024);
```

이러한 설정을 적용하면 최종적으로 쿼리 저장소에서 새 쿼리에 대한 원격 분석을 수집하게 됩니다. 쿼리 저장소를 바로 작동시켜야 하는 경우 SSMS 또는 Azure Portal을 통해 다음 쿼리를 실행하여 쿼리 저장소를 제거하도록 선택할 수 있습니다. (`YourDB`를 데이터베이스 이름으로 바꿉니다.)

> [!NOTE]
> 다음 쿼리를 실행하면 쿼리 저장소에서 이전에 수집한 모니터링된 원격 분석을 모두 삭제합니다.

```SQL
    ALTER DATABASE [YourDB] SET QUERY_STORE CLEAR;
```

## <a name="summary"></a>요약

Query Performance Insight는 쿼리 워크로드의 영향을 파악하고 그것이 데이터베이스 리소스 사용과 어떻게 관련되는지를 이해하는 데 도움이 됩니다. 이 기능을 통해 데이터베이스에서 사용량이 높은 쿼리에 대해 알아보고, 문제가 되기 전에 최적화할 쿼리를 찾을 수 있습니다.

## <a name="next-steps"></a>다음 단계

* 데이터베이스 성능 권장 사항의 경우 Query Performance Insight 탐색 블레이드에서 [권장 사항](sql-database-advisor.md)을 선택합니다.

    ![권장 사항 탭](./media/sql-database-query-performance/ia.png)

* 일반적인 데이터베이스 성능 문제의 경우 [자동 조정](sql-database-automatic-tuning.md)을 사용하도록 설정하는 것을 고려합니다.
* [Intelligent Insights](sql-database-intelligent-insights.md)가 어떻게 자동으로 데이터베이스 성능 문제를 해결하는 데 도움이 되는지에 대해 알아봅니다.
* 기본 제공 인텔리전스를 사용한 SQL Databases, 탄력적 풀 및 Managed Instances의 대규모 고급 성능 모니터링은 [Azure SQL Analytics]( ../azure-monitor/insights/azure-sql.md) 사용을 고려합니다.
