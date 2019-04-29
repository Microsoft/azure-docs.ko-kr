---
title: Intelligent Insights를 사용하여 데이터베이스 성능 모니터링 - Azure SQL Database | Microsoft Docs
description: Azure SQL Database Intelligent Insights는 기본 제공 인텔리전스를 사용하여 지속적으로 인공 지능을 통해 데이터베이스 사용량을 모니터링하고 성능 저하의 원인이 되는 중단 이벤트를 검색합니다.
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
ms.date: 12/19/2018
ms.openlocfilehash: 15154844c954e53ca1add5d3fbaa3e9d02152ad2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60703214"
---
# <a name="intelligent-insights-using-ai-to-monitor-and-troubleshoot-database-performance"></a>AI를 사용하여 데이터베이스 성능을 모니터링하고 문제를 해결하는 Intelligent Insights

Azure SQL Database Intelligent Insights를 통해 SQL Databases 및 Managed Instance 데이터베이스 성능에 어떤 일이 발생했는지 알 수 있습니다.

Intelligent Insights는 기본 제공 인텔리전스를 사용하여 지속적으로 인공 지능을 통해 데이터베이스 사용량을 모니터링하고 성능 저하의 원인이 되는 중단 이벤트를 검색합니다. 검색되면 이를 상세하게 분석하여 문제에 대한 지능형 평가와 함께 진단 로그를 생성합니다. 이 평가는 데이터베이스 성능 문제의 근본 원인 분석과 성능 개선을 위한 가능한 권장 사항이 있는 위치로 구성되어 있습니다.

## <a name="what-can-intelligent-insights-do-for-you"></a>Intelligent Insights가 수행할 수 있는 작업

Intelligent Insights는 다음 값을 제공하는 Azure의 기본 제공 인텔리전스의 고유 기능입니다.

- 사전 모니터링
- 맞춤형 성능 정보
- 데이터베이스 성능 저하의 초기 검색
- 검색된 문제의 근본 원인 분석
- 성능 개선 권장 사항
- 수십만 개의 데이터베이스에서 기능 확장
- DevOps 리소스와 총 소유 비용에 미치는 긍정적인 영향

## <a name="how-does-intelligent-insights-work"></a>Intelligent Insights의 작동 방식

Intelligent Insights는 마지막 1시간의 데이터베이스 워크로드와 지난 7일의 기준 워크로드를 비교하여 데이터베이스 성능을 분석합니다. 데이터베이스 워크로드는 가장 많이 반복되고 가장 큰 쿼리와 같이 데이터베이스 성능에 가장 중요하다고 판단되는 쿼리로 구성됩니다. 생성된 각 워크로드 기준이 개별 인스턴스에 대해 특정적이고 고유하기 때문에 각 데이터베이스는 구조, 데이터, 사용량 및 애플리케이션에 따라 고유합니다. 워크로드 기준과는 별도로 Intelligent Insights는 또한 절대 작업 임계값을 모니터링하고 과도한 대기 시간 문제, 심각한 예외, 성능에 영향을 줄 수 있는 쿼리 매개 변수화의 문제를 감지합니다.

인공 지능을 사용하여 관찰된 여러 개의 메트릭에서 성능 저하 문제가 검색되면 분석이 수행됩니다. 데이터베이스 상황을 지능적으로 분석한 진단 로그가 생성됩니다. 인텔리전스 Insights를 사용하면 데이터베이스 성능 문제가 처음 출현할 때부터 해결될 때까지 쉽게 추적할 수 있습니다. 검색된 각 문제는 최초 문제 검색부터, 성능 개선 확인, 완료의 주기에서 추적됩니다.

![데이터베이스 성능 분석 워크플로](./media/sql-database-intelligent-insights/intelligent-insights-concept.png)

