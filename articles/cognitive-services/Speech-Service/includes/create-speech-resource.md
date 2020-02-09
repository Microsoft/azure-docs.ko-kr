---
title: 컨테이너 지원
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 7/5/2019
ms.author: dapine
ms.openlocfilehash: 3c42bf2b2acc2472741bd603ea9d653a314ecc40
ms.sourcegitcommit: 3f8017692169bd75483eefa96c225d45cd497f06
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73523414"
---
## <a name="create-a-speech-resource"></a>음성 리소스 만들기

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. [ **음성** 리소스 만들기](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices) 를 클릭 합니다.
1. 모든 필수 설정을 입력 합니다.

    |설정|값|
    |--|--|
    |Name|원하는 이름 (2-64 자)|
    |구독|적절 한 구독 선택|
    |위치|주변 및 사용 가능한 위치를 선택 합니다.|
    |가격 책정 계층|`F0`-최소 가격 책정 계층|
    |리소스 그룹|사용 가능한 리소스 그룹 선택|

1. **만들기** 를 클릭 하 고 리소스가 생성 될 때까지 기다립니다. 만든 후에는 리소스 페이지로 이동 합니다.
1. 구성 된 `endpoint` 및 API 키를 수집 합니다.

    |포털의 리소스 탭|설정|값|
    |--|--|--|
    |**개요**|엔드포인트|엔드포인트을 복사 합니다. 다음과 같이 표시 됩니다 `https://speech.cognitiveservices.azure.com/sts/v1.0/issuetoken`|
    |**키**|API 키|두 키의 1을 복사 합니다. `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`공백이 나 대시가 없는 32 영숫자 문자열입니다.|
