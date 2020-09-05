---
title: 모델 변수-Azure Time Series Insights Gen2 | Microsoft Docs
description: 모델 변수
author: shreyasharmamsft
ms.author: shresha
ms.service: time-series-insights
ms.topic: conceptual
ms.date: 08/12/2020
ms.openlocfilehash: d0a5b48e93e839b0a0adaf185700d7f60fec7948
ms.sourcegitcommit: a2a7746c858eec0f7e93b50a1758a6278504977e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/12/2020
ms.locfileid: "88140686"
---
# <a name="time-series-model-variables"></a>시계열 모델 변수

이 문서에서는 이벤트에 대 한 수식 및 계산 규칙을 지정 하는 시계열 모델 변수에 대해 설명 합니다.

각 변수는 *숫자*, *범주*및 *집계*의 세 가지 종류 중 하나일 수 있습니다.

* **숫자** 종류는 연속 숫자 값으로 작동 합니다.
* **범주** 종류는 불연속 된 불연속 값 집합을 사용 합니다.
* **집계** 종류는 단일 종류의 여러 변수 (모든 숫자 또는 모든 범주)를 결합 합니다.

다음 표에서는 각 변수 종류와 관련 된 속성을 보여 줍니다.

[![시계열 모델 변수 테이블](media/v2-update-tsm/time-series-model-variable-table.png)](media/v2-update-tsm/time-series-model-variable-table.png#lightbox)

## <a name="numeric-variables"></a>숫자 변수

| 변수 속성 | Description |
| --- | ---|
| 변수 필터 | 필터는 계산에 고려 되는 행 수를 제한 하는 선택적 조건 절입니다. |
| 변수 값 | 계산에 사용 되는 원격 분석 값은 장치 또는 센서에서 발생 하거나 시계열 식을 사용 하 여 변환 됩니다. 숫자 종류 변수는 *Double*형식 이어야 합니다.|
| 변수 보간 | 보간은 기존 데이터를 사용 하 여 신호를 다시 생성 하는 방법을 지정 합니다. 숫자 변수에는 *단계* 및 *선형* 보간 옵션을 사용할 수 있습니다. |
| 변수 집계 | [숫자 변수 종류에 대해](https://docs.microsoft.com/rest/api/time-series-insights/reference-time-series-expression-syntax#numeric-variable-kind)지원 되는 집계 함수를 통해 계산을 수행 합니다. |

변수는 다음 JSON 예제를 따릅니다.

```JSON
"Interpolated Speed": {
  "kind": "numeric",
  "value": {
    "tsx": "$event['Speed-Sensor'].Double"
  },
  "filter": null,
  "interpolation": {
    "kind": "step",
    "boundary": {
      "span": "P1D"
    }
  },
  "aggregation": {
    "tsx": "right($value)"
  }
}
```

## <a name="categorical-variables"></a>범주 변수

| 변수 속성 | Description |
| --- | ---|
| 변수 필터 | 필터는 계산에 고려 되는 행 수를 제한 하는 선택적 조건 절입니다. |
| 변수 값 | 장치 또는 센서에서 들어오는 계산에 사용 되는 원격 분석 값입니다. 범주 종류 변수는 *Long* 또는 *String*이어야 합니다. |
| 변수 보간 | 보간은 기존 데이터를 사용 하 여 신호를 다시 생성 하는 방법을 지정 합니다. *단계* 보간 옵션은 범주 변수에 사용할 수 있습니다. |
| 변수 범주 | 범주는 장치 또는 센서에서 가져온 값 간의 매핑을 레이블으로 만듭니다. |
| 변수 기본 범주 | 기본 범주는 "범주" 속성에서 매핑되지 않는 모든 값에 대 한 것입니다. |

변수는 다음 JSON 예제를 따릅니다.

```JSON
"Status": {
  "kind": "categorical",
  "value": {
     "tsx": "$event.Status.Long"
},
  "interpolation": {
    "kind": "step",
    "boundary": {
      "span" : "PT1M"
    }
  },
  "categories": [
    {
      "values": [0, 1, 2, 3],
      "label": "Good"
    },
    {
      "values": [4],
      "label": "Bad"
    }
  ],
  "defaultCategory": {
    "label": "Not Applicable"
  }
}
```

## <a name="aggregate-variables"></a>집계 변수

| 변수 속성 | Description |
| --- | ---|
| 변수 필터 | 필터는 계산에 고려 되는 행 수를 제한 하는 선택적 조건 절입니다. |
| 변수 집계 | [집계 변수 종류에 대해](https://docs.microsoft.com/rest/api/time-series-insights/reference-time-series-expression-syntax#aggregate-variable-kind)지원 되는 집계 함수를 통해 계산을 수행 합니다. |

변수는 다음 JSON 예제를 따릅니다.

```JSON
"Speed Range": {
  "kind": "aggregate",
  "filter": null,
  "aggregation": {
    "tsx": "max($event.Speed.Double) - min($event.Speed.Double)"
  }
}
```

변수는 시계열 모델의 형식 정의에 저장 되며 Api를 통해 인라인으로 제공 되어 저장 된 정의를 재정의 하거나 보완할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [시계열 모델](./concepts-model-overview.md)에 대해 자세히 알아보세요.

* [쿼리 api](./concepts-query-overview.md)를 사용 하 여 인라인으로 변수를 정의 하는 방법에 대해 자세히 알아보세요.
