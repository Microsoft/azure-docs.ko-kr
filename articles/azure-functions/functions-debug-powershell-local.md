---
title: 로컬로 PowerShell Azure Functions 디버그
description: PowerShell을 사용 하 여 함수를 개발 하는 방법을 이해 합니다.
author: tylerleonhardt
ms.topic: conceptual
ms.date: 04/22/2019
ms.author: tyleonha
ms.reviewer: glenga
ms.openlocfilehash: 36d15858e1fb535189891303a89b00021027f1e8
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74227030"
---
# <a name="debug-powershell-azure-functions-locally"></a>로컬로 PowerShell Azure Functions 디버그

Azure Functions를 통해 PowerShell 스크립트로 함수를 개발할 수 있습니다.

[!INCLUDE [functions-powershell-preview-note](../../includes/functions-powershell-preview-note.md)]

다음 표준 개발 도구를 사용 하 여 PowerShell 스크립트와 마찬가지로 PowerShell 함수를 로컬로 디버그할 수 있습니다.

* [Visual Studio Code](https://code.visualstudio.com/): 전체 powershell 개발 환경을 제공 하는 powershell 확장이 포함 된 Microsoft의 무료, 경량 및 오픈 소스 텍스트 편집기입니다.
* PowerShell 콘솔: 다른 PowerShell 프로세스를 디버깅 하는 데 사용 하는 것과 동일한 명령을 사용 하 여 디버그 합니다.

[Azure Functions Core Tools](functions-run-local.md) 은 PowerShell 함수를 비롯 한 Azure Functions의 로컬 디버깅을 지원 합니다.

## <a name="example-function-app"></a>예제 함수 앱

이 문서에 사용 된 함수 앱에는 단일 HTTP 트리거 함수가 있으며 다음 파일이 있습니다.

```
PSFunctionApp
 | - HttpTriggerFunction
 | | - run.ps1
 | | - function.json
 | - local.settings.json
 | - host.json
 | - profile.ps1
```

이 함수 앱은 [PowerShell 빠른](functions-create-first-function-powershell.md)시작을 완료할 때 표시 되는 것과 비슷합니다.

`run.ps1`의 함수 코드는 다음 스크립트와 같습니다.

```powershell
param($Request)

$name = $Request.Query.Name

if($name) {
    $status = 200
    $body = "Hello $name"
}
else {
    $status = 400
    $body = "Please pass a name on the query string or in the request body."
}

Push-OutputBinding -Name Response -Value ([HttpResponseContext]@{
    StatusCode = $status
    Body = $body
})
```

## <a name="set-the-attach-point"></a>연결 지점 설정

PowerShell 함수를 디버깅 하려면 디버거를 연결 하기 위해 함수를 중지 해야 합니다. `Wait-Debugger` cmdlet은 실행을 중지 하 고 디버거를 대기 합니다.

다음과 같이 `if` 문 바로 위에 `Wait-Debugger` cmdlet에 대한 호출을 추가 하기만 하면 됩니다.

```powershell
param($Request)

$name = $Request.Query.Name

# This is where we will wait for the debugger to attach
Wait-Debugger

if($name) {
    $status = 200
    $body = "Hello $name"
}
# ...
```

디버깅은 `if` 문에서 시작 됩니다. 

`Wait-Debugger` 현재 준비 된 상태에서 Visual Studio Code 또는 PowerShell 콘솔을 사용 하 여 함수를 디버그할 수 있습니다.

## <a name="debug-in-visual-studio-code"></a>Visual Studio Code에서 디버그

Visual Studio Code에서 PowerShell 함수를 디버깅 하려면 다음이 설치 되어 있어야 합니다.

* [Visual Studio Code에 대한 PowerShell 확장](/powershell/scripting/components/vscode/using-vscode)
* [Visual Studio Code용 Azure Functions 확장](functions-create-first-function-vs-code.md)
* [PowerShell Core 6.2 이상](/powershell/scripting/install/installing-powershell-core-on-windows)

이러한 종속성을 설치한 후 기존 PowerShell 함수 프로젝트를 로드 하거나 [첫 번째 Powershell 함수 프로젝트를 만듭니다](functions-create-first-function-powershell.md).

>[!NOTE]
> 프로젝트에 필요한 구성 파일이 없는 경우 추가 하 라는 메시지가 표시 됩니다.

### <a name="set-the-powershell-version"></a>PowerShell 버전 설정

PowerShell Core는 Windows PowerShell과 나란히 설치 됩니다. Powershell Core를 Visual Studio Code PowerShell 확장과 함께 사용할 PowerShell 버전으로 설정 합니다.

1. F1 키를 눌러 명령 팔레트를 표시 한 다음 `Session`를 검색 합니다.

1. **PowerShell: 세션 표시 메뉴**를 선택 합니다.

1. **현재 세션이** **powershell core 6**이 아닌 경우 **전환: powershell core 6**을 선택 합니다.

PowerShell 파일이 열려 있으면 창의 오른쪽 아래에 녹색으로 표시 된 버전이 표시 됩니다. 이 텍스트를 선택 하면 세션 메뉴도 표시 됩니다. 자세히 알아보려면 [확장에 사용할 PowerShell 버전 선택](/powershell/scripting/components/vscode/using-vscode#choosing-a-version-of-powershell-to-use-with-the-extension)을 참조 하세요.

### <a name="start-the-function-app"></a>함수 앱 시작

디버거를 연결할 함수에서 `Wait-Debugger`가 설정 되어 있는지 확인 합니다.  `Wait-Debugger` 추가 되 면 Visual Studio Code를 사용 하 여 함수 앱을 디버그할 수 있습니다.

**디버그** 창을 선택한 다음 **PowerShell 함수에 연결**합니다.

![디버거로](https://user-images.githubusercontent.com/2644648/56166073-8a7b3780-5f89-11e9-85ce-36ed38e221a2.png)

F5 키를 눌러 디버깅을 시작할 수도 있습니다.

디버깅 시작 작업은 다음 작업을 수행 합니다.

* 터미널에서 `func extensions install`를 실행 하 여 함수 앱에 필요한 Azure Functions 확장을 설치 합니다.
* 터미널에서 `func host start`를 실행 하 여 함수 호스트에서 함수 앱을 시작 합니다.
* Powershell 디버거를 함수 런타임 내의 PowerShell runspace에 연결 합니다.

>[!NOTE]
> Visual Studio Code에서 올바른 디버깅 환경을 보장 하려면 PSWorkerInProcConcurrencyUpperBound가 1로 설정 되어 있는지 확인 해야 합니다. 이 값이 기본값입니다.

함수 앱을 실행 하는 경우 HTTP 트리거된 함수를 호출 하는 별도의 PowerShell 콘솔이 필요 합니다.

이 경우 PowerShell 콘솔은 클라이언트입니다. `Invoke-RestMethod`는 함수를 트리거하는 데 사용 됩니다.

PowerShell 콘솔에서 다음 명령을 실행 합니다.

```powershell
Invoke-RestMethod "http://localhost:7071/api/HttpTrigger?Name=Functions"
```

응답이 즉시 반환 되지 않는다는 것을 알 수 있습니다. 이는 `Wait-Debugger` 디버거가 연결 되었고 PowerShell 실행이 중단 모드로 전환 되었기 때문입니다. 이는 나중에 설명 하는 [모든 개념 개념](#breakall-might-cause-your-debugger-to-break-in-an-unexpected-place)으로 인해 발생 합니다. `continue` 단추를 누르면 디버거가 `Wait-Debugger`후 오른쪽 줄에서 중단 됩니다.

이 시점에서 디버거가 연결 되 고 모든 일반 디버거 작업을 수행할 수 있습니다. Visual Studio Code에서 디버거를 사용 하는 방법에 대한 자세한 내용은 [공식 설명서](https://code.visualstudio.com/Docs/editor/debugging#_debug-actions)를 참조 하세요.

계속 해 서 스크립트를 완전히 호출 하면 다음을 확인할 수 있습니다.

* `Invoke-RestMethod` 된 PowerShell 콘솔에서 결과를 반환 했습니다.
* Visual Studio Code의 PowerShell 통합 콘솔이 스크립트 실행을 기다리는 중입니다.

나중에 동일한 함수를 호출 하면 PowerShell 확장의 디버거가 `Wait-Debugger`바로 뒤에서 중단 됩니다.

## <a name="debugging-in-a-powershell-console"></a>PowerShell 콘솔에서 디버깅

>[!NOTE]
> 이 섹션에서는 [Azure Functions Core Tools 문서](functions-run-local.md) 를 읽고 `func host start` 명령을 사용 하 여 함수 앱을 시작 하는 방법을 알고 있다고 가정 합니다.

콘솔을 열고 함수 앱의 디렉터리에 `cd` 다음 명령을 실행 합니다.

```sh
func host start
```

함수 앱을 실행 하 고 `Wait-Debugger`를 사용 하 여 프로세스에 연결할 수 있습니다. 두 개 이상의 PowerShell 콘솔이 필요 합니다.

콘솔 중 하나가 클라이언트 역할을 합니다. 이를 통해 `Invoke-RestMethod`를 호출 하 여 함수를 트리거합니다. 예를 들어 다음 명령을 실행할 수 있습니다.

```powershell
Invoke-RestMethod "http://localhost:7071/api/HttpTrigger?Name=Functions"
```

`Wait-Debugger`의 결과인 응답이 반환 되지 않는다는 것을 알 수 있습니다. 이제 PowerShell runspace는 디버거가 연결 될 때까지 대기 중입니다. 이를 연결 해 보겠습니다.

다른 PowerShell 콘솔에서 다음 명령을 실행 합니다.

```powershell
Get-PSHostProcessInfo
```

이 cmdlet은 다음 출력과 같은 테이블을 반환 합니다.

```output
ProcessName ProcessId AppDomainName
----------- --------- -------------
dotnet          49988 None
pwsh            43796 None
pwsh            49970 None
pwsh             3533 None
pwsh            79544 None
pwsh            34881 None
pwsh            32071 None
pwsh            88785 None
```

`ProcessName` 테이블에서 항목에 대한 `ProcessId`를 `dotnet`로 적어 둡니다. 이 프로세스는 함수 앱입니다.

다음으로 다음 코드 조각을 실행 합니다.

```powershell
# This enters into the Azure Functions PowerShell process.
# Put your value of `ProcessId` here.
Enter-PSHostProcess -Id $ProcessId

# This triggers the debugger.
Debug-Runspace 1
```

서비스가 시작 되 면 디버거가 중단 되 고 다음과 같은 출력이 표시 됩니다.

```
Debugging Runspace: Runspace1

To end the debugging session type the 'Detach' command at the debugger prompt, or type 'Ctrl+C' otherwise.

At /Path/To/PSFunctionApp/HttpTriggerFunction/run.ps1:13 char:1
+ if($name) { ...
+ ~~~~~~~~~~~
[DBG]: [Process:49988]: [Runspace1]: PS /Path/To/PSFunctionApp>>
```

이 시점에서 [PowerShell 디버거의](/powershell/module/microsoft.powershell.core/about/about_debuggers)중단점에서 중지 됩니다. 여기에서 일반적인 디버그 작업, 프로시저 단위 실행, 한 단계씩 코드 실행, 계속, 종료 및 기타 작업을 수행할 수 있습니다. 콘솔에서 사용할 수 있는 디버그 명령의 전체 집합을 확인 하려면 `h` 또는 `?` 명령을 실행 합니다.

`Set-PSBreakpoint` cmdlet을 사용 하 여이 수준에서 중단점을 설정할 수도 있습니다.

계속 해 서 스크립트를 완전히 호출 하면 다음을 확인할 수 있습니다.

* `Invoke-RestMethod`를 실행 한 PowerShell 콘솔에서 이제 결과가 반환 되었습니다.
* `Debug-Runspace` 실행 한 PowerShell 콘솔에서 스크립트가 실행 될 때까지 기다리고 있습니다.

예를 들어 `Invoke-RestMethod`를 사용 하 여 동일한 함수를 다시 호출할 수 있으며 디버거가 `Wait-Debugger` 명령 바로 뒤에서 중단 됩니다.

## <a name="considerations-for-debugging"></a>디버깅 고려 사항

함수 코드를 디버그할 때는 다음 문제를 염두에 두어야 합니다.

### <a name="breakall-might-cause-your-debugger-to-break-in-an-unexpected-place"></a>`BreakAll` 디버거가 예기치 않은 장소에서 중단 될 수 있습니다.

PowerShell 확장은 `Debug-Runspace`를 사용 하며,이는 PowerShell의 `BreakAll` 기능을 사용 합니다. 이 기능은 실행 되는 첫 번째 명령에서 PowerShell을 중지 하도록 지시 합니다. 이 동작은 디버깅 된 runspace 내에서 중단점을 설정할 수 있는 기회를 제공 합니다.

Azure Functions 런타임은 실제로 `run.ps1` 스크립트를 호출 하기 전에 몇 가지 명령을 실행 하므로 디버거가 `Microsoft.Azure.Functions.PowerShellWorker.psm1` 또는 `Microsoft.Azure.Functions.PowerShellWorker.psd1`내에서 중단 될 수 있습니다.

이러한 중단이 발생 하면 `continue` 또는 `c` 명령을 실행 하 여이 중단점을 건너뜁니다. 그런 다음 예상 중단점에서 중지 합니다.

## <a name="next-steps"></a>다음 단계

PowerShell을 사용 하 여 함수를 개발 하는 방법에 대해 자세히 알아보려면 [Azure Functions powershell 개발자 가이드](functions-reference-powershell.md)를 참조 하세요.
