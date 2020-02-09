---
title: 컨테이너 상태 모니터 구성에 대 한 Azure Monitor | Microsoft Docs
description: 이 문서는 컨테이너에 대 한 Azure Monitor에서 상태 모니터의 세부 구성을 설명 하는 콘텐츠를 제공 합니다.
ms.topic: conceptual
ms.date: 12/01/2019
ms.openlocfilehash: d2d602d767fa6a39b7f72650c426c90be210a6ed
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75405033"
---
# <a name="azure-monitor-for-containers-health-monitor-configuration-guide"></a>컨테이너 상태 모니터의 Azure Monitor 구성 가이드

모니터는 컨테이너에 대 한 Azure Monitor 상태를 측정 하 고 오류를 검색 하기 위한 기본 요소입니다. 이 문서는 상태를 측정 하는 방법 및 상태 모델을 구성 하는 요소가 [상태 (미리 보기)](container-insights-health.md) 기능을 사용 하 여 Kubernetes 클러스터의 상태를 모니터링 하 고 보고 하는 방법에 대 한 개념을 이해 하는 데 도움이 됩니다.

>[!NOTE]
>상태 기능은 현재 공개 미리 보기로 제공 됩니다.
>

## <a name="monitors"></a>모니터

모니터는 관리 개체의 특정 양상에 대한 성능을 측정합니다. 각 모니터는 두 개 또는 세 개의 성능 상태를 포함합니다. 모니터는 항상 한 가지 상태만 표시합니다. 컨테이너 화 된 에이전트에서 모니터를 로드 하면 모니터는 정상 상태로 초기화 됩니다. 상태는 다른 상태에 대해 지정 된 조건이 검색 되는 경우에만 변경 됩니다.

특정 개체의 전반적인 상태는 각 모니터의 상태에서 결정됩니다. 이 계층 구조는 컨테이너에 대 한 Azure Monitor의 상태 계층 구조 창에 설명 되어 있습니다. 상태 롤업 방식에 대 한 정책은 집계 모니터 구성의 일부입니다.

## <a name="types-of-monitors"></a>모니터 유형

|모니터 | Description | 
|--------|-------------|
| 유닛 모니터 |유닛 모니터는 리소스나 응용 프로그램의 일부 측면을 측정 합니다. 이는 성능 카운터를 확인 하 여 리소스의 성능 또는 가용성을 확인할 수 있습니다. |
|집계 모니터 | 집계 모니터는 여러 모니터를 그룹화 하 여 단일 상태 집계 상태를 제공 합니다. 유닛 모니터는 일반적으로 특정 집계 모니터에서 구성 됩니다. 예를 들어 노드 집계 모니터는 노드 CPU 사용률, 메모리 사용률 및 노드 상태의 상태를 롤업 합니다.
 |

### <a name="aggregate-monitor-health-rollup-policy"></a>집계 모니터 상태 롤업 정책

각 집계 모니터는 상태 롤업 정책을 정의 합니다 .이는 모니터의 상태를 기준으로 집계 모니터의 상태를 결정 하는 데 사용 되는 논리입니다. 집계 모니터에 대 한 가능한 상태 롤업 정책은 다음과 같습니다.

#### <a name="worst-state-policy"></a>최악의 상태 정책

집계 모니터의 상태는 최하위 상태와 자식 모니터의 상태를 일치 시킵니다. 이는 집계 모니터에서 가장 일반적으로 사용 되는 정책입니다.

![집계 모니터 롤업 최악의 상태 예](./media/container-insights-health-monitoring-cfg/aggregate-monitor-rollup-worstof.png)

### <a name="percentage-policy"></a>백분율 정책

원본 개체는 최적 상태에서 대상 개체의 지정 된 비율에 해당 하는 멤버 중 최악의 상태를 일치 시킵니다. 이 정책은 대상 개체가 정상 상태로 간주 되기 위해 특정 비율의 대상 개체가 정상이 되어야 하는 경우에 사용 됩니다. 백분율 정책은 모니터를 상태 심각도의 내림차순으로 정렬 하 고, 집계 모니터의 상태는 N%의 최악의 상태 (N은 구성 매개 변수 *StateThresholdPercentage*에 의해 결정 됨)로 계산 됩니다.

