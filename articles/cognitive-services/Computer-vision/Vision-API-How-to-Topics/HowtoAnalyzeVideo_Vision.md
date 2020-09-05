---
title: 거의 실시간으로 비디오 분석 - Computer Vision
titleSuffix: Azure Cognitive Services
description: Computer Vision API를 사용하여 라이브 비디오 스트림에서 가져온 프레임을 거의 실시간으로 분석하는 방법을 알아봅니다.
services: cognitive-services
author: KellyDF
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: sample
ms.date: 09/09/2019
ms.author: kefre
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: c86b06ff46f1ddc8d22d2ab7ec4bc8620a8c862f
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88933174"
---
# <a name="analyze-videos-in-near-real-time"></a>거의 실시간으로 비디오 분석

이 문서에서는 Computer Vision API를 사용하여 라이브 비디오 스트림에서 가져온 프레임을 거의 실시간으로 분석하는 방법을 보여줍니다. 이러한 분석의 기본 요소는 다음과 같습니다.

- 비디오 소스의 프레임을 획득합니다.
- 분석할 프레임을 선택합니다.
- 이러한 프레임을 API에 제출합니다.
- API 호출에서 반환된 각 분석 결과를 사용합니다.

이 문서의 샘플은 C#으로 작성되었습니다. 코드에 액세스하려면 GitHub의 [비디오 프레임 분석 샘플](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/) 페이지로 이동합니다.

## <a name="approaches-to-running-near-real-time-analysis"></a>거의 실시간으로 분석을 실행하는 방법

비디오 스트림을 거의 실시간으로 분석해야 하는 문제를 해결하는 방법에는 여러 가지가 있습니다. 이 문서에서는 그 중 세 가지 방법을 복잡성 순서대로 간략하게 설명합니다.

### <a name="design-an-infinite-loop"></a>무한 루프 디자인

거의 실시간으로 분석하기 위한 가장 간단한 디자인은 무한 루프입니다. 이 루프가 반복될 때마다 프레임을 잡아 분석한 후 분석 결과를 사용합니다.

```csharp
while (true)
{
    Frame f = GrabFrame();
    if (ShouldAnalyze(f))
    {
        AnalysisResult r = await Analyze(f);
        ConsumeResult(r);
    }
}
```

분석을 경량 클라이언트 쪽 알고리즘으로 구성하려는 경우 이 방법이 적합합니다. 그러나 클라우드에서 분석이 발생하는 경우 그로 인한 대기 시간 때문에 API 호출에 수 초가 걸릴 수 있습니다. 이 시간 동안 이미지가 캡처되지 않으며, 스레드는 기본적으로 아무 것도 수행하지 않습니다. 최대 프레임 속도는 API 호출의 대기 시간에 의해 제한됩니다.

### <a name="allow-the-api-calls-to-run-in-parallel"></a>API 호출이 병렬로 실행되도록 허용

간단한 단일 스레드 루프는 경량 클라이언트 쪽 알고리즘에 적합하지만, 클라우드 API 호출과 관련된 대기 시간에는 적합하지 않습니다. 이 문제에 대한 해결 방법은 장기 실행 API 호출이 프레임 잡기와 병렬로 실행되도록 허용하는 것입니다. C#에서는 작업 기반 병렬 처리를 사용하여 이렇게 할 수 있습니다. 예를 들어 다음 코드를 실행하면 됩니다.

```csharp
while (true)
{
    Frame f = GrabFrame();
    if (ShouldAnalyze(f))
    {
        var t = Task.Run(async () =>
        {
            AnalysisResult r = await Analyze(f);
            ConsumeResult(r);
        }
    }
}
```

이 방법을 사용하면 각 분석을 별도의 작업에서 시작합니다. 새 프레임을 가져오는 동안 작업을 백그라운드에서 실행할 수 있습니다. 이 방법은 API 호출이 반환되기를 기다리는 동안 주 스레드가 차단되지 않습니다. 그러나 이 방법은 다음과 같은 단점이 있습니다.
* 간단한 버전이 제공된다는 약간의 보장이 필요합니다. 즉, 여러 API 호출이 병렬로 발생할 수 있으며, 그 결과가 잘못된 순서로 반환될 수 있습니다. 
* 또한 여러 스레드가 동시에 ConsumeResult() 함수를 시작할 수 있으므로 함수가 스레드로부터 안전하지 않은 경우 위험할 수 있습니다. 
* 마지막으로, 이 간단한 코드는 생성되는 작업을 추적하지 않으므로 예외가 자동으로 사라집니다. 따라서 분석 작업을 추적하고 예외를 발생시키고 장기 실행 작업을 종료하고 결과가 올바른 순서로 사용되도록 보장하는 “소비자” 스레드를 추가해야 합니다.

