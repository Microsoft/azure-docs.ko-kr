---
title: Azure Media Services 개요 | Microsoft Docs
description: Microsoft Azure Media Services는 개발자가 확장 가능한 미디어 관리 및 배달 애플리케이션을 빌드할 수 있는 확장 가능한 클라우드 기반 플랫폼입니다. 이 문서에서는 Azure Media Services에 대 한 개요를 제공 합니다.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/19/2019
ms.author: juliako
ms.openlocfilehash: 0d896b0ce23116f1b87718f492d407ac094b9e6a
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89261158"
---
# <a name="azure-media-services-overview"></a>Azure Media Services 개요

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector" title1="사용 중인 Media Services의 버전을 선택 합니다."]
> * [버전 3](../latest/media-services-overview.md)
> * [버전 2](media-services-overview.md)

> [!NOTE]
> Media Services v2에는 새로운 기능이 추가되지 않습니다. <br/>[Media Services v3](../latest/index.yml)의 최신 버전을 확인하세요. 또한 [v2에서 v3로의 마이그레이션 지침](../latest/migrate-from-v2-to-v3.md)을 참조하세요.

Microsoft AMS(Azure Media Services)는 개발자가 확장 가능한 미디어 관리 및 배달 애플리케이션을 빌드할 수 있는 확장 가능한 클라우드 기반 플랫폼입니다. Media Services는 다양한 클라이언트(예: TV, PC 및 모바일 디바이스)로의 주문형 및 라이브 스트리밍 배달을 위해 비디오 또는 오디오 콘텐츠를 안전하게 업로드, 저장, 인코딩 및 패키지할 수 있는 REST API를 기반으로 합니다.

전체 Media Services를 사용하여 엔드투엔드 워크플로를 작성할 수 있습니다. 또한 워크플로의 일부에 타사 구성 요소를 사용하도록 선택할 수 있습니다. 예를 들어 타사 인코더를 사용하여 인코딩합니다. 그런 다음 Media Services를 사용하여 업로드, 보호, 패키징 및 배달합니다. 콘텐츠를 라이브로 스트리밍하고 주문형 콘텐츠를 배달하도록 선택할 수 있습니다. 


## <a name="compliance-privacy-and-security"></a>규정 준수, 개인 정보 보호 및 보안

중요 한 알림으로 Azure Media Services 사용 시 관련 법률을 준수 해야 하 고 다른 사용자의 권리를 위반 하거나 다른 사용자에 게 유해한 영향을 주는 방식으로 Media Services 또는 Azure 서비스를 사용 하지 못할 수 있습니다.

Media Services에 대 한 비디오/이미지를 업로드 하기 전에, Media Services 및 Azure에서 데이터 사용, 처리 및 저장을 위해 법률에서 요구 하는 경우, 비디오/이미지에서 개인의 모든 필수 동의 (있는 경우)를 포함 하 여 비디오/이미지를 사용할 수 있는 적절 한 권한이 있어야 합니다. 일부 관할 지역에서는 생체 데이터와 같은 특정 범주의 데이터를 수집하고 온라인으로 처리하고 저장하는 데 대해 특별한 법적 요구 사항이 적용될 수 있습니다. 특별 한 법적 요구 사항이 적용 되는 데이터의 처리 및 저장을 위해 Media Services 및 Azure를 사용 하기 전에 사용자에 게 적용 될 수 있는 이러한 법적 요구 사항을 준수 하는지 확인 해야 합니다.

