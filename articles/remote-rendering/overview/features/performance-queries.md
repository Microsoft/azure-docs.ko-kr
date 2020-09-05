---
title: 서버 쪽 성능 쿼리
description: API 호출을 통해 서버 쪽 성능 쿼리를 수행하는 방법
author: florianborn71
ms.author: flborn
ms.date: 02/10/2020
ms.topic: article
ms.custom: devx-track-csharp
ms.openlocfilehash: c7212157bb7ef541ac1eb1753f46ea6ad434c2ca
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89013370"
---
# <a name="server-side-performance-queries"></a>서버 쪽 성능 쿼리

안정적인 프레임 속도와 올바른 사용자 환경을 위해서는 서버 렌더링 성능이 매우 중요합니다. 서버에서 성능 특성을 신중하게 모니터링하고 필요에 따라 최적화하는 것이 중요합니다. 성능 데이터는 전용 API 함수를 통해 쿼리할 수 있습니다.

렌더링 성능에 가장 많은 영향을 주는 것은 모델 입력 데이터입니다. 입력 데이터는 [모델 변환 구성](../../how-tos/conversion/configure-model-conversion.md)에 설명된 대로 조정할 수 있습니다.

클라이언트 쪽 애플리케이션 성능도 병목 지점이 될 수 있습니다. 클라이언트 쪽 성능에 대 한 심층 분석을 수행 하는 것이 좋습니다 [:::no-loc text="performance trace":::](../../how-tos/performance-tracing.md) .

## <a name="clientserver-timeline"></a>클라이언트/서버 타임라인

여러 가지 대기 시간 값에 대해 자세히 알아보기 전에 클라이언트와 서버 사이의 동기화 지점을 타임라인 측면에서 살펴보는 것이 유용합니다.

![파이프라인 타임라인](./media/server-client-timeline.png)

이 그림은 다음과 같은 방법을 보여줍니다.

* 60Hz의 일정한 프레임 속도(16.6밀리초 간격)로 클라이언트에서 *포즈 예상*이 시작됩니다.
* 그런 후 포즈를 기반으로 서버가 렌더링을 시작합니다.
* 서버가 인코딩된 비디오 이미지를 다시 전송합니다.
* 클라이언트가 이미지를 디코딩하고, 그 위에서 몇 가지 CPU 및 GPU 작업을 수행하고, 이미지를 표시합니다.

## <a name="frame-statistics-queries"></a>프레임 통계 쿼리

프레임 통계는 대기 시간과 같은 마지막 프레임에 대한 상위 수준의 정보를 제공합니다. `FrameStatistics` 구조체에 제공된 데이터는 클라이언트 쪽에서 측정되므로, API가 동기화 호출입니다.

```cs
void QueryFrameData(AzureSession session)
{
    FrameStatistics frameStatistics;
    if (session.GraphicsBinding.GetLastFrameStatistics(out frameStatistics) == Result.Success)
    {
        // do something with the result
    }
}
```

```cpp
void QueryFrameData(ApiHandle<AzureSession> session)
{
    FrameStatistics frameStatistics;
    if (*session->GetGraphicsBinding()->GetLastFrameStatistics(&frameStatistics) == Result::Success)
    {
        // do something with the result
    }
}
```

검색된 `FrameStatistics` 개체에는 다음 멤버가 포함됩니다.

