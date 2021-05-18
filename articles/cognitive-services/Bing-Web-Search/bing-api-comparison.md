---
title: Bing Search API란?
titleSuffix: Azure Cognitive Services
description: 이 문서에서는 Bing Search API에 대해 알아보고, 앱 및 서비스에서 인지적 인터넷 검색을 사용하는 방법에 대해 자세히 알아봅니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 03/12/2019
ms.author: aahi
ms.openlocfilehash: 72b4755c6f01a10851e79cf274842f1599bc2c55
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "96349540"
---
# <a name="what-are-the-bing-search-apis"></a>Bing Search API란?

> [!WARNING]
> Bing Search API는 Cognitive Services에서 Bing Search Services로 이동합니다. **2020년 10월 30일** 부터 Bing Search의 모든 새 인스턴스는 [여기](/bing/search-apis/bing-web-search/create-bing-search-service-resource)에 설명된 프로세스에 따라 프로비저닝되어야 합니다.
> Cognitive Services를 사용하여 프로비저닝된 Bing Search API는 향후 3년 동안 또는 기업계약이 종료될 때까지(둘 중 먼저 도래할 때까지) 지원됩니다.
> 마이그레이션 지침은 [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource)를 참조하세요.

Bing Search API를 사용하면 광고 없이 웹 페이지, 이미지, 뉴스, 위치 등을 찾는 웹 연결 앱 및 서비스를 구축할 수 있습니다. Bing Search REST API 또는 SDK를 사용하여 검색 요청을 보내면 웹 검색으로 관련 정보와 콘텐츠를 얻을 수 있습니다. 이 문서에서는 다양한 Bing Search API에 대해 알아보고, 애플리케이션 및 서비스에서 인지적 검색을 통합하는 방법에 대해 자세히 알아봅니다. 가격 책정 및 금액 한도는 API마다 다를 수 있습니다.

## <a name="the-bing-web-search-api"></a>Bing Web Search API

[Bing Web Search API](../Bing-Web-Search/overview.md)는 웹 페이지, 이미지, 비디오, 뉴스 등을 반환합니다. 이 API로 보낸 검색 쿼리를 필터링하여 특정 콘텐츠 형식을 포함하거나 제외할 수 있습니다.

모든 형식의 관련 웹 콘텐츠를 검색해야 하는 애플리케이션에서 Bing Web Search API를 사용하는 것이 좋습니다. 애플리케이션이 특정 형식의 온라인 콘텐츠를 검색하는 경우 아래 검색 API 중 하나를 사용하는 것이 좋습니다.

## <a name="content-specific-bing-search-apis"></a>콘텐츠 관련 Bing Search API

다음 Bing Search API는 이미지, 뉴스, 로컬 비즈니스 및 비디오 등 웹에서 특정 콘텐츠를 반환합니다.

| Bing API | Description |
| -- | -- |
| [Entity Search](../Bing-Entities-Search/overview.md) | Bing Entity Search API는 사람, 장소 또는 사물일 수 있는 엔터티를 포함하는 검색 결과를 반환합니다. 쿼리에 따라 API는 검색 쿼리를 충족하는 하나 이상의 엔터티를 반환합니다. 검색 쿼리에는 주목할 만한 개인, 지역 업체, 명소, 목적지 등이 포함될 수 있습니다. |
| [Image Search](../Bing-Image-Search/overview.md) | Bing Image Search API를 사용하면 [Bing.com/images](https://www.Bing.com/images)와 유사한 고품질의 정적 이미지와 애니메이션 이미지를 검색하고 찾을 수 있습니다. 크기, 색, 라이선스 및 최신 여부 등의 특성으로 이미지를 포함하거나 제외하도록 검색을 구체화할 수 있습니다. 또한 최신 트렌드의 이미지를 검색하고, 이미지를 업로드하여 이미지에 대한 인사이트를 얻고, 썸네일 미리 보기를 표시할 수 있습니다. |
| [News Search](../Bing-News-Search/search-the-web.md) | Bing News Search API를 사용하면 [Bing.com/news](https://www.Bing.com/news)와 유사한 뉴스 기사를 찾을 수 있습니다. API는 여러 소스 또는 특정 도메인의 뉴스 기사를 반환합니다. 범주별로 검색하여 최신 트렌드 문서, 주요 기사 및 헤드라인을 얻을 수 있습니다. |
| [Video Search](../Bing-Video-Search/overview.md) | Bing Video Search API를 사용하면 웹에서 비디오를 찾을 수 있습니다. 최신 트렌드의 비디오, 관련 콘텐츠 및 썸네일 미리 보기를 얻을 수 있습니다. |
| [Visual Search](../Bing-visual-search/overview.md) | 이미지를 업로드하거나 URL을 사용하여 시각적으로 유사한 제품, 이미지 및 관련 검색과 같은 유용한 정보를 얻을 수 있습니다. |
 [Local Business Search](../bing-local-business-search/overview.md) | Bing Local Business Search API를 사용하면 애플리케이션에서 검색 쿼리를 기반으로 로컬 비즈니스에 대한 정보를 찾을 수 있습니다. |

## <a name="the-bing-custom-search-api"></a>Bing Custom Search API

[Bing Custom Search API](../Bing-Custom-Search/overview.md)를 사용하여 사용자 지정 검색 인스턴스를 생성하면 관심 있는 콘텐츠와 항목에만 집중한 검색 환경을 만들 수 있습니다. 예를 들어 Bing에서 검색할 도메인, 웹 사이트, 특정 웹 페이지를 지정한 후 Bing Custom Search는 해당 특정 콘텐츠에 맞게 결과를 조정합니다. Bing Custom Autosuggest, Image 및 Video Search API를 통합하여 검색 환경을 더욱 사용자 지정할 수 있습니다.

## <a name="additional-bing-search-apis"></a>추가 Bing Search API

다음 Bing Search API를 사용하면 다른 Bing Search API와 결합하여 검색 환경을 개선할 수 있습니다.

| API | 설명 |
| -- | -- |
| [Bing Autosuggest](../Bing-Autosuggest/get-suggested-search-terms.md) | Bing Autosuggest API로 추천 검색어를 실시간으로 반환하여 애플리케이션의 검색 환경을 개선합니다.  |
| [Bing Statistics](bing-web-stats.md) | Bing Statistics는 애플리케이션에서 사용하는 Bing Search API에 대한 분석을 제공합니다. 일부 제공되는 분석에는 호출량, 주요 쿼리 문자열 및 지리적 분포가 포함됩니다. |

## <a name="next-steps"></a>다음 단계

* Bing Search API [가격 책정 세부 정보](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/)
* [Bing 사용 및 표시 요구 사항](./use-display-requirements.md)에서는 Bing 검색 API를 통해 획득한 콘텐츠와 정보의 허용 가능한 용도를 지정하고 있습니다.