Media Services의 규정 준수, 개인 정보 보호 및 보안에 대 한 자세한 내용은 Microsoft [보안 센터](https://www.microsoft.com/trust-center/?rtc=1)를 참조 하세요. 데이터를 삭제 하는 방법을 비롯 하 여 Microsoft의 개인 정보 취급 방침, 데이터 처리 및 보존 방법에 대 한 자세한 내용은 Microsoft [개인 정보](https://privacy.microsoft.com/PrivacyStatement)취급 방침, [Online Services 약관](https://www.microsoft.com/licensing/product-licensing/products?rtc=1) ("OST") 및 [데이터 처리 추 록](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=67) ("DPA")을 참조 하세요. Media Services를 사용 하 여 OST, DPA 및 개인정보 처리 방침의 범위에 동의 하는 것입니다.
 
## <a name="prerequisites"></a>필수 구성 요소

Azure Media Services 사용을 시작하려면 다음이 있어야 합니다.

* Azure 계정. 계정이 없는 경우 몇 분 만에 평가판 계정을 만들 수 있습니다. 자세한 내용은 [Azure 평가판](https://azure.microsoft.com)을 참조하세요.
* Azure Media Services 계정. 자세한 내용은 [계정 만들기](media-services-portal-create-account.md)를 참조하세요.
* (선택 사항) 개발 환경 설정. 개발 환경에 .NET 또는 REST API를 선택합니다. 자세한 내용은 [환경 설정](media-services-dotnet-how-to-use.md)을 참조하세요.

    [프로그래밍 방식으로 AMS API에 연결](media-services-use-aad-auth-to-access-ams-api.md)하는 방법도 알아봅니다.
* 시작된 상태에 있는 표준 또는 프리미엄 스트리밍 엔드포인트.  자세한 내용은 [스트리밍 끝점 관리](media-services-portal-manage-streaming-endpoints.md) 를 참조 하세요.

## <a name="sdks-and-tools"></a>SDK 및 도구

Media Services 솔루션을 빌드하려면 다음을 사용할 수 있습니다.

* [Media Services REST API](/rest/api/media/operations/azure-media-services-rest-api-reference)
* 사용 가능한 클라이언트 SDK 중 하나:
    * .NET 용 Azure Media Services SDK
    
        * [NuGet 패키지](https://www.nuget.org/packages/windowsazure.mediaservices/)
        * [GitHub 소스 코드](https://github.com/Azure/azure-sdk-for-media-services)
    * [Java 용 AZURE SDK](https://github.com/Azure/azure-sdk-for-java)
    * [Azure PHP SDK](https://github.com/Azure/azure-sdk-for-php),
    * [Node.js용 Azure Media Services](https://github.com/michelle-becker/node-ams-sdk/blob/master/lib/request.js) (Node.js SDK의 Microsoft가 아닌 타사 버전입니다. 커뮤니티에서 유지 관리하고 현재 AMS API를 100% 포함하지 않습니다).
* 기존 도구:
    * [Azure Portal](https://portal.azure.com/)
    * [Azure-Media-Services-Explorer](https://github.com/Azure/Azure-Media-Services-Explorer) (AMSE(Azure Media Services 탐색기)는 Windows용 Winforms/C# 애플리케이션임)

> [!NOTE]
> 최신 버전의 Java SDK를 가져와서 Java를 사용하여 개발을 시작하려면 [Media Services용 Java 클라이언트 SDK 시작](./media-services-java-how-to-use.md)을 참조하세요. <br/>
> Media Services용 최신 PHP SDK를 다운로드하려면 [Packagist 리포지토리](https://packagist.org/packages/microsoft/windowsazure#v0.5.7)에서 Microsoft/WindowAzure 패키지 버전 0.5.7을 찾습니다.  

## <a name="code-samples"></a>코드 샘플

**Azure 코드 샘플** 갤러리: [Azure Media Services 코드 샘플](https://azure.microsoft.com/resources/samples/?service=media-services&sort=0)에서 여러 코드 샘플을 찾습니다.

## <a name="concepts"></a>개념

Azure Media Services 개념은 [개념](media-services-concepts.md)을 참조하세요.

## <a name="supported-scenarios-and-availability-of-media-services-across-data-centers"></a>지원되는 시나리오 및 데이터 센터에서 Media Services의 사용 가용성

자세한 내용은 [AMS 시나리오 및 데이터 센터에서 기능 및 서비스의 사용 가용성](scenarios-and-availability.md)을 참조하세요.

## <a name="service-level-agreement-sla"></a>SLA(서비스 수준 계약)

자세한 내용은 [Microsoft Azure SLA](https://azure.microsoft.com/support/legal/sla/)를 참조하세요.

데이터 센터에서 사용 가용성에 대한 정보는 [사용 가능성](scenarios-and-availability.md#availability) 섹션을 참조하세요.

## <a name="support"></a>지원

[Azure 지원](https://azure.microsoft.com/support/options/) 에서는 Media Services를 포함하여 Azure에 대한 지원 옵션을 제공합니다.

## <a name="provide-feedback"></a>피드백 제공

[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
