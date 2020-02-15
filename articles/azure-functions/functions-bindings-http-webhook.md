---
title: Azure Functions HTTP 트리거 및 바인딩
description: Azure Functions에서 HTTP 트리거 및 바인딩을 사용 하는 방법을 알아봅니다.
author: craigshoemaker
ms.topic: reference
ms.date: 02/14/2020
ms.author: cshoe
ms.openlocfilehash: ebf0728184a5fc104e3e1e7d8a199fec328dbdc0
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/14/2020
ms.locfileid: "77210177"
---
# <a name="azure-functions-http-triggers-and-bindings-overview"></a>Azure Functions HTTP 트리거 및 바인딩 개요

서버 리스 Api를 빌드하고 [웹 후크에](https://en.wikipedia.org/wiki/Webhook)응답 하기 위해 HTTP 요청을 통해 Azure Functions를 호출할 수 있습니다.

| 작업 | Type |
|---------|---------|
| HTTP 요청에서 함수 실행 | [트리거](./functions-bindings-http-webhook-trigger.md) |
| 함수에서 HTTP 응답 반환 |[출력 바인딩](./functions-bindings-http-webhook-output.md) |

이 문서의 코드는 기본적으로 기능 버전 2.x 이상에서 사용 되는 .NET Core 구문을 사용 합니다. 1\.x 구문에 대한 자세한 내용은 [1.x 기능 템플릿](https://github.com/Azure/azure-functions-templates/tree/v1.x/Functions.Templates/Templates)을 참조하세요.

## <a name="add-to-your-functions-app"></a>함수 앱에 추가

### <a name="functions-2x-and-higher"></a>함수 2.x 이상

트리거와 바인딩을 사용 하려면 적절 한 패키지를 참조 해야 합니다. NuGet 패키지는 .NET 클래스 라이브러리에 사용 되며 확장은 다른 모든 응용 프로그램 형식을 묶습니다.

| 언어                                        | 추가 방법 ...                                   | 설명 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | [NuGet 패키지]설치, 버전 3(sp3) | |
| C#스크립트, Java, JavaScript, Python, PowerShell | [확장 번들] 등록          | [Azure Tools 확장](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack) 은 Visual Studio Code와 함께 사용 하는 것이 좋습니다. |
| C#스크립트 (온라인 전용 Azure Portal)         | 바인딩 추가                            | 함수 앱을 다시 게시 하지 않고 기존 바인딩 확장을 업데이트 하려면 [확장 업데이트]를 참조 하세요. |

[core tools]: ./functions-run-local.md
[확장 번들]: ./functions-bindings-register.md#extension-bundles
[NuGet 패키지]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Http
[확장 업데이트]: ./install-update-binding-extensions-manual.md
[Azure Tools extension]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Functions 1.x

함수 1.x 앱은 [WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet 패키지 버전 2.x를 자동으로 참조 합니다.

## <a name="next-steps"></a>다음 단계

- [HTTP 요청에서 함수 실행](./functions-bindings-http-webhook-trigger.md)
- [함수에서 HTTP 응답 반환](./functions-bindings-http-webhook-output.md)
