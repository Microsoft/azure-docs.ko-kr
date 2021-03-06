---
title: 'JavaScript 자습서: 검색 사용 웹 사이트 배포'
titleSuffix: Azure Cognitive Search
description: 검색 사용 웹 사이트를 Azure Static Web App에 배포합니다.
manager: nitinme
author: diberry
ms.author: diberry
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 03/18/2021
ms.custom: devx-track-js
ms.devlang: javascript
ms.openlocfilehash: a49ede283899cec42898672f5a376221265dea10
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "104723519"
---
# <a name="3---deploy-the-search-enabled-website"></a>3 - 검색 사용 웹 사이트 배포

검색 사용 웹 사이트를 Azure Static Web App으로 배포합니다. 이 배포에는 React 앱과 Function 앱이 모두 포함됩니다.  

Static Web App은 샘플 리포지토리의 포크를 사용하여 GitHub에서 배포할 정보와 파일을 가져옵니다.  

## <a name="create-a-static-web-app-in-visual-studio-code"></a>Visual Studio Code에서 Static Web App 만들기

1. 작업 막대에서 **Azure** 를 선택한 다음, 사이드바에서 **Static Web Apps** 를 선택합니다. 
1. 마우스 오른쪽 단추로 구독 이름을 클릭한 다음, **Static Web App 만들기(고급)** 를 선택합니다.    

    :::image type="content" source="media/tutorial-javascript-create-load-index/visual-studio-code-create-static-web-app-resource-advanced.png" alt-text="마우스 오른쪽 단추로 구독 이름을 클릭한 다음, **Static Web App 만들기(고급)**를 선택합니다.":::

1. 프롬프트에 따라 다음 정보를 제공합니다.

    |prompt|Enter 키|
    |--|--|
    |Static Web App을 만드는 방법 선택|기존 GitHub 리포지토리를 사용합니다.|
    |조직 선택|조직으로 _고유한_ GitHub 별칭을 선택합니다.|
    |리포지토리 선택|목록에서 **azure-search-javascript-samples** 를 선택합니다. |
    |리포지토리의 분기 선택|목록에서 **마스터** 를 선택합니다. |
    |새 Static Web App에 대한 이름 입력|리소스에 대한 고유한 이름을 만듭니다. 예를 들어 `joansmith-azure-search-javascript-samples`와 같이 리포지토리 이름 앞에 이름을 추가할 수 있습니다. |
    |새 리소스에 대한 리소스 그룹 선택|이 자습서를 위해 만든 리소스 그룹을 사용합니다.|
    |기본 프로젝트 구조를 구성하기 위한 사전 설정 빌드 선택|**사용자 지정** 을 선택합니다.|
    |애플리케이션 코드의 위치 선택|`search-website`|
    |Azure 함수 코드의 위치 선택|`search-website/api`|
    |빌드 출력 경로 입력|빌드|
    |새 리소스의 위치 선택|가까운 지역을 선택합니다.|

1. 리소스가 만들어지면 알림에서 **GitHub에서 작업 열기** 를 선택합니다. 그러면 포크된 리포지토리를 가리키는 브라우저 창이 열립니다. 

    작업 목록에서 웹앱(클라이언트와 함수 모두)이 Azure Static Web App으로 성공적으로 푸시되었음을 나타냅니다. 

    빌드 및 배포가 완료될 때까지 기다린 후에 계속 진행합니다. 완료하는 데 1~2분이 걸릴 수 있습니다.

## <a name="get-cognitive-search-query-key-in-visual-studio-code"></a>Visual Studio Code에서 Cognitive Search 쿼리 키 가져오기

