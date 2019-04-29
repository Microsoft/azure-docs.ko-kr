---
title: Azure Application Insights 원격 분석 데이터 모델 | Microsoft Docs
description: Application Insights 데이터 모델 개요
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 04/25/2017
ms.reviewer: sergkanz
ms.author: mbullwin
ms.openlocfilehash: 749b4077b457eff836ec515f21d97e892e663156
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60899199"
---
# <a name="application-insights-telemetry-data-model"></a>Application Insights 원격 분석 데이터 모델

[Azure Application Insights](../../azure-monitor/app/app-insights-overview.md)는 애플리케이션의 성능과 사용을 분석할 수 있도록 웹 애플리케이션에서 Azure Portal로 원격 분석을 보냅니다. 원격 분석 모델은 플랫폼 및 언어 독립적인 모니터링을 만들 수 있도록 표준화되었습니다. 

Application Insights에서 수집한 데이터를 통해 다음과 같은 일반적인 애플리케이션 실행 패턴을 모델링합니다.

![Application Insights 애플리케이션 모델](./media/data-model/application-insights-data-model.png)

다음 유형의 원격 분석을 사용하여 앱 실행을 모니터링합니다. 일반적으로 Application Insights SDK에서 웹 애플리케이션 프레임워크로부터 자동으로 수집하는 세 가지 유형은 다음과 같습니다.

* [**요청**](data-model-request-telemetry.md) - 앱에서 받은 요청을 기록하기 위해 생성됩니다. 예를 들어 Application Insights 웹 SDK는 웹앱에서 받은 각 HTTP 요청에 대한 요청 원격 분석 항목을 자동으로 생성합니다. 

    **작업**은 요청을 처리하는 실행 스레드입니다. 또한 [코드를 작성](../../azure-monitor/app/api-custom-events-metrics.md#trackrequest)하여 정기적으로 데이터를 처리하는 웹 작업 또는 함수의 "절전 모드 해제"와 같은 다른 유형의 작업을 모니터링할 수 있습니다.  각 작업에는 ID가 있습니다. 이 ID를 사용하여 앱에서 요청을 처리하는 동안 생성된 모든 원격 분석을 [그룹화](../../azure-monitor/app/correlation.md)할 수 있습니다. 각 작업은 성공하거나 실패하며 일정 기간 동안 지속됩니다.
* [**예외**](data-model-exception-telemetry.md) - 일반적으로 작업이 실패하게 하는 예외를 나타냅니다.
* [**종속성**](data-model-dependency-telemetry.md) - REST API 또는 SQL과 같은 외부 서비스나 저장소에 대한 앱의 호출을 나타냅니다. ASP.NET에서 SQL에 대한 종속성 호출은 `System.Data`으로 정의됩니다. HTTP 엔드포인트에 대한 호출은 `System.Net`으로 정의됩니다. 

Application Insights는 사용자 지정 원격 분석을 위한 데이터 형식으로 다음 세 가지 형식을 추가로 제공합니다.

* [추적](data-model-trace-telemetry.md) - `Log4Net` 또는 `System.Diagnostics`와 같이 친숙한 계측 프레임워크를 사용하는 진단 로깅을 구현하는 데 직접 사용되거나 어댑터를 통해 사용됩니다.
* [이벤트](data-model-event-telemetry.md) - 일반적으로 사용 패턴을 분석하기 위해 서비스와 사용자 간의 상호 작용을 캡처하는 데 사용됩니다.
* [메트릭](data-model-metric-telemetry.md) - 정기적인 스칼라 측정을 보고하는 데 사용됩니다.

모든 원격 분석 항목은 애플리케이션 버전 또는 사용자 세션 ID와 같은 [컨텍스트 정보](data-model-context.md)를 정의할 수 있습니다. 컨텍스트는 특정 시나리오를 차단 해제하는 강력한 형식의 필드 집합입니다. 애플리케이션 버전이 올바르게 초기화된 경우 Application Insights는 재배포와 상호 관련된 애플리케이션 동작에서 새 패턴을 검색할 수 있습니다. 세션 ID를 사용하여 가동 중단 또는 문제가 사용자에게 미치는 영향을 계산할 수 있습니다. 실패한 특정 종속성, 오류 추적 또는 중요한 예외에 대한 세션 ID 값의 고유 개수를 계산하면 영향을 쉽게 이해할 수 있습니다.

Application Insights 원격 분석 모델은 원격 분석과 해당 분석에 속하는 작업 간의 [상관 관계를 지정](../../azure-monitor/app/correlation.md)하는 방법을 정의합니다. 예를 들어 요청은 SQL Database 호출을 수행하고 기록된 진단 정보를 작성할 수 있습니다. 요청 원격 분석과 다시 연결할 해당 원격 분석 항목에 대한 상관 관계 컨텍스트를 설정할 수 있습니다.

## <a name="schema-improvements"></a>향상된 스키마

Application Insights 데이터 모델은 애플리케이션 원격 분석을 모델링하는 단순하고 기본적이면서도 강력한 방법입니다. 처음에는 기본 시나리오를 지원할 수 있게 단순하고 간단한 모델을 구현한 후 고급 사용을 위해 스키마를 확장할 수 있도록 합니다.

데이터 모델 또는 스키마 문제와 제안 사항을 보고하려면 GitHub [ApplicationInsights-Home](https://github.com/Microsoft/ApplicationInsights-Home/labels/schema) 리포지토리를 사용합니다.

## <a name="next-steps"></a>다음 단계

- [사용자 지정 원격 분석을 작성합니다](../../azure-monitor/app/api-custom-events-metrics.md).
- [원격 분석을 확장 및 필터링](../../azure-monitor/app/api-filtering-sampling.md)하는 방법을 알아봅니다.
- [샘플링](../../azure-monitor/app/sampling.md)을 사용하여 데이터 모델을 기준으로 하는 원격 분석의 양을 최소화합니다.
- Application Insights에서 지원되는 [플랫폼](../../azure-monitor/app/platforms.md)을 확인합니다.
