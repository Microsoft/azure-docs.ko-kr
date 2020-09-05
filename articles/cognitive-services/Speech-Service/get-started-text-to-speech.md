---
title: 음성 합성 빠른 시작 - Speech Service
titleSuffix: Azure Cognitive Services
description: Speech SDK를 사용하여 텍스트를 음성으로 변환하는 방법을 알아봅니다. 이 빠른 시작에서는 개체 구성 및 디자인 패턴, 지원되는 오디오 출력 형식, Speech CLI, 음성 합성을 위한 사용자 지정 구성 옵션에 대해 알아봅니다.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 08/11/2020
ms.author: trbye
ms.custom: devx-track-python, devx-track-javascript, devx-track-csharp
zone_pivot_groups: speech-full-stack
ms.openlocfilehash: 12ee7fbbd7699d09deae85971ffe411e115609ec
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88934698"
---
# <a name="get-started-with-speech-synthesis"></a>음성 합성 시작

이 빠른 시작에서는 Speech SDK를 사용하여 텍스트 음성 변환을 수행하기 위한 일반적인 디자인 패턴에 대해 알아봅니다. 먼저 기본 구성 및 합성을 수행하고 다음과 같은 사용자 지정 애플리케이션 개발을 위한 고급 예제로 이동합니다.

* 메모리 내 스트림으로 응답 가져오기
* 출력 샘플 속도 및 비트 전송률 사용자 지정
* SSML(음성 합성 마크업 언어)을 사용하여 합성 요청 제출
* 인공신경망 음성 사용

> [!TIP]
> 샘플 코드로 바로 건너뛰려면 GitHub의 [빠른 시작 샘플](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart)을 참조하세요.

::: zone pivot="programming-language-csharp"
[!INCLUDE [C# Basics include](includes/how-to/text-to-speech-basics/text-to-speech-basics-csharp.md)]
::: zone-end

::: zone pivot="programming-language-cpp"
[!INCLUDE [C++ Basics include](includes/how-to/text-to-speech-basics/text-to-speech-basics-cpp.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [Java Basics include](includes/how-to/text-to-speech-basics/text-to-speech-basics-java.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [JavaScript Basics include](includes/how-to/text-to-speech-basics/text-to-speech-basics-javascript.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Python Basics include](includes/how-to/text-to-speech-basics/text-to-speech-basics-python.md)]
::: zone-end

::: zone pivot="programming-language-spx"
[!INCLUDE [CLI include](includes/how-to/text-to-speech-basics/text-to-speech-basics-cli.md)]
::: zone-end

## <a name="next-steps"></a>다음 단계

* [Custom Voice 시작](how-to-custom-voice.md)
* [SSML을 사용하여 합성 향상](speech-synthesis-markup.md)
* 책 또는 뉴스 문서와 같은 대형 텍스트 샘플에 [긴 오디오 API](long-audio-api.md)를 사용하는 방법에 대해 알아봅니다.
* GitHub의 [빠른 시작 샘플](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart)을 참조하세요.
