---
title: 구독 간에 얼굴 데이터 마이그레이션-얼굴
titleSuffix: Azure Cognitive Services
description: 이 가이드에서는 한 면에서 다른 구독으로 저장 된 얼굴 데이터를 마이그레이션하는 방법을 보여 줍니다.
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: nitinme
ms.custom: devx-track-csharp
ms.openlocfilehash: 3d52cd57d48a03c5dab8cb8098cd212e76ca76a1
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88929077"
---
# <a name="migrate-your-face-data-to-a-different-face-subscription"></a>얼굴 데이터를 다른 Face 구독으로 마이그레이션

이 가이드에서는 저장 된 PersonGroup 개체와 같은 얼굴 데이터를 다른 Azure Cognitive Services 얼굴 구독으로 이동 하는 방법을 보여 줍니다. 데이터를 이동 하려면 Snapshot 기능을 사용 합니다. 이러한 방식으로 작업을 이동 하거나 확장할 때 PersonGroup 또는 FaceList 개체를 반복적으로 작성 하 고 학습할 필요가 없습니다. 예를 들어 무료 구독을 사용 하 여 PersonGroup 개체를 만든 다음이를 유료 구독으로 마이그레이션하려는 경우가 있을 수 있습니다. 또는 대기업 작업을 위해 다른 지역의 구독에서 얼굴 데이터를 동기화 해야 할 수도 있습니다.

이러한 동일한 마이그레이션 전략이 LargePersonGroup 및 LargeFaceList 개체에도 적용됩니다. 이 가이드의 개념을 잘 모르는 경우 [얼굴 인식 개념](../concepts/face-recognition.md) 가이드에서 해당 정의를 참조 하세요. 이 가이드에서는 c #에서 Face .NET 클라이언트 라이브러리를 사용 합니다.

## <a name="prerequisites"></a>필수 구성 요소

다음 항목이 필요 합니다.

