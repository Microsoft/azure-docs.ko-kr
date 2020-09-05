---
title: Azure Functions에 대 한 PowerShell 개발자 참조
description: PowerShell을 사용 하 여 함수를 개발 하는 방법을 이해 합니다.
author: eamonoreilly
ms.topic: conceptual
ms.custom: devx-track-dotnet, devx-track-azurepowershell
ms.date: 04/22/2019
ms.openlocfilehash: 1da4154530f823d391aea779011a34a35edfd070
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89071162"
---
# <a name="azure-functions-powershell-developer-guide"></a>Azure Functions PowerShell 개발자 가이드

이 문서에서는 PowerShell을 사용 하 여 Azure Functions를 작성 하는 방법에 대해 자세히 설명 합니다.

PowerShell Azure 함수 (함수)는 트리거될 때 실행 되는 PowerShell 스크립트로 표시 됩니다. 각 함수 스크립트에는 `function.json` 함수의 동작 방식 (예: 트리거되는 방법 및 해당 입력 및 출력 매개 변수)을 정의 하는 관련 파일이 있습니다. 자세한 내용은 [트리거 및 바인딩 문서](functions-triggers-bindings.md)를 참조 하세요. 

다른 종류의 함수와 마찬가지로 PowerShell 스크립트 함수는 파일에 정의 된 모든 입력 바인딩의 이름과 일치 하는 매개 변수를 사용 `function.json` 합니다. `TriggerMetadata`함수를 시작한 트리거에 대 한 추가 정보를 포함 하는 매개 변수도 전달 됩니다.

이 문서에서는 [Azure Functions 개발자 참조](functions-reference.md)를 이미 읽었다고 가정합니다. 또한 [powershell에 대 한 빠른 시작 함수](./functions-create-first-function-vs-code.md?pivots=programming-language-powershell) 를 완료 하 여 첫 번째 powershell 함수를 만들었습니다.

## <a name="folder-structure"></a>폴더 구조

