---
title: 을 사용 하 여 Azure Functions 개발 Visual Studio Code
description: Visual Studio Code에 대 한 Azure Functions 확장을 사용 하 여 Azure Functions를 개발 하 고 테스트 하는 방법을 알아봅니다.
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 08/21/2019
ms.openlocfilehash: 610c80dc5552eae4f2ad8442fa11b85f2eab35eb
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88206752"
---
# <a name="develop-azure-functions-by-using-visual-studio-code"></a>을 사용 하 여 Azure Functions 개발 Visual Studio Code

[Visual Studio Code에 대 한 Azure Functions 확장] 을 사용 하면 함수를 로컬로 개발 하 고 Azure에 배포할 수 있습니다. Azure Functions를 처음으로 접하는 경우라면 [Azure Functions 소개](functions-overview.md)에서 자세한 내용을 확인할 수 있습니다.

Azure Functions 확장 프로그램은 다음과 같은 이점을 제공 합니다.

* 로컬 개발 컴퓨터에서 함수를 편집, 빌드 및 실행합니다.
* Azure에 직접 Azure Functions 프로젝트를 게시합니다.
* Visual Studio Code의 이점을 활용 하면서 다양 한 언어로 함수를 작성 합니다.

확장은 다음 언어와 함께 사용할 수 있습니다 .이 언어는 버전 2.x부터 Azure Functions 런타임에서 지원 됩니다.

