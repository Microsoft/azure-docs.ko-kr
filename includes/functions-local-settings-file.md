---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 04/14/2019
ms.author: glenga
ms.openlocfilehash: 47e1c509e8b7b60e889e1202b49b1a145c68162c
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88929496"
---
## <a name="local-settings-file"></a>로컬 설정 파일

local.settings.json 파일은 로컬 개발 도구에서 사용하는 앱 설정, 연결 문자열 및 설정을 저장합니다. local.settings.json 파일의 설정은 프로젝트를 로컬에서 실행할 때만 사용됩니다. 로컬 설정 파일의 구조는 다음과 같습니다.

```json
{
  "IsEncrypted": false,
  "Values": {
    "FUNCTIONS_WORKER_RUNTIME": "<language worker>",
    "AzureWebJobsStorage": "<connection-string>",
    "AzureWebJobsDashboard": "<connection-string>",
    "MyBindingConnection": "<binding-connection-string>",
    "AzureWebJobs.HttpExample.Disabled": "true"
  },
  "Host": {
    "LocalHttpPort": 7071,
    "CORS": "*",
    "CORSCredentials": false
  },
  "ConnectionStrings": {
    "SQLConnectionString": "<sqlclient-connection-string>"
  }
}
```

다음 설정은 프로젝트를 로컬에서 실행할 때 지원됩니다.

