---
title: Azure Functions 런타임 버전 개요
description: Azure Functions는 여러 버전의 런타임을 지원합니다. 버전 간 차이점과 적합한 버전을 선택하는 방법을 알아봅니다.
services: functions
documentationcenter: ''
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: conceptual
ms.date: 10/03/2018
ms.author: glenga
ms.openlocfilehash: 6988fb547b07f81891efea3caad8bf34f4c8a476
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61036317"
---
# <a name="azure-functions-runtime-versions-overview"></a>Azure Functions 런타임 버전 개요

 Azure Functions 런타임의 두 주요 버전이 있습니다. 1.x 및 2.x입니다. 새로운 기능이 추가되고 개선이 적용되고 있는 현재 버전은 2.x이지만 두 가지 모두 프로덕션 시나리오에서 지원됩니다.  여기에서는 두 버전의 차이점, 각 버전을 만드는 방법, 1.x에서 2.x로 업그레이드하는 방법에 대해 자세히 설명합니다.

> [!NOTE]
> 이 문서에서는 클라우드 서비스 Azure Functions를 참조합니다. 온-프레미스에서 Azure Functions를 실행할 수 있는 미리 보기 제품에 대한 자세한 내용은 [Azure Functions 런타임 개요](functions-runtime-overview.md)를 참조하세요.

## <a name="cross-platform-development"></a>교차 플랫폼 개발

버전 2.x 런타임은 .NET Core 2에서 실행되며, macOS 및 Linux를 포함하여 .NET Core에서 지원하는 모든 플랫폼에서 실행할 수 있습니다. .NET Core에서 실행하면 플랫폼 간 개발 및 호스팅 시나리오가 가능합니다.

비교해보면 버전 1.x 런타임은 Azure Portal 또는 Windows 컴퓨터의 개발 및 호스팅만 지원합니다.

## <a name="languages"></a>Languages

버전 2.x 런타임은 새 언어 확장성 모델을 사용합니다. 버전 2.x에서는 함수 앱의 모든 함수가 동일한 언어를 공유해야 합니다. 함수 앱의 함수 언어는 앱을 만들 때 선택합니다.

Azure Functions 1.x 실험 언어는 새 모델을 사용하도록 업데이트되지 않을 예정이므로 2.x에서 지원되지 않습니다. 다음 표는 각 런타임 버전에서 현재 지원되는 프로그래밍 언어를 나타냅니다.

[!INCLUDE [functions-supported-languages](../../includes/functions-supported-languages.md)]

자세한 내용은 [지원되는 언어](supported-languages.md)를 참조하세요.

## <a name="creating-1x-apps"></a>버전 1.x에서 실행

