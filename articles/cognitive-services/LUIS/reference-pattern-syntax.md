---
title: 패턴 구문 참조 - LUIS
description: LUIS(Language Understanding) 앱에서 사용자 발화의 키 데이터를 추출하는 엔터티를 만듭니다. 추출된 데이터는 클라이언트 애플리케이션에서 사용됩니다.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 04/14/2020
ms.openlocfilehash: 3caccd6766226ce68b371856b081b052c1033f71
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "91542192"
---
# <a name="pattern-syntax"></a>패턴 구문

패턴 구문은 발언의 템플릿입니다. 템플릿에는 무시하려는 단어 및 [문장 부호](luis-reference-application-settings.md#punctuation-normalization) 뿐만 아니라 일치시키려는 단어 및 엔터티도 포함되어야 합니다. 정규식은 **아닙니다**.

> [!CAUTION]
> 패턴에는 하위 엔터티가 아닌 기계 학습 엔터티 부모만 포함됩니다.

패턴의 엔터티는 중괄호 `{}`로 묶입니다. 패턴은 엔터티 및 역할이 있는 엔터티를 포함할 수 있습니다. [Pattern.any](luis-concept-entity-types.md#patternany-entity)는 패턴에만 사용되는 엔터티입니다.

패턴 구문에서는 다음 구문을 지원합니다.

|함수|구문|중첩 수준|예제|
|--|--|--|--|
|엔터티| {} - 중괄호|2|Where is form {entity-name}?|
|선택적|[] - 대괄호<BR><BR>선택 항목 및 그룹화 조합의 중첩 수준은 3수준으로 제한됩니다. |2|The question mark is optional [?]|
|그룹화|() - 괄호|2|is (a \| b)|
|또는| \| - 세로 막대(파이프)<br><br>한 그룹에서 세로 막대(Or)는 2개로 제한됩니다. |-|Where is form ({form-name-short} &#x7c; {form-name-long} &#x7c; {form-number})|
|발화의 시작 및/또는 끝|^ - 캐럿|-|^발화 시작<br>발화 완료^<br>^전체 발화에서 {number} 엔터티와 엄격한 리터럴 일치 항목^|

## <a name="nesting-syntax-in-patterns"></a>패턴의 중첩 구문

대괄호를 사용하는 **선택적** 구문은 두 수준을 중첩할 수 있습니다. 예: `[[this]is] a new form` 이 예는 다음 발화를 허용합니다.

|중첩된 선택적 발화 예|설명|
|--|--|
|this is a new form|패턴의 모든 단어와 일치|
|is a new form|패턴의 외부 선택적 단어 및 비선택적 단어와 일치|
|a new form|필수 단어만 일치|

괄호를 사용하는 **그룹화** 구문은 두 수준을 중첩할 수 있습니다. 예: `(({Entity1.RoleName1} | {Entity1.RoleName2} ) | {Entity2} )` 이 기능을 사용하면 세 가지 엔터티를 일치시킬 수 있습니다.

Entity1이 출발지(Seattle) 및 도착지(Cairo)와 같은 역할이 있는 위치이고 Entity2가 목록 엔터티에서 알려진 빌딩 이름(RedWest-C)인 경우 다음 발화는 이 패턴에 매핑됩니다.

|중첩된 그룹화 발화 예|설명|
|--|--|
|RedWest-C|외부 그룹화 엔터티와 일치|
|시애틀|내부 그룹화 엔터티 중 하나와 일치|
|Cairo|내부 그룹화 엔터티 중 하나와 일치|

## <a name="nesting-limits-for-groups-with-optional-syntax"></a>선택적 구문이 포함된 그룹에 대한 중첩 제한

**선택적** 구문이 포함된 **그룹화** 조합의 중첩 수준은 3수준으로 제한됩니다.

|허용|예제|
|--|--|
|예|( [ ( test1 &#x7c; test2 ) ] &#x7c; test3 )|
|예|( [ ( [ test1 ] &#x7c; test2 ) ] &#x7c; test3 )|

## <a name="nesting-limits-for-groups-with-or-ing-syntax"></a>or-ing 구문이 포함된 그룹화에 대한 중첩 제한

**or-ing** 구문이 포함된 **그룹화** 조합에서 파이프는 2개로 제한됩니다.

|허용|예제|
|--|--|
|예|( test1 &#x7c; test2 &#x7c; ( test3 &#x7c; test4 ) )|
|예|( test1 &#x7c; test2 &#x7c; test3 &#x7c; ( test4 &#x7c; test5 ) ) |

## <a name="syntax-to-add-an-entity-to-a-pattern-template"></a>패턴 템플릿에 엔터티를 추가하는 구문
패턴 템플릿에 엔터티를 추가하려면 엔터티 이름을 중괄호로 묶습니다(예: `Who does {Employee} manage?`).

|엔터티 사용 패턴|
|--|
|`Who does {Employee} manage?`|

## <a name="syntax-to-add-an-entity-and-role-to-a-pattern-template"></a>패턴 템플릿에 엔터티 및 역할을 추가하는 구문
엔터티 역할은 엔터티 이름에 콜론을 입력하고 역할 이름을 입력하여 `{entity:role}`로 표시합니다. 패턴 템플릿에 역할이 있는 엔터티를 추가하려면 엔터티 이름과 역할 이름을 중괄호로 묶습니다(예: `Book a ticket from {Location:Origin} to {Location:Destination}`).

|엔터티 역할을 사용한 패턴|
|--|
|`Book a ticket from {Location:Origin} to {Location:Destination}`|

## <a name="syntax-to-add-a-patternany-to-pattern-template"></a>패턴 템플릿에 pattern.any를 추가하는 구문
Pattern.any 엔터티를 사용하여 다양한 길이의 엔터티를 패턴에 추가할 수 있습니다. 패턴 템플릿을 따르기만 하면 pattern.any 길이에는 제한이 없습니다.

**Pattern.any** 엔터티를 패턴 템플릿에 추가하려면 Pattern.any 엔터티를 중괄호로 묶습니다(예: `How much does {Booktitle} cost and what format is it available in?`).

|Pattern.any 엔터티 사용 패턴|
|--|
|`How much does {Booktitle} cost and what format is it available in?`|

|패턴의 책 제목|
|--|
|**steal this 책** 가격은 얼마이며 어떤 형식으로 사용할 수 있나요?|
|**ask** 가격은 얼마이며 어떤 형식으로 사용할 수 있나요?|
|**The Curious Incident of the Dog in the Night-Time** 가격은 얼마이며 어떤 형식으로 사용할 수 있나요?|

LUIS는 Pattern.any 엔터티에 따라 책 제목이 끝나는 위치를 알 수 있기 때문에 책 제목의 단어는 LUIS에 혼동을 주지 않습니다.

## <a name="explicit-lists"></a>명시적 목록

작성 API를 통해 [명시적 목록](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5ade550bd5b81c209ce2e5a8)을 만들어 다음의 경우 예외를 허용합니다.

* 패턴에 [Pattern.any](luis-concept-entity-types.md#patternany-entity)가 포함된 경우
* 해당 패턴 구문을 사용하면 발화에 따라 잘못된 엔터티 추출이 발생할 수 있습니다.

예를 들어, 패턴에 선택적 구문 `[]`와 엔터티 구문 `{}`가 데이터를 잘못 추출하는 방식으로 결합되어 있다고 가정해 봅니다.

패턴 `[find] email about {subject} [from {person}]'을 고려해 봅니다.

다음 발언에서 **subject** 및 **person** 엔터티는 올바르게 추출되고 올바르지 않게 추출됩니다.

|발화|엔터티|올바른 추출|
|--|--|:--:|
|email about dogs from Chris|subject=dogs<br>person=Chris|✔|
|email about the man from La Mancha|subject=the man<br>person=La Mancha|X|

위의 표에서 제목은 `the man from La Mancha`(책 제목)이지만 제목에 선택적 단어인 `from`이 포함되어 있으므로 제목이 잘못 예측됩니다.

패턴에서 이러한 예외를 해결하려면 [명시적 목록에 대한 API 작성](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5ade550bd5b81c209ce2e5a8)을 사용하여 {subject} 엔터티에 대한 명시적 목록 일치로 `the man from la mancha`를 추가합니다.

## <a name="syntax-to-mark-optional-text-in-a-template-utterance"></a>템플릿 발언에서 선택적 텍스트를 표시하는 구문
정규식 대괄호 구문 `[]`을 사용하여 발언의 선택적 텍스트에 표시합니다. 선택적 텍스트는 대괄호에 최대 2개의 중괄호를 중첩할 수 있습니다.

|선택적 텍스트 사용 패턴|의미|
|--|--|
|`[find] email about {subject} [from {person}]`|`find` 및 `from {person}`은 선택 사항임|
|`도와주실 수 있으세요[?]|문장 부호 표시는 선택 사항임|

문장 부호 표시(`?`, `!`, `.`)는 무시해야 하며 패턴에 대괄호 구문을 사용하여 무시해야 합니다.

## <a name="next-steps"></a>다음 단계

패턴에 대해 알아봅니다.

* [패턴을 추가하는 방법](luis-how-to-model-intent-pattern.md)
* [pattern.any 엔터티를 추가하는 방법](luis-how-to-add-entities.md#add-a-patternany-entity)
* [패턴 개념](luis-concept-patterns.md)

.json 응답에서 [감정](luis-reference-prebuilt-sentiment.md)이 반환되는 방식을 이해합니다.