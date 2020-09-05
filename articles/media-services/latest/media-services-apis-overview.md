---
title: v3 API로 개발
titleSuffix: Azure Media Services
description: Microsoft Azure Media Services v3로 개발 시 엔터티와 API에 적용되는 규칙에 대해 알아봅니다.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: seodec18
ms.openlocfilehash: 6cb771b8df4ab81ff9c538a38b084856b1dfbc39
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89267278"
---
# <a name="develop-with-media-services-v3-apis"></a>Microsoft Azure Media Services v3 API로 개발

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

개발자인 경우 Media Services [REST API](/rest/api/media/) 또는 REST API와 상호 작용할 수 있도록 하는 클라이언트 라이브러리를 사용하여 사용자 지정 미디어 워크플로를 손쉽게 만들고, 관리하고 유지할 수 있습니다. [Media Services v3](https://aka.ms/ams-v3-rest-sdk) API는 OpenAPI 사양(이전 명칭 Swagger)에 기반을 두고 있습니다.

이 문서에서는 Microsoft Azure Media Services v3로 개발할 때 엔터티와 API에 적용되는 규칙에 대해 설명합니다.

## <a name="accessing-the-azure-media-services-api"></a>Azure Microsoft Azure Media Services API에 액세스

Media Services 리소스 및 Media Services API에 액세스할 수 있는 권한을 부여하려면 먼저 인증을 거쳐야 합니다. Microsoft Azure Media Services는 [Microsoft Azure AD(Azure Active Directory) 기반](../../active-directory/fundamentals/active-directory-whatis.md) 인증을 지원합니다. 다음은 두 가지 일반적인 인증 옵션입니다.
 
* **서비스 주체 인증**: 서비스를 인증하는 데 사용됩니다 (예: 웹앱, 함수 앱, 논리 앱, API 및 마이크로서비스). 이 인증 방법을 일반적으로 사용하는 애플리케이션은 디먼 서비스, 중간 계층 서비스 또는 예약된 작업을 실행하는 앱입니다. 예를 들어 웹앱의 경우 서비스 주체로 Microsoft Azure Media Services에 연결하는 중간 계층이 항상 있어야 합니다.
* **사용자 인증**: 앱을 사용하여 Microsoft Azure Media Services 리소스와 상호 작용하는 사용자를 인증하는 데 사용됩니다. 대화형 앱은 먼저 사용자에게 사용자의 자격 증명을 묻는 메시지를 표시합니다. 예제는 권한 있는 사용자가 인코딩 작업 또는 라이브 스트리밍을 모니터링하기 위해 사용한 관리 콘솔 앱입니다.

Microsoft Azure Media Services API를 사용하려면 REST API 요청을 하는 사용자 또는 앱이 Microsoft Azure Media Services 계정 리소스에 액세스할 수 있고 **기여자** 또는 **소유자** 역할을 사용해야 합니다. **읽기 권한자** 역할로 API에 액세스할 수 있지만 **가져오기** 또는 **나열** 작업만 사용할 수 있습니다. 자세한 내용은 [Microsoft Azure Media Services 계정에 대한 역할 기반 액세스 제어](rbac-overview.md)를 참조하세요.

서비스 주체를 만드는 대신 Azure Resource Manager를 통해 Azure 리소스에 대한 관리 ID를 사용하여 Microsoft Azure Media Services API에 액세스하는 것이 좋습니다. Azure 리소스에 대한 관리 ID에 대한 자세한 내용은 [Azure 리소스에 대한 관리 ID란?](../../active-directory/managed-identities-azure-resources/overview.md)을 참조하세요.

### <a name="azure-ad-service-principal"></a>Microsoft Azure Active Directory 서비스 주체

Microsoft Azure Active Directory 앱 및 서비스 주체를 만드는 경우 앱은 자체 테넌트에 있어야 합니다. 앱을 만든 후 앱 **기여자** 또는 **소유자** 역할에 Microsoft Azure Media Services 계정에 대한 액세스 권한을 제공합니다.

Microsoft Azure Active Directory 앱을 만들 수 있는 권한이 있는지 확실하지 않은 경우 [필요한 권한](../../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)을 참조하세요.

다음 그림에서 숫자는 요청 흐름을 시간순으로 보여줍니다.

![웹 API에서 Microsoft Azure Active Directory를 사용하여 중간 계층 앱 인증](./media/use-aad-auth-to-access-ams-api/media-services-principal-service-aad-app1.png)

1. 중간 계층 앱은 다음 매개 변수가 있는 Microsoft Azure Active Directory 액세스 토큰을 요청합니다.  

   * Azure AD 테넌트 엔드포인트.
   * Media Services 리소스 URI.
   * REST Media Services의 리소스 URI.
   * Microsoft Azure Active Directory 앱 값: 클라이언트 ID 및 클라이언트 암호.

   필요한 값을 모두 가져오려면 [Azure Microsoft Azure Media Services API에 액세스](./access-api-howto.md)를 참조하세요.

2. Azure AD 액세스 토큰이 중간 계층으로 전송됩니다.
4. 중간 계층은 Azure AD 토큰과 함께 Azure Media REST API로 요청을 보냅니다.
5. 중간 계층은 Media Services에서 데이터를 다시 가져옵니다.

### <a name="samples"></a>샘플

Microsoft Azure Active Directory 서비스 주체와 연결하는 방법을 보여주는 다음 샘플을 참조하세요.

* [REST를 사용하여 연결](media-rest-apis-with-postman.md)  
* [Java로 연결](configure-connect-java-howto.md)
* [.NET으로 연결](configure-connect-dotnet-howto.md)
* [Node.js로 연결](configure-connect-nodejs-howto.md)
* [Python으로 연결](configure-connect-python-howto.md)

## <a name="naming-conventions"></a>명명 규칙

Azure Media Services v3 리소스 이름(예: 자산, 작업, 변환)은 Azure Resource Manager 명명 제약 조건에 따라 달라집니다. Azure Resource Manager에 따라 리소스 이름은 항상 고유합니다. 따라서 리소스 이름에 대해 고유 식별자 문자열(예: GUID)을 사용할 수 있습니다.

Microsoft Azure Media Services 리소스 이름에는 '<', '>', '%', '&', ':', '&#92;', '?', '/', '*', '+', '.', 작은따옴표 또는 제어 문자가 포함될 수 없습니다. 다른 문자를 모두 허용합니다. 리소스 이름의 최대 길이는 260자입니다.

Azure Resource Manager의 이름을 지정하는 방법에 대한 자세한 내용은 [명명 요구 사항](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/resource-api-reference.md#arguments-for-crud-on-resource) 및 [명명 규칙](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging)을 참조하세요.

### <a name="names-of-filesblobs-within-an-asset"></a>자산 내의 파일/BLOB 이름

자산 내의 파일/BLOB 이름은 [BLOB 이름 요구 사항](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)과 [NTFS 이름 요구 사항](/windows/win32/fileio/naming-a-file)을 따라야 합니다. 이러한 요구 사항을 따라야 하는 이유는 파일이 처리를 위해 BLOB 스토리지에서 로컬 NTFS 디스크로 복사될 수 있기 때문입니다.

## <a name="long-running-operations"></a>장기 실행 작업

Azure Microsoft Azure Media Services [Swagger 파일](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01/streamingservice.json)에 `x-ms-long-running-operation`으로 표시되는 작업은 장기 실행 작업입니다. 

비동기 Azure 작업을 추적하는 방법에 대한 자세한 내용은 [비동기 작업](../../azure-resource-manager/management/async-operations.md)을 참조하세요.

Microsoft Azure Media Services에는 다음과 같은 장기 실행 작업이 있습니다.

* [라이브 이벤트 만들기](/rest/api/media/liveevents/create)
* [라이브 이벤트 업데이트](/rest/api/media/liveevents/update)
* [라이브 이벤트 삭제](/rest/api/media/liveevents/delete)
* [라이브 이벤트 시작](/rest/api/media/liveevents/start)
* [LiveEvent 중지](/rest/api/media/liveevents/stop)

  이벤트를 중지할 때 연결된 모든 라이브 출력을 삭제하려면 `removeOutputsOnStop` 매개 변수를 사용합니다.  
* [LiveEvent 초기화](/rest/api/media/liveevents/reset)
* [LiveOutput 만들기](/rest/api/media/liveevents/create)
* [LiveOutput 삭제](/rest/api/media/liveevents/delete)
* [StreamingEndpoint 만들기](/rest/api/media/streamingendpoints/create)
* [StreamingEndpoint 업데이트](/rest/api/media/streamingendpoints/update)
* [StreamingEndpoint 삭제](/rest/api/media/streamingendpoints/delete)
* [StreamingEndpoint 시작](/rest/api/media/streamingendpoints/start)
* [StreamingEndpoint 중지](/rest/api/media/streamingendpoints/stop)
* [StreamingEndpoint 스케일링](/rest/api/media/streamingendpoints/scale)

장기 실행 작업 제출에 성공하면 '202 수락됨'이 수신되며 반환된 작업 ID를 사용하여 작업 완료를 폴링해야 합니다.

[비동기 Azure 작업 추적](../../azure-resource-manager/management/async-operations.md) 문서에서는 응답에서 반환된 값을 통해 비동기 Azure 작업의 상태를 추적하는 방법에 대해 자세히 설명합니다.

지정된 라이브 이벤트 또는 이와 연결된 라이브 출력에는 하나의 장기 실행 작업만 지원됩니다. 일단 시작되면 동일한 LiveEvent 또는 연결된 라이브 출력에서 후속 장기 실행 작업을 시작하기 전에 먼저 시작된 장기 실행 작업을 완료해야 합니다. 라이브 출력이 여러 개 있는 라이브 이벤트의 경우 한 라이브 출력에서 장기 실행 작업이 완료될 때까지 기다렸다가 다른 라이브 출력에서 장기 실행 작업을 트리거해야 합니다. 

## <a name="sdks"></a>SDK

> [!NOTE]
> Azure Microsoft Azure Media Services v3 SDK는 스레드로부터 안전을 보장하지 않습니다. 다중 스레드 앱을 개발하는 경우 클라이언트를 보호하는 고유한 스레드 동기화 논리를 추가하거나 스레드별로 새 AzureMediaServicesClient 개체를 사용해야 합니다. 코드에서 클라이언트에 제공하는 선택적 개체(예: .NET의 HttpClient 인스턴스) 때문에 다중 스레드 문제가 도입될 수 있다는 점에도 주의해야 합니다.

|SDK)|참조|
|---|---|
|[.NET SDK](https://aka.ms/ams-v3-dotnet-sdk)|[.NET 참조](https://aka.ms/ams-v3-dotnet-ref)|
|[Java SDK](https://aka.ms/ams-v3-java-sdk)|[Java 참조](https://aka.ms/ams-v3-java-ref)|
|[Python SDK](https://aka.ms/ams-v3-python-sdk)|[Python 예제](https://aka.ms/ams-v3-python-ref)|
|[Node.js SDK](https://aka.ms/ams-v3-nodejs-sdk) |[Node.js 예제](/javascript/api/overview/azure/mediaservices/management)| 
|[SDK로 이동](https://aka.ms/ams-v3-go-sdk) |[참조로 이동](https://aka.ms/ams-v3-go-ref)|
|[Ruby SDK](https://aka.ms/ams-v3-ruby-sdk)||

### <a name="see-also"></a>참고 항목

- [미디어 서비스 이벤트를 포함하는 EventGrid .NET SDK](https://www.nuget.org/packages/Microsoft.Azure.EventGrid/)
- [Media Services 이벤트의 정의](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/eventgrid/data-plane/Microsoft.Media/stable/2018-01-01/MediaServices.json)

## <a name="azure-media-services-explorer"></a>Azure Media Services 탐색기

[Azure Media Services 탐색기](https://github.com/Azure/Azure-Media-Services-Explorer)(AMSE)는 Media Services에 대해 알아보고자 하는 Windows 고객이 사용할 수 있는 도구입니다. AMSE는 Media Services에서 VOD 및 실시간 콘텐츠의 업로드, 다운로드, 인코딩, 스트리밍을 수행하는 Winforms/C# 애플리케이션입니다. AMSE 도구는 코드를 작성하지 않고 Media Services를 테스트하려는 고객을 위한 도구입니다. AMSE 코드는 Media Services를 사용하여 개발하려는 고객을 위한 리소스로 제공됩니다.

AMSE는 오픈 소스 프로젝트이며, 커뮤니티에서 지원을 제공합니다(문제는 https://github.com/Azure/Azure-Media-Services-Explorer/issues) 에 보고할 수 있음). 이 프로젝트는 [Microsoft 오픈 소스 준수 사항](https://opensource.microsoft.com/codeofconduct/)을 채택했습니다. 자세한 내용은 [준수 사항 FAQ](https://opensource.microsoft.com/codeofconduct/faq/)를 참조하고, 추가 질문이나 의견이 있는 경우에는 opencode@microsoft.com으로 문의하세요.

## <a name="filtering-ordering-paging-of-media-services-entities"></a>Media Services 엔터티 필터링, 순서 지정, 페이징

[Azure Microsoft Azure Media Services 엔터티 필터링, 순서 지정, 페이징](entities-overview.md)을 참조하세요.

## <a name="ask-questions-give-feedback-get-updates"></a>질문, 피드백 제공, 업데이트 받기

[Azure Media Services 커뮤니티](media-services-community.md) 문서를 체크 아웃하여 다양한 방법으로 질문을 하고, 피드백을 제공하고, Media Services에 대한 업데이트를 가져올 수 있습니다.

## <a name="see-also"></a>참고 항목

필요한 값을 모두 가져오려면 [Azure Microsoft Azure Media Services API에 액세스](./access-api-howto.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

* [Java를 사용하여 Microsoft Azure Media Services에 연결](configure-connect-java-howto.md)
* [.NET을 사용하여 Microsoft Azure Media Services에 연결](configure-connect-dotnet-howto.md)
* [Node.js를 사용하여 Microsoft Azure Media Services에 연결](configure-connect-nodejs-howto.md)
* [Python을 사용하여 Microsoft Azure Media Services에 연결](configure-connect-python-howto.md)
