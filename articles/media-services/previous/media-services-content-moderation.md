---
title: Azure Media Content Moderator를 사용하여 가능한 성인/외설 콘텐츠 검색 | Microsoft Docs
description: 비디오 조정은 비디오에서 잠재적인 성인/외설 콘텐츠를 검색하는 데 도움이 됩니다.
services: media-services
documentationcenter: ''
author: sanjeev3
manager: mikemcca
editor: ''
ms.assetid: a245529f-3150-4afc-93ec-e40d8a6b761d
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/14/2019
ms.author: sajagtap
ms.openlocfilehash: eb16f5e1e72e5a9379ad530ab9677adba2ccbbcd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61465680"
---
# <a name="use-azure-media-content-moderator-to-detect-possible-adult-and-racy-content"></a>Azure Media Content Moderator를 사용하여 가능한 성인/외설 콘텐츠 검색 

## <a name="overview"></a>개요
**Azure Media Content Moderator** MP(미디어 프로세서)를 통해 비디오에 컴퓨터 지원 조정을 사용할 수 있습니다. 예를 들어 휴먼 조정 팀이 비디오에서 혹시라도 있을 수 있는 성인/외설 콘텐츠를 검색하고 플래그가 지정된 콘텐츠를 검토하게 하는 것이 좋습니다.

**Azure Media Content Moderator** MP는 현재 미리 보기 상태입니다.

이 문서에서는 **Azure Media Content Moderator**에 대한 세부 정보를 제공하고 .NET용 Media Services SDK와 함께 사용하는 방법을 보여 줍니다.

## <a name="content-moderator-input-files"></a>Content Moderator 입력 파일
동영상 파일입니다. 현재 다음 형식이 지원됩니다. MP4, MOV 및 WMV.

## <a name="content-moderator-output-files"></a>Content Moderator 출력 파일
JSON 형식의 조정된 출력에는 자동 검색된 스크린샷 및 키 프레임이 포함됩니다. 가능한 성인/외설 콘텐츠에 대한 신뢰도 점수와 함께 키 프레임이 반환됩니다. 또한 검토가 권장되는지 여부를 나타내는 부울 플래그가 포함됩니다. 검토 권장 플래그에는 성인/외설 점수의 내부 임계값을 기준으로 값이 할당됩니다.

## <a name="elements-of-the-output-json-file"></a>출력 JSON 파일의 요소

작업은 검색된 스크린샷 및 키 프레임과 성인/외설 콘텐츠 포함 여부에 대한 메타데이터가 포함된 JSON 출력 파일을 생성합니다.

출력 JSON은 다음 요소를 포함합니다.

### <a name="root-json-elements"></a>루트 JSON 요소

