---
title: Azure Media Services v3를 사용하여 원격 파일 및 스트림 인코딩
description: 이 자습서의 단계에 따라 REST를 사용하여 Azure Media Services에서 URL에 따라 파일을 인코딩하고 콘텐츠를 스트림합니다.
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
ms.openlocfilehash: 350584984bff82c7dc80aff38044d887f981424c
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89256757"
---
# <a name="tutorial-encode-a-remote-file-based-on-url-and-stream-the-video---rest"></a>자습서: URL에 따라 원격 파일 인코딩 및 비디오 스트림 - REST

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Azure Media Services를 사용하면 다양한 브라우저 및 디바이스에서 재생할 수 있는 형식으로 미디어 파일을 인코딩할 수 있습니다. 예를 들어 콘텐츠를 Apple의 HLS 또는 MPEG DASH 형식으로 스트림할 수 있습니다. 스트림하기 전에 고품질 디지털 미디어 파일을 인코딩해야 합니다. 인코딩 지침은 [인코딩 개념](encoding-concept.md)을 참조하세요.

이 자습서에서는 REST를 사용하여 Azure Media Services에서 URL에 따라 파일을 인코딩하고 비디오를 스트림하는 방법을 보여줍니다. 

![비디오 재생](./media/stream-files-tutorial-with-api/final-video.png)

이 자습서에서는 다음을 수행하는 방법에 대해 설명합니다.    

> [!div class="checklist"]
> * Media Services 계정 만들기
> * Media Services API 액세스
> * Postman 파일 다운로드
> * Postman 구성
> * Postman을 사용하여 요청 보내기
> * 스트리밍 URL 테스트
> * 리소스 정리

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>사전 요구 사항

- [Media Services 계정 만들기](./create-account-howto.md)

    리소스 그룹 이름 및 Media Services 계정 이름에 사용한 값을 기억해 두세요.

