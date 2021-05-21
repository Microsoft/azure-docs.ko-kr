---
title: Functions 2.xd 이상의 Azure Cosmos DB 바인딩
description: Azure Functions에서 Azure Cosmos DB 트리거 및 바인딩을 사용하는 방법을 파악합니다.
author: craigshoemaker
ms.topic: reference
ms.date: 02/24/2017
ms.author: cshoe
ms.openlocfilehash: dde3b95c7997a7f742a9d48a964f4275169f6e9c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92106929"
---
# <a name="azure-cosmos-db-trigger-and-bindings-for-azure-functions-2x-and-higher-overview"></a>Azure Functions 2.x 이상의 Azure Cosmos DB 트리거 및 바인딩 개요

> [!div class="op_single_selector" title1="사용할 Azure Functions 런타임 버전을 선택합니다. "]
> * [버전 1](functions-bindings-cosmosdb.md)
> * [버전 2 이상](functions-bindings-cosmosdb-v2.md)

이 문서 세트에서는 Azure Functions 2.x 이상에서 [Azure Cosmos DB](../cosmos-db/serverless-computing-database.md) 바인딩을 사용하는 방법을 설명합니다. Azure Functions는 Azure Cosmos DB에 대한 트리거, 입력 및 출력 바인딩을 지원합니다.

| 작업 | 형식 |
|---------|---------|
| Azure Cosmos DB 문서를 만들거나 수정할 때 함수 실행 | [트리거](./functions-bindings-cosmosdb-v2-trigger.md) |
| Azure Cosmos DB 문서 읽기 | [입력 바인딩](./functions-bindings-cosmosdb-v2-input.md) |
| Azure Cosmos DB 문서에 변경 내용 저장  |[출력 바인딩](./functions-bindings-cosmosdb-v2-output.md) |

> [!NOTE]
> 이 참조는 [Azure Functions 버전 2.x 이상](functions-versions.md)에 대한 것입니다.  Functions 1.x에서 이러한 바인딩을 사용하는 방법에 대한 내용은 [Azure Functions 1.x에 대한 Azure Cosmos DB 바인딩](functions-bindings-cosmosdb.md)을 참조하세요.
>
> 이 바인딩의 원래 이름은 DocumentDB입니다. Functions 버전 2.x 이상에서는 트리거, 바인딩 및 패키지의 이름이 모두 Cosmos DB로 지정됩니다.

## <a name="supported-apis"></a>지원되는 API

[!INCLUDE [SQL API support only](../../includes/functions-cosmosdb-sqlapi-note.md)]

## <a name="add-to-your-functions-app"></a>Functions 앱에 추가

### <a name="functions-2x-and-higher"></a>Functions 2.x 이상

트리거 및 바인딩을 사용하려면 적절한 패키지를 참조해야 합니다. NuGet 패키지는 .NET 클래스 라이브러리에 사용되는 반면 확장 번들은 다른 모든 애플리케이션 형식에 사용됩니다.

| 언어                                        | 추가 방법...                                   | 설명 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | [NuGet 패키지] 버전 3.x 설치 | |
| C# Script, Java, JavaScript, Python, PowerShell | [확장 번들] 등록          | [Azure Tools 확장]은 Visual Studio Code와 함께 사용하는 것이 좋습니다. |
| C# 스크립트(Azure Portal에서 온라인으로만 사용)         | 바인딩 추가                            | 함수 앱을 다시 게시하지 않고 기존 바인딩 확장을 업데이트하려면 [확장 업데이트]를 참조하세요. |

[NuGet 패키지]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB
[core tools]: ./functions-run-local.md
[확장 번들]: ./functions-bindings-register.md#extension-bundles
[확장 업데이트]: ./functions-bindings-register.md
[Azure Tools 확장]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Functions 1.x

Functions 1.x 앱은 [Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet 패키지 버전 2.x에 대한 참조를 자동으로 포함합니다.

## <a name="next-steps"></a>다음 단계

- [Azure Cosmos DB 문서를 만들거나 수정할 때 함수 실행(트리거)](./functions-bindings-cosmosdb-v2-trigger.md)
- [Azure Cosmos DB 문서 읽기(입력 바인딩)](./functions-bindings-cosmosdb-v2-input.md)
- [Azure Cosmos DB 문서에 변경 내용 저장(출력 바인딩)](./functions-bindings-cosmosdb-v2-output.md)