---
title: Azure Media Encoder Standard를 사용하여 비트 전송률 사다리 자동 생성 | Microsoft Docs
description: 이 항목에서는 MES(Media Encoder Standard)를 사용하여 입력 해상도 및 비트 전송률을 기반으로 비트 전송률 사다리를 자동 생성하는 방법을 보여 줍니다. 입력 해상도 및 비트 전송률은 초과되지 않습니다. 예를 들어, 입력이 3Mbps에서 720p이고 출력이 최적 시 720p로 유지되는 경우 3Mbps보다 낮은 전송률로 시작됩니다.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2019
ms.author: juliako
ms.openlocfilehash: bbaf4d490fcebb4cd741a9b83ffc5d7e85699755
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61224347"
---
#  <a name="use-azure-media-encoder-standard-to-auto-generate-a-bitrate-ladder"></a>Azure Media Encoder Standard를 사용하여 비트 전송률 사다리 자동 생성  

## <a name="overview"></a>개요

이 문서에서는 MES(Media Encoder Standard)를 사용하여 입력 해상도 및 비트 전송률을 기반으로 비트 전송률 사다리를 자동 생성하는 방법을 보여 줍니다. 자동 생성된 사전 설정은 입력 해상도 및 비트 전송률을 초과하지 않습니다. 예를 들어, 입력이 3Mbps에서 720p이고 출력이 최적 시 720p로 유지되는 경우 3Mbps보다 낮은 전송률로 시작됩니다.

### <a name="encoding-for-streaming-only"></a>스트리밍 전용 Encoding

원본 비디오를 스트리밍 전용으로 인코딩하려는 경우에는 인코딩 작업을 만들 때 "적응 스트리밍" 사전 설정을 사용해야 합니다. **적응 스트리밍** 사전 설정을 사용할 때 MES 인코더는 비트 전송률 사다리를 지능적으로 제한합니다. 하지만 서비스에서 사용할 레이어 수와 해상도를 결정하므로 인코딩 비용은 제어할 수 없습니다. **적응 스트리밍** 사전 설정을 사용하여 인코딩한 결과로 MES에 의해 생성된 출력 계층의 예제는 이 문서의 끝부분에서 확인할 수 있습니다. 출력 자산에는 오디오 및 비디오가 인터리빙되지 않은 MP4 파일이 포함됩니다.

### <a name="encoding-for-streaming-and-progressive-download"></a>스트리밍 및 점진적 다운로드용 Encoding

스트리밍을 수행하고 점진적 다운로드를 위한 MP4 파일을 생성하기 위해 원본 비디오를 인코딩하려는 경우에는 인코딩 작업을 만들 때 "콘텐츠 적응 다중 비트 전송률 MP4" 사전 설정을 사용해야 합니다. **콘텐츠 적응 다중 비트 전송률 MP4** 사전 설정을 사용할 때도 MES 인코더는 위에서 설명한 것과 동일한 인코딩 논리를 적용합니다. 그러나 이 경우 출력 자산에는 오디오와 비디오가 인터리빙되는 MP4 파일이 포함됩니다. 이러한 MP4 파일 중 하나(예: 비트 전송률이 가장 높은 버전)를 점진적 다운로드 파일로 사용할 수 있습니다.

## <a id="encoding_with_dotnet"></a>Media Services .NET SDK를 사용하여 Encoding

다음 코드 예제에서는 Media Services .NET SDK를 사용하여 다음 작업을 수행합니다.

- 인코딩 작업을 만듭니다.
- 미디어 인코더 표준 인코더에 대한 참조를 가져옵니다.
- 작업에 인코딩 작업(task)을 추가하고 **적응 스트리밍** 사전 설정을 사용하도록 지정합니다. 
- 인코딩된 자산을 포함하는 출력 자산을 만듭니다.
- 작업 진행 상태를 확인할 이벤트 처리기를 추가합니다.
- 작업을 제출합니다.

#### <a name="create-and-configure-a-visual-studio-project"></a>Visual Studio 프로젝트 만들기 및 구성

개발 환경을 설정하고 [.NET을 사용한 Media Services 환경](media-services-dotnet-how-to-use.md)에 설명된 대로 연결 정보를 사용하여 app.config 파일을 채웁니다. 

#### <a name="example"></a>예

