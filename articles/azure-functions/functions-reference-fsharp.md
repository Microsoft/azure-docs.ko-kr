---
title: Azure Functions F# 개발자 참조 | Microsoft Docs
description: F# 스크립트를 사용하여 Azure Functions를 개발하는 방법을 알아봅니다.
services: functions
documentationcenter: fsharp
author: sylvanc
manager: jbronsk
keywords: Azure Functions, 함수, 이벤트 처리, 웹후크, 동적 계산, 서버리스 아키텍처, F#
ms.assetid: e60226e5-2630-41d7-9e5b-9f9e5acc8e50
ms.service: azure-functions
ms.devlang: fsharp
ms.topic: reference
ms.date: 10/09/2018
ms.author: syclebsc
ms.openlocfilehash: 981ffce34c56f4becee2ed0c72da72baa220e395
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61020264"
---
# <a name="azure-functions-f-developer-reference"></a>Azure Functions F# 개발자 참조

Azure Functions용 F#란 클라우드에서 작은 코드 또는 "함수"를 쉽게 실행하기 위한 솔루션입니다. 데이터는 함수 인수를 통해 F# 함수로 흐릅니다. 인수 이름은 `function.json`에 지정되며 함수 로거 및 취소 토큰 같은 항목에 액세스하기 위해 미리 정의된 이름이 있습니다. 

>[!IMPORTANT]
>F# 스크립트(.fsx)는 Azure Functions 런타임 [버전 1.x](functions-versions.md#creating-1x-apps)에서만 지원됩니다. 버전 2.x 런타임에서 F#을 사용하려는 경우 미리 컴파일된 F# 클래스 라이브러리 프로젝트(.fs)를 사용해야 합니다. [C# 클래스 라이브러리 프로젝트](functions-dotnet-class-library.md)를 사용할 때처럼 Visual Studio를 사용하여 F# 클래스 라이브러리 프로젝트를 만들고, 관리하고, 게시합니다. Functions 버전에 대한 자세한 내용은 [Azure Functions 런타임 버전 개요](functions-versions.md)를 참조하세요.

이 문서에서는 [Azure Functions 개발자 참조](functions-reference.md)를 이미 읽었다고 가정합니다.

## <a name="how-fsx-works"></a>.fsx의 작동 방식
`.fsx` 파일은 F# 스크립트입니다. 단일 파일에 포함된 F# 프로젝트로 생각할 수 있습니다. 파일은 프로그램(이 경우 Azure Function)에 대한 코드 및 종속성 관리를 위한 지시문을 모두 포함합니다.

Azure Function에 `.fsx` 를 사용하는 경우 "상용구" 코드 대신 함수에 집중할 수 있도록 자주 필요한 어셈블리가 자동으로 포함됩니다.

## <a name="folder-structure"></a>폴더 구조

F# 스크립트 프로젝트에 필요한 폴더 구조는 다음과 같습니다.

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

함수 앱을 구성하는 데 사용할 수 있는 공유 [host.json](functions-host-json.md) 파일이 있습니다. 각 함수에는 자체 코드 파일(.fsx)과 바인딩 구성 파일(function.json)이 있습니다.

