---
title: 메모리 누수 검색 - Azure Application Insights 스마트 검색
description: Azure Application Insights를 사용하여 애플리케이션의 잠재적 메모리 누수를 모니터링합니다.
ms.topic: conceptual
ms.date: 12/12/2017
ms.openlocfilehash: 3fe58cd7d61246c5565cd89fa782c8a977f09499
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "86539893"
---
# <a name="memory-leak-detection-preview"></a>메모리 누수 검색(미리 보기)

Application Insights는 애플리케이션에서 각 프로세스의 메모리 사용을 자동으로 분석하고 잠재적 메모리 누수 또는 메모리 사용 증가에 대해 경고할 수 있습니다.

이 기능을 사용하려면 앱에 대한 [성능 카운터 구성](./performance-counters.md) 이외의 특별한 설정이 필요하지 않습니다. 앱이 충분한 메모리 성능 카운터 원격 분석(예: 프라이빗 바이트)을 생성할 때 활성 상태입니다.

## <a name="when-would-i-get-this-type-of-smart-detection-notification"></a>이 형식의 스마트 검색 알림은 언제 받게 되나요?
애플리케이션의 일부인 하나 이상의 머신 및/또는 하나 이상의 프로세스에서 메모리 사용이 장기간 걸쳐 지속적으로 증가하면 일반 알림이 제공됩니다. 기계 학습 알고리즘은 메모리 누출 패턴과 일치하는 증가된 메모리 소비를 감지하는 데 사용됩니다.

## <a name="does-my-app-really-have-a-problem"></a>내 앱에 정말 문제가 있나요?
아니요, 알림이 제공된다고 해서 앱에 반드시 문제가 있는 것은 아닙니다. 메모리 누수 패턴은 일반적으로 애플리케이션 문제를 나타내지만, 이러한 패턴은 특정 프로세스에 일반적이거나, 자연스러운 비즈니스 이유가 있어 무시할 수 있습니다.

## <a name="how-do-i-fix-it"></a>이 문제를 어떻게 해결하나요?
알림에는 진단 프로세스에서 도움이 되는 진단 분석 정보가 포함되어 있습니다.
1. **심사.** 알림에는 메모리 증가(GB) 및 메모리가 증가된 시간 범위를 표시됩니다. 문제에 우선 순위를 할당하는 데 도움이 될 수 있습니다.
2. **범위.** 메모리 누수 패턴을 표시하는 컴퓨터 대수는? 잠재적 메모리 누수 중에 트리거된 예외 수는? 이 정보는 알림에서 얻을 수 있습니다.
3. **진단.** 검색에는 시간에 따른 프로세스의 메모리 사용을 보여 주는 메모리 누수 패턴이 포함됩니다. 지원 정보에 연결된 관련 항목 및 보고서를 사용하면 문제를 추가로 진단할 수 있습니다.
