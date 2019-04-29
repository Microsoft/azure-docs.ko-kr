---
title: REST API를 사용하여 작업 진행률을 확인하는 방법 | Microsoft 문서
description: 작업 진행률을 추적하는 방법에 대해 알아봅니다.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: a1a1f956-c035-448a-af9c-5ac15fcce9dd
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: a7b61ba05aea817d65e7cac991ad460d708a5ea7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60873413"
---
# <a name="how-to-check-job-progress"></a>방법: 작업 진행 상태 확인
> [!div class="op_single_selector"]
> * [포털](media-services-portal-check-job-progress.md)
> * [.NET](media-services-check-job-progress.md)
> * [REST (영문)](media-services-rest-check-job-progress.md)
> 
> 

작업을 실행할 때 작업 진행 상태를 추적하는 방법이 종종 필요합니다. 작업의 상태 속성을 사용하여 작업 상태를 찾을 수 있습니다. 상태 속성에 대한 자세한 내용은 [Job 엔터티 속성](https://docs.microsoft.com/rest/api/media/operations/job#job_entity_properties)을 참조하세요.

## <a name="connect-to-media-services"></a>Media Services에 연결

AMS API에 연결하는 방법에 대한 자세한 내용은 [Azure AD 인증을 사용하여 Azure Media Services API 액세스](media-services-use-aad-auth-to-access-ams-api.md)를 참조하세요. 

## <a name="check-job-progress"></a>작업 진행 상태 확인

요청:

    GET https://media.windows.net/api/Jobs()?$filter=Id%20eq%20'nb%3Ajid%3AUUID%3Af3c43f94-327f-2347-90bb-3bf79f8559f1'&$top=1 HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.17
    Host: media.windows.net

응답:

    HTTP/1.1 200 OK
    Cache-Control: no-cache
    Content-Length: 450
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Server: Microsoft-IIS/8.5
    x-ms-client-request-id: d9b83c57-e26c-4d10-a20b-2be634c4b6a8
    request-id: 91d2be35-20ed-4e1c-a147-e82cd000c193
    x-ms-request-id: 91d2be35-20ed-4e1c-a147-e82cd000c193
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    Strict-Transport-Security: max-age=31536000; includeSubDomains

    {"odata.metadata":"https://media.windows.net/api/$metadata#Jobs","value":[{"Id":"nb:jid:UUID:f3c43f94-327f-2347-90bb-3bf79f8559f1","Name":"Encoding BigBuckBunny into to H264 Adaptive Bitrate MP4 Set 720p","Created":"2015-02-11T01:46:08.897","LastModified":"2015-02-11T01:46:08.897","EndTime":null,"Priority":0,"RunningDuration":0.0,"StartTime":"2015-02-11T01:46:16.58","State":2,"TemplateId":null,"JobNotificationSubscriptions":[]}]} 


## <a name="media-services-learning-paths"></a>Media Services 학습 경로
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>피드백 제공
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>참고 항목

[Media Services Operations REST API 개요](media-services-rest-how-to-use.md)