Functions 런타임의 [버전 2.x](functions-versions.md)에 필요한 바인딩 확장은 `extensions.csproj` 파일에 정의되어 있고 실제 라이브러리 파일은 `bin` 폴더에 있습니다. 로컬에서 개발할 때는 [바인딩 확장을 등록](./functions-bindings-register.md#local-development-azure-functions-core-tools)해야 합니다. Azure Portal에서 함수를 개발할 때 이 등록이 자동으로 수행됩니다.

## <a name="binding-to-arguments"></a>인수에 바인딩
각 바인딩은 [Azure Functions 트리거 및 바인딩 개발자 참조](functions-triggers-bindings.md)에 설명된 대로 일부 인수 집합을 지원합니다. 예를 들어 Blob 트리거가 지원하는 인수 바인딩 중 하나는 F# 레코드를 사용하여 표현될 수 있는 POCO입니다. 예를 들면 다음과 같습니다.

```fsharp
type Item = { Id: string }

let Run(blob: string, output: byref<Item>) =
    let item = { Id = "Some ID" }
    output <- item
```

이 F# Azure Function에서는 하나 이상의 인수를 사용하게 됩니다. Azure Functions 인수에 대해 이야기할 때 *입력* 인수 및 *출력* 인수를 언급합니다. 입력 인수란 표현 그대로 F# Azure Function에 입력하는 것입니다. *출력* 인수란 함수에서 데이터를 *출력*하여 다시 전달하는 방식으로 사용되는 변경 가능한 데이터 또는 `byref<>` 인수입니다.

위의 예에서 `blob`은 입력 인수이며 `output`은 출력 인수입니다. 여기서는 `output`에 `byref<>`를 사용합니다(`[<Out>]` 주석을 추가할 필요 없음). `byref<>` 형식을 사용하면 인수가 참조하는 레코드 또는 개체를 함수를 통해 변경할 수 있습니다.

F# 레코드를 입력 형식으로 사용한 경우, Azure Functions 프레임워크에서 필드를 적절하게 설정하려면 레코드를 함수로 전달하기 전에 레코드 정의를 `[<CLIMutable>]` 로 표시해야 합니다. 내부적으로 `[<CLIMutable>]` 은 레코드 속성에 대한 setter를 생성합니다. 예를 들면 다음과 같습니다.

```fsharp
[<CLIMutable>]
type TestObject =
    { SenderName : string
      Greeting : string }

let Run(req: TestObject, log: ILogger) =
    { req with Greeting = sprintf "Hello, %s" req.SenderName }
```

F# 클래스는 들어오고 나가는 인수 모두에 대해서도 사용할 수 있습니다. 클래스의 경우 일반적으로 속성은 getter 및 setter가 필요합니다. 예를 들면 다음과 같습니다.

```fsharp
type Item() =
    member val Id = "" with get,set
    member val Text = "" with get,set

let Run(input: string, item: byref<Item>) =
    let result = Item(Id = input, Text = "Hello from F#!")
    item <- result
```

## <a name="logging"></a>로깅
출력을 F#의 [스트리밍 로그](../app-service/troubleshoot-diagnostic-logs.md)에 기록하려면 함수에 [ILogger](https://docs.microsoft.com/dotnet/api/microsoft.extensions.logging.ilogger) 형식의 인수를 사용해야 합니다. 일관성을 위해 이 인수의 이름을 `log`로 지정하는 것이 좋습니다. 예를 들면 다음과 같습니다.

```fsharp
let Run(blob: string, output: byref<string>, log: ILogger) =
    log.LogInformation(sprintf "F# Azure Function processed a blob: %s" blob)
    output <- input
```

## <a name="async"></a>Async
`async` 워크플로를 사용할 수 있지만 결과는 `Task`를 반환해야 합니다. `Async.StartAsTask`를 사용하여 이를 확인할 수 있습니다. 예:

```fsharp
let Run(req: HttpRequestMessage) =
    async {
        return new HttpResponseMessage(HttpStatusCode.OK)
    } |> Async.StartAsTask
```

## <a name="cancellation-token"></a>취소 토큰
함수가 정상적인 종료를 처리해야 하는 경우 [`CancellationToken`](/dotnet/api/system.threading.cancellationtoken) 인수를 사용할 수 있습니다. 이는 `async`와 결합할 수 있습니다. 예:

```fsharp
let Run(req: HttpRequestMessage, token: CancellationToken)
    let f = async {
        do! Async.Sleep(10)
        return new HttpResponseMessage(HttpStatusCode.OK)
    }
    Async.StartAsTask(f, token)
```

## <a name="importing-namespaces"></a>네임스페이스 가져오기
네임스페이스는 다음과 같은 일반적인 방법으로 열 수 있습니다.

```fsharp
open System.Net
open System.Threading.Tasks
open Microsoft.Extensions.Logging

let Run(req: HttpRequestMessage, log: ILogger) =
    ...
```

다음과 같은 네임스페이스는 자동으로 열립니다.

* `System`
* `System.Collections.Generic`
* `System.IO`
* `System.Linq`
* `System.Net.Http`
* `System.Threading.Tasks`
* `Microsoft.Azure.WebJobs`
* `Microsoft.Azure.WebJobs.Host`.

## <a name="referencing-external-assemblies"></a>외부 어셈블리 참조
마찬가지로 프레임워크 어셈블리 참조는 `#r "AssemblyName"` 지시문을 사용하여 추가할 수 있습니다.

```fsharp
#r "System.Web.Http"

open System.Net
open System.Net.Http
open System.Threading.Tasks
open Microsoft.Extensions.Logging

let Run(req: HttpRequestMessage, log: ILogger) =
    ...
```

다음 어셈블리는 Azure Functions 호스팅 환경에 의해 자동으로 추가됩니다.

* `mscorlib`,
* `System`
* `System.Core`
* `System.Xml`
* `System.Net.Http`
* `Microsoft.Azure.WebJobs`
* `Microsoft.Azure.WebJobs.Host`
* `Microsoft.Azure.WebJobs.Extensions`
* `System.Web.Http`
* `System.Net.Http.Formatting`.

또한 다음 어셈블리는 특수한 경우이며 simplename으로 참조할 수 있습니다(예: `#r "AssemblyName"`).

* `Newtonsoft.Json`
* `Microsoft.WindowsAzure.Storage`
* `Microsoft.ServiceBus`
* `Microsoft.AspNet.WebHooks.Receivers`
* `Microsoft.AspNEt.WebHooks.Common`.

개인 어셈블리를 참조해야 하는 경우 어셈블리 파일을 함수에 상대적인 `bin` 폴더에 업로드하고 파일 이름(예: `#r "MyAssembly.dll"`)을 사용하여 참조할 수 있습니다. 함수 폴더에 파일을 업로드하는 방법에 대한 내용은 패키지 관리에 대한 다음 섹션을 참조하세요.

## <a name="editor-prelude"></a>Editor Prelude
F# 컴파일러 서비스를 지원하는 편집기는 Azure Functions에 자동으로 포함되는 네임스페이스 및 어셈블리를 인식하지 않습니다. 따라서 편집기가 사용 중인 어셈블리를 찾고 네임스페이스를 명시적으로 여는 데 도움이 되는 Prelude를 포함하는 것이 유용할 수 있습니다. 예를 들면 다음과 같습니다.

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

Azure Functions에서 코드를 실행하는 경우 `COMPILED` 정의된 소스를 처리하므로 Editor Prelude는 무시됩니다.

<a name="package"></a>

## <a name="package-management"></a>패키지 관리
F# 함수에서 NuGet 패키지를 사용하려면 `project.json` 파일을 함수 앱의 파일 시스템에 있는 함수의 폴더에 추가합니다. 다음은 NuGet 패키지 참조를 `Microsoft.ProjectOxford.Face` 버전 1.1.0에 추가하는 예제 `project.json` 파일입니다.

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

.NET Framework 4.6만 지원되므로 `project.json` 파일이 다음과 같이 `net46`을 지정하도록 합니다.

`project.json` 파일을 업로드하는 경우 런타임은 패키지를 가져오고 패키지 어셈블리에 참조를 자동으로 추가합니다. `#r "AssemblyName"` 지시문을 추가할 필요가 없습니다. 필요한 `open` 문만 `.fsx` 파일에 추가합니다.

편집기와 F# Compile Services의 더 나은 상호 작용을 위해 참조 어셈블리를 Editor Prelude에 자동으로 넣고 싶을 수 있습니다.

### <a name="how-to-add-a-projectjson-file-to-your-azure-function"></a>`project.json` 파일을 Azure Function에 추가하는 방법
1. 함수 앱을 실행하여 시작하며 이는 Azure 포털에서 함수를 열어 수행할 수 있습니다. 또한 패키지 설치 출력이 표시되는 스트리밍 로그에 액세스할 수 있습니다.
2. `project.json` 파일을 업로드하려면 [함수 앱 파일을 업데이트하는 방법](functions-reference.md#fileupdate)에 설명되어 있는 것 중 하나를 사용합니다. [Azure Functions에 대한 연속 배포](functions-continuous-deployment.md)를 사용하는 경우 `project.json` 파일을 배포 분기에 추가하기 전에 시험삼아 스테이징 분기에 추가할 수 있습니다.
3. `project.json` 파일을 추가하면 함수의 스트리밍 로그에 다음 예제와 유사한 출력이 표시됩니다.

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

## <a name="environment-variables"></a>환경 변수
환경 변수 또는 앱 설정 값을 가져오려면 다음 예제와 같이 `System.Environment.GetEnvironmentVariable`을 사용합니다.

```fsharp
open System.Environment
open Microsoft.Extensions.Logging

let Run(timer: TimerInfo, log: ILogger) =
    log.LogInformation("Storage = " + GetEnvironmentVariable("AzureWebJobsStorage"))
    log.LogInformation("Site = " + GetEnvironmentVariable("WEBSITE_SITE_NAME"))
```

## <a name="reusing-fsx-code"></a>.fsx 코드 다시 사용
`#load` 지시문을 사용하면 다른 `.fsx` 파일의 코드를 사용할 수 있습니다. 예를 들면 다음과 같습니다.

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

`#load` 지시문에 제공하는 경로는 `.fsx` 파일의 위치에 상대적입니다.

* `#load "logger.fsx"`는 함수 폴더에 있는 파일을 로드합니다.
* `#load "package\logger.fsx"`는 함수 폴더의 `package` 폴더에 있는 파일을 로드합니다.
* `#load "..\shared\mylogger.fsx"`는 함수 폴더와 동일한 수준의 `shared` 폴더 즉, `wwwroot`에 있는 파일을 로드합니다.

`#load` 지시문은 `.fs`파일이 아닌 `.fsx`(F# 스크립트) 파일에서만 작동합니다.

## <a name="next-steps"></a>다음 단계
자세한 내용은 다음 리소스를 참조하세요.

* [F# 가이드](/dotnet/articles/fsharp/index)
* [Azure Functions에 대한 모범 사례](functions-best-practices.md)
* [Azure Functions 개발자 참조](functions-reference.md)
* [Azure Functions 트리거 및 바인딩](functions-triggers-bindings.md)
* [Azure Functions 테스트](functions-test-a-function.md)
* [Azure Functions 크기 조정](functions-scale.md)

