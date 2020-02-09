---
title: Azure Functions F# 개발자 참조
description: 스크립트를 사용 하 여 F# Azure Functions를 개발 하는 방법을 알아봅니다.
author: sylvanc
ms.assetid: e60226e5-2630-41d7-9e5b-9f9e5acc8e50
ms.topic: reference
ms.date: 10/09/2018
ms.author: syclebsc
ms.openlocfilehash: 669701f91ab28a4eb734b0346be6515dc44e8685
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74975009"
---
# <a name="azure-functions-f-developer-reference"></a>Azure Functions F# 개발자 참조

F#Azure Functions는 클라우드에서 작은 코드 또는 "함수"를 쉽게 실행 하기 위한 솔루션입니다. 데이터는 함수 인수 F# 를 통해 함수로 흐릅니다. 인수 이름은 `function.json`에서 지정 되며 함수로 거 및 취소 토큰과 같은 항목에 액세스 하기 위한 미리 정의 된 이름이 있습니다. 

>[!IMPORTANT]
>F#스크립트 (.fsx)는 Azure Functions 런타임의 [버전](functions-versions.md#creating-1x-apps) 1.x 에서만 지원 됩니다. 버전 2.x 이상 버전의 F# 런타임을 사용 하려면 미리 컴파일된 F# 클래스 라이브러리 프로젝트 (fs)를 사용 해야 합니다. 클래스 라이브러리 F# 프로젝트와 마찬가지로 Visual Studio를 사용 하 여 클래스 라이브러리 프로젝트를 만들고, 관리 하 고, 게시할 수 있습니다. [ C# ](functions-dotnet-class-library.md) 함수 버전에 대한 자세한 내용은 [Azure Functions 런타임 버전 개요](functions-versions.md)를 참조 하세요.

이 문서에서는 [Azure Functions 개발자 참조](functions-reference.md)를 이미 읽은 것으로 가정 합니다.

## <a name="how-fsx-works"></a>.Fsx 작동 방법
`.fsx` 파일은 F# 스크립트입니다. 단일 파일에 포함 된 F# 프로젝트로 간주할 수 있습니다. 이 파일에는 프로그램에 대한 코드 (이 경우 Azure 함수)와 종속성을 관리 하기 위한 지시문이 모두 포함 되어 있습니다.

Azure 함수에 `.fsx`를 사용 하는 경우 일반적으로 필요한 어셈블리가 자동으로 포함 되어 "상용구" 코드가 아닌 함수에 집중할 수 있습니다.

## <a name="folder-structure"></a>폴더 구조

F# 스크립트 프로젝트의 폴더 구조는 다음과 같습니다.

```
FunctionsProject
 | - MyFirstFunction
 | | - run.fsx
 | | - function.json
 | | - function.proj
 | - MySecondFunction
 | | - run.fsx
 | | - function.json
 | | - function.proj
 | - host.json
 | - extensions.csproj
 | - bin
```

함수 앱을 구성 하는 데 사용할 수 있는 공유 [호스트나 json](functions-host-json.md) 파일이 있습니다. 각 함수에는 고유한 코드 파일 (.fsx) 및 바인딩 구성 파일 (함수인 json)이 있습니다.

