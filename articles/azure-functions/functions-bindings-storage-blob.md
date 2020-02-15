---
title: Azure Functions에 대 한 Azure Blob storage 트리거 및 바인딩
description: Azure Functions에서 Azure Blob storage 트리거 및 바인딩을 사용 하는 방법을 알아봅니다.
author: craigshoemaker
ms.topic: reference
ms.date: 02/13/2020
ms.author: cshoe
ms.openlocfilehash: 794e18590f554d1537449d2aee9451c0053ac4a7
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77198425"
---
# <a name="azure-blob-storage-bindings-for-azure-functions-overview"></a>Azure Functions 개요에 대 한 Azure Blob storage 바인딩

Azure Functions는 [트리거와 바인딩을](./functions-triggers-bindings.md)통해 [Azure Storage](https://docs.microsoft.com/azure/storage/) 와 통합 됩니다. Blob storage와 통합 하면 blob 데이터의 변경 내용 및 읽기/쓰기 값에 반응 하는 함수를 작성할 수 있습니다.

| 작업 | Type |
|---------|---------|
| Blob storage 데이터 변경으로 함수 실행 | [트리거](./functions-bindings-storage-blob-trigger.md) |
| 함수에서 blob 저장소 데이터 읽기 | [입력 바인딩](./functions-bindings-storage-blob-input.md) |
| 함수가 blob 저장소 데이터를 쓰도록 허용 |[출력 바인딩](./functions-bindings-storage-blob-output.md) |

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
[NuGet 패키지]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage
[확장 업데이트]: ./install-update-binding-extensions-manual.md
[Azure Tools extension]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Functions 1.x

함수 1.x 앱은 [WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet 패키지 버전 2.x를 자동으로 참조 합니다.

[!INCLUDE [functions-storage-sdk-version](../../includes/functions-storage-sdk-version.md)]

## <a name="next-steps"></a>다음 단계

- [Blob storage 데이터가 변경 될 때 함수 실행](./functions-bindings-storage-blob-trigger.md)
- [함수가 실행 될 때 blob 저장소 데이터 읽기](./functions-bindings-storage-blob-input.md)
- [함수에서 blob 저장소 데이터 쓰기](./functions-bindings-storage-blob-output.md)