데이터베이스 성능 문제를 측정하고 검색하는 데 사용되는 메트릭은 쿼리 기간, 시간 제한 요청, 과도한 대기 시간, 오류 발생 요청을 기반으로 합니다. 메트릭에 대한 자세한 내용은 이 문서의 [검색 메트릭](sql-database-intelligent-insights.md#detection-metrics) 섹션을 참조하세요.

확인된 SQL Database 성능 저하는 다음 속성으로 구성된 지능형 항목과 함께 진단 로그에 기록됩니다.

| 자산             | 세부 정보              |
| :------------------- | ------------------- |
| 데이터베이스 정보 | 리소스 URI와 같이 정보가 검색된 데이터베이스에 대한 메타데이터 |
| 관찰된 시간 범위 | 검색된 정보 기간에 대한 시작 및 종료 시간 |
| 영향을 받는 메트릭 | 정보를 생성하게 된 메트릭: <ul><li>쿼리 기간 증가[초].</li><li>과도한 대기[초].</li><li>시간 제한 요청[백분율].</li><li>오류 발생 요청[백분율].</li></ul>|
| 영향 값 | 측정된 메트릭 값. |
| 영향 받는 쿼리 및 오류 코드 | 쿼리 해시 또는 오류 코드입니다. 영향을 받는 쿼리에 상관 관계를 적용하는 데 쉽게 사용할 수 있습니다. 쿼리 기간 증가, 대기 시간, 시간 제한 수 또는 오류 코드로 구성된 메트릭이 제공됩니다. |
| 검색 | 이벤트의 시간 동안 데이터베이스에서 식별된 검색합니다. 15가지 검색 패턴이 있습니다. 자세한 내용은 [인텔리전스 Insights를 사용하여 데이터베이스 성능 문제 해결](sql-database-intelligent-insights-troubleshoot-performance.md)을 참조하세요. |
| 근본 원인 분석 | 인간이 읽을 수 있는 형식으로 식별된 문제의 근본 원인 분석입니다. 가능한 경우 일부 정보에는 성능 개선 권장 사항이 있을 수 있습니다. |
|||

Azure SQL Analytics에서 Intelligent Insights를 사용하는 방법에 대한 실무 중심 개요와 일반적인 사용 시나리오는 포함된 비디오를 참조하세요.

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Get-Intelligent-Insights-for-Improving-Azure-SQL-Database-Performance/player]
>

Intelligent Insights는 SQL Database 성능 문제를 검색하고 해결할 때 특히 유용합니다. Intelligent Insights를 사용하여 SQL Database 및 Managed Instance 데이터베이스 성능 문제를 해결하려면 [Intelligent Insights를 사용하여 Azure SQL Database 성능 문제 해결](sql-database-intelligent-insights-troubleshoot-performance.md)을 참조하세요.

## <a name="configure-intelligent-insights"></a>Intelligent Insights 구성

Intelligent Insights의 출력은 스마트 성능 진단 로그를 사용합니다. 이 로그를 여러 방법으로(Azure SQL Analytics, Azure Event Hubs 및 Azure 저장소를 통해 또는 타사 제품을 통해) 사용할 수 있습니다.

- 이 제품을 [Azure SQL Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-sql)와 함께 사용하여 Azure Portal의 사용자 인터페이스를 통해 인사이트를 볼 수 있습니다. 이 제품은 통합된 Azure 솔루션이며, 인사이트를 살펴보는 가장 일반적인 방법입니다.
- Azure Event Hubs와 함께 사용하여 사용자 지정 모니터링 및 경고 시나리오를 개발할 수 있습니다.
- 이 제품을 Azure 저장소와 함께 사용하여 사용자 지정 보고, 장기 데이터 보관 등의 사용자 지정 애플리케이션 개발할 수 있습니다.

Intelligent Insights를 Azure SQL Analytics, Azure Event Hubs, Azure Storage 또는 소비자용 타사 제품과 통합하려면 먼저 데이터베이스의 [진단] 설정 블레이드에서 Intelligent Insights 로깅("SQLInsights" 로그)을 사용하도록 설정한 다음, 이러한 제품 중 하나로 스트리밍되도록 Intelligent Insights 로그 데이터를 구성합니다.

