---
title: LUIS 앱 테스트
titleSuffix: Azure Cognitive Services
description: 테스트는 LUIS에 샘플 발화를 제공하고 LUIS에서 인식한 의도 및 엔터티의 응답을 가져오는 프로세스입니다.
manager: nitinme
services: cognitive-services
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/10/2019
ms.openlocfilehash: b33f765e936d7c0db301a5b2fcf38ccaf137f771
ms.sourcegitcommit: 5c136a01bddfccb2cc9f7e7e7741e2cf2651ddbe
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/03/2021
ms.locfileid: "111352509"
---
# <a name="testing-example-utterances-in-luis"></a>LUIS에서 예제 발화 테스트

테스트는 LUIS에 샘플 발화를 제공하고 LUIS에서 인식한 의도 및 엔터티의 응답을 가져오는 프로세스입니다. 

LUIS에서 한 번에 하나의 발화를 대화형으로 테스트하거나 발화 세트를 제공할 수 있습니다. 테스트하는 동안 현재 활성 모델의 예측 응답을 게시된 모델의 예측 응답과 비교할 수 있습니다. 

<a name="A-test-score"></a>
<a name="Score-all-intents"></a>
<a name="E-(exponent)-notation"></a>

## <a name="what-is-a-score-in-testing"></a>테스트에서 점수란?
예측 점수에 대해 자세히 알아보려면 [예측 점수](luis-concept-prediction-score.md) 개념을 참조하세요.

## <a name="interactive-testing"></a>대화형 테스트
대화형 테스트는 LUIS 포털의 **테스트** 패널에서 수행됩니다. 발화를 입력하여 의도 및 엔터티를 식별하고 점수를 매기는 방법을 확인할 수 있습니다. LUIS가 테스트 창의 발화에서 의도 및 엔터티를 예상대로 예측하지 않으면 발화를 **의도** 페이지에 새 발화로 복사합니다. 그런 다음, 엔터티에 대한 해당 발화 부분에 레이블을 지정하고 LUIS를 학습시킵니다. 

## <a name="batch-testing"></a>일괄 테스트
한 번에 둘 이상의 발화를 테스트하는 경우, [일괄 테스트](./luis-how-to-batch-test.md)를 참조하세요.

## <a name="endpoint-testing"></a>엔드포인트 테스트
최대 두 개의 앱 버전과 함께 [엔드포인트](luis-glossary.md#endpoint)를 사용하여 테스트할 수 있습니다. **프로덕션** 엔드포인트로 설정된 기본 또는 라이브 버전 앱을 사용하여 두 번째 버전을 **스테이징** 엔드포인트에 추가합니다. 이 접근법은 세 개의 발화 버전인 [LUIS](luis-reference-regions.md) 웹 사이트 [테스트] 창의 현재 모델 및 두 개의 다른 엔드포인트에 있는 두 개의 버전을 제공합니다. 

모든 엔드포인트 테스트에서는 사용 할당량을 계산합니다. 

## <a name="do-not-log-tests"></a>테스트를 기록하지 않음
엔드포인트에서 테스트할 때 발화를 기록하지 않으려면 `logging=false` 쿼리 문자열 구성을 사용해야 합니다.

## <a name="where-to-find-utterances"></a>발화를 찾을 수 있는 위치
LUIS는 LUIS 포털의 **앱** 목록 페이지 및 LUIS [작성](https://go.microsoft.com/fwlink/?linkid=2092087) API에서 다운로드할 수 있는 쿼리 로그에 모든 기록된 발화를 저장합니다. 

LUIS가 확신하지 않는 모든 발언은 [LUIS](luis-reference-regions.md) 웹 사이트의 **[엔드포인트 발언 검토](luis-how-to-review-endpoint-utterances.md)** 페이지에 나열됩니다. 

## <a name="remember-to-train"></a>학습해야 함
모델을 변경한 후에는 LUIS를 [학습](luis-how-to-train.md)시켜야 합니다. 앱이 학습될 때까지 LUIS 앱의 변경 내용은 테스트에 표시되지 않습니다. 

## <a name="best-practices"></a>모범 사례
[모범 사례](luis-concept-best-practices.md)를 알아봅니다.

## <a name="next-steps"></a>다음 단계

* 발화 [테스트](luis-interactive-test.md)에 대해 자세히 알아봅니다.