기본적으로 Azure Portal에서 만든 함수 앱은 버전 2.x로 설정됩니다. 가능한 경우 새 기능을 위한 투자가 진행될 이 런타임 버전을 사용하는 것이 좋습니다. 필요한 경우 버전 1.x 런타임에서 함수 앱을 계속 실행할 수 있습니다. 함수 앱을 만든 후, 함수를 추가하기 전에만 런타임 버전을 변경할 수 있습니다. 런타임 버전을 1.x로 고정하는 방법을 알아보려면 [현재 런타임 버전 확인 및 업데이트](set-runtime-version.md#view-and-update-the-current-runtime-version)를 참조하세요.

## <a name="migrating-from-1x-to-2x"></a>1.x에서 2.x로 마이그레이션

버전 1.x 런타임 사용하도록 작성된 기존 앱을 버전 2.x를 대신 사용하도록 마이그레이션할 수 있습니다. 수행해야 하는 대부분의 변경은 .NET Framework 4.7과 .NET Core 2 간의 C# API 변경과 같은 언어 런타임의 변경과 관련되어 있습니다. 또한 선택한 언어 런타임과 코드 및 라이브러리가 호환되는지 확인해야 합니다. 마지막으로 아래에 강조 표시된 트리거, 바인딩 및 기능의 변경 내용을 기록해 두어야 합니다. 최상의 마이그레이션 결과를 얻으려면 버전 2.x에 대한 새 함수 앱을 만들고 기존 버전 1.x 함수 코드를 새 앱으로 이식해야 합니다.  

### <a name="changes-in-triggers-and-bindings"></a>트리거 및 바인딩의 변경 내용

버전 2.x를 사용하려면 앱의 함수에 사용되는 특정 트리거 및 바인딩의 확장을 설치해야 합니다. 유일한 예외는 확장이 필요 없는 이 HTTP 및 타이머 트리거입니다.  자세한 내용은 [바인딩 확장 등록 및 설치](./functions-bindings-register.md)를 참조하세요.

버전 간에 `function.json` 또는 함수의 특성도 일부 변경되었습니다. 예를 들어, 이벤트 허브 `path` 속성은 이제 `eventHubName`입니다. 각 바인딩의 설명서에 대한 링크는 [기존 바인딩 테이블](#bindings)을 참조하세요.

### <a name="changes-in-features-and-functionality"></a>기능 변경

새 버전에서 제거, 업데이트 또는 대체된 일부 기능입니다. 이 섹션에서는 버전 1.x를 사용한 후에 버전 2.x에서 확인된 변경 내용을 자세히 설명합니다.

버전 2.x에서 다음과 같은 사항이 변경되었습니다.

* HTTP 엔드포인트를 호출하기 위한 키는 항상 Azure Blob Storage에 암호화된 상태로 저장됩니다. 버전 1.x에서 키는 기본적으로 Azure File Storage에 저장되었습니다. 앱을 버전 1.x에서 버전 2.x로 업그레이드할 때 파일 저장소에 있는 기존 암호가 다시 설정됩니다.

* 버전 2.x 런타임에서는 웹후크 공급자가 기본적으로 지원되지 않습니다. 성능 향상을 위해 이렇게 변경되었습니다. HTTP 트리거를 여전히 웹후크에 대한 엔드포인트로 사용할 수 있습니다.

* 호스트 구성 파일(host.json)은 비워 두거나 문자열 `"version": "2.0"`을 포함해야 합니다.

* 모니터링을 향상시키기 위해 [`AzureWebJobsDashboard`](functions-app-settings.md#azurewebjobsdashboard) 설정을 사용하는 포털의 WebJobs 대시보드가 [`APPINSIGHTS_INSTRUMENTATIONKEY`](functions-app-settings.md#appinsights_instrumentationkey) 설정을 사용하는 Azure Application Insights로 바뀌었습니다. 자세한 내용은 [Azure Functions 모니터링](functions-monitoring.md)을 참조하세요.

* 함수 앱의 모든 함수가 동일한 언어를 공유해야 합니다. 함수 앱을 만들 때 앱의 런타임 스택을 선택해야 합니다. 런타임 스택은 애플리케이션 설정의 [`FUNCTIONS_WORKER_RUNTIME`](functions-app-settings.md#functions_worker_runtime) 값으로 지정됩니다. 이 요구 사항은 공간 효율성 및 시작 시간을 개선하기 위해 추가되었습니다. 로컬로 개발하는 경우 [local.settings.json 파일](functions-run-local.md#local-settings-file)에 이 설정을 포함해야 합니다.

* App Service 계획의 함수에 대한 기본 시간 제한은 30분으로 변경되었습니다. host.json에서 [functionTimeout](functions-host-json.md#functiontimeout) 설정을 사용하여 수동으로 제한 시간을 다시 무제한으로 변경할 수 있습니다.

* HTTP 동시성 제한은 기본값으로 인스턴스당 동시 요청 수 100개를 갖는 사용 계획 함수에 대해 기본적으로 구현됩니다. 이 설정은 host.json 파일의 [`maxConcurrentRequests`](functions-host-json.md#http) 설정에서 변경할 수 있습니다.

* [.NET core 제한](https://github.com/Azure/azure-functions-host/issues/3414)때문에 F # 스크립트(.fsx) 함수에 대한 지원이 제거되었습니다. 컴파일된 F# 함수(.fs)는 계속 지원됩니다.

* Event Grid 트리거 웹후크의 URL 형식은 `https://{app}/runtime/webhooks/{triggerName}`으로 변경되었습니다.

### <a name="migrating-a-locally-developed-application"></a>로컬에서 개발한 애플리케이션 마이그레이션

버전 1.x 런타임을 사용하여 로컬로 개발한 기존 함수 앱 프로젝트가 있을 수 있습니다. 버전 2.x로 업그레이드하려면 버전 2.x용 로컬 함수 앱 프로젝트를 만든 후 기존 코드를 새 앱으로 이식해야 합니다. 기존 프로젝트 및 코드를 수동으로 업데이트할 수 있습니다(일종의 "전체" 업그레이드). 그러나 버전 1.x와 버전 2.x 간에는 아직도 많은 기능 개선이 필요합니다. 예를 들어 C#의 디버깅 개체가 `TraceWriter`에서 `ILogger`로 변경되었습니다. 새 버전 2.x 프로젝트를 만들어 최신 버전 2.x 템플릿을 기준으로 하는 업데이트된 함수로 시작합니다.

#### <a name="visual-studio-runtime-versions"></a>Visual Studio 런타임 버전

Visual Studio에서 프로젝트를 만들 때 런타임 버전을 선택합니다. Visual Studio용 Azure Functions 도구는 둘 다 주 런타임 버전을 지원합니다. 디버깅 및 게시를 수행할 때 프로젝트 설정에 따라 올바른 버전이 사용됩니다. 버전 설정은 `.csproj` 파일의 다음 속성에 정의됩니다.

##### <a name="version-1x"></a>버전 1.x

```xml
<TargetFramework>net461</TargetFramework>
<AzureFunctionsVersion>v1</AzureFunctionsVersion>
```

##### <a name="version-2x"></a>버전 2.x

```xml
<TargetFramework>netcoreapp2.2</TargetFramework>
<AzureFunctionsVersion>v2</AzureFunctionsVersion>
```

프로젝트를 디버그하거나 게시하는 경우 올바른 버전의 런타임이 사용됩니다.

#### <a name="vs-code-and-azure-functions-core-tools"></a>VS Code 및 Azure Functions 핵심 도구

[Azure Functions Core Tools](functions-run-local.md)는 명령줄 개발에 사용되며 Visual Studio Code용 [Azure Functions 확장](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)에서도 사용됩니다. 버전 2.x에 대해 개발하려면 Core Tools 버전 2.x를 설치합니다. 버전 1.x 개발에는 Core Tools 버전 1.x가 필요합니다. 자세한 내용은 [Azure Functions Core Tools 설치](functions-run-local.md#install-the-azure-functions-core-tools)를 참조하세요.

Visual Studio Code 개발의 경우 설치된 도구의 버전과 일치하도록 `azureFunctions.projectRuntime`에 대한 사용자 설정을 업데이트해야 할 수도 있습니다.  이 설정은 함수 앱을 만드는 동안 사용되는 템플릿 및 언어도 업데이트합니다.

### <a name="changing-version-of-apps-in-azure"></a>Azure에서 앱 버전 변경

Azure에 게시된 앱에서 사용하는 Functions 런타임 버전은 [`FUNCTIONS_EXTENSION_VERSION`](functions-app-settings.md#functions_extension_version) 애플리케이션 설정에 따라 결정됩니다. `~2` 값은 버전 2.x 런타임을 대상으로 하고 `~1`은 버전 1.x 런타임을 대상으로 합니다. 이 설정을 임의로 변경하지 않도록 합니다. 함수의 다른 앱 설정 및 코드도 변경해야 할 수 있기 때문입니다. 함수 앱을 다른 런타임 버전으로 마이그레이션하기 위한 권장 방법에 대한 자세한 내용은 [Azure Functions 런타임 버전을 대상으로 지정하는 방법](set-runtime-version.md)을 참조하세요.

## <a name="bindings"></a>바인딩

버전 2.x 런타임은 다음과 같은 장점이 있는 새 [바인딩 확장성 모델](https://github.com/Azure/azure-webjobs-sdk-extensions/wiki/Binding-Extensions-Overview)을 사용합니다.

* 타사 바인딩 확장 지원.

* 런타임과 바인딩을 분리. 이렇게 변경하면 바인딩 확장의 버전을 지정하고 독립적으로 릴리스할 수 있습니다. 예를 들어 기본 SDK의 최신 버전을 사용하는 확장 버전으로 업그레이드하도록 선택할 수 있습니다.

* 사용 중인 바인딩만 런타임에 알려지고 로드되는 가벼운 실행 환경.

HTTP 및 타이머 트리거를 제외하고 모든 바인딩은 명시적으로 함수 앱 프로젝트에 추가하거나 포털에 등록해야 합니다. 자세한 내용은 [바인딩 확장 등록](./functions-bindings-expressions-patterns.md)을 참조하세요.

다음 표에는 각 런타임 버전에서 지원되는 바인딩이 나와 있습니다.

[!INCLUDE [Full bindings table](../../includes/functions-bindings.md)]

[!INCLUDE [Timeout Duration section](../../includes/functions-timeout-duration.md)]

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음 리소스를 참조하세요.

* [Azure Functions를 로컬에서 코딩 및 테스트](functions-run-local.md)
* [Azure Functions 런타임 버전을 대상으로 지정하는 방법](set-runtime-version.md)
* [릴리스 정보](https://github.com/Azure/azure-functions-host/releases)
