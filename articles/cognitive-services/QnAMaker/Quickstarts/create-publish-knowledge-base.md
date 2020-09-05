---
title: '빠른 시작: 기술 자료 만들기, 교육 및 게시 - QnA Maker'
description: FAQ 또는 제품 설명서 등, 사용자 고유의 콘텐츠에서 QnA Maker 기술 자료(KB)를 만들 수 있습니다. 이 문서에는 간단한 FAQ 웹 페이지에서 QnA Maker 기술 자료를 만들어 QnA Maker 질문에 답변하는 예가 포함되어 있습니다.
ms.topic: quickstart
ms.date: 05/26/2020
ms.openlocfilehash: 77ddb2a0a149729af72066b53c17b1dcb08a2992
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88540757"
---
# <a name="quickstart-create-train-and-publish-your-qna-maker-knowledge-base"></a>빠른 시작: QnA Maker 기술 자료 만들기, 학습 및 게시

FAQ 또는 제품 설명서 등, 사용자 고유의 콘텐츠에서 QnA Maker 기술 자료(KB)를 만들 수 있습니다. 이 문서에는 간단한 FAQ 웹 페이지에서 QnA Maker 기술 자료를 만들어 QnA Maker 질문에 답변하는 예가 포함되어 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

> [!div class="checklist"]
> * Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/cognitive-services/)을 만듭니다.
> * Azure Portal에서 만든 QnA Maker [리소스](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker)입니다. 리소스를 만들 때 선택한 Azure Active Directory ID, 구독, QnA 리소스 이름을 기억하세요.

## <a name="create-your-first-qna-maker-knowledge-base"></a>첫 번째 QnA Maker 기술 자료 만들기

