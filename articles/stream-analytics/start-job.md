---
title: Azure Stream Analytics 작업을 시작 하는 방법
description: 이 문서에서는 Stream Analytics 작업을 시작 하는 방법을 설명 합니다.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/03/2019
ms.openlocfilehash: 9bc3e4132919e5fc5baadc78841e66efd3c34bcd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61362268"
---
# <a name="how-to-start-an-azure-stream-analytics-job"></a>Azure Stream Analytics 작업을 시작 하는 방법

Azure portal, Visual Studio 및 PowerShell을 사용 하 여 Azure Stream Analytics 작업을 시작할 수 있습니다. 작업을 시작 하는 경우 작업 출력을 만들기 시작 시간을 선택 합니다. Azure portal, Visual Studio 및 각 PowerShell에는 시작 시간을 설정 하는 것에 대 한 가지 방법이 있습니다. 이러한 메서드는 다음과 같습니다.

## <a name="start-options"></a>시작 옵션
세 가지 다음 옵션은 작업을 시작 하려면 사용할 수 있습니다. 아래에 언급 된 모든 시간에 지정 된 것은 [TIMESTAMP BY](https://docs.microsoft.com/stream-analytics-query/timestamp-by-azure-stream-analytics)합니다. TIMESTAMP BY를 지정 하지 않으면 도착 시간 사용 됩니다.
* **이제**: 작업이 시작 될 때 같은 스트림 출력 이벤트의 시작 지점을 만듭니다. Temporal 연산자를 사용 하는 경우 (예: 시간 창, 지연 또는 조인), Azure Stream Analytics 입력된 소스에서 데이터 다시 확인 자동으로 됩니다. 예를 들어 "Now" 작업을 시작 하는 경우 및 쿼리는 5 분 연속 창을 사용 하는 경우 Azure Stream Analytics는 5 분 이내 입력에서에서 데이터를 검색 합니다.
첫 번째 출력 이벤트는 현재 시간 보다 크거나 같은 타임 스탬프 있고 ASA 있습니다 논리적으로 출력에 영향을 주는 모든 입력된 이벤트에 대 한 고려 되어 있는지을 보장 합니다. 예를 들어, 부분 기간 이동 집계가 생성 됩니다. 항상 전체는 집계 된 값입니다.

* **사용자 지정**: 출력의 시작점을 선택할 수 있습니다. 유사 하 게 합니다 **이제** 옵션, Azure Stream Analytics를 임시 연산자를 사용 하는 경우에 자동으로이 시간 전에 데이터를 읽고 

* **마지막으로 중지 된 경우**합니다. 이 옵션은 작업 이전에 시작 되지만 수동으로 중지 되었거나 실패 한 경우에 사용할 수 있습니다. 이 옵션을 선택 하면 Azure Stream Analytics 작업을 다시 데이터 손실은 전혀 없습니다 있도록 마지막 출력 시간을 사용 합니다. 마찬가지로 이전 옵션에 Azure Stream Analytics는 자동으로 데이터를 읽을이 시간 전에 temporal 연산자를 사용 하는 경우. 여러 입력된 파티션 다른 시간 수 없으므로 일부 중복 출력에서 볼 수 있는 결과적으로 모든 파티션에 대 한 가장 오래 된 중지에 사용 됩니다. 에 대 한 자세한 내용은 정확 하 게-페이지에서 사용할 수 있는 한 번 처리 [이벤트 전달 보장이](https://docs.microsoft.com/stream-analytics-query/event-delivery-guarantees-azure-stream-analytics)합니다.


## <a name="azure-portal"></a>Azure portal

Azure 포털 및 select 작업을 이동할 **시작** 개요 페이지에 있습니다. 선택 된 **작업 출력 시작 시간** 선택한 후 **시작**합니다.

에 대 한 옵션 중 하나를 선택 **작업 출력 시작 시간**합니다. 옵션은 *이제*를 *사용자 지정*에서 작업 이전에 실행 하는 경우 *마지막으로 중지 된 경우*합니다. 이러한 옵션에 대 한 자세한 내용은 위의 참조 하세요.

## <a name="visual-studio"></a>Visual Studio

작업 보기에서 작업을 시작 하려면 녹색 화살표 단추를 선택 합니다. 설정 된 **작업 출력 시작 모드** 선택한 **시작**합니다. 작업 상태가으로 변경 됩니다 **실행**합니다.

에 대 한 세 가지 **작업 출력 시작 모드**: *JobStartTime*하십시오 *CustomTime*, 및 *LastOutputEventTime*합니다. 기본값은이 속성이 없으면 *JobStartTime*합니다. 이러한 옵션에 대 한 자세한 내용은 위의 참조 하세요.


## <a name="powershell"></a>PowerShell

PowerShell을 사용 하 여 작업을 시작 하려면 다음 cmdlet을 사용 합니다.

```powershell
Start-AzStreamAnalyticsJob `
  -ResourceGroupName $resourceGroup `
  -Name $jobName `
  -OutputStartMode 'JobStartTime'
```

에 대 한 세 가지 **OutputStartMode**: *JobStartTime*하십시오 *CustomTime*, 및 *LastOutputEventTime*합니다. 기본값은이 속성이 없으면 *JobStartTime*합니다. 이러한 옵션에 대 한 자세한 내용은 위의 참조 하세요.

에 대 한 자세한 합니다 `Start-AzStreamAnalyitcsJob` cmdlet을 보기는 [시작 AzStreamAnalyticsJob 참조](/powershell/module/az.streamanalytics/start-azstreamanalyticsjob)합니다.

## <a name="next-steps"></a>다음 단계

* [빠른 시작: Azure Portal을 사용하여 Stream Analytics 작업 만들기](stream-analytics-quick-create-portal.md)
* [빠른 시작: Azure PowerShell을 사용 하 여 Stream Analytics 작업 만들기](stream-analytics-quick-create-powershell.md)
* [빠른 시작: Azure Stream Analytics Tools for Visual Studio를 사용하여 Stream Analytics 작업 만들기](stream-analytics-quick-create-vs.md)
