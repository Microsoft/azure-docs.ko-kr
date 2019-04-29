---
title: Azure Media Services AES-128을 사용 하 여 비디오를 암호화 하는 데 | Microsoft Docs
description: Microsoft Azure Media Services를 사용하면 AES 128비트 암호화 키로 암호화된 콘텐츠를 배달할 수 있습니다. Media Services는 권한 있는 사용자에게 암호화 키를 제공하는 키 배달 서비스도 제공합니다. 이 항목에서는 AES-128로 동적으로 암호화하는 방법과 키 배달 서비스를 사용하는 방법을 보여 줍니다.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/21/2019
ms.author: juliako
ms.openlocfilehash: aa6b4ef76b039e9e24b4a72cfb6e76dcfae8378d
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62111990"
---
# <a name="tutorial-use-aes-128-dynamic-encryption-and-the-key-delivery-service"></a>자습서: AES-128 동적 암호화 및 키 전달 서비스 사용

Media Services에서는 128비트 암호화 키를 사용하여 AES로 암호화된 HLS(HTTP 라이브 스트리밍), MPEG-DASH 및 부드러운 스트리밍을 배달할 수 있습니다. Media Services는 권한 있는 사용자에게 암호화 키를 제공하는 키 배달 서비스도 제공합니다. Media services 동적으로 비디오를 암호화 하려는 경우 스트리밍 로케이터를 사용 하 여 암호화 키를 연결 하 고 콘텐츠 키 정책을 구성할 수도 있습니다. 플레이어가 스트림을 요청 될 때 Media Services AES-128로 콘텐츠를 동적으로 암호화 하는 지정 된 키를 사용 합니다. 스트림을 해독하기 위해 플레이어는 키 배달 서비스에서 키를 요청합니다. 사용자에게 키를 얻을 수 있는 권한이 있는지 여부를 결정하기 위해 서비스는 키에 지정된 콘텐츠 키 정책을 평가합니다.

