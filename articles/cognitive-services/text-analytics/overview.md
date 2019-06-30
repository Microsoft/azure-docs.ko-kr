---
title: Text Analytics API란? - 기능 -
titleSuffix: Azure Cognitive Services
description: 감정 분석, 핵심 구문 추출, 언어 감지 및 엔터티 인식을 위해 Azure Cognitive Services의 Text Analytics API를 사용합니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: overview
ms.date: 04/03/2019
ms.author: aahi
ms.openlocfilehash: 7d52585b51af09c430130141c3680b5630f7b95e
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66417282"
---
# <a name="what-is-text-analytics-api"></a>Text Analytics API란?

Text Analytics API는 클라우드 기반 서비스로 원시 텍스트에 대한 고급 자연어 처리를 제공하며, 감성 분석, 핵심 구 추출, 언어 감지 및 엔터티 인식의 네 가지 주요 기능을 포함합니다.

이 API는 개발 프로젝트를 위한 클라우드의 기계 학습 및 AI 알고리즘 모음인 [Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/)의 일부입니다.

> [!VIDEO https://channel9.msdn.com/Shows/AI-Show/Understanding-Text-using-Cognitive-Services/player]

텍스트 분석은 다른 작업을 의미할 수 있지만, Cognitive Services의 Text Analytics API는 아래에 설명된 것처럼 네 가지 유형의 분석을 제공합니다.

## <a name="sentiment-analysis"></a>감정 분석
[감정 분석](how-tos/text-analytics-how-to-sentiment-analysis.md)을 사용하여 원시 텍스트에서 긍정적이거나 부정적인 감정에 대한 단서를 분석하여 고객이 브랜드 또는 주제에 대해 생각하는 것을 파악할 수 있습니다. 이 API는 각 문서에 대해 0과 1 사이의 감점 점수를 반환합니다. 여기서 1이 가장 긍정적인 것입니다.<br /> 분석 모델은 Microsoft의 포괄적인 텍스트 본문 및 자연어 기술을 사용하여 미리 학습됩니다. 이 API는 [선택된 언어](text-analytics-supported-languages.md)에 대해 사용자가 제공하는 원시 텍스트를 분석하고 점수를 매겨 호출 애플리케이션에 직접 결과를 반환할 수 있습니다. [REST](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-V2-1/operations/56f30ceeeda5650db055a3c9) API 또는 [.NET](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/csharp#install-the-nuget-sdk-package) SDK를 사용할 수 있습니다.

## <a name="key-phrase-extraction"></a>핵심 문구 추출
자동으로 [핵심 구를 추출](how-tos/text-analytics-how-to-keyword-extraction.md)하여 요점을 빠르게 파악합니다. 예를 들어 "The food was delicious and there were wonderful staff"라는 입력 텍스트에 대해 이 API는 "food" 및 "wonderful staff"이라는 주요 논점을 반환합니다. 여기에 나오는 [REST](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-V2-1/operations/56f30ceeeda5650db055a3c6) API 또는 [.NET](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/csharp#install-the-nuget-sdk-package) SDK를 사용할 수 있습니다.

## <a name="language-detection"></a>언어 검색
[입력 텍스트를 쓴 언어를 감지](how-tos/text-analytics-how-to-language-detection.md)하고 광범위한 언어, 변형, 방언 및 일부 지역/문화 언어로 요청시 제출된 모든 문서에 대해 단일 언어 코드를 보고할 수 있습니다. 언어 코드가 점수와 쌍을 이루어 점수의 강도를 나타냅니다. [REST](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-V2-1/operations/56f30ceeeda5650db055a3c7) API 또는 [.NET](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/csharp#install-the-nuget-sdk-package) SDK를 사용할 수 있습니다.

## <a name="named-entity-recognition"></a>명명된 엔터티 인식
텍스트의 엔터티를 인물, 장소, 조직, 날짜/시간, 수량, 백분율, 통화 등으로 [식별하고 분류합니다](how-tos/text-analytics-how-to-entity-linking.md). 잘 알려진 엔터티도 인식되고, 웹에서 더 많은 정보에 연결됩니다. [REST](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-V2-1/operations/5ac4251d5b4ccd1554da7634) API를 사용할 수 있습니다.

## <a name="use-containers"></a>컨테이너 사용

[Text Analytics 컨테이너를 사용](how-tos/text-analytics-how-to-install-containers.md)하여 표준화된 Docker 컨테이너를 데이터와 가까이 설치함으로써 핵심 구를 추출하고, 언어를 검색하고, 로컬로 감정을 분석할 수 있습니다.

## <a name="typical-workflow"></a>일반적인 워크플로

간단한 워크플로: 분석을 위해 데이터를 제출하고 코드의 출력을 처리합니다. 분석기는 추가 구성 또는 사용자 지정 없이 있는 그대로 사용됩니다.

1. [액세스 키](how-tos/text-analytics-how-to-access-key.md)를 위해 [등록](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)합니다. 이 키를 각 요청에 대해 전달해야 합니다.

2. 데이터를 구조화되지 않은 원시 텍스트로 포함하는 [요청을 JSON으로 구성](how-tos/text-analytics-how-to-call-api.md#json-schema)합니다.

3. 등록 동안 설정된 엔드포인트에 요청을 게시하고, 감정 분석, 핵심 구 추출, 언어 감지 또는 엔터티 ID 등, 원하는 리소스를 추가합니다.

4. 응답을 스트리밍하거나 로컬로 저장합니다. 요청에 따라, 결과는 감정 점수, 추출된 핵심 구 모음 또는 언어 코드가 됩니다.

출력은 ID를 기준으로 게시한 각 텍스트 문서에 대한 결과를 포함하는 단일 JSON 문서로 반환됩니다. 추후에 결과를 실행 가능한 정보로 분석하거나, 시각화하거나, 분류할 수 있습니다.

데이터는 계정에 저장되지 않습니다. Text Analytics API에서 수행하는 작업은 상태 비저장 작업입니다. 즉, 제공하는 텍스트가 처리된 후 결과가 즉시 반환됩니다.

## <a name="text-analytics-for-multiple-programming-experience-levels"></a>여러 프로그래밍 경험 수준을 위한 Text Analytics

프로그래밍 경험이 많지 않더라도 프로세스에서 Text Analytics API 사용을 시작할 수 있습니다. 이러한 자습서를 통해 API를 사용하여 자신의 경험 수준에 맞는 다양한 방법으로 텍스트를 분석하는 방법을 알아봅니다. 

* 필요한 최소한의 프로그래밍:
    * [Text Analytics API 및 MS Flow를 사용하여 Yammer 그룹의 코멘트에 대한 감정 식별](https://docs.microsoft.com/Yammer/integrate-yammer-with-other-apps/sentiment-analysis-flow-azure?toc=%2F%2Fazure%2Fcognitive-services%2Ftext-analytics%2Ftoc.json&bc=%2F%2Fazure%2Fbread%2Ftoc.json)
    * [Power BI를 Text Analytics API와 통합하여 고객 피드백 분석](tutorials/tutorial-power-bi-key-phrases.md)
* 권장되는 프로그래밍 환경:
    * [Azure Databricks를 사용하여 스트리밍 데이터에 대한 감정 분석](https://docs.microsoft.com/azure/azure-databricks/databricks-sentiment-analysis-cognitive-services?toc=%2F%2Fazure%2Fcognitive-services%2Ftext-analytics%2Ftoc.json&bc=%2F%2Fazure%2Fbread%2Ftoc.json)
    * [Flask를 빌드하여 텍스트 번역, 감정 분석 및 음성 합성](https://docs.microsoft.com/azure/cognitive-services/translator/tutorial-build-flask-app-translation-synthesis?toc=%2F%2Fazure%2Fcognitive-services%2Ftext-analytics%2Ftoc.json&bc=%2F%2Fazure%2Fbread%2Ftoc.json)


<a name="supported-languages"></a>

## <a name="supported-languages"></a>지원되는 언어

이 섹션은 검색 가능성을 높이기 위해 별도 문서로 이동되었습니다. 이 콘텐츠에 대해서는 [Text Analytics API의 지원되는 언어](text-analytics-supported-languages.md)를 참조하세요.

<a name="data-limits"></a>

## <a name="data-limits"></a>데이터 제한

모든 Text Analytics API 엔드포인트는 원시 텍스트 데이터를 수락합니다. 현재 제한은 각 문서에 대해 5,120자입니다. 더 큰 문서를 분석해야 할 경우 더 작은 청크로 분리하면 됩니다. 여전히 더 높은 한도가 필요하면 [저희에게 문의](https://azure.microsoft.com/overview/sales-number/)하세요. 그러면 해결해 드릴 수 있습니다.

| 제한 | 값 |
|------------------------|---------------|
| 단일 문서의 최대 크기 | [`StringInfo.LengthInTextElements`](https://docs.microsoft.com/dotnet/api/system.globalization.stringinfo.lengthintextelements)로 측정해서 5,120자입니다. |
| 전체 요청의 최대 크기 | 1MB |
| 요청의 최대 문서 수 | 1,000개 문서 |

속도는 초당 100개 요청 및 분당 1,000개 요청으로 제한됩니다. 단일 호출에서 많은 수의 문서를 제출할 수 있습니다(최대 1,000개 문서).

## <a name="unicode-encoding"></a>유니코드 인코딩

Text Analytics API는 텍스트 표현 및 문자 수 계산에 유니코드 인코딩을 사용합니다. 측정 가능한 문자 수 차이 없이 UTF-8 및 UTF-16으로 요청을 제출할 수 있습니다. 유니코드 코드 포인트는 문자 길이에 대한 추론으로 사용되며, 텍스트 분석 데이터 제한을 위해 동일한 것으로 간주됩니다. [`StringInfo.LengthInTextElements`](https://docs.microsoft.com/dotnet/api/system.globalization.stringinfo.lengthintextelements)를 사용하여 문자 수를 확인하는 경우 데이터 크기 측정에 사용하는 것과 동일한 방법을 사용합니다.

## <a name="next-steps"></a>다음 단계

+ 액세스 키를 얻기 위해 [등록](how-tos/text-analytics-how-to-signup.md)하고 [API 호출](how-tos/text-analytics-how-to-call-api.md) 단계를 검토합니다.

+ [빠른 시작](quickstarts/csharp.md)에서 C#으로 작성된 REST API를 연습할 수 있습니다. 최소의 코드로 텍스트를 제출하고, 분석을 선택하고, 결과를 보는 방법을 알아봅니다. 원할 경우, 대신 [Python 빠른 시작](quickstarts/python.md)부터 시작할 수 있습니다.

+ Azure Databricks를 사용하여 이 [감정 분석 자습서](https://docs.microsoft.com/azure/azure-databricks/databricks-sentiment-analysis-cognitive-services)를 좀 더 면밀히 살펴보세요.

+ [외부 및 커뮤니티 콘텐츠 페이지](text-analytics-resource-external-community.md)에서 다른 도구 및 기술과 함께 Text Analytics API를 사용하는 방법을 보여 주는 블로그 게시물 및 비디오 목록을 확인하세요.