* [C # 컴파일](functions-dotnet-class-library.md)
* [C # 스크립트](functions-reference-csharp.md)<sup>*</sup>
* [JavaScript](functions-reference-node.md)
* [Java](functions-reference-java.md)
* [PowerShell](functions-reference-powershell.md)
* [Python](functions-reference-python.md)

<sup>*</sup>를 사용 하려면 [c # 스크립트를 기본 프로젝트 언어로 설정](#c-script-projects)해야 합니다.

이 문서에서 예제는 현재 JavaScript (Node.js) 및 c # 클래스 라이브러리 함수에 대해서만 사용할 수 있습니다.  

이 문서에서는 Azure Functions 확장을 사용 하 여 함수를 개발 하 고 Azure에 게시 하는 방법에 대 한 세부 정보를 제공 합니다. 이 문서를 읽기 전에 [Visual Studio Code를 사용 하 여 첫 번째 함수를 만들어야](functions-create-first-function-vs-code.md)합니다.

> [!IMPORTANT]
> 단일 함수 앱에 대 한 로컬 개발 및 포털 개발을 혼합 하지 마세요. 로컬 프로젝트에서 함수 앱에 게시할 때 배포 프로세스는 포털에서 개발한 모든 기능을 덮어씁니다.

## <a name="prerequisites"></a>필수 구성 요소

Visual Studio Code 용 [Azure Functions 확장][Azure Functions 확장]을 설치 하 고 실행 하기 전에 다음 요구 사항을 충족 해야 합니다.

* [Visual Studio Code](https://code.visualstudio.com/) [지원 되는 플랫폼](https://code.visualstudio.com/docs/supporting/requirements#_platforms)중 하나에 설치 되어 있어야 합니다.

* 활성화된 Azure 구독.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

Azure storage 계정과 같은 필요한 다른 리소스는 [Visual Studio Code을 사용 하 여 게시할](#publish-to-azure)때 구독에 생성 됩니다.

> [!IMPORTANT]
> 로컬로 함수를 개발 하 여 로컬로 시작 하 고 실행할 필요 없이 Azure에 게시할 수 있습니다. 함수를 로컬로 실행 하려면 Azure Functions Core Tools의 자동 다운로드를 비롯 한 몇 가지 추가 요구 사항을 충족 해야 합니다. 자세히 알아보려면 [프로젝트를 로컬로 실행 하기 위한 추가 요구 사항](#additional-requirements-for-running-a-project-locally)을 참조 하세요.

[!INCLUDE [functions-install-vs-code-extension](../../includes/functions-install-vs-code-extension.md)]

## <a name="create-an-azure-functions-project"></a>Azure Functions 프로젝트 만들기

함수 확장을 사용 하면 첫 번째 함수와 함께 함수 앱 프로젝트를 만들 수 있습니다. 다음 단계에서는 새 함수 프로젝트에서 HTTP 트리거 함수를 만드는 방법을 보여 줍니다. [HTTP 트리거](functions-bindings-http-webhook.md) 는 설명 하는 가장 간단한 함수 트리거 템플릿입니다.

1. **Azure: 함수**에서 **함수 만들기** 아이콘을 선택 합니다.

    ![함수 만들기](./media/functions-develop-vs-code/create-function.png)

1. 함수 앱 프로젝트에 대 한 폴더를 선택 하 고 **함수 프로젝트에 사용할 언어를 선택**합니다.

1. 핵심 도구를 아직 설치 하지 않은 경우 설치할 핵심 도구의 버전을 **선택** 하 라는 메시지가 표시 됩니다. 버전 2.x 또는 이후 버전을 선택 합니다. 

1. **HTTP 트리거** 함수 템플릿을 선택 하거나, **지금 건너뛰기** 를 선택 하 여 함수 없이 프로젝트를 만들 수 있습니다. 나중에 항상 [함수를 프로젝트에 추가할](#add-a-function-to-your-project) 수 있습니다.

    ![HTTP 트리거 템플릿 선택](./media/functions-develop-vs-code/create-function-choose-template.png)

1. 함수 이름에 대해 **Httpexample** 을 입력 하 고 Enter 키를 선택한 다음 **함수** 권한 부여를 선택 합니다. 이 권한 부여 수준에서는 함수 끝점을 호출할 때 [함수 키](functions-bindings-http-webhook-trigger.md#authorization-keys) 를 제공 해야 합니다.

    ![함수 권한 부여 선택](./media/functions-develop-vs-code/create-function-auth.png)

    함수는 HTTP 트리거 함수에 대 한 템플릿 및 선택한 언어로 만들어집니다.

    ![Visual Studio Code에서 HTTP 트리거 함수 템플릿](./media/functions-develop-vs-code/new-function-full.png)

### <a name="generated-project-files"></a>생성 된 프로젝트 파일

프로젝트 템플릿은 선택한 언어로 프로젝트를 만들고 필요한 종속성을 설치 합니다. 모든 언어에 대해 새 프로젝트에는 다음 파일이 있습니다.

* **host.json**: 함수 호스트를 구성할 수 있습니다. 이러한 설정은 로컬에서 함수를 실행 하는 경우와 Azure에서 함수를 실행 하는 경우에 적용 됩니다. 자세한 내용은 [host.json 참조](functions-host-json.md)를 참조하세요.

* **local.settings.json**: 함수를 로컬로 실행 하는 경우 사용 되는 설정을 유지 합니다. 이러한 설정은 함수를 로컬로 실행 하는 경우에만 사용 됩니다. 자세한 내용은 [로컬 설정 파일](#local-settings-file)을 참조 하세요.

    >[!IMPORTANT]
    >파일의 local.settings.js는 암호를 포함할 수 있으므로 프로젝트 소스 제어에서 제외 해야 합니다.

언어에 따라 다음과 같은 다른 파일이 생성 됩니다.

# <a name="c"></a>[C\#](#tab/csharp)

* 함수를 구현 하는 [HttpExample.cs 클래스 라이브러리 파일](functions-dotnet-class-library.md#functions-class-library-project) 입니다.

이 시점에서 [c # 클래스 라이브러리 함수에 매개 변수를 추가](#add-input-and-output-bindings)하 여 입력 및 출력 바인딩을 함수에 추가할 수 있습니다.

# <a name="javascript"></a>[JavaScript](#tab/nodejs)

* 루트 폴더에 있는 파일에 대 한 package.js입니다.

* [정의 파일의function.js](functions-reference-node.md#folder-structure) 및 [index.js 파일](functions-reference-node.md#exporting-a-function)(함수 코드를 포함 하는 Node.js 파일)이 포함 된 httpexample 폴더입니다.

이 시점에서 [파일의 function.js를 수정](#add-input-and-output-bindings)하 여 입력 및 출력 바인딩을 함수에 추가할 수 있습니다.

<!-- # [PowerShell](#tab/powershell)

* An HttpExample folder that contains the [function.json definition file](functions-reference-python.md#programming-model) and the run.ps1 file, which contains the function code.
 
# [Python](#tab/python)
    
* A project-level requirements.txt file that lists packages required by Functions.
    
* An HttpExample folder that contains the [function.json definition file](functions-reference-python.md#programming-model) and the \_\_init\_\_.py file, which contains the function code.
     -->
---

[프로젝트에 새 함수를 추가할](#add-a-function-to-your-project)수도 있습니다.

## <a name="install-binding-extensions"></a>바인딩 확장 설치

HTTP 및 타이머 트리거를 제외 하 고 바인딩은 확장 패키지에서 구현 됩니다. 필요한 트리거 및 바인딩에 대 한 확장 패키지를 설치 해야 합니다. 바인딩 확장을 설치 하는 프로세스는 프로젝트의 언어에 따라 달라 집니다.

# <a name="c"></a>[C\#](#tab/csharp)

터미널 창에서 [dotnet add package](/dotnet/core/tools/dotnet-add-package) 명령을 실행 하 여 프로젝트에 필요한 확장 패키지를 설치 합니다. 다음 명령은 Blob, Queue 및 Table Storage에 대 한 바인딩을 구현 하는 Azure Storage 확장을 설치 합니다.

```bash
dotnet add package Microsoft.Azure.WebJobs.Extensions.Storage --version 3.0.4
```

# <a name="javascript"></a>[JavaScript](#tab/nodejs)

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

---

## <a name="add-a-function-to-your-project"></a>프로젝트에 함수 추가

미리 정의 된 함수 트리거 템플릿 중 하나를 사용 하 여 기존 프로젝트에 새 함수를 추가할 수 있습니다. 새 함수 트리거를 추가 하려면 F1 키를 선택 하 여 명령 팔레트를 연 다음 명령을 검색 하 고 실행 **Azure Functions: Create function**합니다. 프롬프트에 따라 트리거 유형을 선택 하 고 트리거의 필수 특성을 정의 합니다. 서비스에 연결 하기 위해 트리거에 액세스 키 또는 연결 문자열이 필요한 경우 함수 트리거를 만들기 전에 준비 합니다.

이 작업의 결과는 프로젝트의 언어에 따라 달라 집니다.

# <a name="c"></a>[C\#](#tab/csharp)

새 c # 클래스 라이브러리 (.cs) 파일이 프로젝트에 추가 됩니다.

# <a name="javascript"></a>[JavaScript](#tab/nodejs)

프로젝트에 새 폴더가 만들어집니다. 폴더에는 파일에 대 한 새 function.js및 새 JavaScript 코드 파일이 포함 되어 있습니다.

---

## <a name="add-input-and-output-bindings"></a>입력 및 출력 바인딩 추가

입력 및 출력 바인딩을 추가 하 여 함수를 확장할 수 있습니다. 바인딩을 추가 하는 프로세스는 프로젝트의 언어에 따라 달라 집니다. 바인딩에 대해 자세히 알아보려면 [Azure Functions 트리거 및 바인딩 개념](functions-triggers-bindings.md)을 참조하세요.

다음 예에서는 `outqueue` local.settings.js의 응용 프로그램 설정에서 저장소 계정에 대 한 연결 문자열이 설정 된 라는 저장소 큐에 연결 합니다 `MyStorageConnection` .

# <a name="c"></a>[C\#](#tab/csharp)

함수 메서드를 업데이트 하 여 메서드 정의에 다음 매개 변수를 추가 합니다 `Run` .

```cs
[Queue("outqueue"),StorageAccount("MyStorageConnection")] ICollector<string> msg
```

이 코드를 사용 하려면 다음 문을 추가 해야 합니다 `using` .

```cs
using Microsoft.Azure.WebJobs.Extensions.Storage;
```

`msg` 매개 변수는 `ICollector<T>` 형식이며, 함수가 완료될 때 출력 바인딩에 작성되는 메시지의 컬렉션을 나타냅니다. 하나 이상의 메시지를 컬렉션에 추가 합니다. 이러한 메시지는 함수가 완료 되 면 큐에 전송 됩니다.

자세히 알아보려면 [Queue storage output binding](functions-bindings-storage-queue-output.md) 설명서를 참조 하세요.

# <a name="javascript"></a>[JavaScript](#tab/nodejs)

Visual Studio Code를 사용 하면 편리한 프롬프트를 따라 파일의 function.js에 바인딩을 추가할 수 있습니다. 바인딩을 만들려면 함수 폴더의 파일 ** 에 대 한function.js** 를 마우스 오른쪽 단추로 클릭 (Macos에서 Ctrl + 클릭) 하 고 **바인딩 추가**를 선택 합니다.

![기존 JavaScript 함수에 바인딩 추가 ](media/functions-develop-vs-code/function-add-binding.png)

다음은 새 저장소 출력 바인딩을 정의 하는 예제 프롬프트입니다.

| prompt | 값 | 설명 |
| -------- | ----- | ----------- |
| **바인딩 방향 선택** | `out` | 바인딩은 출력 바인딩입니다. |
| **방향이 있는 바인딩 선택** | `Azure Queue Storage` | 바인딩은 Azure Storage 큐 바인딩입니다. |
| **코드에서이 바인딩을 식별 하는 데 사용 되는 이름입니다.** | `msg` | 코드에서 참조되는 바인딩 매개 변수를 식별하는 이름입니다. |
| **메시지가 전송될 큐** | `outqueue` | 바인딩이 데이터를 쓰는 큐의 이름입니다. 바인딩을 처음 사용할 때 *queueName*이 없으면 바인딩이 알아서 만듭니다. |
| **"local.settings.js에서 설정"을 선택 합니다.** | `MyStorageConnection` | 저장소 계정에 대 한 연결 문자열을 포함 하는 응용 프로그램 설정의 이름입니다. 이 `AzureWebJobsStorage` 설정에는 함수 앱을 사용 하 여 만든 저장소 계정에 대 한 연결 문자열이 포함 됩니다. |

이 예제에서는 `bindings` 파일의 function.js에 있는 배열에 다음 바인딩이 추가 됩니다.

```javascript
{
    "type": "queue",
    "direction": "out",
    "name": "msg",
    "queueName": "outqueue",
    "connection": "MyStorageConnection"
}
```

동일한 바인딩 정의를의 function.js에 직접 추가할 수도 있습니다.

함수 코드에서 `msg` 바인딩은 `context` 다음 예제와 같이에서 액세스 됩니다.

```javascript
context.bindings.msg = "Name passed to the function: " req.query.name;
```

자세히 알아보려면 [큐 저장소 출력 바인딩](functions-bindings-storage-queue-output.md) 참조를 참조 하세요.

---

[!INCLUDE [Supported triggers and bindings](../../includes/functions-bindings.md)]

[!INCLUDE [functions-sign-in-vs-code](../../includes/functions-sign-in-vs-code.md)]

## <a name="publish-to-azure"></a>Azure에 게시

Visual Studio Code를 사용 하 여 함수 프로젝트를 Azure에 직접 게시할 수 있습니다. 프로세스를 통해 함수 앱 및 관련된 리소스를 Azure 구독에서 만듭니다. 함수 앱은 함수를 위한 실행 컨텍스트를 제공합니다. 프로젝트는 패키지되어 Azure 구독에서 새 함수 앱에 배포됩니다.

Visual Studio Code에서 Azure의 새 함수 앱에 게시 하는 경우 빠른 함수 앱 만들기 경로와 고급 경로가 모두 제공 됩니다. 

Visual Studio Code에서 게시할 때 [Zip 배포](functions-deployment-technologies.md#zip-deploy) 기술을 활용 합니다. 

### <a name="quick-function-app-create"></a>빠른 함수 앱 만들기

**Azure에서 새 함수 앱 만들기**...를 선택 하면 확장에서 함수 앱에 필요한 Azure 리소스에 대 한 값을 자동으로 생성 합니다. 이러한 값은 선택한 함수 앱 이름을 기반으로 합니다. 기본값을 사용 하 여 Azure의 새 함수 앱에 프로젝트를 게시 하는 예는 [Visual Studio Code 빠른 시작 문서](functions-create-first-function-vs-code.md#publish-the-project-to-azure)를 참조 하세요.

생성 된 리소스에 대 한 명시적 이름을 제공 하려면 고급 만들기 경로를 선택 해야 합니다.

### <a name="publish-a-project-to-a-new-function-app-in-azure-by-using-advanced-options"></a><a name="enable-publishing-with-advanced-create-options"></a>고급 옵션을 사용 하 여 Azure의 새 함수 앱에 프로젝트 게시

다음 단계에서는 고급 만들기 옵션을 사용 하 여 만든 새 함수 앱에 프로젝트를 게시 합니다.

1. **Azure: 함수** 영역에서 **함수 앱에 배포** 아이콘을 선택 합니다.

    ![함수 앱 설정](./media/functions-develop-vs-code/function-app-publish-project.png)

1. 로그인하지 않은 경우 **Azure에 로그인**하라는 메시지가 표시됩니다. **무료 Azure 계정을 만들**수도 있습니다. 브라우저에서 로그인 한 후 Visual Studio Code로 돌아갑니다.

1. 구독이 여러 개인 경우 함수 앱에 대 한 **구독을 선택한** 다음, **Azure에서 새 함수 앱 만들기 ...를 선택 합니다. _고급_**. 이 _고급_ 옵션을 사용 하면 Azure에서 만든 리소스를 보다 강력 하 게 제어할 수 있습니다. 

1. 메시지가 표시 되 면 다음 정보를 제공 합니다.

    | prompt | 값 | 설명 |
    | ------ | ----- | ----------- |
    | Azure에서 함수 앱 선택 | Azure에서 새 함수 앱 만들기 | 다음 프롬프트에서 새 함수 앱을 식별 하는 전역적으로 고유한 이름을 입력 한 다음 Enter 키를 선택 합니다. 함수 앱 이름에 대한 유효한 문자는 `a-z`, `0-9` 및 `-`입니다. |
    | OS 선택 | Windows | 함수 앱은 Windows에서 실행 됩니다. |
    | 호스팅 계획 선택 | 소비 계획 | 서버를 사용 하지 않는 [소비 계획 호스팅을](functions-scale.md#consumption-plan) 사용 합니다. |
    | 새 앱에 대 한 런타임 선택 | 프로젝트 언어 | 런타임은 게시 중인 프로젝트와 일치 해야 합니다. |
    | 새 리소스에 대 한 리소스 그룹을 선택 합니다. | 새 리소스 그룹 만들기 | 다음 프롬프트에서 리소스 그룹 이름 (예:)을 입력 하 `myResourceGroup` 고 enter 키를 선택 합니다. 기존 리소스 그룹을 선택할 수도 있습니다. |
    | 스토리지 계정 선택 | 새 스토리지 계정 만들기 | 다음 프롬프트에서 함수 앱에서 사용 하는 새 저장소 계정에 대 한 전역적으로 고유한 이름을 입력 한 다음 Enter 키를 선택 합니다. Storage 계정 이름은 3 자에서 24 자 사이 여야 하며 숫자와 소문자만 포함할 수 있습니다. 기존 계정을 선택할 수도 있습니다. |
    | 새 리소스의 위치 선택 | region | 가까운 [지역](https://azure.microsoft.com/regions/) 또는 함수에서 액세스 하는 다른 서비스 근처의 위치를 선택 합니다. |

    함수 앱을 만들고 배포 패키지가 적용 된 후 알림이 표시 됩니다. 이 알림에서 **출력 보기**를 선택하여 사용자가 만든 Azure 리소스를 포함한 만들기 및 배포 결과를 표시합니다.

## <a name="republish-project-files"></a>프로젝트 파일 다시 게시

[연속 배포](functions-continuous-deployment.md)를 설정 하는 경우 Azure의 함수 앱은 원본 파일이 연결 된 원본 위치에서 업데이트 될 때마다 업데이트 됩니다. 연속 배포를 권장 하지만 Visual Studio Code에서 프로젝트 파일 업데이트를 다시 게시할 수도 있습니다.

> [!IMPORTANT]
> 기존 함수 앱에 게시하면 Azure에서 해당 앱의 콘텐츠를 덮어씁니다.

[!INCLUDE [functions-republish-vscode](../../includes/functions-republish-vscode.md)]

## <a name="get-the-url-of-the-deployed-function"></a>배포 된 함수의 URL 가져오기

HTTP로 트리거되는 함수를 호출 하려면 함수 앱에 배포 될 때 함수의 URL이 필요 합니다. 이 URL은 필요한 모든 [기능 키](functions-bindings-http-webhook-trigger.md#authorization-keys)를 포함 합니다. 확장을 사용 하 여 배포 된 함수에 대 한 이러한 Url을 가져올 수 있습니다.

1. F1 키를 선택 하 여 명령 팔레트를 연 다음 명령을 검색 하 고 실행 합니다. **함수 URL 복사를 Azure Functions**합니다.

1. 프롬프트에 따라 Azure에서 함수 앱을 선택 하 고 호출 하려는 특정 HTTP 트리거를 선택 합니다.

함수 URL은 쿼리 매개 변수에 의해 전달 된 모든 필수 키와 함께 클립보드로 복사 됩니다 `code` . HTTP 도구를 사용 하 여 POST 요청을 제출 하거나 원격 함수에 GET 요청을 위한 브라우저를 사용 합니다.  

## <a name="run-functions-locally"></a>로컬로 함수 실행

Azure Functions 확장을 사용 하면 로컬 개발 컴퓨터에서 함수 프로젝트를 실행할 수 있습니다. 로컬 런타임은 Azure에서 함수 앱을 호스트 하는 것과 동일한 런타임입니다. [파일의local.settings.js](#local-settings-file)에서 로컬 설정을 읽습니다.

### <a name="additional-requirements-for-running-a-project-locally"></a>프로젝트를 로컬로 실행 하기 위한 추가 요구 사항

함수 프로젝트를 로컬로 실행 하려면 다음과 같은 추가 요구 사항을 충족 해야 합니다.

* [Azure Functions Core Tools](functions-run-local.md#v2)의 버전 2.x 이상을 설치 합니다. 프로젝트를 로컬로 시작 하면 핵심 도구 패키지가 자동으로 다운로드 되어 설치 됩니다. 핵심 도구는 전체 Azure Functions 런타임을 포함 하므로 다운로드 및 설치에 시간이 걸릴 수 있습니다.

* 선택한 언어에 대한 특정 요구 사항을 설치합니다.

    | 언어 | 요구 사항 |
    | -------- | --------- |
    | **C#** | [C# 확장](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)<br/>[.NET Core CLI 도구](/dotnet/core/tools/?tabs=netcore2x)   |
    | **Java** | [Java 용 디버거 확장](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-debug)<br/>[Java 8](https://aka.ms/azure-jdks)<br/>[Maven 3 이상](https://maven.apache.org/) |
    | **JavaScript** | [Node.js](https://nodejs.org/)<sup>*</sup> |  
    | **Python** | [Python 확장](https://marketplace.visualstudio.com/items?itemName=ms-python.python)<br/>[Python 3.6.8](https://www.python.org/downloads/) 권장|

    <sup>*</sup>활성 LTS 및 유지 관리 LTS 버전 (8.11.1 및 10.14.1 권장).

### <a name="configure-the-project-to-run-locally"></a>로컬에서 실행 되도록 프로젝트 구성

함수 런타임은 HTTP 및 웹 후크가 아닌 모든 트리거 형식에 대해 내부적으로 Azure Storage 계정을 사용 합니다. 따라서 **값을 AzureWebJobsStorage** 키를 유효한 Azure Storage 계정 연결 문자열로 설정 해야 합니다.

이 섹션에서는 [Azure Storage 탐색기](https://storageexplorer.com/) [Visual Studio Code에 대 한 Azure Storage 확장](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestorage) 을 사용 하 여 저장소 연결 문자열에 연결 하 고 검색 합니다.

스토리지 계정 연결 문자열을 설정하려면 다음을 수행합니다.

1. Visual Studio에서 **클라우드 탐색기**열고 **저장소 계정**  >  **저장소**계정을 확장 한 다음 **속성** 을 선택 하 고 **기본 연결 문자열** 값을 복사 합니다.

2. 사용자 프로젝트에서 local.settings.json 파일을 열고 **AzureWebJobsStorage** 키의 값을 복사한 연결 문자열로 설정합니다.

3. 이전 단계를 반복하여 사용자의 함수에 필요한 다른 연결에 대한 **값** 배열에 고유 키를 추가합니다.

자세한 내용은 [로컬 설정 파일](#local-settings-file)을 참조 하세요.

### <a name="debugging-functions-locally"></a>로컬에서 함수 디버깅  

함수를 디버깅 하려면 F5 키를 선택 합니다. [핵심 도구][Azure Functions Core Tools]를 아직 다운로드 하지 않은 경우에는이 작업을 수행 하 라는 메시지가 표시 됩니다. 핵심 도구를 설치 하 고 실행 하는 경우 출력은 터미널에 표시 됩니다. 이는 터미널에서 핵심 도구 명령을 실행 하는 것과 같지만 `func host start` 추가 빌드 작업 및 연결 된 디버거를 사용 합니다.  

프로젝트가 실행 되는 동안 프로젝트를 Azure에 배포할 때와 마찬가지로 함수를 트리거할 수 있습니다. 프로젝트가 디버그 모드에서 실행 되는 경우 예상 대로 Visual Studio Code에서 중단점이 적중 됩니다.

HTTP 트리거에 대 한 요청 URL은 터미널의 출력에 표시 됩니다. 프로젝트를 로컬로 실행 하는 경우 HTTP 트리거의 함수 키가 사용 되지 않습니다. 자세한 내용은 [Azure Functions에서 코드를 테스트하기 위한 전략](functions-test-a-function.md)을 참조하세요.  

자세히 알아보려면 [Azure Functions Core Tools][Azure Functions Core Tools]사용을 참조 하세요.

[!INCLUDE [functions-local-settings-file](../../includes/functions-local-settings-file.md)]

기본적으로 이러한 설정은 프로젝트가 Azure에 게시 될 때 자동으로 마이그레이션되지 않습니다. 게시가 완료 된 후 Azure에서 함수 앱에 local.settings.js의 설정을 게시 하는 옵션이 제공 됩니다. 자세히 알아보려면  [응용 프로그램 설정 게시](#publish-application-settings)를 참조 하세요.

**ConnectionStrings**의 값은 절대 게시되지 않습니다.

코드에서 함수 응용 프로그램 설정 값을 환경 변수로 읽을 수도 있습니다. 자세한 내용은 다음 언어 관련 참조 문서의 환경 변수 섹션을 참조 하세요.

* [미리 컴파일된 C#](functions-dotnet-class-library.md#environment-variables)
* [C# 스크립트(.csx)](functions-reference-csharp.md#environment-variables)
* [Java](functions-reference-java.md#environment-variables)
* [JavaScript](functions-reference-node.md#environment-variables)

## <a name="application-settings-in-azure"></a>Azure의 응용 프로그램 설정

프로젝트의 파일에 대 한 local.settings.js설정은 Azure의 함수 앱에 있는 응용 프로그램 설정과 동일 해야 합니다. local.settings.js에 추가 하는 모든 설정은 Azure의 함수 앱에도 추가 해야 합니다. 이러한 설정은 프로젝트를 게시할 때 자동으로 업로드 되지 않습니다. 마찬가지로 [포털의](functions-how-to-use-azure-function-app-settings.md#settings) 함수 앱에서 만드는 모든 설정은 로컬 프로젝트에 다운로드 되어야 합니다.

### <a name="publish-application-settings"></a>응용 프로그램 설정 게시

Azure에서 함수 앱에 필요한 설정을 게시 하는 가장 쉬운 방법은 프로젝트를 게시 한 후에 표시 되는 **업로드 설정** 링크를 사용 하는 것입니다.

![응용 프로그램 설정 업로드](./media/functions-develop-vs-code/upload-app-settings.png)

명령 팔레트에서 **Azure Functions: 로컬 설정 업로드** 명령을 사용 하 여 설정을 게시할 수도 있습니다. **Azure Functions: 새 설정 추가** 명령을 사용 하 여 Azure의 응용 프로그램 설정에 개별 설정을 추가할 수 있습니다.

> [!TIP]
> 게시 하기 전에 파일에 local.settings.js을 저장 해야 합니다.

로컬 파일이 암호화 된 경우 암호 해독, 게시 및 암호화 됩니다. 두 위치에 충돌 하는 값이 있는 설정이 있는 경우 계속 하는 방법을 선택 하 라는 메시지가 표시 됩니다.

**Azure: 함수** 영역에서 구독, 함수 앱 및 **응용 프로그램 설정을**확장 하 여 기존 앱 설정을 봅니다.

![Visual Studio Code에서 함수 앱 설정 보기](./media/functions-develop-vs-code/view-app-settings.png)

### <a name="download-settings-from-azure"></a>Azure에서 설정 다운로드

Azure에서 응용 프로그램 설정을 만든 경우 **Azure Functions: 원격 설정 다운로드** 명령을 사용 하 여 파일의 local.settings.js에 파일을 다운로드할 수 있습니다.

업로드와 마찬가지로 로컬 파일이 암호화 되 면 암호 해독, 업데이트 및 암호화 됩니다. 두 위치에 충돌 하는 값이 있는 설정이 있는 경우 계속 하는 방법을 선택 하 라는 메시지가 표시 됩니다.

## <a name="monitoring-functions"></a>함수 모니터링

함수를 [로컬로 실행](#run-functions-locally)하면 로그 데이터가 터미널 콘솔로 스트리밍됩니다. 함수 프로젝트가 Azure의 함수 앱에서 실행 되는 경우 로그 데이터를 가져올 수도 있습니다. Azure에서 스트리밍 로그에 연결 하 여 거의 실시간으로 로그 데이터를 확인 하거나 함수 앱의 동작 방식에 대 한 보다 완전 한 이해를 위해 Application Insights를 사용 하도록 설정할 수 있습니다.

### <a name="streaming-logs"></a>스트리밍 로그

응용 프로그램을 개발 하는 경우 거의 실시간으로 로깅 정보를 확인 하는 것이 유용한 경우가 많습니다. 함수에 의해 생성 되는 로그 파일의 스트림을 볼 수 있습니다. 이 출력은 HTTP로 트리거되는 함수에 대 한 요청에 대 한 스트리밍 로그의 예입니다.

![HTTP 트리거에 대 한 스트리밍 로그 출력](media/functions-develop-vs-code/streaming-logs-vscode-console.png)

자세히 알아보려면 [스트리밍 로그](functions-monitoring.md#streaming-logs)를 참조 하세요.

[!INCLUDE [functions-enable-log-stream-vs-code](../../includes/functions-enable-log-stream-vs-code.md)]

> [!NOTE]
> 스트리밍 로그는 함수 호스트의 단일 인스턴스만 지원 합니다. 함수를 여러 인스턴스로 확장 하는 경우 다른 인스턴스의 데이터가 로그 스트림에 표시 되지 않습니다. Application Insights [라이브 메트릭 스트림](../azure-monitor/app/live-stream.md) 는 여러 인스턴스를 지원 합니다. 또한 거의 실시간으로 스트리밍 분석은 [샘플링 된 데이터](functions-monitoring.md#configure-sampling)를 기반으로 합니다.

### <a name="application-insights"></a>Application Insights

함수 앱을 Application Insights와 통합 하 여 함수 실행을 모니터링 하는 것이 좋습니다. Azure Portal에서 함수 앱을 만들 때이 통합은 기본적으로 발생 합니다. Visual Studio를 게시 하는 동안 함수 앱을 만들 때 Application Insights 직접 통합 해야 합니다.

[!INCLUDE [functions-connect-new-app-insights.md](../../includes/functions-connect-new-app-insights.md)]

자세히 알아보려면 [Azure Functions 모니터링](functions-monitoring.md)을 참조하세요.

## <a name="c-script-projects"></a>C \# 스크립트 프로젝트

기본적으로 모든 c # 프로젝트는 [c # 컴파일된 클래스 라이브러리 프로젝트로](functions-dotnet-class-library.md)생성 됩니다. 대신 c # 스크립트 프로젝트를 사용 하려면 Azure Functions 확장 프로그램 설정에서 c # 스크립트를 기본 언어로 선택 해야 합니다.

1. **파일**  >  **기본**  >  **설정 설정을**선택 합니다.

1. **사용자 설정**  >  **확장**  >  **Azure Functions**로 이동 합니다.

1. **Azure 함수: 프로젝트 언어**에서 **C # 스크립트** 를 선택 합니다.

이러한 단계를 완료 한 후 기본 핵심 도구에 대 한 호출은 `--csx` c # 스크립트 (csx) 프로젝트 파일을 생성 하 고 게시 하는 옵션을 포함 합니다. 이 기본 언어를 지정 하면 사용자가 만드는 모든 프로젝트는 기본적으로 c # 스크립트 프로젝트로 지정 됩니다. 기본값이 설정 된 경우 프로젝트 언어를 선택 하 라는 메시지가 표시 되지 않습니다. 다른 언어로 프로젝트를 만들려면이 설정을 변경 하거나 파일에 대 한 사용자 settings.js에서 제거 해야 합니다. 이 설정을 제거 하면 프로젝트를 만들 때 언어를 선택 하 라는 메시지가 다시 표시 됩니다.

## <a name="command-palette-reference"></a>명령 팔레트 참조

Azure Functions 확장은 Azure에서 함수 앱과 상호 작용 하기 위한 영역에서 유용한 그래픽 인터페이스를 제공 합니다. 명령 팔레트 (F1) 에서도 같은 기능을 명령으로 사용할 수 있습니다. 이러한 Azure Functions 명령을 사용할 수 있습니다.

|Azure Functions 명령  | 설명  |
|---------|---------|
|**새 설정 추가**  |  Azure에서 새 응용 프로그램 설정을 만듭니다. 자세히 알아보려면 [응용 프로그램 설정 게시](#publish-application-settings)를 참조 하세요. [로컬 설정으로이 설정을 다운로드](#download-settings-from-azure)해야 할 수도 있습니다. |
| **배포 원본 구성** | Azure의 함수 앱을 로컬 Git 리포지토리에 연결 합니다. 자세한 내용은 [Azure Functions에 대 한 연속 배포](functions-continuous-deployment.md)를 참조 하세요. |
| **GitHub 리포지토리에 연결** | 함수 앱을 GitHub 리포지토리에 연결 합니다. |
| **함수 URL 복사** | Azure에서 실행 되는 HTTP 트리거 함수의 원격 URL을 가져옵니다. 자세히 알아보려면 [배포 된 함수의 URL 가져오기](#get-the-url-of-the-deployed-function)를 참조 하세요. |
| **Azure에서 함수 앱 만들기** | Azure의 구독에서 새 함수 앱을 만듭니다. 자세히 알아보려면 [Azure에서 새 함수 앱에 게시](#publish-to-azure)하는 방법에 대 한 섹션을 참조 하세요.        |
| **암호 해독 설정** | **Azure Functions: 암호화 설정**에 의해 암호화 된 [로컬 설정을](#local-settings-file) 해독 합니다.  |
| **함수 앱 삭제** | Azure의 구독에서 함수 앱을 제거 합니다. App Service 계획에 다른 앱이 없는 경우 해당 앱을 삭제 하는 옵션도 제공 됩니다. 저장소 계정 및 리소스 그룹과 같은 다른 리소스는 삭제 되지 않습니다. 모든 리소스를 제거 하려면 대신 [리소스 그룹을 삭제](functions-add-output-binding-storage-queue-vs-code.md#clean-up-resources)해야 합니다. 로컬 프로젝트가 영향을 받지 않습니다. |
|**Delete 함수**  | Azure의 함수 앱에서 기존 함수를 제거 합니다. 이 삭제는 로컬 프로젝트에 영향을 주지 않으므로 대신 함수를 로컬로 제거한 다음 프로젝트를 다시 [게시](#republish-project-files)하는 것이 좋습니다. |
| **프록시 삭제** | Azure의 함수 앱에서 Azure Functions 프록시를 제거 합니다. 프록시에 대 한 자세한 내용은 [Azure Functions 프록시 사용](functions-proxies.md)을 참조 하세요. |
| **설정 삭제** | Azure에서 함수 앱 설정을 삭제 합니다. 이 삭제는 파일의 local.settings.js설정에 영향을 주지 않습니다. |
| **리포지토리의 연결 끊기**  | Azure의 함수 앱과 소스 제어 리포지토리 간의 [연속 배포](functions-continuous-deployment.md) 연결을 제거 합니다. |
| **원격 설정 다운로드** | Azure에서 선택한 함수 앱의 설정을 파일의 local.settings.js로 다운로드 합니다. 로컬 파일이 암호화 되 면 암호 해독, 업데이트 및 암호화 됩니다. 두 위치에 충돌 하는 값이 있는 설정이 있는 경우 계속 하는 방법을 선택 하 라는 메시지가 표시 됩니다. 이 명령을 실행 하기 전에 파일의 local.settings.js에 대 한 변경 내용을 저장 해야 합니다. |
| **설정 편집** | Azure의 기존 함수 앱 설정 값을 변경 합니다. 이 명령은 local.settings.js파일의 설정에 영향을 주지 않습니다.  |
| **설정 암호화** | `Values` [로컬 설정](#local-settings-file)에서 배열의 개별 항목을 암호화 합니다. 이 파일에서 `IsEncrypted` 는를 `true` 사용 하기 전에 로컬 런타임에서 설정의 암호를 해독 하도록 지정 하는도로 설정 됩니다. 로컬 설정을 암호화 하 여 중요 한 정보 누출의 위험을 줄입니다. Azure에서 응용 프로그램 설정은 항상 암호화 된 상태로 저장 됩니다. |
| **지금 함수 실행** | Azure에서 [타이머 트리거 함수](functions-bindings-timer.md) 를 수동으로 시작 합니다. 이 명령은 테스트에 사용 됩니다. Azure에서 HTTP가 아닌 함수를 트리거하는 방법에 대해 자세히 알아보려면 [http로 트리거되는 함수 수동 실행](functions-manually-run-non-http.md)을 참조 하세요. |
| **VS Code에서 사용할 프로젝트를 초기화 합니다.** | 필요한 Visual Studio Code 프로젝트 파일을 기존 함수 프로젝트에 추가 합니다. 이 명령을 사용 하 여 핵심 도구를 사용 하 여 만든 프로젝트를 사용할 수 있습니다. |
| **Azure Functions Core Tools 설치 또는 업데이트** | 함수를 로컬로 실행 하는 데 사용 되는 [Azure Functions Core Tools]를 설치 하거나 업데이트 합니다. |
| **재배포**  | 연결 된 Git 리포지토리의 프로젝트 파일을 Azure의 특정 배포에 다시 배포할 수 있습니다. Visual Studio Code에서 로컬 업데이트를 다시 게시 하려면 프로젝트를 다시 [게시](#republish-project-files)합니다. |
| **설정 이름 바꾸기** | Azure에서 기존 함수 앱 설정의 키 이름을 변경 합니다. 이 명령은 local.settings.js파일의 설정에 영향을 주지 않습니다. Azure에서 설정의 이름을 바꾼 후 [해당 변경 내용을 로컬 프로젝트에 다운로드](#download-settings-from-azure)해야 합니다. |
| **다시 시작** | Azure에서 함수 앱을 다시 시작 합니다. 업데이트를 배포 하면 함수 앱도 다시 시작 됩니다. |
| **AzureWebJobsStorage 설정**| 응용 프로그램 설정의 값을 설정 `AzureWebJobsStorage` 합니다. 이 설정은 Azure Functions에 필요 합니다. Azure에서 함수 앱을 만들 때 설정 됩니다. |
| **시작** | Azure에서 중지 된 함수 앱을 시작 합니다. |
| **스트리밍 로그 시작** | Azure에서 함수 앱에 대 한 스트리밍 로그를 시작 합니다. 로깅 정보를 거의 실시간으로 확인 해야 하는 경우 Azure에서 원격 문제 해결 중에 스트리밍 로그를 사용 합니다. 자세히 알아보려면 [스트리밍 로그](#streaming-logs)를 참조 하세요. |
| **중지** | Azure에서 실행 되는 함수 앱을 중지 합니다. |
| **스트리밍 로그 중지** | Azure에서 함수 앱에 대 한 스트리밍 로그를 중지 합니다. |
| **슬롯 설정으로 전환** | 사용 하도록 설정 하면 지정 된 배포 슬롯에 대 한 응용 프로그램 설정이 유지 됩니다. |
| **제거 Azure Functions Core Tools** | 확장 프로그램에 필요한 Azure Functions Core Tools를 제거 합니다. |
| **로컬 설정 업로드** | local.settings.js파일의 설정을 Azure의 선택한 함수 앱에 업로드 합니다. 로컬 파일이 암호화 되 면 암호 해독, 업로드 및 암호화 됩니다. 두 위치에 충돌 하는 값이 있는 설정이 있는 경우 계속 하는 방법을 선택 하 라는 메시지가 표시 됩니다. 이 명령을 실행 하기 전에 파일의 local.settings.js에 대 한 변경 내용을 저장 해야 합니다. |
| **GitHub에서 커밋 보기** | 함수 앱이 리포지토리에 연결 되어 있을 때 특정 배포의 최신 커밋을 보여 줍니다. |
| **배포 로그 보기** | Azure에서 함수 앱에 대 한 특정 배포에 대 한 로그를 보여 줍니다. |

## <a name="next-steps"></a>다음 단계

Azure Functions Core Tools에 대해 자세히 알아보려면 [Azure Functions Core Tools 사용](functions-run-local.md)을 참조 하세요.

.NET 클래스 라이브러리인 함수를 개발하는 방법에 대해 자세히 알아보려면 [Azure Functions C# 개발자 참조](functions-dotnet-class-library.md)를 참조하세요. 또한이 문서에서는 특성을 사용 하 여 Azure Functions에서 지 원하는 다양 한 유형의 바인딩을 선언 하는 방법에 대 한 링크를 제공 합니다.

[Visual Studio Code용 Azure Functions 확장]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions
[Azure Functions Core Tools]: functions-run-local.md
