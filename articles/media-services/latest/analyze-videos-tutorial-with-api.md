---
title: Media Services v3으로 비디오 분석
titleSuffix: Azure Media Services
description: Azure Media Services를 사용하여 비디오를 분석하는 방법을 알아봅니다.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: tutorial
ms.date: 02/02/2020
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: abd4a3a3a3e8494ea325e65a78eea7fb56b78f94
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/04/2020
ms.locfileid: "76988365"
---
# <a name="tutorial-analyze-videos-with-media-services-v3"></a>자습서: Media Services v3으로 비디오 분석

> [!NOTE]
> 이 자습서에서 [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.liveevent?view=azure-dotnet) 예제를 사용하지만, 일반적인 단계는 [REST API](https://docs.microsoft.com/rest/api/media/liveevents), [CLI](https://docs.microsoft.com/cli/azure/ams/live-event?view=azure-cli-latest) 또는 지원되는 기타 [SDK](media-services-apis-overview.md#sdks)와 동일합니다.

이 자습서는 Azure Media Services로 비디오를 분석하는 방법을 보여줍니다. 녹음/녹화한 비디오나 오디오 콘텐츠를 심층적으로 파악해야 하는 다양한 경우가 있을 수 있습니다. 예를 들어, 고객 만족도를 높이 달성하기 위해 음성을 텍스트로 변환하는 프로세스를 실행하여 고객 지원 기록을 인덱스 및 대시보드와 함께 검색 가능한 카탈로그로 변환할 수 있습니다. 그러면 비즈니스에 대한 인사이트를 얻을 수 있습니다. 이러한 인사이트에는 일반적인 불만 사항, 불만의 원인, 기타 유용한 정보가 포함됩니다.

이 자습서에서는 다음을 수행하는 방법에 대해 설명합니다.

> [!div class="checklist"]
> * 토픽에 설명된 샘플 앱 다운로드
> * 지정된 비디오를 분석하는 코드 검사
> * 앱을 실행합니다.
> * 출력 내용 검사
> * 리소스를 정리합니다.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="compliance-privacy-and-security"></a>규정 준수, 개인 정보 보호 및 보안
 
중요한 알림을 사용하는 경우 Video Indexer 사용 시 적용 가능한 모든 법률을 준수해야 하며, 다른 사용자의 권리를 위반하거나 다른 사용자에게 해로운 방법으로 Video Indexer 또는 다른 Azure 서비스를 사용해서는 안 됩니다. 처리 및 저장을 위해 Video Indexer 서비스에 모든 생체 인식 데이터를 비롯한 비디오를 업로드하려면, 비디오에 나온 모든 사람들의 적절한 동의를 비롯한 적절한 권한이 모두 있어야 합니다. Video Indexer의 규정 준수, 개인 정보 보호 및 보안에 대해 알아보려면 Microsoft [Cognitive Services 약관](https://azure.microsoft.com/support/legal/cognitive-services-compliance-and-privacy/)을 참조하세요. Microsoft의 개인 정보 보호 의무 및 데이터 처리에 대한 내용은 Microsoft의 [개인정보처리방침](https://privacy.microsoft.com/PrivacyStatement), [Online Services 사용 약관](https://www.microsoft.com/licensing/product-licensing/products)(“OST”) 및 [Data Processing 추록](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=67)(“DPA”)을 검토하세요. 데이터 보존, 삭제/소멸을 비롯한 추가 개인 정보 보호 정보는 OST 및 [여기](../video-indexer/faq.md)에서 사용할 수 있습니다. Video Indexer를 사용하면 Cognitive Services 사용 약관, OST, DPA 및 개인정보처리방침에 따라 바인딩되는 것에 동의합니다.

## <a name="prerequisites"></a>사전 요구 사항

- Visual Studio가 설치되지 않은 경우 [Visual Studio Community 2017](https://www.visualstudio.com/thank-you-downloading-visual-studio/?sku=Community&rel=15)을 받습니다.
- [Media Services 계정 만들기](create-account-cli-how-to.md)<br/>리소스 그룹 이름 및 Media Services 계정 이름에 사용한 값을 기억해 두세요.
- [Azure CLI를 사용하여 Azure Media Services API 액세스](access-api-cli-how-to.md)의 단계를 수행하고 자격 증명을 저장합니다. API에 액세스할 때 필요합니다.

## <a name="download-and-configure-the-sample"></a>샘플 다운로드 및 구성

다음 명령을 사용하여 .NET 샘플이 포함된 GitHub 리포지토리를 컴퓨터에 복제합니다.  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git
 ```

샘플은 [AnalyzeVideos](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/tree/master/AMSV3Tutorials/AnalyzeVideos) 폴더에 있습니다.

다운로드한 프로젝트에서 [appsettings.json](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/AnalyzeVideos/appsettings.json) 파일을 엽니다. 값을 [API에 액세스](access-api-cli-how-to.md)하여 가져온 자격 증명으로 바꿉니다.

## <a name="examine-the-code-that-analyzes-the-specified-video"></a>지정된 비디오를 분석하는 코드 검사

이 섹션에서는 *AnalyzeVideos* 프로젝트의 [Program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/AnalyzeVideos/Program.cs) 파일에 정의된 함수를 살펴봅니다.

샘플은 다음 작업을 완료합니다.

1. 비디오를 분석하는 **변환** 및 **작업**을 만듭니다.
2. 입력 **자산**을 만들고 비디오를 업로드합니다. 자산은 작업의 입력으로 사용됩니다.
3. 작업의 출력을 저장하는 출력 자산을 만듭니다.
4. 작업을 제출합니다.
5. 작업의 상태를 확인합니다.
6. 작업 실행에서 생성된 파일을 다운로드합니다.

> [!NOTE]
> 비디오 또는 오디오 분석기 사전 설정을 사용할 때는 Azure Portal을 통해 S3 미디어 예약 10단위를 갖도록 계정을 설정합니다. 자세한 내용은 [미디어 처리 크기 조정](media-reserved-units-cli-how-to.md)을 참조하세요.

### <a name="start-using-media-services-apis-with-net-sdk"></a>.NET SDK로 Media Services API 사용하기

.NET으로 Media Services API를 사용하려면 **AzureMediaServicesClient** 개체를 만들어야 합니다. 개체를 만들려면 Azure AD를 사용하여 클라이언트가 Azure에 연결하는 데 필요한 자격 증명을 제공해야 합니다. 아티클의 시작 부분에서 복제한 코드에서 **GetCredentialsAsync** 함수는 로컬 구성 파일에 제공된 자격 증명에 따라 ServiceClientCredentials 개체를 만듭니다. 

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#CreateMediaServicesClient)]

### <a name="create-an-input-asset-and-upload-a-local-file-into-it"></a>입력 자산을 만들고 여기에 로컬 파일 업로드 

**CreateInputAsset** 함수는 새로운 입력 [Asset](https://docs.microsoft.com/rest/api/media/assets)을 만들고 이 자산에 지정된 로컬 비디오 파일을 업로드합니다. 이 Asset은 인코딩 Job에 대한 입력으로 사용됩니다. Media Services v3에서 Job에 대한 입력은 Asset이거나 HTTPS URL을 통해 Media Services 계정에서 사용할 수 있는 콘텐츠일 수도 있습니다. HTTPS URL에서 인코딩하는 방법은 [이](job-input-from-http-how-to.md) 문서를 참조하세요.  

Media Services v3에서는 Azure Storage API를 사용하여 파일을 업로드합니다. 다음 .NET 코드 조각에서 방법을 참조하세요.

다음 함수는 아래와 같은 작업을 완료합니다.

* 자산 만들기
* [스토리지에 있는 자산 컨테이너](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-dotnet#upload-blobs-to-a-container)에 대해 쓰기가 가능한 [SAS URL](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) 가져오기
* SAS URL을 사용하여 스토리지의 컨테이너에 파일 업로드

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#CreateInputAsset)]

### <a name="create-an-output-asset-to-store-the-result-of-the-job"></a>작업 결과를 저장할 출력 자산 만들기

출력 [Asset](https://docs.microsoft.com/rest/api/media/assets)은 작업의 결과를 저장합니다. 프로젝트는 출력 자산의 결과를 "output" 폴더로 저장하는 **DownloadResults** 함수를 정의합니다. 따라서 무엇을 다운로드했는지 볼 수 있습니다.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#CreateOutputAsset)]

### <a name="create-a-transform-and-a-job-that-analyzes-videos"></a>비디오를 분석하는 Transform 및 Job 만들기

Media Services에서 콘텐츠를 인코딩하거나 처리할 때 인코딩 설정을 레시피로 설정하는 것이 일반적인 패턴입니다. 그런 다음, 이 레시피가 비디오에 적용되도록 **Job**을 제출합니다. 각각의 새 비디오에 대한 새 작업을 제출하면 라이브러리의 모든 비디오에 레시피가 적용됩니다. Media Services의 레시피를 **Transform**이라고 합니다. 자세한 내용은 [Transform 및 Job](transform-concept.md)을 참조하세요. 이 자습서에 설명된 샘플은 지정된 비디오를 분석하는 레시피를 정의합니다.

#### <a name="transform"></a>변환

새로운 [Transform](https://docs.microsoft.com/rest/api/media/transforms) 인스턴스를 만드는 경우 이 인스턴스를 통해 출력하려는 것이 무엇인지 지정해야 합니다. **TransformOutput**은 필수 매개 변수입니다. 각 **TransformOutput**에는 **Preset**이 포함됩니다. **Preset**은 원하는 **TransformOutput**을 생성하는 데 사용되는 비디오 및/또는 오디오 처리 작업에 대한 단계별 지침을 설명합니다. 이 예제에서는 **VideoAnalyzerPreset** preset이 사용되며 해당 생성자에게 언어("en-US")가 전달됩니다(`new VideoAnalyzerPreset("en-US")`). 이 preset을 통해 비디오에서 여러 개의 오디오 및 비디오 인사이트를 추출할 수 있습니다. 비디오에서 여러 오디오 인사이트를 추출해야 하는 경우 **AudioAnalyzerPreset** preset을 사용할 수 있습니다.

**Transform**을 만드는 경우 먼저 **Get** 메서드를 사용하여 해당 Transform이 이미 있는지 확인합니다. 아래 코드를 참조하세요. Media Services v3의 경우, 엔터티가 존재하지 않으면 엔터티에 대한 **Get** 메서드는 **null**을 반환합니다(이름의 대/소문자를 구분하지 않음).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#EnsureTransformExists)]

#### <a name="job"></a>작업

위에서 언급했듯이 [Transform](https://docs.microsoft.com/rest/api/media/transforms) 개체는 레시피이며 [Job](https://docs.microsoft.com/rest/api/media/jobs)은 주어진 입력 비디오 또는 오디오 콘텐츠에 **Transform**을 적용하라는 Media Services에 대한 실제 요청입니다. **Job**은 입력 비디오의 위치 및 출력 위치와 같은 정보를 지정합니다. Media Service 계정에 있는 HTTPS URL, SAS URL 또는 자산을 사용하여 비디오 위치를 지정할 수 있습니다.

이 예에서 작업 입력은 로컬 비디오입니다.  

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#SubmitJob)]

### <a name="wait-for-the-job-to-complete"></a>작업이 완료될 때까지 대기

작업이 완료될 때까지 시간이 조금 걸립니다. 적업이 완료될 때 알림을 받을 수 있습니다. [Job](https://docs.microsoft.com/rest/api/media/jobs) 완료에 대한 알림을 받는 옵션은 여러 가지입니다. 가장 간단한 옵션(여기에 표시됨)은 폴링을 사용하는 것입니다.

폴링은 대기 시간이 발생할 가능성이 있기 때문에 프로덕션 앱에 권장하는 방법이 아닙니다. 폴링이 계정에서 초과 사용되면 정체될 수 있습니다. 대신 Event Grid를 사용해야 합니다.

Event Grid는 고가용성, 일관된 성능 및 동적 확장을 위해 설계되었습니다. Event Grid를 사용하면 앱이 사용자 지정 원본뿐만 아니라 거의 모든 Azure 서비스의 이벤트에 대해 수신 대기하고 대응할 수 있습니다. 간단한 HTTP 기반 반응형 이벤트 처리는 이벤트의 지능형 필터링 및 라우팅을 통해 효율적인 솔루션을 구축하는 데 도움이 됩니다. 자세한 내용은 [이벤트를 사용자 지정 웹 엔드포인트로 라우팅](job-state-events-cli-how-to.md)을 참조하세요.

**작업**은 일반적으로 **예약됨**, **대기**, **처리 중**, **마침**(최종 상태) 상태를 거칩니다. 작업에서 오류가 발생하면 **오류** 상태가 표시됩니다. 작업을 취소 중인 경우 **취소 중**이 표시되고, 취소가 완료되면 **취소됨**이 표시됩니다.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#WaitForJobToFinish)]

### <a name="job-error-codes"></a>작업 오류 코드

[오류 코드](https://docs.microsoft.com/rest/api/media/jobs/get#joberrorcode)를 참조하세요.

### <a name="download-the-result-of-the-job"></a>작업 결과 다운로드

다음 함수는 출력 [Asset](https://docs.microsoft.com/rest/api/media/assets)의 결과를 "output" 폴더로 다운로드하기 때문에 작업의 결과를 검사할 수 있습니다.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#DownloadResults)]

### <a name="clean-up-resource-in-your-media-services-account"></a>Media Services 계정의 리소스 정리

일반적으로 재사용할 개체를 제외하고 모두 정리해야 합니다. 일반적으로 Transform을 재사용하고 StreamingLocator를 유지합니다. 실험 후 계정을 정리하려면 재사용하지 않을 리소스를 삭제해야 합니다. 예를 들어 다음 코드는 Job을 삭제합니다.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#CleanUp)]

## <a name="run-the-sample-app"></a>샘플 앱 실행

Ctrl+F5 키를 눌러 *AnalyzeVideos* 앱을 실행합니다.

프로그램을 실행하면 Job은 동영상에서 찾은 각 얼굴의 썸네일을 생성합니다. insights.json 파일도 생성합니다.

## <a name="examine-the-output"></a>출력 내용 검사

분석 비디오의 출력 파일은 insights.json입니다. 이 파일에는 비디오에 대한 인사이트가 포함됩니다. json 파일에 있는 요소에 대한 설명은 [미디어 인텔리전스](intelligence-concept.md) 문서를 참조하세요.

## <a name="clean-up-resources"></a>리소스 정리

이 자습서에서 만든 Media Services 및 스토리지 계정을 포함하여 리소스 그룹의 리소스가 더 이상 필요하지 않으면, 앞서 만든 리소스 그룹을 삭제합니다.

다음 CLI 명령을 실행합니다.

```azurecli
az group delete --name amsResourceGroup
```

## <a name="multithreading"></a>다중 스레딩

Azure Media Services v3 SDK는 스레드로부터 안전하지 않습니다. 다중 스레드 앱으로 작업하는 경우 스레드마다 새로운 AzureMediaServicesClient 개체를 생성해야 합니다.

## <a name="ask-questions-give-feedback-get-updates"></a>질문, 피드백 제공, 업데이트 받기

[Azure Media Services 커뮤니티](media-services-community.md) 문서를 체크 아웃하여 다양한 방법으로 질문을 하고, 피드백을 제공하고, Media Services에 대한 업데이트를 가져올 수 있습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [자습서: 파일 업로드, 인코딩 및 스트리밍](stream-files-tutorial-with-api.md)
