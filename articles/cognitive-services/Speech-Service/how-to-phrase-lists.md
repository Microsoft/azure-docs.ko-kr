---
title: 구 목록-음성 서비스
titleSuffix: Azure Cognitive Services
description: '`PhraseListGrammar`음성 텍스트 인식 결과를 개선 하기 위해 개체를 사용 하 여 문구 목록에 음성 서비스를 제공 하는 방법을 알아봅니다.'
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/04/2020
ms.author: trbye
zone_pivot_groups: programming-languages-speech-services-one-nomore-no-go
ms.custom: devx-track-javascript, devx-track-csharp
ms.openlocfilehash: a71b0ba459d9363f6f479fd88ef10c8209eaf47c
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88918726"
---
# <a name="phrase-lists-for-speech-to-text"></a>음성 텍스트에 대 한 구 목록

음성 서비스에 구 목록을 제공 하 여 음성 인식의 정확도를 향상 시킬 수 있습니다. 구 목록은 오디오 데이터에서 사람의 이름이나 특정 위치처럼 알려진 문구를 식별하는 데 사용됩니다.

예를 들어 "이동" 명령과 음성 될 수 있는 "말"의 가능한 대상이 있는 경우 "앞으로 이동" 항목을 추가할 수 있습니다. 문구를 추가 하면 오디오가 인식 될 때 "이동"이 아니라 "이동" 하는 것으로 인식 될 확률이 높아집니다.

단일 단어 또는 전체 구를 구 목록에 추가할 수 있습니다. 인식 하는 동안 전체 구와 정확히 일치 하는 구가 오디오에 별도의 구로 포함 되는 경우 구 목록의 항목이 사용 됩니다. 구와 정확히 일치하는 항목을 찾을 수 없는 경우 인식이 지원되지 않습니다.

>[!Note]
> 현재 문구 목록은 음성 텍스트에 대 한 영어만 지원 합니다.

## <a name="how-to-use-phrase-lists"></a>구 목록을 사용 하는 방법

아래 샘플에서는 개체를 사용 하 여 구 목록을 작성 하는 방법을 보여 줍니다 `PhraseListGrammar` .

::: zone pivot="programming-language-csharp"

```cs
PhraseListGrammar phraseList = PhraseListGrammar.FromRecognizer(recognizer);
phraseList.AddPhrase("Move to Ward");
phraseList.AddPhrase("Move to Bill");
phraseList.AddPhrase("Move to Ted");
```

::: zone-end

::: zone pivot="programming-language-cpp"

```C++
auto phraselist = PhraseListGrammar::FromRecognizer(recognizer);
phraselist->AddPhrase("Move to Ward");
phraselist->AddPhrase("Move to Bill");
phraselist->AddPhrase("Move to Ted");
```

::: zone-end

::: zone pivot="programming-language-java"

```Java
PhraseListGrammar phraseListGrammar = PhraseListGrammar.fromRecognizer(recognizer);
phraseListGrammar.addPhrase("Move to Ward");
phraseListGrammar.addPhrase("Move to Bill");
phraseListGrammar.addPhrase("Move to Ted");
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
phrase_list_grammar = speechsdk.PhraseListGrammar.from_recognizer(reco)
phrase_list_grammar.addPhrase("Move to Ward")
phrase_list_grammar.addPhrase("Move to Bill")
phrase_list_grammar.addPhrase("Move to Ted")
```

::: zone-end

::: zone pivot="programming-language-javascript"

```JavaScript
var phraseListGrammar = SpeechSDK.PhraseListGrammar.fromRecognizer(reco);
phraseListGrammar.addPhrase("Move to Ward");
phraseListGrammar.addPhrase("Move to Bill");
phraseListGrammar.addPhrase("Move to Ted");
```

::: zone-end

>[!Note]
> 음성 서비스에서 음성을 일치 시키는 데 사용할 수 있는 최대 문구 목록은 1024 구입니다.

`PhraseListGrammar`Clear ()를 호출 하 여와 관련 된 구를 지울 수도 있습니다.

::: zone pivot="programming-language-csharp"

```cs
phraseList.Clear();
```

::: zone-end

::: zone pivot="programming-language-cpp"

```C++
phraselist->Clear();
```

::: zone-end

::: zone pivot="programming-language-java"

```Java
phraseListGrammar.clear();
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
phrase_list_grammar.clear()
```

::: zone-end

::: zone pivot="programming-language-javascript"

```JavaScript
phraseListGrammar.clear();
```

::: zone-end

> [!NOTE]
> 개체에 대 한 변경 내용은 `PhraseListGrammar` 다음 인식에 적용 되거나 음성 서비스에 다시 연결 된 후 적용 됩니다.

## <a name="next-steps"></a>다음 단계

* [Speech SDK 참조 설명서](speech-sdk.md)
