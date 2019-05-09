---
title: '예제: 얼굴 추가 - Face API'
titleSuffix: Azure Cognitive Services
description: Face API를 사용하여 이미지에 얼굴을 추가합니다.
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: sample
ms.date: 03/01/2018
ms.author: sbowles
ms.openlocfilehash: 722a09b782c902642b599460835151928c16c5f4
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55859031"
---
# <a name="example-how-to-add-faces"></a>예제: 얼굴을 추가하는 방법

이 가이드에서는 PersonGroup에 다수의 사람과 얼굴을 추가하는 모범 사례를 보여줍니다.
FaceList 및 LargePersonGroup에도 동일한 전략이 적용됩니다.
샘플은 Face API 클라이언트 라이브러리를 사용하여 C#에서 작성되었습니다.

## <a name="step-1-initialization"></a>1단계: 초기화

여러 변수를 선언하고 요청을 예약하는 도우미 함수를 구현합니다.

- `PersonCount`는 총 사람 수입니다.
- `CallLimitPerSecond`은 구독 계층에 따른 초당 최대 호출 수입니다.
- `_timeStampQueue`는 요청 타임스탬프를 기록하는 큐입니다.
- `await WaitCallLimitPerSecondAsync()`는 다음 요청을 보낼 수 있을 때까지 대기합니다.

```C#
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

클라이언트 라이브러리를 사용하는 경우 구독 키는 FaceServiceClient 클래스의 생성자를 통해 전달됩니다. 예를 들면 다음과 같습니다.

```C#
FaceServiceClient faceServiceClient = new FaceServiceClient("<Subscription Key>");
```

구독 키는 Azure Portal의 Marketplace 페이지에서 얻을 수 있습니다. [구독](https://www.microsoft.com/cognitive-services/en-us/sign-up)을 참조하세요.

## <a name="step-3-create-the-persongroup"></a>3단계: PersonGroup 만들기

사람을 저장하기 위한 "MyPersonGroup"이라는 PersonGroup이 만들어집니다.
전체 유효성을 검사하기 위해 요청 시간이 `_timeStampQueue`에 추가됩니다.

```C#
const string personGroupId = "mypersongroupid";
const string personGroupName = "MyPersonGroup";
_timeStampQueue.Enqueue(DateTime.UtcNow);
await faceServiceClient.CreatePersonGroupAsync(personGroupId, personGroupName);
```

## <a name="step-4-create-the-persons-to-the-persongroup"></a>4단계: PersonGroup에 사람 만들기

사람이 동시에 만들어지고 호출 제한을 초과하지 않기 위해 `await WaitCallLimitPerSecondAsync()`가 적용됩니다.

```C#
CreatePersonResult[] persons = new CreatePersonResult[PersonCount];
Parallel.For(0, PersonCount, async i =>
{
    await WaitCallLimitPerSecondAsync();

    string personName = $"PersonName#{i}";
    persons[i] = await faceServiceClient.CreatePersonAsync(personGroupId, personName);
});
```

## <a name="step-5-add-faces-to-the-persons"></a>5단계: 사람에게 얼굴 추가

여러 사람에게 얼굴을 추가하는 작업은 동시에 처리되고, 특정한 한 사람에만 추가하는 작업은 순차적으로 처리됩니다.
요청 빈도가 제한 범위를 벗어나지 않도록 방지하기 위해 다시 한 번 `await WaitCallLimitPerSecondAsync()`가 호출됩니다.

```C#
Parallel.For(0, PersonCount, async i =>
{
    Guid personId = persons[i].PersonId;
    string personImageDir = @"/path/to/person/i/images";

    foreach (string imagePath in Directory.GetFiles(personImageDir, "*.jpg"))
    {
        await WaitCallLimitPerSecondAsync();

        using (Stream stream = File.OpenRead(imagePath))
        {
            await faceServiceClient.AddPersonFaceAsync(personGroupId, personId, stream);
        }
    }
});
```

## <a name="summary"></a>요약

이 가이드에서는 다수의 사용자와 얼굴이 있는 PersonGroup을 만드는 프로세스를 배웠습니다. 다음 사항을 기억하세요.

- 이 전략은 FaceList 및 LargePersonGroup에도 적용됩니다.
- LargePersonGroup의 여러 FaceLists 또는 사람들에게 얼굴을 추가/삭제하는 작업은 동시에 처리할 수 있습니다.
- LargePersonGroup의 특정 FaceList 또는 사람에게 얼굴을 추가/삭제하는 작업은 순차적으로 처리해야 합니다.
- 단순성을 유지하기 위해 이 가이드에서는 잠재적 예외 처리를 생략했습니다. 견고성을 강화하려면 적절한 재시도 정책을 적용해야 합니다.

다음 항목을 통해 앞에서 설명하고 시연한 기능을 다시 한 번 간략하게 살펴보시기 바랍니다.

- [PersonGroup - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) API를 사용하여 PersonGroups 만들기
- [PersonGroup Person - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c) API를 사용하여 사람 만들기
- [PersonGroup Person - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) API를 사용하여 사람에게 얼굴 추가

## <a name="related-topics"></a>관련 항목

- [이미지에서 얼굴을 식별하는 방법](HowtoIdentifyFacesinImage.md)
- [이미지에서 얼굴을 감지하는 방법](HowtoDetectFacesinImage.md)
- [대규모 기능을 사용하는 방법](how-to-use-large-scale.md)