- 두 개의 Face 구독 키, 하나는 기존 데이터를 포함 하 고 다른 하나는로 마이그레이션해야 합니다. 얼굴 서비스를 구독 하 고 키를 가져오려면 [Cognitive Services 계정 만들기](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)의 지침을 따르세요.
- 대상 구독에 해당 하는 Face 구독 ID 문자열입니다. 이를 찾으려면 Azure Portal에서 **개요** 를 선택 합니다. 
- [Visual Studio 2015 또는 2017](https://www.visualstudio.com/downloads/)의 모든 버전.

## <a name="create-the-visual-studio-project"></a>Visual Studio 프로젝트 만들기

이 가이드에서는 간단한 콘솔 앱을 사용 하 여 얼굴 데이터 마이그레이션을 실행 합니다. 전체 구현에 대해서는 GitHub의 [Face 스냅숏 샘플](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/FaceApiSnapshotSample/FaceApiSnapshotSample) 을 참조 하세요.

1. Visual Studio에서 새 콘솔 앱 .NET Framework 프로젝트를 만듭니다. 이름을 **FaceApiSnapshotSample**로 합니다.
1. 필요한 NuGet 패키지를 가져옵니다. 솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 클릭 하 고 **NuGet 패키지 관리**를 선택 합니다. **찾아보기** 탭을 선택 하 고 **시험판 포함**을 선택 합니다. 다음 패키지를 찾아서 설치 합니다.
    - [Microsoft.Azure.CognitiveServices.Vision.Face 2.3.0-preview](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.Face/2.2.0-preview)

## <a name="create-face-clients"></a>얼굴 클라이언트 만들기

*Program.cs*의 **Main** 메서드에서 원본 및 대상 구독을 위한 2개의 [FaceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient?view=azure-dotnet)를 만듭니다. 이 예에서는 동아시아 지역의 Face 구독을 원본으로 사용 하 고 미국 서 부 구독을 대상으로 사용 합니다. 이 예제에서는 한 Azure 지역에서 다른 지역으로 데이터를 마이그레이션하는 방법을 보여 줍니다. 

[!INCLUDE [subdomains-note](../../../../includes/cognitive-services-custom-subdomains-note.md)]

```csharp
var FaceClientEastAsia = new FaceClient(new ApiKeyServiceClientCredentials("<East Asia Subscription Key>"))
    {
        Endpoint = "https://southeastasia.api.cognitive.microsoft.com/>"
    };

var FaceClientWestUS = new FaceClient(new ApiKeyServiceClientCredentials("<West US Subscription Key>"))
    {
        Endpoint = "https://westus.api.cognitive.microsoft.com/"
    };
```

원본 및 대상 구독에 대 한 구독 키 값 및 끝점 Url을 입력 합니다.


## <a name="prepare-a-persongroup-for-migration"></a>PersonGroup 마이그레이션 준비

대상 구독으로 마이그레이션하려면 원본 구독의 PersonGroup ID를 알아야 합니다. [PersonGroupOperationsExtensions](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.persongroupoperationsextensions.listasync?view=azure-dotnet) 메서드를 사용 하 여 PersonGroup 개체의 목록을 검색 합니다. 그런 다음 [PersonGroup. PersonGroupId](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.persongroup.persongroupid?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Vision_Face_Models_PersonGroup_PersonGroupId) 속성을 가져옵니다. 이 프로세스는 PersonGroup 개체에 따라 다르게 보입니다. 이 가이드에서 원본 PersonGroup ID는에 저장 됩니다 `personGroupId` .

> [!NOTE]
> 이 [샘플 코드](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/FaceApiSnapshotSample/FaceApiSnapshotSample) 는 새 PersonGroup를 만들어 학습 합니다. 대부분의 경우에는 사용할 PersonGroup 이미 있어야 합니다.

## <a name="take-a-snapshot-of-a-persongroup"></a>PersonGroup의 스냅숏 만들기

스냅숏은 특정 면 데이터 형식에 대 한 임시 원격 저장소입니다. 스냅숏은 다른 구독 간에 데이터를 복사하려면 클립보드의 일종으로 작동합니다. 먼저 원본 구독에서 데이터의 스냅숏을 만듭니다. 그런 다음 대상 구독의 새 데이터 개체에 적용 합니다.

원본 구독의 FaceClient 인스턴스를 사용 하 여 PersonGroup의 스냅숏을 만듭니다. PersonGroup ID 및 대상 구독의 ID와 함께 [TakeAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.snapshotoperationsextensions.takeasync?view=azure-dotnet) 를 사용 합니다. 대상 구독이 여러 개인 경우 세 번째 매개 변수에서 배열 항목으로 추가 합니다.

```csharp
var takeSnapshotResult = await FaceClientEastAsia.Snapshot.TakeAsync(
    SnapshotObjectType.PersonGroup,
    personGroupId,
    new[] { "<Azure West US Subscription ID>" /* Put other IDs here, if multiple target subscriptions wanted */ });
```

> [!NOTE]
> 스냅숏을 만들고 적용 하는 프로세스는 원본 또는 대상 PersonGroups 또는 FaceLists에 대 한 일반적인 호출을 방해 하지 않습니다. 예를 들어 [FaceList 관리 호출](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.facelistoperations?view=azure-dotnet) 또는 [PersonGroup 학습](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.persongroupoperations?view=azure-dotnet) 호출과 같은 소스 개체를 변경 하는 동시 호출을 수행 하지 마세요. 스냅숏 작업은 이러한 작업 전이나 후에 실행 되거나 오류가 발생할 수 있습니다.

## <a name="retrieve-the-snapshot-id"></a>스냅숏 ID를 검색 합니다.

스냅숏을 만드는 데 사용 되는 메서드는 비동기 이므로 완료 될 때까지 기다려야 합니다. 스냅숏 작업을 취소할 수 없습니다. 이 코드에서 메서드는 `WaitForOperation` 비동기 호출을 모니터링 합니다. 100 밀리초 마다 상태를 확인 합니다. 작업이 완료 된 후 필드를 구문 분석 하 여 작업 ID를 검색 합니다 `OperationLocation` . 

```csharp
var takeOperationId = Guid.Parse(takeSnapshotResult.OperationLocation.Split('/')[2]);
var operationStatus = await WaitForOperation(FaceClientEastAsia, takeOperationId);
```

일반적인 `OperationLocation` 값은 다음과 같습니다.

```csharp
"/operations/a63a3bdd-a1db-4d05-87b8-dbad6850062a"
```

여기에 `WaitForOperation` 도우미 메서드가 있습니다.

```csharp
/// <summary>
/// Waits for the take/apply operation to complete and returns the final operation status.
/// </summary>
/// <returns>The final operation status.</returns>
private static async Task<OperationStatus> WaitForOperation(IFaceClient client, Guid operationId)
{
    OperationStatus operationStatus = null;
    do
    {
        if (operationStatus != null)
        {
            Thread.Sleep(TimeSpan.FromMilliseconds(100));
        }

        // Get the status of the operation.
        operationStatus = await client.Snapshot.GetOperationStatusAsync(operationId);

        Console.WriteLine($"Operation Status: {operationStatus.Status}");
    }
    while (operationStatus.Status != OperationStatusType.Succeeded
            && operationStatus.Status != OperationStatusType.Failed);

    return operationStatus;
}
```

작업 상태가 표시 되 면 `Succeeded` `ResourceLocation` 반환 된 operationstatus 인스턴스의 필드를 구문 분석 하 여 스냅숏 ID를 가져옵니다.

```csharp
var snapshotId = Guid.Parse(operationStatus.ResourceLocation.Split('/')[2]);
```

일반적인 `resourceLocation` 값은 다음과 같습니다.

```csharp
"/snapshots/e58b3f08-1e8b-4165-81df-aa9858f233dc"
```

## <a name="apply-a-snapshot-to-a-target-subscription"></a>대상 구독에 스냅숏 적용

다음으로 임의로 생성 된 ID를 사용 하 여 대상 구독에 새 PersonGroup을 만듭니다. 그런 다음 대상 구독의 FaceClient 인스턴스를 사용 하 여이 PersonGroup에 스냅숏을 적용 합니다. 스냅숏 ID와 새 PersonGroup ID를 전달 합니다.

```csharp
var newPersonGroupId = Guid.NewGuid().ToString();
var applySnapshotResult = await FaceClientWestUS.Snapshot.ApplyAsync(snapshotId, newPersonGroupId);
```


> [!NOTE]
> 스냅숏 개체는 48 시간 동안만 유효 합니다. 이후에 데이터 마이그레이션에 사용 하려는 경우에만 스냅숏을 만듭니다.

스냅숏 적용 요청은 다른 작업 ID를 반환 합니다. 이 ID를 가져오려면 `OperationLocation` 반환 된 applySnapshotResult 인스턴스의 필드를 구문 분석 합니다. 

```csharp
var applyOperationId = Guid.Parse(applySnapshotResult.OperationLocation.Split('/')[2]);
```

또한 스냅숏 응용 프로그램 프로세스는 비동기적 이므로를 사용 `WaitForOperation` 하 여 완료 될 때까지 대기 합니다.

```csharp
operationStatus = await WaitForOperation(FaceClientWestUS, applyOperationId);
```

## <a name="test-the-data-migration"></a>데이터 마이그레이션 테스트

스냅숏을 적용 하면 대상 구독의 새 PersonGroup 원래 얼굴 데이터로 채워집니다. 기본적으로 학습 결과도 복사 됩니다. 새 PersonGroup은 재 학습 없이 얼굴 식별 호출에 사용할 준비가 되었습니다.

데이터 마이그레이션을 테스트 하려면 다음 작업을 실행 하 고 콘솔에 출력 되는 결과를 비교 합니다.

```csharp
await DisplayPersonGroup(FaceClientEastAsia, personGroupId);
await IdentifyInPersonGroup(FaceClientEastAsia, personGroupId);

await DisplayPersonGroup(FaceClientWestUS, newPersonGroupId);
// No need to retrain the person group before identification,
// training results are copied by snapshot as well.
await IdentifyInPersonGroup(FaceClientWestUS, newPersonGroupId);
```

다음과 같은 도우미 메서드를 사용합니다.

```csharp
private static async Task DisplayPersonGroup(IFaceClient client, string personGroupId)
{
    var personGroup = await client.PersonGroup.GetAsync(personGroupId);
    Console.WriteLine("Person Group:");
    Console.WriteLine(JsonConvert.SerializeObject(personGroup));

    // List persons.
    var persons = await client.PersonGroupPerson.ListAsync(personGroupId);

    foreach (var person in persons)
    {
        Console.WriteLine(JsonConvert.SerializeObject(person));
    }

    Console.WriteLine();
}
```

```csharp
private static async Task IdentifyInPersonGroup(IFaceClient client, string personGroupId)
{
    using (var fileStream = new FileStream("data\\PersonGroup\\Daughter\\Daughter1.jpg", FileMode.Open, FileAccess.Read))
    {
        var detectedFaces = await client.Face.DetectWithStreamAsync(fileStream);

        var result = await client.Face.IdentifyAsync(detectedFaces.Select(face => face.FaceId.Value).ToList(), personGroupId);
        Console.WriteLine("Test identify against PersonGroup");
        Console.WriteLine(JsonConvert.SerializeObject(result));
        Console.WriteLine();
    }
}
```

이제 대상 구독에서 새 PersonGroup를 사용할 수 있습니다. 

나중에 대상 PersonGroup를 다시 업데이트 하려면 스냅숏을 받을 새 PersonGroup를 만듭니다. 이렇게 하려면이 가이드의 단계를 수행 합니다. 단일 PersonGroup 개체에는 한 번만 적용 되는 스냅숏이 있을 수 있습니다.

## <a name="clean-up-resources"></a>리소스 정리

얼굴 데이터 마이그레이션을 완료 한 후에는 스냅숏 개체를 수동으로 삭제 합니다.

```csharp
await FaceClientEastAsia.Snapshot.DeleteAsync(snapshotId);
```

## <a name="next-steps"></a>다음 단계

다음으로 관련 API 참조 설명서를 참조 하거나, 스냅숏 기능을 사용 하는 샘플 앱을 탐색 하거나, 방법 가이드에 따라 여기에 언급 된 다른 API 작업을 시작 합니다.

- [스냅샷 참조 설명서(.NET SDK)](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.snapshotoperations?view=azure-dotnet)
- [얼굴 스냅숏 샘플](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/FaceApiSnapshotSample/FaceApiSnapshotSample)
- [얼굴 추가](how-to-add-faces.md)
- [이미지에서 얼굴 감지](HowtoDetectFacesinImage.md)
- [이미지에서 얼굴 식별](HowtoIdentifyFacesinImage.md)
