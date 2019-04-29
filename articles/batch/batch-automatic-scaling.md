---
title: Azure Batch 풀에서 자동으로 계산 노드 크기 조정 | Microsoft Docs
description: 클라우드 풀에서 자동 크기 조정을 사용하면 풀의 계산 노드 수를 동적으로 조정합니다.
services: batch
documentationcenter: ''
author: laurenhughes
manager: jeconnoc
editor: ''
ms.assetid: c624cdfc-c5f2-4d13-a7d7-ae080833b779
ms.service: batch
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: multiple
ms.date: 06/20/2017
ms.author: lahugh
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: fdc2cd8f2218d50aa49d6b4eab2800eb6c92d9c9
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62118114"
---
# <a name="create-an-automatic-scaling-formula-for-scaling-compute-nodes-in-a-batch-pool"></a>Batch 풀에서 계산 노드의 크기를 조정하는 자동 크기 조정 수식 만들기

Azure Batch는 정의한 매개 변수에 따라 풀을 자동으로 크기 조정합니다. 자동 크기 조정을 사용하면 작업 요구가 증가함에 따라 Batch에서 풀에 노드를 동적으로 추가하고, 감소함에 따라 계산 노드를 제거합니다. Batch 애플리케이션에서 사용하는 계산 노드 수를 자동으로 조정하여 시간과 비용을 모두 절약할 수 있습니다. 

사용자가 정의한 *자동 크기 조정 수식*에 연결하면 계산 노드 풀에서 자동으로 크기를 조정할 수 있습니다. Batch 서비스는 자동 크기 조정 수식을 사용하여 워크로드를 실행하는 데 필요한 계산 노드의 수를 결정합니다. Compute 노드는 전용 노드이거나 [우선 순위가 낮은 노드](batch-low-pri-vms.md)일 수 있습니다. Batch는 주기적으로 수집되는 서비스 메트릭 데이터에 응답합니다. Batch는 이 메트릭 데이터를 사용하여 구성 가능한 간격으로 수식에 따라 풀의 계산 노드 수를 조정합니다.

풀을 만들 때 또는 기존 풀에서 자동 크기 조정을 사용하도록 설정할 수 있습니다. 자동 크기 조정에 대해 구성된 풀의 기존 수식을 변경할 수도 있습니다. Batch를 사용하면 풀에 할당하기 전에 수식을 평가하고, 자동 크기 조정 실행 상태를 모니터링할 수 있습니다.

이 문서에서는 변수, 연산자, 작업 및 함수를 포함하여 자동 크기 조정 수식을 구성하는 다양한 엔터티를 설명합니다. Batch 내의 다양한 계산 리소스 및 작업 메트릭을 가져오는 방법을 알아봅니다. 이러한 메트릭을 사용하여 리소스 사용량과 작업 상태에 따라 풀의 노드 수를 조정할 수 있습니다. 그런 다음 Batch REST 및 .NET API를 모두 사용하여 수식을 구성하고 풀에서 자동 크기 조정을 사용하는 방법을 설명합니다. 마지막으로 몇 가지 예제 수식으로 마무리하겠습니다.

