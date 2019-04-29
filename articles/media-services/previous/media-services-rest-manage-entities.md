---
title: REST를 사용하여 Media Services 엔터티 관리 | Microsoft Docs
description: REST API를 사용하여 Media Services 엔터티를 관리하는 방법을 알아봅니다.
author: juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: 95262a32-0f2a-4286-b9e2-1a1ca6399b5b
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: ffbf30f2bfdf0a175513a8d2b9182b35c39f6aae
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60761712"
---
# <a name="managing-media-services-entities-with-rest"></a>REST를 사용하여 Media Services 엔터티 관리  

> [!div class="op_single_selector"]
> * [REST (영문)](media-services-rest-manage-entities.md)
> * [.NET](media-services-dotnet-manage-entities.md)
> 
> 

Microsoft Azure Media Services는 OData v3에 빌드된 REST 기반 서비스입니다. 다른 OData 서비스에서와 거의 같은 방법으로 엔터티를 추가, 쿼리, 업데이트 및 삭제할 수 있습니다. 예외는 해당하는 경우 호출됩니다. OData에 대한 자세한 내용은 [개방형 데이터 프로토콜 설명서](https://www.odata.org/documentation/)를 참조하세요.

이 항목에서는 REST를 사용하여 Azure Media Services 엔터티를 관리하는 방법을 보여 줍니다.

>[!NOTE]
> 2017년 4월 1일부터 레코드의 총 수가 최고 할당량 미만인 경우에도 사용자 계정에 있는 90일이 지난 작업 레코드는 연결된 태스크 레코드와 함께 자동으로 삭제됩니다. 예를 들어, 2017년 4월 1일에는 계정에 있는 2016년 12월 31일 이전의 모든 작업 레코드가 자동으로 삭제됩니다. 작업/태스크 정보를 보관해야 하는 경우에는 이 항목에 설명된 코드를 사용할 수 있습니다.

## <a name="considerations"></a>고려 사항  

Media Services에서 엔터티에 액세스할 때는 HTTP 요청에서 구체적인 헤더 필드와 값을 설정해야 합니다. 자세한 내용은 [Media Services REST API 개발 설정](media-services-rest-how-to-use.md)을 참조하세요.

## <a name="connect-to-media-services"></a>Media Services에 연결

AMS API에 연결하는 방법에 대한 자세한 내용은 [Azure AD 인증을 사용하여 Azure Media Services API 액세스](media-services-use-aad-auth-to-access-ams-api.md)를 참조하세요. 

## <a name="adding-entities"></a>엔터티 추가
Media Services의 모든 엔터티는 POST HTTP 요청을 통해 Assets와 같은 엔터티 집합에 추가됩니다.

다음 예제에서는 AccessPolicy를 만드는 방법을 보여 줍니다.

    POST https://media.windows.net/API/AccessPolicies HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.17
    Authorization: Bearer <ENCODED JWT TOKEN> 
    Host: media.windows.net
    Content-Length: 74
    Expect: 100-continue

    {"Name": "DownloadPolicy", "DurationInMinutes" : "300", "Permissions" : 1}

## <a name="querying-entities"></a>엔터티 쿼리
엔터티 쿼리 및 나열은 간단하고 GET HTTP 요청과 선택적 OData 작업만 관련됩니다.
다음 예제에서는 모든 MediaProcessor 엔터티 목록을 검색합니다.

    GET https://media.windows.net/API/MediaProcessors HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.17
    Authorization: Bearer <ENCODED JWT TOKEN> 
    Host: media.windows.net

다음 예제와 같이 특정 엔터티 또는 특정 엔터티와 연결된 모든 엔터티 집합을 검색할 수도 있습니다.

    GET https://media.windows.net/API/JobTemplates('nb:jtid:UUID:e81192f5-576f-b247-b781-70a790c20e7c') HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.17
    Authorization: Bearer <ENCODED JWT TOKEN> 
    Host: media.windows.net

    GET https://media.windows.net/API/JobTemplates('nb:jtid:UUID:e81192f5-576f-b247-b781-70a790c20e7c')/TaskTemplates HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.17
    Authorization: Bearer <ENCODED JWT TOKEN> 
    Host: media.windows.net

다음 예제에서는 모든 작업의 State 속성만 반환합니다.

    GET https://media.windows.net/API/Jobs?$select=State HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.17
    Authorization: Bearer <ENCODED JWT TOKEN> 
    Host: media.windows.net

다음 예제에서는 이름이 "SampleTemplate"인 모든 JobTemplate를 반환합니다.

    GET https://media.windows.net/API/JobTemplates?$filter=startswith(Name,%20'SampleTemplate') HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.17
    Authorization: Bearer <ENCODED JWT TOKEN> 
    Host: media.windows.net

> [!NOTE]
> $expand 작업은 LINQ 고려 사항(WCF Data Services)에 설명된 지원되지 않는 LINQ 메서드 및 Media Services에서 지원되지 않습니다.
> 
> 

## <a name="enumerating-through-large-collections-of-entities"></a>대용량 엔터티 컬렉션 열거
엔터티를 쿼리할 때 한 번에 반환되는 엔터티 수는 최대 1000개입니다. 공용 REST v2에서는 쿼리 결과를 1000개로 제한하기 때문입니다. **skip** 및 **top**을 사용하여 대용량 엔터티 컬렉션을 열거합니다. 

다음 예제에서는 **skip** 및 **top**을 사용하여 처음 2000개의 작업을 건너뛰고 다음 1000개의 작업을 가져오는 방법을 보여 줍니다.  

    GET https://media.windows.net/api/Jobs()?$skip=2000&$top=1000 HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.17
    Authorization: Bearer <ENCODED JWT TOKEN>
    Host: media.windows.net

## <a name="updating-entities"></a>엔터티 업데이트
엔터티 형식 및 엔터티 상태에 따라 PATCH, PUT 또는 MERGE HTTP 요청을 통해 해당 엔터티의 속성을 업데이트할 수 있습니다. 이 작업에 대한 자세한 내용은 [PATCH/PUT/MERGE](https://msdn.microsoft.com/library/dd541276.aspx)를 참조하세요.

다음 코드 예제에서는 Asset 엔터티의 Name 속성을 업데이트하는 방법을 보여 줍니다.

    MERGE https://media.windows.net/API/Assets('nb:cid:UUID:80782407-3f87-4e60-a43e-5e4454232f60') HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.17
    Authorization: Bearer <ENCODED JWT TOKEN>
    Host: media.windows.net
    Content-Length: 21
    Expect: 100-continue

    {"Name" : "NewName" }

## <a name="deleting-entities"></a>엔터티 삭제
DELETE HTTP 요청을 사용하여 Media Services에서 엔터티를 삭제할 수 있습니다. 엔터티에 따라 엔터티 삭제 순서가 중요할 수 있습니다. 예를 들어 자산과 같은 엔터티는 자산을 삭제하기 전에 해당 특정 자산을 참조하는 모든 로케이터를 해지(또는 삭제)해야 합니다.

File Storage에 대한 가용성 섹션을 확인하세요.

    DELETE https://media.windows.net/API/Locators('nb:lid:UUID:76dcc8e8-4230-463d-97b0-ce25c41b5c8d') HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.17
    Authorization: Bearer <ENCODED JWT TOKEN>
    Host: media.windows.net
    Content-Length: 0

## <a name="media-services-learning-paths"></a>Media Services 학습 경로
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>피드백 제공
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

