---
title: Azure Functions 바인딩 확장 등록
description: 사용자 환경에 따라 Azure Functions 바인딩 확장을 등록 하는 방법에 대해 알아봅니다.
author: craigshoemaker
ms.topic: reference
ms.date: 07/08/2019
ms.author: cshoe
ms.openlocfilehash: 35df4c6c20345053bcc39a267a90a7bb1b227241
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/28/2020
ms.locfileid: "76766242"
---
# <a name="register-azure-functions-binding-extensions"></a>Azure Functions 바인딩 확장 등록

Azure Functions 버전 2.x에서 [바인딩은](./functions-triggers-bindings.md) 함수 런타임과 별도의 패키지로 사용할 수 있습니다. .NET 함수는 NuGet 패키지를 통해 바인딩에 액세스 하지만, 확장 번들을 사용 하면 다른 함수에서 구성 설정을 통해 모든 바인딩에 액세스할 수 있습니다.

바인딩 확장과 관련 된 다음 항목을 고려 합니다.

- 바인딩 확장 [은 Visual Studio를 사용 하 여 클래스 라이브러리 C# 를 만드는](#local-csharp)경우를 제외 하 고는 함수 1. x에 명시적으로 등록 되지 않습니다.

- HTTP 및 타이머 트리거는 기본적으로 지원 되며 확장이 필요 하지 않습니다.

다음 표는 바인딩을 등록 하는 시기와 방법을 나타냅니다.

| 개발 환경 |등록<br/> (Functions 1.x)  |등록<br/> (Functions 2.x)  |
|-------------------------|------------------------------------|------------------------------------|
|Azure Portal|자동|자동|
|Non-.NET 언어 또는 로컬 Azure 핵심 도구 개발|자동|[Azure Functions Core Tools 및 확장 번들 사용](#extension-bundles)|
|C#Visual Studio를 사용 하는 클래스 라이브러리|[NuGet 도구 사용](#vs)|[NuGet 도구 사용](#vs)|
|Visual Studio Code를 사용하는 C# 클래스 라이브러리|N/A|[.NET Core CLI 사용](#vs-code)|

## <a name="extension-bundles"></a>로컬 개발용 확장 번들

확장 번들은 함수 앱에 호환 되는 함수 바인딩 확장 집합을 추가할 수 있는 배포 기술입니다. 앱을 빌드할 때 미리 정의 된 확장 집합이 추가 됩니다. 번들에 정의 된 확장 패키지는 서로 호환 되므로 패키지 간의 충돌을 방지할 수 있습니다. 앱의 호스트나 json 파일에서 확장 번들을 사용 하도록 설정 합니다.  

버전 2.x 이상 버전의 함수 런타임에서 확장 번들을 사용할 수 있습니다. 로컬로 개발 하는 경우 최신 버전의 [Azure Functions Core Tools](functions-run-local.md#v2)을 사용 하 고 있는지 확인 합니다.

원격으로 빌드할 때 Azure Functions Core Tools, Visual Studio Code 및를 사용 하 여 로컬 개발을 위해 확장 번들을 사용 합니다.

확장 번들을 사용 하지 않는 경우 바인딩 확장을 설치 하기 전에 로컬 컴퓨터에 .NET Core 2.x SDK를 설치 해야 합니다. 확장 번들은 로컬 개발을 위한 이러한 요구 사항을 제거 합니다. 

확장 번들을 사용 하려면 `extensionBundle`에 대한 다음 항목을 포함 하도록 *호스트나 json* 파일을 업데이트 합니다.
 
[!INCLUDE [functions-extension-bundles-json](../../includes/functions-extension-bundles-json.md)]

<a name="local-csharp"></a>

## <a name="vs"></a>Visual Studio를 사용 하는 C\# 클래스 라이브러리

다음 예제와 같이 **Visual Studio**에서 패키지 관리자 콘솔을 사용 하 여 패키지 [를 설치할 수](https://docs.microsoft.com/nuget/tools/ps-ref-install-package) 있습니다.

```powershell
Install-Package Microsoft.Azure.WebJobs.Extensions.ServiceBus -Version <TARGET_VERSION>
```

지정 된 바인딩에 사용 되는 패키지의 이름은 해당 바인딩에 대한 참조 문서에서 제공 됩니다. 예를 들어 [Service Bus 바인딩 참조 문서의 패키지 섹션](functions-bindings-service-bus.md#packages---functions-1x)을 참조하세요.

예제의 `<TARGET_VERSION>`을 패키지의 특정 버전(예: `3.0.0-beta5`)으로 바꿉니다. 유효한 버전은 [NuGet.org](https://nuget.org)의 개별 패키지 페이지에 나열 되어 있습니다. 런타임 1.x 또는 2.x 함수에 해당 하는 주 버전은 바인딩에 대한 참조 문서에 지정 되어 있습니다.

`Install-Package`를 사용 하 여 바인딩을 참조 하는 경우에는 [확장 번들](#extension-bundles)을 사용할 필요가 없습니다. 이 방법은 Visual Studio에서 빌드된 클래스 라이브러리에만 적용 됩니다.

## <a name="vs-code"></a>C# Visual Studio Code를 포함 하는 클래스 라이브러리

**Visual Studio Code**에서 .NET Core CLI의 C# [dotnet add package](https://docs.microsoft.com/dotnet/core/tools/dotnet-add-package) 명령을 사용 하 여 명령 프롬프트에서 클래스 라이브러리 프로젝트에 대한 패키지를 설치 합니다. 다음 예제에서는 바인딩을 추가 하는 방법을 보여 줍니다.

```terminal
dotnet add package Microsoft.Azure.WebJobs.Extensions.<BINDING_TYPE_NAME> --version <TARGET_VERSION>
```

.NET Core CLI는 Azure Functions 2.x 개발에만 사용할 수 있습니다.

`<BINDING_TYPE_NAME>`은 필요한 바인딩을 포함 하는 패키지의 이름으로 바꿉니다. [지원 되](./functions-triggers-bindings.md#supported-bindings)는 바인딩 목록에서 원하는 바인딩 참조 문서를 찾을 수 있습니다.

예제의 `<TARGET_VERSION>`을 패키지의 특정 버전(예: `3.0.0-beta5`)으로 바꿉니다. 유효한 버전은 [NuGet.org](https://nuget.org)의 개별 패키지 페이지에 나열 되어 있습니다. 런타임 1.x 또는 2.x 함수에 해당 하는 주 버전은 바인딩에 대한 참조 문서에 지정 되어 있습니다.

## <a name="next-steps"></a>다음 단계
> [!div class="nextstepaction"]
> [Azure 함수 트리거 및 바인딩 예제](./functions-bindings-example.md)
