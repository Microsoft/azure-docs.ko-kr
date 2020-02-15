---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 8573a915c572885b53437843b114aa987be4c66b
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77198458"
---
Azure Functions 발생 하는 오류는 다음 원본 중 하나에서 가져올 수 있습니다.

- 기본 제공 Azure Functions [트리거 및 바인딩](..\articles\azure-functions\functions-triggers-bindings.md) 사용
- 기본 Azure 서비스의 Api 호출
- REST 끝점에 대 한 호출
- 클라이언트 라이브러리, 패키지 또는 타사 Api에 대 한 호출

데이터 또는 누락 된 메시지의 손실을 방지 하기 위해 다음과 같은 견고한 오류 처리 방법을 사용 하는 것이 중요 합니다. 권장 되는 오류 처리 방법에는 다음 작업이 포함 됩니다.

- [Application Insights 사용](../articles/azure-functions/functions-monitoring.md)
- [구조적 오류 처리 사용](#use-structured-error-handling)
- [멱 등 성 디자인](../articles/azure-functions/functions-idempotent.md)
- [재시도 정책 구현](../articles/azure-functions/functions-reliable-event-processing.md) (해당 하는 경우)

### <a name="use-structured-error-handling"></a>구조적 오류 처리 사용

캡처 및 게시 오류는 응용 프로그램의 상태를 모니터링 하는 데 매우 중요 합니다. 함수 코드의 최상위 수준에는 try/catch 블록이 포함 되어야 합니다. Catch 블록에서 오류를 캡처 및 게시할 수 있습니다.

### <a name="retry-support"></a>다시 시도 지원

다음 트리거에는 기본 제공 재시도 지원이 있습니다.

* [Azure Blob Storage](../articles/azure-functions/functions-bindings-storage-blob.md)
* [Azure Queue storage](../articles/azure-functions/functions-bindings-storage-queue.md)
* [Azure Service Bus(큐/토픽)](../articles/azure-functions/functions-bindings-service-bus.md)

기본적으로이 작업은 다시 시도 요청을 최대 5 회까지 트리거합니다. 5 번째 재시도 후에는 Azure Queue storage와 Azure Service Bus 트리거에서 모두 [포이즌 큐](..\articles\azure-functions\functions-bindings-storage-queue.md#trigger---poison-messages)에 메시지를 씁니다.

다른 트리거 또는 바인딩 유형에 대 한 재시도 정책을 수동으로 구현 해야 합니다. 수동 구현에는 [포이즌 메시지 큐](..\articles\azure-functions\functions-bindings-storage-blob-trigger.md#poison-blobs)에 오류 정보를 기록 하는 작업이 포함 될 수 있습니다. 포이즌 큐에 기록 하면 나중에 작업을 다시 시도할 수 있습니다. 이 방법은 Blob storage 트리거에서 사용 하는 것과 동일 합니다.
