---
title: Azure Media Services v3 개요
titleSuffix: Azure Media Services
description: 빠른 시작, 자습서 및 코드 샘플에 대한 링크가 포함된 Azure Media Services v3에 대한 개략적인 개요입니다.
services: media-services
documentationcenter: na
author: IngridAtMicrosoft
manager: femila
editor: ''
tags: ''
keywords: Azure Media Services, 스트림, 브로드캐스트, 라이브, 오프라인
ms.service: media-services
ms.devlang: multiple
ms.topic: overview
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: mvc
ms.openlocfilehash: b11cd602850d23ac6ec37a9be51912adceb9afbc
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89267618"
---
# <a name="azure-media-services-v3-overview"></a>Azure Media Services v3 개요

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Azure Media Services는 브로드캐스트 품질의 비디오 스트리밍을 구현하고, 접근성과 배포를 향상시키며, 콘텐츠를 분석하는 등 여러 작업을 수행하는 솔루션을 빌드할 수 있게 하는 클라우드 기반 플랫폼입니다. 앱 개발자, 콜센터, 정부 기관, 엔터테인먼트 회사이든 Media Services는 현재 가장 인기있는 모바일 디바이스 및 브라우저를 통해 뛰어난 품질의 미디어 경험을 많은 사용자에게 제공하는 앱을 만들 수 있도록 도와줍니다.

