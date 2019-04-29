---
title: Azure Media Services에서 DRM 동적 암호화 라이선스 배달 서비스 사용 | Microsoft Docs
description: Azure Media Services를 사용하여 Microsoft PlayReady, Google Widevine 또는 Apple FairPlay 라이선스로 암호화된 스트림을 배달할 수 있습니다.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/10/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: f53ae122e9888f3e537a3557b6ac5bd76856c2eb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60995859"
---
# <a name="use-drm-dynamic-encryption-and-license-delivery-service"></a>DRM 동적 암호화 및 라이선스 배달 서비스 사용

Azure Media Services를 사용하여 [PlayReady DRM(디지털 권한 관리)](https://www.microsoft.com/playready/overview/)으로 보호되는 MPEG-DASH, 부드러운 스트리밍 및 HLS(HTTP 라이브 스트리밍) 스트림을 배달할 수 있습니다. Media Services를 사용하여 **Google Widevine** DRM 라이선스로 암호화된 DASH 스트림을 배달할 수도 있습니다. PlayReady와 Widevine은 모두 일반 암호화(ISO/IEC 23001-7 CENC) 사양에 따라 암호화됩니다. Media Services를 사용하면 **Apple FairPlay**(AES-128 CBC)로 HLS 콘텐츠를 암호화할 수도 있습니다. 

또한 Media Services는 PlayReady, Widevine 및 FairPlay DRM 라이선스를 배달하는 서비스를 제공합니다. 사용자가 DRM으로 보호된 콘텐츠를 요청하면 플레이어 애플리케이션이 Media Services 라이선스 서비스에서 라이선스를 요청합니다. 플레이어 애플리케이션에 권한이 있으면 Media Services 라이선스 서비스에서 플레이어에 라이선스를 발급합니다. 라이선스에는 클라이언트 플레이어가 콘텐츠를 해독하고 스트림하는 데 사용할 수 있는 해독 키가 포함됩니다.

이 아티클은 [DRM으로 암호화](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM) 샘플에 기반합니다. 무엇보다도 샘플은 다음과 같은 방법을 보여줍니다.

* 적응 비트 전송률 인코딩에 대한 기본 제공 사전 설정을 사용하고 [HTTP 원본 URL](job-input-from-http-how-to.md)에서 직접 파일을 수집하는 인코딩 변환을 만듭니다.
* 토큰을 확인하는 데 사용된 서명 키를 설정합니다.
* 지정된 구성을 사용하여 키를 배달하기 위해 충족되어야 하는 콘텐츠 키 정책에 대한 요구 사항(제한)을 설정합니다. 

    * 구성 
    
        이 샘플에서는 [PlayReady](playready-license-template-overview.md) 및 [Widevine](widevine-license-template-overview.md) 라이선스를 Media Services 라이선스 배달 서비스에서 배달할 수 있도록 구성합니다. 이 샘플 앱이 [FairPlay](fairplay-license-overview.md) 라이선스를 구성하지 않았지만 FairPlay를 구성하는 데 사용할 수 있는 메서드가 포함됩니다. 원한다면 또 다른 옵션으로 FairPlay 구성을 추가할 수 있습니다.

    * 제한

        앱은 정책에 대한 JWT 토큰 형식 제한을 설정합니다.

* 지정된스트리밍 정책 이름을 사용하여 지정된 자산에 대한 StreamingLocator를 만듭니다. 이 경우에 미리 정의된 정책이 사용됩니다. StreamingLocator에 AES-128(봉투)과 CENC(PlayReady 및 Widevine)의 두 가지 콘텐츠 키를 설정합니다.  
    
    StreamingLocator가 생성되면 출력 자산이 게시되고 재생용 클라이언트에 지원됩니다.

    > [!NOTE]
    > 스트리밍하려는 StreamingEndpoint가 실행 상태인지 확인합니다.

* PlayReady로 암호화된 콘텐츠를 재생하는 데 필요한 DASH 매니페스트 및 PlayReady 토큰을 모두 포함하는 Azure Media Player에 대한 URL을 만듭니다. 샘플은 토큰 만료를 1시간으로 설정합니다. 

    브라우저를 열고 결과 URL을 붙여넣어 이미 작성된 토큰 및 URL을 사용하여 Azure Media Player 데모 페이지를 시작할 수 있습니다.  

    ![DRM으로 보호](./media/protect-with-drm/playready_encrypted_url.png)

> [!NOTE]
> 여러 암호화 유형(AES-128, PlayReady, Widevine, FairPlay)을 사용하여 각 자산을 암호화할 수 있습니다. 결합에 적합한 것을 확인하려면 [스트리밍 프로토콜 및 암호화 유형](content-protection-overview.md#streaming-protocols-and-encryption-types)을 참조합니다.

이 아티클에서 설명된 샘플에서는 다음과 같은 결과를 생성합니다.

![DRM으로 보호된 비디오가 있는 AMS](./media/protect-with-drm/ams_player.png)

## <a name="prerequisites"></a>필수 조건

자습서를 완료하는 데 필요한 조건은 다음과 같습니다.

* [콘텐츠 보호 개요](content-protection-overview.md) 문서를 검토합니다.
* [액세스 제어가 포함된 다중 DRM 콘텐츠 보호 시스템 설계](design-multi-drm-system-with-access-control.md) 검토
* Visual Studio Code 또는 Visual Studio 설치
* [이 빠른 시작](create-account-cli-quickstart.md)에서 설명된 대로 새로운 Azure Media Services 계정을 만듭니다.
* [액세스 API](access-api-cli-how-to.md)를 수행하여 Media Services API를 사용하는 데 필요한 자격 증명 가져오기
* 애플리케이션 구성 파일(appsettings.json)에서 적절한 값을 설정합니다.

## <a name="download-code"></a>코드 다운로드

다음 명령을 사용하여 이 항목에서 설명한 전체 .NET 샘플이 포함된 GitHub 리포지토리를 머신에 복제합니다.

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git
 ```
 
"DRM으로 암호화" 샘플은 [EncryptWithDRM](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM) 폴더에 있습니다.

> [!NOTE]
> 샘플은 애플리케이션을 실행할 때마다 고유한 리소스를 만듭니다. 일반적으로 변환 및 정책 등 기존 리소스를 다시 사용합니다(기존 리소스가 구성이 필요한 경우). 

## <a name="start-using-media-services-apis-with-net-sdk"></a>.NET SDK로 Media Services API 사용하기

.NET으로 Media Services API를 사용하려면 **AzureMediaServicesClient** 개체를 만들어야 합니다. 개체를 만들려면 Azure AD를 사용하여 클라이언트가 Azure에 연결하는 데 필요한 자격 증명을 제공해야 합니다. 아티클의 시작 부분에서 복제한 코드에서 **GetCredentialsAsync** 함수는 로컬 구성 파일에 제공된 자격 증명에 따라 ServiceClientCredentials 개체를 만듭니다. 

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#CreateMediaServicesClient)]

## <a name="create-an-output-asset"></a>출력 자산 만들기  

출력 [Asset](https://docs.microsoft.com/rest/api/media/assets)은 인코딩 작업의 결과를 저장합니다.  

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#CreateOutputAsset)]
 
## <a name="get-or-create-an-encoding-transform"></a>인코딩 Transform을 가져오거나 만들기

새로운 [Transform](https://docs.microsoft.com/rest/api/media/transforms) 인스턴스를 만드는 경우 이 인스턴스를 통해 출력하려는 것이 무엇인지 지정해야 합니다. 필수 매개 변수는 아래 코드와 같이 **TransformOutput** 개체입니다. 각 **TransformOutput**에는 **Preset**이 포함됩니다. **Preset**은 원하는 **TransformOutput**을 생성하는 데 사용되는 비디오 및/또는 오디오 처리 작업에 대한 단계별 지침을 설명합니다. 이 자습서에서 설명하는 샘플은 **AdaptiveStreaming**이라는 기본 제공 Preset을 사용합니다. Preset은 입력 해상도 및 비트 전송률을 기반으로 자동 생성된 비트 전송률 사다리(비트 전송률-해상도 쌍)에 입력 비디오를 인코딩하고 각 비트 전송률-해상도 쌍에 해당하는 H.264 비디오 및 AAC 오디오가 포함된 ISO MP4 파일을 생성합니다. 

새 [변환](https://docs.microsoft.com/rest/api/media/transforms)을 만들기 전에 다음 코드에 표시된 대로 먼저 **Get** 메서드를 사용하여 해당 변환이 이미 있는지 확인해야 합니다.  Media Services v3의 경우, 엔터티가 존재하지 않으면 엔터티에 대한 **Get** 메서드는 **null**을 반환합니다(이름의 대/소문자를 구분하지 않음).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#EnsureTransformExists)]

## <a name="submit-job"></a>작업 제출

위에서 언급했듯이 [Transform](https://docs.microsoft.com/rest/api/media/transforms) 개체는 레시피이며 [Job](https://docs.microsoft.com/rest/api/media/jobs)은 주어진 입력 비디오 또는 오디오 콘텐츠에 **Transform**을 적용하라는 Media Services에 대한 실제 요청입니다. **Job**은 입력 비디오의 위치 및 출력 위치와 같은 정보를 지정합니다.

이 자습서에서는 [HTTP 원본 URL](job-input-from-http-how-to.md)에서 직접 수집한 파일을 기반으로 작업의 입력을 만듭니다.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#SubmitJob)]

## <a name="wait-for-the-job-to-complete"></a>작업이 완료될 때까지 대기

작업을 완료하는 데 시간이 다소 걸리기 때문에 완료되면 알림을 받는 것이 좋습니다. 아래 코드 샘플은 [Job](https://docs.microsoft.com/rest/api/media/jobs)의 상태에 대한 서비스를 폴링하는 방법을 보여줍니다. 폴링은 잠재적인 대기 시간 때문에 프로덕션 애플리케이션에는 권장되지 않습니다. 폴링이 계정에서 초과 사용되면 정체될 수 있습니다. 대신 Event Grid를 사용해야 합니다. [이벤트를 사용자 지정 웹 엔드포인트로 라우팅](job-state-events-cli-how-to.md)을 참조하세요.

**작업**은 일반적으로 **예약됨**, **대기**, **처리 중**, **마침**(최종 상태) 상태를 거칩니다. 작업에서 오류가 발생하면 **오류** 상태가 표시됩니다. 작업을 취소 중인 경우 **취소 중**이 표시되고 완료되면 **취소됨**이 표시됩니다.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#WaitForJobToFinish)]

## <a name="create-a-contentkeypolicy"></a>ContentKeyPolicy 만들기

콘텐츠 키는 자산에 대한 보안 액세스를 제공합니다. 최종 클라이언트에 콘텐츠 키를 배달하는 방법을 구성하는 콘텐츠 키 정책을 만들어야 합니다. 콘텐츠 키는 StreamingLocator와 연결됩니다. Media Services는 권한 있는 사용자에게 암호화 키 및 라이선스를 배달하는 키 배달 서비스도 제공합니다. 

지정된 구성을 사용하여 키를 배달하기 위해 충족되어야 하는 콘텐츠 키 정책에 대한 요구 사항(제한)을 설정해야 합니다. 이 예제에서는 다음 구성 및 요구 사항을 설정합니다.

* 구성 

    [PlayReady](playready-license-template-overview.md) 및 [Widevine](widevine-license-template-overview.md) 라이선스를 Media Services 라이선스 배달 서비스에서 배달할 수 있도록 구성합니다. 이 샘플 앱이 [FairPlay](fairplay-license-overview.md) 라이선스를 구성하지 않았지만 FairPlay를 구성하는 데 사용할 수 있는 메서드가 포함됩니다. 또 다른 옵션으로 FairPlay 구성을 추가할 수 있습니다.

* 제한

    앱은 정책에 대한 JWT 토큰 형식 제한을 설정합니다.

플레이어가 스트림을 요청하면 Media Services는 지정된 키를 사용하고 동적으로 사용자의 콘텐츠를 암호화합니다. 스트림을 해독하기 위해 플레이어는 키 배달 서비스에서 키를 요청합니다. 사용자에게 키를 얻을 수 있는 권한이 있는지 여부를 결정하기 위해 서비스는 키에 지정된 콘텐츠 키 정책을 평가합니다.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#GetOrCreateContentKeyPolicy)]

## <a name="create-a-streaminglocator"></a>StreamingLocator 만들기

인코딩이 완료되고 콘텐츠 키 정책이 설정되면 다음 단계는 출력 자산의 비디오를 클라이언트가 재생할 수 있도록 만드는 것입니다. 다음 두 단계로 이를 수행합니다. 

1. [StreamingLocator](https://docs.microsoft.com/rest/api/media/streaminglocators) 만들기
2. 클라이언트가 사용할 수 있는 스트리밍 URL을 빌드합니다. 

**StreamingLocator**를 만드는 과정을 게시라고 합니다. 기본적으로 **StreamingLocator**는 API 호출을 수행한 직후부터 유효하며, 선택적인 시작 및 종료 시간을 구성하지 않는 한 삭제될 때까지 지속됩니다. 

[StreamingLocator](https://docs.microsoft.com/rest/api/media/streaminglocators)를 만들 때 원하는 **StreamingPolicyName**을 지정해야 합니다. 이 자습서에서는 스트리밍할 콘텐츠를 게시하는 방법을 Azure Media Services에 알려주는 미리 정의된 StreamingPolicies 중 하나를 사용합니다. 이 예제에서는 StreamingLocator.StreamingPolicyName을 "Predefined_MultiDrmCencStreaming" 정책으로 설정합니다. 이 정책은 로케이터에서 생성되고 설정할 두 가지 콘텐츠 키(봉투 및 CENC)에 대한 것임을 나타냅니다. 따라서 봉투, PlayReady 및 Widevine 암호화가 적용됩니다(키가 구성된 DRM 라이선스에 따라 재생 클라이언트로 배달됨). 또한 CBCS(FairPlay)를 사용하여 스트림을 암호화하려면 "Predefined_MultiDrmStreaming"을 사용합니다. 

> [!IMPORTANT]
> 사용자 지정 [StreamingPolicy](https://docs.microsoft.com/rest/api/media/streamingpolicies)를 사용하는 경우 Media Service 계정에 대해 이러한 정책을 제한적으로 설계하고 동일한 암호화 옵션 및 프로토콜이 필요할 때마다 StreamingLocator에 다시 사용해야 합니다. Media Service 계정에는 StreamingPolicy 항목의 수에 대한 할당량이 있습니다. 각 StreamingLocator에 대해 새 StreamingPolicy를 만들지 말아야 합니다.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#CreateStreamingLocator)]

## <a name="get-a-test-token"></a>테스트 토큰 가져오기
        
이 자습서에서는 콘텐츠 키 정책이 토큰을 제한하도록 지정합니다. 토큰 제한 정책에는 STS(보안 토큰 서비스)에서 발급한 토큰이 수반되어야 합니다. Media Services는 [JSON Web Token](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_3)(JWT) 형식의 토큰을 지원하며 그것이 샘플에서 구성하는 것입니다.

ContentKeyPolicy에는 ContentKeyIdentifierClaim을 사용하는데 이는 키 배달 서비스에 제공된 토큰에는 ContentKey의 식별자가 있어야 한다는 의미입니다. 이 샘플에서는 StreamingLocator를 만들 때 콘텐츠 키를 지정하지 않으면 시스템은 임의의 StreamingLocator를 만듭니다. 테스트 토큰을 생성하려면 ContentKeyId를 가져와 ContentKeyIdentifierClaim 클레임에 삽입해야 합니다.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#GetToken)]

## <a name="build-a-dash-streaming-url"></a>DASH 스트리밍 URL 빌드

[StreamingLocator](https://docs.microsoft.com/rest/api/media/streaminglocators)가 만들어졌으므로 스트리밍 URL을 가져올 수 있습니다. URL을 작성하려면 [StreamingEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints) 호스트 이름과 **StreamingLocator** 경로를 연결해야 합니다. 이 샘플에서는 *기본* **StreamingEndpoint**가 사용됩니다. Media Service 계정을 처음 만들 때 *기본* **StreamingEndpoint**는 중지된 상태이므로 **Start**를 호출해야 합니다.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#GetMPEGStreamingUrl)]

## <a name="clean-up-resources-in-your-media-services-account"></a>Media Services 계정의 리소스 정리

일반적으로 재사용할 개체를 제외하고 모두 정리해야 합니다. (일반적으로 Transform를 재사용하고 StreamingLocator 등을 유지합니다.) 실험 후 계정이 정리되도록 하려면 재사용하지 않을 리소스는 삭제해야 합니다.  예를 들어 다음 코드는 Job을 삭제합니다.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#CleanUp)]

## <a name="next-steps"></a>다음 단계

[AES-128로 보호](protect-with-aes128.md)하는 방법 확인
