---
title: Ink Recognizer API에 잉크 데이터 보내기
titleSuffix: Azure Cognitive Services
description: 다양한 애플리케이션에 대해 Ink Analyzer API를 호출하는 방법을 알아봅니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: conceptual
ms.date: 08/24/2020
ms.author: aahi
ms.openlocfilehash: 8665e27b6b7c43bce7d749d9bfe0a2f815a0f46d
ms.sourcegitcommit: 8a7b82de18d8cba5c2cec078bc921da783a4710e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89051103"
---
# <a name="send-ink-data-to-the-ink-recognizer-api"></a>Ink Recognizer API에 잉크 데이터 보내기 

[!INCLUDE [ink-recognizer-deprecation](../includes/deprecation-note.md)]

디지털 잉크 입력이란 필기 및 그리기 같은 입력을 디지털로 표현할 수 있는 기술입니다. 이는 일반적으로 스타일러스와 같은 입력 디바이스의 움직임을 캡처하는 디지타이저를 사용하여 수행됩니다. 디바이스에서 풍부한 디지털 잉크 입력 환경을 계속 사용할 수 있게 되면서, AI 및 기계 학습은 어떤 상황에서도 손으로 그린 도형과 텍스트를 인식할 수 있습니다. Ink Recognizer API를 사용하면 잉크 스트로크를 보내고 이에 대한 자세한 정보를 얻을 수 있습니다. 

## <a name="the-ink-recognizer-api-vs-ocr-services"></a>잉크 인식기 API와 OCR 서비스 비교

Ink Recognizer API는 OCR(광학 문자 인식)을 사용하지 않습니다. OCR 서비스는 이미지의 픽셀 데이터를 처리하여 필기 및 텍스트 인식을 제공합니다. 이를 오프라인 인식이라고도 합니다. 대신 Ink Recognizer API를 사용하려면 입력 디바이스가 사용될 때 캡처되는 디지털 잉크 스트로크 데이터가 필요합니다. 디지털 잉크 데이터를 이 방식으로 처리하면 OCR 서비스에 비해 더 정확한 인식 결과를 얻을 수 있습니다. 

## <a name="sending-ink-data"></a>잉크 데이터 보내기

Ink Recognizer API에는 검색 화면에 닿는 순간부터 들어 올릴 때까지 입력 디바이스에서 만든 잉크 스트로크를 나타내는 X 및 Y 좌표가 필요합니다. 각 스트로크의 포인트는 쉼표로 구분된 값의 문자열이어야 하며 아래 예제와 같이 JSON 형식으로 지정되어야 합니다. 또한 각 잉크 스트로크에는 각 요청의 고유한 ID가 있어야 합니다. 해당 ID가 동일한 요청 내에서 반복되면 API에서 오류를 반환합니다. 가장 정확한 인식 결과를 얻으려면 소수점 뒤에 8자리 이상의 숫자가 있어야 합니다. 캔버스의 원점(0.0)은 잉크 입력 캔버스의 왼쪽 위 모서리로 간주됩니다.

