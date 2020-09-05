---
title: Ink Recognizer란? - Ink Recognizer API
titleSuffix: Azure Cognitive Services
description: Ink Recognizer를 애플리케이션, 웹 사이트, 도구 및 기타 솔루션과 통합하여 잉크 스트로크 데이터를 입력으로 식별하고 사용하도록 합니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: overview
ms.date: 08/24/2020
ms.author: aahi
ms.custom: devx-track-csharp
ms.openlocfilehash: 6c1a720e7e9bd9c71f925f104ca7fc70a1a5ef59
ms.sourcegitcommit: 8a7b82de18d8cba5c2cec078bc921da783a4710e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89051069"
---
# <a name="what-is-the-ink-recognizer-api"></a>Ink Recognizer API란?

[!INCLUDE [ink-recognizer-deprecation](includes/deprecation-note.md)]

Ink Recognizer Cognitive Service는 디지털 잉크 콘텐츠를 분석하고 인식하는 클라우드 기반 REST API를 제공합니다. OCR(광학 문자 인식)을 사용하는 서비스와 달리, 이 API는 입력으로 디지털 잉크 스트로크 데이터가 필요합니다. 디지털 잉크 스트로크는 시간 순서로 정렬된 2D 포인트 세트(X,Y 좌표)로, 디지털 펜 또는 손가락 같은 입력 도구의 움직임을 나타냅니다. 그런 다음, 입력에서 도형과 필기 콘텐츠를 인식하고, 인식된 모든 엔터티가 포함된 JSON 응답을 반환합니다.

![API로 잉크 스트로크 입력을 보내는 과정을 설명하는 순서도](media/ink-recognizer-pen-graph.svg)

## <a name="features"></a>기능

Ink Recognizer API를 사용하면 애플리케이션에서 필기 콘텐츠를 쉽게 인식할 수 있습니다. 

|기능  |Description  |
|---------|---------|
| 필기 인식 | 63개 주요 [언어 및 로캘](language-support.md)로 필기한 콘텐츠를 인식합니다. | 
| 레이아웃 인식 | 디지털 잉크 콘텐츠에 대한 구조 정보를 가져옵니다. 콘텐츠를 쓰기 영역, 단락, 선, 단어, 글머리 기호 목록으로 구분합니다. 그러면 애플리케이션에서 레이아웃 정보를 사용하여 자동 목록 서식 지정, 도형 맞춤 같은 추가 기능을 빌드할 수 있습니다. |
| 도형 인식 | 메모할 때 가장 일반적으로 사용되는 [기하 도형](concepts/send-ink-data.md#shapes-recognized-by-the-ink-recognizer-api)을 인식합니다. |
| 결합된 도형 및 텍스트 인식 | 도형 또는 필기 콘텐츠에 속한 잉크 스트로크를 인식하여 별도로 분류합니다.|

## <a name="workflow"></a>워크플로

Ink Recognizer API는 RESTful 웹 서비스이며, HTTP 요청을 수행하고 JSON을 구문 분석할 수 있는 프로그래밍 언어에서 쉽게 호출할 수 있습니다.

[!INCLUDE [cognitive-services-ink-recognizer-signup-requirements](../../../includes/cognitive-services-ink-recognizer-signup-requirements.md)]

가입 후:

1. 잉크 스트로크 데이터를 가져와서 유효한 JSON으로 [서식을 지정](concepts/send-ink-data.md#sending-ink-data)합니다. API는 요청당 최대 1500개의 잉크 스트로크를 허용합니다. 
1. 데이터를 사용하여 Ink Recognizer API에 요청을 보냅니다.
1. 반환된 JSON 메시지를 구문 분석하여 API 응답을 처리합니다.

## <a name="next-steps"></a>다음 단계

다음 언어로 빠른 시작을 수행하여 Ink Recognizer API 호출을 시작합니다.
* [C#](quickstarts/csharp.md)
* [Java](quickstarts/java.md)
* [JavaScript](quickstarts/javascript.md)

Ink Recognizer API가 디지털 잉크 입력 앱에서 어떻게 작동하는지 알아보려면 GitHub에서 다음 샘플 애플리케이션을 살펴보세요.
* [C# 및 UWP(유니버설 Windows 플랫폼)](https://go.microsoft.com/fwlink/?linkid=2089803)  
* [C# 및 WPF(Windows Presentation Foundation)](https://go.microsoft.com/fwlink/?linkid=2089804)
* [Javascript 웹 브라우저 앱](https://go.microsoft.com/fwlink/?linkid=2089908)       
* [Java 및 Android 모바일 앱](https://go.microsoft.com/fwlink/?linkid=2089906)
* [Swift 및 iOS 모바일 앱](https://go.microsoft.com/fwlink/?linkid=2089805)
