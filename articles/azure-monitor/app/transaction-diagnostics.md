---
title: Azure Application Insights 트랜잭션 진단 | Microsoft Docs
description: Application Insights 종단 간 트랜잭션 진단
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 01/19/2018
ms.reviewer: sdash
ms.author: mbullwin
ms.openlocfilehash: c6c44525018e2115f1df8ed2d3f15432b95490c6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60783715"
---
# <a name="unified-cross-component-transaction-diagnostics"></a>통합된 구성 요소 간 트랜잭션 진단

통합 진단 환경은 모든 Application Insights 모니터링 구성 요소에서 서버 쪽 원격 분석을 단일 보기로 자동으로 상관 관계를 지정합니다. 별도의 계측 키가 있는 여러 리소스가 있는 경우에는 문제가 되지 않습니다. Application Insights는 기본 관계를 감지하여 트랜잭션 속도 저하 또는 실패가 발생하는 애플리케이션 구성 요소, 종속성 또는 예외를 쉽게 진단할 수 있도록 합니다.

## <a name="what-is-a-component"></a>구성 요소란?

구성 요소는 독립적으로 배포할 수 있는 분산/마이크로 서비스 애플리케이션의 부분입니다. 개발자 및 운영 팀은 이러한 애플리케이션 구성 요소에서 생성된 원격 분석에 대한 코드 수준의 가시성 또는 액세스 권한을 갖습니다.

* 구성 요소는 팀/조직에서 액세스할 수 없는 SQL, EventHub 등과 같은 "관찰된" 외부 종속성(코드 또는 원격 분석)과 다릅니다.
* 구성 요소는 서버/역할/컨테이너 인스턴스의 수에 관계없이 실행됩니다.
* 구성 요소는 별도의 Application Insights 계측 키(구독이 다른 경우에도) 또는 단일 Application Insights 계측 키에 보고하는 다른 역할일 수 있습니다. 새 환경에서는 설정 방법에 관계없이 모든 구성 요소에 대한 세부 정보가 표시됩니다.

