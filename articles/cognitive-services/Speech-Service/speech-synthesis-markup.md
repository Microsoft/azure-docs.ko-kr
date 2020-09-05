---
title: SSML (speech 합성 Markup Language)-Speech service
titleSuffix: Azure Cognitive Services
description: Speech Synthesis Markup Language를 사용하여 텍스트 음성 변환의 발음 및 운율을 제어합니다.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/23/2020
ms.author: trbye
ms.custom: devx-track-javascript, devx-track-csharp
ms.openlocfilehash: d924c019d5ee231f3c9d66a56c4d98857bc89abc
ms.sourcegitcommit: 420c30c760caf5742ba2e71f18cfd7649d1ead8a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89055552"
---
# <a name="improve-synthesis-with-speech-synthesis-markup-language-ssml"></a>SSML (음성 합성 마크업) 언어를 사용 하 여 합성 향상

SSML (Speech 합성 Markup Language)은 텍스트 음성 변환 서비스를 사용 하 여 입력 텍스트가 합성 음성으로 변환 되는 방법을 개발자가 지정할 수 있는 XML 기반 태그 언어입니다. 일반 텍스트와 비교해 서 SSML를 사용 하면 개발자가 피치, 발음, 말하는 요금, 볼륨 및 텍스트 음성 변환의 출력을 세밀 하 게 조정할 수 있습니다. 마침표 뒤에 일시 중지 또는 문장이 끝나는 경우 올바른 intonation를 사용 하는 등의 일반 문장 부호는 자동으로 처리 됩니다.

