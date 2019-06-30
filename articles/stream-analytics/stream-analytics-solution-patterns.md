---
title: Azure Stream Analytics 솔루션 패턴
description: Azure Stream Analytics에 대 한 솔루션 패턴에 알아봅니다.
author: zhongc
ms.author: zhongc
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.openlocfilehash: 5929ff439bc31e16643e5c57868cd6b68f9cd99c
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/22/2019
ms.locfileid: "67329566"
---
# <a name="azure-stream-analytics-solution-patterns"></a>Azure Stream Analytics 솔루션 패턴

Azure의 다른 많은 서비스와 마찬가지로 Stream Analytics는 가장 다른 서비스를 사용 하 여 더 큰 종단 간 솔루션을 만드는 사용 됩니다. 이 문서에서는 간단한 Azure Stream Analytics 솔루션 및 다양 한 아키텍처 패턴에 설명 합니다. 이러한 패턴을 더 복잡 한 솔루션을 개발 하도록 빌드할 수 있습니다. 이 문서에서 설명한 패턴은 다양 한 시나리오에서에서 사용할 수 있습니다. 시나리오별 패턴의 예에 사용할 [Azure 솔루션 아키텍처](https://azure.microsoft.com/solutions/architecture/?product=stream-analytics)합니다.

## <a name="create-a-stream-analytics-job-to-power-real-time-dashboarding-experience"></a>전원 실시간 대시보드 환경에 Stream Analytics 작업 만들기

Azure Stream Analytics를 사용 하 여 구축할 수 있습니다 신속 하 게 실시간 대시보드 및 경고 합니다. Event Hubs 또는 IoT Hub에서 이벤트를 수집 하는 간단한 솔루션 및 [스트리밍 데이터 집합을 사용 하 여 Power BI 대시보드를 피드](/power-bi/service-real-time-streaming)합니다. 자세한 내용은 자세한 자습서를 참조 [Stream Analytics를 사용 하 여 전화 통화 데이터를 분석 하 고 결과 Power BI 대시보드에서 시각화할](stream-analytics-manage-job.md)합니다.

![ASA Power BI 대시보드](media/stream-analytics-solution-patterns/pbidashboard.png)

Azure portal에서 단 몇 분 후에이 솔루션을 빌드할 수 있습니다. 관련 없는 광범위 한 코딩 되며 SQL 언어는 비즈니스 논리를 표현 하는 데 사용 됩니다.

이 솔루션 패턴 브라우저에서 Power BI 대시보드로 이벤트 원본에서 가장 낮은 대기 시간을 제공합니다. Azure Stream Analytics는이 기본 제공 기능을 사용 하 여 Azure 서비스입니다.

## <a name="use-sql-for-dashboard"></a>대시보드에 대 한 SQL을 사용 합니다.

Power BI 대시보드는 짧은 대기 시간을 제공 하지만 완전 한 Power BI 보고서를 생성 하기 위해 사용할 수 없습니다. 보고 하는 일반적인 패턴은 먼저 SQL database에 데이터를 출력 합니다. 최신 데이터에 대 한 SQL 쿼리를 Power BI의 SQL 커넥터를 사용 합니다.

![ASA SQL 대시보드](media/stream-analytics-solution-patterns/sqldashboard.png)

SQL database를 사용 하면 보다 유연 하 게 하지만 약간 더 높은 대기 시간을 희생 합니다. 이 솔루션은 1 초 보다 큰 대기 시간 요구 사항 사용 하 여 작업에 적합 합니다. 이 메서드를 사용 하 여 추가 조각에 Power BI 기능을 최대화할 수 있으며 보고서 및 시각화 옵션을 훨씬 더 많은 데이터를 분할 수 있습니다. 또한 Tableau와 같은 다른 대시보드 솔루션을 사용 하 여 유연 합니다.

SQL은 처리량이 높은 데이터 저장소 아닙니다. Azure Stream Analytics에서 SQL database에 대 한 최대 처리량은 현재 약 24 MB/s입니다. 빠른 속도로 데이터를 생성 하는 솔루션의 이벤트 소스를 Stream Analytics에서 처리 논리를 사용 하 여 SQL 출력 속도 줄입니다 해야 합니다. Temporal 조인을 사용 하 여 일치 하는 기간 이동 집계, 필터링 등의 기술을 패턴 및 분석 기능을 사용할 수 있습니다. Sql 출력 속도 최적화할 수 있습니다. 추가에 설명 된 기술을 사용 하 여 [Azure SQL Database에 Azure Stream Analytics 출력](stream-analytics-sql-output-perf.md)합니다.

## <a name="incorporate-real-time-insights-into-your-application-with-event-messaging"></a>이벤트 메시징을 사용 하 여 응용 프로그램에 대 한 실시간 정보를 통합 합니다.

가장 인기 있는 두 번째 Stream Analytics 사용 실시간 경고를 생성 하는 것입니다. 이 솔루션 패턴에서 Stream Analytics의 비즈니스 논리를 사용할 수를 검색할 [temporal 및 공간 패턴](stream-analytics-geospatial-functions.md) 또는 [변칙](stream-analytics-machine-learning-anomaly-detection.md), 다음 신호 경고 생성 합니다. 그러나 여기서 Stream Analytics가 Power BI를 사용 하 여 기본 끝점으로 대시보드 솔루션과 달리 다양 한 중간 데이터 싱크로 사용할 수 있습니다. 이러한 싱크는 Event Hubs, Service Bus 및 Azure Functions를 포함 합니다. 응용 프로그램 작성기로 결정 해야 데이터 싱크는 시나리오에 적합 합니다.

기존 비즈니스 워크플로에서 경고를 생성 하도록 다운스트림 이벤트 소비자 논리를 구현 합니다. Azure Functions Azure Functions에서 사용자 지정 논리를 구현할 수 있습니다, 되므로 가장 빠른 방법은이 통합을 수행할 수 있습니다. Stream Analytics 작업의 출력에서 찾을 수 있습니다 하는 대로 Azure 함수를 사용 하 여에 대 한 자습서 [Azure Stream Analytics 작업에서 Azure Functions 실행](stream-analytics-with-azure-functions.md)합니다. Azure Functions에는 또한 다양 한 유형의 텍스트 등 전자 메일 알림 지원 합니다. Stream Analytics 및 논리 앱 간에 Event Hubs를 사용 하 여 이러한 통합을 위해 논리 앱을 사용할 수도 있습니다.

![ASA 이벤트 메시징 앱](media/stream-analytics-solution-patterns/eventmessagingapp.png)

Event Hubs는 반면에 가장 유연한 통합 지점을 제공 합니다. Azure Data Explorer 및 Time Series Insights와 같은 다른 많은 서비스는 Event Hubs에서 이벤트를 사용할 수 있습니다. 서비스는 솔루션을 완료 하려면 Azure Stream Analytics에서 Event hubs에 직접 연결할 수 있습니다. Event Hubs는 또한 높은 처리량 메시징 브로커 이러한 통합 시나리오에 대 한 Azure에서 사용할 수 있습니다.

## <a name="dynamic-applications-and-websites"></a>동적 응용 프로그램 및 웹 사이트

대시보드 등의 사용자 지정 실시간 시각화를 만들거나 Azure Stream Analytics 및 Azure SignalR Service를 사용 하 여 지도 시각화를 수 있습니다. SignalR을 사용 하 여 웹 클라이언트 업데이트할 수 있습니다 및 실시간 동적 콘텐츠를 표시 합니다.

![ASA 동적 앱](media/stream-analytics-solution-patterns/dynamicapp.png)

## <a name="incorporate-real-time-insights-into-your-application-through-data-stores"></a>데이터 저장소를 통해 응용 프로그램에 대 한 실시간 정보 통합

대부분의 웹 서비스 및 웹 응용 프로그램 오늘날 프레젠테이션 계층을 제공 하는 요청/응답 패턴을 사용 합니다. 요청/응답 패턴을 간단 하 게 빌드하고 상태 비저장 프런트 엔드 및 Cosmos DB와 같은 확장 가능한 저장소를 사용 하 여 낮은 응답 시간을 사용 하 여 쉽게 확장할 수 있습니다.

종종 높은 데이터 볼륨 CRUD 기반 시스템에서 성능 병목 상태를 만듭니다. 합니다 [이벤트 소싱 패턴 솔루션](/azure/architecture/patterns/event-sourcing) 성능 병목 상태를 해결 하는 데 사용 됩니다. Temporal 패턴 및 insights도 어렵고 비효율적인 기존의 데이터 저장소에서 추출 하 합니다. 최신 대용량 데이터 기반 응용 프로그램을 자주 데이터 흐름 기반 아키텍처를 채택 합니다. 동작 중인 데이터에 대 한 계산 엔진으로 azure Stream Analytics는 핵심 이며 해당 아키텍처의 경우

![ASA 이벤트 소싱 앱](media/stream-analytics-solution-patterns/eventsourcingapp.png)

이 솔루션 패턴의 이벤트 처리 되 고 Azure Stream Analytics에서 데이터 저장소로 집계 합니다. 일반적인 요청/응답 패턴을 사용 하 여 데이터 저장소를 사용 하 여 응용 프로그램 계층 상호 작용 합니다. 많은 이벤트를 처리 하려면 Stream Analytics의 기능으로 인해 실시간으로 응용 프로그램은 뛰어난 데이터 저장소 계층을 대량으로 필요 하지 않습니다. 데이터 저장소 계층에는 시스템의 구체화 된 뷰를 기본적으로 합니다. [Azure Cosmos DB에 azure Stream Analytics 출력](stream-analytics-documentdb-output.md) Stream Analytics 출력으로 Cosmos DB는 사용 하는 방법을 설명 합니다.

여기서 처리 논리는 복잡 하 고 있는 실제 응용 프로그램에서 논리의 특정 부분을 독립적으로 업그레이드 해야 하는, 중간 이벤트 브로커로 서 Event Hubs와 함께 여러 Stream Analytics 작업을 구성할 수 있습니다.

![ASA 복합 이벤트 소싱 앱](media/stream-analytics-solution-patterns/eventsourcingapp2.png)

이 패턴에는 복원 력 및 시스템의 관리 효율성이 향상 됩니다. 그러나 Stream Analytics에서 정확히 한 번 처리를 보장 하며 경우에 중복 이벤트 수 중간 Event Hubs에 도착 하는 경우도 가끔이 됩니다. 논리 키를 사용 하 여 lookback 창의 이벤트 중복 제거를 다운스트림 Stream Analytics 작업에 대 한 것입니다. 이벤트 배달에 대 한 자세한 내용은 참조 하세요. [이벤트 전달 보장이](/stream-analytics-query/event-delivery-guarantees-azure-stream-analytics) 참조 합니다.

## <a name="use-reference-data-for-application-customization"></a>응용 프로그램 사용자 지정에 대 한 참조 데이터 사용

Azure Stream Analytics 참조 데이터 기능 하도록 특별히 지정 된 처리 규칙, 임계값에서 경고와 같은 최종 사용자가 사용자 지정 하 고 [지역 구분](geospatial-scenarios.md)합니다. 응용 프로그램 계층 매개 변수 변경 내용을 적용 하 고 SQL database에 저장할 수 있습니다. Stream Analytics 작업을 데이터베이스에서 변경 내용을 주기적으로 쿼리하고 참조 데이터 조인을 통해 사용자 지정 매개 변수를 액세스할 수 있도록 합니다. 응용 프로그램 사용자 지정에 대 한 참조 데이터를 사용 하는 방법에 대 한 자세한 내용은 참조 하세요. [SQL 참조 데이터](sql-reference-data.md) 하 고 [참조 데이터 조인](/stream-analytics-query/reference-data-join-azure-stream-analytics)합니다.

이 패턴 데도 사용할 수 있습니다 규칙 엔진을 구현 하려면 참조 데이터에서 규칙의 임계값이 여기서 정의 됩니다. 규칙에 대 한 자세한 내용은 참조 하세요. [Azure Stream Analytics에서 구성 가능한 임계값 기반 규칙 처리](stream-analytics-threshold-based-rules.md)합니다.

![ASA 참조 데이터 앱](media/stream-analytics-solution-patterns/refdataapp.png)

## <a name="add-machine-learning-to-your-real-time-insights"></a>실시간 통찰력을 Machine Learning 추가

Azure Stream Analytics의 기본 제공 [변칙 검색 모델](stream-analytics-machine-learning-anomaly-detection.md) 실시간 응용 프로그램에서 Machine Learning 소개 하는 편리한 방법입니다. Machine Learning의 광범위 한 범위에 필요한 참조 [Azure Machine Learning 점수 매기기 서비스를 사용 하 여 Azure Stream Analytics 통합](stream-analytics-machine-learning-integration-tutorial.md)합니다.

온라인 교육 및 점수 매기기 같은 Stream Analytics 파이프라인을 통합 하려는 고급 사용자를 사용 하 여 방법에 대 한이 예제를 참조 하세요 [선형 회귀](stream-analytics-high-frequency-trading.md)합니다.

![ASA Machine Learning 앱](media/stream-analytics-solution-patterns/mlapp.png)

## <a name="near-real-time-data-warehousing"></a>거의 실시간 데이터 웨어하우징

또 다른 일반적인 패턴은 실시간 데이터 웨어하우징, 스트리밍 데이터 웨어하우스 라고도 합니다. 응용 프로그램에서 Event Hubs 및 IoT Hub에 도착 하는 이벤트 외에도 [IoT Edge를 실행 하는 Azure Stream Analytics](stream-analytics-edge.md) 데이터 정리, 데이터 감소 하 고 데이터 저장소 및 전달 요구를 충족 데 사용할 수 있습니다. Stream Analytics IoT Edge에서 실행 중인 대역폭 제한 및 연결 문제는 시스템에서 정상적으로 처리할 수 있습니다. SQL 출력 어댑터를 사용할 수 있습니다 출력 하는 SQL Data Warehouse; 그러나 최대 처리량은 초당 10 1MB로 제한 합니다.

![ASA 데이터 웨어하우징](media/stream-analytics-solution-patterns/datawarehousing.png)

일부 대기 시간 균형 유지를 사용 하 여 처리량을 개선 하는 한 가지 방법은 Azure Blob 저장소에 이벤트를 보관 하는 것 차례로 [Polybase 사용 하 여 SQL Data Warehouse로 가져오는](../sql-data-warehouse/load-data-from-azure-blob-storage-using-polybase.md)합니다. 있습니다 해야 수동으로 함께 붙이기 위한 blob storage에 Stream Analytics에서 출력 및 blob storage에서 입력 하 여 SQL Data Warehouse [타임 스탬프에서 데이터를 보관](stream-analytics-custom-path-patterns-blob-storage-output.md) 주기적으로 가져오고 있습니다.

이 사용 패턴에 Azure Stream Analytics는 거의 실시간 ETL 엔진으로 사용 됩니다. 새로 도착 하는 이벤트가 지속적으로 변형 되 고 다운스트림 분석 서비스 사용을 위한 저장 합니다.

![ASA 처리량이 높은 데이터 웨어하우징](media/stream-analytics-solution-patterns/datawarehousing2.png)

## <a name="archiving-real-time-data-for-analytics"></a>실시간 데이터 분석을 위해 보관

대부분의 데이터 과학 및 분석 작업 계속 오프 라인으로 발생합니다. Azure Data Lake Store Gen2 출력 및 Parquet 출력 형식을 통해 Azure Stream Analytics에서 데이터를 보관할 수 있습니다. 이 기능은 Azure Data Lake Analytics, Azure Databricks 및 Azure HDInsight에 직접 데이터를 제공 하기 위해 충돌을 제거 합니다. Azure Stream Analytics는이 솔루션에서 거의 실시간 ETL 엔진으로 사용 됩니다. 다양 한 계산 엔진을 사용 하 여 Data Lake에서 보관 된 데이터를 탐색할 수 있습니다.

![ASA 오프 라인 분석](media/stream-analytics-solution-patterns/offlineanalytics.png)

## <a name="use-reference-data-for-enrichment"></a>보강에 대 한 참조 데이터 사용

데이터 보강 ETL 엔진에 대 한 요구 사항인 경우가 많습니다. Azure Stream Analytics를 사용 하 여 데이터 보강 지원 [참조 데이터](stream-analytics-use-reference-data.md) SQL database와 Azure Blob storage에서. Azure Data Lake 및 SQL Data Warehouse에 연결 하는 데이터에 대 한 데이터 보강을 수행할 수 있습니다.

![데이터 보강을 사용 하 여 ASA 오프 라인 분석](media/stream-analytics-solution-patterns/offlineanalytics.png)

## <a name="operationalize-insights-from-archived-data"></a>보관 된 데이터에서 통찰력을 운영 화

거의 실시간 응용 프로그램을 사용 하 여 오프 라인 분석 패턴을 결합 하는 경우 패턴 피드백 루프를 만들 수 있습니다. 피드백 루프 응용 프로그램을 데이터의 패턴을 변경 하는 것에 대 한 자동으로 조정할 수 있습니다. 이 피드백 루프는 경고의 임계값을 변경 하는 만큼 간단 하거나 복잡 한 Machine Learning 모델 재 학습으로 수 있습니다. 동일한 솔루션 아키텍처는 클라우드에서 및 IoT Edge를 실행 하는 두 ASA 작업에 적용할 수 있습니다.

![ASA insights 운영 화](media/stream-analytics-solution-patterns/insightsoperationalization.png)

## <a name="how-to-monitor-asa-jobs"></a>ASA 작업을 모니터링 하는 방법

Azure Stream Analytics 작업을 실시간으로 지속적으로 들어오는 이벤트를 처리 하는 데 24/7은 실행할 수 있습니다. 해당 작동 시간 보장은 전체 응용 프로그램의 상태에 중요 합니다. Stream Analytics는 제공 하는 업계에서 유일한 스트리밍 분석 서비스는 [99.9% 가용성을 보장](https://azure.microsoft.com/support/legal/sla/stream-analytics/v1_0/), 어느 정도의 가동 중지 시간이 여전히 발생할 수 있습니다. 년 동안 Stream Analytics에 메트릭, 로그 및 작업 상태는 작업의 상태를 반영 하기 위해 도입 되었습니다. 모든 Azure Monitor 서비스를 통해 표시 됩니다 하 고 OMS로 내보낼 수 있습니다. 자세한 내용은 [이해 하는 Stream Analytics 작업 모니터링 및 쿼리 모니터링 방법](stream-analytics-monitoring.md)합니다.

![ASA 모니터링](media/stream-analytics-solution-patterns/monitoring.png)

두 가지 주요 모니터링 하려면:

- [작업 실패 상태](job-states.md)

    무엇 보다도, 작업이 실행 되 고 있는지 확인 해야 합니다. 실행 중 상태인 작업을 하지 않고 없습니다 새 메트릭 또는 로그 생성 됩니다. 작업은 실패 한 상태로 높은 SU 사용률 수준 등의 다양 한 이유로 변경할 수 있습니다 (즉, 리소스 부족에 있음).

- [워터 마크 지연 메트릭](https://azure.microsoft.com/blog/new-metric-in-azure-stream-analytics-tracks-latency-of-your-streaming-pipeline/)

    이 메트릭에 반영 처리 뒤 얼마나 파이프라인은 벽 시계 시간 (초)입니다. 지연 중 일부는 고유한 처리 논리 특성이 있습니다. 결과적으로, 증가 추세를 모니터링이 절대 값을 모니터링 하는 보다 훨씬 더 중요 합니다. 안정적인 상태 지연 모니터링 또는 경고가 아닌 응용 프로그램 디자인에서 해결 해야 합니다.

실패 하면 활동 로그 및 [진단 로그](stream-analytics-job-diagnostic-logs.md) 시작 오류를 찾는 가장 좋은 장소입니다.

## <a name="build-resilient-and-mission-critical-applications"></a>복원 력 있는 빌드 및 업무용 응용 프로그램

신중 하 게 하는 방법 및 Azure Stream Analytics SLA 보증에 관계 없이 응용 프로그램을 실행할 엔드-투-엔드, 작동 중단 발생 합니다. 중요 업무용 응용 프로그램을 사용 하는 경우 정상적으로 복구 하려면 중단에 대 한 준비 해야 합니다.

응용 프로그램 경고에 대 한 다음 경고를 검색 하는 가장 중요 한 점은입니다. 현재에서 작업을 다시 시작 하도록 선택할 수 있습니다 시점 복구를 이전 경고를 무시 합니다. 첫 번째 출력 시기에 첫 번째 입력된 시간이 아닌 작업 시작 시간 의미 체계는 합니다. 입력 된 적절 한 지정된 된 시간에 첫 번째 출력은 완전 하 고 올바른 보장 하기 위해 시간 이전 버전과 되감깁니다. 부분 집계를 가져오고 결과적으로 경고를 예기치 않게 트리거하 없습니다.

어느 정도의 과거의 지정 시간에서 출력을 시작할 수도 있습니다. IoT Hub와 Event Hubs 보존 정책을 적절 한 양의 과거 로부터의 완벽 한 처리를 허용 하는 데이터를 보유 합니다. 단점은 현재 시간으로 캐치 업 시기 적절 하 게 새 경고를 생성 하기 시작 하는 속도입니다. 데이터 값을 잃기 신속 하 게 시간이 지남에 따라 현재 시간으로 신속 하 게 따라잡을 수 있으므로 합니다. 두 가지 방법으로 신속 하 게 따라잡을 수 있습니다.

- 캐치 업 하는 경우 더 많은 리소스 (SU)을 프로 비전 합니다.
- 현재 시간에서 다시 시작 합니다.

현재 다시 시작에 간단 하 게 간격을 처리 하는 동안 유지 균형을 사용 하 여 수행 합니다. 이러한 방식으로 다시 시작 확인 경고 시나리오를 사용할 수 있습니다 하지만 대시보드 시나리오에 대 한 문제가 될 수 있습니다 이며 시동 보관 및 데이터 웨어하우징 시나리오에 대 한 합니다.

프로세스 속도 높일 수 더 많은 리소스를 프로 비전 하지만 효과가 처리 속도 급증은 복잡 합니다.

- 테스트는 작업은 많은 Su로 확장 가능 합니다. 일부 쿼리는 확장 가능 합니다. 쿼리 인지 확인 해야 [병렬화](stream-analytics-parallelization.md)합니다.

- 업스트림 Event Hubs 또는 IoT Hub 더 많은 처리량 단위 (Tu) 입력된 처리량 확장을 추가할 수 있는 충분 한 파티션을 없는지 확인 합니다. 2 MB/s의 출력 속도로 각 Event Hubs TU 최대한 활용 합니다.

- 해야 하므로 때로는 수 있는 출력의 급격 제한 없는 것으로 인해 시스템이 잠글, 출력 싱크 (즉, SQL Database, Cosmos DB)에 충분 한 리소스를 프로 비전 합니다.

가장 중요 한 점은 처리 속도 변경 것으로 예상 되, 프로덕션 환경으로 이동 하기 전에 이러한 시나리오를 테스트 및 장애 복구 동안 올바르게 처리 하는 확장 준비가 되어야 하는 것입니다.

극단적인 시나리오에서 들어오는 이벤트는 모든 지연 [지연 된 이벤트는 삭제 가능한 모든 것이](stream-analytics-time-handling.md) 지연 도착 창 작업에 적용 한 경우. 이벤트 삭제 보일 수도 알 수 없는 동작을 시작 합니다. 그러나 Stream Analytics는 실시간 처리 엔진을 고려할 때 들어오는 이벤트를 벽 시계 시간에 가까워야 필요 합니다. 이 이러한 제약 조건을 위반 하는 이벤트를 삭제 해야 합니다.

### <a name="backfilling-process"></a>백필 프로세스

다행 스럽게도 이전 데이터 보관 패턴 런타임에 이러한 이벤트를 정상적으로 처리 하는 데 사용할 수 있습니다. 개념은 보관 작업이 도착 시간에서 들어오는 이벤트를 처리할 이벤트 시간을 사용 하 여 Azure Blob 또는 Azure Data Lake Store에 적절 한 시간 버킷으로 이벤트를 보관입니다. 이벤트 도착 지연 하는 방법 뿐 아니라는 삭제 되지 않습니다. 이 항상 적절 한 시간 버킷의 이동 합니다. 복구 하는 동안 선택한 저장소에 결과 보관 된 이벤트 및 백필을 다시 처리 하는 것이 같습니다.

![ASA 백필](media/stream-analytics-solution-patterns/backfill.png)

백필 프로세스가 있는 시스템 가능성이 가장 높은 Azure Stream Analytics 보다 다양 한 프로그래밍 모델을 처리 하는 오프 라인 일괄 처리를 사용 하 여 수행 해야 합니다. 즉, 다시 전체 처리 논리를 구현 해야 합니다.

백필, 요구를 처리 하는 안정 상태 보다 더 높은 처리량을 처리 하도록 더 많은 리소스를 출력 싱크 프로 비전에 적어도 일시적으로 여전히 중요 한 것입니다.

|시나리오  |이제 에서만 다시 시작  |마지막 중지 시간에서 다시 시작 |현재 날짜 + 보관된 되는 이벤트를 사용 하 여 백필에서 다시 시작|
|---------|---------|---------|---------|
|**대시보드**   |간격을 만듭니다.    |짧은 가동 중단에 대 한 확인    |긴 시간 동안 중단 사용 |
|**경고**   |용인되는 |짧은 가동 중단에 대 한 확인    |반드시 그렇지는 않음 |
|**이벤트 소싱 앱** |용인되는 |짧은 가동 중단에 대 한 확인    |긴 시간 동안 중단 사용 |
|**데이터 웨어하우징**   |데이터 손실  |용인되는 |반드시 그렇지는 않음 |
|**오프 라인 분석**  |데이터 손실  |용인되는 |반드시 그렇지는 않음|

## <a name="putting-it-all-together"></a>모든 항목 요약

위에서 언급 한 솔루션 패턴을 모든 복잡 한 종단 간 시스템에 함께 결합할 수는 어렵지 않습니다. 대시보드, 경고, 이벤트 소싱 응용 프로그램, 데이터 웨어하우징, 및 오프 라인 분석 기능과 결합 된 시스템 포함할 수 있습니다.

시스템 구성 가능한 패턴에 각 하위 시스템을 구축할 수 있도록 테스트 업그레이드 디자인과 독립적으로 복구 하는 핵심이입니다.

## <a name="next-steps"></a>다음 단계

이제 Azure Stream Analytics를 사용 하 여 솔루션 패턴의 다양 한을 살펴보았습니다. 다음으로 Stream Analytics 작업을 심층적으로 알아보고 첫 번째 Stream Analytics 작업을 만들 수 있습니다.

* [Azure Portal을 사용하여 Stream Analytics 작업 만들기](stream-analytics-quick-create-portal.md)
* [Azure PowerShell을 사용하여 Stream Analytics 작업 만들기](stream-analytics-quick-create-powershell.md)
* [Visual Studio를 사용하여 Stream Analytics 작업 만들기](stream-analytics-quick-create-vs.md)
