---
title: Azure Portal을 사용하는 Durable Functions 만들기
description: Azure Functions, 포털 개발을 위한 Durable Functions 확장을 설치하는 방법에 대해 알아봅니다.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
origin.date: 10/23/2018
ms.date: 03/25/2019
ms.author: v-junlch
ms.openlocfilehash: 1c60bd4dae6c279ccff637ff0aa798c48ebec6f1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60710951"
---
# <a name="create-durable-functions-using-the-azure-portal"></a>Azure Portal을 사용하는 Durable Functions 만들기

Azure Functions에 대한 [지속성 함수](durable-functions-overview.md) 확장이 NuGet 패키지 [Microsoft.Azure.WebJobs.Extensions.DurableTask](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask)에서 제공됩니다. 이 확장을 함수 앱에 설치해야 합니다. 이 문서에서는 Azure Portal에서 Durable Functions를 개발할 수 있도록 이 패키지를 설치하는 방법을 보여줍니다.

> [!NOTE]
> 
> * C#에서 Durable Functions를 개발하려는 경우에는 대신 [Visual Studio 2017 개발](durable-functions-create-first-csharp.md)을 고려해야 합니다.
> * JavaScript에서 Durable Functions를 개발하려는 경우에는 대신 [Visual Studio Code 개발](./quickstart-js-vscode.md)을 고려해야 합니다.

## <a name="create-a-function-app"></a>함수 앱 만들기

함수 실행을 호스트하는 함수 앱이 있어야 합니다. 함수 앱을 통해 함수를 논리 단위로 그룹화하여 더욱 쉽게 관리, 배포 및 리소스 공유할 수 있습니다. .NET 또는 JavaScript 앱을 만들 수 있습니다.

[!INCLUDE [Create function app Azure portal](../../../includes/functions-create-function-app-portal.md)]

기본적으로 만들어진 함수 앱은 Azure Functions 런타임 버전 2.x를 사용합니다. Durable Functions 확장은 C#의 경우 Azure Functions 런타임의 버전 1.x 및 2.x 모두, JavaScript의 경우 버전 2.x에서 작동합니다. 그러나 템플릿은 선택한 언어에 관계없이 런타임의 버전 2.x를 대상으로 하는 경우에만 사용 가능합니다.

## <a name="install-the-durable-functions-npm-package-javascript-only"></a>durable-functions npm 패키지 설치(JavaScript만 해당)

