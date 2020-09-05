---
title: Azure Media Services v3로 설명 오디오 트랙 신호 보내기 | Microsoft Docs
description: 이 자습서의 단계에 따라 파일을 업로드 하 고, 비디오를 인코딩하고, 설명 오디오 트랙을 추가 하 고, Media Services v3로 콘텐츠를 스트리밍합니다.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.custom: devx-track-csharp
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: 1a8986dcba9dc64eb77d8bc8c6e77704fa212abb
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89267431"
---
# <a name="signal-descriptive-audio-tracks"></a>설명 오디오 트랙 신호

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

비디오에 내레이션 트랙을 추가하여 시각적으로 장애가 있는 클라이언트가 내레이션을 들어 비디오 녹화를 따르도록 할 수 있습니다. Media Services v3에서 매니페스트 파일의 오디오 트랙에 주석을 달고 설명 오디오 트랙에 신호를 보낼 수 있습니다.

이 문서에서는 비디오를 인코딩하고, 설명 오디오를 포함 하는 오디오 전용 MP4 파일 (AAC 코덱)을 출력 자산에 업로드 하 고, 설명 오디오를 포함 하도록 ism 파일을 편집 하는 방법을 보여 줍니다.

## <a name="prerequisites"></a>사전 요구 사항

- [Media Services 계정 만들기](./create-account-howto.md)
- [Azure CLI를 사용하여 Azure Media Services API 액세스](./access-api-howto.md)의 단계를 수행하고 자격 증명을 저장합니다. API에 액세스할 때 필요합니다.
- [동적 패키징](dynamic-packaging-overview.md)을 검토 합니다.
- [비디오 업로드, 인코딩 및 스트리밍](stream-files-tutorial-with-api.md) 자습서를 검토 합니다.

## <a name="create-an-input-asset-and-upload-a-local-file-into-it"></a>입력 자산을 만들고 여기에 로컬 파일 업로드 

**CreateInputAsset** 함수는 새로운 입력 [Asset](/rest/api/media/assets)을 만들고 이 자산에 지정된 로컬 비디오 파일을 업로드합니다. 이 **자산은** 인코딩 작업에 대 한 입력으로 사용 됩니다. Media Services v 3에서 **작업** 에 대 한 입력은 **자산**이거나 HTTPS url을 통해 Media Services 계정에 제공 되는 콘텐츠 일 수 있습니다. 

HTTPS URL에서 인코딩하는 방법에 대 한 자세한 내용은 [이 문서](job-input-from-http-how-to.md) 를 참조 하세요.  

Media Services v3에서는 Azure Storage API를 사용하여 파일을 업로드합니다. 다음 .NET 코드 조각에서 방법을 참조하세요.

다음 함수는 아래와 같은 작업을 수행합니다.

* **자산** 을 만듭니다. 
* [저장소에서 자산의 컨테이너](../../storage/blobs/storage-quickstart-blobs-dotnet.md#upload-blobs-to-a-container) 에 쓸 수 있는 [SAS URL](../../storage/common/storage-sas-overview.md) 을 가져옵니다.
* SAS URL을 사용하여 스토리지의 컨테이너에 파일 업로드

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateInputAsset)]

만든 입력 자산의 이름을 다른 메서드에 전달 해야 하는 경우에서 반환 된 자산 개체의 속성을 사용 해야 `Name` `CreateInputAssetAsync` 합니다 (예: inputAsset.Name). 

## <a name="create-an-output-asset-to-store-the-result-of-the-encoding-job"></a>인코딩 작업의 결과를 저장할 출력 자산 만들기

출력 [Asset](/rest/api/media/assets)은 인코딩 작업의 결과를 저장합니다. 다음 함수는 출력 자산을 만드는 방법을 보여 줍니다.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateOutputAsset)]

만든 출력 자산의 이름을 다른 메서드에 전달 해야 하는 경우에서 반환 된 자산 개체의 속성을 사용 해야 `Name` `CreateIOutputAssetAsync` 합니다 (예: outputAsset.Name). 

이 문서의 경우 `outputAsset.Name` 값을 `SubmitJobAsync` 및 함수에 전달 `UploadAudioIntoOutputAsset` 합니다.