Intelligent Insights 로깅을 사용하도록 설정하고 로그 데이터가 사용 중인 제품에 스트리밍되도록 구성하는 방법에 대한 자세한 내용은 [Azure SQL Database 메트릭 및 진단 로깅](sql-database-metrics-diag-logging.md)을 참조하세요.

### <a name="set-up-with-azure-sql-analytics"></a>Azure SQL Analytics를 사용하여 설정

Azure SQL Analytics 솔루션은 Intelligent Insights 진단 로그 데이터와 함께 데이터베이스 성능에 대한 그래픽 사용자 인터페이스, 보고 및 경고 기능을 제공합니다.

> [!TIP]
> 빠른 시작: Intelligent Insights를 시작하는 가장 쉬운 방법은 데이터베이스 성능 문제에 대한 그래픽 사용자 인터페이스를 제공하는 Azure SQL 분석과 함께 사용하는 것입니다. 마켓플레이스에서 Azure SQL Analytics 솔루션을 추가하고, 이 솔루션 내에서 작업 영역을 만든 다음, Intelligent Insights를 사용하도록 설정할 각 데이터베이스에 대해 데이터베이스의 [진단 설정] 블레이드에서 Azure SQL analytics 작업 영역으로의 "SQLInsights" 로그 스트리밍을 구성합니다.
>

