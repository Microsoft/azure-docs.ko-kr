---
title: Azure Stream Analytics에 대 한 정책 순서를 지정 하는 이벤트를 구성 합니다.
description: 이 문서에서는 Stream Analytics 순서로 설정도 구성 하는 방법 설명
services: stream-analytics
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/12/2019
ms.openlocfilehash: 970eeb871775e24abb87c8b977e214645e514d3b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60789487"
---
# <a name="configuring-event-ordering-policies-for-azure-stream-analytics"></a>Azure Stream Analytics에 대 한 정책 순서를 지정 하는 이벤트를 구성 합니다.

이 문서에서는 설정 및 Azure Stream Analytics에서 지연 도착 및 순서가 이벤트 정책을 사용 하는 방법을 설명 합니다. 이러한 정책을 사용 하는 경우에 적용 되는 [TIMESTAMP BY](https://docs.microsoft.com/stream-analytics-query/timestamp-by-azure-stream-analytics) 쿼리 절.

## <a name="event-time-and-arrival-time"></a>이벤트 시간과 도착 시간

Stream Analytics 작업 중 하나를 기반으로 이벤트를 처리할 수 있습니다 *이벤트 시간* 하거나 *도착 시간*합니다. **이벤트/응용 프로그램 시간** (이벤트가 생성 된) 경우에 이벤트 페이로드의 타임 스탬프입니다. **도착 시간** 입력된 원본 (Event Hubs/IoT Hub/Blob storage)에서 이벤트를 받은 경우 타임 스탬프입니다. 

기본적으로 Stream Analytics에서 이벤트를 처리 *도착 시간*, 이벤트를 처리 하도록 선택할 수 있지만 *이벤트 시간* 사용 하 여 [TIMESTAMP BY](https://docs.microsoft.com/stream-analytics-query/timestamp-by-azure-stream-analytics) 쿼리 절. 지연 도착 및 순서가 정책 이벤트 시간을 기준으로 이벤트를 처리 하는 경우에 적용할 수 있습니다. 이러한 설정을 구성할 때 시나리오에 대 한 대기 시간 및 정확성 요구를 고려해 야 합니다. 

## <a name="what-is-late-arrival-policy"></a>지연 도착 정책 이란?

경우에 따라 다양 한 이유로 인해 이벤트 늦게 도착 합니다. 예를 들어, 40 초 늦게 도착 하는 이벤트를 이벤트 시간을 가집니다 00시 10분: 00 및 도착 시간 = = 40:10:00입니다. 지연 도착 정책 15 초를 15 초 중에 삭제 됩니다 (Stream Analytics에서 처리 되지 않습니다) 또는 이벤트 시간 조정 해야 하는 보다 늦게 도착 하는 모든 이벤트를 설정한 경우. 위의 예제에서는 40 초 늦은 (보다 정책 집합)에 도착 한 이벤트 처럼 해당 이벤트 시간 조정 됩니다 최대값의 지연 도착 정책 00시 10분: 25 (도착 시간-지연 도착 정책 값). 지연 도착 정책 기본값은 5 초입니다.

## <a name="what-is-out-of-order-policy"></a>순서가 정책은 무엇 인가요? 

이벤트를도 순서로 도착할 수 있습니다. 이벤트 시간 지연 도착 정책에 따라 조정 후 자동으로 삭제 또는 조정 이벤트의 순서를 선택할 수 있습니다. 이 정책을 8 시간 (초)로 설정한 경우 이벤트 시간을 기준으로 순서가 하지만 8 초 이내에 도착 하는 모든 이벤트 자식은 합니다. 나중에 도착 하는 이벤트는 삭제 되거나 최대 순서가의 정책 값으로 조정 됩니다. 기본 순서가 정책은 0 초입니다. 

## <a name="adjust-or-drop-events"></a>이벤트 삭제 또는 조정

이벤트 도착 지연 또는 순서가 구성한 정책을 기반으로 하는 경우 (Stream Analytics에서 처리 되지 않습니다)는 이러한 이벤트를 삭제 하거나 해당 이벤트 시간이 조정 합니다.

작업에서 이러한 정책의 예를 확인할 알려 주세요.
<br> **지연 도착 정책:** 15초
<br> **순서가 정책:** 8 시간 (초)

| 이벤트 번호 | 이벤트 시간 | 도착 시간 | System.Timestamp | 설명 |
| --- | --- | --- | --- | --- |
| **1** | 00:10:00  | 00:10:40  | 00:10:25  | 이벤트 도착 지연 및 외부 허용 오차 수준이 있습니다. 따라서 이벤트 시간은 최대 지연 도착 허용 시간으로 조정 가져옵니다.  |
| **2** | 00:10:30 | 00:10:41  | 00:10:30  | 런타임에 있지만 허용 오차 수준이 내 도착 한 이벤트입니다. 따라서 이벤트 시간을 조정할 가져오기지 않습니다.  |
| **3** | 00:10:42 | 00:10:42 | 00:10:42 | 이벤트 시간에 도착 합니다. 필요 없는 조정 합니다.  |
| **4** | 00:10:38  | 00:10:43  | 00:10:38 | 이벤트의 순서가 하지만 8 초의 허용 범위 내에 도착합니다. 따라서 이벤트 시간은 조정 가져오기 되지 않습니다. 분석을 위해이 이벤트 앞에 이벤트 번호 4로 간주 됩니다.  |
| **5** | 00:10:35 | 00:10:45  | 00:10:37 | 이벤트 도착 순서가 및 외부 허용 오차 8 시간 (초)입니다. 따라서 이벤트 시간 순서가의 허용 오차의 최대로 조정 됩니다. |

## <a name="can-these-settings-delay-output-of-my-job"></a>이러한 설정은 내 작업의 출력이 지연 될 수 있습니다? 

예. 기본적으로 순서가 정책 0 (00 분 00 초)에 설정 됩니다. 기본값을 변경 하는 경우이 값으로 지연 된 (또는 그 이상) 작업의 첫 번째 출력이 됩니다. 

이벤트를 받지 못하면 입력의 파티션 중 하나에 지연 도착 정책 값을 기준으로 지연 될 출력을 하시면 됩니다. 이유를 알아보려면 아래 InputPartition 오류 섹션을 참조 합니다. 

## <a name="i-see-lateinputevents-messages-in-my-activity-log"></a>활동 로그 내에서 LateInputEvents 메시지 표시

이러한 메시지는 이벤트는 늦게 도착 하 고는 삭제 또는 조정 구성에 따라 사용자에 게 알리는 표시 됩니다. 지연 도착 정책을 적절히 구성한 경우 이러한 메시지를 무시할 수 있습니다. 

이 메시지의 예제가입니다. <br>
<code>
{"message Time":"2019-02-04 17:11:52Z","error":null,
"message":"First Occurred: 02/04/2019 17:11:48 | Resource Name: ASAjob | Message: Source 'ASAjob' had 24 data errors of kind 'LateInputEvent' between processing times '2019-02-04T17:10:49.7250696Z' and '2019-02-04T17:11:48.7563961Z'. Input event with application timestamp '2019-02-04T17:05:51.6050000' and arrival time '2019-02-04T17:10:44.3090000' was sent later than configured tolerance.","type":"DiagnosticMessage","correlation ID":"49efa148-4asd-4fe0-869d-a40ba4d7ef3b"} 
</code>

## <a name="i-see-inputpartitionnotprogressing-in-my-activity-log"></a>활동 로그 내에서 InputPartitionNotProgressing 표시

가능성이 입력된 원본 (이벤트 허브/IoT Hub)에 여러 개의 파티션이 있습니다. Azure Stream Analytics는 결합 되는 모든 파티션에 적어도 시간 t1 후에 타임 스탬프 t1에 대 한 출력을 생성 합니다. 예를 들어 쿼리가 두 개의 파티션이 있는 이벤트 허브 파티션에서 읽는다고 가정합니다. P1 파티션 중 하나에 t1 시간까지 이벤트가 있습니다. 다른 파티션 P2에 시간 t1 + x까지 이벤트가 있습니다. T1 시간까지 출력이 생성 됩니다. 하지만 PartitionId 절로 명시적 파티션이 있으면 두 파티션은 독립적으로 진행 합니다. 

같은 입력된 스트림이나 여러 파티션이 결합 되 면 모든 파티션이 새 데이터를 기다리는 시간을 최대 지연 도착 허용 시간에는 합니다. Event Hub가 하나의 파티션 또는 해당 파티션에 대 한 타임 라인까지 진행 되지 않습니다 IoT 허브 입력을 받지 못하면, 경우에 지연 도착 허용 시간 임계값에 도달 합니다. 이 지연 도착 허용 시간 임계값으로 출력을 지연합니다. 이러한 경우 다음과 같은 메시지가 표시 될 수 있습니다.
<br><code>
{"message Time":"2/3/2019 8:54:16 PM UTC","message":"Input Partition [2] does not have additional data for more than [5] minute(s). Partition will not progress until either events arrive or late arrival threshold is met.","type":"InputPartitionNotProgressing","correlation ID":"2328d411-52c7-4100-ba01-1e860c757fc2"} 
</code><br><br>
이 메시지는 하나 이상의 파티션을 입력에서을 알리기 위해 비어 있고 출력 지연 도착 임계값으로 지연 됩니다. 이 문제를 해결할 것을 권장 하 중 하나: 1. 입력을 수신 하는 이벤트 허브/IoT Hub의 모든 파티션을 확인 합니다. 2. PartitionID 쿼리 절에서 파티션을 사용 합니다. 

## <a name="next-steps"></a>다음 단계
* [시간 처리 고려 사항](stream-analytics-time-handling.md)
* [Stream Analytics에서 사용 가능한 메트릭](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-monitoring#metrics-available-for-stream-analytics)