> [!IMPORTANT]
> 배치 계정을 만들 때 [계정 구성](batch-api-basics.md#account)을 지정할 수 있습니다. 이 구성은 Batch 서비스 구독(기본값) 또는 사용자 구독에 풀을 할당하는지 여부를 결정합니다. 기본 Batch 서비스 구성으로 배치 계정을 만든 경우 계정은 처리에 사용할 수 있는 코어의 최대 개수로 제한됩니다. Batch 서비스는 계산 노드를 해당 코어 제한까지만 확장합니다. 이러한 이유로 Batch 서비스는 자동 크기 조정 수식에 지정된 계산 노드의 목표 수에 도달하지 못할 수 있습니다. 계정 할당량을 보고 늘리는 방법에 대한 내용은 [Azure Batch 서비스에 대한 할당량 및 제한](batch-quota-limit.md)을 참조하세요.
>
>사용자 구독 구성으로 계정을 만든 경우 계정은 구독의 코어 할당량을 공유합니다. 자세한 내용은 [Azure 구독 및 서비스 제한, 할당량 및 제약 조건](../azure-subscription-service-limits.md)에서 [Virtual Machines 제한](../azure-subscription-service-limits.md#virtual-machines-limits)을 참조하세요.
>
>

## <a name="automatic-scaling-formulas"></a>자동 크기 조정 수식
자동 크기 조정 수식은 하나 이상의 문을 포함하는 사용자가 정의한 문자열 값입니다. 자동 크기 조정 수식은 풀의 [autoScaleFormula][rest_autoscaleformula] 요소(Batch REST) 또는 [CloudPool.AutoScaleFormula][net_cloudpool_autoscaleformula] 속성(Batch .NET)에 할당됩니다. Batch 서비스는 처리할 다음 간격을 위해 풀의 대상 계산 노드 수를 결정하는 수식을 사용합니다. 수식 문자열은 8KB를 초과할 수 없고, 세미콜론으로 구분된 구문을 100개까지 포함할 수 있으며, 줄 바꿈과 주석을 포함할 수 있습니다.

자동 크기 조정 수식은 Batch 자동 크기 조정 "언어"로 고려할 수 있습니다. 수식 문은 자유 형식이고 서비스가 정의한 변수(Batch 서비스에 의해 정의된 변수) 및 사용자가 정의한 변수(사용자가 정의한 변수)를 포함할 수 있습니다. 기본 제공 형식, 연산자 및 함수를 사용하여 이러한 값에 다양한 작업을 수행할 수 있습니다. 예를 들어 문은 다음과 같은 형태일 수 있습니다.

```
$myNewVariable = function($ServiceDefinedVariable, $myCustomVariable);
```

수식은 일반적으로 이전 문에서 가져온 값에 대한 작업을 수행하는 여러 문을 포함합니다. 예를 들어 먼저 `variable1`에 대한 값을 구한 다음 `variable2`을(를) 채우는 함수로 전달합니다.

```
$variable1 = function1($ServiceDefinedVariable);
$variable2 = function2($OtherServiceDefinedVariable, $variable1);
```

자동 크기 조정 수식에 이러한 문을 포함하여 계산 노드의 목표 수에 도달합니다. 전용 노드와 우선 순위가 낮은 노드에는 각각 자체의 목표 설정이 있으므로 각 노드 형식의 목표를 정의할 수 있습니다. 자동 크기 조정 수식에는 전용 노드의 목표 값, 우선 순위가 낮은 노드의 목표 값 또는 둘 다가 포함될 수 있습니다.

노드의 목표 수는 풀에 있는 해당 노드 형식의 현재 수보다 높거나, 낮거나, 같을 수 있습니다. Batch는 풀의 자동 크기 조정 수식을 특정 간격([자동 크기 조정 간격](#automatic-scaling-interval) 참조)으로 평가합니다. Batch는 풀에 있는 각 노드 형식의 목표 수를 크기 조정 수식에서 평가할 때 지정하는 수로 조정합니다.

### <a name="sample-autoscale-formula"></a>샘플 자동 크기 조정 수식

다음은 대부분의 시나리오에 맞게 조정할 수 있는 자동 크기 조정 수식의 예제입니다. 예제 수식에서 변수 `startingNumberOfVMs` 및 `maxNumberofVMs`는 필요에 따라 조정할 수 있습니다. 이 수식은 전용 노드를 크기 조정하지만 우선 순위가 낮은 노드를 크기 조정하는 데 적용되도록 수정할 수 있습니다. 

```
startingNumberOfVMs = 1;
maxNumberofVMs = 25;
pendingTaskSamplePercent = $PendingTasks.GetSamplePercent(180 * TimeInterval_Second);
pendingTaskSamples = pendingTaskSamplePercent < 70 ? startingNumberOfVMs : avg($PendingTasks.GetSample(180 * TimeInterval_Second));
$TargetDedicatedNodes=min(maxNumberofVMs, pendingTaskSamples);
```

이 자동 크기 조정 수식을 사용하면 풀은 단일 VM으로 처음에 생성됩니다. `$PendingTasks` 메트릭은 실행 중이거나 큐에 대기 중인 작업의 수를 정의합니다. 수식은 지난 180초 동안 보류 중인 작업의 평균 수를 찾고 이에 따라 `$TargetDedicatedNodes` 변수를 설정합니다. 수식은 전용 노드의 목표 수가 25개 VM을 절대로 초과하지 않도록 합니다. 새 작업이 제출되면 풀은 자동으로 증가합니다. 작업이 완료되면 VM은 하나씩 비워지고 자동 크기 조정 수식은 풀을 축소합니다.

## <a name="variables"></a>variables
자동 크기 조정 수식에는 **서비스 정의** 및 **사용자 정의** 변수를 모두 사용할 수 있습니다. 서비스 정의 변수는 Batch 서비스에 기본 제공되어 있습니다. 서비스 정의 변수 일부는 읽기-쓰기이고, 일부는 읽기 전용입니다. 사용자 정의 변수는 사용자가 정의한 변수입니다. 이전 섹션에 나온 예제 수식에서 `$TargetDedicatedNodes` 및 `$PendingTasks`는 서비스 정의 변수입니다. 변수 `startingNumberOfVMs` 및 `maxNumberofVMs`는 사용자 정의 변수입니다.

> [!NOTE]
> 서비스 정의 변수에는 항상 달러($) 기호가 앞에 붙습니다. 사용자 정의 변수의 경우 달러 기호는 선택 사항입니다.
>
>

아래 표에서는 Batch 서비스에서 정의하는 읽기-쓰기 및 읽기 전용 변수를 모두 보여 줍니다.

다음과 같은 서비스 정의 변수의 값을 가져오고 설정하여 풀의 계산 노드 수를 관리할 수 있습니다.

| 읽기-쓰기 서비스 정의 변수 | 설명 |
| --- | --- |
| $TargetDedicatedNodes |풀에 대한 전용 계산 노드의 대상 수입니다. 풀에서 항상 원하는 수의 노드에 도달할 수 없으므로 전용 노드의 수가 목표 수로 지정됩니다. 예를 들어 풀에서 최초 목표에 도달하기 전에 자동 크기 조정 평가에 따라 전용 노드의 목표 수가 수정되는 경우 풀에서 목표에 도달하지 못할 수 있습니다. <br /><br /> 목표가 배치 계정 노드 또는 코어 할당량을 초과하는 경우 Batch 서비스 구성으로 만든 계정의 풀에서 해당 목표에 도달하지 못할 수 있습니다. 목표가 구독의 공유 코어 할당량을 초과하는 경우 사용자 구독 구성으로 만든 계정의 풀에서 해당 목표에 도달하지 못할 수 있습니다.|
| $TargetLowPriorityNodes |풀에 대한 우선 순위가 낮은 계산 노드의 목표 수입니다. 풀에서 항상 원하는 수의 노드에 도달할 수 없으므로 우선 순위가 낮은 노드의 수가 목표 수로 지정됩니다. 예를 들어 풀에서 최초 목표에 도달하기 전에 자동 크기 조정 평가에 따라 우선 순위가 낮은 노드의 목표 수가 수정되는 경우 풀에서 목표에 도달하지 못할 수 있습니다. 목표가 Batch 계정 노드 또는 코어 할당량을 초과하는 경우 풀에서 해당 목표에 도달하지 못할 수도 있습니다. <br /><br /> 우선 순위가 낮은 계산 노드에 대한 자세한 내용은 [Batch(미리 보기)에서 낮은 우선 순위 VM 사용](batch-low-pri-vms.md)을 참조하세요. |
| $NodeDeallocationOption |풀에서 계산 노드가 제거되는 경우 발생하는 작업입니다. 가능한 값은 다음과 같습니다.<ul><li>**requeue** - 태스크를 즉시 종료하고 일정을 재조정하도록 작업 큐에 다시 배치합니다.<li>**terminate** - 태스크를 즉시 종료하고 작업 큐에서 제거합니다.<li>**taskcompletion** - 현재 실행 중인 태스크가 완료되기를 기다린 다음 풀에서 해당 노드를 제거합니다.<li>**retaineddata** - 노드의 모든 로컬 태스크 보유 데이터가 정리되기를 기다린 다음 풀에서 해당 노드를 제거합니다.</ul> |

이러한 서비스 정의 변수의 값을 가져와서 Batch 서비스의 메트릭을 기반으로 하여 조정할 수 있습니다.

| 읽기 전용 서비스 정의 변수 | 설명 |
| --- | --- |
| $CPUPercent |평균 CPU 사용량 비율. |
| $WallClockSeconds |사용된 시간(초) 수. |
| $MemoryBytes |사용된 평균 메가바이트 수. |
| $DiskBytes |로컬 디스크에서 사용된 평균 기가바이트 수. |
| $DiskReadBytes |읽은 바이트 수. |
| $DiskWriteBytes |쓴 바이트 수. |
| $DiskReadOps |수행된 디스크 읽기 작업 수. |
| $DiskWriteOps |수행된 디스크 쓰기 작업 수. |
| $NetworkInBytes |인바운드 바이트 수. |
| $NetworkOutBytes |아웃바운드 바이트 수. |
| $SampleNodeCount |계산 노드 수. |
| $ActiveTasks |실행할 준비가 되었지만 아직 실행되지 않은 작업 수입니다. $ActiveTasks 수에는 활성 상태에 있고 종속성이 충족된 작업이 모두 포함됩니다. 활성 상태이지만 종속성이 충족되지 않은 작업은 모두 $ActiveTasks 수에서 제외됩니다.|
| $RunningTasks |실행 중 상태인 태스크 수. |
| $PendingTasks |$ActiveTasks 및 $RunningTasks의 합입니다. |
| $SucceededTasks |성공적으로 완료된 태스크 수. |
| $FailedTasks |실패한 태스크 수. |
| $CurrentDedicatedNodes |현재 전용 계산 노드 수. |
| $CurrentLowPriorityNodes |선점된 노드를 포함하여 우선 순위가 낮은 계산 노드의 현재 수입니다. |
| $PreemptedNodeCount | 선점 상태에 있는 풀의 노드 수입니다. |

> [!TIP]
> 앞의 표에서 설명하는 읽기 전용 서비스 정의 변수는 각각에 연결된 데이터에 액세스하는 다양한 메서드를 제공하는 *개체*입니다. 자세한 내용은 이 문서의 뒷부분에 나오는 [샘플 데이터 가져오기](#getsampledata)를 참조하세요.
>
>

## <a name="types"></a>형식
수식에 지원되는 형식은 다음과 같습니다.

* double
* doubleVec
* doubleVecList
* 문자열
* timestamp--타임스탬프는 다음의 멤버를 포함하는 복합 구조입니다.

  * year
  * month (1-12)
  * day (1-31)
  * weekday(숫자 형식, 예: 1은 월요일을 의미함)
  * hour(24시간 형식, 예: 13은 오후 1시를 의미함)
  * minute (00-59)
  * second (00-59)
* timeinterval

  * TimeInterval_Zero
  * TimeInterval_100ns
  * TimeInterval_Microsecond
  * TimeInterval_Millisecond
  * TimeInterval_Second
  * TimeInterval_Minute
  * TimeInterval_Hour
  * TimeInterval_Day
  * TimeInterval_Week
  * TimeInterval_Year

## <a name="operations"></a>작업
이전 섹션에서 나열된 형식에서 허용되는 연산은 다음과 같습니다.

| 작업(Operation) | 지원되는 연산자 | 결과 형식 |
| --- | --- | --- |
| double *operator* double |+, -, *, / |double |
| double *operator* timeinterval |* |timeinterval |
| doubleVec *operator* double |+, -, *, / |doubleVec |
| doubleVec *operator* doubleVec |+, -, *, / |doubleVec |
| timeinterval *operator* double |*, / |timeinterval |
| timeinterval *operator* timeinterval |+, - |timeinterval |
| timeinterval *operator* timestamp |+ |timestamp |
| timestamp *operator* timeinterval |+ |timestamp |
| timestamp *operator* timestamp |- |timeinterval |
| *operator*double |-, ! |double |
| *operator*timeinterval |- |timeinterval |
| double *operator* double |<, <=, ==, >=, >, != |double |
| string *operator* string |<, <=, ==, >=, >, != |double |
| timestamp *operator* timestamp |<, <=, ==, >=, >, != |double |
| timeinterval *operator* timeinterval |<, <=, ==, >=, >, != |double |
| double *operator* double |&&, &#124;&#124; |double |

3항 연산자(`double ? statement1 : statement2`)가 있는 이중 연산자를 테스트할 경우 0이 아님이 **true**이고 0은 **false**입니다.

## <a name="functions"></a>Functions
이러한 미리 정의된 **함수** 는 자동 크기 조정 수식을 정의하는 데 사용할 수 있습니다.

| 함수 | 반환 형식 | 설명 |
| --- | --- | --- |
| avg(doubleVecList) |double |doubleVecList에 있는 모든 값의 평균 값을 반환합니다. |
| len(doubleVecList) |double |doubleVecList에서 만든 벡터의 길이를 반환합니다. |
| lg(double) |double |double의 로그 밑 2를 반환합니다. |
| lg(doubleVecList) |doubleVec |doubleVecList의 구성 요소 로그 밑 2를 반환합니다. vec(double)은 매개 변수에 대해 명시적으로 전달되어야 합니다. 그렇지 않으면 double lg(double) 버전으로 간주됩니다. |
| ln(double) |double |double의 자연 로그를 반환합니다. |
| ln(doubleVecList) |doubleVec |doubleVecList의 구성 요소 로그 밑 2를 반환합니다. vec(double)은 매개 변수에 대해 명시적으로 전달되어야 합니다. 그렇지 않으면 double lg(double) 버전으로 간주됩니다. |
| log(double) |double |double의 로그 밑 10을 반환합니다. |
| log(doubleVecList) |doubleVec |doubleVecList의 구성 요소 로그 밑 10을 반환합니다. vec(double)은 단일 이중 매개 변수에 대해 명시적으로 전달되어야 합니다. 그렇지 않으면 double log(double) 버전으로 간주됩니다. |
| max(doubleVecList) |double |doubleVecList의 최대값을 반환합니다. |
| min(doubleVecList) |double |doubleVecList의 최소값을 반환합니다. |
| norm(doubleVecList) |double |doubleVecList에서 만든 벡터의 두 기준을 반환합니다. |
| percentile(doubleVec v, double p) |double |벡터 v의 백분위수 요소를 반환합니다. |
| rand() |double |0.0에서 1.0 사이의 임의 값을 반환합니다. |
| range(doubleVecList) |double |doubleVecList에 있는 최소값과 최대값 사이의 차이를 반환합니다. |
| std(doubleVecList) |double |doubleVecList에 있는 값의 샘플 표준 편차를 반환합니다. |
| stop() | |자동 크기 조정 식의 평가를 중지합니다. |
| sum(doubleVecList) |double |doubleVecList에 있는 모든 구성 요소의 합계를 반환합니다. |
| time(string dateTime="") | timestamp |매개 변수가 전달되지 않는 경우 현재 시간의 타임스탬프 또는 매개 변수가 전달되는 경우 dateTime 문자열의 타임스탬프를 반환합니다. 지원되는 dateTime 형식은 W3C-DTF 및 RFC 1123입니다. |
| val(doubleVec v, double i) |double |시작 인덱스가 0인 벡터 v의 위치 i 요소 값을 반환합니다. |

앞의 표에서 설명하는 함수 중 일부는 목록을 인수로 허용할 수 있습니다. 쉼표로 구분된 목록은 *double* 및 *doubleVec*의 조합입니다. 예를 들면 다음과 같습니다.

`doubleVecList := ( (double | doubleVec)+(, (double | doubleVec) )* )?`

*doubleVecList* 값은 평가 전 단일 *doubleVec*으로 변환됩니다. 예를 들어 `v = [1,2,3]`의 경우 `avg(v)` 호출은 `avg(1,2,3)` 호출과 동일합니다. `avg(v, 7)` 호출은 `avg(1,2,3,7)` 호출과 동일합니다.

## <a name="getsampledata"></a>샘플 데이터 가져오기
자동 크기 조정 수식은 Batch 서비스에서 제공한 메트릭 데이터(샘플)에서 작동합니다. 수식은 서비스에서 가져온 값에 따라 풀 크기를 늘리거나 줄입니다. 앞에서 설명한 서비스 정의 변수는 해당 개체에 연결된 데이터에 액세스하는 다양한 메서드를 제공하는 개체입니다. 예를 들어, 다음 식은 최근 5분 동안의 CPU 사용률을 얻기 위한 요청을 보여 줍니다.

```
$CPUPercent.GetSample(TimeInterval_Minute * 5)
```

| 방법 | 설명 |
| --- | --- |
| GetSample() |`GetSample()` 메서드는 데이터 샘플의 벡터를 반환합니다.<br/><br/>하나의 샘플은 30초 동안의 메트릭 데이터입니다. 즉 30초마다 샘플을 가져옵니다. 그러나 아래에서 설명하듯이 샘플이 수집된 시간과 해당 샘플을 수식에 사용할 수 있게 되는 시간 사이에 지연이 있습니다. 따라서 지정된 기간 동안 일부 샘플을 수식에 의한 평가에 사용할 수 없을지도 모릅니다.<ul><li>`doubleVec GetSample(double count)`<br/>수집한 최근 샘플에서 가져올 샘플 수를 지정합니다.<br/><br/>`GetSample(1)`은 사용 가능한 마지막 샘플을 반환합니다. 그러나 `$CPUPercent`와 같은 메트릭의 경우 샘플이 수집된 *시기*를 알 수 없기 때문에 이 메서드를 사용해서는 안 됩니다. 최근 샘플일 수도 있지만 시스템 문제로 인해 훨씬 오래된 샘플일 수도 있습니다. 그러한 경우 아래에 표시된 것처럼 시간 간격을 사용하는 것이 좋습니다.<li>`doubleVec GetSample((timestamp or timeinterval) startTime [, double samplePercent])`<br/>샘플 데이터를 수집하기 위한 시간 프레임을 지정합니다. 선택적으로 요청 시간 프레임에 있어야 하는 샘플의 백분율을 지정합니다.<br/><br/>`$CPUPercent.GetSample(TimeInterval_Minute * 10)`은 지난 10분 동안의 모든 샘플이 CPUPercent 기록에 있는 경우 20개 샘플을 반환합니다. 그러나 내역의 마지막 분을 사용할 수 없으면 샘플 18개만 반환될 것입니다. 이 경우 다음과 같습니다.<br/><br/>`$CPUPercent.GetSample(TimeInterval_Minute * 10, 95)`은 샘플의 90%만 사용할 수 있으므로 실패합니다.<br/><br/>`$CPUPercent.GetSample(TimeInterval_Minute * 10, 80)`은 성공합니다.<li>`doubleVec GetSample((timestamp or timeinterval) startTime, (timestamp or timeinterval) endTime [, double samplePercent])`<br/>시작 시간과 종료 시간을 사용하여 데이터를 수집하기 위한 시간 프레임을 지정합니다.<br/><br/>위에서 언급했듯이 샘플이 수집된 시간과 해당 샘플을 수식에 사용할 수 있게 되는 시간 사이에 지연이 있습니다. `GetSample` 메서드를 사용할 때 이 지연을 고려해 보세요. 아래 `GetSamplePercent` 참조 |
| GetSamplePeriod() |기록 샘플 데이터 집합에서 가져온 샘플의 기간을 반환합니다. |
| Count() |메트릭 기록에 있는 총 샘플 수를 반환합니다. |
| HistoryBeginTime() |메트릭에 대해 사용 가능한 가장 오래된 데이터 샘플의 타임스탬프를 반환합니다. |
| GetSamplePercent() |지정된 시간 간격에 사용할 수 있는 샘플의 백분율을 반환합니다. 예를 들면 다음과 같습니다.<br/><br/>`doubleVec GetSamplePercent( (timestamp or timeinterval) startTime [, (timestamp or timeinterval) endTime] )`<br/><br/>반환된 샘플의 백분율이 지정한 `samplePercent`보다 작은 경우 `GetSample` 메서드가 실패하기 때문에 `GetSamplePercent` 메서드를 사용하여 먼저 확인할 수 있습니다. 그런 다음 비효율적인 샘플이 존재하는 경우 자동 크기 조정 평가를 중단하지 않고 대체 작업을 수행할 수 있습니다. |

### <a name="samples-sample-percentage-and-the-getsample-method"></a>샘플, 샘플 비율 및 *GetSample()* 메서드
자동 크기 조정 수식의 핵심 작업은 작업 및 리소스 메트릭 데이터를 가져오고 데이터를 기반으로 풀 크기를 조정하는 것입니다. 따라서 자동 크기 조정 수식이 메트릭 데이터(샘플)과 상호 작용하는 방식을 명확히 이해해야 합니다.

**샘플**

Batch 서비스는 정기적으로 작업 및 리소스 메트릭의 샘플을 가져와 자동 크기 조정 수식에 사용할 수 있도록 합니다. 이러한 샘플은 Batch 서비스에서 30초 마다 기록됩니다. 하지만 일반적으로 이러한 샘플을 기록하는 시간과 자동 크기 조정 수식에서 사용할 수 있을 시간(읽을 수 있는 시간) 사이에 지연이 있습니다. 또한 네트워크 또는 다른 인프라 문제와 같이 다양한 요인으로 인해 샘플이 특정 기간에 기록되지 않을 수 있습니다.

**샘플 비율**

`samplePercent`를 `GetSample()` 메서드에 전달하거나 `GetSamplePercent()` 메서드를 호출하는 경우 _percent_(비율)는 Batch 서비스에서 기록하는 샘플의 가능한 총 수와 자동 크기 조정 수식에서 사용할 수 있는 샘플 수를 비교한 것입니다.

예를 들어 10분 TimeSpan을 살펴보겠습니다. 샘플이 10분 시간 간격 내에서 30초마다 기록되므로 Batch에 서 기록하는 샘플의 최대 총 수는 20개 샘플(분당 2개)입니다. 그러나 보고 메커니즘의 고유한 대기 시간 또는 Azure 내의 다른 문제로 인해 자동 크기 조정 수식에서 읽는 데 사용할 수 있는 샘플은 15개뿐일 수 있습니다. 즉 10분 동안 기록하는 샘플의 총 개수 중 75%만 수식에서 사용할 수 있습니다.

**GetSample() 및 샘플 범위**

자동 크기 조정 수식은 풀을 확장하거나 축소합니다. 즉 노드를 추가하거나 제거합니다. 노드에 비용이 들기 때문에 수식은 충분한 데이터를 기반으로 지능형 분석 메서드를 사용해야 합니다. 따라서 수식에서 추세 형식 분석을 사용하는 것이 좋습니다. 이 형식은 수집된 샘플의 범위에 따라 풀을 확장하고 축소합니다.

이렇게 하려면 `GetSample(interval look-back start, interval look-back end)`를 사용하여 샘플의 벡터를 반환합니다.

```
$runningTasksSample = $RunningTasks.GetSample(1 * TimeInterval_Minute, 6 * TimeInterval_Minute);
```

위의 줄이 Batch에 의해 확인된 경우 다양한 샘플을 값의 벡터로 반환합니다. 예를 들면 다음과 같습니다.

```
$runningTasksSample=[1,1,1,1,1,1,1,1,1,1];
```

샘플의 벡터를 수집했으면 `min()`, `max()` 및 `avg()`과 같은 함수를 사용하여 수집된 범위에서 의미 있는 값을 파생할 수 있습니다.

추가 보안을 위해 특정 샘플 비율 미만을 특정 기간 동안 사용할 수 있는 경우 수식 평가가 실패하도록 강제할 수 있습니다. 수식 평가가 실패하도록 강제하면 지정된 샘플 비율을 사용할 수 없는 경우 Batch에 수식의 추가 평가를 중단하도록 지시합니다. 이 경우 풀 크기가 변경되지 않습니다. 평가가 성공하기 위해 샘플의 필요한 백분율을 지정하려면 `GetSample()`에 대한 세 번째 매개 변수로 지정합니다. 여기에서는 샘플의 75% 요구 사항이 지정됩니다.

```
$runningTasksSample = $RunningTasks.GetSample(60 * TimeInterval_Second, 120 * TimeInterval_Second, 75);
```

또한 샘플 가용성에 지연 시간이 있을 수 있으므로 시간 범위를 1분 이상인 돌아보기 시작 시간으로 지정하는 것이 중요합니다. 샘플이 시스템을 통해 전파되는 데 약 1분 정도가 걸리므로 `(0 * TimeInterval_Second, 60 * TimeInterval_Second)` 범위에 있는 샘플은 사용하지 못할 수도 있습니다. 다시 `GetSample()`라는 백분율 매개 변수를 사용하여 특정 샘플 비율 요구 사항을 강제할 수 있습니다.

> [!IMPORTANT]
> **자동 크기 조정 수식의 `GetSample(1)`*만* 신뢰하지 않는** 것이 **좋습니다**. `GetSample(1)`은 기본적으로 Batch 서비스에 “경과한 시간에 관계 없이 마지막 샘플을 제공하도록” 요구하기 때문입니다. 이 샘플은 단일 샘플이고 오래된 샘플이기 때문에 최근 작업 또는 리소스 상태의 큰 그림을 나타내지 않을 수 있습니다. `GetSample(1)`을 사용하는 경우 큰 문의 일부이며 수식이 사용하는 유일한 데이터 지점이 아니도록 주의합니다.
>
>

## <a name="metrics"></a>메트릭
수식을 정의할 때 리소스 및 작업 메트릭을 모두 사용할 수 있습니다. 가져오고 평가한 메트릭 데이터를 기반으로 하는 풀의 전용 노드 대상 수를 조정합니다. 각 메트릭에 대한 자세한 내용은 위의 [변수](#variables) 섹션을 참조하세요.

<table>
  <tr>
    <th>메트릭</th>
    <th>설명</th>
  </tr>
  <tr>
    <td><b>리소스</b></td>
    <td><p>리소스 메트릭은 CPU, 대역폭, 계산 노드의 메모리 사용량 및 노드 수를 기반으로 합니다.</p>
        <p> 이러한 서비스 정의 변수는 노드 수에 기반하여 조정하는 데 유용합니다.</p>
    <p><ul>
            <li>$TargetDedicatedNodes</li>
            <li>$TargetLowPriorityNodes</li>
            <li>$CurrentDedicatedNodes</li>
            <li>$CurrentLowPriorityNodes</li>
            <li>$preemptedNodeCount</li>
            <li>$SampleNodeCount</li>
    </ul></p>
    <p>이러한 서비스 정의 변수는 노드 리소스 사용량에 기반하여 조정하는 데 유용합니다.</p>
    <p><ul>
      <li>$CPUPercent</li>
      <li>$WallClockSeconds</li>
      <li>$MemoryBytes</li>
      <li>$DiskBytes</li>
      <li>$DiskReadBytes</li>
      <li>$DiskWriteBytes</li>
      <li>$DiskReadOps</li>
      <li>$DiskWriteOps</li>
      <li>$NetworkInBytes</li>
      <li>$NetworkOutBytes</li></ul></p>
  </tr>
  <tr>
    <td><b>Task</b></td>
    <td><p>작업 메트릭은 활성, 보류 중 및 완료됨과 같은 작업 상태를 기반으로 합니다. 다음 서비스 정의 변수는 노드 작업 메트릭에 기반하여 풀 크기를 조정하는 데 유용합니다.</p>
    <p><ul>
      <li>$ActiveTasks</li>
      <li>$RunningTasks</li>
      <li>$PendingTasks</li>
      <li>$SucceededTasks</li>
            <li>$FailedTasks</li></ul></p>
        </td>
  </tr>
</table>

## <a name="write-an-autoscale-formula"></a>자동 크기 조정 수식 작성
위의 구성 요소를 사용하는 구문을 구성하여 자동 크기 조정 수식을 작성한 다음 해당 구문을 완전한 수식으로 결합합니다. 이 섹션에서는 몇 가지 실제 크기 조정을 결정할 수 있는 자동 크기 조정 수식 예제를 만듭니다.

먼저 새 자동 크기 조정 수식에 대한 요구 사항을 정의합니다. 수식은 다음과 같아야 합니다.

1. CPU 사용량이 많은 경우 풀에 있는 전용 계산 노드의 목표 수를 늘립니다.
2. CPU 사용량이 적은 경우 풀에 있는 전용 계산 노드의 목표 수를 줄입니다.
3. 전용 노드의 최대 수는 항상 400으로 제한합니다.

CPU 사용량이 많은 동안 노드 수를 늘리려면, 지난 10분 동안 CPU 최소 평균 사용량이 70%를 초과한 경우에만 사용자 정의 변수(`$totalDedicatedNodes`)를 전용 노드의 현재 목표 수의 110%인 값으로 채우는 문을 정의합니다. 그렇지 않으면 전용 노드의 현재 수에 대한 값을 사용합니다.

```
$totalDedicatedNodes =
    (min($CPUPercent.GetSample(TimeInterval_Minute * 10)) > 0.7) ?
    ($CurrentDedicatedNodes * 1.1) : $CurrentDedicatedNodes;
```

CPU 사용량이 많은 동안 노드 수를 *줄이려면*, 지난 60분 동안 CPU 평균 사용량이 20% 미만인 경우 수식의 다음 문에서 동일한 `$totalDedicatedNodes` 변수를 전용 노드의 현재 목표 수의 90%로 설정합니다. 그렇지 않으면 위 구문에 채워진 `$totalDedicatedNodes`의 현재 값을 사용합니다.

```
$totalDedicatedNodes =
    (avg($CPUPercent.GetSample(TimeInterval_Minute * 60)) < 0.2) ?
    ($CurrentDedicatedNodes * 0.9) : $totalDedicatedNodes;
```

이제 전용 계산 노드의 대상 수를 최대 400으로 제한합니다.

```
$TargetDedicatedNodes = min(400, $totalDedicatedNodes)
```

완성된 수식은 다음과 같습니다.

```
$totalDedicatedNodes =
    (min($CPUPercent.GetSample(TimeInterval_Minute * 10)) > 0.7) ?
    ($CurrentDedicatedNodes * 1.1) : $CurrentDedicatedNodes;
$totalDedicatedNodes =
    (avg($CPUPercent.GetSample(TimeInterval_Minute * 60)) < 0.2) ?
    ($CurrentDedicatedNodes * 0.9) : $totalDedicatedNodes;
$TargetDedicatedNodes = min(400, $totalDedicatedNodes)
```

## <a name="create-an-autoscale-enabled-pool-with-net"></a>.NET으로 자동 크기 조정 가능한 풀 만들기

.NET에서 자동 크기 조정 가능한 풀을 만들려면 다음 단계를 수행합니다.

1. [BatchClient.PoolOperations.CreatePool](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.pooloperations.createpool)을 사용하여 풀을 만듭니다.
2. [CloudPool.AutoScaleEnabled](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleenabled) 속성을 `true`로 설정합니다.
3. 자동 크기 조정 수식을 사용하여 [CloudPool.AutoScaleFormula](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleformula) 속성을 설정합니다.
4. (선택 사항) [CloudPool.AutoScaleEvaluationInterval](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleevaluationinterval) 속성을 설정합니다(기본값: 15 분).
5. [CloudPool.Commit](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.commit) 또는 [CommitAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.commitasync)로 풀을 커밋합니다.

다음 코드 조각에서는 .NET에서 자동 크기 조정 가능한 풀을 만듭니다. 풀의 자동 크기 조정 수식에서 전용 노드의 목표 수를 월요일에는 5로, 그 외 다른 요일에는 1로 설정합니다. [자동 크기 조정 간격](#automatic-scaling-interval)은 30분으로 설정됩니다. 이 코드 조각과 이 문서의 다른 C# 코드 조각에서 `myBatchClient`는 [BatchClient][net_batchclient] 클래스의 올바르게 초기화된 인스턴스입니다.

```csharp
CloudPool pool = myBatchClient.PoolOperations.CreatePool(
                    poolId: "mypool",
                    virtualMachineSize: "standard_d1_v2",
                    cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "5"));    
pool.AutoScaleEnabled = true;
pool.AutoScaleFormula = "$TargetDedicatedNodes = (time().weekday == 1 ? 5:1);";
pool.AutoScaleEvaluationInterval = TimeSpan.FromMinutes(30);
await pool.CommitAsync();
```

> [!IMPORTANT]
> 자동 크기 조정 가능한 풀을 만들 때는 **CreatePool**에 대한 호출에서 _targetDedicatedNodes_ 매개 변수 또는 _targetLowPriorityNodes_ 매개 변수를 지정하지 마세요. 대신 풀에 **AutoScaleEnabled** 및 **AutoScaleFormula** 속성을 지정합니다. 이러한 속성의 값은 각 노드 형식의 목표 수를 결정합니다. 또한 자동 크기 조정 가능한 풀의 크기를 수동으로 조정하려는 경우(예: [BatchClient.PoolOperations.ResizePoolAsync][net_poolops_resizepoolasync] 사용) 먼저 풀에서 자동 크기 조정을 **사용하지 않도록** 설정한 다음 풀의 크기를 조정합니다.
>
>

Batch .NET 외에도 다른 [Batch SDK](batch-apis-tools.md#azure-accounts-for-batch-development), [Batch REST](https://docs.microsoft.com/rest/api/batchservice/), [Batch PowerShell cmdlet](batch-powershell-cmdlets-get-started.md) 및 [Batch CLI](batch-cli-get-started.md) 중 하나를 사용하여 자동 크기 조정을 구성할 수 있습니다.


### <a name="automatic-scaling-interval"></a>자동 크기 조정 간격
기본적으로 Batch 서비스는 15분마다 자동 크기 조정 수식에 따라 풀의 크기를 조정합니다. 이 간격은 다음 풀 속성을 사용하여 구성할 수 있습니다.

* [CloudPool.AutoScaleEvaluationInterval][net_cloudpool_autoscaleevalinterval] (Batch .NET)
* [autoScaleEvaluationInterval][rest_autoscaleinterval] (REST API)

최소 간격은 5분이고 최대 간격은 168시간입니다. 이 범위를 벗어나는 간격을 지정하면 Batch 서비스에서 잘못된 요청(400) 오류를 반환합니다.

> [!NOTE]
> 자동 크기 조정은 현재 1분 미만의 변경 내용에 응답하지 않지만 워크로드를 실행하면 점차적으로 풀의 크기를 조정합니다.
>
>

## <a name="enable-autoscaling-on-an-existing-pool"></a>기존 풀에서 자동 크기 조정 사용

Batch SDK마다 자동 크기 조정을 사용하도록 설정하는 방법을 제공합니다. 예를 들면 다음과 같습니다.

* [BatchClient.PoolOperations.EnableAutoScaleAsync][net_enableautoscaleasync](Batch .NET)
* [풀에서 자동 크기 조정 사용][rest_enableautoscale] (REST API)

기존 풀에서 자동 크기 조정을 사용하도록 설정하는 경우 다음 사항에 유의하세요.

* 자동 크기 조정을 사용하기 위한 요청을 발급할 때 현재 풀에서 자동 크기 조정을 사용하지 않도록 설정되어 있으면 이 요청을 발급할 때 유효한 자동 크기 조정 수식을 지정해야 합니다. 필요에 따라 자동 크기 조정 평가 간격을 지정할 수 있습니다. 간격을 지정하지 않으면 기본값인 15분이 사용됩니다.
* 현재 풀에서 자동 크기 조정을 사용할 수 있는 경우 자동 크기 조정 수식, 평가 간격 또는 둘 다를 지정할 수 있습니다. 이러한 속성 중 하나 이상을 지정해야 합니다.

  * 새로운 자동 크기 조정 간격을 지정하면 기존 평가 일정이 중지되고 새 일정이 시작됩니다. 새 일정의 시작 시간은 자동 크기 조정을 사용하기 위한 요청이 발급된 시간입니다.
  * 자동 크기 조정 수식 또는 평가 간격을 생략하면 Batch 서비스에서 해당 설정의 현재 값을 계속 사용합니다.

> [!NOTE]
> .NET에서 풀을 만들 때 **CreatePool** 메서드의 *targetDedicatedNodes* 또는 *targetLowPriorityNodes* 매개 변수에 대한 값을 지정했거나 다른 언어의 비교 가능한 매개 변수에 대한 값을 지정한 경우, 자동 크기 조정 수식을 평가할 때 해당 값이 무시됩니다.
>
>

이 C# 코드 조각에서 다음과 같이 [Batch .NET][net_api] 라이브러리를 사용하여 기존 풀에서 자동 크기 조정을 사용하도록 설정합니다.

```csharp
// Define the autoscaling formula. This formula sets the target number of nodes
// to 5 on Mondays, and 1 on every other day of the week
string myAutoScaleFormula = "$TargetDedicatedNodes = (time().weekday == 1 ? 5:1);";

// Set the autoscale formula on the existing pool
await myBatchClient.PoolOperations.EnableAutoScaleAsync(
    "myexistingpool",
    autoscaleFormula: myAutoScaleFormula);
```

### <a name="update-an-autoscale-formula"></a>자동 크기 조정 수식 업데이트

기존의 자동 크기 조정 가능한 풀에서 수식을 업데이트하려면 새 수식을 사용하여 자동 크기 조정을 사용하도록 다시 설정하는 작업을 호출합니다. 예를 들어 다음 .NET 코드가 실행될 때 `myexistingpool`에서 자동 크기 조정을 사용하도록 이미 설정되어 있으면 자동 크기 조정 수식이 `myNewFormula`의 내용으로 바뀝니다.

```csharp
await myBatchClient.PoolOperations.EnableAutoScaleAsync(
    "myexistingpool",
    autoscaleFormula: myNewFormula);
```

### <a name="update-the-autoscale-interval"></a>자동 크기 조정 간격 업데이트

기존의 자동 크기 조정 가능한 풀의 자동 크기 조정 평가 간격을 업데이트하려면 새 간격으로 자동 크기 조정을 사용하도록 다시 설정하는 작업을 호출합니다. 예를 들어 .NET에서 이미 자동 크기 조정 가능한 풀에 대해 자동 크기 조정 평가 간격을 60분으로 설정하려면 다음을 수행합니다.

```csharp
await myBatchClient.PoolOperations.EnableAutoScaleAsync(
    "myexistingpool",
    autoscaleEvaluationInterval: TimeSpan.FromMinutes(60));
```

## <a name="evaluate-an-autoscale-formula"></a>자동 크기 조정 수식 평가

수식은 풀에 적용하기 전에 평가할 수 있습니다. 이러한 방법으로 수식을 테스트하여 프로덕션 환경에 해당 수식을 배포하기 전에 문이 평가되는 방식을 확인할 수 있습니다.

자동 크기 조정 수식을 평가하려면 먼저 유효한 수식을 사용하여 풀에서 자동 크기 조정을 사용하도록 설정해야 합니다. 아직 자동 크기 조정을 사용할 수 없는 풀에서 수식을 테스트하려면 자동 크기 조정을 처음 사용할 때 한 줄로 구성된 `$TargetDedicatedNodes = 0` 수식을 사용합니다. 그런 후에 다음 중 하나를 사용하여 테스트할 수식을 평가합니다.

* [BatchClient.PoolOperations.EvaluateAutoScale](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.pooloperations.evaluateautoscale) 또는 [EvaluateAutoScaleAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.pooloperations.evaluateautoscaleasync)

    이러한 Batch .NET 메서드를 사용하려면 기존 풀의 ID와 평가할 자동 크기 조정 수식이 포함된 문자열이 필요합니다.

* [자동 크기 조정 수식 평가](https://docs.microsoft.com/rest/api/batchservice/evaluate-an-automatic-scaling-formula)

    이 REST API 요청에서 풀 ID는 URI에 지정하고, 자동 크기 조정 수식은 요청 본문의 *autoScaleFormula* 요소에 지정합니다. 작업 응답에는 수식과 관련이 있을 수 있는 오류 정보가 포함됩니다.

이 [Batch .NET][net_api] 코드 조각에서는 자동 크기 조정 수식을 평가합니다. 풀에서 자동 크기 조정을 사용할 수 없으면 먼저 풀에서 이 기능을 사용할 수 있도록 설정해야 합니다.

```csharp
// First obtain a reference to an existing pool
CloudPool pool = await batchClient.PoolOperations.GetPoolAsync("myExistingPool");

// If autoscaling isn't already enabled on the pool, enable it.
// You can't evaluate an autoscale formula on non-autoscale-enabled pool.
if (pool.AutoScaleEnabled == false)
{
    // We need a valid autoscale formula to enable autoscaling on the
    // pool. This formula is valid, but won't resize the pool:
    await pool.EnableAutoScaleAsync(
        autoscaleFormula: "$TargetDedicatedNodes = $CurrentDedicatedNodes;",
        autoscaleEvaluationInterval: TimeSpan.FromMinutes(5));

    // Batch limits EnableAutoScaleAsync calls to once every 30 seconds.
    // Because we want to apply our new autoscale formula below if it
    // evaluates successfully, and we *just* enabled autoscaling on
    // this pool, we pause here to ensure we pass that threshold.
    Thread.Sleep(TimeSpan.FromSeconds(31));

    // Refresh the properties of the pool so that we've got the
    // latest value for AutoScaleEnabled
    await pool.RefreshAsync();
}

// We must ensure that autoscaling is enabled on the pool prior to
// evaluating a formula
if (pool.AutoScaleEnabled == true)
{
    // The formula to evaluate - adjusts target number of nodes based on
    // day of week and time of day
    string myFormula = @"
        $curTime = time();
        $workHours = $curTime.hour >= 8 && $curTime.hour < 18;
        $isWeekday = $curTime.weekday >= 1 && $curTime.weekday <= 5;
        $isWorkingWeekdayHour = $workHours && $isWeekday;
        $TargetDedicatedNodes = $isWorkingWeekdayHour ? 20:10;
    ";

    // Perform the autoscale formula evaluation. Note that this code does not
    // actually apply the formula to the pool.
    AutoScaleRun eval =
        await batchClient.PoolOperations.EvaluateAutoScaleAsync(pool.Id, myFormula);

    if (eval.Error == null)
    {
        // Evaluation success - print the results of the AutoScaleRun.
        // This will display the values of each variable as evaluated by the
        // autoscale formula.
        Console.WriteLine("AutoScaleRun.Results: " +
            eval.Results.Replace("$", "\n    $"));

        // Apply the formula to the pool since it evaluated successfully
        await batchClient.PoolOperations.EnableAutoScaleAsync(pool.Id, myFormula);
    }
    else
    {
        // Evaluation failed, output the message associated with the error
        Console.WriteLine("AutoScaleRun.Error.Message: " +
            eval.Error.Message);
    }
}
```

이 코드 조각에 표시된 수식을 성공적으로 평가하면 다음과 비슷한 결과가 생성됩니다.

```
AutoScaleRun.Results:
    $TargetDedicatedNodes=10;
    $NodeDeallocationOption=requeue;
    $curTime=2016-10-13T19:18:47.805Z;
    $isWeekday=1;
    $isWorkingWeekdayHour=0;
    $workHours=0
```

## <a name="get-information-about-autoscale-runs"></a>자동 크기 조정 실행에 대한 정보 가져오기

수식이 예상대로 수행되는지 확인하려면 풀에서 자동 크기 조정을 실행한 결과를 Batch에서 정기적으로 확인하는 것이 좋습니다. 이렇게 하려면 풀에 대한 참조를 가져오고(또는 새로 고침) 마지막 자동 크기 조정 실행의 속성을 검사합니다.

Batch .NET에서 [CloudPool.AutoScaleRun](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.autoscalerun) 속성에는 풀에서 수행된 마지막 자동 크기 조정 실행에 대한 정보를 제공하는 몇 가지 속성이 있습니다.

* [AutoScaleRun.Timestamp](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.autoscalerun.timestamp)
* [AutoScaleRun.Results](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.autoscalerun.results)
* [AutoScaleRun.Error](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.autoscalerun.error)

REST API에서 [풀에 대한 정보 가져오기](https://docs.microsoft.com/rest/api/batchservice/get-information-about-a-pool) 요청은 [autoScaleRun](https://docs.microsoft.com/rest/api/batchservice/get-information-about-a-pool) 속성에 마지막 자동 크기 조정 실행 정보가 포함된 풀 관련 정보를 반환합니다.

다음 C# 코드 조각에서는 Batch .NET 라이브러리를 사용하여 _myPool_ 풀에서 마지막으로 실행된 자동 크기 조정에 대한 정보를 출력합니다.

```csharp
await Cloud pool = myBatchClient.PoolOperations.GetPoolAsync("myPool");
Console.WriteLine("Last execution: " + pool.AutoScaleRun.Timestamp);
Console.WriteLine("Result:" + pool.AutoScaleRun.Results.Replace("$", "\n  $"));
Console.WriteLine("Error: " + pool.AutoScaleRun.Error);
```

앞에서 언급한 코드 조각의 샘플 출력은 다음과 같습니다.

```
Last execution: 10/14/2016 18:36:43
Result:
  $TargetDedicatedNodes=10;
  $NodeDeallocationOption=requeue;
  $curTime=2016-10-14T18:36:43.282Z;
  $isWeekday=1;
  $isWorkingWeekdayHour=0;
  $workHours=0
Error:
```

## <a name="example-autoscale-formulas"></a>자동 크기 조정 수식 예제
풀의 계산 리소스 양을 조정하는 여러 가지 방법을 보여 주는 몇 가지 수식을 살펴보겠습니다.

### <a name="example-1-time-based-adjustment"></a>예제 1: 시간 기반 조정
요일과 시간에 따라 풀 크기를 조정한다고 가정합니다. 이 예제에서는 풀의 노드 수를 적절히 늘리거나 줄이는 방법을 보여 줍니다.

먼저 수식에서 현재 시간을 가져옵니다. 평일(1-5)에 근무 시간(오전 8시-오후 6시)인 경우, 대상 풀 크기는 20개의 노드로 설정됩니다. 그렇지 않으면 10개 노드로 설정됩니다.

```
$curTime = time();
$workHours = $curTime.hour >= 8 && $curTime.hour < 18;
$isWeekday = $curTime.weekday >= 1 && $curTime.weekday <= 5;
$isWorkingWeekdayHour = $workHours && $isWeekday;
$TargetDedicatedNodes = $isWorkingWeekdayHour ? 20:10;
```

### <a name="example-2-task-based-adjustment"></a>예 2: 작업 기반 조정
이 예에서는 풀 크기는 큐에 있는 작업의 수에 따라 조정 됩니다. 주석과 줄 바꿈은 모두 수식 문자열에 허용됩니다.

```csharp
// Get pending tasks for the past 15 minutes.
$samples = $ActiveTasks.GetSamplePercent(TimeInterval_Minute * 15);
// If we have fewer than 70 percent data points, we use the last sample point,
// otherwise we use the maximum of last sample point and the history average.
$tasks = $samples < 70 ? max(0,$ActiveTasks.GetSample(1)) : max( $ActiveTasks.GetSample(1), avg($ActiveTasks.GetSample(TimeInterval_Minute * 15)));
// If number of pending tasks is not 0, set targetVM to pending tasks, otherwise
// half of current dedicated.
$targetVMs = $tasks > 0? $tasks:max(0, $TargetDedicatedNodes/2);
// The pool size is capped at 20, if target VM value is more than that, set it
// to 20. This value should be adjusted according to your use case.
$TargetDedicatedNodes = max(0, min($targetVMs, 20));
// Set node deallocation mode - keep nodes active only until tasks finish
$NodeDeallocationOption = taskcompletion;
```

### <a name="example-3-accounting-for-parallel-tasks"></a>예 3: 병렬 작업용 계정
이 예제에서는 작업의 수에 따라 풀 크기를 조정합니다. 이 수식은 또한 풀에 대해 설정된 [MaxTasksPerComputeNode][net_maxtasks] 값을 고려합니다. 이 방법은 [병렬 작업 실행](batch-parallel-node-tasks.md)이 풀에서 사용된 경우에 특히 유용합니다.

```csharp
// Determine whether 70 percent of the samples have been recorded in the past
// 15 minutes; if not, use last sample
$samples = $ActiveTasks.GetSamplePercent(TimeInterval_Minute * 15);
$tasks = $samples < 70 ? max(0,$ActiveTasks.GetSample(1)) : max( $ActiveTasks.GetSample(1),avg($ActiveTasks.GetSample(TimeInterval_Minute * 15)));
// Set the number of nodes to add to one-fourth the number of active tasks (the
// MaxTasksPerComputeNode property on this pool is set to 4, adjust this number
// for your use case)
$cores = $TargetDedicatedNodes * 4;
$extraVMs = (($tasks - $cores) + 3) / 4;
$targetVMs = ($TargetDedicatedNodes + $extraVMs);
// Attempt to grow the number of compute nodes to match the number of active
// tasks, with a maximum of 3
$TargetDedicatedNodes = max(0,min($targetVMs,3));
// Keep the nodes active until the tasks finish
$NodeDeallocationOption = taskcompletion;
```

### <a name="example-4-setting-an-initial-pool-size"></a>예제 4: 초기 풀 크기 설정
이 예제에서는 초기 기간 동안 풀 크기를 지정된 노드 수로 설정하는 자동 크기 조정 수식이 있는 C# 코드 조각을 보여 줍니다. 그런 다음 초기 기간이 경과한 후 실행 중이고 활성화된 작업 수를 기반으로 풀 크기를 조정합니다.

다음 코드 조각의 수식은 다음과 같습니다.

* 초기 풀 크기를 4 노드로 설정합니다.
* 풀의 수명 주기의 처음 10분 이내에는 풀 크기를 조정하지 않습니다.
* 10분 후 지난 60분 이내에 실행 중이고 활성화된 작업 수의 최대값을 가져옵니다.
  * 두 값이 모두 0이면(마지막 60분 동안 실행 중이거나 활성화된 작업이 없었음을 나타냄) 풀 크기가 0입니다.
  * 값 중 하나가 0보다 큰 경우 변경되지 않습니다.

```csharp
string now = DateTime.UtcNow.ToString("r");
string formula = string.Format(@"
    $TargetDedicatedNodes = {1};
    lifespan         = time() - time(""{0}"");
    span             = TimeInterval_Minute * 60;
    startup          = TimeInterval_Minute * 10;
    ratio            = 50;

    $TargetDedicatedNodes = (lifespan > startup ? (max($RunningTasks.GetSample(span, ratio), $ActiveTasks.GetSample(span, ratio)) == 0 ? 0 : $TargetDedicatedNodes) : {1});
    ", now, 4);
```

## <a name="next-steps"></a>다음 단계
* [동시 노드 작업으로 Azure Batch 계산 리소스 사용 극대화](batch-parallel-node-tasks.md)는 풀의 계산 노드에서 여러 작업을 동시에 실행할 수 있는 방법을 자세히 설명합니다. 자동 크기 조정 외에도 이 기능은 일부 워크로드에 대한 작업 기간을 줄여서 비용을 절약하는 데 도움이 될 수 있습니다.
* 다른 효율성 부스터의 경우 Batch 애플리케이션이 Batch 서비스를 최적화하여 쿼리하도록 합니다. 잠재적으로 수천 개의 계산 노드 또는 작업의 상태를 쿼리할 때 네트워크를 교차하는 데이터의 양을 제한하는 방법을 알아보려면 [효율적인 Azure Batch 서비스 쿼리](batch-efficient-list-queries.md)를 참조하세요.

[net_api]: https://docs.microsoft.com/dotnet/api/microsoft.azure.batch
[net_batchclient]: https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.batchclient
[net_cloudpool_autoscaleformula]: https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleformula
[net_cloudpool_autoscaleevalinterval]: https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleevaluationinterval
[net_enableautoscaleasync]: https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.pooloperations.enableautoscaleasync
[net_maxtasks]: https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.maxtaskspercomputenode
[net_poolops_resizepoolasync]: https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.pooloperations.resizepoolasync

[rest_api]: https://docs.microsoft.com/rest/api/batchservice/
[rest_autoscaleformula]: https://docs.microsoft.com/rest/api/batchservice/enable-automatic-scaling-on-a-pool
[rest_autoscaleinterval]: https://docs.microsoft.com/rest/api/batchservice/enable-automatic-scaling-on-a-pool
[rest_enableautoscale]: https://docs.microsoft.com/rest/api/batchservice/enable-automatic-scaling-on-a-pool
