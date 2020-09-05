---
title: '예제: 대규모 기능 사용 - Face'
titleSuffix: Azure Cognitive Services
description: 이 가이드는 기존 PersonGroup 및 FaceList 개체에서 LargePersonGroup 및 LargeFaceList 개체로 확장하는 방법에 대한 문서입니다.
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: sample
ms.date: 05/01/2019
ms.author: sbowles
ms.custom: devx-track-csharp
ms.openlocfilehash: c4667b810e4d3d5f13e13572e2420880da5a8a0b
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88931627"
---
# <a name="example-use-the-large-scale-feature"></a>예제: 대규모 기능 사용

이 가이드는 기존 PersonGroup 및 FaceList 개체에서 LargePersonGroup 및 LargeFaceList 개체로 각각 강화하는 방법에 대한 고급 문서입니다. 이 가이드에서는 마이그레이션 프로세스를 설명합니다. 여기서는 PersonGroup 및 FaceList 개체, [Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ae2d16ac60f11b48b5aa4)(학습) 작업 및 얼굴 인식 함수에 대해 기본적으로 이해하고 있다고 가정합니다. 이러한 주제에 대한 자세한 내용은 [얼굴 인식](../concepts/face-recognition.md) 개념 가이드를 참조하세요.

LargePersonGroup과 LargeFaceList는 총괄하여 대규모 작업이라고 합니다. LargePersonGroup은 최대 100만 명의 사람을 포함할 수 있으며, 각 사람마다 최대 248개의 얼굴이 있습니다. LargeFaceList는 최대 100만 개의 얼굴을 포함할 수 있습니다. 대규모 작업은 기존의 PersonGroup 및 FaceList와 비슷하지만 새 아키텍처로 인해 몇 가지 차이점이 있습니다. 

샘플은 Azure Cognitive Services Face 클라이언트 라이브러리를 사용하여 C#으로 작성되었습니다.

> [!NOTE]
> Identification 및 FindSimilar에 대한 Face 검색 성능을 대규모로 사용하도록 설정하려면 LargeFaceList 및 LargePersonGroup을 전처리하도록 Train 작업을 도입합니다. 학습 시간은 실제 용량에 따라 초 단위에서 약 30분까지 다양합니다. 학습 기간 동안 성공적인 학습 작업이 이전에 수행된 경우 Identification 및 FindSimilar를 수행할 수 있습니다. 단점은 대규모 학습으로의 새 게시 마이그레이션이 완료될 때까지 새로 추가된 사람과 얼굴이 결과에 표시되지 않는 것입니다.

## <a name="step-1-initialize-the-client-object"></a>1단계: 클라이언트 개체 초기화

Face 클라이언트 라이브러리를 사용하면 구독 키 및 구독 엔드포인트가 FaceClient 클래스의 생성자를 통해 전달됩니다. 다음은 그 예입니다.

```csharp
string SubscriptionKey = "<Subscription Key>";
// Use your own subscription endpoint corresponding to the subscription key.
string SubscriptionEndpoint = "https://westus.api.cognitive.microsoft.com";
private readonly IFaceClient faceClient = new FaceClient(
            new ApiKeyServiceClientCredentials(subscriptionKey),
            new System.Net.Http.DelegatingHandler[] { });
faceClient.Endpoint = SubscriptionEndpoint
```

