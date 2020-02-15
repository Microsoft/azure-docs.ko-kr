---
title: Azure Media Services v3 개요
titleSuffix: Azure Media Services
description: 빠른 시작, 자습서 및 코드 샘플에 대한 링크가 포함된 Azure Media Services v3에 대한 개략적인 개요입니다.
services: media-services
documentationcenter: na
author: Juliako
manager: femila
editor: ''
tags: ''
keywords: Azure Media Services, 스트림, 브로드캐스트, 라이브, 오프라인
ms.service: media-services
ms.devlang: multiple
ms.topic: overview
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 02/03/2020
ms.author: juliako
ms.custom: mvc
ms.openlocfilehash: 559050d11b6b85a9842c679b94e0e5b2fd5dd0f8
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/04/2020
ms.locfileid: "76987716"
---
# <a name="azure-media-services-v3-overview"></a>Azure Media Services v3 개요

Azure Media Services는 브로드캐스트 품질의 비디오 스트리밍을 구현하고, 접근성과 배포를 향상시키며, 콘텐츠를 분석하는 등 여러 작업을 수행하는 솔루션을 빌드할 수 있게 하는 클라우드 기반 플랫폼입니다. 앱 개발자, 콜센터, 정부 기관, 엔터테인먼트 회사이든 Media Services는 현재 가장 인기있는 모바일 디바이스 및 브라우저를 통해 뛰어난 품질의 미디어 경험을 많은 사용자에게 제공하는 앱을 만들 수 있도록 도와줍니다.

