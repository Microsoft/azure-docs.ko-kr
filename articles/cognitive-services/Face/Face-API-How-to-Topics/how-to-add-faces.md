---
title: '예제: PersonGroup에 얼굴 추가 - Face'
titleSuffix: Azure Cognitive Services
description: 이 가이드에서는 Azure Cognitive Services Face 서비스를 사용하여 PersonGroup 개체에 다수의 사람과 얼굴을 추가하는 방법을 보여줍니다.
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: sample
ms.date: 04/10/2019
ms.author: sbowles
ms.custom: devx-track-csharp
ms.openlocfilehash: 06c353cf5fbd5c2b1058d99e7f91e9fcc3853cb1
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88929047"
---
# <a name="add-faces-to-a-persongroup"></a>PersonGroup에 얼굴 추가

이 가이드에서는 PersonGroup 개체에 다수의 사람과 얼굴을 추가하는 방법을 보여줍니다. 동일한 전략이 LargePersonGroup, FaceList 및 LargeFaceList 개체에도 적용됩니다. 이 샘플은 Azure Cognitive Services Face .NET 클라이언트 라이브러리를 사용하여 C#으로 작성되었습니다.

## <a name="step-1-initialization"></a>1단계: 초기화

다음 코드는 여러 변수를 선언하고 얼굴 및 요청을 예약하는 도우미 기능을 구현합니다.

- `PersonCount`는 총 사람 수입니다.
- `CallLimitPerSecond`은 구독 계층에 따른 초당 최대 호출 수입니다.
- `_timeStampQueue`는 요청 타임스탬프를 기록하는 큐입니다.
- `await WaitCallLimitPerSecondAsync()`는 다음 요청을 보내는 것이 유효할 때까지 기다립니다.

```csharp
const int PersonCount = 10000;
const int CallLimitPerSecond = 10;
static Queue<DateTime> _timeStampQueue = new Queue<DateTime>(CallLimitPerSecond);

static async Task WaitCallLimitPerSecondAsync()
{
    Monitor.Enter(_timeStampQueue);
    try
    {
        if (_timeStampQueue.Count >= CallLimitPerSecond)
        {
            TimeSpan timeInterval = DateTime.UtcNow - _timeStampQueue.Peek();
            if (timeInterval < TimeSpan.FromSeconds(1))
            {
                await Task.Delay(TimeSpan.FromSeconds(1) - timeInterval);
            }
            _timeStampQueue.Dequeue();
        }
        _timeStampQueue.Enqueue(DateTime.UtcNow);
    }
    finally
    {
        Monitor.Exit(_timeStampQueue);
    }
}
```

## <a name="step-2-authorize-the-api-call"></a>2단계: API 호출 권한 부여

클라이언트 라이브러리를 사용하는 경우 구독 키를 **FaceClient** 클래스의 생성자에 전달해야 합니다. 다음은 그 예입니다.

```csharp
private readonly IFaceClient faceClient = new FaceClient(
    new ApiKeyServiceClientCredentials("<SubscriptionKey>"),
    new System.Net.Http.DelegatingHandler[] { });
```

구독 키를 가져오려면 Azure Portal에서 Azure Marketplace로 이동합니다. 자세한 내용은 [구독](https://www.microsoft.com/cognitive-services/sign-up)을 참조하세요.

## <a name="step-3-create-the-persongroup"></a>3단계: PersonGroup 만들기

사람을 저장하기 위한 "MyPersonGroup"이라는 PersonGroup이 만들어집니다.
전체 유효성을 검사하기 위해 요청 시간이 `_timeStampQueue`에 추가됩니다.

```csharp
const string personGroupId = "mypersongroupid";
const string personGroupName = "MyPersonGroup";
_timeStampQueue.Enqueue(DateTime.UtcNow);
await faceClient.LargePersonGroup.CreateAsync(personGroupId, personGroupName);
```

## <a name="step-4-create-the-persons-for-the-persongroup"></a>4단계: PersonGroup에 대한 사람 만들기

사람이 동시에 만들어지고 호출 제한을 초과하지 않기 위해 `await WaitCallLimitPerSecondAsync()`도 적용됩니다.

```csharp
Person[] persons = new Person[PersonCount];
Parallel.For(0, PersonCount, async i =>
{
    await WaitCallLimitPerSecondAsync();

    string personName = $"PersonName#{i}";
    persons[i] = await faceClient.PersonGroupPerson.CreateAsync(personGroupId, personName);
});
```

## <a name="step-5-add-faces-to-the-persons"></a>5단계: 사람에게 얼굴 추가

다른 사람에 추가된 얼굴은 동시에 처리됩니다. 하나의 특정 사람에 대해 추가된 얼굴은 순차적으로 처리됩니다.
요청 빈도가 제한 범위 이내가 되도록 `await WaitCallLimitPerSecondAsync()`가 다시 한 번 호출됩니다.

```csharp
Parallel.For(0, PersonCount, async i =>
{
    Guid personId = persons[i].PersonId;
    string personImageDir = @"/path/to/person/i/images";

    foreach (string imagePath in Directory.GetFiles(personImageDir, "*.jpg"))
    {
        await WaitCallLimitPerSecondAsync();

        using (Stream stream = File.OpenRead(imagePath))
        {
            await faceClient.PersonGroupPerson.AddFaceFromStreamAsync(personGroupId, personId, stream);
        }
    }
});
```

## <a name="summary"></a>요약

이 가이드에서는 다수의 사람과 얼굴이 있는 PersonGroup을 만드는 프로세스를 배웠습니다. 다음 사항을 기억하세요.

- 이 전략은 FaceList 및 LargePersonGroup에도 적용됩니다.
- LargePersonGroup의 여러 FaceList 또는 사람에 얼굴을 추가/삭제하는 작업은 동시에 처리됩니다.
- LargePersonGroup의 특정 FaceList 또는 사람 하나에 얼굴을 추가/삭제하는 작업은 순차적으로 처리됩니다.
- 간단하게 하기 위해 이 가이드에서는 잠재적 예외 처리 방법을 생략했습니다. 견고성을 강화하려면 적절한 재시도 정책을 적용하세요.

다음 기능에 대해 설명하고 시연했습니다.

- [PersonGroup - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) API를 사용하여 PersonGroup을 만듭니다.
- [PersonGroup Person - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c) API를 사용하여 사람을 만듭니다.
- [PersonGroup Person - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) API를 사용하여 사람에 얼굴을 추가합니다.

## <a name="related-topics"></a>관련 항목

- [이미지에서 얼굴 식별](HowtoIdentifyFacesinImage.md)
- [이미지에서 얼굴 감지](HowtoDetectFacesinImage.md)
- [대규모 기능 사용](how-to-use-large-scale.md)