예를 들어 컨테이너 이미지의 컨테이너 인스턴스가 5 개이고 개별 상태가 **위험**, **경고** **, 정상**, **정상**, **정상**입니다.  컨테이너 CPU 사용률 모니터의 상태는 심각도가 내림차순으로 정렬 될 때 컨테이너의 90% 상태가 **심각** 하기 때문에 **중요**합니다.

## <a name="understand-the-monitoring-configuration"></a>모니터링 구성 이해

컨테이너에 대 한 Azure Monitor에는 다음과 같이 구성 된 여러 가지 주요 모니터링 시나리오가 포함 되어 있습니다.

### <a name="unit-monitors"></a>유닛 모니터

|**모니터 이름** | 모니터 유형 | **설명** | **매개 변수** | **값** |
|-----------------|--------------|-----------------|---------------|-----------|
|노드 메모리 사용률 |유닛 모니터 |이 모니터는 cadvisor에서 보고 한 데이터를 사용 하 여 1 분 마다 노드의 메모리 사용률을 평가 합니다. |ConsecutiveSamplesForStateTransition<br> FailIfGreaterThanPercentage<br> WarnIfGreaterThanPercentage | 3<br> 90<br> 80  ||
|노드 CPU 사용률 |유닛 모니터 |이 모니터는 cadvisor에서 보고 한 데이터를 사용 하 여 1 분 마다 노드의 CPU 사용률을 확인 합니다. | ConsecutiveSamplesForStateTransition<br> FailIfGreaterThanPercentage<br> WarnIfGreaterThanPercentage | 3<br> 90<br> 80  ||
|노드 상태 |유닛 모니터 |이 모니터는 Kubernetes에서 보고 한 노드 상태를 확인 합니다.<br> 현재 다음 노드 조건을 검사 합니다. 디스크 압력, 메모리 부족, PID 압력, 디스크 부족, 네트워크 사용할 수 없음, 노드의 준비 상태.<br> 위의 조건 중에서 *디스크 외부* 또는 *네트워크를 사용할 수 없는* 경우 모니터가 **위험** 상태로 **변경 됩니다.**<br> 다른 **조건에 대해** **준비** 됨 상태 외에도 해당 하는 경우 모니터가 **경고** 상태로 변경 됩니다. | NodeConditionTypeForFailedState | outofdisk, networkunavailable 수 없음 ||
|컨테이너 메모리 사용률 |유닛 모니터 |이 모니터는 컨테이너 인스턴스의 메모리 사용률 (RSS)의 결합 상태를 보고 합니다.<br> 각 샘플을 단일 임계값과 비교 하 고 구성 매개 변수 **ConsecutiveSamplesForStateTransition**으로 지정 하는 간단한 비교를 수행 합니다.<br> 상태는 컨테이너 (StateThresholdPercentage) 인스턴스의 90%에 해당 하는 최악의 상태로 계산 되며 컨테이너 성능 상태 (즉, 위험, 경고, 정상)의 심각도를 기준으로 내림차순으로 정렬 됩니다.<br> 컨테이너 인스턴스에서 레코드가 수신 되지 않으면 컨테이너 인스턴스의 상태가 **알 수 없음**으로 보고 되 고 정렬 순서에서 **위험** 상태에 대 한 우선 순위가 높습니다.<br> 각 개별 컨테이너 인스턴스의 상태는 구성에 지정 된 임계값을 사용 하 여 계산 됩니다. 사용량이 위험 임계값 (90%)을 초과 하는 경우 인스턴스는 위험 상태 (90%) 보다 작은 경우 **위험** 상태에 있습니다. 그러나 경고 임계값 (80%) 보다 크면 인스턴스가 **경고** 상태에 있습니다. 그렇지 않으면 **정상** 상태입니다. |ConsecutiveSamplesForStateTransition<br> FailIfLessThanPercentage<br> StateThresholdPercentage<br> WarnIfGreaterThanPercentage| 3<br> 90<br> 90<br> 80 ||
|컨테이너 CPU 사용률 |유닛 모니터 |이 모니터는 컨테이너 인스턴스의 CPU 사용률에 대 한 결합 상태를 보고 합니다.<br> 각 샘플을 단일 임계값과 비교 하 고 구성 매개 변수 **ConsecutiveSamplesForStateTransition**으로 지정 하는 간단한 비교를 수행 합니다.<br> 상태는 컨테이너 (StateThresholdPercentage) 인스턴스의 90%에 해당 하는 최악의 상태로 계산 되며 컨테이너 성능 상태 (즉, 위험, 경고, 정상)의 심각도를 기준으로 내림차순으로 정렬 됩니다.<br> 컨테이너 인스턴스에서 레코드가 수신 되지 않으면 컨테이너 인스턴스의 상태가 **알 수 없음**으로 보고 되 고 정렬 순서에서 **위험** 상태에 대 한 우선 순위가 높습니다.<br> 각 개별 컨테이너 인스턴스의 상태는 구성에 지정 된 임계값을 사용 하 여 계산 됩니다. 사용량이 위험 임계값 (90%)을 초과 하는 경우 인스턴스는 위험 상태 (90%) 보다 작은 경우 **위험** 상태에 있습니다. 그러나 경고 임계값 (80%) 보다 크면 인스턴스가 **경고** 상태에 있습니다. 그렇지 않으면 **정상** 상태입니다. |ConsecutiveSamplesForStateTransition<br> FailIfLessThanPercentage<br> StateThresholdPercentage<br> WarnIfGreaterThanPercentage| 3<br> 90<br> 90<br> 80 ||
|시스템 작업 pod 준비 완료 |유닛 모니터 |이 모니터는 지정 된 작업에서 준비 상태에 있는 pod 비율을 기준으로 상태를 보고 합니다. Pod의 100% 미만이 **정상** 상태 이면 해당 상태가 **위험** 으로 설정 됩니다. |ConsecutiveSamplesForStateTransition<br> FailIfLessThanPercentage |2<br> 100 ||
|Kube API 상태 |유닛 모니터 |이 모니터는 Kube Api 서비스의 상태를 보고 합니다. Kube Api 엔드포인트을 사용할 수 없는 경우 모니터가 위험 상태입니다. 이 특정 모니터의 경우 상태는 kube 서버에 대 한 ' nodes ' 엔드포인트에 대 한 쿼리를 만들어 결정 됩니다. OK 응답 코드 이외의 모든 항목은 모니터를 **위험** 상태로 변경 합니다. | 구성 속성 없음 |||

