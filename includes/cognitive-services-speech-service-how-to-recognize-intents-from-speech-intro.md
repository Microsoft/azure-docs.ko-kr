---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 09/24/2018
ms.author: wolfma
ms.openlocfilehash: 80bf9247bbb07fa61b7153e321b1991b82a9d616
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60805500"
---
<!-- N.B. no header, language-agnostic -->

Microsoft Cognitive Services [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md)는 **음성에도 의도**를 인식하는 방법을 제공하며 Cognitive Services [LUIS(Language Understanding service)](https://www.luis.ai/home)의 지원을 받습니다.

1. LUIS 구독 키와 [지역](~/articles/cognitive-services/speech-service/regions.md#intent-recognition)을 매개 변수로 사용하여 음성 구성을 만듭니다. LUIS 구독 키는 서비스 설명서에서 **엔드포인트 키**라고 합니다. LUIS 작성 키는 사용할 수 없습니다. (이 섹션의 뒷부분에 나오는 내용을 참고하세요.)

1. 음성 구성에서 의도 인식기를 만듭니다. 기본 마이크 이외의 원본에서 인식하는 경우(예: 오디오 스트림 또는 오디오 파일) 오디오 구성을 제공합니다.

1. **AppId**에 기반한 언어 이해 모델을 가져옵니다. 필요한 의도를 추가합니다.

1. 필요한 경우, 비동기 작업에 대한 이벤트를 연결합니다. 중간 및 최종 결과(의도 포함)가 있으면 인식기가 이벤트 처리기를 호출합니다. 이벤트를 연결하지 않은 경우 애플리케이션은 최종 전사 결과만 받습니다.

1. 의도 인식을 시작합니다. 명령 또는 쿼리 인식과 같은 1단계 인식의 경우 `RecognizeOnceAsync()` 메서드를 사용합니다. 이 메서드는 처음 인식된 발언을 반환합니다. 오래 실행되는 인식의 경우 `StartContinuousRecognitionAsync()` 메서드를 사용합니다. 비동기 인식 결과 대한 이벤트를 연결합니다.

Speech SDK를 사용하는 의도 인식 시나리오는 다음 코드 조각을 참조하십시오. 샘플의 값을 자체 LUIS 구독 키(엔드포인트 키), [사용자의 구독 지역](~/articles/cognitive-services/speech-service/regions.md#intent-recognition) 및 의도 모델의 **AppId**와 바꿉니다.

> [!NOTE]
> Speech SDK에서 지원하는 기타 서비스와 달리, 의도 인식에는 특정한 구독 키(LUIS 엔드포인트 키)가 필요합니다. 의도 인식 기술에 대한 자세한 내용은 [LUIS 웹 사이트](https://www.luis.ai)를 참조하세요. **엔드포인트 키**를 구하는 방법에 대한 정보는 [LUIS 엔드포인트 키 만들기](https://docs.microsoft.com/azure/cognitive-services/LUIS/luis-how-to-azure-subscription)를 참조하세요.