```
using System;
using System.Configuration;
using System.Linq;
using Microsoft.WindowsAzure.MediaServices.Client;
using System.Threading;

namespace AdaptiveStreamingMESPresest
{
    class Program
    {
        // Read values from the App.config file.
        private static readonly string _AADTenantDomain =
            ConfigurationManager.AppSettings["AMSAADTenantDomain"];
        private static readonly string _RESTAPIEndpoint =
            ConfigurationManager.AppSettings["AMSRESTAPIEndpoint"];
        private static readonly string _AMSClientId =
            ConfigurationManager.AppSettings["AMSClientId"];
        private static readonly string _AMSClientSecret =
            ConfigurationManager.AppSettings["AMSClientSecret"];

        // Field for service context.
        private static CloudMediaContext _context = null;

        static void Main(string[] args)
        {
            AzureAdTokenCredentials tokenCredentials =
                new AzureAdTokenCredentials(_AADTenantDomain,
                    new AzureAdClientSymmetricKey(_AMSClientId, _AMSClientSecret),
                    AzureEnvironments.AzureCloudEnvironment);

            var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

            _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);

            // Get an uploaded asset.
            var asset = _context.Assets.FirstOrDefault();

            // Encode and generate the output using the "Adaptive Streaming" preset.
            EncodeToAdaptiveBitrateMP4Set(asset);

            Console.ReadLine();
        }

        static public IAsset EncodeToAdaptiveBitrateMP4Set(IAsset asset)
        {
            // Declare a new job.
            IJob job = _context.Jobs.Create("Media Encoder Standard Job");

            // Get a media processor reference, and pass to it the name of the 
            // processor to use for the specific task.
            IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");

            // Create a task
            ITask task = job.Tasks.AddNew("Media Encoder Standard encoding task",
            processor,
            "Adaptive Streaming",
            TaskOptions.None);

            // Specify the input asset to be encoded.
            task.InputAssets.Add(asset);
            // Add an output asset to contain the results of the job. 
            // This output is specified as AssetCreationOptions.None, which 
            // means the output asset is not encrypted. 
            task.OutputAssets.AddNew("Output asset",
            AssetCreationOptions.None);

            job.StateChanged += new EventHandler<JobStateChangedEventArgs>(JobStateChanged);
            job.Submit();
            job.GetExecutionProgressTask(CancellationToken.None).Wait();

            return job.OutputMediaAssets[0];
        }
        private static void JobStateChanged(object sender, JobStateChangedEventArgs e)
        {
            Console.WriteLine("Job state changed event:");
            Console.WriteLine("  Previous state: " + e.PreviousState);
            Console.WriteLine("  Current state: " + e.CurrentState);
            switch (e.CurrentState)
            {
                case JobState.Finished:
                    Console.WriteLine();
                    Console.WriteLine("Job is finished. Please wait while local tasks or downloads complete...");
                    break;
                case JobState.Canceling:
                case JobState.Queued:
                case JobState.Scheduled:
                case JobState.Processing:
                    Console.WriteLine("Please wait...\n");
                    break;
                case JobState.Canceled:
                case JobState.Error:

                    // Cast sender as a job.
                    IJob job = (IJob)sender;

                    // Display or log error details as needed.
                    break;
                default:
                    break;
            }
        }
        private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
        {
            var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
            ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();

            if (processor == null)
                throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));

            return processor;
        }
    }
}
```

## <a id="output"></a>출력

이 섹션에서는 **적응 스트리밍** 사전 설정으로 인코딩한 결과로 MES에 의해 생성된 출력 계층의 세 가지 예를 보여 줍니다. 

### <a name="example-1"></a>예 1
높이가 "1080"이고 프레임 속도가 "29.970"인 원본은 6개의 비디오 계층을 생성합니다.

|계층|높이|너비|비트 전송률(kbps)|
|---|---|---|---|
|1|1080|1920|6780|
|2|720|1280|3520|
|3|540|960|2210|
|4|360|640|1150|
|5|270|480|720|
|6|180|320|380|

### <a name="example-2"></a>예 2
높이가 "720"이고 프레임 속도가 "23.970"인 원본은 5개의 비디오 계층을 생성합니다.

|계층|높이|너비|비트 전송률(kbps)|
|---|---|---|---|
|1|720|1280|2940|
|2|540|960|1850|
|3|360|640|960|
|4|270|480|600|
|5|180|320|320|

### <a name="example-3"></a>예 3
높이가 "360"이고 프레임 속도가 "29.970"인 원본은 3개의 비디오 계층을 생성합니다.

|계층|높이|너비|비트 전송률(kbps)|
|---|---|---|---|
|1|360|640|700|
|2|270|480|440|
|3|180|320|230|
## <a name="media-services-learning-paths"></a>Media Services 학습 경로
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>피드백 제공
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>관련 항목
[Media Services Encoding 개요](media-services-encode-asset.md)

