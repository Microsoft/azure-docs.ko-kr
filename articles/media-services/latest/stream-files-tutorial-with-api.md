---
title: Media Services v3를 사용하여 업로드, 인코딩 및 스트리밍
titleSuffix: Azure Media Services
description: Azure Media Services v3를 사용하여 파일을 업로드하고, 비디오를 인코딩하고, 콘텐츠를 스트리밍하는 방법을 보여주는 자습서입니다.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: tutorial
ms.custom: mvc
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: eedbb63f4928c0397150b40a47fdc7c3e87d1991
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89256772"
---
# <a name="tutorial-upload-encode-and-stream-videos-with-media-services-v3"></a>자습서: Media Services v3를 사용하여 비디오 업로드, 인코딩 및 스트리밍

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

> [!NOTE]
> 이 자습서에서 [.NET SDK](/dotnet/api/microsoft.azure.management.media.models.liveevent?view=azure-dotnet) 예제를 사용하지만, 일반적인 단계는 [REST API](/rest/api/media/liveevents), [CLI](/cli/azure/ams/live-event?view=azure-cli-latest) 또는 지원되는 기타 [SDK](media-services-apis-overview.md#sdks)와 동일합니다.

Azure Media Services를 사용하면 다양한 브라우저 및 디바이스에서 재생되는 형식으로 미디어 파일을 인코딩할 수 있습니다. 예를 들어 콘텐츠를 Apple의 HLS 또는 MPEG DASH 형식으로 스트림할 수 있습니다. 스트림하기 전에 고품질 디지털 미디어 파일을 인코딩해야 합니다. 인코딩에 대한 도움말은 [인코딩 개념](encoding-concept.md)을 참조하세요. 이 자습서는 로컬 비디오 파일을 업로드하고 업로드된 파일을 인코딩합니다. 콘텐츠를 인코딩하고 HTTPS URL을 통해 액세스할 수 있게 만들 수도 있습니다. 자세한 내용은 [HTTP URL에서 작업 입력 만들기](job-input-from-http-how-to.md)를 참조하세요.

![Azure Media Player로 비디오 재생](./media/stream-files-tutorial-with-api/final-video.png)

이 자습서에서는 다음을 수행하는 방법에 대해 설명합니다.

> [!div class="checklist"]
> * 토픽에 설명된 샘플 앱 다운로드
> * 업로드, 인코딩 및 스트리밍하는 코드 검사
> * 앱을 실행합니다.
> * 스트리밍 URL 테스트
> * 리소스를 정리합니다.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>필수 구성 요소

- Visual Studio가 설치되지 않은 경우 [Visual Studio Community 2019](https://www.visualstudio.com/thank-you-downloading-visual-studio/?sku=Community&rel=15)를 가져올 수 있습니다.
- [Media Services 계정 만들기](./create-account-howto.md)<br/>리소스 그룹 이름 및 Media Services 계정 이름에 사용한 값을 기억해 두세요.
- [Azure CLI를 사용하여 Azure Media Services API 액세스](./access-api-howto.md)의 단계를 수행하고 자격 증명을 저장합니다. API에 액세스할 때 필요합니다.

## <a name="download-and-set-up-the-sample"></a>샘플 다운로드 및 설정

다음 명령을 사용하여 스트리밍 .NET 샘플이 포함된 GitHub 리포지토리를 머신에 복제합니다.  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git
 ```

샘플은 [UploadEncodeAndStreamFiles](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/tree/master/AMSV3Tutorials/UploadEncodeAndStreamFiles) 폴더에 있습니다.

다운로드한 프로젝트에서 [appsettings.json](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/appsettings.json) 파일을 엽니다. 값을 [API 액세스](./access-api-howto.md)에서 가져온 자격 증명으로 바꿉니다.

## <a name="examine-the-code-that-uploads-encodes-and-streams"></a>업로드, 인코딩 및 스트림하는 코드 검사

이 섹션에서는 *UploadEncodeAndStreamFiles* 프로젝트의 [Program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs) 파일에 정의된 함수를 살펴봅니다.

샘플은 다음 작업을 수행합니다.

1. 새 **변환**을 만듭니다(먼저 지정된 변환이 있는지 확인합니다).
2. 인코딩 **작업**의 출력으로 사용되는 출력 **자산**을 만듭니다.
3. 입력 **자산**을 만들고 지정된 로컬 비디오 파일을 업로드합니다. 자산은 작업의 입력으로 사용됩니다.
4. 만든 입력 및 출력을 사용하여 인코딩 작업을 제출합니다.
5. 작업의 상태를 확인합니다.
6. **스트리밍 로케이터**를 만듭니다.
7. 스트리밍 URL을 빌드합니다.

### <a name="start-using-media-services-apis-with-net-sdk"></a>.NET SDK로 Media Services API 사용하기

.NET으로 Media Services API를 사용하려면 **AzureMediaServicesClient** 개체를 만들어야 합니다. 개체를 만들려면 Azure AD를 사용하여 클라이언트가 Azure에 연결하는 데 필요한 자격 증명을 제공해야 합니다. 아티클의 시작 부분에서 복제한 코드에서 **GetCredentialsAsync** 함수는 로컬 구성 파일에 제공된 자격 증명에 따라 ServiceClientCredentials 개체를 만듭니다.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateMediaServicesClient)]

### <a name="create-an-input-asset-and-upload-a-local-file-into-it"></a>입력 자산을 만들고 여기에 로컬 파일 업로드

**CreateInputAsset** 함수는 새로운 입력 [Asset](/rest/api/media/assets)을 만들고 이 자산에 지정된 로컬 비디오 파일을 업로드합니다. 이 **자산**은 인코딩 작업에 대한 입력으로 사용됩니다. Media Services v3에서 **작업**에 대한 입력은 **자산**일 수도 있고, HTTPS URL을 통해 Media Services 계정에 사용할 수 있는 콘텐츠일 수도 있습니다. HTTPS URL에서 인코딩하는 방법은 [이](job-input-from-http-how-to.md) 문서를 참조하세요.

Media Services v3에서는 Azure Storage API를 사용하여 파일을 업로드합니다. 다음 .NET 코드 조각에서 방법을 참조하세요.

다음 함수는 아래와 같은 작업을 수행합니다.

* **자산** 만들기
* [스토리지에 있는 자산 컨테이너](../../storage/blobs/storage-quickstart-blobs-dotnet.md#upload-blobs-to-a-container)에 대해 쓰기가 가능한 [SAS URL](../../storage/common/storage-sas-overview.md) 가져오기

    자산의 [ListContainerSas](/rest/api/media/assets/listcontainersas) 함수를 사용하여 SAS URL을 가져오는 경우 각 스토리지 계정에 대해 두 개의 스토리지 계정 키가 있으므로 이 함수는 여러 SAS URL을 반환합니다. 스토리지 계정에는 스토리지 계정 키를 원활하게 회전할 수 있도록 두 개의 키가 있습니다(예: 다른 키를 사용하는 동안 하나를 변경한 다음, 새 키를 사용하여 다른 키를 회전). 첫 번째 SAS URL은 스토리지 key1 및 두 번째 스토리지 key2를 나타냅니다.
* SAS URL을 사용하여 스토리지의 컨테이너에 파일 업로드

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateInputAsset)]

### <a name="create-an-output-asset-to-store-the-result-of-a-job"></a>작업 결과를 저장할 출력 자산 만들기

출력 [Asset](/rest/api/media/assets)은 인코딩 작업의 결과를 저장합니다. 프로젝트는 출력 자산의 결과를 "output" 폴더로 저장하는 **DownloadResults** 함수를 정의합니다. 따라서 무엇을 다운로드했는지 볼 수 있습니다.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateOutputAsset)]

### <a name="create-a-transform-and-a-job-that-encodes-the-uploaded-file"></a>로드된 파일을 인코딩하는 Transform 및 Job 만들기

Media Services에서 콘텐츠를 인코딩하거나 처리할 때 인코딩 설정을 레시피로 설정하는 것이 일반적인 패턴입니다. 그런 다음, 이 레시피가 비디오에 적용되도록 **Job**을 제출합니다. 각각의 새 비디오에 대한 새 작업을 제출하면 라이브러리의 모든 비디오에 레시피가 적용됩니다. Media Services의 레시피를 **Transform**이라고 합니다. 자세한 내용은 [Transform 및 Jobs](./transforms-jobs-concept.md)를 참조하세요. 이 자습서에서 설명하는 샘플은 다양한 iOS 및 Android 디바이스로 스트리밍하기 위해 비디오를 인코딩하는 레시피를 정의합니다.

#### <a name="transform"></a>변환

새로운 [Transform](/rest/api/media/transforms) 인스턴스를 만드는 경우 이 인스턴스를 통해 출력하려는 것이 무엇인지 지정해야 합니다. 필수 매개 변수는 아래 코드와 같이 **TransformOutput** 개체입니다. 각 **TransformOutput**에는 **Preset**이 포함됩니다. **Preset**은 원하는 **TransformOutput**을 생성하는 데 사용되는 비디오 및/또는 오디오 처리 작업에 대한 단계별 지침을 설명합니다. 이 자습서에서 설명하는 샘플은 **AdaptiveStreaming**이라는 기본 제공 Preset을 사용합니다. Preset은 입력 해상도 및 비트 전송률을 기반으로 자동 생성된 비트 전송률 사다리(비트 전송률-해상도 쌍)에 입력 비디오를 인코딩하고 각 비트 전송률-해상도 쌍에 해당하는 H.264 비디오 및 AAC 오디오가 포함된 ISO MP4 파일을 생성합니다. 이 Preset에 대한 정보는 [자동 생성된 비트 전송률 사다리](autogen-bitrate-ladder.md)를 참조하세요.

기본 제공 EncoderNamedPreset을 사용하거나 사용자 지정 사전 설정을 사용할 수 있습니다. 자세한 내용은 [인코더 사전 설정을 사용자 지정하는 방법](customize-encoder-presets-how-to.md)을 참조하세요.

[Transform](/rest/api/media/transforms)을 만드는 경우 먼저 **Get** 메서드를 사용하여 해당 Transform이 이미 있는지 확인해야 합니다. 아래 코드를 참조하세요. Media Services v3의 경우, 엔터티가 존재하지 않으면 엔터티에 대한 **Get** 메서드는 **null**을 반환합니다(이름의 대/소문자를 구분하지 않음).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#EnsureTransformExists)]

#### <a name="job"></a>작업

위에서 언급했듯이 [Transform](/rest/api/media/transforms) 개체는 레시피이며 [Job](/rest/api/media/jobs)은 주어진 입력 비디오 또는 오디오 콘텐츠에 **Transform**을 적용하라는 Media Services에 대한 실제 요청입니다. **Job**은 입력 비디오의 위치 및 출력 위치와 같은 정보를 지정합니다.

이 예제에서는 로컬 컴퓨터에서 입력 비디오가 업로드되었습니다. HTTPS URL에서 인코딩하는 방법은 [이](job-input-from-http-how-to.md) 문서를 참조하세요.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#SubmitJob)]

### <a name="wait-for-the-job-to-complete"></a>작업이 완료될 때까지 대기

작업을 완료하는 데 시간이 다소 걸리기 때문에 완료되면 알림을 받는 것이 좋습니다. 아래 코드 샘플은 [Job](/rest/api/media/jobs)의 상태에 대한 서비스를 폴링하는 방법을 보여줍니다. 폴링은 대기 시간이 발생할 가능성이 있기 때문에 프로덕션 앱에 권장하는 방법이 아닙니다. 폴링이 계정에서 초과 사용되면 정체될 수 있습니다. 대신 Event Grid를 사용해야 합니다.

Event Grid는 고가용성, 일관된 성능 및 동적 확장을 위해 설계되었습니다. Event Grid를 사용하면 앱이 사용자 지정 원본뿐만 아니라 거의 모든 Azure 서비스의 이벤트에 대해 수신 대기하고 대응할 수 있습니다. 간단한 HTTP 기반 반응형 이벤트 처리는 이벤트의 지능형 필터링 및 라우팅을 통해 효율적인 솔루션을 구축하는 데 도움이 됩니다.  [이벤트를 사용자 지정 웹 엔드포인트로 라우팅](job-state-events-cli-how-to.md)을 참조하세요.

**작업**은 일반적으로 **예약됨**, **대기**, **처리 중**, **마침**(최종 상태) 상태를 거칩니다. 작업에서 오류가 발생하면 **오류** 상태가 표시됩니다. 작업을 취소 중인 경우 **취소 중**이 표시되고, 취소가 완료되면 **취소됨**이 표시됩니다.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#WaitForJobToFinish)]

### <a name="job-error-codes"></a>작업 오류 코드

[오류 코드](/rest/api/media/jobs/get#joberrorcode)를 참조하세요.

### <a name="get-a-streaming-locator"></a>스트리밍 로케이터 가져오기

인코딩이 완료되면 다음 단계는 출력 자산의 비디오를 클라이언트가 재생할 수 있도록 만드는 것입니다. 이 작업은 두 단계로 수행할 수 있습니다. 첫 번째 단계로 [스트리밍 로케이터](/rest/api/media/streaminglocators)를 만들고, 두 번째 단계로 클라이언트가 사용할 수 있는 스트리밍 URL을 작성합니다.

**스트리밍 로케이터**를 만드는 과정을 게시라고 합니다. 기본적으로 **스트리밍 로케이터**는 API 호출을 수행한 직후부터 유효하며, 선택적인 시작 및 종료 시간을 구성하지 않는 한 삭제될 때까지 지속됩니다.

[StreamingLocator](/rest/api/media/streaminglocators)를 만들 때 원하는 **StreamingPolicyName**을 지정해야 합니다. 이 예제에서는 미리 정의된 암호화되지 않은 스트리밍 정책(**PredefinedStreamingPolicy.ClearStreamingOnly**)을 사용하도록 암호화되지 않은(in-the-clear 또는 non-encrypted) 콘텐츠를 스트리밍합니다.

> [!IMPORTANT]
> 사용자 지정 [스트리밍 정책](/rest/api/media/streamingpolicies)을 사용하는 경우 Media Service 계정에 대해 이러한 정책을 제한적으로 설계하고 동일한 암호화 옵션 및 프로토콜이 필요할 때마다 StreamingLocator에 다시 사용해야 합니다. Media Service 계정에는 Streaming Policy 항목의 수에 대한 할당량이 있습니다. 각 스트리밍 로케이터에 대한 새 스트리밍 정책을 만들지 않는 것이 좋습니다.

다음 코드에서는 고유한 locatorName을 사용하여 함수를 호출한다고 가정합니다.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateStreamingLocator)]

이 토픽의 샘플에서는 스트리밍에 대해 설명하지만 동일한 호출을 사용하여 점진적 다운로드를 통해 비디오를 전달하는 스트리밍 로케이터를 만들 수 있습니다.

### <a name="get-streaming-urls"></a>스트리밍 URL 얻기

[스트리밍 로케이터](/rest/api/media/streaminglocators)가 생성되었으므로 **GetStreamingURLs**에 표시된 것처럼 스트리밍 URL을 가져올 수 있습니다. URL을 빌드하려면 [스트리밍 엔드포인트](/rest/api/media/streamingendpoints) 호스트 이름과 **스트리밍 로케이터** 경로를 연결해야 합니다. 이 샘플에서는 *기본* **스트리밍 엔드포인트**가 사용됩니다. Media Service 계정을 처음으로 만들면 이 *기본* **스트리밍 엔드포인트**가 중지된 상태이므로 **Start**를 호출해야 합니다.

> [!NOTE]
> 이 메서드에서는 출력 자산에 대한 **스트리밍 로케이터**를 만들 때 사용된 locatorName이 필요합니다.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#GetStreamingURLs)]

### <a name="clean-up-resources-in-your-media-services-account"></a>Media Services 계정의 리소스 정리

일반적으로 재사용할 개체를 제외하고 모두 정리해야 합니다. 일반적으로 Transform을 재사용하고 StreamingLocator 등을 유지합니다. 실험 후 계정을 정리하려면 재사용하지 않을 리소스를 삭제해야 합니다. 예를 들어 다음 코드는 작업, 만든 자산 및 콘텐츠 키 정책을 삭제합니다.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CleanUp)]

## <a name="run-the-sample-app"></a>샘플 앱 실행

1. Ctrl+F5를 눌러서 *EncodeAndStreamFiles* 앱을 실행합니다.
2. 콘솔에서 스트리밍 URL 중 하나를 복사합니다.

아래 예제는 다른 프로토콜을 사용하여 비디오를 재생하는 데 사용할 수 있는 URL입니다.

![Media Services 스트리밍 비디오에 대한 URL을 보여주는 예제 출력](./media/stream-files-tutorial-with-api/output.png)

## <a name="test-the-streaming-url"></a>스트리밍 URL 테스트

이 문서에서는 스트림을 테스트하기 위해 Azure Media Player를 사용합니다.

> [!NOTE]
> 플레이어가 https 사이트에 호스트 될 경우 URL을 "https"로 업데이트해야 합니다.

1. 웹 브라우저를 열고 [https://aka.ms/azuremediaplayer/](https://aka.ms/azuremediaplayer/)로 이동합니다.
2. 앱을 실행할 때 얻은 스트리밍 URL 값 중 하나를 **URL:** 상자에 붙여넣습니다.
3. **플레이어 업데이트**를 선택합니다.

Azure Media Player는 테스트용으로 사용할 수 있지만 프로덕션 환경에서는 사용하면 안 됩니다.

## <a name="clean-up-resources"></a>리소스 정리

이 자습서에서 만든 Media Services 및 스토리지 계정을 포함하여 리소스 그룹의 리소스가 더 이상 필요하지 않으면, 앞서 만든 리소스 그룹을 삭제합니다.

다음 CLI 명령을 실행합니다.

```azurecli
az group delete --name amsResourceGroup
```

## <a name="multithreading"></a>다중 스레딩

Azure Media Services v3 SDK는 스레드로부터 안전하지 않습니다. 다중 스레드 앱을 개발하는 경우 스레드마다 새 AzureMediaServicesClient 개체를 생성하고 사용해야 합니다.

## <a name="ask-questions-give-feedback-get-updates"></a>질문, 피드백 제공, 업데이트 받기

[Azure Media Services 커뮤니티](media-services-community.md) 문서를 체크 아웃하여 다양한 방법으로 질문을 하고, 피드백을 제공하고, Media Services에 대한 업데이트를 가져올 수 있습니다.

## <a name="next-steps"></a>다음 단계

비디오를 업로드, 인코딩 및 스트리밍하는 방법을 알아보았으니 이제 다음 문서를 참조하십시오. 

> [!div class="nextstepaction"]
> [비디오 분석](analyze-videos-tutorial-with-api.md)