## <a name="create-a-transform-and-a-job-that-encodes-the-uploaded-file"></a>업로드 된 파일을 인코딩하는 변환 및 작업 만들기

Media Services에서 콘텐츠를 인코딩하거나 처리할 때 인코딩 설정을 레시피로 설정하는 것이 일반적인 패턴입니다. 그런 다음, 이 레시피가 비디오에 적용되도록 **Job**을 제출합니다. 새 비디오에 대한 새 작업을 제출하면 라이브러리의 모든 비디오에 레시피가 적용됩니다. Media Services의 레시피를 **Transform**이라고 합니다. 자세한 내용은 [Transform 및 Jobs](./transforms-jobs-concept.md)를 참조하세요. 이 자습서에서 설명하는 샘플은 다양한 iOS 및 Android 디바이스로 스트리밍하기 위해 비디오를 인코딩하는 레시피를 정의합니다. 

다음 예에서는 변환을 만듭니다 (없는 경우).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#EnsureTransformExists)]

다음 함수는 작업을 제출 합니다.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#SubmitJob)]

## <a name="wait-for-the-job-to-complete"></a>작업이 완료될 때까지 대기

작업을 완료하는 데 시간이 다소 걸리기 때문에 완료되면 알림을 받는 것이 좋습니다. Event Grid를 사용 하 여 작업이 완료 될 때까지 대기 하는 것이 좋습니다.

작업은 일반적으로 **예약**됨, **대기** **중, 처리 중**, **완료** 됨 상태 (최종 상태)로 이동 합니다. 작업에서 오류가 발생하면 **오류** 상태가 표시됩니다. 작업을 취소 중인 경우 **취소 중**이 표시되고 완료되면 **취소됨**이 표시됩니다.

자세한 내용은 [Event Grid 이벤트 처리](reacting-to-media-services-events.md)를 참조 하세요.

## <a name="upload-the-audio-only-mp4-file"></a>오디오 전용 MP4 파일 업로드

설명 오디오가 포함 된 추가 오디오 전용 MP4 파일 (AAC 코덱)을 출력 자산에 업로드 합니다.  

```csharp
private static async Task UpoadAudioIntoOutputAsset(
    IAzureMediaServicesClient client,
    string resourceGroupName,
    string accountName,
    string outputAssetName,
    string fileToUpload)
{
    // Use the Assets.Get method to get the existing asset. 
    // In Media Services v3, the Get method on entities returns null 
    // if the entity doesn't exist (a case-insensitive check on the name).

    // Call Media Services API to create an Asset.
    // This method creates a container in storage for the Asset.
    // The files (blobs) associated with the asset will be stored in this container.
    Asset asset = await client.Assets.GetAsync(resourceGroupName, accountName, outputAssetName);
    
    if (asset != null)
    {
      // Use Media Services API to get back a response that contains
      // SAS URL for the Asset container into which to upload blobs.
      // That is where you would specify read-write permissions 
      // and the exparation time for the SAS URL.
      var response = await client.Assets.ListContainerSasAsync(
          resourceGroupName,
          accountName,
          outputAssetName,
          permissions: AssetContainerPermission.ReadWrite,
          expiryTime: DateTime.UtcNow.AddHours(4).ToUniversalTime());

      var sasUri = new Uri(response.AssetContainerSasUrls.First());

      // Use Storage API to get a reference to the Asset container
      // that was created by calling Asset's CreateOrUpdate method.  
      CloudBlobContainer container = new CloudBlobContainer(sasUri);
      var blob = container.GetBlockBlobReference(Path.GetFileName(fileToUpload));

      // Use Strorage API to upload the file into the container in storage.
      await blob.UploadFromFileAsync(fileToUpload);
    }
}
```

다음은 함수 호출의 예입니다 `UpoadAudioIntoOutputAsset` .

```csharp
await UpoadAudioIntoOutputAsset(client, config.ResourceGroup, config.AccountName, outputAsset.Name, "audio_description.m4a");
```

## <a name="edit-the-ism-file"></a>Ism 파일 편집

인코딩 작업이 완료 되 면 출력 자산에 인코딩 작업에 의해 생성 된 파일이 포함 됩니다. 

