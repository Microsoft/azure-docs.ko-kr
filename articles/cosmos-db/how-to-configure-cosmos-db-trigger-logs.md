---
title: Cosmos DB에 대한 Azure Functions 트리거를 사용 하 여 로그 구성 및 읽기
description: Cosmos DB용 Azure Functions 트리거를 사용할 때 Azure Functions 로깅 파이프라인에 로그를 노출하는 방법 알아보기
author: ealsur
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/17/2019
ms.author: maquaran
ms.openlocfilehash: 5ff747b225f8984bcaafd80015e85a9f014bdb50
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75441840"
---
# <a name="how-to-configure-and-read-the-logs-when-using-azure-functions-trigger-for-cosmos-db"></a>Cosmos DB용 Azure Functions 트리거를 사용할 때 로그를 구성하고 읽는 방법

이 문서에서는 Cosmos DB용 Azure Functions 트리거 로그를 구성된 [모니터링 솔루션](../azure-functions/functions-monitoring.md)에 전송하도록 Azure Functions 환경을 구성하는 방법에 대해 설명합니다.

## <a name="included-logs"></a>포함된 로그

Cosmos DB용 Azure Functions 트리거는 내부적으로 [변경 피드 프로세서 라이브러리](./change-feed-processor.md)를 사용하며, 라이브러리는 [문제 해결을 위해](./troubleshoot-changefeed-functions.md) 내부 작업을 모니터링하는 데 사용할 수 있는 상태 로그 세트를 생성합니다.

상태 로그는 부하 분산 시나리오 또는 초기화 중에 작업을 시도할 때 Cosmos DB용 Azure Functions 트리거의 작동 방식을 설명합니다.

## <a name="enabling-logging"></a>로깅을 사용하도록 설정

Cosmos DB용 Azure Functions 트리거를 사용할 때 로깅을 설정하려면 Azure Functions 프로젝트 또는 Azure Functions App에서 `host.json` 파일을 찾아서 [필요한 로깅 수준을 구성](../azure-functions/functions-monitoring.md#log-configuration-in-hostjson)합니다. 다음 샘플에 나온 것처럼 `Host.Triggers.CosmosDB`에 대한 추적을 사용하도록 설정해야 합니다.

```js
{
  "version": "2.0",
  "logging": {
    "fileLoggingMode": "always",
    "logLevel": {
      "Host.Triggers.CosmosDB": "Trace"
    }
  }
}
```

업데이트된 구성을 사용하여 Azure Function이 배포되면 추적의 일부로 Cosmos DB용 Azure Functions 트리거 로그가 나타납니다. 구성 된 로깅 공급자의 *범주* `Host.Triggers.CosmosDB`아래에서 로그를 볼 수 있습니다.

## <a name="query-the-logs"></a>로그 쿼리

다음 쿼리를 실행하여 [Azure Application Insights의 Analytics](../azure-monitor/app/analytics.md)에 있는 Cosmos DB용 Azure Functions 트리거에서 생성된 로그를 쿼리합니다.

```sql
traces
| where customDimensions.Category == "Host.Triggers.CosmosDB"
```

## <a name="next-steps"></a>다음 단계

* Azure Functions 애플리케이션에서 [모니터링을 사용하도록 설정](../azure-functions/functions-monitoring.md)합니다.
* Cosmos DB용 Azure Functions 트리거를 사용할 때 [일반적인 문제 진단 및 해결](./troubleshoot-changefeed-functions.md) 방법을 알아봅니다.