JavaScript Durable Functions를 만드는 경우 [`durable-functions` npm 패키지](https://www.npmjs.com/package/durable-functions)를 설치해야 합니다.

1. 함수 앱의 이름과 **플랫폼 기능**을 차례로 선택한 후 **고급 도구(Kudu)** 를 선택합니다.

   ![Functions 플랫폼 기능에서 Kudu를 선택함](./media/durable-functions-create-portal/function-app-platform-features-choose-kudu.png)

2. Kudu 콘솔 내부에서 **디버그 콘솔**을 선택한 후 **CMD**를 선택합니다.

   ![Kudu 디버그 콘솔](./media/durable-functions-create-portal/kudu-choose-debug-console.png)

3. 함수 앱의 파일 디렉터리 구조가 표시되어야 합니다. `site/wwwroot` 폴더로 이동합니다. 여기에서 `package.json` 파일을 파일 디렉터리 창에 끌어서 놓아 업로드할 수 있습니다. 샘플 `package.json`은 다음과 같습니다.

    ```json
    {
      "dependencies": {
        "durable-functions": "^1.1.2"
      }
    }
    ```

   ![Kudu 업로드 package.json](./media/durable-functions-create-portal/kudu-choose-debug-console.png)

4. `package.json`이 업로드되면 Kudu 원격 실행 콘솔에서 `npm install` 명령을 실행합니다.

   ![Kudu 실행 npm install](./media/durable-functions-create-portal/kudu-npm-install.png)

## <a name="create-an-orchestrator-function"></a>오케스트레이터 함수 만들기

1. 함수 앱을 확장한 후 **함수** 옆의 **+** 단추를 클릭합니다. 함수 앱의 첫 번째 함수인 경우 **포털 내**를 선택한 다음, **계속**을 선택합니다. 그렇지 않으면 3단계로 이동합니다.

   ![Azure Portal에서 함수 빨리 시작하기 페이지](./media/durable-functions-create-portal/function-app-quickstart-choose-portal.png)

1. **추가 템플릿**, **템플릿 마침 및 보기**를 차례로 선택합니다.

    ![Functions 빠른 시작 - 추가 템플릿 선택](./media/durable-functions-create-portal/add-first-function.png)

1. 검색 필드에서 `durable`를 입력한 다음, **Durable Functions HTTP 시작** 템플릿을 선택합니다.

1. 메시지가 표시되면 **설치**를 선택하여 함수 앱에 Azure DurableTask 확장과 모든 종속성을 설치합니다. give 함수 앱에 대해 이 확장을 한 번만 설치해야 합니다. 설치가 완료되면 **계속**을 선택합니다.

    ![바인딩 확장 설치](./media/durable-functions-create-portal/install-durabletask-extension.png)

1. 설치가 완료되면 새 함수 `HttpStart`에 이름을 지정하고 **만들기**를 선택합니다. 만들어진 함수를 사용하오여 오케스트레이션을 시작합니다.

1. 이번에는 **Durable Functions 오케스트레이터** 템플릿을 사용하여 이 함수 앱에서 다른 함수를 만듭니다. 새 오케스트레이션 함수 `HelloSequence`에 이름을 지정합니다.

1. **Durable Functions 활동** 템플릿을 사용하여 `Hello`라는 이름의 세 번째 함수를 만듭니다.

## <a name="test-the-durable-function-orchestration"></a>Durable Functions 오케스트레이션 테스트

1. **HttpStart** 함수로 다시 가서 **</> 함수 URL 가져오기**를 선택한 후 URL을 **복사**합니다. 이 URL을 사용하여 **HelloSequence** 함수를 시작합니다.

1. Postman 또는 cURL과 같은 HTTP 도구를 사용하여 POST 요청을 사용자가 복사한 URL로 보냅니다. 다음 예제는 Durable Functions로 POST 요청을 보내는 cURL 명령입니다.

    ```bash
    curl -X POST https://{your-function-app-name}.chinacloudsites.cn/api/orchestrators/HelloSequence
    ```

    이 예제에서는 `{your-function-app-name}`은 도메인으로, 함수 앱의 이름입니다. 응답 메시지에는 실행을 모니터링하고 관리하기 위해 사용할 수 있는 URI 엔드포인트 세트가 포함되어 있습니다. 다음 예와 같습니다.

    ```json
    {  
       "id":"10585834a930427195479de25e0b952d",
       "statusQueryGetUri":"https://...",
       "sendEventPostUri":"https://...",
       "terminatePostUri":"https://...",
       "rewindPostUri":"https://..."
    }
    ```

1. `statusQueryGetUri` 엔드포인트 URI를 호출하면 Durable Functions의 현재 상태가 보이며 다음 예와 같습니다.

    ```json
        {
            "runtimeStatus": "Running",
            "input": null,
            "output": null,
            "createdTime": "2017-12-01T05:37:33Z",
            "lastUpdatedTime": "2017-12-01T05:37:36Z"
        }
    ```

1. 상태가 **완료됨**으로 변경될 때까지 `statusQueryGetUri` 엔드포인트를 계속 호출하면 다음 예제와 같은 응답이 표시됩니다.

    ```json
    {
            "runtimeStatus": "Completed",
            "input": null,
            "output": [
                "Hello Tokyo!",
                "Hello Seattle!",
                "Hello London!"
            ],
            "createdTime": "2017-12-01T05:38:22Z",
            "lastUpdatedTime": "2017-12-01T05:38:28Z"
        }
    ```

첫 번째 Durable Functions가 Azure에서 작동되고 실행됩니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [일반적인 지속성 함수 패턴에 대해 알아보기](durable-functions-concepts.md)

<!-- Update_Description: wording update -->