해당 엔드포인트가 있는 구독 키를 가져오려면 Azure Portal에서 Azure Marketplace로 이동합니다.
자세한 내용은 [구독](https://azure.microsoft.com/services/cognitive-services/directory/vision/)을 참조하세요.

## <a name="step-2-code-migration"></a>2단계: 코드 마이그레이션

이 섹션에서는 PersonGroup 또는 FaceList 구현을 LargePersonGroup 또는 LargeFaceList로 마이그레이션하는 방법에 중점을 둡니다. LargePersonGroup/LargeFaceList는 디자인 및 내부 구현에서 PersonGroup/FaceList와 다르지만 API 인터페이스는 이전 버전과의 호환성을 위해 유사합니다.

데이터 마이그레이션은 지원되지 않습니다. 대신 LargePersonGroup 또는 LargeFaceList를 다시 만듭니다.

### <a name="migrate-a-persongroup-to-a-largepersongroup"></a>PersonGroup을 LargePersonGroup으로 마이그레이션

PersonGroup에서 LargePersonGroup으로의 마이그레이션은 간단합니다. 정확히 동일한 그룹 수준 작업을 공유합니다.

PersonGroup 또는 Person 관련 구현의 경우 API 경로 또는 SDK 클래스/모듈만 LargePersonGroup 및 LargePersonGroup Person으로 변경해야 합니다.

PersonGroup의 모든 얼굴과 사람을 새 LargePersonGroup에 추가합니다. 자세한 내용은 [얼굴 추가](how-to-add-faces.md)를 참조하세요.

### <a name="migrate-a-facelist-to-a-largefacelist"></a>FaceList를 LargeFaceList로 마이그레이션

| FaceList API | LargeFaceList API |
|:---:|:---:|
| 생성 | 생성 |
| DELETE | DELETE |
| 가져오기 | 가져오기 |
| 목록 | 목록 |
| 업데이트 | 업데이트 |
| - | 학습 |
| - | 학습 상태 가져오기 |

앞의 테이블은 FaceList와 LargeFaceList 간 목록 수준 작업의 비교입니다. 표시된 대로 FaceList와 비교할 때 LargeFaceList에는 Train(학습) 및 Get Training Status(학습 상태 가져오기)라는 새 작업이 제공됩니다. LargeFaceList 학습은 [FindSimilar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) 작업의 전제 조건입니다. FaceList에는 학습이 필요하지 않습니다. 다음 코드 조각은 LargeFaceList의 학습을 기다리는 도우미 함수입니다.

```csharp
/// <summary>
/// Helper function to train LargeFaceList and wait for finish.
/// </summary>
/// <remarks>
/// The time interval can be adjusted considering the following factors:
/// - The training time which depends on the capacity of the LargeFaceList.
/// - The acceptable latency for getting the training status.
/// - The call frequency and cost.
///
/// Estimated training time for LargeFaceList in different scale:
/// -     1,000 faces cost about  1 to  2 seconds.
/// -    10,000 faces cost about  5 to 10 seconds.
/// -   100,000 faces cost about  1 to  2 minutes.
/// - 1,000,000 faces cost about 10 to 30 minutes.
/// </remarks>
/// <param name="largeFaceListId">The Id of the LargeFaceList for training.</param>
/// <param name="timeIntervalInMilliseconds">The time interval for getting training status in milliseconds.</param>
/// <returns>A task of waiting for LargeFaceList training finish.</returns>
private static async Task TrainLargeFaceList(
    string largeFaceListId,
    int timeIntervalInMilliseconds = 1000)
{
    // Trigger a train call.
    await FaceClient.LargeTrainLargeFaceListAsync(largeFaceListId);

    // Wait for training finish.
    while (true)
    {
        Task.Delay(timeIntervalInMilliseconds).Wait();
        var status = await faceClient.LargeFaceList.TrainAsync(largeFaceListId);

        if (status.Status == Status.Running)
        {
            continue;
        }
        else if (status.Status == Status.Succeeded)
        {
            break;
        }
        else
        {
            throw new Exception("The train operation is failed!");
        }
    }
}
```

이전에 얼굴이 추가되고 FindSimilar가 있는 FaceList의 일반적인 사용법은 다음과 같았습니다.

```csharp
// Create a FaceList.
const string FaceListId = "myfacelistid_001";
const string FaceListName = "MyFaceListDisplayName";
const string ImageDir = @"/path/to/FaceList/images";
faceClient.FaceList.CreateAsync(FaceListId, FaceListName).Wait();

// Add Faces to the FaceList.
Parallel.ForEach(
    Directory.GetFiles(ImageDir, "*.jpg"),
    async imagePath =>
        {
            using (Stream stream = File.OpenRead(imagePath))
            {
                await faceClient.FaceList.AddFaceFromStreamAsync(FaceListId, stream);
            }
        });

// Perform FindSimilar.
const string QueryImagePath = @"/path/to/query/image";
var results = new List<SimilarPersistedFace[]>();
using (Stream stream = File.OpenRead(QueryImagePath))
{
    var faces = faceClient.Face.DetectWithStreamAsync(stream).Result;
    foreach (var face in faces)
    {
        results.Add(await faceClient.Face.FindSimilarAsync(face.FaceId, FaceListId, 20));
    }
}
```

LargeFaceList로 마이그레이션하는 경우 다음과 같이 됩니다.

```csharp
// Create a LargeFaceList.
const string LargeFaceListId = "mylargefacelistid_001";
const string LargeFaceListName = "MyLargeFaceListDisplayName";
const string ImageDir = @"/path/to/FaceList/images";
faceClient.LargeFaceList.CreateAsync(LargeFaceListId, LargeFaceListName).Wait();

// Add Faces to the LargeFaceList.
Parallel.ForEach(
    Directory.GetFiles(ImageDir, "*.jpg"),
    async imagePath =>
        {
            using (Stream stream = File.OpenRead(imagePath))
            {
                await faceClient.LargeFaceList.AddFaceFromStreamAsync(LargeFaceListId, stream);
            }
        });

// Train() is newly added operation for LargeFaceList.
// Must call it before FindSimilarAsync() to ensure the newly added faces searchable.
await TrainLargeFaceList(LargeFaceListId);

// Perform FindSimilar.
const string QueryImagePath = @"/path/to/query/image";
var results = new List<SimilarPersistedFace[]>();
using (Stream stream = File.OpenRead(QueryImagePath))
{
    var faces = faceClient.Face.DetectWithStreamAsync(stream).Result;
    foreach (var face in faces)
    {
        results.Add(await faceClient.Face.FindSimilarAsync(face.FaceId, largeFaceListId: LargeFaceListId));
    }
}
```

앞에서 설명한 대로 데이터 관리와 FindSimilar 부분은 거의 동일합니다. 유일한 예외는 FindSimilar가 작동하기 전에 LargeFaceList에서 전처리 Train 작업 새로 고침을 완료해야 한다는 것입니다.

## <a name="step-3-train-suggestions"></a>3단계: 학습 제안 사항

Train 작업은 [FindSimilar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) 및 [Identification](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239)의 속도를 향상시키지만 특히 대규모 작업인 경우 학습 시간이 저하됩니다. 다음 표에는 서로 다른 규모에서 예상되는 학습 시간이 나와 있습니다.

| 얼굴 또는 사람에 대한 크기 | 예상 학습 시간 |
|:---:|:---:|
| 1,000 | 1-2초 |
| 10000 | 5-10초 |
| 100,000 | 1-2분 |
| 1,000,000 | 10-30분 |

대규모 기능을 더 잘 활용하려면 다음 전략을 사용하는 것이 좋습니다.

### <a name="step-31-customize-time-interval"></a>3\.1단계: 시간 간격 사용자 지정

`TrainLargeFaceList()`에서와 같이 무한 학습 상태 확인 프로세스를 지연시키는 시간 간격(밀리초)이 있습니다. 더 많은 얼굴이 있는 LargeFaceList의 경우 큰 간격을 사용하면 호출 수 및 비용을 감소시킵니다. LargeFaceList의 예상 용량에 따라 시간 간격을 사용자 지정합니다.

동일한 전략이 LargePersonGroup에도 적용됩니다. 예를 들어 100만 명의 사람을 사용하여 LargePersonGroup을 학습시키는 경우 `timeIntervalInMilliseconds`는 60,000(1분 간격)일 수 있습니다.

### <a name="step-32-small-scale-buffer"></a>3\.2단계: 소규모 버퍼

LargePersonGroup 또는 LargeFaceList의 사람 또는 얼굴은 학습된 후에만 검색할 수 있습니다. 동적 시나리오에서 새 사람 또는 얼굴은 지속적으로 추가되고 즉시 검색할 수 있어야 하지만, 학습은 원하는 것보다 오래 걸릴 수 있습니다. 

이 문제를 완화하려면 소규모의 추가 LargePersonGroup 또는 LargeFaceList를 새로 추가된 항목에 대한 버퍼로만 사용합니다. 이 버퍼는 크기가 작으므로 학습하는 시간이 더 짧습니다. 이 임시 버퍼에 대한 즉시 검색 기능이 작동해야 합니다. 마스터 학습을 더욱 드문 간격으로 실행하여 마스터 LargePersonGroup 또는 LargeFaceList에 대한 학습과 함께 이 버퍼를 사용합니다. 예를 들어 한밤중과 매일이 있습니다.

예제 워크플로:

1. 마스터 컬렉션인 마스터 LargePersonGroup 또는 LargeFaceList를 만듭니다. 버퍼 컬렉션인 버퍼 LargePersonGroup 또는 LargeFaceList를 만듭니다. 버퍼 컬렉션은 새로 추가된 사람 또는 얼굴입니다.
1. 새 사람 또는 얼굴을 마스터 컬렉션 및 버퍼 컬렉션 모두에 추가합니다.
1. 새로 추가된 항목이 적용되도록 버퍼 컬렉션만 짧은 시간 간격으로 학습시킵니다.
1. 마스터 컬렉션 및 버퍼 컬렉션 모두에 대해 Identification 또는 FindSimilar를 호출합니다. 결과를 병합합니다.
1. 버퍼 컬렉션 크기가 임계값까지 증가하거나 시스템 유휴 시간에 증가하는 경우 새 버퍼 컬렉션을 만듭니다. 마스터 컬렉션에 대한 Train 작업을 트리거합니다.
1. 마스터 컬렉션에 대한 Train 작업이 완료되면 이전 버퍼 컬렉션을 삭제합니다.

### <a name="step-33-standalone-training"></a>3\.3단계: 독립 실행형 학습

상대적으로 긴 대기 시간이 허용되면 새 데이터를 추가한 직후 Train 작업을 트리거할 필요가 없습니다. 대신 학습 작업은 기본 논리에서 분할되고 정기적으로 트리거될 수 있습니다. 이 전략은 대기 시간이 허용되는 동적 시나리오에 적합합니다. Train 빈도를 더 줄이기 위해 이 전략을 정적 시나리오에 적용할 수 있습니다.

`TrainLargeFaceList`와 비슷한 `TrainLargePersonGroup` 함수가 있다고 가정합니다. `System.Timers`에서 [`Timer`](https://msdn.microsoft.com/library/system.timers.timer(v=vs.110).aspx) 클래스를 호출하여 LargePersonGroup에 대한 독립 실행형 학습을 구현하는 일반적인 방법은 다음과 같습니다.

```csharp
private static void Main()
{
    // Create a LargePersonGroup.
    const string LargePersonGroupId = "mylargepersongroupid_001";
    const string LargePersonGroupName = "MyLargePersonGroupDisplayName";
    faceClient.LargePersonGroup.CreateAsync(LargePersonGroupId, LargePersonGroupName).Wait();

    // Set up standalone training at regular intervals.
    const int TimeIntervalForStatus = 1000 * 60; // 1-minute interval for getting training status.
    const double TimeIntervalForTrain = 1000 * 60 * 60; // 1-hour interval for training.
    var trainTimer = new Timer(TimeIntervalForTrain);
    trainTimer.Elapsed += (sender, args) => TrainTimerOnElapsed(LargePersonGroupId, TimeIntervalForStatus);
    trainTimer.AutoReset = true;
    trainTimer.Enabled = true;

    // Other operations like creating persons, adding faces, and identification, except for Train.
    // ...
}

private static void TrainTimerOnElapsed(string largePersonGroupId, int timeIntervalInMilliseconds)
{
    TrainLargePersonGroup(largePersonGroupId, timeIntervalInMilliseconds).Wait();
}
```

데이터 관리 및 식별 관련 구현에 대한 자세한 내용은 [얼굴 추가](how-to-add-faces.md) 및 [이미지에서 얼굴 식별](HowtoIdentifyFacesinImage.md)을 참조하세요.

## <a name="summary"></a>요약

이 가이드에서는 데이터가 아니라 기존 PersonGroup 또는 FaceList 코드를 LargePersonGroup 또는 LargeFaceList로 마이그레이션하는 방법을 알아보았습니다.

- LargeFaceList에 Train 작업이 필요하다는 점을 제외하고는 LargePersonGroup 및 LargeFaceList가 PersonGroup 또는 FaceList와 비슷하게 작동합니다.
- 대규모 데이터 세트에 대한 동적 데이터 업데이트에 적합한 Train 전략을 사용합니다.

## <a name="next-steps"></a>다음 단계

방법 가이드에 따라 PersonGroup에 얼굴을 추가하거나 PersonGroup에 대한 확인 작업을 실행하는 방법을 알아봅니다.

- [얼굴 추가](how-to-add-faces.md)
- [이미지에서 얼굴 식별](HowtoIdentifyFacesinImage.md)
