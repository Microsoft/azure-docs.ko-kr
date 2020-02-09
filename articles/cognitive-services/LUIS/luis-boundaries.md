---
title: 제한-LUIS
titleSuffix: Azure Cognitive Services
description: 이 문서에는 Azure Cognitive Services Language Understanding(LUIS)의 알려진 제한이 포함됩니다. LUIS에는 여러 경계 영역이 있습니다. 모델 경계는 LUIS에서 의도, 엔터티 및 기능을 제어합니다. 할당량은 키 형식에 따라 제한됩니다. 키보드 조합은 LUIS 웹 사이트를 제어합니다.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/07/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 0654916b344cf47cf9942b883d62d392c0552979
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73818928"
---
# <a name="boundaries-for-your-luis-model-and-keys"></a>LUIS 모델 및 키에 대한 경계
LUIS에는 여러 경계 영역이 있습니다. 첫 번째는 LUIS에서 의도, 엔터티 및 기능을 제어하는 [모델 경계](#model-boundaries)입니다. 두 번째 영역은 키 유형을 기반으로 하는 [할당량 한도](#key-limits)입니다. 세 번째 경계 영역은 LUIS 웹 사이트를 제어하기 위한 [키보드 조합](#keyboard-controls)입니다. 네 번째 영역은 LUIS 작성 웹 사이트와 LUIS [엔드포인트](luis-reference-regions.md) API 간의 [세계 지역 매핑](luis-glossary.md#endpoint)입니다. 


## <a name="model-boundaries"></a>모델 경계

앱이 LUIS 모델 제한 및 경계를 초과하는 경우 [LUIS 디스패치](luis-concept-enterprise.md#dispatch-tool-and-model) 앱 또는 [LUIS 컨테이너](luis-container-howto.md)를 사용하는 방안을 고려합니다. 

|영역|제한|
|--|:--|
| [앱 이름][luis-get-started-create-app] | *기본 문자 최댓값 |
| 애플리케이션| 500 Azure 제작 리소스 당 응용 프로그램 |
| [일괄 처리 테스트][batch-testing]| 10개 데이터 세트, 데이터 세트당 1000개 발화|
| 명시적 목록 | 애플리케이션당 50개|
| 외부 엔터티 | 제한 없음 |
| [의도][intents]|응용 프로그램당 500:499 사용자 지정 의도 및 필요한 _없음_ 의도입니다.<br>[디스패치 기반](https://aka.ms/dispatch-tool) 애플리케이션에는 해당 디스패치 원본 500개가 있습니다.|
| [목록 엔터티](./luis-concept-entity-types.md) | 부모: 50, 자식: 20,000개 항목 정식 이름은 *기본 문자 최댓값입니다. 동의어 값에는 길이 제한이 없습니다. |
| [컴퓨터에서 학습 한 엔터티 + 역할](./luis-concept-entity-types.md):<br> 합성할<br>쉽게<br>엔터티 역할|100 부모 엔터티 또는 330 엔터티 중 하나는 사용자가 먼저 적중 하는 것을 제한 합니다. 역할은이 경계의 용도에 대 한 엔터티로 계산 됩니다. 예를 들어 두 개의 역할이 있는 복합 엔터티는 1 개의 복합 + 1 단순 + 2 역할 = 4 인 330 엔터티입니다.<br>하위 구성 요소는 최대 5 수준까지 중첩할 수 있습니다.|
|기능으로 서의 모델| 특정 모델에 대 한 설명자 (기능)로 사용할 수 있는 모델의 최대 수를 10 개 모델로 사용할 수 있습니다. 특정 모델에 대 한 설명자 (기능)로 사용 되는 최대 문구 목록 수를 10 개 구 목록으로 표시 합니다.|
| [미리 보기-동적 목록 엔터티](https://aka.ms/luis-api-v3-doc#dynamic-lists-passed-in-at-prediction-time)|2-쿼리 예측 엔드포인트 요청당 ~ 1k 목록|
| [패턴](luis-concept-patterns.md)|애플리케이션당 500개 패턴.<br>패턴의 최대 길이는 400자입니다.<br>패턴당 3개의 Pattern.any 엔터티<br>패턴에 최대 2개의 선택적 중첩 텍스트|
| [Pattern.any](./luis-concept-entity-types.md)|애플리케이션당 100개, 패턴당 3개의 pattern.any 엔터티 |
| [구 목록][phrase-list]|500 구 목록. 교환 가능 하지 않은 phraselist에는 최대 5000 구가 있습니다. 교환 가능한 Phraselist에는 최대 5만 구가 있습니다. 50만 구의 응용 프로그램당 최대 총 문구 수입니다.|
| [미리 빌드된 엔터티](./luis-prebuilt-entities.md) | 제한 없음|
| [정규식 엔터티](./luis-concept-entity-types.md)|20개 엔터티<br>정규식 엔터티 패턴당 최대 500자|
| [역할](luis-concept-roles.md)|애플리케이션당 300개 역할. 엔터티당 10개 역할|
| [Utterance][utterances] | 500자|
| [길이 발언][utterances] | 15000 응용 프로그램당-길이 발언 수에 제한이 없습니다.|
| [버전](luis-concept-version.md)| 응용 프로그램당 100 버전 |
| [버전 이름][luis-how-to-manage-versions] | 영숫자 및 마침표(.)로 제한되는 10자 |

*기본 문자 최댓값은 50자입니다. 

<a name="intent-and-entity-naming"></a>

## <a name="name-uniqueness"></a>이름 고유성

다음 이름 지정 고유성 규칙을 사용 합니다.

다음은 LUIS 앱 내에서 고유 해야 합니다.

* 버전 이름
* 목적은
* 엔터티
* roles

다음은 적용 된 범위 내에서 고유 해야 합니다.

* 구 목록 

## <a name="object-naming"></a>개체 이름 지정

다음 이름에는 다음 문자를 사용 하지 마십시오.

|Object|문자 제외|
|--|--|
|의도, 엔터티 및 역할 이름|`:`<br>`$` <br> `&`|
|버전 이름|`\`<br> `/`<br> `:`<br> `?`<br> `&`<br> `=`<br> `*`<br> `+`<br> `(`<br> `)`<br> `%`<br> `@`<br> `$`<br> `~`<br> `!`<br> `#`|

## <a name="key-usage"></a>키 사용

언어 인식에는 예측 엔드포인트 작성 및 쿼리용으로 하나씩 두 가지 유형의 개별 키가 포함됩니다. 키 유형의 차이에 대해 자세히 알아보려면 [LUIS의 작성 및 쿼리 예측 엔드포인트 키](luis-concept-keys.md)를 참조하세요.

<a name="key-limits"></a>

## <a name="resource-key-limits"></a>리소스 키 제한

리소스 키에는 제작 및 엔드포인트의 제한이 다릅니다. LUIS 예측 쿼리 엔드포인트 키는 엔드포인트 쿼리에만 유효 합니다. 

* 500 Azure 제작 리소스 당 응용 프로그램 

|키|작성|엔드포인트|목적|
|--|--|--|--|
|스타터|100만/월, 5/초|1000/월, 5/초|LUIS 앱 작성|
|F0-무료 계층 |100만/월, 5/초|10000/월, 5/초|LUIS 엔드포인트 쿼리|
|S0-기본 계층|-|50/초|LUIS 엔드포인트 쿼리|
|S0-표준 계층|-|50/초|LUIS 엔드포인트 쿼리|
|[감정 분석 통합](luis-how-to-publish-app.md#enable-sentiment-analysis)|-|-|다른 Azure 리소스를 요구 하지 않고 키 구 데이터 추출을 포함 하 여 감정 정보를 추가 하는 것이 제공 됩니다. |
|[음성 통합](../speech-service/how-to-recognize-intents-from-speech-csharp.md)|-|1000 단위 비용 당 엔드포인트 요청 수|음성 발화를 텍스트 발화로 변환하고 LUIS 결과 반환|

[가격 책정에 대해 자세히 알아보세요.][pricing]

## <a name="keyboard-controls"></a>키보드 제어

|키보드 입력 | 설명 | 
|--|--|
|Control+E|발화 목록에서 토큰과 엔터티 간 전환|

## <a name="website-sign-in-time-period"></a>웹 사이트 로그인 기간

로그인 액세스는 **60분** 동안 가능합니다. 이 기간이 지나면 이 오류가 표시됩니다. 다시 로그인해야 합니다.

[luis-get-started-create-app]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-get-started-create-app
[batch-testing]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-test#batch-testing
[intents]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-intent
[phrase-list]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-feature
[utterances]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-utterance
[luis-how-to-manage-versions]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-manage-versions
[pricing]: https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/
<!-- TBD: fix this link -->
[speech-to-intent-pricing]: https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/
