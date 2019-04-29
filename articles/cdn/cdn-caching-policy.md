---
title: Azure Media Services에서 Azure CDN 캐싱 정책 관리 | Microsoft Docs
description: Azure Media Services에서 Azure CDN 캐싱 정책을 관리하는 방법을 알아봅니다.
services: media-services,cdn
documentationcenter: .NET
author: juliako
manager: erikre
editor: ''
ms.assetid: be33aecc-6dbe-43d7-a056-10ba911e0e94
ms.service: media-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/04/2017
ms.author: juliako
ms.openlocfilehash: 516df2f6177303987fc0354dde647c1fc26820ef
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61069464"
---
# <a name="manage-azure-cdn-caching-policy-in-azure-media-services"></a>Azure Media Services에서 Azure CDN 캐싱 정책 관리
Azure Media Services는 HTTP 기반 적응 스트리밍 및 점진적 다운로드를 제공합니다. HTTP 기반 스트리밍은 클라이언트 쪽 캐싱뿐만 아니라 프록시 및 CDN 계층의 캐싱을 활용하므로 확장성이 뛰어납니다. 스트리밍 엔드포인트는 일반적인 스트리밍 기능 및 HTTP 캐시 헤더에 대한 구성을 제공합니다. 스트리밍 엔드포인트는 HTTP Cache-Control: max-age 및 Expires 헤더를 설정합니다. HTTP 캐시 헤더에 대한 자세한 내용은 [W3.org](https://www.w3.org/Protocols/rfc2616/rfc2616-sec13.html)에서 확인할 수 있습니다.

## <a name="default-caching-headers"></a>기본 캐싱 헤더
기본적으로 스트리밍 끝점은 주문형 스트리밍 데이터(실제 미디어 조각/청크) 및 매니페스트(재생 목록)에 대해 3일 캐시 헤더를 적용합니다. 라이브 스트리밍의 경우 스트리밍 엔드포인트는 데이터(실제 미디어 조각/청크)에 대해 3일 캐시 헤드를 적용하고, 매니페스트(재생 목록) 요청에 대해 2초 캐시 헤더를 적용합니다. 라이브 프로그램이 주문형(라이브 보관 파일)으로 바뀐 경우에는 주문형 스트리밍 캐시 헤더가 적용됩니다.

## <a name="azure-cdn-integration"></a>Azure CDN 통합
Azure Media Services는 스트리밍 끝점에 대한 [CDN 통합](https://azure.microsoft.com/updates/azure-media-services-now-fully-integrated-with-azure-cdn/) 을 제공합니다. Cache-control 헤더는 CDN 사용 스트리밍 엔드포인트에 스트리밍 엔드포인트와 동일한 방식으로 적용됩니다. Azure CDN은 스트리밍 엔드포인트에 구성된 캐시 값을 사용하여 내부적으로 캐시된 개체의 수명을 정의하며, 이 값을 사용하여 배달 캐시 헤더도 설정합니다. CDN 사용 스트리밍 엔드포인트를 사용하는 경우에는 작은 캐시 값을 설정하지 않는 것이 좋습니다. 작은 값을 설정하면 성능이 저하되고 CDN의 이점이 줄어듭니다. CDN 사용 스트리밍 엔드포인트의 경우 캐시 헤더를 600초보다 작게 설정할 수 없습니다.

> [!IMPORTANT]
>Azure Media Services는 Azure CDN과 완전하게 통합됩니다. 한 번의 클릭으로 사용 가능한 모든 Azure CDN 공급자를 표준 및 프리미엄 제품을 포함하는 스트리밍 엔드포인트에 통합할 수 있습니다. 자세한 내용은 이 [공지](https://azure.microsoft.com/blog/standardstreamingendpoint/)를 참조하세요.
> 
> 스트리밍 엔드포인트에서 CDN으로의 데이터 요금은 스트리밍 엔드포인트 API 또는 Azure Portal의 스트리밍 엔드포인트 섹션을 통해 CDN이 사용되도록 설정되는 경우에만 사용되지 않도록 설정됩니다. 수동 통합이나 CDN API 또는 포털 섹션을 통해 CDN 엔드포인트를 직접 만드는 경우에는 데이터 요금이 사용되지 않도록 설정되지 않습니다.

## <a name="configuring-cache-headers-with-azure-media-services"></a>Azure Media Services를 사용하여 캐시 헤더 구성
Azure Portal 또는 Azure Media Services API를 사용하여 캐시 헤더 값을 구성할 수 있습니다.

1. Azure Portal을 사용하여 캐시 헤더를 구성하려면 스트리밍 엔드포인트 구성에서 [스트리밍 엔드포인트를 관리하는 방법](../media-services/previous/media-services-portal-manage-streaming-endpoints.md) 섹션을 참조하세요.
2. Azure Media Services REST API, [StreamingEndpoint](/rest/api/media/operations/streamingendpoint#StreamingEndpointCacheControl)
3. Azure Media Services .NET SDK, [StreamingEndpointCacheControl 속성](https://go.microsoft.com/fwlink/?LinkId=615302)

## <a name="cache-configuration-precedence-order"></a>캐시 구성 우선 순위
1. Azure Media Services에서 구성된 캐시 값은 기본값을 재정의합니다.
2. 수동 구성이 없으면 기본값이 적용됩니다.
3. 라이브 스트리밍에는 Azure 미디어 또는 Azure Storage 구성에 상관없이 기본적으로 2초 캐시 헤더가 적용되며, 이 값은 재정의할 수 없습니다.