> [!NOTE]
> * **관련 항목 링크가 누락되었나요?** 모든 관련 원격 분석은 왼쪽의 [맨 위](#cross-component-transaction-chart) 및 [맨 아래](#all-telemetry-with-this-operation-id) 섹션에 있습니다. 

## <a name="transaction-diagnostics-experience"></a>트랜잭션 진단 환경
이 보기에는 네 가지 주요 부분, 즉 결과 목록, 구성 요소 간 트랜잭션 차트, 이 작업 관련된 모든 원격 분석의 시간 순서 목록 및 선택한 원격 분석 항목에 대한 세부 정보 창이 왼쪽에 있습니다.

![주요 부분](media/transaction-diagnostics/4partsCrossComponent.png)

## <a name="cross-component-transaction-chart"></a>구성 요소 간 트랜잭션 차트

이 차트는 구성 요소 간의 요청 및 종속성의 기간에 대한 가로 막대가 있는 타임라인을 제공합니다. 수집된 모든 예외도 타임라인에 표시됩니다.

* 이 차트의 맨 위에 있는 행은 진입점, 즉 이 트랜잭션에서 호출된 첫 번째 구성 요소에 대해 들어오는 요청을 나타냅니다. 기간은 트랜잭션이 완료되는 데 걸린 총 시간입니다.
* 외부 종속성에 대한 호출은 종속성 유형을 나타내는 아이콘이 있으며, 접을 수 없는 간단한 행입니다.
* 다른 구성 요소에 대한 호출은 접을 수 있는 행입니다. 각 행은 구성 요소에서 호출된 특정 작업에 해당합니다.
* 기본적으로 선택한 요청, 종속성 또는 예외가 오른쪽에 표시됩니다.
* 행을 선택하면 [해당 세부 정보가 오른쪽](#details-of-the-selected-telemetry)에 표시됩니다. 

> [!NOTE]
> 다른 구성 요소에 대한 호출에는 두 행이 있습니다. 한 행은 호출자 구성 요소의 아웃바운드 호출(종속성)을 나타내고, 다른 한 행은 호출된 구성 요소의 인바운드 요청에 해당합니다. 기간 막대의 선행 아이콘과 고유한 스타일은 구분하는 데 도움이 됩니다.

## <a name="all-telemetry-with-this-operation-id"></a>이 작업 ID를 사용한 모든 원격 분석

이 섹션에서는 이 트랜잭션에 관련된 모든 원격 분석의 시간 시퀀스에서 기본 목록 보기를 보여줍니다. 또한 트랜잭션 차트에 표시되지 않는 추적 및 사용자 지정 이벤트도 보여줍니다. 특정 구성 요소/호출에서 생성된 원격 분석에 이 목록을 필터링할 수 있습니다. 이 목록에서 원격 분석 항목을 선택하면 해당 [세부 정보가 오른쪽](#details-of-the-selected-telemetry)에 표시됩니다.

![모든 원격 분석에 대한 시간 순서](media/transaction-diagnostics/allTelemetryDrawerOpened.png)

## <a name="details-of-the-selected-telemetry"></a>선택한 원격 분석의 세부 정보

이 접을 수 있는 창에는 트랜잭션 차트 또는 목록에서 선택한 모든 항목의 세부 정보가 표시됩니다. "모두 표시"는 수집된 표준 특성을 모두 나열합니다. 모든 사용자 지정 특성은 표준 집합 아래에 별도로 나열됩니다. 스택 추적 창 아래의 "..."를 클릭하여 추적을 복사하기 위한 옵션을 가져옵니다. "프로파일러 추적 열기" 또는 "디버그 스냅숏 열기"는 해당 세부 정보 창에서 코드 수준 진단을 보여줍니다.

![예외 세부 정보](media/transaction-diagnostics/exceptiondetail.png)

## <a name="search-results"></a>검색 결과

이 접을 수 있는 창에는 필터 조건을 충족하는 다른 결과가 표시됩니다. 모든 결과를 클릭하여 위에 나열된 세 개의 섹션에 해당 세부 정보를 업데이트합니다. 샘플링이 모든 샘플에 적용되는 경우에도 모든 구성 요소에서 사용 가능한 세부 정보가 있을 가능성이 가장 큰 샘플을 찾으려 노력합니다. 이러한 샘플은 "제안된" 샘플로 표시됩니다.

![검색 결과](media/transaction-diagnostics/searchResults.png)

## <a name="profiler-and-snapshot-debugger"></a>프로파일러 및 스냅숏 디버거

[Application Insights 프로파일러](../../azure-monitor/app/profiler.md) 또는 [스냅숏 디버거](snapshot-debugger.md)는 성능 및 오류 문제에 대한 코드 수준 진단에 도움이 됩니다. 이 환경을 사용하면 한 번의 클릭으로 모든 구성 요소에서 프로파일러 추적 또는 스냅숏을 볼 수 있습니다.

Profiler 작업 못한 경우에 문의 하세요 **serviceprofilerhelp\@microsoft.com**

스냅숏 디버거 작업 못한 경우에 문의 하세요 **snapshothelp\@microsoft.com**

![프로파일러 통합](media/transaction-diagnostics/profilerTraces.png)

## <a name="faq"></a>FAQ

*단일 구성 요소가 차트에 표시되고, 다른 구성 요소는 해당 구성 요소 내에서 발생한 항목에 대한 세부 정보 없이 외부 종속성으로만 표시됩니다.*

잠재적 원인은 다음과 같습니다.

* 다른 구성 요소가 Application Insights를 통해 계측됩니까?
* 최신의 안정적인 Application Insights SDK를 사용하고 있습니까?
* 이러한 구성 요소가 별도의 Application Insights 리소스인 경우 해당 원격 분석에 액세스해야 합니까?

액세스 권한이 있고 구성 요소가 최신 Application Insights SDK를 통해 계측되는 경우 오른쪽 위에 있는 피드백 채널을 통해 알려주세요.

*종속성에 대해 중복된 행이 표시됩니다. 이것은 예상된 동작인가요?*

현재 아웃바운드 종속성 호출은 인바운드 요청과는 별도로 표시됩니다. 일반적으로 두 호출은 네트워크 왕복으로 인해 기간 값만 다를 뿐 동일하게 보입니다. 기간 막대의 선행 아이콘과 고유한 스타일은 구분하는 데 도움이 됩니다. 이러한 데이터 표시가 혼란스러운가요? 의견을 보내주세요!

*서로 다른 구성 요소 인스턴스 간의 클록 스큐는 어떨까요?*

타임라인은 트랜잭션 차트의 클록 스큐에 맞게 조정됩니다. 세부 정보 창이나 Analytics를 사용하여 정확한 타임스탬프를 볼 수 있습니다.

*새 환경에서 대부분의 관련 항목 쿼리가 누락되는 이유는 무엇인가요?*

의도적인 작동입니다. 모든 구성 요소에서 관련된 모든 항목은 이미 왼쪽(위쪽 및 아래쪽 섹션)에서 사용할 수 있습니다. 새 환경에는 왼쪽에서 다루지 않는 두 개의 관련 항목, 즉 이 이벤트 전후 5분의 모든 원격 분석 및 사용자 타임라인이 있습니다.