사전 요구 사항으로 마켓플레이스에서 Azure SQL Analytics를 Azure Portal 대시보드에 추가하고 작업 영역을 만들어야 합니다. 자세한 내용은 [Azure SQL Analytics 구성](../azure-monitor/insights/azure-sql.md#configuration)을 참조하세요.

Azure SQL Analytics에서 Intelligent Insights를 사용하고 이전 단계에서 만든 Azure SQL Analytics 작업 영역으로 스트리밍되도록 Intelligent Insights 로그 데이터를 구성하려면 [Azure SQL Database 메트릭 및 진단 로깅](sql-database-metrics-diag-logging.md)을 참조하세요.

다음은 Azure SQL Analytics를 통해 Intelligent Insights를 살펴보는 예제입니다.

![Intelligent Insights 보고서](./media/sql-database-intelligent-insights/intelligent-insights-azure-sql-analytics.png)

### <a name="set-up-with-event-hubs"></a>Event Hubs를 사용하여 설정

Event Hubs에서 Intelligent Insights를 사용하고 Event Hubs로 스트리밍되도록 Intelligent Insights 로그 데이터를 구성하려면 [Event Hubs로 Azure 진단 로그 스트리밍](../azure-monitor/platform/diagnostic-logs-stream-event-hubs.md)을 참조하세요.

Event Hubs를 사용하여 사용자 지정 모니터링 및 경고를 설정하려면 [Event Hubs에서 메트릭 및 진단 로그로 할 수 있는 일](sql-database-metrics-diag-logging.md#what-to-do-with-metrics-and-diagnostics-logs-in-event-hubs)을 참조하세요.

### <a name="set-up-with-azure-storage"></a>Azure Storage를 사용하여 설정

Storage에서 Intelligent Insights를 사용하고 Storage로 스트리밍되도록 Intelligent Insights 로그 데이터를 구성하려면 [Azure Storage로 스트리밍](sql-database-metrics-diag-logging.md#stream-into-storage)을 참조하세요.

### <a name="custom-integrations-of-intelligent-insights-log"></a>인텔리전스 Insights 로그의 사용자 지정 통합

타사 도구와 함께 또는 사용자 지정 경고 및 모니터링 개발을 위해 Intelligent Insights를 사용하려면 [Intelligent Insights 데이터베이스 성능 진단 로그 사용](sql-database-intelligent-insights-use-diagnostics-log.md)을 참조하세요.

## <a name="detection-metrics"></a>검색 메트릭

인텔리전스 Insights를 생성하는 검색 모델에 사용되는 메트릭은 다음과 같은 모니터링을 기반으로 합니다.

- 쿼리 기간
- 시간 제한 요청
- 과도한 대기 시간
- 오류 발생 요청

쿼리 기간 및 시간 제한 요청은 데이터베이스 워크로드 성능 문제를 검색하는 데 기본 모델로서 사용됩니다. 검색 메트릭을 사용하는 이유는 워크로드의 상황을 직접 측정하기 때문입니다. 워크로드 성능 저하의 가능한 모든 사례를 검색하기 위해 과도한 대기 시간 및 오류 발생 요청이 워크로드 성능에 영향을 주는 문제를 나타내는 추가 모델로 사용됩니다.

시스템은 워크로드의 변화와 데이터베이스에 적용된 쿼리 요청 수의 변경을 자동으로 고려하여 데이터베이스 성능 임계값이 정상적인지 범위를 벗어났는지를 동적으로 판단합니다.

과학적으로 파생된 데이터 모델을 통해 모든 메트릭을 다양한 관계에서 고려하여 각 성능 문제를 검색하여 분류합니다. Intellient Insights를 통해 제공되는 정보의 예를 들면 다음과 같습니다.

- 검색된 성능 문제의 세부 정보.
- 검색된 문제의 근본 원인 분석.
- 모니터링되는 SQL 데이터베이스의 성능을 향상(가능한 경우)하는 방법에 대한 권장 사항

## <a name="query-duration"></a>쿼리 기간

쿼리 기간 저하 모델은 개별 쿼리를 분석하고 성능 기준과 비교하여 쿼리를 컴파일하고 실행하는 데 걸리는 시간의 증가를 검색합니다.

SQL Database 기본 제공 인텔리전스가 워크로드 성능에 영향을 주는 쿼리 컴파일 또는 쿼리 실행 시간이 크게 증가한 것을 검색한 경우, 이러한 쿼리는 쿼리 기간 성능 저하 문제가 있음을 나타내기 위해 플래그 지정됩니다.

Intelligent Insights 진단 로그는 성능이 저하된 쿼리의 쿼리 해시를 출력합니다. 쿼리 해시는 성능 저하가 쿼리 컴파일 또는 실행 시간 증가로 인한 쿼리 지속 시간과 관련되어 있는지 여부를 나타냅니다.

## <a name="timeout-requests"></a>시간 제한 요청

시간 제한 요청 저하 모델은 개별 쿼리를 분석하고 쿼리 실행 수준에서 시간 제한의 증가, 그리고 성능 기준 기간과 비교하여 데이터베이스 수준에서 전체 요청 시간 제한을 감지합니다.

일부 쿼리는 실행 단계에 도달하기 전에 시간이 제한될 수 있습니다. SQL Database 기본 제공 인텔리전스는 중단된 작업자 및 수신된 요청을 통해 실행 단계에 도달했는지 여부와 상관없이 데이터베이스에 도달한 모든 쿼리를 측정 및 분석합니다.

실행된 쿼리의 시간 제한 횟수 또는 중단된 요청 작업자의 수가 시스템에서 관리하는 임계값을 초과하는 경우 진단 로그가 Intelligent Insights로 채워집니다.

생성된 정보에는 시간 제한된 요청의 수 및 시간 제한된 쿼리의 수가 포함됩니다. 성능 저하가 실행 단계에서 시간 제한이 증가한 것과 관련이 있는지 여부 또는 전반적 데이터베이스 수준이 제공됩니다. 시간 제한의 증가가 데이터베이스 성능에 중요한 것으로 간주되는 경우 이러한 쿼리는 시간 제한 성능 저하 문제로 플래그 지정됩니다.

## <a name="excessive-wait-times"></a>과도한 대기 시간

과도한 대기 시간 모델은 개별 데이터베이스 쿼리를 모니터링합니다. 이 모델은 시스템에서 관리하는 절대 임계값을 초과해 비정상적으로 높은 쿼리 대기 통계를 검색합니다. 새로운 SQL Server 기능인 쿼리 저장소 대기 통계(sys.query_store_wait_stats) 기능을 활용하여 다음과 같은 쿼리의 과도한 대기 시간 메트릭을 관찰합니다.

- 리소스 제한에 도달
- 탄력적 풀 리소스 제한에 도달
- 과도한 작업자 또는 세션 스레드 수
- 과도한 데이터베이스 잠금
- 메모리 압력
- 다른 대기 통계

리소스 제한이나 탄력적 풀 리소스 제한에 도달하면 구독 또는 탄력적 풀에서 사용할 수 있는 리소스 소모량이 절대 임계값 이상으로 증가했음을 나타냅니다. 이러한 통계는 워크로드 성능 저하를 나타냅니다. 과도한 작업자 또는 세션 스레드 수는 시작된 작업자 스레드 또는 세션 수가 절대 임계값 이상에 도달한 상태를 나타냅니다. 이러한 통계는 워크로드 성능 저하를 나타냅니다.

과도한 데이터베이스 잠금은 데이터베이스에서 잠금 수가 절대 임계값 이상에 도달한 상태를 나타냅니다. 이 통계는 워크로드 성능 저하를 나타냅니다. 메모리 압력은 메모리 부여를 요청하는 스레드의 수가 절대 임계값을 초과한 상태입니다. 이 통계는 워크로드 성능 저하를 나타냅니다.

다른 대기 통계 검색은 쿼리 저장소 대기 통계를 통해 측정된 기타 메트릭이 절대 임계값 이상에 도달한 상태를 나타냅니다. 이러한 통계는 워크로드 성능 저하를 나타냅니다.

과도한 대기 시간이 감지되면 사용 가능한 데이터에 따라 Intelligent Insights 진단 로그에서 성능 저하에 영향을 주거나 영향을 받은 쿼리의 해시, 쿼리에서 실행 대기 시간의 원인이 된 메트릭의 세부 정보, 측정된 대기 시간을 출력합니다.

## <a name="errored-requests"></a>오류 발생 요청

오류 발생 요청 저하 모델은 개별 쿼리를 모니터링하고 기준 기간과 비교하여 오류가 발생한 쿼리 수의 증가를 검색합니다. 이 모델은 또한 SQL Database 기본 제공 인텔리전스에서 관리되는 절대 임계값을 초과한 중요 예외를 모니터링합니다. 시스템은 모니터링되는 기간에 데이터베이스에 적용된 쿼리 요청과 워크로드의 변화에 대한 계정의 수를 자동으로 고려합니다.

적용된 전체 요청 수와 관련하여 측정된 오류 발생 요청의 증가가 워크로드 성능에 중요한 것으로 간주되는 경우 영향을 받는 쿼리는 오류 발생 요청 성능 저하 문제가 있음을 나타내기 위해 플래그 지정됩니다.

Intelligent Insights 로그는 오류 발생 요청의 수를 출력합니다. 여기에는 성능 저하가 오류 발생 요청 증가와 관련되어 있는지, 또는 모니터링되는 중요 예외 임계값 초과 및 성능 저하 측정 시간과 관련되어 있는지를 나타냅니다.

모니터링되는 중요 예외가 시스템에서 관리하는 절대 임계값을 초과하는 경우 중요 예외 정보와 함께 Intelligent Insights가 생성됩니다.

## <a name="next-steps"></a>다음 단계

- [Intelligent Insights를 사용하여 SQL Database 성능 문제를 해결](sql-database-intelligent-insights-troubleshoot-performance.md)하는 방법을 알아봅니다.
- [Intelligent Insights의 SQL Database 성능 진단 로그](sql-database-intelligent-insights-use-diagnostics-log.md)를 사용합니다.
- [SQL Analytics를 사용하여 SQL Database를 모니터링](../azure-monitor/insights/azure-sql.md)하는 방법을 알아봅니다.
- [Azure 리소스에서 로그 데이터 수집 및 소비](../azure-monitor/platform/diagnostic-logs-overview.md)하는 방법을 알아봅니다.
