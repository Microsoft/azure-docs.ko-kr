---
title: Azure Functions를 사용하여 Azure Static Web Apps에 API 추가
description: Azure Functions를 사용하여 정적 웹앱에 서버리스 API를 추가하여 Azure Static Web Apps를 시작합니다.
services: static-web-apps
author: manekinekko
ms.service: static-web-apps
ms.topic: how-to
ms.date: 05/29/2020
ms.author: wachegha
ms.custom: devx-track-javascript
ms.openlocfilehash: 4dd253aa5dc1b8b89b1b387a555dde416c601f9c
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88691499"
---
# <a name="add-an-api-to-azure-static-web-apps-preview-with-azure-functions"></a>Azure Functions를 사용하여 Azure Static Web Apps 미리 보기에 API 추가

Azure Functions와의 통합을 통해 Azure Static Web Apps에 서버리스 API를 추가할 수 있습니다. 이 문서에서는 Azure Static Web Apps 사이트에 API를 추가하고 배포하는 방법을 보여 줍니다.

## <a name="prerequisites"></a>사전 요구 사항

- 활성 구독이 있는 Azure 계정
  - 계정이 없는 경우 [무료로 만들](https://azure.microsoft.com/free)수 있습니다.
- [Visual Studio Code](https://code.visualstudio.com/)
- Visual Studio Code용 [Azure Functions 확장](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)
- [Live Server Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ritwickdey.LiveServer) 확장
- API 앱을 로컬로 실행 하는 [Node.js](https://nodejs.org/download/)

## <a name="create-a-git-repository"></a>Git 리포지토리 만들기

다음 단계에서는 새 리포지토리를 만들고 파일을 컴퓨터에 복제하는 방법을 보여 줍니다.

1. GitHub에 로그인 되어 있는지 확인 하 고로 이동 https://github.com/staticwebdev/vanilla-basic/generate 하 여 새 리포지토리를 만듭니다.
1. _리포지토리 이름_ 상자에 **my-vanilla-api**를 입력합니다.
1. **템플릿에서 리포지토리 만들기**를 클릭합니다.

   :::image type="content" source="media/add-api/create-repository.png" alt-text="vanilla에서 새 리포지토리 만들기-기본":::

프로젝트를 만든 후에는 새 리포지토리의 브라우저에서 URL을 복사 합니다. Visual Studio Code에서이 URL을 사용 하 여 Git 리포지토리를 복제 합니다.

1. **F1** 키를 눌러 명령 팔레트에서 명령을 엽니다.
1. URL을 _Git: 복제_ 프롬프트에 붙여넣고, **Enter** 키를 누릅니다.

   :::image type="content" source="media/add-api/vscode-git-0.png" alt-text="Visual Studio Code를 사용하여 GitHub 프로젝트 복제":::

    프롬프트에 따라 프로젝트를 복제할 리포지토리 위치를 선택 합니다.

## <a name="create-the-api"></a>API 만들기

다음으로 응용 프로그램의 API로 Azure Functions 프로젝트를 만듭니다. 

1. _my-vanilla-api_ 프로젝트 내에서 **api**라는 하위 폴더를 만듭니다.
1. **F1** 키를 눌러 명령 팔레트를 엽니다.
1. **Azure Functions: 새 프로젝트 만들기...** 를 입력합니다.
1. **Enter** 키를 누릅니다.
1. **찾아보기**를 선택합니다.
1. 프로젝트 작업 영역에 대한 디렉터리로 **api** 폴더를 선택합니다.
1. **선택**을 선택합니다.

   :::image type="content" source="media/add-api/create-azure-functions-vscode-1.png" alt-text="Visual Studio Code를 사용하여 새 Azure Functions 만들기":::

1. 프롬프트에서 다음 정보를 제공합니다.

    - _언어 선택_: **JavaScript** 선택
    - _프로젝트의 첫 번째 함수에 대한 템플릿 선택_: **HTTP 트리거**를 선택합니다.
    - _함수 이름 제공_: **getmessage** 입력
    - _권한 부여 수준_: 누구나 함수 엔드포인트를 호출할 수 있도록 하는 **익명**을 선택합니다.
        - 권한 부여 수준에 대해 알아보려면 [권한 부여 키](../azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys)를 참조하세요.

Visual Studio Code는 HTTP 트리거 함수를 사용 하 여 Azure Functions 프로젝트를 생성 합니다.

이제 앱이 다음 예제와 유사한 프로젝트 구조를 포함 합니다.

```files
├── api
│   ├── GetMessage
│   │   ├── function.json
│   │   ├── index.js
│   │   └── sample.dat
│   ├── host.json
│   ├── local.settings.json
│   ├── package.json
│   └── proxies.json
├── index.html
├── readme.md
└── styles.css
```

다음으로, `GetMessage` 메시지를 프런트 엔드로 반환 하도록 함수를 변경 합니다.

1. `GetMessage`다음 코드를 사용 하 여 _Api/getmessage/index.js_ 아래의 함수를 업데이트 합니다.

    ```javascript
    module.exports = async function (context, req) {
      context.res = {
        body: {
          text: "Hello from the API"
        }
      };
    };
    ```

1. 다음 설정을 사용하여 `api/GetMessage/function.json`의 `GetMessage` 구성을 업데이트합니다.

    ```json
    {
      "bindings": [
        {
          "authLevel": "anonymous",
          "type": "httpTrigger",
          "direction": "in",
          "name": "req",
          "methods": [
            "get"
          ],
          "route": "message"
        },
        {
          "type": "http",
          "direction": "out",
          "name": "res"
        }
      ]
    }
    ```

위의 설정을 사용하는 경우 API 엔드포인트는 다음과 같습니다.

- 함수에 대 한 HTTP 요청이 발생할 때 트리거됩니다.
- 인증 상태에 관계 없이 모든 요청에 사용 가능
- _/api/message_ 경로를 통해 노출됨

## <a name="run-the-api-locally"></a>로컬로 API 실행

Visual Studio Code는 [Azure Functions Core Tools](../azure-functions/functions-run-local.md)와 통합되어 Azure에 게시하기 전에 로컬 개발 컴퓨터에서 이 프로젝트를 실행할 수 있습니다.

> [!TIP]
> 계속 하기 전에 [필수 구성 요소](#prerequisites) 섹션에 모든 리소스가 나열 되어 있는지 확인 합니다.

1. 함수 앱을 시작 하려면 **F5** 키를 눌러 함수를 실행 합니다.

1. Azure Functions Core Tools를 아직 설치하지 않은 경우 프롬프트에서 **설치**를 선택합니다.

    핵심 도구는 _터미널_ 패널에서 실행 중인 응용 프로그램의 출력을 표시 합니다. 출력의 일부로 로컬에서 실행 중인 HTTP 트리거 함수의 URL 엔드포인트를 볼 수 있습니다.

    :::image type="content" source="media/add-api/create-azure-functions-vscode-2.png" alt-text="Visual Studio Code를 사용하여 새 Azure Functions 만들기":::

1. 핵심 도구를 실행 하는 경우 다음 URL로 이동 하 여 API가 올바르게 실행 되 고 있는지 확인 `http://localhost:7071/api/message` 합니다.

   브라우저의 응답은 다음 예제와 같이 표시 됩니다.

   :::image type="content" source="media/add-api/create-azure-functions-vscode-3.png" alt-text="Visual Studio Code를 사용하여 새 Azure Functions 만들기":::

1. **Shift + F5** 키를 눌러 디버깅 세션을 중지 합니다.

### <a name="call-the-api-from-the-application"></a>애플리케이션에서 API 호출

Azure에 배포 되는 경우 API에 대 한 요청은 경로에 전송 된 요청에 대 한 함수 앱으로 자동 라우팅됩니다 `api` . 로컬에서 작업 하는 경우 로컬 API에 대 한 프록시 요청에 대 한 응용 프로그램 설정을 구성 해야 합니다.

[!INCLUDE [static-web-apps-local-proxy](../../includes/static-web-apps-local-proxy.md)]

#### <a name="update-html-files-to-access-the-api"></a>API에 액세스 하도록 HTML 파일 업데이트

1. 다음으로 _index.html_ 파일의 콘텐츠를 다음 코드로 업데이트하여 API 함수에서 텍스트를 인출하고 화면에 표시합니다.

   ```html
   <!DOCTYPE html>
   <html lang="en">

   <head>
     <meta charset="UTF-8">
     <meta name="viewport" content="width=device-width, initial-scale=1.0">
     <link rel="stylesheet" href="styles.css">
     <title>Vanilla JavaScript App</title>
   </head>

   <body>
     <main>
       <h1>Vanilla JavaScript App</h1>
       <p>Loading content from the API: <b id="name">...</b></p>
     </main>

     <script>
       (async function() {
         let { text } = await( await fetch(`/api/message`)).json();
         document.querySelector('#name').textContent = text;
       }())
     </script>
   </body>

   </html>
   ```

1. **F5** 키를 눌러 API 프로젝트를 시작 합니다.

1. **F1** 키를 누르고 **Live Server: Live Server로 열기**를 선택합니다.

    이제 웹 페이지에 API 메시지가 표시 됩니다.

   :::image type="content" source="media/add-api/create-azure-functions-vscode-4.png" alt-text="Visual Studio Code를 사용하여 새 Azure Functions 만들기":::

   > [!NOTE]
   > 다른 HTTP 서버 또는 프록시를 사용하여 `index.html` 파일을 제공할 수 있습니다. `file:///`에서 `index.html`에 대한 액세스가 작동하지 않습니다.

1. **Shift + f5** 키를 눌러 API 프로젝트를 중지 합니다.

### <a name="commit-and-push-your-changes-to-github"></a>GitHub에 변경 내용을 커밋 및 푸시

Visual Studio Code를 사용하여 변경 내용을 원격 git 리포지토리에 커밋하고 푸시합니다.

1. **F1** 키를 눌러 명령 팔레트를 엽니다.
1. **Git: 모두 커밋**을 입력합니다.
1. 커밋 메시지를 추가 하 고 **enter** 키를 누릅니다.
1. **F1** 키를 누릅니다.
1. **Git: push** 를 입력 하 고 **enter** 키를 누릅니다.

## <a name="create-a-static-web-app"></a>정적 웹앱 만들기

1. [Azure Portal](https://portal.azure.com)로 이동합니다.
1. **리소스 만들기**를 클릭합니다.
1. **정적 웹 앱** 검색
1. **정적 웹 앱 (미리 보기)을** 클릭 합니다.
1. **만들기**를 클릭합니다.

다음으로, 앱 별 설정을 추가 합니다.

1. _Azure 구독_ 선택
1. 새 _리소스 그룹_을 선택하거나 만듭니다.
1. 앱 이름을 **my-vanilla-api**로 지정합니다.
1. 가장 가까운 _지역_을 선택합니다.
1. **무료** _SKU_를 선택합니다.
1. **GitHub로 로그인** 단추를 클릭하고 GitHub로 인증합니다.
1. 원하는 _조직_을 선택합니다.
1. _리포지토리_ 드롭다운에서 **my-vanilla-api**를 선택합니다.
1. _분기_ 드롭다운에서 **마스터**를 선택합니다.
1. **다음: 빌드 >** 단추를 클릭하여 빌드 구성을 편집합니다.

다음으로 빌드 세부 정보를 추가합니다.

1. **/** _앱 위치_에 대해를 입력 합니다.
1. _API 위치_ 상자에 **api**를 입력합니다.
1. _앱 아티팩트 위치_에서 기본값을 지워 상자를 비워 둡니다.
1. **검토 + 만들기**를 클릭합니다.
1. **만들기** 단추를 클릭합니다.

    _만들기_ 단추를 클릭 하면 Azure는 두 가지 작업을 수행 합니다. 먼저 앱을 지원 하기 위해 기본 클라우드 서비스를 만듭니다. 그런 다음 백그라운드 프로세스가 응용 프로그램을 빌드하고 배포 하기 시작 합니다.

1. **리소스로 이동** 단추를 클릭 하 여 웹 앱의 _개요_ 페이지로 이동 합니다.

    앱이 백그라운드에서 빌드되는 경우 링크를 포함 하는 배너를 클릭 하 여 빌드 상태를 볼 수 있습니다.

    :::image type="content" source="media/add-api/github-action-flag.png" alt-text="GitHub 워크플로":::

1. 배포가 완료 되 면 ou는 _개요_ 페이지에 표시 된 _URL_ 링크를 클릭 하 여 웹 앱으로 이동할 수 있습니다.

    :::image type="content" source="media/add-api/static-app-url-from-portal.png" alt-text="Azure Portal에서 정적 앱 URL에 액세스":::

## <a name="clean-up-resources"></a>리소스 정리

이 애플리케이션을 나중에 사용하기 위해 유지하지 않으려는 경우 다음 단계를 사용하여 Azure Static Web App 및 관련 리소스를 삭제할 수 있습니다.

1. [Azure Portal](https://portal.azure.com)로 이동합니다.
1. 위쪽 검색 창에 **리소스 그룹**을 입력합니다.
1. **리소스 그룹**을 클릭합니다.
1. **myResourceGroup**을 선택합니다.
1. _myResourceGroup_ 페이지에서 나열된 리소스가 삭제하려는 리소스인지 확인합니다.
1. **삭제** 선택
1. 텍스트 상자에 **myResourceGroup**을 입력합니다.
1. **삭제**를 선택합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [앱 설정 구성](./application-settings.md)
