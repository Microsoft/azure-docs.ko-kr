---
title: 텍스트 분석 API의 새로운 기능
titleSuffix: Text Analytics - Azure Cognitive Services
description: 이 문서에서는 Azure Cognitive Services Text Analytics의 새로운 릴리스 및 기능에 대 한 정보를 제공 합니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 02/06/2020
ms.author: aahi
ms.openlocfilehash: e3fbff7a5919d4d43656f3112cb24f2017191348
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/08/2020
ms.locfileid: "77086792"
---
# <a name="whats-new-in-the-text-analytics-api"></a>Text Analytics API의 새로운 기능

텍스트 분석 API는 지속적으로 업데이트 됩니다. 최신 개발을 최신 상태로 유지 하기 위해이 문서에서는 새로운 릴리스 및 기능에 대 한 정보를 제공 합니다.

## <a name="named-entity-recognition-v3-public-preview---february-2020"></a>명명 된 엔터티 인식 v3 공개 미리 보기-2 월 2020

이제 추가 엔터티 형식은 텍스트에 있는 일반 및 개인 정보 엔터티 검색을 확장할 때 NER (명명 된 엔터티 인식) v3 공개 미리 보기 서비스에서 사용할 수 있습니다. 이 업데이트에서는 다음을 포함 하는 [모델 버전](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features) `2020-02-01`를 소개 합니다.

* 다음과 같은 일반 엔터티 형식 인식 (영어만 해당):
    * PersonType
    * Product
    * 행사
    * 지정 학적 엔터티 (GPE)를 위치 아래의 하위 형식으로
    * 기술

* 다음 개인 정보 엔터티 형식 인식 (영어만 해당):
    * Person
    * 조직
    * 수량 아래의 하위 형식으로 사용 기간
    * DateTime 아래의 하위 형식으로 날짜
    * Email 
    * 전화 번호 (미국에만 해당)
    * URL
    * IP 주소

> [!div class="nextstepaction"]
> [명명 된 엔터티 인식 v3에 대해 자세히 알아보기](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features)

### <a name="october-2019"></a>2019년 10월

#### <a name="named-entity-recognition-ner"></a>NER(명명된 엔터티 인식)

* 개인 정보 엔터티 형식을 인식 하기 위한 [새 엔드포인트](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/EntitiesRecognitionPii) (영어만 해당)

* [엔터티 인식](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/EntitiesRecognitionGeneral) 및 [엔터티 링크](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/EntitiesLinking)를 위한 별도의 엔드포인트.

* 다음을 포함 하는 [모델 버전](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features) `2019-10-01`
    * 텍스트에 있는 엔터티의 확장 된 검색 및 분류 
    * 다음과 같은 새로운 엔터티 유형을 인식 합니다.
        * 전화 번호
        * IP 주소

엔터티 연결은 영어와 스페인어를 지원 합니다. NER 언어 지원은 엔터티 형식에 따라 달라 집니다.

#### <a name="sentiment-analysis-v3-public-preview"></a>감정 분석 v3 공개 미리 보기

* 감정 분석을 위한 [새 엔드포인트](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/Sentiment) 입니다.
* 다음을 포함 하는 [모델 버전](how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features) `2019-10-01`

    * API의 텍스트 분류 및 점수 매기기의 정확성과 세부 정보에 상당한 기능이 향상 되었습니다.
    * 텍스트의 다른 정서에 대 한 자동 레이블 지정
    * 문서 및 문장 수준에서 분석 및 출력을 감정 합니다. 

영어 (`en`), 일본어 (`ja`)를 지원 합니다. 중국어 간체 (`zh-Hans`), 중국어 번체 (`zh-Hant`), 프랑스어 (`fr`), 이탈리아어 (`it`), 스페인어 (`es`), 네덜란드어 (`nl`), 포르투갈어 (`pt`) 및 독일어 (`de`)를 사용할 수 있으며, `Australia East`, `Central Canada`, `Central US`, `East Asia`, `East US`, `East US 2`등의 지역에서 사용할 수 있습니다.`North Europe``Southeast Asia``South Central US``UK South``West Europe``West US 2` 

> [!div class="nextstepaction"]
> [감정 분석 v 3에 대 한 자세한 정보](how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features)

## <a name="next-steps"></a>다음 단계

* [Text Analytics API란?](overview.md)  
* [사용자 시나리오 예제](text-analytics-user-scenarios.md)
* [감정 분석](how-tos/text-analytics-how-to-sentiment-analysis.md)
* [언어 감지](how-tos/text-analytics-how-to-language-detection.md)
* [엔터티 인식](how-tos/text-analytics-how-to-entity-linking.md)
* [핵심 구 추출](how-tos/text-analytics-how-to-keyword-extraction.md)
