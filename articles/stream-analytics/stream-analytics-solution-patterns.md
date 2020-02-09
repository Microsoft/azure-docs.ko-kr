---
title: Azure Stream Analytics 솔루션 패턴
description: 대시보드, 이벤트 메시징, 데이터 저장소, 참조 데이터 보강, 모니터링 등의 Azure Stream Analytics에 대 한 일반적인 솔루션 패턴에 대해 알아봅니다.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.openlocfilehash: 2a449c55a0998f1a114f6aa9d2c067e48cc0cdce
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75443676"
---
# <a name="azure-stream-analytics-solution-patterns"></a>Azure Stream Analytics 솔루션 패턴

Azure의 다른 많은 서비스와 마찬가지로 Stream Analytics 다른 서비스와 함께 사용 하 여 종단 간 솔루션을 만드는 것이 가장 좋습니다. 이 문서에서는 간단한 Azure Stream Analytics 솔루션과 다양 한 아키텍처 패턴을 설명 합니다. 이러한 패턴을 기반으로 하 여 더 복잡 한 솔루션을 개발할 수 있습니다. 이 문서에서 설명 하는 패턴은 다양 한 시나리오에서 사용할 수 있습니다. 시나리오 관련 패턴의 예제는 [Azure 솔루션 아키텍처](https://azure.microsoft.com/solutions/architecture/?product=stream-analytics)에서 사용할 수 있습니다.

## <a name="create-a-stream-analytics-job-to-power-real-time-dashboarding-experience"></a>실시간 대시보드 환경을 위한 Stream Analytics 작업 만들기

Azure Stream Analytics를 사용 하면 실시간 대시보드 및 경고를 신속 하 게 사용할 수 있습니다. 간단한 솔루션은 Event Hubs 또는 IoT Hub에서 이벤트를 수집 하 고 [스트리밍 데이터 집합을 사용 하 여 Power BI 대시보드를 피드](/power-bi/service-real-time-streaming)합니다. 자세한 내용은 자세한 자습서 [Stream Analytics 사용 하 여 전화 통화 데이터 분석 및 Power BI 대시보드에서 결과 시각화](stream-analytics-manage-job.md)를 참조 하세요.

![GLOBAL.ASA Power BI 대시보드](media/stream-analytics-solution-patterns/pbidashboard.png)

Azure Portal에서 몇 분만에이 솔루션을 빌드할 수 있습니다. 포괄적인 코딩은 없으며, 비즈니스 논리를 표현 하는 데 SQL 언어가 사용 됩니다.

이 솔루션 패턴은 이벤트 원본에서 브라우저의 Power BI 대시보드에 대 한 가장 낮은 대기 시간을 제공 합니다. 이 기본 제공 기능을 사용 하는 유일한 Azure 서비스는 Azure Stream Analytics입니다.

## <a name="use-sql-for-dashboard"></a>대시보드에 SQL 사용

Power BI 대시보드는 짧은 대기 시간을 제공 하지만 완전 한 Power BI 보고서를 생성 하는 데 사용할 수 없습니다. 일반적인 보고 패턴은 데이터를 SQL database로 먼저 출력 하는 것입니다. 그런 다음 Power BI의 SQL connector를 사용 하 여 SQL에서 최신 데이터를 쿼리 합니다.

![GLOBAL.ASA SQL 대시보드](media/stream-analytics-solution-patterns/sqldashboard.png)

SQL database를 사용 하면 더 많은 유연성이 제공 되지만 약간 더 높은 대기 시간이 발생 합니다. 이 솔루션은 대기 시간이 1 초 보다 큰 작업에 적합 합니다. 이 방법을 사용 하면 보고서의 데이터를 추가로 조각화 및 분석 하 고 훨씬 더 많은 시각화 옵션을 위해 Power BI 기능을 최대화할 수 있습니다. 또한 Tableau와 같은 다른 대시보드 솔루션을 사용 하는 유연성을 얻을 수 있습니다.

SQL은 처리량이 높은 데이터 저장소가 아닙니다. Azure Stream Analytics에서 SQL database에 대 한 최대 처리량은 현재 약 24 m b/초입니다. 솔루션의 이벤트 원본에서 더 높은 속도로 데이터를 생성 하는 경우 Stream Analytics의 처리 논리를 사용 하 여 출력 속도를 SQL로 줄여야 합니다. 필터링, 기간 이동 집계, 임시 조인과 함께 패턴 일치, 분석 함수 등의 기법을 사용할 수 있습니다. 출력 [Azure Stream Analytics Azure SQL Database](stream-analytics-sql-output-perf.md)에 설명 된 기술을 사용 하 여 SQL에 대 한 출력 율을 추가로 최적화할 수 있습니다.

## <a name="incorporate-real-time-insights-into-your-application-with-event-messaging"></a>이벤트 메시징으로 실시간 정보를 응용 프로그램에 통합

두 번째로 가장 널리 사용 되는 Stream Analytics는 실시간 경고를 생성 하는 것입니다. 이 솔루션 패턴에서는 Stream Analytics의 비즈니스 논리를 사용 하 여 [임시 및 공간 패턴이](stream-analytics-geospatial-functions.md) 나 [비정상](stream-analytics-machine-learning-anomaly-detection.md)을 검색 한 다음 경고 신호를 생성할 수 있습니다. 그러나 Stream Analytics에서 Power BI를 기본 엔드포인트으로 사용 하는 대시보드 솔루션과 달리 많은 중간 데이터 싱크를 사용할 수 있습니다. 이러한 싱크에는 Event Hubs, Service Bus 및 Azure Functions 있습니다. 응용 프로그램 빌더는 시나리오에 가장 적합 한 데이터 싱크를 결정 해야 합니다.

기존 비즈니스 워크플로에서 경고를 생성 하려면 다운스트림 이벤트 소비자 논리를 구현 해야 합니다. Azure Functions에서 사용자 지정 논리를 구현할 수 있으므로이 통합을 수행할 수 있는 가장 빠른 방법은 Azure Functions입니다. Stream Analytics 작업에 대 한 출력으로 Azure 함수를 사용 하는 방법에 대 한 자습서는 [Azure Stream Analytics 작업에서 실행 Azure Functions](stream-analytics-with-azure-functions.md)에서 찾을 수 있습니다. Azure Functions는 텍스트와 전자 메일을 비롯 한 다양 한 유형의 알림도 지원 합니다. 논리 앱은 Stream Analytics와 논리 앱 간의 Event Hubs와 함께 이러한 통합에도 사용할 수 있습니다.

![GLOBAL.ASA 이벤트 메시징 앱](media/stream-analytics-solution-patterns/eventmessagingapp.png)

반면 Event Hubs은 가장 유연한 통합 지점을 제공 합니다. Azure 데이터 탐색기 및 Time Series Insights와 같은 다른 많은 서비스에서는 Event Hubs의 이벤트를 사용할 수 있습니다. 서비스는 Azure Stream Analytics에서 Event Hubs 싱크로 직접 연결 하 여 솔루션을 완료할 수 있습니다. 이러한 통합 시나리오를 위해 Azure에서 제공 하는 최고 수준의 처리량 메시징 broker도 Event Hubs입니다.

## <a name="dynamic-applications-and-websites"></a>동적 응용 프로그램 및 웹 사이트

Azure Stream Analytics 및 Azure SignalR 서비스를 사용 하 여 대시보드 또는 지도 시각화와 같은 사용자 지정 실시간 시각화를 만들 수 있습니다. 웹 클라이언트는 SignalR를 사용 하 여 동적 콘텐츠를 실시간으로 업데이트 하 고 표시할 수 있습니다.

![GLOBAL.ASA 동적 앱](media/stream-analytics-solution-patterns/dynamicapp.png)

## <a name="incorporate-real-time-insights-into-your-application-through-data-stores"></a>데이터 저장소를 통해 응용 프로그램에 실시간 정보를 통합 합니다.

오늘날 대부분의 웹 서비스와 웹 응용 프로그램은 요청/응답 패턴을 사용 하 여 프레젠테이션 계층을 제공 합니다. 요청/응답 패턴은 간단 하 게 작성할 수 있으며, Cosmos DB와 같이 상태 비저장 프런트 엔드 및 확장 가능한 저장소를 사용 하 여 응답 시간을 단축 하 여 쉽게 확장할 수 있습니다.

데이터 볼륨이 높으면 CRUD 기반 시스템에서 성능 병목 현상이 종종 발생 합니다. [이벤트 소싱 솔루션 패턴](/azure/architecture/patterns/event-sourcing) 은 성능 병목 상태를 해결 하는 데 사용 됩니다. 임시 패턴 및 정보는 기존 데이터 저장소에서 추출 하는 것도 어렵고 비효율적입니다. 최신 대용량 데이터 기반 응용 프로그램은 종종 데이터 흐름 기반 아키텍처를 채택 합니다. 동작에서 데이터에 대 한 계산 엔진 Azure Stream Analytics 해당 아키텍처의 linchpin입니다.

![GLOBAL.ASA 이벤트 소싱 앱](media/stream-analytics-solution-patterns/eventsourcingapp.png)

이 솔루션 패턴에서는 이벤트를 처리 하 고 Azure Stream Analytics 하 여 데이터 저장소에 집계 합니다. 응용 프로그램 계층은 일반적인 요청/응답 패턴을 사용 하 여 데이터 저장소와 상호 작용 합니다. 많은 수의 이벤트를 실시간으로 처리 하는 Stream Analytics 기능으로 인해 응용 프로그램은 데이터 저장소 계층을 대량으로 수행할 필요 없이 확장성이 매우 뛰어납니다. 데이터 저장소 계층은 기본적으로 시스템에서 구체화 된 뷰입니다. [Azure Cosmos DB 출력을 Azure Stream Analytics](stream-analytics-documentdb-output.md) Cosmos DB Stream Analytics 출력으로 사용 되는 방법을 설명 합니다.

처리 논리가 복잡 하 고 논리의 특정 부분을 독립적으로 업그레이드 해야 하는 실제 응용 프로그램에서는 여러 Stream Analytics 작업을 Event Hubs와 함께 중간 이벤트 브로커로 구성할 수 있습니다.

![GLOBAL.ASA 복합 이벤트 소싱 앱](media/stream-analytics-solution-patterns/eventsourcingapp2.png)

이 패턴은 시스템의 복원 력 및 관리 효율성을 향상 시킵니다. 그러나 Stream Analytics는 정확히 한 번 처리를 보장 하는 경우에도 중복 이벤트가 중간 Event Hubs에 있을 수 있습니다. 다운스트림 Stream Analytics 작업이 lookback 창에서 논리 키를 사용 하 여 이벤트를 중복 제거 하는 것이 중요 합니다. 이벤트 배달에 대 한 자세한 내용은 [이벤트 배달 보장](/stream-analytics-query/event-delivery-guarantees-azure-stream-analytics) 참조를 참조 하세요.

## <a name="use-reference-data-for-application-customization"></a>응용 프로그램 사용자 지정에 참조 데이터 사용

Azure Stream Analytics 참조 데이터 기능은 경고 임계값, 처리 규칙 및 [지역 구분](geospatial-scenarios.md)같은 최종 사용자 사용자 지정을 위해 특별히 설계 되었습니다. 응용 프로그램 계층은 매개 변수 변경 내용을 받아들이고 SQL 데이터베이스에 저장할 수 있습니다. Stream Analytics 작업은 정기적으로 데이터베이스의 변경 내용을 쿼리하고 참조 데이터 조인을 통해 사용자 지정 매개 변수를 액세스할 수 있도록 합니다. 응용 프로그램 사용자 지정에 참조 데이터를 사용 하는 방법에 대 한 자세한 내용은 [SQL 참조 데이터](sql-reference-data.md) 및 [참조 데이터 조인](/stream-analytics-query/reference-data-join-azure-stream-analytics)을 참조 하세요.

이 패턴을 사용 하 여 규칙의 임계값이 참조 데이터에서 정의 되는 규칙 엔진을 구현할 수도 있습니다. 규칙에 대 한 자세한 내용은 [Azure Stream Analytics에서 구성 가능한 임계값 기반 규칙 처리](stream-analytics-threshold-based-rules.md)를 참조 하세요.

![GLOBAL.ASA 참조 데이터 앱](media/stream-analytics-solution-patterns/refdataapp.png)

## <a name="add-machine-learning-to-your-real-time-insights"></a>실시간 정보에 Machine Learning 추가

Azure Stream Analytics ' 기본 제공 [변칙 검색 모델](stream-analytics-machine-learning-anomaly-detection.md) '은 실시간 응용 프로그램에 Machine Learning를 도입 하는 편리한 방법입니다. 보다 광범위 한 Machine Learning 요구 사항은 [Azure Stream Analytics Azure Machine Learning의 점수 매기기 서비스와 통합](stream-analytics-machine-learning-integration-tutorial.md)을 참조 하세요.

온라인 학습 및 점수 매기기를 동일한 Stream Analytics 파이프라인에 통합 하려는 고급 사용자의 경우 [선형 회귀](stream-analytics-high-frequency-trading.md)를 사용 하 여이 작업을 수행 하는 방법의 예제를 참조 하세요.

![GLOBAL.ASA Machine Learning 앱](media/stream-analytics-solution-patterns/mlapp.png)

## <a name="near-real-time-data-warehousing"></a>거의 실시간 데이터 웨어하우징

또 다른 일반적인 패턴은 스트리밍 데이터 웨어하우스 라고도 하는 실시간 데이터 웨어하우징입니다. 응용 프로그램에서 Event Hubs 및 IoT Hub에 도착 하는 이벤트 외에도 [IoT Edge에서 실행](stream-analytics-edge.md) 되는 Azure Stream Analytics를 사용 하 여 데이터 정리, 데이터 감소, 데이터 저장소 및 전달 요구 사항을 충족할 수 있습니다. IoT Edge에서 실행 되는 Stream Analytics 시스템에서 대역폭 제한 및 연결 문제를 정상적으로 처리할 수 있습니다. SQL 출력 어댑터를 사용 하 여 SQL Data Warehouse에 출력할 수 있습니다. 그러나 최대 처리량은 10mb/s로 제한 됩니다.

![GLOBAL.ASA 데이터 웨어하우징](media/stream-analytics-solution-patterns/datawarehousing.png)

약간의 대기 시간으로 처리량을 향상 시키는 한 가지 방법은 이벤트를 Azure Blob storage에 보관 한 다음 [Polybase를 사용 하 여 SQL Data Warehouse로 가져오는](../sql-data-warehouse/load-data-from-azure-blob-storage-using-polybase.md)것입니다. Stream Analytics의 출력을 blob 저장소에 수동으로 연결 하 고, [타임 스탬프를 통해 데이터를 보관](stream-analytics-custom-path-patterns-blob-storage-output.md) 하 고 주기적으로 가져와서 blob storage에서 SQL Data Warehouse로 입력 해야 합니다.

이 사용 패턴에서 Azure Stream Analytics은 거의 실시간 ETL 엔진으로 사용 됩니다. 새로 도착 한 이벤트는 계속 변환 되어 다운스트림 분석 서비스 사용을 위해 저장 됩니다.

![GLOBAL.ASA 높은 처리량 데이터 웨어하우징](media/stream-analytics-solution-patterns/datawarehousing2.png)

## <a name="archiving-real-time-data-for-analytics"></a>분석에 대 한 실시간 데이터 보관

대부분의 데이터 과학 및 분석 작업은 계속 오프 라인으로 수행 됩니다. Azure Data Lake Store Gen2 output 및 Parquet 출력 형식을 통해 Azure Stream Analytics 하 여 데이터를 보관할 수 있습니다. 이 기능은 Azure Data Lake Analytics, Azure Databricks 및 Azure HDInsight에 직접 데이터를 피드 하는 마찰을 제거 합니다. Azure Stream Analytics는이 솔루션에서 거의 실시간 ETL 엔진으로 사용 됩니다. 다양 한 계산 엔진을 사용 하 여 Data Lake에서 보관 된 데이터를 탐색할 수 있습니다.

![GLOBAL.ASA 오프 라인 분석](media/stream-analytics-solution-patterns/offlineanalytics.png)

## <a name="use-reference-data-for-enrichment"></a>보강에 대 한 참조 데이터 사용

데이터 보강은 종종 ETL 엔진을 위한 요구 사항입니다. Azure Stream Analytics는 SQL database와 Azure Blob storage의 [참조 데이터](stream-analytics-use-reference-data.md) 를 사용 하 여 데이터 보강 지원 합니다. 데이터 보강 Azure Data Lake와 SQL Data Warehouse 모두에 데이터를 입력할 수 있습니다.

![데이터 보강를 사용 하는 GLOBAL.ASA 오프 라인 분석](media/stream-analytics-solution-patterns/offlineanalytics.png)

## <a name="operationalize-insights-from-archived-data"></a>보관 된 데이터에서 통찰력 운영

오프 라인 분석 패턴과 거의 실시간 응용 프로그램 패턴을 결합 하는 경우 피드백 루프를 만들 수 있습니다. 피드백 루프를 통해 응용 프로그램은 데이터의 패턴을 변경 하기 위해 자동으로 조정할 수 있습니다. 이 피드백 루프는 경고에 대 한 임계값을 변경 하는 것 처럼 간단할 수도 있고, Machine Learning 모델을 다시 학습 하는 것 처럼 복잡할 수도 있습니다. 동일한 솔루션 아키텍처를 클라우드 및 IoT Edge에서 실행 되는 모든 작업에 적용할 수 있습니다.

![운영 화](media/stream-analytics-solution-patterns/insightsoperationalization.png)

## <a name="how-to-monitor-asa-jobs"></a>GLOBAL.ASA 작업을 모니터링 하는 방법

Azure Stream Analytics 작업은 24/7를 실행 하 여 들어오는 이벤트를 실시간으로 계속 처리할 수 있습니다. 작동 시간 보장은 전체 응용 프로그램의 상태에 매우 중요 합니다. [99.9%의 가용성 보장](https://azure.microsoft.com/support/legal/sla/stream-analytics/v1_0/)을 제공 하는 업계의 유일한 스트리밍 분석 서비스인 Stream Analytics는 여전히 약간의 중단 시간을 발생 시킬 수 있습니다. 몇 년 동안 Stream Analytics는 작업 상태를 반영 하는 메트릭, 로그 및 작업 상태를 소개 했습니다. 이러한 모든 항목은 Azure Monitor 서비스를 통해 표시 되며 OMS로 추가로 내보낼 수 있습니다. 자세한 내용은 [Stream Analytics 작업 모니터링 및 쿼리 모니터링 방법 이해](stream-analytics-monitoring.md)를 참조 하세요.

![GLOBAL.ASA 모니터링](media/stream-analytics-solution-patterns/monitoring.png)

모니터링 해야 하는 두 가지 주요 사항은 다음과 같습니다.

- [작업 실패 상태](job-states.md)

    무엇 보다도 작업이 실행 중인지 확인 해야 합니다. 작업이 실행 중 상태가 아니면 새 메트릭 또는 로그가 생성 되지 않습니다. 작업은 높은 SU 사용률 수준 (즉, 리소스 부족)을 포함 하 여 다양 한 이유로 인해 실패 한 상태로 변경 될 수 있습니다.

- [워터 마크 지연 메트릭](https://azure.microsoft.com/blog/new-metric-in-azure-stream-analytics-tracks-latency-of-your-streaming-pipeline/)

    이 메트릭은 처리 파이프라인의 이후 경과한 시간 (초)을 반영 합니다. 일부 지연 시간은 내재 된 처리 논리의 특성을 갖습니다. 따라서 추세를 모니터링 하는 것은 절대값을 모니터링 하는 것 보다 훨씬 더 중요 합니다. 안정적인 상태 지연은 모니터링 또는 경고가 아닌 응용 프로그램 디자인에 의해 처리 되어야 합니다.

오류가 발생 하면 활동 로그와 [진단 로그가](stream-analytics-job-diagnostic-logs.md) 오류를 찾기 시작 하는 데 가장 적합 한 위치입니다.

## <a name="build-resilient-and-mission-critical-applications"></a>복원 력 및 중요 업무용 응용 프로그램 빌드

Azure Stream Analytics의 SLA 보증 및 종단 간 응용 프로그램을 실행 하는 방법에 관계 없이 중단이 발생 합니다. 응용 프로그램이 중요 업무용 인 경우 정상적으로 복구 하기 위해 중단을 준비 해야 합니다.

경고 응용 프로그램의 경우 가장 중요 한 사항은 다음 경고를 검색 하는 것입니다. 이전 경고를 무시 하 고 복구할 때 현재 시간에서 작업을 다시 시작 하도록 선택할 수 있습니다. 작업 시작 시간 의미 체계는 첫 번째 입력 시간이 아닌 첫 번째 출력 시간입니다. 입력은 지정 된 시간에 첫 번째 출력이 완료 되 고 수정 되도록 적절 한 시간 동안 되감을 됩니다. 결과적으로 부분 집계를 받고 예기치 않게 경고를 트리거합니다.

또한 과거의 일정 시간 동안 출력을 시작 하도록 선택할 수도 있습니다. Event Hubs 및 IoT Hub의 보존 정책은 모두 과거의 처리를 허용 하는 적절 한 양의 데이터를 보유 합니다. 단점은 현재 시간까지 파악 하 고 시기 적절 한 새 경고를 생성 하기 시작할 수 있는 속도입니다. 시간이 지남에 따라 데이터의 값이 급증 하므로 현재 시간을 신속 하 게 파악 하는 것이 중요 합니다. 다음 두 가지 방법으로 신속 하 게 파악할 수 있습니다.

- 등록할 때 더 많은 리소스 (SU)를 프로 비전 합니다.
- 현재 시간에서 다시 시작 합니다.

현재 시간에서 다시 시작 하는 작업은 처리 중에 간격이 유지 되는 것과 마찬가지로 간단 합니다. 이러한 방식으로 다시 시작 하는 것은 경고 시나리오에는 문제가 없지만 대시보드 시나리오에는 문제가 발생할 수 있으며, 보관 및 데이터 웨어하우징 시나리오에 대 한 비 스타터 시나리오입니다.

리소스를 추가로 프로 비전 하면 프로세스의 속도를 높일 수 있지만 처리 속도 서 수를 갖는 효과는 복잡 합니다.

- 더 많은 수의 SUs로 작업을 확장할 수 있는지 테스트 합니다. 모든 쿼리를 확장할 수 있는 것은 아닙니다. 쿼리가 [병렬](stream-analytics-parallelization.md)처리 되는지 확인 해야 합니다.

- 업스트림 Event Hubs에 파티션이 충분 한지 확인 하거나 더 많은 처리량 단위 (Tu)를 추가 하 여 입력 처리량을 확장할 수 IoT Hub 합니다. 각 Event Hubs TU는 2 m b/s의 출력 속도로 최대로 출력 합니다.

- 출력 싱크에 충분 한 리소스를 프로 비전 했는지 확인 합니다 (예: SQL Database Cosmos DB). 출력에서 서 수를 제한 하지 않으므로 때때로 시스템이 잠기게 될 수 있습니다.

가장 중요 한 것은 처리 비율 변경을 예상 하 고, 프로덕션으로 전환 하기 전에 이러한 시나리오를 테스트 하 고, 오류 복구 시간 동안 처리를 올바르게 확장할 수 있도록 준비 하는 것입니다.

들어오는 이벤트가 모두 지연 되는 극단적인 시나리오에서 작업에 지연 도착 창을 적용 한 경우 [지연 된 이벤트는 모두 삭제 될 수](stream-analytics-time-handling.md) 있습니다. 이벤트의 삭제는 처음에는 알 수 없는 동작으로 나타날 수 있습니다. 그러나 Stream Analytics를 고려 하는 것은 실시간 처리 엔진 이므로 들어오는 이벤트는 벽 시계 시간에 근접 하 게 될 것으로 예상 합니다. 이러한 제약 조건을 위반 하는 이벤트를 삭제 해야 합니다.

### <a name="lambda-architectures-or-backfill-process"></a>람다 아키텍처 또는 백필 프로세스

다행히 이전 데이터 보관 패턴은 이러한 지연 이벤트를 정상적으로 처리 하는 데 사용할 수 있습니다. 보관 작업은 도착 시간에 들어오는 이벤트를 처리 하 고, Azure Blob의 적절 한 시간 버킷에 이벤트를 보관 하거나 이벤트 시간으로 Azure Data Lake Store 하는 것입니다. 이벤트가 발생 하는 빈도는 중요 하지 않습니다 .이는 삭제 되지 않습니다. 항상 올바른 시간 버킷에 배치 됩니다. 복구 중에는 보관 된 이벤트를 다시 처리 하 고 선택한 저장소에 결과를 백필 할 수 있습니다. 이는 람다 패턴을 구현 하는 방법과 비슷합니다.

![GLOBAL.ASA 백필](media/stream-analytics-solution-patterns/backfill.png)

백필 프로세스는 Azure Stream Analytics와 다른 프로그래밍 모델을 사용 하는 오프 라인 일괄 처리 시스템을 사용 하 여 수행 해야 합니다. 즉, 전체 처리 논리를 다시 구현 해야 합니다.

백필의 경우에는 최소한 출력 싱크에 더 많은 리소스를 임시로 프로 비전 하 여 안정적인 상태 처리 요구 보다 높은 처리량을 처리 하는 것이 중요 합니다.

|시나리오  |지금 다시 시작  |마지막 중지 시간에서 다시 시작 |지금 다시 시작 + 보관 이벤트를 사용 하 여 백필|
|---------|---------|---------|---------|
|**대시보드**   |간격 만들기    |단기 중단에 대 한 확인    |장시간 가동 중단에 사용 |
|**경고**   |용인되는 |단기 중단에 대 한 확인    |필요 없음 |
|**이벤트 소싱 앱** |용인되는 |단기 중단에 대 한 확인    |장시간 가동 중단에 사용 |
|**데이터 웨어하우징**   |데이터 손실  |용인되는 |필요 없음 |
|**오프 라인 분석**  |데이터 손실  |용인되는 |필요 없음|

## <a name="putting-it-all-together"></a>모든 항목 요약

위에서 언급 한 모든 솔루션 패턴을 복잡 한 종단 간 시스템에서 함께 결합할 수 있는 것은 아닙니다. 결합 된 시스템에는 대시보드, 경고, 이벤트 소싱 응용 프로그램, 데이터 웨어하우징, 오프 라인 분석 기능 등이 포함 될 수 있습니다.

키는 구성 가능한 패턴으로 시스템을 설계 하 여 각 하위 시스템을 독립적으로 빌드, 테스트, 업그레이드 및 복구할 수 있도록 하는 것입니다.

## <a name="next-steps"></a>다음 단계

이제 Azure Stream Analytics를 사용 하 여 다양 한 솔루션 패턴을 살펴보았습니다. 다음으로 Stream Analytics 작업을 심층적으로 알아보고 첫 번째 Stream Analytics 작업을 만들 수 있습니다.

* [Azure Portal을 사용하여 Stream Analytics 작업 만들기](stream-analytics-quick-create-portal.md)
* [Azure PowerShell을 사용하여 Stream Analytics 작업 만들기](stream-analytics-quick-create-powershell.md)
* [Visual Studio를 사용하여 Stream Analytics 작업 만들기](stream-analytics-quick-create-vs.md)