> [!NOTE]
> 다음 예제는 유효한 JSON이 아닙니다. 전체 Ink Recognizer JSON 요청은 [GitHub](https://go.microsoft.com/fwlink/?linkid=2089909)에서 찾을 수 있습니다.
 
```json
{
  "language": "en-US",
  "strokes": [
   {
    "id": 43,
    "points": 
        "5.1365, 12.3845,
        4.9534, 12.1301,
        4.8618, 12.1199,
        4.7906, 12.2217,
        4.7906, 12.5372,
        4.8211, 12.9849,
        4.9534, 13.6667,
        5.0958, 14.4503,
        5.3299, 15.2441,
        5.6555, 16.0480,
        ..."
   },
    ...
  ]
}
```

## <a name="ink-recognizer-response"></a>Ink Recognizer 응답

Ink Recognizer API는 잉크 콘텐츠에서 인식된 개체에 대한 분석 응답을 반환합니다. 응답에는 서로 다른 잉크 스트로크 간의 관계를 설명하는 인식 단위가 포함되어 있습니다. 예를 들어 별도의 고유한 도형을 만드는 스트로크는 여러 단위로 포함됩니다. 각 단위에는 인식된 개체, 해당 좌표 및 다른 그리기 특성을 포함하여 잉크 스트로크에 대한 자세한 정보가 포함되어 있습니다.

## <a name="shapes-recognized-by-the-ink-recognizer-api"></a>Ink Recognizer API에서 인식되는 도형

Ink Recognizer API는 노트 작성에서 가장 일반적으로 사용되는 도형을 식별할 수 있습니다. 아래 이미지에는 몇 가지 기본 예제가 나와 있습니다. API에서 인식되는 도형 및 다른 잉크 콘텐츠에 대한 전체 목록은 [API 참조 문서](https://go.microsoft.com/fwlink/?linkid=2089907)를 참조하세요. 

![Ink Recognizer API에서 인식되는 도형의 목록](../media/shapes.png)

## <a name="recommended-calling-patterns"></a>추천되는 호출 패턴

Ink Recognizer REST API는 애플리케이션에 따라 다양한 패턴으로 호출할 수 있습니다. 

### <a name="user-initiated-api-calls"></a>사용자 시작 API 호출

사용자 입력을 사용하는 앱(예: 노트 작성 또는 주석 앱)을 빌드하는 경우 Ink Recognizer API에 보내는 시간과 잉크를 제어할 수 있습니다. 이 기능은 텍스트와 도형이 모두 캔버스에 있고 사용자가 각각에 대해 서로 다른 작업을 수행하려는 경우에 특히 유용합니다. 사용자가 API에 보낼 항목을 선택할 수 있게 하는 선택 기능(올가미 또는 다른 기하학적 선택 도구)을 추가하는 것이 좋습니다.  

### <a name="app-initiated-api-calls"></a>앱 시작 API 호출

시간이 초과되면 앱에서 Ink Recognizer API를 호출하도록 할 수도 있습니다. 현재 잉크 스트로크를 API에 정기적으로 보내면 API의 응답 시간을 향상시키는 동시에 만들어지는 인식 결과를 저장할 수 있습니다. 예를 들어 사용자가 완성한 필기 텍스트를 감지한 후 한 줄의 해당 텍스트를 API에 보낼 수 있습니다. 

인식 결과가 이미 있으면 서로 관련된 잉크 스트로크의 특성에 대한 정보를 얻을 수 있습니다. 예를 들어 같은 단어, 선, 목록, 단락 또는 도형을 구성하기 위해 그룹화된 스트로크가 있습니다. 이 정보는 예를 들어 스트로크 그룹을 한 번에 선택할 수 있으므로 앱의 잉크 선택 기능을 향상시킬 수 있습니다.

## <a name="integrate-the-ink-recognizer-api-with-windows-ink"></a>Ink Recognizer API와 Windows Ink 통합

[Windows Ink](https://docs.microsoft.com/windows/uwp/design/input/pen-and-stylus-interactions)는 다양한 디바이스에서 디지털 잉크 입력 환경을 사용할 수 있게 하는 도구와 기술을 제공합니다. Windows 잉크 플랫폼과 잉크 인식기 API를 결합 하 여 디지털 잉크 스트로크를 표시 하 고 해석 하는 응용 프로그램을 만들 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [Ink Recognizer API란?](../overview.md)
* [Ink Recognizer REST API 참조](https://go.microsoft.com/fwlink/?linkid=2089907)

* 다음을 사용하여 디지털 잉크 스트로크 데이터 보내기를 시작합니다.
    * [C#](../quickstarts/csharp.md)
    * [Java](../quickstarts/java.md)
    * [JavaScript](../quickstarts/javascript.md)