Media Services v3 SDK는 [Media Services v3 OpenAPI 사양(Swagger)](https://aka.ms/ams-v3-rest-sdk)을 기반으로 합니다.

> [!NOTE]
> 현재는 Azure Portal을 사용하여 v3 리소스를 관리할 수 없습니다. [REST API](https://aka.ms/ams-v3-rest-ref), [CLI](https://aka.ms/ams-v3-cli-ref) 또는 지원되는 [SDK](media-services-apis-overview.md#sdks) 중 하나를 사용하세요.

## <a name="compliance-privacy-and-security"></a>규정 준수, 개인 정보 보호 및 보안
 
중요한 알림을 사용하는 경우 Azure Media Services 사용 시 적용 가능한 모든 법률을 준수해야 하며, 다른 사용자의 권리를 위반하거나 다른 사용자에게 해로운 방법으로 Azure Media Services 또는 다른 Azure 서비스를 사용해서는 안 됩니다. 

처리 및 저장을 위해 Media Services에 모든 생체 인식 데이터를 비롯한 비디오/이미지를 업로드하려면, (1) 비디오/이미지에 대한 모든 적절한 권리가 있어야 하며, (2) 해당 생체 인식 데이터를 Media Services에 저장 및 사용하도록 허용하는 비디오에 나온 모든 사람들의 적절한 동의가 있어야 합니다. 

규정 준수, 개인 정보 및 Microsoft에서 Azure Media Services 데이터를 보호하는 방법에 대해 알아보려면 Azure [보안 센터](https://www.microsoft.com/trust-center/?rtc=1)를 방문하세요. Microsoft의 개인 정보 보호 의무 및 데이터 처리에 대한 내용은 Microsoft의 [개인정보처리방침](https://privacy.microsoft.com/PrivacyStatement), [Online Services 사용 약관](https://www.microsoft.com/licensing/product-licensing/products)(“OST”) 및 [Data Processing 추록](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=67)(“DPA”)을 검토하세요. 고객 데이터 보존 및 삭제에 대한 정보는 DPA에서 제공됩니다. Azure Media Services를 사용하면 OST, DPA 및 개인정보처리방침의 범위에 동의하는 것입니다.

## <a name="what-can-i-do-with-media-services"></a>Media Services로 무엇을 할 수 있나요?

Media Services를 사용하여 클라우드에서 다양한 미디어 워크플로를 빌드할 수 있습니다. Media Services에서 수행할 수 있는 작업의 몇 가지 예제는 다음과 같습니다.

* 다양한 브라우저 및 디바이스에서 재생할 수 있도록 다양한 형식의 비디오를 제공합니다. 다양한 클라이언트(모바일 디바이스, TV, PC 등)에 주문형 및 라이브 모두의 스트리밍 전달인 경우 비디오 및 오디오 콘텐츠를 적절하게 인코딩하고 패키징해야 합니다. 이러한 콘텐츠를 전달하고 스트리밍하는 방법을 보려면 [빠른 시작: 파일 인코딩 및 스트리밍](stream-files-dotnet-quickstart.md)을 참조하세요.
* 축구, 야구, 대학 및 고교 스포츠 등과 같은 대규모 온라인 시청자에게 라이브 스포츠 이벤트를 스트리밍합니다.
* 마을 회관, 시의회 회의 및 입법 기관과 같은 공공 회의 및 행사를 방송합니다.
* 녹화된 비디오 또는 오디오 컨텐츠를 분석합니다. 예를 들어, 고객 만족도를 높이기 위해 조직은 음성-텍스트를 추출하고 검색 인덱스 및 대시 보드를 빌드할 수 있습니다. 그런 다음 일반적인 불만, 불만의 원인 및 기타 관련 데이터 인텔리전스를 추출할 수 있습니다.
* 고객(예: 영화 스튜디오)이 독점 저작물의 액세스 및 사용을 제한해야 하는 경우 구독 비디오 서비스를 만들고 DRM 보호된 콘텐츠를 스트리밍합니다.
* 항공기, 기차 및 자동차에서 재생할 오프라인 콘텐츠를 제공합니다. 고객은 네트워크에서 연결이 끊길 것으로 예상되는 경우 재생을 위해 휴대 전화나 태블릿에 콘텐츠를 다운로드해야 할 수 있습니다.
* Azure Media Services 및 [Azure Cognitive Services API](https://docs.microsoft.com/azure/#pivot=products&panel=ai)를 사용하여 음성-텍스트 캡션, 다중 언어로 번역 등을 위한 교육 온라인 학습 비디오 플랫폼을 구현합니다.
* [Azure Cognitive Services API](https://docs.microsoft.com/azure/#pivot=products&panel=ai)와 함께 Azure Media Services를 사용하여 더 많은 청중(예: 청각 장애인 또는 다른 언어로 따라 읽기를 원하는 사람들)을 수용하기 위해 자막 및 캡션을 비디오에 추가합니다.
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

[이 샘플 브라우저](https://docs.microsoft.com/samples/browse/?products=azure-media-services)를 사용하여 Azure Media Services 코드 샘플을 찾아볼 수 있습니다.

### <a name="how-to-guides"></a>방법 가이드

방법 가이드에는 작업을 완료하는 방법을 설명하는 코드 샘플이 포함되어 있습니다. 이 섹션에서는 많은 예제를 찾을 수 있습니다. 다음은 몇 가지 옵션입니다.

* [계정 만들기 - CLI](create-account-cli-how-to.md)
* [API 액세스 - CLI](access-api-cli-how-to.md)
* [HTTPS를 작업 입력으로 인코딩 - .NET](job-input-from-http-how-to.md)  
* [이벤트 모니터링 - 포털](monitor-events-portal-how-to.md)
* [다중 DRM으로 동적으로 암호화 - .NET](protect-with-drm.md) 
* [사용자 지정 변환을 사용하여 인코딩하는 방법 - CLI](custom-preset-cli-howto.md)

## <a name="ask-questions-give-feedback-get-updates"></a>질문, 피드백 제공, 업데이트 받기

[Azure Media Services 커뮤니티](media-services-community.md) 문서를 체크 아웃하여 다양한 방법으로 질문을 하고, 피드백을 제공하고, Media Services에 대한 업데이트를 가져올 수 있습니다.

## <a name="next-steps"></a>다음 단계

[기본 개념 알아보기](concepts-overview.md)

