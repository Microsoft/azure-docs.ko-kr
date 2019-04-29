---
title: V3 Api-Azure 사용 하 여 개발 | Microsoft Docs
description: 이 문서에서는 Media Services v3을 사용 하 여 개발 하는 경우 엔터티 및 Api에 적용 되는 규칙을 설명 합니다.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 04/21/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: a336f7faae24517aa4e29c991886107c6a82d2b5
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62103909"
---
# <a name="developing-with-media-services-v3-apis"></a>V3 Api를 Media Services를 사용 하 여 개발

이 문서에서는 Media Services v3을 사용 하 여 개발 하는 경우 엔터티 및 Api에 적용 되는 규칙을 설명 합니다.

## <a name="accessing-the-azure-media-services-api"></a>Azure Media Services API 액세스

Azure Media Services 리소스에 액세스 하려면 Azure AD (Active Directory) 서비스 주체 인증을 사용할 수 있습니다.
Media Services API에 필요한 사용자 또는 응용 프로그램은 REST API 요청 Media Services 계정 리소스에 액세스할 수 있으며 사용을 **참여자** 또는 **소유자** 역할입니다. API를 사용 하 여 액세스할 수 있습니다는 **판독기** 역할에만 **가져오기** 하거나 **목록**   작업을 사용할 수 있습니다. 자세한 내용은 [Media Services 계정에 대 한 역할 기반 액세스 제어](rbac-overview.md)입니다.

서비스 주체를 만드는 대신 Azure 리소스에 대 한 관리 되는 id를 사용 하 여 Azure Resource Manager를 통해 Media Services API에 액세스 하는 것이 좋습니다. Azure 리소스에 대 한 관리 되는 id에 대 한 자세한 내용은 참조 하세요 [Azure 리소스에 대 한 관리 되는 id 란](../../active-directory/managed-identities-azure-resources/overview.md)합니다.

### <a name="azure-ad-service-principal"></a>Azure AD 서비스 주체 

Azure AD 응용 프로그램 및 서비스 주체를 만드는 경우 응용 프로그램은 자체 테 넌 트입니다. 응용 프로그램을 만든 후 앱을 제공 **참여자** 하거나 **소유자** Media Services 계정에 대 한 역할 액세스 합니다. 

내용은 Azure AD 응용 프로그램을 만들 수 있는 권한이 있는지 확실 하지 않은 경우 [필요한 권한](../../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)합니다.

다음 그림에서는 숫자 순서로 요청 흐름을을 나타냅니다.

![중간 계층 앱](./media/use-aad-auth-to-access-ams-api/media-services-principal-service-aad-app1.png)

1. 중간 계층 앱에는 다음 매개 변수가 있는 Azure AD 액세스 토큰을 요청 합니다.  

   * Azure AD 테넌트 엔드포인트.
   * Media Services 리소스 URI.
   * REST Media Services의 리소스 URI.
   * Azure AD 애플리케이션 값: 클라이언트 ID 및 클라이언트 암호.
   
   필요한 모든 값을 가져오려면를 참조 하세요. [Azure CLI를 사용 하 여 Azure Media Services API 액세스](access-api-cli-how-to.md)

2. Azure AD 액세스 토큰이 중간 계층으로 전송됩니다.
4. 중간 계층은 Azure AD 토큰과 함께 Azure Media REST API로 요청을 보냅니다.
5. 중간 계층은 Media Services에서 데이터를 다시 가져옵니다.

## <a name="naming-conventions"></a>명명 규칙

Azure Media Services v3 리소스 이름(예: 자산, 작업, 변환)은 Azure Resource Manager 명명 제약 조건에 따라 달라집니다. Azure Resource Manager에 따라 리소스 이름은 항상 고유합니다. 따라서 리소스 이름에 대해 고유 식별자 문자열(예: GUID)을 사용할 수 있습니다. 

Media Services 리소스 이름에는 '<', '>', '%', '&', ':', '&#92;', '?', '/', '*', '+', '.', 작은 따옴표 또는 제어 문자가 포함될 수 없습니다. 다른 문자를 모두 허용합니다. 리소스 이름의 최대 길이는 260자입니다. 

Azure Resource Manager 이름 지정에 대한 자세한 내용은 [명명 요구 사항](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/resource-api-reference.md#arguments-for-crud-on-resource) 및 [명명 규칙](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions)을 참조하세요.

## <a name="long-running-operations"></a>장기 실행 작업

작업 표시 `x-ms-long-running-operation` Azure Media services에서 [swagger 파일](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01/streamingservice.json) 긴 작업을 실행 합니다. 

Azure 비동기 작업을 추적 하는 방법에 대 한 자세한 내용은 참조 하세요 [비동기 작업](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations#monitor-status-of-operation)합니다.

Media Services에는 다음과 같은 장기 실행 작업에 있습니다.

* LiveEvent 만들기
* LiveEvent 업데이트
* LiveEvent 삭제
* LiveEvent 시작
* LiveEvent 중지
* LiveEvent 다시 설정
* LiveOutput 만들기
* LiveOutput 삭제
* StreamingEndpoint 만들기
* StreamingEndpoint 업데이트
* StreamingEndpoint 삭제
* StreamingEndpoint 시작
* StreamingEndpoint 중지
* StreamingEndpoint 크기 조정

## <a name="filtering-ordering-paging-of-media-services-entities"></a>Media Services 엔터티 필터링, 순서 지정, 페이징

참조 [필터링, 정렬, 페이징 Azure Media Services 엔터티](entities-overview.md)

## <a name="provide-feedback"></a>피드백 제공

[Azure Media Services 커뮤니티](media-services-community.md) 문서를 체크 아웃하여 다양한 방법으로 질문을 하고, 피드백을 제공하고, Media Services에 대한 업데이트를 가져올 수 있습니다.

## <a name="next-steps"></a>다음 단계

[Sdk/도구를 사용 하 여 Media Services v3 API를 사용 하 여 개발 시작](developers-guide.md)
