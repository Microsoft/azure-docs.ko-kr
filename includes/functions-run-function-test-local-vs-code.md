---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 01/12/2020
ms.author: glenga
ms.openlocfilehash: f4af3c202d4f00c4ac3041921175c92226f0db7c
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/02/2020
ms.locfileid: "76964140"
---
## <a name="run-the-function-locally"></a>로컬에서 함수 실행

Visual Studio Code는 [Azure Functions Core Tools](../articles/azure-functions/functions-run-local.md)와 통합되어 Azure에 게시하기 전에 로컬 개발 컴퓨터에서 이 프로젝트를 실행할 수 있습니다.

1. 함수를 호출하려면 F5를 눌러 함수 앱 프로젝트를 시작합니다. 핵심 도구의 출력이 **터미널** 패널에 표시됩니다.

1. Azure Functions Core Tools를 아직 설치하지 않은 경우 프롬프트에서 **설치**를 선택합니다. 핵심 도구가 설치되면 **터미널** 패널에서 앱이 시작됩니다. 로컬에서 실행 중인 HTTP 트리거 함수의 URL 엔드포인트를 볼 수 있습니다. 

    ![Azure 로컬 출력](./media/functions-run-function-test-local-vs-code/functions-vscode-f5.png)

1. Core Tools를 실행 중인 상태에서 다음 URL로 이동하여 `?name=Functions` 쿼리 문자열이 포함된 GET 요청을 실행합니다.

    <http://localhost:7071/api/HttpExample?name=Functions>

1. 브라우저에서 다음과 같이 표시되는 응답이 반환됩니다.

    ![브라우저의 localhost 함수 응답](./media/functions-run-function-test-local-vs-code/functions-test-local-browser.png)

1. 요청에 대한 정보는 **터미널** 패널에 표시됩니다.

    ![터미널 패널에서 함수 실행](./media/functions-run-function-test-local-vs-code/function-execution-terminal.png)

1. Ctrl + C를 눌러 Core Tools를 중지하고 디버거 연결을 끊습니다.
