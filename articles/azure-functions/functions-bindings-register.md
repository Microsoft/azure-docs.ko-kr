---
title: Azure Functions 바인딩 확장 등록
description: 사용자 환경에 따라 Azure Functions 바인딩 확장 등록 하는 방법을 알아봅니다.
services: functions
documentationcenter: na
author: craigshoemaker
manager: jeconnoc
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
origin.date: 02/18/2019
ms.date: 04/26/2019
ms.author: v-junlch
ms.openlocfilehash: 5534086d5754691f650370e465fa2c63210e0dc7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61437857"
---
# <a name="register-azure-functions-binding-extensions"></a>Azure Functions 바인딩 확장 등록

Azure Functions HTTP 및 즉시는 타이머를 지원 합니다. 다른 서비스를 사용 하려면 설치 해야 하거나 *등록* 는 [바인딩](./functions-triggers-bindings.md) 확장 합니다. 바인딩 확장은 Azure 핵심 도구 또는 NuGet 패키지를 통해 제공 됩니다. 

다음 표는 바인딩을 등록 하는 방법과 시기를 나타냅니다.

| 개발 환경 |등록<br/> (Functions 1.x)  |등록<br/> (Functions 2.x)  |
|-------------------------|------------------------------------|------------------------------------|
|Azure portal|자동|[자동(프롬프트)](#azure-portal-development)|
|비.NET 언어 또는 로컬 Azure 핵심 도구 개발|자동|[핵심 도구 CLI 명령 사용](#local-development-azure-functions-core-tools)|
|Visual Studio 2017을 사용하는 C# 클래스 라이브러리|[NuGet 도구 사용](#c-class-library-with-visual-studio-2017)|[NuGet 도구 사용](#c-class-library-with-visual-studio-2017)|
|Visual Studio Code를 사용하는 C# 클래스 라이브러리|N/A|[.NET Core CLI 사용](#c-class-library-with-visual-studio-code)|

다음과 같은 바인딩 유형은 HTTP 및 타이머와 같은 환경 및 모든 버전에서 자동으로 등록되기 때문에 명시적인 등록이 필요하지 않은 예외 항목입니다.

> [!IMPORTANT]
> 이 문서의 나머지 부분에 적용 됩니다에 대 한이 콘텐츠는 2.x에 작동 합니다. 바인딩 확장 함수에서 명시적으로 등록 되지 않습니다 경우는 제외 1.x [만들기를 C# Visual Studio 2017을 사용 하 여 클래스 라이브러리](#local-csharp)합니다.

## <a name="azure-portal-development"></a>Azure Portal 개발

함수를 만들거나 바인딩을 추가할 때 트리거 또는 바인딩에 대한 확장에 등록이 필요한 경우 메시지가 표시됩니다. **설치**를 클릭하여 프롬프트에 응답하고 확장을 등록합니다. 소비 계획에 대해 설치는 최대 10분이 소요될 수 있습니다. 

지정된 함수 앱에 대해 각 확장을 한 번만 등록하면 됩니다. 포털에서 사용할 수 없는 지원되는 바인딩의 경우 또는 설치된 확장을 업데이트하려면 [포털에서 Azure Functions 바인딩 확장을 수동으로 설치 또는 업데이트](install-update-binding-extensions-manual.md)할 수도 있습니다.  

## <a name="local-development-azure-functions-core-tools"></a>Azure Functions 핵심 도구 로컬 개발

[!INCLUDE [functions-core-tools-install-extension](../../includes/functions-core-tools-install-extension.md)]

<a name="local-csharp"></a>
## <a name="c-class-library-with-visual-studio-2017"></a>Visual Studio 2017을 통한 C# 클래스 라이브러리

**Visual Studio 2017**의 경우 다음 예제와 같이 [Install-Package](https://docs.microsoft.com/nuget/tools/ps-ref-install-package) 명령을 사용하여 패키지 관리자 콘솔에서 패키지를 설치할 수 있습니다.

```powershell
Install-Package Microsoft.Azure.WebJobs.Extensions.ServiceBus -Version <target_version>
```

지정된 바인딩에 사용할 패키지의 이름은 해당 바인딩에 대한 참조 문서에 제공됩니다. 예를 들어 [Service Bus 바인딩 참조 문서의 패키지 섹션](functions-bindings-service-bus.md#packages---functions-1x)을 참조하세요.

예제의 `<target_version>`을 패키지의 특정 버전(예: `3.0.0-beta5`)으로 바꿉니다. 유효한 버전은 [NuGet.org](https://nuget.org)의 개별 패키지 페이지에 나열되어 있습니다. Functions 참조 1.x 또는 2.x에 해당하는 주요 버전은 바인딩에 대한 참조 문서에 지정되어 있습니다.

## <a name="c-class-library-with-visual-studio-code"></a>Visual Studio Code를 통한 C# 클래스 라이브러리

**Visual Studio Code**의 경우 다음 예제와 같이 .NET Core CLI에서 [dotnet add package](https://docs.microsoft.com/dotnet/core/tools/dotnet-add-package) 명령을 사용하여 명령 프롬프트에서 패키지를 설치할 수 있습니다.

```terminal
dotnet add package Microsoft.Azure.WebJobs.Extensions.ServiceBus --version <target_version>
```

.NET Core CLI는 Azure Functions 2.x 개발에만 사용할 수 있습니다.

지정된 바인딩에 사용할 패키지의 이름은 해당 바인딩에 대한 참조 문서에 제공됩니다. 예를 들어 [Service Bus 바인딩 참조 문서의 패키지 섹션](functions-bindings-service-bus.md#packages---functions-1x)을 참조하세요.

예제의 `<target_version>`을 패키지의 특정 버전(예: `3.0.0-beta5`)으로 바꿉니다. 유효한 버전은 [NuGet.org](https://nuget.org)의 개별 패키지 페이지에 나열되어 있습니다. Functions 참조 1.x 또는 2.x에 해당하는 주요 버전은 바인딩에 대한 참조 문서에 지정되어 있습니다.

## <a name="next-steps"></a>다음 단계
> [!div class="nextstepaction"]
> [Azure 함수 트리거 및 바인딩 예제](./functions-bindings-example.md)


