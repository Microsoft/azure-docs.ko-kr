---
title: '빠른 시작: Azure Static Web Apps를 사용하여 첫 번째 정적 웹앱 빌드'
description: Azure Static Web Apps 웹 사이트를 빌드하는 방법을 알아봅니다.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: quickstart
ms.date: 08/13/2020
ms.author: cshoe
ms.openlocfilehash: db3836e6171d187539b8615efcb5ab782c368020
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/22/2020
ms.locfileid: "88752451"
---
# <a name="quickstart-building-your-first-static-web-app"></a>빠른 시작: 첫 번째 정적 웹앱 빌드

Azure Static Web Apps는 GitHub 리포지토리에서 앱을 빌드하여 프로덕션 환경에 웹 사이트를 게시합니다. 이 빠른 시작에서는 Visual Studio Code 확장을 사용하여 웹 애플리케이션을 Azure Static 웹앱에 배포합니다.

Azure 구독이 아직 없는 경우 [평가판 계정](https://azure.microsoft.com/free)을 만듭니다.

## <a name="prerequisites"></a>사전 요구 사항

- [GitHub](https://github.com) 계정
- [Azure](https://portal.azure.com) 계정
- [Visual Studio Code](https://code.visualstudio.com)
- [Visual Studio Code용 Azure Static Web Apps 확장](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestaticwebapps)

[!INCLUDE [create repository from template](../../includes/static-web-apps-get-started-create-repo.md)]

[!INCLUDE [clone the repository](../../includes/static-web-apps-get-started-clone-repo.md)]

다음으로, Visual Studio Code를 열고 **파일 > 폴더 열기**로 이동하여 방금 머신에 복제한 리포지토리를 편집기에서 엽니다.

## <a name="create-a-static-web-app"></a>정적 웹앱 만들기

1. Visual Studio Code 내의 활동 표시줄에서 Azure 로고를 선택하여 Azure 확장 창을 엽니다.

    :::image type="content" source="media/getting-started/extension-azure-logo.png" alt-text="Azure 로고":::

    > [!NOTE]
    > Azure 및 GitHub 로그인이 필요합니다. Visual Studio Code에서 Azure 및 GitHub에 아직 로그인하지 않은 경우 확장은 만들기 프로세스 중에 둘 다에 로그인하라는 메시지를 표시합니다.

1. _Static Web Apps_ 레이블 위에 마우스를 놓고 **더하기 기호**를 선택합니다.

    :::image type="content" source="media/getting-started/extension-create-button.png" alt-text="애플리케이션 이름":::

1. 명령 팔레트가 편집기 위쪽에서 열리고 애플리케이션의 이름을 지정하라는 메시지가 표시됩니다.

    **my-first-static-web-app**을 입력하고 **Enter**를 누릅니다.

    :::image type="content" source="media/getting-started/extension-create-app.png" alt-text="Static Web App 만들기":::

1. **마스터** 분기를 선택하고 **Enter**를 누릅니다.

    :::image type="content" source="media/getting-started/extension-branch.png" alt-text="분기 이름":::

1. 애플리케이션 코드의 위치로 **/** 를 선택하고 **Enter**를 누릅니다.

    :::image type="content" source="media/getting-started/extension-app-location.png" alt-text="애플리케이션 코드 위치":::

1. 확장은 애플리케이션에서 API의 위치를 찾고 있습니다. 이 문서에서는 API를 구현하지 않습니다.

    **지금은 건너뛰기**를 선택하고 **Enter**를 누릅니다.

    :::image type="content" source="media/getting-started/extension-api-location.png" alt-text="API 위치":::

1. 앱에서 프로덕션용으로 파일이 빌드되는 위치를 선택합니다.

    # <a name="no-framework"></a>[프레임워크 없음](#tab/vanilla-javascript)

    상자를 선택 취소하고 **Enter**를 누릅니다.

    :::image type="content" source="media/getting-started/extension-artifact-no-framework.png" alt-text="앱 파일 경로":::

    # <a name="angular"></a>[Angular](#tab/angular)

    **dist/angular-basic**을 입력하고 **Enter**를 누릅니다.

    :::image type="content" source="media/getting-started/extension-artifact-angular.png" alt-text="Angular 앱 파일 경로":::

    # <a name="react"></a>[React](#tab/react)

    **빌드**를 입력하고 **Enter**를 누릅니다.

    :::image type="content" source="media/getting-started/extension-artifact-react.png" alt-text="React 앱 파일 경로":::

    # <a name="vue"></a>[Vue](#tab/vue)

    **dist**를 입력하고 **Enter**를 누릅니다.

    :::image type="content" source="media/getting-started/extension-artifact-vue.png" alt-text="Vue 앱 파일 경로":::

    ---

1. 가장 가까운 위치를 선택하고 **Enter**를 누릅니다.

    :::image type="content" source="media/getting-started/extension-location.png" alt-text="리소스 위치":::

1. 앱이 만들어지면 Visual Studio Code에 확인 알림이 표시됩니다.

    :::image type="content" source="media/getting-started/extension-confirmation.png" alt-text="생성 확인":::

1. Visual Studio Code 탐색기 창에서 _Static Web Apps_ 섹션으로 돌아가 **프로덕션**을 마우스 오른쪽 단추로 클릭하고 **포털에서 열기**를 선택하여 Azure Portal에서 앱을 확인합니다.

    :::image type="content" source="media/getting-started/extension-open-in-portal.png" alt-text="포털 열기":::

[!INCLUDE [view website](../../includes/static-web-apps-get-started-view-website.md)]

## <a name="clean-up-resources"></a>리소스 정리

이 애플리케이션을 계속 사용하지 않을 경우 확장을 통해 Azure Static Web App 인스턴스를 삭제할 수 있습니다.

Visual Studio Code 탐색기 창에서 _Static Web Apps_ 섹션으로 돌아가서 **my-first-static-web-app**을 마우스 오른쪽 단추로 클릭하고 **삭제**를 선택합니다.

:::image type="content" source="media/getting-started/extension-delete.png" alt-text="앱 삭제":::

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [API 추가](add-api.md)