여러 암호화 유형(AES-128, PlayReady, Widevine, FairPlay)을 사용하여 각 자산을 암호화할 수 있습니다. 결합에 적합한 것을 확인하려면 [스트리밍 프로토콜 및 암호화 유형](content-protection-overview.md#streaming-protocols-and-encryption-types)을 참조합니다. 도 참조 하세요 [DRM으로 보호 하는 방법을](protect-with-drm.md)합니다.

샘플의 출력을이 문서에서는 Azure Media Player에 대 한 URL, 매니페스트 URL 및 콘텐츠를 재생 하는 데 필요한 AES 토큰을 포함 합니다. 샘플은 JWT 토큰의 만료를 1시간으로 설정합니다. 브라우저를 열고 결과 URL을 붙여넣어 이미 다음 형식으로 작성된 토큰 및 URL을 사용하여 Azure Media Player 데모 페이지를 시작할 수 있습니다. ```https://ampdemo.azureedge.net/?url= {dash Manifest URL} &aes=true&aestoken=Bearer%3D{ JWT Token here}```.

이 자습서에서는 다음을 수행하는 방법에 대해 설명합니다.    

> [!div class="checklist"]
> * 다운로드 합니다 [EncryptWithAES](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES) 문서에 설명 된 샘플
> * .NET SDK로 Media Services API 사용하기
> * 출력 자산 만들기
> * 인코딩 변환을 만듭니다
> * 작업 제출
> * 작업이 완료될 때까지 대기
> * 콘텐츠 키 정책 만들기
> * JWT 토큰 제한을 사용 하도록 정책을 구성합니다 
> * 스트리밍 로케이터 만들기
> * AES (ClearKey)를 사용 하 여 비디오를 암호화 하 여 스트리밍 로케이터를 구성 합니다.
> * 테스트 토큰 가져오기
> * 스트리밍 URL 작성
> * 리소스 정리

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>필수 조건

자습서를 완료하는 데 필요한 조건은 다음과 같습니다.

* 검토 합니다 [콘텐츠 보호 개요](content-protection-overview.md) 문서
* Visual Studio Code 또는 Visual Studio 설치
* [Media Services 계정 만들기](create-account-cli-quickstart.md)
* [액세스 API](access-api-cli-how-to.md)를 수행하여 Media Services API를 사용하는 데 필요한 자격 증명 가져오기

## <a name="download-code"></a>코드 다운로드

다음 명령을 사용하여 이 항목에서 설명한 전체 .NET 샘플이 포함된 GitHub 리포지토리를 머신에 복제합니다.

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git
 ```
 
"AES-128로 암호화" 샘플은 [EncryptWithAES](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES) 폴더에 있습니다.

> [!NOTE]
> 샘플은 애플리케이션을 실행할 때마다 고유한 리소스를 만듭니다. 일반적으로 변환 및 정책 등 기존 리소스를 다시 사용합니다(기존 리소스가 구성이 필요한 경우). 

## <a name="start-using-media-services-apis-with-net-sdk"></a>.NET SDK로 Media Services API 사용하기

.NET으로 Media Services API를 사용하려면 **AzureMediaServicesClient** 개체를 만들어야 합니다. 개체를 만들려면 Azure AD를 사용하여 클라이언트가 Azure에 연결하는 데 필요한 자격 증명을 제공해야 합니다. 아티클의 시작 부분에서 복제한 코드에서 **GetCredentialsAsync** 함수는 로컬 구성 파일에 제공된 자격 증명에 따라 ServiceClientCredentials 개체를 만듭니다. 

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#CreateMediaServicesClient)]

## <a name="create-an-output-asset"></a>출력 자산 만들기  

출력 [Asset](https://docs.microsoft.com/rest/api/media/assets)은 인코딩 작업의 결과를 저장합니다.  

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#CreateOutputAsset)]
 
## <a name="get-or-create-an-encoding-transform"></a>인코딩 Transform을 가져오거나 만들기

새로운 [Transform](https://docs.microsoft.com/rest/api/media/transforms) 인스턴스를 만드는 경우 이 인스턴스를 통해 출력하려는 것이 무엇인지 지정해야 합니다. 필수 매개 변수는 아래 코드와 같이 **TransformOutput** 개체입니다. 각 **TransformOutput**에는 **Preset**이 포함됩니다. **Preset**은 원하는 **TransformOutput**을 생성하는 데 사용되는 비디오 및/또는 오디오 처리 작업에 대한 단계별 지침을 설명합니다. 이 자습서에서 설명하는 샘플은 **AdaptiveStreaming**이라는 기본 제공 Preset을 사용합니다. Preset은 입력 해상도 및 비트 전송률을 기반으로 자동 생성된 비트 전송률 사다리(비트 전송률-해상도 쌍)에 입력 비디오를 인코딩하고 각 비트 전송률-해상도 쌍에 해당하는 H.264 비디오 및 AAC 오디오가 포함된 ISO MP4 파일을 생성합니다. 

새 [변환](https://docs.microsoft.com/rest/api/media/transforms)을 만들기 전에 다음 코드에 표시된 대로 먼저 **Get** 메서드를 사용하여 해당 변환이 이미 있는지 확인해야 합니다.  Media Services v3의 경우, 엔터티가 존재하지 않으면 엔터티에 대한 **Get** 메서드는 **null**을 반환합니다(이름의 대/소문자를 구분하지 않음).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#EnsureTransformExists)]

## <a name="submit-job"></a>작업 제출

위에서 언급했듯이 [Transform](https://docs.microsoft.com/rest/api/media/transforms) 개체는 레시피이며 [Job](https://docs.microsoft.com/rest/api/media/jobs)은 주어진 입력 비디오 또는 오디오 콘텐츠에 **Transform**을 적용하라는 Media Services에 대한 실제 요청입니다. **Job**은 입력 비디오의 위치 및 출력 위치와 같은 정보를 지정합니다.

이 자습서에서는 [HTTP 원본 URL](job-input-from-http-how-to.md)에서 직접 수집한 파일을 기반으로 작업의 입력을 만듭니다.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#SubmitJob)]

## <a name="wait-for-the-job-to-complete"></a>작업이 완료될 때까지 대기

작업을 완료하는 데 시간이 다소 걸리기 때문에 완료되면 알림을 받는 것이 좋습니다. 아래 코드 샘플은 [Job](https://docs.microsoft.com/rest/api/media/jobs)의 상태에 대한 서비스를 폴링하는 방법을 보여줍니다. 폴링은 잠재적인 대기 시간 때문에 프로덕션 애플리케이션에는 권장되지 않습니다. 폴링이 계정에서 초과 사용되면 정체될 수 있습니다. 대신 Event Grid를 사용해야 합니다. [이벤트를 사용자 지정 웹 엔드포인트로 라우팅](job-state-events-cli-how-to.md)을 참조하세요.

**작업**은 일반적으로 **예약됨**, **대기**, **처리 중**, **마침**(최종 상태) 상태를 거칩니다. 작업에서 오류가 발생하면 **오류** 상태가 표시됩니다. 작업을 취소 중인 경우 **취소 중**이 표시되고 완료되면 **취소됨**이 표시됩니다.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#WaitForJobToFinish)]

## <a name="create-a-content-key-policy"></a>콘텐츠 키 정책 만들기

콘텐츠 키는 자산에 대한 보안 액세스를 제공합니다. 만들어야를 **콘텐츠 키 정책** 최종 클라이언트에 콘텐츠 키 배달 방법을 구성 합니다. 와 관련 된 콘텐츠 키 **스트리밍 로케이터**합니다. Media Services는 권한 있는 사용자에게 암호화 키를 제공하는 키 배달 서비스도 제공합니다. 

플레이어가 스트림을 요청하면 Media Services는 지정된 키를 사용하여 동적으로 사용자의 콘텐츠를 암호화합니다(이 경우 AES 암호화를 사용하여.) 스트림을 해독하기 위해 플레이어는 키 배달 서비스에서 키를 요청합니다. 사용자에게 키를 얻을 수 있는 권한이 있는지 여부를 결정하기 위해 서비스는 키에 지정된 콘텐츠 키 정책을 평가합니다.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#GetOrCreateContentKeyPolicy)]

## <a name="create-a-streaming-locator"></a>스트리밍 로케이터 만들기

인코딩이 완료되고 콘텐츠 키 정책이 설정되면 다음 단계는 출력 자산의 비디오를 클라이언트가 재생할 수 있도록 만드는 것입니다. 다음 두 단계로 이를 수행합니다. 

1. [스트리밍 로케이터](https://docs.microsoft.com/rest/api/media/streaminglocators) 만들기
2. 클라이언트가 사용할 수 있는 스트리밍 URL을 빌드합니다. 

만드는 과정을 **스트리밍 로케이터** 게시 라고 합니다. 기본적으로 **스트리밍 로케이터**는 API 호출을 수행한 직후부터 유효하며, 선택적인 시작 및 종료 시간을 구성하지 않는 한 삭제될 때까지 지속됩니다. 

[스트리밍 로케이터](https://docs.microsoft.com/rest/api/media/streaminglocators)를 만들 때 원하는 **StreamingPolicyName**을 지정해야 합니다. 이 자습서에서는 스트리밍에 대한 콘텐츠를 게시하는 방법을 Azure Media Services에 알려주는 PredefinedStreamingPolicies 중 하나를 사용하고 있습니다. 이 예제에서는 AES 봉투 암호화를 적용합니다(DRM 라이선스 아닌 HTTPS를 통해 키를 재생 클라이언트에 배달하기 때문에 ClearKey 암호화라고도 함).

> [!IMPORTANT]
> 사용자 지정 [StreamingPolicy](https://docs.microsoft.com/rest/api/media/streamingpolicies)를 사용하는 경우 Media Service 계정에 대해 이러한 정책을 제한적으로 설계하고 동일한 암호화 옵션 및 프로토콜이 필요할 때마다 Streaming Locator에 다시 사용해야 합니다. Media Service 계정에는 StreamingPolicy 항목의 수에 대한 할당량이 있습니다. 사용자를 만들지 말아야 새 StreamingPolicy 각 스트리밍 로케이터에 대 한 합니다.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#CreateStreamingLocator)]

## <a name="get-a-test-token"></a>테스트 토큰 가져오기
        
이 자습서에서는 콘텐츠 키 정책이 토큰을 제한하도록 지정합니다. 토큰 제한 정책에는 STS(보안 토큰 서비스)에서 발급한 토큰이 수반되어야 합니다. Media Services는 [JSON Web Token](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_3)(JWT) 형식의 토큰을 지원하며 그것이 샘플에서 구성하는 것입니다.

ContentKeyIdentifierClaim에 사용 되는 **콘텐츠 키 정책**, 키 배달 서비스에 제공 된 토큰의 콘텐츠 키의 식별자를 있어야 한다는 의미입니다. 이 샘플에서는 콘텐츠를 지정 하지 않은 스트리밍 로케이터를 만들 때 키를 시스템에 임의의 항목을 생성 합니다. 테스트 토큰을 생성하려면 ContentKeyId를 가져와 ContentKeyIdentifierClaim 클레임에 삽입해야 합니다.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#GetToken)]

## <a name="build-a-dash-streaming-url"></a>DASH 스트리밍 URL 빌드

이제는 [스트리밍 로케이터](https://docs.microsoft.com/rest/api/media/streaminglocators) 되었습니다 만들어지면 스트리밍 Url을 가져올 수 있습니다. URL을 작성 하려면 연결 해야 합니다 [StreamingEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints) 호스트 이름 및 **스트리밍 로케이터** 경로입니다. 이 샘플에서는 *기본* **스트리밍 엔드포인트**가 사용됩니다. Media Service 계정을 처음으로 만들면 이 *기본* **스트리밍 엔드포인트**가 중지된 상태이므로 **Start**를 호출해야 합니다.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#GetMPEGStreamingUrl)]

## <a name="clean-up-resources-in-your-media-services-account"></a>Media Services 계정의 리소스 정리

일반적으로 정리 해야 다시 사용 하려는 개체를 제외한 모든 것 (일반적으로 Transform를 재사용 및 스트리밍 로케이터 등을 유지 합니다.). 실험 후 계정이 정리되도록 하려면 재사용하지 않을 리소스는 삭제해야 합니다.  예를 들어 다음 코드는 Job을 삭제합니다.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#CleanUp)]

## <a name="clean-up-resources"></a>리소스 정리

이 자습서에서 만든 Media Services 및 저장소 계정을 포함하여 리소스 그룹의 리소스가 더 이상 필요하지 않으면, 앞서 만든 리소스 그룹을 삭제합니다. 

다음 CLI 명령을 실행합니다.

```azurecli
az group delete --name amsResourceGroup
```

## <a name="provide-feedback"></a>피드백 제공

[Azure Media Services 커뮤니티](media-services-community.md) 문서를 체크 아웃하여 다양한 방법으로 질문을 하고, 피드백을 제공하고, Media Services에 대한 업데이트를 가져올 수 있습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [DRM으로 보호](protect-with-drm.md)
