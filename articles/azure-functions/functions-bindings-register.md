---
title: Azure Functions 바인딩 확장 등록
description: 사용자 환경에 따라 Azure Functions 바인딩 확장을 등록 하는 방법에 대해 알아봅니다.
author: craigshoemaker
ms.topic: reference
ms.date: 08/16/2020
ms.author: cshoe
ms.openlocfilehash: 942ca3229808b57894598c3477e9dc97e40e8c80
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88689566"
---
# <a name="register-azure-functions-binding-extensions"></a>Azure Functions 바인딩 확장 등록

Azure Functions 버전 2.x부터 함수 런타임에는 기본적으로 HTTP 및 타이머 트리거도 포함 됩니다. 다른 [트리거와 바인딩은](./functions-triggers-bindings.md) 별도의 패키지로 사용할 수 있습니다.

.NET 클래스 라이브러리 함수 앱은 프로젝트에 NuGet 패키지로 설치 된 바인딩을 사용 합니다. 확장 번들을 사용 하면 non-.NET 함수 앱이 .NET 인프라를 처리할 필요 없이 동일한 바인딩을 사용할 수 있습니다.

다음 표는 바인딩을 등록 하는 시기와 방법을 나타냅니다.

| 개발 환경 |등록<br/> (Functions 1.x)  |등록<br/> 함수 3(sp3)의  |
|-------------------------|------------------------------------|------------------------------------|
|Azure portal|자동|자동 번역<sup>*</sup>|
|Non-.NET 언어|자동|[확장 번들](#extension-bundles) 사용 (권장) 또는 [명시적으로 확장 설치](#explicitly-install-extensions)|
|Visual Studio를 사용 하는 c # 클래스 라이브러리|[NuGet 도구 사용](#vs)|[NuGet 도구 사용](#vs)|
|Visual Studio Code를 사용하는 C# 클래스 라이브러리|해당 없음|[.NET Core CLI 사용](#vs-code)|

<sup>*</sup> 포털은 확장 번들을 사용 합니다.

## <a name="access-extensions-in-non-net-languages"></a>Non-.NET 언어의 액세스 확장

Java, JavaScript, PowerShell, Python 및 사용자 지정 처리기 함수 앱의 경우 바인딩에 액세스 하기 위해 확장 번들을 사용 하는 것이 좋습니다. 확장 번들을 사용할 수 없는 경우에는 명시적으로 바인딩 확장을 설치할 수 있습니다.

### <a name="extension-bundles"></a><a name="extension-bundles"></a>확장 번들

확장 번들은 함수 앱에 호환 되는 바인딩 확장 집합을 추가 하는 방법입니다. 응용 프로그램의 *host.js* 파일에서 확장 번들을 사용 하도록 설정 합니다.

버전 2.x 이상 버전의 함수 런타임에서 확장 번들을 사용할 수 있습니다.

확장 번들의 버전이 지정 됩니다. 각 버전에는 함께 작동 하도록 확인 된 특정 바인딩 확장 집합이 포함 되어 있습니다. 앱에서 필요한 확장을 기반으로 번들 버전을 선택 합니다.

함수 앱에 확장 번들을 추가 하려면host.js에 섹션을 추가 `extensionBundle` 합니다 *host.json*. 대부분의 경우 Visual Studio Code 및 Azure Functions Core Tools 자동으로 추가 됩니다.

[!INCLUDE [functions-extension-bundles-json](../../includes/functions-extension-bundles-json.md)]

다음 표에서는 현재 사용할 수 있는 버전의 기본 *Microsoft. Node.js 번들* 번들 및 여기에 포함 된 확장에 대 한 링크를 나열 합니다.

| 번들 버전 | host.js의 버전 | 포함 된 확장 |
| --- | --- | --- |
| 1.x | `[1.*, 2.0.0)` | 번들을 생성 하는 데 사용 된 [extensions.js](https://github.com/Azure/azure-functions-extension-bundles/blob/v1.x/src/Microsoft.Azure.Functions.ExtensionBundle/extensions.json) 를 참조 하세요. |
| 2.x | `[2.*, 3.0.0)` | 번들을 생성 하는 데 사용 된 [extensions.js](https://github.com/Azure/azure-functions-extension-bundles/blob/v2.x/src/Microsoft.Azure.Functions.ExtensionBundle/extensions.json) 를 참조 하세요. |

> [!NOTE]
> host.js에서 사용자 지정 버전 범위를 지정할 수는 있지만이 테이블의 버전 값을 사용 하는 것이 좋습니다.

### <a name="explicitly-install-extensions"></a><a name="explicitly-install-extensions"></a>명시적으로 확장 설치

[!INCLUDE [functions-extension-register-core-tools](../../includes/functions-extension-register-core-tools.md)]

## <a name="install-extensions-from-nuget-in-net-languages"></a><a name="local-csharp"></a>.NET 언어로 NuGet에서 확장 설치

C # 클래스 라이브러리 기반 함수 프로젝트의 경우 확장을 직접 설치 해야 합니다. 확장 번들은 c # 클래스 라이브러리를 기반으로 하지 않는 프로젝트를 위해 특별히 설계 되었습니다.

### <a name="c-class-library-with-visual-studio"></a><a name="vs"></a>\#Visual Studio를 사용 하는 C 클래스 라이브러리

다음 예제와 같이 **Visual Studio**에서 패키지 관리자 콘솔을 사용 하 여 패키지 [를 설치할 수](/nuget/tools/ps-ref-install-package) 있습니다.

```powershell
Install-Package Microsoft.Azure.WebJobs.Extensions.ServiceBus -Version <TARGET_VERSION>
```

지정 된 바인딩에 사용 되는 패키지의 이름은 해당 바인딩에 대 한 참조 문서에서 제공 됩니다. 예를 들어 [Service Bus 바인딩 참조 문서의 패키지 섹션](functions-bindings-service-bus.md#functions-1x)을 참조하세요.

예제의 `<TARGET_VERSION>`을 패키지의 특정 버전(예: `3.0.0-beta5`)으로 바꿉니다. 유효한 버전은 [NuGet.org](https://nuget.org)의 개별 패키지 페이지에 나열 되어 있습니다. 런타임 1.x 또는 2.x 함수에 해당 하는 주 버전은 바인딩에 대 한 참조 문서에 지정 되어 있습니다.

`Install-Package`를 사용 하 여 바인딩을 참조 하는 경우에는 [확장 번들](#extension-bundles)을 사용할 필요가 없습니다. 이 방법은 Visual Studio에서 빌드된 클래스 라이브러리에만 적용 됩니다.

### <a name="c-class-library-with-visual-studio-code"></a><a name="vs-code"></a> Visual Studio Code를 사용 하는 c # 클래스 라이브러리

**Visual Studio Code**에서 .NET Core CLI의 [dotnet add package](/dotnet/core/tools/dotnet-add-package) 명령을 사용 하 여 명령 프롬프트에서 c # 클래스 라이브러리 프로젝트에 대 한 패키지를 설치 합니다. 다음 예제에서는 바인딩을 추가 하는 방법을 보여 줍니다.

```terminal
dotnet add package Microsoft.Azure.WebJobs.Extensions.<BINDING_TYPE_NAME> --version <TARGET_VERSION>
```

.NET Core CLI는 Azure Functions 2.x 개발에만 사용할 수 있습니다.

`<BINDING_TYPE_NAME>`필요한 바인딩을 포함 하는 패키지의 이름으로 대체 합니다. [지원 되](./functions-triggers-bindings.md#supported-bindings)는 바인딩 목록에서 원하는 바인딩 참조 문서를 찾을 수 있습니다.

예제의 `<TARGET_VERSION>`을 패키지의 특정 버전(예: `3.0.0-beta5`)으로 바꿉니다. 유효한 버전은 [NuGet.org](https://nuget.org)의 개별 패키지 페이지에 나열 되어 있습니다. 런타임 1.x 또는 2.x 함수에 해당 하는 주 버전은 바인딩에 대 한 참조 문서에 지정 되어 있습니다.

## <a name="next-steps"></a>다음 단계
> [!div class="nextstepaction"]
> [Azure 함수 트리거 및 바인딩 예제](./functions-bindings-example.md)
