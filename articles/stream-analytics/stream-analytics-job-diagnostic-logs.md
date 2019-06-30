---
title: 진단 로그를 사용하여 Azure Stream Analytics 문제 해결
description: 이 문서에서는 Azure Stream Analytics에서 진단 로그를 분석하는 방법을 설명합니다.
services: stream-analytics
author: jseb225
ms.author: jeanb
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.openlocfilehash: a41c3f60d4b949f78c0755f97c9ef7e6302d78d8
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/22/2019
ms.locfileid: "67329990"
---
# <a name="troubleshoot-azure-stream-analytics-by-using-diagnostics-logs"></a>진단 로그를 사용하여 Azure Stream Analytics 문제 해결

경우에 따라 Azure Stream Analytics 작업이 예기치 않게 처리를 중지합니다. 이 이벤트 종류의 문제를 해결하는 것이 중요합니다. 오류는 예기치 않은 쿼리 결과, 디바이스에 대한 연결 또는 예기치 않은 서비스 중단으로 인해 발생할 수 있습니다. Stream Analytics에서 진단 로그를 사용하면 이와 같은 오류가 발생했을 때 문제 원인을 파악하고 복구 시간을 단축할 수 있습니다.

모든 프로덕션 작업에 대 한 진단 로그를 사용 하도록 설정 하는 것이 좋습니다.

## <a name="log-types"></a>로그 형식

Stream Analytics에서는 다음과 같은 두 가지 형식의 로그를 제공합니다.

* [활동 로그](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)(Always On) - 작업에서 수행되는 조작에 대한 인사이트를 제공합니다.

* [진단 로그](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)(구성 가능) - 작업과 관련하여 발생하는 모든 항목에 대해 더 다양한 인사이트를 제공합니다. 진단 로그는 작업이 생성될 때 시작되고 작업이 삭제될 때 종료됩니다. 작업이 업데이트되고 실행되는 동안 이벤트를 처리합니다.

> [!NOTE]
> Azure Storage, Azure Event Hubs와 같은 서비스를 사용할 수 있습니다 및 맞지 않는 데이터를 분석 하려면 Azure Monitor를 기록 합니다. 그러한 서비스에 대한 가격 책정 모델에 따라 요금이 청구됩니다.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="debugging-using-activity-logs"></a>활동 로그를 사용하여 디버그

활동 로그는 기본적으로 켜져 있고 Stream Analytics 작업에서 수행한 조작에 대한 높은 수준의 인사이트를 제공합니다. 활동 로그에 있는 정보는 작업에 영향을 미치는 문제의 근본 원인을 찾는 데 도움이 될 수 있습니다. Stream Analytics의 활동 로그를 사용하려면 다음 단계를 수행합니다.

1. Azure Portal에 로그인하고 **개요** 아래에서 **활동 로그**를 선택합니다.

   ![Stream Analytics 활동 로그](./media/stream-analytics-job-diagnostic-logs/stream-analytics-menu.png)

2. 수행된 조작 목록을 볼 수 있습니다. 작업 실패의 원인이 된 조작에는 빨간색 정보 풍선이 있습니다.

3. 해당 요약 보기를 보려면 조작을 클릭합니다. 여기에는 보통 제한된 정보가 표시됩니다. 조작에 대해 자세히 알아보려면 **JSON**을 클릭합니다.

   ![Stream Analytics 활동 로그 조작 요약](./media/stream-analytics-job-diagnostic-logs/operation-summary.png)