1. Azure 자격 증명으로 [QnAMaker.ai](https://QnAMaker.ai) 포털에 로그인합니다.

1. QnA Maker 포털에서 **기술 자료 만들기**를 선택합니다.

1. **만들기** 페이지에서 QnA Maker 리소스가 이미 있는 경우 **1단계**를 건너뜁니다.

    리소스를 아직 만들지 않은 경우 **QnA 서비스 만들기**를 선택합니다. 그러면 구독에서 QnA Maker 서비스를 설정할 수 있는 [Azure Portal](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker)로 연결됩니다. 리소스를 만들 때 선택한 Azure Active Directory ID, 구독, QnA 리소스 이름을 기억하세요.

    Azure Portal에서 리소스 만들기를 완료하면 QnA Maker 포털로 돌아가서 브라우저 페이지를 새로 고치고 **2단계**를 계속 진행합니다.

1. **3단계**에서는 Active 디렉터리, 구독, 서비스(리소스) 및 서비스에서 만든 모든 기술 자료에 대한 언어를 선택합니다.

    :::image type="content" source="../media/qnamaker-create-publish-knowledge-base/qnaservice-selection.png" alt-text="QnA Maker 서비스 기술 자료를 선택하는 스크린샷":::

1. **3단계**에서는 기술 자료의 이름을 **내 샘플 QnA KB**로 지정합니다.

1. **4단계**에서는 다음 표를 사용하여 설정을 구성합니다.

    |설정|값|
    |--|--|
    |**URL, .pdf 또는 .docx 파일에서 다중 턴 추출을 사용하도록 설정합니다.**|선택|
    |**기본 답변 텍스트**| `Quickstart - default answer not found.`|
    |**+ URL 추가**|`https://docs.microsoft.com/azure/cognitive-services/qnamaker/troubleshooting`|
    |**잡담**|**Professional** 선택|

1. **5단계**에서는 **KB 만들기**를 선택합니다.

    추출 프로세스는 문서를 읽고 질문과 답변을 확인하는 데 몇 분 정도가 걸립니다.

    QnA Maker가 기술 자료를 성공적으로 만들면 **기술 자료** 페이지가 열립니다. 이 페이지에서 기술 자료의 내용을 편집할 수 있습니다.

## <a name="add-a-new-question-and-answer-set"></a>새 질문 및 답변 집합 추가

1. QnA Maker 포털에서 **편집** 페이지의 상황에 맞는 도구 모음에서 **QnA 쌍 추가**를 선택합니다.
1. 다음 질문을 추가합니다.

    `How many Azure services are used by a knowledge base?`

1. _markdown_으로 서식이 지정된 답변을 추가합니다.

    ` * Azure QnA Maker service\n* Azure Cognitive Search\n* Azure web app\n* Azure app plan`

    :::image type="content" source="../media/qnamaker-create-publish-knowledge-base/add-question-and-answer.png" alt-text="질문을 텍스트로 추가하고 답변을 markdown으로 서식을 지정합니다.":::

    markdown 기호 `*`는 글머리 기호에 사용됩니다. `\n`은 새 줄에 사용됩니다.

    **편집** 페이지에 markdown이 표시됩니다. 나중에 **테스트** 패널을 사용하면 markdown이 올바르게 표시됩니다.

## <a name="save-and-train"></a>저장 후 학습

오른쪽 위에서 **저장 후 학습**을 선택하여 편집 내용을 저장하고 QnA Make를 학습합니다. 저장하지 않으면 편집 내용은 보관되지 않습니다.

## <a name="test-the-knowledge-base"></a>기술 자료 테스트

1. QnA Maker 포털의 오른쪽 위에서 **테스트**를 선택하여 변경 내용이 적용되었는지 테스트합니다.
1. 텍스트 상자에 예제 사용자 쿼리를 입력합니다.

    `How many Azure services are used by a knowledge base?`

    :::image type="content" source="../media/qnamaker-create-publish-knowledge-base/test-panel-in-qna-maker.png" alt-text="텍스트 상자에 예제 사용자 쿼리를 입력합니다.":::

1. **검사**를 선택하여 응답을 더 자세히 살펴봅니다. 테스트 창은 기술 자료를 게시하기 전에 기술 자료의 변경 내용을 테스트하는 데 사용됩니다.

1. **테스트**를 다시 선택하여 **테스트** 패널을 닫습니다.

## <a name="publish-the-knowledge-base"></a>기술 자료 게시

기술 자료를 게시하면 기술 자료의 콘텐츠가 `test` 인덱스에서 Azure 검색의 `prod` 인덱스로 이동합니다.

![기술 자료의 콘텐츠를 이동하는 스크린샷](../media/qnamaker-how-to-publish-kb/publish-prod-test.png)

1. QnA Maker 포털에서 **게시**를 선택합니다. 그런 다음, 확인하려면 페이지에서 **게시**를 선택합니다.

    이제 QnA Maker 서비스가 성공적으로 게시되었습니다. 애플리케이션 또는 봇 코드에서 엔드포인트를 사용할 수 있습니다.

    ![성공적인 게시의 스크린샷](../media/qnamaker-create-publish-knowledge-base/publish-knowledge-base-to-endpoint.png)

## <a name="create-a-bot"></a>봇 만들기

게시 후에는 **게시** 페이지에서 봇을 만들 수 있습니다.

* 개별 봇의 여러 Azure 지역 또는 가격 책정 계획에 대한 동일한 기술 자료를 가리키는 여러 봇을 신속하게 만들 수 있습니다.
* 기술 자료에 봇을 하나만 사용하려면 **Azure Portal에서 모든 봇 보기** 링크를 사용하여 현재 봇 목록을 봅니다.

기술 자료를 변경하고 다시 게시하는 경우 봇에 다른 조치를 취할 필요가 없습니다. 이미 기술 자료와 함께 작동하도록 구성되어 있으며 향후 모든 기술 자료 변경 내용과 호환됩니다. 기술 자료를 게시할 때마다 기술 자료에 연결된 모든 봇이 자동으로 업데이트됩니다.

1. QnA Maker 포털의 **게시** 페이지에서 **봇 만들기**를 선택합니다. 이 단추는 기술 자료를 게시한 후에만 표시됩니다.

    ![봇 만들기 스크린샷](../media/qnamaker-create-publish-knowledge-base/create-bot-from-published-knowledge-base-page.png)

1. 새 브라우저 탭에서 Azure Portal의 Azure Bot Service 만들기 페이지가 열립니다. Azure Bot Service를 구성합니다. 봇과 QnA Maker는 웹앱 서비스 플랜을 공유할 수 있지만, 웹앱을 공유할 수는 없습니다. 즉, 봇의 **앱 이름**이 QnA Maker 서비스의 앱 이름과 달라야 합니다.

    * **해야 할 일**
        * 고유하지 않은 경우 봇 핸들을 변경합니다.
        * SDK 언어를 선택합니다. 봇이 만들어지면 로컬 개발 환경에 코드를 다운로드하고 개발 프로세스를 계속할 수 있습니다.
    * **안 함**
        * 봇을 만들 때 Azure Portal에서 다음 설정을 변경하지 마세요. 기존 기술 자료에 대한 내용이 미리 채워집니다.
           * QnA 인증 기
           * 앱 서비스 플랜 및 위치


1. 봇을 만든 후 **봇 서비스** 리소스를 엽니다.
1. **봇 관리**에서 **웹 채팅에서 테스트**를 선택합니다.
1. **메시지 입력**의 채팅 프롬프트에서 다음을 입력합니다.

    `Azure services?`

    채팅 봇은 기술 자료의 답변으로 응답합니다.

    :::image type="content" source="../media/qnamaker-create-publish-knowledge-base/test-web-chat.png" alt-text="테스트 웹 채팅에 사용자 쿼리를 입력합니다.":::

## <a name="what-did-you-accomplish"></a>수행했던 작업은 무엇인가요?

새 기술 자료를 만들고, 기술 자료에 공용 URL을 추가하고, 사용자 고유의 QnA 쌍을 추가하고, 기술 자료를 학습, 테스트 및 게시했습니다.

기술 자료를 게시한 후 봇을 만들고, 봇을 테스트했습니다.

이 작업은 코드를 작성하거나 콘텐츠를 정리하지 않고도 모두 몇 분 내에 완료되었습니다.

## <a name="clean-up-resources"></a>리소스 정리

다음 빠른 시작을 계속 진행하지 않을 경우 Azure Portal에서 QnA Maker 및 Bot 프레임워크 리소스를 삭제합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [메타데이터를 사용하여 질문 추가](add-question-metadata-portal.md)

자세한 내용은 다음을 참조하세요.

* [답변의 Markdown 형식](../reference-markdown-format.md)
* QnA Maker [ 데이터 원본](../concepts/knowledge-base.md).


