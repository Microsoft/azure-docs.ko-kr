---
title: 새 앱 만들기-LUIS
titleSuffix: Azure Cognitive Services
description: LUIS(Language Understanding) 웹 페이지에서 애플리케이션을 만들고 관리합니다.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/08/2019
ms.author: diberry
ms.openlocfilehash: d3f8696388a33a8ea112aae438c6bbe9af520c61
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/10/2019
ms.locfileid: "73904254"
---
# <a name="create-a-new-luis-app-in-the-luis-portal"></a>LUIS 포털에서 새 LUIS 앱 만들기
LUIS 앱을 만드는 몇 가지 방법이 있습니다. LUIS 포털에서 또는 LUIS authoring [api](developer-reference-resource.md)를 통해 LUIS 앱을 만들 수 있습니다.

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

## <a name="using-the-luis-portal"></a>LUIS 포털 사용

여러 가지 방법으로 preview 포털에서 새 앱을 만들 수 있습니다.

* 빈 앱으로 시작하고 의도, 발화 및 엔터티를 만듭니다.
* 빈 앱으로 시작하고 [미리 빌드된 도메인](luis-how-to-use-prebuilt-domains.md)을 추가합니다.
* 이미 의도, 길이 발언 및 엔터티를 포함 하는 `.lu` 또는 `.json` 파일에서 LUIS 앱을 가져옵니다.

## <a name="using-the-authoring-apis"></a>작성 API 사용
여러 가지 방법으로 작성 API를 사용하여 새 앱을 만들 수 있습니다.

* [응용 프로그램 추가](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c2f) -빈 앱으로 시작 하 고 의도, 길이 발언 및 엔터티를 만듭니다.
* [미리 빌드된 응용 프로그램 추가](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/59104e515aca2f0b48c76be5) -의도, 길이 발언 및 엔터티를 포함 하 여 미리 작성 된 도메인으로 시작 합니다.  


<a name="export-app"></a>
<a name="import-new-app"></a>
<a name="delete-app"></a>
 

[!INCLUDE [Sign in to LUIS](./includes/sign-in-process.md)]

## <a name="create-new-app-in-luis"></a>LUIS에서 새 앱 만들기

1. **내 앱** 페이지에서 구독을 선택 하 고 리소스를 작성 한 후 **+ 만들기**를 선택 합니다. 무료 평가판 키를 사용 하는 경우 [제작 리소스를 만드는](luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal)방법에 대해 알아보세요.

    ![LUIS 앱 목록](./media/create-app-in-portal.png)


1. 대화 상자에서 `Pizza Tutorial`와 같은 응용 프로그램의 이름을 입력 합니다.

    ![새 앱 만들기 대화 상자](./media/create-pizza-tutorial-app-in-portal.png)

1. 응용 프로그램 문화권을 선택한 다음 **완료**를 선택 합니다. 이 시점에서 설명 및 예측 리소스는 선택 사항입니다. 언제 든 지 포털의 **관리** 섹션에서 설정할 수 있습니다.

    > [!NOTE]
    > 애플리케이션을 만든 후에는 문화권을 변경할 수 없습니다. 

    앱을 만든 후에는 LUIS 포털에 `None` 의도가 이미 생성 된 **의도 목록이 표시** 됩니다. 이제 빈 앱이 있습니다. 
    
    > [!div class="mx-imgBorder"]
    > 예 길이 발언를 사용 하 여 만든 의도가 없는 ![의도 목록입니다.](media/pizza-tutorial-new-app-empty-intent-list.png)

## <a name="other-actions-available"></a>사용할 수 있는 기타 작업

상황에 맞는 도구 모음은 다른 작업을 제공 합니다.

* 앱 이름 바꾸기
* `.lu` 또는 `.json`를 사용 하 여 파일에서 가져오기
* 앱을 `.lu` ( [Ludown](https://github.com/microsoft/botbuilder-tools/tree/master/packages/Ludown)), `.json`또는 `.zip` ( [LUIS 컨테이너](luis-container-howto.md)의 경우)로 내보내기
* 컨테이너 엔드포인트 로그를 가져와 길이 발언 엔드포인트을 검토 합니다.
* 오프 라인 분석을 위해 엔드포인트 로그를 `.csv`으로 내보내기
* 앱 삭제

## <a name="next-steps"></a>다음 단계

앱 디자인에 의도 검색을 포함 하는 경우 [새 의도를 만들고](luis-how-to-add-intents.md)길이 발언 예제를 추가 합니다. 앱 디자인이 데이터 추출 인 경우 예를 길이 발언에 추가 하 고, [엔터티를 만들고](luis-how-to-add-example-utterances.md), 예제 길이 발언에 해당 엔터티로 레이블을 추가 합니다. 