### <a name="design-a-producer-consumer-system"></a>생산자-소비자 시스템 디자인

마지막 방법으로 "생산자-소비자" 시스템을 디자인하려면 앞에서 언급한 무한 루프와 비슷한 생산자 스레드를 빌드합니다. 그러나 생산자는 분석 결과가 제공되는 즉시 사용하는 대신, 간단하게 작업을 큐에 배치하여 추적합니다.

```csharp
// Queue that will contain the API call tasks.
var taskQueue = new BlockingCollection<Task<ResultWrapper>>();

// Producer thread.
while (true)
{
    // Grab a frame.
    Frame f = GrabFrame();

    // Decide whether to analyze the frame.
    if (ShouldAnalyze(f))
    {
        // Start a task that will run in parallel with this thread.
        var analysisTask = Task.Run(async () =>
        {
            // Put the frame, and the result/exception into a wrapper object.
            var output = new ResultWrapper(f);
            try
            {
                output.Analysis = await Analyze(f);
            }
            catch (Exception e)
            {
                output.Exception = e;
            }
            return output;
        }

        // Push the task onto the queue.
        taskQueue.Add(analysisTask);
    }
}
```

또한 큐에서 작업을 시작하고 작업이 완료될 때까지 기다렸다가 결과를 표시하거나 throw된 예외를 발생시키는 소비자 스레드를 만듭니다. 큐를 사용하면 시스템의 최대 프레임 속도를 제한하지 않고 결과가 한 번에 하나씩, 올바른 순서로 사용되도록 보장할 수 있습니다.

```csharp
// Consumer thread.
while (true)
{
    // Get the oldest task.
    Task<ResultWrapper> analysisTask = taskQueue.Take();
 
    // Wait until the task is completed.
    var output = await analysisTask;

    // Consume the exception or result.
    if (output.Exception != null)
    {
        throw output.Exception;
    }
    else
    {
        ConsumeResult(output.Analysis);
    }
}
```

## <a name="implement-the-solution"></a>솔루션 구현

### <a name="get-started-quickly"></a>신속하게 시작하기

앱을 최대한 빠르게 실행하기 위해 이전 섹션에서 설명한 시스템을 구현했습니다. 이 시스템은 여러 시나리오를 수용할 수 있도록 유연하면서도 쉽게 사용할 수 있도록 제작되었습니다. 코드에 액세스하려면 GitHub의 [비디오 프레임 분석 샘플](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/) 페이지로 이동합니다.

라이브러리에는 앞에서 설명한 생산자-소비자 시스템을 구현하여 웹캠의 비디오 프레임을 처리하는 `FrameGrabber` 클래스가 있습니다. 사용자는 정확한 형식의 API 호출을 지정할 수 있으며, 클래스에서는 이벤트를 사용하여 새 프레임을 획득하거나 새 분석 결과를 사용할 수 있게 되면 그 사실을 호출 코드에 알립니다.

몇 가지 가능성을 설명하기 위해 라이브러리를 사용하는 두 개의 샘플 앱을 제공해드렸습니다. 

첫 번째 샘플 앱은 기본 웹캠에서 프레임을 가져와 얼굴 감지용 Face 서비스로 전송하는 간단한 콘솔 앱입니다. 이 앱의 간소화된 버전이 다음 코드에 재현되어 있습니다.

```csharp
using System;
using System.Linq;
using Microsoft.Azure.CognitiveServices.Vision.Face;
using Microsoft.Azure.CognitiveServices.Vision.Face.Models;
using VideoFrameAnalyzer;

namespace BasicConsoleSample
{
    internal class Program
    {
        const string ApiKey = "<your API key>";
        const string Endpoint = "https://<your API region>.api.cognitive.microsoft.com";

        private static async Task Main(string[] args)
        {
            // Create grabber.
            FrameGrabber<DetectedFace[]> grabber = new FrameGrabber<DetectedFace[]>();

            // Create Face Client.
            FaceClient faceClient = new FaceClient(new ApiKeyServiceClientCredentials(ApiKey))
            {
                Endpoint = Endpoint
            };

            // Set up a listener for when we acquire a new frame.
            grabber.NewFrameProvided += (s, e) =>
            {
                Console.WriteLine($"New frame acquired at {e.Frame.Metadata.Timestamp}");
            };

            // Set up a Face API call.
            grabber.AnalysisFunction = async frame =>
            {
                Console.WriteLine($"Submitting frame acquired at {frame.Metadata.Timestamp}");
                // Encode image and submit to Face service.
                return (await faceClient.Face.DetectWithStreamAsync(frame.Image.ToMemoryStream(".jpg"))).ToArray();
            };

            // Set up a listener for when we receive a new result from an API call.
            grabber.NewResultAvailable += (s, e) =>
            {
                if (e.TimedOut)
                    Console.WriteLine("API call timed out.");
                else if (e.Exception != null)
                    Console.WriteLine("API call threw an exception.");
                else
                    Console.WriteLine($"New result received for frame acquired at {e.Frame.Metadata.Timestamp}. {e.Analysis.Length} faces detected");
            };

            // Tell grabber when to call the API.
            // See also TriggerAnalysisOnPredicate
            grabber.TriggerAnalysisOnInterval(TimeSpan.FromMilliseconds(3000));

            // Start running in the background.
            await grabber.StartProcessingCameraAsync();

            // Wait for key press to stop.
            Console.WriteLine("Press any key to stop...");
            Console.ReadKey();

            // Stop, blocking until done.
            await grabber.StopProcessingAsync();
        }
    }
}
```

