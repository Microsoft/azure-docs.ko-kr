---
title: 지원 되는 데이터 형식-Azure Time Series Insights Gen2 | Microsoft Docs
description: Azure Time Series Insights Gen2에서 지원 되는 데이터 형식에 대해 알아봅니다.
author: lyrana
ms.author: lyhughes
manager: deepakpalled
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 08/31/2020
ms.openlocfilehash: 4e6586453469797458bc60fc7499a45a9aad9b9b
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89226746"
---
# <a name="supported-data-types"></a>지원되는 데이터 형식

다음 표에서는 Azure Time Series Insights Gen2에서 지 원하는 데이터 형식을 나열 합니다.

| 데이터 형식 | Description | 예제 | [시계열 식 구문](https://docs.microsoft.com/rest/api/time-series-insights/reference-time-series-expression-syntax) | Parquet의 속성 열 이름
|---|---|---|---|---|
| **bool** | `true` 또는 `false`의 두 가지 상태 중 하나인 데이터 형식입니다. | `"isQuestionable" : true` | `$event.isQuestionable.Bool` 또는 `$event['isQuestionable'].Bool` | `isQuestionable_bool`
| **datetime** | 일반적으로 날짜와 시간으로 표현된 시간의 한 순간을 나타냅니다. [ISO 8601](https://www.iso.org/iso-8601-date-and-time-format.html) 형식으로 표현됩니다. Datetime 속성은 항상 UTC 형식으로 저장 됩니다. 형식이 올바르게 지정 된 경우 표준 시간대 오프셋은 적용 되며 UTC로 저장 된 값이 적용 됩니다. 환경 타임 스탬프 속성 및 datetime 오프셋에 대 한 자세한 내용은 [이](concepts-streaming-ingestion-event-sources.md#event-source-timestamp) 섹션을 참조 하세요. | `"eventProcessedLocalTime": "2020-03-20T09:03:32.8301668Z"` |  "EventProcessedLocalTime"가 이벤트 원본 타임 스탬프 이면이 고, 그렇지 않으면 `$event.$ts` 입니다. 다른 JSON 속성인 경우 또는입니다. `$event.eventProcessedLocalTime.DateTime``$event['eventProcessedLocalTime'].DateTime` | `eventProcessedLocalTime_datetime`
| **double** | 배정밀도 64 비트 숫자  | `"value": 31.0482941` | `$event.value.Double` 또는 `$event['value'].Double` |  `value_double`
| **long** | 부호 있는 64 비트 정수입니다.  | `"value" : 31` | `$event.value.Long` 또는 `$event['value'].Long` |  `value_long`
| **string** | 텍스트 값은 유효한 u t f-8로 구성 되어야 합니다. Null 및 빈 문자열은 동일 하 게 처리 됩니다. |  `"site": "DIM_MLGGG"`| `$event.site.String` 또는 `$event['site'].String`| `site_string`
| **dynamic** | 배열 또는 속성 모음 (사전)으로 구성 된 복합 (기본이 아닌) 형식입니다. 현재는 기본 형식의 JSON 배열 또는 TS ID 또는 timestamp 속성을 포함 하지 않는 개체의 배열만 문자열 형식 동적으로 저장 됩니다. 개체를 평면화 하는 방법 및 배열을 unrolled 수 있는 방법을 이해 하려면이 [문서](./concepts-json-flattening-escaping-rules.md) 를 참조 하세요. 이 형식으로 저장 된 페이로드 속성은 `Explore Events` 원시 이벤트를 보기 위해 TSI 탐색기에서 선택 하거나 [`GetEvents`](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/query/execute#getevents)   클라이언트 쪽 구문 분석을 위한 쿼리 API를 통해 액세스할 수 있습니다. |  `"values": "[197, 194, 189, 188]"` | 시계열 식의 동적 형식 참조는 아직 지원 되지 않습니다. | `values_dynamic`

> [!NOTE]
> 64 비트 정수 값이 지원 되지만 Azure Time Series Insights 탐색기에서 안전 하 게 표현할 수 있는 가장 큰 숫자는 JavaScript 제한으로 인해 9007199254740991 (2 ^ 53-1)입니다. 위의 데이터 모델에서 숫자를 사용 하 여 작업 하는 경우 [시계열 모델 변수](/concepts-variables#numeric-variables) 를 만들고 값을 [변환](https://docs.microsoft.com/rest/api/time-series-insights/reference-time-series-expression-syntax#conversion-functions) 하 여 크기를 줄일 수 있습니다.

> [!NOTE]
> **문자열** 형식이 null을 허용 하지 않습니다.
>
> * [시계열 쿼리에서](https://docs.microsoft.com/rest/api/time-series-insights/reference-query-apis) 표현 된 [TSX (시계열 식)](https://docs.microsoft.com/rest/api/time-series-insights/reference-time-series-expression-syntax) 는 빈 문자열 (**' '**)의 값과 **NULL** 을 비교 하는 것과 동일한 방식으로 동작 합니다 .는와 동일 합니다. `$event.siteid.String = NULL` `$event.siteid.String = ''`
> * 원래 이벤트에 빈 문자열이 포함 된 경우에도 API에서 **NULL** 값을 반환할 수 있습니다.
> * 비교 또는 평가를 수행 하기 위해 **문자열** 열의 **NULL** 값에 대 한 종속성을 사용 하지 말고, 빈 문자열과 동일한 방식으로 처리 합니다.

## <a name="sending-mixed-data-types"></a>혼합 데이터 형식 보내기

Azure Time Series Insights Gen2 환경은 강력 하 게 형식화 되어 있습니다. 장치 또는 태그가 장치 속성에 대해 서로 다른 유형의 데이터를 전송 하는 경우 값은 두 개의 분리 된 열에 저장 되며, API 호출에서 시계열 모델 변수 식을 정의할 때 [병합 () 함수](https://docs.microsoft.com/rest/api/time-series-insights/reference-time-series-expression-syntax#other-functions) 를 사용 해야 합니다.

Azure Time Series Insights 탐색기는 동일한 장치 속성의 개별 열을 자동으로 병합 하는 방법을 제공 합니다. 아래 예제에서 센서는 `PresentValue` Long 또는 Double 일 수 있는 속성을 보냅니다. 속성의 모든 저장 된 값 (데이터 형식에 상관 없음)에 대해 쿼리하려면를 `PresentValue` 선택 `PresentValue (Double | Long)` 합니다. 그러면 열이 병합 됩니다.

[![탐색기 자동 병합](media\concepts-supported-data-types/explorer-auto-coalesce-sample.png)](media\concepts-supported-data-types/explorer-auto-coalesce-sample.png#lightbox)

## <a name="objects-and-arrays"></a>개체 및 배열

개체 및 배열과 같은 복합 형식을 이벤트 페이로드의 일부로 보낼 수 있습니다. 중첩 된 개체는 평면화 되 고 배열은 `dynamic` 환경 구성 및 JSON 셰이프에 따라 여러 이벤트를 생성 하기 위해 또는 평면화 됩니다. [JSON 평면화 및 이스케이프 규칙](./concepts-json-flattening-escaping-rules.md) 에 대해 자세히 알아보려면 다음을 참조 하세요.

## <a name="next-steps"></a>다음 단계

* 이벤트를 저장 하는 방법을 이해 하려면 [JSON 평면화 및 이스케이프 규칙](./concepts-json-flattening-escaping-rules.md) 을 참조 하세요.

* 사용자 환경의 [처리량 제한 사항](./concepts-streaming-ingress-throughput-limits.md) 이해

* 스트리밍 데이터를 수집 하는 [이벤트 원본](concepts-streaming-ingestion-event-sources.md) 에 대해 알아봅니다.
