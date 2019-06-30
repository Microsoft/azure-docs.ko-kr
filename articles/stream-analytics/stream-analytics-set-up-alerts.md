---
title: Azure Stream Analytics 작업에 대한 모니터링 경고 설정
description: 이 아티클에서는 Azure Portal을 사용하여 Azure Stream Analytics 작업에 대한 모니터링 및 경고를 설정하는 방법을 설명합니다.
services: stream-analytics
author: jseb225
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.openlocfilehash: 26e8e004f917b1c138bc27389cac1cc52672f3d4
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/22/2019
ms.locfileid: "67329870"
---
# <a name="set-up-alerts-for-azure-stream-analytics-jobs"></a>Azure Stream Analytics 작업에 대한 경고 설정

Azure Stream Analytics 작업을 모니터링하여 작업이 문제 없이 계속 실행되도록 하는 것이 중요합니다. 이 문서에서는 모니터링해야 하는 일반적인 시나리오에 대한 경고를 설정하는 방법에 대해 설명합니다. 

포털을 통해 작업 로그 데이터에서 메트릭에 대 한 규칙을 정의할 수 있습니다 뿐만 [프로그래밍 방식으로](https://code.msdn.microsoft.com/windowsazure/Receive-Email-Notifications-199e2c9a)입니다.

## <a name="set-up-alerts-in-the-azure-portal"></a>Azure Portal에서 경고 설정
### <a name="get-alerted-when-a-job-stops-unexpectedly"></a>작업이 예기치 않게 중지 될 때 알림을 받을합니다

다음 예제에서는 작업이 실패한 상태가 될 때 경고를 설정하는 방법을 보여 줍니다. 이 경고는 모든 작업에 권장됩니다.

1. Azure Portal에서 경고를 만들려는 Stream Analytics 작업을 엽니다.

2. **작업** 페이지에서 **모니터링** 섹션으로 이동합니다.  

3. 선택 **메트릭을**를 차례로 **새 경고 규칙**합니다.

   ![Azure Portal Stream Analytics 경고 설정](./media/stream-analytics-set-up-alerts/stream-analytics-set-up-alerts.png)  

4. Stream Analytics 작업 이름이 **리소스** 아래에 자동으로 나타납니다. **조건 추가**를 클릭하고 **신호 논리 구성**에서 **모든 관리 작업**을 선택합니다.

   ![Stream Analytics 경고에 대한 신호 이름 선택](./media/stream-analytics-set-up-alerts/stream-analytics-condition-signal.png)  

5. **신호 논리 구성**에서 **이벤트 수준**을 **모두**로 변경하고 **상태**를 **실패**로 변경합니다. 둡니다 **이벤트를 시작한 사람** 빈 선택한 **수행**합니다.

   ![Stream Analytics 경고에 대한 신호 논리 구성](./media/stream-analytics-set-up-alerts/stream-analytics-configure-signal-logic.png) 

6. 기존 작업 그룹을 선택하거나 새 그룹을 만듭니다. 이 예제에서는 **소유자** Azure Resource Manager 역할을 가진 사용자에게 이메일을 보내는 **이메일** 작업으로 **TIDashboardGroupActions**라는 새로운 작업 그룹이 생성되었습니다.

   ![Azure Streaming Analytics 작업에 대한 경고 설정](./media/stream-analytics-set-up-alerts/stream-analytics-add-group-email-action.png)

7. **리소스**, **조건** 및 **작업 그룹**에 각 항목이 있어야 합니다. 정의된 조건이 경고 발생 순서대로 충족되어야 합니다. 예를 들어 5분마다 지난 15분의 메트릭 평균 값을 측정할 수 있습니다.

   ![Stream Analytics 경고 규칙 만들기](./media/stream-analytics-set-up-alerts/stream-analytics-create-alert-rule-2.png)

   **경고 규칙 이름**, **설명** 및 **리소스 그룹**을 **경고 세부 정보**에 추가하고 **경고 규칙 만들기**를 클릭하여 Stream Analytics 작업에 대한 규칙을 만듭니다.

   ![Stream Analytics 경고 규칙 만들기](./media/stream-analytics-set-up-alerts/stream-analytics-create-alert-rule.png)
   
## <a name="scenarios-to-monitor"></a>모니터링할 시나리오

Stream Analytics 작업의 성능 모니터링에 대해 다음 경고를 사용하는 것이 좋습니다. 이러한 메트릭은 마지막 5분 동안 1분마다 평가되어야 합니다.

|메트릭|조건|시간 집계|임계값|정정 작업|
|-|-|-|-|-|
|SU% 사용률|다음보다 큼|최대|80|SU% 사용률이 증가하는 여러 요인이 있습니다. 쿼리 병렬화로 크기 조정하거나 스트리밍 단위 수를 늘릴 수 있습니다. 자세한 내용은 [Azure Stream Analytics에서 쿼리 병렬 처리 활용](stream-analytics-parallelization.md)을 참조하세요.|
|런타임 오류|다음보다 큼|합계|0|작업 또는 진단 로그를 검토하고 입력, 쿼리 또는 출력을 적절히 변경합니다.|
|워터마크 지연|다음보다 큼|최대|마지막 15분 동안 이 메트릭의 평균 값이 지연 도착 허용 시간(초)보다 큰 경우. 지연 도착 허용 시간을 수정하지 않은 경우, 기본값은 5초로 설정됩니다.|SU 수를 늘리거나 쿼리를 병렬 처리하세요. SU에 대한 자세한 내용은 [스트리밍 단위 이해 및 조정](stream-analytics-streaming-unit-consumption.md#how-many-sus-are-required-for-a-job)을 참조하세요. 쿼리 병렬 처리에 대한 자세한 내용은 [Azure Stream Analytics에서 쿼리 병렬 처리 활용](stream-analytics-parallelization.md)을 참조하세요.|
|입력 역직렬화 오류|다음보다 큼|합계|0|작업 또는 진단 로그를 검토하고 입력을 적절히 변경합니다. 진단 로그에 대한 자세한 내용은 [진단 로그를 사용하여 Azure Stream Analytics 문제 해결](stream-analytics-job-diagnostic-logs.md)을 참조하세요.|

## <a name="get-help"></a>도움말 보기

Azure Portal에서 경고를 구성에 대한 자세한 내용은 [경고 알림 받기](../monitoring-and-diagnostics/insights-receive-alert-notifications.md)를 참조하세요.  

추가 지원이 필요한 경우 [Azure Stream Analytics 포럼](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)을 참조하세요.

## <a name="next-steps"></a>다음 단계
* [Azure Stream Analytics 소개](stream-analytics-introduction.md)
* [Azure Stream Analytics 사용 시작](stream-analytics-get-started.md)
* [Azure  Stream Analytics 작업 규모 지정](stream-analytics-scale-jobs.md)
* [Azure  Stream Analytics 쿼리 언어 참조](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure Stream Analytics 관리 REST API 참조](https://msdn.microsoft.com/library/azure/dn835031.aspx)

