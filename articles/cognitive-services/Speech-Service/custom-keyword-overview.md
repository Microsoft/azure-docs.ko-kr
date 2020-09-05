---
title: 사용자 지정 키워드-음성 서비스
titleSuffix: Azure Cognitive Services
description: 음성 SDK (소프트웨어 개발 키트)를 사용 하는 사용자 지정 키워드의 기능, 기능 및 제한 사항에 대 한 개요입니다.
services: cognitive-services
author: hasyashah
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: hasshah
ms.custom: devx-track-csharp
ms.openlocfilehash: 8c19650a8f36181e3e003414ff15add071e9a8ac
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88918896"
---
# <a name="what-is-a-keyword"></a>키워드 란 무엇 인가요?

키워드는 제품이 음성으로 활성화 될 수 있도록 하는 단어 또는 짧은 문구입니다. 예를 들어 "안녕하세요 Cortana"는 Cortana 길잡이에 대 한 키워드입니다. 음성 정품 인증을 사용 하면 사용자가 키워드를 말하는 방법으로 제품을 완벽 하 게 자유롭게 조작할 수 있습니다. 제품이 키워드를 지속적으로 수신 하 게 되 면 데이터가 가능한 한 비공개로 유지 되도록 감지 될 때까지 모든 오디오가 사용자의 장치에서 로컬로 처리 됩니다.

## <a name="core-features-of-custom-keyword"></a>사용자 지정 키워드의 핵심 기능

사용자 지정 키워드의 사용자 지정, 성능 및 통합 기능을 사용 하면 제품의 비전과 사용자 요구에 가장 적합 한 음성 활성화를 조정할 수 있습니다.

| 기능 | Description |
|----------|----------|
| 키워드 사용자 지정 | 브랜드의 확장으로 키워드는 고객과 강화 하는 자본을 제공 합니다. Speech Studio의 사용자 지정 키워드 포털을 사용 하면 브랜드를 가장 잘 나타내는 단어 또는 짧은 구를 지정할 수 있습니다. 생성 된 키워드 모델에 의해 적용 되는 올바른 발음를 선택 하 여 키워드를 추가로 개인 설정할 수 있습니다.
| 키워드 확인 | 로컬에서 검색 되는 키워드에 높은 신뢰도가 있는 경우 사용자가 키워드를 추가 하 여 추가 확인을 위해 오디오를 클라우드로 보냅니다. 키워드 확인은 잘못 된 로컬 검색의 영향을 줄이고 사용자의 개인 정보를 보호 하 여 추가 보안 계층을 제공 합니다.
| 음성 도우미 & Speech SDK 통합 | Speech Studio에서 사용자 지정 키워드를 통해 생성 된 키워드는 음성 SDK를 통해 장치 또는 응용 프로그램 내에서 쉽게 통합 될 수 있습니다. SDK가 Speech Studio에서 제공 하는 키워드 모델을 가리키도록 하 고 제품이 음성으로 활성화 되며 키워드 확인으로 지원 됩니다. 사용자 고유의 [음성 도우미](voice-assistants.md)를 빌드하여 제품의 음성 환경을 완료할 수 있습니다.

## <a name="get-started-with-custom-keywords"></a>사용자 지정 키워드 시작

* 기본 사용 및 디자인 패턴은 [사용자 지정 키워드 기본 사항](custom-keyword-basics.md) 을 참조 하세요.
* [C #을 사용 하 여 음성 SDK를 통해 제품을 음성으로 활성화](tutorial-voice-enable-your-bot-speech-sdk.md) 하는 방법

## <a name="choose-an-effective-keyword"></a>유효 키워드 선택

유효 키워드를 만드는 것은 장치가 일관적이 고 정확 하 게 응답 하는지 확인 하는 데 중요 합니다. 키워드를 사용자 지정 하는 것은 장치를 차별화 하 고 브랜딩을 강화 하는 효과적인 방법입니다. 키워드를 선택 하는 경우 다음 지침을 고려 하십시오.

> [!div class="checklist"]
> * 키워드는 영어 단어 또는 문구 여야 합니다.
> * 말하는 데 2초 이상 걸리지 않아야 합니다.
> * 4-7개 음절의 단어가 가장 적합합니다. 예를 들어 "안녕하세요, Computer"는 좋은 키워드입니다. 그냥 "Hey"만 사용하는 것은 그렇지 않습니다.
> * 키워드는 일반적인 영어 발음 규칙을 따라야 합니다.
> * 일반적인 영어 발음 규칙을 따르는 고유하거나 심지어 합성된 단어는 거짓 긍정을 줄일 수 있습니다. 예를 들어 "computerama"은 좋은 키워드가 될 수 있습니다.
> * 일반적인 단어는 선택하지 않도록 합니다. 예를 들어, "eat"과 “go”는 사람들이 평상시 대화에서 자주 말하는 단어입니다. 이러한 단어는 디바이스를 잘못 트리거할 수 있습니다.
> * 대체 발음 될 수 있는 키워드를 사용 하지 마십시오. 사용자는 디바이스의 반응을 얻기 위해 "올바른" 발음을 알고 있어야 합니다. 예를 들어 "509"는 "five zero nine", "five oh nine" 또는 "five hundred and nine"으로 발음할 수 있습니다. "R.E.I." "R E I" 또는 "Ray"로, "Live"는 "/līv/" 또는 "/liv/"로 발음할 수 있습니다.
> * 특수 문자, 기호 또는 숫자는 사용하지 않도록 합니다. 예를 들어 "Go #" 및 "20 + cats"는 문제가 있는 키워드가 될 수 있습니다. 그러나 "go sharp" 또는 "twenty plus cats"는 적합할 수 있습니다. 여전히 브랜딩에 기호를 사용하고, 마케팅 및 설명서를 사용하여 적절한 발음을 강화할 수 있습니다.

> [!NOTE]
> 키워드로 상표 단어를 선택 하는 경우 해당 상표를 소유 하 고 있거나 해당 단어를 사용할 수 있는 상표 소유자의 권한이 있는지를 알고 있어야 합니다. Microsoft는 키워드를 선택 하 여 발생할 수 있는 법적 문제에 대 한 책임을 지지 않습니다.

## <a name="see-samples-on-github"></a>GitHub의 샘플을 참조 하세요.

* [C를 사용 하는 유니버설 Windows 플랫폼에서 음성 SDK를 사용 하 여 키워드 인식 #](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/csharp/uwp/keyword-recognizer)
* [Java를 사용 하 여 Android에서 음성 SDK로 키워드 인식](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/java/android/keyword-recognizer)

## <a name="next-steps"></a>다음 단계

* [평가판 Speech Service 구독 키 받기](get-started.md)
* [Speech SDK 가져오기](speech-sdk.md)
* [음성 도우미에 대 한 자세한 정보](voice-assistants.md)
