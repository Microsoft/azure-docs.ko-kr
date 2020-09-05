---
title: Durable Functions 버전 개요-Azure Functions
description: Durable Functions 버전에 대해 알아봅니다.
author: cgillum
ms.topic: conceptual
ms.date: 08/20/2020
ms.author: azfuncdf
ms.openlocfilehash: d6662259494bba5747e01c4574186e9030112247
ms.sourcegitcommit: 6fc156ceedd0fbbb2eec1e9f5e3c6d0915f65b8e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/21/2020
ms.locfileid: "88719413"
---
# <a name="durable-functions-versions-overview"></a>Durable Functions 버전 개요

*지속성 함수*는 서버를 사용하지 않는 환경에서 상태 저장 함수를 작성할 수 있게 하는 [Azure Functions](../functions-overview.md) 및 [Azure WebJobs](../../app-service/webjobs-create.md)의 확장입니다. 확장은 상태, 검사점 및 다시 시작을 관리합니다. Durable Functions에 익숙하지 않은 경우 [개요 설명서](durable-functions-overview.md)를 참조 하세요.

## <a name="new-features-in-2x"></a>2.x의 새로운 기능

이 섹션에서는 버전 2.x에 추가 된 Durable Functions 기능에 대해 설명 합니다.

### <a name="durable-entities"></a>지 속성 엔터티

Durable Functions 2.x에서는 새로운 [엔터티 함수](durable-functions-entities.md) 개념을 도입 했습니다.

엔터티 함수는 *지속성 엔터티*라고 하는 작은 상태 부분을 읽고 업데이트하는 작업을 정의합니다. 오케스트레이터 함수와 마찬가지로 엔터티 함수는 특수 트리거 유형인 *엔터티 트리거*를 사용하는 함수입니다. Orchestrator 함수와 달리 entity 함수는 특정 코드 제약 조건을 포함 하지 않습니다. 또한 엔터티 함수는 제어 흐름을 통해 상태를 암시적으로 나타내지 않고 명시적으로 관리합니다.

자세한 내용은 지 [속성 엔터티](durable-functions-entities.md) 문서를 참조 하세요.

### <a name="durable-http"></a>지 속성 HTTP

