---
title: REST를 사용하여 Azure Media Services 콘텐츠 게시
description: 스트리밍 URL을 작성하는 데 사용되는 로케이터를 만드는 방법에 대해 알아봅니다. REST API를 사용하는 코드입니다.
author: Juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: ff332c30-30c6-4ed1-99d0-5fffd25d4f23
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: 974f0af461ecdc7de820191950b010035d02a601
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60598298"
---
# <a name="publish-azure-media-services-content-using-rest"></a>REST를 사용하여 Azure Media Services 콘텐츠 게시 
> [!div class="op_single_selector"]
> * [.NET](media-services-deliver-streaming-content.md)
> * [REST (영문)](media-services-rest-deliver-streaming-content.md)
> * [포털](media-services-portal-publish.md)
> 
> 

적응 비트 전송률 MP4 집합은 주문형 스트리밍 로케이터를 만들고 스트리밍 URL을 작성하여 스트리밍할 수 있습니다. [자산 인코딩](media-services-rest-encode-asset.md) 문서에서는 적응 비트 전송률 MP4 집합으로 인코딩하는 방법을 설명합니다. 콘텐츠가 암호화되어 있는 경우 [이 문서](media-services-rest-configure-asset-delivery-policy.md)에서 설명한 대로 자산 배달 정책을 구성한 후에 로케이터를 만듭니다. 

주문형 스트리밍 로케이터는 점진적으로 다운로드할 수 있는 MP4 파일을 가리키는 URL을 작성하는 데 사용할 수도 있습니다.  

이 문서에서는 자산을 게시하고 부드러운 MPEG DASH 및 HLS 스트리밍 URL을 빌드하기 위해 OnDemand 스트리밍 로케이터를 만드는 방법을 설명합니다. 또한 점진적 다운로드 URL을 작성하는 핫을 보여 줍니다.

