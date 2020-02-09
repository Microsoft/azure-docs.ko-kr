---
title: Text Analytics REST API를 사용 하 여 핵심 문구 추출
titleSuffix: Azure Cognitive Services
description: Azure Cognitive Services에서 Text Analytics REST API를 사용 하 여 핵심 문구를 추출 하는 방법입니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 07/29/2019
ms.author: raymondl
ms.openlocfilehash: ec5ff756d7e732430675676868bc754627a2a4a1
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72429032"
---
# <a name="example-how-to-extract-key-phrases-using-text-analytics"></a>예: Text Analytics을 사용 하 여 키 구를 추출 하는 방법

[핵심 구 추출 API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c6)는 구조화되지 않은 텍스트를 평가하고 각 JSON 문서에 대해 핵심 구 목록을 반환합니다.

이 기능은 문서 컬렉션에서 주요 지점을 빠르게 식별해야 하는 경우에 유용합니다. 예를 들어 "음식이 맛있고 훌륭한 직원이 있었습니다"라는 입력 텍스트가 제공되면 서비스에서 "음식" 및 "훌륭한 직원"이라는 주요 논점을 반환합니다.

자세한 내용은 [지원되는 언어](../text-analytics-supported-languages.md)를 참조하세요.

> [!TIP]
> Text Analytics는 핵심 구 추출을 위한 Linux 기반 Docker 컨테이너 이미지도 제공하므로 데이터와 가까이 [Text Analytics 컨테이너를 설치하고 실행](text-analytics-how-to-install-containers.md)할 수 있습니다.

## <a name="preparation"></a>준비

핵심 구 추출은 사용할 텍스트를 더 많이 제공할 때 가장 적합합니다. 이는 작은 양의 텍스트에서 더 효율적으로 수행되는 감정 분석과는 반대입니다. 두 작업에서 최상의 결과를 얻으려면 이에 따라 입력을 다시 구성하는 것이 좋습니다.

ID, 텍스트, 언어 형식의 JSON 문서가 있어야 합니다.

문서 크기는 문서 당 5120 자이 하 여야 하 고 컬렉션 당 최대 1000 개 항목 (Id)을 사용할 수 있습니다. 컬렉션은 요청 본문에 제출됩니다. 다음 예제는 핵심 구 추출에 제출할 수 있는 콘텐츠에 대한 설명입니다.

```json
    {
        "documents": [
            {
                "language": "en",
                "id": "1",
                "text": "We love this trail and make the trip every year. The views are breathtaking and well worth the hike!"
            },
            {
                "language": "en",
                "id": "2",
                "text": "Poorly marked trails! I thought we were goners. Worst hike ever."
            },
            {
                "language": "en",
                "id": "3",
                "text": "Everyone in my family liked the trail but thought it was too challenging for the less athletic among us. Not necessarily recommended for small children."
            },
            {
                "language": "en",
                "id": "4",
                "text": "It was foggy so we missed the spectacular views, but the trail was ok. Worth checking out if you are in the area."
            },
            {
                "language": "en",
                "id": "5",
                "text": "This is my favorite trail. It has beautiful views and many places to stop and rest"
            }
        ]
    }
```

## <a name="step-1-structure-the-request"></a>1단계: 요청 구성

요청 정의에 대 한 자세한 내용은 [텍스트 분석 API 호출 하는 방법](text-analytics-how-to-call-api.md)을 참조 하세요. 편의상 다음 사항을 다시 설명합니다.

+ **POST** 요청을 만듭니다. 이 요청에 대 한 API 설명서를 검토 합니다. [핵심 구 api](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c6).

+ Azure의 Text Analytics 리소스 또는 인스턴스화된 [Text Analytics 컨테이너](text-analytics-how-to-install-containers.md)를 사용 하 여 키 구 추출에 대 한 HTTP 엔드포인트을 설정 합니다. URL에 `/text/analytics/v2.1/keyPhrases`를 포함해야 합니다. 예: `https://<your-custom-subdomain>.api.cognitiveservices.azure.com/text/analytics/v2.1/keyPhrases`.

