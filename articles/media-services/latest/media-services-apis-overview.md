---
title: V3 Api를 사용 하 여 개발
titleSuffix: Azure Media Services
description: Media Services v3로 개발할 때 엔터티 및 Api에 적용 되는 규칙에 대해 알아봅니다.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 10/21/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 4a3b699c90e1fefb834f8ddfe3a23fc2a97354ec
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74186139"
---
# <a name="develop-with-media-services-v3-apis"></a>Media Services v3 Api를 사용 하 여 개발

개발자인 경우 Media Services [REST API](https://aka.ms/ams-v3-rest-ref) 또는 REST API와 상호 작용할 수 있도록 하는 클라이언트 라이브러리를 사용하여 사용자 지정 미디어 워크플로를 손쉽게 만들고, 관리하고 유지할 수 있습니다. [Media Services v3](https://aka.ms/ams-v3-rest-sdk) API는 OpenAPI 사양(이전 명칭 Swagger)에 기반을 두고 있습니다.

이 문서에서는 Media Services v3로 개발할 때 엔터티 및 Api에 적용 되는 규칙을 설명 합니다.

## <a name="accessing-the-azure-media-services-api"></a>Azure Media Services API 액세스

Media Services 리소스 및 Media Services API에 액세스할 수 있는 권한을 부여하려면 먼저 인증을 거쳐야 합니다. Media Services [은 Azure Active Directory (AZURE AD) 기반](../../active-directory/fundamentals/active-directory-whatis.md) 인증을 지원 합니다. 두 가지 일반적인 인증 옵션은 다음과 같습니다.
 
* **서비스 주체 인증**: 서비스를 인증 하는 데 사용 됩니다 (예: 웹 앱, 함수 앱, 논리 앱, API 및 마이크로 서비스). 이 인증 방법을 일반적으로 사용하는 애플리케이션은 디먼 서비스, 중간 계층 서비스 또는 예약된 작업을 실행하는 앱입니다. 예를 들어, 웹 앱의 경우 서비스 주체와 Media Services에 연결 하는 중간 계층이 항상 있어야 합니다.
* **사용자 인증**: 앱을 사용 하 여 Media Services 리소스와 상호 작용 하는 사용자를 인증 하는 데 사용 됩니다. 대화형 앱은 먼저 사용자에 게 사용자의 자격 증명을 입력 하 라는 메시지를 표시 해야 합니다. 예제는 권한 있는 사용자가 인코딩 작업 또는 라이브 스트리밍을 모니터링하기 위해 사용한 관리 콘솔 앱입니다.

Media Services API를 사용 하려면 REST API 요청 하는 사용자 또는 앱이 Media Services 계정 리소스에 대 한 액세스 권한을 가지 며 **참가자** 또는 **소유자** 역할을 사용 해야 합니다. **판독기** 역할을 사용 하 여 API에 액세스할 수 있지만 **Get** 또는 **List** 작업만 사용할 수 있습니다. 자세한 내용은 [Media Services 계정에 대 한 역할 기반 액세스 제어](rbac-overview.md)를 참조 하세요.

서비스 주체를 만드는 대신 Azure Resource Manager를 통해 Azure 리소스에 대 한 관리 되는 id를 사용 하 여 Media Services API에 액세스 하는 것이 좋습니다. Azure 리소스의 관리 되는 id에 대 한 자세한 내용은 [azure 리소스에 대 한 관리 되는 Id 란?](../../active-directory/managed-identities-azure-resources/overview.md)을 참조 하세요.

### <a name="azure-ad-service-principal"></a>Azure AD 서비스 주체

Azure AD 앱 및 서비스 주체를 만드는 경우 앱은 자체 테넌트에 있어야 합니다. 앱을 만든 후 앱 **참가자** 또는 **소유자** 역할에 Media Services 계정에 대 한 액세스 권한을 부여 합니다.

Azure AD 앱을 만들 수 있는 권한이 있는지 확실 하지 않은 경우 [필요한 권한](../../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)을 참조 하세요.

다음 그림에서 숫자는 요청 흐름을 시간 순서로 나타냅니다.

![웹 API에서 AAD를 사용 하 여 중간 계층 앱 인증](./media/use-aad-auth-to-access-ams-api/media-services-principal-service-aad-app1.png)

1. 중간 계층 앱은 다음 매개 변수를 포함 하는 Azure AD 액세스 토큰을 요청 합니다.  

   * Azure AD 테넌트 엔드포인트.
   * Media Services 리소스 URI.
   * REST Media Services의 리소스 URI.
   * Azure AD 앱 값: 클라이언트 ID 및 클라이언트 암호입니다.

   필요한 값을 모두 가져오려면 [Azure CLI를 사용 하 여 AZURE MEDIA SERVICES API 액세스](access-api-cli-how-to.md)를 참조 하세요.

2. Azure AD 액세스 토큰이 중간 계층으로 전송됩니다.
4. 중간 계층은 Azure AD 토큰과 함께 Azure Media REST API로 요청을 보냅니다.
5. 중간 계층은 Media Services에서 데이터를 다시 가져옵니다.

### <a name="samples"></a>샘플

Azure AD 서비스 주체를 사용 하 여 연결 하는 방법을 보여 주는 다음 샘플을 참조 하세요.

* [REST를 사용 하 여 연결](media-rest-apis-with-postman.md)  
* [Java로 연결](configure-connect-java-howto.md)
* [.NET으로 연결](configure-connect-dotnet-howto.md)
* [Node.js로 연결](configure-connect-nodejs-howto.md)
* [Python으로 연결](configure-connect-python-howto.md)

## <a name="naming-conventions"></a>명명 규칙

Azure Media Services v3 리소스 이름(예: 자산, 작업, 변환)은 Azure Resource Manager 명명 제약 조건에 따라 달라집니다. Azure Resource Manager에 따라 리소스 이름은 항상 고유합니다. 따라서 리소스 이름에 대해 고유 식별자 문자열(예: GUID)을 사용할 수 있습니다.

Media Services 리소스 이름에는 ' < ', ' > ', '% ', ' & ', ': ', '&#92;', '? ', '/', ' * ', ' + ', '. ', 작은따옴표 문자 또는 모든 제어 문자를 포함할 수 없습니다. 다른 문자를 모두 허용합니다. 리소스 이름의 최대 길이는 260자입니다.

Azure Resource Manager 명명에 대 한 자세한 내용은 [명명 요구 사항](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/resource-api-reference.md#arguments-for-crud-on-resource) 및 [명명 규칙](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging)을 참조 하세요.

### <a name="names-of-filesblobs-within-an-asset"></a>자산 내의 파일/blob 이름

자산 내의 파일/b a s 이름은 [blob 이름 요구 사항](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata) 및 [NTFS 이름 요구 사항을](https://docs.microsoft.com/windows/win32/fileio/naming-a-file)따라야 합니다. 이러한 요구 사항에 대 한 이유는 파일을 blob 저장소에서 로컬 NTFS 디스크로 복사 하 여 처리할 수 있다는 것입니다.

## <a name="long-running-operations"></a>장기 실행 작업

Azure Media Services [swagger 파일](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01/streamingservice.json) 에 `x-ms-long-running-operation`로 표시 된 작업은 장기 실행 작업입니다. 

비동기 Azure 작업을 추적 하는 방법에 대 한 자세한 내용은 [비동기 작업](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations#monitor-status-of-operation)을 참조 하세요.

Media Services에는 다음과 같은 장기 실행 작업이 있습니다.

* [라이브 이벤트 만들기](https://docs.microsoft.com/rest/api/media/liveevents/create)
* [라이브 이벤트 업데이트](https://docs.microsoft.com/rest/api/media/liveevents/update)
* [라이브 이벤트 삭제](https://docs.microsoft.com/rest/api/media/liveevents/delete)
* [라이브 이벤트 시작](https://docs.microsoft.com/rest/api/media/liveevents/start)
* [라이브 중지](https://docs.microsoft.com/rest/api/media/liveevents/stop)

  이벤트를 중지할 때 연결 된 모든 라이브 출력을 삭제 하려면 `removeOutputsOnStop` 매개 변수를 사용 합니다.  
* [라이브 다시 설정](https://docs.microsoft.com/rest/api/media/liveevents/reset)
* [LiveOutput 만들기](https://docs.microsoft.com/rest/api/media/liveevents/create)
* [LiveOutput 삭제](https://docs.microsoft.com/rest/api/media/liveevents/delete)
* [StreamingEndpoint 만들기](https://docs.microsoft.com/rest/api/media/streamingendpoints/create)
* [StreamingEndpoint 업데이트](https://docs.microsoft.com/rest/api/media/streamingendpoints/update)
* [StreamingEndpoint 삭제](https://docs.microsoft.com/rest/api/media/streamingendpoints/delete)
* [StreamingEndpoint 시작](https://docs.microsoft.com/rest/api/media/streamingendpoints/start)
* [StreamingEndpoint 중지](https://docs.microsoft.com/rest/api/media/streamingendpoints/stop)
* [StreamingEndpoint 크기 조정](https://docs.microsoft.com/rest/api/media/streamingendpoints/scale)

긴 작업을 성공적으로 제출 하면 ' 202 수락 됨 '이 수신 되 고 반환 된 작업 ID를 사용 하 여 작업 완료에 대해 폴링해야 합니다.

[비동기 Azure 작업 추적](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations) 문서에서는 응답에서 반환된 값을 통해 비동기 Azure 작업의 상태를 추적하는 방법에 대해 자세히 설명합니다.

지정 된 라이브 이벤트 또는 연결 된 라이브 출력에는 장기 실행 작업이 하나만 지원 됩니다. 시작 된 후에는 동일한 라이브 또는 연결 된 실시간 출력에서 후속 장기 실행 작업을 시작 하기 전에 장기 실행 작업을 완료 해야 합니다. 여러 라이브 출력이 있는 라이브 이벤트의 경우 다른 라이브 출력에서 장기 실행 작업을 트리거하기 전에 단일 라이브 출력에서 장기 실행 작업이 완료 될 때까지 기다려야 합니다. 

## <a name="sdks"></a>SDK

> [!NOTE]
> Azure Media Services v3 Sdk는 스레드로부터 안전 하 게 보호 되지 않을 수 있습니다. 다중 스레드 앱을 개발할 때 클라이언트를 보호 하거나 스레드별로 새로운 AzureMediaServicesClient 개체를 사용 하기 위해 고유한 스레드 동기화 논리를 추가 해야 합니다. 코드에서 클라이언트에 제공하는 선택적 개체(예: .NET의 HttpClient 인스턴스) 때문에 다중 스레드 문제가 도입될 수 있다는 점에도 주의해야 합니다.

|SDK|참조|
|---|---|
|[.NET SDK](https://aka.ms/ams-v3-dotnet-sdk)|[.NET 참조](https://aka.ms/ams-v3-dotnet-ref)|
|[Java SDK](https://aka.ms/ams-v3-java-sdk)|[Java 참조](https://aka.ms/ams-v3-java-ref)|
|[Python SDK](https://aka.ms/ams-v3-python-sdk)|[Python 예제](https://aka.ms/ams-v3-python-ref)|
|[Node.js SDK](https://aka.ms/ams-v3-nodejs-sdk) |[Node.js 예제](/javascript/api/overview/azure/mediaservices/management)| 
|[SDK로 이동](https://aka.ms/ams-v3-go-sdk) |[참조로 이동](https://aka.ms/ams-v3-go-ref)|
|[Ruby SDK](https://aka.ms/ams-v3-ruby-sdk)||

### <a name="see-also"></a>참고자료

- [미디어 서비스 이벤트를 포함하는 EventGrid .NET SDK](https://www.nuget.org/packages/Microsoft.Azure.EventGrid/)
- [Media Services 이벤트의 정의](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/eventgrid/data-plane/Microsoft.Media/stable/2018-01-01/MediaServices.json)

## <a name="azure-media-services-explorer"></a>Azure Media Services 탐색기

[Azure Media Services 탐색기](https://github.com/Azure/Azure-Media-Services-Explorer)(AMSE)는 Media Services에 대해 알아보고자 하는 Windows 고객이 사용할 수 있는 도구입니다. AMSE는 Media Services에서 VOD 및 실시간 콘텐츠의 업로드, 다운로드, 인코딩, 스트리밍을 수행하는 Winforms/C# 애플리케이션입니다. AMSE 도구는 코드를 작성하지 않고 Media Services를 테스트하려는 고객을 위한 도구입니다. AMSE 코드는 Media Services를 사용하여 개발하려는 고객을 위한 리소스로 제공됩니다.

AMSE는 오픈 소스 프로젝트이며, 커뮤니티에서 지원을 제공합니다(문제는 https://github.com/Azure/Azure-Media-Services-Explorer/issues)에 보고할 수 있음). 이 프로젝트에는 [Microsoft Open Source Code of Conduct](https://opensource.microsoft.com/codeofconduct/)(Microsoft 오픈 소스 준수 사항)이 적용됩니다. 자세한 내용은 질문 및 답변 [의 코드](https://opensource.microsoft.com/codeofconduct/faq/) 를 참조 하거나 다른 질문이 나 의견을 opencode@microsoft.com 문의 하세요.

## <a name="filtering-ordering-paging-of-media-services-entities"></a>Media Services 엔터티 필터링, 순서 지정, 페이징

[Azure Media Services 엔터티의 필터링, 순서 지정, 페이징을](entities-overview.md)참조 하세요.

## <a name="ask-questions-give-feedback-get-updates"></a>질문, 피드백 제공, 업데이트 받기

[Azure Media Services 커뮤니티](media-services-community.md) 문서를 체크 아웃하여 다양한 방법으로 질문을 하고, 피드백을 제공하고, Media Services에 대한 업데이트를 가져올 수 있습니다.

## <a name="see-also"></a>참고자료

[Azure CLI](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)

## <a name="next-steps"></a>다음 단계

* [Java를 사용 하 여 Media Services에 연결](configure-connect-java-howto.md)
* [.NET을 사용 하 여 Media Services에 연결](configure-connect-dotnet-howto.md)
* [Node.js를 사용 하 여 Media Services에 연결](configure-connect-nodejs-howto.md)
* [Python을 사용 하 여 Media Services에 연결](configure-connect-python-howto.md)
