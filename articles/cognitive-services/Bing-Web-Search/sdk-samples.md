---
title: Bing Web Search SDK 샘플
titleSuffix: Azure Cognitive Services
description: Bing Web Search SDK를 사용하여 Python, Node.js, C# 또는 Java 애플리케이션에 검색 기능을 추가합니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: sample
ms.date: 05/15/2019
ms.author: aahi
ms.openlocfilehash: 36719fc8370c04e9c2d01422536502f90e124c12
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "82735252"
---
# <a name="bing-web-search-sdk-samples"></a>Bing Web Search SDK 샘플

Bing Web Search SDK는 Python, Node.js, C# 및 Java에서 사용할 수 있습니다. 코드 샘플, 필수 구성 요소 및 빌드 지침은 GitHub에서 제공됩니다. 적용되는 시나리오는 다음과 같습니다.

* 한 단어를 쿼리하고 웹 페이지, 이미지, 뉴스 기사 및 비디오에 대한 첫 번째 결과의 이름과 URL을 출력합니다.
* 구를 쿼리하고, 결과 수를 확인한 다음, 첫 번째 결과의 이름과 URL을 출력합니다.
* `news`로 설정된 응답 필터를 사용하여 검색 용어를 쿼리하고, 뉴스 결과의 세부 정보를 출력합니다.
* `answerCount` 및 `promote` 매개 변수를 사용하여 검색 용어를 쿼리한 다음, 결과의 세부 정보를 출력합니다.

## <a name="sdks-and-libraries"></a>SDK 및 라이브러리

다음 링크를 사용하여 기본 설정 언어용 SDK에 액세스하세요.

* [Python 샘플](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples) 시작
  * 정의 및 종속성은 [Python 라이브러리](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-search-websearch)도 참조하세요.
* [Node.js 샘플](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples) 시작
  * [Node.js Web Search](https://github.com/Azure/azure-sdk-for-node/tree/master/lib/services/cognitiveServicesWebSearch)도 참조하세요.
* [.NET 샘플](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7) 시작
  * [NuGet 패키지](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.WebSearch/1.2.0)
  * 정의 및 종속성은 [.NET 라이브러리](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Search.BingWebSearch)도 참조하세요.
* [Java 샘플](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples) 시작
  * 정의 및 종속성은 [Java 라이브러리](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master/Search/BingWebSearch) 도 참조하세요.