1. Azure Portal에서 Media Services 계정과 연결 된 저장소 계정으로 이동 합니다. 
1. 출력 자산의 이름이 있는 컨테이너를 찾습니다. 
1. 컨테이너에서 ism 파일을 찾고 오른쪽 창에서 **Blob 편집** 을 클릭 합니다. 
1. 설명 오디오를 포함 하는 업로드 된 오디오 전용 MP4 파일 (AAC 코덱)에 대 한 정보를 추가 하 여 ism 파일을 편집 하 고 완료 되 면 **저장** 을 누릅니다.

    설명 오디오 트랙에 신호를 보내려면 "accessibility" 및 "role" 매개 변수를 ism 파일에 추가 해야 합니다. 오디오 트랙을 오디오 설명으로 알리기 위해 이러한 매개 변수를 올바르게 설정하는 것은 사용자의 책임입니다. 예를 들어 `<param name="accessibility" value="description" />` `<param name="role" value="alternate" />` 다음 예제와 같이 특정 오디오 트랙에 대 한 및을. a .의 ism 파일에 추가 합니다.
 
```xml
<?xml version="1.0" encoding="utf-8"?>
<smil xmlns="http://www.w3.org/2001/SMIL20/Language">
  <head>
    <meta name="clientManifestRelativePath" content="ignite.ismc" />
    <meta name="formats" content="mp4-v3" />
  </head>
  <body>
    <switch>
      <audio src="ignite_320x180_AACAudio_381.mp4" systemBitrate="128041" systemLanguage="eng">
        <param name="systemBitrate" value="128041" valuetype="data" />
        <param name="trackID" value="2" valuetype="data" />
        <param name="trackName" value="aac_eng_2_128041_2_1" valuetype="data" />
        <param name="systemLanguage" value="eng" valuetype="data" />
        <param name="trackIndex" value="ignite_320x180_AACAudio_381_2.mpi" valuetype="data" />
      </audio>
      <audio src="audio_description.m4a" systemBitrate="194000" systemLanguage="eng">
        <param name="trackName" value="aac_eng_audio_description" />
        <param name="accessibility" value="description" />
        <param name="role" value="alternate" />     
      </audio>          
      <video src="ignite_1280x720_AACAudio_3549.mp4" systemBitrate="3549855">
        <param name="systemBitrate" value="3549855" valuetype="data" />
        <param name="trackID" value="1" valuetype="data" />
        <param name="trackName" value="video" valuetype="data" />
        <param name="trackIndex" value="ignite_1280x720_AACAudio_3549_1.mpi" valuetype="data" />
      </video>
      <video src="ignite_960x540_AACAudio_2216.mp4" systemBitrate="2216764">
        <param name="systemBitrate" value="2216764" valuetype="data" />
        <param name="trackID" value="1" valuetype="data" />
        <param name="trackName" value="video" valuetype="data" />
        <param name="trackIndex" value="ignite_960x540_AACAudio_2216_1.mpi" valuetype="data" />
      </video>
      <video src="ignite_640x360_AACAudio_1154.mp4" systemBitrate="1154569">
        <param name="systemBitrate" value="1154569" valuetype="data" />
        <param name="trackID" value="1" valuetype="data" />
        <param name="trackName" value="video" valuetype="data" />
        <param name="trackIndex" value="ignite_640x360_AACAudio_1154_1.mpi" valuetype="data" />
      </video>
      <video src="ignite_480x270_AACAudio_721.mp4" systemBitrate="721893">
        <param name="systemBitrate" value="721893" valuetype="data" />
        <param name="trackID" value="1" valuetype="data" />
        <param name="trackName" value="video" valuetype="data" />
        <param name="trackIndex" value="ignite_480x270_AACAudio_721_1.mpi" valuetype="data" />
      </video>
      <video src="ignite_320x180_AACAudio_381.mp4" systemBitrate="381027">
        <param name="systemBitrate" value="381027" valuetype="data" />
        <param name="trackID" value="1" valuetype="data" />
        <param name="trackName" value="video" valuetype="data" />
        <param name="trackIndex" value="ignite_320x180_AACAudio_381_1.mpi" valuetype="data" />
      </video>
    </switch>
  </body>
</smil>
```

