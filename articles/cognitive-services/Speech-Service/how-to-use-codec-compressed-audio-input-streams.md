---
title: 음성 SDK-음성 서비스를 사용 하 여 코덱 압축 오디오 스트리밍
titleSuffix: Azure Cognitive Services
description: '음성 SDK를 사용 하 여 음성 서비스에 압축 된 오디오를 스트리밍하는 방법에 대해 알아봅니다. C + +, c # 및 Linux 용 Java, Android의 Java 및 iOS의 목적-C에 사용할 수 있습니다.'
services: cognitive-services
author: amitkumarshukla
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: amishu
ms.custom: devx-track-csharp
zone_pivot_groups: programming-languages-set-twenty-two
ms.openlocfilehash: 4585538e552e73f8f7a4b7b105153a9d26eeb4c4
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88934102"
---
# <a name="use-codec-compressed-audio-input-with-the-speech-sdk"></a>음성 SDK를 사용 하 여 코덱 압축 오디오 입력 사용

음성 서비스 SDK **압축 오디오 입력 스트림** API는 또는을 사용 하 여 음성 서비스에 압축 오디오를 스트리밍하는 방법을 제공 합니다 `PullStream` `PushStream` .

스트리밍 압축 입력 오디오는 현재 Windows의 c #, c + +, Java 및 Python (UWP 응용 프로그램은 지원 되지 않음) 및 Linux (Ubuntu 16.04, Ubuntu 18.04, Debian 9, RHEL 7/8, CentOS 7/8)에서 지원 됩니다. Android의 Java 에서도 지원 됩니다.
* RHEL 8 및 CentOS 8에는 Speech SDK 버전 1.10.0 이상이 필요 합니다.
* Windows의 경우 Speech SDK 버전 1.11.0 이상이 필요 합니다.

[!INCLUDE [supported-audio-formats](includes/supported-audio-formats.md)]

## <a name="prerequisites"></a>필수 구성 요소

::: zone pivot="programming-language-csharp"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/csharp/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-cpp"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/cpp/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/java/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/python/prerequisites.md)]
::: zone-end

## <a name="example-code-using-codec-compressed-audio-input"></a>코덱 압축 오디오 입력을 사용 하는 예제 코드

::: zone pivot="programming-language-csharp"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/csharp/examples.md)]
::: zone-end

::: zone pivot="programming-language-cpp"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/cpp/examples.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/java/examples.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/python/examples.md)]
::: zone-end

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [음성 인식 방법 알아보기](quickstarts/speech-to-text-from-microphone.md)