+ Text Analytics 작업에 대한 [액세스 키](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource)가 포함되도록 요청 헤더를 설정합니다.

+ 이 분석을 위해 준비한 JSON 문서 컬렉션을 요청 본문에 제공합니다.

> [!Tip]
> [Postman](text-analytics-how-to-call-api.md)을 사용하거나 [설명서](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c6)에서 **API 테스트 콘솔**을 열어 요청을 구성하고, 서비스에 POST합니다.

## <a name="step-2-post-the-request"></a>2단계: 요청 게시

요청을 받으면 분석이 수행됩니다. 분당 또는 초당 보낼 수 있는 요청의 크기 및 수에 대 한 자세한 내용은 개요의 [데이터 제한](../overview.md#data-limits) 섹션을 참조 하세요.

상태 비저장 서비스임에 유의하세요. 계정에는 아무 데이터도 저장되지 않습니다. 결과는 응답으로 즉시 반환됩니다.

## <a name="step-3-view-results"></a>3단계: 결과 보기

모든 POST 요청에서는 ID 및 감지된 속성이 있는 JSON 형식의 응답을 반환합니다. 반환 된 키 구의 순서는 내부적으로 모델에 의해 결정 됩니다.

출력은 즉시 반환됩니다. JSON을 승인하는 애플리케이션으로 결과를 스트림하거나 로컬 시스템의 파일에 출력을 저장하고, 데이터를 정렬, 검색 및 조작할 수 있는 애플리케이션으로 가져올 수 있습니다.

핵심 구 추출에 대한 출력 예제는 다음과 같습니다.

```json
    {
        "documents": [
            {
                "keyPhrases": [
                    "year",
                    "trail",
                    "trip",
                    "views"
                ],
                "id": "1"
            },
            {
                "keyPhrases": [
                    "marked trails",
                    "Worst hike",
                    "goners"
                ],
                "id": "2"
            },
            {
                "keyPhrases": [
                    "trail",
                    "small children",
                    "family"
                ],
                "id": "3"
            },
            {
                "keyPhrases": [
                    "spectacular views",
                    "trail",
                    "area"
                ],
                "id": "4"
            },
            {
                "keyPhrases": [
                    "places",
                    "beautiful views",
                    "favorite trail"
                ],
                "id": "5"
            }
        ],
        "errors": []
    }
```

앞에서 설명한 것 처럼, 분석기는 중요 하지 않은 단어를 찾아서 삭제 하 고, 문장의 제목이 나 개체에 표시 되는 단일 용어나 구를 유지 합니다.

## <a name="summary"></a>요약

이 문서에서는 Cognitive Services에서 Text Analytics를 사용 하 여 핵심 문구 추출에 대 한 개념과 워크플로를 배웠습니다. 요약하면 다음과 같습니다.

+ [핵심 구 추출 API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c6)는 선택한 언어로 사용할 수 있습니다.
+ 요청 본문의 JSON 문서에는 ID, 텍스트 및 언어 코드가 포함됩니다.
+ POST 요청은 구독에 유효한 개인 설정 [액세스 키와 엔드포인트](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource)를 사용하여 `/keyphrases` 엔드포인트에 수행되는 요청입니다.
+ 각 문서 ID에 대 한 키워드와 구를 구성 하는 응답 출력은 Excel 및 Power BI Microsoft Office를 포함 하 여 JSON을 허용 하는 모든 앱으로 스트리밍하 고 몇 가지 이름을 지정할 수 있습니다.

## <a name="see-also"></a>참고 항목

 [Text Analytics 개요](../overview.md) [FAQ(질문과 대답)](../text-analytics-resource-faq.md)</br>
 [Text Analytics 제품 페이지](//go.microsoft.com/fwlink/?LinkID=759712)

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [텍스트 분석 API](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-V2-1/operations/56f30ceeeda5650db055a3c6)
