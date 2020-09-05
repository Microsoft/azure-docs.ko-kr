---
title: '예제: 실시간 비디오 분석 - Face'
titleSuffix: Azure Cognitive Services
description: Face 서비스를 사용하면 라이브 비디오 스트림에서 가져온 프레임을 거의 실시간으로 분석할 수 있습니다.
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: sample
ms.date: 03/01/2018
ms.author: sbowles
ms.custom: devx-track-csharp
ms.openlocfilehash: 9de803789b7ec11a971cf9a79a5c4665ac372cb4
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88928142"
---
# <a name="example-how-to-analyze-videos-in-real-time"></a>예제: 실시간으로 비디오를 분석하는 방법

이 가이드에서는 라이브 비디오 스트림에서 가져온 프레임을 거의 실시간으로 분석하는 방법을 보여 줍니다. 이러한 시스템의 기본 구성 요소는 다음과 같습니다.

- 비디오 소스에서 프레임 획득
- 분석할 프레임 선택
- API에 이러한 프레임 제출
- API 호출에서 반환된 각 분석 결과 사용

이러한 샘플은 C#으로 작성되었으며, 코드는 GitHub의 다음 경로에서 찾을 수 있습니다. [https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/)

## <a name="the-approach"></a>접근 방식

비디오 스트림을 거의 실시간으로 분석하는 문제를 해결하는 방법에는 여러 가지가 있습니다. 먼저 복잡한 수준에 따라 세 가지 접근 방식을 낮은 수준부터 간단하게 설명하겠습니다.

### <a name="a-simple-approach"></a>간단한 접근 방식

거의 실시간 분석 시스템을 위한 가장 간단한 디자인은 무한 루프로, 여기서는 각 반복에서 프레임을 잡아 분석한 다음, 결과를 사용합니다.

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

분석이 경량 클라이언트 쪽 알고리즘으로 구성되어 있는 경우 이 접근 방식이 적합합니다. 단, 클라우드에서 분석이 발생할 때 관련된 대기 시간은 API 호출에 수 초가 걸릴 수 있음을 의미합니다. 이 시간 동안 이미지를 캡처하지 않으며, 스레드는 기본적으로 아무런 작업도 수행하지 않습니다. 최대 프레임 속도는 API 호출의 대기 시간에 의해 제한됩니다.

### <a name="parallelizing-api-calls"></a>API 호출 병렬 처리

간단한 단일 스레드 루프는 경량 클라이언트 쪽 알고리즘에 적합하지만 클라우드 API 호출과 관련된 대기 시간에는 적합하지 않습니다. 이 문제에 대한 해결 방법은 프레임 잡기를 사용하여 장기 실행 API 호출이 병렬로 실행될 수 있도록 하는 것입니다. 예를 들어 C#에서 작업 기반 병렬 처리를 사용하여 이 작업을 완수할 수 있었습니다.

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

이 코드로 각 분석이 별도의 작업에서 시작되며, 새 프레임을 계속 잡는 동안 백그라운드에서 실행할 수 있습니다. 이 메서드를 사용하면 API 호출이 반환되기를 기다리는 동안 주 스레드를 차단하지는 않지만, 간단한 버전이 제공된 보장이 일부 손실됩니다. 여러 API 호출은 병렬로 발생할 수 있으며, 결과는 잘못된 순서로 반환될 수 있습니다. 이로 인해 여러 스레드가 동시에 ConsumeResult() 함수를 시작할 수 있으므로 함수가 스레드로부터 안전하지 않은 경우 위험할 수 있습니다. 마지막으로, 이 간단한 코드는 생성되는 작업을 추적하지 않으므로 예외가 자동으로 사라집니다. 따라서 추가할 최종 단계는 분석 작업을 추적하고 예외를 발생시키며 장기 실행 작업을 종료하고 결과가 올바른 순서로 사용되도록 하는 “소비자” 스레드입니다.

### <a name="a-producer-consumer-design"></a>생산자-소비자 디자인

최종 “생산자-소비자” 시스템에서는 이전의 무한 루프와 유사한 생산자 스레드가 있습니다. 그러나 분석 결과가 제공되는 즉시 사용하는 대신 생산자는 단순히 작업을 큐에 배치하여 추적합니다.

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

또한 큐에서 작업을 시작하고 완료될 때까지 기다린 다음, 결과를 표시하거나 throw된 예외를 발생하는 소비자 스레드도 있습니다. 큐를 사용하면 시스템의 최대 프레임 속도를 제한하지 않고 결과가 한 번에 하나씩, 올바른 순서로 사용되도록 할 수 있습니다.

```csharp
// Consumer thread. 
while (true)
{
    // Get the oldest task. 
    Task<ResultWrapper> analysisTask = taskQueue.Take();
 
    // Await until the task is completed. 
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

## <a name="implementing-the-solution"></a>솔루션 구현

### <a name="getting-started"></a>시작하기

앱을 가능한 한 빨리 실행하기 위해 위에서 설명한 유연한 시스템 구현을 사용할 것입니다. 코드에 액세스하려면 [https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis)로 이동합니다.

라이브러리에는 위에서 설명한 생산자-소비자 시스템을 구현하여 사용자가 웹캠의 비디오 프레임을 처리하는 FrameGrabber 클래스가 있습니다. 사용자가 정확한 형식의 API 호출을 지정할 수 있으며, 새 프레임이 획득되거나 새 분석 결과를 사용할 수 있는 경우 클래스가 이벤트를 사용하여 호출 코드에 알립니다.

몇 가지 가능성을 설명하기 위해 라이브러리를 사용하는 두 개의 샘플 앱이 있습니다. 첫 번째는 간단한 콘솔 앱이며, 간소화된 버전이 아래에 재현되어 있습니다. 이 앱은 기본 웹캠에서 프레임을 잡아 얼굴 감지를 위해 Face 서비스에 제출합니다.

```csharp
using System;
using VideoFrameAnalyzer;
using Microsoft.ProjectOxford.Face;
using Microsoft.ProjectOxford.Face.Contract;
     
