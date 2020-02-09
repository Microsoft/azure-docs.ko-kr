---
title: Language Understanding에 대 한 미리 빌드된 모델
titleSuffix: Azure Cognitive Services
description: LUIS에는 일반적인 대화형 사용자 시나리오를 신속 하 게 추가 하기 위한 미리 작성 된 모델 집합이 포함 되어 있습니다.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: diberry
ms.openlocfilehash: 0a733aff14b71c6378971c0b72a9ca1f3cd4cfc3
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74013550"
---
# <a name="add-prebuilt-models-for-common-usage-scenarios"></a>일반적인 사용 시나리오에 대 한 미리 빌드된 모델 추가 

LUIS에는 일반적인 대화형 사용자 시나리오를 신속 하 게 추가 하기 위한 미리 작성 된 모델 집합이 포함 되어 있습니다. 이러한 기능에 대 한 모델을 디자인 하지 않고도 대화형 클라이언트 응용 프로그램에 기능을 추가 하는 빠르고 쉬운 방법입니다. 

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

## <a name="add-a-prebuilt-domain"></a>미리 빌드된 도메인 추가

1. **내 앱** 페이지에서 앱을 선택합니다. 그러면 해당 앱의 **빌드** 섹션이 열립니다. 

1. 왼쪽 도구 모음에서 **미리 빌드된 도메인** 을 선택 합니다. 

1. 앱에 추가 하려는 도메인을 찾은 다음 **도메인 추가** 단추를 선택 합니다.

    > [!div class="mx-imgBorder"]
    > 미리 작성 한 일정 도메인을 추가 ![](./media/luis-prebuilt-domains/add-prebuilt-domain.png)

## <a name="add-a-prebuilt-intent"></a>미리 빌드된 의도 추가

1. **내 앱** 페이지에서 앱을 선택합니다. 그러면 해당 앱의 **빌드** 섹션이 열립니다. 

1. **의도** 페이지에서 의도 목록 위의 도구 모음에서 미리 작성 된 **도메인 의도 추가** 를 선택 합니다. 

1. 팝업 대화 상자에서 **Utilities.Cancel** 의도를 선택합니다. 

    > [!div class="mx-imgBorder"]
    > 미리 빌드된 의도를 추가 ![](./media/luis-prebuilt-domains/add-prebuilt-domain-intents.png)

1. **완료** 단추를 선택합니다.

## <a name="add-a-prebuilt-entity"></a>미리 빌드된 엔터티 추가

1. **내 앱** 페이지에서 해당 이름을 클릭하여 앱을 열고 왼쪽에서 **엔터티**를 클릭합니다. 

1. **엔터티** 페이지에서 **미리 빌드된 엔터티 추가**를 클릭합니다.

1. 미리 작성 된 **엔터티 추가** 대화 상자에서 미리 작성 된 엔터티를 선택 합니다. 

    > [!div class="mx-imgBorder"]
    > ![미리 작성 한 엔터티 추가 대화 상자](./media/luis-prebuilt-domains/add-prebuilt-entity.png)

1. **완료**를 선택합니다. 엔터티를 추가한 후에는 앱을 학습할 필요가 없습니다. 

## <a name="publish-to-view-prebuilt-model-from-prediction-endpoint"></a>게시-예측 엔드포인트에서 미리 작성 한 모델 보기

미리 작성 된 모델의 값을 보는 가장 쉬운 방법은 게시 된 엔드포인트에서 쿼리 하는 것입니다. 

## <a name="entities-containing-a-prebuilt-entity-token"></a>미리 작성 된 엔터티 토큰을 포함 하는 엔터티
 
미리 빌드된 엔터티에 의해 제한 되는 컴퓨터에서 배운 엔터티가 있는 경우 컴퓨터에서 학습 한 엔터티에 하위 구성 요소를 추가한 다음 미리 작성 된 엔터티의 제약 조건을 추가 합니다.

## <a name="next-steps"></a>다음 단계
> [!div class="nextstepaction"]
> [REST Api를 사용 하 여 .csv에서 모델 빌드](./luis-tutorial-node-import-utterances-csv.md)
