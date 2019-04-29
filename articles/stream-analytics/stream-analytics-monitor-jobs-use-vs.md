---
title: Visual Studio를 사용하여 Azure Stream Analytics 작업 모니터링 및 관리
description: 이 문서에서는 Visual Studio를 사용하여 Azure Stream Analytics 작업을 모니터링하고 관리하는 방법을 설명합니다.
services: stream-analytics
author: su-jie
ms.author: sujie
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: e673d4f1c2d58a61c541cca4f8448dffc003f9fa
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61479439"
---
# <a name="monitor-and-manage-stream-analytics-jobs-with-visual-studio"></a>Visual Studio를 사용하여 Stream Analytics 작업 모니터링 및 관리

이 문서에서는 Visual Studio에서 Stream Analytics 작업을 모니터링하는 방법을 보여줍니다. Visual Studio용 Azure Stream Analytics 도구는 IDE를 벗어날 필요가 없이 Azure Portal과 비슷한 모니터링 환경을 제공합니다. **Script.asaql**에서 **Azure에 제출**하는 즉시 작업 모니터링을 시작하거나, 작업이 만들어진 방식에 관계없이 기존 작업을 모니터링할 수 있습니다. 

## <a name="job-summary"></a>작업 요약

**작업 요약** 및 **작업 메트릭**은 작업의 빠른 스냅숏을 제공합니다. 작업의 상태 및 이벤트 정보를 한눈에 확인할 수 있습니다.

<img src="./media/stream-analytics-monitor-jobs-use-vs/stream-analytics-job-summary-metrics.png" alt="Stream Analytics job summary and job metrics" width="300px"/> 


## <a name="job-metrics"></a>작업 메트릭

**작업 요약**을 축소하고 **작업 메트릭** 탭을 클릭하여 중요한 메트릭이 포함된 그래프를 볼 수 있습니다. 메트릭 유형을 선택 또는 선택 취소하여 그래프에서 메트릭 유형을 추가하거나 취소할 수 있습니다.

![Visual Studio에서 Stream Analytics 메트릭](./media/stream-analytics-monitor-jobs-use-vs/stream-analytics-vs-metrics.png)


## <a name="error-monitoring"></a>오류 모니터링

**오류** 탭을 클릭하여 오류를 모니터링할 수도 있습니다.

![Visual Studio에서 Stream Analytics 오류](./media/stream-analytics-monitor-jobs-use-vs/stream-analytics-vs-errors.png)


## <a name="get-support"></a>지원 받기
추가 지원이 필요한 경우 [Azure Stream Analytics 포럼](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)을 참조하세요. 

## <a name="next-steps"></a>다음 단계
* [Azure Stream Analytics 소개](stream-analytics-introduction.md)
* [Visual Studio를 사용하여 Azure Stream Analytics 작업 만들기](stream-analytics-quick-create-vs.md)
* [Visual Studio용 Azure Stream Analytics 도구 설치](stream-analytics-tools-for-visual-studio-install.md) 


