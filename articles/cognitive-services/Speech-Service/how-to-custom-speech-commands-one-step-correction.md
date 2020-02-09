---
title: '방법: 사용자 지정 명령에 1 단계 수정 추가 (미리 보기)-음성 서비스'
titleSuffix: Azure Cognitive Services
description: 이 문서에서는 사용자 지정 명령의 명령에 대해 1 단계 수정을 구현 하는 방법에 대해 설명 합니다.
services: cognitive-services
author: encorona-ms
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/05/2019
ms.author: encorona
ms.openlocfilehash: 86a12bd1dccc2b6ac15010546d7e990b768ebc02
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75462390"
---
# <a name="how-to-add-a-one-step-correction-to-a-custom-command-preview"></a>방법: 사용자 지정 명령에 1 단계 수정 추가 (미리 보기)

이 문서에서는 명령에 1 단계 확인을 추가 하는 방법에 대해 알아봅니다.

1 단계 수정은 방금 완료 된 명령을 업데이트 하는 데 사용 됩니다.

즉, 경보를 설정 하는 경우 주의를 변경 하 고 경보 시간을 업데이트할 수 있습니다.

- 입력: 내일 정오에 경보 설정
- 출력: "확인, 12/06/2019 12:00:00에 대 한 경보 설정"
- 입력: 아니요, 내일 오후 1 시
- 출력: "정상

이는 개발자에 게 백 엔드 응용 프로그램에서 경보를 업데이트 하는 메커니즘이 있음을 의미 합니다.

## <a name="prerequisites"></a>필수 조건

다음 문서의 단계를 완료 해야 합니다.

- [빠른 시작: 사용자 지정 명령 만들기 (미리 보기)](./quickstart-custom-speech-commands-create-new.md)
- [빠른 시작: 매개 변수를 사용 하 여 사용자 지정 명령 만들기 (미리 보기)](./quickstart-custom-speech-commands-create-parameters.md)
- [방법: 사용자 지정 명령에 확인 추가 (미리 보기)](./how-to-custom-speech-commands-confirmations.md)

## <a name="add-the-advanced-rules-for-one-step-correction"></a>1 단계 수정에 대 한 고급 규칙 추가 

1 단계 수정을 시연 하기 위해 확인 [방법](./how-to-custom-speech-commands-confirmations.md)에서 만든 **setalarm** 명령을 확장 해 보겠습니다.
 
1. 고급 규칙을 추가 하 여 이전 경보를 업데이트 합니다. 

    이 규칙은 사용자에 게 알람의 날짜 및 시간을 확인 하 고 다음 턴에 확인 (예/아니요)을 예상 하도록 요청 합니다.

   | 설정               | 제안 값                                                  | Description                                        |
   | --------------------- | ---------------------------------------------------------------- | -------------------------------------------------- |
   | 규칙 이름             | 이전 경보 업데이트                                            | 규칙의 용도를 설명 하는 이름입니다.          |
   | 조건            | UpdateLastCommand & 필수 매개 변수-DateTime                | 규칙을 실행할 수 있는 시기를 결정 하는 조건    |   
   | 작업               | SpeechResponse-"-이전 경보를 {DateTime}" (으)로 업데이트       | 규칙 조건이 참인 경우 수행할 동작입니다. |
   | 실행 후의 상태 | Complete 명령                                                 | 턴 후의 사용자 상태                   |

1. 앞서 만든 규칙을 고급 규칙의 위쪽으로 이동 합니다 (패널에서 규칙을 스크롤하고 위쪽 화살표 클릭).
   > [!div class="mx-imgBorder"]
   > 범위 유효성 검사를 추가 ![](media/custom-speech-commands/one-step-correction-rules.png)

> [!NOTE]
> 실제 응용 프로그램에서이 규칙의 작업 섹션에서는 클라이언트에 작업을 다시 보내거나 HTTP 엔드포인트을 호출 하 여 시스템의 경보를 업데이트 합니다.

## <a name="try-it-out"></a>사용해 보기

테스트 패널을 선택 하 고 몇 가지 상호 작용을 시도 합니다.

- 입력: 내일 정오에 경보 설정
- 출력: "12/07/2019 12:00:00에 대 한 경보를 설정 하 시겠습니까?"
- 입력: 예
- 출력: "확인, 12/07/2019 12:00:00에 대 한 경보 설정"
- 입력: 아니요, 내일 오후 1 시
- 출력: "이전 경보를 12/07/2019 13:00:00로 업데이트"
