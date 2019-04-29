---
title: Azure Stream Analytics 창 함수 소개
description: 이 문서에서는 Azure Stream Analytics 작업에 사용되는 네 개의 창 함수(연속, 도약, 슬라이딩, 세션)를 설명합니다.
services: stream-analytics
author: lingliw
ms.author: v-lingwu
manager: digimobile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 01/21/19
ms.openlocfilehash: 04c19e7e51777db4c59bfab3d5a8a7598560556a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60844771"
---
# <a name="introduction-to-stream-analytics-windowing-functions"></a>Stream Analytics 창 함수 소개
실시간 스트리밍 시나리오에서 임시 창에 포함된 데이터에서 작업을 수행하는 것은 일반적인 패턴입니다. Stream Analytics에는 개발자가 최소한의 노력으로 복잡한 스트림 처리 작업을 작성할 수 있도록 창 함수에 대한 네이티브 지원이 있습니다.

선택 가능한 임시 창의 종류에는 [**연속**](https://msdn.microsoft.com/azure/stream-analytics/reference/tumbling-window-azure-stream-analytics), [**도약**](https://msdn.microsoft.com/azure/stream-analytics/reference/hopping-window-azure-stream-analytics), [**슬라이딩**](https://msdn.microsoft.com/azure/stream-analytics/reference/sliding-window-azure-stream-analytics), [**세션**](https://msdn.microsoft.com/azure/stream-analytics/reference/session-window-azure-stream-analytics)의 네 가지가 있습니다.  Stream Analytics 작업에서 쿼리 구문의 [**GROUP BY**](https://msdn.microsoft.com/azure/stream-analytics/reference/group-by-azure-stream-analytics) 절에서 창 함수를 사용합니다.

모든 [창](https://msdn.microsoft.com/azure/stream-analytics/reference/windowing-azure-stream-analytics) 작업 결과는 창의 **끝**에 출력됩니다. 창의 출력은 사용된 집계 함수를 기반으로 하는 단일 이벤트입니다. 출력 이벤트에는 창 끝의 타임스탬프가 있고 모든 창 함수는 고정된 길이로 정의됩니다. 

![Stream Analytics 창 함수 개념](media/stream-analytics-window-functions/stream-analytics-window-functions-conceptual.png)

## <a name="tumbling-window"></a>연속 창
연속 창 함수는 다른 시간 세그먼트에 데이터 스트림을 분할하는 데 사용되고 아래 예와 같이 그에 대한 기능을 수행합니다. 연속 창의 핵심적인 차이는 반복되지만 겹치지 않고 이벤트가 둘 이상의 연속 창에 속할 수 없다는 점입니다.

![Stream Analytics 연속 창](media/stream-analytics-window-functions/stream-analytics-window-functions-tumbling-intro.png)

## <a name="hopping-window"></a>도약 창
도약 창 함수는 고정된 기간만큼 시간을 앞으로 건너 뜁니다. 겹칠 수 있는 연속 창으로 생각하기 쉬우므로 이벤트는 둘 이상의 도약 창 결과 집합에 속할 수 있습니다. 연속 창과 동일한 도약 창을 만들려면 도약 크기를 창 크기와 동일하도록 지정합니다. 

![Stream Analytics 도약 창](media/stream-analytics-window-functions/stream-analytics-window-functions-hopping-intro.png)

## <a name="sliding-window"></a>슬라이딩 윈도우
연속 또는 도약 창과 달리 슬라이딩 윈도우 함수는 이벤트가 발생할 때 출력 **만** 생성합니다. 모든 창에는 하나 이상의 이벤트가 있고 창은 지속적으로 €(엡실론)만큼 앞으로 이동합니다. 도약 창과 마찬가지로 이벤트는 둘 이상의 슬라이딩 윈도우에 속할 수 있습니다.

![Stream Analytics 슬라이딩 윈도우](media/stream-analytics-window-functions/stream-analytics-window-functions-sliding-intro.png)

## <a name="session-window"></a>세션 창
세션 창 함수는 데이터가 없는 기간을 필터링하여 비슷한 시간에 도착하는 이벤트를 그룹화합니다. 세 가지 주요 매개 변수인 시간 제한, 최대 지속 시간 및 분할 키(선택 사항)가 있습니다.

![Stream Analytics 세션 창](media/stream-analytics-window-functions/stream-analytics-window-functions-session-intro.png)

세션 창은 첫 번째 이벤트가 발생할 때 시작합니다. 마지막으로 수집된 이벤트에서 지정된 시간 제한 내에 다른 이벤트가 발생하는 경우 창은 새 이벤트를 포함하도록 확장됩니다. 그렇지 않으면 시간 제한 내에 이벤트가 발생하지 않는 경우 창은 시간 제한에 닫힙니다.

지정된 시간 제한 내에서 이벤트가 계속 발생하는 경우 세션 창은 최대 지속 시간에 도달할 때까지 확장을 유지합니다. 최대 지속 시간 검사 간격은 지정된 최대 지속 시간과 동일한 크기로 설정됩니다. 예를 들어 최대 지속 시간이 10인 경우 창이 최대 지속 시간을 초과하는지에 대한 검사는 t = 0, 10, 20, 30 등에 발생합니다.

파티션 키가 제공되는 경우 이벤트는 키와 함께 그룹화되고 세션 창은 각 그룹에 독립적으로 적용됩니다. 이 분할은 다른 사용자 또는 디바이스에 다른 세션 창이 필요한 경우에 유용합니다.

## <a name="next-steps"></a>다음 단계
* [Azure Stream Analytics 소개](stream-analytics-introduction.md)
* [Azure Stream Analytics 사용 시작](stream-analytics-real-time-fraud-detection.md)
* [Azure  Stream Analytics 작업 규모 지정](stream-analytics-scale-jobs.md)
* [Azure  Stream Analytics 쿼리 언어 참조](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure Stream Analytics 관리 REST API 참조](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!--Update_Description: update meta properties -->