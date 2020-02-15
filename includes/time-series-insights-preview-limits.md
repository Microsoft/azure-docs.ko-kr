---
title: 포함 파일
description: 포함 파일
services: digital-twins
ms.service: digital-twins
ms.topic: include
ms.date: 02/07/2020
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.custom: include file
ms.openlocfilehash: 7bc6938523a6d66a2bc20b37c659568fc5ca494d
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/11/2020
ms.locfileid: "77123166"
---
### <a name="general-availability-and-preview-comparison"></a>일반 가용성 및 미리 보기 비교

다음 표에는 GA (일반 공급) 및 미리 보기 인스턴스 Azure Time Series Insights 간의 몇 가지 주요 차이점이 요약 되어 있습니다.

| | GA | 미리 보기 |
| --- | --- | ---|
| 첫 번째 클래스 시민 | 이벤트 중심 | 시계열 중심 |
| 의미 체계 추론 | 하위 수준 (참조 데이터) | 상위 수준 (모델) |
| 데이터 contextualization | 비 장치 수준 | 장치 및 비 장치 수준 |
| Compute 논리 저장소 | 예 | 모델의 형식 변수에 저장 됩니다. |
| 저장소 및 액세스 제어 | 예 | 모델을 통해 사용 |
| 집계/샘플링 | 예 | 이벤트 가중치 및 시간 가중치 |
| 신호 재구성 | 예 | 삽입할 |
| 파생 된 시계열의 프로덕션 | 예 | 예, 병합 및 조인 |
| 언어 유연성 | 구성할 없는 | 가능 |
| 식 언어 | 조건자 문자열 | 시계열 식 (조건자 문자열, 값, 식 및 함수) |

### <a name="property-limits"></a>속성 제한

Time Series Insights 속성 제한은 GA에서 800의 최대 캡에서 1000로 증가 했습니다. 제공 된 이벤트 속성에는 해당 JSON, CSV 및 [Time Series Insights Preview 탐색기](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-quickstart)내에서 볼 수 있는 차트 열이 있습니다.

| SKU | 최대 속성 |
| --- | --- |
| PAYG 미리 보기 | 1000 속성 (열) |
| GA S1 | 600 속성 (열) |
| GA S2 | 800 속성 (열) |

### <a name="event-sources"></a>이벤트 원본

인스턴스당 최대 2 개의 이벤트 원본이 지원 됩니다. 

* [이벤트 허브 원본을 추가](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-eventhub)하는 방법에 대해 알아봅니다.
* [IoT hub 원본을](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-iothub)구성 합니다.

기본적으로 [미리 보기 환경](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-storage-ingress#ingress-scale-and-preview-limitations) 에서는 **환경 당 초당**최대 1mb의 수신 속도를 지원 합니다. 고객은 필요한 경우 최대 **16mb/초** 처리량까지 미리 보기 환경을 확장할 수 있습니다. 또한 **0.5 m b/s**의 파티션당 제한이 있습니다. 

### <a name="api-limits"></a>API 제한

Time Series Insights Preview에 대 한 REST API 제한은 [REST API 참조 설명서](https://docs.microsoft.com/rest/api/time-series-insights/preview-query#limits)에 지정 되어 있습니다.
