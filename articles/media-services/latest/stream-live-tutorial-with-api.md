---
title: Media Services v3로 라이브 스트리밍
titleSuffix: Azure Media Services
description: Azure Media Services v3에서 라이브 스트리밍을 수행하는 방법을 알아봅니다.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc, devx-track-csharp
ms.date: 06/13/2019
ms.author: inhenkel
ms.openlocfilehash: b2e456474a9d052d9515c8169ce233e9577a5c53
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89256568"
---
# <a name="tutorial-stream-live-with-media-services"></a>자습서: Media Services로 라이브 스트리밍

> [!NOTE]
> 이 자습서에서 [.NET SDK](/dotnet/api/microsoft.azure.management.media.models.liveevent?view=azure-dotnet) 예제를 사용하지만, 일반적인 단계는 [REST API](/rest/api/media/liveevents), [CLI](/cli/azure/ams/live-event?view=azure-cli-latest) 또는 지원되는 기타 [SDK](media-services-apis-overview.md#sdks)와 동일합니다.

[라이브 이벤트](/rest/api/media/liveevents)는 Azure Media Services에서 라이브 스트리밍 콘텐츠를 처리하는 작업을 담당합니다. 라이브 이벤트는 라이브 인코더에 제공할 입력 엔드포인트(수집 URL)를 제공합니다. 라이브 이벤트는 라이브 인코더에서 라이브 입력 스트림을 수신하여 하나 이상의 [스트리밍 엔드포인트](/rest/api/media/streamingendpoints)를 통해 스트리밍하는 데 사용할 수 있도록 합니다. 또한 라이브 이벤트는 스트림을 추가로 처리하고 배달하기 전에 미리 보고 유효성을 검색하는 데 사용되는 미리 보기 엔드포인트(미리 보기 URL)를 제공합니다. 이 자습서에는 .NET Core를 사용하여 **통과** 형식의 라이브 이벤트를 만드는 방법을 보여줍니다.

이 자습서에서는 다음을 수행하는 방법에 대해 설명합니다.

> [!div class="checklist"]
> * 토픽에 설명된 샘플 앱 다운로드
> * 라이브 스트리밍을 수행하는 코드 검사
> * [https://ampdemo.azureedge.net](https://ampdemo.azureedge.net)에서 [Azure Media Player](https://amp.azure.net/libs/amp/latest/docs/index.html)를 사용하여 이벤트를 감시합니다.
> * 리소스를 정리합니다.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>사전 요구 사항

자습서를 완료하는 데 필요한 항목은 다음과 같습니다.

- Visual Studio Code 또는 Visual Studio 설치
- [Media Services 계정 만들기](./create-account-howto.md)<br/>리소스 그룹 이름 및 Media Services 계정 이름에 사용하는 값을 기억해 두세요.
- [Azure CLI를 사용하여 Azure Media Services API 액세스](./access-api-howto.md)의 단계를 수행하고 자격 증명을 저장합니다. API에 액세스할 때 필요합니다.
- 브로드캐스트 또는 이벤트에 사용되는 카메라 또는 디바이스(예: 랩톱).
- 카메라에서 Media Services 라이브 스트리밍 서비스로 보내는 스트림으로 신호를 변환하는 온-프레미스 라이브 인코더입니다. [권장되는 온-프레미스 라이브 인코더](recommended-on-premises-live-encoders.md)를 참조하세요. 스트림은 **RTMP** 또는 **부드러운 스트리밍** 형식이어야 합니다.

> [!TIP]
> 계속 진행하기 전에 [Media Services v3에서 라이브 스트리밍](live-streaming-overview.md)을 검토해야 합니다. 

## <a name="download-and-configure-the-sample"></a>샘플 다운로드 및 구성

다음 명령을 사용하여 스트리밍 .NET 샘플이 포함된 GitHub 리포지토리를 컴퓨터에 복제합니다.  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials.git
 ```

라이브 스트리밍 샘플은 [Live](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/tree/master/NETCore/Live/MediaV3LiveApp) 폴더에 있습니다.

다운로드한 프로젝트에서 [appsettings.json](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/Live/MediaV3LiveApp/appsettings.json) 파일을 엽니다. 값을 [API에 액세스](./access-api-howto.md)하여 가져온 자격 증명으로 바꿉니다.

> [!IMPORTANT]
> 이 샘플에서는 각 리소스에 고유한 접미사를 사용합니다. 디버깅을 취소하거나 앱을 실행하지 않고 종료하면 계정에서 여러 라이브 이벤트가 발생합니다. <br/>실행 중인 라이브 이벤트를 중지해야 합니다. 그렇지 않으면 **비용이 청구**됩니다.

## <a name="examine-the-code-that-performs-live-streaming"></a>라이브 스트리밍을 수행하는 코드 검사

이 섹션에서는 *MediaV3LiveApp* 프로젝트의 [Program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/Live/MediaV3LiveApp/Program.cs) 파일에 정의된 함수를 살펴봅니다.

샘플을 정리하지 않고 여러 번 실행하더라도 이름이 충돌하지 않도록, 이 샘플에서는 각 리소스의 고유 접미사를 만듭니다.

> [!IMPORTANT]
> 이 샘플에서는 각 리소스에 고유한 접미사를 사용합니다. 디버깅을 취소하거나 앱을 실행하지 않고 종료하면 계정에서 여러 라이브 이벤트가 발생합니다. <br/>
> 실행 중인 라이브 이벤트를 중지해야 합니다. 그렇지 않으면 **비용이 청구**됩니다.

### <a name="start-using-media-services-apis-with-net-sdk"></a>.NET SDK로 Media Services API 사용하기

.NET으로 Media Services API를 사용하려면 **AzureMediaServicesClient** 개체를 만들어야 합니다. 개체를 만들려면 Azure AD를 사용하여 클라이언트가 Azure에 연결하는 데 필요한 자격 증명을 제공해야 합니다. 아티클의 시작 부분에서 복제한 코드에서 **GetCredentialsAsync** 함수는 로컬 구성 파일에 제공된 자격 증명에 따라 ServiceClientCredentials 개체를 만듭니다. 

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CreateMediaServicesClient)]

### <a name="create-a-live-event"></a>라이브 이벤트 만들기

이 섹션에서는 **통과** 형식의 라이브 이벤트를 만드는 방법을 보여줍니다(None으로 설정된 LiveEventEncodingType). 사용 가능한 라이브 이벤트 유형에 대한 자세한 내용은 [라이브 이벤트 유형](live-events-outputs-concept.md#live-event-types)을 참조하세요. 
 
라이브 이벤트를 만들 때 지정할 수 있는 몇 가지 사항은 다음과 같습니다.

* Media Services 위치.
* 라이브 이벤트의 스트리밍 프로토콜(현재 RTMP 및 부드러운 스트리밍 프로토콜이 지원됨).<br/>라이브 이벤트 또는 연결된 라이브 출력이 실행 중인 동안에는 프로토콜 옵션을 변경할 수 없습니다. 다른 프로토콜이 필요한 경우 각 스트리밍 프로토콜에 대한 별도의 라이브 이벤트를 만드세요.  
* 수집 및 미리 보기에서 IP 제한입니다. 이 라이브 이벤트에 비디오를 수집하도록 허용된 IP 주소를 정의할 수 있습니다. 허용된 IP 주소는 단일 IP 주소(예: '10.0.0.1'), IP 주소 및 CIDR 서브넷 마스크를 사용하는 IP 범위(예: '10.0.0.1/22') 또는 IP 주소와 점으로 구분된 십진수 서브넷 마스크를 사용하는 IP 범위(예: '10.0.0.1(255.255.252.0)')로 지정할 수 있습니다.<br/>지정된 IP 주소가 없고 정의된 규칙이 없는 경우, IP 주소가 허용되지 않습니다. 모든 IP 주소를 허용하려면 규칙을 만들고 0.0.0.0/0으로 설정합니다.<br/>IP 주소가 다음 형식 중 하나에 있어야 합니다. 4개의 숫자를 사용하는 IpV4 주소 또는 CIDR 주소 범위.
* 이벤트를 만들 때 자동 시작을 지정할 수 있습니다. <br/>Autostart가 true로 설정되어 있는 경우 Live Event가 생성 후 시작됩니다. 즉, 라이브 이벤트를 실행하는 즉시 청구가 시작됩니다. 추가 청구를 중지하려면 라이브 이벤트 리소스에 대해 명시적으로 Stop을 호출해야 합니다. 자세한 내용은 [라이브 이벤트 상태 및 청구](live-event-states-billing.md)를 참조하세요.
* 수집 URL을 예측하려면 "베니티" 모드를 설정합니다. 자세한 내용은 [라이브 이벤트 수집 URL](live-events-outputs-concept.md#live-event-ingest-urls)을 참조하세요.

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CreateLiveEvent)]

### <a name="get-ingest-urls"></a>수집 URL 가져오기

라이브 이벤트가 생성되면 라이브 인코더에 제공할 수집 URL을 구할 수 있습니다. 인코더는 이러한 URL을 사용하여 라이브 스트림을 입력합니다.

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#GetIngestURL)]

### <a name="get-the-preview-url"></a>미리 보기 URL 가져오기

previewEndpoint를 사용하여 인코더에서 입력이 실제로 수신되고 있는지 미리 보고 확인합니다.

> [!IMPORTANT]
> 계속하기 전에 비디오가 미리 보기 URL로 전달되고 있는지 확인합니다.

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#GetPreviewURLs)]

### <a name="create-and-manage-live-events-and-live-outputs"></a>라이브 이벤트 및 라이브 출력 만들기 및 관리

라이브 이벤트로 들어오는 스트림이 있으면 자산, 라이브 출력 및 스트리밍 로케이터를 만들어 스트리밍 이벤트를 시작할 수 있습니다. 이렇게 하면 스트림이 보관되고 스트리밍 엔드포인트를 통해 시청자가 스트림을 사용할 수 있게 됩니다.

#### <a name="create-an-asset"></a>자산 만들기

라이브 출력에서 사용할 자산을 만듭니다.

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CreateAsset)]

#### <a name="create-a-live-output"></a>라이브 출력 만들기

라이브 출력은 생성과 동시에 시작되고 삭제되면 중지됩니다. 라이브 출력을 삭제해도 기본 자산과 자산의 콘텐츠는 삭제되지 않습니다.

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CreateLiveOutput)]

#### <a name="create-a-streaming-locator"></a>스트리밍 로케이터 만들기

> [!NOTE]
> Azure Media Services 계정이 만들어지면 **기본** 스트리밍 엔드포인트가 **중지됨** 상태에 있는 계정에 추가됩니다. 콘텐츠 스트리밍을 시작하고 [동적 패키징](dynamic-packaging-overview.md) 및 동적 암호화를 활용하려면 콘텐츠를 스트리밍하려는 스트리밍 엔드포인트가 **실행** 상태에 있어야 합니다.

스트리밍 로케이터를 사용하여 라이브 출력 자산을 게시한 경우 스트리밍 로케이터가 만료 또는 삭제되는 시점 중 먼저 도래하는 시점까지 라이브 이벤트(DVR 기간 길이까지)를 계속 볼 수 있습니다.

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CreateStreamingLocator)]

```csharp

// Get the url to stream the output
ListPathsResponse paths = await client.StreamingLocators.ListPathsAsync(resourceGroupName, accountName, locatorName);

foreach (StreamingPath path in paths.StreamingPaths)
{
    UriBuilder uriBuilder = new UriBuilder();
    uriBuilder.Scheme = "https";
    uriBuilder.Host = streamingEndpoint.HostName;

    uriBuilder.Path = path.Paths[0];
    // Get the URL from the uriBuilder: uriBuilder.ToString()
}
```

### <a name="cleaning-up-resources-in-your-media-services-account"></a>Media Services 계정에서 리소스 정리

스트리밍 이벤트를 완료하고 이전에 프로비저닝된 리소스를 정리하려면 다음 절차를 수행합니다.

* 인코더에서 스트림의 푸시를 중지합니다.
* 라이브 이벤트를 중지합니다. 라이브 이벤트가 중지되면 요금이 발생하지 않습니다. 채널을 다시 시작해야 하는 경우 채널의 수집 URL은 동일하므로 인코더를 다시 구성하지 않아도 됩니다.
* 라이브 이벤트의 보관 파일을 주문형 스트림으로 계속 제공하지 않으려면 스트리밍 엔드포인트를 중지할 수 있습니다. 라이브 이벤트가 중지됨 상태이면 요금이 발생하지 않습니다.

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CleanupLiveEventAndOutput)]

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CleanupLocatorAssetAndStreamingEndpoint)]

## <a name="watch-the-event"></a>이벤트 보기

이벤트를 시청하려면 [스트리밍 로케이터 만들기]에 설명된 코드를 실행할 때 가져온 스트리밍 URL을 복사합니다. 원하는 미디어 플레이어를 사용할 수 있습니다. [Azure Media Player](https://amp.azure.net/libs/amp/latest/docs/index.html)를 사용하여 https://ampdemo.azureedge.net 에서 스트림을 테스트할 수 있습니다.

라이브 이벤트가 중지되면 이벤트를 주문형 콘텐츠로 자동으로 변환합니다. 이벤트를 중지하고 삭제한 후에도 자산을 삭제하지 않는 한 사용자는 주문형 비디오로 보관된 콘텐츠를 스트리밍할 수 있습니다. 자산을 이벤트에서 사용하는 경우 자산을 삭제할 수 없습니다. 이벤트를 먼저 삭제해야 합니다.

## <a name="clean-up-resources"></a>리소스 정리

이 자습서에서 만든 Media Services 및 스토리지 계정을 포함하여 리소스 그룹의 리소스가 더 이상 필요하지 않으면, 앞서 만든 리소스 그룹을 삭제합니다.

다음 CLI 명령을 실행합니다.

```azurecli-interactive
az group delete --name amsResourceGroup
```

> [!IMPORTANT]
> 라이브 이벤트가 계속 실행되도록 두면 비용이 청구됩니다. 프로젝트/프로그램이 충돌하거나 어떤 이유로 닫힌 경우 라이브 이벤트가 청구 상태에서 계속 실행될 수 있습니다.

## <a name="ask-questions-give-feedback-get-updates"></a>질문, 피드백 제공, 업데이트 받기

[Azure Media Services 커뮤니티](media-services-community.md) 문서를 체크 아웃하여 다양한 방법으로 질문을 하고, 피드백을 제공하고, Media Services에 대한 업데이트를 가져올 수 있습니다.

## <a name="next-steps"></a>다음 단계

[파일 스트리밍](stream-files-tutorial-with-api.md)
 
