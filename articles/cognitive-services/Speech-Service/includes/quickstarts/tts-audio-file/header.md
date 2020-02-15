---
author: IEvangelist
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/31/2020
ms.author: dapine
ms.openlocfilehash: cd8da5eb9313685361ca56b56c024c2dfb37276e
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/01/2020
ms.locfileid: "76961610"
---
이 빠른 시작에서는 [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md)를 사용하여 텍스트를 오디오 파일의 합성된 음성으로 변환합니다. 텍스트 음성 변환 서비스는 [텍스트 음성 변환 언어 지원](../../../language-support.md#text-to-speech)에 따라 합성된 음성을 위한 다양한 옵션을 제공합니다. 몇 가지 필수 구성 요소를 충족한 후 파일에 음성을 합성하는 작업은 다음 다섯 가지 단계를 수행하면 됩니다.
> [!div class="checklist"]
> * 구독 키 및 지역에서 `SpeechConfig` 개체를 만듭니다.
> * .WAV 파일 이름을 지정하는 오디오 구성 개체를 만듭니다.
> * 위의 구성 개체를 사용하여 `SpeechSynthesizer` 개체를 만듭니다.
> * `SpeechSynthesizer` 개체를 사용하여 텍스트를 합성된 음성으로 변환한 후 지정된 오디오 파일에 저장합니다.
> * 오류에 대해 반환된 `SpeechSynthesizer`를 검사합니다.
