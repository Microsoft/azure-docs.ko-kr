---
title: 텍스트 분석 API에서 텍스트 오프셋
titleSuffix: Azure Cognitive Services
description: 다국어 및이 모 지 인코딩으로 인 한 오프셋에 대해 알아봅니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 03/09/2020
ms.author: aahi
ms.reviewer: jdesousa
ms.openlocfilehash: 14fd7c2b034077d818d1a1224d3c4c12a7fc07bc
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88855637"
---
# <a name="text-offsets-in-the-text-analytics-api-output"></a>텍스트 분석 API 출력의 텍스트 오프셋

다국어 및 다른 기능을 지원 하기 위해 두 개 이상의 [코드 포인트](https://wikipedia.org/wiki/Code_point) 를 사용 하 여 grapheme 라는 단일 표시 문자를 나타내는 유니코드 인코딩이 발생 했습니다. 예를 들어 🌷와 같은이 모 지는 👍 여러 문자를 사용 하 여 스킨 톤과 같은 시각적 특성에 대 한 추가 문자를 사용 하 여 셰이프를 작성할 수 있습니다. 마찬가지로 힌디어 단어는 `अनुच्छेद` 5 개의 문자와 3 개의 결합 표시로 인코딩됩니다.

가능한 다국어 및이 모 지 인코딩의 길이가 다르기 때문에 텍스트 분석 API 응답에서 오프셋을 반환할 수 있습니다.

## <a name="offsets-in-the-api-response"></a>API 응답의 오프셋입니다. 

[명명 된 엔터티 인식](../how-tos/text-analytics-how-to-entity-linking.md) 또는 [감정 분석](../how-tos/text-analytics-how-to-sentiment-analysis.md)와 같은 API 응답에서 오프셋이 반환 될 때마다 다음 사항을 명심 하십시오.

* 응답의 요소는 호출 된 끝점과 관련이 있을 수 있습니다. 
* HTTP POST/GET 페이로드는 [u t f-8](https://www.w3schools.com/charsets/ref_html_utf8.asp)로 인코딩되고 클라이언트 쪽 컴파일러 또는 운영 체제의 기본 문자 인코딩이 될 수도 있고 그렇지 않을 수도 있습니다.
* 오프셋은 문자 수가 아니라 [Unicode 8.0.0](https://unicode.org/versions/Unicode8.0.0) 표준을 기반으로 하는 grapheme 개수를 나타냅니다.

## <a name="extracting-substrings-from-text-with-offsets"></a>오프셋을 사용 하 여 텍스트에서 부분 문자열 추출

기호는 문자 기반 부분 문자열 메서드 (예: .NET [substring () 메서드)](https://docs.microsoft.com/dotnet/api/system.string.substring?view=netframework-4.8) 를 사용할 때 문제를 일으킬 수 있습니다. 한 가지 문제는 오프셋으로 인해 substring 메서드가 끝이 아닌 다중 문자 grapheme 인코딩 중간에 종료 될 수 있다는 것입니다.

.NET에서는 [쌍인지](https://docs.microsoft.com/dotnet/api/system.globalization.stringinfo?view=netframework-4.8) 클래스를 사용 하는 것이 좋습니다 .이 클래스를 사용 하면 개별 문자 개체가 아닌 일련의 텍스트 요소로 문자열을 사용할 수 있습니다. 선호 하는 소프트웨어 환경에서 grapheme 분할기 라이브러리를 찾을 수도 있습니다. 

텍스트 분석 API는 편의상 이러한 텍스트 요소도 반환 합니다.

## <a name="offsets-in-api-version-31-preview"></a>API 버전 3.1-미리 보기의 오프셋

API 버전 3.1-preview. 1부터 오프셋을 반환 하는 모든 텍스트 분석 API 끝점은 `stringIndexType` 매개 변수를 지원 합니다. 이 매개 변수는 `offset` `length` 요청 된 문자열 반복 체계와 일치 하도록 API 출력의 및 특성을 조정 합니다. 현재는 다음과 같은 세 가지 형식을 지원 합니다.

1. `textElement_v8` (기본값): [Unicode 8.0.0](https://unicode.org/versions/Unicode8.0.0) standard에서 정의한 대로 graphemes를 반복 합니다.
2. `unicodeCodePoint`: Python 3의 기본 체계를 반복 하 여 [유니코드 코드 요소](http://www.unicode.org/versions/Unicode13.0.0/ch02.pdf#G25564)를 반복 합니다.
3. `utf16CodeUnit`: Javascript, Java 및 .NET의 기본 스키마 인 [Utf-16 코드 단위](https://unicode.org/faq/utf_bom.html#UTF16)를 반복 합니다.

요청 된이 `stringIndexType` 선택한 프로그래밍 환경과 일치 하는 경우 표준 부분 문자열 또는 조각 메서드를 사용 하 여 부분 문자열 추출을 수행할 수 있습니다. 

## <a name="see-also"></a>참고 항목

* [Text Analytics 개요](../overview.md)
* [감정 분석](../how-tos/text-analytics-how-to-sentiment-analysis.md)
* [엔터티 인식](../how-tos/text-analytics-how-to-entity-linking.md)
* [언어 감지](../how-tos/text-analytics-how-to-keyword-extraction.md)
* [언어 인식](../how-tos/text-analytics-how-to-language-detection.md)