### <a name="aggregate-monitors"></a>집계 모니터

|**모니터 이름** | **설명** | **알고리즘** |
|-----------------|-----------------|---------------|
|노드 |이 모니터는 모든 노드 모니터의 집계입니다. 상태는 최하위 상태의 자식 모니터 상태와 일치 합니다.<br> 노드 CPU 사용률<br> 노드 메모리 사용률<br> 노드 상태 | 최악의|
|노드 풀 |이 모니터는 노드 풀 *agentpool*에 있는 모든 노드의 전체 상태를 보고 합니다. 이 모니터는 세 가지 상태 모니터입니다 .이 모니터의 상태는 노드 풀에 있는 노드의 80%에 해당 하는 최악의 상태를 기준으로 하 여 노드 상태 (위험, 경고, 정상)의 심각도를 기준으로 내림차순으로 정렬 되어 있습니다.|백분율 |
|노드 (노드 풀의 부모) |모든 노드 풀의 집계 모니터입니다. 상태는 자식 모니터의 최악의 상태 (즉, 클러스터에 있는 노드 풀)를 기반으로 합니다. |최악의 |
|클러스터 (노드의 부모/<br> Kubernetes 인프라) |이는 자식 모니터의 상태와 kubernetes 인프라 및 노드인 최하위 상태를 일치 시키는 부모 모니터입니다. |최악의 |
|Kubernetes 인프라 |이 모니터는 클러스터의 관리 되는 인프라 구성 요소에 대 한 결합 상태를 보고 합니다. 해당 상태는 자식 모니터 상태 (kube 시스템 워크 로드 및 API 서버 상태)의 ' 최악의 '로 계산 됩니다. |최악의|
|시스템 작업 |이 모니터는 kube 시스템 작업의 상태를 보고 합니다. 이 모니터는 준비 상태 (모니터 및 워크 로드의 컨테이너) **의 pod** 인 최하위 상태와 자식 모니터의 상태를 일치 시킵니다. |최악의 |
|컨테이너 |이 모니터는 지정 된 작업에서 컨테이너의 전반적인 상태를 보고 합니다. 이 모니터는 **CPU 사용률** 및 **메모리 사용률** 모니터와 같은 최악의 상태와 자식 모니터의 상태를 일치 시킵니다. |최악의 |

## <a name="next-steps"></a>다음 단계

[모니터 클러스터 상태](container-insights-health.md) 보기 Kubernetes 클러스터의 상태를 보는 방법에 대해 알아봅니다.
