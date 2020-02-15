---
title: 다중 전환 대화-QnA Maker
description: 프롬프트와 컨텍스트를 사용 하 여 한 질문에서 다른 질문으로의 봇에 대해 다중 턴 이라고 하는 여러 턴을 관리 합니다. 멀티 턴은 이전 질문의 컨텍스트가 다음 질문 및 답변에 영향을 주는 앞뒤로 대화를 수행할 수 있는 기능입니다.
ms.topic: conceptual
ms.date: 02/13/2020
ms.openlocfilehash: abdde09fbb1f6b066772366c5cea933824cb5864
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/14/2020
ms.locfileid: "77210417"
---
# <a name="use-follow-up-prompts-to-create-multiple-turns-of-a-conversation"></a>추가 작업 프롬프트를 사용하여 여러 대화 설정 만들기

후속 프롬프트와 컨텍스트를 사용 하 여 한 질문에서 다른 질문으로의 봇에 대해 _다중 턴_이라고 하는 여러 턴을 관리 합니다.

다중 턴이 작동 하는 방식을 확인 하려면 다음 데모 비디오를 확인 하세요.

[QnA Maker에서 다중 전환 ![](../media/conversational-context/youtube-video.png)](https://aka.ms/multiturnexample)

## <a name="what-is-a-multi-turn-conversation"></a>다중 전환 대화 란?

일부 질문은 단일 턴으로 대답할 수 없습니다. 클라이언트 응용 프로그램 (chat bot) 대화를 디자인할 때 사용자는 필터링 또는 구체화 해야 하는 질문을 하 여 올바른 답을 확인할 수 있습니다. 사용자에 게 *추가 작업 프롬프트를 제공*하 여 가능한 질문을 통해이 흐름을 만들 수 있습니다.

사용자가 질문을 하면 QnA Maker는 대답 _및_ 추가 작업 프롬프트를 반환 합니다. 이 응답을 통해 추가 질문을 선택 사항으로 표시할 수 있습니다.

> [!CAUTION]
> 다중 전환 프롬프트는 FAQ 문서에서 추출 되지 않습니다. 다중 전환 추출을 수행 해야 하는 경우 QnA 쌍을 Faq로 지정 하는 물음표를 제거 합니다.

## <a name="example-multi-turn-conversation-with-chat-bot"></a>채팅 봇을 사용 하는 다중 전환 예제

여러 턴에서 채팅 봇은 다음 이미지에 표시 된 것 처럼 최종 답변을 확인 하기 위해 사용자와 대화를 관리 합니다.

![대화를 통해 사용자에 게 안내 하는 메시지가 표시 된 다중 전환 대화 상자](../media/conversational-context/conversation-in-bot.png)

이전 이미지에서 사용자는 **내 계정을**입력 하 여 대화를 시작 했습니다. 기술 자료에는 세 개의 연결 된 질문 및 답변 쌍이 있습니다. 답변을 구체화 하기 위해 사용자는 기술 자료에서 세 가지 옵션 중 하나를 선택 합니다. 질문 (#1)에는 세 가지 옵션 (#2)으로 채팅 봇에 표시 되는 세 가지 추가 프롬프트가 있습니다.

사용자가 옵션 (#3)을 선택 하면 다음의 구체화 옵션 (#4) 목록이 표시 됩니다. 사용자가 올바른 최종 응답 (#6)을 결정할 때까지이 시퀀스는 계속 (#5) 합니다.


### <a name="use-multi-turn-in-a-bot"></a>봇에서 멀티 턴 사용

KB를 게시 한 후에는 **Bot 만들기** 단추를 선택 하 여 QnA Maker 봇을 Azure bot service에 배포할 수 있습니다. 사용자의 봇에 대해 사용 하도록 설정한 채팅 클라이언트에 프롬프트가 표시 됩니다.

## <a name="create-a-multi-turn-conversation-from-a-documents-structure"></a>문서 구조에서 다중 전환 대화 만들기

기술 자료를 만들 때 **KB** 입력 섹션에는 **url에서 멀티 턴 추출 사용, .pdf 또는 .docx 파일** 확인란이 표시 됩니다.

![다중 전환 추출을 사용 하는 확인란](../media/conversational-context/enable-multi-turn.png)

이 옵션을 선택 하면 QnA Maker 문서 구조에 있는 계층을 추출 합니다. 계층 구조는 후속 프롬프트에 표시 되 고 계층의 루트는 부모 QnA 사용 됩니다. 일부 문서에서 계층의 루트에 답변으로 사용할 수 있는 콘텐츠가 없는 경우 ' 기본 대답 텍스트 '를 제공 하 여 해당 계층을 추출 하는 대체 대답 텍스트로 사용할 수 있습니다.

다중 턴 구조는 Url, PDF 파일 또는 .DOCX 파일 에서만 유추할 수 있습니다. 구조체의 예제를 보려면 [Microsoft Surface 사용자 수동 PDF 파일](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/product-manual.pdf)의 이미지를 확인 하세요.

![! [사용자 설명서 구조의 예] (.. /media/conversational-context/import-file-with-conversational-structure.png)](../media/conversational-context/import-file-with-conversational-structure.png#lightbox)

### <a name="building-your-own-multi-turn-document"></a>사용자 고유의 다중 턴 문서 빌드

다중 턴 문서를 만드는 경우 다음 지침을 염두에 두어야 합니다.

* 머리글 및 하위 머리글을 사용 하 여 계층을 나타냅니다. 예를 들어, 부모 QnA 및 h2를 표시 하 여 프롬프트로 사용 해야 하는 QnA를 나타낼 수 있습니다. 작은 머리글 크기를 사용 하 여 후속 계층을 나타냅니다. 문서에서 구조를 의미 하는 스타일, 색 또는 기타 메커니즘을 사용 하지 않습니다. QnA Maker는 다중 전환 프롬프트를 추출 하지 않습니다.

* 제목의 첫 번째 문자는 대문자 여야 합니다.

* 물음표, `?`제목을 끝에 표시 하지 않습니다.

* 예제로 [샘플 문서](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/multi-turn.docx) 를 사용 하 여 고유한 다중 턴 문서를 만들 수 있습니다.

### <a name="adding-files-to-a-multi-turn-kb"></a>멀티 턴 KB에 파일 추가

계층 문서를 추가 하는 경우 QnA Maker는 구조에서 추가 메시지를 확인 하 여 대화형 흐름을 만듭니다.

1. QnA Maker에서 **url, .pdf 또는 .docx 파일에서 다중 전환 추출을 사용** 하 여 만든 기존 기술 자료를 선택 합니다. 사용.
1. **설정** 페이지로 이동 하 여 추가할 파일 또는 URL을 선택 합니다.
1. 기술 자료를 **저장 하 고 학습** 합니다.

> [!Caution]
> 새 기술 자료 또는 비어 있는 기술 자료에 대 한 데이터 원본으로 내보낸 TSV 또는 XLS 멀티 턴 기술 자료 파일 사용에 대 한 지원은 지원 되지 않습니다. 내보낸 다중 턴 프롬프트를 기술 자료에 추가 하려면 QnA Maker 포털의 **설정** 페이지에서 해당 파일 형식을 **가져와야** 합니다.


## <a name="create-knowledge-base-with-multi-turn-prompts-with-the-create-api"></a>API 만들기를 사용 하 여 다중 전환 프롬프트에서 기술 자료 만들기

[QNA MAKER API 만들기](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/create)를 사용 하 여 다중 전환 프롬프트가 포함 된 기술 자료를 만들 수 있습니다. 프롬프트는 `context` 속성의 `prompts` 배열에 추가 됩니다.

## <a name="show-questions-and-answers-with-context"></a>컨텍스트를 사용 하 여 질문 및 답변 표시

표시 되는 질문 및 답변 쌍을 컨텍스트 대화가 포함 된 쌍 으로만 줄입니다.

**보기 옵션**을 선택한 다음 **컨텍스트 표시**를 선택 합니다. 이 목록에는 추가 작업 프롬프트가 포함 된 질문 및 답변 쌍이 표시 됩니다.

![상황별 대화 별로 질문 및 답변 쌍 필터링](../media/conversational-context/filter-question-and-answers-by-context.png)

다중 턴 컨텍스트는 첫 번째 열에 표시 됩니다.

![! ["Context (PREVIEW)" 열] (.. /media/conversational-context/surface-manual-pdf-follow-up-prompt.png)](../media/conversational-context/surface-manual-pdf-follow-up-prompt.png#lightbox)

위의 그림에서 **#1** 는 현재 질문을 의미 하는 열의 굵은 텍스트를 나타냅니다. 부모 질문은 행의 맨 위 항목입니다. 아래 질문은 연결 된 질문 및 답변 쌍입니다. 이러한 항목은 선택할 수 있으므로 다른 컨텍스트 항목으로 바로 이동할 수 있습니다.

## <a name="add-an-existing-question-and-answer-pair-as-a-follow-up-prompt"></a>기존 질문 및 답변 쌍을 추가 작업 프롬프트로 추가

원래 질문 **내 계정**에는 **계정, 로그인**등의 추가 작업이 표시 됩니다.

!["계정 및 로그인" 답변 및 추가 작업 프롬프트](../media/conversational-context/detected-and-linked-follow-up-prompts.png)

현재 연결 되어 있지 않은 기존 질문 및 답변 쌍에 추가 작업 프롬프트를 추가 합니다. 질문 및 답변 쌍에 연결 되지 않기 때문에 현재 보기 설정을 변경 해야 합니다.

1. 기존 질문 및 답변 쌍을 추가 작업 프롬프트로 연결 하려면 질문 및 답변 쌍에 대 한 행을 선택 합니다. Surface 설명서의 경우 **로그 아웃** 을 검색 하 여 목록을 줄입니다.
1. **Signout**에 대 한 행의 **응답** 열에서 **추가 작업 프롬프트 추가**를 선택 합니다.
1. **추가 작업 프롬프트** 팝업 창의 필드에 다음 값을 입력 합니다.

    |필드|값|
    |--|--|
    |표시 텍스트|**장치 끄기를**입력 합니다. 추가 작업 프롬프트에 표시할 사용자 지정 텍스트입니다.|
    |컨텍스트 전용| 이 확인란을 선택합니다. 질문이 컨텍스트를 지정 하는 경우에만 대답이 반환 됩니다.|
    |답변에 대 한 링크|**로그인 화면 사용** 을 입력 하 여 기존 질문 및 답변 쌍을 찾습니다.|


1.  일치 항목 하나가 반환 됩니다. 이 답변을 추가 작업으로 선택한 다음, **저장**을 선택 합니다.

    !["추가 작업 프롬프트 (미리 보기)" 페이지](../media/conversational-context/search-follow-up-prompt-for-existing-answer.png)

1. 추가 작업 프롬프트를 추가한 후에는 상단 탐색에서 **저장 및 학습** 을 선택 합니다.

### <a name="edit-the-display-text"></a>표시 텍스트 편집

추가 작업 프롬프트가 만들어지고 기존 질문 및 답변 쌍이 **답변할 링크로**입력 되 면 새 **표시 텍스트**를 입력할 수 있습니다. 이 텍스트는 기존 질문을 대체 하지 않으며 새로운 대체 질문을 추가 하지 않습니다. 이러한 값은 해당 값과는 별개입니다.

1. 표시 텍스트를 편집 하려면 **컨텍스트** 필드에서 질문을 검색 하 여 선택 합니다.
1. 해당 질문에 대 한 행에서 대답 열의 추가 작업 프롬프트를 선택 합니다.
1. 편집 하려는 표시 텍스트를 선택한 다음 **편집**을 선택 합니다.

    ![표시 텍스트에 대 한 편집 명령](../media/conversational-context/edit-existing-display-text.png)

1. **추가 작업 프롬프트** 팝업 창에서 기존 표시 텍스트를 변경 합니다.
1. 표시 텍스트 편집이 완료 되 면 **저장**을 선택 합니다.
1. 위쪽 탐색 모음에서를 **저장 하 고 학습**합니다.


## <a name="add-a-new-question-and-answer-pair-as-a-follow-up-prompt"></a>새 질문 및 답변 쌍을 추가 작업 프롬프트로 추가 합니다.

새 질문 및 답변 쌍을 기술 자료에 추가 하는 경우 각 쌍을 추가 작업 프롬프트로 기존 질문에 연결 해야 합니다.

1. 기술 자료 도구 모음에서 **계정 및 로그인**에 대 한 기존 질문 및 답변 쌍을 검색 하 고 선택 합니다.

1. 이 질문에 대 한 **응답** 열에서 **추가 작업 프롬프트 추가**를 선택 합니다.
1. **추가 작업 프롬프트 (미리 보기)** 에서 다음 값을 입력 하 여 새 추가 작업 프롬프트를 만듭니다.

    |필드|값|
    |--|--|
    |표시 텍스트|*Windows 계정을 만듭니다*. 추가 작업 프롬프트에 표시할 사용자 지정 텍스트입니다.|
    |컨텍스트 전용|이 확인란을 선택합니다. 이 대답은 질문이 컨텍스트를 지정 하는 경우에만 반환 됩니다.|
    |답변에 대 한 링크|답변으로 다음 텍스트를 입력 합니다.<br>*신규 또는 기존 전자 메일 계정을 사용 하 여 Windows 계정을 [만듭니다](https://account.microsoft.com/)* .<br>데이터베이스를 저장 하 고 학습 하면이 텍스트가 변환 됩니다. |
    |||

    ![새 프롬프트 질문 및 답변 만들기](../media/conversational-context/create-child-prompt-from-parent.png)


1. **새로 만들기**를 선택한 다음, **저장**을 선택 합니다.

    이 작업은 새 질문 및 답변 쌍을 만들고 선택한 질문을 추가 작업 프롬프트로 연결 합니다. 두 질문에 대 한 **컨텍스트** 열은 후속 프롬프트 관계를 나타냅니다.

1. **보기 옵션**을 선택한 다음 [**컨텍스트 표시 (미리 보기)** ](#show-questions-and-answers-with-context)를 선택 합니다.

    새 질문에는 연결 된 방법이 나와 있습니다.

    ![새 추가 작업 프롬프트 만들기](../media/conversational-context/new-qna-follow-up-prompt.png)

    부모 질문은 선택 항목 중 하나로 새 질문을 표시 합니다.

    ![! [두 질문에 대 한 컨텍스트 열은 후속 프롬프트 관계를 나타냄] (.. /media/conversational-context/child-prompt-created.png)](../media/conversational-context/child-prompt-created.png#lightbox)

1. 추가 작업 프롬프트를 추가한 후 위쪽 탐색 모음에서 **저장 및 학습** 을 선택 합니다.

## <a name="enable-multi-turn-during-testing-of-follow-up-prompts"></a>추가 작업 프롬프트를 테스트 하는 동안 다중 전환 사용

**테스트** 창에서 추가 작업 프롬프트를 사용 하 여 질문을 테스트 하는 경우 **다중 전환 사용**을 선택 하 고 질문을 입력 합니다. 응답에는 추가 작업 프롬프트가 포함 됩니다.

![응답에는 추가 메시지가 포함 됩니다.](../media/conversational-context/test-pane-with-question-having-follow-up-prompts.png)

다중 턴을 사용 하지 않는 경우 응답이 반환 되지만 추가 작업 프롬프트가 반환 되지 않습니다.

## <a name="a-json-request-to-return-an-initial-answer-and-follow-up-prompts"></a>초기 응답 및 추가 작업 프롬프트를 반환 하는 JSON 요청

빈 `context` 개체를 사용 하 여 사용자 질문에 대 한 답변을 요청 하 고 추가 작업 프롬프트를 포함 합니다.

```JSON
{
  "question": "accounts and signing in",
  "top": 10,
  "userId": "Default",
  "isTest": false,
  "context": {}
}
```

## <a name="a-json-response-to-return-an-initial-answer-and-follow-up-prompts"></a>초기 응답 및 추가 작업 프롬프트를 반환 하는 JSON 응답

이전 섹션에서는 대답을 요청 하 고 추가 작업 프롬프트에 **계정 및 로그인**을 요청 합니다. 응답에는 *[0] 컨텍스트*및 사용자에 게 표시할 텍스트에 대 한 프롬프트 정보가 포함 됩니다.

```JSON
{
    "answers": [
        {
            "questions": [
                "Accounts and signing in"
            ],
            "answer": "**Accounts and signing in**\n\nWhen you set up your Surface, an account is set up for you. You can create additional accounts later for family and friends, so each person using your Surface can set it up just the way he or she likes. For more info, see All about accounts on Surface.com. \n\nThere are several ways to sign in to your Surface Pro 4: ",
            "score": 100.0,
            "id": 15,
            "source": "product-manual.pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": [
                    {
                        "displayOrder": 0,
                        "qnaId": 16,
                        "qna": null,
                        "displayText": "Use the sign-in screen"
                    }
                ]
            }
        },
        {
            "questions": [
                "Sign out"
            ],
            "answer": "**Sign out**\n\nHere's how to sign out: \n\n Go to Start, and right-click your name. Then select Sign out. ",
            "score": 38.01,
            "id": 18,
            "source": "product-manual.pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": [
                    {
                        "displayOrder": 0,
                        "qnaId": 16,
                        "qna": null,
                        "displayText": "Turn off the device"
                    }
                ]
            }
        },
        {
            "questions": [
                "Use the sign-in screen"
            ],
            "answer": "**Use the sign-in screen**\n\n1.  \n\nTurn on or wake your Surface by pressing the power button. \n\n2.  \n\nSwipe up on the screen or tap a key on the keyboard. \n\n3.  \n\nIf you see your account name and account picture, enter your password and select the right arrow or press Enter on your keyboard. \n\n4.  \n\nIf you see a different account name, select your own account from the list at the left. Then enter your password and select the right arrow or press Enter on your keyboard. ",
            "score": 27.53,
            "id": 16,
            "source": "product-manual.pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": []
            }
        }
    ]
}
```

`prompts` 배열은 `displayText` 속성 및 `qnaId` 값에 텍스트를 제공 합니다. 이러한 답변은 대화 흐름에서 다음에 표시 되는 선택 항목으로 표시 한 다음, 선택한 `qnaId`를 다음 요청에서 QnA Maker으로 다시 보낼 수 있습니다.

<!--

The `promptsToDelete` array provides the ...

-->

## <a name="a-json-request-to-return-a-non-initial-answer-and-follow-up-prompts"></a>초기이 아닌 응답을 반환 하는 JSON 요청 및 추가 작업 프롬프트

이전 컨텍스트를 포함 하도록 `context` 개체를 채웁니다.

다음 JSON 요청에서 현재 질문은 *Windows Hello를 사용 하 여 로그인 하* 고 이전 질문은 *계정 및 로그인*이었습니다.

```JSON
{
  "question": "Use Windows Hello to sign in",
  "top": 10,
  "userId": "Default",
  "isTest": false,
  "qnaId": 17,
  "context": {
    "previousQnAId": 15,
    "previousUserQuery": "accounts and signing in"
  }
}
```

##  <a name="a-json-response-to-return-a-non-initial-answer-and-follow-up-prompts"></a>초기이 아닌 응답을 반환 하는 JSON 응답 및 추가 작업 프롬프트

QnA Maker _Generateanswer_ JSON 응답에는 `answers` 개체에서 첫 번째 항목의 `context` 속성에 추가 작업이 표시 됩니다.

```JSON
{
    "answers": [
        {
            "questions": [
                "Use Windows Hello to sign in"
            ],
            "answer": "**Use Windows Hello to sign in**\n\nSince Surface Pro 4 has an infrared (IR) camera, you can set up Windows Hello to sign in just by looking at the screen. \n\nIf you have the Surface Pro 4 Type Cover with Fingerprint ID (sold separately), you can set up your Surface sign you in with a touch. \n\nFor more info, see What is Windows Hello? on Windows.com. ",
            "score": 100.0,
            "id": 17,
            "source": "product-manual.pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": []
            }
        },
        {
            "questions": [
                "Meet Surface Pro 4"
            ],
            "answer": "**Meet Surface Pro 4**\n\nGet acquainted with the features built in to your Surface Pro 4. \n\nHere’s a quick overview of Surface Pro 4 features: \n\n\n\n\n\n\n\nPower button \n\n\n\n\n\nPress the power button to turn your Surface Pro 4 on. You can also use the power button to put it to sleep and wake it when you’re ready to start working again. \n\n\n\n\n\n\n\nTouchscreen \n\n\n\n\n\nUse the 12.3” display, with its 3:2 aspect ratio and 2736 x 1824 resolution, to watch HD movies, browse the web, and use your favorite apps. \n\nThe new Surface G5 touch processor provides up to twice the touch accuracy of Surface Pro 3 and lets you use your fingers to select items, zoom in, and move things around. For more info, see Surface touchscreen on Surface.com. \n\n\n\n\n\n\n\nSurface Pen \n\n\n\n\n\nEnjoy a natural writing experience with a pen that feels like an actual pen. Use Surface Pen to launch Cortana in Windows or open OneNote and quickly jot down notes or take screenshots. \n\nSee Using Surface Pen (Surface Pro 4 version) on Surface.com for more info. \n\n\n\n\n\n\n\nKickstand \n\n\n\n\n\nFlip out the kickstand and work or play comfortably at your desk, on the couch, or while giving a hands-free presentation. \n\n\n\n\n\n\n\nWi-Fi and Bluetooth® \n\n\n\n\n\nSurface Pro 4 supports standard Wi-Fi protocols (802.11a/b/g/n/ac) and Bluetooth 4.0. Connect to a wireless network and use Bluetooth devices like mice, printers, and headsets. \n\nFor more info, see Add a Bluetooth device and Connect Surface to a wireless network on Surface.com. \n\n\n\n\n\n\n\nCameras \n\n\n\n\n\nSurface Pro 4 has two cameras for taking photos and recording video: an 8-megapixel rear-facing camera with autofocus and a 5-megapixel, high-resolution, front-facing camera. Both cameras record video in 1080p, with a 16:9 aspect ratio. Privacy lights are located on the right side of both cameras. \n\nSurface Pro 4 also has an infrared (IR) face-detection camera so you can sign in to Windows without typing a password. For more info, see Windows Hello on Surface.com. \n\nFor more camera info, see Take photos and videos with Surface and Using autofocus on Surface 3, Surface Pro 4, and Surface Book on Surface.com. \n\n\n\n\n\n\n\nMicrophones \n\n\n\n\n\nSurface Pro 4 has both a front and a back microphone. Use the front microphone for calls and recordings. Its noise-canceling feature is optimized for use with Skype and Cortana. \n\n\n\n\n\n\n\nStereo speakers \n\n\n\n\n\nStereo front speakers provide an immersive music and movie playback experience. To learn more, see Surface sound, volume, and audio accessories on Surface.com. \n\n\n\n\n",
            "score": 21.92,
            "id": 3,
            "source": "product-manual.pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": [
                    {
                        "displayOrder": 0,
                        "qnaId": 4,
                        "qna": null,
                        "displayText": "Ports and connectors"
                    }
                ]
            }
        },
        {
            "questions": [
                "Use the sign-in screen"
            ],
            "answer": "**Use the sign-in screen**\n\n1.  \n\nTurn on or wake your Surface by pressing the power button. \n\n2.  \n\nSwipe up on the screen or tap a key on the keyboard. \n\n3.  \n\nIf you see your account name and account picture, enter your password and select the right arrow or press Enter on your keyboard. \n\n4.  \n\nIf you see a different account name, select your own account from the list at the left. Then enter your password and select the right arrow or press Enter on your keyboard. ",
            "score": 19.04,
            "id": 16,
            "source": "product-manual.pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": []
            }
        }
    ]
}
```

## <a name="query-the-knowledge-base-with-the-qna-maker-id"></a>QnA Maker ID를 사용 하 여 기술 자료 쿼리

다중 턴 기능을 사용 하 여 사용자 지정 응용 프로그램을 빌드하는 경우 초기 질문의 응답에서 추가 작업 프롬프트와 관련 `qnaId`이 반환 됩니다. 이제 ID가 있으므로 추가 작업 프롬프트의 요청 본문에서이 ID를 전달할 수 있습니다. 요청 본문에 `qnaId`및 컨텍스트 개체 (이전 QnA Maker 속성 포함)가 포함 된 경우 GenerateAnswer는 순위 알고리즘을 사용 하 여 질문 텍스트에서 답변을 찾는 대신 ID를 사용 하 여 정확한 질문을 반환 합니다.


## <a name="display-order-is-supported-in-the-update-api"></a>표시 순서는 업데이트 API에서 지원 됩니다.

JSON 응답에서 반환 되는 [표시 텍스트와 표시 순서](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update#promptdto)는 [업데이트 API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update)에서 편집할 수 있도록 지원 됩니다.

## <a name="add-or-delete-multi-turn-prompts-with-the-update-api"></a>업데이트 API를 사용 하 여 다중 전환 프롬프트 추가 또는 삭제

[QnA Maker 업데이트 API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update)를 사용 하 여 다중 전환 프롬프트를 추가 하거나 삭제할 수 있습니다.  프롬프트는 `context` 속성의 `promptsToAdd` 배열 및 `promptsToDelete` 배열에 추가 됩니다.

## <a name="export-knowledge-base-for-version-control"></a>버전 제어를 위한 기술 자료 내보내기

QnA Maker은 내보낸 파일에 다중 전환 대화 단계를 포함 하 여 버전 제어를 지원 합니다.

## <a name="next-steps"></a>다음 단계

이 [대화 상자의](https://aka.ms/qnamakermultiturnsample) 상황별 대화에 대 한 자세한 내용을 보거나, [다중 전환 대화를 위한 개념 봇 디자인](https://docs.microsoft.com/azure/bot-service/bot-builder-conversations?view=azure-bot-service-4.0)에 대해 자세히 알아보세요.

> [!div class="nextstepaction"]
> [기술 자료 마이그레이션](../Tutorials/migrate-knowledge-base.md)
