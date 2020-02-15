---
title: '자습서: QnA 봇 - Azure Bot Service - QnA Maker'
titleSuffix: Azure Cognitive Services
description: 이 자습서에서는 기존 기술 자료의 게시 페이지에서 QnA 채팅 봇을 만드는 방법을 보여줍니다. 이 봇에는 Bot Framework SDK v4가 사용됩니다. 봇 빌드를 위해 코드를 작성할 필요가 없습니다. 모든 코드가 제공됩니다.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: tutorial
ms.date: 01/28/2020
ms.author: diberry
ms.openlocfilehash: a9afef07af1332910cc9e25f9d66db5930df3bd4
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/31/2020
ms.locfileid: "76901666"
---
# <a name="tutorial-create-a-qna-bot-with-azure-bot-service-v4"></a>자습서: Azure Bot Service v4로 QnA 봇 만들기

기존 기술 자료의 **게시** 페이지에서 QnA 채팅 봇을 만듭니다. 이 봇에는 Bot Framework SDK v4가 사용됩니다. 봇 빌드를 위해 코드를 작성할 필요가 없습니다. 모든 코드가 제공됩니다.

**이 자습서에서 학습할 내용은 다음과 같습니다.**

<!-- green checkmark -->
> [!div class="checklist"]
> * 기존 기술 자료에서 Azure Bot Service 만들기
> * 봇과 채팅하면서 코드가 작동 중인지 확인

## <a name="prerequisites"></a>사전 요구 사항

이 자습서에 대해 게시된 기술 자료가 있어야 합니다. 해당 항목이 없으면 [KB에서 만들기 및 대답](create-publish-query-in-portal.md)의 단계에 따라 질문과 답변이 있는 QnA Maker 기술 자료를 만듭니다.

<a name="create-a-knowledge-base-bot"></a>

## <a name="create-a-qna-bot"></a>QnA 봇 만들기

기술 자료에 대한 클라이언트 애플리케이션으로 봇을 만듭니다.

1. QnA Maker 포털에서 **게시** 페이지로 이동하여 기술 자료를 게시합니다. **봇 만들기**를 선택합니다.

    [![QnA Maker 포털에서 게시 페이지로 이동하여 기술 자료를 게시합니다. 봇 만들기를 선택합니다.](../media/qnamaker-tutorials-create-bot/create-bot-from-published-knowledge-base-page.png)](../media/qnamaker-tutorials-create-bot/create-bot-from-published-knowledge-base-page.png#lightbox)

    Azure Portal이 봇 만들기 구성으로 열립니다.

1.  봇을 만들 설정을 입력합니다.

    |설정|값|목적|
    |--|--|--|
    |봇 핸들|`qna-maker-central-us-bot`|봇에 대한 Azure 리소스 이름입니다.|
    |Subscription|목적을 참조하세요.|QnA Maker 리소스를 만드는 데 사용한 것과 동일한 구독을 선택합니다.|
    |Resource group|`docs`|모든 봇 관련 Azure 리소스에 사용되는 리소스 그룹입니다.|
    |위치|`west us`|봇의 Azure 리소스 위치입니다.|
    |가격 책정 계층|`F0`|Azure Bot Service에 대한 무료 계층입니다.|
    |앱 이름|`qna-maker-central-us-bot-app`|봇만 지원하는 웹앱입니다. QnA Maker 서비스에 이미 사용된 것과 동일한 앱 이름이 아니어야 합니다. QnA Maker의 웹앱을 다른 리소스와 공유하는 것은 지원되지 않습니다.|
    |SDK 언어|C#|Bot Framework SDK에 사용되는 기본 프로그래밍 언어입니다. [C#](https://github.com/Microsoft/botbuilder-dotnet) 또는 [Node.js](https://github.com/Microsoft/botbuilder-js) 중에서 선택할 수 있습니다.|
    |QnA 인증 기|**변경 안 함**|이 값은 채워집니다.|
    |앱 서비스 플랜/위치|**변경 안 함**|이 자습서에서는 위치가 중요하지 않습니다.|
    |Application Insights|**변경 안 함**|로깅은 Application Insights로 전송됩니다.|
    |Microsoft 앱 ID|**변경 안 함**|Active Directory 사용자 및 암호가 필요합니다.|

    > [!div class="mx-imgBorder"]
    > ![이러한 설정으로 기술 자료 봇을 만듭니다.](../media/qnamaker-tutorials-create-bot/create-bot-from-published-knowledge-base.png)

    봇 만들기 프로세스 알림이 성공을 보고할 때까지 2분 정도 기다립니다.

<a name="test-the-bot"></a>

## <a name="chat-with-the-bot"></a>봇과 채팅

1. Azure Portal의 알림에서 새 봇 리소스를 엽니다.

    ![Azure Portal의 알림에서 새 봇 리소스를 엽니다.](../media/qnamaker-tutorials-create-bot/azure-portal-notifications.png)

1. **봇 관리**에서 **웹 채팅에서 테스트**를 선택하고 `How large can my KB be?`를 입력합니다. 봇은 다음과 같이 응답합니다.


    `The size of the knowledge base depends on the SKU of Azure search you choose when creating the QnA Maker service. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/tutorials/choosing-capacity-qnamaker-deployment)for more details.`


    ![새 기술 자료 봇을 테스트합니다.](../media/qnamaker-tutorial-create-publish-query-in-portal/test-bot-in-web-chat-in-azure-portal.png)

    Azure Bot에 대한 자세한 내용은 [QnA Maker를 사용하여 질문에 답변](https://docs.microsoft.com/azure/bot-service/bot-builder-howto-qna?view=azure-bot-service-4.0&tabs=cs)을 참조하세요.

## <a name="clean-up-resources"></a>리소스 정리

이 자습서의 봇 작업이 완료되면 Azure Portal에서 해당 봇을 제거합니다.

봇의 리소스에 대한 새 리소스 그룹을 만든 경우 리소스 그룹을 삭제합니다.

새 리소스 그룹을 만들지 않은 경우, 봇과 연결된 리소스를 찾아야 합니다. 가장 쉬운 방법은 봇과 봇 앱의 이름으로 검색하는 것입니다. 봇 리소스에는 다음이 포함됩니다.

* App Service 플랜
* 검색 서비스
* Cognitive Service
* App service
* 필요에 따라 Application Insights 서비스 및 Application Insights 데이터용 스토리지를 포함할 수도 있습니다.


## <a name="related-to-qna-maker-bots"></a>QnA Maker 봇 관련

* [헬스케어 봇](https://docs.microsoft.com/HealthBot/qna_model_howto)에는 QnA Maker가 [언어 모델](https://docs.microsoft.com/HealthBot/qna_model_howto) 중 하나로 사용됩니다.


[!INCLUDE [Bot Information](../../../../includes/cognitive-services-qnamaker-luis-bot-info.md)]

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [개념: 기술 자료](../concepts/knowledge-base.md)

## <a name="see-also"></a>참고 항목

- [Manage your knowledge base](https://qnamaker.ai)(기술 자료 관리)
- [Enable your bot in different channels](https://docs.microsoft.com/azure/bot-service/bot-service-manage-channels)(다양한 채널에서 봇 사용)
