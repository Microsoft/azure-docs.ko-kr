---
title: '자습서: Facebook 콘텐츠 조정 - Content Moderator'
titleSuffix: Azure Cognitive Services
description: 이 자습서에서는 기계 학습 기반 Content Moderator를 사용하여 Facebook 게시물 및 의견을 조정하는 방법을 알아봅니다.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: tutorial
ms.date: 05/27/2020
ms.author: pafarley
ms.openlocfilehash: 9d8801037be55a262268afcd6e8f5751d158c76e
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88548517"
---
# <a name="tutorial-moderate-facebook-posts-and-commands-with-azure-content-moderator"></a>자습서: Azure Content Moderator를 사용하여 Facebook 게시물 및 의견 조정

이 자습서에서는 Azure Content Moderator를 사용하여 Facebook 페이지의 게시물 및 의견을 조정하는 방법을 알아봅니다. Facebook은 방문자가 게시한 콘텐츠를 Content Moderator 서비스로 보냅니다. 그러면 콘텐츠 점수 및 임계값에 따라 Content Moderator 워크플로에서 콘텐츠를 게시하거나 검토 도구 내에서 검토를 만듭니다. 이 시나리오의 작업 예제를 보려면 [Build 2017 데모 비디오](https://channel9.msdn.com/Events/Build/2017/T6033)를 참조하세요.

이 자습서에서는 다음을 수행하는 방법에 대해 설명합니다.

> [!div class="checklist"]
> * Content Moderator 팀을 만듭니다.
> * Content Moderator 및 Facebook에서 HTTP 이벤트에 대해 수신 대기하는 Azure Functions를 만듭니다.
> * Facebook 애플리케이션을 사용하여 Facebook 페이지를 Content Moderator에 연결합니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/cognitive-services/)을 만듭니다.

이 다이어그램은 이 시나리오의 각 구성 요소를 보여 줍니다.

!["FBListener"를 통해 Facebook에서 정보를 수신하고 "CMListener"를 통해 정보를 전송하는 Content Moderator 다이어그램](images/tutorial-facebook-moderation.png)

> [!IMPORTANT]
> 2018년에 Facebook은 Facebook 앱에 대해 보다 엄격한 심사 정책을 구현했습니다. Facebook 검토 팀에서 앱을 검토하고 승인하지 않으면 이 자습서의 단계를 완료할 수 없습니다.

## <a name="prerequisites"></a>필수 구성 요소