| 설정      | 설명                            |
| ------------ | -------------------------------------- |
| **`IsEncrypted`** | 이 설정이 `true`로 설정된 경우 모든 값은 로컬 머신 키로 암호화됩니다. `func settings` 명령과 함께 사용됩니다. 기본값은 `false`여야 합니다. |
| **`Values`** | 프로젝트가 로컬에서 실행될 때 사용되는 연결 문자열 및 애플리케이션 설정의 배열입니다. 이러한 키-값(문자열-문자열) 쌍은 Azure에서 함수 앱의 애플리케이션 설정에 해당합니다(예: [`AzureWebJobsStorage`]). 많은 트리거와 바인딩에는 연결 문자열 앱 설정을 참조하는 속성(예: [Blob Storage 트리거](../articles/azure-functions/functions-bindings-storage-blob-trigger.md#configuration)에 대한 `Connection`)이 있습니다. 이러한 속성의 경우 `Values` 배열에 정의된 애플리케이션 설정이 필요합니다. 일반적으로 사용되는 설정 목록은 다음 표를 참조하세요. <br/>값은 JSON 개체 또는 배열이 아닌 문자열이어야 합니다. 설정 이름에는 콜론(`:`) 또는 이중 밑줄(`__`)을 포함할 수 없습니다. 이중 밑줄 문자는 런타임에 예약되고 콜론은 [종속성 주입](../articles/azure-functions/functions-dotnet-dependency-injection.md#working-with-options-and-settings)을 지원하도록 예약되어 있습니다. |
| **`Host`** | 이 섹션의 설정은 프로젝트를 로컬에서 실행할 때 Functions 호스트 프로세스를 사용자 지정합니다. 이러한 설정은 Azure에서 프로젝트를 실행하는 경우에도 적용되는 host.json 설정과는 별개입니다. |
| **`LocalHttpPort`** | 로컬 Functions 호스트(`func host start` 및 `func run`)를 실행할 때 사용되는 기본 포트를 설정합니다. `--port` 명령줄 옵션이 이 설정보다 우선합니다. |
| **`CORS`** | [CORS(원본 간 리소스 공유)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing)에 허용된 원본을 정의합니다. 원본은 공백 없이 쉼표로 구분된 목록으로 제공됩니다. 와일드카드 값(\*)이 지원되므로 모든 원본에서 요청할 수 있습니다. |
| **`CORSCredentials`** |  `true`로 설정하면 `withCredentials` 요청을 허용합니다. |
| **`ConnectionStrings`** | 컬렉션입니다. 함수 바인딩에서 사용하는 연결 문자열에는 이 컬렉션을 사용하지 마십시오. 이 컬렉션은 일반적으로 구성 파일의 `ConnectionStrings` 섹션에서 연결 문자열을 가져오는 프레임워크에서만 사용됩니다(예: [Entity Framework](https://msdn.microsoft.com/library/aa937723(v=vs.113).aspx)). 이 개체의 연결 문자열은 공급자 유형이 [System.Data.SqlClient](https://msdn.microsoft.com/library/system.data.sqlclient(v=vs.110).aspx)인 환경에 추가됩니다. 이 컬렉션의 항목은 다른 앱 설정을 사용하여 Azure에 게시되지 않습니다. 이러한 값을 함수 앱 설정의 `Connection strings` 컬렉션에 명시적으로 추가해야 합니다. 함수 코드에서 [`SqlConnection`](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection(v=vs.110).aspx)을 만드는 경우, 포털의 **애플리케이션 설정**에 다른 연결과 함께 연결 문자열 값을 저장해야 합니다. |

다음 애플리케이션 설정은 로컬에서 실행할 때 **`Values`** 배열에 포함될 수 있습니다.

| 설정 | 값 | Description |
|-----|-----|-----|
|**`AzureWebJobsStorage`**| 스토리지 계정 연결 문자열 또는<br/>`UseDevelopmentStorage=true`| Azure Storage 계정에 대한 연결 문자열을 포함합니다. HTTP 이외의 트리거를 사용하는 경우 필요합니다. 자세한 내용은 [`AzureWebJobsStorage`] 참조를 참조하세요.<br/>[Azure Storage 에뮬레이터](../articles/storage/common/storage-use-emulator.md)를 로컬에서 설치한 경우 [`AzureWebJobsStorage`]를 `UseDevelopmentStorage=true`로 설정하면 핵심 도구가 에뮬레이터를 사용합니다. 에뮬레이터는 개발 중에 유용하지만 배포 전에 실제 스토리지 연결로 테스트해야 합니다.| 
|**`AzureWebJobs.<FUNCTION_NAME>.Disabled`**| `true`\|`false` | 로컬에서 실행할 때 함수를 비활성화하려면 컬렉션에 `"AzureWebJobs.<FUNCTION_NAME>.Disabled": "true"`를 추가합니다. `<FUNCTION_NAME>`은 함수의 이름입니다. 자세한 내용은 [Azure Functions에서 함수를 사용하지 않도록 설정하는 방법](../articles/azure-functions/disable-function.md#localsettingsjson)을 참조하세요. |
|**`FUNCTIONS_WORKER_RUNTIME`** | `dotnet`<br/>`node`<br/>`java`<br/>`powershell`<br/>`python`| Functions 런타임의 대상 언어를 나타냅니다. 버전 2.x 이상의 Functions 런타임에 필요합니다. 이 설정은 핵심 도구에 의해 프로젝트에 대해 생성됩니다. 자세한 내용은 [`FUNCTIONS_WORKER_RUNTIME`](../articles/azure-functions/functions-app-settings.md#functions_worker_runtime) 참조를 참조하세요.|
| **`FUNCTIONS_WORKER_RUNTIME_VERSION`** | `~7` |로컬로 실행할 때 PowerShell 7이 사용됨을 나타냅니다. 설정되지 않은 경우 PowerShell Core 6이 사용됩니다. 이 설정은 로컬로 실행하는 경우에만 사용됩니다. Azure에서 실행하는 경우 PowerShell 런타임 버전은 [포털에서 설정](../articles/azure-functions/functions-reference-powershell.md#changing-the-powershell-version)할 수 있는 `powerShellVersion` 사이트 구성 설정에 따라 결정됩니다. | 

[`AzureWebJobsStorage`]: ../articles/azure-functions/functions-app-settings.md#azurewebjobsstorage
