---
title: Azure Media Services REST API로 필터 생성 | Microsoft Docs
description: 이 항목에서는 클라이언트가 스트림의 특정 섹션을 스트리밍하는 데 사용할 수 있는 필터를 생성하는 방법을 설명합니다. 이 선택적 스트리밍은 Media Services가 동적 매니페스트를 생성하여 이루어집니다.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: f7d23daf-7cd2-49c7-a195-ab902912ab3c
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako;cenkdin
ms.openlocfilehash: 5b023a152cf93ec6ff688674e991ad55db215965
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60767815"
---
# <a name="creating-filters-with-azure-media-services-rest-api"></a>Azure Media Services REST API로 필터 생성 
> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-dynamic-manifest.md)
> * [REST (영문)](media-services-rest-dynamic-manifest.md)
> 
> 

Media Services 2.17 버전부터 자산에 대한 필터를 정의할 수 있습니다. 이 필터는 고객이 전체 비디오를 재생하는 대신 비디오의 한 섹션만 재생하거나 자산과 연결된 모든 변환 대신 고객의 디바이스가 처리할 수 있는 오디오 및 비디오 변환의 하위 집합만 지정하는 작업 등을 선택할 수 있도록 하는 서버 측 규칙입니다. 지정한 필터에 따라 비디오를 스트림하는 고객의 요청에 따라 생성된 **동적 매니페스트**를 통해 자산의 필터링이 보관됩니다.

필터 및 동적 매니페스트에 대한 더 자세한 내용은 [동적 매니페스트 개요](media-services-dynamic-manifest-overview.md)를 참조하십시오.

이 항목에서는 REST API를 사용하여 필터를 생성, 업데이트 및 삭제하는 방법을 설명합니다. 

## <a name="types-used-to-create-filters"></a>필터 생성에 사용되는 형식
필터를 생성할 때는 다음 형식이 사용됩니다.  