Media Services v3 SDK는 [Media Services v3 OpenAPI 사양(Swagger)](https://aka.ms/ams-v3-rest-sdk)을 기반으로 합니다.

> [!NOTE]
> 현재 [Azure Portal](https://portal.azure.com/)을 사용하여 Media Services v3 [라이브 이벤트](live-events-outputs-concept.md)를 관리하고, v3 [자산](assets-concept.md)을 보고(관리 아님), [API 액세스에 대한 정보를 가져올](./access-api-howto.md) 수 있습니다. 다른 모든 관리 작업(예제: [변환 및 작업](transforms-jobs-concept.md) 및 [콘텐츠 보호](content-protection-overview.md))의 경우 [REST API](/rest/api/media/), [CLI](https://aka.ms/ams-v3-cli-ref) 또는 지원되는 [SDK](media-services-apis-overview.md#sdks) 중 하나를 사용합니다.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="compliance-privacy-and-security"></a>규정 준수, 개인 정보 보호 및 보안

중요한 사항이라 다시 말씀드리지만, Azure Media Services 사용 시 적용 가능한 모든 법률을 준수해야 하며, 다른 사용자의 권리를 침해하거나 다른 사용자에게 해가 될 수 있는 방식으로 Media Services 또는 다른 Azure 서비스를 사용해서는 안 됩니다.

비디오/이미지를 Media Services에 업로드하기 전에, Media Services와 Azure에서 데이터를 사용하고, 처리하고, 저장하는 데 대한 비디오/이미지에 포함된 개인(있는 경우)의 모든 필요한 동의(법에서 요구되는 경우)를 비롯하여 비디오/이미지를 사용할 수 있는 모든 적절한 권한이 있어야 합니다. 일부 관할 지역에서는 생체 데이터와 같은 특정 범주의 데이터를 수집하고 온라인으로 처리하고 저장하는 데 대해 특별한 법적 요구 사항이 적용될 수 있습니다. Media Services와 Azure를 특별한 법적 요구 사항이 적용되는 데이터를 처리하고 저장하는 데 사용하기 전에, 귀하에게 적용될 수 있는 이러한 법적 요구 사항을 반드시 준수해야 합니다.

Media Services의 규정 준수, 개인 정보 보호 및 보안에 대해 알아보려면 Microsoft [보안 센터](https://www.microsoft.com/trust-center/?rtc=1)를 참조하세요. Microsoft의 개인 정보 보호 의무, 데이터 삭제 방법을 비롯한 데이터 처리 및 보존 방식에 대한 자세한 내용은 Microsoft의 [개인정보처리방침](https://privacy.microsoft.com/PrivacyStatement), [Online Services 사용 약관](https://www.microsoft.com/licensing/product-licensing/products?rtc=1)("OST") 및 [Data Processing 추록](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=67)("DPA")을 검토하세요. Media Services를 사용하면 OST, DPA 및 개인정보처리방침을 준수할 것에 동의하는 것입니다.
 
## <a name="what-can-i-do-with-media-services"></a>Media Services로 무엇을 할 수 있나요?

Media Services를 사용하여 클라우드에서 다양한 미디어 워크플로를 빌드할 수 있습니다. Media Services에서 수행할 수 있는 작업의 몇 가지 예제는 다음과 같습니다.

* 다양한 브라우저 및 디바이스에서 재생할 수 있도록 다양한 형식의 비디오를 제공합니다. 다양한 클라이언트(모바일 디바이스, TV, PC 등)에 주문형 및 라이브 모두의 스트리밍 전달인 경우 비디오 및 오디오 콘텐츠를 적절하게 인코딩하고 패키징해야 합니다. 이러한 콘텐츠를 전달하고 스트리밍하는 방법을 보려면 [빠른 시작: 파일 인코딩 및 스트리밍](stream-files-dotnet-quickstart.md)을 참조하세요.
* 축구, 야구, 대학 및 고교 스포츠 등과 같은 대규모 온라인 시청자에게 라이브 스포츠 이벤트를 스트리밍합니다.
* 마을 회관, 시의회 회의 및 입법 기관과 같은 공공 회의 및 행사를 방송합니다.
* 녹화된 비디오 또는 오디오 컨텐츠를 분석합니다. 예를 들어, 고객 만족도를 높이기 위해 조직은 음성-텍스트를 추출하고 검색 인덱스 및 대시 보드를 빌드할 수 있습니다. 그런 다음 일반적인 불만, 불만의 원인 및 기타 관련 데이터 인텔리전스를 추출할 수 있습니다.
* 고객(예: 영화 스튜디오)이 독점 저작물의 액세스 및 사용을 제한해야 하는 경우 구독 비디오 서비스를 만들고 DRM 보호된 콘텐츠를 스트리밍합니다.
* 항공기, 기차 및 자동차에서 재생할 오프라인 콘텐츠를 제공합니다. 고객은 네트워크에서 연결이 끊길 것으로 예상되는 경우 재생을 위해 휴대 전화나 태블릿에 콘텐츠를 다운로드해야 할 수 있습니다.
* Azure Media Services 및 [Azure Cognitive Services API](../../index.yml?pivot=products&panel=ai)를 사용하여 음성-텍스트 캡션, 다중 언어로 번역 등을 위한 교육 온라인 학습 비디오 플랫폼을 구현합니다.
* [Azure Cognitive Services API](../../index.yml?pivot=products&panel=ai)와 함께 Azure Media Services를 사용하여 더 많은 청중(예: 청각 장애인 또는 다른 언어로 따라 읽기를 원하는 사람들)을 수용하기 위해 자막 및 캡션을 비디오에 추가합니다.
* Azure CDN을 사용하여 순간적인 높은 부하(예: 제품 런칭 이벤트 시작)를 더 효율적으로 처리하기 위한 대규모 크기 조정을 구현합니다.

## <a name="how-can-i-get-started-with-v3"></a>v3는 어떻게 시작하나요? 

Media Services v3를 사용하여 콘텐츠를 인코딩 및 패키징하고, 주문형 비디오 및 라이브 브로드캐스트를 스트리밍하고, 비디오를 분석하는 방법을 알아봅니다. 자습서, API 참조 및 기타 설명서에서는 수백만 명의 사용자로 확장되는 주문형 및 라이브 비디오나 오디오를 안전하게 스트리밍하는 방법을 보여 줍니다.

> [!TIP]
> 개발을 시작하기 전에 다음 사항을 검토합니다.<br/>* [기본 개념](concepts-overview.md)(중요 개념인 패키징, 인코딩 및 보호 포함)<br/>* [Media Services v3 API로 개발](media-services-apis-overview.md)(API 액세스, 명명 규칙 등에 대한 정보 포함)

### <a name="sdks"></a>SDK

[Azure Media Services v3 클라이언트 SDK](media-services-apis-overview.md#sdks)로 개발을 시작합니다.

### <a name="quickstarts"></a>빠른 시작  

빠른 시작에서는 신규 고객이 Media Services를 빠르게 사용해 볼 수 있는 기본적인 1일차 지침을 보여줍니다.

* [비디오 파일 스트리밍 - .NET](stream-files-dotnet-quickstart.md)
* [비디오 파일 스트리밍 - CLI](stream-files-cli-quickstart.md)
* [비디오 파일 스트리밍 - Node.js](stream-files-nodejs-quickstart.md)

### <a name="tutorials"></a>자습서

자습서에서는 일부 주요 Media Services 작업에 대한 시나리오 기반 프로시저를 보여줍니다.

* [원격 파일 및 스트림 비디오 인코딩 – REST](stream-files-tutorial-with-rest.md)
* [업로드된 파일 및 스트림 비디오 인코딩 – .NET](stream-files-tutorial-with-api.md)
* [라이브 스트리밍 - .NET](stream-live-tutorial-with-api.md)
* [비디오 분석 - .NET](analyze-videos-tutorial-with-api.md)
* [AES-128 동적 암호화 - .NET](protect-with-aes128.md)

### <a name="samples"></a>샘플

[이 샘플 브라우저](/samples/browse/?products=azure-media-services)를 사용하여 Azure Media Services 코드 샘플을 찾아볼 수 있습니다.

### <a name="how-to-guides"></a>방법 가이드

방법 가이드에는 작업을 완료하는 방법을 설명하는 코드 샘플이 포함되어 있습니다. 이 섹션에서는 많은 예제를 찾을 수 있습니다. 다음은 몇 가지 옵션입니다.

* [계정 만들기 - CLI](./create-account-howto.md)
* [API 액세스 - CLI](./access-api-howto.md)
* [HTTPS를 작업 입력으로 인코딩 - .NET](job-input-from-http-how-to.md)  
* [이벤트 모니터링 - 포털](monitor-events-portal-how-to.md)
* [다중 DRM으로 동적으로 암호화 - .NET](protect-with-drm.md) 
* [사용자 지정 변환을 사용하여 인코딩하는 방법 - CLI](custom-preset-cli-howto.md)

## <a name="ask-questions-give-feedback-get-updates"></a>질문, 피드백 제공, 업데이트 받기

[Azure Media Services 커뮤니티](media-services-community.md) 문서를 체크 아웃하여 다양한 방법으로 질문을 하고, 피드백을 제공하고, Media Services에 대한 업데이트를 가져올 수 있습니다.

## <a name="next-steps"></a>다음 단계

[기본 개념 알아보기](concepts-overview.md)
