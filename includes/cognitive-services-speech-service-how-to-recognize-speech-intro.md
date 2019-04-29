---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
origin.date: 09/24/2018
ms.date: 04/01/2019
ms.author: v-biyu
ms.openlocfilehash: 31482cc7850c574b952c454021af729da324ba15
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60769941"
---
<!-- N.B. no header, no intents here, language-agnostic -->

Cognitive Services [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md)는 전체 기능과 함께 애플리케이션에서 **음성 텍스트 변환**을 사용하는 가장 간단한 방법을 제공합니다.

1. 음성 구성을 만들고 Speech Service 구독 키(또는 권한 부여 토큰) 및 [지역](~/articles/cognitive-services/speech-service/regions.md)을 매개 변수로 제공합니다. 필요에 따라 구성을 변경합니다. 예를 들어 사용자 지정 엔드포인트를 제공하여 비표준 서비스 엔드포인트를 지정하거나, 음성 입력 언어 또는 출력 형식을 선택합니다.

1. 음성 구성에서 음성 인식기를 만듭니다. 기본 마이크 이외의 원본에서 인식하는 경우(예: 오디오 스트림 또는 오디오 파일) 오디오 구성을 제공합니다.

1. 필요한 경우, 비동기 작업에 대한 이벤트를 연결합니다. 중간 및 최종 결과가 있으면 인식기가 이벤트 처리기를 호출합니다. 그렇지 않을 경우 애플리케이션은 최종 전사 결과만 받습니다.

1. 인식을 시작합니다. 명령 또는 쿼리 인식과 같은 1단계 인식의 경우 `RecognizeOnceAsync()`(또는 해당 언어) 메서드를 사용합니다. 이 메서드는 처음 인식된 발언을 반환합니다. 전사와 같은 장기 실행 인식의 경우 `StartContinuousRecognitionAsync()`(또는 해당 언어) 메서드를 사용합니다. 비동기 인식 결과 대한 이벤트를 연결합니다.

Speech SDK를 사용하는 음성 인식 시나리오는 다음 코드 조각을 참조하십시오.

[!INCLUDE [Get a subscription key](cognitive-services-speech-service-get-subscription-key.md)]