| 멤버 | 설명 |
|:-|:-|
| latencyPoseToReceive | 이 포즈의 서버 프레임이 클라이언트 애플리케이션에 완전히 제공될 때까지 클라이언트 디바이스에서의 클라이언트 카메라 포즈 예상에 걸리는 대기 시간입니다. 이 값에는 네트워크 왕복, 서버 렌더링 시간, 비디오 디코딩 및 지터 보정이 포함됩니다. 위 그림에서 **간격 1**을 참조하세요.|
| latencyReceiveToPresent | 수신된 원격 프레임이 제공된 시점으로부터 클라이언트 앱이 CPU에서 PresentFrame을 호출할 때까지 대기 시간입니다. |
| latencyPresentToDisplay  | CPU에 프레임을 제공한 시점으로부터 디스플레이가 켜질 때까지 대기 시간입니다. 이 값에는 클라이언트 GPU 시간, OS에서 수행되는 모든 프레임 버퍼링, 하드웨어 다시 프로젝션 및 디바이스별 디스플레이 스캔 아웃 시간이 포함됩니다. 위 그림에서 **간격 2**를 참조하세요.|
| timeSinceLastPresent | CPU에서 PresentFrame 후속 호출 사이의 시간입니다. 표시 기간보다 큰 값(예: 60Hz 클라이언트 디바이스에서 16.6밀리초)은 해당 CPU 워크로드가 시간 내에 완료되지 않아 클라이언트 애플리케이션에서 문제가 발생했음을 나타냅니다. 위 그림에서 **간격 3**을 참조하세요.|
| videoFramesReceived | 마지막 1초 동안 서버에서 수신된 프레임 수입니다. |
| videoFrameReusedCount | 디바이스에서 두 번 이상 사용되었고 마지막 1초 동안 수신된 프레임 수입니다. 0이 아닌 값은 네트워크 지터 또는 과도한 서버 렌더링 시간으로 인해 프레임을 다시 사용하고 다시 프로젝션해야 했음을 나타냅니다. |
| videoFramesSkipped | 디코딩되었지만 새로운 프레임이 도착하여 디스플레이에 표시되지 않은 마지막 1초 동안 수신된 프레임 수입니다. 0이 아닌 값은 네트워크 지터링으로 인해 여러 프레임이 표시된 후 클라이언트 디바이스에 갑자기 한번에 도착했음을 나타냅니다. |
| videoFramesDiscarded | **videoFramesSkipped**와 매우 비슷하지만 프레임이 버려지는 이유는 프레임이 너무 늦게 도착해서 보류 중인 포즈와 더 이상 상관 관계로 연결될 수 없기 때문입니다. 이 경우 심각한 네트워크 경합이 발생합니다.|
| videoFrameMinDelta | 마지막 1초 동안 도착하는 연속된 프레임 2개 사이의 최소 시간입니다. 이 범위는 videoFrameMaxDelta와 함께 네트워크 또는 비디오 코덱에 의해 발생한 지터를 표시합니다. |
| videoFrameMaxDelta | 마지막 1초 동안 도착하는 연속된 프레임 2개 사이의 최대 시간입니다. 이 범위는 videoFrameMinDelta와 함께 네트워크 또는 비디오 코덱에 의해 발생한 지터를 표시합니다. |

모든 대기 시간 값의 합계는 60Hz에서 일반적으로 사용 가능한 프레임 시간보다 훨씬 큽니다. 그림에 표시된 것처럼 여러 프레임이 병렬로 처리되고 새 프레임 요청이 원하는 프레임 속도로 시작되기 때문에 이것은 문제가 되지 않습니다. 하지만 대기 시간이 너무 커지면 [후기 단계 다시 프로젝션](../../overview/features/late-stage-reprojection.md)의 품질에 영향을 주고 전체 환경을 손상시킬 수 있습니다.

`videoFramesReceived`, `videoFrameReusedCount` 및 `videoFramesDiscarded`를 사용하면 네트워크 및 서버 성능을 측정할 수 있습니다. `videoFramesReceived`가 낮고 `videoFrameReusedCount`가 높으면 네트워크 정체 또는 서버 성능 저하를 나타낼 수 있습니다. 높은 `videoFramesDiscarded` 값도 네트워크 정체를 나타냅니다.

마지막으로 `timeSinceLastPresent`, `videoFrameMinDelta` 및 `videoFrameMaxDelta`는 수신되는 비디오 프레임 및 로컬 현재 호출의 분산 개념을 제공합니다. 분산이 높으면 프레임 속도 안정성이 낮습니다.

서버가 렌더링을 수행하는 데 걸린 정확한 시간을 왕복 값 `latencyPoseToReceive`에서 빼야 하기 때문에 위 값 중 순수한 네트워크 대기 시간(그림에 표시된 빨간색 화살표)을 명확하게 나타내는 값은 존재하지 않습니다. 전체 대기 시간 중 서버 쪽 부분은 클라이언트에 제공되지 않는 정보입니다. 하지만 다음 단락에서는 서버의 추가 입력을 통해 이 값의 근사값을 구하고 `networkLatency` 값을 통해 표시하는 방법을 설명합니다.

## <a name="performance-assessment-queries"></a>성능 평가 쿼리

*성능 평가 쿼리*는 서버에서 CPU 및 GPU 워크로드에 대해 더 자세한 정보를 제공합니다. 서버에서 데이터가 요청되기 때문에 성능 스냅샷을 쿼리할 때는 일반적인 비동기 패턴을 따릅니다.

```cs
PerformanceAssessmentAsync _assessmentQuery = null;

void QueryPerformanceAssessment(AzureSession session)
{
    _assessmentQuery = session.Actions.QueryServerPerformanceAssessmentAsync();
    _assessmentQuery.Completed += (PerformanceAssessmentAsync res) =>
    {
        // do something with the result:
        PerformanceAssessment result = res.Result;
        // ...

        _assessmentQuery = null;
    };
}
```

