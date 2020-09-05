---
title: 앱 스키마 정의
description: LUIS 앱은 또는에 표시 되며 `.json` `.lu` 모든 의도, 엔터티, 예제 길이 발언, 기능 및 설정을 포함 합니다.
ms.topic: reference
ms.date: 08/22/2020
ms.openlocfilehash: 816a6c50129f37a55ab3dba72319358e832a6b8b
ms.sourcegitcommit: afa1411c3fb2084cccc4262860aab4f0b5c994ef
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2020
ms.locfileid: "88756791"
---
# <a name="app-schema-definition"></a>앱 스키마 정의

LUIS 앱은 또는에 표시 되며 `.json` `.lu` 모든 의도, 엔터티, 예제 길이 발언, 기능 및 설정을 포함 합니다.

## <a name="format"></a>서식

앱을 가져오고 내보낼 때 또는 중 하나를 선택 `.json` `.lu` 합니다.

|서식|정보|
|--|--|
|`.json`| 표준 프로그래밍 형식|
|`.lu`|Bot Framework의 [Bot Builder 도구](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Ludown/docs/lu-file-format.md)에서 지원 됩니다.|

## <a name="version-7x"></a>버전 4.x

* 버전 4.x로 이동 하면 엔터티가 중첩 된 기계 학습 엔터티로 표시 됩니다.
* 다음 제작 Api의 속성을 사용 하 여 중첩 된 machine learning 엔터티를 제작할 수 `enableNestedChildren` 있습니다.
    * [레이블 추가](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c08)
    * [일괄 처리 레이블 추가](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c09)
    * [레이블 검토](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c0a)
    * [엔터티에 대 한 끝점 쿼리 제안](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c2e)
    * [의도에 대 한 끝점 쿼리 제안](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c2d)

```json
{
  "luis_schema_version": "7.0.0",
  "intents": [
    {
      "name": "None",
      "features": []
    }
  ],
  "entities": [],
  "hierarchicals": [],
  "composites": [],
  "closedLists": [],
  "prebuiltEntities": [],
  "utterances": [],
  "versionId": "0.1",
  "name": "example-app",
  "desc": "",
  "culture": "en-us",
  "tokenizerVersion": "1.0.0",
  "patternAnyEntities": [],
  "regex_entities": [],
  "phraselists": [
  ],
  "regex_features": [],
  "patterns": [],
  "settings": []
}
```

| element                  | 의견                              |
|--------------------------|--------------------------------------|
| "hierarchicals": [],     | 사용 되지 않는 경우 [machine learning 엔터티](luis-concept-entity-types.md)를 사용 합니다.   |
| "합성": [],        | 사용 되지 않는 경우 [machine learning 엔터티](luis-concept-entity-types.md)를 사용 합니다. [복합 엔터티](reference-entity-composite.md) 참조입니다. |
| "closedLists": [],       | 엔터티의 기능으로 주로 사용 되는 엔터티 참조를 [나열](reference-entity-list.md) 합니다.    |
| ":": "0.1",      | LUIS 앱의 버전입니다.|
| "name": "example-app",   | LUIS 앱의 이름입니다. |
| "desc": "",              | LUIS 앱에 대 한 선택적 설명입니다.  |
| "culture": "en-us",      | 앱의 [언어로](luis-language-support.md) , 미리 작성 된 엔터티, 기계 학습, 토크 등의 기본 기능에 영향을 줍니다.  |
| "tokenizerVersion": "1.0.0", | [토크](luis-language-support.md#tokenization)  |
| "patternAnyEntities": [],   | [Pattern.any 엔터티](reference-entity-pattern-any.md)    |
| "regex_entities": [],    |  [정규식 엔터티](reference-entity-regular-expression.md)   |
| "phraselists": [],       |  [구 목록 (기능)](luis-concept-feature.md#create-a-phrase-list-for-a-concept)   |
| "regex_features": [],    |  사용 되지 않는 경우 [machine learning 엔터티](luis-concept-entity-types.md)를 사용 합니다. |
| "패턴": [],          |  패턴은 [패턴 구문을](reference-pattern-syntax.md) 사용 하 여 [예측 정확도를 향상 시킵니다](luis-concept-patterns.md) .   |
| "settings": []           | [앱 설정](luis-reference-application-settings.md)|

## <a name="version-6x"></a>버전 6.x

* 버전 6.x로 이동 하 여 새 [machine learning 엔터티](reference-entity-machine-learned-entity.md) 를 사용 하 여 엔터티를 나타냅니다.

```json
{
  "luis_schema_version": "6.0.0",
  "intents": [
    {
      "name": "None",
      "features": []
    }
  ],
  "entities": [],
  "hierarchicals": [],
  "composites": [],
  "closedLists": [],
  "prebuiltEntities": [],
  "utterances": [],
  "versionId": "0.1",
  "name": "example-app",
  "desc": "",
  "culture": "en-us",
  "tokenizerVersion": "1.0.0",
  "patternAnyEntities": [],
  "regex_entities": [],
  "phraselists": [],
  "regex_features": [],
  "patterns": [],
  "settings": []
}
```

## <a name="version-4x"></a>버전 4.x

```json
{
  "luis_schema_version": "4.0.0",
  "versionId": "0.1",
  "name": "example-app",
  "desc": "",
  "culture": "en-us",
  "tokenizerVersion": "1.0.0",
  "intents": [
    {
      "name": "None"
    }
  ],
  "entities": [],
  "composites": [],
  "closedLists": [],
  "patternAnyEntities": [],
  "regex_entities": [],
  "prebuiltEntities": [],
  "model_features": [],
  "regex_features": [],
  "patterns": [],
  "utterances": [],
  "settings": []
}
```

## <a name="next-steps"></a>다음 단계

* [V3 제작 api](luis-migration-authoring-entities.md) 로 마이그레이션