* [Filter](https://docs.microsoft.com/rest/api/media/operations/filter)
* [AssetFilter](https://docs.microsoft.com/rest/api/media/operations/assetfilter)
* [PresentationTimeRange](https://docs.microsoft.com/rest/api/media/operations/presentationtimerange)
* [FilterTrackSelect 및 FilterTrackPropertyCondition](https://docs.microsoft.com/rest/api/media/operations/filtertrackselect)

> [!NOTE]
> 
> Media Services에서 엔터티에 액세스할 때는 HTTP 요청에서 구체적인 헤더 필드와 값을 설정해야 합니다. 자세한 내용은 [Media Services REST API 개발 설정](media-services-rest-how-to-use.md)을 참조하세요.

## <a name="connect-to-media-services"></a>Media Services에 연결

AMS API에 연결하는 방법에 대한 자세한 내용은 [Azure AD 인증을 사용하여 Azure Media Services API 액세스](media-services-use-aad-auth-to-access-ams-api.md)를 참조하세요. 

## <a name="create-filters"></a>필터 생성
### <a name="create-global-filters"></a>전역 Filter 생성
전역 Filter를 만들려면 다음 HTTP 요청을 사용합니다.  

#### <a name="http-request"></a>HTTP 요청
요청 헤더

    POST https://media.windows.net/API/Filters HTTP/1.1 
    DataServiceVersion:3.0 
    MaxDataServiceVersion: 3.0 
    Content-Type: application/json 
    Accept: application/json 
    Accept-Charset: UTF-8 
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.17 
    x-ms-client-request-id: 00000000-0000-0000-0000-000000000000 
    Host:media.windows.net 

요청 본문 

    {  
       "Name":"GlobalFilter",
       "PresentationTimeRange":{  
          "StartTimestamp":"0",
          "EndTimestamp":"9223372036854775807",
          "PresentationWindowDuration":"12000000000",
          "LiveBackoffDuration":"0",
          "Timescale":"10000000"
       },
       "Tracks":[  
          {  
             "PropertyConditions":
                  [  
                {  
                   "Property":"Type",
                   "Value":"audio",
                   "Operator":"Equal"
                },
                {  
                   "Property":"Bitrate",
                   "Value":"0-2147483647",
                   "Operator":"Equal"
                }
             ]
          }
       ]
    }




#### <a name="http-response"></a>HTTP 응답
    HTTP/1.1 201 Created 

### <a name="create-local-assetfilters"></a>로컬 AssetFilter 생성
로컬 AssetFilter를 만들려면 다음 HTTP 요청을 사용합니다.  

#### <a name="http-request"></a>HTTP 요청
요청 헤더

    POST https://media.windows.net/API/AssetFilters HTTP/1.1 
    DataServiceVersion: 3.0 
    MaxDataServiceVersion: 3.0 
    Content-Type: application/json 
    Accept: application/json 
    Accept-Charset: UTF-8 
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.17 
    x-ms-client-request-id: 00000000-0000-0000-0000-000000000000 
    Host: media.windows.net  

요청 본문 

    {   
       "Name":"AssetFilter", 
       "ParentAssetId":"nb:cid:UUID:536e555d-1500-80c3-92dc-f1e4fdc6c592", 
       "PresentationTimeRange":{   
          "StartTimestamp":"0", 
          "EndTimestamp":"9223372036854775807", 
          "PresentationWindowDuration":"12000000000", 
          "LiveBackoffDuration":"0", 
          "Timescale":"10000000" 
       }, 
       "Tracks":[   
          {   
             "PropertyConditions": 
                  [   
                {   
                   "Property":"Type", 
                   "Value":"audio", 
                   "Operator":"Equal" 
                }, 
                {   
                   "Property":"Bitrate", 
                   "Value":"0-2147483647", 
                   "Operator":"Equal" 
                } 
             ] 
          } 
       ] 
    } 

#### <a name="http-response"></a>HTTP 응답
    HTTP/1.1 201 Created 
    . . . 

## <a name="list-filters"></a>필터 나열
### <a name="get-all-global-filters-in-the-ams-account"></a>AMS 계정의 모든 전역 **Filter**가져오기
필터를 나열하려면 다음 HTTP 요청을 사용합니다. 

#### <a name="http-request"></a>HTTP 요청
    GET https://media.windows.net/API/Filters HTTP/1.1 
    DataServiceVersion:3.0 
    MaxDataServiceVersion: 3.0 
    Accept: application/json 
    Accept-Charset: UTF-8 
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.17 
    Host: media.windows.net 

### <a name="get-assetfilters-associated-with-an-asset"></a>자산에 연결된 **AssetFilter**가져오기
#### <a name="http-request"></a>HTTP 요청
    GET https://media.windows.net/API/Assets('nb%3Acid%3AUUID%3A536e555d-1500-80c3-92dc-f1e4fdc6c592')/AssetFilters HTTP/1.1 
    DataServiceVersion: 3.0 
    MaxDataServiceVersion: 3.0 
    Accept: application/json 
    Accept-Charset: UTF-8 
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.17 
    x-ms-client-request-id: 00000000-0000-0000-0000-000000000000 
    Host: media.windows.net 

### <a name="get-an-assetfilter-based-on-its-id"></a>해당 ID에 따라 **AssetFilter** 가져오기
#### <a name="http-request"></a>HTTP 요청
    GET https://media.windows.net/API/AssetFilters('nb%3Acid%3AUUID%3A536e555d-1500-80c3-92dc-f1e4fdc6c592__%23%23%23__TestFilter') HTTP/1.1 
    DataServiceVersion: 3.0 
    MaxDataServiceVersion: 3.0 
    Accept: application/json 
    Accept-Charset: UTF-8 
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.17 
    x-ms-client-request-id: 00000000


## <a name="update-filters"></a>필터 업데이트
PATCH, PUT 또는 MERGE를 사용하여 새 속성 값으로 필터를 업데이트합니다.  이 작업에 대한 자세한 내용은 [PATCH, PUT, MERGE](https://msdn.microsoft.com/library/dd541276.aspx)를 참조하십시오.

필터를 업데이트하는 경우 스트리밍 엔드포인트가 규칙을 새로 고치는 데 최대 2분이 소요될 수 있습니다. 콘텐츠가 이 필터로 처리된 경우(및 프록시와 CDN 캐시에서 캐시된 경우) 이 필터를 업데이트하면 플레이어 오류가 발생할 수 있습니다. 필터를 업데이트한 후에 캐시를 지웁니다. 이 옵션을 사용할 수 없는 경우에 서로 다른 필터를 사용 하는 것이 좋습니다.  

### <a name="update-global-filters"></a>전역 Filter 업데이트
전역 필터를 업데이트하려면 다음 HTTP 요청을 사용합니다. 

#### <a name="http-request"></a>HTTP 요청
헤더 요청: 

    MERGE https://media.windows.net/API/Filters('filterName') HTTP/1.1 
    DataServiceVersion:3.0 
    MaxDataServiceVersion: 3.0 
    Content-Type: application/json 
    Accept: application/json 
    Accept-Charset: UTF-8 
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.17 
    x-ms-client-request-id: 00000000-0000-0000-0000-000000000000 
    Host: media.windows.net 
    Content-Length: 384

본문 요청: 

    { 
       "Tracks":[   
          {   
             "PropertyConditions": 
             [   
                {   
                   "Property":"Type", 
                   "Value":"audio", 
                   "Operator":"Equal" 
                }, 
                {   
                   "Property":"Bitrate", 
                   "Value":"0-2147483647", 
                   "Operator":"Equal" 
                } 
             ] 
          } 
       ] 
    } 

### <a name="update-local-assetfilters"></a>로컬 AssetFilter 업데이트
로컬 필터를 업데이트하려면 다음 HTTP 요청을 사용합니다. 

#### <a name="http-request"></a>HTTP 요청
헤더 요청: 

    MERGE https://media.windows.net/API/AssetFilters('nb%3Acid%3AUUID%3A536e555d-1500-80c3-92dc-f1e4fdc6c592__%23%23%23__TestFilter')  HTTP/1.1 
    DataServiceVersion: 3.0 
    MaxDataServiceVersion: 3.0 
    Content-Type: application/json 
    Accept: application/json 
    Accept-Charset: UTF-8 
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.17 
    x-ms-client-request-id: 00000000-0000-0000-0000-000000000000 
    Host: media.windows.net 

본문 요청: 

    { 
       "Tracks":[   
          {   
             "PropertyConditions": 
             [   
                {   
                   "Property":"Type", 
                   "Value":"audio", 
                   "Operator":"Equal" 
                }, 
                {   
                   "Property":"Bitrate", 
                   "Value":"0-2147483647", 
                   "Operator":"Equal" 
                } 
             ] 
          } 
       ] 
    } 


## <a name="delete-filters"></a>필터 삭제
### <a name="delete-global-filters"></a>전역 Filter 삭제
전역 Filter를 삭제하려면 다음 HTTP 요청을 사용합니다.

#### <a name="http-request"></a>HTTP 요청
    DELETE https://media.windows.net/api/Filters('GlobalFilter') HTTP/1.1 
    DataServiceVersion:3.0 
    MaxDataServiceVersion: 3.0 
    Accept: application/json 
    Accept-Charset: UTF-8 
    Authorization: Bearer <ENCODED JWT TOKEN>  
    x-ms-version: 2.17 
    Host: media.windows.net 


### <a name="delete-local-assetfilters"></a>로컬 AssetFilter 삭제
로컬 AssetFilter를 삭제하려면 다음 HTTP 요청을 사용합니다.

#### <a name="http-request"></a>HTTP 요청
    DELETE https://media.windows.net/API/AssetFilters('nb%3Acid%3AUUID%3A536e555d-1500-80c3-92dc-f1e4fdc6c592__%23%23%23__LocalFilter') HTTP/1.1 
    DataServiceVersion: 3.0 
    MaxDataServiceVersion: 3.0 
    Accept: application/json 
    Accept-Charset: UTF-8 
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.17 
    Host: media.windows.net 

## <a name="build-streaming-urls-that-use-filters"></a>필터를 사용하는 스트리밍 URL 작성
자산을 게시하고 제공하는 방법에 대한 자세한 내용은 [고객에 콘텐츠 배달 개요](media-services-deliver-content-overview.md)를 참조하십시오.

다음 예제에서는 스트리밍 URL에 필터를 추가하는 방법을 보여줍니다.

**MPEG DASH** 

    http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=mpd-time-csf, filter=MyFilter)

**Apple HLS(HTTP 라이브 스트리밍) V4**

    http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl, filter=MyFilter)

**Apple HLS(HTTP 라이브 스트리밍) V3**

    http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl-v3, filter=MyFilter)

**부드러운 스트리밍**

    http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(filter=MyFilter)

    
## <a name="media-services-learning-paths"></a>Media Services 학습 경로
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>피드백 제공
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>관련 항목
[동적 매니페스트 개요](media-services-dynamic-manifest-overview.md)

