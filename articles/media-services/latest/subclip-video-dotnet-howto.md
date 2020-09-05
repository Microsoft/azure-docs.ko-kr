---
title: Azure Media Services로 인코딩할 경우 비디오를 하위 클립
description: 이 항목에서는 .NET SDK를 사용 하 여 Azure Media Services 인코딩할 때 비디오를 하위 클립 하는 방법에 대해 설명 합니다.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 06/09/2019
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 827fa21fd7f0a3179fb1ed3cd01a461a785829b5
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89257401"
---
# <a name="subclip-a-video-when-encoding-with-media-services---net"></a>Media Services-.NET을 사용 하 여 인코딩할 경우 비디오 하위 클립

[작업](/rest/api/media/jobs)을 사용 하 여 인코딩할 때 비디오를 자르거나 하위 클립 할 수 있습니다. 이 기능은 [BuiltInStandardEncoderPreset](/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) 사전 설정 또는 [StandardEncoderPreset](/rest/api/media/transforms/createorupdate#standardencoderpreset) 사전 설정을 사용하여 빌드된 모든 [변환](/rest/api/media/transforms)과 함께 작동합니다.

다음 c # 예제에서는 인코딩 작업을 전송할 때 자산에 비디오를 트리밍하는 작업을 만듭니다. 

## <a name="prerequisites"></a>필수 구성 요소

이 항목에 설명된 단계를 완료하려면 다음을 수행해야 합니다.

- [Azure Media Services 계정 만들기](./create-account-howto.md)
- 변환과 입력 및 출력 자산을 만듭니다. .NET 자습서를 [사용 하 여 비디오 업로드, 인코딩 및 스트리밍](stream-files-tutorial-with-api.md) 에서 변환 및 입력 및 출력 자산을 만드는 방법을 확인할 수 있습니다.
- [인코딩 개념](encoding-concept.md) 항목을 검토 합니다.

## <a name="example"></a>예

```csharp
/// <summary>
/// Submits a request to Media Services to apply the specified Transform to a given input video.
/// </summary>
/// <param name="client">The Media Services client.</param>
/// <param name="resourceGroupName">The name of the resource group within the Azure subscription.</param>
/// <param name="accountName"> The Media Services account name.</param>
/// <param name="transformName">The name of the transform.</param>
/// <param name="jobName">The (unique) name of the job.</param>
/// <param name="inputAssetName">The name of the input asset.</param>
/// <param name="outputAssetName">The (unique) name of the  output asset that will store the result of the encoding job. </param>
// <SubmitJob>
private static async Task<Job> JobWithBuiltInStandardEncoderWithSingleClipAsync(
    IAzureMediaServicesClient client,
    string resourceGroupName,
    string accountName,
    string transformName,
    string jobName,
    string inputAssetName,
    string outputAssetName)
{
    var jobOutputs = new List<JobOutputAsset>
    {
        new JobOutputAsset(state: JobState.Queued, progress: 0, assetName: outputAssetName)
    };

    var clipStart = new AbsoluteClipTime()
    {
        Time = new TimeSpan(0, 0, 20)
    };

    var clipEnd = new AbsoluteClipTime()
    {
        Time = new TimeSpan(0, 0, 30)
    };

    var jobInput = new JobInputAsset(assetName: inputAssetName, start: clipStart, end: clipEnd);

    Job job = await client.Jobs.CreateAsync(
        resourceGroupName,
        accountName,
        transformName,
        jobName,
        new Job(input: jobInput, outputs: jobOutputs.ToArray(), name: jobName)
        {
            Description = $"A Job with transform {transformName} and single clip.",
            Priority = Priority.Normal,
        });

    return job;
}
```

## <a name="next-steps"></a>다음 단계

[사용자 지정 변환을 사용 하 여 인코딩하는 방법](customize-encoder-presets-how-to.md) 
