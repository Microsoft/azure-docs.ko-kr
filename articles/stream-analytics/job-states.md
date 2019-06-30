---
title: Azure Stream Analytics 작업 상태
description: 이 문서에서는 Stream Analytics 작업의 다양한 상태를 설명합니다.
services: stream-analytics
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.openlocfilehash: bef21dc35bbd2b9b50cf7b362624321866773bfe
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/22/2019
ms.locfileid: "67331347"
---
# <a name="azure-stream-analytics-job-states"></a>Azure Stream Analytics 작업 상태

Stream Analytics 작업은 항상 네 가지 상태 중 하나일 수 있습니다. Azure Portal의 Stream Analytics 작업 개요 페이지에서 작업 상태를 확인할 수 있습니다. 

| 시스템 상태 | 설명 | 권장 작업 |
| --- | --- | --- |
| **실행 중** | 작업이 정의된 입력 원본에서 제공되는 Azure 읽기 이벤트에 대해 실행되고 있으며, 이벤트를 처리하고 결과를 구성된 출력 싱크에 씁니다. | [주요 메트릭](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-set-up-alerts#scenarios-to-monitor)을 모니터링하여 작업 성능을 추적하는 것이 좋습니다. |
| **중지** | 작업이 중지되었으며 이벤트를 처리하지 않습니다. | 해당 없음 | 
| **성능 저하됨** | 입력 및 출력 연결에 간헐적 문제가 있을 수 있습니다. 이러한 오류 Degraded 상태가 작업을 수행할 수 있는 일시적인 오류 라고 합니다. Stream Analytics는 이러한 오류를 즉시 복구하고 실행 중 상태로 되돌리려고 합니다(몇 분 이내). 이러한 오류는 네트워크 문제, 다른 Azure 리소스의 가용성, 역직렬화 오류 등으로 인해 발생할 수 있습니다. 작업이 성능 저하됨 상태인 경우 작업 성능이 영향을 받을 수 있습니다.| [진단 또는 활동 로그](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-job-diagnostic-logs#debugging-using-activity-logs)를 살펴보고 이러한 일시적 오류의 원인을 자세히 알아볼 수 있습니다. 역직렬화 오류와 같은 경우 잘못된 형식의 이벤트가 없도록 정정 작업을 수행하는 것이 좋습니다. 작업이 리소스 사용률 한도에 계속 도달하는 경우 SU 수를 늘리거나 [작업을 병렬 처리](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization)합니다. 아무 작업도 수행할 수 없는 경우 Stream Analytics에서 ‘실행 중’ 상태로 복구를 시도합니다.  <br> 사용할 수 있습니다 [지연 워터 마크](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-set-up-alerts#scenarios-to-monitor) 경우 이러한 일시적인 오류에 영향을 작업의 성능을 이해 하는 메트릭.|
| **실패** | 작업 중에 심각한 오류가 발생하여 실패 상태가 되었습니다. 이벤트를 읽고 처리하지 않습니다. 런타임 오류는 작업이 실패 상태가 되는 일반적인 원인입니다. | 작업이 실패 상태로 전환될 때 알림을 받도록 [경고를 구성](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-set-up-alerts#set-up-alerts-in-the-azure-portal)할 수 있습니다. <br> <br>[활동 및 진단 로그](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-job-diagnostic-logs#debugging-using-activity-logs)로 디버그하여 근본 원인을 식별하고 문제를 해결할 수 있습니다.|

## <a name="next-steps"></a>다음 단계
* [Azure Stream Analytics 작업에 대한 경고 설정](stream-analytics-set-up-alerts.md)
* [Stream Analytics에서 사용 가능한 메트릭](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-monitoring#metrics-available-for-stream-analytics)
* [활동 및 진단 로그를 사용하여 문제 해결](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-job-diagnostic-logs)
