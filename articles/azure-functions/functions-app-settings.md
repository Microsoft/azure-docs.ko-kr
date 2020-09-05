---
title: Azure Functions에 대한 앱 설정 참조
description: Azure Functions 앱 설정 또는 환경 변수에 대한 참조 설명서입니다.
ms.topic: conceptual
ms.date: 09/22/2018
ms.openlocfilehash: b17db828aeb19c3347c0db4babf0eee2b9d5f280
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88589303"
---
# <a name="app-settings-reference-for-azure-functions"></a>Azure Functions에 대한 앱 설정 참조

함수 앱의 앱 설정에는 해당 함수 앱의 모든 함수에 영향을 주는 전역 구성 옵션이 포함됩니다. 로컬에서 실행할 때 이러한 설정은 로컬 [환경 변수](functions-run-local.md#local-settings-file)로 액세스합니다. 이 문서에는 함수 앱에서 사용할 수 있는 앱 설정이 나열되어 있습니다.

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

[host.json](functions-host-json.md) 파일과 [local.settings.json](functions-run-local.md#local-settings-file) 파일에는 다른 전역 구성 옵션이 있습니다.

> [!NOTE]  
> 응용 프로그램 설정을 사용 하 여 파일 자체에서 host.js를 변경할 필요 없이 값 설정에 대 한 host.js를 재정의할 수 있습니다. 이 기능은 특정 환경에 대 한 설정에서 특정 host.js를 구성 하거나 수정 해야 하는 경우에 유용 합니다. 이렇게 하면 프로젝트를 다시 게시 하지 않고도 설정 host.js을 변경할 수 있습니다. 자세한 내용은 [ 참조에서host.js문서](functions-host-json.md#override-hostjson-values)를 참조 하세요.  

## <a name="appinsights_instrumentationkey"></a>APPINSIGHTS_INSTRUMENTATIONKEY

Application Insights에 대 한 계측 키입니다. 또는 중 하나만 사용 `APPINSIGHTS_INSTRUMENTATIONKEY` `APPLICATIONINSIGHTS_CONNECTION_STRING` 합니다. 자세한 내용은 [Azure Functions 모니터링](functions-monitoring.md)을 참조하세요. 

|키|샘플 값|
|---|------------|
|APPINSIGHTS_INSTRUMENTATIONKEY|55555555-af77-484b-9032-64f83b83bb|

## <a name="applicationinsights_connection_string"></a>APPLICATIONINSIGHTS_CONNECTION_STRING

Application Insights에 대 한 연결 문자열입니다. `APPLICATIONINSIGHTS_CONNECTION_STRING` `APPINSIGHTS_INSTRUMENTATIONKEY` 함수 앱에 연결 문자열을 사용 하 여 지원 되는 추가 된 사용자 지정이 필요한 경우 대신를 사용 합니다. 자세한 내용은 [연결 문자열](../azure-monitor/app/sdk-connection-string.md)을 참조 하세요. 

|키|샘플 값|
|---|------------|
|APPLICATIONINSIGHTS_CONNECTION_STRING|InstrumentationKey = [key]; IngestionEndpoint = [url]; LiveEndpoint = [url]; ProfilerEndpoint = [url]; SnapshotEndpoint = [url];|

## <a name="azure_function_proxy_disable_local_call"></a>AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL

기본적으로 [함수 프록시](functions-proxies.md) 는 바로 가기를 사용 하 여 프록시의 API 호출을 동일한 함수 앱의 함수로 직접 보냅니다. 이 바로 가기는 새 HTTP 요청을 만드는 대신 사용 됩니다. 이 설정을 사용 하면 바로 가기 동작을 사용 하지 않도록 설정할 수 있습니다.

|키|값|Description|
|-|-|-|
|AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL|true|지역 함수 앱의 함수를 가리키는 백 엔드 URL을 사용 하는 호출은 함수로 직접 전송 되지 않습니다. 대신 요청은 함수 앱에 대 한 HTTP 프런트 엔드로 다시 전달 됩니다.|
|AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL|false|지역 함수 앱의 함수를 가리키는 백 엔드 URL을 사용 하는 호출은 함수로 직접 전달 됩니다. 이것은 기본값입니다. |

## <a name="azure_function_proxy_backend_url_decode_slashes"></a>AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES

이 설정은 문자를 `%2F` 백 엔드 URL에 삽입할 때 경로 매개 변수에서 슬래시로 디코딩되는 지 여부를 제어 합니다. 

|키|값|Description|
|-|-|-|
|AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES|true|인코딩된 슬래시가 있는 경로 매개 변수는 디코딩됩니다. |
|AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES|false|모든 경로 매개 변수는 기본 동작인 변경 되지 않은 상태로 전달 됩니다. |

예를 들어 도메인의 함수 앱에 대 한 proxies.js파일을 살펴보겠습니다 `myfunction.com` .

```JSON
{
    "$schema": "http://json.schemastore.org/proxies",
    "proxies": {
        "root": {
            "matchCondition": {
                "route": "/{*all}"
            },
            "backendUri": "example.com/{all}"
        }
    }
}
```

`AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES`가로 설정 되 면 `true` URL은 `example.com/api%2ftest` 로 확인 `example.com/api/test` 됩니다. 기본적으로 URL은 변경 되지 않고 그대로 유지 됩니다 `example.com/test%2fapi` . 자세한 내용은 [함수 프록시](functions-proxies.md)를 참조 하세요.

## <a name="azure_functions_environment"></a>AZURE_FUNCTIONS_ENVIRONMENT

버전 2.x 이상 버전의 함수 런타임에서는 런타임 환경에 따라 앱 동작을 구성 합니다. [초기화 하는 동안](https://github.com/Azure/azure-functions-host/blob/dev/src/WebJobs.Script.WebHost/Program.cs#L43)이 값을 읽습니다. `AZURE_FUNCTIONS_ENVIRONMENT`는 임의의 값으로 설정할 수 있지만 [개발](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.development), [준비](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.staging)및 [프로덕션](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.production)의 [세 가지 값](/dotnet/api/microsoft.aspnetcore.hosting.environmentname) 이 지원 됩니다. 가 `AZURE_FUNCTIONS_ENVIRONMENT` 설정 되지 않은 경우 로컬 환경 및 Azure에서 기본적으로로 설정 `Development` `Production` 됩니다. 런타임 환경을 설정 하는 대신이 설정을 사용 해야 합니다 `ASPNETCORE_ENVIRONMENT` . 

## <a name="azurefunctionsjobhost__"></a>AzureFunctionsJobHost__\*

버전 2.x 이상 버전의 함수 런타임에서는 응용 프로그램 설정이 현재 환경의 설정 [ 에host.js](functions-host-json.md) 를 재정의할 수 있습니다. 이러한 재정의는 이라는 응용 프로그램 설정으로 표현 됩니다 `AzureFunctionsJobHost__path__to__setting` . 자세한 내용은 [값에 대 한 host.js재정의](functions-host-json.md#override-hostjson-values)를 참조 하세요.

## <a name="azurewebjobsdashboard"></a>AzureWebJobsDashboard

로그를 저장하고 포털의 **모니터** 탭에 표시하기 위한 선택적인 스토리지 계정 연결 문자열입니다. 이 설정은 Azure Functions 런타임의 버전 1.x를 대상으로 하는 앱에만 유효 합니다. 스토리지 계정은 Blob, 큐 및 테이블을 지원하는 범용 계정이어야 합니다. 자세한 내용은 [스토리지 계정 요구 사항](storage-considerations.md#storage-account-requirements)을 참조하세요.

|키|샘플 값|
|---|------------|
|AzureWebJobsDashboard|DefaultEndpointsProtocol=https;AccountName=<name>;AccountKey=<key>|

> [!NOTE]
> 성능 및 환경을 향상 시키기 위해 런타임 버전 2.x 이상 버전에서는 대신 모니터링을 위해 APPINSIGHTS_INSTRUMENTATIONKEY 및 App Insights를 사용 `AzureWebJobsDashboard` 합니다.

## <a name="azurewebjobsdisablehomepage"></a>AzureWebJobsDisableHomepage

`true`는 함수 앱의 루트 URL에 표시되는 기본 방문 페이지를 사용하지 않도록 설정한다는 의미입니다. 기본값은 `false`입니다.

|키|샘플 값|
|---|------------|
|AzureWebJobsDisableHomepage|true|

이 앱 설정이 생략되거나 `false`로 설정되면 URL `<functionappname>.azurewebsites.net`에 대한 응답으로 다음 예와 유사한 페이지가 표시됩니다.

![함수 앱 방문 페이지](media/functions-app-settings/function-app-landing-page.png)

## <a name="azurewebjobsdotnetreleasecompilation"></a>AzureWebJobsDotNetReleaseCompilation

`true`는 .NET 코드를 컴파일할 때 릴리스 모드 사용을 의미합니다. `false`는 디버그 모드 사용을 의미합니다. 기본값은 `true`입니다.

|키|샘플 값|
|---|------------|
|AzureWebJobsDotNetReleaseCompilation|true|

## <a name="azurewebjobsfeatureflags"></a>AzureWebJobsFeatureFlags

사용하도록 설정할 쉼표로 구분된 베타 기능 목록입니다. 이 플래그로 사용할 수 있는 베타 기능은 프로덕션 준비가 되지 않았지만 실제로 사용하기 전에 실험적 용도로 사용할 수 있습니다.

|키|샘플 값|
|---|------------|
|AzureWebJobsFeatureFlags|feature1,feature2|

## <a name="azurewebjobssecretstoragetype"></a>AzureWebJobsSecretStorageType

키 스토리지에 사용할 리포지토리 또는 공급자를 지정합니다. 현재 지원되는 리포지토리는 Blob Storage(&quot;Blob&quot;) 및 로컬 파일 시스템(&quot;Files&quot;)입니다. 기본값은 버전 2에서는 Blob, 버전 1에서는 파일 시스템입니다.

|키|샘플 값|
|---|------------|
|AzureWebJobsSecretStorageType|Files|

## <a name="azurewebjobsstorage"></a>AzureWebJobsStorage

Azure Functions 런타임은 HTTP 트리거 함수를 제외한 모든 함수에 대해 이 스토리지 계정 연결 문자열을 사용합니다. 스토리지 계정은 Blob, 큐 및 테이블을 지원하는 범용 계정이어야 합니다. [스토리지 계정](functions-infrastructure-as-code.md#storage-account) 및 [스토리지 계정 요구 사항](storage-considerations.md#storage-account-requirements)을 참조하세요.

|키|샘플 값|
|---|------------|
|AzureWebJobsStorage|DefaultEndpointsProtocol=https;AccountName=[name];AccountKey=[key]|

## <a name="azurewebjobs_typescriptpath"></a>AzureWebJobs_TypeScriptPath

TypeScript에 사용되는 컴파일러의 경로입니다. 필요한 경우 기본값을 재정의할 수 있습니다.

|키|샘플 값|
|---|------------|
|AzureWebJobs_TypeScriptPath|%HOME%\typescript|

## <a name="function_app_edit_mode"></a>FUNCTION\_APP\_EDIT\_MODE

Azure Portal에서 편집할 수 있는지 여부를 지정 합니다. 유효한 값은 "readwrite"및 "readonly"입니다.

|키|샘플 값|
|---|------------|
|FUNCTION\_APP\_EDIT\_MODE|readonly|

## <a name="functions_extension_version"></a>FUNCTIONS\_EXTENSION\_VERSION

이 함수 앱에 사용할 Functions 런타임의 버전입니다. 주 버전의 물결표는 해당 주 버전의 최신 버전(예: "~2")을 사용한다는 의미입니다. 동일한 주 버전의 새 버전을 사용할 수 있으면 함수 앱에 자동으로 설치됩니다. 앱을 특정 버전으로 고정하려면 전체 버전 번호(예: "2.0.12345")를 사용합니다. 기본값은 "~2"입니다. 값이 `~1`이면 앱을 런타임의 버전 1.x로 고정합니다.

|키|샘플 값|
|---|------------|
|FUNCTIONS\_EXTENSION\_VERSION|~2|

## <a name="functions_v2_compatibility_mode"></a>함수 \_ V2 \_ 호환성 \_ 모드

이 설정을 사용 하 여 함수 앱을 버전 2.x 런타임의 버전 2.x 호환 모드에서 실행할 수 있습니다. [함수 앱을 버전 2.x에서 2.x로 업그레이드 하는](functions-versions.md#migrating-from-2x-to-3x)경우 문제가 발생 하는 경우에만이 설정을 사용 합니다. 

>[!IMPORTANT]
> 이 설정은 응용 프로그램이 버전 3.x에서 올바르게 실행 되도록 업데이트 하는 동안 단기 해결 방법 으로만 사용 됩니다. 이 설정은 2.x [런타임이 지원](functions-versions.md)되는 경우에만 지원 됩니다. 이 설정을 사용 하지 않고 앱이 버전 3(sp3)에서 실행 되지 않도록 하는 문제가 발생 하는 경우 [문제를 보고](https://github.com/Azure/azure-functions-host/issues/new?template=Bug_report.md)하세요.

에서는 [함수 \_ 확장 \_ 버전이](functions-app-settings.md#functions_extension_version) 로 설정 되어야 `~3` 합니다.

|키|샘플 값|
|---|------------|
|함수 \_ V2 \_ 호환성 \_ 모드|true|

## <a name="functions_worker_process_count"></a>함수 \_ 작업자 \_ 프로세스 \_ 수

기본값을 사용 하 여 언어 작업자 프로세스의 최대 수를 지정 합니다 `1` . 허용 되는 최대 값은 `10` 입니다. 함수 호출은 언어 작업자 프로세스 간에 균등 하 게 분산 됩니다. 언어 작업자 프로세스는 함수 작업자 프로세스 수로 설정 된 개수 \_ \_ 에 도달할 때까지 10 초 마다 생성 됩니다 \_ . 여러 언어 작업자 프로세스를 사용 하는 것은 [크기 조정과](functions-scale.md)동일 하지 않습니다. 작업에 CPU 바인딩과 i/o 바인딩된 호출이 혼합 되어 있는 경우이 설정을 사용 하는 것이 좋습니다. 이 설정은 모든 non-.NET 언어에 적용 됩니다.

|키|샘플 값|
|---|------------|
|함수 \_ 작업자 \_ 프로세스 \_ 수|2|


## <a name="functions_worker_runtime"></a>FUNCTIONS\_WORKER\_RUNTIME

함수 앱에 로드할 언어 작업자 런타임입니다.  이것은 애플리케이션(예: "dotnet")에 사용되는 언어에 해당합니다. 여러 언어로 된 함수는 여러 개의 앱을 각각 해당하는 작업자 런타임 값을 사용하여 게시해야 합니다.  유효한 값은 `dotnet` (c #/f #), `node` (JavaScript/TypeScript), `java` (Java), `powershell` (PowerShell) 및 `python` (Python)입니다.

|키|샘플 값|
|---|------------|
|FUNCTIONS\_WORKER\_RUNTIME|dotnet|

## <a name="pip_extra_index_url"></a>PIP \_ 추가 \_ 인덱스 \_ URL

이 설정의 값은 Python 앱에 대 한 사용자 지정 패키지 인덱스 URL을 나타냅니다. 추가 패키지 인덱스에 있는 사용자 지정 종속성을 사용 하 여 원격 빌드를 실행 해야 하는 경우이 설정을 사용 합니다.   

|키|샘플 값|
|---|------------|
|PIP \_ 추가 \_ 인덱스 \_ URL|http://my.custom.package.repo/simple |

자세한 내용은 Python 개발자 참조의 [사용자 지정 종속성](functions-reference-python.md#remote-build-with-extra-index-url) 을 참조 하세요.

## <a name="scale_controller_logging_enable"></a>크기 조정 \_ 컨트롤러 \_ 로깅 \_ 사용

_이 설정은 현재 미리 보기로 있습니다._  

이 설정은 Azure Functions 크기 조정 컨트롤러의 로깅을 제어 합니다. 자세한 내용은 [컨트롤러 로그 크기 조정](functions-monitoring.md#scale-controller-logs-preview)을 참조 하세요.

|키|샘플 값|
|-|-|
|SCALE_CONTROLLER_LOGGING_ENABLE|AppInsights: 자세한 정보 표시|

이 키의 값은 다음과 같이 정의 된 형식으로 제공 됩니다 `<DESTINATION>:<VERBOSITY>` .

[!INCLUDE [functions-scale-controller-logging](../../includes/functions-scale-controller-logging.md)]

## <a name="website_contentazurefileconnectionstring"></a>웹 사이트 \_ CONTENTAZUREFILECONNECTIONSTRING

소비 & 프리미엄 요금제에만 해당 합니다. 함수 앱 코드 및 구성이 저장된 스토리지 계정의 연결 문자열입니다. [함수 앱 만들기](functions-infrastructure-as-code.md#create-a-function-app)를 참조하세요.

|키|샘플 값|
|---|------------|
|WEBSITE_CONTENTAZUREFILECONNECTIONSTRING|DefaultEndpointsProtocol=https;AccountName=[name];AccountKey=[key]|

## <a name="website_contentshare"></a>WEBSITE\_CONTENTSHARE

소비 & 프리미엄 요금제에만 해당 합니다. 함수 앱 코드 및 구성에 대한 파일 경로입니다. Used with WEBSITE_CONTENTAZUREFILECONNECTIONSTRING. 기본값은 함수 앱 이름으로 시작하는 고유한 문자열입니다. [함수 앱 만들기](functions-infrastructure-as-code.md#create-a-function-app)를 참조하세요.

|키|샘플 값|
|---|------------|
|WEBSITE_CONTENTSHARE|functionapp091999e2|

## <a name="website_max_dynamic_application_scale_out"></a>WEBSITE\_MAX\_DYNAMIC\_APPLICATION\_SCALE\_OUT

함수 앱이 확장할 수 있는 최대 인스턴스 수입니다. 기본값은 무제한입니다.

> [!IMPORTANT]
> 이 설정은 미리 보기로 제공 됩니다.  [Function max scale out에 대 한 응용 프로그램 속성이](./functions-scale.md#limit-scale-out) 추가 되었으며 확장을 제한 하는 데 권장 되는 방법입니다.

|키|샘플 값|
|---|------------|
|WEBSITE\_MAX\_DYNAMIC\_APPLICATION\_SCALE\_OUT|5|

## <a name="website_node_default_version"></a>WEBSITE\_NODE\_DEFAULT_VERSION

_Windows에만 해당 합니다._  
Windows에서 함수 앱을 실행할 때 사용할 Node.js 버전을 설정 합니다. 물결표 (~)를 사용 하 여 런타임에서 대상 주 버전의 사용 가능한 최신 버전을 사용 하도록 해야 합니다. 예를 들어로 설정 하면 `~10` 최신 버전의 Node.js 10이 사용 됩니다. 주 버전의 대상이 물결표 인 경우 부 버전을 수동으로 업데이트 하지 않아도 됩니다. 

|키|샘플 값|
|---|------------|
|WEBSITE\_NODE\_DEFAULT_VERSION|~ 10|

## <a name="website_run_from_package"></a>WEBSITE\_RUN\_FROM\_PACKAGE

탑재된 패키지 파일에서 함수 앱을 실행하도록 설정합니다.

|키|샘플 값|
|---|------------|
|WEBSITE\_RUN\_FROM\_PACKAGE|1|

유효한 값은 배포 패키지 파일의 위치를 확인하는 URL이거나 `1`입니다. `1`로 설정하면 패키지는 `d:\home\data\SitePackages` 폴더에 있어야 합니다. 이 설정으로 zip 배포를 사용하면 패키지가 자동으로 이 위치에 업로드됩니다. 미리 보기에서 이 설정은 `WEBSITE_RUN_FROM_ZIP`으로 명명되었습니다. 자세한 내용은 [패키지 파일에서 Functions 실행](run-functions-from-deployment-package.md)을 참조하세요.

## <a name="website_time_zone"></a>웹 사이트 \_ 표준 \_ 시간대

함수 앱에 대 한 표준 시간대를 설정할 수 있습니다. 

|키|OS|샘플 값|
|---|--|------------|
|웹 사이트 \_ 표준 \_ 시간대|Windows|동부 표준시|
|웹 사이트 \_ 표준 \_ 시간대|Linux|아메리카/New_York|

[!INCLUDE [functions-timezone](../../includes/functions-timezone.md)]

## <a name="next-steps"></a>다음 단계

[앱 설정 업데이트 방법 알아보기](functions-how-to-use-azure-function-app-settings.md#settings)

[host.json 파일의 전역 설정 보기](functions-host-json.md)

[App Service 앱에 대한 다른 앱 설정 보기](https://github.com/projectkudu/kudu/wiki/Configurable-settings)
