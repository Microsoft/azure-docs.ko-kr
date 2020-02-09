---
author: ggailey777
ms.author: glenga
ms.date: 7/24/2019
ms.topic: include
ms.service: azure-functions
ms.openlocfilehash: 1928a8238cd73087e3c199675574dd1395f4d76d
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/09/2019
ms.locfileid: "68881362"
---
#### <a name="built-in-log-streaming"></a>기본 제공 로그 스트리밍

다음 예제 `logstream` 와 같이 Azure에서 실행 중인 특정 함수 앱의 스트리밍 로그 수신을 시작 하려면 옵션을 사용 합니다.

```bash
func azure functionapp logstream <FunctionAppName>
```

#### <a name="live-metrics-stream"></a>라이브 메트릭 스트림

다음 예제와 같이 `--browser` 옵션을 포함 하 여 새 브라우저 창에서 함수 앱에 대한 [라이브 메트릭 스트림](../articles/azure-monitor/app/live-stream.md) 를 볼 수도 있습니다.

```bash
func azure functionapp logstream <FunctionAppName> --browser
```