| 요소 | 설명 |
| --- | --- |
| 버전 |Content Moderator 버전입니다. |
| timescale |동영상의 초당 "틱"입니다. |
| offset |타임스탬프의 시간 오프셋입니다. Video API 버전 1.0에서 이 값은 항상 0입니다. 이 값은 나중에 변경할 수 있습니다. |
| framerate |동영상의 초당 프레임 수입니다. |
| width |출력 비디오 프레임의 너비(픽셀)입니다.|
| height |출력 비디오 프레임의 높이(픽셀)입니다.|
| totalDuration |입력 비디오의 기간(“틱”)입니다. |
| [fragments](#fragments-json-elements) |메타데이터는 조각이라고 하는 다른 세그먼트로 청크 분할됩니다. 각 조각은 시작, 기간, 간격 번호 및 이벤트가 포함된 자동 검색된 스크린샷입니다. |

### <a name="fragments-json-elements"></a>조각 JSON 요소

|요소|설명|
|---|---|
| start |“틱” 단위의 첫 이벤트 시작 시간입니다. |
| duration |"틱" 단위의 조각 길이입니다. |
| interval |"틱" 단위의 조각 내 각 이벤트 항목 간격입니다. |
| [events](#events-json-elements) |각 이벤트는 클립을 나타내고, 각 클립에는 해당 기간 내에 검색 및 추적된 키 프레임이 포함됩니다. 이벤트 배열입니다. 외부 배열은 하나의 시간 간격을 나타냅니다. 내부 배열은 해당 특정 시점에 발생한 0개 이상의 이벤트로 구성됩니다.|

### <a name="events-json-elements"></a>이벤트 JSON 요소

|요소|설명|
|---|---|
| reviewRecommended | **adultScore** 또는 **racyScore**가 내부 임계값을 초과하는지 여부에 따라 `true` 또는 `false`입니다. |
| adultScore | 가능한 성인 콘텐츠에 대한 신뢰도 점수로, 0.00에서 0.99 사이입니다. |
| racyScore | 가능한 외설 콘텐츠에 대한 신뢰도 점수로, 0.00에서 0.99 사이입니다. |
| index | 첫 번째 프레임 인덱스에서 마지막 프레임 인덱스 사이의 프레임 인덱스입니다. |
| timestamp | 프레임의 위치(“틱”)입니다. |
| shotIndex | 부모 스크린샷의 인덱스입니다. |


## <a name="content-moderation-quickstart-and-sample-output"></a>콘텐츠 조정 빠른 시작 및 샘플 출력

### <a name="task-configuration-preset"></a>작업 구성(기본 설정)
**Azure Media Content Moderator**로 작업을 만드는 경우 구성 기본 설정을 지정해야 합니다. 다음은 콘텐츠 조정에 대한 구성 기본 설정입니다.

    {
      "version":"2.0"
    }

### <a name="net-code-sample"></a>.NET 코드 샘플

다음 .NET 코드 샘플은 Media Services .NET SDK를 사용하여 Content Moderator 작업을 실행합니다. 조정할 비디오를 포함하는 입력으로 Media Services 자산을 사용합니다.
전체 소스 코드 및 Visual Studio 프로젝트를 보려면 [Content Moderator 비디오 빠른 시작](../../cognitive-services/Content-Moderator/video-moderation-api.md)을 참조하세요.


```csharp
    /// <summary>
    /// Run the Content Moderator job on the designated Asset from local file or blob storage
    /// </summary>
    /// <param name="asset"></param>
    static void RunContentModeratorJob(IAsset asset)
    {
        // Grab the presets
        string configuration = File.ReadAllText(CONTENT_MODERATOR_PRESET_FILE);

        // grab instance of Azure Media Content Moderator MP
        IMediaProcessor mp = _context.MediaProcessors.GetLatestMediaProcessorByName(MEDIA_PROCESSOR);

        // create Job with Content Moderator task
        IJob job = _context.Jobs.Create(String.Format("Content Moderator {0}",
                asset.AssetFiles.First() + "_" + Guid.NewGuid()));

        ITask contentModeratorTask = job.Tasks.AddNew("Adult and racy classifier task",
                mp, configuration,
                TaskOptions.None);
        contentModeratorTask.InputAssets.Add(asset);
        contentModeratorTask.OutputAssets.AddNew("Adult and racy classifier output",
            AssetCreationOptions.None);

        job.Submit();


        // Create progress printing and querying tasks
        Task progressPrintTask = new Task(() =>
        {
            IJob jobQuery = null;
            do
            {
                var progressContext = _context;
                jobQuery = progressContext.Jobs
                .Where(j => j.Id == job.Id)
                    .First();
                    Console.WriteLine(string.Format("{0}\t{1}",
                    DateTime.Now,
                    jobQuery.State));
                    Thread.Sleep(10000);
             }
             while (jobQuery.State != JobState.Finished &&
             jobQuery.State != JobState.Error &&
             jobQuery.State != JobState.Canceled);
        });
        progressPrintTask.Start();

        Task progressJobTask = job.GetExecutionProgressTask(
        CancellationToken.None);
        progressJobTask.Wait();

        // If job state is Error, the event handling 
        // method for job progress should log errors.  Here we check 
        // for error state and exit if needed.
        if (job.State == JobState.Error)
        {
            ErrorDetail error = job.Tasks.First().ErrorDetails.First();
            Console.WriteLine(string.Format("Error: {0}. {1}",
            error.Code,
            error.Message));
        }

        DownloadAsset(job.OutputMediaAssets.First(), OUTPUT_FOLDER);
    }

For the full source code and the Visual Studio project, check out the [Content Moderator video quickstart](../../cognitive-services/Content-Moderator/video-moderation-api.md).

### JSON output

The following example of a Content Moderator JSON output was truncated.

> [!NOTE]
> Location of a keyframe in seconds = timestamp/timescale

    {
    "version": 2,
    "timescale": 90000,
    "offset": 0,
    "framerate": 50,
    "width": 1280,
    "height": 720,
    "totalDuration": 18696321,
    "fragments": [
    {
      "start": 0,
      "duration": 18000
    },
    {
      "start": 18000,
      "duration": 3600,
      "interval": 3600,
      "events": [
        [
          {
            "reviewRecommended": false,
            "adultScore": 0.00001,
            "racyScore": 0.03077,
            "index": 5,
            "timestamp": 18000,
            "shotIndex": 0
          }
        ]
      ]
    },
    {
      "start": 18386372,
      "duration": 119149,
      "interval": 119149,
      "events": [
        [
          {
            "reviewRecommended": true,
            "adultScore": 0.00000,
            "racyScore": 0.91902,
            "index": 5085,
            "timestamp": 18386372,
            "shotIndex": 62
          }
        ]
      ]
    }
    ]
    }
```

## <a name="media-services-learning-paths"></a>Media Services 학습 경로
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>피드백 제공
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-links"></a>관련 링크
[Azure Media Services 분석 개요](media-services-analytics-overview.md)

[Azure 미디어 분석 데모](https://azuremedialabs.azurewebsites.net/demos/Analytics.html)

## <a name="next-steps"></a>다음 단계

Content Moderator의 [비디오 조정 및 검토 솔루션](../../cognitive-services/Content-Moderator/video-moderation-human-review.md)에 대해 자세히 알아봅니다.

[비디오 조정 빠른 시작](../../cognitive-services/Content-Moderator/video-moderation-api.md)에서 전체 소스 코드 및 Visual Studio 프로젝트를 가져옵니다. 

조정된 출력에서 [비디오 검토](../../cognitive-services/Content-Moderator/video-reviews-quickstart-dotnet.md)를 생성하고 .NET에서 [기록을 조정](../../cognitive-services/Content-Moderator/video-transcript-reviews-quickstart-dotnet.md)하는 방법을 알아봅니다.

자세한 .NET [비디오 조정 및 검토 자습서](../../cognitive-services/Content-Moderator/video-transcript-moderation-review-tutorial-dotnet.md)를 확인합니다. 
