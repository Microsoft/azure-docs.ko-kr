---
title: REST를 사용하여 미디어 프로세서 인스턴스를 가져오는 방법 | Microsoft Docs
description: Azure Media Services용 미디어 콘텐츠를 인코딩하거나 형식을 변환하거나 암호화하거나 암호 해독하기 위한 미디어 프로세서 구성 요소를 만드는 방법에 대해 알아봅니다.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: f9ff1997-0da6-4528-aaed-792837e5be41
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: d342cff6d322195ee88a74215f814be7d702aa5e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60761979"
---
# <a name="how-to-get-a-media-processor-instance"></a>미디어 프로세서 인스턴스를 가져오는 방법
> [!div class="op_single_selector"]
> * [.NET](media-services-get-media-processor.md)
> * [REST (영문)](media-services-rest-get-media-processor.md)
> 
> 

## <a name="overview"></a>개요
미디어 프로세서는 인코딩, 형식 변환, 콘텐츠, 암호화 또는 암호 해독 미디어와 같은 특정 비디오 또는 오디오 처리 작업을 처리하는 구성 요소입니다. Media Services에 제출된 모든 작업에는 비디오 또는 오디오 콘텐츠를 인코딩, 암호화 또는 변환하는 미디어 프로세서가 필요합니다. 

## <a name="azure-media-processors"></a>Azure 미디어 프로세서 

미디어 프로세스 목록은 다음 항목에서 제공됩니다.

* [Encoding 미디어 프로세서](scenarios-and-availability.md#encoding-media-processors)
* [분석 미디어 프로세서](scenarios-and-availability.md#analytics-media-processors)

>[!NOTE]
>Media Services에서 엔터티에 액세스할 때는 HTTP 요청에서 구체적인 헤더 필드와 값을 설정해야 합니다. 자세한 내용은 [Media Services REST API 개발 설정](media-services-rest-how-to-use.md)을 참조하세요.

## <a name="connect-to-media-services"></a>Media Services에 연결

AMS API에 연결하는 방법에 대한 자세한 내용은 [Azure AD 인증을 사용하여 Azure Media Services API 액세스](media-services-use-aad-auth-to-access-ams-api.md)를 참조하세요. 


## <a name="get-a-media-processor"></a>미디어 프로세서 가져오기

다음 REST 호출에서는 이름으로 미디어 프로세서 인스턴스를 가져오는 방법을 보여 줍니다(이 경우 **미디어 인코더 표준**). 

요청:

    GET https://media.windows.net/api/MediaProcessors()?$filter=Name%20eq%20'Media%20Encoder%20Standard' HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer <token>
    x-ms-version: 2.17
    Host: media.windows.net

응답:

    . . .

    {  
       "odata.metadata":"https://media.windows.net/api/$metadata#MediaProcessors",
       "value":[  
          {  
             "Id":"nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",
             "Description":"Media Encoder Standard",
             "Name":"Media Encoder Standard",
             "Sku":"",
             "Vendor":"Microsoft",
             "Version":"1.1"
          }
       ]
    }


## <a name="media-services-learning-paths"></a>Media Services 학습 경로
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>피드백 제공
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>다음 단계
미디어 프로세서 인스턴스를 가져오는 방법을 알아보았으므로 이제 Media Encoder Standard를 사용하여 자산을 인코딩하는 방법을 보여주는 [자산을 인코딩하는 방법](media-services-rest-get-started.md) 문서로 이동합니다.

