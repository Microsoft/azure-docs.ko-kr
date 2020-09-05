---
title: '자습서: .NET에서 비디오 및 대본 조정 - Content Moderator'
titleSuffix: Azure Cognitive Services
description: 이 자습서는 머신 지원 조정 및 사람이 개입된(human-in-the-loop) 검토 생성을 통해 완벽한 비디오 및 대본 조정 솔루션을 빌드하는 방법을 이해하는 데 도움이 됩니다.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: tutorial
ms.date: 08/05/2020
ms.author: pafarley
ms.custom: devx-track-csharp
ms.openlocfilehash: 1f5a68bcf0069663d8ef1101407bea7ee26e9e8b
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88919293"
---
# <a name="tutorial-video-and-transcript-moderation"></a>자습서: 비디오 및 대본 조정

이 자습서에서는 머신 지원 조정 및 사람 검토 통합을 통해 완벽한 비디오 및 대본 조정 솔루션을 빌드하는 방법을 알아봅니다.

이 자습서에서는 다음을 수행하는 방법에 대해 설명합니다.

> [!div class="checklist"]
> - 신속한 처리를 위해 입력 비디오 압축
> - 비디오를 조정하여 인사이트가 있는 촬영 및 프레임 가져오기
> - 프레임 타임스탬프를 사용하여 썸네일(이미지) 만들기
> - 타임스탬프 및 썸네일을 제출하여 비디오 검토 만들기
> - Media Indexer API를 사용하여 비디오 음성을 텍스트(대본)로 변환
> - 텍스트 조정 서비스를 사용하여 대본 조정
> - 비디오 검토에 조정된 대본 추가

## <a name="prerequisites"></a>사전 요구 사항