1. Visual Studio Code에서 [작업 막대](https://code.visualstudio.com/docs/getstarted/userinterface)를 열고, Azure 아이콘을 선택합니다. 

1. 사이드바의 **Azure: Cognitive Search** 영역 아래에서 Azure 구독을 선택한 다음, 마우스 오른쪽 단추로 검색 리소스를 클릭하고, **쿼리 키 복사** 를 선택합니다. 

    :::image type="content" source="./media/tutorial-javascript-create-load-index/visual-studio-code-copy-query-key.png" alt-text="사이드바의 **Azure: Cognitive Search** 영역 아래에서 Azure 구독을 선택한 다음, 마우스 오른쪽 단추로 검색 리소스를 클릭하고, **쿼리 키 복사**를 선택합니다.":::

1. 이 쿼리 키를 유지합니다. 다음 섹션에서 사용해야 합니다. 쿼리 키는 인덱스를 쿼리할 수 있습니다. 

## <a name="add-configuration-settings-in-visual-studio-code"></a>Visual Studio Code에서 구성 설정 추가

검색 비밀이 설정에 있을 때까지 Azure 함수 앱에서 검색 데이터를 반환하지 않습니다. 

1. 작업 막대에서 **Azure** 를 선택한 다음, 사이드바에서 **Static Web Apps** 를 선택합니다. 
1. **애플리케이션 설정** 이 표시될 때까지 새 Static Web App을 펼칩니다.
1. 마우스 오른쪽 단추로 **애플리케이션 설정** 을 클릭한 다음, **새 설정 추가** 를 선택합니다.

    :::image type="content" source="media/tutorial-javascript-create-load-index/visual-studio-code-static-web-app-configure-settings.png" alt-text="마우스 오른쪽 단추로 **애플리케이션 설정**을 클릭한 다음, **새 설정 추가**를 선택합니다.":::

1. 다음 설정을 추가합니다.

    |설정|검색 리소스 값|
    |--|--|
    |SearchApiKey|검색 쿼리 키|
    |SearchServiceName|검색 리소스 이름|
    |SearchIndexName|`good-books`|
    |SearchFacets|`authors*,language_code`|

## <a name="use-search-in-your-static-web-app"></a>Static Web App에서 검색 사용

1. Visual Studio Code에서 [작업 막대](https://code.visualstudio.com/docs/getstarted/userinterface)를 열고, Azure 아이콘을 선택합니다.
1. 사이드바의 `Static web apps` 영역 아래에서 **마우스 오른쪽 단추로 Azure 구독을 클릭** 하고, 이 자습서를 위해 만든 Static Web App을 찾습니다.
1. 마우스 오른쪽 단추로 Static Web App 이름을 클릭하고, **사이트 찾아보기** 를 선택합니다.
    
    :::image type="content" source="media/tutorial-javascript-create-load-index/visual-studio-code-browse-static-web-app.png" alt-text="마우스 오른쪽 단추로 Static Web App 이름을 클릭하고, **사이트 찾아보기**를 선택합니다.":::    

1. 팝업 대화 상자에서 **열기** 를 선택합니다.
1. 웹 사이트 검색 창에서 `code`와 같은 검색 쿼리를 _천천히_ 입력합니다. 그러면 제안 기능에서 책 제목을 제안합니다. 제안을 선택하거나 사용자 고유의 쿼리를 계속 입력합니다. 검색 쿼리가 완료되면 Enter 키를 누릅니다. 
1. 결과를 검토한 다음, 책 중 하나를 선택하여 자세한 내용을 확인합니다. 

## <a name="clean-up-resources"></a>리소스 정리

이 자습서에서 만든 리소스를 정리하려면 리소스 그룹을 삭제합니다.

1. Visual Studio Code에서 [작업 막대](https://code.visualstudio.com/docs/getstarted/userinterface)를 열고, Azure 아이콘을 선택합니다. 

1. 사이드바의 `Resource Groups` 영역 아래에서 **마우스 오른쪽 단추로 Azure 구독을 클릭** 하고, 이 자습서를 위해 만든 리소스 그룹을 찾습니다.
1. 마우스 오른쪽 단추로 리소스 그룹 이름을 클릭한 다음, **삭제** 를 선택합니다.
    그러면 검색 및 Static Web App 리소스가 모두 삭제됩니다.
1. 샘플의 GitHub 포크가 더 이상 필요하지 않은 경우 GitHub에서 해당 포크를 삭제해야 합니다. 포크의 **설정** 으로 이동한 다음, 포크를 삭제합니다. 


## <a name="next-steps"></a>다음 단계

* [검색 사용 웹 사이트에 대한 검색 통합 이해](tutorial-javascript-search-query-integration.md)