- [Postman](https://www.getpostman.com/) REST 클라이언트를 설치하여 일부 AMS REST 자습서에 표시되는 REST API를 실행합니다. 

    지금은 **Postman**을 사용하고 있지만 어떤 REST 도구도 괜찮습니다. 다른 대안은 다음과 같습니다. **Visual Studio Code**와 REST 플러그 인을 함께 사용하거나, **Telerik Fiddler**를 사용할 수도 있습니다. 

## <a name="download-postman-files"></a>Postman 파일 다운로드

Postman 컬렉션 및 환경 파일이 포함된 GitHub 리포지토리를 복제합니다.

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-rest-postman.git
 ```

## <a name="access-api"></a>액세스 API

자세한 내용은 [Media Services API에 액세스할 수 있는 자격 증명 가져오기](access-api-howto.md)를 참조하세요.

## <a name="configure-postman"></a>Postman 구성

### <a name="configure-the-environment"></a>환경 구성 

1. **Postman** 앱을 엽니다.
2. 화면 오른쪽에서 **환경 관리** 옵션을 선택합니다.

    ![환경 관리](./media/develop-with-postman/postman-import-env.png)
4. **환경 관리** 대화 상자에서 **가져오기**를 클릭합니다.
2. `https://github.com/Azure-Samples/media-services-v3-rest-postman.git`을 복제할 때 다운로드한 `Azure Media Service v3 Environment.postman_environment.json` 파일을 찾습니다.
6. **Azure Media Service v3 Environment** 환경이 추가되었습니다.

    > [!Note]
    > 액세스 변수를 위의 **Media Services API 액세스** 섹션에서 가져온 값으로 업데이트합니다.

7. 선택한 파일을 두 번 클릭하고[API 액세스](#access-api) 단계를 수행하여 가져온 값을 입력합니다.
8. 대화 상자를 닫습니다.
9. 드롭다운에서 **Azure Media Service v3 Environment** 환경을 선택합니다.

    ![환경 선택](./media/develop-with-postman/choose-env.png)
   
### <a name="configure-the-collection"></a>컬렉션 구성

1. **가져오기**를 클릭하여 컬렉션 파일을 가져옵니다.
1. `https://github.com/Azure-Samples/media-services-v3-rest-postman.git`을 복제할 때 다운로드한 `Media Services v3.postman_collection.json` 파일을 찾습니다.
3. **Media Services v3.postman_collection.json** 파일을 선택합니다.

    ![파일 가져오기](./media/develop-with-postman/postman-import-collection.png)

## <a name="send-requests-using-postman"></a>Postman을 사용하여 요청 보내기

이 섹션에서는 파일을 스트림할 수 있도록 URL 인코딩 및 만들기와 관련된 요청을 보냅니다. 특히 다음 요청을 보냅니다.

1. 서비스 주체 인증을 위한 Azure AD 토큰 가져오기
1. 스트리밍 엔드포인트 시작
2. 출력 자산 만들기
3. Transform 만들기
4. 작업 만들기
5. 스트리밍 로케이터 만들기
6. 스트리밍 로케이터의 경로 나열

> [!Note]
>  이 자습서에서는 고유한 이름을 가진 모든 리소스를 만든다고 가정합니다.  

### <a name="get-azure-ad-token"></a>Azure AD 토큰 가져오기 

1. Postman 앱의 왼쪽 창에서 “1단계: AAD 인증 토큰 가져오기"를 선택합니다.
2. 그런 다음, "서비스 주체 인증을 위한 Azure AD 토큰 가져오기"를 선택합니다.
3. **보내기**를 누릅니다.

    다음 **POST** 작업을 보냅니다.

    ```
    https://login.microsoftonline.com/:tenantId/oauth2/token
    ```

4. 응답은 토큰과 함께 반환되며 "AccessToken" 환경 변수를 토큰 값으로 설정합니다. "AccessToken"을 설정하는 코드를 보려면 **테스트** 탭을 클릭합니다. 

    ![AAD 토큰 가져오기](./media/develop-with-postman/postman-get-aad-auth-token.png)


### <a name="start-a-streaming-endpoint"></a>스트리밍 엔드포인트 시작

스트리밍을 사용하도록 설정하려면 비디오를 스트리밍하려는 [스트리밍 엔드포인트](./streaming-endpoint-concept.md)를 시작해야 합니다.

> [!NOTE]
> 스트리밍 엔드포인트가 실행 중인 상태일 때만 요금이 청구됩니다.

1. Postman 앱의 왼쪽 창에서 “스트리밍 및 라이브”를 선택합니다.
2. 그런 다음, “StreamingEndpoint 시작”을 선택합니다.
3. **보내기**를 누릅니다.

    * 다음 **POST** 작업이 전송됩니다.

        ```
        https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaservices/:accountName/streamingEndpoints/:streamingEndpointName/start?api-version={{api-version}}
        ```
    * 요청에 성공하면 `Status: 202 Accepted`가 반환됩니다.

        이 상태는 요청이 처리를 위해 수락되었지만 처리가 아직 완료되지 않았음을 의미합니다. `Azure-AsyncOperation` 응답 헤더의 값을 기준으로 작업 상태를 쿼리할 수 있습니다.

        예를 들어 다음 GET 작업은 작업 상태를 반환합니다.
        
        `https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/<resourceGroupName>/providers/Microsoft.Media/mediaservices/<accountName>/streamingendpointoperations/1be71957-4edc-4f3c-a29d-5c2777136a2e?api-version=2018-07-01`

        [비동기 Azure 작업 추적](../../azure-resource-manager/management/async-operations.md) 문서에서는 응답에서 반환된 값을 통해 비동기 Azure 작업의 상태를 추적하는 방법에 대해 자세히 설명합니다.

### <a name="create-an-output-asset"></a>출력 자산 만들기

출력 [Asset](/rest/api/media/assets)은 인코딩 작업의 결과를 저장합니다. 

1. Postman 앱의 왼쪽 창에서 “자산”을 선택합니다.
2. 그런 다음, "자산 만들기 또는 업데이트"를 선택합니다.
3. **보내기**를 누릅니다.

    * 다음 **PUT** 작업을 보냅니다.

        ```
        https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/assets/:assetName?api-version={{api-version}}
        ```
    * 작업에는 다음 본문이 포함됩니다.

        ```json
        {
        "properties": {
            "description": "My Asset",
            "alternateId" : "some GUID"
         }
        }
        ```

### <a name="create-a-transform"></a>변환 만들기

Media Services에서 콘텐츠를 인코딩하거나 처리할 때 인코딩 설정을 레시피로 설정하는 것이 일반적인 패턴입니다. 그런 다음, 이 레시피가 비디오에 적용되도록 **Job**을 제출합니다. 새 비디오에 대한 새 작업을 제출하면 라이브러리의 모든 비디오에 레시피가 적용됩니다. Media Services의 레시피를 **Transform**이라고 합니다. 자세한 내용은 [Transform 및 Jobs](./transforms-jobs-concept.md)를 참조하세요. 이 자습서에서 설명하는 샘플은 다양한 iOS 및 Android 디바이스로 스트리밍하기 위해 비디오를 인코딩하는 레시피를 정의합니다. 

새로운 [Transform](/rest/api/media/transforms) 인스턴스를 만드는 경우 이 인스턴스를 통해 출력하려는 것이 무엇인지 지정해야 합니다. 필요한 매개 변수는 **TransformOutput** 개체입니다. 각 **TransformOutput**에는 **Preset**이 포함됩니다. **Preset**은 원하는 **TransformOutput**을 생성하는 데 사용되는 비디오 및/또는 오디오 처리 작업에 대한 단계별 지침을 설명합니다. 이 자습서에서 설명하는 샘플은 **AdaptiveStreaming**이라는 기본 제공 Preset을 사용합니다. Preset은 입력 해상도 및 비트 전송률을 기반으로 자동 생성된 비트 전송률 사다리(비트 전송률-해상도 쌍)에 입력 비디오를 인코딩하고 각 비트 전송률-해상도 쌍에 해당하는 H.264 비디오 및 AAC 오디오가 포함된 ISO MP4 파일을 생성합니다. 이 Preset에 대한 정보는 [자동 생성된 비트 전송률 사다리](autogen-bitrate-ladder.md)를 참조하세요.

기본 제공 EncoderNamedPreset을 사용하거나 사용자 지정 사전 설정을 사용할 수 있습니다. 

> [!Note]
> [Transform](/rest/api/media/transforms)을 만들 때는 먼저 **Get** 메서드를 사용하여 해당 Transform이 이미 있는지 확인해야 합니다. 이 자습서에서는 고유한 이름으로 변환을 만든다고 가정합니다.

1. Postman 앱의 왼쪽 창에서 “인코딩 및 분석”을 선택합니다.
2. 그런 다음, "변환 만들기"를 선택합니다.
3. **보내기**를 누릅니다.

    * 다음 **PUT** 작업을 보냅니다.

        ```
        https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/transforms/:transformName?api-version={{api-version}}
        ```
    * 작업에는 다음 본문이 포함됩니다.

        ```json
        {
            "properties": {
                "description": "Standard Transform using an Adaptive Streaming encoding preset from the library of built-in Standard Encoder presets",
                "outputs": [
                    {
                    "onError": "StopProcessingJob",
                "relativePriority": "Normal",
                    "preset": {
                        "@odata.type": "#Microsoft.Media.BuiltInStandardEncoderPreset",
                        "presetName": "AdaptiveStreaming"
                    }
                    }
                ]
            }
        }
        ```

### <a name="create-a-job"></a>작업 만들기

[작업](/rest/api/media/jobs)은 만들어진 **변환**을 지정된 입력 비디오 또는 오디오 콘텐츠에 적용하기 위한 Media Services에 대한 실제 요청입니다. **Job**은 입력 비디오의 위치 및 출력 위치와 같은 정보를 지정합니다.

이 예제에서 작업의 입력은 HTTPS URL(“https:\//nimbuscdn-nimbuspm.streaming.mediaservices.windows.net/2b533311-b215-4409-80af-529c3e853622/”)을 기반으로 합니다.

1. Postman 앱의 왼쪽 창에서 “인코딩 및 분석”을 선택합니다.
2. 그런 다음, "작업 만들기 또는 업데이트"를 선택합니다.
3. **보내기**를 누릅니다.

    * 다음 **PUT** 작업을 보냅니다.

        ```
        https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/transforms/:transformName/jobs/:jobName?api-version={{api-version}}
        ```
    * 작업에는 다음 본문이 포함됩니다.

        ```json
        {
        "properties": {
            "input": {
            "@odata.type": "#Microsoft.Media.JobInputHttp",
            "baseUri": "https://nimbuscdn-nimbuspm.streaming.mediaservices.windows.net/2b533311-b215-4409-80af-529c3e853622/",
            "files": [
                    "Ignite-short.mp4"
                ]
            },
            "outputs": [
            {
                "@odata.type": "#Microsoft.Media.JobOutputAsset",
                "assetName": "testAsset1"
            }
            ]
        }
        }
        ```

작업을 완료하는 데 시간이 다소 걸리기 때문에 완료되면 알림을 받는 것이 좋습니다. 작업 진행률을 보려면 Event Grid를 사용하는 것이 좋습니다. Event Grid는 고가용성, 일관된 성능 및 동적 크기 조정을 위해 설계되었습니다. Event Grid를 사용하면 앱이 사용자 지정 원본뿐만 아니라 거의 모든 Azure 서비스의 이벤트에 대해 수신 대기하고 대응할 수 있습니다. 간단한 HTTP 기반 반응형 이벤트 처리는 이벤트의 지능형 필터링 및 라우팅을 통해 효율적인 솔루션을 구축하는 데 도움이 됩니다.  [이벤트를 사용자 지정 웹 엔드포인트로 라우팅](job-state-events-cli-how-to.md)을 참조하세요.

**작업**은 일반적으로 **예약됨**, **대기**, **처리 중**, **마침**(최종 상태) 상태를 거칩니다. 작업에서 오류가 발생하면 **오류** 상태가 표시됩니다. 작업을 취소 중인 경우 **취소 중**이 표시되고 완료되면 **취소됨**이 표시됩니다.

#### <a name="job-error-codes"></a>작업 오류 코드

[오류 코드](/rest/api/media/jobs/get#joberrorcode)를 참조하세요.

### <a name="create-a-streaming-locator"></a>스트리밍 로케이터 만들기

인코딩 작업이 완료된 후에 수행할 단계는 출력 **자산**의 비디오를 클라이언트에서 재생할 수 있도록 만드는 것입니다. 이 작업은 두 단계로 수행할 수 있습니다. 첫째, [StreamingLocator](/rest/api/media/streaminglocators)를 만들고 둘째, 클라이언트가 사용할 수 있는 스트리밍 URL을 작성합니다. 

스트리밍 로케이터를 만드는 과정을 게시라고 합니다. 기본적으로 스트리밍 로케이터는 API 호출을 수행한 직후부터 유효하며, 선택적인 시작 및 종료 시간을 구성하지 않는 한 삭제될 때까지 지속됩니다. 

[StreamingLocator](/rest/api/media/streaminglocators)를 만들 때 원하는 **StreamingPolicyName**을 지정해야 합니다. 이 예제에서는 미리 정의된 암호화되지 않은 스트리밍 정책(“Predefined_ClearStreamingOnly”)을 사용하도록 암호화되지 않은(in-the-clear 또는 non-encrypted) 콘텐츠를 스트림합니다.

> [!IMPORTANT]
> 사용자 지정 [StreamingPolicy](/rest/api/media/streamingpolicies)를 사용하는 경우 Media Service 계정에 대해 이러한 정책을 제한적으로 설계하고 동일한 암호화 옵션 및 프로토콜이 필요할 때마다 StreamingLocator에 다시 사용해야 합니다. 

Media Service 계정에는 **스트리밍 정책** 항목의 수에 대한 할당량이 있습니다. 각 스트리밍 로케이터에 대한 새 **스트리밍 정책**을 만들지 않아야 합니다.

1. Postman 앱의 왼쪽 창에서 "스트리밍 정책 및 로케이터"를 선택합니다.
2. 그런 다음, "스트리밍 로케이터 만들기(clear)"를 선택합니다.
3. **보내기**를 누릅니다.

    * 다음 **PUT** 작업을 보냅니다.

        ```
        https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/streamingLocators/:streamingLocatorName?api-version={{api-version}}
        ```
    * 작업에는 다음 본문이 포함됩니다.

        ```json
        {
          "properties": {
            "streamingPolicyName": "Predefined_ClearStreamingOnly",
            "assetName": "testAsset1",
            "contentKeys": [],
            "filters": []
         }
        }
        ```

### <a name="list-paths-and-build-streaming-urls"></a>경로 나열 및 스트리밍 URL 작성

#### <a name="list-paths"></a>경로 나열

[스트리밍 로케이터](/rest/api/media/streaminglocators)가 만들어졌으므로 스트리밍 URL을 가져올 수 있습니다.

1. Postman 앱의 왼쪽 창에서 “스트리밍 정책”을 선택합니다.
2. 그런 다음, "경로 나열"을 선택합니다.
3. **보내기**를 누릅니다.

    * 다음 **POST** 작업을 보냅니다.

        ```
        https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/streamingLocators/:streamingLocatorName/listPaths?api-version={{api-version}}
        ```
        
    * 작업에는 본문이 포함되지 않습니다.
        
4. 스트리밍에 사용하려는 경로 중 하나를 적어 둡니다. 이 경로는 다음 섹션에서 사용합니다. 이 경우 다음 경로가 반환되었습니다.
    
    ```
    "streamingPaths": [
        {
            "streamingProtocol": "Hls",
            "encryptionScheme": "NoEncryption",
            "paths": [
                "/cdb80234-1d94-42a9-b056-0eefa78e5c63/Ignite-short.ism/manifest(format=m3u8-aapl)"
            ]
        },
        {
            "streamingProtocol": "Dash",
            "encryptionScheme": "NoEncryption",
            "paths": [
                "/cdb80234-1d94-42a9-b056-0eefa78e5c63/Ignite-short.ism/manifest(format=mpd-time-csf)"
            ]
        },
        {
            "streamingProtocol": "SmoothStreaming",
            "encryptionScheme": "NoEncryption",
            "paths": [
                "/cdb80234-1d94-42a9-b056-0eefa78e5c63/Ignite-short.ism/manifest"
            ]
        }
    ]
    ```

#### <a name="build-the-streaming-urls"></a>스트리밍 URL 작성

이 섹션에서는 HLS 스트리밍 URL을 작성해 보겠습니다. URL은 다음 값으로 구성됩니다.

1. 데이터를 보내는 프로토콜입니다. 이 경우 "https"입니다.

    > [!NOTE]
    > 플레이어가 https 사이트에 호스트 될 경우 URL을 "https"로 업데이트해야 합니다.

2. StreamingEndpoint의 호스트 이름입니다. 이 경우 이름은 "amsaccount-usw22.streaming.media.azure.net"입니다.

    호스트 이름을 가져오기 위해 다음 GET 작업을 사용할 수 있습니다.
    
    ```
    https://management.azure.com/subscriptions/00000000-0000-0000-0000-0000000000000/resourceGroups/amsResourceGroup/providers/Microsoft.Media/mediaservices/amsaccount/streamingEndpoints/default?api-version={{api-version}}
    ```
    
3. 이전 경로 나열 섹션에서 가져온 경로입니다.  

결과적으로 다음 HLS URL이 작성되었습니다.

```
https://amsaccount-usw22.streaming.media.azure.net/cdb80234-1d94-42a9-b056-0eefa78e5c63/Ignite-short.ism/manifest(format=m3u8-aapl)
```

## <a name="test-the-streaming-url"></a>스트리밍 URL 테스트


> [!NOTE]
> 스트리밍하려는 **스트리밍 엔드포인트**가 실행 중인지 확인합니다.

이 문서에서는 스트림을 테스트하기 위해 Azure Media Player를 사용합니다. 

1. 웹 브라우저를 열고 [https://aka.ms/azuremediaplayer/](https://aka.ms/azuremediaplayer/)로 이동합니다.
2. **URL:** 상자에서 작성한 URL을 붙여넣습니다. 
3. **플레이어 업데이트**를 누릅니다.

Azure Media Player는 테스트용으로 사용할 수 있지만 프로덕션 환경에서는 사용할 수 없습니다. 

## <a name="clean-up-resources-in-your-media-services-account"></a>Media Services 계정의 리소스 정리

일반적으로 재사용할 개체를 제외하고 모두 정리해야 합니다. 일반적으로 **변환**을 재사용하고 **스트리밍 로케이터** 등을 유지합니다. 실험 후 계정이 정리되도록 하려면 재사용하지 않을 리소스는 삭제해야 합니다.  

리소스를 삭제하려면 삭제할 리소스 아래에서 "삭제 ..." 작업을 선택합니다.

## <a name="clean-up-resources"></a>리소스 정리

이 자습서에서 만든 Media Services 및 스토리지 계정을 포함하여 리소스 그룹의 리소스가 더 이상 필요하지 않으면, 앞서 만든 리소스 그룹을 삭제합니다.  

다음 CLI 명령을 실행합니다.

```azurecli
az group delete --name amsResourceGroup
```

## <a name="ask-questions-give-feedback-get-updates"></a>질문, 피드백 제공, 업데이트 받기

[Azure Media Services 커뮤니티](media-services-community.md) 문서를 체크 아웃하여 다양한 방법으로 질문을 하고, 피드백을 제공하고, Media Services에 대한 업데이트를 가져올 수 있습니다.

## <a name="next-steps"></a>다음 단계

비디오를 업로드, 인코딩 및 스트리밍하는 방법을 알아보았으니 이제 다음 문서를 참조하십시오. 

> [!div class="nextstepaction"]
> [비디오 분석](analyze-videos-tutorial-with-api.md)