[버전 2.x 및 이후 버전](functions-versions.md) 의 함수 런타임에 필요한 바인딩 확장은 `bin` 폴더의 실제 라이브러리 파일을 사용 하 여 `extensions.csproj` 파일에 정의 됩니다. 로컬로 개발 하는 경우 [바인딩 확장을 등록](./functions-bindings-register.md#extension-bundles)해야 합니다. Azure Portal에서 함수를 개발 하는 경우이 등록이 수행 됩니다.

## <a name="binding-to-arguments"></a>인수에 바인딩
각 바인딩은 [Azure Functions 트리거 및 바인딩 개발자 참조](functions-triggers-bindings.md)에 설명 된 대로 일부 인수 집합을 지원 합니다. 예를 들어 blob 트리거의 지원 인수 바인딩 중 하나는 F# 레코드를 사용 하 여 표현할 수 있는 POCO입니다. Példa:

```fsharp
type Item = { Id: string }

let Run(blob: string, output: byref<Item>) =
    let item = { Id = "Some ID" }
    output <- item
```

F# Azure 함수는 하나 이상의 인수를 사용 합니다. Azure Functions 인수에 대해 설명 하면 *입력* 인수 및 *출력* 인수를 참조 합니다. 입력 인수는 F# Azure Function에 대한 입력과 정확히 일치 합니다. *출력* 인수는 변경 가능한 데이터 이거나 *함수에서 데이터를 다시 전달* 하는 방법으로 사용 되는 `byref<>` 인수입니다.

위의 예제에서 `blob`는 입력 인수이 고 `output`는 출력 인수입니다. `output`에 대한 `byref<>`를 사용 했습니다 (`[<Out>]` 주석을 추가할 필요는 없음). `byref<>` 형식을 사용 하면 함수에서 인수가 참조 하는 레코드나 개체를 변경할 수 있습니다.

F# 레코드가 입력 형식으로 사용 되는 경우 Azure Functions 프레임 워크에서 레코드를 함수에 전달 하기 전에 필드를 적절 하 게 설정할 수 있도록 레코드 정의를 `[<CLIMutable>]`로 표시 해야 합니다. 내부적으로 `[<CLIMutable>]` 레코드 속성에 대한 setter를 생성 합니다. Példa:

```fsharp
[<CLIMutable>]
type TestObject =
    { SenderName : string
      Greeting : string }

let Run(req: TestObject, log: ILogger) =
    { req with Greeting = sprintf "Hello, %s" req.SenderName }
```

In F# 및 out 인수 모두에 클래스를 사용할 수도 있습니다. 클래스의 경우 속성에는 일반적으로 getter 및 setter가 필요 합니다. Példa:

```fsharp
type Item() =
    member val Id = "" with get,set
    member val Text = "" with get,set

let Run(input: string, item: byref<Item>) =
    let result = Item(Id = input, Text = "Hello from F#!")
    item <- result
```

## <a name="logging"></a>Naplózás
에서 F# [스트리밍 로그](../app-service/troubleshoot-diagnostic-logs.md) 에 대한 출력을 기록 하려면 함수에서 [ILogger](https://docs.microsoft.com/dotnet/api/microsoft.extensions.logging.ilogger)형식의 인수를 사용 해야 합니다. 일관성을 위해이 인수의 이름을 `log`지정 하는 것이 좋습니다. Példa:

```fsharp
let Run(blob: string, output: byref<string>, log: ILogger) =
    log.LogInformation(sprintf "F# Azure Function processed a blob: %s" blob)
    output <- input
```

## <a name="async"></a>Async
`async` 워크플로를 사용할 수 있지만 결과는 `Task`를 반환 해야 합니다. 이 작업은 `Async.StartAsTask`를 사용 하 여 수행할 수 있습니다. 예를 들면 다음과 같습니다.

```fsharp
let Run(req: HttpRequestMessage) =
    async {
        return new HttpResponseMessage(HttpStatusCode.OK)
    } |> Async.StartAsTask
```

## <a name="cancellation-token"></a>취소 토큰
함수에서 종료를 정상적으로 처리 해야 하는 경우 [`CancellationToken`](/dotnet/api/system.threading.cancellationtoken) 인수를 지정할 수 있습니다. 이를 `async`와 결합할 수 있습니다. 예를 들면 다음과 같습니다.

```fsharp
let Run(req: HttpRequestMessage, token: CancellationToken)
    let f = async {
        do! Async.Sleep(10)
        return new HttpResponseMessage(HttpStatusCode.OK)
    }
    Async.StartAsTask(f, token)
```

## <a name="importing-namespaces"></a>네임 스페이스 가져오기
일반적인 방법으로 네임 스페이스를 열 수 있습니다.

```fsharp
open System.Net
open System.Threading.Tasks
open Microsoft.Extensions.Logging

let Run(req: HttpRequestMessage, log: ILogger) =
    ...
```

다음 네임 스페이스가 자동으로 열립니다.

* `System`
* `System.Collections.Generic`
* `System.IO`
* `System.Linq`
* `System.Net.Http`
* `System.Threading.Tasks`
* `Microsoft.Azure.WebJobs`
* `Microsoft.Azure.WebJobs.Host` kérdésre adott válaszban foglalt lépéseket.

## <a name="referencing-external-assemblies"></a>외부 어셈블리 참조
마찬가지로 `#r "AssemblyName"` 지시문을 사용 하 여 프레임 워크 어셈블리 참조를 추가할 수 있습니다.

```fsharp
#r "System.Web.Http"

open System.Net
open System.Net.Http
open System.Threading.Tasks
open Microsoft.Extensions.Logging

let Run(req: HttpRequestMessage, log: ILogger) =
    ...
```

다음 어셈블리는 Azure Functions 호스팅 환경에 의해 자동으로 추가 됩니다.

* `mscorlib`,
* `System`
* `System.Core`
* `System.Xml`
* `System.Net.Http`
* `Microsoft.Azure.WebJobs`
* `Microsoft.Azure.WebJobs.Host`
* `Microsoft.Azure.WebJobs.Extensions`
* `System.Web.Http`
* `System.Net.Http.Formatting` kérdésre adott válaszban foglalt lépéseket.

또한 다음 어셈블리는 특수 한 대/소문자 이며 simplename (예: `#r "AssemblyName"`)에서 참조할 수 있습니다.

* `Newtonsoft.Json`
* `Microsoft.WindowsAzure.Storage`
* `Microsoft.ServiceBus`
* `Microsoft.AspNet.WebHooks.Receivers`
* `Microsoft.AspNEt.WebHooks.Common` kérdésre adott válaszban foglalt lépéseket.

전용 어셈블리를 참조 해야 하는 경우 함수에 상대적인 `bin` 폴더에 어셈블리 파일을 업로드 하 고 파일 이름을 사용 하 여 참조할 수 있습니다 (예:  `#r "MyAssembly.dll"`). 함수 폴더에 파일을 업로드 하는 방법에 대한 자세한 내용은 패키지 관리에 대한 다음 섹션을 참조 하세요.

## <a name="editor-prelude"></a>편집기 Prelude
컴파일러 서비스를 지 F# 원하는 편집기는 Azure Functions 자동으로 포함 되는 네임 스페이스와 어셈블리를 인식 하지 못합니다. 따라서 편집기에서 사용 중인 어셈블리를 찾고 명시적으로 네임 스페이스를 열 수 있도록 하는 prelude를 포함 하는 것이 유용할 수 있습니다. Példa:

```fsharp
#if !COMPILED
#I "../../bin/Binaries/WebJobs.Script.Host"
#r "Microsoft.Azure.WebJobs.Host.dll"
#endif

open System
open Microsoft.Azure.WebJobs.Host
open Microsoft.Extensions.Logging

let Run(blob: string, output: byref<string>, log: ILogger) =
    ...
```

Azure Functions 코드를 실행 하는 경우 `COMPILED` 정의 된를 사용 하 여 소스를 처리 하므로 prelude 편집기가 무시 됩니다.

<a name="package"></a>

## <a name="package-management"></a>Csomagkezelés
F# 함수에서 NuGet 패키지를 사용 하려면 함수 앱의 파일 시스템에서 함수 폴더에 `project.json` 파일을 추가 합니다. 다음은 `Microsoft.ProjectOxford.Face` 버전 1.1.0에 대한 NuGet 패키지 참조를 추가 하는 `project.json` 파일의 예입니다.

```json
{
  "frameworks": {
    "net46":{
      "dependencies": {
        "Microsoft.ProjectOxford.Face": "1.1.0"
      }
    }
   }
}
```

.NET Framework 4.6만 지원 되므로 `project.json` 파일에서 다음과 같이 `net46`를 지정 해야 합니다.

`project.json` 파일을 업로드 하는 경우 런타임은 패키지를 가져오고 패키지 어셈블리에 대한 참조를 자동으로 추가 합니다. `#r "AssemblyName"` 지시문을 추가할 필요가 없습니다. 필요한 `open` 문을 `.fsx` 파일에 추가 하기만 하면 됩니다.

편집기에서 컴파일 서비스와 F# 의 상호 작용을 향상 시키기 위해 자동으로 어셈블리를 참조 하는 어셈블리를 prelude 수 있습니다.

### <a name="how-to-add-a-projectjson-file-to-your-azure-function"></a>Azure 함수에 `project.json` 파일을 추가 하는 방법
1. 함수 앱이 실행 되 고 있는지 확인 하 여 시작 합니다 .이 작업은 Azure Portal에서 함수를 열어 수행할 수 있습니다. 또한 패키지 설치 출력이 표시 되는 스트리밍 로그에 대한 액세스를 제공 합니다.
2. `project.json` 파일을 업로드 하려면 [함수 앱 파일을 업데이트 하는 방법](functions-reference.md#fileupdate)에서 설명 하는 방법 중 하나를 사용 합니다. [Azure Functions에 대한 연속 배포](functions-continuous-deployment.md)를 사용 하는 경우 배포 분기에 추가 하기 전에 실험을 위해 스테이징 분기에 `project.json` 파일을 추가할 수 있습니다.
3. `project.json` 파일을 추가 하면 함수의 스트리밍 로그에 다음 예제와 유사한 출력이 표시 됩니다.

```
2016-04-04T19:02:48.745 Restoring packages.
2016-04-04T19:02:48.745 Starting NuGet restore
2016-04-04T19:02:50.183 MSBuild auto-detection: using msbuild version '14.0' from 'D:\Program Files (x86)\MSBuild\14.0\bin'.
2016-04-04T19:02:50.261 Feeds used:
2016-04-04T19:02:50.261 C:\DWASFiles\Sites\facavalfunctest\LocalAppData\NuGet\Cache
2016-04-04T19:02:50.261 https://api.nuget.org/v3/index.json
2016-04-04T19:02:50.261
2016-04-04T19:02:50.511 Restoring packages for D:\home\site\wwwroot\HttpTriggerCSharp1\Project.json...
2016-04-04T19:02:52.800 Installing Newtonsoft.Json 6.0.8.
2016-04-04T19:02:52.800 Installing Microsoft.ProjectOxford.Face 1.1.0.
2016-04-04T19:02:57.095 All packages are compatible with .NETFramework,Version=v4.6.
2016-04-04T19:02:57.189
2016-04-04T19:02:57.189
2016-04-04T19:02:57.455 Packages restored.
```

## <a name="environment-variables"></a>Környezeti változók
환경 변수 또는 앱 설정 값을 가져오려면 `System.Environment.GetEnvironmentVariable`를 사용 합니다. 예를 들면 다음과 같습니다.

```fsharp
open System.Environment
open Microsoft.Extensions.Logging

let Run(timer: TimerInfo, log: ILogger) =
    log.LogInformation("Storage = " + GetEnvironmentVariable("AzureWebJobsStorage"))
    log.LogInformation("Site = " + GetEnvironmentVariable("WEBSITE_SITE_NAME"))
```

## <a name="reusing-fsx-code"></a>.Fsx 코드 다시 사용
`#load` 지시어를 사용 하 여 다른 `.fsx` 파일의 코드를 사용할 수 있습니다. Példa:

`run.fsx`

```fsharp
#load "logger.fsx"

let Run(timer: TimerInfo, log: ILogger) =
    mylog log (sprintf "Timer: %s" DateTime.Now.ToString())
```

`logger.fsx`

```fsharp
let mylog(log: ILogger, text: string) =
    log.LogInformation(text);
```

`#load` 지시문에 제공 하는 경로는 `.fsx` 파일의 위치를 기준으로 합니다.

* `#load "logger.fsx"`는 함수 폴더에 있는 파일을 로드 합니다.
* `#load "package\logger.fsx"`는 함수 폴더의 `package` 폴더에 있는 파일을 로드 합니다.
* `#load "..\shared\mylogger.fsx"` 함수 폴더와 동일한 수준의 `shared` 폴더에 있는 파일을 로드 합니다. 즉, `wwwroot`바로 아래에 있는 파일을 로드 합니다.

`#load` 지시어는 `.fs` 파일이 아닌 `.fsx` (F# 스크립트) 파일 에서만 작동 합니다.

## <a name="next-steps"></a>Következő lépések
További információkért lásd a következőket:

* [F#도움이](/dotnet/articles/fsharp/index)
* [Azure Functions – ajánlott eljárások](functions-best-practices.md)
* [Az Azure Functions fejlesztői segédanyagai](functions-reference.md)
* [트리거 및 바인딩 Azure Functions](functions-triggers-bindings.md)
* [Azure Functions 테스트](functions-test-a-function.md)
* [Azure Functions 크기 조정](functions-scale.md)