- [Content Moderator 검토 도구](https://contentmoderator.cognitive.microsoft.com/) 웹 사이트에 가입하고 사용자 지정 태그를 만듭니다. 이 단계에 대한 도움말이 필요하면 [태그 사용](Review-Tool-User-Guide/tags.md)을 참조하세요.

    ![비디오 조정 사용자 지정 태그 스크린샷](images/video-tutorial-custom-tags.png)
- 샘플 애플리케이션을 실행하려면 Azure 계정, Azure Media Services 리소스, Azure Content Moderator 리소스 및 Azure Active Directory 자격 증명이 필요합니다. 이러한 리소스를 가져오는 방법에 대한 지침은 [비디오 조정 API](video-moderation-api.md) 가이드를 참조하세요.
- GitHub에서 [비디오 검토 콘솔 애플리케이션](https://github.com/MicrosoftContentModerator/VideoReviewConsoleApp)을 다운로드합니다.

## <a name="enter-credentials"></a>자격 증명 입력

`App.config` 파일을 편집하고 Active Directory 테넌트 이름, 서비스 엔드포인트 및 `#####`에서 표시한 구독 키를 추가합니다. 다음 정보가 필요합니다.

|키|Description|
|-|-|
|`AzureMediaServiceRestApiEndpoint`|AMS(Azure Media Services) API에 대한 엔드포인트|
|`ClientSecret`|Azure Media Services에 대한 구독 키|
|`ClientId`|Azure Media Services에 대한 클라이언트 ID|
|`AzureAdTenantName`|소속 조직을 나타내는 Active Directory 테넌트 이름|
|`ContentModeratorReviewApiSubscriptionKey`|Content Moderator 검토 API에 대한 구독 키|
|`ContentModeratorApiEndpoint`|Content Moderator API에 대한 엔드포인트|
|`ContentModeratorTeamId`|Content Moderator 팀 ID|

## <a name="examine-the-main-code"></a>기본 코드 검사

`Program.cs`의 클래스 `Program`은 비디오 조정 애플리케이션에 대한 기본 진입점입니다.

### <a name="methods-of-program-class"></a>Program 클래스 메서드

|방법|Description|
|-|-|
|`Main`|명령줄을 구문 분석하고 사용자 입력을 수집하고 처리를 시작합니다.|
|`ProcessVideo`|비디오 검토를 압축하고 업로드하고 조정하고 생성합니다.|
|`CreateVideoStreamingRequest`|스트림을 만들어 비디오 업로드|
|`GetUserInputs`|명령줄 옵션이 없을 때 사용된 사용자 입력 수집|
|`Initialize`|조정 프로세스에 필요한 개체 초기화|

### <a name="the-main-method"></a>기본 메서드

`Main()`은 실행이 시작되는 위치이므로 비디오 중재 프로세스를 이해하기 시작하는 장소입니다.

[!code-csharp[Main](~/VideoReviewConsoleApp/Microsoft.ContentModerator.AMSComponent/AMSComponentClient/Program.cs?range=20-24,42-52,54-74)]

`Main()`은 다음 명령줄 인수를 처리합니다.

- 조정을 위해 제출될 MPEG-4 비디오 파일을 포함하는 디렉터리 경로입니다. 이 디렉터리 및 하위 디렉터리의 모든 `*.mp4` 파일이 조정을 위해 제출됩니다.
- 필요에 따라 텍스트 대본이 오디오 조정을 위해 생성되어야 하는지 여부를 나타내는 부울(true/false) 플래그입니다.

명령줄 인수가 없는 경우 `Main()`은 `GetUserInputs()`을 호출합니다. 이 메서드는 사용자가 단일 비디오 파일의 경로를 입력하고 텍스트 대본을 생성해야 할지 여부를 지정하게 합니다.

> [!NOTE]
> 콘솔 애플리케이션은 [Azure Media Indexer API](https://docs.microsoft.com/azure/media-services/media-services-process-content-with-indexer2)를 사용하여 업로드된 비디오의 오디오 트랙에서 대본을 생성합니다. 결과는 WebVTT 형식으로 제공됩니다. 이 형식에 대한 자세한 내용은 [Web Video Text Tracks 형식](https://developer.mozilla.org/docs/Web/API/WebVTT_API)을 참조하세요.

### <a name="initialize-and-processvideo-methods"></a>초기화 및 ProcessVideo 메서드

대화형 사용자 입력 또는 명령줄에서 프로그램 옵션이 제공되는지 여부에 관계 없이 `Main()`은 `Initialize()`를 호출하여 다음 인스턴스를 만듭니다.

|클래스|Description|
|-|-|
|`AMSComponent`|조정을 위해 제출하기 전에 비디오 파일을 압축합니다.|
|`AMSconfigurations`|`App.config`에 있는 애플리케이션의 구성 데이터에 대한 인터페이스입니다.|
|`VideoModerator`| AMS SDK를 사용하여 업로드, 인코딩, 암호화 및 조정|
|`VideoReviewApi`|Content Moderator 서비스에서 비디오 검토 관리|

이러한 클래스(간단한 `AMSConfigurations` 제외)에 대해서는 이 자습서의 이후 섹션에서 자세히 설명합니다.

마지막으로 비디오 파일은 각각에 대해 `ProcessVideo()`를 호출하여 한 번에 하나씩 처리됩니다.

[!code-csharp[ProcessVideo](~/VideoReviewConsoleApp/Microsoft.ContentModerator.AMSComponent/AMSComponentClient/Program.cs?range=76-118)]

`ProcessVideo()` 메서드는 상당히 간단합니다. 순서대로 다음 작업을 수행합니다.

- 비디오 압축
- Azure Media Services 자산에 비디오 업로드
- AMS 작업을 만들어 비디오 조정
- Content Moderator에서 비디오 검토 만들기

다음 섹션에서는 `ProcessVideo()`에서 호출한 일부 개별 프로세스를 좀 더 자세히 살펴봅니다. 

## <a name="compress-the-video"></a>비디오 압축

네트워크 트래픽을 최소화하려면 애플리케이션은 비디오 파일을 H.264(MPEG-4 AVC) 형식으로 변환하고 최대 너비 640픽셀로 크기를 조정합니다. 높은 효율성(압축률) 때문에 H.264 코덱이 권장됩니다. 압축은 Visual Studio 솔루션의 `Lib` 폴더에 포함된 무료 `ffmpeg` 명령줄 도구를 사용하여 이루어집니다. 입력 파일은 가장 일반적으로 사용되는 비디오 파일 형식 및 코덱을 포함하여 `ffmpeg`에서 지원하는 모든 형식이 될 수 있습니다.

> [!NOTE]
> 명령줄 옵션을 사용하여 프로그램을 시작할 경우 조정을 위해 제출될 비디오 파일을 포함하는 디렉터리를 지정합니다. `.mp4` 파일 이름 확장명를 가진 이 디렉터리의 모든 파일이 처리됩니다. 다른 파일 이름 확장명을 처리하려면 `Program.cs`의 `Main()` 메서드를 업데이트하여 원하는 확장명을 포함시킵니다.

단일 비디오 파일을 압축하는 코드는 `AMSComponent.cs`의 `AmsComponent` 클래스입니다. 이 기능을 담당하는 메서드는 여기에 표시된 `CompressVideo()`입니다.

[!code-csharp[CompressVideo](~/VideoReviewConsoleApp/Microsoft.ContentModerator.AMSComponent/AMSComponentClient/AMSComponent.cs?range=31-59)]

이 코드는 다음 단계를 수행합니다.

- `App.config`의 구성이 필요한 모든 데이터를 포함하는지 확인
- `ffmpeg` 이진이 존재하는지 확인
- 파일의 기본 이름(예: `Example.mp4` -> `Example_c.mp4`)에 `_c.mp4`를 추가하여 출력 파일 이름 빌드
- 명령줄 문자열을 빌드하여 변환 수행
- 명령줄을 사용하는 `ffmpeg` 프로세스 시작
- 처리될 비디오 대기

> [!NOTE]
> 이미 비디오가 H.264를 사용하여 압축되고 적절한 크기를 갖고 있음을 아는 경우 압축하지 않으려면 `CompressVideo()`를 다시 생성합니다.

메서드는 압축된 출력 파일의 파일 이름을 반환합니다.

## <a name="upload-and-moderate-the-video"></a>비디오 업로드 및 조정

비디오는 Content Moderation 서비스에서 처리하기 전에 Azure Media Services에 저장되어야 합니다. `Program.cs`의 `Program` 클래스에는 비디오를 업로드하는 데 사용된 스트리밍 요청을 나타내는 개체를 반환하는 짧은 메서드 `CreateVideoStreamingRequest()`가 있습니다.

[!code-csharp[CreateVideoStreamingRequest](~/VideoReviewConsoleApp/Microsoft.ContentModerator.AMSComponent/AMSComponentClient/Program.cs?range=120-133)]

결과 `UploadVideoStreamRequest` 개체는 `UploadVideoStreamRequest.cs`(및 부모인 `UploadVideoRequest.cs`의 `UploadVideoRequest`)에서 정의됩니다. 이러한 클래스는 여기에 표시되지 않고, 짧으며, 압축된 비디오 데이터 및 이에 대한 정보를 보유하기 위해서만 사용합니다. 다른 데이터 전용 클래스 `UploadAssetResult`(`UploadAssetResult.cs`)는 업로드 프로세스의 결과를 보유하는 데 사용됩니다. 이제 `ProcessVideo()`에서 이러한 줄을 이해할 수 있습니다.

[!code-csharp[ProcessVideoSnippet](~/VideoReviewConsoleApp/Microsoft.ContentModerator.AMSComponent/AMSComponentClient/Program.cs?range=91-104)]

이러한 줄은 다음 작업을 수행합니다.

- `UploadVideoStreamRequest`를 만들어 압축된 비디오 업로드
- 사용자가 텍스트 대본을 요청한 경우 요청의 `GenerateVTT` 플래그 설정
- 업로드를 수행하고 결과를 받으려면 `CreateAzureMediaServicesJobToModerateVideo()` 호출

## <a name="examine-video-moderation-code"></a>비디오 조정 코드 검토

메서드 `CreateAzureMediaServicesJobToModerateVideo()`는 Azure Media Services와 상호 작용하는 대량의 코드를 포함하는 `VideoModerator.cs`에 있습니다. 메서드의 소스 코드는 다음 추출에 표시됩니다.

[!code-csharp[CreateAzureMediaServicesJobToModerateVideo](~/VideoReviewConsoleApp/Microsoft.ContentModerator.AMSComponent/AMSComponentClient/VideoModerator.cs?range=230-283)]

이 코드는 다음 작업을 수행합니다.

- 수행될 처리에 대한 AMS 작업 만들기
- 비디오 파일 인코딩, 조정 및 텍스트 대본 생성에 대한 작업 추가
- 작업 제출, 파일 업로드 및 처리 시작
- 조정 결과, 텍스트 대본(요청할 경우) 및 기타 정보 검색

## <a name="sample-video-moderation-output"></a>샘플 비디오 조정 출력

비디오 조정 작업의 결과([비디오 조정 빠른 시작](video-moderation-api.md) 참조)는 조정 결과를 포함하는 JSON 데이터 구조입니다. 이러한 결과는 비디오 내의 조각(샷)에 대한 분석 결과를 포함하며 각 비디오는 검토를 위해 플래그가 지정된 키 프레임이 있는 이벤트(클립)을 포함합니다. 각 키 프레임은 성인 또는 선정적인 콘텐츠를 포함할 가능성에 따라 점수가 매겨집니다. 다음 예제에서는 JSON 응답을 보여 줍니다.

```json
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

비디오에서 오디오의 전사는 `GenerateVTT` 플래그가 설정되는 경우 생성됩니다.

> [!NOTE]
> 콘솔 애플리케이션은 [Azure Media Indexer API](https://docs.microsoft.com/azure/media-services/media-services-process-content-with-indexer2)를 사용하여 업로드된 비디오의 오디오 트랙에서 대본을 생성합니다. 결과는 WebVTT 형식으로 제공됩니다. 이 형식에 대한 자세한 내용은 [Web Video Text Tracks 형식](https://developer.mozilla.org/docs/Web/API/WebVTT_API)을 참조하세요.

## <a name="create-a-human-review"></a>휴먼 검토 만들기

조정 프로세스는 비디오에서 오디오 트랙의 대본과 함께 키 프레임 목록을 반환합니다. 다음 단계는 사람 조정자를 위해 Content Moderator 검토 도구에서 검토를 만드는 것입니다. `Program.cs`의 `ProcessVideo()` 메서드로 돌아가 `CreateVideoReviewInContentModerator()` 메서드에 대한 호출을 봅니다. 이 메서드는 `VideoReviewAPI.cs`에 있는 `videoReviewApi` 클래스에 있으며, 여기에 표시됩니다.

[!code-csharp[CreateVideoReviewInContentModerator](~/VideoReviewConsoleApp/Microsoft.ContentModerator.AMSComponent/AMSComponentClient/VideoReviewAPI.cs?range=42-69)]

`CreateVideoReviewInContentModerator()`는 다음 작업을 수행하려면 다른 여러 메서드를 호출합니다.

> [!NOTE]
> 콘솔 애플리케이션은 썸네일을 생성하기 위한 [FFmpeg](https://ffmpeg.org/) 라이브러리를 사용합니다. 이러한 썸네일(이미지)은 비디오 조정 출력의 프레임 타임스탬프에 해당합니다.

|Task|메서드|파일|
|-|-|-|
|비디오에서 키 프레임 추출 및 키 프레임의 썸네일 이미지 만들기|`CreateVideoFrames()`<br>`GenerateFrameImages()`|`FrameGeneratorServices.cs`|
|성인 또는 선정적인 오디오를 찾으려면 사용 가능한 경우 텍스트 대본 검사|`GenerateTextScreenProfanity()`| `VideoReviewAPI.cs`|
|휴먼 검사를 위한 비디오 검토 요청 준비 및 제출|`CreateReviewRequestObject()`<br> `ExecuteCreateReviewApi()`<br>`CreateAndPublishReviewInContentModerator()`|`VideoReviewAPI.cs`|

다음 화면에서는 이전 단계의 결과를 보여줍니다.

![비디오 검토 기본 보기](images/video-tutorial-default-view.PNG)

## <a name="process-the-transcript"></a>대본 처리

지금까지 이 자습서에 제공된 코드는 시각적 콘텐츠에 중점을 뒀습니다. 음성 콘텐츠 검토는 언급했듯이 오디오에서 생성된 대본을 사용하는 별도의 선택적 프로세스입니다. 이제 텍스트 대본이 검토 프로세스에서 생성되고 사용되는 방법에 대해 살펴볼 때가 됐습니다. 대본 생성 작업은 [Azure Media Indexer](https://docs.microsoft.com/azure/media-services/media-services-index-content) 서비스의 몫입니다.

이 애플리케이션은 다음 작업을 수행합니다.

|Task|메서드|파일|
|-|-|-|
|텍스트 대본이 생성되는지 여부를 결정|`Main()`<br>`GetUserInputs()`|`Program.cs`|
|생성된다면 조정의 일부로 전사 작업 제출|`ConfigureTranscriptTask()`|`VideoModerator.cs`|
|대화의 로컬 복사본 가져오기|`GenerateTranscript()`|`VideoModerator.cs`|
|부적절한 오디오를 포함하는 비디오의 플래그 프레임|`GenerateTextScreenProfanity()`<br>`TextScreen()`|`VideoReviewAPI.cs`|
|검토에 결과 추가|`UploadScreenTextResult()`<br>`ExecuteAddTranscriptSupportFile()`|`VideoReviewAPI.cs`|

### <a name="task-configuration"></a>작업 구성

전사 작업 제출로 바로 들어가겠습니다. `CreateAzureMediaServicesJobToModerateVideo()`(이미 설명된)는 `ConfigureTranscriptTask()`를 호출합니다.

[!code-csharp[ConfigureTranscriptTask](~/VideoReviewConsoleApp/Microsoft.ContentModerator.AMSComponent/AMSComponentClient/VideoModerator.cs?range=295-304)]

솔루션의 `Lib` 폴더의 파일 `MediaIndexerConfig.json`에서 대본 작업에 대한 구성을 읽습니다. 전사 프로세스의 출력 및 구성 파일에 대해 AMS 자산을 생성합니다. AMS 작업이 실행될 때 이 작업은 비디오 파일의 오디오 트랙에서 텍스트 대본을 만듭니다.

> [!NOTE]
> 샘플 애플리케이션은 미국 영어로 된 음성만 인식합니다.

### <a name="transcript-generation"></a>대본 생성

대본은 AMS 자산으로 게시됩니다. 대본에서 불쾌한 콘텐츠를 검사하려면 애플리케이션은 Azure Media Services에서 자산을 다운로드합니다. `CreateAzureMediaServicesJobToModerateVideo()`는 여기에 표시된 `GenerateTranscript()`를 호출하여 파일을 검색합니다.

[!code-csharp[GenerateTranscript](~/VideoReviewConsoleApp/Microsoft.ContentModerator.AMSComponent/AMSComponentClient/VideoModerator.cs?range=351-370)]

필요한 일부 AMS 설치 후 실제 다운로드는 AMS 자산을 로컬 파일로 복사하는 제네릭 함수인 `DownloadAssetToLocal()`을 호출하여 수행됩니다.

## <a name="moderate-the-transcript"></a>대본 조정

대본을 가까이에 두면 이를 검토에서 검사하고 사용합니다. 검토 만들기는 작업을 수행하려면 `GenerateTextScreenProfanity()`를 호출하는 `CreateVideoReviewInContentModerator()`의 권한입니다. 결국 이 메서드는 대부분의 기능을 포함하는 `TextScreen()`을 호출합니다.

`TextScreen()`은 다음 작업을 수행합니다.

- 타임스탬프 및 캡션에 대한 대본 구문 분석
- 텍스트 조정에 대해 각 캡션 제출
- 불쾌한 음성 콘텐츠가 있을 수 있는 모든 프레임에 플래그 지정

이러한 작업 각각에 대해 보다 자세히 살펴보겠습니다.

### <a name="initialize-the-code"></a>코드 초기화

먼저, 모든 변수 및 컬렉션을 초기화합니다.

[!code-csharp[TextScreen](~/VideoReviewConsoleApp/Microsoft.ContentModerator.AMSComponent/AMSComponentClient/VideoReviewAPI.cs?range=515-527)]

### <a name="parse-the-transcript-for-captions"></a>캡션에 대한 대본 구문 분석

다음으로, 캡션 및 타임스탬프에 대해 VTT 형식의 대본을 구문 분석합니다. 검토 도구는 비디오 검토 화면의 대본 탭에 이러한 캡션을 표시합니다. 타임스탬프는 해당 비디오 프레임과 캡션을 동기화하는 데 사용됩니다.

[!code-csharp[TextScreen2](~/VideoReviewConsoleApp/Microsoft.ContentModerator.AMSComponent/AMSComponentClient/VideoReviewAPI.cs?range=528-567)]

### <a name="moderate-captions-with-the-text-moderation-service"></a>텍스트 조정 서비스를 사용하여 캡션 조정

다음으로, Content Moderator의 텍스트 API를 사용하여 구문 분석된 텍스트 캡션을 검사합니다.

> [!NOTE]
> Content Moderator 서비스 키에는 RPS(초당 요청 수) 속도 제한이 있습니다. 제한을 초과하는 경우 SDK는 429 오류 코드로 예외를 throw합니다.
>
> 체험 계층 키에는 하나의 RPS 속도 제한이 있습니다.

[!code-csharp[TextScreen3](~/VideoReviewConsoleApp/Microsoft.ContentModerator.AMSComponent/AMSComponentClient/VideoReviewAPI.cs?range=568-653)]

### <a name="text-moderation-breakdown"></a>텍스트 조정 분석

`TextScreen()`은 중요한 메서드이므로 이를 분석해보겠습니다.

1. 먼저, 메서드에서는 대본 파일을 한 줄씩 읽습니다. 신뢰도 점수에 따라 `NOTE`를 포함한 줄 및 빈 줄은 무시합니다. 파일의 *큐*에서 타임스탬프 및 텍스트 항목을 추출합니다. 큐는 오디오 트랙의 텍스트를 나타내고 시작 및 종료 시간을 포함합니다. 큐는 문자열 `-->`가 있는 타임스탬프 줄에서 시작하며, 텍스트의 하나 이상의 줄이 이어집니다.

1. `CaptionScreentextResult`(`TranscriptProfanity.cs`에 정의된)의 인스턴스는 각 큐에서 구문 분석된 정보를 보유하는 데 사용됩니다.  새 타임스탬프 줄이 검색되거나 최대 텍스트 길이 1024자에 도달하면 새 `CaptionScreentextResult`가 `csrList`에 추가됩니다. 

1. 다음으로, 메서드는 각 큐를 텍스트 조정 API에 제출하고, `Microsoft.Azure.CognitiveServices.ContentModerator` 어셈블리에서 정의되는 `ContentModeratorClient.TextModeration.DetectLanguageAsync()` 및 `ContentModeratorClient.TextModeration.ScreenTextWithHttpMessagesAsync()` 둘 다 호출합니다. 속도가 제한되는 것을 방지하려면 메서드는 1초 동안 일시 중지한 후 각 큐를 제출합니다.

1. 텍스트 조정 서비스에서 결과를 받은 후 메서드는 그 결과를 분석하여 신뢰도 임계값을 충족하는지 여부를 확인합니다. 이러한 값은 `App.config`에 `OffensiveTextThreshold`, `RacyTextThreshold` 및 `AdultTextThreshold`로 설정됩니다. 마지막으로, 불쾌한 용어 자체도 저장됩니다. 큐의 시간 범위 내에 있는 모든 프레임은 불쾌하고 선정적 및/또는 성인 텍스트를 포함하는 것으로 플래그가 지정됩니다.

1. `TextScreen()`은 전체적으로 비디오에서 텍스트 조정 결과를 포함하는 `TranscriptScreenTextResult` 인스턴스를 반환합니다. 이 개체는 불쾌한 용어 목록과 함께 다양한 유형의 불쾌한 콘텐츠에 대한 플래그 및 점수를 포함합니다. 호출자 `CreateVideoReviewInContentModerator()`는 `UploadScreenTextResult()`를 호출하여 인간 검토자에게 사용할 수 있도록 이 정보를 검토에 연결합니다.

다음 화면은 대본 생성 및 조정 단계의 결과를 보여줍니다.

![비디오 조정 대본 보기](images/video-tutorial-transcript-view.PNG)

## <a name="program-output"></a>프로그램 출력

프로그램에서 다음 명령줄 출력은 완료될 때 다양한 작업을 보여줍니다. 또한, 조정 결과(JSON 형식) 및 음성 대본은 원본 비디오 파일과 같은 디렉터리에서 사용할 수 있습니다.

```console
Microsoft.ContentModerator.AMSComponentClient
Enter the fully qualified local path for Uploading the video :
"Your File Name.MP4"
Generate Video Transcript? [y/n] : y

Video compression process started...
Video compression process completed...

Video moderation process started...
Video moderation process completed...

Video review process started...
Video Frames Creation inprogress...
Frames(83) created successfully.
Review Created Successfully and the review Id 201801va8ec2108d6e043229ba7a9e6373edec5
Video review successfully completed...

Total Elapsed Time: 00:05:56.8420355
```

## <a name="next-steps"></a>다음 단계

이 자습서에서는 &mdash;대본 콘텐츠를 포함&mdash;하는 비디오 콘텐츠를 조정하고 검토 도구에서 검토를 만드는 애플리케이션을 설정합니다. 다음으로, 비디오 조정에 대해 자세히 알아보세요.

> [!div class="nextstepaction"]
> [비디오 조정](./video-moderation-human-review.md)
