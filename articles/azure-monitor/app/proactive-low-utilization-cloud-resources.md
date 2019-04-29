---
title: 스마트 검색 - Azure Application Insights에서 검색된 클라우드 리소스의 낮은 사용률 | Microsoft Docs
description: Azure Application Insights를 사용하여 애플리케이션에서 클라우드 리소스의 낮은 사용률을 모니터링합니다.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: ea2a28ed-4cd9-4006-bd5a-d4c76f4ec20b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 12/12/2017
ms.author: mbullwin
ms.openlocfilehash: 7cf72068b9cabceb0c5b535986ac4dfb62151b94
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61297515"
---
# <a name="low-cpu-utilization-in-cloud-resources-preview"></a>클라우드 리소스의 낮은 CPU 사용률(미리 보기)

Application Insight는 애플리케이션에서 각 역할 인스턴스의 CPU 사용을 자동으로 분석하고 CPU 사용률이 낮은 인스턴스를 검색합니다. 이 검색을 사용하면 각 역할이 사용하는 역할 인스턴스 수를 줄이거나 역할 수를 줄여 Azure 리소스를 줄이고 비용을 절감할 수 있습니다.

이 기능을 사용하려면 앱에 대한 [성능 카운터 구성](https://docs.microsoft.com/azure/application-insights/app-insights-performance-counters) 이외의 특별한 설정이 필요하지 않습니다. 앱에서 충분한 CPU 성능 카운터 원격 분석(% 프로세서 시간)을 생성하면 이 기능이 활성화됩니다.

## <a name="when-would-i-get-this-type-of-smart-detection-notification"></a>이 형식의 스마트 검색 알림은 언제 받게 되나요?
많은 웹/작업자 역할 인스턴스의 CPU 사용률이 낮을 경우 일반적인 알림이 발생합니다.

## <a name="does-my-app-definitely-consume-too-many-resources"></a>내 앱이 확실히 너무 많은 리소스를 사용하고 있는 것인가요?
아니요, 알림이 제공된다고 해서 앱에서 너무 많은 리소스를 사용하고 있는 것은 아닙니다. 이러한 패턴의 낮은 CPU 사용률은 대개 리소스 사용을 줄일 수 있음을 나타내지만 특정 역할에는 이 동작이 일반적일 수 있거나 합당한 비즈니스 근거가 있을 수 있으며 무시해도 됩니다. 예를 들어 여러 인스턴스가 CPU가 아니라 메모리/네트워크 같은 다른 리소스에 필요한 것 일 수 있습니다.

## <a name="how-do-i-fix-it"></a>이 문제를 어떻게 해결하나요?
알림에는 진단 프로세스에서 도움이 되는 진단 정보가 포함되어 있습니다.
1. **심사.** 이 알림은 CPU 사용률이 낮은 앱의 역할을 보여 줍니다. 문제에 우선 순위를 할당하는 데 도움이 될 수 있습니다.
2. **범위.** CPU 사용률이 낮은 역할의 수와 각 역할에서 적은 CPU를 사용하는 인스턴스 수는 어떻게 되나요? 이 정보는 알림에서 얻을 수 있습니다.
3. **진단.** 이 검색에는 각 인스턴스의 CPU 사용률을 나타내는 사용된 CPU의 비율이 포함되어 있습니다. CPU 사용률 백분위 수와 같은 지원 정보로 연결되는 관련 항목 및 보고서를 사용하여 문제를 더 자세히 진단할 수도 있습니다.
