---
title: Azure Stream Analytics 작업을 시작 하는 방법
description: 이 문서에서는 Azure Portal, PowerShell 및 Visual Studio에서 Stream Analytics 작업을 시작 하는 방법을 설명 합니다.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 04/03/2019
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: bce1f00603d9304fc544fb92d4cc5f1a429504ed
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89069003"
---
# <a name="how-to-start-an-azure-stream-analytics-job"></a>Azure Stream Analytics 작업을 시작 하는 방법

Azure Portal, Visual Studio 및 PowerShell을 사용 하 여 Azure Stream Analytics 작업을 시작할 수 있습니다. 작업을 시작할 때 작업에서 출력을 만들기 시작 하는 시간을 선택 합니다. Azure Portal, Visual Studio 및 PowerShell에는 각각 시작 시간을 설정 하는 방법이 서로 다릅니다. 이러한 메서드는 아래에 설명 되어 있습니다.

## <a name="start-options"></a>시작 옵션
작업을 시작 하는 데 사용할 수 있는 세 가지 옵션은 다음과 같습니다. 아래에 언급 된 모든 시간은 [TIMESTAMP BY](https://docs.microsoft.com/stream-analytics-query/timestamp-by-azure-stream-analytics)에 지정 된 것입니다. TIMESTAMP BY를 지정 하지 않으면 도착 시간이 사용 됩니다.
* **Now**: 출력 이벤트 스트림의 시작 지점을 작업이 시작 될 때와 동일 하 게 만듭니다. Temporal 연산자 (예: 시간 창, 지연 또는 조인)를 사용 하는 경우 Azure Stream Analytics는 입력 원본의 데이터를 자동으로 다시 검색 합니다. 예를 들어, "Now" 작업을 시작 하 고 쿼리가 5 분 연속 창을 사용 하는 경우 Azure Stream Analytics는 입력에서 5 분 전에 데이터를 검색 합니다.
첫 번째 가능한 출력 이벤트의 타임 스탬프는 현재 시간 보다 같거나 클 수 있으며,이 경우에는 논리적으로 출력에 기여할 수 있는 모든 입력 이벤트가 고려 됩니다. 예를 들어 부분 기간 이동 집계가 생성 되지 않습니다. 항상 완전 한 집계 값입니다.

* **사용자 지정**: 출력의 시작 지점을 선택할 수 있습니다. **Now** 옵션과 마찬가지로 임시 연산자를 사용 하는 경우이 시간 전에 Azure Stream Analytics에서 데이터를 자동으로 읽습니다. 

* **마지막으로 중지 된 시간**입니다. 이 옵션은 작업이 이전에 시작 되었지만 수동으로 중지 되었거나 실패 한 경우에만 사용할 수 있습니다. 이 옵션을 선택 하면 Azure Stream Analytics는 마지막 출력 시간을 사용 하 여 작업을 다시 시작 하 고 데이터가 손실 되지 않습니다. 이전 옵션과 마찬가지로 임시 연산자를 사용 하는 경우 Azure Stream Analytics은이 시간 전에 데이터를 자동으로 읽습니다. 여러 입력 파티션에서 시간이 달라질 수 있으므로 모든 파티션의 가장 빠른 중지 시간이 사용 되므로 출력에 일부 중복이 표시 될 수 있습니다. 정확히 한 번의 처리에 대 한 자세한 내용은 페이지 [이벤트 전달 보장](https://docs.microsoft.com/stream-analytics-query/event-delivery-guarantees-azure-stream-analytics)에서 제공 됩니다.


## <a name="azure-portal"></a>Azure portal

Azure Portal에서 작업으로 이동 하 고 개요 페이지에서 **시작** 을 선택 합니다. **작업 출력 시작 시간** 을 선택 하 고 **시작**을 선택 합니다.

**작업 출력 시작 시간**옵션 중 하나를 선택 합니다. 옵션은 *현재*, *사용자 지정*및 작업을 이전에 실행 한 경우 마지막으로  *중지 된 시간*입니다. 이러한 옵션에 대 한 자세한 내용은 위의 항목을 참조 하세요.

## <a name="visual-studio"></a>Visual Studio

작업 보기에서 녹색 화살표 단추를 선택 하 여 작업을 시작 합니다. **작업 출력 시작 모드** 를 설정 하 고 **시작**을 선택 합니다. 작업 상태가 **실행 중**으로 변경 됩니다.

**작업 출력 시작 모드**에는 *jobstarttime*, *customtime*및 *LastOutputEventTime*의 세 가지 옵션이 있습니다. 이 속성이 없으면 기본값은 *Jobstarttime*입니다. 이러한 옵션에 대 한 자세한 내용은 위의 항목을 참조 하세요.


## <a name="powershell"></a>PowerShell

PowerShell을 사용 하 여 작업을 시작 하려면 다음 cmdlet을 사용 합니다.

```powershell
Start-AzStreamAnalyticsJob `
  -ResourceGroupName $resourceGroup `
  -Name $jobName `
  -OutputStartMode 'JobStartTime'
```

**OutputStartMode**에는 *jobstarttime*, *customtime*및 *LastOutputEventTime*의 세 가지 옵션이 있습니다. 이 속성이 없으면 기본값은 *Jobstarttime*입니다. 이러한 옵션에 대 한 자세한 내용은 위의 항목을 참조 하세요.

Cmdlet에 대 한 자세한 내용은 `Start-AzStreamAnalyitcsJob` [AzStreamAnalyticsJob 참조를 참조](/powershell/module/az.streamanalytics/start-azstreamanalyticsjob)하세요.

## <a name="next-steps"></a>다음 단계

* [빠른 시작: Azure Portal을 사용하여 Stream Analytics 작업 만들기](stream-analytics-quick-create-portal.md)
* [빠른 시작: Azure PowerShell를 사용 하 여 Stream Analytics 작업 만들기](stream-analytics-quick-create-powershell.md)
* [빠른 시작: Azure Stream Analytics Tools for Visual Studio를 사용하여 Stream Analytics 작업 만들기](stream-analytics-quick-create-vs.md)