SSML의 음성 서비스 구현은 World Wide Web 컨소시엄의 [음성 합성 마크업 언어 버전 1.0](https://www.w3.org/TR/speech-synthesis)을 기반으로 합니다.

> [!IMPORTANT]
> 중국어, 일본어 및 한국어 문자는 청구를 위해 두 문자로 계산 됩니다. 자세한 내용은 [가격 책정](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)을 참조하세요.

## <a name="standard-neural-and-custom-voices"></a>표준, 신경망 및 사용자 지정 음성

표준 및 신경망에서 선택 하거나 제품 또는 브랜드에 고유한 사용자 지정 음성을 만드세요. 75 + standard 음성은 45 개 이상의 언어 및 로캘에서 사용할 수 있으며, 5 개의 신경망은 4 개의 언어와 로캘에서 사용할 수 있습니다. 지원되는 언어, 로캘 및 음성(인공신경망 및 표준)의 전체 목록은 [언어 지원](language-support.md)을 참조하세요.

표준, 신경망 및 사용자 지정 음성에 대해 자세히 알아보려면 [텍스트 음성 변환 개요](text-to-speech.md)를 참조 하세요.

## <a name="special-characters"></a>특수 문자

SSML를 사용 하는 동안에는 따옴표, 아포스트로피 및 대괄호와 같은 특수 문자를 이스케이프 처리 해야 합니다. 자세한 내용은 [XML(Extensible Markup Language) (XML) 1.0: 부록 D](https://www.w3.org/TR/xml/#sec-entexpand)를 참조 하세요.

## <a name="supported-ssml-elements"></a>지원 되는 SSML 요소

각 SSML 문서는 SSML 요소 (또는 태그)를 사용 하 여 생성 됩니다. 이러한 요소는 피치, prosody, 볼륨 등을 조정 하는 데 사용 됩니다. 다음 섹션에서는 각 요소를 사용 하는 방법 및 요소가 필요한 경우와 선택 사항인 경우에 대해 자세히 설명 합니다.  

> [!IMPORTANT]
> 특성 값 주위에 큰따옴표를 사용 해야 합니다. 올바른 형식의 유효한 XML에 대 한 표준에서는 특성 값을 큰따옴표로 묶어야 합니다. 예를 들어 `<prosody volume="90">` 는 잘 구성 된 유효한 요소 이지만 `<prosody volume=90>` 는 그렇지 않습니다. SSML에서 따옴표로 묶지 않은 특성 값을 인식 하지 못할 수 있습니다.

## <a name="create-an-ssml-document"></a>SSML 문서 만들기

`speak` 는 루트 요소 이며 모든 SSML 문서에 **필요** 합니다. 요소에는 `speak` 버전, 언어 및 태그 어휘 정의와 같은 중요 한 정보가 포함 되어 있습니다.

**구문**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="string"></speak>
```

**특성**

| attribute | Description | 필수/선택 |
|-----------|-------------|---------------------|
| `version` | 문서 태그를 해석 하는 데 사용 되는 SSML 사양의 버전을 나타냅니다. 현재 버전은 1.0입니다. | 필수 |
| `xml:lang` | 루트 문서의 언어를 지정 합니다. 값에는 소문자, 2 자 언어 코드 (예: `en` ) 또는 언어 코드와 대문자/지역 (예:)이 포함 될 수 있습니다 `en-US` . | 필수 |
| `xmlns` | SSML 문서의 태그 어휘 (요소 형식 및 특성 이름)를 정의 하는 문서에 대 한 URI를 지정 합니다. 현재 URI는 http://www.w3.org/2001/10/synthesis 입니다. | 필수 |

## <a name="choose-a-voice-for-text-to-speech"></a>텍스트 음성 변환에 대 한 음성 선택

`voice`요소가 필요 합니다. 텍스트 음성 변환에 사용 되는 음성을 지정 하는 데 사용 됩니다.

**구문**

```xml
<voice name="string">
    This text will get converted into synthesized speech.
</voice>
```

**특성**

| attribute | Description | 필수/선택 |
|-----------|-------------|---------------------|
| `name` | 텍스트 음성 변환 출력에 사용 되는 음성을 식별 합니다. 지원 되는 음성의 전체 목록은 [언어 지원](language-support.md#text-to-speech)을 참조 하세요. | 필수 |

**예제**

> [!NOTE]
> 이 예제에서는 `en-US-AriaRUS` 음성을 사용 합니다. 지원 되는 음성의 전체 목록은 [언어 지원](language-support.md#text-to-speech)을 참조 하세요.

```XML
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaRUS">
        This is the text that is spoken.
    </voice>
</speak>
```

## <a name="use-multiple-voices"></a>여러 음성 사용

요소 내에서 `speak` 텍스트 음성 변환 출력에 대해 여러 음성을 지정할 수 있습니다. 이러한 음성은 서로 다른 언어를 사용할 수 있습니다. 각 음성에 대해 텍스트를 요소에 래핑해야 합니다 `voice` . 

**특성**

| attribute | Description | 필수/선택 |
|-----------|-------------|---------------------|
| `name` | 텍스트 음성 변환 출력에 사용 되는 음성을 식별 합니다. 지원 되는 음성의 전체 목록은 [언어 지원](language-support.md#text-to-speech)을 참조 하세요. | 필수 |

> [!IMPORTANT]
> 여러 음성은 단어 경계 기능과 호환 되지 않습니다. 여러 음성을 사용 하려면 단어 경계 기능을 사용 하지 않도록 설정 해야 합니다.

### <a name="disable-word-boundary"></a>단어 경계 사용 안 함

Speech SDK 언어에 따라 `"SpeechServiceResponse_Synthesis_WordBoundaryEnabled"` 개체의 인스턴스에서 속성을로 설정 합니다 `false` `SpeechConfig` .

# <a name="c"></a>[C#](#tab/csharp)

자세한 내용은 <a href="https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.setproperty?view=azure-dotnet" target="_blank"> `SetProperty` <span class="docon docon-navigate-external x-hidden-focus"></span></a>를 참조하세요.

```csharp
speechConfig.SetProperty(
    "SpeechServiceResponse_Synthesis_WordBoundaryEnabled", "false");
```

# <a name="c"></a>[C++](#tab/cpp)

자세한 내용은 <a href="https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig#setproperty" target="_blank"> `SetProperty` <span class="docon docon-navigate-external x-hidden-focus"></span></a>를 참조하세요.

```cpp
speechConfig->SetProperty(
    "SpeechServiceResponse_Synthesis_WordBoundaryEnabled", "false");
```

# <a name="java"></a>[Java](#tab/java)

자세한 내용은 <a href="https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig.setproperty?view=azure-java-stable#com_microsoft_cognitiveservices_speech_SpeechConfig_setProperty_String_String_" target="_blank"> `setProperty` <span class="docon docon-navigate-external x-hidden-focus"></span></a>를 참조하세요.

```java
speechConfig.setProperty(
    "SpeechServiceResponse_Synthesis_WordBoundaryEnabled", "false");
```

# <a name="python"></a>[Python](#tab/python)

자세한 내용은 <a href="https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python#set-property-by-name-property-name--str--value--str-" target="_blank"> `set_property_by_name` <span class="docon docon-navigate-external x-hidden-focus"></span></a>를 참조하세요.

```python
speech_config.set_property_by_name(
    "SpeechServiceResponse_Synthesis_WordBoundaryEnabled", "false");
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

자세한 내용은 <a href="https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest#setproperty-string--string-" target="_blank"> `setProperty` <span class="docon docon-navigate-external x-hidden-focus"></span></a>를 참조하세요.

```javascript
speechConfig.setProperty(
    "SpeechServiceResponse_Synthesis_WordBoundaryEnabled", "false");
```

# <a name="objective-c"></a>[Objective-C](#tab/objectivec)

자세한 내용은 <a href="https://docs.microsoft.com/objectivec/cognitive-services/speech/spxspeechconfiguration#setpropertytobyname" target="_blank"> `setPropertyTo` <span class="docon docon-navigate-external x-hidden-focus"></span></a>를 참조하세요.

```objectivec
[speechConfig setPropertyTo:@"false" byName:@"SpeechServiceResponse_Synthesis_WordBoundaryEnabled"];
```

# <a name="swift"></a>[Swift](#tab/swift)

자세한 내용은 <a href="https://docs.microsoft.com/objectivec/cognitive-services/speech/spxspeechconfiguration#setpropertytobyname" target="_blank"> `setPropertyTo` <span class="docon docon-navigate-external x-hidden-focus"></span></a>를 참조하세요.

```swift
speechConfig!.setPropertyTo(
    "false", byName: "SpeechServiceResponse_Synthesis_WordBoundaryEnabled")
```

---

**예제**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaRUS">
        Good morning!
    </voice>
    <voice name="en-US-Guy24kRUS">
        Good morning to you too Aria!
    </voice>
</speak>
```

## <a name="adjust-speaking-styles"></a>말하기 스타일 조정

> [!IMPORTANT]
> 말하는 스타일의 조정은 신경망에만 적용 됩니다.

기본적으로 텍스트 음성 변환 서비스는 표준 및 신경망 모두에 대해 중립 음성 스타일을 사용 하 여 텍스트를 합성 합니다. 신경망을 사용 하면 말하는 스타일을 조정 하 여 cheerfulness, 공감 및 감정을 같은 다양 한 시나리오를 표현 하거나, 요소를 사용 하 여 고객 서비스, newscasting 및 음성 도우미와 같은 다양 한 시나리오에 대해 음성을 최적화할 수 있습니다 `mstts:express-as` . 음성 서비스에 고유한 선택적 요소입니다.

현재 이러한 신경망에 대해 말하는 스타일 조정을 지원 합니다.
* `en-US-AriaNeural`
* `zh-CN-XiaoxiaoNeural`
* `zh-CN-YunyangNeural`

변경 내용은 문장 수준에서 적용 되며 스타일은 음성에 따라 다릅니다. 스타일이 지원 되지 않는 경우 서비스는 기본 중립 말하는 스타일로 음성을 반환 합니다. [Voice LIST API](rest-text-to-speech.md#get-a-list-of-voices)를 통해 각 음성에 대해 지원 되는 스타일을 쿼리할 수 있습니다.

중국어 음성 XiaoxiaoNeural의 경우 사용 사례에 맞게 말하기 스타일의 강도를 추가로 변경할 수 있습니다. 에서 더 강력 하거나 더 부드러운 스타일을 지정 `styledegree` 하 여 음성을 더 표현 하거나 subdued 할 수 있습니다.

**구문**

```xml
<mstts:express-as style="string" styledegree="value"></mstts:express-as>
```
> [!NOTE]
> 현재에서는 `styledegree` XiaoxiaoNeural만 지원 합니다. 

**특성**

| attribute | Description | 필수/선택 |
|-----------|-------------|---------------------|
| `style` | 말하기 스타일을 지정 합니다. 현재 말하는 스타일은 음성 전용입니다. | 신경망의 말하기 스타일을 조정 하는 경우에 필요 합니다. 을 사용 하 `mstts:express-as` 는 경우 스타일을 제공 해야 합니다. 잘못 된 값을 제공 하는 경우이 요소는 무시 됩니다. |
| `styledegree` | 말하기 스타일의 강도를 지정 합니다. **허용**되는 값: 0.01-2 포함 기본값은 미리 정의 된 스타일 강도를 의미 하는 1입니다. 최소 단위는 0.01 이며이로 인해 대상 스타일에 대해 약간의 경향이 있습니다. 값이 2 이면 기본 스타일 농도가 두 배가 됩니다.  | 선택 사항 (현재는 `styledegree` XiaoxiaoNeural만 지원 합니다.)|

이 표를 사용 하 여 각 신경망에 대해 지원 되는 말하는 스타일을 결정 합니다.

| 음성                   | 스타일                     | Description                                                 |
|-------------------------|---------------------------|-------------------------------------------------------------|
| `en-US-AriaNeural`      | `style="newscast-formal"` | 뉴스 배달에 대 한 공식적이 고 신뢰할 수 있는 톤을 나타냅니다. |
|                         | `style="newscast-casual"` | 일반 뉴스 배달에 대 한 다양 한 느낌을 나타냅니다.        |
|                         | `style="customerservice"` | 고객 지원에 대 한 친숙 하 고 유용한 톤을 나타냅니다.  |
|                         | `style="chat"`            | 평범한 톤 및 낮은 톤을 나타냅니다.                         |
|                         | `style="cheerful"`        | 긍정 및 행복 한 톤을 나타냅니다.                         |
|                         | `style="empathetic"`      | 신경쓰지의 의미를 표현 하 고 이해 합니다.               |
| `zh-CN-XiaoxiaoNeural`  | `style="newscast"`        | Narrating news의 공식 및 전문 톤을 나타냅니다. |
|                         | `style="customerservice"` | 고객 지원에 대 한 친숙 하 고 유용한 톤을 나타냅니다.  |
|                         | `style="assistant"`       | 디지털 길잡이에 대 한 웜 및 낮은 톤을 나타냅니다.    |
|                         | `style="chat"`            | Chit-채팅에 대 한 평범한 톤 및 낮은 톤을 나타냅니다.           |
|                         | `style="calm"`            | 말하는 동안 쿨, 수집 및 구성 된 태도를 표현 합니다. 다른 종류의 음성에 비해 톤, 피치, prosody는 훨씬 더 균일 합니다.                                |
|                         | `style="cheerful"`        | 더 높은 피치와 vocal 에너지를 사용 하 여 upbeat 및 톤을 나타냅니다.                         |
|                         | `style="sad"`             | 더 높은 피치, 낮은 강도 및 낮은 vocal 에너지를 사용 하 여 sorrowful 톤을 나타냅니다. 이 emotion의 일반적인 지표는 음성 중에 whimpers 또는 우는입니다.            |
|                         | `style="angry"`           | 고가, 더 높은 강도 및 vocal 에너지를 사용 하 여 화난 및 표정이 톤을 나타냅니다. 스피커가 irate, 실망 및 offended 상태입니다.       |
|                         | `style="fearful"`         | 더 높은 피치, vocal 에너지 및 빠른 속도를 사용 하 여 무서 불안해 톤을 나타냅니다. 스피커가 tenseness 및 uneasiness 상태입니다.                          |
|                         | `style="disgruntled"`     | Disdainful 및 불만이 톤을 나타냅니다. 이 emotion 음성에는 displeasure 및 경 멸가 표시 됩니다.              |
|                         | `style="serious"`         | 엄격한 및 명령 톤을 나타냅니다. 스피커는 stiffer 하 고 회사의 흐름을 통해 훨씬 낮은 수준으로 완화 되는 경우가 많습니다.          |
|                         | `style="affectionate"`    | 고가와 vocal 에너지를 사용 하 여 웜 및 affectionate 톤을 나타냅니다. 스피커가 수신기의 주의 끌어들이고 데 상태입니다. 스피커의 "개성"은 종종 본질적으로 endearing.          |     
|                         | `style="gentle"`          | 낮은 피치와 vocal 에너지를 사용 하 여 가벼운, 처리 완료 후 및 좋은 톤을 나타냅니다.         |   
|                         | `style="lyrical"`         | Melodic 및 sentimental 방식으로 감정을를 표현 합니다.         |   
| `zh-CN-YunyangNeural`   | `style="customerservice"` | 고객 지원에 대 한 친숙 하 고 유용한 톤을 나타냅니다.  | 

**예제**

이 SSML 코드 조각은 요소를 `<mstts:express-as>` 사용 하 여 말하기 스타일을로 변경 하는 방법을 보여 줍니다 `cheerful` .

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis"
       xmlns:mstts="https://www.w3.org/2001/mstts" xml:lang="en-US">
    <voice name="en-US-AriaNeural">
        <mstts:express-as style="cheerful">
            That'd be just amazing!
        </mstts:express-as>
    </voice>
</speak>
```

이 SSML 코드 조각은 특성을 `styledegree` 사용 하 여 XiaoxiaoNeural의 말하기 스타일 강도를 변경 하는 방법을 보여 줍니다.
```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis"
       xmlns:mstts="https://www.w3.org/2001/mstts" xml:lang="zh-CN">
    <voice name="zh-CN-XiaoxiaoNeural">
        <mstts:express-as style="sad" styledegree="2">
            快走吧，路上一定要注意安全，早去早回。
        </mstts:express-as>
    </voice>
</speak>
```

## <a name="add-or-remove-a-breakpause"></a>중단/일시 중지 추가 또는 제거

요소를 사용 `break` 하 여 단어 사이에 일시 중지 (또는 중단)를 삽입 하거나 텍스트 음성 변환 서비스에서 자동으로 추가 된 일시 중지를 방지 합니다.

> [!NOTE]
> 해당 단어나 구에 대 한 합성 된 음성이 자연스럽 게 들리지만이 요소를 사용 하 여 단어 또는 구에 대 한 TTS (텍스트 음성 변환)의 기본 동작을 재정의할 수 있습니다. `strength` `none` 텍스트 음성 변환 서비스에서 자동으로 삽입 하는 운율 적 경계가 중단을 방지 하려면로 설정 합니다.

**구문**

```xml
<break strength="string" />
<break time="string" />
```

**특성**

| attribute | Description | 필수/선택 |
|-----------|-------------|---------------------|
| `strength` | 다음 값 중 하나를 사용 하 여 일시 중지의 상대 기간을 지정 합니다.<ul><li>없음</li><li>x-약함</li><li>약</li><li>보통 (기본값)</li><li>강력</li><li>x-강력한</li></ul> | 선택 사항 |
| `time` | 일시 중지의 절대 기간 (초 또는 밀리초)을 지정 합니다. 유효한 값의 예는 `2s` 및입니다. `500` | 선택 사항 |

| 강도가                      | Description |
|-------------------------------|-------------|
| 없음 또는 제공 된 값이 없는 경우 | 0 밀리초        |
| x-약함                        | 250ms      |
| 약                          | 500ms      |
| 중간                        | 750ms      |
| 강력                        | 1000 밀리초     |
| x-강력한                      | 1250 밀리초     |

**예제**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaNeural">
        Welcome to Microsoft Cognitive Services <break time="100ms" /> Text-to-Speech API.
    </voice>
</speak>
```

## <a name="specify-paragraphs-and-sentences"></a>단락 및 문장 지정

`p` 및 `s` 요소는 각각 단락과 문장을 나타내는 데 사용 됩니다. 이러한 요소가 없으면 텍스트 음성 변환 서비스에서 자동으로 SSML 문서의 구조를 결정 합니다.

요소에는 `p` `audio` ,, `break` `phoneme` , `prosody` , `say-as` `sub` `mstts:express-as` `s` ,, 및 요소가 포함 될 수 있습니다.

요소에는,,,,, `s` `audio` `break` `phoneme` `prosody` `say-as` `mstts:express-as` 및 `sub` 요소가 포함 될 수 있습니다.

**구문**

```XML
<p></p>
<s></s>
```

**예제**

```XML
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaRUS">
        <p>
            <s>Introducing the sentence element.</s>
            <s>Used to mark individual sentences.</s>
        </p>
        <p>
            Another simple paragraph.
            Sentence structure in this paragraph is not explicitly marked.
        </p>
    </voice>
</speak>
```

## <a name="use-phonemes-to-improve-pronunciation"></a>음소를 사용 하 여 발음 향상

`ph`요소는 SSML 문서에서 음성 발음에 사용 됩니다. `ph`요소는 텍스트만 포함할 수 있으며 다른 요소는 포함할 수 없습니다. 항상 사람이 읽을 수 있는 음성을 대체 (fallback)로 제공 합니다.

Phonetic 영문자는 문자, 숫자 또는 문자로 구성 된 전화로 구성 되며, 경우에 따라 조합으로 이루어집니다. 각 전화 음성의 고유한 소리를 설명합니다. 문자는 여러 번의 음성 소리를 나타낼 수 있는 라틴 알파벳과는 대조적입니다. 단어 "사탕" 및 "중단", "c" 문자의 다양 한 발음 또는 고려 문자 조합과 다른 발음 "th" 단어 "thing" 및 "는" 합니다.

**구문**

```XML
<phoneme alphabet="string" ph="string"></phoneme>
```

**특성**

| attribute | Description | 필수/선택 |
|-----------|-------------|---------------------|
| `alphabet` | 특성에서 문자열의 발음을 synthesizing 때 사용할 발음 문자를 지정 합니다 `ph` . 영문자를 지정 하는 문자열은 소문자로 지정 해야 합니다. 지정할 수 있는 알파벳은 다음과 같습니다.<ul><li>`ipa`&ndash; <a href="https://en.wikipedia.org/wiki/International_Phonetic_Alphabet" target="_blank">국제 발음 영문자 <span class="docon docon-navigate-external x-hidden-focus"></span> </a></li><li>`sapi`&ndash; [음성 서비스 발음 영문자](speech-ssml-phonetic-sets.md)</li><li>`ups`&ndash; <a href="https://documentation.help/Microsoft-Speech-Platform-SDK-11/17509a49-cae7-41f5-b61d-07beaae872ea.htm" target="_blank">범용 전화 번호 설정</a></li></ul><br>알파벳은 요소의에만 적용 됩니다. `phoneme` | 선택 사항 |
| `ph` | 요소에 있는 단어의 발음을 지정 하는 전화를 포함 하는 문자열 `phoneme` 입니다. 지정 된 문자열이 인식할 수 없는 휴대폰을 포함 하는 경우 TTS (텍스트 음성 변환) 서비스는 전체 SSML 문서를 거부 하 고 문서에 지정 된 음성 출력을 생성 하지 않습니다. | 음소를 사용 하는 경우 필수입니다. |

**예**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaRUS">
        <phoneme alphabet="ipa" ph="t&#x259;mei&#x325;&#x27E;ou&#x325;"> tomato </phoneme>
    </voice>
</speak>
```

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaRUS">
        <phoneme alphabet="sapi" ph="iy eh n y uw eh s"> en-US </phoneme>
    </voice>
</speak>
```

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaRUS">
        <s>His name is Mike <phoneme alphabet="ups" ph="JH AU"> Zhou </phoneme></s>
    </voice>
</speak>
```

## <a name="use-custom-lexicon-to-improve-pronunciation"></a>사용자 지정 어휘를 사용 하 여 발음 향상

텍스트 음성 변환 서비스가 단어를 정확 하 게 발음 하지 못하는 경우도 있습니다. 예를 들어 회사의 이름 또는 의료 약관입니다. 개발자는 및 태그를 사용 하 여 SSML에서 단일 엔터티를 읽는 방법을 정의할 수 있습니다 `phoneme` `sub` . 그러나 여러 엔터티를 읽는 방법을 정의 해야 하는 경우 태그를 사용 하 여 사용자 지정 어휘를 만들 수 있습니다 `lexicon` .

> [!NOTE]
> 현재 사용자 지정 어휘는 UTF-8 인코딩을 지원 합니다. 

**구문**

```XML
<lexicon uri="string"/>
```

**특성**

| attribute | Description                               | 필수/선택 |
|-----------|-------------------------------------------|---------------------|
| `uri`     | 외부 j 문서의 주소입니다. | 필수 요소.           |

**사용 현황**

여러 엔터티를 읽는 방법을 정의 하려면 .xml 또는. j 파일로 저장 된 사용자 지정 어휘를 만들 수 있습니다. 다음은 샘플 .xml 파일입니다.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<lexicon version="1.0" 
      xmlns="http://www.w3.org/2005/01/pronunciation-lexicon"
      xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 
      xsi:schemaLocation="http://www.w3.org/2005/01/pronunciation-lexicon 
        http://www.w3.org/TR/2007/CR-pronunciation-lexicon-20071212/pls.xsd"
      alphabet="ipa" xml:lang="en-US">
  <lexeme>
    <grapheme>BTW</grapheme> 
    <alias>By the way</alias> 
  </lexeme>
  <lexeme>
    <grapheme> Benigni </grapheme> 
    <phoneme> bɛˈniːnji</phoneme>
  </lexeme>
</lexicon>
```

요소에는 `lexicon` 요소가 하나 이상 포함 되어 있습니다 `lexeme` . 각 `lexeme` 요소는 하나 이상의 `grapheme` 요소와 하나 이상의 `grapheme` , `alias` 및 요소를 포함 `phoneme` 합니다. `grapheme`요소는 <a href="https://www.w3.org/TR/pronunciation-lexicon/#term-Orthography" target="_blank">orthography <span class="docon docon-navigate-external x-hidden-focus"></span> </a>를 설명 하는 텍스트를 포함 합니다. `alias`요소는 머리글자어 또는 축약 된 단어의 발음을 나타내는 데 사용 됩니다. `phoneme`요소는가 어떻게 발음 되는지 설명 하는 텍스트를 제공 `lexeme` 합니다.

사용자 지정 어휘를 사용 하 여 단어의 발음을 직접 설정할 수 없다는 점에 유의 해야 합니다. 머리글자어 또는 축약 된 용어에 대 한 발음을 설정 해야 하는 경우 먼저를 제공 하 고를 `alias` 해당에 연결 합니다 `phoneme` `alias` . 예를 들면 다음과 같습니다.

```xml
  <lexeme>
    <grapheme>Scotland MV</grapheme> 
    <alias>ScotlandMV</alias> 
  </lexeme>
  <lexeme>
    <grapheme>ScotlandMV</grapheme> 
    <phoneme>ˈskɒtlənd.ˈmiːdiəm.weɪv</phoneme>
  </lexeme>
```

> [!IMPORTANT]
> `phoneme`IPA를 사용 하는 경우 요소는 공백을 포함할 수 없습니다.

사용자 지정 어휘 파일에 대 한 자세한 내용은 [j (발음 어휘 사양) 버전 1.0](https://www.w3.org/TR/pronunciation-lexicon/)을 참조 하세요.

다음으로 사용자 지정 어휘 파일을 게시 합니다. 이 파일을 저장할 수 있는 위치에 대 한 제한은 없지만 [Azure Blob Storage](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)를 사용 하는 것이 좋습니다.

사용자 지정 어휘를 게시 한 후 SSML에서 참조할 수 있습니다.

> [!NOTE]
> 요소는 `lexicon` 요소 내에 있어야 합니다 `voice` .

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" 
          xmlns:mstts="http://www.w3.org/2001/mstts" 
          xml:lang="en-US">
    <voice name="en-US-AriaRUS">
        <lexicon uri="http://www.example.com/customlexicon.xml"/>
        BTW, we will be there probably at 8:00 tomorrow morning.
        Could you help leave a message to Robert Benigni for me?
    </voice>
</speak>
```

이 사용자 지정 어휘를 사용 하는 경우 "만들었으므로"는 "방법으로" 읽습니다. "Benigni"는 제공 된 IPA "bɛ ˈ ni ː nji"로 읽습니다.  

**제한 사항**
- 파일 크기: 사용자 지정 사전 파일 크기 최대 제한은 100KB이 크기를 초과 하면 합성 요청이 실패 합니다.
- 어휘 집 캐시 새로 고침: 사용자 지정 어휘는 처음 로드 될 때 TTS 서비스에서 키로 URI를 사용 하 여 캐시 됩니다. 동일한 URI를 사용 하는 어휘는 15 분 이내에 다시 로드 되지 않으므로 사용자 지정 어휘 변화는 최대 15 분이 지나야 적용 됩니다.

**음성 서비스 음성 서비스 음성 설정**

위의 샘플에서는 IPA phone 집합이 라고도 하는 국제 발음 문자를 사용 합니다. 개발자는 국제 표준 이기 때문에 IPA를 사용 하는 것이 좋습니다. 일부 IPA 문자에는 유니코드로 표시 될 때 ' 미리 구성 된 ' 및 ' 분해 됨 ' 버전이 있습니다. 사용자 지정 어휘는 분해 된 unicodes 지원 합니다.

IPA를 기억할 수 없는 경우 음성 서비스는 7 개 언어 ( `en-US` , `fr-FR` ,,,, `de-DE` `es-ES` `ja-JP` `zh-CN` 및 `zh-TW` )에 대 한 윗주 집합을 정의 합니다.

`sapi` `alphabet` 아래와 같이 사용자 지정 lexicons를 사용 하 여 특성에 대 한 베일을로 사용할 수 있습니다.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<lexicon version="1.0" 
      xmlns="http://www.w3.org/2005/01/pronunciation-lexicon"
      xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
      xsi:schemaLocation="http://www.w3.org/2005/01/pronunciation-lexicon
        http://www.w3.org/TR/2007/CR-pronunciation-lexicon-20071212/pls.xsd"
      alphabet="sapi" xml:lang="en-US">
  <lexeme>
    <grapheme>BTW</grapheme>
    <alias> By the way </alias>
  </lexeme>
  <lexeme>
    <grapheme> Benigni </grapheme>
    <phoneme> b eh 1 - n iy - n y iy </phoneme>
  </lexeme>
</lexicon>
```

음성 서비스에 대 한 자세한 내용은 [음성 서비스 음성 서비스 윗주 집합](speech-ssml-phonetic-sets.md)을 참조 하세요.

## <a name="adjust-prosody"></a>Prosody 조정

`prosody`요소는 텍스트 음성 변환 출력에 대 한 피치, 윤곽선, 범위, 요율, 기간 및 볼륨에 대 한 변경 내용을 지정 하는 데 사용 됩니다. 요소에는 `prosody` `audio` ,, `break` `p` , `phoneme` , `prosody` `say-as` `sub` `s` ,, 및 요소가 포함 될 수 있습니다.

운율 적 경계가 특성 값은 광범위 한 범위에 따라 달라질 수 있으므로 음성 인식기는 할당 된 값을 선택한 음성의 실제 운율 적 경계가 값을 제안 하는 것으로 해석 합니다. 텍스트 음성 변환 서비스는 지원 되지 않는 값을 제한 하거나 대체 합니다. 지원 되지 않는 값의 예로는 1 MHz의 피치 또는 120 볼륨이 있습니다.

**구문**

```XML
<prosody pitch="value" contour="value" range="value" rate="value" duration="value" volume="value"></prosody>
```

**특성**

| attribute | Description | 필수/선택 |
|-----------|-------------|---------------------|
| `pitch` | 텍스트의 기준선 피치를 나타냅니다. 다음과 같이 피치를 표현할 수 있습니다.<ul><li>숫자로 표시 되 고 그 뒤에 "Hz" (Hz)가 표시 되는 절대값입니다. 예: 600 Hz.</li><li>간격을 변경할 양을 지정 하는 "+" 또는 "-" 앞에 오는 숫자로 표시 되는 상대 값입니다. 예: + 80 Hz 또는-2st. "St"는 변경 단위가 표준 diatonic 크기에 대 한 반음 (절반 단계의 절반) 임을 나타냅니다.</li><li>상수 값:<ul><li>x-낮음</li><li>low</li><li>중간</li><li>high</li><li>x-높음</li><li>default</li></ul></li></ul>. | 선택 사항 |
| `contour` |이제 컨투어는 신경망 및 표준 음성을 모두 지원 합니다. 컨투어는 피치의 변화를 나타냅니다. 이러한 변경 내용은 음성 출력에서 지정 된 시간 위치의 대상 배열로 표시 됩니다. 각 대상은 매개 변수 쌍 집합으로 정의 됩니다. 예를 들면 다음과 같습니다. <br/><br/>`<prosody contour="(0%,+20Hz) (10%,-2st) (40%,+10Hz)">`<br/><br/>각 매개 변수 집합의 첫 번째 값은 피치 변경의 위치를 텍스트 기간의 백분율로 지정 합니다. 두 번째 값은 피치에 대 한 열거형 값 또는 상대 값을 사용 하 여 피치를 발생 시키거나 낮출 크기를 지정 합니다 (참조 `pitch` ). | 선택 사항 |
| `range` | 텍스트의 피치 범위를 나타내는 값입니다. `range`설명 하는 데 사용 되는 것과 동일한 절대값, 상대 값 또는 열거형 값을 사용 하 여 나타낼 수 있습니다 `pitch` . | 선택 사항 |
| `rate` | 텍스트의 읽어주기 율을 나타냅니다. 다음과 같이 나타낼 수 있습니다 `rate` .<ul><li>기본값의 승수 역할을 하는 숫자로 표시 되는 상대 값입니다. 예를 들어 값이 *1* 이면 비율이 변경 되지 않습니다. 값이 *0.5* 이면 나누어이 발생 합니다. 값이 *3* 이면 tripling이 발생 합니다.</li><li>상수 값:<ul><li>x-느림</li><li>slow</li><li>중간</li><li>빠르지</li><li>x-빠름</li><li>default</li></ul></li></ul> | 선택 사항 |
| `duration` | TTS (음성 합성) 서비스가 텍스트를 읽는 동안 경과 해야 하는 기간 (초 또는 밀리초)입니다. 예를 들면 *2 s* 또는 *18ms*입니다. | 선택 사항 |
| `volume` | 말하는 음성의 볼륨 수준을 나타냅니다. 볼륨을 다음과 같이 나타낼 수 있습니다.<ul><li>*Quietest* 에서 *loudest*까지 0.0 ~ 100.0 범위의 숫자로 표현 된 절대값입니다. 예를 들면 75입니다. 기본값은 100.0입니다.</li><li>볼륨을 변경할 양을 지정 하는 "+" 또는 "-" 앞에 오는 숫자로 표시 되는 상대 값입니다. 예를 들면 + 10 또는-5.5입니다.</li><li>상수 값:<ul><li>무음</li><li>x-소프트</li><li>유동적</li><li>중간</li><li>큰 성문</li><li>x-소리내어</li><li>default</li></ul></li></ul> | 선택 사항 |

### <a name="change-speaking-rate"></a>말하기 속도 변경

말하는 속도는 신경망 또는 문장 수준에서 신경망 및 표준 음성에 적용 될 수 있습니다. 

**예제**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-GuyNeural">
        <prosody rate="+30.00%">
            Welcome to Microsoft Cognitive Services Text-to-Speech API.
        </prosody>
    </voice>
</speak>
```

### <a name="change-volume"></a>볼륨 변경

볼륨 변경 내용은 단어 또는 문장 수준에서 표준 음성에 적용 될 수 있습니다. 반면 볼륨 변경은 문장 수준에서 신경망에만 적용할 수 있습니다.

**예제**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaRUS">
        <prosody volume="+20.00%">
            Welcome to Microsoft Cognitive Services Text-to-Speech API.
        </prosody>
    </voice>
</speak>
```

### <a name="change-pitch"></a>피치 변경

피치 변경 내용은 단어 또는 문장 수준에서 표준 음성에 적용 될 수 있습니다. 하지만 피치 변경 내용은 문장 수준에서 신경망에만 적용할 수 있습니다.

**예제**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-Guy24kRUS">
        Welcome to <prosody pitch="high">Microsoft Cognitive Services Text-to-Speech API.</prosody>
    </voice>
</speak>
```

### <a name="change-pitch-contour"></a>피치 곡선 변경

> [!IMPORTANT]
> 이제 신경망에서 피치 컨투어 변경이 지원 됩니다.

**예제**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaNeural">
        <prosody contour="(60%,-60%) (100%,+80%)" >
            Were you the only person in the room? 
        </prosody>
    </voice>
</speak>
```
## <a name="say-as-element"></a>말 요소로

`say-as` 요소 텍스트의 콘텐츠 형식 (예: 숫자 또는 날짜)을 나타내는 선택적 요소입니다. 텍스트를 발음 하는 방법에 대 한 지침을 음성 합성 엔진에 제공 합니다.

**구문**

```XML
<say-as interpret-as="string" format="digit string" detail="string"> <say-as>
```

**특성**

| attribute | Description | 필수/선택 |
|-----------|-------------|---------------------|
| `interpret-as` | 요소 텍스트의 콘텐츠 형식을 나타냅니다. 형식 목록은 아래 표를 참조 하세요. | 필수 |
| `format` | 모호한 형식이 있을 수 있는 콘텐츠 형식에 대 한 요소 텍스트의 정확한 서식 지정에 대 한 추가 정보를 제공 합니다. SSML은이를 사용 하는 내용 유형에 대 한 형식을 정의 합니다 (아래 표 참조). | 선택 사항 |
| `detail` | 읽을 세부 정보 수준을 나타냅니다. 예를 들어이 특성은 음성 합성 엔진이 문장 부호를 발음 하도록 요청할 수 있습니다. 에 대해 정의 된 표준 값이 `detail` 없는 경우 | 선택 사항 |

<!-- I don't understand the last sentence. Don't we know which one Cortana uses? -->

다음은 및 특성에 대해 지원 되는 내용 유형 `interpret-as` `format` 입니다. `format` `interpret-as` 이 날짜 및 시간으로 설정 된 경우에만 특성을 포함 합니다.

| 해석 방법 | format | 해석 |
|--------------|--------|----------------|
| `address` | | 텍스트를 주소로 읽습니다. 음성 합성 엔진 pronounces:<br /><br />`I'm at <say-as interpret-as="address">150th CT NE, Redmond, WA</say-as>`<br /><br />"저는 150th 법정 워싱턴 워싱턴에 있습니다." |
| `cardinal`, `number` | | 텍스트는 카디널 숫자로 표시 됩니다. 음성 합성 엔진 pronounces:<br /><br />`There are <say-as interpret-as="cardinal">3</say-as> alternatives`<br /><br />"세 가지 대안이 있습니다." |
| `characters`, `spell-out` | | 텍스트는 개별 문자 (철자)로 되어 있습니다. 음성 합성 엔진 pronounces:<br /><br />`<say-as interpret-as="characters">test</say-as>`<br /><br />' T E S T. "로 |
| `date` | dmy, mdy, ymd, ydm, ydm, my, md, dm, d, m, y | 텍스트를 날짜로 발음 합니다. `format`특성은 날짜의 형식 (*d = 일, m = month 및 y = year*)을 지정 합니다. 음성 합성 엔진 pronounces:<br /><br />`Today is <say-as interpret-as="date" format="mdy">10-19-2016</say-as>`<br /><br />"현재는 10 월 19 2016입니다." |
| `digits`, `number_digit` | | 텍스트는 개별 숫자의 시퀀스로 음성으로 변환 됩니다. 음성 합성 엔진 pronounces:<br /><br />`<say-as interpret-as="number_digit">123456789</say-as>`<br /><br />"1 2 3 4 5 6 7 8 9"입니다. |
| `fraction` | | 텍스트를 소수로 표시 합니다. 음성 합성 엔진 pronounces:<br /><br /> `<say-as interpret-as="fraction">3/8</say-as> of an inch`<br /><br />"Eighths 3 개." |
| `ordinal` | | 텍스트는 서 수로 표시 됩니다. 음성 합성 엔진 pronounces:<br /><br />`Select the <say-as interpret-as="ordinal">3rd</say-as> option`<br /><br />"세 번째 옵션을 선택 하십시오." |
| `telephone` | | 텍스트는 전화 번호로 표시 됩니다. 특성에는 `format` 국가 코드를 나타내는 숫자가 포함 될 수 있습니다. 예를 들어 미국의 경우 "1"이 고 이탈리아의 경우 "39"입니다. 음성 합성 엔진은이 정보를 사용 하 여 전화 번호의 발음을 안내할 수 있습니다. 전화 번호에는 국가 코드도 포함 될 수 있으며,이 경우의 국가 코드 보다 우선적으로 적용 `format` 됩니다. 음성 합성 엔진 pronounces:<br /><br />`The number is <say-as interpret-as="telephone" format="1">(888) 555-1212</say-as>`<br /><br />"내 숫자가 지역 코드 8 8 8 5 5 5 1 2 1 2입니다." |
| `time` | hms12, hms24 | 텍스트를 시간으로 읽습니다. `format`특성은 시간이 12 시간제 (hms12) 또는 24 시간제 (hms24)를 사용 하 여 지정 되었는지 여부를 지정 합니다. 콜론을 사용 하 여 시간, 분, 초를 나타내는 숫자를 구분 합니다. 다음은 유효한 시간 예: 12:35, 1:14:32, 08:15 및 02:50:45입니다. 음성 합성 엔진 pronounces:<br /><br />`The train departs at <say-as interpret-as="time" format="hms12">4:00am</say-as>`<br /><br />"The 트레인 분리 4 A M." |

**사용 현황**

`say-as`요소는 텍스트만 포함할 수 있습니다.

**예제**

음성 합성 엔진은 다음과 같은 예를 "첫 번째 요청은 10 월 19 20 10의 한 방에 대 한 첫 번째 요청은 12 35 PM에 초기 도착 하는 것입니다." 라고 말합니다.
 
```XML
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaRUS">
        <p>
        Your <say-as interpret-as="ordinal"> 1st </say-as> request was for <say-as interpret-as="cardinal"> 1 </say-as> room
        on <say-as interpret-as="date" format="mdy"> 10/19/2010 </say-as>, with early arrival at <say-as interpret-as="time" format="hms12"> 12:35pm </say-as>.
        </p>
    </voice>
</speak>
```

## <a name="add-recorded-audio"></a>기록 된 오디오 추가

`audio` 는 SSML 문서에 MP3 오디오를 삽입할 수 있는 선택적 요소입니다. 오디오 요소 본문에는 오디오 파일을 사용할 수 없거나 재생 경우 음성으로 표시 되는 일반 텍스트 또는 SSML 태그가 포함 될 수 있습니다. 또한 요소에는 `audio` `audio` ,, `break` `p` , `s` , `phoneme` `prosody` `say-as` `sub` ,, 및 요소가 포함 될 수 있습니다.

SSML 문서에 포함 된 오디오는 다음 요구 사항을 충족 해야 합니다.

* MP3는 인터넷에 액세스할 수 있는 HTTPS 끝점에서 호스팅되어야 합니다. HTTPS가 필요 하며, MP3 파일을 호스트 하는 도메인은 신뢰할 수 있는 유효한 TLS/SSL 인증서를 제공 해야 합니다.
* MP3는 유효한 MP3 파일 (MPEG v2) 이어야 합니다.
* 비트 전송률은 48 kbps 여야 합니다.
* 샘플링 주기는 16000 Hz 여야 합니다.
* 단일 응답의 모든 텍스트 및 오디오 파일에 대해 결합 된 총 시간은 90 (90) 초를 초과할 수 없습니다.
* MP3는 고객 관련 정보나 기타 중요 한 정보를 포함 해서는 안 됩니다.

**구문**

```xml
<audio src="string"/></audio>
```

**특성**

| attribute | Description                                   | 필수/선택                                        |
|-----------|-----------------------------------------------|------------------------------------------------------------|
| `src`     | 오디오 파일의 위치/URL을 지정 합니다. | SSML 문서에서 audio 요소를 사용 하는 경우 필요 합니다. |

**예제**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaRUS">
        <p>
            <audio src="https://contoso.com/opinionprompt.wav"/>
            Thanks for offering your opinion. Please begin speaking after the beep.
            <audio src="https://contoso.com/beep.wav">
                Could not play the beep, please voice your opinion now.
            </audio>
        </p>
    </voice>
</speak>
```

## <a name="add-background-audio"></a>배경 오디오 추가

요소를 사용 하 여 `mstts:backgroundaudio` SSML 문서에 배경 오디오를 추가 하거나 오디오 파일을 텍스트를 음성으로 혼합할 수 있습니다. 를 사용 `mstts:backgroundaudio` 하 여 백그라운드에서 오디오 파일을 반복 하 고 텍스트 음성 변환의 시작 부분에서 페이드 인하고 텍스트 음성 변환 끝에서 페이드 아웃할 수 있습니다.

제공 된 배경 오디오가 텍스트 음성 변환 또는 페이드 아웃 보다 짧으면 루프가 반복 됩니다. 텍스트를 음성으로 변환 하는 것 보다 길면 페이드 아웃이 완료 되 면 중지 됩니다.

SSML 문서 당 배경 오디오 파일은 하나만 허용 됩니다. 그러나 `audio` 요소 내에서 태그를 섞어서 하 여 `voice` SSML 문서에 오디오를 더 추가할 수 있습니다.

**구문**

```XML
<mstts:backgroundaudio src="string" volume="string" fadein="string" fadeout="string"/>
```

**특성**

| attribute | Description | 필수/선택 |
|-----------|-------------|---------------------|
| `src` | 배경 오디오 파일의 위치/URL을 지정 합니다. | SSML 문서에서 배경 오디오를 사용 하는 경우 필요 합니다. |
| `volume` | 배경 오디오 파일의 볼륨을 지정 합니다. **허용**되는 값: `0` `100` 포함 기본값은 `1`입니다. | 선택 사항 |
| `fadein` | 백그라운드 오디오를 "페이드 인" 하는 시간을 밀리초로 지정 합니다. 기본값은 페이드 인 `0` 되지 않는 것과 동일한입니다. **허용**되는 값: `0` `10000` 포함  | 선택 사항 |
| `fadeout` | 배경 오디오 페이드 아웃 기간 (밀리초)을 지정 합니다. 기본값은 이며,이 값은 `0` 페이드 아웃 하지 않는 것과 같습니다. **허용**되는 값: `0` `10000` 포함  | 선택 사항 |

**예제**

```xml
<speak version="1.0" xml:lang="en-US" xmlns:mstts="http://www.w3.org/2001/mstts">
    <mstts:backgroundaudio src="https://contoso.com/sample.wav" volume="0.7" fadein="3000" fadeout="4000"/>
    <voice name="Microsoft Server Speech Text to Speech Voice (en-US, AriaRUS)">
        The text provided in this document will be spoken over the background audio.
    </voice>
</speak>
```

## <a name="next-steps"></a>다음 단계

* [언어 지원: 음성, 로캘, 언어](language-support.md)
