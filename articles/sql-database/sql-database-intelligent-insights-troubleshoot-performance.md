---
title: Intelligent Insights를 사용하여 Azure SQL Database 성능 문제 해결 | Microsoft Docs
description: Intelligent Insights는 Azure SQL Database 성능 문제를 해결하는 데 도움을 줍니다.
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
ms.date: 01/25/2019
ms.openlocfilehash: fff4aa947f878974d2d0f18f373b8c0917ed7d70
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60703514"
---
# <a name="troubleshoot-azure-sql-database-performance-issues-with-intelligent-insights"></a>Intelligent Insights를 사용하여 Azure SQL Database 성능 문제 해결

이 페이지에서는 [Intelligent Insights](sql-database-intelligent-insights.md) 데이터베이스 성능 진단 로그를 통해 감지되는 Azure SQL Database 및 Managed Instance 성능 문제에 대한 정보를 제공합니다. 진단 로그 원격 분석을 스트리밍할 수 있습니다 [Azure Monitor 로그](../azure-monitor/insights/azure-sql.md)를 [Azure Event Hubs](../azure-monitor/platform/diagnostic-logs-stream-event-hubs.md)합니다 [Azure Storage](sql-database-metrics-diag-logging.md#stream-into-storage), 또는 타사 솔루션에 사용자 지정 DevOps 경고 및 보고 기능을 제공 합니다.

> [!NOTE]
> Intelligent Insights를 사용한 빠른 SQL Database 성능 문제 해결 가이드는 이 문서의 [권장되는 문제 해결 흐름](sql-database-intelligent-insights-troubleshoot-performance.md#recommended-troubleshooting-flow) 순서도를 참조하세요.
>

## <a name="detectable-database-performance-patterns"></a>검색 가능한 데이터베이스 성능 패턴

Intelligent Insights는 쿼리 실행 대기 시간, 오류 또는 시간 제한을 기준으로 SQL Database 및 Managed Instance 데이터베이스의 성능 문제를 자동으로 감지합니다. 감지된 성능 패턴을 진단 로그로 출력합니다. 검색 가능한 성능 패턴은 다음 테이블에 요약되어 있습니다.

| 검색 가능한 성능 패턴 | Azure SQL Database 및 탄력적 풀에 대한 설명 | Managed Instance에서 데이터베이스에 대한 설명 |
| :------------------- | ------------------- | ------------------- |
| [리소스 제한에 도달](sql-database-intelligent-insights-troubleshoot-performance.md#reaching-resource-limits) | 리소스(DTU), 데이터베이스 작업자 스레드 또는 모니터링된 구독에서 사용 가능한 데이터베이스 로그인 세션의 사용량이 제한에 도달하였습니다. 이는 SQL Database 성능에 영향을 줍니다. | CPU 리소스의 사용량이 Managed Instance 제한에 도달합니다. 이는 데이터베이스 성능에 영향을 줍니다. |
| [워크로드 증가](sql-database-intelligent-insights-troubleshoot-performance.md#workload-increase) | 데이터베이스의 워크로드가 증가하거나 지속적으로 누적되었음이 발견되었습니다. 이는 SQL Database 성능에 영향을 줍니다. | 워크로드 증가가 감지되었습니다. 이는 데이터베이스 성능에 영향을 줍니다. |
| [메모리 압력](sql-database-intelligent-insights-troubleshoot-performance.md#memory-pressure) | 메모리 부여를 요청한 작업자는 통계적으로 유의미한 시간 동안 메모리 할당을 대기해야 합니다. 또는 메모리 부여를 요청한 작업자가 지속적으로 누적되어 존재합니다. 이는 SQL Database 성능에 영향을 줍니다. | 메모리 부여를 요청한 작업자는 통계적으로 유의미한 시간 동안 메모리 할당을 대기합니다. 이는 데이터베이스 성능에 영향을 줍니다. |
| [잠금](sql-database-intelligent-insights-troubleshoot-performance.md#locking) | SQL Database 성능에 영향을 주는 과도한 데이터베이스 잠금이 발견되었습니다. | 데이터베이스 성능에 영향을 주는 과도한 데이터베이스 잠금이 발견되었습니다. |
| [MAXDOP 증가](sql-database-intelligent-insights-troubleshoot-performance.md#increased-maxdop) | MAXDOP(최대 병렬 처리 수준 옵션)이 변경되었으며 쿼리 실행 효율성에 영향을 주고 있습니다. 이는 SQL Database 성능에 영향을 줍니다. | MAXDOP(최대 병렬 처리 수준 옵션)이 변경되었으며 쿼리 실행 효율성에 영향을 주고 있습니다. 이는 데이터베이스 성능에 영향을 줍니다. |
| [페이지 래치 경합](sql-database-intelligent-insights-troubleshoot-performance.md#pagelatch-contention) | 여러 스레드가 동일한 메모리 내 데이터 버퍼 페이지에 동시에 액세스하려고 시도하여 페이지 래치 경합이 발생하고 있습니다. 이는 SQL Database 성능에 영향을 줍니다. | 여러 스레드가 동일한 메모리 내 데이터 버퍼 페이지에 동시에 액세스하려고 시도하여 페이지 래치 경합이 발생하고 있습니다. 이는 데이터베이스 성능에 영향을 줍니다. |
| [인덱스 누락](sql-database-intelligent-insights-troubleshoot-performance.md#missing-index) | SQL Database 성능에 영향을 주는 인덱스 누락이 발견되었습니다. | 데이터베이스 성능에 영향을 주는 인덱스 누락이 발견되었습니다. |
| [새 쿼리](sql-database-intelligent-insights-troubleshoot-performance.md#new-query) | SQL Database의 전체 성능에 영향을 주는 새 쿼리가 발견되었습니다. | 데이터베이스의 전체 성능에 영향을 주는 새 쿼리가 발견되었습니다. |
| [대기 통계 증가](sql-database-intelligent-insights-troubleshoot-performance.md#increased-wait-statistic) | SQL Database 성능에 영향을 주는 데이터베이스 대기 시간 증가되었습니다. | 데이터베이스 성능에 영향을 주는 데이터베이스 대기 시간 증가되었습니다. |
| [TempDB 경합](sql-database-intelligent-insights-troubleshoot-performance.md#tempdb-contention) | 여러 스레드가 동일한 TempDB 리소스에 액세스를 시도하여 병목 현상이 발생하고 있습니다. 이는 SQL Database 성능에 영향을 줍니다. | 여러 스레드가 동일한 TempDB 리소스에 액세스를 시도하여 병목 현상이 발생하고 있습니다. 이는 데이터베이스 성능에 영향을 줍니다. |
| [탄력적 풀 DTU 부족](sql-database-intelligent-insights-troubleshoot-performance.md#elastic-pool-dtu-shortage) | 탄력적 풀에서 사용 가능한 eDTU가 부족하여 SQL Database 성능에 영향을 주고 있습니다. | vCore 모델을 사용하므로 Managed Instance에서 사용할 수 없습니다. |
| [계획 회귀](sql-database-intelligent-insights-troubleshoot-performance.md#plan-regression) | 새 계획 또는 기존 계획의 워크로드가 변경되었습니다. 이는 SQL Database 성능에 영향을 줍니다. | 새 계획 또는 기존 계획의 워크로드가 변경되었습니다. 이는 데이터베이스 성능에 영향을 줍니다. |
| [데이터베이스 범위 구성 값 변경](sql-database-intelligent-insights-troubleshoot-performance.md#database-scoped-configuration-value-change) | SQL Database의 구성 변경으로 인해 데이터베이스 성능에 영향을 주고 있습니다. | 데이터베이스의 구성 변경으로 인해 데이터베이스 성능에 영향을 주고 있습니다. |
| [느린 클라이언트](sql-database-intelligent-insights-troubleshoot-performance.md#slow-client) | 느린 애플리케이션 클라이언트는 데이터베이스의 출력을 충분히 신속하게 사용할 수 없습니다. 이는 SQL Database 성능에 영향을 줍니다. | 느린 애플리케이션 클라이언트는 데이터베이스의 출력을 충분히 신속하게 사용할 수 없습니다. 이는 데이터베이스 성능에 영향을 줍니다. |
| [가격 책정 계층 다운그레이드](sql-database-intelligent-insights-troubleshoot-performance.md#pricing-tier-downgrade) | 가격 책정 계층 다운그레이드 작업으로 사용 가능한 리소스가 감소되었습니다. 이는 SQL Database 성능에 영향을 줍니다. | 가격 책정 계층 다운그레이드 작업으로 사용 가능한 리소스가 감소되었습니다. 이는 데이터베이스 성능에 영향을 줍니다. |

> [!TIP]
> SQL Database에 대해 연속적으로 성능을 최적화하려면 [Azure SQL Database 자동 튜닝](sql-database-automatic-tuning.md)을 활성화하세요. SQL Database에 기본 제공되는 이 인텔리전스 기능은 SQL 데이터베이스를 지속적으로 모니터링하고 인덱스를 자동으로 조정하며 쿼리 실행 계획의 수정 사항을 적용합니다.
>

다음 섹션에서는 검색 가능한 성능 패턴에 대해 자세히 설명합니다.

## <a name="reaching-resource-limits"></a>리소스 제한에 도달

### <a name="what-is-happening"></a>설명

이 검색 가능한 성능 패턴은 사용 가능한 리소스 제한, 작업자 제한, 세션 제한 도달과 관련된 성능 문제를 결합합니다. 이 성능 문제가 감지되면 진단 로그의 설명 필드에 성능 문제가 리소스, 작업자 또는 세션 제한 중 어디에 관련되어 있는지가 표시됩니다.

SQL Database의 리소스는 일반적으로 [DTU](sql-database-what-is-a-dtu.md) 또는 [vCore](sql-database-service-tiers-vcore.md) 리소스라고 합니다. 리소스 제한 도달 패턴은 측정된 리소스 제한 중 하나에 도달하여 쿼리 성능 저하가 발견될 때 인식됩니다.

이러한 세션 한도는 SQL 데이터베이스에 대해 사용 가능한 동시 로그인 수를 지정합니다. 이 성능 패턴은 SQL 데이터베이스에 연결하는 애플리케이션이 데이터베이스에 사용 가능한 동시 로그인 수에 도달하는 경우에 인식됩니다. 애플리케이션이 데이터베이스에서 사용할 수 있는 것보다 많은 세션을 사용하려고 하면 쿼리 성능에 영향을 줍니다.

사용 가능한 작업자 수는 DTU 또는 vCore 사용량에 포함되지 않으므로 작업자 제한에 도달하는 것은 리소스 제한 도달의 구체적 경우입니다. 데이터베이스에서 작업자 제한에 도달하면 리소스별 대기 시간이 증가하여 쿼리 성능이 저하됩니다.

### <a name="troubleshooting"></a>문제 해결

진단 로그는 성능 및 리소스 사용 비율에 영향을 주는 쿼리의 쿼리 해시를 출력합니다. 가장 먼저 이 정보를 사용하여 데이터베이스 워크로드를 최적화할 수 있습니다. 특히 인덱스를 추가하여 성능 저하에 영향을 주는 쿼리를 최적화할 수 있습니다. 또는 더욱 고른 워크로드 배분으로 애플리케이션을 최적화할 수 있습니다. 워크로드를 줄이거나 최적화할 수 없는 경우 SQL Database 구독의 가격 책정 계층을 높이는 것이 좋습니다.

사용 가능한 세션 제한에 도달한 경우 데이터베이스에 대한 로그인 수를 줄여 애플리케이션을 최적화할 수 있습니다. 애플리케이션에서 데이터베이스에 접속하는 로그인 수를 줄일 수 없는 경우 데이터베이스의 가격 계층을 높이는 것이 좋습니다. 또는 데이터베이스를 여러 데이터베이스로 나누고 이동해 워크로드를 더욱 균형 있게 배분할 수 있습니다.

세션 제한 해결에 대한 자세한 제안 사항은 [SQL Database 최대 로그인 수 제한을 처리하는 방법(영문)](https://blogs.technet.microsoft.com/latam/20../../how-to-deal-with-the-limits-of-azure-sql-database-maximum-logins/)을 참조하세요. 서버 및 구독 수준의 한도에 관한 정보는 [SQL Database 서버의 리소스 한도 개요](sql-database-resource-limits-database-server.md)를 참조하세요.

## <a name="workload-increase"></a>워크로드 증가

### <a name="what-is-happening"></a>설명

이 성능 패턴은 워크로드 증가 또는 심각할 경우 워크로드 누적으로 인해 발생한 문제를 식별합니다.

이러한 검색은 여러 메트릭의 조합으로 수행됩니다. 측정되는 기본 메트릭은 과거 워크로드 기준과 비교하여 워크로드의 증가를 검색합니다. 다른 형태의 검색은 쿼리 성능에 영향을 줄 만큼 심각한 대량의 활성 작업자 스레드 증가를 측정하는 것을 기반으로 합니다.

더 심각한 경우 SQL Database에서 워크로드를 처리할 수 없어 워크로드가 계속 누적될 수 있습니다. 결과적으로 워크로드 크기가 지속적으로 증가해 워크로드가 누적되는 상태가 됩니다. 이러한 상태로 인해 워크로드가 실행을 대기하는 시간이 증가합니다. 이 상태는 가장 심각한 데이터베이스 성능 문제 중 하나입니다. 이 문제는 중단된 작업자 스레드 수의 증가를 모니터링하여 검색됩니다. 

### <a name="troubleshooting"></a>문제 해결

진단 로그는 실행이 증가한 쿼리의 수 및 워크로드 증가에 가장 많이 기여한 쿼리의 쿼리 해시를 출력합니다. 가장 먼저 이 정보를 사용하여 워크로드를 최적화할 수 있습니다. 특히 워크로드에 가장 크게 기여하는 것으로 식별된 쿼리를 가장 먼저 사용하는 것이 좋습니다.

워크로드를 데이터베이스에 더 균등하게 분산하는 것이 좋습니다. 인덱스를 추가하여 성능에 영향을 미치는 쿼리를 최적화해 보십시오. 여러 데이터베이스에 워크로드를 분산할 수도 있습니다. 이러한 방법을 사용할 수 없는 경우 SQL 데이터베이스 구독의 가격 책정 계층을 높여 사용 가능한 리소스의 양을 늘려 보십시오.

## <a name="memory-pressure"></a>메모리 부족

### <a name="what-is-happening"></a>설명

이 성능 패턴은 메모리 압력으로 인해 현재 데이터베이스 성능이 저하되었거나 지난 7일간의 성능 기준과 비교하여 매우 심각한 메모리 누적 상태에 있음을 나타냅니다.

메모리 압력은 SQL 데이터베이스에서 메모리 부여를 요청하는 작업자 스레드가 매우 많은 성능 상태를 나타냅니다. 이와 같이 높은 사용량으로 인해 메모리 사용률이 높아지고 SQL 데이터베이스는 메모리를 요청하는 모든 작업자에 메모리를 효율적으로 할당할 수 없는 상태가 됩니다. 이 문제의 가장 일반적인 이유 중 하나는 한편으로 SQL 데이터베이스가 사용할 수 있는 메모리의 양과 관련되어 있습니다. 또 한편으로는 워크로드가 증가하여 작업자 스레드 및 메모리 압력이 증가합니다.

더욱 심각한 형태의 메모리 압력은 메모리가 누적되는 상태입니다. 이 상태는 메모리를 해제하는 쿼리보다 많은 수의 작업자 스레드가 메모리 부여를 요청하는 경우를 나타냅니다. SQL 데이터베이스 엔진은 수요를 충족할 만큼 효율적으로 메모리를 할당할 수 없기 때문에 메모리 부여를 요청하는 이 작업자 스레드 수도 계속 증가(누적)할 수 있습니다. 메모리 누적 상태는 가장 심각한 데이터베이스 성능 문제 중 하나입니다.

### <a name="troubleshooting"></a>문제 해결

진단 로그는 높은 메모리 사용량 및 관련 타임스탬프에 대한 가장 큰 이유로 표시된 클럭(즉, 작업자 스레드)과 함께 메모리 개체 저장소 세부 정보를 출력합니다. 이 정보를 문제 해결의 기초로 사용할 수 있습니다. 

가장 높은 메모리 사용량이 있는 클럭과 관련된 쿼리를 최적화하거나 제거할 수 있습니다. 또한 사용하지 않을 데이터는 쿼리하지 않도록 해야 합니다. 쿼리에서 항상 WHERE 절을 사용하는 것이 좋습니다. 또한 데이터를 검사하기보다 검색할 수 있도록 클러스터되지 않은 인덱스를 만드는 것이 좋습니다.

또한 워크로드를 최적화하거나 여러 데이터베이스에 분산하여 워크로드를 줄일 수 있습니다. 또는 여러 데이터베이스에 워크로드를 분산할 수도 있습니다. 이러한 방법을 사용할 수 없는 경우 SQL 데이터베이스 구독의 가격 책정 계층을 높여 데이터베이스에 사용 가능한 메모리 리소스의 양을 늘려 보십시오.

추가 문제 해결 제안 사항은 [메모리 부여 명상: 많은 이름을 가진 특이한 SQL Server 메모리 소비자](https://blogs.msdn.microsoft.com/sqlmeditation/20../../memory-meditation-the-mysterious-sql-server-memory-consumer-with-many-names/)를 참조하세요.

## <a name="locking"></a>잠금

### <a name="what-is-happening"></a>설명

이 성능 패턴은 지난 7일간의 성능 기준과 비교하여 과도한 데이터베이스 잠금이 감지된 현재 데이터베이스의 성능 저하를 나타냅니다. 

최신 RDBMS 잠금은 여러 동시 작업자와 가능한 경우 병렬 데이터베이스 트랜잭션을 실행하여 성능이 최대화되는 다중 스레드 시스템을 구현하는 데 필수적입니다. 이 컨텍스트의 잠금은 단일 트랜잭션만 단독으로 필요한 행, 페이지, 테이블 및 파일에 액세스할 수 있고, 다른 트랜잭션과 리소스에 대해 경쟁하지 않는 기본 제공 액세스 메커니즘을 의미합니다. 사용할 리소스를 잠근 트랜잭션이 완료되면 해당 리소스에 대한 잠금이 해제되어 다른 트랜잭션에서 필요한 리소스에 액세스할 수 있습니다. 잠금에 대한 자세한 내용은 [데이터베이스 엔진에서의 잠금](https://msdn.microsoft.com/library/ms190615.aspx)을 참조하세요.

SQL 엔진에서 실행한 트랜잭션이 사용할 수 없도록 잠긴 리소스에 액세스하는 데 오랫동안 대기하는 경우 이로 인해 워크로드 실행 성능이 느려집니다. 

### <a name="troubleshooting"></a>문제 해결

진단 로그는 문제 해결의 기초로 사용할 수 있는 잠금 세부 정보를 출력합니다. 보고된 차단 쿼리, 즉 잠금 성능 저하가 발생하는 쿼리를 분석하여 제거할 수 있습니다. 경우에 따라 차단 쿼리를 성공적으로 최적화할 수 있습니다.

이 문제를 완화하는 가장 간단하고 안전한 방법은 트랜잭션을 짧게 유지하고 비용이 가장 많이 드는 쿼리의 잠금 범위를 좁히는 것입니다. 큰 작업 배치를 작은 작업으로 분할할 수 있습니다. 쿼리를 최대한 효율적으로 만들어 쿼리 잠금 범위를 좁히는 것이 좋습니다. 교착 상태가 발생할 가능성이 높아지고 전체 데이터베이스 성능에 부정적인 영향을 줄 수 있으므로 대량 스캔을 줄이십시오. 잠금을 유발하는 쿼리가 식별된 경우 새 인덱스를 만들거나 기존 인덱스에 열을 추가하여 테이블 스캔을 방지할 수 있습니다. 

자세한 제안 사항은 [SQL Server 잠금 에스컬레이션으로 인해 발생하는 차단 문제를 해결하는 방법](https://support.microsoft.com/help/323630/how-to-resolve-blocking-problems-that-are-caused-by-lock-escalation-in)을 참조하세요.

## <a name="increased-maxdop"></a>MAXDOP 증가

### <a name="what-is-happening"></a>설명

이 검색 가능한 성능 패턴은 선택한 쿼리 실행 계획이 예상보다 더 많이 병렬 처리된 상태를 나타냅니다. SQL Database 쿼리 최적화 프로그램은 가능한 경우 작업 속도를 높이기 위해 쿼리를 병렬로 실행함으로써 워크로드 성능을 향상시킵니다. 쿼리를 처리하는 병렬 작업자들은 더 적은 수의 병렬 작업자들이 동일한 쿼리를 실행하는 경우 또는 단일 작업자 스레드에 비해 결과를 동기화 및 병합하기 위해 서로 대기하는 데 더 많은 시간을 사용하는 경우가 있습니다.

전문가 시스템은 기준 기간과 비교하여 현재 데이터베이스 성능을 분석합니다. 이 시스템은 쿼리 실행 계획이 정상보다 더 병렬 처리되어 이전에 실행하던 쿼리가 더 느리게 실행되고 있는지 여부를 판단합니다.

SQL Database의 MAXDOP 서버 구성 옵션은 동일한 쿼리를 병렬로 실행하는 데 사용할 수 있는 CPU 코어 수를 제어하는 데 사용됩니다. 

### <a name="troubleshooting"></a>문제 해결

진단 로그는 정상보다 더 병렬 처리되어 실행 기간이 증가한 쿼리와 관련된 쿼리 해시를 출력합니다. 또한 CXP 대기 시간도 출력합니다. 이 시간은 단일 구성 도우미/코디네이터 스레드(스레드 0)에서 결과를 병합하고 예정대로 진행하기 전에 다른 모든 스레드가 완료될 때까지 대기하는 시간을 나타냅니다. 또한 진단 로그는 성능이 느린 쿼리가 실행 시 대기한 시간을 출력합니다. 이 정보를 문제 해결의 기초로 사용할 수 있습니다.

가장 먼저 복잡한 쿼리를 최적화 또는 간소화합니다. 시간이 오래 걸리는 일괄 처리 작업을 더 작은 작업으로 분할하는 것이 좋습니다. 또한 반드시 쿼리를 지원하기 위한 인덱스를 만듭니다. 또한 성능이 느린 것으로 플래그 지정된 쿼리에 대해 MAXDOP(최대 병렬 처리 수준 옵션)를 수동으로 적용할 수 있습니다. T-SQL을 사용하여 이 작업을 구성하려면 [MAXDOP 서버 구성 옵션 구성](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-the-max-degree-of-parallelism-server-configuration-option)을 참조하세요.

MAXDOP 서버 구성 옵션을 기본값인 0으로 설정하면 SQL Database에서 사용 가능한 모든 논리적 CPU 코어를 사용하여 단일 쿼리를 실행하기 위한 스레드를 병렬 처리할 수 있음을 나타냅니다. MAXDOP를 1로 설정할 경우 단일 쿼리 실행 시 하나의 코어만 사용할 수 있음을 나타냅니다. 실제로는 병렬 처리가 해제된 것을 의미합니다. 사례 기준별 사례, 데이터베이스에 사용 가능한 코어 수, 진단 로그 정보에 따라 MAXDOP 옵션을 해당 문제를 해결할 수 있는 병렬 쿼리 실행에 대한 코어 수로 조정할 수 있습니다.

## <a name="pagelatch-contention"></a>Pagelatch 경합

### <a name="what-is-happening"></a>설명

이 성능 패턴은 지난 7일간의 워크로드 기준과 비교하여 페이지 래치 경합으로 인해 발생하는 현재 데이터베이스의 워크로드 성능 저하를 나타냅니다.

래치는 SQL Database에서 다중 스레딩을 활성화하기 위해 사용하는 가벼운 동기화 메커니즘입니다. 이 메커니즘은 인덱스, 데이터 페이지, 기타 내부 구조가 포함된 메모리 내 구조의 일관성을 보장합니다.

SQL 데이터베이스에는 사용할 수 있는 다양한 유형의 래치가 있습니다. 간소화를 위해 버퍼 풀의 메모리 내 페이지를 보호하는 데 버퍼 래치가 사용됩니다. IO 래치는 아직 버퍼 풀로 로드되지 않은 페이지를 보호하는 데 사용됩니다. 버퍼 풀의 페이지에서 데이터를 쓰거나 읽을 때마다 작업자 스레드는 먼저 페이지에 대한 버퍼 래치를 얻어야 합니다. 작업자 스레드가 메모리 내 버퍼 풀에서 이미 사용할 수 없는 페이지에 액세스하려고 할 때마다 저장소에서 필요한 정보를 로드하려는 IO 요청이 진행됩니다. 이 이벤트 순서는 더욱 심각한 형태의 성능 저하를 나타냅니다.

여러 스레드가 동일한 메모리 내 구조에서 래치를 동시에 얻으려고 하여 쿼리 실행에 대한 대기 시간이 증가할 때 페이지 래치 경합이 발생합니다. 저장소에서 데이터에 액세스해야 하는 페이지 래치 IO 경합이 발생하는 경우 이 대기 시간이 훨씬 길어집니다. 이 경우 워크로드 성능에 심각한 영향을 미칠 수 있습니다. 페이지 래치 경합은 서로를 대기하며 여러 CPU 시스템에서 리소스를 대상으로 경쟁하는 가장 일반적인 시나리오입니다.

### <a name="troubleshooting"></a>문제 해결

진단 로그는 페이지 래치 경합 세부 정보를 출력합니다. 이 정보를 문제 해결의 기초로 사용할 수 있습니다.

페이지 래치는 SQL Database의 내부 제어 메커니즘이므로 사용할 시기를 자동으로 결정합니다. 스키마 디자인을 포함한 애플리케이션 결정은 래치의 결정적 동작으로 인해 페이지 래치 동작에 영향을 줄 수 있습니다.

래치 경합을 처리하는 한 가지 방법은 순차적 인덱스 키를 순차적이지 않은 키로 바꿔 인덱스 범위에서 삽입을 균등하게 분산하는 것입니다. 일반적으로 인덱스의 선행 열이 워크로드를 비례적으로 배포합니다. 고려해야 할 다른 방법은 테이블 분할입니다. 과도한 래치 경합을 완화하기 위한 일반적인 방법은 분할된 테이블에 계산 열이 있는 해시 분할 스키마를 만드는 것입니다. 페이지 래치 IO 경합이 발생하는 경우 인덱스를 사용하면 이 성능 문제를 완화할 수 있습니다. 

자세한 내용은 [SQL Server에서 래치 경합 진단 및 해결](https://download.microsoft.com/download/B/9/E/B9EDF2CD-1DBF-4954-B81E-82522880A2DC/SQLServerLatchContention.pdf)(PDF 다운로드)을 참조하세요.

## <a name="missing-index"></a>누락된 인덱스

### <a name="what-is-happening"></a>설명

이 성능 패턴은 지난 7일간의 워크로드 기준과 비교하여 인덱스 누락으로 인해 발생하는 현재 데이터베이스의 워크로드 성능 저하를 나타냅니다.

인덱스는 쿼리 성능의 속도를 향상시키는 데 사용됩니다. 방문하거나 검색해야 하는 데이터 세트 페이지의 수를 줄여 테이블 데이터에 빠르게 액세스할 수 있습니다.

이 검색을 통해 성능이 저하된 쿼리 중 인덱스를 만들 경우 성능을 개선할 수 있는 특정 쿼리가 식별됩니다.

### <a name="troubleshooting"></a>문제 해결

진단 로그는 워크로드 성능에 영향을 주는 것으로 식별된 쿼리에 대한 쿼리 해시를 출력합니다. 이러한 쿼리에 대해 인덱스를 작성할 수 있습니다. 쿼리를 최적화할 수 있으며 이러한 쿼리가 필요하지 않을 경우에는 삭제할 수도 있습니다. 사용하지 않는 데이터를 쿼리하지 않도록 하는 것이 좋습니다.

> [!TIP]
> SQL Database에 기본 제공되는 인텔리전스를 통해 데이터베이스에 대한 성능이 가장 뛰어난 인덱스를 자동으로 관리할 수 있다는 것을 알고 계신가요?
>
> SQL Database에 대해 연속적으로 성능을 최적화하려면 [SQL Database 자동 튜닝](sql-database-automatic-tuning.md)을 활성화하는 것이 좋습니다. SQL Database에 기본 제공되는 이 인텔리전스 기능은 SQL 데이터베이스를 지속적으로 모니터링하고 데이터베이스의 인덱스를 자동으로 조정하며 인덱스를 만듭니다.
>

## <a name="new-query"></a>새 쿼리

### <a name="what-is-happening"></a>설명

이 성능 패턴은 7일 성능 기준과 비교하여 성능이 저하되고 워크로드 성능에 영향을 미치고 있는 새 쿼리가 검색되었음을 나타냅니다.

양호한 성능의 쿼리를 작성하는 것은 어려운 과제일 수 있습니다. 쿼리 작성에 대한 자세한 내용은 [SQL 쿼리 작성(영문)](https://msdn.microsoft.com/library/bb264565.aspx)을 참조하세요. 기존 쿼리 성능을 최적화하려면 [쿼리 튜닝](https://msdn.microsoft.com/library/ms176005.aspx)을 참조하세요.

### <a name="troubleshooting"></a>문제 해결

진단 로그는 쿼리 해시를 포함하여 가장 많은 CPU를 사용하는 최대 2개의 새 쿼리에 대한 정보를 출력합니다. 검색된 쿼리가 워크로드 성능에 영향을 미치므로 쿼리를 최적화하는 것이 좋습니다. 사용해야 하는 데이터만 검색하는 것이 좋습니다. WHERE 절이 있는 쿼리를 사용하는 것도 좋습니다. 또한 복잡한 쿼리를 단순화하고 더 작은 쿼리로 분할하는 것도 좋습니다. 큰 일괄 처리 쿼리를 더 작은 일괄 처리 쿼리로 분할하는 것도 좋은 방법입니다. 이 성능 문제를 완화하려면 일반적으로 새 쿼리에 대해 인덱스를 도입하는 것이 좋습니다.

[Azure SQL Database Query Performance Insight](sql-database-query-performance.md)를 사용하는 것이 좋습니다.

## <a name="increased-wait-statistic"></a>대기 통계 증가

### <a name="what-is-happening"></a>설명

검색 가능한 이 성능 패턴은 지난 7일간의 워크로드 기준과 비교하여 성능이 저하된 쿼리가 식별된 워크로드 성능 저하를 나타냅니다.

이 경우 시스템은 검색 가능한 다른 기본 성능 범주에서 성능이 저하된 쿼리를 분류할 수 없지만 회귀의 원인인 대기 통계를 검색했습니다. 따라서 시스템은 회귀의 원인인 대기 통계도 노출되는 *대기 통계 증가*를 사용하여 쿼리로 간주합니다. 

### <a name="troubleshooting"></a>문제 해결

진단 로그는 영향을 받은 쿼리의 쿼리 해시 및 대기 시간 증가에 대한 정보를 출력합니다.

시스템은 성능이 저하된 쿼리의 근본 원인을 성공적으로 식별할 수 없으므로 가장 먼저 진단 정보를 사용하여 문제를 수동으로 해결할 수 있습니다. 이러한 쿼리의 성능을 최적화할 수 있습니다. 사용해야 하는 데이터만 가져오고 복잡한 쿼리를 간소화하여 작게 분할하는 것이 좋습니다. 

쿼리 성능 최적화에 대한 자세한 내용은 [쿼리 튜닝](https://msdn.microsoft.com/library/ms176005.aspx)을 참조하세요.

## <a name="tempdb-contention"></a>TempDB 경합

### <a name="what-is-happening"></a>설명

검색 가능한 이 성능 패턴은 TempDB 리소스에 액세스하려는 스레드의 병목 현상이 있는 데이터베이스 성능 상태를 나타냅니다. (이 상태는 IO와 관련이 없습니다.) 이 성능 문제가 발생하는 일반적인 시나리오는 수백 개의 동시 쿼리가 모두 작은 TempDB 테이블을 만들고 사용하고 삭제하는 경우입니다. 시스템이 지난 7일간의 성능 기준과 비교하여 동일한 TempDB 테이블을 사용하는 많은 수의 동시 쿼리가 데이터베이스 성능에 영향을 줄 만큼 통계적으로 유의미한 수준으로 증가했음을 감지했습니다.

### <a name="troubleshooting"></a>문제 해결

진단 로그는 TempDB 경합 세부 정보를 출력합니다. 문제를 해결할 때 가장 먼저 이 정보를 참고할 수 있습니다. 이러한 유형의 경합을 완화하고 전체 워크로드의 처리량을 늘리려는 경우 두 가지 방법을 시도할 수 있습니다. 임시 테이블 사용을 중지할 수 있습니다. 또는 메모리 최적화 테이블을 사용할 수 있습니다. 

자세한 내용은 [메모리 최적화 테이블 소개(영문)](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/introduction-to-memory-optimized-tables)를 참조하세요. 

## <a name="elastic-pool-dtu-shortage"></a>탄력적 풀 DTU 부족

### <a name="what-is-happening"></a>설명

검색 가능한 이 성능 패턴은 지난 7일간의 기준과 비교하여 현재 데이터베이스 워크로드의 성능이 저하되었음을 나타냅니다. 구독의 탄력적 풀에서 사용 가능한 DTU가 부족하기 때문입니다 

SQL Database의 리소스는 일반적으로 CPU 및 IO(데이터 및 트랜잭션 로그 IO) 리소스가 복합된 측정값으로 구성되는 [DTU 리소스](sql-database-purchase-models.md#dtu-based-purchasing-model)라고 합니다. [Azure 탄력적 풀 리소스](sql-database-elastic-pool.md)은 크기 조정을 위해 여러 데이터베이스 간에 공유되는 사용 가능한 eDTU 리소스의 풀로 사용됩니다. 탄력적 풀에서 사용 가능한 eDTU 리소스가 충분하지 않아 풀의 모든 데이터베이스를 지원할 수 없는 경우 시스템에서 탄력적 풀 DTU 부족 성능 문제를 감지합니다.

### <a name="troubleshooting"></a>문제 해결

진단 로그는 탄력적 풀에 대한 정보를 출력하고, DTU 사용량이 가장 높은 데이터베이스를 나열하고, DTU 사용량이 가장 많은 데이터베이스에서 사용하는 풀의 DTU 백분율을 제공합니다.

이 성능 상태는 탄력적 풀에서 동일한 eDTU 풀을 사용하는 여러 데이터베이스와 관련되어 있으므로 문제 해결 단계에서는 DTU 사용량이 가장 높은 데이터베이스에 집중해야 합니다. 사용량이 가장 높은 데이터베이스에서 워크로드를 줄일 수 있으며, 예를 들면 이러한 데이터베이스에서 사용량이 가장 많은 쿼리를 최적화할 수 있습니다. 또한 사용하지 않을 데이터는 쿼리하지 않도록 해야 합니다. 또 다른 방법은 상위 DTU 사용 데이터베이스를 사용하여 애플리케이션을 최적화하고 여러 데이터베이스 간에 워크로드를 다시 배포하는 것입니다.

DUT 사용량이 가장 높은 데이터베이스에서 현재 워크로드의 감소 및 최적화가 가능하지 않은 경우 탄력적 풀의 가격 계층을 높여 보십시오. 이와 같이 증가할 경우 탄력적 풀에서 사용 가능한 DTU가 증가합니다.

## <a name="plan-regression"></a>계획 회귀

### <a name="what-is-happening"></a>설명

검색 가능한 이 성능 패턴은 SQL Database에서 최적이 아닌 쿼리 실행 계획을 활용하기 시작하는 상태를 나타냅니다. 최적이 아닌 계획에서는 일반적으로 쿼리 실행이 증가하여 현재 및 기타 쿼리의 대기 시간이 더 길어집니다.

SQL 데이터베이스는 쿼리 실행 비용을 최소화하는 쿼리 실행 계획을 결정합니다. 쿼리 및 워크로드 유형이 변경되면 기존 계획이 더 이상 효율적이지 않거나 SQL Database에서 적절한 평가를 수행하지 못하는 경우가 있습니다. 수정의 문제로서 쿼리 실행 계획은 수동으로 적용할 수 있습니다.

검색 가능한 이 성능 패턴은 계획 회귀의 세 가지 경우, 즉 새 계획 회귀, 이전 계획 회귀, 기존 계획 변경 워크로드를 결합합니다. 발생한 계획 회귀의 특정 유형은 진단 로그의 *details* 속성에 나와 있습니다.

새 계획 회귀 상태는 SQL Database가 기존 계획만큼 효율적이지 않은 새 쿼리 실행 계획을 시작하는 상태를 가리킵니다. 이전 계획 회귀 상태란 SQL Database가 더 효율적인 새 계획에서 새 계획만큼 효율적이지 않은 이전 계획으로 전환하는 상태를 가리킵니다. 기존 계획 변경 워크로드 회귀는 이전 계획과 새 계획이 성능이 낮은 계획으로 균형을 이동하면서 계속해서 이전 계획과 새 계획을 교대하는 상태를 나타냅니다.

계획 회귀에 대한 자세한 내용은 [SQL 서버의 계획 회귀란?(영문)](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../what-is-plan-regression-in-sql-server/)을 참조하세요. 

### <a name="troubleshooting"></a>문제 해결

진단 로그는 쿼리 해시, 좋은 계획 ID, 나쁜 계획 ID, 쿼리 ID를 출력합니다. 이 정보를 문제 해결의 기초로 사용할 수 있습니다.

제공된 쿼리 해시로 식별할 수 있는 특정 쿼리에 대해 성능을 향상시킬 수 있는 계획을 분석할 수 있습니다. 쿼리에 대해 더 효율적으로 작동하는 계획을 결정한 후에는 계획을 수동으로 적용할 수 있습니다. 

자세한 내용은 [SQL Server에서 계획 회귀를 방지하는 방법 알아보기(영문)](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../you-shall-not-regress-how-sql-server-2017-prevents-plan-regressions/)를 참조하세요.

> [!TIP]
> SQL Database에 기본 제공되는 인텔리전스를 통해 데이터베이스에 대해 성능이 가장 뛰어난 쿼리 실행 계획을 자동으로 관리할 수 있다는 것을 알고 계신가요?
>
> SQL Database에 대해 연속적으로 성능을 최적화하려면 [SQL Database 자동 튜닝](sql-database-automatic-tuning.md)을 활성화하는 것이 좋습니다. SQL Database에 기본 제공되는 이 인텔리전스 기능은 SQL 데이터베이스를 지속적으로 모니터링하고 데이터베이스의 성능이 가장 뛰어난 쿼리 실행 계획을 자동으로 조정하며 작성합니다.
>

## <a name="database-scoped-configuration-value-change"></a>데이터베이스 범위 구성 값 변경

### <a name="what-is-happening"></a>설명

검색 가능한 이 성능 패턴은 지난 7일간의 데이터베이스 워크로드 동작과 비교하여 데이터베이스 범위 구성이 변경됨으로써 성능 회귀가 감지된 상태를 나타냅니다. 이 패턴은 최근에 변경한 데이터베이스 범위 구성이 데이터베이스 성능에 도움이 되지 않는 것을 나타냅니다.

데이터베이스 범위 구성 변경은 각 개별 데이터베이스에 대해 설정할 수 있습니다. 이 구성은 데이터베이스의 개별 성능을 최적화하기 위해 상황별로 사용됩니다. 각각의 개별 데이터베이스에 대해 MAXDOP, LEGACY_CARDINALITY_ESTIMATION, PARAMETER_SNIFFING, QUERY_OPTIMIZER_HOTFIXES 및 CLEAR PROCEDURE_CACHE와 같은 옵션을 구성할 수 있습니다.

### <a name="troubleshooting"></a>문제 해결

진단 로그는 지난 7일간의 워크로드 동작과 비교하여 성능 저하를 초래한 데이터베이스 범위 구성 변경 내용을 출력합니다. 구성 변경 사항을 이전 값으로 되돌릴 수 있습니다. 또한 원하는 성능 레벨에 도달할 때까지 각 값을 조정할 수 있습니다. 만족할 만한 성능과 유사한 데이터베이스의 데이터베이스 범위 구성 값을 복사할 수 있습니다. 성능 문제를 해결할 수 없는 경우 기본 SQL Database 기본값으로 되돌리고 이 기준에서 정밀 조정을 시도하십시오.

구성 변경에 대한 데이터베이스 범위 구성 및 T-SQL 구문을 최적화하는 방법에 대한 자세한 내용은 [데이터베이스 범위 구성 변경(Transact-SQL)](https://msdn.microsoft.com/library/mt629158.aspx)을 참조하세요.

## <a name="slow-client"></a>느린 클라이언트

### <a name="what-is-happening"></a>설명

검색 가능한 이 성능 패턴은 SQL Database를 사용하는 클라이언트가 데이터베이스에서 결과를 보내는 속도로 데이터베이스의 출력을 사용할 수 없는 상태를 나타냅니다. SQL Database는 실행 쿼리의 결과를 버퍼에 저장하지 않기 때문에 속도가 느려지고 클라이언트가 전송된 쿼리 출력을 소비할 때까지 대기한 후 계속 진행합니다. 또한 이 상태는 SQL Database의 출력을 클라이언트로 전송할 수 있을 만큼 빠르지 않은 네트워크와 관련되어 있을 수 있습니다.

이 상태는 지난 7일간의 데이터베이스 워크로드 동작과 비교하여 성능 회귀가 감지된 경우에만 생성됩니다. 이 성능 문제는 이전 성능 동작에 비해 통계적으로 유의미한 성능 저하가 발생하는 경우에만 감지됩니다.

### <a name="troubleshooting"></a>문제 해결

검색 가능한 이 성능 패턴은 클라이언트측 상태를 나타냅니다. 클라이언트 측 애플리케이션 또는 클라이언트 측 네트워크에서 문제를 해결해야 합니다. 진단 로그는 지난 2시간 이내에 클라이언트에서 사용하기를 가장 많이 기다리는 것처럼 보이는 쿼리 해시와 대기 시간을 출력합니다. 이 정보를 문제 해결의 기초로 사용할 수 있습니다.

이러한 쿼리를 사용하기 위해 애플리케이션의 성능을 최적화할 수 있습니다. 발생 가능한 네트워크 대기 시간 문제를 고려해야 할 수도 있습니다. 성능 저하 문제를 판단하는 기준은 지난 7일간의 성능 기준 변경이므로 최근 애플리케이션 또는 네트워크 상태 변경이 이 성능 회귀 이벤트의 원인인지 여부를 조사하는 것이 좋습니다. 

## <a name="pricing-tier-downgrade"></a>가격 책정 계층 다운그레이드

### <a name="what-is-happening"></a>설명

이 검색 가능한 성능 패턴은 SQL Database 구독의 가격 책정 계층이 다운그레이드된 상태를 나타냅니다. 데이터베이스에서 사용할 수 있는 리소스(DTU)의 감소로 인해 시스템에서 지난 7일간의 기준과 비교하여 현재 데이터베이스 성능이 저하되었음을 감지했습니다.

또한 SQL Database 구독의 가격 책정 계층이 다운그레이드된 후 단기간 내에 상위 계층으로 업그레이드된 상태가 있었을 수도 있습니다. 이러한 일시적 성능 저하의 검색은 진단 로그에 진단 로그의 세부 정보 섹션에 가격 책정 계층 다운그레이드 및 업그레이드로 출력됩니다.

### <a name="troubleshooting"></a>문제 해결

가격 책정 계층을 낮추고 이에 따라 SQL Database에서 사용할 수 있는 DTU를 줄인 후 성능에 만족하게 되면 수행해야 할 작업이 없습니다. 가격 책정 계층을 낮춘 후 SQL 데이터베이스 성능에 만족하지 않을 경우 데이터베이스 워크로드를 줄이거나 가격 책정 계층을 높은 레벨로 높여보세요.

## <a name="recommended-troubleshooting-flow"></a>권장되는 문제 해결 흐름

 Intelligent Insights를 사용하여 성능 문제를 해결하는 데 권장되는 방법은 순서도를 따르세요.

Azure SQL 분석으로 이동하여 Azure Portal을 통해 Intelligent Insight에 액세스합니다. 들어오는 성능 경고를 찾아서 선택합니다. 검색 페이지에서 진행되는 상황을 확인합니다. 제공되는 문제의 근본 원인 분석, 쿼리 텍스트, 쿼리 시간 추세 및 인시던트 진화를 관찰합니다. 성능 문제 완화에 대한 Intelligent Insights 권장 사항을 사용하여 문제를 해결합니다. 

[![권장되는 문제 해결 흐름](./media/sql-database-intelligent-insights/intelligent-insights-troubleshooting-flowchart.png)](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/intelligent-insight/Troubleshoot%20Azure%20SQL%20Database%20performance%20issues%20using%20Intelligent%20Insight.pdf)

> [!TIP]
> PDF 버전을 다운로드하려면 순서도를 선택하세요.

Intelligent Insights는 일반적으로 성능 문제에 대한 근본 원인 분석을 수행하는 데 1시간이 필요합니다. Intelligent Insights에서 문제를 찾을 수 없고 사용자에게 중요한 경우 쿼리 저장소를 사용하여 성능문제의 근본 원인을 수동으로 식별합니다. (일반적으로 이러한 문제는 1시간 이전에 발생한 것입니다.) 자세한 내용은 [쿼리 저장소를 사용하여 성능 모니터링](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store)을 참조하세요.

## <a name="next-steps"></a>다음 단계
- [Intelligent Insights](sql-database-intelligent-insights.md) 개념을 알아봅니다.
- [Intelligent Insights의 Azure SQL Database 성능 진단 로그](sql-database-intelligent-insights-use-diagnostics-log.md)를 사용합니다.
- [Azure SQL 분석을 사용하여 Azure SQL Database](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-sql)를 모니터링합니다.
- [Azure 리소스에서 로그 데이터 수집 및 소비](../azure-monitor/platform/diagnostic-logs-overview.md)하는 방법을 알아봅니다.