[다음](#types) 섹션에서는 REST 호출에 사용되는 값을 가진 열거형 유형을 보여 줍니다.   

> [!NOTE]
> Media Services에서 엔터티에 액세스할 때는 HTTP 요청에서 구체적인 헤더 필드와 값을 설정해야 합니다. 자세한 내용은 [Media Services REST API 개발 설정](media-services-rest-how-to-use.md)을 참조하세요.
> 

## <a name="connect-to-media-services"></a>Media Services에 연결

AMS API에 연결하는 방법에 대한 자세한 내용은 [Azure AD 인증을 사용하여 Azure Media Services API 액세스](media-services-use-aad-auth-to-access-ams-api.md)를 참조하세요. 

>[!NOTE]
>https://media.windows.net에 연결하면 다른 Media Services URI를 지정하는 301 리디렉션을 받게 됩니다. 사용자는 새 URI에 대한 후속 호출을 해야 합니다.

## <a name="create-an-ondemand-streaming-locator"></a>주문형 스트리밍 로케이터 만들기
OnDemand 스트리밍 로케이터를 만들고 URL을 가져오려면 다음을 수행해야 합니다.

1. 콘텐츠가 암호화되어 있는 경우 액세스 정책을 정의합니다.
2. 주문형 스트리밍 로케이터를 만듭니다.
3. 스트리밍하려는 경우 자산의 스트리밍 매니페스트 파일(.ism)을 가져옵니다. 
   
   점진적으로 다운로드하려는 경우 자산의 MP4 파일의 이름을 가져옵니다. 
4. 매니페스트 파일 또는 MP4 파일에 URL을 작성합니다. 
5. 쓰기 또는 삭제 권한을 포함하는 AccessPolicy를 사용하여 스트리밍 로케이터를 만들 수 없습니다.

### <a name="create-an-access-policy"></a>액세스 정책 만들기

>[!NOTE]
>다른 AMS 정책(예: 로케이터 정책 또는 ContentKeyAuthorizationPolicy의 경우)은 1,000,000개의 정책으로 제한됩니다. 항상 같은 날짜/액세스 권한을 사용하는 경우(예: 비 업로드 정책처럼 오랫동안 유지되는 로케이터에 대한 정책) 동일한 정책 ID를 사용합니다. 자세한 내용은 [이](media-services-dotnet-manage-entities.md#limit-access-policies) 문서를 참조하세요.

요청:

    POST https://media.windows.net/api/AccessPolicies HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.17
    x-ms-client-request-id: 6bcfd511-a561-448d-a022-a319a89ecffa
    Host: media.windows.net
    Content-Length: 68

    {"Name":"access policy","DurationInMinutes":43200.0,"Permissions":1}

응답:

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 311
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Location: https:/media.windows.net/api/AccessPolicies('nb%3Apid%3AUUID%3A69c80d98-7830-407f-a9af-e25f4b0d3e5f')
    Server: Microsoft-IIS/8.5
    request-id: a877528a-bdb4-4414-9862-273f8e64f882
    x-ms-request-id: a877528a-bdb4-4414-9862-273f8e64f882
    x-ms-client-request-id: 6bcfd511-a561-448d-a022-a319a89ecffa
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Wed, 18 Feb 2015 06:52:09 GMT

    {"odata.metadata":"https://media.windows.net/api/$metadata#AccessPolicies/@Element","Id":"nb:pid:UUID:69c80d98-7830-407f-a9af-e25f4b0d3e5f","Created":"2015-02-18T06:52:09.8862191Z","LastModified":"2015-02-18T06:52:09.8862191Z","Name":"access policy","DurationInMinutes":43200.0,"Permissions":1}

### <a name="create-an-ondemand-streaming-locator"></a>주문형 스트리밍 로케이터 만들기
지정된 자산 및 자산 정책에 대한 로케이터를 만듭니다.

요청:

    POST https://media.windows.net/api/Locators HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.17
    x-ms-client-request-id: ac159492-9a0c-40c3-aacc-551b1b4c5f62
    Host: media.windows.net
    Content-Length: 181

    {"AccessPolicyId":"nb:pid:UUID:1480030d-c481-430a-9687-535c6a5cb272","AssetId":"nb:cid:UUID:cc1e445d-1500-80bd-538e-f1e4b71b465e","StartTime":"2015-02-18T06:34:47.267872Z","Type":2}

응답:

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 637
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Location: https://media.windows.net/api/Locators('nb%3Alid%3AUUID%3Abe245661-2bbd-4fc6-b14f-9cf9a1492e5e')
    Server: Microsoft-IIS/8.5
    request-id: 5bd5864a-0afd-44c0-a67a-4044a2c9043b
    x-ms-request-id: 5bd5864a-0afd-44c0-a67a-4044a2c9043b
    x-ms-client-request-id: ac159492-9a0c-40c3-aacc-551b1b4c5f62
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Wed, 18 Feb 2015 06:58:37 GMT

    {"odata.metadata":"https://media.windows.net/api/$metadata#Locators/@Element","Id":"nb:lid:UUID:be245661-2bbd-4fc6-b14f-9cf9a1492e5e","ExpirationDateTime":"2015-03-20T06:34:47.267872+00:00","Type":2,"Path":"https://amstest1.streaming.mediaservices.windows.net/be245661-2bbd-4fc6-b14f-9cf9a1492e5e/","BaseUri":"https://amstest1.streaming.mediaservices.windows.net","ContentAccessComponent":"be245661-2bbd-4fc6-b14f-9cf9a1492e5e","AccessPolicyId":"nb:pid:UUID:1480030d-c481-430a-9687-535c6a5cb272","AssetId":"nb:cid:UUID:cc1e445d-1500-80bd-538e-f1e4b71b465e","StartTime":"2015-02-18T06:34:47.267872+00:00","Name":null}

### <a name="build-streaming-urls"></a>스트리밍 URL 작성
로케이터를 만든 후 반환된 **경로** 값을 사용하여 부드러운 HLS 및 MPEG DASH URL을 작성합니다. 

부드러운 스트리밍: **경로** + 매니페스트 파일 이름 + "/manifest"

다음과 같습니다.

    https://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny.ism/manifest

HLS: **경로** + 매니페스트 파일 이름 + "/ manifest(format=m3u8-aapl)"

다음과 같습니다.

    https://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny.ism/manifest(format=m3u8-aapl)


DASH: **경로** + 매니페스트 파일 이름 + "/ manifest(format=mpd-time-csf)"

다음과 같습니다.

    https://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny.ism/manifest(format=mpd-time-csf)


### <a name="build-progressive-download-urls"></a>점진적 다운로드 URL 작성
로케이터를 만든 후 반환된 **경로** 값을 사용하여 점진적 다운로드 URL을 작성합니다.   

URL: **경로** + 자산 파일 mp4 이름

다음과 같습니다.

    https://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4

## <a id="types"></a>열거형 형식
    [Flags]
    public enum AccessPermissions
    {
        None = 0,
        Read = 1,
        Write = 2,
        Delete = 4,
        List = 8,
    }

    public enum LocatorType
    {
        None = 0,
        Sas = 1,
        OnDemandOrigin = 2,
    }

## <a name="media-services-learning-paths"></a>Media Services 학습 경로
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>피드백 제공
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>참고 항목
[Media Services Operations REST API 개요](media-services-rest-how-to-use.md)

[자산 배달 정책 구성](media-services-rest-configure-asset-delivery-policy.md)