두 번째 샘플 앱은 좀 더 재미있습니다. 두 번째 앱은 비디오 프레임에서 호출할 API를 선택할 수 있습니다. 앱의 왼쪽에는 라이브 비디오의 미리 보기가 표시됩니다. 오른쪽에는 해당 프레임의 최신 API 결과가 중첩됩니다.

대부분의 모드에서는 왼쪽의 라이브 비디오와 오른쪽의 시각화된 분석 간에 시각적 지연이 발생합니다. 이 지연은 API 호출을 수행하는 데 걸리는 시간입니다. 단, Azure Cognitive Services에 이미지를 제출하기 전에 OpenCV를 사용하여 클라이언트 컴퓨터에서 로컬로 얼굴을 감지하는 “EmotionsWithClientFaceDetect” 모드는 예외입니다. 

이 접근 방식을 사용하면 감지된 얼굴을 즉시 시각화할 수 있습니다. 그런 다음, API 호출이 반환된 후 나중에 감정을 업데이트할 수 있습니다. 이는 "하이브리드" 접근 방식의 가능성을 보여줍니다. 즉, 간단한 처리 작업을 클라이언트에서 수행한 다음, 필요할 때 Cognitive Services API를 사용하여 이 처리 기능을 보다 발전된 분석으로 보강할 수 있습니다.

![태그가 있는 이미지를 표시하는 LiveCameraSample 앱](../../Video/Images/FramebyFrame.jpg)

### <a name="integrate-the-samples-into-your-codebase"></a>샘플을 코드베이스에 통합

이 샘플을 시작하려면 다음 단계를 수행합니다.

1. [Azure 계정](https://azure.microsoft.com/free/cognitive-services/)을 만듭니다. 이미 있는 경우 다음 단계로 건너뛸 수 있습니다.
2. Azure Portal에서 Computer Vision 및 Face용 리소스를 만들어 키와 엔드포인트를 가져옵니다. 설치 중에 체험 계층(F0)을 선택했는지 확인합니다.
   - [Computer Vision](https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision)
   - [Face](https://portal.azure.com/#create/Microsoft.CognitiveServicesFace): 리소스를 배포한 후 **리소스로 이동**을 클릭하여 각 리소스에 대한 키와 엔드포인트를 수집합니다. 
3. [Cognitive-Samples-VideoFrameAnalysis](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/) GitHub 리포지토리를 복제합니다.
4. Visual Studio 2015 이상에서 샘플을 열고, 샘플 애플리케이션을 빌드 및 실행합니다.
    - BasicConsoleSample의 경우 Face 키가 [BasicConsoleSample/Program.cs](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/blob/master/Windows/BasicConsoleSample/Program.cs)에 직접 하드 코딩되어 있습니다.
    - LiveCameraSample의 경우 앱의 **설정** 창에 키를 입력합니다. 키는 사용자 데이터로 세션 간에 지속됩니다.

샘플을 통합할 준비가 완료되면 해당 프로젝트에서 VideoFrameAnalyzer 라이브러리를 참조합니다.

VideoFrameAnalyzer의 이미지, 음성, 비디오 및 텍스트 해석 기능은 Azure Cognitive Services를 사용합니다. Microsoft는 사용자가 이 앱을 통해 업로드하는 이미지, 오디오, 비디오 및 기타 데이터를 수신하며, 서비스 개선 목적으로 사용할 수 있습니다. 앱이 개인 데이터를 Azure Cognitive Services에 전송하는 사용자를 보호할 수 있도록 도와주시기 바랍니다.

## <a name="summary"></a>요약

이 문서에서는 Face 및 Computer Vision 서비스를 사용하여 라이브 비디오 스트림을 거의 실시간으로 분석하는 방법을 알아보았습니다. 샘플 코드를 사용하여 시작하는 방법도 배웠습니다.

[GitHub 리포지토리](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/)에서 자유롭게 피드백과 의견을 남겨주세요. 보다 광범위한 API 피드백을 제공하려면 [UserVoice 사이트](https://cognitive.uservoice.com/)로 이동하세요.