```cpp
void QueryPerformanceAssessment(ApiHandle<AzureSession> session)
{
    ApiHandle<PerformanceAssessmentAsync> assessmentQuery = *session->Actions()->QueryServerPerformanceAssessmentAsync();
    assessmentQuery->Completed([] (ApiHandle<PerformanceAssessmentAsync> res)
    {
        // do something with the result:
        PerformanceAssessment result = res->GetResult();

        // ...

    });
}
```

`FrameStatistics` 개체와 반대로, `PerformanceAssessment` 개체에는 서버 쪽 정보가 포함됩니다.

| 멤버 | 설명 |
|:-|:-|
| timeCPU | 프레임당 평균 서버 CPU 시간(밀리초) |
| timeGPU | 프레임당 평균 서버 GPU 시간(밀리초) |
| utilizationCPU | 전체 서버 CPU 사용률(%) |
| utilizationGPU | 총 서버 GPU 사용률(%) |
| memoryCPU | 총 서버 주 메모리 사용률(%) |
| memoryGPU | 서버 GPU 대비 총 전용 비디오 메모리 사용률(%) |
| networkLatency | 대략적인 평균 왕복 네트워크 대기 시간(밀리초)입니다. 위 그림에서 이 값은 빨간색 화살표의 합계에 해당합니다. 값은 `FrameStatistics`의 `latencyPoseToReceive` 값에서 실제 서버 렌더링 시간을 빼서 계산됩니다. 이 근사값이 정확하진 않아도 클라이언트에서 계산되는 대기 시간 값에서 분리된 네트워크 대기 시간을 어느 정도 나타낼 수 있습니다. |
| polygonsRendered | 한 프레임에서 렌더링된 삼각형의 개수입니다. 이 숫자에는 렌더링 중 나중에 골라낸 삼각형도 포함됩니다. 즉, 여러 카메라 위치에 따라 이 숫자가 크게 달라지진 않아도, 삼각형 고르기 속도에 따라 성능이 크게 달라질 수 있습니다.|

값을 평가하는 데 도움이 될 수 있도록 각 부분에는 **Great**, **Good**, **Mediocre** 또는 **Bad**와 같은 품질 분류가 제공됩니다.
이 평가 메트릭은 서버 상태에 대한 간략한 표시를 제공하지만 절대적인 것으로 이해하지는 않아야 합니다. 예를 들어 GPU 시간 점수가 'mediocre'라고 가정해보세요. mediocre로 간주되는 이유는 전체 프레임 시간 예산의 한계에 근접하기 때문입니다. 하지만 이 경우에는 복잡한 모델을 렌더링하기 때문에 그럼에도 불구하고 Good 값일 수 있습니다.

## <a name="statistics-debug-output"></a>통계 디버그 출력

`ARRServiceStats` 클래스는 프레임 통계 및 성능 평가 쿼리를 모두 포함하는 C# 클래스이며, 통계를 집계된 값 또는 미리 작성된 문자열로 반환하는 편리한 기능을 제공합니다. 다음 코드는 클라이언트 애플리케이션에서 서버 쪽 통계를 표시할 수 있는 가장 쉬운 방법입니다.

```cs
ARRServiceStats _stats = null;

void OnConnect()
{
    _stats = new ARRServiceStats();
}

void OnDisconnect()
{
    _stats = null;
}

void Update()
{
    if (_stats != null)
    {
        // update once a frame to retrieve new information and build average values
        _stats.Update(Service.CurrentActiveSession);

        // retrieve a string with relevant stats information
        InfoLabel.text = _stats.GetStatsString();
    }
}
```

위 코드는 텍스트 레이블에 다음 텍스트를 채웁니다.

![ArrServiceStats 문자열 출력](./media/arr-service-stats.png)

`GetStatsString` API는 모든 값의 문자열 형식을 지정하지만, 각 문자열 값은 `ARRServiceStats` 인스턴스에서 프로그래밍 방식으로 쿼리될 수 있습니다.

또한 시간에 따라 값을 집계하는 멤버의 변형도 있습니다. `*Avg`, `*Max` 또는 `*Total` 접미사가 포함된 멤버를 참조하세요. `FramesUsedForAverage` 멤버는 이 집계에 사용된 프레임 수를 나타냅니다.

## <a name="next-steps"></a>다음 단계

* [성능 추적 만들기](../../how-tos/performance-tracing.md)
* [모델 변환 구성](../../how-tos/conversion/configure-model-conversion.md)