4. 아래로 스크롤하여 실패한 조작의 원인이 된 오류의 세부 정보를 제공하는 JSON의 **속성** 섹션으로 이동합니다. 이 예제에서는 범위를 벗어난 위도 값의 런타임 오류로 인해 조작이 실패했습니다. Stream Analytics 작업에 의해 처리 되는 데이터의 불일치 데이터 오류가 발생 합니다. 다른에 대해 알아볼 수 있습니다 [입력 및 출력 데이터 오류 발생 이유 및](https://docs.microsoft.com/azure/stream-analytics/data-errors)합니다.

   ![JSON 오류 세부 정보](./media/stream-analytics-job-diagnostic-logs/error-details.png)

5. JSON의 오류 메시지에 따라 정정 작업을 수행할 수 있습니다. 이 예제에서는 쿼리에 추가해야 하는 위도 값이 -90도-90도 사이인지 확인합니다.

6. 활동 로그에서 오류 메시지를 근본 원인을 식별 하는 데 도움이 없으면 진단 로그를 사용 하도록 설정 하 고 Azure Monitor 로그를 사용 합니다.

## <a name="send-diagnostics-to-azure-monitor-logs"></a>Azure Monitor 로그에 진단 데이터 보내기

진단 로그 켜기 및 Azure Monitor 로그로 보내는 것이 좋습니다. 진단 로그는 기본적으로 **해제**되어 있습니다. 진단 로그를 켜려면 다음과 같은 단계를 완료합니다.

1.  Azure Portal에 로그인하고 Stream Analytics 작업으로 이동합니다. **모니터링** 아래에서 **진단 로그**를 선택합니다. 다음으로, **진단 켜기**를 선택합니다.

    ![진단 로그에 대한 블레이드 탐색](./media/stream-analytics-job-diagnostic-logs/diagnostic-logs-monitoring.png)  

2.  **진단 설정**에서 **이름**을 만들고 **Log Analytics에 보내기** 옆에 있는 상자를 선택합니다. 그런 다음, 기존 **Log Analytics 작업 영역**을 추가하거나 새 작업 영역을 만듭니다. **로그** 아래에서 **실행** 및 **작성** 상자를 선택하고 **메트릭** 아래에서 **AllMetrics** 상자를 선택합니다. **저장**을 클릭합니다. 추가 비용을 방지 하기 위해 Stream Analytics 작업과 동일한 Azure 지역에서 Log Analytics 작업 영역을 사용 하는 것이 좋습니다.

    ![진단 로그 설정](./media/stream-analytics-job-diagnostic-logs/diagnostic-settings.png)

3. Stream Analytics 작업이 시작되면 진단 로그가 Log Analytics 작업 영역으로 라우팅됩니다. Log Analytics 작업 영역으로 이동하고 **일반** 섹션에서 **로그**를 선택합니다.

   ![일반 섹션에서 azure Monitor 로그](./media/stream-analytics-job-diagnostic-logs/log-analytics-logs.png)

4. [고유한 쿼리를 작성](../azure-monitor/log-query/get-started-portal.md)하여 용어를 검색하고, 추세를 파악하고, 패턴을 분석하고, 데이터를 기반으로 인사이트를 제공할 수 있습니다. 예를 들어 “스트리밍 작업 실패”라는 메시지가 있는 진단 로그만 필터링하는 쿼리를 작성할 수 있습니다. Azure Stream Analytics의 진단 로그는 **AzureDiagnostics** 테이블에 저장됩니다.

   ![진단 쿼리 및 결과](./media/stream-analytics-job-diagnostic-logs/diagnostic-logs-query.png)

5. 적합한 로그를 검색하는 쿼리가 있는 경우 **저장**을 선택하여 쿼리를 저장하고 이름 및 범주를 제공합니다. 그런 다음, **새로운 경고 규칙**을 선택하여 경고를 만들 수 있습니다. 다음으로, 경고 조건을 지정합니다. **조건**을 선택하고 임계값 및 이 사용자 지정 로그 검색을 평가할 빈도를 입력합니다.  

   ![진단 로그 검색 쿼리](./media/stream-analytics-job-diagnostic-logs/search-query.png)

6. 경고 규칙을 만들기 전에 작업 그룹을 선택하고 이름 및 설명과 같은 경고 세부 정보를 지정합니다. 다양한 작업의 진단 로그를 동일한 Log Analytics 작업 영역으로 라우팅할 수 있습니다. 이렇게 하면 모든 작업에서 작동하는 경고를 한 번만 설정하면 됩니다.  

## <a name="diagnostics-log-categories"></a>진단 로그 범주

Azure Stream Analytics는 두 가지 범주의 진단 로그를 캡처합니다.

* **작성**: 작업 생성, 입/출력 추가 및 삭제, 쿼리 추가 및 업데이트, 작업 시작 및 중지 등 작업 작성 조작에 관련된 로그 이벤트를 캡처합니다.

* **실행**: 작업 실행 중 발생하는 이벤트를 캡처합니다.
    * 연결 오류
    * 다음을 포함하는 데이터 처리 오류
        * 쿼리 정의를 따르지 않는 이벤트(필드 형식 및 값 불일치, 필드 누락 등)
        * 식 평가 오류
    * 다른 이벤트 및 오류

## <a name="diagnostics-logs-schema"></a>진단 로그 스키마

모든 로그는 JSON 형식으로 저장됩니다. 각 항목에는 다음과 같은 일반적인 문자열 필드가 있습니다.

Name | 설명
------- | -------
time | 로그의 타임스탬프(UTC)입니다.
ResourceId | 작업이 수행되는 리소스의 ID(대문자)입니다. 여기에는 구독 ID, 리소스 그룹 및 작업 이름이 포함됩니다. 예: **/SUBSCRIPTIONS/6503D296-DAC1-4449-9B03-609A1F4A1C87/RESOURCEGROUPS/MY-RESOURCE-GROUP/PROVIDERS/MICROSOFT.STREAMANALYTICS/STREAMINGJOBS/MYSTREAMINGJOB**.
category | 로그 범주로, **실행** 또는 **작성** 중 하나입니다.
operationName | 기록된 작업의 이름 예: **이벤트 전송: mysqloutput에 대한 SQL 출력 쓰기 실패**.
status | 작업의 상태입니다. 예: **실패** 또는 **성공**.
level | 로그 수준. 예: **오류**, **경고** 또는 **정보**.
properties | 로그 항목별 세부 정보로, JSON 문자열로 직렬화됩니다. 자세한 내용은 이 문서의 다음 섹션을 참조하세요.

### <a name="execution-log-properties-schema"></a>실행 로그 속성 스키마

실행 로그에는 Stream Analytics 작업 실행 중에 발생한 이벤트에 대한 정보가 포함됩니다. 속성의 스키마는 이벤트 데이터 오류 또는 일반 이벤트 인지에 따라 달라 집니다.

### <a name="data-errors"></a>데이터 오류

작업이 데이터를 처리하는 동안 발생한 오류는 이 로그 범주에 속합니다. 이러한 로그는 데이터 읽기, serialization 및 쓰기 작업 도중에 가장 자주 생성됩니다. 이러한 로그는 연결 오류를 포함하지 않습니다. 연결 오류는 일반 이벤트로 처리됩니다. 다른 다양 한 원인에 대 한 자세히 알아볼 수 있습니다 [입력 및 출력 데이터 오류](https://docs.microsoft.com/azure/stream-analytics/data-errors)합니다.

이름 | 설명
------- | -------
source | 오류가 발생한 작업 입력 또는 출력의 이름입니다.
Message | 오류와 연결된 메시지
Type | 오류의 형식입니다. 예: **DataConversionError**, **CsvParserError** 또는 **ServiceBusPropertyColumnMissingError**.
Data | 오류 출처를 정확히 찾는 데 도움이 되는 데이터를 포함합니다. 크기에 따라 잘릴 수 있습니다.

**operationName** 값에 따라 데이터 오류의 스키마는 다음과 같습니다.

* **이벤트 직렬화** 이벤트 읽기 작업 중에 발생 합니다. 이는 데이터 입력 시 쿼리 스키마를 충족하지 않을 때 다음과 같은 이유 중 하나로 발생합니다.

   * *이벤트 (역)직렬화 도중 형식 불일치*: 오류의 원인이 되는 필드를 식별합니다.

   * *이벤트를 읽을 수 없음, 잘못된 serialization*: 오류가 발생한 입력 데이터의 위치 정보를 나열합니다. Blob 입력에 대한 Blob 이름, 오프셋 및 데이터 샘플이 포함됩니다.

* **이벤트 보내기** 쓰기 작업 중에 발생 합니다. 오류를 발생시키는 스트리밍 이벤트를 식별합니다.

### <a name="generic-events"></a>일반 이벤트

일반 이벤트는 다른 모든 항목을 처리합니다.

Name | 설명
-------- | --------
오류 | (선택 사항) 오류 정보입니다. 일반적으로 예외 정보입니다(사용 가능한 경우).
Message| 로그 메시지
Type | 메시지 형식입니다. 내부 오류 분류에 매핑합니다. 예: **JobValidationError** 또는 **BlobOutputAdapterInitializationFailure**.
상관관계 ID | 작업 실행을 고유하게 식별하는 [GUID](https://en.wikipedia.org/wiki/Universally_unique_identifier). 작업 시작 시간부터 작업이 중지될 때까지 모든 실행 로그 항목에는 동일한 **상관 관계 ID** 값이 있습니다.

## <a name="next-steps"></a>다음 단계

* [Stream Analytics 소개](stream-analytics-introduction.md)
* [Stream Analytics 시작](stream-analytics-real-time-fraud-detection.md)
* [Stream Analytics 작업 크기 조정](stream-analytics-scale-jobs.md)
* [Stream Analytics 쿼리 언어 참조](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Stream Analytics 데이터 오류](https://docs.microsoft.com/azure/stream-analytics/data-errors)