PowerShell 프로젝트에 필요한 폴더 구조는 다음과 같습니다. 이 기본값은 변경 가능합니다. 자세한 내용은 아래의 [scriptFile](#configure-function-scriptfile) 섹션을 참조하세요.

```
PSFunctionApp
 | - MyFirstFunction
 | | - run.ps1
 | | - function.json
 | - MySecondFunction
 | | - run.ps1
 | | - function.json
 | - Modules
 | | - myFirstHelperModule
 | | | - myFirstHelperModule.psd1
 | | | - myFirstHelperModule.psm1
 | | - mySecondHelperModule
 | | | - mySecondHelperModule.psd1
 | | | - mySecondHelperModule.psm1
 | - local.settings.json
 | - host.json
 | - requirements.psd1
 | - profile.ps1
 | - extensions.csproj
 | - bin
```

프로젝트의 루트에는 [`host.json`](functions-host-json.md) 함수 앱을 구성 하는 데 사용할 수 있는 공유 파일이 있습니다. 각 함수에는 고유한 코드 파일 (ps1) 및 바인딩 구성 파일 ()이 있는 폴더가 있습니다 `function.json` . 파일의 부모 디렉터리에 function.js이름은 항상 함수의 이름입니다.

특정 바인딩에는 `extensions.csproj` 파일이 있어야 합니다. [버전 2.x 및 이후 버전](functions-versions.md) 의 함수 런타임에 필요한 바인딩 확장은 `extensions.csproj` 폴더의 실제 라이브러리 파일을 사용 하 여 파일에 정의 됩니다 `bin` . 로컬에서 개발할 때는 [바인딩 확장을 등록](functions-bindings-register.md#extension-bundles)해야 합니다. Azure Portal에서 함수를 개발할 때 이 등록이 자동으로 수행됩니다.

PowerShell 함수 앱에는 함수 앱이 실행 되기 시작할 때 실행 되는를 선택적으로 사용할 수 있습니다 `profile.ps1` (그렇지 않은 경우 *[콜드 시작](#cold-start)* 으로 인식 됨). 자세한 내용은 [PowerShell 프로필](#powershell-profile)을 참조 하세요.

## <a name="defining-a-powershell-script-as-a-function"></a>PowerShell 스크립트를 함수로 정의

기본적으로 Functions 런타임은 `run.ps1`에서 함수를 찾습니다. 여기서 `run.ps1`는 해당하는 `function.json`과 동일한 부모 디렉터리를 공유합니다.

스크립트에 실행 시 많은 인수가 전달 되었습니다. 이러한 매개 변수를 처리 하려면 `param` 다음 예제와 같이 스크립트의 맨 위에 블록을 추가 합니다.

```powershell
# $TriggerMetadata is optional here. If you don't need it, you can safely remove it from the param block
param($MyFirstInputBinding, $MySecondInputBinding, $TriggerMetadata)
```

### <a name="triggermetadata-parameter"></a>TriggerMetadata 매개 변수

`TriggerMetadata`매개 변수는 트리거에 대 한 추가 정보를 제공 하는 데 사용 됩니다. 추가 메타 데이터는 바인딩에 대 한 바인딩과 다르지만 모두 `sys` 다음 데이터를 포함 하는 속성을 포함 합니다.

```powershell
$TriggerMetadata.sys
```

| 속성   | 설명                                     | 형식     |
|------------|-------------------------------------------------|----------|
| UtcNow     | UTC에서 함수가 트리거된 경우        | DateTime |
| MethodName | 트리거된 함수의 이름     | 문자열   |
| RandGuid   | 이 함수 실행에 대 한 고유 guid입니다. | 문자열   |

모든 트리거 형식에는 서로 다른 메타 데이터 집합이 있습니다. 예를 들어의에는 `$TriggerMetadata` `QueueTrigger` ,, 등이 포함 `InsertionTime` `Id` `DequeueCount` 됩니다. 큐 트리거의 메타 데이터에 대 한 자세한 내용은 [큐 트리거의 공식 설명서](functions-bindings-storage-queue-trigger.md#message-metadata)로 이동 하세요. 작업 중인 [트리거에](functions-triggers-bindings.md) 대 한 설명서를 확인 하 여 트리거 메타 데이터 내에 있는 항목을 확인 합니다.

## <a name="bindings"></a>바인딩

PowerShell에서 [바인딩은](functions-triggers-bindings.md) 함수의 function.js에서 구성 및 정의 됩니다. Functions는 다양한 방법으로 바인딩과 상호 작용합니다.

### <a name="reading-trigger-and-input-data"></a>읽기 트리거 및 입력 데이터

트리거 및 입력 바인딩은 함수로 전달 되는 매개 변수로 읽혀집니다. 입력 바인딩에는 function.js의가 `direction` 로 설정 되어 `in` 있습니다. `name`에 정의 된 속성은 `function.json` 블록에 있는 매개 변수의 이름입니다 `param` . PowerShell에서 바인딩에 명명 된 매개 변수를 사용 하므로 매개 변수의 순서가 중요 하지 않습니다. 그러나에 정의 된 바인딩의 순서를 따르는 것이 가장 좋습니다 `function.json` .

```powershell
param($MyFirstInputBinding, $MySecondInputBinding)
```

### <a name="writing-output-data"></a>출력 데이터 쓰기

함수에서 출력 바인딩에는 function.js의가 `direction` 로 설정 되어 `out` 있습니다. Cmdlet을 사용 하 여 `Push-OutputBinding` 함수 런타임에 사용할 수 있는 출력 바인딩에 쓸 수 있습니다. 모든 경우에 정의 된 `name` 대로 바인딩의 속성은 `function.json` `Name` cmdlet의 매개 변수에 해당 합니다 `Push-OutputBinding` .

다음은 함수 스크립트에서를 호출 하는 방법을 보여 줍니다 `Push-OutputBinding` .

```powershell
param($MyFirstInputBinding, $MySecondInputBinding)

Push-OutputBinding -Name myQueue -Value $myValue
```

파이프라인을 통해 특정 바인딩에 대 한 값을 전달할 수도 있습니다.

```powershell
param($MyFirstInputBinding, $MySecondInputBinding)

Produce-MyOutputValue | Push-OutputBinding -Name myQueue
```

`Push-OutputBinding` 는에 대해 지정 된 값에 따라 다르게 동작 합니다 `-Name` .

* 지정 된 이름을 유효한 출력 바인딩으로 확인할 수 없으면 오류가 throw 됩니다.

* 출력 바인딩에서 값의 컬렉션을 허용 하는 경우 `Push-OutputBinding` 에는를 반복적으로 호출 하 여 여러 값을 푸시할 수 있습니다.

* 출력 바인딩에서 singleton 값만 허용 하는 경우 `Push-OutputBinding` 두 번째를 호출 하면 오류가 발생 합니다.

#### <a name="push-outputbinding-syntax"></a>`Push-OutputBinding` 구문

다음은를 호출 하기 위한 유효한 매개 변수입니다 `Push-OutputBinding` .

| 이름 | 유형 | 위치 | Description |
| ---- | ---- |  -------- | ----------- |
| **`-Name`** | String | 1 | 설정 하려는 출력 바인딩의 이름입니다. |
| **`-Value`** | Object | 2 | 파이프라인 ByValue에서 허용 되는 설정 하려는 출력 바인딩의 값입니다. |
| **`-Clobber`** | SwitchParameter | named | 필드 지정 된 경우 지정 된 출력 바인딩에 대해 값이 설정 되도록 합니다. | 

또한 다음과 같은 일반 매개 변수가 지원 됩니다. 
* `Verbose`
* `Debug`
* `ErrorAction`
* `ErrorVariable`
* `WarningAction`
* `WarningVariable`
* `OutBuffer`
* `PipelineVariable`
* `OutVariable` 

자세한 내용은 [About CommonParameters](https://go.microsoft.com/fwlink/?LinkID=113216)을 (를) 참조 하세요.

#### <a name="push-outputbinding-example-http-responses"></a>푸시 OutputBinding 예: HTTP 응답

HTTP 트리거는 이라는 출력 바인딩을 사용 하 여 응답을 반환 `response` 합니다. 다음 예제에서의 출력 바인딩에는 `response` "output #1" 값이 있습니다.

```powershell
PS >Push-OutputBinding -Name response -Value ([HttpResponseContext]@{
    StatusCode = [System.Net.HttpStatusCode]::OK
    Body = "output #1"
})
```

출력은 단일 항목만 허용 하는 HTTP에 대 한 것 이므로 `Push-OutputBinding` 를 두 번째로 호출 하면 오류가 throw 됩니다.

```powershell
PS >Push-OutputBinding -Name response -Value ([HttpResponseContext]@{
    StatusCode = [System.Net.HttpStatusCode]::OK
    Body = "output #2"
})
```

Singleton 값만 허용 하는 출력의 경우 `-Clobber` 컬렉션에를 추가 하는 대신 매개 변수를 사용 하 여 이전 값을 재정의할 수 있습니다. 다음 예에서는 값을 이미 추가 했다고 가정 합니다. `-Clobber`다음 예제의 응답은를 사용 하 여 기존 값을 재정의 하 여 "output #3" 값을 반환 합니다.

```powershell
PS >Push-OutputBinding -Name response -Value ([HttpResponseContext]@{
    StatusCode = [System.Net.HttpStatusCode]::OK
    Body = "output #3"
}) -Clobber
```

#### <a name="push-outputbinding-example-queue-output-binding"></a>푸시 OutputBinding 예: 큐 출력 바인딩

`Push-OutputBinding` 는 [Azure Queue storage 출력 바인딩과](functions-bindings-storage-queue-output.md)같은 출력 바인딩에 데이터를 전송 하는 데 사용 됩니다. 다음 예제에서는 큐에 기록 된 메시지의 값이 "output #1"입니다.

```powershell
PS >Push-OutputBinding -Name outQueue -Value "output #1"
```

저장소 큐에 대 한 출력 바인딩은 여러 출력 값을 허용 합니다. 이 경우 첫 번째 이후에 다음 예제를 호출 하면 "output #1" 및 "output #2"의 두 항목이 포함 된 목록이 큐에 기록 됩니다.

```powershell
PS >Push-OutputBinding -Name outQueue -Value "output #2"
```

다음 예제는 이전 두 번째 이후에 호출 될 때 출력 컬렉션에 두 개의 값을 더 합니다.

```powershell
PS >Push-OutputBinding -Name outQueue -Value @("output #3", "output #4")
```

큐에 기록 될 때 메시지는 "output #1", "output #2", "output #3" 및 "output #4"의 네 가지 값을 포함 합니다.

#### <a name="get-outputbinding-cmdlet"></a>`Get-OutputBinding` cmdlet

Cmdlet을 사용 하 여 `Get-OutputBinding` 출력 바인딩에 대해 현재 설정 된 값을 검색할 수 있습니다. 이 cmdlet은 해당 값을 사용 하 여 출력 바인딩의 이름을 포함 하는 해시 테이블을 검색 합니다. 

다음은를 사용 하 여 `Get-OutputBinding` 현재 바인딩 값을 반환 하는 예입니다.

```powershell
Get-OutputBinding
```

```Output
Name                           Value
----                           -----
MyQueue                        myData
MyOtherQueue                   myData
```

`Get-OutputBinding` 또한에는 `-Name` 다음 예제와 같이 반환 된 바인딩을 필터링 하는 데 사용할 수 있는 라는 매개 변수가 포함 되어 있습니다.

```powershell
Get-OutputBinding -Name MyQ*
```

```Output
Name                           Value
----                           -----
MyQueue                        myData
```

와일드 카드 (*)는에서 지원 됩니다 `Get-OutputBinding` .

## <a name="logging"></a>로깅

PowerShell 함수의 로깅은 일반적인 PowerShell 로깅과 같은 방식으로 작동 합니다. 로깅 cmdlet을 사용 하 여 각 출력 스트림에 쓸 수 있습니다. 각 cmdlet은 함수에서 사용 하는 로그 수준에 매핑됩니다.

| 함수 로깅 수준 | 로깅 cmdlet |
| ------------- | -------------- |
| 오류 | **`Write-Error`** |
| 경고 | **`Write-Warning`**  | 
| 정보 | **`Write-Information`** <br/> **`Write-Host`** <br /> **`Write-Output`**      | 정보 | _정보_ 수준 로깅에 씁니다. |
| 디버그 | **`Write-Debug`** |
| 추적 | **`Write-Progress`** <br /> **`Write-Verbose`** |

이러한 cmdlet 외에도 파이프라인에 쓰여진 모든 항목은 로그 수준으로 리디렉션되고 `Information` 기본 PowerShell 서식으로 표시 됩니다.

> [!IMPORTANT]
> 또는 cmdlet을 사용 하 `Write-Verbose` `Write-Debug` 는 것 만으로는 자세한 정보와 디버그 수준 로깅을 볼 수 없습니다. 또한 실제로 관심 있는 로그 수준을 선언 하는 로그 수준 임계값을 구성 해야 합니다. 자세히 알아보려면 [함수 앱 로그 수준 구성](#configure-the-function-app-log-level)을 참조 하세요.

### <a name="configure-the-function-app-log-level"></a>함수 앱 로그 수준 구성

Azure Functions를 사용 하면 함수에서 로그에 쓰는 방식을 쉽게 제어할 수 있도록 임계값 수준을 정의할 수 있습니다. 콘솔에 기록 된 모든 추적에 대 한 임계값을 설정 하려면 `logging.logLevel.default` [ 참조에host.js] [ `host.json` 파일] 의 속성을 사용 합니다. 이 설정은 함수 앱의 모든 함수에 적용됩니다.

다음 예에서는 모든 함수에 대해 자세한 정보 로깅을 사용 하도록 임계값을 설정 하지만 라는 함수에 대해 디버그 로깅을 사용 하도록 임계값을 설정 합니다 `MyFunction` .

```json
{
    "logging": {
        "logLevel": {
            "Function.MyFunction": "Debug",
            "default": "Trace"
        }
    }
}  
```

자세한 내용은 [host.json 참조]를 참조하세요.

### <a name="viewing-the-logs"></a>로그 보기

함수 앱 Azure에서 실행 중인 경우 Application Insights를 사용 하 여 모니터링할 수 있습니다. 함수 로그 보기 및 쿼리에 대해 자세히 알아보려면 [Azure Functions 모니터링](functions-monitoring.md)을 읽어보세요.

개발을 위해 함수 앱를 로컬로 실행 하는 경우 로그는 기본적으로 파일 시스템에 기록 됩니다. 콘솔에서 로그를 보려면 `AZURE_FUNCTIONS_ENVIRONMENT` 함수 앱를 시작 하기 전에 환경 변수를로 설정 `Development` 합니다.

## <a name="triggers-and-bindings-types"></a>트리거 및 바인딩 형식

함수 앱에서 사용할 수 있는 여러 가지 트리거와 바인딩이 있습니다. 트리거와 바인딩의 전체 목록은 [여기에서 찾을 수 있습니다](functions-triggers-bindings.md#supported-bindings).

모든 트리거와 바인딩은 코드에서 몇 가지 실제 데이터 형식으로 표현 됩니다.

* Hashtable
* 문자열
* byte[]
* int
* double
* HttpRequestContext
* HttpResponseContext

이 목록에서 처음 5 개 형식은 표준 .NET 형식입니다. 마지막 두 개는 [httptrigger 트리거에서](#http-triggers-and-bindings)만 사용 됩니다.

함수의 각 바인딩 매개 변수는 다음 형식 중 하나 여야 합니다.

### <a name="http-triggers-and-bindings"></a>HTTP 트리거 및 바인딩

HTTP, 웹후크 트리거 및 HTTP 출력 바인딩은 요청 및 응답 개체를 사용하여 HTTP 메시지를 나타냅니다.

#### <a name="request-object"></a>요청 개체

스크립트에 전달 되는 request 개체의 형식은 `HttpRequestContext` 다음과 같습니다.

| 속성  | 설명                                                    | 형식                      |
|-----------|----------------------------------------------------------------|---------------------------|
| **`Body`**    | 요청의 본문을 포함하는 개체입니다. `Body` 는 데이터에 따라 가장 적합 한 형식으로 직렬화 됩니다. 예를 들어 데이터가 JSON 인 경우 hashtable로 전달 됩니다. 데이터가 문자열 인 경우 문자열로 전달 됩니다. | 개체 |
| **`Headers`** | 요청 헤더를 포함 하는 사전입니다.                | 사전<문자열, 문자열><sup>*</sup> |
| **`Method`** | 요청의 HTTP 메서드입니다.                                | 문자열                    |
| **`Params`**  | 요청의 라우팅 매개 변수를 포함하는 개체입니다. | 사전<문자열, 문자열><sup>*</sup> |
| **`Query`** | 쿼리 매개 변수를 포함하는 개체입니다.                  | 사전<문자열, 문자열><sup>*</sup> |
| **`Url`** | 요청의 URL입니다.                                        | 문자열                    |

<sup>*</sup> 모든 `Dictionary<string,string>` 키는 대/소문자를 구분 하지 않습니다.

#### <a name="response-object"></a>응답 개체

다시 전송 해야 하는 응답 개체는 다음과 같은 속성을 포함 하는 형식입니다 `HttpResponseContext` .

| 속성      | 설명                                                 | 형식                      |
|---------------|-------------------------------------------------------------|---------------------------|
| **`Body`**  | 응답의 본문을 포함하는 개체입니다.           | 개체                    |
| **`ContentType`** | 응답의 콘텐츠 형식을 설정 하는 데 사용할 짧은 손입니다. | 문자열                    |
| **`Headers`** | 응답 헤더를 포함하는 개체입니다.               | 사전 또는 해시 테이블   |
| **`StatusCode`**  | 응답의 HTTP 상태 코드입니다.                       | 문자열 또는 int             |

#### <a name="accessing-the-request-and-response"></a>요청 및 응답 액세스

HTTP 트리거를 사용 하는 경우 다른 입력 바인딩과 동일한 방식으로 HTTP 요청에 액세스할 수 있습니다. `param`블록에 있습니다.

다음과 같이 `HttpResponseContext` 개체를 사용 하 여 응답을 반환 합니다.

`function.json`

```json
{
  "bindings": [
    {
      "type": "httpTrigger",
      "direction": "in",
      "authLevel": "anonymous"
    },
    {
      "type": "http",
      "direction": "out"
    }
  ]
}
```

`run.ps1`

```powershell
param($req, $TriggerMetadata)

$name = $req.Query.Name

Push-OutputBinding -Name res -Value ([HttpResponseContext]@{
    StatusCode = [System.Net.HttpStatusCode]::OK
    Body = "Hello $name!"
})
```

이 함수를 호출한 결과는 다음과 같습니다.

```
PS > irm http://localhost:5001?Name=Functions
Hello Functions!
```

### <a name="type-casting-for-triggers-and-bindings"></a>트리거 및 바인딩에 대 한 형식 캐스팅

Blob 바인딩과 같은 특정 바인딩의 경우 매개 변수의 형식을 지정할 수 있습니다.

예를 들어 문자열로 제공 된 Blob storage의 데이터를 포함 하려면 내 블록에 다음 형식 캐스트를 추가 합니다 `param` .

```powershell
param([string] $myBlob)
```

## <a name="powershell-profile"></a>PowerShell 프로필

Powershell에는 PowerShell 프로필 이라는 개념이 있습니다. PowerShell 프로필에 대해 잘 모르는 경우 [프로필 정보](/powershell/module/microsoft.powershell.core/about/about_profiles)를 참조 하세요.

Powershell 함수에서 프로필 스크립트는 처음 배포 될 때 앱의 PowerShell 작업자 인스턴스당 한 번씩, 그리고 유휴 상태 ([콜드](#cold-start)부팅) 후에 한 번 실행 됩니다. [PSWorkerInProcConcurrencyUpperBound](#concurrency) 값을 설정 하 여 동시성을 사용 하도록 설정 하면 생성 된 각 runspace에 대해 프로필 스크립트가 실행 됩니다.

Visual Studio Code 및 Azure Functions Core Tools와 같은 도구를 사용 하 여 함수 앱을 만들면 기본값이 `profile.ps1` 만들어집니다. 기본 프로필은 [핵심 도구 GitHub 리포지토리에서](https://github.com/Azure/azure-functions-core-tools/blob/dev/src/Azure.Functions.Cli/StaticResources/profile.ps1) 유지 관리 되며 다음을 포함 합니다.

* Azure에 대 한 자동 MSI 인증.
* `AzureRM`원하는 경우 Azure PowerShell PowerShell 별칭을 켜는 기능입니다.

## <a name="powershell-versions"></a>PowerShell 버전

다음 표에서는 함수 런타임의 각 주 버전에서 사용할 수 있는 PowerShell 버전 및 필요한 .NET 버전을 보여 줍니다.

| Functions 버전 | PowerShell 버전                               | .NET 버전  | 
|-------------------|--------------------------------------------------|---------------|
| 3(sp3) (권장) | PowerShell 7 (권장)<br/>PowerShell Core 6 | .NET Core 3.1<br/>.NET Core 2.1 |
| 2.x               | PowerShell Core 6                                | .NET Core 2.2 |

모든 함수에서 인쇄 하 여 현재 버전을 확인할 수 있습니다 `$PSVersionTable` .

### <a name="running-local-on-a-specific-version"></a>특정 버전에서 로컬 실행

로컬로 실행 하는 경우 Azure Functions 런타임 기본값은 PowerShell Core 6을 사용 합니다. 로컬에서 실행할 때 PowerShell 7을 사용 하려면 `"FUNCTIONS_WORKER_RUNTIME_VERSION" : "~7"` `Values` 프로젝트 루트의 local.setting.js파일에 있는 배열에 설정을 추가 해야 합니다. PowerShell 7에서 로컬로 실행 하는 경우 파일에 대 한 local.settings.js은 다음 예제와 같습니다. 

```json
{
  "IsEncrypted": false,
  "Values": {
    "AzureWebJobsStorage": "",
    "FUNCTIONS_WORKER_RUNTIME": "powershell",
    "FUNCTIONS_WORKER_RUNTIME_VERSION" : "~7"
  }
}
```

### <a name="changing-the-powershell-version"></a>PowerShell 버전 변경

PowerShell Core 6에서 PowerShell 7로 업그레이드 하려면 함수 앱이 버전 3(sp3)에서 실행 되어야 합니다. 이 작업을 수행 하는 방법에 대 한 자세한 내용은 [현재 런타임 버전 보기 및 업데이트](set-runtime-version.md#view-and-update-the-current-runtime-version)를 참조 하세요.

함수 앱에서 사용 하는 PowerShell 버전을 변경 하려면 다음 단계를 사용 합니다. Azure Portal에서 또는 PowerShell을 사용 하 여이 작업을 수행할 수 있습니다.

# <a name="portal"></a>[포털](#tab/portal)

1. [Azure Portal](https://portal.azure.com)에서 함수 앱으로 이동합니다.

1. **설정**아래에서 **구성**을 선택 합니다. **일반 설정** 탭에서 **PowerShell 버전**을 찾습니다. 

    :::image type="content" source="media/functions-reference-powershell/change-powershell-version-portal.png" alt-text="함수 앱에서 사용 하는 PowerShell 버전 선택"::: 

1. 원하는 **PowerShell Core 버전** 을 선택 하 고 **저장**을 선택 합니다. 보류 중인 다시 시작에 대 한 경고가 표시 되 면 **계속**을 선택 합니다. 선택한 PowerShell 버전에서 함수 앱이 다시 시작 됩니다. 

# <a name="powershell"></a>[PowerShell](#tab/powershell)

다음 스크립트를 실행 하 여 PowerShell 버전을 변경 합니다. 

```powershell
Set-AzResource -ResourceId "/subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP>/providers/Microsoft.Web/sites/<FUNCTION_APP>/config/web" -Properties @{  powerShellVersion  = '<VERSION>' } -Force -UsePatchSemantics

```

`<SUBSCRIPTION_ID>`, `<RESOURCE_GROUP>` 및를 `<FUNCTION_APP>` Azure 구독의 ID, 리소스 그룹 및 함수 앱의 이름으로 바꿉니다.  또한를 `<VERSION>` 또는로 바꿉니다 `~6` `~7` . `powerShellVersion`반환 된 해시 테이블에서 설정의 업데이트 된 값을 확인할 수 있습니다 `Properties` . 

---

구성이 변경 된 후 함수 앱이 다시 시작 됩니다.

## <a name="dependency-management"></a>종속성 관리

함수를 사용 하 여 종속성 관리를 위한 [PowerShell 갤러리](https://www.powershellgallery.com) 를 활용할 수 있습니다. 종속성 관리를 사용 하는 경우 requirements.psd1 파일을 사용 하 여 필요한 모듈을 자동으로 다운로드 합니다. `managedDependency` `true` 다음 예제와 같이 [파일의host.js](functions-host-json.md)루트에서 속성을로 설정 하 여이 동작을 사용 하도록 설정 합니다.

```json
{
  "managedDependency": {
          "enabled": true
       }
}
```

새 PowerShell 함수 프로젝트를 만들 때 기본적으로 Azure [ `Az` 모듈이](/powershell/azure/new-azureps-module-az) 포함 된 종속성 관리가 사용 됩니다. 현재 지원 되는 모듈의 최대 수는 10 개입니다. _`MajorNumber`_ `.*` 다음 requirements.psd1 예에 표시 된 것 처럼 지원 되는 구문은 또는 정확한 모듈 버전입니다.

```powershell
@{
    Az = '1.*'
    SqlServer = '21.1.18147'
}
```

requirements.psd1 파일을 업데이트 하는 경우 다시 시작한 후 업데이트 된 모듈이 설치 됩니다.

> [!NOTE]
> 관리 되는 종속성이 모듈을 다운로드 하려면 www.powershellgallery.com에 액세스 해야 합니다. 로컬로 실행 하는 경우 런타임에서 필요한 방화벽 규칙을 추가 하 여이 URL에 액세스할 수 있는지 확인 합니다.

> [!NOTE]
> 관리 되는 종속성은 현재 대화형으로 라이선스를 수락 하거나를 호출할 때 스위치를 제공 하 여 사용자가 라이선스를 수락 해야 하는 모듈을 지원 하지 않습니다 `-AcceptLicense` `Install-Module` .

다음 응용 프로그램 설정을 사용 하 여 관리 되는 종속성을 다운로드 하 고 설치 하는 방법을 변경할 수 있습니다. 앱 업그레이드는 내에서 시작 되 `MDMaxBackgroundUpgradePeriod` 고, 업그레이드 프로세스는 약에서 완료 됩니다 `MDNewSnapshotCheckPeriod` .

| 함수 앱 설정              | 기본값             | 설명                                         |
|   -----------------------------   |   -------------------     |  -----------------------------------------------    |
| **`MDMaxBackgroundUpgradePeriod`**      | `7.00:00:00` (7 일)     | 각 PowerShell 작업자 프로세스는 프로세스 시작 시와 그 이후에 PowerShell 갤러리에서 모듈 업그레이드 확인을 시작 `MDMaxBackgroundUpgradePeriod` 합니다. PowerShell 갤러리에서 새 모듈 버전을 사용할 수 있는 경우이 버전은 파일 시스템에 설치 되며 PowerShell 작업자에 게 제공 됩니다. 이 값을 줄이면 함수 앱에서 최신 모듈 버전을 보다 빨리 가져올 수 있지만, 앱 리소스 사용량 (네트워크 i/o, CPU, 저장소)도 늘어납니다. 이 값을 늘려도 앱의 리소스 사용량이 줄어들지만 앱에 새 모듈 버전을 전달 하는 작업이 지연 될 수도 있습니다. | 
| **`MDNewSnapshotCheckPeriod`**         | `01:00:00` (1 시간)       | 새 모듈 버전이 파일 시스템에 설치 된 후에는 모든 PowerShell 작업자 프로세스를 다시 시작 해야 합니다. PowerShell 작업자를 다시 시작 하면 현재 함수 실행을 중단할 수 있으므로 앱 사용 가능성에 영향을 줍니다. 모든 PowerShell 작업자 프로세스가 다시 시작 될 때까지 함수 호출은 이전 또는 새 모듈 버전을 사용할 수 있습니다. 내에서 완료 되는 모든 PowerShell 작업자를 다시 시작 `MDNewSnapshotCheckPeriod` 합니다. 이 값을 늘리면 중단 빈도가 줄어들지만 함수 호출로 인해 이전 또는 새 모듈 버전이 명확 하지 않은 경우에도 시간이 길어질 수 있습니다. |
| **`MDMinBackgroundUpgradePeriod`**      | `1.00:00:00` (1 일)     | 자주 실행 하는 작업자를 다시 시작할 때 과도 한 모듈 업그레이드가 발생 하지 않도록 하기 위해 작업자 중 마지막에 체크 인을 이미 시작한 경우에는 모듈 업그레이드를 확인 하지 않습니다 `MDMinBackgroundUpgradePeriod` . |

사용자 고유의 사용자 지정 모듈을 활용 하는 것은 일반적으로 수행 하는 방법과 약간 다릅니다.

로컬 컴퓨터에서 모듈은에서 전역적으로 사용 가능한 폴더 중 하나에 설치 `$env:PSModulePath` 됩니다. Azure에서 실행 하는 경우 컴퓨터에 설치 된 모듈에 액세스할 수 없습니다. 즉, `$env:PSModulePath` powershell 함수 앱의는 `$env:PSModulePath` 일반 powershell 스크립트와 다릅니다.

함수에는 `PSModulePath` 두 개의 경로가 포함 되어 있습니다.

* `Modules`함수 앱의 루트에 있는 폴더입니다.
* `Modules`PowerShell 언어 작업자에 의해 제어 되는 폴더의 경로입니다.


### <a name="function-app-level-modules-folder"></a>함수 앱 수준 `Modules` 폴더

사용자 지정 모듈을 사용 하기 위해 함수가 폴더에 종속 되는 모듈을 저장할 수 있습니다 `Modules` . 이 폴더에서 모듈은 함수 런타임에 자동으로 사용할 수 있습니다. 함수 앱의 모든 함수는 이러한 모듈을 사용할 수 있습니다. 

> [!NOTE]
> requirements.psd1 파일에 지정 된 모듈은 자동으로 다운로드 되어 경로에 포함 되므로 modules 폴더에 포함할 필요가 없습니다. 이러한 `$env:LOCALAPPDATA/AzureFunctions` 폴더는 `/data/ManagedDependencies` 클라우드에서 실행 될 때 폴더 및 폴더에 로컬로 저장 됩니다.

사용자 지정 모듈 기능을 활용 하려면 `Modules` 함수 앱의 루트에 폴더를 만듭니다. 함수에서 사용 하려는 모듈을이 위치에 복사 합니다.

```powershell
mkdir ./Modules
Copy-Item -Path /mymodules/mycustommodule -Destination ./Modules -Recurse
```

폴더를 사용 하는 `Modules` 경우 함수 앱은 다음과 같은 폴더 구조를 가져야 합니다.

```
PSFunctionApp
 | - MyFunction
 | | - run.ps1
 | | - function.json
 | - Modules
 | | - MyCustomModule
 | | - MyOtherCustomModule
 | | - MySpecialModule.psm1
 | - local.settings.json
 | - host.json
 | - requirements.psd1
```

함수 앱을 시작 하는 경우 PowerShell 언어 작업자는이 `Modules` 폴더를에 추가 `$env:PSModulePath` 하므로 일반 powershell 스크립트에서와 마찬가지로 모듈 자동 로드에 의존할 수 있습니다.

### <a name="language-worker-level-modules-folder"></a>언어 작업자 수준 `Modules` 폴더

여러 모듈은 PowerShell 언어 작업자에서 일반적으로 사용 됩니다. 이러한 모듈은의 마지막 위치에 정의 됩니다 `PSModulePath` . 

모듈의 현재 목록은 다음과 같습니다.

* [Microsoft. PowerShell Archive](https://www.powershellgallery.com/packages/Microsoft.PowerShell.Archive):, 등의 보관 작업에 사용 되는 `.zip` 모듈 `.nupkg` 입니다.
* **Threadjob**: PowerShell 작업 api의 스레드 기반 구현입니다.

기본적으로 함수는 이러한 모듈의 최신 버전을 사용 합니다. 특정 모듈 버전을 사용 하려면 함수 앱의 폴더에 특정 버전을 배치 `Modules` 합니다.

## <a name="environment-variables"></a>환경 변수

Functions에서 [앱 설정](functions-app-settings.md)(예: 서비스 연결 문자열)은 실행 중에 환경 변수로 노출됩니다. 다음 예제와 같이를 사용 하 여 이러한 설정에 액세스할 수 있습니다 `$env:NAME_OF_ENV_VAR` .

```powershell
param($myTimer)

Write-Host "PowerShell timer trigger function ran! $(Get-Date)"
Write-Host $env:AzureWebJobsStorage
Write-Host $env:WEBSITE_SITE_NAME
```

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

로컬로 실행하는 경우 앱 설정은 [local.settings.json](functions-run-local.md#local-settings-file) 프로젝트 파일에서 읽습니다.

## <a name="concurrency"></a>동시성

기본적으로 함수 PowerShell 런타임은 한 번에 하나의 함수 호출만 처리할 수 있습니다. 그러나 다음과 같은 경우에는이 동시성 수준으로 충분 하지 않을 수 있습니다.

* 동시에 다 수의 호출을 처리 하려고 하는 경우
* 동일한 함수 앱 내에서 다른 함수를 호출 하는 함수가 있는 경우

작업 유형에 따라 탐색할 수 있는 몇 가지 동시성 모델이 있습니다.

* 증가 ```FUNCTIONS_WORKER_PROCESS_COUNT``` . 이렇게 하면 특정 CPU 및 메모리 오버 헤드를 야기 하는 동일한 인스턴스 내의 여러 프로세스에서 함수 호출을 처리할 수 있습니다. 일반적으로 i/o 바인딩된 함수는 이러한 오버 헤드로 인해 발생 하지 않습니다. CPU 바인딩된 함수의 경우에는 영향이 중요할 수 있습니다.

* ```PSWorkerInProcConcurrencyUpperBound```앱 설정 값을 늘립니다. 이렇게 하면 동일한 프로세스 내에서 여러 runspace를 만들 수 있으므로 CPU와 메모리 오버 헤드가 크게 줄어듭니다.

이러한 환경 변수는 함수 앱의 [앱 설정](functions-app-settings.md) 에서 설정 합니다.

사용 사례에 따라 Durable Functions 확장성이 크게 향상 될 수 있습니다. 자세히 알아보려면 [Durable Functions 응용 프로그램 패턴](/azure/azure-functions/durable/durable-functions-overview?tabs=powershell#application-patterns)을 참조 하세요.

>[!NOTE]
> "사용 가능한 runspace 없어서 요청이 큐에 대기 중입니다." 경고가 나타날 수 있습니다 .이 오류는 오류가 아닙니다. 메시지에는 요청이 대기 중임을 알리는 메시지가 표시 되 고 이전 요청이 완료 되 면 요청이 처리 됩니다.

### <a name="considerations-for-using-concurrency"></a>동시성 사용에 대 한 고려 사항

PowerShell은 기본적으로 _단일 스레드_ 스크립팅 언어입니다. 그러나 동일한 프로세스에서 여러 PowerShell runspace을 사용 하 여 동시성을 추가할 수 있습니다. 만든 runspace의 양은 ```PSWorkerInProcConcurrencyUpperBound``` 응용 프로그램 설정과 일치 합니다. 처리량은 선택한 계획에서 사용할 수 있는 CPU 및 메모리의 양에 따라 영향을 받습니다.

Azure PowerShell은 몇 가지 _프로세스 수준_ 컨텍스트 및 상태를 사용 하 여 과도 한 입력을 저장 하는 데 도움이 됩니다. 그러나 함수 앱에서 동시성을 설정 하 고 상태를 변경 하는 작업을 호출 하는 경우 경합 상태가 발생할 수 있습니다. 이러한 경합 상태는 한 호출이 특정 상태를 사용 하 고 다른 호출이 상태를 변경 하기 때문에 디버깅 하기 어렵습니다.

일부 작업에는 상당한 시간이 걸릴 수 있으므로 Azure PowerShell 동시성에 때 엄청난 값이 있습니다. 그러나 주의 해야 합니다. 경합 상태가 발생 한 것으로 의심 되는 경우 PSWorkerInProcConcurrencyUpperBound 앱 설정을로 설정 하 `1` 고 대신 동시성에 대해 [언어 작업자 프로세스 수준 격리](functions-app-settings.md#functions_worker_process_count) 를 사용 합니다.

## <a name="configure-function-scriptfile"></a>Configure 함수 `scriptFile`

기본적으로 PowerShell 함수는 `run.ps1` 해당와 동일한 부모 디렉터리를 공유 하는 파일에서 실행 됩니다 `function.json` .

`scriptFile`의 속성을 `function.json` 사용 하 여 다음 예제와 같은 폴더 구조를 가져올 수 있습니다.

```
FunctionApp
 | - host.json
 | - myFunction
 | | - function.json
 | - lib
 | | - PSFunction.ps1
```

이 경우의에는 `function.json` `myFunction` `scriptFile` 실행할 내보낸 함수가 있는 파일을 참조 하는 속성이 포함 됩니다.

```json
{
  "scriptFile": "../lib/PSFunction.ps1",
  "bindings": [
    // ...
  ]
}
```

## <a name="use-powershell-modules-by-configuring-an-entrypoint"></a>EntryPoint를 구성 하 여 PowerShell 모듈 사용

이 문서에서는 템플릿에서 생성 된 기본 스크립트 파일에 PowerShell 함수를 보여 줍니다 `run.ps1` .
그러나 PowerShell 모듈에 함수를 포함할 수도 있습니다. `scriptFile` `entryPoint` function.js의 구성 파일에서 및 필드를 사용 하 여 모듈에서 특정 함수 코드를 참조할 수 있습니다.

이 경우 `entryPoint` 는에서 참조 되는 PowerShell 모듈에 있는 함수 또는 cmdlet의 이름입니다 `scriptFile` .

다음 폴더 구조를 고려 하십시오.

```
FunctionApp
 | - host.json
 | - myFunction
 | | - function.json
 | - lib
 | | - PSFunction.psm1
```

여기 `PSFunction.psm1` 에는 다음이 포함 됩니다.

```powershell
function Invoke-PSTestFunc {
    param($InputBinding, $TriggerMetadata)

    Push-OutputBinding -Name OutputBinding -Value "output"
}

Export-ModuleMember -Function "Invoke-PSTestFunc"
```

이 예제에서에 대 한 구성에는 `myFunction` `scriptFile` `PSFunction.psm1` 다른 폴더의 PowerShell 모듈인를 참조 하는 속성이 포함 되어 있습니다.  `entryPoint`속성은 `Invoke-PSTestFunc` 모듈의 진입점 인 함수를 참조 합니다.

```json
{
  "scriptFile": "../lib/PSFunction.psm1",
  "entryPoint": "Invoke-PSTestFunc",
  "bindings": [
    // ...
  ]
}
```

이 구성을 사용 하면가 `Invoke-PSTestFunc` 그대로 실행 됩니다 `run.ps1` .

## <a name="considerations-for-powershell-functions"></a>PowerShell 함수에 대 한 고려 사항

PowerShell 함수를 사용 하는 경우 다음 섹션의 고려 사항에 유의 하세요.

### <a name="cold-start"></a>콜드 부팅

서버를 사용 하지 않는 [호스팅 모델](functions-scale.md#consumption-plan)에서 Azure Functions를 개발 하는 경우 콜드 시작은 현실입니다. *콜드 시작* 은 함수 앱이 요청을 처리 하기 위해 실행 되기 시작 하는 데 걸리는 시간을 나타냅니다. 비활성 기간 동안 함수 앱이 종료 되기 때문에 소비 계획에서 콜드 시작이 더 자주 발생 합니다.

### <a name="bundle-modules-instead-of-using-install-module"></a>를 사용 하는 대신 번들 모듈 `Install-Module`

모든 호출에서 스크립트가 실행 됩니다. `Install-Module`스크립트에서를 사용 하지 마십시오. 대신 `Save-Module` 함수에서 모듈 다운로드 시간을 낭비 하지 않아도 되도록 게시 하기 전에를 사용 합니다. 콜드 시작이 함수에 영향을 주는 경우에는 *always on* 또는 [프리미엄 계획](functions-scale.md#premium-plan)으로 설정 된 [App Service 계획](functions-scale.md#app-service-plan) 에 함수 앱을 배포 하는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음 자료를 참조하세요.

* [Azure Functions에 대한 모범 사례](functions-best-practices.md)
* [Azure Functions 개발자 참조](functions-reference.md)
* [Azure Functions 트리거 및 바인딩](functions-triggers-bindings.md)

[host.json 참조]: functions-host-json.md