Durable Functions 2.x에는 다음을 수행할 수 있는 새로운 [내구성이 있는 HTTP](durable-functions-http-features.md#consuming-http-apis) 기능이 도입 되었습니다.

* 오케스트레이션 함수에서 직접 HTTP Api를 호출 합니다 (몇 가지 문서화 된 제한 사항 포함).
* 자동 클라이언트 쪽 HTTP 202 상태 폴링을 구현 합니다.
* [Azure 관리 되는 id](../../active-directory/managed-identities-azure-resources/overview.md)를 기본적으로 지원 합니다.

자세히 알아보려면 [HTTP 기능](durable-functions-http-features.md#consuming-http-apis) 문서를 참조 하세요.

## <a name="migrate-from-1x-to-2x"></a>1.x에서 2.x로 마이그레이션

이 섹션에서는 기존 버전 1.x Durable Functions를 버전 2.x로 마이그레이션하여 새로운 기능을 활용 하는 방법을 설명 합니다.

### <a name="upgrade-the-extension"></a>확장 업그레이드

프로젝트에 Durable Functions 바인딩 확장의 최신 2.x 버전을 설치 합니다.

#### <a name="javascript-python-and-powershell"></a>JavaScript, Python 및 PowerShell

Durable Functions 2.x는 [Azure Functions 확장 번들](../functions-bindings-register.md#extension-bundles)의 버전 2.x에서 사용할 수 있습니다.

프로젝트에서 확장 번들 버전을 업데이트 하려면 host.js을 열고 `extensionBundle` 버전 2.x ()를 사용 하도록 섹션을 업데이트 `[2.*, 3.0.0)` 합니다.

```json
{
    "version": "2.0",
    "extensionBundle": {
        "id": "Microsoft.Azure.Functions.ExtensionBundle",
        "version": "[2.*, 3.0.0)"
    }
}
```

#### <a name="net"></a>.NET

최신 버전의 [Durable Functions 바인딩 확장](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask)을 사용 하도록 .net 프로젝트를 업데이트 합니다.

자세한 내용은 [Azure Functions 바인딩 확장 등록](../functions-bindings-register.md#local-csharp) 을 참조 하세요.

### <a name="update-your-code"></a>코드 업데이트

Durable Functions 2.x에는 몇 가지 주요 변경 사항이 도입 되었습니다. Durable Functions 1.x 응용 프로그램은 코드를 변경 하지 않고 Durable Functions 2.x와 호환 되지 않습니다. 이 섹션에서는 버전 1.x 함수를 2.x로 업그레이드할 때 수행 해야 하는 몇 가지 변경 사항을 설명 합니다.

#### <a name="hostjson-schema"></a>스키마 Host.js

Durable Functions 2.x는 스키마에 새 host.js를 사용 합니다. 1.x의 주요 변경 내용에는 다음이 포함 됩니다.

* `"storageProvider"``"azureStorage"`저장소 관련 구성에 대 한 및 하위 섹션입니다.
* `"tracing"` 추적 및 로깅 구성의 경우
* `"notifications"``"eventGrid"`event grid 알림 구성의 및 하위 섹션입니다.

자세한 내용은 [Durable Functions host.js참조 설명서](durable-functions-bindings.md#durable-functions-2-0-host-json) 를 참조 하세요.

#### <a name="default-taskhub-name-changes"></a>기본 taskhub 이름 변경

버전 1.x에서 작업 허브 이름이 host.js에 지정 되지 않은 경우 기본적으로 "DurableFunctionsHub"로 설정 되었습니다. 버전 2.x에서는 이제 기본 작업 허브 이름이 함수 앱의 이름에서 파생 됩니다. 따라서 2.x로 업그레이드할 때 작업 허브 이름을 지정 하지 않은 경우 코드는 새 작업 허브를 사용 하 여 작동 하 고 모든 진행 중인 오케스트레이션에 서 더 이상 응용 프로그램을 처리 하지 않습니다. 이 문제를 해결 하려면 작업 허브 이름을 v1. x 기본값을 "DurableFunctionsHub"로 명시적으로 설정 하거나 [가동 중지 시간 (제로) 배포 지침](durable-functions-zero-downtime-deployment.md) 에 따라 진행 중인 오케스트레이션의 주요 변경 사항을 처리 하는 방법에 대 한 자세한 내용을 확인할 수 있습니다.

#### <a name="public-interface-changes-net-only"></a>공용 인터페이스 변경 (.NET만 해당)

버전 1.x에서 Durable Functions에서 지 원하는 다양 한 _컨텍스트_ 개체는 단위 테스트에 사용 하기 위한 추상 기본 클래스를 포함 합니다. Durable Functions 2.x의 일부로 이러한 추상 기본 클래스는 인터페이스로 대체 됩니다.

다음 표는 주요 변경 내용을 나타냅니다.

| 1.x | 2.x |
|----------|----------|
| `DurableOrchestrationClientBase` | `IDurableOrchestrationClient` 또는 `IDurableClient` |
| `DurableOrchestrationContext` 또는 `DurableOrchestrationContextBase` | `IDurableOrchestrationContext` |
| `DurableActivityContext` 또는 `DurableActivityContextBase` | `IDurableActivityContext` |
| `OrchestrationClientAttribute` | `DurableClientAttribute` |

추상 기본 클래스가 가상 메서드를 포함 하는 경우 이러한 가상 메서드는에 정의 된 확장 메서드로 대체 `DurableContextExtensions` 됩니다.

#### <a name="functionjson-changes-javascript-and-c-script"></a>변경 시 function.js(JavaScript 및 c # 스크립트)

1.x Durable Functions에서 오케스트레이션 클라이언트 바인딩은의를 사용 합니다 `type` `orchestrationClient` . 버전 2.x는 `durableClient` 대신을 사용 합니다.

#### <a name="raise-event-changes"></a>이벤트 변경 발생

1.x Durable Functions에서 [이벤트 발생](durable-functions-external-events.md#send-events) API를 호출 하 고 존재 하지 않는 인스턴스를 지정 하면 자동 오류가 발생 합니다. 2. x부터 존재 하지 않는 오케스트레이션에 이벤트를 발생 시키면 예외가 발생 합니다.