- Content Moderator 구독 키. [Cognitive Services 계정 만들기](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)의 지침에 따라 Content Moderator 서비스를 구독하고 키를 가져옵니다.
- [Facebook 계정](https://www.facebook.com/)

## <a name="create-a-review-team"></a>검토 팀 만들기

[Content Moderator 검토 도구](https://contentmoderator.cognitive.microsoft.com/)에 가입하고 검토 팀을 만드는 방법에 대한 지침은 [웹에서 Content Moderator 사용해 보기](quick-start.md) 빠른 시작을 참조하세요. **자격 증명** 페이지에서 **팀 ID** 값을 기록해 둡니다.

## <a name="configure-image-moderation-workflow"></a>이미지 중재 워크플로 구성

[워크플로 정의, 테스트 및 사용](review-tool-user-guide/workflows.md) 가이드를 참조하여 사용자 지정 이미지 워크플로를 만드세요. Content Moderator는 이 워크플로를 사용하여 Facebook의 이미지를 자동으로 확인하고 일부를 검토 도구로 보냅니다. 워크플로 **이름**을 기록해 둡니다.

## <a name="configure-text-moderation-workflow"></a>텍스트 중재 워크플로 구성

[워크플로 정의, 테스트 및 사용](review-tool-user-guide/workflows.md) 가이드를 다시 참조하세요. 이번에는 사용자 지정 텍스트 워크플로를 만듭니다. Content Moderator는 이 워크플로를 사용하여 텍스트 콘텐츠를 자동으로 확인합니다. 워크플로 **이름**을 기록해 둡니다.

![텍스트 워크플로 구성](images/text-workflow-configure.PNG)

**워크플로 실행** 단추를 사용하여 워크플로를 테스트합니다.

![텍스트 워크플로 테스트](images/text-workflow-test.PNG)

## <a name="create-azure-functions"></a>Azure Functions 만들기

[Azure Portal](https://portal.azure.com/)에 로그인하고 다음 단계를 따릅니다.

1. [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-create-function-app-portal) 페이지에 표시된 대로 Azure 함수 앱을 만듭니다.
1. 새로 만든 함수 앱으로 이동합니다.
1. App 내에서 **플랫폼 기능** 탭으로 이동한 후 **구성**을 선택합니다. 다음 페이지의 **애플리케이션 설정** 섹션에서 **새 애플리케이션 설정**을 선택하여 다음 키/값 쌍을 추가합니다.
    
    | 앱 설정 이름 | 값   | 
    | -------------------- |-------------|
    | `cm:TeamId`   | Content Moderator 팀 ID  | 
    | `cm:SubscriptionKey` | Content Moderator 구독 키 - [자격 증명](review-tool-user-guide/credentials.md) 참조 |
    | `cm:Region` | Content Moderator 지역 이름(공백 없이) 이 이름은 Azure 리소스의 **개요** 탭에 있는 **위치** 필드에서 찾을 수 있습니다.|
    | `cm:ImageWorkflow` | 이미지에서 실행할 워크플로의 이름 |
    | `cm:TextWorkflow` | 텍스트에서 실행할 워크플로의 이름 |
    | `cm:CallbackEndpoint` | 이 가이드의 뒷부분에서 만드는 CMListener 함수 앱에 대한 Url |
    | `fb:VerificationToken` | Facebook 피드 이벤트 구독에 사용되는 사용자가 만든 비밀 토큰 |
    | `fb:PageAccessToken` | Facebook 그래프 API 액세스 토큰은 만료되지 않으며 사용자를 대신하여 함수 게시 숨기기/삭제를 허용합니다. 이후 단계에서 이 토큰을 가져옵니다. |

    페이지 위쪽에 있는 **저장** 단추를 클릭합니다.

1. **플랫폼 기능** 탭으로 돌아갑니다. 왼쪽 창에 있는 **+** 단추를 사용하여 **새 함수** 창을 표시합니다. 만들려는 함수가 Facebook에서 이벤트를 받게 됩니다.

    ![함수 추가 단추가 강조 표시된 Azure Functions 창](images/new-function.png)

    1. **Http trigger**라는 타일을 클릭합니다.
    1. 이름 **FBListener**를 입력합니다. **권한 수준** 필드를 **함수**로 설정해야 합니다.
    1. **만들기**를 클릭합니다.
    1. **run.csx**의 콘텐츠를 **FbListener/run.csx**의 콘텐츠로 바꿉니다.

    [!code-csharp[FBListener: csx file](~/samples-fbPageModeration/FbListener/run.csx?range=1-154)]

1. **CMListener**라는 새 **Http trigger** 함수를 만듭니다. 이 함수는 Content Moderator에서 이벤트를 수신합니다. **run.csx**의 콘텐츠를 **CMListener/run.csx**의 콘텐츠로 바꿉니다.

    [!code-csharp[FBListener: csx file](~/samples-fbPageModeration/CmListener/run.csx?range=1-110)]

---

## <a name="configure-the-facebook-page-and-app"></a>Facebook 페이지 및 앱 구성

1. Facebook 앱을 만듭니다.

    ![facebook 개발자 페이지](images/facebook-developer-app.png)

    1. [Facebook 개발자 사이트](https://developers.facebook.com/)로 이동합니다.
    1. **내 앱**을 클릭합니다.
    1. 새 앱을 추가합니다.
    1. 이름을 지정합니다.
    1. **Webhook -> 설정**을 선택합니다.
    1. 드롭다운 메뉴에서 **페이지**를 선택하고 **이 개체에 가입**을 선택합니다.
    1. 콜백 URL로 **FBListener Url** 및 **함수 앱 설정** 아래에서 구성한 **토큰 확인**을 제공합니다.
    1. 구독한 후 피드 아래로 스크롤하고 **구독**을 선택합니다.
    1. **피드** 행의 **테스트** 단추를 클릭하여 FBListener Azure 함수로 테스트 메시지를 전송한 후 **내 서버로 보내기** 단추를 누릅니다. FBListener에서 요청이 수신되는 것을 볼 수 있습니다.

1. Facebook 페이지를 만듭니다.

    > [!IMPORTANT]
    > 2018년에 Facebook은 Facebook 앱에 대해 보다 엄격한 심사를 구현했습니다. Facebook 검토 팀에서 앱을 검토하고 승인하지 않으면 섹션 2, 3 및 4를 실행할 수 없습니다.

    1. [Facebook](https://www.facebook.com/bookmarks/pages)으로 이동하고 **새 Facebook 페이지**를 만듭니다.
    1. Facebook 앱에서 이러한 단계를 따라 이 페이지에 액세스하도록 허용합니다.
        1. [Graph API 탐색기](https://developers.facebook.com/tools/explorer/)로 이동합니다.
        1. **애플리케이션**을 선택합니다.
        1. **페이지 액세스 토큰**을 선택하고, **Get** 요청을 보냅니다.
        1. 응답에서 **페이지 ID**를 클릭합니다.
        1. 이제 URL에 **/subscribed_apps**를 추가하고 **Get**(빈 응답) 요청을 보냅니다.
        1. **Post** 요청을 제출합니다. **success: true**로 응답을 받습니다.

3. 만료되지 않는 Graph API 액세스 토큰을 만듭니다.

    1. [Graph API 탐색기](https://developers.facebook.com/tools/explorer/)로 이동합니다.
    2. **애플리케이션** 옵션을 선택합니다.
    3. **사용자 액세스 토큰 가져오기** 옵션을 선택합니다.
    4. **권한 선택** 아래에서 **manage_pages** 및 **publish_pages** 옵션을 선택합니다.
    5. 다음 단계에서 **액세스 토큰**(수명이 짧은 토큰)을 사용합니다.

4. 다음 몇 단계에 Postman을 사용합니다.

    1. **Postman**을 엽니다(또는 [여기](https://www.getpostman.com/)에서 가져옴).
    2. 다음 두 파일을 가져옵니다.
        1. [Postman 컬렉션](https://github.com/MicrosoftContentModerator/samples-fbPageModeration/blob/master/Facebook%20Permanant%20Page%20Access%20Token.postman_collection.json)
        2. [Postman 환경](https://github.com/MicrosoftContentModerator/samples-fbPageModeration/blob/master/FB%20Page%20Access%20Token%20Environment.postman_environment.json)       
    3. 이러한 환경 변수를 업데이트합니다.
    
        | 키 | 값   | 
        | -------------------- |-------------|
        | appId   | 여기에 Facebook 앱 식별자를 삽입합니다.  | 
        | appSecret | 여기에 Facebook 앱의 비밀을 삽입합니다. | 
        | short_lived_token | 이전 단계에서 생성한 수명이 짧은 사용자 액세스 토큰을 삽입합니다. |
    4. 이제 컬렉션에 나열된 3개의 API를 실행합니다. 
        1. **수명이 긴 액세스 토큰 생성**을 선택하고 **보내기**를 클릭합니다.
        2. **사용자 ID 가져오기**를 선택하고 **보내기**를 클릭합니다.
        3. **영구 페이지 액세스 토큰 가져오기**를 선택하고 **보내기**를 클릭합니다.
    5. 응답에서 **access_token** 값을 복사하고 앱 설정, **fb:PageAccessToken**에 할당합니다.

솔루션은 Facebook 페이지에 게시된 모든 이미지 및 텍스트를 Content Moderator에 보냅니다. 그러면 이전에 구성한 워크플로가 호출됩니다. 워크플로에서 정의한 조건을 통과하지 않는 콘텐츠는 검토 도구 내의 검토로 전달됩니다. 나머지 콘텐츠는 자동으로 게시됩니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 제품 유형별로 태그를 지정하고 검토 팀이 콘텐츠 조정에 대해 합리적인 의사 결정을 할 수 있도록 제품 이미지를 분석하는 프로그램을 설정합니다. 다음으로, 이미지 조정에 대해 자세히 알아보세요.

> [!div class="nextstepaction"]
> [이미지 조정](./image-moderation-api.md)