## <a name="get-a-streaming-locator"></a>스트리밍 로케이터 가져오기

인코딩이 완료되면 다음 단계는 출력 자산의 비디오를 클라이언트가 재생할 수 있도록 만드는 것입니다. 이 작업은 두 단계로 수행할 수 있습니다. 첫째, [스트리밍 로케이터](/rest/api/media/streaminglocators)를 만들고 둘째, 클라이언트가 사용할 수 있는 스트리밍 URL을 작성합니다. 

**스트리밍 로케이터**를 만드는 과정을 게시라고 합니다. 기본적으로 **스트리밍 로케이터** 는 선택적 시작 및 종료 시간을 구성 하지 않는 한 API 호출을 수행한 직후에 유효 하며 삭제 될 때까지 지속 됩니다. 

[StreamingLocator](/rest/api/media/streaminglocators)를 만들 때 원하는 **StreamingPolicyName**을 지정해야 합니다. 이 예제에서는 미리 정의 된 암호화 되지 않은 스트리밍 정책 (**PredefinedStreamingPolicy**)이 사용 되도록 명확 하 게 (또는 암호화 되지 않은 콘텐츠) 스트리밍할 예정입니다.

> [!IMPORTANT]
> 사용자 지정 [스트리밍 정책](/rest/api/media/streamingpolicies)을 사용하는 경우 Media Service 계정에 대해 이러한 정책을 제한적으로 설계하고 동일한 암호화 옵션 및 프로토콜이 필요할 때마다 StreamingLocator에 다시 사용해야 합니다. Media Service 계정에는 Streaming Policy 항목의 수에 대한 할당량이 있습니다. 각 스트리밍 로케이터에 대해 새 스트리밍 정책을 만들지 않아야 합니다.

다음 코드에서는 고유한 locatorName을 사용하여 함수를 호출한다고 가정합니다.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateStreamingLocator)]

이 토픽의 샘플에서는 스트리밍에 대해 설명하지만 동일한 호출을 사용하여 점진적 다운로드를 통해 비디오를 전달하는 스트리밍 로케이터를 만들 수 있습니다.

### <a name="get-streaming-urls"></a>스트리밍 URL 얻기

[스트리밍 로케이터](/rest/api/media/streaminglocators)가 생성되었으므로 **GetStreamingURLs**에 표시된 것처럼 스트리밍 URL을 가져올 수 있습니다. URL을 빌드하려면 [스트리밍 엔드포인트](/rest/api/media/streamingendpoints) 호스트 이름과 **스트리밍 로케이터** 경로를 연결해야 합니다. 이 샘플에서는 *기본* **스트리밍 엔드포인트**가 사용됩니다. Media Service 계정을 처음으로 만들면 이 *기본* **스트리밍 엔드포인트**가 중지된 상태이므로 **Start**를 호출해야 합니다.

> [!NOTE]
> 이 메서드에서는 출력 자산에 대한 **스트리밍 로케이터**를 만들 때 사용된 locatorName이 필요합니다.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#GetStreamingURLs)]

## <a name="test-with-azure-media-player"></a>Azure Media Player로 테스트

이 문서에서는 스트림을 테스트하기 위해 Azure Media Player를 사용합니다. 

> [!NOTE]
> 플레이어가 https 사이트에 호스트 될 경우 URL을 "https"로 업데이트해야 합니다.

1. 웹 브라우저를 열고 [https://aka.ms/azuremediaplayer/](https://aka.ms/azuremediaplayer/)로 이동합니다.
2. **URL:** 상자에 응용 프로그램에서 가져온 스트리밍 URL 값 중 하나를 붙여넣습니다. 
 
     URL을 HLS, Dash 또는 부드러운 스트리밍 형식으로 붙여넣을 수 있으며, Azure Media Player는 디바이스에서 재생하기 위한 적절한 스트리밍 프로토콜로 자동으로 전환합니다.
3. **플레이어 업데이트**를 누릅니다.

Azure Media Player는 테스트용으로 사용할 수 있지만 프로덕션 환경에서는 사용할 수 없습니다. 

## <a name="next-steps"></a>다음 단계

[비디오 분석](analyze-videos-tutorial-with-api.md)
