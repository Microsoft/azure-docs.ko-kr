---
title: 로컬로 Azure Functions 개발 및 실행 | Microsoft Docs
description: Azure Functions에서 실행하기 전에 로컬 컴퓨터에서 Azure Functions를 코딩 및 테스트하는 방법을 알아봅니다.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 6486b38040fc92c2ece839801293817c4afc7cf5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60614000"
---
# <a name="code-and-test-azure-functions-locally"></a>Azure Functions를 로컬에서 코딩 및 테스트

[Azure Portal]에서 Azure Functions를 개발하고 테스트할 수 있지만 대부분의 개발자는 로컬 개발 환경을 선호합니다. Functions를 사용하면 즐겨 찾는 코드 편집기와 개발 도구를 사용하여 로컬 컴퓨터에서 함수를 쉽게 만들고 테스트할 수 있습니다. 로컬 함수는 라이브 Azure 서비스에 연결할 수 있고 사용자는 전체 Functions 런타임을 사용하여 로컬 머신에서 해당 함수를 디버깅할 수 있습니다.

## <a name="local-development-environments"></a>로컬 개발 환경

로컬 컴퓨터에서 함수를 개발하는 방식은 [언어](supported-languages.md) 및 기본 도구에 따라 달라집니다. 다음 표에서 환경은 로컬 개발을 지원합니다.

|Environment                              |Languages         |설명|
|-----------------------------------------|------------|---|
| [명령 프롬프트 또는 터미널](functions-run-local.md) | [C#(클래스 라이브러리)](functions-dotnet-class-library.md), [C# 스크립트(.csx)](functions-reference-csharp.md), [JavaScript](functions-reference-node.md) | [Azure Functions 핵심 도구]는 함수를 만들기 위한 핵심 런타임 및 템플릿을 제공하여 로컬 개발을 사용하도록 설정합니다. 버전 2.x는 Linux, MacOS 및 Windows에서 개발을 지원합니다. 모든 환경은 로컬 Functions 런타임에 대한 핵심 도구를 사용합니다. |
|[Visual Studio Code](functions-create-first-function-vs-code.md)| [C#(클래스 라이브러리)](functions-dotnet-class-library.md), [C# 스크립트(.csx)](functions-reference-csharp.md), [JavaScript](functions-reference-node.md) | [VS Code용 Azure Functions 확장](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)은 VS Code에 Functions 지원을 추가합니다. 핵심 도구가 필요합니다. 2.x 버전의 핵심 도구를 사용하면 Linux, MacOS 및 Windows에서 개발을 지원합니다. 자세한 내용은 [Visual Studio Code를 사용하여 첫 번째 함수 만들기](functions-create-first-function-vs-code.md)를 참조하세요. |
| [Visual Studio 2017](functions-develop-vs.md) | [C#(클래스 라이브러리)](functions-dotnet-class-library.md) | Azure Functions 도구는 [Visual Studio 2017 버전 15.5](https://www.visualstudio.com/vs/) 이상의 **Azure 개발** 워크로드에 포함되어 있습니다. 클래스 라이브러리에서 함수를 컴파일하고 .dll을 Azure에 게시할 수 있습니다. 로컬 테스트에 대한 핵심 도구를 포함합니다. 자세한 내용은 [Visual Studio를 사용하여 Azure Functions 개발](functions-develop-vs.md)을 참조하세요. |
| [Maven](functions-create-first-java-maven.md)(다양) | [Java](functions-reference-java.md) | 핵심 도구와 통합하여 Java 함수를 개발할 수 있습니다. 버전 2.x는 Linux, MacOS 및 Windows에서 개발을 지원합니다. 자세한 내용은 [Java 및 Maven을 사용하여 Azure에서 첫 번째 함수 만들기](functions-create-first-java-maven.md)를 참조하세요. 또한 [Eclipse](functions-create-maven-eclipse.md) 및 [IntelliJ IDEA](functions-create-maven-intellij.md)를 사용한 개발을 지원합니다. |

[!INCLUDE [Don't mix development environments](../../includes/functions-mixed-dev-environments.md)]

이러한 로컬 개발 환경 각각을 통해 함수 앱 프로젝트를 만들고 미리 정의된 함수 템플릿을 사용하여 새 함수를 만들 수 있습니다. 각각은 핵심 도구를 사용합니다. 다른 앱처럼 고유한 머신에서 실제 Functions 런타임에 대해 함수를 테스트하고 디버그할 수 있습니다. 이러한 환경 중 하나의 함수 앱 프로젝트를 Azure에 게시할 수도 있습니다.  

## <a name="next-steps"></a>다음 단계

+ Visual Studio 2017을 사용하여 컴파일된 C# 함수를 로컬로 개발하는 방법에 대한 자세한 내용은 [Visual Studio를 사용하여 Azure Functions 개발](functions-develop-vs.md)을 참조하세요.
+ Mac, Linux 또는 Windows 컴퓨터에서 VS Code를 사용하여 함수를 로컬로 개발하는 방법에 대한 자세한 내용은 [Azure Functions에 대한 VS Code 설명서](https://code.visualstudio.com/tutorials/functions-extension/getting-started)를 참조하세요.
+ 명령 프롬프트 또는 터미널에서 함수를 개발하는 방법에 대한 자세한 내용은 [Azure Functions 핵심 도구 작업](functions-run-local.md)을 참조하세요.

<!-- LINKS -->

[Azure Functions 핵심 도구]: https://www.npmjs.com/package/azure-functions-core-tools
[Azure Portal]: https://portal.azure.com 
[Node.js]: https://docs.npmjs.com/getting-started/installing-node#osx-or-windows