namespace VideoFrameConsoleApplication
{
    class Program
    {
        static void Main(string[] args)
        {
            // Create grabber, with analysis type Face[]. 
            FrameGrabber<Face[]> grabber = new FrameGrabber<Face[]>();
            
            // Create Face Client. Insert your Face API key here.
            private readonly IFaceClient faceClient = new FaceClient(
            new ApiKeyServiceClientCredentials("<subscription key>"),
            new System.Net.Http.DelegatingHandler[] { });

            // Set up our Face API call.
            grabber.AnalysisFunction = async frame => return await faceClient.DetectAsync(frame.Image.ToMemoryStream(".jpg"));

            // Set up a listener for when we receive a new result from an API call. 
            grabber.NewResultAvailable += (s, e) =>
            {
                if (e.Analysis != null)
                    Console.WriteLine("New result received for frame acquired at {0}. {1} faces detected", e.Frame.Metadata.Timestamp, e.Analysis.Length);
            };
            
            // Tell grabber to call the Face API every 3 seconds.
            grabber.TriggerAnalysisOnInterval(TimeSpan.FromMilliseconds(3000));

            // Start running.
            grabber.StartProcessingCameraAsync().Wait();

            // Wait for keypress to stop
            Console.WriteLine("Press any key to stop...");
            Console.ReadKey();
            
            // Stop, blocking until done.
            grabber.StopProcessingAsync().Wait();
        }
    }
}
```

두 번째 샘플 앱은 좀 더 흥미로우며, 비디오 프레임에 대해 호출할 API를 선택할 수 있습니다. 앱의 왼쪽에는 라이브 비디오의 미리 보기가 표시되고, 오른쪽에는 가장 최근 API 결과가 해당 프레임에 겹쳐서 표시됩니다.

대부분의 모드에서는 왼쪽의 라이브 비디오와 오른쪽의 시각화된 분석 간에 눈에 띄는 지연이 발생합니다. 이 지연은 API 호출을 수행하는 데 걸리는 시간입니다. 단, Cognitive Services에 이미지를 제출하기 전에 OpenCV를 사용하여 클라이언트 컴퓨터에서 로컬로 얼굴을 감지하는 “EmotionsWithClientFaceDetect” 모드는 예외입니다. 이렇게 하면 감지된 얼굴을 즉시 시각화한 다음, 나중에 API 호출이 반환된 후 감정을 업데이트할 수 있습니다. 이 방법은 일부 간단한 처리를 클라이언트에서 수행한 다음, Cognitive Services API에서 필요한 경우 이 기능을 보다 고급 분석으로 보강할 수 있는 “하이브리드” 접근 방식의 예제입니다.

![HowToAnalyzeVideo](../../Video/Images/FramebyFrame.jpg)

### <a name="integrating-into-your-codebase"></a>코드베이스에 통합

이 샘플을 시작하려면 다음 단계를 따르세요.

1. [Azure 계정](https://azure.microsoft.com/free/cognitive-services/)을 만듭니다. 이미 있는 경우 다음 단계로 건너뛸 수 있습니다.
2. Azure Portal에서 Computer Vision 및 Face용 리소스를 만들어 키와 엔드포인트를 가져옵니다. 설치 중에 체험 계층(F0)을 선택했는지 확인합니다.
   - [Computer Vision](https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision)
   - [Face](https://portal.azure.com/#create/Microsoft.CognitiveServicesFace): 리소스를 배포한 후 **리소스로 이동**을 클릭하여 각 리소스에 대한 키와 엔드포인트를 수집합니다. 
3. [Cognitive-Samples-VideoFrameAnalysis](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/) GitHub 리포지토리를 복제합니다.
4. Visual Studio에서 샘플을 열고 샘플 애플리케이션을 빌드 및 실행합니다.
    - BasicConsoleSample의 경우 Face 키가  [BasicConsoleSample/Program.cs](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/blob/master/Windows/BasicConsoleSample/Program.cs)에 직접 하드 코딩되어 있습니다.
    - LiveCameraSample의 경우 앱의 설정 창에 키를 입력해야 합니다. 키는 사용자 데이터로 세션 간에 지속됩니다.
        

통합할 준비가 되면, **해당 프로젝트에서 VideoFrameAnalyzer 라이브러리를 참조합니다.** 

## <a name="summary"></a>요약

이 가이드에서는 Face, Computer Vision 및 Emotion API를 사용하여 라이브 비디오 스트림을 거의 실시간으로 실행하는 방법과 샘플 코드를 사용하여 시작하는 방법을 알아보았습니다.

[GitHub 리포지토리](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/)에서 자유롭게 피드백과 제안 사항을 보내주세요. 더 광범위한 API 피드백의 경우  [UserVoice 사이트](https://cognitive.uservoice.com/)를 이용하셔도 됩니다.

## <a name="related-topics"></a>관련 항목
- [이미지에서 얼굴을 식별하는 방법](HowtoIdentifyFacesinImage.md)
- [이미지에서 얼굴을 감지하는 방법](HowtoDetectFacesinImage.md)
