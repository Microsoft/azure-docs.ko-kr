---
title: Azure Functions 런타임 버전 개요
description: Azure Functions는 여러 버전의 런타임을 지원합니다. 버전 간 차이점과 적합한 버전을 선택하는 방법을 알아봅니다.
ms.topic: conceptual
ms.custom: devx-track-dotnet
ms.date: 12/09/2019
ms.openlocfilehash: 6641461e63d7c9452351f3b0b99a274d2714a92e
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88208114"
---
# <a name="azure-functions-runtime-versions-overview"></a>Azure Functions 런타임 버전 개요

현재 Azure Functions는 세 가지 버전의 런타임 호스트 (1.x, 2.x 및 3.x)를 지원 합니다. 세 버전 모두 프로덕션 시나리오에서 지원 됩니다.  

> [!IMPORTANT]
> 버전 1.x는 유지 관리 모드 이며 Azure Portal 또는 Windows 컴퓨터에서 로컬로 개발을 지원 합니다. 향상 된 기능은 이후 버전 에서만 제공 됩니다. 

이 문서에서는 다양 한 버전 간의 차이점, 각 버전을 만드는 방법 및 버전을 변경 하는 방법에 대해 자세히 설명 합니다.

## <a name="languages"></a>언어

버전 2.x부터 런타임은 언어 확장성 모델을 사용 하 고 함수 앱의 모든 함수는 동일한 언어를 공유 해야 합니다. 함수 앱의 함수 언어는 앱을 만들 때 선택 되며, 함수 [ \_ 작업자 \_ 런타임](functions-app-settings.md#functions_worker_runtime) 설정에서 유지 관리 됩니다. 

다음 표는 각 런타임 버전에서 현재 지원되는 프로그래밍 언어를 나타냅니다.

[!INCLUDE [functions-supported-languages](../../includes/functions-supported-languages.md)]

자세한 내용은 [지원되는 언어](supported-languages.md)를 참조하세요.

## <a name="run-on-a-specific-version"></a><a name="creating-1x-apps"></a>특정 버전에서 실행

기본적으로 Azure Portal 및 Azure CLI에서 만든 함수 앱은 버전 3.x로 설정 됩니다. 이 버전은 필요에 따라 수정할 수 있습니다. 함수 앱을 만든 후 함수를 추가 하기 전에는 런타임 버전을 1.x로만 변경할 수 있습니다.  기능이 있는 응용 프로그램 에서도 2.x와 2.x 간 이동이 허용 되지만 먼저 새 앱에서 테스트 하는 것이 좋습니다.

## <a name="migrating-from-1x-to-later-versions"></a>1.x에서 이후 버전으로 마이그레이션

버전 1.x 런타임을 사용 하도록 작성 된 기존 앱을 마이그레이션하려면 최신 버전을 대신 사용 하도록 선택할 수 있습니다. 변경 해야 하는 대부분의 변경 사항은 .NET Framework 4.7 및 .NET Core 간의 c # API 변경과 같은 언어 런타임의 변경 내용과 관련이 있습니다. 또한 선택한 언어 런타임과 코드 및 라이브러리가 호환되는지 확인해야 합니다. 마지막으로 아래에 강조 표시된 트리거, 바인딩 및 기능의 변경 내용을 기록해 두어야 합니다. 최상의 마이그레이션 결과를 위해 새 버전으로 새 함수 앱을 만들고 기존 버전 1.x 함수 코드를 새 앱으로 이식 해야 합니다.  

앱 구성을 수동으로 업데이트 하 여 "내부" 업그레이드를 수행할 수 있지만, 1.x에서 상위 버전으로 이동 하는 데는 몇 가지 주요 변경 사항이 포함 되어 있습니다. 예를 들어 c #에서 디버깅 개체는에서로 변경 `TraceWriter` 됩니다 `ILogger` . 새 버전 2.x 프로젝트를 만들어 최신 버전의 3(sp3) 템플릿을 기반으로 업데이트 된 함수로 시작 합니다.

### <a name="changes-in-triggers-and-bindings-after-version-1x"></a>버전 1.x 이후의 트리거와 바인딩 변경

2.x 버전부터 앱의 함수에 사용 되는 특정 트리거 및 바인딩에 대 한 확장을 설치 해야 합니다. 유일한 예외는 확장이 필요 없는 이 HTTP 및 타이머 트리거입니다.  자세한 내용은 [바인딩 확장 등록 및 설치](./functions-bindings-register.md)를 참조하세요.

또한 버전 간에 함수의function.js또는 특성에 * 대* 한 몇 가지 변경 내용이 있습니다. 예를 들어, 이벤트 허브 `path` 속성은 이제 `eventHubName`입니다. 각 바인딩의 설명서에 대한 링크는 [기존 바인딩 테이블](#bindings)을 참조하세요.

### <a name="changes-in-features-and-functionality-after-version-1x"></a>버전 1.x 이후의 기능 변경 내용

버전 1.x 이후에 제거, 업데이트 또는 교체 된 몇 가지 기능이 있습니다. 이 섹션에서는 버전 1.x를 사용한 후 이후 버전에 표시 되는 변경 내용에 대해 자세히 설명 합니다.

버전 2.x에서 다음과 같은 사항이 변경되었습니다.

* HTTP 엔드포인트를 호출하기 위한 키는 항상 Azure Blob Storage에 암호화된 상태로 저장됩니다. 버전 1.x에서는 키가 기본적으로 Azure File storage에 저장 되었습니다. 앱을 버전 1.x에서 버전 2.x로 업그레이드할 때 파일 스토리지에 있는 기존 암호가 다시 설정됩니다.

* 버전 2.x 런타임에서는 웹후크 공급자가 기본적으로 지원되지 않습니다. 성능 향상을 위해 이렇게 변경되었습니다. HTTP 트리거를 여전히 웹후크에 대한 엔드포인트로 사용할 수 있습니다.

* 호스트 구성 파일(host.json)은 비워 두거나 문자열 `"version": "2.0"`을 포함해야 합니다.

* 모니터링을 개선 하기 위해이 설정을 사용 하는 포털의 WebJobs 대시보드는 [`AzureWebJobsDashboard`](functions-app-settings.md#azurewebjobsdashboard) 설정을 사용 하는 Azure 애플리케이션 Insights로 바뀝니다 [`APPINSIGHTS_INSTRUMENTATIONKEY`](functions-app-settings.md#appinsights_instrumentationkey) . 자세한 내용은 [Azure Functions 모니터링](functions-monitoring.md)을 참조하세요.

* 함수 앱의 모든 함수가 동일한 언어를 공유해야 합니다. 함수 앱을 만들 때 앱의 런타임 스택을 선택해야 합니다. 런타임 스택은 [`FUNCTIONS_WORKER_RUNTIME`](functions-app-settings.md#functions_worker_runtime) 응용 프로그램 설정의 값으로 지정 됩니다. 이 요구 사항은 공간 효율성 및 시작 시간을 개선하기 위해 추가되었습니다. 로컬로 개발하는 경우 [local.settings.json 파일](functions-run-local.md#local-settings-file)에 이 설정을 포함해야 합니다.

* App Service 계획의 함수에 대한 기본 시간 제한은 30분으로 변경되었습니다. host.json에서 [functionTimeout](functions-host-json.md#functiontimeout) 설정을 사용하여 수동으로 제한 시간을 다시 무제한으로 변경할 수 있습니다.

* HTTP concurrency 제한는 기본적으로 소비 계획 함수에 대해 구현 되며 인스턴스당 기본값 100을 사용 합니다. [`maxConcurrentRequests`](functions-host-json.md#http)파일의 host.js에 있는 설정에서이를 변경할 수 있습니다.

* [.Net Core 제한 사항](https://github.com/Azure/azure-functions-host/issues/3414)때문에 F # 스크립트 (. .fsx) 함수에 대 한 지원이 제거 되었습니다. 컴파일된 F# 함수(.fs)는 계속 지원됩니다.

* Event Grid 트리거 웹후크의 URL 형식은 `https://{app}/runtime/webhooks/{triggerName}`으로 변경되었습니다.

## <a name="migrating-from-2x-to-3x"></a>2.x에서 2.x로 마이그레이션

Azure Functions 버전 3(sp3)은 이전 버전과 호환 됩니다. x. x.  많은 앱은 코드를 변경 하지 않고 2.x로 안전 하 게 업그레이드할 수 있어야 합니다.  3. x로 이동 하는 것이 좋습니다. 프로덕션 앱에서 주 버전을 변경 하기 전에 광범위 한 테스트를 실행 해야 합니다.

### <a name="breaking-changes-between-2x-and-3x"></a>2.x와 3(sp3)의 주요 변경 내용

2.x 앱을 3. x로 업그레이드 하기 전에 알아두어야 할 변경 사항은 다음과 같습니다.

#### <a name="javascript"></a>JavaScript

* 또는 반환 값을 통해 할당 된 출력 바인딩은 `context.done` 이제의 설정과 동일 하 게 동작 합니다 `context.bindings` .

* 타이머 트리거 개체는 camelCase입니다.

* 이벤트 허브가 이진으로 트리거된 함수 `dataType` 는 대신의 배열을 받습니다 `binary` `string` .

* HTTP 요청 페이로드에는를 통해 더 이상 액세스할 수 없습니다 `context.bindingData.req` .  여전히에서 입력 매개 변수, 및로 액세스할 수 있습니다 `context.req` `context.bindings` .

* Node.js 8은 더 이상 지원 되지 않으며, 3. x 함수에서 실행 되지 않습니다.

#### <a name="net"></a>.NET

* [동기 서버 작업은 기본적으로 사용 하지 않도록 설정 됩니다](/dotnet/core/compatibility/2.2-3.0#http-synchronous-io-disabled-in-all-servers).

### <a name="changing-version-of-apps-in-azure"></a>Azure에서 앱 버전 변경

Azure에서 게시 된 앱이 사용 하는 함수 런타임의 버전은 응용 프로그램 설정에 따라 결정 됩니다 [`FUNCTIONS_EXTENSION_VERSION`](functions-app-settings.md#functions_extension_version) . 지원 되는 주요 런타임 버전 값은 다음과 같습니다.

| 값 | 런타임 대상 |
| ------ | -------- |
| `~3` | 3.x |
| `~2` | 2.x |
| `~1` | 1.x |

>[!IMPORTANT]
> 다른 앱 설정을 변경 하 고 함수 코드를 변경 해야 할 수 있으므로이 설정을 임의로 변경 하지 마십시오.

### <a name="locally-developed-application-versions"></a>로컬에서 개발한 응용 프로그램 버전

대상 버전을 로컬에서 변경 하도록 함수 앱을 다음과 같이 업데이트할 수 있습니다.

#### <a name="visual-studio-runtime-versions"></a>Visual Studio 런타임 버전

Visual Studio에서 프로젝트를 만들 때 런타임 버전을 선택합니다. Visual Studio 용 Azure Functions 도구는 세 가지 주요 런타임 버전을 지원 합니다. 디버깅 및 게시를 수행할 때 프로젝트 설정에 따라 올바른 버전이 사용됩니다. 버전 설정은 `.csproj` 파일의 다음 속성에 정의됩니다.

##### <a name="version-1x"></a>버전 1.x

```xml
<TargetFramework>net461</TargetFramework>
<AzureFunctionsVersion>v1</AzureFunctionsVersion>
```

##### <a name="version-2x"></a>버전 2.x

```xml
<TargetFramework>netcoreapp2.1</TargetFramework>
<AzureFunctionsVersion>v2</AzureFunctionsVersion>
```

##### <a name="version-3x"></a>버전 3.x

```xml
<TargetFramework>netcoreapp3.1</TargetFramework>
<AzureFunctionsVersion>v3</AzureFunctionsVersion>
```

> [!NOTE]
> Azure Functions 3.x 및 .NET을 사용 하려면 `Microsoft.NET.Sdk.Functions` 확장이 이상 이어야 합니다 `3.0.0` .

###### <a name="updating-2x-apps-to-3x-in-visual-studio"></a>Visual Studio에서 2.x 앱을 3. x로 업데이트

2.x를 대상으로 하는 기존 함수를 열고, 파일을 편집 `.csproj` 하 고 위의 값을 업데이트 하 여 2.x로 이동할 수 있습니다.  Visual Studio는 프로젝트 메타 데이터를 기반으로 런타임 버전을 자동으로 관리 합니다.  그러나이 경우에는 Visual Studio가 컴퓨터에 3gb 용 템플릿과 런타임을 아직 보유 하지 않은 경우에만 3. x 앱을 만들 수 있습니다.  "프로젝트에 지정 된 버전과 일치 하는 사용할 수 있는 함수 런타임이 없습니다."와 같은 오류가 표시 될 수 있습니다.  최신 템플릿 및 런타임을 인출 하려면 새 함수 프로젝트를 만드는 환경을 살펴보겠습니다.  버전 및 템플릿 선택 화면에서 Visual Studio가 최신 템플릿 가져오기를 완료할 때까지 기다립니다.  최신 .NET Core 3 템플릿을 사용할 수 있고 표시 되 면 버전 3.x에 대해 구성 된 모든 프로젝트를 실행 하 고 디버그할 수 있어야 합니다.

> [!IMPORTANT]
> 버전 3.x 함수는 visual studio 버전 16.4 이상을 사용 하는 경우에만 Visual Studio에서 개발할 수 있습니다.

#### <a name="vs-code-and-azure-functions-core-tools"></a>VS Code 및 Azure Functions 핵심 도구

[Azure Functions Core Tools](functions-run-local.md)는 명령줄 개발에 사용되며 Visual Studio Code용 [Azure Functions 확장](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)에서도 사용됩니다. 버전 3.x에 대해 개발 하려면 핵심 도구의 버전 3(sp3)을 설치 합니다. 버전 2.x를 개발 하려면 2. x 버전의 핵심 도구가 필요 합니다. 자세한 내용은 [Azure Functions Core Tools 설치](functions-run-local.md#install-the-azure-functions-core-tools)를 참조하세요.

Visual Studio Code 개발의 경우 설치된 도구의 버전과 일치하도록 `azureFunctions.projectRuntime`에 대한 사용자 설정을 업데이트해야 할 수도 있습니다.  이 설정은 함수 앱을 만드는 동안 사용되는 템플릿 및 언어도 업데이트합니다.  에서 앱을 만들려면 `~3` `azureFunctions.projectRuntime` 사용자 설정을로 업데이트 `~3` 합니다.

![Azure Functions 확장 런타임 설정](./media/functions-versions/vs-code-version-runtime.png)

#### <a name="maven-and-java-apps"></a>Maven 및 Java 앱

로컬로 실행 하는 데 필요한 [핵심 도구의 3. x 버전을 설치](functions-run-local.md#install-the-azure-functions-core-tools) 하 여 Java 앱을 버전 2.x에서 2.x로 마이그레이션할 수 있습니다.  앱이 버전 3.x에서 로컬로 실행 되 고 있는지 확인 한 후 `POM.xml` `FUNCTIONS_EXTENSION_VERSION` `~3` 다음 예제와 같이 앱의 파일을 업데이트 하 여 설정을로 수정 합니다.

```xml
<configuration>
    <resourceGroup>${functionResourceGroup}</resourceGroup>
    <appName>${functionAppName}</appName>
    <region>${functionAppRegion}</region>
    <appSettings>
        <property>
            <name>WEBSITE_RUN_FROM_PACKAGE</name>
            <value>1</value>
        </property>
        <property>
            <name>FUNCTIONS_EXTENSION_VERSION</name>
            <value>~3</value>
        </property>
    </appSettings>
</configuration>
```

## <a name="bindings"></a>바인딩

버전 2.x부터 런타임은 다음과 같은 이점을 제공 하는 새로운 [바인딩 확장성 모델](https://github.com/Azure/azure-webjobs-sdk-extensions/wiki/Binding-Extensions-Overview) 을 사용 합니다.

* 타사 바인딩 확장 지원.

* 런타임과 바인딩을 분리. 이렇게 변경하면 바인딩 확장의 버전을 지정하고 독립적으로 릴리스할 수 있습니다. 예를 들어 기본 SDK의 최신 버전을 사용하는 확장 버전으로 업그레이드하도록 선택할 수 있습니다.

* 사용 중인 바인딩만 런타임에 알려지고 로드되는 가벼운 실행 환경.

HTTP 및 타이머 트리거를 제외하고 모든 바인딩은 명시적으로 함수 앱 프로젝트에 추가하거나 포털에 등록해야 합니다. 자세한 내용은 [바인딩 확장 등록](./functions-bindings-expressions-patterns.md)을 참조하세요.

다음 표에는 각 런타임 버전에서 지원되는 바인딩이 나와 있습니다.

[!INCLUDE [Full bindings table](../../includes/functions-bindings.md)]

[!INCLUDE [Timeout Duration section](../../includes/functions-timeout-duration.md)]

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음 자료를 참조하세요.

* [Azure Functions를 로컬에서 코딩 및 테스트](functions-run-local.md)
* [Azure Functions 런타임 버전을 대상으로 지정하는 방법](set-runtime-version.md)
* [릴리스 정보](https://github.com/Azure/azure-functions-host/releases)
