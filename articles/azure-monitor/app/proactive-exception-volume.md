---
title: 스마트 검색 - Azure Application Insights에서 예외 볼륨의 비정상적인 증가 | Microsoft Docs
description: Azure Application Insights에서 애플리케이션 예외를 모니터링하여 예외 볼륨의 비정상적인 패턴을 확인합니다.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: ea2a28ed-4cd9-4006-bd5a-d4c76f4ec20b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 12/08/2017
ms.author: mbullwin
ms.openlocfilehash: a6e7e8e01ccb623a3ff340c318c9c238c919cb38
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61298587"
---
# <a name="abnormal-rise-in-exception-volume-preview"></a>예외 볼륨의 비정상적인 증가(미리 보기)

Application Insights는 애플리케이션에서 throw된 예외를 자동으로 분석하고 예외 원격 분석에서 비정상적인 패턴을 경고할 수 있습니다.

이 기능을 사용하려면 앱에 대한 [예외 보고를 구성](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net-exceptions#set-up-exception-reporting)하는 것 외에 특별한 설정이 필요하지 않습니다. 앱에서 충분한 예외 원격 분석을 생성하면 이 기능은 활성화됩니다.

## <a name="when-would-i-get-this-type-of-smart-detection-notification"></a>이 유형의 스마트 검색 알림은 언제 받게 되나요?
앱에서 하루 동안 특정 유형의 예외 수가 지난 7일 동안 계산된 기준에 비해 비정상적으로 증가하는 경우 이 유형의 알림을 받을 수 있습니다.
Machine Learning 알고리즘을 사용하여 예외 수의 증가를 검색하고, 애플리케이션 사용량의 자연스러운 증가도 고려합니다.

## <a name="does-my-app-definitely-have-a-problem"></a>내 앱에 분명히 문제가 있나요?
아니요, 알림이 제공된다고 해서 앱에 반드시 문제가 있는 것은 아닙니다. 예외 수가 너무 많으면 대개 애플리케이션 문제를 나타내지만 이러한 예외는 심각하지 않으며 애플리케이션에서 올바로 처리될 수 있습니다.

## <a name="how-do-i-fix-it"></a>이 문제를 어떻게 해결하나요?
알림에는 진단 프로세스에서 도움이 되는 진단 정보가 포함되어 있습니다.
1. **심사.** 이 알림은 영향을 받은 사용자 수 또는 요청의 수를 보여 줍니다. 문제에 우선 순위를 할당하는 데 도움이 될 수 있습니다.
2. **범위.** 이 문제가 모든 트래픽에 영향을 주나요? 아니면 일부 작업에만 영향을 주나요? 이 정보는 알림에서 얻을 수 있습니다.
3. **진단.** 이 검색은 예외가 throw된 메서드뿐 아니라 예외 형식에 대한 정보를 포함합니다. 지원 정보에 연결된 관련 항목 및 보고서를 사용하면 문제를 추가로 진단할 수 있습니다.